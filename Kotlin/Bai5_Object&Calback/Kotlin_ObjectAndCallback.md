# Bài 5: Object và Callback trong Kotlin
## I. Object
Trong **Kotlin** đôi khi chúng ta sẽ cần tới một singleton có thể tái sử dụng nhiều lần hoặc một đối tượng dùng một lần. `Object` có thể giúp chúng ta làm được điều này bằng cách cung cấp `object declarations` để tạo một singleton và `object expressions` để tạo một đối tượng nặc danh hoặc đối tượng dùng một lần.

### 1. Object declarations
- Dùng để tạo 1 `singleton`.
- Được khai báo với từ khóa `object` + tên của singleton đó.
```kotlin
object MySingleton {
    val name = "Singleton"
    fun doSomething() {
        println("Doing something!")
    }
}
```
Khi dùng tới singleton đó ta có thể chấm thẳng tới tên của lớp

```kotlin
MySingleton.doSomething()
```
`Object declaration` có thể kế thừa từ class/interfaceinterface. Nhưng không thể bị kế thừa bởi class hoặc object khác.
```kotlin
open class Base {
    open fun hello() = println("Hello from Base")
}

object MySingleton : Base() {
    //Cách override giống như trong class thông thường:
    override fun hello() = println("Hello from Singleton")
}

//class SubClass : MySingleton() // Lỗi biên dịch
```

`object` không phải 1 biểu thức (expression) nên không thể gán cho 1 biến khác.

```kotlin
// Lỗi:
val myObject = object MySingleton {
    val name = "Singleton"
}
```
Ta có thể khai báo một `object` trong 1 class hoặc 1 object khác. Nhưng ta không thể tạo 1 object trong hàm.

```kotlin
class OuterClass {
    object Object1 { //object trong class
        fun sayHello() = println("Hello from inner object!")
    }
}

object OutterObject {
    object Object2 {// object trong object
        fun sayHi() = println("Hi from inner object!")
    }
}
fun main(){
    OuterClass.Object1.sayHello()
    OuterObject.Object2.sayHi()
}
```

```kotlin
fun foo() {
    object Object { // Lỗi biên dịch
        val name = "I cant exist"
    }
}
```

#### Data object
- Là sự kết hợp giữa data class và object declaration
- data object cho phép khai báo singleton có đầy đủ các phương thức dữ liệu như một data class (equals(), hashCode(), toString()). Các phương thức này không thể tùy chỉnh

```kotlin
data object MyDataObject {
    val number: Int = 3
}

fun main() {
    println(MyDataObject) 
    // in ra tên của data object
    val a = MyDataObject
    val b = MyDataObject
    print(a == b)
    //true
}
```
**Lưu ý:**
- Data object không có hàm `copy()` để đảm bảo singleton là thể hiện duy nhất, không thể tồn tại các bản sao.
- Không có hàm `componentN()` vì data object không có bất kì thuộc tính dữ liệu (data property) nào nên cũng không thể truy cập đến các thuộc tính dữ liệu.

#### Companion objects
- `companion object` là một object đặc biệt nằm bên trong một class.
- Nó cho phép định nghĩa các thành phần tĩnh (`static`) – giống như static method/field trong **Java**.
- Mỗi class chỉ có một `companion object`.

```kotlin
class MyClass {
    companion object Factory{
        fun create() = MyClass()
    }
}
fun main(){
    MyClass.create()
    MyClass.Factory.create()
    //MyClass.Companion.create() // Trong trường hợp ko đặt tên cho companion object (Companion là tên mặc định)
}
```
- Dù nhìn như `static`, các thành phần trong companion object thực chất là instance member của companion object - một singleton.
- Vì vậy `companion object` có thể kế thừa hoặc implement và có thể truyền như một đối tượng thực sự.
```kotlin
interface Clickable {
    fun click()
}

class Button {
    companion object : Clickable {
        override fun click() {
            println("Button clicked!")
        }
    }
}
fun main() {
    val clickable: Clickable = Button // Button.Companion
    clickable.click() // Output: Button clicked!
}
```

- Mặc định, **JVM** sẽ tạo `companion object` như một instance singleton, và các method của nó là instance method (không phải static method thật).
- Nếu muốn một phương thức hoặc field trong `companion object` trở thành `static` thực sự trên JVM ta có thể dùng annotation `@JvmStatic`.

```kotlin
class Example {
    companion object {
        @JvmStatic
        fun staticMethod() = "Hello from static"

        fun normalMethod() = "Hello from instance"
    }
}
```

