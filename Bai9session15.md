#  THIẾT KẾ HỆ THỐNG LƯU TRỮ GOOGLE DRIVE - CAMPUSPAY

## PHẦN 1: PHÂN TÍCH BÀI TOÁN (I/O)

### 1. Phân tích Đầu vào và Đầu ra

* **Đầu vào (Inputs):**
* Các ban nhóm: Ban Kỹ thuật (3 người), Ban Thiết kế (2 người), Ban Kinh doanh (2 người), Nhà đầu tư (1 người).
* Tài liệu: Source code/API keys, thiết kế UI, business plan, slide pitch.
* Ràng buộc: Tối thiểu quyền, chống rò rỉ (`Restricted`), version history $\ge$ 30 ngày, thu hồi quyền tự động (14 ngày).


* **Đầu ra (Outputs):**
* Cấu trúc thư mục 2 cấp trên Google Drive.
* Bảng phân quyền chi tiết cho từng vai trò và loại tài liệu.
* Phương án giải quyết các bẫy dữ liệu (chia sẻ chéo, tải offline, offboarding nhân sự).



### 2. Cấu trúc thư mục Google Drive 2 cấp

* `00_CampusPay_Root/`
* `01_Ban_Ky_Thuat/` (Chứa source code, API keys, tài liệu kỹ thuật)
* `02_Ban_Thiet_Ke/` (Chứa file thiết kế UI/UX, đồ họa)
* `03_Ban_Kinh_Doanh/` (Chứa business plan, tài liệu tài chính)
* `04_Tai_Lieu_Chung/` (Chứa Slide Pitch, Business Summary dùng chung và trình bày với nhà đầu tư)



### 3. Các bước thiết lập hệ thống

1. Tạo thư mục gốc và các thư mục cấp 2 tương ứng cho từng ban.
2. Tắt tính năng chia sẻ cộng đồng (`Anyone with link`) ở thư mục gốc.
3. Cấp quyền `Restricted` theo danh sách email cá nhân dựa trên nguyên tắc tối thiểu quyền.
4. Kiểm tra phân quyền thực tế giữa các ban để tránh lộ lọt dữ liệu.
5. Cài đặt thời hạn truy cập (Expiration date) 14 ngày cho nhà đầu tư và thực hiện thu hồi khi nhân sự nghỉ việc.

---

## PHẦN 2: TRIỂN KHAI & CHỐNG LỖI

### 4. Bảng phân quyền hoàn chỉnh

| Loại tài liệu | Ban KT (3) | Ban TK (2) | Ban KD (2) | Nhà đầu tư (1) | Ghi chú bảo mật |
| --- | --- | --- | --- | --- | --- |
| **1. Tài liệu Kỹ thuật & API Keys** | Chỉnh sửa (Editor) | Không | Không | Không | `Restricted` tuyệt đối, bảo mật nội bộ Kỹ thuật. |
| **2. Tài liệu Thiết kế & UI** | Không | Chỉnh sửa (Editor) | Không | Không | Giới hạn trong ban Thiết kế. |
| **3. Business Plan & Tài chính** | Không | Không | Chỉnh sửa (Editor) | Không | Tài liệu mật của ban Kinh doanh. |
| **4. Slide Pitch & Summary** | Xem (Viewer) | Xem (Viewer) | Chỉnh sửa (Editor) | Xem (Viewer) | Đặt ở thư mục chung để chia sẻ chéo và gọi vốn. |
| **5. Thư mục Quản trị gốc** | Admin | Không | Không | Không | Chỉ Trưởng nhóm / DevOps quản lý. |

### 5. Giải quyết các bẫy dữ liệu

* **Bẫy 1 (Chia sẻ Business Plan với Kỹ thuật nhưng không cho sửa):**
* *Giải pháp:* Cấp quyền **Xem (Viewer)** hoặc **Nhận xét (Commenter)**, đồng thời **bật** tính năng chặn tải xuống/sao chép (`Disable options to download, print, and copy`).
* *Lý do:* Giúp Kỹ thuật nắm bắt nghiệp vụ để code đúng yêu cầu mà không làm thay đổi hay rò rỉ nội dung gốc.


* **Bẫy 2 (Nhà đầu tư muốn tải PDF Slide Pitch để xem offline nhưng không được sửa):**
* *Giải pháp:* Cấp quyền **Xem (Viewer)** và **cho phép tải xuống** (`Viewers can download`), hoặc xuất sẵn file PDF bảo mật gửi cho nhà đầu tư.
* *Lý do:* Đáp ứng nhu cầu xem offline của nhà đầu tư, đồng thời định dạng file đọc (PDF hoặc chế độ chỉ xem) đảm bảo họ không thể chỉnh sửa số liệu cốt lõi của dự án.


* **Bẫy 3 (Xử lý khi thành viên nghỉ việc):**
* *Giải pháp:* Vào quản trị Google Workspace / Google Drive thu hồi ngay quyền truy cập thư mục gốc của tài khoản đó, kiểm tra các tệp chia sẻ độc lập (Direct shares) và vô hiệu hóa tài khoản email công ty để ngắt toàn bộ phiên làm việc.