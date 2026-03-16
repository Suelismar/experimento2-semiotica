# 🧪 Laboratório de Semiótica: Experimento de Estimativa de Magnitude

Este repositório contém o sistema completo de coleta de dados para o experimento de psicofísica (Estimativa de Magnitude) focado na percepção da tensão semiótica em execuções da Libras.

A arquitetura é dividida em duas frentes:

1. **Front-end (Interface):** Aplicação web blindada contra vieses de lateralidade e cor, com controle de tempo de tela, randomização de estímulos e bloqueio para dispositivos móveis.
2. **Back-end (Servidor):** Integração via Google Apps Script para extração e tabulação automatizada dos dados em nuvem.

---

## 📁 1. Estrutura de Arquivos Obrigatória

Para que o experimento funcione corretamente, os arquivos devem estar organizados localmente e no GitHub **exatamente** com esta estrutura de pastas e nomenclaturas:

Plaintext

```
Experimento_Semiotica/
│
├── index.html            # O código principal da interface (Front-end)
├── LEIA-ME.md            # Este arquivo de instruções
│
└── videos/               # Pasta contendo os estímulos visuais (Obrigatório letras minúsculas)
    │
    ├── csa_est.mp4       # Poema CSA - Execução Estética
    ├── csa_util.mp4      # Poema CSA - Execução Utilitária
    ├── vv_est.mp4        # Poema VV - Execução Estética
    ├── vv_util.mp4       # Poema VV - Execução Utilitária
    ├── ac_est.mp4        # Poema AC - Execução Estética
    ├── ac_util.mp4       # Poema AC - Execução Utilitária
    │
    ├── treino_a.mp4      # Vídeo neutro para calibragem motora
    └── treino_b.mp4      # Vídeo neutro para calibragem motora
```

_(Nota: Todos os vídeos devem estar no formato `.mp4` com compressão H.264 para garantir a compatibilidade com todos os navegadores)._

---

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
