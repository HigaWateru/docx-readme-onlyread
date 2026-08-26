# AI Integration Foundation: LLM, Agents, RAG & MCP

Mục tiêu của chương này là giúp bạn thiết kế, xây dựng và vận hành các tính năng AI có kiểm soát trong sản phẩm thật. Học theo thứ tự: **LLM -> structured output/function calling -> embeddings/vector DB -> RAG -> Agent -> ChatOps -> observability/evaluation -> MCP**.

---

## 1. Bản đồ kiến thức và lộ trình

### Beginner: hiểu mô hình và lời gọi API

- **LLM (Large Language Model)**: mô hình dự đoán token tiếp theo dựa trên ngữ cảnh. Nó tạo ra câu trả lời có xác suất cao, không phải cơ sở dữ liệu sự thật.
- **Token, context window, temperature**: token là đơn vị xử lý; context window là giới hạn đầu vào + đầu ra; temperature điều chỉnh độ ngẫu nhiên.
- **Prompt**: instruction, context, examples và output format. Prompt không phải là ranh giới bảo mật.
- **Chat completion**: request thường gồm model, messages, temperature, max output tokens và metadata.
- **Chi phí và độ trễ**: phụ thuộc input tokens, output tokens, model, số lần retry và các bước retrieval/tool.

### Intermediate: làm cho đầu ra đáng tin cậy

- **Structured output**: ép model trả về JSON phù hợp JSON Schema hoặc kiểu dữ liệu của ứng dụng.
- **Function calling/tool calling**: model chỉ đề xuất tên hàm và arguments; ứng dụng phải validate, authorize, execute rồi gửi kết quả trở lại.
- **Embeddings**: vector số biểu diễn ngữ nghĩa của văn bản để tìm các đoạn tương tự.
- **Vector database**: lưu vector kèm metadata và hỗ trợ similarity search, filter, hybrid search hoặc reranking.
- **RAG (Retrieval-Augmented Generation)**: lấy dữ liệu liên quan từ nguồn tin cậy rồi đưa vào context trước khi sinh câu trả lời.

### Advanced: hệ thống có hành động và quan sát được

- **AI Agent**: vòng lặp gồm mục tiêu, lập kế hoạch, gọi công cụ, quan sát kết quả và quyết định bước kế tiếp.
- **LangChain**: primitive để xây prompt, model, retriever, tool, chain/graph và agent. Dùng abstraction khi nó làm rõ kiến trúc, không dùng để che giấu control flow.
- **Langfuse**: tracing, prompt management, token/cost tracking, scores và dataset để đánh giá ứng dụng LLM.
- **ChatOps**: đưa workflow vận hành vào Slack/Teams/Discord với quyền, approval, audit log và idempotency.
- **Evaluation**: đo correctness, groundedness, tool success rate, latency, cost, refusal và security regression.

### Expert: giao thức, governance và production

- **MCP (Model Context Protocol)**: giao thức chuẩn hóa cách AI client khám phá và gọi tools/resources/prompts từ MCP server.
- **Human-in-the-loop**: hành động có side effect hoặc rủi ro cần approval rõ ràng.
- **Guardrails**: kiểm soát input, output, tool arguments, data access, secrets, rate limit và vòng lặp.
- **AI gateway**: lớp tập trung cho routing model, policy, caching, budgets, redaction, retry và telemetry.
- **Model lifecycle**: chọn model, prompt/versioning, offline eval, canary, rollback và theo dõi drift.

### Lộ trình 8 tuần

| Tuần | Kết quả cần đạt | Bài thực hành |
| --- | --- | --- |
| 1 | Gọi LLM, hiểu token/cost/context | CLI hỏi đáp có structured output |
| 2 | Thiết kế prompt và contract | Bộ prompt versioned + 20 test cases |
| 3 | Function calling an toàn | Tool tra cứu ticket ở chế độ read-only |
| 4 | Embedding và vector DB | Ingest tài liệu Markdown, semantic search |
| 5 | RAG có citation | Trợ lý hỏi đáp knowledge base |
| 6 | Agent có giới hạn | Agent phân loại và tạo draft ticket |
| 7 | Langfuse + evaluation | Trace, cost dashboard, golden dataset |
| 8 | ChatOps + MCP | Bot có approval và MCP server nội bộ |

