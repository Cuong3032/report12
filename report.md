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

- Phân loại Header:
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

#### HTTP header

- HTTP Headers (các tiêu đề HTTP) cho phép client (máy khách) và server (máy chủ) truyền thêm thông tin bổ sung (metadata) cùng với một HTTP request (yêu cầu) hoặc response (phản hồi). Chúng là phần cốt lõi để định hình cách thức giao tiếp và xử lý dữ liệu trên web.
- Cú pháp:

	- Trong HTTP/1.x: tên header không phân biệt hoa thường, theo sau là dấu `:` và giá trị (ví dụ: `Allow: POST`).

	- Trong HTTP/2+: headers hiển thị dưới dạng chữ thường, một số pseudo-headers có tiền tố `:` (ví dụ: `:status: 200`).
- Headers tuỳ chỉnh: Trước đây thường dùng tiền tố X-, nhưng đã bị deprecate từ năm 2012 (RFC 6648).
 - Phân Loại Theo Ngữ Cảnh (Classification by Context)
   	- Request Headers: Chứa thông tin về tài nguyên cần lấy hoặc về phía client đang yêu cầu. Ví dụ: `User-Agent`, `Accept-Language`.
   	- Response Headers: Chứa thông tin bổ sung về phản hồi, vị trí tài nguyên, hoặc về phía server. Ví dụ: `Server`, `Location`.
   	- Representation Headers: Mô tả về phần thân dữ liệu (body) của tài nguyên (ví dụ: định dạng, nén). Ví dụ: `Content-Type`, `Content-Encoding`.
   	- Payload Headers: Chứa thông tin độc lập với biểu diễn về dữ liệu payload, như độ dài nội dung để vận chuyển. Ví dụ: `Content-Length`.
- Phân Loại Theo Cách Xử Lý Proxy:
  	- End-to-end headers: Phải được truyền đến người nhận cuối cùng (server hoặc client). Các proxy trung gian phải chuyển tiếp nguyên vẹn và bộ nhớ đệm (cache) phải lưu trữ chúng.
  	- Hop-by-hop headers: Chỉ có ý nghĩa cho một kết nối đơn lẻ (transport-level connection). Không được chuyển tiếp qua proxy hoặc lưu vào cache.
  	  	- Ví dụ điển hình: `Connection`, `Keep-Alive`.
- Các Nhóm Chức Năng Chính (Functional Groups):
  	- Xác thực (Authentication): Dùng để xác minh danh tính người dùng hoặc client.
  	  - `Authorization`: Chứa thông tin xác thực (credentials) để gửi lên server.
  	  - `WWW-Authenticate`: Định nghĩa phương thức xác thực server yêu cầu.
  	- Bộ nhớ đệm (Caching): Kiểm soát việc lưu trữ tài nguyên để tối ưu hiệu suất.
		- Cache-Control: Chỉ thị quan trọng nhất cho cơ chế caching (ví dụ: no-cache, max-age).
  		- Expires: Thời điểm tài nguyên hết hạn.
		- ETag: Chuỗi định danh phiên bản của tài nguyên (dùng để so sánh sự thay đổi).
	- Cookies: Quản lý trạng thái phiên làm việc (state).
		- Cookie: Client gửi cookie đã lưu trữ lên server.
		- Set-Cookie: Server gửi cookie về để client lưu lại.
	- Đàm phán nội dung (Content Negotiation): Client nói cho server biết mình muốn nhận định dạng dữ liệu nào.
		- Accept: Loại dữ liệu (MIME type) client có thể hiểu (ví dụ: application/json).
		- Accept-Language: Ngôn ngữ ưu tiên của người dùng.
		- Accept-Encoding: Thuật toán nén được hỗ trợ (ví dụ: gzip, br).
	- CORS (Cross-Origin Resource Sharing): Kiểm soát bảo mật khi truy cập tài nguyên từ một nguồn (domain) khác.
		- Access-Control-Allow-Origin: Chỉ định domain nào được phép truy cập tài nguyên.
		- Origin: Nguồn gốc của request.
	- Thông tin phần thân (Message Body Information): 
		- Content-Type: Loại nội dung thực tế trả về (ví dụ: text/html; charset=utf-8).
		- Content-Length: Kích thước của nội dung (tính bằng byte).
	- Ngữ cảnh yêu cầu (Request Context)
		- Host: Tên miền và cổng của server (bắt buộc trong HTTP/1.1).
		- User-Agent: Thông tin về trình duyệt/ứng dụng của client.
		- Referer: Địa chỉ của trang web trước đó dẫn tới trang hiện tại.
	- Điều hướng (Redirects)
		- Location: URL mới mà client nên chuyển hướng tới (thường dùng với mã 3xx).
	- Bảo mật (Security)
		- Content-Security-Policy (CSP): Kiểm soát các nguồn tài nguyên mà trình duyệt được phép tải.
		- Strict-Transport-Security (HSTS): Bắt buộc sử dụng HTTPS.
