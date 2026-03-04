import express from "express";
import fetch from "node-fetch";
import cors from "cors";
import path from "path";
import { fileURLToPath } from "url";

const app = express();
app.use(cors());
app.use(express.json());

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const API_KEY = "AIzaSyAafcdgttPPKfnRgQgraXUelcma6SfyDEw"; // 🔑 Put your key here

// Gemini 2.5 model endpoint
app.post("/chat", async (req, res) => {
  try {
    const message = req.body.message;

    const response = await fetch(
      `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${API_KEY}`,
      {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          contents: [{ parts: [{ text: message }] }]
        })
      }
    );

    const data = await response.json();

    if (data.candidates) {
      res.json({
        reply: data.candidates[0].content.parts[0].text
      });
    } else {
      res.json({ reply: "No response from Gemini 2.5" });
    }

  } catch (err) {
    res.json({ reply: "Server error: " + err.message });
  }
});

app.use(express.static(__dirname));

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Advanced Gemini 2.5 AI</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body{margin:0;font-family:Arial;background:#000;color:#fff}
header{padding:10px;background:#111;display:flex;justify-content:space-between;flex-wrap:wrap}
button{background:#222;color:#fff;border:1px solid #444;padding:5px 10px;margin:2px;cursor:pointer}
#chat{height:60vh;overflow:auto;padding:10px}
.msg{margin:8px 0}
.user{color:#0f0}
.bot{color:#0af}
textarea{width:100%;height:60px;background:#111;color:#fff;border:1px solid #333}
footer{padding:10px;background:#111}
.hidden{display:none}
#privacy{position:fixed;top:0;left:0;width:100%;height:100%;background:#000;color:#fff;overflow:auto;padding:20px;display:none}
</style>
</head>
<body>

<header>
<div>🚀 Gemini 2.5 Advanced AI</div>
<div>
<button onclick="clearChat()">Clear</button>
<button onclick="downloadChat()">Download</button>
<button onclick="print()">Print</button>
<button onclick="toggleTheme()">Theme</button>
<button onclick="openPrivacy()">Privacy</button>
<button onclick="premium()">Premium</button>
</div>
</header>

<div id="chat"></div>

<footer>
<textarea id="input" placeholder="Ask anything..."></textarea>
<div>
<button onclick="send()">Send</button>
<button onclick="voice()">🎤</button>
<button onclick="stopSpeak()">⏹</button>
<button onclick="camera()">📷</button>
</div>
<div id="counter"></div>
</footer>

<video id="cam" width="200" class="hidden" autoplay></video>

<div id="privacy">
<button onclick="closePrivacy()">Close</button>
<h2>Privacy Policy</h2>
<p>This app connects to Google Gemini 2.5 API.</p>
<p>Chat history stored locally in browser.</p>
<p>No personal data stored on external server except API processing.</p>
</div>

<script>
const chat=document.getElementById("chat");
const input=document.getElementById("input");

window.onload=()=>chat.innerHTML=localStorage.getItem("chat")||"";

function save(){localStorage.setItem("chat",chat.innerHTML)}

function add(text,type){
const div=document.createElement("div");
div.className="msg "+type;
div.innerHTML="<b>"+type+":</b> "+text;
chat.appendChild(div);
chat.scrollTop=chat.scrollHeight;
save();
}

async function send(){
const msg=input.value;
if(!msg) return;
add(msg,"user");
input.value="";
add("Typing...","bot");

const res=await fetch("/chat",{
method:"POST",
headers:{"Content-Type":"application/json"},
body:JSON.stringify({message:msg})
});
const data=await res.json();
chat.lastChild.remove();
add(data.reply,"bot");
}

function clearChat(){chat.innerHTML="";save()}
function downloadChat(){
const blob=new Blob([chat.innerText],{type:"text/plain"});
const a=document.createElement("a");
a.href=URL.createObjectURL(blob);
a.download="chat.txt";
a.click();
}

function toggleTheme(){
document.body.style.background=
document.body.style.background=="white"?"black":"white";
document.body.style.color=
document.body.style.color=="black"?"white":"black";
}

function voice(){
const rec=new(window.SpeechRecognition||window.webkitSpeechRecognition)();
rec.onresult=e=>input.value=e.results[0][0].transcript;
rec.start();
}

function stopSpeak(){speechSynthesis.cancel();}

function camera(){
const v=document.getElementById("cam");
v.classList.remove("hidden");
navigator.mediaDevices.getUserMedia({video:true})
.then(stream=>v.srcObject=stream);
}

input.addEventListener("input",()=>{
document.getElementById("counter").innerText=
"Words: "+input.value.split(" ").length+
" | Characters: "+input.value.length;
});

function openPrivacy(){document.getElementById("privacy").style.display="block";}
function closePrivacy(){document.getElementById("privacy").style.display="none";}
function premium(){alert("Upgrade to Premium for Unlimited AI 🚀");}
</script>

</body>
</html>
