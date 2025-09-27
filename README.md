<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Advanced Virtual Piano</title>
<link rel="stylesheet" href="style.css">
</head>
<body>
<header>
  <h1>🎹 Virtual Piano</h1>
  <button id="toggle-theme">🌙 Dark/Light Mode</button>
</header>

<main>
  <div class="controls">
    <button id="record-btn">⏺ Record</button>
    <button id="play-btn">▶ Play</button>
    <button id="clear-btn">🗑 Clear</button>
  </div>
  
  <div id="piano"></div>
  
  <div class="tutorial">
    <button id="song1">Play Song 1</button>
    <button id="song2">Play Song 2</button>
  </div>
</main>

<footer>
  <p>© 2025 Golu | All Rights Reserved</p>
</footer>

<script src="script.js"></script>
</body>
</html>
* {margin:0;padding:0;box-sizing:border-box;}
body {font-family: Arial, sans-serif; background: #f0f0f0; color: #111; transition:0.3s;}
body.dark {background:#111; color:#eee;}

header {padding:20px; text-align:center; background:#222; color:#fff;}
header button {margin-left:20px; padding:5px 10px; cursor:pointer;}

.controls {text-align:center; margin:20px;}
.controls button {margin:0 10px; padding:10px 15px; cursor:pointer;}

#piano {display:flex; justify-content:center; flex-wrap: nowrap; overflow-x:auto; padding:20px;}

.key {position:relative; border:1px solid #333; cursor:pointer; display:inline-block;}
.key.white {width:40px; height:200px; background:white; margin:0 1px;}
.key.black {width:30px; height:120px; background:black; position:absolute; margin-left:-15px; z-index:10;}
.key.pressed {background: yellow !important; transition:0.1s;}

.tutorial {text-align:center; margin:20px;}
.tutorial button {margin:0 5px; padding:8px 12px; cursor:pointer;}

footer {text-align:center; padding:15px; background:#222; color:#aaa; margin-top:30px;}
// Full piano notes 2 octaves
const NOTES = [
  'C4','C#4','D4','D#4','E4','F4','F#4','G4','G#4','A4','A#4','B4',
  'C5','C#5','D5','D#5','E5','F5','F#5','G5','G#5','A5','A#5','B5'
];

// Create piano keys
const pianoDiv = document.getElementById('piano');
NOTES.forEach(note => {
  const key = document.createElement('div');
  key.classList.add('key');
  key.dataset.note = note;
  if(note.includes('#')) key.classList.add('black'); else key.classList.add('white');
  key.addEventListener('mousedown',()=>playNote(note,key));
  key.addEventListener('mouseup',()=>releaseKey(key));
  pianoDiv.appendChild(key);
});

// Play note
function playNote(note,keyDiv){
  keyDiv.classList.add('pressed');
  const audio = new Audio(`sounds/${note}.mp3`);
  audio.currentTime=0;
  audio.play();
  if(isRecording) recordedNotes.push({note, time: Date.now()-startTime});
}

// Release key
function releaseKey(keyDiv){ keyDiv.classList.remove('pressed'); }

// Keyboard support
const keyMap = {'a':'C4','w':'C#4','s':'D4','e':'D#4','d':'E4','f':'F4','t':'F#4','g':'G4','y':'G#4','h':'A4','u':'A#4','j':'B4','k':'C5'};
window.addEventListener('keydown',e=>{
  const note=keyMap[e.key];
  if(note){
    const keyDiv=[...document.getElementsByClassName('key')].find(k=>k.dataset.note===note);
    if(keyDiv) playNote(note,keyDiv);
  }
});
window.addEventListener('keyup',()=>document.querySelectorAll('.key.pressed').forEach(k=>k.classList.remove('pressed')));

// Dark/Light mode
const toggleBtn=document.getElementById('toggle-theme');
toggleBtn.addEventListener('click',()=>document.body.classList.toggle('dark'));

// Recording
let isRecording=false, recordedNotes=[], startTime=0;
document.getElementById('record-btn').addEventListener('click',()=>{
  isRecording=!isRecording;
  if(isRecording){recordedNotes=[]; startTime=Date.now(); alert('Recording started');}
  else alert('Recording stopped');
});
document.getElementById('play-btn').addEventListener('click',()=>{
  if(recordedNotes.length===0) return alert('No recording');
  recordedNotes.forEach(n=>{
    setTimeout(()=>playNote(n.note,[...document.getElementsByClassName('key')].find(k=>k.dataset.note===n.note)), n.time);
  });
});
document.getElementById('clear-btn').addEventListener('click',()=>recordedNotes=[]);

// Auto-play tutorial songs
const songs = {
  song1: ['C4','D4','E4','F4','G4','A4','B4','C5'],
  song2: ['E4','F4','G4','A4','B4','C5','D5','E5']
};
document.getElementById('song1').addEventListener('click',()=>playSong('song1'));
document.getElementById('song2').addEventListener('click',()=>playSong('song2'));
function playSong(name){
  const notes = songs[name];
  notes.forEach((note,i)=>setTimeout(()=>{
    playNote(note,[...document.getElementsByClassName('key')].find(k=>k.dataset.note===note));
  }, i*500));
}
