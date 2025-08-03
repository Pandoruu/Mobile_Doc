# Buổi 1: Tìm hiểu về ngôn ngữ Kotlin
## 1. Tiểu sử
**Kotlin** là một ngôn ngữ lập trình được tài trợ và phát triển bởi **JetBrains** và đặt tên theo hòn đảo Kotlin gần St.PetesBurg. Là ngôn ngữ lập trình khá mới khi lần đầu giới thiệu vào giữa năm 2011. Kotlin được tạo ra để khắc phục các điểm yếu của ngôn ngữ **Scala**, một trong những điểm nổi bật nhất là khả năng biên dịch nhanh như **Java**.

**Kotlin** được thiết kế để tương thích hoàn toàn với **Java** và có thể chạy trên **JVM**.

Một số điểm nổi bật của **Kotlin** gồm:
- Tương thích 100% với **Java**: Có thể dùng Kotlin cùng với Java trong cùng một dự án.
- Cú pháp ngắn gọn hơn **Java**: Viết ít mã hơn, dễ bảo trì hơn.
- **Null safety**: Giảm thiểu lỗi NullPointerException.
- Hỗ trợ **coroutines**: Dễ dàng lập trình bất đồng bộ, song song.*
- Dễ học với người biết **Java**: Cú pháp quen thuộc và thân thiện.
- Được **Google** chọn làm ngôn ngữ chính thức cho **Android** từ năm 2017.

**Kotlin** là một ngôn ngữ lập trình hiện đại, an toàn, và mạnh mẽ, đang dần trở thành lựa chọn hàng đầu không chỉ trong **Android** mà còn trong phát triển phần mềm đa nền tảng. Với sự hậu thuẫn mạnh mẽ từ cộng đồng và **Google**, Kotlin và team Mobile sẽ có một tương lai rất sáng sủa.

Trong khuôn khổ của bài ngày hôm nay. Chúng ta sẽ mặc định người đọc có các kiến thức cơ bản về Java. Vì thế nên bài sẽ tập trung đến những điểm khác biệt của Kotlin với ngôn ngữ này.
## 1. Biến và kiểu dữ liệu
### 1.1 Biến:
Kotlin sử dụng hai từ khoá để khai báo các biến: `val` và `var`.

- Sử dụng `val` cho biến có giá trị không bao giờ thay đổi (immutable).
- Sử dụng `var` cho biến có giá trị có thể thay đổi (mutable).

Kotlin có thể suy luận kiểu dữ liệu dựa trên loại giá trị được chỉ định

Khai báo biến trong **Kotlin**:

```kotlin
//immutable
val name = "tuanan" // hoặc val name: String = "tuanan"
val age = 20 // hoặc val age: Int = 20

//mutable
var gender = "im straight"
gender = "im gay now" // có thể thay đổi
```

Ở các ngôn ngữ khác như Java bạn có thể khai báo một biến là `null` tức biến rỗng. Nhưng với Kotlin thì theo mặc định không cho phép biến chứa giá trị rỗng
```kotlin
val name = null // Lỗi biên dịch
```
Đây chính là cơ chế an toàn của Kotlin để tránh lỗi `NullPointerExeption`. Chúng ta sẽ tìm hiểu vấn đề này ở phần sau của bài.
### 1.2 Kiểu dữ liệu
##### 1.2.1 Kiểu dữ liệu nguyên thủy
Kotlin tự động boxing/unboxing primitive thành đối tượng nếu cần – không cần phân biệt như Java
| Kiểu    | Kích thước | Ví dụ                  | Ghi chú                                                            |
| ------- | ---------- | ---------------------- | ------------------------------------------------------------------ |
| Byte    | 8-bit      | val x: Byte = 127      | Số nguyên nhỏ                                                      |
| Short   | 16-bit     | val x: Short = 10000   |
| Int     | 32-bit     | val x: Int = 123456    |
| Long    | 64-bit     | val x: Long = 123L     | Có hậu tố L                                                        |
| Float   | 32-bit     | val pi: Float = 3.14f  | Có hậu tố f                                                        |
| Double  | 64-bit     | val d: Double = 3.1415 | Mặc định cho số thực                                               |
| Char    | 16-bit     | val c: Char = 'A'      | Không như Java, ta không thể dùng giá trị ASCII để biểu diễn ký tự |
| Boolean | 1-bit      | val b: Boolean = true  | true / false                                                       |

##### 1.2.2 Kiểu String

Khai báo String:
```Kotlin
var name: String = "An"
```

Nếu muốn khai báo biến String và không có giá trị khởi tạo thì ta cần chỉ chỉ rõ kiểu dữ liệu của biến.
```kotlin
var name: String // khai báo đúng
name = "An"

var age //khai báo sai, sẽ báo lỗi
age = "20"
```

