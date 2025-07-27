# Bài 4: Generic, ExtentionFunction và ScopeFunction trong Kotlin
## 1. Generic
### 1.1 Generic là gì?
Generic là một tính năng giúp ta viết code với kiểu dữ liệu tổng quát (không cố định là Int, String, ... mà có thể là bất kỳ kiểu nào), để tái sử dụng cho nhiều kiểu dữ liệu khác nhau. Khi sử dụng, ta mới chỉ định kiểu dữ liệu cụ thể.
```kotlin
class Box<T>(var value: T) //T là một type parameter.
```

Để tạo 1 `instance` của lớp trên ta chỉ cần cung cấp các đối số

```kotlin
val box: Box<Int> = Box<Int>(1)
//hoặc
val intBox: Box(1)          // Kotlin tự động nhận diện nó là Box<Int> dựa vào kiểu dữ liệu truyền vào
val strBox = Box("Hello")   // Box<String>
val doubleBox = Box(3.14)   // Box<Double>
```
Có rất nhiều cách để đặt tên cho kiểu tham số trong Generic nhưng chúng ta nên tuân theo nhưng kiểu đặt tên tiêu chuẩn để sau này nếu có làm việc nhóm thì team sẽ dễ đọc code hơn.

- T - Type (Kiểu dữ liệu bất kỳ thuộc Wrapper class: String, Integer, Long, Float, …)
- E – Element (phần tử – được sử dụng phổ biến trong Collection Framework)
- K – Key (khóa)
- V – Value (giá trị)
- N – Number (kiểu số: Integer, Double, Float, …)
---

### 1.2. Variance (Tính tương hợp kiểu)
Variance là cách kiểm soát mối quan hệ giữa các kiểu generic khi kế thừa hoặc truyền tham số, đặc biệt với các collection và interface.
Nó giúp xác định xem kiểu con có thể thay thế cho kiểu cha (hoặc ngược lại) trong generic hay không.

##### a) Covariance (out) - đồng biến
- Dùng từ khóa `out T` trong generic.
- Áp dụng khi generic chỉ trả về kiểu T (output), không nhận vào kiểu T (input).
- Cho phép `List<Cat>` là con của `List<Animal>` nếu Cat extends từ Animal. 
```
open class Animal
class Cat : Animal()

fun feedAnimals(animals: List<Animal>) {
    // ...
}

val cats: List<Cat> = listOf(Cat(), Cat())
feedAnimals(cats) // Được phép vì List trong Kotlin được khai báo là List<out T>
```
**Tóm lại:** Có thể nói tính đồng biến là ta có thể sử dụng các kiểu con ở nơi yêu cầu kiểu cha.
##### b) Contravariance (in) – Đối biến
- Dùng từ khóa `in` T trong generic.
- Áp dụng khi generic chỉ nhận vào kiểu T (input), không trả về kiểu T (output).

**Ví dụ:**
```kotlin
//Có 2 class Animal và Cat
open class Animal {
    fun eat() = println("Animal eats")
}
class Cat : Animal() {
    fun meow() = println("Meow")
}
// định nghĩa một interface để nhận vào Animal:
interface Consumer<in T> {
    fun consume(item: T)
}
fun main(){
    val animalConsumer: Consumer<Animal> = object : Consumer<Animal> {
        override fun consume(item: Animal) {
            item.eat()
        }
    }
    fun feedCat(consumer: Consumer<Cat>) {
        consumer.consume(Cat())
    }
    feedCat(animalConsumer) // ĐƯỢC PHÉP!
}
```
Giải thích:
- Vì `Consumer<in T> `là đối biến, nên ta có thể truyền `Consumer<Animal> `cho nơi yêu cầu `Consumer<Cat>`.
- Animal là kiểu cha của Cat, nên mọi Cat đều là một Animal => xử lý an toàn.

##### c) Invariant – Bất biến
Khi ta không khai báo từ khóa `out` (đồng biến) hay `in` (đối biến) cho tham số kiểu thì Kotlin mặc định generic là bất biến.

```kotlin
class Box<T>(val value: T)

val catBox: Box<Cat> = Box(Cat())
val animalBox: Box<Animal> = Box(Animal())

// Không thể gán catBox cho animalBox hoặc ngược lại
// animalBox = catBox // Lỗi: Type mismatch
```

