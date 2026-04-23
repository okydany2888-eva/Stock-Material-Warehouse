<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Manajemen Stok Gudang | In/Out </title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: system-ui, 'Segoe UI', 'Roboto', 'Helvetica Neue', sans-serif;
        }

        body {
            background: #e9f0f5;
            padding: 24px 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 28px;
            box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.15);
            overflow: hidden;
            padding: 20px 24px 32px 24px;
        }

        h1 {
            font-size: 1.9rem;
            font-weight: 600;
            background: linear-gradient(135deg, #1e3c72, #2b4c7c);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            margin-bottom: 6px;
            display: inline-block;
        }

        .header-sub {
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            flex-wrap: wrap;
            margin-bottom: 24px;
            border-bottom: 2px solid #e2e8f0;
            padding-bottom: 16px;
        }

        .title-area p {
            color: #4a5568;
            margin-top: 4px;
        }

        /* kontrol & filter */
        .filter-bar {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            align-items: center;
            justify-content: space-between;
            background: #f8fafc;
            padding: 16px 18px;
            border-radius: 28px;
            margin-bottom: 28px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.05);
        }

        .search-group {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            align-items: center;
            flex: 2;
        }

        .search-group label {
            font-weight: 600;
            color: #0f3b5f;
        }

        #searchInput {
            padding: 10px 16px;
            border-radius: 40px;
            border: 1px solid #cbd5e1;
            width: 260px;
            font-size: 0.9rem;
            transition: 0.2s;
            background: white;
        }

        #searchInput:focus {
            outline: none;
            border-color: #2c7da0;
            box-shadow: 0 0 0 3px rgba(44,125,160,0.2);
        }

        .action-buttons {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
        }

        button {
            background: white;
            border: none;
            padding: 8px 20px;
            border-radius: 40px;
            font-weight: 600;
            font-size: 0.85rem;
            cursor: pointer;
            transition: all 0.2s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.05);
            border: 1px solid #cbd5e1;
        }

        button i {
            font-style: normal;
            font-weight: bold;
            font-size: 1rem;
        }

        .btn-primary {
            background: #1e466e;
            border-color: #1e466e;
            color: white;
        }

        .btn-primary:hover {
            background: #0f3557;
            transform: translateY(-1px);
        }

        .btn-outline {
            background: white;
            border-color: #2c7da0;
            color: #2c7da0;
        }

        .btn-outline:hover {
            background: #eef6fc;
        }

        /* tabel container responsive */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 20px;
            border: 1px solid #e2edf2;
            background: white;
            margin-bottom: 28px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.9rem;
            min-width: 880px;
        }

        th {
            background: #eef3fa;
            padding: 15px 12px;
            text-align: center;
            font-weight: 700;
            color: #1e2f41;
            border-bottom: 2px solid #d4e0e9;
        }

        td {
            padding: 12px 10px;
            text-align: center;
            border-bottom: 1px solid #e9edf2;
            background-color: white;
        }

        tr:hover td {
            background-color: #fafeff;
        }

        .badge-in {
            background: #dcfce7;
            color: #15803d;
            font-weight: 600;
            padding: 4px 8px;
            border-radius: 30px;
            display: inline-block;
        }

        .badge-out {
            background: #ffe4e2;
            color: #b91c1c;
            font-weight: 600;
            padding: 4px 8px;
            border-radius: 30px;
        }

        .total-row {
            background: #f1f6fd;
            font-weight: 800;
            border-top: 2px solid #becddb;
        }

        .total-row td {
            background: #f1f6fd;
            font-weight: 700;
        }

        .footer-summary {
            display: flex;
            justify-content: flex-end;
            gap: 32px;
            padding: 12px 20px;
            background: #fef9e6;
            border-radius: 24px;
            margin-top: 10px;
            font-weight: 700;
            font-size: 1rem;
        }

        .empty-message {
            text-align: center;
            padding: 40px;
            color: #6c757d;
            font-style: italic;
        }

        @media (max-width: 680px) {
            .container {
                padding: 16px;
            }
            .filter-bar {
                flex-direction: column;
                align-items: stretch;
            }
            .search-group {
                width: 100%;
            }
            #searchInput {
                width: 100%;
            }
        }
    </style>
