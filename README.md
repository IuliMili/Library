# Library
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>My Library</title>
<style>
* { margin:0; padding:0; box-sizing:border-box; }
body { background:#1a1208; font-family: Georgia, serif; color:#e8d9b8; overflow:hidden; height:100vh; }

/* ── Canvas ── */
#canvas { display:block; width:100%; height:100vh; cursor:grab; }
#canvas:active { cursor:grabbing; }

/* ── Top bar ── */
#topbar {
  position:absolute; top:0; left:0; right:0;
  display:flex; align-items:center; gap:10px;
  padding:10px 16px;
  background:linear-gradient(180deg,rgba(10,7,3,0.92) 0%,transparent 100%);
  pointer-events:none;
}
#topbar > * { pointer-events:auto; }
#libTitle { font-size:18px; letter-spacing:0.06em; color:#e8c87a; flex:1; }
.tbtn {
  padding:6px 14px; border-radius:4px; border:1px solid rgba(232,200,122,0.35);
  background:rgba(30,18,6,0.7); color:#e8c87a; font-family:Georgia,serif;
  font-size:12px; cursor:pointer; transition:all 0.15s; white-space:nowrap;
}
.tbtn:hover { background:rgba(92,50,24,0.8); border-color:rgba(232,200,122,0.7); }
.tbtn.accent { background:rgba(92,50,24,0.9); border-color:#e8c87a; }

/* ── Hint ── */
#hint {
  position:absolute; bottom:14px; left:50%; transform:translateX(-50%);
  font-size:11px; color:rgba(232,217,184,0.35); pointer-events:none;
  text-align:center; line-height:1.7;
}

/* ── Side panel ── */
#panel {
  position:absolute; top:0; right:0; bottom:0; width:300px;
  background:rgba(12,8,3,0.93);
  border-left:1px solid rgba(232,200,122,0.15);
  transform:translateX(100%);
  transition:transform 0.3s cubic-bezier(0.22,1,0.36,1);
  display:flex; flex-direction:column;
  overflow:hidden;
}
#panel.open { transform:translateX(0); }
#panelClose {
  position:absolute; top:10px; right:12px;
  background:none; border:none; color:rgba(232,200,122,0.5);
  font-size:20px; cursor:pointer; line-height:1;
}
#panelClose:hover { color:#e8c87a; }
#panelScroll { flex:1; overflow-y:auto; padding:18px 18px 80px; }
#panelScroll::-webkit-scrollbar { width:4px; }
#panelScroll::-webkit-scrollbar-track { background:transparent; }
#panelScroll::-webkit-scrollbar-thumb { background:rgba(232,200,122,0.2); border-radius:2px; }

.pLabel { font-size:10px; letter-spacing:0.12em; color:rgba(232,200,122,0.45); text-transform:uppercase; margin-bottom:5px; margin-top:16px; }
.pLabel:first-child { margin-top:4px; }
.pInput {
  width:100%; background:rgba(255,255,255,0.05); border:1px solid rgba(232,200,122,0.2);
  border-radius:4px; padding:7px 10px; color:#e8d9b8; font-family:Georgia,serif;
  font-size:13px; outline:none; transition:border-color 0.15s;
}
.pInput:focus { border-color:rgba(232,200,122,0.6); }
.pTextarea { min-height:80px; resize:vertical; }

