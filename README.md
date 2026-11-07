# Event Streaming & CDC Platform

**Module 03 — Distributed & Real-Time Data Engineering · Project 07 | OmniCommerce Data Platform**

> Xây dựng luồng thay đổi đơn hàng theo thời gian gần thực, từ nguồn giao dịch đến event stream có quản trị offset và schema.

**Trạng thái: Planned — repository định hướng học tập, CHƯA có implementation.** README này trình bày phạm vi dự kiến, không phải tuyên bố hệ thống đã chạy hoặc đạt benchmark.

## 1. Tổng quan và bài toán nghiệp vụ

OmniCommerce là doanh nghiệp thương mại điện tử giả lập dùng xuyên suốt lộ trình Data Engineering. **Bài toán của Project 07:** Báo cáo batch cập nhật chậm và cần phản ánh đơn hàng vừa phát sinh/thay đổi. Sự kiện có thể trễ, lặp, đến sai thứ tự hoặc consumer crash.

Dự án được thiết kế để chứng minh không chỉ khả năng dùng công cụ mà cả cách phân tích yêu cầu, thiết kế giải pháp, nhận diện tình huống lỗi, kiểm thử và vận hành có thể giải thích được.

## 2. Mục tiêu và giá trị mang lại

- **Business goal:** Xây dựng luồng thay đổi đơn hàng theo thời gian gần thực, từ nguồn giao dịch đến event stream có quản trị offset và schema.
- **Engineering goal:** Hiểu kiến thức nền tảng, nắm trade-offs, viết implementation có test và tài liệu thiết kế.
- **Portfolio goal:** Có demo tái lập được, requirements, architecture/ADR, test evidence và hướng dẫn vận hành.
- **Phạm vi nguồn dữ liệu:** Bảng orders trên PostgreSQL / sự kiện commerce.

**Kết quả dự kiến:** một dự án có thể trả lời rõ *tại sao xây dựng*, *đầu vào/đầu ra là gì*, *hành vi khi lỗi*, *cách kiểm tra* và *giới hạn hệ thống*.

## 3. Technology Stack (dự kiến)

| Nhóm | Công nghệ / chủ đề | Vai trò |
|---|---|---|
| Core tools | Apache Kafka, Kafka Connect, Debezium (milestone CDC), PostgreSQL, Docker Compose, Python, schema validation | Xây dựng các thành phần chính theo từng milestone |
| Engineering knowledge | Topics, partitions, keys, consumer groups, offsets, delivery semantics, CDC snapshot, outbox pattern (so sánh), schema evolution, DLQ | Kiến thức phải hiểu và có thực hành trước khi đánh dấu hoàn thành |
| Documentation | Markdown, Mermaid, ADR, runbook | Ghi lại kiến trúc và quyết định |
| Verification | Tests, fixtures, failure injection, benchmark khi phù hợp | Chứng minh hành vi thay vì chỉ mô tả |

**Nguyên tắc:** Công nghệ có thể thay đổi khi phân tích trade-offs. Không cài đặt tất cả cùng lúc; chỉ thêm công cụ khi có bài toán yêu cầu.

## 4. Kiến trúc mục tiêu — Conceptual Workflow


definition: mô tả một luồng logic dự kiến, chưa phải sơ đồ của hệ thống đang chạy.

```mermaid
flowchart TD
    A0["Mô phỏng event producer và topic"] --> A1["Mô phỏng event producer và topic"]
    A1["Viết consumer lưu offset có kiểm soát"] --> A2["Viết consumer lưu offset có kiểm soát"]
    A2["Mô phỏng duplicate/restart/rebalance"] --> A3["Mô phỏng duplicate/restart/rebalance"]
    A3["CDC PostgreSQL với Debezium"] --> A4["CDC PostgreSQL với Debezium"]
    A4["Kiểm tra ordering theo key và schema evolution"] --> A5["Kiểm tra ordering theo key và schema evolution"]
```

**Điểm cần xác định trong System Design:** dữ liệu đầu vào, hợp đồng dữ liệu/schema, nơi lưu trạng thái, boundary transaction/idempotency, lỗi tạm thời vs lỗi vĩnh viễn, monitoring và các giới hạn mở rộng.

## 5. Functional Requirements

| ID | Requirement | Trạng thái |
|---|---|---|
| FR-01 | Publish order events có schema | Chưa triển khai |
| FR-02 | Consume và ghi nhận offset/processing result | Chưa triển khai |
| FR-03 | Xử lý duplicate theo event identity | Chưa triển khai |
| FR-04 | CDC insert/update/delete có policy | Chưa triển khai |
| FR-05 | DLQ hoặc error channel cho event lỗi | Chưa triển khai |
| FR-06 | Có failure/restart tests | Chưa triển khai |

