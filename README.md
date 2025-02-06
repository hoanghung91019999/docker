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
#### image và container khác gì nhau
- image là ứng dụng
- container là processs khi chạy ứng dụng đó
- có thể có nhiều container chạy cùng từ một image
- image được lưu trên một kho chung gọi là registry

#### moniter container
- process list
```
docker container top <PID>
```
- xem file cấu hình của container
```
docker container inspect
```
- performance
```
docker container stats
```
#### container đang chạy những gì - vào shell của container
- có thể vào shell của container để update, ping,...
- có 2 cách để vào shell của container
    + chạy mới container và vào shell: container chạy bash mặc định ( ubuntu, centos,..) , contaier chạy app (nginx,..) thì cần thêm command bash
    ```
    docker container run -it
    docker container run -it < option> < image> bash
    ```
    + vào shell của container đang chạy
    ```
    docker container exec -it
    docker container exec -it <name> bash
    ```
- COMMAND là lệnh khởi tạo đầu tiên khi chạy container.chính vì vậy với những image OS thì có thể vào bash shell luôn còn với app thì cần thêm command bash mới vào được shell
- thoát terminal container
  + exit : thoát terminal và tắt luôn container
  + ctrl + PQ : thoát và không tắt container

 # Network trong docker
- có 4 loại kết nối mạng trong docker :
    + bridge ( mặc định ) : Khi tạo một container mà không chỉ định network, nó sẽ tự động gán vào bridge network mặc định.
    + host : Loại bỏ lớp network isolation, container sẽ sử dụng mạng của host ( sử dụng luôn IP của host ).Thích hợp khi cần hiệu suất cao hoặc truy cập trực tiếp vào cổng mạng của máy chủ.tuy nhiên mạng này dễ bị xung đột port
    + None : Container không có network (không có địa chỉ IP chỉ có loopback), thích hợp cho các ứng dụng không cần kết nối mạng.
    + Overlay (Dành cho Docker Swarm) : Dùng để kết nối nhiều container trên các máy khác nhau trong một cụm Docker Swarm.Cho phép các container giao tiếp mà không cần NAT.
    + Macvlan (IP Riêng) : Cho phép container có địa chỉ IP riêng trên mạng vật lý, giống như một máy thực.Thích hợp cho các ứng dụng yêu cầu giao tiếp trực tiếp với mạng ngoài.
    ```
    docker network create -d macvlan \
    --subnet=192.168.1.0/24 \
    --gateway=192.168.1.1 \
    -o parent=eth0 my_macvlan
    ```
- đối với mạng bridge nếu kết nối ra ngoài cần NAT port
- mỗi container được kết nối trực tiếp, ngang hàng với nhau
- có thể tạo vitual network riêng cho mỗi lớp ứng dụng
- một container có thể kết noois đến nhiều vitual network khác nhau
- container cũng có thể kết nối trực tiếp với dải mạng của host
- có nhiều loại ( driver ) vitual network cho những mục đích khác nhau

- câu lệnh show network ;
```
docker network ls
```
- kiểm tra cấu hình :
```
docker network inspect <name or ID>
```
- tạo network :
```
docker network create <name network>
docker network create --option <name network>
```
- kết nối network vào container:
```
docker network connect <name network> < container >
docker network connect --help
```
- bỏ kết nối network vào container
```
docker network disconnect <name network> < container >
```

#### DNS trong docker network
- container không nói chuyện với nhau bằng IP mà bằng tên ( --name )
- DNS là chức năng có sẵn của hệ thống khi container được tạo ra và kết nối với custom network
- sử dụng --network-alias để đặt tên alias cho một container
    + nhiều container có thể đặt chung một alias nếu kết nối chung 1 mạng custom
    + sử dụng một alias name cho một cụm contaier có chức năng giống nhau để cân bằng tải
    + ví dụ khi ping tới alias name của 1 cụm container mỗi lần ping sẽ tới một container khác nhau để cân bằng tải
# Image
