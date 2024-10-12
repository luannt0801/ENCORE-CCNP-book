
Chương này gồm các chủ đề:
- **Network Device Communication**: Giải thích cách switches chuyển tiếp traffic mạng từ góc nhìn Layer 2 và Router định tuyến traffic từ góc nhìn Layer 3
- **Forwarding Architectures**: Xem xét các cơ chế - mechanisms sử dụng trong Routers và Switches để forward traffic mạng

| Từ   | Enlish                             | Ý nghĩa                           |
| ---- | ---------------------------------- | --------------------------------- |
| MLS  | Multi Layer Switch                 | Bộ chuyển mạch đa lớp             |
| OUI  | organizationally unique identifier | Mã định danh duy nhất của tổ chức |
| CSMA | Carrier Sense Multiple Access      | Sóng mang nhạy cảm đa truy nhập   |
| CD   | Collision Detect                   | Phát hiện xung đột                |

## Layer 2 Forwarding

#### 1. Địa chỉ MAC:
Là một địa chỉ có 48-bit được chia thành 6 octet và được viết dưới hệ hexa (16)
3 octet đầu tiên được cấp phát cho từng tổ chức gọi là mã OUI, các nhà cung cấp sử dụng 3 octet còn lại để đánh cho thiết bị
MAC FF:FF:FF:FF:FF:FF là ngoại lệ và được broadcast cho các thiết bị. Broadcast thường không được forward ra ngoài Layer 3
#### 2. Collosion Domains:
Các thiết bị kết nối với nhau bằng dây cáp, nếu cả 2 thiết bị cùng cố gắng nói chuyện cùng 1 lúc thì thông tin sẽ trở nên hỗn loạn. Lúc này các thiết bị Ethernet sử dụng CSMA/CD. => Các thiêt sbij chỉ có thể truyền hoặc nhận dữ liệu tại 1 thời điểm **half duplex**
- Khi thêm devices vào cáp, mạng kém hiệu quả hơn vì các thiết bị phải đợi đến khi không còn giao tiếp nào (tất cả các thiết bị đều nằm trong vùng va chạm)
 *Các hub mạng làm trầm trọng hơn vấn đề do lặp lại lưu lượng ra khỏi mọi cổng dẫn đến tăng kích thước miền va chạm*
- Khi thêm devices vào cáp, mạng kém hiệu quả hơn vì các thiết bị phải đợi đến khi không còn giao tiếp nào (tất cả các thiết bị đều nằm trong vùng va chạm)
#### 3. VLAN
Thêm một router giữa các segment LAN giúp chia miền broadcast và cung cấp optimal network communication.
**VLAN provide phân đoạn logic bằng cách tạo multiple broadcast domains on the same network switch.**
Các thiết bị trong 1 VLAN không thể giao tiếp với các thiết bị trong một VLAN khác thông qua lưu lượng truyền thông Layer 2 hoặc broadcast traffic (phát sóng).

VLAN được định nghĩa trong IEEE 802.1Q, 32-bit sẽ được thêm vào packet header của gói tin như sau:

| Destination MAC | Source MAC | ==TPID (0x8100)== | ==PCP== | ==DEI== | ==VLAN ID== | Destination IP | Source IP | Payload |
| --------------- | ---------- | ------------- | --- | --- | ------- | -------------- | --------- | ------- |
- **Tag protocol identifier (TPID)**: Trường 16-bits được đặtthành 0x8100 để xác định gói tin là gói tin 802.1Q
- **Priority code point (PCP):** Trường 3-bits biểu thị class of service (CoS) như một phần của Layer 2 quality of service (QoS) giữa các switches
- **Drop elgible indicator (DEI):** Trường 1-bit cho biết liệu gói tin có thể bị loại bỏ khi có tranh chấp băng thông hay không.
- **VLAN identifyer (VLAN ID):** Trường 12-bits chỉ định VLAN được liên kết với 1 network packet

Do có 12 bit để biểu diễn ID của VLAN nên có 4094 VLAN duy nhất:
- VLAN 0: Dành riêng cho lưu lượng 802.1P và không thể sửa hoặc xóa
- VLAN 1L là VLAN mặc định (cũng ko thể sửa hoặc xóa)
- VLAN 2-1001: VLAN bình thường và có thể thêm, sửa, xóa
- VLAN 1002-1005: được dành riêng và không thể xóa
- VLAN 1006-4094: VLAN mở rộng và có thể thêm, sửa, xóa

