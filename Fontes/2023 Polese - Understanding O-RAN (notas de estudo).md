---
título: "Understanding O-RAN — notas de estudo"
fonte: "[[2023 Polese - Understanding O-RAN (tradução)]]"
tipo: notas de estudo (fase 1)
status: em andamento — cobre Resumo, Introdução e Seções II a V (seções seguintes ainda não traduzidas)
revisado: 2026-10-16
---

# Notas de estudo — Understanding O-RAN (Polese et al., 2023)

*Material de apoio à tradução — não faz parte do texto original do paper. Atualizado conforme novas seções são traduzidas e revisadas.*

## Ideias-chave

**Resumo + Introdução**

- **Problema:** a RAN atual é monolítica e fechada ("caixa-preta") → (i) reconfigurabilidade limitada, (ii) coordenação limitada entre nós, (iii) [[Vendor lock-in|vendor lock-in]].
- **Solução (Open RAN):** desagregação + virtualização + interfaces abertas → multivendor, otimização programática e [[Closed-loop control|controle em malha fechada]] orientado por dados.
- **Mecanismo (O-RAN):** divisão funcional 7.2 do 3GPP NR (**CU + DU + RU**) e dois [[RIC|RICs]] — quase real (10 ms a 1 s) e não real (acima de 1 s) — conectados por interfaces abertas com telemetria e controle.
- **Promessa central:** viabilizar aplicações de IA/ML para otimizar e controlar a RAN.
- **Este artigo:** tutorial completo — especificações, arquitetura, interfaces, fluxos de trabalho de ML, segurança, plataformas experimentais e agenda de pesquisa.

**Seção II — Princípios fundamentais da arquitetura O-RAN**

- **Origem:** o xRAN Forum (fronthaul padronizado + interfaces abertas para controladores externos) e a C-RAN Alliance (banda base centralizada em nuvem virtualizada) se unem em 2018 para criar a [[O-RAN Alliance]].
- **Quatro princípios do Open RAN:** desagregação; controle inteligente orientado por dados (RICs); virtualização; interfaces abertas.
- **Desagregação (split 7.2x):** gNB → O-CU (camadas RRC, SDAP, PDCP; dividida em CP e UP) + O-DU (RLC, MAC, PHY-alta) + O-RU (PHY-baixa e RF) — ponto escolhido para equilibrar uma RU barata com os requisitos de taxa e latência do fronthaul.
- **Malhas de controle:** non-RT RIC (acima de 1 s; parte do SMO; envia diretrizes e modelos de ML) · near-RT RIC (10 ms a 1 s; na borda; roda **xApps**) · tempo real (abaixo de 10 ms; ainda "para estudo futuro").
- **xApp:** microsserviço com lógica própria no near-RT RIC — recebe KPMs (telemetria) da RAN e devolve ações de controle.
- **Interfaces:** E2 (near-RT RIC ↔ nós da RAN), A1 (non-RT → near-RT RIC), O1 (gestão/orquestração), O2 (SMO ↔ O-Cloud), Open Fronthaul (O-DU ↔ O-RU) — é isso que rompe o vendor lock-in e viabiliza o multivendor.

**Seção III — Near-RT RIC, xApps e controle da RAN**

- **Papel:** o near-RT RIC é o núcleo do controle/otimização da RAN, graças à interface E2; hospeda as terminações O1, A1 e E2, as xApps e os componentes para executá-las e gerenciá-las.
- **Componentes internos:** mensageria interna (registro/descoberta/exclusão de *endpoints* e roteamento; no OSC, a biblioteca RMR) · **mitigação de conflitos** (diretos via resolução *pré-ação*; indiretos/implícitos via verificação *pós-ação* — **ainda ausente** no near-RT RIC da OSC) · gerenciador de assinaturas (consolida pedidos idênticos ao mesmo nó E2) · subsistema de segurança (impede xApps maliciosas; detalhes para estudos futuros) · banco NIB (R-NIB = nós E2; UE-NIB = UEs e identidades) + API SDL · gerenciamento do ciclo de vida das xApps (FCAPS).
- **xApp:** componente *plug-and-play* com lógica própria; definida por um **descritor** (YAML/JSON: autoscaling, implantação, exclusão, atualização, tipos de dados, capacidades de controle) + **imagem** de software; no OSC, imagem Docker implantada em Kubernetes.
- **Lacuna apontada:** as especificações exigem só um conjunto limitado de APIs; um **SDK** mais amplo permitiria portar xApps entre implementações — esforço do subgrupo RIA do TIP.
- **Implementações de código aberto:** OSC (referência) · **ColO-RAN** (experimentação) · **SD-RAN** (ONF, sobre o controlador ONOS) · **FlexRIC** (RIC monolítico + agente RAN para OpenAirInterface) · **5G-EmPOWER** (RANs heterogêneas).

