您可以使用 [az network vpn-connection show](/cli/azure/network/vpn-connection#show) 命令來確認連線是否成功。 在範例中， '--name' 是指您想要測試之連線的名稱。 當系統在建立連線時，其連線狀態會顯示「連線中」。 連線建立好之後，狀態會變更為 [已連線]。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

