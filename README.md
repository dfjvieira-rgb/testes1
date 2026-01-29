<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>CADERNO OFICIAL | JOAQUIM ELITE</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary: #0f172a; --gold: #fbbf24; --bg-paper: #cbd5e1;
            --paper-color: #ffffff; --text-color: #1e293b; --line-color: #eef2f7;
            --header-h: 65px;
        }
        body.dark-mode {
            --primary: #020617; --gold: #f59e0b; --bg-paper: #0f172a;
            --paper-color: #1e293b; --text-color: #f8fafc; --line-color: #334155;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { font-family: 'Inter', sans-serif; background: var(--primary); height: 100vh; overflow: hidden; display: flex; flex-direction: column; }

        header { background: var(--primary); height: var(--header-h); padding: 0 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid var(--gold); z-index: 1000; }
       
        .nav-group { display: flex; gap: 8px; }
        .btn-ref { height: 40px; width: 40px; border-radius: 8px; border: 2px solid var(--gold); background: #1e293b; color: var(--gold); cursor: pointer; display: flex; align-items: center; justify-content: center; font-weight: 900; }
        .btn-ref.active { background: var(--gold); color: var(--primary); }

        main { display: flex; flex: 1; overflow: hidden; }

        /* SIDEBAR EXAMES */
        .sidebar { width: 70px; background: #1e293b; display: flex; flex-direction: column; gap: 5px; padding: 10px 5px; overflow-y: auto; border-right: 1px solid var(--gold); }
        .exame-btn { width: 100%; height: 35px; background: #334155; border-radius: 4px; color: white; display: flex; align-items: center; justify-content: center; font-size: 0.75rem; font-weight: bold; cursor: pointer; }
        .exame-btn.active { background: var(--gold); color: var(--primary); }

        /* FOLHA DE RESPOSTAS FGV */
        .editor-container { flex: 1; overflow-y: auto; background: var(--bg-paper); padding: 20px; display: flex; flex-direction: column; align-items: center; }
        .paper { background: var(--paper-color); width: 100%; max-width: 850px; padding: 60px 50px; box-shadow: 0 0 20px rgba(0,0,0,0.2); position: relative; }
       
        /* CABEÇALHO DA PROVA */
        .oab-header { border: 2px solid #000; padding: 15px; margin-bottom: 30px; text-align: center; color: #000; }
        .oab-header h2 { font-size: 1.2rem; text-transform: uppercase; margin-bottom: 5px; }
        .oab-header .peca-nome { font-size: 1.5rem; font-weight: 900; color: #b91c1c; }

        .section-title { background: #000; color: #fff; padding: 5px 15px; font-weight: bold; margin-top: 40px; text-transform: uppercase; letter-spacing: 1px; }
       
        .editor-area {
            outline: none; font-size: 18px; line-height: 30px; color: var(--text-color); font-family: 'Times New Roman', serif;
            background-image: repeating-linear-gradient(var(--paper-color), var(--paper-color) 29px, var(--line-color) 29px, var(--line-color) 30px);
            min-height: 200px; width: 100%; word-wrap: break-word; text-align: justify;
        }

        /* TOOLS */
        .paper-tools { position: fixed; right: 30px; bottom: 30px; display: flex; flex-direction: column; gap: 15px; }
        .btn-fab { width: 60px; height: 60px; border-radius: 50%; border: none; color: white; font-size: 1.5rem; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3); display: flex; align-items: center; justify-content: center; }
       
        .side-rocket { background: linear-gradient(45deg, #f59e0b, #ef4444); animation: pulse 2s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.1); } 100% { transform: scale(1); } }

        /* PDF PANEL */
        .pdf-panel { position: fixed; left: 70px; top: var(--header-h); bottom: 0; width: 0; background: white; transition: 0.3s; z-index: 2000; overflow: hidden; border-right: 3px solid var(--gold); }
        .pdf-panel.open { width: 50%; }

        @media (max-width: 800px) { .pdf-panel.open { width: 100%; left: 0; } }
    </style>
</head>
<body class="dark-mode">

<header>
    <div class="nav-group">
        <button class="btn-ref" onclick="location.reload()"><i class="fas fa-sync"></i></button>
    </div>
    <div class="nav-group">
        <button class="btn-ref" id="ref-p" onclick="togglePdf('p')">P</button>
        <button class="btn-ref" id="ref-g" onclick="togglePdf('g')">G</button>
        <button class="btn-ref" id="ref-v" onclick="togglePdf('v')">V</button>
    </div>
    <div class="nav-group">
        <button class="btn-ref" style="background:#22c55e; color:white; border:none;" onclick="salvarManual()"><i class="fas fa-save"></i></button>
    </div>
</header>

<main>
    <aside class="sidebar" id="exam-nav"></aside>

    <div class="pdf-panel" id="pdf-panel">
        <div style="height:40px; background:#1e293b; display:flex; align-items:center; justify-content:space-between; padding:0 15px; color:white;">
            <span id="pdf-title">REFERÊNCIA</span>
            <button onclick="closePdf()" style="background:#ef4444; border:none; color:white; padding:2px 8px; border-radius:4px;">FECHAR</button>
        </div>
        <iframe id="pdf-frame" style="width:100%; height:calc(100% - 40px); border:none;"></iframe>
    </div>

    <section class="editor-container">
        <div class="paper">
            <div class="oab-header">
                <h2>CADERNO DE RESPOSTAS - PROVA PRÁTICO-PROFISSIONAL</h2>
                <div class="peca-nome" id="nome-da-peca">IDENTIFICANDO...</div>
                <p style="font-size: 0.8rem; margin-top: 5px;">EXAME UNIFICADO DA ORDEM DOS ADVOGADOS DO BRASIL</p>
            </div>

            <div class="section-title">PEÇA PROFISSIONAL</div>
            <div id="editor-peca" class="editor-area" contenteditable="true" placeholder="Inicie sua peça aqui..."></div>

            <div class="section-title">QUESTÃO 01</div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item A)</div>
            <div id="editor-q1a" class="editor-area" contenteditable="true"></div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item B)</div>
            <div id="editor-q1b" class="editor-area" contenteditable="true"></div>

            <div class="section-title">QUESTÃO 02</div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item A)</div>
            <div id="editor-q2a" class="editor-area" contenteditable="true"></div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item B)</div>
            <div id="editor-q2b" class="editor-area" contenteditable="true"></div>

            <div class="section-title">QUESTÃO 03</div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item A)</div>
            <div id="editor-q3a" class="editor-area" contenteditable="true"></div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item B)</div>
            <div id="editor-q3b" class="editor-area" contenteditable="true"></div>

            <div class="section-title">QUESTÃO 04</div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item A)</div>
            <div id="editor-q4a" class="editor-area" contenteditable="true"></div>
            <div style="font-weight:bold; margin: 10px 0 5px 0;">Item B)</div>
            <div id="editor-q4b" class="editor-area" contenteditable="true"></div>
        </div>

        <div class="paper-tools">
            <button class="btn-fab side-rocket" onclick="gerarSnapshot()" title="Finalizar e Enviar"><i class="fas fa-rocket"></i></button>
            <button class="btn-fab" style="background:#3b82f6" onclick="injetarJustica()"><i class="fas fa-gavel"></i></button>
        </div>
    </section>
