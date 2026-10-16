# DevOps Final – Đề 003: Quản lý công việc

Bộ mã nguồn khởi đầu cho bài thi **DevOps Fundamentals – Cuối kỳ**.
Ứng dụng hiển thị **danh sách công việc** lấy từ MySQL thông qua Spring Boot, trả về **dạng JSON** qua Nginx (reverse proxy).

## Kiến trúc

```
Trình duyệt ── http://<IP_máy_chủ>:<APP_PORT>
    ▼
[nginx]  nginx:1.27-alpine        GET /*  → app:8080
    ▼
[app]    Spring Boot 4 (JRE 21)   GET /api/tasks → mysql:3306
    ▼
[mysql]  mysql:8.0                volume mysql_data, KHÔNG public cổng mysql
```

## Cấu trúc thư mục

Đổi tên thư mục/repository thành `devops-final-de003-<họ-tên>` (ví dụ `devops-final-de003-nguyenvana`).
Dấu ★ là phần sinh viên phải làm.

```
devops-final-de003-nguyenvana/     ← Mã nguồn Spring Boot 4 (Java 21, Gradle) – CÓ SẴN
├── src/main/java/com/ptit/devops/task/   ← Controller, Entity, Repository
├── src/main/resources/application.properties
├── build.gradle                   ← Build ra file build/libs/app.jar
├── settings.gradle
├── Dockerfile                     ← ★ FILE KHUNG: tự hoàn chỉnh phần còn thiếu
├── .dockerignore                  ← CÓ SẴN
├── nginx/default.conf             ← ★ FILE KHUNG: tự đổi PORT (tránh trùng nhau)
├── database/init.sql              ← Bảng tasks + 8 bản ghi mẫu – CÓ SẴN
├── docker-compose.yml             ← ★ FILE KHUNG: mysql có sẵn, điền TODO 1–5
├── .github/workflows/ci-cd.yml    ← ★ FILE KHUNG: điền TODO 1–6
├── student.env                    ← ★ ĐIỀN THÔNG TIN SINH VIÊN
├── screenshots/                   ← ★ Ảnh minh chứng (Phần 5)
├── submission-content/            ← ★ Toàn bộ tệp minh chứng khi nộp bài
├── .gitignore                     ← CÓ SẴN
└── README.md
```

Phần **sinh viên phải làm** (xem đề thi):

| File | Việc cần làm |
|------|--------------|
| `student.env` | Điền thông tin sinh viên và thông số triển khai |
| `Dockerfile` | **Hoàn chỉnh**: multi-stage build, stage `build` (Gradle + JDK 21) và stage runtime (JRE 21) |
| `docker-compose.yml` | **Điền các TODO 1–5** (service `mysql` đã có sẵn) |
| `.github/workflows/ci-cd.yml` | **Điền các TODO 1–6** của pipeline build → push image → deploy |
| `screenshots/` | `01-actions.png`, `02-ghcr.png`, `03-compose-ps.png`, `04-website.png` |

## API của ứng dụng

| Method | Đường dẫn | Mô tả |
|--------|-----------|-------|
| GET | `/api/tasks` | Danh sách công việc |

## Biến môi trường mà ứng dụng cần

| Biến | Ý nghĩa |
|------|---------|
| `DB_HOST`, `DB_PORT` | Địa chỉ MySQL (mặc định `mysql`, `3306`) |
| `DB_NAME`, `DB_USER`, `DB_PASSWORD` | Database và tài khoản kết nối |
| `APP_VERSION` | Phiên bản (truyền vào lúc build image, ví dụ commit SHA) |

## Chạy thử (khi đã hoàn thiện Dockerfile và docker-compose.yml)

```bash
docker compose --env-file student.env up -d --build
docker compose --env-file student.env ps
curl http://localhost:<APP_PORT>/api/tasks
docker compose --env-file student.env down
```











 
PHẦN 5: ĐẨY CODE, ĐỔI PACKAGE SANG PUBLIC & CHỤP 4 ẢNH MINH CHỨNG (10 ĐIỂM)
5.1 Đẩy code lên GitHub
Mở PowerShell tại thư mục bài làm:

powershell
git init -b main
git add .
git commit -m "feat: complete devops project"
git remote add origin https://github.com/<USERNAME>/<REPO_NAME>.git
git push -u origin main
5.2 Đổi Package sang Public (Làm ngay sau khi job build chạy xong)
Vào trang chủ Repo GitHub -> cột phải bấm vào Packages -> chọn package app.
Bấm Package settings (bên phải) -> cuộn xuống đáy Danger Zone -> bấm Change visibility -> chọn Public -> gõ lại tên package xác nhận.
5.3 Chụp 4 ảnh minh chứng lưu vào thư mục screenshots/:
01-actions.png: Chụp màn hình tab Actions trên GitHub hiển thị cả 2 job build và deploy có tích xanh ✅.
02-ghcr.png: Chụp trang GitHub Packages hiển thị package app có đủ 2 tag: latest và <commit-SHA>.
03-compose-ps.png: Chụp màn hình Terminal VPS khi gõ lệnh docker compose --env-file student.env ps (thấy cả 3 container đang Up / healthy).
04-website.png: Mở trình duyệt gõ: http://221.121.3.43:8082/api/labs, chụp lại toàn bộ màn hình trình duyệt (thấy rõ URL IP VPS và nội dung JSON).
PHẦN 6: ĐÓNG GÓI NỘP BÀI LÊN HỆ THỐNG RAIA
Copy toàn bộ các ảnh trong screenshots/ vào thư mục submission-content/.
Mở Bitvise SSH Client -> bấm nút New SFTP Window (hoặc dùng FileZilla/WinSCP):
Tải toàn bộ thư mục bài làm từ VPS (/home/loibd/devops-final-de001-...) về máy tính cá nhân.
Nén file zip và nộp lên hệ thống RAIA theo đúng yêu cầu giám thị.