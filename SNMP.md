##Báo Cáo Tìm Hiểu SNMP


**I) Lý Thuyết**

**1) Tìm hiểu về hai giao thức giám sát Poll và Alert**

Trước khi đi tìm hiều về SNMP và cách thức nó làm việc ra sao ta cần tìm hiểu về hai giao thức giám sát hệ thống đó là **Poll** và **Alert** 

**Phương Thức Poll**
Nguyên tắc hoạt động : Trung tâm giám sát (manager) sẽ thường xuyên hỏi thông tin của thiết bị cần giám sát (device). Nếu Manager không hỏi thì Device không trả lời, nếu Manager hỏi thì Device phải trả lời. Bằng cách hỏi thường xuyên, Manager sẽ luôn cập nhật	 được thông tin mới nhất từ Device.

![Caption for the picture.](https://i.imgur.com/OGRNnHk.png)

**Phương thức Alert**
Nguyên tắc hoạt động : Mỗi khi trong Device xảy ra một sự kiện (event) nào đó thì Device sẽ tự động gửi thông báo cho Manager, gọi là Alert. Manager không hỏi thông tin định kỳ từ Device. Device chỉ gửi những thông báo mang tính sự kiện chứ không gửi những thông tin thường xuyên thay đổi, nó cũng sẽ không gửi Alert nếu chẳng có sự kiện gì xảy ra. Chẳng hạn khi một port down/up thì Device sẽ gửi cảnh báo, còn tổng số byte truyền qua port đó sẽ không được Device gửi đi vì đó là thông tin thường xuyên thay đổi. Muốn lấy những thông tin thường xuyên thay đổi thì Manager phải chủ động đi hỏi Device, tức là phải thực hiện phương thức Poll.
![hannv.](https://i.imgur.com/qozAB8v.png)

**2) Giới thiệu về giao thức SNMP**

SNMP là "giao thức quản lý mạng đơn giản", dịch từ cụm từ “Simple Network Management Protocol”. Thế nào là giao thức quản lý mạng đơn giản ? Giao thức là một tập hợp các thủ tục mà các bên tham gia cần tuân theo để có thể giao tiếp được với nhau. Trong lĩnh vực thông tin, một giao thức quy định cấu trúc, định dạng (format) của dòng dữ liệu trao đổi với nhau và quy định trình tự, thủ tục để trao đổi dòng dữ liệu đó. Nếu một bên tham gia gửi dữ liệu không đúng định dạng hoặc không theo trình tự thì các bên khác sẽ không hiểu hoặc từ chối trao đổi thông tin. SNMP là một giao thức, do đó nó có những quy định riêng mà các thành phần trong mạng phải tuân theo.

**Ưu điểm của SNMP**

- SNMP được thiết kế để đơn giản hóa quá trình quản lý các thành phần trong mạng. Nhờ đó các phần mềm SNMP có thể được phát triển nhanh và tốn ít chi phí.

- SNMP được thiết kế để có thể mở rộng các chức năng quản lý, giám sát. Không có giới hạn rằng SNMP có thể quản lý được cái gì. Khi có một thiết bị mới với các thuộc tính, tính năng mới thì người ta có thể thiết kế “custom” SNMP để phục vụ cho riêng mình

- SNMP được thiết kế để có thể hoạt động độc lập với các kiến trúc và cơ chế của các thiết bị hỗ trợ SNMP. Các thiết bị khác nhau có hoạt động khác nhau nhưng đáp ứng SNMP là giống nhau. 

**3) Các khái niệm và thành phần quan trọng trong SNMP**

Kiến trúc SNMP gồm hai thành phần: Network management station và network element.

- Network management station thường là một máy tính chạy phần mềm SNMP Management dùng để giám sát tập trung các network element.

- Network element là các thiết bị, máy tính, hoặc phần mềm tương thích SNMP và được quản lý bởi network management station. Như vậy element có thể là device, host và application. 

