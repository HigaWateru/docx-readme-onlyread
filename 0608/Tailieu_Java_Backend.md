# TÀI LIỆU ÔN TẬP: JAVA BACKEND / SPRING BOOT (GÓC NHÌN SENIOR - CHUẨN PHỎNG VẤN)

Tài liệu này tổng hợp các kiến thức cốt lõi về Java Core, Object-Oriented Programming (OOP) và Spring Boot Framework ở cấp độ nâng cao, bám sát các câu hỏi thực tế trong vòng phỏng vấn kỹ thuật.

---

## 1. Hướng Đối Tượng (OOP) & Cấu Trúc Dữ Liệu Java Core

### Câu 57: Lấy ví dụ thể hiện đầy đủ 4 tính chất OOP từ dự án của bạn.
*   **Ví dụ thực tế trong dự án Quản lý giao dịch thu chi (Transaction):**
    1.  **Đóng gói (Encapsulation):** Định nghĩa class `Transaction` với các thuộc tính private (`amount`, `category`, `createdAt`) và chỉ cho phép đọc/ghi thông qua getter/setter. Ta có thể thêm validation vào setter:
        ```java
        public void setAmount(BigDecimal amount) {
            if (amount.compareTo(BigDecimal.ZERO) <= 0) {
                throw new IllegalArgumentException("Số tiền giao dịch phải lớn hơn 0");
            }
            this.amount = amount;
        }
        ```
    2.  **Kế thừa (Inheritance):** Tạo class cha `Transaction` chứa thông tin chung, và các class con kế thừa như `IncomeTransaction` (Thu nhập) và `ExpenseTransaction` (Chi tiêu) kế thừa lại thuộc tính và phương thức từ `Transaction`.
    3.  **Đa hình (Polymorphism):** Định nghĩa phương thức `calculateTax()` ở lớp cha, sau đó ghi đè (`@Override`) ở lớp con.
        ```java
        // Lớp cha Transaction
        public BigDecimal calculateTax() { return BigDecimal.ZERO; }

        // Lớp con IncomeTransaction ghi đè
        @Override
        public BigDecimal calculateTax() { return this.getAmount().multiply(new BigDecimal("0.1")); } // Thuế thu nhập 10%
        ```
    4.  **Trừu tượng (Abstraction):** Định nghĩa một interface `TransactionService` chỉ chứa các khai báo phương thức nghiệp vụ như `createTransaction()`, `getTransactionHistory()`. Người dùng/Controller chỉ tương tác với interface này mà không cần quan tâm đến cách nó lưu trữ ở MySQL hay PostgreSQL bên dưới.

### Câu 58: Khi thiết kế module thanh toán (VNPay, Momo, COD, PayPal), khi nào dùng Interface, khi nào dùng Abstract Class?
*   **Sử dụng Interface:** Khi cần định nghĩa một **bản hợp đồng chung (behavior contract)** cho tất cả các cổng thanh toán độc lập. Ví dụ, tất cả đều phải có phương thức `processPayment(PaymentRequest req)` và `refundPayment(String txId)`. Các class Momo, VNPay có cách cài đặt hoàn toàn khác nhau.
*   **Sử dụng Abstract Class:** Khi các cổng thanh toán có chung một số **trạng thái (attributes)** hoặc **đoạn code xử lý giống hệt nhau (shared logic)**. Ví dụ: Lớp trừu tượng `BasePaymentGateway` chứa thuộc tính `apiKey`, `merchantId` và phương thức chung `validateSignature()` để kiểm tra chữ ký số từ nhà mạng. Lớp Momo, VNPay sẽ kế thừa từ `BasePaymentGateway` để dùng chung code validate, đồng thời override phương thức `processPayment()`.
*   **Góc nhìn Senior/Interview Tip:** *"Mối quan hệ Interface là **Can-Do** (Có thể làm gì), còn Abstract Class là **Is-A** (Là cái gì). Một class trong Java chỉ được kế thừa một Abstract Class nhưng có thể implement nhiều Interface."*

