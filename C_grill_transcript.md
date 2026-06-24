# Transcript — Sessão de Grilling: Service Blueprint AS-IS do Salário-Maternidade Urbano

**Data:** 24 de junho de 2026
**Skill utilizada:** `grill-me` (instalada via https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me)
**Artefato produzido:** `C_blueprint_asis.md`

---

## Parte 1 — Instalação da Skill

**Usuário:**
> instale esta skill pra mim
> https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me

**Assistente:**
Obteve o conteúdo da skill via WebFetch. O arquivo `SKILL.md` contém:

```
---
name: grill-me
description: A relentless interview to sharpen a plan or design.
disable-model-invocation: true
---

Run a `/grilling` session.
```

Skill instalada em `~/.claude/skills/grill-me/SKILL.md` (estrutura global, equivalente ao `session-start-hook` existente).

---

## Parte 2 — Invocação da Skill `/grill-me`

**Usuário:**
> Quero elaborar conceitualmente um Service Blueprint AS-IS da jornada "Salário Maternidade Urbano". Considere como contexto o artefato @B_relatorio_assistente_v3.md. Siga a metodologia de Service Blueprint de Shostack: camadas (Evidências Físicas, Ações do Cidadão, Frontstage, Backstage, Processos de Suporte), as linhas divisórias (interação, visibilidade, interação interna) e os fail points.

---

## Parte 3 — Sessão de Grilling (6 Rodadas)

---

### Pergunta 1 — Escopo do Sujeito

**Assistente:**
O relatório deixa claro que "a segurada" não é uma persona única. Ela pode ser uma **empregada CLT** (cujo benefício passa inteiramente pelo empregador via eSocial/DCTFWeb, sem tocar no INSS diretamente), uma **MEI/contribuinte individual** (agora isenta de carência pela IN 188/2025), uma **doméstica**, uma **desempregada em período de graça**, ou uma **adotante** — cada uma com um fluxo radicalmente diferente.

**Quem é a "Cidadã" no eixo horizontal do seu Blueprint?**

Você vai modelar uma única jornada genérica e representativa, ou vai segmentar em múltiplos Blueprints por persona/categoria? Se for uma única, qual critério te leva a escolher essa persona como a mais representante do AS-IS? Se forem múltiplos, qual é o recorte de escopo do *primeiro* que você vai construir?

**Usuário:**
A "Cidadã" focal do primeiro Blueprint será a Microempreendedora Individual (MEI) ou Contribuinte Individual que solicita o Salário-Maternidade Urbano após o parto, portando a certidão de nascimento da criança. Optou-se pela segmentação do ecossistema em múltiplos artefatos, elegendo esta persona para o primeiro mapa por ela materializar a jornada digital direta (G2C) de ponta a ponta no portal Meu INSS, sem a intermediação patronal do eSocial/DCTFWeb que retira as trabalhadoras sob regime CLT das filas da autarquia. Esse recorte expõe com precisão o impacto prático da Instrução Normativa PRES/INSS nº 188/2025, que consolidou administrativamente a isenção de carência para essas categorias. Por fim, mapear a MEI permite registrar graficamente as maiores fricções operacionais da retaguarda, como as quebras de fluxo provocadas por instabilidade de rede na consulta automatizada ao SIRC e os critérios eletrônicos de verificação da qualidade de segurada ativa no CNIS, que exigem o pagamento tempestivo da primeira contribuição do ciclo previdenciário.

---

### Pergunta 2 — Fronteiras Temporais da Jornada

**Assistente:**
O relatório registra que o INSS aceita o requerimento de salário-maternidade **até 5 anos após o fato gerador** — mas também que a segurada pode solicitar o afastamento pré-parto **28 dias antes do nascimento**, via atestado médico.

Você está mapeando a persona **pós-parto com certidão em mãos**, o que já define um recorte. Mas as fronteiras temporais ainda estão abertas em duas pontas:

**Ponta inicial:** O seu Blueprint começa quando a segurada abre o aplicativo/portal Meu INSS para protocolar o pedido? Ou começa antes — na etapa cognitiva em que ela descobre que tem direito (exposição à informação, consulta ao CNIS, regularização de contribuições em atraso)?

**Ponta final:** O mapa encerra no **deferimento automático** (ou despacho de indeferimento)? Ou você vai incluir o ciclo de **recurso administrativo** e o **crédito em conta** como estágios finais da jornada?

Essa delimitação muda radicalmente o que entra nas camadas de Backstage e Processos de Suporte. Me diga onde você planta as duas estacas.

