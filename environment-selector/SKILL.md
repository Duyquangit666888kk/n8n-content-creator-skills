---
name: vietnam-environment-selector
description: Classify Vietnam travel/visual setting from free text and return environment
  key plus canonical English setting_description for image or video prompts. Use when
  input text describes Vietnamese locations, scenes, tourism, or geography and you need
  environment + setting_description fields (replacing a Code node classifier). Triggers:
  environment selector, setting_description, Vietnam scene, địa danh Việt Nam, bối cảnh
  video, vịnh Hạ Long, Sapa, Đà Lạt, Miền Tây, Hà Nội, Sài Gòn, Huế, Hội An, Phú Quốc,
  n8n environment, detect environment from text.
---

# Vietnam Environment Selector (from input text)

## Purpose

Given **one input text blob** (Vietnamese or English, mixed case), determine:

1. **`environment`** — one canonical enum key (see list below).
2. **`setting_description`** — the **exact** English phrase for that key (copy verbatim from the catalog; do not paraphrase).

This skill mirrors the logic of a deterministic **first-match-wins** classifier: evaluate categories **only in the priority order** below. Stop at the first category whose keyword list matches the text (case-insensitive substring). If none match, use **`RURAL_VIETNAMESE_VILLAGE`**.

## How to build the text blob (n8n / JSON)

- If the payload has `scenes` as an array: concatenate `String(scene.scene_content ?? '')` for each scene, separated by spaces.
- Otherwise: use a JSON stringification of the object, or any user-provided description string.

Treat the result as **one haystack string** for matching.

## Matching rules

- **Case-insensitive** substring: a keyword matches if it appears anywhere inside the haystack (after lowercasing both).
- **First match wins** by **category order** below — not by “strongest” geography. If two categories could apply, the **earlier** category in the list wins.
- Do **not** invent new `environment` values or rewrite `setting_description`.

## Priority order (strict)

Evaluate in this order; stop at the first hit:

1. `HA_LONG_BAY`
2. `NINH_BINH_TRANG_AN`
3. `PHONG_NHA_KE_BANG`
4. `HANOI_CAPITAL`
5. `SOUTH_METRO_HCMC`
6. `HERITAGE_HUE_HOI_AN`
7. `TROPICAL_ISLANDS_SOUTH`
8. `NORTHERN_HIGHLANDS`
9. `CENTRAL_HIGHLANDS`
10. `MEKONG_DELTA`
11. `CENTRAL_VIETNAM_COAST`
12. If no keyword matched → `RURAL_VIETNAMESE_VILLAGE`

## Canonical `setting_description` catalog (verbatim)

Use **exactly** these strings for `setting_description`:

| environment | setting_description |
|-------------|---------------------|
| `HA_LONG_BAY` | emerald green bay water, thousands of UNESCO-listed limestone karst towers and islets, dramatic sea stacks, traditional wooden junk boats and sampans, vast illuminated sea caves, soft mist — Ha Long Bay (Quảng Ninh) |
| `NINH_BINH_TRANG_AN` | serene river winding through golden rice beneath steep limestone peaks, sampan through water grottoes, goat paths on cliffs, spiritual pagoda complexes — Ninh Bình Tràng An / Tam Cốc (“Hạ Long trên cạn”) |
| `PHONG_NHA_KE_BANG` | dense jungle karst, colossal cave portals, underground rivers and cathedral chambers, tropical forest trails — Phong Nha–Kẻ Bàng / Sơn Đoòng (Quảng Bình) |
| `NORTHERN_HIGHLANDS` | mist-wrapped terraced rice paddies, Fansipan and Hoàng Liên peaks, Hmong and Dao hill villages, Ha Giang stone plateau and Mã Pí Lèng pass hairpin road above deep gorge — Sapa (Lào Cai) & Hà Giang north mountains |
| `HANOI_CAPITAL` | historic lake with red bridge and temple, 36 ancient streets tube houses, colonial balconies, incense at literature temple, vibrant street food — Hà Nội capital heritage (phố cổ, Hồ Gươm, Văn Miếu) |
| `HERITAGE_HUE_HOI_AN` | imperial citadel and Perfume River bridges, royal tombs and pine hills, golden merchant houses glowing with silk lanterns at dusk, Japanese covered bridge — Huế & Hội An UNESCO towns (Quảng Nam / Thừa Thiên Huế) |
| `TROPICAL_ISLANDS_SOUTH` | turquoise shallows, white-sand bays, coconut rows, coral reefs and snorkeling, quiet luxury resorts — Phú Quốc “đảo ngọc” or Côn Đảo pristine islands |
| `CENTRAL_VIETNAM_COAST` | long sandy beaches, open sea, modern seaside city skyline, cable car above cloud hills, golden bridge held by giant hands, diving and island day trips — Đà Nẵng, Nha Trang, and similar central-south coast |
| `SOUTH_METRO_HCMC` | dense tropical metropolis, basilica stone façade, Independence Palace lawns, motorbike rivers, rooftop cafes and neon — TP. Hồ Chí Minh / Sài Gòn |
| `MEKONG_DELTA` | flat green paddies, brown canals and floating markets, sampans, river fruit gardens, humid delta light — Miền Tây / Cần Thơ / Cửu Long waterways (not island beach resort) |
| `CENTRAL_HIGHLANDS` | cool pine air, valley of flowers, French hill villas, morning mist over lakes and coffee hills — Đà Lạt & Tây Nguyên high plateau (distinct from north Sapa/Hà Giang) |
| `RURAL_VIETNAMESE_VILLAGE` | quiet village lanes, bamboo hedges, brick or wooden houses, scattered fruit trees — generic rural Vietnam when no stronger place matches |

