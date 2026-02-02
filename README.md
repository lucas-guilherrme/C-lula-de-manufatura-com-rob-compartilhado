<img width="1472" height="462" alt="image" src="https://github.com/user-attachments/assets/e7e0984c-88ab-4751-b16e-01dc40f1b139" />

---
</center>

# Célula de manufatura com robô compartilhado
  
</center>

## 📜 Índice

- [Descrição do problema](#-Descrição-do-problema)
- [Visão do problema](#-Visão-do-problema)
- [Diagrama de blocos e explicação](#-Diagrama-de-blocos-e-explicação)

---
<div align="justify">
  
## _Descrição do problema_

* **Visão geral do sistema**
O sistema consiste em uma célula de manufatura automatizada composta por duas estações de processamento independentes (Máquinas 1 e 2), um sistema de transporte compartilhado (Robô Industrial) e uma esteira de saída com capacidade de armazenamento limitada (Buffer).

O objetivo do sistema é transformar matéria-prima bruta em peças acabadas e depositá-las na esteira de saída. O desafio principal do controle é coordenar o uso do robô compartilhado e gerenciar o fluxo de produção para evitar o transbordamento da esteira (overflow) e o bloqueio das máquinas (deadlock), garantindo que as operações ocorram em uma sequência lógica e segura.

* **Detalhamento dos componentes**
A seguir o funcionamento físico e lógico de cada componente da planta é descrito.

_A.Máquinas de processamento (M1 e M2)_
As duas máquinas são idênticas em funcionamento, mas operam de forma independente.

Funcionamento: a máquina parte de um estado de repouso. Ao receber um comando de início, ela começa a processar uma peça (assume-se que a matéria-prima está sempre disponível na entrada da máquina). O processo leva um tempo indeterminado. Ao finalizar o processamento, a máquina sinaliza que a peça está pronta e aguarda a retirada da mesma.
Restrições físicas:
A máquina não pode iniciar um novo ciclo de trabalho enquanto a peça anterior não for removida pelo robô (estado de "Peça Pronta" ou Done).
A máquina não pode ter a peça retirada se não tiver terminado o processamento.
_B. Robô industrial (Robô)_
O robô é o agente de transporte central da célula. Ele atua como o elo entre as máquinas e o buffer de saída.

Funcionamento: o robô parte de uma posição neutra, sem carga. Ele pode se mover até a Máquina 1 ou até a Máquina 2 para coletar uma peça processada. Após coletar a peça, ele se move até o Buffer de saída para depositá-la.
Restrições físicas:
Capacidade unitária: o robô só pode carregar uma peça por vez.
Sequenciamento: o robô não pode coletar uma peça se já estiver carregando uma. O robô não pode depositar uma peça se não estiver carregando uma.
Sincronia: o robô só pode realizar a ação de "pegar" (pick/get) se a respectiva máquina estiver no estado "Peça Pronta".
_C. Buffer de saída (Esteira)_
O buffer é uma esteira ou zona de armazenamento temporário onde as peças finalizadas são depositadas antes de serem levadas para o próximo setor da fábrica ou expedição.

Funcionamento: o buffer aceita peças trazidas pelo robô. As peças permanecem no buffer até que um agente externo (outro processo, operador humano ou empilhadeira) as remova.
Restrições físicas:
Capacidade limitada: o buffer possui um número finito de slots (posições). Para este projeto, define-se a capacidade = 2 peças  para evidenciar facilmente problemas de bloqueio.
Transbordamento (overflow): Não é fisicamente possível (ou é catastrófico) depositar uma peça se o buffer já estiver cheio.
Fluxo operacional e interações
O ciclo de operação nominal do sistema segue a seguinte lógica:
Um sinal é enviado para ligar a Máquina 1 e Máquina 2.
As máquinas processam o material e, eventualmente, seus sensores indicam o fim do processo (evento não-controlável). As máquinas param e ficam aguardando a retirada da peça.
O Robô, estando livre, desloca-se até uma máquina que tenha uma peça pronta.
O Robô retira a peça da máquina (liberando a máquina para iniciar um novo ciclo imediatamente, se desejado).
O Robô transporta a peça até o Buffer.
Se o Buffer tiver espaço livre, o Robô deposita a peça e volta ao estado livre.
Um evento externo remove a peça do Buffer, liberando espaço para futuras operações.
* **Problemas potenciais**
Sem um Supervisor (controlador lógico) adequado, o sistema está sujeito às seguintes falhas que o projeto deve evitar:
Colisão de recursos: o robô tentar pegar peças de M1 e M2 simultaneamente.
Violação de capacidade: o robô tentar depositar uma peça no Buffer quando este já está cheio, o que causaria danos à peça ou ao equipamento.
Bloqueio (deadlock):
Cenário: o Buffer está cheio. O Robô está segurando uma peça (aguardando o buffer liberar). As Máquinas M1 e M2 completaram suas peças e estão aguardando o Robô.
Consequência: se não houver garantia de que o Buffer será esvaziado (evento externo), o sistema trava completamente e nenhuma máquina pode produzir mais nada.
Tentativa de operação inválida: o comando de "Pegar peça da M1" ser enviado quando a M1 ainda está trabalhando (sem peça pronta).
</div>

---
## Visão do problema

![Visao do sistema](assets/Visao_do_sistema.png)

---
## Diagrama de blocos e explicação

<img width="1320" height="820" alt="Sistema Físico - Planta" src="https://github.com/user-attachments/assets/b4ce9b42-23bc-4917-bfe4-5422360aec47" />

<img width="980" height="780" alt="Sistema de Controle - Supervisor" src="https://github.com/user-attachments/assets/a353c47d-da4f-40d7-86d6-ecbf69d46b58" />







---
---
