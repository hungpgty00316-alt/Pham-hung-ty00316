# BÁO CÁO ASSIGNMENT MÔN LẬP TRÌNH CƠ SỞ VỚI JAVASCRIPT (WEB1044)

**Trường:** FPT Polytechnic  
**Môn học:** Lập trình cơ sở với JavaScript (WEB1044)  
**Mã Sinh Viên:** TY00316  
**Dự án Website:** TechMart - Cửa Hàng Thiết Bị Công Nghệ  

---

## 1. GIỚI THIỆU DỰ ÁN
Dự án **TechMart** được thiết kế dựa trên ngôn ngữ **JavaScript thuần (Vanilla JS)** với cấu trúc mã nguồn đơn giản, chuẩn mực, dễ đọc và dễ bảo trì dành cho sinh viên. Website thực hiện đầy đủ các tính năng cơ bản của một trang thương mại điện tử:
- Xem và lọc danh mục sản phẩm (Hot, Mới, Sale).
- Up và quản lý sản phẩm mới (Thêm, Sửa, Xóa sản phẩm lưu trên `localStorage`).
- Đồng hồ đếm ngược Flash Sale thời gian thực.
- Giỏ hàng lưu trữ bằng `localStorage` (thêm, sửa số lượng, xóa, tổng tiền).
- Đăng ký & Đăng nhập tài khoản lưu trên `localStorage`.
- Kiểm tra dữ liệu Form (Validate bắt lỗi rỗng, email, mật khẩu, giá bán).

---

## 2. CẤU TRÚC MÃ NGUỒN DỰ ÁN

```text
asm_web1044_ty00316/
├── css/
│   └── style.css            # Toàn bộ kiểu dáng, màu sắc và layout website
├── js/
│   ├── data.js              # Mảng danh sách sản phẩm mặc định (Array of Objects)
│   ├── main.js              # Logic chính: render sản phẩm, up sản phẩm, giỏ hàng, đăng nhập, validate form
│   └── countdown.js         # Chức năng nâng cao: Đồng hồ đếm ngược Flash Sale
├── index.html               # Trang chủ (Banner, Flash Sale, Sản phẩm nổi bật)
├── products.html            # Trang danh sách sản phẩm & bộ lọc
├── upload-product.html      # Trang Up sản phẩm & Quản lý danh sách sản phẩm (CRUD LocalStorage)
├── cart.html                # Trang giỏ hàng & thanh toán
├── login.html               # Trang Đăng nhập / Đăng ký tài khoản (2 tab)
├── contact.html             # Trang liên hệ
└── TY00316_BaoCao_Assignment_WEB1044.md # Bài báo cáo chi tiết
```

---

## 3. Danh Sách Các Chức Năng Và Cách Thức Hoạt Động

### 3.1. Quản lý & Render Sản Phẩm Dùng Mã Giả / Mảng Đối Tượng
- **File xử lý:** `js/data.js` & `js/main.js`
- **Cách hoạt động:**
  1. `data.js` chứa mảng `danhSachSanPham` mặc định với các thuộc tính: `id`, `ten`, `gia`, `giaCu`, `hinhAnh`, `danhMuc`, `nhom` (`hot`, `moi`, `sale`), `moTa`.
  2. Hàm `layDanhSachSanPham()` kiểm tra và đồng bộ danh sách sản phẩm lưu trong `localStorage.getItem('danhSachSanPham')`.
  3. Hàm `hienThiSanPham(danhSach)` trong `main.js` duyệt qua mảng bằng vòng lặp `for`, tạo các chuỗi HTML thẻ sản phẩm và chèn vào giao diện bằng `innerHTML`.
  4. Nhấp vào thẻ sản phẩm hoặc nút **👁️ Xem Chi Tiết** mở hộp thoại chi tiết gồm hình ảnh, danh mục, nhóm, mô tả và giá; có thể thêm sản phẩm vào giỏ ngay tại hộp thoại.
  5. Bộ lọc `locDanhMuc(nhom)` thực hiện lọc mảng theo thuộc tính `nhom`/`danhMuc` và gọi lại hàm hiển thị.