### Câu 59: Trong JVM, dữ liệu nào nằm ở Heap, dữ liệu nào nằm ở Stack? Garbage Collector thu hồi khi nào?
*   **Stack (Vùng nhớ ngăn xếp):**
    *   Lưu trữ các lời gọi hàm (Stack Frame), các biến cục bộ (primitive types) và tham chiếu (reference) trỏ tới các Object thực tế nằm ở Heap.
    *   Mỗi Thread có một Stack riêng biệt. Dữ liệu tự động giải phóng khi hàm kết thúc thực thi.
*   **Heap (Vùng nhớ đống):**
    *   Lưu trữ tất cả các Object được khởi tạo qua từ khóa `new` (kể cả Wrapper classes như `Integer`, `String`, `User`).
    *   Dùng chung cho toàn bộ các Thread trong ứng dụng.
*   **Garbage Collector (GC) thu hồi dữ liệu khi nào?**
    *   GC sẽ thu hồi các Object nằm ở Heap khi chúng **không còn bất kỳ tham chiếu (reference) nào trỏ tới** (Unreachable Objects). GC sử dụng thuật toán như Mark-and-Sweep để dò từ các nút gốc (GC Roots) để xác định xem đối tượng còn sống hay đã chết.

### Câu 60: Lựa chọn Collection phù hợp cho các tình huống:
*   **(a) Danh sách sinh viên có thứ tự:** Chọn **`ArrayList`**. Truy cập ngẫu nhiên theo index cực nhanh O(1), duyệt danh sách có hiệu năng cache tốt do lưu mảng liên tục trong bộ nhớ.
*   **(b) Danh sách email không trùng:** Chọn **`HashSet`**. Đảm bảo tính duy nhất, tốc độ thêm mới và kiểm tra tồn tại cực nhanh O(1).
*   **(c) Tra cứu user theo username:** Chọn **`HashMap`**. Dùng username làm key, User làm value. Tốc độ tra cứu đạt O(1).
*   **(d) Queue xử lý đơn hàng FIFO:** Chọn **`LinkedList`** (implement interface `Queue`/`Deque`) hoặc **`ArrayDeque`**. Thao tác chèn ở cuối và lấy ra ở đầu cực kỳ tối ưu O(1).

---

## 2. Spring Framework Core (IoC & DI)

### Câu 61: Nếu không có IoC Container, phát triển ứng dụng Spring gặp vấn đề gì? DI giải quyết thế nào?
*   **Vấn đề khi không có IoC:** Các class phải tự khởi tạo các phụ thuộc của nó thông qua từ khóa `new` (Tight Coupling). Ví dụ: `UserController` tự `new UserService()`, `UserService` lại tự `new UserRepository()`.
    *   *Hệ quả:* Khó unit test vì không thể mock dữ liệu; khó thay đổi cài đặt (ví dụ đổi từ MySQL sang MongoDB phải sửa code `new` ở hàng trăm chỗ).
*   **DI (Dependency Injection) giải quyết thế nào:**
    *   **IoC (Inversion of Control - Đảo ngược điều khiển):** Việc quản lý vòng đời và khởi tạo các đối tượng (Bean) được bàn giao cho Spring Container lo, thay vì để lập trình viên tự quản lý.
    *   **DI (Dependency Injection):** Khi `UserController` cần `UserService`, Spring Container sẽ tự động "tiêm" (inject) instance của `UserService` vào `UserController` thông qua Constructor hoặc Setter. Nhờ đó, code trở nên Loose Coupling, dễ viết unit test bằng cách mock dependencies.

### Câu 62: Tại sao Spring phân biệt @Component, @Service, @Repository, @Controller dù chúng đều là Bean?
*   **TL;DR:** Cả 4 annotation này đều đăng ký class thành Spring Bean. Việc phân biệt giúp thể hiện rõ **thiết kế phân lớp kiến trúc (Domain Driven Design)** và cho phép Spring áp dụng các xử lý đặc thù (Aspects) cho từng lớp.
*   **Đặc thù riêng:**
    *   `@Controller / @RestController`: Lớp giao tiếp API, xử lý HTTP request/response.
    *   `@Service`: Chứa logic nghiệp vụ (Business logic).
    *   `@Repository`: Lớp truy cập CSDL (Data Access Layer). Spring sẽ tự động dịch các SQLException thành DataAccessException của Spring để dễ xử lý.
    *   `@Component`: Lớp bổ trợ chung không thuộc 3 tầng trên (như Filter, Helper, Uploader).

