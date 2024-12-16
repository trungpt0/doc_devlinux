Bài viết này mình sẽ chia sẻ về Descriptor-based trên Pi Zero W. Hãy cùng theo dõi nhé!

# Descriptor-based GPIO

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

# 2. APIs Descriptor-based GPIO

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

# 3. Example Driver

**Khai báo thư viện dùng trong Descriptor GPIO:**

```c
#include <linux/platform_device.h>  /* For platform devices */
#include <linux/gpio/consumer.h>    /* For GPIO Descriptor */
#include <linux/of.h>               /* For DT */ 
```

**Khai báo cấu trúc dùng để đại diện cho GPIO 27:**

```c
struct gpio_desc *gpio_27;
```

**Trỏ đến và lấy thông tin cấu hình hoặc khởi tạo thiết bị:**

```c
struct device *dev = &pdev->dev;
```

**Lấy và cấu hình chế độ OUTPUT cho GPIO 27 với mức thấp:**

```c
gpio27 = gpiod_get(dev, "led27", GPIOD_OUT_LOW);
```

**Đặt mức HIGH cho GPIO để ON LED:**

```c
gpiod_set_value(gpio_27, HIGH);
```

**Đặt mức LOW cho GPIO để OFF LED:**

```c
gpiod_set_value(gpio_27, LOW);
```

**Giải phóng descriptor GPIO:**

```c
gpiod_put(gpio_27);
```

**Định nghĩa một Device Tree Match Table dùng để ánh xạ driver với thiết bị phần cứng dựa trên thông tin trong Device Tree:**

```c
static const struct of_device_id gpiod_dt_ids[] = {
    { .compatible = "gpio-descriptor-based", },
    { /* sentinel */ }
};;
```

**Cấu hình platform driver:**

```c
static struct platform_driver mypdrv = {
    .probe = my_pdrv_probe,
    .remove = my_pdrv_remove,
    .driver = {
        .name = "descriptor-based",
        .of_match_table = of_match_ptr(gpiod_dt_ids),
        .owner = THIS_MODULE,
    },
};
```

**Đăng ký platform driver:**

```c
module_platform_driver(mypdrv);
```

## Full GPIO driver

```c
#include <linux/module.h>
#include <linux/gpio.h>
#include <linux/platform_device.h>
#include <linux/gpio/consumer.h>
#include <linux/of.h>

#define LOW     0
#define HIGH    1

struct gpio_desc *gpio_27;

static const struct of_device_id gpiod_dt_ids[] = {
    { .compatible = "gpio-descriptor-based", },
    { /* sentinel */ }
};

static int my_pdrv_probe(struct platform_device *pdev)
{
    struct device *dev = &pdev->dev;
    gpio_27 = gpiod_get(dev, "led27", GPIOD_OUT_LOW);
    gpiod_set_value(gpio_27, HIGH);

    pr_info("%s - %d", __func__, __LINE__);
    return 0;
}

static int my_pdrv_remove(struct platform_device *pdev)
{
    gpiod_set_value(gpio_27, LOW);
    gpiod_put(gpio_27);

    pr_info("%s - %d", __func__, __LINE__);
    return 0;
}

static struct platform_driver mypdrv = {
    .probe = my_pdrv_probe,
    .remove = my_pdrv_remove,
    .driver = {
        .name = "descriptor-based",
        .of_match_table = of_match_ptr(gpiod_dt_ids),
        .owner = THIS_MODULE,
    },
};
module_platform_driver(mypdrv);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("trainee");
MODULE_DESCRIPTION("Descriptor-based GPIO");
MODULE_VERSION("1.0");
```

**Device Tree Source:**

```dts
/dts-v1/;
/plugin/;
/ {
        compatible = "brcm,bcm2835";
        fragment@0 {
                target-path = "/";
                __overlay__ {
                        my_device {
                                compatible = "gpio-descriptor-based";
                                status = "okay";
                                led27-gpios = <&gpio 27 0>;
                        };
                };
        };
};
```

**Makefile:**

```Makefile
obj-m += dt_gpio.o

all: module dt

module:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
				
dt: testoverlay.dts
        dtc -@ -I dts -O dtb -o testoverlay.dtbo testoverlay.dts
				
clean:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
        rm -rf testoverlay.dtbo
```

**Build và overlay device tree:**

```bash
$ dtc -@ -I dts -O dtb -o gpio_overlay.dtbo gpio_overlay.dts
$ sudo dtoverlay gpio_overlay.dtbo
```

**Make:**

```bash
$ make
make -C /lib/modules/6.6.51+rpt-rpi-v6/build M=/home/pi/Test/gpio modules
make[1]: Entering directory '/usr/src/linux-headers-6.6.51+rpt-rpi-v6'
  CC [M]  /home/pi/Test/gpio/dt_gpio.o
  MODPOST /home/pi/Test/gpio/Module.symvers
  CC [M]  /home/pi/Test/gpio/dt_gpio.mod.o
  LD [M]  /home/pi/Test/gpio/dt_gpio.ko
make[1]: Leaving directory '/usr/src/linux-headers-6.6.51+rpt-rpi-v6'
```

**Insmod module:**

```bash
$ sudo insmod dt_gpio.ko
```

