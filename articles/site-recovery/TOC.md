# 概觀
## [什麼是 Site Recovery？](site-recovery-overview.md)
## [Site Recovery 如何運作？](site-recovery-components.md)
## [哪些工作負載可以受到保護？](site-recovery-workload.md)
## [Site Recovery 支援矩陣](site-recovery-support-matrix.md)
## [常見問題集](site-recovery-faq.md)
## [觀看簡介](https://www.youtube.com/watch?v=eOOwMQPBKfM)

# 開始使用
## [將 VMware VM 複寫至 Azure](site-recovery-vmware-to-azure.md)
## [在多租用戶部署 (CSP) 中將 VMware VM 複寫至 Azure](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [將 Hyper-V VM 複寫至 Azure (使用 VMM)](site-recovery-vmm-to-azure.md)
## [將 Hyper-V VM 複寫至 Azure](site-recovery-hyper-v-site-to-azure.md)
## [將 VMware VM 和實體伺服器複寫至次要網站](site-recovery-vmware-to-vmware.md)
## [將 Hyper-V VM 複寫至次要站台 (使用 VMM)](site-recovery-vmm-to-vmm.md)

# 作法
## 規劃
### [部署必要條件](site-recovery-prereq.md)
### [網路基礎結構的注意事項](site-recovery-network-design.md)
### [使用 Site Recovery Capacity Planner](site-recovery-capacity-planner.md)
### [規劃容量並調整 Azure 中的 VMware 複寫](site-recovery-plan-capacity-vmware.md)
## 設定
### [設定來源複寫環境](site-recovery-set-up-vmware-to-azure.md)
### [設定複寫設定](site-recovery-setup-replication-settings-vmware.md)
### [部署 VMware 複寫的行動服務](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [使用 System Center Configuration Manager 部署行動服務](site-recovery-install-mobility-service-using-sccm.md)
#### [使用 Azure 自動化 DSC 部署行動服務](site-recovery-automate-mobility-service-install.md)
### [移除伺服器並停用保護](site-recovery-manage-registration-and-protection.md)
## 移轉
### [移轉至 Azure](site-recovery-migrate-to-azure.md)
### [在不同的 Azure 地區之間移轉](site-recovery-migrate-azure-to-azure.md)
### [將 AWS Windows 執行個體移轉到 Azure](site-recovery-migrate-aws-to-azure.md)
## 工作負載
### [Active Directory 和 DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [其他工作負載](site-recovery-workload.md#workload-summary)
## 將複寫自動化
### [自動將 Hyper-V 複寫至 Azure (不使用 VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [自動將 Hyper-V 複寫至 Azure (透過 VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [自動將 Hyper-V 複寫至 Azure (透過 VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## 容錯移轉和容錯回復
### [在 Site Recovery 中進行容錯移轉](site-recovery-failover.md)
### [設定復原方案](site-recovery-create-recovery-plans.md)
#### [將 Runbook 新增至復原方案](site-recovery-runbook-automation.md)
### [執行從 VMware 到 Azure 的測試容錯移轉](site-recovery-test-failover-to-azure.md)
### [在兩個 VMM 站台間執行測試容錯移轉](site-recovery-test-failover-vmm-to-vmm.md)
### [容錯回復 VMware VM 和實體伺服器](site-recovery-failback-azure-to-vmware.md)
## [監視及疑難排解](site-recovery-monitoring-and-troubleshooting.md)

# 參考
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell 傳統](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# 相關參考
## [Azure 自動化](/azure/automation/)

# 資源
## [學習路徑](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [部落格](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [價格](https://azure.microsoft.com/pricing/details/site-recovery/)
## [服務更新](https://azure.microsoft.com/updates/?product=site-recovery)


<!--HONumber=Feb17_HO2-->


