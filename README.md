
<html lang="fa">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Al.VIPZEXNET - چت هوش مصنوعی</title>
<style>
  body {
    margin: 0;
    font-family: "Segoe UI", sans-serif;
    background: linear-gradient(135deg, #0d1117, #1a1f29);
    color: #fff;
    display: flex;
    flex-direction: column;
    height: 100vh;
  }
  header {
    background: #161b22cc;
    backdrop-filter: blur(8px);
    padding: 12px 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #30363d;
  }
  header h1 {
    margin: 0;
    font-size: 18px;
    color: #58a6ff;
  }
  #modelSelect {
    background: #0d1117;
    color: #fff;
    border: 1px solid #30363d;
    padding: 6px 8px;
    border-radius: 6px;
  }
  #chatWindow {
    flex: 1;
    overflow-y: auto;
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    scrollbar-width: thin;
  }
  .message-wrapper {
    display: flex;
    align-items: flex-end;
    gap: 8px;
  }
  .avatar {
    width: 34px;
    height: 34px;
    border-radius: 50%;
    flex-shrink: 0;
    background-size: cover;
    background-position: center;
  }
  .message {
    max-width: 70%;
    padding: 12px 16px;
    border-radius: 16px;
    line-height: 1.5;
    animation: fadeIn 0.3s ease-in-out;
    white-space: pre-wrap;
    box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    word-break: break-word;
    position: relative;
  }
  .user {
    background: #238636;
    color: #fff;
    border-bottom-right-radius: 4px;
    margin-left: auto;
  }
  .bot {
    background: #21262d;
    color: #fff;
    border-bottom-left-radius: 4px;
    margin-right: auto;
  }
  .deleteBtn {
    position: absolute;
    top: 2px;
    right: 4px;
    background: transparent;
    border: none;
    color: #ff4d4d;
    cursor: pointer;
    font-size: 14px;
  }
  #inputArea {
    display: flex;
    padding: 12px;
    border-top: 1px solid #30363d;
    background: #161b22cc;
    backdrop-filter: blur(6px);
    flex-wrap: wrap;
    gap: 6px;
  }
  #userInput {
    flex: 1;
    padding: 10px 12px;
    border: none;
    border-radius: 10px;
    background: #0d1117;
    color: #fff;
    font-size: 15px;
    resize: none;
    max-height: 120px;
    line-height: 1.4;
  }
  #sendBtn, #fileBtn {
    padding: 10px 18px;
    background: #58a6ff;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    color: #fff;
    font-size: 15px;
    transition: background 0.2s ease;
  }
  #sendBtn:hover, #fileBtn:hover {
    background: #1f6feb;
  }
  @keyframes fadeIn {
    from {opacity: 0; transform: translateY(5px);}
    to {opacity: 1; transform: translateY(0);}
  }
  @media (max-width: 768px) {
    header h1 { font-size: 16px; }
    #chatWindow { padding: 12px; gap: 10px; }
    .message { max-width: 85%; font-size: 14px; padding: 10px 14px; }
    #userInput { font-size: 14px; }
    #sendBtn, #fileBtn { font-size: 14px; padding: 8px 14px; }
  }
  @media (max-width: 480px) {
    header { flex-direction: column; align-items: flex-start; gap: 8px; }
    #modelSelect { width: 100%; }
    .message { max-width: 90%; }
    #inputArea { flex-direction: column; gap: 8px; }
    #sendBtn, #fileBtn { width: 100%; }
  }
</style>
</head>
<body>
<header>
<h1>🤖 Al.VIPZEXNET</h1>
<select id="modelSelect">
  <option value="openai">OpenAI</option>
  <option value="deepseek">DeepSeek</option>
  <option value="mistral">Mistral</option>
  <option value="openai-large">OpenAI Large (GPT-4)</option>
</select>
</header>

<div id="chatWindow"></div>

<div id="inputArea">
<textarea id="userInput" placeholder="پیام خود را به Al.VIPZEXNET بنویسید..." rows="1"
onkeydown="if(event.key==='Enter' && !event.shiftKey){event.preventDefault();sendMessage()}"></textarea>
<input type="file" id="fileInput" style="display:none">
<button id="fileBtn">📎 فایل/تصویر</button>
<button id="sendBtn">ارسال</button>
</div>

<script>
let selectedModel = "openai";
const chatHistory = [];

document.getElementById("modelSelect").addEventListener("change", function () {
  selectedModel = this.value;
});

// باز کردن انتخاب فایل
document.getElementById("fileBtn").addEventListener("click", () => {
  document.getElementById("fileInput").click();
});

// وقتی فایل انتخاب شد
document.getElementById("fileInput").addEventListener("change", (e) => {
  const file = e.target.files[0];
  if (!file) return;

  const reader = new FileReader();
  reader.onload = function(ev) {
    const dataUrl = ev.target.result;
    const index = chatHistory.length;
    chatHistory.push({ user: `[فایل]: ${file.name}`, bot: "" });
    addMessage(`[فایل]: ${file.name}`, "user", index);
    addMessage(`<img src="${dataUrl}" style="max-width:100%;border-radius:8px">`, "bot", index);
  };
  reader.readAsDataURL(file);
});

