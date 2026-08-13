# BÁO CÁO TỔNG KẾT THAY ĐỔI ĐẶC TẢ YÊU CẦU PHẦN MỀM (SRS CHANGE REPORT)
## Dự án: EduAI - Intelligent Learning & Assessment Platform
### Phiên bản nâng cấp: `EDUAI_G5 - v1.3.0` (So với `v1.2.0`)

| Thông tin tài liệu | Chi tiết |
| :--- | :--- |
| **Mã dự án** | `EDUAI_2026` - Capstone Project / PRM393 |
| **Ngày thực hiện** | 13/08/2026 |
| **Đơn vị phụ trách** | Nhóm Kiến trúc Phần mềm & Thanh tra Khảo thí (Group 5) |
| **Tài liệu chính thức** | [SRS_NotebookLM_EduAI.md](file:///d:/ai-thucchien/EduAI/SRS_NotebookLM_EduAI.md) (v1.3.0) |
| **Bản lưu trữ gốc (Backup)** | [SRS_NotebookLM_EduAI_v1.2.0_backup.md](file:///d:/ai-thucchien/EduAI/SRS_NotebookLM_EduAI_v1.2.0_backup.md) (v1.2.0) |

---

## 1. TỔNG QUAN NÂNG CẤP (EXECUTIVE SUMMARY)

Bản cập nhật **v1.3.0** bổ sung toàn diện phân hệ **Khiếu nại, Phúc khảo & Thanh tra Khảo thí (Grade Appeal & Dispute Lifecycle with Examination Auditing)**. Nâng cấp này giải quyết bài toán cốt lõi về **niềm tin học thuật (Academic Trust)** đối với tính năng AI Tự động Chấm bài (AI Automated Grading), thiết lập cơ chế **Human-in-the-loop 3 chiều (Sinh viên - AI - Giảng viên - Khảo thí)** minh bạch, chống tiêu cực và đáp ứng đầy đủ tiêu chuẩn kiểm định giáo dục đại học (AUN-QA / MOET).

---

## 2. BẢNG SO SÁNH TỔNG THỂ (VERSION COMPARISON MATRIX)

| Hạng mục so sánh | Phiên bản cũ (`v1.2.0`) | Phiên bản mới (`v1.3.0`) | Tác động / Ý nghĩa |
| :--- | :---: | :---: | :--- |
| **Số lượng Actors** | 4 vai trò người dùng + 2 hệ thống ngoài | **5 vai trò người dùng** + 2 hệ thống ngoài | Bổ sung vai trò **Examination Officer (Khảo thí)** |
| **Số lượng Use Cases** | 82 Use Cases | **87 Use Cases** (+5 UCs lớn, +5 UCs nhánh) | Bổ sung phân hệ Phúc khảo & Thanh tra |
| **Số lượng Thực thể DB** | 16 Entities | **19 Entities** (+3 Tables mới) | Bổ sung `grade_appeals`, `audit_logs`, `freezes` |
| **Danh mục REST APIs** | 32 Endpoints | **40 Endpoints** (+8 Endpoints mới) | Bổ sung trọn bộ API Khiếu nại & Khảo thí |
| **Feature Specs (FID)** | 6 FIDs (FID-01 đến FID-06) | **7 FIDs** (Bổ sung FID-07) | Chi tiết hóa giao diện và luồng xử lý phúc khảo |
| **Quy tắc nghiệp vụ (BR)** | 11 Master Business Rules | **20 Master Business Rules** (+9 BRs) | Bổ sung BR-AP01 đến BR-AP09 |
| **Trạng thái nộp bài** | `IN_PROGRESS`, `SUBMITTED`, `GRADED` | Thêm trạng thái **`LOCKED_APPEAL`** | Khóa bài thi cá nhân khi đang tranh chấp điểm |

---

## 3. CHI TIẾT CÁC NỘI DUNG NÂNG CẤP CHÍNH

### 3.1 Bổ sung Actor: Cán bộ Khảo thí & Đảm bảo Chất lượng (`EXAMINATION_OFFICER`)
* **Vai trò:** Đơn vị trọng tài kiểm toán độc lập cấp trường (University-wide).
* **Quyền hạn đặc thù:**
  * Tra cứu và giám sát toàn bộ đơn khiếu nại trong toàn trường.
  * Đối soát 3 chiều (Three-Way Inspector): So sánh song song bài làm sinh viên vs nhận định AI vs quyết định của Giảng viên.
  * Trọng tài phân xử các ca khiếu nại bị quá hạn SLA ($>72$ giờ).
  * Quyền tối cao đảo ngược (`AUDITOR_OVERRULE`) điểm số nếu phát hiện giảng viên sửa điểm bất thường.
  * Phê duyệt và thực hiện **Khóa sổ bảng điểm chính thức (Final Gradebook Sign-off)**.

---

### 3.2 Chuẩn hóa Vòng đời Khiếu nại (Dispute State Machine)
Hệ thống thiết lập state machine chặt chẽ với 8 trạng thái:
1. `SUBMITTED`: Sinh viên tạo đơn thành công (trong 48h).
2. `UNDER_REVIEW`: Giảng viên trong lớp tiếp nhận (Claim).
3. `APPROVED`: Giảng viên duyệt tăng điểm (bảo vệ quyền lợi, không bao giờ trừ điểm).
4. `REJECTED`: Giảng viên bác bỏ, giữ nguyên điểm AI ban đầu.
5. `CANCELLED`: Sinh viên tự rút đơn trước khi giảng viên tiếp nhận.
6. `ESCALATED_TO_AUDITOR`: Tự động chuyển lên Khảo thí nếu giảng viên bỏ quên quá 72h.
7. `AUDITOR_RESOLVED`: Khảo thí trực tiếp phân xử đơn quá hạn.
8. `AUDITOR_OVERRULED`: Khảo thí phát hiện sai lệch và ghi đè điểm số kèm biên bản thanh tra.

---

### 3.3 Mở rộng Cơ sở Dữ liệu (Database Schema Additions)
Bổ sung 3 bảng mới vào PostgreSQL:
1. **`grade_appeals`**: Quản lý đơn khiếu nại, gắn kết với `document_chunks` (dẫn chứng RAG) hoặc tài liệu ngoài, điểm ban đầu, điểm điều chỉnh và lời phê.
2. **`grade_dispute_audit_logs`**: Bảng kiểm toán bất biến (Immutable Audit Trail) ghi lại mọi hành động thay đổi điểm, chênh lệch điểm ($\Delta\text{Score}$), IP và lý do.
3. **`exam_gradebook_freezes`**: Quản lý quy trình 2 người duyệt (Two-man rule) để khóa sổ điểm chính thức giữa Giảng viên và Khảo thí.

---

### 3.4 Bổ sung 8 REST API Endpoints Mới

| Method | Endpoint Path | Role | Chức năng |
| :--- | :--- | :---: | :--- |
| `POST` | `/api/v1/submissions/{id}/questions/{qid}/appeals` | `STUDENT` | Sinh viên tạo đơn khiếu nại câu hỏi |
| `PATCH` | `/api/v1/appeals/{id}/cancel` | `STUDENT` | Sinh viên rút đơn khiếu nại |
| `GET` | `/api/v1/teachers/workspaces/{id}/appeals` | `TEACHER` | Danh sách đơn khiếu nại trong lớp |
| `POST` | `/api/v1/appeals/{id}/claim` | `TEACHER` | Giảng viên nhận đơn (`UNDER_REVIEW`) |
| `PUT` | `/api/v1/appeals/{id}/resolve` | `TEACHER` | Giảng viên duyệt/từ chối khiếu nại |
| `GET` | `/api/v1/auditor/appeals` | `EXAMINATION_OFFICER` | Tra cứu đơn khiếu nại toàn trường |
| `GET` | `/api/v1/auditor/appeals/{id}/three-way-view` | `EXAMINATION_OFFICER` | Xem đối soát 3 chiều chi tiết |
| `PUT` | `/api/v1/auditor/appeals/{id}/overrule` | `EXAMINATION_OFFICER` | Khảo thí phân xử quá hạn / Đảo ngược điểm |
| `POST` | `/api/v1/exams/{id}/request-freeze` | `TEACHER` | Giảng viên gửi yêu cầu khóa sổ điểm |
| `POST` | `/api/v1/auditor/exams/{id}/sign-off` | `EXAMINATION_OFFICER` | Khảo thí ký duyệt và khóa sổ điểm chính thức |

---

### 3.5 Bổ sung Bộ Quy tắc Nghiệp vụ Chống Gian lận & Quá tải (BR-AP01 $\rightarrow$ BR-AP09)

* **BR-AP01 (Hạn nộp 48h):** Tự động khóa tính năng sau 48h kể từ khi công bố điểm.
* **BR-AP02 (Hạn mức chống Spam):** Tối đa 1 lần/câu và tối đa 3 câu/bài thi.
* **BR-AP03 (Quy định dẫn chứng):** Bắt buộc lý do $\ge 30$ ký tự; hỗ trợ chọn Chunk RAG từ Notebook hoặc nguồn ngoài.
* **BR-AP04 (Zero-Downside Protection):** Không hạ điểm khi phúc khảo (${\text{original\_ai\_score}} \le \text{final\_score} \le \text{max\_score}$).
* **BR-AP05 (Bắt buộc giải trình):** Giảng viên/Khảo thí phải nhập lời phê $\ge 20$ ký tự; tự động ghi nhật ký bất biến.
* **BR-AP06 (Khóa riêng bài thi):** Chỉ khóa bài thi cá nhân (`LOCKED_APPEAL`), bảng điểm của lớp vẫn hoạt động bình thường.
* **BR-AP07 (SLA Escalation 72h):** Đơn quá hạn 72h tự động kích hoạt cảnh báo đỏ và chuyển quyền cho Khảo thí.
* **BR-AP08 (AI Anomaly Red Flag):** Tự động cảnh báo khi đề thi có $\ge 20\%$ sinh viên khiếu nại hoặc câu hỏi bị lệch điểm $>30\%$.
* **BR-AP09 (Two-Man Gradebook Sign-off):** Khóa sổ điểm bắt buộc qua 2 bước: GV yêu cầu $\rightarrow$ Khảo thí đối soát và phê duyệt.

---

## 4. TỔNG KẾT TRẠNG THÁI FILE HỆ THỐNG

1. **File tài liệu chính thức:** [SRS_NotebookLM_EduAI.md](file:///d:/ai-thucchien/EduAI/SRS_NotebookLM_EduAI.md)  
   *(Đã được cập nhật lên phiên bản hoàn chỉnh `v1.3.0`)*.
2. **File sao lưu phiên bản cũ:** [SRS_NotebookLM_EduAI_v1.2.0_backup.md](file:///d:/ai-thucchien/EduAI/SRS_NotebookLM_EduAI_v1.2.0_backup.md)  
   *(Được lưu trữ nguyên vẹn để đối chiếu lịch sử)*.
3. **File báo cáo thay đổi:** [SRS_Change_Report_v1.3.0.md](file:///d:/ai-thucchien/EduAI/SRS_Change_Report_v1.3.0.md)  
   *(Báo cáo chi tiết phục vụ báo cáo tiến độ và nghiệm thu Capstone Project)*.
