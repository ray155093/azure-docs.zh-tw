### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式
* 新增名為 `sender.js` 的 JavaScript 檔案。

### <a name="add-the-relay-npm-package"></a>新增轉送 NPM 套件
* 在您的專案資料夾中從 Node 命令提示字元執行 `npm install hyco-ws`。

### <a name="write-some-code-to-send-messages"></a>撰寫一些程式碼來傳送訊息
1. 將下列 `constants` 新增至 `sender.js` 檔案開頭處。
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. 將下列轉送 `constants` 新增至 `sender.js` 以取得混合式連接的連接詳細資料。 將方括號中的預留位置以建立混合式連接時所取得的正確值取代。
   
   1. `const ns` - 轉送命名空間 (使用 FQDN - 例如 `{namespace}.servicebus.windows.net`)
   2. `const path` - 混合式連接的名稱
   3. `const keyrule` - SAS 金鑰的名稱
   4. `const key` - SAS 金鑰值
3. 將下列程式碼新增至 `sender.js` 檔案：
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    listener.js 看起來應該會像下面這樣：
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

