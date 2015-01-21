Configure a syslog-server with rsyslog for OpenStack
======

Mục Lục

[1. Giới thiệu](#gioithieu)

[2. Mô hình cài đặt](#mohinh)
				
[3. Thực hiện Lab](#thuchien)

- [a. node syslog-server](#server)
	
- [b. node controller](#controller)

- [c. node network](#network)

- [d. node compute](#compute)

[4. Kiểm tra](#kiemtra)

=====================
<a name="gioithieu"></a>

#### 1. Giới thiệu

- OpenStack Cloud gồm rất nhiều dịch vụ khác nhau, chúng có một số lượng lớn các tập tin log. Mặt khác Cloud của bạn có rất nhiều node 
, bạn phải kiểm tra các bản ghi log trên mỗi máy chủ đó để phản ánh đúng 1 sự kiện đã xảy ra, điều này khá là bất tiện. Một giải pháp
tốt hơn được đưa ra là, tại sao không gửi các bản ghi log của các node tới một máy chủ tập trung log để tiện việc theo dõi.

<a name="mohinh"></a>

#### 2. Mô hình cài đặt

![img](http://i.imgur.com/fULW8v9.png "img")

Mô hình có 4 node và các card mạng thiết lập như hình vẽ. Ở đây tôi đùng distro là ubuntu14.04.1 cho tất cả các node.

<a name="thuchien"></a>

[3. Thực hiện Lab](#thuchien)

<a name="server"></a>

###### a. node syslog-server

- Cấu hình mở port 514 và giao thức udp, ở dòng 13 và 14 ta bỏ commend đi
```
vi /etc/rsyslog.conf
```

- Đặt template định đạng các thư mục và file cho các node syslog-client. Thường đặt ở cuối file *rsyslog.conf*

```
$template TmplAuth,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"
Auth.*  ?TmplAuth
*.*     ?TmplAuth
& ~
```

- Thay đổi quyền truy cập của thư mục / var / log cho phép syslog khả năng tạo / thay đổi các thư mục con và các tập tin.
```
cd /var && sudo chown syslog:syslog log
```

<a name="controller"></a>

###### b. node controller

*Cấu hình syslog trong các project của OpenStack*

- Nova

*nova.conf*
```
log-config=/etc/nova/logging.conf
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL0
```

- Keystone

*keystone.conf*
```
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL1
```

- Glance

*glance-api.conf* và *glance-registry.conf*

verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL1

- Cinder 

*cinder.conf*
```
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL2
```

- neutron

*neutron.conf*
```
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL3
```

Bạn cũng cần cấu hình trong file *rsyslog.d/50-default.conf* để gửi log đến node log-server, làm tương tự với node network và compute.
```
local0.info    @172.16.69.111:514
local1.info    @172.16.69.111:514
local2.info    @172.16.69.111:514
local3.info    @172.16.69.111:514
*.* @172.16.69.111:514
```

<a name="network"></a>

###### c. node network

- neutron

```
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL0
```

<a name="compute"></a>

###### d. node compute

- neutron

```
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL0
```

- nova

```
verbose = True
debug = False
use_syslog = True
syslog_log_facility = LOG_LOCAL1
```

<a name="kiemtra"></a>

#### 4. Kiểm tra

Trước khi kiểm tra chúng ta sẽ khởi động lại dịch vụ *rsyslog* trên các node

```
service rsyslog restart
```

Trên node syslog-server kiểm tra xem đã có các thư mục tạo ra tương ứng với các hostname của các node hay chưa

![img](http://i.imgur.com/LDmIem7.png "img")

Thực hiện các thao tác trên dashboard đồng thời theo dõi log báo về trên *log-server*

![img](http://i.imgur.com/SYPIdcS.png "img")

![img](http://i.imgur.com/Vs1hGxq.png "img")

![img](http://i.imgur.com/9LFc2Ld.png "img")

Tài liệu tham khảo

[Link syslog OpenStack](http://docs.openstack.org/admin-guide-cloud/content/section_manage-logs.html