# charming-symphony
a small fun game ; can be dangerously hard  !!

you can play my code using git pages or simply you can copy my code and play it !! it's a fun game in which you have to destroy nodes in sequence and you win thats it ... but when you copy this code plz. rename this file "<name>index.html to play while i will also include git pages link here 

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
    <title>Charming Symphony</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons (pinned version — unpkg isn't reachable once this page is published, jsdelivr is) -->
    <script src="https://cdn.jsdelivr.net/npm/lucide@0.460.0/dist/umd/lucide.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Caveat:wght@600;700&family=Cinzel:wght@600;800&family=Courier+Prime:wght@400;700&family=Orbitron:wght@600;900&family=VT323&display=swap');

        :root {
            color-scheme: dark;
            box-sizing: border-box;
            padding-top: env(safe-area-inset-top, 0px);
            padding-bottom: env(safe-area-inset-bottom, 0px);
        }
        *, *::before, *::after { box-sizing: inherit; }
        html { scroll-padding-top: env(safe-area-inset-top, 0px); }

        body {
            background-color: #0d0b09;
            color: #f3f4f6;
            overflow: hidden;
            touch-action: none;
            user-select: none;
            font-family: 'Courier Prime', monospace;
            margin: 0;
        }

        .font-handwritten { font-family: 'Caveat', cursive; }
        .font-stone { font-family: 'Courier Prime', monospace; }
        .font-medieval { font-family: 'Cinzel', serif; }
        .font-arcade { font-family: 'VT323', monospace; }
        .font-quantum { font-family: 'Orbitron', sans-serif; }

        .sketch-border {
            border-radius: 255px 15px 225px 15px/15px 225px 15px 255px;
        }

        @keyframes subtleWobble {
            0%, 100% { transform: rotate(-0.5deg) scale(1); }
            50% { transform: rotate(0.8deg) scale(1.01); }
        }
        .animate-wobble {
            animation: subtleWobble 4s ease-in-out infinite;
        }

        .scanlines {
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.3) 50%);
            background-size: 100% 4px;
            pointer-events: none;
        }

        .glitch-text {
            text-shadow: 2px 0 #f43f5e, -2px 0 #38bdf8;
        }

        @keyframes shake {
            10%, 90% { transform: translate3d(-1px, 0, 0); }
            20%, 80% { transform: translate3d(2px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-4px, 0, 0); }
            40%, 60% { transform: translate3d(4px, 0, 0); }
        }
        .shake {
            animation: shake 0.3s cubic-bezier(.36,.07,.19,.97) both;
        }
    </style>
