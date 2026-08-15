<html lang="en">
<head>
<meta charset="UTF-16">
<title>Control Valve CV Calculator (MKS Units) - IEC 60534 / ISA 75.01</title>
<style>
  :root{
    --navy:#1b3a5c; --steel:#2f6690; --lt:#eef3f7; --line:#c8d6e0; --ok:#1e7d34; --warn:#b34700; --bad:#a4161a;
  }
  *{box-sizing:border-box;}
  body{font-family:Arial, "Segoe UI", sans-serif; margin:0; background:#f4f7f9; color:#1a2733; overflow-x:hidden;}
  header{background:var(--navy); color:#fff; padding:10px 16px;}
  header h1{margin:0; font-size:18px;}
  header p{margin:2px 0 0; font-size:11px; color:#cfe0ee; line-height:1.4;}
  .tabs{display:flex; background:var(--steel); flex-wrap:wrap;}
  .tabs button{flex:1; min-width:140px; padding:10px 6px; background:var(--steel); color:#fff; border:none; border-right:1px solid #245577; cursor:pointer; font-size:13px; font-weight:bold;}
  .tabs button.active{background:var(--lt); color:var(--navy);}
  .wrap{max-width:1000px; width:min(100%, 1000px); margin:0 auto; padding:14px;}
  .tabpanel{display:none;}
  .tabpanel.active{display:block;}
  .card{background:#fff; border:1px solid var(--line); border-radius:6px; padding:12px 14px; margin-bottom:12px;}
  .card h2{font-size:14px; margin:0 0 8px; color:var(--navy); border-bottom:2px solid var(--lt); padding-bottom:4px;}
  .grid{display:grid; grid-template-columns:1fr 1fr; gap:16px;}
  .row{display:flex; align-items:center; gap:8px; margin-bottom:7px; font-size:12.5px; flex-wrap:wrap;}
  .row label{flex:0 0 190px; max-width:100%;}
  .row input, .row select{flex:1; min-width:0; padding:4px 6px; font-size:12.5px; border:1px solid #9fb4c2; border-radius:3px;}
  .unit{flex:0 0 55px; color:#5a6b78; font-size:11.5px;}
  .btnbar{margin:10px 0; display:flex; gap:8px; flex-wrap:wrap;}
  button.act{background:var(--navy); color:#fff; border:none; padding:8px 16px; border-radius:4px; cursor:pointer; font-size:13px;}
  button.act.secondary{background:#5a7a92;}
  button.act:hover{background:#12283f;}
  .result-table{width:100%; border-collapse:collapse; font-size:12.5px;}
  .result-table td{padding:4px 6px; border-bottom:1px solid var(--lt);}
  .result-table td:first-child{color:#425a68;}
  .result-table td.val{font-weight:bold; text-align:right; color:var(--navy);}
  .badge{display:inline-block; padding:2px 8px; border-radius:10px; font-size:11px; font-weight:bold; color:#fff;}
  .badge.ok{background:var(--ok);} .badge.warn{background:var(--warn);} .badge.bad{background:var(--bad);}
  .note{font-size:11px; color:#5a6b78; margin-top:6px; line-height:1.4;}
  .flexrow{display:flex; gap:14px; flex-wrap:wrap;}
  .flexrow>div{flex:1; min-width:280px;}
  table.curve{width:100%; border-collapse:collapse; font-size:11.5px; margin-top:8px;}
  table.curve th, table.curve td{border:1px solid var(--line); padding:3px 6px; text-align:center;}
  table.curve th{background:var(--lt);}
  canvas{border:1px solid var(--line); background:#fff; display:block; width:100%; max-width:100%; height:auto;}
  .charwrap{display:flex; gap:6px; flex-wrap:wrap; margin-bottom:8px;}
  .charwrap button{flex:1; min-width:110px; padding:6px; font-size:11.5px; border:1px solid var(--line); background:#fff; cursor:pointer; border-radius:4px;}
  .charwrap button.sel{background:var(--navy); color:#fff;}
  select#presetSel, select#presetSelR{width:100%;}

  @media (max-width: 760px){
    .wrap{padding:10px 8px;}
    .grid{grid-template-columns:1fr; gap:10px;}
    .row{align-items:stretch;}
    .row label{flex-basis:100%; margin-bottom:2px;}
    .row input, .row select{width:100%; flex:1 1 100%;}
    .unit{flex-basis: auto;}
    .tabs button{min-width:120px;}
    .charwrap button{min-width:90px;}
    .result-table{display:block; overflow-x:auto;}
    table.curve{display:block; overflow-x:auto;}
  }

  @media print{
    header, .tabs, .no-print{display:none !important;}
    body{background:#fff;}
    .wrap{max-width:100%; padding:0; margin:0;}
    .tabpanel{display:none !important;}
    .tabpanel.active{display:block !important;}
    .card{border:1px solid #888; page-break-inside:avoid; margin-bottom:6px; padding:6px 8px;}
    .card h2{font-size:12px;}
    .row{font-size:10px; margin-bottom:3px;}
    .row input,.row select{font-size:10px; padding:1px 3px;}
    .result-table{font-size:10px;}
    table.curve{font-size:9px;}
    canvas{max-height:180px;}
    @page{size:A4; margin:10mm;}
  }
</style>
</head>
<body>
<header>
  <h1>Control Valve CV Calculator &mdash; MKS Units</h1>
  <p>Sizing (Liquid / Gas &amp; Vapour, incl. choked flow) &middot; Existing Valve Rating &middot; Opening vs Flow Curve &middot; Field Data Fit &nbsp;|&nbsp; Basis: IEC 60534-2-1 / ISA 75.01.01</p>
</header>
<div class="tabs no-print">
  <button class="active" onclick="showTab('sizing',this)">1. CV Sizing</button>
  <button onclick="showTab('twophase',this)">2. Two-Phase Inlet</button>
  <button onclick="showTab('rating',this)">3. Existing Valve Rating</button>
  <button onclick="showTab('opening',this)">4. Opening % vs Flow</button>
  <button onclick="showTab('fielddata',this)">5. Fit from Field Data</button>
</div>
<div class="wrap">

<!-- =========================== TAB 1: SIZING =========================== -->
<div class="tabpanel active" id="sizing">
  <div class="card">
    <h2>Inputs</h2>
    <div class="row">
      <label>Service</label>
      <select id="svc" onchange="toggleService()">
        <option value="liquid">Liquid</option>
        <option value="gas">Gas / Vapour</option>
      </select>
      <span class="unit"></span>
    </div>
    <div class="row">
      <label>Valve type (sets default FL / xT)</label>
      <select id="presetSel" onchange="applyPreset()">
        <option value="globe1">Globe - Single Seat (FL 0.90, xT 0.72)</option>
        <option value="globe2">Globe - Cage/Double Seat (FL 0.85, xT 0.65)</option>
        <option value="butterfly">Butterfly (FL 0.68, xT 0.35)</option>
        <option value="ball">Segmented Ball (FL 0.55, xT 0.25)</option>
      </select>
      <span class="unit"></span>
    </div>
    <div class="grid">
      <div>
        <div class="row" id="liqFlowRow">
          <label>Flow rate, Q</label>
          <input type="number" id="Q" value="50">
          <select id="Qunit" style="flex:0 0 80px;"><option value="m3h">m3/hr</option><option value="kgh">kg/hr</option></select>
        </div>
        <div class="row" id="gasFlowRow" style="display:none;">
          <label>Mass flow, w</label>
          <input type="number" id="w" value="2000">
          <span class="unit">kg/hr</span>
        </div>
        <div class="row">
          <label>Inlet pressure, P1 (abs)</label>
          <input type="number" id="P1" value="10">
          <span class="unit">kg/cm2</span>
        </div>
        <div class="row">
          <label>Outlet pressure, P2 (abs)</label>
          <input type="number" id="P2" value="6">
          <span class="unit">kg/cm2</span>
        </div>
        <div class="row">
          <label>Inlet temperature, T1</label>
          <input type="number" id="T1" value="60">
          <span class="unit">DegC</span>
        </div>
        <div class="row">
          <label>Flowing density, &rho;1</label>
          <input type="number" id="dens" value="900">
          <span class="unit">kg/m3</span>
        </div>
      </div>
      <div>
        <div id="liqExtra">
          <div class="row"><label>Vapour pressure, Pv (abs)</label><input type="number" id="Pv" value="0"><span class="unit">kg/cm2</span></div>
          <div class="row"><label>Critical pressure, Pc (abs)</label><input type="number" id="Pc" value="225"><span class="unit">kg/cm2</span></div>
          <div class="row"><label>FL - recovery factor</label><input type="number" id="FL" value="0.9" step="0.01"><span class="unit"></span></div>
        </div>
        <div id="gasExtra" style="display:none;">
          <div class="row"><label>k = Cp/Cv (specific heat ratio)</label><input type="number" id="kk" value="1.4" step="0.01"><span class="unit"></span></div>
          <div class="row"><label>xT - pressure drop ratio factor</label><input type="number" id="xT" value="0.72" step="0.01"><span class="unit"></span></div>
        </div>
        <div class="note">Pv/Pc only matter for liquids near flashing/cavitation. For light hydrocarbon liquids use appropriate Pv, Pc at T1. Defaults shown are indicative for water &ndash; override for actual fluid.</div>
      </div>
    </div>
    <div class="btnbar">
      <button class="act" onclick="calcSizing()">Calculate CV</button>
      <button class="act secondary" onclick="window.print()">Print (A4)</button>
    </div>
  </div>

  <div class="card" id="sizingResultCard" style="display:none;">
    <h2>Results</h2>
    <table class="result-table" id="sizingResults"></table>
    <div class="note" id="sizingNote"></div>
  </div>

  <div class="card" id="flashCard" style="display:none;">
    <h2>Optional: Flashing Across the Valve (liquid inlet &rarr; vapour forms at/after vena contracta, e.g. JT / hot condensate letdown)</h2>
    <div class="note">The required Cv above already accounts for choked/flashing flow via FL and FF (dP is capped at the choked limit). Use this box only to <b>estimate how much vapour forms downstream</b> &ndash; useful for outlet piping, knock-out drum sizing, noise/erosion risk and trim selection. Leave blank to skip.</div>
    <div class="grid" style="margin-top:6px;">
      <div>
        <div class="row"><label>Saturation temp at P2, Tsat2</label><input type="number" id="Tsat2"><span class="unit">DegC</span></div>
        <div class="row"><label>Liquid Cp</label><input type="number" id="CpL" step="0.01"><span class="unit">kJ/kg.K</span></div>
      </div>
      <div>
        <div class="row"><label>Latent heat at P2, hfg2</label><input type="number" id="hfg2"><span class="unit">kJ/kg</span></div>
        <div class="row"><label>&nbsp;</label><button class="act" style="flex:1;" onclick="calcFlash()">Estimate Flash %</button></div>
      </div>
    </div>
    <div class="note" id="flashResult" style="font-weight:bold;"></div>
  </div>
</div>

<!-- =========================== TAB: TWO-PHASE INLET =========================== -->
<div class="tabpanel" id="twophase">
  <div class="card">
    <h2>Two-Phase (Liquid + Vapour) Inlet Sizing &ndash; Homogeneous (No-Slip) Method</h2>
    <div class="note">For a valve where the inlet stream is <b>already</b> a liquid/vapour mixture (e.g. flashing feed from upstream equipment, wet steam, two-phase blowdown). Uses the homogeneous no-slip volumetric method (mass-weighted mixture density from actual inlet volumes) &ndash; a standard simplified engineering approach (Baumann-type). This is approximate: it assumes no slip between phases and does not capture true flashing critical (choked) two-phase flow as rigorously as vendor two-phase sizing software. For high dP ratio, high quality, or erosion-critical service, verify with vendor software (e.g. Fisher FlowSizer, Masoneilan MASSIZE) and consider anti-cavitation / multi-stage trim.</div>
    <div class="grid" style="margin-top:8px;">
      <div>
        <div class="row"><label>Liquid mass flow, wL</label><input type="number" id="wL" value="8000"><span class="unit">kg/hr</span></div>
        <div class="row"><label>Vapour mass flow, wV</label><input type="number" id="wV" value="500"><span class="unit">kg/hr</span></div>
        <div class="row"><label>Liquid density at inlet, &rho;L</label><input type="number" id="rhoL2" value="900"><span class="unit">kg/m3</span></div>
        <div class="row"><label>Vapour density at inlet, &rho;V</label><input type="number" id="rhoV2" value="12"><span class="unit">kg/m3</span></div>
      </div>
      <div>
        <div class="row"><label>Inlet pressure, P1 (abs)</label><input type="number" id="P1tp" value="10"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Outlet pressure, P2 (abs)</label><input type="number" id="P2tp" value="6"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Liquid vapour pressure, Pv (abs)</label><input type="number" id="Pvtp" value="0"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Critical pressure, Pc (abs)</label><input type="number" id="Pctp" value="225"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>FL - recovery factor</label><input type="number" id="FLtp" value="0.9" step="0.01"></div>
      </div>
    </div>
    <div class="btnbar">
      <button class="act" onclick="calcTwoPhase()">Calculate CV (Two-Phase)</button>
      <button class="act secondary" onclick="window.print()">Print (A4)</button>
    </div>
  </div>
  <div class="card" id="tpResultCard" style="display:none;">
    <h2>Results</h2>
    <table class="result-table" id="tpResults"></table>
    <div class="note" id="tpNote"></div>
  </div>
</div>

<!-- =========================== TAB 2: RATING =========================== -->
<div class="tabpanel" id="rating">
  <div class="card">
    <h2>Existing / Selected Valve Rating &ndash; Predict Flow at Given Conditions</h2>
    <div class="row"><label>Service</label>
      <select id="svcR" onchange="toggleServiceR()"><option value="liquid">Liquid</option><option value="gas">Gas / Vapour</option></select>
    </div>
    <div class="grid">
      <div>
        <div class="row"><label>Rated Cv (100% open, from datasheet)</label><input type="number" id="CvMaxR" value="60"></div>
        <div class="row"><label>Present opening / travel</label><input type="number" id="hR" value="70"><span class="unit">%</span></div>
        <div class="row"><label>Characteristic</label>
          <select id="charR">
            <option value="linear">Linear</option>
            <option value="eq" selected>Equal % (default)</option>
            <option value="quick">Quick opening</option>
          </select>
        </div>
        <div class="row"><label>Rangeability, R (equal % only)</label><input type="number" id="Rrange" value="50"></div>
      </div>
      <div>
        <div class="row"><label>Inlet pressure, P1 (abs)</label><input type="number" id="P1R" value="10"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Outlet pressure, P2 (abs)</label><input type="number" id="P2R" value="6"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Flowing density, &rho;1</label><input type="number" id="densR" value="900"><span class="unit">kg/m3</span></div>
        <div class="row" id="rowFLR"><label>FL</label><input type="number" id="FLR" value="0.9" step="0.01"></div>
        <div class="row" id="rowGasR" style="display:none;">
          <label>k = Cp/Cv &nbsp;/&nbsp; xT</label>
          <input type="number" id="kkR" value="1.4" step="0.01" style="flex:0 0 60px;">
          <input type="number" id="xTR" value="0.72" step="0.01" style="flex:0 0 60px;">
        </div>
      </div>
    </div>
    <div class="btnbar">
      <button class="act" onclick="calcRating()">Calculate Flow</button>
      <button class="act secondary" onclick="window.print()">Print (A4)</button>
    </div>
  </div>
  <div class="card" id="ratingResultCard" style="display:none;">
    <h2>Results</h2>
    <table class="result-table" id="ratingResults"></table>
    <div class="note" id="ratingNote"></div>
  </div>
</div>

<!-- =========================== TAB 3: OPENING vs FLOW =========================== -->
<div class="tabpanel" id="opening">
  <div class="card">
    <h2>Valve Opening (%) vs Flow &ndash; Constant &Delta;P</h2>
    <div class="grid">
      <div>
        <div class="row"><label>Service</label>
          <select id="svcO" onchange="toggleServiceO()"><option value="liquid">Liquid</option><option value="gas">Gas / Vapour</option></select>
        </div>
        <div class="row"><label>Rated Cv (100% open)</label><input type="number" id="CvMaxO" value="60"></div>
        <div class="row"><label>Inlet pressure, P1 (abs)</label><input type="number" id="P1O" value="10"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Outlet pressure, P2 (abs) &ndash; held constant</label><input type="number" id="P2O" value="6"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Flowing density, &rho;1</label><input type="number" id="densO" value="900"><span class="unit">kg/m3</span></div>
        <div class="row" id="rowFLO"><label>FL</label><input type="number" id="FLO" value="0.9" step="0.01"></div>
        <div class="row" id="rowGasO" style="display:none;">
          <label>k &nbsp;/&nbsp; xT</label>
          <input type="number" id="kkO" value="1.4" step="0.01" style="flex:0 0 60px;">
          <input type="number" id="xTO" value="0.72" step="0.01" style="flex:0 0 60px;">
        </div>
      </div>
      <div>
        <div class="row"><label>Rangeability, R (equal % only)</label><input type="number" id="RrangeO" value="50"></div>
        <div class="charwrap no-print">
          <button id="cb-linear" onclick="setCharO('linear')">Linear</button>
          <button id="cb-eq" class="sel" onclick="setCharO('eq')">Equal %</button>
          <button id="cb-quick" onclick="setCharO('quick')">Quick Opening</button>
        </div>
        <div class="note">Assumes upstream/downstream pressure (and hence &Delta;P) stays constant as the valve travels &ndash; i.e. system resistance elsewhere is negligible. Flow is scaled directly from Cv(h)/Cv(max).</div>
      </div>
    </div>
    <div class="btnbar">
      <button class="act" onclick="calcOpening()">Generate Curve</button>
      <button class="act secondary" onclick="window.print()">Print (A4)</button>
    </div>
  </div>
  <div class="card" id="openingResultCard" style="display:none;">
    <h2>Opening % vs Flow</h2>
    <canvas id="chart" width="760" height="260"></canvas>
    <table class="curve" id="curveTable"></table>
    <div class="note" id="openingNote"></div>
  </div>
</div>

<!-- =========================== TAB 5: FIT FROM FIELD DATA =========================== -->
<div class="tabpanel" id="fielddata">
  <div class="card">
    <h2>Evaluate Flow from Existing Field Data &mdash; Curve Fit &amp; Interpolation</h2>
    <div class="note">Enter known (opening %, flow) data points from an existing valve. The tool fits the data to determine the rated Cv and characteristic parameters, then predicts flow at any opening. Useful for diagnosing installed characteristics, verifying as-built performance, or predicting flow at untested openings. Supports <b>Equal Percentage</b> and <b>Quick Opening</b> (and Linear).</div>
    
    <div class="grid" style="margin-top:8px;">
      <div>
        <div class="row"><label>Service</label>
          <select id="svcF" onchange="toggleServiceF()"><option value="liquid">Liquid</option><option value="gas">Gas / Vapour</option></select>
        </div>
        <div class="row"><label>Inlet pressure, P1 (abs)</label><input type="number" id="P1F" value="10"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Outlet pressure, P2 (abs)</label><input type="number" id="P2F" value="6"><span class="unit">kg/cm2</span></div>
        <div class="row"><label>Flowing density, &rho;1</label><input type="number" id="densF" value="900"><span class="unit">kg/m3</span></div>
        <div class="row" id="rowFLF"><label>FL</label><input type="number" id="FLF" value="0.9" step="0.01"></div>
        <div class="row" id="rowGasF" style="display:none;">
          <label>k &nbsp;/&nbsp; xT</label>
          <input type="number" id="kkF" value="1.4" step="0.01" style="flex:0 0 60px;">
          <input type="number" id="xTF" value="0.72" step="0.01" style="flex:0 0 60px;">
        </div>
      </div>
      <div>
        <div class="row"><label>Assumed characteristic</label>
          <select id="charF">
            <option value="eq" selected>Equal Percentage</option>
            <option value="quick">Quick Opening</option>
            <option value="linear">Linear</option>
          </select>
        </div>
        <div class="row"><label>Flow unit</label>
          <select id="flowUnitF" onchange="updateFlowUnitsF()">
            <option value="m3h">m3/hr</option>
            <option value="kgh">kg/hr</option>
          </select>
        </div>
        <div class="note">Enter at least 2 data points. For Equal %, at least 3 points spanning a wide range (e.g. 20&ndash;80%) give a more reliable R estimate. For Quick Opening, low-opening points improve the fit.</div>
      </div>
    </div>
    
    <h3 style="font-size:13px; margin:10px 0 6px; color:var(--navy);">Field Data Points (Opening % &rarr; Flow)</h3>
    <div id="fieldDataRows">
      <!-- Dynamic rows -->
    </div>
    <div class="btnbar no-print">
      <button class="act secondary" onclick="addFieldRow()">+ Add Data Point</button>
      <button class="act secondary" onclick="removeFieldRow()">&minus; Remove Last</button>
    </div>
    
    <div class="btnbar">
      <button class="act" onclick="calcFieldFit()">Fit Curve &amp; Predict</button>
      <button class="act secondary" onclick="window.print()">Print (A4)</button>
    </div>
  </div>
  
  <div class="card" id="fieldResultCard" style="display:none;">
    <h2>Fitted Parameters &amp; Predicted Curve</h2>
    <table class="result-table" id="fieldResults"></table>
    <h3 style="font-size:13px; margin:14px 0 6px; color:var(--navy);">Predicted Flow vs Opening</h3>
    <canvas id="fieldChart" width="760" height="260"></canvas>
    <table class="curve" id="fieldCurveTable"></table>
    <div class="note" id="fieldNote"></div>
  </div>
</div>
<footer class="no-print">
  <h3>Developer Information</h3>
  <p><strong>Gajanand Yadav</strong></p>
  <p>Chemical Engineer, IIT Guwahati</p>
  <p>Email: <a href="mailto:gajanandiitg@gmail.com">gajanandiitg@gmail.com</a> |
     Mobile: <a href="tel:+918369354472">+91-8369354472</a></p>
  <p>For property calculation, Density,Cp, saturation condition visit below link</p>
  <a href="https://gajuiitg.github.io/Thermocal/">Clickable Here</a>
</footer>
</div>

<script>
const presets = {
  globe1:{FL:0.9, xT:0.72}, globe2:{FL:0.85, xT:0.65}, butterfly:{FL:0.68, xT:0.35}, ball:{FL:0.55, xT:0.25}
};
const stdCv = [
  {size:'1/2"',cv:4},{size:'3/4"',cv:8},{size:'1"',cv:13},{size:'1.5"',cv:30},
  {size:'2"',cv:50},{size:'3"',cv:110},{size:'4"',cv:190},{size:'6"',cv:400},{size:'8"',cv:700}
];
const KB = 0.980665; // kg/cm2 -> bar
const N6 = 2.73; // IEC 60534-2-1 Table 1: w kg/h, P/dP in bar, rho kg/m3

function showTab(id, btn){
  document.querySelectorAll('.tabpanel').forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('.tabs button').forEach(b=>b.classList.remove('active'));
  if(btn) btn.classList.add('active');
}
function toggleService(){
  const g = document.getElementById('svc').value;
  document.getElementById('liqFlowRow').style.display = g==='liquid'?'flex':'none';
  document.getElementById('gasFlowRow').style.display = g==='gas'?'flex':'none';
  document.getElementById('liqExtra').style.display = g==='liquid'?'block':'none';
  document.getElementById('gasExtra').style.display = g==='gas'?'block':'none';
  document.getElementById('flashCard').style.display = g==='liquid'?'block':'none';
}
function applyPreset(){
  const p = presets[document.getElementById('presetSel').value];
  document.getElementById('FL').value = p.FL;
  document.getElementById('xT').value = p.xT;
}
function toggleServiceR(){
  const g = document.getElementById('svcR').value;
  document.getElementById('rowFLR').style.display = g==='liquid'?'flex':'none';
  document.getElementById('rowGasR').style.display = g==='gas'?'flex':'none';
}
function toggleServiceO(){
  const g = document.getElementById('svcO').value;
  document.getElementById('rowFLO').style.display = g==='liquid'?'flex':'none';
  document.getElementById('rowGasO').style.display = g==='gas'?'flex':'none';
}
let charO = 'eq';
function setCharO(c){
  charO=c;
  ['linear','eq','quick'].forEach(k=>document.getElementById('cb-'+k).classList.toggle('sel', k===c));
}

function sizeLiquid(Qm3h, P1, P2, dens, Pv, Pc, FL){
  const P1b=P1*KB, P2b=P2*KB, Pvb=Math.max(Pv,0)*KB, Pcb=Pc*KB;
  const dPb = P1b-P2b;
  const FF = 0.96-0.28*Math.sqrt(Math.max(Pvb,0)/Pcb);
  const dPchoked = FL*FL*(P1b-FF*Pvb);
  const choked = dPb > dPchoked;
  const dPeff = choked? dPchoked : dPb;
  const Gf = dens/1000;
  const Cv = Qm3h/(0.865*Math.sqrt(Math.max(dPeff,1e-9)/Gf));
  return {dPb,dPchoked,choked,Cv,FF,dPeff,Gf};
}
function sizeGas(w,P1,P2,dens,k,xT){
  const P1b=P1*KB, P2b=P2*KB;
  const x=(P1b-P2b)/P1b;
  const Fk=k/1.4;
  const xchoked=Fk*xT;
  const choked = x>xchoked;
  const xeff = choked? xchoked : x;
  let Y = 1-xeff/(3*Fk*xT);
  if(Y<0.667) Y=0.667;
  const Cv = w/(N6*Y*Math.sqrt(Math.max(xeff*P1b*dens,1e-9)));
  return {x,xchoked,choked,Y,Cv,xeff,P1b};
}
function suggestValve(cvReq){
  for(const row of stdCv){
    const pct = cvReq/row.cv*100;
    if(pct<=85) return {...row, pct};
  }
  const last = stdCv[stdCv.length-1];
  return {...last, pct:cvReq/last.cv*100, oversize:true};
}

function calcSizing(){
  const svc = document.getElementById('svc').value;
  const P1 = parseFloat(document.getElementById('P1').value);
  const P2 = parseFloat(document.getElementById('P2').value);
  const dens = parseFloat(document.getElementById('dens').value);
  const T1 = parseFloat(document.getElementById('T1').value);
  let rows = [];
  let cvReq, choked, extraNote='';
  if(svc==='liquid'){
    let Q = parseFloat(document.getElementById('Q').value);
    const unit = document.getElementById('Qunit').value;
    const Qm3h = unit==='m3h'? Q : Q/dens;
    const Pv = parseFloat(document.getElementById('Pv').value);
    const Pc = parseFloat(document.getElementById('Pc').value);
    const FL = parseFloat(document.getElementById('FL').value);
    const r = sizeLiquid(Qm3h,P1,P2,dens,Pv,Pc,FL);
    cvReq = r.Cv; choked = r.choked;
    rows.push(['Service','Liquid']);
    rows.push(['Volumetric flow used, Qm3h', Qm3h.toFixed(2)+' m3/hr']);
    rows.push(['Mass flow (equiv.)', (Qm3h*dens).toFixed(0)+' kg/hr']);
    rows.push(['&Delta;P actual', r.dPb.toFixed(3)+' bar ('+(r.dPb/KB).toFixed(2)+' kg/cm2)']);
    rows.push(['FF (liquid crit. pressure ratio)', r.FF.toFixed(3)]);
    rows.push(['&Delta;P choked limit (FL&sup2;(P1-FF&middot;Pv))', r.dPchoked.toFixed(3)+' bar']);
    rows.push(['Choked flow?', choked? 'badge-bad:YES - flow limited, valve cannot pass more at this dP' : 'badge-ok:No - normal (non-choked) flow']);
    rows.push(['Specific gravity, Gf', r.Gf.toFixed(3)]);
  } else {
    const w = parseFloat(document.getElementById('w').value);
    const k = parseFloat(document.getElementById('kk').value);
    const xT = parseFloat(document.getElementById('xT').value);
    const r = sizeGas(w,P1,P2,dens,k,xT);
    cvReq = r.Cv; choked = r.choked;
    rows.push(['Service','Gas / Vapour']);
    rows.push(['Pressure drop ratio, x = &Delta;P/P1', r.x.toFixed(3)]);
    rows.push(['x choked (Fk&middot;xT)', r.xchoked.toFixed(3)]);
    rows.push(['Choked (critical) flow?', choked? 'badge-bad:YES - flow limited (velocity limited at vena contracta)' : 'badge-ok:No - normal (sub-critical) flow']);
    rows.push(['Expansion factor, Y', r.Y.toFixed(3)]);
  }
  const sug = suggestValve(cvReq);
  rows.push(['<b>Required Cv</b>', '<b>'+cvReq.toFixed(2)+'</b>']);
  rows.push(['Suggested valve size (typ. globe, verify with vendor)', sug.size+'  (rated Cv &asymp; '+sug.cv+')']);
  rows.push(['Estimated opening at this flow', sug.pct.toFixed(0)+'% '+(sug.oversize? ' (valve oversized - consider reduced trim)':(sug.pct>85?' (near full open - check next size up)':(sug.pct<20?' (low opening - consider smaller trim)':' (good, in 20-85% control range)')))]);

  let charSuggestion;
  if(svc==='gas' && choked){
    charSuggestion = 'Equal % is generally preferred where the valve regularly sees choked / high pressure-drop-ratio conditions, for smoother control near the choke point.';
  } else {
    charSuggestion = 'Equal % characteristic is the usual default for control loops where system &Delta;P varies with flow (most process control). Use Linear where &Delta;P across the valve stays essentially constant (e.g. some level control). Quick-opening is for on/off or interlock service, not continuous control.';
  }
  rows.push(['Recommended inherent characteristic', charSuggestion]);

  renderTable('sizingResults', rows);
  document.getElementById('sizingResultCard').style.display='block';
  document.getElementById('sizingNote').innerHTML =
    'Basis: IEC 60534-2-1 / ISA 75.01.01 sizing equations, MKS units (kg/cm2 abs, kg/hr, m3/hr, kg/m3). '+
    'N1=0.865 (Q m3/hr, &Delta;P bar), N6=2.73 (w kg/hr, P bar, &rho; kg/m3). Standard Cv table is indicative (globe valve, generic) &ndash; always confirm against manufacturer catalogue. '+
    'Ensure P1/P2 are entered as absolute pressure (add ~1.033 kg/cm2 if gauge).';
}

function calcFlash(){
  const Tsat2 = parseFloat(document.getElementById('Tsat2').value);
  const Cp = parseFloat(document.getElementById('CpL').value);
  const hfg = parseFloat(document.getElementById('hfg2').value);
  const T1 = parseFloat(document.getElementById('T1').value);
  const el = document.getElementById('flashResult');
  if(isNaN(Tsat2)||isNaN(Cp)||isNaN(hfg)||isNaN(T1)||hfg<=0){
    el.innerHTML = 'Enter T1 (in the Inputs above), Tsat2, Cp and hfg2 to estimate flash %.';
    return;
  }
  let xflash = Cp*(T1-Tsat2)/hfg;
  if(xflash<0) xflash=0;
  if(xflash>1) xflash=1;
  el.innerHTML = 'Estimated outlet vapour (flash) fraction &asymp; <span style="color:var(--navy);">'+(xflash*100).toFixed(1)+'% by mass</span>'+
    (xflash<=0? ' (T1 below Tsat at P2 &ndash; liquid stays sub-cooled, negligible flashing).' :
    (xflash>0.15? ' &ndash; significant flashing: check for choked flow above, consider anti-cavitation / hardened multi-stage trim, and size downstream piping / KO drum for two-phase flow.' : ' &ndash; moderate flashing, standard trim usually acceptable if not choked.'));
}

function calcTwoPhase(){
  const wL = parseFloat(document.getElementById('wL').value);
  const wV = parseFloat(document.getElementById('wV').value);
  const rhoL = parseFloat(document.getElementById('rhoL2').value);
  const rhoV = parseFloat(document.getElementById('rhoV2').value);
  const P1 = parseFloat(document.getElementById('P1tp').value);
  const P2 = parseFloat(document.getElementById('P2tp').value);
  const Pv = parseFloat(document.getElementById('Pvtp').value);
  const Pc = parseFloat(document.getElementById('Pctp').value);
  const FL = parseFloat(document.getElementById('FLtp').value);

  const wTotal = wL+wV;
  const xg = wV/wTotal;
  const QL = wL/rhoL, QV = wV/rhoV;
  const Qtotal = QL+QV;
  const rhoM = wTotal/Qtotal;
  const GfM = rhoM/1000;

  const P1b=P1*KB, P2b=P2*KB, Pvb=Math.max(Pv,0)*KB, Pcb=Pc*KB;
  const dPb = P1b-P2b;
  const FF = 0.96-0.28*Math.sqrt(Math.max(Pvb,0)/Pcb);
  const dPchoked = FL*FL*(P1b-FF*Pvb);
  const choked = dPb>dPchoked;
  const dPeff = choked? dPchoked : dPb;

  const Cv = Qtotal/(0.865*Math.sqrt(Math.max(dPeff,1e-9)/GfM));
  const sug = suggestValve(Cv);

  let rows=[];
  rows.push(['Mass vapour fraction (quality) at inlet', (xg*100).toFixed(1)+'%']);
  rows.push(['Liquid volumetric flow, QL', QL.toFixed(2)+' m3/hr']);
  rows.push(['Vapour volumetric flow, QV', QV.toFixed(2)+' m3/hr']);
  rows.push(['Total actual volumetric flow, Qtotal', Qtotal.toFixed(2)+' m3/hr']);
  rows.push(['Homogeneous mixture density, &rho;m', rhoM.toFixed(1)+' kg/m3']);
  rows.push(['&Delta;P actual', dPb.toFixed(3)+' bar']);
  rows.push(['&Delta;P choked limit', dPchoked.toFixed(3)+' bar']);
  rows.push(['Choked / critical two-phase flow?', choked? 'badge-bad:YES - choked, expect noise/erosion risk' : 'badge-ok:No - normal flow']);
  rows.push(['<b>Required Cv (two-phase, homogeneous method)</b>', '<b>'+Cv.toFixed(2)+'</b>']);
  rows.push(['Suggested valve size (typ. globe, verify with vendor)', sug.size+'  (rated Cv &asymp; '+sug.cv+')']);
  rows.push(['Estimated opening at this flow', sug.pct.toFixed(0)+'%']);
  rows.push(['Recommended trim', choked || xg>0.05 ? 'Anti-cavitation / erosion-resistant multi-stage trim recommended given choked and/or vapour-bearing service.' : 'Standard trim likely acceptable; confirm against velocity/erosion limits for the vapour phase at the outlet.']);

  renderTable('tpResults', rows);
  document.getElementById('tpResultCard').style.display='block';
  document.getElementById('tpNote').innerHTML = 'Homogeneous (no-slip) method: mixture density is the mass-weighted density from actual inlet volumetric flows of each phase. Choked-flow check reuses the liquid FL/FF logic as an approximation (governs most flashing two-phase choking). For critical service (relief, high erosion risk, high quality), use rigorous two-phase methods (Baumann, DIERS/Omega, or vendor 2-phase sizing software).';
}

function calcRating(){
  const svc = document.getElementById('svcR').value;
  const CvMax = parseFloat(document.getElementById('CvMaxR').value);
  const hpct = parseFloat(document.getElementById('hR').value);
  const h = Math.max(hpct,0.0001)/100;
  const charType = document.getElementById('charR').value;
  const R = parseFloat(document.getElementById('Rrange').value);
  const P1 = parseFloat(document.getElementById('P1R').value);
  const P2 = parseFloat(document.getElementById('P2R').value);
  const dens = parseFloat(document.getElementById('densR').value);

  let Cv_h;
  if(charType==='linear') Cv_h = CvMax*h;
  else if(charType==='eq') Cv_h = CvMax*Math.pow(R, h-1);
  else Cv_h = CvMax*Math.sqrt(h);

  let rows=[];
  rows.push(['Rated Cv (100%)', CvMax.toFixed(2)]);
  rows.push(['Opening', hpct.toFixed(0)+'%']);
  rows.push(['Effective Cv at this opening', Cv_h.toFixed(2)]);

  if(svc==='liquid'){
    const Pv=0, Pc=225;
    const FL = parseFloat(document.getElementById('FLR').value);
    const P1b=P1*KB, P2b=P2*KB;
    const dPb=P1b-P2b;
    const dPchoked = FL*FL*P1b; // Pv=0 assumption for rating quick-check
    const choked = dPb>dPchoked;
    const dPeff = choked?dPchoked:dPb;
    const Gf = dens/1000;
    const Qm3h = Cv_h*0.865*Math.sqrt(Math.max(dPeff,0)/Gf);
    rows.push(['&Delta;P actual / choked limit', dPb.toFixed(3)+' / '+dPchoked.toFixed(3)+' bar']);
    rows.push(['Choked?', choked?'badge-bad:YES':'badge-ok:No']);
    rows.push(['<b>Predicted flow</b>', '<b>'+Qm3h.toFixed(2)+' m3/hr  ('+(Qm3h*dens).toFixed(0)+' kg/hr)</b>']);
  } else {
    const k = parseFloat(document.getElementById('kkR').value);
    const xT = parseFloat(document.getElementById('xTR').value);
    const P1b=P1*KB, P2b=P2*KB;
    const x=(P1b-P2b)/P1b;
    const Fk=k/1.4;
    const xchoked=Fk*xT;
    const choked = x>xchoked;
    const xeff = choked?xchoked:x;
    let Y=1-xeff/(3*Fk*xT); if(Y<0.667)Y=0.667;
    const w = Cv_h*N6*Y*Math.sqrt(Math.max(xeff*P1b*dens,0));
    rows.push(['x / x choked', x.toFixed(3)+' / '+xchoked.toFixed(3)]);
    rows.push(['Choked?', choked?'badge-bad:YES':'badge-ok:No']);
    rows.push(['Expansion factor Y', Y.toFixed(3)]);
    rows.push(['<b>Predicted mass flow</b>', '<b>'+w.toFixed(0)+' kg/hr</b>']);
  }
  renderTable('ratingResults', rows);
  document.getElementById('ratingResultCard').style.display='block';
  document.getElementById('ratingNote').innerHTML = 'Enter the valve\'s catalogue rated Cv (100% travel) and current opening/travel % to back-calculate the flow this valve will pass at the stated P1/P2/density.';
}

function cvAtH(h, CvMax, charType, R){
  if(charType==='linear') return CvMax*Math.max(h,0);
  if(charType==='eq') return CvMax*Math.pow(R, Math.max(h,0.0001)-1);
  return CvMax*Math.sqrt(Math.max(h,0));
}

function calcOpening(){
  const svc = document.getElementById('svcO').value;
  const CvMax = parseFloat(document.getElementById('CvMaxO').value);
  const P1 = parseFloat(document.getElementById('P1O').value);
  const P2 = parseFloat(document.getElementById('P2O').value);
  const dens = parseFloat(document.getElementById('densO').value);
  const R = parseFloat(document.getElementById('RrangeO').value);

  let choked=false, dPeff, Gf, Y, xeff, P1b;
  if(svc==='liquid'){
    const FL = parseFloat(document.getElementById('FLO').value);
    P1b=P1*KB; const P2b=P2*KB;
    const dPb=P1b-P2b;
    const dPchoked = FL*FL*P1b;
    choked = dPb>dPchoked;
    dPeff = choked?dPchoked:dPb;
    Gf = dens/1000;
  } else {
    const k = parseFloat(document.getElementById('kkO').value);
    const xT = parseFloat(document.getElementById('xTO').value);
    P1b=P1*KB; const P2b=P2*KB;
    const x=(P1b-P2b)/P1b;
    const Fk=k/1.4;
    const xchoked=Fk*xT;
    choked = x>xchoked;
    xeff = choked?xchoked:x;
    Y=1-xeff/(3*Fk*xT); if(Y<0.667)Y=0.667;
  }

  let pts=[];
  for(let pct=0; pct<=100; pct+=10){
    const h = pct/100;
    const Cv_h = cvAtH(h, CvMax, charO, R);
    let flow;
    if(svc==='liquid'){
      flow = Cv_h*0.865*Math.sqrt(Math.max(dPeff,0)/Gf); // m3/hr
    } else {
      flow = Cv_h*N6*Y*Math.sqrt(Math.max(xeff*P1b*dens,0)); // kg/hr
    }
    pts.push({pct, Cv_h, flow});
  }

  // table
  let html = '<tr><th>Opening %</th>'+pts.map(p=>`<th>${p.pct}</th>`).join('')+'</tr>';
  html += '<tr><td>Cv</td>'+pts.map(p=>`<td>${p.Cv_h.toFixed(1)}</td>`).join('')+'</tr>';
  html += `<tr><td>Flow (${svc==='liquid'?'m3/hr':'kg/hr'})</td>`+pts.map(p=>`<td>${p.flow.toFixed(svc==='liquid'?1:0)}</td>`).join('')+'</tr>';
  document.getElementById('curveTable').innerHTML = html;

  drawChart(pts, svc);
  document.getElementById('openingResultCard').style.display='block';
  document.getElementById('openingNote').innerHTML =
    'Characteristic: '+({linear:'Linear',eq:'Equal %',quick:'Quick opening'}[charO])+'. &Delta;P held constant at P1='+P1+' kg/cm2 a, P2='+P2+' kg/cm2 a. '+
    (choked? '<span style="color:#a4161a;font-weight:bold;">Note: flow is choked at these conditions &ndash; curve reflects choked (limited) flow throughout.</span>':'Flow is in the normal (non-choked) region across the travel range.');
}

function drawChart(pts, svc){
  const c = document.getElementById('chart');
  const ctx = c.getContext('2d');
  ctx.clearRect(0,0,c.width,c.height);
  const padL=55, padB=30, padT=15, padR=15;
  const w = c.width-padL-padR, h = c.height-padT-padB;
  const maxFlow = Math.max(...pts.map(p=>p.flow))*1.05 || 1;
  // axes
  ctx.strokeStyle='#334'; ctx.lineWidth=1;
  ctx.beginPath(); ctx.moveTo(padL,padT); ctx.lineTo(padL,padT+h); ctx.lineTo(padL+w,padT+h); ctx.stroke();
  ctx.fillStyle='#334'; ctx.font='11px Arial';
  // y labels
  for(let i=0;i<=5;i++){
    const val = maxFlow*i/5;
    const y = padT+h - (h*i/5);
    ctx.fillText(val.toFixed(0), 2, y+4);
    ctx.strokeStyle='#e2e8ee'; ctx.beginPath(); ctx.moveTo(padL,y); ctx.lineTo(padL+w,y); ctx.stroke();
  }
  ctx.fillStyle='#334';
  ctx.fillText(svc==='liquid'?'Flow (m3/hr)':'Flow (kg/hr)', padL, 10);
  // x labels
  pts.forEach(p=>{
    const x = padL + w*p.pct/100;
    ctx.fillText(p.pct+'%', x-10, padT+h+15);
  });
  ctx.fillText('Valve opening', padL+w/2-20, c.height-2);
  // line
  ctx.strokeStyle='#1b3a5c'; ctx.lineWidth=2; ctx.beginPath();
  pts.forEach((p,i)=>{
    const x = padL + w*p.pct/100;
    const y = padT+h - h*(p.flow/maxFlow);
    if(i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
  });
  ctx.stroke();
  ctx.fillStyle='#c0392b';
  pts.forEach(p=>{
    const x = padL + w*p.pct/100;
    const y = padT+h - h*(p.flow/maxFlow);
    ctx.beginPath(); ctx.arc(x,y,3,0,Math.PI*2); ctx.fill();
  });
}

function renderTable(id, rows){
  const el = document.getElementById(id);
  el.innerHTML = rows.map(r=>{
    let v = r[1];
    let cls='val';
    if(typeof v==='string' && v.startsWith('badge-ok:')){ v = '<span class="badge ok">'+v.replace('badge-ok:','')+'</span>'; }
    if(typeof v==='string' && v.startsWith('badge-bad:')){ v = '<span class="badge bad">'+v.replace('badge-bad:','')+'</span>'; }
    return `<tr><td>${r[0]}</td><td class="${cls}">${v}</td></tr>`;
  }).join('');
}

// =========================== FIELD DATA FIT FUNCTIONS ===========================
function toggleServiceF(){
  const g = document.getElementById('svcF').value;
  document.getElementById('rowFLF').style.display = g==='liquid'?'flex':'none';
  document.getElementById('rowGasF').style.display = g==='gas'?'flex':'none';
}

let fieldRowCount = 0;
function initFieldRows(){
  const container = document.getElementById('fieldDataRows');
  container.innerHTML = '';
  fieldRowCount = 0;
  // Add 4 default rows with sample data
  addFieldRow(20, 12);
  addFieldRow(40, 28);
  addFieldRow(60, 52);
  addFieldRow(80, 78);
}
function addFieldRow(opening='', flow=''){
  fieldRowCount++;
  const container = document.getElementById('fieldDataRows');
  const div = document.createElement('div');
  div.className = 'row';
  div.id = 'fieldRow'+fieldRowCount;
  div.innerHTML = `
    <label>Data point ${fieldRowCount}</label>
    <input type="number" id="fOpen${fieldRowCount}" value="${opening}" placeholder="Opening %" style="flex:0 0 80px;">
    <span class="unit">% &rarr;</span>
    <input type="number" id="fFlow${fieldRowCount}" value="${flow}" placeholder="Flow" style="flex:1;">
    <span class="unit" id="fFlowUnit${fieldRowCount}">m3/hr</span>
  `;
  container.appendChild(div);
  updateFlowUnitsF();
}
function removeFieldRow(){
  if(fieldRowCount<=2) return; // keep at least 2
  const row = document.getElementById('fieldRow'+fieldRowCount);
  if(row) row.remove();
  fieldRowCount--;
}
function updateFlowUnitsF(){
  const unit = document.getElementById('flowUnitF').value;
  for(let i=1;i<=fieldRowCount;i++){
    const el = document.getElementById('fFlowUnit'+i);
    if(el) el.textContent = unit==='m3h'?'m3/hr':'kg/hr';
  }
}

function calcFieldFit(){
  const svc = document.getElementById('svcF').value;
  const P1 = parseFloat(document.getElementById('P1F').value);
  const P2 = parseFloat(document.getElementById('P2F').value);
  const dens = parseFloat(document.getElementById('densF').value);
  const charType = document.getElementById('charF').value;
  const flowUnit = document.getElementById('flowUnitF').value;
  
  // Compute K factor (flow = Cv * K)
  let K, choked=false, dPeff, Gf, Y, xeff, P1b;
  if(svc==='liquid'){
    const FL = parseFloat(document.getElementById('FLF').value);
    P1b=P1*KB; const P2b=P2*KB;
    const dPb=P1b-P2b;
    const dPchoked = FL*FL*P1b;
    choked = dPb>dPchoked;
    dPeff = choked?dPchoked:dPb;
    Gf = dens/1000;
    K = 0.865*Math.sqrt(Math.max(dPeff,0)/Gf); // Q (m3/hr) = Cv * K
  } else {
    const k = parseFloat(document.getElementById('kkF').value);
    const xT = parseFloat(document.getElementById('xTF').value);
    P1b=P1*KB; const P2b=P2*KB;
    const x=(P1b-P2b)/P1b;
    const Fk=k/1.4;
    const xchoked=Fk*xT;
    choked = x>xchoked;
    xeff = choked?xchoked:x;
    Y=1-xeff/(3*Fk*xT); if(Y<0.667)Y=0.667;
    K = N6*Y*Math.sqrt(Math.max(xeff*P1b*dens,0)); // w (kg/hr) = Cv * K
  }
  
  // Collect data points
  let points = [];
  for(let i=1;i<=fieldRowCount;i++){
    const h = parseFloat(document.getElementById('fOpen'+i).value);
    const q = parseFloat(document.getElementById('fFlow'+i).value);
    if(!isNaN(h) && !isNaN(q) && h>0 && h<=100 && q>=0){
      let Qval = q;
      if(svc==='liquid' && flowUnit==='kgh') Qval = q/dens; // convert to m3/hr for Cv calc
      points.push({h: h/100, q: Qval, rawH: h, rawQ: q});
    }
  }
  
  if(points.length<2){
    alert('Enter at least 2 valid data points.');
    return;
  }
  
  // Fit characteristic
  let CvMax, R=50, r2=0;
  
  if(charType==='linear'){
    // Q = CvMax * h * K  =>  CvMax = mean(Q/(h*K))
    let sum=0, n=0;
    points.forEach(p=>{
      if(p.h>0){ sum += (p.q/(p.h*K)); n++; }
    });
    CvMax = n>0 ? sum/n : 0;
    // Compute R2 on original flow scale
    let ssRes=0, ssTot=0, meanQ=0;
    points.forEach(p=>meanQ+=p.q); meanQ/=points.length;
    points.forEach(p=>{
      const pred = CvMax*p.h*K;
      ssRes += Math.pow(p.q-pred,2);
      ssTot += Math.pow(p.q-meanQ,2);
    });
    r2 = ssTot>0 ? 1-ssRes/ssTot : 1;
  } else if(charType==='quick'){
    // Q = CvMax * sqrt(h) * K
    let sum=0, n=0;
    points.forEach(p=>{
      if(p.h>0){ sum += (p.q/(Math.sqrt(p.h)*K)); n++; }
    });
    CvMax = n>0 ? sum/n : 0;
    let ssRes=0, ssTot=0, meanQ=0;
    points.forEach(p=>meanQ+=p.q); meanQ/=points.length;
    points.forEach(p=>{
      const pred = CvMax*Math.sqrt(p.h)*K;
      ssRes += Math.pow(p.q-pred,2);
      ssTot += Math.pow(p.q-meanQ,2);
    });
    r2 = ssTot>0 ? 1-ssRes/ssTot : 1;
  } else { // equal percentage
    // Q = CvMax * R^(h-1) * K
    // ln(Q/K) = ln(CvMax) + (h-1)*ln(R) = [ln(CvMax)-ln(R)] + h*ln(R)
    // y = a + b*h, where a = ln(CvMax)-ln(R), b = ln(R)
    let sumX=0, sumY=0, sumXY=0, sumX2=0, n=0;
    points.forEach(p=>{
      if(p.q>0 && p.h>0){
        const y = Math.log(p.q/K);
        const x = p.h;
        sumX += x; sumY += y; sumXY += x*y; sumX2 += x*x; n++;
      }
    });
    if(n<2){ alert('Need at least 2 valid points for Equal % fit.'); return; }
    const b = (n*sumXY - sumX*sumY)/(n*sumX2 - sumX*sumX);
    const a = (sumY - b*sumX)/n;
    R = Math.exp(b);
    CvMax = Math.exp(a + b); // = exp(a)*exp(b)
    if(R<10) R=10; if(R>200) R=200; // sensible bounds
    
    // R2 on log scale
    let ssRes=0, ssTot=0, meanY=sumY/n;
    points.forEach(p=>{
      const y = Math.log(p.q/K);
      const pred = a + b*p.h;
      ssRes += Math.pow(y-pred,2);
      ssTot += Math.pow(y-meanY,2);
    });
    r2 = ssTot>0 ? 1-ssRes/ssTot : 1;
  }
  
  // Generate predictions 0-100%
  let predPts = [];
  for(let pct=0; pct<=100; pct+=10){
    const h = pct/100;
    let cvh;
    if(charType==='linear') cvh = CvMax*h;
    else if(charType==='quick') cvh = CvMax*Math.sqrt(h);
    else cvh = CvMax*Math.pow(R, h-1);
    let flow = cvh*K;
    if(svc==='liquid' && flowUnit==='kgh') flow *= dens;
    predPts.push({pct, flow, cvh});
  }
  
  // Build results table
  let rows=[];
  rows.push(['Service', svc==='liquid'?'Liquid':'Gas / Vapour']);
  rows.push(['Characteristic fitted', {linear:'Linear',eq:'Equal Percentage',quick:'Quick Opening'}[charType]]);
  rows.push(['Data points used', points.length]);
  rows.push(['R&sup2; (goodness of fit)', r2.toFixed(4)+' '+(r2>0.98?'(excellent)':r2>0.95?'(good)':r2>0.90?'(fair)':'(poor – check data or characteristic)')]);
  rows.push(['<b>Fitted rated Cv (100%)</b>', '<b>'+CvMax.toFixed(2)+'</b>']);
  if(charType==='eq') rows.push(['Fitted rangeability, R', R.toFixed(1)]);
  
  // Residuals
  rows.push(['&nbsp;','&nbsp;']);
  rows.push(['<b>Residuals (measured vs fitted)</b>','']);
  points.forEach(p=>{
    let cvh;
    if(charType==='linear') cvh = CvMax*p.h;
    else if(charType==='quick') cvh = CvMax*Math.sqrt(p.h);
    else cvh = CvMax*Math.pow(R, p.h-1);
    let predQ = cvh*K;
    if(svc==='liquid' && flowUnit==='kgh') predQ *= dens;
    const err = p.rawQ===0 ? 0 : ((p.rawQ-predQ)/p.rawQ*100);
    rows.push([`Opening ${p.rawH}%`, `Meas: ${p.rawQ.toFixed(2)} | Fitted: ${predQ.toFixed(2)} | Error: ${err>0?'+':''}${err.toFixed(1)}%`]);
  });
  
  renderTable('fieldResults', rows);
  
  // Prediction table
  let html = '<tr><th>Opening %</th>'+predPts.map(p=>`<th>${p.pct}</th>`).join('')+'</tr>';
  html += '<tr><td>Cv</td>'+predPts.map(p=>`<td>${p.cvh.toFixed(1)}</td>`).join('')+'</tr>';
  const flowLabel = svc==='liquid' ? (flowUnit==='m3h'?'Flow (m3/hr)':'Flow (kg/hr)') : 'Flow (kg/hr)';
  html += `<tr><td>${flowLabel}</td>`+predPts.map(p=>`<td>${p.flow.toFixed(svc==='liquid'&&flowUnit==='m3h'?1:0)}</td>`).join('')+'</tr>';
  document.getElementById('fieldCurveTable').innerHTML = html;
  
  drawFieldChart(points, predPts, svc, flowUnit);
  
  document.getElementById('fieldResultCard').style.display='block';
  document.getElementById('fieldNote').innerHTML = 
    'Fitted using least-squares regression on the inherent characteristic equation. '+
    'If R&sup2; is poor, the actual installed characteristic may differ from the assumed type (e.g. due to varying &Delta;P with flow in the system). '+
    'For Equal %, R is constrained to 10&ndash;200. Always verify fitted Cv against manufacturer datasheet.';
}

function drawFieldChart(measPts, predPts, svc, flowUnit){
  const c = document.getElementById('fieldChart');
  const ctx = c.getContext('2d');
  ctx.clearRect(0,0,c.width,c.height);
  const padL=55, padB=30, padT=15, padR=15;
  const w = c.width-padL-padR, h = c.height-padT-padB;
  
  const allFlows = [...measPts.map(p=>p.rawQ), ...predPts.map(p=>p.flow)];
  const maxFlow = Math.max(...allFlows)*1.1 || 1;
  const minFlow = Math.min(...allFlows)*0.9 || 0;
  const range = maxFlow - minFlow;
  
  // axes
  ctx.strokeStyle='#334'; ctx.lineWidth=1;
  ctx.beginPath(); ctx.moveTo(padL,padT); ctx.lineTo(padL,padT+h); ctx.lineTo(padL+w,padT+h); ctx.stroke();
  ctx.fillStyle='#334'; ctx.font='11px Arial';
  
  for(let i=0;i<=5;i++){
    const val = minFlow + range*i/5;
    const y = padT+h - (h*i/5);
    ctx.fillText(val.toFixed(svc==='liquid'&&flowUnit==='m3h'?1:0), 2, y+4);
    ctx.strokeStyle='#e2e8ee'; ctx.beginPath(); ctx.moveTo(padL,y); ctx.lineTo(padL+w,y); ctx.stroke();
  }
  
  const flowLabel = svc==='liquid' ? (flowUnit==='m3h'?'Flow (m3/hr)':'Flow (kg/hr)') : 'Flow (kg/hr)';
  ctx.fillStyle='#334';
  ctx.fillText(flowLabel, padL, 10);
  
  predPts.forEach(p=>{
    const x = padL + w*p.pct/100;
    ctx.fillText(p.pct+'%', x-10, padT+h+15);
  });
  ctx.fillText('Valve opening', padL+w/2-20, c.height-2);
  
  // Fitted curve
  ctx.strokeStyle='#1b3a5c'; ctx.lineWidth=2; ctx.beginPath();
  predPts.forEach((p,i)=>{
    const x = padL + w*p.pct/100;
    const y = padT+h - h*((p.flow-minFlow)/range);
    if(i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
  });
  ctx.stroke();
  
  // Predicted points (small blue dots)
  ctx.fillStyle='#2f6690';
  predPts.forEach(p=>{
    const x = padL + w*p.pct/100;
    const y = padT+h - h*((p.flow-minFlow)/range);
    ctx.beginPath(); ctx.arc(x,y,3,0,Math.PI*2); ctx.fill();
  });
  
  // Measured points (red circles with cross)
  measPts.forEach(p=>{
    const x = padL + w*p.rawH/100;
    const y = padT+h - h*((p.rawQ-minFlow)/range);
    
    // Red circle
    ctx.fillStyle='#c0392b';
    ctx.beginPath(); ctx.arc(x,y,6,0,Math.PI*2); ctx.fill();
    
    // White cross
    ctx.strokeStyle='#fff'; ctx.lineWidth=1.5;
    ctx.beginPath(); ctx.moveTo(x-3,y-3); ctx.lineTo(x+3,y+3); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(x+3,y-3); ctx.lineTo(x-3,y+3); ctx.stroke();
  });
  
  // Legend
  ctx.fillStyle='#334'; ctx.font='11px Arial';
  ctx.fillText('● Fitted curve', padL+w-140, padT+12);
  ctx.fillStyle='#c0392b'; ctx.beginPath(); ctx.arc(padL+w-55, padT+8, 4,0,Math.PI*2); ctx.fill();
  ctx.fillStyle='#334'; ctx.fillText('Measured data', padL+w-45, padT+12);
}

// init
toggleService(); toggleServiceR(); toggleServiceO(); toggleServiceF(); initFieldRows();
</script>
</body>
</html>
