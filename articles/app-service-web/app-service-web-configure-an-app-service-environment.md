<properties 
	pageTitle="如何設定 App Service 環境" 
	description="設定、管理和監視 App Service環境" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2016" 
	ms.author="ccompy"/>


# 設定 App Service 環境 #

## 概觀 ##

App Service 環境是由數個主要元件所組成：

- 在 Azure App 環境託管服務中執行的計算資源
- 儲存體
- 資料庫
- 傳統 "v1" 虛擬網路具有至少一個子網路
- 在其中執行 Azure App 環境託管服務的子網路

### 計算資源

計算資源會用於您的 4 個資源集區。每個 App Service 環境都有一組前端和 (或許) 3 個背景工作集區。您不需要 3 個背景工作角色集區都使用，如果您想要，可以只使用其中一或兩個。租用戶無法直接存取資源集區、前端和背景工作。也就是說，您無法為他們 RDP、變更其佈建，或作為其系統管理員，但是您可以設定它們的數量和大小。。在 ASE 中，您有 4 個大小選項，標記為 P1 到 P4。如需大小及其價格的詳細資訊，請參閱 [App Service 價格](../app-service/app-service-value-prop-what-is.md)。一次只能執行一項調整作業。

**前端**是您保留在 ASE 中之應用程式的 HTTP/HTTPS 端點。請勿在前端中執行工作負載。

- ASE 是以 (2) P2 開始，這對於開發/測試工作負載及低階生產工作負載而言已經足夠。強烈建議針對中度到重度生產工作流程使用 P3。
- 對於中度到重度生產工作流程而言，建議至少有 4 個 P3 以確保在排定的維護工作開始執行時有足夠的前端在執行中。排定的維護活動將一次關閉 (1) 個前端，因此在維護活動執行期間會降低整體可用的前端產能。
- 您無法立即新增新的前端執行個體。他們可能需要 2-3 小時來佈建。
- 如需進一步微調，客戶應監視前端集區的 CPU 使用百分比、記憶體使用百分比，以及使用中要求計量。如果執行 P3 時 CPU 或記憶體使用百分比超出 70%，請新增更多前端。如果每個前端的使用中要求數目平均達到 15K 至 20K 個要求，您也應該新增更多前端。整體目標是在執行 P3 時，將 CPU 和記憶體使用百分比維持在低於 70%，以及將每個前端的使用中要求平均數目維持在低於 15K 個要求。  

**背景工作**「背景工作」是您的應用程式實際執行之處。當您相應增加 App Service 計劃時，會用完相關聯背景工作集區中的背景工作。

- 您無法立即新增背景工作。無論新增多少背景工作，它們都可能需要 2 到 3 小時來佈建。
- 調整任何集區的計算資源大小時，每個更新網域都將需要 2-3 小時來完成。ASE 中有 20 個更新網域。如果您調整包含 10 個執行個體之背景工作集區的計算大小，可能需要 20 到 30 個小時來完成。 
- 如果您變更背景工作集區中使用的計算資源大小，將會導致在該背景工作集區中執行的應用程式冷啟動

變更未執行任何應用程式之背景工作集區計算資源大小的最快方式是：

- 將執行個體計數相應減少至 0。這將需要約 30 分鐘來解除配置您的執行個體
- 選取新的計算大小與執行個體數目。從這裡開始，將需要 2 到 3 小時來完成。

如果您的應用程式需要大型計算資源大小，您就無法利用之前的指引。您可以改為擴展另一個含有所需大小之背景工作的背景工作集區，並將您的應用程式移到該集區，而不需要變更裝載那些應用程式之背景工作集區的大小。

- 在另一個背景工作集區中建立所需計算大小的其他執行個體。這將需要 2 到 3 小時來完成。
- 將您需要較大大小之裝載應用程式的 App Service 計劃重新指派至新設定的背景工作集區。這是一項快速作業，應在一分鐘以內即可完成。  
- 如果您不再需要那些未使用的執行個體，請相應減少第一個背景工作集區。此作業約需要 30 分鐘來完成。

**自動調整** 可協助您管理計算資源耗用狀況的其中一項工具，就是您可以針對前端或背景工作集區執行的自動調整。您可以執行像是在早上增加任何集區類型的執行個體數，然後在傍晚時減少執行個體數，或在背景工作集區中可用的背景工作數降到低於特定閾值時，增加執行個體數。當您想要設定計算資源集區計量的自動調整規則時，請不要忘記考量佈建所需的時間。如需自動調整 App Service 環境的更多詳細資料，請移至[如何設定自動調整 App Service 環境][ASEAutoscale]

### 儲存體

每個 ASE 各設有 500 GB 的儲存體。此空間會用於 ASE 中的所有應用程式。此儲存空間屬於 ASE 的一部分，且目前無法切換為使用客戶的儲存空間。如果您調整 VNET 路由或安全性，您仍然需要允許存取 Azure 儲存體，否則 ASE 無法運作。

### 資料庫

