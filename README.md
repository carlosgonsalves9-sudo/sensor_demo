# sensor_demo

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>LearnBeat Drum Machine</title>

  <style>
    /* =========================================================
       CSS = HOW THE WEBSITE LOOKS
       ========================================================= */

    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, Helvetica, sans-serif;
      background: #111318;
      color: #f5f7fb;
      min-height: 100vh;
    }

    .app {
      width: min(1100px, 94%);
      margin: 0 auto;
      padding: 32px 0 50px;
    }

    header {
      margin-bottom: 24px;
    }

    h1 {
      margin: 0 0 8px;
      font-size: clamp(2rem, 5vw, 3.5rem);
    }

    header p {
      margin: 0;
      color: #aeb7c7;
      line-height: 1.6;
    }

    .panel {
      background: #1a1e26;
      border: 1px solid #2b3240;
      border-radius: 16px;
      padding: 20px;
      margin-bottom: 20px;
    }

    .controls {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      align-items: center;
    }

    button,
    input {
      font: inherit;
    }

    button {
      border: 0;
      border-radius: 10px;
      padding: 11px 16px;
      cursor: pointer;
      font-weight: 700;
    }

    button:focus-visible,
    input:focus-visible {
      outline: 3px solid #ffffff;
      outline-offset: 3px;
    }

    #playButton {
      background: #63e6be;
      color: #07130f;
    }

    #clearButton {
      background: #343b49;
      color: white;
    }

    .tempo-wrap {
      display: flex;
      align-items: center;
      gap: 10px;
      margin-left: auto;
    }

    #tempo {
      width: 160px;
    }

    .sequencer {
      overflow-x: auto;
      padding-bottom: 6px;
    }

    .step-labels,
    .track {
      display: grid;
      grid-template-columns: 110px repeat(16, minmax(34px, 1fr));
      gap: 6px;
      min-width: 820px;
      align-items: center;
      margin-bottom: 7px;
    }

    .step-labels {
      color: #7f899b;
      font-size: 0.75rem;
      text-align: center;
    }

    .track-name {
      font-weight: 800;
      padding-right: 10px;
    }

    .step {
      min-height: 42px;
      padding: 0;
      background: #2a303b;
      border: 1px solid #394252;
      border-radius: 8px;
      transition: transform 0.08s ease, background 0.08s ease;
    }

    .step:nth-of-type(4n + 2) {
      border-left-color: #667085;
    }

    .step.active {
      background: #7c9cff;
      border-color: #9eb4ff;
    }

    .step.playing {
      outline: 3px solid #ffffff;
      outline-offset: 1px;
    }

    .step:active {
      transform: scale(0.94);
    }

    .pads {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 12px;
    }

    .pad {
      min-height: 90px;
      background: #252b35;
      color: white;
      border: 1px solid #3c4555;
      font-size: 1rem;
    }

    .pad strong {
      display: block;
      font-size: 1.15rem;
      margin-bottom: 5px;
    }

    .pad span {
      color: #aeb7c7;
      font-weight: 400;
    }

    .pad.hit {
      background: #7c9cff;
      transform: scale(0.97);
    }

    .lesson-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 14px;
    }

    .lesson {
      background: #202630;
      border-radius: 12px;
      padding: 16px;
    }

    .lesson h3 {
      margin-top: 0;
      margin-bottom: 8px;
    }

    .lesson p,
    .tip {
      color: #b9c2d0;
      line-height: 1.55;
    }

    code {
      background: #101318;
      color: #c6d4ff;
      padding: 2px 5px;
      border-radius: 5px;
    }

    .challenge {
      border-left: 4px solid #63e6be;
      padding-left: 14px;
      margin-top: 18px;
    }

    .status {
      color: #aeb7c7;
      min-height: 22px;
      margin-top: 12px;
    }

    @media (max-width: 760px) {
      .lesson-grid,
      .pads {
        grid-template-columns: 1fr 1fr;
      }

      .tempo-wrap {
        width: 100%;
        margin-left: 0;
      }
    }

    @media (max-width: 480px) {
      .lesson-grid,
      .pads {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>

<body>
  <!-- =========================================================
       HTML = WHAT IS ON THE WEBSITE
       ========================================================= -->

  <main class="app">
    <header>
      <h1>LearnBeat</h1>
      <p>
        Build a beat, then look under the hood to see how HTML, CSS,
        and JavaScript work together.
      </p>
    </header>

    <section class="panel">
      <div class="controls">
        <button id="playButton" type="button">▶ Play</button>
        <button id="clearButton" type="button">Clear Pattern</button>

        <label class="tempo-wrap">
          Tempo:
          <input id="tempo" type="range" min="60" max="180" value="100" />
          <strong><span id="tempoValue">100</span> BPM</strong>
        </label>
      </div>

      <p id="status" class="status" aria-live="polite">
        Click squares to build your beat.
      </p>
    </section>

    <section class="panel">
      <h2>16-Step Sequencer</h2>
      <p class="tip">
        Each row is a drum. Each square is one moment in the loop.
        Try kick drums on steps 1 and 9, then snares on 5 and 13.
      </p>

      <div class="sequencer" id="sequencer">
        <div class="step-labels" aria-hidden="true">
          <span></span>
          <span>1</span><span>2</span><span>3</span><span>4</span>
          <span>5</span><span>6</span><span>7</span><span>8</span>
          <span>9</span><span>10</span><span>11</span><span>12</span>
          <span>13</span><span>14</span><span>15</span><span>16</span>
        </div>
      </div>
    </section>

    <section class="panel">
      <h2>Drum Pads</h2>
      <p class="tip">
        Click the pads or use your keyboard: A = Kick, S = Snare,
        D = Hi-Hat, F = Clap.
      </p>

      <div class="pads">
        <button class="pad" type="button" data-sound="kick">
          <strong>Kick</strong>
          <span>A key</span>
        </button>

        <button class="pad" type="button" data-sound="snare">
          <strong>Snare</strong>
          <span>S key</span>
        </button>

        <button class="pad" type="button" data-sound="hat">
          <strong>Hi-Hat</strong>
          <span>D key</span>
        </button>

        <button class="pad" type="button" data-sound="clap">
          <strong>Clap</strong>
          <span>F key</span>
        </button>
      </div>
    </section>

    <section class="panel">
      <h2>What You’re Learning</h2>

      <div class="lesson-grid">
        <article class="lesson">
          <h3>1. HTML</h3>
          <p>
            HTML creates the objects on the page. For example,
            <code>&lt;button&gt;</code> creates a button and
            <code>&lt;section&gt;</code> groups related content.
          </p>
        </article>

        <article class="lesson">
          <h3>2. CSS</h3>
          <p>
            CSS styles those objects. The class
            <code>.step.active</code> changes a sequencer square when
            you turn a beat on.
          </p>
        </article>

        <article class="lesson">
          <h3>3. JavaScript</h3>
          <p>
            JavaScript gives the page behavior. It listens for clicks,
            remembers your pattern, moves through the 16 steps, and
            generates drum sounds.
          </p>
        </article>
      </div>

      <div class="challenge">
        <strong>Try changing the code:</strong>
        <p class="tip">
          Search for <code>value="100"</code> and change it to
          <code>value="130"</code>. Reload the page. You just changed
          the drum machine's default tempo using HTML.
        </p>
      </div>
    </section>
  </main>

  <script>
    /* =========================================================
       JAVASCRIPT = WHAT THE WEBSITE DOES
       ========================================================= */

    // Our drum names.
    const sounds = ["kick", "snare", "hat", "clap"];

    // This object stores whether each of the 16 steps is ON or OFF.
    const pattern = {
      kick:  Array(16).fill(false),
      snare: Array(16).fill(false),
      hat:   Array(16).fill(false),
      clap:  Array(16).fill(false)
    };

    // Get HTML elements so JavaScript can control them.
    const sequencer = document.getElementById("sequencer");
    const playButton = document.getElementById("playButton");
    const clearButton = document.getElementById("clearButton");
    const tempoSlider = document.getElementById("tempo");
    const tempoValue = document.getElementById("tempoValue");
    const status = document.getElementById("status");

    let audioContext = null;
    let isPlaying = false;
    let currentStep = 0;
    let timer = null;

    // Create the sequencer rows using JavaScript.
    sounds.forEach((sound) => {
      const row = document.createElement("div");
      row.className = "track";

      const label = document.createElement("div");
      label.className = "track-name";
      label.textContent = sound === "hat" ? "Hi-Hat" : capitalize(sound);
      row.appendChild(label);

      for (let step = 0; step < 16; step++) {
        const button = document.createElement("button");

        button.type = "button";
        button.className = "step";
        button.dataset.sound = sound;
        button.dataset.step = step;
        button.setAttribute(
          "aria-label",
          `${capitalize(sound)} step ${step + 1}`
        );

        button.addEventListener("click", () => {
          pattern[sound][step] = !pattern[sound][step];
          button.classList.toggle("active", pattern[sound][step]);

          status.textContent =
            `${capitalize(sound)} step ${step + 1} ` +
            (pattern[sound][step] ? "turned on." : "turned off.");
        });

        row.appendChild(button);
      }

      sequencer.appendChild(row);
    });

    // Start the Web Audio system only after the user interacts.
    function getAudioContext() {
      if (!audioContext) {
        audioContext = new (window.AudioContext || window.webkitAudioContext)();
      }

      if (audioContext.state === "suspended") {
        audioContext.resume();
      }

      return audioContext;
    }

    // One function chooses which drum sound to make.
    function playSound(sound) {
      const ctx = getAudioContext();

      if (sound === "kick") playKick(ctx);
      if (sound === "snare") playSnare(ctx);
      if (sound === "hat") playHat(ctx);
      if (sound === "clap") playClap(ctx);
    }

    // KICK: a low oscillator quickly drops in pitch.
    function playKick(ctx) {
      const oscillator = ctx.createOscillator();
      const gain = ctx.createGain();

      oscillator.type = "sine";
      oscillator.frequency.setValueAtTime(150, ctx.currentTime);
      oscillator.frequency.exponentialRampToValueAtTime(
        45,
        ctx.currentTime + 0.18
      );

      gain.gain.setValueAtTime(1, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(
        0.001,
        ctx.currentTime + 0.22
      );

      oscillator.connect(gain);
      gain.connect(ctx.destination);

      oscillator.start();
      oscillator.stop(ctx.currentTime + 0.23);
    }

    // SNARE: filtered noise gives us a noisy drum hit.
    function playSnare(ctx) {
      const buffer = makeNoiseBuffer(ctx, 0.18);
      const source = ctx.createBufferSource();
      const filter = ctx.createBiquadFilter();
      const gain = ctx.createGain();

      source.buffer = buffer;
      filter.type = "highpass";
      filter.frequency.value = 900;

      gain.gain.setValueAtTime(0.65, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(
        0.001,
        ctx.currentTime + 0.18
      );

      source.connect(filter);
      filter.connect(gain);
      gain.connect(ctx.destination);
      source.start();
    }

    // HI-HAT: shorter, brighter filtered noise.
    function playHat(ctx) {
      const buffer = makeNoiseBuffer(ctx, 0.07);
      const source = ctx.createBufferSource();
      const filter = ctx.createBiquadFilter();
      const gain = ctx.createGain();

      source.buffer = buffer;
      filter.type = "highpass";
      filter.frequency.value = 6000;

      gain.gain.setValueAtTime(0.32, ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(
        0.001,
        ctx.currentTime + 0.07
      );

      source.connect(filter);
      filter.connect(gain);
      gain.connect(ctx.destination);
      source.start();
    }

    // CLAP: several tiny noise bursts.
    function playClap(ctx) {
      [0, 0.025, 0.05].forEach((delay) => {
        const buffer = makeNoiseBuffer(ctx, 0.09);
        const source = ctx.createBufferSource();
        const filter = ctx.createBiquadFilter();
        const gain = ctx.createGain();

        source.buffer = buffer;
        filter.type = "bandpass";
        filter.frequency.value = 1400;

        const start = ctx.currentTime + delay;
        gain.gain.setValueAtTime(0.4, start);
        gain.gain.exponentialRampToValueAtTime(0.001, start + 0.08);

        source.connect(filter);
        filter.connect(gain);
        gain.connect(ctx.destination);
        source.start(start);
      });
    }

    // Generate random noise in memory.
    function makeNoiseBuffer(ctx, seconds) {
      const frameCount = Math.floor(ctx.sampleRate * seconds);
      const buffer = ctx.createBuffer(1, frameCount, ctx.sampleRate);
      const data = buffer.getChannelData(0);

      for (let i = 0; i < frameCount; i++) {
        data[i] = Math.random() * 2 - 1;
      }

      return buffer;
    }

    // Move the sequencer forward one square.
    function tick() {
      document.querySelectorAll(".step.playing").forEach((step) => {
        step.classList.remove("playing");
      });

      sounds.forEach((sound) => {
        const stepButton = document.querySelector(
          `.step[data-sound="${sound}"][data-step="${currentStep}"]`
        );

        stepButton.classList.add("playing");

        if (pattern[sound][currentStep]) {
          playSound(sound);
        }
      });

      currentStep = (currentStep + 1) % 16;
    }

    // Convert BPM into the time between 16th notes.
    function getStepTime() {
      const bpm = Number(tempoSlider.value);
      return (60 / bpm / 4) * 1000;
    }

    function startSequencer() {
      if (isPlaying) return;

      getAudioContext();
      isPlaying = true;
      currentStep = 0;

      playButton.textContent = "■ Stop";
      status.textContent = "Sequencer playing.";

      tick();
      timer = setInterval(tick, getStepTime());
    }

    function stopSequencer() {
      isPlaying = false;
      clearInterval(timer);
      timer = null;

      playButton.textContent = "▶ Play";
      status.textContent = "Sequencer stopped.";

      document.querySelectorAll(".step.playing").forEach((step) => {
        step.classList.remove("playing");
      });
    }

    playButton.addEventListener("click", () => {
      if (isPlaying) {
        stopSequencer();
      } else {
        startSequencer();
      }
    });

    clearButton.addEventListener("click", () => {
      sounds.forEach((sound) => {
        pattern[sound].fill(false);
      });

      document.querySelectorAll(".step.active").forEach((step) => {
        step.classList.remove("active");
      });

      status.textContent = "Pattern cleared.";
    });

    tempoSlider.addEventListener("input", () => {
      tempoValue.textContent = tempoSlider.value;

      // If the beat is already running, restart the timer at the new speed.
      if (isPlaying) {
        clearInterval(timer);
        timer = setInterval(tick, getStepTime());
      }
    });

    // Make the drum pads clickable.
    document.querySelectorAll(".pad").forEach((pad) => {
      pad.addEventListener("click", () => {
        triggerPad(pad.dataset.sound, pad);
      });
    });

    // Keyboard controls.
    const keyboardMap = {
      a: "kick",
      s: "snare",
      d: "hat",
      f: "clap"
    };

    document.addEventListener("keydown", (event) => {
      const sound = keyboardMap[event.key.toLowerCase()];

      if (!sound) return;

      const pad = document.querySelector(`.pad[data-sound="${sound}"]`);
      triggerPad(sound, pad);
    });

    function triggerPad(sound, pad) {
      playSound(sound);

      pad.classList.add("hit");
      setTimeout(() => pad.classList.remove("hit"), 90);

      status.textContent = `${capitalize(sound)} played.`;
    }

    function capitalize(word) {
      return word.charAt(0).toUpperCase() + word.slice(1);
    }

    // Give the user a starting beat so the page is immediately fun.
    [0, 8].forEach((step) => pattern.kick[step] = true);
    [4, 12].forEach((step) => pattern.snare[step] = true);
    [0, 2, 4, 6, 8, 10, 12, 14].forEach(
      (step) => pattern.hat[step] = true
    );

    document.querySelectorAll(".step").forEach((button) => {
      const sound = button.dataset.sound;
      const step = Number(button.dataset.step);

      if (pattern[sound][step]) {
        button.classList.add("active");
      }
    });
  </script>
</body>
</html>
