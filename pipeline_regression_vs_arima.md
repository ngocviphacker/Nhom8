
```mermaid
flowchart LR
    A[Dữ liệu PM2.5<br/>Trạm Aotizhongxin] --> B[EDA<br/>Xu hướng · Mùa vụ · Spike]

    B --> C1[Tạo đặc trưng<br/>Lag · Time features]
    B --> C2[Chuỗi PM2.5 đơn biến<br/>Sai phân nếu cần]

    C1 --> D1[Regression Baseline]
    C2 --> D2[ARIMA p-d-q]

    D1 --> E1[Dự báo ngắn hạn<br/>Horizon = 1]
    D2 --> E2[Dự báo ngắn hạn<br/>Horizon = 1]

    E1 --> F[Đánh giá<br/>MAE · RMSE]
    E2 --> F