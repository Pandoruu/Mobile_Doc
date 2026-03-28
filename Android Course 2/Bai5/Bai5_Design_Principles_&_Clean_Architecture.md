1. SOLID, KISS, DRY
2. Dependency Injection, Manual DI
3. Clean Architecture
4. Modularization

# Buổi 5: Design Principles và Clean Architecture trong Android

## 1. Các nguyên tắc thiết kế phần mềm

Trong quá trình xây dựng ứng dụng Android, ngoài việc làm cho ứng dụng chạy được, điều quan trọng không kém là viết code **dễ đọc, dễ bảo trì và dễ mở rộng**. Các nguyên tắc thiết kế phần mềm ra đời nhằm giúp lập trình viên đạt được điều đó.

---

## 2. SOLID

**SOLID** là tập hợp 5 nguyên tắc thiết kế hướng đối tượng, được đặt ra bởi Robert C. Martin (Uncle Bob). Tên gọi SOLID là viết tắt của 5 nguyên tắc sau:

| Chữ cái | Nguyên tắc                              |
| ------- | --------------------------------------- |
| **S**   | Single Responsibility Principle (SRP)  |
| **O**   | Open/Closed Principle (OCP)            |
| **L**   | Liskov Substitution Principle (LSP)    |
| **I**   | Interface Segregation Principle (ISP)  |
| **D**   | Dependency Inversion Principle (DIP)   |

---

### 2.1. S – Single Responsibility Principle (SRP)

#### Khái niệm

> **Một class chỉ nên có một lý do duy nhất để thay đổi**, tức là mỗi class chỉ nên đảm nhận một trách nhiệm (nhiệm vụ) duy nhất.

Nếu một class đảm nhận quá nhiều việc, khi cần thay đổi logic của một việc thì rất dễ vô tình làm ảnh hưởng tới các việc khác trong cùng class đó.

#### Ví dụ vi phạm SRP

```kotlin
// Sai: UserManager đảm nhận quá nhiều việc:
// vừa quản lý dữ liệu, vừa hiển thị UI, vừa gọi API
class UserManager {
    fun getUser(id: Int): User { /* gọi API */ }
    fun saveUser(user: User) { /* lưu vào database */ }
    fun displayUserOnScreen(user: User) { /* cập nhật TextView */ }
    fun sendWelcomeEmail(user: User) { /* gửi email */ }
}
```

#### Ví dụ áp dụng SRP đúng

```kotlin
// Đúng: Mỗi class đảm nhận một trách nhiệm riêng biệt

// Chỉ lo việc gọi API / xử lý dữ liệu người dùng
class UserRepository {
    fun getUser(id: Int): User { /* gọi API */ }
    fun saveUser(user: User) { /* lưu vào database */ }
}

// Chỉ lo việc cập nhật giao diện
class UserView {
    fun displayUser(user: User) { /* cập nhật TextView */ }
}

// Chỉ lo việc gửi email
class EmailService {
    fun sendWelcomeEmail(user: User) { /* gửi email */ }
}
```

Khi cần thay đổi cách hiển thị UI, ta chỉ sửa `UserView` mà không lo làm hỏng logic gọi API hay gửi email.

---

### 2.2. O – Open/Closed Principle (OCP)

#### Khái niệm

> **Một class nên mở để mở rộng (Open for extension) nhưng đóng với việc sửa đổi (Closed for modification).**

Khi cần thêm tính năng mới, ta nên mở rộng (kế thừa, implement interface) thay vì sửa trực tiếp vào code đang hoạt động ổn định.

#### Ví dụ vi phạm OCP

```kotlin
// Sai: Mỗi lần thêm phương thức thanh toán mới phải vào sửa class này
class PaymentProcessor {
    fun processPayment(method: String, amount: Double) {
        if (method == "credit_card") {
            // xử lý thẻ tín dụng
        } else if (method == "momo") {
            // xử lý MoMo
        }
        // Thêm "zalopay" -> phải sửa lại hàm này
    }
}
```

#### Ví dụ áp dụng OCP đúng

