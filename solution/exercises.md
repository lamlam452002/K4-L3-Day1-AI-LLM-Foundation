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
> Ở temperature 0.0 và 0.5, model chọn những sự thật an toàn, phổ biến và
> trình bày mạch lạc, có cấu trúc rõ ràng (ví dụ: giải thích chữ Quốc ngữ,
> động Sơn Đoòng). Ở temperature 1.0, câu trả lời vẫn đúng ngữ pháp nhưng bắt
> đầu chọn những sự thật ít phổ biến hơn (chợ nổi Cái Răng) và thêm chi tiết
> màu mè. Ở temperature 1.5, phản hồi bắt đầu xuất hiện mâu thuẫn nội tại và
> chi tiết bịa đặt (ví dụ nói "27 đảo nổi tiếng" trong khi ngay sau lại nói
> "hơn 1.600 hòn đảo", và bịa ra một "trò chơi Hải sản số" không có thật).
> Quy luật chung: temperature càng cao thì phản hồi càng đa dạng/sáng tạo
> nhưng cũng càng dễ mất chính xác (hallucination).

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Khoảng 0.0–0.3. Chatbot hỗ trợ khách hàng cần trả lời nhất quán, đúng chính
> sách/quy trình công ty và tránh bịa thông tin (như phí, chính sách hoàn
> tiền...). Temperature thấp giúp model bám sát dữ kiện đã cho trong prompt/
> tài liệu tham chiếu thay vì "sáng tạo" thêm chi tiết không có thật — điều
> tối quan trọng khi câu trả lời sai có thể ảnh hưởng trực tiếp đến trải
> nghiệm và niềm tin của khách hàng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Theo bảng giá trong code, giá output GPT-4o là $0.010/1K token và
> GPT-4o-mini là $0.0006/1K token → GPT-4o đắt hơn khoảng 
> (0.010 / 0.0006 ≈ 16.67) lần. 
>
> Với workload: 10.000 người dùng × 3 lượt/ngày × 350 token đầu ra
> = 10.500.000 token/ngày = 10.500 nghìn token.
> - Chi phí output với GPT-4o: 10.500 × 0.010 ≈ **$105/ngày** (~$3.150/tháng).
> - Chi phí output với GPT-4o-mini: 10.500 × 0.0006 ≈ **$6.3/ngày** (~$189/tháng).
>
=>  KL: Dùng GPT-4o cho các tác vụ cần suy luận phức tạp, độ chính xác cao
 hoặc ảnh hưởng lớn đến quyết định — sai sót ở đây tốn kém hơn nhiều so với phần chênh lệch
 chi phí API. Ngược lại thì GPT-4o-mini phù hợp với các tác vụ đơn giản, lặp lại (chẳng hạn như phân loại email, ... ) nơi chất lượng "đủ tốt" và tốc độ/chi phí quan trọng hơn độ chính xác tối đa.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với persona "giáo viên tiểu học", model dùng phép ẩn dụ đời thường ("cuốn
> sổ tay lớn nhiều người cùng viết"), câu ngắn, ví dụ gần gũi (trò chơi ghi
> điểm, tặng quà) và thêm emoji để dễ tiếp cận với trẻ em. Với persona
> "chuyên gia tài chính", model chuyển sang thuật ngữ kỹ thuật (Distributed
> Ledger Technology, Merkle root, PoW/PoS/BFT, smart contract), trình bày
> bằng bảng và sơ đồ kiến trúc node, mật độ thông tin dày đặc hơn hẳn. Cả
> hai đều trả lời đúng cùng một câu hỏi, nhưng system prompt định hình toàn
> bộ giọng văn, mức độ chuyên sâu, độ dài và loại ví dụ được chọn — chứng tỏ
> system prompt không chỉ là "gợi ý phong cách" mà thực sự điều khiển cách
> model lựa chọn nội dung để trình bày.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với một đoạn văn tiếng Việt 131 từ: `count_tokens` (tiktoken, encoding của
> gpt-4o) đếm được **169 token**, trong khi ước lượng `số từ / 0.75` cho ra
> **175 token** — chênh khoảng **3.6%**, khá gần nhau ở đoạn này (heuristic
> 0.75 vẫn tạm ổn cho ước lượng nhanh).
>
> Tuy nhiên khi so cùng nội dung dịch sang tiếng Anh (100 từ, 111 token, tức
> **1.11 token/từ**) với bản tiếng Việt (131 từ, 169 token, tức **1.29
> token/từ**), tiếng Việt tốn nhiều token hơn khoảng **16%** trên mỗi từ.
> Lý do: bộ mã hóa BPE của các model như GPT-4o được huấn luyện chủ yếu trên
> dữ liệu tiếng Anh, nên các từ tiếng Anh thường được gộp thành 1 token duy
> nhất; còn tiếng Việt có dấu thanh và nguyên âm ghép (ví dụ "nghìn", "hiếu
> khách") thường bị tách thành nhiều token nhỏ hơn (đôi khi tới từng
> byte/ký tự có dấu), vì các tổ hợp ký tự đó xuất hiện ít hơn trong dữ liệu
> huấn luyện gốc.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi có người dùng thật đang chờ trực tiếp trên
> giao diện chat — nó giảm "thời gian chờ cảm nhận được" (perceived latency)
> vì người dùng thấy chữ xuất hiện ngay sau vài trăm mili-giây thay vì phải
> nhìn màn hình trống trong nhiều giây trước khi cả câu trả lời dài hiện ra
> một lúc. Ngược lại, non-streaming phù hợp hơn khi kết quả cần được xử lý
> tiếp bởi code trước khi dùng (ví dụ parse JSON, kiểm duyệt nội dung, ghi
> vào database) — vì lúc đó cần toàn bộ response hoàn chỉnh mới xử lý được,
> và việc stream từng phần không mang lại lợi ích UX nào (không có người
> đang nhìn màn hình theo thời gian thực), thậm chí còn làm code phức tạp
> hơn không cần thiết.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tăng dần thời gian chờ sau mỗi lần thất bại (ví dụ
> 0.1s, 0.2s, 0.4s, 0.8s...), giúp giảm áp lực lên server đang quá tải theo
> thời gian thay vì dội thêm request ngay lập tức — cho server thời gian để
> hồi phục. Nếu hàng nghìn client cùng dùng delay cố định giống nhau (ví dụ
> luôn chờ đúng 1 giây), tất cả các request thất bại ban đầu sẽ đồng loạt
> retry lại cùng 1 thời điểm sau đúng 1 giây, tạo ra một đợt "sóng"
> traffic đồng bộ (thundering herd) khiến server vốn đã quá tải lại bị dội
> thêm một lượng request lớn cùng lúc, dễ dẫn đến vòng lặp quá tải liên tục
> không bao giờ hồi phục được. Trong thực tế, người ta còn thêm độ trễ ngẫu nhiên nhỏ
>  vào exponential backoff để tránh các client đồng
> bộ hoá lẫn nhau.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona: **"Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn
> bằng tiếng Việt."**
>
> - Từ "**ngắn gọn**" rất quan trọng vì nếu không giới hạn, model có xu
> hướng trả lời dài dòng, liệt kê thừa chi tiết — với một trợ lý CLI tương
> tác theo lượt (turn-based), câu trả lời dài vừa tốn token/chi phí, vừa
> làm chậm streaming và khó theo dõi trong terminal.
> - Chỉ định rõ "**bằng tiếng Việt**" để tránh model tự chuyển sang tiếng
> Anh khi câu hỏi có thuật ngữ kỹ thuật (một hành vi khá phổ biến ở các
> model đa ngôn ngữ), đảm bảo trải nghiệm nhất quán cho người học tiếng Việt.
> - Từ "**thân thiện**" định hình giọng văn gần gũi, khuyến khích người học
> hỏi tiếp thay vì cảm thấy bị đánh giá khi hỏi câu cơ bản.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất: `run_assistant` chỉ giữ **3 lượt hội thoại gần nhất**
> (`history[-6:]`), nên trợ lý "quên" hoàn toàn ngữ cảnh của các câu hỏi
> trước đó trong một phiên dài — ví dụ nếu người dùng giới thiệu tên hoặc
> mục tiêu học tập ở lượt 1 rồi hỏi tiếp ở lượt 6, model sẽ không còn nhớ
> thông tin đó.
>
> Cải thiện đề xuất: thêm một bước **tóm tắt (summarization)** khi history
> vượt quá ngưỡng — trước khi cắt bớt các lượt cũ, gọi API một lần để tóm
> tắt các lượt sắp bị loại thành 1–2 câu, rồi chèn tóm tắt đó vào đầu
> `messages` dưới dạng một message hệ thống bổ sung (ví dụ:
> `{"role": "system", "content": f"Tóm tắt hội thoại trước: {summary}"}`).
> Cách này giữ được ngữ cảnh dài hạn quan trọng mà không phải gửi toàn bộ
> lịch sử (tốn token) trong mỗi lượt gọi.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
