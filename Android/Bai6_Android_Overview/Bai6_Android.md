# Android Overview and Basic Layout

---

## I. ANDROID OVERVIEW

### 1. Giới thiệu về hệ điều hành Android và IDE Android Studio

#### 1.1. Android là gì?

Android là hệ điều hành mã nguồn mở, dựa trên nhân Linux, chủ yếu dành cho các thiết bị di động như điện thoại thông minh và máy tính bảng. Android được phát triển bởi Android Inc., sau đó được Google mua lại vào năm 2005. Từ đó đến nay, Android đã trở thành nền tảng di động phổ biến nhất thế giới với hàng tỷ thiết bị được kích hoạt.

**Điểm nổi bật của Android:**
- **Mã nguồn mở:** Bất kỳ ai cũng có thể tải về, chỉnh sửa và phân phối lại mã nguồn Android.
- **Đa dạng thiết bị:** Android được sử dụng trên nhiều loại thiết bị: điện thoại, tablet, TV, đồng hồ, ô tô, thiết bị IoT, v.v.
- **Kho ứng dụng phong phú:** Google Play Store cung cấp hàng triệu ứng dụng cho người dùng.

#### 1.2. Lịch sử phát triển Android

- **2008:** Phiên bản Android 1.0 ra mắt cùng chiếc điện thoại HTC Dream.
- **2009-2010:** Các phiên bản Cupcake, Donut, Eclair, Froyo, Gingerbread bổ sung nhiều tính năng mới.
- **2011-2013:** Honeycomb, Ice Cream Sandwich, Jelly Bean tập trung vào giao diện người dùng và hiệu năng.
- **2014-nay:** Các phiên bản KitKat, Lollipop, Marshmallow, Nougat, Oreo, Pie, Q, R, S, Tiramisu... liên tục cải tiến về bảo mật, hiệu năng, giao diện, AI.

#### 1.3. Android Studio

**Android Studio** là IDE chính thức do Google phát triển dành cho lập trình viên Android, dựa trên IntelliJ IDEA.

- **Tính năng nổi bật:**
  - Code Editor thông minh, hỗ trợ Java, Kotlin, C++.
  - Visual Layout Editor kéo-thả giao diện.
  - Trình giả lập mạnh mẽ (Android Emulator).
  - Công cụ debug, profiler, test, build.
  - Hỗ trợ quản lý dependencies và version thông qua Gradle.
  - Tích hợp Google Cloud Platform, Firebase.

**Quy trình phát triển ứng dụng Android:**
1. Ý tưởng → Phân tích yêu cầu → Thiết kế UI/UX → Lập trình → Kiểm thử → Đóng gói APK/AAB → Phát hành trên Play Store.

---

### 2. Tìm hiểu về Android Project Structure trong Android Studio

Khi tạo một project mới, Android Studio sẽ sinh ra cấu trúc thư mục như sau:

```
MyApplication/
│
├── .idea/                  # Cấu hình IDE
├── app/
│   ├── build/              # File build tạm
│   ├── libs/               # Thư viện ngoài (.jar, .aar)
│   ├── src/
│   │   ├── main/
│   │   │   ├── Java/       # Mã nguồn Java/Kotlin
│   │   │   ├── res/        # Tài nguyên ứng dụng
│   │   │   ├── assets/     # File dữ liệu tĩnh
│   │   │   └── AndroidManifest.xml
│   │   └── test/           # Unit Test
│   │   └── androidTest/    # Instrumented Test
│   └── build.gradle        # Cấu hình module app
├── build.gradle            # Cấu hình toàn project
├── gradle/
├── gradlew, gradlew.bat    # Gradle Wrapper
├── settings.gradle
└── ... (các file cấu hình khác)
```

#### 2.1. Thư mục `manifests`

Chứa file `AndroidManifest.xml` – trung tâm cấu hình của app:
- Khai báo các thành phần của app: Activity, Service, BroadcastReceiver, ContentProvider.
- Định nghĩa các quyền (permissions) mà app cần (ví dụ: truy cập Internet, camera, location...).
- Khai báo tên package, icon, theme, version code, version name của ứng dụng.
- Xác định Activity khởi động đầu tiên (Launcher Activity).
- Định nghĩa các intent-filter để app có thể nhận intent từ hệ thống hoặc app khác.
- Có thể khai báo một số meta-data, cấu hình cho các thư viện ngoài hoặc dịch vụ Google (Firebase, AdMob...).