.colorRow { display:flex; gap:8px; flex-wrap:wrap; margin-top:2px; }
.colorSwatch {
  width:26px; height:26px; border-radius:3px; cursor:pointer;
  border:2px solid transparent; transition:border-color 0.15s;
}
.colorSwatch.sel { border-color:#e8c87a; }

.shelfRow { display:flex; gap:6px; flex-wrap:wrap; margin-top:2px; }
.shelfBtn {
  padding:4px 10px; border-radius:3px; border:1px solid rgba(232,200,122,0.2);
  background:transparent; color:rgba(232,200,122,0.7); font-size:11px;
  cursor:pointer; transition:all 0.15s; font-family:Georgia,serif;
}
.shelfBtn:hover, .shelfBtn.sel { background:rgba(92,50,24,0.8); border-color:#e8c87a; color:#e8c87a; }

.progressWrap { margin-top:4px; }
.progressWrap input[type=range] { width:100%; accent-color:#c8a050; }
#progressLabel { font-size:11px; color:rgba(232,200,122,0.55); margin-top:2px; }

#panelActions { padding:12px 18px; border-top:1px solid rgba(232,200,122,0.1); display:flex; gap:8px; }
#btnOpen { flex:1; padding:9px; background:rgba(92,50,24,0.9); border:1px solid #c8a050; border-radius:4px; color:#e8c87a; font-family:Georgia,serif; font-size:13px; cursor:pointer; }
#btnOpen:hover { background:rgba(120,65,30,0.95); }
#btnSave { padding:9px 14px; background:transparent; border:1px solid rgba(232,200,122,0.25); border-radius:4px; color:rgba(232,200,122,0.7); font-family:Georgia,serif; font-size:13px; cursor:pointer; }
#btnSave:hover { border-color:rgba(232,200,122,0.6); color:#e8c87a; }

/* ── Scan overlay ── */
#scanOverlay {
  position:absolute; inset:0;
  background:rgba(10,7,3,0.88);
  display:flex; flex-direction:column; align-items:center; justify-content:center;
  gap:20px; transition:opacity 0.4s;
}
#scanOverlay.hidden { opacity:0; pointer-events:none; }
#scanBox {
  border:1px solid rgba(232,200,122,0.3); border-radius:8px;
  padding:40px 50px; text-align:center; max-width:420px;
}
#scanTitle { font-size:22px; color:#e8c87a; margin-bottom:8px; letter-spacing:0.04em; }
#scanSub { font-size:13px; color:rgba(232,200,122,0.55); line-height:1.7; margin-bottom:28px; }
#scanBtn {
  padding:12px 32px; background:rgba(92,50,24,0.9); border:1px solid #e8c87a;
  border-radius:4px; color:#e8c87a; font-family:Georgia,serif; font-size:15px;
  cursor:pointer; transition:all 0.2s;
}
#scanBtn:hover { background:rgba(140,80,30,0.95); }
#skipBtn {
  padding:8px 20px; background:transparent; border:1px solid rgba(232,200,122,0.2);
  border-radius:4px; color:rgba(232,200,122,0.45); font-family:Georgia,serif;
  font-size:12px; cursor:pointer;
}
#skipBtn:hover { color:rgba(232,200,122,0.8); border-color:rgba(232,200,122,0.5); }
#scanStatus { font-size:12px; color:rgba(232,200,122,0.5); margin-top:4px; }

/* ── Toast ── */
#toast {
  position:absolute; bottom:50px; left:50%; transform:translateX(-50%);
  background:rgba(92,50,24,0.95); border:1px solid rgba(232,200,122,0.4);
  border-radius:4px; padding:8px 18px; font-size:12px; color:#e8c87a;
  pointer-events:none; opacity:0; transition:opacity 0.3s;
  white-space:nowrap;
}
#toast.show { opacity:1; }

/* ── Mode indicator ── */
#modeBar {
  position:absolute; top:52px; left:50%; transform:translateX(-50%);
  background:rgba(140,80,30,0.9); border:1px solid #e8c87a;
  border-radius:4px; padding:5px 16px; font-size:12px; color:#e8c87a;
  pointer-events:none; opacity:0; transition:opacity 0.3s;
  white-space:nowrap;
}
#modeBar.show { opacity:1; }
</style>
</head>
<body>

<canvas id="canvas"></canvas>

<!-- Top bar -->
<div id="topbar">
  <div id="libTitle">My Library</div>
  <button class="tbtn accent" id="btnScan">Scan USB</button>
  <button class="tbtn" id="btnPlace" style="display:none">Place mode</button>
  <button class="tbtn" id="btnAdd">+ Add manually</button>
</div>

<div id="hint">drag to orbit &nbsp;·&nbsp; scroll to zoom &nbsp;·&nbsp; click a book to edit</div>
<div id="modeBar">Place mode — click a shelf slot to place the book</div>

<!-- Side panel -->
<div id="panel">
  <button id="panelClose">×</button>
  <div id="panelScroll">
    <div class="pLabel">Title</div>
    <input class="pInput" id="pTitle" type="text" placeholder="Book title">
    <div class="pLabel">Author / Source</div>
    <input class="pInput" id="pAuthor" type="text" placeholder="Author or year">
    <div class="pLabel">Section tag</div>
    <input class="pInput" id="pSection" type="text" placeholder="e.g. Philosophy, Essays…">
    <div class="pLabel">Shelf</div>
    <div class="shelfRow" id="shelfRow"></div>
    <div class="pLabel">Spine colour</div>
    <div class="colorRow" id="colorRow"></div>
    <div class="pLabel">Reading progress</div>
    <div class="progressWrap">
      <input type="range" min="0" max="100" value="0" id="pProgress">
      <div id="progressLabel">0%</div>
    </div>
    <div class="pLabel">Notes</div>
    <textarea class="pInput pTextarea" id="pNotes" placeholder="Your notes…"></textarea>
    <div class="pLabel" id="pFileLabel" style="display:none">File</div>
    <div id="pFilePath" style="font-size:11px;color:rgba(232,200,122,0.45);word-break:break-all;margin-top:2px;"></div>
  </div>
  <div id="panelActions">
    <button id="btnOpen">Open document →</button>
    <button id="btnSave">Save</button>
  </div>
