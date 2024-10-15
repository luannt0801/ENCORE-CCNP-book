
Chương này bao gồm các chủ đề sau:

**Routing Protocol Overview:** Phần này giải thích cách các giao thức định tuyến khác nhau quảng cáo và xác định tuyến đường.

**Path Selection:** Phần này giải thích logic mà bộ định tuyến sử dụng để xác định tuyến đường tốt nhất và cài đặt nó vào bảng định tuyến.

**Static Routing:** Phần này cung cấp tổng quan ngắn gọn về các khái niệm cơ bản về định tuyến tĩnh.

**Virutal Routing and Forwarding**: Phần này giải thích về việc tạo bộ định tuyến logic trên bộ định tuyến vật lý.

Chương này xem lại các nguyên tắc cơ bản từ Chương 1, "Chuyển tiếp gói tin", cũng như một số thành phần của hoạt động của bộ định tuyến. Chương này củng cố logic lập trình của **Routing Information Base (RIB)**, xem xét sự khác biệt giữa các giao thức định tuyến thông thường và giải thích các khái niệm chung liên quan đến tuyến tĩnh.

As described in the previous chapters, a router is necessary to transmit packets between network segments. This chapter explains the process <font color="#ff0000">a router uses to insert routes into the routing table from routing protocol databases and the methodology for selecting a path</font>. A brief overview of static routing is provided as well. By the end of this chapter, you should have a solid understanding of the routing processes on a router.

### **1. Routing Protocol Overview**

Chức năng chính của một bộ định tuyến (router) là chuyển một gói IP từ mạng này sang mạng khác. Một bộ định tuyến học về các mạng không gắn liền thông qua việc cấu hình các tuyến tĩnh (static routes) hoặc qua các giao thức định tuyến IP động (dynamic IP routing protocols).

Các giao thức định tuyến IP động phân phối thông tin về cấu trúc mạng giữa các bộ định tuyến và cung cấp cập nhật mà không cần can thiệp khi có sự thay đổi trong cấu trúc mạng. Các yêu cầu thiết kế hoặc giới hạn phần cứng có thể hạn chế việc định tuyến IP chỉ còn các tuyến tĩnh, không thích ứng tốt với sự thay đổi cấu trúc và có thể gây gánh nặng cho các kỹ sư mạng, tùy thuộc vào kích thước của mạng. Với các giao thức định tuyến động, các bộ định tuyến cố gắng chọn đường đi tốt nhất, không có vòng lặp, để chuyển tiếp gói đến địa chỉ IP đích.

Một mạng của các bộ định tuyến và các hệ thống liên kết với nhau được quản lý dưới một quản trị mạng chung được gọi là hệ thống tự trị (autonomous system - AS), hay miền định tuyến (routing domain). Internet bao gồm hàng ngàn hệ thống tự trị trải dài trên toàn cầu.

Các giao thức định tuyến động phổ biến hiện nay trên hầu hết các nền tảng định tuyến bao gồm:

1. Routing Information Protocol Version 2 (RIPv2)
2. Enhanced Interior Gateway Routing (EIGRP)
3. Open Shortest Path First (OSPF)
4. Intermediate System-to-Intermediate System (IS-IS)
5. Border Gateway Protocol (BGP)

Ngoại trừ BGP, các giao thức trong danh sách này được thiết kế và tối ưu hóa cho việc định tuyến trong một hệ thống tự trị và được gọi là <font color="#00b050">Giao thức Định tuyến Nội bộ (Interior Gateway Protocols - IGPs)</font>. Các <font color="#ff0000">Giao thức Định tuyến Ngoại biên (Exterior Gateway Protocols - EGPs)</font> định tuyến giữa các hệ thống tự trị.

BGP là một giao thức EGP nhưng cũng có thể được sử dụng trong một hệ thống tự trị. Nếu BGP trao đổi các tuyến trong một hệ thống tự trị, nó được gọi là phiên iBGP (interior BGP). Nếu nó trao đổi các tuyến giữa các hệ thống tự trị khác nhau, nó được gọi là phiên eBGP (exterior BGP).

Hình 6-1 cho thấy một minh họa về cách mà một hoặc nhiều IGP cũng như iBGP có thể hoạt động trong một hệ thống tự trị và cách các phiên eBGP kết nối các hệ thống tự trị khác nhau với nhau.

![](../image/Pasted%20image%2020241008104728.png)

### **2. Distance vector Algorithsm**

