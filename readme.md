# Dominando o kernel em tempo real do FreeRTOS Um guia prático do tutorial (Resumo)
# Ver. 0.0.1

## Observação 
- Material baseado em "Mastering_the_FreeRTOS_Real_Time_Kernel-A_Hands-On_Tutorial_Guide" do autor Richard Barry (2016);
- Autor do Resumo: Eng. Tarcísio Souza de Melo
- Material abaixo de livre reprodução desde que mantido o nome do autor do texto base e do resumo.
___
## Sumário
- [Multitarefa em pequenos sistemas embarcados](#Multitarefa-em-pequenos-sistemas-embarcados) 
    - [Sobre o FreeRTOS](#Sobre-o-FreeRTOS)
    - [Proposta de valor](#Proposta-de-valor)
    - [Terminologia](#Terminologia)
    - [Por que usar um kernel em tempo real?](#Por-que-usar-um-kernel-em-tempo-real?)
    - [Recursos do FreeRTOS](#Recursos-do-FreeRTOS)
    - [Licenciamento do FreRTOS](#Licenciamento-do-FreRTOS)
- [Introdução](#Introdução)
    - [Entendendo a distribuição FreeRTOS](#Entendendo-a-distribuição-FreeRTOS)
        - [Definição Port FreeRTOS](#Definição-Port-FreeRTOS)
        - [Construção FreeRTOS](#Construção-FreeRTOS)
        - [FreeRTOSConfig.h](#FreeRTOSConfig.h)
        - [A distribuição oficial do FreeRTOS](#-A-distribuição-oficial-do-FreeRTOS)
        - [Arquivos de origem do FreeRTOS especificos para port](#Arquivos-de-origem-do-FreeRTOS-especificos-para-port)
        - [Inclusão de Paths](#Inclusão-de-Paths)


___

## Multitarefa em pequenos sistemas embarcados 
[↑](#Sumário) 
### Sobre o FreeRTOS
[↑](#Sumário) 

- O FreeRTOS é de propriedade, desenvolvido e mantido exclusivamente pela Real Time Engineers Ltd;
- O FreeRTOS é uma aplicação em tempo real ideal para microcontroladores ou pequenos microprocessadores;

- Requisitos de tempo real suaves estabelecem um prazo para o seu cumprimento, entretanto a violação desse prazo não torna o sistema inútil;
- Requisitos de tempo real rígidos são aqueles que estabelecem um prazo e a violação do mesmo resulta em uma falha do sistema;
- O FreeRTOS é um kernel em tempo real sobre o qual aplicativos embarcados podem ser desenvolvidos para atender a requisitos rígidos de tempo real. Torna possivel que aplicativos sejam organizados como uma coleção de threads de execuções independentes, mesmo que o sistema ao qual esteja sendo executado possua apenas um núcleo. O kernel decide qual thread deve ser executado examinando a prioridade que foi atribuida a cada thread peo desenvolvedor do aplicativo. Pode-se aplicar prioridades mais altas a threads que necessitam atender a rigidos requisitos de tempo e prioridades mais baixas para threads mais flexiveis no que tange seu tempo de execução;

### Proposta de valor
[↑](#Sumário) 
- O FreeRTOS é gratuito para uso em aplicativos comerciais sem qualquer requisito de expor seu código-fonte proprietário;

### Terminologia 
[↑](#Sumário) 
- No FreeRTOS, cada thread é chamada de tarefa;

### Por que usar um kernel em tempo real?
[↑](#Sumário) 
- Recomenda-se o uso de kernel em tempo real quando a aplicação possui algum nivel de complexidade, nestes casos seu uso torna-se benéfico;
- A priorização de tarefas pode ajudar a garantir que um aplicativa cumpra seus prazos de processamento. Além deste benefício é possivel citar outros:
    - **Abstração de informação de tempo:** O Kernel gerencia o tempo e fornece uma API relacionada ao tempo, permite a simplificação da estrutura de código do aplicativo, tornando inclusive o tamanho do código menor;
    - **Manutenção e Expansão:** Simplifica o processo de manutenção e expansão do projeto;
    - **Modularidade:** As tarefas são módulos independentes possuindo cada uma um propósito definido;
    - **Simplificação de testes:** Como os módulos(tarefas) são independentes eles podem ser testados mais facilmente;
    - **Reutilização de Código:** Maior modularidade geram códigos reutilizaveis
    - **Eficiência:** O Kernel permite que o software seja completamente orientado a eventos, desta forma nenhum tempo de processamento é desperdiçado, o código é executado apenas quando há necessidade 
    - **Gerenciamento de Energia:** Permite que o processador passe mais tempo em modo de baixo consumo de energia (sistemas low energy)

### Recursos do FreeRTOS
[↑](#Sumário) 
- O FreeRTOS possui os seguintes recursos:
    - Operação preemptiva ou cooperativa
    - Níveis de prioridade de tarefa flexiveis 
    - Notificação de tarefas flexível 
    - Filas
    - Semáforos
    - Semáforos contadores
    - Mutex
    - Mutex recursiva
    - Temporizadores de software
    - Event Gro↑s
    - Verificação de recursos utilizados
    - Dados estatisticos de tarefas em execução
    - Licenciamento comercial opcional e s↑orte 

### Licenciamento do FreRTOS
[↑](#Sumário) 
- Usuários do FreeRTOS mantêm a propriedade de sua propriedade intelectual;


## Introdução
[↑](#Sumário) 
- O FreeRTOS é distribuido como um único arquivo zip que contém todas os ports oficiais do FreeRTOS e um grande número de exemplo pré- configurados

### Entendendo a distribuição FreeRTOS
[↑](#Sumário) 

#### Definição Port FreeRTOS
[↑](#Sumário) 
- O FreeRTOS pode ser compilado por aproximadamente 20 compiladores diferens e pode ser executado em mais de 30 arquiteturas de processadores diferentes. 

#### Construção FreeRTOS
[↑](#Sumário) 
- O FreeRTOS pode ser pensado como uma biblioteca que fornece recursos multitarefas;
- O FreeRTOS é fornecido como um conjunto de arquivos ".c". Alguns destes arquivos são comuns a todos os ports enquanto outro são específicos a determinados ports.
- Cada port possui incluso um arquivo de demonstração, incluido seus arquivos de cabeçalho;

#### FreeRTOSConfig.h
[↑](#Sumário) 
- O FreeRTOS é configurado por um arquivo de cabeçalho chamado "FreeRTOSConfig.h";
    - Como ele contém definições específicas do aplicativo, deve estar localizado em um diretório que faça parte do aplicativo, não em um diretório que contenha o código fonte do FreeRTOS.

#### A distribuição oficial do FreeRTOS
[↑](#Sumário) 
- Apesar de um grande numero de arquivos, apenas um número muito pequeno de arquivos é necessário em qualquer aplicação

#### Arquivos de origem do FreeRTOS comuns a todos os ports
[↑](#Sumário) 
- O código-fonte principal do FreeRTOS esta contido em apenas dois arquivos C, sendo eles comuns a todos os ports do freeRTOS, são eles os arquivos "task.c" e "list.c" localizado em FreeRTOS/Source. Outros arquivos estão presentes neste diretório:
    - **queue.c**: serviçps de fila e semáforos (muito utilizado);
    - **timers.c**: funcionalidade de timer de software, necessita de ser incluso na compilação caso sejam necessários na aplicação desenvolvida
    - **event_groups.c**: fornece funcionalidade de grupo de eventos; 

#### Arquivos de origem do FreeRTOS especificos para port
[↑](#Sumário) 
- Arquivos específicos de um port do FreeRTOS estão contidos no diretório FreeRTOS/Source/portable;
- O FreeRTOS também considera a alocação de memória heap como parte da camada portátil;
- O FreeRTOS fornece cinco exemplos de esquemas de alocação de heap. Os cinco esquemas são denominados de heap_1 a heap_5 implementados em heap_1.c a heap_5.c todos com extensão ".c"
    - Estes arquivos estão alocados em FreeRTOS/Source/portable/MemMang
    - A alocação dinâmica de memória obriga que um desses cinco arquivos de origem no projeto

#### Inclusão de Paths
[↑](#Sumário) 
- O FreeRTOS requer 3 diretório incluidos no path do compilador:
    - 
    - 
    - 