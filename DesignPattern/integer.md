Bài viết này mình sẽ chia sẻ về Integer-Based trên Pi Zero W. Hãy cùng theo dõi nhé!

# 1. Integer-based GPIO Driver

Hiểu đơn giản, mỗi chân GPIO được xác định bởi một số nguyên và số này được các hàm API để thực hiện các thao tác như cấu hình, đọc ghi trạng thái GPIO thì được gọi là **Integer-based GPIO**. Kiểu giao tiếp này rất phổ biến, dễ sử dụng nhưng có nhược điểm là dễ xảy ra xung đột tài nguyên, khó để mở rộng khi cần quản lý nhiều chân GPIO.

**Các API Integet-based được định nghĩa tại:**

```c
#include <linux/gpio.h>
```

# 2. APIs Integer-based GPIO 

## gpio_is_valid

GPIO được xác định bằng các số nguyên không dấu trong phạm vi từ 0 cho đến MAX_INT. Số nguyên của GPIO sử dụng được tùy vào GPIO Pin của từng board mạch cụ thể. Có thể sử dụng platform_data để lưu cấu hình pin.

**Để xác định xem số GPIO có hợp lệ hay không thì hãy sử dụng hàm:**

```c
// Valid return 1, invalid return 0
int gpio_is_valid(int number);
```

Nếu GPIO number hợp lệ hàm sẽ trả về một số dương khác 0. Ngược lại nếu không hợp lệ hàm sẽ trả về 0.

## gpio_direction

Sau khi xác định tính hợp lệ của GPIO number thì để cấu hình I/O cho pin thì hai hàm này được sử dụng:

```c
int gpio_direction_input(unsigned gpio);
int gpio_direction_output(unsigned gpio, int value);
```

Hàm sẽ trả về 0 nếu thành công và sẽ trả về một số âm nếu error.

## gpio_get_value

Hầu hết các GPIO đều có thể trup cập vào read/write memory mà không cần phải rơi vào trạng thái ngủ. Hàm dùng để lấy giá trị từ GPIO là:

```c
int gpio_get_value(unsigned gpio);
```

Hàm sẽ trả về một số dương khác 0 nếu lấy giá trị thành công và sẽ trả về 0 nếu ngược lại.

## gpio_set_value

Để ghi giá trị vào GPIO. Ví dụ như ghi value = 1 thì led on và value = 0 thì led off, thì hãy sử dụng hàm:

```c
void gpio_set_value(unsigned gpio, int value);
```

Hàm gpio_get_value và gpio_set_value sẽ không trả về "invalid GPIO" bởi vì là việc giá định GPIO number có hợp lệ hay không thì đó là việc của gpio_direction.

## gpio_cansleep

Trên các chân GPIO dùng để giao tiếp I2C, SPI,... để đọc hoặc ghi các giá trị qua bus này thì hệ thống phải chờ để gửi và nhận lệnh. Trong Linux Kernel hàm được dùng để kiểm tra xem một GPIO có yêu cầu chờ đợi (sleep) hay không là:

```c
int gpio_cansleep(unsigned gpio);
```

Hàm sẽ trả về 1 nếu GPIO yêu cầu chờ tức là nó đang bận để giao tiếp qua các bus như I2C hoặc SPI. Ngược lại hàm sẽ trả về 0 nếu GPIO không yêu cầu chờ đợi tức là có thể sử dụng GPIO này.

Một số hàm tương tự nhưng thêm vào là có thể sử dụng thêm để đọc và ghi giá trị của GPIO là:

```c
int gpio_get_value_cansleep(unsigned gpio);
void gpio_set_value_cansleep(unsigned gpio, int value);
```

## gpio_request và gpio_free

Trước khi mà bạn cấu hình I/O hay là đọc ghi thì mình phải cần yêu cầu truy cập vào GPIO đó. Và hàm dùng để làm việc này là:
	
```c
int gpio_request(unsigned gpio, const char *label);
```

