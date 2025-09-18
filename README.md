<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Lista de Clientes — Gestão de Problemas</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&family=Plus+Jakarta+Sans:wght@400;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  :root{
    --bg:#0b0f14;
    --panel:#131a24;
    --muted:#9aa4b2;
    --accent:#4dd0e1;
    --danger:#ff6b6b;
    --glass: rgba(255,255,255,0.05);
  }
  *{box-sizing:border-box; margin:0; padding:0}
  body{
    font-family:'Inter',sans-serif;
    background:var(--bg);
    color:#f5f5f5;
    height:100vh;
    display:flex;
    flex-direction:column;
  }
  header{
    flex-shrink:0;
    background:#111820;
    padding:1rem 2rem;
    text-align:center;
    box-shadow:0 2px 6px rgba(0,0,0,0.6);
    position:sticky;top:0;z-index:10;
  }
  header h1{
    font-family:'Plus Jakarta Sans',sans-serif;
    font-size:1.6rem;
    color:var(--accent);
  }
  .container{
    flex:1;
    display:grid;
    grid-template-columns: 1fr 2fr;
    gap:24px;
    max-width:1400px;
    margin:20px auto;
    padding:0 20px;
    align-items:start;
  }
  .card{
    background:var(--panel);
    border-radius:14px;
    padding:20px;
    box-shadow:0 8px 18px rgba(0,0,0,0.4);
  }
  h2,h3{margin-bottom:12px;font-size:18px;font-weight:600}
  label{font-size:13px;color:var(--muted);display:block;margin-top:12px}
  input,select{
    width:100%;padding:10px 12px;margin-top:6px;
    border-radius:10px;border:1px solid rgba(255,255,255,0.08);
    background:#1b2532;color:#fff;font-size:14px;
  }
  .fiber-wrap{display:flex;align-items:center;gap:8px;margin-top:6px;}
  .fiber-wrap input{flex:1;text-align:center;}
  .fiber-wrap .dash,.fiber-wrap .unit{
    padding:10px 12px;background:var(--glass);
    border-radius:8px;color:var(--muted);font-weight:600;min-width:38px;text-align:center;
  }
  .toggle{display:flex;gap:16px;align-items:center;margin-top:10px;}
  .btn{
    padding:10px 14px;
    border-radius:10px;
    border:none;
    cursor:pointer;
    font-weight:600;
    transition:all 0.2s;
  }
  .btn.primary{background:var(--accent);color:#000;}
  .btn.primary:hover{filter:brightness(1.1);}
  .btn.ghost{background:transparent;color:var(--muted);border:1px solid var(--glass)}
  .btn.danger{background:var(--danger);color:#fff}
  .actions{display:flex;gap:10px;margin-top:16px;flex-wrap:wrap}
  .content{display:flex;flex-direction:column;gap:24px;}
  .group{background:var(--glass);border-radius:12px;padding:16px;}
  .search-area{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
  .list{margin-top:12px;display:flex;flex-direction:column;gap:10px}
  .item{
    background:#1a2430;
    border-radius:10px;
    padding:12px;
    display:flex;justify-content:space-between;align-items:center;
  }
  .item .name{font-weight:600;font-size:15px}
  .item .meta{font-size:13px;color:var(--muted)}
  .pill{padding:6px 10px;font-size:12px;border-radius:999px;background:rgba(255,255,255,0.06)}
  .charts{display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-top:12px}
  canvas{background:#0f1720;border-radius:8px;padding:10px}
  footer{text-align:center;font-size:13px;color:var(--muted);margin:20px 0}
  @media(max-width:1000px){.container{grid-template-columns:1fr}}
</style>
</head>
<body>
<header><h1>Gestão de Clientes e Problemas</h1></header>
<div class="container">
  <div class="card">
    <h2>Registrar Cliente</h2>
    <label>Nome</label>
    <input id="name" type="text" placeholder="Nome do cliente" />
    <label>Ponto de Acesso</label>
    <input id="ap" type="text" placeholder="Ex: AP-Sul-01" />
    <label>Modelo do Roteador</label>
    <select id="model">
      <option disabled selected>Selecione o modelo</option>
      <option>Greatek</option><option>Multilaser</option><option>TP-Link</option>
      <option>ZTE H198A</option><option>ZTE H199A</option>
      <option>ZTE F670L</option><option>ZTE F6600P</option>
    </select>
    <label>Sinal da Fibra</label>
    <div class="fiber-wrap"><div class="dash">-</div><input id="fiber" type="number" step="0.1"/><div class="unit">dBm</div></div>
    <label>Problema Recorrente</label>
    <div class="toggle">
      <label><input type="radio" name="rec" value="no" checked /> Não</label>
      <label><input type="radio" name="rec" value="yes" /> Sim</label>
      <div id="problem-select" style="display:none; margin-left:auto;">
        <select id="problem">
          <option disabled selected>Escolha o problema</option>
          <option>SEM ACESSO</option><option>ACESSO LENTO</option>
          <option>QUEDAS</option><option>ONU LOS</option>
        </select>
      </div>
    </div>
    <div class="actions">
      <button id="saveBtn" class="btn primary">Salvar</button>
      <button id="clearBtn" class="btn ghost">Limpar</button>
      <button id="exportBtn" class="btn ghost">Exportar JSON</button>
    </div>
  </div>
  <div class="content">
    <div class="card">
      <h3>Clientes por Ponto de Acesso</h3>
      <div class="search-area">
        <input class="search-input" id="searchName" placeholder="Pesquisar nome..." />
        <select id="apFilter" style="min-width:160px;"><option value="">Todos os Pontos</option></select>
      </div>
      <div id="groupsContainer"></div>
    </div>
    <div class="card">
      <h3>Consolidados / Gráficos</h3>
      <div class="charts">
        <canvas id="chartAP"></canvas>
        <canvas id="chartModel"></canvas>
      </div>
      <button id="resetData" class="btn danger" style="margin-top:16px;">Resetar Dados</button>
    </div>
    <footer>Dados armazenados no navegador (localStorage)</footer>
  </div>
</div>
<script>
// (JS igual ao anterior, sem alterações de lógica)
</script>
</body>
</html>
