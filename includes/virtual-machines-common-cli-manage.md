* 如果您尚未安裝 Azure CLI 及連線至您的訂用帳戶，請參閱[安裝 Azure CLI](../articles/xplat-cli-install.md) 和[從 Azure CLI 連線至 Azure 訂用帳戶](../articles/xplat-cli-connect.md)。當您想要使用 Resource Manager 模式命令時，請務必使用 `azure login` 命令連線。

* 若要開始使用 Azure CLI 中的資源管理員模式，您可能需要切換命令模式。根據預設，CLI 會在服務管理模式下啟動。若要變更為資源管理員模式，請執行 `azure config mode arm`。若要回到服務管理模式，請執行 `azure config mode asm`。

* 如需線上命令說明和選項，請輸入 `azure <command> <subcommand> --help` 或 `azure help <command> <subcommand>`。

## VM 工作
下表比較常見的 VM 工作，您可以在服務管理模式和 Resource Manager 模式中使用 Azure CLI 命令來執行這類工作。使用許多資源管理員命令時，您需要傳遞現有的資源群組名稱。

> [AZURE.NOTE] 這些範例不包含以範本為基礎的作業，這些作業一般建議在資源管理員中針對 VM 部署使用。如需資訊，請參閱[搭配 Azure 資源管理員使用 Azure CLI](../articles/xplat-cli-azure-resource-manager.md) 和[使用 Azure 資源管理員範本和 Azure CLI 部署與管理虛擬機器](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md)。

工作 | 服務管理模式 | 資源管理員模型
-------------- | ----------- | -------------------------
建立最基本的 VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(執行 `azure help vm quick-create`，以取得熱門 Marketplace 映像的 `image-urn`。例如，對於 *publisherName:offer:skus:version* 形式的 URN，請參閱[本文](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md))。
建立 Linux VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
建立 Windows VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
列出 VM | `azure  vm list [options]` | `azure  vm list [options]`
取得 VM 的相關資訊 | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
啟動 VM | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
停止 VM | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
解除配置 VM | 尚未提供 | `azure vm deallocate [options] <resource-group> <name>`
重新啟動 VM | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
刪除 VM | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
擷取 VM | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
從使用者映像建立 VM | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
從特殊化磁碟建立 VM | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
將資料磁碟新增至 VM | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -或- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
從 VM 移除資料磁碟 | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
將泛型延伸模組新增至 VM | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
將 VM 存取延伸模組新增至 VM | 尚未提供 | `azure vm reset-access [options] <resource-group> <name>`
將 Docker 延伸模組新增至 VM | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
將 Chef 延伸模組新增至 VM | `azure  vm extension get-chef [options] <vm-name>` | 尚未提供
停用 VM 延伸模組 | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | 尚未提供
移除 VM 延伸模組 | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
列出 VM 延伸模組 | `azure vm extension list [options]` | 尚未提供
顯示 VM 映像 | `azure vm image show [options]` | 尚未提供
取得 VM 資源的使用量 | 尚未提供 | `azure vm list-usage [options] <location>`
取得所有可用的 VM 大小 | 尚未提供 | `azure vm sizes [options]`


## 後續步驟

* 如需 CLI 命令的其他範例，請參閱 [Azure 服務管理模式中的 Azure CLI 命令](../articles/virtual-machines-command-line-tools.md)和 [Azure Resource Manager 模式中的 Azure CLI 命令](../articles/virtual-machines/azure-cli-arm-commands.md)。

