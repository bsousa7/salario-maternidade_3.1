# Diagrama AS-IS — Salário-Maternidade Urbano (MEI / Contribuinte Individual)

**Leitura:** fluxo da esquerda para a direita, agrupado em quatro fases lógicas.
Linhas sólidas = fluxo nominal (happy path). Linhas tracejadas = falhas e desvios.
Nós em losango com borda = fail points sinalizados no Blueprint (`C_blueprint_asis.md`).

---

```mermaid
flowchart LR
    DAS[/"📄 DAS-MEI pago\ntempestivamente\npré-serviço"/]
    MEI(["👤 MEI /\nContribuinte\nIndividual"])

    subgraph F1["① Solicitação  ~10 min"]
        E1["🔐 Login Gov.br\nPrata / Ouro"]
        E2["📋 Seleciona\nSalário-Maternidade\nUrbano"]
        E3["📝 Informa Certidão\nmatrícula 32 dígitos\n+ datas"]
        E4["🔖 Confirma dados\ne Protocola — NB"]
    end

    subgraph F2["② Validação  ~segundos"]
        Motor{{"⚙ Motor de\nRegras / Workflow"}}
    end

    subgraph F3["③ Decisão  ~minutos"]
        E6["📄 Recebe Decisão\nCarta de Concessão\n+ Despacho"]
    end

    subgraph F4["④ Liquidação  1 – 25 dias"]
        E7["💳 Crédito\nem Conta"]
    end

    subgraph SUP["Sistemas de Registro de Retaguarda"]
        SIRC[("SIRC /\nCRC Nacional")]
        CNIS[("CNIS")]
        SIBE[("SIBE / Prisma")]
        SisPag[("Sist. Pagamentos\nINSS / Banco")]
    end

    EXC1[/"⚠ EXC-1\nAnálise Manual"/]
    EXC2[/"⚠ EXC-2\nEm Exigência"/]
    FPLat[/"⚠ Fail Latente\nCrédito Devolvido"/]

    DAS -. "valida qualidade\nde segurada" .-> CNIS

    MEI -->|"CPF + senha\nou biometria"| E1
    E1 --> E2 --> E3 --> E4 --> Motor
    Motor -->|"consulta\ncertidão"| SIRC
    Motor -->|"verifica qualidade\n+ IN 188/2025"| CNIS
    Motor -->|"registra\nprocesso"| SIBE
    SIBE -->|"homologa\ndeferimento"| E6
    E6 -->|"ordem de\npagamento"| SisPag
    SisPag -->|"crédito"| E7

    SIRC -. "⚠ 164 falhas\nem 15 meses" .-> EXC1
    E3 -. "⚠ dados\ndivergentes" .-> EXC1
    CNIS -. "⚠ DAS-MEI inaugural\nem atraso" .-> EXC2
    Motor -. "⚠ regra\npré-IN 188/2025" .-> EXC2
    E4 -. "⚠ conta bancária\ninválida" .-> FPLat
    E7 -. "⚠ conta\nencerrada" .-> FPLat

    classDef citizen fill:#dbeafe,stroke:#2563eb,color:#1e3a8a
    classDef motor fill:#fef3c7,stroke:#d97706,color:#92400e
    classDef sistema fill:#f3f4f6,stroke:#6b7280,color:#374151
    classDef failpoint fill:#fee2e2,stroke:#dc2626,color:#7f1d1d
    classDef preservice fill:#f0fdf4,stroke:#16a34a,color:#14532d

    class MEI citizen
    class Motor motor
    class SIRC,CNIS,SIBE,SisPag sistema
    class EXC1,EXC2,FPLat failpoint
    class DAS preservice
```

---

## Matriz RACI — Responsabilidades por Etapa

