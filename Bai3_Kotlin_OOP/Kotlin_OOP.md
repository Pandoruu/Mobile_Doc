# Buổi X: Lập trình Hướng Đối Tượng (OOP) trong Kotlin

## 1. Giới thiệu về OOP và vai trò trong Kotlin

**OOP (Object-Oriented Programming)** – Lập trình hướng đối tượng – là một phương pháp tổ chức chương trình thông qua các “đối tượng” (object), giúp mã nguồn dễ bảo trì, mở rộng, và tái sử dụng. Kotlin là một ngôn ngữ hiện đại kế thừa đầy đủ các tính chất OOP và còn hỗ trợ nhiều cú pháp ngắn gọn, thuận tiện.

Bốn tính chất kinh điển của OOP bao gồm:
- **Đóng gói (Encapsulation)**
- **Kế thừa (Inheritance)**
- **Đa hình (Polymorphism)**
- **Trừu tượng (Abstraction)**

Ngoài ra, chúng ta sẽ tìm hiểu về **backing field** – một tính năng đặc biệt trong quản lý property của Kotlin.

---

## 2. Đóng gói (Encapsulation)

### **Khái niệm**
Đóng gói là quá trình bảo vệ dữ liệu bên trong một lớp (class) bằng cách giới hạn quyền truy cập trực tiếp từ bên ngoài. Chỉ cung cấp các phương thức (getter/setter) để thao tác với dữ liệu.

### **Lợi ích**
- Ngăn chặn sửa đổi dữ liệu bừa bãi.
- Giúp kiểm soát giá trị hợp lệ của thuộc tính.
- Dễ bảo trì, thay đổi cài đặt bên trong mà không ảnh hưởng bên ngoài.

### **Cách hiện thực trong Kotlin**
- Dùng từ khóa `private`, `protected` cho thuộc tính.
- Dùng hàm `get`/`set` để truy xuất và kiểm soát.


**Ví dụ 1: Che giấu dữ liệu**
```kotlin
class Account(private var balance: Int) {
    fun getBalance(): Int = balance
    fun deposit(amount: Int) {
        if (amount > 0) balance += amount
    }
    fun withdraw(amount: Int) {
        if (amount > 0 && amount <= balance) balance -= amount
    }
}
fun main() {
    val acc = Account(1000)
    acc.deposit(500)
    acc.withdraw(200)
    println("Số dư: ${acc.getBalance()}") // Số dư: 1300
}
```

**Ví dụ 2: Custom getter/setter**
```kotlin
class Product(val name: String, private var _price: Double) {
    var price: Double
        get() = if (_price < 0) 0.0 else _price // Nếu giá nhỏ hơn 0 thì trả về 0.0
        set(value) { _price = value }
}

fun main() {
    val p = Product("Bánh mì", -5000.0)
    println("${p.name}: ${p.price}") // Bánh mì: 0.0
    p.price = 12000.0 //thay vì .get() như Java thì ta sẽ chấm trực tiếp tới biến đó nếu có getter
    println("${p.name}: ${p.price}") // Bánh mì: 12000.0
}
```

```kotlin
class Student {
    var name: String = "Unknown"
        set(value) {
            field = value.trim().replaceFirstChar { it.uppercase() }
        }
}
fun main() {
    val st = Student()
    st.name = "  an" // setter sẽ tự động chuẩn hóa
    println(st.name) // "An"
}
```
**Điểm nổi bật:**  
- Có thể kiểm soát logic khi gán giá trị mới cho thuộc tính.

---

## 3. Kế thừa (Inheritance)

### **Khái niệm**
Kế thừa cho phép một lớp con (subclass) thừa hưởng thuộc tính và phương thức từ lớp cha (superclass), đồng thời có thể mở rộng, bổ sung hoặc ghi đè (override) các hành vi đó.

### **Lợi ích**
- Tái sử dụng mã nguồn hiệu quả.
- Tạo cấu trúc phân cấp ngắn gọn, rõ ràng.

### **Cách hiện thực trong Kotlin**
- Lớp cha phải khai báo với `open` hoặc `abstract`.
- Lớp con dùng dấu `:` để kế thừa.

### **Ví dụ nổi bật**