**Usuário:**
O escopo horizontal deste primeiro Blueprint é balizado temporalmente por duas estacas bem definidas: o evento disparador (*triggering event*) inicia-se exatamente no momento em que a segurada MEI ou contribuinte individual realiza o login com as credenciais Gov.br na plataforma Meu INSS e seleciona ativamente o serviço de "Salário-Maternidade Urbano". Na ponta oposta, a jornada se encerra nos desfechos terminais (*endpoints*), divididos de forma mutuamente exclusiva entre a concessão e o efetivo crédito do benefício em conta bancária, acompanhado da disponibilização da Carta de Concessão no painel digital, ou a ciência formal da cidadã sobre o despacho administrativo fundamentado de indeferimento registrado no sistema, o que encerra o ciclo de análise imediata e inicia a contagem do prazo legal de 30 dias para a eventual interposição de recurso.

---

### Pergunta 3 — A Arquitetura das Bifurcações

**Assistente:**
O relatório documenta que a jornada que você delimitou **não é linear** — ela é um grafo de decisão com pelo menos dois nós de bifurcação críticos antes mesmo da análise de mérito:

**Nó 1 (SIRC):** O motor de regras executa a consulta à CRC Nacional. Se o SIRC retorna os dados → fluxo automatizado. Se o SIRC está indisponível ou retorna inconsistência → desvio para "Em Análise" com instrução manual. O relatório registra 164 falhas de conexão em 15 meses.

**Nó 2 (Qualidade de Segurada):** Mesmo com a certidão localizada, o sistema verifica se a primeira contribuição do ciclo foi paga *em dia*. Se sim → prossegue. Se o recolhimento inaugural foi feito com atraso → rejeição da qualidade de segurada, mesmo que a segurada tenha contribuído regularmente depois.

A metodologia de Shostack foi desenhada para jornadas predominantemente lineares com swim lanes horizontais. Bifurcações geram ambiguidade visual e tornam o Blueprint difícil de ler como artefato de diagnóstico.

**Como você pretende representar essas bifurcações dentro da estrutura Shostack?** Você vai:

(a) Mapear apenas o **happy path** e sinalizar os fail points com ícones de raio, deixando os desvios fora do escopo do AS-IS?

(b) Incluir **ramos alternativos** dentro do mesmo artefato, aceitando a complexidade visual?

(c) Adotar uma **notação híbrida** — Blueprint principal para o happy path + um Blueprint complementar de "fluxo de exceção" para os desvios sistêmicos?

E qual a sua justificativa metodológica para a escolha?

**Usuário:**
A opção metodologicamente correta para este design de serviço é a **(c) adoção de uma notação híbrida**, estruturando o Blueprint principal focado exclusivamente no "happy path" de concessão automática e acoplando dois Blueprints complementares de exceção para detalhar os desvios sistêmicos do SIRC e da checagem de qualidade de segurada. A justificativa metodológica ampara-se no princípio do tempo de serviço (*service tempo*) de Lynn Shostack, que exige que o artefato visual seja lido de forma linear, cronológica e estritamente sequencial da esquerda para a direita para manter sua função de diagnóstico gerencial. Tentar forçar múltiplos ramos alternativos de alta complexidade — como as 164 falhas de indisponibilidade do SIRC ou o complexo refino do CNIS para analisar se o pagamento em atraso descaracteriza a qualidade de segurada — dentro do mesmo canvas geraria um efeito visual de "espaguete", quebrando a clareza das linhas de visibilidade e de interação e tornando o documento inutilizável para fins de engenharia de processos. Na prática da notação híbrida, os nós de bifurcação críticos no Blueprint principal são sinalizados como "portas de saída" ou conectores de falha, que pausam a cronologia do fluxo feliz e remetem o analista de processos aos mapas de exceção secundários, onde as raias de bastidores, as ações humanas de triagem e os estados da fase "Em Exigência" são detalhados com o devido rigor técnico.

---

### Pergunta 4 — O Colapso das Linhas Divisórias na Automação

**Assistente:**
Shostack concebeu as três linhas divisórias — **interação**, **visibilidade** e **interação interna** — pressupondo que os atores acima e abaixo delas são *humanos*: o cliente interage com um atendente frontstage, que é apoiado por colegas backstage, que usam sistemas de suporte.