```kotlin
// Đúng: Định nghĩa interface chung
interface PaymentMethod {
    fun pay(amount: Double)
}

// Mỗi phương thức thanh toán là một class riêng biệt
class CreditCardPayment : PaymentMethod {
    override fun pay(amount: Double) {
        println("Thanh toán $amount bằng thẻ tín dụng")
    }
}

class MomoPayment : PaymentMethod {
    override fun pay(amount: Double) {
        println("Thanh toán $amount bằng MoMo")
    }
}

// Thêm ZaloPay không cần sửa code cũ, chỉ cần tạo class mới
class ZaloPayPayment : PaymentMethod {
    override fun pay(amount: Double) {
        println("Thanh toán $amount bằng ZaloPay")
    }
}

class PaymentProcessor {
    fun processPayment(method: PaymentMethod, amount: Double) {
        method.pay(amount)
    }
}
```

---

### 2.3. L – Liskov Substitution Principle (LSP)

#### Khái niệm

> **Các đối tượng của class con có thể thay thế class cha mà không làm thay đổi tính đúng đắn của chương trình.**

Nói đơn giản hơn: nếu class `B` kế thừa class `A`, thì bất cứ nơi nào đang dùng `A`, ta có thể thay bằng `B` mà chương trình vẫn hoạt động bình thường.

#### Ví dụ vi phạm LSP

```kotlin
open class Bird {
    open fun fly() {
        println("Đang bay")
    }
}

// Chim cánh cụt là Bird nhưng không thể bay -> vi phạm LSP
class Penguin : Bird() {
    override fun fly() {
        throw UnsupportedOperationException("Chim cánh cụt không biết bay!")
    }
}

fun makeBirdFly(bird: Bird) {
    bird.fly() // Crash nếu truyền vào Penguin
}
```

#### Ví dụ áp dụng LSP đúng

```kotlin
// Tách biệt khả năng bay ra một interface riêng
interface Flyable {
    fun fly()
}

open class Bird {
    open fun eat() {
        println("Đang ăn")
    }
}

class Sparrow : Bird(), Flyable {
    override fun fly() {
        println("Chim sẻ đang bay")
    }
}

// Chim cánh cụt kế thừa Bird nhưng không implement Flyable
class Penguin : Bird() {
    fun swim() {
        println("Chim cánh cụt đang bơi")
    }
}
```

---

### 2.4. I – Interface Segregation Principle (ISP)

#### Khái niệm

> **Không nên bắt một class phải implement các phương thức mà nó không sử dụng.**

Thay vì tạo một interface lớn chứa nhiều phương thức, hãy tách thành nhiều interface nhỏ hơn, đặc thù hơn.

#### Ví dụ vi phạm ISP

```kotlin
// Sai: Interface quá lớn, bắt tất cả phải implement dù không dùng
interface Worker {
    fun work()
    fun eat()
    fun sleep()
    fun takeVacation()
}

// Robot không cần ăn, ngủ, nghỉ phép
class Robot : Worker {
    override fun work() { println("Robot đang làm việc") }
    override fun eat() { throw UnsupportedOperationException() }
    override fun sleep() { throw UnsupportedOperationException() }
    override fun takeVacation() { throw UnsupportedOperationException() }
}
```

#### Ví dụ áp dụng ISP đúng

```kotlin
// Đúng: Tách thành các interface nhỏ, đặc thù
interface Workable {
    fun work()
}

interface Eatable {
    fun eat()
}

interface Sleepable {
    fun sleep()
}

// Con người implement tất cả những gì cần
class Human : Workable, Eatable, Sleepable {
    override fun work() { println("Người đang làm việc") }
    override fun eat() { println("Người đang ăn") }
    override fun sleep() { println("Người đang ngủ") }
}

// Robot chỉ implement những gì phù hợp
class Robot : Workable {
    override fun work() { println("Robot đang làm việc") }
}
```

---

### 2.5. D – Dependency Inversion Principle (DIP)

#### Khái niệm

> - **Module cấp cao không nên phụ thuộc vào module cấp thấp. Cả hai nên phụ thuộc vào abstraction (interface/abstract class).**
> - **Abstraction không nên phụ thuộc vào chi tiết. Chi tiết nên phụ thuộc vào abstraction.**

