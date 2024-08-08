# Networking

重點整理

[TOC]

## Latency/Throughput/Performance
- **延遲 (Latency)**: 將封包從一端傳輸到另一端所需的時間
	![](../assets/pics/networking/compare_latency_and_iops.png)
	[圖片連結](https://youtu.be/fPV0-J4DgOE?si=ut34f--Zk4faMAlv)

- **吞吐量 (Throughput)**: 在一段時間內進入/傳出硬碟的資料量
	![](../assets/pics/networking/compare_throughput_and_latency.png)

- **頻寬 (Bandwidth)**: 在一條邏輯 or 實體通訊路徑中的最大吞吐量
	![](../assets/pics/networking/compare_latency_and_bandwidth.png)
	[圖片連結](https://www.oreilly.com/library/view/high-performance-browser/9781449344757/ch01.html)

- **往返時間 (Round-Trip Time, RTT)**: 從發送封包 ～ 接收到回應封包所需的時間
    - 包括 Processing delay, Queuing delay, Transmission delay, Propagation delay
    - 是衡量網路效能的重要指標之一，數值越小表示網路傳輸效率越高
	![](../assets/pics/networking/rtt_definition.png)

### 4 types of packet delay
![](../assets/pics/networking/packet_delay_processing_and_queue.png)
![](../assets/pics/networking/packet_delay_transmission_and_propagation.png)

- **Processing delay**: 處理封包標頭、bit-level 錯誤檢查、決定封包的目的地，所需花費的時間
    - 通常是微秒級 (10^-6^ 秒)
- **Queuing delay**: 封包在 router 的 queue 中等待被處理的時間
	- 取決於 router 的 traffic load
- **Transmission delay**: 封包從 router 的 output port 輸出到 link 上所需花費的時間
	- 取決於封包長度、link 的頻寬
- **Propagation delay**: 封包在 link 上傳播所需花費的時間
	- 取決於 link 的長度、傳播速度
	- Propagation delay 是由傳輸 link 的可用資料速率所決定的，與客戶端, 伺服器之間的距離無關

### Notes
- <strong>網路資料速率</strong>通常以 bits per second (**bps**) 表示; 而<strong>非網路設備的資料速率</strong>通常以 bytes per second (**Bps**) 表示
- 內容交付網路 (CDN): 在全球範圍內分發內容並從附近位置向客戶端提供內容，使我們能夠顯著減少所有資料封包的傳播時間
    - 雖然我們無法使資料封包傳輸得更快，但我們可以透過策略性地<strong>將伺服器放置在更靠近使用者的位置來縮短距離</strong>，從而減少傳播延遲
- 最後一哩延遲 (Last-Mile Latency): 事實上，網路延遲主要發生在 家戶、辦公室 :arrow_right: ISP :arrow_right: 網際網路 這段上，而非跨越海洋 or 大陸的海底電纜傳輸過程上
    - 根據連接類型、路由方法、部署的技術，僅前幾跳（最後一哩延遲）就可能需要數十毫秒
        - 光纖: 10-20 毫秒 (平均性能最佳)
        - 電纜: 15-40 毫秒
        - 數位用戶線路 (Digital Subscriber Line, DSL): 30-65 毫秒
    - 可運用 `traceroute` 指令，來追蹤資料封包傳輸時，每一跳的路徑
		![](../assets/pics/networking/traceroute_check_latency.png)
		[圖片出處](https://www.oreilly.com/library/view/high-performance-browser/9781449344757/ch01.html)
- 對於大多數網站來說，**延遲才是效能能瓶頸**，而不是頻寬！
	![](../assets/pics/networking/latency_affect_performance.png)
	[圖片出處](https://www.oreilly.com/library/view/high-performance-browser/9781449344757/ch10.html#SPEED_PERFORMANCE_HUMAN_PERCEPTION)
- 實務上，作為一個參考依據，收看一個 HD 串流影片約需 2 ~ 10 Mbps 的頻寬，而 4K 影片則需要 25 Mbps 以上的頻寬，具體取決於影片解析度、編/解碼器
- 參考連結: [O'Reilly: High Performance Browser Networking](https://www.oreilly.com/library/view/high-performance-browser/9781449344757/ch01.html)

## Protocol
![](../assets/pics/networking/osi_model_7_layers_illustration.png)
[圖片出處](https://www.cloudflare.com/zh-tw/learning/ddos/glossary/open-systems-interconnection-model-osi/)

![](../assets/pics/networking/osi_model_bytebytego.png)
[圖片出處](https://x.com/bytebytego/status/1609083351700475905/photo/1)

![](../assets/pics/networking/compare_osi_model_and_tcp_ip_model.png) <br>
[圖片出處](https://blog.bytebytego.com/p/network-protocols-run-the-internet)

### Layer 3: Network Layer
#### IP (Internet Protocol)
- 用途: 負責將封包從一個裝置傳送到另一個裝置
- 重要觀念:
	- IP 流量可以使用 TCP 或 UDP 傳送。TCP 會先執行三次握手，以確保兩個裝置都準備好接收資料，才會開始傳輸; 而 UDP 則是無連接的協定，不需要握手過程

#### ICMP (Internet Control Message Protocol)
- 用途: ICMP 是網路層的協定，用於主機、路由器之間傳遞網絡層資訊。其主要功能包括：
	- **錯誤報告**: 當主機, 網路, port, 協定無法送達時，回報錯誤
	- **回應請求**: 使用 Echo Request, Echo Reply 來測試網絡連通性 (e.g `ping`)
- 重要觀念:
    - ICMP 與傳輸層通訊協定（TCP 或 UDP）沒有關聯，因此，**ICMP 成為無連線的通訊協定，在傳送 ICMP 訊息之前，一個裝置不需要開啟與另一個裝置的連線**
    - ICMP 通訊協定也不允許綁定裝置上的特定 port
- ICMP 封包
	- ICMP 封包是使用 ICMP 通訊協定的封包。 ICMP 封包含一般 IP 標頭之後的 ICMP 標頭。當路由器 or 伺服器需要傳送錯誤訊息時，ICMP 封包主體或資料區段一律會包含造成錯誤之封包 IP 標頭的副本
    	- ICMP packet = IP header + ICMP message(ICMP header + ICMP data)
		![](../assets/pics/networking/icmp_packet_error_message.png) <br>
		[圖片出處](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.astorinonetworks.com%2F2012%2F02%2F13%2Fasa-icmp-error-inspection%2F&psig=AOvVaw2w7nR8du7_lO-fC3yinIRB&ust=1723018168772000&source=images&cd=vfe&opi=89978449&ved=0CBEQjRxqFwoTCPiLwoH134cDFQAAAAAdAAAAABAK)

		- 常見的 ICMP error message
    		- 類型 (Type): 表示 ICMP 訊息的類型，定義了<strong>訊息的總類</strong>
        		- 0: Echo Reply
        		- 3: Destination Unreachable
        		- 8: Echo Request
        		- 11: Time Exceeded
    		- 錯誤代碼 (Code): 用於<strong>進一步細分 Type 的具體情況</strong>。例如: Type 3 (Destination Unreachable) 中
        		- Code 0: Network Unreachable
        		- Code 1: Host Unreachable
        		- Code 3: Port Unreachable
			![](../assets/pics/networking/icmp_protocol_error_message_type_and_code.png)

- 常用的終端機公用程式就是使用 ICMP 來測試網路通訊的可用性
    - `ping`: 是 traceroute 的簡化版本。**ping 將測試兩個裝置之間的連線速度**，並準確報告封包到達其目的地並返回寄件者裝置所需的時間
        - 可用於衡量網路延遲的指標
    - `traceroute`: 可用來<strong>顯示兩個網際網路裝置之間的路由路徑</strong>。路由路徑是連接路由器的實際物理路徑，請求在到達目的地之前必須通過該路由器
        - 一個路由器與另一個路由器之間的旅程稱為「躍點」 (hop)，而 traceroute 也會報告每個躍點所需的時間，這對於<strong>確定網路延遲的來源</strong>很有用
- 常見的網路攻擊: ICMP 回應要求和回應回覆訊息通常用於執行 ping。不幸的是，網路攻擊可以利用這個過程，製造破壞手段，例如: **ICMP 洪水攻擊、死亡之 ping 攻擊**
    - ICMP 洪水攻擊: 是指<strong>攻擊者嘗試使用 ICMP 回應請求封包壓垮目標裝置</strong>。目標不得不處理和回應每個封包，消耗其運算資源，直到合法使用者無法接收服務
		![](../assets/pics/networking/ping-icmp-flood-ddos-attack-diagram.png)

    - 死亡之 Ping 攻擊: 是指<strong>攻擊者將大於封包允許大小的 ping 傳送至目標電腦</strong>，造成機器宕機或崩潰。封包在到達其目標的途中被分段，但<strong>當目標將封包重新組裝成其超出最大值的原始大小時，封包的大小會導致緩衝區溢位。</strong>
		- **如今，死亡之 Ping 攻擊基本上已成過去**。但是，較舊的網路裝置仍然可能受到它的影響
- 參考連結:
    - [Cloudflare: 什麼是網際網路控制訊息通訊協定 (ICMP)？](https://www.cloudflare.com/zh-tw/learning/ddos/glossary/internet-control-message-protocol-icmp/)
    - [informIT: ICMP 訊息類型](https://www.informit.com/articles/article.aspx?p=26557&seqNum=5)

## Tools
### netcat (nc)
- 說明: `netcat` 是 Linux 系統中一個多功能的工具程式，雖然它只是一個小程式，但是能夠做的事情很多，就像瑞士刀一樣，**幾乎任何使用 TCP 或 UDP 封包的動作都可以用它來達成，是許多系統管理者常用的網路診斷工具之一**
- 主要功能包括
    - 伺服器 :left_right_arrow: 客戶端模式: 可指定伺服器端監聽指定的 port，或是讓客戶端連接到指定 port，以<strong>實現 client-server socket communication</strong>
		```bash
		# Server 監聽 2389 port 的訊息
		nc localhost 2389
		```

		```bash
		# Client
		nc localhost 2389
		
		# 在終端機畫面上，隨意輸入一段文字
		Hi, server
		```

    - 檔案傳輸: 可以將檔案從一個裝置傳送到另一個裝置

		> 假設在伺服器端有一個檔案叫作 `test`，且在客戶端上有一個檔案叫作 `testfile`

		```bash
		# Server: 監聽 2389 port，並將其內容寫入 test 檔案中
		nc -l 2389 > test
		```

		```bash
		# Client: 將 testfile 檔案內容傳送到伺服器端
		cat testfile | nc localhost 2389
		```

	- 設定連線超時限制: 在某些情況下，我們不希望連線永遠是開放狀態，因此可以透過 `w` 選項來設定連線的超時限制
		```bash
		# Server: 監聽 2389 port
		nc -l 2389
		```

		```bash
		# Client: 連接到 2389 port，並設定連線超時限制為 10 秒
		nc -w 10 localhost 2389
		```

		- 這麼做後，連線將會在 10 秒後自動關閉
		> 請勿在伺服器端使用將 `-w`, `-l` 參數一起使用，因為這樣 `-w` 參數會被視為無效，連線將會一直保持開放狀態，這樣會有風險！

	- `nc` 可支援 IPv4, IPv6 位址，使用 `-4`, `-6` 選項來指定
    	- IPv4
			```bash
			# Server
			nc -4 -l 2389
			```

			```bash
			# Client
			nc -4 localhost 2389
			```

			```bash
			netstat | grep 2389
			```

			```console
			tcp        0      0 localhost:2389          localhost:50851         ESTABLISHED
			tcp        0      0 localhost:50851         localhost:2389          ESTABLISHED
			```

		- IPv6
			```bash
			# Server
			nc -4 -l 2389
			```

			```bash
			# Client
			nc -4 localhost 2389
			```

			```bash
			netstat | grep 2389
			```

			```console
			# 會看到類似以下的結果，這是一個完整的雙向連接，表明本地主機上的這兩個 port 之間的連接狀態為 ESTABLISHED，表示連接已成功建立並正在進行通訊
			tcp        0      0 localhost:2389          localhost:50851         ESTABLISHED
			tcp        0      0 localhost:50851         localhost:2389          ESTABLISHED
			```

	- 禁用讀取 STDIN: 有時候我們不希望 `netcat` 從 STDIN 讀取資料，我們可以在客戶端使用 `-d` 參數來禁用 STDIN
		```bash
		# Server
		nc -l 2389
		```

		```bash
		# Client
		nc -d localhost 2389
		
		# 從鍵盤輸入 "Hi" (即 STDIN)
		Hi
		```

		- 使用 `-d` 選項，從 STDIN 讀取的內容將被禁用，因此 "Hi" 文字不會被傳送到伺服器端
    - 保持伺服器持續運行: 有時候當客戶端斷開連接，我們仍希望伺服器持續運行，可使用 `-k` 選項讓伺服器持續運行
		```bash
		# Server
		nc -k -l 2389
		```
		
		```bash
		# Client
		nc localhost 2389
		
		# 在鍵盤上按下 Ctrl + C 來結束連接
		^C
		```

	- 可設定當客戶端接收到 EOF (End of File) 後，仍保持連線一段時間。通常情況下，`nc` 客戶端在接收到 EOF 字符後會立即終止，但可以使用 `-q` 選項來宣告要在接收到 EOF 後等待多少秒，再終止連接
		```bash
		# Client: 當客戶端接收到 EOF 字符後，將會等待 5 秒後再終止連接
		nc  -q 5  localhost 2389
		```

    - 可使用 UDP 協定: 預設情況下，`nc` 使用 TCP 協定，但是我們可以使用 `-u` 選項來指定要使用 UDP 協定
		```bash
		# Server
		nc -4 -u -l 2389
		```

		```bash
		# Client
		nc -4 -u localhost 2389
		```

		```bash
		netstat | grep 2389
		```

		```console
		# 會看到類似以下的結果，表示當前的連線是採用 UDP 協定
		udp        0      0 localhost:42634         localhost:2389          ESTABLISHED
		```

	- 參考連結: [8 Practical Linux Netcat NC Command Examples](https://www.thegeekstuff.com/2012/04/nc-command-examples/)

### ping
- 說明: `ping` 工具會透過發送 ICMP 協定的 Echo Request 封包給目的地設備，來測試網路連接的問題
- 主要功能: 能提供以下兩種資訊
    - 回傳的回應數量
    - 花費多久時間來回應
- 常見的 `ping` 指令的選項
    - `-a`: 若能解析，就會將目的地的 IP 位址，解析為 hostname
	- `-f`: 防止 ICMP Echo Request 訊息被路由器分段
    - `-i <TTL>`：設置 Time To Live（TTL）值，範圍為 1 ~ 255
    - `-s <size>`: 指定要傳送的封包大小，預設為 32 bytes
    - `-c <count>`: 指定要傳送的封包數量，預設為 4 個
    - `-i <TTL>`: 設定 Time To Live（TTL）值，範圍為 1 到 255
- 常見用法
	- 透過 `ping` 指令，向 `www.google.com` 發送 5 個封包，每個封包大小為 1500 bytes
		```bash
		sudo ping -c 5 -l 1500 www.google.com
		```

		![](../assets/pics/networking/ping_example.png)
		- 說明: 0% packet loss 表示發送到 `www.google.com` 的每個 ICMP Echo Request 封包都已回傳成功，代表網路連接正常

	- 透過 `ping` 指令，向 localhost IP 位址(loopback address)發送封包，檢測<strong>作業系統的網路功能是否正常</strong>
		```bash
		ping 127.0.0.1
		```

	- 透過 `ping` 指令，查詢目標 IP 位址的 hostname，有助於診斷網路問題 (例如: 確認某個 IP 地址是否正確對應到預期的主機)
		```bash
		ping -a 192.168.1.22
		```

	- 透過 `ping` 指令，測試本機與路由器之間的連線狀況

		> 在內網中，路由器(router)的預設 IP 位址通常是 `192.168.2.1`

		```bash
		ping 192.168.2.1
		```

- 參考連結: [How to Use the Ping Command for Testing in Windows](https://www.lifewire.com/ping-command-2618099)

### traceroute
- 說明: `traceroute` 是一個常用的網絡診斷工具，用於追蹤封包從 來源主機 :arrow_right: 目標主機 所經過的路徑
- 安裝方法:
    - Ubuntu
		```bash
		sudo apt-get install traceroute
		```

    - CentOS
		```bash
		sudo yum install traceroute
		```

- 基本用法
	- 查詢封包到達目標 IP address 所經過的路由器、其 IP 地址、回應時間
    	- 在 `traceroute` 工具中，<strong>只有目標 IP 位址是必填的參數</strong>
		```bash
		traceroute www.google.com
		```

		![](../assets/pics/networking/traceroute_example.png)

- 常用參數
    - `-w`: 設定等待每個回應的時間，預設為 5 秒
    - `-q`: 設定要發送的封包數量，預設為 3 個
    - `m`: 設定最大 TTL（Time To Live）值，也就是跳躍數(hop)，預設為 30 次跳躍
    - `f`: 設定起始 TTL 值，預設為 1
    - `-s`: 使用指定的來源 IP 位址作為封包發送的起點
    - `-n`: 禁止將 IP 位址解析為 hostname
    - `-F`: 禁止將探測封包（ICMP Echo Request）分段傳送，確保封包不會被路由器分段，以更準確地測試網路連線的狀況
- 參考連結: [Linux manual page: traceroute](https://linux.die.net/man/8/traceroute)

### mtr
> `ping`, `traceroute`, `mtr` 這三個都是用來測試網路連線的工具，但是 `mtr` 是 `ping` 和 `traceroute` 的結合體，它可以提供更多的資訊，並且可以持續監控網路連線的狀況

> `ping`: 透過發送 ICMP Echo Request 封包並計算往返時間來測試網路連線的狀況

> `traceroute` 和 `mtr`: 使用 TTL 來追蹤封包從 來源主機 :arrow_right: 目標主機 的路由狀況

- 說明: `mtr` 是一個網路診斷工具，它結合了 `ping` 和 `traceroute` 的功能，可以提供更完整的資訊，並且可以持續監控網路連線的狀況
- 安裝方法:
	- Ubuntu
		```bash
		apt update && apt upgrade
		apt install mtr-tiny
		```

	- CentOS
		```bash
		yum update
		yum install mtr
		```

- 產生 `mtr` 報告
	```
	sudo mtr -rw www.google.com
	```

  	- `r`: 產生報告 (若沒有加上 `-r` 選項，則會在互動式環境下，持續監控)
  	- `w`: 顯示每個躍點的完整主機名稱

- 閱讀 `mtr` 報告
	![](../assets/pics/networking/read_mtr_report.png)

	- `mtr` 報告中的每個編號行代表一個躍點(hop)
    	- 躍點是封包到達目的地所經過的網路節點
    - `Loss%`: 表示每經過一個躍點，所發生的封包遺失百分率
    - `Snt`: 表示發送的封包數量 (預設為 10 個封包)
    - 衡量延遲的指標 (以毫秒為單位)
		- `Last`: 表示最後一個封包的往返時間
        - `Avg`: 表示<strong>所有封包的平均往返時間</strong>
        - `Best`: 表示所有封包的最低往返時間 = (最佳)
        - `Wrst`: 表示所有封包的最高往返時間 = (最差)
        - `StDev`: 提供了每個主機的延遲的標準差。當標準差越高，延遲測量之間的差異就越大
    - 通常，`mtr` 報告可分為三個部分
        - 前 2 ~ 3 躍點: 通常代表 來源主機的 ISP
        - 中間的躍點: 通常代表 傳輸過程中所經過的路由器
            - 不幸的是，如果是中間躍點出現問題，兩個 ISP 解決此問題的能力都有限
        - 最後 2 ~ 3 躍點: 通常代表 目的地主機的 ISP

- 分析 `mtr` 報告
  
	> 在分析 `mtr` 報告時，我們最需要關注的兩件事: **封包遺失率** 和 **延遲**

	![](../assets/pics/networking/mtr_report_example1.png) <br>
	[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/#analyze-mtr-reports)

	- 根據上圖，若在任一躍點上，看到 `Loss` 值異常，則可能是該躍點的路由器出現問題
    	- 然而，因為部分 ISP 會對 `mtr` 工具所使用的 ICMP 協定進行流量限制，因此要觀察後續的躍點才能確定問題所在。若後續躍點的 `Loss` 值為 0%，則表示 ICMP 封包受到 ISP 流量限制

	![](../assets/pics/networking/mtr_report_example2.png) <br>
	[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

	- 根據上圖，若在躍點 2 ～ 躍點 3、躍點 3 ～ 躍點 4 發生 60 % 的封包遺失率，但是後續的躍點卻沒有繼續遺失封包，則表示 ICMP 封包受到 ISP 流量限制，因為後續的幾個躍點僅發生 40 % 的封包遺失率 :arrow_right: **當 `mtr` 報告中出現不同的封包遺失率時，請總是相信較後面的躍點**
	- 有時候，封包遺失的情況會發生在封包回傳的過程，因為封包可以成功地傳送到目標主機上，但卻不容易回傳到來源主機上 :arrow_right: 因此，建議最好<strong>雙向收集 `mtr` 報告，才能較為準確地判斷問題所在</strong>
	- 實務上，封包在傳輸的過程中可能會遇到一些不可避免的問題 (例如: 網際網路可能存在網路退化問題、路由器負載限制、網路維護問題等) :arrow_right: **因此，`Loss` 值在 10 % 左右的少量封包遺失率，都是可接受的範圍**

	![](../assets/pics/networking/mtr_report_example3.png) <br>
	[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

	- 根據上圖，在躍點 3 ～ 躍點 4 發生明顯的網路延遲，並且在後續的躍點中，網路延遲秒數無明顯增加，則可以合理地假設第四個路由器可能有問題

	![](../assets/pics/networking/mtr_report_example4.png) <br>
	[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

	- 根據上圖，乍看之下雖然躍點 4 ～ 躍點 5 之間的網路延遲秒數較高，但是在後續的躍點中，網路延遲秒數急劇下降，則表示第五個路由器有 ICMP 流量限制 :arrow_right: **在評估 `mtr` 報告中，關於網路延遲的問題時，請先看最後一個躍點的 `Loss` 值**
- 在 `mtr` 報告中所發生的常見問題
    - 情況 1: 目標主機的網路連線設定異常
		![](../assets/pics/networking/mtr_report_common_issue_destination_host_networking_improperly_configured.png) <br>
		[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

		- 根據上圖，躍點 8 發生了 100 % 的封包遺失率，並且這是最後一個躍點
    		- 表示雖然流量確實傳送到目標主機了，但因為目標主機未發送回應，這可能是由於網路 or 防火牆 (iptables) 規則設定不當，導致主機丟棄 ICMP 封包
	- 情況 2: 家用 or 商用路由器的 ICMP 流量限制
		![](../assets/pics/networking/mtr_report_residential_or_business_router_icmp_traffic_restriction.png) <br>
		[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

		- 根據上圖，躍點 2 雖然發生了 100 % 的封包遺失率，但是在後續的躍點中，封包遺失率為 0%，這表示躍點 2 的路由器可能對 ICMP 流量進行了限制
    - 情況 3: ISP 的路由器設定異常
		![](../assets/pics/networking/mtr_report_isp_router_improperly_configured.png)

		- 當 `mtr` 報告中出現 **???** 時，可能表示路由器的路由設定不正確，導致封包無法傳送到目標主機

		![](../assets/pics/networking/mtr_report_isp_router_improperly_configured_loop_back.png) <br>
		[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

		- 根據上圖，躍點 4 的路由器設定異常，導致封包在路由器之間循環傳送，需通知網路管理員來解決此問題
    - 情況 4: 受到 ICMP 封包的速率限制，導致封包遺失
		![](../assets/pics/networking/mtr_report_icmp_packet_loss.png) <br>
		[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

		- 根據上圖，躍點 5 發生 60 % 的封包遺失率，但是在後續的躍點中，封包遺失率為 0%，這表示躍點 5 的路由器可能對 ICMP 流量進行了限制
    - 情況 5: 超時問題 (timeout)
		![](../assets/pics/networking/mtr_report_timeout.png) <br>
		[圖片出處](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

		- 根據上圖，有很多種原因可能會導致發生<strong>超時問題</strong>，例如: **路由器丟棄 ICMP 封包, 封包返回路由異常** 等
    		- 超時問題，並<strong>不一定會導致封包遺失，但是可能會導致延遲增加，封包仍能正確送達目標主機</strong>
    		- 超時問題可能發生在路由器基於服務品質（QoS），而丟棄 ICMP 封包; 或是封包返回路由異常
- 參考連結: [Diagnosing Network Issues with MTR](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

### tcpdump/wireshark
#### tcpdump
- 說明: `tcpdump` 是一個網路封包分析工具，可以在 <strong>CLI 介面上</strong>捕獲並分析網路上的封包，常用於網路管理、過濾流量、安全監控、故障排除。`wireshark` 是 `tcpdump` 的 GUI 版本，提供了更多的功能、更直觀的操作介面
- 基本用法
    - 查看網路介面上的封包
		```bash
		# 查看所有網路介面上的封包
		sudo tcpdump -i any
		```

		```bash
		# 查看 eth0 網路介面上的封包
		sudo tcpdump -i eth0
		```

	- 查看 HTTPS 協定的封包: 捕獲所有關於 port 443 的封包
		```bash
		sudo tcpdump -nnSX port 443
		```

		- `nn`: 直接顯示 port 號，而不是將 port 號解析為服務名稱
		- `-S`: 顯示每個 TCP 封包的絕對序列號
		- `-X`: 以十六進位和 ASCII 碼的形式顯示封包內容
	- 查看 tcpdump 原始完整的輸出內容
		```bash
		sudo tcpdump -ttnnvvS
		```

		- `tt`: 顯示原始的時間戳記
		- `nn`: 直接顯示 port 號，而不是將 port 號解析為服務名稱
		- `vv`: 顯示詳細的輸出資訊
		- `S`: 顯示每個 TCP 封包的絕對序列號

- 常見用法
    - 擷取明碼顯示的密鑰、文字、使用者名稱、密碼等
		```bash
		tcpdump -A -i eth0 <port http || ftp || telnet> | grep -i 'user\|pass\|login'
		```

	- 監控可疑域名的流量
		```bash
		tcpdump -i eth0 dst host <suspicious.com>
		```

	- 監控來自特定 IP 範圍的流量
		```bash
		tcpdump src net <IP_RANGE_OF_COUNTRY>
		```

	- 監控來自 SMB (Service Message Block) 協定的流量

		> SMB 流量可用來作為檔案共享、印表機共享 等，因此需要預防潛在的安全問題，例如: WannyCry 勒索病毒

		```bash
		tcpdump -nn -i eth0 port 445
		```

	- 監控同時具有 RESET, ACK 標誌的封包，有助於識別可能的網路攻擊 or 設定問題
		```bash
		tcpdump 'tcp[13] = 41'
		```

    	- `tcp[13]`: 表示 TCP 標頭的第 13 個字節，其中包含 TCP 標誌
    	- `41`: 十六進制數字 0x29，表示同時設置了 RESET（0x04）, ACK（0x10）標誌
- 篩選流量
    - 根據 host, net, port, protocol, src（來源）, dst（目標）來篩選流量
		- 監控來自或發送到指定主機的流量
			```bash
			# host
			tcpdump host 192.168.1.1
			```

		- 監控來自或發送到指定網段的流量
			```bash
			# net
			tcpdump net 192.168.1.0/24
			```

		- 監控來自或發送到指定 port 的流量，或是也可以監控指定 port 範圍的流量
			```bash
			# port
			tcpdump port 80
			```

			```bash
			# port range
			tcpdump portrange 21-23
			```

		- 監控指定的協定的流量
			```bash
			# protocol
			tcpdump tcp
			```

		- 監控來自指定來源的流量
			```bash
			# src
			tcpdump 1.1.1.1
			```

		- 監控發送到指定目標的流量
			```bash
			# dst
			tcpdump dst 1.0.0.1
			```

- 將捕獲的封包內容，儲存為 PCAP 檔案，供其他應用程式進行分析，例如: 網路分析器、入侵檢測系統
    - 儲存 PCAP 檔案
		```bash
		tcpdump  port 80 -w <capture_file.txt>
		```

	- 讀取 PCAP 檔案
		```bash
		tcpdump -r <capture_file.txt>
		```

- `tcpdump` 工具，可使用運算子來更精確地篩選流量，例如: `and`, `or`, `not`
    - `and`: 同時符合兩個條件
		```bash
		tcpdump src 192.168.1.1 and dst port 80
		```

    - `or`: 符合其中一個條件
		```bash
		tcpdump src 192.168.1.1 or dst port 443
		```

    - `not`: 不符合條件
		```bash
		tcpdump not port 22
		```

- 需使用單引號 (`' '`) 來讓 `tcpdump` 工具能跳脫解析某些特殊字元（e.g. 括號 `( )`）
	```bash
	# 捕獲來自 IP address 為 10.0.2.4 並且目標 port 是 3389 或 22 的封包
	tcpdump 'src 10.0.2.4 and (dst port 3389 or 22)'
	```

- 可使用 `grep` 指令，來進一步過濾 `tcpdump` 工具所捕獲的封包
	- 篩選出所有關於 HTTP GET 請求的封包
		```bash
		tcpdump -vvAls0 | grep 'GET'
		```

	- 篩選出所有關於 HTTP Set-Cookie, Host, Cookie 的封包
		```bash
		tcpdump -vvAls0 | grep 'Set-Cookie|Host:|Cookie:'
		```

	- 篩選出 DNS 流量的封包
		```bash
		tcpdump -vvAs0 port 53
		```

- 注意! 舊版本的 `tcpdump` 工具會將封包截斷到 68 ～ 96 bytes 的大小，因此可使用 `s` 選項，來捕獲完整大小的封包
	```bash
	tcpdump -i <network interface> -s 65535 -w <file>
	```

- 比較 `tcpdump`, Wireshark 工具
    - 有時候 `tcpdump` 更適合用於遠端捕獲封包，因為它可以在 CLI 介面上執行，特別是在沒有 GUI 介面，或是未安裝 WireShark 工具的情況下

#### WireShark
- 說明: `Wireshark` 是一個網路封包分析工具，提供了 **GUI 介面**，可以捕獲並分析網路上的封包，並且提供了更多的功能，例如: 顯示封包的內容、統計資訊、封包過濾、封包重組 等

- 參考連結:
    - [A tcpdump Tutorial with Examples](https://danielmiessler.com/p/tcpdump)
    - [tcpdump: Capturing with “tcpdump” for viewing with Wireshark](https://www.wireshark.org/docs/wsug_html_chunked/AppToolstcpdump.html)
    - [How to Use Wireshark to Capture, Filter and Inspect Packets?](https://www.howtogeek.com/104278/how-to-use-wireshark-to-capture-filter-and-inspect-packets/)

### iperf
- 說明: 測試網路頻寬的工具，可用於測試網路的吞吐量、延遲、封包丟失率
- 補充觀念
    - **最大傳輸單元** (Maximum Transmission Unit, **MTU**): 是指在<strong>網路上能夠傳輸的最大封包大小</strong>，通常為 **1500 bytes**
		- **網路連線的 MTU 越大，則可以在單一個封包中傳輸的資料量就越多**。Ethernet frames 由封包 (packet) 或是實際傳送的資料，以及其他網路資訊所組成
    - 抖動 (Jitter): 是指在資料傳輸過程中，連續封包分別的到達時間的變化或不一致性。簡單來說，就是封包傳送到目的地的時間間隔不均勻
        - 在網際網路中，封包在路由器、交換機之間傳輸時，可能會經歷不同的路徑和不同的延遲。可能會受到網路擁塞、路由變更、硬體性能不同等因素的影響，導致封包的到達時間不一致
        - 抖動通常以毫秒（ms）為單位，**抖動越小，則網路的穩定性就越好**
        - 對於講求即時性的應用程式會影響比較大，例如: 語音通話（VoIP）、視訊會議、線上遊戲

- 測試 TCP 連線的頻寬
    - 伺服器端
        - 啟動 server 模式
			```bash
			# iperf3 預設使用 port 5001
			sudo iperf3 -s
			```

	- 客戶端
		- 啟動 client 模式，並指定伺服器端的 IP 位址
			```bash
			# iperf3 預設使用 port 5001
			iperf3 -c 172.31.30.41 --parallel 40 -i 1 -t 2
			```

			- `-c`: 指定伺服器端的 IP 位址
			- `--parallel`: 設定併發連線數，即同時進行 40 個連接來測試頻寬
			- `-i`: 設定報告間隔時間為 1 秒，即每秒輸出一次測試報告
			- `-t`: 設定測試時間為 2 秒，即整個測試會持續 2 秒鐘

			![](../assets/pics/networking/iperf3_test_tcp.png)

			- 客戶端已連接到伺服器端（172.31.30.41）的 TCP port 5001
			- TCP window 大小: 975 KB
			- 每個連線都會使用一個唯一的本地 port（e.g. 49498、49506），來連線到伺服器的 port 5001
			- **Interval**: 測試的時間間隔（0.0-2.0 秒）
			- **Transfer**: 傳輸的總資料量（22.8 GBytes）
			- **Bandwidth**: 達到的總頻寬（97.6 Gbits/sec）
			- 分析
    			- 這個 iperf 測試報告顯示，在 2 秒的測試期間，總共傳輸了 22.8 GBytes 的資料，達到的總頻寬為 97.6 Gbits/sec。測試使用了 40 個併發連接，每個連線都有其唯一的本地端 port
    			- **代表在高併發的情況下，此網路能夠保持非常高的頻寬，適合需要大規模資料傳輸的應用**

- 測試 UDP 連線的頻寬
	- 伺服器端
        - 啟動 server 模式
			```bash
			# iperf3 預設使用 port 5001
			sudo iperf3 -s
			```

	- 客戶端
		- 啟動 client 模式，並指定伺服器端的 IP 位址
			```bash
			# iperf3 預設使用 port 5001
			iperf3 -c 172.31.1.152 -u -b 5g
			```

			- `-c`: 指定伺服器端的 IP 位址
			- `-u`: 使用 UDP 協定，因為預設情況下 iperf3 會採用 TCP 協定
			- `-b`: 設定每秒傳送的速度為 5 Gbits/sec

			![](../assets/pics/networking/iperf3_test_udp.png)

			- IPG target: 2.35 us (kalman adjust): 設定的 UDP datagram 的間隔時間（Inter-Packet Gap）目標為 2.35 微秒，使用 Kalman 濾波器調整
			- 本地端 IP 位址（172.31.20.27）使用 port 39022，連線到伺服器端 IP 位址（172.31.30.41）的 port 5001
			- Interval: 測試時間範圍（0.0-10.0 秒）
			- Transfer: 傳輸的總資料量（5.82 GBytes）
			- Bandwidth: 達到的總頻寬（5.00 Gbits/sec）
			- Jitter: 抖動（0.003 毫秒）
			- Lost/Total Datagrams: 丟失的 datagram 數量/總 datagram 數量（1911/4251700），datagram 遺失率為 0.045 %
			- 分析
    			- 這份 iperf3 報告顯示，在 10 秒的測試期間，總共傳輸了 5.82 GB 的資料，達到的總頻寬為 5.00 Gbits/sec。測試中發送了 4251700 個 datagram，遺失了 1911 個，datagram 的遺失率為 0.045 %。抖動（Jitter）非常小，僅為 0.003 毫秒
    			- **代表此網路性能穩定，這些數據可以用來評估網路在高頻寬下的性能、穩定性**
- 常見選項
    - 通用參數
		- `-f` [k|m|K|M]: 報告結果顯示的單位，以Kbits, Mbits, KBytes, MBytes，例如: `iperf -c 192.168.1.3 -f K`
		- `-i`: 報告顯示的時間間隔(以秒為單位)，例如: `iperf -c 192.168.1.3 -i 2`
		- `-l` [KM]: 緩衝區大小，預設是 8 KB,例如: `iperf -c 192.168.1.3 -l 16`
		- `-m`: 顯示MTU最大值
		- `-o`: 將報告與錯誤信息輸出到檔案，例如: `iperf -c 192.168.1.3 -o c:\iperf-log.txt`
		- `-p`: server 端使用的 port，或 client 端使用的 port，兩端的 port 需一致，例如:`iperf -s -p 9999`
		- `-u`: 使用 UDP 協定
		- `-w`: 指定 TCP window 大小，預設是 8 KB
		- `-B`: 綁定一個主機 IP 位址，可以是介面 or 廣播的 IP 位址，當主機端同時有很多 IP 位址時才需要綁定
		- `-C`: 相容舊版本 (兩端版本不一致時使用)
		- `-M`: 設定 TCP 封包的最大 MTU 值
		- `-N`: 設定 TCP 不延遲，禁用 Nagle 演算法
		- `-V`: 傳輸 IPv6 封包
	- Server 端專用
		- `-D`: 背景服務方式運行，例如: `iperf -s -D`
		- `-U`: 使用單一執行緒使用 UDP 模式
    - Client 端專用
        - `-b`: UDP 測試專用，可以設定每秒傳送的速度
        - `c`: 指定 server 端的 IP 位址
        - `-d`: 同時進行雙向傳輸測試
        - `-n`: 指定傳輸的大小，例如: `iperf -c 192.168.1.3 -n 100000`
        - `-r`: 單獨進行雙向傳輸測試
        - `F`: 使用指定檔案來傳輸
        - `-I`: 使用 stdin 方式當做傳輸內容
        - `-T`: 指定 TTL 值
- 參考連結: [How do I benchmark network throughput between Amazon EC2 Linux instances in the same Amazon VPC](https://repost.aws/knowledge-center/network-throughput-benchmark-linux-ec2)