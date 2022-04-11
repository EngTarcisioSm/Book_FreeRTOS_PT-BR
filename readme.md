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
        - [Arquivos de cabeçalho](#Arquivos-de-cabeçalho)
    - [Aplicativos de demonstração](#Aplicativos-de-demonstração)
    - [Criando um projeto FreeRTOS](#Criando-um-projeto-FreeRTOS)
    - [Tipos de dados e Estilo de Codificação](#Tipos-de-dados-e-Estilo-de-Codificação)
        - [Tipos de dados](#Tipos-de-dados)
        - [Nomes de Variáveis](#Nomes-de-Variáveis)
        - [Nomes de Funções](#Nomes-de-Funções)
        - [Nomes de Macros](#Nomes-de-Macros)
- [Gerenciamento de Memória Heap](#Gerenciamento-de-Memória-Heap)
    - [Alocação dinâmica de memória e sua relevância para o FreeRTOS](#Alocação-dinâmica-de-memória-e-sua-relevância-para-o-FreeRTOS)
    - [Opções para alocação de memória dinâmica](#Opções-para-alocação-de-memória-dinâmica)
    - [Exemplo de Esquemas de Alocação de Memória](#Exemplo-de-Esquemas-de-Alocação-de-Memória)
        - [HEAP 1](#HEAP-1)
        - [HEAP 2](#HEAP-2)
        - [HEAP 3](#HEAP-3)
        - [HEAP 4](#HEAP-4)
        - [HEAP 5](#HEAP-5)
    - [Funções utilitárias relacionadas a Heap](#Funções-utilitárias-relacionadas-a-Heap)
        - [Função de API xPortGetFreeHeapSize()](#Função-de-API-xPortGetFreeHeapSize())
        - [Função de API xPortGetMinimumEverFreeHeapSize()](#Função-de-API-xPortGetMinimumEverFreeHeapSize())
- [Gerenciamento de tarefas](#Gerenciamento-de-tarefas)
    - [Funções da Tarefa](#Funções-da-Tarefa)
    - [Estados de Tarefas de Nível Superior](#Estados-de-Tarefas-de-Nível-Superior)
    - [Função da API xTaskCreate()](#Função-da-API-xTaskCreate())
    - [Criando Tarefas](#Criando-Tarefas) 
        - [Utilizando parametro da Task](#Utilizando-parametro-da-Task)
    - [Prioridade de Tarefas](#Prioridade-de-Tarefas)
    - [Medição de tempo e Interrupção de Tick](#Medição-de-tempo-e-Interrupção-de-Tick)
        - [pdMS_TO_TICK()](#pdMS_TO_TICK())
    - [Expandindo o estado "Não em execução"](#Expandindo-o-estado-"Não-em-execução")
    - [Estados de tarefas](#Estados-de-tarefas)
        - [Estado Bloqueado](#Estado-Bloqueado)
        - [Estado Suspenso ](#Estado-Suspenso)
        - [Estado Pronto](#Estado-Pronto)
        - [Fluxograma de Estados](#Fluxograma-de-Estados)
    - [Usando o estado Bloqueado para criar um atraso ](#Usando-o-estado-Bloqueado-para-criar-um-atraso )
        - [vTaskDelay()](#vTaskDelay())
___
## Multitarefa em pequenos sistemas embarcados 
[↑](#Sumário) 
### Sobre o FreeRTOS

- O FreeRTOS é de propriedade, desenvolvido e mantido exclusivamente pela Real Time Engineers Ltd;
- O FreeRTOS é uma aplicação em tempo real ideal para microcontroladores ou pequenos microprocessadores;

- Requisitos de tempo real suaves estabelecem um prazo para o seu cumprimento, entretanto a violação desse prazo não torna o sistema inútil;
- Requisitos de tempo real rígidos são aqueles que estabelecem um prazo e a violação do mesmo resulta em uma falha do sistema;
- O FreeRTOS é um kernel em tempo real sobre o qual aplicativos embarcados podem ser desenvolvidos para atender a requisitos rígidos de tempo real. Torna possivel que aplicativos sejam organizados como uma coleção de threads de execuções independentes, mesmo que o sistema ao qual esteja sendo executado possua apenas um núcleo. O kernel decide qual thread deve ser executado examinando a prioridade que foi atribuida a cada thread peo desenvolvedor do aplicativo. Pode-se aplicar prioridades mais altas a threads que necessitam atender a rigidos requisitos de tempo e prioridades mais baixas para threads mais flexiveis no que tange seu tempo de execução;
<br>[↑](#Sumário) 

### Proposta de valor
- O FreeRTOS é gratuito para uso em aplicativos comerciais sem qualquer requisito de expor seu código-fonte proprietário;
<br>[↑](#Sumário) 

### Terminologia 
- No FreeRTOS, cada thread é chamada de tarefa;
<br>[↑](#Sumário) 

### Por que usar um kernel em tempo real?
- Recomenda-se o uso de kernel em tempo real quando a aplicação possui algum nivel de complexidade, nestes casos seu uso torna-se benéfico;
- A priorização de tarefas pode ajudar a garantir que um aplicativa cumpra seus prazos de processamento. Além deste benefício é possivel citar outros:
    - **Abstração de informação de tempo:** O Kernel gerencia o tempo e fornece uma API relacionada ao tempo, permite a simplificação da estrutura de código do aplicativo, tornando inclusive o tamanho do código menor;
    - **Manutenção e Expansão:** Simplifica o processo de manutenção e expansão do projeto;
    - **Modularidade:** As tarefas são módulos independentes possuindo cada uma um propósito definido;
    - **Simplificação de testes:** Como os módulos(tarefas) são independentes eles podem ser testados mais facilmente;
    - **Reutilização de Código:** Maior modularidade geram códigos reutilizaveis
    - **Eficiência:** O Kernel permite que o software seja completamente orientado a eventos, desta forma nenhum tempo de processamento é desperdiçado, o código é executado apenas quando há necessidade 
    - **Gerenciamento de Energia:** Permite que o processador passe mais tempo em modo de baixo consumo de energia (sistemas low energy)
<br>[↑](#Sumário) 

### Recursos do FreeRTOS
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
<br>[↑](#Sumário) 

### Licenciamento do FreRTOS
- Usuários do FreeRTOS mantêm a propriedade de sua propriedade intelectual;
<br>[↑](#Sumário) 


## Introdução
- O FreeRTOS é distribuido como um único arquivo zip que contém todas os ports oficiais do FreeRTOS e um grande número de exemplo pré- configurados
<br>[↑](#Sumário) 

### Entendendo a distribuição FreeRTOS
<br>[↑](#Sumário) 

#### Definição Port FreeRTOS
- O FreeRTOS pode ser compilado por aproximadamente 20 compiladores diferens e pode ser executado em mais de 30 arquiteturas de processadores diferentes. 
<br>[↑](#Sumário) 

#### Construção FreeRTOS
- O FreeRTOS pode ser pensado como uma biblioteca que fornece recursos multitarefas;
- O FreeRTOS é fornecido como um conjunto de arquivos ".c". Alguns destes arquivos são comuns a todos os ports enquanto outro são específicos a determinados ports.
- Cada port possui incluso um arquivo de demonstração, incluido seus arquivos de cabeçalho;
<br>[↑](#Sumário) 

#### FreeRTOSConfig.h
- O FreeRTOS é configurado por um arquivo de cabeçalho chamado "FreeRTOSConfig.h";
    - Como ele contém definições específicas do aplicativo, deve estar localizado em um diretório que faça parte do aplicativo, não em um diretório que contenha o código fonte do FreeRTOS.
<br>[↑](#Sumário) 

#### A distribuição oficial do FreeRTOS
- Apesar de um grande numero de arquivos, apenas um número muito pequeno de arquivos é necessário em qualquer aplicação
<br>[↑](#Sumário) 

#### Arquivos de origem do FreeRTOS comuns a todos os ports
- O código-fonte principal do FreeRTOS esta contido em apenas dois arquivos C, sendo eles comuns a todos os ports do freeRTOS, são eles os arquivos "task.c" e "list.c" localizado em FreeRTOS/Source. Outros arquivos estão presentes neste diretório:
    - **queue.c**: serviçps de fila e semáforos (muito utilizado);
    - **timers.c**: funcionalidade de timer de software, necessita de ser incluso na compilação caso sejam necessários na aplicação desenvolvida
    - **event_groups.c**: fornece funcionalidade de grupo de eventos; 
<br>[↑](#Sumário) 

#### Arquivos de origem do FreeRTOS especificos para port
- Arquivos específicos de um port do FreeRTOS estão contidos no diretório FreeRTOS/Source/portable;
- O FreeRTOS também considera a alocação de memória heap como parte da camada portátil;
- O FreeRTOS fornece cinco exemplos de esquemas de alocação de heap. Os cinco esquemas são denominados de heap_1 a heap_5 implementados em heap_1.c a heap_5.c todos com extensão ".c"
    - Estes arquivos estão alocados em FreeRTOS/Source/portable/MemMang
    - A alocação dinâmica de memória obriga que um desses cinco arquivos de origem no projeto
<br>[↑](#Sumário) 

#### Inclusão de Paths
- O FreeRTOS requer 3 diretório incluidos no path do compilador:
    - Caminho para os arquivos de cabeçalho "FreeRTOS/Source/include
    - Caminho para os arquivos de origem específicos do port do FreeRTOS FreeRTOS/Source/portable/[compilador]/[arquitetura]
    - Caminho para o arquivo de cabeçalho FreeRTOSConfig.h
<br>[↑](#Sumário) 

#### Arquivos de cabeçalho
- Dentro de um arquivo que utiliza o FreeRTOS deve-se incluir inicialmente o arquivo 'FreeRTOS.h' seguido dos arquivos cabeçalho que tenham recursos sendo utilizados 'task.h', 'queue.h', 'semphr.h' e/ou 'eventgroups.h'
<br>[↑](#Sumário) 

### Aplicativos de demonstração 
- Cada port do FreeRTOS vem com pelo menos um aplicativo de demonstração;
- O aplicativo de demonstração tem vários propósitos:
    - Fornecer um exemplo de um projeto em funcionamento e pré-configurado, com os arquivos corretos incluídos e as opções corretas do compilador 
    - Permitir ensaios com configurações mínimas 
    - Fornece uma demonstração de como a API pode ser usada 
- Cada projeto de demonstração esta localizado em um subdiretório FreeRTOS/Demo
- Cada aplicativo de demonstração também é descrito por uma página Web com suas informações para uso;
<br>[↑](#Sumário) 

### Criando um projeto FreeRTOS
- Recomenda-se a criação de novos projetos adaptando um projeto já existente (demonstração), isso permite que o projeto tenha os arquivos incluídos corretamente, dentre outros recursos 
- Para a utilização de arquivos de demonstração o seu arquivo maindeve ficar como descrito abaixo 
~~~c
int main(void){
    /*Realiza operações de inicialização e configuração dos hardwares*/
    prvSetupHardware();

    /*-- CRIAÇÃO DAS TAREFAS A SEREM EXECUTADAS --*/
    //criar aqui 

    /*Inicialização das tarefas criadas*/
    vTaskStartScheduler();

    /*A execução apenas chegará aqui caso o heap alocado for insuficiente para iniciar o agendador*/
    for(;;){

    }
    return 0;
}
~~~
<br>[↑](#Sumário) 

### Criando um projeto do Zero 
- Nesta situação o projeto pode ser criado seguindo os seguintes procedimentos 
    - Usando as ferramentas desejadas, criar um novo projeto que ainda não inclua nenhum arquivo de origem do FreeRTOS.
    - Testar o projeto criado
    - Após testado, adicionar ao projeto os arquivos de origem do FreeRTOS detalhados na tabela abaixo 
    - Copiar o arquivo de cabeçalho FreeRTOSConfig.h usado pelo projeto de demonstração fornecidos para o port em uso no diretório do projeto 
    - Adicionar os seguintes diretórios ao caminho que o projeto pesquisará para localizar os arquivos de cabeçalho:
        - FreeRTOS/Fonte/include
        - FreeRTOS/Source/[compliador]/[arquitetura]
        - Diretório que contém o arquivo de cabeçalho FreeRTOSConfig.h
    - Copiar as configurações do compilador do projeto de demonstração relevante
    - Instalar os manipuladores de interrupção do FreeRTOS que possam ser necessário

|Arquivo|Localização|
|:---:|:---:|
|task.c|FreeRTOS/Source|
|queue.c|FreeRTOS/Source|
|list.c|FreeRTOS/Source|
|timer.c|FreeRTOS/Source|
|event_groups.c|FreeRTOS/Source|
|Todos arquivos c e assembler|FreeRTOS/Source/portable/[Compilador]/[Arquitetura]|
|heap_n.c|FreeRTOS/Source/portable/MemMang. Este arquivo se tornou opcional a partir do FreeRTOS v9.0.0|
<br>[↑](#Sumário) 

### Tipos de dados e Estilo de Codificação

#### Tipos de dados 

| Macro ou Typedef usado | Tipo Real |
|:---|:---|
|TickType_t| O FreeRTOS configura uma interrupção periódica chamada interrupção de tique. <br>O numero de Interrupções de tick que ocorrem desde de que o aplicativo FreeRTOS foi iniciado é chamado de contagem de Ticks. Acontagem de ticks é usada como medida de tempo.<br> TickType_t é o tipo de dados usado para manter o valor da contagem de ticks e para especificar os tempos.<br> O tamanho da variavel TickType_t depende da  constante "configUSE_16_BIT_TICKS", dentro do arquivo FreeRTOSConfig.h, para valor 1, a variavel será de 16bits, para o valor 0, será definido como 32bits.<br> Recomenda-se o uso de 0 para processadores de 32bits e o valor 1 para processadores de 16 e 8 bits.|
|BaseType_t|É sempre definido com base no tipo de dado mais eficiente da arquitetura.<br> Para microcontroladores de 32bits o tipo é 32bits, para microcontroladores de 16bits a variavel é de 16bits e com arquiteturas de 8bits o tipo será de 8bits.<br> BaseType_t geralmente é usado para tipos de retorno que podem receber um intervalo muito limitado de valores e para booleanos do tipo pdTRUE e pdFALSE.|  
<br>[↑](#Sumário) 

#### Nomes de Variáveis 
- As variáveis são prefixadas com seu tipo 
    - 'c' para char
    - 's' para int16_t
    - 'l' para int32_t
    - 'x' para BaseType_t e quais quer outros tipos não padrão (struct, identificadores, filas e etc...)
- Caso uma varável não tiver sinal, ela também será prefixadas com 'u'
- Caso uma variavel seja um ponteiro, ela será prefixada por 'p'
<br>[↑](#Sumário) 

#### Nomes de Funções
- As funções são prefixadas com base em seu tipo de retorno e com o arquivo em que são definidas 
- O nome é definidido da seguinte forma 
    - [tipo][arquivo_de_origem][nome]()
- Exemplo
    - vTaskPrioritySet()
        - retorno vazio
        - construida dentro do arquivo task.c
<br>[↑](#Sumário) 

#### Nomes de Macros
- A maioria das mnacros são escritas em maiúsculo e prefixadas com letra minuscula com o nome do arquivo de origem da macro
- Exemplo:
    - taskENTER_CRITICAL()
        - macro localizada dentro do arquivo task.c
- Excessão aos nomes dos semaforos, onde grande parte são desenvolvido por macros, entretanto sua nomenclatura é definida como a de funções 
- Valores de algumas macros comuns

|Macro|Valor|
|:---:|:---:|
|pdTRUE|1|
|pdFALSE|0|
|pdPASS|1|
|pdFAIL|0|
<br>[↑](#Sumário) 

## Gerenciamento de Memória Heap
- A partir do FreeRTOS V9.0.0, os aplicativos FreeRTOS podem ser totalmente alocados estaticamente removendo a necessidade de incluir um gerenciador de memória heap, ou dinamicamente em tempo de execução
<br>[↑](#Sumário) 

### Alocação dinâmica de memória e sua relevância para o FreeRTOS
- O Kernel possui objetos como tarefas, filas, semáforos e grupos de eventos;
- Os objetos do Kernel não são alocados em tempo de compilação, mas alocados dinamicamente em tempo de execução;
- Os objetos podem ser alocados sempre que necessário e excluidos, liberando espaço ocupado;
- A alocação dinamica de memória é um conceito de programação C, e não um conceito específico do FreeRTOS ou multitarefas. 
- As ferramentas de alocação dinamica de compiladores nem sempre são as mais ideais para sistemas de tempo real;
- O C fornece as funções malloc() e free() respectivamente para a alocação e desalocação de memoria, entretanto elas acabam não sendo as melhores opções por alguns motivos 
    - Falta de espaço em sistemas embarcados para sua implementação 
    - Raramente são thread-safe
    - Não são deterministicas, tendo seu tempo de execução sofrendo uma variação muito grande 
    - Pode gerar erros de depuração dificil 
<br>[↑](#Sumário) 

### Opções para alocação de memória dinâmica
- O FreeRTOS agora trata a alocação de memória como parte da camada portátil. Diferentes sistemas embarcados tem diferentes requisitos de alocação dinamica de memoria e temporização, devido a isso a alocação no FreeRTOS ficar em uma camada portátil
- Quando o FreeRTOS requer RAM, em vez de chamar malloc(), ele chama pvPortMalloc();
- QUando a RAM necessita de ser liberada, em vez de chamar free(), o kernel chama vPortFree();
- pvPortMalloc() e pvPortFree() são funções publicas, portanto também podem ser chamadas a partir do código do aplicativo 
- O FreeRTOS vem com cinco exemplos de implementações de pvPortMalloc() e pvPortFree(), sendo definidos nos arquivos de origem heap_1.c, heap_2.c, heap_3.c, heap_4.c e heap_5.c localizados no diretório FreeRTOS/Source/portable/MemMang
<br>[↑](#Sumário) 

### Exemplo de Esquemas de Alocação de Memória

#### HEAP 1 
- Em alguns sistemas embarcados ocorre apenas a criação de tarefas e de alguns outros objetos do kernel antes do escalonador ser iniciado;
- O Heap 1 implementa versões básicas do pvPortMalloc() e não implementa vPortFree().
- Aplicações que nunca excluem tarefas ou objetos do kernel tem a possibilidade de usar o Heap 1.
- Sistemas Comerciais críticos e criticos de segurança que proibem o uso de alocação dinÂmica também tem o potencial de usar o Heap 1, a alocção é proibida devido a falta de determinismo;
- O tamanho total em bytes do array é definidio em config_TOTAL_HEAP_SIZE, dentro do arquivo FreeRTOSConfig
<br>[↑](#Sumário) 

#### HEAP 2
- Heap 2 é mantido por questão de compatibilidade com versões anteriores do freeRTOS;
- Não é recomendado seu uso atual, tendo o Heap 4 substituindo-o muito bem;
- Assim como o Heap 1 seu tamanh é definido por "FreeRTOSConfig" na definição para 1 de config_TOTAL_HEAP_SIZE;
- O algoritmo de melhor ajuste garante que pvPotMaloc() use bloco de memória livre com tamanho mais próximo ao numero de bytes solicitados;
- Ao contrário do Heap_4 o healp_2 não combina blocos livres adjacentes em um unico bloco mmaior, sndo mais sucetivel 
- O Heap 2 não é deterministico, mais e mais rápido que a maioria das implementalçies das implementações de biblioteca padrão de malloc() e free() 
<br>[↑](#Sumário) 

#### HEAP 3 
- Usa as funções malloc() e free() da biblioteca padrão, portanto o tamanho do healp é definidio pela configuração do vinculador e a configuração  de config_TOTAL_HEAP_SIZE não possui efeito;
- Heap 3 torna malloc() e free() thread-safe suspendendo temporariamente o agendador do freeRTOS
<br>[↑](#Sumário) 

#### HEAP 4
- O tamanho do HEAP 4 é definidio em config_TOTAL_HEAP_SIZE, assim como o HEAP 1 e 2 
- Esse método de configuração da a impressão que a aplicação consome muita RAM, mesmo antes de qualquer alocamento tenha ocorrido 
- Usa o algorítmo de primeiro ajuste para alocar memória;
- Ao contrário do Heap 2, o Heap 4 aglutina os blocos de memória livres adjacentes em um unico bloco maior, minimizando o risco de fragmentação de memória 
- O algoritmo de ajuste garante que pvPortMalloc() use o promeiro bloco de memória lire suficiente para conter os bytes solicitados 
- Permite ocorrer a liberação repetidamente de blocos de tamanho diferentes de memoria uma vez que objetos podem deixar de existir liberando o espaço anteriormente utilizado.
- Não é deterministico, entretanto é mais rápido que a maioria das implementações de das bibliotecas padrão malloc e free
<br>[↑](#Sumário) 

#### HEAP 5
- O algoritmo usado para alocar e liberar memória é identico ao usado pelo HEAP 4. Diferente do HEAP 4, o HEAP 5 não se limita a alocar memória de um unico array, podendo alocar memória de vários espaços separados;
- Quando HEAP 5 é usado vPortDefineHEAPRegions() deve ser chamado antes que qualquer objeto do kernel (tarefas, filas, semáforos e etc...)
<br>[↑](#Sumário) 

### Funções utilitárias relacionadas a Heap

#### Função de API xPortGetFreeHeapSize()
- Retorna o número de bytes livres no heap no momento em que a função é chamada;
- Não esta disponível para o HEAP_3;
<br>[↑](#Sumário) 

#### Função de API xPortGetMinimumEverFreeHeapSize()
- Retorna o número minimo de bytes não alocados que já existiram no heap desde que o aplicativo FreeRTOS começou a ser executado;
    - Retorna quantos bytes que faltaram para um stack no momento mais critico;
- Disponível quando HEAP 4 ou HEAP 5 é usado
<br>[↑](#Sumário) 

## Gerenciamento de tarefas
- Este é o capítulo mais detalhado 
<br>[↑](#Sumário) 

### Funções da Tarefa
- Tarefas são implementadas como funções C;
- Essa função sempre deve retornar void e receber um parâmetro de ponteiro void
~~~c
    void ATaskFunction(void *pvParameters);
~~~
- Cada tarefa é um pequeno programa em seu próprio direito;
- Possui um ponto de entrada, normalmente será executado para sempre em um loop infinito;
- As tarefas do FreeRTOS não devem ter permissão para trornar de sua função de implementação de forma alguma, não devendo conter a instrução "return"
- Caso uma tarefa não seja mais necessária, ela deverá ser excluida;
- Uma unica definição de tarefa pode ser usada para criar qualquer número de tarefas
    - Cada tarefa criada é uma instancia de execução separada, com sua propria pilha e sua própria cópia de quaisquer variáveis 
- Esqueleto de uma tarefa
~~~c
    void ATaskFunction(void *pvParameters) {
        /*
         * Variaveis podem ser declaradas como em uma função normal.
         * Tarefas criadas dinamicamente podem fazer uso de uma mesma função para ser criada, caso a criação da tarefa seja estática
         * apenas pode ser criada uma tarefa por função
         */
         uint32_ ulVariableExample = 0;

        //Uma tarefa normalmente será implementada como um loop infinito 
         for(;;) {
             //O código para implementar a funcionamidade da tarefa fica aqui 
         }
         /*Caso a implementação da tarefa saia do loop acima, a tarefa deve ser excluida antes de chegar ao final de sua função de
          * implementação. O parametro NULL passado para a função API vTaskDelete() indica que a tarefa a ser exclída é a tarefa
          * em que a função esta sendo executada 
          */
          vTaskDelete(NULL);
    }
~~~
<br>[↑](#Sumário) 

### Estados de Tarefas de Nível Superior 
- Um aplicativo pode consistir em muitas tarefas;
- Uma tarefa pode existir em dois estados possiveis, "em execução" e "não execução". 
    - A condição de "não execução" possui vários subestados 
- Quando uma tarefa esta no estado "Running" o processador esta executando código da tarefa;
- Quando uma tarefa esta no estado "não execução", a tarefa esta inativa, seu status foi salvo e esta pronto para retornar a execução na proxima vez em que o agendador decidir que deve entrar no estado "em execução (Running);
- Quando uma tarefa retoma a execução, ela faz a partir da instrução que esta prester a ser executada antes de sair do estado "em execução"
- Quando uma tarefa sai do estado de "não execução" para o estado de "execução" diz-se que foi "comutada";
- Quando uma tarefa sai do estado de "execução" para o estado de "não execução" diz-se que foi "desativada"
- O agendador do FreeRTOS é a única entidade que pode ativar e dsativar uma tarefa.

### Função da API xTaskCreate()
- As taefas são criadas usando a função da API FreeRTOS xTasCreate(), sendo provavelmente a mais complexa de todas as funções da API;
- Estrutura da função
~~~c
    BaseType_t xTaskCreate(
        TaskFunctio_t pvTaskCode,
        const char *,
        uint'6_t usStackDepth,
        void *pvParameters,
        uBaseType_t uxPriority,
        TaskHandle_t *pxCreatedTask
    )
~~~

|Nome do Parametro| Descrição|
|:---|:---|
|pvTaskCode| O parametro pvTaskCode é simplesmente um ponteiro para a função que implementa a tarefa (na verdade, apenas o nome da função|
|constpcName|Um nome descritivo para a tarefa. Não é usado pelo FreeRTOS de forma alguma. Ele é incluído apenas como um auxílio de depuração. A constante definida pelo aplicativo configMAX_TASK_NAME_LEN define o comprimento máximo que um nome de tarefa pode ter. Fornecer uma string maior que esse valor máximo resultará na string sendo truncada|
|usStackDepth| Cada tarefa possui sua própria pilha excluisva que é alocada pelo kernel para a tarefa quando a mesma é criada. O valor de usStackDepth informa ao kernel o tamanho da pilha. O valor especifica o numero de palavras que a pilha pode conter, não o numero de bytes. Em um sistema de 32bits todo valor passado internamente é multiplicado por 4. O tamanho de uma pilha multiplicado pela largura nçao deve exceder o valor maximo que pode estar contido em uma variavel do tipo uint16_t. O tamanho da pilha usada pela tarefa Idle é definida pela constante definida pelo aplicativo em configMINIMAL_STACK_SIZE. O valor atribuido a essa constante no aplicativo de demonstração do FreeRTOS para a arquitetura do processador em uso é o minimo recomendado para qualquer tarefa. Uma tarefa nunca deve utilizar mais espaço do que o necessário. Não existe uma maneira fácil de determinar o espaço de pilha necessário para uma tarefa. É possivel calcular, mas a maioria dos usuarios simplesmente atribuirá o que ele achar ser um valor razoavel. Esse espaço é consumido da RAM, logo, um recurso escaço |
|pvParameters|As funções de tarefa aceitam um parametro do tipo ponteiro void. O valor atribuito a pvParameters é o valor passado para a tarefa.|
|uxPriority|Define a prioridade na qual a tarefa será executada. As prioridades podem ser atribuidas de 0 que é a prioridade mais baixa, (configMAX_PRIORITIES -1) sendo o valor da prioridade mais alta. configMAX_PRIORITIES é uma constante definida pelo usuário. Passar um valor uxPriority acima do configurado resultará na no valor maximo -1 configurado na constante|
|pxCreatedTask|pxCreatedTask pode ser usado para passar um identificador para a tarefa que esta sendo criada. Esse identificador pode ser usado para fazer uma referência a tarefa em chamadas de API, caso não seja utilizado pode-se atribuir o valor NULL|

- Valor devolvido da função xTaskCreate()
    - pdPASS: Indica que a tarefa foi criada com sucesso
    - pdFALSE: Indica que a tarefa não foi criada porque nao há memória heap suficiente disponivel para o FreeRTOS;
<br>[↑](#Sumário) 

### Criando Tarefas 
- Exemplo de criação de uma tarefa 
~~~c
    void vTask1(void *pvParameters) {

        const char *pcTaskName = "A tarefa 1 esta em execução \r\n";

        //volatile faz com quem o compilador não otimize a a variavel 
        volatile  uint32_t ulCount;

        for(;;) {
            vPrintString(pcTaskName);

            //implementação de atraso grosseira 
            for(ulCount = 0; ulCOunt < mainDELAY_LOOP_COUNT; ulCount++);
        }
    }

    int main(void){

        /*
        Cria tarefa, em um aplicativo real deve verificar o valor de retorno de TaskCreate para garantir que a tarefa foi criada com sucesso
        */
        xTaskCreate(
            vTask1, /* Ponteiro para a função que implementa a tarefa */
            "Task1", /* Nome de texto para a tarefa. Isso é para facilitar apenas depuração */
            1000, /* Profundidade da pilha - microcontroladores pequenos usarão muito menos pilha do que esse valor */
            NULL, /* Não usa o parametro tast  */
            1, /* prioridade de nivel 1 */
            NULL /* Não usa identificador de tarefa */
        );
        /* Inicia o escalonador para que as tarefas comecem a ser executada */
        vYaskStartScheduler();

        /*Tudo dando certo a tarefa nunca chegará aqui, se chegar ocorreu stack over flow */
        for(;;);
    }

~~~
- Apenas uma tarefa pode existir no estado "Em Execução" a qualquer momento. Quando uma tarefa entra no estado "Em execução" as demais devem entrar em estado de "não execução"
    ![tarefas em execucao](/img/0001.png)

- É possivel chamar a função xTaskCreate() dentro de outra tarefa 
<br>[↑](#Sumário) 

#### Utilizando parametro da Task 
- A task criada possui um paramtro passado que pode ser utilizado ("pvParameters")

~~~c
    void vTask(void *pvParameters) {
        const char *pcTaskName;
        volatile uint32_t ulAux;

        pcTaskName = (char *) pvParameters;

        for(;;) {
            vPrint(pcTaskName);
            
            //delay ou bloqueio de tarefa
        }
    }

    static const char *pcTextForTask1 = "A tarefa 1 esta em execucao\r\n";
    static const char *pcTextForTask2 = "A tarefa 2 esta em execucao\r\n";

    int main(void) {
        xTaskCreate(
            vTaskFunction, /* ponteiro para a função */
            "Tarefa1", /* Texto nome task para depuracao */
            1000, /* Tamanho da pilha para tarefa */
            (void *) pcTextForTask1, /* parametro passado por pvParameters */
            1, /* prioridade da tarefa */
            NULL /* identificador da tarefa não utilizado nesse momento */
        );

        xTaskCreate(
            vTaskFunction, /* ponteiro para a função */
            "Tarefa2", /* Texto nome task para depuracao */
            1000, /* Tamanho da pilha para tarefa */
            (void *) pcTextForTask2, /* parametro passado por pvParameters */
            1, /* prioridade da tarefa */
            NULL /* identificador da tarefa não utilizado nesse momento */
        );

        vTaskStartScheduler();
    } 
~~~

- Desta forma é possivel com apenas uma função de tarefa, essa poder ter mais de uma tarefa criada, apresentando comportamentos diferentes;
<br>[↑](#Sumário) 

### Prioridade de Tarefas
- O parametro uxPriority da função xTaskCreate() atribui uma prioridade inicial a tarefa que esta sendo criada;
- A prioridade pode ser alterada depois que o agendador foi iniciado usando a função vTaskPrioritySet();
- O número máximo de prioridades é definido pela constante de configuração  "configMAX_PRIORITIES" localizada dentr "FreeRTOSConfig.h"
- Valores baixo denotam tarefas de baixa prioridade, sendo 0 a tarefa de mais baixo nível;
- O intervalo de prioridade vai de 0 a (configMAX_PRIORITIES - 1);
- Várias tarefas podem ter o mesmo nível de prioridade 
- Se o método de arquitetura for otimizada, recomenda-se utilizar não mais que 32 prioridades. Quanto maior o numero de tarefas, maior o consumo de memória RAM
- O agendador do FreeRTOS sempre garantirá que a tarefa de prioridade mais alta pode ser executada 
<br>[↑](#Sumário) 

### Medição de tempo e Interrupção de Tick
- Cada tarefa é executada por uma fatia, entrand em execução no início de uma faixa de tempo e saindo do estado "em execução" no final de uma faixa de tempo;
- Para poder selecionar a próxima tarefa a ser executada, o próprio agendador deve executar no final de cada fatia de tempo;
- Uma interrupção periódica, chamada interrupção de tick é utilizada para esse propósito;
A duração da fatia de tempo é efetivamente definida pela frequencia de interrupção de tique, que é configurada pela constante de tempo de compilação "configTICK_RATE_HZ" definido "FreeRTOSConfig.h"
    - Se configTICK_RATE_HZ é definidio como 100Hz a fatia de tempo será de 100milissegundos
- Exemplo de execução em duas tarefas:<br>
    ![exemplo](/img/0002.png)
- As chamadas da API FreeRTOS sempre especificam o tempo em multiplos de períodos de tiques. 
<br>[↑](#Sumário) 

#### pdMS_TO_TICK()
- Converte um tempo especificado em milissegundos em tempo especificado em ticks
~~~c
    //retorna o valor convertido de milissegundos para ticks
    TickType_t xTimeInTicks = pdMS_TO_TICKS( valor_em_ms );
~~~
- Os aplicativos do usuário não precisam considerar estouros ao especificar períodos de atraso, pois a consistência de tempo é gerenciada internamente pelo FreeRTOS;
<br>[↑](#Sumário) 

### Expandindo o estado "Não em execução"
- Tarefas de processamento contínuo, ou seja que não são bloqueadas de alguma forma, possuem utilidade limitada, pois só podem ser criadas com prioridades mais baixas para que tenham alguma sentido, caso utilizem altas prioridades não permitiram que as demais tarefas de prioridades menores sejam executadas 
- Para tornar as tarefas úteis, elas devem ser reescritas para serem orientadas a eventos. Tarefas orientadas a eventos tem processamento a ser executado somente após a ocorrencia de algum evento que a acione.
- Usar tarefas orientadas a eventos significa que as tarefas podem ser criadas em diferentes prioridades sem que as tarefas de prioridade mais alta abafem o funcionamento de tarefas de prioridades mais baixas 
<br>[↑](#Sumário) 

### Estados de tarefas 

#### Estado Bloqueado
- Diz-se que uma tarefa que esta aguardando um evento esta no estado "Bloqueado", que é um subestado do estado "Em não Execução"
- As tarefas podem entrar no estado Bloqueado para aguardar dois tipos diferentes de eventos: 
    - Eventos temporais(relacionados a tempo): o evento sendo um periodo de atraso.
        - Exemplo: Uma tarefa pode entrar no estado Bloqueado para aguardar a passagem de 10ms
    - Evento de sincronização: onde os eventos se originam de outra tarefa ou interrupção. 
        - Exemplo: Uma tarefa pode entrar no estado Bloqeuado para aguardar a chegada de dados de uma fila. Eventos de sincronização abrangem uma ampla variedade de tipos de eventos 
        - Filas, semáforos, mutex, eventgroups e notificações podem ser usados para criar eventos de sincronização 
<br>[↑](#Sumário) 

#### Estado Suspenso 
- Também é um sub estado de "em não execução". Tarefas no estado Suspenso não estão disponiveis para o agendador. Aúnica maneira de entrar em estado Suspenso é por meio da chamada  de vTaskSuspend(), sendo a unica saída por meio de uma chamada de vTaskResume ou xTaskResumeFromISR(). A maioria dos aplicativos não usa o estado Suspenso 
<br>[↑](#Sumário) 

#### Estado Pronto
- As tarefas que não estaõ no estado "Não executando", mais não estão bloqeuadas ou suspensas, são consilderadas no estado Pronto. Eles podems er executadas e, portanto, "prontas" para serem executadas, entretanto não estão no estado "execução" no momento.
<br>[↑](#Sumário) 

#### Fluxograma de Estados 
![Estados](/img/0003.png)

### Usando o estado Bloqueado para criar um atraso 
- A função vTaskDelay() esta disponível somente quando INCLUDE_vTaskDelay é definido com o valor 1 em FreeRTOSConfig.h;
- vTaskDelay() coloca a tarefa de chamada no estado Bloqueado para um número fixo de interrupções de tique. A tarefa não usa nenhum tempo de processamento enquanto estiver no estado Bloqueado, portanto, a tarefa só usa o tempo de processamento quando realmente há trabalho a ser feito;
<br>[↑](#Sumário) 

#### vTaskDelay()
- void vTaskDelay(TickType_t xTicksToDelay)

|Parâmetro| Descrição|
|:---|:---|
|xTicksToDelay| O número de interrupções de tique que a tarefa de chamada permanecerá no estado bloqueado antes de ser transferida de volta para o estado Pronto.<br> A macro pdMS_TO_TICKS() pode ser usada para converter um tempo especificado em milissegundos em um tempo especificado em ticks|

- Exemplo de uso 
~~~c
    void vTaskFunction(void *pvParameters) {
        char *pcTaskName;
        const TickType_t xDelay250ms = pdMS_TO_TICKS( 250 );

        pcTaskName = (char*)pcParameters;

        for(;;) {
            vPrintString( pcTaskName );

            vTaskDelay( xDelay250ms );
        }
    }
~~~
<br>[↑](#Sumário) 