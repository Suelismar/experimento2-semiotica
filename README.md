# 🧪 Laboratório de Semiótica experimental: Teste de Estimativa de Magnitude



# PROCEDIMENTO DE COLETA

Neste repositório, disponibilizamos o sistema completo de coleta de dados para o experimento 2 da nossa pesquisa de doutoramento (Suelismar Mariano Florêncio Barbosa, em andamento). Desenvolvemos um Teste de Estimativa de Magnitude focado na percepção de grandezas físicas em execuções sinalizadas em língua brasileira de sinais com diferentes funções comunicativas.

Dividimos a nossa arquitetura em duas frentes:

1. **Front-end (Interface):** Aplicação web que codificamos contra vieses de lateralidade e cor, incluindo controle de tempo de tela, randomização de estímulos e bloqueio para dispositivos móveis.
2. **Back-end (Servidor):** Integração via Google Apps Script que estabelecemos para a extração e tabulação automatizada dos dados em nuvem.

---
## 📁 1. Estrutura de Arquivos Obrigatória

Para que o nosso experimento funcione corretamente e o código consiga puxar o vídeo exato para cada pergunta, organizamos os arquivos localmente e no GitHub **exatamente** com esta estrutura de pastas e nomenclaturas.

Adotamos o padrão de nomenclatura seguindo a regra `[id_do_poema]_[dimensão]_[polo].mp4`. Ao todo, a nossa pasta `videos` contém 20 arquivos:

Plaintext

```
Experimento2_Semiotica/
│
├── index.html            # O código principal da nossa interface de coleta
├── LEIA-ME.md            # Este arquivo de instruções
│
└── videos/               # Pasta contendo os nossos estímulos visuais
    │
    │   # --- POEMA CSA (6 vídeos) ---
    ├── csa_esf_est.mp4   # Poema CSA - Pico de Esforço (Execução Estética)
    ├── csa_esf_util.mp4  # Poema CSA - Pico de Esforço (Execução Utilitária)
    ├── csa_des_est.mp4   # Poema CSA - Pico de Deslocamento (Execução Estética)
    ├── csa_des_util.mp4  # Poema CSA - Pico de Deslocamento (Execução Utilitária)
    ├── csa_dur_est.mp4   # Poema CSA - Pico de Duração (Execução Estética)
    ├── csa_dur_util.mp4  # Poema CSA - Pico de Duração (Execução Utilitária)
    │
    │   # --- POEMA VV (6 vídeos) ---
    ├── vv_esf_est.mp4    # Poema VV - Pico de Esforço (Execução Estética)
    ├── vv_esf_util.mp4   # Poema VV - Pico de Esforço (Execução Utilitária)
    ├── vv_des_est.mp4    # Poema VV - Pico de Deslocamento (Execução Estética)
    ├── vv_des_util.mp4   # Poema VV - Pico de Deslocamento (Execução Utilitária)
    ├── vv_dur_est.mp4    # Poema VV - Pico de Duração (Execução Estética)
    ├── vv_dur_util.mp4   # Poema VV - Pico de Duração (Execução Utilitária)
    │
    │   # --- POEMA AC (6 vídeos) ---
    ├── ac_esf_est.mp4    # Poema AC - Pico de Esforço (Execução Estética)
    ├── ac_esf_util.mp4   # Poema AC - Pico de Esforço (Execução Utilitária)
    ├── ac_des_est.mp4    # Poema AC - Pico de Deslocamento (Execução Estética)
    ├── ac_des_util.mp4   # Poema AC - Pico de Deslocamento (Execução Utilitária)
    ├── ac_dur_est.mp4    # Poema AC - Pico de Duração (Execução Estética)
    ├── ac_dur_util.mp4   # Poema AC - Pico de Duração (Execução Utilitária)
    │
    │   # --- VÍDEOS DE CALIBRAGEM (2 vídeos) ---
    ├── treino_a.mp4      # Vídeo neutro para calibragem motora
    └── treino_b.mp4      # Vídeo neutro para calibragem motora
```

(_Nota: Padronizamos todos os vídeos no formato `.mp4` com compressão H.264 para garantir a compatibilidade de reprodução automática em todos os navegadores_).

---
## 📊 2. Configuração do Banco de Dados (Google Sheets)

Antes de rodarmos o código, precisamos preparar o local onde salvaremos os dados.

