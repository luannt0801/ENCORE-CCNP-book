Chương này bao gồm các chủ đề sau:
- **OSPF Fundamentals** : Phần này cung cấp tổng quan về giao tiếp giữa các bộ định tuyến OSPF.
- **OSPF configuration:** Phần này mô tả các kỹ thuật cấu hình OSPF và các lệnh có thể được thực hiện để xác minh việc trao đổi tuyến đường.
- **Default Route Advertisement:** Phần này giải thích cách quảng cáo tuyến mặc định trong OSPF.
- **Common OSPF Optimizations:** Phần này xem xét các thiết lập OSPF phổ biến để tối ưu hóa hoạt động của giao thức.

Giao thức Open Shortest Path First (OSPF) là <span style="background:#fff88f">giao thức định tuyến link-state </span>đầu tiên được đề cập trong cuốn sách này. OSPF là Giao thức cổng nội bộ (IGP) không độc quyền khắc phục được những thiếu sót của các giao thức định tuyến vectơ khoảng cách khác và phân phối thông tin định tuyến trong một miền định tuyến OSPF duy nhất. OSPF đã giới thiệu khái niệm mặt nạ mạng con có độ dài thay đổi (VLSM), hỗ trợ định tuyến không phân lớp, tóm tắt, xác thực và gắn thẻ tuyến bên ngoài. Có hai phiên bản chính của OSPF trong các mạng sản xuất hiện nay:

- OSPF Phiên bản 2 (OSPFv2): Được định nghĩa trong RFC 2328 và hỗ trợ IPv4
- OSPF Phiên bản 3 (OSPFv3): Được định nghĩa trong RFC 5340 và sửa đổi cấu trúc ban đầu để hỗ trợ IPv6

### OSPF Fundamentals:

OSPF gửi cho các routers lân cận một **<font color="#ff0000">Link-State Advertisements (LSAs)</font>** - bao gồm<font color="#ff0000"> link state </font> và <font color="#ff0000">link metric</font>
Bên nhận khi nhận được LSAs thì sẽ lưu trữ vào trong local database gọi là <font color="#e36c09">link-state database (LSDB)</font>. Sau đó chúng sẽ được flooded - tràn ngập khắp miền định tuyến OSPF, giống như bộ định tuyến quảng cáo đã quảng cáo chúng. Tất cả các bộ định tuyến OSPF đều duy trì một bản sao giống hệt nhau được đồng bộ hóa của LSDB cho cùng một khu vực.

**LSDB (Link-State Database)** cung cấp cấu trúc mạng, về cơ bản cho phép router có một bản đồ đầy đủ về mạng. Tất cả các router OSPF (Open Shortest Path First) *sử dụng thuật toán Dijkstra để xây dựng một cấu trúc topology không có vòng lặp với các đường đi ngắn nhất*. OSPF tự động phát hiện các thay đổi trong cấu trúc mạng và tính toán các đường đi không vòng lặp trong một khoảng thời gian ngắn với ít lưu lượng giao thức định tuyến. <span style="background:#fff88f">Mỗi router coi chính nó là gốc hoặc đỉnh của cây SPF</span> (Shortest Path First), và cây SPT này chứa tất cả các điểm đến trong miền OSPF. <span style="background:#fff88f">Cây SPT khác nhau cho mỗi router OSPF, nhưng LSDB được sử dụng để tính toán cây SPT là giống nhau cho tất cả các router OSPF.</span> Điều này có nghĩa là mặc dù mỗi router có một cái nhìn khác nhau về cây đường đi ngắn nhất của nó, nhưng tất cả đều dựa trên cùng một cơ sở dữ liệu trạng thái liên kết.

![](Pasted%20image%2020241014143435.png)

STPs tạo ra ảo giác rằng không có sự dự phòng nào tồn tại đối với các mạng. Nhưng tất cả SPT hiển thị đường dẫn ngắn nhất để tiếp cận mạng và được xây dựng từ LSDB, chứa tất cả các liên kết cho một khu vực. Trong quá trình thay đổi cấu túc topology thì STP sẽ được xây dựng lại và có thể thay đổi.

##### OSPF cung cấp khả năng mở rộng cho bảng định tuyến

Bằng cách sử dụng multiple OSPF areas cùng với miền định tuyến. 

OSPF areas cung cấp 1 bộ các kết nối mạng, và các host được nhóm lại với nhau.

OSPF sử dụng kiến ​​trúc phân cấp hai tầng, trong đó<font color="#e36c09"> Area 0</font> là một vùng đặc biệt được gọi là **backbone**, mà tất cả các vùng khác phải kết nối. Nói cách khác,
<font color="#e36c09">Area 0 </font>cung cấp kết nối trung chuyển giữa các vùng không phải **backbone**. Các vùng không phải **backbone** quảng cáo các tuyến đường vào **backbone**, và sau đó **backbone** quảng cáo các tuyến đường vào các vùng không phải **backbone** khác.

![](Pasted%20image%2020241014144950.png)

