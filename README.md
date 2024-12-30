import cv2
import numpy as np

# Hàm kiểm tra loại thẻ (thẻ đỏ hoặc thẻ vàng)
def check_card(player1_x, player1_y, player2_x, player2_y):
    # Khoảng cách giữa 2 cầu thủ
    distance = np.sqrt((player2_x - player1_x) ** 2 + (player2_y - player1_y) ** 2)

    # Kiểm tra phạm lỗi và xác định thẻ
    if distance < 15:
        return "RED CARD", "CO THE DO"  # Thẻ đỏ (tình huống phạm lỗi nghiêm trọng)
    elif distance < 30:
        return "YELLOW CARD", "CO THE VANG"  # Thẻ vàng (tình huống phạm lỗi nhẹ)
    return "NO CARD", "KHONG CO THE"  # Không có thẻ

# Danh sách các đường dẫn video
video_paths = [
    'D:/VAR.mp4',  # Thay bằng đường dẫn video của bạn
    'D:/VAR1.mp4',  # Thay bằng đường dẫn video thứ 2
    'D:/VAR2.mp4'  # Thay bằng đường dẫn video thứ 3
]

# Lặp qua từng video trong danh sách
caps = [cv2.VideoCapture(video_path) for video_path in video_paths]

# Kiểm tra video
for i, cap in enumerate(caps):
    if not cap.isOpened():
        print(f"Không thể mở video: {video_paths[i]}. Vui lòng kiểm tra đường dẫn và codec.")
        continue  # Tiếp tục với video tiếp theo nếu không mở được video

# Giả lập thông tin cầu thủ
player_attack_x, player_attack_y = 400, 500  # Cầu thủ tấn công
player_defense_x, player_defense_y = 420, 505  # Cầu thủ phòng ngự (gần cầu thủ tấn công)

# Bắt đầu vòng lặp xử lý từng khung hình của các video
while True:
    frames = []
    for i, cap in enumerate(caps):
        ret, frame = cap.read()
        if not ret:
            print(f"Kết thúc video {video_paths[i]} hoặc không đọc được khung hình.")
            # Quay lại video từ đầu sau khi kết thúc
            cap.set(cv2.CAP_PROP_POS_FRAMES, 0)  # Quay lại khung hình đầu tiên
            ret, frame = cap.read()  # Đọc lại khung hình đầu tiên

        # Vẽ các đối tượng trong khung hình
        cv2.circle(frame, (player_attack_x, player_attack_y), 10, (255, 0, 0), -1)  # Cầu thủ tấn công (xanh dương)
        cv2.circle(frame, (player_defense_x, player_defense_y), 10, (0, 255, 0), -1)  # Cầu thủ phòng ngự (xanh lá)

        # Kiểm tra phạm lỗi và xác định thẻ
        card_type, card_message = check_card(player_attack_x, player_attack_y, player_defense_x, player_defense_y)

        # Hiển thị kết luận về thẻ trên video
        if card_type == "RED CARD":
            cv2.putText(frame, card_type, (50, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 255), 2)  # Thẻ đỏ
            cv2.putText(frame, card_message, (50, 100), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 255), 2)  # Thẻ đỏ
        elif card_type == "YELLOW CARD":
            cv2.putText(frame, card_type, (50, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 255), 2)  # Thẻ vàng
            cv2.putText(frame, card_message, (50, 100), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 255), 2)  # Thẻ vàng
        else:
            cv2.putText(frame, card_type, (50, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 0), 2)  # Không có thẻ
            cv2.putText(frame, card_message, (50, 100), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 0), 2)  # Không có thẻ

        # Thêm khung hình vào danh sách để hiển thị
        frames.append(frame)

    # Hiển thị từng video trong các cửa sổ riêng biệt
    for i, frame in enumerate(frames):
        cv2.imshow(f'Video {i + 1} - VAR', frame)

    # Thoát vòng lặp khi nhấn 'q'
    if cv2.waitKey(25) & 0xFF == ord('q'):
        break

# Giải phóng tài nguyên cho tất cả video
for cap in caps:
    cap.release()

# Đóng tất cả cửa sổ OpenCV
cv2.destroyAllWindows()

# Kết thúc
print("Chương trình kết thúc thành công.")
