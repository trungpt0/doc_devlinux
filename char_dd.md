Trong Linux, Device Driver là một trong những thành phần của Kernel giúp hệ điều hành tương tác với thiết bị phần cứng. Device Driver được chia thành hai loại chính tương tác với phần cứng đó là **Block Device Driver** và **Character Device Driver**. Và trong bài viết này, hãy cùng tìm hiểu xem Character Device Driver là gì nhé.

# 1. Giới thiệu về Character Device Driver

Tương tự như Device Driver, **Character Device Driver** tương tác với thiết bị phần cứng nhưng tuần tự theo từng **ký tự** (Byte) thay vì theo từng **khối** (Block). Sở dĩ có sự phân chia như vậy là do tốc độ, khối lượng và cách thức tổ chức dữ liệu được truyền từ thiết bị vào hệ thống và ngược lại. Ví dụ đối với các thiết bị có tốc độ chậm, quản lý dữ liệu nhỏ và việc truy cập dữ liệu không thường xuyên như bàn phím, chuột, Serial Port hay Joystick thì Character Device Driver được dùng để tương tác với các thiết bị này.

Chúng ta có thể thấy các Character Device trong thư mục **/dev** bằng cách dùng lệnh:

```bash
$ ls -l /dev
crw-rw-rw-  1 root   tty       5,   0 Nov  4 21:47 tty
crw--w----  1 root   tty       4,   0 Nov  4 16:24 tty0
crw--w----  1 root   tty       4,   1 Nov  4 16:24 tty1
crw--w----  1 root   tty       4,  10 Nov  4 16:24 tty10
crw--w----  1 root   tty       4,  11 Nov  4 16:24 tty11
crw--w----  1 root   tty       4,  12 Nov  4 16:24 tty12
crw--w----  1 root   tty       4,  13 Nov  4 16:24 tty13
[...]
```

Những device có chữ **c** ở đầu chính là Character Devices.

# 2. Major và Minor number

Thường thì sẽ có rất nhiều device trong Linux, mỗi device sẽ được gọi là **Device Node** và được định danh bằng 2 số đó là **Major number** và **Minor number**.

```bash
[...]
brw-rw----  1 root   disk      8,   0 Nov  5 09:07 sda
brw-rw----  1 root   disk      8,   1 Nov  5 09:07 sda1
[...]
crw--w----  1 root   tty       4,  62 Nov  5 09:07 tty62
crw--w----  1 root   tty       4,  63 Nov  5 09:07 tty63
[...]
```

Hãy xem các device bên trên, **Major number nằm ở cột thứ 4** là "8" đối với sda và "4" đối với tty. **Minor number nằm ở cột thứ 5** là "0" "1" với sda và "62" "63" đối với tty.

Major number dùng để xác định **loại thiết bị, driver liên kết với thiết bị** nên số Major của các device có thể trùng nhau. ví dụ: disk, serial port... Không giống như Major number,  mỗi device sẽ có Minor number khác nhau tức là nó được dùng để **phân biệt các thiết bị sử dụng chung driver**.

Để tạo một character device, hãy sử dụng lệnh:

```bash
$ mknod /dev/devlinux c 236 0
```

Với lệnh **mknod** dùng để tạo một device file; **/dev/"devlinux"** là tên thiết bị; **c** (character) hoặc **b** (block); **236** và **0** là major và minor number của device.

Để xóa một character device, hãy sử dụng lệnh:

```bash
$ rm /dev/devlinux
```

Tiếp theo mình sẽ giới thiệu các cấu trúc dữ liệu cho một Character Device.

# 3. Data Structures cho một Character Device

Khi viết Character Device Driver, chúng ta sẽ sử dụng 3 cấu trúc dữ liệu: **struct file_operations**, **struct file** và **struct inode**. Những cấu trúc này đều có sự liên quan đến hầu hết các thao tác của driver.

## 3.1 Struct file_operations

Để thao tác cơ bản với Character Device như open, close, read, write thì **struct file_operations** sẽ tập hợp các hàm callback mà driver định nghĩa để xử lý các thao tác này trên thiết bị. Struct file_operations được định nghĩa ở **linux/fs.h**.

```c
struct file_operations {
	struct module *owner;
	ssize_t (*read) (struct file *, char __user *, size_t, loff_t *);
	ssize_t (*write) (struct file *, const char __user *, size_t, loff_t *);
	[...]
	int (*open) (struct inode *, struct file *);
	int (*release) (struct inode *, struct file *);
	[...]
```

