# Reviewer checklist - điền khi kiểm bài người khác

Người gán: Vũ Tùng Lâm   Người kiểm: Vũ Tùng Lâm (**tự kiểm** - làm bài một mình, không có bạn cùng nhóm)   Ngày: 2026-09-16

> Đây là bản tự kiểm, không thay được kiểm chéo thật: người tự kiểm dễ bỏ sót lỗi do chính
> cách hiểu guideline của mình. Mỗi ô "Đạt" dưới đây có bằng chứng từ công cụ hoặc ảnh
> `outputs/vis_train/`.

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

Đã chạy lệnh 1 và 2 trên `dataset/labels/train`. Không chạy được lệnh 3 vì không có bài người khác.

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ | 29 skeleton, dòng nào cũng có 56 số; khớp với gold 29/29 người, không thiếu, không thừa |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☑ | Công cụ cảnh báo `train_02` người #1 và `train_16` người #2. Soi ảnh: cả hai đang quay đầu/xoay người, trái/phải đặt theo thân; gold đặt giống (OKS 0.94 và 0.97-0.99, 0 lỗi `dao_trai_phai`) |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | Đã sửa `train_03` người thứ 2 `right_elbow` (từng nằm trên khuỷu tay người bên cạnh). Sau rework: 0 lỗi `nham_nguoi` |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☑ | 126 điểm v=1, tất cả đều có tọa độ; 0 lỗi `xoa_khop_bi_che` |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☑ | 27 điểm v=0, đều ở chân/hông của người bị cắt ở mép dưới: `train_01`, `train_04`, `train_07`, `train_10`, `train_11`, `train_13`. Cảnh báo `train_04` là báo nhầm (xem GUIDELINE_MINI ca 3) |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ | Soi 20 ảnh `outputs/vis_train/`: không có điểm v=2 nằm ngoài cơ thể. 6 điểm `lech_nhe` còn lại đều nằm gần đúng khớp (≤ 1.5 lần dung sai) |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ | `annotations/coco_keypoints/person_keypoints_default.json`: 20 ảnh, 29 annotation, mọi `keypoints` dài 51, category `person` |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | 29/29 dòng có 56 số; `data.yaml` có `kpt_shape: [17, 3]` |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☐ | Đã có `reports/visibility_report.md` + `outputs/visibility_report.json`. **Chưa đặt cạnh bảng của người khác** vì làm một mình |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☑ | 6 luật có ảnh mẫu + 4 ca mơ hồ (`train_03`, `train_06`, `train_04`, `train_02`) |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☑ | "ĐẠT định dạng", 0 lỗi; 6 cảnh báo đã xem hết ở mục 2 và 5 |

## Lỗi tìm được

Chi tiết ở `reports/review_partner.md`. Tóm tắt: không còn lỗi nặng, còn 6 điểm lệch nhẹ.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_15.jpg` | 1 | `left_knee`, `left_ankle`, `right_ankle` | Lệch nhẹ 44-59 px, chấm đặt cao hơn khớp thật | Kéo xuống theo trục ống chân tới đúng gối/mắt cá |
| `train_04.jpg` | 1 | `left_wrist` | Lệch nhẹ 44 px, chấm vượt quá cổ tay theo hướng cẳng tay | Kéo ngược về phía khuỷu tay |
| `train_01.jpg` | 1 | `right_wrist` | Lệch nhẹ 25 px | Dời sang phải về đúng cổ tay |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này: đặt chấm lệch ở **đầu chi** (cổ tay, gối, cổ chân), phần lớn là khớp bị che hoặc bị xe/vật chắn - 6/6 lỗi `lech_nhe` còn lại đều rơi vào cổ tay/gối/cổ chân - và lệch cờ v=1/v=2 ở **tai và hông** so với gold.
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**? Chủ yếu là **guideline chưa rõ**: lệch cờ xảy ra theo cả hai chiều ở cùng một khớp, nghĩa là chưa có luật cố định; đã bổ sung luật cho hông và tai vào `GUIDELINE_MINI.md`. Các điểm lệch vị trí là lỗi **thao tác** (ước lượng khớp bị che chưa theo trục chi).