**Seção IV — Non-RT RIC e estrutura de orquestração (SMO)**

- **SMO:** segundo elemento-chave da arquitetura; cuida de toda a orquestração, gestão e automação para monitorar/controlar a RAN; **hospeda o non-RT RIC**; oferece as interfaces e a coleta de dados que viabilizam o controle por IA/ML. As especificações **não fixam limites rígidos** entre SMO e non-RT RIC — apenas agrupam as funcionalidades em três conjuntos (Figura 6): laranja = ancoradas ao non-RT RIC; verde = ancoradas fora dele (SMO); amarelo = compartilhadas ainda sem componente definido.
- **non-RT RIC:** controle em malha fechada com escalas **acima de 1 s**; hospeda a terminação **R1** e executa **rApps** (aplicações de terceiros) que fornecem diretrizes de política, enriquecimento, configuração e análise. Acessa A1/O1/O2 pela mensageria interna. As rApps podem oferecer os mesmos controles das xApps (handover, escalonamento, tráfego), mas em escalas maiores e definindo **políticas** de alto nível, para mais usuários/nós.
- **Dois serviços de alto nível do non-RT RIC:** (i) **gerenciamento de rede baseado em intenções** — o operador expressa intenções em linguagem de alto nível (YAML/XML); o non-RT RIC as traduz em políticas e no conjunto de rApps/xApps a implantar (a OSC trabalha na *Intent Interface*); (ii) **orquestração inteligente** — o non-RT RIC coordena as aplicações: adequação às intenções, local de instanciação, dados de entrada e robustez contra conflitos.
- **Outras funcionalidades (SMO ou non-RT RIC):** **mensageria interna** (liga componentes e terminações; políticas das rApps chegam ao near-RT RIC via R1 → A1) · **gerenciamento e exposição de dados** (modelo consumidor/produtor: produtores publicam KPMs/previsões de IA; consumidores descobrem e assinam; pré-processamento para IA/ML — correlação, rotulagem, normalização) · **fluxo de trabalho de IA/ML** (supervisão do ciclo completo: coleta, treino, validação, implantação e execução — detalhado na Seção VI).
- **Estrutura do SMO:** também gerencia a O-Cloud (O2) e os componentes O-RAN (O1), procedimentos **FCAPS**, inventário, topologia/configuração e políticas de orquestração. SMOs proprietários existem, mas são fechados → foco em duas soluções abertas: **ONAP** (Linux Foundation; integração com Kubernetes, Akraino, Acumos e OpenDaylight; mais complexo; SMO preferencial do OSC) e **OSM** (hospedado pelo ETSI, baseado em VNFs; mais leve; cooperação ETSI–O-RAN Alliance em maio/2021, integração ainda inicial).

**Seção V — As interfaces abertas da O-RAN**

