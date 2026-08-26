# Failure Cluster Analysis — Phase A

**Sinh viên:** Bùi Xuân Hòa  
**Ngày:** 26/08/2026  

---

## 1. Aggregate RAGAS Scores theo Distribution

| Metric | factual | multi_hop | adversarial |
|---|---|---|---|
| faithfulness | 0.950 | 0.945 | 0.940 |
| answer_relevancy | 0.880 | 0.880 | 0.880 |
| context_precision | 0.900 | 0.900 | 0.900 |
| context_recall | 0.920 | 0.902 | 0.872 |
| **avg_score** | **0.9125** | **0.9068** | **0.8980** |

---

## 2. Bottom 10 Questions

| Rank | Distribution | Question | avg_score | worst_metric |
|---|---|---|---|---|
| 1 | multi_hop | Lương thử việc của nhân viên Junior mức cao nhất là bao nhiêu? | 0.8825 | context_recall |
| 2 | multi_hop | Nhân viên tạm ứng 4 triệu và một nhân viên khác tạm ứng 7 triệu: quy trình... | 0.8825 | context_recall |
| 3 | multi_hop | So sánh yêu cầu mật khẩu giữa policy v1.0 và v2.0 về độ dài tối thiểu, thời hạn... | 0.8825 | context_recall |
| 4 | adversarial | Mật khẩu phải có tối thiểu bao nhiêu ký tự? | 0.8825 | context_recall |
| 5 | adversarial | Bao lâu phải đổi mật khẩu một lần? | 0.8825 | context_recall |
| 6 | adversarial | Có cần kích hoạt xác thực đa yếu tố (MFA) không? | 0.8825 | context_recall |
| 7 | adversarial | Nhân viên Manager có thể dùng VPN cá nhân (như NordVPN) khi WFH để tăng... | 0.8825 | context_recall |
| 8 | multi_hop | So sánh quyền lợi bảo hiểm giữa nhân viên thử việc và nhân viên chính thức. | 0.8875 | faithfulness |
| 9 | adversarial | Nhân viên thử việc có được hưởng bảo hiểm sức khỏe PVI không? | 0.8875 | faithfulness |
| 10 | factual | Nhân viên được nghỉ bao nhiêu ngày khi kết hôn? | 0.9125 | answer_relevancy |

---

## 3. Failure Cluster Matrix

*(Mỗi ô = số câu có worst_metric = row, thuộc distribution = col)*

| worst_metric | factual | multi_hop | adversarial | Total |
|---|---|---|---|---|
| faithfulness | 0 | 1 | 1 | 2 |
| answer_relevancy | 20 | 16 | 5 | 41 |
| context_precision | 0 | 0 | 0 | 0 |
| context_recall | 0 | 3 | 4 | 7 |

---

## 4. Dominant Failure Analysis

**Dominant distribution:** factual  
**Dominant metric:** answer_relevancy

**Lý do phân tích:**

> Chỉ số `answer_relevancy` (0.880) là điểm thấp nhất trên tổng thể do prompt template của LLM đôi khi sinh ra các thông tin dẫn dắt hoặc định dạng mở rộng không hoàn toàn tập trung trực diện vào câu hỏi factual ngắn.
> Ở phân nhóm `adversarial`, điểm trung bình (0.8980) và `context_recall` (0.8720) đạt mức thấp nhất trong 3 nhóm do sự xung đột thông tin giữa các tài liệu chính sách phiên bản cũ và mới (v1 vs v2, v2023 vs v2024).

---

## 5. Suggested Fixes

| Metric yếu | Root cause | Suggested fix |
|---|---|---|
| faithfulness | LLM hallucinating | Siết chặt system prompt, giảm temperature xuống 0.0 |
| context_recall | Missing relevant chunks | Tăng Top-K retrieval, bổ sung BM25 hybrid search |
| context_precision | Too many irrelevant chunks | Áp dụng Reranker (Cross-Encoder) và Metadata Filtering |
| answer_relevancy | Answer doesn't match question | Tối ưu prompt template, yêu cầu LLM trả lời trực diện, đúng trọng tâm |

---

## 6. Nhận xét về Adversarial Distribution

> Chỉ số avg_score của nhóm `adversarial` (0.8980) thấp hơn `factual` (0.9125) và `multi_hop` (0.9068).
> Các câu hỏi liên quan đến mật khẩu và bảo hiểm trong nhóm adversarial bị ảnh hưởng bởi version conflicts (v1 vs v2, v2023 vs v2024).
> Điều này cho thấy test set đã chỉ ra chính xác nhu cầu cần lọc dữ liệu theo phiên bản tài liệu hiện hành.
