<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />

  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />

  <title>Alice's Voice</title>

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

    h2 {
      margin-top: 28px;
      margin-bottom: 10px;
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
      grid-template-columns: repeat(4, 1fr);
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

    button:active {
      transform: scale(0.98);
    }

    .speak-button {
      background: #2075d6;
      color: white;
    }

    .save-button {
      background: #258044;
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

    .custom-phrase-wrapper {
      display: flex;
      gap: 6px;
    }

    .custom-phrase-wrapper .phrase-button {
      flex: 1;
    }

    .delete-phrase-button {
      min-width: 55px;
      width: 55px;
      padding: 8px;
      background: #b83232;
      color: white;
      font-size: 28px;
    }

    .empty-message {
      grid-column: 1 / -1;
      margin: 0;
      padding: 16px;
      background: white;
      border-radius: 10px;
      font-size: 17px;
      color: #555;
    }

    .shortcut-note {
      margin-top: 20px;
      padding: 14px;
      border-radius: 10px;
      background: white;
      font-size: 16px;
      line-height: 1.5;
    }

    @media (max-width: 750px) {
      .controls {
        grid-template-columns: repeat(2, 1fr);
      }

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
    <h1>Alice's Voice</h1>

    <textarea
      id="message"
      autofocus
      placeholder="Type what you want to say..."
      aria-label="Message to speak"
    ></textarea>

    <section class="controls">
      <button
        class="speak-button"
        id="speakButton"
        type="button"
      >
        Speak
      </button>

      <button
        class="save-button"
        id="savePhraseButton"
        type="button"
      >
        Save Phrase
      </button>

      <button
        class="stop-button"
        id="stopButton"
        type="button"
      >
        Stop
      </button>

      <button
        class="clear-button"
        id="clearButton"
        type="button"
      >
        Clear
      </button>
    </section>

    <h2>Quick Phrases</h2>

    <section class="phrases">
      <button type="button" data-phrase="Yes.">
        Yes
      </button>

      <button type="button" data-phrase="No.">
        No
      </button>

      <button type="button" data-phrase="I need help.">
        Help
      </button>

      <button
        type="button"
        data-phrase="Can I have some water, please?"
      >
        Water
      </button>

      <button type="button" data-phrase="I am in pain.">
        Pain
      </button>

      <button
        type="button"
        data-phrase="Please give me a moment."
      >
        One Moment
      </button>

      <button type="button" data-phrase="Thank you.">
        Thank You
      </button>

      <button type="button" data-phrase="I am okay.">
        I'm Okay
      </button>

      <button
        type="button"
        data-phrase="Please call my husband."
      >
        Call Ryan
      </button>
    </section>

    <h2>Saved Phrases</h2>

    <section
      class="phrases"
      id="customPhrases"
      aria-label="Saved phrases"
    ></section>

    <h2>Voice Settings</h2>

    <section class="settings">
      <label>
        Voice
        <select id="voiceSelect"></select>
      </label>

      <label>
        Speaking Speed
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
    const savePhraseButton =
      document.getElementById("savePhraseButton");
    const stopButton = document.getElementById("stopButton");
    const clearButton = document.getElementById("clearButton");

    const customPhrasesContainer =
      document.getElementById("customPhrases");

    const voiceSelect = document.getElementById("voiceSelect");
    const rateInput = document.getElementById("rate");
    const pitchInput = document.getElementById("pitch");
    const volumeInput = document.getElementById("volume");

    const STORAGE_KEY = "aliceSavedPhrases";

    let availableVoices = [];

    function loadVoices() {
      availableVoices = window.speechSynthesis.getVoices();

      voiceSelect.innerHTML = "";

      availableVoices.forEach((voice, index) => {
        const option = document.createElement("option");

        option.value = index;
        option.textContent = `${voice.name} (${voice.lang})`;

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

    function getSavedPhrases() {
      try {
        const savedData = localStorage.getItem(STORAGE_KEY);

        if (!savedData) {
          return [];
        }

        const phrases = JSON.parse(savedData);

        return Array.isArray(phrases) ? phrases : [];
      } catch (error) {
        console.error("Could not load saved phrases:", error);
        return [];
      }
    }

    function storeSavedPhrases(phrases) {
      try {
        localStorage.setItem(
          STORAGE_KEY,
          JSON.stringify(phrases)
        );

        return true;
      } catch (error) {
        console.error("Could not save phrases:", error);

        alert(
          "The phrase could not be saved on this device."
        );

        return false;
      }
    }

    function renderSavedPhrases() {
      const phrases = getSavedPhrases();

      customPhrasesContainer.innerHTML = "";

      if (phrases.length === 0) {
        const emptyMessage = document.createElement("p");
        emptyMessage.className = "empty-message";
        emptyMessage.textContent =
          "Saved phrases will appear here.";

        customPhrasesContainer.appendChild(emptyMessage);
        return;
      }

      phrases.forEach((phrase, index) => {
        const wrapper = document.createElement("div");
        wrapper.className = "custom-phrase-wrapper";

        const phraseButton = document.createElement("button");
        phraseButton.type = "button";
        phraseButton.className = "phrase-button";
        phraseButton.textContent = phrase.label;

        phraseButton.addEventListener("click", () => {
          speakPreset(phrase.text);
        });

        const deleteButton = document.createElement("button");
        deleteButton.type = "button";
        deleteButton.className = "delete-phrase-button";
        deleteButton.textContent = "×";

        deleteButton.setAttribute(
          "aria-label",
          `Delete ${phrase.label}`
        );

        deleteButton.addEventListener("click", () => {
          const shouldDelete = confirm(
            `Delete the saved phrase "${phrase.label}"?`
          );

          if (!shouldDelete) {
            return;
          }

          const updatedPhrases = getSavedPhrases();
          updatedPhrases.splice(index, 1);

          if (storeSavedPhrases(updatedPhrases)) {
            renderSavedPhrases();
          }
        });

        wrapper.appendChild(phraseButton);
        wrapper.appendChild(deleteButton);

        customPhrasesContainer.appendChild(wrapper);
      });
    }

    function saveCurrentPhrase() {
      const phraseText = messageBox.value.trim();

      if (!phraseText) {
        alert("Type a phrase before saving it.");
        messageBox.focus();
        return;
      }

      const suggestedLabel =
        phraseText.length > 20
          ? `${phraseText.slice(0, 20)}...`
          : phraseText;

      const phraseLabel = prompt(
        "What should the button be called?",
        suggestedLabel
      );

      if (phraseLabel === null) {
        return;
      }

      const cleanedLabel = phraseLabel.trim();

      if (!cleanedLabel) {
        alert("Please give the button a name.");
        return;
      }

      const phrases = getSavedPhrases();

      phrases.push({
        label: cleanedLabel,
        text: phraseText
      });

      if (storeSavedPhrases(phrases)) {
        renderSavedPhrases();
      }
    }

    speakButton.addEventListener("click", speakMessageBox);

    savePhraseButton.addEventListener(
      "click",
      saveCurrentPhrase
    );

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
        return;
      }

      if (event.altKey && event.key.toLowerCase() === "n") {
        event.preventDefault();
        speakPreset("No.");
        return;
      }

      if (event.altKey && event.key.toLowerCase() === "h") {
        event.preventDefault();
        speakPreset("I need help.");
        return;
      }

      if (event.altKey && event.key.toLowerCase() === "w") {
        event.preventDefault();
        speakPreset("Can I have some water, please?");
        return;
      }

      if (event.altKey && event.key.toLowerCase() === "p") {
        event.preventDefault();
        speakPreset("I am in pain.");
        return;
      }

      if (event.altKey && event.key === "Backspace") {
        event.preventDefault();
        clearMessage();
      }
    });

    loadVoices();
    renderSavedPhrases();

    window.speechSynthesis.addEventListener(
      "voiceschanged",
      loadVoices
    );
  </script>
</body>
</html>