Kotlin hỗ trợ việc tạo 1 String có chứa nhiều dòng hay còn gọi là **Raw String**
```kotlin
val text = """
    Đây là một chuỗi
    nhiều dòng
    trong Kotlin
"""
```
 Mỗi dòng sẽ giữ nguyên khoảng trắng đầu dòng, trừ khi bạn xử lý bằng trimIndent() hoặc trimMargin()

 ```kotlin
 val song = """
    Never gonna give you up, never gonna let you down
    Never gonna run around and desert you
    Never gonna make you cry, never gonna say goodbye
    Never gonna tell a lie and hurt you
""".trimIndent()

val poem = """
    |Trăng lên đầu núi
    |Trăng xuống lòng khe
    |Trăng lồng cổ thụ
    |Trăng tỏ hoa che
""".trimMargin()

val msg = """
> Dòng 1
> Dòng 2
""".trimMargin("> ")

print("$song\n$poem\n$msg")
```
##### 1.2.3 Kiểu dữ liệu đặc biệt
**Any**
Kiểu cha của các kiểu không `null`
```Kotlin
val x: Any = "Kotlin" // Có thể gán bất kỳ kiểu nào
```
**Unit**
Tương đương với void trong Java. Được dùng cho hàm không trả giá trị.
```kotlin
fun sayHello(): Unit {
    println("Hello!")
}
```
**Nothing**
Mang nghĩa không bao giờ trả về giá trị (vd: hàm throw Exception)
```kotlin
fun fail(): Nothing {
    throw Exception("Lỗi xảy ra!")
}
```
##### 1.2.4 Ép kiểu
Kotlin tự động suy luận kiểu trong hầu hết trường hợp.

Dùng `is`, `as` để kiểm tra/ép kiểu:
```kotlin
fun printLength(x: Any) {
    if (x is String) {
        println(x.length) // Kotlin tự hiểu x là String
    } else {
        println("Không phải chuỗi")
    }
}
fun castToString(x: Any) {
    return x as? String
}
```
## 2. Câu lệnh rẽ nhánh
Câu lệnh rẽ nhánh `if` `else` trong **Kotlin** có cú pháp và cấu trúc giống với **Java**.

Nhưng lệnh `if` ở Kotlin có thể trả về giá trị và có thể gán trực tiếp cho biến

```kotlin
val price_in_PhungKhoang = 50000
val status = if(price_in_PhungKhoang < 100000) "lợn ốm" else "maybe lợn sạch"
println(status)
```

Câu lệnh rẽ nhiều nhánh ở Kotlin là `when`:
```kotlin
val day = 4

val result = when (day) {
  1 -> "Monday"
  2 -> "Tuesday"
  3 -> "Wednesday"
  4 -> "Thursday"
  5 -> "Friday"
  6 -> "Saturday"
  7 -> "Sunday"
  else -> "Invalid day."
}
println(result)
```
Tương tự với `if` thì `when` cũng có thể gán trực tiếp cho biến
## 3. Câu lệnh lặp
#### 3.1 While, Do While
Cấu trúc vào cú pháp và công dụng giống hệt với Java
#### 3.2 For
Vòng lặp `for` ở Kotlin khá giống với Python khi dùng `in` để duyệt
```kotlin
for(i in 1..10){ //duyệt trong khoảng [1,10]
    println(i)
}

for(i in 1 until 10){ //duyệt trong khoảng [1,10)
    println(i)
}

for(i in 1..10 step 2){ //duyệt với bước nhảy là 2
    println(i)
}

for(i in 10 downTo 1){ //duyệt giảm dần từ 10->1
    println(i)
}

val nums = arrayOf(1, 5, 10, 15, 20)
for (x in nums){ //duyệt qua 1 mảng
  println(x)
}
```
#### 3.3 Lable
Dùng kết hợp với `break` `continue` và `return` để có thể thoát vòng lặp tại các vị trí được chỉ định (đánh dấu trước bởi labels)

```kotlin
outerloop@ for (i in 1..5) {
    for (j in 1..5) {
        if (j == 3) break@outerloop
        println("i = $i, j = $j")
    }
}
```
Hoặc được dùng như sau:
```kotlin
fun test() {
    listOf(1, 2, 3).forEach {
        if (it == 2) return@forEach // khi gặp 2 thì không in ra mà quay lại forEach
        println(it)
    }
    println("Done")
}
```
| Câu lệnh         | Chức năng                                        |
| ---------------- | ------------------------------------------------ |
| `break@label`    | Thoát khỏi vòng lặp được gán nhãn                |
| `continue@label` | Bỏ qua lần lặp hiện tại và tiếp tục vòng có nhãn |
| `return@label`   | Trả về từ lambda/hàm được gán nhãn cụ thể        |
## 4. Collections
Ta sẽ tìm hiểu một vài **collection** hay được sử dụng trong Kotlin: `Array`, `List`, `Set` và `Map`

