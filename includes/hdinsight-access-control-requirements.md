您可以使用 Azure 訂用帳戶，但您不是其系統管理員或擁有者 (例如公司擁有的訂用帳戶)。 如果情況如此，您必須確認已取得下列各項，才便遵循本文中的步驟︰

* 參與者存取權。 您至少需要具備 Azure 資源群組的「參與者」存取權限才能登入 Azure。 此資源群組用來建立 HDInsight 叢集和其他 Azure 資源。
* 提供者註冊。 至少需要具備 Azure 訂用帳戶的「參與者」存取權之某人，必須已在先前註冊您要使用之資源的提供者。 具有訂用帳戶之參與者存取權的使用者第一次在訂用帳戶上建立資源時，便會註冊提供者。 [透過 REST 註冊提供者](https://msdn.microsoft.com/library/azure/dn790548.aspx)，也可以不需要建立資源就完成註冊作業。

如需使用存取管理的詳細資訊，請參閱下列文章：

* [開始在 Azure 入口網站中使用存取管理](../articles/active-directory/role-based-access-control-what-is.md)
* [使用角色指派來管理 Azure 訂用帳戶資源的存取權](../articles/active-directory/role-based-access-control-configure.md)