### Câu 63: Mô tả Bean Lifecycle của Spring. PostConstruct và PreDestroy là gì?
*   **Quy trình vòng đời của Bean:**
    1.  **Instantiation:** Spring khởi tạo instance của Bean (gọi Constructor).
    2.  **Populate Properties:** Tiêm các phụ thuộc vào Bean (thông qua `@Autowired` hoặc Constructor).
    3.  **Aware Interfaces:** Gọi các phương thức BeanNameAware, BeanFactoryAware nếu có.
    4.  **BeanPostProcessor (Before Initialization):** Thực hiện xử lý trước khi init.
    5.  **Initialization:** Gọi phương thức `@PostConstruct` hoặc phương thức `afterPropertiesSet()`. Đây là nơi khởi tạo các tài nguyên phụ thuộc (kết nối DB, load cache).
    6.  **BeanPostProcessor (After Initialization):** Thực hiện tạo Proxy (như `@Transactional` hay `@Async`).
    7.  **Ready for use:** Bean ở trạng thái sẵn sàng sử dụng.
    8.  **Destruction:** Khi ứng dụng tắt, gọi `@PreDestroy` hoặc `destroy()` để dọn dẹp tài nguyên (đóng kết nối, giải phóng thread pool).

### Câu 64: Tại sao hiện nay ưu tiên Constructor Injection hơn Field Injection (@Autowired trực tiếp)?
1.  **Tính bất biến (Immutability):** Cho phép khai báo các phụ thuộc là `final` (bắt buộc phải khởi tạo qua constructor và không thể thay đổi sau đó).
2.  **Dễ viết Unit Test:** Có thể test class bằng cách `new` thủ công và truyền các mock object vào Constructor mà không cần dùng Reflection hay dựng context của Spring Test lên.
3.  **Tránh lỗi NullPointerException:** Đảm bảo Bean không bao giờ được khởi tạo ở trạng thái thiếu phụ thuộc. Nếu thiếu, Spring sẽ báo lỗi ngay khi compile/start-up.
4.  **Phát hiện Circular Dependency (Phụ thuộc vòng):** Spring sẽ ném lỗi ngay khi start-up nếu Service A gọi Service B và Service B lại gọi Service A thông qua Constructor.

---

## 3. Spring Boot Web, Security & Database Transactions

### Câu 65: Thiết kế API RESTful cho module Quản lý Sinh viên.
*   **Thiết kế chuẩn RESTful:**

| Chức năng | HTTP Method | URL | Request Body | Response HTTP Status |
| :--- | :--- | :--- | :--- | :--- |
| **Lấy danh sách (Phân trang)** | `GET` | `/api/v1/students` | Không | `200 OK` |
| **Lấy chi tiết 1 sinh viên** | `GET` | `/api/v1/students/{id}` | Không | `200 OK` (404 nếu không thấy) |
| **Thêm mới sinh viên** | `POST` | `/api/v1/students` | JSON `StudentDTO` | `201 Created` |
| **Cập nhật toàn bộ sinh viên** | `PUT` | `/api/v1/students/{id}` | JSON `StudentDTO` | `200 OK` |
| **Xóa sinh viên** | `DELETE` | `/api/v1/students/{id}` | Không | `204 No Content` |
| **Tìm kiếm theo tên & lớp** | `GET` | `/api/v1/students/search` | Query params | `200 OK` |

### Câu 66: Phân biệt @PathVariable, @RequestParam, @RequestBody.
*   `@PathVariable`: Lấy giá trị trực tiếp từ đường dẫn URL (thường dùng để định danh tài nguyên).
    *   *Ví dụ:* `GET /api/students/{id}` -> `@PathVariable Long id`.
