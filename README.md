import { useState, useEffect, useRef } from "react";

// ─── Data ────────────────────────────────────────────────────────────────────
const ACTIVITIES = [
  { id: 1, name: "Bowling", emoji: "🎳", tags: ["cheap","medium","1km","5km","10km","chill","normal","friends","family","date"] },
  { id: 2, name: "Cinema", emoji: "🎬", tags: ["cheap","medium","1km","5km","chill","normal","friends","family","date"] },
  { id: 3, name: "Arcade", emoji: "🕹️", tags: ["cheap","medium","5km","10km","normal","crazy","friends","family","date"] },
  { id: 4, name: "Swimming", emoji: "🏊", tags: ["cheap","medium","5km","10km","normal","crazy","friends","family","date"] },
  { id: 5, name: "Football", emoji: "⚽", tags: ["free","cheap","anywhere","normal","crazy","friends","family"] },
  { id: 6, name: "BBQ", emoji: "🔥", tags: ["cheap","medium","anywhere","chill","normal","friends","family"] },
  { id: 7, name: "Escape Room", emoji: "🔐", tags: ["medium","expensive","5km","10km","normal","crazy","friends","date"] },
  { id: 8, name: "Mini Golf", emoji: "⛳", tags: ["cheap","medium","5km","10km","chill","normal","friends","family","date"] },
  { id: 9, name: "Laser Tag", emoji: "🔫", tags: ["medium","expensive","5km","10km","normal","crazy","friends"] },
  { id: 10, name: "Hiking", emoji: "🥾", tags: ["free","cheap","anywhere","normal","crazy","adventurous","friends","family","date"] },
  { id: 11, name: "Beach", emoji: "🏖️", tags: ["free","cheap","anywhere","chill","normal","friends","family","date"] },
  { id: 12, name: "Fishing", emoji: "🎣", tags: ["cheap","anywhere","chill","friends","family","solo"] },
  { id: 13, name: "Trampoline Park", emoji: "🤸", tags: ["medium","expensive","10km","anywhere","crazy","friends","family"] },
  { id: 14, name: "Ice Skating", emoji: "⛸️", tags: ["cheap","medium","5km","10km","chill","normal","friends","family","date"] },
  { id: 15, name: "Karaoke", emoji: "🎤", tags: ["cheap","medium","5km","10km","normal","crazy","friends","date"] },
  { id: 16, name: "Board Games Café", emoji: "🎲", tags: ["cheap","medium","1km","5km","chill","normal","friends","family","date"] },
  { id: 17, name: "Rock Climbing", emoji: "🧗", tags: ["medium","expensive","5km","10km","crazy","adventurous","extreme","friends","date"] },
  { id: 18, name: "Paintball", emoji: "🎯", tags: ["expensive","10km","anywhere","crazy","extreme","adventurous","friends"] },
];

const CHALLENGES = [
  "Buy snacks under 50 kr 🍫",
  "Play football for 30 minutes ⚽",
  "Go somewhere you've never been before 🗺️",
  "Take a group photo challenge 📸",
  "Everyone wears a silly hat 🎩",
  "No phones for 1 hour 📵",
  "Random restaurant — spin the menu 🍽️",
  "Teach each other one skill 🤝",
  "Write a group haiku about the day 📝",
  "Film a 15-second TikTok together 🎥",
];

function genCode() {
  return Math.floor(100000 + Math.random() * 900000).toString();
}

// ─── Fake "persistence" via shared state in memory (simulates multi-user) ─────
const DB = {}; // groupCode → { members, votes }

function getOrCreateGroup(code) {
  if (!DB[code]) DB[code] = { members: [], votes: {} };
  return DB[code];
}