Cấu trúc này giúp kết nối các tác vụ của driver với device number. Mỗi trường trong file_operations sẽ trỏ đến một hàm được định nghĩa bởi driver. Nếu trỏ đến không thành công thì **file_operations** sẽ set trường đó là **NULL** và khi đó userspace sẽ không gọi được đến tác vụ tương ứng. Sau đây thì mình sẽ chia sẻ về một số trường cơ bản khi viết Character Device Driver.

```c
struct module *owner;
```

Cấu trúc này sẽ trỏ đến module sở hữu driver, giúp ngăn không cho phép giải phóng module khi đang sử dụng. Thông thường, cấu trúc này được thiết lập là **THIS_MODULE**.

```c
int (*open) (struct inode *, struct file *);
```

Trường này định nghĩa hàm mở device. Hàm này được gọi khi một tiến trình yêu cầu mở Device File, tức là khi tiến trình sử dụng lệnh **open()** trong userspace để truy cập thiết bị. Thực tế thì việc open Device File luôn thành công mặc dù trường **open** được đặt là **NULL** nhưng driver sẽ không nhận được thông báo về sự kiện mở này.

```c
int (*release) (struct inode *, struct file *);
```

Trường này định nghĩa hàm **đóng thiết bị**. Hàm release được gọi khi một tiến trình đóng Device File, thường xảy ra khi tiến trình gọi close() để ngừng truy cập thiết bị.

```c
ssize_t (*read) (struct file *, char __user *, size_t, loff_t *);
```

Trường này định nghĩa hàm đọc dữ liệu từ device. Hàm này **sao chép dữ liệu từ Kernel vào không gian bộ nhớ của người dùng** khi hàm read() được gọi. Nếu đọc dữ liệu thành công hàm trả về số **byte** đã đọc thành công. Nếu là NULL pointer thì system call sẽ trả về userspace **-EINVAL**.

```c
ssize_t (*write) (struct file *, const char __user *, size_t, loff_t *);
```

Trường này định nghĩa hàm ghi dữ liệu vào thiết bị. Hàm này **sao chép dữ liệu từ không gian bộ nhớ của người dùng vào Kernel** khi hàm write() trên tầng app được gọi. Nếu ghi dữ liệu thành công, hàm sẽ trả về số **byte** đã ghi thành công. Nếu trường này là **NULL**, system call sẽ trả về **-EINVAL** cho người dùng trong userspace.

## 3.2 Struct inode

Struct inode là một cấu trúc dữ liệu dùng để đại diện cho một đối tượng file hệ thống. Mỗi inode sẽ chứa các thông tin về một file như quyền truy cập, kích thước, và thông tin về thời gian.

```c
struct inode {
	umode_t			i_mode;
	[...]
	dev_t			i_rdev;
	const struct file_operations	*i_fop;
	struct cdev		*i_cdev;
	[...]
```

Các thành phần của struct inode liên quan đến Character Device giúp Kernel xử lý và quản lý các thiết bị dạng ký tự trong hệ thống Linux. Một số thành phần quan trọng của struct inode đối với Character Device như:
* **`umode_t i_mode`**: trường này chứa thông tin về kiểu và quyền truy cập của file. **i_mode** sẽ xác định đây là một thiết bị ký tự bằng cờ **S_IFCHR** (tức là Character Device).
* **`dev_t i_rdev`**: trường này dùng để nhận dạng thiết bị, **i_rdev** chứa  Major và Minor number.
* **`const struct file_operations *i_fop`**: trường này chứa con trỏ tới cấu trúc file_operations của thiết bị, cho phép Kernel sử dụng các hàm như open, read, write, ioctl của Character Device.
* **`struct cdev *i_cdev`**: trường này liên kết inode của thiết bị với cấu trúc cdev.

## 3.3 Struct file

**Struct file** chứa các thông tin về một file đã được mở. Khi một Character Device được mở, Kernel sẽ tạo một đối tượng struct file để **lưu trữ trạng thái và thông tin cần thiết** cho việc thao tác trên thiết bị này.

```c
struct file {
	[...]
	fmode_t				f_mode;
	const struct file_operations	*f_op;
	void				*private_data;
	struct inode			*f_inode;
	unsigned int			f_flags;
	[...]
```

