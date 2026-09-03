# Memo quyết định — P-047 Safety Monitoring

**Ngày:** 03/09/2026

**Revision bằng chứng:** P-047 `c929e50`; automated evidence gốc `071c575`

**Quyết định:** SỬA trước khi pilot, chưa rollout nhà máy

## 1. Vấn đề và nguyên nhân gốc

P-047 giảm gánh nặng quan sát thủ công bằng Computer Vision phát hiện thiếu PPE/xâm nhập vùng cấm, cảnh báo realtime và HITL. Điểm nghẽn không còn là “chưa có công cụ” mà là **chưa có bằng chứng đủ mạnh để tin cậy công cụ trong workflow an toàn thật**.

- Gartner-Lite: readiness dữ liệu/model chưa đạt vì US-13, provenance/license, ground truth và bốn-camera capacity vẫn là open gate.
- ADKAR: Ability/Reinforcement ngoài môi trường phát triển chưa được chứng minh; chưa có baseline time-to-review, escalation hoặc alert usefulness từ người vận hành thật.

## 2. Framework và bằng chứng

- **Mollick:** AI phát hiện/gợi ý; Safety Manager kiểm tra và ra quyết định; Area Manager chịu trách nhiệm xử lý; Governance quyết định rollout.
- **Bằng chứng chức năng:** 7/7 manual case PASS; 11/11 focused backend checks PASS trong 3,03 giây.
- **Quality evidence:** frontend 127/127, root 33/33, backend/vision 304 pass và 14 fail đã biết.
- **Operational evidence:** log phát triển có 1.544 inference completed, 430 failed, 834 dropped; success proxy 78,2%; P95 inference khoảng 1.306 ms.
- **Thiếu bằng chứng sản phẩm:** local DB có 0 persisted safety event, vì vậy baseline alert precision, false-alarm rate, critical recall và review SLA là **chưa đo**, không phải 0%.
- **Privacy evidence:** MAN-04 và kiểm thử fail-closed đã pass, nhưng 1 test không đủ để tuyên bố baseline privacy 100% trên dữ liệu pilot.

## 3. Thay đổi từ v1 sang v2 sau kiểm tra chéo

1. **Bỏ activity metric làm đích:** v1 dùng số inference/event để biểu diễn adoption. V2 dùng alert usefulness, critical recall và time-to-review; số request/frame chỉ còn là signal vận hành.
2. **Tách test evidence khỏi pilot evidence:** v1 dễ diễn giải 7/7 manual PASS là sẵn sàng triển khai. V2 ghi rõ test chức năng không thay thế US-13, dữ liệu nhà máy và soak test.
3. **Thêm stop gate và owner:** v2 dừng rollout khi privacy fail, critical recall dưới 95%, alert precision dưới 85%, P95 vượt ngưỡng hoặc còn Sev-1/2 chưa xử lý.

## 4. Quyết định

**SỬA.** Tiếp tục hoàn thiện và chuẩn bị pilot hẹp, nhưng không triển khai giám sát nhà máy thật ở trạng thái hiện tại. Lý do: luồng chức năng và kiểm soát privacy đã có bằng chứng tốt, trong khi chất lượng model, giá trị nghiệp vụ và năng lực vận hành thực tế chưa có baseline đáng tin.

## 5. Bước tiếp theo theo owner

| Owner | Việc cần làm | Hạn | Gate |
|---|---|---|---|
| Vision Lead | Chốt provenance/license, ground truth và chạy US-13 | Ngày 30 | Recall critical ≥95%, alert precision ≥85% |
| QA Lead | Sửa/phân loại 14 failure và chạy browser UAT E2E | Ngày 30 | Không còn blocker Sev-1/2 |
| Product Owner | Ghi timestamp review/escalation và outcome HITL | Ngày 30 | Có baseline từ ≥100 event hoặc ≥2 tuần pilot |
| Safety Manager | Pilot một khu vực/một ca và phản hồi false alarm | Ngày 60 | ≥90% event được review trong SLA |
| AI Governance Lead | Soak bốn camera, privacy audit và họp gate | Ngày 90 | Quyết định tiếp tục/sửa/dừng có bằng chứng |

Nếu redaction thất bại, hệ thống phải tiếp tục fail-closed: không tạo event và không lưu snapshot. Không tự động dừng máy hoặc bỏ HITL trong phạm vi hiện tại.
