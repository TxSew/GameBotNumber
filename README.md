## 1. 🔍 Overview

**Mục tiêu dự án:**  
Hệ thống Numbots Clone sẽ tạo điều kiện thuận lợi cho việc học và thực hành các kỹ năng toán học cơ bản thông qua các trò chơi và thử thách tương tác. Hệ thống sẽ bao gồm một số thành phần quản lý tương tác của người dùng, trò chơi, theo dõi tiến trình và bảng xếp hạng.

**Đặc điểm:**

-  Single Page Application (SPA).
-  Phân vai rõ ràng (Families, Tutors, Schools).
-  Có hệ thống thống kê, quản lý người dùng, bảng xếp hạng, và mở rộng cấp độ/màn chơi.

---

## 2. 💡 High-Level Solution

### Frontend

-  **Framework:** React.js với TypeScript.
-  **Routing:** React Router v6.

### Backend

-  **Framework:** NestJS.
-  **Authentication:** JWT kết hợp Guards của NestJS.
-  **Payment Integration:** Stripe API.
-  **Database:** SQL Server.
-  **ORM:** Sequelize.

### Game Logic

-  **Story Mode:** Mở khoá cấp độ theo điểm.
-  **Challenge Mode:** Phân loại bài tập thành Subitising, Number Bonds, Adding, Subtracting.
-  **Leaderboard:** Xếp hạng người chơi theo điểm.
-  **Custom Skin:** Tùy chỉnh giao diện nhân vật.

---

## 🚩 Potential Issues & Solutions

1. **Tích hợp thanh toán Stripe:**

   -  Sử dụng Webhook để xác nhận trạng thái giao dịch từ Stripe.

2. **Quản lý roles phức tạp:**

   -  Thiết kế bảng Role-Based Access Control (RBAC) trong cơ sở dữ liệu.

3. **Hiệu năng game:**

   -  Tối ưu hóa client-side rendering và prefetch dữ liệu với React Query.

4. **Phức tạp trong việc quản lý các bài tập:**
   -  Các bài tập cần phải thay đổi thường xuyên để giữ cho người dùng không cảm thấy nhàm chán, điều này có thể yêu cầu một cơ chế tự động điều chỉnh bài tập theo trình độ người chơi.
5. **Tăng trưởng lượng người dùng:**
   -  Khi số lượng người chơi tăng lên, việc lưu trữ và xử lý dữ liệu người chơi và bảng xếp hạng có thể gặp khó khăn nếu không có cơ chế mở rộng hợp lý.

---

**Numbots frontend:** Ứng dụng web được học sinh sử dụng để chơi trò chơi, theo dõi tiến độ và tương tác với nội dung toán học.

**Numbots service:** Logic phụ trợ cốt lõi để quản lý trạng thái trò chơi, theo dõi tiến trình của người dùng, tạo các bài toán và tính điểm.

## 3. 🛠️ Components

**Components Diagram:**

![Flow Diagram](/components.svg)

**Giải thích**

-  User tương tác với Web Client.
-  Web Client gửi yêu cầu tới Numbots Service API.
-  Numbots Service API đọc/ghi dữ liệu vào Database.

---

## 4. 🔄 Flow

![Flow Diagram](/flow.png)

### Giải thích các bước chính:

1. **Đăng nhập:**

   -  Client gửi thông tin đăng nhập đến API Gateway.
   -  API xác thực thông qua Server và kiểm tra dữ liệu trong Database.
   -  Trả về token để xác thực người dùng.

2. **Lấy câu hỏi:**

   -  Người dùng chọn chế độ chơi (Story hoặc Thử thách).
   -  Client gửi yêu cầu đến API để lấy danh sách câu hỏi.
   -  API truy vấn dữ liệu từ Database thông qua Server.

3. **Gửi kết quả:**

   -  Client gửi kết quả câu trả lời lên API.
   -  API xử lý dữ liệu, lưu tiến trình và điểm số vào Database.

4. **Cập nhật bảng xếp hạng:**
   -  Sau khi hoàn thành, Client yêu cầu cập nhật bảng xếp hạng.
   -  API truy vấn dữ liệu từ Server để hiển thị bảng xếp hạng mới nhất.

---

## 5. 📂 Database Schema

