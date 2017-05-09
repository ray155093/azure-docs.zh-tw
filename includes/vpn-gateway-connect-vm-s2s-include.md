您可以建立 VM 的遠端桌面連線，以連線至已部署至 VNet 的 VM。 一開始確認您可以連線至 VM 的最佳方法是使用其私人 IP 位址 (而不是電腦名稱) 進行連線。 這樣一來，您會測試以查看您是否可以連線，而不是否已正確設定名稱解析。

1. **找出私人 IP 位址。** 在 Azure 入口網站中或使用 PowerShell 查看 VM 的屬性，即可找到 VM 的私人 IP 位址。

  - Azure 入口網站 - 在 Azure 入口網站中尋找您的虛擬機器。 檢視 VM 的屬性。 系統會列出私人 IP 位址。

  - PowerShell - 使用範例來檢視資源群組中的 VM 和私人 IP 位址清單。 使用此範例前，您不需要加以修改。

    ```powershell
    $vms = get-azurermvm
    $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null

    foreach($nic in $nics)
    {
      $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
      $prv = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
      $alloc = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($vm.Name): $prv,$alloc"
    }
    ```

2. **連線至 VM。** 確認您已使用 VPN 連線來連線至 VNet。 在工作列上的搜尋方塊中輸入「RDP」或「遠端桌面連線」以開啟遠端桌面連線，然後選取 [遠端桌面連線]。 您也可以使用 `mstsc` PowerShell 命令開啟遠端桌面連線。 在 [遠端桌面連線] 中，輸入 VM 的私人 IP 位址。 您可以按一下 [顯示選項] 來調整其他設定，然後進行連線。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>針對 VM 的 RDP 連線進行疑難排解

如果您無法透過 VPN 連線與虛擬機器連線，您可以檢查幾件事。

- 確認您的 VPN 連線成功。
- 確認您是連線至 VM 的私人 IP 位址。
- 如果您可以使用私人 IP 位址 (而非電腦名稱) 來連線至 VM，請確認您已正確設定 DNS。
- 如需詳細資訊，請參閱[針對 VM 的遠端桌面連線進行疑難排解](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。