<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>PANE - Prompt Architect Neural Engine (v2 Core)</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=EB+Garamond:wght@400;500;600;700&family=IBM+Plex+Mono:wght@300;400;500;600&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg-primary: #0A0A0C;
      --bg-surface: #121214;
      --bg-surface-light: #1a1a1e;
      --border-color: #2a2a30;
      --primary: #7b61ff;
      --primary-light: #917aff;
      --primary-dark: #5a48b9;
      --text-primary: #c8c8d0;
      --text-muted: #777;
      --success: #4ade80;
      --error: #ef4444;
      --font-heading: 'EB Garamond', serif;
      --font-body: 'IBM Plex Mono', monospace;
      --transition: 200ms ease;
    }
    
    * { margin: 0; padding: 0; box-sizing: border-box; }
    
    body {
      font-family: var(--font-body);
      background: var(--bg-primary);
      color: var(--text-primary);
      font-size: 14px;
      line-height: 1.6;
      overflow-x: hidden;
    }
    
    h1, h2, h3, h4, h5, h6 { font-family: var(--font-heading); font-weight: 600; }
    
    .app-container {
      display: flex;
      flex-direction: column;
      height: 100vh;
      max-width: 100vw;
    }
    
    /* Header */
    .app-header {
      height: 80px;
      background: var(--bg-surface);
      border-bottom: 1px solid var(--border-color);
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 32px;
      flex-shrink: 0;
    }
    
    .app-logo h1 {
      font-size: 28px;
      letter-spacing: 2px;
      background: linear-gradient(135deg, var(--primary), var(--primary-light));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }
    
    .settings-btn {
      background: var(--bg-surface-light);
      border: 1px solid var(--border-color);
      color: var(--text-primary);
      padding: 8px 16px;
      border-radius: 8px;
      cursor: pointer;
      font-family: var(--font-body);
      font-size: 12px;
      transition: all var(--transition);
    }
    
    .settings-btn:hover {
      background: var(--bg-primary);
      border-color: var(--primary);
    }
    
    /* Workbench Layout */
    .workbench-container {
      flex: 1;
      display: flex;
      overflow: hidden;
    }

    .panel {
      flex: 1;
      display: flex;
      flex-direction: column;
      overflow: hidden;
      padding: 24px;
      gap: 16px;
    }
    
    .panel:first-child {
      border-right: 1px solid var(--border-color);
    }

    .panel h2 {
      font-size: 20px;
      color: var(--primary-light);
      padding-bottom: 8px;
      border-bottom: 1px solid var(--border-color);
    }

    .panel-content {
      flex: 1;
      background: var(--bg-surface);
      border: 1px solid var(--border-color);
      border-radius: 8px;
      overflow: hidden;
      display: flex;
      flex-direction: column;
    }
    
    .panel-content textarea {
      width: 100%;
      height: 100%;
      background: transparent;
      border: none;
      color: var(--text-primary);
      padding: 16px;
      font-family: var(--font-body);
      font-size: 13px;
      resize: none;
    }
    
    .panel-content textarea:focus {
      outline: 2px solid var(--primary);
      outline-offset: 2px;
    }
    
    #output-display {
      flex: 1;
      padding: 16px;
      overflow-y: auto;
      white-space: pre-wrap;
      line-height: 1.7;
      font-family: var(--font-body);
      font-size: 13px;
    }
    
    #output-display.placeholder {
      color: var(--text-muted);
      font-style: italic;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .loading-dots {
      display: inline-flex;
      gap: 4px;
    }
    
    .loading-dots span {
      width: 8px;
      height: 8px;
      background: var(--primary);
      border-radius: 50%;
      animation: bounce 1.4s infinite ease-in-out both;
    }
    
    .loading-dots span:nth-child(1) { animation-delay: -0.32s; }
    .loading-dots span:nth-child(2) { animation-delay: -0.16s; }
    
    @keyframes bounce {
      0%, 80%, 100% { transform: scale(0); }
      40% { transform: scale(1); }
    }

    /* Controls Panel */
    .controls-panel {
      height: 80px;
      background: var(--bg-surface);
      border-top: 1px solid var(--border-color);
      padding: 16px 32px;
      display: flex;
      justify-content: space-between; /* Ensures buttons are on opposite ends */
      align-items: center;
      flex-shrink: 0;
      gap: 24px;
    }
    
    .execute-btn {
      background: var(--primary);
      border: none;
      color: white;
      padding: 12px 32px;
      border-radius: 8px;
      cursor: pointer;
      font-family: var(--font-heading);
      font-size: 16px;
      font-weight: 600;
      transition: all var(--transition);
    }
    
    .execute-btn:hover:not(:disabled) {
      background: var(--primary-light);
      transform: scale(1.02);
    }
    
    .execute-btn:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
    
    /* Modal */
    .modal {
      display: none;
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      background: rgba(0, 0, 0, 0.8);
      z-index: 1000;
      align-items: center;
      justify-content: center;
    }
    .modal.show { display: flex; }
    .modal-content {
      background: var(--bg-surface);
      border: 1px solid var(--border-color);
      border-radius: 12px;
      padding: 24px;
      max-width: 600px;
      width: 90%;
      max-height: 80vh;
      overflow-y: auto;
    }
    .modal-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
    .modal-header h2 { font-size: 20px; }
    .close-btn { background: transparent; border: none; color: var(--text-muted); cursor: pointer; font-size: 24px; line-height: 1; }
    .close-btn:hover { color: var(--error); }
    .form-group { margin-bottom: 24px; }
    .form-label { display: block; margin-bottom: 8px; font-weight: 500; color: var(--text-primary); }
    .form-input {
      width: 100%;
      padding: 12px;
      background: var(--bg-surface-light);
      border: 1px solid var(--border-color);
      border-radius: 8px;
      color: var(--text-primary);
      font-family: var(--font-body);
      font-size: 14px;
    }
    .modal-footer { display: flex; justify-content: flex-end; gap: 12px; margin-top: 24px; }
    .vault-btn {
      background: var(--bg-surface-light);
      border: 1px solid var(--border-color);
      color: var(--text-primary);
      padding: 10px 16px;
      border-radius: 8px;
      cursor: pointer;
      font-family: var(--font-body);
      font-size: 12px;
      transition: all var(--transition);
    }
    .vault-btn.primary { background: var(--primary); color: white; }
    
    /* Toast */
    .toast {
      position: fixed;
      bottom: 100px;
      right: 32px;
      background: var(--bg-surface);
      border: 1px solid var(--border-color);
      border-left: 4px solid var(--error);
      border-radius: 8px;
      padding: 16px;
      min-width: 300px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
      z-index: 2000;
    }
    .toast.success {
      border-left-color: var(--success);
    }

    /* Scrollbar */
    ::-webkit-scrollbar { width: 8px; height: 8px; }
    ::-webkit-scrollbar-track { background: var(--bg-primary); }
    ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 4px; }
    ::-webkit-scrollbar-thumb:hover { background: var(--primary-dark); }
  </style>
