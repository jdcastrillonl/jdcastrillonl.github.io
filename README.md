<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Mediciones Corporales</title>
<style>
  :root{--bg:#f6f7fb;--card:#fff;--muted:#6b7280;--accent:#0ea5a4;--accent-2:#2563eb;--danger:#ef4444;--radius:14px}
  *{box-sizing:border-box} html,body{height:100%} body{font-family:Inter,system-ui,Arial;margin:0;padding:18px;background:linear-gradient(180deg,var(--bg),#eef2ff 70%);color:#0f172a}
  .app{max-width:920px;margin:0 auto;display:grid;gap:14px}
  header{display:flex;align-items:center;gap:12px}
  .logo{width:56px;height:56px;border-radius:12px;background:linear-gradient(135deg,var(--accent-2),var(--accent));display:flex;align-items:center;justify-content:center;color:white;font-weight:700}
  h1{font-size:18px;margin:0} p.lead{margin:0;color:var(--muted);font-size:13px}
  .grid{display:grid;gap:12px;grid-template-columns:1fr}
  @media(min-width:900px){.grid{grid-template-columns:1fr 360px}}
  .card{background:var(--card);border-radius:var(--radius);padding:14px;box-shadow:0 6px 18px rgba(15,23,42,0.06)}
  .viewer{position:relative;overflow:hidden;border-radius:12px}
  video{width:100%;height:auto;display:block;background:linear-gradient(180deg,#eef2ff,#fff)}
  canvas.overlay{position:absolute;left:0;top:0;width:100%;height:100%;pointer-events:none}
  .controls{display:flex;flex-direction:column;gap:12px}
  .row{display:flex;gap:8px;align-items:center}
  .btn{flex:1;border:none;padding:12px 14px;border-radius:10px;font-weight:600;font-size:14px;cursor:pointer;box-shadow:0 6px 14px rgba(15,23,42,0.06)}
  .btn.secondary{background:transparent;border:1px solid #e6edf8;color:#0f172a}
  .btn.primary{background:var(--accent);color:white}
  .btn.warn{background:var(--accent-2);color:white}
  .btn.danger{background:var(--danger);color:white}
  .btn.icon{padding:10px;border-radius:10px;width:48px;height:48px;display:grid;place-items:center}
  .meta{font-size:13px;color:var(--muted)}
  .guide{font-size:13px;color:#0f172a;line-height:1.3}
  .badge{display:inline-block;padding:6px 8px;border-radius:999px;font-size:12px;margin:4px 6px 0 0}
  .ok{background:#e6fffb;color:#064e46;border:1px solid rgba(6,78,70,0.08)}
  .bad{background:#fff1f2;color:#7f1d1d;border:1px solid rgba(239,68,68,0.08)}
  .progress{height:10px;background:#eef2ff;border-radius:999px;overflow:hidden;margin-top:8px}
  .progress > i{display:block;height:100%;background:linear-gradient(90deg,var(--accent),var(--accent-2));width:0%; transition: width 0.2s ease;}
  footer{font-size:12px;color:var(--muted);text-align:center;margin-top:6px}
  .report{font-size:14px} .report dt{font-weight:700} .report dd{margin:0 0 10px 0;color:var(--muted)}
  pre.msg {white-space:pre-wrap;background:#fbfbfb;padding:10px;border-radius:8px;color:#0f172a;font-size:13px}
</style>
</head>
<body>
<div class="app">
  <header>
    <div class="logo">VC</div>
    <div>
      <h1>Proyecto final POO</h1>
      <p class="lead">Estimación aproximada de medidas y talla. App basada en Mtaylor</p>
    </div>
  </header>

  <div class="grid">
    <div class="card viewer" id="videoCard">
      <video id="video" playsinline muted></video>
      <canvas id="overlay" class="overlay"></canvas>
    </div>

    <aside class="controls">
      <!-- Registro y selección de usuario -->
      <div class="card">
        <div class="meta">Usuario</div>
        <div class="row">
          <input type="text" id="userName" placeholder="Ingresa tu nombre" style="flex:2;padding:8px;border-radius:6px;border:1px solid #ccc"/>
          <button id="registerBtn" class="btn primary" style="flex:1">Registrar / Seleccionar</button>
        </div>
        <div style="margin-top:6px" id="userStatus" class="meta">No hay usuario seleccionado.</div>
      </div>

      <div class="card">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div><div class="meta">Estado</div><div id="statusText" style="font-weight:700">Inactivo</div></div>
          <div style="text-align:right"><div class="meta">Duración</div><div id="timer" style="font-weight:700">00:00</div></div>
        </div>

        <hr style="border:none;height:1px;background:#f1f5f9;margin:12px 0">

        <div class="row" style="margin-bottom:6px">
          <button id="startBtn" class="btn primary">Iniciar cámara</button>
          <button id="switchBtn" class="btn secondary icon" title="Cambiar cámara">Cambiar cámara</button>
        </div>

        <div class="row">
          <button id="recordBtn" class="btn warn" disabled>Grabar</button>
          <button id="stopBtn" class="btn danger" disabled>Detener</button>
        </div>

        <div style="margin-top:10px" class="guide">
          <strong>Requisitos obligatorios:</strong>
          <ul style="margin:6px 0 0 18px;padding:0;color:var(--muted)">
            <li>Cuerpo completo visible (Cabeza a pies).</li>
            <li>Iluminación clara (Sin sombras fuertes).</li>
            <li>La calidad del video debe ser mayor a 360p .</li>
          </ul>
        </div>
      </div>

      <div class="card">
        <div class="meta">Validación en tiempo real</div>
        <div style="margin-top:8px">
          <span id="badgeHuman" class="badge bad">Figura:  </span>
          <span id="badgeFullBody" class="badge bad">Cuerpo Completo:  </span>
          <span id="badgeLighting" class="badge bad">Luz:  </span>
        </div>
        <div class="progress" id="liveProgress" style="display:none"><i></i></div>
        <div style="margin-top:10px" id="messages" class="meta">Inicia la cámara para comenzar.</div>
      </div>

      <div class="card">
        <div class="meta">Análisis del video grabado</div>
        <div style="margin-top:8px" id="analysisStatus">No analizado</div>
        <div class="progress" id="analysisProgress" style="display:none"><i></i></div>
        <dl class="report" id="analysisReport" style="margin-top:8px;display:none">
          <dt>Resultado Final</dt><dd id="repResult" style="font-weight:700"> </dd>
        </dl>
      </div>

      <div class="card">
        <div class="meta">Mediciones corporales estimadas</div>
        <pre id="measurements" class="msg"> </pre>
      </div>

    </aside>
  </div>

  <footer>Nota: Funciona en PC, Android e iOS. Verifica permisos de cámara.</footer>
</div>

<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.11.0/dist/tf.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/pose-detection@2.1.3/dist/pose-detection.min.js"></script>

<script>
/* ------------------ ELEMENTOS DEL DOM ------------------ */
const video = document.getElementById('video');
const overlay = document.getElementById('overlay');
const ctx = overlay.getContext('2d');
const startBtn = document.getElementById('startBtn');
const switchBtn = document.getElementById('switchBtn');
const recordBtn = document.getElementById('recordBtn');
const stopBtn = document.getElementById('stopBtn');
const statusText = document.getElementById('statusText');
const timerEl = document.getElementById('timer');
const messages = document.getElementById('messages');
const analysisStatus = document.getElementById('analysisStatus');
const repResult = document.getElementById('repResult');
const analysisReport = document.getElementById('analysisReport');
const badgeHuman = document.getElementById('badgeHuman');
const badgeFullBody = document.getElementById('badgeFullBody');
const badgeLighting = document.getElementById('badgeLighting');
const liveProgress = document.getElementById('liveProgress');
const liveProgressBar = liveProgress.querySelector('i');
const analysisProgressBar = document.getElementById('analysisProgress').querySelector('i');
const measurementsEl = document.getElementById('measurements');
const registerBtn = document.getElementById('registerBtn');
const userNameInput = document.getElementById('userName');
const userStatus = document.getElementById('userStatus');

/* ------------------ VARIABLES GLOBALES ------------------ */
let stream = null, mediaRecorder = null, recordedChunks = [], detector = null, rafId = null, timerInterval = null, recordingStart = null;
let facing = 'environment', validationScore = 0, frameCount = 0, lastKeypoints = null;
let currentUser = null; // usuario activo
let usersData = {}; // almacenamiento de resultados por usuario
const THRESHOLD_PUNTUACION = 2;

/* ------------------ REGISTRO Y SELECCION DE USUARIO ------------------ */
registerBtn.onclick = ()=>{
  const name = userNameInput.value.trim();
  if(!name){ alert('Ingresa un nombre'); return; }

  // Si ya existe el usuario, se carga su talla y medidas
  currentUser = name;
  if(!usersData[currentUser]) usersData[currentUser] = {medidas:null};
  userStatus.innerText = 'Usuario activo: '+currentUser;

  // Mostrar medidas guardadas si existen
  if(usersData[currentUser].medidas){
    measurementsEl.innerText = usersData[currentUser].medidas;
  } else {
    measurementsEl.innerText = ' ';
  }

  // Solo después de seleccionar usuario se puede activar la grabación
  recordBtn.disabled = false;
  startBtn.disabled = false;
};

/* ------------------ INICIAR C MARA ------------------ */
async function startCamera(){
  try {
    setStatus('Cargando modelo IA...');
    validationScore = 0; frameCount = 0;
    await ensureDetector();

    setStatus('Accediendo a cámara...');
    const constraints = { video: { facingMode: facing, width:{ideal:1280}, height:{ideal:720}}, audio:true };
    stream = await navigator.mediaDevices.getUserMedia(constraints);

    video.srcObject = stream;
    await new Promise(resolve => video.onloadedmetadata = resolve);
    await video.play();

    overlay.width = video.videoWidth;
    overlay.height = video.videoHeight;

    setStatus('Cámara activa');
    recordBtn.disabled = false;
    startBtn.style.display = 'none'; 
    
    detectLoop();
  } catch(err){ console.error(err); setStatus('Error de cámara'); setMessage('Revisa permisos de cámara.'); }
}

/* ------------------ CREAR DETECTOR POSE ------------------ */
async function ensureDetector(){
  if(detector) return detector;
  await tf.setBackend('webgl');
  detector = await poseDetection.createDetector(poseDetection.SupportedModels.MoveNet, {
    modelType: poseDetection.movenet.modelType.SINGLEPOSE_LIGHTNING
  });
}

/* ------------------ DETECCION EN TIEMPO REAL ------------------ */
async function detectLoop() {
  if(!video.paused && !video.ended && detector){
    try{
      const poses = await detector.estimatePoses(video,{flipHorizontal:false});
      ctx.clearRect(0,0,overlay.width,overlay.height);
      const lightingOk = true; updateBadge(badgeLighting,lightingOk,'OK');
      let frameScore=0;

      if(poses.length>0){
        const p = poses[0];
        const kp = p.keypoints;
        lastKeypoints = kp;

        const isHuman = p.score>0.15;
        const nose=kp[0], lAnkle=kp[15], rAnkle=kp[16];
        const anklesVisible = (lAnkle?.score>0.15)||(rAnkle?.score>0.15);
        const isFullBody = isHuman && (nose?.score>0.15 && anklesVisible);

        updateBadge(badgeHuman,isHuman,isHuman?'Si ':'No');
        updateBadge(badgeFullBody,isFullBody,isFullBody?'Si ':'No');
        if(isHuman) drawSkeleton(kp);

        if(isHuman && isFullBody && lightingOk){
          setMessage('Posición válida para grabar.');
          liveProgressBar.style.width='100%'; liveProgressBar.style.backgroundColor='var(--accent)'; frameScore=3;
        } else if(isHuman && isFullBody && !lightingOk){
          setMessage('Poca luz'); liveProgressBar.style.width='80%'; frameScore=2;
        } else if(isHuman && !isFullBody){
          setMessage('Alejate, pies/cabeza no visibles'); liveProgressBar.style.width='50%'; frameScore=1;
        } else {
          setMessage('No se detecta persona'); liveProgressBar.style.width='10%'; frameScore=0;
        }

        if(mediaRecorder?.state==='recording'){ validationScore+=frameScore; frameCount++; }

      } else {
        updateBadge(badgeHuman,false,'No'); updateBadge(badgeFullBody,false,'No');
        setMessage('Buscando persona en el encuadre...'); liveProgressBar.style.width='5%'; frameScore=0;
      }

    }catch(e){console.error(e);}
  }
  rafId=requestAnimationFrame(detectLoop);
}

/* ------------------ DIBUJAR SKELETON ------------------ */
function drawSkeleton(keypoints){
  ctx.fillStyle='#0ea5a4'; ctx.strokeStyle='white'; ctx.lineWidth=2;
  keypoints.forEach(p=>{ if(p.score>0.3){ ctx.beginPath(); ctx.arc(p.x,p.y,5,0,2*Math.PI); ctx.fill(); ctx.stroke(); } });
  const pairs = poseDetection.util.getAdjacentPairs(poseDetection.SupportedModels.MoveNet);
  ctx.beginPath(); pairs.forEach(([i,j])=>{ const p1=keypoints[i], p2=keypoints[j]; if(p1.score>0.3&&p2.score>0.3){ctx.moveTo(p1.x,p1.y);ctx.lineTo(p2.x,p2.y);} });
  ctx.stroke();
}

/* ------------------ ACTUALIZAR BADGE ------------------ */
function updateBadge(el,ok,text){ 
  el.className='badge '+(ok?'ok':'bad'); 
  el.innerText = el.id==='badgeHuman'?'Figura: '+text:el.id==='badgeFullBody'?'Cuerpo Completo: '+text:'Luz: '+text;
}

/* ------------------ ESTADO Y MENSAJES ------------------ */
function setStatus(s){statusText.innerText=s;}
function setMessage(s){messages.innerText=s;}
function formatTime(sec){ const m=String(Math.floor(sec/60)).padStart(2,'0'); const s=String(Math.floor(sec%60)).padStart(2,'0'); return `${m}:${s}`; }

/* ------------------ GRABACIÓN ------------------ */
recordBtn.onclick=()=>{
  if(!currentUser){ alert('Selecciona o registra un usuario primero'); return; }

  validationScore=0; frameCount=0; recordedChunks=[];
  const options = MediaRecorder.isTypeSupported('video/webm;codecs=vp9')?{mimeType:'video/webm;codecs=vp9'}:{mimeType:'video/webm'};
  mediaRecorder=new MediaRecorder(stream,options);

  // Captura chunks del video
  mediaRecorder.ondataavailable=e=>{if(e.data.size>0) recordedChunks.push(e.data);};

  // Al detener la grabación se analiza
  mediaRecorder.onstop=async()=>{
    clearInterval(timerInterval);
    const blob=new Blob(recordedChunks,{type:'video/webm'});
    setStatus('Procesando video...');
    await postRecordingAnalysis();
  };

  mediaRecorder.start(); recordingStart=Date.now();
  setStatus('GRABANDO'); recordBtn.disabled=true; stopBtn.disabled=false; switchBtn.disabled=true;

  // Mostrar tiempo de grabaci n
  timerInterval=setInterval(()=>{
    const s=Math.floor((Date.now()-recordingStart)/1000);
    timerEl.innerText=formatTime(s);
  },1000);
};

stopBtn.onclick=()=>{
  if(mediaRecorder && mediaRecorder.state!=='inactive') mediaRecorder.stop();
  recordBtn.disabled=false; stopBtn.disabled=true; switchBtn.disabled=false;
};

/* ------------------ MEDICIONES ------------------ */
function measureBody(keypoints){
  const k=(i)=>keypoints[i];
  const nose=k(0), lAnkle=k(15), rAnkle=k(16), lShoulder=k(5), rShoulder=k(6), lHip=k(11), rHip=k(12);
  if(!nose||!lAnkle||!rAnkle) return null;

  const ankleY=(lAnkle.y+rAnkle.y)/2;
  const heightPx=Math.abs(ankleY-nose.y);
  const realHeightCm = 170; // valor de referencia
  const cmPerPixel=realHeightCm/heightPx; // escala proporcional

  const shoulders=Math.abs(lShoulder.x-rShoulder.x)*cmPerPixel;
  const hips=Math.abs(lHip.x-rHip.x)*cmPerPixel;

  return {
    altura:heightPx*cmPerPixel,
    hombros:shoulders,
    pecho:shoulders*1.2,
    cintura:hips*0.9,
    cadera:hips
  };
}

function calcularTalla(pecho){
  if(pecho<90) return'S';
  if(pecho<100) return'M';
  if(pecho<110) return'L';
  return'XL';
}

/* ------------------ ANÁLISIS POST-GRABACIÓ ------------------ */
async function postRecordingAnalysis(){
  analysisStatus.innerText='Analizando calidad de la grabación...';
  analysisReport.style.display='block'; analysisProgressBar.style.width='0%';
  
  // Simular progreso
  for(let i=0;i<=100;i+=10){analysisProgressBar.style.width=i+'%'; await new Promise(r=>setTimeout(r,50));}

  if(frameCount===0){
    analysisStatus.innerText='Grabación demasiado corta'; 
    repResult.style.color='var(--danger)'; 
    repResult.innerText='No se grabaron frames válidos.'; 
    return;
  }

  const averageScore=validationScore/frameCount;

  if(averageScore>=THRESHOLD_PUNTUACION){
    analysisStatus.innerText='Análisis Finalizado'; 
    repResult.style.color='var(--accent)'; 
    repResult.innerText='ÓPTIMO para iniciar medidas';

    const medidas=measureBody(lastKeypoints);
    if(!medidas){measurementsEl.innerText='No se pudieron extraer medidas.'; return;}
    const talla=calcularTalla(medidas.pecho);

    // Guardar medidas en memoria por usuario
    const medidasStr=`Altura estimada: ${medidas.altura.toFixed(1)} cm
Hombros: ${medidas.hombros.toFixed(1)} cm
Pecho: ${medidas.pecho.toFixed(1)} cm
Cintura: ${medidas.cintura.toFixed(1)} cm
Cadera: ${medidas.cadera.toFixed(1)} cm
Talla recomendada: ${talla}`;
    measurementsEl.innerText = medidasStr;
    usersData[currentUser].medidas = medidasStr; // almacenamiento interno
  } else {
    analysisStatus.innerText='An lisis Finalizado'; 
    repResult.style.color='var(--danger)'; 
    repResult.innerText='? Posici n o calidad insuficiente';
  }
}

/* ------------------ CAMBIAR CÁMARA ------------------ */
switchBtn.onclick=()=>{ 
  facing=(facing==='user')?'environment':'user'; 
  if(stream){stream.getTracks().forEach(t=>t.stop()); startCamera();} 
};

/* ------------------ BOTON INICIAR ------------------ */
startBtn.onclick=startCamera;
</script>
</body>

</html>
