Cài đặt Ftp server  trên Centos với Vsftpd.


FTP (viết tắt của File Transfer Protocol dịch ra là "Giao thức truyền tập tin") thường được dùng để trao đổi tập tin qua mạng lưới truyền thông dùng giao thức TCP/IP (chẳng hạn như Internet - mạng ngoại bộ - hoặc intranet - mạng nội bộ).
Hoạt động của FTP cần có hai máy tính, một máy chủ và một máy khách). Máy chủ FTP, dùng chạy phần mềm cung cấp dịch vụ FTP, gọi là trình chủ, lắng nghe yêu cầu về dịch vụ của các máy tính khác trên mạng lưới. Máy khách chạy phần mềm FTP dành cho người sử dụng dịch vụ, gọi là trình khách, thì khởi đầu một liên kết với máy chủ. Một khi hai máy đã liên kết với nhau, máy khách có thể xử lý một số thao tác về tập tin, như tải tập tin lên máy chủ, tải tập tin từ máy chủ xuống máy của mình, đổi tên của tập tin, hoặc xóa tập tin ở máy chủ v.v. Vì giao thức FTP là một giao thức chuẩn công khai, cho nên bất cứ một công ty phần mềm nào, hay một lập trình viên nào cũng có thể viết trình chủ FTP hoặc trình khách FTP. 
Hầu như bất cứ một nền tảng hệ điều hành máy tính nào cũng hỗ trợ giao thức FTP. Điều này cho phép tất cả các máy tính kết nối với một mạng lưới có nền TCP/IP, xử lý tập tin trên một máy tính khác trên cùng một mạng lưới với mình, bất kể máy tính ấy dùng hệ điều hành nào (nếu các máy tính ấy đều cho phép sự truy cập của các máy tính khác, dùng giao thức FTP). Hiện nay trên thị trường có rất nhiều các trình khách và trình chủ FTP, và phần đông các trình ứng dụng này cho phép người dùng được lấy tự do, không mất tiền.



Sau đây là cách để xây dựng một máy chủ FTP.
(Đây chỉ là bài LAB dựng trên VMWARE của mình, để triển khai trên các thiết bị khác tương tự).

##### I. Xem có những gì cần chuẩn bị nào :

###### 1. Một máy chủ VMWARE  chạy hệ điều hành Centos 6.5 (hoặc hơn) với các thông số như sau.
<img s rc="http://prntscr.com/59zdrn">
###### 2.Một trình duyệt nào đó(ở đây mình dùng Firefox) và phần mềm Winscp với chức năng FTP.

##### II. Bắt đầu quá trình cài đặt :

- B1: Cập nhật hệ thống:
```
yum update -y
```
- B2: Tải và cài đặt các gói:
```
yum install vsftpd ftp -y 
sudo service vsftpd start
```
- B3: Cấu hình file config vsftpd . Sửa các thông số của file /etc/vsftps.conf như sau:
```
## Set to "NO" ##
anonymous_enable=NO

## Uncomment ##
ascii_upload_enable=YES
ascii_download_enable=YES

## Uncomment - Enter your Welcome message - This is optional ##
ftpd_banner=Welcome to UNIXMEN FTP service.

## Add at the end of this  file ##
use_localtime=YES
```

-B4 : Mở port ftp (mặc định là port 21):
```
iptables -I INPUT  -m tcp -p tcp -m conntrack --ctstate NEW --dport 21 -j ACCEPT
```
- B5: Khởi động lại vsftpd:
```
service vsftpd start
chkconfig vsftpd on
```
- B6: Bởi mặc định tài khoản root có tất cả quyền. Như vậy không an toàn, ở đây mình tạo 1 tài khoản riềng để sử dụng:
```
useradd longnt
passwd  longnt
```
- B6 : Kết nối đến FTP server:
```
[root@ftp longnt]# ftp 192.168.10.5
Connected to 192.168.10.5 (192.168.10.5).
220 Welcome to  FTP service.
Name (192.168.10.5:root): longnt
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

Như vậy, ta đã cài đặt thành công FTP trên Centos.

Trong quá trình cài đặt đôi khi ta gặp lỗi 
```
500 OOPS: cannot change /home/longnt”
```
thì đây là cách sửa :
```
setsebool -P ftp_home_dir on
```
- B7 :Kết nối từ client lên server xem sao:
Từ thư mục có dữ liệu cần đưa lên
```
ftp 192.168.10.5
```
 Đặng nhập vào và sử dụng lệnh
```
 put text.txt
```

Lên trình duyệt và ta đước kết quả như sau:
<img src=""http://prntscr.com/59zkv3"



The end. ^^



