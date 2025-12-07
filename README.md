<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Browser Privacy Comparison (2025)</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background: #0a0e27;
            overflow-x: hidden;
            overflow-y: auto;
            position: relative;
            min-height: 100vh;
        }
        #fluidCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        .content-wrapper {
            position: relative;
            z-index: 2;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
            padding: 40px 20px;
        }
        .container {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(20px);
            border-radius: 24px;
            padding: 50px;
            box-shadow: 
                0 8px 32px rgba(0, 0, 0, 0.3),
                inset 0 1px 0 rgba(255, 255, 255, 0.1),
                0 0 0 1px rgba(255, 255, 255, 0.05);
            max-width: 1200px;
            width: 100%;
        }
        h1 {
            text-align: center;
            color: #ffffff;
            margin: 0 0 40px 0;
            font-size: 32px;
            font-weight: 700;
            text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
            letter-spacing: -0.5px;
        }
        #chartContainer {
            position: relative;
            height: 550px;
            background: rgba(255, 255, 255, 0.03);
            border-radius: 16px;
            padding: 20px;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
        }
        #chartContainer:hover {
            background: rgba(255, 255, 255, 0.06);
            transform: scale(1.02) translateZ(10px);
            box-shadow: 
                0 20px 60px rgba(0, 0, 0, 0.4),
                inset 0 1px 0 rgba(255, 255, 255, 0.15);
        }
        .glow-effect {
            position: absolute;
            top: 50%;
            left: 50%;
            width: 600px;
            height: 600px;
            background: radial-gradient(circle, rgba(102, 126, 234, 0.15) 0%, transparent 70%);
            transform: translate(-50%, -50%);
            pointer-events: none;
            animation: pulse 4s ease-in-out infinite;
        }
        @keyframes pulse {
            0%, 100% {
                transform: translate(-50%, -50%) scale(1);
                opacity: 0.5;
            }
            50% {
                transform: translate(-50%, -50%) scale(1.1);
                opacity: 0.8;
            }
        }
        .stat-cards {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-top: 30px;
        }
        .stat-card {
            background: rgba(255, 255, 255, 0.03);
            border-radius: 12px;
            padding: 20px;
            border: 1px solid rgba(255, 255, 255, 0.08);
            transition: all 0.3s ease;
            transform-style: preserve-3d;
        }
        .stat-card:hover {
            background: rgba(255, 255, 255, 0.08);
            transform: translateY(-5px) translateZ(15px);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            border-color: rgba(255, 255, 255, 0.2);
        }
        .stat-card h3 {
            color: #667eea;
            font-size: 14px;
            margin-bottom: 8px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        .stat-card p {
            color: #ffffff;
            font-size: 24px;
            font-weight: 700;
        }
        .table-section {
            margin-top: 50px;
        }
        .table-section h2 {
            color: #ffffff;
            font-size: 26px;
            margin-bottom: 15px;
            text-align: center;
        }
        .table-description {
            color: rgba(255, 255, 255, 0.7);
            text-align: center;
            margin-bottom: 30px;
            line-height: 1.6;
            font-size: 15px;
        }
        .table-wrapper {
            overflow-x: auto;
            background: rgba(255, 255, 255, 0.03);
            border-radius: 16px;
            padding: 20px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
        .comparison-table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 0 10px;
        }
        .comparison-table thead th {
            color: #667eea;
            font-size: 13px;
            text-transform: uppercase;
            letter-spacing: 1px;
            padding: 15px;
            text-align: left;
            font-weight: 600;
            border-bottom: 2px solid rgba(102, 126, 234, 0.3);
        }
        .comparison-table tbody tr {
            background: rgba(255, 255, 255, 0.03);
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
            opacity: 0.7;
        }
        .comparison-table tbody tr.visible {
            opacity: 1;
        }
        .comparison-table tbody tr:hover {
            background: linear-gradient(90deg, rgba(102, 126, 234, 0.15), rgba(118, 75, 162, 0.15));
            transform: scale(1.02) translateX(5px);
            box-shadow: -5px 0 20px rgba(102, 126, 234, 0.3);
            border-left: 3px solid #667eea;
        }
        .comparison-table tbody td {
            padding: 18px 15px;
            color: rgba(255, 255, 255, 0.85);
            font-size: 14px;
            line-height: 1.6;
            border-top: 1px solid rgba(255, 255, 255, 0.05);
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
        }
        .comparison-table tbody tr td:first-child {
            border-left: 3px solid transparent;
            border-top: 1px solid rgba(255, 255, 255, 0.05);
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
            border-radius: 8px 0 0 8px;
            transition: border-color 0.3s ease;
        }
        .comparison-table tbody tr td:last-child {
            border-right: 1px solid rgba(255, 255, 255, 0.05);
            border-radius: 0 8px 8px 0;
        }
        .browser-name {
            font-weight: 600;
            color: #ffffff !important;
            display: flex;
            align-items: center;
            gap: 10px;
            white-space: nowrap;
        }
        .color-dot {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            display: inline-block;
            box-shadow: 0 0 10px currentColor;
        }
        .score {
            display: inline-flex;
            align-items: center;
            gap: 5px;
            background: rgba(102, 126, 234, 0.2);
            color: #667eea;
            padding: 5px 12px;
            border-radius: 8px;
            font-weight: 700;
            margin-right: 8px;
            font-size: 13px;
            transition: all 0.3s ease;
        }
        .score.excellent {
            background: rgba(16, 185, 129, 0.2);
            color: #10b981;
            border: 1px solid rgba(16, 185, 129, 0.3);
        }
        .score.good {
            background: rgba(59, 130, 246, 0.2);
            color: #3b82f6;
            border: 1px solid rgba(59, 130, 246, 0.3);
        }
        .score.moderate {
            background: rgba(251, 191, 36, 0.2);
            color: #fbbf24;
            border: 1px solid rgba(251, 191, 36, 0.3);
        }
        .score.poor {
            background: rgba(239, 68, 68, 0.2);
            color: #ef4444;
            border: 1px solid rgba(239, 68, 68, 0.3);
        }
        .score.zero {
            background: rgba(220, 38, 38, 0.2);
            color: #dc2626;
            border: 1px solid rgba(220, 38, 38, 0.3);
        }
        .stars {
            display: inline-flex;
            gap: 2px;
            margin-left: 5px;
        }
        .star {
            font-size: 14px;
        }
        .star.filled {
            color: #fbbf24;
            text-shadow: 0 0 5px rgba(251, 191, 36, 0.5);
        }
        .star.empty {
            color: rgba(255, 255, 255, 0.2);
        }
        .comparison-table tbody tr:hover .score {
            transform: scale(1.1);
            box-shadow: 0 0 15px currentColor;
        }
        @media (max-width: 768px) {
            .comparison-table {
                font-size: 12px;
            }            
            .comparison-table thead th,
            .comparison-table tbody td {
                padding: 10px 8px;
            }
        }
    </style>
</head>
<body>
    <canvas id="fluidCanvas"></canvas>
    <div class="content-wrapper">
        <div class="container">
            <div class="glow-effect"></div>
            <h1>Browser Privacy Comparison (2025)</h1>
            <div id="chartContainer">
                <canvas id="radarChart"></canvas>
            </div>
            <div class="stat-cards">
                <div class="stat-card">
                    <h3>Most Private</h3>
                    <p>Tor Browser</p>
                </div>
                <div class="stat-card">
                    <h3>Best Daily Driver</h3>
                    <p>Brave</p>
                </div>
                <div class="stat-card">
                    <h3>Zero Telemetry</h3>
                    <p>LibreWolf</p>
                </div>
            </div>
            <div class="table-section">
                <h2>Detailed Privacy Comparison</h2>
                <p class="table-description">
                    This comprehensive breakdown shows how each browser handles tracking protection, telemetry, fingerprinting resistance, and anonymity. 
                    Higher scores indicate better privacy protection. Telemetry measurements show network connections and data sent per session.
                </p>
                <div class="table-wrapper">
                    <table class="comparison-table">
                        <thead>
                            <tr>
                                <th>Browser</th>
                                <th>Tracking Protection</th>
                                <th>Telemetry (Type & Data/Session)</th>
                                <th>Fingerprinting Resistance</th>
                                <th>Anonymity</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr data-browser="brave">
                                <td class="browser-name"><span class="color-dot" style="background: #FF6B35;"></span>Brave</td>
                                <td><span class="score excellent">9.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>Built-in Shields zap 90%+ trackers/ads</td>
                                <td>Opt-in usage stats (e.g., blocks count); ~17 connections, ~5-15KB</td>
                                <td><span class="score excellent">9.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>Randomizes per site; "strong" on EFF</td>
                                <td><span class="score moderate">7.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span></span></span>Tor tabs help, but no full IP hide</td>
                            </tr>
                            <tr data-browser="firefox">
                                <td class="browser-name"><span class="color-dot" style="background: #00A651;"></span>Firefox</td>
                                <td><span class="score good">7.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span></span></span>Enhanced mode blocks ~80%; needs tweaks</td>
                                <td>Usage/crash data (anonymized); ~29 connections, ~10-25KB</td>
                                <td><span class="score good">7.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span></span></span>Resists basics; "partial" on EFF</td>
                                <td><span class="score poor">5.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span><span class="star empty">★</span></span></span>No IP mask; local only</td>
                            </tr>
                            <tr data-browser="tor">
                                <td class="browser-name"><span class="color-dot" style="background: #5C2D91;"></span>Tor</td>
                                <td><span class="score excellent">9.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>NoScript blocks scripts; ultra-strict</td>
                                <td>None (zero pings); 0KB</td>
                                <td><span class="score excellent">9.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>All users identical; "strong" on EFF</td>
                                <td><span class="score excellent">10 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>Full onion routing hides IP</td>
                            </tr>
                            <tr data-browser="mullvad">
                                <td class="browser-name"><span class="color-dot" style="background: #1E88E5;"></span>Mullvad</td>
                                <td><span class="score excellent">9.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>uBlock + Tor-like blocks; 141/156 passes</td>
                                <td>None/minimal (VPN-focused); ~15 connections, ~2-8KB</td>
                                <td><span class="score excellent">9.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>Uniform fingerprints; "strong" on EFF</td>
                                <td><span class="score good">8.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>VPN-optimized; no Tor slowdown</td>
                            </tr>
                            <tr data-browser="librewolf">
                                <td class="browser-name"><span class="color-dot" style="background: #FFCA28;"></span>LibreWolf</td>
                                <td><span class="score excellent">9.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>uBlock baked in; 139/156 passes</td>
                                <td>None (stripped out); ~24 connections (updates only), ~3-10KB</td>
                                <td><span class="score good">8.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span></span></span>Hardened resistFingerprinting; "partial-strong" on EFF</td>
                                <td><span class="score moderate">5.5 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span><span class="star empty">★</span></span></span>Local tweaks; pair with VPN</td>
                            </tr>
                            <tr data-browser="ungoogled">
                                <td class="browser-name"><span class="color-dot" style="background: #757575;"></span>Ungoogled Chromium</td>
                                <td><span class="score good">8.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span></span></span>No built-ins; add uBlock for 85% block</td>
                                <td>None (Google code gutted); ~3 connections, ~1-5KB</td>
                                <td><span class="score moderate">7.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span></span></span>Basic Chromium resist; "partial" on EFF w/ extensions</td>
                                <td><span class="score poor">4.0 <span class="stars"><span class="star filled">★</span><span class="star filled">★</span><span class="star empty">★</span><span class="star empty">★</span><span class="star empty">★</span></span></span>Direct connect; no extras</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
    <script>
        // Fluid Mechanics Simulation
        const canvas = document.getElementById('fluidCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        class Particle {
            constructor() {
                this.x = Math.random() * canvas.width;
                this.y = Math.random() * canvas.height;
                this.vx = (Math.random() - 0.5) * 0.5;
                this.vy = (Math.random() - 0.5) * 0.5;
                this.radius = Math.random() * 2 + 1;
                this.color = `hsla(${Math.random() * 60 + 220}, 70%, 60%, 0.6)`;
            }
            update(mouseX, mouseY) {
                // Mouse interaction
                const dx = mouseX - this.x;
                const dy = mouseY - this.y;
                const dist = Math.sqrt(dx * dx + dy * dy);   
                if (dist < 150) {
                    const force = (150 - dist) / 150;
                    this.vx -= (dx / dist) * force * 0.2;
                    this.vy -= (dy / dist) * force * 0.2;
                }
                // Fluid-like viscosity
                this.vx *= 0.98;
                this.vy *= 0.98;
                this.x += this.vx;
                this.y += this.vy;
                // Wrap around edges
                if (this.x < 0) this.x = canvas.width;
                if (this.x > canvas.width) this.x = 0;
                if (this.y < 0) this.y = canvas.height;
                if (this.y > canvas.height) this.y = 0;
            }
            draw() {
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.fill();
            }
        }
        const particles = [];
        for (let i = 0; i < 150; i++) {
            particles.push(new Particle());
        }
        let mouseX = canvas.width / 2;
        let mouseY = canvas.height / 2;
        canvas.addEventListener('mousemove', (e) => {
            mouseX = e.clientX;
            mouseY = e.clientY;
        });
        function animate() {
            ctx.fillStyle = 'rgba(10, 14, 39, 0.1)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            particles.forEach(particle => {
                particle.update(mouseX, mouseY);
                particle.draw();
            });
            // Connect nearby particles
            for (let i = 0; i < particles.length; i++) {
                for (let j = i + 1; j < particles.length; j++) {
                    const dx = particles[i].x - particles[j].x;
                    const dy = particles[i].y - particles[j].y;
                    const dist = Math.sqrt(dx * dx + dy * dy);
                    if (dist < 100) {
                        ctx.beginPath();
                        ctx.strokeStyle = `rgba(102, 126, 234, ${0.2 * (1 - dist / 100)})`;
                        ctx.lineWidth = 0.5;
                        ctx.moveTo(particles[i].x, particles[i].y);
                        ctx.lineTo(particles[j].x, particles[j].y);
                        ctx.stroke();
                    }
                }
            }
            requestAnimationFrame(animate);
        }
        animate();
        window.addEventListener('resize', () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        });
        // Chart.js Radar Chart
        const radarCtx = document.getElementById('radarChart').getContext('2d');
        const data = {
            labels: [
                'Tracking Protection',
                'Telemetry (10 = none)',
                'Fingerprinting Resistance',
                'Anonymity'
            ],
            datasets: [
                {
                    label: 'Brave - Chromium daily driver',
                    data: [9.5, 9.0, 9.0, 7.0],
                    borderColor: '#FF6B35',
                    backgroundColor: 'rgba(255, 107, 53, 0.2)',
                    borderWidth: 3,
                    pointBackgroundColor: '#FF6B35',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: '#FF6B35',
                    pointRadius: 5,
                    pointHoverRadius: 8
                },
                {
                    label: 'Firefox - Gecko standard',
                    data: [7.5, 3.5, 7.5, 5.0],
                    borderColor: '#00A651',
                    backgroundColor: 'rgba(0, 166, 81, 0.2)',
                    borderWidth: 3,
                    pointBackgroundColor: '#00A651',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: '#00A651',
                    pointRadius: 5,
                    pointHoverRadius: 8
                },
                {
                    label: 'Tor - Anonymity king',
                    data: [9.0, 10.0, 9.5, 10.0],
                    borderColor: '#5C2D91',
                    backgroundColor: 'rgba(92, 45, 145, 0.2)',
                    borderWidth: 3,
                    pointBackgroundColor: '#5C2D91',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: '#5C2D91',
                    pointRadius: 5,
                    pointHoverRadius: 8
                },
                {
                    label: 'Mullvad - VPN-tuned',
                    data: [9.0, 9.5, 9.0, 8.5],
                    borderColor: '#1E88E5',
                    backgroundColor: 'rgba(30, 136, 229, 0.2)',
                    borderWidth: 3,
                    pointBackgroundColor: '#1E88E5',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: '#1E88E5',
                    pointRadius: 5,
                    pointHoverRadius: 8
                },
                {
                    label: 'LibreWolf - No-telemetry',
                    data: [9.0, 9.8, 8.5, 5.5],
                    borderColor: '#FFCA28',
                    backgroundColor: 'rgba(255, 202, 40, 0.2)',
                    borderWidth: 3,
                    pointBackgroundColor: '#FFCA28',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: '#FFCA28',
                    pointRadius: 5,
                    pointHoverRadius: 8
                },
                {
                    label: 'Ungoogled Chromium - Google-free base',
                    data: [8.0, 9.9, 7.0, 4.0],
                    borderColor: '#757575',
                    backgroundColor: 'rgba(117, 117, 117, 0.2)',
                    borderWidth: 3,
                    pointBackgroundColor: '#757575',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: '#757575',
                    pointRadius: 5,
                    pointHoverRadius: 8
                }
            ]
        };
        const config = {
            type: 'radar',
            data: data,
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    r: {
                        min: 0,
                        max: 10,
                        ticks: {
                            stepSize: 2,
                            font: {
                                size: 12
                            },
                            color: 'rgba(255, 255, 255, 0.7)',
                            backdropColor: 'transparent'
                        },
                        pointLabels: {
                            font: {
                                size: 14,
                                weight: 'bold'
                            },
                            color: '#ffffff'
                        },
                        grid: {
                            color: 'rgba(255, 255, 255, 0.1)'
                        },
                        angleLines: {
                            color: 'rgba(255, 255, 255, 0.1)'
                        }
                    }
                },
                plugins: {
                    legend: {
                        position: 'bottom',
                        labels: {
                            padding: 20,
                            font: {
                                size: 13
                            },
                            color: '#ffffff',
                            usePointStyle: true,
                            pointStyle: 'circle'
                        }
                    },
                    tooltip: {
                        backgroundColor: 'rgba(0, 0, 0, 0.9)',
                        padding: 15,
                        titleFont: {
                            size: 15,
                            weight: 'bold'
                        },
                        bodyFont: {
                            size: 14
                        },
                        borderColor: 'rgba(255, 255, 255, 0.2)',
                        borderWidth: 1
                    }
                },
                interaction: {
                    mode: 'nearest',
                    intersect: false
                }
            }
        };
        new Chart(radarCtx, config);
        // Scroll-based table row animations
        const observerOptions = {
            threshold: 0.3,
            rootMargin: '0px'
        };
        const observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('visible');
                }
            });
        }, observerOptions);
        document.querySelectorAll('.comparison-table tbody tr').forEach(row => {
            observer.observe(row);
        });
        // Fixed container - no horizontal movement
        const container = document.querySelector('.container');
        let isOverTable = false;
        document.querySelector('.table-wrapper').addEventListener('mouseenter', () => {
            isOverTable = true;
        });
        document.querySelector('.table-wrapper').addEventListener('mouseleave', () => {
            isOverTable = false;
        });
        container.addEventListener('mousemove', (e) => {
            if (isOverTable) return; // Don't tilt when over table 
            const rect = container.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;
            const centerX = rect.width / 2;
            const centerY = rect.height / 2;
            // Only vertical tilt (rotateX), no horizontal movement
            const rotateX = (y - centerY) / 80;
            container.style.transform = `perspective(2000px) rotateX(${rotateX}deg) scale(1.0)`;
        });
        container.addEventListener('mouseleave', () => {
            container.style.transform = 'perspective(2000px) rotateX(0) scale(1.0)';
        });
    </script>
</body>
</html>