Sơ đồ cơ sở dữ liệu này mô tả cấu trúc của một ứng dụng trò chơi, bao gồm người dùng, vai trò, trò chơi, chế độ, hạng, cấp độ, câu hỏi, tiến trình người chơi, bảng xếp hạng, thanh toán và câu hỏi thường gặp. Dưới đây là phân tích chi tiết của mỗi thực thể:

### 1. Bảng `Users`

Lưu trữ thông tin người dùng.

```sql
user_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi người dùng.
username: VARCHAR(255), NOT NULL, Mô tả: Tên đăng nhập của người dùng.
email: VARCHAR(255), NOT NULL, UNIQUE, Mô tả: Địa chỉ email của người dùng.
password: VARCHAR(255), NOT NULL, Mô tả: Mật khẩu đã được mã hóa để xác thực người dùng.
role: ENUM('Admin', 'Player'), NOT NULL, Mô tả: Vai trò của người dùng (Admin hoặc Player).
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo tài khoản.
updated_at: DATETIME, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, Mô tả: Thời gian cập nhật tài khoản.
```

### 2. Bảng `PLAYERS`

Lưu trữ thông tin chi tiết về từng người chơi, là một loại người dùng cụ thể.

```sql
player_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi người chơi.
user_id: INT, FOREIGN KEY REFERENCES USERS(user_id), Mô tả: Mã người dùng liên kết với người chơi.
first_name: VARCHAR(255), NOT NULL, Mô tả: Tên của người chơi.
last_name: VARCHAR(255), NOT NULL, Mô tả: Họ của người chơi.
pin_code: VARCHAR(20), NOT NULL, Mô tả: Mã PIN bảo mật của người chơi.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo người chơi.
updated_at: DATETIME, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, Mô tả: Thời gian cập nhật người chơi.
```

### 3. Bảng `Roles`

Lưu trữ các vai trò người dùng.

```sql
role_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi vai trò.
role_name: VARCHAR(255), NOT NULL, Mô tả: Tên của vai trò (Ví dụ: Admin, Player).
role_description: TEXT, NOT NULL, Mô tả: Mô tả chi tiết về vai trò.
```

### 4. Bảng `Games`

Lưu trữ thông tin các trò chơi.

```sql
game_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi trò chơi.
game_name: VARCHAR(255), NOT NULL, Mô tả: Tên của trò chơi.
game_description: TEXT, NOT NULL, Mô tả: Mô tả chi tiết về trò chơi.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo trò chơi.
```

### 5. Bảng `GameModes`

Lưu trữ các chế độ chơi trong mỗi trò chơi.

```sql
mode_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi chế độ trò chơi.
game_id: INT, FOREIGN KEY REFERENCES GAMES(game_id), Mô tả: Mã trò chơi mà chế độ này thuộc về.
mode_name: VARCHAR(255), NOT NULL, Mô tả: Tên của chế độ trò chơi.
mode_type: ENUM('Solo', 'Co-op', 'Multiplayer'), NOT NULL, Mô tả: Loại chế độ trò chơi.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo chế độ trò chơi.
```

### 6. Bảng `Ranks`

Lưu trữ các rank trong mỗi GameMode.

```sql
rank_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi hạng.
mode_id: INT, FOREIGN KEY REFERENCES GAME_MODES(mode_id), Mô tả: Mã chế độ trò chơi mà hạng này thuộc về.
rank_name: VARCHAR(255), NOT NULL, Mô tả: Tên của hạng.
rank_order: INT, NOT NULL, Mô tả: Thứ tự của hạng trong bảng xếp hạng.
star_required: INT, NOT NULL, Mô tả: Số sao yêu cầu để đạt được hạng này.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo hạng.
```

### 7. Bảng `Levels`

Lưu trữ các cấp độ trong mỗi Rank.

```sql
level_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi cấp độ.
rank_id: INT, FOREIGN KEY REFERENCES RANKS(rank_id), Mô tả: Mã hạng mà cấp độ này thuộc về.
level_name: VARCHAR(255), NOT NULL, Mô tả: Tên của cấp độ.
level_order: INT, NOT NULL, Mô tả: Thứ tự của cấp độ.
star_required: INT, NOT NULL, Mô tả: Số sao yêu cầu để hoàn thành cấp độ này.
level_required: INT, NOT NULL, Mô tả: Cấp độ tối thiểu cần có để tham gia cấp độ này.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo cấp độ.
```

### 8. Bảng `Questions`

Lưu trữ các câu hỏi trong mỗi cấp độ.