**Atores:**
- **MEI** — Cidadã / Requerente
- **Gov.br** — Plataforma de autenticação federal
- **Meu INSS** — Interface Frontstage (tecnologia visível)
- **Motor** — Motor de Regras / Workflow (Backstage — Tecnologia Ativa)
- **SIRC** — SIRC / CRC Nacional (registro civil)
- **CNIS** — CNIS (histórico contributivo)
- **SIBE** — SIBE / Prisma (gestão de benefícios)
- **SisPag** — Sistema de Pagamentos INSS / Banco

**Legenda RACI:** R = Responsible (executa) · A = Accountable (responde) · C = Consulted (consultado) · I = Informed (informado)

| Etapa | MEI | Gov.br | Meu INSS | Motor | SIRC | CNIS | SIBE | SisPag |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **E1 — Login Gov.br** | R | A | I | — | — | — | — | — |
| **E2 — Seleciona Serviço** | R | C | A | I | — | — | — | — |
| **E3 — Informa Dados da Certidão** | R | — | A | C | C | — | — | — |
| **E4 — Confirma e Protocola** | R | — | A | C | — | — | I | — |
| **E5 — Processamento Automático** | I | — | I | A/R | C | C | C | — |
| **E6 — Recebe Decisão** | I | — | R | — | — | — | A | — |
| **E7 — Recebe Crédito** | I | — | I | — | — | — | A | R |

---

## Handoffs Estruturados (H1 – H7)

Cada handoff documenta: acionador, dado transferido, ator receptor, resultado esperado e fail point associado.

### H1 — MEI → Gov.br → Meu INSS
| Campo | Valor |
|---|---|
| **Acionador** | Cidadã acessa `gov.br/meu-inss` e insere CPF + senha ou biometria |
| **Dado transferido** | Credenciais de autenticação + nível de confiabilidade da conta Gov.br (exige mínimo Prata) |
| **Ator receptor** | Plataforma Gov.br valida e emite token de sessão; Meu INSS recebe token e libera acesso ao catálogo |
| **Resultado esperado** | Sessão autenticada com perfil MEI / Contribuinte Individual habilitado |
| **Fail point** | Conta Gov.br com nível Bronze bloqueia acesso — cidadã deve elevar nível antes de prosseguir |

### H2 — Meu INSS (Frontstage) → Motor de Regras
| Campo | Valor |
|---|---|
| **Acionador** | Cidadã preenche formulário e clica em "Enviar" na Etapa 4 |
| **Dado transferido** | Pacote estruturado: matrícula da certidão (32 dígitos) + data de registro + data de nascimento + dados bancários da beneficiária |
| **Ator receptor** | Motor de Regras / Workflow (Backstage) |
| **Resultado esperado** | Motor inicia fluxo de análise automatizada e cria processo no SIBE; Meu INSS exibe NB gerado |
| **Fail point** | Conta bancária inválida ou CPF divergente da titular — fail latente registrado no protocolo, manifesta-se somente na E7 |

### H3 — Motor → SIRC / CRC Nacional
| Campo | Valor |
|---|---|
| **Acionador** | Motor dispara consulta síncrona ao SIRC via barramento CRC Nacional |
| **Dado transferido** | Query com os 3 campos da certidão (matrícula 32 dígitos, data de registro, data de nascimento) |
| **Ator receptor** | SIRC / CRC Nacional |
| **Resultado esperado** | Confirmação do fato gerador (nascimento) e validação da autenticidade da Certidão de Nascimento |
| **Fail point** | **→ EXC-1:** SIRC indisponível (164 falhas registradas em 15 meses via LAI) — fluxo desviado para instrução manual; ou dados divergentes — processo cai em "Em Exigência" |

### H4 — Motor → CNIS
| Campo | Valor |
|---|---|
| **Acionador** | Motor executa cruzamento paralelo ao CNIS durante o Processamento Automático (E5) |
| **Dado transferido** | CPF da segurada para verificação de qualidade de segurada e tempestividade do DAS-MEI inaugural |
| **Ator receptor** | CNIS |
| **Resultado esperado** | Confirmação da qualidade de segurada ativa e aplicação da regra de isenção de carência da IN 188/2025 |
| **Fail point** | **→ EXC-2:** DAS-MEI inaugural pago em atraso — qualidade de segurada não reconhecida; ou Motor parametrizado com regra pré-IN 188/2025 — exigência indevida de carência |

