---
título: "Understanding O-RAN — notas de estudo"
fonte: "[[2023 Polese - Understanding O-RAN (tradução)]]"
tipo: notas de estudo (fase 1)
status: em andamento — cobre Resumo, Introdução e Seção II
revisado: 2026-10-07
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

## Figuras

- **Figura 2 — Desagregação do gNB (split 7.2x):** evolução da estação rádio-base "caixa-preta" tradicional (esquerda) para um gNB virtualizado com divisão funcional (direita): CU e DU na nuvem de borda (*edge cloud*) e RU no site celular. RRC, SDAP e PDCP ficam na CU; RLC, MAC e PHY-alta na DU (funções no domínio da frequência, como modulação e *precoding*); PHY-baixa e RF na RU (funções no domínio do tempo, como iFFT/CP e *beamforming*).
- **Figura 3 — Malhas de controle:** cruza o objetivo do controle (políticas → sessões → acesso ao meio → rádio → dispositivo) com a escala (nº de dispositivos), os dados de entrada (KPMs de infraestrutura/CU/MAC → amostras I/Q) e a escala de tempo: não real, acima de 1 s (via A1); quase real, 10–1000 ms (via E2); tempo real, abaixo de 10 ms (apenas "para estudo futuro").
- **Figura 4 — Arquitetura O-RAN completa:** o SMO (que hospeda o non-RT RIC) no topo, conectado via A1 e O1 ao near-RT RIC e aos nós; E2 do near-RT RIC para O-DU, O-CU e O-eNB; O2 do SMO para o O-Cloud; Open Fronthaul (planos CUS e M) entre O-DU e O-RU; e as interfaces 3GPP (E1, F1, X2/Xn, NG) entre CU, DU e núcleo. Interfaces O-RAN em linha sólida; interfaces 3GPP, tracejadas.

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
| UE | *User Equipment* — equipamento/dispositivo do usuário |
| O-Cloud | plataforma de nuvem híbrida que hospeda os componentes O-RAN |
| E2 · A1 · O1 · O2 · Open FH | interfaces O-RAN: near-RT RIC ↔ nós · non-RT → near-RT RIC · gestão/orquestração · SMO ↔ O-Cloud · O-DU ↔ O-RU (detalhes na Seção V) |
| E1 · F1 · X2/Xn · NG | interfaces intra-RAN definidas pelo 3GPP |
| URLLC | *Ultra-Reliable and Low-Latency Communications* — caso de uso 5G |
| AAL · FEC | *Acceleration Abstraction Layer* · *Forward Error Correction* — aceleração de hardware |
| IA · ML | Inteligência Artificial · Aprendizado de Máquina |
| CAPEX · OPEX | custos de capital · custos operacionais |

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

> Dica: responda por escrito usando o template [[Sessão de estudo]] — serve de ficha para revisão espaçada.
