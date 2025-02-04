# docker
## cài đặt docker trên centos 9
- cài đặt docker : 
```
sudo dnf update -y
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker
```
## sử dụng docker thế nào 
- câu lệnh :
```
docker info
- trạng thái chung của docker engine
```
- trợ giúp lệnh :
```
docker --help
```
- management commands : command chính, đối tượng chính mà chúng ta quản trị
- commands: những chức năng phụ, những câu lệnh tiếp theo gắn vào đối tượng chính
- options : những tham phụ đưa thêm vào
- cấu trúc cú pháp chung :
```
docker < managerment command> < command > [ option ]
```
- vd: tạo ra container đầu tiên
```
docker container run --publish 8080:80 --detach nginx
docker container run --publish 8080:80 --name hunghv --detach nginx
```
  + 8080 : giá trị port cho các mạng bên ngoài truy cập vào port 8080
  + 80: NAT từ 8080 vào port 80 trên tiến trình nginx - có nghĩa là phơi port
  + publish : option phơi port
  + detach : option chạy image ẩn
  + name : option đặt tên
- liệt kê các container đang chạy
```
docker container ls
docker container ls -a
```
- dừng container đang chạy:
```
docker container stop <container ID> or <name>
```
#### container và vitual machine khác gì nhau
- VM là một máy ảo được chạy trên OS chính.có đầy đủ các thành phần như cpu ram disk, kernel
- container không phải là máy ảo.chỉ là một tiến trình (processs) chạy trên hệ điều hành.
  + bản thân container ko có kernel hệ điều hành
  + process này được giới hạn tài nguyên sử dụng trên OS
  + container dừng và thaots như đóng process
- kiểm tra container PID :
```
docker container top <name container>
ps aux | grep <image>
```
- kiểm tra logs của container :
```
docker container logs <name contianer>
```

