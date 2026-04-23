<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Sistem Manajemen Stok Gudang | In/Out + Total Otomatis</title>
    <style>
        * {
            box-sizing: border-box;
            font-family: system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif;
        }

        body {
            background: #e9eff7;
            margin: 0;
            padding: 24px 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 32px;
            box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.15);
            overflow: hidden;
            padding: 20px 28px 32px 28px;
        }

        h1 {
            font-size: 1.8rem;
            margin-top: 0;
            margin-bottom: 0.25rem;
            font-weight: 600;
            color: #1a2c3e;
            display: flex;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
            justify-content: space-between;
        }

        h1 small {
            font-size: 0.85rem;
            font-weight: normal;
            background: #f0f4fa;
            padding: 6px 14px;
            border-radius: 40px;
            color: #2c5b7c;
        }

        hr {
            margin: 20px 0;
            border: none;
            height: 2px;
            background: linear-gradient(90deg, #cbdbe0, transparent);
        }

        /* form panel */
        .form-card {
            background: #f8fafd;
            border-radius: 28px;
            padding: 20px 24px;
            margin-bottom: 30px;
            border: 1px solid #dfe6ef;
            box-shadow: 0 2px 6px rgba(0,0,0,0.02);
        }

        .form-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 18px;
            align-items: flex-end;
        }

        .input-group {
            flex: 1 1 160px;
            min-width: 130px;
        }

        .input-group label {
            display: block;
            font-weight: 600;
            font-size: 0.75rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #4a627a;
            margin-bottom: 6px;
        }

        .input-group input, .input-group select {
            width: 100%;
            padding: 10px 12px;
            border-radius: 18px;
            border: 1px solid #cbdbe0;
            background: white;
            font-size: 0.9rem;
            transition: 0.2s;
        }

        .input-group input:focus, .input-group select:focus {
            outline: none;
            border-color: #2c7da0;
            box-shadow: 0 0 0 3px rgba(44,125,160,0.2);
        }

        button {
            background: #2c7da0;
            border: none;
            padding: 10px 20px;
            border-radius: 40px;
            font-weight: 600;
            color: white;
            cursor: pointer;
            transition: 0.2s;
            font-size: 0.85rem;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.05);
        }

        button:hover {
            background: #1f5e7a;
            transform: scale(0.97);
        }

        .btn-outline {
            background: white;
            border: 1px solid #2c7da0;
            color: #2c7da0;
        }

        .btn-outline:hover {
            background: #eef4fc;
            transform: none;
        }

        .btn-danger {
            background: #bc6c25;
        }

        .btn-danger:hover {
            background: #9e561d;
        }

        .action-bar {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 15px;
            margin-bottom: 24px;
            margin-top: 12px;
        }

        .search-box {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
            align-items: center;
            background: #f1f5f9;
            padding: 8px 16px;
            border-radius: 60px;
        }

        .search-box input, .search-box select {
            border: none;
            background: transparent;
            padding: 8px 5px;
            font-size: 0.9rem;
            min-width: 160px;
        }

        .search-box input:focus, .search-box select:focus {
            outline: none;
        }

        .table-wrapper {
            overflow-x: auto;
            border-radius: 24px;
            border: 1px solid #e2e8f0;
            background: white;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.85rem;
        }

        th {
            background: #eef2f8;
            padding: 14px 10px;
            text-align: center;
            font-weight: 700;
            color: #1f3b4c;
            border-bottom: 1px solid #d4dfea;
        }

        td {
            padding: 12px 8px;
            text-align: center;
            border-bottom: 1px solid #ecf1f6;
            vertical-align: middle;
        }

        .total-row {
            background: #eef3fc;
            font-weight: 700;
            border-top: 2px solid #cbdde9;
        }

        .total-row td {
            font-weight: 700;
            background: #eef3fc;
        }

        .badge {
            background: #e4edf5;
            padding: 4px 10px;
            border-radius: 40px;
            font-size: 0.7rem;
            font-weight: 600;
        }

        .footer-buttons {
            display: flex;
            flex-wrap: wrap;
            justify-content: flex-end;
            gap: 15px;
            margin-top: 28px;
        }

        @media (max-width: 700px) {
            .container {
                padding: 16px;
            }
            .form-grid {
                flex-direction: column;
                align-items: stretch;
            }
            .input-group {
                width: 100%;
            }
            .action-bar {
                flex-direction: column;
                align-items: stretch;
            }
        }

        .delete-btn {
            background: none;
            border: none;
            color: #bc6c25;
            font-size: 1.2rem;
            cursor: pointer;
            padding: 4px 8px;
            box-shadow: none;
        }
        .delete-btn:hover {
            background: #fff0e5;
            transform: none;
            color: #a05115;
        }
        button:active { transform: scale(0.96);}
    </style>