*   `@RequestParam`: Lấy giá trị từ query parameter sau dấu `?` trong URL (thường dùng để lọc, phân trang, sắp xếp).
    *   *Ví dụ:* `/api/students?page=0&size=10` -> `@RequestParam int page`.
*   `@RequestBody`: Chuyển đổi dữ liệu JSON từ body của HTTP request thành Java Object (thường dùng cho POST/PUT/PATCH).
    *   *Ví dụ:* `POST /api/students` -> `@RequestBody StudentDTO dto`.

### Câu 67: Thiết kế Global Exception Handling bằng @ControllerAdvice.
*   **Cài đặt thực tế:**
    ```java
    @RestControllerAdvice
    public class GlobalExceptionHandler {

        @ExceptionHandler(ResourceNotFoundException.class)
        public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex, WebRequest request) {
            ErrorResponse error = new ErrorResponse(
                HttpStatus.NOT_FOUND.value(),
                ex.getMessage(),
                LocalDateTime.now()
            );
            return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
        }

        @ExceptionHandler(Exception.class)
        public ResponseEntity<ErrorResponse> handleGlobal(Exception ex, WebRequest request) {
            ErrorResponse error = new ErrorResponse(
                HttpStatus.INTERNAL_SERVER_ERROR.value(),
                "Hệ thống gặp sự cố ngoài ý muốn",
                LocalDateTime.now()
            );
            return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
    ```
    *   *Tại sao nên dùng:* Giúp tách biệt logic xử lý lỗi ra khỏi Controller, tránh lặp code try-catch, và đảm bảo mọi API khi lỗi đều trả về cùng một cấu trúc JSON đồng nhất cho Frontend dễ xử lý.

### Câu 68: Mô tả quy trình xác thực JWT trong Spring Security.
1.  **Quy trình Đăng nhập:**
    *   Client gửi credentials (username, password) đến API `/api/v1/auth/login`.
    *   Spring Security kiểm tra credentials, nếu đúng, sinh ra JWT (gồm 3 phần: Header, Payload, Signature) và trả về cho Client.
2.  **Quy trình gọi API tiếp theo:**
    *   Client gửi request đính kèm JWT vào header `Authorization: Bearer <token>`.
    *   Một Custom Filter (kế thừa `OncePerRequestFilter`) sẽ chặn request, giải mã token, xác thực chữ ký số bằng Secret Key.
    *   Nếu token hợp lệ, Filter lấy thông tin User (Username, Roles) và đưa vào `SecurityContextHolder` để Spring Security phân quyền truy cập.
3.  **Access Token vs Refresh Token:**
    *   *Access Token:* Thời gian sống ngắn (ví dụ: 15-30 phút), dùng để gọi trực tiếp các API bảo mật.
    *   *Refresh Token:* Thời gian sống dài (ví dụ: 7-30 ngày), lưu an toàn ở DB hoặc Cookie HttpOnly, chỉ dùng để gọi API làm mới Access Token khi nó hết hạn.

### Câu 69: Tại sao dùng @Transactional trong API tạo đơn hàng?
*   **Tình huống:** Tạo đơn hàng cần: (1) Lưu Order, (2) Lưu OrderDetail, (3) Trừ số lượng tồn kho. Nếu bước (3) lỗi nhưng không có `@Transactional`, Order và OrderDetail vẫn được lưu vào DB. Điều này dẫn đến sự không nhất quán dữ liệu (đơn hàng được tạo nhưng kho không giảm).
*   **Cách giải quyết:** Đánh dấu `@Transactional` trên phương thức service. Khi có bất kỳ RuntimeException nào xảy ra ở bất cứ bước nào, Spring sẽ yêu cầu Database **Rollback** toàn bộ các thao tác ghi trước đó trong transaction đó. Nếu tất cả thành công, transaction sẽ được **Commit** cùng lúc.

