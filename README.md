<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DecideTogether</title>
  
  <!-- Load React, ReactDOM, and Babel from a free public network so browsers understand it instantly -->
  <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <style>
    @import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=DM+Sans:ital,wght=0,300;0,500;1,300&display=swap');

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
    .input-field::placeholder { letter-spacing: 3px; font-size: 16px; }
    .input-field:focus { border-color: var(--accent2); }

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
    .code-hint { font-size: 13px; color: var(--muted); margin-top: 10px; }

    .members-list { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 20px; }
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
    .pref-options { display: flex; gap: 8px; flex-wrap: wrap; }
    .pref-btn {
      padding: 10px 18px;
      border-radius: 30px;
      border: 1.5px solid var(--border);
      background: var(--surface);
      color: var(--text);
      font-size: 14px;
      font-weight: 500;
      cursor: pointer;
      transition: all 0.15s;
    }
    .pref-btn.selected {
      background: var(--accent2);
      border-color: var(--accent2);
      color: white;
      box-shadow: 0 2px 12px rgba(123,92,240,0.4);
    }

    .progress-bar { height: 3px; background: var(--surface2); border-radius: 2px; margin-bottom: 28px; overflow: hidden; }
    .progress-fill { height: 100%; background: linear-gradient(90deg, var(--accent2), var(--accent)); transition: width 0.4s ease; }

    .swipe-area { flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 20px 0; }
    .counter-text { font-size: 13px; color: var(--muted); margin-bottom: 24px; font-family: 'Syne', sans-serif; }
    .swipe-card {
      width: 100%;
      max-width: 320px;
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: 24px;
      padding: 40px 32px;
      text-align: center;
      position: relative;
    }
    .card-emoji { font-size: 72px; display: block; margin-bottom: 16px; }
    .card-name { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 28px; margin-bottom: 8px; }
    .card-tags { display: flex; gap: 6px; justify-content: center; flex-wrap: wrap; }
    .tag { background: var(--surface2); border: 1px solid var(--border); border-radius: 20px; padding: 4px 10px; font-size: 11px; color: var(--muted); }

    .vote-actions { display: flex; gap: 14px; justify-content: center; margin-top: 28px; }
    .vote-btn { width: 64px; height: 64px; border-radius: 50%; border: none; cursor: pointer; font-size: 24px; display: flex; align-items: center; justify-content: center; transition: all 0.2s; }
    .vote-btn:hover { transform: scale(1.12); }
    .vote-btn.no-btn  { background: rgba(255,60,110,0.15); border: 2px solid rgba(255,60,110,0.4); }
    .vote-btn.yes-btn { background: rgba(0,229,192,0.15); border: 2px solid rgba(0,229,192,0.4); }
    .vote-btn.maybe-btn { background: rgba(255,196,77,0.15); border: 2px solid rgba(255,196,77,0.4); }

    .results-header { text-align: center; margin-bottom: 28px; }
    .results-header h2 { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 26px; margin-bottom: 6px; }
    .results-header p { color: var(--muted); font-size: 14px; }

    .result-item { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 16px 18px; margin-bottom: 10px; display: flex; align-items: center; gap: 14px; position: relative; overflow: hidden; }
    .result-rank { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 20px; color: var(--muted); width: 28px; text-align: center; }
    .result-rank.top { color: var(--accent); }
    .result-emoji { font-size: 28px; }
    .result-info { flex: 1; }
    .result-name { font-family: 'Syne', sans-serif; font-weight: 700; font-size: 16px; }
    .result-score-bar { height: 4px; background: var(--surface2); border-radius: 2px; margin-top: 6px; overflow: hidden; }
    .result-score-fill { height: 100%; background: linear-gradient(90deg, var(--accent2), var(--accent3)); }
    .result-pct { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 18px; color: var(--accent3); }

    .challenge-card { background: linear-gradient(135deg, var(--accent2), var(--accent)); border-radius: 20px; padding: 28px; text-align: center; margin-bottom: 20px; }
    .challenge-card h3 { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 18px; margin-bottom: 16px; opacity: 0.9; }
    .challenge-text { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 22px; line-height: 1.3; margin-bottom: 4px; }

    .toast { position: fixed; bottom: 32px; left: 50%; transform: translateX(-50%) translateY(80px); background: var(--surface2); border: 1px solid var(--accent3); color: var(--accent3); padding: 12px 24px; border-radius: 30px; font-size: 14px; font-weight: 500; transition: transform 0.3s ease; z-index: 100; }
    .toast.show { transform: translateX(-50%) translateY(0); }

    .name-input { width: 100%; background: var(--surface); border: 1.5px solid var(--border); border-radius: 12px; padding: 14px 16px; color: var(--text); font-size: 16px; outline: none; margin-bottom: 16px; }
    .name-input:focus { border-color: var(--accent2); }

    .scroll-list { flex: 1; overflow-y: auto; }
    .divider { height: 1px; background: var(--border); margin: 20px 0; }
    .small-text { font-size: 13px; color: var(--muted); text-align: center; margin-top: 12px; }
  </style>
