# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Vũ Tùng Lâm   Nhóm: ⚠️ CẦN ĐIỀN   Ngày: 2026-09-16

> Số liệu lấy từ `reports/visibility_report.md`, `outputs/visibility_report.json` và
> `outputs/eval_vs_gold.json` do công cụ sinh ra; không ước lượng.
> Các chỗ đánh dấu ⚠️ CẦN ĐIỀN cần số liệu hoặc nhận định của chính người gán.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 340 / 126 / 27 |
| Thời gian trung bình mỗi ảnh | 5 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` - 59% (17/29)
2. `right_ear` - 41% (12/29)
3. `left_eye` - 34% (10/29)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

⚠️ CẦN ĐIỀN (2-4 câu). Gợi ý từ số liệu: tai và mắt có `%v=1` cao vì hay bị tóc, mũ hoặc
góc nghiêng mặt che (hay bị che). Nhưng khớp có vị trí khó xác định lại là hông: trong
`outputs/eval_vs_gold.json`, hông có 10 lần lệch cờ so với gold, tai có 13 lần (số trước rework).

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.941 | 0.945 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 1 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

Ngoài ra: `lech_nhe` 8 → 6; `co_khac_gold` 48 → 47; `gold_khong_gan_nhan` 74 → 74.
Sau rework: "Không có skeleton nào cần rework" - mọi người OKS >= 0.75, không còn lỗi đã phân loại.

**Tôi đã sửa gì giữa hai lần chạy** (tọa độ pixel, ảnh 427×640, từ diff file nhãn giữa các bản export):

- `train_05.jpg`, người #1, `right_wrist`: kéo từ (123, 334) lên (123, 318) - hết lỗi `lech_nhe`.
- `train_05.jpg`, người #1, `nose` / `left_eye` / `right_eye` / `right_ear`: chỉnh lại
  cụm mặt, `nose` (168, 163) → (171, 152), `right_ear` (147, 150) → (152, 147).
- `train_05.jpg`, người #1, `left_hip` / `right_hip`: kéo lên, (198, 305) → (200, 295)
  và (143, 300) → (143, 294).
- `train_03.jpg`, người #1 theo gold (người đàn ông bên trái, skeleton thứ 2 trong file nhãn),
  `right_elbow`: kéo từ (256, 195) - đang nằm trên khuỷu tay phải của người đội mũ bên
  cạnh - về (244, 224). Hết lỗi `nham_nguoi`; OKS người này 0.864 → 0.932.
- `train_03.jpg`, cùng người, `right_wrist`: (305, 163) → (296, 223), giữ v=1. Hết `lech_nhe`.
- `train_03.jpg`, cùng người, `right_shoulder` (236, 167) v=2 → (233, 164) v=1;
  `left_hip` (293, 313) → (295, 284); `right_hip` (232, 320) → (236, 279).
- `train_13`, `train_14`, `train_15`: bản export mới chỉ đổi **thứ tự** skeleton trong file,
  tọa độ và cờ giữ nguyên - không phải chỉnh sửa nhãn.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh (`dao_trai_phai` = 0 trong
`outputs/eval_vs_gold.json`). `check_pose_labels.py` có cảnh báo `train_16` người #2
(vai và hông ngược chiều so với hai mắt), nhưng skeleton đó khớp gold với OKS
0.97-0.99, nên đây là tư thế thật chứ không phải nhãn đảo.

## 3. Kiểm chéo

Bạn cùng nhóm: ⚠️ CẦN ĐIỀN

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

⚠️ CẦN ĐIỀN - chạy
`python tools/visibility_report.py --labels dataset/labels/train --compare <nhãn của bạn cùng nhóm>`
rồi chép số vào bảng.

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| | | | | |
| | | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- ⚠️ CẦN ĐIỀN

## 4. Model

⚠️ CẦN ĐIỀN - chưa có `outputs/eval_model.json`. Chạy notebook
`notebooks/day4_pose_finetune_yolo26.ipynb` (Chặng 6), rồi chép số vào bảng.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | | | |
| pose_mAP50-95 | | | |
| pose_precision | | | |
| pose_recall | | | |
| box_mAP50-95 | | | |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` thay đổi bao nhiêu? ⚠️ CẦN ĐIỀN
2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? ⚠️ CẦN ĐIỀN
3. Một ảnh test model đoán sai - loại lỗi: ⚠️ CẦN ĐIỀN
4. Ảnh có OKS thấp nhất giữa nhãn của bạn và model: ⚠️ CẦN ĐIỀN
5. Ảnh gán tệ nhất có cũng là ảnh model đoán tệ nhất? Ảnh gán tệ nhất theo gold là
   `train_03.jpg` trước rework (OKS 0.864, `nham_nguoi`); sau rework thấp nhất là
   `train_15.jpg` người #1 (OKS 0.778, lệch nhẹ ở `left_knee`, `left_ankle`, `right_ankle`).
   So với model: ⚠️ CẦN ĐIỀN

## 5. Một rule evidence bạn đã dùng

⚠️ CẦN ĐIỀN (3-5 câu): ảnh + người + khớp, bằng chứng nhìn thấy, vì sao chọn v=1 hay v=0.
Gợi ý ứng viên: trong `train_04.jpg`, người #1 có 7 khớp v=0 và người #2 có 4 khớp v=0;
`check_pose_labels.py` cảnh báo cả hai người đều nằm gọn trong ảnh. Cần mở ảnh xem các khớp đó thật sự ra ngoài khung
(giữ v=0) hay chỉ bị che (đổi sang v=1).
