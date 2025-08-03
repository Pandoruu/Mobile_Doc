# Buổi 3: Lập trình Hướng Đối Tượng (OOP) trong Kotlin

## 1. Giới thiệu về OOP

**OOP (Object-Oriented Programming)** – Lập trình hướng đối tượng – là phương pháp tổ chức chương trình thông qua các “đối tượng” (object), giúp mã nguồn dễ bảo trì, mở rộng và tái sử dụng. Kotlin là một ngôn ngữ hiện đại, kế thừa đầy đủ các tính chất OOP và hỗ trợ cú pháp ngắn gọn, thuận tiện.

Bốn tính chất kinh điển của OOP gồm:
- **Đóng gói (Encapsulation)**
- **Kế thừa (Inheritance)**
- **Đa hình (Polymorphism)**
- **Trừu tượng (Abstraction)**

Ngoài ra, chúng ta sẽ tìm hiểu về **backing field** – một tính năng đặc biệt trong quản lý property của Kotlin.

---

## 2. Tính đóng gói (Encapsulation)

### Khái niệm
Đóng gói là quá trình bảo vệ dữ liệu bên trong một lớp bằng từ khóa `private`, `protected`,... Chỉ cho phép truy cập thông qua các phương thức kiểm soát (getter/setter).

### Lợi ích
- Ngăn chặn sửa đổi dữ liệu bừa bãi.
- Kiểm soát giá trị hợp lệ của thuộc tính.
- Dễ bảo trì, thay đổi cài đặt bên trong mà không ảnh hưởng bên ngoài.

### Cách hiện thực trong Kotlin
- Dùng từ khóa `private`, `protected` cho thuộc tính.
- Custom getter/setter để kiểm soát việc truy cập và chỉnh sửa.

**Ví dụ:**
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
```

Custom getter:
```kotlin
class Product(val name: String, private var _price: Double) {
    fun getPrice(): Double =
        if (_price < 0) 0.0 else _price

    fun setPrice(value: Double) {
        _price = value
    }
}
fun main() {
    val p = Product("Bánh mì", -5000.0)
    println("${p.name}: ${p.getPrice()}") // Bánh mì: 0.0
    p.setPrice(12000.0)
    println("${p.name}: ${p.getPrice()}") // Bánh mì: 12000.0
}
```
Custom setter:
```kotlin
class Student {
    var name: String = "Unknown"
        set(value) {
            field = value.trim().replaceFirstChar { it.uppercase() }
        }
}
```

---

## 3. Tính kế thừa (Inheritance)

### Khái niệm
- Tất cả các class trong Kotlin đều ngầm kế thừa từ class gốc `Any`, cung cấp các phương thức như: `equals()`, `hashCode()`, và `toString()`.
- Mặc định, các class trong Kotlin là **final** (không thể kế thừa). Muốn cho phép kế thừa, phải thêm từ khóa `open`.

### Cách khai báo kế thừa

**Ví dụ:**
```kotlin
open class Base(p: Int) // có thể được kế thừa

class Derived(p: Int) : Base(p) // kế thừa từ Base, truyền tham số vào constructor cha
```
- Nếu lớp cha có **primary constructor**, lớp con **bắt buộc** phải gọi lại trong phần khai báo.
- Nếu lớp con không có primary constructor, mỗi secondary constructor phải gọi `super(...)` đến constructor khác đã gọi tới cha.

**Ví dụ với secondary constructor:**
```kotlin
class GmailSender : EmailSender {
    // Constructor 1: chỉ có recipient
    constructor(recipient: String) : super(recipient) {
        println("Using default Gmail server")
    }

    // Constructor 2: có thêm server address
    constructor(recipient: String, server: String) : super(recipient) {
        println("Using custom Gmail server: $server")
    }
}
fun main() {
    val mail1 = GmailSender("a@example.com")
    println()
    val mail2 = GmailSender("b@example.com", "sigma")
}
```
Kết quá:
```
Sending email to a@example.com  
Using default Gmail server

Sending email to b@example.com  
Using custom Gmail server: sigma
```
### Thứ tự khởi tạo
- Constructor của lớp cha được gọi **trước tiên**, sau đó mới tới constructor của lớp con.
- Các thuộc tính `open` trong lớp con **chưa được khởi tạo** khi constructor của lớp cha thực thi.

**Ví dụ minh họa:**
```kotlin
open class Base(val name: String) {
    init { println("Khởi tạo lớp cha") }
    open val size: Int = name.length.also { println("Khởi tạo size ở lớp cha: $it") }
}

class Derived(name: String, val lastName: String)
    : Base(name.replaceFirstChar { it.uppercase() }.also { println("Tham số truyền cho cha: $it") }) {

    init { println("Khởi tạo lớp con") }
    override val size: Int = (super.size + lastName.length)
        .also { println("Khởi tạo size ở lớp con: $it") }
}
```

### Ghi đè (Overriding)
- Chỉ các method/properties khai báo với từ khóa `open` hoặc thuộc interface mới được override.
- Dùng từ khóa `override` ở lớp con.
- Nếu muốn **cấm ghi đè tiếp**, dùng `final override`.

**Ví dụ:**
```kotlin
open class Shape {
    open fun draw() { println("Drawing shape") }
    fun fill() { println("Fill shape") }
}