### Câu 70: Giải thích Propagation.REQUIRED và Propagation.REQUIRES_NEW.
*   **Propagation.REQUIRED (Mặc định):** Nếu phương thức hiện tại được gọi từ một phương thức khác đã có Transaction, nó sẽ **dùng chung** Transaction đó. Nếu chưa có, nó sẽ tạo mới.
    *   *Hậu quả:* Nếu phương thức gọi bị lỗi, toàn bộ transaction bị rollback bao gồm cả phương thức con.
*   **Propagation.REQUIRES_NEW:** Phương thức con sẽ luôn **khởi tạo một Transaction mới hoàn toàn độc lập**, tạm dừng Transaction hiện tại của phương thức gọi.
    *   *Ví dụ thực tế (Tạo đơn hàng + Ghi log):* Phương thức `createOrder()` gọi `logActivity()`. Ta muốn dù việc tạo đơn hàng thất bại bị rollback, nhưng lịch sử click/gọi API vẫn phải lưu lại thành công vào DB. Khi đó, `logActivity()` phải được đánh dấu `@Transactional(propagation = Propagation.REQUIRES_NEW)`.

### Câu 71: Tại sao @Transactional không rollback? Nêu các nguyên nhân phổ biến.
1.  **Gọi nội bộ trong cùng một Class (Self-invocation):** AOP Proxy của Spring chặn các cuộc gọi từ bên ngoài vào. Nếu phương thức A gọi phương thức B (có `@Transactional`) trong cùng một class, proxy sẽ bị bỏ qua và transaction không được kích hoạt.
2.  **Bắt lỗi (Catching Exception) bên trong code:** Nếu bạn dùng block `try-catch` và bắt các Exception mà không ném ngược ra ngoài (`throw`), Spring Container sẽ không phát hiện ra lỗi và vẫn Commit bình thường.
3.  **Sai loại Exception mặc định:** Theo mặc định, Spring chỉ tự động rollback đối với `RuntimeException` và `Error`. Nếu code ném ra một Checked Exception (như `IOException`, `SQLException`) mà không khai báo `@Transactional(rollbackFor = Exception.class)`, transaction sẽ không rollback.
4.  **Access Modifier không phải public:** `@Transactional` chỉ hoạt động trên các phương thức `public`.

---

## 4. Testing & Vận Hành (Performance, Debugging, Deployment)

### Câu 72: Nguyên tắc FIRST trong Unit Test. Thường test lớp nào?
*   **F - Fast (Nhanh):** Test phải chạy cực kỳ nhanh để dev có thể chạy liên tục trong quá trình gõ code.
*   **I - Independent (Độc lập):** Các test case không được phụ thuộc lẫn nhau hoặc phụ thuộc vào thứ tự chạy.
*   **R - Repeatable (Có thể lặp lại):** Test phải trả về cùng một kết quả trên mọi môi trường (Local, CI/CD, Server).
*   **S - Self-validating (Tự xác thực):** Test phải tự đưa ra kết quả Pass/Fail rõ ràng mà không cần con người đọc log để kiểm tra.
*   **T - Timely (Kịp thời):** Nên viết test trước hoặc song song với viết code sản phẩm (TDD).
*   **Lớp thường test:** Thường tập trung viết Unit Test cho tầng **Service** (nơi chứa logic nghiệp vụ phức tạp) bằng cách mock tầng Repository.

### Câu 73: Phân biệt @Mock, @Spy và @InjectMocks trong Mockito.
*   `@Mock`: Tạo ra một đối tượng giả lập hoàn toàn. Tất cả các phương thức của mock object mặc định sẽ trả về null/mặc định trừ khi ta cấu hình rõ hành vi bằng `when().thenReturn()`.
*   `@Spy`: Tạo ra một đối tượng giả lập bán phần (Partial Mock). Nó sẽ gọi các phương thức **thực tế** của class đó trừ khi ta chủ động mock một phương thức cụ thể bằng `doReturn().when()`.
*   `@InjectMocks`: Tạo ra một thực thể thật của class cần test và tự động tiêm các đối tượng có annotation `@Mock` hoặc `@Spy` vào class đó.

