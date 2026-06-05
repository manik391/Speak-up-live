<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>🎙️ SpeakUp Live</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;600;700;800&display=swap" rel="stylesheet"/>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    min-height: 100vh;
    background: #0a0a0f;
    font-family: 'DM Sans', sans-serif;
    color: #f0eeff;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }

  /* BACKGROUND GLOW */
  .bg-glow {
    position: fixed;
    inset: 0;
    pointer-events: none;
    z-index: 0;
    background:
      radial-gradient(ellipse 60% 40% at 50% 0%, rgba(109,40,217,0.25) 0%, transparent 70%),
      radial-gradient(ellipse 40% 40% at 80% 80%, rgba(79,70,229,0.15) 0%, transparent 60%),
      radial-gradient(ellipse 40% 40% at 20% 80%, rgba(139,92,246,0.1) 0%, transparent 60%);
  }

  /* PHASES */
  .screen { display: none; flex-direction: column; align-items: center; justify-content: center; z-index: 1; width: 100%; padding: 24px; }
  .screen.active { display: flex; }

  /* SETUP SCREEN */
  .setup-title { font-size: 32px; font-weight: 800; text-align: center; margin-bottom: 8px;
    background: linear-gradient(135deg, #c4b5fd, #818cf8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
  .setup-sub { color: #6b7280; font-size: 15px; text-align: center; margin-bottom: 40px; }
  .option-row { display: flex; gap: 14px; flex-wrap: wrap; justify-content: center; margin-bottom: 32px; }
  .opt-btn {
    background: rgba(255,255,255,0.05);
    border: 2px solid rgba(255,255,255,0.1);
    border-radius: 14px;
    padding: 16px 24px;
    cursor: pointer;
    font-size: 15px;
    font-weight: 600;
    color: #f0eeff;
    font-family: inherit;
    transition: all 0.2s;
    display: flex; align-items: center; gap: 10px;
  }
  .opt-btn:hover, .opt-btn.selected {
    background: rgba(109,40,217,0.3);
    border-color: #a78bfa;
  }
  .start-btn {
    background: linear-gradient(135deg, #6d28d9, #4f46e5);
    border: none;
    border-radius: 50px;
    padding: 16px 48px;
    font-size: 17px;
    font-weight: 700;
    color: white;
    cursor: pointer;
    font-family: inherit;
    transition: all 0.2s;
    box-shadow: 0 8px 32px rgba(109,40,217,0.4);
  }
  .start-btn:hover { transform: translateY(-2px); box-shadow: 0 12px 40px rgba(109,40,217,0.5); }
  .start-btn:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

  /* VOICE SCREEN */
  #voiceScreen {
    min-height: 100vh;
    gap: 0;
    justify-content: space-between;
    padding: 0;
  }

  /* TOP BAR */
  .top-bar {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 18px 24px;
    border-bottom: 1px solid rgba(255,255,255,0.06);
    background: rgba(255,255,255,0.02);
  }
  .session-info { display: flex; align-items: center; gap: 10px; }
  .lang-badge {
    background: rgba(196,181,253,0.15);
    border: 1px solid rgba(196,181,253,0.25);
    border-radius: 20px;
    padding: 4px 12px;
    font-size: 13px;
    color: #c4b5fd;
  }
  .end-btn {
    background: rgba(239,68,68,0.15);
    border: 1px solid rgba(239,68,68,0.3);
    border-radius: 8px;
    color: #f87171;
    padding: 7px 16px;
    cursor: pointer;
    font-size: 13px;
    font-family: inherit;
    font-weight: 600;
    transition: all 0.2s;
  }
  .end-btn:hover { background: rgba(239,68,68,0.25); }

  /* MAIN VOICE AREA */
  .voice-center {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 32px;
    padding: 24px;
  }

  /* AVATAR */
  .avatar-wrap { position: relative; display: flex; align-items: center; justify-content: center; }
  .avatar-ring {
    position: absolute;
    border-radius: 50%;
    border: 2px solid rgba(167,139,250,0.3);
    animation: none;
  }
  .ring1 { width: 160px; height: 160px; }
  .ring2 { width: 200px; height: 200px; }
  .ring3 { width: 240px; height: 240px; }
  .avatar-ring.active { animation: pulse-ring 1.5s ease-in-out infinite; }
  .ring2.active { animation-delay: 0.2s; }
  .ring3.active { animation-delay: 0.4s; }
  @keyframes pulse-ring {
    0%, 100% { transform: scale(1); opacity: 0.3; }
    50% { transform: scale(1.05); opacity: 0.7; }
  }
  .avatar {
    width: 120px; height: 120px;
    border-radius: 50%;
    background: linear-gradient(135deg, #6d28d9, #4f46e5);
    display: flex; align-items: center; justify-content: center;
    font-size: 48px;
    position: relative;
    z-index: 2;
    box-shadow: 0 0 40px rgba(109,40,217,0.5);
    transition: box-shadow 0.3s;
  }
  .avatar.speaking { box-shadow: 0 0 60px rgba(167,139,250,0.8); }

  /* STATUS */
  .status-area { text-align: center; }
  .status-label {
    font-size: 22px;
    font-weight: 700;
    margin-bottom: 8px;
    min-height: 32px;
  }
  .status-sub { font-size: 14px; color: #6b7280; min-height: 20px; }

  /* TRANSCRIPT BUBBLE */
  .transcript-box {
    width: 100%;
    max-width: 560px;
    min-height: 80px;
    background: rgba(255,255,255,0.04);
    border: 1px solid rgba(255,255,255,0.08);
    border-radius: 20px;
    padding: 18px 22px;
    font-size: 15px;
    line-height: 1.7;
    color: #d1d5db;
    text-align: center;
    transition: all 0.3s;
  }
  .transcript-box.user-speaking {
    border-color: rgba(167,139,250,0.4);
    background: rgba(109,40,217,0.1);
    color: #f0eeff;
  }

  /* FEEDBACK PANEL */
  .feedback-panel {
    width: 100%;
    max-width: 560px;
    background: rgba(99,102,241,0.1);
    border: 1px solid rgba(99,102,241,0.2);
    border-radius: 16px;
    padding: 16px 20px;
    font-size: 13px;
    line-height: 1.8;
    color: #c4b5fd;
    white-space: pre-line;
    display: none;
    animation: fadeIn 0.3s ease;
  }
  .feedback-panel.show { display: block; }
  @keyframes fadeIn { from { opacity:0; transform:translateY(8px); } to { opacity:1; transform:translateY(0); } }

  /* MIC BUTTON */
  .bottom-bar {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 24px;
    gap: 16px;
    border-top: 1px solid rgba(255,255,255,0.06);
  }
  .mic-wrap { position: relative; display: flex; align-items: center; justify-content: center; }
  .mic-pulse {
    position: absolute;
    width: 80px; height: 80px;
    border-radius: 50%;
    background: rgba(239,68,68,0.2);
    display: none;
  }
  .mic-pulse.active { display: block; animation: mic-anim 1s ease-in-out infinite; }
  @keyframes mic-anim { 0%,100%{transform:scale(1);opacity:0.6;} 50%{transform:scale(1.4);opacity:0;} }
  .mic-btn {
    width: 68px; height: 68px;
    border-radius: 50%;
    border: none;
    background: linear-gradient(135deg, #6d28d9, #4f46e5);
    color: white;
    font-size: 28px;
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    position: relative; z-index: 2;
    box-shadow: 0 8px 24px rgba(109,40,217,0.4);
    transition: all 0.2s;
  }
  .mic-btn.recording {
    background: linear-gradient(135deg, #dc2626, #ef4444);
    box-shadow: 0 8px 24px rgba(239,68,68,0.5);
  }
  .mic-btn:disabled { opacity: 0.4; cursor: not-allowed; }
  .mic-hint { font-size: 13px; color: #6b7280; }

  /* ERROR */
  .err-toast {
    position: fixed;
    bottom: 24px; left: 50%;
    transform: translateX(-50%);
    background: rgba(239,68,68,0.9);
    color: white;
    padding: 10px 20px;
    border-radius: 10px;
    font-size: 14px;
    z-index: 100;
    display: none;
    max-width: 90vw;
    text-align: center;
  }
  .err-toast.show { display: block; animation: fadeIn 0.3s ease; }
</style>
</head>
<body>
<div class="bg-glow"></div>

<!-- SCREEN 1: SETUP -->
<div class="screen active" id="setupScreen">
  <div class="setup-title">🎙️ SpeakUp Live</div>
  <div class="setup-sub">AI-এর সাথে সরাসরি কথা বলুন — ঠিক একজন মানুষের মতো</div>

  <div style="font-size:13px;color:#6b7280;margin-bottom:12px;font-weight:600;letter-spacing:0.5px;text-transform:uppercase">ভাষা বেছে নিন</div>
  <div class="option-row" id="langRow">
    <button class="opt-btn" onclick="selectLang('en',this)">🇺🇸 English</button>
    <button class="opt-btn" onclick="selectLang('de',this)">🇩🇪 Deutsch</button>
  </div>

  <div style="font-size:13px;color:#6b7280;margin-bottom:12px;font-weight:600;letter-spacing:0.5px;text-transform:uppercase">টপিক বেছে নিন</div>
  <div class="option-row" id="topicRow">
    <button class="opt-btn" onclick="selectTopic('daily',this)">☀️ Daily Life</button>
    <button class="opt-btn" onclick="selectTopic('travel',this)">✈️ Travel</button>
    <button class="opt-btn" onclick="selectTopic('work',this)">💼 Work</button>
    <button class="opt-btn" onclick="selectTopic('food',this)">🍳 Food</button>
    <button class="opt-btn" onclick="selectTopic('free',this)">💬 Free Talk</button>
  </div>

  <button class="start-btn" id="startBtn" onclick="startSession()" disabled>শুরু করুন →</button>
</div>

<!-- SCREEN 2: VOICE -->
<div class="screen" id="voiceScreen">
  <div class="top-bar">
    <div class="session-info">
      <span style="font-size:20px">🎙️</span>
      <span style="font-weight:700">SpeakUp Live</span>
      <span class="lang-badge" id="sessionBadge"></span>
    </div>
    <button class="end-btn" onclick="endSession()">✕ শেষ করুন</button>
  </div>

  <div class="voice-center">
    <!-- Avatar -->
    <div class="avatar-wrap">
      <div class="avatar-ring ring3" id="ring3"></div>
      <div class="avatar-ring ring2" id="ring2"></div>
      <div class="avatar-ring ring1" id="ring1"></div>
      <div class="avatar" id="avatar">🤖</div>
    </div>

    <!-- Status -->
    <div class="status-area">
      <div class="status-label" id="statusLabel">সংযুক্ত হচ্ছে...</div>
      <div class="status-sub" id="statusSub"></div>
    </div>

    <!-- Live transcript -->
    <div class="transcript-box" id="transcriptBox">AI কথা বলবে, আপনি শুনুন...</div>

    <!-- Feedback -->
    <div class="feedback-panel" id="feedbackPanel"></div>
  </div>

  <div class="bottom-bar">
    <div class="mic-wrap">
      <div class="mic-pulse" id="micPulse"></div>
      <button class="mic-btn" id="micBtn" onclick="toggleMic()" disabled>🎤</button>
    </div>
    <div class="mic-hint" id="micHint">AI কথা বলা শেষ হলে বাটন চাপুন</div>
  </div>
</div>

<!-- Error Toast -->
<div class="err-toast" id="errToast"></div>

<script>
const TOPICS = {
  en: {
    daily:  "Let's have a friendly conversation about daily life, routines, and hobbies.",
    travel: "Let's talk about travel — destinations, experiences, and adventures.",
    work:   "Let's discuss work, career goals, and professional life.",
    food:   "Let's chat about food, cooking, restaurants, and cuisine.",
    free:   "Let's have a free, open conversation about anything you want to talk about."
  },
  de: {
    daily:  "Lass uns über den Alltag, Routinen und Hobbys sprechen.",
    travel: "Lass uns über Reisen, Ziele und Erlebnisse sprechen.",
    work:   "Lass uns über Arbeit, Karriere und Berufsleben sprechen.",
    food:   "Lass uns über Essen, Kochen und Restaurants sprechen.",
    free:   "Lass uns frei über alles sprechen, was du möchtest."
  }
};

const SYSTEM = {
  en: (topic) => `You are Alex, a warm and encouraging English conversation partner. You help the user practice speaking English.

Topic: ${topic}

Rules:
- Speak naturally and conversationally, like a real person (NOT like an AI assistant).
- Keep your replies SHORT: 1-3 sentences maximum. This is a live voice conversation.
- After your reply, add the line "---FEEDBACK---" then give brief feedback:
  ✅ Good: [what they did well]
  📝 Fix: [one correction if needed, or "Perfect!"]
  💡 Try: [one better phrase they could use]
- Be warm, encouraging, and keep the conversation flowing with a question.
- Never use bullet points in your conversational reply — only in feedback.`,

  de: (topic) => `Du bist Alex, ein freundlicher Gesprächspartner zum Deutschüben.

Thema: ${topic}

Regeln:
- Sprich natürlich und locker, wie ein echter Mensch (NICHT wie ein KI-Assistent).
- Halte deine Antworten KURZ: maximal 1-3 Sätze. Dies ist ein Live-Gespräch.
- Nach deiner Antwort schreibe "---FEEDBACK---" und gib kurzes Feedback:
  ✅ Gut: [was gut war]
  📝 Korrektur: [eine Korrektur oder "Perfekt!"]
  💡 Versuch: [ein besserer Ausdruck]
- Sei warm und ermutigend, stelle eine Frage am Ende.`
};

let lang = null, topicKey = null, history = [], isSpeaking = false, isRecording = false, recognition = null;

// ── SETUP ──
function selectLang(l, btn) {
  lang = l;
  document.querySelectorAll('#langRow .opt-btn').forEach(b => b.classList.remove('selected'));
  btn.classList.add('selected');
  updateTopicLabels(l);
  checkReady();
}

function updateTopicLabels(l) {
  const labels = {
    en: ['☀️ Daily Life','✈️ Travel','💼 Work','🍳 Food','💬 Free Talk'],
    de: ['☀️ Alltag','✈️ Reisen','💼 Arbeit','🍳 Essen','💬 Frei reden']
  };
  document.querySelectorAll('#topicRow .opt-btn').forEach((b,i) => { b.textContent = labels[l][i]; });
}

function selectTopic(t, btn) {
  topicKey = t;
  document.querySelectorAll('#topicRow .opt-btn').forEach(b => b.classList.remove('selected'));
  btn.classList.add('selected');
  checkReady();
}

function checkReady() {
  document.getElementById('startBtn').disabled = !(lang && topicKey);
}

async function startSession() {
  // Check mic permission first
  try {
    await navigator.mediaDevices.getUserMedia({ audio: true });
  } catch(e) {
    showErr('মাইক পারমিশন দিন: ব্রাউজারে 🔒 আইকন → Microphone → Allow');
    return;
  }

  document.getElementById('setupScreen').classList.remove('active');
  document.getElementById('voiceScreen').classList.add('active');

  const topicLabels = {
    en: {daily:'Daily Life',travel:'Travel',work:'Work',food:'Food',free:'Free Talk'},
    de: {daily:'Alltag',travel:'Reisen',work:'Arbeit',food:'Essen',free:'Frei reden'}
  };
  document.getElementById('sessionBadge').textContent =
    (lang==='en'?'🇺🇸 English':'🇩🇪 Deutsch') + ' · ' + topicLabels[lang][topicKey];

  setStatus('AI কথা বলছে...', 'শুনুন');
  setAvatar('speaking');
  setTranscript('');

  // Greet
  history = [];
  const initPrompt = lang==='en'
    ? `Hi! I want to practice speaking English. Topic: ${topicLabels.en[topicKey]}. Please greet me and start the conversation naturally!`
    : `Hallo! Ich möchte Deutsch üben. Thema: ${topicLabels.de[topicKey]}. Begrüße mich und starte das Gespräch!`;

  history.push({role:'user', content: initPrompt});
  const resp = await callClaude();
  if (!resp) return;
  history.push({role:'assistant', content: resp});
  const {reply, feedback} = parse(resp);
  setTranscript(reply);
  await speakAndThen(reply, feedback);
}

// ── CLAUDE ──
async function callClaude() {
  try {
    const res = await fetch('https://api.anthropic.com/v1/messages', {
      method:'POST',
      headers:{'Content-Type':'application/json'},
      body: JSON.stringify({
        model:'claude-sonnet-4-20250514',
        max_tokens:600,
        system: SYSTEM[lang](TOPICS[lang][topicKey]),
        messages: history
      })
    });
    const data = await res.json();
    return data.content?.[0]?.text || '';
  } catch(e) {
    showErr('সংযোগ সমস্যা। আবার চেষ্টা করুন।');
    setStatus('সংযোগ বিচ্ছিন্ন', '');
    setAvatar('idle');
    return null;
  }
}

function parse(text) {
  const parts = text.split('---FEEDBACK---');
  return { reply: parts[0].trim(), feedback: parts[1]?.trim() || null };
}

// ── SPEECH SYNTHESIS ──
function speakAndThen(text, feedback) {
  return new Promise((resolve) => {
    if (!window.speechSynthesis) {
      showErr('আপনার ব্রাউজার Text-to-Speech সাপোর্ট করে না।');
      resolve(); return;
    }
    window.speechSynthesis.cancel();
    const utter = new SpeechSynthesisUtterance(text);
    utter.lang = lang==='en' ? 'en-US' : 'de-DE';
    utter.rate = 0.88;
    utter.pitch = 1.05;
    utter.volume = 1;

    utter.onstart = () => {
      isSpeaking = true;
      setAvatar('speaking');
      setStatus('AI কথা বলছে...', 'মনোযোগ দিয়ে শুনুন');
      document.getElementById('micBtn').disabled = true;
      document.getElementById('micHint').textContent = 'AI কথা বলছে...';
      setRings(true);
    };

    utter.onend = () => {
      isSpeaking = false;
      setAvatar('idle');
      if (feedback) showFeedback(feedback);
      setStatus('আপনার পালা', 'মাইক বাটন চেপে কথা বলুন');
      document.getElementById('micBtn').disabled = false;
      document.getElementById('micHint').textContent = '🎤 বাটন চেপে কথা বলুন';
      setRings(false);
      resolve();
    };

    utter.onerror = () => {
      isSpeaking = false;
      setAvatar('idle');
      setStatus('আপনার পালা', '');
      document.getElementById('micBtn').disabled = false;
      setRings(false);
      resolve();
    };

    setTimeout(() => window.speechSynthesis.speak(utter), 150);
  });
}

// ── SPEECH RECOGNITION ──
function toggleMic() {
  if (isRecording) {
    recognition?.stop();
    return;
  }
  startListening();
}

function startListening() {
  hideFeedback();
  const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
  if (!SR) { showErr('Chrome বা Edge ব্রাউজার ব্যবহার করুন।'); return; }

  recognition = new SR();
  recognition.lang = lang==='en' ? 'en-US' : 'de-DE';
  recognition.interimResults = true;
  recognition.maxAlternatives = 1;

  let finalText = '';

  recognition.onstart = () => {
    isRecording = true;
    document.getElementById('micBtn').classList.add('recording');
    document.getElementById('micBtn').textContent = '⏹';
    document.getElementById('micPulse').classList.add('active');
    document.getElementById('micHint').textContent = 'কথা বলুন... শেষ হলে বাটন চাপুন';
    setStatus('আপনি কথা বলছেন...', 'বলা শেষ হলে বাটন চাপুন');
    setTranscript('...', true);
  };

  recognition.onresult = (e) => {
    let interim = '';
    finalText = '';
    for (let i = e.resultIndex; i < e.results.length; i++) {
      if (e.results[i].isFinal) finalText += e.results[i][0].transcript;
      else interim += e.results[i][0].transcript;
    }
    setTranscript((finalText || interim) + (interim ? '...' : ''), true);
  };

  recognition.onend = async () => {
    isRecording = false;
    document.getElementById('micBtn').classList.remove('recording');
    document.getElementById('micBtn').textContent = '🎤';
    document.getElementById('micPulse').classList.remove('active');
    document.getElementById('micBtn').disabled = true;
    document.getElementById('micHint').textContent = 'AI কথা বলছে...';

    if (!finalText.trim()) {
      setStatus('শুনতে পাইনি', 'আবার চেষ্টা করুন');
      document.getElementById('micBtn').disabled = false;
      document.getElementById('micHint').textContent = '🎤 বাটন চেপে কথা বলুন';
      return;
    }

    setStatus('AI ভাবছে...', '');
    setAvatar('thinking');
    setRings(false);

    history.push({role:'user', content: finalText.trim()});
    const resp = await callClaude();
    if (!resp) return;
    history.push({role:'assistant', content: resp});
    const {reply, feedback} = parse(resp);
    setTranscript(reply);
    await speakAndThen(reply, feedback);
  };

  recognition.onerror = (e) => {
    isRecording = false;
    document.getElementById('micBtn').classList.remove('recording');
    document.getElementById('micBtn').textContent = '🎤';
    document.getElementById('micPulse').classList.remove('active');
    document.getElementById('micBtn').disabled = false;
    if (e.error !== 'no-speech') showErr('মাইক এরর: ' + e.error);
    setStatus('আপনার পালা', '');
    document.getElementById('micHint').textContent = '🎤 বাটন চেপে কথা বলুন';
  };

  recognition.start();
}

// ── UI HELPERS ──
function setStatus(label, sub) {
  document.getElementById('statusLabel').textContent = label;
  document.getElementById('statusSub').textContent = sub;
}

function setTranscript(text, userSpeaking=false) {
  const el = document.getElementById('transcriptBox');
  el.textContent = text || (lang==='en' ? 'AI is speaking...' : 'KI spricht...');
  el.className = 'transcript-box' + (userSpeaking ? ' user-speaking' : '');
}

function setAvatar(state) {
  const av = document.getElementById('avatar');
  av.className = 'avatar' + (state==='speaking' ? ' speaking' : '');
  av.textContent = state==='speaking' ? '🗣️' : state==='thinking' ? '🤔' : '🤖';
}

function setRings(active) {
  ['ring1','ring2','ring3'].forEach(id => {
    const el = document.getElementById(id);
    if (active) el.classList.add('active'); else el.classList.remove('active');
  });
}

function showFeedback(text) {
  const el = document.getElementById('feedbackPanel');
  el.textContent = text;
  el.classList.add('show');
}
function hideFeedback() {
  document.getElementById('feedbackPanel').classList.remove('show');
}

function showErr(msg) {
  const el = document.getElementById('errToast');
  el.textContent = msg;
  el.classList.add('show');
  setTimeout(() => el.classList.remove('show'), 4000);
}

function endSession() {
  window.speechSynthesis?.cancel();
  recognition?.stop();
  history = [];
  lang = null; topicKey = null;
  document.querySelectorAll('.opt-btn').forEach(b => b.classList.remove('selected'));
  document.getElementById('startBtn').disabled = true;
  document.getElementById('voiceScreen').classList.remove('active');
  document.getElementById('setupScreen').classList.add('active');
  setAvatar('idle');
  setRings(false);
}
</script>
</body>
</html>
