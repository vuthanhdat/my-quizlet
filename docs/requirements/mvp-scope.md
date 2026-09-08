# MVP Scope & Acceptance — My Quizlet

**Status:** Baseline v0.1

## 1. MVP objective

MVP phải đủ tốt để người dùng có thể dùng My Quizlet thay cho workflow flashcard cá nhân cơ bản hằng ngày:

1. tạo/import deck;
2. browse card;
3. học tự do toàn deck hoặc subset;
4. review card đến hạn bằng SRS;
5. giữ đầy đủ lịch sử học;
6. phân biệt rõ Free Study và SRS Review.

MVP không cần clone toàn bộ Quizlet.

## 2. In-scope MVP

### 2.1 Deck

- Create/Edit/Delete deck.
- List decks.
- Deck overview.
- Total/New/Due/Weak/Mastered counters.

### 2.2 Card

- Create/Edit/Delete card.
- Structured vocabulary fields.
- Browse/search card.
- Basic pagination.

### 2.3 Import

- CSV.
- TSV.
- Column mapping.
- Preview.
- Validation.
- Atomic commit.

### 2.4 Flashcard / Free Study

- reveal front/back;
- next/previous;
- shuffle;
- Study All;
- New;
- Due;
- Weak;
- Mastered;
- Random N;
- session progress.

### 2.5 SRS Review

- review Due cards;
- Again/Hard/Good/Easy;
- scheduler service;
- update LearningState;
- append ReviewHistory;
- daily due count.

### 2.6 Study Session

- session record;
- session items;
- completion status;
- basic summary.

### 2.7 Home

- Due today;
- New;
- Weak;
- recent decks;
- quick Start Review.

## 3. Explicitly out of MVP

- social/public deck marketplace;
- classroom/teacher features;
- realtime multiplayer;
- native mobile apps;
- AI generation from URL/PDF;
- automatic Kanji etymology generation;
- audio/TTS;
- matching game;
- advanced statistics dashboard;
- offline-first sync;
- public sharing/community moderation;
- complex gamification;
- microservices.

## 4. MVP screens

Baseline screen inventory:

### SCR-001 — Home

Hiển thị:

- Due;
- New;
- Weak;
- recent decks;
- Start Review.

### SCR-002 — Deck List

- list decks;
- search deck;
- create deck.

### SCR-003 — Deck Detail

- deck metadata;
- progress counters;
- Study/Review actions;
- card list entry point;
- import entry point.

### SCR-004 — Card Browser

- table/list cards;
- search;
- pagination;
- create/edit/delete.

### SCR-005 — Card Editor

Fields tối thiểu:

- Term;
- Reading;
- Meaning;
- Sino-Vietnamese;
- Explanation;
- Example;
- Note.

### SCR-006 — Import Wizard

Steps:

1. Upload.
2. Parse.
3. Map columns.
4. Preview/validate.
5. Confirm.
6. Result.

### SCR-007 — Study Setup

Cho phép chọn:

- All;
- New;
- Due;
- Weak;
- Mastered;
- Random N;
- shuffle.

### SCR-008 — Flashcard Study

- front;
- reveal back;
- next/previous;
- progress;
- keyboard shortcut.

### SCR-009 — SRS Review

- question/front;
- reveal;
- Again/Hard/Good/Easy;
- progress.

### SCR-010 — Session Summary

- cards completed;
- rating/result summary;
- duration nếu có;
- action học tiếp/quay lại deck.

## 5. Critical end-to-end acceptance scenarios

### AC-E2E-001 — Import → Study All

**Given** user có CSV 100 từ hợp lệ  
**When** user tạo deck, map columns, preview và confirm import  
**Then** deck có 100 card  
**And when** user chọn Study All  
**Then** session có thể duyệt đủ 100 card  
**And** không cần reset deck.

### AC-E2E-002 — SRS Review persists progress

**Given** một card Due  
**When** user review và chọn Good  
**Then** hệ thống tạo ReviewHistory record  
**And** cập nhật LearningState/next review  
**And** card không còn Due ngay sau transaction nếu scheduler tính như vậy.

### AC-E2E-003 — Free Study does not corrupt SRS

**Given** một card đã có LearningState và next review trong tương lai  
**When** user mở Study All/Free Study và xem card đó  
**Then** existing next review không bị reset/chuyển thành New  
**And** ReviewHistory SRS cũ vẫn còn nguyên.

### AC-E2E-004 — Repeated free study

**Given** deck đã học trước đó  
**When** user Study All nhiều lần trong cùng ngày  
**Then** mọi session đều có thể chạy bình thường  
**And** deck không bị khóa bởi trạng thái “completed”.

### AC-E2E-005 — Edit card preserves progress

**Given** card đã có review history  
**When** user sửa example hoặc meaning  
**Then** card content được cập nhật  
**And** review history/learning state không bị xóa mặc định.

### AC-E2E-006 — Atomic import failure

**Given** một import batch có lỗi validation theo policy atomic  
**When** user confirm import  
**Then** hệ thống không để một phần batch được commit ngoài policy  
**And** user nhận danh sách lỗi đủ để sửa dữ liệu.

## 6. MVP success criteria

MVP được coi là usable khi thỏa đồng thời:

- Có thể chạy full stack bằng Docker Compose.
- Có thể tạo deck và import ít nhất vài trăm card từ CSV/TSV.
- Có thể học toàn deck nhiều lần mà không reset progress.
- Có daily Due queue.
- SRS review tạo history và cập nhật due state chính xác.
- Free Study không phá SRS.
- Có thể search/browse/edit card.
- Các critical E2E scenario ở mục 5 có automated test hoặc test checklist rõ ràng.

## 7. Suggested implementation order

### M0 — Foundation

- React app.
- ASP.NET Core API.
- PostgreSQL.
- Docker Compose.
- migration baseline.

### M1 — Content Management

- Deck CRUD.
- Card CRUD.
- Card browser/search.

### M2 — Import

- CSV/TSV parsing.
- mapping/preview/validation.
- atomic import.

### M3 — Free Study

- StudySession.
- selectors.
- Study All / Random N / shuffle.
- flashcard UI.

### M4 — SRS

- LearningState.
- ReviewHistory.
- scheduler abstraction/FSRS integration.
- Due review.

### M5 — Home & polish

- counters.
- recent decks.
- session summary.
- responsive/keyboard UX.

## 8. Gate before detailed design

Detailed design có thể bắt đầu ngay sau baseline này. Các quyết định cần chốt trong design thay vì requirement gồm:

- exact database schema;
- FSRS library/implementation và scheduler versioning;
- exact Weak/Mastered formula;
- authentication có nằm trong MVP đầu tiên hay chỉ private/single-user deploy;
- section/tag schema;
- duplicate detection khi import;
- soft delete vs hard delete;
- exact API endpoints;
- frontend routing/state management;
- deployment topology.
