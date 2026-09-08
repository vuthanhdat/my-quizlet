# Product Requirements — My Quizlet

**Status:** Baseline v0.1  
**Product:** My Quizlet  
**Primary user:** Cá nhân tự học, trước mắt tập trung vào học từ vựng tiếng Nhật/JLPT.  
**Ngôn ngữ giao diện mặc định:** Tiếng Việt.

## 1. Bối cảnh

Các ứng dụng flashcard hiện tại thường rơi vào hai cực:

- Quizlet dễ bắt đầu, dễ học lại và có nhiều kiểu luyện tập, nhưng trải nghiệm miễn phí bị giới hạn/quảng cáo và không tập trung mạnh vào lịch ôn dài hạn.
- Anki có Spaced Repetition mạnh nhưng workflow học khá cứng. Người dùng dễ có cảm giác deck bị điều khiển bởi scheduler, khó học lại toàn bộ hoặc một tập tùy ý mà không đụng tới trạng thái tiến độ/reset.

My Quizlet được xây để kết hợp hai điểm mạnh: **trải nghiệm học tự do như Quizlet** và **ghi nhớ dài hạn bằng SRS**, nhưng tách hoàn toàn việc “học một phiên” khỏi “trạng thái tiến độ dài hạn”.

## 2. Product vision

> Người dùng có thể mở bất kỳ bộ từ nào và học ngay theo cách mình muốn, trong khi hệ thống vẫn âm thầm theo dõi lịch sử và tối ưu lịch ôn dài hạn.

Ứng dụng phải tạo cảm giác rằng **scheduler phục vụ người học**, không phải người học phục vụ scheduler.

## 3. Nguyên tắc sản phẩm

### P-01 — Progress không đồng nghĩa với Study Session

Progress/SRS state là trạng thái dài hạn của từng card. Study Session chỉ là một lần người dùng chọn một tập card để học.

Người dùng phải có thể học lại toàn deck, random 20 card, chỉ các card yếu, hoặc một subset tự chọn mà không phải reset tiến độ.

### P-02 — Không cần reset để học lại

“Study all” không được làm mất:

- lịch sử review;
- thông tin card đã/đang học;
- độ khó;
- lịch review kế tiếp;
- thống kê quá khứ.

Reset/restart chỉ là thao tác riêng, có chủ ý và có cảnh báo.

### P-03 — Vocabulary-first, không vocabulary-only

MVP tối ưu cho học từ vựng, đặc biệt tiếng Nhật, nhưng domain model phải đủ tổng quát để sau này hỗ trợ các loại flashcard khác.

### P-04 — Nội dung card giàu cấu trúc

Một card có thể có nhiều field, không chỉ `term` và `definition`. Với từ vựng tiếng Nhật có thể gồm:

- từ/cụm từ;
- cách đọc;
- nghĩa tiếng Việt;
- âm Hán Việt;
- giải thích Hán tự/nguồn gốc nghĩa;
- ví dụ;
- ghi chú;
- tags.

### P-05 — Import dễ hơn nhập tay

Người dùng phải có thể tạo deck lớn từ file thay vì nhập từng card. CSV/TSV là baseline của MVP.

### P-06 — Lịch sử là dữ liệu gốc

Hệ thống phải giữ review history thay vì chỉ giữ trạng thái hiện tại. Trạng thái học hiện tại có thể được cập nhật từ các lần review, còn lịch sử dùng cho thống kê, debugging và các thuật toán học sau này.

## 4. Mục tiêu sản phẩm

### G-01 — Học linh hoạt

Người dùng có thể bắt đầu học một deck trong vài thao tác và tự chọn tập card cần học.

### G-02 — Ghi nhớ dài hạn

Hệ thống hỗ trợ review theo lịch SRS và phân biệt rõ card đến hạn, card mới, card yếu và card đã thành thạo.

### G-03 — Không mất tiến độ

Các phiên Free Study/Quiz/Flashcard thông thường không được làm mất hoặc reset lịch SRS.

