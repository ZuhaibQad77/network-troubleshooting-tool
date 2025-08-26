<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Network Troubleshooting Tool</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      max-width: 600px;
      margin: auto;
      background: #f4f4f4;
    }
    input, button {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      font-size: 1em;
      border-radius: 5px;
      border: 1px solid #ccc;
    }
    button {
      background-color: #007bff;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    pre {
      background: #eee;
      padding: 15px;
      border-radius: 5px;
      white-space: pre-wrap;
      word-wrap: break-word;
    }
  </style>
</head>
<body>
  <h1>Network Troubleshooting Tool</h1>
  
  <h2>DNS Lookup</h2>
  <input id="dnsInput" placeholder="Enter domain (e.g., example.com)" />
  <button onclick="dnsLookup()">Lookup DNS</button>
  <pre id="dnsResult"></pre>

  <h2>IP Info Lookup</h2>
  <input id="ipInput" placeholder="Enter IP address (e.g., 8.8.8.8)" />
  <button onclick="ipInfo()">Get IP Info</button>
  <pre id="ipResult"></pre>

  <script>
    async function dnsLookup() {
      const domain = document.getElementById('dnsInput').value.trim();
      const result = document.getElementById('dnsResult');
      if (!domain) {
        result.textContent = 'Please enter a domain.';
        return;
      }
      result.textContent = 'Looking up...';
      try {
        const res = await fetch(`https://dns.google/resolve?name=${domain}&type=A`);
        const data = await res.json();
        if (!data.Answer) {
          result.textContent = 'No DNS records found.';
          return;
        }
        const ips = data.Answer.filter(a => a.type === 1).map(a => a.data);
        result.textContent = `IP addresses for ${domain}:\n${ips.join('\n')}`;
      } catch (e) {
        result.textContent = 'Error fetching DNS info.';
      }
    }

    async function ipInfo() {
      const ip = document.getElementById('ipInput').value.trim();
      const result = document.getElementById('ipResult');
      if (!ip) {
        result.textContent = 'Please enter an IP address.';
        return;
      }
      result.textContent = 'Fetching IP info...';
      try {
        const res = await fetch(`https://ipwho.is/${ip}`);
        const data = await res.json();
        if (!data.success) {
          result.textContent = 'Invalid IP or no info found.';
          return;
        }
        result.textContent = `
IP: ${data.ip}
Location: ${data.city}, ${data.region}, ${data.country}
ISP: ${data.connection.isp}
Type: ${data.type}
        `;
      } catch (e) {
        result.textContent = 'Error fetching IP info.';
      }
    }
  </script>
</body>
</html>
