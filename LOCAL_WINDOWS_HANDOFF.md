# Bàn giao Auto Bang Thiên sang Codex cục bộ Windows

## Quy trình đã chốt
Chỉ sử dụng vật phẩm/Lệnh bài boss ở ô phím 1 và lọc đồ mới. Người dùng tự bật Auto đánh và nhặt của game. Ứng dụng mở/quan sát hành trang bằng Q hoặc F4, ghi nhận đồ trước lượt, gửi phím 1 một lần, chờ thời gian cấu hình, đọc đồ mới: đạt giữ nguyên, không đạt chắc chắn thì vứt. Không rõ phải giữ và dừng báo lỗi. Không đi A/B, không Roll, không cất kho, không gửi Ctrl+F.

## Mã nguồn
Repository: https://github.com/vndark92/Auto
Nhánh: ui-tabs-review-20261028
Bộ nguồn hiện tại nằm trong AutoBangThien_Review.zip, giải nén ra AutoBangThien/.
Không dùng các ZIP AutoPro/BuildKit/BossOnly cũ trong cùng repo.
Entry point: boss_only_app.py. Giao diện: boss_only.html và boss_only.js.
Điều phối: boss_only_flow.py, tái sử dụng thư viện AutoBoss/vision/filter cũ bên trong.
Đầu ra: dist/AutoBangThien.exe. Icon vàng: AutoBangThien.ico.

## Build và kiểm thử
- Windows native, Python 3.12 x64, Tesseract x64. Cần Internet cài dependencies.
- TAO_1_FILE_EXE.bat tạo .venv-bangthien-build; không tái sử dụng .venv-onefile đã sao chép từ nơi khác.
- Khi môi trường build đã cài: .venv-bangthien-build\Scripts\python.exe -m unittest discover -s tests -v
- Có 13 tests tự chứa trong ZIP gọn; bộ kiểm tra rộng ở workspace Linux trước đó chạy 36 tests.
- Build dùng PyInstaller 6.16.0 với .spec để tránh WinError 206.
- collect_all Paddle bỏ nhánh paddle.jit.sot vì người dùng gặp native crash 0xC0000005 khi scanner import nó.
- Kiểm tra import Paddle trước build; log: build-preflight.log, build-onefile.log.
- Chỉ thu thập dependency giao diện Windows bằng hook, không collect_all toàn bộ webview/cv2/pythonnet/win32com.
- Giữ PaddleOCR: nó hỗ trợ nhận diện thuộc tính, không được tự ý bỏ để giảm dung lượng.
- Payload bỏ hình nền/icon cũ, chỉ lấy dependency Python nội bộ, dữ liệu nhận diện, Tesseract executable/DLL/config/license và một bản eng/vie.
- Sau build: build-size-report.json, build-payload-report.json. KIEM_TRA_DUNG_LUONG.bat đọc thành phần EXE mà không chạy game.

## Tình trạng thực tế, không được suy diễn
- Người dùng từng build được EXE khoảng 327,479 KB trước bản tối ưu cuối.
- Chưa đo dung lượng EXE sau bản tối ưu mới. Payload Linux giảm từ 13,979,180 xuống 8,947,533 byte; không phải kích thước EXE.
- Luồng cũ từng lỗi SOURCE_GRID_NOT_VISIBLE_NO_HOTKEY do đòi khung bên phải 6x10 khác yêu cầu hành trang Q/F4.
- Luồng Boss-only mới chưa được xác minh trong game. Không tuyên bố lỗi nhận diện đã được sửa hoàn toàn.
- Thời gian chờ chưa phải nhận diện boss chết. Hành trang giữ mở để theo dõi, cần kiểm tra game có đánh/nhặt được trong trạng thái đó.
- Bản mới có kiểm tra ô trống trước lượt để tránh tác động đồ cũ.
- Windows 10 cá nhân của người dùng đã xác nhận Python 3.12 64-bit và Tesseract 5.5.0. Không áp dụng kết luận này cho VPS.

## Phạm vi thử thực tế
Người dùng trước đó chỉ cho phép kiểm tra giao diện, đọc đồ và bộ lọc trên tài khoản VPS. Chưa có vật phẩm/giới hạn tiêu hao được chỉ định. Không tự chạy 1 lượt, dùng lệnh bài, vứt đồ hoặc chạy liên tục trước khi người dùng xác định vật phẩm thử và cho phép các thao tác đó. Có thể tự build, đọc log và chạy unit tests.

## Việc đầu tiên trong phiên Windows
1. Xác nhận chạy Windows native, đường dẫn nguồn, phiên bản Python, Tesseract; không in biến môi trường chứa bí mật.
2. Đọc tài liệu dự án, kiểm tra thay đổi người dùng; không ghi đè ZIP lên cấu hình đang dùng.
3. Cài/build bằng script mới; phân tích build-size-report.json để đo thư viện thực sự chiếm chỗ.
4. Chạy tests và mở giao diện khi người dùng cho phép; chưa có công cụ desktop thì nói rõ, không tự nhận là đã quan sát/chạy game.
5. Xin ảnh/log hành trang thực tế nếu cần điều chỉnh nhận diện.
