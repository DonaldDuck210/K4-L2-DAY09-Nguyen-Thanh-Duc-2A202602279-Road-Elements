# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới
là xong (gate G5).

- **Nhóm peer:** Nhóm 03
- **Người label blind:** Nguyễn Văn A (Nhóm 03)

## 1. Peer trả lời

1. **Rule nào rõ nhất / giúp quyết định nhanh nhất?**  
   Quy tắc loại trừ đèn đi bộ (hộp bàn tay màu cam ở BDD12 không vẽ) và quy tắc tách hai đầu đèn trên cùng cần treo ở BDD07 rất rõ ràng, đọc là làm được ngay.

2. **Rule nào mơ hồ hoặc phải tự suy diễn?**  
   Ở ảnh BDD12, bảng hiệu màu đỏ của cây xăng Citgo và nguồn sáng mờ phía xa ngã tư gây chút phân vân về việc có cần vẽ box `traffic_light` hay không.

3. **Sample nào khiến guideline "vỡ"?**  
   Ảnh BDD12: Giao lộ ngã tư trước mặt có vạch qua đường nhưng đầu đèn xe ở gần không có, chỉ có đèn người đi bộ. Nhờ có rule escalate gắn tag `image_escalate` nên nhóm đã hoàn thành được.

4. **Attribute / default nào trong CVAT dễ gây thao tác sai?**  
   Giá trị mặc định `__undefined__` ở `state` và `relevance` giúp tránh lỗi gán nhầm nhưng đòi hỏi phải chọn dropdown cẩn thận cho từng box.

5. **Một thay đổi cụ thể giúp annotator mới ít hỏi hơn?**  
   Bổ sung thêm ví dụ cụ thể về bảng hiệu cây xăng / biển quảng cáo thương mại vào danh sách nguồn sáng không phải đèn tín hiệu ở mục 5.

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| Phân vân biển hiệu cây xăng Citgo ở BDD12 | guideline_gap | accept + revise: bổ sung ví dụ biển hiệu thương mại/cây xăng vào mục 5 | Câu hỏi trong `clarification_log.csv` lúc 16:40 |
| Phân biệt 2 đầu đèn trên cùng cần treo ở BDD07 | guideline_gap (đã sửa ở v2) | no_change: rule v2 về giao lộ gần nhất và phân biệt hướng rẽ đã phát huy tác dụng | Peer gán đúng cả 2 đầu đèn trong `transfer_score.csv` |
| Tách đầu đèn trên cần vươn ở BDD21 | execution_error | no_change: rule nhìn nghiêng và ngưỡng 20 px đã rõ | Peer vẽ chính xác box đèn chính, bỏ qua đèn nghiêng |
