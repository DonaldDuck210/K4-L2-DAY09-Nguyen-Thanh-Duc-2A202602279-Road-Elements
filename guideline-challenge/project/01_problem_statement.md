# Problem statement + downstream contract

## Bài toán

Nhận diện trạng thái tín hiệu (`state`) và tính liên quan tới làn xe chủ (`relevance`) của đèn giao thông cho xe cơ giới tại giao lộ nhiều làn và trong điều kiện ánh sáng phức tạp (ban đêm lóa sáng, chạng vạng, đèn xa/nhỏ) trên ảnh camera hành trình đô thị BDD100K.

## Downstream contract

1. **Downstream task / model / user là ai?**
   Module Perception & Trajectory Planning của xe tự hành cấp độ 3+ (AD/ADAS), nhận diện hộp bao và trạng thái đèn để ra quyết định an toàn: dừng (Stop), đi tiếp (Proceed), hoặc nhường đường khi chuyển hướng.

2. **Output annotation nào thực sự cần?**
   - Geometry: 2D Bounding Box (`rectangle`) ôm sát phần vỏ đèn nhìn thấy.
   - Class: `traffic_light`.
   - Attributes:
     - `state`: `red`, `yellow`, `green`, `off`, `unknown`.
     - `relevance`: `relevant` (điều khiển trực tiếp hướng đi của xe chủ), `not_relevant` (đèn phụ lệch góc, nhánh rẽ, cắt ngang hoặc quay lưng), `unknown` (không đủ bằng chứng xác định).

3. **Failure nào gây hậu quả lớn nhất?**
   - Nhận nhầm đầu đèn phụ quay lệch hướng / nhánh rẽ (`not_relevant`) thành đèn điều khiển hướng đi của xe chủ (`relevant`) — ví dụ như đầu đèn lệch góc ở BDD07 $\rightarrow$ Xe nhận sai quyền ưu tiên di chuyển, dẫn đến vượt đèn đỏ hoặc va chạm tại giao lộ (Critical Escape).
   - Nhận diện nhầm `state = green` khi đèn thực tế là `red` đối với xe chủ (`relevance = relevant`), hoặc bỏ sót đèn đỏ trực tiếp.
   - Kéo giãn box bao trọn quầng sáng lóa ban đêm $\rightarrow$ Sai lệch ước lượng khoảng cách 3D của vật thể.

4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?**
   - Thao tác: Khi không đủ bằng chứng hình ảnh để xác định trạng thái hoặc hướng điều khiển, annotator bắt buộc chọn giá trị `unknown` (trong `state` hoặc `relevance`). Nghiêm cấm việc tự ý suy đoán.
   - Escalation: Các object mang nhãn `unknown` trong file export CVAT sẽ được hệ thống lọc tự động và chuyển lên Chuyên gia An toàn Xe tự hành (Safety Reviewer) đối chiếu cùng bản đồ số HD Map và video trước/sau.

## Scope

- **Trong scope (bắt buộc label):** Mọi đầu đèn giao thông dành cho xe cơ giới nhìn thấy mặt đèn hoặc tín hiệu đèn phát sáng hướng về xe; có chiều cao $\ge 12\text{ px}$; thấy tối thiểu 1 khoang bóng hoặc khung vỏ.
- **Ngoài scope (ignore):** Đèn cho người đi bộ (hình người); đèn xe đạp; đèn quay lưng hoàn toàn (không thấy tín hiệu); đèn nhỏ ở xa ($< 12\text{ px}$); quầng sáng rời; vệt đèn phản chiếu trên mặt đường ướt/kính; đèn đuôi xe ô tô.
- **Geometry tolerance:** Tight visible box ôm sát phần vỏ đèn nhìn thấy được (không ôm quầng sáng ban đêm). Sai số cạnh $\le 2\text{ px}$ với đèn $< 30\text{ px}$ và $\le 3\text{ px}$ với đèn $\ge 30\text{ px}$. IoU so với gold $\ge 0.75$.

## Output chấm được

Mọi quyết định đều kiểm tra được qua export CVAT 1.1:
- `LABEL`: Tạo box `traffic_light` kèm thuộc tính `state` và `relevance`.
- `IGNORE`: Không vẽ box trên đối tượng ngoài scope (bóng phản chiếu, đèn người đi bộ).
- `UNKNOWN / ESCALATE`: Gán giá trị `unknown` trong trường `state` hoặc `relevance` khi thông tin bị che khuất hoặc nhập nhằng không thể suy luận.
- `GEOMETRY`: Đánh giá sai số bounding box theo tight visible boundary.

## Dữ liệu và giới hạn

- Nguồn: Trích xuất từ `data/bdd100k` (ảnh 1280x720 ban ngày, đêm, chạng vạng, mưa) 
- Giới hạn: Ảnh monocular 2D tĩnh không có cảm biến 3D LiDAR/HD Map đi kèm; việc gán `relevance` phải dựa trên cấu trúc vạch kẻ đường, vị trí đầu đèn trên làn và góc quay xe.
