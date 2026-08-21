# Atividade Prática — Suíte Iterativa de Prompts para Engenharia de Software

* **Disciplina:** Tendências em Ciências da Computação
* **Unidade I:** Fundamentos de Engenharia de Prompt
* **Integrante:** Dilson Ribeiro de Souza

---

## 1. Definição do Tema e Problema Real

### Domínio

**Tecnologia e Vida Acadêmica Universitária.**

### Problema Real

Alunos de Ciência da Computação frequentemente desenvolvem APIs REST e módulos de código para projetos acadêmicos, como Sistemas de Gestão de TCC ou Agendamento de Monitorias, mas enfrentam dificuldades para realizar revisões de código (*Code Review*) completas.

Essas revisões precisam considerar não apenas a qualidade sintática e a segurança do código, mas também aspectos relacionados à usabilidade, como as **Heurísticas de Nielsen**, além da cobertura de testes unitários.

### Objetivo com IA Generativa

Desenvolver e testar uma suíte iterativa de prompts para utilizar uma IA generativa como **Tech Lead** e **Especialista em UX/UI**, realizando a revisão de um *snippet* de código Python/FastAPI e produzindo:

* Diagnóstico dos problemas encontrados;
* Refatoração do código;
* Melhorias de segurança;
* Análise de usabilidade;
* Casos de testes unitários;
* Explicações didáticas sobre as alterações realizadas.

---

# 2. Artefatos Iniciais — Prompt Mestre e Anatomia

## 2.1 Anatomia Profissional do Prompt Mestre Inicial

| Componente                 | Conteúdo Aplicado no Prompt                                                                                                                                         |
| :------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Papel (Role)**           | Engenheiro de Software Senior e Revisor de Código Python.                                                                                                           |
| **Contexto**               | Um estudante desenvolveu uma função em Python/FastAPI para agendamento de orientações de TCC, mas o código está sem validações adequadas e sem tratamento de erros. |
| **Objetivo**               | Revisar o código, apontar falhas de boas práticas e fornecer a versão refatorada.                                                                                   |
| **Tarefa**                 | Analisar o código fornecido, destacar 3 pontos fracos e reescrever a função.                                                                                        |
| **Restrições**             | Utilizar Python 3.10+, manter comentários explicativos e não usar bibliotecas externas não padrão além do FastAPI.                                                  |
| **Formato de Saída**       | Resposta em Markdown contendo: Lista de Problemas, Código Refatorado e Explicação Breve.                                                                            |
| **Critérios de Qualidade** | O código deve seguir a convenção PEP-8 e conter validação explícita de datas.                                                                                       |

---

## 2.2 Prompt Mestre Inicial

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
```

---

## 2.3 Resposta Obtida — LLM Output Inicial

### 1. Problemas Encontrados

1. Falta de validação de tipo e formato para o campo `data_hora`.
2. Tratamento de erro muito genérico, retornando apenas a string `"Erro"` em vez de lançar uma exceção ou detalhar o problema.
3. Ausência de boas práticas PEP-8 e tipagem de dados (*Type Hints*).

### 2. Código Refatorado

```python
from datetime import datetime


def agendar(aluno: str, professor: str, data_hora: str) -> dict:
    if not aluno.strip() or not professor.strip():
        return {
            "sucesso": False,
            "mensagem": "Nome do aluno e professor são obrigatórios."
        }

    try:
        data_obj = datetime.strptime(data_hora, "%Y-%m-%d %H:%M")

        if data_obj < datetime.now():
            return {
                "sucesso": False,
                "mensagem": "A data deve ser no futuro."
            }

    except ValueError:
        return {
            "sucesso": False,
            "mensagem": "Formato de data inválido. Use YYYY-MM-DD HH:MM."
        }

    db.save(aluno, professor, data_obj)

    return {
        "sucesso": True,
        "mensagem": "Agendamento realizado com sucesso!"
    }
