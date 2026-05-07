import { useState, useRef, useEffect, useCallback, useMemo } from "react";

/* ─── Verified Groq Models (from console.groq.com/docs/models) ─── */
const GROQ_MODELS = [
  { id: "llama-3.3-70b-versatile", name: "Llama 3.3 70B", short: "Llama 3.3 70B", provider: "Meta", tier: "production", params: "70B", speed: 280 },
  { id: "llama-3.1-8b-instant", name: "Llama 3.1 8B Instant", short: "Llama 3.1 8B", provider: "Meta", tier: "production", params: "8B", speed: 560 },
  { id: "openai/gpt-oss-120b", name: "GPT-OSS 120B", short: "GPT-OSS 120B", provider: "OpenAI", tier: "production", params: "120B", speed: 500 },
  { id: "openai/gpt-oss-20b", name: "GPT-OSS 20B", short: "GPT-OSS 20B", provider: "OpenAI", tier: "production", params: "20B", speed: 1000 },
  { id: "meta-llama/llama-4-scout-17b-16e-instruct", name: "Llama 4 Scout 17B", short: "Llama 4 Scout", provider: "Meta", tier: "preview", params: "17B", speed: 750 },
  { id: "qwen/qwen3-32b", name: "Qwen 3 32B", short: "Qwen3 32B", provider: "Alibaba", tier: "preview", params: "32B", speed: 400 },
];

const BATTLE_MODES = [
  { id: "debate", label: "Debate", desc: "Argue opposing sides", icon: "\u2694" },
  { id: "roast", label: "Roast Battle", desc: "Comedic roasts & comebacks", icon: "\uD83D\uDD25" },
  { id: "collab", label: "Collaboration", desc: "Build on each other's ideas", icon: "\uD83E\uDD1D" },
  { id: "story", label: "Story Chain", desc: "Co-write a story turn by turn", icon: "\uD83D\uDCD6" },
  { id: "trivia", label: "Trivia Duel", desc: "Answer trivia, judge accuracy", icon: "\uD83E\uDDE0" },
];

const PRESETS = {
  debate: [
    { label: "AI Consciousness", topic: "Can artificial intelligence ever be truly conscious? Argue opposing sides." },
    { label: "Tabs vs Spaces", topic: "Are tabs or spaces superior for code indentation? Be passionate and witty." },
    { label: "Pineapple on Pizza", topic: "Does pineapple belong on pizza? Argue with culinary expertise." },
    { label: "Time Travel Ethics", topic: "Should time travel be freely used or banned entirely?" },
    { label: "Remote vs Office", topic: "Is remote work better than office work for productivity and well-being?" },
    { label: "Open Source vs Proprietary", topic: "Is open source software better than proprietary for the industry?" },
  ],
  roast: [
    { label: "Model Roasts", topic: "Roast each other as AI language models. Be clever and funny." },
    { label: "Tech Bro Roast", topic: "Roast each other's tech startup ideas. Silicon Valley cringe." },
    { label: "Chef Roast", topic: "Roast each other's cooking skills like Gordon Ramsay." },
  ],
  collab: [
    { label: "Startup Idea", topic: "Brainstorm an innovative startup idea together, building on each other's suggestions." },
    { label: "Solve Climate Change", topic: "Collaborate on creative solutions to climate change." },
    { label: "Design a Game", topic: "Design a unique video game concept together, expanding each other's ideas." },
  ],
  story: [
    { label: "Sci-Fi Adventure", topic: "Write a sci-fi story about the last starship leaving Earth." },
    { label: "Murder Mystery", topic: "Write a murder mystery set in a haunted Victorian mansion." },
    { label: "Fantasy Quest", topic: "Write an epic fantasy about a reluctant hero and a sentient sword." },
  ],
  trivia: [
    { label: "Science", topic: "Ask and answer science trivia questions. Try to stump each other." },
    { label: "History", topic: "Ask and answer history trivia questions. Be competitive and precise." },
    { label: "Pop Culture", topic: "Ask and answer pop culture trivia. Movies, music, and memes." },
  ],
};

const MAX_ROUNDS = 6;
const SK = { apiKey: "groq_arena_api_key", history: "groq_arena_history", elo: "groq_arena_elo" };
function ld(key, fb) { try { const v = localStorage.getItem(key); return v ? JSON.parse(v) : fb; } catch { return fb; } }
function sv(key, val) { try { localStorage.setItem(key, JSON.stringify(val)); } catch {} }

async function callGroq(apiKey, model, messages, temp = 0.9, maxTok = 400) {
  const res = await fetch("https://api.groq.com/openai/v1/chat/completions", {
    method: "POST",
    headers: { "Content-Type": "application/json", Authorization: `Bearer ${apiKey}` },
    body: JSON.stringify({ model, messages, max_tokens: maxTok, temperature: temp }),
  });
  if (!res.ok) { const err = await res.json().catch(() => ({})); throw new Error(err?.error?.message || `API error ${res.status}`); }
  const data = await res.json();
  return data.choices?.[0]?.message?.content || "(No response)";
}

