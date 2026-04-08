# 🎯 Skills trong n8n — Áp dụng Thực Tế cho Content Creator Bán Sách TikTok

> **Cho ai:** Người sáng tạo nội dung TikTok, bán sách/ebook, cần hệ thống AI tạo content hàng loạt nhưng vẫn nhất quán về chất lượng và tone.

---

## Mục Lục
1. [Tại sao Skills phù hợp với bạn?](#1-tại-sao-skills)
2. [Kiến trúc hệ thống content của bạn](#2-kiến-trúc-hệ-thống)
3. [Các Skills cần tạo](#3-danh-sách-skills-cần-tạo)
4. [SKILL.md mẫu đầy đủ — 5 Skills quan trọng nhất](#4-skillmd-mẫu)
5. [Cách tích hợp vào n8n flow](#5-tích-hợp-n8n)
6. [Prompt Engineering trong SKILL.md](#6-prompt-engineering)
7. [Workflow thực tế: Từ ý tưởng đến 30 video/tuần](#7-workflow-thực-tế)
8. [Mở rộng hệ thống](#8-mở-rộng)

---

## 1. Tại sao Skills phù hợp với bạn?

### Vấn đề của content creator bán sách TikTok

```
Mỗi ngày cần: 3-5 video TikTok
Mỗi video cần: hook, script, caption, hashtag, CTA
Mỗi sách khác nhau: tone, target audience, key messages khác nhau
→ Nếu dùng 1 system prompt chung → content bị generic, mất chất
→ Nếu viết tay từng cái → mất 3-4 tiếng/ngày
```

### Skills giải quyết bằng cách nào?

```
Skill "tiktok-book-hook"      → Tạo hook theo từng thể loại sách
Skill "tiktok-script-writer"  → Viết script 30-60 giây chuẩn format
Skill "tiktok-caption"        → Caption + hashtag tối ưu TikTok SEO
Skill "book-blurb-writer"     → Mô tả sách hấp dẫn cho bio/description
Skill "comment-reply"         → Trả lời comment để tăng engagement

→ Mỗi skill = 1 chuyên gia chuyên biệt
→ Agent load đúng chuyên gia khi cần
→ Output nhất quán, chuyên nghiệp, đúng format
```

### So sánh trước/sau khi dùng Skills

| | Trước (viết tay) | Sau (Skills trong n8n) |
|--|-----------------|----------------------|
| **Thời gian/ngày** | 3-4 tiếng | 30 phút review |
| **Số video/tuần** | 5-7 | 20-30 |
| **Nhất quán tone** | Khó giữ | Đảm bảo 100% |
| **A/B testing** | Không khả thi | Dễ dàng |
| **Scale** | Bị giới hạn | Unlimited |

---

## 2. Kiến trúc hệ thống

### Cấu trúc repo GitHub của bạn

```
tiktok-book-skills/              ← GitHub repo chính
│
├── README.md
│
├── hooks/                       ← Skills tạo hook/mở đầu video
│   ├── hook-emotional/
│   │   └── SKILL.md
│   ├── hook-curiosity/
│   │   └── SKILL.md
│   └── hook-controversy/
│       └── SKILL.md
│
├── scripts/                     ← Skills viết kịch bản
│   ├── script-book-review/
│   │   └── SKILL.md
│   ├── script-lesson-extract/
│   │   └── SKILL.md
│   └── script-story-format/
│       └── SKILL.md
│
├── captions/                    ← Skills viết caption
│   ├── caption-tiktok-sell/
│   │   └── SKILL.md
│   └── caption-tiktok-value/
│       └── SKILL.md
│
├── sales/                       ← Skills bán hàng
│   ├── book-blurb-writer/
│   │   └── SKILL.md
│   └── bio-link-copy/
│       └── SKILL.md
│
└── engagement/                  ← Skills tăng tương tác
    ├── comment-reply/
    │   └── SKILL.md
    └── trend-adapter/
        └── SKILL.md
```

### Cách n8n flow kết nối với repo

```
[n8n: Set GitHub Repo URLs]
  ├── "https://github.com/anthropics/skills"         ← Skills chung
  └── "https://github.com/username/tiktok-book-skills" ← Skills của bạn

[n8n: AI Agent] nhận directory map → chọn skill phù hợp → tạo content
```

---

## 3. Danh sách Skills cần tạo

### Priority 1 — Tạo ngay (core workflow)

| Skill | Trigger | Output |
|-------|---------|--------|
| `hook-generator` | "tạo hook", "mở đầu video" | 3-5 hook options |
| `tiktok-script-writer` | "viết script", "kịch bản" | Script 30/60/90s |
| `tiktok-caption` | "caption", "mô tả video" | Caption + hashtags |
| `book-blurb-writer` | "mô tả sách", "blurb" | 3 versions blurb |
| `cta-generator` | "CTA", "kêu gọi mua" | 5 CTA variations |

### Priority 2 — Tạo sau khi hệ thống chạy ổn

| Skill | Trigger | Output |
|-------|---------|--------|
| `comment-reply` | "trả lời comment" | Replies tăng engagement |
| `series-planner` | "lên kế hoạch series" | Plan 7-30 video |
| `trend-adapter` | "theo trend", "sound viral" | Adapt content cho trend |
| `ab-test-variants` | "A/B test", "variants" | Multiple versions |
| `thumbnail-copy` | "thumbnail text", "title card" | Hook ngắn cho thumbnail |

### Priority 3 — Scale up

| Skill | Trigger | Output |
|-------|---------|--------|
| `email-sequence` | "email", "nurture" | Email sequence 5-7 emails |
| `landing-page-copy` | "landing page", "sales page" | Full page copy |
| `review-requester` | "xin review", "feedback" | DM template |

---

## 4. SKILL.md Mẫu — 5 Skills Quan Trọng Nhất

---

### SKILL 1: Hook Generator

**File:** `hooks/hook-generator/SKILL.md`

```markdown
---
name: hook-generator
description: Tạo hook mở đầu video TikTok để bán sách và ebook. Dùng khi 
  user muốn tạo hook video, mở đầu hấp dẫn, câu đầu tiên thu hút viewer,
  hook TikTok, hook bán sách. Trigger khi nhắc đến: hook, mở đầu, câu đầu,
  thu hút, giữ chân viewer, 3 giây đầu, dừng scroll.
---

# Hook Generator cho TikTok Bán Sách

## Mục đích
Tạo hook mạnh cho 3 giây đầu video TikTok — thời điểm quyết định 
viewer có xem tiếp hay scroll qua.

## Thông tin cần thu thập từ user

Trước khi tạo hook, hỏi user:
1. **Tên sách / chủ đề sách** là gì?
2. **Target audience** là ai? (VD: gen Z, phụ nữ 25-35, dân văn phòng...)
3. **Thể loại hook muốn**: Emotional / Curiosity / Controversy / Transformation?
4. **Tone**: Nghiêm túc / Vui vẻ / Intimate / Mạnh mẽ?

Nếu user đã cung cấp info → bỏ qua bước hỏi, đi thẳng vào tạo hook.

## Các loại Hook và khi nào dùng

### 🔥 Emotional Hook
**Dùng khi:** Sách về cảm xúc, mối quan hệ, trải nghiệm cá nhân
**Formula:** [Điều đau/khó] + [Cảm xúc mạnh] + [Hint giải pháp]

Ví dụ:
- "Tôi đã làm việc 70 tiếng một tuần và vẫn không giàu — cho đến khi đọc cuốn sách này"
- "Bạn có bao giờ cảm thấy mình làm đúng mọi thứ nhưng vẫn thất bại không?"
- "3 năm trước tôi không dám ước mơ vì 1 niềm tin sai lầm về tiền"

### 🤔 Curiosity Hook  
**Dùng khi:** Sách về kiến thức, kỹ năng, hacks, tips
**Formula:** [Số] + [Thứ bất ngờ/phản trực giác] + [Kết quả]

Ví dụ:
- "Lý do người giàu KHÔNG đặt mục tiêu như bạn nghĩ"
- "Thứ người thành công làm khác biệt trước 8 giờ sáng"
- "Người ta làm theo cách này để học ngoại ngữ mà không cần học ngữ pháp"

### ⚡ Controversy Hook
**Dùng khi:** Sách về mindset, quan điểm độc đáo
**Formula:** [Phủ nhận quan niệm phổ biến] + [Claim táo bạo]

Ví dụ:
- "Làm việc chăm chỉ không làm bạn giàu. Đây là lý do tại sao."
- "Tất cả những gì bạn học về năng suất đều sai"
- "Self-help không giúp bạn phát triển — nếu bạn làm theo cách này"

### 🌟 Transformation Hook
**Dùng khi:** Sách về thay đổi, hành trình, kết quả hữu hình
**Formula:** [Before state] + [Timeframe] + [After state]

Ví dụ:
- "Từ người không có tiết kiệm đến $10,000 trong tài khoản — trong 12 tháng"
- "Tôi đã xóa TikTok 30 ngày và đây là điều xảy ra"
- "Đọc 1 cuốn sách này thay đổi cách tôi nhìn về thời gian mãi mãi"

## Quy trình tạo Hook

1. Xác định thể loại hook phù hợp với sách và audience
2. Tạo **5 hook options** — ít nhất 2 thể loại khác nhau
3. Với mỗi hook: viết cả **tiếng Việt và tiếng Anh** (nếu user muốn test bilingual)
4. Label rõ loại hook và lý do chọn

## Output Format

```
## Hook Options cho [Tên sách]

### Option 1 — [Loại Hook]
**🇻🇳 VI:** [Hook tiếng Việt]
**🇬🇧 EN:** [Hook tiếng Anh]
**Lý do dùng:** [Ngắn gọn tại sao hook này work]

### Option 2 — [Loại Hook]
...

### Option 3 — [Loại Hook]
...

### Option 4 — [Loại Hook]
...

### Option 5 — [Loại Hook]
...

---
**💡 Gợi ý:** [1 câu recommend option nào và tại sao]
```

## Quy tắc bắt buộc

- ✅ Hook phải ngắn: 1-2 câu, đọc trong 3-5 giây
- ✅ Hook phải tạo "loop" — khiến viewer muốn xem tiếp để biết "vậy thì sao?"
- ✅ Dùng ngôn ngữ bình dân, gần gũi — tránh từ ngữ hàn lâm
- ✅ Tự nhiên khi nói to — test bằng cách đọc thành tiếng
- ❌ Không dùng clickbait vô giá trị
- ❌ Không hứa hẹn phi thực tế
- ❌ Không quá dài (> 15 từ là đã dài)
```

---

### SKILL 2: TikTok Script Writer

**File:** `scripts/tiktok-script-writer/SKILL.md`

```markdown
---
name: tiktok-script-writer
description: Viết kịch bản video TikTok hoàn chỉnh để bán sách và ebook.
  Dùng khi user muốn script, kịch bản, nội dung video, lời thoại cho TikTok.
  Trigger: script, kịch bản, viết video, lời thoại, nội dung video, TikTok content.
  Hỗ trợ format: book review, lesson extract, story format, transformation.
---

# TikTok Script Writer cho Bán Sách

## Thông tin cần từ user

1. **Tên sách** và **tác giả** (nếu có)
2. **Độ dài video:** 30s / 60s / 90s / 3 phút?
3. **Format:** Review / Lesson / Story / Transformation / Tutorial?
4. **Hook đã có chưa?** (nếu có, paste vào để script dùng hook đó)
5. **Mục tiêu video:** Bán sách trực tiếp / Build audience / Tăng trust?

## Format Scripts

### Format A: Book Lesson Extract (phổ biến nhất)
Tốt cho: Sách non-fiction, self-help, business
Structure:
```
[0-3s]  HOOK → Câu hỏi hoặc statement mạnh
[3-10s] SET UP → "Cuốn sách [X] của [tác giả] dạy rằng..."
[10-40s] LESSON → 1-3 ý chính, ngắn gọn, có ví dụ
[40-50s] APPLY → "Bạn có thể áp dụng ngay bằng cách..."
[50-60s] CTA → "Link trong bio" hoặc "Comment [keyword] để..."
```

### Format B: Story Format
Tốt cho: Memoir, biography, narrative non-fiction
Structure:
```
[0-3s]  HOOK → Moment kịch tính nhất
[3-15s] CONTEXT → Tình huống là gì
[15-45s] CONFLICT & JOURNEY → Thử thách và cách vượt qua
[45-55s] LESSON → Điều bạn rút ra được
[55-60s] CTA → Link + "Đọc câu chuyện đầy đủ trong..."
```

### Format C: Transformation Format
Tốt cho: Self-improvement, weight loss, finance
Structure:
```
[0-3s]  HOOK → Kết quả ấn tượng
[3-10s] BEFORE → Trạng thái ban đầu (relatable với audience)
[10-40s] METHOD → 3 bước/nguyên tắc chính từ sách
[40-50s] PROOF → Kết quả ngắn gọn (số liệu nếu có)
[50-60s] CTA → Offer + tạo urgency
```

## Quy trình viết Script

1. Chọn format phù hợp với loại sách và mục tiêu
2. Viết draft theo structure đã chọn
3. Đánh time stamp cho từng phần [0s-3s], [3s-10s]...
4. Thêm **stage directions** trong ngoặc đơn: (nhìn vào camera), (cầm sách)
5. Highlight **key phrases** cần nhấn mạnh bằng **bold**

## Output Format

```
## Script TikTok — [Tên Sách] | Format: [A/B/C] | Độ dài: [Xs]

---

[0-3s] **HOOK**
"[Text hook — đọc to, tự nhiên]"
*(Nhìn thẳng vào camera, confident)*

[3-Xs] **[Tên phần]**
"[Script text]"
*([Stage direction nếu cần])*

...

[Xs-Xs] **CTA**
"[Link trong bio — hoặc comment keyword]"
*(Giơ sách lên nếu physical book)*

---

💬 **Caption gợi ý:** [1-2 dòng caption ngắn]
#️⃣ **Hashtags:** #hashtag1 #hashtag2 (xem skill caption để có bộ đầy đủ)
🎵 **Gợi ý sound:** [Loại nhạc/sound phù hợp mood của script]
```

## Quy tắc bắt buộc

- ✅ Viết như NÓI, không phải như ĐỌC
- ✅ Câu ngắn, nhịp nhanh — tối đa 10-12 từ/câu
- ✅ Mỗi câu phải "earn" câu tiếp theo (viewer muốn nghe tiếp)
- ✅ Tên sách phải xuất hiện ít nhất 2 lần (để nhớ)
- ✅ CTA phải cụ thể: "Link trong bio" hoặc "Comment 'sách' để nhận..."
- ❌ Không dùng từ ngữ phức tạp, academic
- ❌ Không nhồi nhét quá nhiều ý (tối đa 3 lesson/video)
- ❌ Không quảng cáo lộ liễu quá sớm (phải give value trước)
```

---

### SKILL 3: TikTok Caption + Hashtag

**File:** `captions/tiktok-caption-sell/SKILL.md`

```markdown
---
name: tiktok-caption-sell
description: Viết caption TikTok và bộ hashtag tối ưu để bán sách, ebook online.
  Dùng khi user cần caption cho video TikTok, mô tả video, hashtag, SEO TikTok.
  Trigger: caption, hashtag, mô tả, description video, TikTok SEO, bộ tag.
---

# TikTok Caption & Hashtag Optimizer

## Cách TikTok Caption hoạt động

TikTok caption (≤ 2,200 ký tự) có 3 chức năng:
1. **Hook thứ 2** — dòng đầu tiên hiện trước "see more"
2. **SEO TikTok** — keywords giúp video được recommend
3. **CTA** — chuyển đổi viewer thành buyer

## Thông tin cần từ user

1. **Tên sách / chủ đề video**
2. **Mục tiêu:** Bán sách / Tăng follow / Tăng engagement?
3. **Audience:** Target chính là ai?
4. **Có CTA đặc biệt không?** (VD: discount code, link cụ thể...)

## Cấu trúc Caption Chuẩn

```
[Dòng 1 — HOOK TEXT: 1 câu mạnh, repeat hook video hoặc strong claim]

[Dòng 2-4 — VALUE: 2-3 bullet points về lợi ích đọc sách]
• Điều 1 bạn sẽ học được
• Điều 2 sẽ thay đổi
• Điều 3 áp dụng ngay

[Dòng 5-6 — CTA: Rõ ràng và cụ thể]
👉 [Action cụ thể]

[Dòng 7 — HASHTAGS: 5-15 hashtag]
```

## Bộ Hashtag theo từng nhóm sách

### 📚 Sách Self-Help / Phát Triển Bản Thân
```
#sachviet #docSach #phatTrienBanThan #selfhelp #mindset 
#thuatNguTri #sucManhTuDuy #thayDoiCuocDoi #bookstok
#bookstagram #bookrecommendation #sachHay #reviewSach
```

### 💰 Sách Tài Chính / Làm Giàu
```
#taichinhcanhan #lamgiau #dautuchoban #selfmade
#financetok #moneymindset #richhabits #sachTaiChinh
#dauTuThongMinh #tuDoTaichinhh #sachKinhDoanh
```

### 🧠 Sách Tâm Lý / Hành Vi
```
#tamly #tamlyHoc #human behavior #sachTamly
#psychologytok #mindfulNess #selfawareness
#sunghocTamly #giaidieuTamLy
```

### ❤️ Sách Mối Quan Hệ / Tình Cảm
```
#moiQuanHe #tinhYeu #hon nhan #giadinh
#relationshiptok #lovelanguage #sachMoiQuanHe
#soiThanToi #cuocSongHonNhan
```

### Hashtag Universal (dùng cho mọi loại sách)
```
#sachviet #docSach #booktok #bookstok #review
#sachHay #docSachMoiNgay #booklovers #reading
```

## Output Format

```
## Caption cho video: [Tên sách/Chủ đề]

---

[Dòng 1 — Hook]
[Hooks phải xuất hiện trên màn hình trước "see more"]

[Phần body — value]
• [Bullet 1]
• [Bullet 2]  
• [Bullet 3]

[CTA]
👉 [Action]

[Hashtags — nhóm phù hợp]

---

**🔢 Ký tự:** [Số ký tự / 2200 limit]
**💡 Tips:** [1 gợi ý tối ưu thêm nếu có]
```

## Quy tắc bắt buộc

- ✅ Dòng đầu tiên PHẢI là hook — không bắt đầu bằng tên sách
- ✅ Hashtag: 5-15 tags, mix giữa broad và niche
- ✅ CTA phải rõ ràng: "Link bio", "Comment X nhận Y", "Save để đọc lại"
- ✅ Dùng emoji có chọn lọc để break text, không lạm dụng
- ✅ Mobile-first: ngắn dòng, dễ đọc trên điện thoại
- ❌ Không dùng quá 20 hashtag (TikTok penalize)
- ❌ Không bắt đầu bằng hashtag
- ❌ Không viết thành đoạn văn dài — phải ngắn, scannable
```

---

### SKILL 4: Book Blurb Writer

**File:** `sales/book-blurb-writer/SKILL.md`

```markdown
---
name: book-blurb-writer
description: Viết mô tả sách, blurb, tóm tắt cuốn sách để quảng bá và bán hàng.
  Dùng khi user cần mô tả sách, giới thiệu sách, tóm tắt, blurb, back cover copy,
  product description cho shop online, bio link description.
  Trigger: mô tả sách, giới thiệu sách, blurb, tóm tắt, description.
---

# Book Blurb Writer

## 3 loại Blurb và khi nào dùng

| Loại | Độ dài | Dùng ở đâu |
|------|--------|------------|
| **Short Blurb** | 30-50 từ | Bio TikTok, Instagram, Linktree |
| **Medium Blurb** | 100-150 từ | Caption video, Email subject |
| **Long Blurb** | 200-300 từ | Shopee, landing page, back cover |

## Thông tin cần từ user

1. **Tên sách** và **tác giả**
2. **Thể loại:** Non-fiction / Fiction / Business / Self-help / Memoir...
3. **Target reader:** Hồ sơ người đọc lý tưởng?
4. **Core promise:** Sau khi đọc, reader sẽ có được gì?
5. **Hook fact/stat:** Có con số, sự thật nào compelling không?
6. **Loại blurb cần:** Short / Medium / Long / Tất cả?

## Framework viết Blurb

### Formula AIDA (cho Sales-focused Blurb)
```
A - Attention: Mở đầu bằng pain point hoặc curiosity hook
I - Interest: Reveal vấn đề sâu hơn, build tension  
D - Desire: Giải pháp/promise của cuốn sách
A - Action: CTA mua sách
```

### Formula PAS (cho Empathy-focused Blurb)
```
P - Problem: Nêu vấn đề reader đang đối mặt
A - Agitate: Khuếch đại vấn đề — cho thấy hậu quả nếu không giải quyết
S - Solution: Cuốn sách là giải pháp như thế nào
```

## Output Format

```
## Blurbs cho: [Tên Sách]

---

### 🔹 SHORT BLURB (cho Bio/Linktree)
[30-50 từ — punch, memorable, có hook]

---

### 🔸 MEDIUM BLURB (cho Caption/Email)  
[100-150 từ — AIDA hoặc PAS structure]

---

### 🔶 LONG BLURB (cho Shopee/Landing page)
[200-300 từ — đầy đủ, persuasive, SEO-friendly]

---

**Keywords tối ưu SEO:** [5-10 keywords nên có trong description]
**💡 Variation gợi ý:** [1 approach khác có thể thử]
```

## Quy tắc

- ✅ Luôn bắt đầu bằng điều quan trọng nhất với reader — không phải tác giả
- ✅ Dùng "bạn" để nói chuyện trực tiếp với reader
- ✅ Specific beats generic: "Tăng thu nhập 40%" > "Cải thiện tài chính"
- ✅ Short sentences, power words: "Khám phá", "Bí quyết", "Đột phá"
- ❌ Không bắt đầu bằng "Cuốn sách này..."
- ❌ Không dùng từ ngữ quá academic hoặc hoa mỹ
- ❌ Không liệt kê chương — reader quan tâm đến lợi ích, không phải mục lục
```

---

### SKILL 5: CTA Generator

**File:** `sales/cta-generator/SKILL.md`

```markdown
---
name: cta-generator
description: Tạo Call-to-Action (CTA) thuyết phục để bán sách, tăng click link bio,
  tăng comment, và convert viewer thành buyer trên TikTok.
  Trigger: CTA, kêu gọi hành động, call to action, mua sách, click link, 
  tăng comment, convert, bán hàng.
---

# CTA Generator cho TikTok Bán Sách

## 5 loại CTA theo mục tiêu

### 1. Direct Purchase CTA
**Mục tiêu:** Bán ngay lập tức
**Dùng khi:** Video review tích cực, khán giả đã warm

Patterns:
- "Link sách trong bio — [tên sách] đang có giá [giá] hôm nay thôi"
- "Comment 'sách' mình gửi link ngay"
- "Tap vào bio → thấy link ngay → book chờ bạn rồi 📚"

### 2. Engagement CTA
**Mục tiêu:** Tăng reach thông qua comment
**Dùng khi:** Muốn video được đẩy thêm, build community

Patterns:
- "Save video này nếu bạn thích đọc sách 📌"
- "Comment 'đúng' nếu bạn đã từng cảm thấy như vậy"
- "Tag người bạn muốn cùng đọc sách này 👥"

### 3. Lead Magnet CTA
**Mục tiêu:** Collect leads, build email list
**Dùng khi:** Có freemium offer (chapter miễn phí, summary PDF...)

Patterns:
- "Comment 'FREE' để nhận chapter đầu miễn phí"
- "Follow + comment 'tóm tắt' để nhận PDF summary"
- "DM mình 'summary' để nhận file tóm tắt sách miễn phí"

### 4. Curiosity Loop CTA
**Mục tiêu:** Giữ viewer xem video tiếp theo
**Dùng khi:** Đang build series về một cuốn sách

Patterns:
- "Phần 2 ra thứ 4 — follow để không bỏ lỡ"
- "Video tiếp theo mình sẽ chia sẻ lesson số 2 — lesson khó tin nhất"
- "Follow để xem mình áp dụng điều này vào [kết quả cụ thể] trong 30 ngày"

### 5. Social Proof CTA
**Mục tiêu:** Build trust thông qua community
**Dùng khi:** Đã có audience, muốn tạo FOMO

Patterns:
- "[X] người đã đặt mua tuần này — link bio kẻo hết"
- "Đây là cuốn sách [X] người trong community mình đang đọc cùng"

## Quy trình tạo CTA

1. Xác định **mục tiêu chính** của video
2. Xác định **giai đoạn trong funnel**: Cold / Warm / Hot audience?
3. Tạo **3-5 CTA variations** cho mục tiêu đó
4. Đề xuất CTA tốt nhất với lý do cụ thể

## Output Format

```
## CTAs cho video: [Tên sách / Chủ đề]
**Mục tiêu:** [Direct sale / Engagement / Lead / etc.]
**Audience stage:** [Cold / Warm / Hot]

---

### 🔥 Recommended CTA
[CTA tốt nhất]
*(Lý do: [ngắn gọn])*

### Variations

**Option B:** [CTA khác]
**Option C:** [CTA khác]  
**Option D:** [CTA khác]
**Option E:** [CTA khác]

---

**📍 Đặt CTA ở đâu:**
- Trong video: [Giây thứ X]
- Trong caption: [Dòng đầu / dòng cuối]
- Trong comment ghim: [Nếu có]
```

## Quy tắc

- ✅ CTA phải cụ thể: "Link BIO" không phải "tại đây"
- ✅ 1 video = 1 CTA chính (không confuse viewer)
- ✅ Tạo urgency thật: Deadline thật / Stock thật / Offer thật
- ✅ Friction thấp: Ít bước nhất có thể để mua
- ❌ Không dùng "check link in bio" — quá generic, không có urgency
- ❌ Không nhét 3+ CTAs vào 1 video
- ❌ Không nói "mua ngay" nếu audience còn cold
```

---

## 5. Tích hợp vào n8n Flow

### Bước 1: Tạo GitHub repo

```
1. Vào github.com → New repository
2. Tên: tiktok-book-skills (hoặc tên khác)
3. Public repository (để n8n đọc không cần auth)
4. Tạo các folder + SKILL.md theo cấu trúc trên
5. Push lên GitHub
```

### Bước 2: Thêm repo vào flow

Trong node `Set GitHub Repo URLs`, thêm URL repo của bạn:

```javascript
// Trong node "Set GitHub Repo URLs" của flow Skill_n8n.n8n
[
  "https://github.com/anthropics/skills",
  "https://github.com/TÊN_BẠN/tiktok-book-skills"
]
```

### Bước 3: Test với các câu lệnh thực tế

Mở n8n Chat và thử:

```
✅ "Tạo 5 hook cho sách 'Đắc Nhân Tâm' target audience là người đi làm 25-35 tuổi"

✅ "Viết script TikTok 60 giây format Lesson Extract cho sách 'Nhà Giả Kim'"

✅ "Viết caption + hashtag cho video review sách về tài chính cá nhân"

✅ "Tạo blurb ngắn, medium, và dài cho ebook về productivity"

✅ "Tạo CTA để tăng comment cho video sách self-help"
```

### Bước 4: Customize System Prompt

Thêm context về bạn vào System Prompt của AI Agent (phần không phải Skills):

```
You are a content assistant for [TÊN_BẠN], a Vietnamese content creator 
who sells books and ebooks on TikTok.

BRAND VOICE: [Mô tả tone của bạn — VD: Thân thiện, trẻ trung, honest]
TARGET AUDIENCE: [Mô tả audience — VD: Gen Z và Millennials Việt Nam]
SPECIALTY: [Loại sách bạn hay bán — VD: Self-help, Business, Psychology]

Your skills are stored in GitHub repos. Always fetch and follow skill files.
```

---

## 6. Prompt Engineering trong SKILL.md

### Kỹ thuật viết Instructions hiệu quả

#### 1. Specific beats general

```markdown
# ❌ Quá chung chung
Viết hook hấp dẫn cho video.

# ✅ Cụ thể và có structure
Viết hook cho 3 giây đầu video (tối đa 15 từ). Hook phải:
- Tạo "open loop" — câu hỏi hoặc tension chưa được giải quyết
- Dùng ngôn ngữ bình dân, như người bạn nói chuyện
- Test được bằng cách đọc to — tự nhiên và conversational
```

#### 2. Cho ví dụ tốt VÀ xấu

```markdown
## Ví dụ Hook

✅ TỐT: "Tôi đã đọc 100 cuốn sách năm ngoái và đây là điều duy nhất thật sự thay đổi cuộc sống tôi"
→ Tốt vì: Credibility (100 cuốn), curiosity loop (điều duy nhất là gì?)

❌ XẤU: "Hôm nay mình sẽ review cuốn sách rất hay"
→ Xấu vì: Không có hook, không tạo reason to watch on
```

#### 3. Dùng format và output template rõ ràng

AI follow format tốt hơn khi có template cụ thể trong SKILL.md.

#### 4. Negative constraints quan trọng như positive

```markdown
## Quy tắc

# Nói AI làm gì ✅
- Hook phải ≤ 15 từ
- Phải có open loop

# Nói AI KHÔNG làm gì ❌ (cũng quan trọng)
- Không bắt đầu bằng tên sách
- Không dùng từ "amazing", "tuyệt vời" (quá generic)
- Không hứa hẹn phi thực tế
```

#### 5. Escalating specificity

```markdown
# Level 1 - Chung: Viết hook hấp dẫn
# Level 2 - Cụ thể hơn: Viết hook 15 từ tạo curiosity
# Level 3 - Chi tiết nhất: Viết hook 15 từ, dùng format [số] + [claim phản trực giác] + [hint về giải pháp], ngôn ngữ bình dân, đọc được tự nhiên
```

---

## 7. Workflow thực tế: Từ ý tưởng đến 30 video/tuần

### Hệ thống content pipeline với Skills

```
MONDAY — Planning Session (30 phút)
  Chat với n8n Agent:
  "Tôi muốn làm series 5 video về sách [tên]. Tạo plan nội dung"
  → Skill: series-planner → Output: 5 video ideas + angles

TUESDAY-FRIDAY — Production (15 phút/video)
  Với mỗi video:
  
  Step 1: Generate Hook (5 phút)
  "Tạo 5 hook cho video về [lesson X] từ sách [Y]"
  → Skill: hook-generator → Chọn 1 hook

  Step 2: Generate Script (5 phút)
  "Dùng hook này, viết script 60s format Lesson Extract"
  → Skill: tiktok-script-writer → Script hoàn chỉnh

  Step 3: Generate Caption (5 phút)
  "Tạo caption + hashtag cho video về [chủ đề] target [audience]"
  → Skill: tiktok-caption-sell → Caption ready-to-use

  → Quay video theo script, paste caption → DONE

SATURDAY — Review & Optimize (1 tiếng)
  - Review analytics tuần qua
  - Identify video nào perform tốt
  - Update SKILL.md dựa trên learnings
  - "Video hook type [X] perform 2x hơn — cập nhật skill"
```

### Kế hoạch 30 ngày đầu

| Tuần | Mục tiêu | Actions |
|------|----------|---------|
| **Tuần 1** | Setup cơ bản | Tạo 5 skills cốt lõi, test flow |
| **Tuần 2** | Optimize | Dựa trên analytics, refine skills |
| **Tuần 3** | Scale | Thêm skills mới, tăng frequency |
| **Tuần 4** | Expand | Skills cho email, landing page |

---

## 8. Mở rộng hệ thống

### Sau khi hệ thống cơ bản hoạt động tốt

#### 1. Thêm Google Sheets để track performance

```
n8n flow mở rộng:
[Chat] → [Skills Agent] → [Tạo content] → [Lưu vào Google Sheets]
                                              ↓
                                    [Timestamp, content type, status]
```

#### 2. Dùng Skills cùng với File Nodes để batch process

```
[Google Sheets: Danh sách 10 sách] 
→ [Loop]
→ [Skills Agent: Tạo hook + script + caption cho từng sách]
→ [Google Docs: Lưu content calendar tuần]
```

#### 3. Tạo "Meta Skill" — Skill điều phối các Skills khác

**File:** `meta/full-content-pack/SKILL.md`

```markdown
---
name: full-content-pack
description: Tạo bộ content hoàn chỉnh cho một cuốn sách bao gồm hooks, 
  scripts, captions, blurbs và CTAs. Dùng khi user muốn toàn bộ content 
  cho 1 sách hoặc cần content pack đầy đủ.
  Trigger: content pack, toàn bộ content, full pack, content cho sách.
---

# Full Content Pack Generator

## Quy trình

Khi user yêu cầu full content pack cho 1 cuốn sách:

1. Thu thập thông tin sách (tên, thể loại, audience, core promise)

2. Tạo tuần tự bằng cách gọi các skills:
   a. Dùng `hook-generator` → tạo 5 hooks
   b. Dùng `tiktok-script-writer` → tạo 3 scripts (30s, 60s, 90s)
   c. Dùng `tiktok-caption-sell` → tạo captions cho mỗi script  
   d. Dùng `book-blurb-writer` → tạo 3 blurbs (short/medium/long)
   e. Dùng `cta-generator` → tạo 5 CTAs variations

3. Compile tất cả vào 1 Content Pack document

## Output Format

```
# Content Pack: [Tên Sách]
Generated: [Date]

## 📌 HOOKS (5 options)
[Kết quả từ hook-generator]

## 🎬 SCRIPTS
### 30-second version
[Script]
### 60-second version  
[Script]
### 90-second version
[Script]

## 📝 CAPTIONS
[Captions matching each script]

## 📚 BLURBS
[Short / Medium / Long]

## 📣 CTAs
[5 variations]
```
```

---

## Tóm tắt — Action Items ngay hôm nay

### ✅ Checklist bắt đầu

- [ ] Tạo GitHub repo tên `tiktok-book-skills`
- [ ] Tạo folder `hooks/hook-generator/` và paste SKILL.md từ tài liệu này
- [ ] Tạo folder `scripts/tiktok-script-writer/` + SKILL.md
- [ ] Tạo folder `captions/tiktok-caption-sell/` + SKILL.md
- [ ] Mở n8n, vào node `Set GitHub Repo URLs`, thêm URL repo của bạn
- [ ] Test với câu: *"Tạo 5 hook cho sách Đắc Nhân Tâm target dân văn phòng"*
- [ ] Xem kết quả → Điều chỉnh description trong SKILL.md nếu cần

### 🎯 KPI sau 30 ngày

- Content creation time: từ 3-4 tiếng → dưới 1 tiếng/ngày
- Video per week: từ 5-7 → 20-30 videos
- Consistency: 100% follow brand voice và format

---

*Tài liệu này được tạo dựa trên phân tích flow `Skill_n8n.n8n`, repo `anthropics/skills`, và được tùy biến cho use case content creator bán sách TikTok — tháng 4/2026*
