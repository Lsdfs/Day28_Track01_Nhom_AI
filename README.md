# Day 28 — Dashboard Hành động cho Áp dụng AI

## 1. Thành viên và đóng góp

> Thay các trường `CHƯA CUNG CẤP` bằng thông tin thật trước khi nộp. Không nên tự bịa họ tên hoặc MSSV.

| Họ tên | MSSV | Phần phụ trách | Góp ý đã đưa cho nhóm bạn |
|---|---|---|---|
| CHƯA CUNG CẤP | CHƯA CUNG CẤP | Tổng hợp Gartner-Lite, chẩn đoán readiness và dashboard | Đề nghị đổi “số câu hỏi” từ chỉ số đích thành tín hiệu chẩn đoán; thêm tỷ lệ hoàn thành tác vụ có kiểm chứng |

Nhóm phản biện: `CHƯA CUNG CẤP`. Phần phản biện trong bài hiện là phiên tự kiểm tra chéo theo rubric; cần thay bằng tên nhóm thật nếu giảng viên yêu cầu.

## 2. Phạm vi

- Sản phẩm AI: trợ lý tra cứu quy định và tài liệu nội bộ có trích nguồn.
- Nhóm người dùng chính: nhân viên vận hành mới trong 90 ngày đầu.
- Quy trình: (1) tiếp nhận câu hỏi, (2) truy xuất tài liệu, (3) soạn câu trả lời có trích dẫn, (4) người dùng xác nhận/đánh dấu chưa giải quyết.
- Ngoài phạm vi: tư vấn pháp lý, quyết định nhân sự, tự động sửa tài liệu nguồn và câu trả lời không có tài liệu kiểm chứng.

## 3. Vấn đề, nguyên nhân gốc và bằng chứng

Hiện trạng tham chiếu: công cụ đã được cấp quyền nhưng nhân viên vẫn quay lại tìm thủ công hoặc hỏi đồng nghiệp; câu trả lời thiếu nguồn/ngày cập nhật làm giảm niềm tin. Vì không có dữ liệu doanh nghiệp thật trong đề bài, sheet `Evidence` ghi rõ bộ số liệu pilot mô phỏng để minh họa cách đo; nhóm phải thay bằng log/quan sát/phỏng vấn thật trước khi tuyên bố kết quả thực tế.

Hai nguyên nhân gốc được chốt:

1. **Readiness/Gartner-Lite:** độ tin cậy thấp vì quyền sở hữu tài liệu và SLA cập nhật chưa rõ; rollout rộng khi nguồn chưa sẵn sàng.
2. **Ability + Reinforcement/ADKAR:** người dùng chưa biết kiểm tra trích dẫn và chưa được phản hồi trong luồng công việc; hướng dẫn một lần không tạo hành vi bền vững.

Bằng chứng đang dùng: pilot mô phỏng gồm 100 tác vụ tra cứu, checklist QA trích dẫn và quan sát thao tác. Baseline minh họa: 42% tác vụ hoàn thành không cần hỏi lại; 58% câu trả lời vượt qua kiểm tra nguồn; 55% yêu cầu được xử lý trong SLA. Đây là số liệu giả định, không phải kết quả của một doanh nghiệp cụ thể.

## 4. Cách làm mới và lộ trình 30–60–90 ngày

Thiết kế TO-BE phân vai theo Molllick:

- **Người giải quyết:** xác định câu hỏi, đánh giá ngữ cảnh và chịu trách nhiệm kết quả cuối.
- **AI hỗ trợ:** truy xuất, tóm tắt và luôn kèm tài liệu, đoạn trích, ngày cập nhật.
- **AI tự động có kiểm soát:** phân loại câu hỏi và chuyển tuyến khi không tìm được nguồn đủ tin cậy; không tự tạo chính sách.
- **Người kiểm tra/owner:** duyệt nguồn, xử lý phản hồi sai và theo dõi chỉ số.

Ba thay đổi bắt buộc:

1. Chỉ trả lời khi có trích dẫn; nếu không đủ bằng chứng thì từ chối có cấu trúc và chuyển owner.
2. Gắn owner + SLA cho từng bộ tài liệu, kiểm tra quyền và độ mới trước khi mở rộng rollout.
3. Thêm nút “đã giải quyết/chưa giải quyết”, lý do thất bại và hàng đợi QA ngay trong workflow.

Lộ trình:

| Giai đoạn | Mục tiêu | Dấu hiệu hoàn thành | Owner |
|---|---|---|---|
| 0–30 ngày | Chuẩn hóa nguồn và luồng TO-BE | 100% bộ tài liệu có owner/SLA; pilot 20 người | Knowledge Manager |
| 31–60 ngày | Pilot có kiểm soát và huấn luyện tại chỗ | ≥75% câu trả lời qua QA; ≥65% tác vụ hoàn thành | Product Owner |
| 61–90 ngày | Mở rộng theo cổng kiểm soát | Đạt cả hai metric trong 2 tuần liên tiếp; có quyết định tiếp tục/sửa/dừng | AI Governance Lead |

## 5. Chỉ số và quyết định

Dashboard v2 nằm tại `dashboard/dashboard_hanh_dong_v2.xlsx`.

- Product metric: **Tỷ lệ hoàn thành tác vụ không cần hỏi lại** — baseline 42%, target 70% ngày 90.
- Workflow metric: **Tỷ lệ câu trả lời vượt QA nguồn** — baseline 58%, target 90% ngày 60.
- Guardrail: tỷ lệ yêu cầu chuyển tuyến đúng ≤15% và không có sự cố nghiêm trọng do câu trả lời không nguồn.

Mỗi dòng metric trong dashboard có baseline, target, nguồn, tần suất, owner và hành động khi xấu. Login/số câu hỏi chỉ được giữ làm tín hiệu chẩn đoán, không dùng làm thước đo giá trị.

## 6. Quyết định và phản biện

Quyết định hiện tại: **SỬA rồi tiếp tục pilot có kiểm soát**. Không rollout rộng cho đến khi metric QA nguồn đạt ≥75% trong hai tuần liên tiếp. Chi tiết lý do, hai thay đổi sau kiểm tra chéo và bước tiếp theo theo owner nằm trong `memo/memo_quyet_dinh.md`.

## Cấu trúc repo

```text
Day28_Track01_Nhom_AI/
├── README.md
├── dashboard/
│   ├── dashboard_hanh_dong_v2.xlsx
│   └── v1/
│       └── dashboard_hanh_dong_v1.xlsx
└── memo/
    └── memo_quyet_dinh.md
```