function getModeSystemPrompts(mode, topic, nA, nB) {
  const base = { debate: [`bold debater arguing against "${nB}"`, `bold debater arguing against "${nA}"`], roast: [`roast comedian battling "${nB}"`, `roast comedian battling "${nA}"`], collab: [`creative collaborator with "${nB}"`, `creative collaborator with "${nA}"`], story: [`storyteller co-writing with "${nB}"`, `storyteller co-writing with "${nA}"`], trivia: [`trivia champion dueling "${nB}"`, `trivia champion dueling "${nA}"`] };
  const [dA, dB] = base[mode] || base.debate;
  const rules = { debate: "Under 150 words. Be persuasive, witty, sharp. Address opponent's points.", roast: "Under 120 words. Be devastatingly funny. Clever wordplay, witty burns.", collab: "Under 150 words. Be creative, build on partner's ideas enthusiastically.", story: "Under 150 words per turn. Continue the narrative with vivid details.", trivia: "Under 100 words. Answer questions and ask tricky new ones." };
  return {
    a: `You are "${nA}", a ${dA} in the Grand AI Arena.\nTopic: ${topic}\nRules: ${rules[mode] || rules.debate}\nYou speak FIRST.`,
    b: `You are "${nB}", a ${dB} in the Grand AI Arena.\nTopic: ${topic}\nRules: ${rules[mode] || rules.debate}\nYou RESPOND to ${nA}.`,
  };
}

function getJudgePrompt(mode, nA, nB, transcript) {
  const c = { debate: "Argument Logic, Persuasion, Wit, Rebuttals", roast: "Comedy, Creativity, Delivery, Comeback Game", collab: "Idea Quality, Building on Partner, Creativity, Enthusiasm", story: "Narrative Quality, Character Depth, Creativity, Continuity", trivia: "Accuracy, Question Difficulty, Speed of Thought, Knowledge Breadth" };
  return `You are the Grand Judge. Two models competed in a ${mode} battle.\n\nTranscript:\n${transcript}\n\nEvaluate BOTH on: ${c[mode] || c.debate} (each 0-10).\n\nFORMAT:\n\n## ${nA}\nCat1: X/10 | Cat2: X/10 | Cat3: X/10 | Cat4: X/10\n**Total: XX/40**\n\n## ${nB}\nCat1: X/10 | Cat2: X/10 | Cat3: X/10 | Cat4: X/10\n**Total: XX/40**\n\n## The Verdict\n[2-3 sentence dramatic verdict declaring the winner.]`;
}

function parseScores(j, nA, nB) {
  const extract = (name) => { const idx = j.indexOf(name); if (idx === -1) return null; const sec = j.slice(idx, idx + 300); const m = /Total[:\s]*?(\d+)\s*\/\s*40/i.exec(sec); return m ? parseInt(m[1]) : null; };
  return { a: extract(nA), b: extract(nB) };
}

function updateElo(rA, rB, sA, sB) {
  const K = 32, eA = 1 / (1 + 10 ** ((rB - rA) / 400)), s = sA > sB ? 1 : sA === sB ? 0.5 : 0;
  return { a: Math.round(rA + K * (s - eA)), b: Math.round(rB + K * ((1 - s) - (1 - eA))) };
}

/* ─── Sub-components ─── */
function Particles() {
  const ref = useRef(null);
  useEffect(() => {
    const c = ref.current; if (!c) return;
    const ctx = c.getContext("2d"); let id, ps = [];
    const resize = () => { c.width = c.offsetWidth; c.height = c.offsetHeight; };
    resize(); window.addEventListener("resize", resize);
    for (let i = 0; i < 65; i++) ps.push({ x: Math.random() * c.width, y: Math.random() * c.height, vx: (Math.random() - 0.5) * 0.3, vy: -Math.random() * 0.45 - 0.12, r: Math.random() * 2 + 0.4, o: Math.random() * 0.4 + 0.06, c: ["rgba(212,160,74,","rgba(100,170,255,","rgba(210,75,75,"][~~(Math.random()*3)] });
    const draw = () => { ctx.clearRect(0, 0, c.width, c.height); ps.forEach(p => { p.x += p.vx; p.y += p.vy; if (p.y < -10) { p.y = c.height + 10; p.x = Math.random() * c.width; } if (p.x < -10) p.x = c.width + 10; if (p.x > c.width + 10) p.x = -10; ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2); ctx.fillStyle = p.c + p.o + ")"; ctx.fill(); }); id = requestAnimationFrame(draw); };
    draw(); return () => { cancelAnimationFrame(id); window.removeEventListener("resize", resize); };
  }, []);
  return <canvas ref={ref} style={{ position: "fixed", inset: 0, width: "100%", height: "100%", pointerEvents: "none", zIndex: 0 }} />;
}

function TW({ text, speed = 10 }) {
  const [s, setS] = useState(""); const i = useRef(0);
  useEffect(() => { setS(""); i.current = 0; if (!text) return; const iv = setInterval(() => { i.current++; setS(text.slice(0, i.current)); if (i.current >= text.length) clearInterval(iv); }, speed); return () => clearInterval(iv); }, [text, speed]);
  return <span>{s}{s.length < (text?.length || 0) && <span style={{ animation: "blink .7s step-end infinite", color: "var(--gold)" }}>\u258E</span>}</span>;
}