</div>

<!-- Scan overlay -->
<div id="scanOverlay">
  <div id="scanBox">
    <div id="scanTitle">Welcome to your library</div>
    <div id="scanSub">Grant access to your USB folder and every PDF, EPUB and document will appear as a book on the shelves automatically.</div>
    <button id="scanBtn">Choose USB folder</button>
    <div id="scanStatus"></div>
  </div>
  <button id="skipBtn">Skip — use demo books</button>
</div>

<div id="toast"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
// ── Spine colours palette ──
localStorage.clear();
const PALETTE = [
  0x6b3a8c, 0x2e6b4a, 0x7a4a1a, 0x1e3d6b, 0x6b1e2e,
  0x2e4a6b, 0x8c6b1a, 0x4a6b1a, 0x1a5c6b, 0x6b4a1a,
  0x4a1a6b, 0x1a6b4a, 0x3a1a6b, 0x6b1a3a, 0x8c3a1a,
  0x1a3a6b,
];

const DEMO_BOOKS = [
  { title:"Critique of Pure Reason", author:"Immanuel Kant", section:"Philosophy", color:0x6b3a8c, shelf:0, file:null },
  { title:"Being and Time", author:"Heidegger", section:"Philosophy", color:0x2e6b4a, shelf:0, file:null },
  { title:"The Republic", author:"Plato", section:"Philosophy", color:0x7a4a1a, shelf:0, file:null },
  { title:"Simulacra & Simulation", author:"Baudrillard", section:"Theory", color:0x1e3d6b, shelf:0, file:null },
  { title:"Ways of Seeing", author:"John Berger", section:"Theory", color:0x6b1e2e, shelf:1, file:null },
  { title:"A Room of One's Own", author:"Virginia Woolf", section:"Literature", color:0x2e4a6b, shelf:1, file:null },
  { title:"Mod. Philosophy Notes", author:"Spring 2023", section:"Classes", color:0x8c6b1a, shelf:2, file:null },
  { title:"19th C. Literature", author:"Autumn 2023", section:"Classes", color:0x4a6b1a, shelf:2, file:null },
  { title:"History of Science", author:"Spring 2024", section:"Classes", color:0x1a5c6b, shelf:3, file:null },
  { title:"On Memory", author:"Essay 2023", section:"Essays", color:0x6b4a1a, shelf:3, file:null },
  { title:"The Gaze in Baroque", author:"Essay 2023", section:"Essays", color:0x4a1a6b, shelf:4, file:null },
  { title:"Capitalism & Language", author:"Essay 2024", section:"Essays", color:0x1a6b4a, shelf:4, file:null },
  { title:"Archaeology of Knowledge", author:"Foucault", section:"Research", color:0x3a1a6b, shelf:5, file:null },
  { title:"Cogsci Papers", author:"Various", section:"Research", color:0x6b1a3a, shelf:4, file:null },
];

const SHELF_NAMES = ["Shelf A", "Shelf B", "Shelf C", "Shelf D", "Shelf E", "Shelf F"];
const EXTS = ['pdf','epub','doc','docx','txt','md','pages','odt','rtf','pptx','xlsx'];

// ── State ──
let books = [];
let bookMeshes = [];
let shelfSlotMeshes = []; // invisible click targets on shelves
let selectedBook = null;
let placingBook = null; // book waiting to be placed
let dirHandle = null;
let fileHandles = {}; // filename -> FileSystemFileHandle

// Storage
function saveLib() { try { localStorage.setItem('usb_lib_v2', JSON.stringify(books.map(b => ({...b, color: b.color})))); } catch(e){} }
function loadLib() {
  try {
    const d = JSON.parse(localStorage.getItem('usb_lib_v2') || 'null');
    if (d && Array.isArray(d)) { books = d; return true; }
  } catch(e){}
  return false;
}

