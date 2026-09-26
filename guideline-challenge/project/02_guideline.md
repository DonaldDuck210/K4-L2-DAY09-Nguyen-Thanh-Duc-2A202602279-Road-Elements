# Annotation guideline — Trạng thái đèn tín hiệu và mức liên quan tới làn xe mình

**Version:** v3

Đọc hết mục 1–7 trước khi vẽ. Mọi quyết định phải thể hiện được trong CVAT theo đúng cách ghi ở mục 7.
"Xe mình" (ego) là xe gắn camera. Gặp tình huống guideline không nói tới: **không đoán**, dùng đường escalate ở mục 7.

## 1. Objective + scope

Dữ liệu dùng để dạy mô hình nhận biết **đèn nào đang điều khiển làn của xe mình và đèn đó màu gì**, phục vụ quyết định
dừng / đi. Vì vậy sai trạng thái hoặc sai mức liên quan của một đèn điều khiển làn mình là lỗi nghiêm trọng nhất.

- **Trong scope:** mọi đầu đèn tín hiệu **dành cho xe** mà **mặt đèn (phía có ống kính) quay về phía xe mình** và đủ lớn
  theo mục 5.
- **Ngoài scope:** đèn đi bộ, đèn nhìn nghiêng hoặc từ phía sau, phản chiếu, và các nguồn sáng không phải đèn tín hiệu
  (xem danh sách đầy đủ ở mục 5).

## 2. Annotation unit

- Đơn vị là **ảnh tĩnh**. Mỗi ảnh gán độc lập, kể cả các frame LISA liên tiếp.
- Một **instance = một đầu đèn** (một khối vỏ chứa các ô đèn xếp thành một cột hoặc một hàng).
- Hai đầu đèn gắn cạnh nhau trên cùng cột hoặc cùng cần treo là **hai instance**, vẽ hai box riêng.
- Tấm nền đen (backplate) viền quanh đầu đèn không làm nó thành instance riêng.

## 3. Geometry rule

- Công cụ: **Rectangle**, chế độ **Shape** (không dùng Track).
- **Ban ngày / chạng vạng (thấy vỏ đèn):** box ôm sát **phần vỏ đèn nhìn thấy được**, gồm cả mái che nhỏ trên từng ô
  đèn nếu có. **Không** gồm tấm nền viền ngoài, cần treo, cột, biển tên đường.
- **Ban đêm (không thấy vỏ):** box ôm sát **đĩa đèn đang sáng** (phần sáng đều, có màu). **Không** ôm quầng sáng loang
  ra xung quanh.
- Đèn bị che một phần hoặc bị cắt mép ảnh: chỉ ôm **phần nhìn thấy**, không đoán phần bị che (xem mục 6).
- Tolerance: mỗi cạnh lệch không quá **3 px** so với mép vỏ (hoặc mép đĩa sáng). Phóng to ít nhất 200% trước khi chỉnh
  cạnh.

## 4. Taxonomy

Một class `traffic_light` (rectangle) và một tag cả ảnh `image_escalate`. Bảng đầy đủ nằm ở
`03_ontology_and_cvat_setup.md`; hai nơi phải khớp nhau.

| Attribute | Giá trị | Cách chọn |
|---|---|---|
| `state` | `red`, `yellow`, `green`, `off`, `unknown` | Màu của ô đèn **đang sáng**. `off` = thấy rõ mặt đèn nhưng không ô nào sáng. `unknown` = có ô sáng nhưng không nhận ra màu (loá, mờ, cháy sáng trắng) |
| `signal_shape` | `circle`, `arrow_left`, `arrow_right`, `arrow_straight`, `unknown` | Hình của ô đang sáng. Tắt (`off`) hoặc không nhận ra hình thì `unknown` |
| `relevance` | `relevant`, `not_relevant`, `unknown` | Theo quy tắc ngay dưới bảng |
| `occluded` | checkbox | Tick khi hơn 30% vỏ đèn bị che hoặc bị cắt mép ảnh |
| `needs_review` | checkbox | Tick khi escalate đèn này (mục 7) |

Mọi attribute select có mặc định `__undefined__`. **Không được để `__undefined__` trong bản nộp**: còn giá trị này nghĩa
là quên gán.

**Quy tắc relevance** (quan trọng nhất):

1. Giả định xe mình **đi thẳng** trong làn hiện tại. Chỉ khác đi khi làn của xe mình có **mũi tên rẽ sơn trên mặt
   đường nhìn thấy trong ảnh**; khi đó xe mình đi theo hướng mũi tên đó.