![hannv.](https://i.imgur.com/seEYLlF.png)

- Một management station có thể quản lý nhiều element, một element cũng có thể được quản lý bởi nhiều management station.

- Ngoài ra còn có khái niệm SNMP agent. SNMP agent là một tiến trình (process) chạy trên network element, có nhiệm vụ cung cấp thông tin của element cho station, nhờ đó station có thể quản lý được element. Chính xác hơn là application chạy trên station và agent chạy trên element mới là 2 tiến trình SNMP trực tiếp liên hệ với nhau.

![hannv.](https://i.imgur.com/y7yBCRQ.png)

**Khái niệm ObjectID**

Một thiết bị hỗ trợ SNMP có thể cung cấp nhiều thông tin khác nhau, mỗi thông tin đó gọi là một object
Ví dụ:

- Máy tính có thể cung cấp các thông tin : tổng số ổ cứng, tổng số port nối mạng, tổng số byte đã truyền/nhận, tên máy tính, tên các process đang chạy, ....

- Router có thể cung cấp các thông tin : tổng số card, tổng số port, tổng số byte đã truyền/nhận, tên router, tình trạng các port của router, ....

- Mỗi object có một tên gọi và một mã số để nhận dạng object đó, mã số gọi là Object ID (OID). VD:

+) Tên thiết bị được gọi là sysName, OID là 1.3.6.1.2.1.1.5 4 .
+) Tổng số port giao tiếp (interface) được gọi là ifNumber, OID là 1.3.6.1.2.1.2.1
+) Địa chỉ Mac Address của một port được gọi là ifPhysAddress, OID là 1.3.6.1.2.1.2.2.1.6.

Một object chỉ có một OID, chẳng hạn tên của thiết bị là một object. Tuy nhiên nếu một thiết bị lại có nhiều tên thì làm thế nào để phân biệt ? Lúc này người ta dùng thêm 1 chỉ số gọi là “scalar instance index” (cũng có thể gọi là “sub-id”) đặt ngay sau OID. 
Ví dụ : Tên thiết bị được gọi là sysName, OID là 1.3.6.1.2.1.1.5, nếu thiết bị có 2 tên thì chúng sẽ được gọi là sysName.0 & sysName.1 và có OID lần lượt là 1.3.6.1.2.1.1.5.0 & 1.3.6.1.2.1.1.5.1.

Để lấy một thông tin có OID đã chuẩn hóa thì SNMP application phải gửi một bản tin SNMP có chứa OID của object đó cho SNMP agent, SNMP agent khi nhận được thì nó phải trả lời bằng thông tin ứng với OID đó.

VD : Muốn lấy tên của một PC chạy Windows, tên của một PC chạy Linux hoặc tên của một router thì SNMP application chỉ cần gửi bản tin có chứa OID là 1.3.6.1.2.1.1.5.0. Khi SNMP agent chạy trên PC Windows, PC Linux hay router nhận được bản tin có chứa OID 1.3.6.1.2.1.1.5.0, agent lập tức hiểu rằng đây là bản tin hỏi sysName.0, và agent sẽ trả lời bằng tên của hệ thống. Nếu SNMP agent nhận được một OID mà nó không hiểu (không hỗ trợ) thì nó sẽ không trả lời.