</head>
<body>
<div class="container">
    <h1>
        📦 Manajemen Stok Gudang
        <small>In + Out</small>
    </h1>
    <hr>

    <!-- FORM INPUT DATA -->
    <div class="form-card">
        <div class="form-grid">
            <div class="input-group">
                <label>🗓️ Tanggal</label>
                <input type="date" id="tanggalInput" required>
            </div>
            <div class="input-group">
                <label>🏷️ Nama Barang</label>
                <input type="text" id="namaBarangInput" placeholder="Contoh: Plastik, lakban, dll" autocomplete="off">
            </div>
            <div class="input-group">
                <label>📂 Kategori</label>
                <select id="kategoriInput">
                    <option value="BOPP">BOPP</option>
                    <option value="Magnet">Magnet</option>
                    <option value="Plat Magnet">Plat Magnet</option>
                    <option value="Lakban besar">Lakban Besar</option>
                    <option value="Lakban Kecil">Lakban Kecil</option>
                    <option value="Masking Tape kecil">Masking Tape Kecil</option>
                    <option value="Masking Tape Besar">Masking Tape Besar</option>
                    <option value="Plastik Sampah">Plastik Sampah</option>
                    <option value="Plastik Wrapping">Plastik Wrapping</option>
                    <option value="Karet">Karet</option>
                    <option value="Double Tape ">Double Tape</option>
                </select>
            </div>
            <div class="input-group">
                <label>📥 IN (Jumlah Masuk)</label>
                <input type="number" id="inInput" value="0" min="0" step="1">
            </div>
            <div class="input-group">
                <label>📤 OUT (Jumlah Keluar)</label>
                <input type="number" id="outInput" value="0" min="0" step="1">
            </div>
            <div class="input-group">
                <button id="tambahBtn">Tambah barang</button>
            </div>
        </div>
        <div style="font-size: 0.7rem; margin-top: 12px; color: #4a6e8a;">* Setiap baris adalah transaksi harian. Kolom Total In / Total Out akan diakumulasi per baris secara otomatis.</div>
    </div>

    <!-- FITUR PENCARIAN & DOWNLOAD/PRINT -->
    <div class="action-bar">
        <div class="search-box">
            <span>🔍</span>
            <input type="text" id="searchInput" placeholder="Cari Nama Barang / Kategori..." style="min-width: 180px;">
            <select id="filterKategoriSearch">
                <option value="">Semua Kategori</option>
                <option value="BOPP">BOPP</option>
                <option value="Magnet">Magnet</option>
                <option value="Plat Magnet">Plat Magnet</option>
                <option value="Lakban besar">Lakban Besar</option>
                <option value="Lakban Kecil">Lakban Kecil</option>
                <option value="Masking Tape kecil">Masking Tape Kecil</option>
                <option value="Masking Tape Besar">Masking Tape Besar</option>
                <option value="Plastik Sampah">Plastik Sampah</option>
                <option value="Plastik Wrapping">Plastik Wrapping</option>
                <option value="Karet">Karet</option>
                <option value="Double Tape ">Double Tape</option>
            </select>
            <button id="resetFilterBtn" class="btn-outline" style="margin-left: 6px;">Reset</button>
        </div>
        <div style="display: flex; gap: 12px;">
            <button id="printTableBtn">🖨️ Print</button>
            <button id="downloadExcelBtn">⬇️ Download (CSV)</button>
        </div>
    </div>

    <!-- TABEL STOK -->
    <div class="table-wrapper" id="tableWrapper">
        <table id="stockTable">
            <thead>
                <tr>
                    <th>No</th><th>Tanggal</th><th>Nama Barang</th><th>Kategori</th>
                    <th>IN (Masuk)</th><th>OUT (Keluar)</th>
                    <th>📊 TOTAL IN</th><th>📉 TOTAL OUT</th>
                    <th>Aksi</th>
                </tr>
                <tr id="headerTotalRow" style="background:#f9fbfd;">
                    <th colspan="7" style="text-align:right">✨ Keseluruhan Stok (Total In - Total Out) :</th>
                    <th colspan="2" id="grandTotalStockCell" style="background:#e2eef9; font-size:1rem;">0</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <!-- Data akan diisi javascript -->
                <tr><td colspan="9" style="text-align:center; padding: 40px;">Belum ada data, silakan tambah transaksi</td></tr>
            </tbody>
            <tfoot id="tableFootTotal">
                <!-- dynamic footer total keseluruhan in/out -->
            </tfoot>
        </table>
    </div>
    <div class="footer-buttons">
        <button id="clearAllBtn" class="btn-danger">🗑️ Hapus Semua Data</button>
    </div>
