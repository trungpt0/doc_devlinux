Bài viết này thì mình sẽ chia sẻ và cách viết một GPIO Subsystem cơ bản. Về nội dung thì mình sẽ nói về **Integer-based GPIO** (Legacy GPIO Driver) và **Descriptor-based GPIO**.

# Giới thiệu về GPIO Subsystem

GPIO Subsystem trong Linux kernel là một hệ thống phụ quản lý các chân GPIO (General-Purpose Input/Output) trên các bộ xử lý nhúng. Hệ thống này cung cấp một API nhất quán cho các driver và ứng dụng trong kernel, cho phép truy cập, điều khiển và giám sát các chân GPIO của phần cứng. Có hai cách quản lý GPIO subsystem đó là Integer-based GPIO và Descriptor-based GPIO

# 1. Legacy GPIO Driver (Integer-based GPIO)

Hiểu đơn giản, mỗi chân GPIO được xác định bởi một số nguyên và số này được các hàm API để thực hiện các thao tác như cấu hình, đọc ghi trạng thái GPIO thì được gọi là **Integer-based GPIO**. Kiểu giao tiếp này rất phổ biến, dễ sử dụng nhưng có nhược điểm là dễ xảy ra xung đột tài nguyên, khó để mở rộng khi cần quản lý nhiều chân GPIO.

**Các API Integet-based được định nghĩa tại:**

```c
#include <linux/gpio.h>
```

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

## Example code

```c
#include <linux/module.h>
#include <linux/gpio.h>

#define GPIO_NUMBER 17

#define LOW     0
#define HIGH    1

/**
 * Module initialization function
*/
static int __init integer_based_driver_init(void)
{
    int ret;

    ret = gpio_request(GPIO_NUMBER, "legacy_gpio");
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

# 2. Descriptor-based GPIO Driver

Về cách giao tiếp này, thay vì sử dụng số nguyên thì mỗi chân GPIO được đại diện bằng một descriptor (mô tả). Descriptor chứa thông tin chi tiết về chân GPIO, như quyền truy cập, ngữ cảnh driver đang sử dụng...

Các API của phương pháp Descriptor-based GPIO được định nghĩa tại:

```c
#include <linux/gpio/consumer.h>
```

**Cấu trúc mô tả GPIO:**

```c
struct gpio_descs {
	struct gpio_array *info;
	unsigned int ndescs;
	struct gpio_desc *desc[];
};
```

Với:
* struct gpio_array *info: là một con trỏ tới struct gpio_array, chứa thông tin về mảng GPIO đang được quản lý.
* unsigned int ndescs: là số lượng các GPIO descriptors có trong mảng desc.
* struct gpio_desc *desc[]: là mảng con trỏ tới các mô tả GPIO (struct gpio_desc), nơi mỗi phần tử là một mô tả cho một GPIO cụ thể.

## gpiod_get

**Trong giao tiếp bằng Descriptor GPIO, thì API dùng để lấy một GPIO của thiết bị là:**

```c
struct gpio_desc *gpiod_get(struct device *dev, const char *con_id, enum gpiod_flags flags);
```

Với:
* dev: trỏ đến đối tượng thiết bị.
* con_id: là tên kết nối của GPIO, giúp xác định GPIO cụ thể nào trong trường hợp thiết bị có nhiều GPIO.
* flags:
**GPIOD_ASIS** hoặc 0: Không khởi tạo GPIO. Hướng của GPIO phải được thiết lập sau đó bằng một trong các hàm chuyên dụng.
**GPIOD_IN**: Khởi tạo GPIO ở trạng thái đầu vào (input).
**GPIOD_OUT_LOW**: Khởi tạo GPIO ở trạng thái đầu ra (output) với giá trị bằng 0.
**GPIOD_OUT_HIGH**: Khởi tạo GPIO ở trạng thái đầu ra (output) với giá trị bằng 1.
**GPIOD_OUT_LOW_OPEN_DRAIN**: Giống như GPIOD_OUT_LOW, nhưng thêm vào việc bắt buộc đường GPIO phải được sử dụng với chế độ open drain.



**Thay vì lấy một GPIO thì API này cho phép lấy nhiều GPIO:**

```c
struct gpio_desc *gpiod_get_index(struct device *dev, const char *con_id, unsigned int idx, enum gpiod_flags flags);
```

Với:
* dev: trỏ đến đối tượng thiết bị.
* con_id: là tên kết nối của GPIO, giúp xác định GPIO cụ thể nào trong trường hợp thiết bị có nhiều GPIO.
* idx: index của GPIO cần được lấy trong nhóm GPIO.
* flags: các cờ cấu hình GPIO.

Ngoài ra còn một số API khác để lấy GPIO. 

**Hàm yêu cầu một GPIO descriptor cho một GPIO cụ thể có thể không tồn tại:**

```c
struct gpio_desc *gpiod_get_optional(struct device *dev,
                                     const char *con_id,
                                     enum gpiod_flags flags);
```

**Hàm này tương tự gpiod_get_optional với có thể lựa chọn index GPIO:**

```c
struct gpio_desc *gpiod_get_index_optional(struct device *dev,
                                           const char *con_id,
                                           unsigned int index,
                                           enum gpiod_flags flags);
```

**Hàm dùng để lấy một mảng các GPIO:**

```c
struct gpio_descs *gpiod_get_array(struct device *dev,
                                   const char *con_id,
                                   enum gpiod_flags flags);
