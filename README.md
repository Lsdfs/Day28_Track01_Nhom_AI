# Day 28 — Dashboard Hành động cho P-047 Safety Monitoring

## 1. Thành viên và đóng góp

| Họ tên | MSSV | Phần phụ trách có căn cứ từ lịch sử P-047 | Góp ý cho nhóm bạn |
|---|---|---|---|
| Nguyễn Trọng Đức | 2A202601673 | Vision worker, tracking state v2, GPU/RunPod và camera-scoped event workflow | Chưa có dữ liệu nhóm phản biện trong repo — cần điền trước khi nộp |
| Chu Thị Yến Khanh | 2A202601739 | Test evidence, tài liệu, migration, event synchronization và quản trị tài khoản | Chưa có dữ liệu nhóm phản biện trong repo — cần điền trước khi nộp |

Phần phụ trách được tóm tắt từ `git shortlog` và commit history của P-047, không phải phân công tự khai. Tên nhóm phản biện và góp ý thực tế chưa tồn tại trong tài liệu nguồn nên không được tự bịa.

## 2. Phạm vi

- **Sản phẩm AI:** P-047 Safety Monitoring — hệ thống Computer Vision hỗ trợ giám sát PPE và vùng cấm trong nhà máy.
- **Nhóm người dùng chính:** `safety_manager`; `system_admin` cấu hình/vận hành và `area_manager` theo dõi khu vực được giao.
- **Bốn quy trình:** (1) upload và gán media cho camera logic, (2) cấu hình PPE/vùng cấm, (3) chạy inference và tạo cảnh báo, (4) HITL xác nhận rồi lưu audit/snapshot đã làm mờ.
- **Trong phạm vi:** ảnh/video mô phỏng, bốn camera logic, phát hiện người/mũ/áo phản quang, tracking, zone rule, realtime WebSocket, chống event trùng và dashboard sự kiện.
- **Ngoài phạm vi:** camera/PLC thật, tự động dừng máy, nhận dạng danh tính, tuyên bố độ chính xác nhà máy khi US-13 chưa hoàn tất.

Nguồn sản phẩm: `D:\AI-Vin\AI_Builder\P-047` và repository public `https://github.com/AI20K-Build-Phase-Cohort-3/P-047`, tại revision tham chiếu `c929e50` ngày 01/09/2026.

## 3. Vấn đề, nguyên nhân gốc và bằng chứng

### Vấn đề nghiệp vụ

Cán bộ an toàn không thể liên tục theo dõi mọi khu vực có máy dập, xe nâng hoặc hóa chất. Vi phạm PPE và xâm nhập vùng cấm có thể bị bỏ sót hoặc phản ứng chậm. P-047 đã triển khai được workflow kỹ thuật nhưng **chưa đủ bằng chứng để rollout pilot nhà máy**.

### Hai nguyên nhân gốc

1. **Readiness/Gartner-Lite:** dữ liệu đánh giá đại diện, provenance/license và benchmark US-13 vẫn là open gate. Dashboard hiện chỉ có event count; chưa được phép suy diễn compliance khi thiếu mẫu số quan sát bình thường.
2. **Governance + Ability/ADKAR:** hệ thống đã có HITL, audit và privacy fail-closed, nhưng quy trình vận hành thực tế chưa chứng minh trên camera nhà máy, bốn-camera soak test và SLA xử lý cảnh báo. Người dùng cần biết khi nào xác nhận, báo sai và escalate.

### Bằng chứng có thật trong repo P-047

- Manual functional/UI ngày 01/09/2026: **7/7 case PASS**, gồm PPE, vùng cấm, chống trùng, HITL/privacy, auth, authorization và reconnect.
- Focused backend tại revision `071c575`: **11/11 checks PASS trong 3,03 giây**.
- Quality gate rộng: frontend **127/127**, root **33/33**, backend/vision **304 pass và 14 fail đã biết**.
- Log `data/operational-metrics.jsonl`: 12.292 record; 1.544 inference hoàn thành, 430 inference fail, 834 frame bị drop; P95 inference khoảng **1.306 ms**. Đây chủ yếu là traffic phát triển/test, không phải production KPI.
- Local `safety_events.db` hiện có **0 sự kiện**, nên chưa thể tính tỷ lệ false alarm hoặc cảnh báo hữu ích một cách trung thực.

