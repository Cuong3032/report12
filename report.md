## Bài 2

### 1.1 Cài đặt và sử dụng BurpSuite

- Đầu tiên vào trang chủ PortSwigger rồi chọn và cài đặt Burp Suite Community Edition phù hợp với hệ điều hành

  <img width="2482" height="1255" alt="image" src="https://github.com/user-attachments/assets/75943d5b-a064-492f-9845-2034a04cc147" />
	
- sau đó cài proxy như hình cho trình duyệt

  <img width="2560" height="1440" alt="Ảnh chụp màn hình 2025-12-24 214406" src="https://github.com/user-attachments/assets/262c7084-ee97-47ea-8a93-e4c7d4fe82b8" />
	
- hoặc có thể cài extension foxyProxy(nếu dùng fireFox)
  
<img width="2482" height="1198" alt="image" src="https://github.com/user-attachments/assets/888b39f6-00d8-45ec-ad0f-aa88befa60bb" />

- tiếp đến là cài CA Certificate vào Firefox để bắt được gói tin HTTPS (Google, Facebook...).

  <img width="706" height="822" alt="Ảnh chụp màn hình 2025-12-24 214506" src="https://github.com/user-attachments/assets/e470177a-eb47-4a9a-ab03-0b1a9f536b18" />
	
- và đây là giao diện của Burp Suite khi đang chặn (Intercept) một gói tin.
  
<img width="2424" height="1331" alt="image" src="https://github.com/user-attachments/assets/68d00517-8c1d-4d1b-8e54-5f11a6b254c4" />

- Chức năng cơ bản của Burp Suite thì có
  - Proxy là nơi chặn, xem và chỉnh sửa gói tin trước khi gửi đi hoặc nhận về
  	* Intercept is On/Off:

			On: Gói tin bị giữ lại tại Burp, trang web sẽ xoay vòng chờ đợi. Bạn có thể sửa gói tin rồi bấm Forward để gửi đi, hoặc Drop để hủy.

			Off: Gói tin đi qua tự động (nhưng vẫn được ghi lại trong lịch sử).

	* HTTP History: Lưu lại tất cả các request/response đã đi qua. Bạn thường vào đây để tìm lại các gói tin cũ.
  - Repeater: Dùng để gửi đi gửi lại một request nhiều lần với các thay đổi nhỏ để xem server phản ứng thế nào.

	<img width="2429" height="1339" alt="image" src="https://github.com/user-attachments/assets/b6128785-5645-46b1-9656-4a68f814277d" />
	

  - Intruder: là một công cụ tấn công tự động trong BurpSuite. Nó giống như một “máy thử giá trị” (fuzzer) – bạn đưa vào một danh sách các giá trị (payloads), BurpSuite sẽ lần lượt chèn chúng vào vị trí bạn chọn trên request và quan sát phản hồi của server.
 
  	<img width="2430" height="1336" alt="image" src="https://github.com/user-attachments/assets/c5a043ba-a8a2-4466-a335-62a473335c8d" />
	 

  - Decoder: là một công cụ trong BurpSuite dùng để mã hóa (encode) và giải mã (decode) dữ liệu. Nó hỗ trợ nhiều phương pháp phổ biến như: URL encoding, HTML encoding, Base64, Hexadecimal (Hex)

	 <img width="2440" height="1339" alt="image" src="https://github.com/user-attachments/assets/697e5e9b-1bf7-49f3-9aba-f818cb181f78" />
 
### 1.2 Cấu trúc HTTP Request/Response

#### HTTP Request

- HTTP Request Là tin nhắn client gửi đi để kích hoạt một hành động trên server.
##### Start-line (Request-line)
Cấu trúc: <method> <request-target> <protocol>
- `<method>`: HTTP method là một trong một tập hợp các từ được xác định mô tả ý nghĩa của yêu cầu và kết quả mong muốn(ví dụ: GET, POST, PUT, DELETE).
- `<request-target>`: Request-target thường là một URL tuyệt đối hoặc tương đối, tùy thuộc vào ngữ cảnh của request
  	* Trong origin form, request-target là đường dẫn tuyệt đối (có thể kèm query string). Server dựa vào Host header để ghép thành URL đầy đủ.Origin form được dùng với các phương thức phổ biến: GET, POST, HEAD, OPTIONS.
 
    		GET /en-US/docs/Web/HTTP/Guides/Messages HTTP/1.1
	* Absolute form là dạng request-target hiển thị URL đầy đủ kèm authority, và thường được dùng với phương thức GET khi client gửi request qua proxy để proxy biết chính xác tài nguyên cần truy cập.

			GET https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Messages HTTP/1.1
	* Authority form là dạng request-target chỉ gồm tên miền/IP và cổng. Nó chỉ xuất hiện trong phương thức CONNECT, khi client muốn thiết lập một HTTP tunnel qua proxy để truyền dữ liệu (thường cho HTTPS).

			CONNECT developer.mozilla.org:443 HTTP/1.1
	* Asterisk form là dạng request-target đặc biệt, chỉ dùng với OPTIONS, để biểu diễn server như một tổng thể thay vì một tài nguyên cụ thể. Nó hữu ích khi client muốn biết khả năng chung của server, chẳng hạn như những phương thức HTTP nào được hỗ trợ.

			OPTIONS * HTTP/1.1
- `<protocol>`: HTTP version trong request line là thông tin cho server biết cấu trúc và cách phản hồi. Hầu hết hiện nay là HTTP/1.1, còn các phiên bản mới như HTTP/2 và HTTP/3 không cần ghi version trong thông điệp vì đã được xác định từ kết nối

