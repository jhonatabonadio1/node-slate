# Autenticação

## Login

```bash
curl "https://barataoapi.herokuapp.com/login"
-H "Content-Type: application/json"
```
> O comando acima retorna JSON estruturado assim:
```json
{
	"token": "QCAefzdIng24u1BSkuu8...",
	"user": {
		"id": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
		"name": "Jhonata Bonadio",
		"email": "jhonbonadio@gmail.com",
		"cpf": "07169305100",
		"phone": "5561999481240",
		"avatarUrl": null,
		"created_at": "2022-01-06T16:08:23.475Z",
		"updated_at": "2022-01-06T16:08:23.475Z"
	}
```

> O comando acima com o "preLogin" retorna JSON estruturado assim:
```json
{
	"id": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
	"email": "jhonbonadio@gmail.com",
	"phone": "5561999481240",
	"devices": [
		{
			"id": "2e589417-148f-4266-abe7-42634c52c28b",
			"ownerId": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
			"deviceId": "9a662c2228ed37a9",
			"deviceName": "SM-A107M",
			"confirmed": true,
			"created_at": "2022-01-17T17:40:39.470Z",
			"updated_at": "2022-01-17T17:40:39.470Z"
		}
	]
}
```

Algumas rotas exigem que o usuário esteja conectado, como por exemplo, a rota de mostrar os cupons ou a rota de comprar um cupom. Para isso, é necessário que o usuário esteja conectado na aplicação.

Ao lado, está um exemplo de login e a resposta que deve ser mostrada se ocorrer tudo bem.

Será gerado um Bearer Token, mas você pode remover o "Bearer" da frente que irá funcionar normalmente.

### HTTP Request

`POST https://barataoapi.herokuapp.com/login`

### URL Parameters

Parameter | Description
--------- | -----------
email | E-mail de acesso do usuário
password | Senha de acesso do usuário
preLogin | Verificar se é um login temporário ou não

### Pre Login

Caso o parâmetro preLogin seja falso, será gerado o token de autenticação normalmente, caso contrário, irá gerar uma resposta sem gerar token, mostrando os dispositivos do usuário que está tentando autenticar na aplicação. 

Isso serve para verificar se o usuário que está entrando tem o dispositivo cadastrado, caso não tenha, será feita uma série de verificações no aplicativo e no final irá cadastrar o dispositivo do usuário e fazer o login sem o "preLogin".


<aside class=warning>
O token não pode ter data de expiração, pois a aplicativo usa o conceito de offline first.
</aside>


# Cadastro de usuário

## Verificação de CPF

Como o cadastro no aplicativo é feito por etapas, será necessário criar algumas rotas para fazer a busca no banco de dados e validar os dados.

### HTTP Request

`POST https://barataoapi.herokuapp.com/verifyCpfExists`

### URL Parameters

Parameter | Description
--------- | -----------
cpf | CPF informado pelo usuário no momento do cadastro

### CPF already exists

`
{
	"error": "User already exists"
}
`

### Invalid CPF

`
{
	"error": "Cpf invalid"
}
`

```bash
curl "https://barataoapi.herokuapp.com/verifyCpfExists"
-H "Content-Type: application/json"
```
> O comando acima retorna um staus: 200 OK

```json
No body returned for response
```


## Verificação de E-mail


### HTTP Request

`POST https://barataoapi.herokuapp.com/verifyEmailExists`

### URL Parameters

Parameter | Description
--------- | -----------
email | E-mail informado pelo usuário no momento do cadastro

### E-Mail already exists

`
{
	"error": "User already exists"
}
`

### E-mail incorrect

`
{
	"error": "Email incorrect"
}
`

```bash
curl "https://barataoapi.herokuapp.com/verifyEmailExists"
-H "Content-Type: application/json"
```
> O comando acima retorna um staus: 200 OK

```json
No body returned for response
```


## Verificação de telefone


### HTTP Request

