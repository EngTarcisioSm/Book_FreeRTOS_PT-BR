# Dominando o kernel em tempo real do FreeRTOS Um guia prático do tutorial (Resumo)
# Ver. 0.0.1

## Observação 
- Material baseado em "Mastering_the_FreeRTOS_Real_Time_Kernel-A_Hands-On_Tutorial_Guide" do autor Richard Barry (2016);
- Material abaixo de livre reprodução desde que mantido o nome do autor.
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


___

## Multitarefa em pequenos sistemas embarcados 
### Sobre o FreeRTOS

- O FreeRTOS é de propriedade, desenvolvido e mantido exclusivamente pela Real Time Engineers Ltd;
- O FreeRTOS é uma aplicação em tempo real ideal para microcontroladores ou pequenos microprocessadores;

- Requisitos de tempo real suaves estabelecem um prazo para o seu cumprimento, entretanto a violação desse prazo não torna o sistema inútil;
- Requisitos de tempo real rígidos são aqueles que estabelecem um prazo e a violação do mesmo resulta em uma falha do sistema;
- O FreeRTOS é um kernel em tempo real sobre o qual aplicativos embarcados podem ser desenvolvidos para atender a requisitos rígidos de tempo real. Torna possivel que aplicativos sejam organizados como uma coleção de threads de execuções independentes, mesmo que o sistema ao qual esteja sendo executado possua apenas um núcleo. O kernel decide qual thread deve ser executado examinando a prioridade que foi atribuida a cada thread peo desenvolvedor do aplicativo. Pode-se aplicar prioridades mais altas a threads que necessitam atender a rigidos requisitos de tempo e prioridades mais baixas para threads mais flexiveis no que tange seu tempo de execução;

### Proposta de valor
- O FreeRTOS é gratuito para uso em aplicativos comerciais sem qualquer requisito de expor seu código-fonte proprietário;

### Terminologia 
- No FreeRTOS, cada thread é chamada de tarefa;

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
    - Event Groups
    - Verificação de recursos utilizados
    - Dados estatisticos de tarefas em execução
    - Licenciamento comercial opcional e suporte 

### Licenciamento do FreRTOS
- Usuários do FreeRTOS mantêm a propriedade de sua propriedade intelectual;


## Introdução
- O FreeRTOS é distribuido como um único arquivo zip que contém todas os ports oficiais do FreeRTOS e um grande número de exemplo pré- configurados

### Entendendo a distribuição FreeRTOS

#### Definição Port FreeRTOS
- O FreeRTOS pode ser compilado por aproximadamente 20 compiladores diferens e pode ser executado em mais de 30 arquiteturas de processadores diferentes. 