**Access Ports:**
Mỗi VLAN có duy nhất 1 access port. Cổng này mang lưu lượng từ VLAN chỉ định đến thiết bị được kết nối với nó hoặc từ thiết bị này đến thiết bị khác trên cùng 1 VLAN của switch đó.
- Cấu hình bằng lệnh *switchport mode access vlan 10*
**Trunk Ports:**
Kết nối nhiều VLAN giữa 1 switch và 1 switch, router hoặc tường lửa khác và chỉ sử dụng 1 cổng.
Khi nhận được các gói tin liên kết trunk từ xa, các header được kiểm tra, lưu lượng được liên kết với VLAN thích hợp. Sau đó các header 801.1Q được xóa và lưu lượng được chuyển  tiếp đến cổng tiếp theo dựa trên địa chỉ MAC cho VLAN đó
**Native VLANs:**
Bất kỳ lưu lượng nào được broadcast hoặc nhận trên cổng trunk mà không có thẻ VLAN 802.1Q đều được liên kết với VLAN gốc - VLAN 1.
Native VLAN là VLAN đặc biệt có lưu lượng truy cập đi qua đường Trunk nhưng không chứa thẻ TAG VLAN. Cổng trung kế hỗ trợ cả lưu lượng truy cập từ nhiều VLAN, cũng như lưu lượng không đến từ VLAN.
- Native VLAN tạo ra một kênh truyền thông trung tâm mà không yêu cầu các thiết bị kết nối hỗ trợ VLAN. Các khối dữ liệu truyền qua Native VLAN không được gắn thẻ VLAN ID, giúp đảm bảo tính tương thích với các thiết bị không hỗ trợ VLAN.
- Native VLAN làm cầu nối giữa các thiết bị này mà không cần phải quan tâm đến các đặc điểm VLAN cụ thể.
<u> Các đặc điểm của Native VLAN</u>
- **Giá trị Native VLAN chỉ có giá trị tại 2 đầu đường Trunk**: chúng ta cần xác định một VLAN cụ thể để được gọi là Native VLAN. Điều này giúp các thiết bị mạng biết rằng các khối dữ liệu không được gắn thẻ VLAN ID sẽ thuộc về VLAN nào.
- **Không ảnh hưởng nếu các đường trunk sử dụng Native VLAN khác nhau**: Nếu có nhiều đường trunk, mỗi đường có thể được cấu hình để sử dụng một Native VLAN khác nhau. Điều này có nghĩa là các đường trunk có thể có sự khác biệt về Native VLAN mà không gây ảnh hưởng lẫn nhau. Mỗi đường trunk sẽ xác định và chuyển thông tin của Native VLAN của nó mà không can thiệp vào Native VLAN của các đường khác.
- **Mặc định Native VLAN là VLAN 1**: Trong trường hợp không cấu hình Native VLAN, mặc định sẽ là VLAN 1. Điều này có thể xảy ra nếu không có yêu cầu cụ thể nào về việc đặt Native VLAN.

**Allowed VLANs**
Nếu 1 liên kết trunk và VLAN không được phép đi qua cổng trunk đó --> Lệnh giao diện *switchport trunk allowed vlan {vlan-ids}* chỉ định các VLAN được phép đi qua liên kết

#### 4. Layer2 Diagnostic Commands:

- MAC address table là một phản hồi cho định danh của các switch port và VLANs với những thiết bị nào tham gia.
	Switch xây dựng MAC table bằng cách kiểm kia source MAC address để biết luồng lưu lượng nó nhận được. => Thông tin này được duy trì để thu hẹp miền va chạm bằng cách làm giảm lượng tràn đơn hướng không xác định.

## Layer 3 Forwarding

Có hai phương pháp chính để forwarding layer 3:
- Forwarding traffic đến các thiết bị trong cùng 1 mạng con
- Forwarding traffic đến các thiết bị trong các mạng con khác nhau
#### 1. Local Network Forwarding

Hai thiết bị cùng nằm trên 1 mạng con, dữ liệu được đóng gói với địa chỉ IP và phát hiện rằng đích đến nằm trên cùng 1 mạng.
Thiết bị gửi ban đầu không biết địa chỉ MAC đích (biết địa chỉ MAC của bản thân) 
Giao thức ARP ánh xạ địa chỉ IP layer 3 thành địa chỉ MAC layer 2 và lưu trữ IP và MAC tương ứng của thiết bị. Sau đó bảng ARP được thêm tiêu đề layer 2 thích hợp vào gói dữ liệu trước khi gửi xuống layer 2 để xử lý và chuyển tiếp.