DIP thường được áp dụng cùng với **Dependency Injection** (sẽ học ở phần sau).

#### Ví dụ vi phạm DIP

```kotlin
// Sai: UserViewModel phụ thuộc trực tiếp vào UserRepositoryImpl (chi tiết cụ thể)
class UserRepositoryImpl {
    fun getUsers(): List<User> { /* gọi API */ }
}

class UserViewModel {
    // Phụ thuộc vào class cụ thể, không thể thay thế hay test dễ dàng
    private val repository = UserRepositoryImpl()

    fun loadUsers() {
        val users = repository.getUsers()
    }
}
```

#### Ví dụ áp dụng DIP đúng

```kotlin
// Đúng: Cả hai phụ thuộc vào interface

// Abstraction (interface)
interface UserRepository {
    fun getUsers(): List<User>
}

// Chi tiết cụ thể implement interface
class UserRepositoryImpl : UserRepository {
    override fun getUsers(): List<User> { /* gọi API */ }
}

// ViewModel phụ thuộc vào interface, không phải class cụ thể
class UserViewModel(private val repository: UserRepository) : ViewModel() {
    fun loadUsers() {
        val users = repository.getUsers()
    }
}

// Khi test, có thể dễ dàng thay bằng FakeRepository
class FakeUserRepository : UserRepository {
    override fun getUsers(): List<User> = listOf(User(1, "Test User"))
}
```

---

## 3. KISS

### 3.1. Khái niệm

**KISS** là viết tắt của **Keep It Simple, Stupid** – tạm dịch là **"Hãy giữ cho mọi thứ đơn giản"**. Nguyên tắc này nhấn mạnh rằng hầu hết các hệ thống hoạt động tốt nhất khi chúng được thiết kế đơn giản, thay vì phức tạp không cần thiết.

Khi đọc code, bản thân mình (sau vài tháng) hay người khác trong team sẽ cần hiểu được code đó đang làm gì. Code phức tạp dẫn đến khó đọc, dễ sinh lỗi và mất nhiều thời gian sửa chữa.

### 3.2. Ví dụ vi phạm KISS

```kotlin
// Phức tạp không cần thiết
fun isAdult(age: Int): Boolean {
    return when {
        age >= 0 && age < 18 -> false
        age >= 18 && age < 200 -> true
        else -> false
    }
}
```

### 3.3. Ví dụ áp dụng KISS

```kotlin
// Đơn giản, rõ ràng, dễ hiểu
fun isAdult(age: Int): Boolean {
    return age >= 18
}
```

### 3.4. Một số gợi ý để áp dụng KISS

- Đặt tên biến, hàm, class rõ ràng, phản ánh đúng mục đích.
- Tránh viết những điều kiện lồng nhau quá sâu (deeply nested if/else).
- Mỗi hàm chỉ nên làm một việc và không quá dài.
- Không tối ưu hóa sớm khi chưa cần thiết.

---

## 4. DRY

### 4.1. Khái niệm

**DRY** là viết tắt của **Don't Repeat Yourself** – **"Đừng lặp lại chính mình"**. Nguyên tắc này yêu cầu mỗi đoạn logic, kiến thức hay thông tin chỉ nên được biểu diễn **một lần duy nhất** trong toàn bộ hệ thống.

Khi cùng một đoạn code xuất hiện ở nhiều nơi, nếu cần sửa thì phải sửa ở tất cả các nơi đó. Điều này rất dễ dẫn đến bỏ sót và gây ra lỗi.

### 4.2. Ví dụ vi phạm DRY

```kotlin
// Sai: Logic kiểm tra email bị lặp lại ở nhiều chỗ
fun registerUser(email: String, password: String) {
    if (!email.contains("@") || !email.contains(".")) {
        println("Email không hợp lệ")
        return
    }
    // logic đăng ký
}

fun updateEmail(userId: Int, newEmail: String) {
    if (!newEmail.contains("@") || !newEmail.contains(".")) {
        println("Email không hợp lệ")
        return
    }
    // logic cập nhật email
}
```

### 4.3. Ví dụ áp dụng DRY

