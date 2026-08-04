# TÀI LIỆU ÔN TẬP: FRONTEND / REACTJS WITH TYPESCRIPT (GÓC NHÌN SENIOR - CHUẨN PHỎNG VẤN)

Tài liệu này cung cấp các lời giải chi tiết, đào sâu vào cơ chế hoạt động bên dưới của ReactJS và TypeScript, giúp bạn ghi điểm tuyệt đối trong các vòng phỏng vấn Frontend kỹ thuật cao.

---

## 1. Cơ Chế Hoạt Động & Khái Niệm Nền Tảng (React Core)

### Câu 80: ReactJS là gì và tư duy Component-Based Architecture nghĩa là gì?
*   **TL;DR:** ReactJS là một thư viện Javascript mã nguồn mở dùng để xây dựng giao diện người dùng (UI) có tính tương tác cao. Tư duy **Component-Based Architecture** nghĩa là chia nhỏ giao diện người dùng phức tạp thành các khối mã nguồn nhỏ, độc lập, có khả năng tái sử dụng (reusable) và tự quản lý trạng thái (state) riêng của chúng.
*   **Góc nhìn Senior/Interview Tip:** React là một **Library** chứ không phải Framework (như Angular). React chỉ tập trung vào tầng View. Khi phỏng vấn, hãy nhấn mạnh tư duy **Declarative (Khai báo)** của React: *"Bạn chỉ cần mô tả giao diện sẽ trông như thế nào tương ứng với từng trạng thái dữ liệu (state), việc cập nhật DOM thật để khớp với mô tả đó là do React tự động xử lý."* (Ngược lại với tư duy Imperative - Mệnh lệnh của jQuery).

### Câu 81: JSX là gì và trình biên dịch xử lý JSX như thế nào?
*   **JSX (JavaScript XML):** Là một cú pháp mở rộng cho phép viết mã HTML trực tiếp trong JavaScript.
*   **Cách biên dịch:** Trình duyệt không thể đọc hiểu trực tiếp JSX. Các công cụ biên dịch (như Babel hoặc SWC) sẽ chuyển đổi JSX thành các lời gọi hàm `React.createElement()` hoặc hàm jsx-runtime (`_jsx`).
    *   *Ví dụ JSX:* `<div className="box">Hello</div>`
    *   *Sau khi biên dịch (Babel):* `React.createElement('div', { className: 'box' }, 'Hello')`
    *   Kết quả trả về của hàm này là một **React Element** (đối tượng JS thuần túy mô tả cấu trúc DOM).

### Câu 82: Sự khác biệt cơ bản giữa Class Component và Functional Component?
*   **Class Component (Cũ):** Dựa trên Class của ES6. Quản lý state thông qua `this.state` và xử lý vòng đời bằng các Lifecycle methods (`componentDidMount`, `componentDidUpdate`, `componentWillUnmount`).
*   **Functional Component (Hiện đại):** Dựa trên hàm JavaScript thuần túy. Quản lý state và lifecycle thông qua **React Hooks** (`useState`, `useEffect`).
*   **Sự khác biệt bản chất:** Functional Component nắm giữ tính chất **Capture Value**. Mỗi lần render là một lần chạy lại hàm với một snapshot của props/state tại thời điểm đó. Class Component thì giữ tham chiếu thông qua đối tượng `this`, đôi khi dẫn đến lỗi dữ liệu bị cập nhật sai thời điểm (stale data) khi thực hiện các tác vụ bất đồng bộ.

### Câu 83: Cơ chế Virtual DOM và Diffing Algorithm hoạt động như thế nào?
1.  **Virtual DOM (Vdom):** Là một bản sao gọn nhẹ của Real DOM dạng cây đối tượng JavaScript nằm trong bộ nhớ.
2.  **Quy trình cập nhật:**
    *   Khi state thay đổi, React sẽ tạo ra một cây Virtual DOM mới đại diện cho giao diện mới.
    *   React chạy thuật toán đối sánh (**Diffing Algorithm**) để so sánh cây VDOM mới này với cây VDOM cũ.
    *   Sau khi xác định được chính xác các điểm khác biệt tối thiểu, React thực hiện cập nhật các thay đổi đó lên Real DOM thực tế (quá trình này gọi là **Reconciliation** - Giao thoa).