#### HOST header

- Host Header là một tiêu đề yêu cầu (Request Header) bắt buộc trong giao thức HTTP/1.1.
- Chức năng chính: Nó chỉ định chính xác tên miền (domain name) và số cổng (port number - tùy chọn) của máy chủ mà client đang muốn gửi yêu cầu đến.
- Vai trò quan trọng: Giúp máy chủ xác định được website hoặc ứng dụng nào client muốn truy cập, đặc biệt khi máy chủ đó lưu trữ nhiều website khác nhau trên cùng một địa chỉ IP (cơ chế Virtual Hosting).

- Quy Tắc Sử Dụng (Usage Rules)
	- Bắt buộc trong HTTP/1.1: Mọi yêu cầu HTTP/1.1 phải có chứa header Host.
	- Duy nhất: Mỗi yêu cầu chỉ được phép có một header Host.
	- Xử lý lỗi:
		- Nếu yêu cầu HTTP/1.1 thiếu header Host, máy chủ sẽ trả về lỗi 400 Bad Request.
		- Nếu yêu cầu có nhiều hơn một header Host, máy chủ cũng sẽ trả về lỗi 400 Bad Request.
  ##### Tại Sao Host Header Lại Quan Trọng? (Virtual Hosting)
- Trước khi có Host header (trong HTTP/1.0), một máy chủ vật lý chỉ có thể phục vụ một tên miền duy nhất cho mỗi địa chỉ IP. Điều này rất lãng phí địa chỉ IP.
- Host header giải quyết vấn đề này bằng cách cho phép Virtual Hosting (Lưu trữ ảo dựa trên tên):
	- Nhiều tên miền (ví dụ: a.com, b.com, c.com) cùng trỏ về một địa chỉ IP duy nhất của server.
	- Khi request đến, server sẽ đọc giá trị trong header Host.
	- Dựa vào giá trị đó, server biết phải chuyển request đến mã nguồn (source code) của website nào để xử lý.
#### Cookie và flag 

##### HTTP cookie (hay web cookie, browser cookie) là một mẩu dữ liệu nhỏ mà máy chủ gửi đến trình duyệt của người dùng. Trình duyệt có thể lưu trữ nó và gửi lại cùng với các yêu cầu (request) tiếp theo lên cùng máy chủ đó. Điều này giúp web có thể ghi nhớ thông tin trạng thái (vì mặc định HTTP là giao thức không trạng thái - stateless).
- Cookie được sử dụng với 3 mục đích chính:
  - Quản lý phiên (Session management): Lưu trạng thái đăng nhập, giỏ hàng, điểm số game, hoặc bất kỳ thông tin nào cần nhớ trong phiên làm việc của người dùng.
	- Cá nhân hóa (Personalization): Lưu các cài đặt ưu tiên của người dùng như ngôn ngữ hiển thị, giao diện (theme), v.v.
	- Theo dõi (Tracking): Ghi lại và phân tích hành vi người dùng.
- Tạo và Quản lý Cookie
	- Tạo Cookie: Máy chủ gửi header Set-Cookie trong phản hồi (response) để yêu cầu trình duyệt lưu cookie.

			Ví dụ: Set-Cookie: id=a3fWa; Expires=Thu, 31 Oct 2021 07:28:00 GMT;
	- Gửi Cookie: Với mỗi request tiếp theo đến cùng domain, trình duyệt sẽ tự động gửi kèm cookie trong header Cookie.
	- Vòng đời:
		- Session cookies: Bị xóa khi đóng trình duyệt (nếu không set Expires hay Max-Age).
		- Permanent cookies: Tồn tại cho đến khi hết hạn (theo thời gian định sẵn).
		- Xóa Cookie: Để xóa ngay lập tức, server có thể set lại cookie đó với thời gian hết hạn trong quá khứ.
	- JavaScript: Có thể tạo/truy cập cookie qua document.cookie (trừ khi cookie có cờ HttpOnly).
 - Bảo mật: Vì cookie có thể chứa thông tin nhạy cảm, cần chú ý các thuộc tính bảo mật:

		Secure: Chỉ gửi cookie qua kết nối mã hóa (HTTPS), giúp tránh bị nghe lén.

		HttpOnly: Ngăn chặn JavaScript (như document.cookie) truy cập vào cookie. Điều này rất quan trọng để chống lại các cuộc tấn công Cross-site scripting (XSS).

		SameSite: Kiểm soát việc cookie có được gửi cùng với các request từ trang web khác hay không (giúp chống tấn công CSRF).
