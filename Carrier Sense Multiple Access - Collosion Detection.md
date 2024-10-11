## Carrier Sense Đa Truy Cập Với Phát Hiện Va Chạm

CSMA/CD, một giao thức xử lý MAC, trước tiên sẽ phát hiện mọi truyền dẫn từ các trạm khác trong kênh và chỉ bắt đầu truyền khi kênh đó rảnh để truyền.

Ngay khi một trạm phát hiện ra va chạm, nó sẽ dừng truyền và gửi tín hiệu kẹt. Sau đó, nó sẽ đợi một khoảng thời gian trước khi truyền lại.

**Hãy cùng tìm hiểu ý nghĩa của từng thành phần riêng lẻ trong CSMA/CD.**

1. **CS –** Viết tắt của Carrier Sensing. Nó ngụ ý rằng trước khi gửi dữ liệu, một trạm đầu tiên sẽ cảm nhận sóng mang. Nếu sóng mang được tìm thấy rảnh, thì trạm sẽ truyền dữ liệu, nếu không thì sẽ không truyền.
2. **MA –** Viết tắt của Multiple Access, nghĩa là nếu có một kênh, thì sẽ có nhiều đài đang cố gắng truy cập kênh đó.
3. **CD –** Viết tắt của Collision Detection. Nó cũng hướng dẫn cách xử lý trong trường hợp xảy ra va chạm dữ liệu gói.

## CSMA/CD là gì

Quy trình CSMA/CD có thể được hiểu là một cuộc thảo luận nhóm, trong đó nếu những người tham gia nói cùng một lúc thì sẽ rất khó hiểu và việc giao tiếp sẽ không diễn ra.

Thay vào đó, để giao tiếp tốt, cần yêu cầu những người tham gia nói chuyện lần lượt để chúng ta có thể hiểu rõ sự đóng góp của từng người trong cuộc thảo luận.

Khi một người tham gia nói xong, chúng ta nên đợi một khoảng thời gian nhất định để xem có người tham gia nào khác đang nói hay không. Người ta chỉ nên bắt đầu nói khi không có người tham gia nào khác nói. Nếu một người tham gia khác cũng nói cùng lúc, thì chúng ta nên dừng lại, đợi và thử lại sau một thời gian.

Tương tự như quá trình CSMA/CD, trong đó việc truyền gói dữ liệu chỉ được thực hiện khi phương tiện truyền dữ liệu rảnh. Khi nhiều thiết bị mạng cố gắng chia sẻ một kênh dữ liệu cùng lúc, thì sẽ gặp phải **xung đột dữ liệu** .

Phương tiện được giám sát liên tục để phát hiện bất kỳ xung đột dữ liệu nào. Khi phương tiện được phát hiện là rảnh, trạm phải đợi trong một khoảng thời gian nhất định trước khi gửi gói dữ liệu để tránh mọi khả năng xung đột dữ liệu.

Khi không có trạm nào khác cố gắng gửi dữ liệu và không phát hiện xung đột dữ liệu thì việc truyền dữ liệu được coi là thành công.

### Thuật toán

**Các bước của thuật toán bao gồm:**

- Đầu tiên, trạm muốn truyền dữ liệu sẽ cảm nhận sóng mang đang bận hay nhàn rỗi. Nếu sóng mang được tìm thấy nhàn rỗi, thì quá trình truyền sẽ được thực hiện.
- Trạm truyền phát hiện ra va chạm, nếu có, bằng cách sử dụng điều kiện: **Tt >= 2 * Tp** trong đó Tt là độ trễ truyền và Tp là độ trễ lan truyền.
- Trạm sẽ phát tín hiệu nhiễu ngay khi phát hiện va chạm.
- Sau khi va chạm xảy ra, trạm truyền dừng truyền và chờ một khoảng thời gian ngẫu nhiên gọi là ' **thời gian lùi'.** Sau khoảng thời gian này, trạm truyền lại lần nữa.

**Biểu đồ luồng CSMA/CD**

