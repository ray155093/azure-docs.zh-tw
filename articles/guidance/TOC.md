# 架構

## 雲端基本概念

### [設計彈性的應用程式](guidance-resiliency-overview.md)
#### [復原檢查清單](guidance-resiliency-checklist.md)
#### [失敗模式分析](guidance-resiliency-failure-mode-analysis.md)
#### [災害復原](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [災害復原和高可用性](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [高可用性](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [高可用性檢查清單](..\resiliency\resiliency-high-availability-checklist.md)
#### [Azure 服務專用復原指引](..\resiliency\resiliency-service-guidance-index.md)
#### [從資料損毀或意外刪除復原](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [從本機失敗復原](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)。
#### [從區域全面性服務中斷復原](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [從內部部署復原到 Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure 復原技術指導](..\resiliency\resiliency-technical-guidance.md)


## 參考架構

### [在Azure 中執行 VM 工作負載](guidance-ra-compute.md)
#### [在 Azure 上執行 Linux VM](guidance-compute-single-vm-linux.md)
#### [在 Azure 上執行 Windows VM](guidance-compute-single-vm.md)
#### [在 Azure 上執行多個 VM 以獲得延展性和可用性](guidance-compute-multi-vm.md)
#### [執行多層式架構的 Linux VM](guidance-compute-n-tier-vm-linux.md)
#### [執行多層式架構的 Windows VM](guidance-compute-n-tier-vm.md)
#### [在多個區域中執行 Linux VM 以獲得高可用性](guidance-compute-multiple-datacenters-linux.md)
#### [在多個區域中執行 Windows VM 以獲得高可用性](guidance-compute-multiple-datacenters.md)

### [將內部部署網路連接到 Azure](guidance-ra-hybrid-networking.md)
#### [搭配 Azure ExpressRoute 的混合式網路架構](guidance-hybrid-network-expressroute.md)
#### [配 Azure 和內部部署 VPN 的混合式網路架構](guidance-hybrid-network-vpn.md)
#### [高可用性的混合式網路架構](guidance-hybrid-network-expressroute-vpn-failover.md)

### [在 Azure 中保護雲端界限](guidance-ra-network-security.md)
#### [Azure 中的安全混合式網路架構](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Azure 和網際網路之間的 DMZ](guidance-iaas-ra-secure-vnet-dmz.md)

### [在 Azure 中管理身分識別](guidance-ra-identity.md)
#### [實作 Azure Active Directory](guidance-identity-aad.md)
#### [將 ADDS 擴充至 Azure](guidance-identity-adds-extend-domain.md)
#### [在 Azure 中建立 ADDS 資源樹系](guidance-identity-adds-resource-forest.md)
#### [在 Azure 中實作 ADFS](guidance-identity-adfs.md)

### [適用於 Azure App Service 的 Web 應用程式架構](guidance-ra-app-service.md)
#### [基本 Web 應用程式](guidance-web-apps-basic.md)
#### [高可用性的 Web 應用程式](guidance-web-apps-multi-region.md)
#### [改善 Web 應用程式的延展性](guidance-web-apps-scalability.md)


## 雲端應用程式的最佳作法

### [API 設計指引](..\best-practices-api-design.md)
### [API 實作指引](..\best-practices-api-implementation.md)
### [自動調整指引](..\best-practices-auto-scaling.md)
### [可用性檢查清單](..\best-practices-availability-checklist.md)
### [背景作業指引](..\best-practices-background-jobs.md)
### [Asure 配對區域](..\best-practices-availability-paired-regions.md)
### [快取指引](..\best-practices-caching.md)
### [內容傳遞網路指導](..\best-practices-cdn.md)
### [資料分割指引](..\best-practices-data-partitioning.md)
### [監視和診斷指引](..\best-practices-monitoring.md)
### [Microsoft 雲端服務和網路安全性](..\best-practices-network-security.md)
### [設計 Azure Resource Manager 範本的模式](..\best-practices-resource-manager-design-templates.md)
### [Azure 資源的建議命名慣例](guidance-naming-conventions.md)
### [Azure Resource Manager 的安全性考量](..\best-practices-resource-manager-security.md)
### [Azure Resource Manager 範本中的共用狀態](..\best-practices-resource-manager-state.md)
### [重試一般指引](..\best-practices-retry-general.md)
### [重試服務專用指引](..\best-practices-retry-service-specific.md)
### [延展性檢查清單](..\best-practices-scalability-checklist.md)


## 案例指南

### [Azure 上的 Elasticsearch 指引](guidance-elasticsearch.md)
#### [在 Azure 上執行 Elasticsearch](guidance-elasticsearch-running-on-azure.md)
#### [調整資料擷取效能](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [調整資料彙總和查詢效能](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [設定彈性與復原](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [建立效能測試環境](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [實作 JMeter 測試計劃](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [部署 JMeter JUnit 取樣](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [執行自動化復原測試](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [執行自動化效能測試](guidance-elasticsearch-running-automated-performance-tests.md)

### [多組織用戶共享應用程式的身分識別管理](guidance-multitenant-identity.md)
#### [概觀](guidance-multitenant-identity-intro.md)
#### [Tailspin Surveys 應用程式](guidance-multitenant-identity-tailspin.md)
#### [使用 Azure AD 和 OpenID Connect 進行驗證](guidance-multitenant-identity-authenticate.md)
#### [宣告式身分識別](guidance-multitenant-identity-claims.md)
#### [註冊和租用戶上架](guidance-multitenant-identity-signup.md)
#### [應用程式角色](guidance-multitenant-identity-app-roles.md)
#### [角色和資源型授權](guidance-multitenant-identity-authorize.md)
#### [保護後端 Web API](guidance-multitenant-identity-web-api.md)
#### [快取存取權杖](guidance-multitenant-identity-token-cache.md)
#### [與客戶的 AD FS 同盟](guidance-multitenant-identity-adfs.md)
#### [使用用戶端判斷提示取得存取權杖](guidance-multitenant-identity-client-assertion.md)
#### [使用 Azure 金鑰保存庫來保護應用程式祕密](guidance-multitenant-identity-keyvault.md)
#### [部署高可用性的虛擬應用裝置](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


