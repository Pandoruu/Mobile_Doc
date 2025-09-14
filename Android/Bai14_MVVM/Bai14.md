- ViewModel
- LiveData
- MVVM architecture
- Đọc thêm
+ Unidirectional Data Flow

# Mô hình MVVM

## 1. ViewModel 
### 1.1 Giới thiệu
**ViewModel** là một thành phần quan trọng trong kiến trúc Android, giúp lưu trữ và quản lý dữ liệu UI theo vòng đời của Activity hoặc Fragment. ViewModel giúp tách rời logic xử lý dữ liệu khỏi UI, đảm bảo dữ liệu không bị mất khi cấu hình thay đổi (ví dụ: xoay màn hình).

ViewModel thuộc thư viện **Android Architecture Components**, thường được sử dụng cùng với **LiveData**, **Room**, **Repository** có tác dụng:

- **Quản lý dữ liệu UI:** ViewModel giữ dữ liệu UI trong bộ nhớ, không bị mất khi Activity/Fragment bị hủy do thay đổi cấu hình.
- **Tách logic khỏi UI:** Giúp code rõ ràng, dễ kiểm thử (testable), dễ bảo trì.
- **Tối ưu hiệu năng:** Tránh việc tải lại dữ liệu không cần thiết.
- **Hỗ trợ Lifecycle:** ViewModel được thiết kế để phối hợp với Lifecycle của Activity/Fragment.

Vòng đời của **ViewModel** gắn liền với scope của nó (có thể là Activity/Fragment).
- ViewModel tồn tại cho tới khi Activity hoặc Fragment bị hủy hoàn toàn (do back hoặc finish).
- Khi cấu hình thay đổi (xoay màn hình), Activity/Fragment bị hủy/tạo lại, ViewModel vẫn giữ nguyên dữ liệu.

![alt text](image.png)

### 1.2 Cách dùng

#### Thêm dependencies

```gradle
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0"
implementation "androidx.lifecycle:lifecycle-livedata-ktx:2.7.0"
```

#### Tạo Class ViewModel

```kotlin
class UserViewModel : ViewModel() {
    val users = MutableLiveData<List<User>>()

    fun loadUsers() {
        // Logic tải dữ liệu từ Repository
    }
}
```

#### Khởi tạo ViewModel trong Activity/Fragment

```kotlin
val viewModel: UserViewModel by viewModels()
```

Hoặc với Fragment:

```kotlin
val viewModel: UserViewModel by activityViewModels()
```

| `by viewModels()`                                                                    | `by activityViewModels()`                                                       |
| ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| Tạo **ViewModel mới** cho chính scope (Activity hoặc Fragment) đang gọi              | Lấy **ViewModel có sẵn** được gắn với Activity cha                              |
| Dữ liệu trong ViewModel **không chia sẻ** ra ngoài scope của nó                      | Dữ liệu được **chia sẻ** giữa Activity và tất cả các Fragment trong Activity đó |
| Mất dữ liệu khi scope bị destroy                                                     | Giữ dữ liệu chừng nào Activity chưa bị destroy                                  |
| Có thể truyền ViewModelStoreOwner khác để thay đổi scope (`requireParentFragment()`) | Mặc định scope là Activity cha                                                  |

#### LiveData kết hợp với ViewModel
**LiveData**: LiveData là một container dữ liệu có thể quan sát được (observable data holder), thuộc `androidx.lifecycle.`.

**Các loại LiveData:**
- **LiveData<T>**: Chỉ đọc, không cập nhật dữ liệu được.
- **MutableLiveData<T>**: Có thể đọc và ghi dữ liệu.
- **MediatorLiveData<T>**: Có thể quan sát nhiều nguồn LiveData khác nhau.
- **SingleLiveEvent**: Custom LiveData dùng cho sự kiện chỉ xảy ra một lần (ví dụ: navigation).

**Tại sao lại dùng LiveData:**
- Kết hợp chặt với ViewModel: Không bị reset khi xoay màn hình.

- Tự động update UI: Khi `.value` thay đổi, tất cả observer đang active sẽ được gọi lại.

- An toàn vòng đời: Không update UI khi Activity/Fragment đang ở trạng thái DESTROYED. tránh crash.

**Ví dụ về ViewModel:**
```kt
class SharedViewModel : ViewModel() {
    private val message = MutableLiveData<String>() //Sử dụng LiveData để lưu dữ liệu
    val message: LiveData<String> get() = message
}
```
**Fragment:**

```kt
class MyFragment : Fragment(R.layout.fragment_my) {
    private val viewModel: SharedViewModel by activityViewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        val button = view.findViewById<Button>(R.id.myButton)
        button.setOnClickListener {
            viewModel.message.value = "Hello từ Fragment" //gửi dữ liệu lên ViewModel
        }
    }
}
```
**Activity:**

```kt
class MainActivity : AppCompatActivity() {
    private val viewModel: SharedViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        //Nhận dữ liệu bằng observe
        viewModel.message.observe(this) { msg ->
            Toast.makeText(this, "Nhận từ Fragment: $msg", Toast.LENGTH_SHORT).show()
        }
    }
}
```
LiveData chỉ gửi dữ liệu khi Observer trong trạng thái active. Khi LifecycleOwner bị hủy, LiveData tự động loại bỏ Observer.

## 2. Mô hình MVVM

**Mô hình MVVM** là mô hình kiến trúc phần mềm bao gồm các thành phần:
- **View**: phần giao diện của ứng dụng để hiển thị dữ liệu và nhận tương tác của người dùng. Có khả năng thực hiện các hành vi và phản hồi lại người dùng thông qua tính năng binding, command.
- **ViewModel**: lớp trung gian giữa View và Model (thay thế cho **Controller**). Chứa các mã lệnh cần thiết để thực hiện data binding, command.
- **Model**: các đối tượng giúp truy xuất và thao tác trên dữ liệu thực sự.
  
![alt text](image-1.png)

Ưu điểm của MVVM:

- Tách biệt rõ ràng giữa giao diện (View) và logic xử lý (Model, ViewModel).
- Dễ dàng kiểm thử logic (Unit Test) mà không phụ thuộc vào UI.
- Hỗ trợ tốt cho việc binding dữ liệu (Data Binding), giúp cập nhật UI tự động khi dữ liệu thay đổi.

MVVM rất phổ biến trong phát triển ứng dụng Android