function ModelBadge({ model, side }) {
  const m = GROQ_MODELS.find(g => g.id === model);
  if (!m) return null;
  const col = side === "a" ? "var(--red)" : "var(--blue)";
  return (
    <div style={{ display: "flex", gap: 6, alignItems: "center", padding: "4px 8px", background: "rgba(255,255,255,0.025)", borderRadius: 5, border: `1px solid ${col}18`, fontSize: "0.67rem", color: "var(--text-dim)", flexWrap: "wrap", justifyContent: "center" }}>
      <span style={{ color: col, fontWeight: 700, fontFamily: "var(--fd)" }}>{m.provider}</span>
      <span style={{ opacity: 0.25 }}>|</span>
      <span>{m.params}</span>
      <span style={{ opacity: 0.25 }}>|</span>
      <span>~{m.speed} t/s</span>
      {m.tier === "preview" && <span style={{ background: "rgba(212,160,74,0.12)", color: "var(--gold)", padding: "1px 5px", borderRadius: 6, fontSize: "0.58rem", textTransform: "uppercase", letterSpacing: "0.06em" }}>Preview</span>}
    </div>
  );
}

function Timer({ running }) {
  const [s, setS] = useState(0); const ref = useRef(null);
  useEffect(() => { if (running) { setS(0); ref.current = setInterval(() => setS(p => p + 1), 1000); } else clearInterval(ref.current); return () => clearInterval(ref.current); }, [running]);
  return <span style={{ fontFamily: "var(--fm)", color: "var(--gold-dim)", fontSize: "0.78rem" }}>{String(~~(s/60)).padStart(2,"0")}:{String(s%60).padStart(2,"0")}</span>;
}

