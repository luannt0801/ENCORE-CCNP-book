Chương này bao gồm các chủ đề sau:

- **VLAN Trunking Protocol (VTP)**: Phần này cung cấp tổng quan về cách các bộ chuyển mạch nhận biết các bộ chuyển mạch khác và ngăn chặn vòng lặp chuyển tiếp.

- **Dynamic Trunking Protocol (DTP)**: Phần này xem xét những cải tiến được thực hiện đối với STP để hội tụ nhanh hơn.

- **EtherChamnel Bundle:** Phần này giải thích cách kết hợp nhiều giao diện vật lý để tạo thành một giao diện logic nhằm tăng thông lượng và cung cấp khả năng phục hồi liền mạch.

Chương này đề cập đến nhiều <font color="#e36c09">tính năng cho kết nối switch-to-switch</font>. Chương này bắt đầu bằng cách giải thích Giao thức Trunking VLAN (VTP) và Giao thức Trunking động (DTP) để hỗ trợ cung cấp VLAN và đảm bảo rằng <font color="#e36c09">kết nối switch-to-switch có thể mang nhiều VLAN</font>. Cuối cùng, chương này giải thích cách sử dụng các bó <font color="#e36c09">EtherChannel</font> như một phương pháp để thêm băng thông và ngăn chặn các thay đổi về cấu trúc mạng do lỗi liên kết.

### **1. VLAN Trunking Protocol**

Trước khi API khả dụng trên nền tảng Cisco, việc cấu hình một bộ chuyển mạch là một quy trình thủ công. Cisco đã tạo ra giao thức độc quyền, Giao thức truyền VLAN (VTP), để <font color="#ff0000">giảm gánh nặng cung cấp VLAN trên các bộ chuyển mạch</font>. Việc thêm một VLAN có vẻ như là một nhiệm vụ đơn giản, nhưng trong <u>môi trường có 100 bộ chuyển mạch, việc thêm một VLAN yêu cầu phải đăng nhập vào 100 bộ chuyển mạch để cung cấp một VLAN</u>. Nhờ VTP, các bộ chuyển mạch tham gia vào cùng một miền VTP có thể có một VLAN được tạo một lần trên máy chủ VTP và được truyền đến các bộ chuyển mạch máy khách VTP khác trong cùng một miền VTP.

Có bốn vai trò trong kiến ​​trúc VTP:

- **Server**: Bộ chuyển mạch máy chủ chịu trách nhiệm tạo, sửa đổi và xóa VLAN trong miền VTP.

- **Client**: Bộ chuyển mạch máy khách nhận được quảng cáo VTP và sửa đổi VLAN trên bộ chuyển mạch đó. Không thể cấu hình VLAN cục bộ trên máy khách VTP.

- **Transparent**: Các công tắc trong suốt VTP nhận và chuyển tiếp các quảng cáo VTP nhưng không sửa đổi cơ sở dữ liệu VLAN cục bộ. VLAN chỉ được cấu hình cục bộ.

- **Off**: Một bộ chuyển mạch không tham gia vào quảng cáo VTP và cũng không chuyển tiếp chúng ra khỏi bất kỳ cổng nào. VLAN chỉ được cấu hình cục bộ.

Hình 5-1 cho thấy một cấu trúc đơn giản trong đó SW1 là máy chủ VTP và SW2, SW4, SW5 và SW6 là máy khách VTP. SW3 ở chế độ trong suốt và không cập nhật cơ sở dữ liệu VLAN của nó khi các thay đổi được truyền qua miền VTP. SW3 chuyển tiếp các thay đổi VTP đến SW6.

![](../image/Pasted%20image%2020241008093703.png)


Có ba phiên bản VTP và Phiên bản 1 là phiên bản mặc định.

1. Ở mức đơn giản nhất, VTP Phiên bản 1 và 2 giới hạn việc truyền bá đến các VLAN được đánh số từ 1 đến 1005.

2. VTP Phiên bản 3 cho phép truyền bá toàn bộ các VLAN từ 1 đến 4094. Tại thời điểm viết bài này, hầu hết các thiết bị chuyển mạch đều có khả năng chạy VTP Phiên bản 3.

VTP hỗ trợ việc có nhiều máy chủ VTP trong một miền. Các máy chủ này xử lý các bản cập nhật từ các máy chủ VTP khác giống như máy khách. Nếu miền VTP là Phiên bản 3, máy chủ VTP chính phải được thiết lập bằng lệnh điều hành **vtp primary**.


### **2. Dynamic Trunking Protocol**

