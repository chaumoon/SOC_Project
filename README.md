### **1. Tổng quan Mô hình Mạng**

Hệ thống được thiết kế theo mô hình phân vùng mạng mô phỏng thực tế, bao gồm các thành phần chính sau:

- **Vùng External (NAT):** Chứa máy tấn công (**Kali Linux**) và giao tiếp với Internet.
- **Gateway (Ubuntu):** Đóng vai trò là tường lửa, bộ định tuyến (Router) và máy chủ giám sát tập trung (Splunk + Suricata).
- **Vùng Internal (LAN Segment):** Mạng nội bộ bị cô lập, chứa các máy đích bao gồm **Windows Server (DC)** và **Windows 10 (Victim)**.

### **2. Thông số kỹ thuật các máy trạm (VMs)**

Hệ thống sử dụng dải mạng 192.168.18.0/24 cho vùng External  và 192.168.100.0/24 cho vùng Internal:

| **Máy ảo** | **Hệ điều hành** | **Vai trò** | **Địa chỉ IP** | **Ghi chú** |
| --- | --- | --- | --- | --- |
| **Ubuntu_SOC** | Ubuntu | Gateway, SIEM, IDS | 192.168.18.10 (ens33), 192.168.100.1 (ens37) | Chạy Splunk Enterprise và Suricata. |
| **Win_Server** | Win Server Core 2019 | Domain Controller | 192.168.100.12 | Chạy AD DS, DNS (chau.local), Sysmon và Splunk UF. |
| **Windows 10** | Windows 10 | Máy nạn nhân | 192.168.100.11 | Đã join domain chau.local, cài Sysmon và Splunk UF. |
| **Kali Linux** | Kali Linux | Máy tấn công | 192.168.18.3 | Nằm trong dải mạng NAT để tấn công qua Gateway. |

### **3. Cấu hình chi tiết các thành phần giám sát**

#### **A. Ubuntu SOC Gateway**

- **Splunk Enterprise:** Cài đặt bản All-in-One, cấu hình các Index chuyên biệt để quản lý log: endpoint (log tiến trình), network (log IDS), và auth (log xác thực).
- **Suricata (IDS):** Cấu hình lắng nghe trên giao diện mạng nội bộ (ens37), xuất log định dạng JSON (eve.json) và đẩy dữ liệu trực tiếp vào Splunk qua inputs.conf.
- **Cấu hình mạng:** Bật tính năng **IP Forwarding** và cấu hình **NAT (iptables)** để Ubuntu hoạt động như một router điều phối lưu lượng từ mạng nội bộ ra Internet.

#### **B. Windows Server Core (Domain Controller)**

- **Dịch vụ hệ thống:** Thiết lập AD DS và DNS cho domain chau.local. Tạo user labuser để phục vụ các kịch bản tấn công thử nghiệm.
- **Giám sát:** Cài đặt **Sysmon** với file cấu hình của SwiftOnSecurity để bắt các hành vi bất thường. Cấu hình **Audit Policy** chi tiết cho các sự kiện Logon/Logoff và quản lý tài khoản.
- **Đẩy log:** Sử dụng **Splunk Universal Forwarder (UF)** để đẩy log Security (Sự kiện 4624, 4625, 4768...), log Sysmon và log DNS Server về máy chủ Splunk.

#### **C. Windows 10 Victim**

- **Tối ưu hóa:** Gia nhập miền chau.local, tắt Windows Defender và Windows Update để mô phỏng môi trường dễ bị tổn thương cho Lab.
- **Giám sát:** Cài đặt tương tự máy DC với Sysmon và Splunk UF để đẩy log hệ thống, log bảo mật và log tiến trình về trung tâm.

### **4. Luồng dữ liệu và Kết nối**

Máy chủ Splunk trên Ubuntu lắng nghe dữ liệu trên cổng **9997**. Toàn bộ log từ máy nạn nhân và máy chủ điều khiển miền được tập trung về đây để phục vụ quá trình truy vấn, phân tích và săn tìm mối đe dọa
