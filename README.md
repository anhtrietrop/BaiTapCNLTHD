# Hướng Dẫn Chạy N8N Workflow với Telegram Bot

## 📋 Mục Lục
- [Giới thiệu](#giới-thiệu)
- [Yêu cầu hệ thống](#yêu-cầu-hệ-thống)
- [Cài đặt](#cài-đặt)
- [Cấu hình Ngrok](#cấu-hình-ngrok)
- [Chạy ứng dụng](#chạy-ứng-dụng)
- [Cấu hình Telegram Bot](#cấu-hình-telegram-bot)
- [Import Workflow](#import-workflow)
- [Xử lý sự cố](#xử-lý-sự-cố)

## 🎯 Giới thiệu
Project này sử dụng N8N để tạo một workflow tự động hóa với Telegram Bot kết nối AI Agent. Workflow cho phép người dùng tương tác với bot thông qua Telegram và nhận được phản hồi từ AI.

## 💻 Yêu cầu hệ thống
- Docker Desktop đã cài đặt và đang chạy
- Git (để clone repository)
- Ngrok account (miễn phí)
- Telegram account

## 🚀 Cài đặt

### Bước 1: Clone hoặc tải project về máy

```bash
# Clone repository (nếu có link git)
git clone <repository-url>
cd Baitap1

# Hoặc download file ZIP và giải nén
```

### Bước 2: Kiểm tra cấu trúc thư mục

Đảm bảo thư mục có cấu trúc như sau:
```
Baitap1/
├── docker-compose.yml
├── n8n_data/
└── README.md
```

### Bước 3: Cấu hình thông tin đăng nhập (Tùy chọn)

Mở file `docker-compose.yml` và thay đổi thông tin đăng nhập nếu cần:

```yaml
- N8N_BASIC_AUTH_USER=admin           # Đổi username
- N8N_BASIC_AUTH_PASSWORD=password123 # Đổi password
```

## 🌐 Cấu hình Ngrok

### Bước 1: Cài đặt Ngrok

#### Windows:
1. Tải Ngrok từ: https://ngrok.com/download
2. Giải nén file ZIP
3. Mở Command Prompt hoặc PowerShell tại thư mục chứa ngrok.exe

#### macOS/Linux:
```bash
# macOS với Homebrew
brew install ngrok

# Linux
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
tar xvzf ngrok-v3-stable-linux-amd64.tgz
sudo mv ngrok /usr/local/bin
```

### Bước 2: Đăng ký và lấy Auth Token

1. Đăng ký tài khoản miễn phí tại: https://dashboard.ngrok.com/signup
2. Vào dashboard lấy auth token: https://dashboard.ngrok.com/get-started/your-authtoken
3. Cấu hình auth token:

```bash
ngrok config add-authtoken <YOUR_AUTH_TOKEN>
```

### Bước 3: Khởi chạy Ngrok

Mở terminal/command prompt mới và chạy:

```bash
ngrok http 5678
```

Bạn sẽ thấy output tương tự:
```
Session Status                online
Account                       Your Account (Plan: Free)
Forwarding                    https://abcd-1234.ngrok-free.app -> http://localhost:5678
```

**⚠️ QUAN TRỌNG**: Copy URL `https://xxxx.ngrok-free.app` (phần Forwarding)

### Bước 4: Cập nhật Webhook URL

Mở file `docker-compose.yml` và cập nhật `WEBHOOK_URL`:

```yaml
environment:
  - WEBHOOK_URL=https://YOUR-NGROK-URL.ngrok-free.app/
```

**Ví dụ**:
```yaml
- WEBHOOK_URL=https://abcd-1234.ngrok-free.app/
```

## ▶️ Chạy ứng dụng

### Bước 1: Khởi động Docker Container

Mở terminal tại thư mục project và chạy:

```bash
docker compose up -d
```

Kiểm tra container đang chạy:

```bash
docker compose ps
```

### Bước 2: Truy cập N8N

1. Mở trình duyệt và truy cập: http://localhost:5678
2. Đăng nhập với thông tin đã cấu hình:
   - Username: `admin`
   - Password: `password123`

## 🤖 Cấu hình Telegram Bot

### Bước 1: Tạo Bot với BotFather

1. Mở Telegram và tìm kiếm `@BotFather`
2. Gửi lệnh `/newbot`
3. Đặt tên cho bot (ví dụ: `My N8N Assistant`)
4. Đặt username cho bot (phải kết thúc bằng `bot`, ví dụ: `my_n8n_assistant_bot`)
5. **Copy Bot Token** mà BotFather gửi cho bạn (dạng: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`)

### Bước 2: Lấy Chat ID

#### Cách 1: Sử dụng bot GetID
1. Tìm `@userinfobot` trên Telegram
2. Gửi bất kỳ tin nhắn nào
3. Bot sẽ trả về Chat ID của bạn

#### Cách 2: Qua API
1. Gửi tin nhắn cho bot của bạn
2. Truy cập: `https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates`
3. Tìm `"chat":{"id":123456789}` trong response

## 📥 Import Workflow

### Cách 1: Import từ file JSON (nếu có)

1. Trong N8N, click vào menu (3 gạch ngang) ở góc trên bên trái
2. Chọn **Import from File**
3. Chọn file workflow JSON
4. Click **Import**

### Cách 2: Tạo workflow mới

1. Click **Add workflow** trong N8N
2. Thêm node **Telegram Trigger**:
   - Click dấu `+` → chọn **Telegram Trigger**
   - Trong **Credentials**, click **Create New**
   - Dán Bot Token vào
   - Chọn **Updates** là trigger type

3. Thêm node **AI Agent**:
   - Click dấu `+` sau Telegram Trigger
   - Tìm và chọn **AI Agent**
   - Cấu hình prompt: `You are a movie assistant.`
   - Kết nối Chat Model (Google, OpenAI, hoặc các model khác)

4. Kết nối các node với nhau

### Bước 3: Cấu hình Telegram Node

1. Click vào node **Telegram Trigger**
2. Nhập **Bot Token**
3. Click **Save** và **Activate workflow**

### Bước 4: Set Webhook

1. Trong Telegram Trigger node, click **Test step** hoặc **Execute workflow**
2. N8N sẽ tự động set webhook cho bot
3. Hoặc set thủ công qua URL:
```
https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook?url=<YOUR_NGROK_URL>/webhook/<WORKFLOW_ID>
```

## ✅ Kiểm tra hoạt động

1. Mở Telegram và tìm bot của bạn
2. Gửi tin nhắn: `/start`
3. Thử hỏi bot về phim, ví dụ: "Recommend me a good action movie"
4. Bot sẽ trả lời thông qua AI Agent

## 🔧 Xử lý sự cố

### Lỗi: Container không khởi động
```bash
# Xem logs
docker compose logs -f n8n

# Restart container
docker compose restart
```

### Lỗi: Webhook không hoạt động
1. Kiểm tra Ngrok vẫn đang chạy
2. Verify webhook URL trong docker-compose.yml đúng
3. Restart container sau khi thay đổi webhook URL:
```bash
docker compose down
docker compose up -d
```

### Lỗi: Bot không phản hồi
1. Kiểm tra workflow đã được **Activate** (công tắc màu xanh)
2. Kiểm tra Bot Token đã đúng
3. Xem execution logs trong N8N (tab **Executions**)
4. Verify webhook đã được set:
```bash
curl https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getWebhookInfo
```

### Ngrok URL thay đổi (Free tier)
- URL ngrok miễn phí sẽ thay đổi mỗi khi restart
- Phải cập nhật `WEBHOOK_URL` trong docker-compose.yml
- Restart container: `docker compose restart`
- Hoặc upgrade lên Ngrok paid để có URL cố định

## 📝 Ghi chú

- **Ngrok Free**: URL sẽ thay đổi mỗi khi restart ngrok. Cần cập nhật lại `WEBHOOK_URL` và restart container
- **Bảo mật**: Đổi username/password mặc định trong môi trường production
- **Data**: Tất cả dữ liệu workflow được lưu trong thư mục `n8n_data/`
- **Backup**: Nên backup thư mục `n8n_data/` thường xuyên

## 🛑 Dừng ứng dụng

```bash
# Dừng container nhưng giữ data
docker compose stop

# Dừng và xóa container (data vẫn được giữ trong n8n_data/)
docker compose down

# Xóa hoàn toàn (bao gồm data)
docker compose down -v
```

## 📚 Tài liệu tham khảo

- [N8N Documentation](https://docs.n8n.io/)
- [Ngrok Documentation](https://ngrok.com/docs)
- [Telegram Bot API](https://core.telegram.org/bots/api)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## 🆘 Hỗ trợ

Nếu gặp vấn đề, hãy kiểm tra:
1. Docker Desktop đang chạy
2. Ngrok đang chạy và có URL hợp lệ
3. Webhook URL trong docker-compose.yml đã cập nhật
4. Workflow đã được activate trong N8N
5. Bot Token và credentials đã đúng

---

**Chúc bạn thành công! 🎉**
