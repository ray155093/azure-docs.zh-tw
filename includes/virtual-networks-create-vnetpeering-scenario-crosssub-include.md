## 跨訂用帳戶對等互連

在此案例中，您將在屬於不同訂用帳戶的兩個 Vnet 之間建立對等互連。

![跨子案例](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet 對等互連依賴角色型存取控制 (RBAC) 以取得授權。針對跨訂用帳戶案例，首先您需要將足夠的權限授與將建立對等互連連結的使用者︰

> [AZURE.NOTE] 如果同一個使用者擁有這兩個訂用帳戶的權限，則可以略過以下步驟 1-4。

<!---HONumber=AcomDC_0921_2016-->