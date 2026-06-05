<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VoiceVault ✦ Studio</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800&display=swap');

        :root {
            --primary: #8b5cf6; 
            --primary-hover: #7c3aed;
            --success: #10b981; 
            --danger: #f43f5e; 
            --glass-bg: rgba(15, 23, 42, 0.55);
            --glass-border: rgba(255, 255, 255, 0.15);
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
        }

        * { box-sizing: border-box; font-family: 'Nunito', sans-serif; margin: 0; padding: 0; }
        
        body { 
            display: flex; height: 100vh; overflow: hidden; 
            /* Fondo Ghibli - Habitación Mágica */
            background-image: url('https://www.ghibli.jp/gallery/howl050.jpg');
            background-size: cover;
            background-position: center;
            color: var(--text-main);
        }

        .glass-panel {
            background: var(--glass-bg);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid var(--glass-border);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
        }

        /* --- SIDEBAR --- */
        .sidebar { width: 90px; display: flex; flex-direction: column; align-items: center; padding: 25px 0; z-index: 20; border-right: 1px solid var(--glass-border); background: rgba(15, 23, 42, 0.7); }
        .brand-logo { width: 55px; height: 55px; border-radius: 18px; object-fit: cover; margin-bottom: 40px; border: 2px solid var(--primary); box-shadow: 0 0 15px rgba(139, 92, 246, 0.4); }
        .nav-item { width: 55px; height: 55px; display: flex; justify-content: center; align-items: center; color: #cbd5e1; border-radius: 18px; margin-bottom: 20px; cursor: pointer; transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .nav-item:hover, .nav-item.active { background: var(--primary); color: white; box-shadow: 0 10px 20px rgba(139, 92, 246, 0.5); transform: translateY(-3px); }

        /* --- MAIN LAYOUT --- */
        .main-wrapper { flex: 1; display: flex; flex-direction: column; position: relative; }
        .top-header { height: 80px; display: flex; justify-content: space-between; align-items: center; padding: 0 40px; border-bottom: 1px solid var(--glass-border); border-radius: 0 0 25px 25px; margin-bottom: 25px; }
        .header-title { font-size: 24px; font-weight: 800; letter-spacing: 1px; text-shadow: 0 2px 4px rgba(0,0,0,0.5); display: flex; align-items: center; gap: 12px; }
        .avatar { width: 48px; height: 48px; border-radius: 50%; object-fit: cover; border: 2px solid white; box-shadow: 0 0 15px rgba(0,0,0,0.5); }

        /* --- VIEWS --- */
        .view-section { display: none; height: calc(100vh - 130px); overflow: hidden; padding: 0 30px 30px 30px; }
        .view-section.active { display: block; }

        /* --- WORKSPACE GRID --- */
        .workspace-grid { display: grid; grid-template-columns: 1fr 400px; gap: 30px; height: 100%; }
        .card { border-radius: 25px; display: flex; flex-direction: column; overflow: hidden; transition: transform 0.3s;}
        .card-header { padding: 20px 30px; border-bottom: 1px solid rgba(255,255,255,0.1); font-weight: 800; font-size: 14px; background: rgba(255,255,255,0.03); text-transform: uppercase; letter-spacing: 2px; color: var(--text-muted); }
        .card-body { padding: 35px; overflow-y: auto; flex: 1; display: flex; flex-direction: column; }

        /* --- RECORDER UI (IZQUIERDA) --- */
        .recorder-display { 
            flex: 1; display: flex; flex-direction: column; justify-content: center; align-items: center; 
            background: radial-gradient(circle at center, rgba(139, 92, 246, 0.1) 0%, rgba(0,0,0,0.4) 100%); 
            border-radius: 25px; border: 1px solid rgba(255,255,255,0.05); margin-bottom: 30px; 
            box-shadow: inset 0 0 60px rgba(0,0,0,0.4);
        }
        
        .status-text { font-size: 16px; font-weight: 800; color: var(--text-muted); letter-spacing: 3px; margin-bottom: 15px; text-transform: uppercase; background: rgba(0,0,0,0.5); padding: 8px 20px; border-radius: 20px; }
        .status-text.recording { color: white; background: var(--danger); box-shadow: 0 0 15px var(--danger); animation: pulseText 1.5s infinite; }
        @keyframes pulseText { 0% { opacity: 1; } 50% { opacity: 0.7; } 100% { opacity: 1; } }

        .timer-text { font-size: 85px; font-weight: 800; letter-spacing: 4px; margin-bottom: 25px; text-shadow: 0 10px 25px rgba(0,0,0,0.5); font-variant-numeric: tabular-nums; color: white; }
        
        .audio-wave { display: flex; justify-content: center; align-items: flex-end; gap: 8px; height: 80px; margin-bottom: 20px; opacity: 0.1; transition: 0.5s; filter: grayscale(1); }
        .audio-wave.active { opacity: 1; filter: grayscale(0); }
        .bar { width: 10px; background: linear-gradient(180deg, #ec4899 0%, var(--primary) 100%); border-radius: 5px; height: 15px; transition: height 0.1s ease; }
        
        .audio-wave.active .bar { animation: bounce 0.6s infinite alternate ease-in-out; box-shadow: 0 0 20px var(--primary); }
        .audio-wave.active .bar:nth-child(1) { animation-delay: 0.0s; } .audio-wave.active .bar:nth-child(2) { animation-delay: 0.2s; } .audio-wave.active .bar:nth-child(3) { animation-delay: 0.4s; } .audio-wave.active .bar:nth-child(4) { animation-delay: 0.1s; } .audio-wave.active .bar:nth-child(5) { animation-delay: 0.3s; } .audio-wave.active .bar:nth-child(6) { animation-delay: 0.5s; } .audio-wave.active .bar:nth-child(7) { animation-delay: 0.2s; }
        @keyframes bounce { 0% { height: 15px; } 100% { height: 80px; } }

        .controls-row { display: flex; gap: 25px; justify-content: center; }
        .btn-round { width: 90px; height: 90px; border-radius: 50%; border: none; cursor: pointer; display: flex; justify-content: center; align-items: center; transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); background: rgba(255,255,255,0.05); color: white; border: 2px solid rgba(255,255,255,0.1); backdrop-filter: blur(8px); }
        .btn-round:hover:not(:disabled) { background: rgba(255,255,255,0.15); transform: scale(1.1); }
        .btn-round:disabled { opacity: 0.3; cursor: not-allowed; transform: scale(1); }
        .btn-round svg { width: 40px; height: 40px; }

        .btn-record { background: rgba(244, 63, 94, 0.15); border-color: rgba(244, 63, 94, 0.5); color: var(--danger); }
        .btn-record:hover:not(:disabled) { background: var(--danger); color: white; border-color: var(--danger); box-shadow: 0 10px 25px rgba(244, 63, 94, 0.5); }
        .btn-stop { background: rgba(255,255,255,0.05); }
        .btn-stop:hover:not(:disabled) { background: white; color: black; box-shadow: 0 10px 25px rgba(255, 255, 255, 0.4); }

        /* --- DECORACIÓN Y GUARDADO (DERECHA) --- */
        .decoration-container { flex: 1; display: flex; justify-content: center; align-items: center; margin-bottom: 30px; }
        .magic-crystal { 
            width: 180px; height: 180px; border-radius: 50%; object-fit: cover; 
            border: 4px solid rgba(255,255,255,0.1); padding: 5px; background: rgba(0,0,0,0.3);
            box-shadow: 0 15px 35px rgba(0,0,0,0.5); transition: 0.5s;
            animation: float 6s ease-in-out infinite;
        }
        .magic-crystal.recording {
            border-color: var(--primary);
            box-shadow: 0 0 40px rgba(139, 92, 246, 0.6), inset 0 0 20px rgba(139, 92, 246, 0.4);
            animation: spin 10s linear infinite;
        }

        @keyframes float { 0% { transform: translateY(0px); } 50% { transform: translateY(-15px); } 100% { transform: translateY(0px); } }
        @keyframes spin { 100% { transform: rotate(360deg); } }

        .form-label { font-size: 12px; font-weight: 800; color: var(--text-muted); display: block; margin-bottom: 10px; text-transform: uppercase; letter-spacing: 2px; text-align: center; }
        .form-input { 
            width: 100%; padding: 20px; border: 2px solid rgba(255,255,255,0.1); border-radius: 18px; 
            font-size: 18px; font-weight: 700; background: rgba(0,0,0,0.4); color: white; margin-bottom: 25px; 
            outline: none; transition: 0.3s; text-align: center; letter-spacing: 1px;
        }
        .form-input:focus { border-color: var(--primary); box-shadow: 0 0 25px rgba(139, 92, 246, 0.3); background: rgba(0,0,0,0.6); transform: scale(1.02); }
        
        .btn-save { 
            width: 100%; background: linear-gradient(135deg, var(--primary), #ec4899); color: white; border: none; 
            padding: 22px; border-radius: 18px; font-weight: 800; font-size: 16px; cursor: pointer; transition: 0.3s; letter-spacing: 2px; text-transform: uppercase;
        } 
        .btn-save:hover:not(:disabled) { box-shadow: 0 15px 30px rgba(139, 92, 246, 0.5); transform: translateY(-3px); }
        .btn-save:disabled { background: rgba(255,255,255,0.05); color: rgba(255,255,255,0.2); cursor: not-allowed; }

        /* --- HISTORY TABLE --- */
        .history-table { width: 100%; border-collapse: collapse; text-align: left; }
        .history-table th { background: rgba(0,0,0,0.5); padding: 20px 30px; font-size: 12px; color: var(--primary); text-transform: uppercase; letter-spacing: 2px; border-bottom: 1px solid var(--glass-border); }
        .history-table td { padding: 20px 30px; border-bottom: 1px solid rgba(255,255,255,0.03); font-size: 16px; font-weight: 600;}
        .history-table tbody tr { transition: 0.2s; }
        .history-table tbody tr:hover { background: rgba(255,255,255,0.05); transform: scale(1.01); }

        /* --- TOAST --- */
        .toast { position: absolute; bottom: -80px; left: 50%; transform: translateX(-50%); background: white; color: black; padding: 16px 35px; border-radius: 30px; font-weight: 800; font-size: 15px; box-shadow: 0 15px 35px rgba(0,0,0,0.3); transition: bottom 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275); z-index: 100; display: flex; align-items: center; gap: 12px; }
        .toast.show { bottom: 50px; }

        svg { width: 24px; height: 24px; }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.2); border-radius: 10px; }
    </style>