#### 2. Packet Routing

Khi packet được routing giữa 2 devices ở các network khác nhau, data sẽ được đóng gói với địa chỉ IP. Thiết bị sẽ detect được đích đến đang nằm ở network khác và phải được định tuyến.

- Static Route: thiết bị có thể lấy được destination network, subnet mask và next-hops IP address cho network traffic
- Default gateway: là 1 cổng mặc định để ask for the local next-hop IP address for network traffic
- Routes: học từ các routing protocols

#### 3. Routed Switch Port

Liên kết point-to-point giữa các switch để định tuyến. Có thể xây dựng 1 VLAN trung chuyển (VD: VLAN 2001) nhưng luôn có khả năng có VLAN 2001 nào đó tồn tại ở mạng Layer 2 hoặc Spanning Tree tác động đến cấu trúc mạng. 

=> Cổng chuyển mạch đa lớp - multilayer switch port được chuyển đổi từ Layer 2 switch port = cách cấu hình **no switch port** . Sau đó gán địa chỉ IP cho nó.

![[../image/Pasted image 20241003084949.png]]

#### 4. Forwarding Architecture

Router cisco nhận 1 bản tin --> Remove thông tin Layer 2 và xác minh rằng route đã tồn tại cho địa chỉ IP đích.
- Nếu không tìm thấy route phù hợp --> bản tin bị loại bỏ
- Nếu tìm thấy route phù hợp --> Router sẽ thêm thông tin tiêu đề Layer 2 mới vào gói tin.



**Process Switching:** là một kỹ thuật chuyển mạch chậm chạp, trước khi gói tin được gửi ra ngoài phải trải qua nhiều bước. Switch nhận gói tin từ interface đầu vào sau đó mở gói tin và so sánh địa chỉ IP đích với thông tin trong bảng định tuyến của mình. Nếu thông tin về đường đi đến mạng đích đã được lưu trong bảng định tuyến thì switch sẽ tìm địa chỉ MAC của next-hop tương ứng với mạng đích của gói tin.

![[../image/Pasted image 20241003085723.png]]

Nếu chưa có địa chỉ MAC, switch sử dụng giao thức ARP để xác định địa chỉ MAC tương ứng với next-hop. Sau khi có được địa chỉ MAC, switch sẽ rewrite lại thông tin lớp 2 (địa chỉ MAC nguồn và MAC đích) của gói tin và chuyển gói tin sang interface đầu ra. Tiến trình sẽ hoạt động lặp đi lặp lại như vậy với mỗi gói tin switch nhận được. Toàn bộ quá trình trên đều được thực hiển bởi CPU do đó sẽ làm hao tốn một lượng lớn tài nguyên và gây độ trễ trong quá trình định tuyến.

**Fast Switching:** còn gọi là Route Caching, là phương pháp chuyển mạch cải tiến của process switching. So với cơ chế chuyển mạch sử dụng tài nguyên CPU để xử lý tất cả các gói tin đến thì Fast Switching sẽ chỉ xử lý gói tin đầu tiên qua các bước tương tự như Process Switching nhưng sau đó lưu cache lại các thông tin đã được xử lý. Các gói tin đi sau sẽ dựa trên thông tin đã được cache sẵn để đi đến mạng đích. Như vậy nếu như có 100 gói tin đi đến thì switch chỉ cần xử lý 1 gói tin đầu tiên, còn 99 gói tin còn lại sẽ được chuyển mạch dựa trên thông tin đã lưu lại của gói tin trước đó.

**CEF (Cisco Express Forwarding):** hay còn gọi là Topology-base Switching, là phương thức chuyển mạch cho Cisco phát triển để áp dụng cho các dòng Multilayer Switch và Router của hãng. Các thiết bị này được trang bị thêm một bộ chip hardware đặc biệt gọi là Application-Specific Intergrated Circuits (ASICs) được lập trình sẵn và thiết kế để định tuyến giữa các Ethernet port với tốc độ cao. 

Mô hình chuyển mạch sử dụng CEF gồm 2 thành phần chính là Control Plane và Data Plane.