[![Biểu đồ luồng CSMA/CD mới](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/CD-Flow-Chart-new.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/CD-Flow-Chart-new.png)

_[ [nguồn](https://www.gatevidyalay.com/) hình ảnh ]_

### CSMA/CD hoạt động như thế nào

Để hiểu cách thức hoạt động của CSMA/CD, chúng ta hãy xem xét tình huống sau.

[![CD CSMA hoạt động như thế nào](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/How-does-CSMA-CD-work.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/How-does-CSMA-CD-work.png)

- Giả sử có hai trạm A và B. Nếu trạm A muốn gửi một số dữ liệu đến trạm B, thì trước tiên nó phải cảm nhận sóng mang. Dữ liệu chỉ được gửi nếu sóng mang rảnh.
- Nhưng khi đứng tại một điểm, nó không thể cảm nhận được toàn bộ sóng mang, nó chỉ có thể cảm nhận được điểm tiếp xúc. Theo giao thức, bất kỳ trạm nào cũng có thể gửi dữ liệu bất kỳ lúc nào, nhưng điều kiện duy nhất là trước tiên phải cảm nhận được sóng mang như thể nó đang rảnh hoặc bận.
- Trong trường hợp A và B cùng bắt đầu truyền dữ liệu của họ, thì rất có thể dữ liệu của cả hai trạm sẽ va chạm. Vì vậy, cả hai trạm sẽ nhận được dữ liệu va chạm không chính xác.

**_Vậy câu hỏi đặt ra ở đây là: làm sao các trạm biết được dữ liệu của họ đã bị xung đột?_**

Câu trả lời cho câu hỏi này là, nếu tín hiệu keo quay trở lại trong quá trình truyền dẫn thì điều đó cho thấy va chạm đã xảy ra.

Để làm được điều này, các trạm cần phải tiếp tục truyền dữ liệu. Chỉ khi đó, họ mới có thể chắc chắn rằng dữ liệu của chính họ đã bị xung đột/hỏng.

Trong trường hợp này, gói tin đủ lớn, nghĩa là khi tín hiệu va chạm trở về trạm truyền, trạm vẫn đang truyền phần dữ liệu bên trái. Khi đó, nó có thể nhận ra rằng dữ liệu của chính nó đã bị mất trong va chạm.

### Hiểu về phát hiện va chạm

Để phát hiện va chạm, điều quan trọng là trạm phải tiếp tục truyền dữ liệu cho đến khi trạm truyền nhận được tín hiệu va chạm nếu có.

Hãy lấy một ví dụ trong đó các bit đầu tiên được truyền bởi trạm có liên quan đến va chạm. Giả sử chúng ta có bốn trạm A, B, C và D. Giả sử độ trễ lan truyền từ trạm A đến trạm D là 1 giờ, tức là nếu bit gói dữ liệu bắt đầu di chuyển lúc 10 giờ sáng, thì nó sẽ đến D lúc 11 giờ sáng

[![Hiểu về phát hiện va chạm](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/Understanding-Collision-Detection.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/Understanding-Collision-Detection.png)

- Vào lúc 10 giờ sáng, cả hai trạm A và D đều cảm nhận sóng mang đang rảnh và bắt đầu truyền.
- Nếu độ trễ lan truyền tổng thể là 1 giờ, thì sau nửa giờ, cả hai bit đầu tiên của trạm sẽ đạt đến một nửa và sẽ sớm xảy ra va chạm.
- Vì vậy, đúng vào lúc 10:30 sáng, sẽ có một vụ va chạm tạo ra tín hiệu va chạm.
- Vào lúc 11 giờ sáng, tín hiệu va chạm sẽ đến trạm A và D, tức là đúng một giờ sau các trạm nhận được tín hiệu va chạm.

**Do đó, để các trạm tương ứng phát hiện ra rằng dữ liệu của chính chúng đã bị va chạm thì thời gian truyền cho cả hai trạm phải lớn hơn thời gian lan truyền của chúng. _tức là Tt>Tp_**

Trong đó Tt là thời gian truyền và Tp là thời gian lan truyền.

**Bây giờ chúng ta hãy xem xét tình huống tệ nhất.**

[![thời gian truyền và Tp](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/transmission-time-and-Tp.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/transmission-time-and-Tp.png)

- Trạm A bắt đầu phát sóng lúc 10 giờ sáng và sắp đến trạm D lúc 10:59:59 sáng
- Vào thời điểm này, trạm D bắt đầu truyền sau khi cảm nhận được sóng mang đang rảnh.
- Vì vậy, bit đầu tiên của gói dữ liệu được gửi từ trạm D sẽ va chạm với gói dữ liệu của trạm A.
- Sau khi va chạm xảy ra, chất mang bắt đầu gửi tín hiệu dạng keo.
- Trạm A sẽ nhận được tín hiệu va chạm sau 1 giờ.

Đây là điều kiện để **phát hiện va chạm trong trường hợp xấu nhất,** nếu một trạm muốn phát hiện va chạm thì nó phải tiếp tục truyền dữ liệu cho đến khi đạt **2Tp, tức là Tt>2*Tp.**

**_Bây giờ câu hỏi tiếp theo là nếu trạm phải truyền dữ liệu trong thời gian ít nhất là 2*Tp thì trạm cần có bao nhiêu dữ liệu để có thể truyền trong khoảng thời gian này?_**

[![phát hiện va chạm trong trường hợp xấu nhất mới](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/detecting-collision-in-the-worst-case-new.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/detecting-collision-in-the-worst-case-new.png)

Vì vậy, để phát hiện va chạm, kích thước tối thiểu của gói tin phải là 2*Tp*B.

**Sơ đồ bên dưới giải thích sự va chạm của các bit đầu tiên trong CSMA/CD:**

[![Va chạm của các bit đầu tiên trong CSMA](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/Collision-of-first-bits-in-CSMA.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/Collision-of-first-bits-in-CSMA.png)

_[ [nguồn](https://slideplayer.com/) hình ảnh ]_

Trạm A, B, C, D được kết nối thông qua dây Ethernet. Bất kỳ trạm nào cũng có thể gửi gói dữ liệu của mình để truyền sau khi cảm nhận tín hiệu là nhàn rỗi. Ở đây, các gói dữ liệu được gửi theo bit mất thời gian để truyền. Do đó, có khả năng xảy ra va chạm.

Trong sơ đồ trên, tại thời điểm t1, trạm A bắt đầu truyền bit dữ liệu đầu tiên sau khi cảm nhận được sóng mang là rảnh. Tại thời điểm t2, trạm C cũng cảm nhận được sóng mang là rảnh và bắt đầu truyền dữ liệu. Tại thời điểm t3, va chạm xảy ra giữa các bit được gửi bởi các trạm A và C.

Do đó, thời gian truyền cho trạm C trở thành t3-t2. Sau va chạm, sóng mang sẽ gửi lại tín hiệu keo cho trạm A, tín hiệu này sẽ đến trạm A tại thời điểm t4. Điều này có nghĩa là trong khi gửi dữ liệu, va chạm cũng có thể được phát hiện.

**Sau khi xem thời lượng của hai lần truyền, hãy tham khảo hình bên dưới để hiểu rõ hơn.**

[![slideplaye mới](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/slideplaye-new.png)](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/12/slideplaye-new.png)

### Hiệu quả của CSMA/CD

Hiệu quả của CSMA/CD tốt hơn Pure ALOHA, tuy nhiên có một số điểm cần lưu ý khi đo hiệu quả của CSMA/CD.

**Bao gồm:**

- Nếu khoảng cách tăng thì hiệu quả của CSMA/CD sẽ giảm.
- Đối với Mạng cục bộ (LAN), CSMA/CD hoạt động tối ưu nhưng đối với mạng đường dài như WAN, không nên sử dụng CSMA/CD.
- Nếu chiều dài của gói tin lớn hơn, thì hiệu quả sẽ tăng lên nhưng vẫn có giới hạn. Giới hạn tối đa cho chiều dài của gói tin là 1500 byte.

### Ưu điểm và nhược điểm của CSMA/CD

**Thuận lợi**

- Chi phí thấp hơn trong CSMA/CD.
- Bất cứ khi nào có thể, nó sẽ sử dụng toàn bộ băng thông.
- Nó phát hiện va chạm trong một khoảng thời gian rất ngắn.
- Hiệu quả của nó tốt hơn CSMA đơn giản.
- Nó chủ yếu tránh mọi hình thức truyền tải lãng phí.

**Nhược điểm**

- Không phù hợp với mạng có khoảng cách lớn.
- Giới hạn khoảng cách là 2500 mét. Không thể phát hiện va chạm sau giới hạn này.
- Không thể chỉ định mức độ ưu tiên cho một số nút nhất định.
- Khi thêm thiết bị, hiệu suất sẽ bị ảnh hưởng theo cấp số nhân.

### Ứng dụng

CSMA/CD được sử dụng trong các biến thể Ethernet phương tiện chia sẻ (10BASE2, 10BASE5) và trong các phiên bản đầu tiên của Ethernet cặp xoắn sử dụng bộ trung tâm lặp lại.

Nhưng ngày nay, các mạng Ethernet hiện đại được xây dựng bằng các công tắc và kết nối toàn song công nên CSMA/CD không còn được sử dụng nữa.