**Tóm lại:**
Thư mục manifests chứa file AndroidManifest.xml, là nơi khai báo cấu hình tổng thể, các thành phần, quyền hạn và thông tin quan trọng của ứng dụng Android.

**Ví dụ:**
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">
    <application
        android:label="My App"
        android:icon="@mipmap/ic_launcher">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
</manifest>
```

#### 2.2. Thư mục `java` (hoặc `kotlin`)

Chứa mã nguồn chính của app, mỗi package là một thư mục con, cùng với các test.

- `src/main/java`: Code app.
- `src/test/java`: Chứa các Unit Test (kiểm thử đơn vị) chạy trên JVM mà không cần thiết bị thật hoặc giả lập.
- `src/androidTest/java`: Chứa các Instrumented Test (kiểm thử có thiết bị), dùng để kiểm thử tính năng của app trên thiết bị thật hoặc emulator.

**Tóm lại:**
Thư mục `java (hoặc kotlin)` là nơi chứa tất cả các file mã nguồn chính, các file kiểm thử đơn vị và kiểm thử tích hợp của ứng dụng Android. Đây là nơi phát triển logic, xử lý, giao tiếp dữ liệu, điều khiển giao diện, v.v. cho app.

#### 2.3. Thư mục `res` (Resource)

Thư mục res là nơi tập trung tất cả tài nguyên giao diện, dữ liệu tĩnh, hình ảnh, màu sắc, theme, font…

- **`drawable/`**: Chứa các file hình ảnh (bitmap như PNG, JPG, GIF), vector (SVG - dưới dạng XML), hoặc các resource vẽ bằng XML (shape, selector,...).
- **`layout/`**: Chứa các file giao diện mô tả bằng XML, ví dụ `activity_main.xml`, `fragment_profile.xml`, v.v.
- **`values/`**: Chứa các file XML định nghĩa giá trị dùng chung cho app như:
    - **`strings.xml`**: Chuỗi ký tự (dùng cho đa ngôn ngữ, dịch dễ dàng).
    - **`colors.xml`**: Màu sắc dùng trong app.
    - **`dimen.xml`**: Kích thước (đơn vị dp, sp).
    - **`styles.xml`**: Style, theme cho app.
    - **`arrays.xml`**: Mảng dữ liệu tĩnh.
- **`mipmap/`**: Chứa icon launcher ở các độ phân giải khác nhau (hdpi, mdpi, xhdpi, xxhdpi, xxxhdpi).
- **`anim/`**: Chứa file animation diễn hoạt dạng XML (chuyển động alpha, dịch chuyển, scale...).
- **`menu/`**: Chứa các file XML định nghĩa menu (context menu, options menu, popup menu).
- **`raw/`**: Chứa các file nhạc, âm thanh, video, dữ liệu nhị phân hoặc text cần dùng nguyên bản.
- **`font/`**: Chứa các file font chữ custom (ttf, otf).
- **`xml/`**: Chứa các file cấu hình XML khác như cấu hình navigation, backup, các resource đặc biệt.

#### 2.4. Thư mục `assets`

Thư mục `assets` trong project Android dùng để chứa các file dữ liệu tĩnh mà ứng dụng cần truy cập trực tiếp trong quá trình chạy. Các file này không phải là resource XML (như trong `res`), mà là các file dữ liệu nguyên bản, được đóng gói nguyên vẹn vào ứng dụng.
- Text: `.txt`, `.json`, `.csv`, `.xml`...
- HTML: file giao diện trình duyệt nội bộ.
- Âm thanh, video, font chữ, hình ảnh đặc biệt hoặc bất kỳ file nhị phân nào khác.

#### 2.5. Gradle Scripts

**Gradle Scripts** là tập hợp các file cấu hình dùng để tự động hóa quá trình build, test, đóng gói và quản lý phụ thuộc (dependency) trong dự án Android. Các file này nằm ở thư mục gốc hoặc trong từng module của project.

**Các file Gradle Scripts phổ biến**

- **`build.gradle (Project)`**  
  - Đây là file cấu hình cấp dự án (project-level).
  - Khai báo các plugin build, repository (Google, Maven Central…), dependency version dùng chung, các task chung cho toàn bộ project.

- **`build.gradle (Module: app)`**  
  - Đây là file cấu hình cho từng module (module-level), thường là module `app`.
  - Khai báo compileSdk, minSdk, targetSdk, các dependency cho module, cấu hình build types (debug/release), flavor, versionCode, versionName, signing, v.v.

- **`settings.gradle`**  
  - Khai báo các module (subproject) mà project sử dụng.
  - Quản lý tên project và bao gồm các module con.

- **`gradle.properties`**  
  - Chứa các biến cấu hình dùng chung, tối ưu hóa build, cấu hình JVM, đường dẫn, key API, v.v.

- **`gradle/`**  
  - Thư mục chứa các script cấu hình mở rộng hoặc script build dùng chung cho nhiều project.

- **`gradlew` và `gradlew.bat`**  
  - Các script wrapper giúp chạy Gradle mà không cần cài đặt Gradle trên máy (gradlew cho Linux/macOS, gradlew.bat cho Windows).

**Vai trò của Gradle Scripts:**

- **Quản lý phụ thuộc:** Tự động tải về và tích hợp các thư viện bên ngoài.
- **Cấu hình build:** Xác định cách build, test, đóng gói app.
- **Tùy biến build:** Hỗ trợ build nhiều phiên bản app (debug, release, flavor…).
- **Tự động hóa:** Có thể thêm các task tự động hóa như: clean, test, deploy...

**Lưu ý:**

- Khi sửa đổi các file Gradle Scripts, cần **Sync Project** để áp dụng thay đổi.
- Lỗi trong các file này có thể khiến build hoặc chạy ứng dụng thất bại.
  

#### 2.6. Một số file/thư mục khác

- `.gitignore`: File loại trừ khi commit lên Git.
- `.idea/`: Cấu hình IDE, không cần sửa thủ công.
- `build/`: File tạm, sinh ra khi build.

---

### 3. Giới thiệu về XML trong Android

**XML** (eXtensible Markup Language) là ngôn ngữ đánh dấu dùng để định nghĩa dữ liệu có cấu trúc. Trong Android, **XML** dùng để:

- Thiết kế giao diện (layout).
- Định nghĩa chuỗi ký tự, màu sắc, kích thước, style.
- Cấu hình animation, menu, navigation.
- Khai báo permission, intent filter trong Manifest.

**Ưu điểm khi dùng XML:**
- Phân tách rõ ràng giữa code logic và giao diện.
- Dễ dàng tái sử dụng & bảo trì.
- Hỗ trợ preview trực quan trong Android Studio.

**Ví dụ layout XML:**
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Xin chào Android!"/>
    <Button
        android:id="@+id/btnClick"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Nhấn tôi"/>
</LinearLayout>
```