Các giao thức định tuyến vector khoảng cách, như RIP, quảng bá các tuyến đường dưới dạng vector, trong đó khoảng cách là một chỉ số (hoặc chi phí) như số bước nhảy (hop count), và vector là địa chỉ IP của bộ định tuyến tiếp theo được sử dụng để đạt được đích:

- **Distance**: Đây là chỉ số định tuyến để đến mạng.
- **Vector**: Vector là giao diện hoặc hướng để đến mạng.

Khi một Router nhận thông tin định tuyến - route từ một láng giềng, nó sẽ lưu trữ thông tin này vào cơ sở dữ liệu định tuyến cục bộ ngay khi nhận được. Thuật toán vector khoảng cách (như các thuật toán Bellman-Ford và Ford-Fulkerson) được sử dụng để xác định các đường đi tốt nhất, không có vòng lặp, tới mỗi đích có thể truy cập. Khi các đường đi tốt nhất được xác định, chúng sẽ được cài đặt vào bảng định tuyến và được quảng bá tới mỗi Router láng giềng.

Các Router chạy các giao thức vector khoảng cách quảng bá thông tin định tuyến tới các láng giềng từ góc nhìn của chính nó, đã được điều chỉnh từ tuyến đường gốc nhận được. Do đó, <font color="#ff0000">một giao thức vector khoảng cách không có bản đồ đầy đủ về toàn bộ mạng; thay vào đó, cơ sở dữ liệu của nó phản ánh rằng một Router láng giềng biết cách để đến mạng đích và khoảng cách mà Router láng giềng này ở xa mạng đích.</font> Lợi thế của các giao thức vector khoảng cách là chúng yêu cầu ít CPU và bộ nhớ hơn và có thể hoạt động trên các bộ định tuyến cấu hình thấp.

Một phép ẩn dụ thường được sử dụng để mô tả các giao thức vector khoảng cách là biển chỉ đường tại một ngã tư, chỉ ra rằng đích ở cách đó 2 dặm về phía tây; các tài xế tin tưởng và mù quáng đi theo thông tin này, mà không thực sự biết liệu có một con đường ngắn hơn hoặc tốt hơn đến đích hay không, hoặc liệu biển báo đó có chính xác hay không. Hình 6-2 minh họa cách một bộ định tuyến sử dụng giao thức vector khoảng cách nhìn nhận mạng và hướng mà R3 cần đi để đến mạng con 192.168.1.0/24.

![](../image/Pasted%20image%2020241008104956.png)

<u>Giao thức vector khoảng cách chọn đường đi chỉ dựa trên khoảng cách, mà không xem xét tốc độ liên kết hoặc các yếu tố khác.</u> Trong Hình 6-2, liên kết giữa R1 và R7 là một liên kết nối tiếp với băng thông chỉ 64 Kbps, trong khi tất cả các liên kết khác đều là các liên kết Ethernet 1 Gbps. <font color="#ff0000">RIP không xem xét điều này và chuyển tiếp lưu lượng qua liên kết này, điều này có thể dẫn đến mất gói khi liên kết đó bị quá tải.</font>

### **3. Enhanced Distance Vector Algorithsm**

Thuật toán cập nhật khuếch tán (Diffusing Update Algorithm - DUAL) là một thuật toán vector khoảng cách nâng cao mà EIGRP sử dụng để tính toán đường đi ngắn nhất đến một đích trong mạng. EIGRP quảng bá thông tin mạng tới các láng giềng như các giao thức vector khoảng cách khác, nhưng nó có một số cải tiến, như tên gọi của nó đã gợi ý. Dưới đây là một số cải tiến được giới thiệu trong thuật toán này so với các thuật toán vector khoảng cách khác:

- **Thời gian hội tụ nhanh** cho các thay đổi trong cấu trúc mạng.
- **Chỉ gửi cập nhật** khi có thay đổi trong mạng. Nó không gửi cập nhật bảng định tuyến đầy đủ theo định kỳ, như các giao thức vector khoảng cách làm.
- **Sử dụng gói hello** và thiết lập mối quan hệ láng giềng giống như các giao thức trạng thái liên kết.
- **Sử dụng băng thông, độ trễ, độ tin cậy, tải, và kích thước đơn vị truyền tối đa (MTU- Maximum transmission unit)** thay vì số bước nhảy (hop count) cho các phép tính đường đi.
- **Có tùy chọn cân bằng tải** lưu lượng qua các đường đi có chi phí bằng nhau hoặc khác nhau.

