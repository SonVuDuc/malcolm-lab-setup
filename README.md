# Hướng dẫn setup lab Malcolm

## Các thành phần của Malcolm
  + **Moloch**: Dùng để xử lý, duyệt, tìm kiếm, phân tích... file PCAP. Moloch gồm 2 thành phần:
    + moloch-capture: Công cụ để bắt lưu lượng mạng, phân tích offline file PCAP và thêm metadata vào Elasticsearch
    + viewer: Giao diện web 
  + **Elasticsearch**: Công cụ tìm kiếm dựa trên nền tảng Apache Lucene. Nó cung cấp một bộ máy tìm kiếm dạng phân tán, có đầy đủ công cụ với một giao diện web HTTP có hỗ trợ dữ liệu JSON
  + **Logstash** và **Filebeat**
  + **Kibana**: Giao diện sử dụng dành cho người dùng trên môi trường web. Kibana sẽ sử dụng Elashtichsearch để tìm kiếm các dữ liệu phù hợp với yêu cầu của người dùng.
  + **Zeek**: Framework phân tích mạng
  + **ClamAV**: Công cụ antivirus quyét các file giải nén từ Zeek
  + **CyberChef**: Tool chuyển đổi dữ liệu
  + **jQuery File Upload**: Upload file PCAP hoặc Zeek log để xử lý
  + **List.js**: Giao diện ánh xạ host và tên subnet
  + **Docker và Docker Compose**
  + **Nginx**
  + **nginx-auth-ldap**: Module xác thực LDAP cho Nginx
  + **ElastAlert**: Là 1 framework của Elasticsearch, dùng để cảnh báo
  + **freq**: Tool tính toán entropy của chuỗi
  
## 1. Yêu cầu
  + Cấu hình đề nghị: 1 VPS với 16GB RAM và CPU 16 cores. Malcolm vẫn có thể chạy ở cấu hình thấp hơn, tuy nhiên sử dụng 1 VPS cấu hình đề nghi sẽ cho kết quả tối ưu nhất
  + Docker và Docker Compose 
  + Python

## 2. Cài đặt
### Cài đặt từ docker
Clone Malcolm từ github
```
git clone https://github.com/idaholab/Malcolm.git
```
Cài đặt xác thực và tự sinh self-signed SSL Certificate. 
```
python3 ./scripts/auth_setup
```
Khi hệ thống yêu cầu điền Username, người dùng có thể tùy chọn Username và Password để đăng nhập vào Malcolm