- Phạm vi (Scope)

		Domain: Xác định cookie sẽ được gửi đến host nào (ví dụ: nếu set cho mozilla.org thì các subdomain như developer.mozilla.org cũng nhận được).

		Path: Xác định đường dẫn URL nào thì cookie mới được gửi đi (ví dụ: /docs).
  ##### Các flag của Cookie
- Security flag:
  	- HttpOnly: Cờ này chỉ thị cho trình duyệt rằng cookie không được phép truy cập thông qua các API phía client như JavaScript (document.cookie).
    	- Nếu website bị dính lỗi XSS (hacker chèn được mã độc JS vào trang), đoạn mã đó sẽ cố gắng đọc cookie session để gửi về máy hacker thì HttpOnly ngăn chặn hacker đọc được nội dung cookie. Dù hacker có thể thực thi lệnh trên trình duyệt nạn nhân, hắn không thể lấy được Session ID để mạo danh nạn nhân trên máy khác.
    	- `Lưu ý`: HttpOnly không chặn được XSS, nó chỉ giảm thiểu thiệt hại (mitigation). Bạn vẫn cần fix lỗi XSS trong code (sanitize input).
	- Secure: Trình duyệt sẽ chỉ gửi cookie này lên server nếu request được thực hiện qua giao thức mã hóa HTTPS. Cookie sẽ không bao giờ được gửi qua kết nối HTTP không an toàn (trừ localhost).
	- SameSite: Kiểm soát xem cookie có được gửi đi hay không khi người dùng truy cập từ một trang web khác. Các giá trị của SameSite:
		- Strict: Cookie chỉ được gửi nếu người dùng đang ở chính trang web đó. (An toàn nhất, nhưng bất tiện nếu người dùng nhấn link từ email).
 		- Lax (Mặc định): Cookie được gửi khi người dùng điều hướng đến trang web của bạn (ví dụ: nhấn vào link), nhưng không gửi kèm các yêu cầu phụ (như ảnh, iframe) từ web khác.
  		- None: Cookie được gửi trong mọi trường hợp (kể cả từ web khác). Bắt buộc phải dùng kèm với Secure.
- Scope Flags:
  	- Domain: Xác định tên miền (và các tên miền phụ - subdomain) được phép nhận cookie.
  	- Path: Giới hạn cookie chỉ hoạt động trong một đường dẫn cụ thể.
- Lifetime Flags: Nếu không thiết lập các cờ này, cookie sẽ là Session Cookie (Cookie phiên) và sẽ tự xóa khi tắt trình duyệt.
  	- Expires: Đặt một ngày/giờ cụ thể để cookie hết hạn.
  	- Max-Age: Đặt số giây cookie được tồn tại (được ưu tiên hơn Expires).
- Cookie Prefixes: Để tăng cường bảo mật, bạn có thể đặt tên cookie bắt đầu bằng các từ khóa đặc biệt để ép buộc trình duyệt tuân thủ quy tắc:
  	- `__Host-`: (Ví dụ: `__Host-SessionID`) Yêu cầu cookie phải có cờ Secure, Path=/, và không được có thuộc tính Domain. Đây là loại cookie an toàn nhất.
  	- `__Secure-`: Yêu cầu cookie phải có cờ Secure.
##### Lí do website có thể lưu phiên đăng nhập của người dùng vì Website lưu phiên đăng nhập bằng cách gán cho trình duyệt một mã session (thường qua cookie), và dựa vào mã đó để nhận diện người dùng ở các request sau.

- Nguyên lý: "Tấm vé giữ xe"
	- Hãy tưởng tượng việc đăng nhập giống như bạn đi gửi xe ở siêu thị:
		- Gửi xe: Bạn đưa xe cho bảo vệ (Nhập tên đăng nhập/mật khẩu).
		- Nhận vé: Bảo vệ cất xe vào bãi và đưa cho bạn một tấm vé có mã số (Server tạo Session ID và gửi về Browser).
		- Lấy xe: Khi bạn quay lại lấy xe, bạn không cần tả lại chiếc xe, bạn chỉ cần đưa tấm vé ra (Browser gửi lại Session ID). Bảo vệ nhìn số trên vé và biết xe nào là của bạn.
