![Tela de login do Thinking Temperature em execução](documentation/screenshot.png)

# Thinking Temperature

Protótipo acadêmico de Internet das Coisas que combina um frontend React para autenticação e visualização de temperatura/ocupação com um sketch Arduino de leitura de temperatura e exibição em LCD. A proposta original prevê economia de energia, mas a integração completa com controle de ar-condicionado não está implementada nos arquivos atuais.

[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript)
[![License MIT](https://img.shields.io/badge/License-MIT-blue)](LICENSE)

## 📋 Table of contents

- [✨ Features](#features)
- [🧪 Technologies](#technologies)
- [🛠️ Environment setup](#environment-setup)
- [🚀 Getting started](#getting-started)
- [🗂️ Project structure](#project-structure)
- [✅ Testing and quality](#testing-and-quality)
- [🔎 Troubleshooting](#troubleshooting)
- [🤝 Contributing](#contributing)
- [📜 License](#license)

<a id="features"></a>

## ✨ Features

- Login por e-mail/senha com Firebase Authentication.
- Painel web que escuta `/temperatura` e `/qntPessoas` no Realtime Database.
- Botões de modo automático, ligar e desligar que exibem mensagens; não há comando implementado para um atuador físico.
- Sketch Arduino com leitura analógica de temperatura, LCD 16×2 e saída serial a 9600 baud.

O sketch versionado não envia dados ao Firebase, não conta pessoas nem controla ar-condicionado. Essas integrações faziam parte da proposta acadêmica e permanecem pendentes.

<a id="technologies"></a>

## 🧪 Technologies

| Tecnologia | Versão declarada | Finalidade |
| --- | --- | --- |
| [React](https://react.dev/) | `^16.9.0` | Frontend |
| [Create React App](https://create-react-app.dev/) | `react-scripts` `3.1.1` | Desenvolvimento, build e teste |
| [Firebase](https://firebase.google.com/docs) | `^7.2.1` | Autenticação e Realtime Database |
| [React Router](https://v5.reactrouter.com/) | `^5.0.1` | HashRouter |
| [Bootstrap](https://getbootstrap.com/docs/4.3/) | `^4.3.1` | Estilos |
| [Arduino / LiquidCrystal](https://docs.arduino.cc/libraries/liquidcrystal/) | Versões não declaradas | Leitura analógica, serial e LCD |

As dependências web são declaradas em `frontend/package.json`. Node.js não está fixado; a reprodução usa Node.js 14.21.3/npm 6.14.18, compatível com a geração do CRA 3.

<a id="environment-setup"></a>

## 🛠️ Environment setup

Requer Git, Node.js e npm. O projeto não fixa Node.js em `engines`, `.nvmrc` ou `.node-version`. **Node.js 14.21.3 com npm 6.14.18** foi escolhido para a reprodução da pilha histórica; não é uma versão declarada pelo repositório nem uma atualização de dependências.

```bash
nvm install 14.21.3
nvm use 14.21.3
git clone https://github.com/paulodaluz/ThinkingTemperature.git
cd ThinkingTemperature/frontend
npm install
```

Não há lockfile versionado: use `npm install`, não `npm ci`. Este guia adota npm, disponível com o Node.js, sem prometer uma resolução de dependências idêntica entre instalações. Não há Husky, hooks versionados ou script `prepare` para ativar.

### Frontend Firebase

`frontend/src/services/auth.js` inicializa o Firebase a partir de um objeto no próprio arquivo. Configure um projeto Web próprio, habilite login por e-mail/senha e o Realtime Database. O painel espera valores numéricos nos caminhos `/temperatura` e `/qntPessoas`. Não existe leitor de configuração Firebase a partir de `.env`.

`frontend/public/index.html` carrega Bootstrap 4.3.1 por CDN; os estilos de formulário dependem desse recurso externo.

Crie o usuário no seu projeto Firebase por um fluxo administrativo apropriado: o link de cadastro da interface aponta para `#` e não implementa criação de conta. Revise as regras do banco sem expor dados publicamente.

### Arduino component

`temperatura___lcd.ino` usa `LiquidCrystal lcd(12, 11, 5, 4, 3, 2)`, LCD 16×2, entrada analógica `0` e serial a `9600`. A proposta original descreve Arduino UNO, protoboards, sensor de temperatura, LCD, potenciômetro e sensores ultrassônicos; o sketch atual implementa apenas temperatura/LCD/serial. O modelo elétrico do sensor e a montagem completa não estão especificados em arquivos de esquema.

A compilação/upload requer ferramentas Arduino, biblioteca LiquidCrystal e hardware compatível já preparados. Essas ferramentas não foram instaladas, e o firmware não foi compilado nem carregado nesta tarefa.

<a id="getting-started"></a>

## 🚀 Getting started

Na pasta `frontend/`:

```bash
npm start
```

A URL padrão é `http://localhost:3000`; a tela inicial é o login e a rota do painel é `#/home`. Para porta temporária em shell POSIX: `PORT=4302 BROWSER=none npm start`.

```bash
npm run build
```

A saída é `frontend/build/`. Não existe `start:prod`. `frontend/firebase.json` contém configuração histórica de Hosting com `build` e rewrite para `index.html`; publicação não foi executada. Confira o projeto-alvo antes de qualquer deploy.

Validação com Node.js 14.21.3/npm 6.14.18: dependências instaladas, `CI=true npm run build` concluído e o único teste de montagem passou com `CI=true npm test -- --watchAll=false --runInBand`. O servidor de desenvolvimento iniciou na porta temporária 4302 e respondeu HTTP 200; a tela de login foi renderizada sem erros JavaScript observados. A imagem mostra o frontend compilado servido localmente, sem enviar login ou acessar dados do Firebase. Os processos foram encerrados após a verificação.

<a id="project-structure"></a>

## 🗂️ Project structure

| Caminho | Responsabilidade |
| --- | --- |
| `frontend/` | Aplicação React; diretório dos comandos npm |
| `frontend/src/App.js` | Rotas de login e painel |
| `frontend/src/pages/Login/` | Autenticação |
| `frontend/src/pages/Home/` | Leituras do banco e botões do painel |
| `frontend/src/services/` | Inicialização Firebase e funções de leitura |
| `frontend/firebase.json` | Configuração de Hosting |
| `temperatura___lcd.ino` | Leitura analógica, LCD e serial |

<a id="testing-and-quality"></a>

## ✅ Testing and quality

Execute os comandos web dentro de `frontend/`:

| Comando | Finalidade |
| --- | --- |
| `npm test -- --watchAll=false --runInBand` | Teste existente de montagem de `App` |
| `npm run build` | Build estático em `frontend/build/` |

Não há scripts independentes de lint ou formatação, testes do firmware ou automação da integração física. `npm run eject` não é necessário para executar o projeto.

<a id="troubleshooting"></a>

## 🔎 Troubleshooting

| Situação | Evidência e encaminhamento |
| --- | --- |
| Login não funciona | Configure Firebase próprio, usuário e provedor e-mail/senha. O link de cadastro não implementa registro. |
| Painel sem dados | Confira os caminhos `temperatura` e `qntPessoas` e as regras do banco. O sketch não publica esses dados. |
| Botões não controlam equipamento | Os handlers apenas alteram propriedades de `setState` e exibem alertas; não enviam comandos a hardware. |
| Erro ao executar npm na raiz | O manifesto fica em `frontend/`; execute os comandos nessa pasta. |

A verificação cobriu a instalação, o build, o teste de montagem e a renderização do login. Autenticação, leituras do banco, integração física, firmware e publicação não foram validados. As chamadas para Firebase ficaram bloqueadas durante a captura, mantendo a interface inicial sem dados de usuários.

<a id="contributing"></a>

## 🤝 Contributing

Crie uma branch a partir de `master`, mantenha as instruções compatíveis com os arquivos do projeto e descreva a alteração em um pull request. Execute as verificações aplicáveis descritas acima; registre limitações de serviços externos e ambiente. Não inclua credenciais, dados pessoais, `node_modules` ou arquivos gerados na entrega.

O trabalho acadêmico original também credita Alisson Muhammad e Vithor Gabriel.

<a id="license"></a>

## 📜 License

Este projeto está sob a licença **MIT** em [LICENSE](LICENSE). Os avisos e créditos existentes foram preservados.

---

<p align="center">Made with 💜 by <a href="https://github.com/paulodaluz">Paulo da Luz</a></p>
