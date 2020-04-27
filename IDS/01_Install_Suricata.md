# Install Suricata


```
yum update
yum install epel-release
yum install suricata
```

File log của Suricata mặc định nằm trong thư mục `/var/log/suricata `

Bản tin log trong file fast.log có dạng như sau:
```
Time[**][gid:sid:rev]message[**][Classification][Priority][Protocol] Source-> Destination
```
Trong đó:

- Time: Thời gian xảy ra cảnh báo
- gid: Được sử dụng để nhóm các rule khác nhau. Mặc định sẽ nhận giá trị 1 nếu - không được set trong rule
- sid: Dùng để định danh một rule. Mỗi rule có một sid duy nhất
- rev: Xác định số lần sửa đổi của 1 rule. Mỗi lần rule được thay đổi thì rev được - tăng lên
- message: Nội dung của cảnh báo
- Classification: mô tả rule thuộc loại attack nào. Classtype được định nghĩa trong - file classification.config
- Priority: Thể hiện mức độ ưu tiên của cảnh báo. Thứ tự ưu tiên cao hơn sẽ được ưu - tiên xử lý trước. Ví dụ 1 sẽ được ưu tiên hơn 2
- Protocol: Giao thức của gói tin tcp, icmp…
- Source: Địa chỉ nguồn của gói tin bao gồm IP và port
- Destination: Địa chỉ đích của gói tin bao gồm IP và port

Thư mục cấu hình mặc định: 
```
/etc/suricata
```
Thư mục chứa các tập luật: 
```
/etc/suricata/rules
```
```
[root@localhost suricata]# pwd
/etc/suricata
[root@localhost suricata]# ls -la
total 108
drwxr-x---.  3 suricata suricata   117 Apr 27 11:23 .
drwxr-xr-x. 75 root     root      8192 Apr 27 11:23 ..
-rw-r-----.  1 suricata suricata  4167 Oct  1  2019 classification.config
-rw-r-----.  1 suricata suricata  1375 Oct  1  2019 reference.config
drwxr-x---.  2 suricata suricata  4096 Apr 27 11:23 rules
-rw-r-----.  1 suricata suricata 75066 Oct  1  2019 suricata.yaml
-rw-r-----.  1 suricata suricata  1644 Oct  1  2019 threshold.config
[root@localhost suricata]#
```

Theo khuyến nghị thì ta không nên lưu rules trong thư mục: `/etc/suricata`. Thay vào đó ta sẽ lưu các Rules được định nghĩa tại `/var/lib/suricata` và `/etc/suricata` chỉ nên sử dụng để chứa các Rule mặc định do Suricata cung cấp.

# Files and Directories & Update Rules
```
Directory /etc/suricata: read/write access
Directory /var/lib/suricata/rules: read/write access
Directory /var/lib/suricata/update: read/write access
```
Add rules Emerging Threats
```
[root@localhost suricata]# suricata -V
This is Suricata version 4.1.5 RELEASE
```

```
cd /var/lib/suricata
yum install wget
wget https://rules.emergingthreats.net/open/suricata-4.1.5/emerging.rules.tar.gz
```
Giải nén
```
tar xzvf emerging.rules.tar.gz
```
```
sudo suricata-update
```

## Test File Pcap with Rules
```
suricata -c /etc/suricata/suricata.yaml -r PCAP.pcap
```
```
cat /var/log/suricata/fast.log
```

