Xem video trên YouTube: https://youtu.be/B-5O_GBcbV0

*CHÚ Ý: Hướng dẫn này hướng tới người dùng cấp thấp, những người chỉ muốn chạy một nút để giúp hỗ trợ mạng Decred. Vì lý do đó, nó không đi sâu vào việc staking, thiết lập ví hoặc bất kỳ điều gì khác ngoài việc làm cho nút chạy với TOR. Hướng dẫn này sẽ được cập nhật khi cần theo các bản phát hành và điều chỉnh mới*

## Raspberry Pi Full Node với Tor Guide


#### Giới thiệu

Trong hướng dẫn này, tôi sẽ hướng dẫn bạn cách thiết lập và chạy Decred Full Node trên Raspberry Pi với Tor.

Nút đầy đủ là một chương trình xác thực đầy đủ các giao dịch và khối mà không cần phải dựa vào bên thứ ba.

Chạy một nút đầy đủ là một trong những hành động hỗ trợ mạnh mẽ nhất mà bạn có thể làm đối với giao thức phân hạng ngang hàng (peer-to-peer). Mỗi nút Decred chạy trên mạng đều bổ sung sự chắc chắn và khả năng phục hồi cho cơ chế đồng thuận.

Video này hướng đến người dùng cấp thấp mới sử dụng Raspberry Pi và Linux. Cách dễ nhất để làm theo là chạy video này cùng với tập lệnh tôi đã phát hành trên github, nơi bạn có thể sao chép và dán tất cả các lệnh cần thiết. Bạn sẽ thấy tôi thực hành trong video luôn.

