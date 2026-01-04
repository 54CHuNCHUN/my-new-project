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

        * { box-sizing: border-box; -webkit-font-smoothing: antialiased; -webkit-tap-highlight-color: transparent; }
        body { font-family: 'Noto Sans TC', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 0; line-height: 1.5; }

        /* 開場動畫 */
        #splash {
            position: fixed; inset: 0; background: var(--bg); z-index: 10000;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            transition: opacity 0.8s ease;
        }
        .splash-logo { font-size: 36px; font-weight: 200; color: var(--primary); letter-spacing: 15px; animation: splashPulse 2s infinite; }
        @keyframes splashPulse { 0%, 100% { opacity: 0.3; } 50% { opacity: 1; } }

        /* 頁面切換 */
        .page { padding: 20px 24px 140px; max-width: 500px; margin: 0 auto; display: none; }
        .page.active { display: block; animation: pageFadeIn 0.5s ease; }
        @keyframes pageFadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .brand { font-size: 28px; font-weight: 200; color: var(--primary); letter-spacing: 8px; text-align: center; margin: 40px 0 10px; }
        .quote { font-size: 11px; color: var(--sub); text-align: center; margin-bottom: 30px; font-style: italic; }

        /* Dashboard */
        .dashboard {
            background: var(--primary); color: white; padding: 35px 24px;
            border-bottom-left-radius: 35px; border-bottom-right-radius: 35px;
            margin-bottom: 25px; box-shadow: 0 10px 25px rgba(140,118,98,0.2);
        }
        .dash-main-val { font-size: 38px; font-weight: 200; text-align: center; margin: 15px 0 25px; }
        .dash-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; text-align: center; }
        .dash-label { font-size: 10px; opacity: 0.7; letter-spacing: 1px; }
        .dash-val { font-size: 13px; font-weight: 700; }

        .section-title { font-size: 11px; font-weight: 800; color: var(--sub); letter-spacing: 2px; margin: 30px 0 15px 5px; display: flex; justify-content: space-between; align-items: center; }
        
        .card { background: var(--card-bg); border-radius: 24px; padding: 20px; margin-bottom: 15px; border: 1px solid rgba(140,118,98,0.05); }
        .clickable:active { transform: scale(0.97); opacity: 0.9; }
        
        .tag { display: inline-block; padding: 2px 8px; border-radius: 6px; font-size: 10px; font-weight: 700; margin-right: 6px; }
        .tag-fixed { background: var(--hl-fixed); color: #a4161a; }
        .tag-saving { background: var(--hl-saving); color: #2d6a4f; }

        .item-row { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px dotted #eee; }
        .item-row:last-child { border-bottom: none; }
        
        input, select { border: none; outline: none; background: #f5f2ed; padding: 14px; border-radius: 12px; width: 100%; font-size: 15px; margin-bottom: 12px; font-family: inherit; }
        .btn-primary { background: var(--primary); color: white; border: none; padding: 16px; border-radius: 16px; width: 100%; font-weight: 700; cursor: pointer; }
        .btn-ghost { background: #eee; border: none; padding: 14px; border-radius: 16px; width: 100%; cursor: pointer; color: var(--sub); font-size: 13px; }

        /* 分類管理 */
        .cate-chip-group { display: flex; flex-wrap: wrap; gap: 8px; }
        .cate-chip { padding: 8px 18px; background: #fff; border: 1px solid #eee; border-radius: 50px; font-size: 13px; cursor: pointer; transition: 0.2s; }
        .cate-chip.active { background: var(--primary); color: white; border-color: var(--primary); }

        /* 導覽 */
        .nav { position: fixed; bottom: 0; left: 0; right: 0; height: 85px; background: white; border-top: 1px solid #eee; display: flex; justify-content: space-around; align-items: center; padding-bottom: env(safe-area-inset-bottom); z-index: 100; }
        .nav-item { display: flex; flex-direction: column; align-items: center; color: var(--sub); font-size: 10px; font-weight: 800; cursor: pointer; flex: 1; }
        .nav-item.active { color: var(--primary); }

        .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.5); backdrop-filter: blur(5px); z-index: 2000; display: none; align-items: center; justify-content: center; padding: 20px; }
        .modal-body { background: white; border-radius: 30px; width: 100%; max-width: 340px; padding: 28px; box-shadow: 0 20px 40px rgba(0,0,0,0.2); }
    </style>
</head>
<body>

    <div id="splash">
        <div class="splash-logo">CHUN</div>
        <div style="font-size: 10px; letter-spacing: 3px; color: var(--sub); margin-top:15px;">FINANCE NOTEBOOK</div>
    </div>

    <!-- 首頁 -->
    <div id="view-years" class="page">
        <div class="brand">CHUN</div>
        <div class="quote">「生活的平衡，從財務的紀錄開始。」</div>
        
        <div class="section-title">年度帳本 <span onclick="addNewYear()" style="color:var(--primary); cursor:pointer;">[ + 新增年度 ]</span></div>
        <div id="year-container"></div>

        <div class="section-title">支出分類 <span onclick="addNewCate()" style="color:var(--primary); cursor:pointer;">[ + ]</span></div>
        <div class="card"><div id="cate-mgr-list" class="cate-chip-group"></div></div>

        <div class="section-title">每月預載固定計畫 <span onclick="openModal('fixed-modal', 'global')" style="color:var(--primary); cursor:pointer;">[ + 設定 ]</span></div>
        <div id="global-fixed-list"></div>
    </div>

    <!-- 月份列表 -->
    <div id="view-months" class="page">
        <div onclick="showPage('view-years')" style="cursor:pointer; color:var(--sub); font-size:12px; margin-bottom:20px;">← 返回首頁</div>
        <h2 id="month-header-y" style="font-size: 34px; font-weight: 200; margin: 0 0 25px;"></h2>
        <div id="month-grid" style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px;"></div>
    </div>

    <!-- 月份詳細 -->
    <div id="view-detail" class="page" style="padding-top:0;">
        <div class="dashboard">
            <div onclick="showPage('view-months')" style="color:rgba(255,255,255,0.6); font-size:12px; cursor:pointer;">← 返回月份</div>
            <div style="font-size:10px; text-align:center; opacity:0.8; margin-top:15px;">本月可用生活費</div>
            <div class="dash-main-val" id="d-allowance">$ 0</div>
            <div class="dash-grid">
                <div><div class="dash-label">存款目標</div><div class="dash-val" id="d-saving">$ 0</div></div>
                <div><div class="dash-label">固定支出</div><div class="dash-val" id="d-fixed">$ 0</div></div>
                <div><div class="dash-label">本月支用</div><div class="dash-val" id="d-spent">$ 0</div></div>
            </div>
        </div>

        <h2 id="detail-month-label" style="font-size: 28px; font-weight: 200;"></h2>

        <div class="section-title">薪資實領設定</div>
        <div class="card" style="padding:15px; display:flex; justify-content:space-between; align-items:center;">
            <div onclick="openModal('salary-modal')" class="clickable" style="font-size:13px; font-weight:700;">💰 薪資計算</div>
            <input type="number" id="m-income-input" style="text-align:right; font-weight:700; color:var(--primary); font-size:18px; width:130px; background:none; margin:0;" placeholder="0">
        </div>

        <div class="section-title">本月固定收支 (微調) <span onclick="openModal('fixed-modal', 'month')" style="color:var(--primary); cursor:pointer;">[ + ]</span></div>
        <div id="month-fixed-list"></div>

        <div class="section-title">快速記帳</div>
        <div id="quick-acc-btns" style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;"></div>

        <div class="section-title">收支明細</div>
        <div id="month-log-list"></div>
    </div>

    <!-- 資產 -->
    <div id="view-assets" class="page">
        <div class="brand" style="font-size:20px;">ASSETS</div>
        <div class="section-title">帳戶結餘 <span onclick="openModal('acc-modal')" style="color:var(--primary); cursor:pointer;">[ + 新增 ]</span></div>
        <div id="asset-container"></div>
    </div>

    <!-- 導覽 -->
    <nav class="nav">
        <div class="nav-item" id="nav-y" onclick="showPage('view-years')">🏠<br>首頁</div>
        <div class="nav-item" id="nav-a" onclick="showPage('view-assets')">💳<br>資產</div>
    </nav>

    <!-- 彈窗 -->
    <div id="log-modal" class="modal"><div class="modal-body">
        <h3 id="log-modal-title"></h3>
        <div id="log-cate-selector" class="cate-chip-group" style="margin-bottom:18px;"></div>
        <input type="number" id="l-amt" placeholder="輸入金額">
        <input type="text" id="l-note" placeholder="備註 (選填)">
        <button class="btn-primary" onclick="saveNewLog()">儲存紀錄</button>
        <button class="btn-ghost" style="margin-top:10px;" onclick="closeModal('log-modal')">取消</button>
    </div></div>

    <div id="fixed-modal" class="modal"><div class="modal-body">
        <h3>新增固定項目</h3>
        <input type="text" id="f-name" placeholder="項目名稱">
        <input type="number" id="f-amt" placeholder="金額">
        <select id="f-type"><option value="固定">固定支出</option><option value="存款">存款目標</option></select>
        <select id="f-acc" class="acc-dropdown-inject"></select>
        <button class="btn-primary" onclick="saveNewFixed()">確認加入</button>
        <button class="btn-ghost" style="margin-top:10px;" onclick="closeModal('fixed-modal')">取消</button>
    </div></div>

    <div id="salary-modal" class="modal"><div class="modal-body">
        <h3>薪資計算器</h3>
        <input type="number" id="s-base" placeholder="應領本薪 (+)">
        <input type="number" id="s-deduct" placeholder="代扣勞健保 (-)">
        <button class="btn-primary" onclick="applySalaryCalc()">帶入本月</button>
        <button class="btn-ghost" style="margin-top:10px;" onclick="closeModal('salary-modal')">取消</button>
    </div></div>

    <div id="acc-modal" class="modal"><div class="modal-body">
        <h3>新增資產帳戶</h3>
        <input type="text" id="a-name" placeholder="帳戶名稱 (如：現金、中信)">
        <input type="number" id="a-bal" placeholder="目前餘額">
        <button class="btn-primary" onclick="saveNewAccount()">新增</button>
        <button class="btn-ghost" style="margin-top:10px;" onclick="closeModal('acc-modal')">取消</button>
    </div></div>

    <script>
        const STORAGE_KEY = 'chun_finance_pro_v1';
        let state = JSON.parse(localStorage.getItem(STORAGE_KEY)) || {
            years: ["2025"],
            accounts: ["現金"],
            categories: ["午餐", "晚餐", "交通", "購物", "日用品"],
            balances: { "現金": 0 },
            globalFixed: [],
            monthlyData: {}
        };

        let curY = "2025", curM = "一月", activeAcc = "", activeCate = "午餐", fixedMode = "global";

        window.onload = () => {
            // 動畫啟動
            setTimeout(() => {
                const sp = document.getElementById('splash');
                sp.style.opacity = '0';
                setTimeout(() => sp.style.display = 'none', 800);
                showPage('view-years');
            }, 1800);

            document.getElementById('m-income-input').oninput = (e) => {
                const mk = `${curY}-${curM}`;
                if(!state.monthlyData[mk]) initMonth(mk);
                state.monthlyData[mk].income = parseFloat(e.target.value) || 0;
                save();
            };
        };

        function save() {
            localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
            render();
        }

        function showPage(id) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
            if(id === 'view-years' || id === 'view-months') document.getElementById('nav-y').classList.add('active');
            if(id === 'view-assets') document.getElementById('nav-a').classList.add('active');
            render();
        }

        function openModal(id, mode) {
            if(mode) fixedMode = mode;
            document.getElementById(id).style.display = 'flex';
        }
        function closeModal(id) { document.getElementById(id).style.display = 'none'; }

        function initMonth(mk) {
            state.monthlyData[mk] = {
                income: 0,
                logs: [],
                fixed: JSON.parse(JSON.stringify(state.globalFixed))
            };
        }

        function addNewYear() {
            const y = prompt("請輸入年份 (例如 2026)：");
            if(y && !state.years.includes(y)) { state.years.unshift(y); save(); }
        }

        function addNewCate() {
            const c = prompt("新增支出分類：");
            if(c && !state.categories.includes(c)) { state.categories.push(c); save(); }
        }

        function saveNewAccount() {
            const n = document.getElementById('a-name').value;
            const b = parseFloat(document.getElementById('a-bal').value) || 0;
            if(n) {
                state.accounts.push(n);
                state.balances[n] = b;
                closeModal('acc-modal');
                save();
            }
        }

        function saveNewFixed() {
            const n = document.getElementById('f-name').value;
            const a = parseFloat(document.getElementById('f-amt').value);
            const t = document.getElementById('f-type').value;
            const acc = document.getElementById('f-acc').value;
            if(!n || isNaN(a)) return;

            const item = { name: n, amt: a, type: t, acc: acc };
            if(fixedMode === 'global') {
                state.globalFixed.push(item);
            } else {
                const mk = `${curY}-${curM}`;
                if(!state.monthlyData[mk]) initMonth(mk);
                state.monthlyData[mk].fixed.push(item);
            }
            closeModal('fixed-modal');
            save();
        }

        function openLogEntry(acc) {
            activeAcc = acc;
            activeCate = state.categories[0];
            document.getElementById('log-modal-title').innerText = `${acc} 支出記帳`;
            renderCateChips();
            openModal('log-modal');
        }

        function renderCateChips() {
            document.getElementById('log-cate-selector').innerHTML = state.categories.map(c => `
                <div class="cate-chip ${activeCate === c ? 'active' : ''}" onclick="activeCate='${c}'; renderCateChips();">${c}</div>
            `).join('');
        }

        function saveNewLog() {
            const amt = parseFloat(document.getElementById('l-amt').value);
            if(isNaN(amt) || amt <= 0) return;
            const mk = `${curY}-${curM}`;
            if(!state.monthlyData[mk]) initMonth(mk);
            
            state.monthlyData[mk].logs.push({
                acc: activeAcc,
                amt: amt,
                cate: activeCate,
                note: document.getElementById('l-note').value,
                date: new Date().toLocaleDateString()
            });
            state.balances[activeAcc] -= amt;
            document.getElementById('l-amt').value = "";
            document.getElementById('l-note').value = "";
            closeModal('log-modal');
            save();
        }

        function applySalaryCalc() {
            const res = (parseFloat(document.getElementById('s-base').value) || 0) - (parseFloat(document.getElementById('s-deduct').value) || 0);
            const mk = `${curY}-${curM}`;
            if(!state.monthlyData[mk]) initMonth(mk);
            state.monthlyData[mk].income = res;
            closeModal('salary-modal');
            save();
        }

        function render() {
            // 首頁
            document.getElementById('year-container').innerHTML = state.years.map(y => `
                <div class="card clickable" onclick="curY='${y}'; showPage('view-months')">${y} 年度帳本</div>
            `).join('');

            document.getElementById('cate-mgr-list').innerHTML = state.categories.map(c => `
                <div class="cate-chip" onclick="if(confirm('刪除 ${c} 分類？')){state.categories=state.categories.filter(x=>x!=='${c}');save();}">${c} ✕</div>
            `).join('');

            document.getElementById('global-fixed-list').innerHTML = state.globalFixed.map((f,i) => `
                <div class="item-row">
                    <span><span class="tag ${f.type==='存款'?'tag-saving':'tag-fixed'}">${f.type}</span>${f.name}</span>
                    <span>$${f.amt.toLocaleString()} <span onclick="state.globalFixed.splice(${i},1);save()" style="color:#ccc; padding-left:10px">✕</span></span>
                </div>
            `).join('') || '<div style="font-size:11px; color:#ccc; text-align:center; padding:20px;">尚未設定固定計畫</div>';

            // 月份列表
            document.getElementById('month-header-y').innerText = curY;
            const months = ["一月","二月","三月","四月","五月","六月","七月","八月","九月","十月","十一月","十二月"];
            document.getElementById('month-grid').innerHTML = months.map(m => `
                <div class="card clickable" style="text-align:center; padding:25px 10px;" onclick="curM='${m}'; showPage('view-detail')">${m}</div>
            `).join('');

            // 月份詳細
            const mk = `${curY}-${curM}`;
            if(!state.monthlyData[mk]) initMonth(mk);
            const md = state.monthlyData[mk];

            document.getElementById('detail-month-label').innerText = `${curY} ${curM}`;
            document.getElementById('m-income-input').value = md.income || "";

            const sTotal = md.fixed.filter(f=>f.type==='存款').reduce((s,f)=>s+f.amt, 0);
            const fTotal = md.fixed.filter(f=>f.type==='固定').reduce((s,f)=>s+f.amt, 0);
            const lTotal = md.logs.reduce((s,l)=>s+l.amt, 0);

            document.getElementById('d-allowance').innerText = `$ ${(md.income - sTotal - fTotal - lTotal).toLocaleString()}`;
            document.getElementById('d-saving').innerText = `$ ${sTotal.toLocaleString()}`;
            document.getElementById('d-fixed').innerText = `$ ${fTotal.toLocaleString()}`;
            document.getElementById('d-spent').innerText = `$ ${lTotal.toLocaleString()}`;

            document.getElementById('month-fixed-list').innerHTML = md.fixed.map((f,i) => `
                <div class="item-row">
                    <span><span class="tag ${f.type==='存款'?'tag-saving':'tag-fixed'}">${f.type}</span>${f.name}</span>
                    <span style="font-weight:700;">$${f.amt.toLocaleString()} <span onclick="state.monthlyData['${mk}'].fixed.splice(${i},1);save()" style="color:#ccc; padding-left:10px">✕</span></span>
                </div>
            `).join('');

            document.getElementById('quick-acc-btns').innerHTML = state.accounts.map(a => `
                <div class="card clickable" style="text-align:center; padding:15px;" onclick="openLogEntry('${a}')">${a} +</div>
            `).join('');

            document.getElementById('month-log-list').innerHTML = md.logs.map(l => `
                <div class="item-row">
                    <span style="font-size:13px;">${l.cate} <small style="color:#999">${l.note?'('+l.note+')':''}</small></span>
                    <span style="color:var(--red); font-weight:700;">-$${l.amt.toLocaleString()}</span>
                </div>
            `).reverse().join('') || '<div style="font-size:11px; color:#ccc; text-align:center; padding:20px;">尚無支出紀錄</div>';

            // 資產
            document.getElementById('asset-container').innerHTML = state.accounts.map(a => `
                <div class="card" style="display:flex; justify-content:space-between; align-items:center;">
                    <div>
                        <div style="font-size:11px; color:var(--sub); margin-bottom:5px;">${a}</div>
                        <div style="font-size:22px; font-weight:700;">$ ${state.balances[a].toLocaleString()}</div>
                    </div>
                    <div onclick="if(confirm('確定刪除 ${a} 帳戶？')){state.accounts=state.accounts.filter(x=>x!=='${a}'); save();}" style="color:#eee; font-size:20px;">✕</div>
                </div>
            `).join('');

            document.querySelectorAll('.acc-dropdown-inject').forEach(sel => {
                sel.innerHTML = state.accounts.map(a => `<option value="${a}">${a}</option>`).join('');
            });
        }
    </script>
</body>
</html>

