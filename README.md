# EX01 Developing a Simple Webserver
## Date: 15-10-2026
## REG NO: 26003789

## AIM:
To develop a simple webserver to serve html pages and display the list of protocols in TCP/IP Protocol Suite.

## DESIGN STEPS:
### Step 1: 
HTML content creation.

### Step 2:
Design of webserver workflow.

### Step 3:
Implementation using Python code.

### Step 4:
Import the necessary modules.

### Step 5:
Define a custom request handler.

### Step 6:
Start an HTTP server on a specific port.

### Step 7:
Run the Python script to serve web pages.

### Step 8:
Serve the HTML pages.

### Step 9:
Start the server script and check for errors.

### Step 10:
Open a browser and navigate to http://127.0.0.1:8000 (or the assigned port).

## PROGRAM:

~~~
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Network of Protocols</title>
<style>
  :root {
    --bg: #070b16;
    --bg2: #0d1326;
    --text: #e8ecf8;
    --muted: #8a93b2;
    --app: #ff6b9d;
    --trans: #ffb454;
    --net: #4ade80;
    --link: #38bdf8;
    --phys: #a78bfa;
    --glass: rgba(255,255,255,0.04);
    --border: rgba(255,255,255,0.09);
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html { scroll-behavior: smooth; }
  body {
    font-family: "Inter", "Segoe UI", system-ui, -apple-system, sans-serif;
    background: radial-gradient(1200px 700px at 15% -10%, #1a2250 0%, transparent 60%),
                radial-gradient(1000px 600px at 95% 10%, #3a1650 0%, transparent 55%),
                var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }
  body::before {
    content: "";
    position: fixed; inset: 0;
    background-image:
      linear-gradient(var(--border) 1px, transparent 1px),
      linear-gradient(90deg, var(--border) 1px, transparent 1px);
    background-size: 48px 48px;
    mask-image: radial-gradient(ellipse at 50% 30%, #000 0%, transparent 70%);
    -webkit-mask-image: radial-gradient(ellipse at 50% 30%, #000 0%, transparent 70%);
    opacity: .5;
    pointer-events: none;
  }

  /* HERO */
  header {
    position: relative;
    text-align: center;
    padding: 90px 20px 40px;
    max-width: 980px;
    margin: 0 auto;
  }
  .badge {
    display: inline-flex; align-items: center; gap: 8px;
    padding: 6px 14px;
    border: 1px solid var(--border);
    border-radius: 999px;
    background: var(--glass);
    backdrop-filter: blur(8px);
    font-size: 12px; letter-spacing: .14em; text-transform: uppercase; color: var(--muted);
  }
  .badge i {
    width: 8px; height: 8px; border-radius: 50%;
    background: var(--net);
    box-shadow: 0 0 0 0 var(--net);
    animation: ping 2s infinite;
  }
  @keyframes ping {
    0% { box-shadow: 0 0 0 0 rgba(74,222,128,.7); }
    100% { box-shadow: 0 0 0 14px rgba(74,222,128,0); }
  }
  h1 {
    margin-top: 22px;
    font-size: clamp(2.6rem, 7vw, 5.2rem);
    line-height: 1.02;
    font-weight: 800;
    letter-spacing: -0.03em;
    background: linear-gradient(100deg, #fff 10%, var(--link) 40%, var(--app) 75%, var(--trans) 100%);
    background-size: 200% auto;
    -webkit-background-clip: text; background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: shine 8s linear infinite;
  }
  @keyframes shine { to { background-position: 200% center; } }
  header p {
    margin: 20px auto 0; max-width: 620px;
    color: var(--muted); font-size: 1.1rem; line-height: 1.7;
  }

  /* ANIMATED NETWORK GRAPHIC */
  .net-art { max-width: 900px; margin: 30px auto 0; position: relative; }
  .net-art svg { width: 100%; height: auto; display: block; overflow: visible; }
  .edge { stroke: rgba(255,255,255,.14); stroke-width: 1.2; fill: none; }
  .pulse {
    stroke: url(#g1); stroke-width: 2.2; fill: none;
    stroke-dasharray: 14 200; stroke-linecap: round;
    animation: flow 3.2s linear infinite;
    filter: drop-shadow(0 0 5px rgba(56,189,248,.9));
  }
  .pulse.p2 { animation-duration: 4.2s; animation-delay: -1s; }
  .pulse.p3 { animation-duration: 3.6s; animation-delay: -2s; }
  .pulse.p4 { animation-duration: 5s; animation-delay: -.5s; }
  @keyframes flow { to { stroke-dashoffset: -214; } }
  .node { animation: float 6s ease-in-out infinite; transform-box: fill-box; transform-origin: center; }
  .node:nth-of-type(2n) { animation-delay: -2s; }
  .node:nth-of-type(3n) { animation-delay: -4s; }
  @keyframes float { 50% { transform: translateY(-6px); } }
  .node circle.core { filter: drop-shadow(0 0 10px currentColor); }
  .node circle.ring { fill: none; stroke: currentColor; opacity: .35; animation: ripple 3s ease-out infinite; transform-box: fill-box; transform-origin: center; }
  @keyframes ripple { from { transform: scale(.6); opacity: .6; } to { transform: scale(1.8); opacity: 0; } }
  .net-art text { fill: #fff; font-size: 11px; font-weight: 700; text-anchor: middle; letter-spacing: .05em; }

  /* FILTERS */
  .filters {
    display: flex; flex-wrap: wrap; justify-content: center; gap: 10px;
    margin: 20px auto 10px; padding: 0 20px; max-width: 900px;
  }
  .chip {
    --c: #fff;
    cursor: pointer;
    padding: 9px 18px;
    border-radius: 999px;
    border: 1px solid var(--border);
    background: var(--glass);
    color: var(--text);
    font: 600 13px/1 inherit;
    letter-spacing: .04em;
    display: inline-flex; align-items: center; gap: 8px;
    transition: .25s;
  }
  .chip::before { content: ""; width: 9px; height: 9px; border-radius: 50%; background: var(--c); box-shadow: 0 0 10px var(--c); }
  .chip:hover { transform: translateY(-2px); border-color: var(--c); }
  .filters input { position: absolute; opacity: 0; pointer-events: none; }
  .filters input:checked + .chip { background: color-mix(in srgb, var(--c) 18%, transparent); border-color: var(--c); box-shadow: 0 0 24px -6px var(--c); }
  .filters input:focus-visible + .chip { outline: 2px solid var(--c); outline-offset: 3px; }

  /* CSS-only layer filtering */
  body:has(#f-app:checked)   .layer:not([data-l="app"]),
  body:has(#f-trans:checked) .layer:not([data-l="trans"]),
  body:has(#f-net:checked)   .layer:not([data-l="net"]),
  body:has(#f-link:checked)  .layer:not([data-l="link"]),
  body:has(#f-phys:checked)  .layer:not([data-l="phys"]) {
    opacity: .18; filter: grayscale(.8); transform: scale(.98);
  }

  /* STACK */
  main { max-width: 1100px; margin: 30px auto 80px; padding: 0 20px; position: relative; }
  main::before {
    content: ""; position: absolute; left: 50%; top: 0; bottom: 0; width: 2px;
    background: linear-gradient(var(--app), var(--trans), var(--net), var(--link), var(--phys));
    opacity: .25; transform: translateX(-50%);
    filter: blur(.5px);
  }
  .layer {
    --c: #fff;
    position: relative;
    margin: 0 0 28px;
    padding: 26px;
    border-radius: 24px;
    border: 1px solid var(--border);
    background: linear-gradient(145deg, rgba(255,255,255,.06), rgba(255,255,255,.015));
    backdrop-filter: blur(14px);
    -webkit-backdrop-filter: blur(14px);
    box-shadow: 0 30px 60px -30px rgba(0,0,0,.8), inset 0 1px 0 rgba(255,255,255,.08);
    transition: opacity .4s, transform .4s, filter .4s;
    overflow: hidden;
  }
  .layer::before {
    content: ""; position: absolute; inset: 0 0 auto 0; height: 3px;
    background: linear-gradient(90deg, transparent, var(--c), transparent);
  }
  .layer::after {
    content: ""; position: absolute; width: 380px; height: 380px; right: -140px; top: -180px;
    background: radial-gradient(circle, var(--c), transparent 65%); opacity: .14; pointer-events: none;
  }
  .layer.dim { opacity: .18; filter: grayscale(.8); transform: scale(.98); }
  .layer-head { display: flex; align-items: center; gap: 16px; margin-bottom: 20px; }
  .layer-num {
    width: 52px; height: 52px; border-radius: 16px;
    display: grid; place-items: center;
    font-weight: 800; font-size: 20px; color: #0a0e1c;
    background: linear-gradient(135deg, var(--c), color-mix(in srgb, var(--c) 55%, #fff));
    box-shadow: 0 0 28px -4px var(--c);
  }
  .layer-head h2 { font-size: 1.4rem; letter-spacing: -.01em; }
  .layer-head small { display: block; color: var(--muted); font-size: .85rem; margin-top: 3px; }
  .pdu {
    margin-left: auto; font-size: 12px; font-family: ui-monospace, "SF Mono", Menlo, monospace;
    padding: 6px 12px; border-radius: 8px; color: var(--c);
    border: 1px dashed color-mix(in srgb, var(--c) 60%, transparent);
    background: color-mix(in srgb, var(--c) 8%, transparent);
  }

  .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(190px, 1fr)); gap: 14px; position: relative; z-index: 1; }
  .proto {
    position: relative;
    padding: 16px 16px 14px;
    border-radius: 16px;
    background: rgba(8,12,26,.6);
    border: 1px solid var(--border);
    transition: transform .3s cubic-bezier(.2,.8,.2,1), border-color .3s, box-shadow .3s;
    cursor: default;
    overflow: hidden;
  }
  .proto::before {
    content: ""; position: absolute; inset: 0;
    background: radial-gradient(220px 120px at var(--mx, 50%) var(--my, 0%), color-mix(in srgb, var(--c) 30%, transparent), transparent 70%);
    opacity: 0; transition: opacity .3s;
  }
  .proto:hover { transform: translateY(-6px) scale(1.02); border-color: var(--c); box-shadow: 0 18px 40px -16px var(--c); }
  .proto:hover::before { opacity: 1; }
  .proto b { position: relative; display: block; font-size: 1.15rem; letter-spacing: .02em; }
  .proto span { position: relative; display: block; margin-top: 6px; font-size: .82rem; color: var(--muted); line-height: 1.5; }
  .port {
    position: relative; display: inline-block; margin-top: 10px;
    font: 600 11px ui-monospace, Menlo, monospace;
    padding: 3px 8px; border-radius: 6px;
    color: var(--c); background: color-mix(in srgb, var(--c) 12%, transparent);
  }

  .arrow {
    display: flex; justify-content: center; margin: -8px 0 20px; position: relative; z-index: 2;
  }
  .arrow div {
    width: 34px; height: 34px; border-radius: 50%;
    display: grid; place-items: center; font-size: 15px;
    background: var(--bg2); border: 1px solid var(--border);
    animation: bob 2s ease-in-out infinite;
  }
  @keyframes bob { 50% { transform: translateY(5px); } }

  footer { text-align: center; color: var(--muted); font-size: .85rem; padding: 0 20px 60px; }
  footer code { color: var(--link); }

  @media (max-width: 640px) {
    .pdu { display: none; }
    header { padding-top: 60px; }
    .layer { padding: 18px; }
  }
  @media (prefers-reduced-motion: reduce) { * { animation: none !important; } }
</style>
</head>
<body>

<header>
  <span class="badge"><i></i> Live protocol map</span>
  <h1>Network of Protocols</h1>
  <p>Every click, stream and message rides a stack of cooperating rules. Explore how protocols layer, hand off data, and keep the internet talking.</p>

  <div class="net-art">
    <svg viewBox="0 0 900 340" aria-label="Animated network of protocol nodes">
      <defs>
        <linearGradient id="g1" x1="0" x2="1">
          <stop offset="0" stop-color="#38bdf8"/>
          <stop offset="1" stop-color="#ff6b9d"/>
        </linearGradient>
      </defs>
      <!-- edges -->
      <path class="edge" d="M450 60 L200 150"/>
      <path class="edge" d="M450 60 L700 150"/>
      <path class="edge" d="M450 60 L450 170"/>
      <path class="edge" d="M200 150 L450 170"/>
      <path class="edge" d="M700 150 L450 170"/>
      <path class="edge" d="M200 150 L110 270"/>
      <path class="edge" d="M200 150 L300 280"/>
      <path class="edge" d="M450 170 L300 280"/>
      <path class="edge" d="M450 170 L600 280"/>
      <path class="edge" d="M700 150 L600 280"/>
      <path class="edge" d="M700 150 L790 270"/>
      <path class="edge" d="M300 280 L600 280"/>
      <!-- pulses -->
      <path class="pulse"    d="M450 60 L200 150"/>
      <path class="pulse p2" d="M450 60 L700 150"/>
      <path class="pulse p3" d="M200 150 L450 170 L600 280"/>
      <path class="pulse p4" d="M700 150 L450 170 L300 280"/>
      <path class="pulse p2" d="M200 150 L110 270"/>
      <path class="pulse p3" d="M700 150 L790 270"/>
      <!-- nodes -->
      <g class="node" style="color:#ff6b9d"><circle class="ring" cx="450" cy="60" r="22"/><circle class="core" cx="450" cy="60" r="16" fill="currentColor"/><text x="450" y="30">HTTP</text></g>
      <g class="node" style="color:#ffb454"><circle class="ring" cx="200" cy="150" r="22"/><circle class="core" cx="200" cy="150" r="16" fill="currentColor"/><text x="200" y="120">TCP</text></g>
      <g class="node" style="color:#ffb454"><circle class="ring" cx="700" cy="150" r="22"/><circle class="core" cx="700" cy="150" r="16" fill="currentColor"/><text x="700" y="120">QUIC</text></g>
      <g class="node" style="color:#4ade80"><circle class="ring" cx="450" cy="170" r="22"/><circle class="core" cx="450" cy="170" r="18" fill="currentColor"/><text x="450" y="206">IP</text></g>
      <g class="node" style="color:#38bdf8"><circle class="ring" cx="110" cy="270" r="22"/><circle class="core" cx="110" cy="270" r="14" fill="currentColor"/><text x="110" y="310">Ethernet</text></g>
      <g class="node" style="color:#38bdf8"><circle class="ring" cx="300" cy="280" r="22"/><circle class="core" cx="300" cy="280" r="14" fill="currentColor"/><text x="300" y="320">ARP</text></g>
      <g class="node" style="color:#38bdf8"><circle class="ring" cx="600" cy="280" r="22"/><circle class="core" cx="600" cy="280" r="14" fill="currentColor"/><text x="600" y="320">Wi-Fi</text></g>
      <g class="node" style="color:#a78bfa"><circle class="ring" cx="790" cy="270" r="22"/><circle class="core" cx="790" cy="270" r="14" fill="currentColor"/><text x="790" y="310">Fiber</text></g>
    </svg>
  </div>
</header>

<div class="filters" id="filters">
  <input type="radio" name="f" id="f-all" checked><label class="chip" for="f-all" style="--c:#fff">All layers</label>
  <input type="radio" name="f" id="f-app"><label class="chip" for="f-app" style="--c:#ff6b9d">Application</label>
  <input type="radio" name="f" id="f-trans"><label class="chip" for="f-trans" style="--c:#ffb454">Transport</label>
  <input type="radio" name="f" id="f-net"><label class="chip" for="f-net" style="--c:#4ade80">Network</label>
  <input type="radio" name="f" id="f-link"><label class="chip" for="f-link" style="--c:#38bdf8">Link</label>
  <input type="radio" name="f" id="f-phys"><label class="chip" for="f-phys" style="--c:#a78bfa">Physical</label>
</div>

<main>

  <section class="layer" data-l="app" style="--c:#ff6b9d">
    <div class="layer-head">
      <div class="layer-num">5</div>
      <div><h2>Application Layer</h2><small>What apps and people actually speak</small></div>
      <div class="pdu">PDU: Message / Data</div>
    </div>
    <div class="grid">
      <div class="proto"><b>HTTP / HTTPS</b><span>Web pages, APIs and everything in your browser.</span><em class="port">80 · 443</em></div>
      <div class="proto"><b>DNS</b><span>Turns names like example.com into IP addresses.</span><em class="port">53</em></div>
      <div class="proto"><b>SMTP / IMAP</b><span>Sends and retrieves email between servers and clients.</span><em class="port">25 · 143</em></div>
      <div class="proto"><b>SSH</b><span>Encrypted remote login and secure tunnelling.</span><em class="port">22</em></div>
      <div class="proto"><b>FTP / SFTP</b><span>Classic file transfer, plain or over SSH.</span><em class="port">21 · 22</em></div>
      <div class="proto"><b>DHCP</b><span>Hands out IP addresses automatically on join.</span><em class="port">67 · 68</em></div>
      <div class="proto"><b>MQTT</b><span>Lightweight pub/sub messaging for IoT devices.</span><em class="port">1883</em></div>
      <div class="proto"><b>WebSocket</b><span>Full-duplex real-time channel over one connection.</span><em class="port">80 · 443</em></div>
    </div>
  </section>
  <div class="arrow"><div>⬇</div></div>

  <section class="layer" data-l="trans" style="--c:#ffb454">
    <div class="layer-head">
      <div class="layer-num">4</div>
      <div><h2>Transport Layer</h2><small>End-to-end delivery between processes</small></div>
      <div class="pdu">PDU: Segment / Datagram</div>
    </div>
    <div class="grid">
      <div class="proto"><b>TCP</b><span>Reliable, ordered streams with congestion control.</span><em class="port">Connection-oriented</em></div>
      <div class="proto"><b>UDP</b><span>Fast, connectionless datagrams with no guarantees.</span><em class="port">Connectionless</em></div>
      <div class="proto"><b>QUIC</b><span>Multiplexed, encrypted transport over UDP (HTTP/3).</span><em class="port">UDP 443</em></div>
      <div class="proto"><b>TLS</b><span>Encryption and identity on top of a transport.</span><em class="port">Security</em></div>
      <div class="proto"><b>SCTP</b><span>Multi-stream, message-oriented reliable transport.</span><em class="port">Telecom</em></div>
    </div>
  </section>
  <div class="arrow"><div>⬇</div></div>

  <section class="layer" data-l="net" style="--c:#4ade80">
    <div class="layer-head">
      <div class="layer-num">3</div>
      <div><h2>Network Layer</h2><small>Addressing and routing across networks</small></div>
      <div class="pdu">PDU: Packet</div>
    </div>
    <div class="grid">
      <div class="proto"><b>IPv4 / IPv6</b><span>Logical addressing and hop-by-hop packet forwarding.</span><em class="port">Addressing</em></div>
      <div class="proto"><b>ICMP</b><span>Errors and diagnostics, the engine behind ping.</span><em class="port">Control</em></div>
      <div class="proto"><b>IPsec</b><span>Authenticated, encrypted IP tunnels for VPNs.</span><em class="port">Security</em></div>
      <div class="proto"><b>OSPF</b><span>Link-state routing inside an organisation.</span><em class="port">Interior</em></div>
      <div class="proto"><b>BGP</b><span>The routing glue that connects the whole internet.</span><em class="port">TCP 179</em></div>
    </div>
  </section>
  <div class="arrow"><div>⬇</div></div>

  <section class="layer" data-l="link" style="--c:#38bdf8">
    <div class="layer-head">
      <div class="layer-num">2</div>
      <div><h2>Link Layer</h2><small>Talking to the neighbour next door</small></div>
      <div class="pdu">PDU: Frame</div>
    </div>
    <div class="grid">
      <div class="proto"><b>Ethernet</b><span>Wired LAN framing with MAC addressing.</span><em class="port">IEEE 802.3</em></div>
      <div class="proto"><b>Wi-Fi</b><span>Wireless LAN access with shared airtime.</span><em class="port">IEEE 802.11</em></div>
      <div class="proto"><b>ARP</b><span>Maps IP addresses to MAC addresses locally.</span><em class="port">Resolution</em></div>
      <div class="proto"><b>PPP</b><span>Direct point-to-point links, common in DSL.</span><em class="port">Serial</em></div>
      <div class="proto"><b>VLAN</b><span>Splits one switch into isolated logical networks.</span><em class="port">IEEE 802.1Q</em></div>
    </div>
  </section>
  <div class="arrow"><div>⬇</div></div>

  <section class="layer" data-l="phys" style="--c:#a78bfa">
    <div class="layer-head">
      <div class="layer-num">1</div>
      <div><h2>Physical Layer</h2><small>Bits as light, voltage and radio</small></div>
      <div class="pdu">PDU: Bits</div>
    </div>
    <div class="grid">
      <div class="proto"><b>Fiber optic</b><span>Pulses of light over glass for long, fast links.</span><em class="port">Light</em></div>
      <div class="proto"><b>Twisted pair</b><span>Copper cabling like Cat6 for local networks.</span><em class="port">Electrical</em></div>
      <div class="proto"><b>Radio / 5G</b><span>Spectrum-based wireless for mobile and Wi-Fi.</span><em class="port">RF</em></div>
      <div class="proto"><b>Bluetooth</b><span>Short-range, low-power personal area links.</span><em class="port">2.4 GHz</em></div>
    </div>
  </section>

</main>

<footer>Hover a protocol card to light it up · use the chips to focus a layer.</footer>

</body>
</html>
~~~


## OUTPUT:

<img width="1901" height="912" alt="Screenshot 2026-10-24 140650" src="https://github.com/user-attachments/assets/7a14c5ee-449b-4153-8311-cf6118ea6616" />
<img width="441" height="902" alt="Screenshot 2026-10-24 140718" src="https://github.com/user-attachments/assets/9962e311-a990-431d-9347-831d925eb5ed" />


## RESULT:
The program for implementing simple webserver is executed successfully.

