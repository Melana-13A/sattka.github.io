<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Will You Be Mine?</title>
  <style>
    :root{
      --card-bg: rgba(255,255,255,0.95);
      --accent: #ff7b89;
      --muted: #6b6b6b;
    }

    html,body{
      height:100%;
      margin:0;
      font-family: "Segoe UI", system-ui, -apple-system, "Helvetica Neue", Arial;
      background: linear-gradient(135deg,#fdfbfb,#ebedee);
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      overflow:hidden;
    }

    .stage {
      min-height:100%;
      display:flex;
      align-items:center;
      justify-content:center;
      padding:30px;
      box-sizing:border-box;
    }

    .card {
      width:100%;
      max-width:640px;
      background:var(--card-bg);
      border-radius:20px;
      box-shadow:0 8px 30px rgba(0,0,0,0.08);
      padding:32px;
      position:relative;          /* important: containing block for absolute children */
      z-index:2;
      box-sizing:border-box;
      overflow:visible;
    }

    h1{
      margin:0 0 10px 0;
      color:var(--accent);
      font-size:28px;
      letter-spacing:0.2px;
      text-shadow:0 6px 18px rgba(255,123,137,0.08);
    }

    .lead {
      color:var(--muted);
      margin:0 0 18px 0;
      font-size:16px;
    }

    .buttons {
      margin-top:18px;
      min-height:48px; /* reserve some space so button doesn't jump out of view */
      position:relative;
    }

    button {
      display:inline-block;
      padding:10px 22px;
      margin:0;
      font-size:15px;
      border-radius:24px;
      border:none;
      cursor:pointer;
      outline:none;
      transition:transform .18s ease, box-shadow .18s ease, left .12s ease, top .12s ease;
      position:relative;
    }

    #yes {
      background: linear-gradient(180deg,#ff99a8,#ff7b89);
      color:#fff;
      box-shadow:0 6px 18px rgba(255,123,137,0.22);
    }
    #yes:active{ transform:scale(.98); }

    #no {
      background:#bdbdbd;
      color:#fff;
      box-shadow:0 6px 14px rgba(0,0,0,0.06);
      position: absolute; /* we will move this inside the .buttons container */
      left: 260px;        /* initial placement (fallback) */
      top: 0;
      touch-action: manipulation;
    }

    .hidden { display:none !important; }

    #response {
      margin-top:18px;
      color:var(--accent);
      font-size:20px;
      font-weight:600;
    }

    .big-heart {
      font-size:86px;
      display:block;
      margin: 10px auto;
      animation: beat 1000ms infinite alternate;
      pointer-events:none;
    }
    @keyframes beat { to { transform:scale(1.18); } }

    /* floating petals */
    .petal {
      position:fixed;
      pointer-events:none;
      user-select:none;
      will-change: transform, opacity;
      font-size:18px;
      z-index:1;
      opacity:0.95;
    }

    /* small responsive tweaks */
    @media (max-width:520px){
      .card { padding:22px; border-radius:16px; }
      h1 { font-size:22px; }
      #no { left: 160px; }
    }
  </style>
