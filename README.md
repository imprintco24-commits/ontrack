<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1">
<title>Box Countdown Tracker</title>
<meta name="theme-color" content="#6366f1">
<style>
  /* ============================================================
     DESIGN TOKENS / THEME VARIABLES
     ============================================================ */
  :root{
    --bg: #f3f4f8;
    --bg-grad-1: #eef1ff;
    --bg-grad-2: #f7f3ff;
    --card: #ffffff;
    --card-border: rgba(17,17,17,0.06);
    --text: #16161a;
    --text-dim: #6b7280;
    --text-faint: #9ca3af;
    --accent: #6d5bff;
    --accent-2: #ff6d9c;
    --accent-soft: #ece9ff;
    --green: #16b378;
    --green-soft: #e3f9ef;
    --amber: #f5a524;
    --amber-soft: #fff3de;
    --danger: #ef4444;
    --box-empty: #e9eaf0;
    --box-empty-border: #dcdde5;
    --shadow: 0 10px 30px -12px rgba(30,20,80,0.18);
    --shadow-sm: 0 4px 14px -6px rgba(30,20,80,0.12);
    --radius: 18px;
    --radius-sm: 12px;
    --font: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  }
  [data-theme="dark"]{
    --bg: #0e0e14;
    --bg-grad-1: #14141f;
    --bg-grad-2: #1a1420;
    --card: #17171f;
    --card-border: rgba(255,255,255,0.07);
    --text: #f2f2f7;
    --text-dim: #a2a2b3;
    --text-faint: #6b6b7c;
    --accent: #8b7bff;
    --accent-2: #ff85ac;
    --accent-soft: #241f3d;
    --green: #2fd996;
    --green-soft: #123527;
    --amber: #ffbb4d;
    --amber-soft: #3a2c10;
    --danger: #ff6767;
    --box-empty: #201f2b;
    --box-empty-border: #2b2a3a;
    --shadow: 0 10px 30px -12px rgba(0,0,0,0.6);
    --shadow-sm: 0 4px 14px -6px rgba(0,0,0,0.45);
  }

  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    font-family:var(--font);
    background:
      radial-gradient(circle at 10% 0%, var(--bg-grad-1), transparent 55%),
      radial-gradient(circle at 90% 10%, var(--bg-grad-2), transparent 50%),
      var(--bg);
    color:var(--text);
    min-height:100vh;
    transition:background .35s ease, color .35s ease;
    -webkit-font-smoothing:antialiased;
  }
  .wrap{max-width:1000px;margin:0 auto;padding:18px 16px 80px;}

  /* ---------- Top bar ---------- */
  .topbar{
    display:flex;align-items:center;justify-content:space-between;
    gap:10px;margin-bottom:18px;flex-wrap:wrap;
  }
  .brand{display:flex;align-items:center;gap:10px;font-weight:700;font-size:1.15rem;}
  .brand .dot{width:12px;height:12px;border-radius:4px;background:linear-gradient(135deg,var(--accent),var(--accent-2));display:inline-block;}
  .top-actions{display:flex;gap:8px;flex-wrap:wrap;}

  button{
    font-family:inherit;cursor:pointer;border:none;
  }
  .icon-btn{
    background:var(--card);
    border:1px solid var(--card-border);
    color:var(--text);
    width:40px;height:40px;border-radius:12px;
    display:flex;align-items:center;justify-content:center;
    font-size:1.05rem;
    box-shadow:var(--shadow-sm);
    transition:transform .15s ease, background .2s ease;
  }
  .icon-btn:hover{transform:translateY(-2px);}
  .icon-btn:active{transform:scale(.94);}

  .btn{
    padding:10px 16px;border-radius:12px;font-weight:600;font-size:.88rem;
    background:var(--card);color:var(--text);border:1px solid var(--card-border);
    box-shadow:var(--shadow-sm);transition:transform .15s ease, opacity .2s ease;
    display:inline-flex;align-items:center;gap:6px;
  }
  .btn:hover{transform:translateY(-2px);}
  .btn:active{transform:scale(.97);}
  .btn-primary{background:linear-gradient(135deg,var(--accent),#8f7bff);color:#fff;border:none;}
  .btn-danger{background:var(--danger);color:#fff;border:none;}
  .btn-ghost{background:transparent;box-shadow:none;border:1px solid var(--card-border);}
  .btn-sm{padding:7px 12px;font-size:.8rem;border-radius:10px;}
  .btn:disabled{opacity:.45;cursor:not-allowed;transform:none;}

  /* ---------- Cards ---------- */
  .card{
    background:var(--card);
    border:1px solid var(--card-border);
    border-radius:var(--radius);
    box-shadow:var(--shadow-sm);
    padding:18px;
    margin-bottom:16px;
    animation:fadeUp .45s ease both;
  }
  @keyframes fadeUp{from{opacity:0;transform:translateY(10px);}to{opacity:1;transform:translateY(0);}}

  .card h2{margin:0 0 12px;font-size:.95rem;letter-spacing:.02em;color:var(--text-dim);text-transform:uppercase;font-weight:700;}

  /* ---------- Date setup ---------- */
  .setup-row{display:flex;gap:10px;flex-wrap:wrap;align-items:flex-end;}
  .field{display:flex;flex-direction:column;gap:6px;flex:1;min-width:170px;}
  .field label{font-size:.78rem;color:var(--text-dim);font-weight:600;}
  input[type="date"], input[type="text"], textarea, input[type="search"]{
    background:var(--bg);
    border:1px solid var(--card-border);
    color:var(--text);
    padding:10px 12px;
    border-radius:10px;
    font-family:inherit;
    font-size:.9rem;
    outline:none;
    transition:border-color .2s ease;
  }
  input[type="date"]:focus, input[type="text"]:focus, textarea:focus, input[type="search"]:focus{
    border-color:var(--accent);
  }
  input::-webkit-calendar-picker-indicator{ filter: var(--picker-filter, none); cursor:pointer; }
  [data-theme="dark"] input::-webkit-calendar-picker-indicator{ filter: invert(1); }

  /* ---------- Hero countdown ---------- */
  .hero{
    text-align:center;
    background:linear-gradient(135deg, var(--accent-soft), transparent 70%);
  }
  .hero .title-label{color:var(--text-dim);font-weight:600;font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;}
  .hero .goal-title{font-size:1.3rem;font-weight:800;margin:4px 0 14px;}
  .timer{
    display:flex;justify-content:center;gap:10px;flex-wrap:wrap;margin-bottom:14px;
  }
  .timer .unit{
    background:var(--card);
    border:1px solid var(--card-border);
    border-radius:14px;
    padding:10px 14px;
    min-width:70px;
    box-shadow:var(--shadow-sm);
  }
  .timer .unit .num{font-size:1.7rem;font-weight:800;font-variant-numeric:tabular-nums;line-height:1;}
  .timer .unit .lbl{font-size:.68rem;color:var(--text-dim);text-transform:uppercase;letter-spacing:.06em;margin-top:4px;}
  .complete-banner{font-size:1.4rem;font-weight:800;padding:20px 0;}

  .progress-outer{
    background:var(--box-empty);
    border-radius:20px;
    height:16px;
    overflow:hidden;
    margin:10px 0 6px;
    border:1px solid var(--card-border);
  }
  .progress-inner{
    height:100%;
    background:linear-gradient(90deg,var(--accent),var(--accent-2));
    border-radius:20px;
    transition:width .6s cubic-bezier(.4,0,.2,1);
  }
  .progress-meta{display:flex;justify-content:space-between;font-size:.78rem;color:var(--text-dim);}

  /* ---------- Stat grid ---------- */
  .stat-grid{
    display:grid;grid-template-columns:repeat(auto-fit,minmax(120px,1fr));gap:12px;
  }
  .stat-box{
    background:var(--bg);
    border:1px solid var(--card-border);
    border-radius:14px;
    padding:12px 14px;
    text-align:center;
  }
  .stat-box .val{font-size:1.35rem;font-weight:800;}
  .stat-box .lbl{font-size:.72rem;color:var(--text-dim);margin-top:2px;text-transform:uppercase;letter-spacing:.04em;}

  /* ---------- Quote ---------- */
  .quote-card{
    display:flex;align-items:center;gap:10px;font-style:italic;color:var(--text-dim);font-size:.88rem;
  }

  /* ---------- Box grid ---------- */
  .grid-head{display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:10px;margin-bottom:12px;}
  .legend{display:flex;gap:12px;flex-wrap:wrap;font-size:.72rem;color:var(--text-dim);}
  .legend span{display:inline-flex;align-items:center;gap:5px;}
  .legend i{width:10px;height:10px;border-radius:3px;display:inline-block;}

  .box-grid{
    display:grid;
    grid-template-columns:repeat(auto-fill,minmax(30px,1fr));
    gap:6px;
  }
  .day-box{
    position:relative;
    aspect-ratio:1/1;
    background:var(--box-empty);
    border:1px solid var(--box-empty-border);
    border-radius:7px;
    cursor:pointer;
    display:flex;align-items:center;justify-content:center;
    font-size:.6rem;
    color:var(--text-faint);
    transition:transform .15s ease, background .25s ease, border-color .25s ease;
  }
  .day-box:hover{transform:scale(1.15);z-index:2;}
  .day-box.completed{
    background:var(--green-soft);
    border-color:var(--green);
    color:var(--green);
    text-decoration:line-through;
    opacity:.75;
  }
  .day-box.today{
    border-color:var(--accent);
    box-shadow:0 0 0 3px var(--accent-soft);
    font-weight:800;
    color:var(--accent);
  }
  .day-box .dot{
    position:absolute;top:2px;right:2px;width:6px;height:6px;border-radius:50%;
  }
  .dot-goal{background:var(--amber);}
  .dot-done{background:var(--green);}

  /* ---------- Modal ---------- */
  .modal-overlay{
    position:fixed;inset:0;background:rgba(10,10,20,.55);
    display:none;align-items:center;justify-content:center;
    padding:16px;z-index:100;backdrop-filter:blur(3px);
    animation:fadeIn .2s ease both;
  }
  @keyframes fadeIn{from{opacity:0}to{opacity:1}}
  .modal-overlay.active{display:flex;}
  .modal{
    background:var(--card);
    border-radius:20px;
    padding:20px;
    width:100%;max-width:440px;
    max-height:88vh;overflow-y:auto;
    box-shadow:var(--shadow);
    animation:popIn .25s cubic-bezier(.34,1.56,.64,1) both;
    border:1px solid var(--card-border);
  }
  @keyframes popIn{from{opacity:0;transform:scale(.9) translateY(10px);}to{opacity:1;transform:scale(1) translateY(0);}}
  .modal-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:4px;}
  .modal-head h3{margin:0;font-size:1.05rem;}
  .modal-date{color:var(--text-dim);font-size:.8rem;margin-bottom:14px;}
  .close-x{background:none;border:none;font-size:1.3rem;color:var(--text-dim);cursor:pointer;line-height:1;}

  .task-row{display:flex;align-items:center;gap:8px;margin-bottom:6px;}
  .task-row input[type="text"]{flex:1;}
  .task-row input[type="checkbox"]{width:18px;height:18px;accent-color:var(--accent);}
  .task-remove{background:none;border:none;color:var(--danger);font-size:1rem;cursor:pointer;padding:2px 6px;}
  .add-task-btn{margin-top:4px;}

  .modal-actions{display:flex;gap:8px;margin-top:16px;flex-wrap:wrap;}
  .goal-pct{font-size:.78rem;color:var(--text-dim);margin-top:8px;}

  /* ---------- Goals list / search ---------- */
  .goals-toolbar{display:flex;gap:8px;margin-bottom:12px;flex-wrap:wrap;}
  .goals-toolbar input[type="search"]{flex:1;min-width:150px;}
  .filter-chip{
    padding:8px 12px;border-radius:10px;font-size:.78rem;font-weight:600;
    background:var(--bg);border:1px solid var(--card-border);color:var(--text-dim);
  }
  .filter-chip.active{background:var(--accent);color:#fff;border-color:transparent;}
  .goal-item{
    border:1px solid var(--card-border);border-radius:12px;padding:10px 12px;margin-bottom:8px;
    display:flex;justify-content:space-between;align-items:center;gap:10px;cursor:pointer;
    transition:background .15s ease;
  }
  .goal-item:hover{background:var(--bg);}
  .goal-item .gi-left{min-width:0;}
  .goal-item .gi-title{font-weight:700;font-size:.88rem;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
  .goal-item .gi-date{font-size:.72rem;color:var(--text-dim);}
  .badge{font-size:.68rem;padding:3px 8px;border-radius:20px;font-weight:700;white-space:nowrap;}
  .badge-done{background:var(--green-soft);color:var(--green);}
  .badge-pending{background:var(--amber-soft);color:var(--amber);}
  .empty-note{color:var(--text-faint);font-size:.85rem;text-align:center;padding:14px 0;}

  footer{text-align:center;color:var(--text-faint);font-size:.72rem;margin-top:24px;}

  #confettiCanvas{position:fixed;inset:0;pointer-events:none;z-index:200;}

  @media (max-width:520px){
    .timer .unit{min-width:58px;padding:8px 10px;}
    .timer .unit .num{font-size:1.3rem;}
  }
</style>
</head>
<body>

<canvas id="confettiCanvas"></canvas>

<div class="wrap">

  <div class="topbar">
    <div class="brand"><span class="dot"></span> Box Countdown</div>
    <div class="top-actions">
      <button class="icon-btn" id="themeToggle" title="Toggle theme (T)">🌙</button>
      <button class="icon-btn" id="exportBtn" title="Export JSON">⬇️</button>
      <button class="icon-btn" id="importBtn" title="Import JSON">⬆️</button>
      <input type="file" id="importFile" accept="application/json" style="display:none">
      <button class="icon-btn" id="resetBtn" title="Reset everything">🗑️</button>
    </div>
  </div>

  <!-- DATE SETUP -->
  <div class="card">
    <h2>Target Date</h2>
    <div class="setup-row">
      <div class="field">
        <label for="targetDateInput">Countdown to</label>
        <input type="date" id="targetDateInput">
      </div>
      <div class="field">
        <label for="goalTitleInput">Countdown name (optional)</label>
        <input type="text" id="goalTitleInput" placeholder="e.g. CA Final Exam">
      </div>
      <button class="btn btn-primary" id="startBtn">Set Countdown</button>
    </div>
  </div>

  <!-- HERO -->
  <div class="card hero" id="heroCard" style="display:none;">
    <div class="title-label">Counting down to</div>
    <div class="goal-title" id="heroTitle">—</div>

    <div id="timerBlock">
      <div class="timer">
        <div class="unit"><div class="num" id="tDays">0</div><div class="lbl">Days</div></div>
        <div class="unit"><div class="num" id="tHours">0</div><div class="lbl">Hours</div></div>
        <div class="unit"><div class="num" id="tMinutes">0</div><div class="lbl">Minutes</div></div>
        <div class="unit"><div class="num" id="tSeconds">0</div><div class="lbl">Seconds</div></div>
      </div>
    </div>
    <div id="completeBanner" class="complete-banner" style="display:none;">Countdown Complete 🎉</div>

    <div class="progress-outer"><div class="progress-inner" id="progressBar" style="width:0%"></div></div>
    <div class="progress-meta">
      <span id="progressPct">0% complete</span>
      <span id="progressDays">0 / 0 days</span>
    </div>
  </div>

  <!-- QUOTE -->
  <div class="card quote-card" id="quoteCard" style="display:none;">
    <span>💡</span><span id="quoteText"></span>
  </div>

  <!-- STATS -->
  <div class="card" id="statsCard" style="display:none;">
    <h2>Statistics</h2>
    <div class="stat-grid">
      <div class="stat-box"><div class="val" id="statTotalDays">0</div><div class="lbl">Total Days</div></div>
      <div class="stat-box"><div class="val" id="statCompletedDays">0</div><div class="lbl">Days Done</div></div>
      <div class="stat-box"><div class="val" id="statRemainingDays">0</div><div class="lbl">Days Left</div></div>
      <div class="stat-box"><div class="val" id="statTotalGoals">0</div><div class="lbl">Total Goals</div></div>
      <div class="stat-box"><div class="val" id="statCompletedGoals">0</div><div class="lbl">Goals Done</div></div>
      <div class="stat-box"><div class="val" id="statPendingGoals">0</div><div class="lbl">Goals Pending</div></div>
      <div class="stat-box"><div class="val" id="statGoalPct">0%</div><div class="lbl">Goal Completion</div></div>
      <div class="stat-box"><div class="val" id="statWeekPct">0%</div><div class="lbl">This Week</div></div>
    </div>
  </div>

  <!-- BOX GRID -->
  <div class="card" id="gridCard" style="display:none;">
    <div class="grid-head">
      <h2 style="margin:0;">Day Boxes</h2>
      <div class="legend">
        <span><i style="background:var(--box-empty);border:1px solid var(--box-empty-border);"></i>Empty</span>
        <span><i style="background:var(--green-soft);border:1px solid var(--green);"></i>Completed</span>
        <span><i style="background:var(--accent-soft);border:1px solid var(--accent);"></i>Today</span>
        <span><i style="background:var(--amber);"></i>Has Goal</span>
        <span><i style="background:var(--green);"></i>Goal Done</span>
      </div>
    </div>
    <div class="box-grid" id="boxGrid"></div>
  </div>

  <!-- GOALS LIST -->
  <div class="card" id="goalsCard" style="display:none;">
    <h2>Goals</h2>
    <div class="goals-toolbar">
      <input type="search" id="goalSearch" placeholder="Search goals... (/)">
      <button class="filter-chip active" data-filter="all">All</button>
      <button class="filter-chip" data-filter="pending">Pending</button>
      <button class="filter-chip" data-filter="completed">Completed</button>
    </div>
    <div id="goalsList"></div>
  </div>

  <footer>Box Countdown Tracker · 100% offline · data stored only in this browser</footer>
</div>

<!-- GOAL MODAL -->
<div class="modal-overlay" id="modalOverlay">
  <div class="modal">
    <div class="modal-head">
      <h3>Day Goal</h3>
      <button class="close-x" id="modalClose">✕</button>
    </div>
    <div class="modal-date" id="modalDateLabel"></div>

    <div class="field">
      <label for="modalGoalTitle">Goal title</label>
      <input type="text" id="modalGoalTitle" placeholder="What's the focus for this day?">
    </div>
    <div class="field" style="margin-top:10px;">
      <label for="modalGoalNotes">Notes</label>
      <textarea id="modalGoalNotes" rows="3" placeholder="Details, notes..."></textarea>
    </div>

    <div class="field" style="margin-top:12px;">
      <label>Tasks / Checklist</label>
      <div id="taskList"></div>
      <button class="btn btn-ghost btn-sm add-task-btn" id="addTaskBtn">+ Add Task</button>
    </div>

    <div class="goal-pct" id="modalGoalPct"></div>

    <div class="modal-actions">
      <button class="btn btn-primary" id="saveGoalBtn">Save Goal</button>
      <button class="btn btn-danger btn-ghost" id="deleteGoalBtn">Delete</button>
      <button class="btn btn-ghost" id="modalCancelBtn">Cancel</button>
    </div>
  </div>
</div>

<script>
/* =====================================================================
   BOX COUNTDOWN TRACKER — single-file offline app
   All state lives in localStorage under STORAGE_KEY.
   Modular sections: State, Date Utils, Rendering, Timer, Goals,
   Modal, Stats, Import/Export, Confetti, Keyboard shortcuts, Init.
   ===================================================================== */

/* ---------------------------------------------------------------------
   1. STATE / STORAGE
   --------------------------------------------------------------------- */
const STORAGE_KEY = 'boxCountdownAppData';

const DEFAULT_STATE = {
  targetDate: null,   // 'YYYY-MM-DD'
  startDate: null,    // 'YYYY-MM-DD' — day the countdown was created
  title: '',
  theme: 'light',
  goals: {}           // { 'YYYY-MM-DD': { title, notes, tasks:[{text,done}], completed } }
};

let state = loadState();

function loadState(){
  try{
    const raw = localStorage.getItem(STORAGE_KEY);
    if(!raw) return structuredClone(DEFAULT_STATE);
    const parsed = JSON.parse(raw);
    return Object.assign(structuredClone(DEFAULT_STATE), parsed);
  }catch(e){
    console.warn('Could not parse stored data, resetting.', e);
    return structuredClone(DEFAULT_STATE);
  }
}

function saveState(){
  localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
}

/* ---------------------------------------------------------------------
   2. DATE UTILITIES
   --------------------------------------------------------------------- */
function todayISO(){
  const d = new Date();
  return isoFromParts(d.getFullYear(), d.getMonth(), d.getDate());
}
function isoFromParts(y,m,d){
  const mm = String(m+1).padStart(2,'0');
  const dd = String(d).padStart(2,'0');
  return `${y}-${mm}-${dd}`;
}
function parseISO(iso){
  const [y,m,d] = iso.split('-').map(Number);
  return new Date(y, m-1, d);
}
function diffDays(isoA, isoB){
  // number of whole days from A to B (B - A), ignoring time-of-day
  const a = parseISO(isoA), b = parseISO(isoB);
  return Math.round((b - a) / 86400000);
}
function addDays(iso, n){
  const d = parseISO(iso);
  d.setDate(d.getDate()+n);
  return isoFromParts(d.getFullYear(), d.getMonth(), d.getDate());
}
function formatPre