// ── THREE setup ──
const canvas = document.getElementById('canvas');
const renderer = new THREE.WebGLRenderer({ canvas, antialias:true });
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x1a1208);
scene.fog = new THREE.Fog(0x1a1208, 7, 16);

const camera = new THREE.PerspectiveCamera(50, 1, 0.01, 30);
let W, H;
function resize() {
  W = window.innerWidth; H = window.innerHeight;
  renderer.setSize(W, H);
  camera.aspect = W / H;
  camera.updateProjectionMatrix();
}
resize();
window.addEventListener('resize', resize);

// Lights
scene.add(new THREE.AmbientLight(0x7a5a30, 0.5));
const warm1 = new THREE.PointLight(0xffcc66, 1.8, 9); warm1.position.set(-1.5,2.5,1); warm1.castShadow=true; scene.add(warm1);
const warm2 = new THREE.PointLight(0xff9933, 1.2, 6); warm2.position.set(1.5,2,0.5); scene.add(warm2);
const fill  = new THREE.PointLight(0x4466aa, 0.3, 12); fill.position.set(0,0.5,4); scene.add(fill);

function makeMat(color, emissive=0) { return new THREE.MeshLambertMaterial({color, emissive}); }
const woodMat     = makeMat(0x5c3218);
const darkWoodMat = makeMat(0x3a1e0c);
const floorMat    = makeMat(0x3a2510);
const wallMat     = makeMat(0x2a1e0e);
const ceilingMat  = makeMat(0x1e1508);
const pagesMat    = makeMat(0xe8dcc8);
const ghostMat    = new THREE.MeshLambertMaterial({color:0xffffff, transparent:true, opacity:0.15});

function addBox(w,h,d,mat,x,y,z,ry=0) {
  const m = new THREE.Mesh(new THREE.BoxGeometry(w,h,d), mat);
  m.position.set(x,y,z); m.rotation.y=ry;
  m.castShadow=true; m.receiveShadow=true;
  scene.add(m); return m;
}

// Room
addBox(8,0.05,7, floorMat,   0,-0.025,0);
addBox(8,4,0.12, wallMat,    0,2,-3.5);
addBox(0.12,4,7, wallMat,   -3.94,2,0);
addBox(0.12,4,7, wallMat,    3.94,2,0);
addBox(8,0.1,7,  ceilingMat, 0,4,0);

// Lamp helper
function lamp(x,z) {
  addBox(0.06,1.2,0.06, darkWoodMat, x,0.6,z);
  const shade = new THREE.Mesh(new THREE.CylinderGeometry(0.14,0.09,0.2,8), makeMat(0xc8a050));
  shade.position.set(x,1.32,z); scene.add(shade);
  const bulb = new THREE.PointLight(0xffaa33, 0.9, 4);
  bulb.position.set(x,1.2,z); scene.add(bulb);
}
lamp(-2.5, 1.2); lamp(2.5, 1.2);

// ── Shelf geometry ──
// 4 shelf units along back wall, 2 rows each
const SHELF_CONFIGS = [
  { x:-2.8, label:"Shelf A" },
  { x:-0.94, label:"Shelf B" },
  { x: 0.94, label:"Shelf C" },
  { x: 2.8,  label:"Shelf D" },
];
const ROW_Y     = [0.2, 0.72]; // bottom of each shelf plank (book base)
const SHELF_W   = 1.55;
const SHELF_D   = 0.3;
const SHELF_Z   = -3.35 + SHELF_D/2;
const SLOT_W    = 0.12; // approximate slot width per book
const SLOTS_PER_ROW = 10;

SHELF_CONFIGS.forEach(({ x }) => {
  addBox(SHELF_W, 0.03, SHELF_D, woodMat, x, ROW_Y[0], SHELF_Z);
  addBox(SHELF_W, 0.03, SHELF_D, woodMat, x, ROW_Y[1], SHELF_Z);
  addBox(SHELF_W, 0.03, SHELF_D, woodMat, x, ROW_Y[1]+0.52, SHELF_Z);
  addBox(0.04, 1.1, SHELF_D, darkWoodMat, x - SHELF_W/2, ROW_Y[0]+0.55, SHELF_Z);
  addBox(0.04, 1.1, SHELF_D, darkWoodMat, x + SHELF_W/2, ROW_Y[0]+0.55, SHELF_Z);
  addBox(SHELF_W+0.06, 0.05, SHELF_D+0.02, woodMat, x, ROW_Y[1]+0.545, SHELF_Z);
  addBox(SHELF_W+0.06, 0.06, SHELF_D, darkWoodMat, x, ROW_Y[0]-0.03, SHELF_Z);
  addBox(0.025, 0.06, SHELF_D, woodMat, x+0.3, ROW_Y[0]+0.025, SHELF_Z);
  addBox(0.025, 0.06, SHELF_D, woodMat, x-0.3, ROW_Y[0]+0.025, SHELF_Z);
});

