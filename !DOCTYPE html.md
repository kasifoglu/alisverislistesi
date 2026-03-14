<!DOCTYPE html>  
  
<html lang="tr">  
<head>  
<meta charset="UTF-8"/>  
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no"/>  
<title>Alışveriş Listesi</title>  
<link href="https://fonts.googleapis.com/css2?family=Nunito:wght@600;700;800;900&display=swap" rel="stylesheet"/>  
<style>  
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent;font-family:'Nunito',sans-serif;user-select:none;}  
body{margin:0;min-height:100vh;background:linear-gradient(160deg,#0f0c29,#302b63,#24243e);padding:20px 16px 120px;}  
input,textarea{user-select:text;-webkit-user-select:text;}  
input::placeholder,textarea::placeholder{color:rgba(255,255,255,0.3)}  
input:focus,textarea:focus{outline:none}  
@keyframes slideIn{from{opacity:0;transform:translateY(-8px)}to{opacity:1;transform:translateY(0)}}  
@keyframes fadeUp{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}  
.slide-in{animation:slideIn .25s ease forwards}  
::-webkit-scrollbar{width:3px}::-webkit-scrollbar-thumb{background:rgba(255,255,255,0.15);border-radius:2px}  
.swipe-wrapper{position:relative;border-radius:12px;margin-bottom:5px;overflow:hidden;}  
.swipe-bg{position:absolute;inset:0;border-radius:12px;display:flex;align-items:center;padding:0 18px;font-weight:800;pointer-events:none;opacity:0;}  
.swipe-bg-right{background:linear-gradient(90deg,#1a6b3a,#27ae60);justify-content:flex-start;color:#fff;gap:8px;font-size:13px;}  
.swipe-bg-left{background:linear-gradient(90deg,#7b1a1a,#c0392b);justify-content:flex-end;color:#fff;gap:8px;font-size:13px;}  
.swipe-item{position:relative;z-index:1;padding:10px 14px;border-radius:12px;background:rgba(255,255,255,0.04);cursor:pointer;display:flex;align-items:center;gap:10px;border:1px solid transparent;will-change:transform;touch-action:pan-y;}  
#suggestions{position:absolute;left:0;right:0;top:calc(100% + 6px);background:rgba(20,15,50,0.97);backdrop-filter:blur(24px);border:1px solid rgba(255,255,255,0.12);border-radius:14px;overflow:hidden;z-index:200;animation:fadeUp .15s ease;box-shadow:0 8px 32px rgba(0,0,0,0.5);}  
.sug-item{padding:11px 16px;display:flex;align-items:center;gap:10px;cursor:pointer;border-bottom:1px solid rgba(255,255,255,0.05);}  
.sug-item:last-child{border-bottom:none;}  
.sug-item:active{background:rgba(255,255,255,0.1);}  
.sug-name{color:#fff;font-size:14px;font-weight:700;flex:1;}  
.sug-match{color:#43e97b;font-weight:900;}  
.sug-cat{font-size:11px;color:rgba(255,255,255,0.35);font-weight:700;}  
.bought-chip{padding:6px 10px 6px 12px;border-radius:99px;background:rgba(255,255,255,0.04);border:1px solid rgba(255,255,255,0.07);display:flex;align-items:center;gap:6px;}  
.restore-btn{width:22px;height:22px;border-radius:99px;border:none;cursor:pointer;background:rgba(255,255,255,0.1);color:rgba(255,255,255,0.5);font-size:12px;display:flex;align-items:center;justify-content:center;flex-shrink:0;padding:0;touch-action:manipulation;}  
button{touch-action:manipulation;-webkit-tap-highlight-color:transparent;}  
</style>  
</head>  
<body>  
<div id="app">  
<div style="text-align:center;margin-bottom:22px">  
<div style="font-size:42px">🛒</div>  
<h1 style="margin:4px 0 0;color:#fff;font-size:26px;font-weight:900">Alışveriş Listesi</h1>  
<div id="progress-bar" style="display:none;max-width:300px;margin:12px auto 0">  
<div style="display:flex;justify-content:space-between;color:rgba(255,255,255,0.45);font-size:12px;font-weight:700;margin-bottom:6px">  
<span id="bought-count">✓ 0 alındı</span><span id="pct-text">0%</span><span id="waiting-count">0 bekliyor</span>  
</div>  
<div style="background:rgba(255,255,255,0.1);border-radius:99px;height:6px">  
<div id="progress-fill" style="height:100%;width:0%;background:linear-gradient(90deg,#43e97b,#38f9d7);border-radius:99px;transition:width .5s"></div>  
</div>  
</div>  
</div>  
<div style="display:flex;gap:10px;margin-bottom:10px;">  
<div style="flex:1;position:relative;">  
<input id="item-input" type="text" autocomplete="off" placeholder="Ürün ekle..."  
style="width:100%;padding:13px 16px;border-radius:16px;border:1.5px solid rgba(255,255,255,0.15);background:rgba(255,255,255,0.08);color:#fff;font-size:15px;font-weight:600;"/>  
<div id="suggestions" style="display:none;"></div>  
</div>  
<button id="add-btn"  
style="width:50px;height:50px;border-radius:16px;border:none;cursor:pointer;background:rgba(255,255,255,0.15);font-size:24px;font-weight:900;color:#1a1a2e;flex-shrink:0;line-height:1">+</button>  
</div>  
<div style="margin-bottom:20px;">  
<button id="bulk-toggle"  
style="width:100%;padding:14px 16px;border-radius:14px;border:1.5px dashed rgba(255,255,255,0.3);background:rgba(255,255,255,0.05);color:rgba(255,255,255,0.6);font-size:13px;font-weight:700;cursor:pointer;display:flex;align-items:center;justify-content:center;gap:8px;">  
<span>📋</span><span>Listeyi buraya yapıştır</span><span id="bulk-arrow">▾</span>  
</button>  
<div id="bulk-area" style="display:none;margin-top:8px;">  
<textarea id="bulk-input" rows="5"  
style="width:100%;padding:14px 16px;border-radius:16px;border:1.5px solid rgba(255,255,255,0.15);background:rgba(255,255,255,0.06);color:#fff;font-size:14px;font-weight:600;resize:none;font-family:'Nunito',sans-serif;line-height:1.6;"  
placeholder="Listeyi buraya yapıştır veya yaz..."></textarea>  
<button id="bulk-btn"  
style="margin-top:8px;width:100%;padding:13px;border-radius:14px;border:none;cursor:pointer;background:linear-gradient(135deg,#a78bfa,#818cf8);color:#fff;font-size:14px;font-weight:800;font-family:'Nunito',sans-serif;">  
✨ Listeyi Analiz Et ve Ekle  
</button>  
</div>  
</div>  
<div id="groups"></div>  
<div id="bought-section" style="display:none;margin-top:16px">  
<div style="display:flex;align-items:center;gap:8px;margin-bottom:8px">  
<div style="flex:1;height:1px;background:rgba(255,255,255,0.08)"></div>  
<span style="color:rgba(255,255,255,0.3);font-size:11px;font-weight:800;letter-spacing:1px">✓ ALINANLAR</span>  
<div style="flex:1;height:1px;background:rgba(255,255,255,0.08)"></div>  
</div>  
<p style="color:rgba(255,255,255,0.2);font-size:11px;font-weight:700;margin:0 0 10px;text-align:center">↩ butonuna bas → geri listeye ekle</p>  
<div id="bought-list" style="display:flex;flex-wrap:wrap;gap:8px"></div>  
</div>  
<div id="empty-state" style="text-align:center;padding:60px 20px">  
<div style="font-size:64px;opacity:.2">🧺</div>  
<p style="color:rgba(255,255,255,0.25);font-size:15px;font-weight:700;margin-top:12px">Liste boş!<br>Ürün ekleyerek başla.</p>  
</div>  
<div id="reset-btn-wrap" style="display:none;position:fixed;bottom:24px;left:50%;transform:translateX(-50%);z-index:20">  
<button id="reset-btn"  
style="padding:13px 32px;border-radius:99px;border:1.5px solid rgba(255,100,100,0.4);background:rgba(15,5,5,0.85);color:#ff7070;font-size:14px;font-weight:800;cursor:pointer;box-shadow:0 8px 30px rgba(0,0,0,0.5);">  
🔄 Alışverişi Sıfırla  
</button>  
</div>  
</div>  
<script>  
var CAT={  
"Süt Ürünleri":{b:"#F0C040",i:"🧀"},  
"Et & Balık":{b:"#E88080",i:"🥩"},  
"Sebze & Meyve":{b:"#68D391",i:"🥦"},  
"Fırın & Ekmek":{b:"#F6AD55",i:"🥖"},  
"İçecekler":{b:"#63B3ED",i:"🧃"},  
"Temizlik":{b:"#9F7AEA",i:"🧹"},  
"Kişisel Bakım":{b:"#F687B3",i:"🧴"},  
"Atıştırmalık":{b:"#FFA07A",i:"🍿"},  
"Dondurulmuş":{b:"#76D7EA",i:"🧊"},  
"Bakliyat":{b:"#C8B880",i:"🌾"},  
"Diğer":{b:"#AAAAAA",i:"🛒"}  
};  
function trk(s){  
return s.toLowerCase()  
.replace(/ğ/g,"g").replace(/ü/g,"u").replace(/ş/g,"s")  
.replace(/ı/g,"i").replace(/ö/g,"o").replace(/ç/g,"c")  
.replace(/î/g,"i").replace(/â/g,"a").replace(/û/g,"u").trim();  
}  
function rgb(hex){  
var r=/^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);  
return r?parseInt(r[1],16)+","+parseInt(r[2],16)+","+parseInt(r[3],16):"255,255,255";  
}  
function lev(a,b){  
var m=a.length,n=b.length;  
if(Math.abs(m-n)>3)return 99;  
var R=[];  
for(var i=0;i<=m;i++){R[i]=[];for(var j=0;j<=n;j++)R[i][j]=i===0?j:j===0?i:0;}  
for(var i=1;i<=m;i++)for(var j=1;j<=n;j++)  
R[i][j]=a[i-1]===b[j-1]?R[i-1][j-1]:1+Math.min(R[i-1][j],R[i][j-1],R[i-1][j-1]);  
return R[m][n];  
}  
var DB=[  
{n:"Süt",c:"Süt Ürünleri"},{n:"Yoğurt",c:"Süt Ürünleri"},{n:"Süzme Yoğurt",c:"Süt Ürünleri"},  
{n:"Ayran",c:"Süt Ürünleri"},{n:"Kefir",c:"Süt Ürünleri"},{n:"Peynir",c:"Süt Ürünleri"},  
{n:"Beyaz Peynir",c:"Süt Ürünleri"},{n:"Kaşar Peyniri",c:"Süt Ürünleri"},{n:"Tulum Peyniri",c:"Süt Ürünleri"},  
{n:"Lor Peyniri",c:"Süt Ürünleri"},{n:"Labne",c:"Süt Ürünleri"},{n:"Mozarella",c:"Süt Ürünleri"},  
{n:"Çökelek",c:"Süt Ürünleri"},{n:"Hellim",c:"Süt Ürünleri"},{n:"Gravyer",c:"Süt Ürünleri"},  
{n:"Parmesan",c:"Süt Ürünleri"},{n:"Cheddar",c:"Süt Ürünleri"},{n:"Gouda",c:"Süt Ürünleri"},  
{n:"Mascarpone",c:"Süt Ürünleri"},{n:"Tereyağı",c:"Süt Ürünleri"},{n:"Kaymak",c:"Süt Ürünleri"},  
{n:"Krema",c:"Süt Ürünleri"},{n:"Krem Şanti",c:"Süt Ürünleri"},{n:"Yumurta",c:"Süt Ürünleri"},  
{n:"Köy Yumurtası",c:"Süt Ürünleri"},  
{n:"Tavuk",c:"Et & Balık"},{n:"Tavuk Göğsü",c:"Et & Balık"},{n:"Tavuk But",c:"Et & Balık"},  
{n:"Tavuk Kanat",c:"Et & Balık"},{n:"Kıyma",c:"Et & Balık"},{n:"Dana Eti",c:"Et & Balık"},  
{n:"Kuzu Eti",c:"Et & Balık"},{n:"Hindi",c:"Et & Balık"},{n:"Sucuk",c:"Et & Balık"},  
{n:"Sosis",c:"Et & Balık"},{n:"Pastırma",c:"Et & Balık"},{n:"Salam",c:"Et & Balık"},  
{n:"Jambon",c:"Et & Balık"},{n:"Köfte",c:"Et & Balık"},{n:"Kavurma",c:"Et & Balık"},  
{n:"Balık",c:"Et & Balık"},{n:"Hamsi",c:"Et & Balık"},{n:"Levrek",c:"Et & Balık"},  
{n:"Çipura",c:"Et & Balık"},{n:"Somon",c:"Et & Balık"},{n:"Alabalık",c:"Et & Balık"},  
{n:"Uskumru",c:"Et & Balık"},{n:"Ton Balığı",c:"Et & Balık"},{n:"Sardalya",c:"Et & Balık"},  
{n:"Karides",c:"Et & Balık"},{n:"Midye",c:"Et & Balık"},{n:"Ahtapot",c:"Et & Balık"},  
{n:"Domates",c:"Sebze & Meyve"},{n:"Salatalık",c:"Sebze & Meyve"},{n:"Biber",c:"Sebze & Meyve"},  
{n:"Kırmızı Biber",c:"Sebze & Meyve"},{n:"Yeşil Biber",c:"Sebze & Meyve"},{n:"Patlıcan",c:"Sebze & Meyve"},  
{n:"Patates",c:"Sebze & Meyve"},{n:"Tatlı Patates",c:"Sebze & Meyve"},{n:"Soğan",c:"Sebze & Meyve"},  
{n:"Taze Soğan",c:"Sebze & Meyve"},{n:"Sarımsak",c:"Sebze & Meyve"},{n:"Havuç",c:"Sebze & Meyve"},  
{n:"Marul",c:"Sebze & Meyve"},{n:"Kıvırcık Marul",c:"Sebze & Meyve"},{n:"Ispanak",c:"Sebze & Meyve"},  
{n:"Kabak",c:"Sebze & Meyve"},{n:"Brokoli",c:"Sebze & Meyve"},{n:"Karnabahar",c:"Sebze & Meyve"},  
{n:"Mantar",c:"Sebze & Meyve"},{n:"Şampinyon",c:"Sebze & Meyve"},{n:"Kereviz",c:"Sebze & Meyve"},  
{n:"Turp",c:"Sebze & Meyve"},{n:"Pancar",c:"Sebze & Meyve"},{n:"Enginar",c:"Sebze & Meyve"},  
{n:"Bamya",c:"Sebze & Meyve"},{n:"Taze Fasulye",c:"Sebze & Meyve"},{n:"Pırasa",c:"Sebze & Meyve"},  
{n:"Roka",c:"Sebze & Meyve"},{n:"Semizotu",c:"Sebze & Meyve"},{n:"Tere",c:"Sebze & Meyve"},  
{n:"Maydanoz",c:"Sebze & Meyve"},{n:"Dereotu",c:"Sebze & Meyve"},{n:"Nane",c:"Sebze & Meyve"},  
{n:"Fesleğen",c:"Sebze & Meyve"},{n:"Kişniş",c:"Sebze & Meyve"},{n:"Rezene",c:"Sebze & Meyve"},  
{n:"Lahana",c:"Sebze & Meyve"},{n:"Mor Lahana",c:"Sebze & Meyve"},{n:"Kırmızı Lahana",c:"Sebze & Meyve"},  
{n:"Beyaz Lahana",c:"Sebze & Meyve"},{n:"Pazı",c:"Sebze & Meyve"},{n:"Balkabağı",c:"Sebze & Meyve"},  
{n:"Mısır",c:"Sebze & Meyve"},{n:"Bezelye",c:"Sebze & Meyve"},{n:"Elma",c:"Sebze & Meyve"},  
{n:"Muz",c:"Sebze & Meyve"},{n:"Portakal",c:"Sebze & Meyve"},{n:"Mandalina",c:"Sebze & Meyve"},  
{n:"Limon",c:"Sebze & Meyve"},{n:"Armut",c:"Sebze & Meyve"},{n:"Çilek",c:"Sebze & Meyve"},  
{n:"Üzüm",c:"Sebze & Meyve"},{n:"Kiraz",c:"Sebze & Meyve"},{n:"Vişne",c:"Sebze & Meyve"},  
{n:"Karpuz",c:"Sebze & Meyve"},{n:"Kavun",c:"Sebze & Meyve"},{n:"Ananas",c:"Sebze & Meyve"},  
{n:"Avokado",c:"Sebze & Meyve"},{n:"Şeftali",c:"Sebze & Meyve"},{n:"Kayısı",c:"Sebze & Meyve"},  
{n:"Erik",c:"Sebze & Meyve"},{n:"Nar",c:"Sebze & Meyve"},{n:"Kivi",c:"Sebze & Meyve"},  
{n:"Mango",c:"Sebze & Meyve"},{n:"Greyfurt",c:"Sebze & Meyve"},{n:"İncir",c:"Sebze & Meyve"},  
{n:"Ekmek",c:"Fırın & Ekmek"},{n:"Tam Buğday Ekmeği",c:"Fırın & Ekmek"},{n:"Çavdar Ekmeği",c:"Fırın & Ekmek"},  
{n:"Tost Ekmeği",c:"Fırın & Ekmek"},{n:"Baget",c:"Fırın & Ekmek"},{n:"Simit",c:"Fırın & Ekmek"},  
{n:"Poğaça",c:"Fırın & Ekmek"},{n:"Börek",c:"Fırın & Ekmek"},{n:"Pide",c:"Fırın & Ekmek"},  
{n:"Lavaş",c:"Fırın & Ekmek"},{n:"Pasta",c:"Fırın & Ekmek"},{n:"Kek",c:"Fırın & Ekmek"},  
{n:"Kurabiye",c:"Fırın & Ekmek"},{n:"Bisküvi",c:"Fırın & Ekmek"},{n:"Kraker",c:"Fırın & Ekmek"},  
{n:"Su",c:"İçecekler"},{n:"Maden Suyu",c:"İçecekler"},{n:"Kola",c:"İçecekler"},  
{n:"Gazoz",c:"İçecekler"},{n:"Meyve Suyu",c:"İçecekler"},{n:"Çay",c:"İçecekler"},  
{n:"Kahve",c:"İçecekler"},{n:"Nescafe",c:"İçecekler"},{n:"Türk Kahvesi",c:"İçecekler"},  
{n:"Bira",c:"İçecekler"},{n:"Şarap",c:"İçecekler"},{n:"Limonata",c:"İçecekler"},  
{n:"Enerji İçeceği",c:"İçecekler"},  
{n:"Deterjan",c:"Temizlik"},{n:"Çamaşır Suyu",c:"Temizlik"},{n:"Çamaşır Deterjanı",c:"Temizlik"},  
{n:"Bulaşık Deterjanı",c:"Temizlik"},{n:"Bulaşık Sıvısı",c:"Temizlik"},{n:"Yumuşatıcı",c:"Temizlik"},  
{n:"Kağıt Havlu",c:"Temizlik"},{n:"Çöp Torbası",c:"Temizlik"},{n:"Temizlik Bezi",c:"Temizlik"},  
{n:"Sünger",c:"Temizlik"},{n:"WC Taşı",c:"Temizlik"},{n:"Paspas",c:"Temizlik"},  
{n:"Diş Macunu",c:"Kişisel Bakım"},{n:"Diş Fırçası",c:"Kişisel Bakım"},{n:"Şampuan",c:"Kişisel Bakım"},  
{n:"Saç Kremi",c:"Kişisel Bakım"},{n:"Saç Maskesi",c:"Kişisel Bakım"},{n:"Sabun",c:"Kişisel Bakım"},  
{n:"Duş Jeli",c:"Kişisel Bakım"},{n:"Tuvalet Kağıdı",c:"Kişisel Bakım"},{n:"Islak Mendil",c:"Kişisel Bakım"},  
{n:"El Kremi",c:"Kişisel Bakım"},{n:"Yüz Kremi",c:"Kişisel Bakım"},{n:"Nemlendirici",c:"Kişisel Bakım"},  
{n:"Güneş Kremi",c:"Kişisel Bakım"},{n:"Deodorant",c:"Kişisel Bakım"},{n:"Parfüm",c:"Kişisel Bakım"},  
{n:"Vücut Losyonu",c:"Kişisel Bakım"},{n:"Serum",c:"Kişisel Bakım"},  
{n:"Çikolata",c:"Atıştırmalık"},{n:"Sütlü Çikolata",c:"Atıştırmalık"},{n:"Bitter Çikolata",c:"Atıştırmalık"},  
{n:"Cips",c:"Atıştırmalık"},{n:"Gofret",c:"Atıştırmalık"},{n:"Fındık",c:"Atıştırmalık"},  
{n:"Fıstık",c:"Atıştırmalık"},{n:"Badem",c:"Atıştırmalık"},{n:"Ceviz",c:"Atıştırmalık"},  
{n:"Kaju",c:"Atıştırmalık"},{n:"Granola",c:"Atıştırmalık"},{n:"Hurma",c:"Atıştırmalık"},  
{n:"Lokum",c:"Atıştırmalık"},{n:"Keçiboynuzu",c:"Atıştırmalık"},{n:"Leblebi",c:"Atıştırmalık"},  
{n:"Kuru Üzüm",c:"Atıştırmalık"},{n:"Kuru Kayısı",c:"Atıştırmalık"},{n:"Kuru İncir",c:"Atıştırmalık"},  
{n:"Popcorn",c:"Atıştırmalık"},{n:"Mısır Gevreği",c:"Atıştırmalık"},  
{n:"Dondurma",c:"Dondurulmuş"},{n:"Pizza",c:"Dondurulmuş"},{n:"Nugget",c:"Dondurulmuş"},  
{n:"Hazır Yemek",c:"Dondurulmuş"},{n:"Dondurulmuş Sebze",c:"Dondurulmuş"},  
{n:"Mercimek",c:"Bakliyat"},{n:"Nohut",c:"Bakliyat"},{n:"Fasulye",c:"Bakliyat"},  
{n:"Barbunya",c:"Bakliyat"},{n:"Pirinç",c:"Bakliyat"},{n:"Bulgur",c:"Bakliyat"},  
{n:"Makarna",c:"Bakliyat"},{n:"Un",c:"Bakliyat"},{n:"Yulaf",c:"Bakliyat"},  
{n:"Zeytinyağı",c:"Bakliyat"},{n:"Ayçiçek Yağı",c:"Bakliyat"},{n:"Sıvı Yağ",c:"Bakliyat"},  
{n:"Sirke",c:"Bakliyat"},{n:"Elma Sirkesi",c:"Bakliyat"},{n:"Salça",c:"Bakliyat"},  
{n:"Domates Salçası",c:"Bakliyat"},{n:"Ketçap",c:"Bakliyat"},{n:"Mayonez",c:"Bakliyat"},  
{n:"Bal",c:"Bakliyat"},{n:"Reçel",c:"Bakliyat"},{n:"Tuz",c:"Bakliyat"},  
{n:"Zeytin",c:"Bakliyat"},{n:"Yeşil Zeytin",c:"Bakliyat"},{n:"Siyah Zeytin",c:"Bakliyat"},  
{n:"Turşu",c:"Bakliyat"},{n:"Tahin",c:"Bakliyat"},{n:"Pekmez",c:"Bakliyat"}  
];  
var DBNORM=DB.map(function(x){return {n:x.n,c:x.c,k:trk(x.n)};});  
var KW={  
"Süt Ürünleri":["süt","yoğurt","peynir","kaşar","tulum","mozarella","lor","labne","çökelek","tereyağı","kaymak","kefir","ayran","krema","krem şanti","mascarpone","yumurta","hellim","gravyer","parmesan","cheddar","gouda","süzme yoğurt","köy yumurtası"],  
"Et & Balık":["et","tavuk","kıyma","balık","dana","kuzu","hindi","sucuk","sosis","pastırma","salam","jambon","bonfile","biftek","pirzola","köfte","kavurma","levrek","çipura","somon","hamsi","uskumru","sardalya","ton balığı","midye","karides","ahtapot","kalamar","alabalık","tavuk göğsü","tavuk but"],  
"Sebze & Meyve":["domates","salatalık","biber","kırmızı biber","yeşil biber","patlıcan","patates","tatlı patates","soğan","taze soğan","sarımsak","havuç","marul","kıvırcık marul","ıspanak","kabak","brokoli","karnabahar","mantar","şampinyon","kereviz","turp","pancar","enginar","bamya","taze fasulye","pırasa","roka","semizotu","tere","maydanoz","dereotu","nane","fesleğen","kişniş","rezene","lahana","mor lahana","kırmızı lahana","beyaz lahana","pazı","balkabağı","mısır","bezelye","elma","muz","portakal","mandalina","limon","armut","çilek","üzüm","kiraz","vişne","kavun","karpuz","ananas","avokado","şeftali","kayısı","erik","nar","kivi","mango","greyfurt","incir"],  
"Fırın & Ekmek":["ekmek","tam buğday ekmeği","çavdar ekmeği","tost ekmeği","simit","poğaça","börek","pasta","kek","kurabiye","kraker","bisküvi","pide","lavaş","baget","waffle","brownie","muffin"],  
"İçecekler":["su","maden suyu","soda","meyve suyu","kola","çay","kahve","nescafe","türk kahvesi","bira","şarap","limonata","enerji içeceği","gazoz","kombucha"],  
"Temizlik":["deterjan","çamaşır suyu","çamaşır deterjanı","bulaşık deterjanı","bulaşık sıvısı","yumuşatıcı","temizlik bezi","kağıt havlu","çöp torbası","sünger","wc taşı","paspas","tuvalet temizleyici","banyo temizleyici"],  
"Kişisel Bakım":["diş macunu","diş fırçası","şampuan","saç kremi","saç maskesi","sabun","duş jeli","tuvalet kağıdı","ıslak mendil","el kremi","yüz kremi","nemlendirici","güneş kremi","deodorant","parfüm","vücut losyonu","serum"],  
"Atıştırmalık":["çikolata","sütlü çikolata","bitter çikolata","cips","gofret","fındık","fıstık","badem","ceviz","kaju","granola","hurma","lokum","keçiboynuzu","leblebi","kuru üzüm","kuru kayısı","kuru incir","popcorn","mısır gevreği"],  
"Dondurulmuş":["dondurulmuş","dondurma","hazır yemek","pizza","nugget","donmuş","dondurulmuş sebze"],  
"Bakliyat":["mercimek","nohut","fasulye","barbunya","pirinç","bulgur","makarna","un","yulaf","zeytinyağı","ayçiçek yağı","sıvı yağ","sirke","elma sirkesi","salça","domates salçası","ketçap","mayonez","bal","reçel","tuz","zeytin","yeşil zeytin","siyah zeytin","turşu","tahin","pekmez"]  
};  
function categorize(raw){  
var input=trk(raw);  
var words=input.split(" ").filter(function(w){return w.length>=2;});  
var cats=Object.keys(KW);  
for(var ci=0;ci<cats.length;ci++){  
var list=KW[cats[ci]];  
for(var ki=0;ki<list.length;ki++){if(trk(list[ki])===input)return cats[ci];}  
}  
for(var ci=0;ci<cats.length;ci++){  
var list=KW[cats[ci]];  
for(var ki=0;ki<list.length;ki++){var k=trk(list[ki]);if(input.includes(k)||k.includes(input))return cats[ci];}  
}  
for(var ci=0;ci<cats.length;ci++){  
var list=KW[cats[ci]];  
for(var ki=0;ki<list.length;ki++){  
var k=trk(list[ki]);  
for(var wi=0;wi<words.length;wi++){if(words[wi].length>=3&&(k.includes(words[wi])||words[wi].includes(k)))return cats[ci];}  
}  
}  
var bestCat="Diğer",bestDist=99;  
for(var ci=0;ci<cats.length;ci++){  
var list=KW[cats[ci]];  
for(var ki=0;ki<list.length;ki++){  
var k=trk(list[ki]);var maxD=k.length<=5?1:k.length<=9?2:3;  
var d=lev(input,k);  
for(var wi=0;wi<words.length;wi++){if(words[wi].length>=4)d=Math.min(d,lev(words[wi],k));}  
if(d<=maxD&&d<bestDist){bestDist=d;bestCat=cats[ci];}  
}  
}  
return bestCat;  
}  
function saveData(){  
try{localStorage.setItem("ag",JSON.stringify(groups));localStorage.setItem("ab",JSON.stringify(bought));localStorage.setItem("au",String(uid));}catch(e){}  
}  
function loadData(){  
try{  
var g=localStorage.getItem("ag");var b=localStorage.getItem("ab");var u=localStorage.getItem("au");  
if(g)groups=JSON.parse(g);if(b)bought=JSON.parse(b);if(u)uid=parseInt(u)+1;  
}catch(e){}  
}  
var groups={},bought=[],uid=1,sugIndex=-1;  
loadData();  
function getSuggestions(val){  
if(!val||val.length<2)return[];  
var q=trk(val);  
var inList={};  
Object.values(groups).forEach(function(arr){arr.forEach(function(x){inList[trk(x.name)]=1;});});  
var results=[];  
for(var i=0;i<DBNORM.length;i++){  
var item=DBNORM[i];  
if(inList[item.k])continue;  
if(item.k===q){results.unshift(item);continue;}  
if(item.k.startsWith(q)||item.k.includes(q)){results.push(item);continue;}  
if(q.length>=3&&lev(q,item.k.slice(0,q.length+1))<=1)results.push(item);  
}  
return results.slice(0,6);  
}  
function renderSuggestions(val){  
var box=document.getElementById("suggestions");  
var sugs=getSuggestions(val);  
if(!sugs.length){box.style.display="none";sugIndex=-1;return;}  
var q=val.toLowerCase();  
box.innerHTML="";  
for(var i=0;i<sugs.length;i++){  
var item=sugs[i];  
var s=CAT[item.c]||CAT["Diğer"];  
var lname=item.n.toLowerCase();  
var mi=lname.indexOf(q);  
var label=mi>=0?item.n.slice(0,mi)+"<span class='sug-match'>"+item.n.slice(mi,mi+val.length)+"</span>"+item.n.slice(mi+val.length):item.n;  
var div=document.createElement("div");  
div.className="sug-item";  
div.innerHTML="<span style='font-size:16px'>"+s.i+"</span><span class='sug-name'>"+label+"</span><span class='sug-cat' style='color:"+s.b+"'>"+item.c+"</span>";  
(function(name){  
div.addEventListener("touchend",function(e){e.preventDefault();selectSug(name);});  
div.addEventListener("mousedown",function(e){e.preventDefault();selectSug(name);});  
})(item.n);  
box.appendChild(div);  
}  
box.style.display="block";  
}  
function selectSug(name){  
document.getElementById("item-input").value=name;  
document.getElementById("suggestions").style.display="none";  
sugIndex=-1;addItem();  
}  
var inp=document.getElementById("item-input");  
inp.addEventListener("input",function(){  
var btn=document.getElementById("add-btn");  
if(this.value.trim()){btn.style.background="linear-gradient(135deg,#43e97b,#38f9d7)";btn.style.boxShadow="0 4px 18px rgba(67,233,123,0.4)";}  
else{btn.style.background="rgba(255,255,255,0.15)";btn.style.boxShadow="none";}  
renderSuggestions(this.value);  
});  
inp.addEventListener("keydown",function(e){  
var box=document.getElementById("suggestions");  
var items=box.querySelectorAll(".sug-item");  
if(e.key==="ArrowDown"){e.preventDefault();sugIndex=Math.min(sugIndex+1,items.length-1);items.forEach(function(el,i){el.style.background=i===sugIndex?"rgba(255,255,255,0.08)":"";});}  
else if(e.key==="ArrowUp"){e.preventDefault();sugIndex=Math.max(sugIndex-1,-1);items.forEach(function(el,i){el.style.background=i===sugIndex?"rgba(255,255,255,0.08)":"";});}  
else if(e.key==="Enter"){  
if(sugIndex>=0&&items[sugIndex]){e.preventDefault();var name=items[sugIndex].querySelector(".sug-name").textContent;selectSug(name);}  
else addItem();  
}else if(e.key==="Escape"){box.style.display="none";sugIndex=-1;}  
else if(e.key==="Tab"&&items.length>0){e.preventDefault();var name=items[0].querySelector(".sug-name").textContent;selectSug(name);}  
});  
inp.addEventListener("blur",function(){setTimeout(function(){document.getElementById("suggestions").style.display="none";sugIndex=-1;},150);});  
function showToast(msg){  
var old=document.getElementById("toast");if(old)old.remove();  
var t=document.createElement("div");t.id="toast";t.textContent=msg;  
t.style.cssText="position:fixed;bottom:90px;left:50%;transform:translateX(-50%);background:rgba(30,20,70,0.95);color:#fff;padding:12px 24px;border-radius:99px;font-size:14px;font-weight:700;z-index:999;white-space:nowrap;pointer-events:none;";  
document.body.appendChild(t);  
setTimeout(function(){t.style.opacity="0";t.style.transition="opacity .3s";setTimeout(function(){t.remove();},300);},2500);  
}  
function addItem(){  
var text=inp.value.trim();if(!text)return;  
inp.value="";  
document.getElementById("suggestions").style.display="none";  
var cat=categorize(text);  
if(!groups[cat])groups[cat]=[];  
groups[cat].push({id:uid++,name:text});  
var btn=document.getElementById("add-btn");  
btn.style.background="linear-gradient(135deg,#43e97b,#38f9d7)";  
btn.style.boxShadow="0 4px 18px rgba(67,233,123,0.4)";  
saveData();render();inp.focus();  
}  
document.getElementById("bulk-toggle").addEventListener("click",function(){  
var area=document.getElementById("bulk-area");  
var arrow=document.getElementById("bulk-arrow");  
var open=area.style.display!=="none";  
area.style.display=open?"none":"block";  
arrow.textContent=open?"▾":"▴";  
if(!open)setTimeout(function(){document.getElementById("bulk-input").focus();},100);  
});  
document.getElementById("bulk-btn").addEventListener("click",function(){doBulkImport();});  
function doBulkImport(){  
var ta=document.getElementById("bulk-input");  
var rawText=ta.value.trim();  
if(!rawText)return;  
var btn=document.getElementById("bulk-btn");  
btn.textContent="Analiz ediliyor...";btn.disabled=true;  
var added=0;  
{  
var hasSep=false;  
for(var i=0;i<rawText.length;i++){  
var code=rawText.charCodeAt(i);  
if(code===10||code===13||rawText[i]===","||rawText[i]===";"){hasSep=true;break;}  
}  
var tokens=[];  
if(hasSep){  
var cur="";  
for(var i=0;i<rawText.length;i++){  
var code=rawText.charCodeAt(i);  
if(code===10||code===13||rawText[i]===","||rawText[i]===";"){  
if(cur.trim())tokens.push(cur.trim());cur="";  
}else cur+=rawText[i];  
}  
if(cur.trim())tokens.push(cur.trim());  
}else{  
var words=rawText.split(" ").map(function(w){return w.trim();}).filter(function(w){return w.length>0;});  
var used=new Array(words.length).fill(false);  
for(var len=3;len>=2;len--){  
for(var i=0;i<=words.length-len;i++){  
if(used[i])continue;  
var skip=false;for(var j=i;j<i+len;j++)if(used[j]){skip=true;break;}  
if(skip)continue;  
var cand=words.slice(i,i+len).join(" ");  
var candTrk=trk(cand);  
var found=false;  
for(var di=0;di<DBNORM.length;di++){if(DBNORM[di].k===candTrk){found=true;break;}}  
if(!found){var kwcats=Object.keys(KW);for(var ci=0;ci<kwcats.length;ci++){for(var ki=0;ki<KW[kwcats[ci]].length;ki++){if(trk(KW[kwcats[ci]][ki])===candTrk){found=true;break;}}if(found)break;}}  
if(found){tokens.push(cand);for(var j=i;j<i+len;j++)used[j]=true;}  
}  
}  
for(var i=0;i<words.length;i++){  
if(!used[i]){var w=words[i].replace(/^[0-9.\-* ]+/,"").trim();if(w.length>=2)tokens.push(w);}  
}  
}  
for(var i=0;i<tokens.length;i++){  
var line=tokens[i].replace(/^[0-9.\-* ]+/,"").trim();  
if(line.length<2||line.length>60)continue;  
var cat=categorize(line);  
if(!groups[cat])groups[cat]=[];  
groups[cat].push({id:uid++,name:line});added++;  
}  
}  
ta.value="";  
document.getElementById("bulk-area").style.display="none";  
document.getElementById("bulk-arrow").textContent="▾";  
btn.textContent="✨ Listeyi Analiz Et ve Ekle";btn.disabled=false;  
if(added>0){saveData();render();showToast("✅ "+added+" ürün eklendi!");}  
else showToast("⚠️ Ürün bulunamadı");  
}  
function moveItem(id){  
var cats=Object.keys(groups);  
for(var ci=0;ci<cats.length;ci++){  
var idx=groups[cats[ci]].findIndex(function(x){return x.id===id;});  
if(idx!==-1){var item=groups[cats[ci]][idx];groups[cats[ci]].splice(idx,1);if(!groups[cats[ci]].length)delete groups[cats[ci]];bought.push({id:item.id,name:item.name,category:cats[ci]});break;}  
}saveData();render();  
}  
function deleteItem(id){  
var cats=Object.keys(groups);  
for(var ci=0;ci<cats.length;ci++){  
var idx=groups[cats[ci]].findIndex(function(x){return x.id===id;});  
if(idx!==-1){groups[cats[ci]].splice(idx,1);if(!groups[cats[ci]].length)delete groups[cats[ci]];break;}  
}saveData();render();  
}  
function restoreItem(id){  
var idx=bought.findIndex(function(x){return x.id===id;});  
if(idx===-1)return;  
var item=bought[idx];bought.splice(idx,1);  
var cat=item.category||categorize(item.name);  
if(!groups[cat])groups[cat]=[];  
groups[cat].push({id:item.id,name:item.name});  
saveData();render();  
}  
function resetAll(){groups={};bought=[];saveData();render();}  
document.getElementById("reset-btn").addEventListener("click",resetAll);  
function attachSwipe(wrapper,itemEl,itemId){  
var sx=0,sy=0,cx=0,drag=false,dec=false;  
var bgR=wrapper.querySelector(".swipe-bg-right"),bgL=wrapper.querySelector(".swipe-bg-left");  
var TH=75;  
function start(x,y){sx=x;sy=y;cx=0;drag=true;dec=false;itemEl.style.transition="none";}  
function move(x,y){  
if(!drag)return;var dx=x-sx,dy=y-sy;  
if(!dec&&Math.abs(dy)>Math.abs(dx)&&Math.abs(dy)>10){drag=false;return;}  
if(Math.abs(dx)>8)dec=true;if(!dec)return;cx=dx;  
itemEl.style.transform="translateX("+dx+"px)";  
var r=Math.min(Math.abs(dx)/TH,1);  
if(dx>0){bgR.style.opacity=r;bgL.style.opacity=0;itemEl.style.background="rgba(39,174,96,"+(r*.2)+")";}  
else{bgL.style.opacity=r;bgR.style.opacity=0;itemEl.style.background="rgba(192,57,43,"+(r*.2)+")";}  
}  
function end(){  
if(!drag)return;drag=false;bgR.style.opacity=0;bgL.style.opacity=0;  
itemEl.style.transition="transform .28s ease,opacity .28s";  
if(cx>TH){itemEl.style.transform="translateX(115%)";itemEl.style.opacity="0";(function(id){setTimeout(function(){moveItem(id);},280);})(itemId);}  
else if(cx<-TH){itemEl.style.transform="translateX(-115%)";itemEl.style.opacity="0";(function(id){setTimeout(function(){deleteItem(id);},280);})(itemId);}  
else{itemEl.style.transform="translateX(0)";itemEl.style.background="rgba(255,255,255,0.04)";}  
}  
itemEl.addEventListener("touchstart",function(e){var t=e.touches[0];start(t.clientX,t.clientY);},{passive:true});  
itemEl.addEventListener("touchmove",function(e){var t=e.touches[0];move(t.clientX,t.clientY);if(dec)e.preventDefault();},{passive:false});  
itemEl.addEventListener("touchend",end);itemEl.addEventListener("touchcancel",end);  
itemEl.addEventListener("mousedown",function(e){start(e.clientX,e.clientY);});  
document.addEventListener("mousemove",function(e){if(drag)move(e.clientX,e.clientY);});  
document.addEventListener("mouseup",function(){if(drag)end();});  
}  
function render(){  
var ge=document.getElementById("groups"),bs=document.getElementById("bought-section"),  
bl=document.getElementById("bought-list"),es=document.getElementById("empty-state"),  
rw=document.getElementById("reset-btn-wrap"),pb=document.getElementById("progress-bar");  
var ti=Object.values(groups).reduce(function(a,x){return a+x.length;},0);  
var total=ti+bought.length;  
if(total>0){  
pb.style.display="block";var pct=Math.round(bought.length/total*100);  
document.getElementById("bought-count").textContent="✓ "+bought.length+" alındı";  
document.getElementById("pct-text").textContent=pct+"%";  
document.getElementById("waiting-count").textContent=ti+" bekliyor";  
document.getElementById("progress-fill").style.width=pct+"%";  
}else pb.style.display="none";  
es.style.display=total===0?"block":"none";  
rw.style.display=total>0?"block":"none";  
ge.innerHTML="";  
var cats=Object.keys(groups);  
for(var ci=0;ci<cats.length;ci++){  
var cat=cats[ci],items=groups[cat];  
var s=CAT[cat]||CAT["Diğer"],r=rgb(s.b);  
var gd=document.createElement("div");gd.className="slide-in";  
gd.style.cssText="margin-bottom:12px;background:rgba(255,255,255,0.04);border-radius:20px;overflow:hidden;border:1px solid rgba(255,255,255,0.07)";  
var hd=document.createElement("div");  
hd.style.cssText="padding:10px 16px;display:flex;align-items:center;gap:8px;background:rgba("+r+",.15);border-bottom:1px solid rgba(255,255,255,0.06)";  
hd.innerHTML="<span style='font-size:18px'>"+s.i+"</span><span style='color:"+s.b+";font-weight:800;font-size:12px;letter-spacing:1px;text-transform:uppercase'>"+cat+"</span><span style='margin-left:auto;background:"+s.b+";color:#fff;border-radius:99px;min-width:22px;height:22px;display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:800;padding:0 6px'>"+items.length+"</span>";  
gd.appendChild(hd);  
var iw=document.createElement("div");iw.style.padding="8px";  
for(var ii=0;ii<items.length;ii++){  
var item=items[ii];  
var wr=document.createElement("div");wr.className="swipe-wrapper slide-in";  
wr.innerHTML="<div class='swipe-bg swipe-bg-right'><span style='font-size:16px'>✅</span><span>Alındı</span></div><div class='swipe-bg swipe-bg-left'><span>Sil</span><span style='font-size:16px'>🗑️</span></div>";  
var ie=document.createElement("div");ie.className="swipe-item";  
ie.innerHTML="<div style='width:20px;height:20px;border-radius:6px;border:2px solid "+s.b+"80;flex-shrink:0;'></div><span style='color:rgba(255,255,255,0.85);font-size:14px;font-weight:700;flex:1'>"+item.name+"</span><span style='color:rgba(255,255,255,0.18);font-size:13px'>⇄</span>";  
wr.appendChild(ie);attachSwipe(wr,ie,item.id);iw.appendChild(wr);  
}  
gd.appendChild(iw);ge.appendChild(gd);  
}  
if(bought.length>0){  
bs.style.display="block";bl.innerHTML="";  
for(var bi=0;bi<bought.length;bi++){  
var item=bought[bi],s=CAT[item.category]||CAT["Diğer"];  
var chip=document.createElement("div");chip.className="bought-chip slide-in";  
chip.innerHTML="<span style='font-size:12px'>"+s.i+"</span><span style='color:rgba(255,255,255,0.3);font-size:13px;font-weight:600;text-decoration:line-through'>"+item.name+"</span><button class='restore-btn' data-id='"+item.id+"'>↩</button>";  
chip.querySelector(".restore-btn").addEventListener("click",function(){restoreItem(parseInt(this.getAttribute("data-id")));});  
bl.appendChild(chip);  
}  
}else bs.style.display="none";  
}  
document.getElementById("add-btn").addEventListener("click",addItem);  
render();  
</script>  
</body>  
</html>  
