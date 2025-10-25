# Web-based-App-Dev-2
## Thông tin cá nhân
### Họ và tên: Nguyễn Trung Hiếu
### MSSV: K225480106019
### Lớp: K58KTP
## BÀI TẬP 2
1. Sử dụng github để ghi lại quá trình làm, tạo repo mới, để truy cập public, edit file `readme.md`:
   chụp ảnh màn hình (CTRL+Prtsc) lúc đang làm, paste vào file `readme.md`, thêm mô tả cho ảnh.

2. NỘI DUNG BÀI TẬP:

2.1. Cài đặt Apache web server:
- Vô hiệu hoá IIS: nếu iis đang chạy thì mở cmd quyền admin để chạy lệnh: iisreset /stop
- Download apache server, giải nén ra ổ D, cấu hình các file:
  + D:\Apache24\conf\httpd.conf
  + D:Apache24\conf\extra\httpd-vhosts.conf
  để tạo website với domain: fullname.com
  code web sẽ đặt tại thư mục: `D:\Apache24\fullname` (fullname ko dấu, liền nhau)
- sử dụng file `c:\WINDOWS\SYSTEM32\Drivers\etc\hosts` để fake ip 127.0.0.1 cho domain này
  ví dụ sv tên là: `Đỗ Duy Cốp` thì tạo website với domain là fullname ko dấu, liền nhau: `doduycop.com`
- thao tác dòng lệnh trên file `D:\Apache24\bin\httpd.exe` với các tham số `-k install` và `-k start` để cài đặt và khởi động web server apache.

2.2. Cài đặt nodejs và nodered => Dùng làm backend:
- Cài đặt nodejs:
  + download file `https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi`  (đây ko phải bản mới nhất, nhưng ổn định)
  + cài đặt vào thư mục `D:\nodejs`