資料庫會保存定義環境及詳細說明其中執行之應用程式的資訊。這同樣也屬於 Azure 訂用帳戶的一部分，且客戶無法直接加以操作。如果您調整 VNET 路由或安全性，您仍然需要允許存取 SQL Azure 儲存體，否則 ASE 無法運作。

### 網路

與 ASE 搭配使用的虛擬網路，可以是您在建立 ASE 時所設定的虛擬網路，或是您既有的虛擬網路。如果您想要讓 VNET 所屬的資源群組不同於用在 ASE 的資源群組，您必須在 ASE 建立流程以外個別設定 VNET。如果您在 ASE 建立期間建立子網路，它將會強制 ASE 位於和 VNET 相同的資源群組中。

將 VNET 用於 ASE 時有一些限制：

- 目前僅支援 V1「傳統」VNET。
- VNET 必須是區域 VNET
- 用來裝載 ASE 的 VNET 必須使用 RFC1918 位址 (也就是私人位址)
- 需要有包含 8 個或更多位址且為 ASE 部署所在的子網路
- 將子網路用於裝載 ASE 之後，就無法變更子網路的位址範圍。因此，建議子網路至少包含 64 個位置以適應未來 ASE 成長。 

不同於包含 ASE 的託管服務，[虛擬網路][virtualnetwork]和子網路全都在使用者的控制之下。透過虛擬網路 UI 或 Powershell 即可管理您的 VNET。

因為這項功能會將 Azure App Service 放入 VNET，這表示裝載於 ASE 的應用程式現在可以存取直接透過 ExpressRoute 或網站間 VPN 提供的資源。您的 App Service 環境中的應用程式不需要額外的網路功能，即可存取裝載 App Service 環境的 VNET 可用的資源。這表示您不需要使用 VNET 整合或混合式連線，即可將資源移入或連接到您的 VNET。但您仍然可以使用這兩個功能，存取網路中未連接到 VNET 的資源。比方說，您可以使用 VNET 整合，與您的訂用帳戶中未連接到您的 ASE 所在之 VNET 的 VNET 相整合。您仍可使用混合式連線來存取其他網路中的資源，一如既往。

如果您的 VNET 設定了 ExpressRoute VPN，則應留意 ASE 的某些路由需求。某些使用者定義的路由 (UDR) 組態與 ASE 不相容。如需關於在具有 ExpressRoute 的 VNET 中執行 ASE 的詳細資訊，請參閱以下文件：[在具有 ExpressRoute 的 VNET 中執行 App Service 環境][ExpressRoute]

您現在也可以使用網路安全性群組來控制應用程式的存取。這項功能可讓您鎖住您的 App Service 環境，只您想要限制它的 IP 位址。如需相關詳細資訊，請參閱以下文件：[如何在 App Service 環境中控制輸入流量](app-service-app-service-environment-control-inbound-traffic.md)。

## 入口網站

用來管理和監視 App Service 環境的 UI，可從 Azure 入口網站使用。如果您有 ASE，則可能會在資訊看板上看到 App Service 符號。這個符號用來代表 Azure 入口網站中的 App Service 環境。

![][1]

您可以使用圖示，或選取資訊看板底部的＞形箭號 (大於符號)，然後選取 App Service 環境。兩種方式都可開啟 UI，列出您所有的 App Service 環境。選取其中一個列出的 ASE，會開啟用來監視及管理 ASE 的 UI。

![][2]

這是第一個刀鋒視窗，顯示 ASE 的某些屬性和每個資源集區的計量圖表。某些顯示於 [基本功能] 區塊中的屬性同時也是超連結，將會開啟與其相關聯的刀鋒視窗。例如，您可以選取 [VNET 名稱]，以開啟與您的 ASE 執行所在的 VNET 相關聯的 UI。App Service 方案和應用程式分別會開啟不同的刀鋒視窗，列出位於您 ASE 中的項目。

### 監控

圖表可讓您檢視每個資源集區中各種不同的效能計量。對於前端集區，請監視平均 CPU 和記憶體使用量。對於背景工作集區，請監視使用的數量和可用數量。多個 App Service 方案可能會使用同一個背景工作角色集區中的背景工作。工作負載並不是透過和前端伺服器相同的方式散佈，所以 CPU 和記憶體使用量不會提供許多有用的資訊。更重要的是要追蹤您已使用和可用的背景工作角色數目，特別是在您管理此系統以供他人使用時。

所有可在圖表中追蹤的計量，也都可以用來設定警示。設定警示的效果與在 App Service 中的其他位置設定相同。您可以從警示 UI 部分設定警示，或深入探索任何計量 UI 並按 [新增警示] 來設定。
 
![][3]

剛剛所討論的計量是 App Service 環境計量。另外也有可在 App Service 方案層級上使用的計量。在其中監視 CPU 和記憶體是很合理的作法。在 ASE 中，所有的 ASP 都是專用 ASP。這表示，會在配置給該 ASP 的主機上執行的應用程式，僅限於該 ASP 中的應用程式。若要檢視 ASP 的詳細資料，請直接從 ASE UI 中的任何清單顯示您的 ASP，或藉由瀏覽 App Service 方案全部加以列出。

