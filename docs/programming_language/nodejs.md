Node.js Learn
===
自主學習 Node.js 的觀念和指令後做的統整 & 學習筆記

[TOC]

## 安裝方式
- 官方網站會先分成LTS.Current兩個版本
  + `LTS`(Long-term support): Recommended for most users
  + `Current`: Latest features
- 安裝Node.js之後,也會自動安裝NPM
- 若想要安裝指定版本的Node.js,可以輸入以下指令
  + $ `brew install node@<specific version>`
    * 例: `brew install node@10`
  + 可參考[How to install specific NodeJS version](https://medium.com/@katopz/how-to-install-specific-nodejs-version-c6e1cec8aa11)
- 若想要安裝指定版本的NPM(Node Package Manager),可以輸入以下指令
  + 升級到Latest version: `npm install -g npm@latest`
  + 升級到Most recent release version: `npm install -g npm@next`
  + 可參考[Try the latest stable version of npm](https://docs.npmjs.com/try-the-latest-stable-version-of-npm)
- 如果想要在同一台電腦上面,切換Node的版本,可以安裝NVM(Node Version Manager)
  + **MacOS**
    * $ `brew install nvm`
  + **Linux**
    * $ `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash`
  + 接下來要到(~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc)的其中一個檔案,新增以下3行指令 **到該檔案的最下面**
    * `export NVM_DIR="$HOME/.nvm"`<br>
      `[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"`  # This loads nvm<br>
      `[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"`  # This loads nvm bash_completion<br>
  + 檢查是否正常安裝NVM
    * $ `command -v nvm`: 如果有成功安裝NVM的話,應該要回傳`nvm`
  + 檢查安裝的NVM版本
    * $ `nvm --version` 
  + 可參考[Node Version Manager](https://github.com/nvm-sh/nvm#troubleshooting-on-macos)

### Windows 系統
  + 連結: https://nodejs.org/en/download/
  + 安裝完成後,可以用CLI指令檢查 **Node** & **NPM** 的版本
    * $ `node --version` 
    * $ `npm --version`
### MacOS 系統
  + 連結: https://nodejs.org/en/download/package-manager/#macos
    * 指令: `brew install node`
  + 安裝完成後,可以用CLI指令檢查 **Node** & **NPM** 的版本
    * $ `node --version` 
    * $ `npm --version`
### Linux 系統 (以 Ubuntu 為例)
  + 連結: https://github.com/nodesource/distributions/blob/master/README.md
  + 指令: 
    * $ `sudo apt-get install -y nodejs`

---
## Node.js 核心觀念
### Introduction to Node.js
`先備知識`
> `One process`: 一個全域的Object,可以在任何地方被執行,並保有執行時的資料<br>
> `One thread`: single-thread,在一個`process`中只能執行一件事<br>
> `One event loop`: 一個事件迴圈,因為它使Node可以是非同步(asynchronous) & 非阻塞I/O(non-blocking I/O); 因為Node是single-thread的,透過callback.Promise.async/await能將工作分散給system kernel<br>
> `One JS Engine Instance`: 一個Javascript實例,用來執行Javascript的程式碼<br>
> `One Node.js Instance`: 一個Node實例,用來執行Node的程式碼<br>

- Node是一個免費.開源.跨平台的Javascript執行環境,讓開發者可以在瀏覽器以外也能使用Javascript
  + 也因此讓廣大的前端開發者們,可以加入後端開發的行列,並且不用因此需要多學一門程式語言 
- Node是基於Chrome瀏覽器的V8引擎來建立出的執行環境,因此能有非常好的效能
- Node是由開源社群來共同維護,並且能使用最新的ECMAScript標準
  + 因此不必等待所有用戶更新他們的瀏覽器,就可以選擇要使用的Node版本來達到來決定要使用哪個ECMAScript版本
  + 可參考官方文件[ECMAScript 2015 (ES6) and beyond](https://nodejs.org/en/docs/es6/) 
- Node應用程式在單個進程(single process)中運行,無需為每個請求建立新線程(thread),能處理數千個併發連接(concurrent connections),也不會因此而增加管理線程(thread)上的負擔
  + 同時Node也在其標準函式庫中提供了一組非同步I/O原語(asynchronous I/O primitives)來防止Javascript的程式碼被阻塞住,因此在Node中使用非阻塞式語法(non-blocking paradigms)來開發是正常且普遍的
  + 當Node執行會有I/O的操作(e.g. 從網路上讀取資料.存取資料庫or檔案系統)時,Node會自動在response回傳時才恢復操作,而不是無效地阻擋住線程和浪費CPU週期等待時間
- 受惠於npm的簡單結構,其促使Node生態系可以蓬勃地發展,目前在npm上已經有超過1,000,000個開源軟體&工具可供使用
- Node.js算是一個低階的平台,然而在社群(community)上有數千個函式庫與好用的框架建構於Node.js之上
- 範例程式碼
    ``` js
    const http = require('http');

    const hostname = '127.0.0.1';
    const port = process.env.PORT;

    const server = http.createServer((req, res) => {
      res.statusCode = 200
      res.setHeader('Content-Type', 'text/plain')
      res.end('Hello World!\n')
    });

    server.listen(port, hostname, () => {
      console.log(`Server running at http://${hostname}:${port}/`)
    });
    ```
  + 以上程式碼會建立一個新的http server並回傳,同時這個server也會監聽指定的port & host name
  + 當server準備就緒時,將執行callback function,在這時也會通知我們server正在運行中
  + 每當收到一個新的request時,都會呼叫該request event,並提供兩個物件(objects)
    * 請求物件(request): `http.IncomingMessage`
      * 它會提供request details 
    * 回應物件(response): `http.ServerResponse`
      * 它會用來將data回傳給呼叫request event的那方
  + 在這種狀況下, 我們會將狀態碼設定為200,以表示該request成功<br>
    => `res.statusCode = 200`
  + 並設定Content-Type標頭<br>
    => `res.setHeader('Content-Type', 'text/plain')`
  + 最後,會關閉response,並將內容作為參數添加到res.end()中<br>
    => `res.end('Hello World\n')`

### A brief history of Node.js
- Node.js最初是由Ryan Lienhart Dahl,於2010/05月初次發表,相比於Javascript(1995/12月)與網際網路的誕生(1989年)來說,在技術領域中,並不算是很長的時間,但目前看來Node會持續存在下去
  + ![](../assets/pics/nodejs/NodeJS%20logo.png)
  + ![](../assets/pics/nodejs/NodeJS作者%20Ryan%20Lienhart%20Dahl.jpg)
- 受惠於[瀏覽器的效能競爭戰](https://zh.wikipedia.org/wiki/%E6%B5%8F%E8%A7%88%E5%99%A8%E5%A4%A7%E6%88%98#Google_Chrome%E7%9A%84%E5%B4%9B%E8%B5%B7%E8%88%87Internet_Explorer%E7%9A%84%E8%A1%B0%E8%90%BD)(2003~2008年)後的結果,各家瀏覽器供應商爭相為用戶提供最佳性能,Javscript engine的運行效能也越來越好,而Node使用的Chrome瀏覽器的Javascript engine---V8,在性能上也有卓越的提升
- 另一個使Node快速崛起的關鍵因素是[Web2.0](https://zh.wikipedia.org/wiki/Web_2.0)的誕生,例如Flickr.Gmail等,讓Javascript開始被視為一個更為重要的程式語言
- Node恰巧是在正確的時間和正確的時間構建的,它為JavaScript服務器端開發引入了許多創新思維和方法,這些方法和方法已經為許多開發人員提供了幫助,這就是為什麼它開始流行的原因
- Node.js 簡史(Change log)
  + 西元2009年
    * [Node.js](https://nodejs.org/en/)誕生
    * 最初版[npm](https://www.npmjs.com/)問世
  + 西元2010年
    * [Express.js](https://expressjs.com/)框架 誕生
    * [socket.io](https://socket.io/) 誕生
  + 西元2011年
    * npm 發布1.0版本
    * 許多大型公司開始採用Node.js,例:LinkedIn.Uber等
  + 西元2012年
    * 越來越多人開始採用Node.js
  + 西元2013年
    * 第一個使用Node.js的大型部落格平台: [Ghost](https://ghost.org/)
    * [koa](https://koajs.com/)框架 誕生
  + 西元2014年
    * 重大事件: [io.js](https://socket.io/)誕生,是Node.js的主要Fork出來的專案,目的是為了加入Javascript的ES6語法支援並提升了效能 
  + 西元2015年
    * [Node.js基金會](https://openjsf.org/)誕生 (目前隸屬於OpenJS Foundation旗下的其中一個專案)
    * io.js合併回Node.js專案中
    * npm開始推出私有模組(private modules)
    * Node.js 4發布(直接跳過Node.js 1,2,3)
      * 可參考[Node_changelog](https://github.com/nodejs/node/blob/master/CHANGELOG.md)
  + 西元2016年
    * kik模組的left-pad事件爆發,引起開源社群的一陣騷動
    * [Yarn](https://classic.yarnpkg.com/en/)套件管理包工具 誕生
    * Node.js 6發布
  + 西元2017年
    * npm開始更加注重安全性
    * Node.js 8發布
    * [HTTP/2 核心模組](https://nodejs.org/api/http2.html) 發布
    * V8將Node加入它的測試套件中,使Node成為繼Chrome之後的Javascript engine正式目標
    * 達成30億次/週的下載流量紀錄
  + 西元2018年
    * Node.js 10發布
    * [ES modules.mjs](https://nodejs.org/api/esm.html)開始加入實驗性支援(experimental support)
    * Node.js 11發布
  + 西元2019年
    * Node.js 12發布  
    * Node.js 13發布
  + 西元2020年
    * Node.js 14發布
    * Node.js 15發布

### How to install Node.js?
- Node有很多種安裝方式,最常見的方式是透過套件管理包(package manager)下載,在這種情況下,每個作業系統有自己的安裝方式
  + 可參考 **安裝方式** 的章節
- NVM(Node Version Manager)是一種執行Node.js的流行方法
  + nvm讓我們可以輕鬆切換Node版本
  + 假如碰到錯誤時,可以安裝新版本以嘗試輕鬆地回滾(rollback)
  + nvm也是個有效的工具讓我們可以輕鬆地使用Node的舊版本來測試我們的程式碼
  + 可參考[Node Version Manager(nvm)](https://github.com/nvm-sh/nvm)
- 如果使用macOS,推薦使用[Homebrew](https://brew.sh/)來安裝Node
- 當安裝完Node之後,就可以使用`$ node xxx.js`在CLI中執行Node程式

### How much JavaScript do you need to know to use Node.js?
- 身為一個初學者,我們常常難以判斷要到什麼樣的程度才是對程式設計的能力足夠有自信的
- 當我們剛開始學習Javascript,我們可能會對Javascript的結束位置,以及Node的起始位置與結束位置感到很困惑
- 建議先理解Javascript的主要觀念後,在開始投入於Node的研究中
- 以下是Javascript目前的主要觀念
  + 詞彙結構(Lexcial Structure)
  + 運算式(Expressions)
  + 型別(Types)
  + 變數(Variables)
  + 函式(Functions)
  + `this` 關鍵字
  + 箭頭函式(Arrow Functions)
  + 迴圈(Loops)
  + 範圍(Scopes)
  + 陣列(Arrays)
  + 模板文字(Template Literals)
  + 分號(Semicolons)
  + 嚴謹模式(Strict Mode)
  + ECMAScript 6,7,8 (手稿語言規範)
- 如果能將以上的Javascript主要觀念都掌握到的話,無論是在瀏覽器還是在Node中,您都將成為一名熟練的JavaScript開發人員
- 以下是理解 **非同步程式設計** 的(asynchronous programming),這也是Node.js的基本觀念之一
  + [非同步程式設計](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous)與[回呼](https://developer.mozilla.org/zh-TW/docs/Glossary/Callback_function) (Asynchronous programming and callbacks)
  + [計時器](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals) (Timers)
  + [Promise物件](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise) (ES6以後開始加入的語法)
  + [Async & Await](https://developer.mozilla.org/zh-TW/docs/Learn/JavaScript/Asynchronous/Async_await)
  + [閉包](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Closures)(Closures)
  + [事件迴圈](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/EventLoop) (The Event Loop)

### Differences between Node.js and the Browser
- 瀏覽器與Node.js都是使用Javascript程式語言來開發的
- 建構出一個運行在瀏覽器的應用程式與建構出一個運行在Node的應用程式完全不同; 儘管都是使用Javascript程式語言來開發,卻仍存在一些關鍵差異,使體驗完全不同
- Node改變的是整個生態系統(ecosystem),因為它讓我們可以使用一種程式語言-Javascript,就可以完成我們所有的網頁開發工作(包含前端 & 後端),這是一個獨特的優勢地位
- 在瀏覽器中,我們花費大多數的時間在與[DOM](https://developer.mozilla.org/zh-TW/docs/Glossary/DOM)或是其他網頁平台的APIs(例: Cookies)。 當然,這些東西並不存在於Node之中。Node也不會有[Document物件](https://developer.mozilla.org/zh-TW/docs/Web/API/document)與[Window物件](https://developer.mozilla.org/zh-TW/docs/Web/API/Window)以及其他所有透過瀏覽器提供的物件們
- 在瀏覽器中,我們沒有那些Node透過其內建模組所提供的實用APIs(例: [文件系統訪問功能](https://nodejs.org/api/fs.html) (filesystem access functionality))
- 另一個比較大的差異是在Node中,我們控制的是 **環境**,除非您構建一個任何人都可以在任何地方部署的開源應用程式,否則我們通常會知道應該在哪個版本的Node上運行該應用程式; 但是在瀏覽器的環境中,我們無法選擇使用者會使用的瀏覽器,這點非常不方便
  + 這也意味著我們可以使用該Node版本可支援的所有ECMAScript 6-7-8-9的現代化Javascript語法
- 由於Javascript的變化如此之快,但是瀏覽器與使用者所使用的瀏覽器並沒有這麼迅速的升級,因此我們不得不使用較舊的JavaScript/ECMAScript版本
  + 這時我們可以使用[Babel](https://babeljs.io/)來將程式碼轉換成可與ES5可相容的語法,然後再交給瀏覽器
  + 然而在Node中,我們並不需要這樣做
- 還有一個重大的差異是在Node中,我們使用的是CommonJS模組系統; 但是在瀏覽器中,我們會依照ECMAScript的模組標準來實作Javascipt語法
  + 實際上,這代表我們會分別使用
    * require() => 在Node.js中
    * import => 在瀏覽器中

### The V8 JavaScript Engine
- [V8](https://v8.dev/)是用來支持Google Chrome瀏覽器的Javascript engine。當我們使用Chrome瀏覽器時,它需要我們的Javascript程式碼並執行它們 
- V8負責提供Javascript執行時所需要的執行環境(runtime)。`DOM`和其他的Web APIs則由瀏覽器負責提供
- Javascript engine是能獨立運作的,並不一定需要跟隨著託管(hosted)它的瀏覽器,這也促使Node的興起
- 西元2009年時,V8被選為用來作為Node.js的Javascript engine,並且隨者Node的爆炸性成長,V8成為了現在為大量使用Javascript編寫伺服器端程式碼(server-side code)的engine
- Node生態系非常龐大,受惠於此,V8也支援桌面應用程式(例: [Electron](https://www.electronjs.org/))
- 其他瀏覽器使用的Javascript engine
  + Firefox: [SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey)
  + Safari: [JavaScriptCore(也被稱為Nitro)](https://developer.apple.com/documentation/javascriptcore)
  + Microsoft Edge: 最初是基於[ChakraCore](https://github.com/Microsoft/ChakraCore)開發的,後來轉由基於[Chromium](https://www.chromium.org/) & V8 engine來重構
    * 可參考[Download the new Microsoft Edge based on Chromium](https://support.microsoft.com/en-us/microsoft-edge/download-the-new-microsoft-edge-based-on-chromium-0f4a3dd7-55df-60f5-739f-00010dba52cf)  
- V8引擎是由C++程式語言所編寫。它是可攜帶式的(portable),可提供跨平台支援(on macOS &Windows & Linux ...等等)
  + V8也與其他的Javascript engine一樣,V8也在持續改進中,也加速了Web和Node生態系的快速發展; 多年來,在網路上有很多關於性能調校的競賽,身為開發者和使用者的我們也受惠於此,讓我們能擁有更快.效能更好的機器(machines)可以使用
- Javascript通常被認為是一種直譯式語言(interpreted language),但是在現代化的Javascript engine中,已不再只是直譯Javascript,它們也編譯Javascript
  + 從西元2009年開始,當SpiderMonkey JavaScript compiler被加入到Firefox瀏覽器 v3.5之中以後,每個人都開始追隨這種做法
  + Javascript會被V8 engine進行內部即時編譯(just-in-time compilation, JIT)以加快執行速度; 這可能是一種違反直覺的方式,但是從2004年引入Google Maps以來,Javascript已經從一種通常用來執行幾十行程式碼的小型應用程式的程式語言,逐漸發展成可以在瀏覽器中執行成千上萬行的大型.完整的應用程式的程式語言
  + 演變至今,我們的應用程式已經可以在瀏覽器持續執行數小時,而這也不僅限於單純的表單驗證規則(a few form validation rules)或是簡單的程式碼(simple scripts)
  + 在現代的新世界中,"編譯"Javascript是非常有意義的,因為雖然編寫Javascript仍然需要花費很多時間,但是一旦開發完成後,它將比起純直譯程式碼來的擁有更好的效能

### Run Node.js scripts from the command line
- 當我們安裝好Node.js後,通常我們會用可在全域執行的`node`指令,接著傳遞要執行的檔名作為參數到CLI上
  + 假設我們的主要Node應用程式的檔名叫做`app.js` 
  + 例: $ `node app.js`
  + 提醒: 要在包含`app.js`的檔案路徑下執行該指令才行

### How to exit from a Node.js program?
- 有多種方法可以終止Node應用程式
  + CLI: $ `ctrl-C`
  + 程式碼: `process.exit()`
  + 程式碼: `process.on('SIGTERM', callback function)`
- Node的核心模組-[Process](https://nodejs.org/api/process.html)提供了一個簡便的方法,讓我們可以從一個Node應用程式退出,可執行以下的command
  + $ `process.exit()`
  + 當Node執行到這邊時,該進程(process)就會被立即強制終止。這代表任何處於`pending狀態`的`callback function`與任何網路請求(network request)仍然會被傳送出去; 然而任何訪問文件系統(`filesystem`)或是進程(`process`),例如: [process.stdout()](https://nodejs.org/api/process.html#process_process_stdout) 或是 [process.stderr](https://nodejs.org/api/process.html#process_process_stderr)都將立刻被不正常地終止(ungracefully terminated right away.)
  + 如果這樣是我們想要的結果,那我們可以傳遞一個整數,以此作為像作業系統發出的退出碼(exit code)
    * 例: $ `process.exit(1)`
    * 補充: 退出碼(exit code)
      * 預設值: `0` (代表成功) 
      * 當`exit code` <= 0 時,表示指令執行成功
      * 當`exit code` > 0 時,表示指令執行失敗
    * 不同的退出碼具有不同的意義,我們可以利用這些退出碼來讓我們系統中的"程式與程式"之間能互相溝通
    * 可參考[Node核心模組-Process的 Exit codes 章節](https://nodejs.org/api/process.html#process_exit_codes)
  + 我們也可以設定process.exitCode屬性來指定當Node應用程式退出時,要回傳什麼退出碼
    * 使用process.exitCode的情境,需滿足以下條件
      * 當使用process.exit([code]) -> 且未指定退出碼時
      * 進程(process)正常地終止(exits gracefully)時
    * 例: $ `process.exitCode = 1`
    * 當Node應用程式終止時,將會return該退出碼,在完成處理所有進程(process)後,Node應用程式將會正常地退出
    * 可以利用`process.exit([code])`來推翻掉(override)先前的`process.exitCode`的設定值
  + 另一種常見的情況是當我們啟動一個伺服器,例如: HTTP server
      ``` js
      const express = require('express')
      const app = express()

      app.get('/', (req, res) => {
        res.send('Hi!')
      })

      app.listen(3000, () => console.log('Server ready'))
      ```
    * 以上的程式永遠不會終止! 如果我們呼叫`process.exit()`,這時正在處理的pending與running request都將被終止,而這不是一個好的做法
    * 這時候比較好的解決方法是對該command發出`SIGTERM`信號(signal),並使用process模組的signal handler來進行處理
      * 可參考[Signal events](https://nodejs.org/api/process.html#process_signal_events)
      ``` js
      const express = require('express')
      const app = express()
      app.get('/', (req, res) => {
        res.send('Hi!')
      })

      const server = app.listen(3000, () => console.log('Server ready'))

      process.on('SIGTERM', () => {
        server.close(() => {
          console.log('Process terminated')
        })
      })
      ``` 
    > Q: 什麼是信號(signals)?<br>
      A: 信號是[POSIX](https://zh.wikipedia.org/wiki/%E5%8F%AF%E7%A7%BB%E6%A4%8D%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E6%8E%A5%E5%8F%A3)的相互通訊系統: 當通知(notification)被發送到一個進程(process),以便將發生的事件(event)通知給進程(process)
    * `SIGKILL`是告訴進程(process)立即中止的信號
    * `SIGTERM`是告訴進程(process)正常終止的信號。這是從例如[upstart](http://upstart.ubuntu.com/) & [supervisored](http://supervisord.org/)等其他流程管理工具發出的信號
    * 我們也可以在應用程式中的另一個函式中,發出這個信號
      * $ `process.kill(process.pid, 'SIGTERM')`
      * 也可以從其他執行中的Node應用程式or其他在我們的作業系統中正在執行的應用程式,來得知我們想要終止的應用程式的ID(process ID, pid)

### How to read environment variables from Node.js?
- `process`核心模組提供了`env`屬性,`process.env`屬性會託管當啟動Node進程(process)的時候的所有Node環境變數
- 以下是一個預設在`development`環境下,存取`NODE_ENV`這個環境變數的範例
  + 提醒: 因為它是Node的核心模組,所以`process`模組不需要事先匯入(`require()`),可以直接開始使用
    ``` js
    process.env.NODE_ENV // "development"
    ```
- 當在腳本(script)執行之前,可以先將該屬性設定為`production`,來告訴Node這是一個`production`的生產環境
    ``` js
    process.env.NODE_ENV // "production"
    ```
- 當然我們也可以利用上述的方式再設定我們需要的自定義環境變數

### How to use the Node.js REPL?
- 我們可以利用$ `node`指令來執行我們寫好的Node腳本(script)
  + 例: $ `node script.js`
- 如果我們省略要執行的腳本名稱這個參數的話,就會進入`REPL模式`
  > 補充: `REPL`又被稱為Read Evaluate Print Loop(讀取-求值-輸出循環的互動式介面, REPL),是一種程式語言的環境(主要是在CLI console畫面中)。他會使用單個表達式作為使用者輸入,並在執行後將結果回傳到CLI console畫面上<br>
  > 補充: `REPL模式`因為是互動式介面,所以支援可利用`Tab`鍵做自動補全(autocomplete)的功能,它會嘗試自動完成所寫的內容,以匹配已定義的變量或預定義的變量
  + node `REPL模式`範例情境
    ``` console
    ❯ node
    >
    ```
    * 該指令會停留在閒置模式(idle mode),並等待我們輸入些什麼東西。更精確地說是`REPL模式`正在等待我們輸入一些Javascript的程式碼
  + 我們從簡單的範例開始看看
    ``` console
    > console.log('test')
    test
    undefined
    >
    ```
    * 第一個值`test`是要告訴CLI console要打印(print)出什麼值,接著我們收到`undefined`,它是執行中(running)的`console.log()`
    * 接著我們可以開始輸入一行新的Javascript的程式碼
- 探索Javascript物件(Objects)
  + 我們可以試著輸入Javascript的類別(Class),像是`Number`,並在後面加上一個`.` ,再接著按下`Tab`按鈕來自動補全
  + ![node REPL模式-探索Number類別](../assets/pics/nodejs/node%20REPL模式-探索Number類別.png)
- 探索Javascript的全域物件(global)
  + 我們可以試著輸入Javascript的類別(Class),像是`global`,並在後面加上一個`.` ,再接著按下`Tab`按鈕來自動補全
  + ![node REPL模式-探索全域物件(global)](../assets/pics/nodejs/node%20REPL模式-探索全域物件(global).png)
- 探索特殊變數(`_`)
  + 如果在某些程式碼後面輸入`_`,則將回傳上一個操作的結果
- 探索點命令(Dot commands)
  + REPL模式有一些特別的指令,這些指令都是由`.`為開頭的
  + 舉例來說
    * `.help`: 顯示所有的點命令(dot commands)的幫助(help)
    * `.editor`: 啟用編輯模式,可以輕鬆地開始寫多行Javascript程式碼。一旦進入這個模式,我們將要使用`ctrl-D`的組合鍵來執行我們寫的Javascript多行程式碼
    * `.break`: 當輸入多行表達式(multi-line expression)時,輸入`.break`指令將終止進一步的輸入(abort further input)。與`ctrl-C`組合鍵的功能是一樣的
    * `.clear`: 將`REPL`模式的內文重新設定(reset)為一個空物件,並清除當前正在輸入的任何多行表達式
    * `.load`: 讀取當前工作目錄路徑下的Javascript檔案
    * `.save`: 將我們在`REPL連線`(session)中的所有對指定檔案的輸入儲存起來
    * `.exit`: 退出`REPL模式`。與連續按下`ctrl-C`組合鍵 **兩次** 的功能是一樣的
  + 其實`REPL模式`會知道什麼時候要輸入多行表達式,而不需要呼叫`.editor`
    * 以下是範例程式碼 
        ``` js
          [1, 2, 3].forEach(num => {
        ```
    * 這時候當我們按下`enter`鍵時,`REPL模式`會到新的下一行,並以`...`作為開頭,表示我們現在可以繼續在該區塊(block)工作
      * 情境說明
        ``` js
        ... console.log(num)
        ... })
        ``` 
    * 如果我們這時候在一行的句尾加上`.break`時,該多行表達式的模式將會停止並不會被執行

### Node.js, accept arguments from the command line
- 我們可以將不限制數量的參數(arguments)傳遞給Node應用程式,參數的形式可以使用以下2種形式
  + 獨立的參數值(standalone)
    * $ `node app.js joe`
  + 鍵->值形式的參數值(key and value)
    * $ `node app.js name=joe`
- 這也將改變我們在Node應用程式的程式碼中,如何得到此值的方式,我們可以透過Node內建的核心模組`process`來得到此值,該模組的[process.argv](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_argv)屬性會公開(exposes)出一個陣列(array),其中包含當啟動Node應用程式的進程的時候,整個命令列(command-line)要傳遞的所有參數
  + 第1個元素是node指令的完整路徑(= [process.execPath](https://nodejs.org/api/process.html#process_process_execpath))
  + 第2個元素會是正在被執行的Javascript檔案的路徑位置
  + 剩下的元素就是任何其他的命令列參數(command-line arguments)
- 範例程式碼
  + 以下的範例程式碼會利用一個迴圈來遍歷(iterate over)所有的參數們(也包括node指令的完整路徑與正在被執行的Javascript檔案的路徑位置)
    ``` js
    process.argv.forEach((val, index) => {
      console.log(`${index}: ${val}`)
    })
    ```
- 我們可以透過建立一個新的陣列並切片(`array.slice()`方法)來獲得額外的參數(不包含前兩個參數)
    ``` js
    const args = process.argv.slice(2)
    ```
- 如果我們的參數沒有索引名稱的話(without index name)
  + 例: $ `node app.js joe`
      ``` js
      const args = process.argv.slice(2)
      console.log(args[0])
      ```
    * 我們可以透過以上的範例程式碼來存取命令列參數(command-line arguments)
- 另一種情況是,我們會以鍵值(key-value)形式來傳遞命令列參數
  + 例: $ `node app.js name=joe`
    ``` js
    const args = require('minimist')(process.argv.slice(2))
    args['name'] //joe
    ```
    * 以上述範例程式碼為例,`args[0]`是`name=joe`,這種情況我們就會需要來解析(parse)它,最佳的做法是,我們可以利用npm上面的[minimist](https://www.npmjs.com/package/minimist)套件來處理這些 **鍵值形式的參數**
  + 這次我們就要在每個參數的鍵(key)之前使用雙破折號(double dashes)
    * 例: $ `node app.js --name=joe`

### Output to the command line using Node.js
> npm的 chalk 套件---可設定終端機輸出文字的樣式與顏色<br>
> npm的[progress](https://www.npmjs.com/package/progress)套件---可以在`CLI console`畫面上創造進度條的套件<br>

- 利用[console](https://nodejs.org/api/console.html#console_console)核心模組的基本輸出(basic output)
  + Node有一個`console`核心模組,該模組提供了許多與`CLI`有用的互動方法
  + 它基本上跟我們在瀏覽器上看到的`console`物件是一樣的
  + 最基本與最有用的方法是[console.log([data][, ...args])](https://nodejs.org/api/console.html#console_console_log_data_args),這個方法會打印出(print)我們傳遞給console的字串
    * 如果我們傳遞物件,該方法會自動幫我們轉換成字串
    * 我們也可以傳遞多個變數給`console.log()`
    * 範例程式碼
      ``` js
      const x = 'x'
      const y = 'y'
      console.log(x, y)
      ```
      * 以上的程式碼,Node會將兩個變數都打印出來(print)
    * 我們也可以傳遞變數給模板字串
    * 範例程式碼
    ``` js
    console.log('My %s has %d years', 'cat', 2)
    ```
      * `%s`: 將變數格式化為字串
      * `%d`: 將變數格式化為數字
      * `%i`: 將變數格式化為其整數部分
      * `%o`: 將變數格式化為物件
    * 範例程式碼
      ``` js
      console.log('%o', Number)
      ```
- 清除後台(console)-[console.clear()](https://nodejs.org/api/console.html#console_console_clear)
  + 範例程式碼
    ``` js
    console.clear()
    ```
  + 該方法會將後台清空(註: 該方法的行為也會取決於我們是使用哪種後台)
- 計算元素數量-[console.count([label])](https://nodejs.org/api/console.html#console_console_count_label)
  + `console.count()`是一個方便的方法
  + 範例程式碼
    ``` js
    const x = 1
    const y = 2
    const z = 3
    console.count(
      'The value of x is ' + x + 
      ' and has been checked .. how many times?'
    )
    console.count(
      'The value of x is ' + x + 
      ' and has been checked .. how many times?'
    )
    console.count(
      'The value of y is ' + y + 
      ' and has been checked .. how many times?'
    )
    ```
    * 以上的程式碼會計算該字串被打印出(print)幾次了,並將其次打印在旁邊
  + 範例程式碼
    * 我們也可以只計算蘋果和橘子的數量
      ``` js
      const oranges = ['orange', 'orange']
      const apples = ['just one apple']
      oranges.forEach(fruit => {
        console.count(fruit)
      })
      apples.forEach(fruit => {
        console.count(fruit)
      })
      ```
- 打印出堆棧追蹤(print stack trace)-[console.trace([message][, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/console.html#console_console_trace_message_args)
  + 在某些情況下,打印出函數的堆棧調用情況是很有用的,這也許就能回答我們的一個問題---我們該如何到達程式碼的其中一個部分?
  + 我們可以利用`console.trace()`方法來完成
  + 範例程式碼
      ``` js
      const function2 = () => console.trace()
      const function1 = () => function2()
      function1()
      ```
      * 以上的範例程式碼會打印出堆棧追蹤(stack trace)
      * 如果我們在Node的`REPL模式`中嘗試以上的範例程式碼,會得到以下的回傳資訊
        ``` console
        Trace
          at function2 (repl:1:33)
          at function1 (repl:1:25)
          at repl:1:1
          at ContextifyScript.Script.runInThisContext (vm.js:44:33)
          at REPLServer.defaultEval (repl.js:239:29)
          at bound (domain.js:301:14)
          at REPLServer.runBound [as eval] (domain.js:314:12)
          at REPLServer.onLine (repl.js:440:10)
          at emitOne (events.js:120:20)
          at REPLServer.emit (events.js:210:7)
        ```
- 計算執行Node程式碼所花費的時間-[console.time([label])](https://nodejs.org/dist/latest-v15.x/docs/api/console.html#console_console_time_label) & [console.timeEnd([label])](https://nodejs.org/dist/latest-v15.x/docs/api/console.html#console_console_timeend_label)
  + 我們可以使用`console.time()` & `console.timeEnd()`
  + 範例程式碼
    ``` js
    const doSomething = () => console.log('test')
    const measureDoingSomething = () => {
      console.time('doSomething()')
      //do something, and measure the time it takes
      doSomething()
      console.timeEnd('doSomething()')
    }
    measureDoingSomething()
    ```
- 標準輸出 & 標準錯誤-[console.log([data][, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/console.html#console_console_log_data_args) & [console.error([data][, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/console.html#console_console_error_data_args)
  + 如我們所見,`console.log()`非常適合在後台(Console)打印出訊息,這也就是所謂的標準輸出(standard output, `stdout`)
  + 反之,`console.error()`則會將訊息打印到標準錯誤(standard error, `stderr`)流(stream)上面
    + `stderr`流不會出現在後台(Console)上,但是它會出現在錯誤日誌上(error log)
- 為文字輸出上色-[跳脫序列(escape sequences)](https://gist.github.com/iamnewton/8754917) & npm的 chalk package
  + 我們可以利用跳脫序列(escape sequence)將文字輸出上色
  + 跳脫序列是一組字符代表一種顏色
  + 範例程式碼
      ``` js
      console.log('\x1b[33m%s\x1b[0m', 'hi!')
      ```
    * 在`CLI`上進入Node的`REPL模式`中,執行以上的程式碼會看到`hi`變成黃色的字體
    * ![利用Node REPL模式下的跳脫序列來為文字輸出上色](../assets/pics/nodejs/利用Node%20REPL模式下的跳脫序列來為文字輸出上色.png)
    * 然而這樣做其實是一種低階(low-level)方法,最簡單的做法是利用套件(library)來將文字輸出上色
  + 我們可以利用npm上面的`chalk`套件,該套件不僅可以為文字輸出上色,也可以將文字輸出的字體變成 **粗體** or *斜體* or 帶有下劃線
    * 我們可以透過`CLI`指令來安裝`chalk`這個套件,安裝完後就可以立即使用它
      * $ `npm install chalk`
    * 範例程式碼
      ``` js
      const chalk = require('chalk')
      console.log(chalk.yellow('hi!'))
      ```
      * 從上面的範例程式碼可以看出我們可以使用`chalk.yellow()`這個方法會比起跳脫序列(escape sequences)來得更好記與更好閱讀
    * 更多相關功能可以參考 npm 的 chalk 套件
- 建立進度條-npm的[progress](https://www.npmjs.com/package/progress) package
  + `progress`是一個非常棒的套件在CLI console畫面上創造 **進度條** 的套件
  + 可以先利用npm來安裝`progress`這個套件
    * $ `npm install progress`
  + 範例程式碼
    ``` js
    const ProgressBar = require('progress')

    const bar = new ProgressBar(':bar', { total: 10 })
    const timer = setInterval(() => {
      bar.tick()
      if (bar.complete) {
        clearInterval(timer)
      }
    }, 100)
    ```
    * 以上的範例程式碼會建立一個含有10個步驟(steps)的進度條(progress bar),每100毫秒就會執行一次
    * 當進度條完成時,就會清除這個間隔(clear the interval)

### Accept input from the command line in Node.js
> npm的[readline-sync](https://www.npmjs.com/package/readline-sync)套件---提供一個能透過console(TTY)與使用者進行對話的互動式執行地同步讀取行(synchronous readline for interactively running)<br>
> npm的[inquirer](https://www.npmjs.com/package/inquirer)---收集了常見的`CLI`指令<br>

- 如何讓Node應用程式的`CLI console`成為互動性(interactive)的控制台
- 自從Node`v7.0.0`版本之後,Node提供了`readline`這個內建核心模組來確切地執行以下這件事情
  + 從一個可讀取的流(readable stream),例如: `process.stdin`流(stream)獲得輸入(get input)
  + 補充: `process.stdin`是在執行Node.js應用程式期間時,終端機輸入的流,一次僅一行
  + 範例程式碼
    ``` js
    const readline = require('readline').createInterface({
      input: process.stdin,
      output: process.stdout
    })

    readline.question(`What's your name?`, name => {
      console.log(`Hi ${name}!`)
      readline.close()
    })
    ```
    * 以上片段(piece)的程式碼會詢問使用者名稱,一旦使用者輸入文字並按下`enter`鍵後,我們就會發送一個問候(greeting)
    * 說明: [readline.question()](https://nodejs.org/api/readline.html#readline_rl_question_query_callback)方法顯示第一個參數(例: 一個問句),並等待使用者輸入。一旦使用者輸入文字並按下`enter`鍵後,就會呼叫該方法的第二個參數---回呼函式(callback function)
    * 在上述的範例程式碼中,我們的回呼函式會關閉讀取行介面(readline interface)---([readline.close()](https://nodejs.org/api/readline.html#readline_rl_close))
  + `readline`模組提供了許多其他的方法,接下來將會在套件的名稱上設定超連結來讓我們可以去查看該套件的文件
  + 如果我們要求輸入密碼,我們會希望最好不要以明碼顯示回傳的密碼,而是使用`*`符號來替換顯示使用者輸入的密碼
    * 這時最簡單的方式是使用就API而言非常類似的,npm上的[readline-sync](https://www.npmjs.com/package/readline-sync)套件,並立即對其進行處理
  + npm的[inquirer](https://www.npmjs.com/package/inquirer)套件提供了一個更完整且更抽象的解決方案
    * 我們需要先透過npm安裝`inquirer`套件
      * $ `npm install inquirer`
  + 情境說明
    ``` js
    const inquirer = require('inquirer')

    var questions = [
      {
        type: 'input',
        name: 'name',
        message: "What's your name?"
      }
    ]

    inquirer.prompt(questions).then(answers => {
      console.log(`Hi ${answers['name']}!`)
    })
    ```
    * `inquirer`這個套件讓我們可以做許多事情像是詢問選擇題(multiple choices),提供單選按鈕(radio button),確認(confirmation),...等等
    * 值得一提的是所有的替代方案(alternatives),尤其是那些Node提供的內建替代方案還不錯。但如果我們想要將`CLI`互動式輸入提供到另一個更高的水平上時,`inquirer.js`是一個最理想(optimal)的選擇

### Expose functionality from a Node.js file using exports
- Node擁有內建的模組系統,並能透過匯入(import)來使用由其它Node.js的檔案公開(exposed)出來的功能
- 假如我們想要匯入某些我們想使用的東西
  + 範例程式碼
    ``` js
    const library = require('./library')
    ```
    * 可以利用以上的程式碼來匯入存在於(resides)當前檔案目錄下的`library.js`所公開(exposed)出來的功能
    * 在`library.js`檔案的程式碼中的最下面,需要事先公開(exposed)=> (使用`module.exports`物件的API)出來該檔案的功能,才能在其他需要使用時,直接匯入`library.js`就可以使用
      * 因為在預設情況下,所有在`library.js`中定義的物件or變數都是私有的(private)並且沒有對外(to outer world)公開(exposed)
      * 而這也就是[CommonJS modules](https://nodejs.org/api/modules.html#modules_modules_commonjs_modules)模組提供的[module.exports](https://nodejs.org/api/modules.html#modules_module_exports) API,所允許我們這麼做的
- 當我們指派(assign)一個物件或是一個函式成為`exports`的新屬性時,而這就是我們要公開(exposed)的東西,因此我們就可以在我們的Node應用程式中的其它部分中或甚至是在其它的Node應用程式中也可以使用
  + 我們可以透過以下2種方式來操作
  + 方法一: 指派(assign)一個物件給`module.exports`,也就是指派給由Node的`CommonJS modules`核心模組所提供的物件,如此就能將我們的檔案僅對外匯出(export)那個物件
    * 範例程式碼
      ``` js
      const car = {
        brand: 'Ford',
        model: 'Fiesta'
      }

      module.exports = car

      //..in the other file

      const car = require('./car')
      ```
  + 方法二: 將想要匯出(exported)物件新增(add)為[exports](https://nodejs.org/api/modules.html#modules_exports)的屬性(property),這個方法讓我們能夠匯出多個物件(objects),函式(functions),資料(data)
    * 範例程式碼
      ``` js
      const car = {
        brand: 'Ford',
        model: 'Fiesta'
      }

      exports.car = car
      ```
    * 或是也可以直接這樣匯出(export)
    * 範例程式碼
      ``` js
      exports.car = {
        brand: 'Ford',
        model: 'Fiesta'
      }
      ```
    * 接著,我們在另一個檔案中就可以匯入(import)並引用(referencing)這個屬性
    * 我們可以透過以下2種方式來引用它
      * 方法一:
        ``` js
        const items = require('./items')
        items.car
        ```
      * 方法二:
        ``` js
        const car = require('./items').car
        ```
- [module.exports](https://nodejs.org/api/modules.html#modules_module_exports)與[exports](https://nodejs.org/api/modules.html#modules_exports)之間有什麼區別呢?
  + `module.exports`會公開(exposes)它指向(points to)的對象(object)
  + `exports`會公開(exposes)它所指向(points to)的對象(object)的屬性(properties)

### An introduction to the npm package manager
> [npm install](https://docs.npmjs.com/cli/v7/commands/npm-install) - Install a package<br>

- `npm`介紹
  + [npm](https://www.npmjs.com/)是Node標準的套件管理工具(standard package manager)
  + 在2017年1月,根據報告顯示`npm registry`列表上已有超過350,000個套件(packages),也使其成為地球上單一語言程式碼儲存庫,並且我們可以確定在`npm`上有幾乎所有的東西
  + `npm`起初是用來下載和管理Node套件(package)的相依性(dependencies)的方式,但它此後已經成為前端(frontend) Javascript的工具
  + `npm`幫助我們完成很多事情,另外`Yarn`是一個`npm`的替代方案(alternative),可以到[Yarn](https://classic.yarnpkg.com/en/)的官方網站去瞧瞧
- 下載
  + `npm`會管理我們專案的套件相依性(dependencies)
  + 安裝所有該專案的套件與其相依的套件(all dependencies)
    * 如果我們的專案有一個`package.json`檔案的話,只要在終端機執行以下指令
      * $ `npm install`
      * 如果我們的專案中不存在`node_modules/`這個資料夾的話,該指令就會安裝所有這個專案會需要的套件,並將這些東西放在`node_modules/`的資料夾
  + 安裝單一個套件(package)
    * 我們只想安裝指定的套件的話,可以執行以下指令
      * $ `npm install <package-name>`
      * 通常,我們會看到關於這個指令的更多選項,例如以下兩種選項
        * [--save](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#dependencies): 安裝該套件並新增到`package.json`的`dependencies`鍵中
        * [--save-dev](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#devdependencies): 安裝該套件並新增到`package.json`的`devDependencies`鍵中
      * 以上兩種安裝選項的區別主要是`devDependencies`通常是開發工具(development tools),像是測試套件(testing library); 而`dependencies`在生產環境中與應用程式本身綁定在一起
  + 更新套件(Updating packages)
    * 使用`npm`也讓更新套件更容易,可以透過執行以下指令
      * `npm update`
    * `npm`將檢查所有套件是否有能滿足我們專案中的`package.json`檔案版本限制(constraints)的最新版本
    * 我們也可以指定要更新單一個套件
      * $ `npm update <package-name>`
- 套件的版本控制
  + 除了單純的下載之外,`npm`也能管理套件的版本,因此我們可以在我們專案中的`package.json`檔案中,對套件指定任何一個特定的版本,或是要求安裝的版本需要高於 or 低於我們所需要的特定版本
  + 很多時候會發現一個函式庫(library)僅與另一個函式庫的主要版本(major release)所相容(compatible); 或是最新發布的函式庫的版本之中有`bug`,仍然未修復,並造成了一些問題(issue)
  + 指定一個明確的函式庫(library)版本也可以幫助每個人都使用確定相同的套件(package)版本,因此整個團隊都使用相同的版本來運行程式,直到`package.json`檔案被更新為止
  + 在以上的情況中,套件的版本管理都非常有用,`npm`也遵循了語意化版本控制的標準(semantic versioning (semver) standard)
    * [npm Docs---semver](https://docs.npmjs.com/cli/v7/using-npm/semver): `npm`的語意化版本控制工具
    * [Semantic Versioning 2.0.0](https://semver.org/): 語意化版本的官方網站
- 執行任務(Running Tasks)
  + `package.json`支援透過指定格式的命令來執行Node應用程式,可以透過以下指令
    * $ `npm run <task-name>`
    * 範例程式碼
        ``` js
        {
          "scripts": {
            "start-dev": "node lib/server-development",
            "start": "node lib/server-production"
          },
        }
        ```
    * 以下範例程式碼是利用此功能執行 **Webpack** 常見的方式
        ``` js
        {
          "scripts": {
            "watch": "webpack --watch --progress --colors --config webpack.conf.js",
            "dev": "webpack --progress --colors --config webpack.conf.js",
            "prod": "NODE_ENV=production webpack -p --config webpack.conf.js",
          },
        }
        ```
  + 因此,我們不用再去記那些容易忘記or輸入錯誤的長指令,而可以像是如下簡潔的方式來執行Node應用程式
    * $ `npm run watch`
    * $ `npm run dev`
    * $ `npm run prod`
    
### Where does npm install the packages?
- 當我們利用`npm`套件管理工具來安裝一個套件時,可以選擇執行以下2種類型的安裝方式
  + 本地端安裝(a local install)
  + 全域安裝(a global install)
- 在預設情況下,我們可以輸入以下指令來安裝套件(=> 本地端安裝)
  + 例: $ `npm install lodash`
  + 這樣`npm`就會在當前專案的目錄裡面的`node_modules/`資料夾中安裝該套件
  + 這時,`npm`也會新增`lodash`這個項目(entry)到當前專案目錄裡的`package.json`檔案中的`dependencies`屬性中
- 我們也可以使用`-g`選項來執行全域安裝
  + 例: $ `npm install -g lodash`
  + 這時,`npm`就不會在當前的資料夾安裝該套件,而是會安裝到一個全域的位置上(global location)
  + 具體來說,全域安裝的路徑位置在哪裡呢? A: 會依據本地端電腦的作業系統不同而定
    * 在Windows作業系統中會是`C:\Users\YOU\AppData\Roaming\npm\node_modules`
    * 在macOS & Linux作業系統中會是`/usr/local/lib/node_modules`
      * ![npm 全域安裝的路徑位置](../assets/pics/nodejs/npm%20全域安裝的路徑位置.png)
  + 然而,如果我們使用`nvm`來管理Node版本的話,全域安裝的路徑位置就會有所不同
  + 以我使用`nvm`的情況來說,我的套件會被全域安裝在`/Users/joe/.nvm/versions/node/v8.9.0/lib/node_modules`

### How to use or execute a package installed using npm?
> npm的[cowsay](https://www.npmjs.com/package/cowsay)套件---是一個由`Perl`語言所開發的套件,能提供一個終端機介面的程式,並以母牛(cow)的方式說話<br>

- 當我們透過`npm`安裝套件到`node_modules/`資料夾時,或是全域安裝時,我們可以透過`require('<package name>')`的語法來引用該套件(package)
  + 假設我們安裝了`lodash`這個Javascript世界中流行且實用的函式庫(library)
    * 例: $ `npm install lodash`
  + 這樣`npm`將會把`lodash`函式庫安裝到當前專案目錄中的`node_modules/`資料夾中
  + 當我們想要使用`lodash`函式庫在我們的程式碼時,可以利用以下的指令
    * 例: $ `const _ = require('lodash')`
    * 以這個範例為例 **會將可執行的檔案(executable file)放在`node_modules/.bin/`資料夾下面**
- 以上的觀念,我們可以利用`npm`上的[cowsay](https://www.npmjs.com/package/cowsay)套件來做個展示
  + [cowsay](https://www.npmjs.com/package/cowsay)套件能提供一個終端機介面的程式,並以母牛(cow)的方式說話
  + 當我們透過`npm`安裝`cowsay`套件時, **該套件就會安裝它自己本身** & **其所有相依的套件(dependencies)在`node_modules/`資料夾中**
    * ![node_modules-content](../assets/pics/nodejs/node_modules-content.png)
  + 在`node_modules/`資料夾中會有一個隱藏目錄`.bin/`,裡面包含指向`cowsay`套件的二進制文件(binaries)
    * ![binary-files](../assets/pics/nodejs/binary-files.png)
  + 我們可以透過`npx`來執行`cowsay`套件來試試看,`npx`會自動去找到該套件的路徑位置
    * 例: $ `npx cowsay`
    * ![cow-say](../assets/pics/nodejs/cow-say.png)

### The package.json guide
- 如果您有使用過Javascript,或是曾經有和Javascript專案互動(interacted)過,或是您是一位Node.js後端開發人員,或是前端開發人員,您肯定認識`package.json`這個檔案
- 接下來我們會討論`package.json`這個檔案的
  + 有什麼用途呢?
  + 我們應該要對於這個檔案有什麼認知呢?
  + 我們能用它做什麼特別的事情呢?
- `package.json`有點像是我們專案的清單(manifest),它可以用來做很多事情。它是安裝工具(configuration for tools)的一個中央儲存庫(central repository)
  + 例如: `npm` & `yarn`都會將套件(package)的名稱(names)與對應版本(versions)都儲存在這裡
- `package.json`的檔案結構(file structure)
  + 以下是一個`package.json`檔案的範例
    * `{}`
  + 我們可以看到原始的`package.json`檔案內容是空的,對於應用程式來說,它沒有什麼固定的要求,唯一的要求就是需要是`JSON`的資料格式; 否則程式將無法透過程式的方式(programmatically)來讀取它
  + 如果我們在建構(build)一個Node套件,並透過`npm`來分發(distribute over)它,將發生根本性的變化(things change radically),並且必須有一組屬性來幫助其他人來使用這個套件
    * 這是另一個`package.json`檔案的範例
      ``` js
      {
        "name": "test-project"
      }
      ```
    * 它定義了一個`name`屬性(property),該屬性會告訴應用程式(application)或是套件(package),其屬性也會包含於該`package.json`檔案所屬的專案目錄中
  + 以下是一個更複雜的範例,這個範例是從Vue.js的應用程式所提取出來的一部分
      ``` js
      {
        "name": "test-project",
        "version": "1.0.0",
        "description": "A Vue.js project",
        "main": "src/main.js",
        "private": true,
        "scripts": {
          "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
          "start": "npm run dev",
          "unit": "jest --config test/unit/jest.conf.js --coverage",
          "test": "npm run unit",
          "lint": "eslint --ext .js,.vue src test/unit",
          "build": "node build/build.js"
        },
        "dependencies": {
          "vue": "^2.5.2"
        },
        "devDependencies": {
          "autoprefixer": "^7.1.2",
          "babel-core": "^6.22.1",
          "babel-eslint": "^8.2.1",
          "babel-helper-vue-jsx-merge-props": "^2.0.3",
          "babel-jest": "^21.0.2",
          "babel-loader": "^7.1.1",
          "babel-plugin-dynamic-import-node": "^1.2.0",
          "babel-plugin-syntax-jsx": "^6.18.0",
          "babel-plugin-transform-es2015-modules-commonjs": "^6.26.0",
          "babel-plugin-transform-runtime": "^6.22.0",
          "babel-plugin-transform-vue-jsx": "^3.5.0",
          "babel-preset-env": "^1.3.2",
          "babel-preset-stage-2": "^6.22.0",
          "chalk": "^2.0.1",
          "copy-webpack-plugin": "^4.0.1",
          "css-loader": "^0.28.0",
          "eslint": "^4.15.0",
          "eslint-config-airbnb-base": "^11.3.0",
          "eslint-friendly-formatter": "^3.0.0",
          "eslint-import-resolver-webpack": "^0.8.3",
          "eslint-loader": "^1.7.1",
          "eslint-plugin-import": "^2.7.0",
          "eslint-plugin-vue": "^4.0.0",
          "extract-text-webpack-plugin": "^3.0.0",
          "file-loader": "^1.1.4",
          "friendly-errors-webpack-plugin": "^1.6.1",
          "html-webpack-plugin": "^2.30.1",
          "jest": "^22.0.4",
          "jest-serializer-vue": "^0.3.0",
          "node-notifier": "^5.1.2",
          "optimize-css-assets-webpack-plugin": "^3.2.0",
          "ora": "^1.2.0",
          "portfinder": "^1.0.13",
          "postcss-import": "^11.0.0",
          "postcss-loader": "^2.0.8",
          "postcss-url": "^7.2.1",
          "rimraf": "^2.6.0",
          "semver": "^5.3.0",
          "shelljs": "^0.7.6",
          "uglifyjs-webpack-plugin": "^1.1.1",
          "url-loader": "^0.5.8",
          "vue-jest": "^1.0.2",
          "vue-loader": "^13.3.0",
          "vue-style-loader": "^3.0.1",
          "vue-template-compiler": "^2.5.2",
          "webpack": "^3.6.0",
          "webpack-bundle-analyzer": "^2.9.0",
          "webpack-dev-server": "^2.9.1",
          "webpack-merge": "^4.1.0"
        },
        "engines": {
          "node": ">= 6.0.0",
          "npm": ">= 3.0.0"
        },
        "browserslist": ["> 1%", "last 2 versions", "not ie <= 8"]
      }
      ```
    * 這裡有幾個重要的屬性要說明
      * `version`: 表示目前的版本號
      * `name`: 設定應用程式(application)/套件(package)的名稱
      * `description`: 簡單的描述該應用程式(application)/套件(package)
      * `main`: 設定應用程式的入口點(entry point)
      * `private`: 如果設定為`true`,`npm`就會拒絕發布該應用程式(application)/套件(package),此屬性是用來防止我們不小心公開地發布這個應用程式(application)/套件(package)
      * `scripts`: 定義一組`node`的`CLI`腳本(scripts)指令
      * `dependencies`: 設定一個`npm`需要安裝的相依性套件清單(list)
      * `devDependencies`: 設定一個在開發環境(development)中`npm`需要安裝的相依性套件清單(list)
      * `engines`: 設定要用哪個Node的版本來運行該應用程式(application)/套件(package)
      * `browserslist`: 用來聲明該應用程式(application)/套件(package)有支援哪些瀏覽器 & 其瀏覽器的版本
      * 以上的所有屬性,都可以被`npm`或是其他可以讀取`package.json`的檔案所使用
- `package.json`的屬性分類(properties breakdown)
  + 本章節會開始詳細描述我們可以使用的`package.json`檔案的屬性細節。在這裡我們主要討論的是套件(package),但是同樣的道理也適用於我們本地端(local)的應用程式
  + 這些屬性大多數僅會在[npm](https://www.npmjs.com/)官網上所用到。另外,其他屬性則會與我們的程式碼(code)互動(interact with),像是`npm`或是其他的套件管理包工具
  + 以下列舉`package.json`中的幾個常用且重要的屬性
    * `name`: 設定套件(package)名稱
      ``` js
      "name": "test-project"
      ```
      * `name`屬性的命名規則有以下幾項
        * 必須小於214字元(characters)的長度限制
        * 不能含有空格(spaces)
        * 僅能使用 **小寫** 英文字母
        * 可以使用連字號(hyphens) & 底線(underscores)
      * 這是因為當該套件(package)被發布(publish)到`npm`上時,它會基於`name`屬性的值來取得一段自己的(own) URL
      * 如果我們要發布一個套件(package)到`GitHub`上時,此`name`屬性就是一個作為GitHub上儲存庫(repository)的名稱
    * `author`: 列出該套件的作者的相關資訊,可以用以下2種形式來表示(直接列舉 or 物件(object))
      ``` js
      {
        "author": "Joe <joe@whatever.com> (https://whatever.com)"
      }
      ```
      ``` js
      {
        "author": {
          "name": "Joe",
          "email": "joe@whatever.com",
          "url": "https://whatever.com"
        }
      }
      ```
      * 作者(author)為一個人,可以用物件(object)的形式來表示,會有一個`name`的鍵(key)值(value),並且通常會有`url`與`email`的鍵值
    * `contributors`: 除了作者(author)以外,該應用程式(application)/套件(package)可以另外有1~多個貢獻者(contributors),可以用陣列(array)的形式來表示
      ``` js
      {
        "contributors": ["Joe <joe@whatever.com> (https://whatever.com)"]
      }
      ```
      ``` js
      {
        "contributors": [
          {
            "name": "Joe",
            "email": "joe@whatever.com",
            "url": "https://whatever.com"
          }
        ]
      }
      ```
    * `bug`: 連結到該套件(package)的問題追蹤區,大多數情況下會是一個`GitHub`的問題討論頁面(issue page)
      ``` js
        {
          "bugs": "https://github.com/whatever/package/issues"
        }
      ```
    * `homepage`: 設定一個該套件(package)的主頁(homepage)畫面連結
      ``` js
      {
        "homepage": "https://whatever.com/package"
      }
      ```
    * `version`: 表示當前的(current)套件版本號
      ``` js
      "version": "1.0.0"
      ```
      * 該屬性會遵從語意化版本標記(semantic versioning notation)規則,也就是所有`npm`上的套件版本號都會是 **x.x.x** 的數字形式
      * 語意化版本標記(semantic versioning notation)規則
        * 第一個數字代表主要版本號(major version)
          * `使用情境`: 當您進行不兼容的API更改時的主要版本 => 也就是當該套件版本 **含有重大更改** 時
        * 第二個數字代表次要版本號(minor version)
          * `使用情境`: 以向下兼容的方式添加功能時的MINOR版本 => 也就是 **僅引入(introduces)與過去相容的套件功能** 時
        * 第三個數字代表修補版本號(patch version)
          * 向後兼容的bug修復程序時的PATCH版本 => 也就是 **僅修改錯誤** (only fixes bugs)時
        * 可參考`npm`上的[semver](https://www.npmjs.com/package/semver)套件(package)
        * 另外,也可以[Semantic Versioning 2.0.0](https://semver.org/)的官方網站
    * `license`: 表示一個套件的授權(license)範圍
      ``` js
      "license": "MIT"
      ```
      * 可參考[常見的五個開源專案授權條款,使用軟體更自由](https://noob.tw/open-source-licenses/)
    * `keywords`: 包含與該套件(package)相關的關鍵字的陣列(array)
      ``` js
      "keywords": [
        "email",
        "machine learning",
        "ai"
      ]
      ```
      * 該屬性是用來幫助其他人在瀏覽[npm](https://www.npmjs.com/)官網上其他相似的套件時,能透過這些關鍵字更快導向(navigating)到我們的套件
    * `description`: 包含一段對該套件(package)的簡潔的描述
      * 該屬性是當我們在`npm`上公開發布(publish)我們的套件(package)時,能讓其他人了解這個套件的用途與使用方法,這是特別有用的
        ``` js
        "description": "A package to work with strings"
        ```
    * `repository`: 指定這個套件(package)的程式碼儲存庫在哪裡
        ``` js
        "repository": "github:whatever/testing",
        ```
      * 注意`github`前綴(prefix)字樣,我們也可以使用其它流行的程式碼儲存庫,像是`gitlab` or `bitbucket`
          ``` js
          "repository": "gitlab:whatever/testing",
          ```

          ``` js
          "repository": "bitbucket:whatever/testing",
          ```
      * 我們也可以明確地設定一個版本控制系統(version control system)
          ``` js
          "repository": {
            "type": "git",
            "url": "https://github.com/whatever/testing.git"
          }
          ```
      * 也可以指定其它的版本控制系統,像是`svn`
          ``` js
          "repository": {
            "type": "svn",
            "url": "..."
          }
          ```
    * `main`: 設定一個此套件的進入點(entry point)
      * 當我們在應用程式中匯入(import)此套件時,該屬性就會從我們應用程式的這個進入點(entry point)路徑裡面,搜尋(search)並匯出(export)模組
        ``` js
        "main": "src/main.js"
        ```
    * `private`: 用來防止我們將此套件不小心公開地發布到`npm`上
      * 可以將該屬性值設定為`true`,來防止我們將此套件不小心公開地發布到`npm`上
        ``` js
        "private": true
        ```
    * `scripts`: 可用來定義一組可以給Node執行的腳本(scripts)指令
        ``` js
        "scripts": {
          "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
          "start": "npm run dev",
          "unit": "jest --config test/unit/jest.conf.js --coverage",
          "test": "npm run unit",
          "lint": "eslint --ext .js,.vue src test/unit",
          "build": "node build/build.js"
        }
        ```
      * 這些腳本(scripts)指令是用來在`CLI`終端機上執行的,我們可以透過以下指令的形式來執行
        * $ `npm run <script name>`
        * 例: $ `npm run dev` 或是 `yarn dev`
    * `dependencies`: 設定一個清單來表示該套件的相依套件名稱與其分別的版本號之間的對應關係
      * 可以用物件(object)的形式來表示,鍵=相依套件的名稱,值=對應的版本號
      * 我們可以使用`npm`或是`yarn`來安裝所有這些相依套件
        * 例: $ `npm install <PACKAGENAME>`
        * 例: $ `yarn add <PACKAGENAME>`
      * 當透過`npm`或是`yarn`來安裝這些相依套件後,`package.json`檔案的`dependencies`屬性就會自動地新增一條該套件(package)的相依套件(dependencies)的鍵值
        ``` js
        "dependencies": {
          "vue": "^2.5.2"
        }
        ```
    * `devDependencies`: 設定一個清單來表示 **在開發環境(development)中** 該套件的相依套件名稱與其分別的版本號之間的對應關係
      * 該屬性與前一個`dependencies`屬性的差別是,`devDependencies`屬性中所列出的相依套件需要 **在開發環境(development)中** 才會被安裝,並不會在生產環境(production)中被安裝
      * 我們可以使用`npm`或是`yarn`來安裝所有這些相依套件
        * 例: $ `npm install --save-dev <PACKAGENAME>`
        * 例: $ `yarn add --dev <PACKAGENAME>`
      * 當透過`npm`或是`yarn`來安裝這些相依套件後,`package.json`檔案的`dependencies`屬性就會自動地新增一條 **在開發環境(development)中** 該套件(package)的相依套件(devDependencies)的鍵值
        ``` js
        "devDependencies": {
          "autoprefixer": "^7.1.2",
          "babel-core": "^6.22.1"
        }
        ```
    * `engines`: 可設定此應用程式(application)/套件(package)想要用什麼版本的`Node`, `npm`, `yarn`來執行 or 安裝
      * 可指定一個明確的版本,或是給定一個版本區間的範圍
        ``` js
        "engines": {
          "node": ">= 6.0.0",
          "npm": ">= 3.0.0",
          "yarn": "^0.13.0"
        }
        ```
    * `browserslist`: 用來聲明這個套件(package)能支援哪些瀏覽器以及其瀏覽器的對應版本號
      * 該屬性通常是給[Babel](https://babeljs.io/), [Autoprefixer](https://www.npmjs.com/package/autoprefixer)以及其他的工具來讀取的
      * 僅會將`polyfills`與`fallbacks`新增到目標瀏覽器中
        ``` js
        "browserslist": [
          "> 1%",
          "last 2 versions",
          "not ie <= 8"
        ]
        ```
        * 以上的範例設定表示我們想要支援過去所有瀏覽器最新的2個主要版本,並且至少要有使用1%的使用率(依據[CanIUse.com](https://caniuse.com/)的統計數據為準)。另外,不支援IE8以及其更舊的版本
      * 可參考`npm`上的[browserslist](https://www.npmjs.com/package/browserslist)套件
    * 特殊命令的屬性(Command-specific properties)
      * 其實`package.json`檔案還可以託管(host)特殊命令(command-specific)的設定(configuration),像是[eslintConfig](https://eslint.org/docs/user-guide/configuring), [babel](https://babeljs.io/docs/en/configuration)...等等其它的設定
      * 這些都是特殊命令的屬性設定,我們可以到它們分別的官方文件中查詢該如何設定它們(例: `ESlint`, `Babel`)
- 套件的版本號(Package versions)
  + 我們可以從上面的`package.json`範例中看到像是`~3.0.0`或是`^0.13.0`的版本表示方式,這些前綴符號(symbol)代表該套件(package)的這些相依套件(dependencies)可以被更新(update)到哪個版本
  + 其版本號規則可參考[Semantic Versioning using npm](https://nodejs.dev/learn/semantic-versioning-using-npm)章節的說明
  + 我們可以使用組合範圍的版本表達方式
    * 例: `1.0.0 || >=1.1.0 <1.2.0`
    * 以上的組合範圍的版本表達方式就代表我們可以使用`1.0.0`以上 or 從`1.1.0`起,但低於`1.2.0`的版本

### The package-lock.json file
- 從`npm` v5.0.0開始, 就開始引進(introduced)`package-lock.json`檔案
- `package-lock.json`檔案主要是用來追蹤(track)每個套件已安裝的確切(exact)版本,以便可以利用相同的方式來達到100%的複製出同樣的軟體產品,因此也不會受到套件維護者(maintainers)更新這些套件時而影響
- 這解決了一個`package.json`檔案尚未解決的一個具體的問題。在`package.json`中,我們可以利用[semver](https://docs.npmjs.com/cli/v6/using-npm/semver)來設定(set)我們要升級(upgrade)到的版本,像是次要(minor)或是修補(patch)版本
  + 範例說明
  + `~0.13.0`: 表示只想更新(update)修補版本,例如`0.13.1`可以,但是`0.14.0`就不行
  + `^0.13.0`: 表示只想更新次要(minor)以及修補(patch)版本,例如`0.13.0`和`0.14.0`...等等都是可以的
  + `0.13.0`: 表示我們將始終使用這個確切的版本號
- 正常來說,我們 **不會提交** (commit)`node_modules/`資料夾,因為它通常很大。當我們想要複製出一份相同的專案,可以透過 $ `npm install` 指令,該指令就會參考`~`與`^`語法(syntax)來下載相應的版本號
  + 當然,我們有可以指定確切的版本號,像是`0.13.0`
- 因此,我們的原始專案與新初始化的專案實際上是不同的。即使次要(minor)或是修補(patch)版本不應該引入(introduce)重大更改(breaking changes),但是我們都知道仍然可能有`bug`埋藏在其中
- `package-lock.json`檔案會設定目前已安裝的套件的 **確切版本號** ,`npm`就會透過 $ `npm install` 指令來安裝; 其實這不是一個一個新觀念,如同`PHP`的`Composer`套件管理包工具就已經使用類似的系統很多年了
- 如果該專案是公開的,或是我們有其他協作者(collaborators),或者我們有使用Git作為用來部署的資源,`package-lock.json`檔案都需要被提交(commit)到`Git`的儲存庫中,使它能夠被其他人下載
- 所有的相依套件都會被`package-lock.json`更新(updated),當我們執行以下指令時
  + $ `npm update`
- 以下是在一個空資料夾,執行 $ `npm install cowsay` 來安裝`package-lock.json`所得到的範例檔案結構
    ``` js
    {
      "requires": true,
      "lockfileVersion": 1,
      "dependencies": {
        "ansi-regex": {
          "version": "3.0.0",
          "resolved": "https://registry.npmjs.org/ansi-regex/-/ansi-regex-3.
    0.0.tgz",
          "integrity": "sha1-7QMXwyIGT3lGbAKWa922Bas32Zg="
        },
        "cowsay": {
          "version": "1.3.1",
          "resolved": "https://registry.npmjs.org/cowsay/-/cowsay-1.3.1.tgz"
    ,
          "integrity": "sha512-3PVFe6FePVtPj1HTeLin9v8WyLl+VmM1l1H/5P+BTTDkM
    Ajufp+0F9eLjzRnOHzVAYeIYFF5po5NjRrgefnRMQ==",
          "requires": {
            "get-stdin": "^5.0.1",
            "optimist": "~0.6.1",
            "string-width": "~2.1.1",
            "strip-eof": "^1.0.0"
          }
        },
        "get-stdin": {
          "version": "5.0.1",
          "resolved": "https://registry.npmjs.org/get-stdin/-/get-stdin-5.0.
    1.tgz",
          "integrity": "sha1-Ei4WFZHiH/TFJTAwVpPyDmOTo5g="
        },
        "is-fullwidth-code-point": {
          "version": "2.0.0",
          "resolved": "https://registry.npmjs.org/is-fullwidth-code-point/-/
    is-fullwidth-code-point-2.0.0.tgz",
          "integrity": "sha1-o7MKXE8ZkYMWeqq5O+764937ZU8="
        },
        "minimist": {
          "version": "0.0.10",
          "resolved": "https://registry.npmjs.org/minimist/-/minimist-0.0.10
    .tgz",
          "integrity": "sha1-3j+YVD2/lggr5IrRoMfNqDYwHc8="
        },
        "optimist": {
          "version": "0.6.1",
          "resolved": "https://registry.npmjs.org/optimist/-/optimist-0.6.1.tgz",
          "integrity": "sha1-2j6nRob6IaGaERwybpDrFaAZZoY=",

          "requires": {
            "minimist": "~0.0.1",
            "wordwrap": "~0.0.2"
          }
        },
        "string-width": {
          "version": "2.1.1",
          "resolved": "https://registry.npmjs.org/string-width/-/string-width-2.1.1.tgz",
          "integrity": "sha512-nOqH59deCq9SRHlxq1Aw85Jnt4w6KvLKqWVik6oA9ZklXLNIOlqg4F2yrT1MVaTjAqvVwdfeZ7w7aCvJD7ugkw==",
          "requires": {
            "is-fullwidth-code-point": "^2.0.0",
            "strip-ansi": "^4.0.0"
          }
        },
        "strip-ansi": {
          "version": "4.0.0",
          "resolved": "https://registry.npmjs.org/strip-ansi/-/strip-ansi-4.0.0.tgz",
          "integrity": "sha1-qEeQIusaw2iocTibY1JixQXuNo8=",
          "requires": {
            "ansi-regex": "^3.0.0"
          }
        },
        "strip-eof": {
          "version": "1.0.0",
          "resolved": "https://registry.npmjs.org/strip-eof/-/strip-eof-1.0.0.tgz",
          "integrity": "sha1-u0P/VZim6wXYm1n80SnJgzE2Br8="
        },
        "wordwrap": {
          "version": "0.0.3",
          "resolved": "https://registry.npmjs.org/wordwrap/-/wordwrap-0.0.3.tgz",
          "integrity": "sha1-o9XabNXAvAAI03I0u68b7WMFkQc="
        }
      }
    }
    ```
  + `npm`會依據以下4點來安裝`cowsay`
    * get-stdin
    * optimist
    * string-width
    * strip-eof
  + 反過來說,以上那些套件也需要其它套件(packages),正如我們從`requires`屬性所看到的
    * ansi-regex
    * is-fullwidth-code-point
    * minimist
    * wordwrap
    * strip-eof
  + 它們都是按照英文字母順序來加入到`package-lock.json`檔案中的,每個套件都會有以下幾種欄位
    * `version`: 版本號
    * `resolved`: 指向該套件(package)的下載位置
    * `integrity`: 可以用來驗證(verify)該套件的一段文字(通常會是`SHA512`形式的值)

### Find the installed version of an npm package
> [npm list](https://docs.npmjs.com/cli/v7/commands/npm-ls) - List installed packages<br>
> [npm view](https://docs.npmjs.com/cli/v7/commands/npm-view) - View registry info<br>

- 如果我們想要檢視所有已經安裝的套件的最新版本的話(也包含它們所相依的套件們),可以使用以下指令
  + $ `npm list`
    * `-g`: 列出所有已經安裝的全域套件的最新版本
  + ![npm list example](../assets/pics/nodejs/npm%20list%20example.png)
- 我們也可以打開`package-lock.json`檔案來做一些視覺上的掃描(visual scanning)
  + 如果我們只想要列出第一(最高)階層的套件(基本上就是我們利用`npm`來安裝`package.json`中列出的那些套件們),我們可以使用以下指令
    * $ `npm list --depth=0`
      * `--depth`: 想要展示出的套件相依性樹狀圖的最大深度
    * ![npm list --depth=0](../assets/pics/nodejs/npm%20list%20--depth=0.png)
  + 我們有可以透過指定套件名稱來得到其版本號
    * $ `npm list <package>`
    * ![npm list <package>](../assets/pics/nodejs/npm%20list%20<package>.png)
  + 這也適用於我們安裝的套件(package)的相依套件(dependencies)
    * ![npm list <dependencies>](../assets/pics/nodejs/npm%20list%20<dependencies>.png)
  + 我們也可以查看該套件在`npm`儲存庫(repository)上的 **最新版本**
    * $ `npm view <package_name> version`
    * ![npm view <package_name> version](../assets/pics/nodejs/npm%20view%20<package_name>%20version.png)

### Install an older version of an npm package
- 我們可以透過`@`語法(syntax)來安裝較舊版本的`npm`套件(package)
  + $ `npm install <package>@<version>`
  + 例: $ `npm install cowsay@1.2.0`
  + 例: $ `npm install -g webpack@4.16.4`
- 我們可能會對某個套件的 **所有版本** 有興趣,我們可以透過以下指令來檢視
  + $ `npm view <package> versions`
  + 例: $ `npm view cowsay versions`
    * ![npm view <package> versions](../assets/pics/nodejs/npm%20view%20<package>%20versions.png)


### Update all the Node.js dependencies to their latest version
> [npm update](https://docs.npmjs.com/cli/v7/commands/npm-update) - Update a package<br>
> [npm outdated](https://docs.npmjs.com/cli/v7/commands/npm-outdated) - Check for outdated packages<br>
> npm的[check updates](https://www.npmjs.com/package/npm-check-updates)套件---upgrades your package.json dependencies to the latest versions, ignoring specified versions.<br>

- 當我們透過$ `npm install <package-name>`來安裝時,該套件的最新(latest)可用(available)版本就會被下載到`node_modules/`資料夾中,並且將相對應的(corresponding)項目(entry)新增到當前資料夾裡面的`package.json`檔案 & `package-lock.json`檔案中
- `npm`會自動計算(calculates)該套件的相依套件們(dependencies),並安裝最新可用的相依套件們
- 比如說,當我們安裝`cowsay`這個套件,它是一個可`CLI`介面的工具可以讓一隻牛說些話
  + 當我們使用$ `npm install`指令時,該項目(entry)就會被新增到`package.json`檔案中
    ``` js
      {
        "dependencies": {
          "cowsay": "^1.3.1"
        }
      }
    ```
  + 以下是從`package-lock.json`檔案中,提取(extract)出的片段。為了清楚起見,我們移除了巢狀相依套件的內容
    ``` js
      {
        "requires": true,
        "lockfileVersion": 1,
        "dependencies": {
          "cowsay": {
            "version": "1.3.1",
            "resolved": "https://registry.npmjs.org/cowsay/-/cowsay-1.3.1.tgz",
            "integrity": "sha512-3PVFe6FePVtPj1HTeLin9v8WyLl+VmM1l1H/5P+BTTDkMAjufp+0F9eLjzRnOHzVAYeIYFF5po5NjRrgefnRMQ==",
            "requires": {
              "get-stdin": "^5.0.1",
              "optimist": "~0.6.1",
              "string-width": "~2.1.1",
              "strip-eof": "^1.0.0"
            }
          }
        }
      }
    ```
  + 以上兩個檔案告訴我們要安裝`cowsay`套件的`v1.3.1`版本,並且我們的更新(updates規則是`^1.3.1`,這代表`npm`可以更新次要(minor)與修補(patch)版本,像是`v1.3.2`,`v1.4.0`, ...等等
    * 也就是說,當有次要(minor)與修補(patch)版本發布時,我們可以透過$ `npm update`來更新套件版本。這樣一來,我們已安裝的套件就會是最新版本
      * 同時`package-lock.json`檔案也會勤奮地(diligently)填上(filled with)新版本; 而`package.json`檔案維持不變
- 可以透過以下指令,發現(discover)是否有套件有發布新版本
  + $ `npm oudated`
    * ![npm outdated](../assets/pics/nodejs/npm%20outdated.png)
      * 上述的部分套件需要更新主要版本(major),這時候如果我們執行$ `npm update`就 **不會** 更新到這些套件。若要更新套件的主要(major)版本,絕不會用這種方式,因為依據語意化版本規則的定義,主要版本的更新代表有重大更改(breaking changes),而`npm`想要幫我們省下麻煩
  + 如果我們確定想要更新套件的 **主要(major)版本** 的話,可以先 **全域** 安裝`check-updates`這個套件
    * $ `npm install -g npm-check-updates`
    * 接著執行它(該套件)
    * $ `ncu -u`
      * 這樣它就會升級(upgrade)所有在`package.json`檔案中`dependencies`與`devDependencies`的版本提示(version hints),因此`npm`就可以安裝套件新的主要版本
- 如果我們只是下載遠端儲存庫上的專案,而不包含其`node_modules/`資料夾中的相依套件的話,我們可以透過以下指令,來安裝最新的套件版本
  + $ `npm install`

### Semantic Versioning using npm
> npm的[semver](https://docs.npmjs.com/cli/v7/using-npm/semver)套件---The semantic versioner for npm<br>

- 在Node.js套件的世界中,有一件很棒的事情,那就是它們皆同意使用語意化版本規則(using Semantic Versionin)來作為他們的版本編號
- 語意化版本規則的觀念其實很簡單--->所有的都由三個數字(digits)組合而成
  + 範例: `x.y.z`
    * 第1個數字`x`是主要版本(major)
    * 第2個數字`y`是次要版本(minor)
    * 第3個數字`z`是修補版本(patch)
- 當我們發布一個新的套件(package)版本時,我們不僅可以自己選擇要增加的數字之外,也仍須遵守以下規則
  + `major`: 當我們進行不兼容(incompatible)的API修改(changes)時,可以提升(up)主要版本號
  + `minor`: 當我們新增一個能向後兼容(backward-compatible, => 也就是能與過去相容)的功能時,可以提升(up)次要版本號
  + `patch`: 當我們修復(fix)一個能向後兼容(backward-compatible bug fixes)的錯誤(bug)時,可以提升(up)修補版本號
- 這樣的版本號慣例(convention)也被所有的程式語言所採用(adopted),而`npm`的每個套件(package)有都會遵守(adheres to)此版本號規範,所以此一版本號規範是非常重要的
- 因為`npm`設定了一些規則,讓我們可以透過`package.json`檔案來選擇當我們利用$ `npm update`指令來更新我們的套件時,要更新到的哪個版本號
  + 以下是`npm`設定給`package.json`檔案的版本號規範語法
    * `^`(Tilde Ranges)
      * 只會更新(updates),但不會更改最左邊(leftmost)非零(non-zero)的數字
      * 舉例來說,當我們執行$ `npm update`指令時
        * 例1: `^0.13.0` => 可以更新(update)到`0.13.1`或`0.13.2`...等等
        * 例2: `^1.13.0` => 可以更新(update)到`1.13.1`或`1.14.0`,但是 **不能** 更新到`2.0.0`或是再其更之上的版本
    * `~` (Caret Ranges)
      * 如果次要版本在`package.json`中已經指定了的話,允許修補層級的更改(patch-level changes); 若不允許修補層級的修改的話,則允許次要版本層級的修改(minor-level changes)
      * 舉例來說,當我們執行$ `npm update`指令時
        * 例1: `~0.13.0` => 可以更新到`0.13.1`,但是 **不能** 更新到`0.14.0`
    * `>`
      * 可以接受比我們指定的版本還要高的任何一個版本
    * `>=`
      * 可以接受與我們指定的版本相同or還要高的任何一個版本
    * `<`
      * 可以接受比我們指定的版本還要低的任何一個版本
    * `<=`
      * 可以接受與我們指定的版本相同or還要低的任何一個版本
    * `=`
      * 可以接受與我們指定的確切版本 **完全相同** 的版本
    * `-` (Hyphen Ranges)
      * 可以接受一個指定範圍內的所有版本號
      * 舉例來說,當我們執行$ `npm update`指令時
        * 例1: `1.2.3 - 2.3.4` => 表示可以更新到 `>=1.2.3 && <=2.3.4`的版本號
    * `||` (combine sets)
      * 可以接受一個組合的指定範圍內的所有版本號
      * 舉例來說,當我們執行$ `npm update`指令時
        * 例1: `1.0.0 || >=1.1.0 <1.2.0 ` => 可以更新為`1.0.0`的這個確切版本,或是也可以更新到`1.1.0`或是再其更之上的版本,但是 **不能** 更新為`1.2.0`或是再其更之上的版本
        * 例2: `1.2.7 || >=1.2.9 <2.0.0` => 可以更新為`1.2.7`的這個確切版本,或是也可以更新到`1.2.9`或是再其更之上的版本,但是 **不能** 更新為`1.2.8`或是`2.0.0`的版本
- 語意化版本規則也有其他特殊規則
  + 無任何符號(no symbol): 只能接受指定的該明確版本
    * 例: `1.2.1`
  + `latest`: 直接指定可用(available)且最新(latest)的該套件版本號

### Uninstalling npm packages
> [npm uninstall](https://docs.npmjs.com/cli/v7/commands/npm-uninstall) - Remove a package<br>

- 要在本地環境(locally)解除安裝(uninstall)之前已安裝過,並且儲存在`node_modules/`資料夾中的套件(packages)。可以在該專案的根目錄(project root folder, => 也就是包含`node_modules/`資料夾的專案根目錄),並透過以下指令
  + $ `npm uninstall <package-name>`
- 若想要連同`package.json`檔案中的參考(reference)也刪除掉的話,可以使用`-S`或是`--save`選項
  + $ `npm uninstall --save <package-name>`
    * `--save`(=> `-S`): `npm`會將該套件(package)從`package.json`檔案中的`dependencies`鍵之中移除
- 若此套件(package)為開發環境所使用的套件的話,可以使用`-D`或是`--save-dev`選項
  + $ `npm uninstall --save-dev <package-name>`
    * `--save-dev`(=> `-D`): `npm`會將該套件(package)從`package.json`檔案中的`devDependencies`鍵之中移除
- 若此套件(package)是在全域環境安裝的話,可以使用`-g`或是`--global`選項
  + $ `npm uninstall -g <package-name>`
    * `--global`(=> `-g`): `npm`會將該套件(package)從全域環境移除掉,而這時候也無需在意我們是在系統上的哪個目錄下執行該指令,因為我們是要 **全域** 解除安裝此套件
  + 例: $ `npm uninstall -g webpack`

### npm global or local packages
> [npx](https://docs.npmjs.com/cli/v7/commands/npx) - Run a command from a local or remote npm package<br>

- 本地(local)與全域(global)套件(package)的主要差別是
  + 本地(local)套件會安裝在我們執行$ `npm install <package-name>`指令時的那個目錄下,並將`node_modules/`資料夾放在這個資料夾的裡面
  + 全域(global)套件則會安裝在作業系統中的單一個路徑下(確切來說要視各作業系統而定),這時就不會管我們是在哪裡執行$ `npm install -g <package-name>`指令了
- 在我們的程式碼中,我們 **僅能引入(require)本地(local)安裝的套件(package)**
  + 例: `require('package-name')`
- 所以什麼時候需要本地安裝,而什麼時候需要全域安裝套件呢?
  + 一般來說,所有的套件都應該要用 **本地安裝** (locally)
    * 這樣可以確保在我們的電腦中有許多的(dozens of)應用程式(application),而這些套件也分別需要用到不同版本的相依套件時,不會混亂掉
  + 更新全域安裝的套件會讓我們整台電腦的所有專案都被迫需要用最新版本的套件。而這也讓我們可以想像到,對於維運面來說,這會是多大的一場惡夢。因為某些套件可能會破壞(break)與其他相依套件(dependencies)之間的相容性(compatibility)
- 所有的專案都有它們自己的本地安裝(local)的套件版本,即使這樣可能看似是一種浪費資源的行為,但與其可能造成的負面影響(negative consequences)相比,這樣算是非常小的"浪費"
- 只有在我們需要一個可執行(executable)在`CLI`介面上的指令(command),並且這個可執行命令是能跨專案之間(across project)能被重複使用的(reused),那這時候就會需要 **全域安裝套件** (global)
  + 我們也可以本地(locally)安裝可執行命令(executable commands),並透過`npx`來執行。但是有些特定的套件(package),還是會建議用全域(globally)的方式來安裝
    * 以下是一些流行且建議需要全域安裝的套件
      * npm
      * create-react-app
      * vue-cli
      * grunt-cli
      * mocha
      * react-native-cli
      * gatsby-cli
      * forever
      * nodemon
  + 可參考官方文件---[npx VS npm exec](https://docs.npmjs.com/cli/v7/commands/npx#npx-vs-npm-exec)章節的說明
  + 如果我們想知道目前在我們系統中已經全域安裝的套件有哪些的話,可以透過執行以下的指令
    * $ `npm list -g --depth 0`
      * ![npm list -g --depth 0](../assets/pics/nodejs/npm%20list%20-g%20--depth%200.png)

### npm dependencies and devDependencies
- 當我們透過$ `npm install <package-name>`指令來安裝一個`npm`上的套件時,這就是將其安裝為相依套件(dependency)
  + 這個被安裝的相依套件會自動地被列出在`package.json`檔案中
  + 如果我們加上`-D`或是`--save-dev`選項的話,`npm`就會視為要安裝作為開發環境使用的相依套件(development dependency),同時也會自動地新增到`package.json`檔案中的`devDependencies`鍵中
- 開發環境的相依套件(development dependency)旨在僅用在開發環境所使用的套件(development-only packages),而這些套件也不會在生產環境(production)中所使用
  + 例: `testing packages` or `webpack` or `Babel`
- 當我們在生產環境(production)中,如果我們輸入$ `npm install`指令,並且在該目錄中已經有一個`package.json`檔案的話,則會安裝`package.json`檔案中`dependencies`屬性裡面的所有套件,因為`npm`預設會假定這是一個開發部署(development deploy)
  + 這時就必須要加上`--production`選項來避免安裝到那些開發環境的相依套件(development dependencies)
    * $ `npm install --production <package-name>`

### The npx Node.js Package Runner
> npm的[npx](https://www.npmjs.com/package/npx)套件---execute npm package binaries<br>
> [nvm](https://github.com/nvm-sh/nvm)---nvm is a version manager for node.js, designed to be installed per-user, and invoked per-shell<br>

- `npx`是一個強大的工具,是從`npm`的`v5.2.0`(約於2017/06月)發布以後,就已經開始可以使用
  + 如果我們不想下載`npm`,可以透過[install npx as a standalone package](https://www.npmjs.com/package/npx)
  + `npx`讓我們可以在Node環境中執行程式碼,並透過`npm registry`來發布
- 輕鬆地執行本地指令(local commands)
  + 過去,Node的開發者們通常將可執行命令(executable commands)發布為一個全域套件(global package),以使它們立即位於路徑(path)中,並且是立即可以被執行的
    * 這樣其實很痛苦,因為我們確實無法在同一個指令中安裝不同的版本
  + 執行$ `npx <command-name>`指令會自動地找出在該專案目錄中的`node_modules/`資料夾中的此指令的確切參考位置(correct reference of the command),而無需知道確切的路徑,也不需要在全域(global)路徑和用戶路徑中(user's path)安裝套件
- 無需先安裝,就能直接執行的指令
  + `npx`套件的的另一個重要的功能是我們能直接執行指令(run commands),而不需要事先安裝它們
    * 這個功能是非常有用的,主要是因為
      * 我們不需要事先安裝該套件
      * 我們可以透過`syntax @version`的方式來執行同一個指令的不同版本
  + 我們用`npm`的`cowsay`套件來做個展示透過`npx`來呼叫`cowsay`套件的指令。該套件會打印(print)出一隻牛並說出我們在`CLI`介面上輸入的指令
    * 舉例來說,當我們在`CLI`介面上輸入$ `cowsay "Hello"`
      * ![cowsay "Hello"](../assets/pics/nodejs/cowsay%20"Hello".png)
      * 以上的方式,僅會在我們有透過`npm`全域(globally)安裝`cowsay`套件時才能使用,否則當我們執行該指令時,會得到錯誤(error)
    * `npx`可以使我們不必事先本地(locally)安裝,就能執行該指令
      * 例: $ `npx cowsay "Hello"`
  + 然而,這是一個有趣但沒有用的範,其他情境包括
    * 執行`vue CLI`工具
      * 例: $ `npx @vue/cli create my-vue-app`
    * 透過`npm`的`create-react-app`來建立一個新的React app
      * 例: $ `npx create-react-app my-react-app`
- 透過不同的Node版本來執行程式碼
  + 我們可以透過`@`語法來指定一個特定的Node版本,並與`npm`的[node](https://www.npmjs.com/package/node)套件一起搭配來使用
    * 例: $ `npx node@10 -v` #v10.18.1
    * 例: $ `npx node@12 -v` #v12.14.1
  + 這麼做能讓我們能不用特別去使用像是`nvm`或是其他Node的版本管理工具(Node.js version management tools)
- 可以直接從`URL`上執行一段程式碼的片段(snippets)
  + `npx`並不會限制我們使用`npm`上面發布的套件
  + 我們可以利用以下的方式來直接執行`GitHub gist`上的程式碼片段
    * 例: $ `npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32`
    * 當我們需要執行我們無法控制的程式碼時,需要更小心地使用`npx`工具。因為越強大的功能,帶來越大的責任

### The Node.js Event Loop
- 事件迴圈(Event loop)是要理解Node的其中一個最重要的層面
- 事件迴圈(Event loop)能解釋Node如何達到 **非同步(=> asynchronous)** 與 **非阻塞I/O(=> non-blocking I/O)** 。這也是讓Node能成為殺手級應用程式(killer app),並且能如此成功的原因
- Node是用 **單執行緒(single thread)**  在執行Javascript的程式碼,在同一個時間只會發生一件事
  + 這個限制其實是很有用的,因為它能大大地簡化我們在開發程式的心力,而不用去擔心併發(concurrency)的問題(issues)
  + 我們只要專注在如何寫程式碼,並避免任何可能阻塞(block)線程(thread)的事情,像是以下2種情況
    * 同步網路呼叫(synchronous network calls)
    * 無限迴圈(infinite loops)
- 通常在大多數瀏覽器中,每個瀏覽器頁籤(browser tab)都會有一個事件循環,以使每個進程(process)都被隔離開,並避免網頁畫面落入無限迴圈(infinite loops)或是繁重的處理工作,以至於阻塞(block)整個瀏覽器
- 該環境管理多個併發事件迴圈(multiple concurrent event loops),以處理像是`API`呼叫的工作。網頁工作(Web Workers)也會在其自己的事件迴圈(event loops)中執行
- 我們最需要關心的是我們的程式碼會在單一事件迴圈(single event loop)上被執行,並且要牢記這一點在心中,以避免造成阻塞(blocking)
- 阻擋事件迴圈(Blocking the event loop)
  + 任何花費太多時間才將控制權交還(return back control )給事件迴圈(event loop)的Javascript程式碼,都將阻擋(block)任何該頁面上執行的Javascript程式碼,甚至是阻塞UI線程(UI thread),並且用戶無法點擊按鈕.滑動頁面...等等
  + 在Javascript中,幾乎所有的 **I/O原語(=> I/O primitives)** 都是 **非阻塞I/O(=> non-blocking I/O)** 的,例如以下2種情境
    * 網路請求(Network requests)
    * 檔案系統操作(filesystem operations)
    * ...等等
  + 被阻塞是一種例外,這也就是為什麼Javascript會有這麼多基於回呼函式(`callbacks`)的東西,而近年來也開始有`Promise`與`async/await`
- 呼叫堆疊(The call stack)
  + 呼叫堆疊是一種`LIFO`隊列(queue),也就是後進->先出(Last in, First out)的模式
  + 事件迴圈(event loop)會持續地檢查是否在呼叫堆疊(call stack)中仍有任何函式(function)需要被執行
  + 這樣做時,它會將任何所有能找到的函式都加入到呼叫堆疊(call stack)中,並依序執行每個函式
  + 我們會知道自己對於除錯器(debugger)或是瀏覽器控制台(browser console)中,會比較熟悉用哪個方式來追蹤錯誤堆疊(error stack trace)
    * 瀏覽器會在呼叫堆疊(call stack)中查詢函式(function)的名稱,以通知我們是哪個函式產生(originates)了當前的呼叫(current call)
    * ![exception-call-stack](../assets/pics/nodejs/exception-call-stack.png)
- 一個簡單的事件迴圈說明範例
  ``` js
  const bar = () => console.log('bar')

  const baz = () => console.log('baz')

  const foo = () => {
    console.log('foo')
    bar()
    baz()
  }

  foo()
  ```
  + 當以上的程式碼被執行時
    * 首先,是`foo()`被呼叫
    * 接著,在`foo()`中,會呼叫`bar()`
    * 最後,會呼叫`baz()`
  + 此時,呼叫堆疊(call stack)會看起來如下圖所示
    * ![call-stack-first-example](../assets/pics/nodejs/call-stack-first-example.png)
  + 每次迭代中的事件迴圈都會查看在呼叫堆疊(call stack)中是否有東西,並執行它,直到呼叫堆疊(call stack)變成空的(empty)為止
    * ![execution-order-first-example](../assets/pics/nodejs/execution-order-first-example.png)
- 函式執行隊列(Queuing function execution)
  + 以上的範例看起來很正常,並沒有什麼特別之處。Javascript會尋找要執行的東西,並且依序執行它們
  + 讓我們來看看另一個範例,如何將函式(function)推遲(defer)到堆疊(stack)被清除掉之前
      ``` js
        const bar = () => console.log('bar')

        const baz = () => console.log('baz')

        const foo = () => {
          console.log('foo')
          setTimeout(bar, 0)
          baz()
        }

        foo()
      ```
    * 以上的範例程式碼中的`setTimeout(() => {}, 0)`會去呼叫一個函式(function),一旦這段程式碼中的每個其它函式執行一次,便會執行一次該函式
    * 這個範例打印(print)出來的結果也許會令我們非常驚訝
        ``` js
          foo
          baz
          bar
        ```
    * 說明: 當這段程式碼被執行時
      * 首先,是`foo()`被呼叫
      * 接著,在`foo()`內部會先呼叫`setTimeout()`,並將`bar`作為參數(argument),並指示(instruct)它盡快地執行,並傳遞`0`作為計時器(timer)
      * 最後,會呼叫`baz()`
    * 此時,呼叫堆疊(call stack)會看起來如下圖所示
      * ![call-stack-second-example](../assets/pics/nodejs/call-stack-second-example.png)
    * 以下是程式(program)中的所有函式的執行順序
      * ![execution-order-second-example](../assets/pics/nodejs/execution-order-second-example.png)
  + 為什麼會這樣呢? 我們接著看下去
- 訊息隊列(Message Queue)
  + 當`setTimeout()`函式被呼叫時,瀏覽器與Node會啟動一個計時器。當計時器到期時,也就是說在這個情況下,我們將延遲(delay)參數設定為`0`,此時"延遲(delay)"就會立即到期,也會將回呼函式(callback function)放入訊息隊列(Message Queue)中
  + 在訊息隊列(Message Queue)中,使用者初始化的事件(user-initiated events)像是滑鼠點擊(click),鍵盤事件(keyboard events),獲取回應(fetch responses)都將在此排隊(queued)。然後我們才有機會對其做出反應(react),或像是`DOM事件`--->`window.onLoad`
  + **事件迴圈(event loop)將會優先處理呼叫堆疊(call stack),並首先處理(processes)在呼叫堆疊中找到的所有內容。一旦呼叫堆疊中沒有任何內容了,它就會開始到訊息隊列(message queue)中處理內容**
    * 我們不必等待像是`setTimeout()`函式,抓取資料(fetch),或是其它函式來完成它們自己的工作,因為它們是由瀏覽器所提供的,而且它們都位於自己的線程中(threads)
    * 舉例來說,如果將`setTimeout(function[, delay])`函式的`delay`(延遲)->這個參數設定為`2`秒,我們不需要真的去等待2秒,它會在其它地方發生
- ES6工作隊列(ES6 Job Queue)
  + ES6(= ECMAScript 2015)引進(introduced)了工作隊列(job queue)的觀念,並且透過`Promise`物件來使用工作隊列(job queue)
    * 補充: [Promise](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise)物件也是從ES6開始引進的
    * **Promise物件** 是一種盡快地執行 **非同步函式(async function)** 的一種方式,而不是(rather than)放在呼叫堆疊(call stack)的最後面
    * 那些在當前函式結束之前已經解決(resolve)的`Promise`物件, **將在當前函式之後立即被執行**
    * 我發現可以用遊樂園的過山車設施來做個比喻會更好,訊息隊列(message queue)會將我們放在隊列(queue)的後面,在所有其他人的後面,而我們會因此需要等到輪到我們為止。然而,工作隊列(job queue)就是快速通關券讓我們能夠搭乘完一個遊樂設施之後 **緊接著** 再搭乘下一個遊樂設施
    * 範例程式碼
      ``` js
      const bar = () => console.log('bar')

      const baz = () => console.log('baz')

      const foo = () => {
        console.log('foo')
        setTimeout(bar, 0)
        new Promise((resolve, reject) =>
          resolve('should be right after baz, before bar')
        ).then(resolve => console.log(resolve))
        baz()
      }

      foo()
      ```
    * 以上的範例程式碼將會回傳
      ``` js
      foo
      baz
      should be right after baz, before bar
      bar
      ```
  + 這是`Promises`(和基於`Promise`建構的`Async/ await`)<-->與透過`setTimeout()`或其它平台API的普通,舊的非同步函數(asynchronous functions)之間的巨大區別

### Understanding process.nextTick()
> Node內建核心模組`Process`中的[process.nextTick(callback[, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_nexttick_callback_args)---process.nextTick() adds callback to the "next tick queue". This queue is fully drained after the current operation on the JavaScript stack runs to completion and before the event loop is allowed to continue. It's possible to create an infinite loop if one were to recursively call process.nextTick()<br>

- 當我們嘗試理解Node的事件迴圈(event loop)時,`process.nextTick()`方法就是它一個重要的部分
- 每次事件迴圈經過完整的一趟(full trip)時,我們稱其為`tick`
  + 當我們傳遞一個函式(function)給`process.nextTick()`方法時,會指示(instruct)引擎(engine)於當前操作結束之後,並且於下一個事件迴圈(event loop)`tick`開始之前,呼叫(invoke)此方法(`process.nextTick()`)
  + 範例程式碼
    ``` js
    process.nextTick(() => {
      //do something
    })
    ```
    * 該事件迴圈(event loop)正忙於處理當前的程式碼
    * 當該操作結束後,Javascript引擎(engine)會以非同步(asynchronouly)的方式來處理函式(function)
  + 呼叫`setTimeout(() => {}, 0)`方法會在下一個`tick`結束之後才執行函式,比起使用`nextTick()`方法需要在下一個`tick`開始之前,優先呼叫和執行它,相對慢很多
  + 建議使用`process.nextTick()`方法來確保在下一次事件迴圈(event loop)迭代(iteration)中,已經執行了程式碼

### Understanding setImmediate()
> Node內建核心模組`Timers`中的[setImmediate(callback[, ...args])](https://nodejs.org/api/timers.html#timers_setimmediate_callback_args)---Schedules the "immediate" execution of the callback after I/O events' callbacks.<br>
> Node內建核心模組`Timers`中的[setTimeout(callback[, delay[, ...args]])](https://nodejs.org/api/timers.html#timers_settimeout_callback_delay_args)---Schedules execution of a one-time callback after delay milliseconds.<br>

- 當我們想要讓某個片段的程式碼立即被執行時,其中一個選擇是可以使用Node提供的`setImmediate()`方法
    ``` js
    setImmediate(() => {
      //run something
    })
    ```
    * 任何函式作為參數傳遞給`setImmediate()`方法,都會在下一次事件迴圈(event loop)迭代(iteration)被當作回呼函式(callback)執行
  + `setImmediate()`與`setTimeout(() => {}, 0)`與`process.nextTick()`三者有什麼不同呢?
    * 當傳遞一個函式(function)給`process.nextTick()`方法時,該函式會在當前操作(current operation)之後,並於當前事件迴圈(event loop)的迭代中(current iteration)被執行
    * 這也意味著`process.nextTick()`方法總是會在`setTimeout()`方法 & `setImmediate()`方法之前被執行
    * 當設定`setTimeout(callback[, delay[, ...args]])`方法的延遲(`delay`)參數為`0`毫秒時,這時該方法會非常類似於`setImmediate()`方法。執行順序將取決於不同的因素所影響,但它們都會在下一次事件迴圈(event loop)的迭代中(current iteration)被執行

### Discover JavaScript Timers
> Node內建核心模組`Timers`中的[setTimeout(callback[, delay[, ...args]])](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_settimeout_callback_delay_args)---Schedules execution of a one-time callback after delay milliseconds.<br>
> Node內建核心模組`Timers`中的[setInterval(callback[, delay[, ...args]])](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_setinterval_callback_delay_args)---Schedules repeated execution of callback every delay milliseconds.<br>
> Node內建核心模組`Timers`中的[clearTimeout(timeout)](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_cleartimeout_timeout)---Cancels a Timeout object created by setTimeout().<br>
> Node內建核心模組`Timers`中的[setImmediate(callback[, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_setimmediate_callback_args)---Schedules the "immediate" execution of the callback after I/O events' callbacks.<br>

- `setTimeout()`方法
  + 當我們在撰寫Javascript程式碼時,有時我們會希望延遲(delay)某些函式的執行
  + 這就是`setTimeout()`方法在做的事情。我們可以指定一個要延遲執行的回呼函式(callback function),並且指定一個想要延遲執行的時間(以毫秒為單位)
      ``` js
      setTimeout(() => {
        // runs after 2 seconds
      }, 2000)

      setTimeout(() => {
        // runs after 50 milliseconds
      }, 50)
      ```
      * 這個語法定義了一個新的函式,讓我們可以撰寫任何我們想要執行的函式,或是填入我們想要呼叫的函式並給一組參數(a set of parameters)
      ``` js
      const myFunction = (firstParam, secondParam) => {
        // do something
      }

      // runs after 2 seconds
      setTimeout(myFunction, 2000, firstParam, secondParam)
      ```
      * `setTimeout()`方法會回傳一個計時器ID(Timer ID),通常我們不會用到這個計時器ID,但是我們仍然可以將這個計時器ID記下來
      ``` js
      const id = setTimeout(() => {
        // should run after 2 seconds
      }, 2000)

      // I changed my mind
      clearTimeout(id)
      ```
      * 當我們想要將這個排定要執行的函式取消時,就會需要用到這個計時器ID
  + 零延遲執行(Zero delay)
    * 當我們指定要延遲的時間為`0`毫秒時,該回呼函式就會盡快地立即被執行,並且在當前的回呼函式(callback function)執行完後
      ``` js
      setTimeout(() => {
        console.log('after ')
      }, 0)

      console.log(' before ')
      ```
      * 以上的範例會打印(print)出`before after`
      * 這個做法( **將函式加入調度器(scheduler)的排隊(queuing)中** )對於在密集任務時,用來避免CPU資源的阻塞 or 對於要執行繁重的計算時,能讓其他函式先被執行是非常有用的
  + 有些瀏覽器(例如: `IE`或是`Edge`)會實作`setImmediate()`方法也能達到相同的功能,但那樣做不算是標準的,而且也無法在其它瀏覽器中使用。但是這算是Node的標準功能之一
    * 關於`setImmediate()`方法對於各瀏覽器的支援,可參考[Efficient Script Yielding: setImmediate()](https://caniuse.com/setimmediate)
- `setInterval()`方法
  + `setInterval()`方法與`setTimeout()`方法很類似,兩者的差別是
    * `setInterval()`方法會永遠執行下去,而不是只執行一次,並且我們可以指定想要的間隔執行時間(以毫秒為單位)
      ``` js
      setInterval(() => {
        // runs every 2 seconds
      }, 2000)
      ```
      * 以上的範例程式碼,會持續每2秒執行一次,直到我們利用`clearInterval()`方法來停止(stop)它
      ``` js
      const id = setInterval(() => {
        // runs every 2 seconds
      }, 2000)

      clearInterval(id)
      ```
      * 而要停止`clearInterval()`方法,需要傳遞給該方法間隔器ID(interval ID, => 由`setInterval()`方法所回傳的)
    * 常見的做法會是在`setInterval()`回呼函式中呼叫`clearInterval()`方法,來讓`setInterval()`方法自己自動地決定是否要再執行一次(again)或是停止(stop)
      ``` js
      const interval = setInterval(() => {
        if (App.somethingIWait === 'arrived') {
          clearInterval(interval)
          return
        }
        // otherwise do things
      }, 100)
      ```
      * 以上的範例程式碼,會在執行某些東西後,直到`App.somethingIWait`這個屬性的值等於`'arrived'`字串時,才會停止
  + 遞迴計時器(Recursive Timeout)
    * `setInterval()`方法會啟動一個每`n`豪秒執行一次的函式,而無須考慮該函式何時會執行完畢
    * 如果有一個函式,每次都會花相同的時間才會執行完畢,那就不會有問題
      * ![setinterval-ok](../assets/pics/nodejs/setinterval-ok.png)
    * 也有可能該函式每次會因為網路速度的情況(network conditions),花費不同的時間才會執行完畢
      * ![setinterval-varying-duration](../assets/pics/nodejs/setinterval-varying-duration.png)
    * 也因此只要任何一次需要花費比較長的時間才會執行完畢的回呼函式就會與下一次要執行的時間 **重疊** (overlaps)
      * ![setinterval-overlapping](../assets/pics/nodejs/setinterval-overlapping.png)
      * 為了避免上述 **重疊** 的情況,我們可以在回呼函式(callback function)執行完畢後,再安排一個遞迴計時器(recursive timeout)
          ``` js
          const myFunction = () => {
            // do something

            setTimeout(myFunction, 1000)
          }

          setTimeout(myFunction, 1000)
          ```
      * 當達成(achieve)這個遞迴計時器(recursive timeout)的情境(scenario),會如下圖所示
        * ![recursive-settimeout](../assets/pics/nodejs/recursive-settimeout.png)
- `setTimeout()`與`setInterval()`皆為Node的內建核心模組[Timers](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_timers)的方法
  + Node也提供`setImmediate()`方法,這個方法就相當於使用`setTimeout(() => {}, 0)`,主要用來搭配Node的事件迴圈(event loop)使用

### JavaScript Asynchronous Programming and Callbacks
- 程式語言的非同步性(asynchronicity)
  + 電腦都是設計可以非同步的,非同步(asynchronous)意味著事件可以獨立於(independently)主程式流(the main program flow)發生(happen)
  + 在當前的消費者電腦(consumer computers)中,每個程式都會運行一個特定的時間段(time slot)後,然後停止運行,以讓其它程式可以繼續執行。這個循環運行得很快,以至於我們通常不會注意到,我們會以為是我們的電腦能同時(simultaneously)執行多個程式,事實上這只是一個幻覺(illusion)
  + 我們這邊不會深入探討這個觀念,但是我們必須要把這個觀念視為一個正常的情況,也就是程式可以是非同步(asynchronous)執行的,直到他們需要被注意時才會停止(halt),這也能讓電腦能同時(in the meantime)執行其它事情
    * 當程式正在等待來自網路的回應(response from the network)時,該程式是無法在請求完成之前停止(halt)處理器(processor)的
  + 正常來說,程式語言是同步的(synchronous),其中有些程式語言有提供方法來管理非同步性(asynchronicity)或是透過函式庫(library)來處理
    * 預設情況下,`C`、`Java`、`C#`、`PHP`、`Go`、`Ruby`、`Swift`、`Python`都是非同步(asynchronous)的
    * 其中一些程式語言 **透過線程(thread)來處理非同步(async)行為,從而產生(spawning)一個新的進程(process)**
- Javascript
  + **Javascript程式語言,預設是同步(synchronous)且單執行緒(single threaded)的** 。這意味著程式碼不能創造新的線程(threads),並平行(parallel)執行
    ``` js
    const a = 1
    const b = 2
    const c = a * b
    console.log(c)
    doSomething()
    ```
    * 雖然以上的範例程式碼"看起來"是依序執行的,但是Javascript這個程式語言,起初是誕生於瀏覽器(browser)的,它一開始的工作是用來回應(response)使用者的行為(user actions),像是`onClick`、`onMouseOver`、`OnChange`、`OnSubmit`...等等。該如何使用同步程式設計模型(synchronous programming model)來做到這些事情呢?
    * 答案是它的"環境"(environment)。瀏覽器(browser)會透過提供一組能夠處理這種功能的APIs來完成這一點
    * 最近,Node引進(introduced)了一個非阻塞I/O環境(non-blocking I/O environment)來將該概念(concept)延伸(extend)到文件存取(file access)、網路呼叫(network calls)...等等
- 回呼函式(Callbacks)
  + 因為我們無法得知使用者何時會點擊按鈕,所以我們可以對該點擊事件(click event)定義(define)一個事件處理器(event handler)。這個事件處理器(event handler)會接受(accepts)一個函式(function),而這個函式會在該事件被觸發(triggered)時呼叫
      ``` js
      document.getElementById('button').addEventListener('click', () => {
        //item clicked
      })
      ```
    * 而這也就是所謂的"回呼函式"(callback)
  + **回呼函式(callback function)是一個簡單的函式,能夠作為值傳遞給另一個函式,並且僅在事件被觸發時才會執行**
    * 我們可以這樣做的原因是,在Javascript這個程式語言中,有一級函式(first-class functions)這種東西,也就是函式能作為值並指派給變數,再傳遞給其他函式(=> 這種函式被稱作"高階函式", (higher-order functions))
    * 常見的做法是將客戶端的程式碼包裝在`window`物件的`load`事件監聽器(event listener),它只會在頁面準備好時才會執行回呼函式
        ``` js
        window.addEventListener('load', () => {
          //window loaded
          //do what you want
        })
        ```
    * 回呼函式(Callbacks)還有用在很多地方,而不只是在`DOM`事件中
    * 另一個常見的做法是使用計時器(timers)
        ``` js
        setTimeout(() => {
          // runs after 2 seconds
        }, 2000)
        ```
    * 另外,`XHR`請求(requests)也能接受回呼函式(callback function),在以下的範例程式碼中,透過指定一個函式給當一個特定的事件(particular event)發生(occur)時會呼叫的屬性(property)
      * 在這次的範例中,所謂特定的事件就是代表請求的狀態發生改變時(the state of the request changes)
        ``` js
        const xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
          if (xhr.readyState === 4) {
            xhr.status === 200 ? console.log(xhr.responseText) : console.error('error')
          }
        }
        xhr.open('GET', 'https://yoursite.com')
        xhr.send()
        ```
- 在回呼函式中處理錯誤(Handling errors in callbacks)
  + 我們平常會如何處理(handle)回呼函式(callbacks)的錯誤(errors)呢?
    * 一個很常見的策略(strategy)是利用Node所採取的方法,也就是在任何一個回呼函式中的第一個參數(first parameter)就是錯誤(`error`)物件: **error-first callbacks**
    * 如果沒有發生錯誤(`error`)的話,該物件的值就是`null`; 而如果有發生錯誤(`error`)的話,該錯誤(`error`)物件就會包括關於這個錯誤(`error`)的一些描述(description)以及其他資訊(information)
      ``` js
      fs.readFile('/file.json', (err, data) => {
        if (err !== null) {
          //handle error
          console.log(err)
          return
        }

        //no errors, process data
        console.log(data)
      })
      ```
- 回呼函式的問題(The problem with callbacks)
  + 回呼函式(Callbacks)很適合用於簡單的情境中
  + 然而每一個回呼函式都會增加一層巢狀(nesting),這時候如果我們有很多的回呼函式(callbacks),程式碼很快地就會開始變得複雜
      ``` js
      window.addEventListener('load', () => {
        document.getElementById('button').addEventListener('click', () => {
          setTimeout(() => {
            items.forEach(item => {
              //your code here
            })
          }, 2000)
        })
      })
      ```
      * 以上的範例程式碼只是4層的程式碼,但是可能會有更多層的程式碼,這樣就不好玩了
      * 那我們該怎麼解決這個問題呢?
- 回呼函式的替代方案(Alternatives to callbacks)
  + **從ES6開始** ,Javascript程式語言開始引進(introduced)了許多能夠不涉及(do not involve)使用回呼函式(callbacks)來處理非同步(asynchronous)程式碼的功能,例如以下2種功能
    * `Promise`物件 (ES6)
    * `Async/Await`語法 (ES8)

### Understanding JavaScript Promises
> [Promise物件 (by MDN官方文件)](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise) --- The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value.<br>
> [如何使用Promise物件 (by MDN官方文件)](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Using_promises)<br>
> [Fetch API (by MDN官方文件)](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API) --- The Fetch API provides an interface for fetching resources (including across the network). It will seem familiar to anyone who has used XMLHttpRequest, but the new API provides a more powerful and flexible feature set.<br>

- Promise物件的介紹
    ``` js
    let done = true

    const isItDoneYet = new Promise((resolve, reject) => {
      if (done) {
        const workDone = 'Here is the thing I built'
        resolve(workDone)
      } else {
        const why = 'Still working on something else'
        reject(why)
      }
    })

    const checkIfItsDone = () => {
      isItDoneYet
        .then(ok => {
          console.log(ok)
        })
        .catch(err => {
          console.error(err)
        })
    }

    checkIfItsDone()
    ```
    * 以上的範例程式碼會打印出`Here is the thing I built`
  + **`Promise`物件通常會被定義為一個最終(eventually)將會變成一個可用的(available)代理值(a proxy for a value)**
  + `Promise`物件是一種能處理非同步(asynchronous)程式碼的方法,而不用陷入回呼地獄(callback hell)
    * 可參考[callback hell](http://callbackhell.com/)
  + 自從2015年(ES6的標準化與引入)開始後,`Promise`物件已經成為Javascript程式語言的一部分了,並且在近年來更與ES8的`Async/Await`語法整合得更好
    * 非同步函式(Async functions)會在背景中(behind the scenes)使用`Promise`物件,所以了解`Promise`物件運作的原理 & 理解`Async/Await`語法是如何作用的是非常重要的
  + `Promise`物件簡單來說是如何運作的呢?
    * 一旦`Promise`物件被呼叫後,它將會以待處理狀態(pending state)開始。這意味著被呼叫的函式(calling function)將會繼續執行,然而`Promise`物件仍然處於待處理狀態(`pending`),直到被解決(`resolves`)為止,進而將所請求到的任何數據傳遞給其呼叫的函式(calling function)
    * 建立出來的`Promise`物件最終(eventually)將會 **已解決的狀態(resolved state)** 或是 **被拒絕的狀態(rejected state)** 結束(end in),並呼叫各自對應的(respective)回呼函式(callback functions),再傳遞給`then()`與`catch()`之後就會立即結束(upon finishing)
  + 有哪些Javascript的APIs會使用到`Promise`物件呢?
    * 在我們自己的程式碼或是函式庫(library)的程式碼中,標準的現代化Web APIs也會使用到`Promise`物件,像是以下3種Web APIs
      * [the Battery API](https://developer.mozilla.org/zh-TW/docs/Web/API/Battery_Status_API)
      * [the Fetch API](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API/Using_Fetch)
      * [Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker)
    * 在現代化的Javascript中,我們不太可能會發現自己沒有在使用`Promise`物件,所以讓我們開始深入鑽研它吧!
- 創造一個承諾物件
  + `Promise`物件API會公開(exposes)一個建構子(constructor),我們可以使用`new Promise()`的語法來初始化一個`Promise`物件
      ``` js
      let done = true

      const isItDoneYet = new Promise((resolve, reject) => {
        if (done) {
          const workDone = 'Here is the thing I built'
          resolve(workDone)
        } else {
          const why = 'Still working on something else'
          reject(why)
        }
      })
      ```
      * 如我們所見,這個`Promise`物件會檢查全域的`done`常數(global constant),而如果`done`這個全域常數的值為`true`的話,則`Promise`物件就會進入已解決的狀態(resolved state) (由於`resolve`回呼函式已經被呼叫); 否則,`reject`回呼函式就會被執行,並將`Promise`物件變成被拒絕的狀態(rejected state)
        * 而如果上述的2種函式(`resolve` callback function & `reject` callback function)都沒有被呼叫的話,`Promise`物件就會維持為待處理狀態(`pending` state)
      * 我們可以利用`resolve`與`reject`兩個語法來將`Promise`物件的結果狀態(resulting state) & 將如何處理的方法 回傳給呼叫方(caller)。在上述的範例程式碼中,我們僅回傳一個字串(string),但其實也可以回傳一個物件(object)或甚至是`null`
      * 因為我們已經在上述的範例程式碼摘錄(snippet)中,建立了一個`Promise`物件,**因此它已經開始執行了**。這將會有助於我們了解以下的"如何使用`Promise`物件"章節
  + 另一個我們可能會碰到的更常見做法是一種被稱為 **Promisifying** 的技術。這種技術是一個能夠使用古典的Javascript函式來接收(takes)一個回呼函式(callback),並使其回傳一個`Promise`物件
    ``` js
    const fs = require('fs')

    const getFile = (fileName) => {
      return new Promise((resolve, reject) => {
        fs.readFile(fileName, (err, data) => {
          if (err) {
            reject (err)  // calling `reject` will cause the promise to fail with or without the error passed as an argument
            return        // and we don't want to go any further
          }
          resolve(data)
        })
      })
    }

    getFile('/etc/passwd')
    .then(data => console.log(data))
    .catch(err => console.error(err))
    ```
  + 補充: 在最近的Node版本中,我們不需要為許多的API進行手動轉換(manual conversion)。假設我們要使用的 **Promisifying** 技術有正確的簽名(correct signature)的話,在Node的內建核心模組`Util`中有一個方法叫做[util.promisify(original)](https://nodejs.org/docs/latest-v11.x/api/util.html#util_util_promisify_original)可以使用,來幫助我們實現 **Promisifying** 技術
- 使用`Promise`物件
  + 在上一個章節中,我們介紹了一個`Promise`物件是如何被建立的?
  + 現在,我們要來看一個`Promise`物件是如何被使用的(used)
  ``` js
  const isItDoneYet = new Promise(/* ... as above ... */)
  //...

  //no errors, process data
  console.log(data)
  ```
- 回呼函式的問題(The problem with callbacks)
  + 回呼函式(Callbacks)很適合用於簡單的情境中
  + 然而每一個回呼函式都會增加一層巢狀(nesting),這時候如果我們有很多的回呼函式(callbacks),程式碼很快地就會開始變得複雜
    ``` js
    window.addEventListener('load', () => {
      document.getElementById('button').addEventListener('click', () => {
        setTimeout(() => {
          items.forEach(item => {
            //your code here
          })
        }, 2000)
      })
    })
    ```
      * 以上的範例程式碼只是4層的程式碼,但是可能會有更多層的程式碼,這樣就不好玩了
      * 那我們該怎麼解決這個問題呢?
- 回呼函式的替代方案(Alternatives to callbacks)
  + **從ES6開始** ,Javascript程式語言開始引進(introduced)了許多能夠不涉及(do not involve)使用回呼函式(callbacks)來處理非同步(asynchronous)程式碼的功能,例如以下2種功能
    * `Promise`物件 (ES6)
    * `Async/Await`語法 (ES8)
      ``` js
      const checkIfItsDone = () => {
        isItDoneYet
          .then(ok => {
            console.log(ok)
          })
          .catch(err => {
            console.error(err)
          })
      }
      ```
    * 執行`checkIfItsDone()`方法將會指定(specify),當`isItDoneYet`這個`Promise`物件在
      * 已解決的狀態(resolves)時,會執行`then()`方法裡面的函式
      * 或是
      * 被拒絕的狀態(rejects)時,會執行`catch()`方法裡面的函式
- 連鎖`Promise`物件(Chaining promises)
  + 一個`Promise`物件可以被回傳給另一個`Promise`物件,來創造出一串連鎖`Promise`物件(a chain of promises)
  + [Fetch API](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API)就是一個很好的連鎖`Promise`物件的例子。`Fetch API`能讓我們獲取資源(get a resource)並且當資源被獲取(fetched)時,將連鎖`Promise`物件(a chain of promises)排隊(queue)後執行(execute)
    * `Fetch API`是一個基於`Promise`物件的機制(mechanism),呼叫`fetch()`方法相當於透過`new Promise()`建構子來建立出一個屬於我們自定義的`Promise`物件
      ``` js
      const status = response => {
        if (response.status >= 200 && response.status < 300) {
          return Promise.resolve(response)
        }
        return Promise.reject(new Error(response.statusText))
      }

      const json = response => response.json()

      fetch('/todos.json')
        .then(status)    // note that the `status` function is actually **called** here, and that it **returns a promise**
        .then(json)      // likewise, the only difference here is that the `json` function here returns a promise that resolves with `data`
        .then(data => {  // ... which is why `data` shows up here as the first parameter to the anonymous function
          console.log('Request succeeded with JSON response', data)
        })
        .catch(error => {
          console.log('Request failed', error)
        })
      ```
      * 在以上的範例程式碼中,我們呼叫了`fetch()`方法,從根目錄中的`todos.json`這個檔案中來獲得一個待辦清單(a list of TODO items),接著我們就建立出一串連鎖`Promise`物件(a chain of promises)
      * 執行`fetch()`方法會回傳一個[response](https://fetch.spec.whatwg.org/#concept-response)物件,它具有許多屬性(properties),我們有參考(reference)的屬性有以下2種
        * `reponse.status`: 一個代表HTTP狀態碼(status code)的數值(numeric value)
        * `response.statusText`: 一個狀態訊息,如果是`'OK'`則表示該請求成功(request succeeded)
      * 自定義的: `response`物件也有`response.json()`方法,可以回傳一個`Promise`物件,這個方法可以用來處理內文(the content of the body),並轉換成`JSON`格式
      * 所以考慮到這些前提(premises),就會發生這種情況,一串連鎖`Promise`物件中的第一個`Promise`物件是我們定義的`status()`函式,該函式用來檢查回應的狀態(reponse status),若這個回應狀態碼不是成功的(= `200`~`299`),該函式就會拒絕(rejects)這個`Promise`物件
        * 此操作(operation)將會導致整個該`Promise`物件會跳過列出的(listed)所有連鎖`Promise`物件鏈,並直接跳到下面的`catch()`陳述句,該函式會記錄請求失敗(`Request failed`)的文本(text) & 錯誤訊息(error message)
      * 如果我們自定義的`status()`函式回傳的回應狀態碼是成功的話,它會呼叫我們自定義的`repsonse.json()`函式。由於前一個(previous)`Promise`物件在請求成功(successful)後會回傳一個`response`物件,我們會將這個`response`物件作為下一個`Promise`物件的輸入(input)
      * 在以上的範例程式碼中,我們會回傳一個經過`response.json()`函式處理過(`JSON` processed)的資料(data),因此第三個`Promise`物件就能直接收到(receives)這個`JSON`物件
        * ``` js
            .then((data) => {
              console.log('Request succeeded with JSON response', data)
            })
          ```
        * 接下來,我們只要將這個記錄到控制台(console)即可
- 處理錯誤(Handling errors)
  + 在上一節的範例中,我們在連鎖`Promise`物件鏈中附加了`catch()`函式
      ``` js
      .catch(error => {
        console.log('Request failed', error)
      })
      ```
  + 如果在整個連鎖`Promise`物件鏈中有任何的失敗(fails)並引起(raises)一個錯誤(error)或是被拒絕的`Promise`物件,程式碼的流程控制權(control)將會轉移到整個連鎖`Promise`物件鏈下面且最近的(nearest)`catch()`陳述句
      ``` js
      new Promise((resolve, reject) => {
        throw new Error('Error')
      }).catch(err => {
        console.error(err)
      })

      // or

      new Promise((resolve, reject) => {
        reject('Error')
      }).catch(err => {
        console.error(err)
      })
      ```
  + 串鏈錯誤(Cascading errors)
    * 如果在`catch()`陳述句裡面,又引起(raises)了一個錯誤(error),我們可以再附加第2個`catch()`函式來處理該錯誤(error),以此類推
      ``` js
      new Promise((resolve, reject) => {
        throw new Error('Error')
      })
        .catch(err => {
          throw new Error('Error')
        })
        .catch(err => {
          console.error(err)
        })
      ```
- 編排`Promise`物件(Orchestrating promises)
  + [Promise.all(iterable)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)
    * **如果我們想要同步化(synchronize)不同的`Promise`物件,`Promise.all()`方法可以定義一個`Promise`物件的清單(list),並在全部的`Promise`物件都被解決(all resolved)後,執行一些操作**
        ``` js
        const f1 = fetch('/something.json')
        const f2 = fetch('/something2.json')

        Promise.all([f1, f2])
          .then(res => {
            console.log('Array of results', res)
          })
          .catch(err => {
            console.error(err)
          })
        ```
    * 我們可以使用ES6(= ES2015)引進的解構指派語法(destructuring assignment syntax)來這樣做
        ``` js
        Promise.all([f1, f2]).then(([res1, res2]) => {
          console.log('Results', res1, res2)
        })
        ```
    * 當然,我們也可以使用[fetch()](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API/Using_Fetch) API來實作, **任何`Promise`物件都可以使用`Promise.all()`方法**
  + [Promise.race(iterable)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)
    * **當我們將傳遞給`Promise.race()`方法的第1個`Promise`物件被解決(resolved)時,會執行該方法,並且它只會執行其附加的回呼函式(attached callback)"一次",並解決第1個`Promise`物件的結果**
        ``` js
        const first = new Promise((resolve, reject) => {
          setTimeout(resolve, 500, 'first')
        })
        const second = new Promise((resolve, reject) => {
          setTimeout(resolve, 100, 'second')
        })

        Promise.race([first, second]).then(result => {
          console.log(result) // second
        })
        ```
- 常見的錯誤(Common errors)
  + 未捕獲的型別錯誤: `undefined`不是一個`Promise`物件(Uncaught TypeError: undefined is not a promise)
    * 如果在控制台(console)得到此錯誤(error)時,可以去確認一下我們是使用`new Promise()`語法來建立一個新的`Promise`物件,而"不是"使用`Promise()`語法
  + 未處理的被拒絕的`Promise`物件警告(UnhandledPromiseRejectionWarning)
    * 這個錯誤表示我們呼叫的`Promise`物件被拒絕(rejected)了,但這時候找不到任何一個`catch()`陳述式來處理這個錯誤(error)。這時,我們可以新增一個`catch()`陳述式在引起問題(offending)的`then()`方法的後面來適當地(properly)處理這個錯誤(error)

### Modern Asynchronous JavaScript with Async and Await
- 介紹
  + Javascript在很短的時間內從回呼函式(callbacks)發展成`Promise`物件(=> 從ES6引入的),再演變成`async/await`語法(=> 從ES8引入的),來使Javascript的非同步(asynchronous)語法更簡化了
  + 非同步函式(Async functions)是由`Promise`物件們 & 生成器(generators)的結合(combination)。基本上,它們算是一種更高階的`Promise`物件的抽象(abstraction)
  + 讓我再重複一次,**`async/await`語法是建立在`Promise`物件的基礎之上的**
- 為什麼`async/await`語法會被引進(introduced)呢?
  + `async/await`語法能減少關於`Promise`物件的樣板(boilerplate),並且可以打破(break) "不能破壞`Promise`物件鏈" 的這個限制(limitation)
  + 當`Promise`物件在ES6開始引入(introduced)時,它們旨在解決非同步程式碼(asynchronous code)的問題,當然`Promise`物件有完成這個目標了。但是在接下來的2年內(=> ES6~ES8),`Promise`物件很明顯地,不能作為最後的解決方案(final solution)
  + `Promise`物件的引入(introduced)是用來解決(solve)著名的回呼地獄(callback hell)問題。但是`Promise`物件自己本身也引入了複雜性與語法複雜性(syntax complexity)
    * 它們是很好的原語(primitives),可以向開發人員公開更好的語法。因此,當時機合適時,我們就能開始使用`async/await`語法
  + `async/await`語法能讓程式碼"看起來"是同步的(synchronous), **但是在背景中(behind the scenes)它們是非同步的(asynchronous)且非阻塞的(non-blocking)**
- 這是如何運作的呢?
  + 一個非同步函式(async function)會回傳一個`Promise`物件,如同以下的範例程式碼
    ``` js
    const doSomethingAsync = () => {
      return new Promise(resolve => {
        setTimeout(() => resolve('I did something'), 3000)
      })
    }
    ```
    * 如果我們想要呼叫以上的範例函式的話,就要前置(prepend) `await`語法來呼叫,並且呼叫的程式碼會直到當該`Promise`物件被解決(resolved)或是被拒絕(rejected)後才會停止
  + **警告(caveat): 客戶端函式(client function)必須定義為(defined as)非同步函式(async function)**
      ``` js
      const doSomething = async () => {
        console.log(await doSomethingAsync())
      }
      ```
- 一個快速的範例
  + 以下是一個關於`async/await`語法的快速範例,以非同步(asynchronous)的方式來執行函式(function)
    ``` js
    const doSomethingAsync = () => {
      return new Promise(resolve => {
        setTimeout(() => resolve('I did something'), 3000)
      })
    }

    const doSomething = async () => {
      console.log(await doSomethingAsync())
    }

    console.log('Before')
    doSomething()
    console.log('After')
    ```
    * 以上的範例程式碼會"依序"回傳
      * Before
      * After
      * I did something
- Promise all the things
  + 在任何函式前置(prepending) `async`關鍵字(keyword)表示該函式(function)會回傳一個`Promise`物件
    * 即使`async function`看起來沒有明確地這麼做,但是在其內部(internally)會回傳一個`Promise`物件
  + 這也就是為什麼以下的這段範例程式碼是有效的(valid)
    ``` js
    const aFunction = async () => {
      return 'test'
    }

    aFunction().then(alert) // This will alert 'test'
    ```
    * 以上的範例程式碼也相當於下面這段範例程式碼
      ``` js
      const aFunction = () => {
        return Promise.resolve('test')
      }

      aFunction().then(alert) // This will alert 'test'
      ```
- 程式碼也會變得更容易閱讀
  + 如同上一個章節的範例程式碼,這次的範例程式碼看起來非常簡單。從程式碼的比較中可以看出,使用`Promise`物件,並搭配`Promise`物件鏈與回呼函式(callback functions)可以讓程式碼變得更簡潔
  + 以下是2個非常簡單的範例程式碼的比較,當程式碼開始變得複雜的時候,使用`async/await`語法能帶來很多好處
    * 這是利用`Promise`物件獲得一個`JSON`資源,解析(parse)它
      ``` js
      const getFirstUserData = () => {
        return fetch('/users.json') // get users list
          .then(response => response.json()) // parse JSON
          .then(users => users[0]) // pick first user
          .then(user => fetch(`/users/${user.name}`)) // get user data
          .then(userResponse => userResponse.json()) // parse JSON
      }

      getFirstUserData()
      ```
    * 這是利用`async/await`語法提供的相同功能
      ``` js
      const getFirstUserData = async () => {
        const response = await fetch('/users.json') // get users list
        const users = await response.json() // parse JSON
        const user = users[0] // pick first user
        const userResponse = await fetch(`/users/${user.name}`) // get user data
        const userData = await userResponse.json() // parse JSON
        return userData
      }

      getFirstUserData()
      ```
- 多個非同步函式的串連(Multiple async functions in series)
  + 非同步函式(async function)可以很簡單地被串連,並且語法上比起單純的(plain)`Promise`物件的語法更容易閱讀(much more readable)
    ``` js
    const promiseToDoSomething = () => {
      return new Promise(resolve => {
        setTimeout(() => resolve('I did something'), 10000)
      })
    }

    const watchOverSomeoneDoingSomething = async () => {
      const something = await promiseToDoSomething()
      return something + '\nand I watched'
    }

    const watchOverSomeoneWatchingSomeoneDoingSomething = async () => {
      const something = await watchOverSomeoneDoingSomething()
      return something + '\nand I watched as well'
    }

    watchOverSomeoneWatchingSomeoneDoingSomething().then(res => {
      console.log(res)
    })
    ```
    * 以上的範例程式碼會"依序"回傳
      * I did something
      * and I watched
      * and I watched as well
- 更容易地除錯(Easier debugging)
  + 要除錯(debugging)`Promise`物件是困難的,因為除錯器(debugger)不會跳過(step over)非同步程式碼(asynchronous code)
  + `async/await`語法讓除錯(debug)變得更容易,因為對於編譯器(compiler)來說,這就像是同步程式碼(synchronous code)

### The Node.js Event emitter
> Node內建核心模組[Events](https://nodejs.org/api/events.html#events_events)<br>

- 如果我們在瀏覽器使用Javascript工作,就會知道透過多少事件(through events)來跟使用者進行互動(interaction),像是`mouse click`, `keyboard button presses`, `reacting to mouse movements`, ...等等
- 在後端(backend),Node為我們提供(offer)了利用`Events`內建核心模組來建構出一個類似的系統(similar system)的選項(option)
  + 特別的是,這個模組提供了一個[EventEmitter Class](https://nodejs.org/api/events.html#events_class_eventemitter)類別,用來處理(handle)我們的事件(events)
- 我們初始化可以這樣使用
    ``` js
    const EventEmitter = require('events')
    const eventEmitter = new EventEmitter()
    ```
    * 這樣做的話,該`EventEmitter`物件,除了其他的東西之外,我們先介紹`eventEmitter.on()`方法與`eventEmitter.emit()`方法
      * [emitter.emit(eventName[, ...args])](https://nodejs.org/api/events.html#events_emitter_emit_eventname_args)方法: 可以被用來觸發(trigger)一個事件(event)
      * [emitter.on(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_on_eventname_listener)方法: 可以被用來新增作為,當事件(event)被觸發(triggered)時,要執行的回呼函式(callback function)
  + 舉個例子來說,我們建立一個`start`事件(event),作為一個範例而言,我們對該事件的反應(react)就是把字串記錄(logging)到控制台(console)上
      ``` js
      eventEmitter.on('start', () => {
        console.log('started')
      })
      ```
  + 這時,當我們執行
      ``` js
      eventEmitter.emit('start')
      ```
    * 該事件的處理函式(the event handler function )就會被觸發(triggered),並且我們會得到控制台的記錄(console log)
    * 我們將以上的東西作為額外的參數(additional arguments),傳遞(passing)給事件處理器(event handler),也就是指傳遞給`emit()`方法
        ``` js
        eventEmitter.on('start', number => {
          console.log(`started ${number}`)
        })

        eventEmitter.emit('start', 23)
        ```
      * 多個參數(multiple arguments)的情況
        ``` js
        eventEmitter.on('start', (start, end) => {
          console.log(`started from ${start} to ${end}`)
        })

        eventEmitter.emit('start', 1, 100)
        ```
- `EventEmitter`物件(object)也公開(exposes)了許多其它的方法(method),來跟事件(events)做互動(interact),像是以下3種方法
  + [emitter.once(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_once_eventname_listener): 新增(add)一個一次性(one-time)的事件監聽器(listener)
  + [emitter.removeListener(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_removelistener_eventname_listener): 從指定的事件中移除指定的事件監聽器(remove an event listener from an event)
    * 等同於[emitter.off(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_off_eventname_listener)
  + [emitter.removeAllListeners([eventName])](https://nodejs.org/api/events.html#events_emitter_removealllisteners_eventname): 刪除指定的事件中所有的事件監聽器(remove all listeners for an event)

### Build an HTTP Server
> Node內建核心模組[HTTP](https://nodejs.org/api/http.html#http_http)<br>

- 這是一個Hello World範例的HTTP web server
    ``` js
    const http = require('http')

    const port = process.env.PORT

    const server = http.createServer((req, res) => {
      res.statusCode = 200
      res.setHeader('Content-Type', 'text/html')
      res.end('<h1>Hello, World!</h1>')
    })

    server.listen(port, () => {
      console.log(`Server running at port ${port}`)
    })
    ```
    * 以上的範例程式碼會打印出`Hello, World!`字串
  + 讓我們來簡單地分析一下這段範例程式碼,包括Node的`HTTP`內建核心模組
    * 我們可以利用`HTTP`模組(module)來建立一個HTTP 伺服器(server)
    * 這個`HTTP server`被設定用來監聽指定的`3000` port。當伺服器準備好後,就會呼叫[server.listen()](https://nodejs.org/api/http.html#http_server_listen)這個回呼函式(callback function)
    * 我們傳遞的回呼函式是將在收到每個請求(every request)時,立即(upon)要執行的回呼函式(callback function)
    * 每當(whenever)收到(received)一個新的請求(new request)時,就會呼叫[request事件(event)](https://nodejs.org/api/http.html#http_event_request),並提供2個物件(objects)
      * 請求物件(request): 會是一個[http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)物件
        * 該物件會提供關於請求的細節(request details)。透過這個物件,我們可以用來存取(access)請求標頭(request headers)與請求資料(request data)
      * 回應物件(response): 會是一個[http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)物件
        * 該物件是被用來產生(populate)出我們將要回傳給客戶端(client)的資料(data)
    * 在這次的範例程式碼中,我們有設定了回應物件(response)的狀態碼(statusCode)的屬性(property)值為`200`,來表示這是一個成功的請求(successful response)
        ``` js
        res.statusCode = 200
        ```
    * 在這次的範例程式碼中,我們也有設定了回應物件(response)的內文標題形式(`Content-Type header`)
        ``` js
        res.setHeader('Content-Type', 'text/plain')
        ```
    * 在這次範例程式碼的結尾,我們也關閉了回應物件(response),並將內容作為參數(argument)傳遞給`response.end()`方法
        ``` js
        res.end('Hello World\n')
        ```

### Making HTTP requests with Node.js
> Node內建核心模組[HTTPS](https://nodejs.org/dist/latest-v15.x/docs/api/https.html#https_https)<br>

- 完成一個`GET`請求(Perform a `GET` Request)
    ``` js
    const https = require('https')
    const options = {
      hostname: 'whatever.com',
      port: 443,
      path: '/todos',
      method: 'GET'  // 完成一個GET請求
    }

    const req = https.request(options, res => {
      console.log(`statusCode: ${res.statusCode}`)

      res.on('data', d => {
        process.stdout.write(d)
      })
    })

    req.on('error', error => {
      console.error(error)
    })

    req.end()
    ```
- 完成一個`POST`請求(Perform a `POST` Request)
    ``` js
    const https = require('https')

    const data = JSON.stringify({
      todo: 'Buy the milk'
    })

    const options = {
      hostname: 'whatever.com',
      port: 443,
      path: '/todos',
      method: 'POST',  // 完成一個POST請求 
      headers: {
        'Content-Type': 'application/json',
        'Content-Length': data.length
      }
    }

    const req = https.request(options, res => {
      console.log(`statusCode: ${res.statusCode}`)

      res.on('data', d => {
        process.stdout.write(d)
      })
    })

    req.on('error', error => {
      console.error(error)
    })

    req.write(data)
    req.end()
    ```
- 完成一個`PUT`與`DELETE`請求(`PUT` and `DELETE`)
  + `PUT`與`DELETE`請求跟`POST`請求(request)的格式(format)相同,只要修改`options.method`這個屬性值就可以用了

### Make an HTTP POST request using Node.js
> Node內建核心模組[HTTP](https://nodejs.org/api/http.html#http_http)<br>
> [Axios套件](https://github.com/axios/axios)-Make http requests from node.js<br>

- 根據(depending on)我們想要使用的抽象層級(abstraction level),有多種方法可以完成一個`HTTP`的`POST`請求(request)
- 最簡單的方式來完成一個`HTTP`請求(request)就是利用Node來搭配使用[Axios套件](https://github.com/axios/axios)
    ``` js
    const axios = require('axios')

    axios
      .post('https://whatever.com/todos', {
        todo: 'Buy the milk'
      })
      .then(res => {
        console.log(`statusCode: ${res.statusCode}`)
        console.log(res)
      })
      .catch(error => {
        console.error(error)
      })
    ```
  + `axios`套件要求(require)使用第三方函式庫(3rd party library)
- 其實一個`POST`請求(request)僅單純使用Node的內建標準函式庫(standard modules)是可行的,但會比先前(preceding)的2個選項(options)來的冗長(verbose)
    ``` js
    const https = require('https')

    const data = JSON.stringify({
      todo: 'Buy the milk'
    })

    const options = {
      hostname: 'whatever.com',
      port: 443,
      path: '/todos',
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Content-Length': data.length
      }
    }

    const req = https.request(options, res => {
      console.log(`statusCode: ${res.statusCode}`)

      res.on('data', d => {
        process.stdout.write(d)
      })
    })

    req.on('error', error => {
      console.error(error)
    })

    req.write(data)
    req.end()
    ```

### Get HTTP request body data using Node.js
> [Express框架](https://expressjs.com/)<br>

- 這是以`JSON`格式來提取(extract)請求內文(request body)的資料(data)的方式
  + 如果我們正在使用[Express框架](https://expressjs.com/),那很簡單,只要使用Node的`body-parser`模組
  + 舉例來說,假如我們想要獲得請求的內文(request body)
      ``` js
      const axios = require('axios')

      axios.post('https://whatever.com/todos', {
        todo: 'Buy the milk'
      })
      ```
  + 這會與伺服器端的程式碼(server-side code)相對應(matching)
      ``` js
      const express = require('express')
      const app = express()

      app.use(
        express.urlencoded({
          extended: true
        })
      )

      app.use(express.json())

      app.post('/todos', (req, res) => {
        console.log(req.body.todo)
      })
      ```
  + 如果我們沒有在使用`Express`框架,只想要使用普通的Node來完成此操作的話,那麼就會需要做更多的工作。當然,這是因為`Express`框架已經幫我們抽象(abstracts)了很多此類的東西了
    * 要理解的關鍵是,當我們利用`http.creatServer()`方法來初始化一個`HTTP`伺服器(server)的話,這時當伺服器獲得(get)所有的`HTTP`請求標頭(request headers)時,而不是請求正文(request body)
      * 請求物件(request object)傳遞給回呼函式(callback)的關係(connection)是一種串流(stream)
      * 因此我們必須監聽(listen)要處理(processed)的請求正文的內容(body content),並且這個請求正文的內容是成塊處理的(processed in chunks)
    * 首先,我們會透過監聽(listening)`data`這個事件流(event stream),然後當`data`這個事件流結束(`data` ends)時,就會呼叫`end`這個事件流(event stream)一次(once)
        ``` js
        const server = http.createServer((req, res) => {
          // we can access HTTP headers
          req.on('data', chunk => {
            console.log(`Data chunk available: ${chunk}`)
          })
          req.on('end', () => {
            //end of data
          })
        })
        ```
    * 因此,為了要存取資料(access data),假設我們預期(expect)會收到(receive)一個字串(string),我們必須將其放入(put into)陣列(array)中
        ``` js
        const server = http.createServer((req, res) => {
          let data = '';
          req.on('data', chunk => {
            data += chunk;
          })
          req.on('end', () => {
            JSON.parse(data).todo // 'Buy the milk'
          })
        })
        ```

### Working with file descriptors in Node.js
> Node內建核心模組[File system](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_file_system)<br>

- 在我們能夠代理(sits in)檔案系統(file system)來與檔案(file)互動(interact)之前,我們必須先獲得一個檔案描述符號(file descriptor)
- 一個檔案描述符號(file descriptor)就是透過Node內建的`File system`核心模組中的[fs.open()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_open_path_flags_mode_callback)方法來開啟檔案(opening the file)時,所回傳(returned)的東西
    ``` js
    const fs = require('fs')

    fs.open('/Users/joe/test.txt', 'r', (err, fd) => {
      //fd is our file descriptor
    })
    ```
    * 注意到在`fs.open()`方法所使用的第2個參數,也就是`r`參數。這個旗幟(flag)意味著(means)我們是用讀取模式(reading)開啟(open)這個檔案(file),其它可能會常用到的旗幟(flags)會有以下幾種
      * `r+`: 以讀取(reading)+寫入(writing)模式來開啟這個檔案
      * `w+`: 以讀取(reading)+寫入(writing)模式來開啟這個檔案,並且將串流(stream)放在(positioning)檔案的 **最前面**(beginning)。當該檔案不存在時,就會建立一份這個檔案
      * `a`: 以寫入(writing)模式來開啟這個檔案,並且將串流(stream)放在(positioning)檔案的 **最後面** (end)。當該檔案不存在時,就會建立一份這個檔案
      * `a+`: 以讀取(reading)+寫入(writing)模式來開啟這個檔案,並且將串流(stream)放在(positioning)檔案的 **最後面**(end)。當該檔案不存在時,就會建立一份這個檔案
- 我們也可以透過[fs.openSync()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_opensync_path_flags_mode)方法來開啟檔案,該方法會回傳(returns)一個檔案描述符號(file descriptor),而不是(instead of)提供(providing)一個回呼函式(callback)
    ``` js
    const fs = require('fs')

    try {
      const fd = fs.openSync('/Users/joe/test.txt', 'r')
    } catch (err) {
      console.error(err)
    }
    ```
    * 一旦(once)獲得那個檔案描述符號(file descriptor),我們便能運用任何(whatever)我們選擇使用的方式(way)來完成(perform)所有(all)需要(require)檔案描述符號(file descriptor)的操作(operations),像是呼叫(calling)`fs.open()`方法,以及許多其它用來跟檔案系統(file system)互動(interact with)的操作

### Node.js file stats
- 每個檔案(file)都會帶有(comes with)一組細節(a set of details),可提供給Node用來檢查(inspect)它們所使用
  + 特別是使用Node內建的`File system`核心模組中的[fs.stat(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_stat_path_options_callback)方法。我們呼叫這個方法時,可以傳遞(pass)一個檔案路徑(file path)作為參數,之後一旦Node獲得(get)檔案資訊的細節(file details)後,該方法就會呼叫(call)我們指定的回呼函式(callback function, 也就是該方法的`callback`參數),並且再提供給這個回呼函式以下的2個參數(parameters)
    * 一段錯誤訊息(an error message)
    * 檔案狀態(the file states)
    ``` js
    const fs = require('fs')
    fs.stat('/Users/joe/test.txt', (err, stats) => {
      if (err) {
        console.error(err)
        return
      }
      //we have access to the file stats in `stats`
    })
    ```
- Node也提供(provides)了一個同步地方法(sync method, 也就是[fs.statSync(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_statsync_path_options)方法),用來在直到(until)檔案狀態(file states)準備好(ready)之前,阻擋線程(block the thread)
    ``` js
    const fs = require('fs')
    try {
      const stats = fs.statSync('/Users/joe/test.txt')
    } catch (err) {
      console.error(err)
    }
    ```
  + 檔案資訊(file information)就會被包括(included in)在上述範例程式碼中的`stats`這個變數(variable)中。那麼,我們能從這個`stats`這個變數中提取(extract)出哪些資訊(information)呢?
    * 答案是非常多(a lot),包括(including)了
      * 如果是一個資料夾或是檔案時,請使用[stats.isFile()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_stats_isfile)方法 & [stats.isDirectory()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_stats_isdirectory)方法
      * 如果是一個符號連結(symbolic link)時,請使用[stats.isSymbolicLink](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_stats_issymboliclink)方法
      * 如果是檔案大小(以`bytes`為單位)的話,請使用[stats.size](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_stats_size)屬性
    * 其實還有許多進階方法(advanced method),但是大部分(the bulk of)我們在日常(day-to-day)程式開發(programming)中只會使用到這些而已
        ``` js
        const fs = require('fs')
        fs.stat('/Users/joe/test.txt', (err, stats) => {
          if (err) {
            console.error(err)
            return
          }

          stats.isFile() //true
          stats.isDirectory() //false
          stats.isSymbolicLink() //false
          stats.size //1024000 //= 1MB
        })
        ```

### Node.js File Paths
> Node內建核心模組[Path](https://nodejs.org/dist/latest-v15.x/docs/api/path.html#path_path)<br>

- 系統(system)中的每個檔案(every file)都有一個路徑(path)
- 在`Linux`或是`macOS`作業系統(Unix-like operating system)時,檔案路徑看起來會像是以下這樣
  + `/users/joe/file.txt`
- 然而,`Windows`作業系統卻是不同的,它的檔案路徑結構(structure)會像是
  + `C:\users\joe\file.txt`
- 因此,我們會需要注意(pay attention),當我們在應用程式(application)中使用路徑(paths)時,需要將這個路徑結構的差異(difference)考慮進去(taken into account)
  + 我們可以將Node內建的`Path`這個核心模組,包括(include)到我們的檔案中,並且開始使用這個模組的相關方法(method)
    ``` js
    const path = require('path')
    ```
- 利用路徑來作為資訊(Getting information out of a path)
  + 假如(Given)給定一個路徑(path),我們能透過這個這個路徑來提取(extract)資訊,可以利用以下的幾種方法來完成
    * [path.dirname(path)](https://nodejs.org/dist/latest-v15.x/docs/api/path.html#path_path_dirname_path): 獲得(get)父目錄(parent folder)的檔案(file)
    * [path.basename(path[, ext])](): 獲得(get)該檔案名稱(filename part)
    * [path.extname(path)](https://nodejs.org/dist/latest-v15.x/docs/api/path.html#path_path_extname_path): 獲得(get)該檔案的副檔名(file extension)
    ``` js
    const notes = '/users/joe/notes.txt'

    path.dirname(notes) // /users/joe
    path.basename(notes) // notes.txt
    path.extname(notes) // .txt
    ```
  + 如果我們想要得到 **不包含** 副檔名(file extension)的檔案名稱(filename)的話,可以利用`path.basename()`方法,並給定這個方法的第二個參數`ext`的值,以過濾掉其副檔名
      ``` js
      path.basename(notes, path.extname(notes)) //notes
      ```
- 利用Node內建的`path`核心模組來完成工作(Working with paths)
  + 我們可以利用[path.join([...paths])](https://nodejs.org/dist/latest-v15.x/docs/api/path.html#path_path_join_paths)方法來將兩個或是多個路徑片段(parts of a path)組合起來
    ``` js
    const name = 'joe'
    path.join('/', 'users', name, 'notes.txt') //'/users/joe/notes.txt'
    ```
  + 我們可以利用[path.resolve([...paths])](https://nodejs.org/dist/latest-v15.x/docs/api/path.html#path_path_resolve_paths)方法來把相對路徑(relative path)計算(calculation)成為絕對路徑(absolute path)
    ``` js
    path.resolve('joe.txt') //'/Users/joe/joe.txt' if run from my home folder
    ```
    * 在這種情況下(In this case),Node會簡單地(simply)將`/joe.txt`這個檔案附加(append)到當前的工作目錄中(current working directory)。這時,如果我們指定了第2個目錄參數(second parameter folder),`path.resolve()`方法會將第1個目錄(first)參數作為基底(as a base)
      ``` js
      path.resolve('tmp', 'joe.txt') //'/Users/joe/tmp/joe.txt' if run from my home folder
      ```
    * 如果第1個參數是以斜線(slash, 也就是`/`)開頭(starts with)的話,那就代表該路徑是一個"絕對路徑"
  + [path.normalize(path)](https://nodejs.org/dist/latest-v15.x/docs/api/path.html#path_path_normalize_path)方法是另一種有用(useful)的函式(function)。當`path.normalize(path)`方法中的`path`參數值包含了`.`or`..`or`//`...等等時,該方法就會嘗試(try)去計算(calculate)出真實的路徑位置(actual path)
    ``` js
    path.normalize('/users/joe/..//test.txt') //'/users/test.txt'
    ```
- 提醒! `path.resolve([...paths])`方法與`path.normalize(path)`方法皆不會檢查其各自的`path`參數值是否存在。它們僅會(just)根據(based on)它們各自獲得的路徑參數資訊(information)來做計算(calculate)出一個路徑(path)的值而已

### Reading files with Node.js
> Node內建核心模組[File system](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_file_system)<br>

- 在Node中,要讀取一個檔案能使用的最簡單的方法是[fs.readFile(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readfile_path_options_callback)
,並傳遞給這個方法
  + 一個指定要讀取檔案的路徑位置(file path)
  + 文字編碼規則(encoding)
  + 一個將會與檔案資料(file data)一起被呼叫(called with)的回呼函式(callback function)與錯誤(`error`)
    ``` js
    const fs = require('fs')

    fs.readFile('/Users/joe/test.txt', 'utf8' , (err, data) => {
      if (err) {
        console.error(err)
        return
      }
      console.log(data)
    })
    ```
- 或者(Alternatively),我們也可以使用[fs.readFileSync(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readfilesync_path_options)這個同步化(synchronous)地讀取檔案的版本(version)
    ``` js
    const fs = require('fs')

    try {
      const data = fs.readFileSync('/Users/joe/test.txt', 'utf8')
      console.log(data)
    } catch (err) {
      console.error(err)
    }
    ```
- 以上的兩種讀取檔案的方法(`fs.readFile()`與`fs.readFileSync()`)都會在回傳資料(returning the data)之前,將整個檔案內容(full content of the file)讀取(read)到記憶體之中(in memory)
  + 這也就意味(means)著,大檔案將會對我們的記憶體消耗(memory consumption)與程式的執行速度(speed of execution of the program)帶來重大的影響(major impact)
    * 在這種情況下(In this case),更好的選擇(better option)是透過串流(`streams`)來讀取檔案內容(read the file content)

### Writing files with Node.js
> Node內建核心模組[File system](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_file_system)<br>

- 在Node中,要寫入檔案的最簡單的方法就是使用[fs.writeFile(file, data[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback)這個API
    ``` js
    const fs = require('fs')

    const content = 'Some content!'

    fs.writeFile('/Users/joe/test.txt', content, err => {
      if (err) {
        console.error(err)
        return
      }
      //file written successfully
    })
    ```
- 或者(Alternatively),我們也可以使用[fs.writeFileSync(file, data[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_writefilesync_file_data_options)這個同步化(synchronous)地寫入檔案的版本(version)
    ``` js
    const fs = require('fs')

    const content = 'Some content!'

    try {
      const data = fs.writeFileSync('/Users/joe/test.txt', content)
      //file written successfully
    } catch (err) {
      console.error(err)
    }
    ```
  + 預設情況(By default),當給定的指定路徑下的檔案已經存在(already exist)時,這個API就會取代掉這個檔案的內容(replace the contents of the file)
    * 我們可以透過指定(specifying)一個旗幟(flag)來修改(modify)這個預設模式(the default)
      ``` js
      fs.writeFile('/Users/joe/test.txt', content, { flag: 'a+' }, err => {})
      ``` 
    * 以下是我們可能會用到的旗幟(flags)
      * `r+`: 以讀取(reading)+寫入(writing)模式來開啟這個檔案
      * `w+`: 以讀取(reading)+寫入(writing)模式來開啟這個檔案,並且將串流(stream)放在(positioning)檔案的 **最前面**(beginning)。當該檔案不存在時,就會建立一份這個檔案
      * `a`: 以寫入(writing)模式來開啟這個檔案,並且將串流(stream)放在(positioning)檔案的 **最後面** (end)。當該檔案不存在時,就會建立一份這個檔案
      * `a+`: 以讀取(reading)+寫入(writing)模式來開啟這個檔案,並且將串流(stream)放在(positioning)檔案的 **最後面**(end)。當該檔案不存在時,就會建立一份這個檔案
      * 可參考[File system flags](https://nodejs.org/api/fs.html#fs_file_system_flags)
- 附加到檔案中(Append to a file)
  + 有一個便利(handy)的方法(method)能將內容(content)附加(append)到檔案(file)的最後面(end),就是利用[fs.appendFile(path, data[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_appendfile_path_data_options_callback)以及它對應的[fs.appendFileSync(path, data[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_appendfilesync_path_data_options)這兩種方法,
    ``` js
    const content = 'Some content!'

    fs.appendFile('file.log', content, err => {
      if (err) {
        console.error(err)
        return
      }
      //done!
    })
    ```
- 使用串流(Using streams)
  + 所有的這些方法們(methods)皆會先將所有的內容(full content)寫入(wrtie)到檔案(file)中,然後再將控制權(control back)交還(returning)給程式(program)
    * 在非同步化版本(async version, 也就是指`fs.writeFile()`與`fs.appendFile()`這兩種方法)的方法中,這意味(means)著執行(executing)回呼函式(callback)
    * 在這種情況下(In this case),一個更好的選擇(better option)是透過 **串流** (streams)來寫入(write)到檔案內容(file content)中

### Working with folders in Node.js
> Node內建核心模組[File system](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_file_system)<br>

- Node內建核心(core)的`File system`模組(module)有提供(provides)了許多便利(handy)的方法(methods),讓我們可以處理檔案目錄(folders)
- 檢查檔案目錄是否存在(Check if a folder exists)
  + 可利用[fs.access(path[, mode], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_access_path_mode_callback)方法來檢查檔案目錄是否存在
- 建立新的檔案目錄(Create a new folder)
  + 可利用[fs.mkdir(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_mkdir_path_options_callback)與[fs.mkdirSync(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_mkdirsync_path_options)2種方法來建立新的檔案目錄
    ``` js
    const fs = require('fs')

    const folderName = '/Users/joe/test'

    try {
      if (!fs.existsSync(folderName)) {
        fs.mkdirSync(folderName)
      }
    } catch (err) {
      console.error(err)
    }
    ```
- 讀取檔案目錄的內容(Read the content of a directory)
  + 可利用[fs.readdir(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readdir_path_options_callback)與[fs.readdirSync(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readdirsync_path_options)2種方法來讀取檔案目錄的內容
  + 以下這段程式碼會讀取檔案目錄的內容(包含其中的所有檔案(files)與子目錄(subfolders))
      ``` js
      const fs = require('fs')
      const path = require('path')

      const folderPath = '/Users/joe'

      fs.readdirSync(folderPath)
      ```
  + 我們也可以獲得完整(full)的絕對路徑(path)
      ``` js
      fs.readdirSync(folderPath).map(fileName => {
        return path.join(folderPath, fileName)
      })
      ```
  + 我們也可以將結果(results)做過濾(filter),僅回傳(return)檔案(files)而不(exclude)回傳檔案目錄(folders)
      ``` js
      const isFile = fileName => {
        return fs.lstatSync(fileName).isFile()
      }

      fs.readdirSync(folderPath).map(fileName => {
        return path.join(folderPath, fileName)
      })
      .filter(isFile)
      ```
- 將檔案目錄重新命名(Rename a folder)
  + 可利用[fs.rename(oldPath, newPath, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_rename_oldpath_newpath_callback)與[fs.renameSync(oldPath, newPath)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_renamesync_oldpath_newpath)2種方法來將檔案目錄重新命名
    * 這2個方法的第1個參數(first parameter)是當前的路徑(current path),而第2個參數(second parameter)是新的路徑
      ``` js
      const fs = require('fs')

      fs.rename('/Users/joe', '/Users/roger', err => {
        if (err) {
          console.error(err)
          return
        }
        //done
      })
      ```
  + `fs.renameSync()`方法是同步化(synchronous)方法的版本(version)
      ``` js
      const fs = require('fs')

      try {
        fs.renameSync('/Users/joe', '/Users/roger')
      } catch (err) {
        console.error(err)
      }
      ```
- 刪除檔案目錄(Remove a folder)
  + 可利用[fs.rmdir(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_rmdir_path_options_callback)與[fs.rmdirSync(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_rmdirsync_path_options)2種方法來刪除檔案目錄
  + 因為刪除一個含有檔案的目錄(a folder that has content)可能比我們需要的複雜(complicated)
  + 這時候,最佳的做法是安裝npm上的[fs-extra](https://www.npmjs.com/package/fs-extra)套件,它是一個非常熱門且維護良好的套件。`fs-extra`套件是一個可用來臨時替換(drop-in replacement)Node內建的`File system`模組的套件,它能基於`File system`模組之上(on top of it)來提供更多的功能(features)
    * 以這次的範例來說,我們想要使用的功能就是`remove()`方法。這時候,我們需要先安裝`fs-extra`套件
      * 安裝指令: $ `npm install fs-extra`
      * 使用它的`remove()`方法
          ``` js
          const fs = require('fs-extra')

          const folder = '/Users/joe'

          fs.remove(folder, err => {
            console.error(err)
          })
          ```
      * 它也能與`Promise`物件一起使用
          ``` js
          fs.remove(folder)
            .then(() => {
              //done
            })
            .catch(err => {
              console.error(err)
            })
          ```
      * 或是搭配`async/await`語法來使用
          ``` js
          async function removeFolder(folder) {
            try {
              await fs.remove(folder)
              //done
            } catch (err) {
              console.error(err)
            }
          }

          const folder = '/Users/joe'
          removeFolder(folder)
          ```
### The Node.js fs module
> Node內建核心模組[File system](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_file_system)<br>

- `fs`模組提供(provides)了許多非常有用(useful)的功能(functionality)來存取(access) & 與檔案系統(file system)互動(interact)
- 因為`fs`模組是Node本身內建的核心模組,因此我們不用事先安裝它,我們可以透過以下的方法來簡單地引用它
    ``` js
    const fs = require('fs')
    ```
  + 一旦(Once)這樣做,我們就可以開始存取(access)`fs`模組的其它方法(methods)
    * [fs.access(path[, mode], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_access_path_mode_callback): 會檢查(check)該路徑內的檔案是否存在(exists),並且Node有權限(permissions)來存取(access)它
    * [fs.appendFile(path, data[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_appendfile_path_data_options_callback): 附加(append)資料(data)到檔案(file)中。若該路徑內的檔案不存在(exist)的話,就會建立(created)一個
    * [fs.chmod(path, mode, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_chmod_path_mode_callback): 更改(change)指定(specified)的檔案(file)的權限(permissions)設定(透過指定一個檔案名稱來完成的)
      * [fs.lchmod(path, mode, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_lchmod_path_mode_callback): 僅`macOS`作業系統可以使用此方法
      * [fs.fchmod(fd, mode, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_fchmod_fd_mode_callback)
    * [fs.chown(path, uid, gid, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_chown_path_uid_gid_callback): 更改(change)指定(specified)的檔案(file)的擁有者(owner) & 群組(group)設定(透過指定一個檔案名稱來完成的)
      * [fs.lchown(path, uid, gid, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_lchown_path_uid_gid_callback)
      * [fs.fchown(fd, uid, gid, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_fchown_fd_uid_gid_callback)
    * [fs.close(fd, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_close_fd_callback): 關閉一個檔案描述符號(file descriptor)
    * [fs.copyFile(src, dest[, mode], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_copyfile_src_dest_mode_callback): 複製(copies)一個檔案(file)
    * [fs.createReadStream(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_createreadstream_path_options): 建立(create)一個可讀取(readable)的檔案串流(file stream)
    * [fs.createWriteStream(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_createwritestream_path_options): 建立(create)一個可寫入(writable)的檔案串流(file stream)
    * [fs.link(existingPath, newPath, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_link_existingpath_newpath_callback): 建立(create)一個硬連結(hard link)給一個指定的檔案(file)
    * [fs.mkdir()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_mkdir_path_options_callback): 建立(create)一個新的檔案目錄(folder)
    * [fs.mkdtemp(prefix[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_mkdtemp_prefix_options_callback): 建立(create)一個臨時(temporary)的檔案目錄(directory)
    * [fs.open(path[, flags[, mode]], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_open_path_flags_mode_callback): 設定(set)檔案模式(file mode)
    * [fs.readdir(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readdir_path_options_callback): 讀取(read)該檔案目錄(directory)的內容(contents)
    * [fs.readFile()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readfile_path_options_callback): 讀取(read)該檔案(file)的內容(content)
      * [fs.read(fd, buffer, offset, length, position, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_read_fd_buffer_offset_length_position_callback)
    * [fs.readlink(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readlink_path_options_callback): 讀取(read)符號連結(symbolic link)的值(value)
    * [fs.realpath(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_realpath_path_options_callback): 將相對(relative)的檔案路徑(file path)解析(resolve)為完整路徑(full path)=> 也就是將`./`, `../` 等等之類的處理掉
    * [fs.rename(oldPath, newPath, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_rename_oldpath_newpath_callback): 將檔案(file)或是檔案目錄(folder)重新命名(rename)
    * [fs.rmdir(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_rmdir_path_options_callback): 移除(remove)指定的檔案目錄(folder)
    * [fs.stat(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_stat_path_options_callback): 回傳(returns)檔案(file)的狀態(status)
      * [fs.fstat(fd[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_fstat_fd_options_callback)
      * [fs.lstat(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_lstat_path_options_callback)
    * [fs.symlink(target, path[, type], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_symlink_target_path_type_callback): 建立(create)一個新的符號連結(symbolic link)給指定的檔案(file)
    * [fs.truncate(path[, len], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_truncate_path_len_callback): 將指定的檔案名稱縮短為指定的長度( specified length)=> (透過指定一個檔案名稱來完成的)
      * [fs.ftruncate(fd[, len], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_ftruncate_fd_len_callback)
    * [fs.unlink(path, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_unlink_path_callback): 移除一個檔案的符號連結(symbolic link)
    * [fs.unwatchFile(filename[, listener])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_unwatchfile_filename_listener): 停止(stop)查看(watching)一個檔案(file)的修改(changes)
    * [fs.utimes(path, atime, mtime, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_utimes_path_atime_mtime_callback): 更改(change)一個檔案的時間戳記=> (透過指定一個檔案名稱來完成的)(timestamp)
      * [fs.futimes(fd, atime, mtime, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_futimes_fd_atime_mtime_callback)
    * [fs.watchFile(filename[, options], listener)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_watchfile_filename_options_listener): 開始(start)查看(watching)一個檔案(file)的修改(changes)
      * [fs.watch(filename[, options][, listener])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_watch_filename_options_listener)
    * [fs.writeFile(file, data[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback): 將資料(data)寫入(write)檔案(file)中
      * [fs.write(fd, buffer[, offset[, length[, position]]], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_write_fd_buffer_offset_length_position_callback)
- 關於`fs`模組有一件奇怪的事情是所有方法 **預設** 都是 **非同步的** (asynchronous),然而這些方法也都能同步化地(synchronously)使用(=> 只要在各方法後面加上`Sync`語法就可以了)
  + 以下範例說明
  + `fs.rename()`
    * `fs.renameSync()`
  + `fs.write()`
    * `fs.writeSync()`
  + 這麼做會造成(makes)我們的應用程式(application)的工作流(flow)有極大(huge)的差異(difference)
    > Node `v10.0.0`開始,`fs`模組開始引入了[fs Promises API](https://nodejs.org/api/fs.html#fs_fs_promises_api)
    * 舉例來說,我們來檢查(check)一下`fs.rename()`方法,利用回呼函式(callback)的方法來使用非同步化(asynchronous)的API
        ``` js
        const fs = require('fs')

        fs.rename('before.json', 'after.json', err => {
          if (err) {
            return console.error(err)
          }

          //done
        })
        ```
    * 同步化(synchronous)的API可以這樣來使用,並搭配`try/catch`區塊(block)來處理(handle)錯誤(error)
        ``` js
        const fs = require('fs')

        try {
          fs.renameSync('before.json', 'after.json')
          //done
        } catch (err) {
          console.error(err)
        }
        ```
    * 以上這兩種範例的主要(key)差別(difference)是, **第2個範例** 腳本(script)的 **執行(execution)將會被阻塞(block)** ,直到檔案(file)操作(operation)成功(succeeded)

### The Node.js path module
> Node內建核心模組[Path](https://nodejs.org/api/path.html#path_path)<br>

- Node的[Path](https://nodejs.org/api/path.html#path_path)內建核心模組,提供(provides)了許多非常有用(useful)的功能(functionality),來存取(access) & 與檔案系統(file system)互動(interact)
  + `path`模組"不需要"(no need to)事先安裝(install it),才能使用。因為它本身已經是Node的核心模組(core)的一部分(part of)了,它能夠簡單地(simply)透過引用(=> `require`語法)來使用`path`模組
    ``` js
    const path = require('path')
    ```
  + `path`模組有提供[path.sep](https://nodejs.org/api/path.html#path_path_sep)屬性,來作為路徑的分隔符號(path segment separator)
    * 也就是說,在Windows作業系統會是`\`; 而在Linux/macOS作業系統會是`/`
  + `path`模組有提供[path.delimiter](https://nodejs.org/api/path.html#path_path_delimiter)屬性,來作為路徑的定界符(path delimiter)
    * 也就是說,在Windows作業系統會是`;`; 而在Linux/macOS作業系統會是`:`
- 以下是`path`模組常會用到的方法(methods)
  + [path.basename(path[, ext])](https://nodejs.org/api/path.html#path_path_basename_path_ext)
    * 該方法會回傳(return)整個路徑(path)的最後一個(last)部分(portion); 而該方法的第2個參數(parameter)可以用來過濾掉(filter out)副檔名(file extension)
      ``` js
      require('path').basename('/test/something') //something
      require('path').basename('/test/something.txt') //something.txt
      require('path').basename('/test/something.txt', '.txt') //something
      ```
  + [path.dirname(path)](https://nodejs.org/api/path.html#path_path_dirname_path)
    * 該方法會回傳(return)其檔案路徑(path)參數(parameter)的檔案目錄(directory)的部分(part)
      ``` js
      require('path').dirname('/test/something') // /test
      require('path').dirname('/test/something/file.txt') // /test/something
      ```
  + [path.extname(path)](https://nodejs.org/api/path.html#path_path_extname_path)
    * 該方法會回傳(return)其檔案路徑(path)參數(parameter)的副檔名(file extension)的部分(part)
      ``` js
      require('path').extname('/test/something') // ''
      require('path').extname('/test/something/file.txt') // '.txt'
      ```
  + [path.isAbsolute(path)](https://nodejs.org/api/path.html#path_path_isabsolute_path)
    * 該方法會回傳(return)其檔案路徑(path)參數(parameter)是否為一種"絕對路徑"的形式
      ``` js
      require('path').isAbsolute('/test/something') // true
      require('path').isAbsolute('./test/something') // false
      ```
  + [path.join([...paths])](https://nodejs.org/api/path.html#path_path_join_paths)
    * 該方法會結合路徑(path)的2個or多個部分(part)
      ``` js
      const name = 'joe'
      require('path').join('/', 'users', name, 'notes.txt') //'/users/joe/notes.txt'
      ```
  + [path.normalize(path)](https://nodejs.org/api/path.html#path_path_normalize_path)
    * 該方法會嘗試(tries to)去計算(calculate)其檔案路徑(path)參數(parameter)的真實路徑(actual path) (=> 當這個檔案路徑參數(`path`)有包含(contains)了`.` or `..` or `//`... 等等之類的相對說明符(relative specifiers))
  + [path.parse(path)](https://nodejs.org/api/path.html#path_path_parse_path)
    * 該方法會以一個物件(object)的形式,將其檔案路徑(path)參數(parameter)解析(parses)為多個重要片段(segments),來組成(compose)出這個路徑物件
    * 範例程式碼
      ``` js
      require('path').parse('/users/test.txt')
      ```
    * 上述的範例程式碼,將會回傳
      ``` js
        {
          root: '/',
          dir: '/users',
          base: 'test.txt',
          ext: '.txt',
          name: 'test'
        }
      ```
      * `root`: 根目錄(root)
      * `dir`: 從根目錄(root)開始(starting from)的檔案目錄的路徑(folder path)
      * `base`: 檔案名稱(file name)+副檔名(file extension)
      * `name`: 檔案名稱(file name)
      * `ext`: 檔案的副檔名(file extension)
  + [path.relative(from, to)](https://nodejs.org/api/path.html#path_path_relative_from_to)
    * 該方法會接受(accepts)2個檔案路徑(paths)作為參數(arguments),並以(based on)當前(current)工作目錄(working directory)的位置的角度,回傳(returns)一個從第1個參數(=> `from`)到第2個參數(=> `to`)的相對路徑(relative path)
      ``` js
      require('path').relative('/Users/joe', '/Users/joe/test.txt') //'test.txt'
      require('path').relative('/Users/joe', '/Users/joe/something/test.txt') //'something/test.txt'
      ```
  + [path.resolve([...paths])](https://nodejs.org/api/path.html#path_path_resolve_paths)
    * 我們利用此方法,來將相對路徑(relative path)計算(calculation)出絕對路徑(absolute path)
        ``` js
        path.resolve('joe.txt') //'/Users/joe/joe.txt' if run from my home folder
        ```
    * 透過指定第2個參數(parameter),`path.resolve()`方法會使用第1個參數為基礎來解析(resolve)第2個參數
        ``` js
        path.resolve('tmp', 'joe.txt') //'/Users/joe/tmp/joe.txt' if run from my home folder
        ```
    *  如果第一個參數(parameter)是以斜線(slash)開頭(starts with)的話,就表示(means)這是一個絕對路徑(absolute path)
        ``` js
        path.resolve('/etc', 'joe.txt') //'/etc/joe.txt'
        ```

### The Node.js os module
> Node內建核心模組[OS](https://nodejs.org/api/os.html#os_os)<br>

- 這個模組會提供(provides)許多功能(functions)讓我們能夠跟底層作業系統(underlying operating system) & 該程式運行時所在的電腦(the computer the program runs on)做互動(interact)並檢索其資訊(retrieve information)
  + ``` js
    const os = require('os')
    ```
  + 在`os`模組之中,有一些有用(useful)的屬性(properties)能告訴我們一些與處理檔案(handling files)有關的(related to)重要的事情(key things),像是以下屬性們
    * [os.EOL](https://nodejs.org/api/os.html#os_os_eol)
      * 該屬性會給出(gives)定界符(delimiter)序列(sequence)。在Linux/macOS作業系統中就是`\n`,而在Windows作業系統中會是`\r\n`
    * [os.constants.signal](https://nodejs.org/api/os.html#os_signal_constants)
      * 該屬性會告訴我們所有跟處理(handling)進程(process)信號(signals)有關(releated to)的常數(constants),像是`SIGHUP`, `SIGKILL`, ...等等之類的
    * [os.constants.errorno](https://nodejs.org/api/os.html#os_error_constants)
      * 該屬性會設定(sets)錯誤報告(error reporting)的常數(constants),像是`EADDRINUSE`, `EOVERFLOW`, ...等等之類的
- 接下來,我們來看看`os`模組中主要會用到的方法(methods)
  + [os.arch()](https://nodejs.org/api/os.html#os_os_arch)
    * 該方法會回傳(return)一個字串(string)來表示(identifies)此底層作業系統( underlying)的架構(architecture),像是`arm`, `x64`, `arm64`
  + [os.cpus()](https://nodejs.org/api/os.html#os_os_cpus)
    * 該方法會回傳(return)一個陣列(array)來表示我們的作業系統(system)上有多少可用(available)的中央處理器們(CPUs)的資源的資訊(information)
    * ``` js
      [
        {
          model: 'Intel(R) Core(TM)2 Duo CPU     P8600  @ 2.40GHz',
          speed: 2400,
          times: {
            user: 281685380,
            nice: 0,
            sys: 187986530,
            idle: 685833750,
            irq: 0
          }
        },
        {
          model: 'Intel(R) Core(TM)2 Duo CPU     P8600  @ 2.40GHz',
          speed: 2400,
          times: {
            user: 282348700,
            nice: 0,
            sys: 161800480,
            idle: 703509470,
            irq: 0
          }
        }
      ]
      ```
  + [os.endianness()](https://nodejs.org/api/os.html#os_os_endianness)
    * 該方法會回傳(return)一個字串(string)來表示(identifying)此作業系統的中央處理器(CPU)資源的字節序(endianness),僅會回傳`BE`或是`LE`
    * 可參考維基百科的[Big Endian or Little Endian](https://en.wikipedia.org/wiki/Endianness)
  + [os.freemem](https://nodejs.org/api/os.html#os_os_freemem)
    * 該方法會回傳(return)一個整數(integer, => 以`bytes`為單位)來代表(represent)此作業系統的記憶體(memory)資源
  + [os.homedir()](https://nodejs.org/api/os.html#os_os_homedir)
    * 該方法會回傳(return)一個字串(string)來表示當前(current)使用者(user)的家目錄(home directory)
    * ```bash
        '/Users/joe'
      ```
  + [os.hostname()](https://nodejs.org/api/os.html#os_os_hostname)
    * 該方法會回傳(return)一個字串(string)來表示該作業系統(operating system)的主機名稱(host name)
  + [os.loadavg()](https://nodejs.org/api/os.html#os_os_loadavg)
    * 該方法會回傳(return)一個陣列(array)來表示該作業系統(operating system)於第`1`, `5`, `15`分鐘(這三個時段的時候),計算(calculation)出載入(load)所花費的平均時間(average)
      ``` js
      [3.68798828125, 4.00244140625, 11.1181640625]
      ```
    * **注意! `load average`這個值只對Linux/macOS作業系統才有用**
  + [os.networkInterfaces()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_networkinterfaces)
    * 該方法會回傳(returns)一個物件(object),包含此作業系統(system)中可用(available)的網路介面(network interfaces)的細節資訊(details)
      ``` js
      { lo0:
        [ { address: '127.0.0.1',
            netmask: '255.0.0.0',
            family: 'IPv4',
            mac: 'fe:82:00:00:00:00',
            internal: true },
          { address: '::1',
            netmask: 'ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff',
            family: 'IPv6',
            mac: 'fe:82:00:00:00:00',
            scopeid: 0,
            internal: true },
          { address: 'fe80::1',
            netmask: 'ffff:ffff:ffff:ffff::',
            family: 'IPv6',
            mac: 'fe:82:00:00:00:00',
            scopeid: 1,
            internal: true } ],
        en1:
        [ { address: 'fe82::9b:8282:d7e6:496e',
            netmask: 'ffff:ffff:ffff:ffff::',
            family: 'IPv6',
            mac: '06:00:00:02:0e:00',
            scopeid: 5,
            internal: false },
          { address: '192.168.1.38',
            netmask: '255.255.255.0',
            family: 'IPv4',
            mac: '06:00:00:02:0e:00',
            internal: false } ],
        utun0:
        [ { address: 'fe80::2513:72bc:f405:61d0',
            netmask: 'ffff:ffff:ffff:ffff::',
            family: 'IPv6',
            mac: 'fe:80:00:20:00:00',
            scopeid: 8,
            internal: false } ] 
      }
      ```
  + [os.platform()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_platform)
    * 該方法會回傳(return)一個字串(string)來表示(identifying)此作業系統(operating system)的被編譯(compiled for)的平台(platform)。以下是可能的回傳值
    * `darwin`
    * `freebsd`
    * `linux`
    * `openbsd`
    * `win32`
    * ...等等之類的
  + [os.release()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_release)
    * 該方法會回傳(return)一個字串(string)來表示(identifying)此作業系統(operating system)與其版本號(release number)
  + [os.tmpdir()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_tmpdir)
    * 該方法會回傳(return)一個字串(string)來表示(identifying)此作業系統的臨時檔案會存放在哪個檔案目錄(temp folder)
  + [os.totalmem()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_totalmem)
    * 該方法會回傳(return)一個整數(string)來代表(represent)此作業系統(operating system)中可用(available)的總記憶體量(total memory, => 以`bytes`為單位來表示)
  + [os.type()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_type)
    * 該方法會回傳(return)一個字串(string)來表示(identifying)目前所在的作業系統名稱(operating system name)是什麼。以下是最常見的3種作業系統分別對應的值
    * `Linux`作業系統: Linux
    * `macOS`作業系統: Darwin
    * `Windows`作業系統: Windows_NT
  + [os.uptime()](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_uptime)
    * 該方法會回傳(return)一個整數(integer)來表示自從(since)上次電腦重開機(last rebooted)到現在,已經運行(running)了多久(=> 以"秒"為單位)
  + [os.userInfo([options])](https://nodejs.org/dist/latest-v15.x/docs/api/os.html#os_os_userinfo_options)
    * 該方法會回傳(returns)一個物件(object)包含了當前的使用者名稱(username), 使用者ID(uid), 群組ID(gid), shell, 家目錄(homedir), ...等等之類的

### The Node.js events module
> Node內建核心模組[Events](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_events)<br>

- **`event`模組(module)會提供(provides)我們EventEmitter類別(class),這是在Node中處理事件們(events)的關鍵(key)**
    ``` js
    const EventEmitter = require('events')
    const door = new EventEmitter()
    ```
  + 事件監聽器(Event listener)會吃(eats)它自己(its own)的狗食(dog food),並使用(uses)以下的事件(events)
    * `newListener`: 當事件監聽器(event listener)被新增(added)時
    * `removeListener`: 當事件監聽器(event listener)被移除(removed)時
- 以下是`event`模組(module)中常用的方法(methods)
  + [emitter.addListener(eventName, listener)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_addlistener_eventname_listener)
    * 該方法會新增(adds)一個事件監聽器函式(`listener` function)到事件監聽器陣列(`listeners` array)的最後面(end)
    * 等同於[emitter.on(eventName, listener)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_on_eventname_listener)
  + [emitter.emit(eventName[, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_emit_eventname_args)
    * 發出(Emits)一個事件(event)。該方法會 **同步地** (synchronously)依照被註冊(registered)的順序(in the order)來 **呼叫** (calls) **每一個** (every) **事件監聽器** (event listener)
    * ``` js
        door.emit("slam") // emitting the event "slam"
      ```
  + [emitter.eventNames()](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_eventnames)
    * 該方法會回傳(return)一個陣列(array)來代表(represent)有註冊(registered)在目前(current)的`EventEmitter`物件(object)上的事件們(events)
      ``` js
      door.eventNames()
      ```
  + [emitter.getMaxListeners()](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_getmaxlisteners)
    * 該方法會回傳(returns)一個整數(integer)來代表當前的`EventEmitter`物件(object)"最多"(maximum)能被新增(add)幾個事件監聽器(listeners)在其上面
    * 預設值(default): `10`
    * 並可以透過[emitter.setMaxListeners(n)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_setmaxlisteners_n)方法來設定指定的`EventEmitter`物件(object)"最多"(maximum)能被新增(add)幾個事件監聽器(listeners)到其上面
  + [emitter.listenerCount(eventName)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_listenercount_eventname)
    * 該方法會回傳(returns)一個整數(integer)來代表當前的`EventEmitter`物件(object)"上有幾個事件監聽器(listeners)
      ``` js
      door.listenerCount('open')
      ```
  + [emitter.listeners(eventName)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_listeners_eventname)
    * 該方法會回傳(returns)一個陣列(array)來代表當前的`EventEmitter`物件(object)"上有哪些事件監聽器(listeners)
      ``` js
      door.listeners('open')
      ```
  + [emitter.removeListener(eventName, listener)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_removelistener_eventname_listener)
    * 該方法會從事件監聽器陣列(`listener` array)上移除(removes)指定(specified)的事件監聽器(`listener`)
    * 從Node `v10.0.0`版本開始,等同於[emitter.off(eventName, listener)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_off_eventname_listener)
  + [emitter.on(eventName, listener)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_on_eventname_listener)
    * 該方法會新增(adds)一個回呼函式(callback function)給當前被發出(emitted)的事件(event)
      ``` js
      door.on('open', () => {
        console.log('Door was opened')
      })
      ```
  + [emitter.once(eventName, listener)](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_emitter_once_eventname_listener)
    * 該方法會新增(adds)一個回呼函式(callback function)在被註冊(registered)以後(after),而第1次(first)被發出(emitted)的的事件(event)。這個方法只會被呼叫(called)一次(once),不會(never)再被呼叫一次(again)
      ``` js
      const EventEmitter = require('events')
      const ee = new EventEmitter()

      ee.once('my-event', () => {
        //call callback function once
      })
      ```
  + [emitter.prependListener(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_prependlistener_eventname_listener)
    * 當我們使用`emitter.on()`或是`emitter.addListener()`這2種方法來新增(add)事件監聽器(listener)時,它們會把事件監聽器新增到整個監聽器(listeners)隊列(queue)的最後面(last),並且最後(last)一個呼叫(called)。此時,如果我們想要新增事件監聽器到整個監聽器隊列的最前面(before)時,就可以透過`emitter.prependListener()`方法來達成,並且第1個呼叫(called)其新增的事件監聽器
  + [emitter.prependOnceListener(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_prependoncelistener_eventname_listener)
    * 當我們使用`emitter.once()`方法來新增事件監聽器時,它會把事件監聽器新增到整個監聽器(listeners)隊列(queue)的最後面(last),並且最後(last)一個呼叫(called)。此時,如果我們想要新增事件監聽器到整個監聽器隊列的最前面(before)時,就可以透過`emitter.prependOnceListener()`方法來達成,並且第1個呼叫(called)其新增的事件監聽器
  + [emitter.removeAllListeners([eventName])](https://nodejs.org/api/events.html#events_emitter_removealllisteners_eventname)
    * 該方法會從`EventEmitter`物件(object)中移除(removes) **指定事件(specific event)中** 所有(all)的事件監聽器(listeners)
    * ``` js
        door.removeAllListeners('open')
      ```
  + [emitter.removeListener(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_removelistener_eventname_listener)
    * 該方法會 **移除(remove)一個指定(specific)的事件監聽器(listener)** 。我們可以將該方法的回呼函式(callback function)參數儲存(saving)為一個變數(variable),以讓之後(later)當我們需要參考(reference)時,可以透過此變數來呼叫`emitter.removeListener()`方法
      ``` js
      const doSomething = () => {}
      door.on('open', doSomething)
      door.removeListener('open', doSomething)
      ```
  + [emitter.setMaxListeners(n)](https://nodejs.org/api/events.html#events_emitter_setmaxlisteners_n)
    * 該方法可以設定(set) **最多(maximum)** 能新增幾個事件監聽器(listeners)到`EventEmitter`物件(object)上面。預設值(defaults)為`10`個事件監聽器,但能設定要增加(increased) or 減少(lowered)
      ``` js
      door.setMaxListeners(50)
      ```

### The Node.js http module
> Node內建核心模組[HTTP](https://nodejs.org/api/http.html#http_http)<br>

- `HTTP`核心模組(core module)是Node網路(networking)的關鍵模組(key module)
- `HTTP`模組可以透過引用(require)來使用
  ``` js
  const http = require('http')
  ```
  + 該模組會提供一些屬性(properties), 方法(methods), 和一些類別(classes)
- 屬性(Properties)
  + [http.METHODS](https://nodejs.org/api/http.html#http_http_methods)
    * 該屬性會列出(lists)所有能支援(supported)的HTTP方法(methods)
    ``` js
    > require('http').METHODS
    [ 'ACL',
      'BIND',
      'CHECKOUT',
      'CONNECT',
      'COPY',
      'DELETE',
      'GET',
      'HEAD',
      'LINK',
      'LOCK',
      'M-SEARCH',
      'MERGE',
      'MKACTIVITY',
      'MKCALENDAR',
      'MKCOL',
      'MOVE',
      'NOTIFY',
      'OPTIONS',
      'PATCH',
      'POST',
      'PROPFIND',
      'PROPPATCH',
      'PURGE',
      'PUT',
      'REBIND',
      'REPORT',
      'SEARCH',
      'SUBSCRIBE',
      'TRACE',
      'UNBIND',
      'UNLINK',
      'UNLOCK',
      'UNSUBSCRIBE' 
    ]
    ```
  + [http.STATUS_CODES](https://nodejs.org/api/http.html#http_http_status_codes)
    * 該屬性會列出(lists)所有的HTTP狀態碼(status code)以及它們分別的描述(description)
    ``` js
    > require('http').STATUS_CODES
    { '100': 'Continue',
      '101': 'Switching Protocols',
      '102': 'Processing',
      '200': 'OK',
      '201': 'Created',
      '202': 'Accepted',
      '203': 'Non-Authoritative Information',
      '204': 'No Content',
      '205': 'Reset Content',
      '206': 'Partial Content',
      '207': 'Multi-Status',
      '208': 'Already Reported',
      '226': 'IM Used',
      '300': 'Multiple Choices',
      '301': 'Moved Permanently',
      '302': 'Found',
      '303': 'See Other',
      '304': 'Not Modified',
      '305': 'Use Proxy',
      '307': 'Temporary Redirect',
      '308': 'Permanent Redirect',
      '400': 'Bad Request',
      '401': 'Unauthorized',
      '402': 'Payment Required',
      '403': 'Forbidden',
      '404': 'Not Found',
      '405': 'Method Not Allowed',
      '406': 'Not Acceptable',
      '407': 'Proxy Authentication Required',
      '408': 'Request Timeout',
      '409': 'Conflict',
      '410': 'Gone',
      '411': 'Length Required',
      '412': 'Precondition Failed',
      '413': 'Payload Too Large',
      '414': 'URI Too Long',
      '415': 'Unsupported Media Type',
      '416': 'Range Not Satisfiable',
      '417': 'Expectation Failed',
      '418': 'I\'m a teapot',
      '421': 'Misdirected Request',
      '422': 'Unprocessable Entity',
      '423': 'Locked',
      '424': 'Failed Dependency',
      '425': 'Unordered Collection',
      '426': 'Upgrade Required',
      '428': 'Precondition Required',
      '429': 'Too Many Requests',
      '431': 'Request Header Fields Too Large',
      '451': 'Unavailable For Legal Reasons',
      '500': 'Internal Server Error',
      '501': 'Not Implemented',
      '502': 'Bad Gateway',
      '503': 'Service Unavailable',
      '504': 'Gateway Timeout',
      '505': 'HTTP Version Not Supported',
      '506': 'Variant Also Negotiates',
      '507': 'Insufficient Storage',
      '508': 'Loop Detected',
      '509': 'Bandwidth Limit Exceeded',
      '510': 'Not Extended',
      '511': 'Network Authentication Required' 
    }
    ```
  + [http.globalAgent](https://nodejs.org/api/http.html#http_http_globalagent)
    * 該屬性會指向(points to)全域的代理器(`Agent`)物件(object)的實例(instance),也就是[http.Agent](https://nodejs.org/api/http.html#http_class_http_agent)類別(class)的實例(instance)
    * 該屬性被用來管理(manage)與`HTTP`客戶端(clients)的連接(connections)持久性(persistence) & 重用(reuse)。因此該屬性是Node的`HTTP`網路(networking)的關鍵元件(key component)
    * 關於`http.Agent`類別(class),在稍後(later on)的描述(description)中會有更多介紹
- 方法(Methods)
  + [http.createServer([options][, requestListener])](https://nodejs.org/api/http.html#http_http_createserver_options_requestlistener)
    * 該方法會回傳(return)一個新的[http.Server](https://nodejs.org/api/http.html#http_class_http_server)類別(class)的實例(instance)
      ``` js
      const server = http.createServer((req, res) => {
        //handle every single request with this callback
      })
      ```
  + [http.request(url[, options][, callback])](https://nodejs.org/api/http.html#http_http_request_options_callback)
    * 該方法會向伺服器端發出`HTTP`的請求(`request`),並建立(creating)一個[http.ClientRequest](https://nodejs.org/api/http.html#http_class_http_clientrequest)類別(class)的實例(instance)
  + [http.get(url[, options][, callback])](https://nodejs.org/api/http.html#http_http_get_options_callback)
    * 與`http.request()`方法類似(similar),但會自動地(automatically)設定(sets)`HTTP`的請求(`request`)方法為`GET`方法,並且會自動呼叫[request.end([data[, encoding]][, callback])](https://nodejs.org/api/http.html#http_request_end_data_encoding_callback)方法
- 類別(Classes)
  + `HTTP`模組(module)會提供以下5種類別(classes)
    * `http.Agent`
    * `http.ClientRequest`
    * `http.Server`
    * `http.ServerResponse`
    * `http.IncomingMessage`
  + [http.Agent](https://nodejs.org/api/http.html#http_class_http_agent)
    * Node會建立一個全域的代理器(`Agent`)類別(class)的實例(instance),以用來管理(manage)與`HTTP`客戶端(clients)的連接(connections)持久性(persistence) & 重用(reuse)。因此`http.Agent`類別(class)是Node的`HTTP`網路(networking)的關鍵元件(key component)
    * 這個物件會確保(make sure)對於伺服器端(server)的每個請求(every request)都是排隊(queued)的,並且任何單一(single)插座(socket)都是有被重複使用(reused)的
    * **此類別(class)也會維護(maintains)插座池(a pool of sockets)。這也就是Node效能(performance)好的關鍵(key)原因(reason)之一**
  + [http.ClientRequest](https://nodejs.org/api/http.html#http_class_http_clientrequest)
    * 當呼叫(called)[http.request](https://nodejs.org/api/http.html#http_http_request_options_callback)或是[http.get()](https://nodejs.org/api/http.html#http_http_get_options_callback)這2種方法之一時,皆會建立(created)一個`http.ClientRequest`物件(object)
    * 當收到(received)回應(response)時,將會以一個`http.IncomingMessage`實例(instance)作為參數(argument),來呼叫`response`事件(event)來作為回應(response)
    * 該類別(class)所回傳(returned)的回應資料(data of a response),可以透過以下2種方式來讀取
      * 我們可以呼叫`response.read()`方法
      * 在`response`事件處理器(event handler)中,我們可以設定(setup)一個事件監聽器(event listener)給`data`事件(event),因此我們就能監聽(listen)串流進去(streamed into)的資料流(data)
  + [http.Server](https://nodejs.org/api/http.html#http_class_http_server)
    * 當透過`http.createServer()`方法(method)來建立(creating)一個新的伺服器(new server)時,通常(commonly)會實例化(instantiated)並且回傳(returned)此類別(=> 也就是`http.Server`類別(class))
    * 一旦(Once)我們擁有(have)一個伺服器物件(server object)時,我們可以透過以下2種方法(methods)來存取(access)這個伺服器物件
      * [server.close([callback])](https://nodejs.org/api/http.html#http_server_close_callback): 阻止(stop)伺服器(server)繼續接受(accepting)新的連接(new connections)
      * [server.listen()](https://nodejs.org/api/http.html#http_server_listen): 啟動(starts)HTTP伺服器(server),並監聽(listens)連接(connections)
  + [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)
    * 此類別是透過`http.Server`類別(class)所建立的,並傳遞(passed)給作為觸發(fires)`request`事件(event)的第2個參數(parameter)
    * **常見(Commonly)且知名(known)的做法是在程式碼中用作`res`**
        ``` js
        const server = http.createServer((req, res) => {
          //res is an http.ServerResponse object
        })
        ```
      * 在事件處理器(handler)中,我們常會呼叫(call)的會是[response.end([data[, encoding]][, callback])](https://nodejs.org/api/http.html#http_response_end_data_encoding_callback)方法(method),`response.end()`方法會關閉(close)`response`物件,同時訊息(message)已經完成(complete)了,伺服器(server)也能將此訊息發送(send)給客戶端(client)。`response.end()`方法將會在每次回應(each response)時,都會被呼叫(called)到
    * 以下的方法(methods)們將會被用來與HTTP標頭(headers)做互動(interact)
      * [response.getHeaderNames()](https://nodejs.org/api/http.html#http_response_getheadernames)
        * 此方法會得到(get)一個已經(already)設定(set)好的HTTP標頭(headers)的名稱清單(the list of names)
      * [response.getHeaders()](https://nodejs.org/api/http.html#http_response_getheaders)
        * 此方法會得到(get)一個已經(already)設定(set)好的HTTP標頭(headers)的拷貝(copy, => 會以物件(object)的形式來展示)
      * [response.setHeader('headername', value)](https://nodejs.org/api/http.html#http_response_setheader_name_value)
        * 此方法會設定一個(set)HTTP標頭(header)的值(value)
      * [response.getHeader('headername')](https://nodejs.org/api/http.html#http_response_getheaders)
        * 此方法會得到(get)一個已經(already)設定(set)好的HTTP標頭(header)的值(value)
      * [response.removeHeader('headername')](https://nodejs.org/api/http.html#http_response_removeheader_name)
        * 此方法會移除(remove)一個已經(already)設定(set)好的HTTP標頭(header)
      * [response.hasHeader('headername')](https://nodejs.org/api/http.html#http_response_hasheader_name)
        * 當指定的HTTP標頭(header)已經(already)有設定(set)過的值時候,就會回傳`true`
      * [response.headersSent](https://nodejs.org/api/http.html#http_response_headerssent)
        * 當指定的HTTP標頭(header)已經(already)傳送(sent)給客戶端(client)的時候,就會回傳`true`
    * 在處理完HTTP標頭(header)以後,我們就可以透過[response.writeHead(statusCode[, statusMessage][, headers])](https://nodejs.org/api/http.html#http_response_writehead_statuscode_statusmessage_headers)方法來將HTTP標頭(header)傳送給客戶端(client)。`response.writeHead()`方法會接受狀態碼(statusCode)作為第1個參數(parameter),接著是選擇性(optional)的參數(=> 狀態訊息(status message)),最後是HTTP標頭(headers)物件(object)
    * 若想要將資料(data)傳送(send)給客戶端(client)的內文(`response body`)的話,我們會需要透過[response.write(chunk[, encoding][, callback])](https://nodejs.org/api/http.html#http_response_write_chunk_encoding_callback)方法,而`response.write()`方法將會傳送(send)緩存資料(buffered data)給HTTP回應串流(response stream)
    * 如果尚未(not yet)先使用`response.writeHead()`方法來傳送(sent)HTTP標頭(header)出去的話,它將會先(first)傳送(send)依照傳過來的HTTP請求(`response`)的狀態碼(status code) & 狀態訊息(status message)來傳送回去HTTP標頭(header)給客戶端(client)。我們也可以透過[response.statusCode](https://nodejs.org/api/http.html#http_response_statuscode)與[response.statusMessage](https://nodejs.org/api/http.html#http_response_statusmessage)這2種屬性(properties)來設定要回傳給客戶端的HTTP標頭(header)的狀態碼(status code) & 狀態訊息(status message)
  + [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)
    * 此一物件(object)可透過以下2種方式來建立(created)出來
      * 當`http.Server`物件在監聽(listening)`request`事件(event)時
      * 當`http.ClientRequest`物件在監聽(listening)`response`事件(event)時
    * `http.IncomingMessage`物件可以被用來存取回應物件(`response`)
      * 利用它自己的[message.statusCode](https://nodejs.org/api/http.html#http_message_statuscode)與[message.statusMessage](https://nodejs.org/api/http.html#http_message_statusmessage)這2種屬性
      * 利用他自己的HTTP標頭(headers)方法或是[message.rawHeaders](https://nodejs.org/api/http.html#http_message_rawheaders)屬性
      * HTTP方法(method)可以利用它自己的[message.method](https://nodejs.org/api/http.html#http_message_method)屬性
      * HTTP版本(version)可以利用它自己的[message.httpVersion](https://nodejs.org/api/http.html#http_message_httpversion)屬性
      * `URL`可以利用它自己的[message.url](https://nodejs.org/api/http.html#http_message_url)屬性
      * 底層插座(underlying socket)可以利用它自己的[message.socket](https://nodejs.org/api/http.html#http_message_socket)屬性
    * 由於(since)`http.IncomingMessage`物件實現(implements)了可讀取(readable)串流(stream)介面(interface),因此可透過串流(streams)來存取(accessed)資料(data)

### Node.js Buffers
> Node內建核心模組[Buffer](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_buffer)<br>

- `Buffer`是什麼? (What is a buffer?)
  + `buffer`(緩存)是記憶體(memory)的一個區域(area)。Javascript的開發者(developers)可能會不太熟悉這個觀念,而不像是使用其他`C`, `C++`, `Go`程式語言(或是其他也在使用系統程式語言(system programming language)的人),每天需要與記憶體互動(interact)的開發者,來得熟悉
  + `buffer`(緩存)代表一個從`V8`這個Javascript引擎(engine)所分配(allocated)出來的固定大小(a fixed-size chunk)的記憶體(memory),並且不能再被調整大小(resized)
  + 我們可以把`buffer`(緩存)想像(think of)成是一個類似正整數陣列(an array of integer),其中的每個(each)數字都代表(represent)資料的一個位元組(a byte of data)
  + `buffer`(緩存)是透過Node的[Buffer](https://nodejs.org/api/buffer.html#buffer_buffer)類別(class)來實作(implemented)的
- 我們為什麼會需要`buffer`(緩存)? (Why do we need buffer?)
  + 相比於傳統上僅處理字串(string)而不是二進制數據(binaries)的生態圈(ecosystem),Node的`Buffer`類別(class)是被引進(introduced)來幫助開發者處理(dealt with)二進制的資料(binary data)
  + `Buffer`(緩存)與`Stream`(串流)是緊密相連(deeply linked)的。當串流處理器(stream processor)收到(receives)資料(data)的速度比起它能消化(digest)的速度快(faster)時,串流處理器就會將資料放到`buffer`
  + 對於`buffer`(緩存)的一個簡單視覺化(visualization)的方式就是當我們在觀看YouTube影片時,紅線(red line)會超過(beyond)我們的觀看點(visualization point)---這就代表YoutTube下載資料(downloading data)的速度比我們觀看(viewing)影片的速度來得快(faster),並且由我們的瀏覽器(browser)負責處理`buffer`(緩存)
- 如何建立`buffer`(緩存)? (How to create a buffer)
  + `buffer`(緩存)是透過[Buffer.from(), Buffer.alloc(), and Buffer.allocUnsafe()](https://nodejs.org/api/buffer.html#buffer_buffer_from_buffer_alloc_and_buffer_allocunsafe)方法們來建立(created)的
      ``` js
      const buf = Buffer.from('Hey!')
      ```
    * [Buffer.from(array)](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_from_array)
    * [Buffer.from(arrayBuffer[, byteOffset[, length]])](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_from_arraybuffer_byteoffset_length)
    * [Buffer.from(buffer)](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_from_buffer)
    * [Buffer.from(string[, encoding])](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_from_string_encoding)
  + 我們也可以僅傳遞(passing)一個大小(size)來初始化(initialize)一個`buffer`(緩存)。以下的範例會建立一個1`KB`大小的`buffer`
      ``` js
      const buf = Buffer.alloc(1024)
      //or
      const buf = Buffer.allocUnsafe(1024)
      ```
      * 儘管(While)以上的2種方法(=> 也就是[Buffer.alloc()](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_alloc_size_fill_encoding)與[Buffer.allocUnsafe()](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_allocunsafe_size))皆分配給`buffer`(緩存)一個指定(specified)大小(size)的位元組(bytes),但是由`Buffer.alloc()`方法所建立的`buffer`會被初始化(initialized)為`0`,而由`Buffer.allocUnsafe()`方法所建立的`buffer`將"不會"被初始化(uninitialized)。這也意味著儘管由`Buffer.allocUnsafe()`方法所建立的`buffer`將會比由`Buffer.alloc()`方法所建立的`buffer`來得快很多(quite fast),但是由`Buffer.allocUnsafe()`方法所建立的`buffer`被分配(allocated)到的記憶體(memory)片段(segment)可能會包含(contain)舊資料(old data),而這些舊資料將有可能是敏感性(sensitive)資料
      * 當記憶體(memory)中存在舊資料(older data),就可以在`buffer`(緩存)記憶體被讀取(read) or 洩漏(leaked)時可以被存取(accessed)。正也因為這樣,`Buffer.allocUnsafe()`方法才會被命名為不安全(unsafe)的,並且在使用此方法時需要更加注意(extra care)
- 使用`buffer`(緩存) (Using a buffer)
  + 存取`buffer`(緩存)的內容 (Access the content of a buffer)
    * `buffer`(緩存)是一個由二進制資料(bytes)組成的陣列(array),可以用如同陣列(array)一樣的方式來存取(accessed)
        ``` js
        const buf = Buffer.from('Hey!')
        console.log(buf[0]) //72
        console.log(buf[1]) //101
        console.log(buf[2]) //121
        ```
        * 以上的數字是萬國碼(Unicode Code),表示字元(character)在緩衝區(`buffer`)的位置(position)。`H`=> 72, `e`=> 101, `y`=> 121
      * 我們也可以使用[buf.toString()](https://nodejs.org/api/buffer.html#buffer_buf_tostring_encoding_start_end)方法來將`buffer`(緩存)的完整內容(full content)打印(print)出來
          ``` js
          console.log(buf.toString())
          ```
      * 當我們在初始化(initialize)一個`buffer`(緩存)時,有設定(sets)好它的大小(size)的話,則我們將能存取會包含(contain)隨機數據(random data)而不是空(empty)的`buffer`(緩存)的預初始化記憶體(pre-initialized memory)
  + 取得`buffer`(緩存)的長度 (Get the length of a buffer)
    * 可利用[buf.length](https://nodejs.org/api/buffer.html#buffer_buf_length)屬性
      ``` js
      const buf = Buffer.from('Hey!')
      console.log(buf.length)
      ```
  + 遍歷`buffer`(緩存)的內容 (Iterate over the contents of a buffer)
      ``` js
      const buf = Buffer.from('Hey!')
      for (const item of buf) {
        console.log(item) //72 101 121 33
      }
      ```
  + 更改`buffer`(緩存)的內容 (Changing the content of a buffer)
    * 可利用[buf.write(string[, offset[, length]][, encoding])](https://nodejs.org/api/buffer.html#buffer_buf_write_string_offset_length_encoding)方法來整個(whole)資料字串(string of data)寫入(write)到`buffer`(緩存)之中
        ``` js
        const buf = Buffer.alloc(4)
        buf.write('Hey!')
        ```
    * 就如同我們也可以使用陣列(array)語法(syntax)來存取(access)`buffer`(緩存),我們也能利用同樣的方式(in the same way)來設定(set)`buffer`(緩存)的內容(contents)
        ``` js
        const buf = Buffer.from('Hey!')
        buf[1] = 111 //o
        console.log(buf.toString()) //Hoy!
        ```
  + 複製一個`buffer`(緩存) (Copy a buffer)
    * 可利用[buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])](https://nodejs.org/api/buffer.html#buffer_buf_copy_target_targetstart_sourcestart_sourceend)方法來複製一個`buffer`(緩存)
        ``` js
        const buf = Buffer.from('Hey!')
        let bufcopy = Buffer.alloc(4) //allocate 4 bytes
        buf.copy(bufcopy)
        ```
    * 在預設的情況下,我們將複製整個`buffer`(緩存)。`buffer.copy()`方法內的3個參數(parameters)可以讓我們定義(define)
      * 起始位置(starting position)
      * 結束位置(ending position)
      * 新的`buffer`長度(the new buffer length)
        ``` js
        const buf = Buffer.from('Hey!')
        let bufcopy = Buffer.alloc(2) //allocate 2 bytes
        buf.copy(bufcopy, 0, 0, 2)
        bufcopy.toString() //'He'
        ```
  + 將`buffer`(緩存)切片 (Slice a buffer)
    * 如果我們想建立(create)一個部分(partial)可視化(visualization)的`buffer`(緩存),我們可以建立一個切片(slice)。 **切片(slice)"並不是"複製(copy): 原始(original)`buffer`(緩存)仍然是事實來源(the source of truth),但如果原始緩存被改變時,我們所建立的`buffer`切片(slice)也會被改變**
    * 可利用[buf.slice([start[, end]])](https://nodejs.org/api/buffer.html#buffer_buf_slice_start_end)方法來建立一個`buffer`(緩存)切片,此方法的第1個參數(parameter)代表起始位置(starting position),以及可指定(specify)第2個選擇性(optional)的參數(parameter)作為結束位置(ending position)
        ``` js
        const buf = Buffer.from('Hey!')
        buf.slice(0).toString() //Hey!
        const slice = buf.slice(0, 2)
        console.log(slice.toString()) //He
        buf[1] = 111 //o
        console.log(slice.toString()) //Ho
        ```

### Node.js Streams
> Node內建核心模組[Stream](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_stream)<br>

- 什麼是`streams`(串流)? (What are streams?)
  + `Streams`(串流)是其中一個為Node應用程式(applications)提供動力(power)的基本(fundamental)觀念(concept)之一
  + `Streams`(串流)是一個用來處理(handle)讀寫(reading/writing)檔案(files)、網路通訊(network communications)、或是任何(any kind of)端對端(end-to-end)的資訊交流(information exchange)的一種有效率(efficient)的方式(way)
  + `Streams`(串流)並不是Node特有(unique)的概念(concept)。早在幾十年(decades)以前左右,`Streams`(串流)就已經被`Unix`作業系統(operating system)所引進(introduced)了,因此程式(program)就能透過管道運算子(pipe operator, => 也就是`|`)來與每一個通過(passing)的`streams`(串流)做互動
  + 舉例來說,若是傳統(traditional)的方法(way),當我們告訴程式(program)要讀取(read)一個檔案(file)時,檔案會被讀取到記憶體(memory)中,從開始(start)到結束(finish),然後我們就能處理(process)它
  + 透過`streams`(串流)來將檔案內容一部分一部分(piece by piece)地讀取(read),而不用在處理該檔案內容時,需要先把整個(all)檔案內容事先讀取到記憶體(memory)中
  + Node的[Stream](https://nodejs.org/api/stream.html#stream_stream)內建核心模組提供(provides)了能建構(built)出所有`streams`(串流)API的基礎(foundation)
  + 所有的`streams`(串流)都是[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)類別(class)的實例(instances)
- 為什麼要使用`streams`(串流)? (Why streams?)
  + 在資料處理(data handling)的方法(methods)這方面,`Streams`(串流)基本上提供了以下2個主要(major)的優點(advantages)
    * 記憶體效率(Memory efficiency): 當我們處理(process)資料時,不需要事先(before)將大量的資料(large amounts of data)讀取(load)到記憶體中(in memory)
    * 時間效率(Time efficiency): 開始處理(processing)資料(data)的前置時間縮短(less time)。從(since)我們擁有資料後,我們就可以直接進行處理,而不是(rather than)等待整個資料負載(data payload)為可用(available)的時候才能開始處理
- 一個`streams`(串流)的範例 (An example of stream)
  + 有一個典型的範例就是從硬碟(disk)讀取資料(reading file)
  + 利用Node的`fs`模組,我們可以讀取一個檔案,並在當有新被建立(established)的連線(connection)與我們的HTTP伺服器(server)連線時,提供文件(serve it)
      ``` js
      const http = require('http')
      const fs = require('fs')

      const server = http.createServer(function(req, res) {
        fs.readFile(__dirname + '/data.txt', (err, data) => {
          res.end(data)
        })
      })
      server.listen(3000)
      ```
      * [fs.readFile(path[, options], callback)](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_readfile_path_options_callback): 此方法會讀取(reads)整個檔案內容(full contents of the file),當此方法完成時,會呼叫(invokes)回呼函式(callback)
      * [response.end([data[, encoding]][, callback])](https://nodejs.org/dist/latest-v15.x/docs/api/http.html#http_response_end_data_encoding_callback): 此方法會回傳(return)整個檔案內容(full contents)給HTTP客戶端(client)
      * 如果這個檔案很大的話,此操作會將花費大量的時間
  + 以下的範例程式碼是用`streams`(串流)來完成同樣的事情
      ``` js
      const http = require('http')
      const fs = require('fs')

      const server = http.createServer((req, res) => {
        const stream = fs.createReadStream(__dirname + '/data.txt')
        stream.pipe(res)
      })
      server.listen(3000)
      ```
    * 我們沒有等待整個檔案被完全讀取(fully read),而是當我們有已經準備好(ready)的大量數據(a chunk of data)要被傳送時,就可以立即開始利用串流(streaming)的方式傳輸到HTTP客戶端(client)
- [readable.pipe(destination[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pipe_destination_options)方法的介紹
  + 上面的範例使用`stream.pipe(res)`方法,而這個方法(method)會在檔案串流(file stream)上被呼叫(called)
  + 這個方法是用來做什麼的? 它會獲得(takes)消息來源(source),並用管道(`pipe`)的方式來傳送到目的地(destination)
  + 我們也可以在消息來源串流(source stream)上呼叫(call)`stream.pipe(res)`這個方法,因此在這種情況下,檔案通常會被用管道(`pipe`)的方式來傳送到HTTP的回應物件(`response`)中
  + `stream.pipe(res)`這個方法回傳的值就是目的地串流(destination stream),而它是一個非常方便(convenient)的東西來讓我們可以串連(chain)多個(multiple)管道(`pipe`)呼叫(calls)
      ``` js
      src.pipe(dest1).pipe(dest2)
      ```
    * 以上做法的構想(construct)會跟以下這種方式是相同的
      ``` js
      src.pipe(dest1)
      dest1.pipe(dest2)
      ```
- 利用串流驅動的Node APIs (Streams-powered Node.js APIs)
  + 因為這些優點(advantages),許多Node的內建核心模組(core module)都有提供(provide)原生(native)的串流(`stream`)處理(handling)能力(capabilities),尤其(notably)是以下幾種方式
    * [process.stdin](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_stdin): 此屬性會回傳(returns)一個`streams`(串流)來連接(connected)到標準輸入(`stdin`)
    * [process.stdout](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_stdout): 此屬性會回傳(returns)一個`streams`(串流)來連接(connected)到標準輸出(`stdout`)
    * [process.stderr](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_stderr): 此屬性會回傳(returns)一個`streams`(串流)來連接(connected)到標準錯誤(`stderr`)
    * [fs.createReadStream(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_createreadstream_path_options): 此方法會建立(creates)一個可讀取(readable)的檔案串流(stream to a file)
    * [fs.createWriteStream(path[, options])](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_createwritestream_path_options): 此方法會建立(creates)一個可寫入(writable)的檔案串流(stream to a file)
    * [net.connect()](https://nodejs.org/dist/latest-v15.x/docs/api/net.html#net_net_connect): 此方法會開始(initiates)一個以`streams`為基礎(stream-based)的連線(connection)
    * [http.request(url[, options][, callback])](https://nodejs.org/dist/latest-v15.x/docs/api/http.html#http_http_request_url_options_callback): 此方法會回傳(returns)一個`http.ClientRequest`類別(class)的實例(instance),而這個實例會是一個可寫入(writable)的串流(`stream`)
    * [zlib.createGzip([options])](https://nodejs.org/dist/latest-v15.x/docs/api/zlib.html#zlib_zlib_creategzip_options): 此方法會利用`gzip`這種壓縮演算法(compress algorithm)來將資料(data)壓縮(compress)進`streams`(串流)裡面(into)
    * [zlib.createGunzip([options])](https://nodejs.org/dist/latest-v15.x/docs/api/zlib.html#zlib_zlib_creategunzip_options): 此方法會解壓縮(decompress)一個`gzip`串流(`stream`)
    * [zlib.createDeflate([options])](https://nodejs.org/dist/latest-v15.x/docs/api/zlib.html#zlib_zlib_createdeflate_options): 此方法會利用`deflate`這種壓縮演算法(compress algorithm)來將資料(data)壓縮(compress)進`streams`(串流)裡面(into)
    * [zlib.createInflate([options])](https://nodejs.org/dist/latest-v15.x/docs/api/zlib.html#zlib_zlib_createinflate_options): 此方法會解壓縮(decompress)一個`deflate`串流(`stream`)
- 不同類型的串流(`stream`) (Different types of streams)
  + 串流(`stream`)會有以下4種類別(classes)
    * `Readable`: 可讀取串流(readable stream)是一個我們可以透過管道傳出(pipe from),而不能傳入(pipe into)的`stream`(串流); 也就是說,我們可以利用可讀取串流(readable stream)來接收資料(receive data),而不能傳送資料(send data)給它。當我們推送(push)資料(data)進入(into)可讀取串流(readable stream)時,這些資料是緩存(buffered)的,直到(until)使用者(consumer)開始讀取(starts to read)這些資料(data)
    * `Writable`: 可寫入串流(writable stream)是一個我們可以透過管道傳入(pipe into),而不能傳出(pipe from)的`stream`(串流); 也就是說,我們可以利用可寫入串流(writable stream)來傳送資料(send data),而不能傳入資料(receive data)給它。
    * `Duplex`: 雙向串流(duplex stream)是一個既能接受我們透過管道傳入(pipe into),也能接受我們透過管道傳出(pipe from)。基本上來說(basically),就是可讀取串流(readable stream) & 可寫入串流(writable stream)的結合(combination)
    * `Transform`: 轉換串流(transform stream)類似於雙向串流(duplex stream),但是轉換串流(transform stream)的輸出(output)是它的輸入(its input)的轉換(transform)
- 如何建立一個可讀取串流(readable stream)? (How to create a readable stream)
  + 我們可以透過Node的`Stream`內建核心模組來獲得(get)一個可讀取串流(readable stream),接著我們再初始化(initialize)它並且實作(implement)它的[readable._read(size)](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size_1)方法(method)
  + 首先,我們先建立(create)一個串流物件(stream object)
      ``` js
      const Stream = require('stream')
      const readableStream = new Stream.Readable()
      ```
  + 接著,實作(implement)它的[readable._read(size)](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size_1)方法(method)
      ``` js
      readableStream._read = () => {}
      ```
  + 我們也可以利用`read`這個選項(option)來實作(implement)`readable._read(size)`方法(method)
      ``` js
      const readableStream = new Stream.Readable({
        read() {}
      })
      ```
  + 現在(Now),`stream`(串流)是被初始化過後(initialized)的,我們可以傳送資料(send data)給它
      ``` js
      readableStream.push('hi!')
      readableStream.push('ho!')
      ```
- 如何建立一個可寫入串流(writable stream)? (How to create a writable stream)
  + 為了建立(create)出一個可寫入串流(writable stream),我們先繼承(extend)基礎(base)[Writable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_streams)物件(object),接著我們會實作(implement)這個`Writable`物件的[writable._write(chunk, encoding, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_write_chunk_encoding_callback_1)方法(method)
  + 首先,我們先建立(create)一個串流物件(stream object)
      ``` js
      const Stream = require('stream')
      const writableStream = new Stream.Writable()
      ```
  + 接著,實作(implement)它的[writable._write(chunk, encoding, callback)](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_write_chunk_encoding_callback_1)方法(method)
      ``` js
      writableStream._write = (chunk, encoding, next) => {
        console.log(chunk.toString())
        next()
      }
      ```
  + 現在(Now),我們就可以透過管道傳入(pipe into)一個可讀取串流(readable stream)進去
      ``` js
      process.stdin.pipe(writableStream)
      ```
- 如何從可讀取串流中(readable stream)獲得資料? (How to get data from a readable stream?)
  + `Question`: 我們該如何從可讀取串流中(readable stream)獲得資料呢?
    * `Answer`: 利用可寫入串流(writable stream)
      ``` js
      const Stream = require('stream')

      const readableStream = new Stream.Readable({
        read() {}
      })
      const writableStream = new Stream.Writable()

      writableStream._write = (chunk, encoding, next) => {
        console.log(chunk.toString())
        next()
      }

      readableStream.pipe(writableStream)

      readableStream.push('hi!')
      readableStream.push('ho!')
      ```
  + 我們也可以直接地(directly)消耗(consume)一個可讀取串流(readable stream),可利用`readable`事件(event)
      ``` js
      readableStream.on('readable', () => {
        console.log(readableStream.read())
      })
      ```
- 如何傳送資料給可寫入串流(writable stream)? (How to send data to a writable stream)
  + 可利用[writable.write(chunk[, encoding][, callback])](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_write_chunk_encoding_callback)方法(method)
      ``` js
      writableStream.write('hey!\n')
      ```
- 當我們結束寫入操作時,該如何對可寫入串流(writable stream)發送一個信號呢? (Signaling a writable stream that you ended writing)
  + 可利用[writable.end([chunk[, encoding]][, callback])](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_end_chunk_encoding_callback)方法(method)
      ``` js
      const Stream = require('stream')

      const readableStream = new Stream.Readable({
        read() {}
      })
      const writableStream = new Stream.Writable()

      writableStream._write = (chunk, encoding, next) => {
        console.log(chunk.toString())
        next()
      }

      readableStream.pipe(writableStream)

      readableStream.push('hi!')
      readableStream.push('ho!')

      writableStream.end()
      ```

### Node.js, the difference between development and production
- 我們可以對生產環境(production environment)、開發環境(development environment)做不同的設定(configurations)
- Node會假設(assumes)我們總是在開發環境中(development environment)執行。這時,我們可以透過設定Node的環境變數(environment variable)為"生產環境(production environment)",來向Node發出我們在生產環境執行(running)應用程式的信號(signal)
  + ```bash
      export NODE_ENV=production
      ```
    * 以上的指令是在`shell`中執行的,但是最好將這個指令加到我們的`shell`設定檔(例: `.bash_profile`)中,否則當我們重開機(system restart)時,在終端機上執行指令所產生的Node環境變數設定就"不會"被保留(not persist)
- 我們也可以透過以下的另一種方式來設定Node環境變數(environment variable),就是在執行Node應用程式初始化(initialization)的指令(command)前面,前綴(prepending)Node環境變數
  + ```console
      NODE_ENV=production node app.js
    ```
- Node環境變數的設定是一種慣例(convention),也被廣泛(widely)地被使用(used in)在外部函式庫(external libraries)中
- 將Node環境變數設定為 **生產環境** ,通常(generally)可確保(ensures)以下2件事情
  + 日誌記錄(logging)保持(kept)在最少(minimum)、最必要(essential)的水平(level)
  + 會發生(take place)更多的緩存級別(caching levels),以優化(optimize)效能(performance)
- 舉例來說,[Express](https://expressjs.com/)所使用的模板函式庫(templating library)--->[pug](https://pugjs.org/api/getting-started.html),會在當Node環境變數 **不是** 被設定為生產環境(`production`)時,就會以除錯模式(in debug mode)來編譯(compiles)
  + 在開發模式(development mode)中,`Express`的`views`會在每次(every)請求(`request`)時被編譯(compiled); 然而當在生產模式(production mode)中,則會將其緩存(cached)。以下會有更多的範例(examples)
  + 我們可以利用條件陳述句(conditional statements)來切換在不同環境(in different environments)中執行(execute)程式碼
      ``` js
      if (process.env.NODE_ENV === "development") {
        //...
      }
      if (process.env.NODE_ENV === "production") {
        //...
      }
      if(['production', 'staging'].indexOf(process.env.NODE_ENV) >= 0) {
        //...
      })
      ```
  + 舉例來說,在`Express`框架應用程式(app)中,我們可以利用條件陳述句(conditional statements)來針對每個環境(per environment)設定(set)不同的錯誤處理器(error handler)
      ``` js
      if (process.env.NODE_ENV === "development") {
        app.use(express.errorHandler({ dumpExceptions: true, showStack: true }))
      })

      if (process.env.NODE_ENV === "production") {
        app.use(express.errorHandler())
      })
      ```

### Error handling in Node.js
> Node內建核心模組[Errors](https://nodejs.org/api/errors.html#errors_errors)<br>

- 在Node中,是透過(through)例外(`exceptions`)來處理(handled)錯誤(errors)的
- 建立一個例外 (Creating exceptions)
  + 要建立一個例外,可以透過拋出(`throw`)關鍵字(keyword)
      ``` js
      throw value
      ```
  + 一旦Javascript執行(executes)到此行(this line)指令,正常(normal)的程式流程(program flow)就會被停止(halted),並且程式的控制權(control)會被保留(held back)給最近(nearest)的例外處理器(`exception handler`)
    * 通常,在客戶端(client-side)的程式碼(code)中,以上範例中的`value`可以是任何(any)Javascript的值,包含一個字串(`string`)、數字(`number`)、或是物件(`object`)
    * **在Node中,我們"不會"拋出(throw)字串(strings),我們只會拋出(throw)錯誤物件(`Error` objects)**
- 錯誤物件 (Error objects)
  + 在Node中,錯誤物件(`error` object)可以是一個錯誤物件(`Error` object)的實例(instance),或是繼承(extends)自Node的`Errors`內建核心模組內所提供(provided)的錯誤類別(`Error` class)的物件(object)
      ``` js
      throw new Error('Ran out of coffee')
      ```
    * 或是
      ``` js
      class NotEnoughCoffeeError extends Error {
        //...
      }
      throw new NotEnoughCoffeeError()
      ```
- 處理例外 (Handling exceptions)
  + 一個例外處理器(exception handler)就是`try/catch`陳述句(statement)
  + 任何包在(included)`try`區塊(block)內的程式碼所引發(raised in)的例外(exception),都會在其相對應(corresponding)的`catch`區塊(block)中被處理(handled)
      ``` js
      try {
        //lines of code
      } catch (e) {}
      ```
      * 在以上的範例程式碼中,`e`就是例外值(`exception` value)。我們可以在`catch`區塊(block)中新增(add)多個(multiple)錯誤處理器(handlers),這些錯誤處理器就可以用來捕獲(`catch`)各種不同的錯誤(different kinds of errors)
- 捕獲未捕獲的例外 (Catching uncaught exceptions)
  + 當程式(program)正在執行中時,如果在程式中有未捕獲的例外(uncaught exception)被拋出(thrown)時,我們的程式將會崩潰(crash)
  + 為了解決這個問題,我們可以監聽`process`物件上的[uncaughtException](https://nodejs.org/api/process.html#process_event_uncaughtexception)事件
      ``` js
      process.on('uncaughtException', err => {
        console.error('There was an uncaught error', err)
        process.exit(1) //mandatory (as per the Node.js docs)
      })
      ```
    * 我們不需要為此事先匯入(import)Node的`process`內建核心模組(core module),因為`process`模組是Node會自動(automatically)注入(injected)的
- `Promise`物件的例外 (Exceptions with promises)
  + **我們可以利用`Promise`物件來串連(chain)不同的操作(different operations),並且在最後(at the end)處理錯誤(handle errors)**
      ``` js
      doSomething1()
        .then(doSomething2)
        .then(doSomething3)
        .catch(err => console.error(err))
      ```
  + 那我們該怎麼知道錯誤(`error`)是在哪裡發生(occured)的呢? 其實我們並不需要真的(really)知道這件事,我們可以在每個(each)被呼叫(call)的函式(functions)中,都處理(handle)錯誤(error)(=> 也就是以上範例中的`doSomethingX`),並且在錯誤處理器中(inside the `error` handler)拋出(throw)一個錯誤(`error`),而這個被拋出的錯誤會被用來呼叫(call)外面(outside)的`catch`錯誤處理器(handler)
      ``` js
      const doSomething1 = () => {
        //...
        try {
          //...
        } catch (err) {
          //... handle it locally
          throw new Error(err.message)
        }
        //...
      }
      ```
  + 為了能夠(be able to)在本地(locally)處理錯誤(handler `errors`)而不用(without)在我們呼叫(call)函式(function)時才處理(handling),我們可以跳出(break)串連(chain),並且透過在每個(each)`then()`函式後面建立(create)一個函式(function)來處理(process)例外(exception)
      ``` js
      doSomething1()
        .then(() => {
          return doSomething2().catch(err => {
            //handle error
            throw err //break the chain!
          })
        })
        .then(() => {
          return doSomething2().catch(err => {
            //handle error
            throw err //break the chain!
          })
        })
        .catch(err => console.error(err))
      ```
- 利用`async/await`語法來處理例外 (Error handling with async/await)
  + 我們可以利用`async/await`語法來處理例外(exceptions),但我們仍然需要捕抓(catch)錯誤(`errors`),我們可以利用以下的方式(way)來完成
    ``` js
    async function someFunction() {
      try {
        await someOtherFunction()
      } catch (err) {
        console.error(err.message)
      }
    }
    ```

### How to log an object in Node.js
> Node內建核心模組[Console](https://nodejs.org/api/console.html#console_console)<br>

- 當我們在瀏覽器(in the browser)的Javascript程式碼中輸入(type)`console.log()`語法時,就會在瀏覽器後台(browser console)建立(create)一個漂亮(nice)的條目(entry)
  + ![console-log-browser](../assets/pics/nodejs/console-log-browser.png)
- 當我們點擊(click)箭頭(arrow)時,日誌(log)就會被展開(expanded),這時我們就可以清楚地(clearly)看到物件(object)的屬性們(properties)
  + ![console-log-browser-expanded](../assets/pics/nodejs/console-log-browser-expanded.png)
- 在Node中,也會發生(happens)相同(same)的事情
  + 當我們要記錄(log)某些內容到後台(console)時,並不能那麼奢侈的(luxury)。因為如果我們是手動(manually)執行(run)Node程式(program)的話,它將會輸出(output)日誌物件(object)到`shell`或是到日誌檔案(log file)中。這時,我們將會得到物件的字串表現形式(string representation of the object)
  + 現在,直到(until)一定的(certain)巢狀程度級別(level of nesting)之前,一切都是很好的(fine)。但是,從第二層(two levels of)的巢狀物件結構(nesting)以後(after),Node就會放棄(gives up)並且打印(print)出`[Object]`作為佔位符(placeholder)
    ``` js
    const obj = {
      name: 'joe',
      age: 35,
      person1: {
        name: 'Tony',
        age: 50,
        person2: {
          name: 'Albert',
          age: 21,
          person3: {
            name: 'Peter',
            age: 23
          }
        }
      }
    }
    console.log(obj)


    {
      name: 'joe',
      age: 35,
      person1: {
        name: 'Tony',
        age: 50,
        person2: {
          name: 'Albert',
          age: 21,
          person3: [Object]
        }
      }
    }
    ```
  + 那麼,我們該如何打印(print)出整個物件(whole object)呢?
    * 最佳的做法(best way)會是在(while)維持(preserving)漂亮的打印(pretty print)輸出結果時,同時使用Javascript可支援的[JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)方法(method)
        ``` js
        console.log(JSON.stringify(obj, null, 2))
        ```
        * 這個`2`是用來作為指定縮排(indentation)的空格數(number of spaces)
    * 另一個可供選擇(option)的做法是利用Node的[Utilities](https://nodejs.org/api/util.html#util_util)內建核心模組中的[util.inspect(object[, showHidden[, depth[, colors]]])](https://nodejs.org/api/util.html#util_util_inspect_object_showhidden_depth_colors)方法(method)
      * 這個方法(method)可以指定以下的參數們
        * `depth`(深度): 要展開到這個物件的第幾層
          * 預設值: `2`
        * `colors`: 可以客製化地設定文字輸出的顏色
      * 但`util.inspect()`這個方法會遇到的問題是,超過(after)2層(level)以上的巢狀物件(nested objects)會被攤平(flattened),這也會使原本有就比較複雜(complex)結構的物件(objects)會變得更複雜

### Node.js with TypeScript
- 什麼是`Typescript`? (What is TypeScript)
  + `Typescript`是一個由Microsoft開發與維護的開源、熱門的程式語言,它受到全世界許多軟體開發者的喜愛和使用
  + 基本上(Basically), **`Typescript`是Javascript這個程式語言的一個超集(`superset`)** ,並新增了一些能力(capabilities)給這個程式語言。最著名(notable)的新增(addition)功能就是 **靜態型別定義(static type definitions)** ,而這是一般(plain)的Javascript所沒有的功能。受型別(types)所惠,我們可以宣告(declare) **我們預期什麼形式的參數** (what kind of arguments we are expecting)、 **函式確切會回傳什麼東西** (what is returned exactly in our functions)、 **我們建立的物件的確切樣子** (what's the exact shape of the object that we are creating)
  + `Typescript`是一個非常強大(powerful)的工具(tool),並且它開啟(opens)了Javascript專案(project)可能性(possibilites)的新世界(new world)。它讓我們的程式碼能在提交(before code is even shipped)之前更安全(secure)、穩固(robust),當(during)我們在撰寫程式碼時,`Typescript`會捕抓問題(catches problems),並極好地(wonderfully)整合(integrates)到程式碼編輯器(code editors)中(例: `Visual Studio Code`)
  + 我們可以在之後再來討論(talk about)`Typescript`的好處(benefits),現在先讓我們先看看一些範例(examples)程式碼吧
  + 瞧瞧以下的程式碼片段(code snippet),接著我們就可以將它們拆解(unpack)再一起(together)了
      ``` js
      type User = {
        name: string;
        age: number;
      };

      function isAdult(user: User): boolean {
        return user.age >= 18;
      }

      const justine: User = {
        name: 'Justine',
        age: 23,
      };

      const isJustineAnAdult: boolean = isAdult(justine);
      ```
      * 首先,`type`關鍵字的第一部份負責(responsible)宣告(declaring)代表(representing)使用者自己客製化(custom)的物件型別
      * 之後,我們會利用(utilize)這個新(newly)建立的型別來建立一個新的函式(function)`isAdult()`,該函式能接受(accpets)一個`User`型別的`user`參數(argument),並回傳(returns)一個布林值(`boolean`)
      * 接著,我們建立了`justine`這個`user`,它會是我們的範例資料以用來呼叫(calling)之前(previously)定義(defined)的函式(function)
      * 最後,我們會建立(create)一個新的帶有是否`justine`為成年人(adult)的這個資訊(information)的變數(vaiable)
      * 這個範例還有一些我們也應該需要額外(additional)知道的事情
        * 首先,如果我們沒有遵守(comply with)宣告(declared)好的型別(types)時,`Typescript`會警告(alarm)我們就會警告我們有些東西出錯(wrong)了,以防止(prevent)誤用(misuse)
        * 第二點是,不是每個東西都需要明確地(explicitly)定義型別(typed)---`Typescript`是非常聰明(smart)的,並且能為我們推斷(deduce)型別(types)。例如: 即使(even if)我們沒有明確地(explicitly)輸入(type),`isJustineAnAdult`也會是一個布林(`boolean`)型別,或者即使(even if)我們沒有宣告(declare)`justine`為`User`型別(type),它也會變成一個對於函式(function)來說有效(valid)的參數(argument)
  + Okay! 所以我們已經有一些`Typescript`程式碼了。那麼,該怎麼使用呢?
    * 首先,我們需要在我們的專案中先安裝npm上的[typescript](https://www.npmjs.com/package/typescript)
      * $ `npm install typescript`
    * 現在,我們可以在終端機(terminal)利用`tsc`指令來將`Typescript`程式碼編譯(compile)成Javascript程式碼。讓我們開始來動手做吧
      * 假設我們有個檔案的名稱叫做`example.ts`,那麼在終端機的指令會像是
        * $ `tsc example.ts`
        * 這個指令會導致(result in)一個新(new)的檔案(file)叫做`example.js`,這樣我們就可以使用Node來執行了
    * 現在,當我們知道如何編譯(how to compile) & 如何執行`Typescript`程式碼(run TypeScript code)了以後,那就讓我們來看看`Typescript`的防止錯誤功能(bug-preventing capabilities)
      * 以下是我們如何修改(modify)我們原本的程式碼(code)
          ``` js
          type User = {
            name: string;
            age: number;
          };

          function isAdult(user: User): boolean {
            return user.age >= 18;
          }

          const justine: User = {
            name: 'Justine',
            age: 'Secret!',
          };

          const isJustineAnAdult: string = isAdult(justine, "I shouldn't be here!");
          ```
      * 接下來,以下會是`Typescript`會回報我們的事情
        ``` js
        example.ts:12:3 - error TS2322: Type 'string' is not assignable to type 'number'.

        12   age: "Secret!",
            ~~~

          example.ts:3:3
            3   age: number;
                ~~~
            The expected type comes from property 'age' which is declared here on type 'User'

        example.ts:15:7 - error TS2322: Type 'boolean' is not assignable to type 'string'.

        15 const isJustineAnAdult: string = isAdult(justine, "I shouldn't be here!");
                ~~~~~~~~~~~~~~~~

        example.ts:15:51 - error TS2554: Expected 1 arguments, but got 2.

        15 const isJustineAnAdult: string = isAdult(justine, "I shouldn't be here!");
                                                            ~~~~~~~~~~~~~~~~~~~~~~


        Found 3 errors.
        ```
      * 正如同我們所看到(As you can see)的,`Typescript`成功地(successfully)防止(prevent)我們交付(shipping)的程式碼(code)無法如預期地(unexpectedly)運作(work)。這真是太棒了!
- 關於`Typescript`的更多事情 (More about TypeScript)
  + `Typescript`提供(offers)了大量(a whole lot of )其他(others)很棒的機制(mechanisms),例如: 介面(`interfaces`)、類別(`classes`)、實用程序型別(`utility types`)...等等。此外,在較大的專案(on bigger projects)中,我們可以在一個單獨的檔案(a separate file)中宣告(declare)我們自己的`Typescript`編譯器設定(compiler configuration),並細化(granularly)調整(adjust)其工作方式,像是嚴格程度(how strict) & 會將已編譯檔案儲存在哪裡(where it stores compiled files for example)
    * 我們可以到[Typescript的官方文件](https://www.typescriptlang.org/docs/)以閱讀更多(read more about)這個令人驚嘆(awesome)的東西(stuff)
  + `Typescript`有一些其它的好處(other benefits)是值得一提的(worth mentioning),像是`Typescript`可以被漸進式地(progressively)採用(adopted),它能讓程式碼變得更易讀(readable) & 更好理解的(understandable),並且它也允許(allows)開發者(developer)在較舊的Node版本中仍能使用現代化(modern)的語言功能(language features)
- 在Node世界中的`Typescript` (TypeScript in the Node.js world)
  + 在Node的世界中,`Typescript`已經樹立了良好的典範,並且已經被許多公司、開源軟體專案、工具、框架...等等所採用
  + 以下是一些著名的開源軟體專案例子,有使用到`Typescript`的
    * [NestJS](https://nestjs.com/): 它是一個健全(robust)且功能完善(fully-featured)的框架(framework),並且能夠輕鬆(easy)、愉快(pleasant)地建立可擴展的(scalable) & 結構完善(well-architected)的系統(systems)
    * [TypeORM](https://typeorm.io/#/): 優秀(great)的`ORM`,它有受到其它語言(other languages)使用的知名工具(well-known tools)(像是: Java的[Hibernate](https://hibernate.org/)、PHP的[Doctrine](https://www.doctrine-project.org/)、.NET的[Entity Framework](https://docs.microsoft.com/zh-tw/ef/))的影響(influenced)
    * [RxJS](https://rxjs.dev/): 廣泛地(widely)被運用(used)在互動式(reactive)程式設計(programming)的函式庫(library)
    * 以及許許多多的優秀的開源軟體專案之中,甚至(even)可能我們的專案就會成為下一個


---
## Node.js 核心模組
### [HTTP](https://nodejs.org/api/http.html)
- 要使用HTTP server & client,必須要先`require('http')`
- Node的HTTP介面(interfaces)旨在支援HTTP協定的許多功能
,因為這些功能傳統上較難使用,尤其是在大量.大塊(chunk-encoded)的訊息
  + 該介面非常地小心,永遠不會緩衝(buffer)整個請求(requests)和回應(responses),所以使用者可以傳送實時資料流(stream data)
- HTTP訊息標頭(message headers)通常用物件(object)來表示
  + 範例`HTTP message header`
    ``` js
    { 'content-length': '123',
      'content-type': 'text/plain',
      'connection': 'keep-alive',
      'host': 'mysite.com',
      'accept': '*/*' 
    }
  + 鍵(keys)要用小寫表示,值(values)是按照原本的不變
- 為了能支援所有可能的HTTP應用程式,Node的HTTP API是屬於非常底層的,它只處理資料流(stream handling),但是不解析實際的標頭(headers)或是正文(context)
  + 可參考[message.headers](https://nodejs.org/api/http.html#http_message_headers)以了解該如何處理重複標頭(duplicate headers)的細節
- 收到的原始標頭會保留在[message.rawHeaders](https://nodejs.org/api/http.html#http_message_rawheaders)屬性值中,並以陣列(array)的形式來儲存該資訊,像是`[key, value, key2, value2, ...]`
  + 以上述的`HTTP message header`為例,它的`message.rawHeaders`的值會像是以下的陣列(array)形式
    ``` js
    [ 'ConTent-Length', '123456',
      'content-LENGTH', '123',
      'content-type', 'text/plain',
      'CONNECTION', 'keep-alive',
      'Host', 'mysite.com',
      'accepT', '*/*'
    ]
    ```
> method
  + [http.createServer([options][, requestListener])](https://nodejs.org/api/http.html#http_http_createserver_options_requestlistener)<br>
    * 會回傳一個 http.Server 實例
    * options
      * http.IncomingMessage
      * ServerResponse
      * insecureHTTPParser (Default: false)
      * maxHeaderSize (Default: 16384 (16KB)
    * requestListener (Function)
> Class
  + [Class: http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)
    * 這個Class物件會由http.server與http.ClientRequest所建
    * 它是用來作為被傳給request event & response event的第1個參數
    * 它會被用來存取回應物件的狀態(response status),標頭(response headers),資料(response data)
  + [Class: http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)
    * 這個Class物件會由HTTP server內部自動建立,而不是透過user來建立的
    * 它是用來作為被傳給request event的第2個參數
    > property
      * [response.statusCode](https://nodejs.org/api/http.html#http_response_statuscode)
        * 當使用隱式標頭(implicit headers)時,也就是當沒有明確地使用[response.writeHead()](https://nodejs.org/api/http.html#http_response_writehead_statuscode_statusmessage_headers)時,這個屬性會在headers被更新時決定要傳給client端什麼狀態碼(status code)
        * 當回應標頭(response header)已經傳到client端之後,這個屬性會指出已經發送出去的狀態碼
        * 預設是200 (number型別)
        * 例: `response.statusCode = 404;`
    > method
      * [response.setHeader(name, value)](https://nodejs.org/api/http.html#http_response_setheader_name_value)
        * args
          * name: (string)
          * value: (any)
          * Returns: [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)  
        * 回傳一個回應物件(response object)
        * 為隱式標頭(implicit header)設定一筆單一的值,若此標頭已經存在於待發送的header中,那麼待發送header的值就會被取代掉
        * 若要發送為同一個名稱的多個header時,可以用一個Array['xxx', 'yyy']來包住所有的header值
          * 如果輸入非字串型別的值,將自動被儲存下來,而無須多做修改
          * 當header的name或是value包含無效的字元時,就會引發TypeError錯誤
        * 例: `response.setHeader('Content-Type', 'text/html');`
        * 例: `response.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);`
        * 當headers被設定為使用response.setHeader(),它們將會被合併到其他所有要傳給
        [response.writeHead()](https://nodejs.org/api/http.html#http_response_writehead_statuscode_statusmessage_headers)的headers,並且會自動將要傳給response.writeHead()的headers列為優先
          * 若需要逐步新增headers,讓未來有需要的話可以檢索和修改,請使用response.setHeader(),而不要使用response.writeHead()
          ``` js
          // Returns content-type = text/plain
          const server = http.createServer((req, res) => {
            res.setHeader('Content-Type', 'text/html');
            res.setHeader('X-Foo', 'bar');
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('ok');
          });
          ```
      * [response.end()](https://nodejs.org/api/http.html#http_response_end_data_encoding_callback)
        * 完整版: response.end([data[, encoding]][, callback]) 
        * args
          * data: (string || Buffer)
          * encoding: (string)
          * callback: (Function)
          * Returns: (this)
        * 這個方法會向server發送信號,表示所有的回應標頭(response header)和內文(body)皆已發送出去,這時server應認定該請求消息已完成
        * response.end()需要在每個回應的 **結尾** 都使用它
        * 如果data參數有給定的話,它實際上是去呼叫[response.write(data, encoding)](https://nodejs.org/dist/latest-v15.x/docs/api/http.html#http_response_write_chunk_encoding_callback),並接著執行res.end(callback)
        * 如果callback函式有給定的話,該callback函式會在回應串流(response stream)結束之後才會被呼叫並執行

### [Process](https://nodejs.org/dist/latest-v15.x/docs/api/process.html)
- `process`是一個全域的物件,針對當前的Node應用程序的進程(process),提供資訊與控制
  + 讀: 獲取process資訊(資源使用、執行環境、執行狀態)
  + 寫: 執行process操作(監聽事件、排程任務、發出警吿)
- 因為`process`身為global物件,所以可以在Node應用程式中直接使用,而無需事先require()。
  + 但它也還是能透過require()來匯入並使用這個模組
  + 例: $ `const process = require('process');`
> Process events
  + [Event: **exit**](https://nodejs.org/api/process.html#process_event_exit)
    * args
      * code: (integer)
    * 由於以下的任一個原因而導致Node應用程式即將退出時,將發出(emitted)該事件
      * 明確地呼叫`process.exit()`方法時
      * Node的事件循環(event loop)不再需要執行其他額外的工作時
    * 目前沒有方法可以防止在這個時間點要退出事件循環(event loop),一旦所有的 **exit** 監聽器(listener)執行結束後,就會終止Node的進程
    * 只要有給定退出碼(不管是透過`process.exitCode`屬性或是現在這個 **exit事件** 的`exitCode`參數)並傳遞給`process.exit()`方法時,該事件監聽回呼函式(listener callback function)就會被呼叫
      * 範例程式碼 
        ``` js
        process.on('exit', (code) => {
          console.log(`About to exit with code: ${code}`);
        });
        ```
    * 監聽功能 **必須只** 執行 **同步化的操作** ,以下的範例程式碼,該Node進程會在呼叫`exit`事件監聽器(event listeners)後 **就立即退出** ,因而導致任何仍在事件迴圈(event loop)中的佇列(queued)中的額外的工作都會被拋棄(abandoned)
      * 以下的程式碼為例,`setTimeout()`這個函式 **永遠不會** 被執行到
      * 情境說明( **錯誤示範** )
          ``` js
          process.on('exit', (code) => {
            setTimeout(() => {
              console.log('This will not run');
            }, 0);
          });
          ```
  + [Event: **Signal events**](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_signal_events)
    * 信號事件(Signal events)會在Node進程(process)收到信號時發出(emitted)
    * 信號(Signals)不能用在[Worker threads](https://nodejs.org/dist/latest-v15.x/docs/api/worker_threads.html#worker_threads_worker_threads)
    * 信號處理器(signal handler)會將收到的信號名稱(signal's name)做為第一個參數
      * 例: `SIGINT`, `SIGTERM`
    * 每個事件(event)的名稱將會是大寫的常用信號名稱
      * 例: `SIGINT` => SIGINT 信號們
      ``` js
      // Begin reading from stdin so the process does not exit.
      process.stdin.resume();

      process.on('SIGINT', () => {
        console.log('Received SIGINT. Press Control-D to exit.');
      });

      // Using a single function to handle multiple signals
      function handle(signal) {
        console.log(`Received ${signal}`);
      }

      process.on('SIGINT', handle);
      process.on('SIGTERM', handle);
      ```
    * `SIGTERM`與`SIGINT`在非Windows作業系統的環境中,擁有預設處理器可以在退出程式碼之前,重置終端機模式為 **128 + signal number**。如果這些信號之一已安裝了監聽器(listener),則將刪除其默認行為(Node應用程式也不再退出)
    * `SIGTERM`: 在Windows作業系統的環境中不支援,但可以監聽它(listened on)
    * `SIGINT`: 在終端機中是可被所有作業系統所支援的,它通常可以用`ctrl-C`來產生
    * `SIGKILL`: 不能安裝監聽器(listener),它將無條件終止Node應用程式,無論我們在哪個作業系統的環境中
    * 補充: 因為Windows作業系統並不支援信號(signals),所以不能說是等同於信號終止,但是Node有提供了一些仿效的做法,例如: [process.kill()](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_kill_pid_signal)與[subprocess.kill()](https://nodejs.org/dist/latest-v15.x/docs/api/child_process.html#child_process_subprocess_kill_signal)
      * 發送`SIGINT`,`SIGTERM`,`SIGKILL`將導致目標進程(target process)無條件被終止。此後,子進程(subprocess)將回報該進程(process)已被信號(signal)終止了
      * 發送信號(signal) `0` 可以用來做為一種獨立的平台以測試進程(process)的存在  
> method
- [process.cpuUsage([previousValue])](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_cpuusage_previousvalue)
  * args
    * previousValue: (Object)
      * 上一次呼叫process.cpuUsage()的回傳值
  * Returns: (Object)
    * user: (integer)
    * system: (integer)
  * 該方法會回傳一個具有`user`, `system`的物件(`Object`),來表示當前的進程(current process)的`user`和`system`的時間使用率
    * 這些值都是以百萬分之一秒為單位(微秒)--->時間單位
    * 這些值分別用來測量花在使用者端的程式碼＆系統端的程式碼的時間
    * 當有多個CPU內核在為這個進程(process)執行工作時,則這些值最終可能會大於實際經過的時間
  * 上一次呼叫`process.cpuUsage()`的結果可以作為參數傳遞給函式,以得知差異讀數(diff reading)
    ``` js
    const startUsage = process.cpuUsage();
    // { user: 38579, system: 6986 }

    // spin the CPU for 500 milliseconds
    const now = Date.now();
    while (Date.now() - now < 500);

    console.log(process.cpuUsage(startUsage));
    // { user: 514883, system: 11226 }
    ``` 
- [process.memoryUsage()](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_memoryusage)
  + Returns: (Object)
    * rss: (integer)
      * rss => (常駐集的大小,Resident Set Size),代表該進程(process)在主記憶體裝置中已被使用掉的內存記憶體空間(即總分配內存的子集); 包括所有的`C++`與`Javascript`的物件和程式碼
    * heapTotal: (integer)
      * 參考V8 engine的內存記憶體使用情況 
    * heapUsed: (integer)
      * 參考V8 engine的內存記憶體使用情況 
    * external: (integer)
      * 參考由V8 engine管理的`C++物件`綁定到`Javascript物件`的內存記憶體使用量 
    * arrayBuffers: (integer)
      * 參考分配到`ArrayBuffers` & `SharedArrayBuffers`,也包含所有Node應用程式的[Buffer](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html)物件
      * `arrayBuffers`也包含在`external`的值中
      * 當Node應用程式被用作嵌入式函式庫時,該值可能為0,是因為在這種情況下`arrayBuffers`可能不會被追蹤到
  + 該方法會回傳一個物件(`Object`)來描述Node應用程式的內存記憶體(memory)用量
    * 這個值會以`bytes`為單位來表示
  + 範例程式碼
    * 以下面的程式碼為例
        ``` js
        console.log(process.memoryUsage());
        ```
    * 會產生一個物件
      * `{
            rss: 4935680,
            heapTotal: 1826816,
            heapUsed: 650472,
            external: 49879,
            arrayBuffers: 9386
          }`
  + 當使用[Worker threads](https://nodejs.org/dist/latest-v15.x/docs/api/worker_threads.html#worker_threads_class_worker)時,`rss`將會是一個對於整個進程(entire process)有效的值(valid),而其他參數僅會參考到當前的進程
- process.resourceUsage()
  + Returns: (Object)
    * userCPUTime: (integer)
      * 對應到`ru_utime`的值,並以微秒為單位表示
      * 與process.cpuUsage().user的值相同
    * systemCPUTime: (integer)
      * 對應到`ru_stime`的值,並以微秒為單位表示
      * 與process.cpuUsage().system的值相同
  + 該方法會回傳一個物件(`Object`)來表示當前進程(current process)的資源使用率(resource usage); 以上所有該方法的回傳值都是來自於呼叫[uv_rusage_t struct](https://docs.libuv.org/en/v1.x/misc.html#uv_getrusage)的uv_getrusage()方法的回傳值
    > uv_getrusage(): Gets the resource usage measures for the current process.
- process.cwd()
  + Returns: (string)
    * 該方法會回傳當前進程(current process)所在的工作目錄的路徑(working directory)
  + 範例程式碼
      ``` js
      console.log(Current directory:  {process.cwd()});
      ```
- process.exit([code])
  + args
    * code: (integer)
      * 表示要指定的退出碼
      * 預設值: 0 (代表成功)
  + 該方法指示Node應用程式以同步地方式(synchronously)來終止進程(terminate the process)並顯示退出碼
    * 若[code]參數被省略,則會以`0`為退出碼或是`process.exitCode`的值為準(當該屬性在之前有被設定過的話)
    * Node應用程式不會在所有的[Event: 'exit'](https://nodejs.org/api/process.html#process_event_exit)事件監聽器(event listeners)被呼叫之前就終止掉
  + 範例程式碼
    * 以失敗(failure)的狀態碼來退出Node應用程式 
    * $ `process.exit(1);`
    * 在執行該Node應用程式的shell上,我們會看到退出碼(exit code)為`1`
  + 當我們呼叫`process.exit()`方法時,會強迫Node應用程式盡快地終止該進程,即便是還有pending狀態中的非同步操作尚未完全完成的情況,包括I/O操作,像是`process.stdout` & `process.stderr`
    * 在大多數的情況下,其實我們並不需要明確地呼叫`process.exit()`方法。因為在事件循環(event loop)當中沒有額外pending狀態中的工作的話,Node應用程式就會自行退出
    * 我們也可以透過設定好`process.exitCode`屬性的值,來告知進程在正常退出時,要使用哪個退出碼
  + 情境說明( **錯誤示範** )
      ``` js
      // This is an example of what *not* to do:
      if (someConditionNotMet()) {
        printUsageToStdout();
        process.exit(1);
      }
      ```
    * 以上的程式碼是一個使用`process.exit()`的 **錯誤示範** ,可能會因此導致要print到stdout的資料被截斷或遺失
    * 這樣做之所以有問題的原因是因為在Node的世界中要寫入到`process.stdout`有時候是非同步的(asynchronous),也可能會在Node的事件迴圈(event loop)中多次發生
    * 所以我們需要在額外寫入到stdout之後,才能呼叫process.exit()來強制將該進程退出
  + 範例程式碼( **正確做法** )
      ``` js
      // How to properly set the exit code while letting
      // the process exit gracefully.
      if (someConditionNotMet()) {
        printUsageToStdout();
        process.exitCode = 1;
      }
      ```
    * 以上的程式碼是正確的解決方法,我們應該要先設定好`process.exitCode`屬性的值,而不是直接呼叫`process.exit()`方法,並避免為事件迴圈(event loop)安排額外的工作來允許該進程能自然地退出
    * 如果是因為遇到錯誤情況而有必要強制終止該進程的話,可以拋出一個`uncaught error`,並根據這個錯誤來終止該進程,也會比直接呼叫`process.exit()`來得更安全
  + 在[Worker threads](https://nodejs.org/dist/latest-v15.x/docs/api/worker_threads.html#worker_threads_worker_threads)中,`process.exit()`會停止當前的線程(current thread),而不是當前的進程(current process)
- [process.kill(pid[ ,signal])](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_kill_pid_signal)
  + args
    * pid: (number)
      * 給定一個進程(process) ID
    * signal: (string || number)
      * 以字串或是數值的形式來發送信號
      * 預設值: `SIGTERM`
  + 該方法會將信號(signal)發送給指定`pid`的進程(process)
  + 信號的名稱為字串型別,常見的像是`SIGINT`, `SIGHUP`
  + 當給定的目標`pid`不存在時,該方法會拋出錯誤。作為特殊情況時,可以利用`0`作為信號來測試某個`pid`的進程是否存在
    * 當在Windows作業系統時,想砍掉一個進程群組(kill a process group)會拋出一個錯誤
  + 其實`process.kill()`這個方法只是一個信號發送器(signal sender),就如同 $`kill` 的系統指令一樣
    * 比起單純的系統指令 $`kill`,發送信號的這個方法可能會額外做一些其他的事,而不單純只是砍掉目標進程(kill the target process)
  + 範例程式碼
      ``` js
      process.on('SIGHUP', () => {
        console.log('Got SIGHUP signal.');
      });

      setTimeout(() => {
        console.log('Exiting.');
        process.exit(0);
      }, 100);

      process.kill(process.pid, 'SIGHUP');
      ```
    * 當Node應用程式的進程收到`SIGUSR1`的信號時,Node會啟動一個偵錯器(debugger)
      * 可參考[Signal events](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_signal_events)
- [process.nextTick(callback[ ,...args])](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_nexttick_callback_args)
  + args
    * callback: (Function)
      * 回呼函式 
    * ...args: (any)
      * 當有呼叫callback function時,要附加給該回呼函式的參數們
    * 該方法會新增一個`callback function`到"next tick queue"。當Javascript堆疊(stack)當前的操作執行完成並允許事件迴圈(event loop)繼續之前,該佇列(queue)會完全耗盡(fully drained)
      * 如果要循環地重複呼叫`process.nextTick()`,可能會建立一個無限循環的迴圈(infinite loop)
      * 相關背景知識可參考[Event Loop](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/#process-nexttick)章節的介紹
  + 範例程式碼
      ``` js
      console.log('start');
      process.nextTick(() => {
        console.log('nextTick callback');
      });
      console.log('scheduled');
      // Output:
      // start
      // scheduled
      // nextTick callback
      ```
  + process.nextTick()方法在開發API時很重要,以便給用戶在物件被構建(constructed) **之後** ,但是在任何I/O相關操作發生 **之前** ,來指派事件處理器(assign event handlers)
      ``` js
      function MyThing(options) {
        this.setupOptions(options);

        process.nextTick(() => {
          this.startDoingStuff();
        });
      }

      const thing = new MyThing();
      thing.getReadyForStuff();

      // thing.startDoingStuff() gets called now, not before.
      ```
  + 對於API來說,要馬 100%同步 or 100%非同步,是很重要的事情
    * 情境說明( **錯誤示範** )
        ``` js
        // WARNING!  DO NOT USE!  BAD UNSAFE HAZARD!
        function maybeSync(arg, cb) {
          if (arg) {
            cb();
            return;
          }

          fs.stat('file', cb);
        }
        ```
    * 以上的範例是冒險的(hazardous),因為假設我們遇到以下的情況
      * 接下來的情況會造成不清楚到底是foo() 或是 bar()先被呼叫
      * 情境說明( **錯誤示範** )
        ``` js
        const maybeTrue = Math.random() > 0.5;

        maybeSync(maybeTrue, () => {
          foo();
        });

        bar();
        ```
    * 範例程式碼( **正確做法** )
      * 以下的做法會比較好 
        ``` js
        function definitelyAsync(arg, cb) {
          if (arg) {
            process.nextTick(cb);
            return;
          }

          fs.stat('file', cb);
        }
        ```
- [process.send(message[, sendHandle[, options]][, callback])](https://nodejs.org/api/process.html#process_process_send_message_sendhandle_options_callback)
  + args
    * message: (Object)
    * sendHandle ([net.Server])(https://nodejs.org/api/net.html#net_class_net_server) || [net.Socket](https://nodejs.org/api/net.html#net_class_net_socket))
    * options: (Object)
      * 被用作將特定操作類型的發送參數化
      * `options`也支援以下的屬性
        * `keepOpen`: (boolean)
          * 當在傳遞`net.Socket`的實例們(instances)時,可以用來傳遞的值
          * 預設值: false
          * 當`keepOpen`=true時, `socket`會在發送過程(sending process)中保持開放
  + Returns: (boolean)
    * 如果是利用`IPC channel`來產生Node應用程式的話,`process.send()`方法可以用來傳遞訊息給父進程(parent process)
      * 消息(message)會被parent's [ChildProcess](https://nodejs.org/api/child_process.html#child_process_class_childprocess)物件作為[message event](https://nodejs.org/api/child_process.html#child_process_event_message)來接收
    * 如果未使用`IPC channel`來產生Node應用程式的話,則`process.send()`回傳的結果將會是`undefined`
    * 該訊息(message)已經歷過序列化(serialization)與解析(parsing)
      * 結果消息(resulting message)可能會跟最初發送的消息不太一樣
- [process.uptime()](https://nodejs.org/api/process.html#process_process_uptime)
  + Returns: (number)
  + 該方法會回傳當前Node應用程式已經運行多久的秒數
    * 回傳值會包括含有小數點的秒數,可以利用Math.floor()來算出小於等於所給數字的最大整數
      * 例: $ `Math.floor(5.95)` => `5`  
> property
- [process.argv](https://nodejs.org/api/process.html#process_process_argv)
  + Type: (string) 
  + 該屬性會回傳一個陣列(array),該陣列會包含當啟動Node應用程式的進程的時候,整個命令列(command-line)要傳遞的所有參數
    * 其中的第一個元素會是[process.execPath](https://nodejs.org/api/process.html#process_process_execpath)
    * 第二個元素會是正在被執行的Javascript檔案的路徑位置
    * 剩下的元素就是任何其他的命令列參數(command-line arguments)
  + 範例程式碼
    * 假設以下範例程式的檔案名稱是`process-args.js`
      ``` js
      // print process.argv
      process.argv.forEach((val, index) => {
        console.log(`${index}: ${val}`);
      });
      ```
    * 可以利用以下的方式來啟動Node應用程式的進程(process)
      * $ `node process-args.js one two=three four`
    * 將會回傳以下的值
        ``` js
        0: /usr/local/bin/node
        1: /Users/mjr/work/node/process-args.js
        2: one
        3: two=three
        4: four
        ```
- [process.debugPort](https://nodejs.org/api/process.html#process_process_debugport)
  + Type: (number)
  + 該屬性值代表當啟用(enabled)Node應用程式的除錯器時(dubugger),所使用的埠號(port)
- [process.env](https://nodejs.org/api/process.html#process_process_env)
  + Type: (Object) 
  + 該屬性會回傳一個物件,包含使用者的環境變數
    * 像是以下的`Object`
      ``` js
      {
        TERM: 'xterm-256color',
        SHELL: '/usr/local/bin/bash',
        USER: 'maciej',
        PATH: '~/.bin/:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin',
        PWD: '/Users/maciej',
        EDITOR: 'vim',
        SHLVL: '1',
        HOME: '/Users/maciej',
        LOGNAME: 'maciej',
        _: '/usr/local/bin/node'
      } 
      ```
    * 可以修改這個環境變數的物件,但僅限於該Node應用程式進程(process),或者除非明確地要求不會反應到其他[Worker threads](https://nodejs.org/api/worker_threads.html#worker_threads_class_worker)上
      * 換句話說,以下的範例並不會如預期地運作
      * $ `node -e 'process.env.foo = "bar"' && echo $foo`
      * 雖然將執行以下操作
        ``` js
        process.env.foo = 'bar';
        console.log(process.env.foo);
        ```
  + 從Node v10.0.0的版本以後開始,指派給`process.env`的環境變數的值 **不會** 再做隱性轉換型別
    * 所以以下的方式 **不建議** 再使用
    * 情境說明( **錯誤示範** )
    ``` js
    process.env.test = null;
    console.log(process.env.test);
    // => 'null'
    process.env.test = undefined;
    console.log(process.env.test);
    // => 'undefined'
    ```
  + 如果要從`process.env`的屬性值中,刪除Node應用程式的環境變數的話,可以利用Javascript的`delete`指令來完成
    * 範例程式碼
        ``` js
        process.env.TEST = 1;
        delete process.env.TEST;
        console.log(process.env.TEST);
        // => undefined
        ```
  + 在Windows作業系統中,環境變數沒有區分大小寫(case-insensitive)
    * 以下範例程式碼 **僅限於Windows作業系統中** 可以這樣使用
      ``` js
      process.env.TEST = 1;
      console.log(process.env.test);
      // => 1
      ```
  + 除非在建立[Worker](https://nodejs.org/api/worker_threads.html#worker_threads_class_worker)實例時有明確指定環境變數,不然每個`Worker thread`都有自己的一份`process.env`副本(copy),是基於它們各自的父線程(parent thread)的環境變數(`process.env`),或是任何被作為`env`選項並給定到`Worker constructor`
    * `Worker threads`之間並無法看到`process.env`的修改,只有主線程(main thread)才能進行對作業系統或是本機加載項(native add-ons)做看得見的修改
- [process.execArgv](https://nodejs.org/api/process.html#process_process_execargv)
  + Type: (string [set])
  + 該屬性會回傳當Node應用程式的進程被啟動時,在Node.js命令列上面的特定選項的集合(the set of Node.js-specific command-line options)
    * 這邊所指的特定選項(options)並不會出現在`process.argv`的屬性值之中,而且也不包括可執行的(executable),該準備要執行的檔案名稱,以及該準備要執行的檔案名稱 **後面** 的任何選項(options)
    * 這些選項對於以與父級相同執行環境的子進程(child process)是很有用的
    * 在終端機(CLI)執行該以下的指令後
      * 例: $ `node --harmony script.js --version`
      * 檢視`process.execArgv`的屬性值會得到
        * => `['--harmony']`
      * 對照檢視`process.argv`的屬性值會得到
        * => `['/usr/local/bin/node', 'script.js', '--version']`
    * 可參考有關[Worker constructor](https://nodejs.org/api/worker_threads.html#worker_threads_new_worker_filename_options)中的`execArgv`屬性以了解更多的訊息
- [process.execPath](https://nodejs.org/api/process.html#process_process_execpath)
  + Type: (string)
  + 該屬性值會回傳Node應用程式的進程中的可執行文件(executable)的絕對路徑
    * 例: `'/usr/local/bin/node'`  
    * 如果遇到可執行文件的路徑是`symbolic link`,會自動解決掉該問題 
- [process.exitCode](https://nodejs.org/api/process.html#process_process_exitcode)
  + 型別: (integer)
  + 當進程被正常地退出時,或是透過`process.exit()`方法退出時但沒有給定退出碼時,`process.exitCode`的屬性值代表該進程的退出碼
  + 當我們給定process.exit([code])方法的退出碼參數值時,會推翻(override)之前的`process.exitCode`的屬性值設定
- [process.pid](https://nodejs.org/api/process.html#process_process_pid)
  + Type: (integer)
  + 該屬性會回傳該進程的ID(process ID, pid)
    * $ `console.log(`This process is pid ${process.pid}`);` 
- [A note on process I/O](https://nodejs.org/api/process.html#process_a_note_on_process_i_o)
  + [process.stdin](https://nodejs.org/api/process.html#process_process_stdin)
  + [process.stdout](https://nodejs.org/api/process.html#process_process_stdout)
  + [process.stderr](https://nodejs.org/api/process.html#process_process_stderr)
- [process.title](https://nodejs.org/api/process.html#process_process_title)
  + Type: (string)
  + 該屬性會回傳當前的進程標題(current process title) => 也就是會回傳當前`ps`的值
  + 當指派新的值給`process.title`屬性時,會修改掉當前`ps`的值
- [process.version](https://nodejs.org/api/process.html#process_process_version)
  + Type: (string)
  + 該屬性值代表Node.js的版本號(version)
      ``` js
      console.log(`Version: ${process.version}`);
      // Version: v14.8.0
      ```
    * 如果想要取得不帶有前綴v開頭的版本號字串的話,可以利用以下的屬性值
      * $ `process.versions.node`       

> Exit codes(退出碼)
- 在沒有其他額外的 **非同步操作** 處於`pending狀態`後,Node應用程式通常會以`0`的退出碼來正常退出
- 以下是常見的退出碼
  + `0`: 正常退出 (預設值)
  + `1`: 未捕獲的致命錯誤(Uncaught Fatal Exception),並且沒有受到`domain`或是[uncaughtException](https://nodejs.org/api/process.html#process_event_uncaughtexception)事件處理器來處理
  + 以下略...

### [Console](https://nodejs.org/api/console.html#console_console)
  + `console`模組提供了一組簡單的除錯控制台(debugging console),類似於網頁瀏覽器所提供的Javascript控制台機制
  + `console`模組會匯出(export)兩個特定的元件(specific components)
    * 第一個特定的元件是: [Console類別(class)](https://nodejs.org/api/console.html#console_class_console)的方法,像是`console.log()`&`console.error()`&`console.warn()`,它們可以被用來寫入到Node流(stream)中
    * 第二個特定的原件是: 一個全域(global)的`console`實例(instance)會寫入到[process.stdout](https://nodejs.org/api/process.html#process_process_stdout)與[process.stderr](https://nodejs.org/api/process.html#process_process_stderr)。全域的`console`物件可以直接被使用,而 **不用** 事先匯入( **require('console')** )
  + **注意** : 全域的`console`物件方法既不能像類似它們的瀏覽器API一樣始終同步(consistently synchronous),也不能像Node流(streams)一樣始終非同步(consistently asynchronous)
    * 可參考`process`模組的 [A note on process I/O](https://nodejs.org/api/process.html#process_a_note_on_process_i_o) 章節
  + 使用全域的`console物件`的範例程式碼
      ``` js
      console.log('hello world');
      // Prints: hello world, to stdout
      console.log('hello %s', 'world');
      // Prints: hello world, to stdout
      console.error(new Error('Whoops, something bad happened'));
      // Prints error message and stack trace to stderr:
      //   Error: Whoops, something bad happened
      //     at [eval]:5:15
      //     at Script.runInThisContext (node:vm:132:18)
      //     at Object.runInThisContext (node:vm:309:38)
      //     at node:internal/process/execution:77:19
      //     at [eval]-wrapper:6:22
      //     at evalScript (node:internal/process/execution:76:60)
      //     at node:internal/main/eval_string:23:3

      const name = 'Will Robinson';
      console.warn(`Danger ${name}! Danger!`);
      // Prints: Danger Will Robinson! Danger!, to stderr
      ```
  + 使用`Console類別`的範例程式碼
      ``` js
      const out = getStreamSomehow();
      const err = getStreamSomehow();
      const myConsole = new console.Console(out, err);

      myConsole.log('hello world');
      // Prints: hello world, to out
      myConsole.log('hello %s', 'world');
      // Prints: hello world, to out
      myConsole.error(new Error('Whoops, something bad happened'));
      // Prints: [Error: Whoops, something bad happened], to err

      const name = 'Will Robinson';
      myConsole.warn(`Danger ${name}! Danger!`);
      // Prints: Danger Will Robinson! Danger!, to err
      ```
> Class
- [Class: Console](https://nodejs.org/api/console.html#console_class_console)
  + `Console`類別可以用來建立簡單且可配置(configurable)的輸出流(output streams)紀錄器(logger),並且可以利用以下兩種方式來存取,或是用它們相仿的解構對應物件(destructured counterparts)來存取
    * 存取方式一: `require('console').Console`
      * 例: `const { Console } = require('console');`
    * 存取方式二: `console.Console`
      * 例: `const { Console } = console;`
  > method
    * [console.clear()](https://nodejs.org/api/console.html#console_console_clear)
      * 當標準輸出(`stdout`)是`TTY`時,呼叫`console.clear()`的時候,會嘗試清除`TTY`; 當標準輸出(`stdout`)不是`TTY`時,則該方法不會做任何事情
      * 使用特定的`console.clear()`操作時,會因為使用的作業系統不同or終端機類型不同,而有所差異。在大部分的Linux作業系統中,`console.clear()`方法會執行與`clear`指令差不多的操作; 在Windows作業系統中,`console.clear()`將僅會清除掉在當前(current)的終端機視口(terminal viewport)中的Node應用程式的二進制(binary)文件的輸出(output)
    * [console.count([label])](https://nodejs.org/api/console.html#console_console_count_label)
      * args
        * label: (string)
          * 可以給定要顯示的計數器的標籤值
          * 預設值: `default`
      * 該方法會回傳一個內部計數器(internal counter)維持顯示一組`標籤`(label) & `次數`(times)
        * `標籤`(label): 可以透過呼叫`console.count()`方法時,由開發者給定標籤值
        * `次數`(times): 指定給標籤,呼叫`console.count()`方法並輸出給`stdout`的次數
        * 範例程式碼
        * ```console
            > console.count()
            default: 1
            undefined
            > console.count('default')
            default: 2
            undefined
            > console.count('abc')
            abc: 1
            undefined
            > console.count('xyz')
            xyz: 1
            undefined
            > console.count('abc')
            abc: 2
            undefined
            > console.count()
            default: 3
            undefined
            >
          ```
    * [console.error([data][, ...args])](https://nodejs.org/api/console.html#console_console_error_data_args)
      * args
        * data: (any)
        * ...args: (any)
      * 該方法會用新的一行(newline)打印(print)出標準錯誤(`stderr`)
      * 該方法可以傳遞多個參數
        * 第一個參數會被用來當作主要訊息(primary message)
        * 其他額外的參數(additional arguments)會被用作替換值(substitution),類似於[printf(3)](https://man7.org/linux/man-pages/man3/printf.3.html)
          * 其他額外的參數都會被傳遞給`util.format()`方法
          * 相關資訊可參考[util.format()](https://nodejs.org/api/util.html#util_util_format_format_args)方法
      * 範例程式碼
        ``` js
        const code = 5;
        console.error('error #%d', code);
        // Prints: error #5, to stderr
        console.error('error', code);
        // Prints: error 5, to stderr
        ```
        * 若格式化元素(formatted elements),例如: `%d`沒有在新的一行中被發現,則[util.inspect()](https://nodejs.org/api/util.html#util_util_inspect_object_options)方法會被呼叫,並在每個參數上呼叫該元素(`%d`),並將結果的字串值連接起來
    * [console.log([data][, ...args])](https://nodejs.org/api/console.html#console_console_log_data_args)
      * args
        * data: (any)
        * ...args: (any)
      * 該方法會用新的一行(newline)打印(print)出標準輸出(`stdout`)
      * 該方法可以傳遞多個參數
        * 第一個參數會被用來當作主要訊息(primary message)
        * 其他額外的參數(additional arguments)會被用作替換值(substitution),類似於[printf(3)](https://man7.org/linux/man-pages/man3/printf.3.html)
          * 其他額外的參數都會被傳遞給`util.format()`方法
          * 相關資訊可參考[util.format()](https://nodejs.org/api/util.html#util_util_format_format_args)方法
      * 範例程式碼
        ``` js
        const count = 5;
        console.log('count: %d', count);
        // Prints: count: 5, to stdout
        console.log('count:', count);
        // Prints: count: 5, to stdout
        ``` 
    * [console.time([label])](https://nodejs.org/api/console.html#console_console_time_label)
      * args
        * label: (string)
        * 可以給定該計時器的標籤值,以作為計時器識別用
        * 預設值: `default`
      * 該方法會啟動一個計時器(timer)被用來計算該操作(operation)的持續時間
      * 計時器會用獨一無二(unique)的標籤(label)來識別
      * 計時器會呼叫同標籤(same label)的`console.timeEnd()`方法來停止計時器,並將經過的時間以合適的時間單位輸出到標準輸出(`stdout`)中
        * 舉例來說,如果經過的時間是3869毫秒(ms),那麼`console.timeEnd()`方法會顯示"3.869"秒
    * [console.timeEnd([label])](https://nodejs.org/api/console.html#console_console_timeend_label)
      * args
        * label: (string)
        * 可以給定該計時器的標籤值,以作為計時器識別用
        * 預設值: `default`
      * 該方法會停止之前透過呼叫`console.time()`方法設定好的計時器,並將結果打印(print)到標準輸出(`stdout`)中
      * 範例程式碼
        ``` js
        console.time('100-elements');
        for (let i = 0; i < 100; i++) {}
        console.timeEnd('100-elements');
        // prints 100-elements: 225.438ms
        ```
    * [console.trace([message][, ...args])](https://nodejs.org/api/console.html#console_console_trace_message_args)
      * args
        * message: (any)
        * ...args: (any)
      * 該方法會將`Trace:`這段字串打印(print)到標準錯誤(`stderr`)中,接著透過[util.format()](https://nodejs.org/api/util.html#util_util_format_format_args)方法打印出格式化過的字串(formatted message),並堆棧追蹤(stack trace)到程式碼中的當前位置
      * 範例程式碼
        ``` js
        console.trace('Show me');
        // Prints: (stack trace will vary based on where trace is called)
        //  Trace: Show me
        //    at repl:2:9
        //    at REPLServer.defaultEval (repl.js:248:27)
        //    at bound (domain.js:287:14)
        //    at REPLServer.runBound [as eval] (domain.js:300:12)
        //    at REPLServer.<anonymous> (repl.js:412:12)
        //    at emitOne (events.js:82:20)
        //    at REPLServer.emit (events.js:169:7)
        //    at REPLServer.Interface._onLine (readline.js:210:10)
        //    at REPLServer.Interface._line (readline.js:549:8)
        //    at REPLServer.Interface._ttyWrite (readline.js:826:14)
        ```

### [Readline](https://nodejs.org/api/readline.html#readline_readline)
- `Readline`模組提供一個介面能從[Readable stream](https://nodejs.org/api/stream.html#stream_readable_streams)一次一行地讀取數據,它可以用以下的方式來存取
    ``` js
    const readline = require('readline');
    ```
  + 以下是`readline`模組的簡易範例的圖解說明
    ``` js
    const readline = require('readline');

    const rl = readline.createInterface({
      input: process.stdin,
      output: process.stdout
    });

    rl.question('What do you think of Node.js? ', (answer) => {
      // TODO: Log the answer in a database
      console.log(`Thank you for your valuable feedback: ${answer}`);

      rl.close();
    });
    ```
    * 一旦以上的程式碼被呼叫後,Node應用程式就不會終止,直到[readline.Interface](https://nodejs.org/api/readline.html#readline_class_interface)這個介面被關閉為止,因為這個介面會等待輸入流(input stream)接收到數據為止
> Class
  + [Class: Interface](https://nodejs.org/api/readline.html#readline_class_interface)
    + 繼承(Extends): ([EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter))
  + `reader.Interface`的實例(instances)是透過[readline.createInterface()](https://nodejs.org/api/readline.html#readline_readline_createinterface_options)方法建造的
    * 每一個實例都會與單一的輸入可讀取流(input Readable stream) & 單一的輸出可寫入流(output Writable stream)
    * 輸出流會被用來從輸入流(input stream)中讀取使用者的輸入(input)並打印出提示(prompts)
    > method
      + [readline.question(query, callback)](https://nodejs.org/api/readline.html#readline_rl_question_query_callback)
        * args
          * query (string)
            * 要寫入到輸出(`output`)的陳述句(statement)或是查詢語句(`query`),位於提示之前(prepended to the prompt)
          * callback (Function)
            * 透過使用者對查詢語句(`query`)的回應(response)來呼叫的回呼函式
        * 該方法會透過寫入輸出(`output`)來展示查詢語句(`query`),並等待使用者輸入後並將該輸入提供給輸入(`input`)。接著會將提供的輸入(provided input)作為第一個參數,傳遞給回呼函式
        * 當輸入流被暫停(paused)時,呼叫`readline.question()`方法時,會繼續(resume)輸入流
        * 若`readline.createInterface()`被建立時,該輸出(`output`)被設定為`null`或是`undefined`,該查詢語句(`query`)不會被寫入(written)
        * 範例程式碼
        * ``` js
            rl.question('What is your favorite food? ', (answer) => {
              console.log(`Oh, so your favorite food is ${answer}`);
            });
            ```
          * 傳遞給`readline.question()`的回呼函式不會遵從一般接受`Error`物件或是`null`作為第一個參數的模式。回呼函式會以被提供的答案(provided answer)作為唯一的參數
      * [readline.close()](https://nodejs.org/api/readline.html#readline_rl_close)
        * 該方法會關閉`readline.createInterface()`實例,並放棄對輸入流(`input` streams)與輸出流(`output` streams)的控制(control over)。當被呼叫時,[close事件](https://nodejs.org/api/readline.html#readline_event_close)就會被發出(emitted)
        * 呼叫`readline.close()`方法不會立即停止其它被`readline.Interface`實例所發出的其它事件(包含[line事件](https://nodejs.org/api/readline.html#readline_event_line))

### [CommonJS modules](https://nodejs.org/api/modules.html#modules_modules_commonjs_modules)
- 在Node的模組系統(module system)中,每個檔案都被視為一個分開的模組(separate module)
  + 以下的情境說明,我們假設有一個檔案,其檔案名稱為`foo.js`
    ``` js
    const circle = require('./circle.js');
    console.log(`The area of a circle of radius 4 is ${circle.area(4)}`);
    ```
    * 在這個檔案的第一行中,`foo.js`檔案會載入(loads) `circle.js`模組,這兩個檔案都是在同一個檔案目錄(directory)中
  + 以下是`circle.js`的檔案內容
    ``` js
    const { PI } = Math;

    exports.area = (r) => PI * r ** 2;

    exports.circumference = (r) => 2 * PI * r;
    ```
    * `circle.js`模組已匯出了`area()`與`circumference()`這兩個函式,透過在特殊的`exports`物件上指定(specifying)額外的(additional)屬性可以將函式與物件新增到模組的最上層(root of a module)
    * 這時該模組(= `circle.js`)的區域變數會是私有的(private),因為Node會將模組(module)包裝(wrapped)成為一個函式
      * 詳情可參考[module wrapper](https://nodejs.org/api/modules.html#modules_the_module_wrapper)
    * 在上述的`PI`範例中,`PI`變數就是`circle.js`的私有(private)變數
- `module.exports`屬性可以被指派(assigned)一個新的值,像是函式or物件
  + 以下的範例程式碼,`bar.js`檔案會使用到`square`模組,該模組會匯出(exports)一個`Square`類別(class)
    ``` js
    const Square = require('./square.js');
    const mySquare = new Square(2);
    console.log(`The area of mySquare is ${mySquare.area()}`);
    ```
    * `square`模組是在`square.js`這個檔案中定義的
      ``` js
      // Assigning to exports will not modify module, must use module.exports
      module.exports = class Square {
        constructor(width) {
          this.width = width;
        }

        area() {
          return this.width ** 2;
        }
      };
      ```
- 模組系統(module system)會在`require('module')`這個模組中實作(implemented)
> [Core modules](https://nodejs.org/api/modules.html#modules_core_modules)
  + Node有許多被編譯(compiled)成二進制文件(binary)的模組,這些Node內建的核心模組在官方文件的其它地方都有更詳細的介紹
  + `require()`方法會優先讀取Node內建的核心模組,當它們的標識符(identifier)被作為參數傳入給`require()`方法時
      ``` js
      require('http')
      ```
    * 以上的引用方式會優先引用Node內建的核心模組-`HTTP`模組 (即使有同名稱的檔案在目錄中)
> [Folders as modules](https://nodejs.org/api/modules.html#modules_folders_as_modules)
  + 將程式(programs)與套件(libraries)整理到一個自給自足的(self-contained)目錄中,然後為這些目錄提供單一個入口(single entry point)是很方便的
  + 以下有3種方式可以將資料夾(folder)作為參數傳遞給`require()`方法
    * 方法一: 在該資料夾的最上層(root)建立一個[package.json](https://nodejs.org/api/packages.html#packages_node_js_package_json_field_definitions)檔案,並指定一個`main`屬性值來表示當載入此套件(package)時,預設要引入哪個模組
        ``` js
        { "name" : "some-library",
          "main" : "./lib/some-library.js" 
        }
        ```
      * 上述的範例程式碼中,如果在`./some-library/`目錄中有`./some-library/lib/some-library.js`這個檔案的話,Node就會嘗試讀取它
      * 如果該目錄中沒有該文件,或是[main](https://nodejs.org/api/packages.html#packages_main)目錄已不見了(missing)或是無法找到(cannot be resolved),這時Node就會嘗試去讀取[name](https://nodejs.org/api/packages.html#packages_name)檔案中的`index.js`或是`index.node`
      * 情境說明( **當該目錄沒有package.json這個檔案,且又找不到.xxx/lib/xxx.js檔案時** ),當我們引用`require('./some-library')`的時候,會嘗試去讀取以下2種路徑下的檔案
      * 第1種: `./some-library/index.js`
      * 第2種: `./some-library/index.node`
        * 如果以上的嘗試都失敗的話,Node就會將整個模組(entire module)回報(report)為遺失(missing),並顯示一個預設的錯誤
        * 預設: `Error: Cannot find module 'some-library'`
      * 以上就是對Node的`package.json`檔案有所體認的程度(the extent of the awareness)
    + 方法二: 如果傳遞給`require()`方法的模組標識符(module identifier)不是一個Node內建的核心模組名稱,而且不是以`/`, `../`, `./` 的路徑開頭的話,Node就會從當前模組的父目錄(也就是前一層目錄, parent directory)開始,接著新增一個`node_modules/`資料夾,並嘗試從該位置讀取模組
      * 如果在該目錄下沒有找到該模組名稱的話,則Node將會往前一層目錄查看,直到到達檔案系統(file system)的根目錄(root)為止
      * 情境說明(若在`/home/ry/projects/foo.js`的檔案為`require('bar.js')`,則Node會根據以下的順序依序查看接下來的檔案路徑)
        * `/home/ry/projects/node_modules/bar.js`
        * `/home/ry/node_modules/bar.js`
        * `/home/node_modules/bar.js`
        * `/node_modules/bar.js`
      * 這樣做也會讓程式本地化(localize)它們自己的相依性(dependencies),而不會互相抵觸(clash)
      * 我們也可以透過明確的檔案路徑or與該模組一起分發(distributed)的子模組(sub modules)路徑,以後綴(suffix)的方式加在該模組名稱的後面,來引用到我們的程式碼中
        * 例: `require('example-module/path/to/file')`
        * 註: 後綴路徑會遵從與該模組相同的模組解析語意(module resolution semantics)
    + 方法三: 如果`NODE_PATH`這個環境變數設定為以冒號區隔(colon-delimited)的絕對路徑的話,則Node將會搜尋該模組的這些絕對路徑(如果在其他位置沒有找到的話)
      * 在Windows作業系統中,NODE_PATH會用分號(semicolon)做區隔
      * `NODE_PATH`起初是為了在當前的模組解析([module resolution](https://nodejs.org/api/modules.html#modules_all_together))演算法被定義之前,能夠支援從不同路徑來載入模組而建立的
        * 至今,`NODE_PATH`仍然支援,但是Node生態系統已經決定了定位出模組相依性的規範,而顯得`NODE_PATH`不再是那麼必要了
        * 有時候,當其他人不知道這次的部署(deployments)需要依賴(rely on)`NODE_PATH`環境變數時,會發生出乎意料的行為
        * 有時候,一個模組的相依性改變了,會導致`NODE_PATH`環境變數搜尋時會載入到不同的該模組的不同版本(甚至是不同的模組)
      * 此外,Node會根據以下的順序來搜尋`GLOBAL_FOLDERS`清單
        * `$HOME/.node_modules`
        * `$HOME/.node_libraries`
        * `$PREFIX/lib/node`
        * 註: `$HOME`表示使用者的家目錄; `$PREFIX`表示Node已安裝`node_prefix`
      * 這些做法主要是基於具有歷史意義的原因
      * **建議** : 強烈鼓勵將相依的檔案放在本地端的`node_modules`資料夾,這會使載入模組的速度更快 & 更可靠(reliably)
> [The module scope](https://nodejs.org/api/modules.html#modules_the_module_scope)
  + [__dirname](https://nodejs.org/api/modules.html#modules_dirname)
    * Returns: (string)
    * 該屬性會回傳當前模組的資料夾名稱(也就是所在路徑, directory name)
    * 這個屬性跟[path.dirname(path)](https://nodejs.org/api/path.html#path_path_dirname_path)與[__filename](https://nodejs.org/api/modules.html#modules_filename)是一樣的
    * 範例程式碼(從`/Users/mjr`的家目錄中,執行`node example.js`)
      ``` js 
      console.log(__dirname);
      // Prints: /Users/mjr
      console.log(path.dirname(__filename));
      // Prints: /Users/mjr
      ```
  + [__filename](https://nodejs.org/api/modules.html#modules_filename)
    * Returns: (string)
    * 該屬性會回傳當前模組的檔案名稱(file name),也就是當前模組的絕對路徑(如果存在連結(symlink)的話,已解析過的)
    * 對於主程式,`__filename`不一定要與`CLI`中的檔案名稱相同
    * 範例程式碼(從`/Users/mjr`的家目錄中,執行`node example.js`)
        ``` js
        console.log(__filename);
        // Prints: /Users/mjr/example.js
        console.log(__dirname);
        // Prints: /Users/mjr
        ```
    * 假如有兩個模組--- `a` & `b`,然後`b`模組是`a`模組的相依項目(dependency),並且資料夾結構如下
      * `/Users/mjr/app/a.js`
      * `/Users/mjr/app/node_modules/b/b.js`
      * 關於以上的範例,`b.js`檔案的`__filename`屬性值會是`/Users/mjr/app/node_modules/b/b.js`; 另外,`a.js`檔案的`__filename`屬性值會是`/Users/mjr/app/a.js`
> [The module object](https://nodejs.org/api/modules.html#modules_the_module_object)
  + 在每個模組中,`module`物件中閒置的(free)變數代表對於當前模組物件的參考。為了方便起見,`module.exports`也可以透過[exports](https://nodejs.org/dist/latest-v15.x/docs/api/modules.html#modules_exports)這個全域物件來存取。[module](https://nodejs.org/dist/latest-v15.x/docs/api/modules.html#modules_module)物件對於每一個模組來說,實際上是一個區域物件,而不是全域物件
    > object
      * [module.exports](https://nodejs.org/api/modules.html#modules_module_exports)
        * 該物件是由[Module system](https://nodejs.org/api/module.html#module_modules_module_api)所建立的,有些時候這樣是不能被接受(not acceptable)的,許多人希望他們的模組是某個類別(class)的實例(instance)
        * 為此,可以將所需匯出的物件指派給`module.exports`物件
        * 將所需的物件指派給`exports`會簡單地重新綁定(simply rebind)`exports`區域變數,然而這可能不是我們所想要的結果
        * 範例程式碼(假設我們正在製作一個模組,叫做`a.js`)
            ``` js
            const EventEmitter = require('events');

            module.exports = new EventEmitter();

            // Do some work, and after some time emit
            // the 'ready' event from the module itself.
            setTimeout(() => {
              module.exports.emit('ready');
            }, 1000);
            ```
        * 這時我們就可以在另外一個檔案中引入上述的模組
            ``` js
            const a = require('./a');
            a.on('ready', () => {
              console.log('module "a" is ready');
            });
            ```
        * 情境說明( **錯誤示範** )---不會被執行
          * 因為指派給`module.exports`物件需要立即完成它,並且不能在任何回呼函式(callbacks)中完成
          * `x.js`
            ``` js
            setTimeout(() => {
              module.exports = { a: 'hello' };
            }, 0);
            ```
          * `y.js`
            ``` js
            const x = require('./x');
            console.log(x.a);
            ```
      * [exports shortcut](https://nodejs.org/api/modules.html#modules_module_exports)
        * 該變數只有在模組檔案層級(module's file-level)的範圍(scope)才可用,並且在模組被評估之前(evaluated)就會被`module.exports`物件指派其值
        * **該變數允許一個捷徑,因此`module.exports.f = ...`可以被更簡潔地寫成`exports.f = ...`**
        * 然而,請注意!就如同任何的變數一樣,如果有新的值被指派給`exports`變數,則它就不再被重新綁定到`module.exports`物件上
        * 範例程式碼
            ``` js
            module.exports.hello = true; // Exported from require of module
            exports = { hello: false };  // Not exported, only available in the module
           ```
        * 當`module.exports`屬性完全被一個物件取代(replaced)時,通常也會重新指派給`exports`變數
        * 範例程式碼
          ``` js
          module.exports = exports = function Constructor() {
            // ... etc.
          };
          ```
        * 為了說明以上的這種行為,請想像有一個假設的(hypothetical)`require()`方法實作(implementation),該行為與實際的`require()`方法相當類似
        * 範例程式碼
            ``` js
            function require(/* ... */) {
              const module = { exports: {} };
              ((module, exports) => {
                // Module code here. In this example, define a function.
                function someFunc() {}
                exports = someFunc;
                // At this point, exports is no longer a shortcut to module.exports, and
                // this module will still export an empty default object.
                module.exports = someFunc;
                // At this point, the module will now export someFunc, instead of the
                // default object.
              })(module, module.exports);
              return module.exports;
            }
            ```

### [Timers](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_timers)
- `timer`模組會公開(exposes)一個全域的API,用來安排在將來的某個時段呼叫該函式所使用的。因為`timer`模組中的方法都是全域的,所以不用事先引用(`require('timers')`)模組後才能使用這些API
- Node的`timer`模組中的方法會實作(implement)類似於網頁瀏覽器(web browsers)中`timers`API,但會使用不同的實作方法
  + 而Node的`timer`模組會圍繞在[事件迴圈(event loop)](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/#setimmediate-vs-settimeout)的基礎之上來實作不同的方法
> method
  + [setImmediate(callback[, ...args])](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_setimmediate_callback_args)
    * args
      * callback: (Function)
        * 在本次的事件迴圈結束時,要呼叫的函式
      * ...args: (any)
        * 當回呼函式(callback)被呼叫時,可以選擇性地(optional)傳遞參數(arguments)
      * Returns: 可以傳遞給[clearImmediate()](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_clearimmediate_immediate)方法所使用的`Timeout物件`
    * 安排在`callback I/O事件`的立即執行(immediate execution)回呼函式
    * 當有多個`setImmediate()`方法被使用時,這些回呼函式會依照被建立的順序排隊等待被執行(queued for execution)。整個回呼隊列(callback queue)會處理每次的事件迴圈迭代(event loop iteration)
      * 如果有一個立即執行計時器(immediate timer)正在一個回呼執行(execution callback)中的隊列(queue)中,該立即執行計時器(immediate timer)就不會被觸發,直到下次事件迴圈迭代(the next event loop iteration)以前
    * 如果`setImmediate()`方法的`callback`參數不是一個函式(function)的話,會拋出一個[TypeError](https://nodejs.org/dist/latest-v15.x/docs/api/errors.html#errors_class_typeerror)錯誤
    * `setImmediate()`方法有一個客製化的`promises`變形(custom variant for promises),可以利用Node的內建核心模組`Util`中的[util.promisify()](https://nodejs.org/dist/latest-v15.x/docs/api/util.html#util_util_promisify_original)方法
        ``` js
        const util = require('util');
        const setImmediatePromise = util.promisify(setImmediate);

        setImmediatePromise('foobar').then((value) => {
          // value === 'foobar' (passing values is optional)
          // This is executed after all I/O callbacks.
        });

        // Or with async function
        async function timerExample() {
          console.log('Before I/O callbacks');
          await setImmediatePromise();
          console.log('After I/O callbacks');
        }
        timerExample();
        ```
  + [setInterval(callback[, delay[, ...args]])](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_setinterval_callback_delay_args)
    * args
      * callback: (Function)
        * 設定當計時器(timer)經過設定好的間隔時間(elapses)後,要 **重複執行** 的回呼函式(callback function)
      * delay: (number)
        * 呼叫回呼函式(callback)之前要間隔的毫秒數
        * 預設值: `1`
        * 當該參數的值大於`2147483647`或是小於`1`時,都會被設定為`1`; 若該參數為非整數的值,會被無條件捨去後,變成整數
      * `...args`: (any)
        * 當回呼函式(callback)被呼叫時,可以選擇性地(optional)傳遞參數(arguments)
      * Returns: 可以傳遞給[clearInterval()](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_clearinterval_timeout)方法所使用的`Timeout物件`
    * 安排一個會每`n`毫秒後,重複(repeated)執行的回呼函式(callback function)
    * 如果`setInterval()`方法的`callback`參數不是一個函式(function)的話,會拋出一個[TypeError](https://nodejs.org/dist/latest-v15.x/docs/api/errors.html#errors_class_typeerror)錯誤
  + [setTimeout(callback[, delay[, ...args]])](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_setinterval_callback_delay_args)
    * args
      * callback: (function)
        * 設定當計時器(timer)經過設定好的間隔時間(elapses)後,要 **執行一次** 的回呼函式(callback function)
      * delay: (number)
        * 呼叫回呼函式(callback)之前要經過的毫秒數
        * 預設值: `1`
        * 當該參數的值大於`2147483647`或是小於`1`時,都會被設定為`1`; 若該參數為非整數的值,會被無條件捨去後,變成整數
      * ...args: (any)
        * 當回呼函式(callback)被呼叫時,可以選擇性地(optional)傳遞參數(arguments)
      * Returns: 可以傳遞給[clearTimeout()](https://nodejs.org/dist/latest-v15.x/docs/api/timers.html#timers_cleartimeout_timeout)方法所使用的`Timeout物件`
    * 安排一個當經過`n`毫秒後,會執行的回呼函式(callback function)
    * 注意: `setTimeout()`方法要呼叫的回呼函式可能不會在設定好的毫秒數後,被精確地(precisely)呼叫。Node **不能保證** 回呼函式被呼叫的精確時間(exact timing)以及順序(ordering)。只能盡量讓要呼叫的回呼函式(callback)在設定好的時間後被觸發
    * 如果`setTimeout()`方法的`callback`參數不是一個函式(function)的話,會拋出一個[TypeError](https://nodejs.org/dist/latest-v15.x/docs/api/errors.html#errors_class_typeerror)錯誤
    * `setTimeout()`方法有一個客製化的`promises`變形(custom variant for promises),可以利用Node的內建核心模組`Util`中的[util.promisify()](https://nodejs.org/dist/latest-v15.x/docs/api/util.html#util_util_promisify_original)方法
        ``` js
        const util = require('util');
        const setTimeoutPromise = util.promisify(setTimeout);

        setTimeoutPromise(40, 'foobar').then((value) => {
          // value === 'foobar' (passing values is optional)
          // This is executed after about 40 milliseconds.
        });
        ```

### [Events](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_events)
- 大部分Node中的核心API都是基於慣用的(idiomatic)非同步(asynchronous)事件驅動(event-driven)架構(architecture)。在這種架構中,某些類型的物件(被稱為`emitters`)會發出(emit)被命名的事件(named events),導致(cause)函式物件(`Function` object, => 也就是監聽器(listeners))被呼叫(to be called)
- 舉例來說,每當有同級連接(peer connects)時,都會發出(emit)一次事件(event)到一個[net.Server](https://nodejs.org/dist/latest-v15.x/docs/api/net.html#net_class_net_server)物件(object); 當檔案被開啟(opened)時,會有一個[fs.ReadStream](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_class_fs_readstream)實例(instance)會發出(emit)一個事件(event); 每當(whenever)有資料可以被讀取時,就會發出(emit)一個[stream](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html)事件(event)
- 發出的事件(emit events)中的所有物件(objects)都是[EventEmitter](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_class_eventemitter)類別(class)的實例(instances)。這些物件會公開(expose)一個`emitter.on()`函式(function),這個函式允許(allows)一個或多個函式附加到(attached)由該物件(by object)所發出(emitted)的被命名的事件(named events)上。 **通常,事件名稱都是用駝峰命名法(camel-cased)來命名的字串(strings),但是可以使用任何有效的(valid)Javascript屬性鍵(property key)**
- 當`EventEmitter`物件發出(emits)一個事件(event),所有被附加到(attached)那個特定的(specific)事件上的函式(functions)都會被同步地呼叫(called synchronously)。被呼叫的監聽器(the called listeners)所返回(returned)的任何值都會被忽略(ignored)和丟棄(discarded)掉
- 以下的範例程式碼會展示(shows)具有單一個(single)監聽器(listener)的簡單`EventEmitter`物件實例(instance)。`eventEmitter.on()`方法是用來註冊監聽器(register listeners),而`eventEmitter.emit()`方法則是用來觸發(trigger)一個事件(event)
    ``` js
    const EventEmitter = require('events');

    class MyEmitter extends EventEmitter {}

    const myEmitter = new MyEmitter();
    myEmitter.on('event', () => {
      console.log('an event occurred!');
    });
    myEmitter.emit('event');
    ```
> 傳遞參數與`this`給事件監聽器(Passing arguments and this to listeners)
  + `eventEmitter.emit()`方法允許將任意一組參數(arguments)傳遞給事件監聽器函式(listener functions)。
  + 請記住! 在呼叫普通的(ordinary)事件監聽器函式(listener function)時,`this`這個關鍵字會被刻意地(intentionally)被設定作為引用(reference)事件監聽器(listener)所附加的`EventEmitter`實例(instance)
      ``` js 
      const myEmitter = new MyEmitter();
      myEmitter.on('event', function(a, b) {
        console.log(a, b, this, this === myEmitter);
        // Prints:
        //   a b MyEmitter {
        //     domain: null,
        //     _events: { event: [Function] },
        //     _eventsCount: 1,
        //     _maxListeners: undefined } true
      });
      myEmitter.emit('event', 'a', 'b');
      ```
  + 可以使用ES6的箭頭函式(Arrow Functions)來作為事件監聽器(listeners),然而當這麼做時,`this`關鍵字就不會再引用(reference)`EventEmitter`實例(instance)
      ``` js
      const myEmitter = new MyEmitter();
      myEmitter.on('event', (a, b) => {
        console.log(a, b, this);
        // Prints: a b {}
      });
      myEmitter.emit('event', 'a', 'b');
      ```
> 非同步與同步(Asynchronous vs. synchronous)
  + `Eventemitter`實例(instance)會同步地(synchronously)依據所有的事件監聽器(listeners)的照序(in the order)呼叫,這樣可以確保(ensures)事件的正確排序(proper sequencing),並且能避免爭用條件(race conditions)與邏輯錯誤(logic error)
    * 等到適當時機(appropriate)時,事件監聽器函式(listener functions)可以利用`setImmediate()`方法 & `process.nextTick()`方法來切換到非同步操作模式(asynchronous mode of operation)
    ``` js
    const myEmitter = new MyEmitter();
    myEmitter.on('event', (a, b) => {
      setImmediate(() => {
        console.log('this happens asynchronously');
      });
    });
    myEmitter.emit('event', 'a', 'b');
    ```
> 僅處理事件"1次"(Handling events only once)
  + 若利用`eventEmitter.on()`方法來註冊(registered)一個事件監聽器(listener)時,則當每次(every time)發出(emitted)被命名的事件(named event)時,都會呼叫(invoked)該事件監聽器(listener)
      ``` js
      const myEmitter = new MyEmitter();
      let m = 0;
      myEmitter.on('event', () => {
        console.log(++m);
      });
      myEmitter.emit('event');
      // Prints: 1
      myEmitter.emit('event');
      // Prints: 2
      ```
  + 若利用`eventEmitter.once()`方法的話,可以註冊(register)一個針對特定事件(particular event)才會 **最多被呼叫一次**(called at most once)的事件監聽器(listener)。而一旦這個特定事件被發出(emitted)時,該事件監聽器(listener)就會被取消註冊(unregistered)後,再呼叫(called)該事件監聽器(listener)
      ``` js
      const myEmitter = new MyEmitter();
      let m = 0;
      myEmitter.once('event', () => {
        console.log(++m);
      });
      myEmitter.emit('event');
      // Prints: 1
      myEmitter.emit('event');
      // Ignored
      ```
> 錯誤事件(Error events)
  + 當某個`EventEmitter`實例(instance)發生錯誤(error occurs)時,典型的動作(typical action)是發出(emitted)一個`error`事件(event)。而這些`error`事件(event)會在Node中被視為(treated as)特殊情況(special cases)
  + 若一個`Eventemitter`實例(instance)之中,不存在至少(at least)一個用來註冊(registered)給`error`事件(event)的事件監聽器(listener)的話,同時也已經發出(emitted)了一個`error`事件(event),這時該`error`事件(event)就會被拋出(thrown),並且堆疊追蹤(stack trace)就會被打印(printed)出,最後Node的進程(process)就會退出(exits)
      ``` js
      const myEmitter = new MyEmitter();
      myEmitter.emit('error', new Error('whoops!'));
      // Throws and crashes Node.js
      ```
  + 為了防止(To guard against) Node的進程(process)崩潰(crashing),可以使用Node的[Domain](https://nodejs.org/api/domain.html#domain_domain)內建核心模組
    * 請注意! `Domain`模組已棄置(deprecated)
  + 作為最佳的實踐做法(As a best practice),事件監聽器(listeners)應該總是被新增到`error`事件(event)上
      ``` js
      const myEmitter = new MyEmitter();
      myEmitter.on('error', (err) => {
        console.error('whoops! there was an error');
      });
      myEmitter.emit('error', new Error('whoops!'));
      // Prints: whoops! there was an error
      ```
  + 我們可以透過[events.errorMonitor](https://nodejs.org/api/events.html#events_events_errormonitor)符號(symbol)來安裝一個事件監聽器(listener),以用來監測`error`事件(event),而不用(without)消耗(consuming)發出的錯誤(emitted error)
      ``` js
      const { EventEmitter, errorMonitor } = require('events');

      const myEmitter = new EventEmitter();
      myEmitter.on(errorMonitor, (err) => {
        MyMonitoringTool.log(err);
      });
      myEmitter.emit('error', new Error('whoops!'));
      // Still throws and crashes Node.js
      ```
> Class
  + [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)
    * `EventEmitter`類別(class)是被用來定義 & 公開`Events`模組(module)的
    * 可以這樣引用`EvenEmitter`類別(class)
        ``` js
        const EventEmitter = require('events');
        ```
    * 當新的事件監聽器(new listeners)被新增時,所有的`EvenEmitter`類別(class)都會發出[newListener](https://nodejs.org/api/events.html#events_event_newlistener)這個事件(event); 並且當已經存在的(existed)事件監聽器(listeners)被移除時(removed),會發出[removeListener](https://nodejs.org/api/events.html#events_event_removelistener)這個事件
    * `EventEmitter`類別(class)可以支援[captureRejections](https://nodejs.org/api/events.html#events_capture_rejections_of_promises)這個選項(option)
      * `captureRejections`: (boolean)
        * 這個選項能夠自動地(automatic)捕抓(capturing)被拒絕(rejection)的`Promise`物件
        * 預設值: `false`
  + > Event
    * [newListener](https://nodejs.org/api/events.html#events_event_newlistener)
      * args
        * eventName: (string) | (symbol)
          * 正在監聽的事件(event)的名稱
        * listener: (Function)
          * 事件處理器(event handler)函式(function)
    * `EventEmitter`實例(instance)會在當有一個事件監聽器(listener)被加入到自身內部的(internal)事件監聽器們(listeners)陣列(array)之前,先發出(emit)一個自身(own)的`newListener`事件(event)
    * 為了`newListener`事件(event)而註冊(registered)的事件監聽器們(listeners)將會傳遞(passed)事件名稱與要新增(being added)的事件監聽器(listener)的參考(reference)
    * 事實上,在新增事件監聽器(listener)之前觸發(triggered)的事件中有一個微妙(subtle)卻很重要(important)的副作用(side effect)
      * 任何用來註冊(registered)給同名(same `name`)的`newListener`回呼函式(callback)之內(within)的額外(additional)的事件監聽器們(listeners),都會在那些被新增(added)到進程中(process)的事件監聽器"之前"就會被插入
        ``` js
        class MyEmitter extends EventEmitter {}

        const myEmitter = new MyEmitter();
        // Only do this once so we don't loop forever
        myEmitter.once('newListener', (event, listener) => {
          if (event === 'event') {
            // Insert a new listener in front
            myEmitter.on('event', () => {
              console.log('B');
            });
          }
        });
        myEmitter.on('event', () => {
          console.log('A');
        });
        myEmitter.emit('event');
        // Prints:
        //   B
        //   A
        ```
    * [removeListener](https://nodejs.org/api/events.html#events_event_removelistener)
      * args
        * eventName: (string) | (symbol)
          * 事件(event)的名稱
        * listener: (Function)
          * 事件處理器(event handler)函式(function)
      * 當事件監聽器(listener)被移除(removed)後,會發出(emitted)一個`removeListener`事件(event)
  + [emitter.on(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_on_eventname_listener)
    * args
      * eventName: (string) | (symbol)
        * 事件(event)的名稱
      * listener: (Function)
        * 該事件監聽器要執行的回呼函式(callback function)
      * Returns: (`EventEmitter`)類別(class)
    * 在指定的事件(event, 也就是`eventName`參數的值)中的事件監聽器(listeners)陣列(array)的結尾(end)上新增一個事件監聽器函式(`listener` function, 也就是`listener`參數的值)
      * 不會去檢查是否有該事件監聽器要執行的回呼函式(callback function)已經被新增了
      * 若多次呼叫(Multiple calls)並傳遞相同的`eventName`與`listener`兩個參數值的組合(combination)的話,將導致(result in)此事件監聽器(listener)會被多次(multiple times)呼叫(called) & 新增(added)
      ``` js
      server.on('connection', (stream) => {
        console.log('someone connected!');
      });
      ```
      * 以上的範例程式碼可以看出來,`emitter.on()`方法將會回傳(returns)一個對`EventEmitter`類別(class)的參考(reference),以便讓這個呼叫(calls)可以被串連使用(chained)
    * 預設情況下,事件監聽器(event listeners)們會依照它們被新增的順序(in the order they are added)呼叫(invoked)
      * 另一個替代方案(alternative)是利用[emitter.prependListener(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_prependlistener_eventname_listener)方法來將一個指定的事件監聽器(event listener)新增到整個事件監聽器陣列(the listeners array)的最前面(beginning)
        ``` js
        const myEE = new EventEmitter();
        myEE.on('foo', () => console.log('a'));
        myEE.prependListener('foo', () => console.log('b'));
        myEE.emit('foo');
        // Prints:
        //   b
        //   a
        ```
  + [emitter.emit(eventName[, ...args])](https://nodejs.org/api/events.html#events_emitter_emit_eventname_args)
    * args
      * eventName: (string) | (symbol)
      * ...args: (any)
      * Returns: (boolean)
        * 當該指定的事件名稱(event, 也就是`eventName`參數的值)有事件監聽器(listeners)時,回傳`true`; 反之,則回傳`false`
    * 同步地(Synchronously)呼叫(calls)被註冊(registered)給指定的事件(event, 也就是`eventName`參數的值)中的每一個(each of)事件監聽器(listeners),並依照它們被註冊(registered)的順序(in the order),傳遞(passing)給每個事件監聽器(to each)需要的參數(supplied arguments)
  + [emitter.once(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_once_eventname_listener)
    * args
      * eventName: (string) | (symbol)
        * 事件(event)的名稱
      * listener: (Function)
        * 該事件監聽器要執行的回呼函式(callback function)
      * Returns: (`EventEmitter`)類別(class)
    * 新增一個 **一次性(one-time)** 的事件監聽器函式(`listener` function, 也就是`listener`參數的值)給指定的事件(event, 也就是`eventName`參數的值)
      * 當下一次該指定的`eventName`事件被觸發(triggered)時,這個 **一次性(one-time)** 的事件監聽器函式(listener)就會被呼叫(invoked)並移除(removed)
      ``` js
      server.once('connection', (stream) => {
        console.log('Ah, we have our first user!');
      });
      ```
      * 以上的範例程式碼可以看出來,`emitter.on()`方法將會回傳(returns)一個對`EventEmitter`類別(class)的參考(reference),以便讓這個呼叫(calls)可以被串連使用(chained)
    * 預設情況下,事件監聽器(event listeners)們會依照它們被新增的順序(in the order they are added)呼叫(invoked)
      * 另一個替代方案(alternative)是利用[emitter.prependListener(eventName, listener)](https://nodejs.org/api/events.html#events_emitter_prependlistener_eventname_listener)方法來將一個指定的事件監聽器(event listener)新增到整個事件監聽器陣列(the listeners array)的最前面(beginning)
        ``` js
        const myEE = new EventEmitter();
        myEE.once('foo', () => console.log('a'));
        myEE.prependOnceListener('foo', () => console.log('b'));
        myEE.emit('foo');
        // Prints:
        //   b
        //   a
        ```

### [Buffers](https://nodejs.org/api/buffer.html#buffer_buffer)
- `Buffer`(緩存)物件(object)被用來代表(represent)一個固定長度(fixed-length)的字節序列(sequence of bytes)。有許多Node的官方API都有支援`Buffer`(緩存)
- `Buffer`(緩存)類別(Class)是Javascript的[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)類別的子類別(subclass),並且`Buffer`類別有繼承(extends)了它的許多方法(methods),這些方法們都有涵蓋(cover)了額外(additional)的許多使用情境(use cases)
  + 因為Node的官方API能接受(accept)普通(plain)的`Uint8Array`類別,所以到不論在哪裡(wherever)也都會支持(supported)``Buffer`(緩存)類別
- `Buffer`(緩存)類別(Class)是在全域的(global)範圍(scope)之內,因此不太可能(unlikely)會需要利用`require('buffer').Buffer`的語法來匯入此模組
- `Buffer`緩存物件(object)的範例程式碼
    ``` js
    // Creates a zero-filled Buffer of length 10.
    const buf1 = Buffer.alloc(10);

    // Creates a Buffer of length 10,
    // filled with bytes which all have the value `1`.
    const buf2 = Buffer.alloc(10, 1);

    // Creates an uninitialized buffer of length 10.
    // This is faster than calling Buffer.alloc() but the returned
    // Buffer instance might contain old data that needs to be
    // overwritten using fill(), write(), or other functions that fill the Buffer's
    // contents.
    const buf3 = Buffer.allocUnsafe(10);

    // Creates a Buffer containing the bytes [1, 2, 3].
    const buf4 = Buffer.from([1, 2, 3]);

    // Creates a Buffer containing the bytes [1, 1, 1, 1] – the entries
    // are all truncated using `(value & 255)` to fit into the range 0–255.
    const buf5 = Buffer.from([257, 257.5, -255, '1']);

    // Creates a Buffer containing the UTF-8-encoded bytes for the string 'tést':
    // [0x74, 0xc3, 0xa9, 0x73, 0x74] (in hexadecimal notation)
    // [116, 195, 169, 115, 116] (in decimal notation)
    const buf6 = Buffer.from('tést');

    // Creates a Buffer containing the Latin-1 bytes [0x74, 0xe9, 0x73, 0x74].
    const buf7 = Buffer.from('tést', 'latin1');
    ```
> `Buffers`(緩存)物件們與字元編碼 (Buffers and character encodings)
  + 若要將`Buffer`(緩存)物件(object)轉換(converting)為字串(string)或是反過來轉換的話,可能會需要指定(specified)一個編碼方式。若這時候沒有指定一個編碼方式(character encoding)的話,預設(default)會使用(used)`utf-8`這個編碼方式來進行
      ``` js
      const buf = Buffer.from('hello world', 'utf8');

      console.log(buf.toString('hex'));
      // Prints: 68656c6c6f20776f726c64
      console.log(buf.toString('base64'));
      // Prints: aGVsbG8gd29ybGQ=

      console.log(Buffer.from('fhqwhgads', 'utf8'));
      // Prints: <Buffer 66 68 71 77 68 67 61 64 73>
      console.log(Buffer.from('fhqwhgads', 'utf16le'));
      // Prints: <Buffer 66 00 68 00 71 00 77 00 68 00 67 00 61 00 64 00 73 00>
      ```
  + 以下是目前(currently)Node有支援(supported)的字元編碼(character encodings)方式
    * `utf8`: 多字節(Multi-byte)編碼(encoded)的萬國碼(Unicode)字元們(characters)。有許多的網頁(web pages) & 其它的文件(document)格式(formats)都是使用[utf-8](https://en.wikipedia.org/wiki/UTF-8)這個編碼方式
      * 這是`Buffer`物件所預設(default)的編碼方式(character encoding)
      * 若將`Buffer`物件解碼成(decoding)字串時,並不會僅僅(exclusively)包含(contain)有效的(valid)`UTF-8`編碼格式的資料(data),有錯誤(errors)的萬國碼資料會被以`U+FFFD �`這個萬國碼(Unicode)取代字元(replacement character)來表示(represent)
    * `utf16le`: 多字節(Multi-byte)編碼(encoded)的萬國碼(Unicode)字元們(characters)。不同(Unlike)於`utf8`的是,每一個在字串(string)中的字元(each character)都會以2或是4個位元組(bytes)來編碼(encoded)
      * Node只會支援(supports)[UTF-16](https://en.wikipedia.org/wiki/UTF-16)的變體(variant)=> [little-endian](https://en.wikipedia.org/wiki/Endianness)
    * `latin1`: `Latin-1`是代表(stands for)[ISO-8859-1](https://en.wikipedia.org/wiki/ISO/IEC_8859-1)。這個字元編碼方式(character encoding)僅會支援(supports)從萬國碼(Unicode)的`U+0000`~`U+00FF`字元(character)能編碼(encoded)。每個字元(Each character)都會用單一個位元組(single byte)來編碼(encoded)。那些不在(do not fit into)前述範圍(range)內的字元們(characters)會被截掉(truncated)後,並會關聯(mapped)到前述範圍內的字元(characters)
  + 若利用上述(above)提及(referred)的任何一種方式,將`Buffers`(緩存)物件(object)轉換為字串(string),那就是 **解碼(decoding)** ; 而如果是將字串(string)轉換為`Buffers`(緩存)物件(object),那就是 **編碼(encoding)**
  + Node也有支援(supports)以下2種位元組轉字串(binary-to-text)的編碼方式(encodings)。 **對於位元組轉字串的編碼方式來說,命名慣例(naming convention)是相反的,也就是說將`Buffers`(緩存)物件(object)轉換為字串(string),那就是編碼(encoding); 而如果是將字串(string)轉換為`Buffers`(緩存)物件(object),那就是解碼(decoding)**
    * `base64`: 就是[Base64](https://en.wikipedia.org/wiki/Base64)這個編碼方式(encoding)。
      * 每當透過(from)字串來建立(creating)一個`Buffers`(緩存)物件(object)時,這個編碼方式也能正確地(correctly)接受(accept)自[RFC4648的第5章節](https://tools.ietf.org/html/rfc4648#section-5)中指定(specified)的"`URL`與檔案名稱的安全字母( Filename Safe Alphabet)"。在`base64`這個編碼方式中的所有被包含(contained)在字串中(within)的空白字元(whitespace characters),像是空白(spaces)、`tabs`鍵、換行符(new lines)都會被忽略(ignored)
    * `hex`: 將每個位元組(each bytes)都編碼(encode)為2個16進位(hexadecimal)的字元(characters)
      * 當解碼字串(decoding strings)僅(exclusively)包含(contain)有效的(valid)16進位(hexadecimal)的字元(characters)時,就可能(may)會發生(occur)資料截斷(data truncation)。請見以下範例(example)
  + Node也可以支援(supported)以下的傳統(legacy)字元編碼方式(character encodings)
    * `ascii`: 僅適用於7個位元組(=> 也就是7-bit)的[ASCII](https://en.wikipedia.org/wiki/ASCII)編碼格式的資料(data)
      * 當將字串(string)編碼(encoding)為`Buffers`(緩存)物件(object)時,這就相當(equivalent)於是使用(using)`latin1`這個編碼方式
      * 當將`Buffers`(緩存)物件(object)解碼(decoding)為字串(string)時,使用`ascii`這個編碼方式就會另外(additionally)將每個位元組(each byte)的最高位元(highest bit)移動(unset),然後再解碼(decoding)為`latin1`的編碼格式
      * 通常(Generally),我們沒有理由(no reason)會需要使用`ascii`這種編碼方式,因為已經有`utf-8`編碼格式了(或者,如果對已知數據(the data is known)總是僅能使用`ASCII`編碼格式(ASCII-only)的話 or 在編碼(`encode`)/解碼(`decode`)時,僅能使用`ASCII`編碼格式的文字(ASCII-only text)的話,則`latin1`就會是一個更好的選擇(better choice))
      * **Node之所以會提供(provided)`ASCII`這種編碼格式的原因就只是為了傳統(legacy)的相容性(compatibility)問題**
    * `binary`: 為`latin1`的別名(alias)。可先參考[binary strings](https://developer.mozilla.org/en-US/docs/Web/API/DOMString/Binary)來了解更多關於這個主題(topic)的背景知識(brackground)
      * 其實`binary`這個編碼方式的命名(name)很容易被誤導(misleading),是因為所有這裡所列出(listed)的編碼方式(encodings)都是要將字串(`string`)與二進位元(`binary`)的資料之間做相互轉換(convert between)。 **而對於要將字串(`string`)與`Buffers`(緩存)物件(object)的資料之間做相互轉換(converting between),通常`utf-8`編碼方式會是正確的選擇(right choice)**
    * `ucs2`: 為`utf16le`的別名(alias)。在以前(used to),`UCS-2`這種編碼方式是指參考(refer to)`UTF-16`編碼方式而產生的變形(variant),主要是為了支援(support)那些點位(code points)超過(larger than)`U+FFFF`的字元(characters)
      * 在Node中,這些點位(code points)皆總是有被支援(always supported)了
      ``` js
      Buffer.from('1ag', 'hex');
      // Prints <Buffer 1a>, data truncated when first non-hexadecimal value
      // ('g') encountered.

      Buffer.from('1a7g', 'hex');
      // Prints <Buffer 1a>, data truncated when data ends in single digit ('7').

      Buffer.from('1634', 'hex');
      // Prints <Buffer 16 34>, all data represented.
      ```
  + 現代化網頁瀏覽器皆有遵循[WHATWG Encoding Standard](https://encoding.spec.whatwg.org/),也就是`latin1`、`ISO-8859-1`皆為`win-1252`這個編碼方式的別名(alias)。這也意味(means)著當執行像是`http.get()`方法的時候,如果回傳(returned)的字元編碼格式(charset)為`WHATWG`規格(specification)中列出(listed)的其中一種時,很有可能(possible)其實伺服器(server)實際上(actually)是回傳(returned)以`win-1252`這個編碼方式所編碼過後的資料(=> 也就是`win-1252`-encoded data),所以這時候如果使用`latin1`這個編碼方式來解碼(decode)字元(characters)的話,會造成錯誤(incorrectly)
> `Buffers`(緩存)物件(object) & 迭代 (Buffers and iteration)
  + `Buffers`(緩存)實例(instances)是能夠利用(using)`for...of`語法(syntax)來迭代(iterated over)的
      ``` js
      const buf = Buffer.from([1, 2, 3]);

      for (const b of buf) {
        console.log(b);
      }
      // Prints:
      //   1
      //   2
      //   3
      ```
  + 此外(Additionally),[buf.values()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_buf_values)、[buf.keys()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_buf_keys)、[buf.entries()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_buf_entries)方法(methods)皆可以被用來(used to)建立(create)迭代(iterators)
> Class
  + [Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)
    * `Buffers`(緩存)類別(class)是一個全域(global)的型別(type)以用來更直接(directly)地處理(dealing)二進位制位元(binary)的資料(data)。它能透過許多種方式(in a variety of ways)來建構(constructed)出來
  + > Static method
    * [Buffer.alloc(size[, fill[, encoding]])](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_alloc_size_fill_encoding)
      * args
        * size: (integer)
          * 渴望(desired)新(new)的`Buffers`(緩存)物件(object)長度(length)是多少
        * fill: (string) | (Buffer) | [Unit8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | integer
          * 要預先填進(pre-fill)新(new)的`Buffers`(緩存)物件(object)的值(value)
          * 預設值: `0`
        * encoding: (string)
          * 如果`fill`參數的值是字串(string)型別的話,那麼此`encoding`參數的值就會是這個字串參數的編碼格式(encoding)
          * 預設值: `utf8`
      * 會分配(allocates)一個符合`size`參數值指定大小的新(new)的`Buffers`(緩存)物件(object)。如果`fill`參數的值是`undefined`的話,則`Buffer`物件的值將會是`0`(zero-filled)
          ``` js
          const buf = Buffer.alloc(5);

          console.log(buf);
          // Prints: <Buffer 00 00 00 00 00>
          ```
      * 若`size`參數的值比[buffer.constants.MAX_LENGTH](https://nodejs.org/api/buffer.html#buffer_buffer_constants_max_length)屬性的值還要大(larger)或是`size`參數的值比`0`還要小(smaller)的話,就會拋出(thrown)[ERR_INVALID_ARG_VALUE](https://nodejs.org/api/errors.html#ERR_INVALID_ARG_VALUE)這個錯誤(`error`)
        * 補充: `buffer.constants.MAX_LENGTH`屬性的值會視作業系統(operating system)的位元架構(architectures)而定,例如: `32-bit`的作業系統大約就是1GB左右; 而`64-bit`的作業系統大約就是2GB左右
      * 若`fill`參數的值有給定(specified)的話,則這個新被分配(allocated)的`Buffers`(緩存)物件(object)就會透過呼叫(calling)[buf.fill(fill)](https://nodejs.org/api/buffer.html#buffer_buf_fill_value_offset_end_encoding)方法(method)來初始化(initialized)
          ``` js
          const buf = Buffer.alloc(5, 'a');

          console.log(buf);
          // Prints: <Buffer 61 61 61 61 61>
          ```
      * 若`fill`與`encoding`參數的值都有給定(specified)的話,則這個新被分配(allocated)的`Buffers`(緩存)物件(object)就會透過呼叫(calling)[buf.fill(fill, encoding)](https://nodejs.org/api/buffer.html#buffer_buf_fill_value_offset_end_encoding)方法(method)來初始化(initialized)
          ``` js
          const buf = Buffer.alloc(11, 'aGVsbG8gd29ybGQ=', 'base64');

          console.log(buf);
          // Prints: <Buffer 68 65 6c 6c 6f 20 77 6f 72 6c 64>
          ```
      * 雖然呼叫[Buffer.alloc()](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_alloc_size_fill_encoding)方法可預見(measurably)地會比它的替代方法(alternative)(=> 也就是[Buffer.allocUnsafe()](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_allocunsafe_size)方法)來得慢(slower),但是`Buffer.alloc()`方法能確保(ensures)其所新(newly)建立(created)出來的`Buffer`(緩存)實例(instance)的內容(contents)將永遠不會(never)包含(contain)之前分配過(previous allocations)的敏感性資料(sensitive data),而這也包括(including)了那些尚未被分配(not have been allocated)給`Buffers`實例(instance)的資料(data)
    * [Buffer.allocUnsafe(size)](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_allocunsafe_size)
      * args
        * size: (integer)
          * 渴望(desired)新(new)的`Buffers`(緩存)物件(object)長度(length)是多少
      * 會分配(allocates)一個符合`size`參數值指定大小的新(new)的`Buffers`(緩存)物件(object)。如果`fill`參數的值是`undefined`的話,則`Buffer`物件的值將會是`0`(zero-filled)
      * 透過`Buffer.allocUnsafe()`方法(method)所建立(created)的`Buffers`(緩存)實例(instance)的基本記憶體(underlying memory)將不會被初始化(initialized)。而這個新建立出來的`Buffer`實例的內容(contents)將會是未知(unknown)的,並且可能(may)會包含(contain)敏感性資料(sensitive data)
        * 可使用`Buffer.alloc()`方法來建立一個初始化(initialize)的`Buffer`實例(instance),並預先填進值為`0`(zeros)
          ``` js
          const buf = Buffer.allocUnsafe(10);

          console.log(buf);
          // Prints (contents may vary): <Buffer a0 8b 28 3f 01 00 00 00 50 32>

          buf.fill(0);

          console.log(buf);
          // Prints: <Buffer 00 00 00 00 00 00 00 00 00 00>
          ```
        * 如果`Buffer.allocUnsafe()`方法(method)中的`size`參數的值,若不是數字(number)的話就會拋出(thrown)`TypeError`錯誤(error)
      * `Buffer`(緩存)模組(module)會在[Buffer.poolsize()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_class_property_buffer_poolsize))之中的一個內部(internal)`Buffer`實例(instance)裡面,預先分配(pre-allocates)好一個指定大小(`size`)的區塊。而`Buffer.poolsize()`類別(class)是用來(used as)提供[Buffer.allocUnsafe()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_allocunsafe_size)方法、[Buffer.from(array)](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_from_array)方法、[Buffer.concat()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_concat_list_totallength)方法,以及只有(only)當`size`這個參數的值小於(less than) or 等於(equal to) `Buffer.poolSize >> 1`(=> 也就是[Buffer.poolSize()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_class_property_buffer_poolsize)除以(divided)2後的最大整數值(floor))的時候,才會用到的已經被棄置的`new Buffer(size)`建構子(constructor)來在共用池(pool)之中快速分配(fast allocation)記憶體位置給新(new)的`Buffer`(緩存)實例(instance)
      * 利用此預分配(pre-allocated)好的內部共用池中的記憶體(internal memory pool)是在呼叫(calling)`Buffer.alloc(size, fill)`方法、`Buffer.allocUnsafe(size).fill(fill)`方法之間的一個關鍵的區別
        * 特別的是(Specifically),`Buffer.alloc(size, fill)`方法(method)永遠不會(never)用到(use)內部(internal)`Buffer`(緩存)共用池(pool)中的記憶體; 而`Buffer.allocUnsafe(size).fill(fill)`方法則會在`size`參數的值小於 or 等於 `Buffer.poolSize`的一半(half)時,就會用到內部`Buffer`(緩存)共用池(pool)中的記憶體。雖然這個差異是非常細微(subtle)的,但其實很重要(important)的,尤其是當我們的應用程式(application)要求(requires)額外(additional)的效能(performance)時,這時候就正是`Buffer.allocUnsafe()`方法(method)所能提供(provides)的了
    * [Buffer.concat(list[, totalLength])](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_concat_list_totallength)
      * args
        * list: ([Buffer](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_class_buffer)) | ([Unit8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array))
        * totalLength: (integer)
          * 串連在`list`參數值中的所有`Buffer`(緩存)實例(instance)後的總長度
        * Returns: ([Buffer](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_class_buffer))
      * 此方法會回傳已串連`list`參數值中的所有`Buffer`(緩存)實例(instance)過後的的一個新的`Buffer`(緩存)物件
        * 如果`list`參數值中,沒有任何東西(item); 或是`totalLength`參數值為`0`的話,這兩種情況都會回傳(returned)一個新(new)的零長度(zero-length)的`Buffer`物件
        * 若沒有提供(provided)`totalLength`這個參數值的話,就會透過`list`中的`Buffer`(緩存)實例們(instances)來計算(calculated)它們的加總(adding)後的總長度(their lengths)
        * 若有提供`totalLength`這個參數值的話,就會強迫(coerced)變成一個未指派(unsigned)的整數(integer)。如果將`list`內的所有Buffer`(緩存)`實例們(instances)合併(combined)後已超過(exceeds)`totalLength`參數值的話,那`Buffer.concat()`方法(method)所回傳的`Buffer`實例就會被截斷(truncated)到符合`totalLength`參數值所規範的長度限制
        ``` js
        // Create a single `Buffer` from a list of three `Buffer` instances.

        const buf1 = Buffer.alloc(10);
        const buf2 = Buffer.alloc(14);
        const buf3 = Buffer.alloc(18);
        const totalLength = buf1.length + buf2.length + buf3.length;

        console.log(totalLength);
        // Prints: 42

        const bufA = Buffer.concat([buf1, buf2, buf3], totalLength);

        console.log(bufA);
        // Prints: <Buffer 00 00 00 00 ...>
        console.log(bufA.length);
        // Prints: 42
        ```
      * `Buffer.concat()`方法(method)也可以像[Buffer.allocUnsafe()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_allocunsafe_size)方法一樣能使用(use)內部(internal)`Buffer`(緩存)共用池(pool)
    * [Buffer.from(array)](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_from_array)
      * args
        * array: (integer[])
      * 此方法會利用`array`參數值的位元組(bytes)(=> 必須在`0`~`255`的範圍內)來分配(allocates)一個新(new)的`Buffer`(緩存)實例
          ``` js
          // Creates a new Buffer containing the UTF-8 bytes of the string 'buffer'.
          const buf = Buffer.from([0x62, 0x75, 0x66, 0x66, 0x65, 0x72]);
          ```
      * 若`array`參數的值不是一個Javascript的`Array`型別 or 另一個(another)適用(appropriate)於`Buffer.from()`方法的變形(variants)的型別(type)的話,就會拋出(thrown)一個`TypeError`錯誤(error)
      * `Buffer.from(array)`方法(method)與[Buffer.from(string)](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_from_string_encoding)方法(method)也可以像[Buffer.allocUnsafe()](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_static_method_buffer_allocunsafe_size)方法一樣能使用(use)內部(internal)`Buffer`(緩存)共用池(pool)
    * [Buffer.from(buffer)](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_from_buffer)
      * args
        * buffer: ([Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)) | ([Unit8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array))
          * 指定要從已經存在(existing)的哪個`Buffer`物件 or `Unit8Array`類別來複製資料(copy data)
      * 此方法(method)會將傳遞(passed)給這個方法的`buffer`參數複製(copies)一份資料(data)給新(new)的`Buffer`(緩存)實例(instance)
          ``` js
          const buf1 = Buffer.from('buffer');
          const buf2 = Buffer.from(buf1);

          buf1[0] = 0x61;

          console.log(buf1.toString());
          // Prints: auffer
          console.log(buf2.toString());
          // Prints: buffer
          ```
      * 若`Buffer.from(buffer)`方法(method)的`buffer`參數的值不是一個Javascript的`Buffer`物件 or 另一個(another)適用(appropriate)於`Buffer.from()`方法的變形(variants)的型別(type)的話,就會拋出(thrown)一個`TypeError`錯誤(error)
    * [Buffer.from(string[, encoding])](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_from_string_encoding)
      * args
        * string: (string)
          * 要編碼為`Buffer`物件的字串(string)
        * encoding: (string)
          * 可指定要使用哪種編碼格式(encoding)來編碼字串(string)
          * 預設值: `utf8`
      * 此方法會建立一個新的`Buffer`(緩存)物件,並且這個`Buffer`物件的內容會包括(containing)字串(`string`)參數的值
        * 而此方法(method)的`encoding`參數是用來確認(identifies)我們將要使用(used)哪種編碼格式(character encoding)來將字串(string)轉換(converting)為位元組(`bytes`)
          ``` js
          const buf1 = Buffer.from('this is a tést');
          const buf2 = Buffer.from('7468697320697320612074c3a97374', 'hex');

          console.log(buf1.toString());
          // Prints: this is a tést
          console.log(buf2.toString());
          // Prints: this is a tést
          console.log(buf1.toString('latin1'));
          // Prints: this is a tÃ©st
          ```
      * 若`Buffer.from(string)`方法(method)的`string`參數的值不是屬於Javascript的`string`型別 or 另一個(another)適用(appropriate)於`Buffer.from()`方法的變形(variants)的型別(type)的話,就會拋出(thrown)一個`TypeError`錯誤(error)
  + > method
    * [buf.toJSON()](https://nodejs.org/api/buffer.html#buffer_buf_tojson)
      * Returns: ([object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object))
      * 此方法(method)會回傳(returns)一個`JSON`物件來代表(representation)當要將`Buffer`(緩存)實例(instance)轉換為字串(stringifying)時,[JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)方法有不明確地(implicitly)呼叫`buf.JSON()`方法
      * `Buffer.from()`方法(method)能接受(accepts)從`buf.JSON()`方法所回傳(returned)出來的`JSON`物件(objects)的格式(format)
      * 尤其是(In particular),`buf.toJSON()`方法(method)執行起來就會像(works like)是`Buffer.from(buf)`方法一樣
        ``` js
        const buf = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5]);
        const json = JSON.stringify(buf);

        console.log(json);
        // Prints: {"type":"Buffer","data":[1,2,3,4,5]}

        const copy = JSON.parse(json, (key, value) => {
          return value && value.type === 'Buffer' ?
            Buffer.from(value) :
            value;
        });

        console.log(copy);
        // Prints: <Buffer 01 02 03 04 05>
        ```
    * [buf.toString([encoding[, start[, end]]])](https://nodejs.org/api/buffer.html#buffer_buf_tostring_encoding_start_end)
      * args
        * encoding: (string)
          * 可指定要使用哪種編碼格式(encoding)來編碼字串(string)
          * 預設值: `utf8`
        * start: (integer)
          * 可指定要從偏移(offset)多少位元組(byte)後才開始(start)解碼(decoding)
          * 預設值: `0`
        * end: (integer)
          * 可指定要從偏移(offset)多少位元組(byte)後才停止(stop)解碼(decoding)(=> 也就是不要包含的(not inclusive))
          * 預設值: [buf.length](https://nodejs.org/api/buffer.html#buffer_buf_length)
      * 此方法(method)會根據(according)指定(specified)的編碼方式(character encoding)來解碼(decodes)`buf`物件(也就是=> `encoding`的參數值)
        * 而`start`與`end`參數可以僅在需要解碼(decode only)`buf`物件的子集(subset)時才傳遞(passed)到`buf.toString()`方法(method)裡面,作為參數
        * 若`encoding`參數的值是`utf8`,並且輸入(input)的位元組序列(a byte sequence)不是有效的(not valid)`UTF-8`格式的話,那麼(then)這些每一個(each)無效(invalid)的位元組(byte)就會被`U+FFFD`這個替代字元(replacement character)來取代(replaced)
      * 字串實例(string instance)(=> 必須是以`UTF-16`代碼(code)為單位(unit))的最大長度(maximum length)必須是符合[buffer.constants.MAX_STRING_LENGTH](https://nodejs.org/api/buffer.html#buffer_buffer_constants_max_string_length)屬性值的限制
        ``` js
        const buf1 = Buffer.allocUnsafe(26);

        for (let i = 0; i < 26; i++) {
          // 97 is the decimal ASCII value for 'a'.
          buf1[i] = i + 97;
        }

        console.log(buf1.toString('utf8'));
        // Prints: abcdefghijklmnopqrstuvwxyz
        console.log(buf1.toString('utf8', 0, 5));
        // Prints: abcde

        const buf2 = Buffer.from('tést');

        console.log(buf2.toString('hex'));
        // Prints: 74c3a97374
        console.log(buf2.toString('utf8', 0, 3));
        // Prints: té
        console.log(buf2.toString(undefined, 0, 3));
        // Prints: té
        ```
    * [buf.write(string[, offset[, length]][, encoding])](https://nodejs.org/api/buffer.html#buffer_buf_write_string_offset_length_encoding)
      * args
        * string: (string)
          * 指定要寫入(write)到`Buffer`(緩存)物件(object)裡面的字串(`string`)
        * offset: (integer)
          * 要先跳過(skip)多少數量的位元組(number of bytes)後才開始(starting)將指定的字串(`string`)寫入(write)到`Buffer`(緩存)物件(object)中
        * length: (integer)
          * 可限制最大(maximum)能寫入(write)多少數量的位元組(number of bytes)(並且這個指定大小的數值"不能"超過(not exceed)`buf.length - offset`的值)
          * 預設值: `buf.length - offset`
        * encoding: (string)
          * 可指定要用哪種字元編碼格式(character encoding)來將`string`編碼(encoding)為`Buffer`(緩存)物件(object)
        * Returns: (integer)
          * 此方法(method)會回傳有多少數量的位元組(number of bytes)已經被寫入(written)了
      * 此方法(method)會將`string`(字串)寫入到`Buffer`(緩存)物件(object)中,並根據(according to)`encoding`(字元編碼格式)來編碼(encoding)
        * 此方法(method)的`length`參數(parameter)值是用來代表要寫入(write)的`Buffer`(緩存)物件(object)會用掉多少數量的位元組(number of bytes)。若`buf`物件(object)沒有包含(contain)足夠(enough)的空間(space)能填入整個(entire)指定的`string`(字串)的話,那麼就只會有部分(part)的指定的`string`(字串)會被寫入(written)。然而(However),這樣就不會將那些部分(partially)的編碼過後的字元(encoded characters)了寫入到`Buffer`(緩存)物件(object)中了
          ``` js
          const buf = Buffer.alloc(256);

          const len = buf.write('\u00bd + \u00bc = \u00be', 0);

          console.log(`${len} bytes: ${buf.toString('utf8', 0, len)}`);
          // Prints: 12 bytes: ½ + ¼ = ¾

          const buffer = Buffer.alloc(10);

          const length = buffer.write('abcd', 8);

          console.log(`${length} bytes: ${buffer.toString('utf8', 8, 10)}`);
          // Prints: 2 bytes : ab
          ```

### [Stream](https://nodejs.org/api/stream.html#stream_stream)
- `stream`(串流)是在Node中用來處理(working with)數據串流(streaming data)的抽象(abstract)介面(interface)
  + Node內建的`stream`(串流)模組(module)就是用來提供API來實作(implementing)`stream`
- Node有提供許多的`stream`(串流)物件(objects)。舉例來說,以下2種都算是`stream`實例(instances)
  + 往伺服器端發送的請求([a request to an HTTP server](https://nodejs.org/api/http.html#http_class_http_incomingmessage))
  + 連接(connected)到標準輸出(`stdout`)的串流([process.stdout](https://nodejs.org/api/process.html#process_process_stdout))
- `stream`(串流)是可讀取的(readable) & 可寫入的(writable)
  + 所有的`stream`(串流)都是[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)的實例(instances)
- 如果想要存取(access)`stream`(串流)模組(module)的話,能透過以下範例程式碼
    ``` js
    const stream = require('stream');
    ```
- `stream`(串流)模組(module)對於想要建立新的串流實例型別的話是非常有用的
  + 通常不需要(not necessary)利用(use)`stream`(串流)模組(module)來消化(consume)`stream`(串流)
> 此文件的編排 (Organization of this document)
  + 此文件(document)包含(contains)了2個主要(primary)的部分(sections)以及第3部分為註解(notes)
      * 第1個部分(section)是在說明(explains)要如何在一個應用程式(application)中(within)使用(use)已經存在(existing)的`streams`(串流)
      * 第2部分(section)是在說明(explains)該如何建立(create)新類型(new types)的`streams`(串流)
> `Streams`(串流)的類型 (Types of streams)
  + 在Node中,會有4種基礎的`streams`(串流)的類型(types)
    * [Writable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_writable): 可以向其寫入(written)數據(data)的`streams`(串流)
      * 例: [fs.createWriteStream()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_createwritestream_path_options)
    * [Readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable): 可以從其之中讀取(read)數據(data)的`streams`(串流)
      * 例: [fs.createReadStream()](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_fs_createreadstream_path_options)
    * [Duplex](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_duplex): 同時可以寫入(Writable) & 讀取(Readable)的`streams`(串流)
      * 例: [net.Socket](https://nodejs.org/dist/latest-v15.x/docs/api/net.html#net_class_net_socket)
    * [Transaform](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_transform): 就是一種能在寫入(written) or 讀取(read)時,可以修改(modify)或是轉換(transform)的`streams`(串流)
      * 例: [zlib.createDeflate()](https://nodejs.org/dist/latest-v15.x/docs/api/zlib.html#zlib_zlib_createdeflate_options)
  + 此外(Additionally),`stream`(串流)模組(module)包括(includes)了一些實用(utility)的功能(functions),像是以下這幾種
    * [stream.pipeline()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_stream_pipeline_source_transforms_destination_callback)
    * [stream.finished()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_stream_finished_stream_options_callback)
    * [stream.Readable.from()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_stream_readable_from_iterable_options)
    * [stream.addAbortSignal()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_stream_addabortsignal_signal_stream)
  + > `Stream`(串流)的`Promise`API (Streams Promises API)
    * `stream`/`promise`API會提供(provides)一個能回傳(return)`Promise`物件(objects)而不是(rather than)使用回呼函式(callbacks)的一組(set)非同步(asynchronous)實用功能(utility functions)的`stream`(串流)
    * 這組`Promsie`API能透過(via)以下2種方式來使用(accessible)
      ``` js
      require('stream/promises')
      ```
      ``` js
      require('stream').promises.
      ```
  + > 物件模式 (Object mode)
    * 所有透過Node的APIs操作(operate)下,所建立(created)的`streams`(串流)都僅(exclusively)是字串(strings)、`Buffer`(緩存)、或是`Unit8Array`物件(objects)
    * 然而(however),要利用其它Javascript的值(values)來實現(implementations)`stream`(串流)是有可能(possible)的(=> 但是不包含`null`,因為`null`在`streams`(串流)中是有其它特殊用途(special purpose)的),而以上這種情況的`stream`(串流)就會被視為(considered to)以"物件模式(object mode)"來操作(operate)
  + > 緩存 (Buffering)
    * [Writable](https://nodejs.org/api/stream.html#stream_class_stream_writable)與[Readable](https://nodejs.org/api/stream.html#stream_class_stream_readable)`streams`(串流)都可以將資料儲存(store data)在內部(internal)`buffer`(緩存)中
    * 可能(potentially)緩存(buffered)的資料量(data)是取決於(depends on)傳入(passed into)到`stream`(串流)建構子(constructor)中的高水位線(`highWaterMark`)的選項(option)
      * 對於正常(normal)的`streams`(串流)而言,所謂的高水位線(`highWaterMark`)的選項(option)就是指可以指定(specifies)位元組的總數量([total number of bytes](https://nodejs.org/api/stream.html#stream_highwatermark_discrepancy_after_calling_readable_setencoding))
      * 對於在物件模式(object mode)操作(operating)的`streams`(串流)而言,所謂的高水位線(`highWaterMark`)的選項(option)就是指可以指定(specifies)物件的總數量(`total number of objects`)
    * 當透過呼叫(calls)[stream.push(chunk)](https://nodejs.org/api/stream.html#stream_readable_push_chunk_encoding)方法來實現(implementation)緩存時,資料(data)會被緩存(buffered in)到可讀取串流中(`Readable streams`)
      * 這時,如果此`streams`(串流)的消費者(consumer) **沒有呼叫** (call)[stream.read()](https://nodejs.org/api/stream.html#stream_readable_read_size)方法的話,那麼該資料(data)就會代理(sit in)內部隊列(internal queue)直到(until)該資料被消化掉(consumed)為止
    * 一旦(Once)內部可讀取緩存(internal read `buffer`)的總大小(total size) **到達** (reaches)了指定(specified)的高水位線(`highWaterMark`)門檻(threshold)時,這時該`stream`(串流)就會就會停止(stop)從基礎資源(underlying resource)中讀取(reading)資料(data),直到當前(currently)的緩存資料(the data currently buffered)能被消化(consumed)時才會繼續讀取
      * 也就是說,該`stream`(串流)會停止呼叫內部的[readable._read()](https://nodejs.org/api/stream.html#stream_readable_read_size_1)方法(method),而這個方法就是被用來(used to)填滿(fill)可讀取串流(read buffer)
    * 當[writable.write(chunk)](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback)方法(method)被重複地呼叫(called repeatedly)時,資料(data)就會被緩存(buffered in)到可寫入串流(`Writable streams`)中。當(while)內部可寫入緩存(internal write buffer)的總大小(total size) **低於** (below)設定(set)好的高水位線(`highWaterMark`)門檻(threshold)時,這時呼叫(calls)`writable.write()`方法(method)就會回傳(return)`true`。而一旦(Once)內部可寫入緩存(internal write buffer)的總大小(total size) **到達** (reaches) or **超過** (exceeds)高水位線(`highWaterMark`)門檻(threshold)時,則會回傳(returned)`false`
    * `Stream API`的其中一個關鍵目標(key goal),尤其(particularly)是[stream.pipe()](https://nodejs.org/api/stream.html#stream_readable_pipe_destination_options)方法(method),就是限制(limit)資料(data)的緩存(buffering)在一個可接受(acceptable)的程度(levels)之內,以使資源(resources) & 不同速度的目標(destinations of differing speeds)能夠不會淹沒(overwhelm)掉可用(available)的記憶體空間(memory)
    * **高水位線(`highWaterMark`)選項(option)是一個門檻(threshold),而不是一種限制(limit)。它代表在請求(asking)更多的資料之前,該緩存串流(`stream buffers`)所擁有的資料量(the amount of data)**
      * 通常(in general),它並不會強制執行(enforce)嚴格(strict)的記憶體(memory)限制(limitation)。我們可以在特定(specific)的`stream`(串流)實作(implementations)中選擇(choose to)實施(enforce)更嚴格(stricter)的記憶體限制(limits),但高水位線(`highWaterMark`)這個選項仍然是選擇性(optional)的操作
    * 因為[Duplex](https://nodejs.org/api/stream.html#stream_class_stream_duplex)、[Transform](https://nodejs.org/api/stream.html#stream_class_stream_transform)都是可以被讀取(`Readable`)也可以被寫入(`Writable`)的,每一個`stream`(串流)會維護(maintains)2個用來讀取(reading) & 寫入(writing)的分開(separate)的內部的緩存(internal `buffers`),從而允許(allowing)每一端(each side)獨立(independently)於另一端(the other)進行操作(operate),同時保持(maintaining)適當(appropriate)且有效率(efficient)的數據流(flow of data)
      * 舉例來說,[net.Socket](https://nodejs.org/api/net.html#net_class_net_socket)實例(instances)是雙向串流([Duplex](https://nodejs.org/api/stream.html#stream_class_stream_duplex) `streams`),而它的可讀取這端(`Readable` side)允許(allows)消化(consumption)掉從插座(`socket`)接收(received)到的資料。另外,它的可寫入這端(`Writable` side)允許(allows)將資料寫入(writing data)到該插座(`socket`)中
      * 這麼做是因為資料寫入到插座(data may be written to `socket`)的速率(rate)有可能會比收到的時間來得快(faster) or 慢(slower),因此每一端(each side)應該要獨立地(independently)操作(operate) & `buffer`(緩存)於另一端(the other)
      * 內部緩存機制(The mechanics of the internal buffering)是一個內部實作(internal implementation)的細節(detail),而這個細節可能會在任何一個時間(at any time)被改變(changed)掉
        * 然而(However),對於某些進階(certain advanced)的實作(implementations)而言,內部緩存(`internal buffers`)可以透過`writable.writableBuffer`、`readable.readableBuffer`屬性來得到(retrieved)。但是使用像這樣沒有被正式文件化(undocumented)的屬性(properties),是不被鼓勵(discouraged)這麼做的
> `stream`(串流)消費者的API (API for stream consumers)
  + 幾乎所有的Node應用程式(applications),不管是多簡單,都會以某種方式(manner)來使用`streams`(串流)。接下來的就是一個在Node應用程式中使用`streams`(串流)來實作(implements)一個`HTTP`伺服器(server)的範例程式碼
      ``` js
      const http = require('http');

      const server = http.createServer((req, res) => {
        // `req` is an http.IncomingMessage, which is a readable stream.
        // `res` is an http.ServerResponse, which is a writable stream.

        let body = '';
        // Get the data as utf8 strings.
        // If an encoding is not set, Buffer objects will be received.
        req.setEncoding('utf8');

        // Readable streams emit 'data' events once a listener is added.
        req.on('data', (chunk) => {
          body += chunk;
        });

        // The 'end' event indicates that the entire body has been received.
        req.on('end', () => {
          try {
            const data = JSON.parse(body);
            // Write back something interesting to the user:
            res.write(typeof data);
            res.end();
          } catch (er) {
            // uh oh! bad json!
            res.statusCode = 400;
            return res.end(`error: ${er.message}`);
          }
        });
      });

      server.listen(1337);

      // $ curl localhost:1337 -d "{}"
      // object
      // $ curl localhost:1337 -d "\"foo\""
      // string
      // $ curl localhost:1337 -d "not json"
      // error: Unexpected token o in JSON at position 1
      ```
    * 可寫入串流([Writable streams](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_writable))(=> 就像是上面範例程式碼中的`res`一樣)會公開(expose)一些方法(methods),例如:`res.write()`、`res.end()`這些方法是用來將資料寫入(write data)到`stream`(串流)中的
    * 可讀取串流([Readable streams](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable))會在當資料(data)可以從`stream`(串流)中被讀取(read off)時,使用[EventEmitter](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_class_eventemitter)API來通知(notifying)應用程式的程式碼(application code)
      * 而該可從`stream`(串流)中讀取的資料(available data)可以利用多種方式(multiple ways)來讀取(read from)
    * [Writable streams](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_writable)、[Readable streams](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable)都可以透過多種方式(in various ways)來使用(use)[EventEmitter](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_class_eventemitter)API來傳達(communicate)當前`stream`(串流)的狀態(state)
    * [Duplex](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_duplex)、[Transform](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_transform)都是可寫入串流([Writable streams](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_writable)) & 可讀取串流([Readable streams](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable))
    * 從`stream`(串流)中寫入資料(writing data) or 消化資料(consuming data)的應用程式(applications)不會被要求(not required)要直接(directly)地實作(implement)串流介面(`stream` interfaces),並且通常(generally)沒有理由(have no reason)會需要呼叫(call)`require('stream')`語法
    * 想要實作(implement)新(new)的`streams`類型(types)的開發者(developers)們可以參考(refer to)[API for stream implementers](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_api_for_stream_implementers)章節(section)
  + > 可寫入串流 (Writable streams)
    * 可寫入串流(writable streams)是一個給資料(data)寫入(written)的抽象(abstraction)目的地(destination)
    * 以下是常見的可寫入串流(writable streams)的範例,包括了
      * [HTTP requests, on the client](https://nodejs.org/api/http.html#http_class_http_clientrequest)
      * [HTTP responses, on the server](https://nodejs.org/api/http.html#http_class_http_serverresponse)
      * [fs write streams](https://nodejs.org/api/fs.html#fs_class_fs_writestream)
      * [zlib streams](https://nodejs.org/api/zlib.html)
      * [crypto streams](https://nodejs.org/api/crypto.html)
      * [TCP sockets](https://nodejs.org/api/net.html#net_class_net_socket)
      * [child process stdin](https://nodejs.org/api/child_process.html#child_process_subprocess_stdin)
      * [process.stdout](https://nodejs.org/api/process.html#process_process_stdout) & [process.stderr](https://nodejs.org/api/process.html#process_process_stderr)
    * 以上範例中的其中一些實際(actually)上是[雙向串流(Duplex streams)](https://nodejs.org/api/stream.html#stream_class_stream_duplex)來實現可寫入介面[Writable interface](https://nodejs.org/api/stream.html#stream_class_stream_writable)的
    * 所有的可寫入串流([Writable streams](https://nodejs.org/api/stream.html#stream_class_stream_writable))會實作(implement)由[stream.writable](https://nodejs.org/api/stream.html#stream_class_stream_writable)類別(class)所定義(defined)的介面(interface)
    * 然而(While)特定(specific)的可寫入串流([Writable streams](https://nodejs.org/api/stream.html#stream_class_stream_writable))實例(instances)會在不同方面下(various ways)有所不同(differ in),但是所有可寫入串流(`Writable streams`)都會都會遵從(follow)相同(same)的基礎(fundamental)用法(usage)模式(pattern),就如同以下的範例說明(illustrate)
        ``` js
        const myStream = getWritableStreamSomehow();
        myStream.write('some data');
        myStream.write('some more data');
        myStream.end('done writing data');
        ```
    * > Class
      * [stream.Writable](https://nodejs.org/api/stream.html#stream_class_stream_writable)
      * > Event
        * [pipe](https://nodejs.org/api/stream.html#stream_event_pipe)
          * args
            * src: [stream.Readable](https://nodejs.org/api/stream.html#stream_class_stream_readable)
              * 要傳遞(piping to)給此可寫入(writable)的源頭串流(source `stream`)
          * `pipe`事件(event)會在可讀取串流(readable stream)呼叫(called)[stream.pipe()](https://nodejs.org/api/stream.html#stream_readable_pipe_destination_options)方法(method)時被發出(emitted),並可以將這個可寫入串流(writable stream)添加(adding)到其設定好(its set of)的目的地(destination)
            * ``` js
                const writer = getWritableStreamSomehow();
                const reader = getReadableStreamSomehow();
                writer.on('pipe', (src) => {
                  console.log('Something is piping into the writer.');
                  assert.equal(src, reader);
                });
                reader.pipe(writer);
              ```
      * > method
        * [writable.end([chunk[, encoding]][, callback])](https://nodejs.org/api/stream.html#stream_writable_end_chunk_encoding_callback)
          * args
            * chunk: (string | [Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer) | [Unit8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)) | [any](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Data_types))
              * 要寫入(write)的可選擇性(optional)資料(data)。對於那些"不是"在物件模式(object mode)下操作(operating)的`streams`(串流)來說,`chunk`參數值的型別必須為字串(string) or `Buffer`(緩存) or `Unit8Array`三者之一。而對於物件模式(object mode)的`streams`來說,`chunk`參數值的型別可以是任何Javascript中的任何型別(`any`) or `null`
            * encoding: (string)
              * 如果`chunk`參數的值是字串(string)型別的話,可以指定要使用的編碼格式(encoding)
            * callback: (function)
              * 當`stream`(串流)結束(finished)時,要執行的回呼函式(callback)
            * Returns: ([this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this))
          * 當呼叫(calling)此`writable.end()`方法(method)時,就是以信號發出(signals)已經沒有更多(no more)的資料(data)要寫入(written)到可寫入串流([Writable](https://nodejs.org/api/stream.html#stream_class_stream_writable))中
            * 而可選擇性(optional)輸入的`chunk` & `encoding` 這2個參數(arguments)可允許(allow)在關閉(closing)`stream`(串流)之前,立即(immediately)寫入(written)最後一個(one final)額外(additional)的資料塊(chunk of data)
          * 若在呼叫(calling)[stream.end()](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback)方法(method)之後(after)才呼叫(calling)[stream.write()](https://nodejs.org/api/stream.html#stream_writable_end_chunk_encoding_callback)方法(method)的話,會引發錯誤(raise an error)
            ``` js
            // Write 'hello, ' and then end with 'world!'.
            const fs = require('fs');
            const file = fs.createWriteStream('example.txt');
            file.write('hello, ');
            file.end('world!');
            // Writing more now is not allowed!
            ```
        * [writable.write(chunk[, encoding][, callback])](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_write_chunk_encoding_callback)
          * args
            * chunk: (string | [Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer) | [Unit8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)) | [any](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Data_types))
              * 要寫入(write)的可選擇性(optional)資料(data)。對於那些"不是"在物件模式(object mode)下操作(operating)的`streams`(串流)來說,`chunk`參數值的型別必須為字串(string) or `Buffer`(緩存) or `Unit8Array`三者之一。而對於物件模式(object mode)的`streams`來說,`chunk`參數值的型別可以是任何Javascript中的任何型別(`any`) or `null`
            * encoding: (string)
              * 如果`chunk`參數的值是字串(string)型別的話,可以指定要使用的編碼格式(encoding)
              * 預設值: `utf8`
            * callback: (function)
              * 可指定當刷新(flushed)此資料塊(chunk of data)的時候要呼叫的回呼函式(callback)
            * Returns: (boolean)
              * 若`stream`(串流)希望(wishes)在繼續(continuing)寫入(write)額外(additional)的資料(data)之前,先等待(wait for)`drain`事件(event)被發出(emitted)後,再來呼叫(calling)程式碼(code),則該方法會回傳`false`; 否則(otherwise),就是`true`
          * 此方法(method)會寫入(writes)一些資料(data)到`stream`(串流)中,並在資料被完全處理(fully handled)後,才會呼叫我們所提供(supplied)的回呼函式(callback)
            * 若有發生(occurs)錯誤(error)的話,則回呼函式(callback)可以選擇要不要以`errors`(錯誤)為第1個參數(argument)。為了可靠(reliably)地偵測(detect)到錯誤(errors),我們可以新增(add)一個事件監聽器(event listener)給[error](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_error)(錯誤)事件(event),而回呼函式(callback)會在`error`事件被發出(emitted)之前(before),以非同步(asynchronously)的方式被呼叫(called)
          * 若內部緩存(internal `buffer`)小於(less than)我們所配置(configured)的高水位線(`highWaterMark`),並且當`stream`串流被建立(created)時已經允許(admitted)`chunk`了的話,則此方法會回傳`true`
            * 若此方法回傳`false`,則應停止(stop)進一步(further)嘗試(attempts)要將資料(data)寫入(write)`stream`串流,直到[drain](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_drain)事件(event)被發出(emitted)後
          * 當`stream`串流沒有耗盡(not draining)時,呼叫`writable.write()`方法(method)會緩存`chunk`,並且此方法會回傳`false`。當目前(currently)所有(all)的緩存資料塊(buffered chunks)都耗盡(drained)時(=> 由作業系統(operating system)負責接受(accepted)傳遞(delivery)),[drain](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_drain)事件(event)就會被發出(emitted)
            * 建議當此方法回傳(returns)`false`時,就不要再(no more)寫入任何的資料塊(chunks),直到[drain](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_drain)事件(event)被發出(emitted)後,才能繼續寫入資料塊
            * 然而,呼叫`write()`方法在一個還沒有耗盡(not draining)的`stream`串流上是可允許(allowed)的,Node會緩存(buffer)所有寫入(writter)的資料塊(chunks),直到到(until)達最大記憶體使用量(maximum memory usage)限制發生(occurs)為止,在這時將會無條件(unconditionally)地終止(abort)。即使(even)在終止(abort)之前(before),高記憶體使用率(high memory usage)會造成貧乏(poor)的垃圾回收效能(garbage collector performance)、RSS(一般來說不會被釋放(released back)回作業系統中(system),即使(even)在那些記憶體(memory)已經不再(no longer)被需要(required)後)。若遠端(remote)對等方(peer)不讀取(not read)資料(data)的話,則`TCP`插座(sockets)可能永遠不會耗盡(drain),因此寫入(writing)一個不會被耗盡(not draining)的插座(socket),可能會導致(lead to)遠端(remotely)可被利用(exploitable)的弱點(vulnerability)
          * 當`stream`串流沒有被耗盡時,就寫入資料對[Transform stream](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_transform)來說是尤其有問題(particularly problematic)的,因為`Transform streams`在默認(default)情況下是會暫停(paused)的,直到(until)它們被透過管道傳遞(piped) or 一個`data` or `readable`事件處理器(event handler)被新增(added)後才會繼續開始
          * 如果要寫入(written)的資料(data)能夠按照需求(on demand)生成(generated) or 獲取(fetched),則建議(recommended)將邏輯(logic)封裝(encapsulate)進可讀取串流([Readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable))中,並利用[stream.pipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pipe_destination_options)方法(method)來完成
            * 然而(However),若呼叫`writable.write()`方法是優先使用(preferred)的話,這是有可能(possible)的要尊重背壓(respect backpressure)且避免(avoid)記憶體問題(memory issues),並使用[drain](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_drain)事件(event)
          ``` js
          function write(data, cb) {
            if (!stream.write(data)) {
              stream.once('drain', cb);
            } else {
              process.nextTick(cb);
            }
          }

          // Wait for cb to be called before doing any other write.
          write('hello', () => {
            console.log('Write completed, do more writes now.');
          });
          ```
          * 補充: 一個物件模式(object mode)下的可寫入串流(Writable stream),將總是(always)會忽略(ignore)掉`encoding`這個參數(argument)
      * > properties
        * [writable.writable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_writable)
          * Returns: (boolean)
          * 如果此屬性(`writable.writable`)的值是`true`的話,那就能安全地呼叫[writable.write()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_writable_write_chunk_encoding_callback)方法(method)了,也就是代表該`stream`(串流)沒有被消滅(destroyed)掉 or 發生錯誤(errored) or 結束(ended)
  + > 可讀取串流 (Readable streams)
    * 可讀取串流(readable streams)是一個消耗(consumed)掉資料(data)的抽象(abstraction)來源(source)
    * 以下是常見的可讀取串流(readable streams)的範例,包括了
      * [HTTP responses, on the client](https://nodejs.org/dist/latest-v15.x/docs/api/http.html#http_class_http_incomingmessage)
      * [HTTP requests, on the server](https://nodejs.org/dist/latest-v15.x/docs/api/http.html#http_class_http_incomingmessage)
      * [fs read streams](https://nodejs.org/dist/latest-v15.x/docs/api/fs.html#fs_class_fs_readstream)
      * [zlib streams](https://nodejs.org/dist/latest-v15.x/docs/api/zlib.html)
      * [crypto streams](https://nodejs.org/dist/latest-v15.x/docs/api/crypto.html)
      * [TCP sockets](https://nodejs.org/dist/latest-v15.x/docs/api/net.html#net_class_net_socket)
      * [child process stdout and stderr](https://nodejs.org/dist/latest-v15.x/docs/api/child_process.html#child_process_subprocess_stdout)
      * [process.stdin](https://nodejs.org/dist/latest-v15.x/docs/api/process.html#process_process_stdin)
    * 所有的[可讀取串流](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable)(Readable streams)都是透過[stream.readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable)類別(class)來實作(implement)介面(interface)的
    * > 2種可讀取串流的模式 (Two reading modes)
      * 可讀取串流(readable streams)可以用以下2種有效率(effective)的模式(modes)之一來操作(operate)
        * 流動模式(`flowing`)
        * 暫停模式(`paused`)
      * 以上這2種模式都是從物件模式(object mode)下的串流中分離出來(separate from)的。一個可讀取串流(readable stream)可以是物件模式(object mode)也可以不是,不管它是在流動模式(`flowing` mode) or 暫停模式(`paused` mode)
      * 在流動模式(`flowing` mode)中,資料(data)是從底層系統(underlying system)讀取進來(read from)的,並使用事件(events)自動(automatically)且盡快(as quickly as possible)地透過[EventEmitter](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_class_eventemitter)介面(interface)來將資料提供(provided)給應用程式(application)
      * 在暫停模式(`paused` mode)中,[stream.read()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size)方法(method)必須被明確(explicitly)地呼叫(called)來讀取(reads)`stream`(串流)中的資料塊(chunks of data)
      * 所有的可讀取串流(readable streams)都是從暫停模式(`paused` mode)開始(begin)的,但是都能透過以下的3種方式來切換(switched to)為流動模式(`flowing` mode)
        * 新增(Adding)[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)這個事件處理器(event handler)
        * 呼叫[stream.resume()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_resume)方法(method)
        * 呼叫[stream.pipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pipe_destination_options)方法(method)
      * 所有的可讀取串流(readable streams)也都能透過以下的2種方式來從流動模式(`flowing` mode)切換(switch back to)為暫停模式(`paused` mode)
        * 若"沒有"管道(pipe)傳送的目的地(destinations)的話,則呼叫(calling)[stream.pause()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pause)方法(method)
        * 若有管道(pipe)傳送的目的地(destinations)的話,則要移除(removing)所有(all)的管道(pipe)傳送目的地(destination)
          * 多數的管道(pipe)傳送目的地(destinations)都可以透過呼叫[stream.umpipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_unpipe_destination)方法(method)來移除(removing)掉
      * 有一個要記住的重要觀念就是,在直到(until)可讀取串流(readable streams)提供一種消耗(consuming) or 忽略(ignoring)資料(data)的機制(mechanism)之前,都不會生成(generate)資料(data)。如果消耗機制是被禁用(disable)的 or 取消(taken away)的話,可讀取串流(readable streams)將嘗試(attempt to)停止(stop)生成(generating)資料(data)
      * 基於向後(backward)相容(compatibility)的原因(reasons),移除(removing)[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)事件處理器(event handler)將 **不會自動(autmatically)地暫停(pause)掉stream(串流)**
        * 同樣地,如果有管道(piped)傳送的目的地(destinations)的話,則一旦(once)那些管道目的地耗盡(drain)並要求(ask)更多的資料(more data)時,這時再呼叫[stream.pause()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pause)方法(method)將不能保證(guarantee)`stream`(串流)將會保持(remain)暫停(paused)的
      * 如果將[可讀取串流](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable)(readable streams)被切換為流動模式(`flowing` mode),則將不會有消費者(consumers)能夠(available)處理(handle)這些資料(data),而這些資料也就會遺失(lost)掉。舉例來說(for instance),當在沒有(without)附加(attached)[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)事件(event) or 當事件處理器(event handler)已經從`stream`(串流)中移除(removed from)的情況下時,呼叫(called)[readable.resume()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_resume)方法(method)的話,這樣的情況就會發生(oocur)
      * 新增一個[可讀取串流](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable)(Readable)的事件處理器(event handler)會自動(automatically)地讓`stream`(串流)停止(stop)流動(`flowing`),並且資料會繼續透過[readable.read()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size)方法(method)來消耗(consumed)掉
        * 若[readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_readable)這個事件處理器(event handler)已經被移除(removed)掉,並且有[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)這個事件處理器(event handler)的話,那麼`stream`(串流)將會再(again)開始(start)流動(`flowing`)下去
    * > 3種可讀取串流的狀態 (Three states)
      * 可讀取串流(readable streams)的2種模式(tow modes)的操作(operation),其實是對於可讀取串流(readable streams)在實作(implementation)發生(happening)中的時候,其更複雜(more complicated)的內部(internal)狀態(state)管理(management)的一種簡易(simplified)抽象(abstraction)
      * 具體來說(Specifically),在任何(any)一個假定(given)的時間點(point in time),每一個可讀取串流(`Readable`)都會是以下3種可能(possible)的狀態(states)之一
        * `readable.readableFlowing === null`
        * `readable.readableFlowing === false`
        * `readable.readableFlowing === true`
        * 可參考[readable.readableFlowing#](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_readableflowing)章節的介紹
      * 當`readable.readableFlowing`屬性的值是`null`的話,則將不會提供(provided)消耗(consuming)資料流(stream's data)的機制(mechanism)。因此(Therefore),`stream`(串流)將不會生成(generate)資料(data)。當在這個狀態(state)下時,附加(attaching)一個事件監聽器(listener)給[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)這個事件(event),再呼叫[readable.pipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pipe_destination_options)方法(method),或是呼叫[readable.resume()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_resume)方法(method),則會將`readable.readableFlowing`這個屬性的值切換(switch)為`true`,導致(causing)可讀取串流(Reable)會在資料(data)生成(generated)時,主動(actively)地發出(emitting)事件(events)
      * 呼叫(Calling)[readable.pause()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pause)方法(method) or [readable.unpipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_unpipe_destination)方法(method) or 接受(receiving)背壓(backpressure)將會導致(cause)`readable.readableFlowing`這個屬性的值被設定(set)為`false`,這將會暫時(temporarily)地終止(halting)接下來(following)的事件們(events),但是"不會"終止(halting)生成(generating)資料(data)。當處於這種狀態(state)時,附加(attaching)一個事件監聽器(listener)給[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)這個事件(event)的話,將不會把`readable.readableFlowing`這個屬性的值切換(switch)為`true`
        ``` js
        const { PassThrough, Writable } = require('stream');
        const pass = new PassThrough();
        const writable = new Writable();

        pass.pipe(writable);
        pass.unpipe(writable);
        // readableFlowing is now false.

        pass.on('data', (chunk) => { console.log(chunk.toString()); });
        pass.write('ok');  // Will not emit 'data'.
        pass.resume();     // Must be called to make stream emit 'data'.
        ```
        * 當`readable.readableFlowing`這個屬性的值為`false`的話,資料(data)將可以(may)被累積(accumulating)在`stream`(串流)的內部緩存(internal buffer)之中(within)
    * > 選擇一個API類型 (Choose one API style)
      * 可讀取串流(Readable) API已經發展(evolved)成能跨(accross)多個(multiple)Node版本(versions)並提供(provides)多個(multiple)消耗(consuming)資料流(stream data)的方法(method)
        * 通常來說(In general),開發者(developers)應該要選擇(choose)一個消耗(consuming)資料(data)的方法(method)並且應該永遠(never)不要在單一(single)個`stream`(串流中)使用(use)多個(multiple)方法(methods)來消耗(consume)資料(data)
        * 具體來說(Specifically),使用`on('data')` & `on('readable')` & `pipe()`的組合,或是非同步(asynchronous)的迭代器(iterators)可能會導致不直覺(unintuitive)的行為(behavior)
      * 建議大多數的使用者可以利用[readable.pipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pipe_destination_options)方法(method),因為它能提供(provide)消耗(consuming)資料流(stream data)的最簡單(easiest)的實作(implemented)方式
        * 開發者若要求(require)更細緻(fine-grained)地控制(control over)轉換(transform) & 生成資料(generation of data)的話,能使用[EventEmitter](https://nodejs.org/dist/latest-v15.x/docs/api/events.html#events_class_eventemitter)類別(class)以及`readable.on('readable')`/`readable.read()` or `readable.pause()/readable.resume()` APIs
    * > Class
      * [stream.readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_class_stream_readable)
      * > Event
        * data
          * args
            * chunk ([Buffer](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_class_buffer)) | (String) | (any)
              * 資料塊(The chunk of data)。對於那些不是以物件模式(object mode)來操作(operating in)的`streams`(串流)來說,資料塊(chunk)將會是字串(string) or `Buffer`(緩存)這2種型別之一。而對於是以物件模式(object mode)來操作(operating in)的`streams`(串流)來說,資料塊(chunk)將能是除了`null`以外的Javascript中的任何值(any value)
          * 每當(whenever)`stream`(串流)將資料塊(a chunk of data)的擁有權(ownership)轉移(relinquishing)給消費者(consumer)時,都會發出(emitted)`data`事件(event)
          * 每當(whenever)透過呼叫(calling)[readable.pipe()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pipe_destination_options)方法 or [readable.resume()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_resume)方法 or 附加(attaching)一個事件監聽器(listener)的回呼函式(callback)在`data`這個事件(event)上時,來讓`stream`(串流)被切換(switched)為流動模式(`flowing` mode)的時候,都有可能(may)會發生(occur)發出(emitted)`data`這個事件(event)
          * 每當(whenever)呼叫(called)[readable.read()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size)方法(method)時,並且資料塊(a chunk of data)是能夠(available)被回傳(returned)的情況下時,也可能會發出(emitted)`data`這個事件(event)
          * 附加(Attaching)一個事件監聽器(listener)到那些還尚未(has not been)被明確(explicitly)地暫停(paused)的`stream`(串流)上時,將會把`stream`(串流)切換為流動模式(`flowing` mode)。這時,資料(data)將會在其能夠(available)使用時盡快(as soon as)地傳遞(passed)出去
          * 若該`stream`(串流)的預設(default)編碼格式(encoding)已經有透過[readable.setEncoding()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_setencoding_encoding)方法(method)來指定(specified)過的話,事件監聽器(listener)回呼函式(callback)將會把資料塊(the chunk of data)以字串(string)型別的方式傳遞(passed)出去; 否則(otherwise),資料(data)將會以`Buffer`(緩存)型別的方式來傳遞(passed)出去
            ``` js
            const readable = getReadableStreamSomehow();
            readable.on('data', (chunk) => {
              console.log(`Received ${chunk.length} bytes of data.`);
            });
            ```
        * pause
          * 當呼叫(called)[stream.pause()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pause)方法(method)時,並且[readable.redableFlowing](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_readableflowing)的屬性值"不為"`false`的話,`pause`事件(event)就會被發出(emitted)
        * > method
          * [readable.pause()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_pause)
            * Returns: ([this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this))
            * `readable.pause()`方法(method)會導致(cause)處於流動模式(`flowing` mode)的`stream`(串流)停止(stop)發出(emitting)[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)這個事件(events),並從流動模式(`flowing` mode)中切換出來(switching out of)。任何(any)變成(becomes)可用(available)的資料(data)都將會保持(remain)在內部緩存(internal buffer)中
              ``` js
              const readable = getReadableStreamSomehow();
              readable.on('data', (chunk) => {
                console.log(`Received ${chunk.length} bytes of data.`);
                readable.pause();
                console.log('There will be no additional data for 1 second.');
                setTimeout(() => {
                  console.log('Now data will start flowing again.');
                  readable.resume();
                }, 1000);
              });
              ```
              * 補充: 若有可讀取串流(readable stream)事件監聽器(event listener)的話,`readable.pause()`方法(method)將會 **無效** (no effect)
          * [readable.read([size])](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size)
            * args
              * size: ([Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#number_type))
                * 這是一個可選擇性(optional)要不要給的參數(argument),是用來指定(specify)有多少數量的資料(data)要被讀取(read)
              * Returns: (string) | ([Buffer](https://nodejs.org/dist/latest-v15.x/docs/api/buffer.html#buffer_class_buffer)) | ([null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#null_type)) | ([any](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Data_types))
            * 此`readable.read()`方法(method)會從內部緩存區(internal buffers)拉出(pull)一些資料(data)並且回傳(returns)這些資料。若沒有資料能夠(available)讀取的話,就會回傳(returned)`null`
              * 預設情況(default)下,回傳(returned)的資料會是`Buffer`(緩存)物件(object),除非(unless)我們有透過[readable.setEncoding()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_setencoding_encoding)方法(method)來指定(specify)想要的編碼格式(encoding),或是`stream`(串流)是以物件模式(object mode)來運作(operating)的
            * 此方法(method)中的可選性(optional)填入的參數(argument)`size`可以用來指定(specifies)要讀取(read)多少特定(specific)數量(number)的位元組(bytes)。若`size`參數不是有效(available)的話,那麼就會回傳`null`,除非(unless)`stream`(串流)已經結束(ended)了的話,在這種情況下(in which case),就會將所有在內部緩存(internal buffer)中剩下(remaining)的資料(data)都回傳(returned)出來
              * 若此方法中的`size`參數沒有被指定(specified)的話,將回傳(returned)所有包含(contained)在內部緩存區(internal buffer)中的資料(data)
              * `size`參數的值必須小於 or 等於 1 `Gib`
            * `readable.read()`方法(method)應只能在以暫停模式(`paused` mode)運作(operating)並在可讀取串流(Readable)中呼叫(called)此方法。在流動模式(`flowing` mode)中,直到(until)內部緩存(internal buffer)被完全耗盡(fully drained)時,就會自動地呼叫`readable.read()`方法
              ``` js
              const readable = getReadableStreamSomehow();

              // 'readable' may be triggered multiple times as data is buffered in
              readable.on('readable', () => {
                let chunk;
                console.log('Stream is readable (new data received in buffer)');
                // Use a loop to make sure we read all currently available data
                while (null !== (chunk = readable.read())) {
                  console.log(`Read ${chunk.length} bytes of data...`);
                }
              });

              // 'end' will be triggered once when there is no more data available
              readable.on('end', () => {
                console.log('Reached end of stream.');
              });
              ```
              * 每一次(Each)呼叫(call)`readable.read()`方法都會回傳(returns)一個資料塊(a chunk of data), 或是`null`。而這些資料塊(chunks)並不是串連(concatenated)的
              * 在上面的範例程式碼中,`while`迴圈(loop)是必要(necessary)地用來消耗(consume)當前(currently)`buffer`(緩存)中的所有資料(data)
              * 當讀取(reading)一個比較大(large)的檔案(file)時,`readable.read()`方法將會回傳`null`,到目前為止(so far)已經消耗(consumed)掉所有的緩存(buffered)內容(content)了,但是仍然有更多資料尚未被緩存(buffered)。在這種情況下(In this case),當仍然有更多資料還在`buffer`(緩存)時,就會發出(emitted)一個新(new)的[readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_readable)事件(event)。最後(Finally),當沒有更多(no more)的資料(data)要傳來時,就會發出(emitted)[end](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_end)事件(event)
            * **因此(Therefore),若要從可讀取串流(`readable`)中讀取(read)一個檔案(file)的全部內容(whole contents)的話,跨多個[readable](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_readable)事件(event)來收集(collect)資料塊(chunks)是必要(necessary)的**
              ``` js
              const chunks = [];

              readable.on('readable', () => {
                let chunk;
                while (null !== (chunk = readable.read())) {
                  chunks.push(chunk);
                }
              });

              readable.on('end', () => {
                const content = chunks.join('');
              });
              ```
            * 在物件模式(object mode)中的可讀取串流(`Readable`)將總是在呼叫(call)`readable.read(size)`方法時,回傳(return)單一(single)個項目(item),而不管(regardless of)此方法中的`size`參數(argument)值是被指定為多少
            * 若`readable.read()`方法(method)回傳(returns)一個資料塊(a chunk of data),那麼就會發出(emitted)一個[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_data)事件(event)
            * 若在[end](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_event_end)事件(event)已被發出(emitted)之後才呼叫[stream.read(size)](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_read_size)方法(method)的話,將會回傳(return)`null`。這麼做並不會引發(raised)運行錯誤(`runtime error`)
          * [readable.resume()](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_resume)
            * Returns: ([this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this))
            * `readable.resume()`方法(method)將會導致明確(explicitly)地顯示暫停(paused)的可讀取串流(readable stream)會 **繼續** (resume)發出(emitting)[data](https://nodejs.org/dist/latest-v15.x/docs/api/stream.html#stream_readable_resume)事件(events),並將`stream`(串流)切換(switching)到流動模式(`flowing` mode)中
            * `readable.resume()`方法(method)可以被用來(used to)完全(fully)地消耗(consume)從`stream`(串流)傳遞過來的資料(data),而無需(without)實際(actually)地處理(processing)任何那樣的資料(any of that data)
              ``` js
              getReadableStreamSomehow()
                .resume()
                .on('end', () => {
                  console.log('Reached the end, but did not read anything.');
                });
              ```
              * 補充: 若有可讀取串流(readable stream)事件監聽器(event listener)的話,`readable.resume()`方法(method)將會 **無效** (no effect)
            
---
## 參考資料來源
### 官方文件
- [Node.js](https://nodejs.org/en/)
- [Node Package Manager(NPM)](https://www.npmjs.com/)
- [Yarn](https://classic.yarnpkg.com/en/)
- [Node Version Manager(NVM)](https://github.com/nvm-sh/nvm)
- [Express.js](https://expressjs.com/)

### 網路文章
- [Understanding Worker Threads in Node.js](https://vagrantpi.github.io/2019/11/01/understanding-worker-threads-in-Node.js)
- [深入理解 Node.js 的設計錯誤 — 從 Ryan Dahl 的演講中反思](https://medium.com/rytass/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3-node-js-%E7%9A%84%E8%A8%AD%E8%A8%88%E9%8C%AF%E8%AA%A4-%E5%BE%9E-ryan-dahl-%E7%9A%84%E6%BC%94%E8%AC%9B%E4%B8%AD%E5%8F%8D%E6%80%9D-cedbf32cb188)
- [常見的五個開源專案授權條款,使用軟體更自由](https://noob.tw/open-source-licenses/)

### 網路影片
- [10 Things I Regret About Node.js - Ryan Dahl - JSConf EU](https://www.youtube.com/watch?v=M3BM9TB-8yA&feature=emb_logo)
