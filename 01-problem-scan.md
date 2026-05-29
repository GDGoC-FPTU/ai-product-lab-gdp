# Lab 02 — Problem Scan & Quick Cards

## Thành viên nhóm

| Họ tên | MSSV |
|---|---|
| [Hoàng An Đà Giang] | [2A202600695] |
| [Vũ Đình Phượng]   | [2A202600634] |
| [Nguyễn Hoàng Dương]     | [2A202600849] |

---

## Phase 1 — SCAN: Danh sách bài toán

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **Vin Smart Future (HR)** | AI có thể tốt hơn | Ứng viên chuẩn bị phỏng vấn thiếu nguồn tra cứu tập trung về văn hóa, tư duy chất lượng và hệ sinh thái Vingroup — cần chatbot hỏi đáp tri thức nội bộ giúp ứng viên tự học trước khi vào phỏng vấn. |
| 2 | **Vin Smart Future (R&D)** | Lặp lại | Ban lãnh đạo nhận hàng trăm đề xuất ý tưởng AI từ các công ty thành viên, phải đọc và đánh giá thủ công từng ý tưởng để lọc ra những ý tưởng phù hợp với định hướng chiến lược — tốn nhiều thời gian và thiếu nhất quán. |
| 3 | **Vinschool** | Pain từ người khác | Ban giám hiệu muốn đánh giá chất lượng giảng dạy thực tế của từng tiết học nhưng không có đủ nhân lực dự giờ — cần AI theo dõi tình trạng lớp học (mức độ tập trung của học sinh, tương tác thầy–trò) để đo lường khách quan. |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ sản khoa phát hiện muộn các ca khó sinh (đẻ ngược, nhau tiền đạo, tiền sản giật nặng) do thiếu công cụ cảnh báo sớm từ hồ sơ thai kỳ — dẫn đến chuyển viện gấp sang BVPSTW/BVPS Hà Nội trong tình trạng nguy kịch. |
| 5 | **Xanh SM** | AI có thể tốt hơn | Tài xế Xanh SM trong giờ cao điểm không biết nên di chuyển đến điểm nào để đón khách nhanh nhất — hệ thống chưa có gợi ý điểm đón tiềm năng dựa trên dữ liệu lịch sử và nhu cầu thực tế theo khu vực và khung giờ. |
| 6 | **Xanh SM** | Pain từ người khác | Tài xế bị đánh giá 1–2 sao do cảm xúc tiêu cực của khách vì yếu tố ngoài tầm kiểm soát (tắc đường, đặt nhầm điểm đón), ảnh hưởng trực tiếp đến KPI và thu nhập — hệ thống đánh giá hiện tại không phân biệt được lỗi thực sự của tài xế với đánh giá cảm tính. |

---

## Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### Card #1 — Xanh SM: Phân tích đánh giá sao công bằng cho tài xế *(Chọn Deep-Dive)*

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Hệ thống đánh giá sao Xanh SM không phân biệt    │
│ được lỗi thực sự của tài xế với đánh giá sao cảm tính do   │
│ yếu tố ngoài tầm kiểm soát, gây bất công cho tài xế.       │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ Tài xế (bị trừ thưởng/KPI oan), Quản lý chất lượng         │
│ (xử lý thủ công hàng chục khiếu nại/ngày)                  │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách đánh giá 1–5 sao + comment sau chuyến xe         │
│   → 2. Hệ thống tổng hợp điểm sao trung bình tự động       │
│   → 3. Cuối tháng: hệ thống tính KPI dựa trên điểm sao     │
│   → 4. Tài xế khiếu nại thủ công qua App lên quản lý       │
│   → 5. Quản lý xem xét từng case, quyết định điều chỉnh    │
│                                                             │
│ Bước nào tốn nhất? Bước 5 (⏱ 20-30 phút/case khiếu nại) 🔴 │
│ AI hỗ trợ ở bước nào? Bước 1-2                              │
│ (Phân tích comment + metadata chuyến xe → phân loại nguyên  │
│ nhân → đề xuất điều chỉnh để quản lý duyệt)                │
│                                                             │
│ Metric đo thành công:                                       │
│ Phân loại đúng nguyên nhân 1-2 sao >= 85% trong < 5 giây;  │
│ giảm 70% số case khiếu nại thủ công của tài xế             │
│                                                             │
│ Quick Architecture: [x] LLM Feature                         │
└─────────────────────────────────────────────────────────────┘
```

---

### Card #2 — Vinmec: Phát hiện sớm ca khó sinh để chuyển viện kịp thời

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Bác sĩ sản khoa Vinmec phát hiện muộn các ca     │
│ khó sinh do thiếu công cụ cảnh báo sớm từ hồ sơ thai kỳ.  │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ Sản phụ (rủi ro tính mạng), Bác sĩ sản khoa (áp lực        │
│ quyết định chuyển viện), Gia đình bệnh nhân                 │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ thăm khám định kỳ, ghi chép vào bệnh án        │
│   → 2. Bác sĩ đọc toàn bộ lịch sử hồ sơ để đánh giá rủi ro│
│   → 3. Bác sĩ hội chẩn nếu nghi ngờ ca phức tạp            │
│   → 4. Quyết định chuyển viện (thường xảy ra muộn)         │
│                                                             │
│ Bước nào tốn nhất? Bước 2–3 (⏱ 45-60 phút hội chẩn) 🔴    │
│ AI hỗ trợ ở bước nào? Bước 2                                │
│ (Đọc hồ sơ, tự động flag ca có dấu hiệu rủi ro cao)        │
│                                                             │
│ Metric đo thành công:                                       │
│ Phát hiện >= 90% ca khó sinh trước 37 tuần thai;            │
│ giảm tỉ lệ chuyển viện cấp cứu khẩn cấp xuống < 5%         │
│                                                             │
│ Quick Architecture: [x] LLM Feature (bác sĩ luôn quyết định│
│ cuối cùng — AI chỉ cảnh báo, không chẩn đoán thay bác sĩ)  │
└─────────────────────────────────────────────────────────────┘
```

---

### Card #3 — Xanh SM: Gợi ý điểm đón tiềm năng cho tài xế vào giờ cao điểm

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM vào giờ cao điểm không biết nên   │
│ di chuyển đến đâu để đón khách nhanh nhất, chủ yếu dừng    │
│ chờ theo kinh nghiệm cá nhân.                               │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ Tài xế (lãng phí thời gian chờ không có khách)             │
│ Khách hàng (chờ xe lâu vào giờ cao điểm)                   │
│                                                             │
│ Workflow thủ công hiện tại (3 bước):                        │
│   1. Tài xế giao hàng xong / hết chuyến, tắt app chờ       │
│   → 2. Tự quyết định di chuyển đến điểm quen theo kinh     │
│         nghiệm (sân bay, bệnh viện, trung tâm thương mại)  │
│   → 3. Bật app chờ khách, thường mất 15-30 phút            │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 15-30 phút chờ/chuyến) 🔴  │
│ AI hỗ trợ ở bước nào? Bước 2                                │
│ (Phân tích dữ liệu lịch sử + real-time → gợi ý top 3 điểm) │
│                                                             │
│ Metric đo thành công:                                       │
│ Giảm thời gian chờ trung bình từ 20 phút → dưới 8 phút     │
│                                                             │
│ Quick Architecture: [x] LLM Feature + Rule-based routing    │
└─────────────────────────────────────────────────────────────┘
```

---

## Quyết định lựa chọn bài toán Deep-Dive

Nhóm chọn **Card #1 — Xanh SM: Phân tích đánh giá sao công bằng cho tài xế**.

**Lý do loại bỏ các thẻ khác:**
- **Card #2 (Vinmec):** Bài toán phát hiện ca khó sinh có tác động rất lớn nhưng liên quan trực tiếp đến tính mạng sản phụ — yêu cầu mức độ kiểm soát pháp lý y tế, dữ liệu được ẩn danh hóa chuẩn HIPAA, và pilot lâm sàng có kiểm soát trước khi triển khai. Scope quá rộng cho buổi lab này.
- **Card #3 (Xanh SM pickup prediction):** Gợi ý điểm đón chủ yếu là bài toán ML/geospatial analytics (dự đoán từ dữ liệu tọa độ + thời gian + lịch sử), phù hợp với mô hình gradient boosting hoặc time-series hơn là LLM. Không khai thác được thế mạnh của AI sinh ngôn ngữ.
