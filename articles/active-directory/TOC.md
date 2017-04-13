# 概觀
## [什麼是 Azure Active Directory？](active-directory-whatis.md)
## [選擇版本](active-directory-editions.md)
## [關於 Azure 身分識別管理](fundamentals-identity.md)
## [預覽 Azure AD 入口網站體驗](active-directory-preview-explainer.md)


# 開始使用
## [取得 Azure AD 租用戶](active-directory-howto-tenant.md)
## [註冊 Azure AD Premium](active-directory-get-started-premium.md)
## [關聯 Azure 訂用帳戶](active-directory-how-subscriptions-associated-directory.md)
## 管理 Azure AD 授權
### [Azure 入口網站](active-directory-licensing-get-started-azure-portal.md)
### [傳統入口網站](active-directory-licensing-what-is.md)
## [為您組織中的使用者取得 Azure](sign-up-organization.md)
## [常見問題集](active-directory-faq.md)
## [SaaS 應用程式教學課程](active-directory-saas-tutorial-list.md)

# 作法
## 規劃和設計
### [部署混合式身分識別解決方案](active-directory-hybrid-identity-design-considerations-overview.md)
#### 判斷需求
##### [身分識別](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [目錄同步](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [多因素驗證](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [身分識別生命週期策略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [資料安全性的規劃](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [資料保護](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [內容管理](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [存取控制](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [事件回應](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### 規劃身分識別生命週期
##### [工作](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [採用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [後續步驟](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [工具比較](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## 管理使用者
### 新增使用者
#### [Azure 入口網站](active-directory-users-create-azure-portal.md)
#### [傳統入口網站](active-directory-create-users.md)

### [從其他目錄中新增使用者 (傳統入口網站)](active-directory-create-users-external.md)
### [刪除使用者](active-directory-users-delete-user-azure-portal.md)
### [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
### [重設密碼](active-directory-users-reset-password-azure-portal.md)
### [管理使用者工作資訊](active-directory-users-work-info-azure-portal.md)
### [共用帳戶](active-directory-sharing-accounts.md)

## [管理群組和成員](active-directory-manage-groups.md)
### 管理群組
#### [Azure 入口網站](active-directory-groups-create-azure-portal.md)
#### [傳統入口網站](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [管理群組成員](active-directory-groups-members-azure-portal.md)
### [管理群組擁有者](active-directory-accessmanagement-managing-group-owners.md)
### [管理群組成員資格](active-directory-groups-membership-azure-portal.md)
### [檢視全部群組](active-directory-groups-view-azure-portal.md)
### [啟用專用群組](active-directory-accessmanagement-dedicated-groups.md)
### [新增 SaaS 應用程式的群組存取權](active-directory-accessmanagement-group-saasapps.md)
### 管理群組設定
#### [Azure 入口網站](active-directory-groups-settings-azure-portal.md)
#### [Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
### 建立進階規則
#### [Azure 入口網站](active-directory-groups-dynamic-membership-azure-portal.md)
#### [傳統入口網站](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [群組型授權](active-directory-licensing-whatis-azure-portal.md)
#### [將授權指派給群組](active-directory-licensing-group-assignment-azure-portal.md)
#### [識別並解決群組的授權問題](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [將個別授權使用者移轉至群組型的授權](active-directory-licensing-group-migration-azure-portal.md)
#### [群組型授權的其他案例](active-directory-licensing-group-advanced.md)
### [設定自助式群組](active-directory-accessmanagement-self-service-group-management.md)
### [疑難排解](active-directory-accessmanagement-troubleshooting.md)