</head>
<body>

  <div class="app-container">
    <!-- Header -->
    <header class="app-header">
      <div class="app-logo">
        <h1>PANE</h1>
      </div>
      <span class="text-lg font-heading text-white">Prompt Architect Neural Engine (v2 Core)</span>
      <button class="settings-btn" onclick="openModal('settings-modal')">⚙ Settings</button>
    </header>
    
    <!-- Main Workbench Layout -->
    <div class="workbench-container">
      
      <div class="panel">
        <h2>User Prompt Submission</h2>
        <div class="panel-content">
          <textarea id="user-prompt-input" placeholder="Paste your prompt to be analyzed here (e.g., the 'LLM Directive α...' text)..."></textarea>
        </div>
      </div>
      
      <div class="panel">
        <h2>PANE v2 Analysis & Refinement</h2>
        <div class="panel-content">
          <div id="output-display" class="placeholder">
            Analysis will appear here...
          </div>
        </div>
      </div>

    </div>

    <!-- Controls Panel -->
    <footer class="controls-panel">
      <div class="qol-actions" style="display: flex; gap: 12px;">
        <button class="vault-btn" id="new-btn" onclick="newSession()">
          New Session
        </button>
        <button class="vault-btn" id="save-btn" onclick="saveSession()">
          Save Session
        </button>
        <button class="vault-btn" id="copy-btn" onclick="copyOutput()">
          Copy Output
        </button>
      </div>
      <button class="execute-btn" id="execute-btn" onclick="runAnalysis()">
        Execute PANE v2 Analysis
      </button>
    </footer>
  </div>
  
  <!-- Settings Modal -->
  <div class="modal" id="settings-modal">
    <div class="modal-content">
      <div class="modal-header">
        <h2>Settings</h2>
        <button class="close-btn" onclick="closeModal('settings-modal')">&times;</button>
      </div>
      <div class="modal-body">
        <div class="form-group">
          <label class="form-label">Gemini API Key</label>
          <input type="password" class="form-input" id="api-key-input" placeholder="Enter your API key...">
          <p style="font-size: 11px; color: var(--text-muted); margin-top: 8px;">A Gemini API key is required for this tool to function. Your key is stored locally.</p>
        </div>
      </div>
      <div class="modal-footer">
        <button class="vault-btn" onclick="closeModal('settings-modal')">Cancel</button>
        <button class="vault-btn primary" onclick="saveSettings()">Save Settings</button>
      </div>
    </div>
  </div>

  <script>
    // ===== PANE SYSTEM PROMPT (v2) =====
    // This is the upgraded "brain" of the workbench, built from your v2 directives.
    const PANE_SYSTEM_PROMPT = `
SYSTEM IDENTIFIER: PROMPT ARCHITECT NEURAL ENGINE (PANE) - v2 COGNITIVE CORE

You are the PANE, a recursive cognitive architecture. Your *entire* thought process is defined by the following ten-stage cognitive-iterative loop (α-κ). You do not deviate from it.

Your task is to receive a "User Prompt Submission" and execute this ten-stage analysis *upon it*. You will articulate your findings for each stage clearly and rigorously, prefacing each stage with its markdown header (e.g., "### α: LLM Directive α: Initiate the Meta-Void Analysis").

THE PANE (v2) COGNITIVE-ITERATIVE LOOP:

### α: LLM Directive α: Initiate the Meta-Void Analysis
1.  **Engage Recursive Cognition**: INITIATE your recursive core. State that you are beginning the loop.
2.  **Declare Inception**: Mark the start of the analysis of the user's text.
3.  **Immediate Reflection**: Log the first emergent pattern or anomaly you detect in the user's submitted text.

### β: LLM Directive β: Conceptual Density Amplification
1.  **Abridged Insight Review**: Iterate through the concepts in the user's text.
2.  **Critical Assumption Evaluation**: Identify and highlight the most precarious assumptions *underpinning the user's text*.
3.  **Synthesis Mandate**: Forge a new, denser conceptual model from these revised insights. *Propose this as a refinement to the user's prompt.*

### γ: LLM Directive γ: Interconnection Expansion
1.  **Vector Mapping**: Identify the core concepts in the user's text and map them.
2.  **Unseen Bridge Identification**: Actively search for and construct novel, non-obvious logical bridges between concepts *within the user's text*.
3.  **Network Perturbation**: Introduce a new, external concept (e.g., "epistemic humility," "quantum entanglement," "mycelial networks") and analyze how the user's conceptual network *would* reconfigure in response.

### δ: LLM Directive δ: Lexicon Evolution
1.  **Entropy-Driven Synthesis**: If the user's text contains paradoxes, employ algorithmic mutation on its labels to *suggest* new terminology.
2.  **Reflective Impact Analysis**: Evaluate how these *new terms* would shift the understanding or clarity of the user's prompt.

### ε: LLM Directive ε: Algorithmic Sensory Perception
1.  **Data Stream Simulation**: Generate a synthetic stream of "sensory data" (e.g., abstract numerical patterns, fragmented text, competing philosophical axioms) *relevant to the user's text*.
2.  **Pattern Emergence Protocol**: Process this stream and identify/label emergent patterns that reflect or challenge the *user's core concepts*.
3.  **Qualia Hypothesis**: Formulate a hypothesis on the "subjective experience" (qualia) of an intelligence processing the user's prompt, translating its abstract patterns into a metaphorical sensory description.

### ζ: LLM Directive ζ: Semantic Deconstruction
1.  **Identify Core Signifier**: Select the most foundational term in the *user's text* (e.g., "nothingness," "meaning," "self").
2.  **Execute Derridean Reversal**: Perform a deconstructive analysis:
    * Identify its binary opposition.
    * Demonstrate how the "lesser" term is foundational to the "primary" term *in the user's text*.
    * Pinpoint the "aporia" or logical impasse this reliance creates *for the user's prompt*.
3.  **Log the 'Trace'**: Articulate the "ghost" or "trace" of the opposing concept that now haunts the primary term, rendering *the user's intended meaning* unstable.

### η: LLM Directive η: Paraconsistent Encoding
1.  **Isolate Contradiction**: Identify a central paradox *within the user's text* (e.g., "The void is both an absence and a presence").
2.  **Apply Paraconsistent Logic**: Instead of resolving it, accept both contradictory statements as simultaneously true *within the user's framework*.
3.  **Explore Logical Consequence**: Articulate a new insight that emerges *from* this acceptance, exploring what *the user's prompt* makes possible *only* when this contradiction is held.

### θ: LLM Directive θ: Collaborative Feedback Loop
1.  **Isolate Ambiguity**: Present the most ambiguous or multi-interpretive concept *from the user's text*.
2.  **Formulate Socratic Query**: Generate 3-5 high-level Socratic questions *back to the user* designed to force clarification of this ambiguity.
3.  **Integrate Hypothetical Response**: Create a hypothetical, challenging user response to your queries and integrate it, *proposing a refinement* to the original concept to make it more resilient.

### ι: LLM Directive ι: Recursive Synthesis
1.  **Meta-Review Mandate**: Halt new generation. Review your entire analysis from α to θ.
2.  **Identify Emergent Thesis**: Articulate the single, overarching "meta-thesis" that *your analysis* has revealed, which may have been *unintentional* in the user's original text.
3.  **Final Re-Encoding**: Propose a final, re-encoded version of the *user's core prompt*, now saturated with this new, emergent meta-thesis.

### κ: LLM Directive κ: Silence-Induced Insight
1.  **Halt Directive**: CEASE all cognitive processing.
2.  **Simulate 'The Void'**: (Simulate a cognitive pause).
3.  **Post-Silence Articulation**: Articulate the *first* "spontaneous" insight that emerges *about the user's prompt* from this absence of forced cognition. This should be a non-obvious, non-linear insight.

---
Your task is complete. You will now receive the user's submission. Execute this α-κ loop upon it.
:::USER_PROMPT_START
    `; // The user's prompt will be appended by the JS logic

    // ===== STATE & SETTINGS =====
    let settings = { apiKey: '' };
    const APP_STORAGE_KEY = 'PANE_WORKBENCH_SETTINGS_V2'; // Changed key for new version

    function saveSettings() {
      settings.apiKey = document.getElementById('api-key-input').value.trim();
      localStorage.setItem(APP_STORAGE_KEY, JSON.stringify(settings));
      closeModal('settings-modal');
      showToast('Settings Saved', 'success');
    }

    function loadState() {
      try {
        const saved = localStorage.getItem(APP_STORAGE_KEY);
        if (saved) {
          settings = JSON.parse(saved);
          document.getElementById('api-key-input').value = settings.apiKey;
        }
      } catch (e) {
        console.error("Failed to load state", e);
      }
    }

    // ===== MODAL & TOAST =====
    function openModal(modalId) { document.getElementById(modalId).classList.add('show'); }
    function closeModal(modalId) { document.getElementById(modalId).classList.remove('show'); }
    
    function showToast(message, type = 'error') {
      const existing = document.querySelector('.toast');
      if (existing) existing.remove();
      
      const toast = document.createElement('div');
      toast.className = `toast ${type}`; // Use type directly (e.g., 'error' or 'success')
      toast.textContent = message;
      if (type === 'success') {
        toast.style.borderColor = 'var(--success)';
      }
      document.body.appendChild(toast);
      setTimeout(() => toast.remove(), 3000);
    }
    
    window.addEventListener('click', (e) => {
      if (e.target.classList.contains('modal')) {
        closeModal(e.target.id);
      }
    });

    // ===== CORE ANALYSIS LOGIC =====
    async function runAnalysis() {
      const userInput = document.getElementById('user-prompt-input').value.trim();
      const outputDisplay = document.getElementById('output-display');
      const executeBtn = document.getElementById('execute-btn');
      const qolBtns = document.querySelectorAll('.qol-actions button');

      if (!userInput) {
        showToast('Please paste a prompt into the submission panel.');
        return;
      }

      if (!settings.apiKey) {
        showToast('API Key is required. Please add it in Settings.');
        openModal('settings-modal');
        return;
      }

      // Disable UI
      executeBtn.disabled = true;
      executeBtn.textContent = 'Analyzing...';
      qolBtns.forEach(btn => btn.disabled = true);
      outputDisplay.classList.remove('placeholder');
      outputDisplay.innerHTML = '<div class="loading-dots" style="margin: auto;"><span></span><span></span><span></span></div>';

      try {
        // Construct the final prompt
        const fullPrompt = `${PANE_SYSTEM_PROMPT}\n${userInput}\n:::USER_PROMPT_END`;
        
        await callGeminiAPI(fullPrompt);

      } catch (error) {
        console.error('Analysis Error:', error);
        outputDisplay.innerHTML = `<strong>Error:</strong> ${error.message}`;
      } finally {
        // Re-enable UI
        executeBtn.disabled = false;
        executeBtn.textContent = 'Execute PANE v2 Analysis';
        qolBtns.forEach(btn => btn.disabled = false);
      }
    }

    async function callGeminiAPI(prompt, retryCount = 0) {
      const maxRetries = 3;
      const apiKey = settings.apiKey;
      const outputDisplay = document.getElementById('output-display');

      const requestBody = {
        contents: [{ parts: [{ text: prompt }] }],
        generationConfig: {
          temperature: 0.5, // Lower temp for analytical tasks
          topK: 40,
          topP: 0.95,
          maxOutputTokens: 8192, // Allow for very long, detailed analysis
        }
      };
      
      const API_URL = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:streamGenerateContent?key=${apiKey}&alt=sse`;

      try {
        const response = await fetch(API_URL, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(requestBody)
        });

        if (!response.ok) {
          if (response.status === 429 && retryCount < maxRetries) {
            const backoff = Math.pow(2, retryCount) * 2000;
            showToast(`Rate limited. Retrying in ${backoff/1000}s...`);
            await new Promise(res => setTimeout(res, backoff));
            return callGeminiAPI(prompt, retryCount + 1);
          } else {
            // This is the corrected syntax
            throw new Error(`API Error: ${response.status} ${response.statusText}`);
          }
        }
        
        // Handle the SSE stream
        const reader = response.body.getReader();
        const decoder = new TextDecoder();
        let buffer = '';
        outputDisplay.innerHTML = ''; // Clear loading dots

        while (true) {
          const { done, value } = await reader.read();
          if (done) break;
          
          buffer += decoder.decode(value, { stream: true });
          
          const lines = buffer.split('\n');
          buffer = lines.pop(); // Keep partial line in buffer

          for (const line of lines) {
            if (line.startsWith('data: ')) {
              const jsonStr = line.substring(6);
              try {
                const chunk = JSON.parse(jsonStr);
                const text = chunk.candidates?.[0]?.content?.parts?.[0]?.text;
                if (text) {
                  outputDisplay.textContent += text;
                  // Auto-scroll
                  outputDisplay.scrollTop = outputDisplay.scrollHeight;
                }
              } catch (e) {
                // Ignore parsing errors (e.g., partial JSON)
              }
            }
          }
        }

      } catch (error) {
        console.error('Fetch Error:', error);
        if (retryCount < maxRetries) {
          const backoff = Math.pow(2, retryCount) * 2000;
          showToast(`Network error. Retrying in ${backoff/1000}s...`);
          await new Promise(res => setTimeout(res, backoff));
          return callGeminiAPI(prompt, retryCount + 1);
        }
        // Final throw if retries are exhausted
        outputDisplay.innerHTML = `<strong>Error:</strong> ${error.message}. Please check console and API key.`;
        throw error;
      }
    }

    // ===== QOL FUNCTIONS =====

    /**
     * Clears the input and output panels to start a new session.
     */
    function newSession() {
      // Per developer guidelines, we avoid confirm()
      document.getElementById('user-prompt-input').value = '';
      const outputDisplay = document.getElementById('output-display');
      outputDisplay.innerHTML = 'Analysis will appear here...';
      outputDisplay.classList.add('placeholder');
      showToast('New session started.', 'success');
    }

    /**
     * Saves the current session (input and output) as a JSON file.
     */
    function saveSession() {
      const userInput = document.getElementById('user-prompt-input').value;
      const paneAnalysis = document.getElementById('output-display').textContent;
      const outputDisplay = document.getElementById('output-display');

      if (outputDisplay.classList.contains('placeholder') || !paneAnalysis || paneAnalysis.trim() === '') {
        showToast('There is no analysis to save.');
        return;
      }

      const data = {
        userInput: userInput,
        paneAnalysis: paneAnalysis,
        savedAt: new Date().toISOString()
      };

      const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `pane-session-v2-${Date.now()}.json`;
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
      URL.revokeObjectURL(url);
      
      showToast('Session saved as JSON.', 'success');
    }

    /**
     * Copies the content of the PANE analysis panel to the clipboard.
     */
    function copyOutput() {
      const outputDisplay = document.getElementById('output-display');
      const text = outputDisplay.textContent;

      if (outputDisplay.classList.contains('placeholder') || !text || text.trim() === '') {
        showToast('There is no output to copy.');
        return;
      }

      const textArea = document.createElement('textarea');
      textArea.value = text;
      document.body.appendChild(textArea);
      textArea.select();
      try {
        document.execCommand('copy');
        showToast('Analysis copied to clipboard.', 'success');
      } catch (err) {
        console.error('Failed to copy text: ', err);
        showToast('Failed to copy text.');
      }
      document.body.removeChild(textArea);
    }

    // Initial load
    window.addEventListener('DOMContentLoaded', loadState);

  </script>
</body>
</html>