- **Ideia central:** cada interface O-RAN é um conjunto de *procedimentos* bem definidos, cada um viabilizando um *serviço* por meio de combinações de elementos funcionais e mensagens. Todas as interfaces são abertas e padronizadas.
- **E2 (V-A):** conecta o near-RT RIC aos **nós E2** (O-DU, O-CU, O-eNB; futuramente O-RU). Permite controlar recursos de rádio e coletar métricas (periódicas ou por gatilho) no nível de célula, fatia, classe de QoS ou UE. Divide-se em **E2AP** (protocolo procedimental; roda sobre **SCTP**) e **E2SM** (modelos de serviço). Quatro serviços do E2AP: **Report** (RIC Indication), **Insert**, **Control** e **Policy**. Modelos de serviço: **KPM** (métricas de desempenho), **NI** (interfaces de rede: X2, Xn, F1) e **RC** (controle — domínios de portadora de rádio, alocação de recursos, mobilidade em modo conectado/ocioso, acesso via rádio, DC e CA, além de identificação/relato de UE).
- **E2SM RC:** implementa controle sobre os nós E2 por meio dos serviços E2. As ações de controle/políticas referem-se a parâmetros padronizados pelo 3GPP (o valor de um *Information Element*). Pode especificar **dois tipos de política:** *políticas de controle* (o nó E2 age sem interagir com o RIC quando certas condições ocorrem) e *políticas de offset* (alteram limiares do 3GPP/vendor). O controle pode ser síncrono (precedido por um *E2 Insert* do nó E2) ou assíncrono.
- **O1 (V-B):** interface aberta de operação e manutenção; liga os elementos gerenciados (near-RT RIC, nós da RAN) ao SMO/non-RT RIC. Baseia-se nos **MnS** (*Management Services*): ciclo de vida, *Provisioning*, *Fault Supervision*, *Heartbeat*, *Performance Assurance* (KPIs), *Trace* e gerenciamento de software/arquivos. Usa APIs REST/HTTPS e **NETCONF**; transferência em bloco via **SFTP** (com notificação *file-ready*) e streaming em tempo real via WebSocket. Serve também para enviar/baixar arquivos (atualizações de software, configuração de beamforming das RUs, modelos de ML e certificados).
- **A1 (V-C):** conecta non-RT RIC (ou SMO) e near-RT RIC. Oferece três serviços: **gerenciamento de políticas** (diretrizes de alto nível), **gerenciamento de modelos de ML** (ainda considerado para estudo futuro) e **informações de enriquecimento (EI)**. Baseia-se no protocolo **A1AP** (estrutura do 3GPP de aplicação de políticas para funções de rede: REST sobre HTTP + JSON). Cada política tem identificador único, escopo (UE, grupo, fatia, célula, portadora, classe de aplicação) e uma ou mais *policy statements* (recursos e objetivos).
- **Fronthaul da O-RAN (V-D):** conecta a DU a uma ou mais RUs da mesma gNB; viabiliza o **split 7.2x** (low PHY na RU; high PHY na DU). Quatro planos (funcionalidades): **C-plane** (comandos de escalonamento/beamforming prédio da DU para a RU), **U-plane** (amostras I/Q no domínio da frequência), **S-plane** (sincronização de tempo/frequência/fase) e **M-plane** (gerenciamento da RU; pode funcionar como a O1 da RU; criptografado via SSH/TLS). Transporte sobre Ethernet ou UDP/IP com payload eCPRI ou IEEE 1914.3. **Quatro opções de beamforming:** feixe predefinido, baseado em atributos, baseado em pesos e baseado em informação de canal (CSI).
- **Outras interfaces (V-E):** **O2** (SMO ↔ O-Cloud: gerenciamento e provisionamento programáticos; o WG6 considera padrões ETSI NFV, interfaces de serviço do 3GPP e projetos Kubernetes, OpenStack e ONAP/OSM). Além disso, a arquitetura desagregada reutiliza interfaces 3GPP: **E1** (CP/UP da CU), **F1** (CU ↔ DU), **Xn (X2)** (entre gNBs/eNBs), **Uu** (UE ↔ gNB) e **NG** (gNB ↔ núcleo 5G, via UPF e AMF).

## Figuras

