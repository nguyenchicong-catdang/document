# auto run composer run dev
```bash
nano ~/scripts/setup-ws.sh
```
# bash nano ~/scripts/setup-ws.sh
```bash
#!/bin/bash

# Nạp trực tiếp môi trường NVM (Nếu bạn cài Node qua NVM)
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # Nạp nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # Nạp nvm bash_completion

# Hoặc nạp bashrc một cách cưỡng ép (có thể bỏ)
source /home/cong/.bashrc

echo "--- Đang khởi động Workspace ---"

# Di chuyển và mở VS Code
cd /home/cong/ws && \
code ws.code-workspace && \
composer run dev

exec bash
```

## # Tiêu diệt mọi tiến trình node và php8.3 bằng quyền sudo
sudo killall -9 node php8.3 2>/dev/null
## don dep an toan 
fuser -k 8000/tcp 5173/tcp 2>/dev/null