### 2. Object expressions
#### Khởi tạo
- Là cách tạo một đối tượng vô danh (anonymous object) ngay tại chỗ, không cần đặt tên lớp. Tương đương với `anonymous class` từ **Java**
- Được dùng cho các tình huống chỉ cần sử dụng 1 lần.
- Khác với `object declaration` ở chỗ `object expression` là một biểu thức và có thể được khởi tạo trong 1 hàm.

```kotlin
val helloWorld = object {
    val hello = "Hello"
    val world = "World"
    override fun toString() = "$hello $world"
}
println(helloWorld) // Hello World
```

#### Kế thừa
`object expression` được sử dụng để khởi tạo các object có thể kế thừa từ một số class hoặc triển khai một interface.

```kotlin
open class Shape {
    open fun draw() = "Drawing shape"
}

interface Colored {
    val color: String
}
fun main() {
    val coloredCircle = object : Shape(), Colored {
        override val color = "Red"
        override fun draw() = "Drawing a $color circle"
    }
    println(coloredCircle.draw()) // Drawing a Red circle
}
```
#### Sử dụng Object expression làm kiểu trả về
Khi trả về một `anonymous object` từ một hàm hoặc property có phạm vi `local` hoặc `private`, tất cả các property và method của đối tượng vô danh đó đều có thể truy cập được thông qua hàm hoặc property đó:

```kotlin
class UserPreferences {
    private fun getPreferences() = object { //hàm private được trả về 1 anonymous object
        val theme: String = "Tối"
        val fontSize: Int = 14
    }

    fun printPreferences() { //một hàm khác của đối tượng UserPre có thể truy cập vào anonymous object
        val preferences = getPreferences()
        println("Giao diện: ${preferences.theme}, Cỡ chữ: ${preferences.fontSize}")
    }
}
```

=> Được sử dụng là một cách đơn giản để đóng gói dữ liệu hoặc hành vi mà không cần tạo một class riêng biệt.

Nếu một hàm hoặc property trả về anonymous object có phạm vi public, protected hoặc internal thì:
- Nếu object không có supertype:
Kiểu trả về thực tế là Any.
=> Chỉ truy cập được các thành viên của lớp Any (toString(), equals(), ...), không truy cập được các property đặc biệt được khai báo trong object đó.

- Nếu object implement đúng một supertype (class hoặc interface):
Kiểu trả về là supertype đó.
=> Chỉ dùng được các hàm/thuộc tính có trong supertype, các property đặc biệt không truy cập được.

- Nếu object implement nhiều supertype:
Kiểu trả về phải được khai báo rõ ràng.
=> Chỉ dùng được các thành viên có trong kiểu khai báo đó.

```kotlin
interface Notification {
    fun notifyUser()
}
interface DetailedNotification

class NotificationManager {
    // Trả về kiểu Any, các thành phần khác không truy cập được vào message
    fun getNotification() = object {
        val message: String = "Thông báo chung"
    }

    // Trả về kiểu Notification (do implement 1 interface)
    // Chỉ truy cập được notifyUser(), không truy cập được message
    fun getEmailNotification() = object : Notification {
        override fun notifyUser() {
            println("Gửi thông báo email")
        }
        val message: String = "Bạn có thư mới!"
    }

    // Trả về kiểu DetailedNotification (implement nhiều supertype)
    // Chỉ truy cập được các thành viên khai báo trong DetailedNotification
    fun getDetailedNotification(): DetailedNotification = object : Notification, DetailedNotification {
        override fun notifyUser() {
            println("Gửi thông báo chi tiết")
        }
        val message: String = "Nội dung thông báo chi tiết"
    }
}
```

#### Anonymous Object truy cập biến bên ngoài

Anonymous Object có thể tự do truy cập và thay đổi các biến của scope bên ngoài nó.

```kotlin
import java.awt.event.MouseAdapter
import java.awt.event.MouseEvent

fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // Các biến clickCount và enterCount có thể được truy cập và cập nhật bên trong object expression
}
```
## II. Call back
### 1. Higher-order Function
#### 1.1. Định nghĩa
- Là hàm nhận một hoặc nhiều hàm khác làm tham số hoặc trả về một hàm khác.
- Sử dụng trong lập trình hàm (functional programming).

Ví dụ:
```kotlin
fun doSomethingWithNumber(number: Int, receiver: (String?) -> Unit) {
    var result: String? = null
    // Ví dụ: kiểm tra số chẵn lẻ
    result = if (number % 2 == 0) "Even" else "Odd"
    receiver(result)
}

// Định nghĩa hàm receiver riêng biệt
fun printResult(result: String?) {
    println("Kết quả: $result")
}

fun main() {
    // Truyền hàm receiver vào cho higher-order function
    doSomethingWithNumber(5, ::printResult)  // Kết quả: Odd
    doSomethingWithNumber(8, ::printResult)  // Kết quả: Even
}
```
#### 1.2. Function Types
Để truyền một hàm vào hàm khác (hoặc trả về một hàm), ta cần có cách mô tả kiểu của hàm đó — tức là `function type`.

