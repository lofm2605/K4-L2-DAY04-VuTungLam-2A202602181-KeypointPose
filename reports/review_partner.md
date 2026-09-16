# Review - tự kiểm bài của Vũ Tùng Lâm

- Người gán: Vũ Tùng Lâm
- Người kiểm: Vũ Tùng Lâm (**tự kiểm**; tôi làm bài một mình nên không có bạn cùng nhóm để kiểm chéo)
- Ngày: 2026-09-16
- Checklist đã điền: [`reports/REVIEWER_CHECKLIST.md`](REVIEWER_CHECKLIST.md)

> Bản này không thay được kiểm chéo thật. Nếu giảng viên ghép cặp với người khác, phần review
> của họ sẽ được bổ sung vào cuối file.

## Cách kiểm

1. `tools/check_pose_labels.py`: ĐẠT định dạng, 0 lỗi, 6 cảnh báo (đã xem từng cái, đều là báo nhầm - xem mục cuối).
2. `tools/visualize_pose.py` → soi 20 ảnh `outputs/vis_train/`, tìm 4 loại lỗi: đảo trái/phải, nhầm người, keypoint trôi, xoá khớp bị che.
3. Đối chiếu với `outputs/eval_vs_gold.json` để đo độ lệch bằng pixel.

"Người thứ" = thứ tự skeleton trong file nhãn `dataset/labels/train/<ảnh>.txt`.
Tọa độ tính bằng pixel trên ảnh gốc.

## Lỗi tìm được

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_03.jpg` | 2 (người đàn ông phía sau, bên trái) | `right_elbow` | **Nhầm người**: chấm ở (256, 195), nằm trên khuỷu tay phải của người đội mũ phía trước | **Đã sửa** trước khi khóa nhãn: dời về (244, 224), theo cánh tay trên từ vai phải của chính người này |
| `train_03.jpg` | 2 | `right_wrist` | Lệch 54 px: chấm ở (305, 163), cao hơn cổ tay thật | **Đã sửa**: dời về (296, 223), giữ v=1 |
| `train_15.jpg` | 1 | `left_knee` | Lệch nhẹ 56 px: chấm ở (157, 374), cao hơn và lệch phải so với gối | Kéo xuống-sang trái khoảng (104, 393), dọc theo trục đùi |
| `train_15.jpg` | 1 | `left_ankle` | Lệch nhẹ 59 px: chấm ở (146, 478), cao hơn mắt cá | Kéo xuống-sang trái khoảng (98, 512), dọc theo ống chân |
| `train_15.jpg` | 1 | `right_ankle` | Lệch nhẹ 44 px: chấm ở (78, 475), cao hơn mắt cá | Kéo thẳng xuống khoảng (78, 519) |
| `train_04.jpg` | 1 | `left_wrist` | Lệch nhẹ 44 px: chấm ở (367, 362), vượt quá cổ tay theo hướng cẳng tay | Kéo ngược về phía khuỷu tay, khoảng (323, 361), giữ v=1 |
| `train_01.jpg` | 1 | `right_wrist` | Lệch nhẹ 25 px: chấm ở (333, 301) | Dời sang phải khoảng (358, 297), giữ v=1 |
| `train_13.jpg` | 1 | `left_ankle` | Lệch nhẹ 19 px: chấm ở (39, 235) | Dời sang trái khoảng (21, 229) |

Không tìm thấy lỗi đảo trái/phải, lỗi xoá khớp bị che, hay dấu hiệu dùng `Hidden`.
6 lỗi lệch nhẹ còn lại **chưa sửa** và được giữ nguyên khi khóa nhãn: đều dưới 1.5 lần
bán kính dung sai, và không skeleton nào dưới OKS 0.75 (thấp nhất: `train_15` người #1, 0.778).

## Cảnh báo của công cụ đã xem và giữ nguyên

| Ảnh | Người thứ | Cảnh báo | Kết luận |
| --- | ---: | --- | --- |
| `train_02.jpg` | 1 | Vai/hông ngược chiều so với hai mắt | Không phải lỗi: người đạp xe quay đầu, trái/phải đặt theo thân. Gold giống, OKS 0.94 |
| `train_16.jpg` | 2 | Vai/hông ngược chiều so với hai mắt | Không phải lỗi: người nhảy bắt đĩa đang xoay người. Gold giống, OKS 0.97-0.99 |
| `train_04.jpg` | 1 | 7 khớp v=0 dù "nằm gọn giữa ảnh" | Không phải lỗi: người bị cắt ở mép dưới từ hông trở xuống; gold cũng v=0 |
| `train_04.jpg` | 2 | 4 khớp v=0 dù "nằm gọn giữa ảnh" | Không phải lỗi: gối và cổ chân nằm dưới mép ảnh; gold cũng v=0 |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất: lệch vị trí ở **đầu chi** (cổ tay, gối, cổ chân) - 6/6 lỗi lệch nhẹ còn lại - và lệch cờ v=1/v=2 ở **tai (13)** và **hông (10)** so với gold.
- Lỗi **thao tác** hay lỗi **guideline chưa rõ**: lệch cờ là **guideline chưa rõ** (lệch theo cả hai chiều ở cùng khớp → đã thêm luật hông và tai vào `GUIDELINE_MINI.md`); lệch vị trí đầu chi là **lỗi thao tác** khi ước lượng khớp bị che không bám theo trục chi.
