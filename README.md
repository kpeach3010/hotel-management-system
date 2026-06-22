# Hệ Thống Quản Lý Khách Sạn (Hotel Management System)

---

## Giới thiệu dự án

**Hệ Thống Quản Lý Khách Sạn** là một ứng dụng web được xây dựng nhằm hỗ trợ công tác quản lý và vận hành khách sạn một cách hiệu quả. Hệ thống cung cấp đầy đủ các chức năng từ quản lý phòng, khách hàng, nhân viên, dịch vụ cho đến lập hóa đơn và thống kê doanh thu.

Dự án áp dụng kiến trúc **Client – Server**, trong đó giao diện người dùng (Frontend) giao tiếp với máy chủ (Backend) thông qua **RESTful API**.

---

## Cấu trúc dự án

```
hotel-management-system/
├── backend/
│   ├── main.py           # Máy chủ Flask – toàn bộ RESTful API
│   └── database.sql      # Schema CSDL & stored procedures
└── frontend/
    ├── trangchu.html     # Trang chủ
    ├── dangnhap.html     # Đăng nhập
    ├── dangky.html       # Đăng ký tài khoản
    ├── phong.html        # Quản lý phòng
    ├── khachhang.html    # Quản lý khách hàng
    ├── nhanvien.html     # Quản lý nhân viên
    ├── dichvu.html       # Quản lý dịch vụ
    ├── thuephong.html    # Quản lý thuê phòng
    ├── hoadon.html       # Quản lý hóa đơn
    ├── timkiem.html      # Tìm kiếm tổng hợp
    ├── thongke.html      # Thống kê doanh thu
    └── background.png    # Hình nền giao diện
```

---

## Chức năng hệ thống

| Chức năng           | Mô tả                                                                 |
|---------------------|-----------------------------------------------------------------------|
| Xác thực            | Đăng ký, đăng nhập với mật khẩu mã hóa bcrypt                       |
| Quản lý phòng       | Thêm, sửa, xóa phòng; cập nhật trạng thái; xuất Excel               |
| Quản lý khách hàng  | Thêm, sửa, xóa thông tin khách hàng; xuất Excel                     |
| Quản lý nhân viên   | Thêm, sửa, xóa thông tin nhân viên; xuất Excel                      |
| Quản lý dịch vụ     | Thêm, sửa, xóa dịch vụ khách sạn; xuất Excel                        |
| Thuê phòng          | Đặt phòng, cập nhật và hủy đặt phòng; xuất Excel                    |
| Hóa đơn             | Tạo, sửa, xóa hóa đơn; xuất file Excel                              |
| **Tìm kiếm**        | Tìm kiếm tổng hợp khách hàng, nhân viên và phòng *(xem bên dưới)*   |
| Thống kê            | Thống kê doanh thu theo ngày, tuần, tháng bằng biểu đồ              |

---

## Chức năng Tìm kiếm (Đóng góp cá nhân)

> Đây là chức năng mà tôi đảm nhận **toàn bộ** từ thiết kế cơ sở dữ liệu, xây dựng API Backend đến lập trình giao diện Frontend.

### Backend – Stored Procedures & API

Phần backend của chức năng tìm kiếm được xây dựng trên **Flask (Python)** với **MySQL Stored Procedures** để tối ưu hiệu năng truy vấn:

#### Stored Procedures (MySQL)

| Procedure              | Tham số đầu vào                                      | Chức năng                                                        |
|------------------------|------------------------------------------------------|------------------------------------------------------------------|
| `layDSKH`              | `startDate`, `endDate`, `orderType`, `customerName`  | Tìm kiếm khách hàng theo tên, khoảng ngày thuê, sắp xếp A-Z/Z-A |
| `layDanhSachPhong`     | `soPhong`, `tenLoai`, `trangThai`                    | Tìm kiếm phòng theo số phòng, loại phòng và trạng thái          |
| `layDanhSachNhanVien`  | `tenNhanVien`, `soDienThoai`                         | Tìm kiếm nhân viên theo tên hoặc số điện thoại                  |

Cả 3 stored procedures đều sử dụng **truy vấn động (Dynamic SQL)** với `PREPARE` / `EXECUTE` để linh hoạt xử lý nhiều tổ hợp điều kiện lọc khác nhau.

#### REST API Endpoints

| Method | Endpoint           | Mô tả                                  |
|--------|--------------------|----------------------------------------|
| POST   | `/get-customers`   | Tìm kiếm danh sách khách hàng          |
| POST   | `/get-room-list`   | Tìm kiếm danh sách phòng               |
| POST   | `/get-employees`   | Tìm kiếm danh sách nhân viên           |

Tất cả API đều nhận request dạng **JSON**, gọi stored procedure tương ứng và trả về kết quả JSON cho frontend.

### Frontend – Giao diện Tìm kiếm (`timkiem.html`)