</main>

<script type="module">
    import { db } from './firebase-config.js';
    import { ref, set, onValue, push } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-database.js";
    import { BANCO_ESPELHOS } from './banco-espelhos.js';

    let activeEx = "41";
    let currentMode = null;
    const ids = ['peca', 'q1a', 'q1b', 'q2a', 'q2b', 'q3a', 'q3b', 'q4a', 'q4b'];

    const carregarFirebase = () => {
        onValue(ref(db, `estudos_completos/${activeEx}`), (s) => {
            const d = s.val();
            if(d) {
                ids.forEach(id => {
                    const el = document.getElementById('editor-' + id);
                    if(el && document.activeElement !== el) el.innerText = d[id] || "";
                });
            }
        }, { onlyOnce: true });
    };

    window.salvarManual = () => {
        const dados = {};
        ids.forEach(id => dados[id] = document.getElementById('editor-' + id).innerText);
        dados.ts = Date.now();
        set(ref(db, `estudos_completos/${activeEx}`), dados);
    };

    window.gerarSnapshot = () => {
        const dados = {};
        ids.forEach(id => dados[id] = document.getElementById('editor-' + id).innerText);
        dados.timestamp = Date.now();
        dados.exame = activeEx;
        dados.peca_nome = document.getElementById('nome-da-peca').innerText;

        push(ref(db, 'historico_envios/'), dados).then(() => {
            alert("🚀 PROVA ENVIADA PARA O HISTÓRICO!");
        });
    };

    window.togglePdf = (mode) => {
        const panel = document.getElementById('pdf-panel');
        if(currentMode === mode) { closePdf(); }
        else {
            currentMode = mode;
            panel.classList.add('open');
            const docs = { 'p': `ro${activeEx}.pdf`, 'g': `ro${activeEx}-g.pdf`, 'v': `vade.pdf` };
            document.getElementById('pdf-frame').src = docs[mode];
        }
    };

    window.closePdf = () => {
        document.getElementById('pdf-panel').classList.remove('open');
        currentMode = null;
    };

    const atualizarInterface = () => {
        const esp = BANCO_ESPELHOS[activeEx] || "";
        const label = document.getElementById('nome-da-peca');
        if (esp.toUpperCase().includes("AGRAVO DE PETIÇÃO")) label.innerText = "AGRAVO DE PETIÇÃO";
        else if (esp.toUpperCase().includes("CONTESTAÇÃO")) label.innerText = "CONTESTAÇÃO";
        else if (esp.toUpperCase().includes("RECURSO ORDINÁRIO")) label.innerText = "RECURSO ORDINÁRIO";
        else label.innerText = "PEÇA PROFISSIONAL";

        const nav = document.getElementById('exam-nav');
        nav.innerHTML = '';
        for (let i = 44; i >= 35; i--) {
            const btn = document.createElement('div');
            btn.className = `exame-btn ${i == activeEx ? 'active' : ''}`;
            btn.innerText = `E${i}`;
            btn.onclick = () => { activeEx = i.toString(); atualizarInterface(); carregarFirebase(); };
            nav.appendChild(btn);
        }
    };

    // Auto-save em qualquer campo
    ids.forEach(id => {
        document.getElementById('editor-' + id).addEventListener('input', window.salvarManual);
    });

    atualizarInterface();
    carregarFirebase();
</script>
</body>
</html> 

