

## <a name="memory-preserving-updates"></a>記憶體保留的更新
對於 Microsoft Azure 中的某個更新類別，客戶不會看到其執行中的虛擬機器受到任何影響。 許多這些更新都是針對元件或服務，更新時不會干擾執行中的執行個體。 這些更新有些是主機作業系統上的平台基礎結構更新，不需要將虛擬機器完整重新開機就可以套用。

這些更新是透過可進行就地即時移轉 (也稱為「記憶體保留」更新) 的技術來完成。 更新時，虛擬機器會進入「暫停」狀態，在 RAM 中保留記憶體，而基礎主機作業系統會收到必要的更新和修補程式。 虛擬機器會在暫停後 30 秒內繼續執行。 繼續執行後，系統將會自動同步化虛擬機器的時鐘。

並非所有更新都可以透過這種機制來部署，但因為有短暫的暫停期間，以這種方式部署更新可大幅減少對虛擬機器的影響。

多重執行個體更新 (針對可用性集合中的虛擬機器) 一次僅套用到一個更新網域。  

## <a name="virtual-machine-configurations"></a>虛擬機器組態
虛擬機器組態有兩種：多重執行個體和單一執行個體。 在多重執行個體組態中，類似的虛擬機器會放置在可用性集合。

多重執行個體組態可提供跨實體機器、電源及網路的備援，因此我們建議您採用此組態以確保應用程式的可用性。 可用性設定組中所有虛擬機器對您應用程式的用途都應該相同。

如需設定虛擬機器以提供高可用性的詳細資訊，請參閱[管理 Windows 虛擬機器的可用性](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[管理 Linux 虛擬機器的可用性](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

相較之下，單一執行個體組態會用於未放置在可用性集合的獨立虛擬機器。 這些虛擬機器並不符合服務等級協定 (SLA) 的資格，服務等級協定需要在相同可用性設定組中部署兩部以上的虛擬機器。

如需 SLA 的詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)中的〈雲端服務和虛擬機器〉一節。

## <a name="multi-instance-configuration-updates"></a>多重執行個體組態更新
計劃性維護期間，Azure 平台首先會更新裝載多重執行個體組態的虛擬機器集合。 更新會讓這些虛擬機器重新開機，而停機時間大約為 15 分鐘。

多重執行個體組態更新假設每個虛擬機器有可用性設定組中其他虛擬機器的類似功能。 在此設定中，虛擬機器會以保留整個程序可用性的方式來更新。

基礎 Azure 平台會為可用性設定組中的每部虛擬機器指派一個更新網域和一個容錯網域。 每個更新網域是指一個虛擬機器群組，且群組內的虛擬機器會在相同的時間範圍內重新開機。 每個容錯網域是指一個虛擬機器群組，且群組內的虛擬機器會共用通用電源和網路交換器。


如需有關更新網域和容錯網域的詳細資訊，請參閱 [在可用性集合中設定多個虛擬機器以取得備援](../articles/virtual-machines/virtual-machines-windows-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。

為了透過更新維持可用性，Azure 會藉由更新網域來執行維護，一次更新一個網域。 更新網域中的維護包含將網域中的每部虛擬機器關機、將更新套用至主機機器，然後重新啟動虛擬機器。 當網域中的維護完成時，Azure 會對下一個更新網域重複此程序，並且會對每個網域繼續直到所有網域都更新完成為止。

規劃性維護期間的更新網域可能不會循序重新啟動，而是一次僅將一個更新網域重新開機。 目前 Azure 對於多重執行個體組態中虛擬機器的計畫性維護作業，會提供 1 週的事前通知。

還原虛擬機器之後，以下是您的 Windows 事件檢視器可能會顯示的範例：

<!--Image reference-->
![][image2]


使用檢視器報告虛擬機器是使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 設定於多重執行個體組態中。 例如，使用 Azure 入口網站，您可以將_可用性設定組_新增至 [虛擬機器 (傳統)] 瀏覽對話方塊。 報告相同可用性設定組的虛擬機器是多重執行個體組態的一部分。 在下列範例中，多重執行個體組態包含虛擬機器 SQLContoso01 和 SQLContoso02。

<!--Image reference-->
  ![從 Azure 入口網站的虛擬機器 (傳統) 檢視][image4]

## <a name="single-instance-configuration-updates"></a>單一執行個體組態更新
多重執行個體組態更新完成之後，Azure 會執行單一執行個體組態更新。 未在可用性設定組中執行的虛擬機器也會因為這些更新而重新開機。

> [!NOTE]
> 如果可用性設定組只有一個虛擬機器執行個體正在執行中，Azure 平台會將它視為多重執行個體組態更新。
>

單一執行個體組態中的維護包含將主機機器上執行的每部虛擬機器關機、更新主機機器，然後重新啟動虛擬機器。 維護需要大約 15 分鐘的停機時間。 區域中的所有虛擬機器會在單一維護期間執行已計劃的維護事件。


已計劃的維護事件會對單一執行個體組態的應用程式可用性造成影響。 在單一執行個體組態中，對於虛擬機器已計劃的維護，Azure 提供一週的事先通知。

## <a name="email-notification"></a>電子郵件通知
Azure 只會針對單一執行個體和多重執行個體虛擬機器組態，傳送近期已計劃的維護電子郵件警示 (提前&1; 週)。 這封電子郵件會傳送到訂用帳戶管理員和共同管理員電子郵件帳戶。 以下是這種電子郵件類型的範例：

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>區域配對

Azure 在執行維護作業時，只會更新區域配對中單一區域的虛擬機器執行個體。 舉例來說，Azure 在更新美國中北部的虛擬機器時，就不會同時更新任何在美國中南部的虛擬機器。 這兩次更新作業會分別排定在不同的時間，以啟用區域之間的容錯移轉或負載平衡功能。 不過，像是北歐等其他區域可以和美國東部一同進行維護。

請參閱下列表格以取得目前的區域配對︰

| 區域 1 | 區域 2 |
|:--- | ---:|
| 美國東部 |美國西部 |
| 美國東部 2 |美國中部 |
| 美國中北部 |美國中南部 |
| 美國中西部 |美國西部 2 |
| 加拿大東部 |加拿大中部 |
| 巴西南部 |美國中南部 |
| 美國政府愛荷華州 |美國政府維吉尼亞州 |
| 美國 DoD 東部 |美國國防部中央 |
| 北歐 |西歐 |
| 英國西部 |英國南部 |
| 德國中部 |德國東北部 |
| 東南亞 |東亞 |
| 澳大利亞東南部 |澳洲東部 |
| 印度中部 |印度南部 |
| 印度西部 |印度南部 |
| 日本東部 |日本西部 |
| 韓國中部 |韓國南部 |
| 中國東部 |中國北部 |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