`POST https://barataoapi.herokuapp.com/verifyPhoneExists`

### URL Parameters

Parameter | Description
--------- | -----------
phone | Telefone informado pelo usuário no momento do cadastro

### E-Mail already exists

`
{
	"error": "User already exists"
}
`

### E-mail incorrect

`
{
	"error": "Phone incorrect"
}
`

```bash
curl "https://barataoapi.herokuapp.com/verifyPhoneExists"
-H "Content-Type: application/json"
```
> O comando acima retorna um staus: 200 OK

```json
No body returned for response
```


## Cadastro do usuário

Se todas as informações estiverem OK, o usuário pode ser inserido no banco de dados.

### HTTP Request

`POST https://barataoapi.herokuapp.com/users`

### URL Parameters

Parameter | Description | Default
--------- | --------------- | -----------
name | Nome do usuário
email | E-mail do usuário 
cpf | CPF do usuário 
phone | Telefone do usuário 
password | Senha do usuário

### E-mail/CPF/Phone already exists

`
{
	"error": "User already exists"
}
`

### E-mail/Phone incorrect

`
{
	"error": "E-mail/Phone incorrect"
}
`

### CPF invalid

`
{
	"error": "CPF invalid"
}
`

```bash
curl "https://barataoapi.herokuapp.com/users"
-H "Content-Type: application/json"
```
> O comando acima retorna um JSON estruturado assim:

```json
{
	"id": "cd93d109-5a12-4d82-87d1-8facc4751f58",
	"name": "Jhonata Henrique Lima BOnadio",
	"email": "jhonbonadio@gmail.com",
	"cpf": "07169305100",
	"phone": "5561999481240",
	"created_at": "2022-01-17T15:11:00.544Z",
	"updated_at": "2022-01-17T15:11:00.544Z"
}
```

# Cupons

## Listar cupons do usuário

Para listar os cupons, o usuário deve estar conectado na aplicação e deve ser inserido o token de autorização no cabeçalho da requisição.

Esse token é obtido na rota /login

### HTTP Request

`GET https://barataoapi.herokuapp.com/vouchers`

### Headers

Header | Value
--------- | -----------
Authorization | Token

<aside class=success>
É preciso informar o token no cabeçalho, esse token não pode ser expirado.
</aside>

```bash
curl "https://barataoapi.herokuapp.com/vouchers"
-H "Content-Type: application/json"
-H "Authorization: Token"
```
> O comando acima retorna um JSON estruturado assim:

```json
[
	{
		"id": "34ef7f02-8d8c-4c1b-a38a-f2290c277070",
		"fuelId": "4edf806f-b29d-41d6-88e8-046a4a12661e",
		"fuelImage": "https://www.barataocombustiveis.com.br/assets/gasolina-aditivada-33783dc4d53a76965ba7e401a3743f458f69b5ce0d1b7f4539570a42e57d1d71.png",
		"ownerId": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
		"name": "Gasolina Aditivada",
		"liters": "15",
		"value": "94.65",
		"region": "DF",
		"created_at": "2022-01-17T18:19:44.155Z",
		"updated_at": "2022-01-17T18:19:44.155Z"
	}
]
```
## Adicionar cupom na conta do usuário

### HTTP Request

`POST https://barataoapi.herokuapp.com/vouchers`

### Headers

Header | Value
--------- | -----------
Authorization | Token

Paramter | Description
--------- | -----------
ownerId | ID do usuário que adquiriu o voucher
fuelId | ID do combustível
fuelImage | Imagem do combustível
name | Nome do combustível
liters | Litros
value | Valor pago
region | Região (DF, GO...)

<aside class=success>
É preciso informar o token no cabeçalho, esse token não pode ser expirado.
</aside>

```bash
curl "https://barataoapi.herokuapp.com/vouchers"
-H "Content-Type: application/json"
-H "Authorization: Token"
```
> O comando acima retorna um JSON estruturado assim:

```json
{
	"id": "bd68af3b-dc75-4ec9-82af-bc16ec8072a2",
	"fuelId": "4edf806f-b29d-41d6-88e8-046a4a12661e",
	"fuelImage": "https://www.barataocombustiveis.com.br/assets/gasolina-aditivada-33783dc4d53a76965ba7e401a3743f458f69b5ce0d1b7f4539570a42e57d1d71.png",
	"ownerId": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
	"name": "Gasolina Aditivada",
	"liters": "15",
	"value": "94.65",
	"region": "DF",
	"created_at": "2022-01-17T18:23:51.932Z",
	"updated_at": "2022-01-17T18:23:51.932Z"
}
```

# Devices

## Cadastrar dispositivo

Cadastrar o dispositivo na conta do usuário.

### HTTP Request

`POST https://barataoapi.herokuapp.com/devices`

Paramter | Description
--------- | -----------
ownerId | ID do usuário
deviceId | ID do dispositivo
deviceName | Nome do dispositivo
confirmed | Dispositivo confirmado


```bash
curl "https://barataoapi.herokuapp.com/devices"
-H "Content-Type: application/json"
```
> O comando acima retorna um JSON estruturado assim:

```json
{
	"id": "4c3ee4e1-d3cc-40d4-9239-d4c2d9b8df49",
	"ownerId": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
	"deviceId": "ID830FKD_1984HKD2_FFGH7763",
	"deviceName": "iPhone 12",
	"confirmed": true,
	"created_at": "2022-01-17T18:27:06.953Z",
	"updated_at": "2022-01-17T18:27:06.953Z"
}
```
## Listar Dispositivos

Listar os dispositivos cadastrados

### HTTP Request

`GET https://barataoapi.herokuapp.com/devices`


Header | Value
--------- | -----------
Authorization | Token

<aside class=success>
É preciso informar o token no cabeçalho, esse token não pode ser expirado.
</aside>

```bash
curl "https://barataoapi.herokuapp.com/devices"
-H "Content-Type: application/json"
-H "Authorization: Token"
```
> O comando acima retorna um JSON estruturado assim:

```json
[
	{
		"id": "2e589417-148f-4266-abe7-42634c52c28b",
		"ownerId": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
		"deviceId": "9a662c2228ed37a9",
		"deviceName": "SM-A107M",
		"confirmed": true,
		"created_at": "2022-01-17T17:40:39.470Z",
		"updated_at": "2022-01-17T17:40:39.470Z"
	}
]
```

# Combustíveis

## Listar combustíveis

Listar combustíveis no aplicativo

### HTTP Request

`GET https://barataoapi.herokuapp.com/fuels`

```bash
curl "https://barataoapi.herokuapp.com/fuels"
-H "Content-Type: application/json"
```
> O comando acima retorna um JSON estruturado assim:

```json
[
	{
		"id": "efba902c-23d9-4317-9e09-aa4cdac881f8",
		"popular": false,
		"name": "Etanol",
		"regularPrice": "6.49",
		"price": "5.39",
		"region": "DF",
		"imageUrl": "https://www.barataocombustiveis.com.br/assets/etanol-793cedf38c598cb250953800a35b36f5c3de232b843687e882aefacea8a49bd3.png",
		"created_at": "2022-01-03T11:49:39.000Z",
		"updated_at": "2022-01-03T11:49:39.000Z"
	},
	{
		"id": "2dc966b1-9b6c-46a0-b639-4244a623adf5",
		"popular": false,
		"name": "Diesel Comum",
		"regularPrice": "5.64",
		"price": "5.05",
		"region": "DF",
		"imageUrl": "https://www.barataocombustiveis.com.br/assets/diesel-comum-6a87165e6a5a74c4b8b10793a8e009cad1309d4b224d2e55c59455802b50db94.png",
		"created_at": "2022-01-03T11:50:55.000Z",
		"updated_at": "2022-01-03T11:50:55.000Z"
	}
]
```