**Một số file XML phổ biến:**
- `activity_main.xml`: Layout cho Activity chính.
- `fragment_home.xml`: Layout cho Fragment.
- `strings.xml`: Khai báo text dùng chung.
- `colors.xml`: Khai báo màu sắc.
- `styles.xml`: Khai báo style, theme.
- `menu_main.xml`: Định nghĩa menu.

---

### 4. Các tool cơ bản có trong Android Studio

#### 4.1. Debugger

**Debugger** là công cụ mạnh mẽ giúp lập trình viên tìm và sửa lỗi trong quá trình phát triển ứng dụng Android. Debugger cho phép bạn quan sát, kiểm soát quá trình thực thi mã nguồn để hiểu rõ hoạt động của chương trình, phát hiện và xử lý lỗi hiệu quả.

##### Các tính năng chính của Debugger:

- **Đặt breakpoint:**  
  Dừng chương trình tại dòng mã mong muốn để kiểm tra trạng thái hệ thống, biến, đối tượng.
- **Bước qua từng dòng lệnh (step over, step into, step out):**  
  Điều khiển quá trình thực thi chương trình từng dòng hoặc từng hàm để theo dõi luồng chạy.
- **Xem và chỉnh sửa giá trị biến:**  
  Kiểm tra giá trị các biến tại thời điểm dừng, thậm chí có thể thay đổi giá trị biến để thử nghiệm.