Các thành phần chính trong struct file có liên quan đến Character Device là:
* **`fmode_t	f_mode`**: trường này dùng để xác định quyền truy cập của tiến trình đối với Character Device như đọc (**FMODE_READ**), ghi (**FMODE_WRITE**), vị trí con trỏ đọc/ghi (**FMODE_LSEEK**), ... 
* **`const struct file_operations	*f_op`**: là một trường đóng vai trò liên kết với các hàm thao tác (operations) của driver đối với Character Device. Thông qua f_op, kernel biết được các hàm nào sẽ được sử dụng để xử lý các thao tác như mở, đọc, ghi, hay đóng thiết bị.
* **`void *private_data`**: trường này dùng để lưu thông tin riêng của driver khi Character Device được mở. 
* **`struct inode	*f_inode`**: trường này là một con trỏ tới struct inode. Nó chứa thông tin về device number là major và minor để xác định driver tương ứng.
* **`unsigned int f_flags`**: trường f_flags lưu trữ các cờ của device như **O_RDONLY**, **O_WRONLY**, **O_RDWR**, **O_NONBLOCK** , **O_SYNC**.

# 4. Viết một Character Device Driver cơ bản

Để viết một Character Device Driver, về cơ bản gồm các bước đó là đăng ký device number cho device file, tạo class device, tạo device file và cuối cùng là add device vào system.

## 4.1 Register Device Number

Có 2 cách để đăng ký Device Number đó là đăng ký **Device Number tĩnh** và **Device Number động**.

### 4.1.1 Static Device Number Allocation

Để đăng ký Device Number trước tiên ta cần một kiểu dữ liệu số nguyên không dấu u32 **dev_t** được sử dụng để lưu Major và Minor number.

```c
dev_t dev_num;
```

Đăng ký Device Number tĩnh có nghĩa là ta sẽ tự chọn Major number và Minor cho Device File bằng macro **MKDEV**.

```c
MKDEV(int major, int minor);
```

Nếu muốn lấy giá Major và Minor number, thì 2 macro dưới đây được sử dụng để làm việc đó.

```c
MAJOR(dev_t dev);
MINOR(dev_t dev);
```

Cuối cùng ta sử dụng hàm **register_chrdev_region** định nghĩa tại **linux/fs.h** để đăng ký device number.

```c
int register_chrdev_region(dev_t first, unsigned count, const char *name);
```

Với,
* first: là số Major hoặc cặp số được lưu trong biến kiểu dev_t.
* count: là tổng số device liền kề muốn đăng ký.
* name: là tên của device hoặc driver sẽ được xuất hiện ở /proc/devices

Hàm **register_chrdev_region**  sẽ trả về 0 khi đăng ký device number thành công. Khi error thì một số âm sẽ được trả về. 

Để giải phóng device number ta sử dụng hàm:

```c
void unregister_chrdev_region(dev_t dev, unsigned count);
```

Với,
* dev_t dev: là cặp số Major và Minor được lưu trong biến kiểu dev_t.
* unsigned count: là tổng số device liền kề muốn xóa.

Sau đây là ví dụ về Static Device Number Allocation với file chardd_static.c:
```c
#include <linux/module.h>
#include <linux/fs.h>

dev_t dev_num = MKDEV(236, 0);

/**
 * Module initialization function
*/
static int __init chardd_static_init(void)
{
    int ret;

    /* Register a range of device numbers */
    if ((ret = register_chrdev_region(dev_num, 1, "devlinux_dev")) != 0) {
        pr_err("can not register device\n");
        return -1;
    }
    pr_info("Major = %d Minor = %d\n", MAJOR(dev_num), MINOR(dev_num));

    pr_info("Kernel Module Inserted Successfully!\n");
    return 0;
}

/**
 * Module exit function
*/
static void __exit chardd_static_exit(void)
{
    /* Unregister a range of device numbers */
    unregister_chrdev_region(dev_num, 1);
    pr_info("Kernel Module Removed Successfully!\n");
}

/**
 * Register initialization and exit functions of the module
*/
module_init(chardd_static_init);
module_exit(chardd_static_exit);

/**
 * Module Information
*/
MODULE_LICENSE("GPL");
MODULE_AUTHOR("DEVLINUX - Trung Tran");
MODULE_DESCRIPTION("A alloc device file static");
MODULE_VERSION("1.0");
```

Build driver, load module và check device number bằng lệnh:

```bash
$ make
$ sudo insmod chardd_static.ko
$ cat /proc/devices | grep "devlinux_dev"
236 devlinux_dev
$ sudo rmmod chardd_static
```

### 4.1.2 Dynamic Device Number Allocation