- **Control Plan:** có nhiệm vụ xây dựng và duy trì thông tin định tuyến bao gồm routing table và ARP table. Các thông tin từ các giao thức định tuyến sẽ được tập trung lưu trữ tại đây. Với mỗi gói tin ip đi đến sẽ được xác định chuyển ra interface nào với địa chỉ đích là bao nhiêu. Cơ chế Layer 3 routing của router đề cập ở trên hoạt động tương tự với cơ chế của control plane. 
- **Data Plane:** làm nhiệm vụ định tuyến các gói tin dựa trên những thông tin học được từ control plane. Trong khi ở control plane mọi thứ đều được xử lý bởi CPU thì tại data plane quá trình xử lý định tuyến sẽ được thực hiện bằng phần cứng nhờ vào ASICs. Quá trình xử lý này còn được gọi là layer 3 switching.

**TCAM (Ternary Content Addressable Memory):** 

- Trong cách định tuyến truyền thống, các ACL có thể so khớp, lọc, hoặc điều khiển lưu lượng đặc biệt. Danh sách truy cập được cấu thành từ một hoặc nhiều mục truy cập (ACE - Access Control Entry), hoặc so khớp câu lệnh được ước lượng (Evaluating) trong lệnh theo sau. Việc ước lượng (Evaluating) một danh sách truy cập có thể bổ sung thời gian vào các gói chuyển tiếp.  
- Tuy nhiên trong chuyển mạch đa lớp, tất cả quá trình so khớp mà các ACL cung cấp được thực hiện ở phần cứng. TCAM cho phép một gói được ước lượng dựa vào toàn bộ danh sách truy cập trong bảng tra cứu. Hầu hết switch có nhiều bảng TCAM để bảo mật cả trong và ngoài, và các QoS ACL được ước lượng đồng thời, hoặc hoàn toàn trong quyết định song song chuyển tiếp lớp 2 hoặc lớp 3.  
- Phần mềm IOS của Catalyst có hai thành phần để thực thi hoạt động của TCAM:

• **_Quản lý tính năng FM ( Feature Manager):_** sau khi một danh sách truy cập được tạo hoặc cấu hình, phần mềm quản lý tính năng sẽ biên dịch, và các ACE sẽ được hợp nhất vào trong toàn bộ bảng TCAM. Sau đó TCAM được tra cứu với tốc độ chuyển tiếp frame.  
• **_Quản lý cơ sở dữ liệu chuyển mạch SDM ( Switching Database Manager):_** ta có thể chia TCAM trên các Catalyst switch thành các vùng có chức năng khác nhau. Phần mềm SDM cấu hình hoặc các phần chia TCAM này nếu cần.

**_Cấu trúc bảng TCAM:_**

- TCAM là một bảng mở rộng của bảng CAM, nên nó cũng thực hiện truy tìm dựa trên thuật toán so trùng gồm có hai giá trị vào là bit 0 và 1, cho kết quả nhanh nhưng hoạt động của trừu tượng hơn. Ví dụ giá trị nhị phân (0 và 1) là từ khóa trong bảng, nhưng giá trị mặt nạ cũng được sử dụng để quyết định bit nào có liên quan thực sự. Như vậy từ khóa của bảng TCAM có ba giá trị đó là 0,1 và X.  
![[../image/Pasted image 20241003092443.png]]
- Toàn bộ TCAM được so sánh kết hợp cả ba giá trị, mặt nạ và kết quả (Value, Mask, và Result). Các trường có được từ header của frame hoặc packet và sẽ được dựa vào TCAM. Việc ánh xạ được thực hiện như sau:

• Value: là một chuỗi 134 bit, gồm có địa chỉ nguồn và đích, và các thông tin giao thức liên quan, tất cả đều được so trùng. Thông tin móc nối đến Value liên quan đến kiểu danh sách truy cập được biểu diễn trong bảng 1. Value trong bảng TCAM lấy trực tiếp từ địa chị, port và thông tin giao thức trong ACE.  
• Mask: cũng là một chuỗi 134 bit trong cùng frame. Mark chỉ chọn các bit Value, và bit mask sẽ được thiết lập để so trùng bit Value chính xác. Mask sử dụng bảng TCAM xuất phát từ địa chỉ hoặc bit mask trong các ACE.  
• Result: là giá trị bằng số cho biết hành động sau khi so trùng xảy ra ở bảng TCAM. Ví dụ Result có thể là một quyết định cho phép hoặc không, hoặc giá trị QoS, hoặc con trỏ đến bảng định tuyến kết tiếp…

***Centralized Forwarding:***

