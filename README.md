# AI Hỗ Trợ Học Tập — EduAdvisor AI

Ứng dụng web hỗ trợ học tập bằng AI: hỏi đáp kiến thức, luyện tập, xây dựng lộ trình cá nhân và theo dõi tiến độ. Giao diện tiếng Việt, sử dụng React + TypeScript + Vite; API sử dụng Python FastAPI và SQLite.

## Chức năng

- Chat cố vấn học tập, hiển thị công thức với KaTeX và hỗ trợ phản hồi streaming từ backend.
- Lộ trình học tập, bài tập, hồ sơ người học và bảng theo dõi tiến độ.
- Tích hợp Google Gemini; backend có phản hồi mẫu cục bộ khi dịch vụ AI không khả dụng.
- Đăng ký, đăng nhập, xác thực email qua SMTP và giao diện quản trị.

Đây là dự án thử nghiệm. Một số nội dung, số liệu hoặc tương tác giao diện mang tính minh họa; phản hồi cục bộ không tương đương một mô hình AI chạy offline. Tính năng Gemini cần API key hợp lệ và quyền truy cập model tương ứng.

## Yêu cầu

- Python 3.10 trở lên (khuyến nghị 3.11).
- Node.js 20 trở lên và npm.
- Git; kết nối Internet để cài thư viện và gọi các dịch vụ bên ngoài.
- Không cần Apache, PHP hay MySQL của XAMPP.

## Cách chạy trên Windows (PowerShell)

### 1. Tải mã nguồn

```powershell
git clone https://github.com/NhatHao-2805/AI-HoTroHocTap.git
cd AI-HoTroHocTap
```

### 2. Cài đặt và chạy backend

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Mở `backend/.env`, thay `SECRET_KEY` bằng chuỗi ngẫu nhiên dài và đặt tài khoản quản trị riêng. Có thể tạo khóa bằng:

```powershell
python -c "import secrets; print(secrets.token_urlsafe(48))"
```

Điền Gemini và SMTP nếu sử dụng, sau đó chạy:

```powershell
.\.venv\Scripts\python.exe run.py
```

API: http://127.0.0.1:8002 — Swagger: http://127.0.0.1:8002/docs.
SQLite tự tạo tại `backend/app/eduadvisor.db` khi ứng dụng khởi động. Cấu hình đường dẫn hiện nằm trong `backend/app/database/session.py`; biến `DATABASE_URL` trong settings chưa được module này sử dụng.

### 3. Cài đặt và chạy frontend

Mở terminal thứ hai tại thư mục dự án:

```powershell
cd frontend
npm ci
npm run dev
```

Truy cập http://localhost:3000. Vite chuyển tiếp `/api` đến backend cổng **8002**; cần chạy cả hai dịch vụ.

Trên macOS/Linux, thay đường dẫn Python trong môi trường ảo bằng `.venv/bin/python`.

## Cấu hình

Xem `backend/.env.example`:

| Biến | Ý nghĩa |
| --- | --- |
| `SECRET_KEY` | Khóa ký JWT; phải thay trước khi chia sẻ dịch vụ |
| `ADMIN_USERNAME`, `ADMIN_PASSWORD` | Tài khoản quản trị |
| `ADMIN_EMAILS` | Email được cấp quyền quản trị, phân cách bằng dấu phẩy |
| `GEMINI_API_KEY` | API key cho dịch vụ Gemini phía backend |
| `DEFAULT_MODEL` | Model Gemini cần có quyền truy cập |
| `SMTP_HOST`, `SMTP_PORT` | Máy chủ và cổng gửi thư (mặc định Gmail, 465) |
| `SMTP_USER`, `SMTP_PASSWORD` | Tài khoản và mật khẩu ứng dụng SMTP để gửi OTP |

