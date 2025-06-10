# Vitapay
# Mô hình tổng quan 
![image](https://github.com/user-attachments/assets/d57d5b7c-7929-445b-9516-e10422129f12)

# kiến trúc thành phần 
### jenkins 
- hệ thống sẽ bao gồm 2 Jenkins
  + Jenkins Master : deploy từ docker
```
docker run -d   --name jenkins   -p 8080:8080   -v jenkins_home:/var/jenkins_home   -v /opt/shared-code:/var/jenkins_data   jenkins/jenkins:lts
```
- Jenkins master sẽ kết nối đến agent bằng ssh agent nên sẽ không cần mở cổng 50000 nếu sử dụng JNLP agent sẽ cần mở cổng 50000 trên master để slave kết nối được đến
- tạo volume để lưu file cầu hính jenkins khi jenkins bị stop hay xóa khởi động lại sẽ không mất dữ liệu
- 
