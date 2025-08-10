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
  >> Wappalyzer cho mình biết challenge dùng language (PHP ver 5.2.17) và web server (Nginx). Url tại đây nhận tham số page và khi mình đưa payload ../../../../../../etc/passwd%00 lên thì không thể đọc được file này. Giải thích 1 chút về payload thì %00 nó là null byte, nó được xem như hành động kết thúc 1 chuỗi đồng nghĩa là sẽ cắt chuỗi ngay tại vị trí đó lúc này ta đã bypass được nhưng lỗ hổng này chỉ tồn tại trên các (PHP ver < 5.3) và vấn đề ở đây là mặc dù mọi thông tin về system và payload mình thực hiện đúng nhưng chưa phải là đi đúng hướng. <br>
  >> Mình biết thêm một điều rằng là nếu tên tệp php nó dài hơn 4096 bytes (tức là content length payload bạn đưa vào) thì sẽ dẫn đến việc truncated -> bypass. Và nó thực hiện việc truncated như sau, vd payload mình là ../etc/passwd/././././././. Các bạn để ý sau string "passwd" là mình đã nối thêm string là "/.", mình viết script cho nối chuỗi và lặp lại 2050 lần => tổng số ký tự cho payload là 4113 char (> 4096 bytes). Try do it !!!!
    
</p>
