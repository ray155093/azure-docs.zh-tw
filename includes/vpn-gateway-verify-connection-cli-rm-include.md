您可以使用下列 CLI 命令來確認連線是否成功。 設定和您自有值相符的值。 在範例中， -n 是指您已建立，而且想要測試之連線的名稱。

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

系統還在建立連線時，其連線狀態會顯示「連線中」。 連線建立好之後，狀態會變更為 [已連線]，而且您會看見輸入和輸出位元組。