Các collection có thể thêm và lấy dữ liệu thường là bất biến, ví dụ: `MutableList<T>`. Điều này đảm bảo an toàn kiểu dữ liệu: Nếu cho phép gán kiểu cha/con, có thể làm hỏng tính nhất quán của collection.

---
### 1.3. Generic class
Khi định nghĩa một class với `generic`, ta dùng ký tự đại diện (T, E, K, V, ...) trong dấu <>.
Class generic có thể chứa thuộc tính, phương thức với kiểu dữ liệu là tham số kiểu.

Ví dụ:
```kotlin
class Pair<A, B>(val first: A, val second: B)
val pair1 = Pair("One", 1) // Pair<String, Int>
val pair2 = Pair(2, "Monday") //Pair<Int, String>
```
---
### 1.4. Generic function
Bên cạnh class, ta có thể khai báo các hàm generic dùng cú pháp <T> trước kiểu trả về.
Điều này cho phép xây dựng 1 hàm hoạt động với mọi kiểu dữ liệu.

Ví dụ:
```kotlin
fun <T> singletonList(item: T): List<T> {
    return listOf(item)
}
```
Khi gọi hàm, kiểu T được xác định dựa trên kiểu đối số truyền vào.

---
### 1.5. Generic Constraints (Ràng buộc chung)
Ta có thể dùng `generic constraints` để ràng buộc các kiểu có thể thay thế cho `T`.
Kiểu ràng buộc phổ biến nhất là **giới hạn trên** - tương ứng với `extends` trong **Java**.

```kotlin
fun <T : Comparable<T>> sort(list: List<T>) { ... }
```
Kiểu được chỉ định sau dấu hai chấm là giới hạn trên , cho biết rằng chỉ có kiểu con của Comparable<T>có thể được thay thế cho T.
**Ví dụ 1:**
```kotlin
sort(listOf(1, 2, 3)) // Đúng vì Int là con của Comparable<Int>
sort(listOf(HashMap<Int, String>())) // Sai vì HashMap<Int, String> không kế thừa Comparable<HashMap<Int, String>>
//Kotlin không so sánh hai HashMap nào "lớn hơn"
```
**Ví dụ 2:**

```kotlin
fun <T : Number> doubleValue(x: T): Double {
    return x.toDouble() * 2
}
```

`T : Number` nghĩa là kiểu T phải là `Number` hoặc con của `Number`

Ta có thể truyền Int, Double, Float, v.v.

Kotlin sẽ từ chối nếu truyền "abc" vì String không phải `Number`.

**Ví dụ 3:**
```kotlin
class Repository<T : Any> {
    fun save(item: T) {
        println("Đã lưu $item")
    }
}
```
`T : Any` nghĩa là không cho phép nullable type (T không thể là `String?`)

Hữu ích khi muốn tránh xử lý null.

**Ví dụ 4: Đa ràng buộc**

```kotlin
fun <T> process(item: T) where T : CharSequence, T : Comparable<T> {
    println(item.length)
    println(item.compareTo(item))
}
```
Trong ví dụ trên ta đặt 2 ràng buộc cho T:
- T phải vừa là `CharSequence` (có .length)

- T là `Comparable<T>` (có `.compareTo()`)

---
### 1.6. Khi nào thì sử dụng generic?
Ta nên dùng Generic nếu code yêu cầu:
- Cần tái sử dụng code cho nhiều kiểu dữ liệu khác nhau:

- Viết một hàm, class hoặc interface chỉ thay đổi kiểu dữ liệu mà vẫn giữ nguyên logic. (Ví dụ: danh sách, stack, map, repository, hàm xử lý dữ liệu…)
- Muốn đảm bảo an toàn kiểu dữ liệu lúc biên dịch: Giúp phát hiện lỗi sai kiểu khi compile, tránh lỗi runtime do truyền sai kiểu.
- Làm việc với các cấu trúc dữ liệu tổng quát: Các collection như `List`, `Set`, `Map`,… đều sử dụng generic.
Khi cần xử lý hoặc trả về nhiều kiểu dữ liệu trong cùng một logic.

