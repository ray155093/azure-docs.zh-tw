

自從自訂指令碼擴充功能可供使用後，已廣泛用來在 Windows 和 Linux VM 上設定工作負載。 隨著 Azure 資源管理員範本的引入，使用者現在可以建立單一的範本，不只可用於佈建 VM，也可在 VM 上設定工作負載。

## <a name="about-azure-resource-manager-templates"></a>關於 Azure Resource Manager 範本
Azure Resource Manager 範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。 如需 Azure 資源管理員範本的詳細概觀，請參閱以下文章：

* [資源群組概觀](../articles/azure-resource-manager/resource-group-overview.md)
* [以 Azure PowerShell 部署範本](../articles/virtual-machines/windows/ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>必要條件
1. 在[這裡](https://azure.microsoft.com/downloads/)下載適用於您作業系統的 Azure 命令列工具。
2. 如果將在現有 VM 上執行指令碼，請確定會在該 VM 上啟用 VM 代理程式，如果沒有，請依照 [Linux](../articles/virtual-machines/linux/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 或 [Windows](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 指南來安裝 VM。
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。 指令碼可以來自單一或多個儲存體容器。
4. 或者也能將指令碼上傳至 GitHub 帳戶。
5. 指令碼應該以由延伸模組依序要啟動的項目指令碼啟動其他指令碼的方式來撰寫。

## <a name="using-the-custom-script-extension"></a>使用自訂指令碼擴充功能
為了部署範本，我們使用 Azure 服務管理 API 中可用之相同版本的自訂指令碼擴充功能。 本延伸模組支援將檔案上傳至 Azure 儲存體帳戶或 GitHub 位置的相同參數和案例。 搭配範本使用時，主要差異在於應該指定正確版本的延伸模組，而不是以 majorversion.* 格式指定版本。