```

### 3. Explicação das Melhorias

Foi adicionada validação utilizando `strip()` para evitar nomes em branco, além da conversão e validação da data e hora para impedir agendamentos no passado.

Também foi adotado um retorno estruturado em formato de dicionário, permitindo que o sistema identifique se a operação foi realizada com sucesso e apresente uma mensagem específica ao usuário.

---

## 2.4 Técnicas Utilizadas na Primeira Iteração

### Role Prompting

Definição da persona **"Engenheiro de Software Senior"**, direcionando o modelo para responder com uma perspectiva profissional de desenvolvimento de software.

### Contextualização

Inserção de um cenário real relacionado a um **sistema acadêmico de agendamento de orientações de TCC**, evitando que a resposta fosse excessivamente genérica.

### Restrição de Formato

Definição clara da estrutura da resposta em três seções:

1. Problemas encontrados;
2. Código refatorado;
3. Explicação das melhorias.

---

# 3. Desenvolvimento de 3 Variações do Prompt

Para refinar o resultado obtido no prompt inicial, foram desenvolvidas três variações focadas em objetivos distintos do desenvolvimento de software.

```text
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
```

---

# 3.1 Variação 1 — Foco Didático e Explicação Passo a Passo

### Objetivo da Variação

Explicar a refatoração para um estudante iniciante utilizando analogias e detalhamento dos conceitos envolvidos.

### Prompt Enviado

```text
Você é um Professor de Programação com foco didático para iniciantes.

A partir da solução de código refatorada anteriormente, crie um guia explicativo passo a passo orientado a um estudante do 1º semestre de Ciência da Computação.

Requisitos:
- Utilize uma analogia do cotidiano para explicar o conceito de tratamento de exceções (try/except).
- Explique o motivo pelo qual usar dicionários de retorno é melhor do que retornar apenas strings soltas.
- Mantenha o tom encorajador e profissional.
```

### Resposta Obtida — LLM Output

#### Guia de Aprendizado: Melhorando seu Código

### 1. A Analogia do Try/Except — O Balcão de Atendimento

Pense no `try/except` como um atendente de biblioteca.

Você pede um livro (`try`). Se o livro existir, o atendente entrega o livro normalmente. Porém, se o livro não existir ou ocorrer algum problema, em vez de o atendente simplesmente parar de funcionar, ele possui uma instrução preparada para informar o problema de maneira adequada (`except`).

Da mesma forma, o `try/except` permite que o programa tente executar uma operação e trate possíveis erros sem interromper toda a aplicação.

### 2. Por que usar Dicionários em vez de Strings Soltas?

Quando o programa retorna apenas:

```python
"Erro"
```

o sistema que recebe essa informação não sabe exatamente o que aconteceu.

Ao utilizar um retorno estruturado como:

```python
{
    "sucesso": False,
    "mensagem": "Data inválida."
}
```

a aplicação consegue identificar tanto o estado da operação quanto a mensagem que deverá ser apresentada ao usuário.

Por exemplo, a interface pode utilizar o campo `sucesso` para decidir se deve apresentar uma mensagem de sucesso ou de erro, enquanto utiliza o campo `mensagem` para mostrar uma explicação específica ao estudante.

---

# 3.2 Variação 2 — Foco em Performance, Segurança e Casos de Teste

### Objetivo da Variação

Elevar o nível do código para um contexto próximo de produção (*Production-Ready*), incluindo validação de dados, segurança e testes automatizados.

### Prompt Enviado

```text
Você é um Arquiteto de Software e Engenheiro de Segurança de Dados.

Refatore a função de agendamento para torná-la pronta para produção (Production-Ready).

Diretrizes exigidas:
1. Aplique validações contra entradas maliciosas ou caracteres especiais nos nomes.
2. Adicione tipagem estática rigorosa usando Pydantic.
3. Escreva 3 casos de teste automatizados utilizando a biblioteca PyTest:
   - 1 caso de sucesso;
   - 1 caso de data no passado;
   - 1 caso de nome em branco.
```

### Resposta Obtida — LLM Output

```python
from datetime import datetime

from pydantic import BaseModel, Field, validator


class AgendamentoSchema(BaseModel):
    aluno: str = Field(..., min_length=3, max_length=100)
    professor: str = Field(..., min_length=3, max_length=100)
    data_hora: datetime

    @validator("data_hora")
    def validar_data_futura(cls, v):
        if v < datetime.now():
            raise ValueError("A data de agendamento deve ser no futuro.")
        return v


# --- CASOS DE TESTE (PyTest) ---

import pytest


def test_agendamento_sucesso():
    dados = {
        "aluno": "Lucas Silva",
        "professor": "Dr. Carlos",
        "data_hora": "2026-10-10 14:00"
    }

    obj = AgendamentoSchema(**dados)

    assert obj.aluno == "Lucas Silva"


def test_agendamento_data_passada():
    with pytest.raises(ValueError, match="no futuro"):
        AgendamentoSchema(
            aluno="Lucas",
            professor="Carlos",
            data_hora="2020-01-01 10:00"
        )


