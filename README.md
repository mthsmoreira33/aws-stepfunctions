# Criando um Assistente de Delivery com AWS Step Functions e Bedrock
# Desafio
Criar um Assistente de Delievery usando as Máquinas de Estado do AWS Step Functions e boas práticas de Prompt Engineering para integrar com o Bedrock.

## O que é AWS Step Functions?
O Step Functions é uma ferramenta low-code no estilo "clica e arrasta" com o objetivo de criar fluxos de trabalho visuais para gerenciar componentes em aplicações distribuídas.

Permitindo que os microsserviços se comuniquem entre si.

## Situação Problema
Desenvolver uma aplicação distribuída de um sistema de Delivery.
Subdividida em:
- Uma API de User Management: Que guarda o histórico de pedidos e os cupons disponíveis para um usuário;
- Um gerenciador de Push Notifications sobre o estado dos pedidos para os clientes e para o restaurante;
- Uma API de Gerenciamento do Estado do Pedido, que envia para o gerenciador de push notifications uma mensagem sobre o estado atual do pedido (se foi realizado ou concluído por exemplo).

![image](https://github.com/user-attachments/assets/a1f468a6-7604-4890-bda4-1803db7ffcdf)


## Serviços de Mensageria
Cada serviço da aplicação funciona de forma independente. A API de User Management não depende do gerenciador de Push Notification nem da API de Gerenciamento de Estado de Pedido.
Mas esses serviços precisam se comunicar entre si por serviços de mensagerias.

Um exemplo de um Messenger Broker (Software de Serviços de Mensageria) é o RabbitMQ.

### Publisher
![image](https://github.com/user-attachments/assets/200397e9-042a-4263-a7ce-56737fda5cee)

O Publisher é o agente do serviço de mensageria que envia as informações de um serviço para o outro. Ex.:
Nesta aplicação, precisariamos de um publisher que enviasse a mensagem da API de Estado do Pedido para o Gerenciador de Notificações para entregar ao usuário o estado do pedido (por exemplo, o Ifood entregando para o usuário uma notificação indicando que o pedido já está saindo do restaurante).

### Subscriber

O Subscriber por outro lado é o agente que se inscreve para receber uma mensagem. Nessa aplicação por exemplo, o Gerenciador de Notificações é um subscriber que recebe as informações das APIs.

## E onde entra o AWS Step Functions?

O AWS Step Functions permite que ao usuário gerenciar componentes (incluindo os publishers e subscribers) dessas aplicações distribuídas em microsserviços

![image](https://github.com/user-attachments/assets/5a70da1d-a0bf-4a68-8679-c6176cf4f71a)

## Padrões de Orquestrações comuns do Step Functions

### Sequência
![image](https://github.com/user-attachments/assets/475b079d-6aff-46a9-b589-0d05ff84525d)

### Repetição
![image](https://github.com/user-attachments/assets/8bb3a9f6-fd05-4081-a5c1-e11261863978)

### Paralelismo
![image](https://github.com/user-attachments/assets/fc64fbd1-ac83-4bd7-a4bb-79843a52f712)

### Condicional
![image](https://github.com/user-attachments/assets/db9bac79-4945-4be9-a011-5969e81d1cbd)

### Try/catch/finally
![image](https://github.com/user-attachments/assets/cd538c23-ac1e-4dea-8e92-508223feabe5)

## Workflows
Os fluxos de trabalho da AWS são escritos usando Amazon States Language (ASL) e podem ser usados para orquestrar multiplos serviços AWS (como Lambda, Bedrock etc...)

## Estados do Step Functions
### Choice
Adiciona uma condicional "if-else"<br>
![image](https://github.com/user-attachments/assets/777d608d-1209-4c46-894c-9d54df773e0b)

### Parallel
Adiciona branches paralelas<br>
![image](https://github.com/user-attachments/assets/a9524f49-16ff-4b4c-9ea6-07e525c26a15)

### Map
Adiciona um loop for-each<br>
![image](https://github.com/user-attachments/assets/fc71f538-7499-499b-b6ea-f4b5e9bfaace)

### Pass
Transforma dados ou atos em "tapa-buracos"<br>
![image](https://github.com/user-attachments/assets/f4be2714-81e6-432d-9dfb-f565c887401b)


### Wait
Tem um tempo de delay antes de prosseguir com o workflow<br>
![image](https://github.com/user-attachments/assets/c3ea59ca-f6ad-4f15-81e8-2e324495547b)

### Success
Para o workflow e assinala como "sucesso"<br>
![image](https://github.com/user-attachments/assets/3c4a1d76-37e6-4b51-9afe-7890d223e154)


### Fail
Para o workflow e assinala como "falha"<br>
![image](https://github.com/user-attachments/assets/09509867-d1d3-4f8e-adfc-c72f78397498)

## Tasks
As Tasks são estados que chamam outros serviços da AWS. Ex.:
Invoke (chama o Lambda), Publish (chama o SNS), StartJobRun (chama o Glue)<br>
![image](https://github.com/user-attachments/assets/54e4b93b-6750-4ea0-a65a-56ef37fabe59)


## Workflow Studio
O Workflow Studio é a interface do AWS Step Functions para criar máquinas de estado (assim como são chamados os fluxos de trabalho no AWS Step Functions).<br>
![image](https://github.com/user-attachments/assets/6eb2c2a6-7213-4447-a7df-2899c84dd2ab)

## ASL (Amazon States Language)
O ASL é uma linguagem estruturada baseada em JSON usada para definir máquinas de estado, colecionando estados de Task, de Choice e de Success ou Fail por exemplo.Ex.:<br>
![image](https://github.com/user-attachments/assets/27748757-d917-4115-8898-e6f2f0d5ec22)
<br>
O estado "Hello world" é um estado do tipo Choice (condicional), que passa para o próximo estado quando a variável "IsHelloWorldExample" é ``true`` e não passa para o próximo estado quando a mesma variável é ``false``. Por padrão o valor é ``true``.

