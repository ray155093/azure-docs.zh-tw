UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <要用於傳輸資料的 IP 位址] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

參數：

* / ServerMode：必要。 指定應該同時安裝組態和處理序伺服器，還是只安裝處理序伺服器。 輸入值：CS、PS
* InstallLocation：必要。 在其中安裝元件的資料夾。
* /MySQLCredsFilePath。 必要。 儲存 MySQL 伺服器認證的檔案路徑。 檔案格式應該如下：
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath。 必要。 保存庫認證檔的位置
* /EnvType。 必要。 安裝類型。 值：VMware、NonVMware
* /PSIP 和 /CSIP。 必要。 處理序伺服器和組態伺服器 IP 位址。
* /PassphraseFilePath。 必要。 複雜密碼檔案的位置。
* /BypassProxy。 選用。 指定組態伺服器不使用 Proxy 連接至 Azure。
* /ProxySettingsFilePath。 選用。 Proxy 設定 (預設 Proxy 需要驗證或自訂的 Proxy)。 檔案格式應該如下：
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort。 選用。 複寫資料的連接埠號碼。
* SkipSpaceCheck。 選用。 略過快取的空間檢查。
* AcceptThirdpartyEULA。 必要。 接受協力廠商使用者授權合約。
* ShowThirdpartyEULA。 必要。 顯示協力廠商使用者授權合約。 如果提供作為輸入，則會忽略所有其他參數。