---

## 2. Kiến trúc tổng thể

```text
User / Slack / Teams / Web UI
              |
       API / ChatOps Adapter
              |
       Auth + Policy + Rate Limit
              |
       AI Orchestrator / Agent
        /        |          \\
   LLM call   Retriever     Tools
      |          |            |
 Model Gateway  Vector DB   APIs / MCP Servers
      |          |            |
      +----------+------------+
                 |
       Response + citations + audit
                 |
       Langfuse traces / metrics / eval
```

### Request lifecycle chuẩn

1. Xác thực user và tạo `request_id`, `trace_id`.
2. Xác định use case, policy và ngân sách token/thời gian.
3. Lấy context từ database, search hoặc vector DB nếu cần.
4. Gọi LLM với schema rõ ràng; không đưa secret vào prompt.
5. Nếu model yêu cầu tool, validate arguments và kiểm tra quyền trên server.
6. Thực thi tool với timeout, retry có giới hạn và idempotency key.
7. Gửi observation về model hoặc kết thúc nếu đã đủ dữ liệu.
8. Validate output, gắn citation, redaction và trả response.
9. Ghi trace, cost, latency, tool result metadata và feedback; không ghi dữ liệu nhạy cảm thô.

---

## 3. LLM và prompt engineering

### Mental model

LLM không tự biết dữ liệu mới nhất, không đảm bảo tính đúng và có thể bị ảnh hưởng bởi context độc hại. Vì vậy:

- Quyết định nghiệp vụ phải nằm ở code/policy, không chỉ ở prompt.
- Dữ liệu tham chiếu phải được phân biệt với instruction.
- Output dùng cho máy phải có schema và validation.
- Nhiệm vụ cần tính toán, truy vấn hoặc side effect nên dùng tool.

### Template prompt có version

```text
SYSTEM v3
Role: Bạn là trợ lý phân loại support ticket.
Rules:
- Chỉ dùng các nhãn trong enum đã cho.
- Nếu thiếu dữ kiện, trả needs_more_info=true.
- Không tự thực hiện hành động bên ngoài.

CONTEXT
Ticket text: <untrusted_user_content>
Allowed labels: billing, bug, access, other

OUTPUT SCHEMA
{
  "label": "billing | bug | access | other",
  "confidence": 0.0,
  "needs_more_info": true,
  "reason": "..."
}
```

### Nguyên tắc chọn model

- Bắt đầu bằng model nhỏ, nhanh cho classification/extraction; dùng model lớn cho reasoning phức tạp.
- So sánh theo chất lượng trên golden dataset, p95 latency, cost/request và tỷ lệ retry.
- Tách model cho embedding khỏi model sinh văn bản; embedding model phải ổn định trong cả pipeline.
- Pin model/version khi cần reproducibility và ghi model vào trace.

---

## 4. Function Calling và Tool Design

### Luồng đúng

```text
LLM -> tool_call(name, arguments)
App -> parse + schema validation + authorization
App -> execute with timeout/idempotency
App -> tool_result (minimized data)
LLM -> final answer or next tool_call
```

Model **không được tự gọi HTTP, database hay shell**. Application là nơi quyết định tool có được chạy hay không.

### Contract mẫu

```typescript
type ToolContext = {
  userId: string;
  requestId: string;
  roles: string[];
};

type ToolResult = {
  ok: boolean;
  data?: unknown;
  errorCode?: 'NOT_FOUND' | 'FORBIDDEN' | 'TEMPORARY_FAILURE';
};

async function getTicket(
  input: { ticketId: string },
  context: ToolContext
): Promise<ToolResult> {
  if (!context.roles.includes('support.read')) {
    return { ok: false, errorCode: 'FORBIDDEN' };
  }
  return { ok: true, data: await ticketRepository.findSummary(input.ticketId) };
}
```