// Desk
addBox(1.4, 0.05, 0.7, woodMat, 0, 0.44, 1.6);
[[-0.65,1.28],[ 0.65,1.28],[-0.65,1.92],[ 0.65,1.92]].forEach(([dx,dz]) => addBox(0.06,0.44,0.06,darkWoodMat,dx,0.22,dz));
const openBook = new THREE.Mesh(new THREE.BoxGeometry(0.32,0.022,0.24), pagesMat);
openBook.position.set(-0.1,0.462,1.58); openBook.rotation.y=0.1; scene.add(openBook);
const crease = new THREE.Mesh(new THREE.BoxGeometry(0.009,0.026,0.24), makeMat(0xb0a080));
crease.position.set(-0.1,0.474,1.58); crease.rotation.y=0.1; scene.add(crease);
const pen = new THREE.Mesh(new THREE.CylinderGeometry(0.006,0.006,0.24,6), makeMat(0x2a1a0a));
pen.rotation.z=Math.PI/2; pen.rotation.y=0.55; pen.position.set(0.26,0.454,1.5); scene.add(pen);

// Rug
const rug = new THREE.Mesh(new THREE.PlaneGeometry(2.2,1.4), makeMat(0x6b3020));
rug.rotation.x=-Math.PI/2; rug.position.set(0,0.002,1.4); scene.add(rug);

// Invisible slot targets for placement
function buildSlotTargets() {
  shelfSlotMeshes.forEach(m => scene.remove(m));
  shelfSlotMeshes = [];
  SHELF_CONFIGS.forEach((cfg, si) => {
    [0, 1].forEach((row) => {
      for (let s=0; s<SLOTS_PER_ROW; s++) {
        const slotX = cfg.x - SHELF_W/2 + 0.08 + s * (SHELF_W-0.12)/SLOTS_PER_ROW + (SHELF_W-0.12)/(SLOTS_PER_ROW*2);
        const slotY = ROW_Y[row] + 0.25;
        const m = new THREE.Mesh(new THREE.BoxGeometry(SLOT_W, 0.5, SHELF_D), new THREE.MeshBasicMaterial({visible:false}));
        m.position.set(slotX, slotY, SHELF_Z);
        m.userData = { isSlot:true, shelfIdx: si*2+row, slotX, slotY };
        scene.add(m);
        shelfSlotMeshes.push(m);
      }
    });
  });
}
buildSlotTargets();

// ── Book mesh management ──
function getBookDimensions(b) {
  const words = (b.title || '').length;
  const h = 0.22 + Math.min(words, 40) * 0.002 + (b._h || 0);
  const w = 0.05 + (b._w || 0);
  const d = SHELF_D - 0.04;
  return { w, h, d };
}

function buildBookMesh(b, idx) {
  const { w, h, d } = getBookDimensions(b);
  const mat = new THREE.MeshLambertMaterial({ color: b.color, emissive: 0 });
  const spine = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), mat);
  spine.castShadow = true; spine.receiveShadow = true;
  spine.userData = { bookIdx: idx, isBook: true };

  const pages = new THREE.Mesh(new THREE.BoxGeometry(w-0.008, h-0.01, 0.012), pagesMat);
  pages.position.z = d/2 + 0.006;

  const group = new THREE.Group();
  group.add(spine); group.add(pages);
  group.userData = { bookIdx: idx, isBook: true };
  scene.add(group);
  return group;
}

function positionBook(group, b, xOffset) {
  const { h } = getBookDimensions(b);
  const shelfIdx = Math.max(0, Math.min(SHELF_CONFIGS.length*2 -1, b.shelf || 0));
  const si = Math.floor(shelfIdx / 2);
  const row = shelfIdx % 2;
  const cfg = SHELF_CONFIGS[Math.min(si, SHELF_CONFIGS.length-1)];
  const baseX = cfg.x - SHELF_W/2 + 0.08;
  group.position.set(baseX + xOffset, ROW_Y[row] + h/2 + 0.03, SHELF_Z);
  group.rotation.z = (Math.random()-0.5)*0.06;
}

