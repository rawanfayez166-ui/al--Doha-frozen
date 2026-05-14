# Al-dohafrozen<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مجمدات آل ضحى - المنيو الشامل</title>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Cairo', sans-serif; background-color: #0a0a0a; color: white; margin: 0; padding: 20px; padding-bottom: 160px; }
        .header { text-align: center; padding: 20px; border-bottom: 2px solid #d4af37; margin-bottom: 20px; position: sticky; top: 0; background-color: #0a0a0a; z-index: 100; }
        .header h1 { color: #d4af37; margin: 0; font-size: 2.2em; cursor: pointer; }
        .category-title { color: #d4af37; border-right: 4px solid #d4af37; padding-right: 10px; margin: 30px 0 15px; font-size: 1.4em; background: #111; padding: 5px 10px; }
        .product-card { background: #1a1a1a; border: 1px solid #333; padding: 15px; margin-bottom: 12px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; }
        .add-btn { background: #d4af37; border: none; padding: 10px 18px; border-radius: 8px; font-weight: bold; cursor: pointer; color: #000; }
        .cart-section { background: #111; padding: 15px; border-radius: 15px; position: fixed; bottom: 10px; left: 10px; right: 10px; border: 1px solid #d4af37; z-index: 90; box-shadow: 0 -5px 25px rgba(0,0,0,0.9); }
        .customer-input { width: 100%; padding: 12px; margin-bottom: 10px; border-radius: 8px; border: 1px solid #d4af37; background: #000; color: white; text-align: center; font-family: 'Cairo'; box-sizing: border-box; }
        .whatsapp-btn { background: #25D366; color: white; text-decoration: none; display: block; text-align: center; padding: 14px; border-radius: 10px; font-weight: bold; font-size: 1.1em; }
        .added-mark { color: #d4af37; margin-right: 8px; display: none; }
        
        #admin-panel { display: none; position: fixed; top: 5%; left: 5%; right: 5%; bottom: 5%; background: #151515; border: 2px solid #d4af37; padding: 15px; border-radius: 15px; z-index: 1000; overflow-y: auto; }
        .order-log { background: #222; padding: 10px; margin-bottom: 10px; border-radius: 8px; border-right: 4px solid #25D366; font-size: 0.85em; }
        .admin-btn { background: #d4af37; color: black; border: none; padding: 10px; margin: 5px 0; border-radius: 5px; font-weight: bold; width: 100%; cursor: pointer; font-family: 'Cairo'; }
    </style>
</head>
<body>

<div id="admin-panel">
    <h2 style="color: #d4af37; text-align: center;">سجل مبيعات آل ضحى 📋</h2>
    <div id="stats-summary" style="text-align: center; margin-bottom: 15px; color: #aaa;"></div>
    <div id="orders-list"></div>
    <button class="admin-btn" onclick="setActivePhone('201228937888')">تفعيل شفت 1 (888)</button>
    <button class="admin-btn" onclick="setActivePhone('201551818888')">تفعيل شفت 2 (8888)</button>
    <button class="admin-btn" style="background: #ff4444; color: white;" onclick="toggleAdmin()">إغلاق السجل</button>
</div>

<div class="header">
    <h1 onclick="handleHeaderClick()">مجمدات آل ضحى</h1>
    <p>❄️ الطعم الأصلي والجودة المضمونة ❄️</p>
</div>

<div id="menu"></div>

<div class="cart-section">
    <input type="text" id="cust-info" class="customer-input" placeholder="اكتبي اسمك أو رقمك (مطلوب) ✍️">
    <div style="text-align: center; margin-bottom: 8px; font-size: 0.95em;">الأصناف المختارة: <span id="item-count" style="color: #d4af37; font-weight: bold;">0</span></div>
    <a href="#" onclick="sendWhatsApp()" class="whatsapp-btn">تأكيد الطلب وإرسال 💬</a>
</div>

<script>
    const products = [
        { cat: "🐔 عروض التوفير (أحجام كبيرة)", items: ["🍗 أطياب بانية جامبو - 2 كجم", "🍟 أطياب استربس عائلي - 2 كجم", "📦 كوكى بانية توفير - 1.5 كجم", "🍱 كوكى وجبة (12 قطعة)"] },
        { cat: "🐔 قسم الدجاج والبانية", items: ["🍗 بانية أطياب (1 كجم)", "🍗 بانية كوكى (1 كجم)", "🍗 بانية شيكيتيتا", "🔥 استربس أطياب (حار/عادي)", "🔥 استربس كوكى", "✨ ناجتس أطياب", "🍢 شيش طاووق أطياب", "🍗 أوراك دجاج كوكى", "🍗 دبابيس دجاج كوكى", "🥚 كبد وقوانص فرز أول"] },
        { cat: "🐥 قسم الطيور", items: ["🦆 بط العابد (مجمد فاخر)", "🍗 طبق جوز فراخ (2 قطعة)", "🐦 سمان جامبو (طبق)"] },
        { cat: "🐟 قسم الرنجة والأسماك", items: ["🐟 رنجة هولندي (أنواع شركات)", "🐟 رنجة سوبر جامبو", "🍤 جمبري مقشر", "🐠 سمك فيليه باسا", "🦀 أصابع كابوريا"] },
        { cat: "🍔 قسم اللحوم (المدخن والمصنعات)", items: ["🌭 سوسيس (اللحيمي/ميتكو)", "🌭 سجق مدخن (هوت دوج)", "🍔 برجر حلواني إخوان", "🍔 برجر اللحيمي", "🌭 سجق شرقي سبيشيال", "🍢 كفتة سيخ روستيكا", "🥪 حواوشي بيتي"] },
        { cat: "🔥 قسم الصوصات", items: ["🍅 كاتشب الحريف", "🥚 مايونيز الحريف", "🧄 ثومية جاهزة", "🧀 صوص شيدر", "🍯 صوص باربيكيو"] },
        { cat: "🥦 الخضروات والفاكهة", items: ["🌿 ملوخية (بسمة/مولاتي)", "🎋 بامية (اكسترا/زيرو)", "🌽 بسلة بالجزر", "🥭 مانجو مجمدة (طبيعي)", "🍎 رمان مجمد", "🍓 فراولة مجمدة"] },
        { cat: "🍟 مقليات ومقبلات", items: ["🍟 بطاطس فارم فريتس 2.5ك", "🍟 بطاطس بوم فريت 1ك", "🥟 سمبوسة (جبنة/لحمة)", "🥐 جلاش السلام", "🥟 كبيبة شامي"] },
        { cat: "🧀 ألبان", items: ["🧀 جبنة موتزاريلا الحمد", "🧈 زبدة فيرن", "🥩 لانشون حلواني (أنواع)"] }
    ];

    let cart = [];
    let clickCount = 0;
    let activePhone = localStorage.getItem('activePhone') || '201228937888';

    const menuDiv = document.getElementById('menu');
    let globalIdx = 0;
    products.forEach(group => {
        menuDiv.innerHTML += `<h2 class="category-title">${group.cat}</h2>`;
        group.items.forEach(name => {
            menuDiv.innerHTML += `
                <div class="product-card">
                    <h3><span class="added-mark" id="mark-${globalIdx}">✅</span>${name}</h3>
                    <button class="add-btn" id="btn-${globalIdx}" onclick="addToCart('${name}', ${globalIdx})">إضافة +</button>
                </div>`;
            globalIdx++;
        });
    });

    function addToCart(name, index) {
        if (!cart.includes(name)) {
            cart.push(name);
            document.getElementById(`btn-${index}`).style.backgroundColor = "#444";
            document.getElementById(`btn-${index}`).innerText = "تم";
            document.getElementById(`mark-${index}`).style.display = "inline";
            document.getElementById('item-count').innerText = cart.length;
        }
    }

    function sendWhatsApp() {
        const custInfo = document.getElementById('cust-info').value;
        if (cart.length === 0) { alert("من فضلك اختاري بعض الأصناف أولاً"); return; }
        if (custInfo.trim() === "") { alert("يجب كتابة اسمك أو رقمك لنتمكن من تسجيل الطلب"); return; }

        const now = new Date();
        const timeStr = now.toLocaleTimeString('ar-EG');
        const dateStr = now.toLocaleDateString('ar-EG');

        let logs = JSON.parse(localStorage.getItem('orderLogs') || '[]');
        logs.unshift({ customer: custInfo, count: cart.length, time: timeStr, date: dateStr, details: cart.join(' - ') });
        localStorage.setItem('orderLogs', JSON.stringify(logs));

        let message = `طلب جديد من: ${custInfo}\nالوقت: ${timeStr}\n\nالأصناف:\n`;
        cart.forEach((item, i) => message += `${i+1}. ${item}\n`);
        
        window.location.href = `https://wa.me/${activePhone}?text=${encodeURIComponent(message)}`;
    }

    function handleHeaderClick() {
        clickCount++;
        if (clickCount === 5) { toggleAdmin(); clickCount = 0; }
    }

    function toggleAdmin() {
        const panel = document.getElementById('admin-panel');
        const isVisible = panel.style.display === 'block';
        panel.style.display = isVisible ? 'none' : 'block';
        
        if (!isVisible) {
            const logs = JSON.parse(localStorage.getItem('orderLogs') || '[]');
            const listDiv = document.getElementById('orders-list');
            listDiv.innerHTML = logs.map(log => `
                <div class="order-log">
                    🟢 <b>${log.customer}</b><br>
                    📦 ${log.count} أصناف | ⏰ ${log.time}<br>
                    <small style="color: #888">${log.details.substring(0, 50)}...</small>
                </div>
            `).join('');
            document.getElementById('stats-summary').innerText = `إجمالي الطلبات المسجلة: ${logs.length}`;
        }
    }

    function setActivePhone(phone) {
        activePhone = phone;
        localStorage.setItem('activePhone', phone);
        alert("تم تحويل الاستقبال للرقم الذي ينتهي بـ " + phone.slice(-4));
    }
</script>
</body>
</html>