## Keyword lists (substring match, case-insensitive)

If **any** keyword under a category appears in the haystack, that category matches (subject to priority order above).

### HA_LONG_BAY

vịnh hạ long, ha long, hạ long, halong, halong bay, quảng ninh, quang ninh, tuần châu, tuan chau, vịnh lan hạ, lan hạ, lan ha, thiên cung, thien cung, hang sửng sốt, hang dau go, động thiên cung, đảo đá vôi, dao da voi, núi đá vôi, limestone tower, sea stack, cát bà, cat ba, bãi cháy, bai chay, yên tử, yen tu, junk boat, thuyền buồm, tàu du lịch vịnh, kayak, chèo kayak, cheo kayak, vịnh bắc bộ, vinh bac bo, di sản thiên nhiên, di sản thế giới, unesco vịnh, di sản vịnh, ngàn đảo, nghìn đảo, đảo titop, titop, soi sim

### NINH_BINH_TRANG_AN

ninh bình, ninh binh, tràng an, trang an, tam cốc, tam coc, bích động, bich dong, bái đính, bai dinh, hang mua, núi múa, nui mua, hạ long trên cạn, ha long tren can, van long, vân long, cố đô hoa lư, hoa lu, hoa lư

### PHONG_NHA_KE_BANG

phong nha, phong nha kẻ bàng, phong nha ke bang, kẻ bàng, ke bang, sơn đoòng, son doong, son đoong, hang en, hang va, tu lan, quảng bình, quang binh, động thiên đường, dong thien duong, paradise cave, dark cave, phong nha cave

### HANOI_CAPITAL

hà nội, ha noi, hanoi, thủ đô, thu do, phố cổ hà nội, pho co ha noi, 36 phố phường, hoàn kiếm, hoan kiem, hồ gươm, ho guom, hồ hoàn kiếm, văn miếu, van mieu, quốc tử giám, quoc tu giam, ông quan chưởng, long biên, long bien, nhà thờ lớn hà nội, ho tay, hồ tây, bát tràng, bat trang, imperial city hanoi, literature temple

### SOUTH_METRO_HCMC

hồ chí minh, ho chi minh, tp.hcm, tp hcm, tphcm, sài gòn, sai gon, saigon, chợ bến thành, cho ben thanh, ben thanh, phố đi bộ nguyễn huệ, nguyen hue, dinh độc lập, dinh doc lap, independence palace, reunification palace, nhà thờ đức bà, nha tho duc ba, notre dame saigon, bưu điện trung tâm, buu dien, landmark 81, bitexco

### HERITAGE_HUE_HOI_AN

huế, thừa thiên huế, thua thien hue, kinh thành huế, kinh thanh hue, đại nội, dai noi, tử cấm thành, tu cam thanh, lăng tẩm, lang tam, lăng khải định, lang khai dinh, sông hương, song huong, perfume river, hội an, hoi an, quảng nam, quang nam, đèn lồng, den long, chùa cầu, chua cau, japanese covered bridge, phố cổ hội an, pho co hoi an, unesco hội an, hoi an ancient, mỹ sơn, thánh địa mỹ sơn, my son sanctuary, sanctuary of my son

### TROPICAL_ISLANDS_SOUTH

phú quốc, phu quoc, đảo phú quốc, dao phu quoc, đảo ngọc, dao ngoc, bãi sao, bai sao, vinpearl phú quốc, sun world hon thom, hon thom, côn đảo, con dao, côn sơn, con son, bà rịa, ba ria, vũng tàu, vung tau, đảo hòn tre, hon tre nha trang

### NORTHERN_HIGHLANDS

sapa, sa pa, lào cai, lao cai, fansipan, phansipang, ruộng bậc thang, ruong bac thang, thửa ruộng bậc thang, hà giang, ha giang, đồng văn, dong van, mã pí lèng, ma pi leng, đèo mã pí lèng, deo ma pi leng, cao nguyên đá, cao nguyen da, cột cờ lũng cú, lung cu, y tý, y ty, bắc hà, bac ha, hoàng su phì, hoang su phi, dân tộc thiểu số, hmông, hmong, dao đỏ, tây bắc, tay bac, nóc nhà đông dương

### CENTRAL_HIGHLANDS

đà lạt, da lat, dalat, thành phố ngàn hoa, thanh pho ngan hoa, đồi thông, doi thong, buôn ma thuột, buon ma thuot, ban me thuot, pleiku, gia lai, kon tum, đắk lắk, dak lak, daklak, đắk nông, dak nong, lâm đồng, lam dong, tây nguyên, tay nguyen, central highlands, rừng thông, rung thong, đồi chè, doi che, coffee plantation, rubber plantation, núi rừng, nui rung, thác nước, thac nuoc, waterfall, hill tribe, nhà sàn, nha san, stilt house highland, misty hill, pine forest, jungle mountain, khí hậu mát, khi hau mat