## 4. Cách làm mới và lộ trình 30–60–90 ngày

### Workflow TO-BE và phân vai theo Molllick

- **AI tự động:** lấy mẫu frame, phát hiện person/hat/vest, gắn track, đánh giá rule và mở candidate event; không tự kết luận vi phạm chính thức.
- **Safety Manager — người giải quyết/người kiểm tra:** xem snapshot đã làm mờ, đối chiếu bối cảnh và chọn “Vi phạm thực tế” hoặc “Bình thường / báo sai”.
- **Area Manager — người chịu trách nhiệm hành động:** nhận sự kiện đã xác nhận thuộc khu vực được giao, xử lý và đóng sự kiện.
- **System Admin/AI Governance:** cấu hình nguồn/rule, theo dõi operational metrics, kiểm soát quyền, retention và quyết định rollout.

### Ba thay đổi bắt buộc

1. Bổ sung ground-truth protocol US-13 và metric alert precision/recall; không dùng login, số frame hay event count làm bằng chứng giá trị.
2. Gắn SLA từ `unacknowledged → acknowledged/resolved`, owner theo camera/khu vực và escalation khi quá hạn.
3. Chỉ mở rộng rollout sau khi vượt quality gate, privacy gate và soak test; tiếp tục fail-closed khi redaction hoặc Vision provider lỗi.

| Giai đoạn | Mục tiêu | Hành động | Dấu hiệu hoàn thành | Owner |
|---|---|---|---|---|
| 0–30 ngày | Tạo baseline đáng tin | Chốt dataset/provenance; gán nhãn ground truth; sửa hoặc phân loại 14 lỗi; browser UAT end-to-end | US-13 protocol ký duyệt; ≥200 scenario có nhãn; không còn blocker Sev-1/2 | Vision Lead + QA Lead |
| 31–60 ngày | Pilot hẹp có HITL | Chạy 1 khu vực/1 ca; đo precision, recall, false alarm, time-to-review và privacy recall | Alert precision ≥85%; recall critical ≥95%; review SLA ≥90% | Safety Manager + Product Owner |
| 61–90 ngày | Mở rộng có điều kiện | Soak bốn camera 30 phút; đo freshness/P95; diễn tập escalation/rollback | P95 cảnh báo ≤2 giây; 0 snapshot lộ danh tính; gate tiếp tục/sửa/dừng | AI Governance Lead |

## 5. Chỉ số

Dashboard v2: `dashboard/dashboard_hanh_dong_v2.xlsx`.

- **Product metric:** tỷ lệ cảnh báo hữu ích được HITL xác nhận — baseline chưa đủ mẫu (`0` persisted event trong local DB), target ≥85% ở ngày 60.
- **Product safety metric:** recall tình huống critical trên tập ground truth — baseline chưa đo vì US-13 là open gate, target ≥95%.
- **Workflow metric:** tỷ lệ inference hoàn thành — baseline log phát triển `1.544 / (1.544 + 430) = 78,2%`, target ≥99%.
- **Workflow metric:** P95 inference latency — baseline khoảng 1.306 ms, target ≤1.000 ms; end-to-end alert target ≤2 giây.
- **Quality gate:** 304/318 backend/vision test pass (95,6%); phải xử lý/phân loại 14 lỗi trước pilot.

Mỗi metric trong workbook có baseline, target, nguồn, owner và hành động cụ thể nếu chỉ số xấu. Workbook cũng ghi rõ đâu là số liệu test/development và đâu là dữ liệu pilot cần thu.

## 6. Quyết định

**SỬA trước khi pilot; không rollout nhà máy lúc này.** P-047 đã chứng minh các luồng chức năng cốt lõi nhưng chưa chứng minh chất lượng model trên dữ liệu đại diện, false-alarm/recall thực tế, privacy recall và năng lực bốn camera. Memo tại `memo/memo_quyet_dinh.md` ghi rõ gate, owner và hai thay đổi từ v1 sang v2.

## Cấu trúc bài nộp

```text
Day28_Track01_Nhom_AI/
├── README.md
├── dashboard/
│   └── dashboard_hanh_dong_v2.xlsx
├── memo/
│   └── memo_quyet_dinh.md
└── v1/
    └── dashboard_hanh_dong_v1.xlsx
```
