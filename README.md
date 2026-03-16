# 🧪 Laboratório de Semiótica: Experimento de Estimativa de Magnitude



# PROCEDIMENTO DE COLETA

Este repositório contém o sistema completo de coleta de dados para o experimento de psicofísica (Estimativa de Magnitude) focado na percepção da tensão semiótica em execuções da Libras.

A arquitetura é dividida em duas frentes:

1. **Front-end (Interface):** Aplicação web blindada contra vieses de lateralidade e cor, com controle de tempo de tela, randomização de estímulos e bloqueio para dispositivos móveis.
2. **Back-end (Servidor):** Integração via Google Apps Script para extração e tabulação automatizada dos dados em nuvem.

---

## 📁 1. Estrutura de Arquivos Obrigatória

Para que o experimento funcione corretamente e o código consiga puxar o vídeo exato para cada pergunta, os arquivos devem estar organizados localmente e no GitHub **exatamente** com esta estrutura de pastas e nomenclaturas.

O padrão de nomenclatura agora segue a regra `[id_do_poema]_[dimensão]_[polo].mp4`. Ao todo, a pasta `videos` deve conter 20 arquivos:

Plaintext

```
Experimento_Semiotica/
│
├── index.html            # O código principal da interface de coleta (Front-end)
├── LEIA-ME.md            # Este arquivo de instruções
│
└── videos/               # Pasta contendo os estímulos visuais (Obrigatório letras minúsculas)
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

_(Nota: Todos os vídeos devem estar estritamente no formato `.mp4` com compressão H.264 para garantir a compatibilidade de reprodução automática em todos os navegadores, sem exceções)._

*** Assim fica documentada a justificativa de cada arquivo e evita-se qualquer erro de digitação (um underline a mais ou a menos quebraria a chamada do vídeo no código).
## 📊 2. Configuração do Banco de Dados (Google Sheets)

Antes de rodar o código, é necessário preparar o local onde os dados serão salvos.

1. Acesse o [Google Sheets](https://sheets.google.com/) e crie uma planilha em branco.
    
2. Renomeie a primeira aba (na parte inferior da tela) para exatamente: `Dados_Experimento`
    
3. Na **Linha 1**, crie os cabeçalhos das 16 colunas na ordem exata abaixo:
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

Este script atuará como a "ponte" entre o site do participante e a sua planilha.

1. Na sua planilha do Google, clique no menu superior em **Extensões** > **Apps Script**.
2. Apague qualquer código que estiver lá e cole o código abaixo:

JavaScript

```
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Dados_Experimento');
    var data = JSON.parse(e.postData.contents);
    
    // Mapeamento exato das 16 colunas
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

3. Clique no ícone de **Salvar** (disquete).
4. Clique no botão azul superior direito **Implantar** > **Nova implantação**.
5. Clique no ícone de engrenagem ao lado de "Selecionar tipo" e escolha **App da Web**.
6. Preencha as configurações:
    - **Descrição:** Coleta de Dados Doutorado
    - **Executar como:** _Eu (seu e-mail)_
    - **Quem pode acessar:** _Qualquer pessoa_ (⚠️ **MUITO IMPORTANTE** para os participantes não precisarem fazer login).

7. Clique em **Implantar**. Autorize os acessos na sua conta Google (pode aparecer um aviso de segurança, clique em "Avançado" e depois em "Acessar projeto").
8. Copie a **URL do App da Web** gerada (ela termina em `/exec`).

---

## 🔗 4. Conectando a Interface ao Servidor

1. Abra o arquivo `index.html` em qualquer editor de texto simples ou de código (como o Bloco de Notas ou VS Code).
2. Role até a linha `465` (dentro da função `enviar()`).
3. Substitua o link de marcação pela URL do App da Web que você acabou de copiar no passo anterior.

**Como deve ficar:**

JavaScript

```
      // ── INSIRA O LINK DO SEU GOOGLE APPS SCRIPT ABAIXO ──
      fetch('https://script.google.com/macros/s/AKfycbwSUA_CHAVE_AQUI_Gv4x/exec', {
        method: 'POST', mode: 'no-cors',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });
```

4. Salve o arquivo `index.html`.

---

## 🚀 5. Hospedagem para Coleta Remota (GitHub Pages)

Para enviar o teste aos participantes, hospede-o gratuitamente no GitHub:

1. Crie uma conta no [GitHub](https://github.com/).
2. Crie um novo repositório público (Ex: `experimento-semiotica`).
3. Faça o upload do arquivo `index.html`, do `LEIA-ME.md` e da pasta `videos` inteira para dentro do repositório.
4. Vá na aba **Settings** (Configurações) do seu repositório.
5. No menu lateral esquerdo, clique em **Pages**.
6. Sob a seção "Build and deployment" -> "Source", selecione **Deploy from a branch**.
7. Logo abaixo, onde diz "Branch", mude de `none` para `main` (ou `master`) e clique em **Save**.
8. Aguarde de 2 a 5 minutos. O GitHub gerará um link no topo da página (ex: `https://seu-usuario.github.io/experimento-semiotica/`).
9. Este é o link oficial da coleta. Pode enviá-lo aos participantes!

---

## ✅ Checklist de Validação Antes da Coleta

- [ ] Os 8 vídeos (6 do corpus + 2 de treino) estão na pasta `videos/`?
- [ ] O arquivo `index.html` tem o link do Google Script atualizado?
- [ ] O repositório no GitHub está como "Público"?
- [ ] O primeiro teste feito por você registrou todas as 16 colunas perfeitamente no Google Sheets?


# PROCEDIMENTO DE ANÁLISE 

Este módulo complementa o sistema de coleta de dados do Experimento de Estimativa de Magnitude. Ele consiste em uma aplicação web (_Dashboard_) capaz de consumir os dados brutos da planilha do Google Sheets em tempo real, processar as médias em módulo e desenhar as curvas psicofísicas de percepção.

---

## 📁 1. Arquivo Necessário

Certifique-se de que o arquivo abaixo está salvo no mesmo repositório do seu projeto:

- `visualizacao.html` : O código-fonte do painel de análise.

---
## ⚙️ 2. Configurando a API de Leitura (Google Apps Script)

Para que o Dashboard consiga ler os dados da sua planilha, o seu servidor (Google Apps Script) precisa de uma função de "Busca" (`doGet`).

1. Abra sua planilha do Google Sheets > **Extensões** > **Apps Script**.
    
2. Certifique-se de que o seu código contém tanto a função de gravação (`doPost`) quanto a nova função de leitura (`doGet`). O código completo deve ser exatamente este:
    

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

3. Salve o projeto.
    
4. **⚠️ PASSO CRÍTICO:** Clique em **Implantar** > **Nova Implantação**. Sempre que alterar o script, você deve gerar uma nova versão.
    
5. Copie a nova **URL do App da Web**.
    

---

## 🔗 3. Conectando o Dashboard

1. Abra o arquivo `visualizacao.html` em um editor de código.
    
2. Localize a constante `API_URL` (próximo à linha 190).
    
3. Cole a sua nova URL do Apps Script entre as aspas:
    

JavaScript

```
        // ── 1. CONFIGURAÇÃO DE ACESSO AOS DADOS ──
        const API_URL = "https://script.google.com/macros/s/SUA_NOVA_CHAVE_AQUI/exec";
```

4. Salve o arquivo. Você pode abrir o `visualizacao.html` com dois cliques direto no seu navegador de preferência (não precisa de servidor local para rodar).
    

---

## 🧠 4. Lógica de Processamento Matemático (ETL)

Quando o Dashboard é carregado, o seguinte processo ocorre em frações de segundo (detalhado para fins de relatório metodológico):

1. **Extração (Fetch):** O HTML faz uma requisição assíncrona ao Google Apps Script, que retorna todo o banco de dados em formato JSON.
    
2. **Tratamento de Dados (Módulo):** O script JavaScript varre as linhas, agrupa os participantes por _Perfil Sociolinguístico_ e aplica a função `Math.abs()` aos valores do VAS. Isso garante que a intensidade da magnitude seja avaliada independentemente da lateralidade (Esquerda/Direita) em que o vídeo foi exibido.
    
3. **Cálculo da Curva Visuomotora:** A média aritmética das variáveis (Esforço, Deslocamento e Duração) é calculada. Para plotar a curva no gráfico, essa média bruta é convertida em um pseudo-expoente, simulando o comportamento da Lei de Potência de Stevens no ambiente gráfico bidimensional.
    

---

## 🖥️ 5. Como Operar o Painel

O painel possui dois modos de análise estrita:

- **Comparar Perfis (Modo Individual):** Isola uma única variável cinematográfica (ex: Esforço) e plota 4 curvas diferentes. Serve para responder: _"Como Nativos, Bilíngues, Iniciantes e Absolutos diferem na percepção de Esforço?"_.
    
- **Comparar Variáveis (Modo Integrado):** Isola um único Perfil (ex: Nativos) e plota as 3 curvas das variáveis. Serve para responder: _"O que os Nativos percebem com maior intensidade: a Duração, o Deslocamento ou o Esforço?"_.
    

**A Escala VAS Virtual:** O _slider_ abaixo do gráfico permite simular o julgamento do participante. Ao arrastá-lo, a tabela dinâmica cruza a posição do cursor (Eixo X) com as curvas matemáticas geradas em tempo real (Eixo Y), revelando o peso fenomenológico exato daquela escolha para cada perfil.

---

Seu pacote está completo: Interface de Coleta (Cega e Randômica), Banco de Dados em Nuvem (Google Sheets) e Dashboard de Processamento Matemático em Tempo Real. Pronto para avançar!
