Delivery Tracking — Domain-Driven Design (DDD)
==============================================

Este projeto foi desenvolvido com foco em **Domain-Driven Design (DDD)**, priorizando a modelagem do **domínio de negócio** antes de qualquer preocupação com tecnologia, frameworks ou infraestrutura.

O objetivo principal é que este repositório sirva como **referência prática de DDD** para futuros projetos, especialmente em arquiteturas de microsserviços.

Objetivo do Domínio
-------------------

O domínio modelado neste microserviço é o **rastreamento de entregas (Delivery Tracking)**.

Ele é responsável por representar:

*   O ciclo de vida de uma entrega

*   As regras que governam cada mudança de estado

*   Os dados essenciais para que uma entrega exista e evolua corretamente


Nenhuma regra de negócio relevante deve estar fora da camada de domínio.

Princípios de DDD aplicados no projeto
--------------------------------------

### 1\. O domínio é independente de tecnologia

A camada domain:

*   **Não depende de Spring**

*   **Não depende de banco de dados**

*   **Não depende de frameworks de serialização**

*   **Não conhece HTTP, JSON ou mensageria**


Ela contém apenas:

*   Regras de negócio

*   Estados válidos

*   Comportamentos permitidos


Isso garante que o domínio:

*   Seja testável isoladamente

*   Possa sobreviver a mudanças de tecnologia

*   Seja reutilizável em outros contextos


### 2\. Organização por camadas (não por frameworks)

Estrutura adotada:

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   delivery-tracking   └── src/main/java       └── domain           ├── model           ├── exception       └── api       └── infrastructure   `

#### domain

Contém **o coração do sistema**.

Tudo que está aqui representa conceitos reais do negócio de delivery.

#### api

Camada de entrada:

*   Controllers

*   DTOs

*   Tradução HTTP → domínio


A API **não contém regras de negócio**.

#### infrastructure

Detalhes técnicos:

*   Persistência

*   Integrações

*   Configurações específicas


A infraestrutura **depende do domínio**, nunca o contrário.

Modelagem do Domínio
--------------------

### Aggregate Root: Delivery

A classe Delivery é o **Aggregate Root** do domínio.

Ela é responsável por:

*   Garantir a consistência interna do agregado

*   Controlar o ciclo de vida da entrega

*   Expor comportamentos válidos do negócio


Nada externo deve alterar o estado interno da entrega sem passar por seus métodos.

Exemplos de comportamentos:

*   draft()

*   addItem(...)

*   removeItem(...)

*   place()

*   pickUp(...)

*   markAsDelivered()


Esses métodos representam **ações do negócio**, não simples setters.

### Entidade: Item

Item é uma entidade interna do agregado Delivery.

Características:

*   Possui identidade própria (UUID)

*   Só existe dentro do contexto de uma entrega

*   Não deve ser manipulada diretamente fora do agregado


A criação ocorre via **factory method**:

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Item.brandNew(...)   `

Isso garante que o item já nasça válido segundo o domínio.

### Value Object: ContactPoint

ContactPoint representa um **ponto de contato** (remetente ou destinatário).

Ele é um **Value Object**, o que significa:

*   Não possui identidade própria

*   É definido pelo conjunto de seus valores

*   Representa um conceito imutável do domínio


Ele encapsula:

*   Endereço

*   Nome

*   Telefone


Isso evita espalhar dados soltos e sem significado pelo código.

### Enum: DeliveryStatus

DeliveryStatus representa os **estados possíveis** de uma entrega.

Estados modelados:

*   DRAFT

*   WAITING\_FOR\_COURIER

*   IN\_TRANSIT

*   DELIVERY


Esses estados definem a **máquina de estados do domínio**, e as transições entre eles são controladas exclusivamente pela entidade Delivery.

Validação de regras de negócio
------------------------------

As regras de negócio **não são validadas na API**, nem em services genéricos.

Exemplo de regras que pertencem ao domínio:

*   Uma entrega não pode ser colocada se estiver vazia

*   Uma entrega só pode mudar de estado se estiver em um estado válido

*   A sequência de estados deve ser respeitada


Essas validações vivem dentro do domínio e, quando violadas, disparam exceções específicas.

DomainException
---------------

A classe DomainException representa **erros de negócio**, não erros técnicos.

Ela é utilizada quando:

*   Uma regra do domínio é violada

*   Uma ação inválida é tentada segundo o negócio


Isso diferencia claramente:

*   ❌ erro técnico (infraestrutura, framework)

*   ❌ erro de entrada (API)

*   ✅ erro de domínio (negócio)


Factory Methods
---------------

A criação de objetos do domínio segue o padrão de **factory methods**, como:

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Delivery.draft()   `

Motivos:

*   Garantir que a entidade já nasça em um estado válido

*   Evitar construtores públicos complexos

*   Tornar explícito o estado inicial do objeto


Encapsulamento e consistência
-----------------------------

Decisões importantes:

*   Setters privados (@Setter(AccessLevel.PRIVATE))

*   Listas internas protegidas (Collections.unmodifiableList)

*   Alterações de estado apenas via métodos do domínio


Isso garante que:

*   Nenhuma camada externa corrompa o estado do agregado

*   As invariantes do domínio sejam sempre respeitadas


Por que DDD neste projeto?
--------------------------

DDD foi adotado para:

*   Reduzir acoplamento entre regras de negócio e tecnologia

*   Facilitar evolução do sistema

*   Tornar o código mais expressivo e alinhado ao negócio

*   Criar microsserviços realmente independentes


Este projeto **não é sobre Spring Boot**Este projeto **não é sobre REST**

Ele é sobre **entender, modelar e proteger o domínio**.

Notas para quando eu for desenvolver projetos futuros
-----------------------------------------------------

Sempre que iniciar um novo projeto com DDD:

1.  Modele o domínio primeiro

2.  Crie o Aggregate Root

3.  Defina entidades e value objects

4.  Escreva comportamentos, não setters

5.  Proteja invariantes com exceções de domínio

6.  Só depois pense em API, banco e infraestrutura


📌 **Este repositório é a referência prática e de estudo de DDD para projetos futuros.**