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
- Mở file sau đó thực hiện sửa đường dẫn đến thư mục vừa giải nén: ```d:/Apache/Apache24```
- Bỏ dấu # ở dòng ```LoadModule vhost_alias_module modules/mod_vhost_alias.so``` và ```Include conf/extra/httpd-vhosts.conf```
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
- Mở file ```C:\WINDOWS\SYSTEM32\Drivers\etc\hosts``` bằng Notepad với quyền admin (chuột phải Notepad > Run as administrator, rồi mở file).
- Thêm dòng cuối file: ```127.0.0.1 nguyentrunghieu.com```
- Ctrl + S để lưu file.
#### Bước 5: Cài đặt và khởi động Apache
Mở CMD quyền Administrator sau đó gõ lệnh:
- Lệnh cài đặt: ```D:\Apache\Apache24\bin\httpd.exe -k install```
- Lệnh khởi động: ```D:\Apache\Apache24\bin\httpd.exe -k start```

<img width="1798" height="110" alt="image" src="https://github.com/user-attachments/assets/326ca30f-bd57-4df6-b4b0-c9b28dad287a" />
<img width="1808" height="44" alt="image" src="https://github.com/user-attachments/assets/b293be4f-146b-4ce5-bbfe-03d286740656" />

#### Kết quả sau khi chạy Apache:
<img width="3071" height="1817" alt="image" src="https://github.com/user-attachments/assets/10237f7b-274d-4b10-9ac1-e35e47ec8da2" />

### 2.2. Cài đặt Nodejs và Nodered.
#### Bước 1: Cài đặt Nodejs
- download file `https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi`
- cài đặt vào thư mục `D:\nodejs`
#### Bước 2: Cài đặt Nodered
Mở cmd, vào thư mục ```D:\nodejs```, chạy lệnh ```npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"``` để cài đặt Nodered
<img width="1789" height="280" alt="image" src="https://github.com/user-attachments/assets/3079137d-72d7-4b00-b36f-7a2d7ad7e30c" />

#### Bước 3: Cài đặt NSSM
- Truy cập link: https://nssm.cc/release/nssm-2.24.zip để tải về.Sau đó giải nén thu được file nssm.exe
- Copy file nssm.exe vào thư mục ```D:\nodejs\nodered```
- Tạo file cmd: ```D:\nodejs\nodered\run-nodered.cmd``` bằng Notepad với nội dung:
```
@echo off
REM fix path
set PATH=D:\nodejs;%PATH%
REM Run Node-RED
node "D:\nodejs\nodered\node_modules\node-red\red.js" -u "D:\nodejs\nodered\work" %*
```
<img width="2459" height="795" alt="image" src="https://github.com/user-attachments/assets/e1a413ad-eae1-4fd5-8135-6527c06ede7d" />

#### Bước 4: Chạy service
- Mở cmd với quyền Administrator, chuyển đến: ```cd D:\nodejs\nodered```
- Chạy: ```nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"```
- Chạy: ```nssm start a1-nodered```
<img width="1795" height="246" alt="image" src="https://github.com/user-attachments/assets/060412ce-1f71-4b16-9e47-be4207a7bf39" />

- Sau khi chạy thành công, Node-Red sẽ hoạt động ở ```http://localhost:1880```
<img width="3071" height="1749" alt="image" src="https://github.com/user-attachments/assets/d6913733-86fa-4ac5-90fd-8bf0608fac2e" />

### 2.3. Tạo CSDL
- Thiết kế cơ sở dữ liệu chứa các thông tin về các sản phẩm trong một web bán sách.
- Database name: Book_Sale
- Port: 1433
- Username: sa
- Table name: Books
- Server name: DELL-INS16PLUS
#### Bảng dữ liệu:
<img width="1499" height="510" alt="image" src="https://github.com/user-attachments/assets/94d8c8ba-6cbb-4a1f-ab6c-d238d7ac6ea1" />

#### Dữ liệu demo:
<img width="2405" height="267" alt="image" src="https://github.com/user-attachments/assets/3cbba10b-8219-422c-b32e-c69d85c2ad6d" />