3.  **Độ phức tạp thuật toán:** Diffing Algorithm của React là thuật toán heuristic có độ phức tạp **O(N)** dựa trên 2 giả định:
    *   Hai element khác type (ví dụ `<div>` đổi thành `<span>`) sẽ tạo ra hai cây hoàn toàn khác nhau -> React hủy cây cũ và dựng cây mới.
    *   Sử dụng thuộc tính `key` để xác định các phần tử con nào ổn định qua các lần render.

### Câu 84: Sự khác biệt cơ bản giữa props và state?
*   **Props (Properties):** Là dữ liệu được truyền từ Component cha xuống Component con. Props có tính chất **chỉ đọc (read-only / immutable)** từ phía component nhận.
*   **State:** Là dữ liệu nội bộ được quản lý bên trong chính component đó. State có thể thay đổi được (**mutable**) thông qua hàm cập nhật state (như `setState` hoặc `useState` dispatch). Khi state thay đổi, component sẽ tự động re-render.

### Câu 85: 4 trường hợp khiến React Component bị Re-render?
1.  **State** nội bộ của chính component đó thay đổi.
2.  **Props** truyền từ component cha vào thay đổi.
3.  **Component cha** bị re-render (dẫn đến toàn bộ con của nó mặc định bị re-render theo, trừ khi được tối ưu bằng `React.memo`).
4.  Component sử dụng dữ liệu từ một **Context** và giá trị của Context đó thay đổi.
*(Ngoài ra còn có thể ép buộc re-render bằng hook `useForceUpdate` tự viết hoặc gọi `this.forceUpdate()` trong Class Component).*

### Câu 86: Vai trò của thuộc tính key khi render danh sách bằng .map()? Tại sao không nên dùng index làm key?
*   **Vai trò của `key`:** Giúp React định danh duy nhất các phần tử trong danh sách. Khi danh sách thay đổi (thêm, xóa, sắp xếp lại), React dựa vào `key` để biết phần tử nào thực sự thay đổi để cập nhật, phần tử nào giữ nguyên để tái sử dụng DOM, tránh vẽ lại toàn bộ danh sách.
*   **Tại sao không nên dùng index làm key:**
    *   Nếu danh sách có thứ tự thay đổi (ví dụ: chèn một phần tử vào đầu danh sách), index của tất cả các phần tử phía sau sẽ bị thay đổi hoàn toàn.
    *   React sẽ lầm tưởng các phần tử đó bị thay đổi dữ liệu và thực hiện cập nhật lại DOM một cách không cần thiết, làm giảm hiệu năng và gây ra lỗi hiển thị nghiêm trọng nếu các phần tử con có chứa state nội bộ (như input fields).

---

## 2. React Hooks & State Management nâng cao

### Câu 87: Tính chất "bất đồng bộ" của useState nghĩa là gì? Cách cập nhật state đúng khi giá trị mới phụ thuộc vào giá trị cũ?
*   **Tính chất "bất đồng bộ":** Thực chất, các hàm cập nhật state không chạy bất đồng bộ theo cơ chế Event Loop của JS, mà React thực hiện **Batching** (gộp nhiều lệnh set state lại để chạy một lần duy nhất vào cuối event handler) nhằm tối ưu hiệu năng render. Do đó, ngay sau khi gọi `setCount(count + 1)`, bạn in ra `console.log(count)` thì giá trị vẫn là giá trị cũ.
*   **Cách cập nhật đúng khi phụ thuộc vào giá trị cũ:** Truyền vào một **callback function (updater function)**.
    ```typescript
    // Sai (nếu gọi liên tục 3 lần, count chỉ tăng 1 vì count bị giữ ở snapshot cũ)
    setCount(count + 1);
    setCount(count + 1);

    // Đúng (mỗi lần gọi sẽ nhận giá trị mới nhất thực tế từ queue)
    setCount(prevCount => prevCount + 1);
    setCount(prevCount => prevCount + 1);
    ```

