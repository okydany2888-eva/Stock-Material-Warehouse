<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>Manajemen Gudang - Sistem Daily Activity</title>
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif;
        }
        body {
            background: #f0f2f5;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }
        .mode-selection-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(8px);
            z-index: 10000;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .mode-selection-card {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 32px;
            padding: 32px 28px;
            width: 90%;
            max-width: 380px;
            text-align: center;
            box-shadow: 0 25px 50px -12px rgba(0,0,0,0.25);
            animation: fadeInUp 0.4s ease-out;
        }
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .mode-selection-card h2 { font-size: 1.6rem; color: #008080; margin-bottom: 8px; }
        .mode-selection-card .subtitle { font-size: 0.8rem; color: #666; margin-bottom: 28px; border-bottom: 1px solid #eee; padding-bottom: 16px; }
        .mode-buttons { display: flex; flex-direction: column; gap: 16px; margin-bottom: 20px; }
        .mode-btn { padding: 16px 20px; border-radius: 60px; font-size: 1.1rem; font-weight: 600; cursor: pointer; border: none; transition: 0.2s; }
        .mode-btn:active { transform: scale(0.97); }
        .mode-btn-admin { background: #008080; color: white; box-shadow: 0 4px 14px rgba(0,128,128,0.3); }
        .mode-btn-admin:hover { background: #006666; }
        .mode-btn-operator { background: white; color: #008080; border: 2px solid #008080; }
        .mode-note { font-size: 0.7rem; color: #999; margin-top: 20px; padding-top: 16px; border-top: 1px solid #eee; }
        
        .app-wrapper { display: flex; height: 100%; width: 100%; position: relative; }
        .sidebar {
            width: 200px;
            background: linear-gradient(180deg, #008080 0%, #006666 100%);
            color: white;
            display: flex;
            flex-direction: column;
            box-shadow: 4px 0 20px rgba(0,0,0,0.15);
            transition: transform 0.3s;
            z-index: 1000;
            position: relative;
            overflow-y: auto;
            flex-shrink: 0;
        }
        .sidebar.hidden { transform: translateX(-100%); }
        .sidebar::-webkit-scrollbar { width: 5px; }
        .sidebar::-webkit-scrollbar-track { background: #1a8a8a; }
        .sidebar::-webkit-scrollbar-thumb { background: #40c4c4; border-radius: 10px; }
        .logo-area { padding: 20px 16px; border-bottom: 1px solid rgba(255,255,255,0.15); margin-bottom: 16px; }
        .logo-area h2 { font-weight: 600; font-size: 1.3rem; display: flex; align-items: center; gap: 8px; }
        .logo-area h2:before { content: "🏭"; font-size: 24px; }
        .logo-area p { font-size: 0.65rem; opacity: 0.7; margin-top: 4px; }
        .user-role { background: rgba(255,255,255,0.15); margin: 10px 12px; padding: 8px; border-radius: 10px; text-align: center; font-size: 0.7rem; }
        .nav-menu { flex: 1; display: flex; flex-direction: column; gap: 4px; padding: 0 12px 20px 12px; }
        .nav-item {
            display: flex;
            align-items: center;
            padding: 12px 16px;
            border-radius: 10px;
            font-weight: 500;
            font-size: 0.85rem;
            cursor: pointer;
            transition: all 0.2s ease;
            color: #e0f0f0;
            -webkit-tap-highlight-color: transparent;
        }
        .nav-item:active { transform: scale(0.97); background: rgba(255,255,255,0.2); }
        .nav-item:hover { background: rgba(255,255,255,0.12); color: white; }
        .nav-item.active { background: #40c4c4; color: white; box-shadow: 0 4px 8px rgba(0,0,0,0.2); }
        .nav-item-logout { margin-top: 20px; border-top: 1px solid rgba(255,255,255,0.2); padding-top: 16px; color: #ffcccc; }
        .nav-item-logout:hover { background: rgba(255,100,100,0.2); }
        
        .main-content { flex: 1; display: flex; flex-direction: column; overflow-y: auto; background: #f4f7fc; position: relative; }
        .top-bar { background: white; padding: 12px 20px; box-shadow: 0 2px 6px rgba(0,0,0,0.05); border-bottom: 1px solid #e2e8f0; display: flex; align-items: center; gap: 15px; flex-wrap: wrap; position: sticky; top: 0; z-index: 100; }
        .menu-toggle-btn { background: transparent; border: none; color: #008080; font-size: 1.4rem; cursor: pointer; padding: 6px 10px; border-radius: 10px; width: 36px; height: 36px; }
        .menu-toggle-btn:active { transform: scale(0.94); background: #eef2f8; }
        .title-area { flex: 1; }
        .title-area h1 { font-size: 1.2rem; font-weight: 600; color: #1e2a3a; }
        .title-area .sub { font-size: 0.65rem; color: #5b6e8c; margin-top: 4px; }
        
        .container { 
            flex: 1;
            display: flex;
            flex-direction: column;
            padding: 16px 20px;
            gap: 16px;
            min-height: 0;
        }
        
        .scrollable-content {
            flex: 1;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 16px;
        }
        
        .card-panel { 
            background: white; 
            border-radius: 16px; 
            padding: 16px; 
            box-shadow: 0 2px 8px rgba(0,0,0,0.03); 
            border: 1px solid #eef2f6; 
            overflow-x: auto; 
        }
        .section-title { 
            font-size: 0.9rem; 
            font-weight: 600; 
            margin-bottom: 16px; 
            color: #1e2f3e; 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            flex-wrap: wrap; 
            gap: 10px; 
        }
        .btn-group-right { display: flex; gap: 4px; align-items: center; flex-wrap: wrap; }
        .icon-btn { background: transparent; border: none; font-size: 0.9rem; cursor: pointer; padding: 5px; border-radius: 6px; width: 28px; height: 28px; }
        .icon-btn:active { transform: scale(0.94); background: #eef2f8; }
        .icon-excel { color: #1e6f3f; }
        .icon-print { color: #6c757d; }
        .icon-add { color: #008080; font-size: 0.85rem; font-weight: normal; }
        .btn-text { background: transparent; border: none; color: #008080; font-weight: 500; font-size: 0.7rem; cursor: pointer; padding: 5px 10px; border-radius: 20px; }
        .btn-text:active { transform: scale(0.96); background: #eaf4fc; }
        .btn-operator-wa { background: #25D366; color: white; border: none; font-weight: 500; font-size: 0.6rem; cursor: pointer; padding: 4px 8px; border-radius: 20px; display: inline-flex; align-items: center; gap: 3px; }
        .btn-operator-add { background: transparent; border: none; color: #008080; font-weight: 500; font-size: 0.65rem; cursor: pointer; padding: 4px 8px; border-radius: 20px; }
        .btn-operator-add:active { transform: scale(0.96); background: #eaf4fc; }
        .btn-filter { background: #008080; border: none; color: white; padding: 4px 8px; border-radius: 20px; font-size: 0.6rem; cursor: pointer; }
        
        /* Date Navigator - Lurus Sejajar */
        .date-navigator {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
            padding: 12px 0;
            margin-top: 8px;
            border-top: 1px solid #eef2f6;
        }
        .date-nav-btn {
            background: transparent;
            border: 1px solid #008080;
            color: #008080;
            font-size: 0.7rem;
            font-weight: 500;
            cursor: pointer;
            padding: 6px 14px;
            border-radius: 30px;
            transition: all 0.2s;
            white-space: nowrap;
        }
        .date-nav-btn:active { transform: scale(0.96); background: #eef2f8; }
        .date-nav-btn:hover { background: #eef2f8; }
        .current-date-display {
            font-weight: 600;
            color: #008080;
            font-size: 0.85rem;
            background: #e0f2f1;
            padding: 6px 20px;
            border-radius: 30px;
            display: inline-block;
            white-space: nowrap;
        }
        .daily-tag { 
            background: #ff9800; 
            color: white; 
            font-size: 0.6rem; 
            margin-left: 8px; 
            padding: 2px 8px; 
            border-radius: 20px; 
            font-weight: normal;
        }
        
        /* Sync Section */
        .sync-section {
            background: #e8f5e9;
            border-radius: 12px;
            padding: 10px 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
            border: 1px solid #c8e6c9;
            margin-top: auto;
            flex-shrink: 0;
        }
        .sync-title { font-size: 0.65rem; font-weight: 600; color: #2e7d32; display: flex; align-items: center; gap: 6px; }
        .sync-buttons { display: flex; gap: 6px; flex-wrap: wrap; }
        .sync-btn { background: #008080; border: none; color: white; padding: 5px 10px; border-radius: 20px; cursor: pointer; font-size: 0.65rem; }
        .sync-btn:active { transform: scale(0.96); }
        .sync-btn-wa { background: #25D366; }
        .sync-btn-warning { background: #ff9800; }
        
        /* ========= TABEL YANG DIPERBAIKI ========= */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 12px;
            margin-top: 8px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.75rem;
            min-width: 700px;
        }
        th {
            text-align: left;
            padding: 12px 10px;
            background: #f8fafc;
            color: #2c3e50;
            font-weight: 600;
            border-bottom: 2px solid #e2e8f0;
            font-size: 0.75rem;
        }
        td {
            text-align: left;
            padding: 10px 10px;
            border-bottom: 1px solid #eef2f8;
            color: #334155;
            vertical-align: middle;
        }
        tr:hover {
            background: #fafbfc;
        }
        .badge { 
            background: #e2f0f7; 
            padding: 4px 10px; 
            border-radius: 40px; 
            font-size: 0.65rem; 
            font-weight: 600; 
            color: #008080; 
            display: inline-block;
            white-space: nowrap;
        }
        
        /* Tabs */
        .division-tabs { 
            display: flex; 
            gap: 8px; 
            flex-wrap: wrap; 
            margin-bottom: 16px; 
            border-bottom: 1px solid #e2e8f0; 
            padding-bottom: 10px; 
        }
        .division-tab-btn { 
            background: #f1f5f9; 
            border: none; 
            padding: 6px 16px; 
            border-radius: 40px; 
            cursor: pointer; 
            font-weight: 500; 
            font-size: 0.7rem; 
            color: #334155; 
            transition: 0.2s; 
        }
        .division-tab-btn.active { 
            background: #008080; 
            color: white; 
        }
        .worker-subtabs { 
            display: flex; 
            gap: 8px; 
            flex-wrap: wrap; 
            margin-bottom: 16px; 
            padding-left: 8px; 
        }
        .worker-subtab-btn { 
            background: #eef2ff; 
            border: none; 
            padding: 4px 12px; 
            border-radius: 30px; 
            cursor: pointer; 
            font-size: 0.65rem; 
            font-weight: 500; 
            color: #1f3b4c; 
        }
        .worker-subtab-btn.active { 
            background: #008080; 
            color: white; 
        }
        
        .action-icons { 
            display: flex; 
            gap: 10px; 
            justify-content: flex-start;
        }
        .edit-icon, .delete-icon { 
            cursor: pointer; 
            font-size: 1rem; 
            padding: 4px;
            transition: transform 0.1s;
        }
        .edit-icon:active, .delete-icon:active { transform: scale(0.9); }
        .edit-icon { color: #008080; }
        .delete-icon { color: #e74c3c; }
        
        .modal {
            display: none;
            position: fixed;
            top:0; left:0;
            width:100%; height:100%;
            background: rgba(0,0,0,0.5);
            align-items: center;
            justify-content: center;
            z-index: 2000;
            backdrop-filter: blur(3px);
        }
        .modal-content { background: white; max-width: 450px; width: 90%; border-radius: 20px; padding: 24px; max-height: 90vh; overflow-y: auto; }
        .footer-modal { display: flex; justify-content: flex-end; gap: 12px; margin-top: 20px; }
        form input, form select, form textarea { padding: 10px 12px; border-radius: 10px; border: 1px solid #cfdfed; width: 100%; font-size: 0.85rem; }
        .form-grid { display: flex; flex-direction: column; gap: 14px; margin-bottom: 20px; }
        .password-input { width: 100%; padding: 12px; font-size: 0.9rem; border: 1px solid #cfdfed; border-radius: 10px; }
        .operator-list { display: flex; flex-direction: column; gap: 8px; margin-top: 10px; }
        .operator-item { display: flex; justify-content: space-between; align-items: center; padding: 10px 14px; background: #f8f9fa; border-radius: 12px; border: 1px solid #eef2f6; }
        .operator-info { display: flex; flex-direction: column; gap: 3px; }
        .operator-division { font-weight: 600; color: #008080; font-size: 0.8rem; }
        .operator-name { font-size: 0.75rem; color: #555; }
        .operator-actions { display: flex; gap: 12px; }
        .operator-edit, .operator-delete { cursor: pointer; font-size: 1rem; padding: 5px; border-radius: 6px; }
        .operator-edit:hover, .operator-delete:hover { background: #eef2f8; }
        .operator-edit { color: #008080; }
        .operator-delete { color: #e74c3c; }
        .error-text { color: #d32f2f; font-size: 0.7rem; margin-top: 5px; text-align: center; }
        
        @media (max-width: 768px) {
            .container { padding: 12px; }
            .top-bar { padding: 8px 12px; }
            .sidebar { position: fixed; top: 0; left: 0; height: 100%; z-index: 1000; width: 200px; }
            .sidebar.hidden { transform: translateX(-100%); }
            .sidebar-overlay { display: none; position: fixed; top:0; left:0; right:0; bottom:0; background: rgba(0,0,0,0.5); z-index: 999; }
            .sidebar-overlay.active { display: block; }
            .sync-section { flex-direction: column; align-items: stretch; text-align: center; }
            .sync-buttons { justify-content: center; }
            .date-navigator { gap: 8px; }
            .date-nav-btn { padding: 4px 10px; font-size: 0.65rem; }
            .current-date-display { font-size: 0.75rem; padding: 4px 12px; }
            .section-title { flex-direction: column; align-items: stretch; }
            .mode-selection-card { padding: 24px 20px; }
            .mode-selection-card h2 { font-size: 1.3rem; }
            .mode-btn { padding: 12px 16px; font-size: 1rem; }
            th, td { padding: 8px 6px; font-size: 0.7rem; }
        }
    </style>
</head>
<body>
<!-- MODE SELECTION SPLASH SCREEN -->
<div id="modeSelectionOverlay" class="mode-selection-overlay">
    <div class="mode-selection-card">
        <h2>🏭 WarehousePro</h2>
        <div class="subtitle">Sistem Manajemen Gudang</div>
        <div class="mode-buttons">
            <button class="mode-btn mode-btn-admin" id="selectAdminMode">👑 Login sebagai Admin</button>
            <button class="mode-btn mode-btn-operator" id="selectOperatorMode">👤 Login sebagai Operator</button>
        </div>
        <div class="mode-note">📱 Data tersimpan secara lokal di perangkat ini<br>💬 Gunakan fitur Kirim via WhatsApp untuk sinkronisasi data</div>
    </div>
</div>

<div class="app-wrapper" id="appWrapper" style="display: none;">
    <div class="sidebar" id="sidebar">
        <div class="logo-area"><h2><span>WarehousePro</span></h2><p>Manajemen Gudang</p></div>
        <div class="user-role" id="userRoleDisplay">Mode: <strong id="roleName">Loading...</strong></div>
        <div class="nav-menu">
            <div class="nav-item" data-menu="daily-activity"><span>Daily Activity</span></div>
            <div class="nav-item admin-only" data-menu="operators" style="display:none;"><span>👥 Kelola Operator</span></div>
            <div class="nav-item admin-only" data-menu="stock-in" style="display:none;"><span>Barang Masuk</span></div>
            <div class="nav-item admin-only" data-menu="stock-out" style="display:none;"><span>Barang Keluar</span></div>
            <div class="nav-item admin-only" data-menu="history" style="display:none;"><span>Riwayat</span></div>
            <div class="nav-item nav-item-logout" id="logoutBtn"><span>🚪 Logout</span></div>
        </div>
        <div style="padding: 16px 12px; font-size: 0.6rem; opacity:0.5; text-align:center;">v3.0 • Daily Activity</div>
    </div>
    <div id="sidebarOverlay" class="sidebar-overlay"></div>
    <div class="main-content">
        <div class="top-bar">
            <button class="menu-toggle-btn" id="toggleSidebarBtn">☰</button>
            <div class="title-area"><h1 id="pageTitle">Daily Activity</h1><div class="sub" id="pageDesc">Aktivitas harian per bagian</div></div>
        </div>
        <div class="container" id="appContainer"></div>
    </div>
</div>

<!-- MODALS -->
<div id="passwordModal" class="modal"><div class="modal-content"><h3>🔐 Akses Admin</h3><p style="font-size:0.75rem; margin-bottom:15px;">Masukkan password untuk mengakses menu Admin</p><input type="password" id="adminPassword" class="password-input" placeholder="Masukkan password"><div id="passwordError" class="error-text"></div><div class="footer-modal"><button class="btn-text" id="cancelPasswordBtn">Batal</button><button class="btn-filter" id="confirmPasswordBtn">Masuk</button></div></div></div>

<div id="operatorModal" class="modal"><div class="modal-content"><h3>Login Operator</h3><div class="form-grid"><select id="operatorDivision"><option value="">-- Pilih Bagian --</option><option value="Raw Material">Raw Material</option><option value="Finish Goods">Finish Goods</option><option value="Save Material">Save Material</option></select><select id="operatorName"><option value="">-- Pilih Nama --</option></select></div><div class="footer-modal"><button class="btn-text" id="closeOperatorModal">Batal</button><button class="btn-filter" id="confirmOperatorBtn">Masuk sebagai Operator</button></div></div></div>

<div id="operatorFormModal" class="modal"><div class="modal-content"><h3 id="operatorFormTitle">Tambah Operator</h3><div class="form-grid"><select id="opDivision" required><option value="">-- Pilih Bagian --</option><option value="Leader">Leader</option><option value="Raw Material">Raw Material</option><option value="Finish Goods">Finish Goods</option><option value="Save Material">Save Material</option></select><input type="text" id="opName" placeholder="Nama Operator" required></div><div class="footer-modal"><button class="btn-text" id="closeOperatorFormBtn">Batal</button><button class="btn-filter" id="saveOperatorBtn">Simpan</button></div></div></div>

<div id="activityModal" class="modal"><div class="modal-content"><h3 id="modalTitle">Tambah Aktivitas</h3><form id="activityForm"><input type="hidden" id="activityId"><div class="form-grid"><input type="text" id="actDate" placeholder="DD/MM/YYYY" required><select id="actDivision" required><option value="">-- Pilih Bagian --</option></select><select id="actWorkerName" required><option value="">-- Pilih Pekerja --</option></select><input type="text" id="actActivity" placeholder="Aktivitas" required><input type="time" id="actStart" required><input type="time" id="actEnd" required><textarea id="actDesc" rows="2" placeholder="Keterangan (opsional)"></textarea></div><div class="footer-modal"><button type="button" class="btn-text" id="closeModalBtn">Batal</button><button type="submit" class="btn-filter">Simpan</button></div></form></div></div>

<div id="stockInModal" class="modal"><div class="modal-content"><h3 id="stockInModalTitle">Tambah Barang Masuk</h3><form id="stockInForm"><input type="hidden" id="stockInId"><div class="form-grid"><input type="text" id="inTanggal" placeholder="DD/MM/YYYY" required><input type="time" id="inJam" required><select id="inProductId" required><option value="">-- Pilih Barang --</option></select><input type="text" id="inNoPO" placeholder="No. PO" required><input type="text" id="inNoSJ" placeholder="No. Surat Jalan" required><input type="text" id="inSupplier" placeholder="Supplier" required><input type="number" id="inQty" placeholder="Jumlah" min="1" required><input type="text" id="inSatuan" placeholder="Satuan" required><input type="text" id="inKeterangan" placeholder="Keterangan"><input type="text" id="inPIC" placeholder="PIC Penerima" required></div><div class="footer-modal"><button type="button" class="btn-text" id="closeStockInModalBtn">Batal</button><button type="submit" class="btn-filter">Simpan</button></div></form></div></div>

<div id="stockOutModal" class="modal"><div class="modal-content"><h3 id="stockOutModalTitle">Tambah Barang Keluar</h3><form id="stockOutForm"><input type="hidden" id="stockOutId"><div class="form-grid"><input type="text" id="outTanggal" placeholder="DD/MM/YYYY" required><input type="time" id="outJam" required><select id="outProductId" required><option value="">-- Pilih Barang --</option></select><input type="number" id="outQty" placeholder="Jumlah" min="1" required><input type="text" id="outSatuan" placeholder="Satuan" required><input type="text" id="outEkspedisi" placeholder="Ekspedisi / Kurir" required><input type="text" id="outUser" placeholder="User / Peminta" required><input type="text" id="outPIC" placeholder="PIC Pengeluar" required><input type="text" id="outKeterangan" placeholder="Keterangan"></div><div class="footer-modal"><button type="button" class="btn-text" id="closeStockOutModalBtn">Batal</button><button type="submit" class="btn-filter">Simpan</button></div></form></div></div>

<div id="importModal" class="modal"><div class="modal-content"><h3>Import Data dari File Backup</h3><p style="font-size:0.75rem; margin-bottom:15px;">Pilih file JSON hasil export dari perangkat lain</p><input type="file" id="importFile" accept=".json"><div class="footer-modal"><button class="btn-text" id="closeImportModal">Batal</button><button class="btn-filter" id="confirmImportBtn">Import</button></div></div></div>

<div id="pullDataModal" class="modal"><div class="modal-content"><h3>📥 Tarik Data dari Operator</h3><p style="font-size:0.75rem; margin-bottom:15px;">Pilih file JSON yang dikirim operator via WhatsApp</p><input type="file" id="pullDataFile" accept=".json"><p style="font-size:0.7rem; margin-top:10px; color:#666;">Data Daily Activity dari operator akan digabung dengan data yang sudah ada (data duplikat akan dilewati).</p><div class="footer-modal"><button class="btn-text" id="closePullModal">Batal</button><button class="btn-filter" id="confirmPullBtn">Tarik Data</button></div></div></div>

<script>
    // ========== KONFIGURASI ==========
    const ADMIN_PASSWORD = "admin123";
    let userMode = null;
    let isAdminAuthenticated = false;
    let operatorDivision = '';
    let operatorWorkerName = '';
    let dynamicOperators = [];
    const operatorDivisions = ["Raw Material", "Finish Goods", "Save Material"];
    const allDivisions = ["Leader", "Raw Material", "Finish Goods", "Save Material"];
    let dailyActivitiesMap = {};
    let currentViewDate = null;
    let products = [];
    let stockInEntries = [];
    let stockOutEntries = [];
    let currentDivision = "Leader";
    let currentWorker = "";
    let currentMenu = "daily-activity";
    
    // ========== FUNGSI OPERATOR ==========
    function getOperators() {
        const stored = localStorage.getItem('dynamic_operators');
        if(stored) dynamicOperators = JSON.parse(stored);
        else {
            dynamicOperators = [
                { id: 1, division: "Leader", name: "Oky Dany P." },
                { id: 2, division: "Raw Material", name: "Darma" },
                { id: 3, division: "Raw Material", name: "Nurhadi" },
                { id: 4, division: "Finish Goods", name: "Athar" },
                { id: 5, division: "Save Material", name: "Purnomo" }
            ];
            saveOperators();
        }
        return dynamicOperators;
    }
    function saveOperators() { localStorage.setItem('dynamic_operators', JSON.stringify(dynamicOperators)); }
    function getOperatorsByDivision(division) { return dynamicOperators.filter(op => op.division === division); }
    function addOperator(division, name) {
        const newId = dynamicOperators.length > 0 ? Math.max(...dynamicOperators.map(o => o.id)) + 1 : 1;
        dynamicOperators.push({ id: newId, division: division, name: name });
        saveOperators();
        if(userMode === 'admin' && currentMenu === 'operators') renderOperatorsList();
    }
    function updateOperator(id, division, name) {
        const idx = dynamicOperators.findIndex(o => o.id === id);
        if(idx !== -1) { dynamicOperators[idx] = { id, division, name }; saveOperators(); if(userMode === 'admin' && currentMenu === 'operators') renderOperatorsList(); }
    }
    function deleteOperator(id) { dynamicOperators = dynamicOperators.filter(o => o.id !== id); saveOperators(); if(userMode === 'admin' && currentMenu === 'operators') renderOperatorsList(); }
    
    // ========== DATA HARIAN ==========
    function loadDailyActivities() {
        const stored = localStorage.getItem('daily_activities_map');
        if(stored) dailyActivitiesMap = JSON.parse(stored);
        else {
            const today = new Date().toISOString().slice(0,10);
            dailyActivitiesMap = {};
            dailyActivitiesMap[today] = [
                { id: 1, division: "Leader", workerName: "Oky Dany P.", activity: "Koordinasi tim", startTime: "08:00", endTime: "10:00", duration: "2 jam 0 menit", description: "Meeting pagi" },
                { id: 2, division: "Raw Material", workerName: "Darma", activity: "Pengecekan bahan baku", startTime: "09:00", endTime: "11:30", duration: "2 jam 30 menit", description: "Inspeksi" },
                { id: 3, division: "Finish Goods", workerName: "Athar", activity: "Packing barang jadi", startTime: "10:00", endTime: "12:00", duration: "2 jam 0 menit", description: "Packing pesanan" }
            ];
            saveDailyActivities();
        }
    }
    function saveDailyActivities() { localStorage.setItem('daily_activities_map', JSON.stringify(dailyActivitiesMap)); }
    function getActivitiesByDate(dateStr) { if(!dailyActivitiesMap[dateStr]) dailyActivitiesMap[dateStr] = []; return dailyActivitiesMap[dateStr]; }
    function addActivityToDate(dateStr, activity) {
        if(!dailyActivitiesMap[dateStr]) dailyActivitiesMap[dateStr] = [];
        const maxId = dailyActivitiesMap[dateStr].length > 0 ? Math.max(...dailyActivitiesMap[dateStr].map(a => a.id)) : 0;
        activity.id = maxId + 1;
        dailyActivitiesMap[dateStr].push(activity);
        saveDailyActivities();
    }
    function updateActivityOnDate(dateStr, id, updatedActivity) {
        if(dailyActivitiesMap[dateStr]) {
            const idx = dailyActivitiesMap[dateStr].findIndex(a => a.id === id);
            if(idx !== -1) { updatedActivity.id = id; dailyActivitiesMap[dateStr][idx] = updatedActivity; saveDailyActivities(); return true; }
        }
        return false;
    }
    function deleteActivityFromDate(dateStr, id) {
        if(dailyActivitiesMap[dateStr]) { dailyActivitiesMap[dateStr] = dailyActivitiesMap[dateStr].filter(a => a.id !== id); saveDailyActivities(); return true; }
        return false;
    }
    
    // ========== FUNGSI BANTU ==========
    function toStorageDate(dmy) { if (!dmy) return ""; let parts = dmy.split('/'); if (parts.length === 3) return `${parts[2]}-${parts[1]}-${parts[0]}`; return dmy; }
    function toDisplayDate(ymd) { if (!ymd) return ""; let parts = ymd.split('-'); if (parts.length === 3) return `${parts[2]}/${parts[1]}/${parts[0]}`; return ymd; }
    function isValidDateDDMMYYYY(dateStr) { let parts = dateStr.split('/'); if (parts.length !== 3) return false; let day = parseInt(parts[0]), month = parseInt(parts[1]), year = parseInt(parts[2]); if (isNaN(day)||isNaN(month)||isNaN(year)) return false; if (month<1||month>12) return false; let daysInMonth = new Date(year, month, 0).getDate(); return day>=1 && day<=daysInMonth; }
    function formatDateToInput(date) { let dd = String(date.getDate()).padStart(2,'0'); let mm = String(date.getMonth()+1).padStart(2,'0'); let yyyy = date.getFullYear(); return `${dd}/${mm}/${yyyy}`; }
    function formatDateToStorage(date) { return date.toISOString().slice(0,10); }
    function calculateDuration(start,end) { if (!start||!end) return "-"; const startDate = new Date(`2000-01-01T${start}`); const endDate = new Date(`2000-01-01T${end}`); let diff = (endDate-startDate)/(1000*60); if (diff<0) diff+=24*60; return `${Math.floor(diff/60)} jam ${diff%60} menit`; }
    
    // ========== LOAD DATA ==========
    function loadData() {
        const storedProducts = localStorage.getItem('warehouse_products');
        const storedStockIn = localStorage.getItem('stock_in_entries');
        const storedStockOut = localStorage.getItem('stock_out_entries');
        getOperators();
        loadDailyActivities();
        if(storedProducts) products = JSON.parse(storedProducts);
        else products = [{ id:1,name:"Kipas Angin",sku:"FAN-001",price:185000,stock:50},{ id:2,name:"Monitor LED 24",sku:"MON-24D",price:1250000,stock:15},{ id:3,name:"Mouse Wireless",sku:"MSE-101",price:85000,stock:45},{ id:4,name:"Keyboard Mechanical",sku:"KEY-202",price:375000,stock:20}];
        if(storedStockIn) stockInEntries = JSON.parse(storedStockIn); else stockInEntries = [];
        if(storedStockOut) stockOutEntries = JSON.parse(storedStockOut); else stockOutEntries = [];
        updateStockFromTransactions();
        saveToLocal();
    }
    function updateStockFromTransactions() { products.forEach(p=>p.stock=0); stockInEntries.forEach(inEntry=>{ const prod=products.find(p=>p.id===inEntry.productId); if(prod) prod.stock+=inEntry.qty; }); stockOutEntries.forEach(outEntry=>{ const prod=products.find(p=>p.id===outEntry.productId); if(prod) prod.stock-=outEntry.qty; }); }
    function saveToLocal() { localStorage.setItem('warehouse_products',JSON.stringify(products)); localStorage.setItem('stock_in_entries',JSON.stringify(stockInEntries)); localStorage.setItem('stock_out_entries',JSON.stringify(stockOutEntries)); }
    
    // ========== CRUD ==========
    function getNextStockInId(){ return stockInEntries.length>0?Math.max(...stockInEntries.map(s=>s.id))+1:1; }
    function addStockIn(data){ const newEntry={id:getNextStockInId(),...data}; stockInEntries.push(newEntry); updateStockFromTransactions(); saveToLocal(); renderCurrentMenu(); return true; }
    function updateStockIn(id,data){ const idx=stockInEntries.findIndex(s=>s.id===id); if(idx!==-1){ stockInEntries[idx]={...stockInEntries[idx],...data}; updateStockFromTransactions(); saveToLocal(); renderCurrentMenu(); return true; } return false; }
    function deleteStockIn(id){ stockInEntries=stockInEntries.filter(s=>s.id!==id); updateStockFromTransactions(); saveToLocal(); renderCurrentMenu(); }
    function getNextStockOutId(){ return stockOutEntries.length>0?Math.max(...stockOutEntries.map(s=>s.id))+1:1; }
    function addStockOut(data){ const prod=products.find(p=>p.id===data.productId); if(!prod||prod.stock<data.qty){ alert(`Stok tidak mencukupi! Stok ${prod?.name||'produk'} tersisa: ${prod?.stock||0}`); return false; } const newEntry={id:getNextStockOutId(),...data}; stockOutEntries.push(newEntry); updateStockFromTransactions(); saveToLocal(); renderCurrentMenu(); return true; }
    function updateStockOut(id,data){ const idx=stockOutEntries.findIndex(s=>s.id===id); if(idx!==-1){ stockOutEntries[idx]={...stockOutEntries[idx],...data}; updateStockFromTransactions(); saveToLocal(); renderCurrentMenu(); return true; } return false; }
    function deleteStockOut(id){ stockOutEntries=stockOutEntries.filter(s=>s.id!==id); updateStockFromTransactions(); saveToLocal(); renderCurrentMenu(); }
    
    // ========== EXPORT & IMPORT ==========
    function exportAllData() { const allData = { products, stockInEntries, stockOutEntries, dailyActivitiesMap, dynamicOperators, exportDate: new Date().toISOString(), version:"3.0" }; const dataStr=JSON.stringify(allData,null,2); const blob=new Blob([dataStr],{type:'application/json'}); const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download=`warehouse_backup_${new Date().toISOString().slice(0,19).replace(/:/g,'-')}.json`; a.click(); URL.revokeObjectURL(url); alert("Data berhasil diexport!"); return blob; }
    function exportDailyActivityOnly() { const dailyData={ dailyActivitiesMap, operatorDivision, operatorWorkerName, exportDate:new Date().toISOString(), type:"DAILY_ACTIVITY_ONLY" }; const dataStr=JSON.stringify(dailyData,null,2); const blob=new Blob([dataStr],{type:'application/json'}); const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download=`daily_activity_${operatorDivision}_${operatorWorkerName}_${new Date().toISOString().slice(0,10)}.json`; a.click(); URL.revokeObjectURL(url); alert("Data Daily Activity berhasil diexport!"); return blob; }
    function sendWhatsAppMessage(message) { window.open(`https://wa.me/?text=${encodeURIComponent(message)}`, '_blank'); }
    function shareViaWhatsApp(isOperator) { let message = isOperator && userMode==='operator' ? `*DATA DAILY ACTIVITY - ${operatorDivision} (${operatorWorkerName})*\n\nHalo Admin, berikut data Daily Activity saya.\n📅 ${new Date().toLocaleDateString()}\n\nCara import:\n1. Buka WarehousePro sebagai Admin\n2. Klik "📥 Tarik Data Operator"\n3. Pilih file backup\n\n- ${operatorWorkerName} -` : `*BACKUP DATA WAREHOUSEPRO*\n\n📅 ${new Date().toLocaleString()}\n\nCara restore:\n1. Buka WarehousePro\n2. Klik "📥 Import"\n3. Pilih file backup ini`; alert("Pesan WhatsApp akan dibuka. Kirimkan juga file backup yang sudah diunduh."); sendWhatsAppMessage(message); }
    function importAndMergeDataFromFile(file) { const reader=new FileReader(); reader.onload=function(e){ try{ const importedData=JSON.parse(e.target.result); let mergeCount=0; if(importedData.type==="DAILY_ACTIVITY_ONLY" && importedData.dailyActivitiesMap){ Object.keys(importedData.dailyActivitiesMap).forEach(date=>{ if(!dailyActivitiesMap[date]) dailyActivitiesMap[date]=[]; importedData.dailyActivitiesMap[date].forEach(newAct=>{ const exists=dailyActivitiesMap[date].some(ex=>ex.division===newAct.division && ex.workerName===newAct.workerName && ex.activity===newAct.activity && ex.startTime===newAct.startTime); if(!exists){ const maxId=dailyActivitiesMap[date].length>0?Math.max(...dailyActivitiesMap[date].map(a=>a.id)):0; newAct.id=maxId+dailyActivitiesMap[date].length+1; dailyActivitiesMap[date].push(newAct); mergeCount++; } }); }); saveDailyActivities(); alert(`Import berhasil!\n✅ Data baru ditambahkan: ${mergeCount} aktivitas`); renderCurrentMenu(); } else if(importedData.products && importedData.dailyActivitiesMap){ if(importedData.dailyActivitiesMap){ Object.keys(importedData.dailyActivitiesMap).forEach(date=>{ if(!dailyActivitiesMap[date]) dailyActivitiesMap[date]=[]; importedData.dailyActivitiesMap[date].forEach(newAct=>{ const exists=dailyActivitiesMap[date].some(ex=>ex.division===newAct.division && ex.workerName===newAct.workerName && ex.activity===newAct.activity); if(!exists){ const maxId=dailyActivitiesMap[date].length>0?Math.max(...dailyActivitiesMap[date].map(a=>a.id)):0; newAct.id=maxId+dailyActivitiesMap[date].length+1; dailyActivitiesMap[date].push(newAct); mergeCount++; } }); }); } if(importedData.products) products=importedData.products; if(importedData.stockInEntries) stockInEntries=importedData.stockInEntries; if(importedData.stockOutEntries) stockOutEntries=importedData.stockOutEntries; if(importedData.dynamicOperators) { dynamicOperators=importedData.dynamicOperators; saveOperators(); } updateStockFromTransactions(); saveToLocal(); saveDailyActivities(); alert(`Import full backup berhasil!\n✅ Daily Activity baru: ${mergeCount}`); renderCurrentMenu(); } else alert("Format file tidak dikenali."); } catch(err){ alert("File tidak valid!"); } }; reader.readAsText(file); }
    function importDataFromFile(file) { importAndMergeDataFromFile(file); }
    
    // ========== AUTH & LOGOUT ==========
    function showPasswordModal(){ const modal=document.getElementById("passwordModal"); document.getElementById("adminPassword").value=""; document.getElementById("passwordError").textContent=""; modal.style.display="flex"; document.getElementById("adminPassword").focus(); }
    function verifyPasswordAndSwitch(){ const password=document.getElementById("adminPassword").value; if(password===ADMIN_PASSWORD){ isAdminAuthenticated=true; userMode='admin'; localStorage.setItem('userMode','admin'); localStorage.setItem('isAdminAuthenticated','true'); document.getElementById("userRoleDisplay").innerHTML=`Mode: <strong>Admin (Pusat)</strong>`; document.querySelectorAll(".admin-only").forEach(el=>el.style.display="flex"); document.getElementById("pullFromOperatorBtn").style.display="inline-block"; document.getElementById("passwordModal").style.display="none"; currentViewDate = formatDateToStorage(new Date()); renderCurrentMenu(); } else{ document.getElementById("passwordError").textContent="Password salah! Akses ditolak."; document.getElementById("adminPassword").value=""; } }
    function logout() {
        userMode = null; isAdminAuthenticated = false; operatorDivision = ''; operatorWorkerName = ''; currentViewDate = null;
        localStorage.removeItem('userMode'); localStorage.removeItem('isAdminAuthenticated'); localStorage.removeItem('operatorDivision'); localStorage.removeItem('operatorWorkerName');
        document.getElementById('appWrapper').style.display = 'none';
        document.getElementById('modeSelectionOverlay').style.display = 'flex';
        document.getElementById("adminPassword").value = ""; document.getElementById("passwordError").textContent = "";
    }
    function checkSavedSession() {
        const savedMode = localStorage.getItem('userMode');
        const savedAdminAuth = localStorage.getItem('isAdminAuthenticated');
        const savedOperatorDiv = localStorage.getItem('operatorDivision');
        const savedOperatorName = localStorage.getItem('operatorWorkerName');
        if(savedMode === 'admin' && savedAdminAuth === 'true') {
            userMode = 'admin'; isAdminAuthenticated = true;
            document.getElementById("userRoleDisplay").innerHTML = `Mode: <strong>Admin (Pusat)</strong>`;
            document.querySelectorAll(".admin-only").forEach(el=>el.style.display="flex");
            document.getElementById("pullFromOperatorBtn").style.display="inline-block";
            document.getElementById('modeSelectionOverlay').style.display = 'none';
            document.getElementById('appWrapper').style.display = 'flex';
            currentViewDate = formatDateToStorage(new Date());
            renderCurrentMenu();
            return true;
        } else if(savedMode === 'operator' && savedOperatorDiv && savedOperatorName) {
            userMode = 'operator'; operatorDivision = savedOperatorDiv; operatorWorkerName = savedOperatorName;
            document.getElementById("userRoleDisplay").innerHTML = `Mode: <strong>Operator - ${operatorDivision}</strong>`;
            document.getElementById('modeSelectionOverlay').style.display = 'none';
            document.getElementById('appWrapper').style.display = 'flex';
            currentViewDate = formatDateToStorage(new Date());
            renderCurrentMenu();
            return true;
        }
        return false;
    }
    
    // ========== RENDER ==========
    function renderCurrentMenu(){ 
        if(userMode==='operator') {
            renderOperatorDailyActivity();
        } else if(userMode==='admin'){ 
            if(currentMenu==="daily-activity") {
                renderAdminDailyActivity();
            } else if(currentMenu==="operators") {
                renderOperatorsList();
            } else if(currentMenu==="stock-in") {
                renderStockIn();
            } else if(currentMenu==="stock-out") {
                renderStockOut();
            } else if(currentMenu==="history") {
                renderHistory();
            }
        }
    }
    
    function renderSyncSection() {
        return `<div class="sync-section">
                    <div class="sync-title"><span>📡</span> Sinkronisasi Database</div>
                    <div class="sync-buttons">
                        <button class="sync-btn" id="exportDataBtn">📤 Export</button>
                        <button class="sync-btn sync-btn-wa" id="shareWAbtn">💬 Share Data</button>
                        <button class="sync-btn" id="importDataBtn">📥 Import</button>
                        <button class="sync-btn sync-btn-warning" id="pullFromOperatorBtn" style="display:${userMode === 'admin' ? 'inline-block' : 'none'};">📥 Tarik Data Operator</button>
                    </div>
                </div>`;
    }
    
    function renderDateNavigator() {
        if(currentViewDate && (userMode === 'admin' || userMode === 'operator')) {
            return `<div class="date-navigator">
                        <button class="date-nav-btn" id="prevDateBtn">◀ Sebelumnya</button>
                        <span class="current-date-display">${toDisplayDate(currentViewDate)}<span class="daily-tag">Harian</span></span>
                        <button class="date-nav-btn" id="nextDateBtn">Berikutnya ▶</button>
                        <button class="date-nav-btn" id="todayDateBtn">📅 Hari Ini</button>
                    </div>`;
        }
        return '';
    }
    
    function renderOperatorsList(){ 
        const ops=getOperators(); 
        const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>👥 Daftar Operator</span>
                                <div class="btn-group-right">
                                    <button class="icon-btn icon-add" id="addOperatorBtn" title="Tambah Operator">+</button>
                                </div>
                            </div>
                            <div class="operator-list">
                                ${ops.map(op=>`<div class="operator-item"><div class="operator-info"><span class="operator-division">${op.division}</span><span class="operator-name">${op.name}</span></div><div class="operator-actions"><span class="operator-edit" data-id="${op.id}" data-division="${op.division}" data-name="${op.name}">✏️</span><span class="operator-delete" data-id="${op.id}">🗑️</span></div></div>`).join('')}
                            </div>
                        </div>
                    </div>
                    ${renderSyncSection()}`; 
        document.getElementById("appContainer").innerHTML=html; 
        document.getElementById("pageTitle").innerText="Kelola Operator"; 
        document.getElementById("pageDesc").innerText="Tambah, edit, atau hapus data operator"; 
        document.getElementById("addOperatorBtn")?.addEventListener("click",()=>openOperatorFormModal()); 
        document.querySelectorAll(".operator-edit").forEach(el=>el.addEventListener("click",()=>{ const id=parseInt(el.dataset.id),division=el.dataset.division,name=el.dataset.name; openOperatorFormModal(id,division,name); })); 
        document.querySelectorAll(".operator-delete").forEach(el=>el.addEventListener("click",()=>{ if(confirm("Hapus operator ini?")) deleteOperator(parseInt(el.dataset.id)); })); 
        attachSyncEvents();
    }
    
    function openOperatorFormModal(id=null,division="",name=""){ const modal=document.getElementById("operatorFormModal"); document.getElementById("operatorFormTitle").innerText=id?"Edit Operator":"Tambah Operator"; document.getElementById("opDivision").value=division; document.getElementById("opName").value=name; modal.dataset.editId=id||""; modal.style.display="flex"; }
    function closeOperatorFormModal(){ document.getElementById("operatorFormModal").style.display="none"; document.getElementById("opDivision").value=""; document.getElementById("opName").value=""; }
    
    function renderAdminDailyActivity(){ 
        const activities = getActivitiesByDate(currentViewDate);
        let filtered = activities.filter(a => a.division === currentDivision);
        if(currentWorker) filtered = filtered.filter(a => a.workerName === currentWorker);
        filtered.sort((a,b) => a.startTime.localeCompare(b.startTime));
        const divisionTabs = allDivisions.map(div=>`<button class="division-tab-btn ${currentDivision===div?'active':''}" data-division="${div}">${div}</button>`).join('');
        const workersList = getOperatorsByDivision(currentDivision);
        const workerSubtabs = `<div class="worker-subtabs"><button class="worker-subtab-btn ${currentWorker===''?'active':''}" data-worker="">Semua</button>${workersList.map(w=>`<button class="worker-subtab-btn ${currentWorker===w.name?'active':''}" data-worker="${w.name}">${w.name}</button>`).join('')}</div>`;
        const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>📋 Daily Activity (Admin) - ${toDisplayDate(currentViewDate)}</span>
                                <div class="btn-group-right">
                                    <button class="icon-btn icon-excel" id="exportExcelBtn">📎</button>
                                    <button class="icon-btn icon-print" id="printBtn">🖨️</button>
                                    <button class="btn-text" id="addActivityBtn">+ Tambah</button>
                                </div>
                            </div>
                            <div class="division-tabs">${divisionTabs}</div>
                            ${workerSubtabs}
                            <div class="table-wrapper">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Pekerja</th>
                                            <th>Aktivitas</th>
                                            <th>Jam Mulai</th>
                                            <th>Jam Selesai</th>
                                            <th>Durasi</th>
                                            <th>Keterangan</th>
                                            <th>Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        ${filtered.map(act => `
                                            <tr>
                                                <td><strong>${act.workerName}</strong></td>
                                                <td>${act.activity}</td>
                                                <td>${act.startTime}</td>
                                                <td>${act.endTime}</td>
                                                <td><span class="badge">${act.duration}</span></td>
                                                <td>${act.description||'-'}</td>
                                                <td class="action-icons">
                                                    <span class="edit-icon" data-id="${act.id}">✏️</span>
                                                    <span class="delete-icon" data-id="${act.id}">🗑️</span>
                                                </td>
                                            </tr>
                                        `).join('')}
                                        ${filtered.length === 0 ? '<tr><td colspan="7" style="text-align:center; padding:30px;">Belum ada aktivitas untuk bagian ini</td></tr>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        ${renderDateNavigator()}
                    </div>
                    ${renderSyncSection()}`; 
        document.getElementById("appContainer").innerHTML=html; 
        document.getElementById("pageTitle").innerText="Daily Activity (Admin)"; 
        document.getElementById("pageDesc").innerText="Manajer dapat melihat semua data operator - Data Harian";
        attachDailyActivityEvents(); 
        attachSyncEvents();
    }
    
    function attachDailyActivityEvents(){ 
        document.querySelectorAll(".division-tab-btn").forEach(btn=>btn.addEventListener("click",()=>{ currentDivision=btn.dataset.division; currentWorker=""; renderAdminDailyActivity(); })); 
        document.querySelectorAll(".worker-subtab-btn").forEach(btn=>btn.addEventListener("click",()=>{ currentWorker=btn.dataset.worker; renderAdminDailyActivity(); })); 
        document.getElementById("addActivityBtn")?.addEventListener("click",()=>openActivityModal()); 
        document.getElementById("exportExcelBtn")?.addEventListener("click",exportToExcelFiltered); 
        document.getElementById("printBtn")?.addEventListener("click",()=>window.print()); 
        document.getElementById("prevDateBtn")?.addEventListener("click",()=>changeDate(-1));
        document.getElementById("nextDateBtn")?.addEventListener("click",()=>changeDate(1));
        document.getElementById("todayDateBtn")?.addEventListener("click",goToToday);
        document.querySelectorAll(".edit-icon").forEach(icon=>{ const id=parseInt(icon.dataset.id); const act=getActivitiesByDate(currentViewDate).find(a=>a.id===id); if(act) icon.addEventListener("click",()=>openActivityModal(id,act.division,act.workerName,toDisplayDate(currentViewDate),act.activity,act.startTime,act.endTime,act.description)); }); 
        document.querySelectorAll(".delete-icon").forEach(icon=>icon.addEventListener("click",()=>{ if(confirm("Hapus aktivitas ini?")) deleteActivityFromDate(currentViewDate,parseInt(icon.dataset.id)); renderAdminDailyActivity(); })); 
    }
    
    function renderOperatorDailyActivity(){ 
        const activities = getActivitiesByDate(currentViewDate);
        let filtered = activities.filter(a => a.division === operatorDivision && a.workerName === operatorWorkerName);
        filtered.sort((a,b) => a.startTime.localeCompare(b.startTime));
        const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>📋 Daily Activity - ${operatorDivision} (${operatorWorkerName}) - ${toDisplayDate(currentViewDate)}</span>
                                <div class="btn-group-right">
                                    <button class="btn-operator-add" id="addActivityBtnOp">+ Tambah Aktivitas</button>
                                    <button class="btn-operator-wa" id="shareToAdminBtn">📤 Kirim ke Admin</button>
                                </div>
                            </div>
                            <div class="table-wrapper">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Aktivitas</th>
                                            <th>Jam Mulai</th>
                                            <th>Jam Selesai</th>
                                            <th>Durasi</th>
                                            <th>Keterangan</th>
                                            <th>Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        ${filtered.map(act => `
                                            <tr>
                                                <td>${act.activity}</td>
                                                <td>${act.startTime}</td>
                                                <td>${act.endTime}</td>
                                                <td><span class="badge">${act.duration}</span></td>
                                                <td>${act.description||'-'}</td>
                                                <td class="action-icons">
                                                    <span class="edit-icon" data-id="${act.id}">✏️</span>
                                                    <span class="delete-icon" data-id="${act.id}">🗑️</span>
                                                </td>
                                            </tr>
                                        `).join('')}
                                        ${filtered.length === 0 ? '<tr><td colspan="6" style="text-align:center; padding:30px;">Belum ada aktivitas Anda</td></tr>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        ${renderDateNavigator()}
                    </div>
                    ${renderSyncSection()}`; 
        document.getElementById("appContainer").innerHTML=html; 
        document.getElementById("pageTitle").innerText=`Daily Activity - ${operatorDivision}`; 
        document.getElementById("pageDesc").innerText=`Operator: ${operatorWorkerName} | Data Harian`; 
        document.getElementById("addActivityBtnOp")?.addEventListener("click",()=>openActivityModalOperator()); 
        document.getElementById("shareToAdminBtn")?.addEventListener("click",()=>{ exportDailyActivityOnly(); setTimeout(()=>shareViaWhatsApp(true),500); }); 
        document.getElementById("prevDateBtn")?.addEventListener("click",()=>changeDate(-1));
        document.getElementById("nextDateBtn")?.addEventListener("click",()=>changeDate(1));
        document.getElementById("todayDateBtn")?.addEventListener("click",goToToday);
        document.querySelectorAll(".edit-icon").forEach(icon=>{ const id=parseInt(icon.dataset.id); const act=getActivitiesByDate(currentViewDate).find(a=>a.id===id); if(act) icon.addEventListener("click",()=>openActivityModalOperator(id,act.division,act.workerName,toDisplayDate(currentViewDate),act.activity,act.startTime,act.endTime,act.description)); }); 
        document.querySelectorAll(".delete-icon").forEach(icon=>icon.addEventListener("click",()=>{ if(confirm("Hapus aktivitas ini?")) deleteActivityFromDate(currentViewDate,parseInt(icon.dataset.id)); renderOperatorDailyActivity(); })); 
        attachSyncEvents();
    }
    
    function renderStockIn(){ populateProductSelect('inProductId'); const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>📥 Barang Masuk</span>
                                <div class="btn-group-right">
                                    <button class="icon-btn icon-excel" id="exportStockInExcelBtn">📎</button>
                                    <button class="icon-btn icon-print" id="printStockInBtn">🖨️</button>
                                    <button class="btn-text" id="addStockInBtn">+ Barang Masuk</button>
                                </div>
                            </div>
                            <div class="table-wrapper">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Tanggal</th><th>Jam</th><th>Barang</th><th>No PO</th><th>No SJ</th><th>Supplier</th><th>Qty</th><th>Satuan</th><th>Keterangan</th><th>PIC</th><th>Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        ${stockInEntries.map(s=>{ const prod=products.find(p=>p.id===s.productId); return `
                                            <tr>
                                                <td>${toDisplayDate(s.tanggal)}</td>
                                                <td>${s.jam}</td>
                                                <td><strong>${prod?.name||'-'}</strong></td>
                                                <td>${s.noPO}</td>
                                                <td>${s.noSJ}</td>
                                                <td>${s.supplier}</td>
                                                <td>${s.qty}</td>
                                                <td>${s.satuan}</td>
                                                <td>${s.keterangan||'-'}</td>
                                                <td>${s.pic}</td>
                                                <td class="action-icons">
                                                    <span class="edit-icon" data-id="${s.id}" data-type="in">✏️</span>
                                                    <span class="delete-icon" data-id="${s.id}" data-type="in">🗑️</span>
                                                </td>
                                            </tr>
                                        `}).join('')}
                                        ${stockInEntries.length === 0 ? '<tr><td colspan="11" style="text-align:center; padding:30px;">Belum ada data barang masuk</td></tr>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                    ${renderSyncSection()}`; 
        document.getElementById("appContainer").innerHTML=html; 
        document.getElementById("pageTitle").innerText="Barang Masuk"; 
        document.getElementById("addStockInBtn")?.addEventListener("click",()=>openStockInModal()); 
        document.getElementById("exportStockInExcelBtn")?.addEventListener("click",exportStockInToExcel); 
        document.getElementById("printStockInBtn")?.addEventListener("click",()=>window.print()); 
        document.querySelectorAll(".edit-icon[data-type='in']").forEach(icon=>icon.addEventListener("click",()=>openStockInModal(parseInt(icon.dataset.id)))); 
        document.querySelectorAll(".delete-icon[data-type='in']").forEach(icon=>icon.addEventListener("click",()=>{ if(confirm("Hapus?")) deleteStockIn(parseInt(icon.dataset.id)); })); 
        attachSyncEvents(); 
    }
    
    function renderStockOut(){ populateProductSelect('outProductId'); const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>📤 Barang Keluar</span>
                                <div class="btn-group-right">
                                    <button class="icon-btn icon-excel" id="exportStockOutExcelBtn">📎</button>
                                    <button class="icon-btn icon-print" id="printStockOutBtn">🖨️</button>
                                    <button class="btn-text" id="addStockOutBtn">+ Barang Keluar</button>
                                </div>
                            </div>
                            <div class="table-wrapper">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Tanggal</th><th>Jam</th><th>Barang</th><th>Qty</th><th>Satuan</th><th>Ekspedisi</th><th>User</th><th>PIC</th><th>Keterangan</th><th>Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        ${stockOutEntries.map(s=>{ const prod=products.find(p=>p.id===s.productId); return `
                                            <tr>
                                                <td>${toDisplayDate(s.tanggal)}</td>
                                                <td>${s.jam}</td>
                                                <td><strong>${prod?.name||'-'}</strong></td>
                                                <td>${s.qty}</td>
                                                <td>${s.satuan}</td>
                                                <td>${s.ekspedisi}</td>
                                                <td>${s.user}</td>
                                                <td>${s.pic}</td>
                                                <td>${s.keterangan||'-'}</td>
                                                <td class="action-icons">
                                                    <span class="edit-icon" data-id="${s.id}" data-type="out">✏️</span>
                                                    <span class="delete-icon" data-id="${s.id}" data-type="out">🗑️</span>
                                                </td>
                                            </tr>
                                        `}).join('')}
                                        ${stockOutEntries.length === 0 ? '<tr><td colspan="10" style="text-align:center; padding:30px;">Belum ada data barang keluar</td></tr>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                    ${renderSyncSection()}`; 
        document.getElementById("appContainer").innerHTML=html; 
        document.getElementById("pageTitle").innerText="Barang Keluar"; 
        document.getElementById("addStockOutBtn")?.addEventListener("click",()=>openStockOutModal()); 
        document.getElementById("exportStockOutExcelBtn")?.addEventListener("click",exportStockOutToExcel); 
        document.getElementById("printStockOutBtn")?.addEventListener("click",()=>window.print()); 
        document.querySelectorAll(".edit-icon[data-type='out']").forEach(icon=>icon.addEventListener("click",()=>openStockOutModal(parseInt(icon.dataset.id)))); 
        document.querySelectorAll(".delete-icon[data-type='out']").forEach(icon=>icon.addEventListener("click",()=>{ if(confirm("Hapus?")) deleteStockOut(parseInt(icon.dataset.id)); })); 
        attachSyncEvents(); 
    }
    
    function renderHistory(){ 
        const allTransactions=[...stockInEntries.map(s=>({...s,type:"Barang Masuk",detail:`PO: ${s.noPO} | SJ: ${s.noSJ}`})),...stockOutEntries.map(s=>({...s,type:"Barang Keluar",detail:`Ekspedisi: ${s.ekspedisi} | User: ${s.user}`}))].sort((a,b)=>`${b.tanggal} ${b.jam}`.localeCompare(`${a.tanggal} ${a.jam}`)); 
        const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">📜 Riwayat Semua Transaksi</div>
                            <div class="table-wrapper">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Tanggal</th><th>Jam</th><th>Tipe</th><th>Barang</th><th>Qty</th><th>Detail</th><th>PIC</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        ${allTransactions.map(t=>{ const prod=products.find(p=>p.id===t.productId); return `
                                            <tr>
                                                <td>${toDisplayDate(t.tanggal)}</td>
                                                <td>${t.jam}</td>
                                                <td><span class="badge">${t.type==="Barang Masuk"?"📥 Masuk":"📤 Keluar"}</span></td>
                                                <td><strong>${prod?.name||'-'}</strong></td>
                                                <td>${t.qty} ${t.satuan}</td>
                                                <td>${t.detail||'-'}</td>
                                                <td>${t.pic}</td>
                                            </tr>
                                        `}).join('')}
                                        ${allTransactions.length === 0 ? '<tr><td colspan="7" style="text-align:center; padding:30px;">Belum ada transaksi</td></tr>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                    ${renderSyncSection()}`; 
        document.getElementById("appContainer").innerHTML=html; 
        document.getElementById("pageTitle").innerText="Riwayat Transaksi"; 
        document.getElementById("exportHistoryExcelBtn")?.addEventListener("click",exportHistoryToExcel); 
        document.getElementById("printHistoryBtn")?.addEventListener("click",()=>window.print()); 
        attachSyncEvents(); 
    }
    
    function attachSyncEvents() {
        document.getElementById("exportDataBtn")?.addEventListener("click",exportAllData); 
        document.getElementById("shareWAbtn")?.addEventListener("click",()=>shareViaWhatsApp(false)); 
        document.getElementById("importDataBtn")?.addEventListener("click",()=>document.getElementById("importModal").style.display="flex"); 
        document.getElementById("pullFromOperatorBtn")?.addEventListener("click",()=>document.getElementById("pullDataModal").style.display="flex"); 
    }
    
    function exportToExcelFiltered(){ 
        const activities = getActivitiesByDate(currentViewDate);
        let filtered = activities.filter(a => a.division === currentDivision);
        if(currentWorker) filtered = filtered.filter(a => a.workerName === currentWorker);
        const exportData = filtered.map(act => ({"Tanggal":toDisplayDate(currentViewDate),"Bagian":act.division,"Pekerja":act.workerName,"Aktivitas":act.activity,"Jam Mulai":act.startTime,"Jam Selesai":act.endTime,"Durasi":act.duration,"Keterangan":act.description||"-"}));
        const ws=XLSX.utils.json_to_sheet(exportData); const wb=XLSX.utils.book_new(); XLSX.utils.book_append_sheet(wb,ws,"Daily Activity"); XLSX.writeFile(wb,`Daily_Activity_${currentViewDate}.xlsx`); 
    }
    function exportStockInToExcel(){ const exportData=stockInEntries.map(s=>{ const prod=products.find(p=>p.id===s.productId); return{"Tanggal":toDisplayDate(s.tanggal),"Jam":s.jam,"Barang":prod?.name||'-',"No PO":s.noPO,"No SJ":s.noSJ,"Supplier":s.supplier,"Jumlah":s.qty,"Satuan":s.satuan,"Keterangan":s.keterangan||'-',"PIC":s.pic}; }); const ws=XLSX.utils.json_to_sheet(exportData); const wb=XLSX.utils.book_new(); XLSX.utils.book_append_sheet(wb,ws,"Barang Masuk"); XLSX.writeFile(wb,`Barang_Masuk_${new Date().toISOString().slice(0,10)}.xlsx`); }
    function exportStockOutToExcel(){ const exportData=stockOutEntries.map(s=>{ const prod=products.find(p=>p.id===s.productId); return{"Tanggal":toDisplayDate(s.tanggal),"Jam":s.jam,"Barang":prod?.name||'-',"Jumlah":s.qty,"Satuan":s.satuan,"Ekspedisi":s.ekspedisi,"User":s.user,"PIC":s.pic,"Keterangan":s.keterangan||'-'}; }); const ws=XLSX.utils.json_to_sheet(exportData); const wb=XLSX.utils.book_new(); XLSX.utils.book_append_sheet(wb,ws,"Barang Keluar"); XLSX.writeFile(wb,`Barang_Keluar_${new Date().toISOString().slice(0,10)}.xlsx`); }
    function exportHistoryToExcel(){ const allTransactions=[...stockInEntries.map(s=>({...s,type:"Barang Masuk",detail:`PO: ${s.noPO} | SJ: ${s.noSJ}`})),...stockOutEntries.map(s=>({...s,type:"Barang Keluar",detail:`Ekspedisi: ${s.ekspedisi} | User: ${s.user}`}))].sort((a,b)=>`${b.tanggal} ${b.jam}`.localeCompare(`${a.tanggal} ${a.jam}`)); const exportData=allTransactions.map(t=>{ const prod=products.find(p=>p.id===t.productId); return{"Tanggal":toDisplayDate(t.tanggal),"Jam":t.jam,"Tipe":t.type,"Barang":prod?.name||'-',"Jumlah":`${t.qty} ${t.satuan}`,"Detail":t.detail||'-',"PIC":t.pic}; }); const ws=XLSX.utils.json_to_sheet(exportData); const wb=XLSX.utils.book_new(); XLSX.utils.book_append_sheet(wb,ws,"Riwayat"); XLSX.writeFile(wb,`Riwayat_Transaksi_${new Date().toISOString().slice(0,10)}.xlsx`); }
    
    function populateProductSelect(id){ const select=document.getElementById(id); if(select) select.innerHTML='<option value="">-- Pilih Barang --</option>'+products.map(p=>`<option value="${p.id}">${p.name} (Stok: ${p.stock})</option>`).join(''); }
    
    function changeDate(delta) {
        if(!currentViewDate) currentViewDate = formatDateToStorage(new Date());
        const newDate = new Date(currentViewDate);
        newDate.setDate(newDate.getDate() + delta);
        currentViewDate = formatDateToStorage(newDate);
        renderCurrentMenu();
    }
    function goToToday() { currentViewDate = formatDateToStorage(new Date()); renderCurrentMenu(); }
    
    // ========== MODAL FUNCTIONS ==========
    function updateWorkerSelectByDivision(division){ const workerSelect=document.getElementById("actWorkerName"); if(workerSelect){ const workersList=getOperatorsByDivision(division); workerSelect.innerHTML='<option value="">-- Pilih Pekerja --</option>'+workersList.map(w=>`<option value="${w.name}">${w.name}</option>`).join(''); } }
    
    function openActivityModal(id=null,division=null,workerName=null,dateDisplay="",activity="",start="",end="",desc=""){ 
        const modal=document.getElementById("activityModal"); 
        if(id){ 
            document.getElementById("modalTitle").innerText="Edit Aktivitas"; 
            document.getElementById("activityId").value=id; 
            document.getElementById("actDivision").value=division; 
            updateWorkerSelectByDivision(division); 
            setTimeout(()=>{ document.getElementById("actWorkerName").value=workerName; },50); 
            document.getElementById("actDate").value=dateDisplay; 
            document.getElementById("actActivity").value=activity; 
            document.getElementById("actStart").value=start; 
            document.getElementById("actEnd").value=end; 
            document.getElementById("actDesc").value=desc; 
        }else{ 
            document.getElementById("modalTitle").innerText="Tambah Aktivitas"; 
            document.getElementById("activityId").value=""; 
            document.getElementById("actDivision").value=currentDivision; 
            updateWorkerSelectByDivision(currentDivision); 
            document.getElementById("actDate").value=toDisplayDate(currentViewDate); 
            document.getElementById("actActivity").value=""; 
            document.getElementById("actStart").value="08:00"; 
            document.getElementById("actEnd").value="12:00"; 
            document.getElementById("actDesc").value=""; 
        } 
        modal.style.display="flex"; 
    }
    
    function openActivityModalOperator(id=null,division=null,workerName=null,dateDisplay="",activity="",start="",end="",desc=""){ 
        const modal=document.getElementById("activityModal"); 
        if(id){ 
            document.getElementById("modalTitle").innerText="Edit Aktivitas"; 
            document.getElementById("activityId").value=id; 
            document.getElementById("actDivision").value=division; 
            document.getElementById("actDivision").disabled=true; 
            updateWorkerSelectByDivision(division); 
            setTimeout(()=>{ document.getElementById("actWorkerName").value=workerName; document.getElementById("actWorkerName").disabled=true; },50); 
            document.getElementById("actDate").value=dateDisplay; 
            document.getElementById("actActivity").value=activity; 
            document.getElementById("actStart").value=start; 
            document.getElementById("actEnd").value=end; 
            document.getElementById("actDesc").value=desc; 
        }else{ 
            document.getElementById("modalTitle").innerText="Tambah Aktivitas"; 
            document.getElementById("activityId").value=""; 
            document.getElementById("actDivision").value=operatorDivision; 
            document.getElementById("actDivision").disabled=true; 
            updateWorkerSelectByDivision(operatorDivision); 
            setTimeout(()=>{ document.getElementById("actWorkerName").value=operatorWorkerName; document.getElementById("actWorkerName").disabled=true; },50); 
            document.getElementById("actDate").value=toDisplayDate(currentViewDate); 
            document.getElementById("actActivity").value=""; 
            document.getElementById("actStart").value="08:00"; 
            document.getElementById("actEnd").value="12:00"; 
            document.getElementById("actDesc").value=""; 
        } 
        modal.style.display="flex"; 
    }
    
    function closeActivityModal(){ document.getElementById("activityModal").style.display="none"; document.getElementById("actDivision").disabled=false; document.getElementById("actWorkerName").disabled=false; }
    function handleActivitySubmit(e){ 
        e.preventDefault(); 
        const id = document.getElementById("activityId").value; 
        const division = document.getElementById("actDivision").value; 
        const workerName = document.getElementById("actWorkerName").value; 
        const dateDisplay = document.getElementById("actDate").value; 
        if (!isValidDateDDMMYYYY(dateDisplay)) { alert("Format tanggal harus DD/MM/YYYY!"); return; } 
        const activity = document.getElementById("actActivity").value.trim(); 
        const startTime = document.getElementById("actStart").value; 
        const endTime = document.getElementById("actEnd").value; 
        const description = document.getElementById("actDesc").value; 
        if(!division||!workerName||!dateDisplay||!activity||!startTime||!endTime){ alert("Harap lengkapi semua data"); return; } 
        const storageDate = toStorageDate(dateDisplay);
        const duration = calculateDuration(startTime,endTime);
        const newActivity = { division, workerName, activity, startTime, endTime, duration, description: description||"" };
        if(id){ 
            updateActivityOnDate(storageDate, parseInt(id), newActivity); 
        }else{ 
            addActivityToDate(storageDate, newActivity); 
        } 
        if(storageDate === currentViewDate) renderCurrentMenu(); 
        closeActivityModal(); 
    }
    
    function openStockInModal(id=null){ const modal=document.getElementById("stockInModal"); populateProductSelect('inProductId'); if(id){ const data=stockInEntries.find(s=>s.id===id); if(data){ document.getElementById("stockInModalTitle").innerText="Edit Barang Masuk"; document.getElementById("stockInId").value=data.id; document.getElementById("inTanggal").value=toDisplayDate(data.tanggal); document.getElementById("inJam").value=data.jam; document.getElementById("inProductId").value=data.productId; document.getElementById("inNoPO").value=data.noPO; document.getElementById("inNoSJ").value=data.noSJ; document.getElementById("inSupplier").value=data.supplier; document.getElementById("inQty").value=data.qty; document.getElementById("inSatuan").value=data.satuan; document.getElementById("inKeterangan").value=data.keterangan||''; document.getElementById("inPIC").value=data.pic; } }else{ document.getElementById("stockInModalTitle").innerText="Tambah Barang Masuk"; document.getElementById("stockInId").value=""; document.getElementById("inTanggal").value=formatDateToInput(new Date()); document.getElementById("inJam").value=new Date().toLocaleTimeString('id-ID',{hour12:false}).slice(0,5); document.getElementById("inProductId").value=""; document.getElementById("inNoPO").value=""; document.getElementById("inNoSJ").value=""; document.getElementById("inSupplier").value=""; document.getElementById("inQty").value=""; document.getElementById("inSatuan").value="pcs"; document.getElementById("inKeterangan").value=""; document.getElementById("inPIC").value=""; } modal.style.display="flex"; }
    function closeStockInModal(){ document.getElementById("stockInModal").style.display="none"; }
    function handleStockInSubmit(e){ e.preventDefault(); const id=document.getElementById("stockInId").value; const tanggalDisplay=document.getElementById("inTanggal").value; if(!isValidDateDDMMYYYY(tanggalDisplay)){ alert("Format tanggal harus DD/MM/YYYY!"); return; } const data={tanggal:toStorageDate(tanggalDisplay),jam:document.getElementById("inJam").value,productId:parseInt(document.getElementById("inProductId").value),noPO:document.getElementById("inNoPO").value,noSJ:document.getElementById("inNoSJ").value,supplier:document.getElementById("inSupplier").value,qty:parseInt(document.getElementById("inQty").value),satuan:document.getElementById("inSatuan").value,keterangan:document.getElementById("inKeterangan").value,pic:document.getElementById("inPIC").value}; if(!data.productId||!data.qty){ alert("Lengkapi data!"); return; } if(id) updateStockIn(parseInt(id),data); else addStockIn(data); closeStockInModal(); }
    
    function openStockOutModal(id=null){ const modal=document.getElementById("stockOutModal"); populateProductSelect('outProductId'); if(id){ const data=stockOutEntries.find(s=>s.id===id); if(data){ document.getElementById("stockOutModalTitle").innerText="Edit Barang Keluar"; document.getElementById("stockOutId").value=data.id; document.getElementById("outTanggal").value=toDisplayDate(data.tanggal); document.getElementById("outJam").value=data.jam; document.getElementById("outProductId").value=data.productId; document.getElementById("outQty").value=data.qty; document.getElementById("outSatuan").value=data.satuan; document.getElementById("outEkspedisi").value=data.ekspedisi; document.getElementById("outUser").value=data.user; document.getElementById("outPIC").value=data.pic; document.getElementById("outKeterangan").value=data.keterangan||''; } }else{ document.getElementById("stockOutModalTitle").innerText="Tambah Barang Keluar"; document.getElementById("stockOutId").value=""; document.getElementById("outTanggal").value=formatDateToInput(new Date()); document.getElementById("outJam").value=new Date().toLocaleTimeString('id-ID',{hour12:false}).slice(0,5); document.getElementById("outProductId").value=""; document.getElementById("outQty").value=""; document.getElementById("outSatuan").value="pcs"; document.getElementById("outEkspedisi").value=""; document.getElementById("outUser").value=""; document.getElementById("outPIC").value=""; document.getElementById("outKeterangan").value=""; } modal.style.display="flex"; }
    function closeStockOutModal(){ document.getElementById("stockOutModal").style.display="none"; }
    function handleStockOutSubmit(e){ e.preventDefault(); const id=document.getElementById("stockOutId").value; const tanggalDisplay=document.getElementById("outTanggal").value; if(!isValidDateDDMMYYYY(tanggalDisplay)){ alert("Format tanggal harus DD/MM/YYYY!"); return; } const data={tanggal:toStorageDate(tanggalDisplay),jam:document.getElementById("outJam").value,productId:parseInt(document.getElementById("outProductId").value),qty:parseInt(document.getElementById("outQty").value),satuan:document.getElementById("outSatuan").value,ekspedisi:document.getElementById("outEkspedisi").value,user:document.getElementById("outUser").value,pic:document.getElementById("outPIC").value,keterangan:document.getElementById("outKeterangan").value}; if(!data.productId||!data.qty){ alert("Lengkapi data!"); return; } if(id) updateStockOut(parseInt(id),data); else addStockOut(data); closeStockOutModal(); }
    
    function openOperatorModal(){ const modal=document.getElementById("operatorModal"); const divisionSelect=document.getElementById("operatorDivision"); divisionSelect.innerHTML='<option value="">-- Pilih Bagian --</option>'+operatorDivisions.map(d=>`<option value="${d}">${d}</option>`).join(''); document.getElementById("operatorName").innerHTML='<option value="">-- Pilih Nama --</option>'; modal.style.display="flex"; }
    
    function toggleSidebar(){ const s=document.getElementById('sidebar'),o=document.getElementById('sidebarOverlay'); s.classList.toggle('hidden'); o.classList.toggle('active',!s.classList.contains('hidden')); }
    function closeSidebarOnMobile(){ if(window.innerWidth<=768){ document.getElementById('sidebar').classList.add('hidden'); document.getElementById('sidebarOverlay').classList.remove('active'); } }
    
    function startApp(selectedMode){ 
        document.getElementById('modeSelectionOverlay').style.display='none'; 
        document.getElementById('appWrapper').style.display='flex'; 
        loadData(); 
        if(selectedMode==='admin') showPasswordModal(); 
        else openOperatorModal(); 
    }
    
    // ========== EVENT LISTENERS ==========
    function init(){ 
        if(checkSavedSession()) {
            loadData();
            renderCurrentMenu();
        }
        document.getElementById('selectAdminMode').addEventListener('click',()=>startApp('admin')); 
        document.getElementById('selectOperatorMode').addEventListener('click',()=>startApp('operator')); 
        document.getElementById('toggleSidebarBtn').addEventListener('click',toggleSidebar); 
        document.getElementById('sidebarOverlay').addEventListener('click',closeSidebarOnMobile); 
        document.getElementById('logoutBtn').addEventListener('click',logout);
        
        document.getElementById("closeImportModal").addEventListener("click",()=>document.getElementById("importModal").style.display="none"); 
        document.getElementById("confirmImportBtn").addEventListener("click",()=>{ const file=document.getElementById("importFile").files[0]; if(file) importDataFromFile(file); else alert("Pilih file terlebih dahulu!"); document.getElementById("importModal").style.display="none"; }); 
        document.getElementById("closePullModal").addEventListener("click",()=>document.getElementById("pullDataModal").style.display="none"); 
        document.getElementById("confirmPullBtn").addEventListener("click",()=>{ const file=document.getElementById("pullDataFile").files[0]; if(file) importAndMergeDataFromFile(file); else alert("Pilih file dari operator terlebih dahulu!"); document.getElementById("pullDataModal").style.display="none"; }); 
        document.getElementById("activityForm").addEventListener("submit",handleActivitySubmit); 
        document.getElementById("closeModalBtn").addEventListener("click",closeActivityModal); 
        document.getElementById("stockInForm").addEventListener("submit",handleStockInSubmit); 
        document.getElementById("stockOutForm").addEventListener("submit",handleStockOutSubmit); 
        document.getElementById("closeStockInModalBtn").addEventListener("click",closeStockInModal); 
        document.getElementById("closeStockOutModalBtn").addEventListener("click",closeStockOutModal); 
        document.getElementById("confirmPasswordBtn").addEventListener("click",verifyPasswordAndSwitch); 
        document.getElementById("cancelPasswordBtn").addEventListener("click",()=>{ document.getElementById("passwordModal").style.display="none"; logout(); }); 
        document.getElementById("closeOperatorModal").addEventListener("click",()=>{ document.getElementById("operatorModal").style.display="none"; logout(); }); 
        document.getElementById("saveOperatorBtn").addEventListener("click",()=>{ const division=document.getElementById("opDivision").value; const name=document.getElementById("opName").value.trim(); if(!division||!name){ alert("Pilih bagian dan isi nama operator!"); return; } const editId=document.getElementById("operatorFormModal").dataset.editId; if(editId){ updateOperator(parseInt(editId),division,name); }else{ addOperator(division,name); } closeOperatorFormModal(); renderOperatorsList(); });
        document.getElementById("closeOperatorFormBtn").addEventListener("click",closeOperatorFormModal);
        document.getElementById("operatorDivision").addEventListener("change",(e)=>{ const div=e.target.value; const nameSelect=document.getElementById("operatorName"); const workers=getOperatorsByDivision(div); nameSelect.innerHTML='<option value="">-- Pilih Nama --</option>'+workers.map(w=>`<option value="${w.name}">${w.name}</option>`).join(''); });
        document.getElementById("confirmOperatorBtn").addEventListener("click",()=>{ const division=document.getElementById("operatorDivision").value; const name=document.getElementById("operatorName").value; if(!division||!name){ alert("Pilih bagian dan nama anda!"); return; } operatorDivision=division; operatorWorkerName=name; userMode='operator'; localStorage.setItem('userMode','operator'); localStorage.setItem('operatorDivision',operatorDivision); localStorage.setItem('operatorWorkerName',operatorWorkerName); document.getElementById("userRoleDisplay").innerHTML=`Mode: <strong>Operator - ${operatorDivision}</strong>`; document.querySelectorAll(".admin-only").forEach(el=>el.style.display="none"); document.getElementById("pullFromOperatorBtn").style.display="none"; document.getElementById("operatorModal").style.display="none"; currentViewDate = formatDateToStorage(new Date()); renderCurrentMenu(); });
        window.addEventListener("click",(e)=>{ 
            if(e.target===document.getElementById("activityModal")) closeActivityModal(); 
            if(e.target===document.getElementById("importModal")) document.getElementById("importModal").style.display="none"; 
            if(e.target===document.getElementById("stockInModal")) closeStockInModal(); 
            if(e.target===document.getElementById("stockOutModal")) closeStockOutModal(); 
            if(e.target===document.getElementById("pullDataModal")) document.getElementById("pullDataModal").style.display="none"; 
            if(e.target===document.getElementById("operatorFormModal")) closeOperatorFormModal(); 
            if(e.target===document.getElementById("passwordModal")){ document.getElementById("passwordModal").style.display="none"; logout(); } 
        }); 
        document.querySelectorAll(".nav-item").forEach(item=>{ item.addEventListener("click",()=>{ const menu=item.dataset.menu; if(menu==="daily-activity"){ currentMenu=menu; renderCurrentMenu(); }else if(menu==="operators" && userMode==='admin'){ currentMenu=menu; renderCurrentMenu(); }else if((menu==="stock-in"||menu==="stock-out"||menu==="history") && userMode==='admin'){ currentMenu=menu; renderCurrentMenu(); } document.querySelectorAll(".nav-item").forEach(n=>n.classList.remove("active")); item.classList.add("active"); if(window.innerWidth<=768) closeSidebarOnMobile(); }); }); 
        document.querySelector(".nav-item[data-menu='daily-activity']")?.classList.add("active"); 
    }
    document.addEventListener('DOMContentLoaded',init);
</script>
</body>
</html>
