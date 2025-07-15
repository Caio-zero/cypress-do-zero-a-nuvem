# Documentação de Teste Automatizado com Cypress

## Objetivo

Testar automaticamente o envio do formulário da aplicação "Central de Atendimento ao Cliente TAT", utilizando Cypress. Os testes garantem que os campos obrigatórios funcionam corretamente e que mensagens de sucesso ou erro sejam exibidas conforme esperado.

---

## ⚙️ Configuração do Ambiente

### 1. Clonar o Projeto via SSH

```bash
cd "C:\Users\CaioNunes\OneDrive - Zero-Defect\Documentos\GitHub"
git clone git@github.com:CaioNunes/cypress-do-zero-a-nuvem.git
cd cypress-do-zero-a-nuvem
```

### 2. Instalar Dependências e o Cypress

```bash
npm install
npm install cypress@13.12.0 -D
```

### 3. Iniciar o Cypress App (modo interativo)

```bash
npm run cy:open
```

**Scripts recomendados no `package.json`:**

```json
"scripts": {
  "cy:open": "cypress open",
  "cy:open:mobile": "cypress open --config viewportWidth=375,viewportHeight=667",
  "test": "cypress run"
}
```

---

## 📁 Estrutura Esperada do Projeto

```
cypress-do-zero-a-nuvem/
  src/
    index.html
    privacy.html
    script.js
    style.css
  cypress/
    e2e/
      CAC-TAT.cy.js
    support/
      commands.js
      e2e.js
  cypress.config.js
  package.json
```

---

## 🔧 Configuração do `cypress.config.js`

```js
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  viewportHeight: 880,
  viewportWidth: 1280,
  e2e: {}
})
```

---

## 📅 Casos de Teste

### ✅ Verifica o título da aplicação

```js
describe('Central de Atendimento ao Cliente TAT', () => {
  it('verifica o título da aplicação', () => {
    cy.visit('./src/index.html')
    cy.title().should('be.equal', 'Central de Atendimento ao Cliente TAT')
  })
})
```

### ⛔️ Exibe mensagem de erro ao submeter sem preencher campos

```js
it('exibe mensagem de erro ao submeter o formulário sem preencher os campos obrigatórios', () => {
  cy.visit('./src/index.html')
  cy.get('button[type="submit"]').click()
  cy.get('.error').should('be.visible')
})
```

### 📅 Envia o formulário com sucesso usando comando customizado

**Comando customizado em `cypress/support/commands.js`:**

```js
Cypress.Commands.add('fillMandatoryFieldsAndSubmit', data => {
  cy.get('#firstName').type(data.firstName)
  cy.get('#lastName').type(data.lastName)
  cy.get('#email').type(data.email)
  cy.get('#open-text-area').type(data.text)
  cy.contains('button', 'Enviar').click()
})
```

**Teste usando o comando:**

```js
it('envia o formulário com sucesso usando um comando customizado', () => {
  const data = {
    firstName: 'Caio',
    lastName: 'Nunes',
    email: 'caio.test@testando.com',
    text: 'Mensagem de teste.'
  }
  cy.fillMandatoryFieldsAndSubmit(data)
  cy.get('.success', { timeout: 10000 }).should('be.visible')
})
```

---

## ✨ Extras

### ✉️ Testa o radio "Feedback"

```js
it('marca o tipo de atendimento "Feedback"', () => {
  cy.get('input[type="radio"][value="feedback"]').check().should('be.checked')
})
```

### 🚫 Corrigindo erros comuns

* Se receber `Cannot read properties of undefined (reading 'get')` → provavelmente tem um `cy.` incompleto.
* Se receber `expected <.success> to be visible` → o campo obrigatório pode estar vazio ou com dados inválidos.

---

## 🌟 Observações Finais

* Cypress não encontra elementos ocultos (com `display: none`).
* Sempre use `cy.visit()` antes dos testes, exceto se estiver em um `beforeEach()`.
* Use `cy.get(selector, { timeout: ms })` se os elementos demorarem a aparecer.

---

**Autor:** Caio Nunes
**E-mail:** [caio.nunes@zero-defect.com.br](mailto:caio.nunes@zero-defect.com.br)
**Projeto:** cypress-do-zero-a-nuvem