Ví dụ:
```kotlin
val onClick: () -> Unit = ...
//Khai báo một biến onClick có kiểu là hàm không nhận tham số và không trả về giá trị gì (Unit == Void)
```

Các kiểu này có một số cách viết đặc biệt tương ứng với đặc trưng của hàm: tham số và giá trị trả về.

- Tất cả các function type đều có danh sách kiểu tham số nằm trong dấu ngoặc đơn và kiểu trả về.
Ví dụ: (A, B) -> C là kiểu đại diện cho hàm nhận 2 tham số kiểu A và B, trả về kiểu C.
Danh sách tham số có thể rỗng, như () -> A.
Kiểu trả về Unit không thể bỏ qua.

- Kiểu hàm với receiver:
Có thể có thêm một receiver type, viết trước dấu chấm:
Ví dụ: A.(B) -> C là hàm có thể gọi trên một đối tượng A với tham số B, trả về C.
Hàm kiểu này thường dùng với function literal có receiver.

- Hàm suspend:
Kiểu hàm đặc biệt có từ khóa suspend, ví dụ: suspend () -> Unit hoặc suspend A.(B) -> C.

- Có thể đặt tên tham số trong kiểu hàm:
Ví dụ: (x: Int, y: Int) -> Point để tài liệu hóa ý nghĩa tham số.

- Kiểu hàm nullable:
Dùng dấu ngoặc: ((Int, Int) -> Int)?.

- Kết hợp nhiều function type:
(Int) -> ((Int) -> Unit)

- Dấu mũi tên phải (right-associative):
(Int) -> (Int) -> Unit tương đương với trên, nhưng khác với ((Int) -> (Int)) -> Unit.
- Đặt tên khác cho hàm với `typealias`:
```kotlin
typealias ClickHandler = (Button, ClickEvent) -> Unit
```

**Khởi tạo một function type**
Có nhiều cách để tạo một instance của function type:
- Dùng lamda:
```kotlin
val sum = { a: Int, b: Int -> a + b }
```
- Dùng anonymous func
```kotlin
val parse = fun(s: String): Int { return s.toIntOrNull() ?: 0 }
```
- Dùng function reference (như trong ví dụ đầu tiên):
  - Đến hàm: ::isOdd, String::toInt
  - Đến property: List<Int>::size
  - Đến constructor: ::Regex
  - Đến member của instance: foo::toString
- Dùng class tự cài đặt function type:
```Kotlin
class IntTransformer: (Int) -> Int {
    override fun invoke(x: Int): Int = x * 2
}
val intFunction: (Int) -> Int = IntTransformer()
```

- Kotlin có thể suy luận kiểu hàm:

```Kotlin
val a = { i: Int -> i + 1 } // Kiểu là (Int) -> Int
```
- Có thể hoán đổi giữa function type có receiver và không receiver:

```Kotlin
val repeatFun: String.(Int) -> String = { times -> this.repeat(times) }
val twoParameters: (String, Int) -> String = repeatFun // OK

fun runTransformation(f: (String, Int) -> String): String {
    return f("hello", 3)
}
val result = runTransformation(repeatFun) // OK
```
`repeatFun` là một biến hàm (function variable) có kiểu `function type with receiver`:
String.(Int) -> String,  tức là Hàm này có thể được gọi trên một đối tượng kiểu String, nhận vào một tham số kiểu Int tên là times, và trả về một String

`repeatFun` được gán cho biến twoParameters có kiểu (String, Int) -> String (không receiver).

Khi đó, tham số receiver (String) sẽ trở thành tham số đầu tiên của hàm.

**Lưu ý:** Function type không receiver được suy luận mặc định, nếu muốn khác cần ghi rõ kiểu.

### 2. Lambda expressions và anonymous functions
**Biểu thức lambda** và **Hàm ẩn danh** là các hàm không được khai báo nhưng được truyền trực tiếp dưới dạng 1 biểu thức.

Chúng thường được dùng để truyền vào các `higher-order function`.

#### 2.1. Lambda expression
- Lambda luôn nằm trong dấu {}.
- Tham số (có thể khai báo hoặc không khai báo kiểu), thân hàm nằm sau ->.
- Nếu không ghi chú kiểu, trình biên dịch sẽ tự suy luận.

**Cú pháp:**
```kotlin
{ thamSo1: Kieu1, thamSo2: Kieu2 -> thân hàm }
```

**Trailing lambda**:
Là một cú pháp đặc biệt trong Kotlin dùng để truyền biểu thức lambda làm tham số cuối cùng của hàm. Nếu tham số cuối cùng của hàm là một function type (tức là hàm nhận lambda làm tham số cuối) thì có thể viết biểu thức lambda bên ngoài dấu ngoặc tròn.