def test_agendamento_nome_curto():
    with pytest.raises(ValueError):
        AgendamentoSchema(
            aluno="Lu",
            professor="Carlos",
            data_hora="2026-10-10 14:00"
        )
```

---

# 3.3 Variação 3 — Foco em Persona e Heurísticas de Usabilidade de Nielsen

### Objetivo da Variação

Avaliar o fluxo de mensagens e erros do sistema de agendamento sob a perspectiva de UX/UI, utilizando as **10 Heurísticas de Usabilidade de Nielsen**.

### Prompt Enviado

```text
Você é um Consultor UX/UI e Especialista nas 10 Heurísticas de Usabilidade de Nielsen.

Avalie o fluxo de comunicação de erros e feedbacks do código de agendamento de TCC sob a ótica da usabilidade.

Tarefa:
1. Relacione pelo menos 2 Heurísticas de Nielsen que foram violadas na versão inicial do código.
2. Proponha como as mensagens de retorno da API devem ser apresentadas na tela para o estudante universitário para garantir prevenção de erros e clareza.
```

### Resposta Obtida — LLM Output

## Análise de Usabilidade — Heurísticas de Nielsen

### Visibilidade do Status do Sistema — Heurística #1

**Violação Inicial:**

O retorno genérico `"Erro"` deixa o usuário sem saber exatamente o que aconteceu ou se o sistema processou corretamente sua solicitação.

**Solução UX:**

Fornecer feedback imediato em um estado visual claro, como uma notificação do tipo *Toast*, acompanhada de um ícone de alerta e uma mensagem descritiva.

---

### Prevenção de Erros — Heurística #5

**Violação Inicial:**

O sistema permitia que o usuário tentasse enviar dados inválidos sem receber orientações prévias sobre o formato esperado.

**Solução UX:**

Exibir o formato esperado diretamente no campo do formulário, por exemplo:

```text
DD/MM/AAAA - HH:mm
```

Também é possível desabilitar o botão de envio enquanto o formulário não estiver válido.

---

### Diagnóstico e Recuperação de Erros — Heurística #9

**Violação Inicial:**

A mensagem `"Erro"` não explica qual campo está incorreto nem como o usuário pode solucionar o problema.

**Solução UX:**

As mensagens devem informar claramente:

* O que aconteceu;
* Qual informação está incorreta;
* Como o usuário pode corrigir o problema.

---

# 4. Análise Crítica e Prompt Refinado Final

## 4.1 O que Funcionou vs. O que Faltou

### O que Funcionou

A anatomia base do prompt direcionou o LLM a fornecer respostas estruturadas e separadas por blocos.

A inclusão do contexto específico, relacionado a um sistema acadêmico de TCC, evitou respostas genéricas demais.

As três variações demonstraram a flexibilidade do modelo ao alternar entre diferentes perspectivas:

* Explicação didática;
* Desenvolvimento e testes;
* Segurança;
* Usabilidade;
* Heurísticas de Nielsen.

A utilização de diferentes personas também ajudou a direcionar a IA para objetivos específicos em cada etapa da atividade.

### O que Faltou na Tentativa Inicial

O prompt inicial não especificou restrições de arquitetura nem padrões de validação como **Pydantic/Schemas**.

Também não foram exigidos testes automatizados na primeira versão.

Além disso, a primeira versão poderia ter especificado de forma mais clara como os erros deveriam ser apresentados ao usuário final.

---

# 4.2 O Prompt Refinado Final — Consolidado

A partir dos resultados obtidos nas iterações anteriores, foi desenvolvido um novo Prompt Mestre incorporando os principais aprendizados da atividade.

### Prompt Final

```text
[PAPEL]

Você é um Tech Lead e Arquiteto de Software Full-Stack especialista em Python (FastAPI), Segurança de APIs e Usabilidade (Heurísticas de Nielsen).


[CONTEXTO]

Alunos de graduação estão construindo o backend de um Sistema de Agendamento Acadêmico. O código inicial apresenta falhas de validação, falta de padrões de segurança e mensagens de erro confusas para os usuários.


[TAREFA]

1. Refatorar o código Python fornecido adotando Pydantic para validação de esquemas e tipagem estática.

2. Garantir que as exceções lançadas sigam a Heurística #9 de Nielsen, ajudando usuários a reconhecer e diagnosticar erros com clareza.

3. Gerar 3 casos de teste unitários com PyTest cobrindo cenários de sucesso e falha.


[RESTRIÇÕES E REGRAS]

