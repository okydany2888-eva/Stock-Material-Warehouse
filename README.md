<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>Manajemen Gudang - Daily Activity</title>
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <script src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js"></script>
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
        
        /* MODE SELECTION */
        .mode-selection-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, #008080 0%, #004d4d 100%);
            z-index: 10000;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .mode-selection-card {
            background: white;
            border-radius: 32px;
            padding: 32px 28px;
            width: 85%;
            max-width: 340px;
            text-align: center;
            box-shadow: 0 25px 50px -12px rgba(0,0,0,0.3);
            animation: fadeInUp 0.4s ease-out;
        }
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .mode-selection-card h2 { font-size: 1.6rem; color: #008080; margin-bottom: 8px; }
        .mode-selection-card .subtitle { font-size: 0.75rem; color: #666; margin-bottom: 28px; border-bottom: 1px solid #eee; padding-bottom: 16px; }
        .mode-buttons { display: flex; flex-direction: column; gap: 14px; margin-bottom: 20px; }
        .mode-btn { padding: 14px 20px; border-radius: 60px; font-size: 1rem; font-weight: 600; cursor: pointer; border: none; transition: 0.2s; }
        .mode-btn:active { transform: scale(0.97); }
        .mode-btn-admin { background: #008080; color: white; box-shadow: 0 4px 12px rgba(0,128,128,0.3); }
        .mode-btn-operator { background: white; color: #008080; border: 2px solid #008080; }
        .mode-note { font-size: 0.65rem; color: #999; margin-top: 16px; padding-top: 14px; border-top: 1px solid #eee; }
        
        /* APP WRAPPER */
        .app-wrapper { display: flex; height: 100%; width: 100%; position: relative; }
        
        /* SIDEBAR - HANYA UNTUK ADMIN */
        .sidebar {
            width: 160px;
            background: linear-gradient(180deg, #008080 0%, #006666 100%);
            color: white;
            display: flex;
            flex-direction: column;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
            z-index: 1000;
            position: relative;
            overflow-y: auto;
            flex-shrink: 0;
        }
        .sidebar.hidden { transform: translateX(-100%); }
        .sidebar::-webkit-scrollbar { width: 3px; }
        .sidebar::-webkit-scrollbar-track { background: #1a8a8a; }
        .sidebar::-webkit-scrollbar-thumb { background: #40c4c4; border-radius: 10px; }
        .logo-area { 
            padding: 14px 10px 10px 10px; 
            border-bottom: 1px solid rgba(255,255,255,0.12); 
            margin-bottom: 8px;
        }
        .logo-area h2 { font-weight: 600; font-size: 0.95rem; display: flex; align-items: center; gap: 5px; }
        .logo-area h2:before { content: "🏭"; font-size: 16px; }
        .logo-area p { font-size: 0.5rem; opacity: 0.7; margin-top: 2px; }
        .user-role { 
            background: rgba(255,255,255,0.12); 
            margin: 0 6px 10px 6px; 
            padding: 6px 6px; 
            border-radius: 8px; 
            text-align: center; 
            font-size: 0.55rem; 
        }
        .nav-menu { 
            flex: 1; 
            display: flex; 
            flex-direction: column; 
            gap: 2px; 
            padding: 0 6px 12px 6px;
        }
        .nav-item {
            display: flex;
            align-items: center;
            gap: 6px;
            padding: 8px 8px;
            border-radius: 8px;
            font-weight: 500;
            font-size: 0.65rem;
            cursor: pointer;
            transition: all 0.2s ease;
            color: #e0f0f0;
        }
        .nav-item span:first-child { font-size: 0.9rem; width: 20px; flex-shrink: 0; }
        .nav-item span:last-child { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .nav-item:active { transform: scale(0.97); background: rgba(255,255,255,0.2); }
        .nav-item.active { background: #40c4c4; color: white; box-shadow: 0 2px 6px rgba(0,0,0,0.15); }
        .nav-item-logout { margin-top: 6px; border-top: 1px solid rgba(255,255,255,0.12); padding-top: 8px; color: #ffcccc; }
        .sidebar-footer { padding: 6px 10px; font-size: 0.45rem; opacity: 0.45; text-align: center; border-top: 1px solid rgba(255,255,255,0.08); margin-top: auto; }
        
        /* MAIN CONTENT */
        .main-content { flex: 1; display: flex; flex-direction: column; overflow-y: auto; background: #f5f7fb; position: relative; }
        .top-bar { background: white; padding: 10px 14px; box-shadow: 0 1px 4px rgba(0,0,0,0.04); border-bottom: 1px solid #e2e8f0; display: flex; align-items: center; gap: 10px; position: sticky; top: 0; z-index: 100; }
        .menu-toggle-btn { background: transparent; border: none; color: #008080; font-size: 1.2rem; cursor: pointer; padding: 6px; border-radius: 8px; width: 36px; height: 36px; display: flex; align-items: center; justify-content: center; }
        .menu-toggle-btn:active { transform: scale(0.94); background: #eef2f8; }
        .title-area { flex: 1; }
        .title-area h1 { font-size: 1rem; font-weight: 600; color: #1e2a3a; }
        .title-area .sub { font-size: 0.55rem; color: #5b6e8c; margin-top: 2px; }
        
        .container { flex: 1; display: flex; flex-direction: column; padding: 12px; gap: 12px; min-height: 0; overflow-y: auto; }
        .scrollable-content { flex: 1; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        
        /* CARDS */
        .card-panel { background: white; border-radius: 16px; padding: 14px; box-shadow: 0 1px 3px rgba(0,0,0,0.05); border: 1px solid #eef2f6; overflow-x: auto; }
        .section-title { font-size: 0.8rem; font-weight: 600; margin-bottom: 10px; color: #1e2f3e; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 8px; }
        .btn-group-right { display: flex; gap: 6px; align-items: center; flex-wrap: wrap; }
        .btn-text { background: transparent; border: none; color: #008080; font-weight: 500; font-size: 0.65rem; cursor: pointer; padding: 5px 10px; border-radius: 20px; }
        .btn-text:active { transform: scale(0.96); background: #eaf4fc; }
        .btn-operator-wa { background: #25D366; color: white; border: none; font-weight: 500; font-size: 0.55rem; cursor: pointer; padding: 5px 10px; border-radius: 20px; display: inline-flex; align-items: center; gap: 4px; }
        .btn-operator-add { background: #00808010; border: 1px solid #008080; color: #008080; font-weight: 500; font-size: 0.6rem; cursor: pointer; padding: 5px 12px; border-radius: 20px; }
        .btn-filter { background: #008080; border: none; color: white; padding: 6px 14px; border-radius: 25px; font-size: 0.65rem; cursor: pointer; font-weight: 500; }
        .icon-btn { background: transparent; border: none; font-size: 0.8rem; cursor: pointer; padding: 4px; border-radius: 8px; width: 30px; height: 30px; display: inline-flex; align-items: center; justify-content: center; }
        .icon-btn:active { transform: scale(0.94); background: #eef2f8; }
        .icon-excel { color: #1e6f3f; }
        .icon-print { color: #6c757d; }
        
        /* QR CODE SECTION */
        .qr-section {
            background: white;
            border-radius: 20px;
            padding: 20px;
            text-align: center;
            border: 1px solid #eef2f6;
        }
        .qr-title {
            font-size: 0.9rem;
            font-weight: 600;
            color: #008080;
            margin-bottom: 8px;
        }
        .qr-subtitle {
            font-size: 0.6rem;
            color: #6b7280;
            margin-bottom: 16px;
        }
        .qr-container {
            display: flex;
            justify-content: center;
            margin: 10px 0;
            padding: 10px;
            background: white;
            border-radius: 16px;
        }
        .qr-info {
            background: #e8f5e9;
            border-radius: 12px;
            padding: 10px;
            margin-top: 12px;
            font-size: 0.6rem;
            color: #2e7d32;
        }
        .qr-refresh-btn {
            background: #008080;
            border: none;
            color: white;
            padding: 8px 16px;
            border-radius: 25px;
            font-size: 0.65rem;
            cursor: pointer;
            margin-top: 12px;
        }
        .qr-refresh-btn:active { transform: scale(0.96); }
        
        /* SCANNER SECTION */
        .scanner-section {
            background: white;
            border-radius: 20px;
            padding: 20px;
            text-align: center;
            border: 1px solid #eef2f6;
        }
        .scanner-title {
            font-size: 0.9rem;
            font-weight: 600;
            color: #008080;
            margin-bottom: 8px;
        }
        .scanner-subtitle {
            font-size: 0.6rem;
            color: #6b7280;
            margin-bottom: 16px;
        }
        #qr-reader {
            width: 100%;
            max-width: 400px;
            margin: 0 auto;
            border-radius: 16px;
            overflow: hidden;
        }
        #qr-reader video {
            border-radius: 16px;
            width: 100%;
        }
        .scanner-controls {
            margin-top: 16px;
            display: flex;
            gap: 10px;
            justify-content: center;
            flex-wrap: wrap;
        }
        .scan-status {
            margin-top: 12px;
            padding: 10px;
            border-radius: 12px;
            font-size: 0.7rem;
        }
        .scan-success {
            background: #e8f5e9;
            color: #2e7d32;
            border: 1px solid #c8e6c9;
        }
        .scan-error {
            background: #ffebee;
            color: #c62828;
            border: 1px solid #ffcdd2;
        }
        .scan-warning {
            background: #fff3e0;
            color: #ef6c00;
            border: 1px solid #ffe0b2;
        }
        
        /* OPERATOR LOGOUT */
        .operator-logout-btn {
            background: transparent;
            border: none;
            color: #e65100;
            font-size: 0.65rem;
            font-weight: 500;
            cursor: pointer;
            padding: 6px 12px;
            text-decoration: none;
            display: inline-block;
        }
        .operator-logout-btn:active { transform: scale(0.96); opacity: 0.7; }
        
        /* DATE NAVIGATOR */
        .date-navigator {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 8px;
            padding: 8px 0;
            margin-top: 6px;
            border-top: 1px solid #eef2f6;
            flex-wrap: wrap;
        }
        .date-nav-btn {
            background: transparent;
            border: 1px solid #008080;
            color: #008080;
            font-size: 0.6rem;
            font-weight: 500;
            cursor: pointer;
            padding: 5px 12px;
            border-radius: 30px;
        }
        .date-nav-btn:active { transform: scale(0.96); background: #eef2f8; }
        .current-date-display {
            font-weight: 600;
            color: #008080;
            font-size: 0.75rem;
            background: #e0f2f1;
            padding: 5px 14px;
            border-radius: 30px;
            display: inline-block;
            white-space: nowrap;
        }
        .daily-tag { background: #ff9800; color: white; font-size: 0.5rem; margin-left: 6px; padding: 2px 6px; border-radius: 20px; font-weight: normal; }
        
        /* SYNC SECTION */
        .sync-section {
            background: #e8f5e9;
            border-radius: 14px;
            padding: 10px 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 8px;
            border: 1px solid #c8e6c9;
            margin-top: auto;
            flex-shrink: 0;
        }
        .sync-title { font-size: 0.6rem; font-weight: 600; color: #2e7d32; display: flex; align-items: center; gap: 5px; }
        .sync-buttons { display: flex; gap: 6px; flex-wrap: wrap; }
        .sync-btn { background: #008080; border: none; color: white; padding: 5px 10px; border-radius: 20px; cursor: pointer; font-size: 0.6rem; font-weight: 500; }
        .sync-btn:active { transform: scale(0.96); }
        .sync-btn-wa { background: #25D366; }
        .sync-btn-warning { background: #ff9800; }
        .sync-btn-qr { background: #6c5ce7; }
        .sync-btn-stop { background: #e65100; }
        
        /* TABLES */
        .table-responsive { overflow-x: auto; margin-top: 6px; border-radius: 12px; }
        table { width: 100%; border-collapse: collapse; font-size: 0.68rem; min-width: 500px; }
        th, td { text-align: left; padding: 8px 8px; border-bottom: 1px solid #eef2f8; vertical-align: middle; }
        th { color: #1e2a3a; font-weight: 600; background: #f8fafc; border-bottom: 2px solid #e2e8f0; font-size: 0.65rem; }
        .badge { background: #e0f2f1; padding: 3px 8px; border-radius: 30px; font-size: 0.6rem; font-weight: 600; color: #008080; display: inline-block; white-space: nowrap; }
        .activity-text { font-weight: 500; color: #1e2a3a; }
        .desc-text { font-size: 0.6rem; color: #6b7280; line-height: 1.3; max-width: 180px; word-break: break-word; }
        .time-cell { font-family: monospace; font-size: 0.65rem; font-weight: 500; color: #2c3e50; }
        
        /* TABS */
        .division-tabs { display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 10px; border-bottom: 1px solid #e2e8f0; padding-bottom: 8px; }
        .division-tab-btn { background: #f1f5f9; border: none; padding: 5px 12px; border-radius: 30px; cursor: pointer; font-weight: 500; font-size: 0.6rem; color: #334155; }
        .division-tab-btn.active { background: #008080; color: white; }
        .worker-subtabs { display: flex; gap: 5px; flex-wrap: wrap; margin-bottom: 10px; padding-left: 4px; }
        .worker-subtab-btn { background: #eef2ff; border: none; padding: 3px 8px; border-radius: 30px; cursor: pointer; font-size: 0.55rem; font-weight: 500; color: #1f3b4c; }
        .worker-subtab-btn.active { background: #008080; color: white; }
        
        /* ACTION ICONS */
        .action-icons { display: flex; gap: 8px; }
        .edit-icon, .delete-icon { cursor: pointer; font-size: 0.85rem; padding: 4px; border-radius: 6px; }
        .edit-icon:active, .delete-icon:active { transform: scale(0.92); background: #eef2f8; }
        .edit-icon { color: #008080; }
        .delete-icon { color: #e65100; }
        
        /* MODALS */
        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(0,0,0,0.5);
            align-items: center;
            justify-content: center;
            z-index: 2000;
            backdrop-filter: blur(3px);
        }
        .modal-content { background: white; max-width: 450px; width: 90%; border-radius: 24px; padding: 24px; max-height: 85vh; overflow-y: auto; }
        .footer-modal { display: flex; justify-content: flex-end; gap: 12px; margin-top: 20px; }
        form input, form select, form textarea { padding: 10px 12px; border-radius: 12px; border: 1px solid #cfdfed; width: 100%; font-size: 0.8rem; background: white; }
        form input:focus, form select:focus, form textarea:focus { outline: none; border-color: #008080; box-shadow: 0 0 0 3px rgba(0,128,128,0.1); }
        .form-grid { display: flex; flex-direction: column; gap: 12px; margin-bottom: 18px; }
        .password-input { width: 100%; padding: 12px; font-size: 0.85rem; border: 1px solid #cfdfed; border-radius: 12px; }
        .error-text { color: #d32f2f; font-size: 0.7rem; margin-top: 8px; text-align: center; }
        .permission-request {
            background: #fff3e0;
            padding: 16px;
            border-radius: 12px;
            text-align: center;
            margin-bottom: 16px;
        }
        
        /* OPERATOR LIST */
        .operator-list { display: flex; flex-direction: column; gap: 6px; margin-top: 8px; }
        .operator-item { display: flex; justify-content: space-between; align-items: center; padding: 8px 10px; background: #f8f9fa; border-radius: 10px; border: 1px solid #eef2f6; }
        .operator-info { display: flex; flex-direction: column; gap: 2px; }
        .operator-division { font-weight: 600; color: #008080; font-size: 0.7rem; }
        .operator-name { font-size: 0.65rem; color: #555; }
        .operator-actions { display: flex; gap: 8px; }
        .operator-edit, .operator-delete { cursor: pointer; font-size: 0.85rem; padding: 4px; border-radius: 6px; }
        .operator-edit:active, .operator-delete:active { background: #eef2f8; }
        .operator-edit { color: #008080; }
        .operator-delete { color: #bc5a2c; }
        
        /* OPERATOR MODE */
        .operator-mode .sidebar { display: none; }
        .operator-mode .menu-toggle-btn { display: none; }
        .operator-mode .main-content { margin-left: 0; }
        
        /* OPERATOR TOP BAR */
        .operator-top-bar {
            background: white;
            padding: 8px 14px;
            box-shadow: 0 1px 4px rgba(0,0,0,0.04);
            border-bottom: 1px solid #e2e8f0;
            display: flex;
            align-items: center;
            justify-content: space-between;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .operator-user-info {
            font-size: 0.65rem;
            color: #008080;
            background: #e0f2f1;
            padding: 4px 12px;
            border-radius: 20px;
        }
        
        @media (max-width: 768px) {
            .container { padding: 10px; }
            .top-bar { padding: 8px 12px; }
            .sidebar { position: fixed; top: 0; left: 0; height: 100%; z-index: 1000; width: 160px; }
            .sidebar.hidden { transform: translateX(-100%); }
            .sidebar-overlay { display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.5); z-index: 999; }
            .sidebar-overlay.active { display: block; }
            .sync-section { flex-direction: column; align-items: stretch; text-align: center; }
            .sync-buttons { justify-content: center; }
            .qr-container canvas, .qr-container img { width: 180px !important; height: 180px !important; }
            #qr-reader { max-width: 100%; }
        }
    </style>
</head>
<body>
<!-- MODE SELECTION SPLASH SCREEN -->
<div id="modeSelectionOverlay" class="mode-selection-overlay">
    <div class="mode-selection-card">
        <h2>🏭 WarehousePro</h2>
        <div class="subtitle">Daily Activity System</div>
        <div class="mode-buttons">
            <button class="mode-btn mode-btn-admin" id="selectAdminMode">👑 Login sebagai Admin</button>
            <button class="mode-btn mode-btn-operator" id="selectOperatorMode">👤 Login sebagai Operator</button>
        </div>
        <div class="mode-note">📱 Data tersimpan secara lokal di perangkat ini</div>
    </div>
</div>

<div class="app-wrapper" id="appWrapper" style="display: none;">
    <!-- SIDEBAR (HANYA UNTUK ADMIN) -->
    <div class="sidebar" id="sidebar">
        <div class="logo-area">
            <h2>WarehousePro</h2>
            <p>Admin Panel</p>
        </div>
        <div class="user-role" id="userRoleDisplay">Mode: <strong>Admin</strong></div>
        <div class="nav-menu">
            <div class="nav-item" data-menu="daily-activity">
                <span>📋</span>
                <span>Daily Activity</span>
            </div>
            <div class="nav-item" data-menu="operators">
                <span>👥</span>
                <span>Kelola Operator</span>
            </div>
            <div class="nav-item nav-item-logout" id="logoutBtn">
                <span>🚪</span>
                <span>Logout</span>
            </div>
        </div>
        <div class="sidebar-footer">v3.2 • Admin</div>
    </div>
    <div id="sidebarOverlay" class="sidebar-overlay"></div>
    
    <div class="main-content" id="mainContent">
        <!-- TOP BAR UNTUK ADMIN -->
        <div class="top-bar" id="adminTopBar">
            <button class="menu-toggle-btn" id="toggleSidebarBtn">☰</button>
            <div class="title-area"><h1 id="pageTitle">Daily Activity</h1><div class="sub" id="pageDesc">Aktivitas harian per bagian</div></div>
        </div>
        
        <!-- TOP BAR UNTUK OPERATOR -->
        <div class="operator-top-bar" id="operatorTopBar" style="display: none;">
            <div class="operator-user-info" id="operatorUserInfo"></div>
            <button class="operator-logout-btn" id="operatorLogoutBtn">Logout</button>
        </div>
        
        <div class="container" id="appContainer"></div>
    </div>
</div>

<!-- MODAL PERMINTAAN AKSES KAMERA -->
<div id="cameraPermissionModal" class="modal">
    <div class="modal-content">
        <h3>📷 Izin Akses Kamera</h3>
        <div class="permission-request">
            <p style="font-size:0.8rem; margin-bottom:12px;">Aplikasi memerlukan akses kamera untuk memindai QR Code dari operator.</p>
            <p style="font-size:0.7rem; color:#666;">Kamera akan digunakan hanya untuk scanning QR Code sinkronisasi data.</p>
        </div>
        <div class="footer-modal">
            <button class="btn-text" id="cancelCameraBtn">Batal</button>
            <button class="btn-filter" id="allowCameraBtn">Izinkan Akses Kamera</button>
        </div>
    </div>
</div>

<!-- MODAL SCAN QR -->
<div id="scanQRModal" class="modal">
    <div class="modal-content scan-modal-content">
        <h3>📷 Scan QR Code Operator</h3>
        <p style="font-size:0.7rem; margin:8px 0;">Arahkan kamera ke QR Code yang ditampilkan operator</p>
        <div id="qr-reader" style="width:100%;"></div>
        <div id="scanStatus" class="scan-status" style="display:none;"></div>
        <div class="scanner-controls">
            <button class="sync-btn sync-btn-stop" id="stopScanBtn">⏹️ Hentikan Scan</button>
            <button class="btn-text" id="closeScanModalBtn">Tutup</button>
        </div>
    </div>
</div>

<!-- MODAL LAINNYA -->
<div id="passwordModal" class="modal">
    <div class="modal-content">
        <h3>🔐 Akses Admin</h3>
        <p style="font-size:0.7rem; margin:8px 0;">Masukkan password untuk mengakses menu Admin</p>
        <input type="password" id="adminPassword" class="password-input" placeholder="Masukkan password">
        <div id="passwordError" class="error-text"></div>
        <div class="footer-modal">
            <button class="btn-text" id="cancelPasswordBtn">Batal</button>
            <button class="btn-filter" id="confirmPasswordBtn">Masuk</button>
        </div>
    </div>
</div>

<div id="operatorModal" class="modal">
    <div class="modal-content">
        <h3>👤 Login Operator</h3>
        <div class="form-grid">
            <select id="operatorDivision">
                <option value="">-- Pilih Bagian --</option>
                <option value="Raw Material">Raw Material</option>
                <option value="Finish Goods">Finish Goods</option>
                <option value="Save Material">Save Material</option>
            </select>
            <select id="operatorName">
                <option value="">-- Pilih Nama --</option>
            </select>
        </div>
        <div class="footer-modal">
            <button class="btn-text" id="closeOperatorModal">Batal</button>
            <button class="btn-filter" id="confirmOperatorBtn">Masuk sebagai Operator</button>
        </div>
    </div>
</div>

<div id="operatorFormModal" class="modal">
    <div class="modal-content">
        <h3 id="operatorFormTitle">Tambah Operator</h3>
        <div class="form-grid">
            <select id="opDivision" required>
                <option value="">-- Pilih Bagian --</option>
                <option value="Leader">Leader</option>
                <option value="Raw Material">Raw Material</option>
                <option value="Finish Goods">Finish Goods</option>
                <option value="Save Material">Save Material</option>
            </select>
            <input type="text" id="opName" placeholder="Nama Operator" required>
        </div>
        <div class="footer-modal">
            <button class="btn-text" id="closeOperatorFormBtn">Batal</button>
            <button class="btn-filter" id="saveOperatorBtn">Simpan</button>
        </div>
    </div>
</div>

<div id="activityModal" class="modal">
    <div class="modal-content">
        <h3 id="modalTitle">Tambah Aktivitas</h3>
        <form id="activityForm">
            <input type="hidden" id="activityId">
            <div class="form-grid">
                <input type="text" id="actDate" placeholder="DD/MM/YYYY" required>
                <select id="actDivision" required><option value="">-- Pilih Bagian --</option></select>
                <select id="actWorkerName" required><option value="">-- Pilih Pekerja --</option></select>
                <input type="text" id="actActivity" placeholder="Nama Aktivitas" required>
                <input type="time" id="actStart" required>
                <input type="time" id="actEnd" required>
                <textarea id="actDesc" rows="2" placeholder="Keterangan (opsional)"></textarea>
            </div>
            <div class="footer-modal">
                <button type="button" class="btn-text" id="closeModalBtn">Batal</button>
                <button type="submit" class="btn-filter">Simpan</button>
            </div>
        </form>
    </div>
</div>

<div id="importModal" class="modal">
    <div class="modal-content">
        <h3>📥 Import Data</h3>
        <p style="font-size:0.7rem; margin:8px 0;">Pilih file backup JSON</p>
        <input type="file" id="importFile" accept=".json">
        <div class="footer-modal">
            <button class="btn-text" id="closeImportModal">Batal</button>
            <button class="btn-filter" id="confirmImportBtn">Import</button>
        </div>
    </div>
</div>

<script>
    // ========== KONFIGURASI ==========
    const ADMIN_PASSWORD = "admin123";
    let userMode = null;
    let isAdminAuthenticated = false;
    let operatorDivision = '';
    let operatorWorkerName = '';
    let dynamicOperators = [];
    let qrCodeInstance = null;
    let html5QrCode = null;
    let isScanning = false;
    const operatorDivisions = ["Raw Material", "Finish Goods", "Save Material"];
    const allDivisions = ["Leader", "Raw Material", "Finish Goods", "Save Material"];
    let dailyActivitiesMap = {};
    let currentViewDate = null;
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
                { id: 1, division: "Leader", workerName: "Oky Dany P.", activity: "Koordinasi tim operasional", startTime: "08:00", endTime: "10:00", duration: "2 jam 0 menit", description: "Meeting pagi" },
                { id: 2, division: "Raw Material", workerName: "Darma", activity: "Pengecekan stok bahan baku", startTime: "09:00", endTime: "11:30", duration: "2 jam 30 menit", description: "Inspeksi" },
                { id: 3, division: "Finish Goods", workerName: "Athar", activity: "Packing barang jadi", startTime: "10:00", endTime: "12:00", duration: "2 jam 0 menit", description: "Packing" }
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
    
    // ========== FUNGSI QR CODE ==========
    function generateQRData() {
        const today = formatDateToStorage(new Date());
        const activities = getActivitiesByDate(today);
        const operatorActivities = activities.filter(a => a.division === operatorDivision && a.workerName === operatorWorkerName);
        
        const qrData = {
            type: "QR_SYNC",
            operator: {
                division: operatorDivision,
                name: operatorWorkerName,
                date: today
            },
            activities: operatorActivities,
            timestamp: new Date().toISOString()
        };
        return JSON.stringify(qrData);
    }
    
    function refreshQRCode() {
        const qrContainer = document.getElementById("qrcode");
        if (!qrContainer) return;
        
        qrContainer.innerHTML = "";
        const qrData = generateQRData();
        
        try {
            qrCodeInstance = new QRCode(qrContainer, {
                text: qrData,
                width: 200,
                height: 200,
                colorDark: "#008080",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.H
            });
            
            const updateTime = new Date().toLocaleTimeString();
            const infoDiv = document.getElementById("qrInfoText");
            if (infoDiv) infoDiv.innerHTML = `📅 Tanggal: ${toDisplayDate(currentViewDate || formatDateToStorage(new Date()))}<br>⏱️ Diperbarui: ${updateTime}<br>📊 Total aktivitas: ${JSON.parse(qrData).activities.length}`;
        } catch(e) {
            console.log("QR Generate error:", e);
        }
    }
    
    // ========== FUNGSI SCAN QR DENGAN KAMERA ==========
    async function requestCameraPermission() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: true });
            stream.getTracks().forEach(track => track.stop());
            return true;
        } catch (err) {
            console.error("Camera permission denied:", err);
            return false;
        }
    }
    
    async function startScanner() {
        const scanStatus = document.getElementById("scanStatus");
        
        if (html5QrCode && isScanning) {
            try {
                await html5QrCode.stop();
                isScanning = false;
            } catch(e) {}
        }
        
        const hasPermission = await requestCameraPermission();
        if (!hasPermission) {
            scanStatus.style.display = "block";
            scanStatus.innerHTML = "❌ Tidak dapat mengakses kamera. Silakan izinkan akses kamera di pengaturan browser.";
            scanStatus.className = "scan-status scan-error";
            return;
        }
        
        html5QrCode = new Html5Qrcode("qr-reader");
        const config = { fps: 10, qrbox: { width: 250, height: 250 } };
        
        try {
            isScanning = true;
            scanStatus.style.display = "block";
            scanStatus.innerHTML = "📷 Menunggu QR Code... Arahkan kamera ke QR Code operator.";
            scanStatus.className = "scan-status scan-warning";
            
            await html5QrCode.start(
                { facingMode: "environment" },
                config,
                (decodedText, decodedResult) => {
                    // QR Code terdeteksi
                    handleQRScanResult(decodedText);
                },
                (errorMessage) => {
                    // Error scanning, ignore
                    // console.log(errorMessage);
                }
            );
        } catch (err) {
            console.error("Failed to start scanner:", err);
            scanStatus.style.display = "block";
            scanStatus.innerHTML = "❌ Gagal memulai kamera. Pastikan perangkat memiliki kamera dan izinkan akses.";
            scanStatus.className = "scan-status scan-error";
            isScanning = false;
        }
    }
    
    async function stopScanner() {
        if (html5QrCode && isScanning) {
            try {
                await html5QrCode.stop();
                isScanning = false;
                const scanStatus = document.getElementById("scanStatus");
                scanStatus.style.display = "none";
            } catch(e) {}
        }
    }
    
    function handleQRScanResult(qrString) {
        try {
            const qrData = JSON.parse(qrString);
            if (qrData.type !== "QR_SYNC") {
                showScanStatus("❌ QR Code tidak valid! Pastikan QR dari aplikasi WarehousePro.", "scan-error");
                return;
            }
            
            const { operator, activities, timestamp } = qrData;
            const syncDate = operator.date;
            
            let mergeCount = 0;
            
            if (activities && activities.length > 0) {
                if (!dailyActivitiesMap[syncDate]) {
                    dailyActivitiesMap[syncDate] = [];
                }
                
                activities.forEach(newAct => {
                    const exists = dailyActivitiesMap[syncDate].some(ex => 
                        ex.division === newAct.division && 
                        ex.workerName === newAct.workerName && 
                        ex.activity === newAct.activity && 
                        ex.startTime === newAct.startTime
                    );
                    
                    if (!exists) {
                        const maxId = dailyActivitiesMap[syncDate].length > 0 ? 
                            Math.max(...dailyActivitiesMap[syncDate].map(a => a.id)) : 0;
                        newAct.id = maxId + dailyActivitiesMap[syncDate].length + 1;
                        dailyActivitiesMap[syncDate].push(newAct);
                        mergeCount++;
                    }
                });
                
                saveDailyActivities();
            }
            
            const successMsg = `✅ BERHASIL SINKRONISASI!\n\n📋 Operator: ${operator.name}\n🏭 Bagian: ${operator.division}\n📅 Tanggal: ${toDisplayDate(syncDate)}\n✅ Data baru: ${mergeCount} aktivitas\n⏰ Waktu: ${new Date().toLocaleTimeString()}`;
            
            showScanStatus(successMsg, "scan-success");
            
            setTimeout(() => {
                stopScanner();
                document.getElementById("scanQRModal").style.display = "none";
                renderCurrentMenu();
                alert(successMsg);
            }, 2000);
            
        } catch (error) {
            showScanStatus("❌ Gagal memproses QR Code: " + error.message, "scan-error");
        }
    }
    
    function showScanStatus(message, className) {
        const scanStatus = document.getElementById("scanStatus");
        scanStatus.style.display = "block";
        scanStatus.innerHTML = message;
        scanStatus.className = `scan-status ${className}`;
    }
    
    // ========== FUNGSI BANTU ==========
    function toStorageDate(dmy) { if (!dmy) return ""; let parts = dmy.split('/'); if (parts.length === 3) return `${parts[2]}-${parts[1]}-${parts[0]}`; return dmy; }
    function toDisplayDate(ymd) { if (!ymd) return ""; let parts = ymd.split('-'); if (parts.length === 3) return `${parts[2]}/${parts[1]}/${parts[0]}`; return ymd; }
    function isValidDateDDMMYYYY(dateStr) { let parts = dateStr.split('/'); if (parts.length !== 3) return false; let day = parseInt(parts[0]), month = parseInt(parts[1]), year = parseInt(parts[2]); if (isNaN(day)||isNaN(month)||isNaN(year)) return false; if (month<1||month>12) return false; let daysInMonth = new Date(year, month, 0).getDate(); return day>=1 && day<=daysInMonth; }
    function formatDateToInput(date) { let dd = String(date.getDate()).padStart(2,'0'); let mm = String(date.getMonth()+1).padStart(2,'0'); let yyyy = date.getFullYear(); return `${dd}/${mm}/${yyyy}`; }
    function formatDateToStorage(date) { return date.toISOString().slice(0,10); }
    function calculateDuration(start,end) { if (!start||!end) return "-"; const startDate = new Date(`2000-01-01T${start}`); const endDate = new Date(`2000-01-01T${end}`); let diff = (endDate-startDate)/(1000*60); if (diff<0) diff+=24*60; return `${Math.floor(diff/60)} jam ${diff%60} menit`; }
    
    function loadData() { getOperators(); loadDailyActivities(); }
    
    // ========== EXPORT & IMPORT ==========
    function exportAllData() { 
        const allData = { dailyActivitiesMap, dynamicOperators, exportDate: new Date().toISOString(), version: "3.2" }; 
        const dataStr = JSON.stringify(allData, null, 2); 
        const blob = new Blob([dataStr], {type: 'application/json'}); 
        const url = URL.createObjectURL(blob); 
        const a = document.createElement('a'); 
        a.href = url; 
        a.download = `warehouse_backup_${new Date().toISOString().slice(0,19).replace(/:/g,'-')}.json`; 
        a.click(); 
        URL.revokeObjectURL(url); 
        alert("Data berhasil diexport!"); 
    }
    function exportDailyActivityOnly() { 
        const dailyData = { dailyActivitiesMap, operatorDivision, operatorWorkerName, exportDate: new Date().toISOString(), type: "DAILY_ACTIVITY_ONLY" }; 
        const dataStr = JSON.stringify(dailyData, null, 2); 
        const blob = new Blob([dataStr], {type: 'application/json'}); 
        const url = URL.createObjectURL(blob); 
        const a = document.createElement('a'); 
        a.href = url; 
        a.download = `daily_activity_${operatorDivision}_${operatorWorkerName}_${new Date().toISOString().slice(0,10)}.json`; 
        a.click(); 
        URL.revokeObjectURL(url); 
        alert("Data Daily Activity berhasil diexport!"); 
    }
    function sendWhatsAppMessage(message) { window.open(`https://wa.me/?text=${encodeURIComponent(message)}`, '_blank'); }
    function shareViaWhatsApp(isOperator) { 
        let message = isOperator && userMode === 'operator' ? 
            `*DATA DAILY ACTIVITY - ${operatorDivision} (${operatorWorkerName})*\n\nHalo Admin, berikut data Daily Activity saya.\n📅 ${new Date().toLocaleDateString()}\n\nScan QR Code di aplikasi untuk sinkronisasi otomatis!\n\n- ${operatorWorkerName} -` : 
            `*BACKUP DATA WAREHOUSEPRO*\n\n📅 ${new Date().toLocaleString()}\n\nCara restore:\n1. Buka WarehousePro\n2. Klik "📥 Import"\n3. Pilih file backup ini`; 
        alert("Pesan WhatsApp akan dibuka."); 
        sendWhatsAppMessage(message); 
    }
    function importAndMergeDataFromFile(file) { 
        const reader = new FileReader(); 
        reader.onload = function(e) { 
            try { 
                const importedData = JSON.parse(e.target.result); 
                let mergeCount = 0; 
                if (importedData.type === "DAILY_ACTIVITY_ONLY" && importedData.dailyActivitiesMap) { 
                    Object.keys(importedData.dailyActivitiesMap).forEach(date => { 
                        if (!dailyActivitiesMap[date]) dailyActivitiesMap[date] = []; 
                        importedData.dailyActivitiesMap[date].forEach(newAct => { 
                            const exists = dailyActivitiesMap[date].some(ex => ex.division === newAct.division && ex.workerName === newAct.workerName && ex.activity === newAct.activity && ex.startTime === newAct.startTime); 
                            if (!exists) { 
                                const maxId = dailyActivitiesMap[date].length > 0 ? Math.max(...dailyActivitiesMap[date].map(a => a.id)) : 0; 
                                newAct.id = maxId + dailyActivitiesMap[date].length + 1; 
                                dailyActivitiesMap[date].push(newAct); 
                                mergeCount++; 
                            } 
                        }); 
                    }); 
                    saveDailyActivities(); 
                    alert(`Import berhasil!\n✅ Data baru ditambahkan: ${mergeCount} aktivitas`); 
                    renderCurrentMenu(); 
                } else if (importedData.dailyActivitiesMap) { 
                    Object.keys(importedData.dailyActivitiesMap).forEach(date => { 
                        if (!dailyActivitiesMap[date]) dailyActivitiesMap[date] = []; 
                        importedData.dailyActivitiesMap[date].forEach(newAct => { 
                            const exists = dailyActivitiesMap[date].some(ex => ex.division === newAct.division && ex.workerName === newAct.workerName && ex.activity === newAct.activity); 
                            if (!exists) { 
                                const maxId = dailyActivitiesMap[date].length > 0 ? Math.max(...dailyActivitiesMap[date].map(a => a.id)) : 0; 
                                newAct.id = maxId + dailyActivitiesMap[date].length + 1; 
                                dailyActivitiesMap[date].push(newAct); 
                                mergeCount++; 
                            } 
                        }); 
                    }); 
                    if (importedData.dynamicOperators) { dynamicOperators = importedData.dynamicOperators; saveOperators(); } 
                    saveDailyActivities(); 
                    alert(`Import full backup berhasil!\n✅ Daily Activity baru: ${mergeCount}`); 
                    renderCurrentMenu(); 
                } else alert("Format file tidak dikenali."); 
            } catch(err) { alert("File tidak valid!"); } 
        }; 
        reader.readAsText(file); 
    }
    function importDataFromFile(file) { importAndMergeDataFromFile(file); }
    
    // ========== AUTH ==========
    function showPasswordModal() { 
        const modal = document.getElementById("passwordModal"); 
        document.getElementById("adminPassword").value = ""; 
        document.getElementById("passwordError").textContent = ""; 
        modal.style.display = "flex"; 
        document.getElementById("adminPassword").focus(); 
    }
    function verifyPasswordAndSwitch() { 
        const password = document.getElementById("adminPassword").value; 
        if (password === ADMIN_PASSWORD) { 
            isAdminAuthenticated = true; 
            userMode = 'admin'; 
            localStorage.setItem('userMode', 'admin'); 
            localStorage.setItem('isAdminAuthenticated', 'true'); 
            document.getElementById("userRoleDisplay").innerHTML = `Mode: <strong>Admin</strong>`; 
            document.getElementById("passwordModal").style.display = "none"; 
            currentViewDate = formatDateToStorage(new Date()); 
            document.getElementById('appWrapper').classList.remove('operator-mode');
            document.getElementById('adminTopBar').style.display = 'flex';
            document.getElementById('operatorTopBar').style.display = 'none';
            renderCurrentMenu(); 
        } else { 
            document.getElementById("passwordError").textContent = "Password salah! Akses ditolak."; 
            document.getElementById("adminPassword").value = ""; 
        } 
    }
    function logout() {
        if (html5QrCode && isScanning) {
            html5QrCode.stop().catch(e => {});
            isScanning = false;
        }
        userMode = null; 
        isAdminAuthenticated = false; 
        operatorDivision = ''; 
        operatorWorkerName = ''; 
        currentViewDate = null;
        localStorage.removeItem('userMode'); 
        localStorage.removeItem('isAdminAuthenticated'); 
        localStorage.removeItem('operatorDivision'); 
        localStorage.removeItem('operatorWorkerName');
        document.getElementById('appWrapper').style.display = 'none';
        document.getElementById('modeSelectionOverlay').style.display = 'flex';
        document.getElementById('appWrapper').classList.remove('operator-mode');
    }
    function checkSavedSession() {
        const savedMode = localStorage.getItem('userMode');
        const savedAdminAuth = localStorage.getItem('isAdminAuthenticated');
        const savedOperatorDiv = localStorage.getItem('operatorDivision');
        const savedOperatorName = localStorage.getItem('operatorWorkerName');
        if (savedMode === 'admin' && savedAdminAuth === 'true') {
            userMode = 'admin'; 
            isAdminAuthenticated = true;
            document.getElementById("userRoleDisplay").innerHTML = `Mode: <strong>Admin</strong>`;
            document.getElementById('modeSelectionOverlay').style.display = 'none';
            document.getElementById('appWrapper').style.display = 'flex';
            document.getElementById('appWrapper').classList.remove('operator-mode');
            document.getElementById('adminTopBar').style.display = 'flex';
            document.getElementById('operatorTopBar').style.display = 'none';
            currentViewDate = formatDateToStorage(new Date());
            renderCurrentMenu();
            return true;
        } else if (savedMode === 'operator' && savedOperatorDiv && savedOperatorName) {
            userMode = 'operator'; 
            operatorDivision = savedOperatorDiv; 
            operatorWorkerName = savedOperatorName;
            document.getElementById('modeSelectionOverlay').style.display = 'none';
            document.getElementById('appWrapper').style.display = 'flex';
            document.getElementById('appWrapper').classList.add('operator-mode');
            document.getElementById('adminTopBar').style.display = 'none';
            document.getElementById('operatorTopBar').style.display = 'flex';
            document.getElementById('operatorUserInfo').innerHTML = `👤 ${operatorWorkerName} | ${operatorDivision}`;
            currentViewDate = formatDateToStorage(new Date());
            renderCurrentMenu();
            return true;
        }
        return false;
    }
    
    // ========== RENDER ==========
    function renderCurrentMenu() { 
        if (userMode === 'operator') {
            renderOperatorDailyActivity();
        } else if (userMode === 'admin') { 
            if (currentMenu === "daily-activity") renderAdminDailyActivity();
            else if (currentMenu === "operators") renderOperatorsList();
        }
    }
    
    function renderSyncSection(isOperator = false) {
        if (isOperator) {
            return `<div class="sync-section">
                        <div class="sync-title"><span>📡</span> Sinkronisasi Data</div>
                        <div class="sync-buttons">
                            <button class="sync-btn sync-btn-qr" id="refreshQRBtn">🔄 Refresh QR</button>
                            <button class="sync-btn sync-btn-wa" id="shareWAbtnOp">💬 Kirim ke Admin</button>
                            <button class="sync-btn" id="exportDataBtnOp">📤 Export</button>
                        </div>
                    </div>`;
        }
        return `<div class="sync-section">
                    <div class="sync-title"><span>📡</span> Sinkronisasi</div>
                    <div class="sync-buttons">
                        <button class="sync-btn sync-btn-qr" id="scanQRBtn">📷 Scan QR Operator</button>
                        <button class="sync-btn" id="exportDataBtn">📤 Export</button>
                        <button class="sync-btn sync-btn-wa" id="shareWAbtn">💬 Share</button>
                        <button class="sync-btn" id="importDataBtn">📥 Import</button>
                    </div>
                </div>`;
    }
    
    function renderDateNavigator() {
        if (currentViewDate) {
            return `<div class="date-navigator">
                        <button class="date-nav-btn" id="prevDateBtn">◀</button>
                        <span class="current-date-display">${toDisplayDate(currentViewDate)}<span class="daily-tag">Harian</span></span>
                        <button class="date-nav-btn" id="nextDateBtn">▶</button>
                        <button class="date-nav-btn" id="todayDateBtn">📅 Hari Ini</button>
                    </div>`;
        }
        return '';
    }
    
    function renderOperatorsList() { 
        const ops = getOperators(); 
        const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>👥 Daftar Operator</span>
                                <div class="btn-group-right">
                                    <button class="btn-text" id="addOperatorBtn" style="font-size:1rem;">+ Tambah</button>
                                </div>
                            </div>
                            <div class="operator-list">
                                ${ops.map(op => `<div class="operator-item"><div class="operator-info"><span class="operator-division">${op.division}</span><span class="operator-name">${op.name}</span></div><div class="operator-actions"><span class="operator-edit" data-id="${op.id}" data-division="${op.division}" data-name="${op.name}">✏️</span><span class="operator-delete" data-id="${op.id}">🗑️</span></div></div>`).join('')}
                            </div>
                        </div>
                    </div>
                    ${renderSyncSection(false)}`; 
        document.getElementById("appContainer").innerHTML = html; 
        document.getElementById("pageTitle").innerText = "Kelola Operator"; 
        document.getElementById("pageDesc").innerText = "Tambah, edit, atau hapus operator"; 
        document.getElementById("addOperatorBtn")?.addEventListener("click", () => openOperatorFormModal()); 
        document.querySelectorAll(".operator-edit").forEach(el => el.addEventListener("click", () => { const id = parseInt(el.dataset.id), division = el.dataset.division, name = el.dataset.name; openOperatorFormModal(id, division, name); })); 
        document.querySelectorAll(".operator-delete").forEach(el => el.addEventListener("click", () => { if (confirm("Hapus operator ini?")) deleteOperator(parseInt(el.dataset.id)); })); 
        attachSyncEvents(false);
    }
    
    function renderAdminDailyActivity() { 
        const activities = getActivitiesByDate(currentViewDate);
        let filtered = activities.filter(a => a.division === currentDivision);
        if (currentWorker) filtered = filtered.filter(a => a.workerName === currentWorker);
        filtered.sort((a, b) => a.startTime.localeCompare(b.startTime));
        const divisionTabs = allDivisions.map(div => `<button class="division-tab-btn ${currentDivision === div ? 'active' : ''}" data-division="${div}">${div}</button>`).join('');
        const workersList = getOperatorsByDivision(currentDivision);
        const workerSubtabs = `<div class="worker-subtabs"><button class="worker-subtab-btn ${currentWorker === '' ? 'active' : ''}" data-worker="">Semua</button>${workersList.map(w => `<button class="worker-subtab-btn ${currentWorker === w.name ? 'active' : ''}" data-worker="${w.name}">${w.name}</button>`).join('')}</div>`;
        
        const html = `<div class="scrollable-content">
                        <div class="card-panel">
                            <div class="section-title">
                                <span>📋 Daily Activity - ${toDisplayDate(currentViewDate)}</span>
                                <div class="btn-group-right">
                                    <button class="icon-btn icon-excel" id="exportExcelBtn">📎</button>
                                    <button class="icon-btn icon-print" id="printBtn">🖨️</button>
                                    <button class="btn-text" id="addActivityBtn">+ Tambah</button>
                                </div>
                            </div>
                            <div class="division-tabs">${divisionTabs}</div>
                            ${workerSubtabs}
                            <div class="table-responsive">
                                <table>
                                    <thead><tr><th>Pekerja</th><th>Aktivitas</th><th>Mulai</th><th>Selesai</th><th>Durasi</th><th>Keterangan</th><th>Aksi</th></tr></thead>
                                    <tbody>
                                        ${filtered.map(act => `<tr><td><strong>${act.workerName}</strong></td><td><span class="activity-text">${act.activity}</span></td><td class="time-cell">${act.startTime}</td><td class="time-cell">${act.endTime}</td><td><span class="badge">${act.duration}</span></td><td><div class="desc-text">${act.description || '-'}</div></td><td class="action-icons"><span class="edit-icon" data-id="${act.id}">✏️</span><span class="delete-icon" data-id="${act.id}">🗑️</span></td>`).join('')}
                                        ${filtered.length === 0 ? '<tr><td colspan="7" style="text-align:center; padding:24px;">✨ Belum ada aktivitas</td>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        ${renderDateNavigator()}
                    </div>
                    ${renderSyncSection(false)}`; 
        document.getElementById("appContainer").innerHTML = html; 
        document.getElementById("pageTitle").innerText = "Daily Activity"; 
        document.getElementById("pageDesc").innerText = "Admin - Data Harian";
        attachAdminEvents(); 
        attachSyncEvents(false);
    }
    
    function renderOperatorDailyActivity() { 
        const activities = getActivitiesByDate(currentViewDate);
        let filtered = activities.filter(a => a.division === operatorDivision && a.workerName === operatorWorkerName);
        filtered.sort((a, b) => a.startTime.localeCompare(b.startTime));
        
        const html = `<div class="scrollable-content">
                        <div class="qr-section">
                            <div class="qr-title">📱 QR Code Sinkronisasi</div>
                            <div class="qr-subtitle">Scan QR ini dengan Admin untuk sinkron data harian</div>
                            <div class="qr-container" id="qrcode"></div>
                            <div class="qr-info" id="qrInfoText"></div>
                            <button class="qr-refresh-btn" id="refreshQRBtnBottom">🔄 Refresh QR Code</button>
                        </div>
                        
                        <div class="card-panel">
                            <div class="section-title">
                                <span>📋 Daily Activity - ${toDisplayDate(currentViewDate)}</span>
                                <div class="btn-group-right">
                                    <button class="btn-operator-add" id="addActivityBtnOp">+ Tambah Aktivitas</button>
                                </div>
                            </div>
                            <div class="table-responsive">
                                <table>
                                    <thead><tr><th>Aktivitas</th><th>Mulai</th><th>Selesai</th><th>Durasi</th><th>Keterangan</th><th>Aksi</th></tr></thead>
                                    <tbody>
                                        ${filtered.map(act => `<tr><td><span class="activity-text">${act.activity}</span></td><td class="time-cell">${act.startTime}</td><td class="time-cell">${act.endTime}</td><td><span class="badge">${act.duration}</span></td><td><div class="desc-text">${act.description || '-'}</div></td><td class="action-icons"><span class="edit-icon" data-id="${act.id}">✏️</span><span class="delete-icon" data-id="${act.id}">🗑️</span></td>`).join('')}
                                        ${filtered.length === 0 ? '<tr><td colspan="6" style="text-align:center; padding:32px;">📝 Belum ada aktivitas untuk hari ini. Silakan tambah aktivitas baru.</td>' : ''}
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        ${renderDateNavigator()}
                    </div>
                    ${renderSyncSection(true)}`; 
        document.getElementById("appContainer").innerHTML = html; 
        document.getElementById("pageTitle").innerText = `Daily Activity`; 
        document.getElementById("pageDesc").innerText = `${operatorDivision} - ${operatorWorkerName}`; 
        
        setTimeout(() => {
            refreshQRCode();
        }, 100);
        
        document.getElementById("addActivityBtnOp")?.addEventListener("click", () => openActivityModalOperator()); 
        document.getElementById("refreshQRBtn")?.addEventListener("click", () => refreshQRCode());
        document.getElementById("refreshQRBtnBottom")?.addEventListener("click", () => refreshQRCode());
        document.getElementById("prevDateBtn")?.addEventListener("click", () => changeDate(-1));
        document.getElementById("nextDateBtn")?.addEventListener("click", () => changeDate(1));
        document.getElementById("todayDateBtn")?.addEventListener("click", goToToday);
        document.querySelectorAll(".edit-icon").forEach(icon => { const id = parseInt(icon.dataset.id); const act = getActivitiesByDate(currentViewDate).find(a => a.id === id); if (act) icon.addEventListener("click", () => openActivityModalOperator(id, act.division, act.workerName, toDisplayDate(currentViewDate), act.activity, act.startTime, act.endTime, act.description)); }); 
        document.querySelectorAll(".delete-icon").forEach(icon => icon.addEventListener("click", () => { if (confirm("Hapus aktivitas ini?")) deleteActivityFromDate(currentViewDate, parseInt(icon.dataset.id)); renderOperatorDailyActivity(); })); 
        attachSyncEvents(true);
    }
    
    function attachAdminEvents() { 
        document.querySelectorAll(".division-tab-btn").forEach(btn => btn.addEventListener("click", () => { currentDivision = btn.dataset.division; currentWorker = ""; renderAdminDailyActivity(); })); 
        document.querySelectorAll(".worker-subtab-btn").forEach(btn => btn.addEventListener("click", () => { currentWorker = btn.dataset.worker; renderAdminDailyActivity(); })); 
        document.getElementById("addActivityBtn")?.addEventListener("click", () => openActivityModal()); 
        document.getElementById("exportExcelBtn")?.addEventListener("click", exportToExcelFiltered); 
        document.getElementById("printBtn")?.addEventListener("click", () => window.print()); 
        document.getElementById("prevDateBtn")?.addEventListener("click", () => changeDate(-1));
        document.getElementById("nextDateBtn")?.addEventListener("click", () => changeDate(1));
        document.getElementById("todayDateBtn")?.addEventListener("click", goToToday);
        document.querySelectorAll(".edit-icon").forEach(icon => { const id = parseInt(icon.dataset.id); const act = getActivitiesByDate(currentViewDate).find(a => a.id === id); if (act) icon.addEventListener("click", () => openActivityModal(id, act.division, act.workerName, toDisplayDate(currentViewDate), act.activity, act.startTime, act.endTime, act.description)); }); 
        document.querySelectorAll(".delete-icon").forEach(icon => icon.addEventListener("click", () => { if (confirm("Hapus aktivitas ini?")) deleteActivityFromDate(currentViewDate, parseInt(icon.dataset.id)); renderAdminDailyActivity(); })); 
    }
    
    function attachSyncEvents(isOperator) {
        if (isOperator) {
            document.getElementById("exportDataBtnOp")?.addEventListener("click", exportDailyActivityOnly);
            document.getElementById("shareWAbtnOp")?.addEventListener("click", () => shareViaWhatsApp(true));
        } else {
            document.getElementById("exportDataBtn")?.addEventListener("click", exportAllData);
            document.getElementById("shareWAbtn")?.addEventListener("click", () => shareViaWhatsApp(false));
            document.getElementById("importDataBtn")?.addEventListener("click", () => document.getElementById("importModal").style.display = "flex");
            document.getElementById("scanQRBtn")?.addEventListener("click", async () => {
                const hasPermission = await requestCameraPermission();
                if (!hasPermission) {
                    document.getElementById("cameraPermissionModal").style.display = "flex";
                } else {
                    document.getElementById("scanQRModal").style.display = "flex";
                    startScanner();
                }
            });
        }
    }
    
    function exportToExcelFiltered() { 
        const activities = getActivitiesByDate(currentViewDate);
        let filtered = activities.filter(a => a.division === currentDivision);
        if (currentWorker) filtered = filtered.filter(a => a.workerName === currentWorker);
        const exportData = filtered.map(act => ({"Tanggal": toDisplayDate(currentViewDate), "Bagian": act.division, "Pekerja": act.workerName, "Aktivitas": act.activity, "Jam Mulai": act.startTime, "Jam Selesai": act.endTime, "Durasi": act.duration, "Keterangan": act.description || "-"}));
        const ws = XLSX.utils.json_to_sheet(exportData); 
        const wb = XLSX.utils.book_new(); 
        XLSX.utils.book_append_sheet(wb, ws, "Daily Activity"); 
        XLSX.writeFile(wb, `Daily_Activity_${currentViewDate}.xlsx`); 
    }
    
    function changeDate(delta) {
        if (!currentViewDate) currentViewDate = formatDateToStorage(new Date());
        const newDate = new Date(currentViewDate);
        newDate.setDate(newDate.getDate() + delta);
        currentViewDate = formatDateToStorage(newDate);
        renderCurrentMenu();
        if (userMode === 'operator') {
            setTimeout(() => refreshQRCode(), 200);
        }
    }
    function goToToday() { 
        currentViewDate = formatDateToStorage(new Date()); 
        renderCurrentMenu();
        if (userMode === 'operator') {
            setTimeout(() => refreshQRCode(), 200);
        }
    }
    
    // ========== MODAL FUNCTIONS ==========
    function updateWorkerSelectByDivision(division) { 
        const workerSelect = document.getElementById("actWorkerName"); 
        if (workerSelect) { 
            const workersList = getOperatorsByDivision(division); 
            workerSelect.innerHTML = '<option value="">-- Pilih Pekerja --</option>' + workersList.map(w => `<option value="${w.name}">${w.name}</option>`).join(''); 
        } 
    }
    
    function openActivityModal(id = null, division = null, workerName = null, dateDisplay = "", activity = "", start = "", end = "", desc = "") { 
        const modal = document.getElementById("activityModal"); 
        populateDivisionSelect();
        if (id) { 
            document.getElementById("modalTitle").innerText = "Edit Aktivitas"; 
            document.getElementById("activityId").value = id; 
            document.getElementById("actDivision").value = division; 
            updateWorkerSelectByDivision(division); 
            setTimeout(() => { document.getElementById("actWorkerName").value = workerName; }, 50); 
            document.getElementById("actDate").value = dateDisplay; 
            document.getElementById("actActivity").value = activity; 
            document.getElementById("actStart").value = start; 
            document.getElementById("actEnd").value = end; 
            document.getElementById("actDesc").value = desc; 
        } else { 
            document.getElementById("modalTitle").innerText = "Tambah Aktivitas"; 
            document.getElementById("activityId").value = ""; 
            document.getElementById("actDivision").value = currentDivision; 
            updateWorkerSelectByDivision(currentDivision); 
            document.getElementById("actDate").value = toDisplayDate(currentViewDate); 
            document.getElementById("actActivity").value = ""; 
            document.getElementById("actStart").value = "08:00"; 
            document.getElementById("actEnd").value = "12:00"; 
            document.getElementById("actDesc").value = ""; 
        } 
        modal.style.display = "flex"; 
    }
    
    function openActivityModalOperator(id = null, division = null, workerName = null, dateDisplay = "", activity = "", start = "", end = "", desc = "") { 
        const modal = document.getElementById("activityModal"); 
        populateDivisionSelect();
        if (id) { 
            document.getElementById("modalTitle").innerText = "Edit Aktivitas"; 
            document.getElementById("activityId").value = id; 
            document.getElementById("actDivision").value = division; 
            document.getElementById("actDivision").disabled = true; 
            updateWorkerSelectByDivision(division); 
            setTimeout(() => { document.getElementById("actWorkerName").value = workerName; document.getElementById("actWorkerName").disabled = true; }, 50); 
            document.getElementById("actDate").value = dateDisplay; 
            document.getElementById("actActivity").value = activity; 
            document.getElementById("actStart").value = start; 
            document.getElementById("actEnd").value = end; 
            document.getElementById("actDesc").value = desc; 
        } else { 
            document.getElementById("modalTitle").innerText = "Tambah Aktivitas"; 
            document.getElementById("activityId").value = ""; 
            document.getElementById("actDivision").value = operatorDivision; 
            document.getElementById("actDivision").disabled = true; 
            updateWorkerSelectByDivision(operatorDivision); 
            setTimeout(() => { document.getElementById("actWorkerName").value = operatorWorkerName; document.getElementById("actWorkerName").disabled = true; }, 50); 
            document.getElementById("actDate").value = toDisplayDate(currentViewDate); 
            document.getElementById("actActivity").value = ""; 
            document.getElementById("actStart").value = "08:00"; 
            document.getElementById("actEnd").value = "12:00"; 
            document.getElementById("actDesc").value = ""; 
        } 
        modal.style.display = "flex"; 
    }
    
    function populateDivisionSelect() {
        const select = document.getElementById("actDivision");
        if (select && select.options.length <= 1) {
            select.innerHTML = '<option value="">-- Pilih Bagian --</option>' + allDivisions.map(d => `<option value="${d}">${d}</option>`).join('');
        }
    }
    
    function closeActivityModal() { 
        document.getElementById("activityModal").style.display = "none"; 
        document.getElementById("actDivision").disabled = false; 
        document.getElementById("actWorkerName").disabled = false; 
    }
    
    function handleActivitySubmit(e) { 
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
        if (!division || !workerName || !dateDisplay || !activity || !startTime || !endTime) { alert("Harap lengkapi semua data"); return; } 
        const storageDate = toStorageDate(dateDisplay);
        const duration = calculateDuration(startTime, endTime);
        const newActivity = { division, workerName, activity, startTime, endTime, duration, description: description || "" };
        if (id) { updateActivityOnDate(storageDate, parseInt(id), newActivity); } 
        else { addActivityToDate(storageDate, newActivity); } 
        if (storageDate === currentViewDate) renderCurrentMenu(); 
        if (userMode === 'operator' && storageDate === formatDateToStorage(new Date())) {
            setTimeout(() => refreshQRCode(), 300);
        }
        closeActivityModal(); 
    }
    
    function openOperatorFormModal(id = null, division = "", name = "") { 
        const modal = document.getElementById("operatorFormModal"); 
        document.getElementById("operatorFormTitle").innerText = id ? "Edit Operator" : "Tambah Operator"; 
        document.getElementById("opDivision").value = division; 
        document.getElementById("opName").value = name; 
        modal.dataset.editId = id || ""; 
        modal.style.display = "flex"; 
    }
    
    function closeOperatorFormModal() { 
        document.getElementById("operatorFormModal").style.display = "none"; 
        document.getElementById("opDivision").value = ""; 
        document.getElementById("opName").value = ""; 
    }
    
    function openOperatorModal() { 
        const modal = document.getElementById("operatorModal"); 
        const divisionSelect = document.getElementById("operatorDivision"); 
        divisionSelect.innerHTML = '<option value="">-- Pilih Bagian --</option>' + operatorDivisions.map(d => `<option value="${d}">${d}</option>`).join(''); 
        document.getElementById("operatorName").innerHTML = '<option value="">-- Pilih Nama --</option>'; 
        modal.style.display = "flex"; 
    }
    
    function toggleSidebar() { 
        const s = document.getElementById('sidebar'), o = document.getElementById('sidebarOverlay'); 
        s.classList.toggle('hidden'); 
        o.classList.toggle('active', !s.classList.contains('hidden')); 
    }
    function closeSidebarOnMobile() { 
        if (window.innerWidth <= 768) { 
            document.getElementById('sidebar').classList.add('hidden'); 
            document.getElementById('sidebarOverlay').classList.remove('active'); 
        } 
    }
    
    function startApp(selectedMode) { 
        document.getElementById('modeSelectionOverlay').style.display = 'none'; 
        document.getElementById('appWrapper').style.display = 'flex'; 
        loadData(); 
        if (selectedMode === 'admin') showPasswordModal(); 
        else openOperatorModal(); 
    }
    
    // ========== EVENT LISTENERS ==========
    function init() { 
        if (checkSavedSession()) { loadData(); renderCurrentMenu(); }
        
        document.getElementById('selectAdminMode').addEventListener('click', () => startApp('admin')); 
        document.getElementById('selectOperatorMode').addEventListener('click', () => startApp('operator')); 
        document.getElementById('toggleSidebarBtn').addEventListener('click', toggleSidebar); 
        document.getElementById('sidebarOverlay').addEventListener('click', closeSidebarOnMobile); 
        document.getElementById('logoutBtn').addEventListener('click', logout);
        document.getElementById('operatorLogoutBtn').addEventListener('click', logout);
        
        document.getElementById("closeImportModal").addEventListener("click", () => document.getElementById("importModal").style.display = "none"); 
        document.getElementById("confirmImportBtn").addEventListener("click", () => { const file = document.getElementById("importFile").files[0]; if (file) importDataFromFile(file); else alert("Pilih file terlebih dahulu!"); document.getElementById("importModal").style.display = "none"; }); 
        
        // Camera permission modal
        document.getElementById("cancelCameraBtn").addEventListener("click", () => document.getElementById("cameraPermissionModal").style.display = "none");
        document.getElementById("allowCameraBtn").addEventListener("click", async () => {
            document.getElementById("cameraPermissionModal").style.display = "none";
            const granted = await requestCameraPermission();
            if (granted) {
                document.getElementById("scanQRModal").style.display = "flex";
                startScanner();
            } else {
                alert("Akses kamera diperlukan untuk scan QR Code. Silakan izinkan akses kamera di pengaturan browser.");
            }
        });
        
        // Scan modal controls
        document.getElementById("closeScanModalBtn").addEventListener("click", async () => {
            await stopScanner();
            document.getElementById("scanQRModal").style.display = "none";
        });
        document.getElementById("stopScanBtn").addEventListener("click", async () => {
            await stopScanner();
            document.getElementById("scanQRModal").style.display = "none";
        });
        
        document.getElementById("activityForm").addEventListener("submit", handleActivitySubmit); 
        document.getElementById("closeModalBtn").addEventListener("click", closeActivityModal); 
        document.getElementById("confirmPasswordBtn").addEventListener("click", verifyPasswordAndSwitch); 
        document.getElementById("cancelPasswordBtn").addEventListener("click", () => { document.getElementById("passwordModal").style.display = "none"; logout(); }); 
        document.getElementById("closeOperatorModal").addEventListener("click", () => { document.getElementById("operatorModal").style.display = "none"; logout(); }); 
        document.getElementById("saveOperatorBtn").addEventListener("click", () => { 
            const division = document.getElementById("opDivision").value; 
            const name = document.getElementById("opName").value.trim(); 
            if (!division || !name) { alert("Pilih bagian dan isi nama operator!"); return; } 
            const editId = document.getElementById("operatorFormModal").dataset.editId; 
            if (editId) { updateOperator(parseInt(editId), division, name); } 
            else { addOperator(division, name); } 
            closeOperatorFormModal(); 
            if (userMode === 'admin' && currentMenu === 'operators') renderOperatorsList(); 
        });
        document.getElementById("closeOperatorFormBtn").addEventListener("click", closeOperatorFormModal);
        document.getElementById("operatorDivision").addEventListener("change", (e) => { 
            const div = e.target.value; 
            const nameSelect = document.getElementById("operatorName"); 
            const workers = getOperatorsByDivision(div); 
            nameSelect.innerHTML = '<option value="">-- Pilih Nama --</option>' + workers.map(w => `<option value="${w.name}">${w.name}</option>`).join(''); 
        });
        document.getElementById("confirmOperatorBtn").addEventListener("click", () => { 
            const division = document.getElementById("operatorDivision").value; 
            const name = document.getElementById("operatorName").value; 
            if (!division || !name) { alert("Pilih bagian dan nama anda!"); return; } 
            operatorDivision = division; 
            operatorWorkerName = name; 
            userMode = 'operator'; 
            localStorage.setItem('userMode', 'operator'); 
            localStorage.setItem('operatorDivision', operatorDivision); 
            localStorage.setItem('operatorWorkerName', operatorWorkerName); 
            document.getElementById("operatorModal").style.display = "none"; 
            currentViewDate = formatDateToStorage(new Date()); 
            document.getElementById('appWrapper').classList.add('operator-mode');
            document.getElementById('adminTopBar').style.display = 'none';
            document.getElementById('operatorTopBar').style.display = 'flex';
            document.getElementById('operatorUserInfo').innerHTML = `👤 ${operatorWorkerName} | ${operatorDivision}`;
            renderCurrentMenu(); 
        });
        
        window.addEventListener("click", async (e) => { 
            if (e.target === document.getElementById("activityModal")) closeActivityModal(); 
            if (e.target === document.getElementById("importModal")) document.getElementById("importModal").style.display = "none"; 
            if (e.target === document.getElementById("scanQRModal")) { await stopScanner(); document.getElementById("scanQRModal").style.display = "none"; }
            if (e.target === document.getElementById("cameraPermissionModal")) document.getElementById("cameraPermissionModal").style.display = "none";
            if (e.target === document.getElementById("operatorFormModal")) closeOperatorFormModal(); 
            if (e.target === document.getElementById("passwordModal")) { document.getElementById("passwordModal").style.display = "none"; logout(); } 
        }); 
        
        document.querySelectorAll(".nav-item").forEach(item => { 
            item.addEventListener("click", () => { 
                const menu = item.dataset.menu; 
                if (menu === "daily-activity") { currentMenu = menu; renderCurrentMenu(); } 
                else if (menu === "operators" && userMode === 'admin') { currentMenu = menu; renderCurrentMenu(); } 
                document.querySelectorAll(".nav-item").forEach(n => n.classList.remove("active")); 
                item.classList.add("active"); 
                if (window.innerWidth <= 768) closeSidebarOnMobile(); 
            }); 
        }); 
        document.querySelector(".nav-item[data-menu='daily-activity']")?.classList.add("active"); 
        
        setInterval(() => {
            if (userMode === 'operator') {
                refreshQRCode();
            }
        }, 60000);
    }
    document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
