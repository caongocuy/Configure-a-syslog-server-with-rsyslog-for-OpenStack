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

![img](http://i.imgur.com/7oKIMQe.png "img")

Mô hình có 4 node và các card mạng thiết lập như hình vẽ.

<a name="thuchien"></a>

[3. Thực hiện Lab](#thuchien)