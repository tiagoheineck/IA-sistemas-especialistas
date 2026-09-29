# 📋 Plano de Aula: Sistemas Especialistas e Motores de Inferência

* **Disciplina:** Inteligência Artificial
* **Nível:** Graduação em Ciência da Computação (Último Ano)
* **Carga Horária:** 4 a 6 horas-aula (Teoria + Laboratório Prático)
* **Tema:** Sistemas Especialistas, Base de Conhecimento, Encadeamento Direto (*Forward Chaining*) e Inverso (*Backward Chaining*).

---

## 1. Objetivos de Aprendizagem

Ao final desta aula, o estudante deverá ser capaz de:

1. Compreender a arquitetura de um **Sistema Baseado em Conhecimento (SBC)** e diferenciar *Base de Conhecimento* de *Motor de Inferência*.
2. Explicar e comparar a dinâmica dos algoritmos de **Encadeamento para a Frente (*Forward Chaining*)** e **Encadeamento para Trás (*Backward Chaining*)**.
3. Escolher a estratégia de inferência adequada para problemas de diagnóstico, triagem e tomada de decisão.
4. Implementar um **protótipo funcional de Sistema Especialista** em Python para triagem (Suporte Técnico ou Triagem Médica).

---

## 2. Estrutura e Conteúdo Programático (Teoria - ~2h)

### Bloco I: Arquitetura de Sistemas Especialistas

* **Histórico e Relevância:** Do DENDRAL/MYCIN aos motores de regras industriais (DROOLS, CLIPS).
* **Separação de Responsabilidades:**
* **Base de Conhecimento (KB):** Fatos + Regras de produção ($SE \text{ [condição]} \implies \text{ENTÃO [conclusão/ação]}$).
* **Memória de Trabalho (Working Memory):** Estado/fatos conhecidos na sessão atual.
* **Motor de Inferência (Inference Engine):** O mecanismo de raciocínio independente dos dados.
* **Interface de Usuário & Módulo de Explicação:** Explicar o "porquê" de uma pergunta ou o "como" de uma conclusão.



---

### Bloco II: Algoritmos de Inferência e Estratégias de Busca

#### 1. Encadeamento para a Frente (*Forward Chaining* — Guiado por Dados)

* **Conceito:** Parte-se dos **fatos conhecidos** e aplicam-se as regras cujas premissas são satisfeitas, adicionando novas conclusões à memória de trabalho até atingir a meta ou esgotar as regras.
* **Complexidade/Uso:** Ideal para problemas de monitoramento, controle, planejamento e triagem inicial (onde temos dados de entrada e queremos descobrir a conclusão).
* **Ciclo de Execução (Match-Resolve-Act):**
1. *Match:* Identifica regras cujos antecedentes ($SE$) casam com os fatos atuais.
2. *Conflict Resolution:* Resolve conflitos entre regras aplicáveis simultaneamente.
3. *Act:* Executa o consequente ($ENTÃO$) da regra escolhida e atualiza a memória de trabalho.



#### 2. Encadeamento para Trás (*Backward Chaining* — Guiado por Objetivos)

* **Conceito:** Parte-se de uma **hipótese/meta** e trabalha-se de trás para frente. Busca regras que tenham a meta no consequente ($ENTÃO$) e verifica recursivamente se as premissas ($SE$) são verdadeiras na Base de Fatos. Se faltarem dados, o sistema faz perguntas direcionadas ao usuário.
* **Complexidade/Uso:** Ideal para diagnóstico focado, verificação de hipóteses e cenários onde perguntar/medir tudo é caro ou inviável (ex: exames médicos específicos).

---

### Quadro Comparativo para Discussão em Aula

| Critério | Forward Chaining | Backward Chaining |
| --- | --- | --- |
| **Direção** | Dados $\rightarrow$ Conclusões | Meta $\rightarrow$ Fatos de Suporte |
| **Abordagem** | Guiado por Dados (*Data-driven*) | Guiado por Objetivos (*Goal-driven*) |
| **Comportamento** | Reativo / Exploratório | Hipotético / Investigativo |
| **Aplicação Típica** | Triagem geral, interpretação de sensores | Diagnóstico médico focado, depuração |

