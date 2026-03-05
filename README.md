Atividade avaliativa: Resumir os passos para desenvolver o software no arquivo markdown e descrever sua arquitetura.  Link de referencia: https://www.youtube.com/watch?v=hffTmMVVdpo

---

# Resumo do Desenvolvimento do Software

O Software consiste em um Sistema de notificações em tempo real com painel admin, níveis de prioridade e marcação de leitura.

# Sobre o Projeto

Sistema completo de notificações em tempo real para aplicações web. O usuário recebe alertas ao vivo no navegador, sem necessidade de atualizar a página, com suporte a múltiplos usuários e três níveis de prioridade. O projeto foi criado durante um desafio ao vivo no canal da Codecon, onde um desenvolvedor júnior, um pleno e um sênior construíram suas versões em 2 horas.

---

# Funcionalidades

- Notificações em tempo real — via WebSocket, sem refresh
- Painel de admin — dispara notificações para um ou mais usuários
- Marcar como lida — controle de estado por notificação
- Níveis de prioridade — informação, alerta e crítico com tratamento visual distinto
- Notificação crítica impossível de ignorar — destaque visual e/ou sonoro

---

# Passos para desenvolver o software

## 1. Definir o objetivo do sistema

Criar um sistema de notificações em tempo real para aplicações web que permita aos usuários receber alertas instantaneamente no navegador sem necessidade de atualizar a página.

---

## 2. Implementar comunicação em tempo real

Utilizar uma tecnologia de comunicação persistente entre cliente e servidor:

- WebSocket (principal opção)
- Server-Sent Events (SSE) como alternativa

Essas tecnologias permitem que o servidor envie notificações automaticamente ao navegador.

---

## 3. Criar o backend de notificações

O backend deve ser responsável por:

- gerar notificações
- definir prioridade
- enviar notificações aos usuários conectados
- armazenar notificações
- controlar estado de leitura

---

## 4. Criar o painel administrativo

O painel admin deve permitir:

- criar notificações
- selecionar usuários destinatários
- definir nível de prioridade
- disparar notificações em tempo real

---

## 5. Implementar níveis de prioridade

Cada notificação deve possuir um nível:

- Informação
- Alerta
- Crítico

Esses níveis determinam:

- aparência visual
- comportamento da notificação
- destaque (som ou animação)

Notificações críticas devem receber maior destaque e não devem ser facilmente ignoradas.

---

## 6. Implementar controle de leitura

Cada notificação deve possuir estado:

- lida
- não lida

O usuário pode marcar notificações como lidas e o sistema deve atualizar esse estado.

---

## 7. Desenvolver interface do usuário

Criar um painel de notificações no frontend que permita:

- visualizar notificações recebidas
- destacar prioridades
- mostrar contador de notificações não lidas
- marcar notificações como lidas

---

## 8. Implementar funcionalidades extras (opcional)

Possíveis melhorias:

- contador de notificações não lidas
- animação ou som ao receber notificação
- histórico de notificações
- push notifications do navegador
- suporte a múltiplos usuários simultâneos

---

## 9. Implementar reconexão automática

Caso a conexão WebSocket seja perdida, o cliente deve:

- tentar reconectar automaticamente
- restaurar a comunicação com o servidor

---

# Arquitetura do Sistema

A arquitetura segue um modelo **cliente-servidor com comunicação em tempo real**, combinando API REST, mensageria e WebSockets.

---

# Camadas da Aplicação

## Models

Representam as entidades do banco de dados.

Exemplos:

- User
- Notification
- UserNotification

Essas classes são utilizadas para mapear os dados da aplicação para as tabelas do banco.

---

## Data

Contém o **DbContext do Entity Framework**, responsável por:

- configurar conexão com o banco
- mapear entidades
- executar persistência de dados

---

## Repositories

Camada responsável pelo acesso aos dados.

Implementa o **Repository Pattern**, que cria uma abstração entre a aplicação e o banco de dados.

Responsabilidades:

- consultar dados
- salvar registros
- atualizar informações
- remover registros

---

## Services

Camada responsável pela **lógica de negócio da aplicação**.

Utiliza os repositórios para:

- aplicar regras de negócio
- processar notificações
- definir prioridades
- controlar estado de leitura

---

## Controllers

Responsáveis pelos **endpoints da API REST**.

Funções:

- receber requisições HTTP
- validar dados
- chamar serviços
- retornar respostas para o frontend

---

# Padrões Arquiteturais Implementados

## Repository Pattern

Abstrai o acesso ao banco de dados, evitando dependência direta entre regras de negócio e persistência.

Benefícios:

- desacoplamento
- maior facilidade de manutenção
- melhor testabilidade

---

## Dependency Injection

Utiliza a **injeção de dependências nativa do ASP.NET Core**.

Permite que dependências sejam fornecidas automaticamente pelo framework.

Benefícios:

- baixo acoplamento
- maior facilidade de testes
- organização do código

---

## Service Layer Pattern

Define uma camada dedicada para regras de negócio.

Fluxo típico:

Controller → Service → Repository → Database

Isso evita que controllers possuam lógica de negócio complexa.

---

# Arquitetura de Comunicação

O sistema utiliza uma arquitetura **orientada a eventos**, com mensageria e comunicação em tempo real.

```mermaid
graph LR
    A[Vue 3 Frontend] -->|HTTP/REST| B[Express Backend]
    B --> C[(PostgreSQL Database)]
    B -->|Publish| D[RabbitMQ Queue]
    A -.WebSocket.-> E[Consumer Worker]
    D -->|Consume| E
    E -.WebSocket Broadcast.-> A
