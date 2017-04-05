

## <a name="multi-and-single-instance-vms"></a>多重和單一執行個體 VM
對於在 Azure 上執行的許多客戶而言，在 VM 進行計劃性維護時可以排程是非常重要的，因為維護期間會發生大約 15 分鐘的停機。 您可以在佈建的 VM 接受計劃性維護時，使用可用性設定組協助控制。

有兩個可能的 VM 設定在 Azure 上執行。 VM 可能會設定為多重執行個體或單一執行個體。 如果 VM 在可用性設定組中，它們會隨後設定為多重執行個體。 請注意，即使單一 VM 也可以部署在可用性設定組中，所以才會被視為多重執行個體。 如果 VM 不在可用性設定組中，它們會隨後設定為單一執行個體。  如需可用性設定組的詳細資料，請參閱[管理 Windows 虛擬機器的可用性](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[管理 Linux 虛擬機器的可用性](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

單一執行個體和多重執行個體的 VM 計劃性維護更新會分別發生。 藉由將 VM 重新設定維單一執行個體 (如果它們是多重執行個體)，或設定為多重執行個體 (如果它們是單一執行個體)，您可以控制其 VM 收到計劃性維護的時間。 如需 Azure VM 計劃性維護的詳細資料，請參閱 [Azure Linux 虛擬機器的維護計劃](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或 [Azure Windows 虛擬機器的維護計劃](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="for-multi-instance-configuration"></a>對於多重執行個體組態
您可以藉由將 VM 從可用性設定組中移除，來選取計劃性維護影響部署在「可用性設定組」組態中之 VM 的時間。

1. 在計劃性維護 7 天前，會傳送電子郵件給您在多重執行個體組態中的 VM。 受影響多重執行個體 VM 的訂用帳戶識別碼和名稱會包含在電子郵件將電子郵件的本文中。
2. 在這 7 天內，您可以藉由從可用性設定組移除該區域中的多重執行個體 VM，以選擇執行個體的更新時間。 此組態變更會導致重新開機，因為虛擬機器正在從一部以維護為目標的實體主機，移至另一部不是以維護為目標的實體主機。
3. 您可以在 Azure 入口網站中從可用性設定組移除 VM。

   1. 在入口網站中，選取要從可用性設定組中移除的 VM。  

   2. 在 [設定] 之下，按一下 [可用性設定組]。

      ![可用性設定組選取](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. 在可用性設定組下拉式功能表中，選取 [不是可用性設定組的一部分]。

      ![從設定組移除](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. 按一下頂端的 [儲存]。 按一下 [是] 確認此動作會重新啟動 VM。

   >[!TIP]
   >您可以選取其中一個列出的可用性設定組，稍後再將 VM 重新設定為多個執行個體。

4. 從可用性設定組中移除的 VM 會移至單一執行個體的主機，而且不會在可用性設定組組態的計劃性維護期間更新。
5. 可用性設定組 VM 更新完成 (根據原始電子郵件中所述的排程) 之後，您應該將 VM 新增回其可用性設定組。 成為可用性設定組的一部分可將 VM 重新設定為多重執行個體，並導致重新啟動。 一般而言，跨整個 Azure 環境的所有多重執行個體更新完成之後，就輪到單一執行個體維護。

使用 Azure PowerShell 也可以達成從可用性設定組中移除 VM：

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>對於單一執行個體組態
您可以藉由將這些 VM 新增至可用性設定組，以選取計劃性維護影響單一執行個體設定中之 VM 的時間。

逐步說明

1. 在計劃性維護 7 天前，會傳送電子郵件給單一執行個體組態中的 VM。 受影響單一執行個體的訂用帳戶識別碼和名稱會包含在電子郵件將電子郵件的本文中。
2. 在這 7 天內，您可以藉由將單一執行個體 VM 新增至相同區域中的可用性設定組，以選擇執行個體重新啟動的時間。 此組態變更會導致重新開機，因為虛擬機器正在從一部以維護為目標的實體主機，移至另一部不是以維護為目標的實體主機。
3. 遵循此處的指示以使用 Azure 入口網站和 Azure PowerShell，將現有的 VM 新增至可用性設定組。 (請參閱遵循下列步驟的 Azure PowerShell 範例。)
4. 一旦這些 VM 重新設定為多重執行個體，隨即會從單一執行個體 VM 的計劃性維護排除。
5. 單一執行個體 VM 更新完成 (根據原始電子郵件中的排程) 後，您可以從可用性設定組中移除 VM，讓 VM 回復為單一執行個體。

使用 Azure PowerShell 也可以達成將 VM 新增至可用性設定組：

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