**Ví dụ 1: Kế thừa và mở rộng**
```kotlin
open class Animal(val name: String) {
    open fun speak() = println("$name phát ra âm thanh lạ.")
}

class Cat(name: String) : Animal(name) {
    override fun speak() = println("$name kêu: Meo meo!")
}

fun main() {
    val tom = Cat("Tom")
    tom.speak() // Tom kêu: Meo meo!
}
```

**Ví dụ 2: Kế thừa nhiều cấp**
```kotlin
open class Vehicle(val brand: String) {
    open fun move() = println("$brand di chuyển")
}
class Car(brand: String, val seat: Int) : Vehicle(brand) {
    override fun move() = println("$brand chạy bằng 4 bánh với $seat ghế")
}
class Bus(brand: String, seat: Int) : Car(brand, seat) {
    fun pickUp() = println("$brand đón khách")
}
fun main() {
    val bus = Bus("Hyundai", 45)
    bus.move()    // Hyundai chạy bằng 4 bánh với 45 ghế
    bus.pickUp()  // Hyundai đón khách
}
```
**Lưu ý**:
- Kotlin chỉ cho phép kế thừa đơn (mỗi lớp chỉ có một cha).
- Lớp cha cần có từ khóa `open` hoặc `abstract`.
- Khi kế thừa từ một lớp cha trong Kotlin, nếu lớp cha có primary constructor (hàm khởi tạo chính), thì lớp con bắt buộc phải gọi lại (super) hàm khởi tạo này trong phần khai báo constructor của mình.
---
## 4. Đa hình (Polymorphism)

### **Khái niệm**
Đa hình cho phép đối tượng cùng kiểu cha có thể biểu hiện hành động khác nhau tùy thuộc vào lớp con thực tế được khởi tạo.

### **Lợi ích**
- Linh hoạt mở rộng hệ thống.
- Tăng tính trừu tượng và dễ mở rộng.

### **Cách hiện thực trong Kotlin**
- Dùng `open`/`override` cho phương thức.
- Có thể khai báo biến kiểu cha, nhưng gán đối tượng kiểu con.

### **Ví dụ nổi bật**

**Ví dụ 1: Đa hình qua phương thức override**
```kotlin
open class Shape {
    open fun area(): Double = 0.0
}
class Circle(val radius: Double) : Shape() {
    override fun area() = Math.PI * radius * radius
}
class Rectangle(val width: Double, val height: Double) : Shape() {
    override fun area() = width * height
}
fun printArea(shape: Shape) {
    println("Diện tích: ${shape.area()}")
}

fun main() {
    val shapes = listOf(Circle(2.0), Rectangle(3.0, 4.0))
    for (s in shapes) printArea(s)
    // Diện tích: 12.566370614359172
    // Diện tích: 12.0
}
```

**Ví dụ 2: Đa hình thông qua Interface**
```kotlin
interface Drawable {
    fun draw()
}
class Square : Drawable {
    override fun draw() = println("Vẽ hình vuông")
}
class Triangle : Drawable {
    override fun draw() = println("Vẽ hình tam giác")
}
fun main() {
    val shapes: List<Drawable> = listOf(Square(), Triangle())
    shapes.forEach { it.draw() }
    // Vẽ hình vuông
    // Vẽ hình tam giác
}
```

Ứng dụng của tính đa hình:
```kotlin
open class Animal {
    open fun speak() = println("Some sound")
}

class Dog : Animal() {
    override fun speak() = println("Woof!")
}

class Cat : Animal() {
    override fun speak() = println("Meow!")
}

fun makeAnimalSpeak(animal: Animal) {
    animal.speak()
}

fun main() {
    val animals: List<Animal> = listOf(Dog(), Cat(), Animal())
    for (a in animals) {
        makeAnimalSpeak(a)
    }
}
// Kết quả:
// Woof!
// Meow!
// Some sound
```
**Điểm nổi bật:**  
- T có thể đạt được Đa hình cả ở class và interface.
- Một hàm có thể xử lý nhiều loại đối tượng cùng lúc qua cùng một kiểu cha.

---

## 5. Trừu tượng (Abstraction)

### **Khái niệm**
Trừu tượng là khi ta chỉ mô tả một hàm là “cái gì” mà không quan tâm bên trong đó “làm như thế nào”.  
Lớp trừu tượng (abstract class) hoặc interface cung cấp các phương thức chưa cài đặt, yêu cầu lớp con phải thực hiện.

