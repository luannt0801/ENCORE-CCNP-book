<u>Nội dung chính giao thức cây mở rộng - Spanning Tree Protocol - STP:</u>
- Nguyên tắc cơ bản: Tổng quan về cách các switches nhận biết các switches khác và ngăn chặn vòng lặp chuyển tiếp
- Rapid Spanning Tree Protocol - Giao thức cây mở rộng nhanh: Phần này xem xét những cải tiến được thực hiện đối với STP để hội tụ nhanh hơn

## **SPANNING TREE PROTOLCOL**

### **1. Cơ bản về Spanning Tree Protocol**

Giao thức Spanning Tree (STP) cho phép các switch nhận biết các switch khác thông qua việc quảng cáo và nhận các đơn vị dữ liệu giao thức cầu nối (BPDU). STP xây dựng một cấu trúc không có vòng lặp Lớp 2 trong một môi trường bằng cách tạm thời chặn lưu lượng trên các cổng dự phòng. STP hoạt động bằng cách chọn một switch cụ thể làm switch chính và chạy thuật toán dựa trên cây để xác định các cổng dự phòng nào không nên chuyển tiếp lưu lượng.

STP có nhiều lần lặp lại:
- 802.1D, là thông số kỹ thuật ban đầu
- Cây mở rộng theo VLAN (PVST) | Per-VLAN Spanning Tree Protocol
- Cây kéo dài trên mỗi VLAN Plus (PVST+) | Per-VLAN Spanning Tree Protocol Plus
- Giao thức cây mở rộng nhanh 802.1W (RSTP) | 802.1W Rapid Spanning Tree Protocol
- Giao thức cây mở rộng đa 802.1S (MST) | 802.1S Multiple Spanning Tree Protocol
Các công tắc Catalyst hiện hoạt động ở chế độ PVST+, RSTP và MST. Cả ba chế độ này đều tương thích ngược với 802.1D.

| Từ                         | Viết tắt | Nghĩa                            |
| -------------------------- | -------- | -------------------------------- |
| bridge protocol data units | BPDU     | Đơn vị dữ liệu giao thức cầu nối |
|                            |          |                                  |

###  **2.  Tiêu chuẩn 802.1D Port States - Các trạng thái của Port 

Trong 802.1D STP, mỗi cổng chuyển đổi qua các trạng thái:
- **Disable**: Cổng đang ở vị trí Disable về mặt hành chính (tức là đã Disable).
- **Blocking**: Cổng chuyển mạch được bật, nhưng <font color="#00b050">cổng không chuyển tiếp bất kỳ lưu lượng nào</font> để đảm bảo rằng vòng lặp không được tạo. Chuyển mạch không sửa đổi bảng địa chỉ MAC. Nó chỉ có thể nhận BPDU từ các chuyển mạch khác.
- **Listening**: Cổng chuyển mạch đã <font color="#00b050">chuyển từ trạng thái chặn và giờ có thể gửi hoặc nhận BPDU</font>. Nó không thể chuyển tiếp bất kỳ lưu lượng mạng nào khác. Thời lượng của trạng thái tương quan với thời gian chuyển tiếp STP. Trạng thái cổng tiếp theo là học.
- **Learning**: Cổng chuyển mạch hiện có thể sửa đổi bảng địa chỉ MAC với bất kỳ lưu lượng mạng nào mà nó nhận được. Switch vẫn không chuyển tiếp bất kỳ lưu lượng mạng nào khác ngoài BPDU. Thời lượng của trạng thái tương quan với thời gian chuyển tiếp STP. Trạng thái cổng tiếp theo là chuyển tiếp.
- **Forwarding**: Cổng chuyển mạch có thể chuyển tiếp tất cả lưu lượng mạng và có thể cập nhật bảng địa chỉ MAC như mong đợi. Đây là trạng thái cuối cùng để cổng chuyển mạch chuyển tiếp lưu lượng mạng.
- **Broken**: Bộ chuyển mạch đã phát hiện ra cấu hình hoặc sự cố hoạt động trên cổng có thể gây ra hậu quả nghiêm trọng. Cổng sẽ loại bỏ các gói tin miễn là sự cố vẫn tiếp tục tồn tại.