function rebuildAllBooks() {
  bookMeshes.forEach(g => scene.remove(g));
  bookMeshes = [];

  const shelfOffsets = {};
  books.forEach((b, idx) => {
    const sKey = b.shelf || 0;
    if (!shelfOffsets[sKey]) shelfOffsets[sKey] = 0;
    const { w } = getBookDimensions(b);
    const xOff = shelfOffsets[sKey];
    shelfOffsets[sKey] += w + 0.014;
    if (shelfOffsets[sKey] > SHELF_W - 0.15) return;
    const group = buildBookMesh(b, idx);
    positionBook(group, b, xOff);
    bookMeshes.push(group);
  });
}

// ── Panel UI ──
const panel = document.getElementById('panel');
const pTitle = document.getElementById('pTitle');
const pAuthor = document.getElementById('pAuthor');
const pSection = document.getElementById('pSection');
const pProgress = document.getElementById('pProgress');
const progressLabel = document.getElementById('progressLabel');
const pNotes = document.getElementById('pNotes');
const pFilePath = document.getElementById('pFilePath');
const pFileLabel = document.getElementById('pFileLabel');
const shelfRow = document.getElementById('shelfRow');
const colorRow = document.getElementById('colorRow');

// Build colour swatches
PALETTE.forEach((c, i) => {
  const sw = document.createElement('div');
  sw.className = 'colorSwatch';
  sw.style.background = '#' + c.toString(16).padStart(6,'0');
  sw.addEventListener('click', () => {
    document.querySelectorAll('.colorSwatch').forEach(s => s.classList.remove('sel'));
    sw.classList.add('sel');
    if (selectedBook !== null) { books[selectedBook].color = c; saveLib(); rebuildAllBooks(); }
  });
  colorRow.appendChild(sw);
});

// Build shelf buttons
function buildShelfButtons() {
  shelfRow.innerHTML = '';
  SHELF_NAMES.slice(0,8).forEach((name,i) => {
    const btn = document.createElement('button');
    btn.className = 'shelfBtn'; btn.textContent = name; btn.dataset.shelf = i;
    btn.addEventListener('click', () => {
      document.querySelectorAll('.shelfBtn').forEach(b => b.classList.remove('sel'));
      btn.classList.add('sel');
      if (selectedBook !== null) {
        books[selectedBook].shelf = i; saveLib(); rebuildAllBooks();
        toast('Moved to '+name);
      }
    });
    shelfRow.appendChild(btn);
  });
}
buildShelfButtons();

pProgress.addEventListener('input', () => { progressLabel.textContent = pProgress.value+'%'; });

function openPanel(idx) {
  selectedBook = idx;
  const b = books[idx];
  pTitle.value   = b.title || '';
  pAuthor.value  = b.author || '';
  pSection.value = b.section || '';
  pProgress.value = b.progress || 0;
  progressLabel.textContent = (b.progress||0)+'%';
  pNotes.value   = b.notes || '';
  pFileLabel.style.display = b.file ? 'block' : 'none';
  pFilePath.textContent = b.file || '';

  document.querySelectorAll('.colorSwatch').forEach((sw,i) => sw.classList.toggle('sel', PALETTE[i]===b.color));
  document.querySelectorAll('.shelfBtn').forEach(btn => btn.classList.toggle('sel', parseInt(btn.dataset.shelf)===(b.shelf||0)));

  // Highlight mesh
  bookMeshes.forEach((g,i) => {
    const spine = g.children[0];
    spine.material.emissive = new THREE.Color(i===idx ? 0x443322 : 0x000000);
  });
  panel.classList.add('open');
}

function closePanel() {
  panel.classList.remove('open');
  bookMeshes.forEach(g => { g.children[0].material.emissive = new THREE.Color(0); });
  selectedBook = null;
}

document.getElementById('panelClose').addEventListener('click', closePanel);

document.getElementById('btnSave').addEventListener('click', () => {
  if (selectedBook === null) return;
  books[selectedBook].title    = pTitle.value;
  books[selectedBook].author   = pAuthor.value;
  books[selectedBook].section  = pSection.value;
  books[selectedBook].progress = parseInt(pProgress.value);
  books[selectedBook].notes    = pNotes.value;
  saveLib(); rebuildAllBooks();
  openPanel(selectedBook);
  toast('Saved');
});

document.getElementById('btnOpen').addEventListener('click', async () => {
  if (selectedBook === null) return;
  const b = books[selectedBook];
  if (b.file && fileHandles[b.file]) {
    try {
      const fh = fileHandles[b.file];
      const file = await fh.getFile();
      const url = URL.createObjectURL(file);
      window.open(url, '_blank');
    } catch(e) { toast('Could not open file'); }
  } else if (b.file) {
    toast('Rescan USB to reopen files');
  } else {
    toast('No file attached to this book');
  }
});

