# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *Temperature 0.0 cho câu trả lời an toàn, mang tính sự thật phổ biến (như vịnh Hạ Long) và lặp lại nếu gọi nhiều lần. Khi tăng dần lên 0.5 và 1.0, câu trả lời trở nên đa dạng, sáng tạo và thú vị hơn. Ở mức 1.5, ngôn từ bắt đầu lộn xộn, lủng củng và mô hình có xu hướng bịa đặt thông tin (hallucination) hoặc mất tính logic.*

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> *Tôi sẽ đặt temperature ở mức thấp (khoảng 0.0 đến 0.2). Chatbot hỗ trợ khách hàng cần ưu tiên sự chính xác, tính nhất quán, tuân thủ đúng chính sách công ty và không được phép bịa đặt thông tin gây hiểu lầm cho người dùng.*

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**

> *Ước tính GPT-4o đắt hơn GPT-4o-mini khoảng 25 lần (dựa trên chênh lệch giá token đầu ra hiện hành).

Nên dùng GPT-4o: Khi cần suy luận logic phức tạp, viết mã nguồn (code) khó, hoặc phân tích báo cáo tài chính chuyên sâu.

Nên dùng mini: Tóm tắt văn bản, chatbot giao tiếp cơ bản, phân loại dữ liệu người dùng nhằm tối ưu chi phí ở quy mô lớn.*

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *Prompt "giáo viên tiểu học" cho câu trả lời ngắn, dùng từ vựng hàng ngày và ví dụ sinh động như quyển sổ chung của lớp học, không dùng từ kỹ thuật. Prompt "chuyên gia tài chính" sinh ra văn bản dài hơn, dùng cấu trúc câu phức tạp và ngập tràn thuật ngữ (sổ cái phân tán, node, mã hóa hash). Điều này cho thấy system prompt đóng vai trò như chiếc mỏ neo định hình hoàn toàn giọng điệu, mức độ chi tiết và góc nhìn của mô hình.*

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *Số token thực tế thường cao hơn ước lượng khoảng 30-50%. Tiếng Việt tốn nhiều token hơn vì mô hình được huấn luyện chủ yếu bằng dữ liệu tiếng Anh; do đó các từ tiếng Việt (đặc biệt là ký tự có dấu) thường bị thuật toán phân mảnh thành nhiều token nhỏ (subwords/bytes) thay vì một token duy nhất như từ tiếng Anh.*

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *Streaming cực kỳ quan trọng đối với các ứng dụng giao diện chat trực tiếp (như ChatGPT, chatbot), giúp giảm thời gian phản hồi cảm nhận (Time to First Token), giữ chân người dùng vì họ có thể đọc ngay trong lúc model đang tạo văn bản. Ngược lại, non-streaming phù hợp hơn cho các tác vụ chạy ngầm (batch processing, tổng hợp dữ liệu, tự động gửi email) nơi hệ thống chỉ cần toàn bộ kết quả cuối cùng để xử lý bước tiếp theo thay vì tương tác theo thời gian thực.*

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *Exponential backoff giúp giảm bớt áp lực dồn dập lên server bằng cách dãn cách thời gian giữa các lần gọi lại. Nếu hàng nghìn client cùng retry với delay cố định 1 giây, chúng sẽ tạo ra hiệu ứng "Thundering Herd" (bầy đàn sấm sét) - đồng loạt tấn công server ngay khi nó vừa phục hồi, khiến server tiếp tục sập ngay lập tức.*

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Tôi chọn persona "Trợ lý lập trình viên tối giản". System prompt: "Bạn là chuyên gia Python. Chỉ cung cấp đoạn code, không giải thích dài dòng trừ khi được hỏi cụ thể. Sử dụng tiếng Việt."
Lựa chọn "Chỉ cung cấp đoạn code" giúp tiết kiệm token đầu ra và tăng tốc độ trả lời khi tôi chỉ cần xem cú pháp; "Sử dụng tiếng Việt" để tránh mô hình tự động chuyển sang tiếng Anh khi gặp các thuật ngữ lập trình.*

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Hạn chế lớn nhất là trợ lý mắc chứng "mất trí nhớ dài hạn" vì chỉ giữ được một số lượt chat gần nhất trong history (giới hạn token context window). Để cải thiện, tôi sẽ tích hợp kiến trúc RAG (Retrieval-Augmented Generation) kết hợp cơ sở dữ liệu Vector (như ChromaDB). Khi người dùng chat, hệ thống sẽ nhúng (embed) câu hỏi để tìm kiếm các đoạn hội thoại cũ có liên quan trong Vector DB, sau đó đưa vào prompt để mô hình nhớ lại ngữ cảnh cũ.*

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