1. Acessamos o [Google Sheets](https://sheets.google.com/) e criamos uma planilha em branco.
2. Renomeamos a primeira aba para exatamente: `Dados_Experimento`
3. Na **Linha 1**, criamos os cabeçalhos das 16 colunas na ordem exata abaixo:
    
    - **A:** `Data_Hora`
    - **B:** `Sessao`
    - **C:** `Audiologia`
    - **D:** `Perfil`
    - **E:** `Idade`
    - **F:** `Genero`
    - **G:** `Escolaridade`
    - **H:** `Lateralidade`
    - **I:** `Estado`
    - **J:** `Poema`
    - **K:** `Esforco`
    - **L:** `ID_E`
    - **M:** `Deslocamento`
    - **N:** `ID_S`
    - **O:** `Duracao`
    - **P:** `ID_D`


---

## ⚙️ 3. Configuração do Servidor (Google Apps Script)

Utilizamos este script como a "ponte" entre o site do participante e a nossa planilha.

1. Na planilha do Google, acessamos **Extensões** > **Apps Script**.
2. Limpamos o editor e colamos o nosso código de integração:

JavaScript

```
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Dados_Experimento');
    var data = JSON.parse(e.postData.contents);
    
    // Mapeamos as 16 colunas conforme nosso protocolo
    sheet.appendRow([
      new Date(),
      data.Sessao,
      data.Audiologia,
      data.Perfil,
      data.Idade,
      data.Genero,
      data.Escolaridade,
      data.Lateralidade,
      data.Estado,
      data.Poema,
      data.Esforco,
      data.ID_E,
      data.Deslocamento,
      data.ID_S,
      data.Duracao,
      data.ID_D
    ]);
    
    return ContentService.createTextOutput(JSON.stringify({"result":"success"})).setMimeType(ContentService.MimeType.JSON);
  } catch (f) {
    return ContentService.createTextOutput("Erro: " + f);
  }
}
```

3. Salvamos o projeto e clicamos em **Implantar** > **Nova implantação**.
4. Configuramos como **App da Web**, definindo o acesso para **"Qualquer pessoa"**.
5. Copiamos a **URL do App da Web** gerada para a conexão final.

---

## 🔗 4. Conectando a Interface ao Servidor

1. Abrimos o nosso arquivo `index.html`.
2. Localizamos a linha `465` (dentro da função `enviar()`).
3. Substituímos o link de marcação pela URL que geramos no passo anterior.

---
## 🚀 5. Hospedagem para Coleta Remota (GitHub Pages)

Hospedamos o nosso teste gratuitamente no GitHub para enviá-lo aos participantes:

1. Criamos um repositório público (Ex: `experimento-semiotica`).
2. Subimos o arquivo `index.html`, o `LEIA-ME.md` e a nossa pasta `videos` completa.
3. Em **Settings** > **Pages**, ativamos o deploy a partir da branch `main`.
4. Após o processamento, utilizamos o link gerado como o endereço oficial da nossa coleta.

---

## ✅ Checklist de Validação

- [ ] Confirmamos os 20 vídeos na pasta `videos/`?
- [ ] Atualizamos o link do Google Script no `index.html`?
- [ ] O repositório está configurado como "Público"?
- [ ] Realizamos um teste e os dados surgiram corretamente nas 16 colunas da planilha?

---

# PROCEDIMENTO DE ANÁLISE

Desenvolvemos este módulo para complementar o sistema de coleta. Trata-se de uma aplicação web (_Dashboard_) capaz de consumir os nossos dados brutos em tempo real, processar as médias em módulo e desenhar as curvas psicofísicas de percepção.

---

## 📁 1. Arquivo Necessário

Garantimos que o arquivo abaixo esteja no mesmo repositório:

- `visualizacao.html` : O código-fonte do nosso painel de análise.

---

## ⚙️ 2. Configurando a API de Leitura

Para que o nosso Dashboard consiga ler os dados, atualizamos o servidor com uma função de busca (`doGet`). O código completo que utilizamos é:

JavaScript

```
// FUNÇÃO PARA RECEBER DADOS DO TESTE (Gravação)
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Dados_Experimento');
    var data = JSON.parse(e.postData.contents);
    
    sheet.appendRow([
      new Date(), data.Sessao, data.Audiologia, data.Perfil, data.Idade,
      data.Genero, data.Escolaridade, data.Lateralidade, data.Estado,
      data.Poema, data.Esforco, data.ID_E, data.Deslocamento, data.ID_S,
      data.Duracao, data.ID_D
    ]);
    
    return ContentService.createTextOutput(JSON.stringify({"result":"success"})).setMimeType(ContentService.MimeType.JSON);
  } catch (f) {
    return ContentService.createTextOutput("Erro: " + f);
  }
}

// FUNÇÃO PARA ENVIAR DADOS AO DASHBOARD (Leitura)
function doGet(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Dados_Experimento');
    var rows = sheet.getDataRange().getValues();
    
    var headers = rows[0];
    var data = [];
    for (var i = 1; i < rows.length; i++) {
      var rowData = {};
      for (var j = 0; j < headers.length; j++) {
        rowData[headers[j]] = rows[i][j];
      }
      data.push(rowData);
    }
    
    return ContentService.createTextOutput(JSON.stringify(data)).setMimeType(ContentService.MimeType.JSON);
  } catch (f) {
    return ContentService.createTextOutput(JSON.stringify({"error": f.toString()})).setMimeType(ContentService.MimeType.JSON);
  }
}
```

---

## 🔗 3. Conectando o Dashboard

No arquivo `visualizacao.html`, inserimos a URL do nosso Script na constante `API_URL` (próximo à linha 190). Isso nos permite visualizar os dados em qualquer navegador sem a necessidade de um servidor local.

---

## 🧠 4. Lógica de Processamento Matemático (ETL)

Ao carregarmos o Dashboard, executamos o seguinte processo:

1. **Extração (Fetch):** Nossa interface solicita o banco de dados completo ao script.
2. **Tratamento (Módulo):** Varremos as linhas e aplicamos `Math.abs()` aos valores do VAS. Isso garante que avaliemos a magnitude independentemente da lateralidade da exibição do vídeo.
3. **Cálculo da Curva:** Calculamos a média aritmética e a convertemos em um pseudo-expoente, permitindo-nos visualizar o comportamento da Lei de Stevens.

---

## 🖥️ 5. Como Operamos o Painel

Projetamos o painel com dois modos de análise:

- **Comparar Perfis:** Isolamos uma variável (ex: Esforço) para observar como os quatro perfis diferem em sua percepção.
- **Comparar Variáveis:** Isolamos um perfil (ex: Nativos) para entender qual variável ele percebe com maior intensidade.

Utilizamos o **slider da Escala VAS Virtual** para simular julgamentos e observar o peso fenomenológico exato de cada escolha em nossas curvas matemáticas.

---

Nosso pacote está completo: Interface de Coleta, Banco de Dados e Dashboard de Processamento em Tempo Real. Estamos prontos para avançar!

---

# 📈 INTERPRETAÇÃO DO DASHBOARD

O gráfico apresentado em nosso painel de visualização é uma adaptação experimental do **Modelo de Quadrado em Pêndulo de Matte**. Utilizamos a **Lei de Potência de Stevens** ($S = kI^n$) como o motor matemático para descrever como a tensão semiótica é percebida pelos diferentes perfis de informantes.
### 1. Correspondência de Eixos (Matte & Stevens)

Para que os dados fossem processados pelo dashboard, estabelecemos uma correlação direta entre as categorias da semiótica tensiva e as variáveis psicofísicas:

- **Eixo X (Abscissa) | Extensão (O "Quanto"):** Representa a grandeza física do estímulo (Duração, Deslocamento e Esforço). No gráfico, este eixo varia de 0 a 50, correspondendo à distância absoluta do marco zero na escala VAS.
- **Eixo Y (Ordenada) | Intensidade (O "Como"):** Representa a magnitude da sensação percebida pelo observador. É a dimensão do sensível, traduzida em uma escala de impacto perceptivo de 0 a 100%.
### 2. Dinâmica do Pêndulo e a Curvatura ($n$)

A forma da curva gerada pelo nosso sistema revela em qual regime tensivo a percepção do participante se encontra, simulando a oscilação do pêndulo de Matte:

- **Regime de Ascendência (Curva Convexa / $n > 1$):**
    - **Interpretação:** Um pequeno incremento na extensão física (Eixo X) gera um salto desproporcional na intensidade percebida (Eixo Y).
    - **Semiótica:** Corresponde ao polo da **Surpresa** e do acontecimento estético. O sinalizante "gasta" pouco movimento para gerar muito sentido de tensão. É a marca da execução poética/estética em Libras.

- **Regime de Decidência ou Atenuação (Curva Côncava / $n < 1$):**
    - **Interpretação:** É necessário um grande aumento na extensão física para que o observador note um aumento sutil na intensidade.
    - **Semiótica:** Corresponde ao polo da **Estasia** ou da sinalização utilitária. A percepção é amortecida, e o movimento torna-se previsível, inteligível e focado na função referencial, perdendo o impacto sensível.

- **Equilíbrio Linear ($n \approx 1$):**
    - Indica uma percepção puramente proporcional, onde o sensível e o inteligível caminham em simetria técnica.
### 3. Análise Comparativa dos Perfis

O objetivo central de utilizarmos este gráfico em nossa análise é verificar se o **domínio linguístico** altera a posição do pêndulo semiótico:

1. **Nativos (NAT):** Esperamos observar curvas mais "ascendentes" ($n$ mais alto), indicando que o conhecimento profundo da língua permite ao surdo perceber microvariações de tensão que o leigo ignora.
2. **Absolutos (ABS):** Atuam como nosso grupo de controle. Suas curvas tendem a ser mais lineares ou atenuadas, baseando-se em uma percepção intuitiva da força física (pantomímica), sem o filtro da gramática poética da Libras.