- **Theo dõi call stack:**  
  Quan sát thứ tự gọi hàm, giúp phát hiện lỗi logic, lỗi gọi nhầm hàm, vòng lặp lạ...
- **Kiểm tra luồng (thread):**  
  Theo dõi trạng thái các luồng trong ứng dụng, chuyển đổi giữa các thread để debug đa luồng.
- **Đặt watchpoint:**  
  Theo dõi sự thay đổi giá trị của một biến cụ thể.
- **Debug trên thiết bị thật hoặc emulator:**  
  Có thể debug trực tiếp trên điện thoại, tablet thật hoặc trên thiết bị ảo (Android Emulator).
- **Hỗ trợ debug cả Java/Kotlin và native C/C++:**  
  Android Studio hỗ trợ debug cho cả mã nguồn Java/Kotlin và mã native (thông qua NDK).


#### 4.2. Logcat

**Logcat** là công cụ giúp hiển thị nhật ký hệ thống (log) và log của ứng dụng Android trong quá trình chạy và phát triển. Đây là công cụ rất quan trọng để kiểm tra, theo dõi lỗi, dòng chạy chương trình, cũng như phân tích hoạt động của app.

## Các tính năng chính của Logcat

- **Hiển thị log thời gian thực:**  
  Cho phép xem các thông báo log từ hệ thống Android và từ ứng dụng đang chạy trên thiết bị/emulator.
- **Lọc log:**  
  Có thể lọc log theo tag, mức độ (Verbose, Debug, Info, Warn, Error, Assert), process, package, hoặc từ khóa bất kỳ.
- **Tìm kiếm nhanh:**  
  Dễ dàng tìm kiếm các dòng log liên quan đến vấn đề đang gặp phải.
- **Kết hợp với Debugger:**  
  Khi debug, logcat giúp theo dõi các sự kiện, lỗi, thông báo trong quá trình phân tích mã nguồn.
- **Hỗ trợ ghi log từ code:**  
  Sử dụng các phương thức như `Log.d()`, `Log.i()`, `Log.w()`, `Log.e()` để ghi log từ code ứng dụng.

##### Ví dụ ghi log trong code

```java
Log.d("MainActivity", "Ứng dụng đã khởi động");
Log.e("Login", "Lỗi đăng nhập: " + errorMessage);
```

##### Một số mức độ log phổ biến

- **Verbose (Log.v)**: Mọi thông tin, chi tiết nhất (ít dùng cho production).
- **Debug (Log.d)**: Dùng cho log debug, phát triển.
- **Info (Log.i)**: Thông tin chung, trạng thái hoạt động.
- **Warn (Log.w)**: Cảnh báo có thể gây lỗi.
- **Error (Log.e)**: Báo lỗi nghiêm trọng.
- **Assert (Log.wtf)**: Lỗi nghiêm trọng không thể phục hồi.

##### Lưu ý khi sử dụng Logcat

- Không để log nhạy cảm (mật khẩu, token, dữ liệu cá nhân) trong sản phẩm release.
- Có thể clear log, xuất log ra file để phân tích.
- Logcat hiển thị log của tất cả ứng dụng đang chạy trên thiết bị/emulator, nên nên lọc theo package hoặc tag để dễ kiểm soát.

#### 4.3. Layout Inspector

**Layout Inspector** là công cụ trực quan giúp lập trình viên Android kiểm tra, phân tích và gỡ lỗi giao diện (UI) của ứng dụng đang chạy trên thiết bị thật hoặc emulator. Công cụ này rất hữu ích để xem cấu trúc view, giá trị thuộc tính, và xác định các vấn đề về giao diện.

##### Chức năng chính của Layout Inspector:

- **Hiển thị cây giao diện (View Hierarchy):**  
  Cho phép xem cấu trúc phân cấp các view, widget, layout trong màn hình app.
- **Xem thuộc tính chi tiết của từng view:**  
  Xem giá trị các thuộc tính như width, height, padding, margin, visibility, text, background, v.v.
- **Chụp và phân tích UI theo thời gian thực:**  
  Hỗ trợ xem và phân tích giao diện ngay lúc app đang chạy, kể cả khi giao diện động thay đổi.
- **Chế độ 3D:**  
  Xem cây giao diện dưới dạng 3D, dễ phát hiện view bị chồng lấp hoặc bố trí sai.