Trang tìm kiếm được thiết kế dạng **Tab UI** với 3 tab riêng biệt:

- **Tab Khách hàng**: Tìm theo tên, khoảng ngày nhận/trả phòng, sắp xếp A→Z / Z→A
- **Tab Nhân viên**: Tìm theo tên hoặc số điện thoại
- **Tab Phòng**: Tìm theo số phòng, loại phòng, lọc theo trạng thái (Trống / Đang sử dụng)

Mỗi tab hiển thị kết quả theo bảng và có nút **Xuất Excel** để tải dữ liệu kết quả ra file `.xlsx` sử dụng thư viện `SheetJS (xlsx)` và `FileSaver.js`.

---

## Công nghệ sử dụng

| Tầng       | Công nghệ                                      |
|------------|------------------------------------------------|
| Frontend   | HTML5, CSS3, JavaScript (Vanilla)              |
| Backend    | Python 3, Flask, Flask-CORS                    |
| Database   | MySQL (PyMySQL connector)                      |
| Bảo mật    | bcrypt (mã hóa mật khẩu)                      |
| Xuất file  | Pandas (Excel phía server), SheetJS (phía client) |

---

## Hướng dẫn cài đặt & chạy dự án

### Yêu cầu hệ thống

- Python 3.8+
- MySQL Server 5.7+
- Trình duyệt web hiện đại (Chrome, Firefox, Edge...)

### 1. Clone dự án

```bash
git clone https://github.com/<your-username>/hotel-management-system.git
cd hotel-management-system
```

### 2. Cài đặt thư viện Python

```bash
pip install flask flask-cors pymysql pandas openpyxl bcrypt
```

### 3. Tạo cơ sở dữ liệu

Mở MySQL Workbench hoặc terminal MySQL và chạy:

```sql
source backend/database.sql
```

### 4. Cấu hình kết nối Database

Mở file `backend/main.py` và chỉnh sửa thông tin kết nối:

```python
def get_db_connection():
    return pymysql.connect(
        host='127.0.0.1',
        user='root',
        password='YOUR_PASSWORD',   # Nhập mật khẩu MySQL tại đây
        database='QuanLyKhachSan',
        cursorclass=pymysql.cursors.DictCursor
    )
```

### 5. Khởi động Backend

```bash
cd backend
python main.py
```

Máy chủ Flask sẽ chạy tại: `http://127.0.0.1:5000`

### 6. Mở Frontend

Mở trực tiếp file `frontend/trangchu.html` bằng trình duyệt hoặc dùng **Live Server** (VS Code extension).

---

## Cơ sở dữ liệu

### Sơ đồ các bảng chính

```
LoaiPhong --< Phong --< ThuePhong >-- KhachHang
                              |
                        SuDungDichVu >-- DichVu
                              |
                           HoaDon
NhanVien (doc lap)
users (tai khoan dang nhap)
```

### Các bảng

| Bảng            | Mô tả                                      |
|-----------------|--------------------------------------------|
| `LoaiPhong`     | Loại phòng và giá                          |
| `Phong`         | Thông tin từng phòng, trạng thái           |
| `KhachHang`     | Thông tin khách hàng                       |
| `NhanVien`      | Thông tin nhân viên                        |
| `DichVu`        | Danh sách dịch vụ khách sạn               |
| `ThuePhong`     | Lịch sử đặt/thuê phòng                    |
| `SuDungDichVu`  | Dịch vụ đã sử dụng trong từng lần thuê   |
| `HoaDon`        | Hóa đơn thanh toán                        |
| `users`         | Tài khoản đăng nhập hệ thống              |

---

## Vai trò cá nhân trong dự án

Trong dự án nhóm này, tôi đảm nhận **toàn bộ chức năng Tìm kiếm** bao gồm cả Backend lẫn Frontend:

### Backend (Flask + MySQL)
- Thiết kế 3 **MySQL Stored Procedures** (`layDSKH`, `layDanhSachPhong`, `layDanhSachNhanVien`) với truy vấn động hỗ trợ nhiều bộ lọc kết hợp linh hoạt
- Xây dựng 3 **REST API endpoints** (`/get-customers`, `/get-room-list`, `/get-employees`) xử lý request JSON và gọi stored procedure tương ứng
- Xử lý encoding UTF-8 cho dữ liệu tiếng Việt trong response API

### Frontend (HTML/CSS/JavaScript)
- Thiết kế và lập trình trang `timkiem.html` với giao diện **Tab UI** cho 3 nhóm đối tượng tìm kiếm
- Triển khai toàn bộ logic JavaScript: gọi API, xử lý response, render kết quả động vào bảng HTML
- Tích hợp chức năng **xuất Excel** từ dữ liệu kết quả tìm kiếm sử dụng thư viện SheetJS

---



Dự án này được phát triển cho mục đích học tập.


