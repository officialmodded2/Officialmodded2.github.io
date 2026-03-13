# Officialmodded2.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Rivals Hub Key System</title>
<style>
body {
    margin: 0;
    padding: 0;
    font-family: "Segoe UI", Arial, sans-serif;
    background: linear-gradient(135deg, #0f0f0f, #1a1a1a);
    color: white;
    text-align: center;
}
.container {
    max-width: 550px;
    margin: 60px auto;
    padding: 30px;
    border-radius: 12px;
    background: rgba(0,0,0,0.75);
    box-shadow: 0 0 25px #00ff9c;
}
h1 { color: #00ff9c; margin-bottom: 20px; }
p { font-size: 16px; margin-bottom: 25px; }
input[type="text"] {
    width: 80%;
    padding: 10px;
    margin: 10px 0;
    border-radius: 6px;
    border: none;
}
button {
    padding: 12px 25px;
    margin: 10px;
    font-size: 16px;
    font-weight: bold;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    background: #00ff9c;
    color: #000;
    transition: 0.3s;
}
button:hover { background: #00ffa5; transform: scale(1.05); }
#key { display: none; margin-top: 20px; font-size: 22px; color: #00ff9c; }
#expiry { display: none; margin-top: 10px; font-size: 14px; color: #ff6b6b; }
</style>
</head>
<body>

<div class="container">
<h1>Rivals Hub Key System</h1>
<p>Complete the tasks below or enter your own link to unlock a key</p>

<button id="task1">Subscribe to YouTube</button><br>
<button id="task2">Visit Page</button><br>
<button id="task3">Join Discord</button><br>

<p>Or enter your own task link:</p>
<input type="text" id="customLink" placeholder="Enter your custom URL here">
<button id="useCustomLink">Use Custom Link</button><br>

<button id="generateKey">Generate Key</button>
<button id="copyKey" style="display:none;">Copy Key</button>

<div id="key"></div>
<div id="expiry"></div>
</div>

<script>
// --- Configuration ---
const DEFAULT_TASKS = {
    task1: "https://youtube.com/YOURCHANNEL",
    task2: "https://example.com/page",
    task3: "https://discord.gg/YOURSERVER"
};
let tasksDone = { task1:false, task2:false, task3:false };
let currentKey = "";
let generated = false;
let botSafe = false;

// Anti-bot check: wait 3 seconds
setTimeout(()=>{ botSafe = true; }, 3000);

let customLinkUsed = false;

// --- Buttons ---
document.getElementById("task1").addEventListener("click", ()=>{
    tasksDone.task1 = true;
    window.open(DEFAULT_TASKS.task1,"_blank");
});
document.getElementById("task2").addEventListener("click", ()=>{
    tasksDone.task2 = true;
    window.open(DEFAULT_TASKS.task2,"_blank");
});
document.getElementById("task3").addEventListener("click", ()=>{
    tasksDone.task3 = true;
    window.open(DEFAULT_TASKS.task3,"_blank");
});

// --- Custom link ---
document.getElementById("useCustomLink").addEventListener("click", ()=>{
    const link = document.getElementById("customLink").value.trim();
    if(!link){ alert("Enter a valid URL."); return; }
    customLinkUsed = true;
    alert("Custom link set! You can now generate your key.");
    window.open(link,"_blank");
});

// --- Generate Key ---
const SERVER_URL = "https://yourserver.com"; // Replace with your secure server
document.getElementById("generateKey").addEventListener("click", async ()=>{
    if(!botSafe){ alert("Please wait a few seconds to verify human interaction."); return; }
    if(generated){ alert("Key already generated!"); return; }
    if(!tasksDone.task1 || !tasksDone.task2 || !tasksDone.task3){
        if(!customLinkUsed){ alert("Complete all tasks or use a custom link first."); return; }
    }

    try {
        const res = await fetch(`${SERVER_URL}/generate-key`);
        if(!res.ok) throw new Error("Failed to generate key");
        const data = await res.json();
        if(data.key){
            currentKey = data.key;
            generated = true;
            document.getElementById("key").innerHTML = "Your Key: <b>" + currentKey + "</b>";
            document.getElementById("key").style.display = "block";

            if(data.expiry){
                document.getElementById("expiry").innerHTML = "Expires in: <b>"+data.expiry+"</b> minutes";
                document.getElementById("expiry").style.display = "block";
            }

            document.getElementById("copyKey").style.display = "inline-block";
        } else {
            alert("Error generating key from server.");
        }
    } catch(e) {
        alert("Server not reachable: " + e.message);
    }
});

// --- Copy Key ---
document.getElementById("copyKey").addEventListener("click", ()=>{
    if(currentKey !== ""){
        navigator.clipboard.writeText(currentKey).then(()=>{
            alert("Key copied to clipboard!");
        }).catch(()=>{ alert("Copy manually: "+currentKey); });
    }
});
</script>

</body>
</html>
