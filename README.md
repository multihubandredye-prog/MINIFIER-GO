MINIFIER GO — GUIAS DIRETOS PARA TASKER

════════════════════════════════════════════════════════════════

STEP 1 — COPIAR BINÁRIO (Run Shell — uma vez)

mkdir -p /data/data/net.dinglisch.android.taskerm/files/minifier && cp /storage/emulated/0/Download/minifier_go /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go && chmod 755 /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go && echo "copied"

════════════════════════════════════════════════════════════════

STEP 2 — INICIAR SERVIDOR (Java Code)

rt = Runtime.getRuntime(); String[] cmd = new String[]{ "/system/bin/sh", "-c", "cd /data/data/net.dinglisch.android.taskerm/files/minifier && nohup /system/bin/linker64 /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go </dev/null >/dev/null 2>&1 &" }; rt.exec(cmd);

════════════════════════════════════════════════════════════════

STEP 3 — PARAR SERVIDOR (Java Code)

Runtime.getRuntime().exec(new String[]{ "/system/bin/sh", "-c", "pkill -f minifier_go" });

════════════════════════════════════════════════════════════════

STEP 4 — VERIFICAR SE ESTÁ RODANDO (Run Shell)

pgrep -f minifier_go >/dev/null && echo "running" || echo "stopped"

════════════════════════════════════════════════════════════════

STEP 5 — STATUS VIA HTTP (Java Code — Return: %STATUS_MINIFIER)

String resposta = ""; try { java.net.URL url = new java.net.URL("http://localhost:7070/status"); java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection(); conn.setRequestMethod("GET"); conn.setConnectTimeout(3000); conn.connect(); java.io.BufferedReader br = new java.io.BufferedReader( new java.io.InputStreamReader(conn.getInputStream()) ); resposta = br.readLine(); br.close(); } catch (Exception e) { resposta = "offline: " + e.getMessage(); } return resposta;

════════════════════════════════════════════════════════════════

STEP 6 — MINIFICAR UM ARQUIVO (Java Code — Return: %RESULT_MINIFY)

String resposta = ""; try { String input = "/storage/emulated/0/Download/Meuhtml.html"; String output = "/storage/emulated/0/Download/Meuhtml.min.html"; String level = "maximum"; java.net.URL url = new java.net.URL("http://localhost:7070/minify"); java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection(); conn.setRequestMethod("POST"); conn.setDoOutput(true); conn.setConnectTimeout(30000); conn.setReadTimeout(60000); String body = "input=" + java.net.URLEncoder.encode(input, "UTF-8") + "&output=" + java.net.URLEncoder.encode(output, "UTF-8") + "&level=" + level; java.io.OutputStream os = conn.getOutputStream(); os.write(body.getBytes("UTF-8")); os.flush(); os.close(); int code = conn.getResponseCode(); java.io.InputStream is = code >= 400 ? conn.getErrorStream() : conn.getInputStream(); java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(is)); resposta = br.readLine(); br.close(); } catch (Exception e) { resposta = "Error: " + e.getMessage(); } return resposta;

════════════════════════════════════════════════════════════════

STEP 7 — MINIFICAR PASTA INTEIRA (Java Code — Return: %RESULT_FOLDER)

String resposta = ""; try { String dir = "/storage/emulated/0/Download/MyProject/"; String output = "/storage/emulated/0/Download/index.min.html"; String level = "maximum"; java.net.URL url = new java.net.URL("http://localhost:7070/minify/folder"); java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection(); conn.setRequestMethod("POST"); conn.setDoOutput(true); conn.setConnectTimeout(30000); conn.setReadTimeout(60000); String body = "dir=" + java.net.URLEncoder.encode(dir, "UTF-8") + "&output=" + java.net.URLEncoder.encode(output, "UTF-8") + "&level=" + level; java.io.OutputStream os = conn.getOutputStream(); os.write(body.getBytes("UTF-8")); os.flush(); os.close(); int code = conn.getResponseCode(); java.io.InputStream is = code >= 400 ? conn.getErrorStream() : conn.getInputStream(); java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(is)); resposta = br.readLine(); br.close(); } catch (Exception e) { resposta = "Error: " + e.getMessage(); } return resposta;

════════════════════════════════════════════════════════════════

STEP 8 — MINIFICAR JSON (Java Code — Return: %RESULT_JSON)

String resposta = ""; try { String input = "/storage/emulated/0/Download/data.json"; String output = "/storage/emulated/0/Download/data.min.json"; java.net.URL url = new java.net.URL("http://localhost:7070/minify/json"); java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection(); conn.setRequestMethod("POST"); conn.setDoOutput(true); conn.setConnectTimeout(30000); conn.setReadTimeout(60000); String body = "input=" + java.net.URLEncoder.encode(input, "UTF-8") + "&output=" + java.net.URLEncoder.encode(output, "UTF-8"); java.io.OutputStream os = conn.getOutputStream(); os.write(body.getBytes("UTF-8")); os.flush(); os.close(); int code = conn.getResponseCode(); java.io.InputStream is = code >= 400 ? conn.getErrorStream() : conn.getInputStream(); java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(is)); resposta = br.readLine(); br.close(); } catch (Exception e) { resposta = "Error: " + e.getMessage(); } return resposta;

════════════════════════════════════════════════════════════════

NÍVEIS DE COMPRESSÃO

normal     → 30%-50%   (compatibilidade máxima)
aggressive → 50%-65%   (recomendado)
maximum    → 60%-75%   (menor tamanho)

════════════════════════════════════════════════════════════════

ROTAS DISPONÍVEIS

/minify          POST → arquivo HTML
/minify/folder   POST → pasta inteira
/minify/json     POST → apenas JSON
/status          GET  → verificar servidor

════════════════════════════════════════════════════════════════

FLUXO RECOMENDADO

1. STEP 2 (iniciar)
2. Wait 2s
3. STEP 5 (status)
4. Se %STATUS_MINIFIER ~ *running*
5. STEP 6, 7 ou 8 (minificar)
6. Flash %RESULT
7. STEP 3 (parar - opcional)

════════════════════════════════════════════════════════════════

LOCAL DO BINÁRIO

/data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go

════════════════════════════════════════════════════════════════

STATUS NO NAVEGADOR

http://localhost:7070/status