- **Hỗ trợ tìm kiếm nhanh view:**  
  Có thể tìm kiếm tên view, ID, hoặc thuộc tính để truy cập nhanh.


#### 4.4. Profiler

- Kiểm tra hiệu suất CPU, Memory, Network, Energy.
- Phát hiện memory leak, thread block, GC, network bottleneck.

#### 4.5. Device File Explorer

- Truy cập hệ thống file của thiết bị/emulator.
- Lấy log, ảnh, dữ liệu lưu trữ nội bộ.

#### 4.6. APK Analyzer

- Phân tích file APK: cấu trúc, kích thước, tài nguyên, dependency.

#### 4.7. Emulator

- Chạy ứng dụng trên thiết bị ảo với nhiều cấu hình, độ phân giải, phiên bản Android khác nhau.
- Hỗ trợ cảm biến, vị trí, camera, network, simulate call/SMS, v.v.

#### 4.8. Refactor Tools

- Đổi tên, di chuyển, tách/gộp class, extract method, inline, convert Java↔Kotlin.

#### 4.9. Code Lint, Inspections

- Phân tích code, phát hiện lỗi tiềm ẩn, cảnh báo performance, style.

#### 4.10. Build Variant & Flavors

- Hỗ trợ build nhiều phiên bản app (free/pro, dev/release).

---

### 5. Các view cơ bản trong Android

#### 5.1. TextView

- Dùng để hiển thị văn bản tĩnh.
- Thuộc tính hay dùng: `text`, `textSize`, `textColor`, `gravity`, `maxLines`, `ellipsize`.

```xml
<TextView
    android:id="@+id/tvInfo"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Thông tin ứng dụng"
    android:textSize="16sp"
    android:textColor="#333"
    android:maxLines="2"
    android:ellipsize="end"/>
```

#### 5.2. Button

- Nút bấm thực hiện một hành động khi người dùng click.
- Thuộc tính: `text`, `onClick`, `background`, `enabled`.

```xml
<Button
    android:id="@+id/btnSubmit"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Gửi"
    android:onClick="onSubmitClicked"/>
```

#### 5.3. ImageView

- Hiển thị hình ảnh (bitmap, vector, drawable).
- Thuộc tính: `src`, `scaleType`, `adjustViewBounds`.

```xml
<ImageView
    android:id="@+id/imgAvatar"
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:src="@drawable/avatar"
    android:scaleType="centerCrop"/>
```

#### 5.4. EditText

- Nhập dữ liệu từ người dùng.
- Thuộc tính: `hint`, `inputType`, `maxLength`, `lines`, `textColorHint`.

```xml
<EditText
    android:id="@+id/edtUsername"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Tên đăng nhập"
    android:inputType="textPersonName"/>
```

#### 5.5. CheckBox

- Hộp kiểm chọn/bỏ chọn.
- Dùng cho lựa chọn không loại trừ nhau.

```xml
<CheckBox
    android:id="@+id/chkRemember"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Ghi nhớ tài khoản"/>
```

#### 5.6. RadioButton & RadioGroup

- Lựa chọn 1 trong nhiều tùy chọn.

```xml
<RadioGroup
    android:id="@+id/rgGender"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="horizontal">
    <RadioButton
        android:id="@+id/rbMale"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Nam"/>
    <RadioButton
        android:id="@+id/rbFemale"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Nữ"/>
</RadioGroup>
```

#### 5.7. Switch/ToggleButton

- Nút chuyển đổi trạng thái bật/tắt.

```xml
<Switch
    android:id="@+id/swDarkMode"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Chế độ tối"/>
```

#### 5.8. ProgressBar

- Hiển thị tiến trình (xoay tròn, thanh ngang).

```xml
<ProgressBar
    android:id="@+id/progressLoading"
    style="?android:attr/progressBarStyleHorizontal"
    android:layout_width="match_parent"
    android:layout_height="8dp"
    android:progress="50"/>
```

#### 5.9. ListView, RecyclerView

- Hiển thị danh sách dữ liệu động.

**ListView:**
```xml
<ListView
    android:id="@+id/listView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"/>
```

**RecyclerView** (tối ưu, hiện đại hơn ListView):
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/rvContacts"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

#### 5.10. CardView, Chip, FloatingActionButton