</head>
<body>
<div class="container">
    <div class="header-sub">
        <div class="title-area">
            <h1>📦 SISTEM STOK GUDANG</h1>
            <p>Kelola barang masuk (In) / keluar (Out) · Otomatis hitung total & stok</p>
        </div>
    </div>

    <div class="filter-bar">
        <div class="search-group">
            <label>🔍 Cari :</label>
            <input type="text" id="searchInput" placeholder="Nama barang atau kategori ...">
            <button id="resetSearchBtn" class="btn-outline" style="background:#f1f5f9;">↺ Reset</button>
        </div>
        <div class="action-buttons">
            <button id="printBtn" class="btn-outline">🖨️ Cetak / Print</button>
            <button id="downloadExcelBtn" class="btn-primary">📎 Download (CSV/Excel)</button>
        </div>
    </div>

    <div class="table-wrapper">
        <table id="stockTable">
            <thead>
                <tr>
                    <th>Tanggal</th>
                    <th>Nama Barang</th>
                    <th>Kategori</th>
                    <th>In (Masuk)</th>
                    <th>Out (Keluar)</th>
                    <th>Total In</th>
                    <th>Total Out</th>
                </tr>
                </thead>
                <tbody id="tableBody">
                    <!-- Data akan diisi oleh javascript -->
                </tbody>
                <tfoot id="tableFooter">
                    <!-- summary row khusus total in & out kumulatif -->
                </tfoot>
        </table>
    </div>
    <div class="footer-summary" id="extraSummary">
        <!-- dynamic grand total bisa ditampilkan juga disini -->
    </div>
</div>

