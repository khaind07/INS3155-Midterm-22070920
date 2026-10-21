Part C input: 4 tấm ảnh tự chụp một dãy phố ở Hà Nội, chụp liên tiếp từ trái sang phải
tại cùng một chỗ đứng, chỉ xoay điện thoại (camera chính 1x).

original/1..4.jpg : ảnh gốc 2000x1500.
1..4.jpg          : phần giữa 50% chiều ngang của mỗi ảnh gốc (1000x1500).

Lý do cắt: mỗi lần chụp xoay khoảng 20-25 độ và ống kính 1x đã rộng (~75 độ), nên 4 ảnh gốc
phủ tổng cộng khoảng 140-150 độ. Khi chiếu tất cả lên mặt phẳng của ảnh giữa, mép ảnh
ngoài cùng bị kéo giãn gần tới vô cực (canvas 12752x5055 px với SIFT, 22392x8068 px với ORB).
Giữ phần giữa 50% tương đương chụp bằng ống kính hẹp hơn: góc nhìn của mỗi ảnh nhỏ lại,
mỗi cặp ảnh liền nhau vẫn chồng lấn nhiều, và panorama phẳng ghép được.

Thay ảnh khác: đặt 4 ảnh mới vào thư mục này, tên file theo thứ tự trái -> phải.