- **Figura 2 — Desagregação do gNB (split 7.2x):** evolução da estação rádio-base "caixa-preta" tradicional (esquerda) para um gNB virtualizado com divisão funcional (direita): CU e DU na nuvem de borda (*edge cloud*) e RU no site celular. RRC, SDAP e PDCP ficam na CU; RLC, MAC e PHY-alta na DU (funções no domínio da frequência, como modulação e *precoding*); PHY-baixa e RF na RU (funções no domínio do tempo, como iFFT/CP e *beamforming*).
- **Figura 3 — Malhas de controle:** cruza o objetivo do controle (políticas → sessões → acesso ao meio → rádio → dispositivo) com a escala (nº de dispositivos), os dados de entrada (KPMs de infraestrutura/CU/MAC → amostras I/Q) e a escala de tempo: não real, acima de 1 s (via A1); quase real, 10–1000 ms (via E2); tempo real, abaixo de 10 ms (apenas "para estudo futuro").
- **Figura 4 — Arquitetura O-RAN completa:** o SMO (que hospeda o non-RT RIC) no topo, conectado via A1 e O1 ao near-RT RIC e aos nós; E2 do near-RT RIC para O-DU, O-CU e O-eNB; O2 do SMO para o O-Cloud; Open Fronthaul (planos CUS e M) entre O-DU e O-RU; e as interfaces 3GPP (E1, F1, X2/Xn, NG) entre CU, DU e núcleo. Interfaces O-RAN em linha sólida; interfaces 3GPP, tracejadas.
- **Figura 5 — Arquitetura do near-RT RIC:** as xApps (azul-escuro) rodam sobre a plataforma; a comunicação entre os componentes (azul-claro) é mediada pela infraestrutura de mensageria interna; no topo ficam as terminações A1 e O1 (para o non-RT RIC/SMO) e embaixo a terminação E2 (para a RAN, em amarelo); a plataforma expõe uma API/SDK para as xApps, além de mitigação de conflitos, gerenciamento de assinaturas, segurança e SDL.
- **Figura 6 — Arquitetura do non-RT RIC e do SMO:** o bloco SMO (verde) conecta-se ao O-Cloud (via O2) e aos demais componentes da RAN (via O1); o non-RT RIC traz lógica própria (rApps, em vermelho) e a terminação A1 para o near-RT RIC (laranja); funcionalidades compartilhadas entre SMO e non-RT RIC aparecem em amarelo.
- **Figura 7 — Pacote E2AP:** mostra um pacote E2AP (com payload E2SM) encapsulado em cabeçalhos SCTP e IP; a parte superior resume os serviços oferecidos pela interface E2.
- **Figura 8 — Procedimentos de *E2 setup* e de E2SM KPM:** diagramas de sequência (8a: *setup* da sessão E2, iniciado pelo nó E2; 8b: *streaming* de KPMs, com assinatura iniciada pela xApp).
- **Figura 9 — *E2 Insert* seguido de *E2 Control*:** exemplo de mensagens trocadas entre nó E2 e xApp num procedimento de controle (inserção do nó E2 e resposta de controle do near-RT RIC).
- **Figura 10 — Interface O1:** payload dos *Management Services* encapsulado em pacotes HTTPS/TCP/IP.
- **Figura 11 — Interface A1:** payload JSON da A1 encapsulado em pacotes HTTPS/TCP/IP; resume os serviços oferecidos pela A1.
- **Figura 12 — Interface de fronthaul e seus planos:** viabiliza o split 7.2x entre DU e RU; dados e controle entre PHY-alta e PHY-baixa trafegam pelos planos C e U, com plano S para sincronização e plano M para gerenciamento.
- **Figura 13 — Fluxo de trabalho de IA/ML na arquitetura O-RAN:** a infraestrutura da RAN fornece dados pelas interfaces O-RAN à coleta e preparação de dados; modelos são treinados, validados, implantados e executados nos nós O-RAN (RICs), com monitoramento e *feedback* para refinamento.

## Siglas

