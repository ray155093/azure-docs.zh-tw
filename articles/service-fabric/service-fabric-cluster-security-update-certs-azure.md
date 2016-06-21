<properties
   pageTitle="更新 Azure 叢集的憑證 | Microsoft Azure"
   description="說明如何將新憑證上傳到「Azure 金鑰保存庫」，以及更新或移除現有 Service Fabric 叢集的憑證。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# 新增或移除 Azure 中 Service Fabric 叢集的憑證

於叢集建立期間設定憑證安全性時，Service Fabric 可讓您指定兩個憑證 (主要和次要)。根據預設，您在建立時所指定的憑證是主要憑證。在叢集建立完成後，您可以新增憑證做為次要憑證，或是移除現有的憑證。如需有關 Service Fabric 如何使用 X.509 憑證的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

>[AZURE.NOTE] 對於安全的叢集，您一律需要至少部署一個有效 (未撤銷或過期) 的憑證 (主要或次要)，否則您將無法存取叢集。

## 新增次要憑證
若要新增另一個憑證做為次要憑證，您必須將該憑證上傳到 Azure 金鑰保存庫，然後將它部署到叢集中的 VM。如需其他資訊，請參閱 [Deploy certificates to VMs from a customer-managed Key Vault (將憑證從客戶管理的金鑰保存庫部署到 VM)](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。

1. [將 X.509 憑證上傳至金鑰保存庫](service-fabric-cluster-azure-secure-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至您想要新增此憑證的叢集資源。
3. 在 [設定] 底下，按一下憑證設定並輸入次要憑證指紋。
4. 按一下 [儲存]。隨即會開始部署，在部署順利完成後，您就可以使用主要或次要憑證在叢集上執行管理作業。

![入口網站中的憑證指紋螢幕擷取畫面][SecurityConfigurations_02]

## 移除憑證
以下是移除舊憑證的程序，以讓叢集不會使用它：

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至您叢集的安全性設定。
2. 移除其中一個憑證。
3. 按一下 [儲存]，隨即會開始一個新的部署。部署完成後，您移除的憑證就無法再用於連線到叢集。

## 後續步驟
如需有關叢集管理的詳細資訊，請參閱下列文件︰

- [Service Fabric 叢集升級程序與您的期望](service-fabric-cluster-upgrade.md)
- [設定用戶端的角色型存取](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->