### Câu 88: Ý nghĩa của Dependency Array trong useEffect.
*   `[]` (Mảng rỗng): Callback chỉ chạy **đúng một lần duy nhất** sau lần render đầu tiên (tương đương `componentDidMount`).
*   `[dep1, dep2]` (Có dependency): Callback chạy sau lần render đầu tiên, và chạy lại **mỗi khi có ít nhất một trong các phần tử trong mảng dependency thay đổi** giá trị so với lần render trước.
*   `Không truyền` (Omitted): Callback chạy **sau mỗi lần render** của component. (Rất nguy hiểm, dễ gây loop vô tận nếu trong callback có lệnh set state).

### Câu 89: Hàm Cleanup trong useEffect được gọi khi nào và dùng để làm gì?
*   **Khi nào được gọi:** Được gọi ngay **trước khi** component bị unmount khỏi DOM, HOẶC **trước mỗi lần chạy lại** callback của `useEffect` ở lần render tiếp theo.
*   **Mục đích:** Để dọn dẹp các tài nguyên/tác vụ nền tránh rò rỉ bộ nhớ (Memory Leak).
    *   *Ví dụ thực tế:* Clear bộ đếm `setInterval`/`setTimeout`, hủy đăng ký sự kiện (`removeEventListener`), ngắt kết nối WebSocket, hoặc gọi hủy request API (`AbortController`).

### Câu 90: Sự khác biệt bản chất giữa useMemo và useCallback?
*   `useMemo`: Dùng để lưu trữ bộ nhớ đệm (cache) **kết quả trả về của một phép tính toán phức tạp**.
    ```typescript
    const doubleValue = useMemo(() => heavyCalculation(count), [count]);
    ```
*   `useCallback`: Dùng để lưu trữ bộ nhớ đệm chính **đối tượng hàm (function instance)** để tránh việc tạo lại hàm mới sau mỗi lần render.
    ```typescript
    const handleButtonClick = useCallback(() => { doSomething(id); }, [id]);
    ```
*   **Khi nào thực sự cần dùng:** Chỉ sử dụng khi cần tối ưu hiệu năng của component con bằng `React.memo` (truyền callback/data làm props cho con), hoặc khi hàm/kết quả đó là dependency của một hook khác (như `useEffect`). Lạm dụng chúng vô tội vạ sẽ làm code chậm hơn vì bản thân việc so sánh dependency array cũng tốn chi phí bộ nhớ.

### Câu 91 & 92: Rules of Hooks & Custom Hook.
*   **2 Quy tắc bắt buộc của Hooks:**
    1.  **Chỉ gọi Hooks ở tầng trên cùng (Top-level):** Không gọi hooks bên trong vòng lặp (`for`), câu điều kiện (`if`), hoặc các hàm lồng nhau. Điều này đảm bảo React luôn gọi các hooks theo đúng thứ tự cố định ở mỗi lần render.
    2.  **Chỉ gọi Hooks từ React Function:** Chỉ gọi từ Functional Component hoặc từ một Custom Hook khác.
*   **Custom Hook:** Là một hàm JavaScript tự định nghĩa nhằm tách biệt và chia sẻ logic chứa state giữa các component.
    *   *Điều kiện bắt buộc:* Tên hàm bắt buộc phải bắt đầu bằng từ khóa **`use`** (Ví dụ: `useFetch`, `useAuth`, `useLocalStorage`). Điều này giúp các công cụ linter (ESLint) nhận diện và kiểm tra 2 Quy tắc của Hooks nêu trên.

