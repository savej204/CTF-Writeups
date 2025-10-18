# Analyst
- Để ý title web này thì mình đoán được rằng đây là 1 lỗi về Authentication (IDOR) của chall này.
  
<img width="1919" height="1009" alt="anh1" src="https://github.com/user-attachments/assets/0ae8290e-0cea-44b0-afcd-f7002c6394cf" />

- Sau khi chạy request login thành công thì điểm trigger rõ ràng nhất là ở `/api/profile?id=`

# Solution
- Mình sẽ truyền thử param id = 1 trước xem kết quả sẽ như thế nào và đúng là như vậy. Response trả về profile của Admin và bài này nó nhàn ở chỗ là không cần chạy brute force id :v
  
  <img width="1490" height="741" alt="image" src="https://github.com/user-attachments/assets/688cb5c4-552c-4692-9e34-1e1bae5fb39e" />

  > Xem qua từng field thì có vẻ không tồn tại flag ở đây và mình quyết định login thẳng vào.
  
- Vấn đề ở đây là ta sẽ login như thế nào khi không có passwd và tất nhiên là trang chứa thông tin user bất kỳ đều có chức năng reset passwd
  
  <img width="1484" height="741" alt="image" src="https://github.com/user-attachments/assets/fc3e17fb-f9bc-4baa-a5ad-fc2cfdb13159" />
  
  > Phần xử lý reset passwd đã không validate chặt chẽ dẫn đến việc mình có thể thay đổi passwd của 1 user bất kỳ với việc chỉ cần dựa vào param id và mục tiêu của mình là anh Admin dth :))
  
 🔎 Login thành công không có nghĩa là vào được vì lúc này URL là public nên sẽ có rất nhiều CTFer chạy request reset passwd. Có thể tự triển khai script giúp login tiện hơn.
 - Vào được profile thì nổ FLAG 🔥🔥

  <img width="1916" height="962" alt="image" src="https://github.com/user-attachments/assets/b7e7f7d3-5970-4e0b-bd7c-d91adf3c900d" />


 
