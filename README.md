[![Badge ServeRest](https://img.shields.io/badge/API-ServeRest-green)](https://github.com/ServeRest/ServeRest/)
[![Postman](https://img.shields.io/badge/Postman-Collection-orange)](https://serverest.dev/#/)
[![Newman](https://img.shields.io/badge/Newman-Reports-blue)](https://www.npmjs.com/package/newman)
[![CI/CD](https://github.com/[SEU-USUARIO]/serve-rest_postman_newman/actions/workflows/api-tests.yml/badge.svg)](https://github.com/[SEU-USUARIO]/serve-rest_postman_newman/actions/workflows/api-tests.yml)

# ServeRest API Tests

Projeto de automação de testes de API para a plataforma **ServeRest**, um simulador de e-commerce para testes. Este repositório implementa uma suíte completa de testes utilizando **Postman**, **Newman** e **GitHub Actions** para CI/CD.

| Recurso | Link |
|---------|------|
| Documentação da API | https://serverest.dev/#/ |
| Base URL | `https://serverest.dev` |

---

## Sumário

- [Visão Geral](#visão-geral)
- [Métricas da Collection](#métricas-da-collection)
- [Estrutura e Validações](#estrutura-e-validações)
- [Linha de Raciocínio](#linha-de-raciocínio)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Execução com Newman](#execução-com-newman)
- [Pipeline CI/CD](#pipeline-cicd)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Relatórios](#relatórios)

---

## Visão Geral

Este projeto foi desenvolvido com o objetivo de validar os principais endpoints da API ServeRest, cobrindo:

- **Testes funcionais** de CRUD (Create, Read, Update, Delete)
- **Cenários positivos** (happy path)
- **Cenários negativos** (validação de erros e edge cases)
- **Fluxo E2E** (jornada completa do usuário no e-commerce)
- **Limpeza de dados** (cleanup após execução)

---

## Métricas da Collection

| Métrica | Valor |
|---------|-------|
| **Total de Requisições** | 30 |
| **Cenários Positivos** | 18 |
| **Cenários Negativos** | 7 |
| **Fluxos E2E** | 5 |
| **Endpoints Testados** | 4 (/usuarios, /login, /produtos, /carrinhos) |
| **Métodos HTTP** | GET, POST, PUT, DELETE |

### Distribuição por Módulo

| Módulo | Requisições | Positivos | Negativos |
|--------|-------------|-----------|-----------|
| User Admin | 7 | 4 | 3 |
| Auth | 1 | 1 | 0 |
| Product | 9 | 5 | 4 |
| Cart | 3 | 3 | 0 |
| Flow (E2E) | 5 | 5 | 0 |
| Data Cleanup | 5 | 5 | 0 |

---

## Estrutura e Validações

### 1. User Admin

Testes relacionados ao gerenciamento de usuários administradores.

#### Cenários Positivos

| Request | Método | Endpoint | Validações |
|---------|--------|----------|------------|
| Create User [201] | POST | `/usuarios` | Status 201, mensagem "Cadastro realizado com sucesso", presença do `_id`, tempo de resposta < 2000ms |
| User by ID [200] | GET | `/usuarios/{id}` | Status 200, dados do usuário (nome, email, password, administrador), ID correspondente |
| Update User [200] | PUT | `/usuarios/{id}` | Status 200, mensagem "Registro alterado com sucesso" |
| Get User Admin [200] | GET | `/usuarios/{id}` | Status 200, campo `administrador` igual a "true", ID correspondente |

#### Cenários Negativos

| Request | Método | Endpoint | Validações |
|---------|--------|----------|------------|
| Create User (Duplicate Email) [400] | POST | `/usuarios` | Status 400, mensagem "Este email já está sendo usado" |
| Update User (Invalid ID) [400] | PUT | `/usuarios/{id_invalido}` | Status 400, mensagem "Este email já está sendo usado" |
| User by (Invalid ID) [400] | GET | `/usuarios/{id_invalido}` | Status 400, mensagem "id deve ter exatamente 16 caracteres alfanuméricos" |

---

### 2. Auth (Autenticação)

| Request | Método | Endpoint | Validações |
|---------|--------|----------|------------|
| Login [200] | POST | `/login` | Status 200, mensagem "Login realizado com sucesso", presença do token Bearer |

**Variável armazenada:** `token` (utilizado nas requisições autenticadas)

---

### 3. Product

Testes relacionados ao gerenciamento de produtos.

#### Cenários Positivos

| Request | Método | Endpoint | Validações |
|---------|--------|----------|------------|
| Create Product 1 [201] | POST | `/produtos` | Status 201, mensagem "Cadastro realizado com sucesso", presença do `_id`, tempo < 2000ms |
| Get Product by ID [200] | GET | `/produtos/{id}` | Status 200, campos (preco, nome, descricao, quantidade, _id) |
| Update Product [200] | PUT | `/produtos/{id}` | Status 200, mensagem "Registro alterado com sucesso", tempo < 2000ms |
| Get Update Product [200] | GET | `/produtos/{id}` | Status 200, dados atualizados, ID correspondente |
| Create Product 2 [201] | POST | `/produtos` | Status 201, cadastro realizado, tempo < 2000ms |

#### Cenários Negativos

| Request | Método | Endpoint | Validações |
|---------|--------|----------|------------|
| Create Product (Invalid Price) [400] | POST | `/produtos` | Status 400, mensagem "preco deve ser um número positivo", "quantidade deve ser maior ou igual a 0" |
| Create Product (Duplicate Name) [400] | POST | `/produtos` | Status 400, mensagem "Já existe produto com esse nome" |
| Update Product (Invalid ID) [400] | PUT | `/produtos/{id_invalido}` | Status 400, mensagem "id deve ter exatamente 16 caracteres alfanuméricos" |
| Get Product by (Invalid ID) [400] | GET | `/produtos/{id_invalido}` | Status 400, mensagem "id deve ter exatamente 16 caracteres alfanuméricos" |

---

### 4. Cart (Carrinho)

#### Cenários Positivos

| Request | Método | Endpoint | Validações |
|---------|--------|----------|------------|
| Create Cart [201] | POST | `/carrinhos` | Status 201, mensagem "Cadastro realizado com sucesso", presença do `_id` |
| Get Product [200] | GET | `/carrinhos` | Status 200 |
| Get Cart by ID [200] | GET | `/carrinhos/{id}` | Status 200 |

---

### 5. Flow (E2E)

Fluxo completo simulando a jornada do e-commerce:

```
Delete Cart → Create User → Create Product 1 → Create Product 2 → Create Cart
```

| Etapa | Request | Validação |
|-------|---------|-----------|
| 1 | Delete Cart [200] Flow | Limpa carrinho existente |
| 2 | Create User [201] Flow | Cria novo usuário |
| 3 | Create Product 1 [201] Flow | Cadastra primeiro produto |
| 4 | Create Product 2 [201] Flow | Cadastra segundo produto |
| 5 | Create Cart [201] Flow | Adiciona produtos ao carrinho |

---

### 6. Data Cleanup

Limpeza de dados gerados durante a execução dos testes:

| Request | Ação |
|---------|------|
| Delete Cart [200] | Conclui compra e remove carrinho |
| Cancel Cart [200] | Cancela carrinho (caso exista) |
| Delete Product [200] | Remove produto 1 |
| Delete Product 2 [200] | Remove produto 2 |
| Delete User [200] | Remove usuário criado |

---

## Linha de Raciocínio

### Estratégia de Testes

A collection foi estruturada seguindo uma abordagem **bottom-up** e **data-driven**:

1. **Isolamento por domínio**: Cada módulo (User, Auth, Product, Cart) é testado independentemente
2. **Progressão lógica**: Os testes positivos são executados primeiro, seguidos dos negativos
3. **Encadeamento de dados**: Variáveis de ambiente são utilizadas para passar dados entre requisições
4. **Cleanup automatizado**: Ao final, todos os dados criados são removidos

### Fluxo de Variáveis

```
Create User → userId
↓
Update User → email
↓
Login → token
↓
Create Product → userId_product, userId_product2
↓
Create Cart → userId_cart
```

### Tipos de Validações Implementadas

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| **Status Code** | Verifica código HTTP retornado | `pm.response.to.have.status(201)` |
| **Body Content** | Valida conteúdo da resposta | `pm.expect(pm.response.text()).to.include("mensagem")` |
| **Response Time** | Verifica performance | `pm.expect(pm.response.responseTime).to.be.below(2000)` |
| **JSON Schema** | Valida estrutura do JSON | `pm.expect(pm.response.json().campo).to.be.eql(valor)` |
| **Data Persistence** | Verifica dados salvos | Comparação de IDs armazenados |

---

## Pré-requisitos

- [Node.js](https://nodejs.org/) versão 18 ou superior
- [npm](https://www.npmjs.com/) (incluído com Node.js)
- [Git](https://git-scm.com/)

---

## Instalação

```bash
# Clone o repositório
git clone https://github.com/[SEU-USUARIO]/serve-rest_postman_newman.git

# Acesse o diretório
cd serve-rest_postman_newman

# Instale as dependências
npm install
```

---

## Execução com Newman

### Comandos Disponíveis

| Comando | Descrição |
|---------|-----------|
| `npm test` | Executa todos os testes (saída no console) |
| `npm run test:html` | Executa e gera relatório HTML |
| `npm run test:ci` | Executa com todos os reporters (HTML + JUnit) |
| `npm run test:smoke` | Executa apenas testes de autenticação |
| `npm run test:users` | Executa testes do módulo de usuários |
| `npm run test:products` | Executa testes do módulo de produtos |
| `npm run test:e2e` | Executa testes de fluxo E2E |

### Execução Básica

```bash
# Executar todos os testes
npm test

# Executar com relatório HTML
npm run test:html
# Relatório gerado em: ./reports/newman-report.html
```

### Execução Direta com Newman CLI

**Linux/Mac (Bash):**
```bash
# Executar collection completa
npx newman run ServeRestAPITests.postman_collection.json \
-e env.postman_environment.json

# Executar pasta específica
npx newman run ServeRestAPITests.postman_collection.json \
-e env.postman_environment.json \
--folder "User Admin"

# Executar com múltiplos reporters
npx newman run ServeRestAPITests.postman_collection.json \
-e env.postman_environment.json \
-r cli,htmlextra,junit \
--reporter-htmlextra-export ./reports/report.html \
--reporter-junit-export ./reports/report.xml
```

**Windows (PowerShell):**

```powershell
# Executar collection completa (com backtick)
npx newman run ServeRestAPITests.postman_collection.json `
-e env.postman_environment.json

# Ou em uma única linha
npx newman run ServeRestAPITests.postman_collection.json -e env.postman_environment.json

# Executar pasta específica (com backtick)
npx newman run ServeRestAPITests.postman_collection.json `
-e env.postman_environment.json `
--folder "User Admin"

# Ou em uma única linha
npx newman run ServeRestAPITests.postman_collection.json -e env.postman_environment.json --folder "User Admin"

# Executar com múltiplos reporters (com backtick)
npx newman run ServeRestAPITests.postman_collection.json `
-e env.postman_environment.json `
-r cli,htmlextra,junit `
--reporter-htmlextra-export ./reports/report.html `
--reporter-junit-export ./reports/report.xml

# Ou em uma única linha
npx newman run ServeRestAPITests.postman_collection.json -e env.postman_environment.json -r cli,htmlextra,junit --reporter-htmlextra-export ./reports/report.html --reporter-junit-export ./reports/report.xml
```

### Parâmetros Úteis do Newman

| Parâmetro | Descrição |
|-----------|-----------|
| `-e` | Arquivo de environment |
| `--folder` | Executa apenas uma pasta específica |
| `-r` | Define os reporters (cli, htmlextra, junit) |
| `--delay-request` | Delay entre requisições (ms) |
| `-n` | Número de iterações |
| `--bail` | Para execução no primeiro erro |
| `--color on` | Habilita cores no output |

---

## Pipeline CI/CD

A pipeline enxuta está definida em `.github/workflows/api-tests.yml`. Ela roda automaticamente em todos os **pushes** e **pull requests** do repositório e gera o relatório HTML do Newman como artefato.

### Passos executados

1. Checkout do código na máquina de execução
2. Setup do Node.js 20 (através da ação oficial `actions/setup-node@v4`)
3. Instalação das dependências com `npm install`
4. Execução da collection `ServeRestAPITests.postman_collection.json` via Newman com o reporter `htmlextra`
5. Exportação do relatório para `reports/newman-report.html`
6. Upload do artefato `newman-html-report` (HTML) a cada execução

### Observações

- O workflow é simples e não possui schedules nem jobs paralelos.
- O único artefato publicado é o relatório HTML (`newman-html-report`) com retenção de 30 dias.
- Para executar manualmente, basta ir em **Actions → Teste API - Postman CI/CD (relatório Newman)** e clicar em **Run workflow**.

---

## Estrutura do Projeto

```
serve-rest_postman_newman/
├── .github/
│ └── workflows/
│ └── api-tests.yml # Pipeline CI/CD
├── reports/ # Relatórios gerados (gitignore)
├── ServeRestAPITests.postman_collection.json # Collection Postman
├── env.postman_environment.json # Variáveis de ambiente
├── package.json # Dependências e scripts
├── .gitignore # Arquivos ignorados
└── README.md # Documentação
```

### Dependências do Projeto

```json
{
"devDependencies": {
"newman": "^6.2.1",
"newman-reporter-htmlextra": "^1.23.1",
"newman-reporter-junit": "^2.0.0"
}
}
```

---

## Relatórios

### HTML Extra Reporter

O relatório HTML inclui:

- Resumo geral (total, passed, failed)
- Tempo total de execução
- Gráficos de distribuição de resultados
- Detalhes de cada requisição
- Request/Response completos
- Assertions executadas
- Filtros e busca
- Dados de environment e globals

### JUnit Reporter

Formato XML compatível com:

- Jenkins
- Azure DevOps
- GitHub Actions Test Reporter
- Outras ferramentas de CI

### Acessando os Relatórios

**Localmente:**
```bash
npm run test:html
# Abrir: ./reports/newman-report.html
```

**No GitHub Actions:**
1. Acesse a execução do workflow
2. Vá até a seção **Artifacts**
3. Baixe `newman-html-report` ou `newman-junit-report`

---

## Variáveis de Ambiente

| Variável | Descrição | Valor Padrão |
|----------|-----------|--------------|
| `baseUrl` | URL base da API | https://serverest.dev |
| `password` | Senha padrão para testes | 123456 |
| `userId` | ID do usuário (gerado dinamicamente) | - |
| `email` | Email do usuário (gerado dinamicamente) | - |
| `token` | Token JWT (gerado no login) | - |
| `userId_product` | ID do produto 1 | - |
| `userId_product2` | ID do produto 2 | - |
| `userId_cart` | ID do carrinho | - |
| `nome` | Nome do produto (para validações) | - |

---

## Links Úteis

- [ServeRest API](https://serverest.dev/)
- [Newman Documentation](https://learning.postman.com/docs/collections/using-newman-cli/command-line-integration-with-newman/)
- [Newman HTML Extra Reporter](https://github.com/DannyDainton/newman-reporter-htmlextra)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Postman Learning Center](https://learning.postman.com/)