### Tool nguy hiểm cần approval

Các tool như `send_email`, `refund_payment`, `delete_resource`, `deploy_production` phải có quyền server-side theo user/tenant/resource, preview/dry-run, approval, idempotency key, audit log, timeout và khả năng rollback.

---

## 5. Embeddings, Vector DB và RAG

### Pipeline ingestion

```text
Documents -> parse -> clean -> split chunks -> enrich metadata
          -> embedding model -> vector DB
```

Metadata tối thiểu nên có `document_id`, `source`, `title`, `section`, `tenant_id`, `permissions`, `updated_at` và `content_hash`.

### Chunking

- Chia theo heading/paragraph trước; chỉ dùng cửa sổ token cố định khi cấu trúc không đáng tin.
- Giữ overlap vừa đủ để không mất ngữ cảnh, nhưng đo bằng evaluation thay vì chọn theo cảm tính.
- Không trộn tài liệu khác quyền truy cập vào cùng context nếu chưa filter theo tenant/user.
- Lưu chunk version và embedding model để re-index có kiểm soát.

### Retrieval và generation

```text
Question -> query rewrite (optional) -> embed
         -> metadata filter -> top-k vector/hybrid search
         -> rerank (optional) -> context budget
         -> LLM -> answer + source citations
```

RAG tốt không chỉ là tăng `top_k`: cần đo **recall@k**, precision của context, answer groundedness và tỷ lệ câu trả lời nói rõ “không đủ dữ liệu”.

### Pseudocode RAG

```python
def answer(question: str, user: User) -> Answer:
    query_vector = embed(question)
    chunks = vector_store.search(
        query_vector=query_vector,
        top_k=8,
        filters={"tenant_id": user.tenant_id, "permissions": {"$contains": user.id}},
    )
    context = select_within_budget(chunks, max_tokens=5000)
    result = llm.generate(build_grounded_prompt(question, context), schema=AnswerSchema)
    return validate_and_attach_citations(result, chunks)
```

### Chọn vector DB

- **PostgreSQL + pgvector**: phù hợp khi dữ liệu quan hệ, quyền và vector cần cùng transaction.
- **Qdrant/Weaviate/Milvus**: phù hợp khi cần vector-native search, filtering và scale riêng.
- **Managed vector service**: giảm vận hành, nhưng phải đánh giá lock-in, data residency, backup và chi phí.

Vector DB không thay thế database nghiệp vụ. Trạng thái thanh toán, quyền và workflow vẫn phải đọc từ source of truth.

---

## 6. AI Agent và LangChain

### Agent loop

```text
Goal -> plan/decide -> call tool -> observe -> validate
  ^                                      |
  +------------- until done -------------+
```

Agent chỉ nên dùng khi cần lựa chọn động giữa nhiều bước hoặc công cụ. Với workflow cố định, pipeline/chain tường minh thường dễ kiểm thử và rẻ hơn.

### Guardrail bắt buộc

- `max_steps`, `max_tool_calls`, `timeout` và token budget;
- allowlist tools theo use case, không expose toàn bộ hệ thống;
- giới hạn kích thước observation và loại bỏ prompt injection từ tool output;
- trạng thái bền vững, correlation id và resume/retry rõ ràng;
- human approval cho side effect.

### LangChain nên dùng thế nào

- Dùng `PromptTemplate`, model adapter, output parser, retriever và tool interface ở biên của ứng dụng.
- Giữ domain logic, authorization và transaction trong service của bạn.
- Với flow nhiều nhánh/trạng thái, cân nhắc graph/state-machine thay vì agent tự do.
- Pin package, kiểm thử prompt/model adapter và trace từng bước.

---

## 7. Langfuse: Observability và Evaluation

### Trace cần trả lời được

Request nào, user/tenant nào, use case nào; prompt/model/version nào; token, cost, latency, retry; retrieval lấy document/chunk nào; tool nào được gọi; và người dùng/evaluator chấm điểm thế nào.

```text
Trace: support_answer
  Span: retrieve_documents
  Generation: answer_prompt_v3 / model-x
  Span: validate_output
  Score: groundedness=0..1
  Score: user_feedback=positive|negative
```