Hàm dùng để giải phóng truy cập một GPIO:

```c
void gpio_free(unsigned gpio);
```

Với,
* gpio: là GPIO number mà bạn muốn yêu cầu quyền truy cập.
* label: là một chuỗi ký tự để mô tả mục đích sử dụng GPIO.

Hàm sẽ trả về 0 nếu success. Nếu fail sẽ trả về các mã lỗi như -EBUSY (bận), -EINVAL (lỗi gpio number), -ENOMEM (không đủ bộ nhớ). Một số hàm liên quan có thể kể đến như gpio_request_one, gpio_request_array, void gpio_free_array.

## gpio_request_one

Hàm này yêu cầu quyền truy cập cho một GPIO cụ thể, giống như gpio_request(), nhưng nó cho phép cấu hình thêm các flags để chỉ định chế độ hoạt động ban đầu cho GPIO (như input, output, pull-up/pull-down).

```c
int gpio_request_one(unsigned gpio, unsigned long flags, const char *label);
```

Ví dụ:

```c
[...]
unsigned gpio_num = 30;
unsigned long flags = GPIOF_DIR_OUT; // Output
const char *label = "LED";

[...]
	if (gpio_request_one(gpio_num, flags, label) < 0) {
			printk("cannot request GPIO %d\n", gpio_num);
			return -EBUSY;
	}
[...]
```

Với các flags:

* GPIOF_DIR_IN - to configure direction as input
* GPIOF_DIR_OUT - to configure direction as output
* GPIOF_INIT_LOW - as output, set initial level to LOW
* GPIOF_INIT_HIGH - as output, set initial level to HIGH
* GPIOF_OPEN_DRAIN - gpio pin is open drain type.
* GPIOF_OPEN_SOURCE - gpio pin is open source type.
* GPIOF_EXPORT_DIR_FIXED - export gpio to sysfs, keep direction
* GPIOF_EXPORT_DIR_CHANGEABLE - also export, allow changing direction
* GPIOF_IN - configure as input
* GPIOF_OUT_INIT_LOW - configured as output, initial level LOW
* GPIOF_OUT_INIT_HIGH - configured as output, initial level HIGH

## gpio_request_array

Hàm này cho phép yêu cầu quyền truy cập cho một mảng các GPIOs trong một lần gọi. Thay vì yêu cầu từng GPIO một, bạn có thể sử dụng hàm này để yêu cầu nhiều GPIO cùng lúc.

```c
int gpio_request_array(struct gpio *array, size_t num);
```

Ví dụ:

```c
[...]
struct gpio array[] = {
    { .gpio = 24, .flags = GPIOF_DIR_OUT, .label = "LED" },
    { .gpio = 25, .flags = GPIOF_DIR_IN, .label = "Button" }
};

[...]
	if (gpio_request_array(array, ARRAY_SIZE(array)) < 0) {
			printk("cannot request GPIOs\n");
			return -EBUSY;
	}
[...]
```

## gpio_free_array

Hàm này giải phóng (free) một mảng các GPIOs đã yêu cầu trước đó bằng gpio_request_array().

```c
void gpio_free_array(struct gpio *array, size_t num);
```

Ví dụ:

```c
[...]
	gpio_free_array(array, ARRAY_SIZE(array));
[...]
```

## gpio_to_irq

Giá trị đầu vào của GPIO có thể làm tín hiệu ngắt và các hàm API dùng để chuyển đổi giữa số GPIO và số IRQ (Interrupt Request), giúp kết nối GPIO với các cơ chế ngắt (interrupts):

```c
int gpio_to_irq(unsigned gpio);
int irq_to_gpio(unsigned irq);
```

# 3. Example Driver

Đây là một ví dụ về Integer-based GPIO dùng để on/off LED khi insmod/remove driver.

## Bước 1: xác định GPIO base 

Để request một GPIO thì chúng ta cần phải biết GPIO base của Pi. Và sau đây là cách xem GPIO base.