### Câu 93: Hiện tượng "Stale Closure" trong React Hooks là gì? Cho ví dụ.
*   **TL;DR:** Xảy ra khi một hàm (closure) được tạo ra bên trong component chụp lại (capture) các biến state cũ từ một lần render trước đó, dẫn đến việc khi hàm đó được thực thi sau này, nó vẫn đọc và xử lý trên giá trị state đã lỗi thời.
*   **Ví dụ cụ thể:**
    ```typescript
    const [count, setCount] = useState(0);

    useEffect(() => {
      const interval = setInterval(() => {
        // Stale closure: hàm này capture giá trị count = 0 ban đầu
        console.log("Count hiện tại: " + count); // Sẽ luôn in ra 0
        setCount(count + 1); // Sẽ chỉ tăng lên 1 và dừng ở đó
      }, 1000);
      return () => clearInterval(interval);
    }, []); // Dependency array trống làm useEffect không bao giờ chạy lại để cập nhật closure mới
    ```
    *   *Cách sửa:* Thêm `count` vào dependency array của `useEffect`, hoặc dùng callback update state `setCount(prev => prev + 1)`.

### Câu 94: Hai điểm khác biệt chính giữa useRef và useState?
1.  **Kích hoạt Re-render:** Thay đổi giá trị của `useState` sẽ kích hoạt component re-render. Thay đổi giá trị của `useRef` (thông qua `.current`) **không bao giờ** làm component bị re-render.
2.  **Tính đồng bộ:** Giá trị cập nhật của `useRef.current` có hiệu lực ngay lập tức tại dòng code tiếp theo (đồng bộ). Giá trị cập nhật của `useState` chỉ được cập nhật ở lượt render tiếp theo.

---

## 3. Kiến Trúc Luồng Dữ Liệu & Định Tuyến (Routing & State Management)

### Câu 95 & 96: Unidirectional Data Flow & Prop Drilling.
*   **Unidirectional Data Flow (Luồng dữ liệu một chiều):** Trong React, dữ liệu chỉ có thể truyền từ trên xuống dưới (từ Component cha sang con qua props). Component con không thể truyền ngược dữ liệu trực tiếp lên cha, mà phải thông qua việc gọi một hàm callback được truyền xuống từ cha.
*   **Prop Drilling:** Là hiện tượng phải truyền một prop qua quá nhiều tầng component trung gian chỉ để đưa nó đến một component con ở rất sâu bên dưới, dù các component trung gian hoàn toàn không sử dụng prop đó.
*   **Cách khắc phục:**
    *   Sử dụng **React Context API** để cung cấp dữ liệu trực tiếp cho các component tiêu thụ.
    *   Sử dụng các thư viện quản lý state tập trung như **Zustand**, **Redux Toolkit**.
    *   Sử dụng cơ chế **Component Composition** (truyền component con trực tiếp qua `children`).

### Câu 97 & 101: Nhược điểm Context API khi ứng dụng phình to & Zustand/Redux giải quyết thế nào?
*   **Nhược điểm Context API:** Context API không được thiết kế cho các state thay đổi với tần suất cao (High-frequency state changes). Khi giá trị của Context provider thay đổi, **tất cả các component tiêu thụ (consume) context đó đều bị ép buộc re-render**, bất kể chúng chỉ sử dụng một phần nhỏ dữ liệu trong context đó.
*   **Zustand / Redux giải quyết thế nào:**
    *   Chúng sử dụng cơ chế **Selector-based subscription**. Component chỉ đăng ký lắng nghe và re-render khi phần dữ liệu cụ thể được chọn thay đổi.
    *   *Ví dụ với Zustand:* `const user = useStore(state => state.user)`. Nếu `state.settings` thay đổi, component này hoàn toàn không bị re-render.