```kotlin
// Đúng: Tách logic dùng chung vào một hàm riêng
fun isValidEmail(email: String): Boolean {
    return email.contains("@") && email.contains(".")
}

fun registerUser(email: String, password: String) {
    if (!isValidEmail(email)) {
        println("Email không hợp lệ")
        return
    }
    // logic đăng ký
}

fun updateEmail(userId: Int, newEmail: String) {
    if (!isValidEmail(newEmail)) {
        println("Email không hợp lệ")
        return
    }
    // logic cập nhật email
}
```

### 4.4. DRY không đồng nghĩa với "copy ít code nhất có thể"

Cần lưu ý: DRY nói về **logic trùng lặp**, không phải về độ dài code. Đôi khi hai đoạn code trông giống nhau về cú pháp nhưng biểu diễn hai khái niệm khác nhau thì không nên gộp lại, vì điều đó sẽ tạo ra sự phụ thuộc không cần thiết.

---

## 5. Dependency Injection (DI)

### 5.1. Dependency là gì?

Trong lập trình hướng đối tượng, khi một class A **sử dụng** class B để hoàn thành công việc, ta nói A **phụ thuộc** (depend on) vào B. Lúc này B được gọi là **dependency** của A.

```kotlin
class UserViewModel {
    // UserRepository là dependency của UserViewModel
    private val repository = UserRepository()
}
```

### 5.2. Dependency Injection là gì?

**Dependency Injection (DI)** là kỹ thuật mà thay vì để class tự tạo ra các dependency của nó, ta sẽ **cung cấp (inject) các dependency từ bên ngoài vào**.

Có 3 cách inject dependency phổ biến:

| Cách                     | Mô tả                                                          |
| ------------------------ | -------------------------------------------------------------- |
| **Constructor Injection** | Truyền dependency qua constructor (phổ biến nhất, khuyến nghị) |
| **Property Injection**    | Gán dependency vào property sau khi object được tạo            |
| **Method Injection**      | Truyền dependency qua tham số của một hàm cụ thể               |

### 5.3. Tại sao cần Dependency Injection?

Xem xét ví dụ không dùng DI:

```kotlin
class UserRepository {
    private val apiService = RetrofitInstance.apiService // tự tạo dependency
    fun getUsers(): List<User> = apiService.getUsers()
}

class UserViewModel {
    private val repository = UserRepository() // tự tạo dependency
    fun loadUsers() = repository.getUsers()
}
```

**Vấn đề:**
- `UserViewModel` bị gắn chặt với `UserRepository` cụ thể → khó thay thế.
- Khi viết Unit Test, không thể thay `UserRepository` bằng một `FakeRepository`.
- Khi `UserRepository` thay đổi constructor, phải sửa tất cả nơi tạo nó.

Với DI:

```kotlin
// Repository nhận dependency từ ngoài vào
class UserRepository(private val apiService: ApiService) {
    fun getUsers(): List<User> = apiService.getUsers()
}

// ViewModel nhận repository từ ngoài vào
class UserViewModel(private val repository: UserRepository) : ViewModel() {
    fun loadUsers() = repository.getUsers()
}
```

**Lợi ích:**
- **Dễ thay thế:** Có thể truyền vào bất kỳ implementation nào của `ApiService` hay `UserRepository`.
- **Dễ kiểm thử:** Có thể truyền `FakeRepository` vào khi test mà không cần kết nối mạng thật.
- **Giảm sự phụ thuộc cứng (tight coupling):** Các class không tự tạo dependency cho nhau.

---

### 5.4. Manual DI (Dependency Injection thủ công)

**Manual DI** là cách triển khai Dependency Injection mà **không dùng framework** (như Hilt hay Dagger). Lập trình viên tự tạo và quản lý các dependency theo cách thủ công.

#### 5.4.1. Cách tổ chức Manual DI

Thông thường với Manual DI, ta sẽ tạo một class trung tâm gọi là **Container** (hoặc `AppContainer`) chịu trách nhiệm khởi tạo và giữ các dependency dùng chung.

**Bước 1: Định nghĩa các interface và implementation**