2. `relevant` = đầu đèn quay về phía xe mình **và** điều khiển hướng đi của xe mình **tại giao lộ gần nhất phía trước**.
   - **Định nghĩa "giao lộ gần nhất":** là vạch dừng hoặc vạch qua đường gần nhất nằm phía trước xe; trường hợp không thấy vạch thì lấy cụm đầu đèn đầu tiên dọc theo làn đường xe mình đang di chuyển.
   - Một giao lộ thường có nhiều đầu đèn cùng điều khiển một hướng (đèn trên cần treo, đèn trên cột phía xa): **tất cả** đều `relevant`.
3. `not_relevant` = đầu đèn quay về phía xe mình nhưng điều khiển **hướng khác** (ví dụ đầu đèn chỉ có mũi tên rẽ trái
   khi xe mình đi thẳng), hoặc thuộc **giao lộ xa hơn** giao lộ gần nhất.
4. `unknown` + tick `needs_review` = không xác định được đầu đèn điều khiển hướng nào hoặc thuộc giao lộ nào.
5. **Quy tắc phân xử khi phân vân:** Phân vân giữa `relevant` và `not_relevant` thì **bắt buộc chọn `unknown` và tick `needs_review`**, không tự ý chọn một trong hai hoặc đoán mò.

## 5. Inclusion / exclusion

**Bắt buộc vẽ** khi đủ cả ba điều kiện:
1. Là đèn tín hiệu dành cho xe (ô đèn tròn hoặc mũi tên, xếp đỏ–vàng–xanh).
2. Thấy được **mặt đèn** (ống kính) quay về phía xe mình.
3. Đủ lớn:
   - Thấy rõ mép vỏ đèn: **cạnh dài của vỏ đèn ≥ 20 px**.
   - Khi không thấy rõ mép vỏ đèn (**ban đêm, lúc chạng vạng**): dùng ngưỡng **đường kính đĩa sáng ≥ 10 px**.
   - Đo ở kích thước gốc của ảnh (CVAT hiện kích thước box khi chọn object).
   - **Đèn sát ngưỡng (vỏ 18–22 px, đĩa sáng 8–12 px):** vẫn vẽ (LABEL) và bắt buộc tick `needs_review` (xem mục 7).

**Không vẽ** (IGNORE, không cần đánh dấu gì):
- **Đèn đi bộ**: hiện hình bàn tay hoặc hình người đi bộ, thường là hộp vuông với hai ô cạnh nhau, gắn thấp trên cột ở
  góc phố.
- Đầu đèn nhìn **nghiêng hoặc từ phía sau** (chỉ thấy thân vỏ, không thấy ô đèn nào).
- **Phản chiếu** của đèn trên kính xe mình, kính xe khác, cửa kính nhà, mặt đường ướt.
- Nguồn sáng không phải đèn tín hiệu: đèn phanh, đèn pha, đèn đường, **bảng hiệu cây xăng (như trạm Citgo ở BDD12), logo thương hiệu và biển hiệu quảng cáo phát sáng**.
- Đèn **nhỏ hơn ngưỡng** ở điều kiện 3 (dưới 18 px với vỏ, dưới 8 px với đĩa sáng), hoặc các nguồn sáng mờ nhạt không nhận diện được cấu trúc vỏ đèn ở góc xa giao lộ.

## 6. Visibility / occlusion

- Bị che dưới 30% (cây, cột, xe tải): vẽ phần nhìn thấy, `occluded` để trống.
- Bị che từ 30% trở lên hoặc bị cắt mép ảnh: vẽ phần nhìn thấy, tick `occluded`. Nếu vẫn thấy ô đang sáng thì gán
  `state` bình thường; không thấy ô nào sáng thì `state=unknown`.
- Loá nắng, loá đèn pha, ảnh mờ do mưa: vẫn vẽ nếu đủ điều kiện ở mục 5; màu không chắc thì `state=unknown`, **không
  đoán màu**.
- Ban đêm màu xanh của đèn tín hiệu thường ngả xanh ngọc (xanh lơ), đỏ thường ngả cam; đó vẫn là `green` / `red`.

## 7. Ambiguity / escalation