![hannv.](https://i.imgur.com/uBmusAl.png)

**Management Information Base**
MIB (cơ sở thông tin quản lý) là một cấu trúc dữ liệu gồm các đối tượng được quản lý (managed object), được dùng cho việc quản lý các thiết bị chạy trên nền TCP/IP. MIB là kiến trúc chung mà các giao thức quản lý trên TCP/IP nên tuân theo, trong đó có SNMP. MIB được thể hiện thành 1 file (MIB file), và có thể biểu diễn thành 1 cây (MIB tree). MIB có thể được chuẩn hóa hoặc tự tạo

![hannv.](https://i.imgur.com/aMPB3B8.png)
	
Một node trong cây là một object, có thể được gọi bằng tên hoặc id. 
Ví dụ:

- Node iso.org.dod.internet.mgmt.mib-2.system có OID là 1.3.6.1.2.1.1, chứa tất cả các object liên quan đến thông tin của một hệ thống như tên của thiết bị (iso.org.dod.internet.mgmt.mib- 2.system.sysName hay 1.3.6.1.2.1.1.5).

- Các OID của các hãng tự thiết kế nằm dưới iso.org.dod.internet.private.enterprise. Ví dụ : Cisco nằm dưới iso.org.dod.internet.private.enterprise.cisco hay 1.3.6.1.4.1.9, Microsoft nằm dưới iso.org.dod.internet.private.enterprise.microsoft hay 1.3.6.1.4.1.311. Số 9 (Cisco) hay 311 (Microsoft) là số dành riêng cho các công ty do IANA cấp 5 . Nếu Cisco hay Microsoft chế tạo ra một thiết bị nào đó, thì thiết bị này có thể hỗ trợ các MIB chuẩn đã được định nghĩa sẵn (như mib-2) hay hỗ trợ MIB được thiết kế riêng. Các MIB được công ty nào thiết kế riêng thì phải nằm bên dưới OID của công ty đó.

Muốn hiểu được một OID nào đó thì bạn cần có file MIB mô tả OID đó. Một MIB file không nhất thiết phải chứa toàn bộ cây ở trên mà có thể chỉ chứa mô tả cho một nhánh con. Bất cứ nhánh con nào và tất cả lá của nó đều có thể gọi là một mib.

**4) Các bản tin trong SNMP**
Giao thức SNMPv1 có 5 phương thức hoạt động, tương ứng với 5 loại bản tin như sau :

| Bản tin | Mục đích |
| ------ | ------ |
| GetRequest | Manager gửi GetRequest cho agent để yêu cầu agent cung cấp thông tin nào đó dựa vào ObjectID (trong GetRequest có chứa OID)|
| GetNextRequest | Manager gửi GetNextRequest có chứa một ObjectID cho agent để yêu cầu cung cấp thông tin nằm kế tiếp ObjectID đó trong MIB. |
| SetRequest| Manager gửi SetRequest cho agent để đặt giá trị cho đối tượng của agent dựa vào ObjectID.  |
| GetResponse| Agent gửi GetResponse cho Manager để trả lời khi nhận được GetRequest/GetNextRequest |
| Trap | Agent tự động gửi Trap cho Manager khi có một sự kiện xảy ra đối với một object nào đó trong agent. |


![hannv.](https://i.imgur.com/q4YWNY5.png)

**6) Cơ chế bảo mật**

Một SNMP management station có thể quản lý/giám sát nhiều SNMP element, thông qua hoạt động gửi request và nhận trap. Tuy nhiên một SNMP element có thể được cấu hình để chỉ cho phép các SNMP management station nào đó được phép quản lý/giám sát mình. Các cơ chế bảo mật đơn giản này gồm có : community string, view và SNMP access control list.

**Community String**

Community string là một chuỗi ký tự được cài đặt giống nhau trên cả SNMP manager và SNMP agent, đóng vai trò như “mật khẩu” giữa 2 bên khi trao đổi dữ liệu. Community string có 3 loại : Read-community, Write-Community và Trap-Community.

Khi manager gửi GetRequest, GetNextRequest đến agent thì trong bản tin gửi đi có chứa Read- Community. Khi agent nhận được bản tin request thì nó sẽ so sánh Read-community do manager gửi và Read-community mà nó được cài đặt. Nếu 2 chuỗi này giống nhau, agent sẽ trả lời; nếu 2 chuỗi này khác nhau, agent sẽ không trả lời.

Write-Community được dùng trong bản tin SetRequest. Agent chỉ chấp nhận thay đổi dữ liệu khi write- community 2 bên giống nhau.

Trap-community nằm trong bản tin trap của trap sender gửi cho trap receiver. Trap receiver chỉ nhận và lưu trữ bản tin trap chỉ khi trap-community 2 bên giống nhau, tuy nhiên cũng có nhiều trap receiver được cấu hình nhận tất cả bản tin trap mà không quan tâm đến trap-community.

**View**

Khi manager có read-community thì nó có thể đọc toàn bộ OID của agent. Tuy nhiên agent có thể quy định chỉ cho phép đọc một số OID có liên quan nhau, tức là chỉ đọc được một phần của MIB. Tập con của MIB này gọi là view, trên agent có thể định nghĩa nhiều view. Ví dụ : agent có thể định nghĩa view interfaceView bao gồm các OID liên quan đến interface, storageView bao gồm các OID liên quan đến lưu trữ, hay AllView bao gồm tất cả các OID.

**SNMP access control list**
Khi manager gửi không đúng community hoặc khi OID cần lấy lại không nằm trong view cho phép thì agent sẽ không trả lời. Tuy nhiên khi community bị lộ thì một manager nào đó vẫn request được thông tin. Để ngăn chặn hoàn toàn các SNMP manager không được phép, người quản trị có thể dùng đến SNMP access control list (ACL).

**II) Cấu hình Lab**

**Mô hình Lab**

