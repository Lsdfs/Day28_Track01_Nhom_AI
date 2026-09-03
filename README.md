# Day 28 — Dashboard Hành động cho P-047 Safety Monitoring

## 1. Thành viên và đóng góp

| Họ tên | MSSV | Phần phụ trách | Góp ý đã đưa cho nhóm bạn |
|---|---|---|---|
| Nguyễn Trọng Đức | 2A202601673 | Viết mục 4: cách làm mới và lộ trình 30–60–90 ngày; viết mục 5: hệ thống chỉ số theo dõi | Chưa có dữ liệu phản biện trong lịch sử repo — cần điền góp ý thực tế trước khi nộp |
| Chu Thị Yến Khanh | 2A202601739 | Xây dựng dashboard v1/v2 và memo; viết mục 1–3, mục 6; tổng hợp README và chuẩn hóa cấu trúc repo | Chưa có dữ liệu phản biện trong lịch sử repo — cần điền góp ý thực tế trước khi nộp |

Phần phụ trách được đối chiếu từ commit history của chính repository bài Lab này: commit `e46f170` ghi nhận phần chỉnh sửa mục 4–5 của Nguyễn Trọng Đức; các commit còn lại ghi nhận phần xây dựng, hiệu chỉnh và chuẩn hóa bộ bài của Chu Thị Yến Khanh. Lịch sử hiện chỉ ghi nhận commit của hai thành viên. Tên nhóm phản biện và góp ý thực tế chưa có trong repository nên cần bổ sung từ buổi kiểm tra chéo, không tự bịa.

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

## 4. Đề xuất cải tiến và kế hoạch 30–60–90 ngày

### Quy trình mục tiêu và phân công vai trò theo Mollick

- **AI đảm nhận khâu tự động:** lấy mẫu khung hình, nhận diện người/mũ/áo phản quang, theo dõi đối tượng, áp dụng quy tắc và tạo sự kiện ứng viên. AI chỉ đưa ra gợi ý, không tự xác nhận một vi phạm chính thức.
- **Safety Manager kiểm tra và ra quyết định:** xem ảnh chụp đã được làm mờ, đánh giá ngữ cảnh rồi phân loại sự kiện là “Vi phạm thực tế” hoặc “Bình thường / báo sai”.
- **Area Manager tổ chức xử lý:** tiếp nhận những sự kiện đã được xác nhận trong khu vực phụ trách, thực hiện biện pháp khắc phục và hoàn tất sự kiện.
- **System Admin/AI Governance giám sát hệ thống:** thiết lập nguồn dữ liệu và quy tắc, theo dõi chỉ số vận hành, quản lý phân quyền và thời gian lưu trữ, đồng thời phê duyệt việc mở rộng triển khai.

### Ba cải tiến cần hoàn thành

1. Hoàn thiện quy trình ground truth US-13 và bổ sung các chỉ số precision/recall cho cảnh báo. Lượt đăng nhập, số frame hay tổng số event chỉ phản ánh mức độ hoạt động, không được dùng để khẳng định giá trị sản phẩm.
2. Thiết lập SLA cho toàn bộ vòng đời `unacknowledged → acknowledged/resolved`, chỉ định người phụ trách theo camera/khu vực và thực hiện chuyển cấp khi quá thời hạn.
3. Chỉ cho phép mở rộng triển khai khi hệ thống đạt các ngưỡng về chất lượng, quyền riêng tư và kiểm thử sức bền. Nếu không thể làm mờ dữ liệu nhận dạng hoặc dịch vụ Vision gặp lỗi, hệ thống phải duy trì cơ chế fail-closed.

| Giai đoạn | Mục tiêu | Hành động | Dấu hiệu hoàn thành | Owner |
|---|---|---|---|---|
| 0–30 ngày | Xây dựng baseline đáng tin cậy | Xác nhận dataset và provenance; gán nhãn ground truth; khắc phục hoặc phân loại 14 lỗi; thực hiện browser UAT end-to-end | Quy trình US-13 được phê duyệt; có ít nhất 200 tình huống đã gán nhãn; không còn blocker Sev-1/2 | Vision Lead + QA Lead |
| 31–60 ngày | Thử nghiệm hẹp với HITL | Pilot tại một khu vực trong một ca; đo precision, recall, false alarm, time-to-review và privacy recall | Alert precision ≥85%; critical recall ≥95%; tỷ lệ review đúng SLA ≥90% | Safety Manager + Product Owner |
| 61–90 ngày | Mở rộng theo điều kiện | Chạy soak test bốn camera trong 30 phút; đo freshness/P95; diễn tập quy trình escalation và rollback | P95 cảnh báo ≤2 giây; không có snapshot lộ danh tính; đủ căn cứ để quyết định tiếp tục/sửa/dừng | AI Governance Lead |

## 5. Hệ thống chỉ số theo dõi

Các chỉ số chi tiết được trình bày trong `dashboard/dashboard_hanh_dong_v2.xlsx`.

- **Chỉ số sản phẩm:** theo dõi tỷ lệ cảnh báo hữu ích sau khi được HITL xác nhận. Cơ sở dữ liệu cục bộ hiện có `0` sự kiện được lưu nên chưa thể lập baseline; mục tiêu đến ngày 60 là đạt ít nhất 85%.
- **Chỉ số an toàn sản phẩm:** đo recall của các tình huống critical trên tập ground truth. Chỉ số này chưa có baseline do gate US-13 vẫn chưa được hoàn tất; ngưỡng mục tiêu là tối thiểu 95%.
- **Chỉ số vận hành — tỷ lệ hoàn thành inference:** log trong môi trường phát triển cho baseline `1.544 / (1.544 + 430) = 78,2%`; mục tiêu là từ 99% trở lên.
- **Chỉ số vận hành — độ trễ inference P95:** baseline hiện tại xấp xỉ 1.306 ms, trong khi mục tiêu là không quá 1.000 ms; toàn bộ luồng cảnh báo end-to-end phải hoàn tất trong 2 giây.
- **Cổng chất lượng:** backend/vision hiện vượt qua 304/318 bài test, tương đương 95,6%. Toàn bộ 14 lỗi còn lại phải được khắc phục hoặc phân loại trước khi bắt đầu pilot.

Với từng chỉ số, workbook nêu rõ baseline, mục tiêu, nguồn dữ liệu, người phụ trách và phương án xử lý khi kết quả không đạt. Tài liệu cũng phân biệt số liệu thu từ môi trường test/development với dữ liệu cần thu thập trong giai đoạn pilot.

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