// ── + Add manually ──
document.getElementById('btnAdd').addEventListener('click', () => {
  const newBook = {
    title: 'New Document', author: '', section: 'Unsorted',
    color: PALETTE[Math.floor(Math.random()*PALETTE.length)],
    shelf: 0, file: null, progress: 0, notes: ''
  };
  books.push(newBook);
  saveLib(); rebuildAllBooks();
  openPanel(books.length - 1);
  toast('New book added — edit details in the panel');
});

// ── Place mode ──
let placeModeActive = false;
const btnPlace = document.getElementById('btnPlace');
const modeBar  = document.getElementById('modeBar');

btnPlace.addEventListener('click', () => {
  if (!placeModeActive || placingBook === null) return;
  placeModeActive = false;
  placingBook = null;
  btnPlace.style.display = 'none';
  modeBar.classList.remove('show');
  toast('Placement cancelled');
});

function startPlaceMode(idx) {
  placingBook = idx;
  placeModeActive = true;
  btnPlace.style.display = '';
  modeBar.classList.add('show');
  closePanel();
}

// ── Scan USB ──
async function scanUSB() {
  if (!window.showDirectoryPicker) {
    toast('File System Access API not supported in this browser. Try Chrome or Edge.');
    return;
  }
  const statusEl = document.getElementById('scanStatus');
  statusEl.textContent = 'Choosing folder…';
  try {
    dirHandle = await window.showDirectoryPicker({ mode:'read' });
    statusEl.textContent = 'Scanning…';
    let found = 0;
    const existingFiles = new Set(books.map(b => b.file).filter(Boolean));

    async function scanDir(dh, prefix) {
      for await (const [name, handle] of dh.entries()) {
        if (handle.kind === 'directory') {
          await scanDir(handle, prefix + name + '/');
        } else {
          const ext = name.split('.').pop().toLowerCase();
          if (EXTS.includes(ext)) {
            const filePath = prefix + name;
            fileHandles[filePath] = handle;
            if (!existingFiles.has(filePath)) {
              const cleanTitle = name.replace(/\.[^.]+$/, '').replace(/[-_]/g,' ').replace(/\b\w/g,c=>c.toUpperCase());
              books.push({
                title: cleanTitle, author: '', section: guessSection(prefix),
                color: PALETTE[Math.floor(Math.random()*PALETTE.length)],
                shelf: guessShelf(prefix), file: filePath, progress: 0, notes: ''
              });
              found++;
              statusEl.textContent = `Found ${found} new document${found!==1?'s':''}…`;
            } else {
              fileHandles[filePath] = handle;
            }
          }
        }
      }
    }
    await scanDir(dirHandle, '');
    saveLib(); rebuildAllBooks();
    hideScanOverlay();
    toast(found > 0 ? `${found} new document${found!==1?'s':''} added to your library!` : 'Library up to date — no new files found');
  } catch(e) {
    if (e.name !== 'AbortError') toast('Could not read folder: ' + e.message);
    statusEl.textContent = '';
  }
}

function guessSection(prefix) {
  const p = prefix.toLowerCase();
  if (p.includes('essay') || p.includes('writing')) return 'Essays';
  if (p.includes('class') || p.includes('lecture') || p.includes('note')) return 'Classes';
  if (p.includes('research') || p.includes('paper')) return 'Research';
  if (p.includes('book')) return 'Books';
  return 'Unsorted';
}

function guessShelf(prefix) {
  const p = prefix.toLowerCase();
  if (p.includes('essay') || p.includes('writing') || p.includes('paper')) return 4;
  if (p.includes('class') || p.includes('lecture') || p.includes('note') || p.includes('cours')) return 2;
  if (p.includes('research') || p.includes('article') || p.includes('journal')) return 6;
  if (p.includes('book') || p.includes('read') || p.includes('lit')) return 0;
  // Spread unknown files across shelves based on first letter of folder name
  const code = (prefix.charCodeAt(0) || 0) % 4;
  return code * 2;
}

function hideScanOverlay() {
  document.getElementById('scanOverlay').classList.add('hidden');
}

