<!DOCTYPE html><html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>إدارة الحلاقة – محمود علي موسى</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/qrcodejs/qrcode.min.js"></script>
<style>
body{font-family:Tahoma,Arial;background:#f4f4f4;margin:0;padding:0;}
header{background:#111;color:white;text-align:center;padding:20px}
header h1{margin:0;color:#FFD700}
.container{max-width:1000px;margin:auto;padding:20px}
.card{background:white;border-radius:10px;padding:15px;margin-bottom:20px;box-shadow:0 4px 10px rgba(0,0,0,0.1);}
label{display:block;margin-top:10px;font-weight:bold}
input,textarea,button{width:100%;padding:10px;margin-top:5px;border-radius:6px;border:1px solid #ccc}
button{background:#111;color:white;cursor:pointer;margin-top:10px;font-size:16px}
button:hover{background:#333}
.services label{font-weight:normal;display:block;margin-top:5px}
table{width:100%;border-collapse:collapse;margin-top:10px}
table th, table td{border:1px solid #ddd;padding:8px;text-align:center}
table th{background:#222;color:white}
.barcode{font-weight:bold;letter-spacing:2px}
.danger{background:#b00020;color:white}
.info{background:#0066cc;color:white}
.success{background:#0a7d2c;color:white}
</style>
</head>
<body>
<header>
<h1>✂️ إدارة الحلاقة – محمود علي موسى</h1>
<p>العنوان: نمره البصل | واتساب: +90 538 459 18 03</p>
</header>
<div class="container"><div class="card">
<h2>حجز موعد جديد</h2>
<label>اسم الزبون</label>
<input id="name" placeholder="اسمك" />
<label>رقم الهاتف</label>
<input id="phone" placeholder="رقمك" />
<label>تاريخ ووقت الحجز</label>
<input type="datetime-local" id="datetime" />
<label>الخدمات</label>
<div class="services">
<label><input type="checkbox" value="50" /> شعر + دقن (50)</label>
<label><input type="checkbox" value="25" /> دقن (25)</label>
<label><input type="checkbox" value="30" /> شعر (30)</label>
<label><input type="checkbox" value="15" /> فتلة (15)</label>
<label><input type="checkbox" value="15" /> مسك (15)</label>
<label><input type="checkbox" value="50" /> صبغة (50)</label>
<label><input type="checkbox" value="40" /> استشوار / كوي (40)</label>
<label><input type="checkbox" value="50" /> ألوان شعر (50)</label>
<label><input type="checkbox" value="25" /> مسك لزك (25)</label>
</div>
<p>الإجمالي: <span id="total">0</span></p>
<button onclick="book()">تأكيد الحجز</button>
</div><div class="card">
<h2>الحجوزات</h2>
<table>
<thead>
<tr><th>#</th><th>الاسم</th><th>الهاتف</th><th>الوقت</th><th>السعر</th><th>كود الحجز</th><th>إجراءات</th></tr>
</thead>
<tbody id="list"></tbody>
</table>
</div><div class="card">
<h2>مركز الشكاوى والنصائح</h2>
<textarea id="complaint" placeholder="اكتب الشكوى أو الاقتراح هنا..."></textarea>
<button class="info" onclick="addComplaint()">إرسال</button>
<div id="complaints"></div>
</div><div class="card">
<h2>باركود آخر زبون</h2>
<div id="qr"></div>
</div><script>
let bookings = [];
let banned = [];
let complains = [];
const services = document.querySelectorAll('.services input');
services.forEach(s => s.addEventListener('change', calcTotal));
function calcTotal(){let total=0;services.forEach(s=>{if(s.checked)total+=Number(s.value)});document.getElementById('total').innerText=total;}
function generateCode(){return 'MM'+Math.floor(100000+Math.random()*900000)}
function book(){
const name=document.getElementById('name').value;
const phone=document.getElementById('phone').value;
const time=document.getElementById('datetime').value;
const total=document.getElementById('total').innerText;
if(!name||!phone||!time){alert('اكمل البيانات');return}
if(banned.includes(phone)){alert('هذا الرقم محظور من الحجز');return}
if(bookings.find(b=>b.time===time)){alert('هذا الموعد محجوز بالفعل');return}
const code=generateCode();
bookings.push({name,phone,time,total,code});
render();
makeQR(code);
// فتح واتساب لإرسال رسالة
const shopWhats='905384591803';
const msg=`حجز جديد في إدارة الحلاقة محمود علي موسى%0Aالاسم: ${name}%0Aالهاتف: ${phone}%0Aالموعد: ${time}%0Aالإجمالي: ${total}%0Aكود الحجز: ${code}`;
window.open(`https://wa.me/${shopWhats}?text=${msg}`,'_blank');
}
function render(){
const list=document.getElementById('list');
list.innerHTML='';
bookings.forEach((b,i)=>{
list.innerHTML+=`<tr><td>${i+1}</td><td>${b.name}</td><td>${b.phone}</td><td>${b.time}</td><td>${b.total}</td><td class='barcode'>${b.code}</td><td><button class='danger' onclick='cancel(${i})'>إلغاء</button> <button onclick='ban("${b.phone}")'>حظر</button> <button onclick='printBill(${i})'>فاتورة</button></td></tr>`;
});
}
function cancel(i){bookings.splice(i,1);render()}
function ban(phone){banned.push(phone);alert('تم حظر الزبون')}
function printBill(i){const b=bookings[i];const w=window.open('');w.document.write(`<h2>فاتورة – إدارة الحلاقة محمود علي موسى</h2><p>الاسم: ${b.name}</p><p>الهاتف: ${b.phone}</p><p>الوقت: ${b.time}</p><p>الإجمالي: ${b.total}</p><p>كود الحجز: ${b.code}</p>`);w.print()}
function makeQR(code){document.getElementById('qr').innerHTML='';new QRCode(document.getElementById('qr'),{text:code,width:180,height:180})}
function addComplaint(){let t=document.getElementById('complaint').value.trim();if(!t)return;complains.push({text:t,date:new Date().toLocaleString()});document.getElementById('complaint').value='';showComplaints()}
function showComplaints(){const c=document.getElementById('complaints');c.innerHTML='<h4>آخر الملاحظات</h4>';complains.slice(-5).forEach(s=>{c.innerHTML+=`<div style="background:#eee;padding:5px;margin:3px;border-radius:5px">${s.text}<br><small>${s.date}</small></div>`})}
</script></body>
</html>
