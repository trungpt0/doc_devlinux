Bài viết này thì mình sẽ hướng dẫn Enable UART để serial monitor cho Pi Zero W. Hãy cùng mình tìm hiểu nhé.

# 1. Hardware Required

Để Serial Monitor cho Pi Zero W thì mình cần 1 module **USB-Uart CP2101**.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_9677163b80.png)

Với lại 1 em Pi Zero W với thẻ nhớ đã được flash image. Bạn flash image dùng Yocto hay dùng Pi Imager đều được nhé.

# 2. Enable and Config Uart

**Đây là cách dùng để enable uart:**

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_5987f6d48f.png)

Sau đây thì mình sẽ hướng dẫn các bước chi tiết nhé. Đầu tiên thì mình sẽ cắm thẻ nhớ vào máy tính để config. Và sau khi cắm, ta thấy một ổ đĩa cứng của thẻ nhớ trên máy.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_5341128adc.png)

Tiếp theo chúng ta hãy vào ổ đĩa và thấy 2 file dùng để config đó là **cmdline.txt** và **config.txt**

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_494fcaa43b.png)

File cmdline.txt dùng để cấu hình bus console uart và baudrate. File config.txt dùng để enable uart dùng để serial monitor.

**Tiếp theo thì hãy thêm "enable_uart=1" vào file config.txt:**

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_6c230f36d4.png)

**Sau đó các bạn vào file cmdline.txt và thêm "console=serial0,115200" nếu chưa có và xóa "quiet":**

Trước khi xóa:

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_9cdf99c097.png)

Sau khi xóa:

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_2d22487b29.png)

# 3. Serial Monitor

Và đó là các bước config Uart. Cuối cùng thì mình sẽ cắm thẻ nhớ vào Pi nối Pin của Pi với USB-Uart. Sau đây là sơ đồ nối chân:

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_c90b0bb142.png)

| CP2102 | GPIO Pi |
| -------- | -------- |
| RX     | Pin 8 (GPIO 14) |
| TX     | Pin 10 (GPIO 15) |
| GND | Pin 14 (GND) |

Sau đó các bạn vào moba hoặc các phần mềm khác dùng để xem log sau khi boot nhé.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/27/image_f461308bc0.png)

# 4. Kết luận

Và sau đây thì mình đã chia sẻ về cách enable-uart1 cho Pi Zero W. Hẹn gặp các bạn ở các bài viết khác !
