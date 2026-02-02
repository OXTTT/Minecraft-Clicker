<!DOCTYPE html>
<html lang="da">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minecraft Clicker</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Courier New', monospace;
            background: #1a1a1a;
            color: white;
            overflow-x: hidden;
        }
        
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .header {
            text-align: center;
            padding: 20px;
            border: 4px solid #555;
            background: #2a2a2a;
            margin-bottom: 20px;
        }
        
        .dimension-title {
            font-size: 32px;
            font-weight: bold;
            text-shadow: 2px 2px 4px black;
        }
        
        .stats {
            display: flex;
            justify-content: space-around;
            margin: 20px 0;
            flex-wrap: wrap;
        }
        
        .stat-box {
            background: #333;
            border: 3px solid #666;
            padding: 15px;
            margin: 5px;
            text-align: center;
            min-width: 150px;
        }
        
        .stat-label {
            color: #aaa;
            font-size: 12px;
        }
        
        .stat-value {
            font-size: 24px;
            font-weight: bold;
        }
        
        .click-area {
            text-align: center;
            margin: 30px 0;
        }
        
        .click-btn {
            width: 200px;
            height: 200px;
            font-size: 80px;
            cursor: pointer;
            border: none;
            background: #4a4a4a;
            border-radius: 20px;
            box-shadow: 0 10px 0 #2a2a2a, 0 10px 20px rgba(0,0,0,0.5);
            transition: all 0.1s;
        }
        
        .click-btn:active {
            transform: translateY(10px);
            box-shadow: 0 0 0 #2a2a2a, 0 0 10px rgba(0,0,0,0.5);
        }
        
        .section {
            background: #2a2a2a;
            border: 3px solid #555;
            margin: 20px 0;
            padding: 15px;
        }
        
        .section-title {
            font-size: 20px;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #555;
        }
        
        .upgrade {
            background: #333;
            border: 2px solid #666;
            margin: 10px 0;
            padding: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            transition: all 0.2s;
            user-select: none;
        }
        
        .upgrade:hover {
            background: #444;
            border-color: #888;
        }
        
        .upgrade.disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .upgrade.affordable {
            border-color: #0f0;
            background: #2a3a2a;
        }
        
        .upgrade-info {
            flex: 1;
        }
        
        .upgrade-name {
            font-weight: bold;
            margin-bottom: 5px;
        }
        
        .upgrade-desc {
            font-size: 12px;
            color: #aaa;
        }
        
        .upgrade-cost {
            background: #555;
            padding: 10px 20px;
            border-radius: 5px;
            font-weight: bold;
        }
        
        .dimensions {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            justify-content: center;
        }
        
        .dim-btn {
            padding: 15px 25px;
            border: 3px solid #666;
            background: #333;
            color: white;
            cursor: pointer;
            font-size: 16px;
            transition: all 0.2s;
        }
        
        .dim-btn:hover {
            background: #444;
        }
        
        .dim-btn.active {
            border-color: #0f0;
            background: #3a3a3a;
        }
        
        .dim-btn.locked {
            opacity: 0.3;
            cursor: not-allowed;
        }
        
        .unlock-btn {
            background: #2a5a2a;
            border-color: #4a4;
            width: 100%;
            padding: 20px;
            font-size: 18px;
            margin-top: 20px;
        }
        
        .unlock-btn:hover {
            background: #3a7a3a;
        }
        
        .floating-text {
            position: absolute;
            color: #0f0;
            font-weight: bold;
            font-size: 20px;
            pointer-events: none;
            animation: float 1s ease-out forwards;
        }
        
        @keyframes float {
            0% { opacity: 1; transform: translateY(0); }
            100% { opacity: 0; transform: translateY(-50px); }
        }
        
        .save-indicator {
            position: fixed;
            bottom: 10px;
            right: 10px;
            background: #2a5a2a;
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 12px;
            opacity: 0;
            transition: opacity 0.5s;
        }
        
        .save-indicator.show {
            opacity: 1;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <div class="dimension-title" id="dimTitle">🟫 OVERWORLD 🟫</div>
        </div>
        
        <div class="stats">
            <div class="stat-box">
                <div class="stat-label">VALUTA</div>
                <div class="stat-value" id="currency">0</div>
                <div id="currencyName">Dirt</div>
            </div>
            <div class="stat-box">
                <div class="stat-label">GLOBAL CLICK POWER</div>
                <div class="stat-value" id="clickPower">1</div>
            </div>
            <div class="stat-box">
                <div class="stat-label">TOTAL INCOME/s</div>
                <div class="stat-value" id="income">0</div>
            </div>
        </div>
        
        <div class="click-area">
            <button class="click-btn" id="clickBtn" onclick="game.click(event)">🟫</button>
        </div>
        
        <div class="section">
            <div class="section-title">CLICKER UPGRADE</div>
            <div class="upgrade" id="clickerUpgrade" onmousedown="game.startBuying('clicker')" onmouseup="game.stopBuying()" onmouseleave="game.stopBuying()" ontouchstart="game.startBuying('clicker')" ontouchend="game.stopBuying()">
                <div class="upgrade-info">
                    <div class="upgrade-name" id="clickerName">Global Clicker</div>
                    <div class="upgrade-desc">2x klik power i alle dimensioner</div>
                </div>
                <div class="upgrade-cost" id="clickerCost">100</div>
            </div>
        </div>
        
        <div class="section">
            <div class="section-title">FARMS</div>
            <div id="farms"></div>
        </div>
        
        <div class="section">
            <div class="section-title">DIMENSIONER</div>
            <div class="dimensions" id="dimensions"></div>
            <button class="dim-btn unlock-btn" id="unlockBtn" onclick="game.unlockNext()" style="display:none;">
                Lås op for næste dimension!
            </button>
        </div>
    </div>
    
    <div class="save-indicator" id="saveIndicator">Gemt!</div>

    <script>
        const DIMENSIONS = {
            overworld: {
                name: "Overworld",
                currency: "Dirt",
                symbol: "🟫",
                color: "#5a5",
                farms: [
                    {name: "Wheat Farm", baseCost: 100},
                    {name: "Cow Farm", baseCost: 500},
                    {name: "Iron Golem Farm", baseCost: 2500},
                    {name: "Sugarcane Farm", baseCost: 12500},
                    {name: "Tree Farm", baseCost: 62500},
                    {name: "Villager Trading", baseCost: 312500},
                    {name: "Raid Farm", baseCost: 1562500}
                ]
            },
            nether: {
                name: "Nether",
                currency: "Netherrack",
                symbol: "🟥",
                color: "#a55",
                farms: [
                    {name: "Nether Wart Farm", baseCost: 100000000},
                    {name: "Blaze Farm", baseCost: 500000000},
                    {name: "Ghast Farm", baseCost: 2500000000},
                    {name: "Magma Cube Farm", baseCost: 12500000000},
                    {name: "Piglin Bartering", baseCost: 62500000000},
                    {name: "Hoglin Farm", baseCost: 312500000000},
                    {name: "Wither Skeleton Farm", baseCost: 1562500000000}
                ]
            },
            end: {
                name: "The End",
                currency: "End Stone",
                symbol: "⬜",
                color: "#aa5",
                farms: [
                    {name: "Chorus Farm", baseCost: 100000000000},
                    {name: "Enderman Farm", baseCost: 500000000000},
                    {name: "Shulker Farm", baseCost: 2500000000000},
                    {name: "End Crystal Farm", baseCost: 12500000000000},
                    {name: "Void Platform", baseCost: 62500000000000},
                    {name: "Elytra Farm", baseCost: 312500000000000},
                    {name: "Dragon Respawn", baseCost: 1562500000000000}
                ]
            },
            deep_dark: {
                name: "Deep Dark",
                currency: "Sculk",
                symbol: "⬛",
                color: "#55a",
                farms: [
                    {name: "Sculk Catalyst", baseCost: 100000000000000},
                    {name: "Sculk Shrieker", baseCost: 500000000000000},
                    {name: "Ancient City Loot", baseCost: 2500000000000000},
                    {name: "Darkness Spreader", baseCost: 12500000000000000},
                    {name: "Sculk Sensor Grid", baseCost: 62500000000000000},
                    {name: "The Warden's Lair", baseCost: 312500000000000000},
                    {name: "Ancient Portal", baseCost: 1562500000000000000}
                ]
            },
            owner: {
                name: "Owner",
                currency: "Command Block",
                symbol: "🟨",
                color: "#a5a",
                farms: [
                    {name: "Diamond Generator", baseCost: 100000000000000000},
                    {name: "Netherite Generator", baseCost: 500000000000000000},
                    {name: "Beacon Farm", baseCost: 2500000000000000000},
                    {name: "Dragon Egg Farm", baseCost: 12500000000000000000},
                    {name: "Creative Mode", baseCost: 62500000000000000000},
                    {name: "Command Block", baseCost: 312500000000000000000},
                    {name: "World Edit", baseCost: 1562500000000000000000}
                ]
            }
        };

        class Game {
            constructor() {
                this.currentDim = 'overworld';
                this.currencies = {overworld: 0, nether: 0, end: 0, deep_dark: 0, owner: 0};
                this.globalClickPower = 1;
                this.clickerLevel = 0;
                this.clickerPrice = 100;
                this.farmCounts = {
                    overworld: [0,0,0,0,0,0,0],
                    nether: [0,0,0,0,0,0,0],
                    end: [0,0,0,0,0,0,0],
                    deep_dark: [0,0,0,0,0,0,0],
                    owner: [0,0,0,0,0,0,0]
                };
                this.unlockedDims = ['overworld'];
                this.lastTime = Date.now();
                this.lastSaveTime = Date.now();
                this.buyInterval = null;
                this.currentBuyTarget = null;
                
                this.load();
                this.init();
                this.loop();
            }
            
            save() {
                const data = {
                    currencies: this.currencies,
                    globalClickPower: this.globalClickPower,
                    clickerLevel: this.clickerLevel,
                    clickerPrice: this.clickerPrice,
                    farmCounts: this.farmCounts,
                    unlockedDims: this.unlockedDims,
                    currentDim: this.currentDim,
                    lastSaveTime: Date.now()
                };
                localStorage.setItem('minecraftClickerSave', JSON.stringify(data));
                
                const indicator = document.getElementById('saveIndicator');
                indicator.classList.add('show');
                setTimeout(() => indicator.classList.remove('show'), 2000);
            }
            
            load() {
                const saved = localStorage.getItem('minecraftClickerSave');
                if (saved) {
                    const data = JSON.parse(saved);
                    
                    const offlineTime = (Date.now() - data.lastSaveTime) / 1000;
                    
                    this.currencies = data.currencies || this.currencies;
                    this.globalClickPower = data.globalClickPower || 1;
                    this.clickerLevel = data.clickerLevel || 0;
                    this.clickerPrice = data.clickerPrice || 100;
                    this.farmCounts = data.farmCounts || this.farmCounts;
                    this.unlockedDims = data.unlockedDims || ['overworld'];
                    this.currentDim = data.currentDim || 'overworld';
                    
                    const offlineIncome = this.calculateTotalIncome() * offlineTime;
                    this.currencies[this.currentDim] += offlineIncome;
                    
                    if (offlineIncome > 0) {
                        alert(`Velkommen tilbage! Du har tjent ${this.formatNumber(offlineIncome)} mens du var væk!`);
                    }
                }
            }
            
            init() {
                this.renderDimensions();
                this.renderFarms();
                this.updateDisplay();
            }
            
            formatNumber(n) {
                if (n >= 1e18) return (n/1e18).toFixed(2) + 'Qi';
                if (n >= 1e15) return (n/1e15).toFixed(2) + 'Q';
                if (n >= 1e12) return (n/1e12).toFixed(2) + 'T';
                if (n >= 1e9) return (n/1e9).toFixed(2) + 'B';
                if (n >= 1e6) return (n/1e6).toFixed(2) + 'M';
                if (n >= 1e3) return (n/1e3).toFixed(2) + 'K';
                return Math.floor(n).toString();
            }
            
            getFarmCost(dim, idx) {
                const base = DIMENSIONS[dim].farms[idx].baseCost;
                const count = this.farmCounts[dim][idx];
                return base * Math.pow(1.15, count);
            }
            
            getFarmIncome(dim, idx) {
                const base = DIMENSIONS[dim].farms[idx].baseCost;
                return base * 0.05 * this.farmCounts[dim][idx];
            }
            
            calculateTotalIncome() {
                let total = 0;
                for (const dim of Object.keys(DIMENSIONS)) {
                    for (let i = 0; i < 7; i++) {
                        total += this.getFarmIncome(dim, i);
                    }
                }
                return total;
            }
            
            click(e) {
                this.currencies[this.currentDim] += this.globalClickPower;
                this.updateDisplay();
                
                if (e) {
                    const btn = e.target;
                    const rect = btn.getBoundingClientRect();
                    const float = document.createElement('div');
                    float.className = 'floating-text';
                    float.textContent = '+' + this.formatNumber(this.globalClickPower);
                    float.style.left = (rect.left + rect.width/2) + 'px';
                    float.style.top = (rect.top) + 'px';
                    document.body.appendChild(float);
                    setTimeout(() => float.remove(), 1000);
                }
            }
            
            buyClicker() {
                if (this.currencies[this.currentDim] >= this.clickerPrice) {
                    this.currencies[this.currentDim] -= this.clickerPrice;
                    this.globalClickPower *= 2;
                    this.clickerLevel++;
                    this.clickerPrice *= 3.5;
                    return true;
                }
                return false;
            }
            
            buyFarm(idx) {
                const cost = this.getFarmCost(this.currentDim, idx);
                if (this.currencies[this.currentDim] >= cost) {
                    this.currencies[this.currentDim] -= cost;
                    this.farmCounts[this.currentDim][idx]++;
                    return true;
                }
                return false;
            }
            
            startBuying(target) {
                this.currentBuyTarget = target;
                this.buyOnce();
                this.buyInterval = setInterval(() => {
                    this.buyOnce();
                }, 100);
            }
            
            stopBuying() {
                if (this.buyInterval) {
                    clearInterval(this.buyInterval);
                    this.buyInterval = null;
                }
                this.currentBuyTarget = null;
            }
            
            buyOnce() {
                if (this.currentBuyTarget === 'clicker') {
                    if (!this.buyClicker()) {
                        this.stopBuying();
                    }
                } else if (this.currentBuyTarget !== null) {
                    const idx = parseInt(this.currentBuyTarget);
                    if (!this.buyFarm(idx)) {
                        this.stopBuying();
                    }
                }
            }
            
            canUnlockNext() {
                return this.farmCounts[this.currentDim].every(count => count > 0);
            }
            
            unlockNext() {
                if (!this.canUnlockNext()) return;
                
                const dims = Object.keys(DIMENSIONS);
                const idx = dims.indexOf(this.currentDim);
                if (idx < dims.length - 1) {
                    const next = dims[idx + 1];
                    if (!this.unlockedDims.includes(next)) {
                        this.unlockedDims.push(next);
                    }
                    this.currentDim = next;
                    this.renderFarms();
                    this.renderDimensions();
                    this.updateDisplay();
                }
            }
            
            switchDim(dim) {
                if (this.unlockedDims.includes(dim)) {
                    this.currentDim = dim;
                    this.renderFarms();
                    this.renderDimensions();
                    this.updateDisplay();
                }
            }
            
            renderDimensions() {
                const container = document.getElementById('dimensions');
                container.innerHTML = '';
                
                const dims = Object.keys(DIMENSIONS);
                for (let i = 0; i < dims.length; i++) {
                    const key = dims[i];
                    const data = DIMENSIONS[key];
                    const btn = document.createElement('button');
                    btn.className = 'dim-btn';
                    
                    if (key === this.currentDim) {
                        btn.classList.add('active');
                    }
                    
                    if (!this.unlockedDims.includes(key)) {
                        btn.classList.add('locked');
                        btn.textContent = '🔒 ' + data.name;
                        btn.disabled = true;
                    } else {
                        btn.textContent = data.symbol + ' ' + data.name;
                        const self = this;
                        btn.onclick = function() {
                            self.switchDim(key);
                        };
                    }
                    
                    container.appendChild(btn);
                }
            }
            
            renderFarms() {
                const container = document.getElementById('farms');
                container.innerHTML = '';
                
                const dimData = DIMENSIONS[this.currentDim];
                
                for (let i = 0; i < 7; i++) {
                    const farm = dimData.farms[i];
                    const count = this.farmCounts[this.currentDim][i];
                    const cost = this.getFarmCost(this.currentDim, i);
                    const income = this.getFarmIncome(this.currentDim, i);
                    
                    const div = document.createElement('div');
                    div.className = 'upgrade';
                    
                    if (this.currencies[this.currentDim] >= cost) {
                        div.classList.add('affordable');
                    } else {
                        div.classList.add('disabled');
                    }
                    
                    div.innerHTML = `
                        <div class="upgrade-info">
                            <div class="upgrade-name">${farm.name}</div>
                            <div class="upgrade-desc">Ejet: ${count} | Income: ${this.formatNumber(income)}/s</div>
                        </div>
                        <div class="upgrade-cost">${this.formatNumber(cost)}</div>
                    `;
                    
                    const self = this;
                    const idx = i;
                    
                    div.onmousedown = function() {
                        self.startBuying(idx);
                    };
                    div.onmouseup = function() {
                        self.stopBuying();
                    };
                    div.onmouseleave = function() {
                        self.stopBuying();
                    };
                    div.ontouchstart = function() {
                        self.startBuying(idx);
                    };
                    div.ontouchend = function() {
                        self.stopBuying();
                    };
                    
                    container.appendChild(div);
                }
            }
            
            updateDisplay() {
                const dimData = DIMENSIONS[this.currentDim];
                
                document.getElementById('dimTitle').textContent = 
                    `${dimData.symbol} ${dimData.name.toUpperCase()} ${dimData.symbol}`;
                document.getElementById('dimTitle').style.color = dimData.color;
                
                document.getElementById('currency').textContent = 
                    this.formatNumber(this.currencies[this.currentDim]);
                document.getElementById('currencyName').textContent = dimData.currency;
                
                document.getElementById('clickPower').textContent = 
                    this.formatNumber(this.globalClickPower);
                
                document.getElementById('income').textContent = 
                    this.formatNumber(this.calculateTotalIncome());
                
                document.getElementById('clickBtn').textContent = dimData.symbol;
                
                document.getElementById('clickerName').textContent = 
                    `Global Clicker (Lvl ${this.clickerLevel})`;
                document.getElementById('clickerCost').textContent = 
                    this.formatNumber(this.clickerPrice);
                
                const clickerDiv = document.getElementById('clickerUpgrade');
                clickerDiv.classList.remove('affordable', 'disabled');
                if (this.currencies[this.currentDim] >= this.clickerPrice) {
                    clickerDiv.classList.add('affordable');
                } else {
                    clickerDiv.classList.add('disabled');
                }
                
                const unlockBtn = document.getElementById('unlockBtn');
                if (this.canUnlockNext()) {
                    const dims = Object.keys(DIMENSIONS);
                    const idx = dims.indexOf(this.currentDim);
                    if (idx < dims.length - 1) {
                        unlockBtn.style.display = 'block';
                        unlockBtn.textContent = `Lås op for ${DIMENSIONS[dims[idx+1]].name}!`;
                    } else {
                        unlockBtn.style.display = 'none';
                    }
                } else {
                    unlockBtn.style.display = 'none';
                }
                
                this.renderFarms();
            }
            
            loop() {
                const now = Date.now();
                const delta = (now - this.lastTime) / 1000;
                this.lastTime = now;
                
                const totalIncome = this.calculateTotalIncome();
                this.currencies[this.currentDim] += totalIncome * delta;
                
                if (now - this.lastSaveTime > 120000) {
                    this.save();
                    this.lastSaveTime = now;
                }
                
                this.updateDisplay();
                requestAnimationFrame(() => this.loop());
            }
        }

        const game = new Game();
        
        window.addEventListener('beforeunload', function() {
            game.save();
        });
    </script>
</body>
</html>