###  **3.  Tiêu chuẩn 802.1D STP định nghĩa ba loại cổng sau**
- **Cổng gốc - Root Port (RP)**: Một cổng mạng kết nối với cầu gốc hoặc một công tắc thượng nguồn trong cấu trúc cây mở rộng. Chỉ nên có một cổng gốc cho mỗi VLAN trên một công tắc.
- C**ổng được chỉ định - Designated Port (DP)**: Một cổng mạng nhận và chuyển tiếp các khung BPDU đến các bộ chuyển mạch khác. Các cổng được chỉ định cung cấp khả năng kết nối đến các thiết bị hạ lưu và các bộ chuyển mạch. Chỉ nên có một cổng được chỉ định đang hoạt động trên một liên kết.
- **Cổng chặn - Blocking Port:** Mạng không chuyển tiếp lưu lượng do tính toán STP.

BPDU header:
![](../image/Pasted%20image%2020241007163418.png)

###  **4. Thuật ngữ chính của STP**

Một số thuật ngữ chính liên quan đến STP:
- **<span style="background:#fff88f">Root bridge</span>**: Root bridge là switch quan trọng nhất trong cấu trúc Layer 2. Tất cả các cổng đều ở trạng thái forwarding. Switch này được coi là <font color="#ff0000">đỉnh của cây mở rộng</font> cho tất cả các phép tính đường dẫn của các switch khác. Tất cả các cổng trên root bridge được phân loại là các cổng được chỉ định.
- **<span style="background:#fff88f">Đơn vị dữ liệu giao thức cầu nối - Brigde protocol data unit (BPDU):</span>** Gói mạng này được sử dụng cho các bộ chuyển mạch mạng để<font color="#ff0000"> xác định một hệ thống phân cấp và thông báo về những thay đổi trong cấu trúc mạng</font>. BPDU sử dụng địa chỉ MAC đích 01:80:c2:00:00:00. Có hai loại BPDU:
  1. **Configuration BPDU:** Loại BPDU này được sử dụng để <font color="#e36c09">xác định cầu gốc, cổng gốc, cổng được chỉ định và cổng chặn</font>. BPDU cấu hình bao gồm các trường sau: Loại STP, chi phí đường dẫn gốc, mã định danh cầu gốc, mã định danh cầu cục bộ, tuổi tối đa, thời gian chào và độ trễ chuyển tiếp.
  2. **Topology change nofitication (TCN) BPDU:** Loại BPDU này được sử dụng để truyền đạt những thay đổi trong cấu trúc Lớp 2 đến các thiết bị chuyển mạch khác. Điều này được giải thích chi tiết hơn ở phần sau của chương.
- **<span style="background:#fff88f">Root path cost</span>:** Đây là chi phí kết hợp cho một đường dẫn cụ thể tới công tắc gốc.
- **System priority**: Giá trị 4 bit này biểu thị mức độ ưu tiên cho một switch là root brigde. Giá trị mặc định là 32.768.
- **System ID extension:** Giá trị 12 bit này chỉ ra VLAN mà BPDU tương quan. Mức độ ưu tiên hệ thống và phần mở rộng ID hệ thống được kết hợp như một phần trong quá trình nhận dạng cầu gốc của bộ chuyển mạch.
- **Root brigde identifier:** Đây là sự kết hợp của địa chỉ MAC hệ thống cầu gốc, phần mở rộng ID hệ thống và mức độ ưu tiên hệ thống của cầu gốc.
- **Local Brigde Identifier:** Đây là sự kết hợp giữa địa chỉ MAC hệ thống cầu nối của bộ chuyển mạch cục bộ, phần mở rộng ID hệ thống và mức độ ưu tiên hệ thống của cầu nối gốc.
- **Max Age**: Đây là khoảng thời gian tối đa trôi qua trước khi một cổng cầu lưu thông tin BPDU của nó. Giá trị mặc định là 20 giây, nhưng giá trị có thể được cấu hình bằng lệnh spanning-tree vlan vlan-id max-age maxage. Nếu một switch mất liên lạc với nguồn BPDU, nó sẽ cho rằng thông tin BPDU vẫn hợp lệ trong thời gian của bộ đếm thời gian Tuổi tối đa.
- **Hello time:** Đây là thời gian mà BPDU được quảng cáo ra khỏi một cổng. Giá trị mặc định là 2 giây, nhưng giá trị có thể được cấu hình từ 1 đến 10 giây bằng lệnh spanning-tree vlan vlan-id hello-time hello-time.
- **Forward delay:** Đây là khoảng thời gian mà một cổng duy trì trạng thái lắng nghe và học hỏi. Giá trị mặc định là 15 giây, nhưng giá trị có thể được thay đổi thành giá trị từ 15 đến 30 giây bằng lệnh spanning-tree vlan vlan-id forward-time forward-time.

