# fix loi dang nhap
## https (1) -> http (0)
secure                0
```bash
sudo cat /usr/local/lsws/admin/conf/admin_config.conf
```
## can ghi vao session admin
```bash
sudo chown -R nobody:nogroup /usr/local/lsws/admin/conf
sudo chmod -R 755 /usr/local/lsws/admin/conf
```
# doc log
```bash
sudo tail /usr/local/lsws/logs/error.log
sudo tail /usr/local/lsws/logs/access.log
```
