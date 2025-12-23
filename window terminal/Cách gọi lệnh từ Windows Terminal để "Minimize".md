# Cách gọi lệnh từ Windows Terminal để "Minimize"
wsl.exe -d Ubuntu-24.04 -e bash -i /home/cong/scripts/setup-ws.sh
## -i
-i: Rất quan trọng, nó báo cho Ubuntu rằng "Hãy chạy script này như thể tôi đang ngồi gõ trực tiếp", giúp các lệnh tương tác hoạt động mượt mà hơn.

## v2
start wt.exe -M -p "VSCode" wsl.exe -d Ubuntu-24.04 -e bash -i /home/cong/scripts/setup-ws.sh