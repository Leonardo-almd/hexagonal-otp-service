[🇧🇷 Português](#-api-de-tokens-otp-one-time-password) | [🇦🇺 English](#-otp-one-time-password-token-api)

---

# 🇧🇷 API de Tokens OTP (One-Time Password)

Este projeto implementa uma API REST para gerenciamento de tokens OTP (senhas de uso único), seguindo os princípios da Arquitetura Hexagonal.

## Índice

- [Visão Geral](#visão-geral)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Arquitetura](#arquitetura)
- [Requisitos](#requisitos)
- [Configuração do Ambiente](#configuração-do-ambiente)
- [Executando a Aplicação](#executando-a-aplicação)
  - [Com Docker](#com-docker)
  - [Localmente](#localmente)
- [Testes](#testes)
- [Decisões Técnicas](#decisões-técnicas)
- [Considerações de Segurança](#considerações-de-segurança)
- [Escalabilidade](#escalabilidade)

## Visão Geral

Esta API permite a criação e validação de tokens OTP (One-Time Password), que são senhas numéricas de uso único, geralmente utilizadas como segundo fator de autenticação ou para confirmar operações sensíveis.

## Tecnologias Utilizadas

- **Node.js**: Ambiente de execução
- **TypeScript**: Linguagem de programação
- **NestJS**: Framework para construção de aplicações escaláveis
- **Redis**: Banco de dados NoSQL para armazenamento de tokens
- **Docker**: Containerização
- **Jest**: Framework de testes
- **Swagger/OpenAPI**: Documentação da API
- **bcrypt**: Biblioteca para hashing seguro

## Arquitetura

O projeto segue os princípios da Arquitetura Hexagonal (também conhecida como Ports and Adapters), que promove a separação de responsabilidades e a independência do domínio em relação a tecnologias externas.

### Estrutura de Diretórios

```
src/
├── common/                       # Componentes compartilhados
├── config/                       # Configurações da aplicação
├── token-otp/                    # Módulo principal
│   ├── adapters/                 # Adaptadores (controllers, repositories)
│   │   ├── controllers/          # Controladores da API
│   │   ├── infrastructure/       # Implementações de infraestrutura
│   │   ├── model/                # DTOs e mapeadores
│   │   ├── repositories/         # Implementações de repositórios
│   │   └── services/             # Serviços de adaptadores
│   ├── domain/                   # Lógica de domínio
│   │   ├── model/                # Entidades de domínio
│   │   └── ports/                # Portas (interfaces)
│   │       ├── input/            # Portas de entrada
│   │       └── output/           # Portas de saída
│   └── token-otp.module.ts       # Módulo NestJS
└── main.ts                       # Ponto de entrada da aplicação
```

## Requisitos

- Node.js 18+
- Docker e Docker Compose (opcional, para execução containerizada)
- Redis (instalado localmente ou via Docker)

## Configuração do Ambiente

1. Clone o repositório:
   ```bash
   git clone https://github.com/Leonardo-almd/btg-otp-challenge
   cd btg-otp-challenge
   ```

2. Instale as dependências:
   ```bash
   npm install
   ```

3. Configure as variáveis de ambiente:
   - Crie um arquivo `.env` baseado no `env-example.txt`:
   ```bash
   cp env-example.txt .env
   ```
   - Ajuste as variáveis conforme necessário:
   ```
   PORT=3000
   REDIS_HOST=localhost
   REDIS_PORT=6379
   THROTTLE_TTL=5000
   THROTTLE_LIMIT=3
   ```

## Executando a Aplicação

### Com Docker

1. Construa e inicie os containers:
   ```bash
   docker compose up --build
   ```

2. A API estará disponível em `http://localhost:8080/api`
3. A documentação Swagger estará disponível em `http://localhost:8080/api/docs`

### Localmente

1. Certifique-se de que o Redis está em execução:
   ```bash
   redis-server
   ```

2. Inicie a aplicação:
   ```bash
   npm run start:dev
   ```

3. A API estará disponível em `http://localhost:3000/api`
4. A documentação Swagger estará disponível em `http://localhost:3000/api/docs`

## Testes

O projeto inclui testes unitários e end-to-end (e2e):

```bash
# Executar todos os testes unitários
npm test

# Executar testes com watch mode
npm run test:watch

# Executar testes com cobertura
npm run test:cov

# Executar testes e2e
npm run test:e2e
```

## Decisões Técnicas

### Arquitetura Hexagonal

Optamos pela Arquitetura Hexagonal para:
- Isolar o domínio da aplicação de detalhes de infraestrutura
- Facilitar a testabilidade através da inversão de dependências
- Permitir a substituição de componentes externos (como o banco de dados) com impacto mínimo

### Redis como Banco de Dados

Escolhemos o Redis pelos seguintes motivos:
- Performance: Operações extremamente rápidas, essenciais para autenticação
- TTL nativo: Suporte integrado para expiração de chaves, ideal para tokens temporários
- Simplicidade: Fácil configuração e uso para o caso específico de tokens OTP

## Considerações de Segurança

- Os tokens OTP são armazenados em formato hash no Redis
- Implementamos rate limiting para prevenir ataques de força bruta
- Validação rigorosa de todas as entradas para prevenir injeções
- Logs estruturados sem informações sensíveis

## Escalabilidade

O projeto foi projetado considerando escalabilidade em vários níveis:

- **Infraestrutura distribuída com Docker Compose**:
  - Separação clara entre serviços (API, Redis, Nginx)
  - Configuração de rede isolada para comunicação entre serviços
  - Possibilidade de escalar cada serviço independentemente
  - Facilidade para adicionar novos serviços ou réplicas conforme necessário

- **Benefícios da Arquitetura Hexagonal para escalabilidade**:
  - Desacoplamento entre domínio e infraestrutura facilita a distribuição de componentes
  - Interfaces bem definidas permitem substituir implementações por versões mais escaláveis
  - Separação clara de responsabilidades facilita a identificação de gargalos

- **Tecnologias escaláveis**:
  - **Redis** com suporte a clustering para alta disponibilidade e particionamento de dados
  - **Proxy reverso com Nginx** para balanceamento de carga e terminação SSL
  - **NestJS** com suporte a processamento assíncrono e não-bloqueante

- **Práticas de DevOps**:
  - Containerização com Docker para facilitar o deployment em ambientes de nuvem
  - Configurações externalizadas via variáveis de ambiente
  - Monitoramento de performance através de logs estruturados

Esta abordagem de infraestrutura distribuída, combinada com os princípios da arquitetura hexagonal, proporciona uma base sólida para o crescimento da aplicação, permitindo escalar horizontalmente (adicionando mais instâncias) ou verticalmente (aumentando recursos) conforme a demanda.

---

[⬆️ Back to top / Voltar ao topo](#-api-de-tokens-otp-one-time-password)

---

# 🇺🇸 OTP (One-Time Password) Token API

This project implements a REST API for managing OTP (One-Time Password) tokens, following the principles of Hexagonal Architecture.

## Table of Contents

- [Overview](#overview)
- [Technologies Used](#technologies-used)
- [Architecture](#architecture-1)
- [Requirements](#requirements)
- [Environment Setup](#environment-setup)
- [Running the Application](#running-the-application)
  - [With Docker](#with-docker)
  - [Locally](#locally)
- [Tests](#tests)
- [Technical Decisions](#technical-decisions)
- [Security Considerations](#security-considerations)
- [Scalability](#scalability)

## Overview

This API allows the creation and validation of OTP (One-Time Password) tokens, which are single-use numeric passwords typically used as a second authentication factor or to confirm sensitive operations.

## Technologies Used

- **Node.js**: Runtime environment
- **TypeScript**: Programming language
- **NestJS**: Framework for building scalable applications
- **Redis**: NoSQL database for token storage
- **Docker**: Containerization
- **Jest**: Testing framework
- **Swagger/OpenAPI**: API documentation
- **bcrypt**: Secure hashing library

## Architecture

The project follows the principles of Hexagonal Architecture (also known as Ports and Adapters), which promotes separation of concerns and keeps the domain independent from external technologies.

### Directory Structure

```
src/
├── common/                       # Shared components
├── config/                       # Application configuration
├── token-otp/                    # Main module
│   ├── adapters/                 # Adapters (controllers, repositories)
│   │   ├── controllers/          # API controllers
│   │   ├── infrastructure/       # Infrastructure implementations
│   │   ├── model/                # DTOs and mappers
│   │   ├── repositories/         # Repository implementations
│   │   └── services/             # Adapter services
│   ├── domain/                   # Domain logic
│   │   ├── model/                # Domain entities
│   │   └── ports/                # Ports (interfaces)
│   │       ├── input/            # Input ports
│   │       └── output/           # Output ports
│   └── token-otp.module.ts       # NestJS module
└── main.ts                       # Application entry point
```

## Requirements

- Node.js 18+
- Docker and Docker Compose (optional, for containerized execution)
- Redis (installed locally or via Docker)

## Environment Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/Leonardo-almd/btg-otp-challenge
   cd btg-otp-challenge
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Set up environment variables:
   - Create a `.env` file based on `env-example.txt`:
   ```bash
   cp env-example.txt .env
   ```
   - Adjust the variables as needed:
   ```
   PORT=3000
   REDIS_HOST=localhost
   REDIS_PORT=6379
   THROTTLE_TTL=5000
   THROTTLE_LIMIT=3
   ```

## Running the Application

### With Docker

1. Build and start the containers:
   ```bash
   docker compose up --build
   ```

2. The API will be available at `http://localhost:8080/api`
3. The Swagger documentation will be available at `http://localhost:8080/api/docs`

### Locally

1. Make sure Redis is running:
   ```bash
   redis-server
   ```

2. Start the application:
   ```bash
   npm run start:dev
   ```

3. The API will be available at `http://localhost:3000/api`
4. The Swagger documentation will be available at `http://localhost:3000/api/docs`

## Tests

The project includes unit and end-to-end (e2e) tests:

```bash
# Run all unit tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:cov

# Run e2e tests
npm run test:e2e
```

## Technical Decisions

### Hexagonal Architecture

We chose Hexagonal Architecture to:
- Isolate the application domain from infrastructure details
- Improve testability through dependency inversion
- Allow external components (such as the database) to be swapped with minimal impact

### Redis as the Database

We chose Redis for the following reasons:
- Performance: Extremely fast operations, essential for authentication
- Native TTL: Built-in support for key expiration, ideal for temporary tokens
- Simplicity: Easy to configure and use for the specific OTP token use case

## Security Considerations

- OTP tokens are stored in hashed form in Redis
- Rate limiting is implemented to prevent brute-force attacks
- Strict validation of all inputs to prevent injection attacks
- Structured logs with no sensitive information

## Scalability

The project was designed with scalability in mind at several levels:

- **Distributed infrastructure with Docker Compose**:
  - Clear separation between services (API, Redis, Nginx)
  - Isolated network configuration for inter-service communication
  - Ability to scale each service independently
  - Easy to add new services or replicas as needed

- **Benefits of Hexagonal Architecture for scalability**:
  - Decoupling between domain and infrastructure eases component distribution
  - Well-defined interfaces allow swapping implementations for more scalable versions
  - Clear separation of concerns makes it easier to identify bottlenecks

- **Scalable technologies**:
  - **Redis** with clustering support for high availability and data partitioning
  - **Nginx reverse proxy** for load balancing and SSL termination
  - **NestJS** with support for asynchronous, non-blocking processing

- **DevOps practices**:
  - Containerization with Docker to ease deployment in cloud environments
  - Externalized configuration via environment variables
  - Performance monitoring through structured logs

This distributed infrastructure approach, combined with the principles of hexagonal architecture, provides a solid foundation for the application's growth, allowing it to scale horizontally (adding more instances) or vertically (increasing resources) as demand requires.

---

[⬆️ Back to top / Voltar ao topo](#-api-de-tokens-otp-one-time-password)