// ─── Styles ──────────────────────────────────────────────────────────────────
const css = `
  @import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=DM+Sans:ital,wght@0,300;0,500;1,300&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #0a0a0f;
    --surface: #13131c;
    --surface2: #1d1d2e;
    --border: rgba(255,255,255,0.08);
    --accent: #ff3c6e;
    --accent2: #7b5cf0;
    --accent3: #00e5c0;
    --text: #f0f0f8;
    --muted: #6b6b8a;
    --yes: #00e5c0;
    --no: #ff3c6e;
    --maybe: #ffc44d;
  }

  body { background: var(--bg); color: var(--text); font-family: 'DM Sans', sans-serif; min-height: 100vh; }

  .app {
    max-width: 480px;
    margin: 0 auto;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    position: relative;
    overflow: hidden;
  }

  /* Ambient glow bg */
  .app::before {
    content: '';
    position: fixed;
    top: -200px; left: -200px;
    width: 600px; height: 600px;
    background: radial-gradient(circle, rgba(123,92,240,0.15) 0%, transparent 70%);
    pointer-events: none;
    z-index: 0;
  }
  .app::after {
    content: '';
    position: fixed;
    bottom: -200px; right: -200px;
    width: 600px; height: 600px;
    background: radial-gradient(circle, rgba(255,60,110,0.12) 0%, transparent 70%);
    pointer-events: none;
    z-index: 0;
  }

  .screen {
    flex: 1;
    display: flex;
    flex-direction: column;
    padding: 24px 20px 40px;
    position: relative;
    z-index: 1;
    animation: fadeUp 0.35s ease both;
  }

  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(20px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  /* ── Header ── */
  .header {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 32px;
  }
  .logo-mark {
    width: 38px; height: 38px;
    background: linear-gradient(135deg, var(--accent2), var(--accent));
    border-radius: 10px;
    display: flex; align-items: center; justify-content: center;
    font-size: 18px;
  }
  .logo-text {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 18px;
    letter-spacing: -0.5px;
  }
  .logo-text span { color: var(--accent); }
  .back-btn {
    margin-left: auto;
    background: var(--surface);
    border: 1px solid var(--border);
    color: var(--muted);
    padding: 6px 14px;
    border-radius: 20px;
    cursor: pointer;
    font-size: 13px;
    font-family: 'DM Sans', sans-serif;
    transition: all 0.2s;
  }
  .back-btn:hover { color: var(--text); border-color: var(--accent2); }

  /* ── Hero ── */
  .hero { text-align: center; padding: 20px 0 32px; }
  .hero-emoji { font-size: 64px; display: block; margin-bottom: 16px;
    animation: float 3s ease-in-out infinite; }
  @keyframes float {
    0%,100% { transform: translateY(0); }
    50%      { transform: translateY(-8px); }
  }
  .hero h1 {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 32px;
    line-height: 1.1;
    letter-spacing: -1px;
    margin-bottom: 10px;
  }
  .hero h1 em { font-style: normal; color: var(--accent); }
  .hero p { color: var(--muted); font-size: 15px; line-height: 1.5; }

  /* ── Buttons ── */
  .btn {
    display: block;
    width: 100%;
    padding: 16px;
    border-radius: 14px;
    border: none;
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 16px;
    cursor: pointer;
    transition: all 0.2s;
    text-align: center;
  }
  .btn-primary {
    background: linear-gradient(135deg, var(--accent2), var(--accent));
    color: white;
    box-shadow: 0 4px 24px rgba(255,60,110,0.3);
  }
  .btn-primary:hover { transform: translateY(-2px); box-shadow: 0 8px 32px rgba(255,60,110,0.4); }
  .btn-primary:active { transform: translateY(0); }
  .btn-secondary {
    background: var(--surface);
    color: var(--text);
    border: 1px solid var(--border);
  }
  .btn-secondary:hover { border-color: var(--accent2); background: var(--surface2); }
  .btn-ghost {
    background: transparent;
    color: var(--muted);
    border: 1px dashed var(--border);
  }
  .btn-ghost:hover { color: var(--text); border-color: var(--muted); }
  .btn + .btn { margin-top: 10px; }

  /* ── Input ── */
  .input-group { margin-bottom: 20px; }
  .input-group label {
    display: block;
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1.5px;
    color: var(--muted);
    margin-bottom: 8px;
    font-weight: 500;
  }
  .input-field {
    width: 100%;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 14px 16px;
    color: var(--text);
    font-size: 24px;
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    letter-spacing: 6px;
    text-align: center;
    outline: none;
    transition: border-color 0.2s;
  }
  .input-field::placeholder { color: var(--muted); letter-spacing: 3px; font-size: 16px; }
  .input-field:focus { border-color: var(--accent2); }

  /* ── Code Display ── */
  .code-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 28px;
    text-align: center;
    margin-bottom: 24px;
    position: relative;
    overflow: hidden;
  }
  .code-card::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, rgba(123,92,240,0.08), rgba(255,60,110,0.08));
  }
  .code-label {
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 2px;
    color: var(--muted);
    margin-bottom: 12px;
  }
  .code-value {
    font-family: 'Syne', sans-serif;
    font-size: 48px;
    font-weight: 800;
    letter-spacing: 8px;
    background: linear-gradient(135deg, var(--accent3), var(--accent2));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }
  .code-hint {
    font-size: 13px;
    color: var(--muted);
    margin-top: 10px;
  }

  /* ── Members list ── */
  .members-list {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
  }
  .member-chip {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 6px 14px;
    font-size: 13px;
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .member-chip.you { border-color: var(--accent2); color: var(--accent2); }
  .member-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--accent3); }

  /* ── Prefs ── */
  .section-title {
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 13px;
    text-transform: uppercase;
    letter-spacing: 1.5px;
    color: var(--muted);
    margin-bottom: 10px;
  }
  .pref-section { margin-bottom: 24px; }
  .pref-options {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }
  .pref-btn {
    padding: 10px 18px;
    border-radius: 30px;
    border: 1.5px solid var(--border);
    background: var(--surface);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.15s;
    white-space: nowrap;
  }
  .pref-btn:hover { border-color: var(--accent2); }
  .pref-btn.selected {
    background: var(--accent2);
    border-color: var(--accent2);
    color: white;
    box-shadow: 0 2px 12px rgba(123,92,240,0.4);
  }

  /* ── Progress ── */
  .progress-bar {
    height: 3px;
    background: var(--surface2);
    border-radius: 2px;
    margin-bottom: 28px;
    overflow: hidden;
  }
  .progress-fill {
    height: 100%;
    background: linear-gradient(90deg, var(--accent2), var(--accent));
    border-radius: 2px;
    transition: width 0.4s ease;
  }

  /* ── Swipe card ── */
  .swipe-area {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 20px 0;
  }
  .counter-text {
    font-size: 13px;
    color: var(--muted);
    margin-bottom: 24px;
    font-family: 'Syne', sans-serif;
  }
  .swipe-card {
    width: 100%;
    max-width: 320px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 24px;
    padding: 40px 32px;
    text-align: center;
    position: relative;
    overflow: hidden;
    transition: transform 0.15s, box-shadow 0.15s;
    cursor: grab;
    user-select: none;
  }
  .swipe-card.dragging { cursor: grabbing; }
  .swipe-card::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, rgba(123,92,240,0.06), transparent);
  }
  .card-emoji { font-size: 72px; display: block; margin-bottom: 16px; }
  .card-name {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 28px;
    letter-spacing: -0.5px;
    margin-bottom: 8px;
  }
  .card-tags { display: flex; gap: 6px; justify-content: center; flex-wrap: wrap; }
  .tag {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 4px 10px;
    font-size: 11px;
    color: var(--muted);
    text-transform: capitalize;
  }

  /* Vote overlay hints */
  .vote-hint-yes, .vote-hint-no, .vote-hint-maybe {
    position: absolute;
    top: 20px;
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 22px;
    border: 3px solid;
    padding: 6px 16px;
    border-radius: 8px;
    opacity: 0;
    transform: rotate(-15deg);
    pointer-events: none;
    transition: opacity 0.1s;
  }
  .vote-hint-yes { color: var(--yes); border-color: var(--yes); left: 20px; transform: rotate(-12deg); }
  .vote-hint-no  { color: var(--no);  border-color: var(--no);  right: 20px; transform: rotate(12deg); }

  .vote-actions {
    display: flex;
    gap: 14px;
    justify-content: center;
    margin-top: 28px;
  }
  .vote-btn {
    width: 64px; height: 64px;
    border-radius: 50%;
    border: none;
    cursor: pointer;
    font-size: 24px;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.2s;
    position: relative;
  }
  .vote-btn:hover { transform: scale(1.12); }
  .vote-btn:active { transform: scale(0.95); }
  .vote-btn.no-btn  { background: rgba(255,60,110,0.15); border: 2px solid rgba(255,60,110,0.4); }
  .vote-btn.no-btn:hover  { background: rgba(255,60,110,0.3); }
  .vote-btn.yes-btn { background: rgba(0,229,192,0.15); border: 2px solid rgba(0,229,192,0.4); }
  .vote-btn.yes-btn:hover { background: rgba(0,229,192,0.3); }
  .vote-btn.maybe-btn { background: rgba(255,196,77,0.15); border: 2px solid rgba(255,196,77,0.4); }
  .vote-btn.maybe-btn:hover { background: rgba(255,196,77,0.3); }

  /* ── Results ── */
  .results-header { text-align: center; margin-bottom: 28px; }
  .results-header h2 {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 26px;
    margin-bottom: 6px;
  }
  .results-header p { color: var(--muted); font-size: 14px; }

  .result-item {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 16px 18px;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    gap: 14px;
    position: relative;
    overflow: hidden;
    transition: transform 0.2s;
  }
  .result-item:hover { transform: translateX(4px); }
  .result-fill {
    position: absolute;
    inset: 0;
    border-radius: 16px;
    opacity: 0.07;
    transition: width 0.6s ease;
  }
  .result-rank {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 20px;
    color: var(--muted);
    width: 28px;
    text-align: center;
    flex-shrink: 0;
  }
  .result-rank.top { color: var(--accent); }
  .result-emoji { font-size: 28px; flex-shrink: 0; }
  .result-info { flex: 1; }
  .result-name {
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 16px;
    margin-bottom: 2px;
  }
  .result-score-bar {
    height: 4px;
    background: var(--surface2);
    border-radius: 2px;
    margin-top: 6px;
    overflow: hidden;
  }
  .result-score-fill {
    height: 100%;
    border-radius: 2px;
    background: linear-gradient(90deg, var(--accent2), var(--accent3));
  }
  .result-pct {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 18px;
    color: var(--accent3);
    flex-shrink: 0;
  }
  .result-pct.low { color: var(--muted); }

  /* ── Challenge ── */
  .challenge-card {
    background: linear-gradient(135deg, var(--accent2), var(--accent));
    border-radius: 20px;
    padding: 28px;
    text-align: center;
    margin-bottom: 20px;
    position: relative;
    overflow: hidden;
    animation: pulse-glow 2s ease-in-out infinite;
  }
  @keyframes pulse-glow {
    0%,100% { box-shadow: 0 4px 30px rgba(255,60,110,0.3); }
    50%      { box-shadow: 0 8px 50px rgba(255,60,110,0.6); }
  }
  .challenge-card h3 {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 18px;
    margin-bottom: 16px;
    opacity: 0.9;
  }
  .challenge-text {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 22px;
    line-height: 1.3;
    margin-bottom: 4px;
  }
  .challenge-sub { font-size: 13px; opacity: 0.8; }

  /* ── Toast ── */
  .toast {
    position: fixed;
    bottom: 32px;
    left: 50%;
    transform: translateX(-50%) translateY(80px);
    background: var(--surface2);
    border: 1px solid var(--accent3);
    color: var(--accent3);
    padding: 12px 24px;
    border-radius: 30px;
    font-size: 14px;
    font-weight: 500;
    transition: transform 0.3s ease;
    z-index: 100;
    white-space: nowrap;
  }
  .toast.show { transform: translateX(-50%) translateY(0); }

  /* ── Done state ── */
  .done-banner {
    background: var(--surface);
    border: 1px solid var(--accent3);
    border-radius: 14px;
    padding: 14px 18px;
    text-align: center;
    font-size: 14px;
    color: var(--accent3);
    margin-bottom: 20px;
    font-weight: 500;
  }

  /* ── Name input ── */
  .name-input {
    width: 100%;
    background: var(--surface);
    border: 1.5px solid var(--border);
    border-radius: 12px;
    padding: 14px 16px;
    color: var(--text);
    font-size: 16px;
    font-family: 'DM Sans', sans-serif;
    outline: none;
    transition: border-color 0.2s;
    margin-bottom: 16px;
  }
  .name-input:focus { border-color: var(--accent2); }
  .name-input::placeholder { color: var(--muted); }

  /* scrollable */
  .scroll-list { flex: 1; overflow-y: auto; padding-right: 2px; }
  .scroll-list::-webkit-scrollbar { width: 3px; }
  .scroll-list::-webkit-scrollbar-track { background: transparent; }
  .scroll-list::-webkit-scrollbar-thumb { background: var(--surface2); border-radius: 2px; }

  .divider { height: 1px; background: var(--border); margin: 20px 0; }
  .small-text { font-size: 13px; color: var(--muted); text-align: center; margin-top: 12px; }

  .spin-icon { display: inline-block; animation: spin 0.6s ease-out; }
  @keyframes spin { from { transform: rotate(0); } to { transform: rotate(720deg); } }

  .badge {
    display: inline-block;
    background: var(--accent);
    color: white;
    border-radius: 20px;
    padding: 2px 10px;
    font-size: 11px;
    font-weight: 700;
    font-family: 'Syne', sans-serif;
    margin-left: 6px;
  }
`;

