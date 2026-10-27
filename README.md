```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Azam Weds Phuljadi - Animated Web Invitation</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- QRCode.js Library -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <!-- Canvas Confetti Library -->
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
  <!-- Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400&family=Cormorant+Garamond:ital,wght@0,500;0,600;0,700;1,400&family=Great+Vibes&family=Montserrat:wght@300;400;500;600;700&family=Rozha+One&display=swap" rel="stylesheet">
  
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            blush: '#fff0f3',
            softpink: '#ffd6e0',
            rosegold: '#c97a7e',
            maroon: '#6b0f24',
            deepmaroon: '#4a0817',
            gold: '#d4af37',
            cream: '#fffdf9'
          },
          fontFamily: {
            serif: ['"Cormorant Garamond"', 'serif'],
            script: ['"Great Vibes"', 'cursive'],
            sans: ['"Montserrat"', 'sans-serif'],
            arabic: ['"Amiri"', 'serif'],
            hindi: ['"Rozha One"', 'serif']
          }
        }
      }
    }
  </script>

  <style>
    /* Custom Scrollbar */
    ::-webkit-scrollbar { width: 6px; }
    ::-webkit-scrollbar-track { background: #fff0f3; }
    ::-webkit-scrollbar-thumb { background: #f472b6; border-radius: 9999px; }

    /* Ambient Canvas */
    #particle-canvas {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      pointer-events: none;
      z-index: 1;
    }

    /* 3D Envelope Transformations */
    .perspective-1000 { perspective: 1000px; }
    .preserve-3d { transform-style: preserve-3d; }
    
    .envelope-flap {
      transform-origin: top;
      transition: transform 0.8s cubic-bezier(0.4, 0, 0.2, 1);
      z-index: 30;
    }

    .envelope-flap.open {
      transform: rotateX(180deg);
      z-index: 10;
    }

    /* Glassmorphism styling */
    .glass-card {
      background: rgba(255, 253, 250, 0.95);
      backdrop-filter: blur(14px);
      -webkit-backdrop-filter: blur(14px);
      border: 1px solid rgba(212, 175, 55, 0.35);
    }

    /* Metallic Gold Shimmer Gradient */
    .gold-gradient-text {
      background: linear-gradient(135deg, #bf953f 0%, #fcf6ba 25%, #b38728 50%, #fbf5b7 75%, #aa771c 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .gold-gradient-bg {
      background: linear-gradient(135deg, #d4af37 0%, #fef08a 50%, #b38728 100%);
    }

    .rose-gradient-bg {
      background: linear-gradient(135deg, #fda4af 0%, #f43f5e 50%, #6b0f24 100%);
    }

    /* Floating Heart Effect */
    .floating-heart {
      position: fixed;
      pointer-events: none;
      animation: riseAndFade 2.2s ease-out forwards;
      z-index: 999;
    }

    @keyframes riseAndFade {
      0% {
        opacity: 1;
        transform: translateY(0) scale(0.8) rotate(0deg);
      }
      100% {
        opacity: 0;
        transform: translateY(-150px) scale(1.6) rotate(20deg);
      }
    }

    /* Sound Visualizer Wave Animation */
    .eq-bar {
      width: 3px;
      height: 12px;
      background-color: #6b0f24;
      border-radius: 9999px;
      animation: soundWave 1.2s ease-in-out infinite alternate;
    }
    .eq-bar:nth-child(1) { animation-delay: 0.1s; }
    .eq-bar:nth-child(2) { animation-delay: 0.3s; }
    .eq-bar:nth-child(3) { animation-delay: 0.2s; }
    .eq-bar:nth-child(4) { animation-delay: 0.4s; }

    @keyframes soundWave {
      0% { height: 4px; }
      100% { height: 18px; }
    }

    /* Pulse Glow Animation */
    @keyframes pulseGlow {
      0%, 100% { box-shadow: 0 0 15px rgba(212, 175, 55, 0.5); }
      50% { box-shadow: 0 0 35px rgba(244, 63, 94, 0.8); }
    }
    .pulse-glow { animation: pulseGlow 2.5s infinite; }
  </style>
</head>
<body class="bg-gradient-to-br from-pink-50 via-rose-50 to-amber-50 text-gray-800 font-sans min-h-screen relative overflow-x-hidden">

  <!-- Ambient Particle Canvas -->
  <canvas id="particle-canvas"></canvas>

  <!-- Sound Toggle Floating Button with Visualizer -->
  <div class="fixed top-5 right-5 z-50 flex items-center gap-2">
    <button id="audio-toggle" title="Toggle Wedding Music" class="bg-white/95 hover:bg-white backdrop-blur-md text-maroon p-3.5 rounded-full shadow-xl border border-pink-200 transition-all transform hover:scale-110 active:scale-95 flex items-center justify-center gap-2">
      <i id="audio-icon" class="fa-solid fa-music text-base"></i>
      <div id="sound-wave-bars" class="hidden flex items-end gap-0.5 h-4">
        <div class="eq-bar"></div>
        <div class="eq-bar"></div>
        <div class="eq-bar"></div>
        <div class="eq-bar"></div>
      </div>
    </button>
  </div>

  <!-- Toast Notification -->
  <div id="toast-notification" class="fixed top-5 left-1/2 -translate-x-1/2 z-50 bg-maroon text-amber-100 text-xs sm:text-sm font-semibold px-6 py-3 rounded-full shadow-2xl border border-amber-300 transition-all duration-300 transform -translate-y-20 opacity-0 flex items-center gap-2 pointer-events-none">
    <i class="fa-solid fa-circle-check text-amber-300"></i>
    <span id="toast-message">Link copied to clipboard!</span>
  </div>

  <!-- QR CODE ENLARGE MODAL OVERLAY -->
  <div id="qr-modal" class="fixed inset-0 z-50 bg-black/70 backdrop-blur-sm hidden flex items-center justify-center p-4 opacity-0 transition-opacity duration-300">
    <div class="bg-white rounded-3xl p-6 max-w-sm w-full text-center relative shadow-2xl border-2 border-amber-300 transform scale-95 transition-transform duration-300" id="qr-modal-card">
      <button id="close-qr-modal" class="absolute top-4 right-4 text-gray-400 hover:text-maroon text-xl">
        <i class="fa-solid fa-xmark"></i>
      </button>

      <span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-pink-100 text-maroon text-[10px] font-bold tracking-widest uppercase mb-2">
        <i class="fa-solid fa-qrcode text-xs"></i> Scan Invitation Link
      </span>

      <h3 class="font-serif text-2xl font-bold text-gray-900">Azam <span class="font-script text-3xl text-rosegold">&</span> Phuljadi</h3>
      <p class="font-hindi text-xs text-maroon font-semibold mb-4">अज़म संग फुलझड़ी</p>

      <!-- Large QR Code -->
      <div class="p-4 bg-white rounded-2xl border-2 border-pink-200 shadow-md inline-block relative my-2">
        <div id="qrcode-large" class="flex justify-center items-center"></div>
        <div class="absolute inset-0 m-auto w-10 h-10 bg-cream rounded-full border border-amber-400 shadow flex items-center justify-center text-maroon text-base pointer-events-none">
          <i class="fa-solid fa-heart animate-pulse"></i>
        </div>
      </div>

      <p class="text-xs text-gray-600 mt-3 font-medium">Point phone camera to scan and open this interactive card live on web!</p>

      <button id="modal-copy-btn" class="mt-4 w-full rose-gradient-bg text-white font-bold py-2.5 rounded-xl text-xs uppercase tracking-wider shadow active:scale-95 transition-all flex items-center justify-center gap-2">
        <i class="fa-solid fa-copy"></i> Copy Direct Link
      </button>
    </div>
  </div>

  <!-- Main Container -->
  <main class="min-h-screen flex flex-col items-center justify-center px-4 py-8 relative z-10">

    <!-- FRONT LANDING SCREEN WITH ENVELOPE & QUICK SHARE -->
    <div id="front-landing-screen" class="w-full max-w-md flex flex-col items-center gap-6 my-auto transition-all duration-700">
      
      <!-- Quick Share & QR Card -->
      <div class="w-full bg-white/95 backdrop-blur-md rounded-3xl p-5 shadow-2xl border-2 border-amber-300/80 text-center flex flex-col items-center relative overflow-hidden">
        
        <span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-pink-100/90 text-maroon text-[10px] font-bold tracking-widest uppercase mb-1 border border-pink-200">
          <i class="fa-solid fa-sparkles text-amber-500"></i> Digital Invitation Card
        </span>

        <h2 class="font-serif text-2xl font-bold text-gray-900">Azam <span class="font-script text-3xl text-rosegold">&</span> Phuljadi</h2>
        <p class="font-hindi text-xs text-maroon font-semibold">अज़म संग फुलझड़ी</p>

        <!-- Scannable QR Code -->
        <div id="open-qr-modal-trigger" class="my-3 p-3 bg-white rounded-2xl border-2 border-pink-200 shadow-md relative cursor-pointer hover:scale-105 transition-transform group" title="Click to enlarge QR Code">
          <div id="qrcode-front" class="flex justify-center items-center"></div>
          <div class="absolute inset-0 m-auto w-8 h-8 bg-cream rounded-full border border-amber-400 shadow flex items-center justify-center text-maroon text-xs">
            <i class="fa-solid fa-heart animate-pulse"></i>
          </div>
          <div class="absolute inset-0 bg-maroon/10 opacity-0 group-hover:opacity-100 rounded-2xl transition-opacity flex items-center justify-center">
            <span class="bg-maroon text-white text-[10px] px-2 py-1 rounded font-bold shadow"><i class="fa-solid fa-expand"></i> Tap to Enlarge</span>
          </div>
        </div>

        <!-- Working Share Action Buttons -->
        <div class="flex gap-2 w-full">
          <button id="copy-link-btn" class="flex-1 text-xs bg-pink-50 hover:bg-pink-100 text-maroon font-bold py-2.5 px-3 rounded-full border border-pink-200 transition-all flex items-center justify-center gap-1.5 active:scale-95 shadow-sm">
            <i class="fa-solid fa-copy text-rose-600"></i> Copy Link
          </button>
          
          <button id="share-whatsapp-btn" class="flex-1 text-xs bg-emerald-500 hover:bg-emerald-600 text-white font-bold py-2.5 px-3 rounded-full border border-emerald-400 transition-all flex items-center justify-center gap-1.5 shadow-md active:scale-95">
            <i class="fa-brands fa-whatsapp text-sm"></i> WhatsApp Share
          </button>
        </div>
      </div>

      <!-- 3D LUXE MAROON & GOLD ENVELOPE -->
      <div id="envelope-container" class="w-full perspective-1000 cursor-pointer transition-all duration-500 transform hover:scale-[1.02]">
        <div class="relative w-full aspect-[4/3] bg-gradient-to-br from-maroon via-deepmaroon to-maroon rounded-3xl shadow-2xl preserve-3d border-2 border-amber-300/80 p-1">
          
          <div class="absolute inset-0 bg-[radial-gradient(#d4af37_1px,transparent_1px)] [background-size:12px_12px] opacity-25 rounded-3xl"></div>

          <!-- Card Inner Preview -->
          <div class="absolute inset-x-5 top-5 bottom-5 bg-cream rounded-2xl p-4 shadow-inner flex flex-col justify-center items-center text-center border border-amber-200">
            <p class="font-arabic text-amber-800 font-bold text-xl">بِسْمِ ٱللَّٰهِ ٱلرَّحْمَٰنِ ٱلرَّحِيمِ</p>
            <p class="font-script text-3xl text-maroon mt-1">Azam & Phuljadi</p>
            <p class="font-hindi text-xs text-amber-900 font-semibold mt-0.5">शुभ विवाह एवं निकाह निमंत्रण</p>
            <p class="text-[10px] uppercase tracking-widest text-gray-500 font-medium mt-1">Sunday, 22 November 2026</p>
          </div>

          <!-- Top Envelope Flap -->
          <div id="envelope-flap" class="envelope-flap absolute top-0 inset-x-0 h-1/2 bg-gradient-to-b from-deepmaroon to-maroon rounded-t-3xl border-t-2 border-amber-300/80 flex items-end justify-center pb-2 shadow-xl">
            <div class="w-full h-full border-b-2 border-amber-300/40 rounded-b-full opacity-60"></div>
          </div>

          <!-- Wax Seal -->
          <div id="wax-seal" class="absolute inset-0 m-auto w-20 h-20 bg-gradient-to-tr from-amber-600 via-yellow-400 to-amber-300 rounded-full shadow-2xl flex flex-col items-center justify-center border-2 border-yellow-100 z-30 transition-all duration-500 hover:scale-110 pulse-glow">
            <div class="w-16 h-16 rounded-full border border-maroon/40 flex flex-col items-center justify-center text-maroon">
              <i class="fa-solid fa-heart text-maroon text-sm"></i>
              <span class="font-script text-sm font-bold leading-tight">A & P</span>
              <span class="text-[8px] uppercase tracking-tighter font-bold">Tap To Open</span>
            </div>
          </div>

        </div>

        <p class="text-center text-maroon font-bold text-xs mt-3 animate-bounce flex items-center justify-center gap-1.5">
          <i class="fa-solid fa-hand-pointer"></i> Tap wax seal or envelope to open card & play sound
        </p>
      </div>

    </div>

    <!-- FULL MAIN INVITATION CARD CONTENT -->
    <div id="full-card-section" class="w-full max-w-2xl hidden opacity-0 transition-all duration-1000 transform translate-y-10">
      
      <!-- Card Container -->
      <div class="glass-card rounded-3xl p-6 sm:p-10 shadow-2xl relative overflow-hidden border-2 border-amber-300/70 my-4">
        
        <!-- Corner Ornaments -->
        <div class="absolute top-0 left-0 w-16 h-16 border-t-4 border-l-4 border-amber-400/70 rounded-tl-3xl pointer-events-none"></div>
        <div class="absolute top-0 right-0 w-16 h-16 border-t-4 border-r-4 border-amber-400/70 rounded-tr-3xl pointer-events-none"></div>
        <div class="absolute bottom-0 left-0 w-16 h-16 border-b-4 border-l-4 border-amber-400/70 rounded-bl-3xl pointer-events-none"></div>
        <div class="absolute bottom-0 right-0 w-16 h-16 border-b-4 border-r-4 border-amber-400/70 rounded-br-3xl pointer-events-none"></div>

        <!-- Calligraphy Header -->
        <div class="text-center mb-6">
          <div class="inline-block px-5 py-1.5 rounded-full bg-pink-50 border border-amber-300 shadow-sm mb-2">
            <p class="font-arabic text-amber-800 text-2xl font-bold tracking-widest">بِسْمِ ٱللَّٰهِ ٱلرَّحْمَٰنِ ٱلرَّحِيمِ</p>
          </div>
          <p class="text-[11px] uppercase tracking-widest text-maroon font-bold">In The Name Of Allah, The Most Gracious, The Most Merciful</p>
        </div>

        <!-- Couple Names Header -->
        <div class="text-center space-y-3">
          <span class="text-xs sm:text-sm uppercase tracking-[0.25em] text-amber-900 font-bold bg-amber-100/70 px-4 py-1.5 rounded-full border border-amber-300 shadow-sm">
            Nikah & Walima Celebration
          </span>

          <h1 class="font-script text-5xl sm:text-7xl gold-gradient-text py-1 drop-shadow-sm">
            Azam <span class="text-maroon font-serif text-3xl sm:text-4xl">&</span> Phuljadi
          </h1>

          <div class="flex items-center justify-center gap-3 text-maroon font-semibold">
            <span class="font-hindi text-lg sm:text-xl">अज़म संग फुलझड़ी</span>
            <span class="text-amber-500">•</span>
            <span class="font-arabic text-lg sm:text-xl" dir="rtl">ﻋﻈﻢ ﻭ ﭘﮭﻠﺠﮭﮍﯼ</span>
          </div>

          <p class="text-xs sm:text-sm text-gray-600 italic max-w-md mx-auto leading-relaxed pt-2">
            "And We created you in pairs." <br>
            <span class="text-[11px] text-gray-400 font-normal">[Surah An-Naba 78:8]</span>
          </p>
          <p class="text-xs sm:text-sm text-maroon font-medium">Cordially invite you to grace the auspicious occasion of their wedding ceremony.</p>
        </div>

        <!-- Schedule & Event Details Grid -->
        <div class="my-8 grid grid-cols-1 md:grid-cols-2 gap-4">
          
          <!-- Date & Time Card -->
          <div class="bg-white/90 rounded-2xl p-4 border border-rose-200 shadow-sm hover:border-amber-400 transition-colors">
            <div class="flex items-center space-x-3 mb-3">
              <div class="w-10 h-10 rounded-xl gold-gradient-bg flex items-center justify-center text-maroon font-bold shadow">
                <i class="fa-solid fa-calendar-check text-maroon"></i>
              </div>
              <div>
                <p class="text-[10px] text-maroon font-bold uppercase tracking-wider">Date & Day</p>
                <p class="text-sm font-bold text-gray-900">Sunday, 22 November 2026</p>
              </div>
            </div>

            <hr class="border-rose-100 my-2">

            <!-- Timings List -->
            <div class="space-y-2 text-xs text-gray-700">
              <div class="flex justify-between items-center">
                <span class="font-medium flex items-center gap-1.5 text-maroon">
                  <i class="fa-solid fa-users text-amber-600"></i> Barat Arrival:
                </span>
                <span class="font-bold bg-pink-50 px-2 py-0.5 rounded border border-pink-100">6:30 PM</span>
              </div>
              <div class="flex justify-between items-center">
                <span class="font-medium flex items-center gap-1.5 text-maroon">
                  <i class="fa-solid fa-handshake text-amber-600"></i> Nikah Ceremony:
                </span>
                <span class="font-bold bg-pink-50 px-2 py-0.5 rounded border border-pink-100">8:00 PM</span>
              </div>
              <div class="flex justify-between items-center">
                <span class="font-medium flex items-center gap-1.5 text-maroon">
                  <i class="fa-solid fa-utensils text-amber-600"></i> Dinner & Walima:
                </span>
                <span class="font-bold bg-pink-50 px-2 py-0.5 rounded border border-pink-100">9:00 PM</span>
              </div>
            </div>
          </div>

          <!-- Venue & Address Card -->
          <div class="bg-white/90 rounded-2xl p-4 border border-rose-200 shadow-sm hover:border-amber-400 transition-colors flex flex-col justify-between">
            <div>
              <div class="flex items-center space-x-3 mb-2">
                <div class="w-10 h-10 rounded-xl gold-gradient-bg flex items-center justify-center text-maroon font-bold shadow">
                  <i class="fa-solid fa-location-dot text-maroon"></i>
                </div>
                <div>
                  <p class="text-[10px] text-maroon font-bold uppercase tracking-wider">Venue Location</p>
                  <p class="text-sm font-bold text-gray-900">Tuba Farm House</p>
                </div>
              </div>

              <hr class="border-rose-100 my-2">

              <p class="text-xs text-gray-600 leading-relaxed">
                📍 <strong>Address:</strong> Chaar Number Chowki, Main Road, Sahaswan, District Badaun, Uttar Pradesh
              </p>
            </div>

            <button id="get-directions-btn" class="mt-4 w-full bg-rose-50 hover:bg-rose-100 text-maroon border border-pink-200 py-2.5 rounded-xl text-xs font-bold tracking-wider uppercase transition-all flex items-center justify-center gap-2 active:scale-95">
              <i class="fa-solid fa-map-location-dot text-rose-600 text-sm"></i> Open In Google Maps
            </button>
          </div>

        </div>

        <!-- Live Countdown Timer -->
        <div class="bg-gradient-to-r from-maroon via-deepmaroon to-maroon text-white rounded-2xl p-5 shadow-lg mb-8 text-center border border-amber-300/40">
          <p class="text-xs uppercase tracking-widest text-amber-200 font-semibold mb-3">
            <i class="fa-solid fa-clock mr-1"></i> Countdown To The Wedding Day
          </p>
          <div class="grid grid-cols-4 gap-2 max-w-xs mx-auto text-center">
            <div class="bg-black/30 p-2 rounded-xl border border-amber-400/20">
              <span id="count-days" class="text-xl sm:text-2xl font-bold font-serif text-amber-200">00</span>
              <p class="text-[10px] uppercase tracking-wider text-rose-200">Days</p>
            </div>
            <div class="bg-black/30 p-2 rounded-xl border border-amber-400/20">
              <span id="count-hours" class="text-xl sm:text-2xl font-bold font-serif text-amber-200">00</span>
              <p class="text-[10px] uppercase tracking-wider text-rose-200">Hours</p>
            </div>
            <div class="bg-black/30 p-2 rounded-xl border border-amber-400/20">
              <span id="count-mins" class="text-xl sm:text-2xl font-bold font-serif text-amber-200">00</span>
              <p class="text-[10px] uppercase tracking-wider text-rose-200">Mins</p>
            </div>
            <div class="bg-black/30 p-2 rounded-xl border border-amber-400/20">
              <span id="count-secs" class="text-xl sm:text-2xl font-bold font-serif text-amber-200">00</span>
              <p class="text-[10px] uppercase tracking-wider text-rose-200">Secs</p>
            </div>
          </div>
        </div>

        <!-- Quick Calendar & Share Actions -->
        <div class="flex flex-wrap gap-3 justify-center mb-8">
          <button id="add-calendar-btn" class="bg-white text-maroon border border-pink-200 hover:border-amber-400 px-5 py-2.5 rounded-full text-xs font-bold tracking-wider uppercase shadow-sm hover:shadow transition-all flex items-center gap-2 active:scale-95">
            <i class="fa-solid fa-calendar-plus text-rose-600"></i> Add To Google Calendar
          </button>
          
          <button id="card-whatsapp-share" class="bg-emerald-500 hover:bg-emerald-600 text-white px-5 py-2.5 rounded-full text-xs font-bold tracking-wider uppercase shadow hover:shadow-md transition-all flex items-center gap-2 active:scale-95">
            <i class="fa-brands fa-whatsapp text-sm"></i> Share Card On WhatsApp
          </button>
        </div>

        <!-- RSVP Form Section -->
        <div class="bg-rose-50/80 rounded-2xl p-5 sm:p-7 border border-rose-200/90 relative">
          <h3 class="font-serif text-2xl text-maroon font-bold text-center mb-1">RSVP & Blessings</h3>
          <p class="text-xs text-gray-600 text-center mb-5">Will you join us in celebrating our special day?</p>

          <form id="rsvp-form" class="space-y-4">
            <div>
              <label class="block text-xs font-bold uppercase text-gray-700 mb-1">Your Name (आपका नाम)</label>
              <input type="text" id="rsvp-name" required placeholder="e.g. Mohd Rashid Khan" class="w-full px-4 py-2.5 rounded-xl border border-rose-200 focus:outline-none focus:ring-2 focus:ring-rose-400 text-sm bg-white">
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div>
                <label class="block text-xs font-bold uppercase text-gray-700 mb-1">Total Guests</label>
                <select id="rsvp-guests" class="w-full px-4 py-2.5 rounded-xl border border-rose-200 focus:outline-none focus:ring-2 focus:ring-rose-400 text-sm bg-white">
                  <option value="1">1 Person</option>
                  <option value="2">2 Persons</option>
                  <option value="3">3 Persons</option>
                  <option value="4+">4+ Family Members</option>
                </select>
              </div>

              <div>
                <label class="block text-xs font-bold uppercase text-gray-700 mb-1">Attendance Status</label>
                <select id="rsvp-status" class="w-full px-4 py-2.5 rounded-xl border border-rose-200 focus:outline-none focus:ring-2 focus:ring-rose-400 text-sm bg-white">
                  <option value="yes">Joyfully Accept</option>
                  <option value="no">Regretfully Decline</option>
                </select>
              </div>
            </div>

            <div>
              <label class="block text-xs font-bold uppercase text-gray-700 mb-1">Best Wishes & Dua (शुभकामनाएं)</label>
              <textarea id="rsvp-message" rows="2" placeholder="Write your heartfelt message..." class="w-full px-4 py-2.5 rounded-xl border border-rose-200 focus:outline-none focus:ring-2 focus:ring-rose-400 text-sm bg-white"></textarea>
            </div>

            <button type="submit" class="w-full rose-gradient-bg text-white font-bold py-3 px-6 rounded-xl shadow-lg hover:opacity-95 transition-all text-sm tracking-wider uppercase flex items-center justify-center gap-2 active:scale-95">
              <i class="fa-solid fa-paper-plane"></i> Send RSVP & Blessings
            </button>
          </form>

          <!-- RSVP Confirmation -->
          <div id="rsvp-success" class="hidden text-center py-6 space-y-3">
            <div class="w-14 h-14 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto text-2xl">
              <i class="fa-solid fa-check"></i>
            </div>
            <h4 class="font-serif text-xl font-bold text-gray-800">JazakAllah Khair / धन्यवाद!</h4>
            <p id="rsvp-success-msg" class="text-xs text-gray-600 max-w-xs mx-auto">Your confirmation and warm blessings have been recorded.</p>
          </div>
        </div>

        <!-- Footer / Compliments -->
        <div class="mt-8 text-center border-t border-rose-200/80 pt-6 space-y-2">
          <p class="text-xs uppercase tracking-widest text-maroon font-bold">With Compliments / दर्शनाभिलाषी</p>
          <p class="font-serif text-lg text-gray-800 font-bold">Family & Friends (परिवार एवं मित्रगण)</p>
          <p class="text-xs text-gray-500 italic">Khan & Siddiqui Family, Sahaswan</p>
          <p class="text-[10px] text-gray-400 mt-4">Designed for Azam & Phuljadi Wedding • 22 Nov 2026</p>
        </div>

      </div>
    </div>

  </main>

  <script>
    /* ==========================================================================
       1. AMBIENT MUSIC & SOUND SYNTHESIZER (Web Audio API)
       ========================================================================== */
    let audioCtx = null;
    let isPlaying = false;
    let synthInterval = null;
    
    // Smooth pentatonic romantic melody notes (Hz)
    const melodyNotes = [
      261.63, 329.63, 392.00, 440.00, 523.25, 659.25,
      523.25, 440.00, 392.00, 329.63, 293.66, 329.63
    ]; 

    function initAudioContext() {
      if (!audioCtx) {
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      }
      if (audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
    }

    // Sound effect generator for interaction (Chime/Bell)
    function playChimeSound() {
      try {
        initAudioContext();
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(523.25, audioCtx.currentTime); // C5
        osc.frequency.exponentialRampToValueAtTime(1046.50, audioCtx.currentTime + 0.5); // C6

        gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.8);

        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.8);
      } catch(e) {}
    }

    // Envelope opening harp swipe sound effect
    function playEnvelopeOpenSound() {
      try {
        initAudioContext();
        const harpNotes = [261.63, 329.63, 392.00, 523.25, 659.25, 783.99, 1046.50];
        harpNotes.forEach((freq, index) => {
          setTimeout(() => {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(freq, audioCtx.currentTime);

            gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.6);

            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.6);
          }, index * 70);
        });
      } catch(e) {}
    }

    // Continuous Romantic Ambient Music Loop
    function startWeddingMusic() {
      try {
        initAudioContext();
        if (isPlaying) return;
        isPlaying = true;
        
        document.getElementById('sound-wave-bars').classList.remove('hidden');
        document.getElementById('sound-wave-bars').classList.add('flex');
        document.getElementById('audio-icon').className = 'fa-solid fa-volume-high text-maroon';

        let step = 0;
        synthInterval = setInterval(() => {
          if (!isPlaying) return;
          playSoftMelodyTone(melodyNotes[step % melodyNotes.length]);
          step++;
        }, 1100);
      } catch(e) {}
    }

    function playSoftMelodyTone(freq) {
      if (!audioCtx) return;
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();

      osc.type = 'sine';
      osc.frequency.setValueAtTime(freq, audioCtx.currentTime);

      gain.gain.setValueAtTime(0.001, audioCtx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.045, audioCtx.currentTime + 0.35);
      gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 1.9);

      osc.connect(gain);
      gain.connect(audioCtx.destination);

      osc.start();
      osc.stop(audioCtx.currentTime + 2.0);
    }

    function stopWeddingMusic() {
      isPlaying = false;
      if (synthInterval) clearInterval(synthInterval);
      document.getElementById('sound-wave-bars').classList.add('hidden');
      document.getElementById('sound-wave-bars').classList.remove('flex');
      document.getElementById('audio-icon').className = 'fa-solid fa-volume-xmark text-gray-400';
    }

    document.getElementById('audio-toggle').addEventListener('click', () => {
      if (!isPlaying) {
        startWeddingMusic();
        playChimeSound();
      } else {
        stopWeddingMusic();
      }
    });

    /* ==========================================================================
       2. REAL QR CODE GENERATOR & RELIABLE WHATSAPP SHARE
       ========================================================================== */
    function getCardUrl() {
      return window.location.href;
    }

    function generateQRCodes() {
      const url = getCardUrl();

      document.getElementById('qrcode-front').innerHTML = '';
      document.getElementById('qrcode-large').innerHTML = '';

      new QRCode(document.getElementById('qrcode-front'), {
        text: url,
        width: 120,
        height: 120,
        colorDark: "#6b0f24",
        colorLight: "#ffffff",
        correctLevel: QRCode.CorrectLevel.H
      });

      new QRCode(document.getElementById('qrcode-large'), {
        text: url,
        width: 200,
        height: 200,
        colorDark: "#6b0f24",
        colorLight: "#ffffff",
        correctLevel: QRCode.CorrectLevel.H
      });
    }

    function copyCardLink() {
      playChimeSound();
      const url = getCardUrl();
      if (navigator.clipboard && window.isSecureContext) {
        navigator.clipboard.writeText(url).then(showToast).catch(() => fallbackCopy(url));
      } else {
        fallbackCopy(url);
      }
    }

    function fallbackCopy(text) {
      const textArea = document.createElement("textarea");
      textArea.value = text;
      textArea.style.position = "fixed";
      textArea.style.left = "-999999px";
      document.body.appendChild(textArea);
      textArea.focus();
      textArea.select();
      try {
        document.execCommand('copy');
        showToast();
      } catch (err) {
        alert("Invitation Link: " + text);
      }
      document.body.removeChild(textArea);
    }

    function showToast(msg = "Invitation link copied to clipboard!") {
      const toast = document.getElementById('toast-notification');
      document.getElementById('toast-message').innerText = msg;
      toast.classList.remove('-translate-y-20', 'opacity-0');
      toast.classList.add('translate-y-0', 'opacity-100');
      setTimeout(() => {
        toast.classList.remove('translate-y-0', 'opacity-100');
        toast.classList.add('-translate-y-20', 'opacity-0');
      }, 3000);
    }

    function shareToWhatsApp() {
      playChimeSound();
      const cardUrl = getCardUrl();
      const message = `🌸 *Wedding Invitation: Azam Weds Phuljadi* 🌸\n\nYou are cordially invited to celebrate the Nikah & Walima of Azam and Phuljadi on Sunday, 22 November 2026 at Tuba Farm House, Sahaswan.\n\nOpen interactive card live on your browser:\n${cardUrl}`;
      const encodedMsg = encodeURIComponent(message);
      
      const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);
      
      if (isMobile) {
        window.location.href = `whatsapp://send?text=${encodedMsg}`;
      } else {
        window.open(`https://api.whatsapp.com/send?text=${encodedMsg}`, '_blank');
      }
    }

    document.getElementById('copy-link-btn').addEventListener('click', copyCardLink);
    document.getElementById('modal-copy-btn').addEventListener('click', copyCardLink);
    document.getElementById('share-whatsapp-btn').addEventListener('click', shareToWhatsApp);
    document.getElementById('card-whatsapp-share').addEventListener('click', shareToWhatsApp);

    // QR Modal controls
    const qrModal = document.getElementById('qr-modal');
    const qrModalCard = document.getElementById('qr-modal-card');
    
    document.getElementById('open-qr-modal-trigger').addEventListener('click', (e) => {
      e.stopPropagation();
      playChimeSound();
      qrModal.classList.remove('hidden');
      setTimeout(() => {
        qrModal.classList.remove('opacity-0');
        qrModalCard.classList.remove('scale-95');
        qrModalCard.classList.add('scale-100');
      }, 10);
    });

    function closeQRModal() {
      qrModal.classList.add('opacity-0');
      qrModalCard.classList.remove('scale-100');
      qrModalCard.classList.add('scale-95');
      setTimeout(() => {
        qrModal.classList.add('hidden');
      }, 300);
    }

    document.getElementById('close-qr-modal').addEventListener('click', closeQRModal);
    qrModal.addEventListener('click', (e) => {
      if (e.target === qrModal) closeQRModal();
    });

    window.addEventListener('load', generateQRCodes);

    /* ==========================================================================
       3. 3D ENVELOPE OPENING ANIMATION
       ========================================================================== */
    const frontLandingScreen = document.getElementById('front-landing-screen');
    const envelopeContainer = document.getElementById('envelope-container');
    const envelopeFlap = document.getElementById('envelope-flap');
    const waxSeal = document.getElementById('wax-seal');
    const fullCardSection = document.getElementById('full-card-section');

    envelopeContainer.addEventListener('click', openInvitation);

    function openInvitation() {
      playEnvelopeOpenSound();
      startWeddingMusic();

      waxSeal.style.transform = 'scale(0) rotate(180deg)';
      waxSeal.style.opacity = '0';

      setTimeout(() => {
        envelopeFlap.classList.add('open');
      }, 200);

      setTimeout(() => {
        if (typeof confetti === 'function') {
          confetti({
            particleCount: 110,
            spread: 90,
            origin: { y: 0.6 },
            colors: ['#d4af37', '#6b0f24', '#ffd6e0', '#f43f5e']
          });
        }
      }, 450);

      setTimeout(() => {
        frontLandingScreen.style.opacity = '0';
        frontLandingScreen.style.transform = 'scale(0.85) translateY(-30px)';

        setTimeout(() => {
          frontLandingScreen.classList.add('hidden');
          fullCardSection.classList.remove('hidden');
          
          setTimeout(() => {
            fullCardSection.classList.remove('opacity-0', 'translate-y-10');
            fullCardSection.classList.add('opacity-100', 'translate-y-0');
          }, 50);

        }, 400);

      }, 750);
    }

    /* ==========================================================================
       4. COUNTDOWN TIMER TO 22 NOV 2026
       ========================================================================== */
    const eventDate = new Date('November 22, 2026 18:30:00').getTime();

    function updateCountdown() {
      const now = new Date().getTime();
      const difference = eventDate - now;

      if (difference > 0) {
        const days = Math.floor(difference / (1000 * 60 * 60 * 24));
        const hours = Math.floor((difference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
        const minutes = Math.floor((difference % (1000 * 60 * 60)) / (1000 * 60));
        const seconds = Math.floor((difference % (1000 * 60)) / 1000);

        document.getElementById('count-days').innerText = String(days).padStart(2, '0');
        document.getElementById('count-hours').innerText = String(hours).padStart(2, '0');
        document.getElementById('count-mins').innerText = String(minutes).padStart(2, '0');
        document.getElementById('count-secs').innerText = String(seconds).padStart(2, '0');
      } else {
        document.getElementById('count-days').innerText = '00';
        document.getElementById('count-hours').innerText = '00';
        document.getElementById('count-mins').innerText = '00';
        document.getElementById('count-secs').innerText = '00';
      }
    }

    setInterval(updateCountdown, 1000);
    updateCountdown();

    /* ==========================================================================
       5. RSVP SUBMISSION & FLOATING HEARTS
       ========================================================================== */
    const rsvpForm = document.getElementById('rsvp-form');
    const rsvpSuccess = document.getElementById('rsvp-success');

    rsvpForm.addEventListener('submit', (e) => {
      e.preventDefault();
      playChimeSound();
      
      const name = document.getElementById('rsvp-name').value;
      const guests = document.getElementById('rsvp-guests').value;
      
      for (let i = 0; i < 18; i++) {
        setTimeout(() => {
          createFloatingHeart(e.clientX || window.innerWidth / 2, e.clientY || window.innerHeight / 2);
        }, i * 90);
      }

      document.getElementById('rsvp-success-msg').innerText = `Thank you ${name}! Your RSVP for ${guests} guest(s) and warm blessings have been received.`;

      rsvpForm.classList.add('hidden');
      rsvpSuccess.classList.remove('hidden');
    });

    function createFloatingHeart(x, y) {
      const heart = document.createElement('div');
      heart.className = 'floating-heart text-maroon text-2xl';
      heart.innerHTML = '<i class="fa-solid fa-heart"></i>';
      heart.style.left = `${x + (Math.random() * 120 - 60)}px`;
      heart.style.top = `${y + (Math.random() * 60 - 30)}px`;

      document.body.appendChild(heart);
      setTimeout(() => heart.remove(), 2200);
    }

    /* ==========================================================================
       6. MAPS & CALENDAR UTILITIES
       ========================================================================== */
    document.getElementById('get-directions-btn').addEventListener('click', () => {
      playChimeSound();
      const mapsUrl = `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent('Tuba Farm House, Chaar Number Chowki, Sahaswan, Badaun, Uttar Pradesh')}`;
      window.open(mapsUrl, '_blank');
    });

    document.getElementById('add-calendar-btn').addEventListener('click', () => {
      playChimeSound();
      const title = "Nikah & Walima Celebration - Azam Weds Phuljadi";
      const details = "Nikah & Walima celebration of Azam and Phuljadi at Tuba Farm House, Sahaswan, Badaun, UP.";
      const location = "Tuba Farm House, Chaar Number Chowki, Sahaswan, Badaun, Uttar Pradesh";
      const startDate = "20261122T183000";
      const endDate = "20261122T233000";

      const googleCalUrl = `https://calendar.google.com/calendar/render?action=TEMPLATE&text=${encodeURIComponent(title)}&dates=${startDate}/${endDate}&details=${encodeURIComponent(details)}&location=${encodeURIComponent(location)}`;
      window.open(googleCalUrl, '_blank');
    });
  </script>
</body>
</html>
```
