# INS3155 – Computer Vision – Midterm Project

Lọc ảnh (Part A), tái dựng 3D từ ảnh stereo (Part B) và ghép ảnh panorama (Part C)
chỉ bằng các phương pháp xử lý ảnh / thị giác máy tính **truyền thống** (không deep learning, không mô hình pretrained).

## 1. Cài đặt

Yêu cầu Python ≥ 3.10.

```bash
pip install -r requirements.txt
```

Thư viện: OpenCV (`opencv-python` ≥ 4.8, đã có SIFT), NumPy, scikit-image, Matplotlib, pandas.

## 2. Cấu trúc thư mục

- `22070920_NguyenDinhKhai.ipynb`: toàn bộ mã nguồn Part A/B/C và các đầu ra đã lưu.
- `requirements.txt`: các thư viện cần cài.
- `data/part_a/`: hai ảnh sạch.
- `data/part_b/Venus/`: cặp ảnh stereo và ground truth.
- `data/part_c/`: bốn ảnh ghép panorama; `original/` chứa ảnh gốc.
- `results/part_a/`, `results/part_b/`, `results/part_c/`: bảng CSV, hình và point cloud.
- `22070920_NguyenDinhKhai_Report.pdf`: báo cáo PDF hoàn chỉnh.
- `report_source/main.tex`: nguồn LaTeX tham khảo của báo cáo.

## 3. Dữ liệu

- **Part A**: ảnh sạch (jpg/png) đặt vào `data/part_a/`; ảnh nhiễu được tạo từ ảnh sạch. Hiện tại có hai ảnh tự chụp:
  `desk.jpg` (góc bàn làm việc, đã cắt bỏ phần màn hình laptop ở mép phải) và `setup.jpg`
  (laptop, hai màn hình, bàn phím, chuột). Bảng tổng hợp là trung bình trên các ảnh; từng ảnh có trong `metrics_all.csv`.
- **Part B**: cặp stereo **Venus** của Middlebury Optical Flow benchmark
  (<https://vision.middlebury.edu/flow/data/>, `other-color-twoframes.zip` + `other-gt-flow.zip`).
  Cặp ảnh đã rectify (flow dọc bằng 0), nên disparity thật của ảnh trái là `d = -u`.
  Bộ dữ liệu không có thông số camera nên khi tái dựng 3D dùng camera giả định
  (`FOCAL_PX`, `BASELINE`, `DISP_OFFSET` trong cell Part B của notebook): point cloud chỉ minh họa,
  chưa bảo đảm hình dạng hoặc kích thước thực. Quy ước `d = x_trái - x_phải`,
  `DISP_OFFSET = cx_phải - cx_trái` giả định, nên `Z = f*B/(d + DISP_OFFSET)`.
- **Part C**: 4 ảnh chồng lấn đặt vào `data/part_c/` (tên file sắp theo thứ tự trái → phải).
  Hiện tại là 4 ảnh tự chụp một dãy phố ở Hà Nội; `1..4.jpg` là phần giữa 50% chiều ngang của ảnh gốc
  trong `data/part_c/original/` (ảnh gốc phủ khoảng 140° nên panorama phẳng bị kéo giãn quá mức, xem `data/part_c/README.txt`).

## 4. Chạy chương trình

1. Clone repo (`git clone <URL của repo>`) hoặc tải ZIP từ GitHub rồi mở thư mục vừa tải.
2. Cài thư viện bằng `pip install -r requirements.txt` trong môi trường Python dùng cho notebook.
3. Mở `22070920_NguyenDinhKhai.ipynb` bằng Jupyter hoặc VS Code và chọn kernel của môi trường vừa cài.
4. Chọn **Run All** để chạy lần lượt Part A, B, C. Thư mục làm việc phải là thư mục chứa notebook và `data/`.

Notebook chứa toàn bộ mã xử lý trực tiếp, hiển thị hình sau mỗi phần và ghi kết quả vào `results/`.
Các chỉ số chất lượng tái lập được trong môi trường đã kiểm tra; thời gian chạy thay đổi theo máy.
Phụ lục A của báo cáo trích một số hàm chính từ notebook này.
Nguồn LaTeX được giữ để tham khảo; bản nộp này dùng PDF đã biên dịch, không kèm công cụ sinh bảng LaTeX.

## 5. Kết quả sinh ra

| Part | File |
|---|---|
| A | `metrics_all.csv`, `metrics_summary.csv` (PSNR, SSIM, MSE, thời gian của mọi bộ lọc / tham số / loại nhiễu), `*_grid.png` (gốc / nhiễu / đã lọc / đã làm nét), `chart_psnr_vs_kernel.png` |
| B | `fundamental_metrics.csv` (inliers, khoảng cách epipolar, Sampson error), `F_ransac.txt`, `epipolar_lines.png`, `disparity_metrics.csv` (valid ratio, RMSE, bad-pixel, runtime), `disparity_comparison.png`, `chart_stereo_parameters.png`, `pointcloud_sgbm.ply`, `pointcloud_views.png` |
| C | `stitching_metrics.csv` (mỗi detector × cặp ảnh × ngưỡng RANSAC, thêm dòng tổng), `stitching_summary.csv`, `keypoints_<det>.png`, `matches_<det>_pair*.png`, `panorama_<det>.jpg`, `panorama_comparison.png`, `chart_ransac_threshold.png`; bonus: `blending_metrics.csv` (seam gradient, độ sắc nét vùng chồng lấn, thời gian của seam cứng / feathering / multi-band 2-4-6 tầng), `panorama_SIFT_multiband.jpg`, `panorama_blending.png`, `blending_comparison.png` (phóng to vùng bóng mờ và seam) |

## 6. Ghi chú

- Thời gian chạy (ms, CPU) được đo một lần cho mỗi cấu hình, có một lần chạy khởi động trước; giá trị thay đổi theo tải máy.
- Trong Part B, SGBM với `blockSize ≥ 15` cho kết quả sai. Penalty `P2 = 32·3·blockSize²` tăng theo blockSize²
  (42 336 ở blockSize 21, vượt giới hạn 16-bit có dấu 32 767); giả thuyết là chi phí cộng dồn bị tràn kiểu 16-bit
  trong OpenCV, chưa kiểm chứng bên trong thư viện. Tài liệu OpenCV gợi ý blockSize thường dùng 3–11.
  Các cấu hình này được giữ lại để phân tích.
- Point cloud của Part B dùng camera giả định (`FOCAL_PX`, `BASELINE`, `DISP_OFFSET`), chỉ để minh họa.
