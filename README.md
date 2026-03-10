# 🐝 DESAFIO-QA-BEEDOO-2026

Repositório de entrega do desafio técnico de QA da Beedoo.

---

## 🔗 Links Importantes

| Recurso | Link |
|---|---|
| 🌐 Aplicação testada | https://creative-sherbet-a51eac.netlify.app/ |
| 📊 Planilha de Casos de Teste | https://docs.google.com/spreadsheets/d/1M-4mDUo6IUiWPouF8wJCqVBtnZsUEzJI4UVzCAFM-hc/edit?gid=544216340#gid=544216340 |
| 🗂️ Evidências | https://drive.google.com/drive/folders/1rDclbcYpZfH_qYHSzMjhOW1pSKJ1Adp5?usp=drive_link |

---

## 1. Análise da Aplicação

### Objetivo da Aplicação

A aplicação **Beedoo QA Tests** é uma plataforma de gerenciamento de cursos educacionais. Seu propósito é permitir que administradores **criem, visualizem e excluam cursos**, formando um catálogo de treinamentos — compatível com a proposta da Beedoo de aprendizado corporativo para equipes de frontline.

### Principais Fluxos Disponíveis

| Fluxo | Descrição |
|---|---|
| **Cadastro de Curso** | Formulário com os campos: Nome do curso, Descrição, Instrutor, URL da imagem de capa, Data de início, Data de fim, Número de vagas, Tipo (Presencial/Online/Híbrido), Endereço (para Presencial) e Link (para Online) |
| **Listagem de Cursos** | Exibição dos cursos cadastrados em cards com: tipo, nome, instrutor, data de início, data de fim, número de vagas e botão "Excluir Curso" |
| **Exclusão de Curso** | Botão "Excluir Curso" disponível em cada card da listagem |

### Pontos Mais Críticos para Teste

Com base na exploração da aplicação, os pontos que considero mais críticos são:

**1. Validações do formulário de cadastro** — Nenhum campo apresenta validação funcional. Campos obrigatórios não são verificados, o campo de vagas aceita letras e as datas não são validadas entre si. É o ponto de maior risco da aplicação.

**2. Lógica condicional de campos por tipo de curso** — O campo "Link" deveria aparecer ao selecionar "Online" e o campo "Endereço" ao selecionar "Presencial". Essa lógica está com defeito, o que compromete o cadastro de cursos online.

**3. Funcionalidade de exclusão** — Clicar em "Excluir Curso" não remove o registro. Operações destrutivas com falha silenciosa são críticas pois o usuário não tem feedback do que ocorreu.

**4. Consistência de datas** — A data de fim pode ser anterior à de início, gerando registros logicamente inválidos visíveis na própria listagem.

---

## 2. Cenários e Casos de Teste

Os casos de teste foram escritos em formato **Gherkin (DADO / QUANDO / ENTÃO)** e estão disponíveis na planilha abaixo.

