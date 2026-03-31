 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/README.md b/README.md
index 382cc1e7e430e2002884b4edf5155c2869881f11..724953a2e694bbd373b63a7597722a48365943b7 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,54 @@
-# my-vps-duonf
-VPS Windows RDP (Ngrok) - CodeCloud
+# Minecraft Server trên GitHub (qua Codespaces)
+
+> ⚠️ **Lưu ý quan trọng:** GitHub **không phải** nền tảng hosting server game 24/7. Bộ cấu hình này giúp bạn chạy server Minecraft **tạm thời** bằng GitHub Codespaces để test/chơi ngắn hạn.
+
+## Bạn sẽ có gì
+- Script tự tải và chạy **PaperMC**.
+- Cấu hình sẵn để chạy trong GitHub Codespaces.
+- Tự mở cổng mặc định `25565`.
+
+## 1) Tạo repo trên GitHub
+1. Push toàn bộ project này lên GitHub.
+2. Mở repo trên web GitHub.
+
+## 2) Chạy bằng Codespaces
+1. Bấm **Code** → **Codespaces** → **Create codespace on main**.
+2. Chờ máy ảo khởi động xong.
+3. Trong terminal của Codespace, chạy:
+
+```bash
+./scripts/start-server.sh
+```
+
+4. Lần chạy đầu sẽ hỏi chấp nhận EULA. Script sẽ tự tạo `server/eula.txt` với `eula=true`.
+5. Khi server chạy xong, bạn sẽ thấy dòng kiểu:
+
+```text
+Done (...)! For help, type "help"
+```
+
+## 3) Kết nối vào server
+Vì Codespaces không public cổng game theo kiểu trực tiếp như VPS, bạn cần tunnel TCP. Gợi ý:
+- [playit.gg](https://playit.gg/)
+- [ngrok TCP](https://ngrok.com/docs/using-ngrok-with/minecraft/)
+
+Sau khi có địa chỉ tunnel (ví dụ `abc.playit.gg:12345`), dùng địa chỉ đó để vào game.
+
+## Cấu hình nhanh
+Bạn có thể thay phiên bản Minecraft/Paper trong file `.env`:
+
+```env
+MC_VERSION=1.20.6
+PAPER_BUILD=151
+MEMORY=2G
+```
+
+## Lệnh hữu ích
+Trong terminal server:
+- `op <ten_nguoi_choi>`: cấp quyền admin.
+- `stop`: tắt server an toàn.
+
+## Giới hạn cần biết
+- Codespaces có thể sleep/stop khi không dùng.
+- Không phù hợp để host public dài hạn.
+- Muốn server ổn định 24/7 nên dùng VPS/cloud thực thụ (Oracle Cloud/AWS Lightsail/DigitalOcean).
 
EOF
)