Các yêu cầu sẽ được tách thành tickets nhỏ, mỗi ticket có inputs/outputs, các trường hợp lỗi và acceptance tests trước khi implementation.

## 6. Non-functional Requirements

- **NFR-01:** Thứ tự trong partition được hiểu và kiểm tra.
- **NFR-02:** Recovery không âm thầm mất event.
- **NFR-03:** Tách cấu hình/credentials khỏi code.
- **NFR-04:** Có đo consumer lag và throughput.


Không tự đặt ra kết quả như “exactly-once”, “99.9% uptime”, “tối ưu 30% chi phí” hoặc “xử lý 1 triệu events/s” trước khi có phạm vi và phép đo xác thực.

## 7. Engineering Challenges & Trade-offs

- **Challenge 01:** At-least-once và duplicate processing. Cần document các phương án, điều kiện áp dụng và test kiểm chứng.
- **Challenge 02:** Offset commit trước/sau side effect. Cần document các phương án, điều kiện áp dụng và test kiểm chứng.
- **Challenge 03:** Ordering chỉ trong một partition. Cần document các phương án, điều kiện áp dụng và test kiểm chứng.
- **Challenge 04:** Schema changes và tombstones. Cần document các phương án, điều kiện áp dụng và test kiểm chứng.


## 8. Milestones / Learning Roadmap

| Milestone | Nội dung và deliverable dự kiến | Tiến độ |
|---|---|---|
| M1 | Kafka fundamentals + local Compose | Chưa bắt đầu |
| M2 | Producer/consumer + partition semantics | Chưa bắt đầu |
| M3 | Offset management + failure injection | Chưa bắt đầu |
| M4 | Debezium CDC integration | Chưa bắt đầu |
| M5 | DLQ, monitoring và verification | Chưa bắt đầu |

**Cách học mỗi checkpoint:** Problem → Concept → Tiny Example → Feynman (≥ 8/10) → Implementation → Tests/Evidence → Commit Git. Học từng khái niệm, không học dồn nhiều kỹ thuật trong một lần.

## 9. Acceptance Criteria — Definition of Done

- [ ] **AC-01:** Event insert/update được truyền từ DB sang consumer.
- [ ] **AC-02:** Thử restart consumer và giải thích duplicate/loss conditions.
- [ ] **AC-03:** Test key partition ordering và schema change.
- [ ] **AC-04:** Có evidence offset/lag/recovery.

- [ ] Có README phản ánh đúng khả năng hiện tại, không nhầm planned với implemented.
- [ ] Có tài liệu kiến thức, architecture/ADR và runbook tương ứng khi cần.
- [ ] Test evidence ghi rõ environment, input, command, expected/actual và hạn chế.

**Hoàn thành:** Chỉ chuyển milestone/requirement sang Completed khi có code chạy thực tế và evidence phù hợp, không phải khi mới có tài liệu mô tả.

## 10. Repository Structure (định hướng, chưa tạo)

```text
kafka-event-streaming-cdc-platform/
├── README.md                 # Hiện tại chỉ có file này
├── docs/                     # Tạo khi có bài học/requirements/ADR
│   ├── 01-knowledge/
│   ├── 02-design/
│   ├── 03-decisions/
│   ├── 04-operations/
│   └── 05-evidence/
├── src/ hoặc scripts/         # Tạo khi bắt đầu code
├── tests/                    # Tạo cùng implementation
└── config/                   # Tạo khi cần môi trường
```

**Lưu ý:** Không tạo thư mục trống chỉ để trông như dự án đã triển khai. Không commit credentials, PII thật, dumps hoặc runtime data nhạy cảm.

## 11. Current Progress & Next Steps

| Nội dung | Trạng thái |
|---|---|
| Business problem, goals, technology scope, draft requirements | Planned trong README |
| Source code / working pipeline | Chưa triển khai |
| Unit / integration / failure tests | Chưa triển khai |
| Benchmarks và kiểm thử vận hành | Chưa triển khai |

**Bước đầu tiên khi bắt đầu Project 07:** Xác minh lại business scenario và requirements, học khái niệm nền tảng đầu tiên, sau đó tạo ticket nhỏ có acceptance criteria. AI Agent chỉ được giao implementation sau khi đã đủ rõ đầu vào, đầu ra, edge cases và cách review.

## 12. Liên kết trong lộ trình

- **Module:** 03 — Distributed & Real-Time Data Engineering.
- **Project:** 07/11.

