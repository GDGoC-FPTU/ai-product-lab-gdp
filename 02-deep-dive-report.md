# Lab 02 — Problem Deep-Dive Report

## Thành viên nhóm

| Họ tên | MSSV | Vai trò |
|---|---|---|
| [Hoàng An Đà Giang] | [2A202600695] | Nhóm trưởng |
| [Vũ Đình Phượng]   | [2A202600634] | Thành viên |
| [Nguyễn Hoàng Dương]     | [2A202600849] | Thành viên |

**Bài toán được chọn:** Xanh SM — Phân tích đánh giá sao công bằng cho tài xế

---

## Phase 3.1 — Current-State Workflow Mapping

*(Xem file `04-workflow-diagram.png` đính kèm)*

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Bước 1     │     │   Bước 2     │     │   Bước 3     │     │   Bước 4     │
│ Khách đánh   │     │ Hệ thống     │     │ Cuối tháng:  │     │ Tài xế nộp  │
│ giá 1–5 sao  │ ──→ │ tổng hợp     │ ──→ │ tính KPI tự  │ ──→ │ khiếu nại   │
│ + comment    │     │ điểm sao tự  │     │ động dựa     │     │ thủ công     │
│ sau chuyến   │     │ động         │     │ trên sao TB  │     │ lên quản lý  │
│              │     │              │     │              │     │              │
│ Ai: Khách    │     │ Ai: System   │     │ Ai: System   │     │ Ai: Tài xế   │
│ ⏱ Ngay lập  │     │ ⏱ Real-time  │     │ ⏱ Tự động    │     │ ⏱ 5 phút     │
│ tức          │     │              │     │              │     │              │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │   Bước 5     │
                                                               │ Quản lý xem  │
                                                               │ xét từng     │
                                                               │ case, quyết  │
                                                               │ định điều    │
                                                               │ chỉnh        │
                                                               │ Ai: Quản lý  │
                                                               │ ⏱ 20-30 phút │
                                                               │ /case 🔴     │
                                                               └──────────────┘

🔴 Bottleneck  |  ~30-50 khiếu nại/ngày × 25 phút/case = 12+ giờ quản lý/ngày
```

**Vấn đề cốt lõi:** Bước 2 không có cơ chế phân biệt đánh giá 1–2 sao do lỗi thực sự của tài xế với đánh giá do yếu tố khách quan (tắc đường, khách đặt nhầm điểm, sự cố ngoài ý muốn). Mọi thứ đều được tính vào KPI một cách máy móc.

---

## Phase 3.2 — Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Tài xế Xanh SM (đối tượng bị ảnh hưởng trực tiếp bởi điểm sao) và Đội quản lý chất lượng tài xế Xanh SM (phải xử lý thủ công toàn bộ khiếu nại). Ước tính 5.000+ tài xế đang hoạt động tại Hà Nội và TP.HCM. |
| **2. Current Workflow** | Sau mỗi chuyến xe, khách hàng đánh giá 1–5 sao kèm comment tùy chọn. Hệ thống tổng hợp điểm sao trung bình tự động và dùng con số này để tính KPI tháng của tài xế. Tài xế bất mãn có thể khiếu nại qua App, quản lý xem xét thủ công từng case rồi quyết định giữ nguyên hay điều chỉnh. Toàn bộ bước phân tích nguyên nhân là thủ công, không có dữ liệu hỗ trợ. |
| **3. Bottleneck** | Bước 5: Quản lý phải đọc từng khiếu nại, đối chiếu lịch sử chuyến xe, xem xét comment của khách và bối cảnh (giờ cao điểm, sự cố giao thông...) để ra quyết định — mất 20–30 phút/case. Không có công cụ nào hỗ trợ phân loại tự động nguyên nhân đánh giá thấp. Ước tính ~15–20% tổng số đánh giá 1–2 sao là do yếu tố ngoài tầm kiểm soát của tài xế, nhưng tất cả đều được tính vào KPI giống nhau. |
| **4. Business Impact** | Xanh SM có ~5.000 tài xế tại Hà Nội, mỗi tháng ghi nhận ~3.000 khiếu nại liên quan đến điểm sao. Với 25 phút/case × 30–50 case/ngày = **12+ giờ nhân công quản lý/ngày** bị tiêu tốn. Quan trọng hơn: tài xế bị trừ KPI oan → stress → tỉ lệ bỏ việc tăng. Chi phí tuyển dụng và đào tạo một tài xế mới ước tính 3–5 triệu VNĐ/người, trong khi Xanh SM đang mở rộng đội xe nhanh chóng. |
| **5. Success Metric** | 1. Tỉ lệ phân loại đúng nguyên nhân đánh giá 1–2 sao (lỗi tài xế vs yếu tố khách quan) đạt **≥ 85%** trong vòng **< 5 giây/đánh giá** (đo qua sampling 200 case/tháng có human review). 2. Giảm **70%** số case khiếu nại thủ công tài xế phải nộp (AI xử lý trước, chỉ escalate lên quản lý khi cần). 3. Điểm hài lòng tài xế trong khảo sát nội bộ tăng từ baseline hiện tại lên **≥ 4.0/5.0** sau 3 tháng. |
| **6. Operational Boundary** | **AI được phép:** Phân tích nội dung comment của khách + metadata chuyến xe (thời gian, lộ trình GPS, tình trạng giao thông tại thời điểm đó, thời tiết) để phân loại nguyên nhân và đề xuất điều chỉnh điểm KPI dạng NHÁP có lý giải rõ ràng. **TUYỆT ĐỐI CẤM:** (a) AI không được tự thay đổi điểm sao mà khách đã đánh giá — đây là dữ liệu của khách hàng, không thể sửa tự động; (b) AI không được tự điều chỉnh KPI tài xế khi chưa có quản lý phê duyệt (bắt buộc Human-in-the-loop); (c) Không phân tích âm thanh hay hình ảnh trong xe (vi phạm quyền riêng tư). |

---

## Phase 3.3 — AI Fit & Future-State Flow

### AI-Fit Matrix

| Tiêu chí | Rule / State-Machine | **LLM Feature** ✅ | Agentic Loop |
|---|---|---|---|
| Phân tích comment tiếng Việt đa dạng | Không xử lý được | **Tốt nhất** | Overkill |
| Đối chiếu với metadata chuyến xe | Phù hợp (rule cứng) | **Tốt nhất** | Phù hợp |
| Viết lý giải điều chỉnh cho quản lý | Không linh hoạt | **Tốt nhất** | Overkill |
| Rủi ro khi sai (ảnh hưởng thu nhập tài xế) | Cao nếu rule sai | **Trung bình** (có HITL) | Cao (tự trị) |

**Quyết định: LLM Feature** — Comment của khách viết bằng tiếng Việt tự nhiên, đa dạng cách diễn đạt, không thể dùng rule cứng để phân loại. LLM có thể hiểu ngữ cảnh và đối chiếu với metadata chuyến xe để đưa ra đề xuất có lý giải. Bắt buộc HITL vì ảnh hưởng trực tiếp đến thu nhập tài xế.

### Future-State Flow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Bước 1     │     │   Bước 2     │     │   Bước 3     │     │   Bước 4     │
│ Khách đánh   │     │ 🔵 AI phân   │     │ 🔵 AI tạo   │     │ 🟢 Quản lý  │
│ giá 1–5 sao  │ ──→ │ tích comment │ ──→ │ đề xuất điều │ ──→ │ review và   │
│ + comment    │     │ + metadata   │     │ chỉnh KPI    │     │ duyệt 1-click│
│              │     │ chuyến xe    │     │ có lý giải   │     │ (chỉ ~15%   │
│              │     │              │     │ [DRAFT_ONLY] │     │ tổng số)    │
│ ⏱ Ngay lập  │     │ ⏱ < 5 giây   │     │ ⏱ < 5 giây   │     │ ⏱ 2 phút    │
│ tức          │     │              │     │              │     │ /case       │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
        │                   │                    │
        │            <= 3 sao?                   │              ↩️ Fallback:
        │                   │                    │              AI không tự
        │            ┌──────┘                    │              tin (comment
        │            ▼                           │              mơ hồ) →
        │     80-85% case:                       │              escalate thẳng
        │     AI tự xử lý,                       │              lên quản lý
        │     không cần                          │              xem xét thủ
        │     quản lý duyệt                      │              công như cũ
        │     (đánh giá rõ                       │
        │     là lỗi tài xế                      │
        │     hoặc rõ là                         │
        │     khách quan)                        │
        └────────────────────────────────────────┘

Tổng thời gian quản lý/ngày: giảm từ 12 giờ → ~2 giờ (review 15% case AI flag)
```

