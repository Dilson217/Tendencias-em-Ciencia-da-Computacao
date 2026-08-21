# Atividade Prática — Suíte Iterativa de Prompts para Engenharia de Software

> **Disciplina:** Tendências em Ciências da Computação  
> **Unidade I:** Fundamentos de Engenharia de Prompt  
> **Integrante:** Dilson Ribeiro de Souza  
> **Entrega:** Repositório GitHub  

---

## 1. Definição do Tema e Problema Real

* **Domínio:** Tecnologia e Vida Acadêmica Universitária.
* **Problema Real:** Alunos de Ciência da Computação frequentemente desenvolvem APIs REST e módulos de código para projetos acadêmicos (como Sistemas de Gestão de TCC ou Agendamento de Monitorias), mas enfrentam dificuldades para realizar revisões de código (*Code Review*) completas que cubram tanto a **qualidade sintática/segurança do código** quanto aspectos de **usabilidade (Heurísticas de Nielsen)** e **cobertura de testes unitários**.
* **Objetivo com IA Generativa:** Desenvolver e testar uma suíte iterativa de prompts para atuar como um *Tech Lead* e *Especialista em UX/UI*, revisando um snippet de código Python/FastAPI e entregando diagnósticos estruturados, refatoração otimizada e casos de teste.

---

## 2. Artefatos Iniciais — Prompt Mestre e Anatomia

### 2.1 Anatomia Profissional do Prompt Mestre Inicial

| Componente | Conteúdo Aplicado no Prompt |
| :--- | :--- |
| **Papel (Role)** | Engenheiro de Software Senior e Revisor de Código Python. |
| **Contexto** | Um estudante desenvolveu uma função em Python/FastAPI para agendamento de orientações de TCC, mas o código está sem validações adequadas e sem tratamento de erros. |
| **Objetivo** | Revisar o código, apontar falhas de boas práticas e fornecer a versão refatorada. |
| **Tarefa** | Analisar o código fornecido, destacar 3 pontos fracos e reescrever a função. |
| **Restrições** | Utilizar Python 3.10+, manter comentários explicativos e não usar bibliotecas externas não padrão além do FastAPI. |
| **Formato de Saída** | Resposta em Markdown contendo: Lista de Problemas, Código Refatorado e Explicação Breve. |
| **Critérios de Qualidade** | O código deve seguir a convenção PEP-8 e conter validação explicita de datas. |

---

### 2.2 Prompt Mestre Inicial (Executado)

