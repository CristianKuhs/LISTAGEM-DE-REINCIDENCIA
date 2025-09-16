<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Lista de Clientes — Gestão de Problemas</title>

<!-- Google Font (diferente de projetos anteriores) -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&family=Plus+Jakarta+Sans:wght@400;600;700&display=swap" rel="stylesheet">

<!-- Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
  :root{
    --bg:#0b0f14;
    --panel:#0f1720;
    --muted:#9aa4b2;
    --accent:#4dd0e1;
    --danger:#ff6b6b;
    --glass: rgba(255,255,255,0.03);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
 body {
  margin: 0;
  font-family: 'Inter', sans-serif;
  background: #121212;
  color: #f0f0f0;
  line-height: 1.6;
  padding-top: 80px;
}
  header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: #1e1e1e;
  padding: 1rem;
  text-align: center;
  z-index: 1000; /* garante que fique na frente */
  box-shadow: 0 2px 8px rgba(0,0,0,0.6);
}

header h1 {
  margin: 0;
  color: #3a82f7;
  font-family: 'Plus Jakarta Sans', sans-serif;
  font-size: 1.5rem;
}
  .app{
    display:grid;
    grid-template-columns: 420px 1fr;
    gap:20px;
    max-width:1280px;
    margin:0 auto;
  }
  .card{
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border:1px solid rgba(255,255,255,0.04);
    padding:18px;
    border-radius:14px;
    box-shadow: 0 6px 18px rgba(2,6,23,0.6);
  }
  h2{margin:0 0 12px 0; font-size:18px}
  label{display:block; font-size:13px; color:var(--muted); margin-top:10px}
  input[type="text"], input[type="number"], select, .search-input{
    width:100%;
    padding:10px 12px;
    margin-top:6px;
    border-radius:10px;
    border:1px solid rgba(255,255,255,0.04);
    background:var(--panel);
    color:inherit;
    font-size:14px;
  }
  .fiber-wrap{display:flex; align-items:center; gap:8px; margin-top:6px;}
  .fiber-wrap .dash, .fiber-wrap .unit{
    background:var(--glass); padding:10px 12px;
    border-radius:8px; min-width:36px; text-align:center;
    color:var(--muted); font-weight:600;
  }
  .fiber-wrap input[type="number"]{text-align:center; width:80px}
  .toggle{display:flex; gap:12px; align-items:center; margin-top:8px;}
  .btn{
    display:inline-flex; align-items:center; justify-content:center;
    padding:10px 14px; border-radius:10px; cursor:pointer;
    border:1px solid rgba(255,255,255,0.03);
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    color:var(--accent); font-weight:600;
  }
  .btn.ghost{background:transparent; color:var(--muted)}
  .btn.danger{background:linear-gradient(180deg, rgba(255,100,100,0.08), rgba(255,60,60,0.04)); color:var(--danger)}
  .content{display:flex; flex-direction:column; gap:18px;}
  .search-area{display:flex; gap:10px; align-items:center;}
  .group{
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border:1px solid rgba(255,255,255,0.03);
    padding:12px; border-radius:12px;
  }
  .list{margin-top:8px; display:grid; gap:8px;}
  .item{
    padding:10px; border-radius:10px;
    border:1px solid rgba(255,255,255,0.02);
    display:flex; justify-content:space-between; gap:12px; align-items:center;
  }
  .item .meta{font-size:13px; color:var(--muted)}
  .item .name{font-weight:700; font-size:15px}
  .pill{font-size:12px; padding:6px 8px; border-radius:999px; background:rgba(255,255,255,0.02); color:var(--muted);}
  .charts{display:grid; grid-template-columns:1fr 1fr; gap:12px;}
  canvas{background:transparent; border-radius:8px; padding:6px}
  footer{margin-top:12px; color:var(--muted); font-size:13px; text-align:center}
  @media(max-width:980px){.app{grid-template-columns:1fr;}}
</style>
</head>
<body>
<div class="app">
  <!-- FORM -->
  <div class="card">
    <h2>Registrar Cliente</h2>
    <label>NOME:</label>
    <input id="name" type="text" placeholder="Nome do cliente" />
    <label>PONTO DE ACESSO:</label>
    <input id="ap" type="text" placeholder="Ex: AP-Sul-01" />
    <label>MODELO DO ROUTER:</label>
    <select id="model">
      <option disabled selected>Selecione o modelo</option>
      <option>Greatek</option><option>Multilaser</option><option>TP-Link</option>
      <option>ZTE H198A</option><option>ZTE H199A</option>
      <option>ZTE F670L</option><option>ZTE F6600P</option>
    </select>
    <label>SINAL DA FIBRA:</label>
    <div class="fiber-wrap"><div class="dash">-</div><input id="fiber" type="number" step="0.1"/><div class="unit">dBm</div></div>
    <label>PROBLEMA RECORRENTE:</label>
    <div class="toggle">
      <label><input type="radio" name="rec" value="no" checked /> NÃO</label>
      <label><input type="radio" name="rec" value="yes" /> SIM</label>
      <div id="problem-select" style="display:none; margin-left:auto;">
        <select id="problem">
          <option disabled selected>Escolha o problema</option>
          <option>SEM ACESSO</option><option>ACESSO LENTO</option>
          <option>QUEDAS</option><option>ONU LOS</option>
        </select>
      </div>
    </div>
    <div style="display:flex; gap:8px; margin-top:14px;">
      <button id="saveBtn" class="btn">Salvar</button>
      <button id="clearBtn" class="btn ghost">Limpar</button>
      <button id="exportBtn" class="btn ghost">Exportar JSON</button>
    </div>
  </div>
  <!-- LIST + GRÁFICOS -->
  <div class="content">
    <div class="group card">
      <h3>Clientes por Ponto de Acesso</h3>
      <div style="display:flex; gap:8px; margin-top:8px;">
        <input class="search-input" id="searchName" placeholder="Pesquisar nome..." />
        <select id="apFilter" style="min-width:160px;">
          <option value="">Todos os Pontos</option>
        </select>
      </div>
      <div id="groupsContainer" style="margin-top:12px;"></div>
    </div>
    <div class="group card">
      <h3>Consolidados / Gráficos</h3>
      <div class="charts">
        <div><canvas id="chartAP"></canvas></div>
        <div><canvas id="chartModel"></canvas></div>
      </div>
      <button id="resetData" class="btn danger" style="margin-top:12px;">Resetar dados</button>
    </div>
    <footer>Dados armazenados no navegador (localStorage)</footer>
  </div>
