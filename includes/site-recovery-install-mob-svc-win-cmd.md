1. 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 C:\Temp)。 請以網域系統管理員身分在命令提示字元執行下列命令：

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 若要安裝行動服務，請執行下列命令：

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>行動服務安裝程式命令列引數

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | 參數|類型|說明|可能的值|
  |-|-|-|-|
  |/Role|強制|指定是否應該安裝行動服務|代理程式 </br> MasterTarget|
  |/InstallLocation|強制|行動服務的安裝位置|電腦上的任何資料夾|
  |/CSIP|強制|組態伺服器的 IP 位址| 任何有效的 IP 位址|
  |/PassphraseFilePath|強制|複雜密碼的位置 |任何有效的 UNC 或本機檔案路徑|
  |/LogFilePath|選用|安裝記錄檔的位置|電腦上的任何有效資料夾|

#### <a name="example"></a>範例

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
