在前述步驟中，您在資源群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，可以用刪除資源群組的方式將它們刪除。
 
1. 執行下列命令，確定資源群組不包含您想要儲存的任何資源：

  ```azurecli
  az group show --name myResourceGroup
  ```

2. 如果所顯示的資源全部都是您想要刪除的項目，請執行下列命令：
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