Tôi muốn cảm ơn Checkmate vì bản gốc [Raspberry Pi Guide](https://medium.com/decred/running-a-decred-raspberry-pi-node-ac605b70c652), và Kozel về [hướng dẫn thiết lập TOR](https://github.com/artikozel/decred-articles/blob/master/English/howilearnedtostopworryingandlovethecli/part2-configuringtor.md).

Vậy hãy bắt đầu:

#### Bạn sẽ cần:

- Raspberry pi 3b+ hoặc cao hơn
- Bộ đổi nguồn (Dây nguồn)
- Đầu đọc thẻ SD và thẻ SD 32 Gigabyte (hoặc hơn), tôi khuyên bạn nên dùng SSD thay vì thẻ SD nếu bạn muốn có được hiệu suất và thời hạn sử dụng tốt nhất từ nút của mình, nhưng bắt đầu bằng thẻ SD là tốt nhất.
- Cáp HDMI nếu bạn muốn kết nối Pi của mình trực tiếp với màn hình hoặc TV. 

Nếu bạn chưa có Raspberry Pi, tôi khuyên bạn nên mua một bộ hoặc gói đi kèm với mọi thứ bạn cần. Chúng thường dao động từ $60 đến $100, tùy thuộc vào mô hình bạn sử dụng.

> ### Hướng dẫn bắt đầu tại đây

#### Bước 1. Tải Raspberry Pi Imager

- Cắm thẻ SD hoặc SSD vào máy tính của bạn. Chúng ta sẽ cài đặt hệ điều hành Raspberry Pi. Tải xuống chương trình có tên [Raspberry Pi Imager](https://www.raspberrypi.org/downloads/) và cài đặt nó. Chọn hệ điều hành của bạn (chế độ mặc định là được), chọn thẻ SD của bạn (cẩn thận đừng chọn bất kỳ thứ gì khác) và nhấp vào ghi.

- Sau khi hoàn thành, nhấp chuột phải vào ổ đĩa và nhấp đẩy ra.

Bạn sẽ cần quyết định xem liệu rằng mình có muốn thiết lập số pi của mình "không đầu" hay không, nghĩa là bạn truy cập số pi của mình từ xa mà không cần phải cắm chuột, bàn phím và màn hình hoặc bạn có thể chọn cắm mọi thứ và kết nối số pi của mình với TV hoặc màn hình bằng cáp HDMI. Tôi khuyên bạn nên dành thời gian tìm hiểu cách SSH vào và cấu hình VNC để dễ dàng kiểm tra trên nút của bạn.

#### Bước 2. Cài đặt không đầu

- Để thiết lập không đầu, hãy cắm lại thẻ SD hoặc SSD của bạn. **bạn sẽ cần tạo một tệp trống trong ổ đĩa khởi động có tiêu đề** ```ssh```

- Nếu bạn định **sshing vào pi không dây**, thì còn một bước nữa mà bạn cần tạo một tệp có tiêu đề ```wpa_supplicant.conf``` và nhập nội dung sau vào tài liệu:

```
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant  GROUP=netdev
update_config=1

network={
  scan_ssid=1
  ssid="your_wifi_ssid"
  psk="your_wifi_password"
  key_mgmt=WPA-PSK
}
```
Tất nhiên, bạn cần nhập tên quốc gia, ssid (Tên mạng) và mật khẩu mạng của riêng bạn.

- Lưu tệp khi bạn đã hoàn tất.

- Nhấp chuột phải vào ổ đĩa và nhấp vào đẩy ra.

- Cắm thẻ SD vào Pi của bạn, cắm nguồn, cắm cáp ethernet (nếu cần) và khởi động pi của bạn.


Nếu bạn đang thực hiện quá trình này kết nối trực tiếp số pi của mình với màn hình, bạn sẽ thấy pi khởi động và bạn sẽ thấy màn hình chính.

#### Bước 3. Putty và VNC

Nếu bạn đang thực hiện cài đặt không đầu, bạn sẽ cần tải xuống một chương trình có tên [PUTTY](https://putty.org/). Chương trình này sẽ cho phép chúng ta truy cập số pi của mình và điều khiển nó thông qua dòng lệnh sau khi nó khởi động.

- Trong PUTTY, nhập ```raspberrypi.local``` và nhấp enter. Nhấp vào kết nối để phòng ngừa.
Bạn sẽ được nhắc nhập Tên người dùng và Mật khẩu. Tên người dùng mặc định là ``` pi```  và mật khẩu là ``` raspberry```  (tất cả chữ thường).

Xin chúc mừng, bạn vừa SSH vào số pi của mình.

Nếu bạn đã cố gắng kết nối không dây và không thể tìm thấy máy chủ, bạn có thể phải cắm trực tiếp số pi của mình vào màn hình và nhập cụm mật khẩu mạng của bạn theo cách thủ công khi được nhắc. Bạn cũng có thể truy cập trực tiếp qua ethernet, đây là lựa chọn dễ dàng hơn.

Bây giờ chúng ta cần kích hoạt VNC.

- Trong dòng lệnh Run

```sudo raspi-config```

- Chọn **Interfacing Options** 

- Chọn **VNC**

- Nhấn có, rồi nhấn OK, sau đó hoàn tất.

Tiếp theo, chúng tôi sẽ thiết lập độ phân giải cho VNC. (Bước này không cần thiết, chỉ để thay đổi độ phân giải)

- Chạy:

```sudo nano /boot/config.txt```

- Thêm văn bản sau vào tài liệu:
```
framebuffer_width=1900
framebuffer_height=1024
```



Bây giờ tải xuống, cài đặt và bắt đầu chạy [VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)

- Chọn Kết nối mới từ các Tệp

- Nhập ```raspberrypi.local``` trong "Serve VNC"

- Nhấp Ok.

- Nhấp đúp vào biểu tượng kết nối để kết nối.

- Nhấp vào Ok nếu bạn được hiển thị cảnh báo bảo mật.

- Nhập tên người dùng và mật khẩu của Pi khi được nhắc. Mặc định là tên người dùng: ``` pi```  và mật khẩu: ``` raspberry```  Nhấp Ok.

#### Bước 4. Cấu hình ban đầu 

Màn hình Raspberry Pi của bạn sau đó sẽ xuất hiện trong một cửa sổ trên màn hình máy tính chính của bạn. Bạn sẽ có thể kiểm soát mọi thứ từ đó.

- Bấm cho qua cảnh báo SSH (nếu nó xuất hiện).

- Chọn quốc gia của bạn nếu cần.

- Thay đổi mật khẩu tài khoản Pi của bạn khi được nhắc

- Bạn có thể định cấu hình wi-fi của mình tại thời điểm này, nếu cần.

- Update Pi của bạn.

- Khởi động lại Pi của bạn khi được nhắc.

Nếu bạn đang làm điều này qua VNC, bạn sẽ cần kết nối lại khi pi đã khởi động lại.

Bây giờ chúng ta cần tải xuống trình cài đặt DCR.

#### Bước 5. Tải và chạy dcrinstall

- Mở Trình duyệt web (Trên cùng bên trái).

- Điều hướng đến trang web decred.org.

Cuộn xuống và tìm nút "Ổn định V1.5.1" màu xanh lục.

Các bản phát hành mới hơn sẽ là một phiên bản khác.

Nhấp vào đây sẽ đưa chúng ta đến trang phát hành github của Decred.

- Tải ```dcrinstall-linux-arm-v1.5.1```

Một lần nữa, có thể có một phiên bản mới hơn. Đảm bảo tải xuống phiên bản mới nhất.

- Chúng tôi sẽ cần đặt tệp dưới dạng tệp thực thi. Mở thiết bị đầu cuối Pi.

- Chạy ```cd ~/Downloads/```

- Chạy ```sudo chmod u+x dcrinstall-linux-arm-v1.5.1```

- Điều hướng đến thư mục tải xuống của bạn. Nhấp đúp vào trình cài đặt, thực thi trình cài đặt trong Terminal

Sẽ mất một vài phút để tải xuống và thiết lập tất cả các tệp. Một thư mục có tên ./decred hiện đã được đặt trong thư mục chính của bạn. 

Nó có thể yêu cầu bạn nhập mật khẩu cho một ví mới. Gõ mật khẩu. Hãy nhớ rằng, chúng tôi chỉ sử dụng cái này như một nút chứ không phải một chiếc ví.

- Nhập n cho dữ liệu công khai

- Nhấn n cho Seed

- Chúng tôi không sử dụng nó như một ví tiền, vì vậy đừng viết seed ra.

- Đánh ok cho seed.


Chờ cho nó hoàn thành.


Bây giờ chúng ta có thể bắt đầu nút:

- Mở thiết bị đầu cuối.

- Chạy:
 
```
cd ./decred/decred-linux-arm-v1.5.1
./dcrd
```

Đảm bảo thay đổi số phiên bản nếu phiên bản hiện tại không còn là v1.5.1 (ví dụ: v1.6.0)

Daemon Decred sẽ khởi động và bắt đầu kết nối với các peer

Nếu bạn thấy rằng nút của mình đã khởi động thành công, hãy nhấn Control + c để chúng tôi có thể kết thúc bằng cách bật TOR.

Nếu bạn không muốn chạy TOR, hãy đảm bảo chuyển tiếp Cổng 9108. Nếu bạn chọn TOR, bạn sẽ không cần thực hiện bất kỳ chuyển tiếp cổng nào.

> ### Cài đặt và cấu hình TOR

TOR là phần mềm mã nguồn mở và miễn phí cho phép giao tiếp ẩn danh.

Nếu bạn muốn được giải thích sâu hơn về những gì tất cả các lệnh thực hiện, tôi khuyên bạn nên xem Hướng dẫn của Kozel: [hướng dẫn thiết lập TOR](https://github.com/artikozel/decred-articles/blob/master/English/howilearnedtostopworryingandlovethecli/part2-configuringtor.md)

Hãy bắt đầu.

- Mở thiết bị đầu cuối Raspberry pi:

- Chạy ```sudo apt install tor```

- Nhấn y để tiếp tục

- Chạy  ```sudo nano /etc/tor/torrc```

- Thêm văn bản sau vào đầu:
```
SocksPort 9050
SocksPort raspberrypi.local:9050
RunAsDaemon 1
DataDirectory /var/lib/tor
HiddenServiceDir /var/lib/tor/dcrd
HiddenServiceVersion 2
HiddenServicePort 9108 127.0.0.1:9108
```

Nhấn Control và X cùng lúc khi bạn hoàn tất. Lưu vào cùng một vị trí tệp.
Nhấn enter để tiếp tục.

- Khởi động lại dịch vụ TOR bằng ```sudo systemctl restart tor@default.service```

- Kiểm tra Trạng thái để xem nó có hoạt động không ```sudo systemctl status tor@default.service```

(Thoát chế độ xem bằng ctrl+C)

- Chạy ```sudo cat /var/lib/tor/dcrd/hostname```

- Lưu .onion của bạn **, chúng tôi sẽ cần nó cho bước tiếp theo.**

- Chạy ```nano .dcrd/dcrd.conf```

Chỉnh sửa phần đầu của tệp bằng:
```
proxy=127.0.0.1:9050
listen=127.0.0.1
externalip=Your-Onion-From-Above.onion
torisolation=1
```
Giữ Control+X để thoát.

Lưu file

Chạy nút Decred của bạn với

```
cd
cd ./decred/decred-linux-arm-v1.5.1
./dcrd
```
Node sẽ cần nhiều thời gian để tải xuống và đồng bộ hóa. Hiện tại Decred Blockchain có kích thước 4,2 Gigabyte.


Khi bạn bắt đầu thấy các nhật ký cho biết **(inbound)**, điều đó có nghĩa là nút của bạn đang chấp nhận các kết nối ngang hàng và bây giờ bạn chính thức là một phần của mạng Decred, giúp nó phát triển. Ngay cả khi mọi thứ được định cấu hình đúng cách, trung bình bạn sẽ không thấy các lỗi gửi đến trong vài ngày.
Mạng được thiết kế có chủ đích để ưu tiên các nút có hồ sơ theo dõi hơn các nút mới để giúp ngăn chặn những thứ như kẻ xấu kích hoạt một loạt các nút độc hại.

Nếu Pi của bạn bị mất nguồn, hãy đảm bảo khởi động lại nó và chạy lại dcrd. Bạn cũng nên sử dụng VNC để kiểm tra số Pi của mình thường xuyên. 

Hãy chắc chắn rằng bạn theo dõi Dự án Decred để không bỏ lỡ bất kỳ bản phát hành mới nào.