Tương tự như phương pháp tĩnh, ta cũng cần một biến có kiểu dữ liệu **dev_t** để lưu cặp số Major và Minor của device.

```c
dev_t dev_num;
```

Đối với phương pháp động, hàm **alloc_chrdev_region** định nghĩa tại **linux/fs.h** sẽ tự động chọn Major number mà không bị trùng, xung đột.

```c
int alloc_chrdev_region(dev_t *dev, unsigned minor, unsigned count, const char *name);
```

Với,
* dev: địa chỉ của biến kiểu dev_t dùng để lưu cặp số device.
* minor: là minor number muốn đăng ký.
* count: số device muốn cấp phát
* name: là tên của device hoặc driver sẽ được xuất hiện ở /proc/devices

Để giải phóng device number ta sử dụng như trên phương pháp tĩnh. Sau đây là ví dụ về Dynamic Device Number Allocation với file chardd_dyna.c

```c
#include <linux/module.h>
#include <linux/fs.h>

dev_t dev_num;

/**
 * Module initialization function
*/
static int __init chardd_dyna_init(void)
{
    int ret;

    /* Register a range of device numbers. The major number will be chosen dynamically. */
    if ((ret = alloc_chrdev_region(&dev_num, 0, 1, "devlinux_dev")) != 0) {
        pr_err("can not alloc device major\n");
        return -1;
    }
    pr_info("Major = %d Minor = %d\n", MAJOR(dev_num), MINOR(dev_num));

    pr_info("Kernel Module Inserted Successfully!\n");
    return 0;
}

/**
 * Module exit function
*/
static void __exit chardd_dyna_exit(void)
{
    /* Unregister a range of device numbers */
    unregister_chrdev_region(dev_num, 1);
    pr_info("Kernel Module Removed Successfully!\n");
}

/**
 * Register initialization and exit functions of the module
*/
module_init(chardd_dyna_init);
module_exit(chardd_dyna_exit);

/**
 * Module Information
*/
MODULE_LICENSE("GPL");
MODULE_AUTHOR("DEVLINUX - Trung Tran");
MODULE_DESCRIPTION("A alloc device file dynamic");
MODULE_VERSION("1.0");
```

Build driver, load module và check device number bằng lệnh:

```bash
$ make
$ sudo insmod chardd_dyna.ko
$ cat /proc/devices | grep "devlinux_dev"
236 devlinux_dev
$ sudo rmmod chardd_dyna
```

## 4.2 Tạo Device File

Bước tiếp theo là tạo một Character Device File để có thể tương tác với device file ở bước sau. Về tạo Device File cũng có hai cách cơ bản đó là tạo thủ công bằng lệnh và tạo tự động bằng các hàm tạo class và device.

### 4.2.1 Phương pháp tạo Device File thủ công

Để tạo Device File theo phương pháp này ta chỉ cần sử dụng lệnh:

```bash
mknod <option> <name> <device type> <major> <minor>
```

Với,
* option: --mode, --help, --version.
* name: là tên device file sẽ xuất hiện trong /dev/name.
* device type: là loại device với c (character) hoặc b (block).
* major: là số major của device
* minor: là số minor của device

Demo:

```bash
$ sudo mknod -m 666 /dev/devlinux_dev c 236 0
$ ls -l /dev | grep "devlinux_dev"
crw-rw-rw- 1 root root 236, 0 Nov 6 10:04 devlinux_dev
```

### 4.2.2 Phương pháp tạo Device File bằng hàm tạo class device và device

Thường thì các device trong kernel có cùng chức năng sẽ được phân vào một class device. Vậy nên việc tạo class gúp tổ chức và quản lý các device một cách có cấu trúc hơn, làm cho việc phát triển driver trở nên dễ dàng và hiệu quả hơn. Chúng ta có thể xem các class tại **/sys/class**.

```bash
$ ls -l /sys/class
total 0
[...]
drwxr-xr-x 2 root root 0 Jan  1  2000 i2c-adapter
drwxr-xr-x 2 root root 0 Jan  1  2000 i2c-dev
[...]
```

#### 4.2.2.1 Tạo class device 

Để tạo một class device ta dùng hàm:

```c
struct class *class_create(struct module *owner, const char *name);
```

Với,
* struct module *owner: là cấu trúc trỏ đến module sở hữu class device thường là THIS_MODULE
* const char *name: là tên của class device

Nếu tạo class device thành công, hàm sẽ trả về một con trỏ đến **struct class**. Nếu thất bại trong việc tạo class device nó sẽ trả về một con trỏ có giá trị ERR_PTR.