<font color="#ff0000">EIGRP đôi khi được gọi là một giao thức định tuyến hybrid vì nó có đặc điểm của cả giao thức vector khoảng cách và giao thức trạng thái liên kết</font>, như đã nêu trong danh sách trên. EIGRP dựa vào các chỉ số tiên tiến hơn ngoài số bước nhảy (ví dụ: băng thông) cho các phép tính đường đi tốt nhất. Mặc định, EIGRP quảng bá độ trễ tổng cộng của đường đi và băng thông tối thiểu cho một tuyến đường. Thông tin này được quảng bá ra mọi hướng, giống như một giao thức định tuyến vector khoảng cách; tuy nhiên, mỗi bộ định tuyến có thể tính toán đường đi tốt nhất dựa trên thông tin được cung cấp bởi các láng giềng trực tiếp của nó.

Hình 6-3 cho thấy cấu trúc mạng trước đó nhưng giờ đây bao gồm các phép tính chỉ số của EIGRP cho từng liên kết. R3 đang cố gắng chuyển tiếp gói tin đến mạng 192.168.1.0/24. Nếu miền định tuyến sử dụng một giao thức định tuyến vector khoảng cách, nó sẽ chọn đường đi R3→R1→R7, chỉ có hai bước nhảy, thay vì đường đi R3→R1→R2→R7, có ba bước nhảy. Tuy nhiên, đường đi R3→R1→R7 không thể hỗ trợ lưu lượng qua 64 kbps. Trong khi đường đi R3→R1→R2→R7 dài hơn, nó cung cấp nhiều băng thông hơn và không có độ trễ nhiều (do quá trình tuần tự trên các giao diện tốc độ thấp hơn) và là đường đi được EIGRP chọn.**

![](../image/Pasted%20image%2020241008110316.png)

### **4. Link-State Algorithsm**

Một giao thức định tuyến IP động trạng thái liên kết quảng bá trạng thái liên kết và chỉ số liên kết cho từng liên kết gắn kết của nó và các bộ định tuyến được kết nối trực tiếp tới mọi bộ định tuyến trong mạng. **OSPF và IS-IS** là hai giao thức định tuyến trạng thái liên kết thường được sử dụng trong các mạng doanh nghiệp và nhà cung cấp dịch vụ.<font color="#00b050"> Các quảng bá của OSPF được gọi là quảng bá trạng thái liên kết (Link-State Advertisements - LSAs)</font>, trong khi<font color="#e36c09"> IS-IS sử dụng các gói trạng thái liên kết (Link-State Packets - LSPs) cho các quảng bá của mình.</font>

Khi một bộ định tuyến nhận quảng bá từ một láng giềng, nó sẽ lưu trữ thông tin này trong một cơ sở dữ liệu cục bộ gọi là<u> cơ sở dữ liệu trạng thái liên kết (Link-State Database - LSDB) </u>và quảng bá thông tin trạng thái liên kết đến từng bộ định tuyến láng giềng của nó chính xác như nó đã nhận được. Thông tin trạng thái liên kết sẽ được phát tán khắp mạng, không thay đổi, từ bộ định tuyến này sang bộ định tuyến khác, giống như cách mà bộ định tuyến gốc đã quảng bá. Điều này cho phép tất cả các bộ định tuyến trong mạng có một bản đồ đồng bộ và giống hệt nhau của mạng.

Sử dụng bản đồ hoàn chỉnh của mạng, mỗi bộ định tuyến trong mạng sẽ chạy <font color="#ff0000">thuật toán Dijkstra (thuật toán đường đi ngắn nhất đầu tiên - Shortest Path First - SPF) </font>để tính toán các đường đi tốt nhất không có vòng lặp. Thuật toán trạng thái liên kết sau đó sẽ cập nhật bảng định tuyến với thông tin này.

Do có bản đồ hoàn chỉnh của mạng, các giao thức trạng thái liên kết thường yêu cầu nhiều CPU và bộ nhớ hơn so với các giao thức vector khoảng cách, nhưng chúng ít bị mắc phải vòng lặp định tuyến hơn và đưa ra các quyết định đường đi tốt hơn. Thêm vào đó, c*ác giao thức trạng thái liên kết được trang bị các khả năng mở rộng như LSA mờ cho OSPF và TLV (type/length/value) cho IS-IS, cho phép chúng hỗ trợ các tính năng thường được sử dụng bởi các nhà cung cấp dịch vụ, chẳng hạn như kỹ thuật lưu lượng **MPLS**.*

