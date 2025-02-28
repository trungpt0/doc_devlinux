Bài viết này thì mình chia sẻ về GPIO Driver Legacy trên board Raspberry Pi Zero W. Hãy cùng mình theo dõi nhé!

# 1. Giới thiệu về GPIO (General Purpose I/O) trên chip BCM2835

Để viết một Legacy GPIO trên Pi Zero W thì trước tiên chúng ta cần tìm hiểu về GPIO và đọc kèm với datasheet của chip BCM2835. Và đây là tài liệu về dòng chip này nhé: https://www.raspberrypi.org/app/uploads/2012/02/BCM2835-ARM-Peripherals.pdf

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/30/image_d417612e6c.png)

Về cơ bản thì dòng BCM2835 có 54 GPIO được chia thành 2 bank. Tất cả các chân GPIO đều có ít nhất 2 chức năng như vừa có thể là một chân IO vừa có chức năng sử dụng để truyền dữ liệu (UART, SPI...). GPIO của BCM2835 thì có 41 thanh ghi và mỗi thanh ghi là 32 bit. Các thanh ghi này được dùng để cấu hình I/O, ON/OFF của LED.

# 2. APIs

**Các API trong bài này được định nghĩa tại:**

```c
#include <linux/io.h>
```

## 2.1 ioremap

Hàm ioremap trong Linux kernel được sử dụng để ánh xạ (map) một vùng địa chỉ vật lý của thiết bị phần cứng vào không gian địa chỉ của kernel. Điều này cho phép kernel và driver truy cập trực tiếp vào thanh ghi của thiết bị ngoại vi, vốn được định vị ở các địa chỉ vật lý cụ thể.

```c
void __iomem *ioremap(unsigned long phys_addr, unsigned long size);
```

**Các tham số:**
* phys_addr: Địa chỉ vật lý của vùng bộ nhớ phần cứng mà bạn muốn ánh xạ.
* size: Kích thước (tính bằng byte) của vùng bộ nhớ mà bạn muốn ánh xạ.

**Giá trị trả về:**
* Trả về một con trỏ (virtual address) trong không gian địa chỉ của kernel, có thể được sử dụng để truy cập các thanh ghi hoặc bộ nhớ của thiết bị.
* Trả về NULL nếu ánh xạ thất bại.

## 2.2 iounmap

Hàm iounmap trong Linux kernel được sử dụng để hủy ánh xạ (unmap) một vùng địa chỉ bộ nhớ thiết bị đã được ánh xạ trước đó bằng ioremap. Nó giải phóng tài nguyên và đảm bảo rằng vùng nhớ kernel không bị lãng phí.

```c
void iounmap(volatile void __iomem *addr);
```

**Tham số:**
* addr: Con trỏ tới địa chỉ ảo (virtual address) đã được ánh xạ trước đó bằng ioremap.

## 2.3 ioread32

Hàm ioread32 trong Linux kernel được sử dụng để đọc một giá trị 32-bit từ một địa chỉ bộ nhớ ánh xạ I/O (I/O-mapped memory).

```c
u32 ioread32(const void __iomem *addr);
```

**Tham số:**
* addr: Địa chỉ ảo ánh xạ đến không gian I/O của thiết bị, được trả về bởi hàm ioremap.

**Giá trị trả về:**
* Trả về một giá trị 32-bit (4 byte) được đọc từ địa chỉ addr.

## 2.4 iowrite32

Hàm iowrite32 trong Linux kernel được sử dụng để ghi một giá trị 32-bit vào một địa chỉ bộ nhớ ánh xạ I/O (I/O-mapped memory).

```c
void iowrite32(u32 value, void __iomem *addr);
```

**Các tham số:**
* value: Giá trị 32-bit (4 byte) cần ghi vào thiết bị.
* addr: Địa chỉ bộ nhớ ảo được ánh xạ trước đó bằng hàm ioremap. Địa chỉ này trỏ đến thanh ghi hoặc bộ nhớ của thiết bị ngoại vi.

# 3. Các bước viết driver

## 3.1 Định nghĩa các thanh ghi cần thiết

### BCM2835_GPIO_BASE

Để cấu hình được GPIO thì ta cần phải biết Base Address của GPIO. Ta có thể tìm thấy được tại Datasheet của hãng.

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_5644e01a1d.png)

Ta có thể thấy địa chỉ vật lý cho ngoại vi từ 0x2000000 đến 0x20FFFFFF. Vậy nên địa chỉ BCM2835_BASE của ngoại vi là 0x2000000.

```c
#define BCM2835_PERI_BASE  0x20000000
```

Tiếp theo là lấy BCM2835_GPIO_BASE dựa vào địa chỉ ảo của ngoại vi.

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_aa77d10fed.png)

Dựa vào địa chỉ đầu tiên là 0x7E200000 có nghĩa là địa chỉ ảo là 0x7E00000 và physical address offset là 0x200000. Vậy nên BCM2835_GPIO_BASE sẽ là:

```c
#define BCM2835_GPIO_BASE  (BCM2835_PERI_BASE + 0x200000)
```

### GPIO_SET_OFFSET

Để cấu hình OUTPUT cho GPIO thì 2 thanh ghi này sẽ cho phép làm điều đó:

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_b53e8c7e83.png)

Ở bài này thì mình cần cấu hình output cho GPIO 27 thuộc thanh khi GPSET0 nên ta cần định nghĩa offset của thanh ghi này:

```c
#define GPIO_SET_OFFSET    0x1C
```

### GPIO_CLR_OFFSET    

Tương tự để clear cấu hình thì cần quan tâm đến 2 thanh ghi này:

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_a4055cba3e.png)

Và cũng như trên để clear GPIO 27 thì mình chỉ cần định nghĩa offset của thanh ghi GPCLR0:

```c
#define GPIO_CLR_OFFSET    0x28
```

## 3.2 Map thanh ghi GPIO vào bộ nhớ kernel

Để map thanh ghi vào kernel ta dùng hàm **ioremap**:

```c
static void __iomem *gpio_base;
[...]
gpio_base = ioremap(BCM2835_GPIO_BASE, 0x100);
    if (!gpio_base) {
        pr_err("Failed to ioremap GPIO\n");
        return -ENOMEM;
}
```

Lý do mà 0x100 được sử dụng trong hàm ioremap để chỉ định kích thước vùng bộ nhớ là do offset của các thanh ghi như Function Select, Set, Clear, Level, Event, và Pull-up/down... từ 0x00 đến 0xA0 nên 0x100 đảm bảo rằng toàn bộ không gian của các thanh ghi GPIO được ánh xạ.

## 3.3 Cấu hình output cho GPIO27

Để cấu hình output ta sử dụng hàm ioread32 và iowrite32.

**Đọc giá trị 32-bit từ thanh ghi chức năng GPIO**:

```c
uint32_t reg;
[...]
reg = ioread32(gpio_base + GPIO_FSEL_OFFSET + (GPIO_PIN / 10) * 4);
```

Với,
- gpio_base: được ánh xạ qua ioremap
- (GPIO_PIN / 10) dùng để chỉ GPIO Alternate function select register 2 chứa GPIO 27

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_c537557920.png)

**Tiếp theo và clear bit và cấu hình output:**

```c
reg &= ~(7 << ((GPIO_PIN % 10) * 3)); // Clear chức năng hiện tại
reg |= (1 << ((GPIO_PIN % 10) * 3));  // Set thành output
```

**Ghi giá trị đã cấu hình (reg) vào thanh ghi điều khiển Function Select (FSEL):**

```c
iowrite32(reg, gpio_base + GPIO_FSEL_OFFSET + (GPIO_PIN / 10) * 4);
```

### 3.4 ON LED

Tiếp theo ta chỉ cần dịch 27 bit vào thanh ghi BCM2835_GPIO_BASE + GPIO_SET_OFFSET.

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_000ab0b10d.png)

**Dùng hàm iowrite32 để cấu hình thanh GPSET:**

```c
iowrite32(1 << GPIO_PIN, gpio_base + GPIO_SET_OFFSET);
```

### 3.4 OFF LED

Tiếp theo ta chỉ cần dịch 27 bit vào thanh ghi GPCLR.

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_a686eb879c.png)
![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_c766b6fc45.png)

**Dùng hàm iowrite32 để cấu hình thanh GPCLR:**

```c
iowrite32(1 << GPIO_PIN, gpio_base + GPIO_CLR_OFFSET);
```

### 3.5 Giải phóng bộ nhớ

**Dùng hàm iounmap để giải phóng bộ nhớ:**

```c
iounmap(gpio_base);
```

# Full Driver:

```c
/**
  * file gpio.c
	*/
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/io.h>

#define BCM2835_GPIO_BASE  0x20200000  // Base address của GPIO trên Pi Zero
#define GPIO_SET_OFFSET    0x1C        // Offset cho thiết lập (set)
#define GPIO_CLR_OFFSET    0x28        // Offset cho xóa (clear)
#define GPIO_FSEL_OFFSET   0x00        // Offset cho lựa chọn chức năng (function select)
#define GPIO_PIN           27          // Số GPIO

static void __iomem *gpio_base;

static int __init gpio_driver_init(void)
{
    uint32_t reg;

    // Map thanh ghi GPIO vào bộ nhớ kernel
    gpio_base = ioremap(BCM2835_GPIO_BASE, 0x100);
    if (!gpio_base) {
        pr_err("Failed to ioremap GPIO\n");
        return -ENOMEM;
    }

    // Cấu hình GPIO27 thành output
    reg = ioread32(gpio_base + GPIO_FSEL_OFFSET + (GPIO_PIN / 10) * 4);
    reg &= ~(7 << ((GPIO_PIN % 10) * 3)); // Clear chức năng hiện tại
    reg |= (1 << ((GPIO_PIN % 10) * 3));  // Set thành output
    iowrite32(reg, gpio_base + GPIO_FSEL_OFFSET + (GPIO_PIN / 10) * 4);

    // Bật GPIO27
    iowrite32(1 << GPIO_PIN, gpio_base + GPIO_SET_OFFSET);
    pr_info("GPIO27 set to HIGH\n");

    return 0;
}

static void __exit gpio_driver_exit(void)
{
    // Tắt GPIO27
    iowrite32(1 << GPIO_PIN, gpio_base + GPIO_CLR_OFFSET);
    pr_info("GPIO27 set to LOW\n");

    // Giải phóng map bộ nhớ
    if (gpio_base)
        iounmap(gpio_base);
}

module_init(gpio_driver_init);
module_exit(gpio_driver_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Trung Tran");
MODULE_DESCRIPTION("Legacy GPIO Driver for Raspberry Pi Zero W");
```

**Để build driver là cần viết 1 file Makefile:**

```Makefile
obj-m += gpio.o

KDIR=/lib/modules/$(shell uname -r)/build

all:
        make -C $(KDIR) M=$(PWD) modules

clean:
        make -C $(KDIR) M=$(PWD) clean
```

**Build driver:**

```bash
$ make
```

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_24d62b564d.png)

**Và cuối cùng là insmod và rmmod để add và remove kernel module:**

```bash
$ sudo insmod gpio.ko
$ sudo rmmod gpio
```

**Kết quả:**

![](https://assets.devlinux.vn/uploads/editor-images/2024/12/1/image_35f3855516.png)