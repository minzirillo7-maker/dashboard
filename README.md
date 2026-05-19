<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>VapeDash — Panel Administrativo</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500&family=Libre+Baskerville:wght@400;700&family=Source+Sans+3:wght@400;500;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@2.44.0/tabler-icons.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --white:    #ffffff;
  --bg:       #f4f5f7;
  --bg2:      #eceef2;
  --panel:    #ffffff;
  --border:   #d0d5dd;
  --border2:  #b0b8c8;
  --text:     #1a1d23;
  --text2:    #4a5060;
  --text3:    #8a90a0;
  --navy:     #1e3a5f;
  --navyL:    #2a4f7f;
  --blue:     #2563eb;
  --blueL:    #dbeafe;
  --green:    #166534;
  --greenL:   #dcfce7;
  --greenM:   #16a34a;
  --amber:    #92400e;
  --amberL:   #fef3c7;
  --amberM:   #d97706;
  --red:      #991b1b;
  --redL:     #fee2e2;
  --redM:     #dc2626;
  --sans:     'Source Sans 3', sans-serif;
  --serif:    'Libre Baskerville', serif;
  --mono:     'IBM Plex Mono', monospace;
  --radius:   6px;
  --shadow:   0 1px 3px rgba(0,0,0,.08), 0 1px 2px rgba(0,0,0,.05);
  --shadow2:  0 4px 12px rgba(0,0,0,.10);
}

html, body { min-height: 100vh; background: var(--bg); color: var(--text); font-family: var(--sans); font-size: 14px; line-height: 1.5; }

/* ── LAYOUT ── */
.app { display: flex; min-height: 100vh; }
.sidebar { width: 220px; background: var(--navy); display: flex; flex-direction: column; flex-shrink: 0; position: sticky; top: 0; height: 100vh; overflow-y: auto; }
.main    { flex: 1; min-width: 0; display: flex; flex-direction: column; }

