
From: [Tecmint](https://www.tecmint.com/install-samba-on-ubuntu-for-file-sharing-on-windows/)


### 1. Cài đặt samba và các gói phụ thuộc
```
sudo apt install samba samba-common python-dnspython
```

### 2. Backup file cấu hình gốc
```
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.orig
```

### 3. Kiểm tra tên workgroup của máy Windows

<img src="http://2.pik.vn/20176f5bc729-3bbb-4c59-871d-818c54816228.png">


### 4. Cấu hình folder dành cho khách

Khởi tạo thư mục lưu và phân quyền
```
sudo mkdir -p /srv/samba/anonymous_shares
```
```
sudo chmod -R 0775 /srv/samba/anonymous_shares
```
```
sudo chown -R nobody:nogroup /srv/samba/anonymous_shares
```
Thêm cấu hình trong file samba
```
sudo nano /etc/samba/smb.conf
```
```
[global]
workgroup = WORKGROUP
netbios name = ubuntu
security = user
[Anonymous]
comment = Anonymous File Server Share
path = /srv/samba/anonymous_shares
browsable =yes
writable = yes
guest ok = yes
read only = no
force user = nobody
```

Kiểm tra cài đặt trên
```
testparm
```
```
...
idmap config * : backend = tdb
[printers]
comment = All Printers
path = /var/spool/samba
create mask = 0700
printable = Yes
[print$]
comment = Printer Drivers
path = /var/lib/samba/printers
browseable = No
[Anonymous]
comment = Anonymous File Server Share
path = /srv/samba/anonymous_shares
force user = nobody
read only = No
guest ok = Yes
```
Khởi động lại samba
```
sudo systemctl restart smbd
```
Trên máy Windows, truy cập bằng \\IP-SAMBA
<img src="http://2.pik.vn/2017494800e2-ab00-49ba-9f9b-773d0cf42016.png">
<img src="http://2.pik.vn/20170f013a86-d5ca-40ba-9c38-a86dcd858f28.png">




### 5. Cấu hình folder dành cho user

Khởi tạo group, user, pasword trong samba
```
sudo addgroup smbgrp
sudo usermod aaronkilik -aG smbgrp
sudo smbpasswd -a aaronkilik
````
Lưu ý:
```
User samba và user hệ thống là khác nhau, nếu muốn đồng bộ user từ hệ thống sang samba thì dùng libpam-winbind
```
Khởi tạo thư mục lưu và phân quyền
```
sudo mkdir -p /srv/samba/secure_shares
sudo chmod -R 0770 /srv/samba/secure_shares
sudo chown -R root:smbgrp /srv/samba/secure_shares
```

Thêm cấu hình trong file samba
```
sudo nano /etc/samba/smb.conf
```
```
[Secure]
comment = Secure File Server Share
path =  /srv/samba/secure_shares
valid users = @smbgrp
guest ok = no
writable = yes
browsable = yes
```

Kiểm tra cài đặt trên
```
testparm
```
```
...
[Anonymous]
comment = Anonymous File Server Share
path = /srv/samba/anonymous_shares
force user = nobody
read only = No
guest ok = Yes
[Secure]
comment = Secure File Server Share
path = /srv/samba/secure_shares
valid users = @smbgrp
read only = No
```
Khởi động lại samba
```
sudo systemctl restart smbd
```
Trên máy Windows, truy cập bằng \\IP-SAMBA

<img src="http://2.pik.vn/2017fe19345a-0d43-4577-a277-945a5137975e.png">
<img src="http://2.pik.vn/2017e8b781a2-637f-4ec5-bce9-a4144ded3d66.png">
