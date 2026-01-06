# 5.3.1 Regression vs ARIMA – Khi Nào Nên Chọn Mô Hình Nào?

## 🎯 Mục tiêu so sánh

Trong chủ đề này, nhóm giữ nguyên pipeline hiện tại và **so sánh hai hướng dự báo đã triển khai**:

- **Regression baseline**  
  (sử dụng time features + lag features, đặc biệt là PM2.5_lag1)
- **ARIMA**  
  (mô hình chuỗi thời gian đơn biến, khai thác cấu trúc tự tương quan)

Việc so sánh được đảm bảo **công bằng tuyệt đối** bằng cách:
- Cùng **trạm đo**: *Aotizhongxin*
- Cùng **mốc chia train/test** theo thời gian (**CUTOFF**)
- Cùng **horizon dự báo**, tập trung phân tích **horizon = 1**

---

## ⚙️ Thiết lập thí nghiệm

- **Regression baseline**:
  - Đặc trưng: hour, day_of_week, PM2.5_lag1, PM2.5_lag2, …
  - Ưu điểm: tận dụng mạnh thông tin ngắn hạn

- **ARIMA**:
  - Mô hình chuỗi thời gian đơn biến
  - Tham số (p, d, q) xác định dựa trên ACF/PACF
  - Có khả năng sinh khoảng tin cậy dự báo

Cả hai mô hình đều được đánh giá trên **tập test giống nhau** bằng:
- **MAE**
- **RMSE**

---

## ❓ Câu hỏi 1: Mô hình nào tốt hơn cho horizon = 1?

### 📊 Kết quả định lượng (tóm tắt)
- Regression baseline đạt **MAE và RMSE thấp hơn** ARIMA ở horizon = 1.
- Sự khác biệt rõ ràng nhất thể hiện ở MAE.

### 🧠 Giải thích
Dự báo **rất ngắn hạn (1 bước)** của PM2.5 bị chi phối mạnh bởi:
- Giá trị ngay trước đó (**PM2.5_lag1**)

Regression baseline:
- Sử dụng trực tiếp PM2.5_lag1 làm feature
- Do đó “bám sát” giá trị thực tế rất tốt nếu feature engineering hợp lý

ARIMA:
- Dự báo dựa trên cấu trúc tự tương quan toàn chuỗi
- Hiệu quả phụ thuộc vào:
  - Quyết định sai phân (d)
  - Độ chính xác của (p, q)

➡ **Kết luận:**  
Với horizon = 1, regression baseline thường có lợi thế vì tận dụng trực tiếp độ trễ gần nhất, trong khi ARIMA có thể tốt nhưng nhạy cảm với cấu trúc mô hình.

---

## ❓ Câu hỏi 2: Mô hình nào ổn định hơn khi xuất hiện spike PM2.5?

### 🔍 Phân tích tình huống spike
Nhóm chọn một đoạn **1–3 ngày** có đỉnh PM2.5 rõ rệt và vẽ:
- Forecast vs Actual của **Regression**
- Forecast vs Actual của **ARIMA**  
(trên cùng một biểu đồ)

### 📈 Quan sát chính
- **Regression baseline**:
  - Phản ứng nhanh hơn với spike
  - Dự báo tăng/giảm mạnh theo PM2.5_lag1
- **ARIMA**:
  - Có xu hướng **mượt hóa (over-smoothing)**
  - Phản ứng chậm hơn với biến động đột ngột

### 📐 Liên hệ MAE vs RMSE
- Trong giai đoạn spike:
  - **RMSE tăng mạnh hơn MAE**
  - Điều này cho thấy mô hình mắc **lỗi lớn tại một số thời điểm**
- ARIMA thường bị phạt nặng hơn bởi RMSE do:
  - Sai số lớn tại đỉnh cực trị

➡ **Kết luận:**  
Regression baseline ổn định hơn khi cần phản ứng nhanh với spike, trong khi ARIMA có xu hướng làm trơn quá mức các biến động ngắn hạn.

---

## ❓ Câu hỏi 3: Nếu triển khai thực tế, nên chọn mô hình nào?

### 🔧 Góc nhìn vận hành hệ thống

| Tiêu chí | Regression baseline | ARIMA |
|---|---|---|
| Mở rộng feature | Rất dễ (thời tiết, giao thông…) | Khó |
| Tốc độ huấn luyện | Nhanh | Chậm hơn |
| Cập nhật mô hình | Linh hoạt | Phức tạp |
| Giải thích mô hình | Trực quan theo feature | Dựa trên (p, d, q) |
| Khoảng tin cậy | Không có sẵn | Có |

### 🧠 Quyết định triển khai
Nếu mục tiêu là:
- **Cảnh báo sớm PM2.5**
- Môi trường **biến động mạnh theo thời tiết**
- Cần mở rộng thêm dữ liệu ngoại sinh

➡ **Regression baseline** là lựa chọn phù hợp hơn.

ARIMA phù hợp khi:
- Cần mô hình chuỗi thời gian “thuần”
- Muốn phân tích cấu trúc tự tương quan
- Cần khoảng tin cậy dự báo

---

## 📌 Tổng kết chủ đề

- **Horizon rất ngắn (h = 1):** Regression baseline thường thắng nhờ lag features.
- **Giai đoạn spike:** Regression phản ứng nhanh hơn, ARIMA dễ bị mượt hóa.
- **Triển khai thực tế:** Regression linh hoạt và dễ vận hành hơn.

> **ARIMA giúp hiểu chuỗi thời gian, Regression giúp phản ứng nhanh với thực tế.**  
> Trong hệ thống cảnh báo sớm, tốc độ và khả năng mở rộng thường quan trọng hơn sự “thuần lý thuyết”.

---
**Nhóm thực hiện:** Nhóm 8 – CNTT 17-10  