# Mini guideline - nhóm: làm một mình  |  người gán: Vũ Tùng Lâm  |  ngày: 2026-09-16

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt chấm ở vị trí giải phẫu ước lượng: ngang chỗ đùi nối với thân, lệch vào trong từ mép áo. **v=2** nếu chỉ có quần áo phủ lên và không có vật hay người nào khác chắn phía trước; **v=1** khi hông bị vật (xe, bàn, túi) hoặc người khác che. | Quần áo dài chỉ phủ hình dáng, không làm mất vị trí khớp; nếu gán v=1 cho mọi người mặc áo dài thì %v=1 của hông mất ý nghĩa. Vì vậy v=1 dành cho khi thật sự có vật che. Ví dụ: `train_07` (áo khoác dài, hông v=2), `train_06` (hông phải bị thùng xe che, v=1). |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Không thấy da tai thì **v=1**, vẫn đặt chấm ở vị trí tai ước lượng: ngang tầm mắt, sau góc hàm. Áp dụng cho cả tai trong mũ bảo hiểm kín đầu. Chỉ **v=2** khi thấy rõ vành tai. | Tai là khớp nhỏ, dung sai OKS chặt; nếu xoá thì model học "tai biến mất khi đội mũ". Đây là khớp có %v=1 cao nhất trong bài tôi (tai trái 59%, tai phải 41%). Ví dụ: `train_06`, `train_04` (mũ bảo hiểm). |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp nằm **ngoài mép ảnh** thì **v=0**, không đặt chấm. Khớp còn trong khung nhưng bị che (xe, người khác) thì vẫn **v=1**. Muốn chọn v=0 phải nối được đoạn chi ra tới mép ảnh. | Dùng v=0 cho khớp đang trong khung sẽ xoá khớp đó khỏi bảng điểm OKS. Ví dụ: `train_04` người #1 (mất từ hông trở xuống, 7 khớp v=0), người #2 (mất từ gối trở xuống). Gold cũng để v=0 ở đúng các khớp này. |
| Cổ tay nằm sau tay lái / sau thân mình | **v=1**, đặt chấm ước lượng: nối tiếp hướng cẳng tay từ khuỷu, dài khoảng bằng cánh tay trên. | Cổ tay sau tay lái hay sau thân là trạng thái bình thường, không phải ngoại lệ. Ví dụ: `train_06` cổ tay phải v=1, `train_03` người thứ 2 cổ tay phải v=1 (bị người phía trước che). |
| Hai người chồng lên nhau | Gán **xong trọn 17 điểm của một người rồi mới sang người kế tiếp**. Trước khi lưu, bật đường nối và kiểm từng chi có nối về đúng thân của người đó không. | `train_03`: tôi đã đặt `right_elbow` của người phía sau lên khuỷu tay người đội mũ phía trước (lỗi `nham_nguoi`, đã sửa). |
| Người nhỏ đến mức nào thì không gán nữa | Gán nếu người **cao khoảng từ 100 px trở lên** và thấy rõ ít nhất 3 khớp. Người nhỏ hơn, mờ, không phân biệt được các khớp thì không gán. | Người nhỏ nhất tôi đã gán cao ~129 px (`train_13`, người bên trái). Hai người đi bộ ở xa trong `train_02` chỉ cao khoảng 40-50 px, không phân biệt được khớp nên không gán; gold cũng không có hai người này (29 skeleton, khớp đúng số người của tôi). |

Ảnh mẫu (vẽ từ nhãn của tôi bằng `tools/visualize_pose.py`; xanh = trái, cam = phải,
vàng = v=1):