```sql
question_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi câu hỏi.
level_id: INT, FOREIGN KEY REFERENCES LEVELS(level_id), Mô tả: Mã cấp độ mà câu hỏi này thuộc về.
question_text: TEXT, NOT NULL, Mô tả: Nội dung câu hỏi.
correct_answer: TEXT, NOT NULL, Mô tả: Câu trả lời đúng của câu hỏi.
difficulty_level: ENUM('Easy', 'Medium', 'Hard'), NOT NULL, Mô tả: Mức độ khó của câu hỏi.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo câu hỏi.
```

### 9. Bảng `UserGames`

Theo dõi các trò chơi mà người dùng đã chơi.

```sql
user_game_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi trò chơi mà người chơi tham gia.
player_id: INT, FOREIGN KEY REFERENCES PLAYERS(player_id), Mô tả: Mã người chơi tham gia trò chơi.
game_id: INT, FOREIGN KEY REFERENCES GAMES(game_id), Mô tả: Mã trò chơi được chơi.
started_at: DATETIME, NOT NULL, Mô tả: Thời gian bắt đầu trò chơi.
finished_at: DATETIME, NOT NULL, Mô tả: Thời gian kết thúc trò chơi.
```

### 10. Bảng `UserLevelProgress`

Theo dõi tiến độ của người dùng ở mỗi cấp độ.

```sql
user_level_progress_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho tiến trình cấp độ của người chơi.
player_id: INT, FOREIGN KEY REFERENCES PLAYERS(player_id), Mô tả: Mã người chơi tham gia vào cấp độ.
level_id: INT, FOREIGN KEY REFERENCES LEVELS(level_id), Mô tả: Mã cấp độ mà người chơi đang tham gia.
score: INT, NOT NULL, Mô tả: Điểm số đạt được trong cấp độ.
stars: INT, NOT NULL, Mô tả: Số sao đạt được trong cấp độ.
completed_at: DATETIME, NOT NULL, Mô tả: Thời gian hoàn thành cấp độ.
```

### 11. Bảng `Leaderboard`

Lưu trữ bảng xếp hạng của người chơi.

```sql
leaderboard_id: INT, PRIMARY KEY, Mô tả: Mã định danh duy nhất cho mỗi bản ghi bảng xếp hạng.
player_id: INT, FOREIGN KEY REFERENCES PLAYERS(player_id), Mô tả: Mã người chơi trong bảng xếp hạng.
game_id: INT, FOREIGN KEY REFERENCES GAMES(game_id), Mô tả: Mã trò chơi mà bảng xếp hạng này thuộc về.
rank: INT, NOT NULL, Mô tả: Vị trí của người chơi trong bảng xếp hạng.
score: INT, NOT NULL, Mô tả: Điểm số đạt được trong trò chơi.
created_at: DATETIME, DEFAULT CURRENT_TIMESTAMP, Mô tả: Thời gian tạo bản ghi bảng xếp hạng.

```

### 12. Bảng `Payment`

Lưu trữ các thông tin thanh toán của người dùng.

```sql
CREATE TABLE Payment (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    payment_method VARCHAR(255) NOT NULL,
    status ENUM('Pending', 'Completed', 'Failed') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

```

### 13. Bảng `FAQs`

Lưu trữ các câu hỏi thường gặp và trả lời.

```sql
CREATE TABLE FAQs (
    faq_id INT AUTO_INCREMENT PRIMARY KEY,
    question VARCHAR(255) NOT NULL,
    answer TEXT NOT NULL
);

```

## Mối Quan Hệ Giữa Các Thực Thể

