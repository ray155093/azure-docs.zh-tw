
1. 使用[從 Azure CLI 連接到 Azure](../articles/xplat-cli-connect.md) 中列出的步驟登入 Azure 訂用帳戶。
2. 請使用下列項目來確定您處於傳統部署模式：
   
        azure config mode asm
3. 找出您想要從可用映像載入的 Linux 映像：
   
        azure vm image list | grep "Linux"
   
   在 Windows 命令提示字元視窗中，請使用 **find**，而不要使用 grep。
4. 使用 `azure vm create` 並搭配先前清單中的 Linux 映像，建立新的虛擬機器。這個步驟會建立新的雲端服務及儲存體帳戶。您也可以利用 `-c` 選項將此虛擬機器連接到現有的雲端服務。它也會建立 SSH 端點以利用 `-e` 選項登入 Linux 虛擬機器。
   
        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-zh-TW-30GB -g adminUser -p P@ssw0rd! -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-zh-TW-30GB
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating VM
        info:    vm create command OK
   
   > [!NOTE]
   > 針對 Linux 虛擬機器，您必須在 `vm create` 中提供 `-e`。建立虛擬機器後，就無法啟用 SSH。如需 SSH 的詳細資料，請參閱[如何在 Azure 上搭配使用 SSH 和 Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)。
   > 
   > 
   
    映像 *b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-zh-TW-30GB* 是我們在先前步驟中從映像清單中選擇的映像。*MyTestVM* 是新虛擬機器的名稱，而 *adminUser* 是在虛擬機器中用於 SSH 的使用者名稱。您可以根據您的需求來取代這些變數。如需此命令的詳細資料，請造訪[搭配使用 Azure CLI 和傳統部署模型](../articles/virtual-machines-command-line-tools.md)。
5. 新建立的 Linux 虛擬機器會出現在透過下列命令取得的清單中：
   
        azure vm list
6. 您可以使用下列命令來確認虛擬機器的屬性：
   
        azure vm show MyTestVM
7. 新建立的虛擬機器已經準備開始使用 `azure vm start` 命令。

## 後續步驟
如需所有 Azure CLI 虛擬機器命令的詳細資料，請參閱[搭配使用 Azure CLI 和傳統部署 API](../articles/virtual-machines-command-line-tools.md)。

<!---HONumber=AcomDC_0824_2016-->