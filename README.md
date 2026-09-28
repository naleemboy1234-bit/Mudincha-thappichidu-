<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Mudincha Thappichidu</title>
<style>
:root{--panel:rgba(14,22,48,.8);--card:#1b2a55;--text:#fff;--muted:#b8c6ee;--accent:#ffd23f;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--panel:rgba(8,12,28,.85);--card:#131d3d}}
:root[data-theme="dark"]{--panel:rgba(8,12,28,.85);--card:#131d3d}
html{height:100%;background:#000;scroll-padding-top:env(safe-area-inset-top,0px)}
body{height:100%;margin:0;overflow:hidden;background:#000;color:var(--text);font-family:'Segoe UI',system-ui,Arial,sans-serif;user-select:none;-webkit-user-select:none;touch-action:none}
#canvasHolder{position:fixed;inset:0}canvas{display:block;width:100%;height:100%}
.overlay{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;gap:12px;text-align:center;z-index:10;overflow-y:auto;touch-action:pan-y;
padding:calc(18px + env(safe-area-inset-top,0px)) 18px calc(18px + env(safe-area-inset-bottom,0px))}
.overlay>:first-child{margin-top:auto}.overlay>:last-child{margin-bottom:auto}
#mainMenu{background:linear-gradient(180deg,rgba(10,20,50,.78),rgba(10,20,50,.25) 55%,rgba(10,20,50,.7))}
#charScreen,#pauseScreen,#overScreen{background:var(--panel)}
.hidden{display:none!important}
h1{font-size:clamp(1.7em,7vw,2.8em);margin:0;color:var(--accent);text-shadow:2px 3px 0 #7a3e00;letter-spacing:1px}
h2{margin:0;font-weight:400;color:#dbe9ff}
.btn{background:linear-gradient(180deg,#ffdd55,#ff8800);border:0;padding:15px 0;width:min(280px,80vw);font-size:1.15em;border-radius:40px;color:#3a1e00;font-weight:800;cursor:pointer;box-shadow:0 6px 14px rgba(0,0,0,.4)}
.btn.alt{background:linear-gradient(180deg,#5aa0ff,#2a5fd0);color:#fff}
.btn:active{transform:scale(.95)}
.badge{background:rgba(0,0,0,.4);padding:8px 18px;border-radius:24px;font-weight:700;font-size:1.05em;border:1px solid rgba(255,210,63,.5)}
.small{font-size:.85em;color:var(--muted);max-width:360px;line-height:1.5}
#charGrid{display:grid;grid-template-columns:repeat(2,1fr);gap:12px;width:min(420px,100%)}
.card{background:var(--card);border-radius:16px;padding:12px 8px;border:2px solid transparent;display:flex;flex-direction:column;align-items:center;gap:4px}
.card.sel{border-color:var(--accent);box-shadow:0 0 14px rgba(255,210,63,.35)}
.card small{color:var(--muted)}
.cbtn{margin-top:4px;border:0;border-radius:20px;padding:9px 14px;font-weight:800;background:#ffcc33;color:#3a1e00;cursor:pointer;width:92%}
.cbtn.dis{background:#3c8f4a;color:#fff}.cbtn.poor{background:#777;color:#ddd}
.avatar{position:relative;width:56px;height:88px}
.avatar i{position:absolute;display:block}
.avatar .h{left:16px;top:0;width:24px;height:24px;border-radius:50%;background:var(--skin)}
.avatar .b{left:8px;top:26px;width:40px;height:34px;border-radius:8px;background:var(--shirt)}
.avatar .l{left:12px;top:60px;width:32px;height:28px;border-radius:0 0 6px 6px;background:var(--pants)}
#hud{position:fixed;top:calc(12px + env(safe-area-inset-top,0px));left:12px;right:12px;display:flex;justify-content:space-between;align-items:flex-start;z-index:5;font-weight:700;text-shadow:0 2px 4px rgba(0,0,0,.6);pointer-events:none}
#hud .box{background:rgba(0,0,0,.4);padding:7px 12px;border-radius:10px;margin-bottom:6px}
#pauseBtn{pointer-events:auto;background:rgba(0,0,0,.4);border:0;color:#fff;font-size:1.3em;width:44px;height:44px;border-radius:50%;cursor:pointer}
#powerRow{position:fixed;top:calc(100px + env(safe-area-inset-top,0px));left:12px;display:flex;gap:8px;z-index:5;pointer-events:none}
.pIcon{min-width:52px;height:38px;padding:0 8px;border-radius:19px;background:rgba(0,0,0,.4);display:flex;align-items:center;justify-content:center;gap:4px;font-weight:700;opacity:.3}
.pIcon.on{opacity:1;box-shadow:0 0 12px 3px gold}
#toast{position:fixed;left:50%;bottom:calc(30px + env(safe-area-inset-bottom,0px));transform:translateX(-50%);background:#000c;padding:10px 20px;border-radius:20px;z-index:30;font-weight:700;opacity:0;transition:opacity .25s;pointer-events:none;text-align:center}
#toast.show{opacity:1}
#overTitle{color:#ff6b6b}
.stat{font-size:1.25em}
</style>
</head>
<body>
<div id="canvasHolder"></div>

<div id="hud" class="hidden">
  <div><div class="box">🏃 <span id="hDist">0</span> m</div><div class="box">🪙 <span id="hRun">0</span> · Total <span id="hTot">0</span></div></div>
  <button id="pauseBtn" aria-label="Pause">⏸</button>
</div>
<div id="powerRow" class="hidden">
  <div class="pIcon" id="jIcon">⬆️<span id="jT"></span></div>
  <div class="pIcon" id="mIcon">🧲<span id="mT"></span></div>
</div>

<div id="mainMenu" class="overlay">
  <h1>MUDINCHA<br>THAPPICHIDU</h1>
  <h2>Escape Gopal, the cop!</h2>
  <div class="badge">🪙 <span class="coinTotal">0</span></div>
  <div class="small">Running as <b id="menuChar">Naleem</b> · Best: <b id="menuBest">0</b> m</div>
  <button class="btn" id="startBtn">▶ START</button>
  <button class="btn alt" id="charsBtn">👤 CHARACTERS</button>
  <button class="btn alt" id="muteBtn">🔊 SOUND ON</button>
  <div class="small">← → / A D: change lane · ↑ / W / Space: jump · ↓ / S: slide · P: pause<br>On phones, swipe left, right, up and down.</div>
</div>

<div id="charScreen" class="overlay hidden">
  <h1 style="font-size:2em">CHARACTERS</h1>
  <div class="badge">🪙 <span class="coinTotal">0</span> coins left</div>
  <div id="charGrid"></div>
  <button class="btn" id="backBtn">⬅ BACK</button>
</div>

<div id="pauseScreen" class="overlay hidden">
  <h1>PAUSED</h1>
  <button class="btn" id="resumeBtn">▶ RESUME</button>
  <button class="btn alt" id="quitBtn">🏠 MAIN MENU</button>
</div>

<div id="overScreen" class="overlay hidden">
  <h1 id="overTitle">GOPAL GOT YOU!</h1>
  <div class="stat">🏁 <span id="oDist">0</span> m <span id="oNew" class="small"></span></div>
  <div class="stat">🪙 +<span id="oRun">0</span> this run</div>
  <div class="badge">Total coins: <span class="coinTotal">0</span></div>
  <button class="btn" id="againBtn">↻ RUN AGAIN</button>
  <button class="btn alt" id="charsBtn2">👤 CHARACTERS</button>
  <button class="btn alt" id="menuBtn2">🏠 MAIN MENU</button>
</div>
<div id="toast"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
(function(){
"use strict";
const $=id=>document.getElementById(id);
if(typeof THREE==='undefined'){document.body.innerHTML='<p style="padding:24px">Could not load the 3D engine. Check your connection and reload.</p>';return;}

/* ---------- Save data (coins persist between runs) ---------- */
const KEY='mudincha_thappichidu_v2';
let save={coins:0,owned:['naleem'],selected:'naleem',best:0,muted:false};
try{const s=JSON.parse(localStorage.getItem(KEY)||'null');if(s&&typeof s==='object')save=Object.assign(save,s);}catch(e){}
if(!Array.isArray(save.owned))save.owned=[];
if(!save.owned.includes('naleem'))save.owned.push('naleem');
function persist(){try{localStorage.setItem(KEY,JSON.stringify(save));}catch(e){}}
addEventListener('pagehide',persist);

/* ---------- Characters ---------- */
const hex=n=>'#'+n.toString(16).padStart(6,'0');
const CHARS=[
 {id:'naleem',name:'Naleem',price:0,shirt:0xd62828,pants:0x1d2b53,hair:0x1a1a1a,skin:0xf0b98d,pack:0xf2c94c},
 {id:'shahid',name:'Shahid',price:200,shirt:0x1e6fe0,pants:0x2b2b33,hair:0x3a2414,skin:0xe0a677,pack:0xffffff},
 {id:'anuj',name:'Anuj',price:250,shirt:0x151515,pants:0x4a5060,hair:0x0d0d0d,skin:0xd9a074,pack:0xd62828},
 {id:'shakeel',name:'Shakeel',price:200,shirt:0x1f9d55,pants:0x1f2a44,hair:0x161616,skin:0xc98d5f,pack:0x222222,beard:true}
];
const charById=id=>CHARS.find(c=>c.id===id)||CHARS[0];

/* ---------- Sound ---------- */
let AC=null;
function tone(f,d,type,v,to){
 if(save.muted)return;
 try{if(!AC)AC=new(window.AudioContext||window.webkitAudioContext)();if(AC.state==='suspended')AC.resume();
  const o=AC.createOscillator(),g=AC.createGain(),t=AC.currentTime;o.type=type||'sine';o.frequency.setValueAtTime(f,t);
  if(to)o.frequency.exponentialRampToValueAtTime(to,t+d);g.gain.setValueAtTime(v||.07,t);g.gain.exponentialRampToValueAtTime(.0001,t+d);
  o.connect(g);g.connect(AC.destination);o.start(t);o.stop(t+d);}catch(e){}
}

/* ---------- Renderer / scene ---------- */
let renderer;
try{renderer=new THREE.WebGLRenderer({antialias:true});}catch(e){document.body.innerHTML='<p style="padding:24px">WebGL is not available on this device/browser.</p>';return;}
renderer.setPixelRatio(Math.min(devicePixelRatio||1,2));
renderer.setSize(innerWidth,innerHeight);
renderer.shadowMap.enabled=true;renderer.shadowMap.type=THREE.PCFSoftShadowMap;
$('canvasHolder').appendChild(renderer.domElement);
const scene=new THREE.Scene();
(function(){const c=document.createElement('canvas');c.width=2;c.height=256;const x=c.getContext('2d'),g=x.createLinearGradient(0,0,0,256);
 g.addColorStop(0,'#3f86e0');g.addColorStop(.55,'#a8d8ff');g.addColorStop(1,'#ffe9c7');x.fillStyle=g;x.fillRect(0,0,2,256);scene.background=new THREE.CanvasTexture(c);})();
scene.fog=new THREE.Fog(0xd6e8f5,30,115);
const camera=new THREE.PerspectiveCamera(60,innerWidth/innerHeight,.1,220);
addEventListener('resize',()=>{camera.aspect=innerWidth/innerHeight;camera.updateProjectionMatrix();renderer.setSize(innerWidth,innerHeight);});
scene.add(new THREE.HemisphereLight(0xdcecff,0x606875,.8));
const sun=new THREE.DirectionalLight(0xfff0d0,.85);sun.position.set(-9,18,10);sun.castShadow=true;sun.shadow.mapSize.set(1024,1024);
Object.assign(sun.shadow.camera,{left:-16,right:16,top:26,bottom:-26,near:1,far:60});scene.add(sun);
const sunBall=new THREE.Mesh(new THREE.SphereGeometry(7,20,16),new THREE.MeshBasicMaterial({color:0xfff3c4,fog:false}));sunBall.position.set(-35,38,-150);scene.add(sunBall);

const MAT=(c,o)=>new THREE.MeshStandardMaterial(Object.assign({color:c,roughness:.7,metalness:.05},o||{}));
const box=(w,h,d,m)=>{const x=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m);x.castShadow=true;return x;};
const rnd=n=>Math.floor(Math.random()*n);
const LANE=[-2.4,0,2.4];

/* ---------- Road segments (road, sidewalks, dashes, lamps) ---------- */
const SEG=20,NSEG=7,segs=[];
const roadM=MAT(0x3a3c44,{roughness:.95}),walkM=MAT(0x9c9ca6,{roughness:.9}),dashM=MAT(0xf5f5f5),edgeM=MAT(0xffd23f);
const poleM=MAT(0x555a66,{metalness:.5}),bulbM=MAT(0xfff2b0,{emissive:0xffe08a,emissiveIntensity:.9});
const poleG=new THREE.CylinderGeometry(.06,.09,4.2,8),bulbG=new THREE.SphereGeometry(.17,10,8);
for(let i=0;i<NSEG;i++){
 const g=new THREE.Group();
 const road=new THREE.Mesh(new THREE.PlaneGeometry(9,SEG),roadM);road.rotation.x=-Math.PI/2;road.receiveShadow=true;g.add(road);
 [-1,1].forEach(s=>{
  const w=new THREE.Mesh(new THREE.BoxGeometry(4,.22,SEG),walkM);w.position.set(s*6.5,.11,0);w.receiveShadow=true;g.add(w);
  const e=new THREE.Mesh(new THREE.PlaneGeometry(.18,SEG),edgeM);e.rotation.x=-Math.PI/2;e.position.set(s*4.2,.012,0);g.add(e);
  const p=new THREE.Mesh(poleG,poleM);p.position.set(s*4.9,2.3,0);p.castShadow=true;g.add(p);
  const b=new THREE.Mesh(bulbG,bulbM);b.position.set(s*4.9,4.4,0);g.add(b);
  for(let k=0;k<4;k++){const d=new THREE.Mesh(new THREE.PlaneGeometry(.12,2.6),dashM);d.rotation.x=-Math.PI/2;d.position.set(s*1.2,.013,-7.5+k*5);g.add(d);}
 });
 g.position.z=10-i*SEG;scene.add(g);segs.push(g);
}

/* ---------- Buildings ---------- */
const BN=8,BSP=22,blds=[],bGeo=new THREE.BoxGeometry(1,1,1);
function winTex(){const c=document.createElement('canvas');c.width=c.height=64;const x=c.getContext('2d');x.fillStyle='#39414f';x.fillRect(0,0,64,64);
 for(let i=0;i<4;i++)for(let j=0;j<4;j++){x.fillStyle=Math.random()<.35?'#ffe08a':'#9fc4e8';x.fillRect(i*16+3,j*16+4,10,9);}
 const t=new THREE.CanvasTexture(c);t.wrapS=t.wrapT=THREE.RepeatWrapping;return t;}
function placeB(b,z){const w=5+Math.random()*4,h=9+Math.random()*16,d=9+Math.random()*3;
 b.m.scale.set(w,h,d);b.m.position.set(b.s*(9.5+w/2+Math.random()*2),h/2,z);b.tex.repeat.set(Math.max(1,Math.round(w/3)),Math.max(1,Math.round(h/4)));}
[-1,1].forEach(s=>{for(let i=0;i<BN;i++){
 const tex=winTex(),m=new THREE.Mesh(bGeo,new THREE.MeshStandardMaterial({map:tex,color:[0xffffff,0xffe0cc,0xd6e4ff,0xe8ffe0][rnd(4)],roughness:.85}));
 const b={m,tex,s};placeB(b,-i*BSP-Math.random()*6);scene.add(m);blds.push(b);}});

/* ---------- Characters (3D humanoids with pivoted limbs) ---------- */
function limb(rad,len,mat,x,y,shoe,sleeve){
 const p=new THREE.Group();p.position.set(x,y,0);
 const m=new THREE.Mesh(new THREE.CylinderGeometry(rad,rad*.85,len,10),mat);m.position.y=-len/2;m.castShadow=true;p.add(m);
 if(shoe){const s=new THREE.Mesh(new THREE.BoxGeometry(.21,.13,.36),MAT(0xf4f4f4));s.position.set(0,-len+.03,-.05);s.castShadow=true;p.add(s);}
 if(sleeve){const s=new THREE.Mesh(new THREE.CylinderGeometry(rad+.025,rad+.03,.24,10),sleeve);s.position.y=-.12;p.add(s);}
 return p;
}
function buildChar(o){
 const g=new THREE.Group(),skin=MAT(o.skin),shirt=MAT(o.shirt),pants=MAT(o.pants);
 const torso=box(.68,.74,.38,shirt);torso.position.y=1.2;g.add(torso);
 const belt=box(.7,.09,.4,MAT(0x111111));belt.position.y=.88;g.add(belt);
 const pack=box(.46,.52,.2,MAT(o.pack||0x333333));pack.position.set(0,1.27,.28);g.add(pack);
 const head=new THREE.Mesh(new THREE.SphereGeometry(.27,22,16),skin);head.position.y=1.8;head.castShadow=true;g.add(head);
 const hair=new THREE.Mesh(new THREE.SphereGeometry(.285,22,12,0,Math.PI*2,0,Math.PI*.55),MAT(o.hair));hair.position.y=1.82;hair.rotation.x=.3;g.add(hair);
 [-.1,.1].forEach(x=>{const e=new THREE.Mesh(new THREE.SphereGeometry(.035,8,6),MAT(0x111111));e.position.set(x,1.82,-.245);g.add(e);});
 if(o.beard){const b=new THREE.Mesh(new THREE.SphereGeometry(.285,18,10,0,Math.PI*2,Math.PI*.6,Math.PI*.4),MAT(o.hair));b.position.y=1.8;g.add(b);}
 const legL=limb(.13,.86,pants,-.17,.86,true),legR=limb(.13,.86,pants,.17,.86,true);
 const armL=limb(.09,.62,skin,-.43,1.5,false,shirt),armR=limb(.09,.62,skin,.43,1.5,false,shirt);
 g.add(legL,legR,armL,armR);g.userData.p={legL,legR,armL,armR};return g;
}
function label(text,color){
 const c=document.createElement('canvas');c.width=256;c.height=72;const x=c.getContext('2d');
 x.fillStyle='rgba(0,0,0,.45)';x.fillRect(0,8,256,56);x.font='bold 38px Segoe UI,Arial';x.textAlign='center';x.fillStyle=color;x.fillText(text,128,50);
 const s=new THREE.Sprite(new THREE.SpriteMaterial({map:new THREE.CanvasTexture(c),transparent:true,fog:false}));s.scale.set(1.9,.53,1);return s;
}
const ringJ=new THREE.Mesh(new THREE.RingGeometry(.55,.7,28),new THREE.MeshBasicMaterial({color:0x33ff88,transparent:true,opacity:.8,side:THREE.DoubleSide}));
const ringM=new THREE.Mesh(new THREE.RingGeometry(.8,.92,28),new THREE.MeshBasicMaterial({color:0xff4466,transparent:true,opacity:.8,side:THREE.DoubleSide}));
[ringJ,ringM].forEach(r=>{r.rotation.x=-Math.PI/2;r.position.y=.06;r.visible=false;});
let player=null;
function setPlayerModel(def){
 if(player)scene.remove(player);
 player=buildChar(def);const lb=label(def.name.toUpperCase(),'#ffe27a');lb.position.y=2.75;player.add(lb,ringJ,ringM);scene.add(player);
}
const cop=buildChar({shirt:0xb89a5e,pants:0x9c8250,hair:0x111111,skin:0xc98d5f,pack:0x6b5a34});
(function(){
 const cap=new THREE.Mesh(new THREE.CylinderGeometry(.29,.31,.17,16),MAT(0x8c7443));cap.position.y=2.04;
 const brim=box(.36,.03,.2,MAT(0x2a2418));brim.position.set(0,1.97,-.27);
 const mus=box(.2,.045,.05,MAT(0x111111));mus.position.set(0,1.73,-.26);
 const baton=new THREE.Mesh(new THREE.CylinderGeometry(.035,.035,.55,8),MAT(0x3a2a18));baton.position.set(0,-.62,-.2);baton.rotation.x=1.3;
 cop.userData.p.armR.add(baton);
 const lb=label('GOPAL','#ff8a8a');lb.position.y=2.75;cop.add(cap,brim,mus,lb);cop.scale.setScalar(1.05);scene.add(cop);
})();

/* ---------- Shared geometry for pickups & obstacles ---------- */
const coinG=new THREE.CylinderGeometry(.3,.3,.07,22);coinG.rotateX(Math.PI/2);
const coinM=MAT(0xffc928,{metalness:.85,roughness:.25,emissive:0x553300,emissiveIntensity:.5});
const redM=MAT(0xe63946),whiteM=MAT(0xffffff),greyM=MAT(0x777d88,{metalness:.4}),yellowM=MAT(0xffb703),blackM=MAT(0x15151a),trainM=MAT(0x2b6cb0,{metalness:.3,roughness:.5}),glassM=MAT(0x0f1a2a,{metalness:.6,roughness:.2});
function mkBarrier(){const g=new THREE.Group();
 const pl=box(1.9,.34,.16,redM);pl.position.y=.62;g.add(pl);
 [-.6,0,.6].forEach(x=>{const s=box(.2,.36,.17,whiteM);s.position.set(x,.62,0);g.add(s);});
 [-.85,.85].forEach(x=>{const p=box(.12,.8,.12,greyM);p.position.set(x,.4,0);g.add(p);});return g;}
function mkGantry(){const g=new THREE.Group();
 [-.95,.95].forEach(x=>{const p=box(.14,1.9,.14,greyM);p.position.set(x,.95,0);g.add(p);});
 const b=box(2.1,.4,.25,yellowM);b.position.y=1.72;g.add(b);
 for(let i=0;i<5;i++){const s=box(.18,.42,.26,blackM);s.position.set(-.8+i*.4,1.72,0);s.rotation.z=.5;g.add(s);}return g;}
function mkTrain(){const g=new THREE.Group();
 const b=box(2.0,2.6,6,trainM);b.position.y=1.5;g.add(b);
 const roof=box(2.05,.15,6.05,MAT(0xdde3ea));roof.position.y=2.85;g.add(roof);
 const st=box(2.02,.2,6.02,yellowM);st.position.y=.75;g.add(st);
 for(let k=0;k<4;k++)[-1,1].forEach(s=>{const w=box(.03,.75,1.0,glassM);w.position.set(s*1.005,1.9,-2.2+k*1.45);g.add(w);});
 const f=box(1.7,.9,.05,glassM);f.position.set(0,1.95,3.02);g.add(f);
 [-.6,.6].forEach(x=>{const l=new THREE.Mesh(new THREE.SphereGeometry(.13,10,8),MAT(0xfff6c0,{emissive:0xffee99,emissiveIntensity:1}));l.position.set(x,1.0,3.04);g.add(l);});return g;}
function mkPower(t){
 if(t==='jump'){const g=new THREE.Group(),c=new THREE.Mesh(new THREE.ConeGeometry(.36,.7,4),MAT(0x33ff88,{emissive:0x14aa55,emissiveIntensity:.7}));g.add(c);
  const c2=c.clone();c2.position.y=-.5;c2.scale.set(.7,.7,.7);g.add(c2);return g;}
 const g=new THREE.Group(),m=MAT(0xff3355,{emissive:0x660011,emissiveIntensity:.6}),a=new THREE.Mesh(new THREE.TorusGeometry(.3,.1,10,20,Math.PI),m);g.add(a);
 [-.3,.3].forEach(x=>{const l=box(.2,.2,.2,MAT(0xffffff));l.position.set(x,-.05,0);g.add(l);const c=box(.2,.14,.2,m);c.position.set(x,-.2,0);g.add(c);});return g;}

/* ---------- Game state ---------- */
let state='menu',lane=1,curX=0,speed=9,dist=0,runCoins=0;
let jumping=false,jt=0,jd=.64,jh=2,sliding=false,st=0;const SD=.65;
let boostT=0,magT=0,sinceOb=0,nextGap=8,animT=0,overT=0,overShown=false,camX=0,tSec=0;
const objs=[],clock=new THREE.Clock();
function clearObjs(){objs.forEach(o=>scene.remove(o.m));objs.length=0;}
function addObj(m,x,y,z,o){m.position.set(x,y,z);scene.add(m);objs.push(Object.assign({m:m,y0:y,ph:Math.random()*6},o));}
function spawnChunk(z){
 const l=rnd(3),types=['jump','duck','train'],type=types[rnd(3)];
 const m=type==='jump'?mkBarrier():type==='duck'?mkGantry():mkTrain();
 addObj(m,LANE[l],0,z,{k:'ob',type:type,half:type==='train'?3:.3});
 if(type==='jump')for(let i=0;i<5;i++){const t=(i-2)/2;addObj(new THREE.Mesh(coinG,coinM),LANE[l],1.15+.95*(1-t*t),z+(i-2)*1.1,{k:'coin'});}
 let o=(l+1+rnd(2))%3;const r=Math.random();
 if(r<.15)spawnPw(z-5,o);
 else if(r<.75){const n=3+rnd(3);for(let i=0;i<n;i++)addObj(new THREE.Mesh(coinG,coinM),LANE[o],1,z-3-i*1.5,{k:'coin'});}
}
// fix power type after creation
const _add=addObj;
function spawnPw(z,lane){const t=Math.random()<.5?'jump':'mag';addObj(mkPower(t),LANE[lane],1.1,z,{k:'pw',pt:t});}

/* ---------- UI helpers ---------- */
function ui(id,on){$(id).classList.toggle('hidden',!on);}
let toastT=0;
function toast(msg){const t=$('toast');t.textContent=msg;t.classList.add('show');clearTimeout(toastT);toastT=setTimeout(()=>t.classList.remove('show'),1900);}
function refresh(){
 document.querySelectorAll('.coinTotal').forEach(e=>e.textContent=save.coins);
 $('menuChar').textContent=charById(save.selected).name;$('menuBest').textContent=Math.floor(save.best);
 $('muteBtn').textContent=save.muted?'🔇 SOUND OFF':'🔊 SOUND ON';
}
function renderChars(){
 const box=$('charGrid');box.innerHTML='';
 CHARS.forEach(c=>{
  const owned=save.owned.includes(c.id),sel=save.selected===c.id;let txt,cls='';
  if(sel){txt='SELECTED ✓';cls='dis';}else if(owned){txt='SELECT';}else{txt='BUY 🪙 '+c.price;if(save.coins<c.price)cls='poor';}
  const d=document.createElement('div');d.className='card'+(sel?' sel':'');
  d.innerHTML='<div class="avatar" style="--shirt:'+hex(c.shirt)+';--pants:'+hex(c.pants)+';--skin:'+hex(c.skin)+'"><i class="h"></i><i class="b"></i><i class="l"></i></div><b>'+c.name+'</b><small>'+(c.price?(owned?'Owned':'🪙 '+c.price):'FREE')+'</small><button class="cbtn '+cls+'" data-id="'+c.id+'">'+txt+'</button>';
  box.appendChild(d);
 });
}
$('charGrid').addEventListener('click',e=>{
 const b=e.target.closest('button[data-id]');if(!b)return;
 const c=charById(b.dataset.id);
 if(save.owned.includes(c.id)){save.selected=c.id;setPlayerModel(c);tone(660,.12,'triangle');}
 else if(save.coins>=c.price){save.coins-=c.price;save.owned.push(c.id);save.selected=c.id;setPlayerModel(c);toast('Unlocked '+c.name+'! 🎉');tone(520,.15,'triangle',.08,900);}
 else{toast('Need '+(c.price-save.coins)+' more coins 🪙');tone(160,.2,'sawtooth',.05);}
 persist();refresh();renderChars();
});

function resetPose(){
 lane=1;curX=0;jumping=false;sliding=false;boostT=magT=0;
 player.position.set(0,0,0);player.rotation.set(0,0,0);cop.position.set(0,0,3.7);cop.rotation.set(0,0,0);
 ringJ.visible=ringM.visible=false;
}
function goMenu(){state='menu';clearObjs();resetPose();['charScreen','overScreen','pauseScreen','hud','powerRow'].forEach(i=>ui(i,false));ui('mainMenu',true);refresh();}
function openChars(){state='chars';['mainMenu','overScreen','pauseScreen'].forEach(i=>ui(i,false));ui('charScreen',true);refresh();renderChars();}
function startRun(){
 clearObjs();resetPose();dist=0;runCoins=0;speed=9;sinceOb=0;nextGap=5;overShown=false;overT=0;camX=0;
 for(let i=0;i<4;i++)spawnChunk(-40-i*16);
 ['mainMenu','charScreen','overScreen','pauseScreen'].forEach(i=>ui(i,false));ui('hud',true);ui('powerRow',true);
 state='playing';clock.getDelta();tone(500,.15,'triangle',.06,800);
}
function crash(){
 state='over';overT=0;overShown=false;tone(200,.5,'sawtooth',.09,60);
 const isNew=dist>save.best;if(isNew)save.best=dist;persist();
 $('oDist').textContent=Math.floor(dist);$('oRun').textContent=runCoins;$('oNew').textContent=isNew&&dist>10?' 🏆 New best!':'';
 ui('hud',false);ui('powerRow',false);
}
function pause(){if(state==='playing'){state='paused';ui('pauseScreen',true);}}
function resume(){if(state==='paused'){ui('pauseScreen',false);state='playing';clock.getDelta();}}

$('startBtn').onclick=startRun;$('againBtn').onclick=startRun;
$('charsBtn').onclick=openChars;$('charsBtn2').onclick=openChars;
$('backBtn').onclick=goMenu;$('menuBtn2').onclick=goMenu;$('quitBtn').onclick=goMenu;
$('pauseBtn').onclick=pause;$('resumeBtn').onclick=resume;
$('muteBtn').onclick=()=>{save.muted=!save.muted;persist();refresh();};
document.addEventListener('visibilitychange',()=>{if(document.hidden)pause();});

/* ---------- Controls ---------- */
function moveLane(d){if(state!=='playing')return;const n=lane+d;if(n>=0&&n<=2){lane=n;tone(420,.06,'square',.03);}}
function doJump(){
 if(state!=='playing'||jumping)return;
 if(sliding){sliding=false;player.rotation.x=0;player.position.y=0;}
 jumping=true;jt=0;jh=boostT>0?3.4:2;jd=boostT>0?.8:.64;tone(400,.2,'triangle',.06,800);
}
function doSlide(){
 if(state!=='playing'||sliding)return;
 if(jumping){jumping=false;}
 sliding=true;st=0;player.rotation.x=1.15;player.position.y=.42;tone(300,.15,'sawtooth',.03,150);
}
addEventListener('keydown',e=>{
 const k=e.key.toLowerCase();
 if(k==='p'||k==='escape'){state==='playing'?pause():resume();return;}
 if(e.repeat)return;
 if(k==='arrowleft'||k==='a')moveLane(-1);
 else if(k==='arrowright'||k==='d')moveLane(1);
 else if(k==='arrowup'||k==='w'||k===' '){doJump();e.preventDefault();}
 else if(k==='arrowdown'||k==='s')doSlide();
});
let tx=0,ty=0;
addEventListener('touchstart',e=>{tx=e.touches[0].clientX;ty=e.touches[0].clientY;},{passive:true});
addEventListener('touchend',e=>{
 if(state!=='playing')return;
 const dx=e.changedTouches[0].clientX-tx,dy=e.changedTouches[0].clientY-ty;
 if(Math.abs(dx)>Math.abs(dy)){if(Math.abs(dx)>28)moveLane(dx>0?1:-1);}
 else{if(dy<-28)doJump();else if(dy>28)doSlide();}
},{passive:true});

/* ---------- Update ---------- */
function scrollWorld(d){
 segs.forEach(s=>{s.position.z+=d;if(s.position.z>SEG*1.5)s.position.z-=SEG*NSEG;});
 blds.forEach(b=>{b.m.position.z+=d;if(b.m.position.z>28)placeB(b,b.m.position.z-BN*BSP);});
}
function swing(g,ph,amp){const p=g.userData.p;p.legL.rotation.x=Math.sin(ph)*amp;p.legR.rotation.x=-Math.sin(ph)*amp;p.armL.rotation.x=-Math.sin(ph)*amp*.85;p.armR.rotation.x=Math.sin(ph)*amp*.85;}

function play(dt){
 speed=Math.min(24,9+dist*.006);dist+=speed*dt;
 curX+=(LANE[lane]-curX)*Math.min(1,dt*11);player.position.x=curX;
 if(jumping){jt+=dt;const p=jt/jd;if(p>=1){jumping=false;player.position.y=0;}else player.position.y=jh*Math.sin(Math.PI*p);}
 if(sliding){st+=dt;if(st>=SD){sliding=false;player.rotation.x=0;player.position.y=0;}}
 if(boostT>0)boostT-=dt;if(magT>0)magT-=dt;
 ringJ.visible=boostT>0;ringM.visible=magT>0;
 if(ringJ.visible)ringJ.rotation.z+=dt*4;if(ringM.visible)ringM.rotation.z-=dt*4;
 scrollWorld(speed*dt);

 sinceOb+=speed*dt;
 if(sinceOb>nextGap){spawnChunk(-100);sinceOb=0;nextGap=Math.max(9,16-dist*.004)+Math.random()*5;}

 const py=player.position.y+1;
 for(let i=objs.length-1;i>=0;i--){
  const o=objs[i],m=o.m;m.position.z+=speed*dt;const z=m.position.z;
  if(o.k==='ob'){
   if(Math.abs(m.position.x-curX)<1.05&&Math.abs(z)<o.half+.3){
    const safe=(o.type==='jump'&&player.position.y>.8&&!sliding)||(o.type==='duck'&&sliding);
    if(!safe){crash();return;}
   }
  }else{
   m.rotation.y+=dt*3.5;
   let pulled=false,dx=curX-m.position.x,dz=-z,dy=py-m.position.y,dd=Math.sqrt(dx*dx+dz*dz+dy*dy);
   if(magT>0&&o.k==='coin'&&dd<8){const f=Math.min(1,dt*9);m.position.x+=dx*f;m.position.y+=dy*f;m.position.z+=dz*f*.6;pulled=true;o.y0=m.position.y;}
   if(!pulled)m.position.y=o.y0+Math.sin(tSec*4+o.ph)*.08;
   if((Math.abs(m.position.x-curX)<1.05&&Math.abs(m.position.z)<.85)||(pulled&&dd<1)){
    if(o.k==='coin'){save.coins++;runCoins++;tone(900+Math.min(runCoins,30)*8,.08,'sine',.05);}
    else{if(o.pt==='jump')boostT=6;else magT=6;tone(600,.25,'triangle',.08,1200);}
    scene.remove(m);objs.splice(i,1);continue;
   }
  }
  if(m.position.z>10){scene.remove(m);objs.splice(i,1);}
 }
 animT+=dt*(8+speed*.4);
 swing(player,animT,jumping?.25:.95);if(sliding)swing(player,0,0);
 cop.position.x+=(curX-cop.position.x)*Math.min(1,dt*3);cop.position.z=3.7+Math.sin(tSec*3)*.12;swing(cop,animT*.95,.95);
 camX+=(curX*.5-camX)*Math.min(1,dt*4);
 camera.position.set(camX,4.7,8.4);camera.lookAt(curX*.25,1.1,-9);
 $('hDist').textContent=Math.floor(dist);$('hRun').textContent=runCoins;$('hTot').textContent=save.coins;
 $('jIcon').classList.toggle('on',boostT>0);$('mIcon').classList.toggle('on',magT>0);
 $('jT').textContent=boostT>0?Math.ceil(boostT):'';$('mT').textContent=magT>0?Math.ceil(magT):'';
}
function idle(dt){
 scrollWorld(4*dt);animT+=dt*10;
 swing(player,animT,.9);swing(cop,animT*.95,.9);
 cop.position.z=3.7;cop.position.x+=(curX-cop.position.x)*Math.min(1,dt*3);
 camera.position.set(Math.sin(tSec*.4)*1.2,3.6,7.4);camera.lookAt(0,1.3,-4);
}
function over(dt){
 overT+=dt;
 cop.position.z+=(.95-cop.position.z)*Math.min(1,dt*6);cop.position.x+=(curX-cop.position.x)*Math.min(1,dt*6);
 swing(cop,animT,.2);
 const sh=Math.max(0,.5-overT)*.5;camera.position.x=camX+(Math.random()-.5)*sh;camera.position.y=4.7+(Math.random()-.5)*sh;
 if(overT>.9&&!overShown){overShown=true;refresh();ui('overScreen',true);}
}
function tick(){
 const dt=Math.min(.05,clock.getDelta());tSec+=dt;
 if(state==='playing')play(dt);
 else if(state==='menu'||state==='chars')idle(dt);
 else if(state==='over')over(dt);
 renderer.render(scene,camera);requestAnimationFrame(tick);
}

setPlayerModel(charById(save.selected));resetPose();refresh();
camera.position.set(0,3.6,7.4);camera.lookAt(0,1.3,-4);
requestAnimationFrame(tick);
})();
</script>
</body>
</html>