## [管理報告](active-directory-reporting-azure-portal.md)
### [登入活動](active-directory-reporting-activity-sign-ins.md)
### [稽核活動](active-directory-reporting-activity-audit-logs.md)
### [有風險的使用者](active-directory-reporting-security-user-at-risk.md)
### [有風險的登入](active-directory-reporting-security-risky-sign-ins.md)
### [風險事件](active-directory-reporting-risk-events.md)
### [具名網路](active-directory-known-networks-azure-portal.md)
### [報告移轉](active-directory-reporting-migration.md)
### [保留](active-directory-reporting-retention.md)
### [延遲](active-directory-reporting-latencies-azure-portal.md)
### [常見問題集](active-directory-reporting-faq.md)
### 疑難排解
#### [缺少稽核資料](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [下載中缺少的資料](active-directory-reporting-troubleshoot-missing-data-download.md)
###    程式設計存取
#### [稽核參考](active-directory-reporting-api-audit-reference.md)
#### [稽核範例](active-directory-reporting-api-audit-samples.md)
#### [必要條件](active-directory-reporting-api-prerequisites.md)
#### [登入參考](active-directory-reporting-api-sign-in-activity-reference.md)
#### [登入範例](active-directory-reporting-api-sign-in-activity-samples.md)
### [傳統入口網站](active-directory-view-access-usage-reports.md)
#### [Azure AD 報告](active-directory-reporting-getting-started.md)
#### [報告指南](active-directory-reporting-guide.md)
#### [已知的網路](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [稽核事件](active-directory-reporting-audit-events.md)
#### [延遲](active-directory-reporting-latencies.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### 了解報告
##### [異常登入](active-directory-reporting-irregular-sign-in-activity.md)
##### [多次失敗](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [可疑 IP 位址](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [多個地理位置](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [可能受感染的裝置](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [不明來源](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [異常登入](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [管理密碼](active-directory-manage-passwords.md)
### [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)
### [註冊自助式密碼重設](active-directory-passwords-reset-register.md)
### [管理員啟用自助式密碼重設](active-directory-passwords.md)
### [了解密碼管理](active-directory-passwords-how-it-works.md)
### [了解原則和限制](active-directory-passwords-policy.md)
### 重設密碼
#### [Azure 入口網站](active-directory-users-reset-password-azure-portal.md)
#### [傳統入口網站](active-directory-create-users-reset-password.md)
### [設定到期原則](active-directory-passwords-set-expiration-policy.md)
### 啟用密碼管理
#### [開始使用](active-directory-passwords-getting-started.md)
#### [部署](active-directory-passwords-best-practices.md)
#### [自訂](active-directory-passwords-customize.md)
#### [檢視報告](active-directory-passwords-get-insights.md)
#### [深入了解](active-directory-passwords-learn-more.md)
#### [常見問題集](active-directory-passwords-faq.md)
#### [疑難排解](active-directory-passwords-troubleshoot.md)

## 管理裝置
### [註冊裝置](active-directory-device-registration-overview.md)
#### [設定](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [部署內部部署](active-directory-device-registration-on-premises-setup.md)
#### [常見問題集](active-directory-device-registration-faq.md)
#### 疑難排解
##### [針對 Windows 10 與 Windows Server 2016 進行疑難排解](active-directory-device-registration-troubleshoot-windows.md)
##### [針對 Windows 下層用戶端進行疑難排解](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Azure AD Join](active-directory-azureadjoin-overview.md)
#### [規劃](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [設定裝置註冊](active-directory-azureadjoin-setup.md)
#### [註冊新裝置](active-directory-azureadjoin-user-frx.md)
#### [部署](active-directory-azureadjoin-devices-group-policy.md)
#### [了解 Windows 10 整合](active-directory-azureadjoin-windows10-devices-overview.md)
#### [使用 Windows 10 裝置](active-directory-azureadjoin-windows10-devices.md)
#### [加入裝置](active-directory-azureadjoin-personal-device.md)
#### [加入 Windows 10 裝置](active-directory-azureadjoin-user-upgrade.md)

## 管理應用程式
### [概觀](active-directory-enable-sso-scenario.md)
### [開始使用](active-directory-integrating-applications-getting-started.md)

### [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
#### [更新登錄設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [了解安全性和隱私權](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [提供應用程式的遠端存取權](active-directory-application-proxy-get-started.md)
#### [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
#### [了解連接器](application-proxy-understand-connectors.md)
#### [發佈應用程式](application-proxy-publish-azure-portal.md)
#### [安全性](application-proxy-security-considerations.md)
#### [網路](application-proxy-network-topology-considerations.md)
#### [遠端桌面](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
#### [在不同網路上發佈](active-directory-application-proxy-connectors-azure-portal.md)
#### [Proxy 伺服器](application-proxy-working-with-proxy-servers.md)
#### [自訂網域](active-directory-application-proxy-custom-domains.md)
#### [存取應用程式](active-directory-appssoaccess-whatis.md)
##### [Azure 入口網站](application-proxy-sso-azure-portal.md)
#### [搭配 KCD 的 SSO](active-directory-application-proxy-sso-using-kcd.md)
#### [搭配標頭的 SSO](application-proxy-ping-access.md)
#### [宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
#### [原生用戶端應用程式](active-directory-application-proxy-native-client.md)
#### [自訂首頁](application-proxy-office365-app-launcher.md)
#### [條件式存取](active-directory-application-proxy-conditional-access.md)
#### [無訊息安裝](active-directory-application-proxy-silent-installation.md)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [疑難排解](active-directory-application-proxy-troubleshoot.md)
#### 使用傳統入口網站
##### [下載連接器](application-proxy-enable-classic-portal.md)
##### [發佈應用程式](active-directory-application-proxy-publish.md)
##### [使用連接器](active-directory-application-proxy-connectors.md)


