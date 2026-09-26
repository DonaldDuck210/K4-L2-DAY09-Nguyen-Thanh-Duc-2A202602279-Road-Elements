# Team

Điền trước phút 15. Thay mọi placeholder; còn sót thì `make status` báo ở gate G1.

- **Team:** Lab9 (ví dụ `team07`)
- **Nhóm peer test bài của mình:** TODO (cặp A ↔ B; số nhóm lẻ thì ring 3 nhóm A → B → C → A — Lab Coach công bố)
- **Nhóm mình test bài của:** TODO
- **Problem family:** `label traffic lights` (xem README mục "1 · Chọn bài toán")
- **Nguồn ảnh:** `bdd100k` và `lisa` (`bdd100k`, `gtsdb`, `lisa` — chỉ dùng ảnh trong `data/`)

| Thành viên | GitHub | Vai trò chính | File phụ trách |
|---|---|---|---|
| Nguyễn Thanh Đức / Dương Quang Hiệp | DonaldDuck210 / hiepdq-working | spec owner | `01_problem_statement.md`, `02_guideline.md`, `08_revision_log.md` |
| Đoàn Văn Thắng | vanthang10tin | CVAT owner | `03_ontology_and_cvat_setup.md`, `03_cvat_labels.json`, `sample_pack.csv`, `09_cvat_export_or_task_reference.txt` |
| Phạm Văn Nhật Trường | phamvannhattruong | gold owner | `04_edge_cases/` |
| Đặng Văn Huy | imvhuy | QA owner | `05_qa_plan.md`, `06_calibration_report.csv`, `06_calibration_exports/`, `07_blind_handoff/` |

Gợi ý chia vai (nhóm 2–3 người thì gộp): **spec owner** (`01`, `02`), **CVAT owner** (`03_*`, `sample_pack.csv`,
`09`), **gold owner** (`04_edge_cases/`), **QA owner** (`05`, `06`, `07_blind_handoff/`). Mỗi file một người sửa
chính để tránh xung đột git. Calibration thì mọi người cùng label.
