╔═══════════════════════════════════════════════════════════════════════════════╗
║                    MINIFIER GO — GUIA COMPLETO PARA TASKER                     ║
║                          Versão Profissional — Tasker Java Code                ║
╚═══════════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────────┐
│                           STEP 1 — CONFIGURAÇÃO INICIAL                        │
│                              (Run Shell — executar uma única vez)              │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

mkdir -p /data/data/net.dinglisch.android.taskerm/files/minifier && cp /storage/emulated/0/Download/minifier_go /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go && chmod 755 /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go && echo "copied"

📌 OBS: Altere o caminho "/storage/emulated/0/Download/minifier_go" se seu binário estiver em outra pasta.

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                          STEP 2 — INICIAR O SERVIDOR                           │
│                                    (Java Code)                                 │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

rt = Runtime.getRuntime();
String[] cmd = new String[]{
    "/system/bin/sh",
    "-c",
    "cd /data/data/net.dinglisch.android.taskerm/files/minifier && nohup /system/bin/linker64 /data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go </dev/null >/dev/null 2>&1 &"
};
rt.exec(cmd);

📌 O servidor será iniciado em background na porta 7070.

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                          STEP 3 — PARAR O SERVIDOR                             │
│                                    (Java Code)                                 │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

Runtime.getRuntime().exec(new String[]{
    "/system/bin/sh",
    "-c",
    "pkill -f minifier_go"
});

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                       STEP 4 — VERIFICAR STATUS DO SERVIDOR                    │
│                      (Java Code — Return: %STATUS_MINIFIER)                    │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

String resposta = "";
try {
    java.net.URL url = new java.net.URL("http://localhost:7070/status");
    java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
    conn.setRequestMethod("GET");
    conn.setConnectTimeout(3000);
    conn.connect();

    java.io.BufferedReader br = new java.io.BufferedReader(
        new java.io.InputStreamReader(conn.getInputStream())
    );
    resposta = br.readLine();
    br.close();
} catch (Exception e) {
    resposta = "offline: " + e.getMessage();
}
return resposta;

📌 A variável %STATUS_MINIFIER retornará "online" ou "offline: [erro]"

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                    STEP 5 — MINIFICAR PASTA COMPLETA (RECOMENDADO)             │
│                    (Java Code — Return: %RESULT_MINIFY_FOLDER)                 │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