### 管理企業應用程式
#### [指派使用者](active-directory-coreapps-assign-user-azure-portal.md)
#### [自訂商標](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [停用使用者登入](active-directory-coreapps-disable-app-azure-portal.md)
#### [移除使用者](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [檢視我的所有應用程式](active-directory-coreapps-view-azure-portal.md)
#### [管理使用者帳戶佈建](active-directory-enterprise-apps-manage-provisioning.md)

### 開發
#### [指派使用者](active-directory-applications-guiding-developers-assigning-users.md)
#### [指派群組](active-directory-applications-guiding-developers-assigning-groups.md)
#### [需要指派](active-directory-applications-guiding-developers-requiring-user-assignment.md)
#### [開發 LoB 應用程式](active-directory-applications-guiding-developers-for-lob-applications.md)

### [管理應用程式的存取權](active-directory-managing-access-to-apps.md)
#### [自助服務存取](active-directory-self-service-application-access.md)
#### [SSO 憑證](active-directory-sso-certs.md)
#### [租用戶限制](active-directory-tenant-restrictions.md)
#### [使用 SCIM 佈建使用者](active-directory-scim-provisioning.md)

### [疑難排解](active-directory-application-troubleshoot-content-map.md)
#### 影片應用程式開發
##### [設定和註冊](active-directory-application-dev-config-content-map.md)
##### [開發](active-directory-application-dev-development-content-map.md)
#### 應用程式管理
##### [組態](active-directory-application-config-content-map.md)
##### [登入](active-directory-application-sign-in-content-map.md)
##### [佈建](active-directory-application-provisioning-content-map.md)
##### [管理存取](active-directory-application-access-content-map.md)
##### [存取面板](active-directory-application-access-panel-content-map.md)
##### [應用程式 Proxy](active-directory-application-proxy-content-map.md)
##### [條件式存取](active-directory-application-conditional-access-content-map.md)

### [文件庫](active-directory-apps-index.md)

## 管理您的目錄
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### 自訂網域名稱
#### [概觀](active-directory-add-domain-concepts.md)
#### 新增您的網域名稱
##### [Azure 入口網站](active-directory-domains-add-azure-portal.md)
##### [傳統入口網站](active-directory-add-domain.md)
##### [使用 AD FS](active-directory-add-domain-federated.md)
#### [指派使用者](active-directory-add-domain-add-users.md)
#### 管理網域名稱
##### [Azure 入口網站](active-directory-domains-manage-azure-portal.md)
##### [傳統入口網站](active-directory-add-manage-domain-names.md)
### 自訂登入頁面
#### [Azure 入口網站](active-directory-branding-custom-signon-azure-portal.md)
#### [特定語言](active-directory-branding-localize-azure-portal.md)
#### [傳統入口網站](active-directory-add-company-branding.md)
### [管理目錄](active-directory-administer.md)
### [多個目錄](active-directory-licensing-directory-independence.md)
### [O365 目錄](active-directory-manage-o365-subscription.md)
### [自助式註冊](active-directory-self-service-signup.md)
### [企業狀態漫遊](active-directory-windows-enterprise-state-roaming-overview.md)
#### [啟用](active-directory-windows-enterprise-state-roaming-enable.md)
#### [群組原則設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 設定](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [疑難排解](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [將合作夥伴與 Azure AD B2B 整合](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [管理員新增 B2B 使用者](active-directory-b2b-admin-add-users.md)
#### [資訊背景工作新增 B2B 使用者](active-directory-b2b-iw-add-users.md)
#### [邀請電子郵件](active-directory-b2b-invitation-email.md)
#### [兌換邀請](active-directory-b2b-redemption-experience.md)
#### [B2B 使用者屬性](active-directory-b2b-user-properties.md)
#### [將來賓使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
#### [稽核與報告](active-directory-b2b-auditing-and-reporting.md)
#### [API 與自訂](active-directory-b2b-api.md)
#### [委派邀請](active-directory-b2b-delegate-invitations.md)
#### [動態群組與 B2B](active-directory-b2b-dynamic-groups.md)
#### [Multi-factor authentication for B2B](active-directory-b2b-mfa-instructions.md)
#### [B2B 使用者權杖](active-directory-b2b-user-token.md)
#### [B2B 使用者宣告對應](active-directory-b2b-claims-mapping.md)
#### [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
#### [設定適用於 B2B 的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
#### [程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
#### [目前限制](active-directory-b2b-current-limitations.md)
#### [授權](active-directory-b2b-licensing.md)
#### [疑難排解 B2B](active-directory-b2b-troubleshooting.md)
#### [比較 B2B 共同作業與 B2C](active-directory-b2b-compare-b2c.md)
#### [取得 B2B 支援](active-directory-b2b-support.md)
#### [常見問題集](active-directory-b2b-faq.md)
### [使用 Azure AD Connect 整合內部部署身分識別](./connect/active-directory-aadconnect.md)