Để giải phóng class device ta dùng hàm:

```c
void class_destroy (struct class * cls);
```

#### 4.2.2.2 Tạo device 

Tiếp đến là tạo device thông qua hàm **device_create()**. Việc này tạo một thiết bị trong Kernel được hiển thị tại **/dev**, cho phép hệ thống quản lý thiết bị nhận biết thiết bị này. Đồng thời iúp người dùng và các ứng dụng có thể tương tác với thiết bị thông qua các syscall như open, read, write...

```c
struct device *device_create(struct class *class, struct device *parent, dev_t dev, void *drvdata, const char *fmt, ...);
```

Với,
* struct class *class: con trỏ đến class của device.
* struct device *parent: Con trỏ đến thiết bị cha, nếu không có hãy đặt NULL.
* dev_t dev: là device number.
* void *drvdata:  dữ liệu bổ sung của device
* const char *fmt: là tên của device

Để xóa giải phóng device ta dùng lệnh:

```c
void device_destroy (struct class * class, dev_t dev);
```

Với,
* struct class *class: con trỏ đến class của device.
* dev_t dev: là device number.

Demo:
```c
#include <linux/module.h>
#include <linux/fs.h>
#include <linux/kdev_t.h>
#include <linux/device.h>

[...]
static struct class *dev_class = NULL;

static int __init dfile_init(void)
{
	[...]
	
	dev_class = class_create(THIS_MODULE, "devlinux_class");
    if (IS_ERR(dev_class)) {
        pr_err("can not create class device\n");
    }
		
	if (IS_ERR(device_create(dev_class, NULL, dev_num, NULL, "devlinux"))) {
        pr_err("can not create device\n");
    }
		
	[...]
}

static void __exit dfile_exit(void)
{
    device_destroy(dev_class, dev_num);
    class_destroy(dev_class);
	[...]
}

[...]
```

Build driver, load module và check device file bằng lệnh:

```bash
$ ls -l /dev/ | grep "devlinux"
crw------- 1 root   root    236,   0 Sep 16 12:57 devlinux
```

## 4.3 Add device to the Kernel

Để thêm thao tác được các tác với Character Device File ta cần phải tạo và đăng ký device với Kernel và 2 hàm cho phép điều đó là **cdev_init** và **cdev_add** được định nghĩa tại **linux/cdev.h**.

```c
void cdev_init(struct cdev *cdev, const struct file_operations *fops);
```

Với,
* struct cdev *cdev: là một con trỏ đến cấu trúc cdev (character device) của thiết bị

```c
struct cdev {
	struct kobject kobj;
	struct module *owner;
	const struct file_operations *ops;
	struct list_head list;
	dev_t dev;
	unsigned int count;
}
```

* const struct file_operations *fops:  là con trỏ đến struct file_operations chứa các thao tác cơ bản như read, write, open, release, v.v.

```c
static struct file_operations fops =
{
    .open = device_open,
    .release = device_release,
    .read = device_read,
    .write = device_write,
	[...]
};
```

Lệnh dùng để add cdev vào Kernel là:

```c
int cdev_add(struct cdev *cdev, dev_t dev, unsigned count);
```

Với
* struct cdev *cdev: à một con trỏ đến cấu trúc cdev (character device) của thiết bị
* dev_t dev: biến chứa device number
* unsigned count: số lượng thiết bị cần đăng ký với số minor liên tiếp nhau

# 5. Build và tương tác với Character Device Driver

Full Character Device Driver:

```c
#include <linux/module.h>
#include <linux/fs.h>
#include <linux/device.h>
#include <linux/cdev.h>
#include <linux/uaccess.h>
#include <linux/slab.h>

#define BUFFER_SIZE 1024

dev_t dev_num = 0; // Device number
static struct class *dev_class = NULL; // Device class
static struct cdev devlinux_cdev; // Character device structure
static char *k_buffer = NULL;

// Function to handle opening the device
static int device_open(struct inode *inode, struct file *filp)
{
    pr_info("device was opened!\n");
    return 0;
}

// Function to handle closing the device
static int device_release(struct inode *inode, struct file *file)
{
    pr_info("device was closed!\n");
    return 0;
}

// Function to handle reading from the device
static ssize_t device_read(struct file *filp, char __user *buffer, size_t len, loff_t *off)
{
    if (*off >= BUFFER_SIZE) return 0;
    if (len > BUFFER_SIZE - *off) len = BUFFER_SIZE - *off;

    if (copy_to_user(buffer, k_buffer + *off, len)) {
        pr_err("cannot copy data to user\n");
        return -EFAULT;
    }

    *off += len;
    pr_info("device was read!\n");
    return len;
}

// Function to handle writing to the device
static ssize_t device_write(struct file *filp, const char __user *buffer, size_t len, loff_t *off)
{
    if (*off >= BUFFER_SIZE) return 0;
    if (len > BUFFER_SIZE - *off) len = BUFFER_SIZE - *off;

    if (copy_from_user(k_buffer + *off, buffer, len)) {
        pr_err("cannot copy data to kernel\n");
        return -EFAULT;
    }

    *off += len;
    pr_info("device was write!\n");
    return len;
}

static struct file_operations fops =
{
    .owner = THIS_MODULE,
    .open = device_open,
    .release = device_release,
    .read = device_read,
    .write = device_write,
};

/**
 * Module initialization function
*/
static int __init char_dev_driver_init(void)
{
    int ret;

    /* Register a range of device numbers. The major number will be chosen dynamically. */
    if ((ret = alloc_chrdev_region(&dev_num, 0, 1, "devlinux_dev")) != 0) {
        pr_err("can not alloc device major\n");
        return -1;
    }
    pr_info("Major = %d Minor = %d\n", MAJOR(dev_num), MINOR(dev_num));

    /* Create a struct class structure */
    dev_class = class_create(THIS_MODULE, "devlinux_class");
    if (IS_ERR(dev_class)) {
        pr_err("can not create class device\n");
        goto class_err;
    }

    /* Creates a device */
    if (IS_ERR(device_create(dev_class, NULL, dev_num, NULL, "devlinux"))) {
        pr_err("can not create device\n");
        goto dev_err;
    }

    /* Allocate kernel buffer */
    k_buffer = kmalloc(BUFFER_SIZE, GFP_KERNEL);
    if (!k_buffer) {
        pr_err("failed to allocate memory for kernel buffer\n");
        goto dev_err;
    }

    cdev_init(&devlinux_cdev, &fops);
    if (cdev_add(&devlinux_cdev, dev_num, 1) < 0) {
        pr_err("can not add character device\n");
        goto cdev_err;
    }

    pr_info("Kernel Module Inserted Successfully!\n");
    return 0;

cdev_err:
    kfree(k_buffer);
    device_destroy(dev_class, dev_num);
dev_err:
    class_destroy(dev_class);
class_err:
    unregister_chrdev_region(dev_num, 1);
    return -1;
}

/**
 * Module exit function
*/
static void __exit char_dev_driver_exit(void)
{
    kfree(k_buffer); // Free the kernel buffer
    cdev_del(&devlinux_cdev); // Remove the character device
    device_destroy(dev_class, dev_num); // Removes a device
    class_destroy(dev_class); // Destroys a struct class structure
    unregister_chrdev_region(dev_num, 1); // Unregister a range of device numbers
    pr_info("Kernel Module Removed Successfully!\n");
}

/**
 * Register initialization and exit functions of the module
*/
module_init(char_dev_driver_init);
module_exit(char_dev_driver_exit);

/**
 * Module Information
*/
MODULE_LICENSE("GPL");
MODULE_AUTHOR("DEVLINUX - Trung Tran");
MODULE_DESCRIPTION("Interacting with device file");
MODULE_VERSION("1.0");
```

Build driver:

```bash
$ make
```

Load module vào kernel:

```bash
$ sudo insmod char_device_driver
```

Cấp mode và thao tác với device file:

```bash
$ sudo chmod 666 /dev/devlinux
$ sudo echo "DEVLINUX" > /dev/devlinux
$ sudo cat /dev/devlinux
DEVLINUX
```

Xem log:

```bash
$ dmesg | tail -10
[...]
[  965.707133] Major = 236 Minor = 0
[  965.712073] Kernel Module Inserted Successfully!
[  980.762845] device was opened!
[  980.842192] device was write!
[  980.856148] device was closed!
[  987.175487] device was opened!
[  987.175640] device was read!
[  987.176581] device was closed!
```

Qua đây mình đã chia sẻ sơ lược và cách viết Character Device Driver. Hẹn gặp các bạn ở các bài viết sau!

**Facebook Group Học viện Linux & Android Embedded (devlinux.vn)**: [tại đây](https://www.facebook.com/groups/481584733205269).

**Youtube**: [tại đây](https://www.youtube.com/@devlinux097).

**Tác giả: Trung Trần**