### Câu 99: Trong React Router v6, component <Outlet/> đóng vai trò gì?
*   `<Outlet/>` là một component giữ chỗ (placeholder) dùng trong cơ chế **Nested Routing (Định tuyến lồng nhau)**. Nó cho phép render các component con tương ứng với route con hiện tại bên trong giao diện chung (Layout) của route cha.
    *   *Ví dụ:* Route cha `/admin` định nghĩa một sidebar chung. Khi user truy cập `/admin/users`, `<Outlet/>` nằm trong Layout Admin sẽ tự động render component `UserList`.

### Câu 100: Pattern để tạo Protected Route bảo vệ trang cần đăng nhập?
*   Tạo một component bọc ngoài (Wrapper Component) để kiểm tra trạng thái đăng nhập.
    ```typescript
    import { Navigate, Outlet } from 'react-router-dom';

    const ProtectedRoute = () => {
      const { isAuthenticated } = useAuth(); // Lấy từ Context/Zustand

      if (!isAuthenticated) {
        // Chuyển hướng về trang login và lưu lại trang hiện tại để redirect sau
        return <Navigate to="/login" replace />;
      }

      return <Outlet />; // Render các route con nếu đã đăng nhập
    };
    ```
    *Cấu hình Route:*
    ```typescript
    <Route element={<ProtectedRoute />}>
      <Route path="/dashboard" element={<Dashboard />} />
      <Route path="/profile" element={<Profile />} />
    </Route>
    ```

---

## 4. Tối Ưu Hiệu Năng & Bảo Mật (Performance, Security, Testing)

### Câu 102: React.memo tối ưu hiệu năng bằng cơ chế nào? Khi nào nó vô dụng?
*   **Cơ chế:** `React.memo` là một Higher-Order Component (HOC). Nó thực hiện so sánh nông (**Shallow Comparison**) các props mới và props cũ trước khi quyết định có cho phép component con re-render hay không. Nếu props không đổi, React sẽ bỏ qua lượt render đó.
*   **Khi nào vô dụng:**
    *   Khi props truyền vào là các kiểu dữ liệu tham chiếu (Object, Array, Function) được tạo mới ở component cha ở mỗi lần render mà không được bọc bởi `useMemo`/`useCallback`. Lúc này phép so sánh nông luôn trả về false, component con vẫn re-render và ta tốn thêm chi phí chạy phép so sánh props vô ích.

### Câu 103 & 104: Code Splitting, React.lazy và Suspense.
*   **Code Splitting:** Là kỹ thuật chia tách gói JS bundle lớn của ứng dụng thành các file bundle nhỏ hơn. Thay vì tải toàn bộ code của trang web ngay lần đầu, ứng dụng chỉ tải phần code cần thiết cho trang hiện tại, cải thiện đáng kể thời gian tải trang đầu tiên (FCP).
*   **Cài đặt kết hợp React.lazy & Suspense:**
    ```typescript
    import React, { lazy, Suspense } from 'react';

    // Lazy load component trang Admin
    const AdminDashboard = lazy(() => import('./pages/AdminDashboard'));

    const App = () => (
      <Suspense fallback={<div>Đang tải trang...</div>}>
        <AdminDashboard />
      </Suspense>
    );
    ```

### Câu 106: Tại sao KHÔNG nên mutate trực tiếp State/Props?
*   React dựa trên nguyên lý **Immutability (Tính bất biến)** để kiểm tra sự thay đổi của dữ liệu. Nếu bạn mutate trực tiếp (Ví dụ: `state.user.name = "New Name"`), tham chiếu của object `state.user` không hề thay đổi.
*   Do đó, các phương thức so sánh nông của React (trong Virtual DOM diffing hoặc `React.memo`) sẽ cho rằng dữ liệu không đổi và quyết định **không re-render**, dẫn đến giao diện hiển thị không khớp với dữ liệu thực tế.

