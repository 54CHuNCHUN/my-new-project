<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Chun 財務手帳</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@200;400;500;700&display=swap');
        
        :root {
            --bg: #fdfaf5;
            --primary: #8c7662;
            --text: #37352f;
            --sub: #9b9a97;
            --card-bg: #ffffff;
            --red: #eb5757;
            --green: #2d6a4f;
            --hl-saving: #d8e2dc;
            --hl-fixed: #f1d5d5;
        }

        * { box-sizing: border-box; -webkit-font-smoothing: antialiased; }
        body { font-family: 'Noto Sans TC', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 0; overflow-x: hidden; }

        /* 開場漸進動畫 */
        #splash {
            position: fixed; inset: 0; background: var(--bg); z-index: 9999;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            transition: opacity 1s ease;
        }
        .splash-logo { font-size: 40px; font-weight: 200; color: var(--primary); letter-spacing: 15px; margin-bottom: 20px; animation: breathe 3s infinite; }
        @keyframes breathe { 0%, 100% { opacity: 0.3; } 50% { opacity: 1; } }

        /* 頁面切換 */
        .page { padding: 20px 24px 140px; max-width: 500px; margin: 0 auto; display: none; }
        .page.active { display: block; animation: pageIn 0.5s ease forwards; }
        @keyframes pageIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .brand { font-size: 28px; font-weight: 200; color: var(--primary); letter-spacing: 8px; text-align: center; margin: 40px 0 10px; }
        .quote { font-size: 11px; color: var(--sub); text-align: center; margin-bottom: 30px; font-style: italic; }

        /* 儀表板 */
        .dashboard {
            background: var(--primary); color: white; padding: 30px 24px;
            border-bottom-left-radius: 30px; border-bottom-right-radius: 30px;
            margin-bottom: 25px; box-shadow: 0 10px 25px rgba(140,118,98,0.15);
        }
        .dash-main-val { font-size: 36px; font-weight: 200; text-align: center; margin: 10px 0 20px; }
        .dash-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; text-align: center; }
        .dash-label { font-size: 9px; opacity: 0.7; }
        .dash-val { font-size: 12px; font-weight: 700; }

        .section-title { font-size: 11px; font-weight: 800; color: var(--sub); text-transform: uppercase; letter-spacing: 2.5px; margin: 30px 0 15px 5px; display: flex; justify-content: space-between; align-items: center; }
        
        .card { background: var(--card-bg); border-radius: 24px; padding: 20px; margin-bottom: 15px; border: 1px solid rgba(140,118,98,0.05); }
        .clickable:active { transform: scale(0.98); opacity: 0.9; }
        
        .tag { display: inline-block; padding: 2px 8px; border-radius: 6px; font-size: 10px; font-weight: 700; margin-right: 6px; }
        .tag-fixed { background: var(--hl-fixed); color: #a4161a; }
        .tag-saving { background: var(--hl-saving); color: #2d6a4f; }
        .tag-income { background: #e8f5e9; color: #2e7d32; }

        .item-row { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px solid #fcfaf5; }
        
        input, select { border: none; outline: none; background: #f5f2ed; padding: 12px; border-radius: 12px; width: 100%; font-size: 15px; margin-bottom: 12px; font-family: inherit; }
        .btn-primary { background: var(--primary); color: white; border: none; padding: 14px; border-radius: 15px; width: 100%; font-weight: 700; cursor: pointer; }
        .btn-ghost { background: #eee; border: none; padding: 12px; border-radius: 15px; width: 100%; cursor: pointer; color: var(--sub); font-size: 13px; }

        /* 分類按鈕 */
        .cate-chip-group { display: flex; flex-wrap: wrap; gap: 8px; }
        .cate-item { display: flex; align-items: center; background: white; border: 1px solid #eee; border-radius: 50px; overflow: hidden; }
        .cate-name { padding: 8px 12px 8px 16px; font-size: 13px; cursor: pointer; }
        .cate-del { padding: 8px 12px; color: var(--red); cursor: pointer; border-left: 1px solid #eee; }
        .cate-chip { padding: 8px 16px; background: #fff; border: 1px solid #eee; border-radius: 50px; font-size: 13px; cursor: pointer; }
        .cate-chip.active { background: var(--primary); color: white; }

        /* 導覽 */
        .nav { position: fixed; bottom: 0; left: 0; right: 0; height: 85px; background: white; border-top: 1px solid #eee; display: flex; justify-content: space-around; align-items: center; padding-bottom: env(safe-area-inset-bottom); z-index: 100; }
        .nav-item { display: flex; flex-direction: column; align-items: center; color: var(--sub); font-size: 10px; font-weight: 800; cursor: pointer; flex: 1; }
        .nav-item.active { color: var(--primary); }

        .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.4); backdrop-filter: blur(4px); z-index: 1000; display: none; align-items: center; justify-content: center; padding: 20px; }
        .modal-body { background: white; border-radius: 28px; width: 100%; max-width: 350px; padding: 25px; }
    </style>
</head>
<body>

    <!-- 開場動畫 -->
    <div id="splash">
        <div class="splash-logo">CHUN</div>
        <div style="font-size: 10px; letter-spacing: 2px; color: var(--sub);">FINANCIAL NOTEBOOK</div>
    </div>

    <!-- 首頁 -->
    <div id="view-years" class="page">
        <div class="brand">CHUN</div>
        <div class="quote">「生活的平衡，從財務的紀錄開始。」</div>
        
        <div class="section-title">年度帳本 <span onclick="promptAddYear()" style="color:var(--primary); cursor:pointer;">[ + 新增年度 ]</span></div>
        <div id="year-container"></div>

        <div class="section-title">支出分類管理 <span onclick="promptAddCategory()" style="color:var(--primary); cursor:pointer;">[ + ]</span></div>
        <div class="card" style="padding:15px; background:rgba(140,118,98,0.03); border:none;">
            <div id="category-manager-list" class="cate-chip-group"></div>
        </div>

        <div class="section-title">每月預載固定計畫 <span onclick="openModal('fixed-modal')" style="color:var(--primary); cursor:pointer;">[ + 設定 ]</span></div>
        <div id="global-fixed-list"></div>
    </div>

    <!-- 月份列表 -->
    <div id="view-months" class="page">
        <div onclick="showPage('view-years')" style="cursor:pointer; color:var(--sub); font-size:12px; margin-bottom:15px;">← 返回首頁</div>
        <h2 id="month-year-title" style="font-size: 32px; font-weight: 200; margin: 0 0 20px;"></h2>
        <div id="month-grid" style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px;"></div>
    </div>

    <!-- 月份詳細 -->
    <div id="view-detail" class="page" style="padding-top:0;">
        <div class="dashboard">
            <div onclick="showPage('view-months')" style="color:rgba(255,255,255,0.6); font-size:12px; cursor:pointer;">← 返回月份</div>
            <div style="font-size:10px; text-align:center; opacity:0.8; margin-top:10px;">本月可用生活費</div>
            <div class="dash-main-val" id="dash-allowance">$ 0</div>
            <div class="dash-grid">
                <div><div class="dash-label">存款目標</div><div class="dash-val" id="dash-saving">$ 0</div></div>
                <div><div class="dash-label">固定預算</div><div class="dash-val" id="dash-fixed">$ 0</div></div>
                <div><div class="dash-label">已支生活費</div><div class="dash-val" id="dash-spent">$ 0</div></div>
            </div>
        </div>

        <h2 id="detail-month-label" style="font-size: 26px; font-weight: 200;"></h2>

        <div class="section-title">薪資實領設定</div>
        <div class="card" style="padding:15px; display:flex; justify-content:space-between; align-items:center;">
            <div onclick="openModal('salary-modal')" class="clickable" style="font-size:13px; font-weight:700;">💰 薪資計算器</div>
            <input type="number" id="month-income-input" style="text-align:right; font-weight:700; color:var(--primary); font-size:18px; width:120px; background:none; margin:0;" placeholder="0">
        </div>

        <div class="section-title">本月固定收支 (可微調) <span onclick="openModal('fixed-modal')" style="color:var(--primary); cursor:pointer;">[ + 新增 ]</span></div>
        <div id="month-fixed-list"></div>

        <div class="section-title">快速記帳</div>
        <div id="acc-quick-selector" style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;"></div>

        <div class="section-title">帳戶收支明細</div>
        <div id="acc-detail-list"></div>
    </div>

    <!-- 資產管理 -->
    <div id="view-assets" class="page">
        <div class="brand" style="font-size:20px;">ASSETS</div>
        <div class="section-title">帳戶結餘 <span onclick="openModal('add-acc-modal')" style="color:var(--primary); cursor:pointer;">[ + 新增 ]</span></div>
        <div id="asset-container"></div>
    </div>

    <!-- 底部導覽 -->
    <nav class="nav">
        <div class="nav-item" id="nav-y" onclick="showPage('view-years')">🏠<br>首頁</div>
        <div class="nav-item" id="nav-a" onclick="showPage('view-assets')">💳<br>資產</div>
    </nav>

    <!-- 彈窗 -->
    <div id="log-modal" class="modal"><div class="modal-body">
        <h3 id="log-modal-title"></h3>
        <div id="log-cate-selector" class="cate-chip-group" style="margin-bottom:15px;"></div>
        <input type="number" id="l-amt" placeholder="金額">
        <input type="text" id="l-note" placeholder="備註">
        <button class="btn-primary" onclick="saveLog()">儲存</button>
        <button class="btn-ghost" style="margin-top:8px;" onclick="closeModal('log-modal')">取消</button>
    </div></div>

    <div id="fixed-modal" class="modal"><div class="modal-body">
        <h3>新增/調整固定項目</h3>
        <input type="text" id="f-name" placeholder="項目名稱">
        <input type="number" id="f-amt" placeholder="金額">
        <select id="f-type"><option value="固定">固定支出</option><option value="存款">存款目標</option></select>
        <select id="f-acc" class="acc-dropdown-inject"></select>
        <button class="btn-primary" onclick="saveFixed()">加入</button>
        <button class="btn-ghost" style="margin-top:8px;" onclick="closeModal('fixed-modal')">取消</button>
    </div></div>

    <div id="salary-modal" class="modal"><div class="modal-body">
        <h3>薪資計算器</h3>
        <input type="number" id="s-base" placeholder="本薪 (+)">
        <input type="number" id="s-ins" placeholder="勞健保 (-)">
        <div id="s-res" style="text-align:center; margin-bottom:15px; font-weight:700; font-size:20px;">$ 0</div>
        <button class="btn-primary" onclick="applySalary()">帶入本月</button>
        <button class="btn-ghost" style="margin-top:8px;" onclick="closeModal('salary-modal')">取消</button>
    </div></div>

    <div id="add-acc-modal" class="modal"><div class="modal-body">
        <h3>新增帳戶</h3>
        <input type="text" id="new-acc-name" placeholder="名稱">
        <input type="number" id="new-acc-bal" placeholder="餘額">
        <button class="btn-primary" onclick="saveNewAccount()">新增</button>
        <button class="btn-ghost" style="margin-top:8px;" onclick="closeModal('add-acc-modal')">取消</button>
    </div></div>

    <script>
        const DB_KEY = 'chun_finance_v3';
        let state = JSON.parse(localStorage.getItem(DB_KEY)) || {
            years: ["2025"],
            accounts: ["現金", "銀行卡"],
            categories: ["午餐", "晚餐", "交通", "購物"],
            balances: { "現金": 0, "銀行卡": 0 },
            globalFixed: [],
            monthlyData: {}
        };

        let curYear = "2025";
        let curMonth = "一月";
        let activeAcc = "";
        let activeCate = "午餐";

        window.onload = () => {
            setTimeout(() => {
                const splash = document.getElementById('splash');
                splash.style.opacity = '0';
                setTimeout(() => splash.style.display = 'none', 1000);
                showPage('view-years');
            }, 2000);

            document.getElementById('month-income-input').addEventListener('input', (e) => {
                const mk = `${curYear}-${curMonth}`;
                if(!state.monthlyData[mk]) state.monthlyData[mk] = { income:0, logs:[], fixed:[] };
                state.monthlyData[mk].income = parseFloat(e.target.value) || 0;
                save();
            });

            document.getElementById('s-base').addEventListener('input', calcSalary);
            document.getElementById('s-ins').addEventListener('input', calcSalary);
        };

        function save() {
            localStorage.setItem(DB_KEY, JSON.stringify(state));
            render();
        }

        function showPage(id) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            render();
        }

        function openModal(id) { document.getElementById(id).style.display = 'flex'; }
        function closeModal(id) { document.getElementById(id).style.display = 'none'; }

        function promptAddYear() {
            const y = prompt("輸入年份 (例如 2026)：");
            if(y && !state.years.includes(y)) { state.years.unshift(y); save(); }
        }

        function promptAddCategory() {
            const c = prompt("輸入新分類：");
            if(c && !state.categories.includes(c)) { state.categories.push(c); save(); }
        }

        function saveFixed() {
            const n = document.getElementById('f-name').value;
            const a = parseFloat(document.getElementById('f-amt').value);
            const t = document.getElementById('f-type').value;
            const acc = document.getElementById('f-acc').value;
            if(!n || isNaN(a)) return;

            const mk = `${curYear}-${curMonth}`;
            const activePage = document.querySelector('.page.active').id;

            if(activePage === 'view-years') {
                state.globalFixed.push({ name: n, amt: a, type: t, acc: acc });
            } else {
                if(!state.monthlyData[mk]) state.monthlyData[mk] = { income:0, logs:[], fixed:[] };
                state.monthlyData[mk].fixed.push({ name: n, amt: a, type: t, acc: acc });
            }
            closeModal('fixed-modal');
            save();
        }

        function openLogFor(acc) {
            activeAcc = acc;
            document.getElementById('log-modal-title').innerText = `${acc} 支出`;
            renderLogCates();
            openModal('log-modal');
        }

        function renderLogCates() {
            document.getElementById('log-cate-selector').innerHTML = state.categories.map(c => `
                <div class="cate-chip ${activeCate === c ? 'active' : ''}" onclick="activeCate='${c}'; renderLogCates();">${c}</div>
            `).join('');
        }

        function saveLog() {
            const amt = parseFloat(document.getElementById('l-amt').value);
            if(isNaN(amt)) return;
            const mk = `${curYear}-${curMonth}`;
            if(!state.monthlyData[mk]) state.monthlyData[mk] = { income:0, logs:[], fixed:[] };
            
            state.monthlyData[mk].logs.push({ acc: activeAcc, amt: amt, cate: activeCate, note: document.getElementById('l-note').value });
            state.balances[activeAcc] -= amt;
            document.getElementById('l-amt').value = "";
            closeModal('log-modal');
            save();
        }

        function calcSalary() {
            const b = parseFloat(document.getElementById('s-base').value) || 0;
            const i = parseFloat(document.getElementById('s-ins').value) || 0;
            document.getElementById('s-res').innerText = `$ ${(b-i).toLocaleString()}`;
        }

        function applySalary() {
            const b = parseFloat(document.getElementById('s-base').value) || 0;
            const i = parseFloat(document.getElementById('s-ins').value) || 0;
            const mk = `${curYear}-${curMonth}`;
            if(!state.monthlyData[mk]) state.monthlyData[mk] = { income:0, logs:[], fixed:[] };
            state.monthlyData[mk].income = b - i;
            closeModal('salary-modal');
            save();
        }

        function saveNewAccount() {
            const n = document.getElementById('new-acc-name').value;
            const b = parseFloat(document.getElementById('new-acc-bal').value) || 0;
            if(n) { state.accounts.push(n); state.balances[n] = b; save(); closeModal('add-acc-modal'); }
        }

        function render() {
            // 首頁年度
            document.getElementById('year-container').innerHTML = state.years.map(y => `
                <div class="card clickable" onclick="curYear='${y}'; showPage('view-months')">${y} 年度帳本</div>
            `).join('');

            // 分類
            document.getElementById('category-manager-list').innerHTML = state.categories.map(c => `
                <div class="cate-item"><div class="cate-name">${c}</div><div class="cate-del" onclick="state.categories=state.categories.filter(x=>x!=='${c}');save();">✕</div></div>
            `).join('');

            // 全域固定
            document.getElementById('global-fixed-list').innerHTML = state.globalFixed.map((f,i) => `
                <div class="item-row"><span><span class="tag ${f.type==='存款'?'tag-saving':'tag-fixed'}">${f.type}</span>${f.name}</span><span>$${f.amt} <span onclick="state.globalFixed.splice(${i},1);save()" style="color:#ccc">✕</span></span></div>
            `).join('') || '<div style="font-size:10px; color:#ccc;">無設定</div>';

            // 月份
            document.getElementById('month-year-title').innerText = curYear;
            const ms = ["一月","二月","三月","四月","五月","六月","七月","八月","九月","十月","十一月","十二月"];
            document.getElementById('month-grid').innerHTML = ms.map(m => `<div class="card clickable" onclick="curMonth='${m}'; showPage('view-detail')">${m}</div>`).join('');

            // 詳細
            const mk = `${curYear}-${curMonth}`;
            if(!state.monthlyData[mk]) state.monthlyData[mk] = { income:0, logs:[], fixed: JSON.parse(JSON.stringify(state.globalFixed)) };
            const md = state.monthlyData[mk];

            document.getElementById('detail-month-label').innerText = `${curYear} ${curMonth}`;
            document.getElementById('month-income-input').value = md.income || "";

            // 計算 Dashboard
            const savT = md.fixed.filter(f=>f.type==='存款').reduce((s,f)=>s+f.amt,0);
            const fixT = md.fixed.filter(f=>f.type==='固定').reduce((s,f)=>s+f.amt,0);
            const speT = md.logs.reduce((s,l)=>s+l.amt,0);
            document.getElementById('dash-allowance').innerText = `$ ${(md.income - savT - fixT - speT).toLocaleString()}`;
            document.getElementById('dash-saving').innerText = `$ ${savT.toLocaleString()}`;
            document.getElementById('dash-fixed').innerText = `$ ${fixT.toLocaleString()}`;
            document.getElementById('dash-spent').innerText = `$ ${speT.toLocaleString()}`;

            // 月份固定清單
            document.getElementById('month-fixed-list').innerHTML = md.fixed.map((f,i) => `
                <div class="card" style="padding:15px; display:flex; justify-content:space-between; align-items:center; margin-bottom:8px;">
                    <span style="font-size:13px;"><span class="tag ${f.type==='存款'?'tag-saving':'tag-fixed'}">${f.type}</span>${f.name}</span>
                    <div style="text-align:right">
                        <div style="font-weight:700;">$${f.amt.toLocaleString()}</div>
                        <div onclick="state.monthlyData['${mk}'].fixed.splice(${i},1);save()" style="font-size:10px; color:#ccc;">刪除本月</div>
                    </div>
                </div>
            `).join('');

            // 記帳按鈕與明細
            document.getElementById('acc-quick-selector').innerHTML = state.accounts.map(a => `<div class="card clickable" style="text-align:center;" onclick="openLogFor('${a}')">${a} +</div>`).join('');
            document.getElementById('acc-detail-list').innerHTML = state.accounts.map(a => {
                const logs = md.logs.filter(l=>l.acc===a);
                if(!logs.length) return '';
                return `<div class="card"><div style="font-weight:700; font-size:12px; margin-bottom:10px;">${a} 明細</div>` + 
                    logs.map(l=>`<div class="item-row"><span>${l.cate} - ${l.note||''}</span><span style="color:var(--red)">-$${l.amt}</span></div>`).join('') + `</div>`;
            }).join('');

            // 資產
            document.getElementById('asset-container').innerHTML = state.accounts.map(a => `
                <div class="card" style="display:flex; justify-content:space-between; align-items:center;">
                    <div><div style="font-size:10px; color:var(--sub);">${a}</div><div style="font-size:20px; font-weight:700;">$ ${state.balances[a].toLocaleString()}</div></div>
                    <div onclick="if(confirm('刪除？')){state.accounts=state.accounts.filter(x=>x!=='${a}');save();}" style="color:#eee">✕</div>
                </div>
            `).join('');

            document.querySelectorAll('.acc-dropdown-inject').forEach(s => s.innerHTML = state.accounts.map(a=>`<option value="${a}">${a}</option>`).join(''));
        }
    </script>
</body>
</html>

