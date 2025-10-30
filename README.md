<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>نظام محاسبي - مصنع البلاستيك</title>
<link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<style>
body { font-family: 'Tajawal', sans-serif; background: #f3f6fb; }
.card { background: #fff; border-radius: 12px; box-shadow: 0 6px 24px rgba(20,20,50,0.06); padding: 1rem; margin-bottom: 1rem; }
.small { font-size:0.9rem; color:#374151; }
table th, table td { text-align:center; }
.accordion-header { cursor:pointer; }
</style>
</head>
<body class="p-6">

<!-- صفحة تسجيل الدخول -->
<div id="loginPage" class="flex justify-center items-center h-screen">
  <div class="card w-96 p-6">
    <h1 class="text-xl font-bold mb-4">تسجيل الدخول</h1>
    <label class="block small mb-1">اسم المستخدم</label>
    <input id="loginUser" type="text" class="border rounded p-1 w-full mb-3"/>
    <label class="block small mb-1">كلمة المرور</label>
    <input id="loginPass" type="password" class="border rounded p-1 w-full mb-3"/>
    <button onclick="login()" class="w-full bg-green-600 text-white p-2 rounded">دخول</button>
    <p id="loginError" class="text-red-600 mt-2 small"></p>
  </div>
</div>

<!-- الصفحة الرئيسية -->
<div id="mainPage" class="hidden">

<header class="mb-6">
  <h1 class="text-2xl font-bold">🏭 نظام محاسبي - مصنع البلاستيك</h1>
  <p class="text-sm text-gray-600">كل قسم مستقل: إنتاج، مخزون، مبيعات، مصروفات يومية، مصروفات شهرية، تقارير.</p>
  <button onclick="logout()" class="bg-red-600 text-white px-3 py-1 rounded mt-2">تسجيل خروج</button>
</header>

<!-- أزرار تحكم الأقسام -->
<div class="mb-4 flex gap-2 flex-wrap">
  <button onclick="toggleAll(false)" class="px-3 py-1 bg-gray-200 rounded">إخفاء الكل</button>
  <button onclick="toggleAll(true)" class="px-3 py-1 bg-indigo-600 text-white rounded">إظهار الكل</button>
  <button onclick="openOnly('dailyExpCard')" class="px-3 py-1 bg-red-600 text-white rounded">المصروفات اليومية</button>
  <button onclick="openOnly('monthlyExpCard')" class="px-3 py-1 bg-purple-600 text-white rounded">المصروفات الشهرية</button>
  <button onclick="openOnly('productionCard')" class="px-3 py-1 bg-green-600 text-white rounded">الإنتاج</button>
  <button onclick="openOnly('inventoryCard')" class="px-3 py-1 bg-blue-600 text-white rounded">المخزون</button>
  <button onclick="openOnly('salesCard')" class="px-3 py-1 bg-yellow-600 text-white rounded">المبيعات</button>
  <button onclick="openOnly('summaryCard')" class="px-3 py-1 bg-gray-700 text-white rounded">الملخص</button>
</div>

<section class="space-y-4">

<!-- المصروفات اليومية -->
<div id="dailyExpCard" class="card">
  <div class="flex justify-between items-center">
    <h2 class="font-semibold">🧾 المصروفات اليومية</h2>
    <div class="accordion-header text-sm text-gray-500" onclick="toggleCard('dailyExpCard')">إظهار/إخفاء</div>
  </div>
  <div id="dailyExpBody" class="mt-3">
    <p class="small mb-2">أسعار ثابتة: HD=2250/طن, LD=2600/طن, مجفف=160/وحدة, لون=140, سابك=2.5/كجم, تغليف=180, ديزل=50</p>
    <div class="grid lg:grid-cols-2 gap-3">
      <div>
        <label class="block small">HD (طن)</label>
        <input id="q_hd" type="number" min="0" step="0.01" value="0" class="border rounded p-1 w-full" />
        <label class="block small mt-2">LD (طن)</label>
        <input id="q_ld" type="number" min="0" step="0.01" value="0" class="border rounded p-1 w-full" />
        <label class="block small mt-2">مجفف (وحدة)</label>
        <input id="q_dryer" type="number" min="0" value="0" class="border rounded p-1 w-full" />
      </div>
      <div>
        <label class="block small">لون أسود (وحدة)</label>
        <input id="q_black" type="number" min="0" value="0" class="border rounded p-1 w-full" />
        <label class="block small mt-2">أكياس سابك (كجم)</label>
        <input id="q_sabic" type="number" min="0" step="0.1" value="0" class="border rounded p-1 w-full" />
        <label class="block small mt-2">أكياس تغليف (كيس)</label>
        <input id="q_pack" type="number" min="0" value="0" class="border rounded p-1 w-full" />
        <label class="block small mt-2">ديزل (يوم)</label>
        <input id="q_diesel" type="number" min="0" value="0" class="border rounded p-1 w-full" />
        <label class="block small mt-2">متفرقات (ريال)</label>
        <input id="q_misc_cost" type="number" min="0" value="0" class="border rounded p-1 w-full" />
      </div>
    </div>
    <div class="mt-3">
      <button onclick="saveDailyExpenses()" class="bg-yellow-600 text-white p-2 rounded">حفظ المصروفات اليومية</button>
      <div id="dailyExpResult" class="text-sm text-gray-700 mt-2"></div>
    </div>
  </div>
</div>

<!-- المصروفات الشهرية -->
<div id="monthlyExpCard" class="card">
  <div class="flex justify-between items-center">
    <h2 class="font-semibold">📆 المصروفات الشهرية</h2>
    <div class="accordion-header text-sm text-gray-500" onclick="toggleCard('monthlyExpCard')">إظهار/إخفاء</div>
  </div>
  <div id="monthlyExpBody" class="mt-3">
    <div class="grid lg:grid-cols-2 gap-3">
      <div>
        <label class="block small">كهرباء</label>
        <input id="m_electricity" type="number" min="0" value="3000" class="border rounded p-1 w-full" />
        <label class="block small mt-2">إيجار</label>
        <input id="m_rent" type="number" min="0" value="7600" class="border rounded p-1 w-full" />
      </div>
      <div>
        <label class="block small">رواتب 7 عمال (مفصولة بفاصلة)</label>
        <input id="m_salaries" type="text" class="border rounded p-1 w-full" value="2200,3000,2500,1800,1800,1400,1400" />
        <label class="block small mt-2">متفرقات</label>
        <input id="m_misc" type="number" min="0" value="0" class="border rounded p-1 w-full" />
      </div>
    </div>
    <div class="mt-3 flex gap-2">
      <button onclick="saveMonthlyExpenses()" class="bg-purple-600 text-white p-2 rounded">حفظ المصروفات الشهرية</button>
    </div>
  </div>
</div>

<!-- الإنتاج اليومي -->
<div id="productionCard" class="card">
  <div class="flex justify-between items-center">
    <h2 class="font-semibold">📦 الإنتاج اليومي (بنادل حسب الحجم)</h2>
    <div class="accordion-header text-sm text-gray-500" onclick="toggleCard('productionCard')">إظهار/إخفاء</div>
  </div>
  <div id="productionBody" class="mt-3">
    <div class="grid lg:grid-cols-3 gap-3">
      <div>
        <label class="block small">التاريخ</label>
        <input id="prodDate" type="date" class="border rounded p-1 w-full" />
        <label class="block small mt-2">الفترة</label>
        <select id="shift" class="border rounded p-1 w-full">
          <option value="صباحية">صباحية</option>
          <option value="مسائية">مسائية</option>
        </select>
      </div>
      <div>
        <label class="block small">ادخل البنادل لكل حجم</label>
        <div class="grid gap-2 mt-2">
          <div class="flex gap-2"><span class="w-28">30 جالون</span><input id="p_30" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
          <div class="flex gap-2"><span class="w-28">50 جالون</span><input id="p_50" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
          <div class="flex gap-2"><span class="w-28">55 جالون</span><input id="p_55" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
          <div class="flex gap-2"><span class="w-28">70 جالون</span><input id="p_70" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
          <div class="flex gap-2"><span class="w-28">80 جالون</span><input id="p_80" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
          <div class="flex gap-2"><span class="w-28">100 جالون</span><input id="p_100" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
          <div class="flex gap-2"><span class="w-28">متفرقات</span><input id="p_misc" type="number" min="0" value="0" class="border rounded p-1 w-full"></div>
        </div>
      </div>
      <div class="flex flex-col justify-between">
        <div>
          <p class="small">وزن البندل: <strong id="bundleWeight">8</strong> كجم</p>
          <p class="small">سعر البندل: <strong id="bundlePrice">27</strong> ريال</p>
        </div>
        <div class="space-y-2">
          <button onclick="saveProduction()" class="w-full bg-green-600 text-white p-2 rounded">حفظ الإنتاج</button>
          <button onclick="resetProdInputs()" class="w-full bg-gray-200 text-gray-800 p-2 rounded">مسح الحقول</button>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- المخزون -->
<div id="inventoryCard" class="card">
  <div class="flex justify-between items-center">
    <h2 class="font-semibold">📦 المخزون الحالي</h2>
    <div class="accordion-header text-sm text-gray-500" onclick="toggleCard('inventoryCard')">إظهار/إخفاء</div>
  </div>
  <div id="inventoryBody" class="mt-3">
    <table class="w-full">
      <thead><tr><th>الحجم</th><th>عدد البنادل</th><th>الوزن (كجم)</th></tr></thead>
      <tbody id="inventoryTable"></tbody>
    </table>
  </div>
</div>

<!-- المبيعات -->
<div id="salesCard" class="card">
  <div class="flex justify-between items-center">
    <h2 class="font-semibold">💸 المبيعات</h2>
    <div class="accordion-header text-sm text-gray-500" onclick="toggleCard('salesCard')">إظهار/إخفاء</div>
  </div>
  <div id="salesBody" class="mt-3">
    <div class="grid lg:grid-cols-3 gap-3">
      <div>
        <label class="block small">تاريخ البيع</label>
        <input id="saleDate" type="date" class="border rounded p-1 w-full"/>
        <label class="block small mt-2">الجهة</label>
        <select id="buyerType" class="border rounded p-1 w-full">
          <option value="مندوب_بدون">مندوب - بدون ضريبة</option>
          <option value="مندوب_بضريبة">مندوب - بضريبة 15%</option>
          <option value="مصنع">مصنع - بدون ضريبة</option>
        </select>
      </div>
      <div>
        <label class="block small">اختر الحجم</label>
        <select id="saleSize" class="border rounded p-1 w-full">
          <option value="30">30 جالون</option>
          <option value="50">50 جالون</option>
          <option value="55">55 جالون</option>
          <option value="70">70 جالون</option>
          <option value="80">80 جالون</option>
          <option value="100">100 جالون</option>
          <option value="misc">متفرقات</option>
        </select>
        <label class="block small mt-2">الكمية (بنادل)</label>
        <input id="saleQty" type="number" min="1" value="1" class="border rounded p-1 w-full" />
      </div>
      <div>
        <label class="block small">سعر الوحدة</label>
        <input id="unitPrice" type="number" min="0" value="27" class="border rounded p-1 w-full" />
        <button onclick="processSale()" class="mt-3 w-full bg-blue-600 text-white p-2 rounded">تنفيذ البيع</button>
        <div id="saleResult" class="text-sm text-gray-700 mt-2"></div>
      </div>
    </div>
  </div>
</div>

<!-- الملخص -->
<div id="summaryCard" class="card">
  <h2 class="font-semibold">📊 الملخص اليومي</h2>
  <div id="summaryBody" class="mt-3 grid lg:grid-cols-2 gap-3">
    <div class="card p-3">
      <div id="dailyExpSummary"></div>
    </div>
    <div class="card p-3">
      <div id="salesSummary"></div>
    </div>
  </div>
  <button onclick="exportToExcel()" class="mt-3 bg-green-700 text-white p-2 rounded">تصدير Excel</button>
</div>

</section>

<script>
/* ----------- تسجيل الدخول ------------- */
const USER = "admin", PASS="12345";
function login(){
  let u=document.getElementById('loginUser').value;
  let p=document.getElementById('loginPass').value;
  if(u===USER && p===PASS){
    document.getElementById('loginPage').classList.add('hidden');
    document.getElementById('mainPage').classList.remove('hidden');
  } else {
    document.getElementById('loginError').innerText="اسم المستخدم أو كلمة المرور غير صحيحة";
  }
}
function logout(){
  document.getElementById('mainPage').classList.add('hidden');
  document.getElementById('loginPage').classList.remove('hidden');
}

/* ----------- مصروفات يومية ------------- */
let dailyExpenses={};
function saveDailyExpenses(){
  dailyExpenses={
    HD:parseFloat(document.getElementById('q_hd').value)*2250,
    LD:parseFloat(document.getElementById('q_ld').value)*2600,
    Dryer:parseFloat(document.getElementById('q_dryer').value)*160,
    Black:parseFloat(document.getElementById('q_black').value)*140,
    Sabic:parseFloat(document.getElementById('q_sabic').value)*2.5,
    Pack:parseFloat(document.getElementById('q_pack').value)*180,
    Diesel:parseFloat(document.getElementById('q_diesel').value)*50,
    Misc:parseFloat(document.getElementById('q_misc_cost').value)
  };
  let total=Object.values(dailyExpenses).reduce((a,b)=>a+b,0);
  document.getElementById('dailyExpResult').innerText="المجموع اليومي: "+total.toFixed(2)+" ريال";
  updateSummary();
}

/* ----------- مصروفات شهرية ------------- */
let monthlyExpenses={};
function saveMonthlyExpenses(){
  let salaries=document.getElementById('m_salaries').value.split(',').map(x=>parseFloat(x));
  monthlyExpenses={
    electricity:parseFloat(document.getElementById('m_electricity').value),
    rent:parseFloat(document.getElementById('m_rent').value),
    salaries:salaries.reduce((a,b)=>a+b,0),
    misc:parseFloat(document.getElementById('m_misc').value)
  };
  alert("تم حفظ المصروفات الشهرية");
}

/* ----------- الإنتاج اليومي ------------- */
let production={};
function saveProduction(){
  production={
    date:document.getElementById('prodDate').value,
    shift:document.getElementById('shift').value,
    p30:parseInt(document.getElementById('p_30').value),
    p50:parseInt(document.getElementById('p_50').value),
    p55:parseInt(document.getElementById('p_55').value),
    p70:parseInt(document.getElementById('p_70').value),
    p80:parseInt(document.getElementById('p_80').value),
    p100:parseInt(document.getElementById('p_100').value),
    misc:parseInt(document.getElementById('p_misc').value)
  };
  updateInventory();
  alert("تم حفظ الإنتاج اليومي");
}
function resetProdInputs(){
  ['p_30','p_50','p_55','p_70','p_80','p_100','p_misc'].forEach(id=>document.getElementById(id).value=0);
}

/* ----------- المخزون ------------- */
let inventory={};
function updateInventory(){
  inventory={
    "30":production.p30,
    "50":production.p50,
    "55":production.p55,
    "70":production.p70,
    "80":production.p80,
    "100":production.p100,
    "misc":production.misc
  };
  let tbody='';
  Object.keys(inventory).forEach(k=>{
    let w=inventory[k]*8;
    tbody+=`<tr><td>${k}</td><td>${inventory[k]}</td><td>${w}</td></tr>`;
  });
  document.getElementById('inventoryTable').innerHTML=tbody;
  updateSummary();
}

/* ----------- المبيعات ------------- */
let sales=[];
function processSale(){
  let size=document.getElementById('saleSize').value;
  let qty=parseInt(document.getElementById('saleQty').value);
  let price=parseFloat(document.getElementById('unitPrice').value);
  if(inventory[size]===undefined || inventory[size]<qty){
    document.getElementById('saleResult').innerText="الكمية غير متوفرة في المخزون";
    return;
  }
  inventory[size]-=qty;
  sales.push({date:document.getElementById('saleDate').value,size,qty,price,buyer:document.getElementById('buyerType').value});
  document.getElementById('saleResult').innerText="تمت عملية البيع بنجاح";
  updateInventory();
}

/* ----------- الملخص ------------- */
function updateSummary(){
  let dailyTotal=Object.values(dailyExpenses).reduce((a,b)=>a+b,0);
  let salesTotal=sales.reduce((a,b)=>a+b.qty*b.price,0);
  document.getElementById('dailyExpSummary').innerHTML=`<h3 class="font-semibold">المصروفات اليومية</h3><p>المجموع: ${dailyTotal.toFixed(2)} ريال</p>`;
  document.getElementById('salesSummary').innerHTML=`<h3 class="font-semibold">المبيعات</h3><p>المجموع: ${salesTotal.toFixed(2)} ريال</p>`;
}

/* ----------- تصدير Excel ------------- */
function exportToExcel(){
  let wb=XLSX.utils.book_new();
  let ws_data=[["الحجم","العدد","الوزن"]];
  Object.keys(inventory).forEach(k=>ws_data.push([k,inventory[k],inventory[k]*8]));
  let ws=XLSX.utils.aoa_to_sheet(ws_data);
  XLSX.utils.book_append_sheet(wb,ws,"المخزون");
  XLSX.writeFile(wb,"factory.xlsx");
}

/* ----------- عناصر واجهة المستخدم ------------- */
function toggleCard(id){
  let body=document.getElementById(id+'Body');
  body.classList.toggle('hidden');
}
function toggleAll(show){
  ['dailyExpCard','monthlyExpCard','productionCard','inventoryCard','salesCard','summaryCard'].forEach(id=>{
    let body=document.getElementById(id+'Body');
    body.classList.toggle('hidden',!show);
  });
}
function openOnly(id){
  toggleAll(false);
  document.getElementById(id+'Body').classList.remove('hidden');
}
</script>

</body>
</html>