</head>
<body class="flex flex-col items-center min-h-screen px-2 sm:px-4 py-2">
    <div class="w-full max-w-4xl flex flex-col items-center flex-grow">

        <!-- HEADER / ERA BANNER -->
        <header id="era-header" class="w-full flex justify-between items-center p-3 sm:p-4 rounded-3xl border transition-all duration-700 z-20 sketch-border shadow-lg bg-amber-950/40 border-amber-800/60 text-amber-200 mb-2">
            <div class="flex items-center gap-3">
                <div id="era-icon-box" class="p-2.5 rounded-2xl border sketch-border bg-amber-900/40 border-amber-700/50 text-amber-500">
                    <i id="era-icon" data-lucide="flame" class="w-6 h-6"></i>
                </div>
                <div class="text-left">
                    <h1 id="era-title" class="text-lg sm:text-2xl font-bold tracking-wide font-stone">Era 1: Stone Age Chalk</h1>
                    <p id="era-subtitle" class="text-[11px] sm:text-xs opacity-70 font-mono">Primitive hearth embers &amp; rough cave sketches</p>
                </div>
            </div>

            <!-- Right Quick Controls -->
            <div class="flex items-center space-x-2">
                <button id="btn-gyro" class="px-3 py-2 rounded-2xl bg-amber-900/50 hover:bg-amber-800/80 text-amber-200 border border-amber-700/50 text-xs font-bold flex items-center gap-1.5 transition sketch-border">
                    <i data-lucide="smartphone" class="w-4 h-4"></i>
                    <span id="gyro-status">Enable Tilt</span>
                </button>
                <button id="btn-devlog" class="p-2.5 rounded-2xl bg-amber-900/40 hover:bg-amber-800/60 text-amber-300 border border-amber-700/50 transition sketch-border" title="Dev Note">
                    <i data-lucide="book-open" class="w-5 h-5"></i>
                </button>
                <button id="btn-sound" class="p-2.5 rounded-2xl bg-amber-900/40 hover:bg-amber-800/60 text-amber-300 border border-amber-700/50 transition sketch-border" title="Toggle Sound">
                    <i data-lucide="volume-2" class="w-5 h-5"></i>
                </button>
            </div>
        </header>

        <!-- MAIN CANVAS CONTAINER -->
        <main class="relative w-full flex-grow flex items-center justify-center my-2">
            <div id="canvas-container" class="relative w-full h-full max-h-[75vh] aspect-[4/3] sm:aspect-[16/10] rounded-3xl overflow-hidden border-2 transition-all duration-700 border-amber-800/50 shadow-2xl flex items-center justify-center bg-stone-950 sketch-border">

                <canvas id="gameCanvas" class="w-full h-full"></canvas>

                <!-- CRT Filter for late futuristic eras -->
                <div id="crt-overlay" class="absolute inset-0 scanlines opacity-0 pointer-events-none transition-opacity duration-700"></div>

                <!-- IN-GAME HUD OVERLAY -->
                <div id="hud" class="absolute top-0 left-0 right-0 p-4 sm:p-6 flex justify-between items-start pointer-events-none hidden z-10">
                    <!-- Left: Time & Score -->
                    <div class="flex flex-col gap-2">
                        <div class="bg-black/60 backdrop-blur-md px-3 py-1.5 rounded-2xl border border-white/10 sketch-border flex items-center gap-2">
                            <div>
                                <div class="text-[10px] uppercase font-bold tracking-wider opacity-70">Chronos Gauge</div>
                                <div id="hud-timer" class="text-xl sm:text-2xl font-bold font-mono text-amber-400">35.0s</div>
                            </div>
                            <span id="hud-flux-tag" class="hidden text-[10px] px-2 py-0.5 rounded-full bg-cyan-500/20 text-cyan-300 border border-cyan-400/40 font-bold animate-pulse">FLUX SLOW</span>
                        </div>
                        <div class="bg-black/60 backdrop-blur-md px-3 py-1.5 rounded-2xl border border-white/10 sketch-border">
                            <div class="text-[10px] uppercase font-bold tracking-wider opacity-70">Odyssey Score</div>
                            <div id="hud-score" class="text-sm sm:text-base font-bold font-mono text-amber-200">0000</div>
                        </div>
                    </div>

                    <!-- Center Sequence Target HUD -->
                    <div id="hud-seq-panel" class="flex flex-col items-center bg-black/70 backdrop-blur-md px-4 py-2.5 rounded-2xl border border-white/20 sketch-border shadow-xl">
                        <span class="text-[10px] font-bold tracking-widest uppercase mb-1 opacity-70 text-amber-200" id="hud-seq-label">Touch In Order</span>
                        <div id="hud-sequence" class="flex gap-1.5 sm:gap-2 items-center flex-wrap justify-center max-w-[200px] sm:max-w-xs"></div>
                    </div>

                    <!-- Right: Era Step -->
                    <div class="text-right bg-black/60 backdrop-blur-md px-3 py-1.5 rounded-2xl border border-white/10 sketch-border">
                        <div class="text-[10px] uppercase font-bold tracking-wider opacity-70">Era Level</div>
                        <div id="hud-level" class="text-lg sm:text-xl font-black font-mono text-amber-400">01 / 10</div>
                    </div>
                </div>

                <!-- SCREEN 1: COZY DEV INTRO SCREEN -->
                <div id="screen-start" class="absolute inset-0 bg-stone-950/90 backdrop-blur-md flex flex-col items-center justify-center p-6 text-center z-20">
                    <div class="max-w-md w-full bg-amber-950/30 border-2 border-amber-800/60 p-6 sm:p-8 rounded-3xl sketch-border animate-wobble shadow-2xl relative">

                        <div class="inline-block p-4 rounded-full bg-amber-900/40 border border-amber-700/50 mb-3">
                            <i data-lucide="compass" class="w-10 h-10 text-amber-400"></i>
                        </div>

                        <h2 class="text-3xl sm:text-4xl font-bold font-handwritten text-amber-300 mb-1">
                            Charming Symphony
                        </h2>
                        <p class="text-xs text-amber-200/70 font-mono uppercase tracking-widest mb-4">10 Eras • Gravity &amp; Flux Mechanics</p>

                        <!-- Handwritten Developer Note Box -->
                        <div class="bg-amber-100/10 border border-amber-500/20 p-4 rounded-2xl text-left mb-6 text-amber-200/90 text-xs sm:text-sm leading-relaxed font-handwritten text-base sm:text-lg">
                            "3am update: finally got all 10 eras in. The black hole level nearly broke my brain (and my keyboard, a little). Gravity Wells will yank you off course, so duck into the blue Flux Zones when it gets hairy — they buy you breathing room. Plays best with tilt on if you're on mobile. Good luck out there."
                            <div class="text-right text-xs font-mono text-amber-400 mt-2">— Amogh, running on coffee and spite</div>
                        </div>

                        <button id="btn-start" class="w-full py-3.5 bg-gradient-to-r from-amber-700 via-amber-600 to-amber-700 hover:brightness-110 text-amber-100 font-bold tracking-wider rounded-2xl shadow-xl transition transform hover:scale-[1.02] flex items-center justify-center gap-2 sketch-border">
                            <i data-lucide="play" class="w-5 h-5"></i> BEGIN 10-ERA ODYSSEY
                        </button>
                    </div>
                </div>

                <!-- SCREEN 2: ERA CLEAR OVERLAY -->
                <div id="screen-win" class="absolute inset-0 bg-stone-950/90 backdrop-blur-md flex flex-col items-center justify-center p-6 text-center z-20 hidden">
                    <div class="max-w-md w-full bg-emerald-950/30 border-2 border-emerald-700/60 p-6 rounded-3xl sketch-border shadow-2xl">
                        <div class="p-4 rounded-full bg-emerald-900/40 border border-emerald-700/50 inline-block mb-3">
                            <i data-lucide="sparkles" class="w-10 h-10 text-emerald-400"></i>
                        </div>
                        <h2 id="win-title" class="text-2xl sm:text-3xl font-bold font-handwritten text-emerald-300 mb-1">
                            Era Complete!
                        </h2>
                        <p id="win-desc" class="text-xs text-slate-300 mb-5 font-mono">The particle stream evolves forward into history...</p>

                        <div class="bg-white/5 p-3 rounded-2xl border border-white/10 mb-6 inline-block w-48 sketch-border">
                            <div class="text-xs text-slate-400 uppercase font-bold">Total Score</div>
                            <div id="win-score" class="text-2xl font-black font-mono text-emerald-400">0000</div>
                        </div>

                        <button id="btn-next-level" class="w-full py-3 bg-gradient-to-r from-emerald-600 to-teal-600 hover:brightness-110 text-white font-bold tracking-wider rounded-2xl shadow-xl transition transform flex items-center justify-center gap-2 sketch-border">
                            <i data-lucide="arrow-right" class="w-5 h-5"></i> LEAP TO NEXT ERA
                        </button>
                    </div>
                </div>

                <!-- SCREEN 3: GAME OVER OVERLAY -->
                <div id="screen-gameover" class="absolute inset-0 bg-stone-950/95 backdrop-blur-md flex flex-col items-center justify-center p-6 text-center z-20 hidden">
                    <div class="max-w-md w-full bg-rose-950/30 border-2 border-rose-700/60 p-6 rounded-3xl sketch-border shadow-2xl">
                        <div class="p-4 rounded-full bg-rose-900/40 border border-rose-700/50 inline-block mb-3">
                            <i data-lucide="hourglass" class="w-10 h-10 text-rose-400"></i>
                        </div>
                        <h2 class="text-2xl sm:text-3xl font-bold font-handwritten text-rose-400 mb-1">
                            Timeline Collapsed
                        </h2>
                        <p class="text-xs text-slate-300 mb-5 font-mono">Time ran out or sequence feedback overwhelmed the core particle.</p>

                        <div class="bg-white/5 p-3 rounded-2xl border border-white/10 mb-6 inline-block w-48 sketch-border">
                            <div class="text-xs text-slate-400 uppercase font-bold">Final Score</div>
                            <div id="gameover-score" class="text-2xl font-black font-mono text-rose-300">0000</div>
                        </div>

                        <button id="btn-restart" class="w-full py-3 bg-gradient-to-r from-rose-700 to-amber-700 hover:brightness-110 text-white font-bold tracking-wider rounded-2xl shadow-xl transition transform flex items-center justify-center gap-2 sketch-border">
                            <i data-lucide="rotate-ccw" class="w-5 h-5"></i> RETRY TIMELINE
                        </button>
                    </div>
                </div>

                <!-- MODAL: DEV LOG / NOTEBOOK OVERLAY -->
                <div id="modal-devlog" class="absolute inset-0 bg-black/90 backdrop-blur-xl flex flex-col items-center justify-center p-4 sm:p-6 text-left z-30 hidden">
                    <div class="max-w-lg w-full bg-stone-900 border-2 border-amber-800/60 rounded-3xl p-6 relative sketch-border text-amber-100 max-h-[85vh] overflow-y-auto">
                        <button id="btn-close-devlog" class="absolute top-4 right-4 text-slate-400 hover:text-white p-2">
                            <i data-lucide="x" class="w-6 h-6"></i>
                        </button>

                        <h3 class="text-2xl font-bold font-handwritten text-amber-300 mb-2 flex items-center gap-2">
                            <i data-lucide="pen-tool" class="w-5 h-5"></i> Developer Notebook &amp; Guide
                        </h3>

                        <div class="space-y-4 text-xs sm:text-sm text-amber-200/90 leading-relaxed font-mono">
                            <div class="bg-amber-950/40 p-3 rounded-2xl border border-amber-800/40">
                                <strong class="text-amber-300 block mb-1">🕹️ How to Play:</strong>
                                Collect the numbered nodes in order (1 → 2 → 3...). Grab the wrong one and you'll get bounced back and lose a few seconds — I made that penalty way too harsh in testing and then just... left it. Builds character.
                            </div>

                            <div class="bg-amber-950/40 p-3 rounded-2xl border border-amber-800/40">
                                <strong class="text-amber-300 block mb-1">📱 Controls:</strong>
                                • <strong>Gyro Tilt:</strong> Tap "Enable Tilt" at the top to steer by holding your phone!<br>
                                • <strong>Mouse / Touch:</strong> Move or drag on screen to guide your core particle.<br>
                                • <strong>Keyboard:</strong> Arrow keys or WASD also work.
                            </div>

                            <div class="bg-amber-950/40 p-3 rounded-2xl border border-amber-800/40">
                                <strong class="text-amber-300 block mb-1">⏳ The 10 Evolutionary Eras:</strong>
                                1. Stone Age Chalk — static 4-node sequence.<br>
                                2. Bronze Age Clay — 5 nodes, ambient wind drift.<br>
                                3. Medieval Alchemy — 5 moving nodes, 1 Flux Zone.<br>
                                4. Renaissance Astronomy — 6 nodes, 1 Gravity Well.<br>
                                5. Industrial Steampunk — 6 nodes, gravity + flux + wind.<br>
                                6. 80s Arcade Synthwave — 7 nodes, repulsor field, CRT scanlines.<br>
                                7. Quantum Laser Grid — 7 nodes, moving Flux Zones.<br>
                                8. Cosmic Nebula — 8 nodes, dual Gravity Wells.<br>
                                9. Black Hole Singularity — 8 nodes, strong gravity. Sorry in advance.<br>
                                10. Multiverse Void — 9 nodes, oscillating gravity &amp; chaotic flux. If you beat this one, you're better than me.
                            </div>

                            <div class="bg-amber-950/40 p-3 rounded-2xl border border-amber-800/40">
                                <strong class="text-amber-300 block mb-1">📝 Patch Notes:</strong>
                                <span class="opacity-80">v1.2</span> — Expanded to the full 10-era run. Rebalanced the Black Hole level after my partner couldn't clear it (sorry, still can't fully fix physics).<br>
                                <span class="opacity-80">v1.1</span> — Added Flux Zones because the timer felt too punishing without a breather.<br>
                                <span class="opacity-80">v1.0</span> — First playable. Built this over a few late nights messing around with canvas physics.
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </main>

        <!-- FOOTER -->
        <footer class="w-full flex justify-between items-center px-4 py-1.5 text-xs text-amber-200/60 font-mono">
            <span id="footer-control-mode">Input: Mouse / Touch Fallback</span>
            <span>Handcrafted in HTML5 • 2026</span>
        </footer>
    </div>

    <!-- GAME ENGINE CODE -->
    <script>
        lucide.createIcons();

        // ---- era config ----
        // ten difficulty steps, hand-tuned by playtesting (i.e. me dying a lot
        // and nudging numbers). nodeCount, gravity and flux all ramp up
        // together so it doesn't spike too hard around era 6
        const ERAS = [
            {
                id: 1,
                title: "Era 1: Stone Age Chalk",
                subtitle: "Primitive hearth embers & rough cave sketches",
                fontClass: "font-stone",
                icon: "flame",
                headerBg: "bg-amber-950/40 border-amber-800/60 text-amber-200",
                iconBg: "bg-amber-900/40 border-amber-700/50 text-amber-500",
                nodeCount: 4,
                hasMovingNodes: false,
                gravityWells: 0,
                fluxZones: 0,
                windDrift: { x: 0, y: 0 },
                timeLimit: 40.0,
                bgColor: "#140e0a",
                nodeColors: ["#d97706", "#b45309", "#92400e", "#78350f"],
                particleColor: "#f59e0b",
                synthType: "marimba"
            },
            {
                id: 2,
                title: "Era 2: Bronze Age Clay",
                subtitle: "Chiseled tablets & ambient desert wind drift",
                fontClass: "font-stone",
                icon: "hammer",
                headerBg: "bg-amber-900/40 border-amber-700/60 text-amber-100",
                iconBg: "bg-amber-800/40 border-amber-600/50 text-amber-400",
                nodeCount: 5,
                hasMovingNodes: false,
                gravityWells: 0,
                fluxZones: 0,
                windDrift: { x: 0.12, y: 0.05 },
                timeLimit: 36.0,
                bgColor: "#18120b",
                nodeColors: ["#b45309", "#d97706", "#ca8a04", "#a16207", "#78350f"],
                particleColor: "#fbbf24",
                synthType: "marimba"
            },
            {
                id: 3,
                title: "Era 3: Medieval Alchemy",
                subtitle: "Codex scroll & time-dilation Flux Bubbles",
                fontClass: "font-medieval",
                icon: "scroll",
                headerBg: "bg-yellow-950/40 border-yellow-800/60 text-yellow-200",
                iconBg: "bg-yellow-900/40 border-yellow-700/50 text-yellow-400",
                nodeCount: 5,
                hasMovingNodes: true,
                gravityWells: 0,
                fluxZones: 1,
                windDrift: { x: 0, y: 0 },
                timeLimit: 34.0,
                bgColor: "#1a150e",
                nodeColors: ["#eab308", "#ca8a04", "#a16207", "#854d0e", "#eab308"],
                particleColor: "#fde047",
                synthType: "chime"
            },
            {
                id: 4,
                title: "Era 4: Renaissance Astronomy",
                subtitle: "Celestial maps & central Gravity Well pull",
                fontClass: "font-medieval",
                icon: "compass",
                headerBg: "bg-blue-950/40 border-blue-800/60 text-blue-200",
                iconBg: "bg-blue-900/40 border-blue-700/50 text-blue-400",
                nodeCount: 6,
                hasMovingNodes: true,
                gravityWells: 1,
                fluxZones: 0,
                windDrift: { x: 0, y: 0 },
                timeLimit: 32.0,
                bgColor: "#09111e",
                nodeColors: ["#3b82f6", "#60a5fa", "#2563eb", "#1d4ed8", "#93c5fd", "#3b82f6"],
                particleColor: "#60a5fa",
                synthType: "chime"
            },
            {
                id: 5,
                title: "Era 5: Industrial Steampunk",
                subtitle: "Brass pistons, steam vents & moving gears",
                fontClass: "font-stone",
                icon: "cog",
                headerBg: "bg-orange-950/40 border-orange-800/60 text-orange-200",
                iconBg: "bg-orange-900/40 border-orange-700/50 text-orange-400",
                nodeCount: 6,
                hasMovingNodes: true,
                gravityWells: 1,
                fluxZones: 1,
                windDrift: { x: -0.15, y: 0 },
                timeLimit: 30.0,
                bgColor: "#140c08",
                nodeColors: ["#ea580c", "#c2410c", "#9a3412", "#f97316", "#c2410c", "#ea580c"],
                particleColor: "#fb923c",
                synthType: "steam"
            },
            {
                id: 6,
                title: "Era 6: 80s Arcade Synthwave",
                subtitle: "Vector grids, Repulsor fields & CRT lines",
                fontClass: "font-arcade",
                icon: "terminal",
                headerBg: "bg-fuchsia-950/40 border-fuchsia-800/60 text-fuchsia-200",
                iconBg: "bg-fuchsia-900/40 border-fuchsia-700/50 text-cyan-400",
                nodeCount: 7,
                hasMovingNodes: true,
                gravityWells: 1,
                fluxZones: 1,
                repulsor: true,
                timeLimit: 28.0,
                bgColor: "#08030d",
                nodeColors: ["#ec4899", "#06b6d4", "#a855f7", "#3b82f6", "#ec4899", "#06b6d4", "#a855f7"],
                particleColor: "#22d3ee",
                synthType: "chiptune"
            },
            {
                id: 7,
                title: "Era 7: Quantum Laser Grid",
                subtitle: "Pulsing gravity sinks & moving Flux Bubbles",
                fontClass: "font-quantum",
                icon: "atom",
                headerBg: "bg-cyan-950/40 border-cyan-800/60 text-cyan-200",
                iconBg: "bg-cyan-900/40 border-cyan-700/50 text-cyan-400",
                nodeCount: 7,
                hasMovingNodes: true,
                gravityWells: 1,
                fluxZones: 2,
                movingFlux: true,
                timeLimit: 26.0,
                bgColor: "#020617",
                nodeColors: ["#38bdf8", "#818cf8", "#c084fc", "#f472b6", "#34d399", "#38bdf8", "#818cf8"],
                particleColor: "#38bdf8",
                synthType: "quantum"
            },
            {
                id: 8,
                title: "Era 8: Cosmic Nebula",
                subtitle: "Dual Gravity Wells & orbiting stellar nodes",
                fontClass: "font-quantum",
                icon: "sparkles",
                headerBg: "bg-indigo-950/40 border-indigo-800/60 text-indigo-200",
                iconBg: "bg-indigo-900/40 border-indigo-700/50 text-indigo-400",
                nodeCount: 8,
                hasMovingNodes: true,
                gravityWells: 2,
                fluxZones: 2,
                timeLimit: 24.0,
                bgColor: "#050518",
                nodeColors: ["#818cf8", "#a78bfa", "#c084fc", "#e879f9", "#38bdf8", "#818cf8", "#a78bfa", "#c084fc"],
                particleColor: "#c084fc",
                synthType: "quantum"
            },
            {
                id: 9,
                title: "Era 9: Black Hole Singularity",
                subtitle: "Bending Event Horizons & intense gravitational pull",
                fontClass: "font-quantum",
                icon: "disc",
                headerBg: "bg-purple-950/40 border-purple-800/60 text-purple-200",
                iconBg: "bg-purple-900/40 border-purple-700/50 text-rose-400",
                nodeCount: 8,
                hasMovingNodes: true,
                gravityWells: 2,
                strongGravity: true,
                fluxZones: 2,
                timeLimit: 22.0,
                bgColor: "#03020a",
                nodeColors: ["#f43f5e", "#fb7185", "#e11d48", "#c084fc", "#38bdf8", "#f43f5e", "#fb7185", "#e11d48"],
                particleColor: "#f43f5e",
                synthType: "quantum"
            },
            {
                id: 10,
                title: "Era 10: Multiverse Void",
                subtitle: "Shifting Gravity Oscillators & chaotic flux nebulae",
                fontClass: "font-quantum",
                icon: "zap",
                headerBg: "bg-rose-950/40 border-rose-800/60 text-rose-200",
                iconBg: "bg-rose-900/40 border-rose-700/50 text-rose-400",
                nodeCount: 9,
                hasMovingNodes: true,
                gravityWells: 3,
                oscillatingGravity: true,
                fluxZones: 3,
                movingFlux: true,
                timeLimit: 20.0,
                bgColor: "#0a0106",
                nodeColors: ["#fb7185", "#38bdf8", "#34d399", "#facc15", "#c084fc", "#f43f5e", "#fb7185", "#38bdf8", "#34d399"],
                particleColor: "#fb7185",
                synthType: "quantum"
            }
        ];

        // audio stuff below. no sample files, everything's synthesized with
        // Web Audio so it all stays one file. each era gets its own little
        // "instrument" so later levels feel more electronic
        class CozySoundEngine {
            constructor() {
                this.ctx = null;
                this.muted = false;
            }

            init() {
                if (!this.ctx) {
                    const AudioContext = window.AudioContext || window.webkitAudioContext;
                    this.ctx = new AudioContext();
                }
                if (this.ctx.state === 'suspended') this.ctx.resume();
            }

            playTone(freq, type = 'sine', duration = 0.2, volume = 0.2) {
                if (this.muted || !this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();

                    osc.type = type;
                    osc.frequency.setValueAtTime(freq, this.ctx.currentTime);

                    gain.gain.setValueAtTime(volume, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.0001, this.ctx.currentTime + duration);

                    osc.connect(gain);
                    gain.connect(this.ctx.destination);

                    osc.start();
                    osc.stop(this.ctx.currentTime + duration);
                } catch (e) {}
            }

            playFluxSwoosh() {
                if (this.muted || !this.ctx) return;
                this.playTone(180, 'sine', 0.4, 0.15);
                setTimeout(() => this.playTone(120, 'sine', 0.4, 0.15), 100);
            }

            playSuccess(type) {
                if (!this.ctx) return;
                if (type === 'marimba') {
                    this.playTone(329.63, 'sine', 0.25, 0.3);
                    setTimeout(() => this.playTone(440, 'sine', 0.3, 0.3), 80);
                } else if (type === 'chime') {
                    this.playTone(523.25, 'triangle', 0.35, 0.25);
                    setTimeout(() => this.playTone(659.25, 'triangle', 0.4, 0.25), 90);
                } else if (type === 'steam') {
                    this.playTone(280, 'sawtooth', 0.1, 0.2);
                    setTimeout(() => this.playTone(420, 'square', 0.2, 0.15), 60);
                } else if (type === 'chiptune') {
                    this.playTone(440, 'square', 0.08, 0.2);
                    setTimeout(() => this.playTone(880, 'square', 0.15, 0.2), 60);
                } else {
                    this.playTone(600, 'sine', 0.15, 0.25);
                    setTimeout(() => this.playTone(1200, 'sine', 0.25, 0.25), 80);
                }
            }

            playError() {
                if (!this.ctx) return;
                this.playTone(130, 'sawtooth', 0.25, 0.3);
                setTimeout(() => this.playTone(100, 'sawtooth', 0.3, 0.3), 80);
            }

            playWin() {
                if (!this.ctx) return;
                [261.63, 329.63, 392.00, 523.25].forEach((freq, i) => {
                    setTimeout(() => this.playTone(freq, 'triangle', 0.3, 0.25), i * 90);
                });
            }
        }

        const audio = new CozySoundEngine();

        // game state. kept this as loose top-level vars instead of one big
        // state object -- probably not "clean" but it's fast to read mid-frame
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let gameState = 'START';
        let currentEraIdx = 0;
        let score = 0;
        let timeLeft = 30.0;
        let timerInterval = null;
        let inFluxZone = false;

        const player = {
            x: 0,
            y: 0,
            vx: 0,
            vy: 0,
            radius: 12,
            speed: 0.6,
            friction: 0.9,
            particles: []
        };

        let keys = {};
        let mouse = { x: 0, y: 0, active: false };
        let gyroActive = false;
        let tilt = { x: 0, y: 0 };
        let frameCount = 0; // had a debug overlay using this at one point, ripped it out, never deleted the counter

        let nodes = [];
        let sequence = [];
        let currentSeqIdx = 0;
        let gravityWells = [];
        let fluxZones = [];
        let backgroundAngle = 0;

        function resizeCanvas() {
            const container = canvas.parentElement;
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
            if (gameState === 'START') {
                player.x = canvas.width / 2;
                player.y = canvas.height / 2;
            }
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // controls -- gyro / mouse / touch / keyboard, pick your poison
        window.addEventListener('keydown', e => keys[e.key.toLowerCase()] = true);
        window.addEventListener('keyup', e => keys[e.key.toLowerCase()] = false);

        canvas.addEventListener('mousemove', e => {
            const rect = canvas.getBoundingClientRect();
            mouse.x = e.clientX - rect.left;
            mouse.y = e.clientY - rect.top;
            mouse.active = true;
        });

        canvas.addEventListener('touchmove', function(e) {
            const rect = canvas.getBoundingClientRect();
            if (e.touches.length > 0) {
                mouse.x = e.touches[0].clientX - rect.left;
                mouse.y = e.touches[0].clientY - rect.top;
                mouse.active = true;
            }
            e.preventDefault();
        }, { passive: false });

        const btnGyro = document.getElementById('btn-gyro');
        const gyroStatus = document.getElementById('gyro-status');
        const footerMode = document.getElementById('footer-control-mode');

        btnGyro.addEventListener('click', async () => {
            audio.init();
            if (typeof DeviceOrientationEvent !== 'undefined' && typeof DeviceOrientationEvent.requestPermission === 'function') {
                try {
                    const response = await DeviceOrientationEvent.requestPermission();
                    if (response === 'granted') enableGyro();
                    else alert('Gyroscope permission denied.');
                } catch (e) { console.error(e); }
            } else if (window.DeviceOrientationEvent) {
                enableGyro();
            } else {
                alert('DeviceOrientation not supported on this device.');
            }
        });

        function enableGyro() {
            gyroActive = true;
            gyroStatus.innerText = 'Tilt Active';
            footerMode.innerText = 'Input: Mobile Gyroscope Tilt';
            btnGyro.classList.add('bg-emerald-600', 'text-white');

            window.addEventListener('deviceorientation', e => {
                if (e.gamma !== null && e.beta !== null) {
                    tilt.x = Math.max(-1, Math.min(1, e.gamma / 22));
                    tilt.y = Math.max(-1, Math.min(1, (e.beta - 35) / 22));
                }
            });
        }

        // level setup. this one got messy, sorry future me
        function initEra(eraIdx) {
            const era = ERAS[eraIdx];
            nodes = [];
            gravityWells = [];
            fluxZones = [];
            sequence = [];
            currentSeqIdx = 0;
            timeLeft = era.timeLimit;
            inFluxZone = false;

            // UI Styling updates
            const header = document.getElementById('era-header');
            const iconBox = document.getElementById('era-icon-box');
            const eraTitle = document.getElementById('era-title');
            const eraSubtitle = document.getElementById('era-subtitle');
            const eraIcon = document.getElementById('era-icon');
            const crtOverlay = document.getElementById('crt-overlay');

            header.className = `w-full flex justify-between items-center p-3 sm:p-4 rounded-3xl border transition-all duration-700 z-20 sketch-border shadow-lg mb-2 ${era.headerBg}`;
            iconBox.className = `p-2.5 rounded-2xl border sketch-border ${era.iconBg}`;
            eraTitle.className = `text-lg sm:text-2xl font-bold tracking-wide ${era.fontClass} ${era.id === 10 ? 'glitch-text' : ''}`;
            eraTitle.innerText = era.title;
            eraSubtitle.innerText = era.subtitle;

            eraIcon.setAttribute('data-lucide', era.icon);
            lucide.createIcons();

            crtOverlay.style.opacity = (era.id >= 6) ? '1' : '0';

            // Generate Nodes
            const padding = 60;
            for (let i = 1; i <= era.nodeCount; i++) {
                let valid = false, nx, ny, attempts = 0;
                while (!valid && attempts < 100) {
                    nx = padding + Math.random() * (canvas.width - padding * 2);
                    ny = padding + Math.random() * (canvas.height - padding * 2);
                    valid = true;
                    for (let n of nodes) {
                        if (Math.hypot(n.x - nx, n.y - ny) < 70) valid = false;
                    }
                    attempts++;
                }

                nodes.push({
                    id: i,
                    x: nx,
                    y: ny,
                    vx: era.hasMovingNodes ? (Math.random() - 0.5) * (1.2 + era.id * 0.15) : 0,
                    vy: era.hasMovingNodes ? (Math.random() - 0.5) * (1.2 + era.id * 0.15) : 0,
                    radius: 22,
                    color: era.nodeColors[(i - 1) % era.nodeColors.length],
                    pulse: Math.random() * Math.PI
                });
            }

            // Generate Gravity Wells
            for (let g = 0; g < era.gravityWells; g++) {
                gravityWells.push({
                    x: (canvas.width / (era.gravityWells + 1)) * (g + 1),
                    y: canvas.height / 2 + (Math.random() - 0.5) * 100,
                    radius: era.strongGravity ? 45 : 35,
                    strength: era.repulsor ? -1.8 : (era.strongGravity ? 2.5 : 1.5),
                    oscillate: era.oscillatingGravity
                });
            }

            // Generate Flux Zones (Time-slowing nebulae)
            for (let f = 0; f < era.fluxZones; f++) {
                fluxZones.push({
                    x: padding * 2 + Math.random() * (canvas.width - padding * 4),
                    y: padding * 2 + Math.random() * (canvas.height - padding * 4),
                    radius: 65,
                    vx: era.movingFlux ? (Math.random() - 0.5) * 1.5 : 0,
                    vy: era.movingFlux ? (Math.random() - 0.5) * 1.5 : 0,
                    pulse: 0
                });
            }

            // Shuffle sequence
            let nums = Array.from({ length: era.nodeCount }, (_, k) => k + 1);
            for (let i = nums.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [nums[i], nums[j]] = [nums[j], nums[i]];
            }
            sequence = nums;

            updateSequenceHUD();
        }

        function updateSequenceHUD() {
            const container = document.getElementById('hud-sequence');
            container.innerHTML = '';
            sequence.forEach((nodeId, idx) => {
                const pill = document.createElement('div');
                pill.className = `w-6 h-6 sm:w-7 sm:h-7 rounded-lg flex items-center justify-center font-bold text-xs font-mono transition-all duration-300 sketch-border ${
                    idx < currentSeqIdx
                        ? 'bg-white/10 text-slate-500 line-through border border-white/10'
                        : idx === currentSeqIdx
                            ? 'bg-amber-400 text-black scale-110 border-2 border-white animate-pulse shadow-md'
                            : 'bg-black/60 text-slate-300 border border-white/20'
                }`;
                pill.innerText = nodeId;
                container.appendChild(pill);
            });
        }

        function startGame() {
            audio.init();
            document.getElementById('screen-start').classList.add('hidden');
            document.getElementById('screen-win').classList.add('hidden');
            document.getElementById('screen-gameover').classList.add('hidden');
            document.getElementById('hud').classList.remove('hidden');

            gameState = 'PLAYING';
            score = 0;
            currentEraIdx = 0;
            player.x = canvas.width / 2;
            player.y = canvas.height / 2;
            player.vx = 0;
            player.vy = 0;

            initEra(currentEraIdx);
            startTimer();
        }

        function startTimer() {
            if (timerInterval) clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                if (gameState !== 'PLAYING') return;
                // Time ticks 50% slower inside Flux Zones
                const decrement = inFluxZone ? 0.05 : 0.1;
                timeLeft -= decrement;

                if (timeLeft <= 0) {
                    timeLeft = 0;
                    triggerGameOver();
                }
                // document.title = timeLeft.toFixed(1); // was using this to debug the flux slowdown, leaving it here in case
                document.getElementById('hud-timer').innerText = timeLeft.toFixed(1) + 's';
            }, 100);
        }

        function triggerGameOver() {
            gameState = 'GAMEOVER'; // TODO: maybe a "near miss" message if they were close to clearing the sequence
            clearInterval(timerInterval);
            audio.playError();
            document.getElementById('hud').classList.add('hidden');
            document.getElementById('gameover-score').innerText = String(score).padStart(4, '0');
            document.getElementById('screen-gameover').classList.remove('hidden');
        }

        function passEra() {
            gameState = 'WIN';
            clearInterval(timerInterval);
            audio.playWin();
            score += Math.floor(timeLeft * 20) + ((currentEraIdx + 1) * 150); // bonus math I fiddled with til it felt fair, don't ask me to justify the 150

            document.getElementById('hud').classList.add('hidden');
            document.getElementById('win-score').innerText = String(score).padStart(4, '0');

            if (currentEraIdx < ERAS.length - 1) {
                document.getElementById('win-title').innerText = "Era Complete!";
                document.getElementById('win-desc').innerText = "The particle stream leaps into the next evolutionary era...";
                document.getElementById('btn-next-level').innerHTML = '<i data-lucide="arrow-right" class="w-5 h-5"></i> LEAP TO NEXT ERA';
            } else {
                document.getElementById('win-title').innerText = "10-Era Odyssey Complete!";
                document.getElementById('win-desc').innerText = "Incredible! You guided the particle timeline across all 10 historical & futuristic eras!";
                document.getElementById('btn-next-level').innerHTML = '<i data-lucide="rotate-ccw" class="w-5 h-5"></i> RESTART ODYSSEY';
            }
            lucide.createIcons();
            document.getElementById('screen-win').classList.remove('hidden');
        }

        // the actual game loop lives down here
        function update() {
            if (gameState !== 'PLAYING') return;
            const era = ERAS[currentEraIdx];

            // 1. Check Flux Zone collisions (Time Dilation)
            let insideFlux = false;
            fluxZones.forEach(fz => {
                fz.pulse += 0.03;
                if (era.movingFlux) {
                    fz.x += fz.vx;
                    fz.y += fz.vy;
                    if (fz.x < fz.radius || fz.x > canvas.width - fz.radius) fz.vx *= -1;
                    if (fz.y < fz.radius || fz.y > canvas.height - fz.radius) fz.vy *= -1;
                }
                if (Math.hypot(player.x - fz.x, player.y - fz.y) < fz.radius) {
                    insideFlux = true;
                }
            });

            if (insideFlux && !inFluxZone) audio.playFluxSwoosh();
            inFluxZone = insideFlux;
            document.getElementById('hud-flux-tag').classList.toggle('hidden', !inFluxZone);

            // Friction & Speed dampening in Flux Zone
            const currentSpeed = inFluxZone ? player.speed * 0.45 : player.speed;
            const currentFriction = inFluxZone ? 0.82 : player.friction;

            // 2. Control Inputs
            if (gyroActive) {
                player.vx += tilt.x * currentSpeed;
                player.vy += tilt.y * currentSpeed;
            } else if (mouse.active) {
                const pull = inFluxZone ? 0.03 : 0.07; // softer pull when time's slowed down, felt too twitchy otherwise
                player.vx += (mouse.x - player.x) * pull;
                player.vy += (mouse.y - player.y) * pull;
            }

            if (keys['arrowleft'] || keys['a']) player.vx -= currentSpeed;
            if (keys['arrowright'] || keys['d']) player.vx += currentSpeed;
            if (keys['arrowup'] || keys['w']) player.vy -= currentSpeed;
            if (keys['arrowdown'] || keys['s']) player.vy += currentSpeed;

            // Apply Ambient Wind Drift
            if (era.windDrift) {
                player.vx += era.windDrift.x;
                player.vy += era.windDrift.y;
            }

            // 3. Gravity Wells — basic inverse-square pull, capped to a 220px
            // radius so it doesn't yank you from across the whole map. The /120
            // constant is just whatever felt right on my screen; tweak freely.
            gravityWells.forEach(gw => {
                if (gw.oscillate) {
                    gw.strength = Math.sin(backgroundAngle * 2) * 3.0;
                }
                const dx = gw.x - player.x;
                const dy = gw.y - player.y;
                const dist = Math.hypot(dx, dy);
                if (dist > 10 && dist < 220) {
                    const force = (gw.strength * 120) / (dist * dist);
                    player.vx += (dx / dist) * force;
                    player.vy += (dy / dist) * force;
                }
            });

            player.vx *= currentFriction;
            player.vy *= currentFriction;
            player.x += player.vx;
            player.y += player.vy;

            // Bounce off the walls instead of clamping dead-stop — feels way less
            // janky, even though the 0.5 damping here is a totally made-up number.
            if (player.x < player.radius) { player.x = player.radius; player.vx *= -0.5; }
            if (player.x > canvas.width - player.radius) { player.x = canvas.width - player.radius; player.vx *= -0.5; }
            if (player.y < player.radius) { player.y = player.radius; player.vy *= -0.5; }
            if (player.y > canvas.height - player.radius) { player.y = canvas.height - player.radius; player.vy *= -0.5; }

            // Particle Trail
            player.particles.push({
                x: player.x + (Math.random() - 0.5) * 6,
                y: player.y + (Math.random() - 0.5) * 6,
                size: Math.random() * 5 + 3
            });
            if (player.particles.length > (inFluxZone ? 30 : 20)) player.particles.shift();

            backgroundAngle += 0.008;

            // 4. Update Sequence Nodes
            nodes.forEach(node => {
                node.pulse += 0.05;
                if (era.hasMovingNodes) {
                    const speedMult = inFluxZone ? 0.4 : 1.0;
                    node.x += node.vx * speedMult;
                    node.y += node.vy * speedMult;
                    if (node.x < 40 || node.x > canvas.width - 40) node.vx *= -1;
                    if (node.y < 40 || node.y > canvas.height - 40) node.vy *= -1;
                }

                // Right node in the right order → reward. Wrong node → bounce
                // the player back and dock some time. The `currentSeqIdx === 0 ||`
                // check stops it from double-punishing you for bumping into a
                // node you *already* collected, which tripped me up for a while.
                const dist = Math.hypot(player.x - node.x, player.y - node.y);
                if (dist < player.radius + node.radius) {
                    const expectedId = sequence[currentSeqIdx];
                    if (node.id === expectedId) {
                        audio.playSuccess(era.synthType);
                        currentSeqIdx++;
                        timeLeft += 2.5;
                        score += 100;
                        updateSequenceHUD();
                        node.x = -1000;
                        node.y = -1000;

                        if (currentSeqIdx >= sequence.length) {
                            passEra();
                        }
                    } else if (currentSeqIdx === 0 || node.id !== sequence[currentSeqIdx - 1]) {
                        audio.playError();
                        timeLeft = Math.max(0, timeLeft - 3.0);
                        player.vx *= -1.4;
                        player.vy *= -1.4;

                        const container = document.getElementById('canvas-container');
                        container.classList.add('shake');
                        setTimeout(() => container.classList.remove('shake'), 300);
                    }
                }
            });

            document.getElementById('hud-score').innerText = String(score).padStart(4, '0');
            document.getElementById('hud-level').innerText = `${String(currentEraIdx + 1).padStart(2, '0')} / 10`;
        }

        function render() {
            const era = ERAS[currentEraIdx];

            // 1. Draw Background & Era Aesthetic Details
            ctx.fillStyle = era.bgColor;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            if (era.id === 6 || era.id === 7) {
                // Neon Grid for Synthwave/Laser Eras
                ctx.strokeStyle = era.id === 6 ? 'rgba(236, 72, 153, 0.12)' : 'rgba(56, 189, 248, 0.12)';
                ctx.lineWidth = 1;
                for (let x = 0; x < canvas.width; x += 35) {
                    ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
                }
            }

            // 2. Draw Flux Zones (Time-slowing Nebulae)
            fluxZones.forEach(fz => {
                ctx.beginPath();
                ctx.arc(fz.x, fz.y, fz.radius + Math.sin(fz.pulse) * 4, 0, Math.PI * 2);
                ctx.fillStyle = 'rgba(6, 182, 212, 0.15)';
                ctx.strokeStyle = 'rgba(34, 211, 238, 0.4)';
                ctx.lineWidth = 2;
                ctx.fill();
                ctx.stroke();

                ctx.fillStyle = 'rgba(165, 243, 252, 0.7)';
                ctx.font = '9px monospace';
                ctx.textAlign = 'center';
                ctx.fillText('FLUX ZONE', fz.x, fz.y);
            });

            // 3. Draw Gravity Wells / Black Holes
            gravityWells.forEach(gw => {
                ctx.beginPath();
                ctx.arc(gw.x, gw.y, gw.radius + Math.sin(backgroundAngle * 4) * 3, 0, Math.PI * 2);
                ctx.fillStyle = gw.strength < 0 ? 'rgba(236, 72, 153, 0.2)' : 'rgba(0, 0, 0, 0.85)';
                ctx.strokeStyle = gw.strength < 0 ? '#f43f5e' : era.particleColor;
                ctx.lineWidth = 2;
                ctx.fill();
                ctx.stroke();

                // Distortion Rings
                ctx.beginPath();
                ctx.arc(gw.x, gw.y, gw.radius * 1.8, 0, Math.PI * 2);
                ctx.strokeStyle = gw.strength < 0 ? 'rgba(244, 63, 94, 0.2)' : 'rgba(255, 255, 255, 0.1)';
                ctx.setLineDash([4, 4]);
                ctx.stroke();
                ctx.setLineDash([]);
            });

            // 4. Render Nodes
            nodes.forEach(node => {
                const isNext = node.id === sequence[currentSeqIdx];

                if (isNext) {
                    ctx.beginPath();
                    ctx.arc(node.x, node.y, node.radius + 6 + Math.sin(node.pulse * 2) * 3, 0, Math.PI * 2);
                    ctx.strokeStyle = era.particleColor;
                    ctx.lineWidth = 2;
                    ctx.stroke();
                }

                ctx.beginPath();
                ctx.arc(node.x, node.y, node.radius, 0, Math.PI * 2);
                ctx.fillStyle = node.color;
                ctx.shadowColor = node.color;
                ctx.shadowBlur = isNext ? 18 : 4;
                ctx.fill();
                ctx.shadowBlur = 0;

                ctx.fillStyle = '#000000';
                ctx.font = 'bold 14px monospace, sans-serif';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(node.id, node.x, node.y);
            });

            // 5. Render Player Particle Swarm
            player.particles.forEach((p, idx) => {
                const alpha = idx / player.particles.length;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size * alpha, 0, Math.PI * 2);
                ctx.fillStyle = inFluxZone ? '#22d3ee' : era.particleColor;
                ctx.globalAlpha = alpha * 0.7;
                ctx.fill();
            });
            ctx.globalAlpha = 1.0;

            ctx.beginPath();
            ctx.arc(player.x, player.y, player.radius, 0, Math.PI * 2);
            ctx.fillStyle = '#ffffff';
            ctx.shadowColor = inFluxZone ? '#22d3ee' : era.particleColor;
            ctx.shadowBlur = 16;
            ctx.fill();
            ctx.shadowBlur = 0;
        }

        function gameLoop() {
            frameCount++;
            update();
            render();
            requestAnimationFrame(gameLoop);
        }

        // wire everything up
        document.getElementById('btn-start').addEventListener('click', startGame);
        document.getElementById('btn-restart').addEventListener('click', startGame);

        document.getElementById('btn-next-level').addEventListener('click', () => {
            if (currentEraIdx < ERAS.length - 1) {
                currentEraIdx++;
                document.getElementById('screen-win').classList.add('hidden');
                document.getElementById('hud').classList.remove('hidden');
                gameState = 'PLAYING';
                initEra(currentEraIdx);
                startTimer();
            } else {
                startGame();
            }
        });

        document.getElementById('btn-devlog').addEventListener('click', () => {
            document.getElementById('modal-devlog').classList.remove('hidden');
        });

        document.getElementById('btn-close-devlog').addEventListener('click', () => {
            document.getElementById('modal-devlog').classList.add('hidden');
        });

        document.getElementById('btn-sound').addEventListener('click', () => {
            audio.muted = !audio.muted;
            const icon = document.getElementById('btn-sound').querySelector('i');
            icon.setAttribute('data-lucide', audio.muted ? 'volume-x' : 'volume-2');
            lucide.createIcons();
        });

        requestAnimationFrame(gameLoop);
    </script>
</body>
</html>
