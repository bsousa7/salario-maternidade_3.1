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