// ─── App ─────────────────────────────────────────────────────────────────────
export default function DecideTogether() {
  const [screen, setScreen] = useState("home"); // home | create | join | lobby | prefs | vote | results
  const [groupCode, setGroupCode] = useState("");
  const [joinCode, setJoinCode] = useState("");
  const [myName, setMyName] = useState("");
  const [members, setMembers] = useState([]);
  const [prefs, setPrefs] = useState({ budget: "", distance: "", energy: "", danger: "", type: "" });
  const [activities, setActivities] = useState([]);
  const [voteIndex, setVoteIndex] = useState(0);
  const [myVotes, setMyVotes] = useState({});
  const [allVotes, setAllVotes] = useState({});
  const [results, setResults] = useState([]);
  const [challenge, setChallenge] = useState(null);
  const [spinning, setSpinning] = useState(false);
  const [toast, setToast] = useState("");
  const [toastVisible, setToastVisible] = useState(false);

  function showToast(msg) {
    setToast(msg);
    setToastVisible(true);
    setTimeout(() => setToastVisible(false), 2200);
  }

  function handleCreateGroup() {
    if (!myName.trim()) return;
    const code = genCode();
    setGroupCode(code);
    const grp = getOrCreateGroup(code);
    grp.members.push(myName.trim());
    setMembers([myName.trim()]);
    setScreen("lobby");
    showToast("Group created! 🎉");
  }

  function handleJoinGroup() {
    if (!joinCode.trim() || !myName.trim()) return;
    const grp = DB[joinCode.trim()];
    if (!grp) { showToast("Group not found ❌"); return; }
    if (!grp.members.includes(myName.trim())) {
      grp.members.push(myName.trim());
    }
    setGroupCode(joinCode.trim());
    setMembers([...grp.members]);
    setScreen("lobby");
    showToast(`Joined group ${joinCode.trim()}! 🙌`);
  }

  function handleStartPrefs() {
    setScreen("prefs");
  }

  function handlePrefsComplete() {
    // filter activities by prefs
    let filtered = ACTIVITIES.filter(a => {
      const t = a.tags;
      const budgetOk = !prefs.budget || t.includes(prefs.budget.toLowerCase().replace(" ", ""));
      const distOk = !prefs.distance || prefs.distance === "Anywhere" || t.includes(prefs.distance.toLowerCase().replace(" ", ""));
      const energyOk = !prefs.energy || t.includes(prefs.energy.toLowerCase());
      const typeOk = !prefs.type || t.includes(prefs.type.toLowerCase());
      return budgetOk || distOk || energyOk || typeOk; // loose match for more activities
    });
    if (filtered.length < 5) filtered = ACTIVITIES.slice(0, 10);
    setActivities(filtered);
    setVoteIndex(0);
    setMyVotes({});
    setScreen("vote");
  }

  function vote(activityId, choice) {
    const newVotes = { ...myVotes, [activityId]: choice };
    setMyVotes(newVotes);
    // Update group db
    const grp = getOrCreateGroup(groupCode);
    if (!grp.votes[myName]) grp.votes[myName] = {};
    grp.votes[myName][activityId] = choice;

    if (voteIndex < activities.length - 1) {
      setVoteIndex(i => i + 1);
    } else {
      computeResults(newVotes);
    }
  }

  function computeResults(votes) {
    const grp = getOrCreateGroup(groupCode);
    // merge all votes including mine
    const allV = { ...grp.votes, [myName]: votes };
    const scores = activities.map(a => {
      let score = 0;
      let total = 0;
      Object.values(allV).forEach(memberVotes => {
        const v = memberVotes[a.id];
        if (v === "yes") score += 2;
        else if (v === "maybe") score += 1;
        total += 2;
      });
      const pct = total > 0 ? Math.round((score / total) * 100) : 0;
      return { ...a, pct };
    });
    scores.sort((a, b) => b.pct - a.pct);
    setResults(scores);
    setAllVotes(allV);
    setScreen("results");
  }

  function spinChallenge() {
    setSpinning(true);
    setTimeout(() => {
      const c = CHALLENGES[Math.floor(Math.random() * CHALLENGES.length)];
      setChallenge(c);
      setSpinning(false);
    }, 600);
  }

  const setPref = (key, val) => setPrefs(p => ({ ...p, [key]: val }));

  const prefsDone = prefs.budget && prefs.distance && prefs.energy && prefs.type;

  // refresh members from DB while in lobby
  useEffect(() => {
    if (screen !== "lobby") return;
    const interval = setInterval(() => {
      const grp = DB[groupCode];
      if (grp) setMembers([...grp.members]);
    }, 1500);
    return () => clearInterval(interval);
  }, [screen, groupCode]);

  const Logo = () => (
    <div className="header">
      <div className="logo-mark">🎯</div>
      <div className="logo-text">Decide<span>Together</span></div>
    </div>
  );

  const BackBtn = ({ to }) => (
    <button className="back-btn" onClick={() => setScreen(to)}>← Back</button>
  );

  // ── SCREENS ──

  if (screen === "home") return (
    <div className="app">
      <div className="screen">
        <Logo />
        <div className="hero">
          <span className="hero-emoji">🎯</span>
          <h1>Stop arguing.<br /><em>Start doing.</em></h1>
          <p>Swipe on activities, match with your crew, and find what everyone actually wants to do.</p>
        </div>
        <button className="btn btn-primary" onClick={() => setScreen("create")}>
          Create a Group
        </button>
        <button className="btn btn-secondary" onClick={() => setScreen("join")}>
          Join with Code
        </button>
        <p className="small-text">No account needed · Free forever</p>
      </div>
    </div>
  );

  if (screen === "create") return (
    <div className="app">
      <div className="screen">
        <div className="header">
          <div className="logo-mark">🎯</div>
          <div className="logo-text">Decide<span>Together</span></div>
          <BackBtn to="home" />
        </div>
        <h2 style={{ fontFamily: "'Syne', sans-serif", fontWeight: 800, fontSize: 24, marginBottom: 8 }}>Create Group</h2>
        <p style={{ color: "var(--muted)", fontSize: 14, marginBottom: 28 }}>Enter your name to get started</p>
        <div className="input-group">
          <label>Your Name</label>
          <input
            className="name-input"
            placeholder="e.g. Alex"
            value={myName}
            onChange={e => setMyName(e.target.value)}
            onKeyDown={e => e.key === "Enter" && handleCreateGroup()}
            maxLength={20}
          />
        </div>
        <button className="btn btn-primary" onClick={handleCreateGroup} disabled={!myName.trim()}>
          Create Group 🚀
        </button>
      </div>
    </div>
  );

  if (screen === "join") return (
    <div className="app">
      <div className="screen">
        <div className="header">
          <div className="logo-mark">🎯</div>
          <div className="logo-text">Decide<span>Together</span></div>
          <BackBtn to="home" />
        </div>
        <h2 style={{ fontFamily: "'Syne', sans-serif", fontWeight: 800, fontSize: 24, marginBottom: 8 }}>Join Group</h2>
        <p style={{ color: "var(--muted)", fontSize: 14, marginBottom: 28 }}>Ask your friend for the 6-digit code</p>
        <div className="input-group">
          <label>Your Name</label>
          <input
            className="name-input"
            placeholder="e.g. Jordan"
            value={myName}
            onChange={e => setMyName(e.target.value)}
            maxLength={20}
          />
        </div>
        <div className="input-group">
          <label>Group Code</label>
          <input
            className="input-field"
            placeholder="000000"
            value={joinCode}
            onChange={e => setJoinCode(e.target.value.replace(/\D/g, "").slice(0, 6))}
            onKeyDown={e => e.key === "Enter" && handleJoinGroup()}
            maxLength={6}
          />
        </div>
        <button className="btn btn-primary" onClick={handleJoinGroup} disabled={!myName.trim() || joinCode.length < 6}>
          Join Group 🙌
        </button>
      </div>
    </div>
  );

  if (screen === "lobby") return (
    <div className="app">
      <div className="screen">
        <Logo />
        <div className="code-card">
          <div className="code-label">Group Code</div>
          <div className="code-value">{groupCode}</div>
          <div className="code-hint">Share this with your friends</div>
        </div>
        <div className="section-title">Members ({members.length})</div>
        <div className="members-list">
          {members.map((m, i) => (
            <div key={i} className={`member-chip ${m === myName ? "you" : ""}`}>
              <div className="member-dot" />
              {m}{m === myName ? " (you)" : ""}
            </div>
          ))}
        </div>
        <div className="divider" />
        <button className="btn btn-primary" onClick={handleStartPrefs}>
          Set My Preferences →
        </button>
        <p className="small-text">Others can still join later</p>
      </div>
      <div className={`toast ${toastVisible ? "show" : ""}`}>{toast}</div>
    </div>
  );

  if (screen === "prefs") {
    const steps = [
      {
        key: "budget", label: "💸 Budget", options: [
          { label: "Free", val: "free" },
          { label: "Cheap", val: "cheap" },
          { label: "Medium", val: "medium" },
          { label: "Expensive", val: "expensive" },
        ]
      },
      {
        key: "distance", label: "📍 Distance", options: [
          { label: "1 km", val: "1km" },
          { label: "5 km", val: "5km" },
          { label: "10 km", val: "10km" },
          { label: "Anywhere", val: "anywhere" },
        ]
      },
      {
        key: "energy", label: "⚡ Energy Level", options: [
          { label: "😴 Chill", val: "chill" },
          { label: "😊 Normal", val: "normal" },
          { label: "🤪 Crazy", val: "crazy" },
        ]
      },
      {
        key: "danger", label: "⚠️ Danger Level", options: [
          { label: "🛡️ Safe", val: "safe" },
          { label: "🌶️ Adventurous", val: "adventurous" },
          { label: "💀 Extreme", val: "extreme" },
        ]
      },
      {
        key: "type", label: "👥 Group Type", options: [
          { label: "👫 Friends", val: "friends" },
          { label: "👨‍👩‍👧 Family", val: "family" },
          { label: "💑 Date", val: "date" },
          { label: "🧘 Solo", val: "solo" },
        ]
      },
    ];
    const filledCount = Object.values(prefs).filter(Boolean).length;
    const progress = (filledCount / steps.length) * 100;

    return (
      <div className="app">
        <div className="screen">
          <div className="header">
            <div className="logo-mark">🎯</div>
            <div className="logo-text">Decide<span>Together</span></div>
            <BackBtn to="lobby" />
          </div>
          <h2 style={{ fontFamily: "'Syne', sans-serif", fontWeight: 800, fontSize: 22, marginBottom: 6 }}>
            Your Preferences
          </h2>
          <p style={{ color: "var(--muted)", fontSize: 13, marginBottom: 20 }}>
            {filledCount}/{steps.length} answered
          </p>
          <div className="progress-bar">
            <div className="progress-fill" style={{ width: `${progress}%` }} />
          </div>
          <div className="scroll-list">
            {steps.map(s => (
              <div key={s.key} className="pref-section">
                <div className="section-title">{s.label}</div>
                <div className="pref-options">
                  {s.options.map(o => (
                    <button
                      key={o.val}
                      className={`pref-btn ${prefs[s.key] === o.val ? "selected" : ""}`}
                      onClick={() => setPref(s.key, o.val)}
                    >
                      {o.label}
                    </button>
                  ))}
                </div>
              </div>
            ))}
          </div>
          <div style={{ paddingTop: 16 }}>
            <button
              className="btn btn-primary"
              onClick={handlePrefsComplete}
              disabled={!prefsDone}
            >
              Find Activities →
            </button>
          </div>
        </div>
      </div>
    );
  }

  if (screen === "vote") {
    const current = activities[voteIndex];
    if (!current) return null;
    const pct = Math.round(((voteIndex) / activities.length) * 100);

    return (
      <div className="app">
        <div className="screen">
          <div className="header">
            <div className="logo-mark">🎯</div>
            <div className="logo-text">Decide<span>Together</span></div>
          </div>
          <div className="progress-bar">
            <div className="progress-fill" style={{ width: `${pct}%` }} />
          </div>
          <div className="swipe-area">
            <div className="counter-text">{voteIndex + 1} / {activities.length}</div>
            <div className="swipe-card">
              <span className="card-emoji">{current.emoji}</span>
              <div className="card-name">{current.name}</div>
              <div className="card-tags">
                {current.tags.slice(0, 4).map(t => <span key={t} className="tag">{t}</span>)}
              </div>
            </div>
            <div className="vote-actions">
              <button className="vote-btn no-btn" onClick={() => vote(current.id, "no")} title="No">👎</button>
              <button className="vote-btn maybe-btn" onClick={() => vote(current.id, "maybe")} title="Maybe">🤔</button>
              <button className="vote-btn yes-btn" onClick={() => vote(current.id, "yes")} title="Yes!">👍</button>
            </div>
            <p className="small-text" style={{ marginTop: 20 }}>
              👎 No &nbsp;&nbsp; 🤔 Maybe &nbsp;&nbsp; 👍 Yes
            </p>
          </div>
        </div>
      </div>
    );
  }

  if (screen === "results") {
    const memberCount = Object.keys(allVotes).length || 1;
    const topThree = results.slice(0, 3);
    const rest = results.slice(3);

    return (
      <div className="app">
        <div className="screen">
          <Logo />
          <div className="results-header">
            <h2>🏆 Top Matches</h2>
            <p>{members.length} member{members.length !== 1 ? "s" : ""} voted · {activities.length} activities</p>
          </div>
          <div className="scroll-list">
            {results.map((a, i) => (
              <div key={a.id} className="result-item">
                <div
                  className="result-fill"
                  style={{
                    width: `${a.pct}%`,
                    background: i === 0 ? "var(--accent)" : i === 1 ? "var(--accent2)" : "var(--accent3)"
                  }}
                />
                <div className={`result-rank ${i < 3 ? "top" : ""}`}>
                  {i === 0 ? "🥇" : i === 1 ? "🥈" : i === 2 ? "🥉" : `${i + 1}`}
                </div>
                <div className="result-emoji">{a.emoji}</div>
                <div className="result-info">
                  <div className="result-name">{a.name}</div>
                  <div className="result-score-bar">
                    <div className="result-score-fill" style={{ width: `${a.pct}%` }} />
                  </div>
                </div>
                <div className={`result-pct ${a.pct < 30 ? "low" : ""}`}>{a.pct}%</div>
              </div>
            ))}

            <div className="divider" />

            {/* Challenge Mode */}
            <div className="section-title">🎲 Random Challenge Mode</div>
            {!challenge ? (
              <button
                className="btn btn-primary"
                onClick={spinChallenge}
                style={{ background: "linear-gradient(135deg, #ff3c6e, #ffc44d)" }}
              >
                {spinning ? <span className="spin-icon">🎰</span> : "🎰"} Spin the Wheel!
              </button>
            ) : (
              <div className="challenge-card">
                <h3>Your Challenge</h3>
                <div className="challenge-text">{challenge}</div>
                <div className="challenge-sub">Share this on TikTok 🎥</div>
              </div>
            )}
            {challenge && (
              <button className="btn btn-ghost" onClick={spinChallenge} style={{ marginTop: 8 }}>
                Spin again ↺
              </button>
            )}

            <div className="divider" />
            <button className="btn btn-secondary" onClick={() => {
              setScreen("home");
              setMyName("");
              setGroupCode("");
              setMembers([]);
              setPrefs({ budget: "", distance: "", energy: "", danger: "", type: "" });
              setActivities([]);
              setMyVotes({});
              setAllVotes({});
              setResults([]);
              setChallenge(null);
            }}>
              New Session 🔄
            </button>
            <button className="btn btn-ghost" onClick={() => setScreen("vote")}>
              Re-vote
            </button>
          </div>
        </div>
        <div className={`toast ${toastVisible ? "show" : ""}`}>{toast}</div>
      </div>
    );
  }

  return null;
}

// Inject styles
const style = document.createElement("style");
style.textContent = css;
document.head.appendChild(style);
