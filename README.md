# Vitapay
# Mô hình tổng quan 
![image](https://github.com/user-attachments/assets/d57d5b7c-7929-445b-9516-e10422129f12)

# kiến trúc thành phần 
### jenkins 
- hệ thống sẽ bao gồm 2 Jenkins
###### Jenkins Master : deploy từ docker
```
docker run -d   --name jenkins   -p 8080:8080   -v jenkins_home:/var/jenkins_home   -v /opt/shared-code:/var/jenkins_data   jenkins/jenkins:lts
```
- Jenkins master sẽ kết nối đến agent bằng ssh agent nên sẽ không cần mở cổng 50000 nếu sử dụng JNLP agent sẽ cần mở cổng 50000 trên master để slave kết nối được đến
- tạo volume để lưu file cầu hình jenkins khi jenkins bị stop hay xóa khởi động lại sẽ không mất dữ liệu
- Jenkins lắng nghe trên port 8080
- cấu hình jenkins master :
    + cấu hình ssh tới slave
    + cấu hình ssh tới các server service
    + cấu hình credential.credentials " SSH Username with private key " ssh tới các server
    + cấu hình credentials Username with password để clone, pull code từ git
    + cấu hình Node  : thêm node vào master, user dùng để ssh

###### Jenkins slave 
- Là server chạy ubuntu được cài ansible, maven,  java để pull build deploy code
- cần cấu hình ssh tới các server service

###### Luồng hoạt động của ssh
- SSH sẽ hoạt động dựa trên private key và public key để ssh không mật khẩu
- tạo ssh key :
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
- sau khi tạo hệ thống sẽ sinh ra public key và private key trên folder của user đang tạo
```
/home/root/.ssh/
```
- nếu đang đứng ở root
- sau khi tạo xong sẽ copy key trong public key copy vào file authorized_keys trên server cần ssh đến
- cặp key private và public ở 2 servre sẽ match với nhau theo profile ở user tạo key để ssh không mật khẩu

##### tạo luồng kết nối từ slave tới gitlab
- slave cần kết nối tới gitlab để clone, pull code
- sử dụng credentials Username with password để clone, pull code
- tạo ssh key và access token trên gitlab để authencation
      + vào preferen --> SSH Keys --> copy public key --> add key
      + tạo access token --> mã access token
- mã access token sẽ dùng cho việc cấu hình credential Username with password với password là mã access token, user là user name của gitlab
- khi clone pull code từ gitlab sẽ đăng nhập vào bằng user và password credential.
```
 withCredentials([usernamePassword(credentialsId: 'https-creds-id', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')])
```
##### Jenkins file 
- sau khi cấu hình xong jenkins và luồng kết nối sẽ tiến hành viết jenkins file
- Jenkins file có thể để ở trên git hoặc trong chính các project của jenkins
- các kiểu tạo project sẽ có cách đặt Jenkins file khác nhau