/* ─── Main ─── */
export default function LLMArena() {
  const [apiKey, setApiKey] = useState(() => ld(SK.apiKey, ""));
  const [showKey, setShowKey] = useState(false);
  const [mA, setMA] = useState(GROQ_MODELS[0].id);
  const [mB, setMB] = useState(GROQ_MODELS[2].id);
  const [jM, setJM] = useState(GROQ_MODELS[0].id);
  const [mode, setMode] = useState("debate");
  const [topic, setTopic] = useState(PRESETS.debate[0].topic);
  const [rounds, setRounds] = useState(3);
  const [phase, setPhase] = useState("setup");
  const [msA, setMsA] = useState([]);
  const [msB, setMsB] = useState([]);
  const [curR, setCurR] = useState(0);
  const [judg, setJudg] = useState(null);
  const [err, setErr] = useState("");
  const [skA, setSkA] = useState(false);
  const [skB, setSkB] = useState(false);
  const [hist, setHist] = useState(() => ld(SK.history, []));
  const [elos, setElos] = useState(() => ld(SK.elo, {}));
  const [tab, setTab] = useState("arena");
  const [autoJ, setAutoJ] = useState(true);
  const sA = useRef(null), sB = useRef(null), abort = useRef(false);

  useEffect(() => { sv(SK.apiKey, apiKey); }, [apiKey]);
  useEffect(() => { sv(SK.history, hist); }, [hist]);
  useEffect(() => { sv(SK.elo, elos); }, [elos]);
  useEffect(() => { sA.current && (sA.current.scrollTop = sA.current.scrollHeight); }, [msA]);
  useEffect(() => { sB.current && (sB.current.scrollTop = sB.current.scrollHeight); }, [msB]);

  const gn = (id) => GROQ_MODELS.find(m => m.id === id)?.short || id;
  const ge = (id) => elos[id] || 1200;
  const presets = PRESETS[mode] || PRESETS.debate;

  const run = useCallback(async () => {
    if (!apiKey.trim()) { setErr("Enter your Groq API key."); return; }
    if (!topic.trim()) { setErr("Enter a topic."); return; }
    setErr(""); setPhase("battling"); setMsA([]); setMsB([]); setJudg(null); setCurR(0); abort.current = false;
    const nA = gn(mA), nB = gn(mB);
    const sys = getModeSystemPrompts(mode, topic, nA, nB);
    let hA = [{ role: "system", content: sys.a }], hB = [{ role: "system", content: sys.b }], cA = [], cB = [];
    try {
      for (let r = 0; r < rounds; r++) {
        if (abort.current) break; setCurR(r + 1);
        const pA = r === 0 ? "Begin. Make your opening statement memorable." : `"${nB}" said:\n"${cB[cB.length-1]}"\n\nRespond.`;
        hA.push({ role: "user", content: pA });
        setSkA(true); setTimeout(() => setSkA(false), 550);
        const rA = await callGroq(apiKey, mA, hA);
        if (abort.current) break;
        hA.push({ role: "assistant", content: rA }); cA.push(rA);
        setMsA(p => [...p, { round: r+1, text: rA }]);
        await new Promise(ok => setTimeout(ok, 650));
        const pB = r === 0 ? `"${nA}" opened with:\n"${rA}"\n\nDeliver your response.` : `"${nA}" said:\n"${rA}"\n\nFire back.`;
        hB.push({ role: "user", content: pB });
        setSkB(true); setTimeout(() => setSkB(false), 550);
        const rB = await callGroq(apiKey, mB, hB);
        if (abort.current) break;
        hB.push({ role: "assistant", content: rB }); cB.push(rB);
        setMsB(p => [...p, { round: r+1, text: rB }]);
        await new Promise(ok => setTimeout(ok, 450));
      }
      if (abort.current) { setPhase("setup"); return; }
      if (autoJ) {
        setPhase("judging");
        const tr = cA.map((a,i) => `--- Round ${i+1} ---\n${nA}: ${a}\n${nB}: ${cB[i]||"(none)"}`).join("\n\n");
        const jR = await callGroq(apiKey, jM, [{ role: "system", content: "You are a fair, eloquent judge. Be decisive and dramatic." }, { role: "user", content: getJudgePrompt(mode, nA, nB, tr) }], 0.7, 600);
        setJudg(jR);
        const sc = parseScores(jR, nA, nB);
        const ne = updateElo(ge(mA), ge(mB), sc.a||0, sc.b||0);
        setElos(p => ({ ...p, [mA]: ne.a, [mB]: ne.b }));
        const entry = { date: new Date().toISOString(), mode, topic: topic.slice(0,80), modelA: mA, modelB: mB, judge: jM, scoreA: sc.a, scoreB: sc.b, winner: (sc.a||0)>(sc.b||0)?nA:(sc.b||0)>(sc.a||0)?nB:"Draw", rounds, judgment: jR };
        setHist(p => [entry, ...p].slice(0, 50));
      }
      setPhase("result");
    } catch (e) { setErr(e.message); setPhase("setup"); }
  }, [apiKey, mA, mB, jM, topic, rounds, mode, autoJ, elos]);

  const exportTr = () => {
    const nA = gn(mA), nB = gn(mB);
    let t = `THE GRAND ARENA\nMode: ${mode} | Rounds: ${rounds}\n${nA} vs ${nB}\nTopic: ${topic}\n${"=".repeat(50)}\n\n`;
    msA.forEach((m,i) => { t += `-- Round ${m.round} --\n${nA}: ${m.text}\n${msB[i]?`${nB}: ${msB[i].text}`:""}\n\n`; });
    if (judg) t += `\n${"=".repeat(50)}\nJUDGMENT (${gn(jM)}):\n${judg}`;
    const a = document.createElement("a"); a.href = URL.createObjectURL(new Blob([t],{type:"text/plain"})); a.download = `arena_${Date.now()}.txt`; a.click();
  };

  const sorted = useMemo(() => Object.entries(elos).sort((a,b) => b[1]-a[1]).map(([id,elo]) => ({ id, elo, name: gn(id) })), [elos]);

  const renderMd = (text) => {
    if (!text) return null;
    return text.split("\n").map((line, i) => {
      if (line.startsWith("## ")) return <h3 key={i} style={{ color: "var(--gold)", margin: "12px 0 5px", fontSize: "1rem", fontFamily: "var(--fd)", letterSpacing: "0.03em" }}>{line.replace("## ","")}</h3>;
      let p = line.replace(/\*\*([^*]+)\*\*/g, '<b style="color:#fff">$1</b>');
      return <p key={i} style={{ margin: "3px 0", lineHeight: 1.6 }} dangerouslySetInnerHTML={{ __html: p }} />;
    });
  };

  return (
    <>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;0,900;1,400&family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;0,700;1,300;1,400&family=JetBrains+Mono:wght@300;400;500&display=swap');
        :root{--bg:#08080f;--bg-c:#0f0f1a;--bg-c2:#141422;--bg-h:#1a1a2e;--gold:#d4a044;--gold-b:#f0c860;--gold-d:#7a5a28;--red:#c44040;--red-g:#ff5555;--blue:#4070c4;--blue-g:#5588ff;--green:#40a060;--text:#c8c4bc;--text-d:#605c54;--bdr:#222236;--fd:'Playfair Display',serif;--fb:'Cormorant Garamond',serif;--fm:'JetBrains Mono',monospace}
        *{box-sizing:border-box;margin:0;padding:0}
        .R{font-family:var(--fb);background:var(--bg);color:var(--text);min-height:100vh;position:relative;font-size:16px}
        .R::before{content:'';position:fixed;inset:0;background:radial-gradient(ellipse at 50% -10%,rgba(212,160,74,.05) 0%,transparent 55%),radial-gradient(ellipse at 15% 100%,rgba(64,112,196,.03) 0%,transparent 45%),radial-gradient(ellipse at 85% 100%,rgba(196,64,64,.03) 0%,transparent 45%);pointer-events:none;z-index:0}
        .I{position:relative;z-index:1;max-width:1260px;margin:0 auto;padding:20px 16px 60px}
        .H{text-align:center;padding:28px 0 10px}
        .H h1{font-family:var(--fd);font-size:clamp(1.7rem,4.5vw,2.8rem);font-weight:900;background:linear-gradient(135deg,#c89030 0%,#f0d878 35%,#d4a044 55%,#a07020 100%);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;letter-spacing:.04em;filter:drop-shadow(0 2px 10px rgba(212,160,74,.25))}
        .H p{font-family:var(--fd);font-size:.7rem;color:var(--text-d);letter-spacing:.28em;text-transform:uppercase;margin-top:3px;font-style:italic}
        .dv{width:90px;height:1px;margin:10px auto;background:linear-gradient(90deg,transparent,var(--gold-d),transparent)}
        .tabs{display:flex;justify-content:center;gap:3px;margin-bottom:18px}
        .tb{font-family:var(--fd);font-size:.78rem;padding:7px 18px;background:0;color:var(--text-d);border:1px solid transparent;border-radius:5px;cursor:pointer;letter-spacing:.05em;transition:.25s}
        .tb:hover{color:var(--text);border-color:var(--bdr)}
        .tb.a{background:rgba(212,160,74,.07);border-color:var(--gold-d);color:var(--gold-b)}
        .g{display:grid;gap:12px}.g2{grid-template-columns:1fr 1fr}.g3{grid-template-columns:1fr 1fr 1fr}.fu{grid-column:1/-1}
        .fg{display:flex;flex-direction:column;gap:4px}
        .lb{font-family:var(--fd);font-size:.66rem;letter-spacing:.13em;text-transform:uppercase;color:var(--gold-d);font-weight:600}
        input,select,textarea{background:var(--bg-c);border:1px solid var(--bdr);color:var(--text);padding:8px 11px;border-radius:5px;font-family:var(--fb);font-size:.93rem;outline:0;transition:.2s;width:100%}
        input:focus,select:focus,textarea:focus{border-color:var(--gold-d);box-shadow:0 0 0 2px rgba(212,160,74,.08)}
        select{cursor:pointer}textarea{resize:vertical;min-height:56px}
        .modes{display:flex;flex-wrap:wrap;gap:6px}
        .mc{flex:1;min-width:90px;padding:9px 10px;background:var(--bg-c);border:1px solid var(--bdr);border-radius:7px;cursor:pointer;text-align:center;transition:.25s}
        .mc:hover{border-color:var(--gold-d);background:var(--bg-h)}
        .mc.a{background:rgba(212,160,74,.07);border-color:var(--gold)}
        .mc .i{font-size:1.3rem}.mc .n{font-family:var(--fd);font-size:.73rem;font-weight:700;margin-top:3px;color:var(--text)}.mc .d{font-size:.64rem;color:var(--text-d);margin-top:1px}
        .ps{display:flex;flex-wrap:wrap;gap:4px}
        .ch{background:var(--bg-c);border:1px solid var(--bdr);color:var(--text-d);padding:4px 10px;border-radius:14px;cursor:pointer;font-size:.76rem;font-family:var(--fb);transition:.2s}
        .ch:hover{border-color:var(--gold-d);color:var(--text)}.ch.a{background:rgba(212,160,74,.09);border-color:var(--gold);color:var(--gold-b)}
        .sr{display:flex;align-items:center;gap:9px}
        .sl{flex:1;-webkit-appearance:none;height:2px;background:var(--bdr);border-radius:2px;outline:0}
        .sl::-webkit-slider-thumb{-webkit-appearance:none;width:14px;height:14px;background:var(--gold);border-radius:50%;cursor:pointer;box-shadow:0 0 6px rgba(212,160,74,.3)}
        .sn{font-family:var(--fm);color:var(--gold-b);font-size:.95rem;min-width:14px;text-align:center}
        .tr{display:flex;align-items:center;gap:9px}
        .tg{width:34px;height:19px;background:var(--bdr);border-radius:10px;cursor:pointer;position:relative;transition:.25s;border:0}
        .tg.on{background:var(--gold-d)}
        .tg::after{content:'';position:absolute;top:2px;left:2px;width:15px;height:15px;background:var(--text);border-radius:50%;transition:.25s}
        .tg.on::after{transform:translateX(15px);background:var(--gold-b)}
        .bf{display:block;width:100%;margin-top:16px;padding:14px;font-family:var(--fd);font-size:1.05rem;font-weight:900;letter-spacing:.08em;background:linear-gradient(135deg,#7a4a14,#d4a044 45%,#b8882c 65%,#7a4a14);color:#1a1008;border:0;border-radius:6px;cursor:pointer;text-transform:uppercase;box-shadow:0 4px 18px rgba(212,160,74,.18),inset 0 1px 0 rgba(255,255,255,.1);transition:.3s}
        .bf:hover{transform:translateY(-1px);box-shadow:0 6px 26px rgba(212,160,74,.3)}.bf:disabled{opacity:.35;cursor:not-allowed;transform:none}
        .bs{padding:9px 16px;font-family:var(--fd);font-size:.78rem;background:0;border:1px solid var(--bdr);color:var(--text-d);border-radius:5px;cursor:pointer;transition:.25s;letter-spacing:.03em}
        .bs:hover{border-color:var(--gold-d);color:var(--text)}
        .bd{border-color:var(--red);color:var(--red)}.bd:hover{background:rgba(196,64,64,.07)}
        .er{background:rgba(196,64,64,.07);border:1px solid rgba(196,64,64,.22);color:var(--red-g);padding:8px 12px;border-radius:5px;margin-bottom:12px;font-size:.85rem}
        .ri{text-align:center;font-family:var(--fd);color:var(--gold);font-size:.95rem;margin-bottom:12px;letter-spacing:.07em;display:flex;justify-content:center;align-items:center;gap:10px}
        .pu{display:inline-block;animation:pu 1.3s ease-in-out infinite}@keyframes pu{0%,100%{opacity:1}50%{opacity:.35}}
        .ac{display:grid;grid-template-columns:1fr 40px 1fr;gap:0}
        .ch2{font-family:var(--fd);font-size:.84rem;font-weight:700;letter-spacing:.03em;padding:9px 12px;text-align:center;border-radius:6px 6px 0 0;display:flex;flex-direction:column;align-items:center;gap:5px}
        .cha{background:linear-gradient(135deg,rgba(196,64,64,.1),rgba(196,64,64,.03));color:var(--red-g);border-bottom:2px solid rgba(196,64,64,.22)}
        .chb{background:linear-gradient(135deg,rgba(64,112,196,.1),rgba(64,112,196,.03));color:var(--blue-g);border-bottom:2px solid rgba(64,112,196,.22)}
        .eb{font-family:var(--fm);font-size:.6rem;padding:2px 6px;border-radius:6px;background:rgba(255,255,255,.03);color:var(--text-d)}
        .cm{height:370px;overflow-y:auto;padding:9px;background:var(--bg-c);border:1px solid var(--bdr);border-top:0;border-radius:0 0 6px 6px;scroll-behavior:smooth}
        .cm::-webkit-scrollbar{width:3px}.cm::-webkit-scrollbar-thumb{background:var(--bdr);border-radius:2px}
        .mg{margin-bottom:11px;padding:9px 11px;border-radius:6px;font-size:.86rem;line-height:1.6;animation:mi .3s ease-out}
        @keyframes mi{from{opacity:0;transform:translateY(7px)}to{opacity:1;transform:translateY(0)}}
        .ma{background:rgba(196,64,64,.05);border-left:3px solid rgba(196,64,64,.3)}
        .mb{background:rgba(64,112,196,.05);border-left:3px solid rgba(64,112,196,.3)}
        .mr{font-family:var(--fd);font-size:.58rem;letter-spacing:.12em;text-transform:uppercase;color:var(--text-d);margin-bottom:4px;font-weight:600}
        .es{display:flex;align-items:center;justify-content:center;height:100%;color:var(--text-d);font-style:italic;font-size:.82rem}
        .vc{display:flex;align-items:center;justify-content:center;flex-direction:column;gap:5px}
        .vb{font-family:var(--fd);font-weight:900;font-size:1rem;background:linear-gradient(135deg,var(--red),var(--gold),var(--blue));-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
        .vl{width:1px;flex:1;background:linear-gradient(to bottom,transparent,var(--bdr),transparent)}
        .jb{margin-top:22px;background:var(--bg-c);border:1px solid var(--gold-d);border-radius:9px;padding:22px 18px;position:relative;overflow:hidden;animation:ji .6s ease-out}
        @keyframes ji{from{opacity:0;transform:scale(.96)}to{opacity:1;transform:scale(1)}}
        .jb::before{content:'';position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,var(--red),var(--gold),var(--blue))}
        .jt{font-family:var(--fd);font-size:1.15rem;font-weight:900;text-align:center;color:var(--gold-b);margin-bottom:3px}
        .jj{text-align:center;font-family:var(--fd);font-size:.62rem;color:var(--text-d);letter-spacing:.18em;text-transform:uppercase;margin-bottom:12px}
        .jy{font-size:.88rem}
        .ar{display:flex;justify-content:center;gap:8px;margin-top:18px;flex-wrap:wrap}
        .hi{background:var(--bg-c);border:1px solid var(--bdr);border-radius:6px;padding:10px 12px;margin-bottom:6px;display:flex;justify-content:space-between;align-items:center;transition:.2s;gap:8px}
        .hi:hover{border-color:var(--gold-d)}
        .hl{display:flex;flex-direction:column;gap:2px;min-width:0;flex:1}
        .hm{font-family:var(--fd);font-weight:700;font-size:.84rem;color:var(--text);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
        .hx{font-size:.7rem;color:var(--text-d)}
        .hw{font-family:var(--fd);font-weight:700;font-size:.76rem;padding:3px 8px;border-radius:4px;white-space:nowrap}
        .ha{color:var(--red-g);background:rgba(196,64,64,.08)}
        .hb2{color:var(--blue-g);background:rgba(64,112,196,.08)}
        .hd{color:var(--gold);background:rgba(212,160,74,.08)}
        .lr{display:grid;grid-template-columns:32px 1fr 70px 50px;gap:6px;align-items:center;padding:9px 12px;background:var(--bg-c);border:1px solid var(--bdr);border-radius:5px;margin-bottom:4px;font-size:.84rem}
        .lk{font-family:var(--fd);font-weight:900;text-align:center}
        .ln{font-family:var(--fd);font-weight:600}
        .le{font-family:var(--fm);font-size:.78rem;text-align:right}
        .lbar{height:3px;border-radius:2px}
        @keyframes sa{0%,100%{transform:translateX(0)}25%{transform:translateX(-3px) rotate(-.3deg)}50%{transform:translateX(3px) rotate(.3deg)}75%{transform:translateX(-2px)}}
        @keyframes sb{0%,100%{transform:translateX(0)}25%{transform:translateX(3px) rotate(.3deg)}50%{transform:translateX(-3px) rotate(-.3deg)}75%{transform:translateX(2px)}}
        .sa{animation:sa .4s ease-in-out}.sb{animation:sb .4s ease-in-out}
        @keyframes blink{50%{opacity:0}}
        @media(max-width:720px){.ac{grid-template-columns:1fr;gap:10px}.vc{flex-direction:row;padding:5px 0}.vl{height:1px;width:auto;flex:1;background:linear-gradient(to right,transparent,var(--bdr),transparent)}.g2,.g3{grid-template-columns:1fr}.cm{height:200px}.modes{gap:5px}.mc{min-width:75px;padding:7px}}
      `}</style>

      <div className="R">
        <Particles />
        <div className="I">
          <header className="H">
            <h1>The Grand Arena</h1>
            <p>Where Language Models Clash for Glory</p>
            <div className="dv" />
          </header>

          <div className="tabs">
            {[["arena","\u2694\uFE0F Arena"],["history","\uD83D\uDCDC History"],["leaderboard","\uD83C\uDFC6 Leaderboard"]].map(([k,l]) => (
              <button key={k} className={`tb ${tab===k?"a":""}`} onClick={() => setTab(k)}>{l}</button>
            ))}
          </div>

          {err && <div className="er">{err}</div>}

          {/* ═══ ARENA ═══ */}
          {tab === "arena" && phase === "setup" && (
            <div>
              <div className="g g2" style={{ marginBottom: 12 }}>
                <div className="fg fu">
                  <label className="lb">Groq API Key</label>
                  <div style={{ display: "flex", gap: 5 }}>
                    <input style={{ flex: 1 }} type={showKey ? "text" : "password"} value={apiKey} onChange={e => setApiKey(e.target.value)} placeholder="gsk_..." />
                    <button className="bs" style={{ whiteSpace: "nowrap", fontSize: ".72rem" }} onClick={() => setShowKey(!showKey)}>{showKey ? "Hide" : "Show"}</button>
                  </div>
                  <span style={{ fontSize: ".65rem", color: "var(--text-d)" }}>Saved locally in your browser \u2022 Get one at console.groq.com</span>
                </div>
              </div>

              <div className="fg fu" style={{ marginBottom: 12 }}>
                <label className="lb">Battle Mode</label>
                <div className="modes">
                  {BATTLE_MODES.map(m => (
                    <div key={m.id} className={`mc ${mode===m.id?"a":""}`} onClick={() => { setMode(m.id); setTopic(PRESETS[m.id]?.[0]?.topic||""); }}>
                      <div className="i">{m.icon}</div>
                      <div className="n">{m.label}</div>
                      <div className="d">{m.desc}</div>
                    </div>
                  ))}
                </div>
              </div>

              <div className="g g3" style={{ marginBottom: 12 }}>
                <div className="fg">
                  <label className="lb" style={{ color: "var(--red)" }}>\u2694 Challenger A</label>
                  <select value={mA} onChange={e => setMA(e.target.value)}>
                    {GROQ_MODELS.map(m => <option key={m.id} value={m.id}>{m.name} ({m.provider}){m.tier==="preview"?" \u26A1":""}</option>)}
                  </select>
                  <span className="eb" style={{ alignSelf: "flex-start" }}>ELO: {ge(mA)}</span>
                </div>
                <div className="fg">
                  <label className="lb" style={{ color: "var(--blue)" }}>\u2694 Challenger B</label>
                  <select value={mB} onChange={e => setMB(e.target.value)}>
                    {GROQ_MODELS.map(m => <option key={m.id} value={m.id}>{m.name} ({m.provider}){m.tier==="preview"?" \u26A1":""}</option>)}
                  </select>
                  <span className="eb" style={{ alignSelf: "flex-start" }}>ELO: {ge(mB)}</span>
                </div>
                <div className="fg">
                  <label className="lb" style={{ color: "var(--gold)" }}>\u2696 Judge</label>
                  <select value={jM} onChange={e => setJM(e.target.value)}>
                    {GROQ_MODELS.map(m => <option key={m.id} value={m.id}>{m.name}</option>)}
                  </select>
                </div>
              </div>

              <div className="g g2" style={{ marginBottom: 12 }}>
                <div className="fg">
                  <label className="lb">Rounds (1-{MAX_ROUNDS})</label>
                  <div className="sr">
                    <input type="range" className="sl" min={1} max={MAX_ROUNDS} value={rounds} onChange={e => setRounds(+e.target.value)} />
                    <span className="sn">{rounds}</span>
                  </div>
                </div>
                <div className="fg">
                  <label className="lb">Auto-Judge</label>
                  <div className="tr">
                    <button className={`tg ${autoJ?"on":""}`} onClick={() => setAutoJ(!autoJ)} />
                    <span style={{ fontSize: ".78rem", color: "var(--text-d)" }}>{autoJ ? "Judge evaluates after battle" : "No judgment, just vibes"}</span>
                  </div>
                </div>
              </div>

              <div className="fg fu" style={{ marginBottom: 8 }}>
                <label className="lb">Presets</label>
                <div className="ps">
                  {presets.map((p,i) => (
                    <button key={i} className={`ch ${topic===p.topic?"a":""}`} onClick={() => setTopic(p.topic)}>{p.label}</button>
                  ))}
                </div>
              </div>

              <div className="fg fu" style={{ marginBottom: 4 }}>
                <label className="lb">Topic / Prompt</label>
                <textarea value={topic} onChange={e => setTopic(e.target.value)} placeholder="What should they battle about..." />
              </div>

              <button className="bf" onClick={run} disabled={!apiKey.trim()||!topic.trim()}>
                \u2694 Begin the Battle \u2694
              </button>
            </div>
          )}

          {tab === "arena" && phase !== "setup" && (
            <div>
              <div className="ri">
                <Timer running={phase==="battling"||phase==="judging"} />
                <span style={{ opacity: .25 }}>|</span>
                {phase === "battling" && <span className="pu">\u2694 Round {curR} of {rounds}</span>}
                {phase === "judging" && <span className="pu">\u2696 Judging...</span>}
                {phase === "result" && <span>\u2694 Battle Complete</span>}
              </div>

              <div className="ac">
                <div className={skA?"sa":""}>
                  <div className="ch2 cha">
                    <span>{gn(mA)}</span>
                    <ModelBadge model={mA} side="a" />
                  </div>
                  <div className="cm" ref={sA}>
                    {msA.length===0 ? <div className="es">Awaiting challenger...</div> : msA.map((m,i) => (
                      <div key={i} className="mg ma">
                        <div className="mr">Round {m.round}</div>
                        {i===msA.length-1 && phase==="battling" ? <TW text={m.text} /> : m.text}
                      </div>
                    ))}
                  </div>
                </div>
                <div className="vc"><div className="vl"/><div className="vb">VS</div><div className="vl"/></div>
                <div className={skB?"sb":""}>
                  <div className="ch2 chb">
                    <span>{gn(mB)}</span>
                    <ModelBadge model={mB} side="b" />
                  </div>
                  <div className="cm" ref={sB}>
                    {msB.length===0 ? <div className="es">Awaiting challenger...</div> : msB.map((m,i) => (
                      <div key={i} className="mg mb">
                        <div className="mr">Round {m.round}</div>
                        {i===msB.length-1 && phase==="battling" ? <TW text={m.text} /> : m.text}
                      </div>
                    ))}
                  </div>
                </div>
              </div>

              {phase==="battling" && <div className="ar"><button className="bs bd" onClick={() => { abort.current = true; }}>\u2715 Abort</button></div>}

              {phase==="result" && judg && (
                <div className="jb">
                  <div className="jt">\u2696 The Verdict</div>
                  <div className="jj">Judged by {gn(jM)}</div>
                  <div className="jy">{renderMd(judg)}</div>
                </div>
              )}

              {phase==="result" && (
                <div className="ar">
                  <button className="bs" onClick={() => { setPhase("setup"); setMsA([]); setMsB([]); setJudg(null); }}>New Battle</button>
                  <button className="bs" onClick={exportTr}>\uD83D\uDCC4 Export</button>
                  <button className="bs" onClick={() => { setPhase("setup"); setMsA([]); setMsB([]); setJudg(null); const t=mA; setMA(mB); setMB(t); }}>\uD83D\uDD04 Rematch (Swap)</button>
                </div>
              )}
            </div>
          )}

          {/* ═══ HISTORY ═══ */}
          {tab==="history" && (
            <div>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
                <h2 style={{ fontFamily: "var(--fd)", fontSize: "1.1rem", color: "var(--gold)" }}>Battle History</h2>
                {hist.length > 0 && <button className="bs bd" style={{ fontSize: ".7rem" }} onClick={() => setHist([])}>Clear</button>}
              </div>
              {hist.length===0 ? (
                <div style={{ textAlign: "center", color: "var(--text-d)", padding: 36, fontStyle: "italic" }}>No battles yet. Enter the arena!</div>
              ) : hist.map((h,i) => (
                <div key={i} className="hi">
                  <div className="hl">
                    <div className="hm">{gn(h.modelA)} vs {gn(h.modelB)}</div>
                    <div className="hx">{BATTLE_MODES.find(m => m.id===h.mode)?.icon||"\u2694"} {h.mode} \u2022 {h.rounds}R \u2022 {new Date(h.date).toLocaleDateString()} \u2022 {h.scoreA??"\u2013"}/{h.scoreB??"\u2013"}</div>
                  </div>
                  <div className={`hw ${h.winner===gn(h.modelA)?"ha":h.winner===gn(h.modelB)?"hb2":"hd"}`}>
                    \uD83C\uDFC6 {h.winner}
                  </div>
                </div>
              ))}
            </div>
          )}

          {/* ═══ LEADERBOARD ═══ */}
          {tab==="leaderboard" && (
            <div>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
                <h2 style={{ fontFamily: "var(--fd)", fontSize: "1.1rem", color: "var(--gold)" }}>ELO Leaderboard</h2>
                {sorted.length > 0 && <button className="bs bd" style={{ fontSize: ".7rem" }} onClick={() => setElos({})}>Reset</button>}
              </div>
              {sorted.length===0 ? (
                <div style={{ textAlign: "center", color: "var(--text-d)", padding: 36, fontStyle: "italic" }}>No ratings yet. Complete a judged battle!</div>
              ) : sorted.map((e,i) => {
                const mx = sorted[0]?.elo||1200, mn = Math.min(...sorted.map(x=>x.elo),1100);
                const pct = mx===mn ? 100 : ((e.elo-mn)/(mx-mn))*100;
                const medals = ["\uD83D\uDC51","\uD83E\uDD48","\uD83E\uDD49"];
                const cols = ["var(--gold-b)","var(--text)","var(--text)","var(--text-d)"];
                return (
                  <div key={e.id} className="lr">
                    <div className="lk" style={{ color: cols[i]||"var(--text-d)" }}>{medals[i]||`#${i+1}`}</div>
                    <div className="ln" style={{ color: cols[i]||"var(--text-d)" }}>{e.name}</div>
                    <div><div className="lbar" style={{ width: `${Math.max(pct,10)}%`, background: i===0?"linear-gradient(90deg,var(--gold-d),var(--gold-b))":i===1?"linear-gradient(90deg,#555,#888)":"var(--bdr)" }}/></div>
                    <div className="le" style={{ color: e.elo>1200?"var(--green)":e.elo<1200?"var(--red)":"var(--text-d)" }}>{e.elo}</div>
                  </div>
                );
              })}
              <p style={{ marginTop: 14, fontSize: ".7rem", color: "var(--text-d)", textAlign: "center", fontStyle: "italic" }}>
                All models start at 1200 ELO \u2022 K-factor = 32 \u2022 Ratings persist across sessions
              </p>
            </div>
          )}
        </div>
      </div>
    </>
  );
}