```kotlin
// Interface
interface UserRepository {
    fun getUsers(): List<User>
}

// Implementation thực tế (gọi API)
class UserRepositoryImpl(private val apiService: ApiService) : UserRepository {
    override fun getUsers(): List<User> = apiService.getUsers()
}

// Implementation giả cho test
class FakeUserRepository : UserRepository {
    override fun getUsers(): List<User> = listOf(
        User(1, "Alice"),
        User(2, "Bob")
    )
}
```

**Bước 2: Tạo AppContainer để quản lý dependency**

```kotlin
// AppContainer chứa tất cả dependency của ứng dụng
class AppContainer {
    // Tạo ApiService một lần duy nhất (Singleton)
    val apiService: ApiService = RetrofitInstance.create()

    // Tạo Repository, truyền ApiService vào
    val userRepository: UserRepository = UserRepositoryImpl(apiService)
}
```

**Bước 3: Khởi tạo AppContainer trong Application**

```kotlin
class MyApplication : Application() {
    // AppContainer sống suốt vòng đời ứng dụng
    val appContainer = AppContainer()
}
```

**Bước 4: Sử dụng trong Activity/Fragment**

```kotlin
class UserActivity : AppCompatActivity() {
    private lateinit var viewModel: UserViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_user)

        // Lấy dependency từ AppContainer
        val repository = (application as MyApplication).appContainer.userRepository

        // Tạo ViewModel với dependency được inject vào
        viewModel = UserViewModel(repository)
    }
}
```

#### 5.4.2. Ưu và nhược điểm của Manual DI

| Ưu điểm                                         | Nhược điểm                                                       |
| ----------------------------------------------- | ---------------------------------------------------------------- |
| Không cần học framework phức tạp                | Phải tự quản lý vòng đời của dependency                          |
| Kiểm soát hoàn toàn việc tạo object             | Khi ứng dụng lớn, AppContainer trở nên cồng kềnh, khó duy trì   |
| Phù hợp với dự án nhỏ hoặc để học khái niệm DI  | Dễ mắc lỗi do tạo trùng lặp hay quản lý không nhất quán         |
| Không phụ thuộc thư viện bên ngoài              | Tốn công viết code "boilerplate" nhiều hơn so với dùng framework |

> Khi ứng dụng phát triển lớn hơn, nên chuyển sang sử dụng framework DI như **Hilt** (khuyến nghị cho Android) để tự động hóa quá trình này.

---

## 6. Clean Architecture

### 6.1. Giới thiệu

**Clean Architecture** là một kiến trúc phần mềm được đề xuất bởi Robert C. Martin, có mục tiêu xây dựng hệ thống:
- **Độc lập với framework:** Logic nghiệp vụ không phụ thuộc vào Android SDK hay bất kỳ thư viện nào.
- **Dễ kiểm thử:** Logic có thể được test mà không cần thiết bị thật hay giả lập.
- **Độc lập với UI:** Có thể thay đổi giao diện mà không ảnh hưởng đến logic.
- **Độc lập với database:** Có thể đổi từ Room sang SQLite hay API mà không ảnh hưởng đến logic nghiệp vụ.

### 6.2. Nguyên tắc cốt lõi – Dependency Rule (Quy tắc phụ thuộc)

Trong Clean Architecture, code được tổ chức thành các **tầng (layer)** đồng tâm. Quy tắc quan trọng nhất là:

> **Các tầng bên trong không được biết gì về các tầng bên ngoài.** Sự phụ thuộc chỉ được phép đi từ ngoài vào trong.

```
┌─────────────────────────────────────┐
│         Presentation Layer          │  ← ngoài cùng (biết tất cả các tầng trong)
│  (Activity, Fragment, ViewModel)    │
├─────────────────────────────────────┤
│           Domain Layer              │  ← giữa (chỉ biết chính nó)
│      (UseCase, Entity, Repository   │
│            Interface)               │
├─────────────────────────────────────┤
│            Data Layer               │  ← trong cùng (biết Domain Layer)
│  (RepositoryImpl, API, Database)    │
└─────────────────────────────────────┘
          Dependency Rule:
          Ngoài → Trong ✅
          Trong → Ngoài ✗
```

### 6.3. Các tầng trong Clean Architecture