##### Status code

- Status code cho biết liệu 1 yêu cầu http cụ thể có được hoàn thành thành công hay không và được chia thành 5 loại:
	- Informational responses (100 – 199)
   	- Successful responses (200 – 299)
   	- Redirection messages (300 – 399)
   	- Client error responses (400 – 499)
   	- Server error responses (500 – 599)
- Informational responses:
  	- `100 Continue`: phản hồi này cho biêt client nên tiếp tục yêu cầu hoặc bỏ qua phản hồi nếu yêu cầu đã kết thúc.
  	- `101 Switching Protocols`: Server đồng ý chuyển đổi giao thức theo yêu cầu của client (ví dụ: chuyển từ HTTP sang WebSocket).
  	- `102 Processing (WebDAV)`: Server đã nhận và đang xử lý yêu cầu nhưng chưa có phản hồi (tránh timeout).
  	- `103 Early Hints`: Dùng để trả về các header trước khi phản hồi chính sẵn sàng (giúp preload tài nguyên).
- Successful responses
  	- `200 OK`: Thành công tiêu chuẩn (GET, HEAD, PUT, POST).
  	- `201 Created`: Thành công và đã tạo ra tài nguyên mới (thường sau lệnh POST/PUT).
  	- `202 Accepted`: Đã nhận yêu cầu để xử lý nhưng chưa hoàn thành (xử lý bất đồng bộ/background job).
  	- `203 Non-Authoritative Information`: Thành công, nhưng thông tin trả về là từ một nguồn khác (proxy) chứ không phải server gốc.
  	- `204 No Content`: Thành công nhưng không trả về nội dung gì (thường dùng cho DELETE hoặc SAVE).
  	- `205 Reset Content`: Yêu cầu client reset lại document view (ví dụ: xóa trắng form sau khi gửi).
  	- `206 Partial Content`: Trả về một phần tài nguyên (dùng khi tải file lớn, resume download hoặc streaming video).
  	- `207 Multi-Status (WebDAV)`: Thông báo về nhiều trạng thái cho nhiều hoạt động khác nhau cùng lúc (XML body).
  	- `208 Already Reported (WebDAV)`: Các thành viên của một liên kết DAV đã được liệt kê trước đó, không cần lặp lại.
  	- `226 IM Used`: Server đã hoàn thành yêu cầu GET và phản hồi là sự biểu diễn kết quả của một hoặc nhiều thao tác instance-manipulation.
- Redirection messages
  	- `300 Multiple Choices`: Có nhiều lựa chọn cho tài nguyên (ví dụ: nhiều định dạng video, nhiều ngôn ngữ), client cần chọn một.
  	- `301 Moved Permanently`: Tài nguyên đã chuyển vĩnh viễn sang URL mới. Các trình duyệt và bot tìm kiếm (SEO) sẽ cập nhật sang link mới này.
  	- `302 Found`: Tài nguyên tạm thời chuyển sang URL khác.
  	- `304 Not Modified`: Tài nguyên không thay đổi kể từ lần cuối truy cập. Trình duyệt có thể dùng bản lưu trong bộ nhớ đệm (cache) thay vì tải lại, giúp tiết kiệm băng thông.
- Client error responses
  	- `400 Bad Request`: Server không hiểu yêu cầu do cú pháp sai.
  	- `401 Unauthorized`: Yêu cầu cần xác thực danh tính (cần đăng nhập).
  	- `403 Forbidden`: Client đã được nhận diện (đăng nhập rồi) nhưng không có quyền truy cập tài nguyên này.
  	- `404 Not Found`: Server không tìm thấy tài nguyên được yêu cầu (Lỗi phổ biến nhất khi gõ sai URL hoặc link bị chết).
  	- `429 Too Many Requests`: Người dùng gửi quá nhiều yêu cầu trong một khoảng thời gian nhất định (Rate limiting).
- Server error responses
  	- `500 Internal Server Error`: Lỗi chung chung, server gặp sự cố bất ngờ và không biết cụ thể là gì.
  	- `502 Bad Gateway`: Server hoạt động như một gateway hoặc proxy và nhận được phản hồi không hợp lệ từ server phía trên (upstream).
  	- `503 Service Unavailable`: Server chưa sẵn sàng xử lý (thường do quá tải hoặc đang bảo trì).
  	- `504 Gateway Timeout`: Server (như một gateway) không nhận được phản hồi kịp thời từ server phía trên.
### 1.3. Tìm hiểu cấu trúc của 1 URL