</div>
<script>
const els = {
  name:document.getElementById('name'), ap:document.getElementById('ap'),
  model:document.getElementById('model'), fiber:document.getElementById('fiber'),
  problem:document.getElementById('problem'),
  saveBtn:document.getElementById('saveBtn'), clearBtn:document.getElementById('clearBtn'),
  exportBtn:document.getElementById('exportBtn'), searchName:document.getElementById('searchName'),
  apFilter:document.getElementById('apFilter'), groups:document.getElementById('groupsContainer'),
  resetData:document.getElementById('resetData'), problemSelect:document.getElementById('problem-select'),
  chartAP:document.getElementById('chartAP'), chartModel:document.getElementById('chartModel')
};
let data=[];
function load(){const raw=localStorage.getItem('client_list'); if(raw) data=JSON.parse(raw);}
function save(){localStorage.setItem('client_list',JSON.stringify(data));}
function resetForm(){els.name.value=''; els.ap.value=''; els.model.value=''; els.fiber.value=''; document.querySelector('input[name="rec"][value="no"]').checked=true; els.problemSelect.style.display='none';}
document.querySelectorAll('input[name="rec"]').forEach(r=>r.addEventListener('change',e=>{els.problemSelect.style.display=e.target.value==='yes'?'block':'none'}));
els.saveBtn.onclick=()=>{if(!els.name.value||!els.ap.value||!els.model.value){alert('Preencha os campos obrigatórios');return;}
  const rec=document.querySelector('input[name="rec"]:checked').value==='yes';
  data.push({id:Date.now(),name:els.name.value,ap:els.ap.value,model:els.model.value,fiber:els.fiber.value,recurring:rec,problem:rec?els.problem.value:''});
  save(); render(); updateCharts(); resetForm();};
els.clearBtn.onclick=resetForm;
els.exportBtn.onclick=()=>{const a=document.createElement('a');a.href=URL.createObjectURL(new Blob([JSON.stringify(data,null,2)],{type:'application/json'}));a.download='clientes.json';a.click();}
els.resetData.onclick=()=>{if(confirm('Apagar todos os dados?')){data=[];save();render();updateCharts();}}
els.searchName.oninput=render; els.apFilter.onchange=render;
function render(){els.groups.innerHTML='';const q=els.searchName.value.toLowerCase(),f=els.apFilter.value;const aps={};data.forEach(d=>(aps[d.ap]=aps[d.ap]||[]).push(d));
  els.apFilter.innerHTML='<option value="">Todos os Pontos</option>'+Object.keys(aps).map(ap=>`<option>${ap}</option>`).join('');
  for(const ap in aps){if(f&&ap!==f)continue;const list=aps[ap].filter(d=>d.name.toLowerCase().includes(q));if(!list.length)continue;
    const div=document.createElement('div');div.className='group';div.innerHTML='<h3>'+ap+'</h3>';
    const l=document.createElement('div');l.className='list';
    list.forEach(it=>{const item=document.createElement('div');item.className='item';
      item.innerHTML='<div><div class="name">'+it.name+'</div><div class="meta">'+it.model+' • '+(it.fiber||'-')+' dBm</div></div><div class="pill">'+(it.recurring?'Recorrente: '+it.problem:'Não recorrente')+'</div>';
      l.appendChild(item);});
    div.appendChild(l);els.groups.appendChild(div);}}
let chartAP,chartModel;
function updateCharts(){if(chartAP)chartAP.destroy();if(chartModel)chartModel.destroy();
  const apC={},mC={};data.forEach(d=>{if(d.recurring){apC[d.ap]=(apC[d.ap]||0)+1;mC[d.model]=(mC[d.model]||0)+1}});
  chartAP=new Chart(els.chartAP,{type:'bar',data:{labels:Object.keys(apC),datasets:[{data:Object.values(apC)}]},options:{plugins:{legend:{display:false}}}});
  chartModel=new Chart(els.chartModel,{type:'bar',data:{labels:Object.keys(mC),datasets:[{data:Object.values(mC)}]},options:{plugins:{legend:{display:false}}}});}
load();render();updateCharts();
</script>
</body>
</html>
