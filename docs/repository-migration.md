# Tách YangHooks thành repository độc lập

Ngày chuẩn bị: 04/11/2026. Đây là chuyển vị trí mã và cập nhật tài liệu/CI,
không phải viết lại ứng dụng hoặc tạo lịch sử phát triển mới.

## Nguồn chính xác

- Repository nguồn: [nguyenhonggiang2407/DTDD](https://github.com/nguyenhonggiang2407/DTDD).
- Nhánh nguồn: [yanghooks](https://github.com/nguyenhonggiang2407/DTDD/tree/yanghooks).
- Commit nguồn: [`6831b9635107ff848e11a53eb20c3c5d9b7c7f20`](https://github.com/nguyenhonggiang2407/DTDD/commit/6831b9635107ff848e11a53eb20c3c5d9b7c7f20).
- Repository đích: [nguyenhonggiang2407/YangHooks](https://github.com/nguyenhonggiang2407/YangHooks), nhánh `main`.

Clone nhánh nguồn bằng `--single-branch` **không dùng `--depth`**, giữ cả bảy
commit reachable từ commit nguồn. Tạo nhánh `main` từ đúng commit đó; commit
chuyển repository nối tiếp lịch sử cũ. Không filter, squash, rebase hoặc thay
author/date của commit. Nhánh `yanghooks` trong DTDD không bị sửa hoặc xóa.

Trong bản chuẩn bị, remote nguồn được đổi tên thành `upstream` trước khi thêm
remote đích để tránh push nhầm vào DTDD. Một clone mới từ YangHooks sẽ chỉ có
remote của repository đích; thông tin nguồn vẫn nằm trong tài liệu này và lịch sử.

## Phạm vi thay đổi

- README dùng clone URL và CI badge của YangHooks, bổ sung lệnh CLI PowerShell.
- Workflow dùng `main` cho push, pull request và bước lưu screenshot đã kiểm tra.
- Cấu trúc source, SQLite schema, nghiệp vụ HMAC/replay, package lock và cấu hình
  runtime được giữ nguyên. Không chuyển dữ liệu hoặc khóa của máy đang chạy.
- Screenshot vẫn dùng đường dẫn tương đối `docs/screenshots/`; giữ ảnh thật từ
  source. Browser test chụp lại để kiểm tra, không dựng ảnh thay giao diện.

## Giấy phép và ghi nguồn

Giữ nguyên byte của `LICENSE`, `NOTICE.md` và toàn bộ `upstream/`. Git object tại
commit nguồn:

| Nội dung         | Git object SHA                             |
| ---------------- | ------------------------------------------ |
| `LICENSE`        | `86f0abeafd90fb3c5f3a29e16d3269515a757ba7` |
| `NOTICE.md`      | `7dcf657bd136181293d1b0e47169a2930fedeb89` |
| Tree `upstream/` | `70e98faca07bcf9d8ee4c1d02492beccba63e316` |

Nguồn Motia MIT của patchy631 qua fork Trinhvhao, snapshot và phần triển khai
mới được phân định trong [NOTICE.md](../NOTICE.md). Việc tách repository không
thay đổi tác giả hoặc điều khoản sử dụng của nguồn tham chiếu.

## Kiểm chứng cục bộ

Windows, Node.js 24.19.0; cài dev dependency từ lockfile bằng `npm ci`.

| Kiểm tra           | Kết quả                                                                           |
| ------------------ | --------------------------------------------------------------------------------- |
| `npm test`         | 22/22 qua, gồm HMAC, replay, owner, quota, restart và CLI HTTP thật               |
| `test/browser.mjs` | 4/4 qua, gồm gửi/receipt/export, xoay khóa, replay/lỗi và mobile                  |
| Browser sử dụng    | Google Chrome cài sẵn; preload ngoài repository chỉ chọn executable khi launch    |
| Docker             | Không có Docker CLI trên máy audit; chưa build/run container trong lần chuyển này |

Browser test dùng cùng file test của source, backend SQLite in-memory và HTTP
loopback thật. Bản Playwright đã khóa yêu cầu Chromium riêng; khi tái tạo môi
trường tiêu chuẩn, chạy `npx playwright install chromium` rồi `npm run test:ui`.
Không đổi test để bỏ qua assertion.

Máy audit có `core.autocrlf=true`, nên `npm run format:check` mặc định báo khác
line ending ở các file nguồn được checkout thành CRLF. Kiểm tra
`prettier --check . --end-of-line auto` qua; không format lại mã nghiệp vụ hoặc
NOTICE chỉ để đổi line ending của máy audit. CI Linux tiếp tục kiểm tra định dạng
chuẩn của Git source.

Ứng dụng chưa có demo internet được xác minh. Dockerfile/Compose được giữ nguyên.

## CI sau khi xuất bản

Kiểm tra ngày 05/11/2026: [run 41748757517](https://github.com/nguyenhonggiang2407/YangHooks/actions/runs/41748757517)
đã hoàn tất thành công ngày 04/11/2026 cho commit
[`25cf4809820900c7839998e50c8a40d2fb5bd4d8`](https://github.com/nguyenhonggiang2407/YangHooks/commit/25cf4809820900c7839998e50c8a40d2fb5bd4d8).
Các bước backend test, browser test và build/smoke-test container đều thành công;
đây là kết quả CI của commit đó, không phải một lần chạy Docker local trong đợt
chuyển repository. Xem [workflow và các lần chạy mới hơn](https://github.com/nguyenhonggiang2407/YangHooks/actions/workflows/ci.yml).

Các giới hạn bảy ngày workspace, secret lưu đọc được trong SQLite và phạm vi chạy
cá nhân vẫn áp dụng như README.
