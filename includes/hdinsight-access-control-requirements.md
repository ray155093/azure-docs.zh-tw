如果您使用某個 Azure 訂用帳戶，但您不是其系統管理員/擁有者 (例如公司擁有的訂用帳戶)，您必須先確認下列事項再使用本文件中的步驟︰

* 在建立 HDInsight (以及其他 Azure 資源) 時，您的 Azure 登入至少必須有您使用之 Azure 資源群組的__參與者__存取權。

* 至少具備 Azure 訂用帳戶之__參與者__存取權的某人，必須已在先前註冊您要使用之資源的提供者。具有訂用帳戶之參與者存取權的使用者第一次在訂用帳戶上建立資源時，便會註冊提供者。透過[使用 REST 註冊提供者](https://msdn.microsoft.com/library/azure/dn790548.aspx)，也可以不需要建立資源就完成註冊作業。

如需使用存取管理的詳細資訊，請參閱下列文件：

* [開始在 Azure 入口網站中使用存取管理](../articles/active-directory/role-based-access-control-what-is.md)
* [使用角色指派來管理 Azure 訂用帳戶資源的存取權](../articles/active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_1005_2016-->