```
04/13/2020-11:19:54.172334  [**] [1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP [**] [Classification: Potential Corporate Privacy Violation] [Priority: 1] {TCP} 69.73.182.127:80 -> 10.4.13.102:49736
04/13/2020-11:21:59.804536  [**] [1:2404323:5713] ET CNC Feodo Tracker Reported CnC Server group 24 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49741 -> 91.235.129.199:443
04/13/2020-11:22:22.393572  [**] [1:2404307:5713] ET CNC Feodo Tracker Reported CnC Server group 8 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49742 -> 185.14.29.141:443
04/13/2020-11:22:46.001717  [**] [1:2404301:5713] ET CNC Feodo Tracker Reported CnC Server group 2 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49743 -> 110.232.76.39:449
04/13/2020-11:22:47.168359  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49743
04/13/2020-11:22:48.829904  [**] [1:2013028:4] ET POLICY curl User-Agent Outbound [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.4.13.102:49745 -> 18.214.132.216:80
04/13/2020-11:26:23.569929  [**] [1:2404320:5713] ET CNC Feodo Tracker Reported CnC Server group 21 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49748 -> 62.109.30.83:447
04/13/2020-11:26:23.954486  [**] [1:2021013:7] ET TROJAN ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex/Trickbot CnC) [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 62.109.30.83:447 -> 10.4.13.102:49748
04/13/2020-11:27:13.643300  [**] [1:2404317:5713] ET CNC Feodo Tracker Reported CnC Server group 18 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49751 -> 31.131.21.184:443
04/13/2020-11:27:14.343556  [**] [1:2404315:5713] ET CNC Feodo Tracker Reported CnC Server group 16 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49752 -> 203.176.135.102:8082
04/13/2020-11:27:16.786447  [**] [1:2027117:2] ET TROJAN Suspicious POST with Common Windows Process Names - Possible Process List Exfiltration [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49752 -> 203.176.135.102:8082
04/13/2020-11:27:16.786447  [**] [1:2100494:12] GPL ATTACK_RESPONSE command completed [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49752 -> 203.176.135.102:8082
04/13/2020-11:27:35.004124  [**] [1:2013926:8] ET POLICY HTTP traffic on port 443 (POST) [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49753 -> 185.90.61.62:443
04/13/2020-11:27:35.620600  [**] [1:2018358:9] ET INFO GENERIC SUSPICIOUS POST to Dotted Quad with Fake Browser 1 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49753 -> 185.90.61.62:443
04/13/2020-11:28:28.560561  [**] [1:2013926:8] ET POLICY HTTP traffic on port 443 (POST) [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49755 -> 185.90.61.62:443
04/13/2020-11:28:29.176783  [**] [1:2018358:9] ET INFO GENERIC SUSPICIOUS POST to Dotted Quad with Fake Browser 1 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49755 -> 185.90.61.62:443
04/13/2020-11:29:22.751307  [**] [1:2013926:8] ET POLICY HTTP traffic on port 443 (POST) [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49757 -> 185.90.61.62:443
04/13/2020-11:29:23.372836  [**] [1:2018358:9] ET INFO GENERIC SUSPICIOUS POST to Dotted Quad with Fake Browser 1 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49757 -> 185.90.61.62:443
04/13/2020-11:30:17.004434  [**] [1:2013926:8] ET POLICY HTTP traffic on port 443 (POST) [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49759 -> 185.90.61.62:443
04/13/2020-11:30:17.618011  [**] [1:2018358:9] ET INFO GENERIC SUSPICIOUS POST to Dotted Quad with Fake Browser 1 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49759 -> 185.90.61.62:443
04/13/2020-11:31:00.920800  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49768
04/13/2020-11:31:35.342863  [**] [1:2013926:8] ET POLICY HTTP traffic on port 443 (POST) [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49822 -> 185.90.61.62:443
04/13/2020-11:31:35.954990  [**] [1:2018358:9] ET INFO GENERIC SUSPICIOUS POST to Dotted Quad with Fake Browser 1 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49822 -> 185.90.61.62:443
04/13/2020-11:32:29.414882  [**] [1:2013926:8] ET POLICY HTTP traffic on port 443 (POST) [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49824 -> 185.90.61.62:443
04/13/2020-11:32:30.031980  [**] [1:2018358:9] ET INFO GENERIC SUSPICIOUS POST to Dotted Quad with Fake Browser 1 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.4.13.102:49824 -> 185.90.61.62:443
04/13/2020-11:38:27.512959  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49826
04/13/2020-11:45:14.667323  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49830
04/13/2020-11:52:03.349031  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49836
04/13/2020-11:58:59.604819  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49839
04/13/2020-11:59:01.277043  [**] [1:2021013:7] ET TROJAN ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex/Trickbot CnC) [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 62.109.30.83:447 -> 10.4.13.102:49840
04/13/2020-12:04:54.838591  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49894
04/13/2020-12:11:44.198602  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49896
04/13/2020-12:18:30.582056  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49905
04/13/2020-12:25:15.749407  [**] [1:2404301:5713] ET CNC Feodo Tracker Reported CnC Server group 2 [**] [Classification: A Network Trojan was detected] [Priority: 1] {TCP} 10.4.13.102:49909 -> 110.232.76.39:449
04/13/2020-12:25:16.997329  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49909
04/13/2020-12:32:04.353125  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49912
04/13/2020-12:38:52.056300  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49915
04/13/2020-12:45:37.327414  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49918
04/13/2020-12:52:23.249013  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49921
04/13/2020-12:59:10.991617  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49923
04/13/2020-13:05:59.006928  [**] [1:2011540:6] ET POLICY OpenSSL Demo CA - Internet Widgits Pty (O) [**] [Classification: Not Suspicious Traffic] [Priority: 3] {TCP} 110.232.76.39:449 -> 10.4.13.102:49933
```














