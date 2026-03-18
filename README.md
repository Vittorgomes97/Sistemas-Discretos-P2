# Segundo Projeto SED

**Sistemas Discretos – Projeto da disciplina de Sistemas Discretos – UFCG – Período 25.2**

---

# Descrição Detalhada do Projeto – Célula de Manufatura com Robô Compartilhado

---

# 1. Visão Geral do Sistema

Este projeto tem como objetivo proporcionar uma experiência prática na **modelagem e análise de Sistemas a Eventos Discretos (SED)** utilizando **Redes de Petri Coloridas Hierárquicas (HCPN)**.

As **HCPN (Hierarchical Colored Petri Nets)** são uma extensão das Redes de Petri tradicionais que incorporam dois conceitos principais:

- **Cores (tokens com dados associados)**, permitindo representar diferentes tipos de informação dentro do modelo.
- **Hierarquia**, possibilitando dividir sistemas complexos em módulos menores e reutilizáveis.

A ferramenta utilizada para o desenvolvimento e análise do modelo é o **CPN Tools**, que permite a criação, simulação e verificação formal de modelos baseados em Redes de Petri Coloridas Hierárquicas.

O sistema estudado consiste em **três células de manufatura automatizadas**, cada uma composta por:

- duas máquinas de processamento independentes (**M1 e M2**)
- um **robô industrial responsável pelo transporte interno**
- um **buffer de saída com capacidade limitada**

Além disso, existe **um robô adicional responsável por transportar as peças produzidas pelas células até o depósito final da fábrica**, bem como **uma esteira de saída central**.

O objetivo principal do sistema é **transformar matéria-prima bruta em peças acabadas**, depositando-as inicialmente nos buffers das células e posteriormente na esteira de saída da fábrica.

O principal desafio do projeto é **coordenar corretamente o uso dos robôs e gerenciar o fluxo de produção**, evitando situações como:

- transbordamento de buffers (**overflow**)
- bloqueio das máquinas (**deadlock**)
- conflitos de transporte
- operações inválidas

---

# 2. Detalhamento dos Componentes do Sistema

A seguir são descritos o funcionamento físico e lógico dos componentes presentes na célula de manufatura.

---

# 2.1 Estrutura Geral das Células de Manufatura

O sistema completo é composto por **três células de manufatura idênticas**, operando em paralelo.

Cada célula contém:

- **Máquina 1 (M1)**
- **Máquina 2 (M2)**
- **Robô da célula**
- **Buffer de saída da célula**

Essas células operam de forma **independente no processamento**, mas compartilham o sistema final de transporte para o depósito da fábrica.

---

# 2.2 Máquinas de Processamento (M1 e M2)

As máquinas são responsáveis pelo **processamento das peças brutas**, transformando-as em produtos acabados.

As duas máquinas possuem funcionamento **idêntico**, porém operam de forma **independente**, podendo terminar seus ciclos em tempos diferentes.

### Funcionamento

Cada máquina inicia no **estado de repouso (Idle)**.

Quando recebe um **comando de início**, a máquina inicia o processamento da peça. Assume-se que **a matéria-prima está sempre disponível**.

O processamento ocorre durante um **tempo indeterminado**.

Ao finalizar o processamento, a máquina entra no estado **Peça Pronta (Done)** e aguarda a retirada da peça pelo robô da célula.

### Restrições Físicas

- A máquina **não pode iniciar um novo processamento** enquanto a peça anterior não for removida.
- A peça **não pode ser retirada antes do término do processamento**.
- Enquanto estiver no estado **Done**, a máquina permanece bloqueada aguardando a ação do robô.

---

# 2.3 Robô Industrial da Célula

Cada célula possui **um robô próprio**, responsável pelo transporte interno entre as máquinas e o buffer.

O robô funciona como **o agente central de movimentação dentro da célula**.

### Funcionamento

O robô inicia em um **estado livre (Free)**, sem carregar nenhuma peça.

Ele pode deslocar-se até:

- **Máquina 1 (M1)**
- **Máquina 2 (M2)**

Caso a máquina esteja no estado **Peça Pronta**, o robô pode coletar a peça.

Após coletar a peça, o robô a transporta até o **buffer da célula**, onde realiza a deposição.

Após a deposição, o robô retorna ao estado **livre**, podendo iniciar um novo ciclo de transporte.

### Restrições Físicas

**Capacidade unitária**

- o robô **só pode transportar uma peça por vez**

**Sequenciamento**

- não pode coletar uma peça se já estiver carregando outra
- não pode depositar uma peça se não estiver carregando

**Sincronização**

- a coleta só pode ocorrer quando a máquina estiver no estado **Done**

---

# 2.4 Buffer de Saída da Célula

