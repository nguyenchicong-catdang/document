## list port
```bash
sudo ss -tulpn | grep LISTEN
```
## xem chi tiết cụ thể 1 port
```bash
sudo lsof -i :5173
```
## Cách Kill port nhanh (Dùng PID)
kill -9 <MÃ_PID>

pid=15592

```bash
kill -9 15592
```
## tiêu diệt theo tên tiến trình
```bash
killall -9 node;
killall -9 php8.3
```

## # Kill các tiến trình đang chiếm port của Vite và PHP server
sudo fuser -k 5173/tcp 8000/tcp 2>/dev/null

sudo killall -9 node php8.3 2>/dev/null