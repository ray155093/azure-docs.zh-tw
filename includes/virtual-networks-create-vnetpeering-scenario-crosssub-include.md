## <a name="a-namex-subapeering-across-subscriptions"></a><a name="x-sub"></a>跨訂用帳戶對等互連
在此案例中，您會在存在於不同訂用帳戶的兩個 VNet 之間建立對等互連。

![跨子案例](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet 對等互連依賴角色型存取控制 (RBAC) 以取得授權。 針對跨訂用帳戶案例，首先您需要授與將建立對等互連連結的使用者足夠的權限。

> [!NOTE]
> 如果同一個使用者擁有這兩個訂用帳戶的權限，則可以略過以下步驟 1-4。
> 
> 



<!--HONumber=Feb17_HO1-->


