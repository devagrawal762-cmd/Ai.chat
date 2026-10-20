<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>TechX AI</title>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  background: #212121;
  color: #f5f5f5;
  height: 100dvh;
  display: flex;
}

.sidebar {
  width: 250px;
  background: #171717;
  padding: 18px;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.brand {
  font-size: 23px;
  font-weight: bold;
  padding: 10px 4px;
}

button {
  cursor: pointer;
  color: inherit;
  border: 1px solid #444;
  background: transparent;
  border-radius: 10px;
  padding: 12px;
  font-size: 14px;
}

button:hover {
  background: #353535;
}

.history {
  color: #bbb;
  font-size: 14px;
  overflow-y: auto;
}

.history div {
  padding: 12px 8px;
  border-radius: 8px;
  cursor: pointer;
  overflow-wrap: anywhere;
}

.history div:hover {
  background: #303030;
}

main {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  position: relative;
}

header {
  height: 60px;
  padding: 18px;
  border-bottom: 1px solid #383838;
  font-weight: bold;
}

#messages {
  flex: 1;
  overflow-y: auto;
  padding: 25px 16px 150px;
}

.message {
  max-width: 720px;
  width: 100%;
  margin: 0 auto 24px;
  line-height: 1.65;
  white-space: pre-wrap;
  overflow-wrap: anywhere;
}

.user {
  background: #303030;
  padding: 14px 17px;
  border-radius: 18px;
  width: fit-content;
  max-width: 85%;
  margin-left: auto;
}

.ai {
  padding: 10px 0;
}

.composer {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 20px 16px 12px;
  background: linear-gradient(transparent, #212121 18%);
}

.input-box {
  max-width: 760px;
  margin: auto;
  display: flex;
  align-items: end;
  gap: 10px;
  background: #303030;
  border: 1px solid #505050;
  padding: 10px;
  border-radius: 24px;
}

textarea {
  flex: 1;
  min-width: 0;
  resize: none;
  max-height: 140px;
  background: transparent;
  color: white;
  border: none;
  outline: none;
  padding: 10px;
  font: inherit;
}

#send {
  background: #f5f5f5;
  color: #111;
  border-radius: 50%;
  width: 42px;
  height: 42px;
  padding: 0;
  font-size: 20px;
}

.note {
  text-align: center;
  color: #aaa;
  font-size: 11px;
  margin-top: 10px;
}

@media (max-width: 650px) {
  .sidebar {
    width: 64px;
    padding: 10px 6px;
    align-items: center;
  }

  .brand {
    font-size: 12px;
    padding: 8px 0;
  }

  .new-label,
  .history {
    display: none;
  }

  #messages {
    padding-top: 22px;
  }

  header {
    padding: 20px 12px;
  }
}
</style>
</head>

<body>
<aside class="sidebar">
  <div class="brand">TechX AI</div>
  <button onclick="newChat()">＋ <span class="new-label">New Chat</span></button>
  <div style="font-size:13px;color:#999">Your chats</div>
  <div class="history" id="history"></div>
</aside>

<main>
  <header>TechX AI <span style="color:#999">⌄</span></header>

  <section id="messages">
    <div class="message ai">
      <h2 style="margin-bottom:12px">What can I help with?</h2>
      Welcome to TechX AI. Start a conversation below.
    </div>
  </section>

  <div class="composer">
    <div class="input-box">
      <textarea id="prompt" rows="1"
        placeholder="Message TechX AI..."
        aria-label="Message"></textarea>
      <button id="send" aria-label="Send message">↑</button>
    </div>
    <div class="note">
      TechX AI demo · Connect an AI backend for real answers
    </div>
  </div>
</main>

<script>
const messages = document.getElementById("messages");
const promptBox = document.getElementById("prompt");
const historyBox = document.getElementById("history");

let chats = [];
let currentChat = [];

function addMessage(text, type) {
  const item = document.createElement("div");
  item.className = "message " + type;

  // textContent prevents user input from being treated as HTML.
  item.textContent = text;
  messages.appendChild(item);
  messages.scrollTop = messages.scrollHeight;
}

function renderHistory() {
  historyBox.replaceChildren();

  chats.forEach((chat) => {
    const item = document.createElement("div");
    item.textContent = chat.title;
    item.onclick = () => openChat(chat.id);
    historyBox.appendChild(item);
  });
}

function newChat() {
  currentChat = [];
  messages.replaceChildren();
  addMessage("What can I help with?", "ai");
  promptBox.value = "";
}

function openChat(id) {
  const chat = chats.find(c => c.id === id);
  if (!chat) return;

  currentChat = chat.messages;
  messages.replaceChildren();

  currentChat.forEach(m => addMessage(m.text, m.type));
}

async function sendMessage() {
  const text = promptBox.value.trim();
  if (!text) return;

  promptBox.value = "";
  addMessage(text, "user");
  currentChat.push({ text, type: "user" });

  let chat = chats.find(c => c.messages === currentChat);

  if (!chat) {
    chat = {
      id: Date.now(),
      title: text.slice(0, 35),
      messages: currentChat
    };
    chats.unshift(chat);
    renderHistory();
  }

  // Demo reply: this is not a real AI model.
  const reply = "Your message was received! Connect a backend API to get real AI-generated answers.";

  addMessage(reply, "ai");
  currentChat.push({ text: reply, type: "ai" });
}

document.getElementById("send").addEventListener("click", sendMessage);

promptBox.addEventListener("keydown", (event) => {
  if (event.key === "Enter" && !event.shiftKey) {
    event.preventDefault();
    sendMessage();
  }
});
</script>
</body>
</html>
