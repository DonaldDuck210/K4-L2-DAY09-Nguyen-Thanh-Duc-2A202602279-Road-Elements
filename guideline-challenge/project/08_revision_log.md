# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản nháp đầu: 1 class `traffic_light` (rectangle) + tag `image_escalate`; attribute `state`, `signal_shape`, `relevance`, `occluded`, `needs_review` (mọi select mặc định `__undefined__`); rule relevance theo giao lộ gần nhất; ngưỡng vỏ đèn 20 px / đĩa sáng ban đêm 10 px; không vẽ đèn đi bộ, đèn nhìn nghiêng hoặc phản chiếu | Viết trước khi label thử, suy từ downstream contract: planner dừng/đi chỉ cần đèn điều khiển làn xe mình và màu của đèn đó | `01_problem_statement.md`; ví dụ LISA01, LISA30, BDD12, BDD06; bản gốc lưu ở `02_guideline_v1.md` |
| v2 | Mục 4: định nghĩa "giao lộ gần nhất" (vạch dừng hoặc vạch qua đường gần nhất phía trước xe; không thấy vạch thì lấy cụm đầu đèn đầu tiên dọc làn mình), và quy tắc phân xử: phân vân giữa `relevant` và `not_relevant` thì chọn `unknown` + `needs_review` | Hai annotator gán relevance ngược nhau cho cả 2 đầu đèn trong cùng một ảnh; rule v1 chưa nói cách xác định giao lộ khi thiếu vạch hoặc cách xử lý khi phân vân (guideline gap) | `06_calibration_report.csv`, dòng BDD17: thang=`relevant/unknown`; trường=`not_relevant/relevant` |
| v2 | Mục 5: khi không thấy rõ mép vỏ đèn (đêm, chạng vạng), dùng ngưỡng đĩa sáng ≥ 10 px; đo bằng kích thước box CVAT hiển thị; đèn sát ngưỡng (vỏ 18–22 px, đĩa 8–12 px) vẫn vẽ và tick `needs_review`. Mục 7: thêm quy tắc "đèn sát ngưỡng → LABEL + ESCALATE" | Hai annotator đếm khác nhau số đèn trong ảnh chạng vạng; v1 chỉ nêu ngưỡng cho "ban đêm" nên chạng vạng bị bỏ ngỏ (guideline gap) | `06_calibration_report.csv`, dòng BDD25 count: thang=2; trường=3 |
| v2 | Mục 10: thêm bước tự kiểm bắt buộc trước khi Save (Attribute annotation, không còn `__undefined__`, đèn `unknown` phải có `needs_review`, kiểm tag `escalate`) và lỗi thường gặp số 8 | Một annotator để `__undefined__` ở relevance và state dù v1 đã cấm; rule rõ nhưng không được làm theo (execution error → coaching + checklist). Default `__undefined__` đã giúp phát hiện lỗi này ngay trong export | `06_calibration_report.csv`, dòng BDD25 relevance/state và BDD26 relevance |