### **Lợi ích**
- Tăng tính linh hoạt, dễ thay đổi/hoán đổi cài đặt.
- Định nghĩa khung hành vi chuẩn cho nhiều lớp con phải tuân theo.

### **Cách hiện thực trong Kotlin**
- Sử dụng từ khóa `abstract` cho class hoặc method.
- Interface cũng là một dạng trừu tượng hóa hoàn toàn.

### **Ví dụ nổi bật**

**Ví dụ 1: Abstract Class**
```kotlin
abstract class Employee(val name: String) {
    abstract fun calcSalary(): Int
}
class FullTime(name: String, val base: Int) : Employee(name) {
    override fun calcSalary() = base * 2
}
class PartTime(name: String, val hour: Int, val wage: Int) : Employee(name) {
    override fun calcSalary() = hour * wage
}
fun main() {
    val emp: List<Employee> = listOf(
        FullTime("An", 5_000_000),
        PartTime("Hải", 80, 100_000)
    )
    emp.forEach { println("${it.name}: ${it.calcSalary()}") }
    // An: 10000000
    // Hải: 8000000
}
```

**Ví dụ 2: Interface trừu tượng hóa**
```kotlin
interface Logger {
    fun log(msg: String)
}
class FileLogger : Logger {
    override fun log(msg: String) = println("Ghi file: $msg")
}
class ConsoleLogger : Logger {
    override fun log(msg: String) = println("Màn hình: $msg")
}
fun main() {
    val logger: Logger = FileLogger()
    logger.log("Đây là log!") // Ghi file: Đây là log!
}
```
**Điểm nổi bật:**  
- Không thể tạo thực thể từ abstract class hoặc interface.
- Lớp con **bắt buộc** phải cài đặt các hàm abstract.

---

## 6. Backing Field trong Kotlin

### **Khái niệm**
**Backing field** là trường dữ liệu ẩn mà Kotlin tự động sinh ra để lưu trữ giá trị thực của một property khi bạn tự viết getter/setter cho property đó.

- Khi ta muốn kiểm soát logic truy cập/gán cho property (ví dụ: kiểm tra hợp lệ, log thay đổi,…)
- Không có backing field, property sẽ gây vòng lặp vô hạn khi gán/gọi chính nó trong getter/setter.

### **Quy tắc sử dụng**
- Chỉ dùng được từ khóa `field` bên trong getter/setter custom của property đó.
- Không thể dùng `field` ngoài phạm vi getter/setter.

### **Ví dụ nổi bật**

**Ví dụ 1: Kiểm soát giá trị hợp lệ**
```kotlin
class Person {
    var age: Int = 18
        set(value) {
            if (value >= 0) field = value
            else println("Tuổi không hợp lệ!")
        }
}
fun main() {
    val p = Person()
    p.age = 25
    println(p.age) // 25
    p.age = -1     // Tuổi không hợp lệ!
    println(p.age) // 25
}
```

**Ví dụ 2: Đếm số lần property bị thay đổi**
```kotlin
class Counter {
    var count: Int = 0
        set(value) {
            println("Giá trị mới: $value")
            field = value
        }
}
fun main() {
    val c = Counter()
    c.count = 10 // Giá trị mới: 10
    c.count = 20 // Giá trị mới: 20
}
```

**Ví dụ 3: Custom getter chỉ đọc, không cho set**
```kotlin
class Rectangle(val width: Int, val height: Int) {
    val area: Int
        get() = width * height
}
fun main() {
    val r = Rectangle(4, 5)
    println(r.area) // 20
    // r.area = 100 // Lỗi, chỉ read-only
}
```

**Điểm nổi bật:**  
- `field` đại diện cho giá trị thực lưu trữ bên trong property.
- Khi chỉ custom getter (không có setter), backing field **không được tạo ra**.

---

## 7. Tổng Kết

- **Đóng gói (Encapsulation):** Che giấu dữ liệu, chỉ cho thao tác qua giao diện an toàn.  
- **Kế thừa (Inheritance):** Tái sử dụng, mở rộng hành vi của lớp cha.  
- **Đa hình (Polymorphism):** Hành vi phong phú qua cùng một giao diện hoặc kiểu cha.  
- **Trừu tượng (Abstraction):** Xác định hành vi chung, ẩn đi chi tiết cài đặt.  
- **Backing field:** Biến ẩn giúp quản lý property an toàn, linh hoạt, đặc trưng của Kotlin.
