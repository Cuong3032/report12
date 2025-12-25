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

##### Request headers

- Request header là phần siêu dữ liệu (metadata) đi kèm với một HTTP request, nằm sau dòng bắt đầu (start line) và trước phần body. Nó cung cấp thông tin bổ sung để server hiểu rõ cách xử lý yêu cầu của client
- Cấu trúc: mỗi header là 1 cặp key:value phân cách bởi dấu `:`, không phân biệt hoa thường và toàn bộ header nằm trên 1 dòng duy nhất, có thể rất dài như Cookie

  <img width="1899" height="1256" alt="image" src="https://github.com/user-attachments/assets/becb4dc0-b404-40a3-976d-990d27edf6ba" />

- Phân loại Header trong HTTP
  	* Request Headers: Chỉ xuất hiện trong request (từ client gửi lên server). Mục đích là cung cấp ngữ cảnh bổ sung hoặc logic xử lý cho server.
  	* Representation Headers: Xuất hiện khi request có body (ví dụ: POST, PUT). Mục đích là mô tả dữ liệu gốc và cách nó được mã hóa để server có thể tái tạo chính xác.
  	* Headers dùng chung (cả request và response)
##### Request body

- Request body là nơi chứa dữ liệu mà client muốn gửi lên server. Chỉ có PATCH, PUT, POST request là có body.
- Được mô tả bởi headers như Content-Type (kiểu dữ liệu) và Content-Length (độ dài dữ liệu).

#### HTTP responses

- HTTP Response là thông điệp mà máy chủ (server) gửi ngược lại cho máy khách (client/browser) sau khi nhận và xử lý một yêu cầu (request).
- Cấu trúc của nó bao gồm các thành phần chính sau:

1. Dòng trạng thái (Status Line)

   		<protocol> <status-code> <reason-phrase>
   - `<protocol>`: là phiên bản HTTP, thường là HTTP/1.1 hoặc HTTP/2.
   - `<status-code>`: Mã status code cho biết yêu cầu thành công hoặc thất bại. Các status code thường gặp là 200, 404, 302.
   - `<reason-phrase>`: Một mô tả ngắn gọn, dễ đọc cho con người về mã trạng thái đó. Ví dụ: `OK`, `Not Found`, `Bad Request`.

2. Response headers

   - Respone headers ngay sau dòng trạng thái là các dòng tiêu đề (Headers). Chúng cung cấp thêm thông tin (metadata) về máy chủ hoặc về tài nguyên được gửi về. Và giống như Request headers nó không phân biệt hoa thường và mỗi header là 1 cặp key:value
   - Giống như request headers, response headers cũng được chia thành nhiều loại:
   		* Response Headers: Xuất hiện trong phản hồi từ server → client. Cung cấp ngữ cảnh bổ sung hoặc logic xử lý tiếp theo cho client.
				   		Ví dụ:
				
				Server: thông tin về phần mềm server (Apache, Nginx…).
				
				Date: thời điểm server tạo ra response.
				
				Cache-Control: quy định cách client hoặc proxy cache dữ liệu.
				
				Location: dùng trong redirect, chỉ định URL mới.
			* Những header này không mô tả nội dung dữ liệu, mà chủ yếu là thông tin về cách client nên xử lý response.
     * Representation Headers: Xuất hiện khi response có body (ví dụ: trả về HTML, JSON, file). Mục đích là mô tả dữ liệu gốc và cách nó được mã hóa để client có thể tái tạo chính xác. Ví dụ:
			
			Content-Type: loại dữ liệu (HTML, JSON, XML…).

			Content-Length: độ dài dữ liệu trong body.
			
			Content-Encoding: cách dữ liệu được nén (gzip, deflate).
			
			Content-Language: ngôn ngữ của nội dung (ví dụ: en-US, vi-VN).
		* Những header này giúp client hiểu dữ liệu nhận được là gì và cách giải mã nó.

3. Response body

	 - Response body là phần dữ liệu mà server trả về cho client sau khi xử lý một request. Nó thường xuất hiện trong hầu hết các phản hồi, ngoại trừ một số trường hợp đặc biệt.
	- Đặc điểm chính
		- Có mặt trong đa số response  
		  - Với `GET` thành công → chứa dữ liệu mà client yêu cầu (HTML, JSON, file…).  
		  - Với request lỗi → mô tả lý do thất bại, cho biết lỗi tạm thời hay vĩnh viễn.  
		- Không phải lúc nào cũng có body  
		  - Một số status code như `201 Created` hoặc `204 No Content` có thể không cần body.  
- Các loại response body:

	a. Single-resource body  
	- Được xác định bởi hai header:
  
  		- `Content-Type`: loại dữ liệu (HTML, JSON, XML…).
  		- `Content-Length`: độ dài dữ liệu.  
	- Ví dụ: trả về một trang HTML hoặc một JSON object.
	
	b. Chunked body (Transfer-Encoding: chunked)  
	- Dùng khi dữ liệu có độ dài không xác định trước.  
	- Server gửi dữ liệu thành nhiều “chunk” liên tiếp cho đến khi hoàn tất.  
	- Thường dùng trong streaming hoặc dữ liệu động.
	
	c. Multiple-resource body (multipart  
	- Chứa nhiều phần dữ liệu khác nhau trong cùng một response.  
	- Mỗi phần có header riêng (ví dụ: `Content-Disposition`).  
	- Thường gặp trong:
	  	- HTML Forms (upload nhiều file).  
	    - Range requests (client yêu cầu nhiều đoạn của một file).

#### Blank line
   - Vị trí: Luôn nằm giữa phần Headers và phần Body
   - Vai trò kỹ thuật: Đây là dấu hiệu bắt buộc (ký tự CRLF hay \r\n) để báo cho Server hoặc Browser biết rằng "Phần Header đã kết thúc tại đây, những gì phía sau là Dữ liệu (Body)".

#### HTTP Method

- GET: Dùng để lấy dữ liệu từ server. Đây là phương thức an toàn, idempotent (kết quả không thay đổi khi gọi nhiều lần), và có thể cache.

- HEAD: Giống như GET nhưng chỉ lấy phần header của response, không có body. Thường dùng để kiểm tra metadata (ví dụ: kích thước file, loại nội dung).

- POST: Dùng để gửi dữ liệu lên server (ví dụ: form, JSON). Server có thể tạo tài nguyên mới hoặc xử lý dữ liệu.

- PUT: Dùng để thay thế toàn bộ tài nguyên tại một URL cụ thể bằng dữ liệu mới.

- PATCH: Dùng để cập nhật một phần của tài nguyên.

- DELETE: Dùng để xóa tài nguyên trên server.

- OPTIONS: Dùng để truy vấn các phương thức mà server hỗ trợ cho một tài nguyên nhất định.

- CONNECT: Dùng để thiết lập một kết nối mạng trực tiếp (thường cho proxy hoặc tunneling).

- TRACE: Dùng để gỡ lỗi, trả lại nội dung request mà server nhận được.