**Collections** trong **Kotlin** cũng chia thành immutable và mutable
```kotlin
//immutable
val list = listOf("A", "B", "C")
val set = setOf(1, 2, 3)
val map = mapOf("name" to "An", "birth" to 2005)

//mutable
val list = mutableListOf("A", "B")
list.add("C")

val set = mutableSetOf(1, 2)
set.add(3)

val map = mutableMapOf("a" to 1)
map["b"] = 2
```
#### 4.1 List
**List** là danh sách có thứ tự, cho phép trùng lặp. Có thể truy cập phần tử qua chỉ số


```kotlin
//Khai báo
var list = listOf(1, 2, 3) // Immutable
var mList = mutableListOf(1, 2, 3) // Mutable

//Duyệt list
for(i in mList) { //duyệt bằng for
    println(i)
}
for(i in mList.indices) { //duyệt bằng chỉ số
    println(mList[i])
}

mList.forEach{print(it)} //duyệt bằng forEach
```

Để thay đổi 1 mutableList ta dùng:
```kotlin
mList.add(4) //thêm giá trị vào cuối
mList[0] = 10 //cập nhật giá trị tại chỉ số chỉ định
mList.removeAt(1) //xóa 1 phần tử tại chỉ số chỉ định
println(mList) // In ra: [10, 3, 4]
```
Một số hàm hữu dụng:
```kotlin
val nums = listOf(1, 2, 3, 4, 5)

val doubled = nums.map { it * 2 }
val even = nums.filter { it % 2 == 0 }
val sum = nums.sum()

println(doubled) // [2, 4, 6, 8, 10]
println(even)    // [2, 4]
println(sum)     // 15
```
Ta có thể chuyển đổi giữa immutableList và mutableList bằng cách sau:
```kotlin
var list = listOf("A", "B")
list = list.toMutableList() //list đã trở thành mutable list và có thể thay đổi
list.add("C") 
print(list) //[A, B, C]
```
#### 4.2 Array
Cũng là danh sách để chứa các phần tử nhưng giới hạn và chức năng của **Array** khác với **List**

| Tiêu chí                     | `List`                                                | `Array`                     |
| ---------------------------- | ----------------------------------------------------- | --------------------------- |
| Là gì?                       | Interface (danh sách trừu tượng)                      | Kiểu dữ liệu cụ thể cố định |
| Kích thước                   | Có thể cố định hoặc thay đổi (nếu dùng `MutableList`) | Cố định sau khi tạo         |
| Có thể thay đổi?             | `List` là immutable<br> `MutableList` thì có          | Có thể thay đổi phần tử     |
| Truy cập phần tử             | Có thể dùng `get(index)` hoặc `[index]`               | Dùng `[index]`              |
| Hàm hỗ trợ cao cấp           | Rất nhiều (`filter`, `map`, `forEach`, ...)           | Ít hơn so với `List`        |
| Cách tạo                     | `listOf(1, 2, 3)`                                     | `arrayOf(1, 2, 3)`          |
| Có dùng được như Java array? | Không (List là Collection Kotlin)                     | Có – tương thích hoàn toàn  |

Các mảng chuyên biệt ở Kotlin
| Kiểu dữ liệu | Mảng thường      | Mảng chuyên biệt |
| ------------ | ---------------- | ---------------- |
| `Int`        | `Array<Int>`     | `IntArray`       |
| `Double`     | `Array<Double>`  | `DoubleArray`    |
| `Boolean`    | `Array<Boolean>` | `BooleanArray`   |
| `Long`       | `Array<Long>`    | `LongArray`      |
| `Char`       | `Array<Char>`    | `CharArray`      |
| `Float`      | `Array<Float>`   | `FloatArray`     |
| `Byte`       | `Array<Byte>`    | `ByteArray`      |
| `Short`      | `Array<Short>`   | `ShortArray`     |

Tại sao dùng mảng chuyên biệt? Khi bạn dùng Array<Int>, Kotlin thật ra lưu trữ như mảng các đối tượng Integer, không phải giá trị nguyên thủy int ⇒ điều này có thể gây chậm và tốn bộ nhớ.

#### 4.3 Set
Trong Kotlin, **Set** là một collection không chứa phần tử trùng lặp và không đảm bảo thứ tự. Giống như List ta cùng có Set và mutableSet

Khai báo immutableSet
```kotlin
val colors = setOf("Red", "Green", "Blue", "Red")
println(colors) // [Red, Green, Blue]
```

Khai báo mutableSet
```kotlin
val numbers = mutableSetOf(1, 2, 3)
numbers.add(4)
numbers.remove(2)
println(numbers) // [1, 3, 4]
numbers.clear() //xóa toàn bộ phần tử
```
Duyệt qua Set