class Circle : Shape() {
    override fun draw() { println("Drawing circle") }
}
```

### Ghi đè thuộc tính
- Có thể override property với cùng kiểu hoặc override `val` bằng `var`, nhưng không ngược lại.

**Ví dụ:**
```kotlin
open class Shape {
    open val vertexCount: Int = 0
}
class Rectangle : Shape() {
    override val vertexCount = 4
}
```

**Override property trong constructor:**
```kotlin
interface Shape { val vertexCount: Int }
class Rectangle(override val vertexCount: Int = 4) : Shape
class Polygon : Shape { override var vertexCount: Int = 0 }
```

### Gọi hàm/thuộc tính từ lớp cha
- Dùng `super` để gọi hàm/thuộc tính từ lớp cha.
- Trong inner class, dùng `super@Outer` để gọi hàm cha của lớp ngoài.

**Ví dụ:**
```kotlin
open class Rectangle {
    open fun draw() { println("Drawing a rectangle") }
    val borderColor: String get() = "black"
}
class FilledRectangle : Rectangle() {
    override fun draw() {
        super.draw()
        println("Filling the rectangle")
    }
    val fillColor: String get() = super.borderColor
}
```

**Ví dụ inner class:**
```kotlin
class FilledRectangle: Rectangle() {
    override fun draw() {
        val filler = Filler()
        filler.drawAndFill()
    }
    inner class Filler {
        fun fill() { println("Filling") }
        fun drawAndFill() {
            super@FilledRectangle.draw()
            fill()
            println("Drawn a filled rectangle with color ${super@FilledRectangle.borderColor}")
        }
    }
}
```

### Kế thừa đa nguồn và quy tắc override
- Nếu kế thừa từ nhiều class/interface có cùng tên hàm, **bắt buộc** phải override và chỉ rõ super từ đâu.

**Ví dụ:**
```kotlin
open class Rectangle { open fun draw() { println("Rectangle") } }
interface Polygon { fun draw() { println("Polygon") } }

class Square : Rectangle(), Polygon {
    override fun draw() {
        super<Rectangle>.draw()
        super<Polygon>.draw()
    }
}
```

---

## 4. Tính đa hình (Polymorphism)

### Khái niệm
Đa hình cho phép một hàm, biến kiểu cha xử lý nhiều loại đối tượng là các lớp con khác nhau.

### Ví dụ
```kotlin
// Lớp cha (Base class)
open class Animal {
    open fun makeSound() {
        println("Some generic animal sound")
    }
}

// Lớp con kế thừa và override phương thức
class Dog : Animal() {
    override fun makeSound() {
        println("Woof! Woof!")
    }
}

class Cat : Animal() {
    override fun makeSound() {
        println("Meow~")
    }
}

// Hàm sử dụng đa hình
fun playWithAnimal(animal: Animal) {
    animal.makeSound()
}
fun main() {
    val dog = Dog()
    val cat = Cat()

    playWithAnimal(dog) // Output: Woof! Woof!
    playWithAnimal(cat) // Output: Meow~
}
```
Mặc dù `playWithAnimal()` nhận vào kiểu `Animal`, nhưng khi truyền vào `Dog` hay `Cat`, chương trình sẽ gọi đúng phương thức `makeSound()` tương ứng 
### Đa hình với interface
```kotlin
// Định nghĩa interface
interface Drawable {
    fun draw()
}

// Các lớp triển khai interface
class Circle : Drawable {
    override fun draw() {
        println("Drawing a circle")
    }
}

class Rectangle : Drawable {
    override fun draw() {
        println("Drawing a rectangle")
    }
}

// Hàm sử dụng tính đa hình
fun render(d: Drawable) {
    d.draw()
}
fun main() {
    val shapes: List<Drawable> = listOf(Circle(), Rectangle())

    for (shape in shapes) {
        render(shape)
    }
//Drawing a circle  
//Drawing a rectangle
}
```

---

## 5. Tính trừu tượng (Abstraction)

### Khái niệm
Trừu tượng là khi ta chỉ định nghĩa “cái gì” mà không quan tâm “làm như thế nào”. Lớp abstract hoặc interface cung cấp các hàm chưa cài đặt, yêu cầu lớp con phải thực hiện.

### Ví dụ abstract class
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
```

### Ví dụ interface
```kotlin
interface Logger { fun log(msg: String) }
class FileLogger : Logger { override fun log(msg: String) = println("Ghi file: $msg") }
class ConsoleLogger : Logger { override fun log(msg: String) = println("Màn hình: $msg") }
```

---

## 6. Backing field

### Khái niệm
**Backing field** là trường dữ liệu ẩn mà Kotlin tự động sinh ra để lưu trữ giá trị thực của một property khi bạn tự viết getter/setter cho property đó.

- Dùng từ khóa `field` trong getter/setter để truy cập giá trị thực.
- Chỉ dùng được trong phạm vi getter/setter của property đó.

### Ví dụ
```kotlin
class Person {
    var age: Int = 18
        set(value) {
            if (value >= 0) field = value
            else println("Tuổi không hợp lệ!")
        }
}
```
**Chỉ getter, không có backing field:**
```kotlin
class Rectangle(val width: Int, val height: Int) {
    val area: Int
        get() = width * height // Chỉ getter, không có backing field
}
```

---

> **Hãy thực hành từng ví dụ trên để hiểu rõ bản chất và sức mạnh của OOP trong Kotlin!**