
MINIFIER GO — GUIA COMPLETO PARA TASKER

============================================================

STEP 1 — INICIAR O SERVIDOR (Java Code)

COPIE O CÓDIGO ABAIXO:

```

rt = Runtime.getRuntime();
String[] cmd = new String[]{
"/system/bin/sh",
"-c",
"cd /data/data/net.dinglisch.android.taskerm/files/minifier && nohup /system/bin/linker64 /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go </dev/null >/dev/null 2>&1 &"
};
rt.exec(cmd);

```

============================================================

STEP 2 — PARAR O SERVIDOR (Java Code)

COPIE O CÓDIGO ABAIXO:

```

Runtime.getRuntime().exec(new String[]{
"/system/bin/sh",
"-c",
"pkill -f minifier_go"
});

```

============================================================

STEP 3 — VERIFICAR STATUS (Java Code — Return: %STATUS_MINIFIER)

COPIE O CÓDIGO ABAIXO:

```

String resposta = "";
try {
java.net.URL url = new java.net.URL("http://localhost:7070/status");
java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");
conn.setConnectTimeout(3000);
conn.connect();
java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(conn.getInputStream()));
resposta = br.readLine();
br.close();
} catch (Exception e) {
resposta = "offline: " + e.getMessage();
}
return resposta;

```

============================================================

STEP 4 — MINIFICAR PASTA COMPLETA (Java Code — Return: %RESULT_MINIFY_FOLDER)

PARÂMETROS QUE VOCÊ PODE ALTERAR:
- String dir = caminho da pasta de entrada
- String output = caminho do arquivo de saída
- String level = "normal" | "aggressive" | "maximum"

COPIE O CÓDIGO ABAIXO:

```

String resposta = "";
try {
String dir = "/storage/emulated/0/Download/PastaData";
String output = "/storage/emulated/0/Download/index.min.html";
String level = "maximum";
java.net.URL url = new java.net.URL("http://localhost:7070/minify/folder");
java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
conn.setRequestMethod("POST");
conn.setDoOutput(true);
conn.setConnectTimeout(30000);
conn.setReadTimeout(60000);
String body = "dir=" + java.net.URLEncoder.encode(dir, "UTF-8")
+ "&output=" + java.net.URLEncoder.encode(output, "UTF-8")
+ "&level=" + level;
java.io.OutputStream os = conn.getOutputStream();
os.write(body.getBytes("UTF-8"));
os.flush();
os.close();
int code = conn.getResponseCode();
java.io.InputStream is = code >= 400 ? conn.getErrorStream() : conn.getInputStream();
java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(is));
resposta = br.readLine();
br.close();
} catch (Exception e) {
resposta = "Error: " + e.getMessage();
}
return resposta;

```

============================================================

STEP 5 — MINIFICAR ARQUIVO INDIVIDUAL HTML (Java Code — Return: %RESULT_MINIFY_SINGLE)

PARÂMETROS QUE VOCÊ PODE ALTERAR:
- String input = caminho do arquivo de entrada
- String output = caminho do arquivo de saída
- String level = "normal" | "aggressive" | "maximum"

COPIE O CÓDIGO ABAIXO:

```

String resposta = "";
try {
String input = "/storage/emulated/0/Download/Meuhtml.html";
String output = "/storage/emulated/0/Download/Meuhtml.min.html";
String level = "maximum";
java.net.URL url = new java.net.URL("http://localhost:7070/minify");
java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
conn.setRequestMethod("POST");
conn.setDoOutput(true);
conn.setConnectTimeout(30000);
conn.setReadTimeout(60000);
String body = "input=" + java.net.URLEncoder.encode(input, "UTF-8")
+ "&output=" + java.net.URLEncoder.encode(output, "UTF-8")
+ "&level=" + level;
java.io.OutputStream os = conn.getOutputStream();
os.write(body.getBytes("UTF-8"));
os.flush();
os.close();
int code = conn.getResponseCode();
java.io.InputStream is = code >= 400 ? conn.getErrorStream() : conn.getInputStream();
java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(is));
resposta = br.readLine();
br.close();
} catch (Exception e) {
resposta = "Error: " + e.getMessage();
}
return resposta;

```

============================================================

STEP 6 — MINIFICAR ARQUIVO JSON (Java Code — Return: %RESULT_MINIFY_JSON)

PARÂMETROS QUE VOCÊ PODE ALTERAR:
- String input = caminho do arquivo JSON de entrada
- String output = caminho do arquivo JSON de saída

COPIE O CÓDIGO ABAIXO:

```

String resposta = "";
try {
String input = "/storage/emulated/0/Download/data.json";
String output = "/storage/emulated/0/Download/data.min.json";
java.net.URL url = new java.net.URL("http://localhost:7070/minify/json");
java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
conn.setRequestMethod("POST");
conn.setDoOutput(true);
conn.setConnectTimeout(30000);
conn.setReadTimeout(60000);
String body = "input=" + java.net.URLEncoder.encode(input, "UTF-8")
+ "&output=" + java.net.URLEncoder.encode(output, "UTF-8");
java.io.OutputStream os = conn.getOutputStream();
os.write(body.getBytes("UTF-8"));
os.flush();
os.close();
int code = conn.getResponseCode();
java.io.InputStream is = code >= 400 ? conn.getErrorStream() : conn.getInputStream();
java.io.BufferedReader br = new java.io.BufferedReader(new java.io.InputStreamReader(is));
resposta = br.readLine();
br.close();
} catch (Exception e) {
resposta = "Error: " + e.getMessage();
}
return resposta;

```

============================================================

NÍVEIS DE COMPRESSÃO

normal      -> 30% - 50%   (compatibilidade máxima)
aggressive  -> 50% - 65%   (recomendado para uso geral)
maximum     -> 60% - 75%   (menor arquivo possível)

============================================================

ROTAS DISPONÍVEIS

/minify        -> POST -> Minificar um único arquivo HTML
/minify/folder -> POST -> Minificar pasta inteira
/minify/json   -> POST -> Minificar arquivo JSON
/status        -> GET  -> Verificar se o servidor está rodando

============================================================

VERIFICAR STATUS NO NAVEGADOR

http://localhost:7070/status

============================================================

FLUXO RECOMENDADO DA TASKER

1. Executar STEP 1 (Iniciar servidor)
2. Wait 2 segundos
3. Executar STEP 3 (Verificar status)
4. Se %STATUS_MINIFIER ~ *running*
5. Executar STEP 4, 5 ou 6 (Minificar)
6. Flash a variável de retorno
7. Executar STEP 2 (Parar servidor - opcional)

============================================================

LOCALIZAÇÃO DO BINÁRIO

/data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go

============================================================

OBSERVAÇÕES IMPORTANTES

- Todos os caminhos de arquivo DEVEM ser absolutos (começando com /storage/)
- O servidor roda na porta 7070
- Timeout de conexão: 30 segundos
- A pasta de saída DEVE existir antes da execução

============================================================