---

## 3. Roteiro da Experimentação Prática (Laboratório - ~2h a 4h)

### Desafio Prático

> **Projeto:** Construção de um Protótipo de Sistema Especialista para **Triagem de Suporte Técnico** (ou Triagem Médica Inicial).

### 🛠️ Código Template de Referência (Python)

Abaixo está uma implementação simples e didática de um Motor de Inferência com *Forward Chaining* para triagem técnica/médica que pode ser disponibilizada aos alunos como ponto de partida:

```python
class BaseDeConhecimento:
    def __init__(self):
        # Fatos conhecidos na sessão (Memória de Trabalho)
        self.fatos = set()
        # Regras no formato: (conjunto_de_premissas, consequente_ou_acao)
        self.regras = []

    def adicionar_fato(self, fato):
        self.fatos.add(fato)

    def adicionar_regra(self, premissas, consequente):
        self.regras.append((set(premissas), consequente))


class MotorDeInferênciaForward:
    def __init__(self, base_conhecimento):
        self.kb = base_conhecimento

    def executar(self):
        novos_fatos = True
        regras_aplicadas = set()

        print("--- Iniciando Processo de Inferência (Forward Chaining) ---")
        
        while novos_fatos:
            novos_fatos = False
            for idx, (premissas, consequente) in enumerate(self.kb.regras):
                if idx in regras_aplicadas:
                    continue
                
                # Verifica se todas as premissas da regra estão na base de fatos
                if premissas.issubset(self.kb.fatos):
                    print(f"[REGRA DISPARADA]: SE {list(premissas)} ENTÃO '{consequente}'")
                    self.kb.adicionar_fato(consequente)
                    regras_aplicadas.add(idx)
                    novos_fatos = True
                    break # Recomeça o ciclo com a memória atualizada

        print("\n--- Fatos Finais Inferidos ---")
        for fato in sorted(self.kb.fatos):
            print(f"• {fato}")


# ==========================================
# SIMULAÇÃO: TRIAGEM DE SUPORTE TÉCNICO DE REDE
# ==========================================
if __name__ == "__main__":
    kb = BaseDeConhecimento()

    # Definição das Regras do Especialista
    kb.adicionar_regra(["sem_internet", "ping_roteador_ok"], "problema_provedor")
    kb.adicionar_regra(["sem_internet", "ping_roteador_falhou"], "problema_rede_local")
    kb.adicionar_regra(["problema_rede_local", "cabo_desconectado"], "triagem: Reconectar cabo de rede")
    kb.adicionar_regra(["problema_rede_local", "cabo_conectado", "wifi_desativado"], "triagem: Ativar Wi-Fi do dispositivo")
    kb.adicionar_regra(["problema_provedor"], "triagem: Reiniciar Modem e Contatar Operadora")

    # Entrada de Fatos (Simulação de Coleta de Dados do Usuário)
    kb.adicionar_fato("sem_internet")
    kb.adicionar_fato("ping_roteador_falhou")
    kb.adicionar_fato("cabo_desconectado")

    # Execução do Motor
    motor = MotorDeInferênciaForward(kb)
    motor.executar()

```

---

### Tarefas Sugeridas para os Alunos (Exercício de Lab)

1. **Expansão da Base de Regras:**
* Adicionar regras para cobrir pelo menos 5 cenários distintos de triagem (ex: *Suporte Técnico de Hardware* ou *Triagem Médica de Manchester - Vermelho/Laranja/Amarelo/Verde*).


2. **Interface Interativa:**
* Modificar o código para que o sistema **pergunte ao usuário** interativamente sobre os fatos quando eles não estiverem presentes na memória de trabalho.


3. **Implementação do Módulo de Explicação:**
* Criar uma função `explicar_por_que(conclusao)` que exiba a árvore de regras disparadas que levaram a determinada triagem.


4. *(Desafio Avançado)* **Implementação de Backward Chaining:**
* Implementar o método equivalente em *Backward Chaining*, onde o sistema recebe como entrada uma meta (ex: `"triagem: Reconectar cabo de rede"`) e investiga recursivamente se os fatos a suportam.
