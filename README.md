# Bảng chỉ số HRV & Công thức tính toán

| STT | Chỉ số | Thuật toán & Công thức lõi | Ý nghĩa chuyên môn |
|-----|--------|---------------------------|-------------------|
| **01** | **BPM (Pulse)** | `60,000 / Median(RR_Intervals)` | Tốc độ tim đập cơ bản, dùng số trung vị để triệt tiêu nhiễu. |
| **02** | **RMSSD** | `sqrt(mean(diff(RR_Intervals)^2))` | Chỉ số vàng về độ biến thiên nhịp tim (HRV) ngắn hạn. |
| **03** | **SDNN** | `Standard Deviation of RR Intervals` | Độ lệch chuẩn nhịp tim, phản ánh tổng năng lượng thần kinh. |
| **04** | **HRV Score** | `Logarithmic Normalization of RMSSD` | Chuyển đổi dữ liệu miligiây sang thang điểm 0-100 dễ hiểu. |
| **05** | **Stress** | Hàm Sigmoid: `1 / (1 + exp(1.2 * (HRV - 0.8)))` | Phân tích mức độ căng thẳng dựa trên nhịp tim và HRV. |
| **06** | **Energy** | `Weighted Score (HRV, BPM Baseline, Stability)` | Đánh giá năng lượng cơ thể (khả năng phục hồi thần kinh). |
| **07** | **Respiratory** | `Autocorrelation Function (ACF)` | Trích xuất nhịp thở từ hiện tượng tim đập nhanh khi hít vào. |
| **08** | **pNN50** | `Count(ΔRR > 50ms) / Total_Intervals * 100` | Phần trăm khoảng RR chênh lệch >50ms, đo hoạt động phó giao cảm. |
| **09** | **CoV** | `SDNN / Average(RR_Intervals)` | Hệ số biến thiên, đánh giá độ ổn định tổng thể phiên đo. |
| **10** | **Heart Score** | `(0.7 * HRV_Score) + (0.3 * (100 - BPM / 2))` | Điểm sức khỏe tim mạch tổng quát của người dùng. |
| **11** | **Interaction** | `Proxy Logic (pNN50, CoV, HRV)` | Đánh giá mức độ phối hợp giữa hệ Tim mạch - Phổi - Não. |

---

## Ghi chú kỹ thuật

### 🔬 Nhóm chỉ số cơ bản (01-04)
- **BPM, RMSSD, SDNN**: Chỉ số nền tảng được tính trực tiếp từ RR intervals
- **HRV Score**: Chuẩn hóa RMSSD để dễ trình bày cho người dùng

### 🧠 Nhóm chỉ số phân tích cao cấp (05-07)
- **Stress**: Sử dụng hàm phi tuyến (Sigmoid) để phản ánh đúng cảm nhận sinh lý
- **Energy**: Kết hợp đa chỉ số với trọng số khác nhau
- **Respiratory**: Kỹ thuật xử lý tín hiệu nâng cao (ACF)

### 📊 Nhóm chỉ số đánh giá tổng thể (08-11)
- **pNN50, CoV**: Chỉ số bổ trợ đánh giá độ tin cậy phép đo
- **Heart Score**: Điểm tổng hợp cho UX
- **Interaction**: Phân tích mối liên hệ đa hệ thống

---

## Công thức chi tiết

### RMSSD (Root Mean Square of Successive Differences)
```
1. Tính sai khác liên tiếp: diff = [RR[i+1] - RR[i] for i in range(n-1)]
2. Bình phương: squared = [d^2 for d in diff]
3. Trung bình: mean_squared = sum(squared) / len(squared)
4. Căn bậc 2: RMSSD = sqrt(mean_squared)
```

### Stress Index (Sigmoid Function)
```
HRV_normalized = RMSSD / 100  # Chuẩn hóa về [0, 1]
Stress = 1 / (1 + e^(1.2 * (HRV_normalized - 0.8)))

Giải thích:
- HRV cao (>0.8) → Stress thấp (<0.3)
- HRV thấp (<0.5) → Stress cao (>0.7)
- Hàm Sigmoid tạo chuyển tiếp mượt mà
```

### Respiratory Rate (ACF Method)
```
1. Lọc tín hiệu PPG ở băng tần thở (0.15-0.4 Hz)
2. Tính Autocorrelation: ACF[lag] = correlation(signal, signal_shifted_by_lag)
3. Tìm đỉnh đầu tiên trong ACF (chu kỳ thở)
4. Chuyển đổi: Respiratory_Rate = 60 / period_in_seconds
```
