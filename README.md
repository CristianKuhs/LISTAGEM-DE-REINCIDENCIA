<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Lista de Clientes — Gestão de Problemas (Melhorada)</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&family=Plus+Jakarta+Sans:wght@400;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  :root{
    --bg:#071024; --panel:rgba(255,255,255,0.04); --muted:#9aa4b2; --accent:#4dd0e1; --accent-2:#3a82f7; --success:#43d675; --warn:#ffb86b; --danger:#ff6b6b; --glass: rgba(255,255,255,0.03);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{margin:0; font-family:'Inter',system-ui,Arial; background:linear-gradient(180deg,#061018 0%, #081827 100%); color:#e9eef6; -webkit-font-smoothing:antialiased; padding-top:78px}
  header{position:fixed;inset:0 0 auto 0;height:78px;background:rgba(6,8,15,0.6);backdrop-filter:blur(8px);display:flex;align-items:center;justify-content:space-between;padding:12px 20px;border-bottom:1px solid rgba(255,255,255,0.03);z-index:1000}
  header h1{margin:0;font-family:'Plus Jakarta Sans';font-weight:700;color:var(--accent-2);letter-spacing:0.2px}
  header .actions{display:flex;gap:8px;align-items:center}
  .btn{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.04);padding:10px 12px;border-radius:10px;cursor:pointer;font-weight:600}
  .btn.primary{color:#042331;background:linear-gradient(180deg,var(--accent),#2ccfdc);box-shadow:0 6px 18px rgba(45,200,210,0.08)}
  .btn.ghost{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,0.02)}
  .container{max-width:1280px;margin:0 auto;display:grid;grid-template-columns:420px 1fr;gap:20px;padding:20px}
  .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border:1px solid rgba(255,255,255,0.03); padding:18px; border-radius:14px; box-shadow: 0 8px 30px rgba(2,6,23,0.6)}
  h2,h3{margin:0 0 12px 0;font-weight:700}
  label{display:block;font-size:13px;color:var(--muted);margin-top:8px}
  input[type="text"], input[type="number"], select, .search-input{width:100%;padding:10px 12px;margin-top:6px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);background:rgba(255,255,255,0.02);color:inherit;font-size:14px}
  .fiber-wrap{display:flex;align-items:center;gap:8px;margin-top:6px}
  .fiber-wrap .dash,.fiber-wrap .unit{background:var(--glass);padding:10px 12px;border-radius:8px;min-width:36px;text-align:center;color:var(--muted);font-weight:600}
  .row{display:flex;gap:8px}
  .toggle{display:flex;gap:12px;align-items:center;margin-top:8px}
  .pill{font-size:12px;padding:6px 8px;border-radius:999px;background:rgba(255,255,255,0.02);color:var(--muted)}
  .list{margin-top:12px;display:grid;gap:8px}
  .item{display:flex;justify-content:space-between;align-items:center;padding:10px;border-radius:10px;border:1px solid rgba(255,255,255,0.02);background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.00))}
  .meta{font-size:13px;color:var(--muted)}
  .name{font-weight:700}
  .badge{padding:6px 10px;border-radius:999px;font-weight:700}
  .badge.ok{background:rgba(67,214,117,0.08);color:var(--success);border:1px solid rgba(67,214,117,0.06)}
  .badge.warn{background:rgba(255,184,107,0.06);color:var(--warn);border:1px solid rgba(255,184,107,0.06)}
  .badge.danger{background:rgba(255,107,107,0.06);color:var(--danger);border:1px solid rgba(255,107,107,0.06)}
  .charts{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  canvas{background:transparent;border-radius:8px;padding:6px}
  footer{margin-top:12px;color:var(--muted);font-size:13px;text-align:center}
  .controls{display:flex;gap:8px;align-items:center}
  .sort-select{min-width:160px}
  /* modal */
  .modal-backdrop{position:fixed;inset:0;background:rgba(3,6,12,0.6);display:flex;align-items:center;justify-content:center;z-index:2000}
  .modal{width:420px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:18px;border-radius:12px;border:1px solid rgba(255,255,255,0.04)}
  .modal h4{margin:0 0 12px 0}
  .flex-between{display:flex;justify-content:space-between;align-items:center}
  /* responsive */
  @media(max-width:980px){.container{grid-template-columns:1fr;padding:14px}.charts{grid-template-columns:1fr}.modal{width:92%}}
  /* small interactions */
  .btn:hover{transform:translateY(-2px);transition:all .18s}
  .item:hover{transform:scale(1.01);transition:all .12s}
</style>
</head>
<body>
<header>
  <h1>Lista de Clientes • Gestão de Problemas</h1>
  <div class="actions">
    <div class="controls">
      <button id="importBtn" class="btn ghost" title="Importar JSON">Importar</button>
      <button id="exportJsonBtn" class="btn ghost" title="Exportar JSON">Exportar JSON</button>
      <button id="exportCsvBtn" class="btn ghost" title="Exportar CSV">Exportar CSV</button>
      <button id="addSample" class="btn ghost" title="Adicionar exemplos">+Exemplos</button>
    </div>
    <button id="newBtn" class="btn primary">Novo Cliente</button>
  </div>
</header>
<main class="container">
  <!-- FORM -->
  <aside class="card" id="formCard">
    <h2 id="formTitle">Registrar Cliente</h2>
    <label>NOME:</label>
    <input id="name" type="text" placeholder="Nome do cliente" />
    <label>PONTO DE ACESSO:</label>
    <input id="ap" type="text" placeholder="Ex: AP-Sul-01" />
    <label>MODELO DO ROUTER:</label>
    <select id="model">
      <option value="">Selecione o modelo</option>
      <option>Greatek</option><option>Multilaser</option><option>TP-Link</option>
      <option>ZTE H198A</option><option>ZTE H199A</option>
      <option>ZTE F670L</option><option>ZTE F6600P</option>
    </select>
    <label>SINAL DA FIBRA (dBm):</label>
    <div class="fiber-wrap"><div class="dash">-</div><input id="fiber" type="number" step="0.1" placeholder="Ex: -26.5"/><div class="unit">dBm</div></div>
    <label>PROBLEMA RECORRENTE:</label>
    <div class="toggle">
      <label><input id="recNo" type="radio" name="rec" value="no" checked/> NÃO</label>
      <label><input id="recYes" type="radio" name="rec" value="yes"/> SIM</label>
      <div id="problem-select" style="display:none;margin-left:auto;">
        <select id="problem">
          <option value="">Escolha o problema</option>
          <option>SEM ACESSO</option><option>ACESSO LENTO</option>
          <option>QUEDAS</option><option>ONU LOS</option>
        </select>
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px;">
      <button id="saveBtn" class="btn primary">Salvar</button>
      <button id="clearBtn" class="btn ghost">Limpar</button>
      <button id="cancelEdit" class="btn ghost" style="display:none">Cancelar</button>
    </div>
    <div style="margin-top:12px;color:var(--muted);font-size:13px">Dica: clique em um cliente para editar. Duplo clique para abrir opções rápidas.</div>
  </aside>
  <!-- LIST + GRÁFICOS -->
  <section>
    <div class="card">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <h3>Clientes por Ponto de Acesso</h3>
        <div style="display:flex;gap:8px;align-items:center">
          <input class="search-input" id="searchName" placeholder="Pesquisar nome..." />
          <select id="apFilter" style="min-width:160px;"></select>
          <select id="sortBy" class="sort-select">
            <option value="created_desc">Ordenar: Recentes</option>
            <option value="name_asc">Nome ↑</option>
            <option value="name_desc">Nome ↓</option>
            <option value="fiber_desc">Sinal (melhor)</option>
            <option value="fiber_asc">Sinal (pior)</option>
          </select>
          <button id="resetData" class="btn ghost" title="Resetar dados">Resetar</button>
        </div>
      </div>
      <div id="groupsContainer" style="margin-top:12px;max-height:56vh;overflow:auto;padding-right:6px"></div>
    </div>
    <div class="card" style="margin-top:12px">
      <h3>Consolidados / Gráficos</h3>
      <div class="charts" style="margin-top:12px">
        <div><canvas id="chartAP"></canvas></div>
        <div><canvas id="chartModel"></canvas></div>
      </div>
      <div style="display:flex;gap:8px;margin-top:12px;align-items:center;flex-wrap:wrap">
        <div class="pill">Total clientes: <span id="totalCount">0</span></div>
        <div class="pill">Recorrentes: <span id="recCount">0</span></div>
        <div class="pill">Não recorrentes: <span id="nonRecCount">0</span></div>
      </div>
      <footer style="margin-top:8px">Dados armazenados localmente no navegador (localStorage) • Suas ações ficam salvas somente neste dispositivo</footer>
    </div>
  </section>
</main>
<!-- MODAL BACKDROP (reutilizável) -->
<div id="modalRoot" style="display:none"></div>
<script>
// --- helpers ---
const qs=(s,ctx=document)=>ctx.querySelector(s);
const qsa=(s,ctx=document)=>Array.from(ctx.querySelectorAll(s));
const els = {
  name:qs('#name'), ap:qs('#ap'), model:qs('#model'), fiber:qs('#fiber'), problem:qs('#problem'),
  saveBtn:qs('#saveBtn'), clearBtn:qs('#clearBtn'), cancelEdit:qs('#cancelEdit'),
  searchName:qs('#searchName'), apFilter:qs('#apFilter'), groups:qs('#groupsContainer'),
  exportJsonBtn:qs('#exportJsonBtn'), exportCsvBtn:qs('#exportCsvBtn'), importBtn:qs('#importBtn'),
  resetData:qs('#resetData'), newBtn:qs('#newBtn'), addSample:qs('#addSample'), sortBy:qs('#sortBy'),
  totalCount:qs('#totalCount'), recCount:qs('#recCount'), nonRecCount:qs('#nonRecCount'), modalRoot:qs('#modalRoot')
};
let data=[];let editingId=null;
// storage
function load(){const raw=localStorage.getItem('client_list_v2'); if(raw) data=JSON.parse(raw); else data=[]}
function save(){localStorage.setItem('client_list_v2',JSON.stringify(data));}
// utils
function uid(){return Date.now() + Math.floor(Math.random()*999)}
function formatDate(ts){const d=new Date(ts);return d.toLocaleString();}
// render
function render(){els.groups.innerHTML=''; const q=els.searchName.value.trim().toLowerCase(); const f=els.apFilter.value; const sort=els.sortBy.value;
  const grouped = data.reduce((acc,d)=>{ (acc[d.ap] = acc[d.ap]||[]).push(d); return acc },{});
  // fill apFilter
  const aps = Object.keys(grouped).sort(); els.apFilter.innerHTML = '<option value="">Todos os Pontos</option>' + aps.map(a=>`<option value="${a}">${a}</option>`).join('');
  // iterate
  let total=0, rec=0, nonrec=0;
  aps.forEach(ap=>{
    if(f && ap!==f) return;
    let list = (grouped[ap]||[]).filter(it=> it.name.toLowerCase().includes(q));
    if(!list.length) return;
    // sort
    list = list.slice().sort((a,b)=>{
      switch(sort){
        case 'name_asc': return a.name.localeCompare(b.name);
        case 'name_desc': return b.name.localeCompare(a.name);
        case 'fiber_desc': return (parseFloat(b.fiber)||0) - (parseFloat(a.fiber)||0);
        case 'fiber_asc': return (parseFloat(a.fiber)||0) - (parseFloat(b.fiber)||0);
        default: return b.created - a.created;
      }
    });
    const wrapper = document.createElement('div'); wrapper.className='group card'; wrapper.style.marginBottom='8px';
    wrapper.innerHTML = `<h4 style="margin:0 0 8px 0">${ap} <span class=\"pill\">${list.length}</span></h4>`;
    const l = document.createElement('div'); l.className='list';
    list.forEach(it=>{
      total++;
      if(it.recurring) rec++; else nonrec++;
      const item = document.createElement('div'); item.className='item';
      const badgeClass = it.recurring ? (it.problem==='SEM ACESSO'||it.problem==='ONU LOS' ? 'danger' : 'warn') : 'ok';
      item.innerHTML = `
        <div style="display:flex;gap:12px;align-items:center;min-width:0">
          <div style="width:44px;height:44px;border-radius:8px;background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.00));display:flex;align-items:center;justify-content:center;font-weight:700">${it.name.split(' ').map(s=>s[0]).slice(0,2).join('').toUpperCase()}</div>
          <div style="min-width:0">
            <div class=\"name\">${it.name}</div>
            <div class=\"meta\">${it.model} • ${it.fiber||'-'} dBm • <span title='registrado em'>${formatDate(it.created)}</span></div>
          </div>
        </div>
        <div style="display:flex;align-items:center;gap:8px">
          <div class=\"badge ${badgeClass}\">${it.recurring? 'Recorrente' : 'OK'}</div>
          <div style="text-align:right;min-width:120px"><div class=\"meta\">${it.recurring? it.problem : 'Sem problema'}</div></div>
          <div style=\"display:flex;gap:6px\">
            <button class=\"btn ghost btn-edit\" data-id=\"${it.id}\">Editar</button>
            <button class=\"btn ghost btn-delete\" data-id=\"${it.id}\">Excluir</button>
          </div>
        </div>
      `;
      // click to quick edit
      item.addEventListener('dblclick',()=>openQuickMenu(it));
      l.appendChild(item);
    });
    wrapper.appendChild(l); els.groups.appendChild(wrapper);
  });
  // stats
  els.totalCount.textContent = total; els.recCount.textContent = rec; els.nonRecCount.textContent = nonrec;
  // attach edit/delete handlers
  qsa('.btn-edit', els.groups).forEach(b=>b.onclick = e=>startEdit(b.dataset.id));
  qsa('.btn-delete', els.groups).forEach(b=>b.onclick = e=>confirmDelete(b.dataset.id));
  updateCharts();
}
// forms
function resetForm(){els.name.value=''; els.ap.value=''; els.model.value=''; els.fiber.value=''; document.getElementById('recNo').checked=true; qs('#problem-select').style.display='none'; els.problem.value=''; editingId=null; qs('#formTitle').textContent='Registrar Cliente'; els.cancelEdit.style.display='none';}
qs('#recYes').addEventListener('change',()=>qs('#problem-select').style.display='block'); qs('#recNo').addEventListener('change',()=>qs('#problem-select').style.display='none');
els.saveBtn.onclick = ()=>{
  if(!els.name.value.trim() || !els.ap.value.trim() || !els.model.value){ alert('Preencha os campos obrigatórios (Nome, AP, Modelo)'); return; }
  const rec = document.querySelector('input[name="rec"]:checked').value === 'yes';
  const record = { id: editingId || uid(), name: els.name.value.trim(), ap: els.ap.value.trim(), model: els.model.value, fiber: els.fiber.value.trim(), recurring: rec, problem: rec? els.problem.value : '', created: editingId ? (data.find(d=>d.id==editingId).created) : Date.now() };
  if(editingId){ data = data.map(d=> d.id==editingId ? record : d); alert('Cliente atualizado'); } else { data.push(record); alert('Cliente salvo'); }
  save(); resetForm(); render();
};
els.clearBtn.onclick = resetForm; els.cancelEdit.onclick = resetForm;
function startEdit(id){ const rec = data.find(d=>d.id==id); if(!rec) return; editingId = id; qs('#formTitle').textContent='Editando: '+rec.name; els.name.value=rec.name; els.ap.value=rec.ap; els.model.value=rec.model; els.fiber.value=rec.fiber; if(rec.recurring){ qs('#recYes').checked=true; qs('#problem-select').style.display='block'; els.problem.value=rec.problem; } else { qs('#recNo').checked=true; qs('#problem-select').style.display='none'; }
  els.cancelEdit.style.display='inline-flex'; window.scrollTo({top:0,behavior:'smooth'});
}
function confirmDelete(id){ showModal({ title:'Confirma exclusão?', body:'Tem certeza que deseja excluir este cliente? Esta ação não pode ser desfeita.', actions:[{label:'Excluir',kind:'danger',onClick:()=>{ data = data.filter(d=>d.id!=id); save(); render(); closeModal(); }},{label:'Cancelar',kind:'ghost',onClick:closeModal}] }); }
function openQuickMenu(it){ showModal({ title: it.name, body:`<div style=\"margin-bottom:12px\"><strong>AP:</strong> ${it.ap}<br/><strong>Modelo:</strong> ${it.model}<br/><strong>Sinal:</strong> ${it.fiber||'-'} dBm<br/><strong>Recorrente:</strong> ${it.recurring? it.problem : 'não'}</div>`, actions:[{label:'Editar',onClick:()=>{ startEdit(it.id); closeModal(); }},{label:'Excluir',kind:'danger',onClick:()=>{ data = data.filter(d=>d.id!=it.id); save(); render(); closeModal(); }},{label:'Fechar',kind:'ghost',onClick:closeModal}] }); }
// import/export
els.exportJsonBtn.onclick = ()=>{ const a=document.createElement('a'); a.href=URL.createObjectURL(new Blob([JSON.stringify(data,null,2)],{type:'application/json'})); a.download='clientes.json'; a.click(); }
els.exportCsvBtn.onclick = ()=>{ if(!data.length){ alert('Sem dados para exportar'); return;} const keys=['id','name','ap','model','fiber','recurring','problem','created']; const csv = [keys.join(',')].concat(data.map(r=> keys.map(k=> '"'+String(r[k]||'').replace(/"/g,'""')+'"').join(','))).join('\n'); const a=document.createElement('a'); a.href=URL.createObjectURL(new Blob([csv],{type:'text/csv'})); a.download='clientes.csv'; a.click(); }
els.importBtn.onclick = ()=>{ const input = document.createElement('input'); input.type='file'; input.accept='.json'; input.onchange = e=>{ const f = e.target.files[0]; if(!f) return; const reader=new FileReader(); reader.onload = ev=>{ try{ const arr = JSON.parse(ev.target.result); if(Array.isArray(arr)){ data = arr; save(); render(); alert('Importação concluída'); } else alert('Arquivo inválido'); }catch(err){ alert('Erro ao ler arquivo: '+err.message) } }; reader.readAsText(f); }; input.click(); }
// reset / samples
els.resetData.onclick = ()=>{ if(confirm('Apagar todos os dados?')){ data = []; save(); render(); }}
els.addSample.onclick = ()=>{ const samples = [
  {id:uid(),name:'João Silva',ap:'AP-Sul-01',model:'ZTE F670L',fiber:'-26.4',recurring:true,problem:'QUEDAS',created:Date.now()-1000*60*60*24},
  {id:uid(),name:'Maria Souza',ap:'AP-Sul-01',model:'TP-Link',fiber:'-20.1',recurring:false,problem:'',created:Date.now()-1000*60*60*12},
  {id:uid(),name:'Carlos Pereira',ap:'AP-Norte-02',model:'ZTE H198A',fiber:'-28.7',recurring:true,problem:'SEM ACESSO',created:Date.now()-1000*60*60*48}
]; data = data.concat(samples); save(); render(); }
// charts
let chartAP=null, chartModel=null;
function updateCharts(){ if(chartAP) chartAP.destroy(); if(chartModel) chartModel.destroy();
  const apC = {}, mC = {};
  data.forEach(d=>{ apC[d.ap] = (apC[d.ap]||0) + (d.recurring?1:0); mC[d.model] = (mC[d.model]||0) + (d.recurring?1:0); });
  const apLabels = Object.keys(apC); const apValues = apLabels.map(l=>apC[l]);
  const modelLabels = Object.keys(mC); const modelValues = modelLabels.map(l=>mC[l]);
  const ctxA = qs('#chartAP').getContext('2d'); const ctxM = qs('#chartModel').getContext('2d');
  chartAP = new Chart(ctxA, { type:'bar', data:{ labels:apLabels, datasets:[{ label:'Recorrentes por AP', data:apValues, backgroundColor: apLabels.map((_,i)=> 'rgba(63,150,255,'+ (0.5 + (i*0.03)) +')') }] }, options:{ plugins:{ legend:{display:false}, tooltip:{callbacks:{label:ctx=>`${ctx.formattedValue} recorrentes (${((ctx.parsed.y / (data.length||1))*100).toFixed(1)}%)`}} }, responsive:true, maintainAspectRatio:false } });
  chartModel = new Chart(ctxM, { type:'doughnut', data:{ labels:modelLabels, datasets:[{ data:modelValues }] }, options:{ plugins:{ legend:{position:'bottom'}, tooltip:{callbacks:{label:ctx=>`${ctx.label}: ${ctx.formattedValue} recorrentes`}} }, responsive:true, maintainAspectRatio:false } });
}
// modal helpers
function showModal({title='Aviso', body='', actions=[]}){
  const backdrop = document.createElement('div'); backdrop.className='modal-backdrop';
  const modal = document.createElement('div'); modal.className='modal';
  const h = document.createElement('h4'); h.textContent = title; modal.appendChild(h);
  const content = document.createElement('div'); content.innerHTML = body; modal.appendChild(content);
  const footer = document.createElement('div'); footer.style.marginTop='12px'; footer.style.display='flex'; footer.style.gap='8px'; footer.style.justifyContent='flex-end';
  actions.forEach(a=>{ const b = document.createElement('button'); b.className = 'btn ' + (a.kind==='danger'?'':'ghost'); b.textContent = a.label; b.onclick = ()=>{ if(a.onClick) a.onClick(); }; footer.appendChild(b); });
  modal.appendChild(footer); backdrop.appendChild(modal); els.modalRoot.innerHTML=''; els.modalRoot.appendChild(backdrop); els.modalRoot.style.display='block'; backdrop.addEventListener('click', (e)=>{ if(e.target===backdrop) closeModal(); }); }
function closeModal(){ els.modalRoot.style.display='none'; els.modalRoot.innerHTML=''; }
// quick menu on new
els.newBtn.onclick = ()=>{ resetForm(); window.scrollTo({top:0,behavior:'smooth'}); }
// events
els.searchName.oninput = render; els.apFilter.onchange = render; els.sortBy.onchange = render;
// keyboard: Enter to save when focused in form
[q s = 'name ap fiber'].split? null: null; // keep tooling quiet
['name','ap','fiber'].forEach(id=> qs('#'+id).addEventListener('keydown', e=>{ if(e.key==='Enter') els.saveBtn.click(); }));
// init
load(); render();
// expose for debugging (optional)
window._app = { data, load, save, render };
</script>
</body>
</html>
