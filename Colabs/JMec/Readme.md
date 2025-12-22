# 🎮 Jogo 3 – Labirinto ⚙️

O desenvolvimento do **Jogo 3**, um projeto eletromecânico interativo. O objetivo principal foi criar um labirinto, quando uma bolinha atinge o fim do percurso, um sensor infravermelho (modelo **LM193**) detecta o evento e envia um sinal para um sistema central (uma caixa ou cofre) para destravar uma tranca. A comunicação entre o jogo e a caixa é realizada via **Bluetooth**, utilizando o módulo integrado da placa **ESP32**, que também é responsável pelo controle geral do sistema. Para alimentar o circuito, utiliza-se uma bateria de **12V** do tipo **Li-Po**, cuja tensão é convertida para o nível adequado (**5V**) por um módulo **step-down LM2596**. A lista de componentes tem um custo direto de apenas **R$ 12,90**, referente ao sensor, pois os demais itens (como bateria, ESP32 e step-down) são considerados recursos da equipe, reduzindo significativamente o orçamento em comparação a uma montagem com todos os itens adquiridos separadamente, veja Figura 2.4-A.

## 🔨 Desenvolvimento Físico

O desenvolvimento físico do labirinto começou com um protótipo inicial feito em **papelão e isopor**, baseado em um croqui com dimensões de **180mm x 180mm**. Posteriormente, o projeto foi modelado em 3D utilizando o software **Catia V5**, permitindo simulações, correção de eventuais erros e a visualização detalhada da montagem por meio de uma **vista explodida**. A peça final do labirinto foi fabricada como uma única parte usando **impressão 3D** com material **PLA** (Ácido Polilático), escolhido por sua facilidade de uso e origem renovável. A impressão consumiu **125 gramas** de filamento e levou aproximadamente **2 horas e 47 minutos**. A parte do invólucro foi desenvolvida no Catia V5 e exportado o arquivo STL para a impressão 3D, manufaturada em cerca de **7 horas** no total para as peças do invólucro, utilizando **329 gramas** de filamento. O pedaço de acrílico foi doado por um amigo, cortei e furei para usar como tampo, vide Figuras 2.4-B, Figura 2.4-C e Figura 2.4-D. Para entender melhor a montagem observe a simulação criada no Catia em **Figura 2.4-E**.

## ⚡ Aspecto Eletrônico e Programação

No aspecto eletrônico, o circuito foi montado conforme esquemático apresentado (Figura 2.4-F1 e Figura 2.4-F2), integrando a bateria, o step-down, a ESP32 e o sensor IR. A programação do sistema foi desenvolvida em **C++** utilizando a plataforma **Arduino IDE**. O código configura a ESP32 para gerenciar a comunicação Bluetooth e ler o estado do sensor IR. Quando a bolinha é detectada (sinaliza o final do percurso), a ESP32 envia uma mensagem, como **"Final de Jogo"** e **"Bolinha Detectada"**, para um celular pareado, usando um aplicativo **"terminal serial Bluetooth"** para exibir as informações. Testes práticos validaram o funcionamento do circuito, da detecção e da comunicação, confirmando que o sistema opera conforme o esperado, porém sem a comunicação com a Caixa (Figura 2.4-G, Figura 2.4-H). O documento inclui ainda referências sobre materiais e ferramentas utilizadas, como informações sobre as propriedades do PLA.

---

### 📋 Figura 2.4-A – Lista de Componentes 🛒

| Unidade | Componente                                   | Valor       |
|---------|----------------------------------------------|-------------|
| 1       | bateria de 12V - Li - Po 1500 mAh (Equipe)   |             |
| 1       | sensor IR - LM193                            | R$ 12,90    |
| 1       | Step-Down LM2596 (Equipe)                    |             |
| 1       | Esp32 (Equipe)                               |             |
| x       | fios jumper (Equipe)                         |             |
| 4 peças | Filamento PLA Manufatura Aditiva             | R$ 55,00    |
|         | **Total Geral**                              | **R$ 67,90**|

*Fonte: autoria própria (03/11/2025)*

### 🧩 Figura 2.4-B – Peça Manufaturada do Labirinto

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Fig01.png" alt="Peça Manufaturada do Labirinto" width="800" /> 

*Fonte: autoria própria (03/11/2025)*

### 📦 Figura 2.4-C – Peças Manufaturadas do Invólucro Parte 01

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Fig02.png" alt="Peças Manufaturadas do Invólucro Parte 01" width="500" />

*Fonte: autoria própria (23/11/2025)*

### 📦 Figura 2.4-D – Peças Manufaturadas do Invólucro Parte 02

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Fig03.png" alt="Peças Manufaturadas do Invólucro Parte 02" width="800" />

*Fonte: autoria própria (23/11/2025)*

### 💻 Figura 2.4-E – Simulação no Catia V5

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Simula%C3%A7%C3%A3o.gif" alt="Simulação no Catia V5" width="800" />

*Fonte: autoria própria (23/11/2025)*

### 🔌 Figura 2.4-F1 - Esquemático

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Fig04.png" alt="Esquemático" width="800" />

*Fonte: autoria própria (03/11/2025)*

### 🔋 Figura 2.4-F2 – Circuito Montado

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Fig05.png" alt="Circuito Montado" width="800" />

*Fonte: autoria própria (03/11/2025)*

### 🛠 Figura 2.4-G – Teste Prático

<img src="https://github.com/elisfcampos/INNOWAVE/blob/56019b2658642808648d829adeda81ccfff5a1a9/Colabs/JMec/Imgs/Teste.gif" alt="Teste Prático" width="800" />

*Fonte: autoria própria (26/11/2025)*

### 📟 **Figura 2.4-H – Código** 

<img src="https://github.com/elisfcampos/INNOWAVE/blob/2f82d90a75a9c0eeeaca3b949a0ac97cd46dffa6/Colabs/JMec/Imgs/cod.png" alt="Código" width="700" />

### ✨ Conclusão

O desenvolvimento do projeto Jogo 3 - Labirinto seguiu todas as etapas planejadas com sucesso, desde o estudo teórico e a simulação inicial, passando pela manufatura das peças e a montagem do circuito, até a codificação de controle. Cada uma dessas fases foi testada e validada em múltiplas iterações, garantindo que todos os componentes funcionassem conforme o esperado.

Apesar do cumprimento bem-sucedido dessas etapas fundamentais, a integração final do mecanismo com o sistema principal do projeto não pôde ser concluída dentro do prazo estipulado.
É importante destacar, no entanto, que o funcionamento básico e autônomo do Labirinto foi plenamente validado. O módulo permanece totalmente operacional, com sua comunicação via Bluetooth com um dispositivo celular perfeitamente funcional. Isso permite que ele seja utilizado com êxito para testes isolados, demonstrações do conceito e validação da jogabilidade, atendendo ao seu propósito central.

### 🗺️ Referências

Alvaro, Julie. Ácido Polilático (PLA). QUIÍMICA.com.br. Disponível em: https://www.quimica.com.br/acido-polilaticoplal#:~:text=Propriedades%20do%20%C3%A1cido%20polil%C3%A1tico,com%20variados%20graus%20de%20flexibilidade. Acesso em: 03 Novembro 2025.
