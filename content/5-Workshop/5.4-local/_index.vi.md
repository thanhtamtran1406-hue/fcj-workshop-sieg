---
title: "Bước 1 — Chạy và kiểm tra trên máy"
date: 2026-11-26
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Hãy chạy ứng dụng trên máy của bạn trước, để biết mã nguồn, các bài kiểm thử và mô hình dữ liệu hoạt động tốt rồi mới tạo tài nguyên trên AWS.

```sh
pnpm install --frozen-lockfile
pnpm setup
pnpm dev
pnpm check && pnpm build && pnpm test:smoke
```
`pnpm setup` tạo file `.env.local` cục bộ; không đưa file này vào Git.

**Kết quả mong đợi:** `pnpm check` và `pnpm build` chạy không lỗi, bộ smoke đạt, và website mở được trên máy với thực đơn đã tải.