<script>
    // ----- DATA AWAL STOK GUDANG (contoh) -----
    let stockData = [
        { tanggal: "2025-03-01", namaBarang: "BOPP", kategori: "Plastik", inQty: 10, outQty: 2 },
        { tanggal: "2025-03-03", namaBarang: "MAGNET", kategori: "Material", inQty: 50, outQty: 12 },
        { tanggal: "2025-03-05", namaBarang: "Plat Magnet", kategori: "Material", inQty: 30, outQty: 5 },
        { tanggal: "2025-03-07", namaBarang: "Plastik Wrapping", kategori: "Plastik", inQty: 8, outQty: 3 },
        { tanggal: "2025-03-10", namaBarang: "Plastik sampah", kategori: "Plastik", inQty: 5, outQty: 0 },
        { tanggal: "2025-03-12", namaBarang: "Lakban besar", kategori: "Plastik", inQty: 4, outQty: 1 },
        { tanggal: "2025-03-15", namaBarang: "Lakban kecil", kategori: "Plastik", inQty: 20, outQty: 8 },
        { tanggal: "2025-03-18", namaBarang: "Karet", kategori: "Karet", inQty: 12, outQty: 4 },
        { tanggal: "2025-03-20", namaBarang: "Masking Tape kecil", kategori: "Glue", inQty: 15, outQty: 6 },
        { tanggal: "2025-03-22", namaBarang: "Masking tape besar", kategori: "Glue", inQty: 25, outQty: 10 }
    ];

    // Fungsi untuk menghitung ulang Total In (akumulasi per barang dari awal hingga baris ke-i)
    // dan Total Out (akumulasi per barang) berdasarkan urutan data (diurutkan berdasarkan tanggal untuk logika akumulasi yang konsisten)
    // Agar akumulasi logis, kita urutkan data berdasarkan tanggal ascending dulu sebelum render,
    // tetapi tetap mempertahankan array asli? kita buat fungsi render yang mengurutkan per tanggal.
    function computeRunningTotals(dataArray) {
        // dataArray harus sudah dalam urutan kronologis (tanggal asc)
        const mapRunningIn = new Map();   // key: namaBarang -> total in sejauh ini
        const mapRunningOut = new Map();  // key: namaBarang -> total out sejauh ini
        
        const result = dataArray.map(item => {
            const nama = item.namaBarang;
            let prevIn = mapRunningIn.get(nama) || 0;
            let prevOut = mapRunningOut.get(nama) || 0;
            
            const newTotalIn = prevIn + item.inQty;
            const newTotalOut = prevOut + item.outQty;
            
            mapRunningIn.set(nama, newTotalIn);
            mapRunningOut.set(nama, newTotalOut);
            
            return {
                ...item,
                runningIn: newTotalIn,
                runningOut: newTotalOut
            };
        });
        return result;
    }

    // Variabel untuk menyimpan data yang sudah di proses (dengan running total)
    let processedData = [];

    // Fungsi untuk refresh data berdasarkan filter (pencarian nama/kategori) & urutkan tanggal
    function refreshDisplay() {
        // 1. Filter dulu berdasarkan search input
        const searchTerm = document.getElementById('searchInput').value.trim().toLowerCase();
        let filtered = [...stockData];
        if (searchTerm !== "") {
            filtered = filtered.filter(item => 
                item.namaBarang.toLowerCase().includes(searchTerm) || 
                item.kategori.toLowerCase().includes(searchTerm)
            );
        }
        // 2. Urutkan berdasarkan tanggal (ascending) agar akumulasi total in/out berurutan secara historis
        filtered.sort((a,b) => new Date(a.tanggal) - new Date(b.tanggal));
        
        // 3. Hitung running total in & out per barang
        const computed = computeRunningTotals(filtered);
        processedData = computed;
        
        // 4. Render tabel body
        renderTableBody(processedData);
        
        // 5. Render footer total keseluruhan (grand total in & out dari data yang tampil)
        renderFooterTotals(processedData);
    }
    
    // Render baris tbody
    function renderTableBody(data) {
        const tbody = document.getElementById('tableBody');
        if (!data.length) {
            tbody.innerHTML = `<tr><td colspan="7" class="empty-message">📭 Tidak ada data stok yang sesuai</td></tr>`;
            return;
        }
        
        let html = '';
        for (let row of data) {
            // format tanggal biar lebih rapi
            const formattedDate = row.tanggal;
            html += `<tr>
                        <td>${formattedDate}</td>
                        <td><strong>${escapeHtml(row.namaBarang)}</strong></td>
                        <td>${escapeHtml(row.kategori)}</td>
                        <td><span class="badge-in">+${row.inQty}</span></td>
                        <td><span class="badge-out">-${row.outQty}</span></td>
                        <td>${row.runningIn}</td>
                        <td>${row.runningOut}</td>
                     </tr>`;
        }
        tbody.innerHTML = html;
    }
    
    // Menampilkan ringkasan total IN dan total OUT secara keseluruhan dari data yang ditampilkan (setelah filter)
    function renderFooterTotals(data) {
        const foot = document.getElementById('tableFooter');
        let totalInAll = 0;
        let totalOutAll = 0;
        for (let row of data) {
            totalInAll += row.inQty;
            totalOutAll += row.outQty;
        }
        // Tampilkan di footer tabel (baris terakhir ringkasan)
        if (data.length > 0) {
            foot.innerHTML = `<tr class="total-row">
                                <td colspan="3" style="text-align:right; font-weight:800;">📊 TOTAL KESELURUHAN (Filter Aktif) :</td>
                                <td style="background:#eef2ff; font-weight:800;">+ ${totalInAll}</td>
                                <td style="background:#ffefef; font-weight:800;">- ${totalOutAll}</td>
                                <td colspan="2" style="background:#eef2ff; font-weight:800;">Stok Net : ${totalInAll - totalOutAll}</td>
                              </tr>`;
        } else {
            foot.innerHTML = '';
        }
        
        // juga update extra summary (opsional)
        const extraDiv = document.getElementById('extraSummary');
        if (data.length > 0) {
            extraDiv.innerHTML = `📌 Stok Masuk Total: <span style="color:#0e6b0e;">${totalInAll}</span> &nbsp;|&nbsp; Stok Keluar Total: <span style="color:#b91c1c;">${totalOutAll}</span> &nbsp;|&nbsp; 💎 Stok Bersih: <strong>${totalInAll - totalOutAll}</strong>`;
        } else {
            extraDiv.innerHTML = `📭 Tidak ada data untuk ditampilkan`;
        }
    }
    
    // helper sederhana untuk menghindari XSS
    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if (m === '&') return '&amp;';
            if (m === '<') return '&lt;';
            if (m === '>') return '&gt;';
            return m;
        });
    }
    
    // Fungsi download ke CSV / Excel (termasuk data yang tampil difilter dan total in/out running)
    function downloadCSV() {
        if (processedData.length === 0) {
            alert("Tidak ada data untuk di-download. Coba ubah filter atau tambah data.");
            return;
        }
        // menyiapkan header CSV
        const headers = ["Tanggal", "Nama Barang", "Kategori", "In (Masuk)", "Out (Keluar)", "Total In (Akumulasi)", "Total Out (Akumulasi)"];
        const rows = processedData.map(item => [
            item.tanggal,
            item.namaBarang,
            item.kategori,
            item.inQty,
            item.outQty,
            item.runningIn,
            item.runningOut
        ]);
        
        // baris total tambahan di akhir file (opsional)
        let totalInAll = 0, totalOutAll = 0;
        for (let item of processedData) {
            totalInAll += item.inQty;
            totalOutAll += item.outQty;
        }
        const summaryRow = ["", "TOTAL KESELURUHAN", "", totalInAll, totalOutAll, `Total In: ${totalInAll}`, `Total Out: ${totalOutAll}`];
        
        const csvContent = [headers, ...rows, summaryRow]
            .map(row => row.map(cell => `"${String(cell).replace(/"/g, '""')}"`).join(","))
            .join("\n");
        
        const blob = new Blob(["\uFEFF" + csvContent], { type: "text/csv;charset=utf-8;" });
        const link = document.createElement("a");
        const url = URL.createObjectURL(blob);
        link.setAttribute("href", url);
        link.setAttribute("download", "stock_gudang_export.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
    }
    
    // Fungsi print: mencetak tabel dengan gaya rapi (menyembunyikan tombol, filter, dll)
    function printTable() {
        const originalTitle = document.title;
        document.title = "Laporan Stok Gudang";
        // clone bagian container yang penting: judul + tabel + ringkasan
        const printContent = document.querySelector('.container').cloneNode(true);
        // Hapus tombol action & filter bar agar tidak ikut tercetak (tapi tetap elegan)
        const filterBarClone = printContent.querySelector('.filter-bar');
        if (filterBarClone) filterBarClone.remove();
        // hapus aksi tombol yang berlebihan di dalam tabel? tidak perlu tetapi kita bisa menyembunyikan tombol tambahan jika ada di printContent
        // Di container, cari tombol2 yang mungkin ada di header-sub? kita tetap izinkan judul.
        // tapi hapus juga tombol download cetak pada clone di bagian manapun? lebih baik hapus semua tombol dari action-buttons.
        const actionDivs = printContent.querySelectorAll('.action-buttons');
        actionDivs.forEach(div => div.remove());
        const btnReset = printContent.querySelector('#resetSearchBtn');
        if(btnReset) btnReset.remove();
        // Pastikan input search tidak tampil
        const searchGroupClone = printContent.querySelector('.search-group');
        if(searchGroupClone) searchGroupClone.remove();
        
        // Ganti extraSummary mungkin tidak perlu tapi biarkan ringkasan footer
        
        const printWindow = window.open('', '_blank');
        printWindow.document.write(`
            <html>
            <head>
                <title>Cetak Stok Gudang</title>
                <style>
                    body { font-family: 'Segoe UI', Arial, sans-serif; margin: 20px; padding: 20px; }
                    table { width: 100%; border-collapse: collapse; margin: 20px 0; }
                    th, td { border: 1px solid #aaa; padding: 8px 10px; text-align: center; }
                    th { background: #eef2fa; }
                    .total-row { background: #f5f0e0; font-weight: bold; }
                    .badge-in, .badge-out { font-weight: bold; }
                    .footer-summary { margin-top: 20px; background: #f9f2e0; padding: 10px; }
                    h1 { color: #1e3c72; }
                </style>
            </head>
            <body>
                ${printContent.outerHTML}
                <p style="text-align:center; margin-top:30px; font-size:12px;">Dicetak dari Sistem Stok Gudang - ${new Date().toLocaleString()}</p>
            </body>
            </html>
        `);
        printWindow.document.close();
        printWindow.print();
        document.title = originalTitle;
    }
    
    // Bisa tambah sample data atau reset data (tapi tidak ada fitur edit, namun user bisa di-extra)
    // Sediakan reset filter dan inisialisasi data awal, atau tambah data dinamis? sesuai permintaan tidak ada form tambah, 
    // namun user bisa mengubah array stockData secara langsung? lebih baik menyediakan contoh dan fungsional.
    // Untuk memudahkan uji fitur, Saya bisa menyisipkan tombol tambah (opsional tidak diminta, tapi disediakan simpel?) 
    // Tidak mengganggu requirement: Print, download, pencarian, otomatis menjumlahkan In/Out di akhir.

    // Inisialisasi listener dan event
    document.addEventListener('DOMContentLoaded', () => {
        refreshDisplay();
        
        const searchInput = document.getElementById('searchInput');
        searchInput.addEventListener('input', () => refreshDisplay());
        
        const resetBtn = document.getElementById('resetSearchBtn');
        resetBtn.addEventListener('click', () => {
            document.getElementById('searchInput').value = '';
            refreshDisplay();
        });
        
        const downloadBtn = document.getElementById('downloadExcelBtn');
        downloadBtn.addEventListener('click', downloadCSV);
        
        const printBtn = document.getElementById('printBtn');
        printBtn.addEventListener('click', printTable);
    });
    
    // Untuk menambah data contoh agar lebih terlihat dinamis? (tidak diwajibkan, tapi agar user bisa melihat akumulasi out/in)
    // Tambahkan sedikit interaktivitas untuk menambah transaksi stok? TIDAK diminta. Tapi karena kebutuhan menggunakan 'total in' dan 'total out' kumulatif per barang,
    // serta data awal lengkap. Jika ingin menambah contoh, user bisa edit manual di console? Tetapi kami hadirkan sesuai spek.
    // Namun karena pencarian berdasarkan kategori & nama, tabel akan menampilkan akumulasi dari urutan sejarah (berdasarkan tanggal) 
    // untuk setiap barang: total in dan total out berjalan indikasi sisa stok per barang, sesuai dengan 'total in' dan 'total out'.
    // Di setiap baris, "Total In" = kumulatif barang tersebut dari awal sampai baris itu, "Total Out" juga.
    
    // Untuk memenuhi agar out/in dijumlah secara otomatis di akhir (grand total), sudah kami tampilkan baik di footer tabel maupun summary bawah.
    // Capability: Print, download, filter kategori/nama OK.
    // NOTE: Agar tabel terlihat rapi, sudah disertakan running total yang benar dan stabil.
    
    // Jika ingin data lebih dinamis, tetapi tetap mengikuti permintaan tidak perlu edit, tapi fitur pencarian nama kategori telah maksimal.
    console.log("Sistem stok gudang siap, dengan akumulasi In/Out, filter, print, download excel/csv");
</script>
</body>
</html>
