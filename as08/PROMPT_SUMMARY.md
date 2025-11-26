專案摘要 — as08 網頁需求與實作紀錄

概覽

- 目標：使用 Bootstrap 與 jQuery 製作一個單頁式多媒體敘事頁（Landing → Onboarding → Core Narrative Chapters）。
- 主要需求：
  1. Landing Page：寬 100% 黑底，顯示「一日採訪日記」。
  2. Onboarding Page：頁面分左右兩欄，左欄為四格影片縮圖（直式），且縮圖 sticky top；右欄為網頁內容摘要。Onboarding 區希望高度可調（使用者要求 200vh，後來變更為 100vh 並有 margin/padding 微調）。
  3. Core Narrative Chapters：四個章節各以對應影片為背景，文字在前（黑底白字），影片在畫面上固定或 sticky，並於滾動時切換。使用者要求：
     - 章節的背景影片在使用者滾過 Onboarding 後才載入與顯示（避免一開始就載入）。
     - 當從章節往回滑到 Onboarding，Onboarding 必須能正常顯示，影片隱藏或暫停。
     - 最終視覺行為：背景影片在滾動中自然出現並在碰到視窗頂端後變為 sticky(top)，文字以黑底白字在前方滾動。
     - 文字與影片應同時出現（同步顯示/動畫）；文字為長段落（每章節有標題與內容，總字數接近要求）。
  4. 互動細節：縮圖懸停播放預覽、點擊縮圖平滑滾動到對應章節、滾動觸發哪個章節 active（章節頂端進到視窗頂端時切換）。

已實作的要點（檔案：`as08/index.html`）

- Landing Page：黑底、置中文字。
- Onboarding Page：
  - 左欄四個縮圖影片（已改為直式 portrait，CSS 使用 aspect-ratio 與 object-fit；後來使用者要求縮圖寬度為 10% 並置中、間距 5px）。
  - Onboarding 區高度/間距可調（曾多次變更，如 200vh、100vh、padding/margin 微調）。
  - 縮圖行為：mouseenter 播放預覽、mouseleave 暫停並回 0、點擊平滑滾到章節。
- Core Narrative Chapters：
  - 四個章節各包含一個 <video class="bg-video" data-src="...">（延遲載入 data-src），文字在 .text-overlay（黑底白字）。
  - 影片延遲載入邏輯：JS 偵測使用者是否滾過 Onboarding（底部），若滾過會把 data-src 寫回 src，並在 body 加 class `videos-enabled`。
  - 當使用者在章節區域時，章節成為 .active（切換條件改為：章節 top 到達或小於視窗頂端時），會同時播放該章節影片並顯示文字（文字使用 CSS 淡入/位移效果；後續使用者要求改為文字與影片同時出現，並改用 sticky 介面讓影片在到達頂端後停留）。
  - 若使用者向上滾回 Onboarding，JS 會移除 body 的 `in-chapters` 類別並暫停影片（選擇是否重置 currentTime）。
- 影片播放行為：僅播放當前章節的影片，其他影片會 pause 並 reset（可調整為保留進度）。

主要檔案變更

- as08/index.html
  - 加入 Bootstrap CDN、jQuery CDN
  - 新增並修改 CSS：縮圖直式、置中、gap=5px、縮圖寬度 10%（使用 !important）、Onboarding padding 與高度、文字黑底白字樣式、bg-video 行為（sticky 或 fixed 的多次變更以滿足視覺需求）
  - 新增並修改 JS：延遲載入影片（data-src→src）、滾動偵測決定何時 enable 影片、章節 active 判定、縮圖互動（hover/leave/click）

已知限制與建議

- 瀏覽器對於自動播放影片（即使 muted）在部分情況仍會有限制；若影片未自動播放，需使用者互動或顯示 poster。
- MOV 格式在部分瀏覽器相容性較差，建議將影片轉為 MP4 或 WebM（H.264/VP9）以提高相容性。
- 效能：若影片檔案較大，建議採用更細緻的 lazy-loading 與 poster 策略，或使用低解析度預覽。
- 可改進：使用 IntersectionObserver 取代手動的 scroll/getBoundingClientRect 判斷，能提高效能並更精準觸發。

下一步（可選，請指示）

- 使用 IntersectionObserver 改寫章節與 Onboarding 的觸發邏輯。
- 將縮圖在行動版/小視窗改為回到 100% 寬、或加入響應式行為。
- 改為不重置影片 progress（或改為淡入淡出切換）。

備註

- 本摘要以目前 `as08/index.html` 的內容為準；使用者在測試過程中曾多次手動還原檔案並要求多次微調，檔案中保留多個版本的想法與修改（fixed/sticky/opacity 設定在不同時間有變更）。

