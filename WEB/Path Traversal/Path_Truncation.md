<h1>Root Me | <a href ="https://www.root-me.org/fr/Challenges/Web-Serveur/PHP-Path-Truncation">PHP - Path Truncation</a></h1>
<b>1. Analyst:</b><br>
<ul>
  <li>Challenge yêu cầu access to admin.</li>
  <li>Title của nó là 1 technique truncation hay mình gọi cụ thể nó là "Dot Truncation".</li>
</ul>
<b>2. Vector Attack:</b><br>
<p>
  >> Nhìn sơ qua các bạn sẽ thấy có 2 file là "admin.html" và "home.php". Page admin nó response thông báo lỗi 403 Forbidden và content trong file admin này có thể là flag chúng ta cần tìm, bây giờ mình sẽ thử qua trang home và nó trả về <b>key content</b> "Work in progress" như thế này.<br>
  <img width="813" height="279" alt="image" src="https://github.com/user-attachments/assets/c71c424a-6890-4b0a-8d3d-06e475170204" /><br>
  >> Wappalyzer cho mình biết challenge dùng language (PHP ver 5.2.17) và web server (Nginx). Url tại đây nhận tham số page và khi mình truyền payload ../../../../../../etc/passwd%00 lên thì không thể đọc được file này. Giải thích 1 chút về payload thì %00 nó là null byte, nó được xem như hành động kết thúc 1 chuỗi đồng nghĩa là sẽ cắt chuỗi ngay tại vị trí đó lúc này ta đã bypass được nhưng lỗ hổng này chỉ tồn tại trên các (PHP ver < 5.3) và vấn đề ở đây là mặc dù mọi thông tin về system và payload mình thực hiện đúng nhưng chưa phải là đi đúng hướng. <br>
  >> Đọc tài liệu từ challenge gửi thì mình biết thêm là nếu tên tệp php nó dài hơn 4096 bytes (tức là content length payload bạn đưa vào) thì sẽ dẫn đến việc truncated -> bypass. Và nó thực hiện việc truncated như sau, vd payload mình là a/../etc/passwd/././././././. Các bạn để ý sau string "a/../etc/passwd" là mình đã nối thêm string là "/.", mình viết script cho nối chuỗi và lặp lại 2050 lần => tổng số ký tự cho payload là 4113 char (> 4096 bytes). Phân tích kỹ vào payload thì ký tự "a" ở đầu string được hiểu là đường dẫn "a" nhưng nó không có thật (ko tồn tại thì chắc chắn ko đọc đc file) và trên tài liệu thì payload này lại hoạt động. Try do it !!!
  <img width="1490" height="745" alt="image" src="https://github.com/user-attachments/assets/a9a12fe7-53ff-4c51-8a02-37afdcf651e0" /><br>
   🔎Chưa đọc được file, chúng ta nên tiếp tục thử location của etc/passwd -> BINGO !!!
    <img width="748" height="740" alt="image" src="https://github.com/user-attachments/assets/bb5de62b-5e14-423c-8837-a0579a889979" /><br>
  >> Vậy có khả năng là chúng ta không thể access trực tiếp đến server thông qua đường dẫn chính xác mà phải có sự thay đổi logic gì ở đây. Tại sao lại thêm 1 ký tự bất kỳ ở phía trước đường dẫn thì mới access được. Đầu tiên mình thử "ab" thì true, "abc" cũng true. Khúc này mình sẽ thử check location của file admin.html và test thử.
    <img width="1493" height="738" alt="image" src="https://github.com/user-attachments/assets/ebff3323-7499-4364-909d-52aedb1934e8" /><br>
  >> Vậy là cách access 2 đường dẫn này hoàn toàn khác nhau và mấu chốt ở đây là thằng admin.html còn etc/passwd thực chất mình chỉ muốn test để xem thử bản chất của bài này như thế nào. Ban đầu payload a/../admin.html tổng là 15 char và payload của mình (hình trên) là 20 char, bây giờ hãy thử thêm 1 ký tự ở chỗ đường dẫn không có thật thử xem điều gì sẽ xảy ra :))
    <img width="1463" height="656" alt="image" src="https://github.com/user-attachments/assets/429aab3d-64e6-4316-a84a-9f8675a51848" /><br>
  💡 Vấn đề đã được giải quyết -> Congratz! The flag is 110V3TrUnC4T10n <br>
  >> Tóm lại muốn access và đọc được file admin.html thì sau khi ném payload truncated vào thì format (sau khi truncated string) phải có tổng độ dài chuỗi là <b>LẺ :))</b>.
  Meme -> Bài này thực sự mà không có hint cụ thể thì mình cũng bó tay 🤣🤣
</p>
