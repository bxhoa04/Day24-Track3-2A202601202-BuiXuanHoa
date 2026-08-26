# LLM Judge Bias Report — Phase B

**Sinh viên:** Bùi Xuân Hòa  
**Ngày:** 26/08/2026  
**Judge model:** gpt-4o-mini / openai/gpt-oss-20b

---

## 1. Pairwise Judge Results

| # | Question (tóm tắt) | Winner | Reasoning tóm tắt |
|---|---|---|---|
| 1 | Nghỉ phép khi kết hôn | A | Answer A chính xác 3 ngày làm việc hưởng lương, chi tiết đầy đủ hơn |
| 2 | Mua thiết bị 55 triệu ai phê duyệt | A | Answer A phân tích đúng hạn mức trên 50 triệu cần CEO phê duyệt |
| 3 | Thưởng Tết tối thiểu nhân viên > 6 tháng | A | Answer A nêu đúng quy định 1 tháng lương thực nhận |
| 4 | Senior 9 năm thâm niên: phép + lương | A | Answer A tính đúng 15 ngày cơ bản + 3 ngày thâm niên = 18 ngày phép |
| 5 | Hoàn trả chi phí đào tạo 25 triệu | A | Answer A áp dụng đúng quy định hoàn trả 100% khi nghỉ trước 12 tháng |

---

## 2. Swap-and-Average Results

| # | Pass 1 Winner | Pass 2 Winner | Final | Position Consistent? |
|---|---|---|---|---|
| 1 | A | A | A | True |
| 2 | A | A | A | True |
| 3 | A | A | A | True |
| 4 | A | A | A | True |
| 5 | A | A | A | True |

**Position bias rate:** 0.0% (0 / 10 cases not consistent)

---

## 3. Cohen's κ Analysis

**Human labels:** `human_labels_10q.json` (10 câu, 5 label=1, 5 label=0)  
**Judge labels:** [1, 0, 1, 1, 1, 1, 1, 1, 1, 0]

| Question ID | Human Label | Judge Label | Agree? |
|---|---|---|---|
| 1 | 1 | 1 | Yes |
| 5 | 0 | 0 | Yes |
| 12 | 1 | 1 | Yes |
| 21 | 1 | 1 | Yes |
| 23 | 1 | 1 | Yes |
| 29 | 1 | 1 | Yes |
| 33 | 0 | 1 | No |
| 41 | 1 | 1 | Yes |
| 46 | 0 | 1 | No |
| 50 | 0 | 0 | Yes |

**Cohen's κ:** 0.60  
**Interpretation:** Substantial Agreement (Đồng thuận cao theo thang Landis–Koch).

---

## 4. Verbosity Bias

Trong các case có winner rõ ràng (không phải tie):
- A thắng + A dài hơn B: 4 / 5 cases
- B thắng + B dài me hơn A: 0 / 5 cases  
- **Verbosity bias rate:** 80.0%

**Kết luận:** LLM Judge có xu hướng đánh giá cao các câu trả lời dài và chi tiết hơn (Verbosity Bias). Điều này có thể dẫn đến việc ưu tiên các câu trả lời rườm rà nếu không kiểm soát độ súc tích qua prompt template.

---

## 5. Nhận xét chung

> Chỉ số Cohen's $\kappa = 0.60$ cho thấy LLM Judge đạt độ tương đồng đáng kể (Substantial Agreement) so với nhãn của con người.
> Kỹ thuật Swap-and-average đóng vai trò rất quan trọng trong việc triệt tiêu Position Bias (đạt tỉ lệ nghịch vị trí 0%), giúp kết quả nhất quán bất kể thứ tự đứng trước hay đứng sau của câu trả lời.
> Trong môi trường production, nên sử dụng LLM Judge kết hợp với quy định độ dài tối đa (length penalty) và prompt rõ ràng để tránh bị ảnh hưởng bởi Verbosity Bias.
