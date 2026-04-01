 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/README.md b/README.md
index 152fc3bb5a6b11f7b1a2a99a4614a250aa11ec83..c4980845e3dc6b9eec3f36ad7b111884c8c93ea3 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,128 @@
-# Hhhhhh
-VPS Windows RDP (Tailscale) - CodeCloud
+# Windows RDP VPS bằng GitHub Actions + Tailscale
+
+Repository này cung cấp workflow để khởi tạo **máy Windows tạm thời** trên GitHub Actions, bật **Remote Desktop (RDP)** và kết nối qua **Tailscale**.
+
+> Mục đích: môi trường test nhanh, demo, chạy tác vụ ngắn hạn. Không phù hợp cho production lâu dài.
+
+---
+
+## 1) Tính năng chính
+
+- Tạo máy `windows-latest` khi bấm **Run workflow**.
+- Tự động:
+  - Tạo/cập nhật tài khoản local admin cho RDP.
+  - Bật RDP + Firewall rule.
+  - Cài Tailscale và join mạng bằng `TAILSCALE_AUTHKEY`.
+- Upload artifact `result` chứa endpoint RDP dạng:
+  - `TAILSCALE_IP:3389|RDP_USERNAME`
+- Cho phép tùy chỉnh thời gian chạy (30–360 phút).
+
+---
+
+## 2) Chuẩn bị trước khi dùng
+
+### 2.1 Fork hoặc clone repo lên GitHub
+
+Bạn cần có repo chứa file workflow:
+
+- `.github/workflows/run.yml`
+
+### 2.2 Tạo 2 secrets bắt buộc
+
+Vào **GitHub repo → Settings → Secrets and variables → Actions → New repository secret**:
+
+1. `RDP_PASSWORD`
+   - Mật khẩu đăng nhập Remote Desktop.
+   - Nên dùng mật khẩu mạnh (12+ ký tự, gồm chữ hoa/thường, số, ký tự đặc biệt).
+
+2. `TAILSCALE_AUTHKEY`
+   - Tạo trong Tailscale Admin Console.
+   - Nên dùng key có expiry và scope tối thiểu cần thiết.
+
+---
+
+## 3) Cách chạy workflow
+
+1. Vào tab **Actions**.
+2. Chọn workflow **Windows RDP VPS (GitHub Actions)**.
+3. Nhấn **Run workflow**.
+4. Nhập các input (tùy chọn):
+   - `duration_minutes`: thời gian giữ máy chạy (mặc định 180, min 30, max 360).
+   - `rdp_username`: tên user RDP (mặc định `runneradmin`).
+5. Nhấn **Run workflow** để bắt đầu.
+
+---
+
+## 4) Lấy thông tin để Remote Desktop
+
+Sau khi job chạy qua bước upload artifact:
+
+1. Mở run vừa chạy.
+2. Tải artifact **`result`**.
+3. Mở file `info.txt` sẽ thấy dạng:
+
+```txt
+100.x.y.z:3389|runneradmin
+```
+
+- Host: `100.x.y.z`
+- Port: `3389`
+- Username: phía sau ký tự `|`
+- Password: giá trị secret `RDP_PASSWORD` đã cấu hình
+
+Sau đó dùng **Remote Desktop Connection (mstsc)** trên Windows để kết nối.
+
+---
+
+## 5) Cấu trúc workflow
+
+File: `.github/workflows/run.yml`
+
+Các bước chính:
+
+1. **Validate input/secrets**
+   - Kiểm tra bắt buộc có `RDP_PASSWORD`, `TAILSCALE_AUTHKEY`.
+   - Chuẩn hóa `duration_minutes` vào khoảng 30–360.
+
+2. **Setup local admin + enable RDP**
+   - Tạo user local (hoặc reset password nếu đã tồn tại).
+   - Add vào nhóm Administrators.
+   - Bật RDP trong registry + firewall.
+
+3. **Install Tailscale**
+   - Cài bản MSI chính thức từ `pkgs.tailscale.com`.
+
+4. **Connect Tailscale**
+   - Chạy `tailscale up --authkey=...`.
+   - Lấy IPv4 Tailnet.
+
+5. **Upload artifact result**
+   - Ghi `info.txt` để bạn tải xuống và kết nối.
+
+6. **Keep alive**
+   - Giữ máy online theo thời lượng đã chọn.
+
+---
+
+## 6) Lưu ý quan trọng
+
+- GitHub-hosted runner là tài nguyên tạm thời, không đảm bảo lưu dữ liệu lâu dài.
+- Không commit mật khẩu vào source code.
+- Chỉ chia sẻ thông tin truy cập cho người tin cậy.
+- Nếu cần dùng ổn định lâu dài, nên chuyển sang VPS/cloud thực thụ.
+
+---
+
+## 7) Troubleshooting nhanh
+
+- **Lỗi thiếu secret**: kiểm tra lại đã tạo `RDP_PASSWORD` và `TAILSCALE_AUTHKEY` đúng tên chưa.
+- **Không thấy IP Tailscale**: kiểm tra auth key còn hạn/đúng quyền.
+- **RDP không vào được**: kiểm tra host/port/user/password và xem log workflow ở step `Setup local admin and enable RDP`.
+
+---
+
+## 8) Gợi ý mở rộng
+
+- Thêm bước cài phần mềm tự động (Chrome, VS Code, Python…).
+- Tự động gửi endpoint lên Telegram/Slack (qua webhook secret).
+- Bổ sung cleanup thủ công (logout Tailnet, xoá user tạm).
 
EOF
)
