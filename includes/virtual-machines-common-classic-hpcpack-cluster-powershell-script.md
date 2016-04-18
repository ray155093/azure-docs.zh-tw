



根據您的環境和選擇，指令碼可建立所有的叢集基礎結構，包括 Azure 虛擬網路、儲存體帳戶、雲端服務、網域控制站、遠端或本機 SQL Database、前端節點、訊息代理程式節點、運算節點與 Azure 雲端服務 (「高載」或 PaaS) 節點。或者，指令碼可使用既有的 Azure 基礎結構，然後建立 HPC 叢集前端節點、訊息代理程式節點、運算節點和 Azure 高載節點。


如需規劃 HPC Pack 叢集的背景資訊，請參閱 HPC Pack TechNet 文件庫中的[產品評估及規劃](https://technet.microsoft.com/library/jj899596.aspx)和[快速入門](https://technet.microsoft.com/library/jj899590.aspx)內容。



## 必要條件

* **Azure 訂用帳戶** - 您可以在 Azure 全域或 Azure China 服務中使用訂用帳戶。您的訂用帳戶限制將會影響到您可以部署的叢集節點類型與數量。如需相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../articles/azure-subscription-service-limits.md)。


* **已安裝並設定 Azure PowerShell 0.8.7 或更新版本的 Windows 用戶端電腦** - 請參閱[安裝及設定 Azure PowerShell](../articles/powershell-install-configure.md)。指令碼會以 Azure 服務管理 (asm) 模式執行。


* **HPC Pack IaaS 部署指令碼** - 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)下載並解壓縮最新版的指令碼。執行 `New-HPCIaaSCluster.ps1 –Version` 以檢查指令碼的版本。這篇文章是根據 4.4.0 版的指令碼撰寫的。

* **指令碼組態檔** - 您必須建立讓指令碼用來設定 HPC 叢集的 XML 檔案。如需相關資訊和範例，請參閱本文稍後的章節。


## 語法

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]您必須以系統管理員身分執行指令碼。

### 參數

* **/ConfigFile** - 指定說明 HPC 叢集之組態檔的檔案路徑。如需詳細資訊，請參閱本主題中的[組態檔](#Configuration-file)，或指令碼所在之資料夾中的檔案 Manual.rtf。

* **AdminUserName** - 指定使用者名稱。如果網域樹系是由指令碼所建立，這將會成為所有 VM 的本機系統管理員使用者名稱和網域系統管理員名稱。如果網域樹系已存在，則會將網域使用者指定為安裝 HPC Pack 的本機系統管理員使用者名稱。

* **AdminPassword** -指定系統管理員的密碼。如果未在命令列中指定，指令碼會提示您輸入密碼。

* **HPCImageName** (選擇性) - 指定用來部署 HPC 叢集的 HPC Pack VM 映像名稱。它必須是 Microsoft 在 Azure Marketplace 中提供的 HPC Pack 映像。如果未指定 (在大部分的情況下建議使用)，指令碼會選擇最新發佈的 HPC Pack 映像。

    >[AZURE.NOTE] 若未指定有效的 HPC Pack 映像，部署將會失敗。

* **LogFile** (選擇性) - 指定部署記錄檔路徑。若未指定，指令碼會在執行指令碼之電腦的暫存目錄中建立記錄檔。

* **Force** (選擇性) - 抑制所有的確認提示。

* **NoCleanOnFailure** (選擇性) - 指定未成功部署的 Azure VM 將不會移除。您必須先手動移除這些 VM 才能重新執行指令碼以繼續部署，否則部署可能會失敗。

* **PSSessionSkipCACheck** (選擇性) - 針對每個使用此指令碼部署 VM 的雲端服務，都會由 Azure 自動產生自我簽署憑證，且雲端服務中的所有 VM 都會使用此憑證作為預設 Windows 遠端管理 (WinRM) 憑證。為了在這些 Azure VM 中部署 HPC 功能，指令碼會依預設將這些憑證暫時安裝在用戶端電腦的「本機電腦\\信任的根憑證授權單位」存放區中，以抑制指令碼執行期間的「不受信任的 CA」安全性錯誤；指令碼完成時即會移除憑證。如果指定此參數，則不會在用戶端電腦上安裝憑證，並且會抑制安全性警告。

    >[AZURE.IMPORTANT] 此參數不建議用於生產部署。

### 範例

下列範例會使用組態檔 *MyConfigFile.xml* 建立新的 HPC Pack 叢集，並指定用來安裝叢集的系統管理認證。

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### 其他考量

* 指令碼會使用 Azure Marketplace 中的 HPC Pack VM 映像來建立叢集前端節點。最新的映像是基於已安裝 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。

* 指令碼可選擇性地讓工作透過 HPC Pack Web 入口網站或 HPC Pack REST API 來提交。

* 如果您想要安裝其他軟體或進行其他設定，指令碼可以選擇性地在前端節點上執行自訂的前置和後置組態指令碼。


## 組態檔

部署指令碼的組態檔是 XML 檔案。結構描述檔案 HPCIaaSClusterConfig.xsd 位於 HPC Pack IaaS 部署指令碼資料夾中。**IaaSClusterConfig** 是組態檔的根元素，其中包含相關子元素用以說明部署指令碼資料夾中的檔案 Manual.rtf。如需不同案例的範例檔案，請參閱本文中的[範例組態檔](#Example-configuration-files)。

<!---HONumber=AcomDC_0406_2016-->