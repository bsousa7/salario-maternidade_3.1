# Service Blueprint AS-IS — Salário-Maternidade Urbano (MEI / Contribuinte Individual)

**Persona focal:** Microempreendedora Individual (MEI) ou Contribuinte Individual — requerimento pós-parto com Certidão de Nascimento em mãos, via portal Meu INSS (canal G2C digital direto).

**Fronteiras temporais:** Login Gov.br → Crédito em conta bancária + Carta de Concessão disponível **ou** ciência formal do despacho de indeferimento.

**Fonte de verdade:** Triangulação entre IN PRES/INSS nº 188/2025, Manual DTI/CGAUT v3 (passo-a-passo B80 Urbano, 05/2025) e dados empíricos: BEPS Jan/2026, Portal da Transparência Mar/2026, Relatório LAI de indisponibilidade do SIRC (164 falhas entre jan/2023 e abr/2024) e métricas da força-tarefa MAES (Mai/2026 — 61.616 requerimentos com prazo estourado).

**Nota metodológica (notação híbrida):** Este artefato mapeia o *happy path* de concessão automática. Os nós de bifurcação críticos são sinalizados como portas de saída (⚠ **→ EXC**) e remetem a dois Blueprints de Exceção complementares: **EXC-1** (falha SIRC → desvio para "Em Análise" manual) e **EXC-2** (rejeição de qualidade de segurada → "Em Exigência").

**Adaptação de camadas para serviço digital (Digital Service Blueprint):**
- *Frontstage* = interface Meu INSS (tecnologia visível pela cidadã)
- *Backstage* = **Tecnologia Ativa** (motor de regras Workflow — agente decisor algorítmico, invisível)
- *Processos de Suporte* = **Sistemas de Registro de Retaguarda** (repositórios e APIs — CNIS, SIRC, SIBE)

---

> **INPUT PRÉ-JORNADA** *(fora do escopo temporal — condição de entrada no happy path)*
> `📄 DAS-MEI pago tempestivamente` — O histórico de adimplência da MEI, registrado no CNIS, é o insumo estático que valida a qualidade de segurada ativa. Conecta-se por linha pontilhada ao CNIS na camada de Processos de Suporte da Etapa 5. A ausência deste input ou o pagamento inaugural em atraso é o gatilho do ⚠ EXC-2.

---

## Mapa de Fases e Etapas

| Fase | FASE 1 — SOLICITAÇÃO (~10 min) | | | FASE 2 — VALIDAÇÃO (~segundos) | FASE 3 — DECISÃO (~minutos) | FASE 4 — LIQUIDAÇÃO (1 a 25 dias) |
|---|---|---|---|---|---|---|
| **Etapa** | **1. Login Gov.br** | **2. Seleciona Serviço** | **3. Informa Dados da Certidão** | **4. Confirma e Protocola** | **5. Processamento Automático** | **6. Recebe Decisão** | **7. Recebe Crédito** |

---

## Blueprint AS-IS