### Câu 107: Sự khác biệt giữa useLayoutEffect và useEffect?
*   `useEffect`: Chạy **bất đồng bộ sau khi** React đã vẽ (paint) giao diện lên màn hình của trình duyệt. Tránh làm nghẽn quá trình render giao diện chính.
*   `useLayoutEffect`: Chạy **đồng bộ ngay sau khi** React cập nhật DOM nhưng **trước khi trình duyệt thực hiện vẽ (paint)** lên màn hình. Thường dùng khi cần tính toán kích thước phần tử DOM (như lấy chiều cao, chiều rộng phần tử để định vị tooltip) nhằm tránh hiện tượng giao diện bị giật/nhấp nháy (flicker).

### Câu 108: TanStack Query (React Query) tự động hóa những gì?
1.  **Caching (Bộ nhớ đệm dữ liệu server):** Tự động lưu cache kết quả API. Nếu gọi lại cùng API ở trang khác, trả ngay data từ cache và âm thầm fetch mới dưới nền (stale-while-revalidate).
2.  **Auto Re-fetching:** Tự động gọi lại API khi người dùng quay lại tab ứng dụng (focus window), khi kết nối mạng được khôi phục, hoặc sau một khoảng thời gian nhất định (polling).
3.  **Quản lý trạng thái yêu cầu:** Tự động cung cấp các biến boolean tiện lợi như `isLoading`, `isError`, `isFetching`.
4.  **Pagination & Infinite Scroll support:** Hỗ trợ phân trang và lazy load danh sách dài một cách mượt mà.

### Câu 109: Tại sao lưu JWT vào localStorage lại nguy hiểm? Phương án thay thế?
*   **Nguy hiểm:** localStorage có thể truy cập được bởi bất kỳ đoạn mã JavaScript nào chạy trên cùng tên miền (origin). Nếu trang web bị tấn công **XSS (Cross-Site Scripting)** qua các thư viện bên thứ ba hoặc do lọc đầu vào kém, kẻ tấn công có thể dễ dàng đánh cắp JWT và giả mạo người dùng.
*   **Phương án thay thế (Senior):**
    *   Lưu Access Token trong bộ nhớ tạm (**In-Memory / React State**) và lưu Refresh Token trong **HttpOnly Cookie** kèm theo cờ `Secure` và `SameSite=Strict`. Cờ HttpOnly ngăn chặn JavaScript đọc token, vô hiệu hóa hoàn toàn nguy cơ mất token qua XSS.

### Câu 110 & 111: Chiến lược Testing & Tại sao dùng React Testing Library (RTL)?
*   **Chiến lược Frontend Testing:** Tập trung vào **Integration Tests** (Test luồng tương tác giữa các component) và **Unit Tests** cho các logic hàm thuần túy/Custom hooks. Test giao diện quan trọng bằng **E2E Tests** (Cypress / Playwright).
*   **Tại sao dùng RTL (thay vì Enzyme):**
    *   Enzyme tập trung vào kiểm tra **Implementation Details** (Test state nội bộ, props, tên phương thức của class). Điều này làm test dễ bị gãy (fragile) khi ta refactor code dù giao diện hiển thị cho người dùng không đổi.
    *   RTL hướng tới triết lý: *"Code test của bạn càng giống với cách người dùng sử dụng ứng dụng của bạn, thì nó càng đáng tin cậy."* RTL truy vấn DOM dựa trên các phần tử hiển thị thực tế (như text, label, role: button), giúp test bền vững và phản ánh đúng trải nghiệm người dùng thực tế.

### Câu 112: Làm sao mock một API call trong Jest khi component gọi fetch/axios?
*   Sử dụng thư viện **msw (Mock Service Worker)** để chặn bắt request ở tầng mạng (network layer) là cách tốt nhất hiện nay.
*   Hoặc sử dụng cách mock trực tiếp module `axios` trong Jest:
    ```typescript
    import axios from 'axios';
    jest.mock('axios');
    const mockedAxios = axios as jest.Mocked<typeof axios>;

    test('should fetch students successfully', async () => {
      mockedAxios.get.mockResolvedValueOnce({ data: [{ id: 1, name: 'Hung' }] });
      // Chạy component test...
    });
    ```
