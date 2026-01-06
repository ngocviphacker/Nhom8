# Giải Thích Chỉ Số Lift  
## Cách Nhận Diện Mối Liên Kết Tạo Doanh Thu Thực Sự Từ Dữ Liệu Giao Dịch

## Vấn đề kinh doanh

Trong vai trò **Trưởng bộ phận Marketing** của một doanh nghiệp bán lẻ, bạn đang sở hữu hàng trăm nghìn giao dịch lịch sử và vô số luật kết hợp sản phẩm được sinh ra từ thuật toán Apriori.

- **Luật phổ biến:** Khách mua *Laptop* thường mua *Chuột máy tính* – hợp lý nhưng không mới.
- **Luật gây tò mò:** Khách mua *Bỉm trẻ em* lại hay mua *Bia* – nghe thú vị nhưng cần kiểm chứng.
- **Luật ngách:** Khách mua *Bộ cốc trà xanh* gần như luôn mua thêm *Bộ cốc trà hồng* – hành vi mang tính sưu tập.

Vấn đề đặt ra là:  
**Làm thế nào để xác định đâu là mối quan hệ thực sự có giá trị, đâu chỉ là sự trùng hợp ngẫu nhiên?**

Câu trả lời nằm ở chỉ số **Lift (Độ nâng)**.

---

## Dữ liệu & Phương pháp

### Dataset
- **541.909 giao dịch** từ hệ thống bán lẻ trực tuyến tại Anh.
- **Mục tiêu:** Phát hiện các cặp sản phẩm có mối quan hệ mua chung vượt mức ngẫu nhiên.

### Phân loại luật theo Lift

| Nhóm phân tích | Đặc trưng | Ý nghĩa kinh doanh |
|---|---|---|
| **Nhóm A – Sản phẩm lõi** | Support cao – Lift cao | Doanh thu ổn định, dễ bán kèm |
| **Nhóm B – Cơ hội ngách** | Support thấp – Lift cao | Ít người mua nhưng mua theo bộ |
| **Nhóm C – Nhiễu dữ liệu** | Confidence cao – Lift ≈ 1 | Không nên đầu tư khuyến mãi |

---

## Kết quả phân tích

Sau khi áp dụng thuật toán Apriori với:
- **Support > 1.5%**
- **Lift > 2.0**

Kết quả thu được:

- **Tổng số luật:** 516
- **Nhóm A:** 254 luật
- **Nhóm B:** 257 luật
- **Nhóm C:** 0 luật (đã loại bỏ từ đầu)

> Gần **50% luật giá trị cao thuộc nhóm ngách**, thường bị bỏ qua nếu chỉ nhìn vào doanh số.

---

## So sánh các nhóm

| Chỉ số trung bình | Nhóm A | Nhóm B | Nhận xét |
|---|---|---|---|
| **Lift** | ~27 | ~39 | Nhóm ngách liên kết mạnh hơn |
| **Confidence** | ~77% | ~83% | Khách hàng ngách mua theo bộ |
| **Support** | ~1.8% | ~1.5% | Không chênh lệch nhiều |

---

## Insight & Hành động

### 1. Không bị đánh lừa bởi Confidence cao
- Confidence cao nhưng Lift ≈ 1 thường là do sản phẩm quá phổ biến.
- **Hành động:** Tránh khuyến mãi không cần thiết.

### 2. Giá trị nằm ở phân khúc đuôi dài
- Sản phẩm ngách phản ánh hành vi tiêu dùng có chủ đích.
- **Hành động:** Gợi ý cá nhân hóa qua email hoặc ứng dụng.

### 3. Tối ưu trưng bày sản phẩm lõi
- Các cặp bán chạy nên được đặt gần nhau hoặc bán theo combo.
- **Hành động:** Tăng giá trị đơn hàng trung bình (AOV).

### 4. Lift rất cao thường là bộ sưu tập
- Biến thể màu sắc, mẫu mã thường có Lift > 30.
- **Hành động:** Thiết kế tính năng “Mua trọn bộ”.

### 5. Chiến lược neo giá
- Sản phẩm phổ biến làm mỏ neo cho các sản phẩm liên quan.
- **Hành động:** Điều chỉnh giá thông minh để xả tồn kho.

---

## Case Study tiêu biểu

**Luật kết hợp (Nhóm B):**


- **Lift:** ~39  
- **Confidence:** >83%  
- **Support:** ~1.5%

**Nhận định:**  
Khách hàng gần như không mua lẻ. Bán theo **set sưu tập** mang lại giá trị cao hơn.

---

##  Kết luận

Áp dụng mô hình **3 lớp giá trị**:

1. **Lớp phổ thông:** Tối ưu bán kèm.
2. **Lớp ngách:** Cá nhân hóa, bán theo bộ.
3. **Lớp loại bỏ:** Cắt giảm khuyến mãi dư thừa.

> *Dữ liệu không chỉ cho biết khách mua gì, mà còn cho biết họ muốn mua kèm điều gì.*

---

**Nhóm thực hiện:** Nhóm 8 – CNTT 17-10  
**Công cụ:** Python, Mlxtend, Thuật toán Apriori
