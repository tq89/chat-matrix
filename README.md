# Liên Lạc — Ứng dụng Chat Android

**Liên Lạc** là ứng dụng nhắn tin nội bộ dành cho tổ chức, được xây dựng trên nền tảng giao thức [Matrix](https://matrix.org/) mã nguồn mở. Ứng dụng kết nối trực tiếp với máy chủ riêng tại `https://chat.trongqui.info`, đảm bảo toàn bộ dữ liệu tin nhắn được lưu trữ nội bộ, bảo mật và riêng tư.

---

## Mục lục

- [Giới thiệu](#giới-thiệu)
- [Tính năng nổi bật](#tính-năng-nổi-bật)
- [Yêu cầu hệ thống](#yêu-cầu-hệ-thống)
- [Hướng dẫn build](#hướng-dẫn-build)
- [Cấu trúc dự án](#cấu-trúc-dự-án)
- [Cấu hình](#cấu-hình)
- [Push Notification](#push-notification)
- [Bản quyền](#bản-quyền)

---

## Giới thiệu

Liên Lạc được phát triển dựa trên mã nguồn [Element X Android](https://github.com/element-hq/element-x-android) — thế hệ ứng dụng Matrix client mới nhất, viết lại hoàn toàn bằng Kotlin và Jetpack Compose. Bên dưới sử dụng [Matrix Rust SDK](https://github.com/matrix-org/matrix-rust-sdk) để xử lý giao thức, mang lại hiệu năng cao và độ ổn định vượt trội.

**Package:** `com.trongqui.chat`  
**Máy chủ mặc định:** `https://chat.trongqui.info`  
**Android tối thiểu:** 7.0 (API 24)

---

## Tính năng nổi bật

- **Nhắn tin thời gian thực** — Gửi/nhận tin nhắn tức thì qua giao thức Matrix
- **Mã hóa đầu cuối (E2EE)** — Tin nhắn được mã hóa, chỉ người nhận mới đọc được
- **Đính kèm đa file** — Chọn tối đa 50 ảnh/video/file cùng lúc và gửi theo hàng đợi tự động
- **Cuộc gọi thoại & video** — Tích hợp Element Call
- **Push Notification** — Nhận thông báo qua Firebase Cloud Messaging (FCM)
- **Chế độ tối/sáng** — Hỗ trợ Dark Mode và Light Mode
- **Phòng chat nhóm** — Tạo phòng, mời thành viên, phân quyền
- **Chia sẻ vị trí** — Gửi vị trí thời gian thực
- **Bình chọn (Poll)** — Tạo khảo sát ngay trong phòng chat
- **Tin nhắn thoại** — Ghi âm và gửi voice message

---

## Yêu cầu hệ thống

| Mục | Yêu cầu |
|-----|---------|
| Android | 7.0 trở lên (API 24+) |
| JDK | 21 (Temurin/OpenJDK) |
| Android Studio | Mới nhất (Ladybug trở lên) |
| Gradle | 9.5.1 (tự động qua wrapper) |
| Android SDK | API 36 (compile), API 36 (target) |

---

## Hướng dẫn build

### 1. Clone dự án

```bash
git clone https://github.com/tq89/chat-matrix.git
cd chat-matrix
git checkout claude/serene-ride-3saoii
```

### 2. Mở bằng Android Studio

**File → Open** → chọn thư mục `chat-matrix` → chờ Gradle sync hoàn tất.

### 3. Build APK debug

```bash
./gradlew assembleDebug
```

APK output:
```
app/build/outputs/apk/gplay/debug/app-gplay-debug.apk
```

### 4. Các lệnh Gradle thường dùng

```bash
# Build
./gradlew assembleDebug          # Build APK debug
./gradlew assembleRelease        # Build APK release

# Kiểm tra chất lượng code
./gradlew runQualityChecks       # Chạy toàn bộ kiểm tra
./gradlew detekt                 # Phân tích tĩnh
./gradlew ktlintCheck            # Kiểm tra định dạng code
./gradlew ktlintFormat           # Tự động định dạng code
./gradlew lint                   # Android Lint

# Test
./gradlew test                   # Chạy unit tests
```

---

## Cấu trúc dự án

```
chat-matrix/
├── app/                    # Module ứng dụng chính
│   └── google-services.json  # Cấu hình Firebase
├── appconfig/              # Cấu hình ứng dụng (server, tên, analytics...)
├── appicon/                # Icon ứng dụng các kích thước
├── appnav/                 # Điều hướng màn hình (Appyx)
├── features/               # Các tính năng (login, messages, roomdetails...)
│   └── messages/impl/      # Màn hình chat chính
├── libraries/              # Thư viện dùng chung
│   ├── compound/           # Hệ thống màu sắc & design tokens
│   ├── matrix/             # Wrapper Matrix Rust SDK
│   ├── mediapickers/       # Bộ chọn file/ảnh (hỗ trợ multi-select)
│   └── pushproviders/      # Tích hợp Firebase & UnifiedPush
├── services/               # Dịch vụ nền (analytics, notifications...)
└── plugins/                # Gradle plugins & cấu hình build
```

---

## Cấu hình

Các thông số chính của ứng dụng nằm tại:

### `plugins/src/main/kotlin/config/BuildTimeConfig.kt`
```kotlin
APPLICATION_ID   = "com.trongqui.chat"
APPLICATION_NAME = "Liên lạc"
```

### `appconfig/src/main/kotlin/.../AuthenticationConfig.kt`
```kotlin
MATRIX_ORG_URL = "https://chat.trongqui.info"  // Homeserver mặc định
```

### `appconfig/src/main/kotlin/.../ApplicationConfig.kt`
```kotlin
PRODUCTION_APPLICATION_NAME = "Liên lạc"
```

---

## Push Notification

Ứng dụng sử dụng **Firebase Cloud Messaging (FCM)** để gửi thông báo đẩy.

**Cấu hình Firebase:**
- Project: `phan-mem-tro` (ID: `267937925978`)
- File config: `app/google-services.json`
- Package đã đăng ký: `com.trongqui.chat`

**Để Push Notification hoạt động đầy đủ**, máy chủ Matrix (`chat.trongqui.info`) cần cài đặt **Sygnal** (push gateway) và kết nối với FCM Server Key lấy từ Firebase Console.

Ứng dụng cũng hỗ trợ **UnifiedPush** cho bản F-Droid (không cần Google Services).

---

## Kiến trúc kỹ thuật

| Thành phần | Công nghệ |
|-----------|-----------|
| Ngôn ngữ | Kotlin 2.4 |
| UI | Jetpack Compose + Material 3 |
| Điều hướng | Appyx 1.7 |
| Giao thức Matrix | Matrix Rust SDK 26.06 |
| Dependency Injection | Metro 1.1 |
| State management | Molecule (Presenter pattern) |
| Push | Firebase FCM + UnifiedPush |
| Database | SQLDelight + Room |

---

## Bản quyền

Dự án này được phát triển dựa trên mã nguồn [Element X Android](https://github.com/element-hq/element-x-android).

Copyright (c) 2025 Element Creations Ltd.  
Copyright (c) 2022 - 2025 New Vector Ltd.

Phần mềm gốc được cấp phép kép:
- **Miễn phí** theo GNU Affero General Public License v3 (AGPL-3.0)
- **Thương mại** theo Element Commercial License

Xem chi tiết tại file [LICENSE](LICENSE).