#### 6.3.1. Domain Layer (Tầng nghiệp vụ)

Đây là tầng **trung tâm** và **quan trọng nhất**. Tầng này chứa:

- **Entity:** Các model đại diện cho dữ liệu cốt lõi của ứng dụng, hoàn toàn là Kotlin thuần, không phụ thuộc Android.
- **Repository Interface:** Định nghĩa các hợp đồng (contract) về cách lấy/lưu dữ liệu, không có implementation cụ thể.
- **Use Case (Interactor):** Chứa các logic nghiệp vụ cụ thể của ứng dụng. Mỗi Use Case thực hiện một nhiệm vụ đơn lẻ.

```kotlin
// Entity - Kotlin thuần, không có gì của Android
data class User(
    val id: Int,
    val name: String,
    val email: String
)

// Repository Interface - chỉ là hợp đồng, không có implementation
interface UserRepository {
    suspend fun getUsers(): List<User>
    suspend fun getUserById(id: Int): User?
}

// Use Case - chứa logic nghiệp vụ
class GetUsersUseCase(private val repository: UserRepository) {
    suspend operator fun invoke(): List<User> {
        // Logic nghiệp vụ: ví dụ chỉ lấy user đang hoạt động
        return repository.getUsers().filter { it.isActive }
    }
}
```

#### 6.3.2. Data Layer (Tầng dữ liệu)

Tầng này chịu trách nhiệm **cung cấp dữ liệu** cho ứng dụng. Tầng này chứa:

- **Repository Implementation:** Hiện thực cụ thể của Repository Interface được định nghĩa ở Domain Layer.
- **Data Source:** Các nguồn dữ liệu như Remote (API) hoặc Local (Room Database).
- **Data Model/DTO:** Các model phù hợp với cấu trúc của API hoặc database (có thể khác với Entity ở Domain Layer).

```kotlin
// Data Model cho API response
data class UserDto(
    @SerializedName("id") val id: Int,
    @SerializedName("full_name") val fullName: String,
    @SerializedName("email_address") val email: String
)

// Hàm ánh xạ từ DTO sang Entity
fun UserDto.toDomain(): User = User(
    id = id,
    name = fullName,
    email = email
)

// Repository Implementation - implement interface từ Domain Layer
class UserRepositoryImpl(
    private val apiService: ApiService,
    private val userDao: UserDao
) : UserRepository {

    override suspend fun getUsers(): List<User> {
        return try {
            // Thử lấy từ mạng
            val response = apiService.getUsers()
            val users = response.map { it.toDomain() }
            users
        } catch (e: Exception) {
            // Nếu lỗi mạng, lấy từ local database
            userDao.getAllUsers().map { it.toDomain() }
        }
    }

    override suspend fun getUserById(id: Int): User? {
        return apiService.getUserById(id).toDomain()
    }
}
```

#### 6.3.3. Presentation Layer (Tầng trình bày)

Tầng này chứa toàn bộ **UI và logic hiển thị**. Tầng này chứa:

- **Activity / Fragment:** Hiển thị dữ liệu và nhận tương tác người dùng.
- **ViewModel:** Cầu nối giữa Use Case và UI, giữ trạng thái màn hình.

```kotlin
class UserViewModel(
    private val getUsersUseCase: GetUsersUseCase
) : ViewModel() {

    private val _users = MutableLiveData<List<User>>()
    val users: LiveData<List<User>> = _users

    private val _isLoading = MutableLiveData<Boolean>()
    val isLoading: LiveData<Boolean> = _isLoading

    fun loadUsers() {
        viewModelScope.launch {
            _isLoading.value = true
            try {
                // Gọi Use Case, không gọi trực tiếp Repository
                val result = getUsersUseCase()
                _users.value = result
            } catch (e: Exception) {
                // xử lý lỗi
            } finally {
                _isLoading.value = false
            }
        }
    }
}
```

```kotlin
class UserFragment : Fragment(R.layout.fragment_user) {
    private val viewModel: UserViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        viewModel.users.observe(viewLifecycleOwner) { users ->
            // Cập nhật RecyclerView
        }

        viewModel.isLoading.observe(viewLifecycleOwner) { isLoading ->
            progressBar.isVisible = isLoading
        }

        viewModel.loadUsers()
    }
}
```

