# Buổi 2: Class trong Kotlin
## 1. Các khái niệm liên quan đến class
Để tạo 1 class trong **Kotlin** ta sẽ làm như sau:
```Kotlin
class Class_name {}
```
#### 1.1. Access modifier
Trong **Kotlin** có 4 phạm vi truy cập là `private`, `protected`, `internal` và `public`. Nếu ta không chỉ định phạm vi cụ thể thì `public` sẽ là mặc định. Trong đó:
- `private`: Các thành phần private chỉ được truy cập bên trong class đó.
- `protected`: Các thành phần protected chỉ được truy cập trong class đó và các class kế thừa của nó.
- `internal`: Các thành phần internal chỉ được truy cập trong cùng một module.
- `public`: Các thành phần được khai báo là public có thể được truy cập từ bất kỳ đâu.

```kotlin
open class SinhVien {
    private val msv = "D23001"  // Chỉ truy cập được trong class Person
    protected open val name = "An" // Truy cập được trong Person và các subclass
    internal val age = 20 // Truy cập được trong cùng module
    val address = "Hanoi" // Mặc định là public
}
```
#### 1.2. Constructors
Dùng để khởi tạo đối tượng của 1 class. Trong **Kotlin** có 2 loại contructor là **primary contructor** và **secondary contructor**.
##### a) Primary contructor
**Primary constructor** được khai báo ngay sau tên class, không chứa bất kỳ code thực thi nào ngoài việc khai báo tham số.
```kotlin
class Father (val name: String, val birth: Int) {}
```
Khi tạo đối tượng:
```kotlin
val someone_father = Father("Jack", 1997)
```
##### b) Secondary Constructor
T có thể khai báo thêm một hoặc nhiều **secondary constructor** dùng từ khóa `constructor`. Thường dùng khi ta muốn tạo nhiều cách khởi tạo object hoặc muốn thêm 1 số logic khi khởi tạo.

```kotlin
class Human {
    val name: String
    var age: Int

    contructor(name: String, age: Int) {
        this.name = name
        this.age = age
    }

    contructor(name: String) {
        this.name = name
        this.age = 0
    }
}
```
Khi tạo đối tượng của class ta có thể tùy ý dùng các kiểu contructor khác nhau:
```kotlin
val human1 = Human("Harry", 30)
val human2 = Human("Ron", 30)
```

Ta cũng có thể sử dụng kết hợp 2 loại contructor như sau:
```kotlin
class Human(val name: String, var age: Int) {
    var address: String = ""

    // Contructor này cần phải có đủ các thành phần của primary contructor!
    constructor(name: String, age: Int, address: String) : this(name, age) {
        this.address = address
    }
}
```
**Lưu ý**: Nếu không khai báo constructor nào, Kotlin sẽ tạo mặc định constructor không tham số.
#### 1.3. Init block
Trong Kotlin, **init block** là một khối mã đặc biệt bên trong class, được sử dụng để thực thi các đoạn lệnh khi một đối tượng được tạo ra. Trong 1 class có thể có nhiều init block.

Init block thường dùng để thực hiện các thao tác khởi tạo phức tạp, kiểm tra dữ liệu, ...

```kotlin
class User(val name: String, val age: Int) {
    init {
        println("Khởi tạo User: $name")
    }

    init {
        println("Tuổi: $age")
    }
}
```
Hoặc:
```kotlin
class Account(val username: String, var balance: Int) {
    init {
        require(balance >= 0) { "Số dư không được âm!" }
        println("Đã tạo tài khoản cho $username với số dư: $balance")
    }
}

fun main() {
    val acc = Account("user1", 100)
    // val acc2 = Account("user2", -50) // Lỗi: Số dư không được âm!
}
```
#### 1.4 Companion object
**Companion object** trong Kotlin là một cách để khai báo các thành phần static bên trong class. Để khai báo các thành phần static ta dùng từ khóa `companion object`.

