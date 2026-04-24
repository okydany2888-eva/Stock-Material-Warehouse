<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>Stock Gudang - Mobile Optimized</title>
    <style>
        * {
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            margin: 0;
            padding: 12px;
            background: #eef2f7;
            font-family: 'Segoe UI', Roboto, system-ui, -apple-system, 'Helvetica Neue', sans-serif;
        }

        .app-container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 28px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.08);
            overflow: hidden;
            padding: 16px 18px 24px 18px;
        }

        h1 {
            font-size: 1.6rem;
            margin: 0 0 4px 0;
            font-weight: 700;
            color: #1a3e50;
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 8px;
        }
        h1 small {
            font-size: 0.7rem;
            background: #e9edf2;
            padding: 5px 12px;
            border-radius: 50px;
            font-weight: normal;
            color: #2c6e8f;
        }

        .form-card {
            background: #f8fafc;
            border-radius: 24px;
            padding: 16px;
            margin: 16px 0 20px 0;
            border: 1px solid #e2e8f0;
        }

        .form-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
        }

        .input-group {
            flex: 1 1 calc(50% - 12px);
            min-width: 140px;
        }

        .input-group label {
            display: block;
            font-size: 0.7rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.3px;
            color: #4a627a;
            margin-bottom: 5px;
        }

        .input-group input, 
        .input-group select {
            width: 100%;
            padding: 12px 12px;
            font-size: 0.9rem;
            border: 1px solid #cbd5e1;
            border-radius: 18px;
            background: white;
            transition: all 0.2s;
        }

        .input-group input:focus, 
        .input-group select:focus {
            border-color: #2c7da0;
            outline: none;
            box-shadow: 0 0 0 2px rgba(44,125,160,0.2);
        }

        button {
            background: #2c7da0;
            border: none;
            padding: 12px 20px;
            border-radius: 40px;
            font-weight: 600;
            color: white;
            font-size: 0.85rem;
            cursor: pointer;
            transition: 0.2s;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            width: 100%;
        }

        button:active {
            transform: scale(0.97);
        }

        .btn-outline {
            background: white;
            border: 1px solid #2c7da0;
            color: #2c7da0;
        }

        .btn-outline:active {
            background: #eef4fc;
        }

        .btn-danger {
            background: #bc6c25;
        }

        .action-bar {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            gap: 12px;
            margin: 8px 0 16px 0;
        }

        .search-box {
            background: #f1f5f9;
            border-radius: 48px;
            padding: 5px 16px;
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            gap: 8px;
            flex: 2;
            min-width: 180px;
        }

        .search-box input {
            background: transparent;
            border: none;
            padding: 10px 5px;
            font-size: 0.85rem;
            flex: 1;
            min-width: 100px;
        }

        .search-box input:focus {
            outline: none;
        }

        .btn-group {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .btn-group button {
            width: auto;
            padding: 8px 18px;
        }

        .table-wrapper {
            overflow-x: auto;
            border-radius: 20px;
            border: 1px solid #e9edf2;
            background: white;
            margin: 12px 0;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.75rem;
            min-width: 550px;
        }

        th {
            background: #eef2f8;
            padding: 12px 6px;
            font-weight: 700;
            color: #1e3a4d;
            text-align: center;
            border-bottom: 1px solid #dce3ec;
        }

        td {
            padding: 10px 4px;
            text-align: center;
            border-bottom: 1px solid #f0f4f9;
        }

        .delete-btn {
            background: none;
            border: none;
            font-size: 1.2rem;
            padding: 6px 10px;
            color: #b45309;
            width: auto;
            box-shadow: none;
        }

        .delete-btn:active {
            background: #fff0e5;
            transform: none;
        }

        .footer-buttons {
            display: flex;
            justify-content: flex-end;
            margin-top: 18px;
            gap: 12px;
            flex-wrap: wrap;
        }

        @media (min-width: 768px) {
            body {
                padding: 20px;
            }
            .app-container {
                padding: 24px 28px;
            }
            .form-grid {
                gap: 16px;
            }
            .input-group {
                flex: 1 1 auto;
            }
            button {
                width: auto;
                padding: 10px 24px;
            }
            .btn-group button {
                width: auto;
            }
            .search-box {
                flex: 2;
            }
            table {
                font-size: 0.8rem;
                min-width: auto;
            }
            td, th {
                padding: 10px 8px;
            }
        }

        @media (max-width: 900px) and (orientation: landscape) {
            body {
                padding: 8px;
            }
            .app-container {
                padding: 12px 16px;
            }
            .form-card {
                padding: 12px;
                margin: 8px 0;
            }
            .input-group input, .input-group select {
                padding: 8px 10px;
            }
            button {
                padding: 8px 16px;
            }
            th, td {
                padding: 6px 4px;
            }
        }

        @media (max-width: 480px) {
            .input-group {
                flex: 1 1 100%;
            }
            .search-box {
                flex-direction: column;
                align-items: stretch;
                border-radius: 28px;
                background: #f1f5f9;
                padding: 12px;
            }
            .search-box input {
                background: white;
                border-radius: 30px;
                padding: 10px 12px;
                margin: 2px 0;
            }
            .btn-group {
                width: 100%;
                justify-content: space-between;
            }
            .btn-group button {
                flex: 1;
            }
        }

        .info-note {
            font-size: 0.65rem;
            color: #5c7a94;
            margin-top: 8px;
            text-align: center;
        }
    </style>
</head>
<body>
<div class="app-container">
    <h1>
        📦 Stock Gudang
        <small>In/Out + Total</small>
    </h1>

    <div class="form-card">
        <div class="form-grid">
            <div class="input-group">
                <label>🗓️ Tanggal</label>
                <input type="date" id="tanggalInput" required>
            </div>
            <div class="input-group">
                <label>🏷️ Nama Barang</label>
                <input type="text" id="namaBarangInput" placeholder="Contoh: Semen" autocomplete="off">
            </div>
            <div class="input-group">
                <label>📥 IN (Masuk)</label>
                <input type="number" id="inInput" value="0" min="0" step="1">
            </div>
            <div class="input-group">
                <label>📤 OUT (Keluar)</label>
                <input type="number" id="outInput" value="0" min="0" step="1">
            </div>
            <div class="input-group">
                <button id="tambahBtn">➕ Tambah</button>
            </div>
        </div>
        <div class="info-note">* Total In / Out per barang terakumulasi otomatis berdasarkan urutan tanggal</div>
    </div>

    <div class="action-bar">
        <div class="search-box">
            <span>🔍</span>
            <input type="text" id="searchInput" placeholder="Cari nama barang...">
            <button id="resetFilterBtn" class="btn-outline" style="width:auto; padding:6px 14px;">Reset</button>
        </div>
        <div class="btn-group">
            <button id="printTableBtn">🖨️ Print</button>
            <button id="downloadExcelBtn">⬇️ CSV</button>
        </div>
    </div>

    <div class="table-wrapper">
        <table id="stockTable">
            <thead>
                <tr>
                    <th>No</th>
                    <th>Tanggal</th>
                    <th>Nama Barang</th>
                    <th>IN</th>
                    <th>OUT</th>
                    <th>Total IN</th>
                    <th>Total OUT</th>
                    <th>Aksi</th>
                </tr>
                <tr style="background:#eef2f8;">
                    <th colspan="6" style="text-align:right">✨ Stok Akhir (Total IN - Total OUT) :</th>
                    <th colspan="2" id="grandTotalStockCell" style="background:#cbdde9;">0</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <tr><td colspan="8" style="text-align:center; padding:35px;">📭 Kosong, tambah transaksi</td></tr>
            </tbody>
            <tfoot id="tableFootTotal"></tfoot>
        </table>
    </div>

    <div class="footer-buttons">
        <button id="clearAllBtn" class="btn-danger">🗑️ Hapus Semua</button>
    </div>
</div>

<script>
    let stockData = [];
    let nextId = 1;

    function getTodayDate() {
        const d = new Date();
        return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
    }
    document.getElementById('tanggalInput').value = getTodayDate();

    function recalcAccumulatedTotals() {
        const barangMap = new Map();
        for (let i = 0; i < stockData.length; i++) {
            const key = stockData[i].namaBarang;
            if (!barangMap.has(key)) barangMap.set(key, []);
            barangMap.get(key).push({ index: i, tanggal: stockData[i].tanggal, id: stockData[i].id });
        }
        for (let [_, items] of barangMap.entries()) {
            items.sort((a,b) => {
                if (a.tanggal === b.tanggal) return a.id - b.id;
                return a.tanggal.localeCompare(b.tanggal);
            });
            let runningIn = 0;
            let runningOut = 0;
            for (let entry of items) {
                const idx = entry.index;
                const item = stockData[idx];
                runningIn += item.inVal;
                runningOut += item.outVal;
                item.totalIn = runningIn;
                item.totalOut = runningOut;
            }
        }
    }

    function renderTable() {
        const searchTerm = document.getElementById('searchInput').value.toLowerCase().trim();

        const filtered = stockData.filter(item => {
            const matchNama = item.namaBarang.toLowerCase().includes(searchTerm);
            return matchNama;
        });

        const tbody = document.getElementById('tableBody');
        if (filtered.length === 0) {
            tbody.innerHTML = `<tr><td colspan="8" style="text-align:center; padding:32px;">🔍 Tidak ada data / kosong</td></tr>`;
            updateTotalFooter([]);
            document.getElementById('grandTotalStockCell').innerHTML = "0";
            return;
        }

        let html = '';
        filtered.forEach((item, idx) => {
            html += `
                <tr>
                    <td>${idx+1}</td>
                    <td>${item.tanggal}</td>
                    <td style="font-weight:500;">${escapeHtml(item.namaBarang)}</td>
                    <td>${item.inVal}</td>
                    <td>${item.outVal}</td>
                    <td style="background:#eef6fc; font-weight:500;">${item.totalIn}</td>
                    <td style="background:#fff1e6; font-weight:500;">${item.totalOut}</td>
                    <td><button class="delete-btn" data-id="${item.id}">🗑️</button></td>
                </tr>
            `;
        });
        tbody.innerHTML = html;

        let totalInFilter = 0, totalOutFilter = 0;
        filtered.forEach(item => {
            totalInFilter += item.inVal;
            totalOutFilter += item.outVal;
        });
        const netStock = totalInFilter - totalOutFilter;
        document.getElementById('grandTotalStockCell').innerHTML = `<strong>${netStock}</strong>`;
        updateTotalFooter(filtered);

        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = parseInt(btn.getAttribute('data-id'));
                deleteById(id);
            });
        });
    }

    function updateTotalFooter(filteredArr) {
        let sumIn = 0, sumOut = 0;
        filteredArr.forEach(i => { sumIn += i.inVal; sumOut += i.outVal; });
        const net = sumIn - sumOut;
        const footer = document.getElementById('tableFootTotal');
        if (footer) {
            footer.innerHTML = `
                <tr style="background:#f4f9ff; font-weight:600;">
                    <td colspan="3" style="text-align:right;">📊 TOTAL (filter) :</td>
                    <td><strong>${sumIn}</strong></td>
                    <td><strong>${sumOut}</strong></td>
                    <td colspan="2"><strong>Net: ${net}</strong></td>
                    <td></td>
                </tr>
            `;
        }
    }

    function deleteById(id) {
        stockData = stockData.filter(item => item.id !== id);
        if (stockData.length === 0) nextId = 1;
        else {
            const maxId = Math.max(...stockData.map(i => i.id), 0);
            if (nextId <= maxId) nextId = maxId + 1;
        }
        recalcAccumulatedTotals();
        renderTable();
        saveToLocal();
    }

    function addTransaction() {
        const tanggal = document.getElementById('tanggalInput').value;
        let namaBarang = document.getElementById('namaBarangInput').value.trim();
        let inVal = parseInt(document.getElementById('inInput').value, 10);
        let outVal = parseInt(document.getElementById('outInput').value, 10);
        if (isNaN(inVal)) inVal = 0;
        if (isNaN(outVal)) outVal = 0;

        if (!tanggal) { alert("Tanggal wajib diisi!"); return; }
        if (namaBarang === "") { alert("Nama barang harus diisi!"); return; }
        if (inVal === 0 && outVal === 0) { alert("IN atau OUT minimal salah satu > 0"); return; }

        const newId = nextId++;
        const newItem = {
            id: newId, tanggal, namaBarang,
            inVal, outVal, totalIn: 0, totalOut: 0
        };
        stockData.push(newItem);
        stockData.sort((a,b) => {
            if (a.tanggal === b.tanggal) return a.id - b.id;
            return a.tanggal.localeCompare(b.tanggal);
        });
        recalcAccumulatedTotals();
        renderTable();
        saveToLocal();

        document.getElementById('inInput').value = 0;
        document.getElementById('outInput').value = 0;
        document.getElementById('namaBarangInput').value = '';
        document.getElementById('namaBarangInput').focus();
    }

    function clearAll() {
        if (confirm("⚠️ Hapus semua data stok? Tidak dapat dikembalikan.")) {
            stockData = [];
            nextId = 1;
            recalcAccumulatedTotals();
            renderTable();
            saveToLocal();
        }
    }

    function printStock() {
        const win = window.open('', '_blank');
        const tableClone = document.getElementById('stockTable').cloneNode(true);
        const style = document.createElement('style');
        style.textContent = `
            body { font-family: system-ui, sans-serif; margin: 20px; }
            table { border-collapse: collapse; width: 100%; }
            th, td { border: 1px solid #aaa; padding: 8px; text-align: center; }
            th { background: #eef; }
            .delete-btn { display: none; }
        `;
        win.document.write(`
            <html><head><title>Laporan Stock Gudang</title></head><body>
            <h2>📋 Laporan Stok Gudang</h2>
            <p>Tanggal cetak: ${new Date().toLocaleString()}</p>
            ${tableClone.outerHTML}
            </body></html>
        `);
        win.document.head.appendChild(style);
        win.document.close();
        win.print();
    }

    function downloadCSV() {
        const searchTerm = document.getElementById('searchInput').value.toLowerCase().trim();
        let filtered = stockData.filter(item => {
            const matchNama = item.namaBarang.toLowerCase().includes(searchTerm);
            return matchNama;
        });
        const headers = ["No","Tanggal","Nama Barang","IN","OUT","TOTAL IN","TOTAL OUT"];
        const rows = filtered.map((item, idx) => [
            idx+1, item.tanggal, item.namaBarang,
            item.inVal, item.outVal, item.totalIn, item.totalOut
        ]);
        let csv = headers.join(",") + "\n";
        rows.forEach(row => {
            csv += row.map(cell => `"${String(cell).replace(/"/g, '""')}"`).join(",") + "\n";
        });
        const blob = new Blob(["\uFEFF" + csv], {type: "text/csv;charset=utf-8;"});
        const a = document.createElement('a');
        const url = URL.createObjectURL(blob);
        a.href = url;
        a.download = "stok_gudang_export.csv";
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
    }

    function saveToLocal() {
        localStorage.setItem("stockGudangAppData", JSON.stringify(stockData));
        localStorage.setItem("stockNextId", nextId);
    }

    function loadFromLocal() {
        const saved = localStorage.getItem("stockGudangAppData");
        if (saved && saved !== "undefined") {
            stockData = JSON.parse(saved);
            stockData.forEach(item => {
                item.inVal = Number(item.inVal);
                item.outVal = Number(item.outVal);
                item.totalIn = Number(item.totalIn) || 0;
                item.totalOut = Number(item.totalOut) || 0;
            });
            const savedId = localStorage.getItem("stockNextId");
            if (savedId) nextId = parseInt(savedId, 10);
            else {
                const maxId = stockData.reduce((a,b) => Math.max(a, b.id), 0);
                nextId = maxId + 1;
            }
            recalcAccumulatedTotals();
        } else {
            stockData = [];
            nextId = 1;
        }
        renderTable();
    }

    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if(m === '&') return '&amp;';
            if(m === '<') return '&lt;';
            if(m === '>') return '&gt;';
            return m;
        });
    }

    document.getElementById('tambahBtn').addEventListener('click', addTransaction);
    document.getElementById('clearAllBtn').addEventListener('click', clearAll);
    document.getElementById('printTableBtn').addEventListener('click', printStock);
    document.getElementById('downloadExcelBtn').addEventListener('click', downloadCSV);
    document.getElementById('searchInput').addEventListener('input', () => renderTable());
    document.getElementById('resetFilterBtn').addEventListener('click', () => {
        document.getElementById('searchInput').value = '';
        renderTable();
    });

    loadFromLocal();
</script>
</body>
</html>
