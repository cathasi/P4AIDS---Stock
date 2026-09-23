# Stock Market EDA — VN & US

Dự án cung cấp một notebook có thể chạy tuần tự để kiểm tra chất lượng, làm sạch, EDA sáu nhóm chỉ tiêu, phân tích chân trời `T = [1, 5, 10, 20, 30]`, tạo insight theo quy tắc và xuất báo cáo Excel cho 30 mã VN cùng 20 mã US.

## Cấu trúc

- `Raw Data/VN/`, `Raw Data/US/`: dữ liệu gốc, notebook chỉ đọc và không sửa.
- `notebooks/Stock_EDA.ipynb`: notebook chính, tự chứa pipeline và kiểm thử.
- `outputs/single/`: báo cáo chi tiết mã được chọn.
- `outputs/batch/`: workbook tổng hợp tất cả mã.
- `outputs/charts/`: ảnh biểu đồ được dùng lại trong Excel.

## Cài đặt và chạy

Từ thư mục gốc dự án:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m jupyter nbconvert --to notebook --execute --inplace notebooks\Stock_EDA.ipynb --ExecutePreprocessor.timeout=1200
```

Cũng có thể mở notebook trong Jupyter/VS Code và chọn **Run All**. Notebook tự nhận diện thư mục gốc khi kernel bắt đầu từ thư mục gốc hoặc `notebooks/`.

## Cấu hình chính

Sửa dictionary `CONFIG` ở đầu notebook:

- `RUN_MODE`: `single`, `batch` hoặc `both`.
- `SELECTED_MARKET`, `SELECTED_TICKER`: mã dùng cho báo cáo single.
- `TRAIN_END`, `VALIDATION_END`: biên thời gian; nhãn vượt biên được purge theo từng T.
- `FULL_SAMPLE_DESCRIPTIVE=False`: mặc định chỉ dùng Train cho EDA quyết định. Nếu bật, kết quả toàn mẫu chỉ có ý nghĩa mô tả.
- Chính sách loại giá/OHLC sai và toàn bộ dòng `Volume=0` được cấu hình riêng, luôn có log kiểm toán. Việc loại `Volume=0` là quyết định chất lượng dữ liệu, không phải kết luận rằng mọi dòng đó là ngày sàn đóng cửa.

## Lưu ý phương pháp

- US dùng lịch XNYS từ `exchange_calendars`.
- Chưa có lịch HOSE chính thức trong dependency: VN dùng kiểm tra ngày làm việc bảo thủ. Khoảng chân trời đi qua ngày làm việc bị thiếu được loại và báo là chưa xác minh, không được tự gán là ngày lễ.
- Lợi nhuận và nhãn chỉ dùng `Adj Close`. Dữ liệu điều chỉnh hồi cứu không hoàn toàn point-in-time.
- Lợi nhuận nhiều phiên chồng lấn có phụ thuộc thống kê. Notebook không chọn T tối ưu; việc đó phải dựa trên ML validation và test sau này.
- Một CSV lỗi không làm dừng batch; lỗi được ghi ở `Processing_Log`.

## Đầu ra mặc định

- `outputs/single/VN_FPT_EDA.xlsx`
- `outputs/batch/All_Stocks_EDA.xlsx`

Cuối notebook có bảng PASS/FAIL chạy thật cho công thức target, NaN cuối chuỗi, kiểm soát gap/session, chống leakage, tính hợp lệ worksheet, tính nhất quán single/batch và khả năng mở workbook.