## 委派資源存取
### [管理員角色](active-directory-assign-admin-roles.md)
#### [指派管理員角色](active-directory-users-assign-role-azure-portal.md)
### [管理單位](active-directory-administrative-units-management.md)
### [Azure 中的資源存取](active-directory-understanding-resource-access.md)
### [角色型存取控制](role-based-access-control-what-is.md)
#### 管理存取權指派
##### [依使用者](role-based-access-control-manage-assignments.md)
##### [依資源](role-based-access-control-configure.md)
#### [內建角色](role-based-access-built-in-roles.md)
#### [自訂角色](role-based-access-control-custom-roles.md)
#### [報告](role-based-access-control-access-change-history-report.md)
#### 更多管理角色的方式
##### [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [疑難排解](role-based-access-control-troubleshooting.md)
### [設定權杖存留期](active-directory-configurable-token-lifetimes.md)

## 保護您的身分識別
### [條件式存取](active-directory-conditional-access.md)
#### [開始使用](active-directory-conditional-access-azuread-connected-apps.md)
#### [支援的應用程式](active-directory-conditional-access-supported-apps.md)
#### [了解裝置原則](active-directory-conditional-access-device-policies.md)
#### [設定已連接應用程式的存取權](active-directory-conditional-access-policy-connected-applications.md)
#### [常見問題集](active-directory-conditional-faqs.md)
#### [疑難排解](active-directory-conditional-access-device-remediation.md)
#### [參考](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [無需密碼驗證](active-directory-azureadjoin-passport.md)
#### [啟用 Windows Hello 企業版](active-directory-azureadjoin-passport-deployment.md)
### 憑證式驗證
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [啟用](active-directory-identityprotection-enable.md)
#### [偵測弱點](active-directory-identityprotection-vulnerabilities.md)
#### [風險事件](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [登入體驗](active-directory-identityprotection-flows.md)
#### [模擬風險事件](active-directory-identityprotection-playbook.md)
#### [解除封鎖使用者](active-directory-identityprotection-unblock-howto.md)
#### [詞彙](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [在 Azure VM 上部署 AD DS](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Azure VM 上的 Windows Server Active Directory](active-directory-deploying-ws-ad-guidelines.md)
### [Azure 虛擬網路中的複本網域控制站](active-directory-install-replica-active-directory-domain-controller.md)
### [在 Azure 虛擬網路上的新樹系](active-directory-new-forest-virtual-machine.md)



## [在 Azure 中部署 AD FS](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [變更簽章雜湊演算法](active-directory-federation-sha256-guidance.md)

## [疑難排解](active-directory-troubleshooting.md)

## 部署 Azure AD 概念證明 (PoC)
### [PoC 腳本：簡介](active-directory-playbook-intro.md)
### [PoC 腳本：因素](active-directory-playbook-ingredients.md)
### [PoC 腳本：實作](active-directory-playbook-implementation.md)
### [PoC 腳本：建置區塊](active-directory-playbook-building-blocks.md)


# 參考
## [PowerShell Cmdlet](/powershell/ )
## [Java API 參考](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [服務限制](active-directory-service-limits-restrictions.md)

# 相關參考
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [開發人員適用的 Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# 資源
## [價格](https://azure.microsoft.com/pricing/details/active-directory/)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [服務更新](https://azure.microsoft.com/updates/?product=active-directory)
## [Azure 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory)
