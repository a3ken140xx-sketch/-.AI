<!doctype html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<title>مترجم فوري ذكي</title>
<style>
:root{
  --bg:#0f1220; --card:#171a2b; --muted:#8a90a6; --text:#e8ebf5;
  --accent:#6dd5fa; --accent2:#7f53ac; --success:#35d07f; --danger:#ff5d5d;
  --radius:16px; --shadow:0 10px 30px rgba(0,0,0,.25);
}
body{margin:0; background:var(--bg); color:var(--text);
  font-family: ui-sans-serif,"Noto Naskh Arabic",Tahoma;
  min-height:100dvh; display:flex; justify-content:center; padding:20px;}
.app{width:100%; max-width:1100px; display:grid; gap:16px;}
.header{display:flex; justify-content:space-between; align-items:center; padding:8px 12px;
  background:linear-gradient(145deg, rgba(255,255,255,.04), rgba(255,255,255,.02));
  border-radius:var(--radius);}
.status{font-size:12px; color:var(--muted);}
.status.ok{color:var(--success);} .status.err{color:var(--danger);}
.controls{display:grid; grid-template-columns:1fr auto 1fr; gap:8px;}
.select{display:flex; align-items:center; gap:8px; background:var(--card);
  border-radius:12px; padding:8px;}
select{background:none; border:none; color:var(--text); flex:1;}
.swap{width:40px; height:40px; border-radius:50%; border:none;
  background:linear-gradient(135deg,var(--accent),var(--accent2));
  cursor:pointer; color:#0b0e1a;}
.panes{display:grid; grid-template-columns:1fr 1fr; gap:12px;}
textarea{background:var(--card); border:1px solid rgba(255,255,255,.08);
  border-radius:12px; color:var(--text); padding:12px; min-height:160px; resize:vertical;}
.toolbar{display:flex; gap:8px; margin-top:4px; flex-wrap:wrap;}
.btn{padding:8px; border:none; border-radius:10px; cursor:pointer; flex:1;}
.primary{background:linear-gradient(135deg,var(--accent),var(--accent2)); color:#0b0e1a;}
.ghost{background:none; border:1px solid rgba(255,255,255,.08); color:var(--text);}
@media (max-width:900px){.panes{grid-template-columns:1fr}}
</style>
</head>
<body>
<main class="app">
  <div class="header">
    <div>🌐 مترجم فوري ذكي</div>
    <div id="status" class="status">جاهز</div>
  </div>

  <div class="controls">
    <div class="select">
      <span>من</span>
      <select id="from"><option value="auto">Auto</option></select>
    </div>
    <button id="swap" class="swap">⇄</button>
    <div class="select">
      <span>إلى</span>
      <select id="to"></select>
    </div>
  </div>

  <div class="panes">
    <div>
      <textarea id="src" placeholder="اكتب النص هنا..."></textarea>
      <div class="toolbar">
        <button id="clear" class="ghost">مسح</button>
        <button id="paste" class="ghost">لصق</button>
      </div>
    </div>
    <div>
      <textarea id="dst" readonly placeholder="الترجمة هنا"></textarea>
      <div class="toolbar">
        <button id="translate" class="primary">ترجمة</button>
        <button id="aiEnhance" class="ghost">تحسين بالذكاء الاصطناعي</button>
        <button id="copy" class="ghost">نسخ</button>
      </div>
    </div>
  </div>
</main>

<script>
const LANGS=[["ar","🇸🇦 Arabic"],["en","🇬🇧 English"],["fr","🇫🇷 French"],["es","🇪🇸 Spanish"],["de","🇩🇪 German"],["tr","🇹🇷 Turkish"]];
const fromSel=document.getElementById('from'),toSel=document.getElementById('to');
LANGS.forEach(([c,n])=>{
  let o=document.createElement('option'); o.value=c; o.textContent=n; fromSel.appendChild(o.cloneNode(true));
  toSel.appendChild(o);
});
toSel.value="en";
const status=document.getElementById('status');
const src=document.getElementById('src'),dst=document.getElementById('dst');

function setStatus(msg,type=""){status.textContent=msg; status.className="status "+type;}

async function viaLibre(text,from,to){
  const r=await fetch("https://libretranslate.com/translate",{
    method:"POST",headers:{"Content-Type":"application/json"},
    body:JSON.stringify({q:text,source:(from||"auto"),target:to})
  });
  if(!r.ok) throw 1; let d=await r.json();
  return d.translatedText;
}
async function viaMyMemory(text,from,to){
  const f=(from==="auto"?"auto":from);
  const r=await fetch(`https://api.mymemory.translated.net/get?q=${encodeURIComponent(text)}&langpair=${f}|${to}`);
  if(!r.ok) throw 1; let d=await r.json();
  return d?.responseData?.translatedText;
}
async function translateText(){
  if(!src.value.trim()) return;
  setStatus("⏳ يترجم...");
  dst.value="";
  try{
    let out; try{ out=await viaLibre(src.value,fromSel.value,toSel.value);}
    catch{ out=await viaMyMemory(src.value,fromSel.value,toSel.value);}
    dst.value=out||"❌ لم تنجح الترجمة";
    if(out) setStatus("✅ تم","ok"); else setStatus("⚠️ فشل","err");
  }catch(e){setStatus("⚠️ خطأ","err");}
}
async function aiEnhanceText(){
  if(!dst.value.trim()) return;
  setStatus("🤖 يحسّن الصياغة...");
  try{
    // عدّل الرابط والمفتاح حسب مزوّدك
    let r=await fetch("https://api.example.com/ai",{
      method:"POST",headers:{"Content-Type":"application/json","Authorization":"Bearer YOUR_KEY"},
      body:JSON.stringify({text:dst.value,instruction:"أعد صياغة النص ليكون أنعم وأوضح"})
    });
    let d=await r.json();
    dst.value=d.output || dst.value;
    setStatus("✨ تم التحسين","ok");
  }catch(e){setStatus("⚠️ لم ينجح التحسين","err");}
}

document.getElementById('translate').onclick=translateText;
document.getElementById('aiEnhance').onclick=aiEnhanceText;
document.getElementById('copy').onclick=()=>navigator.clipboard.writeText(dst.value);
document.getElementById('clear').onclick=()=>{src.value=""; dst.value=""; setStatus("جاهز");};
document.getElementById('paste').onclick=async()=>{src.value=await navigator.clipboard.readText();};
document.getElementById('swap').onclick=()=>{
  let tv=toSel.value; toSel.value=fromSel.value; fromSel.value=tv;
  let txt=src.value; src.value=dst.value; dst.value=txt;
};
</script>
</body>
</html>
