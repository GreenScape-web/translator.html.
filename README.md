<!doctype html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>مترجم Google</title>
<style>
body{font-family:"Segoe UI",Tahoma,Arial,sans-serif; background:#0f1724; color:#e6eef3; display:flex; justify-content:center; padding:20px;}
.container{max-width:900px; width:100%; background:#0b1120; padding:20px; border-radius:14px;}
h1{margin:0 0 10px 0;font-size:20px;}
textarea{width:100%;min-height:120px;padding:10px;font-size:16px;border-radius:10px;background:#071827;color:#e6eef3;border:none;resize:vertical;}
select,button{padding:8px 10px;border-radius:10px;border:none;background:#06b6d4;color:#032;cursor:pointer;font-size:14px;}
.row{display:flex;gap:10px;margin:10px 0;flex-wrap:wrap;}
.result{min-height:120px;padding:10px;background:#071827;border-radius:10px;}
.loader{display:inline-block;width:18px;height:18px;border-radius:50%;border:2px solid rgba(255,255,255,0.06);border-top-color:#06b6d4; animation:spin 1s linear infinite;}
@keyframes spin{to{transform:rotate(360deg)}}
</style>
</head>
<body>
<div class="container">
<h1>مترجم Google</h1>

<div class="row">
<select id="from-lang">
  <option value="">كشف تلقائي</option>
  <option value="ar">العربية</option>
  <option value="en">الإنجليزية</option>
  <option value="fr">الفرنسية</option>
  <option value="es">الإسبانية</option>
  <option value="de">الألمانية</option>
  <option value="zh">الصينية</option>
  <option value="ja">اليابانية</option>
  <option value="ko">الكورية</option>
</select>
<select id="to-lang">
  <option value="ar">العربية</option>
  <option value="en" selected>الإنجليزية</option>
  <option value="fr">الفرنسية</option>
  <option value="es">الإسبانية</option>
  <option value="de">الألمانية</option>
  <option value="zh">الصينية</option>
  <option value="ja">اليابانية</option>
  <option value="ko">الكورية</option>
</select>
<button id="swap-btn">⟲ تبديل</button>
</div>

<textarea id="source-text" placeholder="اكتب النص هنا ..."></textarea>
<div class="row">
<button id="translate-btn">ترجمة</button>
<button id="speak-src">🔊 تشغيل نص المصدر</button>
<button id="speak-tgt">🔊 تشغيل الترجمة</button>
</div>

<div class="result" id="result"></div>
<div id="status"></div>
</div>

<script>
const sourceText = document.getElementById('source-text');
const resultBox = document.getElementById('result');
const fromSelect = document.getElementById('from-lang');
const toSelect = document.getElementById('to-lang');
const statusSpan = document.getElementById('status');

// مفتاح Google API الخاص بك هنا
const GOOGLE_API_KEY = 'AIzaSyAbP6tuUV4MEzhniCXMNxsP4bpQB89u1UM';

document.getElementById('translate-btn').addEventListener('click', async ()=>{
  const q = sourceText.value.trim();
  if(!q) return alert('أدخل النص');
  const source = fromSelect.value;
  const target = toSelect.value;

  statusSpan.innerHTML = '<span class="loader"></span> ترجمة...';
  resultBox.textContent='';

  try{
    const res = await fetch(`https://translation.googleapis.com/language/translate/v2?key=${GOOGLE_API_KEY}`, {
      method: 'POST',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify({q, source: source||undefined, target, format:'text'})
    });
    const data = await res.json();
    const translated = data.data?.translations?.[0]?.translatedText || '';
    resultBox.textContent = translated;
    statusSpan.textContent = 'تمت الترجمة';
  }catch(err){
    resultBox.textContent = 'خطأ: ' + (err.message||err);
    statusSpan.textContent = 'فشل';
  }
});

document.getElementById('swap-btn').addEventListener('click', ()=>{
  const tmp = fromSelect.value;
  fromSelect.value = toSelect.value;
  toSelect.value = tmp;
  const tempText = resultBox.textContent;
  resultBox.textContent = sourceText.value;
  sourceText.value = tempText;
});

function speak(text, lang){
  if(!text) return;
  if('speechSynthesis' in window){
    const u = new SpeechSynthesisUtterance(text);
    u.lang = lang||'ar';
    speechSynthesis.cancel();
    speechSynthesis.speak(u);
  } else alert('الميكروفون/الصوت غير مدعوم');
}

document.getElementById('speak-src').addEventListener('click', ()=> speak(sourceText.value, fromSelect.value||'ar'));
document.getElementById('speak-tgt').addEventListener('click', ()=> speak(resultBox.textContent, toSelect.value));
</script>
</body>
</html>