| Luật | Ảnh mẫu |
| --- | --- |
| Hông dưới áo dài | ![hông](reports/guideline_img/hong_ao_dai_train_07.jpg) |
| Tai trong mũ bảo hiểm | ![tai](reports/guideline_img/tai_mu_bao_hiem_train_06.jpg) |
| Người bị cắt ở mép ảnh | ![mép ảnh](reports/guideline_img/cat_mep_anh_train_04.jpg) |
| Cổ tay bị che | ![cổ tay](reports/guideline_img/co_tay_bi_che_train_03.jpg) |
| Hai người chồng nhau | ![chồng nhau](reports/guideline_img/hai_nguoi_chong_train_03.jpg) |
| Người nhỏ ở xa | ![người nhỏ](reports/guideline_img/nguoi_nho_train_02.jpg) |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_03`, người thứ `2` trong file nhãn (người đàn ông phía sau, bên trái), khớp `right_elbow`

- Mơ hồ ở chỗ nào: hai người đứng sát nhau, tay phải người phía sau bị người đội mũ phía trước che; khuỷu tay phải của hai người nằm gần nhau.
- Bạn quyết thế nào: lần đầu tôi đặt ở (256, 195), trùng khuỷu tay người phía trước. Sau khi bật đường nối, tôi dời về (244, 224) theo hướng cánh tay trên đi xuống từ vai phải của chính người này. Cổ tay phải đặt (296, 223), v=1.
- Vì sao: một chi phải nối liền với vai của chính người đó; đường nối vai-khuỷu lúc đầu băng ngang sang thân người khác, đó là dấu hiệu nhầm người.
- Nếu người khác quyết ngược lại thì model học sai cái gì: model học rằng khi hai người đứng sát nhau, có thể lấy tay của người bên cạnh ghép vào skeleton - tạo ra skeleton "lai" giữa hai người.

### Ca 2 - ảnh `train_06`, người thứ `1` (người đi mô tô, nhìn từ phía sau), khớp `nose` / `left_eye` / `right_eye` / `left_ear` / `right_ear`

- Mơ hồ ở chỗ nào: người quay lưng và đội mũ bảo hiểm kín đầu, không thấy mặt; phân vân giữa xoá 5 điểm mặt, v=0, hay v=1.
- Bạn quyết thế nào: cả 5 điểm để **v=1**, đặt chấm ước lượng trên mũ theo hướng quay của đầu, dựa vào vị trí hai vai.
- Vì sao: đầu vẫn nằm trong khung, chỉ bị mũ và chính người đó che, nên theo luật lớp là v=1. Skeleton này đạt OKS 0.955 so với gold.
- Nếu người khác quyết ngược lại thì model học sai cái gì: với v=0, model học rằng người nhìn từ sau lưng "không có đầu", và không học được vị trí đầu cho các ca DMS/OMS nhìn từ phía sau. Chính ảnh này có OKS model-vs-nhãn thấp nhất (0.672), cho thấy model gốc cũng yếu ở ca này.

### Ca 3 - ảnh `train_04`, người thứ `2` (người đội mũ bảo hiểm bên phải), khớp `left_knee` / `right_knee`

- Mơ hồ ở chỗ nào: người ngồi trên xe máy, hông ở sát mép dưới ảnh; đầu gối có thể vẫn trong khung nhưng bị xe che (v=1), hoặc đã ra khỏi mép dưới (v=0). `check_pose_labels.py` cảnh báo "người nằm gọn giữa ảnh".
- Bạn quyết thế nào: **v=0** cho hai đầu gối và hai cổ chân.
- Vì sao: hông đã ở y ≈ 405 trên ảnh cao 457 px; tư thế ngồi làm đùi đi xuống và ra trước, nên đầu gối nằm dưới mép ảnh. Cảnh báo của công cụ chỉ dựa trên khung bao, không nhìn mép ảnh. Gold cũng để v=0 ở đúng 4 khớp này.
- Nếu người khác quyết ngược lại thì model học sai cái gì: đặt v=1 với chấm ước lượng ngoài ảnh (bị kẹp vào mép) thì model học rằng đầu gối nằm dọc mép dưới ảnh, và dự đoán đầu gối "dính mép" cho mọi người bị cắt.

### Ca 4 - ảnh `train_02`, người thứ `1` (người đạp xe quay đầu đi), khớp `left_shoulder` / `right_shoulder`

- Mơ hồ ở chỗ nào: đầu quay về một hướng, thân quay hướng khác; công cụ cảnh báo vai và hông "ngược chiều so với hai mắt", nghi đảo trái/phải.
- Bạn quyết thế nào: giữ trái/phải theo **thân và tay cầm ghi đông**, không theo hướng mắt; 5 điểm mặt v=1 vì mặt bị mũ và góc quay che.
- Vì sao: trái/phải tính theo cơ thể, và thân mới là phần quyết định; đầu có thể xoay độc lập. Gold đặt trái/phải giống tôi (OKS 0.94, không có lỗi `dao_trai_phai`).
- Nếu người khác quyết ngược lại thì model học sai cái gì: đổi vai theo hướng mắt sẽ tạo lỗi đảo trái/phải thật, và augmentation lật ảnh dạy lỗi đó cho model hai lần.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: **không có dữ liệu** - tôi làm bài một mình, không có bạn cùng nhóm để chạy `visibility_report.py --compare`.
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: chưa so được. Thay vào đó, tôi đối chiếu với gold: lệch cờ nhiều nhất ở **tai (13 lần)** và **hông (10 lần)** (`co_khac_gold` trong `outputs/eval_vs_gold.json`, trước rework), theo cả hai chiều v=1↔v=2.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: tách rõ "quần áo phủ" (hông v=2) với "vật/người khác che" (hông v=1), và "không thấy da tai" → tai v=1. Cả hai nhằm giảm lệch cờ ở đúng hai khớp này.