### 6.4. Tổng quan luồng dữ liệu trong Clean Architecture

```
User tương tác với UI
        ↓
Fragment/Activity gọi ViewModel
        ↓
ViewModel gọi Use Case
        ↓
Use Case gọi Repository Interface (Domain)
        ↓
Repository Implementation (Data) xử lý
        ↓
Lấy dữ liệu từ API hoặc Database
        ↓
Trả dữ liệu ngược lên (theo chiều ngược lại)
        ↓
ViewModel cập nhật LiveData/StateFlow
        ↓
UI tự động cập nhật
```

### 6.5. Cấu trúc thư mục gợi ý

```
app/
├── data/
│   ├── remote/
│   │   ├── ApiService.kt
│   │   └── dto/
│   │       └── UserDto.kt
│   ├── local/
│   │   ├── AppDatabase.kt
│   │   └── dao/
│   │       └── UserDao.kt
│   └── repository/
│       └── UserRepositoryImpl.kt
│
├── domain/
│   ├── model/
│   │   └── User.kt
│   ├── repository/
│   │   └── UserRepository.kt         ← Interface
│   └── usecase/
│       └── GetUsersUseCase.kt
│
└── presentation/
    └── user/
        ├── UserFragment.kt
        ├── UserViewModel.kt
        └── UserAdapter.kt
```

---

## 7. Modularization

### 7.1. Giới thiệu

**Modularization** (Mô-đun hóa) là quá trình **chia nhỏ ứng dụng thành các module độc lập**, mỗi module chứa code và tài nguyên cho một tính năng hoặc một lớp kỹ thuật nhất định.

Trong Android, mặc định tất cả code nằm trong một module duy nhất là `:app`. Khi ứng dụng phát triển lớn, module này sẽ ngày càng phình to và khó quản lý.

### 7.2. Lợi ích của Modularization

| Lợi ích                           | Giải thích                                                                                        |
| --------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Tăng tốc độ build**             | Android chỉ rebuild các module có thay đổi, thay vì rebuild toàn bộ project                       |
| **Tái sử dụng code**              | Một module có thể được dùng trong nhiều ứng dụng khác nhau                                        |
| **Phân tách rõ ràng**             | Ranh giới giữa các tính năng và tầng kỹ thuật được thể hiện rõ qua cấu trúc module               |
| **Phát triển song song**          | Các team có thể làm việc độc lập trên các module khác nhau mà ít xung đột hơn                    |
| **Hỗ trợ Dynamic Delivery**       | Có thể phân phối các tính năng theo yêu cầu (on-demand) thông qua Play Feature Delivery           |

### 7.3. Các loại module trong Android

#### 7.3.1. Phân loại theo kỹ thuật (Layer Modules)

Cách tổ chức module theo các tầng kỹ thuật, tương tự Clean Architecture:

```
:app                    ← Module chính, điểm vào ứng dụng
:data                   ← Chứa tầng Data (Repository, API, Database)
:domain                 ← Chứa tầng Domain (UseCase, Entity, Interface)
:presentation           ← Chứa tầng Presentation (ViewModel, Fragment)
```

#### 7.3.2. Phân loại theo tính năng (Feature Modules)

Cách tổ chức module theo từng tính năng của ứng dụng, mỗi tính năng là một module độc lập:

```
:app
:feature:login          ← Toàn bộ code của màn hình đăng nhập
:feature:home           ← Toàn bộ code của màn hình trang chủ
:feature:profile        ← Toàn bộ code của màn hình hồ sơ
:feature:settings       ← Toàn bộ code của màn hình cài đặt
:core:network           ← Tầng mạng dùng chung
:core:database          ← Database dùng chung
:core:ui                ← Các component UI dùng chung
```

#### 7.3.3. Kết hợp cả hai (Hybrid)

Trong thực tế, các dự án thường kết hợp cả hai cách trên:

```
:app
:feature:login
:feature:home
:core:domain            ← Domain layer dùng chung
:core:data              ← Data layer dùng chung
:core:ui                ← UI components dùng chung
:core:utils             ← Các hàm tiện ích dùng chung
```