Langfuse có thể self-host hoặc dùng managed deployment. Dùng SDK/callback hiện hành của phiên bản thư viện đang pin, kiểm tra masking PII và xác nhận event không làm chậm request chính.

### Evaluation matrix

| Nhóm | Chỉ số gợi ý |
| --- | --- |
| Quality | correctness, relevance, groundedness, citation accuracy |
| Retrieval | recall@k, precision@k, MRR, no-context rate |
| Agent | task success, tool success, steps/request, approval rate |
| Operations | p50/p95 latency, error rate, retry rate, availability |
| Economics | input/output tokens, cost/request, cache hit rate |
| Safety | prompt-injection pass rate, leakage, unauthorized action |

Golden dataset nên có happy path, thiếu context, dữ liệu mâu thuẫn, prompt injection, quyền khác nhau và tool failure.

---

## 8. ChatOps

```text
Slack/Teams event -> signature verification -> command router
                  -> identity/role mapping -> policy
                  -> read-only query or approval workflow
                  -> response + audit event
```

Quy tắc production: xác minh chữ ký và chống replay; phân biệt channel/thread/DM/tenant; mặc định read-only; lệnh ghi phải hiện preview, target, diff và approver; dùng queue cho tác vụ lâu; có rate limit và correlation id.

Ví dụ: `/deploy staging service-a` chỉ tạo deployment request, bot trả diff + risk + expiry; người có role `release.approver` bấm approve; worker thực thi một lần bằng idempotency key.

---

## 9. MCP (Model Context Protocol)

### MCP cung cấp gì?

- **Tools**: hành động có input schema.
- **Resources**: dữ liệu/context mà client có thể đọc.
- **Prompts**: template prompt được server công bố.
- **Client/server**: client AI kết nối nhiều server qua một giao thức thống nhất.

MCP không tự giải quyết identity, authorization, trust hay data governance. Các lớp đó vẫn phải do hệ thống triển khai.

```text
AI Host/Client -> initialize/capabilities -> MCP Server
AI Host        -> list tools/resources
AI Host        -> call tool(arguments)
MCP Server     -> authenticate + authorize + validate + execute
MCP Server     -> structured result / error
```

### Nguyên tắc xây MCP server

1. Mỗi server có boundary rõ: Git, ticketing, database read-only, deployment.
2. Tool schema nhỏ, mô tả chính xác, input strict và output có cấu trúc.
3. Không expose tool tổng quát kiểu `run_sql` hoặc `run_shell` cho model không tin cậy.
4. Enforce permission trong server, không dựa vào system prompt của client.
5. Chống SSRF, path traversal, injection, secret exfiltration và resource exhaustion.
6. Log caller, tool, resource, decision, duration; redaction trước khi gửi telemetry.

---

## 10. Security, reliability và governance

### Threat model

- Prompt injection trực tiếp và gián tiếp trong tài liệu, web page hoặc tool output.
- Rò rỉ PII, secret, system prompt và dữ liệu cross-tenant.
- Tool misuse, confused deputy và escalation qua ChatOps/MCP.
- Hallucination, stale index, citation giả và output không hợp lệ.
- Denial of wallet: context quá dài, agent loop vô hạn, retry storm.

### Controls

- Identity: user identity xuyên suốt, least privilege, tenant isolation.
- Data: classify, redact, encrypt, retention policy, permission-aware retrieval.
- Execution: schema validation, allowlist, sandbox, timeout, idempotency, approval.
- Reliability: fallback model, circuit breaker, queue, cache TTL, graceful degradation.
- Audit: immutable event cho prompt version, tool decision và side effect.
- Release: offline eval gate, canary, feature flag, rollback prompt/model/index.

Không dùng LLM làm lớp duy nhất để quyết định quyền truy cập. Không coi “đã có citation” là bằng chứng câu trả lời đúng.

---

## 11. Mini Project: Enterprise Knowledge Assistant

