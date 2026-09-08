# Domain & Learning Concepts — My Quizlet

**Status:** Baseline v0.1

Tài liệu này định nghĩa các khái niệm nghiệp vụ cốt lõi. Đây chưa phải database schema hay class design.

## 1. Core model

```text
Deck
 ├── Section (optional)
 │    └── Card
 └── Card

Card
 ├── LearningState        0..1 per user
 └── ReviewHistory        0..n

StudySession
 └── StudySessionItem
      └── Card
```

Mục tiêu của model là tách ba loại dữ liệu vốn dễ bị trộn lẫn:

1. **Learning content** — nội dung cần học.
2. **Long-term learning progress** — trạng thái ghi nhớ dài hạn.
3. **Study activity** — một phiên học cụ thể.

## 2. Deck

Deck là một bộ nội dung học có chủ đề chung.

Ví dụ:

- 新完全マスター N1 — Vocabulary;
- Mimikara Oboeru N1;
- English IELTS Vocabulary.

Deck không phải scheduler queue. Một deck có thể được dùng để tạo nhiều kiểu study session khác nhau.

### Deck responsibilities

- tổ chức card;
- metadata chung;
- section/chapter;
- cung cấp nguồn card để tạo session;
- thống kê tổng hợp.

Deck không trực tiếp quyết định card nào “được phép” học hôm nay.

## 3. Section

Section là cách nhóm card bên trong deck.

Ví dụ:

```text
新完全マスター N1
 ├── Chapter 01
 ├── Chapter 02
 └── Chapter 03
```

Section phục vụ:

- import theo giáo trình;
- browse;
- filter;
- study subset.

Section không phải một deck độc lập và không cần có scheduler riêng.

## 4. Card

Card là đơn vị nội dung học.

### 4.1 Generic fields

Tối thiểu:

- front/term;
- back/definition.

### 4.2 Vocabulary extension

Đối với từ vựng tiếng Nhật, card có thể có:

- term;
- reading;
- Vietnamese meaning;
- Sino-Vietnamese;
- explanation;
- etymology/semantic origin;
- example;
- note;
- tags.

Các field mở rộng là content, không phải learning progress.

### 4.3 Card identity

Việc sửa meaning/example không tạo một card learning state mới nếu người dùng vẫn coi đó là cùng một mục từ. Tuy nhiên detailed design cần cân nhắc audit/version nếu nội dung thay đổi lớn.

## 5. Learning State

LearningState là snapshot trạng thái ghi nhớ dài hạn của một card đối với một user.

Nó có thể chứa các thuộc tính như:

- scheduler state;
- difficulty;
- stability;
- due/next review time;
- last review time;
- lapse count;
- review count.

Tên field chính xác phụ thuộc scheduler implementation.

### 5.1 Learning State không phải lịch sử

LearningState chỉ cho biết “hiện tại card đang ở đâu”.

ReviewHistory trả lời “card đã đi đến đây như thế nào”.

Không được chỉ lưu LearningState rồi overwrite qua từng lần học.

### 5.2 Learning State không khóa việc học

Một card chưa đến hạn vẫn có thể được đưa vào Free Study.

Scheduler chỉ trả lời:

> Khi nào card nên được review để tối ưu ghi nhớ dài hạn?

Scheduler không trả lời:

> Người dùng có được phép xem card này hôm nay không?

## 6. Review History

ReviewHistory là append-only learning event log ở mức nghiệp vụ.

Một record có thể lưu:

- card;
- user;
- timestamp;
- rating/result;
- scheduler mode;
- response time;
- state before/after hoặc dữ liệu đủ để audit;
- source study session.

### 6.1 Tại sao phải giữ history

Review history cho phép:

- thống kê accuracy;
- tìm weak cards;
- đo lapse/relearning;
- phân tích retention;
- debug scheduler;
- đổi thuật toán trong tương lai;
- giải thích vì sao một card được xếp Due/Weak.

## 7. Study Session

StudySession là một phiên học hữu hạn do người dùng bắt đầu.

### 7.1 Session properties

Một session cần biểu diễn được:

- mode;
- card selection/filter;
- deck/section nguồn;
- started at;
- completed/abandoned state;
- optional settings: shuffle, direction, limit.

### 7.2 Session modes

Baseline:

- Flashcards;
- SRS Review;
- Free Study;
- Quiz (phase kế tiếp).

### 7.3 Session Item

StudySessionItem đại diện một card xuất hiện trong session.

