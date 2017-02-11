---
title: "使用 Media Encoder Standard 進行進階編碼"
description: "本主題說明如何透過自訂 Media Encoder Standard 工作預設值執行進階編碼。 本主題說明如何使用媒體服務 .NET SDK 建立編碼工作與作業。 本主題也會說明如何提供自訂預設值給編碼作業。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 602f86f17baffe706f27963e8d9963f082971f54
ms.openlocfilehash: 0d60d491c459c96cb0f507e52159d0b60e38ac33


---
# <a name="advanced-encoding-with-media-encoder-standard"></a>使用 Media Encoder Standard 進行進階編碼
## <a name="overview"></a>Overview
本主題說明如何以 Media Encoder Standard 執行進階編碼工作。 本主題說明 [如何使用 .NET 建立編碼工作與執行此工作的作業](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)。 本主題也會說明如何提供自訂預設值給編碼工作。 如需預設值所用項目的說明，請參閱 [這份文件](https://msdn.microsoft.com/library/mt269962.aspx)。

以下說明執行下列編碼工作的自訂預設值：

* [產生縮圖](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
* [修剪視訊 (裁剪)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
* [建立疊加層](media-services-custom-mes-presets-with-dotnet.md#overlay)
* [在輸入不含音訊時插入靜音曲目](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
* [停用自動去交錯](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
* [純音訊預設值](media-services-custom-mes-presets-with-dotnet.md#audio_only)
* [串連兩個以上的視訊檔案](#concatenate)
* [以 Media Encoder Standard 裁剪影片](#crop)
* [在輸入不含視訊時插入視訊播放軌](#no_video)
* [旋轉視訊](#rotate_video)

## <a name="a-idencodingwithdotnetaencoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>使用媒體服務 .NET SDK 進行編碼
下列程式碼範例使用媒體服務 .NET SDK 執行下列工作：

* 建立編碼工作。
* 取得對 Media Encoder Standard 編碼器的參考
* 載入自訂 XML 或 JSON 預設值。 您可以在檔案中儲存 XML 或 JSON (例如 [XML](media-services-custom-mes-presets-with-dotnet.md#xml) 或 [JSON](media-services-custom-mes-presets-with-dotnet.md#json))，並使用下列程式碼載入檔案。

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* 將編碼工作新增至作業。
* 指定要編碼的輸入資產。
* 建立包含已編碼資產的輸出資產。
* 加入事件處理常式來檢查工作進度。
* 提交作業。

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;

        namespace CustomizeMESPresests
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];

                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;

                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");

                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);

                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();

                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);

                    Console.ReadLine();
                }

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job.
                    // This output is specified as AssetCreationOptions.None, which
                    // means the output asset is not encrypted.
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }

                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;

                        af.Update();
                    }

                    return asset;
                }


                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);

                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);

                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }


                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:

                            // Cast sender as a job.
                            IJob job = (IJob)sender;

                            // Display or log error details as needed.
                            break;
                        default:
                            break;
                    }
                }


                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }

            }
        }


## <a name="support-for-relative-sizes"></a>支援相對大小
產生它的縮圖時，您不需要永遠以像素單位指定輸出的寬度和高度。 您可以以百分比指定它們，範圍為 [1%、…、100%]。

### <a name="json-preset"></a>JSON 預設值
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML 預設值
    <Width>100%</Width>
    <Height>100%</Height>