- CardView: hiển thị khối nội dung với shadow, bo góc.
- Chip: thẻ tròn nhỏ cho tag, lựa chọn, filter.
- FloatingActionButton: nút tròn nổi bật, thường dùng cho hành động chính.

```xml
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="8dp"
    app:cardElevation="4dp">
    <!-- Nội dung -->
</com.google.android.material.card.MaterialCardView>
```

---

## II. BASIC LAYOUT

### 1. FrameLayout, ConstraintLayout và các Layout khác

#### 1.1. FrameLayout

- Các view con xếp chồng lên nhau, view nào khai báo sau sẽ nằm trên cùng.
- Dùng cho các giao diện đơn giản hoặc overlay.

**Ví dụ:**
```xml
<FrameLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:src="@drawable/bg"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Chồng lên ảnh"/>
</FrameLayout>
```

#### 1.2. LinearLayout

- Sắp xếp view con theo chiều ngang (`horizontal`) hoặc dọc (`vertical`).
- Dùng thuộc tính `weight` để chia tỷ lệ không gian.

**Ví dụ:**
```xml
<LinearLayout
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="Trái"/>
    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="2"
        android:text="Phải"/>
</LinearLayout>
```

#### 1.3. RelativeLayout

- Định vị view con tương đối nhau (trên/dưới/trái/phải).
- Giảm số lượng layout lồng nhau so với LinearLayout.

**Ví dụ:**
```xml
<RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <Button
        android:id="@+id/btnLeft"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Trái"/>
    <Button
        android:id="@+id/btnRight"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Phải"
        android:layout_toRightOf="@id/btnLeft"/>
</RelativeLayout>
```

#### 1.4. TableLayout, GridLayout

- Sắp xếp các view theo dạng bảng, lưới.

**Ví dụ TableLayout:**
```xml
<TableLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
    <TableRow>
        <TextView android:text="Cột 1"/>
        <TextView android:text="Cột 2"/>
    </TableRow>
</TableLayout>
```

#### 1.5. ConstraintLayout

ConstraintLayout là layout mạnh mẽ nhất, linh hoạt, tối ưu hiệu suất, thay thế cho đa số các layout khác.

**Đặc điểm:**
- **Sắp xếp linh hoạt**: Có thể định vị View ở bất kỳ đâu trong layout bằng cách đặt constraint tương đối với các View khác hoặc parent.
- **Tiết kiệm hierarchy (phân cấp View)**: Giảm số lượng View lồng nhau, giúp tăng hiệu suất và dễ bảo trì.
- **Thiết kế giao diện phức tạp**: Có thể xây dựng layout phức tạp mà không cần nhiều ViewGroup lồng ghép như LinearLayout, RelativeLayout,...
- **Hỗ trợ Responsive UI**: Dễ dàng tạo giao diện thích ứng nhiều kích thước màn hình.
- **Tích hợp tốt với Layout Editor**: Android Studio hỗ trợ kéo thả, thiết lập constraint trực quan.

**Ví dụ cơ bản:**
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <Button
        android:id="@+id/btnA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="A"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"/>
    <Button
        android:id="@+id/btnB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="B"
        app:layout_constraintTop_toBottomOf="@id/btnA"
        app:layout_constraintStart_toStartOf="parent"/>
    <TextView
        android:id="@+id/tvCenter"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Giữa"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>
