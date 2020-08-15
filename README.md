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


Chạy lệnh install bằng sudo
```
sudo python3 ./scripts/install.py 
```
Hệ thống sẽ hỏi người dùng có muốn thêm username vào docker group. Chọn **y** (Yes)

![Screenshot from 2020-08-15 21-04-19](https://user-images.githubusercontent.com/32956424/90314023-eae20680-df3a-11ea-95b7-cdcd03982d78.png)

Chạy lại lệnh install một lần nữa nhưng bỏ **sudo** và thêm **--configure**
```
python3 ./scripts/install.py  --configure
```
