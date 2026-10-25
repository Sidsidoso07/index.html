<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Rota | Rastreamento do VW Golf</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Barlow:wght@400;500;600&family=Chakra+Petch:wght@500;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">
<style>
:root{--bg:#0f1114;--panel:#171a1f;--panel2:#1d2127;--line:#292e36;--ice:#e9eef4;--mute:#8d97a3;--red:#e5322d;--amber:#f2a93b;--ok:#4cc38a;--chrome:#0b0d0f;--stripe1:#101215;--stripe2:#0d0f12;--field:#b8c1cc;font-family:Barlow,sans-serif;color:var(--ice);background:var(--bg)}
:root[data-theme=light]{--bg:#f4f6f8;--panel:#fff;--panel2:#e9edf2;--line:#d2d8e0;--ice:#202832;--mute:#626d79;--chrome:#fff;--stripe1:#f5f7fa;--stripe2:#edf0f4;--field:#424d59}
*{box-sizing:border-box}
body{margin:0;min-width:320px;min-height:100vh;background:repeating-linear-gradient(45deg,var(--stripe1) 0 2px,var(--stripe2) 2px 4px)}
button,input,select{font:inherit;color:inherit}button{cursor:pointer}
:focus-visible{outline:2px solid var(--amber);outline-offset:2px}
h1,h2,h3,.num{font-family:"Chakra Petch",sans-serif;margin:0}
.app{min-height:100vh;display:grid;grid-template-columns:232px minmax(0,1fr)}
.sidebar{background:var(--chrome);border-right:1px solid var(--line);padding:22px 14px;display:flex;flex-direction:column;gap:26px}
.brand{display:flex;align-items:center;gap:10px;padding:0 6px;font:700 22px "Chakra Petch"}
.brand i{width:34px;height:34px;display:grid;place-items:center;background:var(--red);clip-path:polygon(0 0,100% 0,82% 100%,0 100%);font-style:normal;font-size:18px}
.nav{display:grid;gap:4px}
.nav button{display:flex;gap:12px;align-items:center;padding:11px 12px;border:0;border-left:3px solid transparent;background:none;color:var(--mute);text-align:left;font-size:14px}
.nav button.active{border-left-color:var(--red);background:var(--panel);color:var(--ice)}
.nav .badge{margin-left:auto}
.badge{min-width:20px;padding:1px 6px;border-radius:10px;background:var(--red);font-size:11px;font-weight:600;text-align:center}.badge:empty,.badge[hidden]{display:none}
.fleet-title{margin:0 6px 8px;color:var(--mute);font-size:12px}
.vehicle{width:100%;display:flex;align-items:center;gap:10px;padding:10px;border:1px solid var(--line);background:var(--panel);text-align:left}
.vehicle b{display:block;font-size:13px;font-weight:600}.vehicle small{color:var(--mute);font-size:12px;letter-spacing:.06em}
.vehicle .grow{flex:1;min-width:0}
.dot{width:9px;height:9px;border-radius:50%;background:var(--mute)}.dot.on{background:var(--ok);box-shadow:0 0 8px var(--ok)}
.account{margin-top:auto;padding-top:14px;border-top:1px solid var(--line);display:flex;gap:10px;align-items:center;font-size:12px}
.avatar{width:34px;height:34px;border-radius:50%;background:var(--panel2);border:1px solid var(--line);display:grid;place-items:center;font-weight:600}
.account small{display:block;color:var(--mute)}
.topbar{min-height:72px;padding:0 26px;display:flex;align-items:center;justify-content:space-between;gap:14px;background:var(--chrome);border-bottom:2px solid var(--red)}
.topbar h1{font-size:20px;font-weight:600}.live{display:flex;align-items:center;gap:7px;margin-top:3px;color:var(--mute);font-size:12px}
.top-actions{display:flex;gap:8px;align-items:center}
.btn{min-height:38px;padding:0 14px;border:1px solid var(--line);background:var(--panel2);display:inline-flex;align-items:center;justify-content:center;gap:8px;font-size:13px;font-weight:500}
.btn:hover{border-color:#47505c}.btn:disabled{opacity:.4;cursor:not-allowed}
.btn.primary{background:var(--red);border-color:var(--red);color:#fff;font-weight:600}
.btn.danger{border-color:#6b2a2a;color:#ff8a85}.btn.danger.on{background:var(--red);border-color:var(--red);color:#fff}
select.btn{padding:0 8px}
.content{padding:20px 26px 28px}
.summary{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:10px;margin-bottom:14px}
.stat{padding:13px 15px;background:var(--panel);border:1px solid var(--line);border-top:2px solid var(--line)}
.stat.hl{border-top-color:var(--amber)}
.stat span{color:var(--mute);font-size:12px}.stat .num{display:block;margin-top:6px;font-size:22px;font-weight:600}.stat small{font:500 12px Barlow;color:var(--mute)}
.map-layout{height:calc(100vh - 224px);min-height:540px;display:grid;grid-template-columns:minmax(0,1fr) 330px;border:1px solid var(--line);background:var(--panel)}
.map-wrap{position:relative;min-height:380px}#map{position:absolute;inset:0;background:var(--bg)}
.leaflet-tile-pane{filter:invert(1) hue-rotate(180deg) brightness(.82) contrast(.95) saturate(.45)}
:root[data-theme=light] .leaflet-tile-pane{filter:none}
.leaflet-control-attribution{font-size:10px!important}
.map-search{position:absolute;z-index:500;top:14px;left:14px;width:min(330px,calc(100% - 28px));display:flex;background:var(--panel);border:1px solid var(--line)}
.map-search input{width:100%;height:40px;padding:0 12px;border:0;background:none;outline:0;font-size:13px}
.map-search input:focus-visible{outline:2px solid var(--amber)}
.chips{position:absolute;z-index:500;top:14px;right:14px;display:flex;gap:6px;flex-wrap:wrap;justify-content:flex-end}
.chip{height:34px;padding:0 12px;border:1px solid var(--line);background:var(--panel);font-size:12px;color:var(--mute)}
.chip[aria-pressed=true]{color:var(--ice);border-color:var(--amber);background:#2a2216}
:root[data-theme=light] .chip[aria-pressed=true]{background:#fff4df}
.car{position:relative;width:38px;height:38px}
.car svg{width:100%;height:100%;filter:drop-shadow(0 2px 4px #000a);transition:transform .9s linear}
.car.moving::before{content:"";position:absolute;inset:-4px;border-radius:50%;border:2px solid var(--red);animation:pulse 1.6s ease-out infinite}
@keyframes pulse{from{transform:scale(.6);opacity:.9}to{transform:scale(1.5);opacity:0}}
@media(prefers-reduced-motion:reduce){.car.moving::before{animation:none}.car svg{transition:none}}
.details{padding:18px;border-left:1px solid var(--line);overflow-y:auto;display:flex;flex-direction:column;gap:14px;background:var(--bg)}
.dh{display:flex;justify-content:space-between;gap:10px;align-items:flex-start}
.dh h2{font-size:19px}.plate{display:inline-block;margin-top:5px;padding:2px 10px;border:2px solid #cfd6de;border-top:6px solid #2a5db8;border-radius:3px;background:#f3f5f7;color:#14181d;font:700 14px "Chakra Petch";letter-spacing:.12em}
.status{padding:5px 9px;font-size:12px;font-weight:600;background:#2a1311;color:#ff8a85;white-space:nowrap}
.status.ok{background:#12251d;color:var(--ok)}.status.warn{background:#2a2216;color:var(--amber)}.status.off{background:var(--panel2);color:var(--mute)}
.gauge{background:var(--panel);border:1px solid var(--line);padding:6px 10px 12px;text-align:center}
.gauge svg{width:100%;max-width:260px}
.gauge .lim{font-size:12px;color:var(--mute)}
.bars{display:grid;gap:10px;padding:12px;background:var(--panel);border:1px solid var(--line)}
.bar-row{display:grid;grid-template-columns:104px 1fr minmax(46px,auto);gap:10px;align-items:center;font-size:12px}
.bar-row span:first-child{color:var(--mute)}.bar-row b{text-align:right;font-weight:600}
.track{height:7px;background:var(--panel2);overflow:hidden}.track i{display:block;height:100%;width:0;background:var(--amber);transition:width .6s}
.address{padding:12px;background:var(--panel);border:1px solid var(--line);font-size:13px;line-height:1.55}
.address small{display:block;color:var(--mute)}
.rows div{display:flex;justify-content:space-between;gap:10px;padding:9px 0;border-bottom:1px solid var(--line);font-size:12px}
.rows span{color:var(--mute)}.rows b{font-weight:600;text-align:right}
.actions{display:grid;grid-template-columns:1fr 1fr;gap:8px}.actions .wide{grid-column:1/-1}
.events{display:grid;gap:6px;font-size:12px}.events h3{font-size:14px;margin-bottom:4px}
.ev{display:flex;gap:8px;padding:8px 10px;background:var(--panel);border-left:3px solid var(--mute)}
.ev.danger{border-color:var(--red)}.ev.warn{border-color:var(--amber)}.ev.ok{border-color:var(--ok)}
.ev time{margin-left:auto;color:var(--mute);white-space:nowrap}
.empty{color:var(--mute);font-size:12px;padding:6px 0}
.view-wrap{max-width:980px}
.view-wrap h2{font-size:22px}.view-wrap p{color:var(--mute);font-size:13px;line-height:1.6}
.tabs{display:flex;gap:6px;margin:18px 0 0;border-bottom:1px solid var(--line)}
.tab{min-height:42px;padding:0 14px;border:0;border-bottom:2px solid transparent;background:none;color:var(--mute);font-size:13px}
.tab.active{border-bottom-color:var(--red);color:var(--ice);font-weight:600}
.section{padding:22px 0}
.section h3{font-size:16px}
.fields{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:14px;margin-top:14px}
.field{display:grid;gap:6px;font-size:12px;font-weight:500;color:var(--field)}
.field input{height:42px;padding:0 11px;border:1px solid var(--line);background:var(--panel);outline-color:var(--amber)}
.ctrl{display:grid;grid-template-columns:minmax(0,1fr) auto;gap:10px;align-items:end;margin-top:14px}
.msg{min-height:18px;margin:12px 0 0;color:var(--amber);font-size:13px}
.tag{padding:5px 9px;font-size:12px;font-weight:600;background:#12251d;color:var(--ok)}.tag.off{background:var(--panel2);color:var(--mute)}
:root[data-theme=light] .tag{background:#e8f6ee;color:#19764a}
:root[data-theme=light] .status{background:#fcebea;color:#b42318}
:root[data-theme=light] .status.ok{background:#e8f6ee;color:#19764a}
:root[data-theme=light] .status.warn{background:#fff4df;color:#8a5700}
.shead{display:flex;justify-content:space-between;align-items:flex-start;gap:12px}
table{width:100%;border-collapse:collapse;font-size:13px;margin-top:12px}
th,td{padding:10px 8px;border-bottom:1px solid var(--line);text-align:left}th{color:var(--mute);font-weight:500}
.table-scroll{overflow-x:auto}
.eco{display:flex;gap:10px;align-items:center;font-size:13px;grid-column:1/-1}.eco input{width:18px;height:18px;accent-color:var(--red)}
.field select{height:42px;justify-content:flex-start}
#toasts{position:fixed;z-index:2000;top:84px;right:18px;display:grid;gap:8px;width:min(340px,calc(100% - 36px))}
.toast{padding:12px 14px;background:var(--panel2);border:1px solid var(--line);border-left:4px solid var(--amber);font-size:13px;box-shadow:0 8px 24px #0008}
.toast.danger{border-left-color:var(--red)}.toast.ok{border-left-color:var(--ok)}
@media(max-width:1000px){.map-layout{grid-template-columns:minmax(0,1fr) 290px}.summary{grid-template-columns:repeat(2,1fr)}.map-layout{height:calc(100vh - 290px)}}
@media(max-width:760px){
.app{display:block}.sidebar{padding:12px;gap:12px}.nav{grid-template-columns:repeat(3,1fr)}.nav button{justify-content:center;padding:9px 4px;font-size:12px;border-left:0;border-bottom:3px solid transparent}.nav button.active{border-bottom-color:var(--red)}
.fleet-section,.account{display:none}.topbar{padding:10px 14px;flex-wrap:wrap}.content{padding:14px}
.map-layout{height:auto;grid-template-columns:1fr}.map-wrap{min-height:55vh}.details{border-left:0;border-top:1px solid var(--line)}
.fields,.ctrl{grid-template-columns:1fr}.chips{top:62px}}
</style>
</head>
<body>
<div class="app">
<aside class="sidebar">
  <div class="brand"><i>⌁</i> rota<span style="color:var(--red)">.</span></div>
  <nav class="nav" aria-label="Navegação principal">
    <button class="active" data-view="tracking">◉ <span>Rastreamento</span></button>
    <button data-view="reports">▤ <span>Relatórios</span><span class="badge" id="nav-badge" hidden></span></button>
    <button data-view="settings">⚙ <span>Configurações</span></button>
  </nav>
  <section class="fleet-section"><p class="fleet-title">Minha frota</p><div id="fleet"></div></section>
  <div class="account"><div class="avatar">MA</div><div><b>Marina Alves</b><small>Administradora</small></div></div>
</aside>

<main>
<header class="topbar">
  <div><h1 id="page-title">Rastreamento</h1><div class="live"><span class="dot on" id="live-dot"></span><span id="page-context">Atualizado agora</span></div></div>
  <div class="top-actions" id="sim-actions">
    <button class="btn primary" id="simulate" type="button">Ⅱ Desligar ignição</button>
  </div>
</header>

<section class="content" id="tracking-view">
  <div class="summary">
    <div class="stat hl"><span>Situação</span><b class="num" id="st-status">Em movimento</b></div>
    <div class="stat"><span>Distância hoje</span><b class="num"><span id="st-dist">0,0</span> <small>km</small></b></div>
    <div class="stat"><span>Velocidade máxima</span><b class="num"><span id="st-max">0</span> <small>km/h</small></b></div>
    <div class="stat"><span>Alertas hoje</span><b class="num" id="st-alerts">0</b></div>
  </div>
  <div class="map-layout">
    <div class="map-wrap">
      <div class="map-search"><input id="search" type="search" placeholder="Buscar veículo ou rua do trajeto" aria-label="Buscar veículo ou rua do trajeto"></div>
      <div class="chips">
        <button class="chip" id="chip-follow" aria-pressed="true">Seguir veículo</button>
        <button class="chip" id="chip-route" aria-pressed="true">Trajeto</button>
      </div>
      <div id="map" aria-label="Mapa de localização do veículo"></div>
    </div>
    <aside class="details">
      <div class="dh"><div><h2 id="d-name">VW Golf</h2><span class="plate" id="d-plate">GOL7F26</span></div><span class="status" id="d-status">Em movimento</span></div>

      <div class="gauge">
        <svg viewBox="0 0 200 180" role="img" aria-label="Velocímetro">
          <g transform="rotate(135 100 100)" fill="none" stroke-linecap="round">
            <circle cx="100" cy="100" r="80" stroke="var(--line)" stroke-width="8" stroke-dasharray="377 503"/>
            <circle id="g-arc" cx="100" cy="100" r="80" stroke="var(--ice)" stroke-width="8" stroke-dasharray="0 503"/>
          </g>
          <g id="ticks"></g>
          <g id="needle" style="transition:transform .9s linear"><line x1="100" y1="100" x2="168" y2="100" stroke="#e5322d" stroke-width="3" stroke-linecap="round"/><circle cx="100" cy="100" r="7" fill="var(--panel)" stroke="#e5322d" stroke-width="3"/></g>
          <text id="g-speed" x="100" y="146" text-anchor="middle" font-family="Chakra Petch" font-weight="700" font-size="28" fill="var(--ice)">0</text>
          <text x="100" y="164" text-anchor="middle" font-size="11" fill="var(--mute)">km/h</text>
        </svg>
        <div class="lim" id="g-limit">Limite 60 km/h</div>
      </div>

      <div class="bars">
        <div class="bar-row"><span>Combustível</span><div class="track"><i id="b-fuel"></i></div><b id="t-fuel">0%</b></div>
        <div class="bar-row"><span>Motor</span><div class="track"><i id="b-temp"></i></div><b id="t-temp">0 °C</b></div>
        <div class="bar-row"><span>Bateria auxiliar</span><div class="track"><i id="b-bat"></i></div><b id="t-bat">0%</b></div>
        <div class="bar-row"><span>Bateria do carro</span><div class="track"><i id="b-car"></i></div><b id="t-car">-</b></div>
      </div>

      <div class="address"><small>Última localização</small><b id="d-addr">Av. Paulista</b><small>São Paulo - SP</small></div>

      <div class="rows">
        <div><span>Fonte de energia</span><b id="d-power">Bateria do carro</b></div>
        <div><span>Tensão do carro</span><b id="d-volt">-</b></div>
        <div><span>Autonomia restante</span><b id="d-auto">-</b></div>
        <div><span>Ignição</span><b id="d-ign">Ligada</b></div>
        <div><span>Direção</span><b id="d-head">N</b></div>
        <div><span>Odômetro</span><b id="d-odo">0 km</b></div>
        <div><span>Última atualização</span><b id="d-time">Agora</b></div>
      </div>

      <div class="actions">
        <button class="btn" id="a-center">⌖ Centralizar</button>
        <button class="btn" id="a-share">⇪ Compartilhar</button>
        <button class="btn danger wide" id="a-lock">⛔ Bloquear veículo</button>
        <button class="btn wide" id="a-export">⭳ Exportar trajeto (CSV)</button>
      </div>

      <div class="events"><h3>Eventos recentes</h3><div id="mini-events"></div></div>
    </aside>
  </div>
</section>

<section class="content view-wrap" id="reports-view" hidden>
  <h2>Relatórios</h2>
  <p>Resumo da sessão de hoje e histórico de alertas do veículo.</p>
  <div class="summary" style="margin-top:16px">
    <div class="stat"><span>Distância</span><b class="num"><span id="r-dist">0</span> <small>km</small></b></div>
    <div class="stat"><span>Velocidade média</span><b class="num"><span id="r-avg">0</span> <small>km/h</small></b></div>
    <div class="stat"><span>Tempo em movimento</span><b class="num" id="r-time">0 min</b></div>
    <div class="stat"><span>Consumo estimado</span><b class="num"><span id="r-fuel">0,0</span> <small>L</small></b></div>
  </div>
  <div class="shead" style="margin-top:22px"><h3>Histórico de alertas</h3><div class="top-actions"><button class="btn" id="r-export">⭳ Exportar trajeto</button><button class="btn" id="r-clear">Limpar</button></div></div>
  <div class="table-scroll"><table><thead><tr><th>Horário</th><th>Tipo</th><th>Descrição</th></tr></thead><tbody id="r-body"></tbody></table></div>
</section>

<section class="content view-wrap" id="settings-view" hidden>
  <h2>Configurações</h2>
  <p>Gerencie a conexão do rastreador, os dados do veículo e as regras de alerta.</p>
  <div class="tabs" role="tablist" aria-label="Configurações">
    <button class="tab active" id="tab-connection" role="tab" aria-selected="true" aria-controls="connection-panel" data-tab="connection">⌁ Conectar rastreador</button>
    <button class="tab" id="tab-vehicle" role="tab" aria-selected="false" aria-controls="vehicle-panel" tabindex="-1" data-tab="vehicle">▰ Veículo e alertas</button>
    <button class="tab" id="tab-power" role="tab" aria-selected="false" aria-controls="power-panel" tabindex="-1" data-tab="power">⚡ Energia</button>
    <button class="tab" id="tab-appearance" role="tab" aria-selected="false" aria-controls="appearance-panel" tabindex="-1" data-tab="appearance">◐ Aparência</button>
  </div>
  <section class="section" id="connection-panel" role="tabpanel" aria-labelledby="tab-connection">
    <div class="shead"><div><h3>Conexão do rastreador</h3><p>Estado da conexão e identificação do dispositivo instalado no veículo.</p></div><span class="tag" id="conn-tag">Conectado</span></div>
    <div class="ctrl"><label class="field">Identificador do rastreador<input id="tracker-id" autocomplete="off"></label><button class="btn" id="toggle-conn">Desconectar</button></div>
    <p class="msg" id="conn-msg" aria-live="polite"></p>
    <p style="font-size:12px">Conexão e trajeto demonstrativos. Para usar um rastreador real, integre o sistema à API ou ao protocolo do dispositivo.</p>
  </section>
  <section class="section" id="vehicle-panel" role="tabpanel" aria-labelledby="tab-vehicle" hidden>
    <h3>Veículo monitorado</h3><p>Altere o modelo, a placa e as regras que geram alertas.</p>
    <div class="fields">
      <label class="field">Modelo do veículo<input id="v-name" maxlength="40" autocomplete="off"></label>
      <label class="field">Placa<input id="v-plate" maxlength="8" autocomplete="off"></label>
      <label class="field">Limite de velocidade (km/h)<input id="v-limit" type="number" min="20" max="200"></label>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px;flex-wrap:wrap"><button class="btn primary" id="save-vehicle">Salvar alterações</button><button class="btn" id="refuel">Reabastecer</button></div>
    <p class="msg" id="v-msg" aria-live="polite"></p>
  </section>
  <section class="section" id="power-panel" role="tabpanel" aria-labelledby="tab-power" hidden>
    <div class="shead"><div><h3>Energia</h3><p>Informe como o rastreador é alimentado e quando ele usa a bateria auxiliar.</p></div><span class="tag" id="p-tag">Bateria do carro</span></div>
    <div class="fields">
      <label class="field">Fonte de energia<select class="btn" id="p-source"><option value="auto">Automática (carro ligado: bateria do carro; desligado: bateria auxiliar)</option><option value="car">Sempre a bateria do carro</option><option value="battery">Bateria própria, sem ligação ao carro</option></select></label>
      <label class="field">Alertar quando a bateria ficar abaixo de (%)<input id="p-alert" type="number" min="5" max="50"></label>
      <label class="field">Simular bateria do carro (demonstração)<select class="btn" id="p-car"><option value="auto">Normal</option><option value="low">Baixa</option><option value="dead">Sem bateria (ligação cortada)</option></select></label>
    </div>
    <div class="address" id="p-preview" style="margin-top:14px"></div>
    <div style="display:flex;gap:8px;margin-top:14px;flex-wrap:wrap"><button class="btn primary" id="save-power">Salvar alterações</button></div>
    <p class="msg" id="p-msg" aria-live="polite"></p>
    <p style="font-size:12px">Na demonstração, a bateria descarrega mais rápido do que no mundo real. Sem ligação ao carro, a ignição é estimada pelo movimento e o bloqueio não funciona.</p>
  </section>
  <section class="section" id="appearance-panel" role="tabpanel" aria-labelledby="tab-appearance" hidden>
    <h3>Aparência</h3>
    <p>Escolha o tema da interface. A alteração é aplicada e salva automaticamente neste navegador.</p>
    <div class="fields">
      <label class="field">Tema da interface
        <select class="btn" id="theme-mode">
          <option value="dark">Modo escuro</option>
          <option value="light">Modo claro</option>
        </select>
      </label>
    </div>
  </section>
</section>
</main>
</div>
<div id="toasts" aria-live="polite"></div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
const $=id=>document.getElementById(id),rad=x=>x*Math.PI/180;
const esc=t=>String(t).replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
const fmt=(n,d=1)=>n.toLocaleString("pt-BR",{minimumFractionDigits:d,maximumFractionDigits:d});
const hhmm=d=>d.toLocaleTimeString("pt-BR",{hour:"2-digit",minute:"2-digit",second:"2-digit"});

/* ---------- configuração salva no navegador ---------- */
let saved={};try{saved=JSON.parse(localStorage.getItem("rota")||"{}")}catch(e){}
const cfg=Object.assign({name:"VW Golf",plate:"GOL7F26",tracker:"RST-GOLF-001",limit:120,fuel:72,odo:48213.4,power:"auto",interval:10,eco:true,batAlert:20,bat:88,theme:"dark"},saved);
if(cfg.v!==3){if(!cfg.v)cfg.power="auto";cfg.limit=120;cfg.v=3}
if(cfg.theme!=="light"&&cfg.theme!=="dark")cfg.theme="dark";
document.documentElement.dataset.theme=cfg.theme;
const persist=()=>{cfg.fuel=s.fuel;cfg.bat=s.bat;try{localStorage.setItem("rota",JSON.stringify(cfg))}catch(e){}};

/* ---------- trajeto simulado (circuito em São Paulo) ---------- */
const WP=[[-23.5676,-46.6481,"Av. Paulista"],[-23.5559,-46.6618,"Av. Paulista"],[-23.5600,-46.6710,"Rua da Consolação"],[-23.5690,-46.6900,"Av. Rebouças"],[-23.5790,-46.6860,"Av. Brig. Faria Lima"],[-23.5830,-46.6620,"Av. 23 de Maio"],[-23.5676,-46.6481,"Av. Brigadeiro Luís Antônio"]];
function hav(a,b){const x=Math.sin(rad(b[0]-a[0])/2)**2+Math.cos(rad(a[0]))*Math.cos(rad(b[0]))*Math.sin(rad(b[1]-a[1])/2)**2;return 12742000*Math.asin(Math.sqrt(x))}
function bearing(a,b){const y=Math.sin(rad(b[1]-a[1]))*Math.cos(rad(b[0])),x=Math.cos(rad(a[0]))*Math.sin(rad(b[0]))-Math.sin(rad(a[0]))*Math.cos(rad(b[0]))*Math.cos(rad(b[1]-a[1]));return(Math.atan2(y,x)*180/Math.PI+360)%360}
const cum=[0];for(let i=1;i<WP.length;i++)cum[i]=cum[i-1]+hav(WP[i-1],WP[i]);const TOTAL=cum[cum.length-1];
function pointAt(d){d=((d%TOTAL)+TOTAL)%TOTAL;let i=1;while(cum[i]<d)i++;const f=(d-cum[i-1])/(cum[i]-cum[i-1]),a=WP[i-1],b=WP[i];return{lat:a[0]+(b[0]-a[0])*f,lng:a[1]+(b[1]-a[1])*f,head:bearing(a,b),name:b[2]}}

/* ---------- estado ---------- */
const s={mult:1,dead:false,dist:0,speed:0,cruise:40,fuel:cfg.fuel,temp:40,bat:cfg.bat,carV:12.7,carMode:"auto",carLevel:"high",lastSrc:"car",sinceReport:1e9,wasParked:true,lowBat:false,run:true,connected:true,locked:false,
  today:0,max:0,movSec:0,sum:0,n:0,over:false,lowFuel:false,lastFix:Date.now(),follow:true,pos:pointAt(0),tick:0};
s.rpos=s.pos;const events=[],trail=[];let unread=0,alertsToday=0;

/* ---------- mapa ---------- */
const map=L.map("map",{zoomControl:false}).setView([s.pos.lat,s.pos.lng],14);
L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",{maxZoom:19,attribution:'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'}).addTo(map);
L.control.zoom({position:"bottomright"}).addTo(map);
const carIcon=L.divIcon({className:"",iconSize:[38,38],iconAnchor:[19,19],html:'<div class="car moving"><svg viewBox="0 0 38 38"><path d="M19 3 31 33 19 27 7 33Z" fill="#e5322d" stroke="#fff" stroke-width="2.5" stroke-linejoin="round"/></svg></div>'});
const marker=L.marker([s.pos.lat,s.pos.lng],{icon:carIcon}).addTo(map);
const trailLine=L.polyline([],{color:"#f2a93b",weight:4,opacity:.9}).addTo(map);
map.on("dragstart",()=>setChip("follow",false));

/* ---------- alertas ---------- */
function addEvent(type,text){
  events.unshift({t:new Date(),type,text});if(events.length>100)events.pop();
  if(type!=="ok"){alertsToday++;unread++}
  const t=document.createElement("div");t.className="toast "+type;t.textContent=text;$("toasts").appendChild(t);setTimeout(()=>t.remove(),4500);
  renderEvents();
}
const typeLabel={danger:"Crítico",warn:"Atenção",ok:"Informação"};
function renderEvents(){
  $("mini-events").innerHTML=events.slice(0,4).map(e=>`<div class="ev ${e.type}"><span>${esc(e.text)}</span><time>${hhmm(e.t)}</time></div>`).join("")||'<div class="empty">Nenhum evento até agora.</div>';
  $("r-body").innerHTML=events.map(e=>`<tr><td>${hhmm(e.t)}</td><td>${typeLabel[e.type]}</td><td>${esc(e.text)}</td></tr>`).join("")||'<tr><td colspan="3" class="empty">Sem alertas registrados.</td></tr>';
  $("nav-badge").textContent=unread||"";$("nav-badge").hidden=!unread;
}

/* ---------- simulação ---------- */
const carLevel=v=>v>=12.4?"high":v>=11.6?"low":"dead",linked=()=>cfg.power!=="battery",src=()=>cfg.power==="auto"?(s.run?"car":"battery"):cfg.power,onInternal=()=>src()==="battery"||s.carV<9;
const DEMO=20,effInterval=()=>10,drainH=iv=>.15+18/iv;
const fmtH=h=>h>=48?`${Math.floor(h/24)} d ${Math.round(h%24)} h`:`${Math.floor(h)} h ${Math.round((h%1)*60)} min`;
const fmtInt=n=>n<60?`${n} s`:`${n/60} min`;
function step(){
  s.tick++;
  if(s.dead&&src()==="car"&&s.carMode!=="dead"){s.dead=false;s.connected=true;s.carV=12.6;s.carLevel="high";connUI();addEvent("ok","Rastreador religado pela bateria do carro")}
  if(s.connected){
    const drive=s.run&&!s.locked;
    if(drive&&Math.random()<.12)s.cruise=25+Math.random()*45+(Math.random()<.08?25:0);
    const target=drive?s.cruise:0,acc=s.locked?9:4;
    s.speed=Math.max(0,s.speed+Math.max(-acc*1.6,Math.min(acc,target-s.speed)));
    if(!s.run&&s.speed<1)s.speed=0;
    const m=s.speed/3.6*s.mult,km=m/1000;
    s.dist+=m;s.today+=km;cfg.odo+=km;s.fuel=Math.max(0,s.fuel-km*.17);
    if(linked()){
      const tv=s.carMode==="dead"?0:s.carMode==="low"?11.9:(s.run?14.1:12.6);
      s.carV=Math.max(0,s.carV+(tv-s.carV)*.2+(tv?(Math.random()-.5)*.04:0));
      const lv=carLevel(s.carV);
      if(lv!==s.carLevel){s.carLevel=lv;
        if(lv==="high")addEvent("ok",`Bateria do carro carregada (${fmt(s.carV)} V)`);
        else if(lv==="low")addEvent("warn",`Bateria do carro baixa (${fmt(s.carV)} V)`);
        else addEvent("danger","Bateria do carro sem carga ou desligada: rastreador usando a bateria auxiliar")}
    }
    if(onInternal())s.bat=Math.max(0,s.bat-drainH(effInterval())/3600*DEMO*s.mult);
    else s.bat=Math.min(100,s.bat+.05*s.mult);
    const cur=onInternal()?"aux":"car";
    if(cur!==s.lastSrc){s.lastSrc=cur;addEvent("ok",cur==="aux"?`Fonte de energia: bateria auxiliar${linked()?(s.carV<9?" (carro sem carga)":" (ignição desligada)"):""}`:"Fonte de energia: bateria do carro (ignição ligada)")}
    s.max=Math.max(s.max,s.speed);if(s.speed>2){s.movSec+=s.mult;s.sum+=s.speed;s.n++}
    s.temp+=((s.run?84+s.speed*.12:30)-s.temp)*.05;
    s.pos=pointAt(s.dist);
    const parked=s.speed<2;
    s.lastFix=Date.now();s.rpos=s.pos;
    marker.setLatLng([s.pos.lat,s.pos.lng]);
    if(!parked){trail.push({t:new Date(),lat:s.pos.lat,lng:s.pos.lng,v:s.speed});if(trail.length>3000)trail.shift();trailLine.setLatLngs(trail.map(p=>[p.lat,p.lng]))}
    if(s.follow)map.panTo([s.pos.lat,s.pos.lng],{animate:true,duration:.9,easeLinearity:1});
    checkRules();
    if(s.bat<=0&&onInternal()){s.dead=true;s.connected=false;connUI();addEvent("danger","Bateria do rastreador esgotada: veículo sem sinal")}
    if(s.tick%10===0)persist();
  }
  render();
}
function checkRules(){
  if(s.speed>cfg.limit&&!s.over){s.over=true;addEvent("warn",`Excesso de velocidade: ${Math.round(s.speed)} km/h (limite ${cfg.limit})`)}
  else if(s.speed<=cfg.limit-2)s.over=false;
  if(s.fuel<15&&!s.lowFuel){s.lowFuel=true;addEvent("warn",`Combustível baixo: ${Math.round(s.fuel)}%`)}
  if(s.fuel>=15)s.lowFuel=false;
  if(onInternal()&&s.bat<=cfg.batAlert&&!s.lowBat){s.lowBat=true;addEvent("warn",`Bateria do rastreador em ${Math.round(s.bat)}%: autonomia de ${fmtH(s.bat/drainH(effInterval()))}`)}
  if(s.bat>cfg.batAlert+2)s.lowBat=false;
}

/* ---------- interface ---------- */
(function(){let h="";for(let v=0;v<=200;v+=20){const a=rad(135+270*v/200),c=Math.cos(a),n=Math.sin(a);h+=`<line x1="${100+c*65}" y1="${100+n*65}" x2="${100+c*71}" y2="${100+n*71}" stroke="#4a5666" stroke-width="2"/><text x="${100+c*54}" y="${100+n*54+3}" text-anchor="middle" font-size="9" fill="#8d97a3">${v}</text>`}$("ticks").innerHTML=h})();
const cardinal=h=>["N","NE","L","SE","S","SO","O","NO"][Math.round(h/45)%8];
const ago=ms=>{const t=Math.floor(ms/1000);return t<5?"Agora":t<60?`Há ${t} s`:`Há ${Math.floor(t/60)} min`};
function render(){
  const mv=s.connected&&s.speed>2;
  const [label,cls]=!s.connected?["Sem sinal","off"]:s.locked?["Bloqueado","danger"]:!s.run?["Estacionado","off"]:mv?["Em movimento","ok"]:["Parado","warn"];
  $("st-status").textContent=label;$("d-status").textContent=label;$("d-status").className="status "+(cls==="danger"?"":cls);
  $("st-dist").textContent=fmt(s.today);$("st-max").textContent=Math.round(s.max);$("st-alerts").textContent=alertsToday;
  $("d-name").textContent=cfg.name;$("d-plate").textContent=cfg.plate;
  const v=Math.min(s.speed,200);
  $("g-arc").setAttribute("stroke-dasharray",`${377*v/200} 503`);
  $("g-arc").setAttribute("stroke",s.speed>cfg.limit?"#e5322d":"var(--ice)");
  $("needle").style.transform=`rotate(${135+270*v/200}deg)`;$("needle").style.transformOrigin="100px 100px";
  $("g-speed").textContent=Math.round(s.speed);$("g-limit").textContent=`Limite ${cfg.limit} km/h`;
  const set=(b,t,p,txt,warn)=>{$(b).style.width=p+"%";$(b).style.background=warn?"#e5322d":"#f2a93b";$(t).textContent=txt};
  set("b-fuel","t-fuel",s.fuel,Math.round(s.fuel)+"%",s.fuel<15);
  set("b-temp","t-temp",Math.min(100,(s.temp-30)/90*100),Math.round(s.temp)+" °C",s.temp>105);
  set("b-bat","t-bat",s.bat,Math.round(s.bat)+"%",s.bat<=cfg.batAlert);
  const hasCar=linked(),lvl=carLevel(s.carV),col={high:"#4cc38a",low:"#f2a93b",dead:"#e5322d"}[lvl];
  $("t-car").textContent=hasCar?{high:"Alta",low:"Baixa",dead:"Sem bateria"}[lvl]:"Sem leitura";$("t-car").style.color=hasCar?col:"";
  $("b-car").style.width=hasCar?Math.max(4,Math.min(100,(s.carV-9)/5.5*100))+"%":"0%";$("b-car").style.background=col;
  $("d-volt").textContent=hasCar?`${fmt(s.carV)} V`:"Sem leitura (sem ligação ao carro)";
  $("d-addr").textContent=s.rpos.name;
  $("d-ign").textContent=linked()?(s.run?"Ligada":"Desligada"):(mv?"Ligada (estimada)":"Desligada (estimada)");
  $("d-head").textContent=`${cardinal(s.rpos.head)} · ${Math.round(s.rpos.head)}°`;
  $("d-power").textContent=onInternal()?(linked()?"Bateria auxiliar"+(s.carV<9?" (carro sem carga)":" (ignição desligada)"):"Bateria própria"):"Bateria do carro";
  $("d-auto").textContent=!onInternal()?"Carregando pelo carro":s.bat<=0?"Esgotada":fmtH(s.bat/drainH(effInterval()));
  $("d-odo").textContent=fmt(cfg.odo)+" km";
  $("d-time").textContent=ago(Date.now()-s.lastFix);
  $("page-context").textContent=s.connected?"Atualizado "+ago(Date.now()-s.lastFix).toLowerCase():"Sem sinal do rastreador";
  $("live-dot").className="dot"+(s.connected?" on":"");
  $("r-dist").textContent=fmt(s.today);$("r-avg").textContent=s.n?Math.round(s.sum/s.n):0;
  $("r-time").textContent=Math.round(s.movSec/60)+" min";$("r-fuel").textContent=fmt(s.today/12);
  const el=marker.getElement();if(el){const car=el.firstChild;car.classList.toggle("moving",mv);car.firstChild.style.transform=`rotate(${s.rpos.head}deg)`;
    car.querySelector("path").setAttribute("fill",s.connected?"#e5322d":"#8d97a3")}
  $("a-lock").classList.toggle("on",s.locked);$("a-lock").style.opacity=cfg.power==="battery"?.5:1;$("a-lock").textContent=s.locked?"✔ Desbloquear veículo":"⛔ Bloquear veículo";
  $("simulate").disabled=!s.connected&&!s.dead;$("simulate").textContent=s.run?"Ⅱ Desligar ignição":"▶ Ligar ignição";
  $("fleet").innerHTML=`<button class="vehicle" id="fleet-btn"><span class="grow"><b>${esc(cfg.name)}</b><small>${esc(cfg.plate)}</small></span><span class="dot ${s.connected?"on":""}"></span></button>`;
  $("fleet-btn").onclick=()=>{setView("tracking");centerNow()};
  document.title=`Rota | Rastreamento do ${cfg.name}`;
}

/* ---------- ações ---------- */
function centerNow(){map.flyTo([s.pos.lat,s.pos.lng],15,{duration:.7})}
function setChip(k,on){const c=$("chip-"+k);c.setAttribute("aria-pressed",on);if(k==="follow")s.follow=on}
$("chip-follow").onclick=()=>{setChip("follow",!s.follow);if(s.follow)centerNow()};
$("chip-route").onclick=e=>{const on=e.currentTarget.getAttribute("aria-pressed")!=="true";setChip("route",on);on?trailLine.addTo(map):map.removeLayer(trailLine)};
$("a-center").onclick=centerNow;
$("a-share").onclick=async()=>{
  const url=`https://www.google.com/maps?q=${s.pos.lat.toFixed(6)},${s.pos.lng.toFixed(6)}`;
  try{await navigator.clipboard.writeText(url);addEvent("ok","Link da localização copiado")}catch(e){prompt("Copie o link da localização:",url)}
};
$("a-lock").onclick=()=>{
  if(cfg.power==="battery"){addEvent("warn","Bloqueio indisponível: o rastreador está sem ligação ao carro");return}
  if(!s.connected){addEvent("warn","Sem sinal: não é possível enviar o comando de bloqueio");return}
  if(!s.locked&&!confirm("Bloquear o veículo? Ele será desacelerado até parar."))return;
  s.locked=!s.locked;addEvent(s.locked?"danger":"ok",s.locked?"Bloqueio enviado: veículo parando":"Veículo desbloqueado");
};
function exportCsv(){
  if(!trail.length){addEvent("warn","Ainda não há trajeto para exportar");return}
  const csv="horario,latitude,longitude,velocidade_kmh\n"+trail.map(p=>`${p.t.toISOString()},${p.lat.toFixed(6)},${p.lng.toFixed(6)},${p.v.toFixed(1)}`).join("\n");
  const a=document.createElement("a");a.href=URL.createObjectURL(new Blob([csv],{type:"text/csv"}));a.download=`trajeto-${cfg.plate}.csv`;a.click();URL.revokeObjectURL(a.href);
}
$("a-export").onclick=exportCsv;$("r-export").onclick=exportCsv;
$("r-clear").onclick=()=>{events.length=0;alertsToday=0;unread=0;renderEvents();render()};
$("simulate").onclick=()=>{s.run=!s.run;addEvent("ok",s.run?"Ignição ligada":"Ignição desligada")};
$("search").addEventListener("keydown",e=>{
  if(e.key!=="Enter")return;const q=e.target.value.trim().toLowerCase();if(!q)return;
  if(`${cfg.name} ${cfg.plate}`.toLowerCase().includes(q)){setChip("follow",true);centerNow();return}
  const w=WP.find(p=>p[2].toLowerCase().includes(q));
  if(w){setChip("follow",false);map.flyTo([w[0],w[1]],16,{duration:.8})}else addEvent("warn",`Nada encontrado para “${e.target.value}”`);
});

/* ---------- navegação ---------- */
function setView(v){
  ["tracking","reports","settings"].forEach(k=>$(k+"-view").hidden=k!==v);
  document.querySelectorAll(".nav [data-view]").forEach(b=>b.classList.toggle("active",b.dataset.view===v));
  $("page-title").textContent={tracking:"Rastreamento",reports:"Relatórios",settings:"Configurações"}[v];
  $("sim-actions").hidden=v!=="tracking";
  if(v==="reports"){unread=0;renderEvents()}
  if(v==="tracking")map.invalidateSize();
}
document.querySelectorAll(".nav [data-view]").forEach(b=>b.onclick=()=>setView(b.dataset.view));

/* ---------- configurações ---------- */
const tabs=[...document.querySelectorAll("[data-tab]")];
function selTab(t){["connection","vehicle","power","appearance"].forEach(k=>$(k+"-panel").hidden=k!==t.dataset.tab);tabs.forEach(i=>{const on=i===t;i.classList.toggle("active",on);i.setAttribute("aria-selected",on);i.tabIndex=on?0:-1})}
tabs.forEach((t,i)=>{t.onclick=()=>selTab(t);t.onkeydown=e=>{if(e.key!=="ArrowRight"&&e.key!=="ArrowLeft")return;e.preventDefault();const n=tabs[(i+(e.key==="ArrowRight"?1:-1)+tabs.length)%tabs.length];selTab(n);n.focus()}});
function fillForm(){$("p-source").value=cfg.power;$("p-alert").value=cfg.batAlert;previewPower();$("tracker-id").value=cfg.tracker;$("v-name").value=cfg.name;$("v-plate").value=cfg.plate;$("v-limit").value=cfg.limit;$("theme-mode").value=cfg.theme}
$("theme-mode").onchange=e=>{cfg.theme=e.target.value;document.documentElement.dataset.theme=cfg.theme;persist()};
function connUI(){
  $("conn-tag").textContent=s.connected?"Conectado":"Desconectado";$("conn-tag").classList.toggle("off",!s.connected);
  $("toggle-conn").textContent=s.connected?"Desconectar":"Conectar ao rastreador";
  if(!s.connected)s.speed=0;
}
$("toggle-conn").onclick=()=>{
  const id=$("tracker-id").value.trim(),m=$("conn-msg");
  if(!s.connected&&!id){m.textContent="Informe o identificador do rastreador para conectar.";return}
  if(!s.connected&&s.bat<=0&&onInternal()){m.textContent="O rastreador está sem energia. Ligue a ignição para alimentá-lo pelo carro.";return}
  s.connected=!s.connected;
  if(s.connected){cfg.tracker=id;s.dead=false;m.textContent=`Rastreador ${id} conectado.`}else m.textContent="Rastreador desconectado.";
  connUI();addEvent(s.connected?"ok":"warn",s.connected?"Rastreador conectado":"Rastreador desconectado");persist();
};
function previewPower(){
  const src=$("p-source").value,h=fmtH(100/drainH(10));
  $("p-tag").textContent={auto:"Automática",car:"Bateria do carro",battery:"Bateria própria"}[src];
  $("p-preview").innerHTML=src==="auto"
    ?`<b>Troca automática de fonte</b><small>Ignição ligada: usa a bateria do carro e mantém a auxiliar carregada. Ignição desligada: passa sozinho para a auxiliar, que dura cerca de ${h} com 100%.</small>`
    :src==="car"
    ?`<b>Sempre alimentado pelo carro</b><small>A bateria auxiliar fica carregada e serve de reserva se a ligação for cortada: com 100%, dura cerca de ${h}.</small>`
    :`<b>Autonomia com 100%: ${h}</b><small>Sem ligação ao carro, o rastreador depende só da bateria própria.</small>`;
}
["p-source"].forEach(id=>$(id).addEventListener("input",previewPower));
$("p-car").onchange=e=>{s.carMode=e.target.value};
$("save-power").onclick=()=>{
  const a=+$("p-alert").value,m=$("p-msg"),src=$("p-source").value;
  if(!(a>=5&&a<=50)){m.textContent="O alerta de bateria deve ficar entre 5% e 50%.";return}
  Object.assign(cfg,{power:src,batAlert:a});
  if(src==="battery")s.locked=false;
  s.sinceReport=1e9;s.lowBat=false;persist();render();m.textContent="Configurações de energia salvas.";
  addEvent("ok",{auto:"Troca automática de fonte ativada",car:"Rastreador alimentado pelo carro",battery:"Rastreador usando bateria própria"}[src]);
};

$("save-vehicle").onclick=()=>{
  const name=$("v-name").value.trim(),plate=$("v-plate").value.replace("-","").trim().toUpperCase(),limit=+$("v-limit").value,m=$("v-msg");
  if(!name){m.textContent="Informe o modelo do veículo.";return}
  if(!/^[A-Z]{3}[0-9][A-Z0-9][0-9]{2}$/.test(plate)){m.textContent="Placa inválida. Use o formato ABC1D23 ou ABC1234.";return}
  if(!(limit>=20&&limit<=200)){m.textContent="O limite de velocidade deve ficar entre 20 e 200 km/h.";return}
  Object.assign(cfg,{name,plate,limit});
  marker.bindTooltip(`${name} · ${plate}`);persist();render();m.textContent="Dados do veículo atualizados.";
};
$("refuel").onclick=()=>{s.fuel=100;s.lowFuel=false;addEvent("ok","Tanque cheio registrado");$("v-msg").textContent="Combustível em 100%."};

/* ---------- início ---------- */
marker.bindTooltip(`${cfg.name} · ${cfg.plate}`);
fillForm();renderEvents();render();
setInterval(step,1000);
</script>
</body>
</html>
