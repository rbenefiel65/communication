<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />

  <title>Communication Assistant</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      padding: 20px;
      font-family: Arial, sans-serif;
      background: #f4f4f4;
      color: #222;
    }

    .app {
      width: min(900px, 100%);
      margin: 0 auto;
    }

    h1 {
      margin-top: 0;
      text-align: center;
    }

    textarea {
      width: 100%;
      min-height: 180px;
      padding: 18px;
      font-size: 28px;
      line-height: 1.4;
      border: 2px solid #777;
      border-radius: 12px;
      resize: vertical;
    }

    .controls,
    .phrases,
    .settings {
      display: grid;
      gap: 12px;
      margin-top: 16px;
    }

    .controls {
      grid-template-columns: repeat(3, 1fr);
    }

    .phrases {
      grid-template-columns: repeat(3, 1fr);
    }

    .settings {
      grid-template-columns: repeat(2, 1fr);
      background: white;
      padding: 16px;
      border-radius: 12px;
    }

    button {
      min-height: 70px;
      padding: 12px;
      border: none;
      border-radius: 12px;
      font-size: 22px;
      cursor: pointer;
      background: #dedede;
    }

    button:hover {
      filter: brightness(0.95);
    }

    .speak-button {
      background: #2075d6;
      color: white;
    }

    .stop-button {
      background: #b83232;
      color: white;
    }

    .clear-button {
      background: #555;
      color: white;
    }

    label {
      display: flex;
      flex-direction: column;
      gap: 8px;
      font-size: 18px;
    }

    select,
    input[type="range"] {
      width: 100%;
      min-height: 42px;
      font-size: 18px;
    }

    .shortcut-note {
      margin-top: 20px;
      padding: 14px;
      border-radius: 10px;
      background: white;
      font-size: 16px;
      line-height: 1.5;
    }

    @media (max-width: 650px) {
      .controls,
      .phrases,
      .settings {
        grid-template-columns: 1fr;
      }

      textarea {
        font-size: 22px;
      }
    }
  </style>
</head>

<body>
  <main class="app">
    <h1>Communication Assistant</h1>

    <textarea
      id="message"
      autofocus
      placeholder="Type what you want to say..."
      aria-label="Message to speak"
    ></textarea>

    <section class="controls">
      <button class="speak-button" id="speakButton">
        Speak
      </button>

      <button class="stop-button" id="stopButton">
        Stop
      </button>

      <button class="clear-button" id="clearButton">
        Clear
      </button>
    </section>

    <section class="phrases">
      <button data-phrase="Yes.">Yes</button>
      <button data-phrase="No.">No</button>
      <button data-phrase="I need help.">Help</button>
      <button data-phrase="Can I have some water, please?">
        Water
      </button>
      <button data-phrase="I am in pain.">Pain</button>
      <button data-phrase="Please give me a moment.">
        One moment
      </button>
      <button data-phrase="Thank you.">Thank you</button>
      <button data-phrase="I am okay.">I’m okay</button>
      <button data-phrase="Please call my husband.">
        Call Ryan
      </button>
    </section>

    <section class="settings">
      <label>
        Voice
        <select id="voiceSelect"></select>
      </label>

      <label>
        Speaking speed
        <input
          id="rate"
          type="range"
          min="0.5"
          max="2"
          value="1"
          step="0.1"
        />
      </label>

      <label>
        Pitch
        <input
          id="pitch"
          type="range"
          min="0.5"
          max="2"
          value="1"
          step="0.1"
        />
      </label>

      <label>
        Volume
        <input
          id="volume"
          type="range"
          min="0"
          max="1"
          value="1"
          step="0.1"
        />
      </label>
    </section>

    <div class="shortcut-note">
      <strong>Keyboard shortcuts:</strong><br />
      Ctrl + Enter: Speak<br />
      Escape: Stop<br />
      Alt + Y: Yes<br />
      Alt + N: No<br />
      Alt + H: Help<br />
      Alt + W: Water<br />
      Alt + P: Pain<br />
      Alt + Backspace: Clear
    </div>
  </main>

  <script>
    const messageBox = document.getElementById("message");
    const speakButton = document.getElementById("speakButton");
    const stopButton = document.getElementById("stopButton");
    const clearButton = document.getElementById("clearButton");

    const voiceSelect = document.getElementById("voiceSelect");
    const rateInput = document.getElementById("rate");
    const pitchInput = document.getElementById("pitch");
    const volumeInput = document.getElementById("volume");

    let availableVoices = [];

    function loadVoices() {
      availableVoices = window.speechSynthesis.getVoices();

      voiceSelect.innerHTML = "";

      availableVoices.forEach((voice, index) => {
        const option = document.createElement("option");

        option.value = index;
        option.textContent =
          `${voice.name} (${voice.lang})`;

        if (voice.default) {
          option.textContent += " — Default";
        }

        voiceSelect.appendChild(option);
      });
    }

    function speakText(text) {
      const cleanedText = text.trim();

      if (!cleanedText) {
        return;
      }

      window.speechSynthesis.cancel();

      const utterance =
        new SpeechSynthesisUtterance(cleanedText);

      const selectedVoice =
        availableVoices[Number(voiceSelect.value)];

      if (selectedVoice) {
        utterance.voice = selectedVoice;
      }

      utterance.rate = Number(rateInput.value);
      utterance.pitch = Number(pitchInput.value);
      utterance.volume = Number(volumeInput.value);

      window.speechSynthesis.speak(utterance);
    }

    function speakMessageBox() {
      speakText(messageBox.value);
    }

    function stopSpeaking() {
      window.speechSynthesis.cancel();
    }

    function clearMessage() {
      stopSpeaking();
      messageBox.value = "";
      messageBox.focus();
    }

    function speakPreset(phrase) {
      messageBox.value = phrase;
      speakText(phrase);
    }

    speakButton.addEventListener("click", speakMessageBox);
    stopButton.addEventListener("click", stopSpeaking);
    clearButton.addEventListener("click", clearMessage);

    document
      .querySelectorAll("[data-phrase]")
      .forEach((button) => {
        button.addEventListener("click", () => {
          speakPreset(button.dataset.phrase);
        });
      });

    document.addEventListener("keydown", (event) => {
      if (event.ctrlKey && event.key === "Enter") {
        event.preventDefault();
        speakMessageBox();
        return;
      }

      if (event.key === "Escape") {
        event.preventDefault();
        stopSpeaking();
        return;
      }

      if (event.altKey && event.key.toLowerCase() === "y") {
        event.preventDefault();
        speakPreset("Yes.");
      }

      if (event.altKey && event.key.toLowerCase() === "n") {
        event.preventDefault();
        speakPreset("No.");
      }

      if (event.altKey && event.key.toLowerCase() === "h") {
        event.preventDefault();
        speakPreset("I need help.");
      }

      if (event.altKey && event.key.toLowerCase() === "w") {
        event.preventDefault();
        speakPreset("Can I have some water, please?");
      }

      if (event.altKey && event.key.toLowerCase() === "p") {
        event.preventDefault();
        speakPreset("I am in pain.");
      }

      if (
        event.altKey &&
        event.key === "Backspace"
      ) {
        event.preventDefault();
        clearMessage();
      }
    });

    loadVoices();

    window.speechSynthesis.addEventListener(
      "voiceschanged",
      loadVoices
    );
  </script>
</body>
</html>
