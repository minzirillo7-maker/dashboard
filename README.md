<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>VapeDash — Panel de Gestión</title>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@2.44.0/tabler-icons.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500&display=swap');

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --white:    #ffffff;
  --gray-50:  #f7f8fa;
  --gray-100: #eef0f4;
  --gray-200: #dde1e9;
  --gray-300: #c4cad6;
  --gray-400: #8f99ab;
  --gray-500: #5d6a7e;
  --gray-700: #2d3748;
  --gray-900: #111827;
  --blue:     #1a56db;
  --blue-lt:  #e8f0fe;
  --blue-md:  #3b82f6;
  --green:    #0e7c3a;
  --green-lt: #e6f4ec;
  --green-md: #22c55e;
  --amber:    #92530a;
  --amber-lt: #fef3e2;
  --amber-md: #f59e0b;
  --red:      #b91c1c;
  --red-lt:   #fef2f2;
  --red-md:   #ef4444;
  --border:   #dde1e9;
  --font:     'IBM Plex Sans', sans-serif;
  --mono:     'IBM Plex Mono', monospace;
  --shadow-sm: 0 1px 3px rgba(0,0,0,.06), 0 1px 2px rgba(0,0,0,.04);
  --shadow:    0 2px 8px rgba(0,0,0,.08), 0 1px 3px rgba(0,0,0,.05);
}

html, body {
  min-height: 100vh;
  background: var(--gray-50);
  color: var(--gray-900);
  font-family: var(--font);
  font-size: 14px;
  line-height: 1.5;
}

/* ── LAYOUT ─────────────────────────────── */
.layout { display: flex; min-height: 100vh; }

.sidebar {
  width: 224px;
  background: var(--white);
  border-right: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
}

.main { flex: 1; min-width: 0; display: flex; flex-direction: column; }

/* ── SIDEBAR ────────────────────────────── */
.sb-brand {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 18px 16px;
  border-bottom: 1px solid var(--border);
}
.sb-brand-icon {
  width: 34px; height: 34px;
  background: var(--blue);
  border-radius: 7px;
  display: flex; align-items: center; justify-content: center;
  color: #fff; font-size: 17px; flex-shrink: 0;
}
.sb-brand-name { font-size: 15px; font-weight: 600; color: var(--gray-900); }
.sb-brand-sub  { font-size: 10px; color: var(--gray-400); font-family: var(--mono); margin-top: 1px; }

.sb-section { padding: 12px 0 4px; }
.sb-section-label {
  font-size: 10px; font-weight: 600; color: var(--gray-400);
  letter-spacing: .8px; text-transform: uppercase;
  padding: 0 16px 6px;
}
.sb-link {
  display: flex; align-items: center; gap: 9px;
  padding: 8px 16px;
  font-size: 13px; color: var(--gray-500);
  cursor: pointer; border-left: 3px solid transparent;
  transition: all .12s;
}
.sb-link:hover { color: var(--gray-900); background: var(--gray-50); }
.sb-link.active { color: var(--blue); background: var(--blue-lt); border-left-color: var(--blue); font-weight: 500; }
.sb-link i { font-size: 16px; }

.sb-footer {
  margin-top: auto;
  padding: 14px 16px;
  border-top: 1px solid var(--border);
}
.sb-sync {
  display: flex; align-items: center; gap: 7px;
  font-size: 12px; color: var(--gray-500);
}
.sync-dot {
  width: 7px; height: 7px; border-radius: 50%;
  background: var(--green-md); flex-shrink: 0;
  animation: pulse 2.5s infinite;
}
@keyframes pulse { 0%,100%{opacity:1} 50%{opacity:.4} }

/* ── TOPBAR ─────────────────────────────── */
.topbar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 12px 24px;
  background: var(--white);
  border-bottom: 1px solid var(--border);
  position: sticky; top: 0; z-index: 100;
  flex-wrap: wrap; gap: 10px;
  box-shadow: var(--shadow-sm);
}
.topbar-title { font-size: 16px; font-weight: 600; color: var(--gray-900); }
.topbar-right  { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }

.search {
  display: flex; align-items: center; gap: 7px;
  border: 1px solid var(--border);
  border-radius: 6px; padding: 6px 11px;
  background: var(--white);
}
.search input {
  border: none; outline: none;
  font-size: 13px; font-family: var(--font);
  color: var(--gray-900); width: 190px;
  background: transparent;
}
.search input::placeholder { color: var(--gray-400); }
.search i { color: var(--gray-400); font-size: 15px; }

