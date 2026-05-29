# Lab 02 — AI Log & Reflection (Cá nhân)

**Họ tên:** [Nguyễn Văn A]
**MSSV:** [SE000001]
**Ngày:** 29/05/2026

---

## 1. AI đã giúp tôi những gì trong buổi lab?

**Brainstorm bài toán (Phase 1):**
Tôi dùng prompt *"Tôi là AI Engineer tại Vin Smart Future. Tôi đang tìm kiếm pain point vận hành cho mảng Xanh SM. Gợi ý 5 quy trình nghiệp vụ thủ công gây rò rỉ hiệu suất kèm con số ước tính."*

AI trả về 6 gợi ý có số liệu cụ thể. Nhóm phân công mỗi thành viên khai thác một tập ý tưởng riêng:

| Ý tưởng | Thành viên phụ trách |
|---|---|
| #1, #6 | Vũ Đình Phượng |
| Nhóm trưởng, review workflow | Vũ Đình Phượng |
| #2, #3 | Hoàng An Đà Giang |
| Triển khai text & code | Hoàng An Đà Giang |
| #4, #5 | Nguyễn Hoàng Dương |
| Review tổng thể | Nguyễn Hoàng Dương |

Hữu ích nhất là ý tưởng về sự cố pin taxi điện (thuộc phần Vũ Đình Phượng phụ trách) — AI ước tính 15–20 phút/lượt xử lý thủ công, con số này khớp với thực tế nhóm tìm hiểu thêm. Việc phân chia ý tưởng theo thành viên giúp mỗi người đi sâu vào một góc độ riêng, tránh trùng lặp và tổng hợp được phổ pain point rộng hơn.

**Stress-test thẻ bài toán (Phase 2):**
Tôi dán nội dung Card #1 vào AI và hỏi: *"Đóng vai CFO khắt khe, chỉ ra 3 điểm yếu."* AI phản biện rất sắc:
- *"Tại sao không dùng rule-based: IF pin < X% THEN gửi template cố định?"*
- *"Con số 80 sự cố/ngày từ đâu? Có thể kiểm chứng không?"*
- *"Dispatcher có thực sự dùng App hay vẫn gọi điện?"*

Những câu hỏi này buộc tôi phải làm rõ lý do chọn LLM thay vì rule — đó là vì tin nhắn chỉ dẫn cần linh hoạt theo ngữ cảnh (loại xe, địa hình, giờ cao điểm), không thể template cứng.

**Viết Problem Statement (Phase 3.2):**
AI giúp tôi diễn đạt Business Impact bằng phép tính cụ thể (80 lượt × 15 phút = 20 giờ/ngày) thay vì chỉ nói chung chung "tốn nhiều thời gian".

---

## 2. AI đã sai / hallucinate ở đâu? Tôi đã sửa lại thế nào?

**Hallucination về số liệu kỹ thuật:**
Khi hỏi về loại cổng sạc của VF5 và VF8, AI tự tin trả lời *"VF5 dùng CHAdeMO, VF8 dùng CCS2."* — **Sai hoàn toàn.** VinFast sử dụng chuẩn GBT (tiêu chuẩn Trung Quốc) cho phần lớn mẫu xe tại thị trường Việt Nam, không phải CHAdeMO. Tôi phải tự tra cứu tài liệu kỹ thuật của VinFast để xác nhận lại.

**Bài học:** Không bao giờ tin số liệu kỹ thuật đặc thù từ AI mà không kiểm tra nguồn — đặc biệt với sản phẩm của doanh nghiệp Việt Nam mà AI ít có dữ liệu training.

**AI đề xuất kiến trúc quá phức tạp:**
Khi tôi hỏi kiến trúc giải pháp, AI gợi ý dùng *"Multi-Agent: Agent A lấy GPS, Agent B query trạm sạc, Agent C soạn tin nhắn, Agent D gửi đi tự động."* — **Overkill và nguy hiểm.** Việc Agent D tự gửi tin nhắn đến tài xế mà không có dispatcher duyệt vi phạm Operational Boundary đã xác định.

Tôi đã sửa lại: Giữ kiến trúc LLM Feature đơn giản, bắt buộc dispatcher click duyệt trước khi gửi (HITL). Giải thích với AI tại sao Agentic Loop không phù hợp ở đây — AI sau đó đồng ý và giải thích ngược lại đúng hơn.

**AI viết SYSTEM_PROMPT quá dài và mơ hồ:**
Draft đầu tiên của AI dài 400 từ với nhiều câu mô tả vai trò kiểu *"Bạn là một trợ lý thông minh..."* — Không hiệu quả cho việc enforce boundary. Tôi đã cắt bỏ phần mô tả chung, giữ lại 3 rule cứng ngắn gọn với hành động cụ thể (PHẢI làm gì, CẤM làm gì, format output ra sao).

---

## 3. Nhận xét tổng thể

AI là công cụ brainstorm và drafting tốt, nhưng **tôi phải là người kiểm soát chất lượng.** Nếu tôi chấp nhận kết quả AI mà không kiểm tra lại:
- Số liệu kỹ thuật về cổng sạc sẽ sai → Bài toán mất tính tin cậy.
- Kiến trúc Multi-Agent sẽ tạo ra hệ thống nguy hiểm (tự động gửi lệnh không qua duyệt).
- SYSTEM_PROMPT dài dòng sẽ không enforce được boundary khi test adversarial.

**Điều tôi học được:** Dùng AI như một "junior teammate cần được review" — không phải như một oracle. Đặt câu hỏi phản biện lại kết quả AI giúp tôi hiểu sâu hơn chính bài toán của mình.