Một phép ẩn dụ cho các giao thức trạng thái liên kết là hệ thống điều hướng GPS. Hệ thống điều hướng GPS có một bản đồ hoàn chỉnh và có thể đưa ra quyết định tốt nhất về con đường nào là ngắn nhất và tốt nhất để đến đích. Hình 6-4 minh họa cách mà R3 sẽ nhìn nhận mạng để đến mạng con 192.168.1.0/24. R1 sẽ sử dụng cùng một thuật toán như R3 và đi theo liên kết trực tiếp đến R4.

![](../image/Pasted%20image%2020241008110544.png)

### **4. Path Vector Algorithsm**

Một giao thức vector đường đi như BGP tương tự như một giao thức vector khoảng cách; sự khác biệt là thay vì chỉ nhìn vào khoảng cách để xác định đường đi tốt nhất không có vòng lặp, nó <u>xem xét các thuộc tính đường đi BGP khác nhau</u>. <span style="background:#fff88f">Các thuộc tính đường đi BGP bao gồm đường đi qua hệ thống tự trị (AS_Path), bộ phân loại đa điểm (MED), nguồn gốc, bộ định tuyến tiếp theo, ưu tiên cục bộ, tổng hợp nguyên tử, và người tổng hợp. </span>Các thuộc tính đường đi BGP sẽ được đề cập trong Chương 11, “BGP,” và Chương 12, “BGP Nâng Cao.”

Một giao thức vector đường đi **đảm bảo các đường đi không có vòng lặp bằng cách giữ một bản ghi về mỗi hệ thống tự trị mà quảng bá định tuyến đi qua**. Mỗi khi một bộ định tuyến nhận quảng bá mà nó đã có mặt trong AS_Path, quảng bá đó sẽ bị từ chối vì việc chấp nhận AS_Path sẽ dẫn đến một vòng lặp định tuyến.

Hình 6-5 minh họa khái niệm ngăn chặn vòng lặp qua các bước sau:

1. R1 (AS 1) quảng bá mạng 10.1.1.0/24 đến R2 (AS 2). R1 thêm AS 1 vào AS_Path trong quảng bá mạng đến R2.
2. R2 quảng bá mạng 10.1.1.0/24 đến R4 và thêm AS 2 vào AS_Path trong quảng bá mạng đến R4.
3. R4 quảng bá mạng 10.1.1.0/24 đến R3 và thêm AS 4 vào AS_Path trong quảng bá mạng đến R3.
4. R3 quảng bá mạng 10.1.1.0/24 trở lại R1 và R2 sau khi thêm AS 3 vào AS_Path trong quảng bá mạng.
5. Khi R1 nhận quảng bá mạng 10.1.1.0/24 từ R3, nó sẽ loại bỏ quảng bá đường đi vì R1 phát hiện AS của nó (AS 1) trong AS_Path “3 4 2 1” và coi quảng bá này là một vòng lặp. Tương tự, R2 cũng loại bỏ quảng bá mạng 10.1.1.0/24 từ R3 vì nó phát hiện AS của mình (AS 2) trong AS_Path “3 4 2 1” và cũng coi đây là một vòng lặp.

![](../image/Pasted%20image%2020241008112630.png)

<u>LƯU Ý </u>Bản vẽ không mô tả quảng cáo của mạng 10.1.1.0/24 đến R3 để dễ hình dung hơn, nhưng quá trình này cũng diễn ra theo hướng ngược lại. R3 cũng cố gắng quảng cáo mạng 10.1.1.0/24 đến R2. R2 loại bỏ tuyến đường vì R1 phát hiện AS (AS 2) của nó trong AS_Path “3 4 2 1” và cũng coi đó là một vòng lặp—mặc dù nó không phải là nguồn tuyến đường ban đầu.

### **5. Path Selection**

Bộ định tuyến xác định đường đi mà một gói tin phải đi qua bằng cách đánh giá độ dài tiền tố được lập trình trong <span style="background:#fff88f">Forward Information Base (FIB)</span>. FIB được lập trình thông qua bảng định tuyến, còn được gọi là Cơ sở thông tin định tuyến (RIB). RIB bao gồm các tuyến đường được trình bày từ các quy trình giao thức định tuyến. Lựa chọn đường đi có ba thành phần chính:

- **Prefix length**: Độ dài tiền tố biểu thị số bit nhị phân hàng đầu trong mặt nạ mạng con ở vị trí bật.

