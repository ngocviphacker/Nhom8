# 5.3.1 Regression vs ARIMA – Khi Nào Nên Chọn Mô Hình Nào?

## Phân tích pipeline Regression vs ARIMA 

Sơ đồ pipeline mô tả quá trình so sánh hai hướng dự báo PM2.5 là **Regression baseline** và **ARIMA** trong cùng một điều kiện thí nghiệm, nhằm đảm bảo tính công bằng và khả năng đánh giá trực tiếp hiệu quả của từng mô hình.

### 1. Dữ liệu đầu vào và EDA

Pipeline bắt đầu từ **dữ liệu PM2.5 của trạm Aotizhongxin**, được biểu diễn dưới dạng chuỗi thời gian.  
Bước **EDA (Exploratory Data Analysis)** được thực hiện nhằm:

- Xác định **xu hướng dài hạn** của PM2.5 theo thời gian.
- Nhận diện **tính mùa vụ** (theo giờ trong ngày, ngày trong tuần, theo tháng).
- Phát hiện các **spike** – những giai đoạn PM2.5 tăng đột biến trong thời gian ngắn (1–3 ngày).

Kết quả EDA là cơ sở để thiết kế đặc trưng cho mô hình hồi quy và quyết định việc **sai phân** trong mô hình ARIMA.

---

### 2. Hai nhánh mô hình hóa theo sơ đồ

Sau EDA, pipeline được chia thành hai nhánh xử lý song song.

#### Nhánh Regression baseline (B → C1 → D1)

Ở nhánh này, dữ liệu được chuyển sang bước **tạo đặc trưng**, bao gồm:

- **Lag features**: PM2.5_lag1, PM2.5_lag2,… nhằm nắm bắt sự phụ thuộc ngắn hạn.
- **Time features**: giờ, ngày, tháng,… giúp mô hình học được quy luật theo thời gian.

Regression baseline học mối quan hệ trực tiếp giữa PM2.5 hiện tại và các đặc trưng này. Đặc biệt, trong dự báo rất ngắn hạn, **PM2.5_lag1** thường là biến có sức ảnh hưởng lớn nhất.

#### Nhánh ARIMA (B → C2 → D2)

Ở nhánh ARIMA, dữ liệu được giữ dưới dạng **chuỗi PM2.5 đơn biến**, không bổ sung đặc trưng bên ngoài.

Chuỗi được kiểm tra tính dừng; nếu cần, áp dụng **sai phân (d)** để loại bỏ xu hướng. Mô hình ARIMA được xác định bởi bộ tham số **p–d–q**, phản ánh cấu trúc tự tương quan của chuỗi thời gian.

ARIMA tận dụng thông tin nội tại của chuỗi PM2.5, thay vì phụ thuộc vào feature engineering như regression.

---

### 3. Dự báo ngắn hạn (Horizon = 1)

Cả hai mô hình đều được sử dụng để **dự báo ngắn hạn với horizon = 1**, đảm bảo:

- Cùng mốc chia train/test theo **CUTOFF**.
- Không bị ảnh hưởng bởi sai số tích lũy nhiều bước.

Ở horizon = 1:
- Regression baseline thường **bám sát giá trị thực** nhờ sử dụng PM2.5_lag1.
- ARIMA có thể cho kết quả tốt trong giai đoạn ổn định nhưng dễ bị làm mượt khi dữ liệu biến động mạnh.

---

### 4. Đánh giá bằng MAE và RMSE

Kết quả dự báo của hai mô hình được đánh giá bằng **MAE** và **RMSE**:

- **MAE** phản ánh sai lệch trung bình, ít nhạy với một vài điểm sai lớn.
- **RMSE** phạt mạnh các sai số lớn, đặc biệt nhạy với các spike PM2.5.

Sự khác biệt giữa hai chỉ số này giúp đánh giá rõ hơn khả năng phản ứng của mô hình trong các giai đoạn biến động.

---

## Trả lời các câu hỏi theo yêu cầu

### Câu 1: Mô hình nào tốt hơn cho horizon = 1?

Dựa trên MAE và RMSE, **Regression baseline** thường cho kết quả tốt hơn hoặc ít nhất là ổn định hơn ARIMA ở horizon = 1.

Nguyên nhân là dự báo rất ngắn hạn của PM2.5 bị chi phối mạnh bởi **giá trị trễ gần nhất (PM2.5_lag1)**. Regression baseline khai thác trực tiếp đặc trưng này, nên nếu feature engineering được thực hiện đúng, mô hình sẽ bám sát dữ liệu thực tế.  
ARIMA cũng có thể đạt kết quả tốt, nhưng phụ thuộc nhiều vào cấu trúc tự tương quan và quyết định sai phân, khiến hiệu quả không luôn ổn định.

**Kết luận:** Với horizon = 1, regression baseline là lựa chọn hiệu quả hơn trong đa số trường hợp.

---

### Câu 2: Mô hình nào ổn hơn khi xuất hiện spike PM2.5?

Khi chọn một đoạn thời gian có spike PM2.5 rõ rệt (1–3 ngày) và vẽ **Forecast vs Actual** cho cả hai mô hình:

- Regression baseline phản ứng **nhanh hơn** với spike, dự báo tăng/giảm gần thời điểm thực tế.
- ARIMA có xu hướng **làm mượt**, dự báo đỉnh thấp hơn hoặc xuất hiện trễ hơn.

Trong các giai đoạn này, **RMSE của ARIMA tăng mạnh** do sai số lớn tại một vài thời điểm spike, trong khi MAE tăng ít hơn. Regression baseline kiểm soát RMSE tốt hơn nhờ giảm sai lệch tại các đỉnh.

**Kết luận:** Regression baseline ổn định hơn ARIMA khi dữ liệu có spike PM2.5.

---

### Câu 3: Nếu triển khai thực tế, nên chọn mô hình nào và vì sao?

Trong bối cảnh triển khai hệ thống cảnh báo sớm chất lượng không khí:

- Regression baseline:
  - Dễ mở rộng khi thêm đặc trưng thời tiết.
  - Cập nhật và huấn luyện nhanh.
  - Phản ứng tốt với biến động ngắn hạn.

- ARIMA:
  - Dễ giải thích thông qua tham số p–d–q.
  - Có thể cung cấp khoảng tin cậy cho dự báo.
  - Phù hợp cho phân tích cấu trúc chuỗi.

Nếu mục tiêu là **cảnh báo sớm trong điều kiện thời tiết biến động mạnh**, regression baseline là lựa chọn phù hợp hơn cho vận hành thực tế, trong khi ARIMA được sử dụng như mô hình tham chiếu và phân tích bổ trợ.

---

## Kết luận chung

Dựa trên pipeline và kết quả so sánh:
- Regression baseline vượt trội trong dự báo rất ngắn hạn (horizon = 1).
- Regression baseline phản ứng tốt hơn với spike PM2.5 và kiểm soát RMSE hiệu quả hơn.
- Trong triển khai thực tế, regression baseline là lựa chọn ưu tiên, còn ARIMA đóng vai trò mô hình giải thích và tham chiếu.

Thông điệp chính:  
**Trong bài toán dự báo ngắn hạn với dữ liệu biến động mạnh, mô hình đơn giản nhưng khai thác đúng đặc trưng thường hiệu quả hơn mô hình chuỗi thời gian thuần túy.**

**Nhóm thực hiện:** Nhóm 8 – CNTT 17-10  