### 3.2. Giỏ Hàng Chi Tiết (LocalStorage)
- **File xử lý:** `js/main.js`
- **Cách hoạt động:**
  1. Giỏ hàng lưu trữ trong trình duyệt bằng `localStorage.getItem('gioHang')` dưới dạng chuỗi JSON.
  2. **Thêm vào giỏ (`themVaoGio(id)`):** Tìm sản phẩm theo `id` trong `layDanhSachSanPham()`. Nếu đã có trong giỏ thì tăng `soLuong`, chưa có thì thêm mới. Sau đó ghi lại vào LocalStorage bằng `JSON.stringify()`.
  3. **Hiển thị giỏ hàng (`hienThiGioHang()`):** Đọc giỏ hàng từ `localStorage`, dùng vòng lặp duyệt tính thành tiền từng món (`gia * soLuong`) và tổng tiền toàn giỏ hàng.
  4. **Thay đổi số lượng & Xóa:** Các hàm `thayDoiSoLuong(id, delta)` và `xoaKhoiGio(id)` cập nhật trực tiếp mảng giỏ hàng và lưu lại `localStorage`.

### 3.3. Trang Đăng Nhập & Đăng Ký (Session & Phân Quyền LocalStorage)
- **File xử lý:** `login.html` & `js/main.js`
- **Cách hoạt động:**
  1. **Tài khoản mặc định:** Hệ thống tự động khởi tạo 2 tài khoản thử nghiệm trong `localStorage.getItem('danhSachTaiKhoan')`:
     - **Admin:** `admin@techmart.com` / `admin123` (`vaiTro: "admin"`)
     - **User:** `user@techmart.com` / `user123` (`vaiTro: "user"`)
  2. **Đăng ký:** Nhập Tên, Email, Mật khẩu. Nếu hợp lệ và không trùng email, tạo user mới với `vaiTro: "user"` và lưu vào LocalStorage.
  3. **Đăng nhập:** So sánh Email và Mật khẩu. Nếu khớp, lưu thông tin vào `localStorage.setItem('userDangNhap', ...)` bao gồm cả thuộc tính `vaiTro`.
  4. **Quản lý phiên & Giao diện Header (`capNhatHeaderUser()`):** Cập nhật Tên user + Badge (`👑 ADMIN` hoặc `👤 USER`). Nếu là Admin, hệ thống mới cho hiển thị liên kết `⚡ Up Sản Phẩm (Admin)` trên thanh điều hướng menu.

```mermaid
flowchart TD
    A[Người dùng truy cập Trang web] --> B{Đã Đăng Nhập?}
    B -- Chưa đăng nhập --> C[Khách hàng thông thường]
    B -- Đã đăng nhập --> D{Kiểm tra vaiTro trong localStorage}
    
    C --> E[Xem danh sách & Giỏ hàng]
    
    D -- vaiTro == 'user' --> F[Quyền USER: Xem SP, Thêm giỏ hàng, Mua sắm]
    D -- vaiTro == 'admin' --> G[Quyền ADMIN: Full tính năng + Up/Sửa/Xóa SP]
    
    H[Cố truy cập upload-product.html] --> I{kiemTraQuyenAdmin()}
    I -- Không phải Admin --> J[Bật thông báo Từ chối & Đẩy về login.html]
    I -- Là Admin --> K[Cho phép vào trang Quản Lý Sản Phẩm]
```

### 3.4. Trang Up & Quản Lý Sản Phẩm Mới (Upload & Admin Guard LocalStorage)
- **File xử lý:** `upload-product.html` & `js/main.js`
- **Cách hoạt động:**
  1. **Bảo vệ tuyến đường (`kiemTraBaoVeTrangAdmin()`):** Khi vào trang `upload-product.html`, hệ thống tự động kiểm tra `userDangNhap.vaiTro === 'admin'`. Nếu không phải Admin, sẽ lập tức hiển thị cảnh báo từ chối truy cập và tự động chuyển hướng về trang Đăng nhập.
  2. **Up sản phẩm mới (`xuLyUpSanPham()`):** Lấy dữ liệu từ Form (Tên sản phẩm, Danh mục, Nhóm hiển thị, Giá bán, Giá cũ, Mô tả và Hình ảnh từ Upload file Base64/Link URL/Ảnh mẫu). Kiểm tra tính hợp lệ (Validate bắt lỗi rỗng, giá > 0, giá niêm yết không thấp hơn giá bán, link ảnh phải dùng HTTP/HTTPS và yêu cầu chọn tệp khi upload ảnh). Khi sửa sản phẩm mà không chọn ảnh thay thế, ảnh hiện tại được giữ nguyên. Tạo đối tượng sản phẩm mới có `id` tự tăng và đưa lên đầu mảng `danhSachSanPham` lưu trong `localStorage`.
  3. **Xem trước hiển thị (Live Preview Card):** Bắt sự kiện `oninput`/`onchange` trên form để hiển thị trực quan card sản phẩm mẫu theo thời gian thực trước khi bấm Up.
  4. **Danh sách quản lý & Thao tác (Sửa / Xóa / Reset):** Hàm `hienThiDanhSachQuanLy()` render bảng tất cả sản phẩm. Cho phép bấm **✏️ Sửa** (nạp dữ liệu vào form để cập nhật) hoặc **🗑️ Xóa** (xóa sản phẩm khỏi `localStorage`) và nút **🔄 Khôi Phục Mặc Định**.

