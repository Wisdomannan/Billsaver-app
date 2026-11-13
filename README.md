<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BillSaver Pro</title>
    <link rel="manifest" href="manifest.json">
    <meta name="theme-color" content="#121212">
    <style>
        *{box-sizing:border-box;margin:0;padding:0;font-family:'Segoe UI',Tahoma,Geneva,Verdana,sans-serif}
        body{background:#121212;color:#fff;min-height:100vh;display:flex;flex-direction:column;align-items:center;padding:16px 16px 90px}
        h1{color:#f0c040;font-size:22px;margin-bottom:4px}
        .status{font-size:12px;color:#888;margin-bottom:16px}
        .status.stale{color:#e67e22;font-weight:bold}
        .container{width:100%;max-width:480px}
        .page{display:none;flex-direction:column;gap:14px}
        .page.active{display:flex}
        .card{background:#1e1e1e;border-radius:12px;padding:18px;box-shadow:0 4px 6px rgba(0,0,0,.3);border:1px solid #333}
        .card-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px;font-size:16px;font-weight:bold}
        .load-1{color:#3498db}
        .load-2{color:#2ecc71}
        .data-row{display:flex;justify-content:space-between;margin-bottom:8px;font-size:14px;color:#ccc}
        .data-row:last-of-type{margin-bottom:0}
        .data-value{font-weight:bold;color:#fff}
        .big-value{font-size:22px;font-weight:bold;color:#f0c040;margin-bottom:8px}
        .section-title{font-size:12px;color:#f0c040;margin:14px 0 8px;text-transform:uppercase;letter-spacing:.5px;font-weight:bold}
        .section-title:first-child{margin-top:0}
        .btn-toggle{width:100%;padding:14px;margin-top:16px;border:none;border-radius:8px;font-size:16px;font-weight:bold;cursor:pointer;transition:background .15s}
        .btn-on{background:#2ecc71;color:#121212}
        .btn-off{background:#e74c3c;color:#fff}
        .btn-sending{background:#666!important;color:#ddd!important;cursor:wait}
        .btn-toggle:active{transform:scale(.98)}
        .btn-primary{width:100%;padding:12px;margin-top:12px;border:none;border-radius:8px;font-size:15px;font-weight:bold;background:#f0c040;color:#121212;cursor:pointer}
        .btn-primary:active{transform:scale(.98)}
        .btn-primary:disabled{opacity:.5;cursor:not-allowed}
        .btn-danger{background:#e74c3c;color:#fff}
        .btn-secondary{width:100%;padding:10px;margin-top:8px;border:1px solid #555;border-radius:8px;font-size:14px;font-weight:bold;background:transparent;color:#ccc;cursor:pointer}
        .btn-secondary:active{background:#333}
        .alert{background:#e74c3c;color:#fff;padding:10px;border-radius:6px;text-align:center;display:none;margin-bottom:14px;font-size:13px}
        .budget-bar{background:#333;border-radius:8px;height:8px;width:100%;margin-top:10px;overflow:hidden}
        .budget-fill{height:100%;width:0;background:#2ecc71;transition:width .5s,background .5s}
        .tabs{display:flex;gap:6px;margin-bottom:14px}
        .tab{flex:1;padding:10px;background:#2a2a2a;border:1px solid #333;border-radius:6px;color:#aaa;cursor:pointer;font-size:13px;text-align:center;transition:background .15s}
        .tab.active{background:#f0c040;color:#121212;font-weight:bold;border-color:#f0c040}
        .chart{display:flex;align-items:flex-end;gap:5px;height:100px;margin:10px 0;padding:6px;background:#111;border-radius:8px}
        .chart-bar{flex:1;background:linear-gradient(to top,#2ecc71,#3498db);border-radius:3px 3px 0 0;min-height:3px;position:relative}
        .chart-bar-label{position:absolute;top:-16px;left:50%;transform:translateX(-50%);font-size:9px;color:#888;white-space:nowrap}
        .history-row{display:flex;justify-content:space-between;padding:8px 0;border-bottom:1px solid #2a2a2a;font-size:13px}
        .history-row:last-child{border-bottom:none}
        .history-date{color:#aaa}
        .summary-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:6px}
        .summary-box{background:#111;border-radius:8px;padding:10px;text-align:center}
        .summary-box .label{font-size:11px;color:#888;margin-bottom:4px;text-transform:uppercase;letter-spacing:.3px}
        .summary-box .value{font-size:16px;font-weight:bold;color:#f0c040}
        .summary-box .value-unit{font-size:11px;color:#888;margin-left:2px}
        .badge{display:inline-block;padding:2px 8px;border-radius:4px;font-size:11px;font-weight:bold}
        .badge-ok{background:#2ecc71;color:#121212}
        .badge-warn{background:#e67e22;color:#fff}
        .badge-bad{background:#e74c3c;color:#fff}
        .badge-off{background:#333;color:#888}
        .kv{display:flex;justify-content:space-between;font-size:13px;margin-bottom:8px;align-items:center}
        .kv:last-child{margin-bottom:0}
        .kv .k{color:#aaa}
        .kv .v{color:#fff;font-weight:bold}
        .timeline-item{border-left:3px solid #f0c040;padding:6px 10px;margin-bottom:8px;background:#252525;border-radius:4px;font-size:13px}
        .timeline-time{color:#888;font-size:11px;margin-bottom:3px}
        .empty{color:#666;font-size:13px;text-align:center;padding:20px 0}
        .input-row{display:flex;gap:8px;margin-bottom:10px;align-items:center}
        .input-row label{flex:1;color:#ccc;font-size:13px}
        .input-row input{width:130px;padding:8px 10px;background:#111;border:1px solid #444;color:#fff;border-radius:6px;font-size:14px;text-align:right}
        .input-row input[type=time]{width:140px;text-align:center}
        .input-row input:focus{outline:none;border-color:#f0c040}
        .checkbox-row{display:flex;gap:8px;align-items:center;margin-bottom:10px}
        .checkbox-row input{width:20px;height:20px}
        .checkbox-row label{color:#ccc;font-size:13px}
        .insight-card{background:#111;border-radius:8px;padding:14px;margin-bottom:12px;border-left:4px solid #3498db}
        .insight-card.good{border-left-color:#2ecc71}
        .insight-card.warn{border-left-color:#e67e22}
        .insight-card.danger{border-left-color:#e74c3c}
        .insight-title{font-size:13px;color:#888;text-transform:uppercase;letter-spacing:.3px;margin-bottom:6px}
        .insight-value{font-size:18px;font-weight:bold;color:#fff;margin-bottom:4px}
        .insight-detail{font-size:13px;color:#bbb}
        .tip-card{background:#252525;border-radius:8px;padding:12px 14px;margin-bottom:10px;font-size:13px;color:#ddd;border-left:3px solid #f0c040}
        .tip-card.warn{border-left-color:#e67e22}
        .tip-card.danger{border-left-color:#e74c3c}
        .alert-card{background:#2a1515;border-radius:8px;padding:12px 14px;margin-bottom:10px;font-size:13px;color:#ddd;border-left:3px solid #e74c3c}
        .alert-card.warn{background:#2a1f0a;border-left-color:#e67e22}
        .alert-card.info{background:#0f1e2a;border-left-color:#3498db}
        .notif-banner{background:#1a3a2a;border:1px solid #2ecc71;border-radius:8px;padding:10px 14px;margin-bottom:14px;font-size:13px;display:none;align-items:center;justify-content:space-between}
        .notif-banner button{background:#2ecc71;color:#121212;border:none;padding:6px 12px;border-radius:6px;font-weight:bold;font-size:12px;cursor:pointer}
        .tabbar{position:fixed;bottom:0;left:0;right:0;background:#1a1a1a;border-top:1px solid #333;display:flex;justify-content:space-around;padding:8px 0 max(8px,env(safe-area-inset-bottom));z-index:100}
        .tabbar-btn{flex:1;text-align:center;padding:6px 2px;color:#666;font-size:10px;cursor:pointer;transition:color .15s;user-select:none;position:relative}
        .tabbar-btn .icon{display:block;font-size:18px;margin-bottom:2px}
        .tabbar-btn.active{color:#f0c040}
        .tabbar-btn .dot{position:absolute;top:4px;right:25%;width:8px;height:8px;background:#e74c3c;border-radius:50%;display:none}
        .tabbar-btn.has-notif .dot{display:block}
        #bell{position:absolute;top:14px;right:18px;font-size:22px;cursor:pointer;color:#f0c040;z-index:50}
        #bell .bell-dot{position:absolute;top:0;right:0;width:10px;height:10px;background:#e74c3c;border-radius:50%;display:none}
        #bell.has-notif .bell-dot{display:block}
        #login-screen{position:fixed;inset:0;background:#121212;z-index:1000;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:20px;overflow-y:auto}
        #login-screen h1{margin-bottom:6px}
        #login-screen .subtitle{color:#888;font-size:12px;margin-bottom:22px}
        #login-screen .form-wrap{width:100%;max-width:320px;display:flex;flex-direction:column;gap:10px}
        #login-screen input{width:100%;padding:12px;border-radius:8px;border:1px solid #444;background:#222;color:#fff;font-size:14px}
        #login-screen input:focus{outline:none;border-color:#f0c040}
        #login-screen button{padding:12px;border-radius:8px;border:none;background:#f0c040;color:#121212;font-weight:bold;cursor:pointer;font-size:14px}
        #login-screen button:disabled{opacity:.5;cursor:not-allowed}
        #login-screen .switch-text{color:#888;font-size:13px;text-align:center;margin-top:14px}
        #login-screen .switch-text a{color:#f0c040;cursor:pointer;text-decoration:underline}
        #login-screen .error{color:#e74c3c;font-size:13px;text-align:center;min-height:18px}
        #login-screen .link-row{display:flex;justify-content:center;gap:16px;margin-top:10px;font-size:12px}
        #login-screen .link-row a{color:#3498db;cursor:pointer;text-decoration:none}
        .modal-bg{position:fixed;inset:0;background:rgba(0,0,0,.85);z-index:2000;display:none;justify-content:center;align-items:center;padding:20px}
        .modal-bg.show{display:flex}
        .modal{background:#1e1e1e;border:1px solid #444;border-radius:12px;max-width:520px;width:100%;max-height:80vh;display:flex;flex-direction:column}
        .modal-header{padding:16px 18px;border-bottom:1px solid #333;display:flex;justify-content:space-between;align-items:center;font-weight:bold;color:#f0c040}
        .modal-header button{background:transparent;border:none;color:#e74c3c;font-size:22px;cursor:pointer;line-height:1}
        .modal-body{padding:18px;overflow-y:auto;font-size:13px;line-height:1.6;color:#ccc}
        .modal-body h3{color:#f0c040;margin:14px 0 8px;font-size:14px}
        .modal-body h3:first-child{margin-top:0}
        .modal-body p{margin-bottom:10px}
        .modal-body ul{margin:6px 0 10px 20px}
        .modal-body li{margin-bottom:4px}
    </style>
</head>
<body>

<div id="bell" onclick="switchPage('events')">🔔<span class="bell-dot"></span></div>

<!-- LOGIN / SIGNUP SCREEN -->
<div id="login-screen">
    <h1>BillSaver Pro</h1>
    <div class="subtitle">A product of Nineotech</div>
    <div class="form-wrap">
        <div class="tabs">
            <div class="tab active" id="tab-login" onclick="switchAuthTab('login')">Log In</div>
            <div class="tab" id="tab-signup" onclick="switchAuthTab('signup')">Sign Up</div>
        </div>

        <div id="form-login" style="display:flex;flex-direction:column;gap:10px;">
            <input type="email" id="login-email" placeholder="Email" autocomplete="email">
            <input type="password" id="login-password" placeholder="Password" autocomplete="current-password">
            <button onclick="loginUser()">Log In</button>
            <div class="link-row"><a onclick="forgotPassword()">Forgot password?</a></div>
        </div>

        <div id="form-signup" style="display:none;flex-direction:column;gap:10px;">
            <input type="email" id="signup-email" placeholder="Email" autocomplete="email">
            <input type="password" id="signup-password" placeholder="Password (min 6 characters)" autocomplete="new-password">
            <input type="password" id="signup-password2" placeholder="Confirm password" autocomplete="new-password">
            <div class="checkbox-row" style="margin-bottom:0;">
                <input type="checkbox" id="signup-tos">
                <label for="signup-tos" style="font-size:12px;">I agree to the <a style="color:#3498db;cursor:pointer;" onclick="showTerms()">Terms of Service</a> and <a style="color:#3498db;cursor:pointer;" onclick="showPrivacy()">Privacy Policy</a></label>
            </div>
            <button onclick="signupUser()">Create Account</button>
        </div>

        <div class="error" id="auth-error"></div>
    </div>
</div>

<!-- MAIN APP -->
<div id="main-app" style="display:none;width:100%;align-items:center;flex-direction:column;">
    <h1>BillSaver Pro</h1>

    <div class="container" style="margin-bottom:10px;">
        <div class="card" style="padding:10px 14px;display:flex;flex-direction:column;gap:8px;">
            <div style="display:flex;justify-content:space-between;align-items:center;">
                <span style="font-weight:bold;color:#ccc;">Device:</span>
                <select id="device-select" onchange="changeDevice()" style="padding:6px;border-radius:6px;background:#222;color:#fff;border:1px solid #444;width:60%;"></select>
                <button onclick="logoutUser()" style="background:transparent;border:none;color:#e74c3c;cursor:pointer;font-weight:bold;">Logout</button>
            </div>
            <div style="display:flex;gap:5px;">
                <input type="text" id="claim-input" placeholder="6-digit Code" style="flex:1;padding:6px;border-radius:6px;border:1px solid #444;background:#111;color:#fff;">
                <button onclick="claimDevice()" style="padding:6px 12px;border-radius:6px;border:none;background:#2ecc71;color:#121212;font-weight:bold;cursor:pointer;">Claim</button>
            </div>
        </div>
    </div>

    <div class="status" id="connection-status">Connecting...</div>
    <div class="alert" id="alert-box">No connection to Supabase.</div>
    <div class="notif-banner" id="notif-banner">
        <span>Enable phone notifications for important alerts</span>
        <button onclick="requestNotifPermission()">Enable</button>
    </div>

    <div class="container">
        <div class="page active" id="page-live">
            <div class="card">
                <div class="card-header">
                    <span class="load-1" id="title-l1">Load 1</span>
                    <div>
                        <span id="l1-status" style="color:#e74c3c;margin-right:10px;">OFF</span>
                        <span style="cursor:pointer;font-size:14px;" onclick="renameLoad(1)" title="Rename Load 1">✏️</span>
                    </div>
                </div>
                <div class="big-value" id="l1-power">0 W</div>
                <div class="data-row"><span>Today</span><span class="data-value" id="l1-energy">0.000 kWh</span></div>
                <div class="data-row"><span>Cost today</span><span class="data-value" id="l1-cost" style="color:#f0c040;">GHS 0.00</span></div>
                <div class="data-row"><span>Runtime today</span><span class="data-value" id="l1-runtime">0h 0m</span></div>
                <div class="data-row" style="margin-top:12px;font-size:12px;color:#888;"><span>Daily budget</span><span id="l1-budget-text">0%</span></div>
                <div class="budget-bar"><div class="budget-fill" id="l1-budget-fill"></div></div>
                <button class="btn-toggle btn-off" id="l1-btn" onclick="toggleRelay(1)">Turn ON</button>
                <button class="btn-primary btn-danger" id="l1-clear" style="display:none;" onclick="clearTrip(1)">Clear Load 1 trip</button>
                <button class="btn-secondary" id="l1-reset" onclick="resetEnergy(1)">Reset today (L1)</button>
            </div>
            <div class="card">
                <div class="card-header">
                    <span class="load-2" id="title-l2">Load 2</span>
                    <div>
                        <span id="l2-status" style="color:#e74c3c;margin-right:10px;">OFF</span>
                        <span style="cursor:pointer;font-size:14px;" onclick="renameLoad(2)" title="Rename Load 2">✏️</span>
                    </div>
                </div>
                <div class="big-value" id="l2-power">0 W</div>
                <div class="data-row"><span>Today</span><span class="data-value" id="l2-energy">0.000 kWh</span></div>
                <div class="data-row"><span>Cost today</span><span class="data-value" id="l2-cost" style="color:#f0c040;">GHS 0.00</span></div>
                <div class="data-row"><span>Runtime today</span><span class="data-value" id="l2-runtime">0h 0m</span></div>
                <div class="data-row" style="margin-top:12px;font-size:12px;color:#888;"><span>Daily budget</span><span id="l2-budget-text">0%</span></div>
                <div class="budget-bar"><div class="budget-fill" id="l2-budget-fill"></div></div>
                <button class="btn-toggle btn-off" id="l2-btn" onclick="toggleRelay(2)">Turn ON</button>
                <button class="btn-primary btn-danger" id="l2-clear" style="display:none;" onclick="clearTrip(2)">Clear Load 2 trip</button>
                <button class="btn-secondary" id="l2-reset" onclick="resetEnergy(2)">Reset today (L2)</button>
            </div>
        </div>

        <div class="page" id="page-history">
            <div class="card">
                <div class="card-header"><span>History</span></div>
                <div class="tabs">
                    <div class="tab active" id="hist-tab-1" onclick="setHistLoad(1)">Load 1</div>
                    <div class="tab" id="hist-tab-2" onclick="setHistLoad(2)">Load 2</div>
                </div>
                <div class="section-title">Summary</div>
                <div class="summary-grid">
                    <div class="summary-box"><div class="label">Today</div><div class="value" id="h-today">0.000<span class="value-unit">kWh</span></div></div>
                    <div class="summary-box"><div class="label">Yesterday</div><div class="value" id="h-yesterday">0.000<span class="value-unit">kWh</span></div></div>
                    <div class="summary-box"><div class="label">This week</div><div class="value" id="h-week">0.000<span class="value-unit">kWh</span></div></div>
                    <div class="summary-box"><div class="label">Last 30 days</div><div class="value" id="h-month">0.000<span class="value-unit">kWh</span></div></div>
                </div>
                <div class="section-title">Last 7 days</div>
                <div class="chart" id="hist-chart"><div class="empty">Waiting for first midnight rollover...</div></div>
                <div class="section-title">Last 30 days</div>
                <div id="hist-list"><div class="empty">No data yet</div></div>
                <div class="kv" style="margin-top:14px;border-top:1px solid #333;padding-top:12px;"><span class="k">Last calendar month total</span><span class="v" id="hist-last-month">—</span></div>
            </div>
        </div>

        <div class="page" id="page-insights">
            <div class="card">
                <div class="card-header"><span>Insights</span></div>
                <div class="tabs">
                    <div class="tab active" id="ins-tab-1" onclick="setInsightLoad(1)">Load 1</div>
                    <div class="tab" id="ins-tab-2" onclick="setInsightLoad(2)">Load 2</div>
                </div>
                <div id="insight-container"><div class="empty">Collecting data...</div></div>
            </div>
            <div class="card">
                <div class="card-header"><span>Smart tips</span></div>
                <div id="tips-container"><div class="empty">No tips right now</div></div>
            </div>
        </div>

        <div class="page" id="page-health">
            <div class="card"><div class="card-header"><span>Device health</span></div>
                <div class="kv"><span class="k">AP clients</span><span class="v" id="h-ap">0</span></div>
                <div class="kv"><span class="k">STA connection</span><span class="v" id="h-sta">—</span></div>
                <div class="kv"><span class="k">Free heap</span><span class="v" id="h-heap">—</span></div>
                <div class="kv"><span class="k">Uptime</span><span class="v" id="h-uptime">—</span></div>
                <div class="kv"><span class="k">Firmware</span><span class="v" id="h-firmware">—</span></div>
                <button class="btn-secondary" id="ota-btn" style="margin-top:14px;" onclick="checkForOta()">Check for updates</button>
                <button class="btn-primary" id="reboot-btn" style="margin-top:8px;" onclick="rebootDevice()">Reboot device</button>
            </div>
            <div class="card"><div class="card-header"><span>Protection trips</span></div>
                <div class="kv"><span class="k">Load 1</span><span class="v" id="h-trips1">—</span></div>
                <div class="kv"><span class="k">Load 2</span><span class="v" id="h-trips2">—</span></div>
                <div class="kv"><span class="k">Load 1 voltage recovery</span><span class="v" id="h-vrec1">—</span></div>
                <div class="kv"><span class="k">Load 2 voltage recovery</span><span class="v" id="h-vrec2">—</span></div>
            </div>
            <div class="card"><div class="card-header"><span>Active alerts</span></div><div id="alerts-container"><div class="empty">No active alerts</div></div></div>
            <div class="card">
                <div class="card-header"><span>Budget settings</span></div>
                <div class="section-title">Load 1</div>
                <div class="input-row"><label>Daily budget (GHS)</label><input type="number" id="edit-budget1" min="0" step="0.5" value="20.00"></div>
                <button class="btn-primary" onclick="saveBudget(1)">Save Load 1 budget</button>
                <div class="section-title">Load 2</div>
                <div class="input-row"><label>Daily budget (GHS)</label><input type="number" id="edit-budget2" min="0" step="0.5" value="20.00"></div>
                <button class="btn-primary" onclick="saveBudget(2)">Save Load 2 budget</button>
            </div>
            <div class="card">
                <div class="card-header"><span>Schedule settings</span></div>
                <div class="section-title">Load 1</div>
                <div class="checkbox-row"><input type="checkbox" id="sched1-enabled"><label for="sched1-enabled">Enable schedule</label></div>
                <div class="input-row"><label>ON time</label><input type="time" id="sched1-on" value="06:00"></div>
                <div class="input-row"><label>OFF time</label><input type="time" id="sched1-off" value="12:00"></div>
                <button class="btn-primary" onclick="saveSchedule(1)">Save Load 1 schedule</button>
                <div class="section-title">Load 2</div>
                <div class="checkbox-row"><input type="checkbox" id="sched2-enabled"><label for="sched2-enabled">Enable schedule</label></div>
                <div class="input-row"><label>ON time</label><input type="time" id="sched2-on" value="14:00"></div>
                <div class="input-row"><label>OFF time</label><input type="time" id="sched2-off" value="20:00"></div>
                <button class="btn-primary" onclick="saveSchedule(2)">Save Load 2 schedule</button>
            </div>
            <div class="card"><div class="card-header"><span>Tariff</span></div>
                <div class="kv"><span class="k">Rate (set in Supabase)</span><span class="v" id="h-tariff">GHS 0.00/kWh</span></div>
                <div style="font-size:12px;color:#888;margin-top:8px;">Edit <code>global_settings.tariff_rate</code> in Supabase. Device picks it up within 5 minutes.</div>
            </div>
            <div class="card" style="margin-top:14px;">
                <div class="card-header"><span>Settings & Legal</span></div>
                <div class="kv" style="cursor:pointer;" onclick="showPrivacy()"><span class="k" style="color:#3498db;">Privacy Policy</span><span class="v">View</span></div>
                <div class="kv" style="cursor:pointer;margin-top:12px;" onclick="showTerms()"><span class="k" style="color:#3498db;">Terms of Service</span><span class="v">View</span></div>
                <div class="kv" style="cursor:pointer;margin-top:12px;" onclick="deleteAccount()"><span class="k" style="color:#e74c3c;">Delete Account</span><span class="v">Delete</span></div>
                <div style="font-size:11px;color:#666;text-align:center;margin-top:20px;padding-top:10px;border-top:1px solid #333;">
                    BillSaver Pro v1.0<br>
                    A product of <strong>Nineotech</strong><br>
                    © 2026 Nineotech. All rights reserved.<br>
                    Contact: annanwisdom834@gmail.com
                </div>
            </div>
        </div>

        <div class="page" id="page-events">
            <div class="card"><div class="card-header"><span>Recent activity</span></div><div id="timeline-container"><div class="empty">Waiting for events...</div></div></div>
            <div class="card"><div class="card-header"><span>Event log (last 20)</span></div><div id="events-container"><div class="empty">No events yet</div></div></div>
        </div>
    </div>

    <div class="tabbar">
        <div class="tabbar-btn active" data-page="live" onclick="switchPage('live')"><span class="icon">⚡</span>Live</div>
        <div class="tabbar-btn" data-page="history" onclick="switchPage('history')"><span class="icon">📊</span>History</div>
        <div class="tabbar-btn" data-page="insights" onclick="switchPage('insights')"><span class="icon">💡</span>Insights</div>
        <div class="tabbar-btn" data-page="health" onclick="switchPage('health')"><span class="icon">🩺</span>Health<span class="dot"></span></div>
        <div class="tabbar-btn" data-page="events" onclick="switchPage('events')"><span class="icon">📜</span>Events</div>
    </div>
</div>

<div class="modal-bg" id="modal-bg" onclick="if(event.target===this)closeModal()">
    <div class="modal">
        <div class="modal-header"><span id="modal-title">Terms</span><button onclick="closeModal()">×</button></div>
        <div class="modal-body" id="modal-body"></div>
    </div>
</div>

<script>
const SUPABASE_URL = "https://pjsbtgfmcgtnvyxpoenq.supabase.co";
const SUPABASE_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InBqc2J0Z2ZtY2d0bnZ5eHBvZW5xIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODYwMTk2OTUsImV4cCI6MjEwMTU5NTY5NX0.ce6E6qzafaCuUrMJ0sNmr9dMPv83fuePrVSp0dvq4KY";

let authToken = null;
let currentUser = null;
let activeDeviceId = null;
let userDevices = [];

const pending = { 1: null, 2: null };
const resetPending = { 1: null, 2: null };

let lastReading = null;
let lastSuccessTime = 0;
let fetchInFlight = false;
let histLoad = 1;
let insightLoad = 1;
let notifShown = {};
let rebootPending = null;
let lastEventCheckCount = 0;

let loadNames = { 1: "Load 1", 2: "Load 2" };
let pollDataTimer = null;

// ============================================================
// AUTH TOKEN PERSISTENCE (localStorage)
// ============================================================
function saveSession() {
    try {
        if (authToken && currentUser) {
            localStorage.setItem('bs_token', authToken);
            localStorage.setItem('bs_user', JSON.stringify(currentUser));
        }
    } catch (e) {}
}
function clearSession() {
    try {
        localStorage.removeItem('bs_token');
        localStorage.removeItem('bs_user');
    } catch (e) {}
}
function restoreSession() {
    try {
        const t = localStorage.getItem('bs_token');
        const u = localStorage.getItem('bs_user');
        if (t && u) {
            authToken = t;
            currentUser = JSON.parse(u);
            return true;
        }
    } catch (e) {}
    return false;
}

// ============================================================
// HEADERS
// ============================================================
function getHeaders() {
    const h = { "apikey": SUPABASE_KEY, "Content-Type": "application/json" };
    h["Authorization"] = "Bearer " + (authToken || SUPABASE_KEY);
    return h;
}

async function fetchWithTimeout(url, opts = {}) {
    const controller = new AbortController();
    const timer = setTimeout(() => controller.abort(), 8000);
    try {
        const res = await fetch(url, { ...opts, signal: controller.signal });
        return res;
    } finally {
        clearTimeout(timer);
    }
}

// ============================================================
// AUTH UI — TABS
// ============================================================
function switchAuthTab(which) {
    document.getElementById('tab-login').classList.toggle('active', which === 'login');
    document.getElementById('tab-signup').classList.toggle('active', which === 'signup');
    document.getElementById('form-login').style.display = which === 'login' ? 'flex' : 'none';
    document.getElementById('form-signup').style.display = which === 'signup' ? 'flex' : 'none';
    document.getElementById('auth-error').innerText = '';
}

function setAuthError(msg) {
    document.getElementById('auth-error').innerText = msg || '';
}

// ============================================================
// AUTH — LOG IN
// ============================================================
async function loginUser() {
    const email = document.getElementById('login-email').value.trim();
    const password = document.getElementById('login-password').value;
    if (!email || !password) { setAuthError('Enter email and password'); return; }
    setAuthError('Logging in...');
    try {
        const res = await fetch(`${SUPABASE_URL}/auth/v1/token?grant_type=password`, {
            method: "POST",
            headers: { "apikey": SUPABASE_KEY, "Content-Type": "application/json" },
            body: JSON.stringify({ email, password })
        });
        const data = await res.json();
        if (!res.ok) throw new Error(data.error_description || data.msg || "Login failed");
        authToken = data.access_token;
        currentUser = data.user;
        saveSession();
        await enterApp();
    } catch (e) {
        setAuthError(e.message);
    }
}

// ============================================================
// AUTH — SIGN UP
// ============================================================
async function signupUser() {
    const email = document.getElementById('signup-email').value.trim();
    const p1 = document.getElementById('signup-password').value;
    const p2 = document.getElementById('signup-password2').value;
    const tos = document.getElementById('signup-tos').checked;

    if (!email) { setAuthError('Enter email'); return; }
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) { setAuthError('Invalid email format'); return; }
    if (p1.length < 6) { setAuthError('Password must be at least 6 characters'); return; }
    if (p1 !== p2) { setAuthError('Passwords do not match'); return; }
    if (!tos) { setAuthError('You must accept the Terms and Privacy Policy'); return; }

    setAuthError('Creating account...');
    try {
        const res = await fetch(`${SUPABASE_URL}/auth/v1/signup`, {
            method: "POST",
            headers: { "apikey": SUPABASE_KEY, "Content-Type": "application/json" },
            body: JSON.stringify({ email, password: p1 })
        });
        const data = await res.json();
        if (!res.ok) throw new Error(data.msg || data.error_description || "Sign up failed");

        // Email confirmation OFF → signup returns access_token → auto-login
        if (data.access_token && data.user) {
            authToken = data.access_token;
            currentUser = data.user;
            saveSession();
            await enterApp();
        } else {
            // Fallback: try to log in immediately
            const loginRes = await fetch(`${SUPABASE_URL}/auth/v1/token?grant_type=password`, {
                method: "POST",
                headers: { "apikey": SUPABASE_KEY, "Content-Type": "application/json" },
                body: JSON.stringify({ email, password: p1 })
            });
            const loginData = await loginRes.json();
            if (!loginRes.ok) throw new Error("Account created but login failed. Try logging in.");
            authToken = loginData.access_token;
            currentUser = loginData.user;
            saveSession();
            await enterApp();
        }
    } catch (e) {
        setAuthError(e.message);
    }
}

// ============================================================
// FORGOT PASSWORD
// ============================================================
async function forgotPassword() {
    const email = prompt('Enter your email to reset password:');
    if (!email) return;
    try {
        const res = await fetch(`${SUPABASE_URL}/auth/v1/recover`, {
            method: "POST",
            headers: { "apikey": SUPABASE_KEY, "Content-Type": "application/json" },
            body: JSON.stringify({ email })
        });
        if (res.ok) alert('If that email exists, a reset link has been sent.');
        else alert('Failed to send reset link.');
    } catch (e) { alert('Error: ' + e.message); }
}

// ============================================================
// LOGOUT
// ============================================================
function logoutUser() {
    clearSession();
    authToken = null;
    currentUser = null;
    activeDeviceId = null;
    userDevices = [];
    if (pollDataTimer) { clearInterval(pollDataTimer); pollDataTimer = null; }
    document.getElementById('main-app').style.display = 'none';
    document.getElementById('login-screen').style.display = 'flex';
    document.getElementById('login-email').value = '';
    document.getElementById('login-password').value = '';
    document.getElementById('signup-email').value = '';
    document.getElementById('signup-password').value = '';
    document.getElementById('signup-password2').value = '';
    document.getElementById('signup-tos').checked = false;
    setAuthError('');
    switchAuthTab('login');
}

// ============================================================
// ENTER APP (after login or signup)
// ============================================================
async function enterApp() {
    document.getElementById('login-screen').style.display = 'none';
    document.getElementById('main-app').style.display = 'flex';
    document.getElementById('bell').style.display = 'block';
    await loadUserDevices();
    if (!pollDataTimer) pollDataTimer = setInterval(fetchData, 3000);
}

// ============================================================
// DEVICES
// ============================================================
async function loadUserDevices() {
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/user_devices?user_id=eq.${currentUser.id}`, { headers: getHeaders() });
        userDevices = await res.json();
        const select = document.getElementById('device-select');
        select.innerHTML = '';
        if (!userDevices || userDevices.length === 0) {
            select.innerHTML = '<option value="">No devices claimed</option>';
            activeDeviceId = null;
            document.getElementById('connection-status').innerText = "Claim a device to begin";
            return;
        }
        userDevices.forEach(d => {
            const opt = document.createElement('option');
            opt.value = d.device_id;
            opt.innerText = d.device_id;
            select.appendChild(opt);
        });
        if (!activeDeviceId || !userDevices.find(d => d.device_id === activeDeviceId)) {
            activeDeviceId = userDevices[0].device_id;
        }
        select.value = activeDeviceId;
        await loadDeviceSettings();
        fetchData();
    } catch (e) {
        console.log("Failed to load devices", e);
    }
}

function changeDevice() {
    const select = document.getElementById('device-select');
    activeDeviceId = select.value;
    if (activeDeviceId) { loadDeviceSettings(); fetchData(); }
}

async function claimDevice() {
    const code = document.getElementById('claim-input').value.trim();
    if (!code) return alert("Enter a claim code");
    const btn = event.target;
    btn.innerText = "Checking...";
    btn.disabled = true;
    try {
        const devRes = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/devices?claim_code=eq.${code}&select=*`, { headers: getHeaders() });
        if (!devRes.ok) throw new Error("Database error: " + devRes.status);
        const devData = await devRes.json();
        if (!devData || devData.length === 0) throw new Error("Invalid claim code");
        const targetDeviceId = devData[0].device_id;
        if (userDevices && Array.isArray(userDevices)) {
            if (userDevices.find(d => d.device_id === targetDeviceId)) throw new Error("You already own this device");
        }
        const linkRes = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/user_devices`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ user_id: currentUser.id, device_id: targetDeviceId })
        });
        if (!linkRes.ok) throw new Error("Failed to link device: " + linkRes.status);
        showToast("Device claimed successfully!");
        document.getElementById('claim-input').value = '';
        await loadUserDevices();
    } catch (e) {
        showToast(e.message, true);
    } finally {
        btn.innerText = "Claim";
        btn.disabled = false;
    }
}

// ============================================================
// DEVICE SETTINGS (LOAD NAMES)
// ============================================================
async function loadDeviceSettings() {
    if (!activeDeviceId) return;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/user_devices?user_id=eq.${currentUser.id}&device_id=eq.${activeDeviceId}`, { headers: getHeaders() });
        const data = await res.json();
        if (data && data.length > 0) {
            loadNames[1] = data[0].load1_name || "Load 1";
            loadNames[2] = data[0].load2_name || "Load 2";
        } else { loadNames[1] = "Load 1"; loadNames[2] = "Load 2"; }
        updateLoadTitles();
    } catch (e) { console.log(e); }
}

async function renameLoad(loadNum) {
    if (!activeDeviceId) return;
    const currentName = loadNames[loadNum];
    const newName = prompt(`Enter new name for Load ${loadNum}:`, currentName);
    if (!newName || newName.trim() === "" || newName === currentName) return;
    const cleanName = newName.trim();
    const payload = {};
    if (loadNum === 1) payload.load1_name = cleanName;
    if (loadNum === 2) payload.load2_name = cleanName;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/user_devices?user_id=eq.${currentUser.id}&device_id=eq.${activeDeviceId}`, {
            method: "PATCH", headers: getHeaders(),
            body: JSON.stringify(payload)
        });
        if (!res.ok) throw new Error("Failed to save");
        loadNames[loadNum] = cleanName;
        showToast(`Renamed to ${cleanName}`);
        updateLoadTitles();
    } catch (e) {
        showToast("Failed to save name", true);
    }
}

function updateLoadTitles() {
    document.getElementById('title-l1').innerText = loadNames[1];
    document.getElementById('title-l2').innerText = loadNames[2];
    document.getElementById('hist-tab-1').innerText = loadNames[1];
    document.getElementById('hist-tab-2').innerText = loadNames[2];
    document.getElementById('ins-tab-1').innerText = loadNames[1];
    document.getElementById('ins-tab-2').innerText = loadNames[2];
}

// ============================================================
// POLLING — LIVE DATA
// ============================================================
async function fetchData() {
    if (!activeDeviceId) return;
    if (fetchInFlight) return;
    fetchInFlight = true;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/power_readings?device_id=eq.${activeDeviceId}&order=id.desc&limit=1`, { headers: getHeaders() });
        if (!res.ok) throw new Error('HTTP ' + res.status);
        const data = await res.json();
        if (!data || data.length === 0) {
            document.getElementById('connection-status').innerText = "Waiting for data from " + activeDeviceId;
            return;
        }
        const r = data[0];
        lastReading = r;
        lastSuccessTime = Date.now();

        document.getElementById('l1-power').innerText = `${(r.load1_power||0).toFixed(0)} W`;
        document.getElementById('l1-energy').innerText = `${(r.load1_energy||0).toFixed(3)} kWh`;
        document.getElementById('l1-cost').innerText = `GHS ${(r.load1_cost||0).toFixed(2)}`;
        document.getElementById('l1-runtime').innerText = fmtDuration(r.runtime1_today);
        let p1 = (r.load1_cost && r.budget_limit1) ? (r.load1_cost / r.budget_limit1) * 100 : 0;
        if (p1 > 100) p1 = 100;
        document.getElementById('l1-budget-text').innerText = `${p1.toFixed(0)}%`;
        document.getElementById('l1-budget-fill').style.width = `${p1}%`;
        document.getElementById('l1-budget-fill').style.background = p1 > 80 ? '#e74c3c' : '#2ecc71';
        reconcile(1, r.load1_state);

        document.getElementById('l2-power').innerText = `${(r.load2_power||0).toFixed(0)} W`;
        document.getElementById('l2-energy').innerText = `${(r.load2_energy||0).toFixed(3)} kWh`;
        document.getElementById('l2-cost').innerText = `GHS ${(r.load2_cost||0).toFixed(2)}`;
        document.getElementById('l2-runtime').innerText = fmtDuration(r.runtime2_today);
        let p2 = (r.load2_cost && r.budget_limit2) ? (r.load2_cost / r.budget_limit2) * 100 : 0;
        if (p2 > 100) p2 = 100;
        document.getElementById('l2-budget-text').innerText = `${p2.toFixed(0)}%`;
        document.getElementById('l2-budget-fill').style.width = `${p2}%`;
        document.getElementById('l2-budget-fill').style.background = p2 > 80 ? '#e74c3c' : '#2ecc71';
        reconcile(2, r.load2_state);

        const tripAny1 = r.trip_amp1 || r.trip_volt1 || r.trip_budget1 || r.trip_idle1;
        const tripAny2 = r.trip_amp2 || r.trip_volt2 || r.trip_budget2 || r.trip_idle2;
        document.getElementById('l1-clear').style.display = tripAny1 ? 'block' : 'none';
        document.getElementById('l2-clear').style.display = tripAny2 ? 'block' : 'none';

        document.getElementById('h-ap').innerText = r.ap_clients ?? "—";
        document.getElementById('h-sta').innerHTML = r.sta_connected ? '<span class="badge badge-ok">Connected</span>' : '<span class="badge badge-bad">Offline</span>';
        document.getElementById('h-heap').innerText = fmtBytes(r.free_heap);
        document.getElementById('h-uptime').innerText = r.uptime_seconds ? fmtDuration(r.uptime_seconds) : "—";
        document.getElementById('h-tariff').innerText = `GHS ${(r.tariff_rate||0).toFixed(2)}/kWh`;

        const mkTrip = (arr) => arr.length ? arr.map(k => `<span class="badge badge-bad">${k}</span>`).join(" ") : '<span class="badge badge-ok">OK</span>';
        const t1 = []; if (r.trip_amp1) t1.push("amp"); if (r.trip_volt1) t1.push("volt"); if (r.trip_budget1) t1.push("budget"); if (r.trip_idle1) t1.push("idle");
        document.getElementById('h-trips1').innerHTML = mkTrip(t1);
        const t2 = []; if (r.trip_amp2) t2.push("amp"); if (r.trip_volt2) t2.push("volt"); if (r.trip_budget2) t2.push("budget"); if (r.trip_idle2) t2.push("idle");
        document.getElementById('h-trips2').innerHTML = mkTrip(t2);

        document.getElementById('h-vrec1').innerText = `${(r.voltage_recovery_accum1||0)/1000}s / 30s`;
        document.getElementById('h-vrec2').innerText = `${(r.voltage_recovery_accum2||0)/1000}s / 30s`;

        if (!document.activeElement || !['edit-budget1','edit-budget2','sched1-on','sched1-off','sched2-on','sched2-off'].includes(document.activeElement.id)) {
            document.getElementById('edit-budget1').value = (r.budget_limit1 || 20).toFixed(2);
            document.getElementById('edit-budget2').value = (r.budget_limit2 || 20).toFixed(2);
            document.getElementById('sched1-enabled').checked = !!r.schedule1_enabled;
            document.getElementById('sched1-on').value = fmtHM(r.schedule1_on_hour||0, r.schedule1_on_min||0);
            document.getElementById('sched1-off').value = fmtHM(r.schedule1_off_hour||0, r.schedule1_off_min||0);
            document.getElementById('sched2-enabled').checked = !!r.schedule2_enabled;
            document.getElementById('sched2-on').value = fmtHM(r.schedule2_on_hour||0, r.schedule2_on_min||0);
            document.getElementById('sched2-off').value = fmtHM(r.schedule2_off_hour||0, r.schedule2_off_min||0);
        }

        const age = Date.now() - new Date(r.created_at + "Z").getTime();
        const cs = document.getElementById('connection-status');
        if (age > 10000) {
            cs.innerText = `Last update: ${fmtDuration(age/1000)} ago`;
            cs.classList.add('stale');
        } else {
            cs.innerText = "Live & Connected";
            cs.classList.remove('stale');
        }
        cs.style.color = age > 10000 ? "#e67e22" : "#2ecc71";
        document.getElementById('alert-box').style.display = "none";

        if (document.getElementById('page-insights').classList.contains('active')) renderInsights();
        if (document.getElementById('page-health').classList.contains('active')) renderAlerts();
    } catch (err) {
        console.log("fetchData err:", err.message);
        const cs = document.getElementById('connection-status');
        cs.innerText = "Connection Error";
        cs.classList.add('stale');
        cs.style.color = "#e74c3c";
    } finally {
        fetchInFlight = false;
    }
}

// ============================================================
// NAVIGATION — INCLUDING EVENTS FIX
// ============================================================
function switchPage(name) {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById('page-' + name).classList.add('active');
    document.querySelectorAll('.tabbar-btn').forEach(b => b.classList.toggle('active', b.dataset.page === name));
    if (name === 'history')  fetchHistory();
    if (name === 'insights') renderInsights();
    if (name === 'health')   renderAlerts();
    if (name === 'events') { fetchTimeline(); fetchEvents(); clearBellDot(); }
}

// ============================================================
// STATE RECONCILE
// ============================================================
function applyState(loadNum, isOn) {
    const s = document.getElementById(`l${loadNum}-status`);
    const b = document.getElementById(`l${loadNum}-btn`);
    if (isOn) {
        s.innerText = "ON"; s.style.color = "#2ecc71";
        b.innerText = "Turn OFF"; b.className = "btn-toggle btn-on";
    } else {
        s.innerText = "OFF"; s.style.color = "#e74c3c";
        b.innerText = "Turn ON"; b.className = "btn-toggle btn-off";
    }
}

function reconcile(loadNum, dbState) {
    const p = pending[loadNum];
    if (p) {
        if (p.value === dbState) { pending[loadNum] = null; applyState(loadNum, dbState); }
        else if (Date.now() - p.ts < 15000) { applyState(loadNum, p.value); }
        else { pending[loadNum] = null; applyState(loadNum, dbState); }
    } else applyState(loadNum, dbState);
}

// ============================================================
// FORMATTERS
// ============================================================
const fmtDuration = s => { s = Math.round(s||0); return `${Math.floor(s/3600)}h ${Math.floor((s%3600)/60)}m`; };
const fmtBytes = n => !n ? "—" : n >= 1024 ? (n/1024).toFixed(1) + " KB" : n + " B";
const fmtYmd = y => { const s = String(y); if (s.length !== 8) return s; const m = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"]; return m[+s.slice(4,6)-1] + " " + (+s.slice(6,8)) + ", " + s.slice(0,4); };
const fmtYmdShort = y => { const s = String(y); if (s.length !== 8) return s; const m = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"]; return m[+s.slice(4,6)-1] + " " + (+s.slice(6,8)); };
const fmtHM = (h,m) => String(h).padStart(2,'0') + ":" + String(m).padStart(2,'0');
// ============================================================
// HISTORY
// ============================================================
function setHistLoad(n) {
    histLoad = n;
    document.getElementById('hist-tab-1').classList.toggle('active', n === 1);
    document.getElementById('hist-tab-2').classList.toggle('active', n === 2);
    fetchHistory();
}

async function fetchHistory() {
    if (!activeDeviceId) return;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/daily_energy?device_id=eq.${activeDeviceId}&load_num=eq.${histLoad}&order=ymd.desc&limit=30`, { headers: getHeaders() });
        const rows = await res.json();
        renderHistory(rows);
    } catch (e) { console.log("History error", e.message); }
}

function renderHistory(rows) {
    const chartEl = document.getElementById('hist-chart');
    const listEl = document.getElementById('hist-list');
    const lastMonthEl = document.getElementById('hist-last-month');

    if (lastReading) {
        const pre = histLoad === 1 ? '1' : '2';
        document.getElementById('h-today').innerHTML = `${(lastReading[`load${pre}_energy`] || 0).toFixed(3)}<span class="value-unit">kWh</span>`;
        document.getElementById('h-yesterday').innerHTML = `${(lastReading[`yesterday_energy${pre}`]|| 0).toFixed(3)}<span class="value-unit">kWh</span>`;
        document.getElementById('h-week').innerHTML = `${(lastReading[`weekly_energy${pre}`]   || 0).toFixed(3)}<span class="value-unit">kWh</span>`;
        document.getElementById('h-month').innerHTML = `${(lastReading[`monthly_energy${pre}`]  || 0).toFixed(3)}<span class="value-unit">kWh</span>`;
    }

    if (!rows || rows.length === 0) {
        chartEl.innerHTML = '<div class="empty">Waiting for first midnight rollover...</div>';
        listEl.innerHTML = '<div class="empty">No data yet</div>';
        lastMonthEl.innerText = '—';
        return;
    }

    const recent = rows.slice(0, 7).reverse();
    const maxE = Math.max(...recent.map(r => r.energy || 0), 0.01);
    chartEl.innerHTML = recent.map(r => {
        const h = Math.max(3, Math.round(((r.energy || 0) / maxE) * 70));
        return `<div class="chart-bar" style="height:${h}px;"><div class="chart-bar-label">${fmtYmdShort(r.ymd).split(' ')[1]}</div></div>`;
    }).join('');

    listEl.innerHTML = rows.map(r => `<div class="history-row"><span class="history-date">${fmtYmd(r.ymd)}</span><span>${(r.energy||0).toFixed(3)} kWh · GHS ${(r.cost||0).toFixed(2)}</span></div>`).join('');

    const now = new Date();
    let prevY = now.getFullYear(), prevM = now.getMonth() - 1;
    if (prevM < 0) { prevM = 11; prevY -= 1; }
    const from = prevY * 10000 + (prevM + 1) * 100 + 1;
    const to   = now.getFullYear() * 10000 + (now.getMonth() + 1) * 100 + 1;
    let total = 0, count = 0;
    for (const r of rows) { if (r.ymd >= from && r.ymd < to) { total += r.energy || 0; count++; } }
    lastMonthEl.innerText = count > 0 ? `${total.toFixed(2)} kWh` : '—';
}

// ============================================================
// INSIGHTS
// ============================================================
function setInsightLoad(n) {
    insightLoad = n;
    document.getElementById('ins-tab-1').classList.toggle('active', n === 1);
    document.getElementById('ins-tab-2').classList.toggle('active', n === 2);
    renderInsights();
}

async function renderInsights() {
    if (!activeDeviceId) return;
    const pre = insightLoad === 1 ? '1' : '2';
    const today     = lastReading ? (lastReading[`load${pre}_energy`]     || 0) : 0;
    const yesterday = lastReading ? (lastReading[`yesterday_energy${pre}`]|| 0) : 0;

    let rows = [];
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/daily_energy?device_id=eq.${activeDeviceId}&load_num=eq.${insightLoad}&order=ymd.desc&limit=60`, { headers: getHeaders() });
        rows = await res.json();
    } catch (e) {}

    const container = document.getElementById('insight-container');
    const tips = document.getElementById('tips-container');
    const cards = [];

    if (yesterday > 0.001) {
        const pct = ((today - yesterday) / yesterday) * 100;
        const cls = pct > 20 ? (pct > 50 ? 'danger' : 'warn') : (pct < -10 ? 'good' : '');
        cards.push(`<div class="insight-card ${cls}"><div class="insight-title">Today vs yesterday</div>
            <div class="insight-value">${today.toFixed(3)} kWh</div>
            <div class="insight-detail">${pct >= 0 ? '↑' : '↓'} ${Math.abs(pct).toFixed(0)}% ${pct >= 0 ? 'more' : 'less'} than yesterday (${yesterday.toFixed(3)} kWh)</div></div>`);
    }

    if (rows && rows.length >= 3) {
        const sum = rows.reduce((a, r) => a + (r.energy || 0), 0);
        const avg = sum / rows.length;
        cards.push(`<div class="insight-card"><div class="insight-title">Average per day (last ${rows.length} days)</div>
            <div class="insight-value">${avg.toFixed(3)} kWh</div>
            <div class="insight-detail">≈ GHS ${(avg * (lastReading?.tariff_rate || 0)).toFixed(2)} per day</div></div>`);
    }

    container.innerHTML = cards.length ? cards.join('') : '<div class="empty">Not enough data yet. Come back tomorrow.</div>';

    const tipsArr = [];
    const r = lastReading;
    if (r) {
        const is1 = insightLoad === 1;
        const power     = is1 ? r.load1_power   : r.load2_power;
        const isOn      = is1 ? r.load1_state   : r.load2_state;
        const costToday = is1 ? r.load1_cost    : r.load2_cost;
        const budget    = is1 ? r.budget_limit1 : r.budget_limit2;
        const runtime   = is1 ? r.runtime1_today : r.runtime2_today;

        if (isOn && power > 0 && power < 8) tipsArr.push({ level: 'warn', msg: `${loadNames[insightLoad]} is ON but only drawing ${power.toFixed(0)} W. That's idle load.` });
        if (budget > 0 && costToday > 0) {
            const pct = (costToday / budget) * 100;
            if (pct > 100)     tipsArr.push({ level: 'danger', msg: `${loadNames[insightLoad]} exceeded daily budget: GHS ${costToday.toFixed(2)} / ${budget.toFixed(2)}.` });
            else if (pct > 80) tipsArr.push({ level: 'warn', msg: `${loadNames[insightLoad]} at ${pct.toFixed(0)}% of daily budget.` });
        }
        if (runtime > 8 * 3600) tipsArr.push({ level: 'warn', msg: `${loadNames[insightLoad]} running ${Math.floor(runtime/3600)} h today.` });
    }

    tips.innerHTML = tipsArr.length
        ? tipsArr.map(t => `<div class="tip-card ${t.level}">${t.msg}</div>`).join('')
        : '<div class="empty">No tips right now.</div>';
}

// ============================================================
// ALERTS + NOTIFICATIONS
// ============================================================
function renderAlerts() {
    const container = document.getElementById('alerts-container');
    const r = lastReading;
    const alerts = [];
    if (!r) { container.innerHTML = '<div class="empty">No alerts</div>'; return; }

    if (!r.sta_connected) alerts.push({ level: 'danger', msg: 'Device is offline from WiFi. Data may be stale.' });
    if (r.free_heap && r.free_heap < 50000) alerts.push({ level: 'warn', msg: `Free heap low (${fmtBytes(r.free_heap)}).` });
    if (r.uptime_seconds && r.uptime_seconds < 60) alerts.push({ level: 'info', msg: `Device just rebooted (uptime ${fmtDuration(r.uptime_seconds)}).` });
    if (r.created_at) {
        const ageSec = (Date.now() - new Date(r.created_at + "Z").getTime()) / 1000;
        if (ageSec > 120) alerts.push({ level: 'warn', msg: `No data from device for ${fmtDuration(ageSec)}.` });
    }
    if (r.trip_amp1)    alerts.push({ level: 'danger', msg: 'Load 1 overcurrent trip.' });
    if (r.trip_amp2)    alerts.push({ level: 'danger', msg: 'Load 2 overcurrent trip.' });
    if (r.trip_volt1)   alerts.push({ level: 'warn',   msg: 'Load 1 voltage trip.' });
    if (r.trip_volt2)   alerts.push({ level: 'warn',   msg: 'Load 2 voltage trip.' });
    if (r.trip_budget1) alerts.push({ level: 'warn',   msg: 'Load 1 budget trip.' });
    if (r.trip_budget2) alerts.push({ level: 'warn',   msg: 'Load 2 budget trip.' });
    if (r.trip_idle1)   alerts.push({ level: 'info',   msg: 'Load 1 idle trip.' });
    if (r.trip_idle2)   alerts.push({ level: 'info',   msg: 'Load 2 idle trip.' });

    container.innerHTML = alerts.length
        ? alerts.map(a => `<div class="alert-card ${a.level}">${a.msg}</div>`).join('')
        : '<div class="empty">No active alerts</div>';

    const danger = alerts.find(a => a.level === 'danger');
    if (danger) showPhoneNotification('BillSaver Alert', danger.msg);
    document.querySelector('.tabbar-btn[data-page="health"]').classList.toggle('has-notif', alerts.length > 0);
}

function requestNotifPermission() {
    if (!('Notification' in window)) return alert('Notifications not supported');
    Notification.requestPermission().then(p => {
        if (p === 'granted') {
            document.getElementById('notif-banner').style.display = 'none';
            try { new Notification('BillSaver', { body: 'Notifications enabled!' }); } catch (e) {}
        }
    });
}

function showPhoneNotification(title, body) {
    if (!('Notification' in window) || Notification.permission !== 'granted') return;
    const key = title + '|' + body;
    if (notifShown[key] && Date.now() - notifShown[key] < 5*60*1000) return;
    notifShown[key] = Date.now();
    try { new Notification(title, { body }); } catch (e) {}
}

function initNotifBanner() {
    if (!('Notification' in window)) return;
    if (Notification.permission === 'default') document.getElementById('notif-banner').style.display = 'flex';
}

// ============================================================
// EVENTS — FIXED
// ============================================================
async function fetchTimeline() {
    if (!activeDeviceId) return;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/timeline_events?device_id=eq.${activeDeviceId}&order=id.desc&limit=10`, { headers: getHeaders() });
        const events = await res.json();
        const c = document.getElementById('timeline-container');
        if (!events || events.length === 0) { c.innerHTML = '<div class="empty">No events yet</div>'; return; }
        c.innerHTML = events.map(ev => `<div class="timeline-item"><div class="timeline-time">${ev.event_type} — Load ${ev.load_num} · ${ev.created_at ? new Date(ev.created_at + "Z").toLocaleString() : ''}</div>${ev.description || ''}${ev.power_at_event != null ? ` (${Number(ev.power_at_event).toFixed(0)} W)` : ''}</div>`).join('');
    } catch (e) { console.log("Timeline error", e.message); }
}

async function fetchEvents() {
    if (!activeDeviceId) return;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/event_log?device_id=eq.${activeDeviceId}&order=id.desc&limit=20`, { headers: getHeaders() });
        const events = await res.json();
        const c = document.getElementById('events-container');
        if (!events || events.length === 0) { c.innerHTML = '<div class="empty">No events yet</div>'; return; }
        c.innerHTML = events.map(ev => {
            const t = ev.created_at ? new Date(ev.created_at + "Z").toLocaleString() : '—';
            return `<div class="history-row"><span class="history-date">${t}</span><span>${ev.message || ''}</span></div>`;
        }).join('');
        // Bell dot: if new events since last seen
        if (events.length > lastEventCheckCount) {
            document.getElementById('bell').classList.add('has-notif');
            lastEventCheckCount = events.length;
        }
    } catch (e) { console.log("Events error", e.message); }
}

function clearBellDot() {
    document.getElementById('bell').classList.remove('has-notif');
}

// ============================================================
// RELAY / TRIP / RESET
// ============================================================
async function toggleRelay(loadNum) {
    if (!activeDeviceId) return;
    const statusEl = document.getElementById(`l${loadNum}-status`);
    const btnEl    = document.getElementById(`l${loadNum}-btn`);
    const currentState = statusEl.innerText === "ON";
    const newState = !currentState;
    pending[loadNum] = { value: newState, ts: Date.now() };
    btnEl.classList.add('btn-sending');
    applyState(loadNum, newState);
    const cmd = loadNum === 1 ? (currentState ? "LOAD1_OFF" : "LOAD1_ON") : (currentState ? "LOAD2_OFF" : "LOAD2_ON");
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ device_id: activeDeviceId, command: cmd, executed: false })
        });
        if (!res.ok) throw new Error('HTTP ' + res.status);
    } catch (err) {
        showToast(`Failed to send command`, true);
        pending[loadNum] = null;
        applyState(loadNum, currentState);
    } finally {
        btnEl.classList.remove('btn-sending');
    }
}

async function clearTrip(loadNum) {
    if (!activeDeviceId) return;
    try {
        await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ device_id: activeDeviceId, command: `CLEAR_TRIP${loadNum}`, executed: false })
        });
        showToast(`Clear trip sent for Load ${loadNum}`);
    } catch (e) { showToast("Failed to send clear command", true); }
}

async function resetEnergy(loadNum) {
    if (!activeDeviceId) return;
    const btn = document.getElementById(`l${loadNum}-reset`);
    const now = Date.now();
    const p = resetPending[loadNum];
    if (!p || now - p > 5000) {
        resetPending[loadNum] = now;
        btn.innerText = "Tap again to confirm (5s)";
        setTimeout(() => {
            if (resetPending[loadNum] === now) {
                resetPending[loadNum] = null;
                btn.innerText = `Reset today (L${loadNum})`;
            }
        }, 5000);
        return;
    }
    resetPending[loadNum] = null;
    btn.innerText = `Reset today (L${loadNum})`;
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ device_id: activeDeviceId, command: `RESET_ENERGY${loadNum}`, executed: false })
        });
        if (!res.ok) throw new Error('HTTP ' + res.status);
        showToast(`Reset sent for Load ${loadNum}`);
    } catch (e) { showToast("Failed to send reset", true); }
}

async function checkForOta() {
    if (!activeDeviceId) return;
    const btn = document.getElementById('ota-btn');
    btn.innerText = "Sending OTA command...";
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ device_id: activeDeviceId, command: "OTA_CHECK", executed: false })
        });
        if (!res.ok) throw new Error('HTTP ' + res.status);
        showToast("OTA check command sent");
        setTimeout(() => { btn.innerText = "Check for updates"; }, 3000);
    } catch (e) {
        showToast("Failed to send OTA command", true);
        btn.innerText = "Check for updates";
    }
}

async function rebootDevice() {
    if (!activeDeviceId) return;
    const btn = document.getElementById('reboot-btn');
    const now = Date.now();
    if (!rebootPending || now - rebootPending > 5000) {
        rebootPending = now;
        btn.innerText = "Tap again to confirm (5s)";
        btn.style.background = "#e67e22";
        setTimeout(() => {
            if (rebootPending === now) {
                rebootPending = null;
                btn.innerText = "Reboot device";
                btn.style.background = "";
            }
        }, 5000);
        return;
    }
    rebootPending = null;
    btn.innerText = "Reboot device";
    btn.style.background = "";
    try {
        const res = await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ device_id: activeDeviceId, command: "REBOOT", executed: false })
        });
        if (!res.ok) throw new Error('HTTP ' + res.status);
        showToast("Reboot command sent");
    } catch (e) { showToast("Failed to send reboot", true); }
}

async function saveBudget(loadNum) {
    if (!activeDeviceId) return;
    const v = parseFloat(document.getElementById(`edit-budget${loadNum}`).value);
    if (isNaN(v) || v <= 0) return alert("Enter GHS > 0");
    try {
        await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({ device_id: activeDeviceId, command: `SET_BUDGET${loadNum}`, payload: { limit: v }, executed: false })
        });
        showToast(`Budget L${loadNum} sent: GHS ${v.toFixed(2)}`);
    } catch (e) { showToast("Failed to save budget", true); }
}

async function saveSchedule(loadNum) {
    if (!activeDeviceId) return;
    const enabled = document.getElementById(`sched${loadNum}-enabled`).checked;
    const onStr  = document.getElementById(`sched${loadNum}-on`).value;
    const offStr = document.getElementById(`sched${loadNum}-off`).value;
    if (!onStr || !offStr) return alert("Set both ON and OFF");
    const [onH, onM] = onStr.split(':').map(Number);
    const [offH, offM] = offStr.split(':').map(Number);
    if (onH === offH && onM === offM) return alert("ON and OFF cannot be same");
    try {
        await fetchWithTimeout(`${SUPABASE_URL}/rest/v1/commands`, {
            method: "POST", headers: getHeaders(),
            body: JSON.stringify({
                device_id: activeDeviceId,
                command: `SET_SCHEDULE${loadNum}`,
                payload: { enabled, on_hour: onH, on_min: onM, off_hour: offH, off_min: offM },
                executed: false
            })
        });
        showToast(`Schedule L${loadNum} sent`);
    } catch (e) { showToast("Failed to save schedule", true); }
}

// ============================================================
// TOAST
// ============================================================
function showToast(msg, isError = false) {
    const t = document.createElement('div');
    t.style.cssText = `position:fixed;bottom:100px;left:50%;transform:translateX(-50%);background:${isError ? '#e74c3c' : '#2ecc71'};color:${isError ? '#fff' : '#121212'};padding:10px 18px;border-radius:8px;font-weight:bold;font-size:13px;z-index:200;`;
    t.innerText = msg;
    document.body.appendChild(t);
    setTimeout(() => t.remove(), 3000);
}

// ============================================================
// MODAL — TERMS / PRIVACY
// ============================================================
function openModal(title, html) {
    document.getElementById('modal-title').innerText = title;
    document.getElementById('modal-body').innerHTML = html;
    document.getElementById('modal-bg').classList.add('show');
}
function closeModal() {
    document.getElementById('modal-bg').classList.remove('show');
}

function showTerms() {
    openModal('Terms of Service', `
<h3>1. Acceptance</h3>
<p>By creating an account and using BillSaver Pro (the "Service"), you agree to these Terms. If you do not agree, do not use the Service.</p>
<h3>2. The Service</h3>
<p>BillSaver Pro is a software application by Nineotech that displays electrical energy data from compatible hardware and allows you to control relays remotely. The Service depends on WiFi connectivity and Supabase cloud services. Interruptions may occur.</p>
<h3>3. Safety</h3>
<p>BillSaver Pro is a monitoring and convenience tool. It is <strong>not</strong> a substitute for proper electrical safety devices (breakers, fuses, earth leakage protection). Do not rely on it as your only protection against electric shock or fire.</p>
<h3>4. Account</h3>
<p>You are responsible for keeping your password confidential. You may not share your account. One account may claim multiple devices.</p>
<h3>5. Acceptable Use</h3>
<p>You may not use the Service to interfere with electrical equipment you do not own or control, or in any way that violates Ghanaian law.</p>
<h3>6. Limitation of Liability</h3>
<p>To the maximum extent permitted by law, Nineotech is not liable for any indirect, incidental, or consequential damages arising from use of the Service, including but not limited to energy loss, equipment damage, or data loss.</p>
<h3>7. Changes</h3>
<p>We may update these Terms. Continued use after an update constitutes acceptance.</p>
<h3>8. Governing Law</h3>
<p>These Terms are governed by the laws of the Republic of Ghana.</p>
<h3>9. Contact</h3>
<p>annanwisdom834@gmail.com</p>
    `);
}

function showPrivacy() {
    openModal('Privacy Policy', `
<h3>1. Data We Collect</h3>
<ul>
<li>Email address (for login)</li>
<li>Account ID (auto-generated)</li>
<li>Device telemetry: power, voltage, current, energy, runtime, event logs</li>
<li>Device identification: device ID, claim code</li>
</ul>
<h3>2. How We Use Data</h3>
<p>To display your energy usage, calculate costs, send safety alerts, and provide the Service. We do not sell your data.</p>
<h3>3. Storage & Security</h3>
<p>Data is stored in Supabase (PostgreSQL) with encryption in transit. Access is controlled by Row Level Security.</p>
<h3>4. Your Rights (Ghana Data Protection Act, 2012, Act 843)</h3>
<ul>
<li>Request a copy of your data</li>
<li>Request correction of your data</li>
<li>Request deletion of your account and data</li>
</ul>
<p>To exercise these rights, email <strong>annanwisdom834@gmail.com</strong>.</p>
<h3>5. Third Parties</h3>
<p>Data is processed by Supabase (hosting + database) and Vercel/Netlify (static hosting). No other third parties receive your data.</p>
<h3>6. Retention</h3>
<p>We retain data while your account is active. On deletion, data is removed within 30 days.</p>
<h3>7. Contact</h3>
<p>annanwisdom834@gmail.com</p>
<p style="color:#888;font-size:12px;margin-top:16px;">Placeholder contact — replace with support@nineotech.com before public launch.</p>
    `);
}

function deleteAccount() {
    if (!confirm("Are you sure you want to delete your account? This will permanently remove all your data and disconnect your device. This cannot be undone.")) return;
    alert("Account deletion requires a direct request. Please email annanwisdom834@gmail.com with your account email to complete deletion.");
    logoutUser();
}

// ============================================================
// VISIBILITY (pause polling when tab hidden)
// ============================================================
document.addEventListener('visibilitychange', () => {
    if (document.hidden) {
        if (pollDataTimer) { clearInterval(pollDataTimer); pollDataTimer = null; }
    } else {
        fetchData();
        if (!pollDataTimer) pollDataTimer = setInterval(fetchData, 3000);
    }
});

// ============================================================
// BOOT
// ============================================================
document.addEventListener('DOMContentLoaded', () => {
    document.getElementById('bell').style.display = 'none';
    initNotifBanner();
    if (restoreSession()) {
        enterApp();
    } else {
        document.getElementById('login-screen').style.display = 'flex';
    }
    setInterval(() => {
        if (document.getElementById('page-history').classList.contains('active')) fetchHistory();
    }, 30000);
    setInterval(() => {
        if (activeDeviceId && document.getElementById('main-app').style.display !== 'none') fetchEvents();
    }, 15000);
});
</script>
</body>
</html>