- **Aministrative distance**: Khoảng cách hành chính (AD) là đánh giá độ tin cậy của nguồn thông tin định tuyến. Nếu một bộ định tuyến tìm hiểu về một tuyến đường đến đích từ nhiều hơn một giao thức định tuyến và tất cả các tuyến đường có cùng độ dài tiền tố, thì AD sẽ được so sánh.

- **Metrics**: là đơn vị đo lường được giao thức định tuyến sử dụng trong tính toán đường dẫn tốt nhất. Metrics thay đổi tùy theo từng giao thức định tuyến.

##### **Prefix Length**

Xét một bộ định tuyến có các tuyến sau với nhiều độ dài tiền tố khác nhau trong Route Table:
- 10.0.3.0/28
- 10.0.3.0/26
- 10.0.3.0/24

_ Mỗi tuyến đường này, được gọi là **prefix routes** hoặc đơn giản là **prefixes** có prefix length khác nhau (tức mặt nạ mạng con khác nhau).
_ Các Routes được coi là các đích đến khác nhau và tất cả được cài đặt vào RIB - Bảng định tuyến.

![](../image/Pasted%20image%2020241008135657.png)

1. Nếu packet cần được forwards đến 10.0.3.14, thiết bị Router sẽ kết nối tất cả 3 routes vì nó phù hợp với độ dài của cả 3 địa chỉ IP address. Nhưng gói tin được chuyển tiếp next-hop 10.1.1.1 với interface đi ra ngoài Gigabit Ethernet 1/1 vì 10.0.3.0/28 có tiền tố khớp dài nhất.
2. Nếu một gói tin cần được chuyển tiếp đến 10.0.3.42, bộ định tuyến sẽ khớp với các tiền tố 10.0.3.0/24 và 10.0.3.0/26. Nhưng gói tin được chuyển tiếp đến 10.2.2.2 với giao diện đầu ra Gigabit Ethernet 2/2 vì 10.0.3.0/26 có tiền tố khớp dài nhất.
3. Nếu một gói tin cần được chuyển tiếp đến 10.0.3.100, bộ định tuyến chỉ khớp với tiền tố 10.0.3.0/24. Gói tin được chuyển tiếp đến 10.3.3.3 với giao diện đầu ra Gigabit Ethernet 3/3.

Quyết định chuyển tiếp là một chức năng của FIB và là kết quả từ các phép tính được thực hiện trong RIB. RIB được tính toán thông qua sự kết hợp của các số liệu giao thức định tuyến và Administrative Distance.

##### **Administrative Distance**

Khi mỗi giao thức định tuyến nhận được các bản cập nhật định tuyến và thông tin định tuyến khác, nó sẽ chọn đường dẫn tốt nhất đến bất kỳ đích nào và cố gắng cài đặt đường dẫn này vào bảng định tuyến. Bảng dưới cung cấp các AD mặc định cho nhiều giao thức định tuyến khác nhau.

| Routing Protocol    | Default Administrative Distance |
| ------------------- | ------------------------------- |
| Connected           | 0                               |
| Static              | 1                               |
| EIRGP summary route | 5                               |
| External BGP (eBGP) | 20                              |
| EIRGP (internal)    | 90                              |
| OSPF                | 110                             |
| IS-IS               | 115                             |
| RIP                 | 120                             |
| EIRGP (external)    | 170                             |
| Internal BGP (iBGP) | 200                             |
Bảng định tuyến - RIB được lập trình từ các quy trình giao thức định tuyến khác nhau. <span style="background:#fff88f">Mỗi giao thức định tuyến đều trình bày cùng một thông tin cho RIB để chèn: mạng đích, địa chỉ IP hop tiếp theo, AD và giá trị số liệu</span>. RIB chấp nhận hoặc từ chối một tuyến đường dựa trên logic sau:

- Nếu <u>tuyến đường không tồn tại trong RIB</u>, tuyến đường đó sẽ được chấp nhận.

- Nếu <u>tuyến đường tồn tại trong RIB, AD phải được so sánh</u>. Nếu AD của tuyến đường đã có trong RIB thấp hơn quy trình gửi tuyến đường thứ hai, tuyến đường sẽ bị từ chối. Sau đó, quy trình định tuyến đó sẽ được thông báo.