No happy path que você delimitou, isso colapsa. A segurada MEI interage com uma **interface digital**. A validação é executada por um **motor de regras**. A consulta ao SIRC é feita por um **daemon de integração**. O deferimento é expedido por **inteligência artificial**. Em nenhum momento da concessão automática um ser humano aparece acima da linha de interação interna.

Isso gera uma tensão metodológica concreta:

- A **linha de visibilidade** separa o que a cidadã vê do que ela não vê. Mas no Meu INSS, ela *vê* atualizações de status em tempo real ("Processando", "Aguardando retorno do SIRC", "Deferido") — ou seja, partes do Backstage vazam para o Frontstage via interface.

- A **linha de interação interna** pressupõe que o Backstage aciona os Processos de Suporte. Mas aqui o "Backstage" *é* o sistema — CNIS, SIRC, motor de regras — e os "Processos de Suporte" são a infraestrutura de TI que sustenta esses mesmos sistemas.

**Como você vai operacionalizar as três linhas divisórias num serviço onde o ator principal de entrega é algorítmico?** Você vai manter a nomenclatura original de Shostack mapeando sistemas como se fossem atores humanos nas raias? Vai renomear as camadas para refletir a realidade digital? Ou vai adotar um framework complementar — como o Digital Service Blueprint ou a adaptação de Clatworthy — para resolver essa tensão?

**Usuário:**
A estratégia ideal é adotar o framework do **Digital Service Blueprint**, adaptando as camadas clássicas de Shostack para a entrega algorítmica sem descaracterizar a estrutura original do artefato. Sob essa ótica, a **Linha de Visibilidade** permanece intacta: a interface visual do Meu INSS continua posicionada na camada de tecnologia de linha de frente (*Onstage*), enquanto as atualizações de status que "vazam" (como "Aguardando retorno do SIRC" ou "Em Análise") são representadas graficamente como *evidências digitais geradas pelo sistema* que cruzam a linha de visibilidade de baixo para cima para dar transparência à cidadã. Logo abaixo dela, a camada de **Ações de Bastidores (*Backstage*)** deixa de abrigar funcionários e passa a ser composta pelas raias de **Tecnologia Ativa**, onde se aloca o motor de regras do Workflow, o qual atua como o agente decisor e orquestrador lógico do processo. Por fim, a **Linha de Interação Interna** separa esse motor ativo dos **Processos de Suporte**, os quais são redefinidos e renomeados como **Sistemas de Registro de Retaguarda** — raias estáticas que representam os repositórios oficiais e barramentos externos de dados (CNIS, SIRC e eSocial) —, garantindo que o Backstage represente a lógica algorítmica de tomada de decisão e o Suporte represente as bases de dados e APIs integradas.

---

### Pergunta 5 — A Camada de Evidências Físicas num Serviço Integralmente Digital

**Assistente:**
Na taxonomia de Shostack, a camada de **Evidências Físicas** ocupa o topo do Blueprint e registra todos os artefatos tangíveis que a cidadã percebe como prova da existência e qualidade do serviço — o ambiente, os documentos, os objetos. Historicamente: o guichê, o formulário em papel, o carimbo, a carta enviada pelos Correios.

Na jornada que você delimitou — integralmente digital, de login a crédito bancário — essa camada exige redefinição. Mas há uma armadilha: designers de serviços digitais frequentemente colapsam "Evidências Físicas" em capturas de tela da interface, o que confunde *canal de entrega* (o Meu INSS) com *evidência percebida* (o artefato que a cidadã retém ou consulta como prova).

Considere que nesta jornada coexistem evidências de naturezas radicalmente distintas:

- O **comprovante de pagamento do DAS-MEI** — artefato gerado *antes* da jornada mapeada, mas que constitui pré-condição de qualidade de segurada
- A **tela de protocolo com número NB** — evidência digital efêmera, que some se a cidadã não captura ou anota
- A **notificação push/SMS** — evidência assíncrona, fora da plataforma
- A **Carta de Concessão em PDF** — documento persistente no painel
- O **extrato bancário com o crédito** — evidência física que migra do mundo digital para o mundo analógico da conta bancária

**Qual o seu critério de curadoria para essa camada?** O que entra e o que fica de fora? E como você vai representar o DAS-MEI — que é pré-condição da jornada mas não está dentro das fronteiras temporais que você estabeleceu na Pergunta 2?