### 3.5. Chức Năng Nâng Cao: Đồng Hồ Đếm Ngược & Quản Lý Flash Sale Cho Admin
- **File xử lý:** `js/countdown.js` & `upload-product.html`
- **Cách hoạt động:**
  1. Hàm `layMocThoiGian()` lấy hoặc khởi tạo mốc đếm ngược trong `localStorage.getItem('thoiGianFlashSale')`.
  2. Dùng `setInterval(capNhatDongHo, 1000)` để thực hiện tính toán mỗi 1 giây:
     $$\text{Thời gian còn lại} = \text{Thời gian kết thúc} - \text{Thời gian hiện tại}$$
  3. Đổi số mili-giây còn lại ra số **Giờ, Phút, Giây** và gán vào thẻ HTML qua `document.getElementById()`.
  4. **Tự động trả giá về mặc định khi hết giờ (`hetGioFlashSale()`):** Khi thời gian còn lại $\le 0$, hệ thống hiển thị "⏰ Chương trình Flash Sale đã kết thúc!", tự động gán lại `gia = giaCu` (trả về giá gốc ban đầu) cho các sản phẩm Flash Sale và cập nhật lại giao diện.
  5. **Bảng cài đặt Flash Sale dành cho Admin (`upload-product.html`):** Cho phép tài khoản Admin thực hiện:
     - Theo dõi trạng thái (🟢 Đang đếm ngược / 🔴 Hết giờ) thời gian thực.
     - Chọn nhanh số giờ đếm ngược mới: **+1 Giờ, +6 Giờ, +12 Giờ, +24 Giờ** (`datSoGioFlashSale()`).
     - Chọn mốc ngày & giờ kết thúc cụ thể (`datDatetimeFlashSale()`).
     - Nút **⏹️ Kết Thúc Flash Sale Ngay** (`ketThucFlashSaleNgay()`).

### 3.6. Kiểm Tra Dữ Liệu Form (Validation)
- **File xử lý:** `login.html`, `upload-product.html` & `js/main.js`
- **Cách hoạt động:**
  1. Bắt sự kiện submit hoặc khi nhấn nút bấm.
  2. Kiểm tra ô trống (`value.trim() === ''`).
  3. Kiểm tra định dạng Email (`/^[^\s@]+@[^\s@]+\.[^\s@]+$/`).
  4. Kiểm tra độ dài Mật khẩu (`password.length >= 6`) và Kiểm tra Giá bán phải lớn hơn 0.
  5. Nếu có lỗi, hiển thị thông báo màu đỏ bên dưới ô input và dừng form không gửi đi.

---

## 4. DANH SÁCH HÌNH ẢNH SẢN PHẨM TRONG DỰ ÁN

Tất cả các sản phẩm trên website đều sử dụng hình ảnh thực tế chất lượng cao nằm trong thư mục `images/`:

| ID | Tên sản phẩm | Danh mục | Nhóm | Đường dẫn file hình ảnh |
|---|---|---|---|---|
| **#1** | iPhone 15 Pro Max 256GB | Điện Thoại | HOT | `images/iphone15.jpg` |
| **#2** | MacBook Pro 14 M3 | Laptop | MỚI | `images/macbook.jpg` |
| **#3** | Samsung Galaxy S24 Ultra 5G | Điện Thoại | SALE | `images/samsung.jpg` |
| **#4** | Apple Watch Ultra 2 | Đồng Hồ | HOT | `images/watch.jpg` |
| **#5** | AirPods Pro 2 | Phụ Kiện | SALE | `images/airpods.jpg` |
| **#6** | Dell XPS 13 Plus Core i7 | Laptop | MỚI | `images/dell.jpg` |

---

## 5. KẾT LUẬN
Website **TechMart (Assignment WEB1044)** hoàn thành 100% đầy đủ yêu cầu của môn học. Cấu trúc mã nguồn dễ hiểu, áp dụng đầy đủ kiến thức mảng, vòng lặp, DOM, sự kiện, LocalStorage, setInterval, Validate Form, Phân quyền Admin/User và Quản lý Up sản phẩm.