document.getElementById('scanBtn').addEventListener('click', scanUSB);
document.getElementById('btnScan').addEventListener('click', () => {
  document.getElementById('scanOverlay').classList.remove('hidden');
  document.getElementById('scanOverlay').style.opacity = '';
});
document.getElementById('skipBtn').addEventListener('click', () => {
  if (books.length === 0) { books = JSON.parse(JSON.stringify(DEMO_BOOKS)); saveLib(); rebuildAllBooks(); }
  hideScanOverlay();
});

// ── Orbit controls ──
let isDragging=false, prevX=0, prevY=0;
let theta=0, phi=0.22, radius=4.2;
const target = new THREE.Vector3(0, 0.5, 0);
let clickMoved = false;

function updateCam() {
  camera.position.x = target.x + radius * Math.sin(theta) * Math.cos(phi);
  camera.position.y = target.y + radius * Math.sin(phi);
  camera.position.z = target.z + radius * Math.cos(theta) * Math.cos(phi);
  camera.lookAt(target);
}
updateCam();

canvas.addEventListener('mousedown', e => { isDragging=true; prevX=e.clientX; prevY=e.clientY; clickMoved=false; });
window.addEventListener('mouseup', () => isDragging=false);
window.addEventListener('mousemove', e => {
  if (!isDragging) return;
  const dx = e.clientX-prevX, dy = e.clientY-prevY;
  if (Math.abs(dx)+Math.abs(dy) > 3) clickMoved=true;
  theta -= dx*0.007; phi = Math.max(-0.05, Math.min(0.75, phi+dy*0.004));
  prevX=e.clientX; prevY=e.clientY; updateCam();
});
canvas.addEventListener('wheel', e => { radius=Math.max(1.8,Math.min(7,radius+e.deltaY*0.005)); updateCam(); e.preventDefault(); }, {passive:false});
let lastTouch=null;
canvas.addEventListener('touchstart', e=>{lastTouch=e.touches[0];clickMoved=false;});
canvas.addEventListener('touchmove', e=>{
  if(!lastTouch) return;
  const t=e.touches[0];
  const dx=t.clientX-lastTouch.clientX, dy=t.clientY-lastTouch.clientY;
  if(Math.abs(dx)+Math.abs(dy)>3) clickMoved=true;
  theta-=dx*0.009; phi=Math.max(-0.05,Math.min(0.75,phi+dy*0.006));
  lastTouch=t; updateCam(); e.preventDefault();
},{passive:false});

// ── Raycasting ──
const raycaster = new THREE.Raycaster();
const mouse2 = new THREE.Vector2();

canvas.addEventListener('click', e => {
  if (clickMoved) return;
  const rect = canvas.getBoundingClientRect();
  mouse2.x = ((e.clientX-rect.left)/rect.width)*2-1;
  mouse2.y = -((e.clientY-rect.top)/rect.height)*2+1;
  raycaster.setFromCamera(mouse2, camera);

  if (placeModeActive && placingBook !== null) {
    const slotHits = raycaster.intersectObjects(shelfSlotMeshes);
    if (slotHits.length) {
      const slot = slotHits[0].object.userData;
      books[placingBook].shelf = slot.shelfIdx;
      saveLib(); rebuildAllBooks();
      toast('Book placed on ' + SHELF_NAMES[Math.floor(slot.shelfIdx/2)]);
      placeModeActive = false;
      btnPlace.style.display = 'none';
      modeBar.classList.remove('show');
      openPanel(placingBook);
      placingBook = null;
    }
    return;
  }

  // Hit test all book groups
  const allBookChildren = [];
  bookMeshes.forEach(g => allBookChildren.push(...g.children));
  const hits = raycaster.intersectObjects(allBookChildren);
  if (hits.length) {
    const obj = hits[0].object;
    let idx = obj.parent?.userData?.bookIdx;
    if (idx === undefined) idx = obj.userData?.bookIdx;
    if (idx !== undefined) { openPanel(idx); return; }
  }
  if (!panel.contains(e.target)) closePanel();
});

// ── Toast ──
function toast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  setTimeout(()=>t.classList.remove('show'), 2500);
}

// ── Animate ──
let tick=0;
function animate() {
  requestAnimationFrame(animate);
  tick+=0.008;
  warm1.intensity = 1.8 + Math.sin(tick*0.7)*0.15;
  warm2.intensity = 1.2 + Math.sin(tick*0.5+1)*0.1;
  renderer.render(scene, camera);
}
animate();

// ── Init ──
if (!loadLib()) {
  // first visit — show overlay
} else {
  rebuildAllBooks();
  hideScanOverlay();
}
</script>
</body>
</html>