Chức năng: ingest Markdown; tìm kiếm theo tenant/permission; trả lời tiếng Việt kèm citation; tool read-only `get_ticket_summary`; ChatOps tạo draft ticket cần approval; Langfuse trace và golden evaluation.

### Definition of Done

- [ ] Ingestion incremental bằng `content_hash`.
- [ ] Schema cho answer, citation và tool arguments.
- [ ] Test prompt injection và cross-tenant access.
- [ ] Agent có giới hạn bước và xử lý tool timeout.
- [ ] Side effect có approval, idempotency và audit log.
- [ ] Dashboard có latency, token/cost, retrieval và quality scores.
- [ ] Tài liệu data retention, model/provider và incident runbook.

### Cấu trúc gợi ý

```text
ai-assistant/
  app/
    api/              # REST, webhook, auth
    domain/           # policy, use cases, schemas
    retrieval/        # ingestion, embeddings, vector search
    agent/            # explicit workflow and tools
    integrations/     # LLM, Langfuse, ChatOps, MCP
  evals/
    golden.jsonl
    regression.py
  infra/
    docker-compose.yml
  docs/
    threat-model.md
```

---

## 12. Checklist học và triển khai

### Nền tảng

- [ ] Giải thích được token, context window, temperature, embedding và hallucination.
- [ ] Phân biệt prompt, structured output, function calling, RAG và agent.
- [ ] Tính được cost/request và biết giới hạn latency.

### Engineering

- [ ] Output và tool arguments đều validate bằng schema.
- [ ] Retrieval có metadata filter theo permission.
- [ ] Agent có giới hạn, timeout, retry và human approval.
- [ ] Prompt/model/index được version và có regression tests.

### Production

- [ ] Có Langfuse trace với PII masking.
- [ ] Có golden dataset, quality score và release gate.
- [ ] Có audit log, incident runbook, budget alert và rollback.
- [ ] MCP/ChatOps server enforce auth ở server-side.

---

## 13. Interview Questions

1. LLM khác gì database tìm kiếm truyền thống?
2. Khi nào dùng RAG, fine-tuning hoặc prompt engineering?
3. Function calling khác gì việc cho model tự chạy code?
4. Làm thế nào ngăn tool call trái quyền?
5. Vì sao vector similarity không đủ để đảm bảo RAG đúng?
6. Chunk size và overlap ảnh hưởng retrieval thế nào?
7. Khi nào chain tốt hơn agent?
8. Bạn đo groundedness và task success ra sao?
9. Langfuse trace cần mask dữ liệu nào?
10. MCP giải quyết interoperability nào và không giải quyết vấn đề nào?
11. Thiết kế approval flow cho lệnh deploy từ ChatOps thế nào?
12. Xử lý prompt injection trong tài liệu được index ra sao?
13. Làm thế nào kiểm soát cost khi agent gọi nhiều tool?
14. Làm sao đảm bảo cross-tenant isolation trong RAG?
15. Chiến lược fallback khi LLM provider bị lỗi là gì?

---

## 14. Senior Notes

- Bắt đầu bằng workflow xác định và tool read-only; agent tự do là bước cuối.
- Chất lượng AI là thuộc tính của dữ liệu, retrieval, prompt, model, policy và UX; đổi model không chữa được dữ liệu sai.
- Mọi output sinh bởi model đều là **untrusted input** cho hệ thống.
- Citation và confidence giúp kiểm soát rủi ro nhưng không thay thế verification.
- Observability phải được thiết kế từ ngày đầu; không thể debug production chỉ bằng câu trả lời cuối cùng.
- Tách “model can suggest” khỏi “application can execute”. Đây là ranh giới quan trọng nhất của AI integration.

---

## 15. Tài liệu tham khảo

- [OpenAI Platform Documentation](https://platform.openai.com/docs)
- [Anthropic Documentation](https://docs.anthropic.com/)
- [LangChain Documentation](https://python.langchain.com/docs/)
- [Langfuse Documentation](https://langfuse.com/docs)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [Qdrant Documentation](https://qdrant.tech/documentation/)
- [pgvector](https://github.com/pgvector/pgvector)