Frontend cũng có luồng gọi Gemini trực tiếp và lưu key trong trình duyệt. Key backend không tự cấu hình cho luồng frontend. Không đưa key riêng vào biến `VITE_*` khi phát hành công khai vì các giá trị đó được đóng gói vào mã trình duyệt. Kiểm tra model trong phần cài đặt hoặc `frontend/src/services/geminiService.ts` nếu gặp lỗi model/quota.

## Script chạy nhanh và chia sẻ online

Sau khi cài thư viện, có thể chạy `CHAY_CHAT_ONLINE.bat` trên Windows. Script dùng lệnh `python` trên PATH (hoặc đường dẫn Python có sẵn được ghi trong file batch); interpreter đó phải có các gói backend. Để dùng môi trường ảo đã tạo, kích hoạt nó trong terminal trước khi chạy file batch:

```powershell
.\backend\.venv\Scripts\Activate.ps1
.\CHAY_CHAT_ONLINE.bat
```

Nếu PowerShell chặn kích hoạt môi trường ảo, dùng cách chạy thủ công ở trên. Nếu muốn tạo tunnel, cần tự cài `cloudflared.exe` và đặt cạnh `start_online.py`; tệp này không đi kèm kho. Khi không có tunnel, dùng địa chỉ local/LAN do script hiển thị. Link tunnel tạm thời có thể đổi sau mỗi lần chạy.

**Chú ý:** script có thể dừng tiến trình chiếm cổng 3000/8002 và các tiến trình cloudflared trên Windows. Chỉ chạy khi đã kiểm tra các dịch vụ đang dùng cổng này. Tunnel đưa ứng dụng ra Internet; cần rà soát bảo mật trước khi bật.

## Kiểm tra và build

```powershell
cd frontend
npm run build
```

Kết quả nằm trong `frontend/dist`. `npm run preview` chỉ dùng xem bản build; proxy `/api` hiện được cấu hình cho máy chủ dev. Khi triển khai cần cấu hình reverse proxy `/api` đến FastAPI.

## Cấu trúc

```text
AI-HoTroHocTap/
├── frontend/                 # React, TypeScript, Vite, Tailwind CSS
│   └── src/                  # Trang giao diện, thành phần, dịch vụ AI
├── backend/
│   ├── app/api/              # Auth, chat, roadmap, tasks, documents, admin
│   ├── app/core/             # Cấu hình môi trường
│   ├── app/database/         # SQLAlchemy, SQLite
│   ├── app/services/         # LLM, truy xuất kiến thức, lộ trình
│   ├── .env.example          # Mẫu cấu hình, không chứa bí mật
│   ├── requirements.txt
│   └── run.py
├── CHAY_CHAT_ONLINE.bat
├── start_online.py
└── README.md
```

## Lưu ý trước khi sử dụng thực tế

- Không commit `.env`, API key, mật khẩu, cơ sở dữ liệu người dùng hoặc log; `.gitignore` đã loại các tệp này cùng `node_modules`, môi trường ảo và bản build.
- Không sử dụng tài khoản quản trị hoặc khóa JWT mặc định của mã nguồn khi đưa lên mạng.
- Các luồng xác thực Google, API cấu hình SMTP, phân quyền và CORS cần được rà soát trước khi vận hành công khai. Dự án chưa được kiểm toán bảo mật.
- Dữ liệu học tập được lưu trong SQLite và một phần trạng thái ở trình duyệt; sao lưu trước khi xóa dữ liệu hoặc đổi máy.
- Không gửi dữ liệu cá nhân nhạy cảm vào chat. Câu trả lời AI có thể sai; cần kiểm tra lại kiến thức và lời giải.
- Lỗi gửi OTP: kiểm tra cấu hình SMTP và mật khẩu ứng dụng. Lỗi API: kiểm tra backend cổng 8002. Lỗi Gemini: kiểm tra API key, quyền model và hạn mức.
- Kho chưa có giấy phép sử dụng riêng; hãy liên hệ chủ kho nếu muốn tái sử dụng hoặc phân phối.