1. Bảng USERS ↔ Bảng ROLES: Một người dùng (USERS) có một vai trò (ROLES), nhưng một vai trò có thể được gán cho nhiều người dùng. (1:N)
2. Bảng USERS ↔ Bảng PLAYERS: Một người dùng (USERS) có thể có nhiều người chơi (PLAYERS). (1:N)
3. Bảng GAMES ↔ Bảng GAME_MODES: Một trò chơi (GAMES) có thể có nhiều chế độ trò chơi (GAME_MODES). (1:N)
4. Bảng GAME_MODES ↔ Bảng RANKS: Một chế độ trò chơi (GAME_MODES) có thể có nhiều cấp bậc (RANKS). (1:N)
5. Bảng RANKS ↔ Bảng LEVELS: Một cấp bậc (RANKS) có thể có nhiều cấp độ (LEVELS). (1:N)
6. Bảng LEVELS ↔ Bảng QUESTIONS: Một cấp độ (LEVELS) có thể có nhiều câu hỏi (QUESTIONS). (1:N)
7. Bảng PLAYERS ↔ Bảng USER_GAMES: Một người chơi (PLAYERS) có thể tham gia nhiều trò chơi (USER_GAMES). (1:N)
8. Bảng USER_GAMES ↔ Bảng GAMES: Một trò chơi (GAMES) có thể có nhiều bản ghi trong bảng tham gia trò chơi (USER_GAMES). (1:N)
9. Bảng PLAYERS ↔ Bảng USER_LEVEL_PROGRESS: Một người chơi (PLAYERS) có thể có nhiều tiến độ cấp (USER_LEVEL_PROGRESS). (1:N)
10.   Bảng LEVELS ↔ Bảng USER_LEVEL_PROGRESS: Một cấp độ (LEVELS) có thể có nhiều bản ghi tiến độ cấp (USER_LEVEL_PROGRESS). (1:N)
11.   Bảng GAMES ↔ Bảng LEADERBOARD: Một trò chơi (GAMES) có thể có nhiều bảng xếp hạng (LEADERBOARD). (1:N)
12.   Bảng PLAYERS ↔ Bảng LEADERBOARD: Một người chơi (PLAYERS) có thể xuất hiện trong nhiều bảng xếp hạng (LEADERBOARD). (N:M)
13.   Bảng USERS ↔ Bảng PAYMENTS: Một người dùng (USERS) có thể thực hiện nhiều thanh toán (PAYMENTS). (1:N)

![Flow Diagram](/ERD_GAME.jpg)

```
erDiagram
    USERS {
        int user_id PK
        string username
        string email
        string password
        enum role
        datetime created_at
        datetime updated_at
    }


    PLAYERS {
        int player_id PK
        int user_id FK
        string first_name
        string last_name
        string pin_code
        datetime created_at
        datetime updated_at
    }


    ROLES {
        int role_id PK
        string role_name
        string role_description
    }


    GAMES {
        int game_id PK
        string game_name
        string game_description
        datetime created_at
    }


    GAME_MODES {
        int mode_id PK
        int game_id FK
        string mode_name
        enum mode_type
        datetime created_at
    }


    RANKS {
        int rank_id PK
        int mode_id FK
        string rank_name
        int rank_order
        int star_required
        datetime created_at
    }


    LEVELS {
        int level_id PK
        int rank_id FK
        string level_name
        int level_order
        int star_required
        int level_required
        datetime created_at
    }


    QUESTIONS {
        int question_id PK
        int level_id FK
        string question_text
        string correct_answer
        enum difficulty_level
        datetime created_at
    }


    USER_GAMES {
        int user_game_id PK
        int player_id FK
        int game_id FK
        datetime started_at
        datetime finished_at
    }


    USER_LEVEL_PROGRESS {
        int user_level_progress_id PK
        int player_id FK
        int level_id FK
        int score
        int stars
        datetime completed_at
    }


    LEADERBOARD {
        int leaderboard_id PK
        int game_id FK
        int player_id FK
        int score
        datetime recorded_at
    }


    PAYMENTS {
        int payment_id PK
        int user_id FK
        string payment_method
        enum status
        datetime created_at
        datetime updated_at
    }


    FAQs {
        int faq_id PK
        string question
        string answer
    }


    %% Relationships
    USERS ||--o| PLAYERS : "can have"
    PLAYERS ||--o| USERS : "belongs to"
    USERS ||--o| ROLES : "has"
    GAMES ||--o| GAME_MODES : "has"
    GAME_MODES ||--o| RANKS : "has"
    RANKS ||--o| LEVELS : "has"
    LEVELS ||--o| QUESTIONS : "has"
    PLAYERS ||--o| USER_GAMES : "participates in"
    GAMES ||--o| USER_GAMES : "has"
    PLAYERS ||--o| USER_LEVEL_PROGRESS : "has progress in"
    LEVELS ||--o| USER_LEVEL_PROGRESS : "has"
    GAMES ||--o| LEADERBOARD : "has"
    PLAYERS ||--o| LEADERBOARD : "appears in"
    USERS ||--o| PAYMENTS : "makes"
```
