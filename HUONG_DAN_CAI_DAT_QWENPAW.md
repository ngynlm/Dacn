# Hướng dẫn cài đặt QwenPaw (repo local)

Bản hướng dẫn này mô tả cách cài đặt dự án QwenPaw từ mã nguồn local trên máy cá nhân, bao gồm:
- tạo môi trường ảo Python
- build frontend console
- cài package Python
- khởi tạo config
- chạy giao diện web

## 1. Yêu cầu hệ thống

- Python: 3.11 đến 3.13
- Node.js: 22
- npm
- Git

Kiểm tra phiên bản:

```powershell
python --version
node -v
npm -v
git --version
```

Nếu chưa có Node 22, có thể dùng `nvm` hoặc cài Node 22 theo cách phù hợp.

---

## 2. Clone repo

```powershell
git clone https://github.com/agentscope-ai/QwenPaw.git
cd QwenPaw
```

---

## 3. Tạo môi trường ảo Python

```powershell
python -m venv .venv
```

Kích hoạt môi trường ảo:

```powershell
.\.venv\Scripts\Activate.ps1
```

Nâng cấp pip:

```powershell
python -m pip install -U pip
```

---

## 4. Build frontend console

Vì dự án cần build web UI trước khi chạy, nên cần chạy bước này trong thư mục `console`.

```powershell
cd console
npm ci
```

Nếu máy có RAM thấp, nên set heap lớn hơn cho Node để tránh lỗi out of memory:

```powershell
$Env:NODE_OPTIONS='--max_old_space_size=8192'
npm run build
```

Nếu build thành công, tiếp tục:

```powershell
cd ..
```

---

## 5. Copy build frontend vào package Python

```powershell
New-Item -ItemType Directory -Force -Path ".\src\qwenpaw\console" | Out-Null
Copy-Item -Recurse -Force ".\console\dist\*" ".\src\qwenpaw\console\"
```

---

## 6. Cài đặt package Python

Từ thư mục gốc repo:

```powershell
pip install -e .
```

Nếu muốn cài thêm dev/test/full dependencies:

```powershell
pip install -e ".[dev,test,full]"
```

---

## 7. Khởi tạo cấu hình mặc định

```powershell
.
.venv\Scripts\qwenpaw init --defaults --accept-security
```

> Lưu ý: Trong môi trường non-interactive hoặc PowerShell không có console phù hợp, bạn nên dùng `--defaults --accept-security` để bỏ qua prompt tương tác.

---

## 8. Chạy giao diện web

```powershell
.
.venv\Scripts\qwenpaw app --host 127.0.0.1 --port 8088
```

Sau đó mở trình duyệt và truy cập:

```text
http://127.0.0.1:8088/
```

---

## 9. Nếu gặp lỗi port 8088 đang bị chiếm

Lỗi thường xuất hiện như sau:

```text
[Errno 10048] error while attempting to bind on address ('127.0.0.1', 8088): only one usage of each socket address (protocol/network address/port) is normally permitted
```

Kiểm tra port đang đang được dùng:

```powershell
Get-NetTCPConnection -LocalPort 8088 -ErrorAction SilentlyContinue | Format-Table -AutoSize
```

Nếu thấy PID đang chiếm port, dừng tiến trình đó:

```powershell
Stop-Process -Id 14852 -Force
```

Sau đó chạy lại:

```powershell
.
.venv\Scripts\qwenpaw app --host 127.0.0.1 --port 8088
```

Nếu không muốn dừng tiến trình cũ, chạy trên port khác:

```powershell
.
.venv\Scripts\qwenpaw app --host 127.0.0.1 --port 8089
```

Mở:

```text
http://127.0.0.1:8089/
```

---

## 10. Chạy QwenPaw theo TUI (không cần giao diện web)

```powershell
.
.venv\Scripts\qwenpaw
```

---

## 11. Cấu hình model để sử dụng

Sau khi giao diện web mở:

1. Vào `Settings → Models`
2. Chọn nhà cung cấp model (OpenAI, DashScope, Ollama, LM Studio...)
3. Nhập API key nếu dùng cloud model
4. Bấm Save / Enable

Nếu dùng model local:

- Ollama: `http://127.0.0.1:11434`
- LM Studio: `http://127.0.0.1:1234/v1`

---

## 12. Ghi chú quan trọng

- Nếu đang dựng từ source, phải build frontend console trước khi chạy app.
- Repos này yêu cầu Node 22.
- Nếu máy RAM thấp, cần tăng heap cho Node: `--max_old_space_size=8192`.
- Nếu port 8088 đang bị chiếm, đổi port hoặc stop tiến trình cũ.

---

## 13. Tóm tắt lệnh chính

```powershell
git clone https://github.com/agentscope-ai/QwenPaw.git
cd QwenPaw
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -U pip
cd console
npm ci
$Env:NODE_OPTIONS='--max_old_space_size=8192'
npm run build
cd ..
New-Item -ItemType Directory -Force -Path ".\src\qwenpaw\console" | Out-Null
Copy-Item -Recurse -Force ".\console\dist\*" ".\src\qwenpaw\console\"
pip install -e .
.
.venv\Scripts\qwenpaw init --defaults --accept-security
.
.venv\Scripts\qwenpaw app --host 127.0.0.1 --port 8088
```

---

Nếu cần, tôi có thể tiếp tục viết thêm phiên bản hướng dẫn theo từng hệ điều hành (Windows / macOS / Linux) hoặc tạo thêm file `README` ngắn gọn để chia sẻ nhanh cho team.
