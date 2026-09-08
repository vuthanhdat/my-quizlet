# Functional Requirements — My Quizlet

**Status:** Baseline v0.1

Quy ước mức ưu tiên:

- **Must:** bắt buộc cho MVP.
- **Should:** ưu tiên cao nhưng có thể đi ngay sau MVP.
- **Could:** roadmap sau.

## 1. Deck Management

### FR-DECK-001 — Tạo deck — Must

Người dùng có thể tạo deck với ít nhất:

- tên;
- mô tả tùy chọn;
- ngôn ngữ/source language tùy chọn;
- target language tùy chọn.

**Acceptance criteria**

- Không cho phép tên rỗng.
- Sau khi tạo thành công, deck xuất hiện trong danh sách deck.

### FR-DECK-002 — Sửa deck — Must

Người dùng có thể sửa metadata của deck mà không ảnh hưởng card, review history hoặc learning state.

### FR-DECK-003 — Xóa deck — Must

Người dùng có thể xóa deck sau bước xác nhận.

**Acceptance criteria**

- UI phải cảnh báo rằng card và dữ liệu học liên quan sẽ bị xóa hoặc archive theo policy được chốt ở design.
- Không được xóa ngoài ý muốn chỉ bằng một click trực tiếp.

### FR-DECK-004 — Xem tổng quan deck — Must

Trang deck hiển thị tối thiểu:

- tổng số card;
- số card New;
- Due;
- Weak;
- Mastered;
- các hành động Study/Flashcards/Review.

### FR-DECK-005 — Section/Chapter — Should

Deck có thể chia thành section/chapter để phù hợp giáo trình như Shinkanzen N1 Chapter 1, Chapter 2...

Người dùng có thể bắt đầu học chỉ một section.

## 2. Card Management

### FR-CARD-001 — Tạo card — Must

Người dùng có thể tạo card thủ công.

Card tối thiểu phải hỗ trợ:

- term/front;
- definition/back.

### FR-CARD-002 — Structured vocabulary fields — Must

Card có thể lưu các field từ vựng mở rộng:

- reading;
- Vietnamese meaning;
- Sino-Vietnamese reading;
- explanation/etymology;
- example;
- note.

Không phải mọi field đều bắt buộc.

### FR-CARD-003 — Sửa card — Must

Người dùng có thể sửa nội dung card mà không làm mất review history của card.

### FR-CARD-004 — Xóa card — Must

Người dùng có thể xóa card với xác nhận.

### FR-CARD-005 — Tags — Should

Người dùng có thể gắn nhiều tag cho card, ví dụ:

- N1;
- Shinkanzen;
- Chapter-01;
- Finance;
- Idiom.

### FR-CARD-006 — Browse/Search cards — Must

Người dùng có thể xem danh sách card trong deck và tìm kiếm theo term/definition/reading.

### FR-CARD-007 — Filter cards — Should

Người dùng có thể lọc theo trạng thái học, tag, section hoặc keyword.

## 3. Import

### FR-IMP-001 — Import CSV — Must

Người dùng có thể import nhiều card từ CSV.

### FR-IMP-002 — Import TSV — Must

Người dùng có thể import nhiều card từ TSV.

### FR-IMP-003 — Column mapping — Must

Trước khi import, người dùng có thể map column của file vào field của card.

Ví dụ:

- `term` → Term;
- `reading` → Reading;
- `meaning` → Vietnamese Meaning;
- `sino_vietnamese` → Sino-Vietnamese;
- `explanation` → Explanation;
- `example` → Example.

### FR-IMP-004 — Preview import — Must

Hệ thống hiển thị preview một số dòng trước khi ghi dữ liệu.

### FR-IMP-005 — Import validation — Must

Hệ thống báo rõ:

- dòng lỗi;
- field thiếu bắt buộc;
- encoding/format không hợp lệ;
- duplicate theo rule được cấu hình trong bước design.

### FR-IMP-006 — Import không làm hỏng dữ liệu cũ — Must

Nếu import thất bại giữa chừng, hệ thống không được để deck ở trạng thái dữ liệu nửa vời ngoài policy đã định nghĩa.

## 4. Flashcard Mode

### FR-FLASH-001 — Xem mặt trước/mặt sau — Must

Người dùng có thể xem front, reveal back và chuyển card tiếp theo/trước đó.

### FR-FLASH-002 — Shuffle — Must

Người dùng có thể shuffle thứ tự card trong session.

### FR-FLASH-003 — Study all — Must

Người dùng có thể học toàn bộ card của deck bất kể trạng thái SRS.

**Acceptance criteria**

- Không yêu cầu reset deck.
- Không xóa hoặc tái tạo learning state.
- Không xóa review history.

### FR-FLASH-004 — Study subset — Must

Người dùng có thể tạo session từ:

- All;
- New;
- Due;
- Weak;
- Mastered;
- random N card;
- section được chọn.

### FR-FLASH-005 — Session progress — Must

Hiển thị vị trí hiện tại và tổng số card trong session.

## 5. SRS Review

### FR-SRS-001 — Due review — Must

Hệ thống xác định card đến hạn và cho người dùng bắt đầu daily review.

### FR-SRS-002 — Review rating — Must

Sau khi reveal đáp án, người dùng có thể đánh giá ít nhất bốn mức:

- Again;
- Hard;
- Good;
- Easy.

UI có thể dùng wording tiếng Việt thân thiện hơn nhưng backend phải giữ semantic rating ổn định.

### FR-SRS-003 — Update learning state — Must

