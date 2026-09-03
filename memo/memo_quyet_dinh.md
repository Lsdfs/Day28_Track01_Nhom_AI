# Memo quyết định — Trợ lý AI tra cứu tài liệu nội bộ

**Ngày:** 03/09/2026  
**Phạm vi:** nhân viên vận hành mới; bốn bước tra cứu nội bộ  
**Trạng thái:** SỬA, sau đó tiếp tục pilot có kiểm soát

## 1. Vấn đề và nguyên nhân gốc

Việc cấp công cụ chưa tạo ra adoption: người dùng vẫn tìm thủ công/hỏi đồng nghiệp vì câu trả lời thiếu trích nguồn và không rõ độ mới. Gartner-Lite cho thấy điểm nghẽn readiness/governance; ADKAR cho thấy Ability và Reinforcement còn yếu. Bằng chứng hiện có là pilot mô phỏng 100 tác vụ, không phải dữ liệu doanh nghiệp thật: completion 42%, QA nguồn 58%, SLA 55%.

## 2. Framework và bằng chứng

- Gartner-Lite: chỉ mở rộng khi nguồn, owner, SLA và cổng kiểm soát đã sẵn sàng.
- Molllick: người dùng chịu trách nhiệm kết quả; AI truy xuất/tóm tắt; owner kiểm tra; automation chỉ phân loại và chuyển tuyến.
- ADKAR: hướng dẫn kiểm tra citation tại điểm sử dụng, checklist ngắn và phản hồi lỗi trong workflow.
- Bằng chứng đo: log theo `task_id`, kết quả “đã giải quyết”, mẫu QA hàng tuần và hàng đợi escalation.

## 3. Thay đổi sau kiểm tra chéo

1. **Đổi metric đích:** v1 dùng số câu hỏi/người, dễ khuyến khích hỏi nhiều nhưng không chứng minh giá trị. v2 thay bằng tỷ lệ hoàn thành tác vụ không cần hỏi lại; số câu hỏi chỉ còn là activity signal.
2. **Bổ sung kiểm soát nguồn:** v1 chỉ ghi tỷ lệ có trích dẫn. v2 đo tỷ lệ vượt QA gồm nguồn tồn tại, đúng đoạn, còn hiệu lực và người dùng có quyền truy cập; thêm hành động dừng mở rộng nếu dưới ngưỡng.
3. **Làm rõ trách nhiệm:** thêm owner cho từng metric và owner dữ liệu nguồn; AI không chịu trách nhiệm kết quả cuối.

## 4. Quyết định

**SỬA rồi tiếp tục pilot.** Không rollout rộng ở baseline hiện tại vì QA nguồn 58% thấp hơn ngưỡng 75%. Tiếp tục có điều kiện giúp thu bằng chứng thật mà vẫn giới hạn rủi ro. Dừng pilot nếu có một sự cố nghiêm trọng do nội dung không có/ghi sai nguồn hoặc QA dưới 60% hai tuần liên tiếp.

## 5. Bước tiếp theo theo owner

| Owner | Việc cần làm | Hạn | Dấu hiệu hoàn thành |
|---|---|---|---|
| Knowledge Manager | Gắn owner/SLA và ngày hiệu lực cho toàn bộ nguồn pilot | Ngày 30 | 100% nguồn đạt checklist |
| Product Owner | Triển khai feedback trong luồng và event log | Ngày 30 | Log đủ `task_id`, outcome, citation, escalation |
| QA Lead | Chấm mẫu 30 câu/tuần theo rubric nguồn | Hàng tuần | Báo cáo QA có lỗi và hành động sửa |
| Adoption Lead | Hướng dẫn 10 phút tại điểm dùng + office hour | Ngày 45 | ≥80% người pilot vượt bài kiểm tra thao tác |
| AI Governance Lead | Họp gate và quyết định tiếp tục/sửa/dừng | Ngày 60 và 90 | Memo quyết định được ký và lưu |

