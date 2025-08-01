# IMD_GUI
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ESP32 Radar Control</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #1a2b3c;
            --secondary: #2c3e50;
            --accent: #3498db;
            --accent-dark: #2980b9;
            --success: #27ae60;
            --warning: #f39c12;
            --danger: #e74c3c;
            --text: #ecf0f1;
            --card-bg: rgba(30, 40, 60, 0.7);
            --panel-bg: rgba(20, 30, 48, 0.9);
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            color: var(--text);
            min-height: 100vh;
            overflow-x: hidden;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            text-align: center;
            padding: 20px 0;
            margin-bottom: 30px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .logo {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
        }
        
        .logo i {
            font-size: 2.5rem;
            color: var(--accent);
        }
        
        .logo h1 {
            font-size: 2.5rem;
            font-weight: 700;
            background: linear-gradient(to right, #3498db, #2ecc71);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            letter-spacing: 1px;
        }
        
        .subtitle {
            margin-top: 10px;
            color: #bdc3c7;
            font-size: 1.1rem;
        }
        
        .tabs {
            display: flex;
            justify-content: center;
            margin-bottom: 30px;
            gap: 20px;
        }
        
        .tab-btn {
            background: var(--panel-bg);
            border: none;
            color: var(--text);
            padding: 15px 30px;
            border-radius: 50px;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }
        
        .tab-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }
        
        .tab-btn.active {
            background: var(--accent);
            box-shadow: 0 0 15px rgba(52, 152, 219, 0.5);
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
            animation: fadeIn 0.5s ease;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .dashboard {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 25px;
            margin-bottom: 30px;
        }
        
        .panel {
            background: var(--panel-bg);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .panel-header {
            display: flex;
            align-items: center;
            margin-bottom: 25px;
            gap: 15px;
        }
        
        .panel-header i {
            font-size: 1.8rem;
            color: var(--accent);
        }
        
        .panel-header h2 {
            font-size: 1.8rem;
            font-weight: 600;
            color: var(--accent);
        }
        
        .chart-container {
            position: relative;
            height: 350px;
            margin-bottom: 25px;
        }
        
        .controls {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }
        
        .control-group {
            background: var(--card-bg);
            padding: 20px;
            border-radius: 12px;
        }
        
        .control-group h3 {
            margin-bottom: 15px;
            color: var(--accent);
            font-size: 1.3rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .slider-container {
            margin: 20px 0;
        }
        
        .slider-label {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }
        
        .slider {
            width: 100%;
            height: 10px;
            -webkit-appearance: none;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 5px;
            outline: none;
        }
        
        .slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 25px;
            height: 25px;
            border-radius: 50%;
            background: var(--accent);
            cursor: pointer;
            box-shadow: 0 0 10px rgba(52, 152, 219, 0.5);
        }
        
        .btn-group {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
        }
        
        .btn {
            flex: 1;
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            min-width: 120px;
        }
        
        .btn-primary {
            background: var(--accent);
        }
        
        .btn-success {
            background: var(--success);
        }
        
        .btn-warning {
            background: var(--warning);
        }
        
        .btn-danger {
            background: var(--danger);
        }
        
        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }
        
        .status {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background: rgba(0, 0, 0, 0.2);
            padding: 15px;
            border-radius: 10px;
            margin-top: 20px;
        }
        
        .status-indicator {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .indicator {
            width: 15px;
            height: 15px;
            border-radius: 50%;
            background: var(--success);
        }
        
        .indicator.offline {
            background: var(--danger);
        }
        
        .footer {
            text-align: center;
            margin-top: 40px;
            padding: 20px;
            color: #95a5a6;
            font-size: 0.9rem;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        @media (max-width: 900px) {
            .dashboard {
                grid-template-columns: 1fr;
            }
            
            .tabs {
                flex-wrap: wrap;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="logo">
                <i class="fas fa-radar"></i>
                <h1>ESP32 Radar Control</h1>
            </div>
            <p class="subtitle">Precision motor control with live radar visualization</p>
        </header>
        
        <div class="tabs">
            <button class="tab-btn active" data-tab="azimuth">
                <i class="fas fa-compass"></i> Azimuth Control
            </button>
            <button class="tab-btn" data-tab="elevation">
                <i class="fas fa-chart-line"></i> Elevation Control
            </button>
        </div>
        
        <!-- Azimuth Tab -->
        <div class="tab-content active" id="azimuth-tab">
            <div class="dashboard">
                <div class="panel">
                    <div class="panel-header">
                        <i class="fas fa-globe-americas"></i>
                        <h2>Azimuth Radar Visualization</h2>
                    </div>
                    <div class="chart-container">
                        <canvas id="azimuth-chart"></canvas>
                    </div>
                    
                    <div class="controls">
                        <div class="control-group">
                            <h3><i class="fas fa-cogs"></i> Motor Control</h3>
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Current Angle: <span id="azimuth-angle">0°</span></span>
                                    <span>Target: <span id="azimuth-target">0°</span></span>
                                </div>
                                <input type="range" min="0" max="360" value="0" class="slider" id="azimuth-slider">
                            </div>
                            
                            <div class="btn-group">
                                <button class="btn btn-primary" id="azimuth-cw">
                                    <i class="fas fa-arrow-right"></i> CW
                                </button>
                                <button class="btn btn-primary" id="azimuth-ccw">
                                    <i class="fas fa-arrow-left"></i> CCW
                                </button>
                                <button class="btn btn-danger" id="azimuth-stop">
                                    <i class="fas fa-stop"></i> Stop
                                </button>
                            </div>
                        </div>
                        
                        <div class="control-group">
                            <h3><i class="fas fa-tachometer-alt"></i> Speed Control</h3>
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Speed: <span id="azimuth-speed">50%</span></span>
                                </div>
                                <input type="range" min="0" max="100" value="50" class="slider" id="speed-slider">
                            </div>
                            
                            <div class="btn-group">
                                <button class="btn btn-success">
                                    <i class="fas fa-save"></i> Save Position
                                </button>
                                <button class="btn btn-warning">
                                    <i class="fas fa-sync"></i> Calibrate
                                </button>
                            </div>
                        </div>
                    </div>
                    
                    <div class="status">
                        <div class="status-indicator">
                            <div class="indicator"></div>
                            <span>Motor Status: <strong>Running</strong></span>
                        </div>
                        <div>
                            <span>Last Update: <strong id="azimuth-update">Just now</strong></span>
                        </div>
                    </div>
                </div>
                
                <div class="panel">
                    <div class="panel-header">
                        <i class="fas fa-wave-square"></i>
                        <h2>Signal Analysis</h2>
                    </div>
                    <div class="chart-container">
                        <canvas id="signal-chart"></canvas>
                    </div>
                    
                    <div class="controls">
                        <div class="control-group">
                            <h3><i class="fas fa-sliders-h"></i> Signal Settings</h3>
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Sensitivity: <span id="sensitivity-value">75%</span></span>
                                </div>
                                <input type="range" min="0" max="100" value="75" class="slider" id="sensitivity-slider">
                            </div>
                            
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Range: <span id="range-value">100m</span></span>
                                </div>
                                <input type="range" min="10" max="500" value="100" class="slider" id="range-slider">
                            </div>
                        </div>
                        
                        <div class="control-group">
                            <h3><i class="fas fa-broadcast-tower"></i> Detection</h3>
                            <div class="btn-group">
                                <button class="btn btn-primary">
                                    <i class="fas fa-play"></i> Start Scan
                                </button>
                                <button class="btn btn-danger">
                                    <i class="fas fa-stop"></i> Stop Scan
                                </button>
                            </div>
                            <div class="status" style="margin-top: 15px;">
                                <div class="status-indicator">
                                    <div class="indicator"></div>
                                    <span>Scan Status: <strong>Active</strong></span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Elevation Tab -->
        <div class="tab-content" id="elevation-tab">
            <div class="dashboard">
                <div class="panel">
                    <div class="panel-header">
                        <i class="fas fa-mountain"></i>
                        <h2>Elevation Control</h2>
                    </div>
                    <div class="chart-container">
                        <canvas id="elevation-chart"></canvas>
                    </div>
                    
                    <div class="controls">
                        <div class="control-group">
                            <h3><i class="fas fa-cogs"></i> Motor Control</h3>
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Current Angle: <span id="elevation-angle">0°</span></span>
                                    <span>Target: <span id="elevation-target">0°</span></span>
                                </div>
                                <input type="range" min="-30" max="90" value="0" class="slider" id="elevation-slider">
                            </div>
                            
                            <div class="btn-group">
                                <button class="btn btn-primary" id="elevation-up">
                                    <i class="fas fa-arrow-up"></i> Up
                                </button>
                                <button class="btn btn-primary" id="elevation-down">
                                    <i class="fas fa-arrow-down"></i> Down
                                </button>
                                <button class="btn btn-danger" id="elevation-stop">
                                    <i class="fas fa-stop"></i> Stop
                                </button>
                            </div>
                        </div>
                        
                        <div class="control-group">
                            <h3><i class="fas fa-tachometer-alt"></i> Settings</h3>
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Speed: <span id="elevation-speed">50%</span></span>
                                </div>
                                <input type="range" min="0" max="100" value="50" class="slider" id="elevation-speed-slider">
                            </div>
                            
                            <div class="btn-group">
                                <button class="btn btn-success">
                                    <i class="fas fa-save"></i> Save Position
                                </button>
                                <button class="btn btn-warning">
                                    <i class="fas fa-sync"></i> Calibrate
                                </button>
                            </div>
                        </div>
                    </div>
                    
                    <div class="status">
                        <div class="status-indicator">
                            <div class="indicator"></div>
                            <span>Motor Status: <strong>Running</strong></span>
                        </div>
                        <div>
                            <span>Last Update: <strong id="elevation-update">Just now</strong></span>
                        </div>
                    </div>
                </div>
                
                <div class="panel">
                    <div class="panel-header">
                        <i class="fas fa-chart-area"></i>
                        <h2>Elevation Profile</h2>
                    </div>
                    <div class="chart-container">
                        <canvas id="profile-chart"></canvas>
                    </div>
                    
                    <div class="controls">
                        <div class="control-group">
                            <h3><i class="fas fa-sliders-h"></i> Profile Settings</h3>
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Resolution: <span id="resolution-value">High</span></span>
                                </div>
                                <input type="range" min="1" max="3" value="2" class="slider" id="resolution-slider">
                            </div>
                            
                            <div class="slider-container">
                                <div class="slider-label">
                                    <span>Scan Interval: <span id="interval-value">500ms</span></span>
                                </div>
                                <input type="range" min="100" max="2000" value="500" class="slider" id="interval-slider">
                            </div>
                        </div>
                        
                        <div class="control-group">
                            <h3><i class="fas fa-broadcast-tower"></i> Detection</h3>
                            <div class="btn-group">
                                <button class="btn btn-primary">
                                    <i class="fas fa-play"></i> Start Scan
                                </button>
                                <button class="btn btn-danger">
                                    <i class="fas fa-stop"></i> Stop Scan
                                </button>
                            </div>
                            <div class="status" style="margin-top: 15px;">
                                <div class="status-indicator">
                                    <div class="indicator"></div>
                                    <span>Scan Status: <strong>Active</strong></span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="footer">
            <p>ESP32 Radar Control System &copy; 2023 | Real-time monitoring and control interface</p>
        </div>
    </div>

    <script>
        // Tab switching functionality
        document.querySelectorAll('.tab-btn').forEach(button => {
            button.addEventListener('click', () => {
                // Remove active class from all buttons and content
                document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
                document.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));
                
                // Add active class to clicked button
                button.classList.add('active');
                
                // Show corresponding content
                const tabId = button.getAttribute('data-tab');
                document.getElementById(`${tabId}-tab`).classList.add('active');
            });
        });
        
        // Initialize charts
        const ctxAzimuth = document.getElementById('azimuth-chart').getContext('2d');
        const ctxSignal = document.getElementById('signal-chart').getContext('2d');
        const ctxElevation = document.getElementById('elevation-chart').getContext('2d');
        const ctxProfile = document.getElementById('profile-chart').getContext('2d');
        
        // Radar chart for azimuth
        const azimuthChart = new Chart(ctxAzimuth, {
            type: 'radar',
            data: {
                labels: ['N', 'NE', 'E', 'SE', 'S', 'SW', 'W', 'NW'],
                datasets: [{
                    label: 'Signal Strength',
                    data: [85, 59, 90, 81, 56, 55, 40, 75],
                    fill: true,
                    backgroundColor: 'rgba(52, 152, 219, 0.2)',
                    borderColor: 'rgba(52, 152, 219, 1)',
                    pointBackgroundColor: 'rgba(52, 152, 219, 1)',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: 'rgba(52, 152, 219, 1)'
                }]
            },
            options: {
                elements: {
                    line: {
                        borderWidth: 3
                    }
                },
                scales: {
                    r: {
                        angleLines: {
                            display: true
                        },
                        suggestedMin: 0,
                        suggestedMax: 100
                    }
                },
                plugins: {
                    legend: {
                        display: false
                    }
                }
            }
        });
        
        // Signal strength chart
        const signalChart = new Chart(ctxSignal, {
            type: 'line',
            data: {
                labels: Array.from({length: 20}, (_, i) => i + 1),
                datasets: [{
                    label: 'Signal Strength',
                    data: Array.from({length: 20}, () => Math.floor(Math.random() * 100)),
                    borderColor: 'rgba(46, 204, 113, 1)',
                    backgroundColor: 'rgba(46, 204, 113, 0.1)',
                    tension: 0.4,
                    fill: true
                }]
            },
            options: {
                responsive: true,
                plugins: {
                    legend: {
                        display: false
                    }
                },
                scales: {
                    y: {
                        beginAtZero: true,
                        max: 100
                    }
                }
            }
        });
        
        // Elevation chart
        const elevationChart = new Chart(ctxElevation, {
            type: 'line',
            data: {
                labels: Array.from({length: 15}, (_, i) => i * 10),
                datasets: [{
                    label: 'Elevation Profile',
                    data: [0, 10, 20, 40, 60, 80, 70, 60, 50, 40, 30, 20, 10, 0, -10],
                    borderColor: 'rgba(231, 76, 60, 1)',
                    backgroundColor: 'rgba(231, 76, 60, 0.1)',
                    tension: 0.4,
                    fill: true
                }]
            },
            options: {
                responsive: true,
                plugins: {
                    legend: {
                        display: false
                    }
                },
                scales: {
                    y: {
                        min: -30,
                        max: 90
                    }
                }
            }
        });
        
        // Profile chart
        const profileChart = new Chart(ctxProfile, {
            type: 'bar',
            data: {
                labels: ['0-10°', '10-20°', '20-30°', '30-40°', '40-50°', '50-60°', '60-70°', '70-80°', '80-90°'],
                datasets: [{
                    label: 'Object Distribution',
                    data: [12, 19, 15, 24, 18, 22, 17, 14, 8],
                    backgroundColor: 'rgba(155, 89, 182, 0.7)',
                    borderColor: 'rgba(155, 89, 182, 1)',
                    borderWidth: 1
                }]
            },
            options: {
                responsive: true,
                plugins: {
                    legend: {
                        display: false
                    }
                },
                scales: {
                    y: {
                        beginAtZero: true
                    }
                }
            }
        });
        
        // Slider functionality
        document.getElementById('azimuth-slider').addEventListener('input', function() {
            document.getElementById('azimuth-target').textContent = this.value + '°';
            azimuthChart.data.datasets[0].data = generateRadarData(parseInt(this.value));
            azimuthChart.update();
        });
        
        document.getElementById('elevation-slider').addEventListener('input', function() {
            document.getElementById('elevation-target').textContent = this.value + '°';
            document.getElementById('elevation-angle').textContent = this.value + '°';
        });
        
        document.getElementById('speed-slider').addEventListener('input', function() {
            document.getElementById('azimuth-speed').textContent = this.value + '%';
        });
        
        document.getElementById('elevation-speed-slider').addEventListener('input', function() {
            document.getElementById('elevation-speed').textContent = this.value + '%';
        });
        
        document.getElementById('sensitivity-slider').addEventListener('input', function() {
            document.getElementById('sensitivity-value').textContent = this.value + '%';
        });
        
        document.getElementById('range-slider').addEventListener('input', function() {
            document.getElementById('range-value').textContent = this.value + 'm';
        });
        
        document.getElementById('resolution-slider').addEventListener('input', function() {
            const resolutions = ['Low', 'Medium', 'High'];
            document.getElementById('resolution-value').textContent = resolutions[this.value - 1];
        });
        
        document.getElementById('interval-slider').addEventListener('input', function() {
            document.getElementById('interval-value').textContent = this.value + 'ms';
        });
        
        // Motor control buttons
        document.getElementById('azimuth-cw').addEventListener('click', () => {
            const slider = document.getElementById('azimuth-slider');
            slider.value = parseInt(slider.value) + 10;
            if (slider.value > 360) slider.value = 360;
            slider.dispatchEvent(new Event('input'));
        });
        
        document.getElementById('azimuth-ccw').addEventListener('click', () => {
            const slider = document.getElementById('azimuth-slider');
            slider.value = parseInt(slider.value) - 10;
            if (slider.value < 0) slider.value = 0;
            slider.dispatchEvent(new Event('input'));
        });
        
        document.getElementById('elevation-up').addEventListener('click', () => {
            const slider = document.getElementById('elevation-slider');
            slider.value = parseInt(slider.value) + 5;
            if (slider.value > 90) slider.value = 90;
            slider.dispatchEvent(new Event('input'));
        });
        
        document.getElementById('elevation-down').addEventListener('click', () => {
            const slider = document.getElementById('elevation-slider');
            slider.value = parseInt(slider.value) - 5;
            if (slider.value < -30) slider.value = -30;
            slider.dispatchEvent(new Event('input'));
        });
        
        // Simulate live radar updates
        function updateRadar() {
            // Update signal chart
            const signalData = signalChart.data.datasets[0].data;
            signalData.shift();
            signalData.push(Math.floor(Math.random() * 100));
            signalChart.update();
            
            // Update profile chart
            const profileData = profileChart.data.datasets[0].data;
            for (let i = 0; i < profileData.length; i++) {
                profileData[i] = Math.max(0, profileData[i] + Math.floor(Math.random() * 5) - 2);
            }
            profileChart.update();
            
            // Update timestamps
            document.querySelectorAll('#azimuth-update, #elevation-update').forEach(el => {
                el.textContent = new Date().toLocaleTimeString();
            });
        }
        
        function generateRadarData(angle) {
            // Generate data with strongest signal at current angle
            const data = [];
            const directions = 8;
            const anglePerDir = 360 / directions;
            
            for (let i = 0; i < directions; i++) {
                const dirAngle = i * anglePerDir;
                const angleDiff = Math.min(
                    Math.abs(angle - dirAngle),
                    360 - Math.abs(angle - dirAngle)
                );
                const strength = Math.max(20, 100 - (angleDiff * 100 / 180));
                data.push(Math.floor(strength));
            }
            
            return data;
        }
        
        // Initialize with some data
        document.getElementById('azimuth-slider').dispatchEvent(new Event('input'));
        
        // Update every 2 seconds
        setInterval(updateRadar, 2000);
    </script>
</body>
</html>
