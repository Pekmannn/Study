[index.html](https://github.com/user-attachments/files/34169221/index.html)
# Study<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="theme-color" content="#4f46e5">
<title>מרכז הלימוד</title>
<link href="https://fonts.googleapis.com/css2?family=Heebo:wght@400;600;800&display=swap" rel="stylesheet">
<style>
:root{--bg:#eff0f6;--card:#fff;--tx:#14151f;--mut:#686c80;--line:#dfe1ec;--ac:#4f46e5;color-scheme:light}
@media (prefers-color-scheme:dark){:root{--bg:#0e0f17;--card:#171926;--tx:#eef0fa;--mut:#8f93aa;--line:#272a3d;--ac:#8b85ff;color-scheme:dark}}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--tx);font-family:Heebo,system-ui,-apple-system,"Segoe UI",Arial,sans-serif;line-height:1.5}
.wrap{max-width:600px;margin:0 auto;padding:12px 14px 96px}
.dt{font-size:22px;font-weight:800;margin:4px 0 12px}
.card{background:var(--card);border:1px solid var(--line);border-radius:14px;padding:16px;margin-bottom:12px}
h3{margin:0 0 12px;font-size:17px}
.mut{color:var(--mut);font-size:14px}
.row{display:flex;gap:8px}
.b{flex:1;padding:13px 8px;border:0;border-radius:12px;background:var(--ac);color:#fff;font-size:16px;font-weight:600;font-family:inherit;cursor:pointer}
.b:disabled{opacity:.6}
.b.s{background:transparent;color:var(--tx);border:1px solid var(--line)}
input,select,textarea{width:100%;padding:11px;border:1px solid var(--line);border-radius:10px;background:var(--bg);color:var(--tx);font-size:16px;font-family:inherit}
label{display:block;font-size:13px;color:var(--mut);margin-bottom:4px}
.g2{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-bottom:10px}
.chips{display:flex;gap:8px;flex-wrap:wrap}
.chip{padding:8px 14px;border-radius:20px;border:1px solid var(--line);background:var(--bg);color:var(--tx);font-family:inherit;font-size:15px;cursor:pointer}
.chip.on{background:var(--ac);color:#fff;border-color:var(--ac)}
.opt{display:block;width:100%;text-align:right;padding:12px;margin-bottom:8px;border:1px solid var(--line);border-radius:12px;background:var(--bg);color:var(--tx);font-size:16px;font-family:inherit;cursor:pointer}
.opt.ok{border-color:#16a34a;background:rgba(22,163,74,.16)}
.opt.no{border-color:#dc2626;background:rgba(220,38,38,.16)}
.hint{margin-top:10px;color:var(--mut)}
.hint div{margin-top:6px}
.big{font-size:44px;font-weight:800;color:var(--ac);line-height:1}
a{color:var(--ac)}
nav{position:fixed;bottom:0;left:0;right:0;display:flex;background:var(--card);border-top:1px solid var(--line);padding:6px 6px calc(6px + env(safe-area-inset-bottom,0px))}
nav button{flex:1;background:none;border:0;color:var(--mut);font-size:12px;padding:5px;font-family:inherit;cursor:pointer}
nav button span{display:block;font-size:21px}
nav .on{color:var(--ac);font-weight:800}
</style>
</head>
<body>
<div class="wrap" id="app"></div>
<nav id="nav"></nav>
<script>
var KEY='study-gemini-v1',MAXC=60000,S={cs:[],cur:null,w:{},key:'',model:'gemini-flash-latest'},tab='c',Q=null,dcf=0;
try{var r=localStorage.getItem(KEY);if(r)S=Object.assign(S,JSON.parse(r))}catch(e){}
function save(){try{localStorage.setItem(KEY,JSON.stringify(S))}catch(e){}}
function esc(s){return String(s==null?'':s).replace(/[&<>"]/g,function(c){return{'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c]})}
function C(){return S.cs.filter(function(c){return c.id===S.cur})[0]}
function $(id){return document.getElementById(id)}
function num(id){return parseFloat($(id).value)||0}
async function gem(parts,max){
 if(!S.key)throw new Error('הוסף מפתח Gemini בלשונית ״הגדרות״.');
 var r=await fetch('https://generativelanguage.googleapis.com/v1beta/models/'+encodeURIComponent(S.model||'gemini-flash-latest')+':generateContent',{method:'POST',headers:{'Content-Type':'application/json','x-goog-api-key':S.key},body:JSON.stringify({contents:[{parts:parts}],generationConfig:{maxOutputTokens:max||16000}})});
 var j=await r.json();if(!r.ok)throw new Error((j.error&&j.error.message)||'שגיאת API');
 var c=j.candidates&&j.candidates[0];
 if(!c||!c.content||!c.content.parts)throw new Error('לא התקבלה תשובה'+(c&&c.finishReason?' ('+c.finishReason+')':''));
 return c.content.parts.map(function(p){return p.text||''}).join('')}
function ask(p,max){return gem([{text:p}],max)}
async function run(id,p,cb){var e=$(id);e.innerHTML='<div class="mut">Gemini עובד על זה…</div>';
 try{var t=await ask(p);if(cb)cb(t);else e.innerHTML='<div style="white-space:pre-wrap">'+esc(t)+'</div>'}catch(x){e.innerHTML='<div class="mut">'+esc(x.message||'לא הצלחתי. נסה שוב.')+'</div>'}}
function need(){var c=C();if(!c||!c.notes.trim())return'<div class="card mut">קודם צור קורס והוסף חומר בטאב ״קורסים״.</div>';return''}
function add(){var n=$('cn').value.trim();if(!n)return;var id=Date.now();S.cs.push({id:id,name:n,notes:'',sum:''});S.cur=id;save();render()}
function sel(id){S.cur=id;Q=null;dcf=0;save();render()}
function del(id){if(dcf!==id){dcf=id;render();return}S.cs=S.cs.filter(function(c){return c.id!==id});S.cur=S.cs.length?S.cs[0].id:null;Q=null;dcf=0;save();render()}
function st(t){var e=$('ms');if(e)e.textContent=t}
function addN(title,t){var c=C();c.notes+=(c.notes?'\n\n':'')+'--- '+title+' ---\n'+t;save();render()}
function loadT(i){var f=i.files[0];if(!f)return;var r=new FileReader();r.onload=function(){var t=String(r.result).replace(/^WEBVTT.*$/m,'').replace(/^\d+\s*$/gm,'').replace(/\d{2}:\d{2}:\d{2}[.,]\d{3} --> .*$/gm,'').replace(/\n{2,}/g,'\n').trim();addN(f.name,t)};r.readAsText(f)}
var MIME={mp3:'audio/mp3',m4a:'audio/mp4',wav:'audio/wav',aac:'audio/aac',ogg:'audio/ogg',flac:'audio/flac',aiff:'audio/aiff',webm:'audio/webm'};
function b64(f){return new Promise(function(res,rej){var r=new FileReader();r.onload=function(){res(String(r.result).split(',')[1])};r.onerror=rej;r.readAsDataURL(f)})}
async function doTr(i){var f=i.files[0];if(!f)return;
 if(f.size>18*1024*1024){st('הקובץ גדול מ-18MB. דחוס אותו (למשל mp3 מונו 32kbps) או חתוך אותו לחלקים.');return}
 var ext=(f.name.split('.').pop()||'').toLowerCase(),mt=MIME[ext]||f.type||'audio/mpeg';
 st('מתמלל… זה יכול לקחת כמה דקות.');
 try{var t=await gem([{inlineData:{mimeType:mt,data:await b64(f)}},{text:'תמלל בעברית את ההקלטה במלואה, מילה במילה. החזר רק את הטקסט המתומלל בפסקאות, בלי הקדמות ובלי סימני markdown. אל תסכם ואל תתייג דוברים. אם קטע לא ברור, כתוב [לא ברור].'}],32768);
  addN(f.name,t)}catch(e){st(e.message||'התמלול נכשל.')}}
function vC(){var c=C(),h='<div class="card"><h3>קורס חדש</h3><div class="row"><input id="cn" placeholder="למשל: סטטיסטיקה 1"><button class="b" style="flex:0 0 84px" onclick="add()">הוספה</button></div></div>';
 if(S.cs.length)h+='<div class="card"><h3>הקורסים שלי</h3><div class="chips">'+S.cs.map(function(x){return'<button class="chip'+(x.id===S.cur?' on':'')+'" onclick="sel('+x.id+')">'+esc(x.name)+'</button>'}).join('')+'</div></div>';
 if(c)h+='<div class="card"><h3>חומר הלימוד: '+esc(c.name)+'</h3><textarea id="nt" rows="12" placeholder="הדבק סיכומים, שקפים או תמלולים, או העלה קובץ" oninput="C().notes=this.value;save()">'+esc(c.notes)+'</textarea><div class="mut" style="margin:8px 0">עד כ-60,000 תווים נשלחים לעיבוד (בערך 70 דקות של הקלטה).</div>'
 +'<div class="row" style="margin-bottom:8px"><label class="b s" style="margin:0;display:flex;align-items:center;justify-content:center;color:var(--tx);font-size:16px">🎙️ תמלול הקלטה<input type="file" accept="audio/*,.m4a,.mp3,.wav,.aac,.ogg,.flac" onchange="doTr(this)" style="display:none"></label><label class="b s" style="margin:0;display:flex;align-items:center;justify-content:center;color:var(--tx);font-size:16px">📄 העלאת טקסט<input type="file" accept=".txt,.srt,.vtt,.md,text/plain" onchange="loadT(this)" style="display:none"></label></div><div id="ms" class="mut" style="margin-bottom:8px"></div>'
 +'<button class="b s" style="width:100%" onclick="del('+c.id+')">'+(dcf===c.id?'לחץ שוב למחיקה':'מחיקת הקורס')+'</button></div>';
 return h}
function vS(){var c=C(),e=need();if(e)return e;
 return'<div class="card"><h3>סיכום: '+esc(c.name)+'</h3><label>סגנון</label><select id="ss" style="margin-bottom:10px"><option>סיכום קצר: נקודות עיקריות</option><option>סיכום מפורט לפי נושאים</option><option>כרטיסיות שאלה ותשובה</option><option>מושגים והגדרות</option></select><button class="b" style="width:100%" onclick="doS()">סכם</button></div><div class="card" id="so">'+(c.sum?'<div style="white-space:pre-wrap">'+esc(c.sum)+'</div>':'<div class="mut">הסיכום יופיע כאן וישמר בקורס.</div>')+'</div>'}
function doS(){var c=C(),st=$('ss').value;
 run('so','סכם בעברית את חומר הלימוד הבא. סגנון: '+st+'. החזר טקסט פשוט בלי סימני markdown כמו כוכביות או סולמיות, השתמש במקפים ובשורות ריקות בין נושאים. אל תוסיף מידע שאינו בחומר.\n\nחומר:\n'+c.notes.slice(0,MAXC),function(t){c.sum=t;save();$('so').innerHTML='<div style="white-space:pre-wrap">'+esc(t)+'</div>'})}
function hintTxt(){var x=Q.it[Q.i];return x.h.slice(0,Q.hs).map(function(t){return'<div>💡 '+esc(t)+'</div>'}).join('')}
function vQ(){var e=need();if(e)return e;
 if(!Q)return'<div class="card"><h3>מבחן מהחומר</h3><div class="g2"><div><label>מספר שאלות</label><select id="qn"><option>5</option><option>8</option><option>10</option></select></div><div><label>סוג</label><select id="qt"><option>מעורב</option><option>אמריקאיות</option><option>פתוחות</option></select></div></div><button class="b" style="width:100%" onclick="startQ()">התחל מבחן</button><div id="qm" class="mut" style="margin-top:8px"></div></div>';
 var n=Q.it.length;
 if(Q.i>=n){var m=Q.miss.filter(function(t,i){return t&&Q.miss.indexOf(t)===i});
  return'<div class="card"><h3>סיום המבחן</h3><div class="big">'+Math.round(Q.sc/n*100)+'%</div><div class="mut" style="margin:8px 0">ניקוד '+(Math.round(Q.sc*10)/10)+' מתוך '+n+' · רמזים שנוצלו: '+Q.nh+'</div>'+(m.length?'<div><b>נושאים לחזרה:</b> '+m.map(esc).join(', ')+'</div>':'<div>עברת את כל הנושאים 👏</div>')+'<button class="b" style="width:100%;margin-top:12px" onclick="Q=null;render()">מבחן חדש</button></div>'}
 var x=Q.it[Q.i],h='<div class="card"><div class="mut">שאלה '+(Q.i+1)+' מתוך '+n+' · ניקוד '+(Math.round(Q.sc*10)/10)+'</div><div style="font-size:18px;font-weight:600;margin:8px 0 12px">'+esc(x.q)+'</div>';
 if(x.t==='mc')h+=x.o.map(function(o,j){var s='';if(Q.st)s=j===x.a?' ok':(j===Q.pick?' no':'');return'<button class="opt'+s+'" onclick="ansMc('+j+')">'+esc(o)+'</button>'}).join('');
 else if(!Q.st)h+='<textarea id="qa" rows="4" placeholder="התשובה שלך"></textarea><button class="b" style="width:100%;margin-top:8px" id="qb" onclick="ansO()">בדיקה</button>';
 h+='<div id="hv" class="hint">'+hintTxt()+'</div>';
 if(!Q.st)h+='<button class="b s" style="width:100%;margin-top:8px" onclick="hint()">💡 רמז</button>';
 else h+='<div style="white-space:pre-wrap;margin:12px 0">'+esc(Q.fb)+'</div><button class="b" style="width:100%" onclick="nextQ()">'+(Q.i+1>=n?'לתוצאות':'השאלה הבאה')+'</button>';
 return h+'</div>'}
async function startQ(){var c=C(),m=$('qm');m.textContent='Gemini מכין מבחן…';
 try{var t=await ask('על סמך חומר הלימוד הבא הכן מבחן בעברית עם '+$('qn').value+' שאלות, סוג: '+$('qt').value+'. החזר JSON בלבד, בלי טקסט נוסף ובלי גדרות קוד: מערך של אובייקטים בפורמט {"t":"mc" או "open","q":"השאלה","o":["ארבע אפשרויות"],"a":"ב-mc אינדקס התשובה הנכונה 0 עד 3, ב-open תשובה מלאה לדוגמה","topic":"נושא קצר","h":["רמז עדין שלא חושף את התשובה","רמז חזק יותר"],"e":"הסבר קצר"}. השדה o קיים רק ב-mc. השאלות מבוססות רק על החומר.\n\nחומר:\n'+c.notes.slice(0,MAXC),32768);
  var a=JSON.parse(t.match(/\[[\s\S]*\]/)[0]);a.forEach(function(x){if(x.t==='mc')x.a=+x.a;x.h=x.h||[]});
  Q={it:a,i:0,sc:0,st:0,hs:0,nh:0,miss:[],fb:'',pick:-1};render()}catch(x){m.textContent=x instanceof SyntaxError||x instanceof TypeError?'לא הצלחתי להכין מבחן. נסה שוב.':(x.message||'שגיאה')}}
function hint(){var x=Q.it[Q.i],v=$('hv');if(Q.hs<x.h.length){Q.hs++;Q.nh++;v.innerHTML=hintTxt()}else v.innerHTML=hintTxt()+'<div>אין עוד רמזים לשאלה הזאת.</div>'}
function ansMc(j){if(Q.st)return;var x=Q.it[Q.i],ok=j===x.a;Q.pick=j;Q.st=1;if(ok)Q.sc++;else Q.miss.push(x.topic);Q.fb=(ok?'נכון ✓ ':'לא נכון. ')+(x.e||'');render()}
async function ansO(){var x=Q.it[Q.i],v=$('qa').value.trim();if(!v)return;var b=$('qb');b.disabled=true;b.textContent='בודק…';
 try{var t=await ask('שאלה: '+x.q+'\nתשובה לדוגמה: '+x.a+'\nתשובת הסטודנט: '+v+'\nבדוק את התשובה. החזר JSON בלבד: {"s":מספר בין 0 ל-1,"f":"משוב קצר בעברית: מה נכון ומה חסר"}');
  var j=JSON.parse(t.match(/\{[\s\S]*\}/)[0]),s=Math.max(0,Math.min(1,+j.s||0));Q.sc+=s;if(s<0.6)Q.miss.push(x.topic);Q.st=1;Q.fb=j.f+'\n\nתשובה לדוגמה: '+x.a;render()}
 catch(e){b.disabled=false;b.textContent='בדיקה (נסה שוב)'}}
function nextQ(){Q.i++;Q.st=0;Q.hs=0;Q.fb='';Q.pick=-1;render();window.scrollTo(0,0)}
function vW(){var w=S.w||{};
 return'<div class="card"><h3>עזרה בכתיבה</h3><label>במה לעזור</label><select id="wm" style="margin-bottom:10px"><option value="0">מתווה לעבודה</option><option value="1">משוב על טיוטה</option><option value="2">ניסוח מחדש של טקסט</option><option value="3">רמז להמשך, בלי לכתוב במקומי</option><option value="4">עיצוב רשימת מקורות (הדבק פרטי מקורות בתיבת הטיוטה)</option><option value="5">בדיקת הטקסט מול כללי APA</option></select><label>מהדורת APA</label><select id="wa" style="margin-bottom:10px"><option value="4">APA מהדורה 4</option><option value="6">APA מהדורה 6</option><option value="7">APA מהדורה 7</option></select><label>נושא או הנחיות העבודה</label><textarea id="wt" rows="3" oninput="wsv()" style="margin-bottom:10px">'+esc(w.t)+'</textarea><label>הטיוטה או הטקסט (אם יש)</label><textarea id="wd" rows="8" oninput="wsv()" style="margin-bottom:10px">'+esc(w.d)+'</textarea><button class="b" style="width:100%" onclick="doW()">שלח</button></div><div class="card" id="wo"><div class="mut">התשובה תופיע כאן.</div></div>'}
function wsv(){S.w={t:$('wt').value,d:$('wd').value};save()}
function doW(){var m=$('wm').value,a=$('wa').value,c=C(),w=S.w||{},
 I=['הצע מתווה מפורט לעבודה אקדמית: כותרות, נקודות לכל פרק ושאלות מחקר. אל תכתוב את העבודה עצמה.','תן משוב על הטיוטה: חוזקות, חולשות בטיעון ובמבנה, ניסוח, ומה לשפר בסדר עדיפות.','נסח מחדש את הטקסט בעברית אקדמית ברורה ושמור על המשמעות.','תן רמז קצר איך להמשיך מהנקודה שבה הטיוטה נעצרה: איזו שאלה כדאי לענות עליה עכשיו. אל תכתוב את ההמשך.','עצב את רשימת המקורות שבטיוטה כרשימת ביבליוגרפיה מסודרת בסדר אלפבתי. אם חסרים פרטים, ציין מה חסר ואל תנחש.','בדוק את הטקסט מול כללי APA: ציטוט בגוף הטקסט, רשימת מקורות, רמות כותרות, מבנה ופרטים טכניים. רשום כל סטייה והצע תיקון.'];
 run('wo',I[m]+' כל הכתיבה והעיצוב חייבים להיות לפי כללי APA מהדורה '+a+' (ציטוטים בגוף הטקסט, רשימת מקורות, כותרות), כולל התאמות למקורות בעברית. אם אינך בטוח בכלל מסוים במהדורה הזאת, ציין זאת בסוף בקצרה. אל תמציא מקורות או ציטוטים. החזר טקסט פשוט בלי סימני markdown.\n\nנושא והנחיות: '+(w.t||'')+'\n\nטיוטה: '+(w.d||'')+(c&&c.notes?'\n\nחומר הקורס (להקשר):\n'+c.notes.slice(0,6000):''))}
function vG(){return'<div class="card"><h3>הגדרות</h3><label>מפתח Gemini API</label><input id="gk" type="password" autocomplete="off" value="'+esc(S.key)+'" placeholder="AIza..." style="margin-bottom:10px"><label>שם המודל</label><input id="gm" value="'+esc(S.model)+'" style="margin-bottom:10px" dir="ltr"><button class="b" style="width:100%" onclick="saveG()">שמירה</button><div id="gs" class="mut" style="margin-top:8px"></div></div>'
 +'<div class="card mut"><b style="color:var(--tx)">איך מקבלים מפתח חינמי:</b> נכנסים ל-<a href="https://aistudio.google.com/apikey" target="_blank" rel="noopener">aistudio.google.com/apikey</a> עם חשבון Google ויוצרים מפתח.<br><br>המפתח והנתונים נשמרים בדפדפן הזה בלבד. אל תשתמש באתר במחשב משותף. החומר שאתה מעלה נשלח ל-Google. במסלול החינמי ייתכן שהיא משתמשת בו, ולכן כדאי לקרוא את התנאים לפני שמעלים חומר רגיש.<br><br>אם מתקבלת שגיאה על שם המודל, החלף אותו בשם מודל Flash עדכני מרשימת המודלים של Google. מכסת השימוש החינמית מוגבלת וכשהיא נגמרת מקבלים שגיאה 429, אז מחכים או ממשיכים למחרת.</div>'}
function saveG(){S.key=$('gk').value.trim();S.model=$('gm').value.trim()||'gemini-flash-latest';save();$('gs').textContent='נשמר ✓'}
function render(){var c=C();
 var h='<div class="dt">מרכז הלימוד'+(c?' <span class="mut" style="font-weight:400">· '+esc(c.name)+'</span>':'')+'</div>';
 h+=({c:vC,s:vS,q:vQ,w:vW,g:vG})[tab]();
 $('app').innerHTML=h;
 var T=[['c','📚','קורסים'],['s','📝','סיכום'],['q','🎯','מבחן'],['w','✍️','כתיבה'],['g','⚙️','הגדרות']];
 $('nav').innerHTML=T.map(function(t){return'<button class="'+(tab===t[0]?'on':'')+'" onclick="go(\''+t[0]+'\')"><span>'+t[1]+'</span>'+t[2]+'</button>'}).join('')}
function go(t){tab=t;render();window.scrollTo(0,0)}
render();
</script>
</body>
</html>