</div>

<script>
    // ======================= DATA STORAGE =======================
    let stockData = [];     // setiap item: { id, tanggal, namaBarang, kategori, inVal, outVal, totalIn, totalOut }
    let nextId = 1;

    // Helper ambil tanggal hari ini untuk default
    function getTodayDate() {
        const today = new Date();
        const yyyy = today.getFullYear();
        const mm = String(today.getMonth() + 1).padStart(2, '0');
        const dd = String(today.getDate()).padStart(2, '0');
        return `${yyyy}-${mm}-${dd}`;
    }

    // set default tanggal hari ini
    document.getElementById('tanggalInput').value = getTodayDate();

    // Fungsi untuk menghitung ulang totalIn dan totalOut berdasarkan histori (akumulasi per barang)
    // Konsep: Untuk setiap transaksi, total_in = akumulasi IN untuk barang tsb hingga transaksi tsb (urut berdasarkan id atau tanggal)
    // Biar lebih intuitif: Total In per baris = jumlah IN dari semua transaksi barang yang SAMA (nama barang) SEBELUMNYA + IN saat ini.
    // Total Out per baris = akumulasi OUT untuk barang tsb hingga baris itu.
    // Urutan berdasarkan tanggal (jika tanggal sama maka berdasarkan id). Ini memberikan laporan riwayat yang logis.
    function recalcAccumulatedTotals() {
        // group berdasarkan nama barang (case sensitive)
        const barangMap = new Map(); // key: namaBarang -> array of indices
        for (let i = 0; i < stockData.length; i++) {
            const item = stockData[i];
            const key = item.namaBarang;
            if (!barangMap.has(key)) barangMap.set(key, []);
            barangMap.get(key).push({ index: i, tanggal: item.tanggal, id: item.id });
        }
        // untuk tiap barang, urutkan berdasarkan tanggal kemudian id (agar akumulasi sesuai kronologi)
        for (let [_, arr] of barangMap.entries()) {
            arr.sort((a,b) => {
                if (a.tanggal === b.tanggal) return a.id - b.id;
                return a.tanggal.localeCompare(b.tanggal);
            });
            let runningIn = 0;
            let runningOut = 0;
            for (let entry of arr) {
                const idx = entry.index;
                const item = stockData[idx];
                runningIn += item.inVal;
                runningOut += item.outVal;
                item.totalIn = runningIn;
                item.totalOut = runningOut;
            }
        }
    }

    // fungsi render tabel + filter + total akhir
    function renderTable() {
        const searchTerm = document.getElementById('searchInput').value.toLowerCase().trim();
        const kategoriFilter = document.getElementById('filterKategoriSearch').value;
        
        let filteredData = stockData.filter(item => {
            const matchNama = item.namaBarang.toLowerCase().includes(searchTerm);
            const matchKategori = kategoriFilter === "" || item.kategori === kategoriFilter;
            return matchNama && matchKategori;
        });

        const tbody = document.getElementById('tableBody');
        if (filteredData.length === 0) {
            tbody.innerHTML = `<tr><td colspan="9" style="text-align:center; padding: 40px;">📭 Tidak ada data sesuai pencarian / kosong</td></tr>`;
            // update total keseluruhan di footer dan grand total tetap 0
            updateTotalFooter([]);
            document.getElementById('grandTotalStockCell').innerText = "0";
            return;
        }

        let htmlRows = '';
        filteredData.forEach((item, idx) => {
            // no urut berdasarkan tampilan
            const number = idx+1;
            htmlRows += `
                <tr>
                    <td>${number}</td>
                    <td>${item.tanggal}</td>
                    <td style="font-weight:500;">${escapeHtml(item.namaBarang)}</td>
                    <td><span class="badge">${escapeHtml(item.kategori)}</span></td>
                    <td>${item.inVal}</td>
                    <td>${item.outVal}</td>
                    <td style="background:#f4fafd; font-weight:500;">${item.totalIn}</td>
                    <td style="background:#fef4ea; font-weight:500;">${item.totalOut}</td>
                    <td><button class="delete-btn" data-id="${item.id}">🗑️ Hapus</button></td>
                </tr>
            `;
        });
        tbody.innerHTML = htmlRows;

        // hitung total keseluruhan dari FILTER (biar user lihat stok akhir dari data yg tampil)
        let overallInFilter = 0;
        let overallOutFilter = 0;
        filteredData.forEach(item => {
            overallInFilter += item.inVal;
            overallOutFilter += item.outVal;
        });
        const netStockFilter = overallInFilter - overallOutFilter;
        
        // Tampilkan total stok akhir filter di grandTotalStockCell (header special row)
        document.getElementById('grandTotalStockCell').innerHTML = `<strong>${netStockFilter}</strong> (Net Stok)`;
        
        // Update footer total in/out keseluruhan filter
        updateTotalFooter(filteredData);
        
        // Event listener untuk tombol hapus
        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = parseInt(btn.getAttribute('data-id'));
                deleteTransactionById(id);
            });
        });
    }
    
    function updateTotalFooter(filteredArray) {
        let totalInAll = 0;
        let totalOutAll = 0;
        filteredArray.forEach(item => {
            totalInAll += item.inVal;
            totalOutAll += item.outVal;
        });
        const netAll = totalInAll - totalOutAll;
        const foot = document.getElementById('tableFootTotal');
        if (!foot) return;
        foot.innerHTML = `
            <tr style="background:#f2f6fc; font-weight:600;">
                <td colspan="4" style="text-align:right;">📌 TOTAL (Filter) :</td>
                <td><strong>${totalInAll}</strong></td>
                <td><strong>${totalOutAll}</strong></td>
                <td colspan="2"><strong>Total In - Out = ${netAll}</strong></td>
                <td></td>
            </tr>
        `;
    }
    
    function deleteTransactionById(id) {
        stockData = stockData.filter(item => item.id !== id);
        if(stockData.length === 0) nextId = 1;
        else {
            // optional: reset nextId minimal lebih besar dari max id yg ada
            const maxId = stockData.reduce((max, item) => Math.max(max, item.id), 0);
            if(nextId <= maxId) nextId = maxId + 1;
        }
        recalcAccumulatedTotals();
        renderTable();
        saveToLocal();
    }
    
    // fungsi menambah transaksi baru
    function addTransaction() {
        const tanggal = document.getElementById('tanggalInput').value;
        const namaBarang = document.getElementById('namaBarangInput').value.trim();
        const kategori = document.getElementById('kategoriInput').value;
        let inVal = parseInt(document.getElementById('inInput').value, 10);
        let outVal = parseInt(document.getElementById('outInput').value, 10);
        
        if (isNaN(inVal)) inVal = 0;
        if (isNaN(outVal)) outVal = 0;
        if (!tanggal) {
            alert("Tanggal wajib diisi!");
            return;
        }
        if (namaBarang === "") {
            alert("Nama barang tidak boleh kosong!");
            return;
        }
        if (inVal === 0 && outVal === 0) {
            alert("Minimal salah satu IN atau OUT harus diisi > 0");
            return;
        }
        
        const newId = nextId++;
        const newItem = {
            id: newId,
            tanggal: tanggal,
            namaBarang: namaBarang,
            kategori: kategori,
            inVal: inVal,
            outVal: outVal,
            totalIn: 0,
            totalOut: 0
        };
        stockData.push(newItem);
        // urutkan secara global berdasarkan tanggal kemudian id untuk akumulasi yang stabil
        stockData.sort((a,b) => {
            if(a.tanggal === b.tanggal) return a.id - b.id;
            return a.tanggal.localeCompare(b.tanggal);
        });
        recalcAccumulatedTotals();
        renderTable();
        saveToLocal();
        
        // reset input in/out tapi biarkan nama barang dan tanggal opsional? lebih praktis in out jadi 0
        document.getElementById('inInput').value = 0;
        document.getElementById('outInput').value = 0;
        document.getElementById('namaBarangInput').value = '';
        document.getElementById('namaBarangInput').focus();
        // tanggal tetap hari ini? biarkan yang terpilih
        // optional set kategori default tetap
    }
    
    function clearAllData() {
        if(confirm("⚠️ Hapus SEMUA data transaksi? Tindakan ini permanen.")) {
            stockData = [];
            nextId = 1;
            recalcAccumulatedTotals();
            renderTable();
            saveToLocal();
        }
    }
    
    // print area (hanya tabel stok yang terlihat + judul)
    function printTable() {
        const originalTitle = document.title;
        document.title = "Laporan Stok Gudang";
        const printWindow = window.open('', '_blank');
        const tableContent = document.getElementById('stockTable').cloneNode(true);
        // hapus perhaps tombol aksi di kolom hapus? tapi tetap boleh print, tidak masalah
        const style = document.createElement('style');
        style.textContent = `
            body { font-family: sans-serif; margin: 20px; }
            table { border-collapse: collapse; width: 100%; }
            th, td { border: 1px solid #aaa; padding: 8px; text-align: center; }
            th { background: #f0f0f0; }
            .badge { background: #eef; padding: 2px 6px; }
            .delete-btn { display: none; }
            .total-row { background: #f9f9f9;}
        `;
        printWindow.document.write(`
            <html><head><title>Cetak Stok Gudang</title></head><body>
            <h2>📋 Laporan Stok Barang</h2>
            <p>Tanggal cetak: ${new Date().toLocaleString()}</p>
            ${tableContent.outerHTML}
            </body></html>
        `);
        printWindow.document.head.appendChild(style);
        printWindow.document.close();
        printWindow.print();
        document.title = originalTitle;
    }
    
    // download CSV (termasuk data yang sedang difilter, supaya sesuai ekspor)
    function downloadCSV() {
        const searchTerm = document.getElementById('searchInput').value.toLowerCase().trim();
        const kategoriFilter = document.getElementById('filterKategoriSearch').value;
        let filtered = stockData.filter(item => {
            const matchNama = item.namaBarang.toLowerCase().includes(searchTerm);
            const matchKategori = kategoriFilter === "" || item.kategori === kategoriFilter;
            return matchNama && matchKategori;
        });
        // mapping data untuk ekspor total in, total out berdasarkan akumulasi terbaru (sudah dihitung)
        const headers = ["No","Tanggal","Nama Barang","Kategori","IN","OUT","TOTAL IN","TOTAL OUT"];
        const rows = filtered.map((item, idx) => [
            idx+1,
            item.tanggal,
            item.namaBarang,
            item.kategori,
            item.inVal,
            item.outVal,
            item.totalIn,
            item.totalOut
        ]);
        const csvContent = [headers, ...rows].map(row => row.map(cell => `"${String(cell).replace(/"/g, '""')}"`).join(",")).join("\n");
        const blob = new Blob(["\uFEFF" + csvContent], { type: "text/csv;charset=utf-8;" });
        const link = document.createElement("a");
        const url = URL.createObjectURL(blob);
        link.href = url;
        link.setAttribute("download", "stok_gudang_export.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
    }
    
    // local storage
    function saveToLocal() {
        localStorage.setItem("stockGudangData", JSON.stringify(stockData));
        localStorage.setItem("stockNextId", nextId);
    }
    
    function loadFromLocal() {
        const saved = localStorage.getItem("stockGudangData");
        if(saved) {
            stockData = JSON.parse(saved);
            // rekonstruksi tipe data number
            stockData.forEach(item => {
                item.inVal = Number(item.inVal);
                item.outVal = Number(item.outVal);
                item.totalIn = Number(item.totalIn) || 0;
                item.totalOut = Number(item.totalOut) || 0;
            });
            const savedId = localStorage.getItem("stockNextId");
            if(savedId) nextId = parseInt(savedId, 10);
            else {
                const maxId = stockData.reduce((max,item)=> Math.max(max, item.id),0);
                nextId = maxId+1;
            }
            recalcAccumulatedTotals();
        } else {
            // sample data awal biar tidak kosong (optional demo)
            stockData = [];
            nextId = 1;
            const demo = [
                { id: nextId++, tanggal: getTodayDate(), namaBarang: "Cat Tembok", kategori: "Material Bangunan", inVal: 20, outVal: 5, totalIn:0, totalOut:0 },
                { id: nextId++, tanggal: getTodayDate(), namaBarang: "Pensil 2B", kategori: "Alat Tulis", inVal: 100, outVal: 30, totalIn:0, totalOut:0 },
                { id: nextId++, tanggal: getTodayDate(), namaBarang: "Mouse Wireless", kategori: "Elektronik", inVal: 15, outVal: 3, totalIn:0, totalOut:0 }
            ];
            stockData.push(...demo);
            recalcAccumulatedTotals();
            saveToLocal();
        }
        renderTable();
    }
    
    function escapeHtml(str) {
        if(!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if(m === '&') return '&amp;';
            if(m === '<') return '&lt;';
            if(m === '>') return '&gt;';
            return m;
        });
    }
    
    // filter & search event binding
    document.getElementById('searchInput').addEventListener('input', () => renderTable());
    document.getElementById('filterKategoriSearch').addEventListener('change', () => renderTable());
    document.getElementById('resetFilterBtn').addEventListener('click', () => {
        document.getElementById('searchInput').value = '';
        document.getElementById('filterKategoriSearch').value = '';
        renderTable();
    });
    document.getElementById('tambahBtn').addEventListener('click', addTransaction);
    document.getElementById('clearAllBtn').addEventListener('click', clearAllData);
    document.getElementById('printTableBtn').addEventListener('click', printTable);
    document.getElementById('downloadExcelBtn').addEventListener('click', downloadCSV);
    
    // inisialisasi
    loadFromLocal();
</script>
</body>
</html>