*<span style="background:#fff88f">LƯU Ý:</span>  *STP được định nghĩa trước khi các thiết bị chuyển mạch hiện đại ra đời. Các thiết bị ban đầu sử dụng STP được gọi là cầu nối. Các thiết bị chuyển mạch thực hiện cùng một vai trò ở tốc độ và quy mô cao hơn trong khi về cơ bản là cầu nối lưu lượng Lớp 2. Các thuật ngữ cầu nối và chuyển mạch có thể hoán đổi cho nhau trong bối cảnh này.*

###  **5. Spanning Tree Path Cost**

- **Cách Tính Chi Phí**: Chi phí giao diện STP được xác định dựa trên tốc độ của liên kết. Mỗi tốc độ giao diện tương ứng với một giá trị chi phí nhất định. Chi phí này được tính cộng dồn khi đi qua các switch để xác định tổng chi phí đến root bridge.
    
- **Chi Phí 16-bit và 32-bit**:
    
    - **Chế Độ Ngắn (Short Mode)**: Sử dụng giá trị 16-bit, với giá trị tham chiếu là 20 Gbps. Điều này có nghĩa là các tốc độ liên kết thấp hơn 20 Gbps sẽ có chi phí cao hơn. Ví dụ, một liên kết 10 Mbps có chi phí là 100.
    - **Chế Độ Dài (Long Mode)**: Sử dụng giá trị 32-bit và tham chiếu tốc độ là 20 Tbps. Phương pháp này được áp dụng khi các switch hiện đại có tốc độ rất cao (hơn 20 Gbps), giúp tăng khả năng mở rộng cho mạng.

Bảng chi phí STP hiển thị mối quan hệ giữa tốc độ liên kết và chi phí tương ứng. Ví dụ:

- **Liên kết 1 Gbps** có chi phí 4 trong chế độ ngắn và 20,000 trong chế độ dài, cho thấy sự khác biệt rõ rệt giữa hai chế độ.
- **Liên kết 10 Tbps** có chi phí là 1 trong cả hai chế độ, cho thấy rằng ở tốc độ rất cao, chi phí không thay đổi.

| Tốc Độ Liên Kết | Chi Phí STP Chế Độ Ngắn | Chi Phí STP Chế Độ Dài |
| --------------- | ----------------------- | ---------------------- |
| 10 Mbps         | 100                     | 2,000,000              |
| 100 Mbps        | 19                      | 200,000                |
| 1 Gbps          | 4                       | 20,000                 |
| 10 Gbps         | 2                       | 2,000                  |
| 20 Gbps         | 1                       | 1,000                  |
| 100 Gbps        | 1                       | 200                    |
| 1 Tbps          | 1                       | 20                     |
| 10 Tbps         | 1                       | 2                      |
Các thiết bị có thể được cấu hình với chi phí giao diện chế độ dài bằng lệnh `spanning-tree pathcost method long`. Tất cả các topologies Layer 2 nên sử dụng cùng một cài đặt cho mọi thiết bị trong môi trường để đảm bảo tính nhất quán của topology. Trước khi bật cài đặt này trong một môi trường, điều quan trọng là thực hiện một cuộc kiểm tra để đảm bảo rằng cài đặt sẽ hoạt động.


##  **Rapid Spanning Tree Protocol**

### **1. Cơ bản về Rapid Spanning Tree Protocol**
Giao thức cây mở rộng nhanh

802.1D đã làm tốt việc ngăn chặn các vòng lặp chuyển tiếp Lớp 2, nhưng nó chỉ sử dụng một cây chính sách, điều này gây ra các vấn đề về khả năng mở rộng. Một số môi trường lớn hơn với nhiều VLAN cần các cấu trúc STP khác nhau cho mục đích kỹ thuật lưu lượng (ví dụ: cân bằng tải, điều hướng lưu lượng). Cisco đã tạo ra Per-VLAN Spanning Tree (PVST) và Per-VLAN Spanning Tree Plus (PVST+) để cho phép linh hoạt hơn.

PVST và PVST+ là các giao thức mở rộng độc quyền. Các khái niệm trong các giao thức này được kết hợp với các cải tiến khác để cung cấp khả năng hội tụ nhanh hơn vào thông số kỹ thuật IEEE 802.1W, được gọi là Giao thức cây mở rộng nhanh (RSTP).