**Điểm HITL bắt buộc:** Quản lý phải xác nhận mọi điều chỉnh KPI — AI không tự thay đổi dữ liệu.

**Fallback strategy:** Khi AI không tự tin phân loại (confidence < threshold), tự động đẩy lên hàng đợi quản lý xem xét thủ công và ghi log để cải thiện model.

---

## Phase 5 — Evaluate & Decision

### AI Readiness Checklist

- [x] Dữ liệu sẵn sàng: Xanh SM có log đánh giá sao + comment + toàn bộ metadata chuyến xe (GPS, thời gian, lộ trình) theo thời gian thực. Có thể bắt đầu labeling 1.000 case lịch sử để tạo tập validation.
- [x] Rủi ro kiểm soát được: AI chỉ đề xuất — quản lý luôn là người quyết định cuối cùng về điều chỉnh KPI. Worst-case khi AI sai: quản lý bác bỏ đề xuất, quy trình tiếp tục thủ công như hiện tại.
- [x] Stakeholders sẵn sàng: Quản lý chất lượng tài xế đang quá tải với khiếu nại thủ công; tài xế đang bức xúc với hệ thống đánh giá hiện tại. Cả hai bên đều có động lực cao để thay đổi.

### Quyết định cuối cùng

**[x] GO — Bắt đầu xây dựng Prototype với scope hẹp**

**Justification:**

1. **Dữ liệu đủ và sẵn sàng:** Log đánh giá + metadata chuyến xe đã tồn tại trong hệ thống, không cần đầu tư thu thập dữ liệu mới. Chỉ cần labeling ~1.000 case lịch sử để đo độ chính xác baseline.

2. **Rủi ro thấp nhờ HITL chặt:** Vì AI không tự thay đổi KPI, worst-case là AI đề xuất sai và quản lý bác bỏ — không tệ hơn baseline. Không có rủi ro hệ thống tự động gây hại.

3. **ROI rõ ràng và đo được:**
   - Tiết kiệm: 12 giờ quản lý/ngày × 22 ngày/tháng × chi phí nhân sự ≈ tiết kiệm đáng kể mỗi tháng.
   - Chi phí API: ~3.000 đánh giá 1–2 sao/ngày × $0.0003/request = ~$0.9/ngày (~28.000 VNĐ/ngày).
   - ROI trong vòng 2 tuần vận hành.

4. **Pilot an toàn:** Triển khai thí điểm với 500 tài xế tại Hà Nội trong 4 tuần, đo tỉ lệ quản lý đồng ý với đề xuất AI trước khi scale toàn quốc.