```kotlin
class CharacterConfig {
    companion object {
        val MAX_HP = 500
        val MAX_MANA = 300
        val SPEED = 1
    }
}
```
Các đặc điểm của **companion object**:
- Chỉ có một companion object duy nhất trong mỗi class.
- Các thuộc tính và hàm nằm trong companion object có thể được truy cập thông qua tên class mà không cần tạo đối tượng.
- Companion object có thể được đặt tên hoặc dùng tên mặc định là Companion.
- Companion object có thể implement interface.

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}

fun main() {
    val a = MyClass.create()       // Truy cập qua tên class
    val b = MyClass.Factory.create() // Truy cập qua tên companion
}
```
Khi implement từ 1 interface thì việc đặt tên là điều cần thiết:
```kotlin
interface Maker {
    fun make(): String
}

class Device {
    companion object Creator : Maker {
        override fun make() = "new device"
    }
}

fun main() {
    val maker: Maker = Device.Creator
    println(maker.make()) // Output: new device
}
```
## 2. Special class
#### 2.1. Data class
`Data class` là lớp dùng để lưu dữ liệu. Với mỗi data class trình biên dịch sẽ tự động tạo các hàm có sẵn cho phép đọc, so sánh, sao chép,... Các lớp dữ liệu được đánh dấu bởi từ khóa `data`.

```kotlin
data class User(val name: String, val age: Int)
```

Các hàm có sẵn phổ biến:
- equals() và hashCode()
```kotlin
val u1 = User("An", 20)
val u2 = User("An", 20)
println(u1 == u2) // true (so sánh giá trị, không phải địa chỉ)
```
- toString()
```kotlin
println(u1) // Output: User(name=An, age=20)
```
- copy()
```kotlin
val u3 = u1.copy(age = 22)
println(u3) // Output: User(name=An, age=22)
```
- componentN(): Tự động sinh ra các hàm component1(), component2(),... để hỗ trợ phân rã đối tượng.
```kotlin
val (n, a) = u1
println(n) // An
println(a) // 20
```

#### 2.2. Enum class
Trong **Kotlin**, `enum class` là một loại class đặc biệt dùng để đại diện cho tập hợp các hằng số (constants) cố định, thường dùng cho các giá trị như trạng thái, loại, ...

```kotlin
enum class Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

Một số cách dùng phổ biến:
```kotlin
val days = Day.values() // trả về mảng chứa các phần tử của enum
for (day in days) {
    println(day)
}

println(Day.MONDAY.name)    // "MONDAY"
println(Day.MONDAY.ordinal) // Thứ tự của phần tử đó trong enum (bắt đầu từ 0)
```

Hoặc ta có thể dùng `enum class` để chứa các hàm:
```kotlin
enum class Operation {
    PLUS {
        override fun apply(a: Int, b: Int) = a + b
    },
    MINUS {
        override fun apply(a: Int, b: Int) = a - b
    };

    abstract fun apply(a: Int, b: Int): Int
}

fun main() {
    val op1 = Operation.PLUS
    val op2 = Operation.MINUS

    val result1 = op1.apply(2, 3)
    val result2 = op2.apply(10, 7)

    println(result1) // 5
    println(result2) // 3
}
```

#### 2.3. Sealed class
`Sealed class` trong **Kotlin** là một loại class đặc biệt được thiết kế để giới hạn tập hợp các lớp con mà nó có thể có. Điều này giúp bạn đảm bảo tính an toàn khi kiểm soát các loại đối tượng khác nhau trong chương trình.

```kotlin
sealed class NetworkState {
    object Connected : NetworkState()
    object Disconnected : NetworkState()
    data class Error(val code: Int) : NetworkState()
}

fun handleState(state: NetworkState) {
    when(state) {
        Connected -> println("Đã kết nối")
        Disconnected -> println("Mất kết nối")
        is Error -> println("Lỗi mạng, mã lỗi: ${state.code}")
    }
}
```
**Lưu ý**: Các lớp con của sealed class phải được khai báo cùng file với sealed class đó.