// افزودن پیام
function addMessage(text, sender, responseIndex = null) {
  const chatWindow = document.getElementById("chatWindow");
  const wrapper = document.createElement("div");
  wrapper.className = "message-wrapper " + sender;

  const avatar = document.createElement("div");
  avatar.className = "avatar";
  avatar.style.backgroundImage = sender === "user" 
    ? "url('https://i.imgur.com/0y0y0y0.png')" 
    : "url('https://i.imgur.com/q1bPzOZ.png')"; // آواتار Al.VIPZEXNET

  const msgDiv = document.createElement("div");
  msgDiv.className = "message " + sender;
  msgDiv.innerHTML = text;

  if(sender==="user"){
    msgDiv.style.cursor = "pointer";
    msgDiv.title = "برای ویرایش کلیک کنید";
    msgDiv.addEventListener("click", ()=>editMessage(msgDiv,responseIndex));
    // دکمه حذف
    const delBtn = document.createElement("button");
    delBtn.className = "deleteBtn";
    delBtn.textContent = "✖";
    delBtn.onclick = ()=>{ wrapper.remove(); chatHistory[responseIndex]=null; };
    msgDiv.appendChild(delBtn);

    wrapper.appendChild(msgDiv);
    wrapper.appendChild(avatar);
  } else {
    wrapper.appendChild(avatar);
    wrapper.appendChild(msgDiv);
  }

  chatWindow.appendChild(wrapper);
  chatWindow.scrollTop = chatWindow.scrollHeight;

  // پاسخ صوتی بات
  if(sender==="bot"){
    const utterance = new SpeechSynthesisUtterance(msgDiv.innerText);
    utterance.lang = "fa-IR";
    speechSynthesis.speak(utterance);
  }
}

// ویرایش پیام
function editMessage(msgDiv,responseIndex){
  const originalText = msgDiv.innerText.replace("✖","");
  const input = document.createElement("textarea");
  input.value = originalText;
  input.style.width="100%";
  input.style.fontSize="14px";
  input.style.borderRadius="8px";
  input.style.padding="6px";
  input.style.boxSizing="border-box";
  msgDiv.replaceWith(input);
  input.focus();
  input.addEventListener("keydown",async e=>{
    if(e.key==="Enter" && !e.shiftKey){
      e.preventDefault();
      const newText = input.value;
      chatHistory[responseIndex].user = newText;
      const newMsg = document.createElement("div");
      newMsg.className="message user";
      newMsg.textContent=newText;
      newMsg.style.cursor="pointer";
      newMsg.title="برای ویرایش کلیک کنید";
      newMsg.addEventListener("click",()=>editMessage(newMsg,responseIndex));
      // دکمه حذف
      const delBtn = document.createElement("button");
      delBtn.className = "deleteBtn";
      delBtn.textContent = "✖";
      delBtn.onclick = ()=>{ newMsg.parentElement.remove(); chatHistory[responseIndex]=null; };
      newMsg.appendChild(delBtn);

      input.replaceWith(newMsg);
      await updateBotResponse(responseIndex);
    } else if(e.key==="Escape"){
      const originalMsg = document.createElement("div");
      originalMsg.className="message user";
      originalMsg.textContent=originalText;
      originalMsg.style.cursor="pointer";
      originalMsg.title="برای ویرایش کلیک کنید";
      originalMsg.addEventListener("click",()=>editMessage(originalMsg,responseIndex));
      // دکمه حذف
      const delBtn = document.createElement("button");
      delBtn.className="deleteBtn";
      delBtn.textContent = "✖";
      delBtn.onclick = ()=>{ originalMsg.parentElement.remove(); chatHistory[responseIndex]=null; };
      originalMsg.appendChild(delBtn);

      input.replaceWith(originalMsg);
    }
  });
}

// بروزرسانی پاسخ بات بعد از ویرایش پیام
async function updateBotResponse(index){
  const loadingMsg=document.createElement("div");
  loadingMsg.className="message bot";
  loadingMsg.textContent="⌛ بروزرسانی پاسخ Al.VIPZEXNET...";
  document.getElementById("chatWindow").appendChild(loadingMsg);
  try{
    const formData = new FormData();
    formData.append("userid","user123");
    formData.append("prompt",chatHistory[index].user);
    formData.append("model",selectedModel);

    const res = await fetch("https://api2.api-code.ir/gpt-save-v2/",{
      method:"POST",
      body:formData
    });
    const data = await res.text();
    loadingMsg.remove();
    chatHistory[index].bot = data;
    addMessage(data,"bot",index);
  }catch(err){
    loadingMsg.remove();
    addMessage("❌ خطا در بروزرسانی پاسخ Al.VIPZEXNET","bot",index);
  }
}

// ارسال پیام
async function sendMessage(){
  const input = document.getElementById("userInput");
  const text = input.value.trim();
  if(!text) return;

  const index = chatHistory.length;
  chatHistory.push({user:text, bot:""});
  addMessage(text,"user",index);
  input.value="";
  input.style.height="auto";

  const loadingMsg=document.createElement("div");
  loadingMsg.className="message bot";
  loadingMsg.textContent="⌛ در حال دریافت پاسخ از Al.VIPZEXNET...";
  document.getElementById("chatWindow").appendChild(loadingMsg);

  try{
    const formData = new FormData();
    formData.append("userid","user123");
    formData.append("prompt",text);
    formData.append("model",selectedModel);

    const res = await fetch("https://api2.api-code.ir/gpt-save-v2/",{
      method:"POST",
      body:formData
    });
    const data = await res.text();
    loadingMsg.remove();
    chatHistory[index].bot = data;
    addMessage(data,"bot",index);
  }catch(err){
    loadingMsg.remove();
    addMessage("❌ خطا در دریافت پاسخ از Al.VIPZEXNET","bot",index);
  }
}
</script>
</body>
</html>