### **2. Trạng thái cổng RSTP (802.1W)**

RSTP giảm số lượng trạng thái cổng xuống còn ba:
- **Discarding**: Cổng chuyển mạch được bật, nhưng cổng không chuyển tiếp bất kỳ lưu lượng nào để đảm bảo rằng vòng lặp không được tạo. Trạng thái này kết hợp các trạng thái STP truyền thống là vô hiệu hóa, chặn và lắng nghe.
- **Learning**: Cổng chuyển mạch sửa đổi bảng địa chỉ MAC với bất kỳ lưu lượng mạng nào mà nó nhận được. Chuyển mạch vẫn không chuyển tiếp bất kỳ lưu lượng mạng nào khác ngoài BPDU.
- **Forwarding**: Cổng chuyển tiếp chuyển tiếp tất cả lưu lượng mạng và cập nhật bảng địa chỉ MAC như mong đợi. Đây là trạng thái cuối cùng để cổng chuyển tiếp lưu lượng mạng.

<span style="background:#fff88f">LƯU Ý</span> *Một bộ chuyển mạch cố gắng thiết lập bắt tay RSTP với thiết bị được kết nối với đầu kia của cáp. Nếu không xảy ra bắt tay, thiết bị kia được coi là không tương thích với RSTP và cổng mặc định là hành vi 802.1D thông thường. Điều này có nghĩa là các thiết bị chủ như máy tính, máy in, v.v. vẫn gặp phải độ trễ truyền đáng kể (khoảng 30 giây) sau khi liên kết mạng được thiết lập.*

### **3. Vai trò cổng RSTP (802.1W)**

RSTP xác định các vai trò cổng sau:
- **Cổng gốc (RP)**: Một cổng mạng kết nối với root switch hoặc một upstream switch trong cấu trúc cây mở rộng. Chỉ nên có một root port cho mỗi VLAN trên một switch.
- **Cổng được chỉ định (DP)**: Một cổng mạng nhận và chuyển tiếp khung đến các bộ chuyển mạch khác. Các cổng được chỉ định cung cấp khả năng kết nối đến các thiết bị hạ lưu và bộ chuyển mạch. Chỉ nên có một cổng được chỉ định đang hoạt động trên một liên kết.
- **Cổng thay thế:** Cổng mạng cung cấp kết nối thay thế tới bộ chuyển mạch gốc thông qua một bộ chuyển mạch khác.
- **Cổng dự phòng:** Một cổng mạng cung cấp liên kết dự phòng cho công tắc gốc hiện tại. Cổng dự phòng không thể đảm bảo kết nối đến cầu gốc trong trường hợp công tắc thượng nguồn bị lỗi. Cổng dự phòng chỉ tồn tại khi nhiều liên kết kết nối giữa cùng một công tắc.

### **4. Các loại cổng RSTP (802.1W)**

RSTP định nghĩa ba loại cổng được sử dụng để xây dựng cấu trúc STP:
- **Cổng biên**: Một cổng ở biên của mạng nơi máy chủ kết nối với cấu trúc lớp 2 bằng một giao diện và không thể tạo thành một vòng lặp. Các cổng này tương quan trực tiếp với các cổng có tính năng STP portfast được bật.
- **Cổng gốc**: Cổng có chi phí đường dẫn tốt nhất đến cầu gốc. Chỉ có thể có một cổng gốc trên một bộ chuyển mạch.
- **Cổng điểm-điểm**: Bất kỳ cổng nào kết nối với một bộ chuyển mạch RSTP khác với chế độ song công hoàn toàn. Các liên kết song công hoàn toàn không cho phép nhiều hơn hai thiết bị trên một phân đoạn mạng, do đó, xác định xem một liên kết có phải là song công hoàn toàn hay không là cách nhanh nhất để kiểm tra tính khả thi của việc được kết nối với một bộ chuyển mạch.

<span style="background:#fff88f">LƯU Ý</span> *Các thiết bị Lớp 2 đa truy cập như hub chỉ có thể kết nối ở chế độ bán song công. Nếu một cổng chỉ có thể kết nối qua chế độ bán song công, nó phải hoạt động ở trạng thái chuyển tiếp 802.1D truyền thống.hạ lưu. Chỉ nên có một cổng được chỉ định đang hoạt động trên một liên kết.*




