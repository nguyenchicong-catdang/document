# setup-ws.sh
nano ~/scripts/setup-ws.sh
# cai dat terminal
wsl.exe -d Ubuntu-24.04 -e bash -i /home/cong/scripts/setup-ws.sh
# viet script
```bash
#!/bin/bash
## -e bash -i -> sẽ chay từng lệnh như gõ terminal
# Tiêu diệt mọi tiến trình node và php8.3 bằng quyền sudo
#killall -9 node php8.3 2>/dev/null
# kill nguy hiểm nên dùng fuer
fuser -k 8000/tcp 5173/tcp 2>/dev/null
# vao thu muc ws -> workspace
cd /home/cong/ws

# Mở VS Code
code ws.code-workspace

# Chạy server (Lệnh này sẽ giữ terminal để hiện log)
composer run dev

# --- BƯỚC 4: GIỮ TƯƠNG TÁC ---
exec bash
```