</androidx.constraintlayout.widget.ConstraintLayout>
```

**Một số thuộc tính constraint:**
##### 1. `app:layout_constraintTop_toTopOf`
Ràng buộc cạnh **trên** (top) của View này với cạnh **trên** của View khác (hoặc của parent).

**Ví dụ:**
```xml
app:layout_constraintTop_toTopOf="parent"
```
Cạnh trên của View sẽ trùng với cạnh trên của parent.

##### 2. `app:layout_constraintBottom_toBottomOf`
Ràng buộc cạnh **dưới** (bottom) của View này với cạnh **dưới** của View khác (hoặc của parent).

**Ví dụ:**
```xml
app:layout_constraintBottom_toBottomOf="@id/textView"
```
Cạnh dưới của View sẽ trùng với cạnh dưới của View có id là textView.

##### 3. `app:layout_constraintStart_toStartOf`
Ràng buộc cạnh **bắt đầu** (start - trái với ngôn ngữ LTR) của View này với cạnh start của View khác (hoặc của parent).

**Ví dụ:**
```xml
app:layout_constraintStart_toStartOf="parent"
```
Cạnh bắt đầu của View sẽ trùng với cạnh bắt đầu của parent.

##### 4. `app:layout_constraintEnd_toEndOf`
Ràng buộc cạnh **kết thúc** (end - phải với ngôn ngữ LTR) của View này với cạnh end của View khác (hoặc của parent).

**Ví dụ:**
```xml
app:layout_constraintEnd_toEndOf="@id/buttonLogin"
```
Cạnh end của View sẽ trùng với cạnh end của View có id là buttonLogin.

##### 5. `app:layout_constraintBaseline_toBaselineOf`
Ràng buộc đường **baseline** (đường cơ sở ký tự, dùng với TextView) của View này với baseline của View khác.

**Ví dụ:**
```xml
app:layout_constraintBaseline_toBaselineOf="@id/textViewTitle"
```
Dòng chữ của hai View sẽ nằm cùng một đường ngang.

##### 6. `app:layout_constraintHorizontal_chainStyle`
Chỉ định kiểu phân bố các View nằm trong **chuỗi ngang (horizontal chain)**:
- `spread`: Các View được giãn đều ra (mặc định).
- `spread_inside`: Hai View ở đầu và cuối sát parent, các View còn lại giãn đều khoảng cách giữa chúng.
- `packed`: Các View gom lại sát nhau, toàn chuỗi nằm ở giữa.

**Ví dụ:**
```xml
app:layout_constraintHorizontal_chainStyle="packed"
```


**Một số tính năng nổi bật:**

- **Chain (chuỗi các view)**: Sắp xếp nhiều View theo chuỗi ngang hoặc dọc với khoảng cách và phân bố linh hoạt.
- **Barrier**: Tạo "rào chắn" động dựa vào vị trí các View khác.
- **Guideline**: Đường tham chiếu giúp căn chỉnh các View dễ dàng.
- **Bias**: Điều chỉnh vị trí View theo tỷ lệ phần trăm giữa hai constraint.

**Ưu điểm ConstraintLayout:**
- Tối ưu hiệu suất, giảm số lượng view lồng nhau.
- Dễ dàng responsive, phù hợp nhiều kích thước màn hình.
- Thiết kế trực quan với Layout Editor.

---

### 2. ViewBinding và cách sử dụng

ViewBinding là tính năng giúp truy cập các view trong layout an toàn, mạnh mẽ, hiệu quả, thay thế `findViewById`.

**Cách bật ViewBinding:**
- Thêm vào `build.gradle (Module: app)`:
```gradle
android {
    ...
    viewBinding {
        enabled = true
    }
}
```
- Sync project.

**Cách sử dụng trong Activity:**
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.tvHello.text = "Xin chào"
        binding.btnClick.setOnClickListener {
            Toast.makeText(this, "Nhấn!", Toast.LENGTH_SHORT).show()
        }
    }
}
```

**Trong Fragment:**
```kotlin
class HomeFragment : Fragment(R.layout.fragment_home) {
    private var _binding: FragmentHomeBinding? = null
    private val binding get() = _binding!!
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        _binding = FragmentHomeBinding.bind(view)
        binding.tvTitle.text = "Hello Fragment"
    }
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

**Trong Adapter:**
```kotlin
class UserAdapter : RecyclerView.Adapter<UserAdapter.UserViewHolder>() {
    class UserViewHolder(val binding: ItemUserBinding) : RecyclerView.ViewHolder(binding.root)
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UserViewHolder {
        val binding = ItemUserBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return UserViewHolder(binding)
    }
    override fun onBindViewHolder(holder: UserViewHolder, position: Int) {
        // Truy cập view thông qua holder.binding
    }
    ...
}
```

**Ưu điểm ViewBinding:**
- An toàn kiểu dữ liệu, không sợ NullPointerException.
- Không cần ép kiểu.
- Dễ đọc, dễ maintain.

**Lưu ý:**
- Không hỗ trợ các layout dùng `<merge/>` làm root.
- Tên class binding theo quy tắc PascalCase: file `activity_main.xml` → `ActivityMainBinding`.