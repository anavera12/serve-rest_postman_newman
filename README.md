[![Badge ServeRest](https://img.shields.io/badge/API-ServeRest-green)](https://github.com/ServeRest/ServeRest/)
[![Postman](https://img.shields.io/badge/Postman-Collection-orange)](https://serverest.dev/#/)
[![Newman](https://img.shields.io/badge/Newman-Reports-blue)](https://www.npmjs.com/package/newman)

# ServeRest API Tests (Postman + Newman)

Este repositório contém uma **collection Postman** com testes automatizados para a API **ServeRest** (e-commerce) disponível em:  
- Documentação / Swagger: https://serverest.dev/#/  
- Base URL: `https://serverest.dev`

O objetivo é demonstrar **organização de testes**, **validações funcionais de API**, **cenários positivos/negativos** e **execução automatizada com relatórios** via Newman.

---

## ✅ O que foi automatizado

### Estrutura da Collection: `ServeRestAPITests`
A collection está organizada por domínio, separando cenários **Positive**, **Negative**, **Flow (E2E)** e **Data Cleanup**:

- **User Admin**
  - **Positive**
    - Create User `[201]` (salva `userId`)
    - User by ID `[200]` (valida dados e ID)
    - Update User `[200]` (atualiza dados e salva `email`)
    - Get User Admin `[200]` (valida `administrador=true`)
  - **Negative**
    - Create User (Duplicate Email) `[400]`
    - Update User (Invalid ID) `[400]`
    - User by (Invalid ID) `[400]`

- **Auth**
  - Login `[200]` (salva `token`)

- **Product**
  - **Positive**
    - Create Product 1 `[201]` (salva `userId_product`)
    - Get Product by ID `[200]`
    - Update Product `[200]`
    - Get Update Product `[200]`
    - Create Product 2 `[201]` (salva `userId_product2`)
  - **Negative**
    - Create Product (Invalid Price) `[400]`
    - Create Product (Duplicate Name) `[400]`
    - Update Product (Invalid ID) `[400]`
    - Get Product by (Invalid ID) `[400]`

- **Cart**
  - **Positive**
    - Create Cart `[201]` (salva `userId_cart`)
    - Get Cart `[200]` / Get Cart by ID `[200]`

- **Flow (E2E)**
  - Fluxo principal de negócio para simular jornada do e-commerce:
    - Create User → Create Product(s) → Create Cart → Conclude Purchase

- **Data Cleanup**
  - Remove dados gerados durante a execução:
    - Concluir/Cancelar carrinho
    - Deletar produtos criados
    - Deletar usuário criado

---

## Como instalar o ambiente
- Primeiro: Instalar o node em seu computador [Baixe o Node](https://nodejs.org/en/download/current)
- Segundo: Instalar o Newman de forma global [Baixe as dependências do newman](https://www.npmjs.com/package/newman)