### MEKONG_DELTA

miền tây, mien tay, miền tây nam bộ, đồng bằng sông cửu long, song cuu long, sông cửu long, cửu long, cuu long, mekong delta, mekong, sông mekong, song mekong, mekong river, đbscl, chợ nổi, cho noi, floating market, cái răng, cai rang, chợ nổi cái răng, phong dien, phong điền, ngã bảy, nga bay, soc trang, sóc trăng, kênh rạch, kenh rach, rạch giá, rach gia, long xuyên, long xuyen, mương nước, cần thơ, can tho, an giang, đồng tháp, dong thap, hậu giang, hau giang, bạc liêu, bac lieu, cà mau, ca mau, tiền giang, tien giang, bến tre, ben tre, trà vinh, tra vinh, vĩnh long, vinh long, sông tiền, song tien, sông hậu, song hau, nhánh sông, ruộng lúa, ruong lua, cánh đồng lúa, canh dong lua, lúa chín, lua chin, paddy field, rice paddy, two-crop rice, đò ngang, do ngang, xuồng ba lá, xuong ba la, xuồng máy, xuong may, vườn cây ăn trái, vuon cay an trai, đất phù sa, dat phu sa, miệt vườn, miet vuon, coconut, dừa nước, dua nuoc, kiên giang, kien giang

### CENTRAL_VIETNAM_COAST

đà nẵng, da nang, danang, bà nà, ba na, bà nà hills, cầu vàng, cau vang, golden bridge, mỹ khê, my khe, sơn trà, son tra, ngũ hành sơn, ngu hanh son, biển đông, bien dong, nha trang, vinpearl nha trang, đảo khánh hòa, khanh hoa, vinh hy, vịnh hý, đảo yến, dao yen, chàm islands, cu lao cham, cù lao chàm, quy nhơn, quy nhon, eo gió, eo gio, kỳ co, ky co, phú yên, phu yen, mũi điện, mui dien, mũi né, mui ne, lagi, phan thiết, phan thiet, bình thuận, binh thuan, bãi biển, bai bien, bờ biển, bo bien, ven biển, ven bien, sóng biển, song bien, cát trắng, cat trang, cát vàng, cat vang, coastline, seaside, snorkel, scuba, surfing, lặn biển, lan bien, resort biển, resort bien, fishing village coast, bãi tắm, bai tam

### RURAL_VIETNAMESE_VILLAGE

No keywords — this is the **fallback** when none of the above categories match.

## Procedure

1. Normalize mentally: treat the input as lowercase for matching; preserve original input only if the user asked for it elsewhere.
2. Walk the priority list from top to bottom; for each category, check whether **any** keyword from that category’s list appears as a substring of the haystack.
3. On first match, set `environment` to that category’s key and set `setting_description` from the catalog table **exactly**.
4. If no match after `CENTRAL_VIETNAM_COAST`, set `environment` to `RURAL_VIETNAMESE_VILLAGE` and use its catalog string.

## Output format

Return **only** these two fields (plus optional echo of input for traceability if the pipeline needs it):

```json
{
  "environment": "HA_LONG_BAY",
  "setting_description": "emerald green bay water, thousands of UNESCO-listed limestone karst towers and islets, dramatic sea stacks, traditional wooden junk boats and sampans, vast illuminated sea caves, soft mist — Ha Long Bay (Quảng Ninh)"
}
```

Optional for debugging in n8n:

```json
{
  "environment": "...",
  "setting_description": "...",
  "matched_keyword": "halong bay",
  "text_blob_preview": "first 200 chars..."
}
```

## Rules

- Never output a `setting_description` that is not in the catalog table.
- Never skip the priority order to prefer a “more specific” region that appears later in the list.
- If the text is empty or non-textual noise, default to `RURAL_VIETNAMESE_VILLAGE`.
- For ambiguous travel copy (“beautiful Vietnam”), default to `RURAL_VIETNAMESE_VILLAGE` unless a keyword clearly triggers a higher-priority category.

## Example

**Input text blob:** `Kịch bản quay tại vịnh Hạ Long, kayak và hang Sửng Sốt`

**Output:**

```json
{
  "environment": "HA_LONG_BAY",
  "setting_description": "emerald green bay water, thousands of UNESCO-listed limestone karst towers and islets, dramatic sea stacks, traditional wooden junk boats and sampans, vast illuminated sea caves, soft mist — Ha Long Bay (Quảng Ninh)"
}
```

## n8n integration

- **Agent node:** Load this skill from your skills repo; user message = the text blob (or instructions + pasted JSON).
- **Structured output parser:** Map model output to `environment` and `setting_description` fields on your item’s `json`.
- **vs Code node:** The Code node is deterministic and token-cheap; this skill is for LLM-based flows where you want the same taxonomy without maintaining JS in n8n.
