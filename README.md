# welcome-survey
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Welcome Survey</title>
  <style>
    /* ============================================================
       BASIC RESET + PAGE
       ============================================================ */
    * { box-sizing: border-box; }
    body {
      margin: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      background: #f7f7f5;
      color: #1a1a1a;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 2rem 1rem;
      line-height: 1.5;
    }

    /* ============================================================
       SURVEY CONTAINER
       ============================================================ */
    .survey-root {
      width: 100%;
      max-width: 560px;
      background: #ffffff;
      border-radius: 12px;
      border: 1px solid rgba(0, 0, 0, 0.08);
      padding: 2rem;
      box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
    }

    /* Progress bar */
    .progress-track {
      height: 4px;
      background: #eeece7;
      border-radius: 2px;
      overflow: hidden;
      margin-bottom: 1.5rem;
    }
    .progress-fill {
      height: 100%;
      background: #1a1a1a;
      transition: width 0.3s ease;
      width: 0%;
    }

    /* Question text */
    .step-label {
      font-size: 13px;
      color: #6b6b66;
      margin-bottom: 8px;
    }
    .question {
      font-size: 20px;
      font-weight: 500;
      margin: 0 0 1.25rem;
      line-height: 1.4;
    }

    /* Option buttons */
    .option-btn {
      display: block;
      width: 100%;
      text-align: left;
      padding: 14px 16px;
      margin-bottom: 8px;
      background: #ffffff;
      border: 1px solid rgba(0, 0, 0, 0.12);
      border-radius: 8px;
      font-size: 15px;
      cursor: pointer;
      transition: all 0.15s ease;
      font-family: inherit;
      color: #1a1a1a;
    }
    .option-btn:hover {
      border-color: rgba(0, 0, 0, 0.3);
      background: #fafaf8;
    }
    .option-btn.selected {
      border-color: #1a1a1a;
      border-width: 2px;
      padding: 13px 15px;
    }

    /* Navigation buttons */
    .nav-row {
      display: flex;
      justify-content: space-between;
      gap: 8px;
      margin-top: 1.5rem;
    }
    .nav-btn {
      padding: 10px 24px;
      font-size: 14px;
      font-weight: 500;
      border-radius: 8px;
      cursor: pointer;
      font-family: inherit;
      transition: all 0.15s ease;
    }
    .nav-btn.primary {
      background: #1a1a1a;
      color: #ffffff;
      border: none;
    }
    .nav-btn.primary:hover:not(:disabled) {
      background: #000;
    }
    .nav-btn.primary:disabled {
      opacity: 0.3;
      cursor: not-allowed;
    }
    .nav-btn.secondary {
      background: transparent;
      border: 1px solid rgba(0, 0, 0, 0.2);
      color: #1a1a1a;
    }
    .nav-btn.secondary:hover {
      background: #fafaf8;
    }

    /* Recommendation screen */
    .recommendation {
      background: #f4f1ea;
      border-radius: 12px;
      padding: 1.5rem;
      margin-top: 1rem;
    }
    .rec-label {
      font-size: 11px;
      color: #6b6b66;
      text-transform: uppercase;
      letter-spacing: 0.8px;
      margin-bottom: 6px;
      font-weight: 500;
    }
    .rec-title {
      font-size: 22px;
      font-weight: 500;
      margin: 0 0 10px;
    }
    .rec-body {
      font-size: 15px;
      line-height: 1.6;
      color: #4a4a45;
      margin: 0;
    }

    /* Summary card */
    .summary-card {
      background: #ffffff;
      border: 1px solid rgba(0, 0, 0, 0.08);
      border-radius: 8px;
      padding: 12px 16px;
      margin-top: 1rem;
      font-size: 13px;
    }
    .summary-row {
      display: flex;
      justify-content: space-between;
      padding: 6px 0;
    }
    .summary-row + .summary-row {
      border-top: 1px solid rgba(0, 0, 0, 0.05);
    }
    .summary-row .k {
      color: #6b6b66;
    }

    /* Status messages */
    .status {
      font-size: 13px;
      color: #6b6b66;
      margin-top: 14px;
      text-align: center;
    }
    .status.success {
      color: #0f6e56;
    }
    .status.error {
      color: #a32d2d;
    }

    /* Restart button */
    .restart-btn {
      display: block;
      margin: 1.5rem auto 0;
      padding: 8px 20px;
      font-size: 13px;
      background: transparent;
      border: 1px solid rgba(0, 0, 0, 0.15);
      border-radius: 8px;
      cursor: pointer;
      font-family: inherit;
      color: #4a4a45;
    }
    .restart-btn:hover {
      background: #fafaf8;
    }
  </style>