</head>
<body>

  <!-- Destination where React renders the app on the web page -->
  <div id="root"></div>

  <!-- React Logic Script block compiled in browser -->
  <script type="text/babel">
    const { useState, useEffect } = React;

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

    const DB = {}; 

    function getOrCreateGroup(code) {
      if (!DB[code]) DB[code] = { members: [], votes: {} };
      return DB[code];
    }

    function DecideTogether() {
      const [screen, setScreen] = useState("home"); 
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
        let filtered = ACTIVITIES.filter(a => {
          const t = a.tags;
          const budgetOk = !prefs.budget || t.includes(prefs.budget.toLowerCase().replace(" ", ""));
          const distOk = !prefs.distance || prefs.distance === "Anywhere" || t.includes(prefs.distance.toLowerCase().replace(" ", ""));
          const energyOk = !prefs.energy || t.includes(prefs.energy.toLowerCase());
          const typeOk = !prefs.type || t.includes(prefs.type.toLowerCase());
          return budgetOk || distOk || energyOk || typeOk; 
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
            <h2>Create Group</h2>
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
            <h2>Join Group</h2>
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
          { key: "budget", label: "💸 Budget", options: [{ label: "Free", val: "free" }, { label: "Cheap", val: "cheap" }, { label: "Medium", val: "medium" }, { label: "Expensive", val: "expensive" }] },
          { key: "distance", label: "📍 Distance", options: [{ label: "1 km", val: "1km" }, { label: "5 km", val: "5km" }, { label: "10 km", val: "10km" }, { label: "Anywhere", val: "anywhere" }] },
          { key: "energy", label: "⚡ Energy Level", options: [{ label: "😴 Chill", val: "chill" }, { label: "😊 Normal", val: "normal" }, { label: "🤪 Crazy", val: "crazy" }] },
          { key: "danger", label: "⚠️ Danger Level", options: [{ label: "🛡️ Safe", val: "safe" }, { label: "🌶️ Adventurous", val: "adventurous" }, { label: "💀 Extreme", val: "extreme" }] },
          { key: "type", label: "👥 Group Type", options: [{ label: "👫 Friends", val: "friends" }, { label: "👨‍👩‍👧 Family", val: "family" }, { label: "💑 Date", val: "date" }, { label: "🧘 Solo", val: "solo" }] },
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
              <h2>Your Preferences</h2>
              <p style={{ color: "var(--muted)", fontSize: 13, marginBottom: 20 }}>{filledCount}/{steps.length} answered</p>
              <div className="progress-bar"><div className="progress-fill" style={{ width: `${progress}%` }} /></div>
              <div className="scroll-list">
                {steps.map(s => (
                  <div key={s.key} className="pref-section">
                    <div className="section-title">{s.label}</div>
                    <div className="pref-options">
                      {s.options.map(o => (
                        <button key={o.val} className={`pref-btn ${prefs[s.key] === o.val ? "selected" : ""}`} onClick={() => setPref(s.key, o.val)}>{o.label}</button>
                      ))}
                    </div>
                  </div>
                ))}
              </div>
              <div style={{ paddingTop: 16 }}>
                <button className="btn btn-primary" onClick={handlePrefsComplete} disabled={!prefsDone}>Find Activities →</button>
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
              <div className="progress-bar"><div className="progress-fill" style={{ width: `${pct}%` }} /></div>
              <div className="swipe-area">
                <div className="counter-text">{voteIndex + 1} / {activities.length}</div>
                <div className="swipe-card">
                  <span className="card-emoji">{current.emoji}</span>
                  <div className="card-name">{current.name}</div>
                  <div className="card-tags">{current.tags.slice(0, 4).map(t => <span key={t} className="tag">{t}</span>)}</div>
                </div>
                <div className="vote-actions">
                  <button className="vote-btn no-btn" onClick={() => vote(current.id, "no")}>👎</button>
                  <button className="vote-btn maybe-btn" onClick={() => vote(current.id, "maybe")}>🤔</button>
                  <button className="vote-btn yes-btn" onClick={() => vote(current.id, "yes")}>👍</button>
                </div>
              </div>
            </div>
          </div>
        );
      }

      if (screen === "results") return (
        <div className="app">
          <div className="screen">
            <Logo />
            <div className="results-header">
              <h2>Top Matches! 🏆</h2>
              <p>Here is what your group agreed on</p>
            </div>
            <div className="scroll-list">
              {results.slice(0, 5).map((r, i) => (
                <div key={r.id} className="result-item">
                  <div className={`result-rank ${i === 0 ? "top" : ""}`}>#{i + 1}</div>
                  <span className="result-emoji">{r.emoji}</span>
                  <div className="result-info">
                    <div className="result-name">{r.name}</div>
                    <div className="result-score-bar"><div className="result-score-fill" style={{ width: `${r.pct}%` }} /></div>
                  </div>
                  <div className="result-pct">{r.pct}%</div>
                </div>
              ))}
              
              <div className="divider" />
              <button className="btn btn-secondary" onClick={spinChallenge} disabled={spinning}>
                {spinning ? "Spinning..." : "🎰 Spin Side-Quest Challenge"}
              </button>
              
              {challenge && (
                <div className="challenge-card" style={{ marginTop: 16 }}>
                  <h3>Your Group Mission:</h3>
                  <div className="challenge-text">{challenge}</div>
                </div>
              )}
            </div>
          </div>
        </div>
      );
    }

    // Render app inside HTML file root element
    const root = AppReactDOM.createRoot(document.getElementById('root'));
    root.render(<DecideTogether />);
  </script>
</body>
</html>