```kotlin
fun doSomething(x: Int, action: (Int) -> Unit) {
    action(x)
}

//Khi gọi hàm cách thông thường:
doSomething(5, { value -> println(value) })
//trailing lambda:
doSomething(5) { value -> println(value) }
```
Nếu lambda là tham số duy nhất thì ta có thể bỏ luôn dấu ngoặc tròn:

```Kotlin
run { println("Hello Kotlin") }
```

**Sử dụng it cho tham số đơn**

Nếu lambda chỉ có 1 tham số, có thể bỏ qua tên tham số đó và dùng mặc định là `it`:

```kotlin
ints.filter { it > 0 }
```
**Trả về giá trị từ lambda**
- Giá trị trả về mặc định là biểu thức cuối cùng trong lambda.
- Nếu muốn trả về rõ ràng thì ta dùng `return@tenHam`:

```kotlin
ints.filter {
    val shouldFilter = it > 0
    return@filter shouldFilter
}
```

**Dùng dấu _ cho biến không sử dụng**
Nếu không dùng một tham số trong lambda, có thể đặt tên là `_`:
```kotlin
map.forEach { (_, value) -> println("$value!") }
```

#### 2.2. Anonymous function
- Có thể chỉ rõ kiểu trả về, và khi dùng block body thì bắt buộc phải chỉ rõ hoặc sẽ mặc định là `Unit`.
- Khi truyền `anonymous function` làm tham số, phải để trong ngoặc

**Cú pháp:**
```kotlin
fun(thamSo1: Kieu1, thamSo2: Kieu2): KieuTraVe { thân hàm }
```

**Return với Anonymous func**
- Lambda cho phép sử dụng `return` để thoát ra khỏi hàm bao ngoài (tức là hàm chứa lambda đó).
- Với anonymous function, `return` chỉ thoát ra chính hàm ẩn danh đó chứ không thoát khỏi hàm bên ngoài.

```kotlin
fun testAnonymous() {
    listOf(1, 2, 3, 4).forEach(fun(it) {
        if (it == 3) return // Chỉ thoát khỏi anonymous function, không thoát testAnonymous
        println(it)
    })
    println("Done") // Vẫn in ra "Done"
}

fun main() {
    testAnonymous()
}
```
còn với lambda:
```kotlin
fun testLambda() {
    listOf(1, 2, 3, 4).forEach {
        if (it == 3) return // Thoát khỏi testLambda luôn
        println(it)
    }
    println("Done") // Không in ra nếu gặp return ở trên
}

fun main() {
    testLambda()
}
```
**Closure:**
`Lambda` và `anonymous function` có thể truy cập và thay đổi biến bên ngoài phạm vi của chúng (closure):

```Kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it // có thể thay đổi sum
}
```

### 3. Callback
**Callback** trong **Kotlin** là một kỹ thuật lập trình cho phép truyền một hàm vào một hàm khác để hàm đó sẽ được gọi lại (callback) ở một thời điểm thích hợp, thường là sau khi hoàn thành một tác vụ nào đó.

**Callback** thường được khai báo như một function type, và truyền vào như một tham số của hàm khác.

```kotlin
// Định nghĩa một hàm nhận callback
fun processData(data: String, callback: (String) -> Unit) {
    // Xử lý dữ liệu, ví dụ: viết hoa
    val result = data.uppercase()
    // Gọi callback và truyền kết quả vào
    callback(result)
}

// Truyền một lambda làm callback
processData("kotlin") { result ->
    println("Kết quả xử lý: $result")
}

// Truyền một hàm làm callback
fun printResult(result: String) {
    println("Kết quả: $result")
}
processData("hello", ::printResult)
```

Hàm `processData` nhận 2 tham số:
- `data`: Dữ liệu để xử lý
- `callback`: Hàm có kiểu `(String) -> Unit`, tức là nhận một String và không trả về gì.

Sau khi xử lý dữ liệu, `processData` gọi `callback(result)` để trả kết quả ra ngoài.
Khi gọi `processData`, ta có thể truyền vào một lambda, anonymous func hoặc một hàm có sẵn.

**Công dụng:**
- Tách biệt logic xử lý: Hàm chính chỉ lo xử lý công việc, còn việc làm gì với kết quả thì giao cho callback.
- Xử lý bất đồng bộ: Đặc biệt hữu ích trong các thao tác bất đồng bộ như đọc file, gọi API, xử lý xong rồi mới gọi callback.
- Tái sử dụng mã lệnh: Có thể truyền các callback khác nhau vào cùng một hàm để có hành vi linh hoạt.