![Screenshot from 2020-08-15 20-40-12](https://user-images.githubusercontent.com/32956424/90313536-925d3a00-df37-11ea-8545-542b73a9502a.png)


Chạy file **install.py** bằng **sudo**
```
sudo python3 ./scripts/install.py 
```
Hệ thống sẽ hỏi người dùng có muốn thêm username vào docker group. Chọn **y** (Yes)

![Screenshot from 2020-08-15 21-04-19](https://user-images.githubusercontent.com/32956424/90314023-eae20680-df3a-11ea-95b7-cdcd03982d78.png)

Chạy lại lệnh install một lần nữa nhưng bỏ **sudo** và thêm **--configure**
```
python3 ./scripts/install.py  --configure
```
Hệ thống sẽ hỏi người dùng một số tùy chỉnh cài đặt, máy có cấu hình khác nhau sẽ có thể có một số lựa chọn khác, nhưng hầu hết chúng sẽ như này:
![Screenshot from 2020-08-15 21-08-51](https://user-images.githubusercontent.com/32956424/90314157-c63a5e80-df3b-11ea-99f7-82c81ce81fea.png)

![Screenshot from 2020-08-15 21-09-20](https://user-images.githubusercontent.com/32956424/90314178-f5e96680-df3b-11ea-99d3-46faa86a5f9d.png)

Sau đó, reboot lại VPS để cài đặt có hiệu lực 

Pull images từ DockerHub bằng lệnh ```docker-compose pull```

![Screenshot from 2020-08-15 21-17-59](https://user-images.githubusercontent.com/32956424/90314306-d3a41880-df3c-11ea-9e50-b8af04094da7.png)

Sau khi quá trình pull images hoàn tất. Bắt đầu chạy Malcolm bằng lệnh
```
python3 ./scripts/start
```
Quá trình cài đặt sẽ mất tầm 10p - 15p

![Screenshot from 2020-08-15 21-21-14](https://user-images.githubusercontent.com/32956424/90314344-46ad8f00-df3d-11ea-8da0-e6ebe2c6ae49.png)

Khi thấy Logstash in ra messenge như này, nghĩa là quá trình cài đặt đã xong. 
```
…
logstash_1  | [2019-06-11T15:45:42,009][INFO ][logstash.agent    ] Pipelines running {:count=>4, :running_pipelines=>[:"malcolm-output", :"malcolm-input", :"malcolm-zeek", :"malcolm-enrichment"], :non_running_pipelines=>[]}
logstash_1  | [2019-06-11T15:45:42,599][INFO ][logstash.agent    ] Successfully started Logstash API endpoint {:port=>9600}
…
```

Màn hình dòng lệnh vẫn sẽ tiếp tục chạy và in ra log. Người dùng có thể tắt bằng **Ctrl + C** mà không ảnh hưởng đến Malcolm đang chạy

Dịch vụ Malcolm có thể truy cập thông qua:
  + Moloch: https://localhost/
  + Kibana: https://localhost/kibana/
  + PCAP Upload (web): https://localhost/upload/
  + PCAP Upload (sftp): sftp://username@127.0.0.1:8022/files/
  + Host and subnet name mapping editor: https://localhost/name-map-ui/
  + Quản lý account: https://localhost:488/
  
## 3. Các tính năng Malcolm

### Upload file PCAP

Malcolm web-server cho phép upload file PCAP và Zeek log thông qua https://localhost/upload

![Screenshot from 2020-08-15 21-36-25](https://user-images.githubusercontent.com/32956424/90314672-968d5580-df3f-11ea-84d6-ccba65a41d79.png)

Người dùng có thể gán tag cho file PCAP upload tiện cho việc tìm kiếm sau này

![Screenshot from 2020-08-16 09-27-20](https://user-images.githubusercontent.com/32956424/90325188-bb66e480-dfa2-11ea-8a96-fc871486c34f.png)



### Moloch
Truy cập vào giao diện Moloch thông qua port 443 và IP của VPS (e.g https://localhost)

![Screenshot from 2020-08-16 03-27-18](https://user-images.githubusercontent.com/32956424/90325309-3f6d9c00-dfa4-11ea-92e7-933324ecbc42.png)

#### Session

Chế độ xem Session cung cấp thông tin chi tiết của các phiên, có thể là phiên được **Moloch session** tạo ra từ file PCAP hoặc Zeek log được ánh xạ đến cơ sở dữ liệu phiên

![Screenshot from 2020-08-16 03-27-18](https://user-images.githubusercontent.com/32956424/90529966-8626e600-e19e-11ea-9920-f3a2360d46b0.png)

Chi tiết 1 session: 
![Screenshot from 2020-08-18 22-11-19](https://user-images.githubusercontent.com/32956424/90531050-c6d32f00-e19f-11ea-9178-47585890b773.png)


Chế độ xem Session có các tính năng cho phép lọc và tìm kiếm các session
  + Search bar
  + Time bounding
  + map
  + chế độ xem 

Export ra file PCAP

![Screenshot from 2020-08-18 22-09-39](https://user-images.githubusercontent.com/32956424/90530842-8a9fce80-e19f-11ea-8aa9-25c82ef4b7f3.png)


#### SPIView

Giao diện dùng để xem xét số liệu của các session. Liệt kê các số liệu chung của session (e.g IP nguồn và đích, port, giao thức sử dụng...)

![Screenshot from 2020-08-18 22-45-45](https://user-images.githubusercontent.com/32956424/90534956-94780080-e1a4-11ea-8d0b-df6f0c43e64f.png)


#### SPIGraph

Moloch's SPI (**S**ession **P**rofile **I**nformation) 

![image](https://user-images.githubusercontent.com/32956424/90539945-b2486400-e1aa-11ea-9cb7-c254445545a4.png)


#### Connection

Connection thể hiện các network communication thông qua đồ thị, giúp người dùng dễ dàng hình dung được mối quan hệ giữa các host  

![Screenshot from 2020-08-16 10-16-54](https://user-images.githubusercontent.com/32956424/90326505-3506ce80-dfb3-11ea-80e9-734fd5981d36.png)


#### Hunt

#### Files

Hiển thị những file PCAP đã được Export hoặc file PCAP do người dùng upload lên

![Screenshot from 2020-08-18 22-28-33](https://user-images.githubusercontent.com/32956424/90533048-2d594c80-e1a2-11ea-8141-9b1779132474.png)

#### Stats



#### History

Chế độ History hiển thị danh sách lịch sử các vấn đề truy vấn tới Moloch và chi tiết các truy vấn đó
![Screenshot from 2020-08-18 22-34-17](https://user-images.githubusercontent.com/32956424/90533621-f899c500-e1a2-11ea-9f90-d7bae61f0305.png)

### Kibana

Kibana là một nền tảng phân tích và trực quan hóa nguồn mở được thiết kế để hoạt động kết hợp chặt chẽ với Elasticsearch. Cung cấp các tính năng mạnh mẽ và dễ sử dụng như biểu đồ và hỗ trợ không gian địa lý tích hợp.

![Screenshot from 2020-08-19 16-54-13](https://user-images.githubusercontent.com/32956424/90622593-84f9c580-e23f-11ea-82ab-0e16fb0d48fd.png)


#### Visualizations và dashboards

![Screenshot from 2020-08-19 17-29-06](https://user-images.githubusercontent.com/32956424/90624027-888e4c00-e241-11ea-937b-7c57d21b72e3.png)

![image](https://user-images.githubusercontent.com/32956424/90629176-1883c400-e249-11ea-84d9-72013d0a78fa.png)

![image](https://user-images.githubusercontent.com/32956424/90629204-2afdfd80-e249-11ea-9481-0db1b065f756.png)

![image](https://user-images.githubusercontent.com/32956424/90629224-33eecf00-e249-11ea-9f6b-a08d829a8f3a.png)

![image](https://user-images.githubusercontent.com/32956424/90629273-4b2dbc80-e249-11ea-9369-c8b3d46327b4.png)