![hannv.](https://i.imgur.com/RaKy139.png)


**Cấu hình trên máy SNMP Manager**

Trên máy triển khai SNMP Manager ta cũng cài đặt thêm phần mềm Cacti để xử các thông tin thu thập được SNMP Agent thành dạng đồ thị và tích hợp thêm các tính năng nâng cao như: cảnh báo...

Trước tiên cần update
```sh
 apt-get update
```
Cài đặt SNMP, SNMPD, và RRDtools:
```sh
 apt-get install snmp snmpd rrdtool
```
Caì đặt Cacti:

```sh
apt-get install cacti cacti-spine
```
Chon apache2 khi có dialog sau:

![hannv.](https://i.imgur.com/RYXuZHF.png)

Chọn yes

![hannv.](https://i.imgur.com/FqcpgM9.png)

Khơi động lại các dịch vụ :

```sh
service snmpd restart
service mysql restart
service apache2 restart
```

Cấu hình Web Cacti:

Truy cập theo đường link sau:

```sh
http://your-server_ip-address/cacti
```
![hannv.](https://i.imgur.com/HxnX9Vm.png)

Chọn New Install:

![hannv.](https://i.imgur.com/IGEKqvm.png)

Kiểm tra các giá trị 

![hannv.](https://i.imgur.com/bJmbUbb.png)


Nhập username password mặc định: admin/admin

![hannv.](https://i.imgur.com/CeUScfI.png)

Ta được trang home của Cacti như sau

![hannv.](https://i.imgur.com/WCJRl0y.png)

Bây giờ điều ta cần là phải cấu hình để SNMP Agent gửi các thông số hệ thống lên cho SNMP Manager để Cacti lấy các thông số đó xuất ra dạng biểu đồ các bước cấu hình như sau:

Truy cập theo đường dẫn 

```sh
/etc/snmp/snmpd.conf
```

Cần sửa cấu hình các thông số sau
```sh
agentAddress udp:161,udp6:[::1]:161
rocommunity public localhost
rocommunity public 10.0.2.0/24
```
Còn lại các thông số khác ta có thể để mặc định 

**Cấu hình tại máy cần giám sát (SNMP Agent)**

Cần cài đặt các gói SNMP cần thiết
```sh
 apt-get -y install snmp snmpd snmp-mibs-downloader
```
Truy cập theo đường dẫn sau:

```sh
vi /etc/snmp/snmp.conf
```	
Cần chỉnh sửa các phần cấu hính sau
```sh
agentAddress udp:161,udp6:[::1]:161
rocommunity public  localhost
rocommunity public  10.0.2.0/24
```
Kiểm tra kết nối giữa SNMP Manager và SNMP Agent

```sh
snmpwalk -v 2c -c Serverworld localhost system
```	
Không có lỗi ta tiến hành các bước tiếp theo 

Đăng nhập vào trang quản trị cacti : http://your_ip/cacti chọn Device

![hannv.](https://i.imgur.com/hHoaU3Z.png)

Sau đó click nút Add và cấu hình Device như sau:

![hannv.](https://i.imgur.com/ewXO56A.png)	

Lưu thông số Device lại và quay lại màn hình chính chọn Graph Trees > Default Trees 

![hannv.](https://i.imgur.com/o7MxZnT.png)	

Chọn Add và thêm thông số host như sau rồi lưu cấu hình lại 

![hannv.](https://i.imgur.com/Gs3ViCI.png)

Click New Graph và lựa chọn các thông số cần giám sát như CPU, Load, Memory 

Đơị một khoảng thời gian để các thông số được xử lý ta có các đồ thị như sau:

![hannv.](https://i.imgur.com/u7rXHdd.png)


**Cấu hình cảnh báo**
Một cấu hình quan trọng nữa đó là cấu hình cảnh báo về Email cho người quản trị, trên cacti hỗ trợ điều đó ta cấu hình theo các bước sau:

Trước tiên cần downloadPlugin và giải nén:
```sh
 wget -P /usr/share/cacti/site/plugins http://docs.cacti.net/_media/plugin:settings-v0.71-1.tgz
 tar zxvf /usr/share/cacti/site/plugins/plugin:settings-v0.71-1.tgz -C /usr/share/cacti/site/plugins 
```
Đăng nhập vào giao diện admin cacti 

![hannv.](https://i.imgur.com/RJrO9Zs.png)

Chỉnh về như sau là hoàn thành 

![hannv.](https://i.imgur.com/TbWul0k.png)

Vào Mục Settings > Cấu hình Mail/DNS

![hannv.](https://i.imgur.com/jXfE9WE.png)

Click Send and test nếu kết quá Success là thành công 
![hannv.](https://i.imgur.com/nia2dur.png)

Vào mail kiểm tra có thư test cảnh báo như sau:

![hannv.](https://i.imgur.com/H8aANrD.png)

Ta cần cấu hình thêm plugin Threshold để có thêm các khả năng quản lý các cảnh báo tốt hơn..	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	