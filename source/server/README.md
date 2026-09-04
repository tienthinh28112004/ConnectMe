# MODULE SERVER
---

## MỤC TIÊU

Server chịu trách nhiệm:
- Xử lý toàn bộ logic nghiệp vụ của ứng dụng (authentication, quản lý user, phòng chat, tin nhắn).
- Cung cấp REST API cho Client truy cập (HTTP/1.1).
- Xác thực và phân quyền user bằng JWT (access token + refresh token).
- Lưu trữ và truy vấn dữ liệu từ cơ sở dữ liệu MySQL.
- Tạo và phát hành connection token / subscription token cho Centrifugo.
- Nhận tin nhắn từ Client và publish sang Centrifugo để phân phối realtime.
- Quản lý trạng thái online/offline của user (kết hợp với Redis + Centrifugo).
- Upload file từ Client và đẩy lên Cloudinary.
- Tương tác với Nginx để định tuyến request từ frontend.

---

## CÔNG NGHỆ SỬ DỤNG

| Thành phần     | Công nghệ                         |
|----------------|-----------------------------------|
| Ngôn ngữ       | Java Maven(Spring Boot 3.4.1)              |
| Thư viện chính | Spring Web, Spring Security, JPA, JWT |
| Cơ sở dữ liệu  | MySQL                             |
| Bộ nhớ đệm     | Redis                             |
| Giao thức      | HTTP/1.1 (REST API)               |

---

## HƯỚNG DẪN CHẠY

### Cài đặt
```bash
#Lấy dự án về
Mở dự án bằng IntelliJ IDEA  
Chờ Maven tải toàn bộ dependencies

# Tạo cơ sở dữ liệu
Vào Mysql Workbench để tạo Schema tên chatme
```

### Khởi động server
```bash
Bấm "Run" để tự build lại cơ sở dữ liệu trong máy.
Bấm tiếp "Run" để chạy dự án
```

Server chạy tại: `http://localhost:8080`

---

## API

| Endpoint | Protocol | Method | Input | Output |
|----------|----------|--------|--------|-------------------------------------------|
| `/api/auth/register` | HTTP/1.1 | POST | Body: UserCreateRequest | ApiResponse<UserResponse> |
| `/api/auth/login` | HTTP/1.1 | POST | Body: LogInRequest | ApiResponse<SignInResponse> |
| `/api/auth/outbound/authentication?code=...` | HTTP/1.1 | POST | Query: code | ApiResponse<SignInResponse> |
| `/api/auth/logout` | HTTP/1.1 | POST | Body: LogOutRequest | ApiResponse<String> |
| `/api/auth/refresh` | HTTP/1.1 | POST | Cookie: refreshToken | ApiResponse<RefreshTokenResponse> |
| `/api/auth/introspect` | HTTP/1.1 | POST | Body: IntrospectRequest | ApiResponse<IntrospectResponse> |
| `/api/user/getUserById/{userId}` | HTTP/1.1 | POST | Path: userId | ApiResponse<UserResponse> |
| `/api/user/getMyInfo` | HTTP/1.1 | GET | — | ApiResponse<UserResponse> |
| `/api/user/getUserByKeyword?keyword=...` | HTTP/1.1 | GET | Query: keyword | ApiResponse<List<UserResponse>> |
| `/api/centrifugo/connectionToken` | HTTP/1.1 | GET | — | ApiResponse<String> |
| `/api/centrifugo/subcriptionToken?channels=...` | HTTP/1.1 | GET | Query: channels | ApiResponse<String> |
| `/api/centrifugo/isUserOnline/{userId}` | HTTP/1.1 | GET | Path: userId | ApiResponse<Boolean> |
| `/api/centrifugo/getRoomOnlineCount/{roomId}` | HTTP/1.1 | GET | Path: roomId | ApiResponse<Integer> |
| `/api/centrifugo/notifyOnline` | HTTP/1.1 | POST | — | ApiResponse<String> |
| `/api/centrifugo/notifyOffline` | HTTP/1.1 | POST | — | ApiResponse<String> |
| `/api/uploadImage` | HTTP/1.1 | POST | multipart/form-data: file | ApiResponse<UploadResponse> |
| `/api/rooms/{roomId}/messages` | HTTP/1.1 | GET | Path: roomId | ApiResponse<List<MessageResponse>> |
| `/api/rooms/sendMessage` | HTTP/1.1 | POST | Body: MessageRequest | ApiResponse<MessageResponse> |
| `/api/room/listRoom` | HTTP/1.1 | GET | Query: keyword | ApiResponse<List<RoomResponse>> |
| `/api/room/addRoom` | HTTP/1.1 | POST | Body: CreateRoomRequest | ApiResponse<RoomResponse> |
| `/api/rooms/join/{roomId}` | HTTP/1.1 | POST | Path: roomId | ApiResponse<RoomMemberResponse> |
| `/api/rooms/leave/{roomId}` | HTTP/1.1 | POST | Path: roomId | ApiResponse<RoomMemberResponse> |

---

## 🧪 TEST
```bash
# Test API đăng nhập bằng curl
curl -X POST http://localhost:8080/api/auth/login \
     -H "Content-Type: application/json" \
     -d '{"email":"test@gmail.com","password":"123456"}'

```

---

## 📝 GHI CHÚ
- Port mặc định: **8080**
- Có thể thay đổi trong file application.yml