### 7.4. Tạo module trong Android Studio

**Bước 1:** Vào **File → New → New Module...**

**Bước 2:** Chọn loại module phù hợp:
- **Android Library:** Module chứa code Android (có thể dùng Android SDK).
- **Java or Kotlin Library:** Module Kotlin/Java thuần (không phụ thuộc Android SDK, phù hợp cho Domain Layer).

**Bước 3:** Đặt tên module và cấu hình.

### 7.5. Cấu hình phụ thuộc giữa các module

Phụ thuộc giữa các module được khai báo trong file `build.gradle` của module đó.

**Ví dụ:** Module `:app` phụ thuộc vào `:feature:home` và `:core:ui`:

```gradle
// app/build.gradle
dependencies {
    implementation(project(":feature:home"))
    implementation(project(":feature:login"))
    implementation(project(":core:ui"))
}
```

**Ví dụ:** Module `:feature:home` phụ thuộc vào `:core:domain`:

```gradle
// feature/home/build.gradle
dependencies {
    implementation(project(":core:domain"))
    implementation(project(":core:ui"))
}
```

### 7.6. Quy tắc phụ thuộc giữa các module

Tương tự Dependency Rule trong Clean Architecture, cần tuân theo quy tắc:

- Module cấp cao hơn có thể phụ thuộc vào module cấp thấp hơn.
- Module cấp thấp hơn **không được** phụ thuộc ngược lại lên module cấp cao hơn.
- Các `:feature` module **không được** phụ thuộc lẫn nhau trực tiếp.

```
:app  →  :feature:*  →  :core:domain  →  (không phụ thuộc gì)
                     →  :core:data
                     →  :core:ui
```

Nếu hai feature cần giao tiếp với nhau, nên thực hiện thông qua `:app` hoặc một module trung gian (navigation module).

### 7.7. Ví dụ cấu trúc đầy đủ

```
MyApp/
├── app/
│   ├── src/main/
│   │   └── MainActivity.kt     ← Điểm khởi động, setup Navigation
│   └── build.gradle
│
├── core/
│   ├── domain/
│   │   ├── src/main/
│   │   │   ├── model/User.kt
│   │   │   ├── repository/UserRepository.kt
│   │   │   └── usecase/GetUsersUseCase.kt
│   │   └── build.gradle
│   ├── data/
│   │   ├── src/main/
│   │   │   ├── remote/ApiService.kt
│   │   │   └── repository/UserRepositoryImpl.kt
│   │   └── build.gradle
│   └── ui/
│       ├── src/main/
│       │   └── components/LoadingView.kt
│       └── build.gradle
│
└── feature/
    ├── home/
    │   ├── src/main/
    │   │   ├── HomeFragment.kt
    │   │   └── HomeViewModel.kt
    │   └── build.gradle
    └── profile/
        ├── src/main/
        │   ├── ProfileFragment.kt
        │   └── ProfileViewModel.kt
        └── build.gradle
```

---

## 8. Mối quan hệ giữa các khái niệm

Các nguyên tắc và kiến trúc đã học không tách biệt nhau mà có mối liên hệ chặt chẽ:

- **SOLID** là nền tảng tư duy: giúp viết từng class, interface đúng cách.
- **KISS và DRY** giúp code sạch hơn, dễ đọc hơn ở mọi cấp độ.
- **Dependency Injection** là cách hiện thực **Dependency Inversion Principle (D trong SOLID)**.
- **Clean Architecture** áp dụng tất cả các nguyên tắc SOLID vào cấp độ kiến trúc toàn ứng dụng.
- **Modularization** là bước tiếp theo để **tổ chức vật lý** code theo Clean Architecture, giúp dự án lớn dễ quản lý hơn.

```
SOLID / KISS / DRY
        ↓  (nền tảng tư duy)
Dependency Injection
        ↓  (cách kết nối các tầng)
Clean Architecture
        ↓  (cách tổ chức logic)
Modularization
        ↓  (cách tổ chức vật lý)
Dự án Android quy mô lớn, dễ bảo trì
```
