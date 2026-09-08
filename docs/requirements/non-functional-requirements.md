# Non-functional Requirements — My Quizlet

**Status:** Baseline v0.1

## 1. Architecture

### NFR-ARCH-001 — Modular monolith

Hệ thống ban đầu phải triển khai theo modular monolith. Không tách microservice nếu chưa có nhu cầu độc lập rõ ràng về scale, deployment hoặc ownership.

### NFR-ARCH-002 — Clear separation

Frontend, backend application/domain logic và persistence phải tách biệt đủ để:

- test độc lập;
- thay UI mà không viết lại domain logic;
- thay scheduler implementation mà không sửa toàn hệ thống;
- thêm client khác trong tương lai.

### NFR-ARCH-003 — API-first backend

Mọi chức năng chính phải đi qua ASP.NET Core Web API; frontend không truy cập trực tiếp database.

## 2. Technology constraints

### NFR-TECH-001 — Frontend

- React;
- TypeScript;
- Fluent UI được ưu tiên;
- responsive web.

### NFR-TECH-002 — Backend

- ASP.NET Core Web API;
- .NET phiên bản LTS/stable phù hợp tại thời điểm triển khai;
- dependency injection và configuration theo chuẩn .NET.

### NFR-TECH-003 — Database

- PostgreSQL;
- migration phải được quản lý bằng code;
- schema thay đổi phải reproducible giữa môi trường.

### NFR-TECH-004 — Container

Frontend/backend/database phải chạy được qua Docker/Docker Compose cho local development và deployment đơn giản.

## 3. Performance

### NFR-PERF-001 — Interactive response

Với tải cá nhân/thấp, các thao tác thông thường như mở deck, lấy card tiếp theo, lưu review nên có phản hồi cảm nhận nhanh và không phụ thuộc vào batch background.

### NFR-PERF-002 — Large personal decks

Thiết kế phải xử lý được deck ít nhất vài nghìn card mà không tải toàn bộ dữ liệu không cần thiết lên client trong mọi màn hình.

### NFR-PERF-003 — Pagination/filtering

Danh sách card phải hỗ trợ phân trang hoặc virtualized loading khi kích thước deck lớn.

## 4. Reliability & Data Integrity

### NFR-REL-001 — Transactional review

Một SRS review làm thay đổi LearningState và tạo ReviewHistory phải đảm bảo tính nhất quán transactionally.

### NFR-REL-002 — Import consistency

Import nhiều card phải có policy transaction rõ ràng. Với MVP ưu tiên atomic import: hoặc toàn bộ batch hợp lệ được commit, hoặc không commit batch lỗi.

### NFR-REL-003 — No silent data loss

Không được overwrite/xóa review history âm thầm khi người dùng học lại, sửa card hoặc chạy Free Study.

### NFR-REL-004 — Idempotency where needed

Các command có rủi ro double-submit như lưu review/import confirmation cần có biện pháp tránh tạo duplicate event do retry/double click.

## 5. Security

### NFR-SEC-001 — Secrets

Không hard-code password, connection string production, token hoặc secret vào source control.

### NFR-SEC-002 — Input validation

Backend phải validate input độc lập với frontend, đặc biệt với import file và nội dung text dài.

### NFR-SEC-003 — File upload safety

Import chỉ chấp nhận format được hỗ trợ, có giới hạn kích thước hợp lý và không thực thi nội dung file.

### NFR-SEC-004 — Authentication-ready

Nếu ứng dụng được public internet, hệ thống phải có authentication/authorization trước khi hỗ trợ nhiều user hoặc dữ liệu cá nhân ngoài phạm vi local/private deployment.

## 6. Privacy

### NFR-PRIV-001 — User-owned learning data

Deck, card và lịch sử học là dữ liệu của người dùng. Thiết kế nên hỗ trợ export/backup về sau.

### NFR-PRIV-002 — Minimal data collection

Không thu thập dữ liệu cá nhân không cần thiết cho chức năng học.

## 7. Usability

### NFR-UX-001 — Low-friction study

Từ Home hoặc Deck, người dùng phải có thể bắt đầu một phiên học chính trong tối đa vài thao tác rõ ràng.

### NFR-UX-002 — No scheduler lock-in feeling

UI không được tạo cảm giác card “không thể học” chỉ vì chưa Due.

### NFR-UX-003 — Clear destructive actions

Delete/Restart/Reset phải khác biệt rõ với Study/Review và cần confirmation khi có nguy cơ mất tiến độ hoặc nội dung.

### NFR-UX-004 — Keyboard-friendly desktop

Flashcard/review trên desktop nên hỗ trợ keyboard shortcut cho reveal, next và rating để học nhanh.

### NFR-UX-005 — Responsive

Các flow học chính phải dùng tốt trên desktop và mobile browser.

## 8. Maintainability

### NFR-MAIN-001 — Requirement traceability

Các feature/design/task quan trọng nên tham chiếu requirement ID tương ứng.

### NFR-MAIN-002 — Automated tests

Phải ưu tiên test cho:

- scheduler behavior;
- card selection;
- invariants Free Study vs SRS;
- review transaction;
- import validation.

### NFR-MAIN-003 — No duplicated business rules

Rule như Due/Weak/Mastered hoặc quyền update LearningState phải nằm ở backend/domain/application layer, không copy logic giữa nhiều UI component.

### NFR-MAIN-004 — Configuration

Các threshold có thể thay đổi như mastery/weak rules nên được cấu hình hoặc gom tập trung, không rải magic number trong code.

## 9. Observability

### NFR-OBS-001 — Structured logging

Backend dùng structured logging cho request lỗi và các command quan trọng.

### NFR-OBS-002 — Correlation

Các lỗi import/review phải đủ thông tin để truy vết request/session mà không cần log nội dung nhạy cảm quá mức.

### NFR-OBS-003 — Health check

Backend cần health endpoint để Docker/deployment kiểm tra trạng thái service và database dependency.

## 10. Deployment

### NFR-DEP-001 — Local startup

Developer phải có thể clone repo, cấu hình environment và chạy stack qua Docker Compose với số bước tối thiểu.

### NFR-DEP-002 — Environment configuration

Dev/test/prod khác nhau bằng configuration/environment variable, không bằng sửa source code.

### NFR-DEP-003 — Stateless API preference

API nên stateless ở mức request; study session state được lưu rõ ràng nếu cần resume, không phụ thuộc memory của một process backend.

## 11. Accessibility

### NFR-A11Y-001

Các action chính phải usable bằng keyboard và có label/semantic phù hợp với component library.

### NFR-A11Y-002

Không chỉ dùng màu sắc để biểu diễn đúng/sai hoặc trạng thái New/Due/Weak/Mastered.