### Câu 74: Viết Unit Test cho Service phụ thuộc vào Repository và EmailService.
```java
@ExtendWith(MockitoExtension.class)
public class RegisterUserServiceTest {

    @Mock
    private UserRepository userRepository;

    @Mock
    private EmailService emailService;

    @InjectMocks
    private UserServiceImpl userService; // Lớp cần test

    @Test
    public void registerUser_Success() {
        // 1. Arrange (Chuẩn bị dữ liệu và mock)
        User user = new User("test@gmail.com", "password");
        Mockito.when(userRepository.existsByEmail("test@gmail.com")).thenReturn(false);
        Mockito.when(userRepository.save(any(User.class))).thenReturn(user);

        // 2. Act (Thực hiện hành động)
        User savedUser = userService.registerUser(user);

        // 3. Assert (Xác minh kết quả)
        assertNotNull(savedUser);
        assertEquals("test@gmail.com", savedUser.getEmail());
        
        // Xác minh EmailService thực sự được gọi gửi mail chào mừng đúng 1 lần
        verify(emailService, times(1)).sendWelcomeEmail("test@gmail.com");
    }
}
```

### Câu 75: API chạy rất chậm khi có 2.000 người dùng đồng thời. Phân tích theo thứ tự nào? Công cụ debug?
*   **Thứ tự phân tích:**
    1.  **Database:** Kiểm tra CPU/RAM của DB Server, kiểm tra Slow Queries, kết nối Connection Pool (HikariCP) có bị cạn kiệt không.
    2.  **Application Code:** Kiểm tra lỗi rò rỉ bộ nhớ (Memory Leak), các tác vụ bị block, hoặc thuật toán xử lý tốn nhiều CPU.
    3.  **Network/Infrastructure:** Kiểm tra băng thông, độ trễ kết nối, giới hạn chịu tải của API Gateway hoặc Web Server (Nginx/Tomcat).
*   **Công cụ debug:**
    *   *Database:* `EXPLAIN ANALYZE` (SQL), Prometheus + Grafana (giám sát connection pool).
    *   *Application:* **JProfiler**, **VisualVM** (để profiling bộ nhớ và CPU JVM), **JStack** (để phân tích Thread Dump xem thread nào bị block).
    *   *Load Testing:* **JMeter** hoặc **Gatling** để giả lập tải 2.000 users.

### Câu 76: Xử lý thế nào khi API timeout khi gọi đến một external service?
*   **Sử dụng Circuit Breaker Pattern (Ví dụ: Resilience4j):**
    *   Ngăn không cho hệ thống tiếp tục gửi yêu cầu đến service ngoài đang bị lỗi. Hệ thống tự động chuyển sang trạng thái **OPEN**, chặn mọi request gọi đi và trả về ngay kết quả dự phòng (Fallback response) để tránh làm nghẽn luồng xử lý chính.
*   **Thiết lập Connect Timeout và Read Timeout ngắn hợp lý:** Tránh để các thread ứng dụng phải đợi vô thời hạn làm cạn kiệt tài nguyên máy chủ.
*   **Áp dụng cơ chế Retry kèm Backoff:** Nếu lỗi do mất kết nối tạm thời, thử lại sau một khoảng thời gian tăng dần (Exponential Backoff).

### Câu 77: Tại sao không dùng ddl-auto=update trên Production? Thay thế bằng gì?
*   **Tại sao không dùng `update` trên Prod:**
    *   Hibernate tự động thay đổi cấu trúc bảng. Nếu có lỗi logic hoặc xung đột, nó có thể tự động xóa cột, đổi kiểu dữ liệu hoặc tạo ra các index thừa thãi gây khóa bảng, dẫn đến **mất mát dữ liệu** hoặc làm treo hệ thống mà không có sự kiểm soát của con người.
*   **Thay thế bằng gì:** Sử dụng các công cụ quản lý phiên bản cơ sở dữ liệu chuyên nghiệp (Database Migration Tools) như **Flyway** hoặc **Liquibase**. Toàn bộ thay đổi DB phải được viết thành các script SQL cụ thể (ví dụ: `V1__create_table_users.sql`), được review kỹ càng và deploy tự động qua CI/CD.
