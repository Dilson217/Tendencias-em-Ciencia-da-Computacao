# Investigação e Ética — O Take Away

* **Disciplina:** Tendências em Ciências da Computação
* **Unidade I:** Fundamentos de Engenharia de Prompt
* **Integrante:** Dilson Ribeiro de Souza

---

## 1. Dimensão Cognitiva

> **Questão:** *O que mudou na minha compreensão sobre o uso de IA depois de aprender a estruturar um prompt iterativo?*

* **Mudança de Paradigma:** Antes da Engenharia de Prompt, o uso da IA ocorria de forma direta e reativa (comandos genéricos como *"faça um código"*). A prática mostrou que a IA não é um buscador avançado, mas um **amplificador de intenção**.
* **A Importância da Anatomia:** A definição explícita de **Papel, Contexto, Tarefa, Restrições e Formato** reduz drasticamente as ambiguidades e evita que o modelo faça suposições incorretas.
* **Processo Iterativo:** O primeiro prompt raramente entrega o código ideal para produção. O valor real do desenvolvimento com IA reside no ciclo contínuo:
  
  $$\text{Prompt Inicial} \rightarrow \text{Análise de Output} \rightarrow \text{Variação/Refinamento} \rightarrow \text{Validação}$$

* **Especialização via Personas:** Alterar apenas a persona (de *Professor Didático* para *Engenheiro de Segurança* ou *Especialista em UX*) extrai diferentes camadas de análise técnica para o mesmo problema de código.

---

## 2. Dimensão Ética

> **Questão:** *Qual é a principal responsabilidade de um profissional de tecnologia que utiliza IA generativa para tomar decisões ou produzir conhecimento?*

* **Responsabilidade Inalienável (Human-in-the-Loop):** A responsabilidade final sobre qualquer código, arquitetura ou decisão de software é **exclusivamente do profissional humano**. A IA é uma ferramenta de apoio, não um tomador de decisão autônomo.
* **Mitigação de Alucinações e Vieses:** O profissional deve assumir uma postura crítica contínua, pois os modelos de linguagem podem gerar sintaxes inexistentes, métodos desatualizados ou soluções com falhas ocultas de segurança.
* **Supervisão e Validação Técnica:** Todo output gerado por IA deve passar por:
  1. **Testes Locais:** Execução do código e da suíte de testes (`pytest`).
  2. **Revisão Manual:** Verificação de boas práticas, tipagem e alinhamento às regras do negócio.
  3. **Ajuste Fino Humano:** Correção de detalhes específicos do ambiente (como tratamento de fusos horários/timezones e sanitização de dados).