O **buffer da célula** funciona como um espaço temporário de armazenamento para as peças já processadas.

### Funcionamento

As peças transportadas pelo robô da célula são depositadas no buffer.

Elas permanecem ali até serem coletadas pelo **robô de transporte da fábrica**.

### Restrições Físicas

**Capacidade limitada**

Cada buffer possui **capacidade máxima de 2 peças**.

**Transbordamento (Overflow)**

Não é permitido depositar uma peça se o buffer já estiver cheio.

Essa limitação foi definida propositalmente para **evidenciar situações de bloqueio no sistema**.

---

# 2.5 Robô de Transporte da Fábrica

Além dos robôs internos das células, existe **um robô adicional responsável por transportar as peças produzidas até o depósito final da fábrica**.

Esse robô coleta peças dos **buffers das células** e as transporta para a **esteira de saída da fábrica**.

### Funcionamento

O robô inicia em estado **livre**, sem carga.

Ele pode deslocar-se até qualquer **buffer de célula** que possua peças disponíveis.

Após a coleta, transporta as peças até a **esteira de saída da fábrica**.

### Restrições Físicas

**Capacidade ampliada**

O robô pode transportar **até duas peças por vez**.

**Sequenciamento**

Não pode coletar novas peças se já estiver com a carga máxima.

**Sincronização**

Só pode coletar peças se houver itens disponíveis no buffer da célula.

---

# 2.6 Esteira de Saída da Fábrica

A esteira de saída representa o **depósito final das peças produzidas**.

### Funcionamento

As peças transportadas pelo robô da fábrica são depositadas nesta esteira.

Posteriormente, elas podem ser removidas por:

- operadores
- sistemas automatizados
- transporte logístico da fábrica

### Restrições Físicas

**Capacidade limitada**

A esteira possui **capacidade máxima de 4 peças**.

**Overflow**

Não é permitido depositar novas peças quando a esteira estiver cheia.

---

# 3. Fluxo Operacional do Sistema

O fluxo nominal de operação ocorre da seguinte forma:

1. As máquinas das células recebem comandos de início.
2. As máquinas processam as peças.
3. Ao finalizar o processamento, sinalizam que a peça está pronta.
4. O robô da célula coleta a peça da máquina.
5. A peça é transportada até o **buffer da célula**.
6. O robô deposita a peça no buffer.
7. O **robô de transporte da fábrica** coleta peças dos buffers das células.
8. As peças são levadas até a **esteira de saída da fábrica**.

Esse processo ocorre continuamente enquanto houver produção.

---

# 4. Problemas Potenciais do Sistema

Sem um mecanismo de controle adequado, o sistema pode apresentar falhas críticas.

---

## 4.1 Conflito de Recursos

Situação em que múltiplos robôs tentam acessar simultaneamente o mesmo recurso, como buffers ou máquinas.

---

## 4.2 Transbordamento de Buffer (Overflow)

Se o robô tentar depositar uma peça em um buffer cheio, ocorre uma violação da capacidade física do sistema.

---

## 4.3 Bloqueio do Sistema (Deadlock)

Um possível cenário de deadlock ocorre quando:

- os buffers das células estão cheios
- as máquinas concluíram o processamento
- os robôs não conseguem depositar novas peças

Nesse caso, **nenhum componente consegue avançar**, interrompendo a produção.

---

## 4.4 Operações Inválidas

Também podem ocorrer erros como:

- tentativa de coleta de uma máquina ainda em processamento
- tentativa de depósito sem carga no robô
- tentativa de transporte acima da capacidade permitida

---

# 5. Modelagem do Sistema

O comportamento do sistema é modelado utilizando **Redes de Petri Coloridas Hierárquicas (HCPN)**.

Essa abordagem permite representar:

- estados dos componentes
- eventos controláveis e não controláveis
- sincronização entre processos
- restrições físicas do sistema

A estrutura hierárquica possibilita representar cada **célula de manufatura como um módulo reutilizável**, facilitando a expansão do sistema para múltiplas células.

---

# 6. Desenvolvimento do Modelo (HCPN no CPN Tools)

Nesta seção é apresentado o desenvolvimento prático do sistema utilizando **Redes de Petri Coloridas Hierárquicas (HCPN)** na ferramenta **CPN Tools**. A modelagem foi estruturada de forma hierárquica, permitindo representar cada célula como um módulo independente e reutilizável, além de um nível superior responsável pela integração do sistema completo.

---

## 6.1 Modelo de uma Célula de Manufatura

A Figura abaixo representa o modelo interno de uma célula de manufatura. Lembrando que são 3 células

<div align="center">
  <img src="https://github.com/user-attachments/assets/1bc7453d-66d2-4bd9-85c3-0b6a391403a9" width="800px" />
</div>

Cada célula é composta por:

- Dois fluxos de processamento independentes (**M1 e M2**)
- Estados representando as etapas: `idle`, `processing` e `done`
- Transições de início (`Start`) e finalização (`finish`)
- Ações de coleta (`Pick_M1` e `Pick_M2`)
- Um robô responsável pelo transporte (`Rob_f`)
- Um buffer com controle de capacidade (`Buf_free`)

O fluxo ocorre da seguinte forma:

1. A máquina inicia no estado `idle`.
2. Ao receber o comando `Start`, passa para `processing`.
3. Após o evento `finish`, a peça fica disponível em `done`.
4. O robô realiza a coleta (`Pick`), transporta e deposita no buffer (`drop_buf`).
5. O buffer controla a ocupação através do lugar `Buf_free`.

A presença de marcações (tokens) nos lugares indica o estado atual do sistema, permitindo simular o comportamento dinâmico da célula.

---

## 6.2 Modelo do Sistema de Transporte entre Células

A próxima figura representa o modelo responsável pela integração das três células e pelo transporte das peças até o buffer final da fábrica.

<div align="center">
  <img src="https://github.com/user-attachments/assets/b8fb3450-52bc-4d96-8c06-e8c7248b1083" width="800px" />
</div>

Nesse modelo, observa-se:

- Entradas das três células (`in_c1`, `in_c2`, `in_c3`)
- Um robô compartilhado (`F_rob_f`) responsável pela coleta
- Transições de coleta (`pick1`, `pick2`, `pick3`)
- Estados intermediários de transporte (`F_lo1`, `F_lo2_A`, `F_lo2_B`)
- Controle de capacidade do buffer final (`F_buf_f`)

O robô da fábrica pode:

- Coletar peças de diferentes células
- Transportar múltiplas peças simultaneamente (até o limite definido)
- Depositar no buffer final através das transições `drop1` e `drop2`

A modelagem com cores permite diferenciar as peças provenientes de cada célula, garantindo rastreabilidade dentro do sistema.

---

## 6.3 Modelo Hierárquico do Sistema Completo

A figura a seguir apresenta o nível superior do modelo, onde as três células são integradas.

<div align="center">
  <img src="https://github.com/user-attachments/assets/cbcc31b8-8381-4a31-98ac-baee4af16384" width="800px" />
</div>


Neste nível, cada célula é representada como um **módulo hierárquico**:

- `Cell1`
- `Cell2`
- `Cell3`

As saídas de cada célula (`Cel_out1`, `Cel_out2`, `Cel_out3`) são conectadas ao sistema de saída da fábrica (`Fact_Out`).

Essa abordagem permite:

- Reutilização do modelo de célula
- Redução da complexidade visual
- Melhor organização do sistema

---

## 6.4 Considerações sobre a Modelagem

Durante o desenvolvimento do modelo, foram considerados diversos aspectos importantes:

- **Controle de capacidade dos buffers**, evitando overflow
- **Sincronização entre máquinas e robôs**
- **Restrição de capacidade dos robôs (1 peça na célula e até 2 no sistema global)**
- **Modelagem modular utilizando hierarquia**
- **Uso de cores para diferenciar tokens (peças)**

Além disso, a modelagem permite simular cenários críticos como:

- Bloqueio por buffers cheios
- Espera por disponibilidade de robôs
- Concorrência entre células

## 7. Vídeo Demonstrativo do Funcionamento

Com o objetivo de complementar a descrição teórica e a modelagem apresentada ao longo do projeto, foi desenvolvido um **vídeo demonstrativo** que ilustra o funcionamento da célula de manufatura automatizada em operação.

🔗 **Acesse o vídeo demonstrativo:** https://www.youtube.com/watch?v=rdBp-LoTi5Y 


# 8. Conclusão

Este projeto apresentou a modelagem de um **sistema de manufatura automatizado composto por três células de produção**, cada uma contendo duas máquinas de processamento, um robô de transporte interno e um buffer de saída.

Além disso, foi considerado um **robô adicional responsável pelo transporte das peças produzidas até o depósito final da fábrica**, bem como uma **esteira de saída central com capacidade limitada**.

A utilização de **Redes de Petri Coloridas Hierárquicas** permitiu representar o sistema de forma modular e estruturada, possibilitando analisar o comportamento do sistema e identificar situações críticas como **deadlocks, overflow e conflitos de recursos**.

A modelagem evidencia a importância de mecanismos adequados de controle e coordenação para garantir que o sistema opere de forma **segura, eficiente e contínua**.

Como trabalho futuro, o modelo pode ser utilizado para:

- implementação de **controle supervisor**
- análise formal de propriedades do sistema
- simulações avançadas no **CPN Tools**
- expansão para sistemas industriais de maior escala.