- Utilize Python 3.10+.
- Código rigorosamente alinhado à PEP-8.
- Não utilize bibliotecas obsoletas.
- Inclua comentários curtos e explicativos no código.


[FORMATO DE SAÍDA]

Entregue a resposta dividida estritamente nas seguintes seções em Markdown:

### 1. Diagnóstico e Usabilidade (Nielsen)

### 2. Código Refatorado com Pydantic

### 3. Suíte de Testes Automatizados (PyTest)
```

---

# 4.3 Comparação — Resultado Inicial vs. Resultado Refinado

| Critério                  | Resultado Inicial — Prompt Mestre                   | Resultado Refinado — Prompt Final                         |
| :------------------------ | :-------------------------------------------------- | :-------------------------------------------------------- |
| **Robustez do Código**    | Funções simples com validação básica via `if/else`. | Uso de DTOs/Schemas com Pydantic e validação estruturada. |
| **Qualidade da Resposta** | Resposta textual curta e direta.                    | Documentação mais completa, com testes e análise de UX.   |
| **Tratamento de Erros**   | Dicionários simples com mensagens estáticas.        | Mensagens informativas orientadas ao usuário final.       |
| **Validação**             | Validação básica de strings e datas.                | Validação estruturada utilizando Pydantic.                |
| **Testabilidade**         | Sem testes inclusos.                                | Suíte de testes automatizados com PyTest.                 |
| **Segurança**             | Poucas validações de entrada.                       | Maior preocupação com validação e entradas malformadas.   |
| **Usabilidade**           | Não considerada diretamente.                        | Aplicação das Heurísticas de Nielsen.                     |
| **Documentação**          | Explicação breve.                                   | Explicação estruturada e orientada ao contexto acadêmico. |

---

# 4.4 Validação Humana

## Procedimento de Conferência Humana

Após a geração do código pela IA, foi realizada uma conferência humana para verificar a coerência das soluções apresentadas.

### Execução Local

O código refatorado e os testes gerados pela IA foram copiados e executados em ambiente local utilizando **Python 3.10** e **PyTest**.

O objetivo foi verificar se a implementação gerada poderia ser executada corretamente e se os casos de teste apresentavam o comportamento esperado.

### Verificação de Alucinações

Foi realizada uma conferência das bibliotecas utilizadas pela solução.

Verificou-se que as bibliotecas e recursos recomendados, como:

* `pydantic`;
* `pytest`;
* `datetime`;

existem e são compatíveis com a proposta apresentada.

Também foram verificadas as importações utilizadas nos exemplos de código.

### Correção Humana

Durante a validação foi identificada uma pequena discrepância relacionada ao tratamento de fuso horário no uso de `datetime.now()` sugerido pelo LLM.

Para evitar possíveis inconsistências entre o horário local do ambiente de desenvolvimento e o horário utilizado pelo servidor, foi realizada uma correção manual utilizando tratamento explícito de timezone:

```python
from datetime import datetime, timezone

data_atual = datetime.now(timezone.utc)
```

Essa alteração demonstra a importância da **validação humana após a geração de código por IA**, principalmente em situações que envolvem datas, horários, segurança e sistemas que podem ser executados em diferentes ambientes.

---

# 5. Conclusão

A atividade demonstrou, na prática, como a Engenharia de Prompt pode ser utilizada de maneira iterativa para melhorar a qualidade das respostas produzidas por uma IA generativa.

O primeiro prompt apresentou uma solução funcional, porém relativamente simples. A partir da análise desse resultado, foram desenvolvidas novas variações com diferentes objetivos, permitindo explorar aspectos didáticos, de segurança, testes automatizados e usabilidade.

A evolução dos prompts mostrou que a qualidade da resposta está diretamente relacionada à qualidade das instruções fornecidas ao modelo. A definição clara de **papel, contexto, objetivo, tarefas, restrições e formato de saída** possibilitou obter respostas mais específicas e adequadas ao problema proposto.

A etapa de validação humana também foi importante para identificar possíveis problemas na solução gerada automaticamente. O ajuste relacionado ao tratamento de *timezone* demonstra que a IA generativa deve ser utilizada como uma ferramenta de apoio ao desenvolvimento, e não como substituta da análise e da decisão do desenvolvedor.

Dessa forma, a suíte iterativa de prompts desenvolvida nesta atividade contribuiu para demonstrar como diferentes estratégias de Engenharia de Prompt podem ser aplicadas em um cenário real de Engenharia de Software, combinando **qualidade de código, segurança, testes automatizados e experiência do usuário**.