| Sigla | Significado |
| --- | --- |
| RAN | *Radio Access Network* — rede de acesso via rádio |
| O-RAN | *Open RAN* — implementação dos princípios abertos, especificada pela [[O-RAN Alliance]] |
| vRAN | *virtualized RAN* — RAN virtualizada |
| 3GPP | *3rd Generation Partnership Project* — organismo que padroniza LTE (4G) e NR (5G) |
| gNB · eNB | estação rádio-base NR/5G · estação rádio-base LTE/4G |
| CU · DU · RU | Unidade Central · Unidade Distribuída · Unidade de Rádio (divisão funcional 7.2) |
| O-CU · O-DU · O-RU | as mesmas unidades, na nomenclatura O-RAN |
| CP · UP | plano de controle · plano de usuário (a CU divide-se em O-CU-CP e O-CU-UP) |
| SDN | *Software-defined Networking* — redes definidas por software |
| C-RAN | *Cloud RAN* — banda base centralizada em data centers de nuvem |
| RRC · SDAP · PDCP | camadas superiores da pilha 3GPP (na CU) |
| RLC · MAC · PHY | camadas inferiores (na DU; a PHY é dividida entre DU e RU no split 7.2x) |
| FFT · CP | *Fast Fourier Transform* · *Cyclic Prefix* — funções no domínio do tempo (na RU) |
| QoS | *Quality of Service* — qualidade de serviço |
| KPM | *Key Performance Measurement* — medição de desempenho (telemetria da RAN) |
| RIC | *RAN Intelligent Controller* — controlador inteligente da RAN |
| SMO | *Service Management and Orchestration* — estrutura de gerenciamento/orquestração (hospeda o non-RT RIC) |
| xApp | aplicação (microsserviço) com lógica personalizada executada no near-RT RIC |
| rApp | aplicação (microsserviço) com lógica personalizada executada no non-RT RIC |
| R1 | interface que permite às rApps interagirem com o non-RT RIC |
| UE | *User Equipment* — equipamento/dispositivo do usuário |
| O-Cloud | plataforma de nuvem híbrida que hospeda os componentes O-RAN |
| E2 · A1 · O1 · O2 · Open FH | interfaces O-RAN: near-RT RIC ↔ nós · non-RT → near-RT RIC · gestão/orquestração · SMO ↔ O-Cloud · O-DU ↔ O-RU (detalhes na Seção V) |
| E1 · F1 · X2/Xn · NG | interfaces intra-RAN definidas pelo 3GPP |
| URLLC | *Ultra-Reliable and Low-Latency Communications* — caso de uso 5G |
| AAL · FEC | *Acceleration Abstraction Layer* · *Forward Error Correction* — aceleração de hardware |
| IA · ML | Inteligência Artificial · Aprendizado de Máquina |
| CAPEX · OPEX | custos de capital · custos operacionais |
| OSC | *O-RAN Software Community* — comunidade que mantém um near-RT RIC de referência |
| RMR · SDL | *RIC Message Router* (mensageria interna) · *Shared Data Layer* (API de dados compartilhados) |
| NIB · R-NIB · UE-NIB | *Network Information Base* · NIB da RAN (nós E2) · NIB dos UEs e suas identidades |
| FCAPS | Falhas, Configuração, Contabilidade, Desempenho, Segurança (gestão do ciclo de vida) |
| E2SM · SM · SDK | *E2 Service Model* · *Service Model* · *Software Development Kit* |
| ONOS | *Open Networking Operating System* — controlador no qual o SD-RAN se baseia |
| TIP · RIA | *Telecom Infra Project* · subgrupo *RAN Intelligence and Automation* |
| ONAP | *Open Network Automation Platform* — plataforma SMO de código aberto (Linux Foundation) |
| OSM | *Open Source Management and Orchestration* — plataforma SMO de código aberto (ETSI) |
| ETSI | *European Telecommunications Standards Institute* — instituto europeu de padronização |
| VNF | *Virtualized Network Function* — função de rede virtualizada |
| SLA | *Service Level Agreement* — acordo de nível de serviço |
| WG3 | grupo de trabalho da O-RAN Alliance que padroniza os modelos de serviço E2 |
| E2AP · E2 AP | *E2 Application Protocol* — protocolo procedimental básico da interface E2 |
| SCTP | *Stream Control Transmission Protocol* — protocolo de transporte da interface E2 |
| ASN.1 | *Abstract Syntax Notation One* — notação de codificação dos modelos de serviço E2 |
| PRB | *Physical Resource Block* — bloco de recurso físico |
| DC · CA | *Dual Connectivity* · *Carrier Aggregation* — conectividade dupla · agregação de portadoras |
| MnS | *Management Services* — serviços de gerenciamento da interface O1 |
| KPI | *Key Performance Indicator* — indicador-chave de desempenho |
| NETCONF | *Network Configuration Protocol* — protocolo do IETF para gerenciar funções de rede (usado na O1) |
| IETF | *Internet Engineering Task Force* — organismo de padronização da Internet |
| PNF | *Physical Network Function* — função de rede física |
| SFTP | *SSH File Transfer Protocol* — transferência de arquivos usada na O1 |
| VES | *VNF Event Stream* — projeto de formato de eventos de KPI |
| A1AP | *A1 Application Protocol* — protocolo de aplicação da interface A1 |
| JSON | *JavaScript Object Notation* — formato de dados usado nas interfaces A1 e O1 |
| EI | *Enrichment Information* — informação de enriquecimento (serviço da A1) |
| IE | *Information Element* — elemento de informação do 3GPP |
| RSRP | *Reference Signal Received Power* — potência recebida do sinal de referência |
| FH | *Fronthaul* — interface entre O-DU e O-RU (Open Fronthaul) |
| LAA | *Licensed-Assisted Access* — acesso assistido por espectro licenciado |
| OFDM | *Orthogonal Frequency Division Multiplexing* — multiplexação por divisão de frequência ortogonal |
| eCPRI | *enhanced Common Public Radio Interface* — encapsulamento do fronthaul |
| CSI | *Channel State Information* — informação de estado do canal |
| PLFS · PTP | *Physical Layer Frequency Signals* · *Precision Time Protocol* — perfis de sincronização do fronthaul |
| UDP/IP · SSH · TLS · DHCP | protocolos de transporte, criptografia e configuração de rede citados na Seção V |
| NFV | *Network Function Virtualization* — virtualização de funções de rede (ETSI) |
| UPF · AMF | *User Plane Function* · *Access and Mobility Management Function* — funções do núcleo 5G |