</head>
<body>

  <div class="survey-root" id="app">
    <div class="progress-track"><div class="progress-fill" id="progress"></div></div>
    <div class="step-label" id="step-label">Step 1</div>
    <h2 class="question" id="question">Loading...</h2>
    <div id="options"></div>
    <div class="nav-row">
      <button class="nav-btn secondary" id="back-btn" style="visibility: hidden;">← Back</button>
      <button class="nav-btn primary" id="next-btn" disabled>Continue</button>
    </div>
    <div id="status"></div>
  </div>

  <script>
  (function() {
    // ============================================================
    // SURVEY CONFIGURATION
    // Edit this object to change questions, options, and branching.
    // Each option has a `next` field pointing to the next question's ID,
    // or `null` if it's the final question on that path.
    // ============================================================
    const survey = {
      start: 'purpose',
      questions: {
        purpose: {
          label: 'What brings you here?',
          question: 'What\'s your main purpose for joining?',
          options: [
            { value: 'learn',   label: 'Learn new skills',        next: 'learn_topic' },
            { value: 'network', label: 'Network with others',     next: 'network_role' },
            { value: 'build',   label: 'Build a project',         next: 'build_stage' },
            { value: 'hire',    label: 'Hire or recruit talent',  next: 'hire_urgency' },
          ],
        },

        // ---- LEARN path ----
        learn_topic: {
          label: 'Learning path',
          question: 'What area interests you most?',
          options: [
            { value: 'tech',     label: 'Technology & engineering', next: 'experience' },
            { value: 'design',   label: 'Design & creative',        next: 'experience' },
            { value: 'business', label: 'Business & strategy',      next: 'experience' },
          ],
        },
        experience: {
          label: 'Experience level',
          question: 'How would you describe your current level?',
          options: [
            { value: 'beginner',     label: 'Just starting out',    next: null },
            { value: 'intermediate', label: 'Some experience',      next: null },
            { value: 'advanced',     label: 'Experienced',          next: null },
          ],
        },

        // ---- NETWORK path ----
        network_role: {
          label: 'Your role',
          question: 'Which best describes you?',
          options: [
            { value: 'founder',   label: 'Founder or executive',  next: 'network_goal' },
            { value: 'ic',        label: 'Individual contributor', next: 'network_goal' },
            { value: 'investor',  label: 'Investor',              next: 'network_goal' },
          ],
        },
        network_goal: {
          label: 'Networking goal',
          question: 'What are you hoping to find?',
          options: [
            { value: 'mentors', label: 'Mentors and advisors', next: null },
            { value: 'peers',   label: 'Peers in my field',    next: null },
            { value: 'leads',   label: 'Business leads',       next: null },
          ],
        },

        // ---- BUILD path ----
        build_stage: {
          label: 'Project stage',
          question: 'Where is your project today?',
          options: [
            { value: 'idea',     label: 'Just an idea',          next: 'build_help' },
            { value: 'building', label: 'Actively building',     next: 'build_help' },
            { value: 'launched', label: 'Already launched',      next: 'build_help' },
          ],
        },
        build_help: {
          label: 'What you need',
          question: 'What would help you most right now?',
          options: [
            { value: 'cofounder', label: 'Finding a co-founder',    next: null },
            { value: 'feedback',  label: 'Feedback on my work',     next: null },
            { value: 'resources', label: 'Tools and resources',     next: null },
          ],
        },

        // ---- HIRE path ----
        hire_urgency: {
          label: 'Hiring timeline',
          question: 'How soon do you need to hire?',
          options: [
            { value: 'immediate', label: 'Immediately (< 1 month)', next: 'hire_role' },
            { value: 'soon',      label: 'Soon (1-3 months)',       next: 'hire_role' },
            { value: 'planning',  label: 'Planning ahead',          next: 'hire_role' },
          ],
        },
        hire_role: {
          label: 'Role type',
          question: 'What kind of role are you filling?',
          options: [
            { value: 'eng',    label: 'Engineering',       next: null },
            { value: 'design', label: 'Design',            next: null },
            { value: 'gtm',    label: 'Sales / marketing', next: null },
            { value: 'ops',    label: 'Operations',        next: null },
          ],
        },
      },

      // ============================================================
      // RECOMMENDATION ENGINE
      // Receives the full answers object and returns { title, body }.
      // Customize this to generate smarter recommendations based on
      // combinations of answers.
      // ============================================================
      getRecommendation: function(answers) {
        const recs = {
          learn: {
            title: 'Start with the Learning Hub',
            body: 'We\'ll match you with curated courses and a study buddy based on your level and topic. Welcome aboard!',
          },
          network: {
            title: 'Join our Community Circles',
            body: 'Weekly small-group sessions tailored to your role and goals. Your first intro goes out tomorrow.',
          },
          build: {
            title: 'Unlock Builder Mode',
            body: 'Access to project showcases, feedback exchanges, and our founder directory. Perfect for where you are.',
          },
          hire: {
            title: 'Employer Dashboard access',
            body: 'Post roles, browse candidates, and get matched with vetted talent. We\'ll prioritize based on your timeline.',
          },
        };
        return recs[answers.purpose] || {
          title: 'Welcome!',
          body: 'We\'ll personalize your experience.',
        };
      },
    };

    // ============================================================
    // BACKEND CONFIGURATION
    // Replace ENDPOINT with your actual API URL.
    // Set DEMO_MODE to false when you have a real endpoint ready.
    // ============================================================
    const ENDPOINT = 'https://your-api.example.com/survey';
    const DEMO_MODE = true;

    // ============================================================
    // STATE
    // ============================================================
    const state = {
      currentId: survey.start,
      history: [],     // stack of visited question IDs (for Back button)
      answers: {},     // { questionId: selectedValue }
      selected: null,  // selection on current screen (pre-submit)
      done: false,
    };

    const $ = id => document.getElementById(id);

    // Best-effort progress estimate: walks forward from current state
    // following the user's selections where available, or the first
    // option otherwise. Not exact (branches differ in length), but good
    // enough for a progress bar.
    function totalSteps() {
      let count = state.history.length + 1;
      let id = state.currentId;
      while (id) {
        const q = survey.questions[id];
        if (!q) break;
        const picked = state.answers[id];
        const opt = q.options.find(o => o.value === picked) || q.options[0];
        id = opt.next;
        if (id) count++;
      }
      return count;
    }

    function render() {
      if (state.done) { renderDone(); return; }

      const q = survey.questions[state.currentId];
      $('step-label').textContent = q.label;
      $('question').textContent = q.question;

      const total = totalSteps();
      const current = state.history.length + 1;
      $('progress').style.width = Math.round((current / total) * 100) + '%';

      const container = $('options');
      container.innerHTML = '';
      q.options.forEach(opt => {
        const btn = document.createElement('button');
        btn.className = 'option-btn' + (state.selected === opt.value ? ' selected' : '');
        btn.textContent = opt.label;
        btn.onclick = () => { state.selected = opt.value; render(); };
        container.appendChild(btn);
      });

      $('back-btn').style.visibility = state.history.length ? 'visible' : 'hidden';
      $('next-btn').disabled = state.selected === null;
      const selectedOpt = q.options.find(o => o.value === state.selected);
      const isLast = selectedOpt && !selectedOpt.next;
      $('next-btn').textContent = isLast ? 'Submit' : 'Continue';
    }

    function renderDone() {
      const rec = survey.getRecommendation(state.answers);
      const rows = Object.entries(state.answers).map(([qid, val]) => {
        const q = survey.questions[qid];
        const opt = q.options.find(o => o.value === val);
        return `<div class="summary-row"><span class="k">${q.label}</span><span>${opt.label}</span></div>`;
      }).join('');

      $('app').innerHTML = `
        <div class="progress-track"><div class="progress-fill" style="width: 100%;"></div></div>
        <div class="step-label">Your recommendation</div>
        <div class="recommendation">
          <div class="rec-label">Recommended for you</div>
          <h2 class="rec-title">${rec.title}</h2>
          <p class="rec-body">${rec.body}</p>
        </div>
        <div class="summary-card">${rows}</div>
        <div id="status" class="status">Sending your responses...</div>
        <button class="restart-btn" id="restart-btn">Start over</button>
      `;
      document.getElementById('restart-btn').onclick = restart;
      submitToBackend();
    }

    function submitToBackend() {
      const payload = {
        answers: state.answers,
        recommendation: survey.getRecommendation(state.answers).title,
        completed_at: new Date().toISOString(),
      };

      if (DEMO_MODE) {
        // Simulate a successful save for demo purposes
        console.log('Survey payload (demo mode):', payload);
        setTimeout(() => {
          const el = document.getElementById('status');
          if (el) { el.textContent = '✓ Responses saved (demo mode)'; el.className = 'status success'; }
        }, 800);
        return;
      }

      fetch(ENDPOINT, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      })
        .then(r => {
          if (!r.ok) throw new Error('Network response was not ok');
          return r.json();
        })
        .then(() => {
          const el = document.getElementById('status');
          if (el) { el.textContent = '✓ Responses saved'; el.className = 'status success'; }
        })
        .catch(err => {
          console.error('Survey submission failed:', err);
          const el = document.getElementById('status');
          if (el) { el.textContent = 'Could not save. Please try again.'; el.className = 'status error'; }
        });
    }

    function goNext() {
      if (state.selected === null) return;
      const q = survey.questions[state.currentId];
      state.answers[state.currentId] = state.selected;
      const opt = q.options.find(o => o.value === state.selected);

      if (!opt.next) { state.done = true; render(); return; }

      state.history.push(state.currentId);
      state.currentId = opt.next;
      state.selected = null;
      render();
    }

    function goBack() {
      if (!state.history.length) return;
      const prevId = state.history.pop();
      state.currentId = prevId;
      state.selected = state.answers[prevId] || null;
      delete state.answers[prevId];
      render();
    }

    function restart() {
      state.currentId = survey.start;
      state.history = [];
      state.answers = {};
      state.selected = null;
      state.done = false;

      // Rebuild the initial DOM (we nuked it in renderDone)
      $('app').innerHTML = `
        <div class="progress-track"><div class="progress-fill" id="progress"></div></div>
        <div class="step-label" id="step-label">Step 1</div>
        <h2 class="question" id="question">Loading...</h2>
        <div id="options"></div>
        <div class="nav-row">
          <button class="nav-btn secondary" id="back-btn" style="visibility: hidden;">← Back</button>
          <button class="nav-btn primary" id="next-btn" disabled>Continue</button>
        </div>
        <div id="status"></div>
      `;
      $('next-btn').onclick = goNext;
      $('back-btn').onclick = goBack;
      render();
    }

    // ============================================================
    // INIT
    // ============================================================
    $('next-btn').onclick = goNext;
    $('back-btn').onclick = goBack;
    render();
  })();
  </script>

</body>
</html>