# Usuário
## Atualizar dados do usuário

### HTTP Request

`PUT https://barataoapi.herokuapp.com/users`


### Headers

Header | Value
--------- | -----------
Authorization | Token

Paramter | Description
--------- | -----------
phone | Novo número
email | Novo e-mail

<aside class=success>
É preciso informar o token no cabeçalho, esse token não pode ser expirado.
</aside>

```bash
curl "https://barataoapi.herokuapp.com/users"
-H "Content-Type: application/json"
-H "Authorization: Token"
```
> O comando acima retorna um JSON estruturado assim:

```json
{
	"id": "cab1fb14-84d1-4e4e-b2e1-83fab1e74fc7",
	"name": "Jhonata Bonadio",
	"email": "jhonbonadio@gmail.com",
	"cpf": "07169305100",
	"phone": "5561985553370",
	"avatarUrl": null,
	"created_at": "2022-01-06T16:08:23.475Z",
	"updated_at": "2022-01-17T18:35:31.235Z"
}
```
# Postos credenciados

## Listar postos credenciados


### HTTP Request

`GET https://barataoapi.herokuapp.com/stations`


```bash
curl "https://barataoapi.herokuapp.com/stations"
-H "Content-Type: application/json"
```
> O comando acima retorna um JSON estruturado assim:

```json
[
	{
		"id": "48861f4d-7ac2-40e2-b6cb-43090cd099c4",
		"name": "Auto Posto Original 415",
		"address": "SHCS 415 Lote 03 PLL, Asa Sul",
		"state": "DF",
		"city": "Brasília",
		"cep": "72298-000",
		"country": "BR",
		"created_at": "2022-01-03T11:42:18.000Z",
		"updated_at": "2022-01-03T11:42:18.000Z"
	},
	{
		"id": "2854a793-e359-49fa-b5f3-5089193295fe",
		"name": "Auto Posto Original 409",
		"address": "SHCS 409 Bloco A PLL, Asa Sul",
		"state": "DF",
		"city": "Brasília",
		"cep": "70258-010",
		"country": "BR",
		"created_at": "2022-01-03T11:42:40.000Z",
		"updated_at": "2022-01-03T11:42:40.000Z"
	}
]
```

# Banco de dados

API Baratão: Essa API foi escrita em javascript e será necessário adapta-la para a linguagem escolhida. Será necessário crar algumas tabelas no banco de dados e fazer a relação com usuário.

Tabelas:

- devices: Tabela que contem os dispositivos cadastrados de cada usuário.
- fuels: Combustíveis cadastrados
- stations: Postos credênciados
- users: Usuários
- vouchers: Cupons comprados

## Devices

- id: uuid
- ownerId: UUID (ID do usuário)
- deviceId: varchar
- deviceName: varchar 
- confirmed: boolean
- created_at: timestamp
- updated_at: timestamp

## Fuels

- id: uuid
- name: varchar
- regularPrice: varchar
- price: varchar 
- region: varchar
- imageUrl: varchar (Imagem do combustível)
- popular: boolean
- created_at: timestamp
- updated_at: timestamp

## Stations

- id: uuid
- name: varchar
- address: varchar
- state: varchar 
- city: varchar
- cep: varchar
- country: boolean
- created_at: timestamp
- updated_at: timestamp

## Users

- id: uuid
- name: varchar
- email: varchar
- cpf: varchar 
- phone: varchar
- password: varchar
- avatarUrl: varchar (DEFAULT: NULL)
- created_at: timestamp
- updated_at: timestamp

## Vouchers

- id: uuid
- fuelId: uuid (ID do combustível)
- ownerId: uuid (ID do usuário)
- name: varchar 
- liters: varchar
- value: varchar
- region: varchar
- fuelImage: varchar (Imagem do combustível)
- created_at: timestamp
- updated_at: timestamp