### 2.4. Cài đặt thư viện trên Notered
- Mở NOTERED bằng url trên trình duyệt: localhost:1880
- Chọn Manage Palette rồi chọn Install
- Cài đặt các thư viện:
```
node-red-contrib-mssql-plus
node-red-node-mysql
node-red-contrib-telegrambot
node-red-contrib-moment
node-red-contrib-influxdb
node-red-contrib-duckdns
node-red-contrib-cron-plus
```
<img width="1407" height="1500" alt="image" src="https://github.com/user-attachments/assets/abafc95e-e6a0-4887-9203-ecdcb1e7fe69" />

- Mở file cấu hình: ```D:\nodejs\nodered\work\settings.js```
- Tìm phần adminAuth, uncomment (bỏ //):
<img width="1430" height="401" alt="image" src="https://github.com/user-attachments/assets/8bc8ea94-2f59-485a-8c77-cfcb9ef9588d" />

- Sửa chuỗi mã hóa mật khẩu bằng chuỗi mới (chuỗi mã hóa mật khẩu có thể được thiết lập bằng tool ```https://tms.tnut.edu.vn/pw.php```) :
<img width="1443" height="400" alt="image" src="https://github.com/user-attachments/assets/7a52f717-a6bd-46b9-a983-8ce93762a9fb" />

- Chạy lại Nodered bằng cmd, vào thư mục ```D:\nodejs\nodered``` và chạy lệnh ```nssm restart a1-nodered```:
<img width="1797" height="186" alt="image" src="https://github.com/user-attachments/assets/ed46e498-b606-45e4-aca3-b871bbb74a4a" />

- Sau khi chạy lệnh thành công, Notedred sẽ yêu cầu mật khẩu mới khi đăng nhập:
<img width="3064" height="1750" alt="image" src="https://github.com/user-attachments/assets/2545225c-060c-4551-8cef-778ff93a7036" />

### 2.5. Tạo API backend bằng Notered
#### Tạo API tìm kiếm sách nhận tham số từ url trả về json
- Trên Nodered, ở flow 1 sử dụng `http in` và `http response` để tạo api
- Thêm node MSSQL để kết nối và truy vấn tới cơ sở dữ liệu
- Logic Flow sẽ gồm 4 node sau (thứ tự nối dây):
#### Bước 1: Thêm node http in: dùng GET, URL đặt tùy ý (/timkiem)
<img width="1019" height="563" alt="image" src="https://github.com/user-attachments/assets/e04ebb66-650d-44fe-abd9-b13e1bbf626a" />

#### Bước 2: Thêm node function : tiền xử lý dữ liệu
<img width="1284" height="774" alt="image" src="https://github.com/user-attachments/assets/9b4e720a-e0af-401f-b53a-5e5f30124147" />

#### Bước 3: Thêm node MSSQL để truy vấn tới CSDL và nhận tham số từ node Function
<img width="1019" height="825" alt="image" src="https://github.com/user-attachments/assets/5096108b-f8ca-4bbe-ac51-611b963904fb" />
<img width="1007" height="1427" alt="image" src="https://github.com/user-attachments/assets/9f82da87-b379-4e76-9347-9dd8082149f0" />

#### Bước 4: Thêm node http response: để phản hồi dữ liệu tới client. 
<img width="1027" height="677" alt="image" src="https://github.com/user-attachments/assets/c7084fd5-0760-4a67-9ccb-4598675b6463" />

#### Bước 5: Thêm node debug để quan sát giá trị trung gian
<img width="1022" height="663" alt="image" src="https://github.com/user-attachments/assets/24d1c338-3cdc-4ef3-9b69-3265ece53987" />

#### Kết quả:
<img width="1497" height="261" alt="image" src="https://github.com/user-attachments/assets/f206aaa0-d03f-4fff-b921-0cb6be272739" />

#### Test API tìm kiếm sách
Ví dụ: tìm kiếm sách http://localhost:1880/timkiem?q=Du
<img width="3071" height="1740" alt="image" src="https://github.com/user-attachments/assets/7b4f0f2b-ce46-4c7a-b3c2-0603420c83b1" />