Cấu trúc chính xác của khu vực không thể nhìn thấy từ bên ngoài khu vực trong khi vẫn cung cấp kết nối đến các bộ định tuyến bên ngoài khu vực. Điều này có nghĩa là <span style="background:#fff88f">các bộ định tuyến bên ngoài khu vực không có bản đồ cấu trúc hoàn chỉnh cho khu vực đó, làm giảm lưu lượng OSPF trong khu vực đó.</span> Khi bạn phân đoạn một miền định tuyến OSPF thành nhiều khu vực, không còn đúng nữa là tất cả các bộ định tuyến OSPF sẽ có LSDB giống hệt nhau; tuy nhiên, tất cả các bộ định tuyến trong cùng một khu vực sẽ có LSDB khu vực giống hệt nhau.

Việc giảm lưu lượng định tuyến sử dụng ít bộ nhớ và tài nguyên của bộ định tuyến hơn và do đó cung cấp khả năng mở rộng. 

Một bộ định tuyến có thể chạy nhiều tiến trình OSPF. Mỗi tiến trình duy trì cơ sở dữ liệu riêng của nó và các tuyến đường được học trong một tiến trình OSPF không khả dụng cho một tiến trình OSPF khác nếu không phân phối lại các tuyến đường giữa các tiến trình. Các số tiến trình OSPF có ý nghĩa cục bộ và không phải khớp giữa các bộ định tuyến. Chạy tiến trình OSPF số 1 trên một bộ định tuyến và chạy tiến trình OSPF số 1234 vẫn cho phép hai bộ định tuyến trở thành hàng xóm.

### Inter-Router Communitcation

OSPF chạy trên địa chỉ IPv4, sử dụng protocol 89 của nó (Được cung cấp cho OSPF bởi Internet Assigned Numbers Authority - IANA)\

OSPF sử dụng multicast để khả dụng trong việc giảm các traffic không cần thiết.

2 địa chỉ OSPF multicast của OSPF:
- AllSPFRouters: IPv4 address 224.0.0.5 or MAC address 01:00:5E:00:00:05. Tất cả các Router chạy OSPF nên được khả dụng để nhận packets này
- AllDRouters: IPv4 address 224.0.0.6 or MAC address 01:00:5E:00:00:06. Giao tiếp với bộ định tuyến được chỉ định - Designated Routers (DR) sử dụng địa chỉ này

#### OSPF packets gồm 5 packets:


| Type | Packet Name                       | Functional Overview                                                                                                                                                                                               |
| ---- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | Hello                             | Những packet này cho việc khám phá và duy trì với các neighbors. Packets được gửi theo chu kỳ bởi tất cả OSPF interfaces đi khám phá các new neighbors khi vẫn đảm bảo rằng các neighbors liền kề khác vẫn online |
| 2    | Databaes Description (DBD or DDP) | Những packet này tổng hợp lại các nội dung của database. Những gói tin này được trao đổi khi các sự kề cận OSPF đầu tiên được hình thành. Các packets này mô tả nội dung của LSDB                                 |
| 3    | Link-state request (LSR)          | Các gói này dành cho việc tải xuống cơ sở dữ liệu. Khi một bộ định tuyến nghĩ rằng một phần LSDB của nó đã cũ, nó có thể yêu cầu một phần cơ sở dữ liệu của hàng xóm bằng cách sử dụng loại gói này.              |
| 4    | Link-state Update (LSU)           | Các gói này dành cho việc cập nhật cơ sở dữ liệu. Đây là một LSA rõ ràng cho một liên kết mạng cụ thể và thường được gửi trực tiếp phản hồi đến một LSR                                                           |
| 5    | Link-state Ack                    | Các gói này dành cho xác nhận tràn ngập. Các gói này được gửi để phản hồi lại việc tràn ngập LSA, do đó làm cho việc tràn ngập trở thành một tính năng vận chuyển đáng tin cậy.                                   |

##### OSPF Hello Packet

| Data Field                                      | Description                                                                                                                                                       |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Router ID (RID)                                 | ID 23-bit riêng cho dừng OSPF domain                                                                                                                              |
| Authentication Options                          | Trường anfy cho phép giao tiếp bảo mật giữa OSPF routers để chống lại những hoạt động độc hại. Thiết lập là rỗng, clear text, hoặc Message Digest (MD5) xác thực. |
| Area ID                                         | OSPF area thuộc về OSPF interface. là số 32-bít được viết theo dạng thập phân chấm (0.0.1.0) hoặc deccimal (255)                                                  |
| Interface address mask                          | Mặc nạ mạng cho địa chỉ IP chisnhc ảu giao diện mà lời chào được gửi đi.                                                                                          |
| Interface priority                              | Ưu tiên giao diện bộ định tuyến cho cuộc bầu cử DR                                                                                                                |
| Hello interval                                  | Khoảng thời gian tính bằng giây mà routers gửi các gói tin chào trên giao diện                                                                                    |
| Dead interal                                    | KHoảng thời gian tính bằng giây mà bộ định tuyến chờ để nghe lời chào từ bộ định tuyến lân cận trước khi tuyên bố bố định cho ngừng hoạt động.                    |
| Designated Router and backeup designated router | Địa chỉ IP cảu DR và DR dự phòng (BDR) cho liên kết mạng.                                                                                                         |
| Actice neighbor                                 | Danh sách các láng giềng OSPF được nhìn thấ trên phân đoạn mạng. Một bộ định tuyến mà phải nhận được lời chào từ láng giếng trong khoảng thời gian giám sát       |

