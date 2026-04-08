# 📚 Agent Skills trong n8n — Lý Thuyết Toàn Diện

> Tài liệu phân tích dựa trực tiếp trên flow `Skill_n8n.n8n` và repo `anthropics/skills` (112k ⭐)

---

## Mục Lục
1. [Skills là gì?](#1-skills-là-gì)
2. [Kiến trúc của một Skill](#2-kiến-trúc)
3. [Hệ sinh thái Skills của Anthropic](#3-hệ-sinh-thái)
4. [Phân tích flow Skill_n8n.n8n — Node by Node](#4-phân-tích-flow)
5. [Cơ chế hoạt động bên trong](#5-cơ-chế-hoạt-động)
6. [Danh sách Skills có sẵn](#6-danh-sách-skills)
7. [Cách tạo Skill tùy chỉnh](#7-tạo-skill)
8. [System Prompt vs Skills](#8-so-sánh)
9. [Các pattern nâng cao](#9-pattern-nâng-cao)
10. [Lỗi thường gặp & cách xử lý](#10-lỗi-thường-gặp)

---

## 1. Skills là gì?

### Vấn đề trước khi có Skills

Trước đây, để dạy AI agent làm một việc cụ thể, bạn phải nhét **toàn bộ hướng dẫn vào System Prompt**:

```
System Prompt 10.000 tokens
→ AI mất tập trung, hướng dẫn mâu thuẫn nhau
→ Khó bảo trì khi cần thay đổi  
→ Tốn token với MỌI request dù chỉ hỏi điều đơn giản
```

### Skills giải quyết bằng cách nào?

**Skills = Các file hướng dẫn nhỏ, có cấu trúc, được load ĐỘNG theo yêu cầu**

> **Analogy thực tế:** Thay vì đào tạo trợ lý nhớ hết mọi quy trình, bạn tạo một **thư viện SOP** (Standard Operating Procedures). Khi cần làm gì, trợ lý tra đúng SOP → thực hiện. Đó chính xác là cách Skills hoạt động.

### Định nghĩa chính thức (Anthropic)

> *"Skills are folders of instructions, scripts, and resources that Claude loads **dynamically** to improve performance on specialized tasks."*

**3 yếu tố cốt lõi:**
- **Folders** — Mỗi skill là một thư mục riêng trên GitHub
- **Instructions** — File SKILL.md chứa hướng dẫn bằng Markdown
- **Loads dynamically** — Chỉ load khi cần, không phải lúc nào cũng nạp

---

## 2. Kiến trúc

### Cấu trúc tối thiểu

```
my-skill/
└── SKILL.md    ← BẮT BUỘC — hướng dẫn chính
```

### Cấu trúc đầy đủ

```
my-skill/
├── SKILL.md           ← Instructions + YAML metadata
├── scripts/           ← (Tùy chọn) Scripts phụ trợ
│   └── helper.py
├── examples/          ← (Tùy chọn) Ví dụ tham khảo
│   └── sample_output.md
└── resources/         ← (Tùy chọn) Tài nguyên bổ sung
    └── template.txt
```

### Cấu trúc file SKILL.md

```markdown
---
name: ten-skill-viet-thuong-gach-ngang
description: Mô tả skill làm gì và khi nào AI nên dùng nó.
             Viết đầy đủ từ khóa trigger ở đây.
---

# Tên Skill

## Khi nào dùng
- Trigger condition 1
- Trigger condition 2

## Quy trình thực hiện
### Bước 1: ...
### Bước 2: ...

## Output Format
[Mô tả format output mong muốn]

## Ví dụ
Input: ...
Output: ...

## Quy tắc
- Quy tắc 1 (không được vi phạm)
- Quy tắc 2
```

### Giải thích YAML frontmatter

| Field | Bắt buộc | Chức năng |
|-------|----------|-----------|
| `name` | ✅ | ID duy nhất (lowercase, dùng `-` thay space) |
| `description` | ✅ | AI đọc để **quyết định có dùng skill không** |
| `license` | ❌ | Thông tin bản quyền |

> ⚠️ **Cực kỳ quan trọng:** `description` là phần AI đọc **đầu tiên** để routing. Viết description rõ ràng, đầy đủ từ khóa = AI chọn đúng skill.

---

## 3. Hệ sinh thái

### 2 Repo chính của Anthropic

| Repo | Stars | Nội dung |
|------|-------|---------|
| `anthropics/skills` | 112k ⭐ | Ví dụ đa dạng: creative, tech, enterprise |
| `anthropics/knowledge-work-plugins` | — | Skills cho công việc tri thức |

### Cấu trúc thư mục `anthropics/skills`

```
anthropics/skills/
├── .claude-plugin/    ← Config cho Claude Code plugin marketplace
├── skills/            ← Tất cả examples (16 skills)
│   ├── algorithmic-art/
│   ├── brand-guidelines/      ← Đơn giản nhất, tốt để học
│   ├── canvas-design/
│   ├── claude-api/
│   ├── doc-coauthoring/       ← Phức tạp nhất, pattern hay nhất
│   ├── docx/ pdf/ pptx/ xlsx/ ← Document skills (production-grade)
│   ├── frontend-design/
│   ├── internal-comms/
│   ├── mcp-builder/
│   ├── skill-creator/         ← Meta-skill: tạo Skills mới!
│   ├── slack-gif-creator/
│   ├── theme-factory/
│   ├── web-artifacts-builder/
│   └── webapp-testing/
├── spec/              ← Đặc tả kỹ thuật Agent Skills standard
└── template/
    └── SKILL.md       ← Template bắt đầu (chỉ 4 dòng!)
```

### Template tối thiểu (từ repo)

```markdown
---
name: template-skill
description: Replace with description of the skill and when Claude should use it.
---

# Insert instructions below
```

---

## 4. Phân tích flow Skill_n8n.n8n

### Sơ đồ luồng dữ liệu

```
[User Chat Message]
        ↓
[Set GitHub Repo URLs]  ← Khai báo repos chứa skills
        ↓
[Split Out]  ← Tách từng repo ra xử lý PARALLEL
    ↙               ↘
[List Root Dirs]  [List Skills Dirs /skills]
    ↓                       ↓
[Remove Dots/Dups]  [Remove Errors/Dots]
    ↘                   ↙
  [Merge Directory Structures]
              ↓
         [AI Agent]
        /     |     \
[Chat Model] [Memory] [Tools]
                      ↙      ↘
            [List Files]  [Get File from GitHub]
```

### Chi tiết từng Node

---

#### Node: `Set GitHub Repo URLs`
**Type:** Set (Edit Fields)

```javascript
// Giá trị mặc định trong flow của bạn:
SKILLS_REPOS = [
  "https://github.com/anthropics/skills",
  "https://github.com/anthropics/knowledge-work-plugins"
]
```

> **Để tùy chỉnh:** Thêm repo GitHub của bạn vào array. Chỉ cần repo đó public hoặc credentials có quyền đọc.

---

#### Node: `Split Out`
**Type:** SplitOut

Tách array `SKILLS_REPOS` thành từng item riêng để xử lý song song:
```
Input:  ["repo1", "repo2"]
Output: item1 → "repo1"  (chạy đồng thời)
        item2 → "repo2"  (chạy đồng thời)
```

---

#### Node: `List Root Dirs`
**Type:** GitHub (file.list)  
**Path:** `/`

List tất cả files/folders ở root của mỗi repo. Mục đích: tìm các skill folders nằm trực tiếp ở root (không trong subfolder `/skills`).

---

#### Node: `List Skills Dirs`
**Type:** GitHub (file.list)  
**Path:** `/skills`  
**onError:** `continueRegularOutput`

List nội dung trong folder `/skills` (nếu có). Nếu repo không có folder `/skills` → không crash, flow vẫn tiếp tục.

---

#### Node: `Remove Skills Dirs & Dot Files`
**Type:** Filter

Loại bỏ khỏi danh sách:
- Thư mục tên `skills` (để tránh duplicate với kết quả từ List Skills Dirs)
- Files/folders bắt đầu bằng `.` (dotfiles như `.gitignore`, `.claude-plugin`)

---

#### Node: `Remove Errors and Dot Files`
**Type:** Filter

Loại bỏ:
- Items có lỗi (repo không có `/skills` folder → có error field)
- Dotfiles

---

#### Node: `Merge Directory Structures`
**Type:** Merge

Gộp 2 luồng (Root Dirs + Skills Dirs) thành **1 danh sách JSON thống nhất**. Đây là input cho system prompt của Agent.

---

#### Node: `AI Agent` — Trung tâm của flow

**Type:** LangChain Agent  
**Temperature:** 0.1 (thấp = ổn định, follow instructions chính xác)  
**maxIterations:** 150  
**executeOnce:** true

**System Prompt được inject động:**
```
You are a helpful assistant. Your capabilities and instructions 
for completing tasks are stored in skill files.

## How You Work
1. Identify which skill(s) are relevant to the request
   (traverse directories using the List Files tool as needed)
2. Fetch those skill files (use concurrent tool calls when fetching multiple)
3. Follow the instructions in those files to complete the task

## Available Skills Files and Directories
[JSON list TỰ ĐỘNG ĐƯỢC INJECT từ Merge node]

## IMPORTANT
- Do NOT answer based on general knowledge
- Your instructions live in the skill files
```

> 🔑 **Key insight:** Agent **không có kiến thức tích hợp** — nó PHẢI đọc skill file trước khi làm bất cứ điều gì. Đây là thiết kế có chủ đích để đảm bảo consistency.

---

#### Node: `Chat Model`
**Type:** OpenRouter → Google Gemini 3 Flash Preview

Bạn có thể thay bằng bất kỳ model nào. Gợi ý:
- **Claude Sonnet** — Tốt nhất cho complex reasoning + follow instructions
- **GPT-4o** — Cân bằng tốc độ và chất lượng
- **Gemini Flash** — Nhanh, rẻ, đủ tốt cho tasks đơn giản

---

#### Node: `Simple Memory`
**Type:** Buffer Window Memory

Nhớ lịch sử conversation trong 1 session. Không persistent qua lần mở lại.

---

#### Tool: `List Files by Path Name`
**Type:** GitHub Tool

Cho phép Agent **tự khám phá** cấu trúc thư mục khi cần:
- `repoUsername` — Agent tự điền
- `repoName` — Agent tự điền  
- `Path` — Agent tự xác định (VD: `/tiktok-skills`, `/tiktok-skills/caption-writer`)

---

#### Tool: `Get a File From GitHub`
**Type:** HTTP Request Tool

Tải **nội dung text thuần** của skill file.

> **Tại sao dùng HTTP thay GitHub node?** GitHub node trả về base64 encoded (phải decode thêm bước). HTTP node với header `Accept: application/vnd.github.v3.raw` trả về plain text trực tiếp → tiết kiệm token, đơn giản hơn.

---

## 5. Cơ chế hoạt động

### Lifecycle của một request thực tế

```
User: "Viết caption TikTok quảng bá sách mindfulness"

───── PHASE 1: STARTUP (chạy khi chat bắt đầu) ─────
Set repos → Split → List Dirs (parallel) → Filter → Merge
Kết quả: directory map được inject vào system prompt

───── PHASE 2: AGENT REASONING ─────
Agent đọc directory map:
  {type:"dir", path:"tiktok-book-seller", ...}
  {type:"dir", path:"skills/doc-coauthoring", ...}
→ Agent nhận diện: "tiktok-book-seller" có vẻ relevant

───── PHASE 3: DYNAMIC SKILL FETCHING ─────
[Tool call 1] List Files by Path: "/tiktok-book-seller"
→ Thấy: SKILL.md

[Tool call 2] Get File from GitHub: ".../SKILL.md"
→ Đọc toàn bộ instructions

───── PHASE 4: EXECUTION ─────
Agent follow instructions trong SKILL.md
→ Áp dụng đúng format, tone, structure đã định nghĩa
→ Output caption theo yêu cầu
```

### Tại sao Agent biết chọn đúng skill?

Cơ chế routing dựa trên **description matching**:

1. Agent có trong system prompt: danh sách `{path, type, orgName, repoName}`
2. Agent đọc `description` trong YAML của các skill (thông qua tool calls)
3. So khớp intent của user với description
4. Chọn skill phù hợp nhất

### Concurrent fetching (quan trọng cho performance)

Khi cần nhiều skills, Agent gọi nhiều tools **đồng thời**:
```
[Fetch skill A] ──── (parallel) ──── [Fetch skill B]
                           ↓
                    [Combine & execute]
```

---

## 6. Danh sách Skills có sẵn

| Skill | Nhóm | Mô tả | Học pattern |
|-------|------|-------|-------------|
| `brand-guidelines` | Design | Áp dụng brand colors & font | ✅ Bắt đầu ở đây |
| `doc-coauthoring` | Enterprise | Workflow 3 giai đoạn đồng tác giả | ✅ Phức tạp nhất |
| `skill-creator` | Meta | Tạo Skills mới bằng Skills! | ✅ Rất hữu ích |
| `internal-comms` | Enterprise | Viết truyền thông nội bộ | Workflow đơn giản |
| `algorithmic-art` | Creative | Nghệ thuật thuật toán | Code-based |
| `canvas-design` | Creative | Thiết kế visual | Multi-step |
| `theme-factory` | Creative | Tạo color palette | JSON output |
| `frontend-design` | Tech | UI/HTML/CSS | Code output |
| `webapp-testing` | Tech | Test web apps tự động | Playwright |
| `mcp-builder` | Tech | Build MCP servers | Advanced |
| `claude-api` | Tech | Dùng Claude API | API patterns |
| `web-artifacts-builder` | Tech | Build web artifacts | HTML output |
| `slack-gif-creator` | Fun | Tạo GIFs cho Slack | Image gen |
| `docx` | Documents | Tạo/edit Word files | Production |
| `pdf` | Documents | Xử lý PDF | Production |
| `pptx` | Documents | Tạo PowerPoint | Production |
| `xlsx` | Documents | Xử lý Excel | Production |

> 💡 **Thứ tự học:** `brand-guidelines` → `doc-coauthoring` → `skill-creator` → tự tạo skill riêng

---

## 7. Tạo Skill tùy chỉnh

### Bước 1: Trả lời 3 câu hỏi nền tảng

1. Skill này làm **đúng một việc** gì? (càng cụ thể càng tốt)
2. **Khi nào** Agent nên trigger skill này? (list keywords)
3. **Output** mong muốn trông như thế nào? (format, length, structure)

### Bước 2: Template SKILL.md đầy đủ

```markdown
---
name: ten-skill-cua-ban
description: Mô tả đầy đủ skill này làm gì. Dùng khi user yêu cầu 
  [X], [Y], [Z]. Trigger khi nhắc đến: [keyword1], [keyword2], [keyword3].
  Không dùng khi: [negative condition].
---

# Tên Skill

## Mục đích
[1-2 câu mô tả rõ ràng]

## Khi nào dùng (Trigger conditions)
- User yêu cầu [X]
- User đề cập đến [Y]
- User hỏi về [Z]

## Khi nào KHÔNG dùng
- Khi user chỉ muốn [A]
- Khi context là [B]

## Quy trình thực hiện

### Bước 1: [Tên bước]
[Hướng dẫn chi tiết, rõ ràng, cụ thể]

### Bước 2: [Tên bước]
[Hướng dẫn chi tiết]

### Bước 3: [Tên bước]
[Hướng dẫn chi tiết]

## Output Format

```
[Template format của output]
[Ví dụ: "Hook: ...\nBody: ...\nCTA: ..."]
```

## Ví dụ tốt

**Input:** [Ví dụ input cụ thể]

**Output:**
[Ví dụ output cụ thể, thực sự chất lượng]

## Quy tắc bắt buộc
- ✅ Luôn làm: [quy tắc]
- ✅ Luôn làm: [quy tắc]
- ❌ Không được: [quy tắc]
- ❌ Không được: [quy tắc]
```

### Bước 3: Đưa lên GitHub

```bash
mkdir my-tiktok-skills
cd my-tiktok-skills
mkdir ten-skill-cua-ban
# Tạo SKILL.md trong thư mục đó
git init && git add . && git commit -m "Add skill"
git remote add origin https://github.com/username/my-tiktok-skills
git push origin main
```

### Bước 4: Thêm vào n8n

Trong node `Set GitHub Repo URLs`:
```javascript
[
  "https://github.com/anthropics/skills",
  "https://github.com/username/my-tiktok-skills"  // ← Thêm vào đây
]
```

---

## 8. So sánh: System Prompt vs Skills

| Tiêu chí | System Prompt | Skills |
|----------|---------------|--------|
| **Kích thước** | Bị giới hạn context window | Vô hạn (load khi cần) |
| **Bảo trì** | 1 file khổng lồ khó edit | Mỗi skill = 1 file riêng |
| **Token cost** | Trả cho MỌI request | Chỉ trả khi skill được dùng |
| **Modularity** | Không có | Cao — bật/tắt từng skill |
| **Version control** | Phức tạp | Git tự nhiên |
| **Chia sẻ** | Phải share cả flow | Chỉ cần share URL repo |
| **Team work** | Khó | Pull Request trên GitHub |

### Khi nào dùng gì?

```
✅ Dùng System Prompt cho:
   - Persona và tone chung của agent
   - Rules áp dụng cho MỌI interactions
   - Context cố định (VD: "You are assistant for Company X")

✅ Dùng Skills cho:
   - Instructions cho từng task cụ thể
   - Workflows phức tạp có nhiều bước
   - Instructions cần thay đổi thường xuyên
   - Instructions dùng chung giữa nhiều flows
```

---

## 9. Các pattern nâng cao

### Pattern 1: Skills theo domain

```
my-content-skills/
├── tiktok/
│   ├── caption-writer/SKILL.md
│   ├── hook-generator/SKILL.md
│   └── hashtag-researcher/SKILL.md
├── seo/
│   ├── keyword-analyzer/SKILL.md
│   └── meta-writer/SKILL.md
└── books/
    ├── title-creator/SKILL.md
    └── blurb-writer/SKILL.md
```

### Pattern 2: Skills Chain (Skill gọi Skill)

```markdown
## Quy trình tổng hợp
1. Dùng `hook-generator` skill để tạo hook
2. Dùng `caption-writer` skill để mở rộng hook thành caption đầy đủ  
3. Dùng `hashtag-researcher` skill để thêm hashtags phù hợp
4. Kết hợp tất cả theo format cuối
```

### Pattern 3: Multi-repo Skills

```javascript
// n8n node "Set GitHub Repo URLs"
[
  "https://github.com/anthropics/skills",          // Skills chung từ Anthropic
  "https://github.com/your-brand/brand-skills",    // Skills theo thương hiệu
  "https://github.com/your-name/personal-skills"   // Skills cá nhân
]
```

### Pattern 4: Conditional Logic trong Skill

```markdown
## Quy trình
- Nếu user cung cấp tên sách → dùng tên đó làm anchor
- Nếu user chỉ cung cấp chủ đề → tạo hook dựa trên chủ đề
- Nếu user muốn viral → ưu tiên emotional hook
- Nếu user muốn credibility → ưu tiên educational hook
```

---

## 10. Lỗi thường gặp & cách xử lý

### ❌ Agent không chọn đúng skill

**Nguyên nhân:** `description` quá ngắn, thiếu keywords

**Fix:**
```yaml
# SAI - Quá ngắn
description: Viết caption

# ĐÚNG - Đầy đủ từ khóa
description: Viết caption TikTok để quảng bá và bán sách. Dùng khi user 
  muốn tạo nội dung TikTok, caption mạng xã hội, hook thu hút viewer,
  quảng bá sách, content bán hàng online. Trigger khi nhắc đến: TikTok,
  caption, sách, bán hàng, content creator, hook, viral.
```

### ❌ List Skills Dirs báo lỗi

**Nguyên nhân:** Repo không có folder `/skills`

**Fix:** Bình thường — node đã có `continueRegularOutput`. Skill của bạn có thể nằm ở root level thay vì trong `/skills`.

### ❌ Agent không follow instructions

**Nguyên nhân:** System prompt không đủ mạnh

**Fix:** Thêm force instruction:
```
CRITICAL: You MUST read skill files before doing ANYTHING.
NEVER use general knowledge. Instructions ONLY come from skill files.
If no skill is relevant, say so — do not improvise.
```

### ❌ GitHub API rate limit

**Nguyên nhân:** Quá nhiều API calls từ GitHub node

**Fix:** Thêm GitHub Personal Access Token vào credentials trong n8n. Unauthenticated: 60 req/hour. Authenticated: 5,000 req/hour.

### ❌ Slow response (latency cao)

**Nguyên nhân:** Agent phải list dirs + fetch files sequentially

**Fix:** Preload skill content vào system prompt thay vì dynamic loading (trade-off: tốn token hơn nhưng nhanh hơn).

---

## Mental Model Cuối

```
Skills    = Thư viện SOP (Standard Operating Procedures) của AI
GitHub    = Kệ sách chứa SOPs
SKILL.md  = Một cuốn SOP cụ thể cho một nhiệm vụ
description = Tiêu đề cuốn SOP (AI dùng để tìm đúng cuốn)

n8n Flow  = Thủ thư giúp AI biết có những SOPs nào
AI Agent  = Nhân viên đọc SOP rồi thực hiện đúng quy trình
Tools     = Tay của nhân viên (lấy sách từ kệ, đọc nội dung)
```

---

## Tài liệu tham khảo

- **GitHub:** https://github.com/anthropics/skills
- **n8n Template:** https://n8n.io/workflows/13270-use-skills-in-n8n-agent-node/
- **Claude Skills Docs:** https://support.claude.com/en/articles/12512176-what-are-skills
- **Creating Skills:** https://support.claude.com/en/articles/12512198-creating-custom-skills
- **Agent Skills Blog:** https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
- **agentskills.io:** http://agentskills.io

---
*Dựa trên phân tích trực tiếp `Skill_n8n.n8n` và `anthropics/skills` repo — tháng 4/2026*