| Camada \ Etapa | **1. Login Gov.br** | **2. Seleciona Serviço** | **3. Informa Dados da Certidão** | **4. Confirma e Protocola** | **5. Processamento Automático** | **6. Recebe Decisão** | **7. Recebe Crédito** |
|---|---|---|---|---|---|---|---|
| **Evidências Físicas** | Tela de autenticação Gov.br | Catálogo de serviços Meu INSS | Formulário digital de dados + Certidão de Nascimento (apoio físico/digital) | Tela de resumo do pedido + **Número de Benefício (NB)** gerado | Status "Em Processamento" no painel + SMS/e-mail de confirmação de protocolo | **Carta de Concessão em PDF** no painel + notificação e-mail/SMS de deferimento | **Extrato bancário** com crédito do salário-maternidade |
| **Ações do Cidadão** | Acessa gov.br/meu-inss, insere CPF e senha ou biometria Gov.br | Busca e clica em "Salário-Maternidade Urbano" no catálogo | Digita matrícula da certidão (32 dígitos), data de registro e data de nascimento da criança | Revisa dados, informa conta bancária para crédito e clica em "Enviar" | Acompanha status no painel Meu INSS (ação passiva — monitoramento) | Lê despacho de deferimento, baixa a Carta de Concessão em PDF e toma ciência da data de crédito | Verifica crédito na conta bancária e encerra a jornada |
| — *Linha de Interação* — | | | | | | | |
| **Frontstage** *(tecnologia visível — Meu INSS)* | Portal Gov.br autentica credenciais (nível Prata ou Ouro) e redireciona para Meu INSS | Interface Meu INSS exibe catálogo de serviços disponíveis para o perfil | Formulário estruturado com validação de formato (campo de 32 caracteres obrigatório para matrícula) | Meu INSS gera número NB e exibe tela de confirmação de protocolo com data/hora | Painel de acompanhamento exibe status em tempo real ("Em Processamento" → "Deferido") | Meu INSS exibe despacho fundamentado e disponibiliza Carta de Concessão para download | Notificação bancária (app do banco — fora do ecossistema Meu INSS) |
| — *Linha de Visibilidade* — | | | | | | | |
| **Backstage** *(Tecnologia Ativa — Motor de Regras / Workflow — invisível)* | Motor verifica nível de confiabilidade da conta Gov.br (exige nível mínimo Prata para serviços previdenciários) | Motor verifica elegibilidade do serviço para o perfil da segurada (categoria MEI/Contribuinte Individual) | Motor estrutura *query* com os 3 campos e dispara consulta síncrona ao SIRC via barramento CRC Nacional | Motor de Workflow cria processo no SIBE/Prisma e inicia fluxo de análise automatizada | Motor executa: (1) cruzamento CNIS para verificar qualidade de segurada e tempestividade do DAS-MEI; (2) valida fato gerador confirmado pelo SIRC; (3) aplica regra de isenção de carência da IN 188/2025 | Motor registra decisão de deferimento no SIBE/Prisma e envia ordem de pagamento ao sistema financeiro | Sistema financeiro do INSS processa ordem de pagamento e aciona banco da beneficiária |
| — *Linha de Interação Interna* — | | | | | | | |
| **Processos de Suporte** *(Sistemas de Registro de Retaguarda)* | Base de identidade Gov.br (Ministério da Gestão) | — | **SIRC / CRC Nacional** (registro civil — localiza e valida a Certidão de Nascimento) | **SIBE / Prisma** (sistema de gestão de benefícios do INSS — abertura do processo) | **CNIS** (qualidade de segurada, histórico de contribuições DAS-MEI) + **SIRC** (confirmação do fato gerador) + regras IN 188/2025 parametrizadas no motor | SIBE / Prisma + sistema de emissão de Carta de Concessão + barramento de notificações (e-mail/SMS) | Sistema de pagamentos INSS + banco da beneficiária (convênios BB/CEF) |
| **⚠ Fail Points** | — | — | ⚠ **SIRC indisponível** (164 falhas em 15 meses): consulta ao CRC Nacional falha → fluxo desviado para instrução manual → **→ EXC-1** / ⚠ **Dados divergentes** da certidão (matrícula ou data incorreta): sistema não localiza o registro → "Em Exigência" → **→ EXC-1** | ⚠ **Conta bancária inválida** ou CPF divergente da titular → falha latente na liquidação financeira (Etapa 7) | ⚠ **CNIS desatualizado**: DAS-MEI inaugural pago em atraso → qualidade de segurada não reconhecida → indeferimento automático → **→ EXC-2** / ⚠ **Motor parametrizado com regra pré-IN 188/2025**: exige carência indevida (risco sistêmico de configuração, pós-julho/2025) → **→ EXC-2** | — | ⚠ **Conta bancária encerrada ou dados divergentes**: crédito devolvido → necessidade de atualização cadastral e reprocessamento manual |

---

## Legenda

| Símbolo | Significado |
|---|---|
| ⚠ | Fail point identificado no fluxo |
| → EXC-1 | Porta de saída para Blueprint de Exceção 1: Falha SIRC / Desvio para Análise Manual |
| → EXC-2 | Porta de saída para Blueprint de Exceção 2: Rejeição de Qualidade de Segurada / "Em Exigência" |
| 📄 (pré-jornada) | Input externo pré-serviço — condição de entrada, fora do escopo temporal mapeado |
| *Linha de Interação* | Separa Ações do Cidadão do Frontstage (tecnologia visível) |
| *Linha de Visibilidade* | Separa o visível (Frontstage) do invisível (Backstage algorítmico) |
| *Linha de Interação Interna* | Separa o Backstage (lógica decisora) dos Sistemas de Retaguarda (repositórios/APIs) |

---

## Resumo de Fail Points por Etapa

| Etapa | Fail Point | Causa-Raiz | Impacto Operacional | Desvio |
|---|---|---|---|---|
| 3 | SIRC indisponível | Infraestrutura pública instável (164 falhas/15 meses — dados LAI) | Quebra do fluxo automático; processo migra para fila de análise humana | → EXC-1 |
| 3 | Dados da certidão divergentes | Erro de digitação da segurada ou inconsistência cadastral no CRC | Processo cai em "Em Exigência"; prazo de 30 dias para correção | → EXC-1 |
| 4 | Conta bancária inválida | Conta encerrada ou CPF diferente da titular informada no protocolo | Crédito devolvido na Etapa 7; reprocessamento manual necessário | — (fail latente) |
| 5 | CNIS desatualizado / DAS-MEI inaugural em atraso | Contribuição inaugural fora do prazo não inaugura o vínculo de segurada | Qualidade de segurada rejeitada; indeferimento automático ou exigência | → EXC-2 |
| 5 | Motor com regra pré-IN 188/2025 | Falha de parametrização sistêmica após publicação da norma em jul/2025 | Exigência indevida de carência; indeferimento administrativo incorreto | → EXC-2 |
| 7 | Conta bancária encerrada | Dado cadastral desatualizado não detectado na Etapa 4 | Crédito devolvido; necessidade de atualização e reprocessamento | — |

---

*Referências de base: IN PRES/INSS nº 188/2025; Manual DTI/CGAUT v3 (B80 Urbano, 05/2025); BEPS Vol. 31 Nº 01 (Jan/2026); Portal da Transparência Previdenciária (Mar/2026); Relatório LAI — meutudo.com.br (Jun/2024); Portal Gov.br — Força-tarefa MAES (Mai/2026); Acórdão 1606/2025-TCU-Plenário.*