Trunk Switch ở chương 1 là kết nối 1 switch tới 1 thiết bị khác (switch, firewall, ...) trong khi mang nhiều VLAN qua chúng.
=> Cisco cung cấp một cơ chế để các cổng switch tạo thành một cổng trunk động - Dynamic Trunk

Dynamic Trunk được established bằng cách:
1. Switch gửi các packet của giao thức Dynamic Trunk Protocol (DTP) để thương lượng xem đầu kia có thể là cổng trunk hay không.
2. Nếu cả 2 cổng có thể thương lượng thành công 1 thỏa thuận, cổng sẽ trở thành cổng chuyển mạch trunk.

DTP tự broadcast sau mỗi 30s cho các láng giềng để họ biết trạng thái của nó
DTP yêu cầu miền VTP phải khớp giữa 2 công tác

###### 3 chế độ sử dụng khi thiết lập port switch thành trunk:

- **Trunk:** Chế độ này đặt tín hiệu cổng chuyển mạch như một trunk và broadcast các gói DTP đến đầu kia để thiết lập mộ trunk động. Đặt một port switch trong chế độ này bằng lệnh `switch port mode trunk`
- **Dynamic desired:** Cổng chuyển mạch hoạt động như một cổng truy cập, nhưng nó lắng nghe các gói và broadcast các gói DTP đến đầu kia để thiết lập một trunk động. Nếu đàm phán thành công, cổng sẽ trở thành một trunk port Đặt một cổng chuyển mạch ở chế độ này bằng lệnh `switch port mode dynamic desired`
- **Dynamic auto:** Cổng switch hoạt động như một cổng truy cập nhưng nó lắng nghe các gói DTP. Nó phản hồi các gói DTP và sau khi đàm phán thành công, cổng trở thành cổng trunk. Đặt một cổng chuyển mạch ở chế độ này bằng lệnh `switch port mode dynamic auto`

Một liên kết trunk có thể hình thành thành công trong hầu hết mọi sự kết hợp của các chế độ này trừ khi cả hai đầu được cấu hình là tự động.

![](../image/Pasted%20image%2020241008095112.png)

### **3. EtherChannel Bundle**

Tốc độ mạng Ethernet dựa trên lũy thừa của 10 (10 Mbps, 100 Mbps, 1 Gbps, 10 Gbps,
100 Gbps). Khi một liên kết giữa các bộ chuyển mạch bị bão hòa, làm thế nào để thêm băng thông vào liên kết đó để ngăn ngừa mất gói tin?

Nếu cả hai công tắc đều có cổng khả dụng với thông lượng nhanh hơn liên kết hiện tại (ví dụ:

ví dụ, 10 Gbps so với 1 Gbps), sau đó thay đổi liên kết sang giao diện tốc độ cao hơn sẽ giải quyết vấn đề dự phòng băng thông. Tuy nhiên, trong hầu hết các trường hợp, điều này là không khả thi.

Lý tưởng nhất là cắm thêm một cáp thứ hai và tăng gấp đôi băng thông giữa các công tắc. Tuy nhiên, Spanning Tree Protocol (STP) sẽ đặt một trong các cổng vào trạng thái chặn để ngăn chặn các vòng lặp chuyển tiếp.

![](../image/Pasted%20image%2020241008095631.png)


Các liên kết vật lý có thể được tổng hợp thành một liên kết logic được gọi là một bó EtherChannel.

Thuật ngữ trong ngành cho một bó EtherChannel là EtherChannel (gọi tắt), hoặc kênh cổng, được định nghĩa trong thông số kỹ thuật tổng hợp liên kết IEEE 802.3AD.

Các giao diện vật lý được sử dụng để lắp ráp EtherChannel logic được gọi là giao diện thành viên. STP hoạt động trên một liên kết logic chứ không phải trên một liên kết vật lý. Sau đó, liên kết logic sẽ có băng thông của bất kỳ giao diện thành viên đang hoạt động nào và nó sẽ được cân bằng tải trên tất cả các liên kết.

EtherChannels có thể được sử dụng để chuyển tiếp Lớp 2 (truy cập hoặc đường trục) hoặc Lớp 3 (định tuyến).

<span style="background:#fff88f">LƯU Ý</span> *Các thuật ngữ EtherChannel, EtherChannel bundle và kênh cổng thường được sử dụng thay thế cho nhau trên nền tảng Catalyst, nhưng các nền tảng Cisco khác chỉ sử dụng thuật ngữ kênh cổng - port channel một cách độc quyền.*


![](../image/Pasted%20image%2020241008102647.png)



### **4. Dynamic Link Aggregation Protocols**

