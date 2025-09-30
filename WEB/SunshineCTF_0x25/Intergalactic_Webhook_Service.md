# Analyst
URL: https://2025.sunshinectf.org/challenges#Intergalactic%20Webhook%20Service-11 (source will update later)
- Tạo một webhook url với url formart hợp lệ http://1.1.1.1 thì nó sẽ trả về json dưới đây:
  
  ```json
  {
    "id": "87b30699-852e-4c5a-80e6-adb02149965d",
    "status": "registered",
    "url": "http://1.1.1.1"
  }
  ````
  
- Trigger webhook <=> response server
  
  ```json
  {
    "response":"<html>\r\n<head><title>301 Moved Permanently</title></head>\r\n<body>\r\n<center><h1>301 Moved Permanently</h1></center>\r\n<hr><center>cloudflare</center>\r\n</body>\r\n</html>\r\n",
    "status":301,
    "url":"http://1.1.1.1"
  }
  ````
  
- Dòng 27 trong source code đã cho ta câu trả lời rằng `flag` sẽ nằm ở đâu trên server
  
  ```python
  class FlagHandler(BaseHTTPRequestHandler):
      def do_POST(self):
          if self.path == '/flag':
              self.send_response(200)
              self.send_header('Content-Type', 'text/plain')
              self.end_headers()
              self.wfile.write(FLAG.encode())
          else:
              self.send_response(404)
              self.end_headers()
  
  threading.Thread(target=lambda: HTTPServer(('127.0.0.1', 5001), FlagHandler).serve_forever(), daemon=True).start()
  ````
  > Exploit SSRF vul here !!!

- Test input feild `url` với url formart http://127.0.0.1 thì server trả về notification `IP "127.0.0.1" not allowed`
  
  ```python
    if ip_obj.is_private or ip_obj.is_loopback or ip_obj.is_link_local or ip_obj.is_reserved:
        return False, f'IP "{ip}" not allowed'
  ```
  > Program sử dụng các function như `is_private, is_loopback, is_link_local, is_reserved` để block ngăn SSRF
  
- Đến đây tạo một `shortlink` để né đòn xem thử https://tinyurl.com/3ry38bpb. Kết quả là ta đã lách được condition nhưng server trả về một json khá rõ ràng

  ```json
  {
    "response":"<!DOCTYPE html>\n<html>\n    <head>\n        <meta charset=\"UTF-8\" />\n        <meta http-equiv=\"refresh\" content=\"0;url='http://127.0.0.1:5001/flag'\" />\n\n        <title>Redirecting to http://127.0.0.1:5001/flag</title>\n    </head>\n    <body>\n        Redirecting to <a href=\"http://127.0.0.1:5001/flag\">http://127.0.0.1:5001/flag</a>.\n    </body>\n</html>",
    "status":308,
    "url":"https://tinyurl.com/3ry38bpb"
  }
  ````
  > Dòng 76 trong source code thì giá trị của allow_redirects=False nên là không thể redirect đến locolhost của victim để receive data được.
  
💡 Sau một hồi research thì mình tìm thấy được một keyword là `DNS rebinding`. Có thể đây là attack vector mình cần triển khai kế tiếp
  
# Solution
- Mình tìm thấy thấy một repo https://github.com/taviso/rbndr, mọi người tự đọc và tự tìm hiểu tool này nhé
  
🔎 Khi dùng tool thì mình được trả một chuỗi kết quả như thế này `7f000001.01010101.rbndr.us`. Phân tích thử thì ta thấy rằng hostname này trước đó được convert một cách có chủ ý. Cụ thể là `DNS server sẽ trả về 1 hoặc nhiều bản ghi A/AAAA (IP)` vậy nên luồng chạy của nó mình sẽ đưa vào sơ đồ phía dưới.

<img width="896" height="481" alt="image" src="https://github.com/user-attachments/assets/90a345a2-e49a-47dd-ba76-f21a07f05ff6" /></br>
> Write script and enjoy (●'◡'●)
