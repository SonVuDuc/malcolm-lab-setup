# Hướng dẫn setup lab Malcolm

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

