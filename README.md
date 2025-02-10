
# Bài 1: So sánh Monolithic Kernel và Microkernel

## 1. Sự khác biệt giữa Monolithic Kernel và Microkernel

### Monolithic Kernel
Monolithic Kernel là kiểu kernel truyền thống, trong đó tất cả các dịch vụ hạt nhân (như quản lý tiến trình, quản lý bộ nhớ, hệ thống tập tin, trình điều khiển thiết bị) đều chạy trong chế độ kernel mode.

### Microkernel
Microkernel thiết kế với một hạt nhân nhỏ gồm các chức năng tối thiểu (như quản lý bộ nhớ, quản lý tiến trình, giao tiếp giữa các tiến trình IPC). Các dịch vụ hệ thống như driver, quản lý tập tin, giao tiếp mạng chạy trong chế độ user mode.

## 2. So sánh ưu nhược điểm

| Tiêu chí        | Monolithic Kernel | Microkernel |
|------------------|------------------|------------|
| **Hiệu suất**    | Cao do tất cả các chức năng chạy trong kernel mode. | Thấp hơn do các giao tiếp IPC gây độ trễ. |
| **Bảo trì**     | Khó bảo trì do cấu trúc lớn. | Dễ bảo trì và nâng cấp do các dịch vụ tách biệt. |
| **Bảo mật**   | Kém an toàn, lỗi trong hạt nhân có thể gây crash toàn hệ thống. | An toàn hơn do driver chạy trong user mode, giảm thiểu tầm ảnh hướng khi gặp lỗi. |
| **Tính linh hoạt** | Kém linh hoạt, thay đổi kernel yêu cầu biên dịch lại. | Rất linh hoạt do các dịch vụ chạy tạch biệt. |

## 3. Tính linh hoạt của Linux dù sử dụng Monolithic Kernel
Linux sử dụng Monolithic Kernel nhưng vẫn linh hoạt nhờ:
- **Loadable Kernel Modules (LKM)**: Cho phép tải hoặc gỡ bỏ các module kernel mà không cần biên dịch lại toàn bộ kernel.
- **Cấu hình linh hoạt**: Cho phép tuỳ chỉnh kernel khi biên dịch.
- **Giao tiếp IPC hiệu quả**: Linux tối ưu hiệu suất và bổ sung giao tiếp IPC, giảm bất lợi của Monolithic Kernel.

---

# Bài 2: Mô hình "Everything as a File" trong Linux

## 1. Giải thích mô hình "Everything as a File"
Linux áp dụng nguyên tắc **"Everything as a File"**, trong đó:
- Mọi đối tượng hệ thống (định tuyến, thiết bị, tiến trình, socket) được đối xử như tập tin.
- Cho phép quản lý dễ dàng, đơn giản hóa giao tiếp giữa các thành phần.

## 2. Các đối tượng hoạt động như file
- **Thiết bị**: `/dev/sda`, `/dev/ttyUSB0`
- **Tiến trình**: `/proc/1234/cmdline`, `/proc/1234/status`
- **Socket**: `/proc/net/tcp`, `/proc/net/udp`

## 3. Lệnh kiểm tra và phân tích đầu ra

### Kiểm tra thiết bị là file:
```sh
ls -l /dev/sda
```
**Đầu ra:**
```
brw-rw---- 1 root disk 8, 0 Feb 10 10:00 /dev/sda
```
(Chữ "b" cho biểu diễn block device)

### Kiểm tra tiến trình là file:
```sh
ls -l /proc/$$
```
**Đầu ra:**
```
dr-xr-xr-x 9 user user 0 Feb 10 10:00 /proc/1234
```
(Tiến trình 1234 có thư mục trong `/proc`)

### Kiểm tra socket là file:
```sh
cat /proc/net/tcp
```
(Hiển thị danh sách kết nối TCP hiện tại)

## Kết luận
Mô hình "Everything as a File" giúp Linux quản lý hệ thống dễ dàng, đồng nhất và linh hoạt.

Bài 3: Cách Linux thực hiện Preemptive Multitasking

1. Giải thích Preemptive Multitasking

Preemptive Multitasking là cơ chế đa nhiệm trong đó hệ điều hành có thể giành quyền kiểm soát CPU từ một tiến trình đang chạy để chuyển sang tiến trình khác dựa trên mức độ ưu tiên.

Phân biệt Preemptive và Cooperative Multitasking

Preemptive Multitasking: Hệ điều hành kiểm soát việc chuyển đổi tiến trình dựa trên lịch trình và ưu tiên.

Cooperative Multitasking: Tiến trình tự nguyện nhường CPU khi hoàn thành nhiệm vụ hoặc gọi hàm hệ thống.

![image](https://github.com/user-attachments/assets/13abc8d8-a460-4e23-aa9c-1e8d874661f6)

2. Vai trò của Linux Scheduler

Linux sử dụng trình lập lịch để quản lý tiến trình, đảm bảo CPU được phân bổ hợp lý.

Completely Fair Scheduler (CFS)

CFS là thuật toán lập lịch chính của Linux, dựa trên ý tưởng:

Mỗi tiến trình có virtual runtime (vruntime), được sử dụng để xác định tiến trình nào sẽ chạy tiếp theo.

Tiến trình có vruntime nhỏ nhất được ưu tiên thực thi trước.

Mức ưu tiên có thể điều chỉnh bằng giá trị nice và priority.

3. Thực hành kiểm chứng

Dùng lệnh sau để kiểm tra mức ưu tiên của các tiến trình:

ps -eo pid,pri,ni,cmd

Đầu ra mẫu:

  PID PRI  NI CMD
  1234  20   0 /usr/bin/firefox
  5678  30 -10 /usr/bin/rt-process

PRI: Độ ưu tiên thực tế của tiến trình (số cao hơn nghĩa là ưu tiên thấp hơn).

NI: Giá trị nice (có thể thay đổi bằng nice hoặc renice).

II- Practice
Kiểm tra các module kernel đang chạy:

lsmod

![image](https://github.com/user-attachments/assets/215e5621-6357-480b-8d4e-dee6d261b3a2)
Xem thông tin về một module cụ thể:

modinfo ext4

Tìm hiểu hệ thống tập tin trong Linux

Liệt kê các thiết bị trong /dev:

![image](https://github.com/user-attachments/assets/23798c1b-e092-4923-9473-a8e19918afba)

Kiểm tra thông tin CPU và bộ nhớ:


