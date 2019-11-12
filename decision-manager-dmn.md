# Instruções da capacitação técnica Red Hat Decision Manager

## Links importantes

Página do RH-DM

https://developers.redhat.com/products/red-hat-decision-manager/overview

Documentação do Decision Manager

https://access.redhat.com/documentation/en-us/red_hat_decision_manager

Documentação de como criar regras com DMN

https://access.redhat.com/documentation/en-us/red_hat_decision_manager/7.5/html-single/designing_a_decision_service_using_dmn_models/index

Documentação do RH-PAM

https://access.redhat.com/documentation/en-us/red_hat_process_automation_manager

Página do RH-PAM

https://developers.redhat.com/products/rhpam/overview

Projeto Upstream do RH-PAM e RH-DM

https://www.jbpm.org/index.html

## Construindo modelos de decisão

### Criando projeto

A instalação limpa conterá apenas o Space `MySpace`.

![imagem](imagens/cap-1.png)


Crie um novo `Space` com o nome `analise-de-credito`.

![imagem](imagens/cap-2.png)

![imagem](imagens/cap-3.png)

Dentro do `Space` crie o projeto `Oportunidades`

![imagem](imagens/cap-4.png)

O projeto deve ter as seguintes configurações

```
Nome: Oportunidades
Descrição: Avalia oportunidades para o cliente conforme as transações.
Group ID: com.analise.credito
Artifact ID: oportunidades
Versão: 1.0.0
```

![imagem](imagens/cap-5.png)

Com o projeto criado é possível adicionar os objetos.

![imagem](imagens/cap-6.png)

### Criando o primeiro DMN

Crie um novo DMN (Decision Model Notation)

![imagem](imagens/cap-7.png)

O nome do objeto deve ser Analise e o pacote com.analise.credito.oportunidades.

![imagem](imagens/cap-8.png)

O editor do diagrama irá aparecer

![imagem](imagens/cap-9.png)

Adicione os objetos `Perfil` e `Cliente`  da seguinte forma: 

![imagem](imagens/cap-10.png)

Na aba `Data Types` crie os tipos `tCliente` e `tPerfil`.

![imagem](imagens/cap-11.png)

![imagem](imagens/cap-12.png)

Estrutura do `Cliente`

| Nome          | Tipo          |
| ------------- | ------------- |
| tCliente      | structure     |
| score         | number        |
| tempo         | number        |

Estrutura do `Perfil`

| Nome                | Tipo          |
| ------------------- | ------------- |
| tPerfil             | structure     |
| nome                | string        |
| limiteCartao        | number        |
| limitePagamento     | number        |
| limiteTransferencia | number        |

Altere o Data Type do Cliente e Perfil nas propriedades.

![imagem](imagens/cap-13.png)

![imagem](imagens/cap-14.png)

No diagrama clique em Perfil e em *Edit*. Selecione *Decision Table* como tipo de lógica.

![imagem](imagens/cap-15.png)

Prencha as colunas input como segue.

![imagem](imagens/cap-16.png)

Deixe as colunas de output e preencha as informações como a imagem.

![imagem](imagens/cap-17.png)

A tabela de decisão analisa o score e o tempo do cliente para definir o seu perfil (bronze, prata, ouro e diamante) e seus limites.

PS: **Salve o DMN**

### Testando DMN

PS: **Na lista de assets, mande construir o projeto.**

Crie um novo objeto *Test Scenario*

![imagem](imagens/cap-18.png)

Com as seguintes informações:

```
Test Scenario: AvaliacaoTeste
Pacote: com.analise.credito.oportunidades
Source Type: DMN
DMN Asset: Avaliacao.dmn
```

![imagem](imagens/cap-19.png)

A estrutura será criada com base no DMN

![imagem](imagens/cap-20.png)

Preencha as informações de teste e no botão de play execute os testes

![imagem](imagens/cap-21.png)

### Criando uma função

Ajuste o diagrama para acrescentar um Bussiness Knowledge Model chamado CalculaLimite

![imagem](imagens/cap-215.png)

Clique no CalculaLimite e em *Edit*, deixe os parâmetros e linhas da função.

![imagem](imagens/cap-22.png)

![imagem](imagens/cap-23.png)

Os fatores (score, tempo e categoria) influenciam nos limites do cliente, logo cada um deles gera uma pontuação que no final é somada. Em cada linha adicionar uma tabela de decisão e preencher conforme a imagem.

Fator Score

![imagem](imagens/cap-24.png)

Fator Tempo

![imagem](imagens/cap-25.png)

Fator Categoria

![imagem](imagens/cap-26.png)

O resultado é a soma dos 3 fatores, o que pode ser calculado usando uma expressão literal

![imagem](imagens/cap-27.png)

![imagem](imagens/cap-28.png)

Atualize a tabela de decisão do Perfil incluindo a chamada para a função.

![imagem](imagens/cap-29.png)

Após isso salve o DMN e na lista de assets, mande construir o projeto. Após construído verifique os testes, caso alguns falhem é preciso corrigir de acordo com a lógica implementada na função `CalculaLimite`.

![imagem](imagens/cap-30.png)

### Evoluindo o DMN

Para tornar mais complexo o DMN iremos adicionar o input de transação e a decisão de Oportunidade, junto com uma nova função.

![imagem](imagens/cap-31.png)

É preciso criar o tipo `tTransacao`, na aba *Data types*

![imagem](imagens/cap-32.png)

Estrutura do `Transacao`

| Nome          | Tipo          |
| ------------- | ------------- |
| tTransacao    | structure     |
| valor         | number        |
| tipo          | string        |

Após atualize o *Data Type* da Transacao e da Oportunidade nas propriedades.

![imagem](imagens/cap-33.png)

![imagem](imagens/cap-34.png)

Edite a função `PassouLimite` conforme as imagens

![imagem](imagens/cap-35.png)

![imagem](imagens/cap-315.png)

Adicione uma tabela de decisão como expressão

![imagem](imagens/cap-36.png)

E preencha a tabela  conforme as imagens

![imagem](imagens/cap-37.png)

Para cada tipo de transação é verificado se o valor passou do limite do cliente.

![imagem](imagens/cap-38.png)

Após implementada a função editar a Oportunidade, a mesma é do tipo contexto.

![imagem](imagens/cap-39.png)

Para definir se existe uma Oportunidade é avaliado o tipo de transação e se ela passou do limite. Para cada tipo de transação há uma oportunidade, oferecer um upgrade ou um empréstimo.

![imagem](imagens/cap-40.png)

### Criando novos testes

Crie uma novo objeto de cenário de teste

![imagem](imagens/cap-41.png)

![imagem](imagens/cap-42.png)

Com as seguinte informações

```
Test Scenario: AvaliacaoTeste2
Pacote: com.analise.credito.oportunidades
Source Type: DMN
DMN Asset: Avaliacao.dmn
```

Será criada a tabela de entradas e saídas

![imagem](imagens/cap-43.png)

Preencher a tabela com cenários de teste. É possível colocar valores aleatórios para identificar os retornos do DMN.

![imagem](imagens/cap-44.png)

![imagem](imagens/cap-45.png)

### Consumindo o DMN via REST

Verificar nas propriedades do projeto a versão e nome do artefato

![imagem](imagens/cap-46.png)

Na lista de assets clicar em `Deploy`.

![imagem](imagens/cap-47.png)

Clicar no Menu, e Servidores de Execução

![imagem](imagens/cap-48.png)

Na tela de servidores deverá haver o deployment-unit criado e a url do serviço

![imagem](imagens/cap-49.png)

Através dessa url é possível consultarmos os DMNs disponíveis e testá-los

Devolver informações sobre o dmn

[GET] http://localhost:8080/kie-server/services/rest/server/containers/oportunidades_1.0.0/dmn

![imagem](imagens/cap-50.png)

```sh
curl --request GET \
  --url http://localhost:8080/kie-server/services/rest/server/containers/oportunidades_1.0.0/dmn \
  --header 'authorization: Basic YWRtaW46cjNkaDR0MSE=' \
  --header 'content-type: application/json' \
```

Executa o dmn

[POST] http://localhost:8080/kie-server/services/rest/server/containers/oportunidades_1.0.0/dmn

![imagem](imagens/cap-51.png)

```sh
curl --request POST \
  --url http://localhost:8080/kie-server/services/rest/server/containers/oportunidades_1.0.1/dmn \
  --header 'authorization: Basic YWRtaW46cjNkaDR0MSE=' \
  --header 'content-type: application/json' \
  --data '{
  "model-namespace" : "https://kiegroup.org/dmn/_0405571C-C3FF-4EE4-A710-940701800275",
  "model-name" : "Avaliacao",
  "dmn-context" :
  {
    "Cliente" :
    {
  	   "score" : 70,
			 "tempo": 2
    },
  	"Transacao" :
    {
  		"valor" : 6000,
  		"tipo" : "PAGAMENTO"
  	}
  }
}'
```
