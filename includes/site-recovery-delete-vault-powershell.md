## <a name="delete-a-recovery-services-vault-powershell"></a>刪除復原服務保存庫 (PowerShell)

1. 取得復原服務保存庫

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. 刪除保存庫

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> 儘可能小心使用上述命令，因為如果您誤刪了任何保存庫，將會失去所有資料。 這是一種永久動作，沒有任何方式能夠加以反轉。  


