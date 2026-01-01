# SQLI-1
- Nhìn vào blacklist thì blocked khá đầy đủ nhưng có vẻ như 'union' sẽ dùng được cho thử thách này
  
  <img width="926" height="165" alt="image" src="https://github.com/user-attachments/assets/7a7778cf-6030-48a7-9a0d-0cc1652979b3" />

- Fuzzing thử để check response server

  <img width="1494" height="754" alt="image" src="https://github.com/user-attachments/assets/4db5ac21-03aa-47e4-8522-817839e73fa5" />
  
  >Thông báo "process error" server trả về vì '--' nằm trong blacklist

  <img width="1500" height="758" alt="image" src="https://github.com/user-attachments/assets/7baa1c82-3197-4c79-8233-33d1d35b4ed4" />

  >Khúc này nó báo lỗi trực tiếp từ SQL, trong quá trình desgine chall thì mình quên xử lý vấn đề này. Mong các bạn thông bạn ^^.
  >Chỗ này nó server sẽ trả về thông báo lỗi chung chung như hình trước đó là "process error"

- Follow theo code thì thấy được rằng điểm tiêm của chúng ta thì dường như output sẽ nhảy đến chỗ khác chứ không phải là tại chính vị trí mình vừa tiêm
  
  <img width="980" height="742" alt="image" src="https://github.com/user-attachments/assets/15992433-a3f5-4b05-b5ad-4c069a1cff28" />

  >Line 17 ở param `$query` đầu tiên sẽ nhận input là name của 1 user. Param `$query` thứ 2 làm nhiệm vụ là data từ column (username,mssv) với id được lấy từ `row[0]` (trong query1)

- Phân tích một hồi thì thấy rằng nó thuộc loại Routed SQli. Dưới đây là payload của mình
  >`'union select+0x27756e696f6e2073656c656374206e756c6c2c656e6459656172323032352066726f6d20666c61677323,1#`.
  
  >Giải thích 1 chút ở chỗ payload dùng bypass thì `select` trong blacklist có đề cập nhưng format của nó là `/select\s+.*,/i` tức là `select -> khoảng trắng -> bất cứ thứ gì -> dấu phẩy`.
  >Ta có thể dùng "+" thêm sau select để bypass WAF vì nó đúng chuẩn 1 syntax tương tự như `select 1,2` thông thường