Khi một bộ xử lý tuyến đường (Router Processor) được trang bị một bộ chuyển tiếp để có thể đưa ra tất cả các quyết định chuyển mạch gói tin, thì đây được gọi là kiến ​​trúc chuyển tiếp tập trung. 

Đối với kiến ​​trúc chuyển tiếp tập trung, khi một gói tin được nhận trên thẻ đường truyền vào - **ingress line card**, nó được truyền đến công cụ chuyển tiếp trên RP. Công cụ chuyển tiếp kiểm tra tiêu đề của gói tin và xác định rằng gói tin sẽ được gửi ra một cổng trên thẻ đường truyền ra và chuyển tiếp gói tin đến thẻ đường truyền ra để chuyển tiếp.

***Distributed Forwarding:***

Đối với kiến ​​trúc chuyển tiếp phân tán, khi một gói tin được nhận trên **ingress line card**, nó được truyền đến công cụ chuyển tiếp cục bộ. Công cụ chuyển tiếp thực hiện tra cứu gói tin và nếu nó xác định rằng giao diện ra là cục bộ, nó sẽ chuyển tiếp gói tin ra một giao diện cục bộ.
Nếu giao diện ra nằm trên một thẻ đường truyền khác, gói tin được gửi qua cấu trúc chuyển mạch, còn được gọi là mặt phẳng sau (backplane), trực tiếp đến thẻ đường truyền ra, bỏ qua RP.

*Sự khác biệt giữa kiến trúc tập trung và phân tán:*

![[../image/Pasted image 20241003093802.png]]


#### 5. Software CEF

*Software Forwarding Information Base* bao gồm các thành phần sau:

**Forwarding Information Base - FIB:** FIB được xây dựng trực tiếp từ routing table và chứa địa chỉ IP của nexh-hop cho mỗi đích trong mạng.
	Nó giữ 1 ảnh phản chiếu của thông tin chuyển tiếp có trong bảng định tuyến IP. Khi xảy ra thay đổi định tuyến hoặc cấu trúc mạng trong mạng, Routing Table IP được cập nhật và những thay đổi thì sẽ được phản ánh ở FIB.
	=> CEF sử dụng FIB để đưa ra quyết định chuyển mạch dựa trên tiền tố đích IP.
**Adjacency table:** Adjacency Information Base - Cơ sở thông tin kề (AIB), chứa các địa chỉ IP next-hop được kết nối trực tiếp và các địa chỉ MAC next-hop tương ứng của chúng cũng như địa chỉ MAC của giao diện đầu ra. Bảng kề được điền dữ liệu từng bảng ARP hoặc các bảng giao thức Layer 2 khác.

![[../image/Pasted image 20241003094904.png]]

- Khi nhận 1 IP packet, FIB check xem các mục nhập đã hợp lệ không. Nếu mục nhập bị thiếu thì đó là một "glean" adjacency trong CEF, gói tin lúc này phải được chuyển đến CPU vì CEF không thể xử lý được nó. Các mục nhập FIB hợp lệ tiếp tục được xử lý bằng cách kiểm tra bẳng adjacency cho địa chỉ IP đích của mỗi gói tin.
  Các mục nhập adjacency bị thiếu sẽ kích hoạt ARP. Khi ARP được giải quyết --> Có thể tạo mục nhập CEF hoàn chỉnh
- Là một phần của quá trình chuyển tiếp gói tin, các tiêu đề của gói tin được viết lại. Bộ định tuyến ghi đè địa chỉ MAC đích của gói tin bằng địa chỉ MAC của bộ định tuyến hop tiếp theo từ bảng kề, ghi đè địa chỉ MAC nguồn bằng địa chỉ MAC của giao diện Lớp 3 đi, giảm trường thời gian tồn tại (TTL) của IP, tính toán lại tổng kiểm tra tiêu đề IP và cuối cùng chuyển gói tin đến bộ định tuyến bước tiếp theo.

*LƯU Ý Các gói tin được CPU xử lý thường phải tuân theo bộ giới hạn tốc độ khi có sự liền kề không hợp lệ hoặc không đầy đủ để ngăn chặn tình trạng thiếu chu kỳ CPU từ các quy trình thiết yếu khác.*

*LƯU Ý TTL là cơ chế ngăn ngừa vòng lặp Lớp 3, giảm trường TTL của gói tin đi 1 cho mỗi lần nhảy Lớp 3. Nếu bộ định tuyến nhận được gói tin có TTL bằng 0, gói tin sẽ bị loại bỏ.*

#### 6. Hardware CEF

