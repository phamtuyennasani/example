# Hướng dẫn sử dụng Class Session

Class `NASANICORE\Core\Session\Http\Session` cung cấp các phương thức để tương tác với PHP Session một cách dễ dàng và an toàn.

## Khởi tạo và Truy cập

Class `Session` chứa các phương thức tĩnh (static methods). Tuy nhiên, bạn cũng có thể sử dụng helper function `session()` để truy cập instance của class này.

```php
use NASANICORE\Core\Session\Http\Session;

// Sử dụng static method
Session::set('key', 'value');

// Sử dụng helper function
session()->set('key', 'value');
```

## Các phương thức chính

### 1. Lấy dữ liệu (Retrieving Data)

#### `get($param, $default = null, $sanitize = true)`
Lấy giá trị của một biến session.

- **$param**: Tên (key) của biến session. Có thể dùng "dot notation" để truy cập mảng đa chiều (ví dụ: `user.id`).
- **$default**: Giá trị trả về nếu không tìm thấy key.
- **$sanitize**: Nếu `true`, dữ liệu sẽ được làm sạch (escape HTML) trước khi trả về.

```php
$userId = Session::get('user_id');
$userName = Session::get('user.name', 'Guest');
```

#### `all()` hoặc `body()`
Lấy toàn bộ dữ liệu session dưới dạng mảng.

```php
$allData = Session::all();
```

#### `has($key)`
Kiểm tra xem một key có tồn tại trong session hay không.

```php
if (Session::has('user_id')) {
    // ...
}
```

### 2. Lưu dữ liệu (Storing Data)

#### `set($key, $value = null)`
Đặt giá trị cho một biến session. Hỗ trợ "dot notation".

```php
Session::set('user_id', 123);
Session::set('user.name', 'Nguyen Van A');
```

#### `append($key, $value = null)`
Thêm dữ liệu vào một mảng trong session hoặc nối chuỗi nếu dữ liệu hiện tại là chuỗi.

```php
Session::append('notifications', 'New alert');
```

### 3. Xóa dữ liệu (Removing Data)

#### `unset($key)`, `delete($key)`, `remove($key)`
Xóa một hoặc nhiều key khỏi session.

```php
Session::unset('user_id');
Session::unset(['user_id', 'user_name']);
```

#### `retrieve($key, $default = null, $sanitize = true)`
Lấy giá trị của một key và xóa nó khỏi session ngay lập tức.

```php
$value = Session::retrieve('temp_token');
```

#### `clear()`
Xóa toàn bộ dữ liệu trong session (nhưng không hủy session ID).

```php
Session::clear();
```

#### `destroy()`
Hủy toàn bộ session hiện tại.

```php
Session::destroy();
```

### 4. Flash Messages

#### `flash($message = null)`
Đặt hoặc hiển thị thông báo flash (thông báo chỉ tồn tại trong 1 request kế tiếp).

```php
// Đặt flash message
Session::flash('Update successful!');

// Hiển thị flash message (thường dùng trong View)
echo Session::flash();
```

### 5. Quản lý Session ID

#### `id($id = null)`
Lấy hoặc đặt session ID hiện tại.

```php
$sessionId = Session::id();
```

#### `regenerate($clearData = false)`
Tạo lại session ID mới (để ngăn chặn tấn công session fixation).

- **$clearData**: Nếu `true` sẽ xóa luôn dữ liệu cũ.

```php
Session::regenerate();
```

#### `reset($id = null)`
Reset lại session với ID mới.

```php
Session::reset();
```

### 6. Mã hóa (Encoding)

#### `encode()`
Mã hóa dữ liệu session hiện tại thành chuỗi.

#### `decode($data)`
Giải mã dữ liệu từ chuỗi vào session.

---

## Ví dụ tổng hợp

```php
// Đăng nhập người dùng
public function login() {
    // ... xác thực thành công ...
    
    // Lưu thông tin người dùng
    session()->set('auth.user_id', $user->id);
    session()->set('auth.role', 'admin');
    
    // Tạo lại session ID bảo mật
    session()->regenerate();
    
    // Thông báo
    session()->flash('Đăng nhập thành công!');
    
    return redirect('/dashboard');
}

// Kiểm tra quyền
public function checkAccess() {
    if (!session()->has('auth.user_id')) {
        return redirect('/login');
    }
    
    $role = session()->get('auth.role');
    // ...
}
```