- Nếu tuyến đường tồn tại trong RIB, AD phải được so sánh. Nếu <u>AD của tuyến đường đã có trong RIB cao hơn quy trình định tuyến gửi mục thay thế</u>, tuyến đường được chấp nhận và giao thức nguồn hiện tại được thông báo về việc xóa mục khỏi RIB.

Ví dụ: nếu tuyến EIGRP 10.3.3.0/24 được cài đặt trong bảng định tuyến không thành công vì một số lý do, quy trình bảng định tuyến sẽ gọi OSPF và IS-IS và yêu cầu chúng cài đặt lại
tuyến trong bảng định tuyến. Trong hai giao thức này, tuyến được ưu tiên sẽ được chọn dựa trên AD, sẽ là OSPF vì AD của nó thấp hơn.

Việc hiểu thứ tự xử lý từ bộ định tuyến là rất quan trọng vì trong một số trường hợp,
đường dẫn có AD thấp nhất không phải lúc nào cũng được cài đặt trong RIB. Ví dụ, quy trình chọn đường dẫn của BGP có thể chọn đường dẫn iBGP thay vì đường dẫn eBGP. Do đó, BGP sẽ trình bày đường dẫn có AD là 200, không phải 20, cho RIB, có thể không chiếm quyền ưu tiên cho tuyến được học qua OSPF có AD là 110. Những tình huống này hầu như không bao giờ xảy ra; nhưng hãy nhớ rằng đó là tuyến tốt nhất từ ​​giao thức định tuyến được trình bày cho RIB khi AD sau đó được so sánh.

<u>LƯU Ý</u>: AD có thể được cấu hình và điều chỉnh trong mạng, tùy mục đích ưu tiên xử lý.

##### **Metrics**

Logic để chọn đường dẫn tốt nhất cho một giao thức định tuyến có thể khác nhau. Hầu hết các IGP thích các tuyến đường được học nội bộ hơn các tuyến đường bên ngoài và ưu tiên hơn nữa đường dẫn có số liệu thấp nhất.

***Equal-Cost Multipathing***

Nếu một giao thức định tuyến xác định nhiều đường dẫn là đường dẫn tốt nhất và hỗ trợ nhiều mục đường dẫn, bộ định tuyến sẽ cài đặt số lượng đường dẫn tối đa được phép cho mỗi đích. Điều này được gọi là <span style="background:#fff88f">equal-cost multipathing (ECMP)</span> và cung cấp chia sẻ tải trên tất cả các liên kết. RIP, EIGRP, OSPF và IS-IS đều hỗ trợ ECMP. ECMP cung cấp cơ chế để tăng băng thông trên nhiều đường dẫn bằng cách chia đều lưu lượng trên các liên kết.

Hình 6-6 minh họa bốn bộ định tuyến chạy OSPF.

![](../image/Pasted%20image%2020241009084737.png)

Cả bốn bộ định tuyến đều thuộc cùng một khu vực và sử dụng cùng chi phí số liệu giao diện.

R1 có hai đường dẫn có chi phí bằng nhau để đến mạng 10.3.3.0/24 của R3.

R1 cài đặt cả hai tuyến đường trong bảng định tuyến và chuyển tiếp lưu lượng qua đường dẫn R1–R2–R3 và R1–R4–R3 để đến mạng 10.3.3.0/24.

***Unequal-Cost Load Balancing***

Theo mặc định, các giao thức định tuyến chỉ cài đặt các tuyến có số liệu đường dẫn thấp nhất. Tuy nhiên, EIGRP có thể được cấu hình (không được bật theo mặc định) để cài đặt nhiều tuyến có số liệu đường dẫn khác nhau. Điều này cho phép **Unequal-Cost Load Balancing** trên nhiều đường dẫn. Lưu lượng được truyền ra các giao diện của bộ định tuyến dựa trên số liệu đường dẫn đó theo tỷ lệ với các số liệu giao diện khác.

Hình 6-7 cho thấy một cấu trúc mạng với bốn bộ định tuyến chạy EIGRP. Độ trễ đã được
tăng trên giao diện Gi0/2 của R1 từ 1 μ lên 10 μ. R1 nhìn thấy hai đường dẫn có số liệu khác nhau. Đường dẫn từ R1 đến R3 qua R1–R2–R3 đã được gán số liệu đường dẫn là 3328,
và đường dẫn qua R1–R4–R3 đã được gán số liệu đường dẫn là 5632

![](../image/Pasted%20image%2020241009091018.png)

### **5. Static Routing**

#### Static Route Types



### **6. Virtual Routing and Forwarding**


















