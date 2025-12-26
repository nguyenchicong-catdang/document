# thêm vào nhóm
## groud chính
```bash
sudo chown -R cong:cong /home/cong/openLiteSpeed/VD1
sudo chmod -R 775 /home/cong/openLiteSpeed/VD1
```
## thêm lsadm vào nhóm cong
```bash
sudo usermod -aG cong lsadm
```
## kiểm tra

```bash
groups lsadm
```