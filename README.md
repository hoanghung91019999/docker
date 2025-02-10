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
#### sơ bộ về docker image
- là bản đóng gói của ứng dụng đang chạy bao gồm appbinary và dependency
- metadata về việc khởi tạo một container
- không phải là một OS image, chỉ bao gồm các thành phần cần thieets để chạy ứng dụng
- lưu trữ và chỉ sẻ sử dụng image qua registry
- image được tạo thành từ dockerfile
#### có những gì bên trong image
- imgae được tạo thành bởi nhiều layers
- ![image](https://github.com/user-attachments/assets/15b51319-b875-4784-94af-6099a2d8c32f)

- mỗi layer chỉ được lưu một lần trên host
    + tiết kiệm bộ nhớ
    + tiết kiệm thời gian pull push
- containeer là một layer của image
-  ![Uploading image.png…]()
- kiểm bằng bằng :
```
docker image inspect
docker image history
```
#### cách sử dụng image tag và docker hub
- đăng nhập trên docker hub và tạo repo
- đánh tag cho image
```
docker login
docker imgae tag <tên imgae > < name mới >
```
- push lên docker hub
```
docker image push
** đánh tag version **
docker image push <repo/name:version>
```
- xóa image
```
docker rmi <name:tag>
docker rmi -f <name:tag> ( buộc xóa khi container vẫn còn)
```
### Tạo image
#### docker file 
- là file text chứa mô tả tuần tự để tạo ra image. nói cách khác nó chứa các image layer
- Khi bạn chạy lệnh docker build, Docker sẽ kiểm tra từng bước (RUN, COPY, CMD,...) trong Dockerfile và lưu cache cho mỗi bước.
- Nếu nội dung một bước không thay đổi, Docker sử dụng lại cache thay vì chạy lại lệnh đó.
- Nếu một bước thay đổi, Docker bỏ cache từ bước đó trở đi và build lại từ đầu.
- |Lệnh  |	Ý nghĩa  |
  |-------|------------|
  |FROM	|Xác định base image (ví dụ: ubuntu, alpine, node).|
  |WORKDIR|	Đặt thư mục làm việc mặc định trong container.|
  |COPY	|Sao chép file từ thư mục hiện tại vào container.|
  |ADD|	Tương tự COPY, nhưng hỗ trợ tải file từ URL & giải nén file .tar.gz.|
  |RUN|	Chạy lệnh khi build image (thường dùng để cài đặt phần mềm).|
  |CMD|	Chạy lệnh mặc định khi container khởi động (chỉ chạy một lần duy nhất).|
  |ENTRYPOINT	|Tương tự CMD nhưng không bị ghi đè nếu có lệnh được truyền vào docker run.|
  |EXPOSE|	Mở cổng để container có thể nhận kết nối mạng.|
  |ENV|	Định nghĩa biến môi trường.|
  |VOLUME	|Tạo thư mục để lưu trữ dữ liệu bên ngoài container.|
  |ARG|	Định nghĩa biến chỉ sử dụng khi build (docker build --build-arg).|
  |LABEL	|Gán metadata cho image.|

- lưu ý khi build :
  + docker sẽ tìm và build theo tên file " Dockerfile " theo thư mục chỉ định
  + sắp xếp các layer hợp lý để tối ưu hóa cache khi build
  + vì container chạy bằng quyền admin nên trong code không cần sudo
  + firewall-cmd là lệnh quản lý firewalld, nhưng container thường không chạy dịch vụ nên khi chạy hay báo lỗi nên loại bỏ.
- tạo 1 docker file đơn giản :
  + làm việc tại thư mục root
  + tạo 1 file test.html : vi test.html
  ```
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Test Nginx</title>
  </head>
  <body>
      <h1>Hello, Nginx is working!</h1>
  </body>
  </html>
  ```
- tạo Docker file
```
vi Dockerfile
```
- chỉnh sửa Dockerfile
```
FROM nginx
WORKDIR /usr/share/nginx/html/
COPY test.html index.html
EXPOSE 80 443
```
- build image
```
docker image build -t nginxapp .
```
- giải thích :
  + image có tên nginxapp chạy trên nền nginx
  + nginx sẽ đọc file html ở /usr/share/nginx/html/ nên WORKDIR chuyển thư mục làm việc vào /usr/share/nginx/html/
  + COPY sẽ copy file test.html ở thư mục hiện vào thư mục làm việc trong docker ở đây sẽ copy file test.html tới đích là file index.html ( nội dung của index.html sẽ được thay đổi và nginx sẽ ưu tiên đọc index.html)
  + EXPOSE sẽ document lại cho người dùng biết app sẽ NAT ra ở port bao nhiêu
- chạy image vừa tạo :
```
docker container run -p 80:80 -d nginxapp
```
- nếu image đã được build nhưng khi run container không chạy đúng có thể vào container để check
```
docker exec - it <ID container > /bin/bash
```
- kiểm tra trong thư mục làm việc các file đã được copy hay chưa,...vv
- lưu ý : Nếu container chỉ hỗ trợ HTTP (80), bạn cần một proxy bên ngoài như Nginx hoặc Traefik để xử lý SSL. lúc này cần cấu hình khôi block trên nginx để sử lý traffix SSL tới service 
    + Khi bạn có nhiều container chạy trên cùng một server
    + Khi bạn muốn bật HTTPS nhưng ứng dụng chỉ hỗ trợ HTTP
    + Khi bạn muốn load balancing (cân bằng tải)
    + Khi bạn muốn bảo vệ backend và giấu cổng nội bộ
- build ứng dụng package.json và server.js đơn giản xem 
# lưu trữ trong container
#### chia sẻ file giữa host và container 
- container bản chất là 1 layer trong image vì vậy khi container được xóa đi thì dữ liệu trong container đó cũng sẽ mất đi
- cần map tất cả các dữ liệu trong container đó vào bộ nhớ trên host
