# Introdução passo a passo ao framework mobile Ionic

📖 **Leia o tutorial: [https://stahe.github.io/br-ionic-etape-par-etape-oct-2026/](https://stahe.github.io/br-ionic-etape-par-etape-oct-2026/)**

Este curso ensina como construir um aplicativo **mobile** com o framework [Ionic](https://ionicframework.com) 9, combinado com o [Angular](https://angular.dev) 22 e o [Capacitor](https://capacitorjs.com) 8: um aplicativo Android cujas telas são renderizadas **no celular** a partir de dados JSON fornecidos por um servidor. É um aplicativo web (HTML, CSS, TypeScript) que o Capacitor empacota em um aplicativo Android — o mesmo código também roda em um navegador.

Ele segue o roteiro do curso [Introdução passo a passo ao framework mobile Flutter](https://stahe.github.io/br-flutter-etape-par-etape-sept-2026/) (e dos cursos de [React](https://stahe.github.io/br-react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/br-vuejs-etape-par-etape-sept-2026/) e [Angular](https://stahe.github.io/br-angular-etape-par-etape-sept-2026/)): mesmo servidor, mesmas telas, escritas no estilo Ionic, para um smartphone.

| Curso Flutter | Curso Ionic |
|---|---|
| a linguagem Dart | TypeScript |
| o engine do Flutter renderiza cada pixel | componentes web com aparência nativa (Material Design no Android), exibidos pela WebView do celular |
| widgets, um método `build()`, `setState` | componentes Angular, templates HTML, signals (`signal`, `computed`, `effect`) |
| `InheritedWidget`, provider (`ChangeNotifier`) | injeção de dependência do Angular, serviços com signals |
| `Form` e `FormField` | formulários reativos do Angular |
| go_router, `ShellRoute` + `NavigationBar` | roteador do Angular, `ion-tabs`, guards `CanActivateFn` |
| `Future`, `Stream` | `Promise`, `Observable` (RxJS), `resource()` |
| `shared_preferences` | `@capacitor/preferences` |
| um cliente HTTP que trata cookies (no celular) | `HttpClient`; no celular, o plugin CapacitorHttp define e devolve o cookie |

O servidor, no entanto, permanece inalterado: é o servidor JSON do aplicativo **RdvMedecins**, já utilizado pelos clientes Flutter, React, Vue.js e Angular.

## A abordagem: muitos exemplos curtos e, depois, um estudo de caso

O curso é estruturado em torno de **25 exemplos curtos**, cada um focado em um único conceito, que têm os mesmos números (e mostram as mesmas telas) dos cursos de Flutter, React, Vue.js e Angular. Eles formam um único projeto Ionic: basta um comando `npm install`, seguido de `npm start`; você seleciona o exemplo no menu do aplicativo (ou por sua URL: http://localhost:8100/ex05).

| Capítulo | Conteúdo | Exemplos |
|---|---|---|
| Primeiros passos | um projeto Ionic + Angular + Capacitor, componentes Ionic, signals e propriedades computadas, templates (`@if`, `@for`), eventos, todos os tipos de campo, um reducer, validação, formulários reativos, formatação (pipes, `Intl`) | 01–09 |
| Componentes | entradas (`input`), saídas (`output`, `model`), projeção de conteúdo (`ng-content`, `ng-template`), um array genérico, ciclo de vida, injeção de dependência, diretivas e animações, diálogo de confirmação (`AlertController`) | 10–16 |
| Roteamento | o roteador do Angular com o Ionic: rotas, parâmetros, `ion-tabs`, carregamento tardio (lazy loading), páginas 404, guards | 17–18 |
| Operações assíncronas e estado compartilhado | `Promise`, `Observable`, geradores assíncronos, timers, anti-bounce, respostas obsoletas (`switchMap`), `resource()`, serviços com signals, `@capacitor/preferences`, modo escuro | 19–20 |
| Internacionalização | dicionários JSON, parâmetros, plurais (`Intl.PluralRules`), datas, valores, calendário localizado (`ion-datetime`) | 21 |
| O servidor: uma caixa-preta | configuração do servidor JSON, sua API, 48 exemplos com `curl`, o que muda para um cliente mobile | – |
| Comunicação com o servidor | `HttpClient`, o proxy de desenvolvimento, o endereço do servidor (emulador, celular, web), o CapacitorHttp e o cookie do token, uma camada de acesso à API, um view model, erros do servidor associados aos campos | 22–25 |

Cada exemplo é apresentado com seu código completo, comentado linha por linha, e com capturas de tela de sua execução (geradas automaticamente por scripts do [Playwright](https://playwright.dev), na pasta `captures/`).

## O servidor: uma caixa-preta

O servidor é o servidor NestJS das lições anteriores, cujos controllers retornam **JSON**. Esta lição o trata como uma **caixa-preta**: nós o instalamos, estudamos sua API e o consultamos com `curl`, mas não precisamos ler seu código (que é fornecido e comentado para os curiosos).

- Todos os erros têm o mesmo formato: `{ "statusCode": 409, "cle": "ERRORS. LOGIN_TAKEN", "params": {...}, "fields": {...} }` — **chaves** de tradução, nunca texto simples;
- Autenticação por um token JWT em um cookie `httpOnly`: no navegador, é o navegador que o armazena; no celular, é o plugin CapacitorHttp que cuida disso;
- um "modo de teste" para o CAPTCHA, que permite consultar a API com `curl` (e gerar capturas de tela automaticamente).


## O estudo de caso: o cliente Ionic do RdvMedecins

Um aplicativo completo de **agendamento de consultas em um consultório médico**, com **todos** os arquivos listados e comentados.

- **Ionic e Angular modernos**: componentes standalone, signals, um aplicativo sem Zone.js, o novo fluxo de controle de templates, `inject()`, um interceptor HTTP funcional, guards `CanActivateFn` e uma arquitetura em camadas (acesso à API, estado, UI).
- **Três papéis**: `ADMIN` (gerencia médicos e clientes), `DOCTOR` (agenda e cancela consultas), `USER` (o paciente: agenda consultas para si mesmo, gerencia sua conta).
- **Privacidade**: um paciente nunca recebe os nomes dos outros pacientes — o servidor não os envia.
- **Todo o estado da tela está contido na URL**: `/agenda?idMedecin=1&jour=2026-10-13&reserver=7`; no celular, o botão Voltar fecha a janela de agendamento; na web, F5 e Voltar/Avançar funcionam.
- **Validação no servidor**: os formulários exibem, abaixo de cada campo, os erros retornados pela API; bloqueio otimista, nomes duplicados, nome de usuário já utilizado...
- **Sessão**: restabelecida na inicialização (`GET /api/auth/moi`), com a expiração tratada em um único ponto (resposta 401, no interceptor).
- **Adaptado ao mobile**: menu lateral (drawer) (`ion-menu`), sempre visível em telas grandes (`ion-split-pane`), listas em vez de tabelas.
- **Francês / inglês**, incluindo o calendário.
- **Implantação**: a versão web compilada, servida pelo próprio servidor JSON, e o aplicativo Android (APK).

## Conteúdo do repositório

```
exemples_ionic/            os 25 exemplos curtos (um único projeto Ionic)
rdvmedecins-nestjs-json/   o servidor JSON do RdvMedecins (a “caixa-preta”)
rdvmedecins_ionic/         o cliente Ionic do estudo de caso
```

A pasta `android/` de cada projeto não é fornecida: ela é criada por `npx cap add android` (veja o curso).

## Tecnologias

Ionic 9 · Angular 22 · Capacitor 8 · TypeScript 6 · RxJS 7 · @capacitor/app · @capacitor/preferences · ionicons · Playwright (capturas de tela) · no servidor: NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Pré-requisitos

- Noções básicas de TypeScript e Angular, abordadas nos cursos [Introdução ao TypeScript por meio de exemplos](https://stahe.github.io/typescript-sept-2026/) e [Introdução passo a passo ao framework Angular](https://stahe.github.io/angular-etape-par-etape-sept-2026/); de todo modo, os conceitos utilizados são revisados ao longo dos exemplos.
- Node.js 24 (ou 22.22.3 e posteriores, exigido pelo Angular CLI 22), Visual Studio Code e suas extensões Angular Language Service e Ionic, Chrome, Android Studio (para o SDK do Android e o emulador) ou um celular Android; um servidor MySQL (por exemplo, o Laragon no Windows) para o servidor JSON. As instruções de instalação são fornecidas no curso.

## Autor

Este curso, seus exemplos, o cliente Ionic e a adaptação do servidor JSON foram escritos por **Claude**, a IA da [Anthropic](https://www.anthropic.com) (outubro de 2026), a pedido de **Serge Tahé**.