## <a name="a-idthumbnailsagenerate-thumbnails"></a><a id="thumbnails"></a>產生縮圖
本節說明如何自訂產生縮圖的預設值。 下面定義的預設值包含有關如何將檔案編碼的資訊，以及產生縮圖時所需的資訊。 您可以使用 [這裡](https://msdn.microsoft.com/library/mt269960.aspx) 記載的任何 MES 預設值，並加入可產生縮圖的程式碼。  

> [!NOTE]
> 如果編碼為單一位元速率視訊，下列預設值中的 **SceneChangeDetection** 設定只能設定為 true。 如果您是針對多位元速率視訊進行編碼，並將 **SceneChangeDetection** 設為 true，編碼器會傳回錯誤。  
>
>

如需結構描述的資訊，請參閱 [這個](https://msdn.microsoft.com/library/mt269962.aspx) 主題。

請務必閱讀 [考量](media-services-custom-mes-presets-with-dotnet.md#considerations) 一節。

### <a name="a-idjsonajson-preset"></a><a id="json"></a>JSON 預設值
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"

            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="a-idxmlaxml-preset"></a><a id="xml"></a>XML 預設值
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>考量
您必須考量下列事項：

* 為 Start/Step/Range 使用明確的時間戳記會假設輸入來源至少為 1 分鐘的長度。
* 具有 Start、Step 和 Range 字串屬性的 Jpg/Png/BmpImage 項目 - 這些可以解譯為：

  * 畫面格數目 (如果是非負整數)，例如 "Start": "120"、
  * 相對於持續時間 (如果以 % 尾碼表示)，例如 "Start": "15%"，或
  * 時間戳記 (如果以 HH:MM:SS... 格式表示)，例如 "Start" : "00:01:00"

    您可以隨意混合使用標記法。

    此外， Start 也支援特殊的巨集 (即 {Best})，它會嘗試判斷第一個「 有趣 」的內容畫面。附註：(Start 設為 {Best} 時，會忽略 Step 與 Range)
  * 預設值：Start:{Best}
* 必須明確地提供每個影像格式的輸出格式：Jpg/Png/BmpFormat。 顯示時，MES 會比對 JpgVideo 與 JpgFormat，依此類推。 OutputFormat 引進了新的影像轉碼器特定巨集 (即 {Index})，必須針對影像輸出格式提供一次 (只需一次)。

## <a name="a-idtrimvideoatrim-a-video-clipping"></a><a id="trim_video"></a>修剪視訊 (裁剪)
本節說明修改編碼器預設值，以裁剪或修剪其輸入為所謂的夾層檔或隨選檔的輸入視訊。 編碼器也可以用來裁剪或修剪從即時串流擷取或封存的資產 - [此部落格](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)提供詳細資料。

若要剪輯您的視訊，可以使用 [這裡](https://msdn.microsoft.com/library/mt269960.aspx) 記載的任何 MES 預設值，並修改 **Sources** 元素 (如下所示)。 StartTime 值必須符合輸入視訊的絕對時間戳記。 例如，如果輸入視訊的第一個畫面有 12:00:10.000 的時間戳記，則 StartTime 至少應該為 12:00:10.000 以上。 在下列範例中，我們假設輸入視訊的開始時間戳記為零。 **Sources** 應該位於預設值開頭。

### <a name="a-idjsonajson-preset"></a><a id="json"></a>JSON 預設值
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML 預設值
若要修剪您的影片，您可以使用 [這裡](https://msdn.microsoft.com/library/mt269960.aspx) 記載的任何 MES 預設值，並修改 **Sources** 元素 (如下所示)。

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a name="a-idoverlayacreate-an-overlay"></a><a id="overlay"></a>建立疊加層
Media Encoder Standard 可讓您在現有影片上疊加影像。 目前支援下列格式：png、jpg、gif 及 bmp。 下面定義的預設值為視訊疊加層的基本範例。

除了定義預設檔案之外，您還必須讓媒體服務知道資產中哪個檔案是疊加影像，以及哪個檔案是您要在上面疊加影像的來源影片。 視訊檔案必須是「主要」  檔案。

上述 .NET 範例定義兩個函式：**UploadMediaFilesFromFolder** 和 **EncodeWithOverlay**。 UploadMediaFilesFromFolder 函式會上傳資料夾中的檔案 (例如，BigBuckBunny.mp4 和 Image001.png)，並將 mp4 檔案設定為資產中的主要檔案。 **EncodeWithOverlay** 函式會使用傳遞給它的自訂預設值檔案 (例如後續的預設值) 建立編碼工作。

> [!NOTE]
> 目前限制：
>
> 不支援疊加不透明度設定。
>
> 來源視訊檔案和疊加影像檔案必須位在相同的資產中，而且視訊檔案需要設定為此資產中的主要檔案。
>
>

### <a name="json-preset"></a>JSON 預設值
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>XML 預設值
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a name="a-idsilentaudioainsert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>在輸入不含音訊時插入靜音曲目
依照預設，如果您傳送僅包含視訊不含音訊的輸入到編碼器，輸出資產將包含僅含視訊資料的檔案。 某些播放器可能無法處理此類型輸出資料流。 您可以在該案例中使用此設定來強制編碼器將靜音曲目新增至輸出。

若要強制編碼器在輸入不含音訊時產生包含靜音曲目的資產，請指定 "InsertSilenceIfNoAudio" 值。

您可以使用 [這裡](https://msdn.microsoft.com/library/mt269960.aspx)記載的任何 MES 預設值，並執行以下修改：

### <a name="json-preset"></a>JSON 預設值
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML 預設值
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a name="a-iddeinterlacingadisable-auto-de-interlacing"></a><a id="deinterlacing"></a>停用自動去交錯
如果客戶想要將交錯內容自動去交錯，就不需要採取任何動作。 當自動去交錯開啟 (預設) 時，MES 會自動偵測交錯式畫面，並且只會將標示為交錯式的畫面去交錯。

您可以關閉自動去交錯。 但不建議您這樣做。

### <a name="json-preset"></a>JSON 預設值
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML 預設值
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a name="a-idaudioonlyaaudio-only-presets"></a><a id="audio_only"></a>純音訊預設值
本節示範兩個純音訊的 MES 預設值︰AAC 音訊和 AAC 好品質音訊。

### <a name="aac-audio"></a>AAC 音訊
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>AAC 好品質音訊
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a name="a-idconcatenateaconcatenate-two-or-more-video-files"></a><a id="concatenate"></a>串連兩個以上的視訊檔案
下列範例示範如何產生預設值來串連兩個以上的視訊檔案。 最常見的案例是您想要在主要視訊中加入標頭或預告片時。 預定的使用時機為同時編輯的視訊檔案具有共用屬性 (視訊解析度、畫面播放速率、曲目數等) 時。 您應該注意不要混合使用不同畫面播放速率或不同曲目數的視訊。

### <a name="requirements-and-considerations"></a>需求和考量
* 輸入視訊應該只有一個曲目。
* 輸入視訊的畫面播放速率應該都相同。
* 您必須將視訊上傳至不同的資產，並將視訊設定為每個資產中的主要檔案。
* 您需要知道您視訊的持續時間。
* 下列預設值範例假設所有輸入視訊的開頭為零時間戳記。 如果視訊具有不同的開始時間戳記 (即時封存時常發生這種情況)，則需要修改 StartTime 值。
* JSON 預設值會建立輸入資產之 AssetID 值的明確參考。
* 範例程式碼假設 JSON 預設值已儲存至本機檔案 (例如 "C:\supportFiles\preset.json")。 其也會假設已透過上傳兩個視訊檔案來建立兩個資產，並假設您知悉產生的 AssetID 值。
* 程式碼片段和 JSON 預設值顯示串連兩個視訊檔案的範例。 您可以將其擴充至兩個以上的視訊，方法是︰

  1. 重複呼叫 task.InputAssets.Add() 依序加入更多視訊。
  2. 加入更多項目，以依相同順序對 JSON 中的 "Sources" 元素進行對應編輯。

### <a name="net-code"></a>.NET 程式碼
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>JSON 預設值
使用您想要串連的資產識別碼以及每個視訊的適當時間區段，進而更新您的自訂預設值。

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a name="a-idcropacrop-videos-with-media-encoder-standard"></a><a id="crop"></a>以 Media Encoder Standard 裁剪影片
請參閱 [以 Media Encoder Standard 裁剪影片](media-services-crop-video.md) 主題。

## <a name="a-idnovideoainsert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>在輸入不含視訊時插入視訊播放軌
依照預設，如果您傳送僅包含音訊訊不含視訊的輸入到編碼器，輸出資產將包含僅含音訊資料的檔案。 某些播放器，包括 Azure 媒體播放器 (請參閱 [這篇文章](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios))，可能無法處理這類串流。 您可以在該案例中使用此設定來強制編碼器將單色視訊播放軌新增至輸出。

> [!NOTE]
> 強制編碼器插入輸出視訊播放軌，將會增加輸出資產的大小，並提升編碼工作所產生的成本。 您應該執行測試，以確認所導致的成本提升對您的每月費用沒有太大的影響。
>
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>於最低位元速率插入視訊
假設您正在使用多重位元速率編碼預設值 (例如 [「H264 Multiple 多重位元速率 720p」](https://msdn.microsoft.com/library/mt269960.aspx) ) 來將整個輸入目錄針對串流進行編碼，這將會包含各種視訊檔案和純音訊檔案。 在這個案例中，當輸入沒有視訊時，您可能需要強制編碼器於最低位元速率插入單色視訊播放軌，而非於所有輸出位元速率插入視訊。 若要達成此目的，您必須指定 "InsertBlackIfNoVideoBottomLayerOnly" 旗標。

您可以使用 [這裡](https://msdn.microsoft.com/library/mt269960.aspx)記載的任何 MES 預設值，並執行以下修改：

#### <a name="json-preset"></a>JSON 預設值
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML 預設值
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>以所有輸出位元速率插入視訊
假設您正在使用多重位元速率編碼預設值 (例如 [「H264 Multiple 多重位元速率 720p」](https://msdn.microsoft.com/library/mt269960.aspx) ) 來將整個輸入目錄針對串流進行編碼，這將會包含各種視訊檔案和純音訊檔案。 在這個案例中，當輸入沒有視訊時，您可能需要強制編碼器於所有輸出位元速率插入單色視訊播放軌。 這能確保您所有的輸出資產與視訊播放軌和音訊播放軌之間的同質性。 若要達成此目的，您必須指定 "InsertBlackIfNoVideo" 旗標。

您可以使用 [這裡](https://msdn.microsoft.com/library/mt269960.aspx)記載的任何 MES 預設值，並執行以下修改：

#### <a name="json-preset"></a>JSON 預設值
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML 預設值
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

## <a name="a-idrotatevideoarotate-a-video"></a><a id="rotate_video"></a>旋轉視訊
[媒體編碼器標準](media-services-dotnet-encode-with-media-encoder-standard.md)支援 0/90/180/270 度的旋轉角度。 預設行為是「自動」，此時它會嘗試偵測內送之視訊檔案的旋轉中繼資料並加以補償。 包括以下 **Sources** 元素至 [這裡](http://msdn.microsoft.com/library/azure/mt269960.aspx)所定義的其中一個預設項目：

### <a name="json-preset"></a>JSON 預設值
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>XML 預設值
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

此外，如需在觸發旋轉補償時，編碼器會如何解譯預設中寬度和高度設定的詳細資訊，請參閱[此主題](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation)。

您可以使用 "0" 值，向編碼器指出要忽略輸入視訊中的旋轉中繼資料 (如果有的話)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另請參閱
[媒體服務編碼概觀](media-services-encode-asset.md)



<!--HONumber=Nov16_HO3-->


