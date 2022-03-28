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
|TickType_t| O FreeRTOS configura uma interrupção periódica chamada interrupção de tique. <br>O numero de Interrupções de tick que ocorrem desde de que o aplicativo FreeRTOS foi iniciado é chamado de contagem de Ticks. Acontagem de ticks é usada como medida de tempo.<br> TickType_t é o tipo de dados usado para manter o valor da contagem de ticks e para especificar os tempos.<br>|




PAREI PAGINA  7/20 DA PARTE 3
