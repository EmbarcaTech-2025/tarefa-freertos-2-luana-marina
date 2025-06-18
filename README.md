# Tarefa: Roteiro de FreeRTOS #2 - EmbarcaTech 2025

Autor: **Luana Varela Vacari e Marina Donaire**

Curso: Residência Tecnológica em Sistemas Embarcados

Instituição: EmbarcaTech - HBr

Campinas, 13 de junho de 2025


**Objetivo do Projeto**

O objetivo deste projeto é desenvolver um sistema embarcado para medir o tempo de reação de um usuário, utilizando o microcontrolador Raspberry Pi Pico, com suporte ao sistema operacional de tempo real FreeRTOS. O tempo de reação é registrado com base na resposta do usuário ao acendimento de um LED RGB controlado aleatoriamente, sendo registrada a diferença de tempo entre o estímulo e o pressionamento de um botão.


**Componentes Integrados na placa utilizados**

Microcontrolador: Raspberry Pi Pico (RP2040)

LED RGB: Para indicar o momento de ação localizado no centro da placa 

Botões: Para registrar a resposta do usuário 

Display OLED: Para exibir as mensagens e resultados em milissegundos 

FreeRTOS: Sistema operacional de tempo real

Linguagem: C


**Funcionamento Geral**

O sistema inicia o LED RGB, display OLED, botões, e o sistema de fila para o tempo de reação. O display OLED é utilizado para exibir instruções e o resultados do tempo de reação medido de acordo com o aperto do botão. O LED RGB acende em um momento aleatório, e o usuário deve pressionar um botão o mais rápido possível. O tempo entre o acendimento do LED e o pressionamento do botão é medido e exibido.

**Estrutura do Código**

O código principal está no arquivo **main.c**, que é responsável pelas inicializações e criação das tarefas do FreeRTOS. A lógica do sistema é dividida em módulos especializados:

buttons.h: Manipulação dos botões físicos

reaction_time.h: Lógica de tempo de reação e controle de estado

rgb_led.h: Controle do LED RGB

oled.h: Interface com o display OLED

**Descrição das Tarefas FreeRTOS**

  **reaction_time_task**

Objetivo: Controlar o fluxo do teste de tempo de reação.

**Funções:**

Aguardar um tempo aleatório, ou seja ocioso 

Acionar o LED RGB como estímulo.

Medir o tempo até o botão ser pressionado.

Mostrar o tempo de reação no display.

**Partes do Código e suas Funções**

Quando o sistema fica em repouso : 

![image](https://github.com/user-attachments/assets/f5b8a212-2076-4824-9ea2-dc2c6bdd86c5)

Quando há atraso aleatório antes do estímulo: 

![image](https://github.com/user-attachments/assets/954c6c0e-6ea8-4335-845e-567795fff931)

Quando o tempo de reação é calculado e exibido no display OLED : 

![image](https://github.com/user-attachments/assets/a9e0b97f-7272-488c-9893-5631f85c9054)

  **buttons_task**

Objetivo: Monitorar os botões físicos e enviar eventos para a fila.

**Funções:**

Detectar pressionamento.

Enviar evento de botão pressionado para a fila compartilhada.

![image](https://github.com/user-attachments/assets/81c0bcff-547d-4048-b773-2d4aef2ce10e)



**Fluxograma de Funcionamento**

![image](https://github.com/user-attachments/assets/2292dc4d-3857-41bf-b0b2-029292ecd1e0)

O projeto é um sistema para medir o tempo de reação do usuário, utilizando o raspberry, acoplado na BitDogLab em conjunto com FreeRTOS. Ele integra os periféricos display OLED, LED RGB, e botões físicos, presentes na placa e está estruturado em camadas modulares, favorecendo a clareza, escalabilidade e manutenção desse código. A execução do programa começa pela função main(), que realiza todas as inicializações necessárias. Primeiramente, é configurada a interface USB para saída de dados via stdio_init_all(), o que permite mensagens de depuração pelo terminal. Em seguida, o display OLED é inicializado e limpo com oled_init_display() e oled_clear_display(), preparando o sistema para exibir mensagens ao usuário. Na sequência, o módulo reaction_time é inicializado. Ele é responsável por criar e configurar a fila de eventos que será utilizada para comunicação entre as tarefas. Logo após, o módulo buttons é configurado através da função buttons_init(get_button_queue()), que associa os botões físicos à mesma fila de eventos compartilhada.

Um ponto importante do sistema é a introdução de aleatoriedade no tempo de estímulo visual. Para isso, a função srand() é chamada com um valor baseado no tempo desde o boot (to_ms_since_boot(get_absolute_time())). Isso garante que o gerador de números mude a cada reinicialização do sistema, permitindo tempos de espera diferentes a cada ciclo do jogo.

Finalizadas as inicializações, o código cria duas tarefas usando xTaskCreate():

    A primeira tarefa, chamada reaction_time_task, é responsável pela lógica central do jogo. Ela exibe as mensagens no OLED, aguarda um tempinho aleatório, acende o LED RGB como sinal para o usuário reagir, e       então espera por um evento. Assim que um botão é pressionado, a tarefa calcula o tempo de reação e exibe o resultado no display, reiniciando o ciclo em seguida, que é feito apertando o botão de reiniciar da      placa.

    A segunda tarefa, buttons_task, é dedicada exclusivamente aos botões físicos. Ela roda de maneira continua, monitorando os pinos GPIO configurados. Quando detecta que um botão foi pressionado, a tarefa          envia o evento contendo essa informação para a fila. A outra tarefa (reaction_time_task) consome esse evento para determinar o tempo de resposta do usuário.

Ambas as tarefas são executadas concorrentemente graças ao FreeRTOS, que gerencia o tempo de CPU entre elas por meio do seu escalonador (vTaskStartScheduler()). A comunicação entre essas tarefas ocorre de maneira segura e eficiente por meio da fila de eventos, evitando concorrência indevida no acesso aos recursos compartilhados.

Além das tarefas, o projeto é dividido em módulos auxiliares que encapsulam funcionalidades específicas:

    O módulo buttons cuida da configuração dos botões como entradas digitais e da leitura dos estados dos GPIOs.

    O módulo reaction_time concentra a lógica do tempo de reação e controle do estado da aplicação.

    O módulo oled gerencia a comunicação com o display OLED via protocolo I2C, exibindo mensagens e o tempo medido.


## 📜 Licença
GNU GPL-3.0.