Có thể lưu:

- order;
- shown/revealed state;
- answer/result;
- response time;
- completed time.

Session item không thay thế ReviewHistory. Chỉ SRS review event hợp lệ mới bắt buộc cập nhật scheduler state.

## 8. Nguyên tắc quan trọng: Progress ≠ Study Session

```text
                   ┌── SRS Review ──> update LearningState + ReviewHistory
                   │
Deck/Card source ──┼── Free Study ──> session activity, không reset SRS
                   │
                   ├── Flashcards ──> session activity
                   │
                   └── Quiz ────────> quiz/session result
```

Một user có thể học cùng một card 10 lần trong Free Study nhưng điều đó không đồng nghĩa 10 lần scheduler review.

Detailed design phải định nghĩa rõ mode nào được quyền mutate LearningState.

## 9. Card Selection

Card Selection là rule tạo tập card cho một session.

Baseline selectors:

- All;
- New;
- Due;
- Weak;
- Mastered;
- Random N;
- Section;
- Tag;
- Search/filter result.

Selector là query/filter, không phải permanent state của deck.

## 10. New / Due / Weak / Mastered

### 10.1 New

Card chưa có SRS review hợp lệ hoặc chưa có initialized learning state theo scheduler policy.

### 10.2 Due

Card có next review time <= thời điểm xét theo scheduler.

### 10.3 Weak

Weak là classification suy ra từ learning data, không phải lifecycle state cứng.

Candidate signals:

- recent Again/Hard;
- lapse count cao;
- accuracy thấp;
- stability thấp;
- response time cao;
- repeated failure trong các session gần đây.

### 10.4 Mastered

Mastered cũng là derived classification.

Candidate signals:

- stability vượt threshold;
- retrieval probability/retention mục tiêu cao;
- lịch review đã giãn đủ xa;
- ít lapse trong cửa sổ gần đây.

Weak và Mastered có thể thay đổi theo thời gian mà không cần chuyển state thủ công.

## 11. SRS Scheduler

MVP ưu tiên FSRS hoặc thuật toán SRS hiện đại tương đương.

Yêu cầu domain:

- scheduler được đóng gói sau interface/application service;
- UI không biết công thức scheduler;
- persistence không phụ thuộc trực tiếp vào một package cụ thể nếu có thể tránh;
- scheduler version nên có khả năng được lưu/audit nếu việc nâng cấp có thể thay đổi lịch review.

## 12. Restart / Reset

Restart learning là một use case riêng, không phải điều kiện để học lại deck.

### 12.1 Study All

```text
Study All
→ tạo session từ toàn bộ card
→ giữ LearningState
→ giữ ReviewHistory
```

### 12.2 Restart Learning

```text
Restart Learning
→ explicit destructive/progress-changing action
→ yêu cầu xác nhận
→ reset/reinitialize LearningState theo policy
→ mặc định vẫn giữ historical events
```

Không dùng chung hai khái niệm này trong UI hoặc API.

## 13. Import Model

Import là một workflow, không chỉ là endpoint upload file.

```text
Upload
  ↓
Parse
  ↓
Column Mapping
  ↓
Preview + Validation
  ↓
Confirm
  ↓
Persist transactionally
  ↓
Import Result
```

Import nên có khả năng báo lỗi theo row để sửa nguồn dữ liệu dễ dàng.

## 14. Ownership boundaries trong modular monolith

Gợi ý module nghiệp vụ ban đầu:

```text
Deck Management
  Deck / Section / Card / Tag

Import
  Parse / Mapping / Validation / Commit

Learning
  LearningState / ReviewHistory / Scheduler

Study
  StudySession / StudySessionItem / selection

Quiz
  Question generation / answer evaluation (later)
```

Đây là logical module boundaries, không phải microservices.

## 15. Invariants cần bảo vệ

### INV-001

Free Study không tự reset LearningState.

### INV-002

Study All không xóa ReviewHistory.

### INV-003

Một SRS review hợp lệ phải tạo history record nếu nó cập nhật LearningState.

### INV-004

Không được có trạng thái “deck đã học xong nên không thể học lại”.

### INV-005

Weak/Mastered là classification có thể tính lại, không làm mất trạng thái scheduler gốc.

### INV-006

Import failure không được tạo một tập dữ liệu nửa thành công nếu transaction mode đã chọn là atomic import.

### INV-007

Card content và learning progress phải có lifecycle độc lập: sửa nội dung không được mặc định xóa tiến độ.
