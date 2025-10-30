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