</head>
<body>
  <div class="stage">
    <main class="card" role="main" aria-labelledby="title">
      <h1 id="title">Will You Be Mine? 🌸</h1>

      <p class="lead" id="prompt">
        From the moment I met you, my world began to bloom — will you let me hold your heart forever?
      </p>

      <div class="buttons" aria-hidden="false">
        <button id="yes" type="button" aria-label="Yes, I will">Yes 💖</button>
        <button id="no" type="button" aria-label="No, I won't">No 😅</button>
      </div>

      <div id="response" class="hidden" aria-live="polite">
        <span class="big-heart" aria-hidden="true">💗</span>
        <div id="love-message" style="white-space:pre-wrap;"></div>
      </div>
    </main>
  </div>

  <!-- Music: will be played after a user gesture (click Yes) -->
  <audio id="love-music" preload="auto" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3"></audio>
  <!-- Replace src with your own MP3 file if you prefer -->

  <script>
    (function(){
      const yesBtn = document.getElementById('yes');
      const noBtn  = document.getElementById('no');
      const card   = document.querySelector('.card');
      const question = document.getElementById('prompt');
      const response = document.getElementById('response');
      const loveMsg  = document.getElementById('love-message');
      const audio    = document.getElementById('love-music');

      // create petals (cleaned up)
      function createPetal(){
        const p = document.createElement('div');
        p.className = 'petal';
        p.textContent = '🌸';
        const size = 10 + Math.random()*28; // px
        p.style.fontSize = size + 'px';
        // start X position across the viewport
        p.style.left = (Math.random()*100) + 'vw';
        // set transform animation using requestAnimationFrame for smoother timing
        const duration = 6 + Math.random()*6; // seconds
        const start = performance.now();
        const startY = -10; // start off top
        const endY = 110;   // end off bottom
        p.style.opacity = '1';
        document.body.appendChild(p);

        // animate manually to avoid needing keyframes count control
        function tick(now){
          const t = (now - start) / (duration*1000);
          if (t >= 1){
            p.remove();
            return;
          }
          // progress eased slightly
          const eased = t; // linear works fine here
          const y = startY + (endY - startY) * eased;
          const rotation = eased * 360 * (Math.random()<0.5 ? 1 : -1);
          const scale = 0.9 + 0.4 * Math.sin(eased * Math.PI);
          p.style.transform = `translateY(${y}vh) rotate(${rotation}deg) scale(${scale})`;
          p.style.opacity = String(1 - eased*0.85);
          requestAnimationFrame(tick);
        }
        requestAnimationFrame(tick);
      }
      // spawn petals at interval but keep a cap
      let petalInterval = setInterval(() => {
        // avoid overcrowding on very small devices
        if (document.querySelectorAll('.petal').length < 28) createPetal();
      }, 380);

      // typing effect (safe and cancellable)
      function typeText(text, target, speed=60){
        target.textContent = '';
        let i = 0;
        let cancelled = false;
        function step(){
          if (cancelled) return;
          if (i < text.length){
            target.textContent += text.charAt(i++);
            setTimeout(step, speed);
          }
        }
        step();
        return ()=>{ cancelled=true; }; // return cancel function
      }

      // Yes button behaviour
      yesBtn.addEventListener('click', function onYes(e){
        // hide question area
        question.classList.add('hidden');
        document.querySelector('.buttons').classList.add('hidden');
        response.classList.remove('hidden');

        // gentle background change for the whole page
        document.body.style.background = 'linear-gradient(135deg,#ffd6e0,#ffe9ec)';

        // play music - browsers allow play after user gesture
        if (audio){
          audio.currentTime = 0;
          const p = audio.play();
          if (p && p.catch) p.catch(()=>{/* ignore autoplay rejection fallback */});
        }

        // typing message
        const text = "You’ve just given me the most beautiful gift — your heart. I promise to cherish it forever. 💕";
        typeText(text, loveMsg, 45);

        // remove yes listener so double clicks don't retrigger any logic
        yesBtn.removeEventListener('click', onYes);
      }, { once: true });

      // Helpers to move the "No" button randomly inside the card
      function randomizeNoButton(){
        // get the inner available area of the card (content box)
        const paddingLeft = parseFloat(getComputedStyle(card).paddingLeft) || 0;
        const paddingTop  = parseFloat(getComputedStyle(card).paddingTop) || 0;
        const innerWidth  = card.clientWidth - paddingLeft*2;
        const innerHeight = card.clientHeight - paddingTop*2;

        // ensure positive max values
        const maxX = Math.max(0, innerWidth - noBtn.offsetWidth);
        const maxY = Math.max(0, innerHeight - noBtn.offsetHeight);

        const x = Math.floor(Math.ra
