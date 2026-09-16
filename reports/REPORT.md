# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Vũ Tùng Lâm   Nhóm: làm một mình   Ngày: 2026-09-16

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

Bạn cùng nhóm: không có - tôi làm bài một mình, nên chưa có bảng visibility của người khác để so
và chưa có `reports/review_partner.md`. ⚠️ CẦN XÁC NHẬN với giảng viên cách thay thế cho phần
kiểm chéo (ví dụ ghép cặp với người khác trong lớp).

## 4. Model

Số chép từ `outputs/eval_model.json` (đo trên 10 ảnh test). Chênh = sau fine-tune - gốc.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50 | 0.9785 | 0.9600 | -0.0185 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` **tăng nhẹ 0.0055** (0.6853 → 0.6908, khoảng +0.8%), không giảm.
   `pose_mAP50` và `pose_recall` giữ nguyên, nên model vẫn tìm ra đúng số người như trước;
   phần tăng nằm ở độ chính xác vị trí khớp ở ngưỡng OKS chặt, kèm `pose_precision` +0.0058.
   Điều 20 ảnh có thể dạy thêm so với COCO: nhãn của tôi đặt chấm cho khớp bị che (v=1 là
   126/493 điểm, khoảng 26%), trong khi COCO thường để v=0 cho khớp không gán. Cái giá phải
   trả là `box_mAP` giảm (mAP50 -0.0185, mAP50-95 -0.0078): fine-tune trên 20 ảnh kéo nhẹ
   đầu dò box khỏi trọng số COCO. Với 10 ảnh test, mức chênh ±0.01 rất có thể chỉ là nhiễu,
   nên không kết luận model tốt lên rõ rệt.

2. Sau fine-tune, `box_mAP50-95` 0.8041 so với `pose_mAP50-95` 0.6908: **chênh 0.1133**
   (trước fine-tune chênh 0.1266). Ở mức 50: box 0.9600 so với pose 0.8450, chênh 0.1150.
   **Model tìm người dễ hơn tìm khớp.** Một box chỉ cần 4 cạnh ôm đúng thân người, còn pose
   phải đặt đúng cả 17 điểm; OKS phạt nặng các khớp nhỏ như mắt, tai, cổ tay và các khớp bị
   che, trong khi box vẫn đúng dù có khớp bên trong bị che.

> Lưu ý: Colab clone fork ở commit trước bản export cuối (nhãn train v=2 341 / v=1 125),
> tức model được fine-tune và so trên nhãn **trước rework** - `train_03` khi đó vẫn còn lỗi
> `nham_nguoi`.

3. **`test_07.jpg`** (người phụ nữ ngồi sau quầy, trước mặt là lồng kính bánh): model đặt
   `left_hip` / `right_hip` trên **mặt bàn phía trước lồng bánh**, nằm ngoài cả box người
   mà model vừa dự đoán. Thân dưới của người này bị quầy che hoàn toàn, nên hông phải nằm
   sau quầy, không thể ở trên mặt bàn gần camera. Đây là lỗi **trượt hẳn**: điểm rơi vào
   vật khác, không phải lệch nhẹ quanh đúng khớp. Nhận xét dựa trên lưới ảnh ở mục 5 của
   notebook (ảnh thu nhỏ). Ngoài ra ở `test_02.jpg`, model phát hiện thêm một "person" 0.31
   rất nhỏ trên bờ tường, trong khi nhãn test chỉ có 1 người.

4. OKS thấp nhất giữa nhãn của tôi và model là **`train_06`: 0.672** (người đi mô tô nhìn
   từ phía sau, đội mũ bảo hiểm kín đầu, nửa thân phải bị thùng xe che). **Nhãn của tôi đúng
   hơn**: skeleton này đạt OKS **0.955** so với gold, không có lỗi phân loại nào. Ảnh khó cho
   model vì mặt không lộ (mắt, mũi, tai đều bị mũ che, phải đặt ước lượng với v=1) và nhìn
   từ sau lưng, nên trái/phải chỉ suy ra được từ vai và tay. Cả hai điều này COCO dạy ít.
   Vì model cho điểm tin cậy thấp ở khớp bị che, notebook đổi các khớp đó thành v=0, và vì
   vậy OKS giữa model và nhãn tụt xuống.

5. **Có một phần.** Ảnh tôi gán tệ nhất theo gold (trước rework) là `train_03` (OKS 0.864,
   `nham_nguoi` ở `right_elbow`). Đó cũng là ảnh có OKS model-vs-nhãn **thấp thứ hai (0.725)**,
   và là ảnh lệch số người nhiều nhất (model 4 / tôi 2). Nhưng ảnh model lệch nhất
   (`train_06`) lại là ảnh tôi gán tốt (0.955 vs gold). Điều đó cho thấy `train_03` khó thật
   sự: hai người đứng chồng lên nhau, tay người sau bị người trước che, và còn các vật dễ bị
   nhận nhầm là người (con búp bê dưới đất, xe đạp). Cả người lẫn model đều dễ gán nhầm
   điểm sang cơ thể bên cạnh. Ảnh kiểu này cần luật "gán xong một người rồi mới sang người
   kế tiếp" trong guideline, và nên được kiểm chéo kỹ nhất.

## 5. Một rule evidence bạn đã dùng

⚠️ CẦN ĐIỀN (3-5 câu): ảnh + người + khớp, bằng chứng nhìn thấy, vì sao chọn v=1 hay v=0.
Gợi ý ứng viên: trong `train_04.jpg`, người #1 có 7 khớp v=0 và người #2 có 4 khớp v=0;
`check_pose_labels.py` cảnh báo cả hai người đều nằm gọn trong ảnh. Cần mở ảnh xem các khớp đó thật sự ra ngoài khung
(giữ v=0) hay chỉ bị che (đổi sang v=1).