String resposta = "";
try {
    // 📁 CAMINHO DA PASTA DE ENTRADA (ALTERE AQUI)
    String dir = "/storage/emulated/0/Download/PastaData";
    
    // 💾 CAMINHO DO ARQUIVO DE SAÍDA (ALTERE AQUI)
    String output = "/storage/emulated/0/Download/index.min.html";
    
    // 📊 NÍVEL DE COMPRESSÃO: "normal" | "aggressive" | "maximum"
    String level = "maximum";

    java.net.URL url = new java.net.URL("http://localhost:7070/minify/folder");
    java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
    conn.setRequestMethod("POST");
    conn.setDoOutput(true);
    conn.setConnectTimeout(30000);
    conn.setReadTimeout(60000);

    String body = "dir="    + java.net.URLEncoder.encode(dir,    "UTF-8")
                + "&output=" + java.net.URLEncoder.encode(output, "UTF-8")
                + "&level="  + level;

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

📌 O servidor varrerá a pasta inteira e gerará um único arquivo minificado.

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                    STEP 6 — MINIFICAR ARQUIVO INDIVIDUAL HTML                  │
│                    (Java Code — Return: %RESULT_MINIFY_SINGLE)                 │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

String resposta = "";
try {
    // 📄 ARQUIVO DE ENTRADA (ALTERE AQUI)
    String input = "/storage/emulated/0/Download/Meuhtml.html";
    
    // 💾 ARQUIVO DE SAÍDA (ALTERE AQUI)
    String output = "/storage/emulated/0/Download/Meuhtml.min.html";
    
    // 📊 NÍVEL DE COMPRESSÃO: "normal" | "aggressive" | "maximum"
    String level = "maximum";

    java.net.URL url = new java.net.URL("http://localhost:7070/minify");
    java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
    conn.setRequestMethod("POST");
    conn.setDoOutput(true);
    conn.setConnectTimeout(30000);
    conn.setReadTimeout(60000);

    String body = "input="  + java.net.URLEncoder.encode(input,  "UTF-8")
                + "&output=" + java.net.URLEncoder.encode(output, "UTF-8")
                + "&level="  + level;

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

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                       STEP 7 — MINIFICAR ARQUIVO JSON                          │
│                      (Java Code — Return: %RESULT_MINIFY_JSON)                 │
└───────────────────────────────────────────────────────────────────────────────┘

➤ CÓDIGO:

String resposta = "";
try {
    // 📦 ARQUIVO JSON DE ENTRADA (ALTERE AQUI)
    String input = "/storage/emulated/0/Download/data.json";
    
    // 💾 ARQUIVO JSON DE SAÍDA (ALTERE AQUI)
    String output = "/storage/emulated/0/Download/data.min.json";

    java.net.URL url = new java.net.URL("http://localhost:7070/minify/json");
    java.net.HttpURLConnection conn = (java.net.HttpURLConnection) url.openConnection();
    conn.setRequestMethod("POST");
    conn.setDoOutput(true);
    conn.setConnectTimeout(30000);
    conn.setReadTimeout(60000);

    String body = "input="  + java.net.URLEncoder.encode(input,  "UTF-8")
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

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                          TABELA DE NÍVEIS DE COMPRESSÃO                        │
└───────────────────────────────────────────────────────────────────────────────┘

╔═══════════════╦══════════════╦═══════════════════════════════════════════════╗
║ Nível         ║ Redução      ║ Quando usar                                    ║
╠═══════════════╬══════════════╬═══════════════════════════════════════════════╣
║ normal        ║ 30% - 50%    ║ Compatibilidade máxima (arquivos antigos)     ║
║ aggressive    ║ 50% - 65%    ║ Uso geral (recomendado para a maioria)        ║
║ maximum       ║ 60% - 75%    ║ Menor arquivo possível (espaço crítico)       ║
╚═══════════════╩══════════════╩═══════════════════════════════════════════════╝

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                              ROTAS DISPONÍVEIS                                 │
└───────────────────────────────────────────────────────────────────────────────┘

╔═════════════════════╦══════════╦══════════════════════════════════════════════╗
║ Rota                ║ Método   ║ Descrição                                     ║
╠═════════════════════╬══════════╬══════════════════════════════════════════════╣
║ /minify             ║ POST     ║ Minificar um único arquivo HTML              ║
║ /minify/folder      ║ POST     ║ Minificar pasta inteira (HTML/CSS/JS/JSON)   ║
║ /minify/json        ║ POST     ║ Minificar arquivo JSON                       ║
║ /status             ║ GET      ║ Verificar se o servidor está rodando         ║
╚═════════════════════╩══════════╩══════════════════════════════════════════════╝

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                         VERIFICAR STATUS NO NAVEGADOR                          │
└───────────────────────────────────────────────────────────────────────────────┘

➤ Acesse no navegador do seu celular:

http://localhost:7070/status

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                          FLUXO RECOMENDADO DA TASKER                           │
└───────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ 1. Java Code ─────────────────► Iniciar servidor (STEP 2)                   │
│ 2. Wait ──────────────────────► Aguardar 2 segundos                         │
│ 3. Java Code ─────────────────► Verificar status (STEP 4)                   │
│ 4. If %STATUS_MINIFIER ~ *running* ──► Continuar                            │
│ 5. Java Code ─────────────────► Minificar (STEP 5, 6 ou 7)                  │
│ 6. Flash ─────────────────────► Exibir %RESULT_MINIFY_FOLDER (ou similar)   │
│ 7. Java Code ─────────────────► Parar servidor (STEP 3) — opcional          │
└─────────────────────────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                           LOCALIZAÇÃO DO BINÁRIO                               │
└───────────────────────────────────────────────────────────────────────────────┘

/data/data/net.dinglisch.android.taskerm/files/minifier/minifier_go

═══════════════════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────────────────────┐
│                               OBSERVAÇÕES IMPORTANTES                          │
└───────────────────────────────────────────────────────────────────────────────┘

• Todos os caminhos de arquivo DEVEM ser absolutos (começando com /storage/...)
• O servidor roda na porta 7070 — certifique-se que nenhum outro app use esta porta
• Timeout de conexão: 30 segundos para conexão, 60 segundos para leitura
• A pasta de saída DEVE existir antes da execução
• Para minificar pastas, o servidor varre recursivamente todos os subdiretórios