**ASIC** trong routers dựa trên phần cứng rất tốt kém về thiết kế, sản xuất và khắc phục sự cố.
Nó cho phép tốc độ gói tin rất cao nhưng đánh đổi là chúng bị hạn chế về chức năng vì chúng được kết nối để thực hiện các tác vụ cụ thể. 
Các router được trang bị **NPU - Network Processing Unit** để khắc phục tính không linh hoạt của ASIC.
NPU có thể lập trình được.

Kiến trúc distributed forwarding giúp hiệu suất thông lượng gói tin được cải thiện đáng kể bằng cách chuyển giao trách nhiệm chuyển mạch gói tin cho card đường truyền.

#### 7. Stateful Switchover

Router luôn được thiết kế:
- Tính khả dụng cao: Dự phòng phần cứng như bộ nguồn kép, dự phòng Router Processing (RP)|
	  ***Lỗi RP*** có thể kích hoạt các giao thức định tuyến kề nhau để thiết lập lại, dẫn đến mất gói tin và mất ổn định mạng. Trong *quá trình RP bị lỗi*, có thể mong muốn hơn là **ẩn lỗi và cho phép bộ định tuyến tiếp tục chuyển tiếp các gói tin bằng các mục bảng CEF đã lập trình trước đó thay vì tạm thời loại bỏ các gói tin trong khi chờ RP thứ cấp thiết lập lại** các giao thức định tuyến kề nhau và xây dựng lại bảng chuyển tiếp.
- Stateful switchover (SSO) là tính năng dự phòng cho phép<font color="#ff0000"> Router Cisco có hai RP</font> đồng bộ hóa thông tin cấu hình bộ định tuyến và trạng thái mặt phẳng điều khiển.
	Quá trình phản chiếu thông tin giữa các RP được gọi là <font color="#ff0000">checkpointing</font>. Các bộ định tuyến hỗ trợ SSO luôn kiểm tra hoạt động của thẻ đường truyền và trạng thái giao thức Lớp 2. Trong <font color="#e36c09">quá trình chuyển đổi, RP dự phòng sẽ ngay lập tức nắm quyền điều khiển và ngăn ngừa các sự cố cơ bản như lỗi liên kết giao diện.</font> Tuy nhiên, việc chuyển tiếp gói tin Lớp 3 bị gián đoạn nếu không có cấu hình bổ sung.

- **Chuyển đổi RP** kích hoạt một giao thức định tuyến adjacency flap xóa bảng định tuyến.
	
	Khi bảng định tuyến được xóa, các mục <font color="#e36c09">CEF sẽ bị xóa và lưu lượng không còn được định tuyến</font> cho đến khi cấu trúc mạng được học lại và bảng chuyển tiếp được lập trình lại.
	
	Việc bật khả năng chuyển tiếp không dừng - nonstop forwarding (NSF) hoặc định tuyến không dừng - nonstop routing (NSR) sẽ thông báo cho bộ định tuyến duy trì các mục CEF trong một khoảng thời gian ngắn và tiếp tục chuyển tiếp các gói tin thông qua lỗi RP cho đến khi mặt phẳng điều khiển phục hồi.

#### 8. SDM-Switching Database Manager Templates

Dung lượng địa chỉ MAC mà một switch cần so với số tuyến mà nó nắm giữ phụ thuộc vào nơi nó được triển khai trong mạng.
Bộ nhớ được sử dụng cho các bảng TCAM bị giới hạn và được phân bổ tĩnh trong quá trình khởi động của switch.
Khi <font color="#f79646">một phần của tài nguyên phần cứng đầy</font>,<font color="#ff0000"> tất cả quá trình tràn xử lý sẽ được gửi đến CPU</font>, điều này ảnh hưởng nghiêm trọng đến hiệu suất của switch.

Tỷ lệ phân bổ giữa các bảng TCAM khác nhau được lưu trữ và có thể được sửa đổi bằng các mẫu Switching Database Manager (SDM). Có nhiều bộ chuyển mạch Cisco và mẫu SDM có thể được cấu hình trên các bộ chuyển mạch Catalyst 9000 bằng lệnh cấu hình toàn cục sdm prefer {vlan | advanced}. Sau đó, phải khởi động lại bộ chuyển mạch bằng lệnh reload.

*<font color="#ff0000">*LƯU Ý Mọi thiết bị chuyển mạch trong ngăn xếp thiết bị chuyển mạch phải được cấu hình với cùng một mẫu SDM.*</font>

