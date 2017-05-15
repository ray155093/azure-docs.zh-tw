您可以使用透過企業解決方案產生的根憑證 (建議)，也可以產生自我簽署憑證。 如果您使用自我簽署憑證，請務必使用[建立點對站連線的自我簽署根憑證](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#rootcert)一文。 本文包含產生 P2S 相容憑證所需的特定設定。

建立根憑證之後，您要將公開憑證資料 (不是私密金鑰)，匯出為 Base-64 編碼的 X.509.cer 檔案。 接著將來自根憑證的公開憑證資料上傳至 Azure。

* **企業憑證：**如果您是使用企業解決方案，則可以使用現有的憑證鏈結。 取得您想要使用的根憑證 .cer 檔案。
* **自我簽署根憑證：**如果您未使用企業憑證解決方案，則必須建立自我簽署的根憑證。 根憑證必須包含特定值，才能進行點對站連線。 如需相關指示，請參閱下列文章：
  * 若要建立自我簽署根憑證，請參閱[建立點對站連線的自我簽署根憑證](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#rootcert)。
  * 若要匯出公開金鑰 (.cer 檔案)，請參閱[建立點對站連線的自我簽署根憑證](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer)。