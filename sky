<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>구글 시트 → 깜박이는 문구</title>
<style>
  :root { --font: system-ui, AppleSDGothicNeo, "Malgun Gothic", Arial, sans-serif; }
  html, body { height: 100%; margin: 0; }
  body {
    display: grid;
    place-items: center;
    font-family: var(--font);
    background: #fff;
  }
  .card {
    min-width: 280px;
    max-width: 90vw;
    padding: 24px 28px;
    border: 1px solid #e8e8e8;
    border-radius: 16px;
    box-shadow: 0 6px 20px rgba(0,0,0,.06);
    text-align: center;
  }
  .label { font-size: 14px; color: #666; margin-bottom: 8px; }
  .text {
    font-size: 28px;
    line-height: 1.3;
    font-weight: 700;
    word-break: keep-all;
  }
  /* 깜박임 효과 */
  .blink { animation: blink 1s step-start infinite; }
  @keyframes blink { 50% { visibility: hidden; } }

  .meta { margin-top: 12px; font-size: 12px; color: #999; }
  .error { color: #b00020; font-weight: 600; }
</style>
</head>
<body>
  <div class="card">
    <div class="label">시트 값에 따른 표시</div>
    <div id="text" class="text blink">불러오는 중…</div>
    <div id="meta" class="meta"></div>
  </div>

<script>
/* ========================= 설정 =========================
 * 시트 공개: "링크가 있는 모든 사용자(보기)"로 설정
 * SHEET_NAME은 시트 탭 이름을 정확히 입력
 */
const SHEET_ID   = "16_aHITP-iPWE57OWnv85gw60qTN6Rhfo-41G1_rQpT0"; // 주신 시트 ID
const SHEET_NAME = "시트1";      // <-- 여기를 실제 탭 이름으로 바꾸세요
const RANGE      = "A1:B2";
const REFRESH_MS = 5000;         // 5초마다 재조회

const GVIZ_URL = `https://docs.google.com/spreadsheets/d/${encodeURIComponent(SHEET_ID)}/gviz/tq?` +
                 `tqx=out:json&sheet=${encodeURIComponent(SHEET_NAME)}&range=${encodeURIComponent(RANGE)}`;

const $text = document.getElementById("text");
const $meta = document.getElementById("meta");

function parseGviz(text) {
  const start = text.indexOf("{");
  const end   = text.lastIndexOf("}");
  if (start === -1 || end === -1) throw new Error("GViz 응답 파싱 실패");
  return JSON.parse(text.slice(start, end + 1));
}

function applyData(rows) {
  const A1 = rows?.[0]?.c?.[0]?.v ?? null;
  const B1 = rows?.[0]?.c?.[1]?.v ?? "";
  const B2 = rows?.[1]?.c?.[1]?.v ?? "";

  let message;
  if (A1 === 1 || A1 === "1") {
    message = B1;
  } else {
    message = B2;
  }

  $text.textContent = message || "(표시할 문구가 없습니다)";
  $meta.textContent = `A1=${A1 ?? "N/A"} · ${new Date().toLocaleString()}`;
}

async function loadOnce() {
  try {
    $meta.textContent = "불러오는 중…";
    const res = await fetch(GVIZ_URL, { cache: "no-store" });
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const raw = await res.text();
    const json = parseGviz(raw);
    applyData(json.table.rows);
  } catch (err) {
    console.error(err);
    $text.textContent = "데이터를 불러오지 못했습니다";
    $text.classList.remove("blink");
    $text.classList.add("error");
    $meta.textContent = `${new Date().toLocaleString()}`;
  }
}

loadOnce();
if (typeof REFRESH_MS === "number" && REFRESH_MS > 0) {
  setInterval(loadOnce, REFRESH_MS);
}
</script>
</body>
</html>