### H5 — Motor → SIBE / Prisma
| Campo | Valor |
|---|---|
| **Acionador** | Motor conclui análise com resultado de deferimento |
| **Dado transferido** | Decisão de deferimento + NB + fundamentação normativa (IN 188/2025) |
| **Ator receptor** | SIBE / Prisma |
| **Resultado esperado** | SIBE registra deferimento e dispara geração da Carta de Concessão |
| **Fail point** | Falha de gravação no SIBE pode bloquear emissão da Carta sem retorno visível à cidadã |

### H6 — SIBE / Prisma → Meu INSS (Frontstage)
| Campo | Valor |
|---|---|
| **Acionador** | SIBE homologa deferimento e aciona barramento de notificações |
| **Dado transferido** | Despacho fundamentado + Carta de Concessão em PDF + data prevista de crédito |
| **Ator receptor** | Meu INSS (exibe no painel da cidadã) + notificação via e-mail/SMS |
| **Resultado esperado** | Cidadã recebe ciência formal do deferimento e acessa PDF da Carta de Concessão |
| **Fail point** | Falha no barramento de notificações — cidadã não é informada; deve consultar painel proativamente |

### H7 — SIBE / Prisma → Sistema de Pagamentos → Banco
| Campo | Valor |
|---|---|
| **Acionador** | SIBE emite ordem de pagamento ao sistema financeiro do INSS |
| **Dado transferido** | Ordem de pagamento com valor do salário-maternidade + dados bancários da beneficiária (conta, agência, CPF) |
| **Ator receptor** | Sistema de Pagamentos INSS → Banco conveniado (BB / CEF) → conta da beneficiária |
| **Resultado esperado** | Crédito do salário-maternidade na conta bancária da segurada em até 25 dias úteis |
| **Fail point** | **→ FPLat:** Conta bancária encerrada ou dados divergentes — crédito devolvido; necessidade de atualização cadastral e reprocessamento manual |

---

## Legenda de Cores e Formas

| Elemento | Cor | Significado |
|---|---|---|
| 👤 Oval azul | `#dbeafe` | Cidadã (persona focal — MEI / Contribuinte Individual) |
| 📄 Losango verde | `#f0fdf4` | Input pré-serviço (DAS-MEI — fora do escopo temporal) |
| ⚙ Hexágono amarelo | `#fef3c7` | Motor de Regras / Workflow (Backstage — Tecnologia Ativa) |
| 🗄 Cilindro cinza | `#f3f4f6` | Sistemas de Registro de Retaguarda (CNIS, SIRC, SIBE) |
| ⚠ Losanco vermelho | `#fee2e2` | Fail point — porta de saída para Blueprint de Exceção |
| → Linha sólida | — | Fluxo nominal do happy path |
| -.-> Linha tracejada | — | Desvio por falha ou conexão assíncrona de pré-condição |

## Relações-Chave Evidenciadas

| Relação | Tipo | Descrição |
|---|---|---|
| MEI → Motor (via E1–E4) | Indireta / mediada | A cidadã aciona o Motor apenas através da interface Meu INSS; não há contato direto |
| Motor ↔ SIRC | Síncrona | Consulta em tempo real ao CRC Nacional — ponto de maior fragilidade operacional |
| Motor ↔ CNIS | Síncrona | Verificação da qualidade de segurada e adimplência do DAS-MEI |
| DAS-MEI -.-> CNIS | Assíncrona / pré-jornada | Adimplência registrada antes do requerimento; ausência gera EXC-2 |
| SIBE → E6 | Resultado de retaguarda | O deferimento nasce no SIBE, não diretamente no Motor |
| E4 -.-> FPLat | Fail latente | Conta bancária inválida no protocolo só falha na Etapa 7 |

---

*Referência: Blueprint AS-IS completo em `C_blueprint_asis.md`.*