📊 **[Acessar planilha de casos de teste](https://docs.google.com/spreadsheets/d/1M-4mDUo6IUiWPouF8wJCqVBtnZsUEzJI4UVzCAFM-hc/edit?gid=544216340#gid=544216340)**

### Resumo de Cobertura

| Funcionalidade | Qtd. de Casos | Positivos | Negativos |
|---|---|---|---|
| Cadastro de Curso | 6 | 2 | 4 |
| Listagem de Cursos | 1 | 1 | 0 |
| Exclusão de Curso | 1 | 0 | 1 |
| **Total** | **8** | **3** | **5** |

---

## 3. Bugs Encontrados

Foram identificados **8 bugs** durante a exploração e execução dos testes:

### BUG-001 — Curso não é excluído ao clicar em 'Excluir Curso'
- **Severidade:** 🔴 Alta
- **Passos:** Acessar listagem → Clicar em 'Excluir Curso' de qualquer card → Confirmar
- **Resultado Atual:** O curso permanece na listagem. Nenhuma mensagem de erro ou sucesso é exibida.
- **Resultado Esperado:** O curso deve ser removido imediatamente da listagem com mensagem de confirmação.
- **Impacto:** Impede completamente a gestão do catálogo. Cursos errados ou desatualizados não podem ser removidos.

---

### BUG-002 — Sistema permite salvar curso com todos os campos em branco
- **Severidade:** 🔴 Alta
- **Passos:** Acessar cadastro → Não preencher nenhum campo → Clicar em 'Cadastrar'
- **Resultado Atual:** Um card em branco é criado na listagem, sem nome, datas ou vagas.
- **Resultado Esperado:** Sistema deve exibir erros de validação e impedir o salvamento.
- **Impacto:** Gera registros inválidos na base, comprometendo a integridade do catálogo.

---

### BUG-003 — Data de fim anterior à data de início é aceita
- **Severidade:** 🔴 Alta
- **Passos:** Preencher Data Início: 10/03/2026 → Data Fim: 01/01/2026 → Salvar
- **Resultado Atual:** Curso salvo com datas inconsistentes. Na listagem, o card exibe FIM anterior ao INÍCIO sem nenhum aviso.
- **Resultado Esperado:** Sistema deve bloquear com mensagem: *"A data de fim deve ser posterior à data de início"*.
- **Impacto:** Dados logicamente inválidos podem causar falhas em cálculos de duração, agendamentos e relatórios.

---

### BUG-004 — Campo 'Número de Vagas' aceita caracteres alfabéticos
- **Severidade:** 🟡 Média
- **Passos:** No campo 'Número de Vagas', digitar 'abc' → Preencher demais campos → Salvar
- **Resultado Atual:** O sistema aceita letras no campo numérico e salva o curso com dado inválido.
- **Resultado Esperado:** Campo deve aceitar apenas números inteiros positivos, com mensagem de erro ao inserir letras.
- **Impacto:** Compromete o controle de inscrições e relatórios de ocupação de turmas.

---

### BUG-005 — Link de inscrição do curso Online não é exibido na listagem
- **Severidade:** 🔴 Alta
- **Passos:** Cadastrar curso Online com o campo 'Link de inscrição' preenchido → Acessar a listagem → Observar o card
- **Resultado Atual:** O link de inscrição informado no cadastro não aparece no card do curso Online na listagem.
- **Resultado Esperado:** O card de cursos Online deve exibir o link de inscrição para que o usuário possa acessá-lo diretamente pela listagem.
- **Impacto:** O campo 'Link de inscrição' torna-se inútil na prática, pois o dado é cadastrado mas nunca exibido ao usuário.

---

### BUG-006 — Campo 'Número de Vagas' aceita valores absurdamente altos
- **Severidade:** 🟡 Média
- **Passos:** Digitar '1212121212' no campo Número de Vagas → Preencher demais campos → Salvar
- **Resultado Atual:** O sistema aceita e salva o curso com 1212121212 vagas sem nenhuma validação.
- **Resultado Esperado:** Sistema deve validar um limite máximo razoável e exibir mensagem de erro ao ultrapassá-lo.
- **Impacto:** Dados irreais comprometem relatórios de ocupação e cálculos de capacidade de turmas.

---

### BUG-007 — Campo 'Data de Início' aceita ano inválido (ex: 0001)
- **Severidade:** 🟡 Média
- **Passos:** Preencher Data de Início com '0001-01-02' → Preencher demais campos → Salvar
- **Resultado Atual:** O sistema aceita e salva a data com ano 0001 sem nenhuma validação de intervalo.
- **Resultado Esperado:** Sistema deve validar que o ano está dentro de um intervalo razoável (ex: 2000–2100) e exibir erro para datas inválidas.
- **Impacto:** Datas com anos inválidos geram inconsistências em relatórios, ordenações e lógicas que dependem das datas do curso.

---

### BUG-008 — Endereço do curso Presencial não é exibido na listagem
- **Severidade:** 🟡 Média
- **Passos:** Cadastrar curso Presencial com endereço preenchido → Acessar a listagem → Observar o card
- **Resultado Atual:** O endereço não aparece no card do curso, mesmo sendo um curso Presencial.
- **Resultado Esperado:** O card deve exibir o endereço cadastrado para que o usuário saiba onde o curso será realizado.
- **Impacto:** Usuários não conseguem visualizar o local de realização do curso diretamente na listagem, prejudicando a utilidade da plataforma.

---

## 4. Evidências

As evidências de execução (prints e gravações de tela) estão disponíveis no link abaixo:

🗂️ **[Acessar pasta de evidências no Google Drive](https://drive.google.com/drive/folders/1rDclbcYpZfH_qYHSzMjhOW1pSKJ1Adp5?usp=drive_link)**

---

## 5. Uso de Inteligência Artificial

Durante este desafio, utilizei IA (Claude da Anthropic) como apoio para estruturar a documentação, formatar os casos de teste em Gherkin e revisar a cobertura dos cenários. Em todos os casos, as sugestões foram revisadas criticamente — os bugs foram identificados manualmente durante a execução na aplicação, e cenários que não correspondiam ao comportamento real observado foram corrigidos.

---

*Entrega realizada por: **William de Matos Pereira** | Março de 2026*