### G-04 — Dễ đưa dữ liệu vào

Một bộ từ vựng vài trăm hoặc vài nghìn từ phải có thể import nhanh bằng CSV/TSV.

### G-05 — Dùng được hằng ngày trên desktop và mobile browser

Frontend phải responsive và có thể phát triển thành PWA.

## 5. Non-goals giai đoạn đầu

MVP không nhằm clone toàn bộ Quizlet. Các chức năng sau nằm ngoài baseline v0.1 trừ khi được đưa vào roadmap sau:

- marketplace/public deck discovery;
- social feed, follower, classroom;
- realtime multiplayer game;
- teacher/class management;
- AI tự crawl/generate deck từ URL/PDF;
- speech recognition;
- native iOS/Android app;
- gamification phức tạp;
- microservices/distributed architecture.

## 6. User personas

### U-01 — Người học từ vựng có dữ liệu riêng

Có sẵn deck từ CSV/Excel/nguồn bên ngoài. Muốn import nhanh, học nhiều lần và không bị workflow SRS khóa.

### U-02 — Người học JLPT

Muốn mỗi từ có reading, nghĩa, âm Hán Việt, giải thích Kanji, ví dụ, tag theo giáo trình/chapter/JLPT level.

### U-03 — Người dùng SRS nhưng thích học tự do

Vẫn muốn hệ thống nhắc card đến hạn nhưng đôi khi muốn ôn cả deck, card yếu hoặc random subset mà không phá lịch review.

## 7. User journeys chính

### J-01 — Import và bắt đầu học

1. Tạo deck.
2. Import CSV/TSV.
3. Preview dữ liệu và mapping column → field.
4. Xác nhận import.
5. Mở deck.
6. Chọn Flashcards hoặc Learn/Review.

### J-02 — Review hằng ngày

1. Mở Home.
2. Xem số card Due/New/Weak.
3. Bấm Start Review.
4. Review từng card.
5. Đánh giá mức nhớ.
6. Hệ thống lưu review history và cập nhật SRS state.

### J-03 — Học lại toàn deck

1. Mở deck.
2. Chọn `Study all`.
3. Học toàn bộ hoặc shuffle.
4. Kết thúc session.
5. SRS progress/history trước đó vẫn còn nguyên.

### J-04 — Ôn từ yếu

1. Mở deck hoặc Home.
2. Chọn Weak cards.
3. Học/quiz subset đó.
4. Kết quả phiên học được lưu theo policy của mode nhưng không reset deck.

## 8. Chỉ số sản phẩm ban đầu

Các metric cần có khả năng đo được từ dữ liệu:

- số card học/ngày;
- số review/ngày;
- completion rate của study session;
- số card Due/New/Weak/Mastered;
- accuracy theo deck/card;
- số lần trả lời sai liên tiếp;
- thời gian phản hồi trung bình;
- retention theo khoảng thời gian khi thuật toán đủ dữ liệu.

MVP chưa cần dashboard phức tạp, nhưng data model không được làm mất khả năng tính các metric này.

## 9. Tech constraints

- Frontend: React + TypeScript.
- UI: Fluent UI được ưu tiên để giữ trải nghiệm nhất quán và tăng tốc phát triển.
- Backend: ASP.NET Core Web API (.NET).
- Database: PostgreSQL.
- Local/deployment packaging: Docker và Docker Compose.
- Kiến trúc ban đầu: modular monolith; không dùng microservice nếu chưa có lý do thực tế.
- API và database phải tách biệt khỏi UI để sau này có thể thêm client khác.

## 10. Definition of product baseline

Baseline v0.1 được coi là đủ rõ để đi sang design khi:

- scope MVP đã được chốt;
- functional requirements có ID và acceptance criteria;
- mô hình khái niệm Deck/Card/Study Session/Learning State/Review History được thống nhất;
- nguyên tắc Free Study không phá SRS được xác nhận;
- import format và field model ban đầu đủ để thiết kế database/API.
