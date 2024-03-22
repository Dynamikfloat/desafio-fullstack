# Instruções e Regras para Desafio Fullstack Dynamik

## Resumo

- O Sistema consiste em um registo de desenvolvedores.
- O desenvolvimento será feito na tecnologia em que desejar, será de interesse da Dynamik o motivo que levou a escolha da tecnologia.

## Endpoints

As APIs precisam expor 3 endpoints:

- `POST /devs` – para criar um recurso de desenvolvedor.
- `GET /devs` – para consultar uma lista de desenvolvedores.
- `GET /devs/[:id]` – para consultar um recurso criado com a requisição anterior.
- `GET /devs?terms=[:termo da busca]` – para fazer uma busca por desenvolvedores.

### Criação de Devs

`POST /devs`

Deverá aceitar uma requisição em formato JSON com os seguintes parâmetros:

| atributo       | descrição                                                                             |
| -------------- | ------------------------------------------------------------------------------------- |
| **nickname**   | obrigatório, único, string de até 32 caracteres.                                      |
| **name**       | obrigatório, string de até 100 caracteres.                                            |
| **birth_date** | obrigatório, string para data no formato YYYY-MM-DD (ano, mês, dia).                  |
| **stack**      | opcional, vetor de string com cada elemento sendo obrigatório e de até 32 caracteres. |

Para requisições válidas, sua API deverá retornar status code 201 - created junto com o header "Location: /devs/[:id]" onde [:id] é o id – em formato UUID com a versão a seu critério – do desenvolvedor que acabou de ser criado. O conteúdo do corpo fica a seu critério; retorne o que quiser.

Exemplos de requisições válidas:

```json
{
  "nickname": "judit",
  "name": "Judit Polgár",
  "birth_date": "1976-07-23",
  "stack": ["C#", "Node", "Oracle"]
}
```

```json
{
  "nickname": "leo",
  "name": "Leonardo Barreto",
  "birth_date": "1986-09-05",
  "stack": null
}
```

Para requisições inválidas, o status code deve ser 422 - Unprocessable Entity/Content. Aqui, novamente, o conteúdo do corpo fica a seu critério.

Exemplos de requisições inválidas:

```json
{
  "nickname": "judit", // caso "judit" já tenha sido criado em outra requisição
  "name": "Judit Polgár",
  "birth_date": "1976-07-23",
  "stack": ["C#", "Node", "Oracle"]
}
```

```json
{
  "nickname": "leo",
  "name": null, // não pode ser null
  "birth_date": "1986-09-05",
  "stack": null
}
```

```json
{
  "nickname": null, // não pode ser null
  "name": "Judit Polgár",
  "birth_date": "1976-07-23",
  "stack": null
}
```

Para o caso de requisições sintaticamente inválidas, a resposta deverá ter o status code para 400 - bad request. Exemplos:

```json
{
  "nickname": "nickname",
  "name": 1, // name deve ser string e não número
  "birth_date": "1985-01-01",
  "stack": null
}
```

```json
{
  "nickname": "nickname",
  "name": "name",
  "birth_date": "1985-01-01",
  "stack": [1, "PHP"] // stack deve ser um array de apenas strings
}
```

### Lista de Desenvolvedores

`GET /devs`
Deverá retornar uma lista de desenvolvedores e poderá retornar apenas os 20 primeiros registros para facilitar a implementação.

O `header` de resposta deve conter o número total de registos na base de dados. 

O status code deverá ser sempre 200 - Ok, mesmo para o caso da listagem não retornar resultados (vazio).

Exemplos: Listagem com recursos existentes em sua aplicação:

```json
[
  {
    "id": "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
    "nickname": "judit",
    "name": "Judit Polgár",
    "birth_date": "1976-07-23",
    "stack": ["C#", "Node", "Oracle"]
  },
  {
    "id": "5ce4668c-4710-4cfb-ae5f-38988d6d49cb",
    "nickname": "leo",
    "name": "Leonardo Barreto",
    "birth_date": "1986-09-05",
    "stack": null
  }
]
```

Listagem sem recursos existentes em sua aplicação:

```json
[]
```

### Detalhe de um Desenvolvedor

`GET /devs/[:id]`

Deverá retornar os detalhes de um desenvolvedor caso este tenha sido criado anteriormente. O parâmetro [:id] deve ser do tipo UUID na versão que escolher. O retorno deve ser como os exemplos a seguir.

```json
{
  "id": "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
  "nickname": "judit",
  "name": "Judit Polgár",
  "birth_date": "1976-07-23",
  "stack": ["C#", "Node", "Oracle"]
}
```

```json
{
  "id": "5ce4668c-4710-4cfb-ae5f-38988d6d49cb",
  "nickname": "leo",
  "name": "Leonardo Barreto",
  "birth_date": "1986-09-05",
  "stack": null
}
```

Note que a resposta é praticamente igual ao payload de criação com o acréscimo de `id`. O status code para desenvolvedores que existem deve ser 200 - Ok. Para recursos que não existem, deve-se retornar 404 - Not Found.

### Busca de Desenvolvedores

`GET /devs?terms=[:termo da busca]`

Dado o `termo da busca`, a resposta deverá ser uma lista que satisfaça o termo informado estar contido nos atributos `nickname`, `name`, e/ou elementos de `stack`. A busca não precisa ser paginada e poderá retornar apenas os 20 primeiros registros resultantes da filtragem para facilitar a implementação.

O status code deverá ser sempre 200 - Ok, mesmo para o caso da busca não retornar resultados (vazio).

Exemplos: Dado os recursos seguintes existentes em sua aplicação:

```json
[
  {
    "id": "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
    "nickname": "judit",
    "name": "Judit Polgár",
    "birth_date": "1976-07-23",
    "stack": ["C#", "Node", "Oracle"]
  },
  {
    "id": "5ce4668c-4710-4cfb-ae5f-38988d6d49cb",
    "nickname": "leo",
    "name": "Leonardo Barreto",
    "birth_date": "1986-09-05",
    "stack": null
  }
]
```

Uma requisição `GET /devs?terms=node`, deveria retornar o seguinte:

```json
[
  {
    "id": "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
    "nickname": "judit",
    "name": "Judit Polgár",
    "birth_date": "1976-07-23",
    "stack": ["C#", "Node", "Oracle"]
  },
  {
    "id": "a3f0f161-05f6-4f1f-a851-e9bd30e0f62e",
    "nickname": "ana",
    "name": "Ana Barbosa",
    "birth_date": "1985-09-23",
    "stack": ["Node", "Postgres"]
  }
]
```

Uma requisição `GET /devs?terms=polgar`, deveria retornar o seguinte:

```json
[
  {
    "id": "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
    "nickname": "judit",
    "name": "Judit Polgár",
    "birth_date": "1976-07-23",
    "stack": ["C#", "Node", "Oracle"]
  }
]
```

Uma requisição `GET /devs?terms=Python`, deveria retornar o seguinte:

```json
[]
```

Se a query string `terms` não for informada, a resposta deve ter seu status code para 400 - bad request com o corpo que quiser. Ou seja, informar `terms` é obrigatório.

### Database

- Como já mencionado no início do documento, você poderá optar por usar qualquer base de dados relacional (SQL). Fica a seu critério.
- A criação da base de dados e otimizações são mais que incentivados para o sistema ter performance.

### Frontend

- Você tem total liberdade para criar o layout que irá utilizar os endpoints expostos pela sua API.
- UI/UX, cores, disposição dos elementos e responsividade fica de responsabilidade a implementação.
- Use uma framework e tecnologias que desejar.

### Sobre a Entrega

Você precisa fazer o seguinte:

- Criar um repositório git público com o código fonte da sua aplicação.
  - Um `README.md` com informações importantes do projeto, como:
    - Versões das stacks escolhidas.
    - Comandos básicos para ter o sistema a correr do nosso lado.
    - Qualquer outra informação que ache pertinente termos em consideração.
- Enviar para o recrutador com endereço do seu repositório com o teste finalizado.

### Sobre a Avaliação

Pontos que iremos avaliar:

- Commits realizados no repositório.
- Organização do projeto: Pastas, ficheiros, classes e componentes.
- Layout: Cores, disposição dos elementos em tela e UI/UX.
- Bibliotecas auxiliares para a construção do aplicativo.
- Boas práticas como Clean Code e SOLID.

Pontos não obrigatórios, mas que serão valorizados no desafio:

- Ter o projeto em um [Docker](https://docs.docker.com/) ou em algum serviço publico como o [docker hub](https://hub.docker.com/), por exemplo.
- Possíveis otimizações técnicas no backend / base de dados.
- Testes unitários no backend e frontend, dos pontos que entender serem os mais relevantes.
- Testes automatizados end-to-end. Pode usar o Cypress, por exemplo.