---
## 2. Extention Function
**Extension Function** (Hàm mở rộng) là một tính năng cho phép thêm hàm mới vào một class có sẵn (kể cả class của hệ thống hoặc thư viện ngoài) mà không cần thay đổi mã nguồn của class đó

Cú pháp:
```kotlin
fun TênClass.tênHàm(tham số): KiểuTrảVề {
    // thân hàm
}
```

Ví dụ:

```kotlin
fun Int.isEven(): Boolean {
    return this % 2 == 0 //this đại diện cho đối tượng kiểu Int đang gọi hàm
}

val x = 10
println(x.isEven()) // true
```

```kotlin
val s = "Hello"
fun String.reverse(): String {
    return this.reversed().uppercase()
}
fun main(){
    println(s.reverse()) // "OLLEH"
}
```
```kotlin
class CheckNumber {
    fun greaterthanTen(x: Int): Boolean {
        return x > 10
    }
}
fun main() {
    var x = 13
    fun CheckNumber.lessthanTen(x: Int): Boolean{ // hàm lesthanTen được bổ sung ở main chứ không phải class CheckNumber
        return x<10
    }
    var checkNumber = CheckNumber()
    println(checkNumber.greaterthanTen(x))
    println(checkNumber.lessthanTen(x))
}
```
**Lưu ý:**
- Extension function không thực sự thêm vào class gốc, mà chỉ là “hàm tiện ích” gọi được như thành viên.
- Nếu class đã có hàm cùng tên, extension function không ghi đè được mà sẽ bị ưu tiên sau hàm thành viên.

---
## 3. Scope Function
**Scope function** là các hàm đặc biệt trong Kotlin giúp ta thao tác với một đối tượng trong một phạm vi (scope) tạm thời.

Có 5 loại **scope function**: `let`, `run`, `with`, `apply` và `also`.

Về cơ bản, tất cả các hàm này đều thực hiện cùng một hành động: **thực thi một khối mã trên một đối tượng**. Điểm khác biệt là cách đối tượng này xuất hiện bên trong khối mã và kết quả của toàn bộ biểu thức.

| Tên   | Tham số đại diện | Giá trị trả về | Thường dùng                             |
| ----- | ---------------- | -------------- | --------------------------------------- |
| let   | it               | Lambda result  | Xử lý nếu không null, biến đổi dữ liệu  |
| run   | this             | Lambda result  | Khởi tạo, tính toán, kết hợp nhiều lệnh |
| with  | this             | Lambda result  | Làm việc với object có sẵn              |
| apply | this             | Object         | Thiết lập thuộc tính, cấu hình object   |
| also  | it               | Object         | Thực hiện hành động phụ, logging, debug |

Trong đó:
- `this`:Dùng trong truy cập thuộc tính/phương thức như trong class.
- `it`: Dùng trong truy cập object qua argument lambda (thường dùng cho xử lý nhanh, logging, kiểm tra điều kiện).
#### 3.1 let
Biến đổi dữ liệu:
```kotlin
Person("Alice", 20, "Amsterdam").let {
    println(it)
    it.moveTo("London")
    it.incrementAge()
    println(it)
}
```
Nếu **không** dùng `let` ta phải gọi lại đối tượng với mỗi thực thi
```kotlin
val alice = Person("Alice", 20, "Amsterdam")
println(alice)
alice.moveTo("London")
alice.incrementAge()
println(alice)
```
Dùng `let` để xử lí biến nullable:
```kotlin
val name: String? = "Kotlin"
name?.let {
    println(it.length) // In ra độ dài nếu name khác null
}
```

#### 3.2 with
Trả về kết quả lambda.
```kotlin
val numbers = mutableListOf("one", "two", "three")
with(numbers) {
    println("'with' is called with argument $this")
    println("It contains $size elements")
}
```
Tính toán với object:

```kotlin
val firstAndLast = with(numbers) {
    "The first element is ${first()}, the last element is ${last()}"
}
println(firstAndLast)
```
#### 3.3 run

Dùng để khởi tạo và trả về kết quả.

```kotlin
val message = StringBuilder().run {
    append("Hello, ")
    append("Kotlin!")
    toString() // Giá trị trả về của run là kết quả cuối cùng của lambda
}

println(message)  // In ra: Hello, Kotlin!
```

Non-extension run:

