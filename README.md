# Projeto de Sistemas Embarcados com FreeRTOS na Raspberry Pi Pico

## 1. Visão Geral
Este projeto é um estudo prático e aprofundado sobre o desenvolvimento de sistemas embarcados multitarefa utilizando a placa Raspberry Pi Pico e o Sistema Operacional de Tempo Real (RTOS) FreeRTOS. O objetivo principal foi explorar conceitos fundamentais de concorrência, gerenciamento de hardware e comunicação entre tarefas, evoluindo de um sistema simples baseado em terminal para uma aplicação completa com interface gráfica em um display OLED.

O desenvolvimento foi incremental, começando com tarefas básicas e evoluindo para uma arquitetura robusta que lida com múltiplos periféricos de forma segura e eficiente.

## 2. Funcionalidades Principais
O software final implementa as seguintes funcionalidades, todas rodando como tarefas concorrentes no FreeRTOS:

* Tela de Inicialização (Splash Screen): Ao ligar, o dispositivo exibe um logo personalizado em um display OLED por 3 segundos antes de iniciar a aplicação principal.

* Monitoramento de Temperatura: Uma tarefa dedicada lê o sensor de temperatura interno da Raspberry Pi Pico a cada 2 segundos. A média de 3 leituras é calculada e exibida no display a cada 6 segundos.

* Leitura de Joystick: Uma segunda tarefa monitora um joystick analógico continuamente. A direção (Cima, Baixo, Esquerda, Direita) é detectada e exibida no display, com uma taxa de atualização controlada para não sobrecarregar a visualização.

* Sinalização Visual (LED): Uma tarefa leve e de baixa prioridade controla o piscar de um LED de forma contínua e independente, demonstrando a capacidade do sistema de lidar com tarefas de fundo.

* Interface com Display OLED: Todas as informações são renderizadas em um display OLED de 128x64 pixels (controlador SSD1306) via comunicação I2C.

* Gerenciamento de Recurso Compartilhado: O acesso ao display OLED é controlado por um Mutex, garantindo que as tarefas de temperatura e do joystick não corrompam os dados da tela ao tentar atualizá-la simultaneamente.

## 3. Arquitetura do Software
A arquitetura final é baseada no FreeRTOS e consiste em três tarefas de aplicação principais e um mecanismo de exclusão mútua para o display:

* vTemperatureTask: Responsável por toda a lógica de leitura, cálculo de média e formatação dos dados de temperatura.

* vJoystickTask: Responsável por ler o estado do joystick, interpretar a direção e formatar a saída.

* vBlinkTask: Uma tarefa simples e de baixo consumo que opera de forma independente para piscar um LED.

* display_mutex: Um semáforo do tipo Mutex que protege o acesso ao buffer da tela (screen_buffer). Antes de qualquer tarefa poder desenhar no display, ela deve adquirir o mutex. Após a renderização, o mutex é liberado, permitindo que outra tarefa possa usar o recurso.

## 4. Evolução do Projeto
O desenvolvimento seguiu uma abordagem evolutiva, demonstrando diferentes conceitos e arquiteturas:

* Conceitos Iniciais: O projeto começou como uma exploração dos fundamentos do FreeRTOS: multitarefa, filas (Queues), semáforos e mutexes.

* Comunicação via Filas: Uma arquitetura inicial foi criada onde tarefas produtoras (ex: leitura de sensor) enviavam dados para tarefas consumidoras (ex: processamento) através de filas, uma prática robusta para desacoplar tarefas.

* Logger Centralizado: Foi implementado um padrão onde todas as saídas de printf eram enviadas para uma fila gerenciada por uma única "tarefa de logging", otimizando o uso de pilha (stack) e prevenindo conflitos no terminal.

* Mudança para Tarefas Independentes: Atendendo a um requisito de design, o projeto foi refatorado para um modelo onde as filas foram removidas e cada tarefa se tornou responsável por sua própria saída, chamando printf diretamente.

* Integração com Display OLED: O passo mais significativo foi substituir toda a saída de terminal pela renderização em um display OLED. Isso introduziu o desafio de gerenciar um recurso compartilhado (o display).

* Implementação de Mutex: Para resolver o problema de acesso concorrente ao display, um Mutex foi adicionado, garantindo a integridade dos dados na tela.

* Polimento com Splash Screen: Por fim, uma tela de inicialização foi adicionada para dar um acabamento mais profissional ao projeto, incluindo o processo de conversão de imagens (JPG/PNG) para o formato de bitmap monocromático exigido pelo display.

## 5. Hardware Utilizado
* Controlador: Raspberry Pi Pico

* Display: OLED 128x64 com controlador SSD1306 (interface I2C)

* Entrada: Joystick Analógico de 2 eixos

* Saída Visual: 1x LED de qualquer cor

## 6. Como Compilar
Para compilar o projeto, é necessário ter o ambiente de desenvolvimento para a Raspberry Pi Pico configurado.

Pré-requisitos:

* Pico SDK

* ARM GCC Toolchain

 *CMake

Estrutura de Arquivos:

* tarefa1_SE.c: Arquivo principal da aplicação.

* ssd1306_i2c.c, ssd1306_i2c.h, ssd1306.h, ssd1306_font.h: Drivers para o display OLED.

* CMakeLists.txt: Arquivo de configuração da compilação.