**Router ID:** RID là duy nhất cho mỗi quy trình OSPF trong một miền OSPF và phải là duy nhất giữa các quy trình OSPF trên một Router.

**Neighbors:** Là một Router chia sẻ một liên kết mạng hỗ trợ OSPF chung. Các Router OSPF khám phá các hàng xóm khác thông qua các gói tin chào OSPF. Một neighbor OSPF liền kề là một neighbor OSPF chia sẻ cơ sở dữ liệu OSPF được đồng bộ hóa giữa 2 neighbors,

##### OSPF Neighbor States


| State    | Description                                                                                                                                                                                                                   |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Down     | Là trạng thái ban đầu của mối quan hệ với neighbor, nó chỉ ra rằng router chưa nhận được bất kỳ gói tin OSPF nào.                                                                                                             |
| Attempt  | Trạng thái này liên quan đến các mạng NBMA không hỗ trợ phát sóng và yêu cầu cấu hình lân cạn rõ ràng. Trạng thái này không cho biết không có thông tin nào được nhận gần đây, nhưng bộ định tuyến vẫn đang cố gắng giao tiếp |
| Init     | Cho biết 1 gói tin chào đã được nhận từ một router khác nhưng giao tiếp 2 chiều chưa được thiết lập                                                                                                                           |
| 2-Way    | Giao tiếp 2 chiều đã được thiết lập. Nếu cầu DR hoặc BDR cuộc bầu cử sẽ diễn ra trong trạng thái này.                                                                                                                         |
| ExStart  | Đây là trạng thái đầu tiên trong việc hình thành sự kề cận. Router xác định router nào sẽ là chủ hoặc tớ cho quá trình đồng bộ hóa LSDB                                                                                       |
| Exchange | Các router trao đổ các link states bằng cách sử dụng các gói BDB                                                                                                                                                              |
| Loading  | Các gói LSR được gửi đến thiết bị lân cạn, yêu cầu các LSA gần hơn đã được phát hiện (nhưng chưa nhận được) trong trạng thái Exchange                                                                                         |
| Full     | Các router lân cận hoàn toàn liền kề nhau                                                                                                                                                                                     |

##### Designated Router and Backup Designated Router
<u>Bộ định tuyến chỉ định và bộ định tuyến dự phòng.</u>

**Vấn đề**
Mạng đa truy cập (Multi-access networks) ví dụ như mạng Ethernets(LANs) và Frame Relay cho phép nhiều hơn 2 routers tồn tại trên một network segment
=> Thiết lập như vậy gây ra các vấn đề về khả năng mở rộng với OSPF khi số lượng bộ định tuyến trong một phân đoạn tăng lên. các routers bổ sung sẽ làm ngập - flood nhiều LSA hơn trên phân đoạn và lưu lượng OSPF trở nên quá mức khi các lân cận OSPF tăng lên.

Nếu 4 định tuyến chia sẻ cùng 1 mạng đa truy cập, sáu lân cận OSPF sẽ hình thành, cùng với sau lần xảy ra tình trạng ngập cơ sở dữ liệu trên 1 mạng.

  ![](Pasted%20image%2020241015091037.png)


${n*(n-1)}/2$

<span style="background:#fff88f">Hình minh họa tốc độ theo cấp số nhân của các kết nối OSPF cần thiết khi các bộ định tuyến trên một phân đoạn mạng tăng lên.</span>

![](Pasted%20image%2020241015091148.png)

**Giải pháp**

OSPF vượt qua tác động bằng cách tạo ra một pseudonode (một virtual router) để quản lý các trạng thái lân cận với tất cả các routers khác trên phân đoạn mạng broadcast đó. Một router trên broadcast segment được gọi là Designated Router đảm nhận vai trò của pseudonode. DR làm giảm số lượng OSPF adjacency trên phân đoạn mạng đa truy cập vì các bộ định tuyến chỉ tạo thành kề cận OSPF đầy đủ với DR chứ không phải với nhau.

DR chịu trách nhiệm cho việc tràn ngập các bản cập nhật với tất cả các bộ định tuyến OSPF trên phân đoạn đó khi các bản cập nhật xảy ra.

![](Pasted%20image%2020241015092015.png)


![](Pasted%20image%2020241015092115.png)


##### Statically Setting the Router ID

Theo mặc định, RID sẽ được tự động allocated sử dụng IP address cao nhất của bất kì up loopback interfaces.

Nếu không có ip address up nào thì địa chỉ IP cao nhất của interface vật lý đang up nào sẽ trở thành RID

Tiến trình OSPF chọn RID khi nó đang khởi tạo và không thay đổi cho đến khi tiến trình khởi động lại.




