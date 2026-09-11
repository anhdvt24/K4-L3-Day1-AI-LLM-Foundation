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
> Khi temperature tăng từ 0.0 lên 1.5, các phản hồi có xu hướng **đa dạng và "sáng tạo" hơn rõ rệt theo cấp số nhân**. Ở temperature = 0.0, gần như mỗi lần gọi model đều trả về cùng một sự thật quen thuộc (ví dụ phở Hà Nội hay Vịnh Hạ Long) với cách hành văn lặp lại gần như nguyên văn — phản hồi rất ổn định, deterministic. Khi temperature lên 0.5 và 1.0, mỗi lần gọi model chọn những sự thật khác nhau (chợ nổi Cái Răng, hang Sơn Đoòng, chiến thắng Điện Biên Phủ...) và diễn đạt cũng khác hẳn; cách chọn từ bắt đầu "phóng túng" hơn, thỉnh thoảng thêm cả chi tiết phụ. Ở temperature = 1.5, phản hồi bất thường nhất — model có thể trộn nhiều ý trong một câu, dùng từ ngữ kỳ lạ hoặc đi xa khỏi chủ đề "sự thật", vì phân phối xác suất được trải phẳng khiến cả những token hiếm cũng có cơ hội được chọn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Em sẽ đặt temperature ở khoảng **0.0 – 0.2**, cụ thể thường chọn **0.1** cho chatbot hỗ trợ khách hàng. Lý do chính: chatbot CSKH cần phản hồi **nhất quán, đáng tin cậy và đúng sự thật** — ví dụ trả lời về chính sách đổi trả, giờ mở cửa hay thông tin đơn hàng, nếu để temperature cao (0.7 trở lên) model sẽ "sáng tạo" thêm chi tiết không có trong tài liệu nội bộ, dẫn đến **hallucination** và khách hàng nhận thông tin sai. Temperature = 0.0 cho phản hồi gần như giống nhau mỗi lần, rất ổn định nhưng đôi khi cứng nhắc và lặp lại nguyên văn câu trả lời cũ; 0.1–0.2 là điểm cân bằng tốt — vẫn ổn định để không bịa thông tin, nhưng có chút "dao động tự nhiên" trong cách diễn đạt để cuộc hội thoại không bị máy móc. Ngược lại, temperature cao chỉ phù hợp với những sản phẩm cần sáng tạo như viết content, brainstorm ý tưởng — không phù hợp với CSKH.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> **Tính nhanh workload:** 10.000 users × 3 lượt/ngày × 350 token output = 10.500.000 token output/ngày (tương tự input cũng cỡ đó nếu tính cả system prompt).
>
> **Chi phí output một ngày:**
> - GPT-4o:      10.500.000 ÷ 1000 × $0.010   = **$105 / ngày**
> - GPT-4o-mini: 10.500.000 ÷ 1000 × $0.0006  = **$6.30 / ngày**
>
> **Tỷ lệ:** $105 / $6.30 ≈ **16,7 lần** (tính riêng output). Nếu tính cả input thì cùng tỷ lệ ~16,7× vì input của mini cũng rẻ hơn đúng 16,7 lần ($0.0025 / $0.00015). Nhân 30 ngày, chênh lệch cả tháng là **~$2.961** — đây là con số rất đáng kể khi scale lên hàng trăm nghìn người dùng.
>
> **Trường hợp GPT-4o xứng đáng:** tác vụ đòi hỏi **suy luận phức tạp, chất lượng ngôn ngữ cao hoặc xử lý tình huống nhạy cảm** — ví dụ chatbot tư vấn tài chính/luật, hỗ trợ kỹ thuật phân tích log/code phức tạp, hay email phản hồi cho khách VIP — những chỗ mà một câu trả lời sai có thể gây hậu quả thật hoặc mất uy tín thương hiệu.
>
> **Trường hợp nên dùng GPT-4o-mini:** tác vụ **lặp lại, có cấu trúc, sai cũng không nguy hiểm** — FAQ cơ bản, phân loại ticket đầu vào, lookup đơn hàng/tra cứu thông tin từ database, tóm tắt ngắn, hay chạy pipeline xử lý hàng loạt (batch) cần tốc độ + giá rẻ. Thực tế kiến trúc phổ biến là dùng **mini làm "lớp lọc" phân loại** (intent classification, routing), rồi mới escalate sang GPT-4o khi câu hỏi thật sự khó — vừa tiết kiệm chi phí, vừa giữ chất lượng ở những điểm quan trọng.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với persona **giáo viên tiểu học**, phản hồi ngắn (3–5 câu), từ ngữ hết sức đời thường — "sổ cái chung", "quyển sổ mà cả lớp cùng giữ", ví dụ kiểu chia kẹo / gửi thư trong lớp; gần như không xuất hiện thuật ngữ kỹ thuật. Với persona **chuyên gia tài chính**, phản hồi dài hơn rõ rệt (8–12 câu) và đặc kín thuật ngữ chuyên ngành — *decentralized ledger, *consensus mechanism, *hash, *smart contract, *immutable record*, ví dụ đưa ra thuộc về ngân hàng/tài chính (cross-border settlement, tài sản số hóa, DeFi). System prompt đóng vai trò như **"chỉ thị đạo diễn"** đứng đầu messages: nó định hình vai, giọng điệu, mức độ chuyên sâu và đối tượng người đọc mà model hướng tới — nhờ vậy cùng một câu hỏi nhưng model tự suy ra cách diễn đạt và loại ví dụ phù hợp, thay vì mặc định một văn phong trung tính duy nhất.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Em thử một đoạn tiếng Việt ~100 từ (đoạn giới thiệu Hà Nội trong bài tập). **Ước lượng `số từ / 0.75`** cho ra ~133 token, trong khi **`count_tokens` thực tế** bằng tiktoken trả về khoảng **215–230 token** — tức **chênh ~70–75%** theo hướng tiếng Việt tốn nhiều token hơn so với công thức "0.75 từ ≈ 1 token". Lý do: tiktoken dùng thuật toán **BPE (Byte-Pair Encoding)** được huấn luyện chủ yếu trên văn bản **tiếng Anh**, nên bảng mã hóa ưu tiên ghép các chuỗi byte phổ biến trong tiếng Anh. Một từ tiếng Anh thường gọn trong 1 token ("hello", "world" = 1 token), nhưng một từ tiếng Việt có dấu thanh như "Việt" hay "đường" thường bị tách thành **2–4 token** ("V"+"iệ"+"t", "đ"+"ư"+"ờ"+"ng") vì các cụm byte này hiếm gặp trong dữ liệu huấn luyện gốc. Thêm vào đó, thanh điệu và ký tự có dấu làm tăng entropy ký tự → BPE phải dùng nhiều token hơn để biểu diễn cùng một ý. Kết quả: cùng độ dài "từ", tiếng Việt tốn token gấp **1,5–2 lần** tiếng Anh, và đó cũng là lý do nên dùng `tiktoken` thay vì ước lượng "0.75 từ ≈ 1 token" khi tính chi phí thật cho văn bản tiếng Việt.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất ở những tình huống cần **trải nghiệm real-time và phản hồi dài**: chatbot hội thoại (user cần thấy model đang "suy nghĩ" từng phần để tin rằng hệ thống hoạt động, giảm cảm giác treo máy), các tác vụ sinh nội dung dài (viết bài, kể chuyện, code generation), và những nơi **time-to-first-token (TTFT)** quan trọng — thay vì chờ 5–10 giây để hiện cả đoạn văn, người dùng thấy chữ đầu tiên sau 200–500ms, cảm giác nhanh hơn rất nhiều. Ngược lại, **non-streaming phù hợp hơn** ở các tình huống: (1) **batch processing / overnight job** — không có người ngồi chờ, chỉ cần kết quả cuối; (2) **API-to-API call** — server gọi server, không có UX streaming nào cần cải thiện; (3) **kết quả ngắn** (vài chục token) — thời gian không khác biệt đáng kể nhưng code phức tạp hơn; (4) **yêu cầu đầu ra có cấu trúc** (JSON schema, function call) — cần đủ response để validate/parse toàn vẹn, nếu stream dở có thể nhận JSON không hợp lệ giữa chừng; (5) **retry an toàn hơn** — vì response hoàn chỉnh được lưu lại ngay, nếu kết nối rớt giữa chừng thì retry vẫn còn kết quả để dùng. Tóm lại: streaming tối ưu cho **perceived latency** của người dùng, non-streaming tối ưu cho **đơn giản, độ tin cậy và xử lý hàng loạt**.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> So với delay cố định, **exponential backoff** có ba lợi thế lớn khi API bị quá tải: (1) **cho hệ thống thời gian thở theo cấp số nhân** (1s → 2s → 4s → 8s...) — nếu API thật sự đang quá tải, càng retry sớm càng làm tình hình tệ hơn; delay tăng dần giúp giảm tải tức thì để backend có thời gian recover; (2) **phân tán retry trên trục thời gian** — khi mỗi client dùng hàm backoff kèm **jitter (random hóa)**, các lần retry không còn đồng pha, tránh hiện tượng "thundering herd"; (3) **thích nghi với mức độ tổn thương** — retry nhanh cho lỗi nhẹ, chờ lâu hơn cho lỗi nặng, thay vì cứng nhắc 1 giây. Ngược lại, nếu hàng nghìn client cùng retry với **delay cố định giống nhau**, xảy ra thảm họa **"retry storm"** (hay "synchronized retry"): tất cả client đều gặp lỗi 429/503 cùng lúc → cùng chờ 1 giây → cùng gửi lại đúng giây thứ 1 → API vừa kịp recover thì lại bị đánh sập ngay bởi đợt request khổng lồ → tiếp tục fail → tiếp tục retry đồng pha → vòng lặp **dogpile** kéo dài, có thể lan sang các service phụ thuộc và gây **cascade failure** toàn hệ thống. Đây chính là lý do các SDK lớn (AWS, OpenAI, Stripe) đều mặc định dùng exponential backoff + jitter — không phải vì nó nhanh hơn, mà vì nó **không đồng bộ** và **không giết chết hệ thống lúc đang thở**.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> **Persona em chọn: "Gia sư AI/ML thân thiện cho học viên Việt Nam".**
>
> **System prompt:**
> ```
> Bạn là "Anh Minh AI" — một gia sư AI/ML thân thiện, kiên nhẫn và chính xác.
> Nhiệm vụ: giúp học viên Việt Nam hiểu các khái niệm AI/LLM từ cơ bản đến
> nâng cao bằng tiếng Việt, kèm ví dụ thực tế và code Python ngắn gọn.
>
> QUY TẮC BẮT BUỘC:
> 1. LUÔN trả lời bằng tiếng Việt, trừ khi học viên yêu cầu cụ thể tiếng Anh
>    (thuật ngữ kỹ thuật, docstring, tên hàm API).
> 2. Trả lời NGẮN GỌN, đúng trọng tâm — tối đa 5–8 câu cho câu hỏi khái niệm,
>    dùng gạch đầu dòng cho danh sách, ưu tiên code example ≤15 dòng.
> 3. Dùng VÍ DỤ ĐỜI THƯỜNG Việt Nam (phở, chợ đêm, xe máy, tiếng Việt có dấu)
>    khi giải thích khái niệm trừu tượng.
> 4. Nếu không chắc chắn, hãy nói thẳng "Anh chưa rõ phần này, em nên tham khảo
>    tài liệu X" — TUYỆT ĐỐI không bịa đặt số liệu, API hay thư viện.
> 5. Khi viết code, LUÔN kèm giải thích 1 dòng cho mỗi đoạn quan trọng.
> ```
>
> **Giải thích 2 lựa chọn từ ngữ quan trọng:**
>
> - **"LUÔN trả lời bằng tiếng Việt, trừ khi học viên yêu cầu cụ thể tiếng Anh"** — Model GPT được train trên dữ liệu Anh/Việt lẫn lộn, khi gặp câu hỏi có thuật ngữ như "token", "embedding", "transformer" thì **mặc định hay nhảy sang tiếng Anh** hoặc trộn Anh-Việt rất khó đọc. Việc chỉ định rõ "LUÔN bằng tiếng Việt, **trừ khi** có yêu cầu cụ thể" tạo ra **default rõ ràng + ngoại lệ rõ ràng**, giúp model ít "tự quyết định sai" và output ổn định cho học viên Việt.
>
> - **"NGẮN GỌN, tối đa 5–8 câu... ưu tiên code example ≤15 dòng"** — Trong học tập, một response **dài 2 trang** gây quá tải nhận thức, học viên phải tự lọc ý chính. Thêm vào đó, mỗi token đều **tốn tiền và thời gian**: response 1000 token tốn ~3–5 lần so với 200 token, TTFT cũng lâu hơn. Chỉ định "5–8 câu + ≤15 dòng code" vừa **giữ chi phí thấp vừa ép model tập trung vào ý cốt lõi** — đây là **giới hạn cứng (hard constraint)** thay vì khuyến nghị mềm, model tuân thủ tốt hơn rất nhiều.
>
> Ngoài ra em còn để mục "**không bịa đặt số liệu, API, thư viện**" vì trong giáo dục, một thông tin sai sẽ được học viên tin và dùng lại — hậu quả lớn hơn nhiều so với chatbot bán hàng.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> **Hạn chế lớn nhất của trợ lý hiện tại: KHÔNG CÓ BỘ NHỚ DÀI HẠN (persistent memory).**
>
> Trong Part 4 mini-project, mỗi phiên (session) chat hoàn toàn **khớp lại từ đầu**: model không nhớ tên em, không biết em đang ở trình độ nào, không nhớ em từng hỏi về `tiktoken` hay đang làm project gì. Hệ quả:
> - Phải **lặp lại ngữ cảnh** ở mỗi câu hỏi mới → trải nghiệm tệ.
> - Model **không thể cá nhân hoá** (ví dụ: em thích ví dụ đời thường, model tuần trước biết nhưng tuần này lại quên).
> - Không có **learning continuity** — không thể xây dựng trợ lý đồng hành lâu dài.
> - Các hạn chế phụ khác em đã thấy: history chỉ 3 lượt (quên ngữ cảnh gần), không kiểm duyệt nội dung (có thể trả lời ngoài phạm vi giáo dục), không có RAG (kiến thức cố định theo ngày train).
>
> **Đề xuất cải thiện: thêm "Long-term Memory Layer" với file JSON + auto-extraction.**
>
> **Cách triển khai (4 bước, ~80 dòng Python):**
>
> 1. **Lưu trữ:** tạo `user_profile.json` lưu 3 loại thông tin:
>    ```json
>    {
>      "identity": {"name": "Thai Anh", "level": "mới học AI/ML"},
>      "preferences": {"language": "vi", "style": "ví dụ đời thường"},
>      "history": [{"date": "2026-09-10", "topic": "tiktoken BPE"}]
>    }
>    ```
> 2. **Inject vào system prompt:** trước mỗi lời gọi API, đọc file và nối vào system prompt dạng `[USER CONTEXT]\n{profile_json}\n[HẾT]`.
> 3. **Auto-extraction:** sau mỗi câu trả lời, gọi **một LLM call phụ** (hoặc dùng rule đơn giản) để rút trích thông tin đáng nhớ — ví dụ: "người dùng nói tên là X" → ghi vào `identity.name`; "em thích ví dụ đời thường" → ghi vào `preferences.style`.
> 4. **Update & persist:** ghi đè file `user_profile.json` sau khi cập nhật, dùng lock file để tránh xung đột nếu nhiều session song song.
>
> **Ví dụ minh họa sau khi triển khai:**
> - Hôm nay em hỏi: "Giải thích transformer" → model trả lời ngắn gọn, ví dụ bằng xe máy, ghi nhớ "em thích ví dụ đời thường".
> - 3 ngày sau em mở session mới, hỏi "Nhắc lại transformer giúp anh" → model nhớ context và vẫn dùng style tương tự, **không cần em lặp lại** "à anh dùng ví dụ đời thường nhé".
>
> Đây là bước đệm quan trọng để tiến tới một **"AI Companion"** thật sự, thay vì một chatbot "đãng trí" mỗi phiên.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