Một lần SRS review hợp lệ cập nhật learning state của card, bao gồm tối thiểu next review time và dữ liệu cần cho scheduler.

### FR-SRS-004 — Preserve review history — Must

Mỗi review được lưu thành history record riêng, không overwrite review trước đó.

### FR-SRS-005 — New card handling — Must

Card chưa từng review được phân biệt với card đã học.

### FR-SRS-006 — Scheduler abstraction — Must

Business logic không được gắn cứng UI vào một thuật toán cụ thể. MVP có thể dùng FSRS, nhưng scheduler phải được đóng gói để có thể test/thay đổi phiên bản.

### FR-SRS-007 — Restart learning — Should

Người dùng có thể chủ động restart trạng thái học của card/deck thông qua thao tác riêng.

**Acceptance criteria**

- Có cảnh báo rõ.
- Không được dùng restart như cách bắt buộc để “study all”.
- Review history cũ phải được giữ hoặc xử lý theo tùy chọn rõ ràng; mặc định ưu tiên giữ history.

## 6. Free Study

### FR-FREE-001 — Free Study độc lập với scheduler — Must

Người dùng có thể học card bất kỳ mà không bị giới hạn bởi due queue.

### FR-FREE-002 — Free Study không reset SRS — Must

Bắt đầu hoặc hoàn thành Free Study không được reset learning state.

### FR-FREE-003 — Free Study result policy — Must

Kết quả Free Study phải được lưu tách biệt đủ để hệ thống biết đây không phải một SRS review chính thức, trừ khi người dùng chủ động chọn mode cập nhật SRS.

### FR-FREE-004 — Random N — Must

Người dùng có thể chọn một số lượng card ngẫu nhiên để học, ví dụ 10/20/50 hoặc nhập N hợp lệ.

## 7. Weak / Mastery Classification

### FR-STATE-001 — Weak cards — Must

Hệ thống có khái niệm Weak để chọn các card có dấu hiệu khó nhớ.

Rule ban đầu có thể dựa trên một hoặc nhiều yếu tố:

- Again/Hard gần đây;
- tỷ lệ sai;
- lapse count;
- thời gian phản hồi;
- stability thấp.

Rule chính xác sẽ được chốt ở detailed design.

### FR-STATE-002 — Mastered cards — Must

Hệ thống có khái niệm Mastered để phân loại card đã đạt mức ghi nhớ đủ cao theo rule cấu hình.

### FR-STATE-003 — State là derived classification — Must

Weak/Mastered không được thiết kế như trạng thái cứng làm mất thông tin SRS. Đây là classification có thể suy ra từ dữ liệu học hiện tại/history.

## 8. Study Session

### FR-SES-001 — Create study session — Must

Mỗi lần học tạo Study Session với:

- mode;
- nguồn card/filter;
- thời điểm bắt đầu;
- trạng thái session.

### FR-SES-002 — Session item tracking — Must

Hệ thống có thể ghi lại card nào đã xuất hiện trong session và kết quả tương tác khi mode yêu cầu.

### FR-SES-003 — Resume session — Should

Người dùng có thể tiếp tục session chưa hoàn thành.

### FR-SES-004 — Session summary — Must

Khi hoàn thành session, hiển thị tối thiểu:

- số card đã học;
- đúng/sai hoặc rating breakdown nếu mode có đánh giá;
- thời lượng session nếu có dữ liệu.

## 9. Quiz Mode

### FR-QUIZ-001 — Multiple choice — Should

Hệ thống có thể tạo câu hỏi trắc nghiệm từ card trong deck.

### FR-QUIZ-002 — Typing answer — Should

Người dùng có thể nhập đáp án thay vì chỉ reveal card.

### FR-QUIZ-003 — Direction — Should

Có thể đổi chiều câu hỏi, ví dụ:

- Japanese → Vietnamese;
- Vietnamese → Japanese.

### FR-QUIZ-004 — Quiz result — Should

Kết quả quiz được lưu theo session và không tự động reset SRS.

## 10. Home / Daily Study

### FR-HOME-001 — Dashboard summary — Must

Home hiển thị tối thiểu:

- Due today;
- New;
- Weak;
- tổng số deck hoặc recent decks.

### FR-HOME-002 — Start review — Must

Có hành động nhanh để bắt đầu review các card Due.

### FR-HOME-003 — Recent decks — Must

Người dùng có thể nhanh chóng mở các deck học gần đây.

### FR-HOME-004 — Streak — Could

Hiển thị streak học liên tục nếu có đủ dữ liệu.

## 11. User / Account

### FR-USER-001 — Single-user-friendly MVP — Must

MVP phải chạy được đơn giản cho một người dùng cá nhân.

### FR-USER-002 — Authentication-ready — Should

Kiến trúc không được khóa cứng vào single-user database design nếu dự kiến public deploy. Authentication có thể được đưa vào ngay MVP hoặc phase kế tiếp tùy deployment scope.

## 12. Future Capabilities

Các yêu cầu dưới đây chưa thuộc MVP:

### FR-FUT-001 — AI explanation — Could

Sinh giải thích từ vựng, Kanji/etymology và ví dụ.

### FR-FUT-002 — Generate quiz distractors — Could

AI tạo distractor có chất lượng cho multiple choice.

### FR-FUT-003 — Import from URL/PDF/text — Could

Tạo deck từ nguồn web, PDF hoặc text sau bước preview/approval.

### FR-FUT-004 — Audio — Could

Text-to-speech hoặc audio pronunciation cho card.

### FR-FUT-005 — PWA offline — Could

Cho phép học một phần deck khi mất mạng và sync lại sau.