</head>
<body>

    <div class="sidebar glass-panel">
        <img src="https://www.ghibli.jp/gallery/howl014.jpg" alt="Logo" class="brand-logo">
        <div class="nav-item active" onclick="switchView('workspace', this)" title="Grabadora">
            <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z"></path></svg>
        </div>
        <div class="nav-item" onclick="switchView('history', this)" title="Historial de Archivos">
            <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 002-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path></svg>
        </div>
    </div>

    <div class="main-wrapper">
        <header class="top-header glass-panel">
            <div class="header-title" id="top-title">
                <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z"></path></svg>
                VoiceVault ✦ Studio
            </div>
            <div class="avatar">
                <img src="https://www.ghibli.jp/gallery/howl012.jpg" alt="User" style="width: 100%; height: 100%; border-radius: 50%; object-fit: cover;">
            </div>
        </header>

        <div id="view-workspace" class="view-section active">
            <div class="workspace-grid">
                
                <!-- PANEL GRABADOR (IZQUIERDA) -->
                <div class="card glass-panel">
                    <div class="card-header">Panel de Grabación Master</div>
                    <div class="card-body">
                        <div class="recorder-display">
                            <div class="status-text" id="status-text">ESPERANDO INICIO</div>
                            <div class="timer-text" id="timer">00:00:00</div>
                            <div class="audio-wave" id="audio-wave">
                                <div class="bar"></div><div class="bar"></div><div class="bar"></div><div class="bar"></div>
                                <div class="bar"></div><div class="bar"></div><div class="bar"></div>
                            </div>
                        </div>
                        <div class="controls-row">
                            <button class="btn-round btn-record" id="btn-record" onclick="startRecording()" title="Iniciar Grabación">
                                <svg fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M7 4a3 3 0 016 0v4a3 3 0 11-6 0V4zm4 10.93A7.001 7.001 0 0017 8a1 1 0 10-2 0A5 5 0 015 8a1 1 0 00-2 0 7.001 7.001 0 006 6.93V17H6a1 1 0 100 2h8a1 1 0 100-2h-3v-2.07z" clip-rule="evenodd"></path></svg>
                            </button>
                            <button class="btn-round btn-stop" id="btn-stop" onclick="stopRecording()" disabled title="Detener Grabación">
                                <svg fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8 7a1 1 0 00-1 1v4a1 1 0 001 1h4a1 1 0 001-1V8a1 1 0 00-1-1H8z" clip-rule="evenodd"></path></svg>
                            </button>
                        </div>
                    </div>
                </div>

                <!-- PANEL GUARDADO Y DECORACIÓN (DERECHA) -->
                <div class="card glass-panel">
                    <div class="card-header">Exportar Archivo</div>
                    <div class="card-body" style="justify-content: center;">
                        
                        <!-- Imagen Decorativa (Cristal) -->
                        <div class="decoration-container">
                            <img src="https://www.ghibli.jp/gallery/howl017.jpg" alt="Magic Crystal" class="magic-crystal" id="magic-crystal">
                        </div>

                        <label class="form-label">Nombre del Archivo</label>
                        <input type="text" class="form-input" id="record-title" placeholder="Ej: ALVARADO PINEDO, FERNANDO" autocomplete="off">
                        
                        <button class="btn-save" id="btn-save" disabled onclick="saveRecordingData()">Extraer a Carpeta</button>
                    </div>
                </div>
            </div>
        </div>

        <div id="view-history" class="view-section">
            <div class="card glass-panel" style="height: 100%;">
                <div class="card-header">Archivos Locales Exportados</div>
                <div class="card-body" style="padding: 0;">
                    <table class="history-table">
                        <thead>
                            <tr>
                                <th>Fecha de Creación</th>
                                <th>Nombre del Archivo Guardado</th>
                                <th style="text-align: right;">Duración Total</th>
                            </tr>
                        </thead>
                        <tbody id="history-body"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <div id="toast" class="toast">
            <svg fill="currentColor" viewBox="0 0 20 20" style="color: var(--success);"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd"></path></svg>
            <span id="toast-msg">Operación Exitosa</span>
        </div>
    </div>

    <script>
        function switchView(viewId, element) {
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            element.classList.add('active');
            document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
            document.getElementById(`view-${viewId}`).classList.add('active');
            document.getElementById('top-title').innerHTML = viewId === 'workspace' 
                ? '<svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z"></path></svg> VoiceVault ✦ Studio' 
                : '<svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 002-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path></svg> Bóveda de Archivos';
        }

        function showToast(msg) {
            document.getElementById('toast-msg').innerText = msg;
            const toast = document.getElementById('toast');
            toast.classList.add('show');
            setTimeout(() => toast.classList.remove('show'), 4000);
        }

        let mediaRecorder;
        let audioChunks = [];
        let isRecording = false;
        let timerInt;
        let sec = 0;
        let lastDuration = "";
        let audioBlobFinal = null;

        const elTimer = document.getElementById('timer');
        const elWave = document.getElementById('audio-wave');
        const elStatus = document.getElementById('status-text');
        const elCrystal = document.getElementById('magic-crystal');
        const btnRecord = document.getElementById('btn-record');
        const btnStop = document.getElementById('btn-stop');
        const btnSave = document.getElementById('btn-save');

        async function startRecording() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];

                mediaRecorder.ondataavailable = event => { audioChunks.push(event.data); };
                
                mediaRecorder.onstop = () => {
                    audioBlobFinal = new Blob(audioChunks, { type: 'audio/webm' });
                    btnSave.disabled = false;
                    btnSave.innerHTML = "Extraer a Carpeta";
                    document.getElementById('record-title').focus();
                    showToast("Audio en memoria. Ponle un nombre y guárdalo.");
                };

                mediaRecorder.start();
                isRecording = true;
                btnRecord.disabled = true;
                btnStop.disabled = false;
                btnSave.disabled = true;
                
                elStatus.textContent = "● GRABANDO EN VIVO";
                elStatus.classList.add('recording');
                elWave.classList.add('active');
                elCrystal.classList.add('recording'); // Activa la animación del cristal

                sec = 0; elTimer.textContent = "00:00:00";
                timerInt = setInterval(() => {
                    sec++;
                    const h = String(Math.floor(sec / 3600)).padStart(2, '0');
                    const m = String(Math.floor((sec % 3600) / 60)).padStart(2, '0');
                    const s = String(sec % 60).padStart(2, '0');
                    elTimer.textContent = `${h}:${m}:${s}`;
                }, 1000);

            } catch (err) {
                alert("Permite el acceso al micrófono en tu navegador para continuar.");
            }
        }

        function stopRecording() {
            if(!isRecording) return;
            mediaRecorder.stop();
            mediaRecorder.stream.getTracks().forEach(track => track.stop());
            clearInterval(timerInt);
            lastDuration = elTimer.textContent;
            isRecording = false;

            btnRecord.disabled = false;
            btnStop.disabled = true;
            
            elStatus.textContent = "AUDIO PAUSADO / LISTO PARA EXPORTAR";
            elStatus.classList.remove('recording');
            elWave.classList.remove('active');
            elCrystal.classList.remove('recording'); // Detiene el giro del cristal
        }

        async function saveRecordingData() {
            let title = document.getElementById('record-title').value.trim();
            if(!title) title = "Audio_Generico";
            
            const fullFileName = `${title}.webm`;

            try {
                if (window.showSaveFilePicker) {
                    const handle = await window.showSaveFilePicker({
                        suggestedName: fullFileName,
                        types: [{ description: 'Archivo de Audio', accept: {'audio/webm': ['.webm']} }],
                    });
                    const writable = await handle.createWritable();
                    await writable.write(audioBlobFinal);
                    await writable.close();
                } else {
                    const audioUrl = URL.createObjectURL(audioBlobFinal);
                    const a = document.createElement('a');
                    a.href = audioUrl; a.download = fullFileName;
                    document.body.appendChild(a); a.click(); document.body.removeChild(a);
                }

                const dateObj = new Date();
                const dateStr = `${String(dateObj.getDate()).padStart(2,'0')}/${String(dateObj.getMonth()+1).padStart(2,'0')}/${dateObj.getFullYear()} ${String(dateObj.getHours()).padStart(2,'0')}:${String(dateObj.getMinutes()).padStart(2,'0')}`;

                const tbody = document.getElementById('history-body');
                const row = `<tr>
                    <td style="color: var(--text-muted); font-family: monospace;">${dateStr}</td>
                    <td style="color: white; font-weight: 800; font-size: 18px;">${title}</td>
                    <td style="font-family: monospace; font-size: 18px; color: var(--primary); text-align: right;">${lastDuration}</td>
                </tr>`;
                tbody.innerHTML = row + tbody.innerHTML; 

                showToast(`Audio guardado exitosamente`);

                elTimer.textContent = "00:00:00";
                elStatus.textContent = "ESPERANDO INICIO";
                document.getElementById('record-title').value = '';
                btnSave.disabled = true;
                btnSave.innerHTML = "Extraer a Carpeta";
                audioBlobFinal = null; 

            } catch (err) {
                showToast("Guardado cancelado por el usuario.");
            }
        }
    </script>
</body>
</html>