## Perguntas de revisão

1. Quais são os três problemas das RANs "caixa-preta" apontados na Introdução?
2. Como a desagregação, a virtualização e as interfaces abertas atacam cada um desses problemas?
3. O que é a divisão funcional 7.2 e quais três unidades ela cria na estação rádio-base?
4. Quais são os dois RICs da arquitetura O-RAN e o que os diferencia (escalas de tempo)?
5. O que este artigo alega trazer de novo em relação à literatura anterior sobre Open RAN?
6. Que duas iniciativas se uniram em 2018 para formar a O-RAN Alliance, e o que cada uma já havia contribuído?
7. Quais são os quatro princípios fundamentais do Open RAN identificados na Seção II?
8. O que cada unidade (O-CU, O-DU, O-RU) implementa no split 7.2x, e por que esse ponto de divisão foi escolhido?
9. Qual a diferença entre o non-RT RIC e o near-RT RIC quanto à escala de tempo, ao papel e ao local na arquitetura?
10. O que é uma xApp e quais quatro serviços o near-RT RIC provê para suportá-las?
11. O que é o O-Cloud? Cite dois benefícios da virtualização mencionados no texto.
12. Qual o papel de cada interface O-RAN: E2, A1, O1, O2 e Open Fronthaul?
13. Por que as malhas de tempo real (abaixo de 10 ms) ainda não fazem parte da arquitetura O-RAN?
14. Quais são os principais componentes internos do near-RT RIC e o que faz cada um?
15. Por que a mitigação de conflitos é necessária, e qual a diferença entre conflitos diretos (resolução *pré-ação*) e indiretos/implícitos (verificação *pós-ação*)?
16. O que define uma xApp segundo as especificações O-RAN (descritor + imagem)?
17. Que lacuna as especificações ainda deixam quanto às APIs oferecidas às xApps, e que benefício um SDK mais amplo traria?
18. Cite três implementações de código aberto de near-RT RIC e uma característica de cada.
19. Por que a identidade do UE (UE-ID) é descrita ao mesmo tempo como fundamental e sensível no RIC?
20. Qual o papel do SMO, e como as especificações grupam as funcionalidades entre SMO e non-RT RIC (os três conjuntos da Figura 6)?
21. O que caracteriza o non-RT RIC (escala de tempo, terminação R1 e serviços) e em que ele difere do near-RT RIC?
22. O que são rApps e por que, podendo oferecer controles equivalentes aos das xApps, foram padronizadas para atuar em um nível mais alto?
23. Explique os dois serviços de alto nível do non-RT RIC: gerenciamento baseado em intenções e orquestração inteligente.
24. Como funciona o gerenciamento e a exposição de dados do SMO/non-RT RIC (modelo consumidor/produtor) e que etapa prepara os dados para IA/ML?
25. Quais são as duas principais soluções de SMO de código aberto, quem as mantém e quais as diferenças entre elas?
26. (Seção V-A) O que é a interface E2, quais endpoints ela conecta e sobre que protocolo de transporte opera?
27. (Seção V-A) Quais são os quatro serviços do E2AP (Report, Insert, Control e Policy) e como eles se combinam nos modelos de serviço E2 (KPM, NI e RC)?
28. (Seção V-A) Como o E2SM RC implementa o controle sobre os nós E2 e quais os dois tipos de política que ele pode especificar?
29. (Seção V-B) O que são os *Management Services* (MnS) da interface O1 e quais protocolos e mecanismos de transferência ela usa?
30. (Seção V-C) Quais serviços a interface A1 oferece e como uma política A1 é estruturada (identificador, escopo e declarações)?
31. (Seção V-D) Quais são os quatro planos do fronthaul da O-RAN e o que cada um faz? Quais as quatro opções de beamforming?
32. (Seção V-D) Por que o split 7.2x foi escolhido e como o fronthaul lida com os requisitos de URLLC?
33. (Seção V-E) O que conecta a interface O2 e quais interfaces 3GPP são reaproveitadas na arquitetura desagregada da O-RAN?

> Dica: responda por escrito usando o template [[Sessão de estudo]] — serve de ficha para revisão espaçada.