- Cài đặt nodered:
  + chạy cmd, vào thư mục `D:\nodejs`, chạy lệnh `npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"`
  + download file: https://nssm.cc/release/nssm-2.24.zip
    giải nén được file nssm.exe
    copy nssm.exe vào thư mục `D:\nodejs\nodered\`
  + tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):
@echo off
REM fix path
set PATH=D:\nodejs;%PATH%
REM Run Node-RED
node "D:\nodejs\nodered\node_modules\node-red\red.js" -u "D:\nodejs\nodered\work" %*
  + mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`
  + cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"
  + chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`

2.3. Tạo csdl tuỳ ý trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name

2.4. Cài đặt thư viện trên nodered:
- truy cập giao diện nodered bằng url: http://localhost:1880
- cài đặt các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus
- Sửa file `D:\nodejs\nodered\work\settings.js` : 
  tìm đến chỗ adminAuth, bỏ comment # ở đầu dòng (8 dòng), thay chuỗi mã hoá mật khẩu bằng chuỗi mới
    adminAuth: {
        type: "credentials",
        users: [{
            username: "admin",
            password: "chuỗi_mã_hoá_mật_khẩu",
            permissions: "*"
        }]
    },   
   với mã hoá mật khẩu có thể thiết lập bằng tool: https://tms.tnut.edu.vn/pw.php
- chạy lại nodered bằng cách: mở cmd, vào thư mục `D:\nodejs\nodered` và chạy lệnh `nssm restart a1-nodered`
  khi đó nodered sẽ yêu cầu nhập mật khẩu mới vào được giao diện cho admin tại: http://localhost:1880

2.5. tạo api back-end bằng nodered:
- tại flow1 trên nodered, sử dụng node `http in` và `http response` để tạo api
- thêm node `MSSQL` để truy vấn tới cơ sở dữ liệu
- logic flow sẽ gồm 4 node theo thứ tự sau (thứ tự nối dây): 
  1. http in  : dùng GET cho đơn giản, URL đặt tuỳ ý, ví dụ: /timkiem
  2. function : để tiền xử lý dữ liệu gửi đến
  3. MSSQL: để truy vấn dữ liệu tới CSDL, nhận tham số từ node tiền xử lý
  4. http response: để phản hồi dữ liệu về client: Status Code=200, Header add : Content-Type = application/json
  có thể thêm node `debug` để quan sát giá trị trung gian.
- test api thông qua trình duyệt, ví dụ: http://localhost:1880/timkiem?q=thị

2.6. Tạo giao diện front-end:
- html form gồm các file : index.html, fullname.js, fullname.css
  cả 3 file này đặt trong thư mục: `D:\Apache24\fullname`
  nhớ thay fullname là tên của bạn, viết liền, ko dấu, chữ thường, vd tên là Đỗ Duy Cốp thì fullname là `doduycop`
  khi đó 3 file sẽ là: index.html, doduycop.js và doduycop.css
- index.html và fullname.css: trang trí tuỳ ý, có dấu ấn cá nhân, có form nhập được thông tin.
- fullname.js: lấy dữ liệu trên form, gửi đến api nodered đã làm ở bước 2.5, nhận về json, dùng json trả về để tạo giao diện phù hợp với kết quả truy vấn của bạn.

2.7. Nhận xét bài làm của mình:
- đã hiểu quá trình cài đặt các phần mềm và các thư viện như nào?
- đã hiểu cách sử dụng nodered để tạo api back-end như nào?
- đã hiểu cách frond-end tương tác với back-end ra sao?
## BÀI LÀM
### 2.1. Cài đặt Apache Web Server
#### Bước 1: Vô hiệu hóa IIS đang chạy
- Phải vô hiệu hóa IIS (hoặc ít nhất là dừng nó tạm thời) khi cài đặt Apache vì hai dịch vụ này tranh chấp cùng cổng (80/443), khiến Apache không thể khởi động hoặc hoạt động ổn định
- Mở CMD với quyền Admin và chạy lệnh iisreset /stop
<img width="1806" height="153" alt="Screenshot 2025-10-25 092240" src="https://github.com/user-attachments/assets/6661162b-8fa6-4b05-8279-84855aa1a7bc" />

#### Bước 2: Tải xuống Apache Web Server, giải nén trên ổ D
- Truy cập link https://www.apachelounge.com/download/ để tải Apache về máy.
- Sau khi tải xuống, giải nén vào thư mục D:\Apache\Apache24
#### Bước 3: Cấu hình Apache
Cấu hình các file:
  + D:\Apache24\conf\httpd.conf
  + D:\Apache24\conf\extra\httpd-vhosts.conf

Để tạo website với domain: nguyentrunghieu.com
##### Cấu hình file conf\httpd.conf:
- Mở file sau đó thực hiện sửa đường dẫn đến thư mục vừa giải nén: "d:/Apache/Apache24"
- Bỏ dấu # ở dòng LoadModule vhost_alias_module modules/mod_vhost_alias.so và Include conf/extra/httpd-vhosts.conf
- Lưu file
##### Cấu hình file conf\extra\httpd-vhosts.conf
Trong thẻ <VirtualHost *:80>
- Thay DocumentRoot bằng đường dẫn chứ thư mục web
- Đổi tên ServerName

```
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "D:\Apache\Apache24\nguyentrunghieu"
    ServerName nguyentrunghieu.com
    ServerAlias www.dummy-host.example.com
    ErrorLog "logs/dummy-host.example.com-error.log"
    CustomLog "logs/dummy-host.example.com-access.log" common
    <Directory "D:\Apache\Apache24\nguyentrunghieu">
    	 Options Indexes FollowSymLinks
   	 AllowOverride None
   	 Require all granted
    </Directory>
</VirtualHost> 
```
##### Tạo một thư mục chứa website: D:\Apache\Apache24\nguyentrunghieu
Tạo 1 file index.html trong thư mục nguyentrunghieu
#### Bước 4: Fake IP cho domain
- Mở file C:\WINDOWS\SYSTEM32\Drivers\etc\hosts bằng Notepad với quyền admin (chuột phải Notepad > Run as administrator, rồi mở file).
- Thêm dòng cuối file: 127.0.0.1 nguyentrunghieu.com.
- Ctrl + S để lưu file.
#### Bước 5: Cài đặt và khởi động Apache
Mở CMD quyền Administrator sau đó gõ lệnh:
- Lệnh cài đặt: D:\Apache\Apache24\bin\httpd.exe -k install
- Lệnh khởi động: D:\Apache\Apache24\bin\httpd.exe -k start

<img width="1798" height="110" alt="image" src="https://github.com/user-attachments/assets/326ca30f-bd57-4df6-b4b0-c9b28dad287a" />
<img width="1808" height="44" alt="image" src="https://github.com/user-attachments/assets/b293be4f-146b-4ce5-bbfe-03d286740656" />