**Usuário:**
O critério de curadoria para a camada de Evidências Físicas em uma jornada digital deve focar exclusivamente em artefatos persistentes de valor comprobatório e transacional que a cidadã retém para si, diferenciando o canal de entrega das provas de conclusão de etapas. Dessa forma, entram no topo do Blueprint o recibo de protocolo eletrônico com o número de benefício (NB) gerado pós-envio, os alertas de progresso assíncronos recebidos por e-mail ou SMS, a Carta de Concessão em PDF homologada no sistema e o extrato bancário consolidado com o depósito, deixando de fora capturas das telas intermediárias de digitação do Meu INSS. Para representar o comprovante de pagamento do DAS-MEI — que é uma pré-condição previdenciária fundamental, mas gerada em momento anterior ao do fato gerador —, a solução no framework é alocá-lo na seção de "Pré-Serviço" ou "Antecedentes", localizada na margem esquerda antes da estaca de tempo inicial. Ele deve ser desenhado como uma caixa de input externo, conectando-se por meio de uma linha vertical pontilhada diretamente ao motor de processamento do CNIS na camada de bastidores, sinalizando visualmente que o histórico de adimplência da MEI é o insumo estático que valida o início do fluxo feliz no backstage previdenciário.

---

### Pergunta 6 — A Crise Epistemológica do AS-IS

**Assistente:**
Você está desenhando um Blueprint **AS-IS** — o estado atual do serviço. Mas o relatório que fundamenta este trabalho expõe uma tensão que pode invalidar o artefato na raiz: **o processo prescrito e o processo real são objetos distintos**, e frequentemente divergentes.

Do lado prescrito, você tem a IN PRES/INSS nº 188/2025, o Manual de Fluxo Operacional DTI/CGAUT v3 e as páginas de serviço do Meu INSS — documentos que descrevem o fluxo *como foi desenhado para funcionar*.

Do lado real, você tem: 164 falhas de conexão do SIRC em 15 meses; 61.616 requerimentos com prazo estourado que exigiram uma força-tarefa nacional em maio de 2026; e o fato de que a IN 188/2025 foi publicada em julho de 2025, mas o sistema ainda acumulava estoque crítico 10 meses depois — o que sugere que o motor de regras, os analistas humanos ou ambos ainda não operavam plenamente sob a nova norma em todos os fluxos.

Um Blueprint AS-IS que documente apenas o processo prescrito é, na prática, um Blueprint **TO-BE disfarçado** — mapeia como a autarquia *quer* que a jornada funcione, não como ela *de fato* funciona para a MEI que protocola hoje.

**Qual será a sua fonte de verdade?**

Como você vai operacionalizar a coleta do processo real para validar (ou contradizer) o processo prescrito nos manuais? Você vai triangular dados do Portal da Transparência, do LAI e do BEPS? Vai conduzir shadowing com servidores ou entrevistas com seguradas? Ou vai assumir explicitamente que este Blueprint é uma representação do processo *normativo* e sinalizar esse limite de escopo no próprio artefato — e se sim, como?

**Usuário:**
A fonte de verdade para a construção deste Blueprint AS-IS não será unicamente a documentação normativa e prescrita, mas sim a colisão entre ela e os dados de comportamento real do sistema. Para que o artefato não se torne um modelo idealizado disfarçado, a engrenagem prescrita pelos manuais e pela Instrução Normativa 188/2025 será adotada apenas como o esqueleto estrutural do fluxo feliz, enquanto o processo real será mapeado sobre essa estrutura a partir da triangulação rigorosa de dados empíricos. Operacionalizaremos essa validação cruzando os registros de indisponibilidade de sistemas obtidos via Lei de Acesso à Informação, os tempos médios de concessão reais do Boletim Estatístico da Previdência Social e do Portal da Transparência, e o volume de acúmulo histórico evidenciado pelas métricas da força-tarefa especial de análise. Esses dados quantitativos serão consolidados no artefato por meio da representação explícita de ramos de desvio e gargalos sistêmicos de retaguarda, transformando o Blueprint em um mapa dinâmico de falhas. Assim, em vez de assumir o limite de um modelo puramente normativo, o documento registrará de forma realista as quebras de fluxo provocadas pela instabilidade crônica da infraestrutura e pela demora na parametrização do motor de regras, posicionando essas barreiras diretamente na camada de bastidores e evidenciando a real experiência transacional da cidadã.

---

### Síntese do Grilling — Decisões Cristalizadas

**Assistente:**
Grilling encerrado. O plano conceitual do Blueprint AS-IS sobreviveu a seis rodadas de interrogação e saiu mais robusto em todas as dimensões críticas:

| Dimensão | Decisão Consolidada |
|---|---|
| **Persona focal** | MEI / Contribuinte Individual, pós-parto, portando certidão, jornada G2C direta no Meu INSS |
| **Fronteiras temporais** | Login Gov.br → crédito em conta + Carta de Concessão **ou** ciência formal do indeferimento |
| **Arquitetura de bifurcações** | Notação híbrida: happy path principal + 2 Blueprints de exceção (falha SIRC; rejeição de qualidade de segurada) |
| **Adaptação das camadas** | Digital Service Blueprint: Frontstage = interface Meu INSS; Backstage = "Tecnologia Ativa" (motor de regras/Workflow); Suporte = "Sistemas de Registro de Retaguarda" (CNIS, SIRC, eSocial) |
| **Evidências Físicas** | Apenas artefatos persistentes comprobatórios; DAS-MEI alocado como input externo pré-jornada com linha pontilhada ao CNIS |
| **Fonte de verdade** | Triangulação: IN 188/2025 + Manual DTI/CGAUT v3 como esqueleto normativo; LAI + BEPS + Portal da Transparência + dados MAES como camada de realidade empírica sobreposta |

**Vulnerabilidade residual identificada:** o eixo horizontal do Blueprint precisaria de uma convenção explícita de tempo dada a variância extrema entre o happy path automatizado (segundos) e o crédito bancário (dias).

---

### Rodada Adicional — Convenção de Tempo no Eixo Horizontal

**Assistente:**
Uma vulnerabilidade não resolvida que você deve atacar antes de começar a desenhar: o eixo horizontal do Blueprint precisa de uma convenção explícita de tempo. O happy path automatizado transcorre em segundos; o crédito bancário pode levar dias. Se você usar escala linear, o artefato fica ilegível. Considere adotar **fases lógicas** (não proporcionais ao tempo real) com anotações de duração típica em cada transição — é o que permite o *service tempo* de Shostack funcionar num fluxo com essa variância temporal extrema.

**Usuário:**
Para solucionar a extrema distorção de tempo do eixo horizontal, o Blueprint adotará uma escala não linear baseada em quatro fases lógicas sucessivas, em vez de uma escala cronológica física proporcional. Na margem do artefato, cada transição entre colunas receberá anotações específicas com a duração média estimada daquela etapa. A jornada se inicia com a Fase de Solicitação, que transcorre em tempo de tela de cerca de dez minutos, compreendendo o login pelo Gov.br e a inserção dos dados cadastrais e da matrícula da certidão de nascimento. Em seguida, o fluxo migra imediatamente para a Fase de Validação e Orquestração no Backstage, que transcorre em tempo quase real de poucos segundos, onde o motor de regras realiza as consultas automáticas e síncronas ao barramento do SIRC e às bases do CNIS. A terceira etapa é a Fase de Decisão Administrativa, que no fluxo feliz de concessão automática é homologada e exibida na tela do Meu INSS em questão de minutos, mas que no fluxo de desvio ou análise humana pode se estender por semanas. Por fim, o processo entra na Fase de Liquidação Financeira, cuja transição para o depósito do crédito na conta bancária e geração da carta de concessão consome de alguns dias até a média nacional de vinte e cinco dias. Essa convenção de fases lógicas com marcações temporais relativas nas transições preserva a legibilidade do Service Tempo de Shostack, impedindo que as etapas instantâneas de milissegundos do sistema fiquem visualmente invisíveis e que os dias de espera bancária estiquem o mapa horizontalmente de forma infinita.

---

## Parte 4 — Produção do Blueprint

**Usuário:**
> Produza o Blueprint AS-IS em C_blueprint_asis.md usando o formato de tabela. Mapeie ≥ 5 etapas na ótica do cidadão, com as cinco camadas e marcando os fail points.

**Assistente:**
Criado o arquivo `C_blueprint_asis.md` com:

- **7 etapas** da jornada da MEI (Login Gov.br → Crédito em conta)
- **4 fases lógicas** com anotações de tempo (Solicitação ~10min / Validação ~segundos / Decisão ~minutos / Liquidação até 25 dias)
- **5 camadas** adaptadas para serviço digital
- **3 linhas divisórias** de Shostack
- **6 fail points** catalogados com causa-raiz, impacto e portas de saída (→ EXC-1 e → EXC-2)
- **Tabela de resumo** de fail points separada

---

*Fim do transcript. Artefato gerado: `C_blueprint_asis.md`. Commit na branch `claude/pensive-rubin-lg8uk9`.*