/* ── SIDEBAR ── */
.sb-header { padding: 20px 18px 16px; border-bottom: 1px solid rgba(255,255,255,.1); }
.sb-logo   { display: flex; align-items: center; gap: 10px; }
.sb-logo-icon { width: 36px; height: 36px; background: #fff; border-radius: 6px; display: flex; align-items: center; justify-content: center; }
.sb-logo-icon i { font-size: 20px; color: var(--navy); }
.sb-logo-name { font-family: var(--serif); font-size: 17px; color: #fff; font-weight: 700; letter-spacing: -.2px; }
.sb-logo-sub  { font-size: 10px; color: rgba(255,255,255,.45); font-family: var(--mono); margin-top: 2px; }

.sb-nav { padding: 12px 0; flex: 1; }
.sb-group-label { font-size: 10px; color: rgba(255,255,255,.35); font-family: var(--mono); letter-spacing: .8px; text-transform: uppercase; padding: 10px 18px 4px; }
.sb-item { display: flex; align-items: center; gap: 10px; padding: 9px 18px; font-size: 13px; color: rgba(255,255,255,.65); cursor: pointer; transition: all .15s; border-left: 3px solid transparent; }
.sb-item:hover { color: #fff; background: rgba(255,255,255,.07); }
.sb-item.active { color: #fff; background: rgba(255,255,255,.12); border-left-color: #fff; }
.sb-item i { font-size: 16px; }

.sb-footer { padding: 14px 18px; border-top: 1px solid rgba(255,255,255,.1); }
.sync-row  { display: flex; align-items: center; gap: 7px; font-size: 11px; color: rgba(255,255,255,.55); font-family: var(--mono); }
.sync-dot  { width: 7px; height: 7px; border-radius: 50%; background: #4ade80; flex-shrink: 0; animation: pulse 2.5s ease-in-out infinite; }
@keyframes pulse { 0%,100%{opacity:1} 50%{opacity:.4} }

/* ── TOPBAR ── */
.topbar { background: var(--panel); border-bottom: 1px solid var(--border); padding: 12px 24px; display: flex; align-items: center; justify-content: space-between; gap: 12px; flex-wrap: wrap; }
.topbar-left h1 { font-family: var(--serif); font-size: 18px; font-weight: 700; color: var(--navy); }
.topbar-left p  { font-size: 12px; color: var(--text3); margin-top: 1px; }
.topbar-right { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }

/* ── BUTTONS ── */
.btn { display: inline-flex; align-items: center; gap: 6px; padding: 7px 14px; border-radius: var(--radius); font-size: 13px; font-weight: 600; cursor: pointer; border: 1px solid var(--border); background: var(--panel); color: var(--text2); font-family: var(--sans); transition: all .15s; text-decoration: none; white-space: nowrap; }
.btn:hover { border-color: var(--border2); color: var(--text); background: var(--bg); }
.btn.primary { background: var(--navy); color: #fff; border-color: var(--navy); }
.btn.primary:hover { background: var(--navyL); }
.btn i { font-size: 15px; }

/* ── SEARCH ── */
.search-wrap { display: flex; align-items: center; gap: 6px; background: var(--bg); border: 1px solid var(--border); border-radius: var(--radius); padding: 6px 12px; }
.search-wrap input { background: none; border: none; outline: none; color: var(--text); font-size: 13px; font-family: var(--sans); width: 200px; }
.search-wrap input::placeholder { color: var(--text3); }
.search-wrap i { color: var(--text3); font-size: 15px; }
select.btn { cursor: pointer; padding-right: 8px; }

/* ── CONTENT ── */
.content { padding: 22px 24px; flex: 1; }

/* ── ALERTS ── */
.alerts-zone { margin-bottom: 18px; }
.alert { display: flex; align-items: flex-start; gap: 10px; border-radius: var(--radius); padding: 10px 14px; margin-bottom: 8px; font-size: 13px; line-height: 1.5; border-left: 4px solid; }
.alert.red   { background: var(--redL);   border-color: var(--redM);   color: var(--red); }
.alert.amber { background: var(--amberL); border-color: var(--amberM); color: var(--amber); }
.alert i { font-size: 16px; flex-shrink: 0; margin-top: 1px; }

/* ── SECTION HEADER ── */
.section-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 12px; }
.section-title { font-family: var(--serif); font-size: 15px; font-weight: 700; color: var(--navy); }
.section-sub   { font-size: 12px; color: var(--text3); font-family: var(--mono); }

/* ── KPI CARDS ── */
.kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(155px, 1fr)); gap: 12px; margin-bottom: 22px; }
.kpi { background: var(--panel); border: 1px solid var(--border); border-radius: var(--radius); padding: 16px 18px; box-shadow: var(--shadow); position: relative; overflow: hidden; }
.kpi::before { content:''; position: absolute; top: 0; left: 0; right: 0; height: 3px; }
.kpi.navy::before  { background: var(--navy); }
.kpi.blue::before  { background: var(--blue); }
.kpi.green::before { background: var(--greenM); }
.kpi.amber::before { background: var(--amberM); }
.kpi.red::before   { background: var(--redM); }
.kpi-icon { width: 32px; height: 32px; border-radius: 6px; display: flex; align-items: center; justify-content: center; margin-bottom: 10px; font-size: 16px; }
.kpi.navy  .kpi-icon { background: #e8edf5; color: var(--navy); }
.kpi.blue  .kpi-icon { background: var(--blueL); color: var(--blue); }
.kpi.green .kpi-icon { background: var(--greenL); color: var(--greenM); }
.kpi.amber .kpi-icon { background: var(--amberL); color: var(--amberM); }
.kpi.red   .kpi-icon { background: var(--redL); color: var(--redM); }
.kpi-label { font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: .5px; font-family: var(--mono); margin-bottom: 4px; }
.kpi-val   { font-size: 22px; font-weight: 700; color: var(--text); line-height: 1; margin-bottom: 3px; font-family: var(--serif); }
.kpi-hint  { font-size: 11px; color: var(--text3); }

/* ── CHARTS ROW ── */
.charts-row { display: grid; grid-template-columns: 1.5fr 1fr; gap: 16px; margin-bottom: 22px; }
.chart-panel { background: var(--panel); border: 1px solid var(--border); border-radius: var(--radius); padding: 18px 20px; box-shadow: var(--shadow); }
.chart-panel-title { font-family: var(--serif); font-size: 14px; font-weight: 700; color: var(--navy); margin-bottom: 2px; }
.chart-panel-sub   { font-size: 11px; color: var(--text3); font-family: var(--mono); margin-bottom: 14px; }
.chart-legend { display: flex; flex-wrap: wrap; gap: 12px; margin-bottom: 12px; }
.leg { display: flex; align-items: center; gap: 5px; font-size: 11px; color: var(--text2); font-family: var(--mono); }
.leg-sq { width: 10px; height: 10px; border-radius: 2px; flex-shrink: 0; }

/* ── STAT TABLE (inside charts) ── */
.stat-table { width: 100%; border-collapse: collapse; font-size: 12px; }
.stat-table th { font-size: 10px; color: var(--text3); font-family: var(--mono); text-transform: uppercase; letter-spacing: .4px; text-align: left; padding: 5px 8px; border-bottom: 1px solid var(--border); }
.stat-table td { padding: 7px 8px; border-bottom: 1px solid var(--bg2); color: var(--text); }
.stat-table tr:last-child td { border-bottom: none; }
.stat-table tr:hover td { background: var(--bg); }

/* ── MAIN TABLE ── */
.table-panel { background: var(--panel); border: 1px solid var(--border); border-radius: var(--radius); overflow: hidden; box-shadow: var(--shadow); margin-bottom: 22px; }
.table-toolbar { display: flex; align-items: center; gap: 8px; padding: 12px 16px; border-bottom: 1px solid var(--border); background: var(--bg); flex-wrap: wrap; }
.filt { font-size: 12px; padding: 4px 12px; border-radius: 20px; cursor: pointer; border: 1px solid var(--border); background: var(--panel); color: var(--text2); font-family: var(--sans); transition: all .15s; white-space: nowrap; }
.filt:hover { border-color: var(--border2); color: var(--text); }
.filt.on { background: var(--navy); color: #fff; border-color: var(--navy); }
.tbl-wrap { overflow-x: auto; }
table.main-table { width: 100%; border-collapse: collapse; min-width: 800px; }
.main-table thead th { font-size: 11px; font-family: var(--mono); color: var(--text3); text-transform: uppercase; letter-spacing: .4px; text-align: left; padding: 10px 14px; border-bottom: 2px solid var(--border); background: var(--bg); white-space: nowrap; cursor: pointer; user-select: none; }
.main-table thead th:hover { color: var(--navy); }
.main-table thead th.sort-asc::after  { content: ' ↑'; color: var(--navy); }
.main-table thead th.sort-desc::after { content: ' ↓'; color: var(--navy); }
.main-table tbody tr { border-bottom: 1px solid var(--bg2); transition: background .1s; cursor: default; }
.main-table tbody tr:hover { background: #f8f9fc; }
.main-table tbody tr:last-child { border-bottom: none; }
.main-table td { padding: 10px 14px; font-size: 13px; vertical-align: middle; }
.prod-name   { font-weight: 600; color: var(--text); }
.prod-detail { font-size: 11px; color: var(--text3); font-family: var(--mono); margin-top: 2px; }
.badge { display: inline-flex; align-items: center; font-size: 11px; font-weight: 600; padding: 2px 8px; border-radius: 20px; white-space: nowrap; }
.b-green { background: var(--greenL); color: var(--green); }
.b-amber { background: var(--amberL); color: var(--amber); }
.b-red   { background: var(--redL);   color: var(--red); }
.b-navy  { background: #e8edf5; color: var(--navy); }
.b-blue  { background: var(--blueL); color: #1d4ed8; }
.mono { font-family: var(--mono); font-size: 12px; }
.bar-row { display: flex; align-items: center; gap: 8px; }
.bar-bg  { flex: 1; height: 5px; background: var(--bg2); border-radius: 3px; min-width: 50px; }
.bar-fill { height: 100%; border-radius: 3px; transition: width .3s; }
.img-thumb { width: 40px; height: 40px; object-fit: cover; border-radius: var(--radius); border: 1px solid var(--border); display: block; background: var(--bg); }
.img-ph    { width: 40px; height: 40px; border-radius: var(--radius); border: 1px solid var(--border); background: var(--bg2); display: flex; align-items: center; justify-content: center; color: var(--text3); }
.mp-link   { display: inline-flex; align-items: center; gap: 4px; font-size: 11px; font-weight: 600; color: #0a66c2; text-decoration: none; padding: 2px 7px; border: 1px solid #b0cce0; border-radius: 4px; background: #f0f7ff; }
.mp-link:hover { background: #dbeafe; }

/* ── BOTTOM PANELS ── */
.bottom-row { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 16px; margin-bottom: 24px; }
.rank-panel { background: var(--panel); border: 1px solid var(--border); border-radius: var(--radius); overflow: hidden; box-shadow: var(--shadow); }
.rank-header { padding: 12px 16px; border-bottom: 1px solid var(--border); background: var(--bg); display: flex; align-items: center; gap: 8px; }
.rank-header-title { font-family: var(--serif); font-size: 13px; font-weight: 700; color: var(--navy); }
.rank-body { padding: 0; }
.rank-row { display: flex; align-items: center; gap: 10px; padding: 9px 14px; border-bottom: 1px solid var(--bg2); }
.rank-row:last-child { border-bottom: none; }
.rank-num { font-size: 12px; font-family: var(--mono); color: var(--text3); width: 18px; flex-shrink: 0; font-weight: 500; }
.rank-info { flex: 1; min-width: 0; }
.rank-name { font-size: 12px; font-weight: 600; color: var(--text); overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.rank-sub  { font-size: 10px; color: var(--text3); font-family: var(--mono); margin-top: 1px; }
.rank-val  { font-size: 12px; font-family: var(--mono); font-weight: 500; flex-shrink: 0; }

/* ── LOADING / ERROR ── */
.loading-wrap { display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 14px; padding: 80px 20px; }
.spinner { width: 34px; height: 34px; border: 3px solid var(--bg2); border-top-color: var(--navy); border-radius: 50%; animation: spin .8s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
.error-box { background: var(--redL); border: 1px solid #fca5a5; border-left: 4px solid var(--redM); border-radius: var(--radius); padding: 18px 22px; margin: 24px; }
.error-box strong { color: var(--red); font-size: 14px; display: flex; align-items: center; gap: 7px; }
.error-box p { font-size: 13px; color: var(--red); margin-top: 8px; line-height: 1.6; }

/* ── DIVIDER ── */
.divider { height: 1px; background: var(--border); margin: 20px 0; }

/* ── RESPONSIVE ── */
@media (max-width: 960px) {
  .sidebar { display: none; }
  .charts-row { grid-template-columns: 1fr; }
  .bottom-row { grid-template-columns: 1fr; }
  .kpi-grid { grid-template-columns: repeat(2, 1fr); }
  .topbar { padding: 10px 14px; }
  .content { padding: 16px; }
}
@media (max-width: 500px) {
  .kpi-grid { grid-template-columns: 1fr; }
  .search-wrap input { width: 140px; }
}
</style>
</head>
<body>
<div class="app">

  <!-- ══ SIDEBAR ══ -->
  <aside class="sidebar">
    <div class="sb-header">
      <div class="sb-logo">
        <div class="sb-logo-icon"><i class="ti ti-building-store"></i></div>
        <div>
          <div class="sb-logo-name">VapeDash</div>
          <div class="sb-logo-sub">PANEL ADMINISTRATIVO</div>
        </div>
      </div>
    </div>
    <nav class="sb-nav">
      <div class="sb-group-label">Panel</div>
      <div class="sb-item active"><i class="ti ti-layout-dashboard"></i> Resumen general</div>
      <div class="sb-item"><i class="ti ti-package"></i> Inventario</div>
      <div class="sb-item"><i class="ti ti-chart-line"></i> Estadísticas</div>
      <div class="sb-group-label">Herramientas</div>
      <div class="sb-item" onclick="loadData()"><i class="ti ti-refresh"></i> Actualizar datos</div>
      <div class="sb-item" onclick="exportCSV()"><i class="ti ti-file-download"></i> Exportar CSV</div>
      <div class="sb-item" onclick="window.open('https://docs.google.com/spreadsheets','_blank')"><i class="ti ti-table"></i> Abrir Google Sheets</div>
    </nav>
    <div class="sb-footer">
      <div class="sync-row"><span class="sync-dot"></span><span id="syncText">Conectando...</span></div>
      <div style="font-size:10px;color:rgba(255,255,255,.3);font-family:var(--mono);margin-top:4px" id="syncTime"></div>
    </div>
  </aside>

  <!-- ══ MAIN ══ -->
  <div class="main">

    <!-- TOPBAR -->
    <div class="topbar">
      <div class="topbar-left">
        <h1>Panel de Control</h1>
        <p id="topbarSub">Cargando datos desde Google Sheets...</p>
      </div>
      <div class="topbar-right">
        <div class="search-wrap">
          <i class="ti ti-search"></i>
          <input type="text" id="searchInput" placeholder="Buscar producto o sabor..." oninput="doSearch(this.value)">
        </div>
        <select class="btn" id="catFilter" onchange="setCat(this.value)">
          <option value="">Todas las categorías</option>
        </select>
        <button class="btn primary" onclick="loadData()"><i class="ti ti-refresh"></i> Actualizar</button>
        <button class="btn" onclick="exportCSV()"><i class="ti ti-download"></i> Exportar CSV</button>
      </div>
    </div>

    <!-- CONTENT -->
    <div class="content">
      <div id="appContent">
        <div class="loading-wrap">
          <div class="spinner"></div>
          <div style="font-size:14px;color:var(--text2);font-weight:600">Cargando datos...</div>
          <div style="font-size:12px;color:var(--text3);font-family:var(--mono);text-align:center;max-width:300px">Conectando a tu Google Sheets en tiempo real</div>
        </div>
      </div>
    </div>

  </div><!-- /main -->
</div><!-- /app -->

<script>
const SHEET_URL = 'https://script.google.com/macros/s/AKfycbwiJQnEG8DaPdTtXeP6vKKKB813pqGNul6LLXQnf6_-D1j4ESTPfcCm4p4Xf87fASn7aA/exec';

let ALL = [], filtered = [];
let sortKey = 'margin', sortDir = -1;
let activeFilter = 'all', searchQ = '', catQ = '';
let chartBar = null, chartDona = null;

function parseMoney(v) {
  if (typeof v === 'number') return v;
  if (!v) return 0;
  return parseFloat(String(v).replace(/[$\s]/g,'').replace(/\./g,'').replace(',','.')) || 0;
}

function calc(p) {
  const costo   = parseMoney(p['COSTO']);
  const precio  = parseMoney(p['PRECIO VENTA']);
  const stock   = parseInt(p['STOCK ACTUAL']) || 0;
  const margin  = precio > 0 ? ((precio - costo) / precio * 100) : 0;
  const netGain = precio - costo;
  const invVal  = stock * costo;
  const potential = stock * netGain;
  const roi     = costo > 0 ? ((precio - costo) / costo * 100) : 0;
  return { ...p, _costo: costo, _precio: precio, _stock: stock, margin, netGain, invVal, potential, roi };
}

function stockStatus(p) {
  const st = String(p['ESTADO'] || '').toUpperCase();
  if (st.includes('SIN') || p._stock === 0) return 'sinstock';
  if (st.includes('BAJO') || p._stock <= 3) return 'bajo';
  return 'ok';
}

const fmtPesos = n => '$' + Math.round(n).toLocaleString('es-AR');
const fmtPct   = n => Math.round(n) + '%';
const fmtK     = n => n >= 1000 ? '$' + (n/1000).toFixed(1) + 'k' : fmtPesos(n);

/* ── LOAD ── */
async function loadData() {
  setSyncState('loading');
  try {
    const res = await fetch(SHEET_URL + '?t=' + Date.now());
    if (!res.ok) throw new Error('HTTP ' + res.status);
    const json = await res.json();
    let rows = [];
    if (Array.isArray(json)) rows = json;
    else if (json.productos) rows = json.productos;
    else if (json.data)      rows = json.data;
    else { const v = Object.values(json).find(x => Array.isArray(x)); if(v) rows = v; }
    rows = rows.filter(r => r['PRODUCTO'] && String(r['PRODUCTO']).trim() !== '');
    if (!rows.length) throw new Error('No se encontraron filas con datos en la hoja "STOCK".');
    ALL = rows.map(calc);
    filtered = [...ALL];
    buildCatFilter();
    renderPage();
    setSyncState('ok');
    const n = new Date();
    document.getElementById('topbarSub').textContent =
      ALL.length + ' productos · última actualización ' + n.toLocaleTimeString('es-AR',{hour:'2-digit',minute:'2-digit'});
  } catch(e) {
    setSyncState('error');
    document.getElementById('appContent').innerHTML = `
      <div class="error-box">
        <strong><i class="ti ti-alert-circle"></i> No se pudo conectar a Google Sheets</strong>
        <p>${e.message}</p>
        <p style="margin-top:8px">Verificá que el Apps Script esté publicado con acceso "Cualquier usuario (anónimo)".</p>
        <button class="btn" style="margin-top:12px" onclick="loadData()"><i class="ti ti-refresh"></i> Reintentar</button>
      </div>`;
  }
}

function setSyncState(state) {
  const txt = document.getElementById('syncText');
  const dot = document.querySelector('.sync-dot');
  if (!txt) return;
  if (state === 'loading') { txt.textContent = 'Actualizando...'; dot.style.background = '#fbbf24'; }
  else if (state === 'ok') {
    txt.textContent = 'Sincronizado';
    dot.style.background = '#4ade80';
    document.getElementById('syncTime').textContent = new Date().toLocaleTimeString('es-AR',{hour:'2-digit',minute:'2-digit'});
  } else { txt.textContent = 'Sin conexión'; dot.style.background = '#f87171'; }
}

function buildCatFilter() {
  const cats = [...new Set(ALL.map(p => p['CATEGORÍA'] || '').filter(Boolean))];
  const sel = document.getElementById('catFilter');
  if (!sel) return;
  const cur = sel.value;
  sel.innerHTML = '<option value="">Todas las categorías</option>' +
    cats.map(c=>`<option value="${c}">${c}</option>`).join('');
  sel.value = cur;
}

/* ── RENDER PAGE SHELL ── */
function renderPage() {
  document.getElementById('appContent').innerHTML = `
    <div id="alertsZone" style="margin-bottom:16px"></div>

    <div class="section-header">
      <span class="section-title">Resumen financiero</span>
      <span class="section-sub" id="lastUpdate"></span>
    </div>
    <div class="kpi-grid" id="kpiGrid"></div>

    <div class="charts-row" id="chartsRow"></div>

    <div class="section-header">
      <span class="section-title">Inventario completo</span>
      <span class="section-sub" id="tableCount"></span>
    </div>
    <div class="table-panel">
      <div class="table-toolbar" id="toolbar"></div>
      <div class="tbl-wrap">
        <table class="main-table">
          <thead id="tHead"></thead>
          <tbody id="tBody"></tbody>
        </table>
      </div>
    </div>

    <div class="section-header">
      <span class="section-title">Rankings y alertas</span>
    </div>
    <div class="bottom-row" id="bottomRow"></div>`;

  renderAlerts();
  renderKPIs();
  renderCharts();
  buildToolbar();
  buildThead();
  applyAndRender();
  renderRankings();
}

/* ── ALERTS ── */
function renderAlerts() {
  const sinSt = ALL.filter(p => stockStatus(p) === 'sinstock');
  const lowSt = ALL.filter(p => stockStatus(p) === 'bajo');
  let h = '';
  if (sinSt.length) h += `<div class="alert red"><i class="ti ti-alert-triangle"></i><div><strong>Sin stock (${sinSt.length} productos):</strong> ${sinSt.map(p=>p['PRODUCTO']+' '+p['SABOR']).join(' · ')} — Reponer urgente.</div></div>`;
  if (lowSt.length) h += `<div class="alert amber"><i class="ti ti-alert-circle"></i><div><strong>Stock bajo (${lowSt.length} productos):</strong> ${lowSt.map(p=>p['PRODUCTO']+' '+p['SABOR']+' ('+p._stock+' ud.)').join(' · ')}</div></div>`;
  document.getElementById('alertsZone').innerHTML = h;
}

/* ── KPIs ── */
function renderKPIs() {
  const totalInv = ALL.reduce((a,p)=>a+p.invVal,0);
  const totalPot = ALL.reduce((a,p)=>a+p.potential,0);
  const avgM     = ALL.length ? ALL.reduce((a,p)=>a+p.margin,0)/ALL.length : 0;
  const sinSt    = ALL.filter(p=>stockStatus(p)==='sinstock').length;
  const lowSt    = ALL.filter(p=>stockStatus(p)==='bajo').length;
  const topM     = [...ALL].sort((a,b)=>b.margin-a.margin)[0];
  const totalUni = ALL.reduce((a,p)=>a+p._stock,0);

  const kpis = [
    {cls:'navy',  icon:'ti-package',       label:'Total productos', val:ALL.length,        hint:'en inventario'},
    {cls:'navy',  icon:'ti-stack-2',        label:'Unidades stock',  val:totalUni,          hint:'disponibles para vender'},
    {cls:'amber', icon:'ti-coins',          label:'Capital invertido',val:fmtPesos(totalInv),hint:'valor al costo'},
    {cls:'green', icon:'ti-trending-up',    label:'Gan. potencial',  val:fmtPesos(totalPot),hint:'si vendés todo el stock'},
    {cls:'blue',  icon:'ti-percentage',     label:'Margen promedio', val:fmtPct(avgM),      hint:'sobre todos los productos'},
    {cls:'red',   icon:'ti-alert-triangle', label:'Sin stock',       val:sinSt,             hint:'productos agotados'},
    {cls:'amber', icon:'ti-alert-circle',   label:'Stock bajo',      val:lowSt,             hint:'≤3 unidades'},
    {cls:'green', icon:'ti-award',          label:'Mejor margen',    val:topM?fmtPct(topM.margin):'-', hint:topM?topM['PRODUCTO']:''},
  ];

  document.getElementById('kpiGrid').innerHTML = kpis.map(k=>`
    <div class="kpi ${k.cls}">
      <div class="kpi-icon"><i class="ti ${k.icon}"></i></div>
      <div class="kpi-label">${k.label}</div>
      <div class="kpi-val">${k.val}</div>
      <div class="kpi-hint">${k.hint}</div>
    </div>`).join('');
}

/* ── CHARTS ── */
function renderCharts() {
  document.getElementById('chartsRow').innerHTML = `
    <div class="chart-panel">
      <div class="chart-panel-title">Ganancia potencial por producto</div>
      <div class="chart-panel-sub">Stock actual × (precio de venta − costo) — top 8 productos</div>
      <div class="chart-legend" id="leg1"></div>
      <div style="position:relative;height:230px"><canvas id="ch1" role="img" aria-label="Ganancia potencial por producto">Gráfico de barras de ganancia potencial</canvas></div>
    </div>
    <div class="chart-panel">
      <div class="chart-panel-title">Estado del inventario</div>
      <div class="chart-panel-sub">Distribución de productos por disponibilidad</div>
      <div class="chart-legend" id="leg2"></div>
      <div style="position:relative;height:160px"><canvas id="ch2" role="img" aria-label="Estado del inventario">Gráfico de dona de estado</canvas></div>
      <div style="margin-top:14px">
        <table class="stat-table" id="statTable"></table>
      </div>
    </div>`;

  const top8 = [...ALL].sort((a,b)=>b.potential-a.potential).slice(0,8);
  const barColors = top8.map(p => {
    const s = stockStatus(p);
    return s==='sinstock'?'#dc2626':s==='bajo'?'#d97706':'#1d4ed8';
  });
  const barBorder = top8.map(p => {
    const s = stockStatus(p);
    return s==='sinstock'?'#991b1b':s==='bajo'?'#92400e':'#1e40af';
  });

  if (chartBar) chartBar.destroy();
  chartBar = new Chart(document.getElementById('ch1'), {
    type: 'bar',
    data: {
      labels: top8.map(p => (p['PRODUCTO']+' '+p['SABOR']).slice(0,22)),
      datasets: [{
        label: 'Ganancia potencial',
        data: top8.map(p => p.potential),
        backgroundColor: barColors,
        borderColor: barBorder,
        borderWidth: 1,
        borderRadius: 3,
        borderSkipped: false
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend:{display:false}, tooltip:{callbacks:{label:v=>fmtPesos(v.raw)}} },
      scales: {
        x: { ticks:{color:'#8a90a0',font:{size:9},maxRotation:30}, grid:{color:'#eceef2'} },
        y: { ticks:{color:'#8a90a0',font:{size:9},callback:v=>fmtK(v)}, grid:{color:'#eceef2'} }
      }
    }
  });
  document.getElementById('leg1').innerHTML = `
    <span class="leg"><span class="leg-sq" style="background:#1d4ed8"></span>Con stock</span>
    <span class="leg"><span class="leg-sq" style="background:#d97706"></span>Stock bajo</span>
    <span class="leg"><span class="leg-sq" style="background:#dc2626"></span>Sin stock</span>`;

  const ok   = ALL.filter(p=>stockStatus(p)==='ok').length;
  const bajo = ALL.filter(p=>stockStatus(p)==='bajo').length;
  const sin  = ALL.filter(p=>stockStatus(p)==='sinstock').length;

  if (chartDona) chartDona.destroy();
  chartDona = new Chart(document.getElementById('ch2'), {
    type: 'doughnut',
    data: {
      labels: ['Con stock','Stock bajo','Sin stock'],
      datasets: [{
        data: [ok, bajo, sin],
        backgroundColor: ['#dbeafe','#fef3c7','#fee2e2'],
        borderColor:     ['#1d4ed8','#d97706','#dc2626'],
        borderWidth: 2
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false, cutout: '65%',
      plugins: { legend:{display:false}, tooltip:{callbacks:{label:v=>v.label+': '+v.raw+' productos'}} }
    }
  });
  document.getElementById('leg2').innerHTML = `
    <span class="leg"><span class="leg-sq" style="background:#1d4ed8"></span>OK (${ok})</span>
    <span class="leg"><span class="leg-sq" style="background:#d97706"></span>Bajo (${bajo})</span>
    <span class="leg"><span class="leg-sq" style="background:#dc2626"></span>Sin stock (${sin})</span>`;

  const totalInv = ALL.reduce((a,p)=>a+p.invVal,0);
  const totalPot = ALL.reduce((a,p)=>a+p.potential,0);
  document.getElementById('statTable').innerHTML = `
    <thead><tr><th>Métrica</th><th style="text-align:right">Valor</th></tr></thead>
    <tbody>
      <tr><td>Capital total invertido</td><td style="text-align:right;font-family:var(--mono)">${fmtPesos(totalInv)}</td></tr>
      <tr><td>Ganancia potencial total</td><td style="text-align:right;font-family:var(--mono);color:var(--greenM)">${fmtPesos(totalPot)}</td></tr>
      <tr><td>Productos sin movimiento</td><td style="text-align:right;font-family:var(--mono);color:var(--red)">${sin}</td></tr>
      <tr><td>Mejor ROI del catálogo</td><td style="text-align:right;font-family:var(--mono)">
        ${ALL.length ? fmtPct(Math.max(...ALL.map(p=>p.roi))) : '-'}
      </td></tr>
    </tbody>`;
}

/* ── TOOLBAR ── */
function buildToolbar() {
  const ok   = ALL.filter(p=>stockStatus(p)==='ok').length;
  const bajo = ALL.filter(p=>stockStatus(p)==='bajo').length;
  const sin  = ALL.filter(p=>stockStatus(p)==='sinstock').length;
  document.getElementById('toolbar').innerHTML = `
    <button class="filt on"  onclick="setFilt(this,'all')">Todos (${ALL.length})</button>
    <button class="filt"     onclick="setFilt(this,'ok')">Con stock (${ok})</button>
    <button class="filt"     onclick="setFilt(this,'bajo')">Stock bajo (${bajo})</button>
    <button class="filt"     onclick="setFilt(this,'sinstock')">Sin stock (${sin})</button>
    <button class="filt"     onclick="setFilt(this,'topmargin')">Más rentables</button>`;
}

/* ── TABLE HEAD ── */
function buildThead() {
  document.getElementById('tHead').innerHTML = `<tr>
    <th style="width:50px"></th>
    <th onclick="doSort('PRODUCTO')"   id="th-PRODUCTO">Producto / Sabor</th>
    <th onclick="doSort('CATEGORÍA')"  id="th-CATEGORÍA">Categoría</th>
    <th onclick="doSort('_costo')"     id="th-_costo">Costo</th>
    <th onclick="doSort('_precio')"    id="th-_precio">Precio venta</th>
    <th onclick="doSort('netGain')"    id="th-netGain">Gan. neta/ud.</th>
    <th onclick="doSort('margin')"     id="th-margin">Margen %</th>
    <th onclick="doSort('roi')"        id="th-roi">ROI %</th>
    <th onclick="doSort('_stock')"     id="th-_stock">Stock</th>
    <th onclick="doSort('potential')"  id="th-potential">G. potencial</th>
    <th onclick="doSort('invVal')"     id="th-invVal">Val. inventario</th>
    <th>Estado</th>
    <th>Pago</th>
  </tr>`;
}

/* ── APPLY FILTERS + RENDER TABLE ── */
function applyAndRender() {
  let d = [...ALL];
  if (searchQ) d = d.filter(p =>
    (p['PRODUCTO']+' '+p['SABOR']+' '+(p['CATEGORÍA']||'')).toLowerCase().includes(searchQ));
  if (catQ) d = d.filter(p => p['CATEGORÍA'] === catQ);
  if      (activeFilter==='ok')       d = d.filter(p=>stockStatus(p)==='ok');
  else if (activeFilter==='bajo')     d = d.filter(p=>stockStatus(p)==='bajo');
  else if (activeFilter==='sinstock') d = d.filter(p=>stockStatus(p)==='sinstock');
  else if (activeFilter==='topmargin') d = d.filter(p=>p.margin>=40);
  d.sort((a,b)=>(a[sortKey]>b[sortKey]?1:-1)*sortDir);
  filtered = d;
  renderTable();
}

function renderTable() {
  const barColor = m => m>=50?'#16a34a':m>=35?'#1d4ed8':m>=20?'#d97706':'#dc2626';
  const statusBadge = p => {
    const s = stockStatus(p);
    if (s==='sinstock') return '<span class="badge b-red">Sin stock</span>';
    if (s==='bajo')     return '<span class="badge b-amber">Stock bajo</span>';
    return '<span class="badge b-green">Disponible</span>';
  };
  const catBadge = c => c ? `<span class="badge b-navy">${c}</span>` : '';

  document.getElementById('tableCount').textContent = filtered.length + ' de ' + ALL.length + ' productos';

  document.getElementById('tBody').innerHTML = filtered.map(p => `
    <tr>
      <td>${p['IMAGEN_URL']
        ? `<img class="img-thumb" src="${p['IMAGEN_URL']}" alt="${p['PRODUCTO']}" loading="lazy" onerror="this.outerHTML='<div class=img-ph><i class=ti ti-package></i></div>'">`
        : '<div class="img-ph"><i class="ti ti-package"></i></div>'}</td>
      <td>
        <div class="prod-name">${p['PRODUCTO']||''}</div>
        <div class="prod-detail">${p['SABOR']||''}</div>
      </td>
      <td>${catBadge(p['CATEGORÍA'])}</td>
      <td class="mono">${fmtPesos(p._costo)}</td>
      <td class="mono" style="font-weight:600">${fmtPesos(p._precio)}</td>
      <td class="mono" style="color:var(--greenM);font-weight:600">${fmtPesos(p.netGain)}</td>
      <td>
        <div class="bar-row">
          <span class="mono" style="min-width:34px;color:${barColor(p.margin)};font-weight:600">${Math.round(p.margin)}%</span>
          <div class="bar-bg"><div class="bar-fill" style="width:${Math.min(Math.max(p.margin,0),100)}%;background:${barColor(p.margin)}"></div></div>
        </div>
      </td>
      <td class="mono">${Math.round(p.roi)}%</td>
      <td class="mono" style="font-weight:600;color:${p._stock===0?'var(--redM)':p._stock<=3?'var(--amberM)':'var(--text)'}">${p._stock}</td>
      <td class="mono" style="color:var(--blue);font-weight:600">${fmtPesos(p.potential)}</td>
      <td class="mono">${fmtPesos(p.invVal)}</td>
      <td>${statusBadge(p)}</td>
      <td>${p['mp_link']
        ? `<a class="mp-link" href="${p['mp_link']}" target="_blank"><i class="ti ti-external-link" style="font-size:11px"></i> MP</a>`
        : '<span style="color:var(--text3)">—</span>'}</td>
    </tr>`).join('') ||
    `<tr><td colspan="13" style="text-align:center;padding:32px;color:var(--text3);font-size:13px">Sin productos para este filtro.</td></tr>`;

  document.querySelectorAll('thead th[id^="th-"]').forEach(th => {
    th.className = '';
    if (th.id.replace('th-','') === sortKey) th.className = sortDir===-1?'sort-desc':'sort-asc';
  });
}

/* ── RANKINGS ── */
function renderRankings() {
  const top5margin = [...ALL].sort((a,b)=>b.margin-a.margin).slice(0,5);
  const top5pot    = [...ALL].sort((a,b)=>b.potential-a.potential).slice(0,5);
  const reponer    = [...ALL].filter(p=>stockStatus(p)!=='ok').sort((a,b)=>a._stock-b._stock).slice(0,6);

  const makeRank = (items, valFn, valColor) => items.map((p,i)=>`
    <div class="rank-row">
      <div class="rank-num">${i+1}</div>
      <div class="rank-info">
        <div class="rank-name">${p['PRODUCTO']} ${p['SABOR']}</div>
        <div class="rank-sub">${p['CATEGORÍA']||'Desechable'} · ROI ${Math.round(p.roi)}%</div>
      </div>
      <div class="rank-val" style="color:${valColor}">${valFn(p)}</div>
    </div>`).join('');

  document.getElementById('bottomRow').innerHTML = `
    <div class="rank-panel">
      <div class="rank-header"><i class="ti ti-star" style="color:var(--amberM)"></i><span class="rank-header-title">Mejores márgenes</span></div>
      <div class="rank-body">${makeRank(top5margin, p=>fmtPct(p.margin), 'var(--greenM)')}</div>
    </div>
    <div class="rank-panel">
      <div class="rank-header"><i class="ti ti-trending-up" style="color:var(--blue)"></i><span class="rank-header-title">Mayor ganancia potencial</span></div>
      <div class="rank-body">${makeRank(top5pot, p=>fmtPesos(p.potential), 'var(--blue)')}</div>
    </div>
    <div class="rank-panel">
      <div class="rank-header"><i class="ti ti-alert-triangle" style="color:var(--redM)"></i><span class="rank-header-title">Reponer urgente</span></div>
      <div class="rank-body">${reponer.length
        ? reponer.map((p,i)=>`
          <div class="rank-row">
            <div class="rank-num">${i+1}</div>
            <div class="rank-info">
              <div class="rank-name">${p['PRODUCTO']} ${p['SABOR']}</div>
              <div class="rank-sub">Pérdida potencial: ${fmtPesos(p.potential)}</div>
            </div>
            <div class="rank-val" style="color:${p._stock===0?'var(--redM)':'var(--amberM)'}">
              ${p._stock===0?'AGOTADO':p._stock+' ud.'}
            </div>
          </div>`).join('')
        : '<div style="padding:20px;text-align:center;font-size:13px;color:var(--text3)">Todo en orden ✓</div>'
      }</div>
    </div>`;
}

/* ── UTILS ── */
function setFilt(el, f) {
  activeFilter = f;
  document.querySelectorAll('.filt').forEach(b=>b.classList.remove('on'));
  el.classList.add('on');
  applyAndRender();
}
function doSearch(v) { searchQ = v.toLowerCase(); applyAndRender(); }
function setCat(v)   { catQ = v; applyAndRender(); }
function doSort(k) {
  if (sortKey===k) sortDir*=-1; else { sortKey=k; sortDir=-1; }
  applyAndRender();
}

function exportCSV() {
  if (!ALL.length) return alert('No hay datos para exportar.');
  const cols = ['PRODUCTO','SABOR','CATEGORÍA','COSTO','PRECIO VENTA','STOCK ACTUAL','ESTADO','margin','netGain','invVal','potential','roi'];
  const headers = ['Producto','Sabor','Categoría','Costo','Precio Venta','Stock','Estado','Margen %','Gan. Neta/ud','Val. Inventario','Gan. Potencial','ROI %'];
  const rows = ALL.map(p => cols.map(c => {
    const v = ['margin','netGain','invVal','potential','roi'].includes(c) ? Math.round(p[c]) : (p[c]||'');
    return '"'+String(v).replace(/"/g,'""')+'"';
  }).join(','));
  const csv = [headers.join(','), ...rows].join('\n');
  const a = document.createElement('a');
  a.href = 'data:text/csv;charset=utf-8,\uFEFF' + encodeURIComponent(csv);
  a.download = 'vapedash_' + new Date().toISOString().slice(0,10) + '.csv';
  a.click();
}

loadData();
setInterval(loadData, 60000);
</script>
</body>
</html>