### 設定

ASE 刀鋒視窗中有一個包含幾項重要功能的「設定」區段。

[設定] > [屬性] [設定] 刀鋒視窗將在您顯示 ASE 刀鋒視窗時自動開啟。最上方是 [屬性]。在這裡會看到許多與 [基本功能] 重複的項目，但 [VIP 位址] 和 [輸出 IP 位址] 是非常好用的項目。

![][4]

[設定] > [IP 位址] 當您在 ASE 中建立 IP SSL 應用程式時，您需要一個 IP SSL 位址。若要那樣做，您的 ASE 必須擁有一些可配置的 IP SSL 位址。ASE 在建立時會擁有 1 個 IP SSL 以供此用途使用，但您可以新增更多位址。額外的 IP SSL 位址需付費，如 SSL 連線之小節中的 [App Service 定價][AppServicePricing]所示。額外的價格是 IP SSL 價格。

[設定] > [前端集區] > [背景工作集區]這些資源集區刀鋒視窗每一個都可讓您檢視該資源集區的資訊，以及提供充分調整該資源集區所需的控制。

每個資源集區的基準刀鋒視窗分別會提供一個圖表，內附該資源集區的計量。就像 ASE 刀鋒視窗中的圖表，您可以進入圖表，並設定所需的警示。從 ASE 刀鋒視窗為特定的資源集區設定警示，效果等同於從資源集區進行設定。在背景工作集區的 [設定] 刀鋒視窗中，您有權列出在此背景工作集區中執行的所有應用程式或 App Service 方案。

![][5]

### 入口網站調整功能  

共有三種級別作業：

- 變更 ASE 中可供 IP SSL 使用的 IP 位址數目
- 變更用於資源集區中的計算資源大小
- 以手動方式或透過自動調整變更用於資源集區中的計算資源數目

有三種方式可以在入口網站中控制資源集區的伺服器數量

- 從頂端的主要 ASE 刀鋒視窗進行調整作業。您可以對前端和背景工作集區變更多項調整設定，而且它們會以單一作業的方式全部套用。
- 從個別資源集區 [設定] 下的 [級別] 刀鋒視窗手動進行調整作業
- 從個別資源集區的 [級別] 刀鋒視窗自動調整設定

若要使用 ASE 刀鋒視窗上的調整作業，請將滑桿拖曳至所需的數量並儲存。此 UI 也支援變更大小作業。

![][6]

若要使用特定資源集區中的手動或自動調整估能，請移至適當的 [設定] > [前端集區] > [背景工作集區]，然後開啟您想要變更的集區。移至 [設定] > [相應放大或設定] > [相應增加]。[相應放大] 刀鋒視窗可讓您控制執行個體數量。[相應增加] 可讓您控制資源大小。

![][7]

## 容錯考量

App Service 環境可設定為最多總計使用 55 個計算資源。在這 55 個計算資源中，只有 50 個可用來裝載工作負載。其原因有兩個。前端計算資源的下限為 2 個。上限可高達 53 個，用以支援背景工作集區配置。為了提供容錯功能，您需要根據下列規則配置額外的計算資源：

- 每個背景工作角色集區至少需要一個無法被指派工作負載的額外計算資源
- 當背景工作角色集區中的計算資源數量超出特定值時，則需要另一個計算資源以進行容錯。在前端集區中並非如此。

在任何單一背景工作集區中，容錯需求就是指派給背景工作集區的 X 個資源的指定值：

- 如果 X 介於 2 到 20，您可用於工作負載的可用計算資源數量為 X-1
- 如果 X 介於 21 到 40，您可用於工作負載的可用計算資源數量為 X-2
- 如果 X 介於 41 到 53，您可用於工作負載的可用計算資源數量為 X-3

最少的使用量具有 2 個前端伺服器和 2 個背景工作。以上陳述可用幾個範例加以釐清。

- 如果您在單一集區中有 30 個背景工作，則其中有 28 個可用來裝載工作負載。 
- 如果您在單一集區中有 2 個背景工作角色，則有 1 個可用來裝載工作負載。
- 如果您在單一集區中有 20 個背景工作角色，則有 19 個可用來裝載工作負載。  
- 如果您在單一集區中有 21 個背景工作角色，則同樣只有 19 個可用來裝載工作負載。  

容錯是很重要的環節，但您在調整超過特定臨界值時必須加以留意。如果您想要從 20 個執行個體新增更多容量，請調整至 22 或更高，因為 21 並不會額外新增任何容量。調整為 40 以上時也是一樣，下一個會新增容量的數目為 42。

## 刪除 App Service 環境 ##

如果您要刪除 App Service 環境，只需使用 App Service 環境刀鋒視窗頂端的 [刪除] 動作。當您這麼做時，系統將提示您輸入 App Service 環境的名稱，以確認您真正想要執行這項操作。附註：當您刪除 App Service 環境時，將同時刪除其中包含的所有內容。

![][9]

## 開始使用

若要開始使用 App Service 環境，請參閱[如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0413_2016-->