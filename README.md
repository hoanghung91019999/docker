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
## docker là gì ?
- Docker là một nền tảng mã nguồn mở giúp đóng gói, phân phối và chạy ứng dụng trong các container. Container giúp đảm bảo rằng ứng dụng có thể chạy đồng nhất trên mọi môi trường (máy cá nhân, server, cloud...).
#### thành phần kiến trúc của docker 
![image](https://github.com/user-attachments/assets/edcfc7b8-49d6-4b1a-8fce-4861e9a60bf5)
- Docker sử dụng kiến trúc client-server. Docker client sẽ liên lạc với các Docker daemon, các Docker daemon sẽ thực hiện các tác vụ build, run và distribuing các Docker container
- Docker gồm :
- Docker Engine : Là phần mềm chính của Docker, bao gồm:
    + Docker Daemon: Quản lý container, image và volume.
    + Docker CLI (Command-Line Interface): Công cụ dòng lệnh để thao tác với Docker.
    + REST API: Cho phép giao tiếp với Docker Daemon.
- ![image](https://github.com/user-attachments/assets/1a1db856-d350-402c-a822-3f2b333b03c7)

- Docker Registry : Kho lưu trữ Docker Image (VD: Docker Hub, AWS ECR, GitHub Container Registry).Khi chạy container, nếu image chưa có trên máy, Docker sẽ tải từ registry.
- docker object bao gồm : 
    + Docker Image : Là một tệp chứa mã nguồn, thư viện, môi trường và tất cả các cài đặt cần thiết để chạy ứng dụng.
    + Docker Container : Là một instance của Docker Image đang chạy.Hoạt động độc lập, nhẹ, chia sẻ kernel với hệ điều hành host.Dễ dàng di chuyển giữa các môi trường (dev, test, production).
    + Docker Volume : Dùng để lưu trữ dữ liệu bên ngoài container, tránh mất dữ liệu khi container bị xóa.
    + Docker Network : Quản lý cách các container giao tiếp với nhau hoặc với bên ngoài.
### cơ chế hoạt động
- Khi chạy một container (docker run), Docker Client gửi lệnh đến Docker Daemon.
- Docker Daemon kiểm tra xem image có sẵn chưa nếu chưa có, nó kéo image từ Docker Registry.
- Docker Daemon tạo container từ image và thiết lập:
    + Namespace (cách ly tài nguyên như PID, Network, File System...).
    + Cgroups (giới hạn CPU, RAM, I/O...).
- Container chạy với môi trường riêng biệt nhưng dùng chung Kernel với Host.(  với các hđh khác docker sử dụng công nghệ ảo hóa môi trường để tạo môi trường )
### tại sao nên sử dụng docker
- Chạy đồng nhất trên mọi môi trường
- Nhẹ hơn so với Virtual Machine (VM)
- Dễ dàng scale và triển khai
- Quản lý và rollback dễ dàng
- Dễ dàng chia sẻ & CI/CD
### khi nào nên sử dụng docker
-  Khi cần môi trường đồng nhất giữa DEV - TEST - PROD
-  Khi cần triển khai nhanh & tự động
-  Khi làm việc với microservices
-  Khi muốn tiết kiệm tài nguyên hơn so với VM
-  khi cần CI/CD tự động
### so sánh docker và VM

## Docker container 
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
- build ứng dụng package.json và server.js đơn giản xem [tại đây](https://github.com/hoanghung91019999/docker/blob/main/dockerbuild%20nodejs.md)
#### tối ưu hóa docker file
- Dùng Base Image Nhỏ Gọn :
    + Không nên dùng image lớn như ubuntu hoặc debian nếu không cần thiết.
    + dùng Alpine (~5MB), hoặc Slim versions (debian:slim).
- Dùng Multi-Stage Build để giảm kích thước Image
    + sử dụng một stage để build ứng dụng (có thể chứa nhiều dependency, công cụ build, file tạm, v.v.) và sau đó chỉ copy các file cần thiết sang một stage khác để tạo Image cuối cùng.
    ```
    # Stage 1: Build
    FROM golang:1.20 AS builder
    WORKDIR /app
    COPY . .
    RUN go build -o myapp

    # Stage 2: Runtime (Chỉ giữ lại binary)
    FROM alpine:latest
    WORKDIR /root/
    COPY --from=builder /app/myapp .
    CMD ["./myapp"]
    ```
    + Cách hoạt động của Multi-Stage Build
    + Stage 1 (Builder Stage)
    + Dùng một Image lớn (ví dụ: golang, node, maven, v.v.) để build code.
    + Cài đặt tất cả dependency, compile code, chạy test, v.v.
    + Kết quả của stage này có thể tạo ra một binary hoặc thư mục chứa ứng dụng đã sẵn sàng chạy.
    + Stage 2 (Final Stage)

    + Dùng một Image nhẹ hơn (ví dụ: alpine, scratch, v.v.).
    + Chỉ copy những file cần thiết từ stage 1 sang (ví dụ: chỉ copy binary đã build, không mang theo source code hay công cụ build).
    + Giảm thiểu kích thước Image cuối cùng.
- Giảm số lượng Layers (RUN kết hợp nhiều lệnh)
    + Mỗi lệnh RUN tạo một layer mới, nên nên gộp nhiều lệnh lại.
    + Sử dụng && để giảm số lượng layers.
- Luôn sử dụng .dockerignore
    + tạo file .dockerignore
    + Điền vào file .dockerignore những file/thư mục cần bỏ qua
- Không chạy container với quyền root
    + Mặc định, container chạy với quyền root, gây rủi ro bảo mật.
    + Nên tạo user không phải root để chạy app.
    ```
    RUN addgroup -S appgroup && adduser -S appuser -G appgroup
    USER appuser
    ```
- Sử dụng Cache hiệu quả (Đặt lệnh COPY trước khi RUN)
    + Docker cache từng layer, nếu một layer thay đổi, các layer sau bị build lại.
    + Đặt lệnh COPY trước RUN để tận dụng cache.
- Định nghĩa CMD và ENTRYPOINT đúng cách
    + CMD có thể bị override khi chạy docker run.
    + ENTRYPOINT không bị override trừ khi dùng --entrypoint.
- Không lưu dữ liệu trong container (Dùng Volume)
- Luôn đặt phiên bản cụ thể cho base image
    + FROM node:20.4-alpine
- Dọn dẹp file tạm sau khi cài đặt package
    + Sau khi cài package, nên xóa các file cache để giảm kích thước image.
    ```
    RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
    ```

# lưu trữ trong container
#### chia sẻ file giữa host và container 
- container bản chất là 1 layer trong image vì vậy khi container được xóa đi thì dữ liệu trong container đó cũng sẽ mất đi
- cần map tất cả các dữ liệu trong container đó vào bộ nhớ trên host
- với container không thay đổi chỉ re-deploy
- chính vì vậy các container chạy database khi xóa container đi sẽ mất đi dữ liệu
#### persistent data
- Persistent Data giúp lưu trữ dữ liệu bên trong container mà không bị mất khi container dừng hoặc bị xóa.
- Nếu không cấu hình Persistent Data, dữ liệu sẽ bị mất khi container bị xóa
- các phương pháp lưu trữ dữ liệu :
    + Volume : docker quản lý
    + bind mount : ổ cứng máy host
    + tmpfs : RAM ( bộ nhớ tạm )
##### Volume 
- phương pháp được docker quản lý
    + nó được docker tạo ra sửa xóa
    + chỉ nằm trên 1 đường dẫn thư mục duy nhất
    + bảo mật và quyền truy cập do docker kiểm soát có nghĩa là dữ liệu nằm trong thư mục mà docker quản lý host sẽ không thấy dữ liệu này ngày trừ khi vào đúng thư mục docker quản lý
- Dữ liệu được lưu trong thư mục /var/lib/docker/volumes/
- docker không hỗ trợ gắn volume vào container đang chạy
- Tạo Volume & Gắn vào Container :
```
docker volume create < name volume >
docker run -d -v <name volume>:/data --name my_container nginx
```
- Gắn volume <name volume> vào thư mục /data trong container.
    + thư mục chứa dữ liệu trên host sẽ là /var/lib/docker/volumes/name_volume/_data
- liệu kê volume
```
docker volume ls
```
- xem cấu hình chi tiết
```
docker volume inspect < name volume >
```
- sử dụng muont thay cho -v
```
docker run -d --mount type=bind,source=/home/user/data,target=/app/data nginx
```
- di chuyển volume giữa các máy chủ bằng cách backup: 
```
docker run --rm -v my_volume:/data -v $(pwd):/backup alpine tar czf /backup/my_backup.tar.gz -C /data .
```
- Restore dữ liệu từ file backup vào volume mới: 
```
docker run --rm -v new_volume:/data -v $(pwd):/backup alpine tar xzf /backup/my_backup.tar.gz -C /data
```
- khi nào dùng volume:
    + khi muốn docker quản lý mà ko cần quan tâm đến thư viện trên host
    + dữ liệu cần tồn tại lâu dài ngay cả khi container bị xóa
    + chạy trên nhiều môi trường khác nhau
- Không dễ chỉnh sửa file từ host (cần docker cp hoặc docker exec).
- Không trực quan như Bind Mount khi cần truy cập nhanh vào file.
#### bind mount 
- Bind Mount là cách gắn một thư mục hoặc file trên host vào container, giúp host và container chia sẻ dữ liệu trực tiếp và đồng bộ ngay lập tức.
  + Host kiểm soát dữ liệu (Docker không quản lý).
  + Dữ liệu thay đổi ngay lập tức trên cả host và container.
  + Có thể dùng để đọc hoặc ghi dữ liệu.
  + Nguy cơ lỗi do chỉnh sửa file trên host.
- câu lệnh :
```
docker run -v /path/on/host:/path/in/container IMAGE
```
- sử dụng muont
```
docker run --mount type=bind,source=/path/on/host,target=/path/in/container IMAGE
```
- ví dụ : Gắn thư mục web vào container Nginx để có thể chỉnh sửa file trên host mà không cần rebuild container:
```
docker run -d --name my_nginx -v $(pwd)/html:/usr/share/nginx/html -p 8080:80 nginx
```
- vì dữ liệu giữa host và container sẽ được đồng bộ nên thư mục trên host cần có quyền
```
sudo chmod -R 777 /home/user/data
```
- khi nào dùng bind muont:
  + Khi cần đồng bộ file giữa host và container ngay lập tức.
  + Khi làm development, giúp sửa code mà không cần rebuild container.
  +  Khi chạy web server, giúp cập nhật file mà không cần restart container.
  +  Khi muốn log file ra host để dễ đọc.
  +  Khi làm backup hoặc restore dữ liệu từ container ra host.
#### docker cp và docker commit
- có thể đưa dữ liệu từ host vào container đang chạy băng docker cp
```
docker cp /home/user/data my_container:/app/data
```
- đưa dữ liệu từ container ra mà chưa muont
```
docker cp my_container:/app/data /home/user/data_backup
```
- sử dụng docker commit để tạo image mới với toàn bộ trạng thái của container đang chạy
```
docker commit my_container my_backup_image
```
- tạo volume
- chạy lại container mới với image vừa tạo
# docker-compose
#### vì sao cần tự động hóa 
- môi trường có nhiều container
- chuẩn hóa môi trường
- mô tả lại toàn hệ thống trong 1 file
#### sử dụng
- file mô tả bằng YAML
- CLI docker-compose
#### viết docker-compose.yml
- chủ yếu dùng trong test/dev. môi trường prod thường dùng stack, k8s
- đặt tên là docker-compose.yml. nếu muốn sử dụng tên khác thì cần dùng option
```
docker-compose -f <filename>
```
- 2 câu lệnh :
```
docker-compose up -d
docker-compose downd
```
- Xem logs
```
docker compose logs -f
```
- Xem danh sách container
```
docker compose ps
```
- Restart một service
```
docker compose restart web
```
#### cách viết file mô tả
- template :
```
version: "3.8" #Xác định phiên bản YAML cho Docker Compose. Phiên bản mới nhất thường là 3.8.

services: # Trong services, bạn định nghĩa từng container ứng dụng.
  nginx: #name container cũng như DNS name để giao tiếp với các container khác
    image: nginx # image
    ports: # port NAT
      - "8080:80"
    volumes: # cấu hình volume cho container
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./src:/var/www/html
    depends_on:  # tùy chọn dùng để xác định thứ tự khởi động các services.ở đây nginx sẽ chỉ được bắt đầu khi php khởi động
      - php
    networks: # cấu hình mạng cho container kết nối vào mạng nào
      - app_network

  php:
    build: # Tự build image từ Dockerfile
      context: . # Thư mục chứa Dockerfile
      dockerfile: Dockerfile  # Tên file Dockerfile . sau khi build xong sẽ run container luôn
    volumes:
      - ./src:/var/www/html
    networks:
      - app_network

  db:
    image: mysql:5.7
    environment: Thiết lập biến môi trường
      - MYSQL_ROOT_PASSWORD=root
    networks:
      - app_network
    volumes:
      - db_data:/var/lib/mysql

volumes: # create volume. nếu không tạo ở đây các cấu hình volume bên trên sẽ không tìm thấy 
  db_data:

networks: # create network. nếu không tạo ở đây các cấu hình network bên trên sẽ không tìm thấy 
  app_network: #name volume
    driver:  # kiểu mạng
```
- lưu ý :
    + Dùng .env Để Quản Lý Biến Môi Trường
    ```
    services:
      db:
        image: mysql
        environment:
        - MYSQL_ROOT_PASSWORD=${MYSQL_PASSWORD}
        env_file:
        - .env
    ```
    + tạo file .env : MYSQL_PASSWORD=supersecret
- Dùng "restart: unless-stopped" để container tự chạy lại khi bị lỗi.