.btn {
  display: inline-flex; align-items: center; gap: 6px;
  padding: 7px 13px; border-radius: 6px;
  font-size: 12px; font-weight: 500;
  cursor: pointer; font-family: var(--font);
  border: 1px solid var(--border);
  background: var(--white); color: var(--gray-700);
  transition: all .12s; text-decoration: none;
}
.btn:hover { background: var(--gray-50); border-color: var(--gray-300); }
.btn.primary { background: var(--blue); color: #fff; border-color: var(--blue); }
.btn.primary:hover { background: #1648c0; }
select.btn { cursor: pointer; }

/* ── CONTENT ────────────────────────────── */
.content { padding: 24px; flex: 1; }

.page-header {
  display: flex; align-items: center; justify-content: space-between;
  margin-bottom: 20px; flex-wrap: wrap; gap: 10px;
}
.page-header h1 { font-size: 18px; font-weight: 600; color: var(--gray-900); }
.page-header p  { font-size: 12px; color: var(--gray-400); font-family: var(--mono); margin-top: 2px; }

/* ── ALERTS ─────────────────────────────── */
.alert {
  display: flex; align-items: flex-start; gap: 10px;
  border-radius: 6px; padding: 10px 14px;
  margin-bottom: 8px; font-size: 12.5px; line-height: 1.5;
}
.alert.danger { background: var(--red-lt); border: 1px solid #fca5a5; color: var(--red); }
.alert.warning { background: var(--amber-lt); border: 1px solid #fcd34d; color: var(--amber); }
.alert i { font-size: 16px; flex-shrink: 0; margin-top: 1px; }

/* ── KPI CARDS ──────────────────────────── */
.kpi-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(148px, 1fr));
  gap: 12px; margin-bottom: 22px;
}
.kpi-card {
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 16px;
  box-shadow: var(--shadow-sm);
  position: relative;
  overflow: hidden;
}
.kpi-card::before {
  content: '';
  position: absolute; top: 0; left: 0; right: 0; height: 3px;
}
.kpi-card.blue::before  { background: var(--blue); }
.kpi-card.green::before { background: var(--green-md); }
.kpi-card.amber::before { background: var(--amber-md); }
.kpi-card.red::before   { background: var(--red-md); }
.kpi-card.teal::before  { background: #0891b2; }

.kpi-icon {
  width: 32px; height: 32px; border-radius: 7px;
  display: flex; align-items: center; justify-content: center;
  font-size: 16px; margin-bottom: 10px;
}
.kpi-icon.blue  { background: var(--blue-lt); color: var(--blue); }
.kpi-icon.green { background: var(--green-lt); color: var(--green); }
.kpi-icon.amber { background: var(--amber-lt); color: var(--amber); }
.kpi-icon.red   { background: var(--red-lt); color: var(--red); }
.kpi-icon.teal  { background: #e0f2fe; color: #0891b2; }

.kpi-label { font-size: 11px; color: var(--gray-400); font-family: var(--mono); margin-bottom: 4px; letter-spacing: .2px; }
.kpi-value { font-size: 22px; font-weight: 600; color: var(--gray-900); line-height: 1.1; }
.kpi-sub   { font-size: 11px; color: var(--gray-400); margin-top: 3px; }

/* ── SECTION TITLE ──────────────────────── */
.sec-title {
  font-size: 12px; font-weight: 600;
  color: var(--gray-500); letter-spacing: .5px;
  text-transform: uppercase; margin-bottom: 12px;
  padding-bottom: 8px; border-bottom: 1px solid var(--border);
}

/* ── CHARTS ROW ─────────────────────────── */
.charts-grid {
  display: grid;
  grid-template-columns: 1.5fr 1fr;
  gap: 16px; margin-bottom: 22px;
}
.chart-panel {
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: 8px; padding: 18px 20px;
  box-shadow: var(--shadow-sm);
}
.chart-panel-title { font-size: 13px; font-weight: 600; color: var(--gray-900); margin-bottom: 2px; }
.chart-panel-sub   { font-size: 11px; color: var(--gray-400); font-family: var(--mono); margin-bottom: 14px; }
.legend-row { display: flex; flex-wrap: wrap; gap: 14px; margin-bottom: 10px; }
.leg { display: flex; align-items: center; gap: 5px; font-size: 11px; color: var(--gray-500); }
.leg-dot { width: 9px; height: 9px; border-radius: 50%; flex-shrink: 0; }

/* ── TABLE ──────────────────────────────── */
.table-panel {
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: 8px;
  box-shadow: var(--shadow-sm);
  margin-bottom: 22px;
  overflow: hidden;
}
.table-toolbar {
  display: flex; align-items: center; gap: 6px;
  padding: 12px 16px; border-bottom: 1px solid var(--border);
  flex-wrap: wrap;
}
.tab-btn {
  font-size: 12px; padding: 5px 11px;
  border-radius: 5px; cursor: pointer;
  border: 1px solid var(--border);
  background: var(--white); color: var(--gray-500);
  font-family: var(--font); transition: all .12s;
}
.tab-btn:hover { border-color: var(--gray-300); color: var(--gray-700); }
.tab-btn.on { background: var(--blue-lt); color: var(--blue); border-color: #93c5fd; font-weight: 500; }

.tbl-scroll { overflow-x: auto; }
table { width: 100%; border-collapse: collapse; min-width: 800px; }
thead tr { background: var(--gray-50); border-bottom: 2px solid var(--border); }
thead th {
  font-size: 11px; font-weight: 600; color: var(--gray-500);
  font-family: var(--mono); letter-spacing: .3px;
  text-align: left; padding: 10px 14px;
  white-space: nowrap; cursor: pointer; user-select: none;
}
thead th:hover { color: var(--gray-900); }
thead th.asc::after  { content: ' ↑'; color: var(--blue); }
thead th.desc::after { content: ' ↓'; color: var(--blue); }
tbody tr { border-bottom: 1px solid var(--gray-100); transition: background .1s; cursor: pointer; }
tbody tr:hover { background: var(--gray-50); }
tbody tr:last-child { border-bottom: none; }
td { padding: 10px 14px; font-size: 12.5px; vertical-align: middle; }
.prod-name   { font-size: 13px; font-weight: 500; color: var(--gray-900); }
.prod-flavor { font-size: 11px; color: var(--gray-400); font-family: var(--mono); margin-top: 1px; }

.badge {
  display: inline-flex; align-items: center;
  font-size: 10.5px; padding: 2px 8px;
  border-radius: 4px; font-weight: 500;
  font-family: var(--mono); white-space: nowrap;
}
.bg-green  { background: var(--green-lt); color: var(--green); }
.bg-amber  { background: var(--amber-lt); color: var(--amber); }
.bg-red    { background: var(--red-lt); color: var(--red); }
.bg-blue   { background: var(--blue-lt); color: var(--blue); }

.mono-val { font-family: var(--mono); font-size: 12px; }
.bar-wrap  { display: flex; align-items: center; gap: 8px; }
.bar-track { flex: 1; height: 4px; background: var(--gray-100); border-radius: 4px; min-width: 55px; }
.bar-fill  { height: 100%; border-radius: 4px; }

.img-th  {
  width: 38px; height: 38px; border-radius: 6px;
  border: 1px solid var(--border); object-fit: cover; display: block;
  background: var(--gray-100);
}
.img-ph  {
  width: 38px; height: 38px; border-radius: 6px;
  border: 1px solid var(--border); background: var(--gray-100);
  display: flex; align-items: center; justify-content: center;
  color: var(--gray-300); font-size: 16px;
}

/* ── BOTTOM GRID ────────────────────────── */
.bottom-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 24px; }
.rank-panel {
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: 8px; padding: 16px 18px;
  box-shadow: var(--shadow-sm);
}
.rank-row {
  display: flex; align-items: center; gap: 10px;
  padding: 8px 0; border-bottom: 1px solid var(--gray-100);
}
.rank-row:last-child { border-bottom: none; }
.rank-n  { font-size: 11px; color: var(--gray-400); font-family: var(--mono); width: 16px; flex-shrink: 0; }
.rank-info { flex: 1; min-width: 0; }
.rank-name { font-size: 12.5px; font-weight: 500; color: var(--gray-900); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.rank-hint { font-size: 11px; color: var(--gray-400); font-family: var(--mono); margin-top: 1px; }
.rank-val  { font-size: 12px; font-weight: 600; font-family: var(--mono); flex-shrink: 0; }

/* ── LOADING / ERROR ────────────────────── */
.loading-state {
  display: flex; flex-direction: column; align-items: center;
  justify-content: center; padding: 80px 20px; gap: 14px;
}
.spinner {
  width: 32px; height: 32px;
  border: 2px solid var(--gray-200); border-top-color: var(--blue);
  border-radius: 50%; animation: spin .7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }

.err-box {
  margin: 24px;
  background: var(--red-lt); border: 1px solid #fca5a5;
  border-radius: 8px; padding: 18px 20px;
}
.err-box strong { color: var(--red); font-size: 14px; }
.err-box p { font-size: 12px; color: var(--gray-700); margin-top: 7px; line-height: 1.6; }

/* ── FOOTER ─────────────────────────────── */
.footer {
  text-align: center; padding: 14px;
  font-size: 11px; color: var(--gray-400); font-family: var(--mono);
  border-top: 1px solid var(--border); background: var(--white);
}

@media (max-width: 920px) {
  .sidebar { display: none; }
  .charts-grid { grid-template-columns: 1fr; }
  .bottom-grid  { grid-template-columns: 1fr; }
  .kpi-row { grid-template-columns: repeat(2, 1fr); }
  .content { padding: 16px; }
}
</style>
</head>
<body>
<div class="layout">

<!-- ══ SIDEBAR ══════════════════════════════════════ -->
<aside class="sidebar">
  <div class="sb-brand">
    <div class="sb-brand-icon"><i class="ti ti-chart-bar"></i></div>
    <div>
      <div class="sb-brand-name">VapeDash</div>
      <div class="sb-brand-sub">GESTIÓN DE NEGOCIO</div>
    </div>
  </div>

  <div class="sb-section">
    <div class="sb-section-label">Principal</div>
    <div class="sb-link active"><i class="ti ti-layout-dashboard"></i> Dashboard</div>
    <div class="sb-link" onclick="scrollTo(0,0)"><i class="ti ti-package"></i> Inventario</div>
    <div class="sb-link" onclick="scrollTo(0,0)"><i class="ti ti-chart-pie"></i> Estadísticas</div>
  </div>
  <div class="sb-section">
    <div class="sb-section-label">Herramientas</div>
    <div class="sb-link" onclick="loadData()"><i class="ti ti-refresh"></i> Actualizar datos</div>
    <div class="sb-link" onclick="exportCSV()"><i class="ti ti-file-download"></i> Exportar CSV</div>
    <div class="sb-link" onclick="window.open('https://docs.google.com/spreadsheets','_blank')"><i class="ti ti-brand-google"></i> Abrir Google Sheets</div>
  </div>

  <div class="sb-footer">
    <div class="sb-sync">
      <span class="sync-dot" id="syncDot"></span>
      <span id="syncText">Cargando...</span>
    </div>
    <div style="font-size:11px;color:var(--gray-300);margin-top:4px;font-family:var(--mono)" id="syncTime"></div>
  </div>
</aside>

<!-- ══ MAIN ══════════════════════════════════════════ -->
<div class="main">

  <!-- Topbar -->
  <div class="topbar">
    <div class="topbar-title">Panel de Control — Stock &amp; Ganancias</div>
    <div class="topbar-right">
      <div class="search">
        <i class="ti ti-search"></i>
        <input type="text" id="searchInput" placeholder="Buscar producto o sabor..." oninput="doSearch(this.value)">
      </div>
      <select class="btn" id="catSel" onchange="setCat(this.value)">
        <option value="">Todas las categorías</option>
      </select>
      <button class="btn primary" onclick="loadData()"><i class="ti ti-refresh"></i> Actualizar</button>
      <button class="btn" onclick="exportCSV()"><i class="ti ti-download"></i> CSV</button>
    </div>
  </div>

  <!-- Content -->
  <div class="content">
    <div class="page-header">
      <div>
        <h1>Resumen general</h1>
        <p id="headerSub">Conectando a Google Sheets...</p>
      </div>
    </div>

    <div id="appRoot">
      <div class="loading-state">
        <div class="spinner"></div>
        <div style="font-size:13px;color:var(--gray-500)">Cargando datos desde Google Sheets...</div>
      </div>
    </div>
  </div>

  <div class="footer" id="footerBar">VapeDash · Google Sheets sync · Actualización automática cada 60 seg</div>
</div><!-- /main -->
</div><!-- /layout -->

<script>
const SHEET_URL = 'https://script.google.com/macros/s/AKfycbwiJQnEG8DaPdTtXeP6vKKKB813pqGNul6LLXQnf6_-D1j4ESTPfcCm4p4Xf87fASn7aA/exec';

let ALL = [], filtered = [];
let sortKey = 'potential', sortDir = -1;
let activeTab = 'all', searchQ = '', catQ = '';
let chartMargen = null, chartEstado = null;

/* ── PARSE ───────────────────────────────── */
function parsePesos(v) {
  if (typeof v === 'number') return v;
  if (!v) return 0;
  return parseFloat(String(v).replace(/[$\s]/g,'').replace(/\./g,'').replace(',','.')) || 0;
}
function calc(p) {
  const costo   = parsePesos(p['COSTO']);
  const precio  = parsePesos(p['PRECIO VENTA']);
  const stock   = parseInt(p['STOCK ACTUAL']) || 0;
  const margin  = precio > 0 ? (precio - costo) / precio * 100 : 0;
  const netGain = precio - costo;
  const invVal  = stock * costo;
  const potential = stock * netGain;
  const roi     = costo > 0 ? (precio - costo) / costo * 100 : 0;
  return { ...p, _c: costo, _p: precio, _s: stock, margin, netGain, invVal, potential, roi };
}
function status(p) {
  const st = String(p['ESTADO'] || '').toUpperCase();
  if (st.includes('SIN') || p._s === 0) return 'sin';
  if (st.includes('BAJO') || p._s <= 3) return 'bajo';
  return 'ok';
}

/* ── FORMAT ──────────────────────────────── */
const $  = n => '$' + Math.round(n).toLocaleString('es-AR');
const pct = n => Math.round(n) + '%';
const num = n => Math.round(n).toLocaleString('es-AR');

/* ── LOAD ────────────────────────────────── */
async function loadData() {
  setSyncState('loading');
  try {
    const r = await fetch(SHEET_URL + '?t=' + Date.now());
    if (!r.ok) throw new Error('HTTP ' + r.status);
    const json = await r.json();
    let rows = [];
    if (Array.isArray(json)) rows = json;
    else if (json.productos) rows = json.productos;
    else if (json.data) rows = json.data;
    else { const k = Object.values(json).find(v => Array.isArray(v)); if (k) rows = k; }
    rows = rows.filter(r => r['PRODUCTO'] && String(r['PRODUCTO']).trim());
    if (!rows.length) throw new Error('No se encontraron filas con datos en la pestaña STOCK.');
    ALL = rows.map(calc);
    filtered = [...ALL];
    buildCatFilter();
    renderAll();
    setSyncState('ok');
    const now = new Date();
    document.getElementById('syncTime').textContent = now.toLocaleTimeString('es-AR',{hour:'2-digit',minute:'2-digit'}) + ' hs';
    document.getElementById('headerSub').textContent =
      ALL.length + ' productos · Actualizado ' + now.toLocaleTimeString('es-AR',{hour:'2-digit',minute:'2-digit'});
  } catch(e) {
    setSyncState('error');
    document.getElementById('appRoot').innerHTML = `
      <div class="err-box">
        <strong><i class="ti ti-alert-circle"></i> No se pudo conectar con Google Sheets</strong>
        <p>${e.message}<br><br>
        Verificá en Apps Script → Implementar → Administrar implementaciones → Acceso: <b>"Cualquier usuario (anónimo)"</b></p>
        <button class="btn" style="margin-top:12px" onclick="loadData()"><i class="ti ti-refresh"></i> Reintentar</button>
      </div>`;
  }
}

function setSyncState(s) {
  const dot = document.getElementById('syncDot');
  const txt = document.getElementById('syncText');
  if (s === 'loading') { dot.style.background='#f59e0b'; txt.textContent='Sincronizando...'; }
  else if (s === 'ok')  { dot.style.background='#22c55e'; txt.textContent='Conectado'; }
  else                  { dot.style.background='#ef4444'; txt.textContent='Sin conexión'; }
}

function buildCatFilter() {
  const cats = [...new Set(ALL.map(p => p['CATEGORÍA']||'').filter(Boolean))];
  const sel = document.getElementById('catSel');
  const cur = sel.value;
  sel.innerHTML = '<option value="">Todas las categorías</option>' +
    cats.map(c => `<option value="${c}">${c}</option>`).join('');
  sel.value = cur;
}

/* ── RENDER ALL ──────────────────────────── */
function renderAll() {
  document.getElementById('appRoot').innerHTML = `
    <div id="alertsZone" style="margin-bottom:16px"></div>
    <div class="kpi-row" id="kpiRow"></div>
    <div class="charts-grid" id="chartsGrid"></div>
    <div class="sec-title">Detalle de inventario</div>
    <div class="table-panel">
      <div class="table-toolbar" id="toolbar"></div>
      <div class="tbl-scroll"><table><thead id="tHead"></thead><tbody id="tBody"></tbody></table></div>
    </div>
    <div class="bottom-grid" id="bottomGrid"></div>`;
  renderAlerts();
  renderKPIs();
  renderCharts();
  buildToolbar();
  buildThead();
  applyFilters();
  renderTable();
  renderRankings();
}

/* ── ALERTS ──────────────────────────────── */
function renderAlerts() {
  const sin  = ALL.filter(p => status(p)==='sin');
  const bajo = ALL.filter(p => status(p)==='bajo');
  let h = '';
  if (sin.length)
    h += `<div class="alert danger"><i class="ti ti-alert-triangle"></i><div>
      <strong>Sin stock (${sin.length} productos):</strong> ${sin.map(p=>p['PRODUCTO']+' '+p['SABOR']).join(' · ')}
    </div></div>`;
  if (bajo.length)
    h += `<div class="alert warning"><i class="ti ti-alert-circle"></i><div>
      <strong>Stock bajo (${bajo.length} productos):</strong> ${bajo.map(p=>p['PRODUCTO']+' '+p['SABOR']+' ('+p._s+' ud.)').join(' · ')}
    </div></div>`;
  document.getElementById('alertsZone').innerHTML = h;
}

/* ── KPIs ────────────────────────────────── */
function renderKPIs() {
  const inv  = ALL.reduce((a,p)=>a+p.invVal,0);
  const pot  = ALL.reduce((a,p)=>a+p.potential,0);
  const avgM = ALL.length ? ALL.reduce((a,p)=>a+p.margin,0)/ALL.length : 0;
  const sinN = ALL.filter(p=>status(p)==='sin').length;
  const bajN = ALL.filter(p=>status(p)==='bajo').length;
  const stTot= ALL.reduce((a,p)=>a+p._s,0);
  const topM = [...ALL].sort((a,b)=>b.margin-a.margin)[0];

  const kpis = [
    { icon:'ti-package',     cls:'blue',  label:'Total productos', value:ALL.length,    sub:stTot+' unidades en stock' },
    { icon:'ti-currency-dollar', cls:'green', label:'Capital invertido', value:$(inv),  sub:'Costo total del stock' },
    { icon:'ti-trending-up', cls:'teal',  label:'Ganancia potencial', value:$(pot),     sub:'Si vendés todo el stock' },
    { icon:'ti-percent',     cls:'blue',  label:'Margen promedio',  value:pct(avgM),    sub:'Sobre todos los productos' },
    { icon:'ti-alert-triangle', cls:'red', label:'Sin stock',        value:sinN,        sub:'Productos agotados' },
    { icon:'ti-alert-circle', cls:'amber', label:'Stock bajo',       value:bajN,        sub:'≤ 3 unidades' },
    { icon:'ti-star',        cls:'green', label:'Mejor margen',     value:topM?pct(topM.margin):'-', sub:topM?(topM['PRODUCTO']+' '+topM['SABOR']):'—' },
    { icon:'ti-calculator',  cls:'teal',  label:'Mayor ganancia/ud', value:topM?$(topM.netGain):'-', sub:topM?'ROI '+Math.round(topM.roi)+'%':'—' },
  ];
  document.getElementById('kpiRow').innerHTML = kpis.map(k => `
    <div class="kpi-card ${k.cls}">
      <div class="kpi-icon ${k.cls}"><i class="ti ${k.icon}"></i></div>
      <div class="kpi-label">${k.label}</div>
      <div class="kpi-value">${k.value}</div>
      <div class="kpi-sub">${k.sub}</div>
    </div>`).join('');
}

/* ── CHARTS ──────────────────────────────── */
function renderCharts() {
  document.getElementById('chartsGrid').innerHTML = `
    <div class="chart-panel">
      <div class="chart-panel-title">Ganancia potencial por producto</div>
      <div class="chart-panel-sub">Stock actual × (Precio − Costo) — top 8 productos</div>
      <div class="legend-row" id="leg1"></div>
      <div style="position:relative;height:230px"><canvas id="ch1" role="img" aria-label="Ganancia potencial por producto">Gráfico de barras</canvas></div>
    </div>
    <div class="chart-panel">
      <div class="chart-panel-title">Estado del inventario</div>
      <div class="chart-panel-sub">Distribución por disponibilidad</div>
      <div class="legend-row" id="leg2"></div>
      <div style="position:relative;height:230px"><canvas id="ch2" role="img" aria-label="Estado del inventario">Gráfico de dona</canvas></div>
    </div>`;

  const top8 = [...ALL].sort((a,b)=>b.potential-a.potential).slice(0,8);
  const barColors = top8.map(p => {
    const s = status(p);
    return s==='sin'?'#ef444488':s==='bajo'?'#f59e0b99':'#1a56db99';
  });
  const borderColors = top8.map(p => {
    const s = status(p);
    return s==='sin'?'#ef4444':s==='bajo'?'#f59e0b':'#1a56db';
  });

  if (chartMargen) chartMargen.destroy();
  chartMargen = new Chart(document.getElementById('ch1'), {
    type: 'bar',
    data: {
      labels: top8.map(p => (p['PRODUCTO']+' '+p['SABOR']).slice(0,22)),
      datasets: [{
        label: 'Gan. potencial',
        data: top8.map(p => p.potential),
        backgroundColor: barColors,
        borderColor: borderColors,
        borderWidth: 1.5,
        borderRadius: 4,
        borderSkipped: false
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: false }, tooltip: { callbacks: { label: v => $(v.raw) } } },
      scales: {
        x: { ticks: { color:'#8f99ab', font:{size:9} }, grid: { color:'#eef0f4' } },
        y: { ticks: { color:'#8f99ab', font:{size:9}, callback: v => '$'+Math.round(v/1000)+'k' }, grid: { color:'#eef0f4' } }
      }
    }
  });
  document.getElementById('leg1').innerHTML = `
    <span class="leg"><span class="leg-dot" style="background:#1a56db"></span>Con stock</span>
    <span class="leg"><span class="leg-dot" style="background:#f59e0b"></span>Stock bajo</span>
    <span class="leg"><span class="leg-dot" style="background:#ef4444"></span>Sin stock</span>`;

  const ok  = ALL.filter(p=>status(p)==='ok').length;
  const baj = ALL.filter(p=>status(p)==='bajo').length;
  const sin = ALL.filter(p=>status(p)==='sin').length;

  if (chartEstado) chartEstado.destroy();
  chartEstado = new Chart(document.getElementById('ch2'), {
    type: 'doughnut',
    data: {
      labels: ['Con stock','Stock bajo','Sin stock'],
      datasets: [{
        data: [ok, baj, sin],
        backgroundColor: ['#1a56db44','#f59e0b44','#ef444444'],
        borderColor: ['#1a56db','#f59e0b','#ef4444'],
        borderWidth: 2
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false, cutout: '65%',
      plugins: {
        legend: { display: false },
        tooltip: { callbacks: { label: v => v.label + ': ' + v.raw + ' prods' } }
      }
    }
  });
  document.getElementById('leg2').innerHTML = `
    <span class="leg"><span class="leg-dot" style="background:#1a56db"></span>OK (${ok})</span>
    <span class="leg"><span class="leg-dot" style="background:#f59e0b"></span>Bajo (${baj})</span>
    <span class="leg"><span class="leg-dot" style="background:#ef4444"></span>Sin stock (${sin})</span>`;
}

/* ── TOOLBAR ─────────────────────────────── */
function buildToolbar() {
  const ok  = ALL.filter(p=>status(p)==='ok').length;
  const baj = ALL.filter(p=>status(p)==='bajo').length;
  const sin = ALL.filter(p=>status(p)==='sin').length;
  document.getElementById('toolbar').innerHTML = `
    <button class="tab-btn on"  onclick="setTab(this,'all')">Todos (${ALL.length})</button>
    <button class="tab-btn" onclick="setTab(this,'ok')">Con stock (${ok})</button>
    <button class="tab-btn" onclick="setTab(this,'bajo')">Stock bajo (${baj})</button>
    <button class="tab-btn" onclick="setTab(this,'sin')">Sin stock (${sin})</button>
    <button class="tab-btn" onclick="setTab(this,'top')">Más rentables</button>`;
}

/* ── THEAD ───────────────────────────────── */
function buildThead() {
  document.getElementById('tHead').innerHTML = `<tr>
    <th style="width:50px">Img.</th>
    <th onclick="doSort('PRODUCTO')" id="th-PRODUCTO">Producto / Sabor</th>
    <th onclick="doSort('margin')" id="th-margin">Margen %</th>
    <th onclick="doSort('_c')" id="th-_c">Costo</th>
    <th onclick="doSort('_p')" id="th-_p">Precio venta</th>
    <th onclick="doSort('netGain')" id="th-netGain">Gan. neta / ud.</th>
    <th onclick="doSort('_s')" id="th-_s">Stock</th>
    <th onclick="doSort('potential')" id="th-potential">Gan. potencial</th>
    <th onclick="doSort('roi')" id="th-roi">ROI %</th>
    <th>Estado</th>
    <th>Pago</th>
  </tr>`;
}

/* ── FILTERS ─────────────────────────────── */
function applyFilters() {
  let d = [...ALL];
  if (searchQ) d = d.filter(p =>
    (p['PRODUCTO']+' '+p['SABOR']+' '+(p['CATEGORÍA']||'')).toLowerCase().includes(searchQ));
  if (catQ) d = d.filter(p => p['CATEGORÍA'] === catQ);
  if (activeTab === 'ok')   d = d.filter(p => status(p)==='ok');
  else if (activeTab === 'bajo') d = d.filter(p => status(p)==='bajo');
  else if (activeTab === 'sin')  d = d.filter(p => status(p)==='sin');
  else if (activeTab === 'top')  d = d.filter(p => p.margin >= 40);
  d.sort((a,b) => (a[sortKey] > b[sortKey] ? 1 : -1) * sortDir);
  filtered = d;
}

/* ── TABLE ───────────────────────────────── */
function renderTable() {
  const barC = m => m>=50?'#0e7c3a':m>=35?'#1a56db':m>=20?'#92530a':'#b91c1c';
  const statusBadge = p => {
    const s = status(p);
    if (s==='sin')  return '<span class="badge bg-red">Sin stock</span>';
    if (s==='bajo') return '<span class="badge bg-amber">Stock bajo</span>';
    return '<span class="badge bg-green">Disponible</span>';
  };

  if (!filtered.length) {
    document.getElementById('tBody').innerHTML =
      '<tr><td colspan="11" style="text-align:center;padding:32px;color:var(--gray-400);font-size:13px">Sin resultados para este filtro</td></tr>';
    return;
  }

  document.getElementById('tBody').innerHTML = filtered.map(p => `
    <tr>
      <td>${p['IMAGEN_URL']
        ? `<img class="img-th" src="${p['IMAGEN_URL']}" alt="${p['PRODUCTO']}" loading="lazy" onerror="this.outerHTML='<div class=img-ph><i class=ti ti-package></i></div>'">`
        : '<div class="img-ph"><i class="ti ti-package"></i></div>'}</td>
      <td>
        <div class="prod-name">${p['PRODUCTO']||''}</div>
        <div class="prod-flavor">${p['SABOR']||''}${p['CATEGORÍA']?' · '+p['CATEGORÍA']:''}</div>
      </td>
      <td>
        <div class="bar-wrap">
          <span class="mono-val" style="min-width:34px;color:${barC(p.margin)};font-weight:600">${Math.round(p.margin)}%</span>
          <div class="bar-track"><div class="bar-fill" style="width:${Math.min(Math.max(p.margin,0),100)}%;background:${barC(p.margin)}"></div></div>
        </div>
      </td>
      <td class="mono-val">${$(p._c)}</td>
      <td class="mono-val" style="font-weight:600">${$(p._p)}</td>
      <td class="mono-val" style="color:var(--green);font-weight:600">${$(p.netGain)}</td>
      <td class="mono-val" style="color:${p._s===0?'var(--red)':p._s<=3?'var(--amber)':'var(--gray-900)'};font-weight:600">${p._s}</td>
      <td class="mono-val" style="color:var(--blue);font-weight:600">${$(p.potential)}</td>
      <td class="mono-val">${Math.round(p.roi)}%</td>
      <td>${statusBadge(p)}</td>
      <td>${p['mp_link']
        ? `<a href="${p['mp_link']}" target="_blank" class="badge bg-blue" style="text-decoration:none"><i class="ti ti-external-link" style="font-size:11px"></i> MP</a>`
        : '<span style="color:var(--gray-300)">—</span>'}</td>
    </tr>`).join('');

  document.querySelectorAll('thead th[id^="th-"]').forEach(th => {
    th.className = '';
    const k = th.id.replace('th-','');
    if (k === sortKey) th.className = sortDir === -1 ? 'desc' : 'asc';
  });
}

/* ── RANKINGS ────────────────────────────── */
function renderRankings() {
  const top5 = [...ALL].sort((a,b)=>b.margin-a.margin).slice(0,5);
  const rep  = [...ALL].filter(p=>status(p)!=='ok').sort((a,b)=>a._s-b._s).slice(0,5);

  document.getElementById('bottomGrid').innerHTML = `
    <div class="rank-panel">
      <div class="sec-title">Top 5 — Más rentables por margen</div>
      ${top5.map((p,i) => `
        <div class="rank-row">
          <div class="rank-n">${i+1}</div>
          <div class="rank-info">
            <div class="rank-name">${p['PRODUCTO']} ${p['SABOR']}</div>
            <div class="rank-hint">ROI ${Math.round(p.roi)}% · Gan. ${$(p.netGain)}/ud · Stock ${p._s}</div>
          </div>
          <div class="rank-val" style="color:var(--green)">${Math.round(p.margin)}%</div>
        </div>`).join('')}
    </div>
    <div class="rank-panel">
      <div class="sec-title">Reponer urgente — Stock crítico</div>
      ${rep.length ? rep.map((p,i) => `
        <div class="rank-row">
          <div class="rank-n">${i+1}</div>
          <div class="rank-info">
            <div class="rank-name">${p['PRODUCTO']} ${p['SABOR']}</div>
            <div class="rank-hint">Gan. potencial bloqueada: ${$(p.potential)}</div>
          </div>
          <div class="rank-val" style="color:${p._s===0?'var(--red)':'var(--amber)'}">${p._s===0?'AGOTADO':p._s+' ud.'}</div>
        </div>`).join('')
      : '<div style="text-align:center;padding:20px;font-size:12px;color:var(--gray-400)"><i class="ti ti-circle-check" style="font-size:22px;display:block;margin-bottom:6px;color:var(--green-md)"></i>Todo el inventario está en orden</div>'}
    </div>`;
}

/* ── EVENTS ──────────────────────────────── */
function setTab(el, t) {
  activeTab = t;
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('on'));
  el.classList.add('on');
  applyFilters(); renderTable();
}
function doSearch(v) { searchQ = v.toLowerCase(); applyFilters(); renderTable(); }
function setCat(v)   { catQ = v; applyFilters(); renderTable(); }
function doSort(k) {
  if (sortKey === k) sortDir *= -1; else { sortKey = k; sortDir = -1; }
  applyFilters(); renderTable();
}

/* ── EXPORT CSV ──────────────────────────── */
function exportCSV() {
  if (!ALL.length) return alert('No hay datos para exportar.');
  const cols = [
    ['PRODUCTO','Producto'],['SABOR','Sabor'],['CATEGORÍA','Categoría'],
    ['_c','Costo'],['_p','Precio Venta'],['_s','Stock'],['ESTADO','Estado'],
    ['margin','Margen %'],['netGain','Gan. neta/ud'],['invVal','Valor inventario'],
    ['potential','Gan. potencial'],['roi','ROI %']
  ];
  const hdr = cols.map(c=>c[1]).join(',');
  const rows = ALL.map(p => cols.map(([k]) => {
    let v = p[k] !== undefined ? p[k] : '';
    if (['margin','netGain','invVal','potential','roi','_c','_p','_s'].includes(k)) v = Math.round(v);
    return '"'+String(v).replace(/"/g,'""')+'"';
  }).join(','));
  const csv = '\uFEFF' + [hdr, ...rows].join('\n');
  const a = document.createElement('a');
  a.href = 'data:text/csv;charset=utf-8,' + encodeURIComponent(csv);
  a.download = 'vapedash_' + new Date().toISOString().slice(0,10) + '.csv';
  a.click();
}

/* ── INIT ────────────────────────────────── */
loadData();
setInterval(loadData, 60000);
</script>
</body>
</html>
