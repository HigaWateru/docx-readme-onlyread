# Fullstack Developer Knowledge Base

Kho kiến thức này đi từ Frontend, Backend, Database đến System Design, DevOps, Agile và AI Integration. Mỗi bài ưu tiên cách giải thích từ dễ đến khó, một tình huống thực tế, code minh họa và bài tập để tự kiểm tra.

---

## 🗺️ Bản đồ Tri thức (Map of Knowledge)

Hệ thống tài liệu được phân loại theo các lớp kiến trúc rõ ràng:

### 🎨 1. Frontend Architecture
Chứa các tài liệu cốt lõi về xây dựng giao diện người dùng hiện đại, tối ưu hóa hiệu năng render, quản lý state và build tools.
- ⚙️ [JavaScript ES6+ & TypeScript Deep Dive](1-Frontend/JavaScript-TypeScript.md)
- ⚛️ [React Core Mechanics](1-Frontend/React-Core.md)

### ☕ 2. Backend Engineering (Java & Spring Ecosystem)
Các bài hiện có tập trung vào Java, Spring Boot, JPA/Hibernate và message broker.
- ☕ [Java Core & Virtual Machine (JVM)](2-Backend/Java-Core-Deep-Dive.md)
- 🌱 [Spring Boot & Microservices](2-Backend/Spring-Boot-Microservices.md)
- 🗄️ [Data Persistence (Spring Data JPA & Hibernate)](2-Backend/Spring-Data-JPA-Hibernate.md)
- ✉️ [Asynchronous Messaging (Kafka vs RabbitMQ)](2-Backend/Message-Broker-Kafka-RabbitMQ.md)

### 💾 3. Database Management & Optimization
Các bài hiện có tập trung vào transaction, locking, indexing và tối ưu truy vấn.
- 🔒 [Transactions, Isolation Levels & Locking Strategies](3-Database/Transactions-Isolation-Locking.md)
- 🚀 [Indexing & Query Optimization Techniques](3-Database/Index-Query-Optimization.md)

### 🏢 4. System Design & Distributed Systems
Nguyên lý thiết kế hệ thống phân tán qua các pattern thường gặp.
- 🗺️ [Monolith vs Microservices Architecture](4-System-Design/Monolith-vs-Microservices.md)
- 🌐 [Microservices Architecture Master Guide (Tổng hợp toàn diện)](4-System-Design/Microservices-Master-Guide.md)

### 🔄 5. Agile Project Management
Quy trình vận hành dự án chuẩn Scrum/Agile trong môi trường doanh nghiệp chuyên nghiệp.
- 🏃 [Agile & Scrum Framework in Action](5-Agile-Scrum/Agile-Scrum-Framework.md)

### 🚀 6. DevOps & Infrastructure
Vận hành, đóng gói, tự động hóa quy trình CI/CD và triển khai ứng dụng lên Cloud.
- 🐳 [Docker Containerization](6-DevOps/Docker-Containerization.md)
- 🛩️ [CI/CD Pipelines & Cloud Infrastructure (AWS)](6-DevOps/CI-CD-AWS-Deployment.md)

### 🧠 7. AI Integration Engineering
Nền tảng tích hợp LLM vào sản phẩm: RAG, vector database, tool calling, agent, ChatOps, observability với Langfuse và MCP.
- 🤖 [AI Integration Foundation: OpenAI với Java/Spring Boot](7-AI-Integration/AI-Integration-Foundation.md)

---

## 📐 Cách đọc mỗi bài

Các bài chủ đề dùng cùng một luồng học:
1. **Khái niệm**: đọc Beginner trước, chỉ chuyển sang mức tiếp theo khi đã hiểu thuật ngữ.
2. **Mục đích và ví dụ thực tế**: nối lý thuyết với một vấn đề sản phẩm hoặc vận hành.
3. **Kiến trúc**: lần theo sơ đồ từ trái sang phải hoặc từ trên xuống dưới.
4. **Code Demo**: đọc phần giải thích trước, sau đó chạy từng đoạn nhỏ và thay đổi input để quan sát kết quả.
5. **Best Practices và Common Mistakes**: dùng như checklist khi áp dụng vào dự án.
6. **Interview Questions, Senior Notes và References**: dùng để ôn tập sau khi đã làm xong bài tập.

Đồng thời, mỗi chương luôn đi kèm các phần bổ trợ thực chiến:
- ✅ **Checklist**: Giúp bạn kiểm soát mức độ hoàn thành.
- ✅ **Mindmap & Cheat Sheet**: Tóm tắt nhanh kiến thức cốt lõi.
- ✅ **Interview Tips**: Mẹo đắt giá khi đối mặt với nhà tuyển dụng.
- ✅ **Mini Project & Bài tập thực hành**: Tự tay triển khai ứng dụng thực tế.

> **Quy ước code:** các đoạn có thể chạy độc lập sẽ ghi ngôn ngữ và cấu hình cần thiết. Các đoạn sơ đồ, pseudocode hoặc fragment chỉ dùng để minh họa ý tưởng và không nên copy nguyên xi vào production.

## 🧭 Lộ trình gợi ý

1. JavaScript/TypeScript → React.
2. Java Core → Spring Boot → JPA/Hibernate → Kafka/RabbitMQ.
3. Transactions/locking → Index/query optimization.
4. Monolith vs Microservices → Microservices Master Guide → Docker → CI/CD/AWS.
5. Agile/Scrum và AI Integration học song song khi đã có một ứng dụng mẫu để thực hành.

---

## 🛠️ Yêu cầu môi trường học tập (Prerequisites)
- **Java JDK**: Version 17+ (LTS).
- **Node.js**: Version 18+ (LTS).
- **Docker**: Bản mới nhất để chạy các container MySQL, PostgreSQL, Redis, Kafka.
- **IDE**: IntelliJ IDEA (cho Java) và VS Code (cho React/TypeScript).