```text
Você é um Engenheiro de Software Senior e Revisor de Código Python.

Contexto: Um estudante do primeiro ano de Computação desenvolveu o código abaixo para um sistema universitário de agendamento de orientações de TCC. O sistema vem apresentando bugs no envio de horários inválidos.

Código Original:
def agendar(aluno, professor, data_hora):
    if aluno != "" and professor != "":
        db.save(aluno, professor, data_hora)
        return "Agendado com sucesso!"
    else:
        return "Erro"

Objetivo:
Analise o código acima, identifique problemas e forneça a versão corrigida.

Formato de Saída esperado:
1. Lista com os 3 principais problemas encontrados.
2. Código refatorado em Python.
3. Explicação resumida das melhorias.

2.3 Resposta Obtida (LLM Output - Inicial)
1. Problemas Encontrados:

Falta de validação de tipo e formato para o campo data_hora.

Tratamento de erro muito genérico (retorna apenas a string "Erro" em vez de lançar exceção ou detalhar o problema).

Ausência de boas práticas PEP-8 e tipagem de dados (Type Hints).

2. Código Refatorado:

from datetime import datetime

def agendar(aluno: str, professor: str, data_hora: str) -> dict:
    if not aluno.strip() or not professor.strip():
        return {"sucesso": False, "mensagem": "Nome do aluno e professor são obrigatórios."}
    
    try:
        data_obj = datetime.strptime(data_hora, "%Y-%m-%d %H:%M")
        if data_obj < datetime.now():
            return {"sucesso": False, "mensagem": "A data deve ser no futuro."}
    except ValueError:
        return {"sucesso": False, "mensagem": "Formato de data inválido. Use YYYY-MM-DD HH:MM."}
    
    db.save(aluno, professor, data_obj)
    return {"sucesso": True, "mensagem": "Agendamento realizado com sucesso!"}

3. Explicação das Melhorias:
Adicionada validação com strip() para evitar nomes em branco, conversão e validação de data/hora no passado, e retorno estruturado em dicionário.

2.4 Técnicas Utilizadas na Primeira Iteração
Role Prompting: Definição da persona "Engenheiro de Software Senior".

Contextualização: Inserção do cenário real (sistema acadêmico de TCC).

Restrição de Formato: Divisão clara do output em 3 seções numeradas.

3. Desenvolvimento de 3 Variações do Prompt
Para refinar o resultado obtido no prompt inicial, foram desenvolvidas 3 variações focadas em objetivos distintos do desenvolvimento de software.

                  ┌──────────────────────────────────────────────┐
                  │             PROMPT INICIAL                   │
                  │   Revisão básica de código Python/FastAPI    │
                  └──────────────────────┬───────────────────────┘
                                         │
         ┌───────────────────────────────┼───────────────────────────────┐
         │                               │                               │
         ▼                               ▼                               ▼
┌─────────────────────────┐   ┌─────────────────────────┐   ┌─────────────────────────┐
│       VARIAÇÃO 1        │   │       VARIAÇÃO 2        │   │       VARIAÇÃO 3        │
│     Foco Didático       │   │    Foco em Testes e     │   │  Foco em Usabilidade e  │
│    e Documentação       │   │      Performance        │   │ Heurísticas de Nielsen  │
└─────────────────────────┘   └─────────────────────────┘   └─────────────────────────┘

3.1 Variação 1 — Foco Didático e Explicação Passo a Passo
Objetivo da Variação: Explicar a refatoração para um estudante iniciante usando analogias e detalhamento de conceitos.

Prompt Enviado:

Você é um Professor de Programação com foco didático para iniciantes.

A partir da solução de código refatorada anteriormente, crie um guia explicativo passo a passo orientado a um estudante do 1º semestre de Ciência da Computação.

Requisitos:
- Utilize uma analogia do cotidiano para explicar o conceito de tratamento de exceções (try/except).
- Explique o motivo pelo qual usar dicionários de retorno é melhor do que retornar apenas strings soltas.
- Mantenha o tom encorajador e profissional.

Resposta Obtida (LLM Output):
Guia de Aprendizado: Melhorando seu Código

1. A Analogia do Try/Except (O Balcão de Atendimento):
Pense no try/except como um atendente de biblioteca. Você pede um livro (try). Se o livro existir, ele te entrega. Mas se o livro não existir ou a data for inválida, em vez de o atendente congelar ou sumir, ele já tem uma instrução preparada para te avisar educadamente o erro (except).

2. Por que usar Dicionários em vez de Strings soltas?
Quando você retorna só "Erro", o programa do cliente não sabe o porquê do erro. Retornar {"sucesso": False, "mensagem": "..."} permite que a interface do usuário leia o status booleano para decidir a cor da mensagem (vermelho ou verde) e exiba o texto exato para o aluno.

3.2 Variação 2 — Foco em Performance, Segurança e Casos de Teste
Objetivo da Variação: Elevar o nível do código para ambiente de produção, incluindo testes unitários (pytest) e segurança contra Injeção/Inputs Maliciosos.

Você é um Arquiteto de Software e Engenheiro de Segurança de Dados.

Refatore a função de agendamento para torná-la pronta para produção (Production-Ready).

Prompt Enviado:
Diretrizes exigidas:
1. Aplique validações contra entradas maliciosas ou caracteres especiais nos nomes.
2. Adicione tipagem estática rigorosa usando Pydantic.
3. Escreva 3 casos de teste automatizados utilizando a biblioteca PyTest (1 caso de sucesso, 1 caso de data no passado e 1 caso de nome em branco).

Resposta Obtida (LLM Output):
from pydantic import BaseModel, Field, validator
from datetime import datetime

class AgendamentoSchema(BaseModel):
    aluno: str = Field(..., min_length=3, max_length=100)
    professor: str = Field(..., min_length=3, max_length=100)
    data_hora: datetime

    @validator('data_hora')
    def validar_data_futura(cls, v):
        if v < datetime.now():
            raise ValueError('A data de agendamento deve ser no futuro.')
        return v

# --- CASOS DE TESTE (PyTest) ---
import pytest

def test_agendamento_sucesso():
    dados = {"aluno": "Lucas Silva", "professor": "Dr. Carlos", "data_hora": "2026-10-10 14:00"}
    obj = AgendamentoSchema(**dados)
    assert obj.aluno == "Lucas Silva"

def test_agendamento_data_passada():
    with pytest.raises(ValueError, match="no futuro"):
        AgendamentoSchema(aluno="Lucas", professor="Carlos", data_hora="2020-01-01 10:00")

def test_agendamento_nome_curto():
    with pytest.raises(ValueError):
        AgendamentoSchema(aluno="Lu", professor="Carlos", data_hora="2026-10-10 14:00")

3.3 Variação 3 — Foco em Persona e Heurísticas de Usabilidade de Nielsen
Objetivo da Variação: Avaliar o fluxo de mensagens e erros do sistema a partir da perspectiva de UX/UI, aplicando as Heurísticas de Nielsen.

Prompt Enviado:
Você é um Consultor UX/UI e Especialista nas 10 Heurísticas de Usabilidade de Nielsen.

Avalie o fluxo de comunicação de erros e feedbacks do código de agendamento de TCC sob a ótica da usabilidade.

Tarefa:
1. Relacione pelo menos 2 Heurísticas de Nielsen que foram violadas na versão inicial do código.
2. Proponha como as mensagens de retorno da API devem ser apresentadas na tela para o estudante universitário para garantir prevenção de erros e clareza.

Resposta Obtida (LLM Output):
Análise de Usabilidade (Heurísticas de Nielsen):

Visibilidade do Status do Sistema (Heurística #1):

Violação Inicial: O retorno genérico "Erro" deixa o usuário sem saber o que aconteceu ou se o sistema processou seu pedido.

Solução UX: Fornecer feedback imediato em estado visual claro (Ex: Toast notification com ícone de alerta e mensagem descritiva).

Prevenção de Erros (Heurística #5) & Diagnóstico de Erros (Heurística #9):

Violação Inicial: O sistema permitia tentar enviar dados inválidos sem orientar previamente o formato exigido.

Solução UX: Exibir o formato esperado (DD/MM/AAAA - HH:mm) diretamente no campo do formulário e desabilitar o botão de envio enquanto o formulário não for válido.

4. Análise Crítica e Prompt Refinado Final
4.1 O que Funcionou vs. O que Faltou
O que funcionou:

A anatomia base do prompt direcionou o LLM a fornecer respostas estruturadas e separadas por blocos.

A inclusão do contexto específico (sistema de TCC) evitou respostas genéricas demais.

As variações demonstraram grande flexibilidade do modelo ao alternar entre explicações didáticas, código com Pydantic e análise teórica de UX.

O que faltou na tentativa inicial:

O prompt inicial não especificou restrições de arquitetura nem padrões de validação como Pydantic/Schemas.

Não foram exigidos testes automatizados na primeira versão.

4.2 O Prompt Refinado Final (Consolidado)
Abaixo está a versão otimizada do Prompt Mestre, incorporando os aprendizados de todas as iterações anteriores:

[PAPEL]
Você é um Tech Lead e Arquiteto de Software Full-Stack especialista em Python (FastAPI), Segurança de APIs e Usabilidade (Heurísticas de Nielsen).

[CONTEXTO]
Alunos de graduação estão construindo o backend de um Sistema de Agendamento Acadêmico. O código inicial apresenta falhas de validação, falta de padrões de segurança e mensagens de erro confusas para os usuários.

[TAREFA]
1. Refatorar o código Python fornecido adotando Pydantic para validação de esquemas e tipagem estática.
2. Garantir que as exceções lançadas sigam a Heurística #9 de Nielsen (ajudar usuários a reconhecer e diagnosticar erros com clareza).
3. Gerar 3 casos de teste unitários com PyTest cobrindo cenários de sucesso e falha.

[RESTRIÇÕES E REGRAS]
- Utilize Python 3.10+.
- Código rigorosamente alinhado à PEP-8.
- Não utilize bibliotecas obsoletas.
- Inclua comentários curtos explicativos no código.

[FORMATO DE SAÍDA]
Entregue a resposta dividida estritamente nas seguintes seções em Markdown:
### 1. Diagnóstico e Usabilidade (Nielsen)
### 2. Código Refatorado com Pydantic
### 3. Suíte de Testes Automatizados (PyTest)

### 4.3 Comparação: Resultado Inicial vs. Resultado Refinado

| Critério | Resultado Inicial (Prompt Mestre) | Resultado Refinado (Prompt Final) |
| :--- | :--- | :--- |
| **Robustez do Código** | Funções simples com validação básica via `if/else`. | Uso de DTOs/Schemas com Pydantic e sanitização. |
| **Qualidade da Resposta** | Resposta textual curta e direta. | Documentação completa com testes e análise de UX. |
| **Tratamento de Erros** | Dicionários simples com mensagens estáticas. | Mensagens informativas orientadas ao usuário final. |
| **Testabilidade** | Sem testes inclusos. | Suíte completa com `pytest` pronta para CI/CD. |

4.4 Validação Humana
Procedimento de Conferência Humana:

Execução Local: O código refatorado e os testes gerados pela IA foram copiados e executados em ambiente local usando Python 3.10 e pytest.

Verificação de Alucinações: Confirmou-se que todas as bibliotecas recomendadas (pydantic, pytest, datetime) existem, estão atualizadas e foram importadas corretamente.

Correção Humana: Foi identificada uma pequena discrepância no fuso horário do método datetime.now() sugerido pelo LLM. O grupo corrigiu manualmente adicionando tratamento para Timezones (datetime.now(timezone.utc)), evitando inconsistências no servidor.
