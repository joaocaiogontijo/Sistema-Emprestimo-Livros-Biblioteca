**# Software Design Document (SDD) - Sistema de Empréstimo de Livros**



**## 1. Visão Geral e Descrição do Problema**

**O \*\*Sistema de Empréstimo de Livros\*\* é uma API REST para controle de acervo e gerenciamento do ciclo de vida de empréstimos e devoluções para estudantes de uma biblioteca. O sistema automatiza a validação de regras de elegibilidade, impedindo novos empréstimos para alunos inadimplentes, controlando o estoque de exemplares e calculando penalidades financeiras em caso de atraso na devolução.**



**---**



**## 2. Requisitos do Sistema**



**### Requisitos Funcionais (RF)**

**\* \*\*RF01 - Cadastro de Alunos:\*\* Permitir o registro e a consulta do perfil do estudante.**

**\* \*\*RF02 - Cadastro de Livros:\*\* Permitir o registro e a alteração do status dos livros no acervo.**

**\* \*\*RF03 - Realização de Empréstimo:\*\* Permitir a vinculação de um livro disponível a um aluno elegível.**

**\* \*\*RF04 - Realização de Devolução:\*\* Permitir o registro do retorno do livro, alterando seu status e calculando multas, se aplicável.**

**\* \*\*RF05 - Consulta de Situação:\*\* Retornar os empréstimos ativos, histórico e eventuais pendências de um aluno.**



**### Requisitos Não-Funcionais (RNF)**

**\* \*\*RNF01 - Formato dos Dados:\*\* A API deve comunicar-se exclusivamente no padrão JSON.**

**\* \*\*RNF02 - Persistência:\*\* Os dados de alunos, acervo e histórico de empréstimos devem ser armazenados em banco de dados relacional ou em memória para os testes.**

**\* \*\*RNF03 - Arquitetura Integrável:\*\* A API deve ser containerizada via Docker para facilitar a execução local e a automação de testes.**



**---**



**## 3. Regras de Negócio (RN)**

**\* \*\*RN01 - Limite do Aluno:\*\* Cada aluno pode ter no máximo \*\*3 livros\*\* emprestados simultaneamente.**

**\* \*\*RN02 - Prazo Padrão:\*\* O prazo fixo de empréstimo é de \*\*7 dias corridos\*\* a contar da data de criação do registro.**

**\* \*\*RN03 - Cálculo de Multa:\*\* Em devoluções com atraso, será cobrada uma taxa fixa de \*\*R$ 2,00 por dia de atraso\*\*. O cálculo do valor final é dado por:**

  **$$V\_{\\text{multa}} = \\text{dias\\\_atraso} \\times 2,00$$**

**\* \*\*RN04 - Bloqueio por Pendência:\*\* Alunos que possuírem pelo menos 1 livro em atraso ou saldo de multa em aberto ficam automaticamente bloqueados para novos empréstimos.**

**\* \*\*RN05 - Disponibilidade do Acervo:\*\* Um livro só pode ser emprestado se seu status atual for `DISPONIVEL`.**



**---**



**## 4. Entradas e Saídas do Sistema (API Endpoints)**



**| Método | Endpoint | Descrição | Parâmetros/Body de Entrada | Resposta / Status HTTP |**

**| :--- | :--- | :--- | :--- | :--- |**

**| `POST` | `/alunos` | Cadastra um novo aluno | `{"nome": string, "ra": string}` | `201 Created` - `{"id": int, "nome": string, "ra": string}` |**

**| `POST` | `/livros` | Cadastra um novo livro | `{"titulo": string, "autor": string}` | `201 Created` - `{"id": int, "status": "DISPONIVEL"}` |**

**| `POST` | `/emprestimos` | Realiza o empréstimo | `{"aluno\_id": int, "livro\_id": int}` | `201 Created` (Sucesso) / `400 Bad Request` ou `422` (Violação de RN) |**

**| `POST` | `/devolucoes` | Registra a devolução | `{"emprestimo\_id": int, "data\_devolucao": string}` | `200 OK` - `{"dias\_atraso": int, "multa": float}` |**

**| `GET` | `/alunos/{id}` | Consulta dados do aluno | `id` (path parameter) | `200 OK` - `{"id": int, "emprestimos\_ativos": int, "bloqueado": bool}` |**



**---**



**## 5. Divisão de Componentes**

**Para garantir o desacoplamento e simplificar a escrita dos testes automatizados, a aplicação é dividida em quatro componentes principais:**



**\* \*\*Componente de Alunos (`AlunoModule`):\*\* Responsável pelo cadastro de estudantes e por informar se o aluno possui bloqueios ou se atingiu o limite de 3 livros.**

**\* \*\*Componente de Acervo (`LivroModule`):\*\* Responsável por gerenciar o status do livro (`DISPONIVEL` ou `EMPRESTADO`).**

**\* \*\*Componente de Empréstimos (`EmprestimoService`):\*\* Valida as regras de negócio cruzadas (verificar se o aluno pode pegar livros e se o livro está disponível), calcula a data prevista de devolução e registra a transação.**

**\* \*\*Componente de Cobrança e Devoluções (`DevolucaoService`):\*\* Recebe a devolução, computa os dias transcorridos, calcula o valor da multa se ultrapassar 7 dias e altera o status do livro de volta para `DISPONIVEL`.**



**---**



**## 6. Registros de Evolução e Mudanças na Especificação**

**Este registro documenta as alterações realizadas na especificação ao longo do desenvolvimento e dos testes de integração:**



**| Data | Versão | Alteração | Motivo / Origem do Feedback |**

**| :--- | :--- | :--- | :--- |**

**| 06/09/2026 | 1.0.0 | Criação da especificação inicial (SDD). | Definição do escopo do projeto. |**

**| -- | 1.0.1 | \*Reservado para futuras alterações pós-testes\* | \*A preencher caso os testes exijam mudanças de contrato\* |**