```

**Hàm dùng để lấy một mảng các GPIO có thể không tồn tại:**

```c
struct gpio_descs *gpiod_get_array_optional(struct device *dev,
                                            const char *con_id,
                                            enum gpiod_flags flags);
```
## gpiod_direction

**Hàm dùng để cấu hình đầu vào cho một GPIO descriptor:**

```c
int gpiod_direction_input(struct gpio_desc *desc);
```

Trả về 0 nếu việc thay đổi hướng GPIO thành đầu vào thành công. Nếu có lỗi xảy ra, hàm sẽ trả về một giá trị âm, thường là một mã lỗi (-EINVAL, -EBUSY, v.v.).

**Hàm dùng để cấu hình đầu ra cho một GPIO descriptor:**

```c
int gpiod_direction_output(struct gpio_desc *desc, int value);
```

Cũng tương tự hàm cấu hình input, hàm này trả về 0 nếu việc thay đổi hướng GPIO thành đầu vào thành công. Nếu có lỗi xảy ra, hàm sẽ trả về một giá trị âm, thường là một mã lỗi (-EINVAL, -EBUSY, v.v.).

Hàm dùng để lấy giá trị đã cấu hình GPIO:

```c
int gpiod_get_direction(const struct gpio_desc *desc);
```

Hàm trả về 0 nếu GPIO đang ở chế độ đầu ra (output), trả về 1 nếu GPIO đang ở chế độ đầu vào (input), Nếu lỗi sẽ trả về một số âm .

## gpiod_get_value

**Hàm được sử dụng để đọc giá trị hiện tại của một GPIO:**

```c
int gpiod_get_value(const struct gpio_desc *desc);
```

Hàm trả về 0 nếu GPIO đang ở mức thấp (low) hoặc trả về 1 nếu GPIO đang ở mức cao (high).

## gpiod_set_value

**Hàm được sử dụng để thiết lập giá trị cho một GPIO:**

```c
void gpiod_set_value(struct gpio_desc *desc, int value);
```

## gpiod_to_irq

**Hàm này chuyển đổi một GPIO descriptor thành số IRQ:**

```c
int gpiod_to_irq(const struct gpio_desc *desc);
```

Nếu hàm thành công, nó trả về số IRQ liên kết với GPIO đó. Nếu thất bại (không có IRQ được liên kết), nó sẽ trả về một giá trị lỗi âm.

## Example Code

```c
#include <linux/module.h>
#include <linux/gpio.h>
#include <linux/platform_device.h>
#include <linux/gpio/consumer.h>
#include <linux/of.h>
#include <linux/interrupt.h>

#define LOW     0
#define HIGH    1

static struct gpio_desc *led_gpio; // led descriptor
static struct gpio_desc *button_gpio; // button descriptor
static int irq_number;
static bool led_state = false;

/* handle interrupt function */
static irqreturn_t button_irq_handler(int irq, void *dev_id)
{
    led_state = !led_state;
    gpiod_set_value(led_gpio, led_state);
    pr_info("interrupted! led: %s\n", led_state ? "on" : "off");
    return IRQ_HANDLED;
}

/* probe driver */
static int des_gpio_probe(struct platform_device *pdev)
{
    int ret;
    struct device *dev = &pdev->dev;

    /* init led GPIO with LOW */
    led_gpio = gpiod_get(dev, "led", GPIOD_OUT_LOW);
    if (IS_ERR(led_gpio)) {
        pr_err("cannot init led gpio\n");
        return PTR_ERR(led_gpio);
    }

    /* init button GPIO with input mode*/
    button_gpio = gpiod_get(dev, "button", GPIOD_IN);
    if (IS_ERR(button_gpio)) {
        pr_err("cannot init button gpio\n");
        gpiod_put(led_gpio);
        return PTR_ERR(button_gpio);
    }

    /* get IRQ number */
    irq_number = gpiod_to_irq(button_gpio);
    if (irq_number < 0) {
        pr_err("cannot get irq number\n");
        gpiod_put(led_gpio);
        gpiod_put(button_gpio);
        return irq_number;
    }

    /* register irq number */
    ret = request_irq(irq_number, button_irq_handler, IRQF_TRIGGER_RISING, "button_irq", NULL);
    if (ret) {
        pr_err("cannot request irq\n");
        gpiod_put(led_gpio);
        gpiod_put(button_gpio);
        return ret;
    }

    pr_info("Driver added successfully!\n");
    return 0;
}

/* remove driver */
static int des_gpio_remove(struct platform_device *pdev)
{
    free_irq(irq_number, NULL);
    gpiod_set_value(led_gpio, LOW);
    gpiod_put(led_gpio);
    gpiod_put(button_gpio);
    pr_info("Driver added removed!\n");
    return 0;
}

/* device tree match table */
static const struct of_device_id gpiod_dt_ids[] = {
    { .compatible = "gpio-descriptor-based", },
    { }
};

/* platform driver structure */
static struct platform_driver mypdrv = {
    .probe = des_gpio_probe,
    .remove = des_gpio_remove,
    .driver = {
        .name = "descriptor-based",
        .of_match_table = of_match_ptr(gpiod_dt_ids),
        .owner = THIS_MODULE,
    },
};
module_platform_driver(mypdrv);

/**
 * Module Information
*/
MODULE_LICENSE("GPL");
MODULE_AUTHOR("DEVLINUX - Trung Tran");
MODULE_DESCRIPTION("Descriptor-based GPIO Driver");
MODULE_VERSION("1.0");
```