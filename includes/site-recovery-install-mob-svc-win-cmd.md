1. 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 C:\Temp)，並從提高的命令提示字元執行下列命令。

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 我們現在可以使用下列命令列來安裝行動服務

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>行動服務安裝程式命令列引數

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | 參數|類型|說明|可能的值|
  |-|-|-|-|
  |/Role|強制|指定是否應該要安裝行動服務|代理程式 </br> MasterTarget|
  |/InstallLocation|強制|將要安裝行動服務的位置|電腦上的任何資料夾|
  |/CSIP|強制|組態伺服器的 IP 位址| 任何有效的 IP 位址|
  |/PassphraseFilePath|強制|存放複雜密碼的位置 |任何有效的 UNC 或本機檔案路徑|
  |/LogGilePath|選用|安裝記錄檔的位置|電腦上的任何有效資料夾|

#### <a name="sample-usage"></a>範例用法

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