```kotlin
val hexNumberRegex = run {
    val digits = "0-9"
    val hexDigits = "A-Fa-f"
    val sign = "+-"
    Regex("[$sign]?[$digits$hexDigits]+")
}
for (match in hexNumberRegex.findAll("+123 -FFFF !%*& 88 XYZ")) {
    println(match.value)
}
```

---

#### 3.4 apply
Dùng để thiết lập thuộc tính khi khởi tạo object.

```kotlin
val adam = Person("Adam").apply {
    age = 32
    city = "London"
}
println(adam)
```

---

#### 3.5 also
Dùng cho các hành động phụ, logging, debug.

```kotlin
val numbers = mutableListOf("one", "two", "three")
numbers
    .also { println("The list elements before adding new one: $it") }
    .add("four")
```
```kotlin
val list = mutableListOf<Int>()
list
    .also { println("Khởi tạo: $it") }
    .add(1)
    .also { println("Sau khi thêm 1: $list") }
    .add(2)
    .also { println("Sau khi thêm 2: $list") }
```
Đối tượng trong also là it, và giá trị trả về vẫn là object gốc, thuận tiện cho việc chain các thao tác.

---
#### 3.6 takeIf và takeUnless
Bên cạnh các scope function, thư viện chuẩn của Kotlin còn cung cấp các hàm `takeIf` và `takeUnless`. Hai hàm này cho phép ta nhúng các kiểm tra trạng thái của một đối tượng vào chuỗi gọi hàm (call chain), giúp code linh hoạt và gọn gàng hơn.

##### a) takeIf
Khi gọi trên một đối tượng kèm với một điều kiện (predicate), `takeIf` sẽ trả về chính đối tượng đó nếu nó **thỏa mãn điều kiện**. Nếu không thỏa mãn, nó trả về `null`.  
Vì vậy, `takeIf` có thể xem như một hàm lọc (filter) cho một đối tượng duy nhất.

**Ví dụ:**
```kotlin
val number = Random.nextInt(100)

val evenOrNull = number.takeIf { it % 2 == 0 }
val oddOrNull = number.takeUnless { it % 2 == 0 }
println("even: $evenOrNull, odd: $oddOrNull")
```
Ở đây, nếu `number` là số chẵn, `evenOrNull` sẽ nhận giá trị `number`, ngược lại là `null`.

##### b) takeUnless
`takeUnless` có logic ngược lại với `takeIf`:  
Khi gọi trên một đối tượng với một điều kiện, nó trả về `null` nếu đối tượng **thỏa mãn điều kiện**, ngược lại sẽ trả về chính đối tượng.

##### Giá trị trả về:
Khi sử dụng `takeIf` hoặc `takeUnless`, cần nhớ rằng giá trị trả về có thể là `null` nếu điều kiện không thỏa mãn. Vì vậy, khi chain các hàm phía sau, phải dùng kiểm tra `null` hoặc toán tử safe call (`?.`).

**Ví dụ:**
```kotlin
val str = "Hello"
val caps = str.takeIf { it.isNotEmpty() }?.uppercase()
//val caps = str.takeIf { it.isNotEmpty() }.uppercase() // lỗi biên dịch
println(caps)
```
Ở ví dụ trên, nếu chuỗi không rỗng, kết quả sẽ là chuỗi viết hoa; ngược lại kết quả là `null`.

##### Kết hợp với Scope Function
`takeIf` và `takeUnless` thường được dùng kết hợp với các scope function như `let` để thực thi một đoạn code khi đối tượng thỏa mãn điều kiện.
**Ví dụ:**
```kotlin
fun displaySubstringPosition(input: String, sub: String) {
    input.indexOf(sub).takeIf { it >= 0 }?.let {
        println("Chuỗi con $sub được tìm thấy trong $input.")
        println("Vị trí bắt đầu của nó là $it.")
    }
}
displaySubstringPosition("010000011", "11")
displaySubstringPosition("010000011", "12")
```
- Nếu `indexOf(sub)` trả về vị trí lớn hơn hoặc bằng 0 (tức là tìm thấy chuỗi con), thì khối `let` sẽ được thực thi.
- Nếu không tìm thấy (giá trị trả về -1), khối `let` sẽ không thực thi.