```kotlin
val animals = setOf("Dog", "Cat", "Elephant")

for (animal in animals) {
    println(animal)
}
```
Kiểm tra phần tử trong Set
```kotlin
val items = setOf("A", "B", "C")

println("B" in items)    // true
println("D" in items)    // false
```

Các hàm hàm xử lí Set phổ biến

```kotlin
val a = setOf(1, 2, 3)
val b = setOf(3, 4, 5)

val union = a union b        // [1, 2, 3, 4, 5]
val intersect = a intersect b // [3]
val subtract = a subtract b   // [1, 2]

println(union)
println(intersect)
println(subtract)
```
Ta có thể chuyển đổi giữa Set và List như sau
```kotlin
val list = listOf("A", "B", "A", "C")
val uniqueSet = list.toSet()  // ["A", "B", "C"]
val backToList = uniqueSet.toList()
```
Một số loại Set khác
```kotlin
fun main() {
    val hashSet = hashSetOf("Banana", "Apple", "Orange", "Banana")
    val linkedHashSet = linkedSetOf("Banana", "Apple", "Orange", "Banana")

    println("HashSet:")
    println(hashSet)  // Thứ tự không đảm bảo

    println("\nLinkedHashSet:")
    println(linkedHashSet)  // Giữ đúng thứ tự thêm vào
}
```
| Đặc điểm               | `HashSet`                         | `LinkedHashSet`                     |
| ---------------------- | --------------------------------- | ----------------------------------- |
| **Loại bỏ trùng lặp**  | Có                                | Có                                  |
| **Thứ tự phần tử**     | **Không đảm bảo thứ tự**          | **Giữ thứ tự thêm vào**             |
| **Tốc độ tìm kiếm**    | Rất nhanh (O(1) trung bình)       | Nhanh (gần như tương đương HashSet) |
| **Ứng dụng điển hình** | Khi chỉ quan tâm giá trị duy nhất | Khi cần duy nhất + thứ tự xuất hiện |

#### 4.4 Map
**Map** là một danh sách chưa các cặp giá trị ánh xạ key - value

Tương tự thì Map cũng chia thành Map (immutable) và mutableMap

Khai báo Map:
```kotlin
val studentScores = mapOf(
    "Alice" to 85,
    "Bob" to 90,
    "Charlie" to 78
)

println(studentScores["Bob"]) // 90
```

Khai báo mutableMap
```kotlin
val ages = mutableMapOf(
    "John" to 30,
    "Anna" to 25
)

ages["Anna"] = 26           // cập nhật value
ages["Mike"] = 22           // thêm key mới
ages.remove("John")         // xoá key

println(ages) // {Anna=26, Mike=22}
```

Duyệt qua Map
```kotlin
val capitals = mapOf(
    "Vietnam" to "Hanoi",
    "USA" to "Washington",
    "France" to "Paris"
)

// Cách 1: Duyệt cặp key-value
for ((country, capital) in capitals) {
    println("$country → $capital")
}

// Cách 2: Duyệt riêng key hoặc value
for (key in capitals.keys) println("Quốc gia: $key")
for (value in capitals.values) println("Thủ đô: $value")
```
Một số hàm thường dùng

```kotlin
val map = mutableMapOf("A" to 1, "B" to 2)

// containsKey / containsValue
println(map.containsKey("A")) // true
println(map.containsValue(3)) // false

// getOrDefault
val score = map.getOrDefault("C", 0)  // trả về 0 nếu "C" không có
println(score)
```
## 5. Null safety
Như đã nói ở phần khai báo biến. Theo mặc định thì ta không được khai báo 1 biến là `null`. Khi muốn khai báo 1 biến là `null` thì ta phải chuyển nó thành biến `nullable` bằng cách đặt dấu `?`

```kotlin
val name: String? = "An"
name = null // được chuyển thành null
val age: Int = 20
age = null //bị báo lỗi
```

Khi gọi đến các biến `nullable` ta cần dùng `safe call`

```kotlin
val len = name?.length
// nếu name == null thì biến len = null => không gây lỗi
```

Nếu ta muốn `len` nhận 1 giá trị nào đó nếu `name` là `null` thì ta dùng **toán tử Elvis**:

```kotlin
val len = name?.length ?: 0
// len sẽ nhận giá trị 0 nếu name == null
```

Ta có thể ép 1 biến `nullable` về biến mặc định bằng toán tử `!!`

```kotlin
val len: Int
if(name != null){
    len = name!!.length
}
//Khi ép về non-null ta phải kiểm tra xem biến có đang null hay không
//Nếu biến được ép đang nhận gía trị null sẽ bị lỗi NullPointerException
```