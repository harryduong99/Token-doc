
[Source](https://scotch.io/tutorials/the-ins-and-outs-of-token-based-authentication "Permalink to The Ins and Outs of Token Based Authentication ― Scotch")

# The Ins and Outs of Token Based Authentication ― Scotch

## Giới thiệu  

Token xác thực nguời dùng xuât hiện ở khắp nơi trên các trang web ngày nay. Với hầu hết các công ty web sử dụng API, token là các tốt nhất để xử lí việc xác thực đa người dùng.
Có một vài yếu tố rất quan trọng khi chọn token based authentication và ứng dụng của bạn. Các lí do chính là:

Servers có thể mở rộng và mang tính stateless
Sẵn sàng cho mobile  
Truyền xác thực tới các ứng dụng khác  
Tính bảo mật cao

## Ai dùng Token Based Authentication?  
Bất kì hệ thống API lơn hoặc web app mà bạn từng tiếp xúc đều sử dụng token. Các ứng dụng như Facebook, Twitter, Google+, GitHub đêù sử dụng tokens  
Hãy xem xét cách nó thực sự làm việc  

## Tại sao token được sinh ra  
Trước khi xem cách mà token based authentication làm việc và hiệu quả của nó, chúng ta sẽ xem cách token làm việc trong qúa khứ  

### Server Based Authentication (Phương thức truyền thống)

> Giao thức HTTP là _stateless_, điều này có nghiwax là nếu chúng ta xác thực người dùng với username và password, trong request tiếp theo, chugns ta sẽ không biết họ là ai và phải xác thực lại lần nữa.  

Các truyền thống để ứng dụng nhớ ta là ai là  **Lưu thông tin đăng nhập của người dùng trên server**. Diều này có thể thực hiện thông qua một vài cách khác nhau với session, thường là trong bộ nhớ hoặc trên ổ đĩa.

Đây là biểu đồ về quy trình làm việc của Server Based Authentication:

Khi web, app, sự nổi lên của ứng dụng di động xuất hiên, phương thức này cho thấy một số vấn đề, đặc biệt là khả năng mở rộng.

## Các vấn đề với Server Based Authentication

Một số vấn đề lớn nảy sinh với phương pháp xác thực này.

**Sessions**: Mỗi lần người dùng xác thực, máy chủ lại phải tạo bản ghi ở đâu đó trên chính nó. Điều này thường được thực hiện trên bộ nhớ, và khi có nhiều người dùng xác thực, chi phí cho máy chủ của bạn tăng lên.

**Scalability**: Khi các session được lưu trong bộ nhớ, điều này tạo ra vấn đề với khả năng mở rộng. Khi các nhà cung cấp dịch vụ đám mây của chúng ta bắt đầu sao chép các máy chủ để xử lý load ứng dụng, việc có các thông tin quan trọng trong bộ nhớ session sẽ hạn chế khả năng mở rộng của chúng ta.

**CORS**:  Vì chúng ta muốn mở rộng ứng dụng của mình để cho phép dữ liệu của chúng ta được sử dụng trên nhiều thiết bị di động, ta phải lo lắng về việc chia sẻ tài nguyên gốc (CORS). Khi sử dụng các lần gọi AJAX để lấy các tài nguyên từ một domain khác (từ điện thoại di động đến máy chủ API của chúng ta), ta có thể gặp sự cố với các yêu cầu bị cấm.  

**CSRF**: Chúng ta sẽ có sự bảo vệ chống lại [cross-site request forgery][1] (CSRF).  Người dùng dễ bị tấn công CSRF vì họ có thể đã xác thực với một trang web ngân hàng và nó có thể được tận dụng khi truy cập các trang web khác.  
Với những vấn đề này thì khả năng mở rộng là vấn đề chính, bạn nên thử một cách tiếp cận khác.  
## Cách Token Based hoạt động  
Token based authentication là **stateless**. Chúng ta không lưu bất cứ thông tin người dùng nào trên server hay trên session.

Nó sẽ xử lí nhiều vấn đề với việc phải lưu thông tin trên máy chủ  

> Không có thông tin session có nghĩa là ứng dụng của bạn có thể mở rộng quy mô và thêm nhiều máy hơn nếu cần mà không phải lo lắng về nơi người dùng đăng nhập.  

Mặc dù việc triển khai này có thể thay đổi, nhưng luôn gồm các điều sau:  

1. Người dùng yêu cầu truy cập với Username / Password
2. Ứng dụng xác thực thông tin đăng nhập 
3. Ứng dụng cung cấp token đã được đăng ký cho máy khách
4. Máy khách lưu trữ token đó và gửi nó kém theo  mỗi request
5. Server xác thực token và phản hồi lại với data

**Mỗi request sẽ yêu cầu token**. Token được gửi trong HTTP header và điều đó giữu nguyên tính chất stateless của HTTP request. Chúng ta cũng cần cài đặt server để chấp nhận request từ tất cả các miền sử dụng `Access-Control-Allow-Origin: *`. diều thú vị về việc chỉ định `*` trong ACAO header là nó không cho phép yêu cầu cung cấp thông tin xác thực như trong HTTP authentication, xác thực client-side SSL , hay cookies.

Dưới đây là hình ảnh minh họa để giải thích quá trình:

Một khi chúng ta đã xác thực thông tin của mình và có token, chúng ta có thể thực hiện nhiều việc với token này.

Chúng ta thậm chí có thể tạo một permission based token và truyền nó vào ứng dụng thứ 3 (như là một mobile mà ta muốn sử dụng), và nó sẽ có quyền truy cập vào dữ liệu của chúng ta -- **nhưng chỉ thông tin mà ta cho phép với token cụ thể**.

## Các lợi ích của token  

### Stateless and Scalable

Các token được lưu trữ ở phía client. Hoàn toàn là stateless và sẵn sàng thay đổi quy mô. Cân bằng tải của chúng ta có thể chuyển người dùng đến bất kỳ máy chủ nào của chúng ta vì không có thông tin trạng thái hoặc phiên nào ở bất kỳ đâu.

Nếu chúng ta giữ thông tin về session trên người dùng đã đăng nhập, điều này sẽ yêu cầu ta tiếp tục gửi người dùng đó đến máy chủ họ đã đăng nhập (được gọi là tính liên thông session).

Điều này đem đến vấn đề kể từ việc một số người dùng sẽ bị buộc phải cùng một máy chủ và điều này có thể mang lại một điểm lưu lượng truy cập lớn.

Cũng đừng lo lắng! Những vấn đề đó đã biến mất với token vì bản thân token mang dữ liệu của người dùng đó.  

### Bảo mật  

Token,chú không phải cookie, được gửi theo mọi request và vì không có cookie nào được gửi, điều này giúp ngăn chặn các cuộc tấn công CSRF. Ngay cả khi việc triển khai cụ thể của bạn lưu trữ token trong một cookie ở phía client thì cookie cũng chỉ là một cơ chế lưu trữ thay vì một cơ chế xác thực. Không có thông tin dựa trên session để thao tác vì chúng ta không có session!  

Token cũng hết hạn sau một khoảng thời gian nhất định, do đó, người dùng sẽ được yêu cầu đăng nhập lại. Điều này giúp chúng ta luôn an toàn. Ngoài ra còn có khái niệm về [thu hồi token] [2] cho phép chúng ta vô hiệu hóa token cụ thể và thậm chí là một nhóm token dựa trên cùng một sự cho phép.  

### Khả năng mở rộng (Bạn của bạn và permissions)

Tokens sẽ cho phép chúng ta xây dựng các ứng dụng mà có thể chia sẻ quyền với nhau. Ví dụ: chúng ta đã liên kết các tài khoản xã hội ngẫu nhiên với các tài khoản chính của chúng ta như Facebook hoặc Twitter.

Khi chúng ta đăng nhập vào Twitter thông qua một dịch vụ (gọi là Buffer), chúng ta đang cho phép Buffer đăng lên luồng Twitter của mình.

Bằng cách sử dụng token, đây là cách chúng ta **cung cấp quyền chọn lọc cho các ứng dụng của bên thứ ba**. Chúng ta thậm chí có thể xây dựng API của riêng mình và cung cấp các token quyền đặc biệt nếu người dùng của chúng ta muốn cấp quyền truy cập vào dữ liệu của họ cho một ứng dụng khác.  

### Multiple Platforms and Domains

Chúng ta đã nói một chút về CORS trước đó. Khi ứng dụng và dịch vụ của ta mở rộng, chúng ta sẽ cần cung cấp quyền truy cập vào tất cả các loại thiết bị và ứng dụng (ứng dụng của chúng ta chắc chắn sẽ trở nên phổ biến!).

Khi API của chúng ta chỉ phân phối dữ liệu, chúng ta cũng có thể đưa ra lựa chọn phân phát nội dung qua CDN. Điều này giúp loại bỏ các vấn đề mà CORS mang lại sau khi chúng ta thiết lập nhanh cấu hình header cho ứng dụng của mình.
    
    
    Access-Control-Allow-Origin: *
    

Dữ liệu và tài nguyên của chúng ta sẵn có cho các yêu cầu từ bất kỳ miền nào ngay bây giờ **miễn là người dùng có token hợp lệ
.  


### Standards Based

Khi tạo một token, bạn có một vài tùy chọn. Chúng ta sẽ đào sâu hơn vào chủ đề này khi chúng ta bảo mật một API trong một bài viết tiếp theo, nhưng tiêu chuẩn để sử dụng sẽ là [JSON Web Tokens] [3].  

 Trình gỡ lỗi tiện ích và biểu đồ thư viện này hiển thị hỗ trợ cho các JSON Web Tokens. Bạn có thể thấy rằng nó có một số lượng lớn hỗ trợ trên nhiều ngôn ngữ. Điều này có nghĩa là bạn có thể thực sự chuyển đổi cơ chế xác thực của mình nếu bạn chọn làm như vậy trong tương lai!  

## Phần kết luận  

Đây chỉ là xem xét việc làm thế nào và tại sao của token based authentication. Như mọi khi trong thế giới của bảo mật, có nhiều, nhiều, nhiều, nhiều (quá nhiều?) Nhiều hơn cho mỗi chủ đề và nó thay đổi theo từng trường hợp sử dụng. Thậm chí, chúng tôi cũng nghiên cứu một số chủ đề về khả năng mở rộng xứng đáng với cuộc trò chuyện của riêng họ.

Đây là một overview nhanh ở cấp độ cao, vì vậy xin vui lòng chỉ ra bất cứ điều gì đã bị bỏ lỡ hoặc bất kỳ câu hỏi nào bạn có về vấn đề này.  
Trong bài việt tiếp theo, chugns ta sẽ tìm hiểu về Json Web Token.  

**Edit #1** Adding ACAO header info and CSRF clarifications (thanks to [Emily Stark][5] for the article info) 

[1]: https://en.wikipedia.org/wiki/Cross-site_request_forgery
[2]: https://tools.ietf.org/html/rfc7009
[3]: https://scotch.io/tutorials/the-anatomy-of-a-json-web-token
[4]: https://leanpub.com/mean-machine
[5]: https://twitter.com/estark37

  