| Tình huống | Quyết định | Thể hiện trong CVAT |
|---|---|---|
| Rõ ràng, đủ điều kiện mục 5 | LABEL | Box `traffic_light` + đủ attribute |
| Thuộc danh sách "Không vẽ" ở mục 5 | IGNORE | Không vẽ gì |
| Không nhận ra màu hoặc hình của ô đang sáng | UNKNOWN | `state=unknown` và/hoặc `signal_shape=unknown` |
| Không biết đầu đèn điều khiển hướng nào / giao lộ nào | ESCALATE đèn | `relevance=unknown` + tick `needs_review` |
| Phân vân giữa `relevant` và `not_relevant` tại giao lộ | ESCALATE đèn | `relevance=unknown` + tick `needs_review` |
| Đèn sát ngưỡng kích thước (vỏ 18–22 px, đĩa sáng 8–12 px) | LABEL + ESCALATE | Vẽ box theo phần nhìn thấy + tick `needs_review` |
| Một đầu đèn sáng đồng thời hai ô (ví dụ đỏ tròn + mũi tên xanh) | LABEL + ESCALATE | `state` theo ô tròn, `signal_shape=circle`, tick `needs_review` |
| Thấy vạch dừng hoặc vạch qua đường của giao lộ gần nhất nhưng **không thấy đầu đèn xe nào** điều khiển làn mình (chỉ thấy đèn đi bộ, hoặc chỉ thấy đèn của giao lộ xa hơn) | ESCALATE ảnh | Gắn tag `image_escalate` |
| Không có giao lộ, không có đèn | Không làm gì | Không vẽ, không tag |

Không dùng `image_escalate` thay cho việc vẽ các đèn nhìn thấy được: vẫn vẽ mọi đèn đủ điều kiện rồi mới gắn tag.

## 8. Temporal rule

Không áp dụng — task ảnh tĩnh. Các frame LISA liên tiếp cũng gán độc lập từng frame, không suy trạng thái từ frame trước.

## 9. Examples

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| LISA01 | Cần treo có ba đầu đèn đang đỏ: đầu bên trái sáng mũi tên rẽ trái, hai đầu còn lại sáng đèn tròn. Phía dưới xa có hai đèn đỏ rất nhỏ | 3 box. Đầu trái: `state=red`, `signal_shape=arrow_left`, `relevance=not_relevant`. Hai đầu đèn tròn: `state=red`, `signal_shape=circle`, `relevance=relevant`. Hai đèn nhỏ phía xa dưới ngưỡng 20 px: không vẽ | Mục 4 (relevance 1–3), mục 5 (ngưỡng) |
| LISA30 | Cùng giao lộ; mũi tên trái vẫn đỏ, hai đầu đèn tròn đã xanh | 3 box. Đầu trái: `red`, `arrow_left`, `not_relevant`. Hai đầu tròn: `green`, `circle`, `relevant` | Mỗi đầu đèn gán `state` độc lập |
| BDD12 | Trước vạch qua đường; cột bên phải có hộp đèn hiện bàn tay màu cam. Không thấy đầu đèn xe nào | Không vẽ box nào. Gắn tag `image_escalate` | Mục 5 (đèn đi bộ), mục 7 (escalate ảnh) |
| BDD06 | Cao tốc, chỉ có biển cảnh báo vàng hình thoi, không có giao lộ | Không vẽ, không tag | Mục 5, dòng cuối mục 7 |

## 10. Common mistakes & Quy trình tự kiểm

**Quy trình tự kiểm tra bắt buộc trước khi Save (Ctrl+S):**
1. Chuyển sang chế độ **Attribute annotation** (góc trên bên phải) để rà soát từng đối tượng.
2. Đảm bảo **không còn bất kỳ giá trị `__undefined__` nào** trong mọi trường dropdown (`state`, `signal_shape`, `relevance`).
3. Mọi đèn có `state=unknown` hoặc `relevance=unknown` bắt buộc phải được tick `needs_review`.
4. Rà soát xem ảnh có tình huống ngã tư thiếu đèn xe để gắn tag `image_escalate` hay không.

**Lỗi thường gặp:**
1. Vẽ đèn đi bộ bàn tay đỏ thành `traffic_light` `state=red`. Đèn đi bộ luôn **không vẽ**.
2. Để `relevance=relevant` cho đầu đèn mũi tên rẽ trái khi xe mình đi thẳng.
3. Gán `relevant` cho đèn của giao lộ phía sau giao lộ gần nhất.
4. Ban đêm vẽ box ôm cả quầng sáng, làm box to gấp 2–3 lần đĩa đèn.
5. Quên đổi `__undefined__` ở một attribute. Dùng chế độ **Attribute annotation** để rà từng đèn.
6. Vẽ phản chiếu đèn trên kính xe mình, hoặc vẽ đèn phanh xe phía trước thành đèn đỏ.
7. Đoán màu khi đèn bị loá thay vì chọn `unknown`.
8. Bỏ quên giá trị mặc định `__undefined__` ở `relevance` hoặc `state` (lỗi thực thi). Cần tự kiểm checklist trước khi bấm Save.