```bash
$ ls -l /sys/class/gpio/
```

Sau đó chúng ta sẽ thấy **gpiochip512** tại đường dẫn trên. Nhìn vào tên gpiochip ta có thể đoán gpio base là 512.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/30/image_2304255961.png)

**Để chắc chắn hơn ta có thể xem gpio base bằng lệnh:**

```bash
$ cat /sys/class/gpio/gpiochip512/base
```

Và đúng như trên gpio base là 512.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/30/image_fc216a0fe7.png)

## Bước 2: xác định LED GPIO

Để xác định LED GPIO thì khác đơn giản, ta chỉ việc cộng gpio mà mình muốn sử dụng với gpio base.

**<div class="align-center">GPIO Number Request = GPIO Pi + GPIO base</div>**

Ví dụ tôi sử dụng GPIO 27 (Pin 13) thì gpio mà tôi request sẽ là: 27 + 512 = 539.

## Bước 3: viết GPIO Driver

**Trước tiên thì mình phải define GPIO_NUMBER:**

```c
#define GPIO_NUMBER 539
```

**Request GPIO mà mình muốn sử dụng:**

```c
gpio_request(GPIO_NUMBER, "integer_gpio");
```

**Cấu hình chế độ output cho GPIO:**

```c
gpio_direction_output(GPIO_NUMBER, 0);
```

**Set ON LED khi insmod driver:**

```c
gpio_set_value(GPIO_NUMBER, 1);
```

**Set OFF LED khi rmmod driver:**

```c
gpio_set_value(GPIO_NUMBER, 0);
```

**Giải phóng GPIO:**

```c
gpio_free(GPIO_NUMBER);
```

**Full code driver:**

```c
/**
  * file: itg-gpio.c
  */
#include <linux/module.h>
#include <linux/gpio.h>

#define GPIO_NUMBER 539

#define LOW     0
#define HIGH    1

/**
 * Module initialization function
*/
static int __init integer_based_driver_init(void)
{
    int ret;

    ret = gpio_request(GPIO_NUMBER, "integer_gpio");

    if (ret) {
        pr_err("cannot to request GPIO %d\n", GPIO_NUMBER);
        return ret;
    }

    gpio_direction_output(GPIO_NUMBER, LOW);
    gpio_set_value(GPIO_NUMBER, HIGH);

    pr_info("Driver added successfully!\n");
    return 0;
}

/**
 * Module exit function
*/
static void __exit integer_based_driver_exit(void)
{
    gpio_set_value(GPIO_NUMBER, LOW);
    gpio_free(GPIO_NUMBER);
    pr_info("Driver added removed!\n");
}

/**
 * Register initialization and exit functions of the module
*/
module_init(integer_based_driver_init);
module_exit(integer_based_driver_exit);

/**
 * Module Information
*/
MODULE_LICENSE("GPL");
MODULE_AUTHOR("DEVLINUX - Trung Tran");
MODULE_DESCRIPTION("Integer-based GPIO Driver");
MODULE_VERSION("1.0");
```

## Bước 4: viết Makefile cho driver

**Ta cần file Makefile như sau để build driver:**

```Makefile
obj-m += itg-gpio.o

KDIR=/lib/modules/$(shell uname -r)/build

all:
        make -C $(KDIR) M=$(PWD) modules

clean:
        make -C $(KDIR) M=$(PWD) clean
```

**Gõ make để build:**

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/30/image_aa1013f561.png)

Chúng ta đã có thể thấy Linux Kernel Module File.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/30/image_f30ca47674.png)

## Bước 4: insmod và remove module

**insmod module:**

```bash
$ sudo insmod itg-gpio.ko
```

Và ta thấy LED đã ON.

![](https://assets.devlinux.vn/uploads/editor-images/2024/11/30/image_edc543280b.png)

**rmmod module:**

```bash
$ sudo rmmod itg-gpio
```

Và ta thấy LED đã OFF.

