- tạo file package.json
```
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "Ứng dụng web chạy trong Docker",
  "scripts": {
    "start": "node server.js",
    "build": "echo 'Giả lập quá trình build...'",
    "test": "echo 'Chạy test...'"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
- tạo file server.js
```
const express = require("express");
const app = express();
const PORT = 3000;

app.get("/", (req, res) => {
  res.send("🚀 Ứng dụng Node.js chạy trong Docker!");
});

app.listen(PORT, () => {
  console.log(`Server chạy tại http://localhost:${PORT}`);
});
```
- viết Dockerfile
```
# Sử dụng Node.js base image (Alpine để giảm dung lượng)
FROM node:18-alpine

# Thiết lập thư mục làm việc trong container
WORKDIR /app

# Copy package.json và package-lock.json để tận dụng cache
COPY package.json ./

# Cài đặt dependencies
RUN npm install

# Copy toàn bộ source code vào container
COPY . .

# Mở cổng ứng dụng chạy trên container
EXPOSE 3000

# Lệnh mặc định khi container chạy
CMD ["node", "server.js"]
```
- build image:
```
docker image build -t serverapp .
```
- chạy container bằng image vừa tạo ra
```
docker container run -p 8080:3000 -d serverapp
```
- kiểm tra truy cập từ ngoài vào container bằng port 8080 nếu xuất hiện như ảnh là thành công
![image](https://github.com/user-attachments/assets/0f09ccdd-9b9d-4a59-984b-527151f0678a)
