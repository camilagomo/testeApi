# Teste de API
Testando API com Postman e documentação padrão swagger.  

Exercício 1 - Teste de API
Utilize a Swagger Petstore (https://petstore.swagger.io/):
1) Crie os usuarios Ana Maia, Rodrigo Mendes, Tatiana Vasconcelos
2) Crie os pets Snoopy (dog), Bichento (cat) e Perry (platypus)
3) Mude o status da ordem de venda do Perry e do Snoopy para "approved" e do Bichento para "delivered"
4) Consulte as 3 ordens geradas

OBS: Durante os testes identifiquei que a persistência de dados na criação de um novo Pet, falha após alguns segundos. Não persiste os dados por muito tempo. Portanto salvei algumas destas consultas para exemplificar a resolução do exercício. 

--------------------------------------------------------------------------------------------------------------------

# Solucionando exercício:
<br> Para facilitar a edição dos métodos GET, POST, UPDATE e DELETE defini a variável baseUrl com o end-point "https://petstore.swagger.io/v2" com valor inicial {{baseUrl}}. A partir disso, os novos métodos criados não são necessários de serem atualizados com a url completa, apenas passamos a variável {{baseUrl}}/diretório. </br>

<br> 1) Crie os usuarios Ana Maia, Rodrigo Mendes, Tatiana Vasconcelos: </br>
Resp: 
<p> - Criei com o método POST {{baseUrl}}/user/createWithList </p>
<p> - Criei um array no Body com os dados para novo usuário, exemplo: </p>

```
{                            
         "username": "amaia", 
         "firstName": "Ana",  
         "lastName": "Maia",  
         "email": "amaia@gmail.com", 
         "password": "teste@123", 
         "phone": "11988888888",
         "userStatus": "0"
 }, 
```

- Para listar usuário cadastrado, usei o método GET com username de cada usuário já criado, dessa forma: {{baseUrl}}/user/amaia, resultando na resposta: 

```
{
    "id": 12,
    "username": "amaia",
    "firstName": "Ana",
    "lastName": "Maia",
    "email": "amaia@gmail.com",
    "password": "teste@123",
    "phone": "11988888888",
    "userStatus": 0
}
```

2) Crie os pets Snoopy (dog), Bichento (cat) e Perry (platypus)
Resp:

<p> - Para criar os pets, usei o método POST {{baseUrl}}/pet </p>

<p> - Para criar a categoria (dog,cat e platypus) e os nomes de cada animal (Snoopy, Bichento e Perry), adicionei duas variáveis em Pre-request Script. Isto facilita para que eu não preencha manualmente o nome dos animais e suas categorias. Com isso, resultou em: </p> 

```
let name = ["Snoopy", "Bichento","Perry"];
let categoria = ["Dog","Cat","Platypus"];

pm.variables.set("name",name[0]);
pm.variables.set("categoria", categoria[0]);
```

<p>  No Body chamei as duas variáveis name e categoria, que resultou em: </p> 

```
{
    "name": "{{name}}",
    "photoUrls": [
        "https://www.cobasi.com.br/"
    ],
    "id": "0",
    "category": {
        "id": "0",
        "name": "{{categoria}}"
    },
    "tags": [
        {
            "id": "0",
            "name": "{{categoria}}"
        }
    ],
    "status": "castrado"
}
```

</p> O Response Body resulta em: </p> 

```
{
    "id": 112808,
    "category": {
        "id": 0,
        "name": "Dog"
    },
    "name": "Snoopy",
    "photoUrls": [
        "https://www.cobasi.com.br/"
    ],
    "tags": [
        {
            "id": 0,
            "name": "Dog"
        }
    ],
    "status": "castrado"
}

```

<p> - Em "Tests" criei a variável petId que captura o ID do pet que será usado para atualizar o status Approved e Delivered quando consultados na store, dessa forma </p> 
       
```       
let petId = pm.response.json().id;
pm.environment.set("petId",petId);
```

<p> -  Para criar cada pet, e gerar um novo id identificador do novo pet, alterei o valor do array de 0 a 2 [0]. </p>

<p> 3) Mude o status da ordem de venda do Perry e do Snoopy para "approved" e do Bichento para "delivered" </p> 
Resp:

<p> - Criei o método POST {{baseUrl}}/store/order </p>
<p> - O método GET {{baseUrl}}/store/order/:orderId retorna se o status Delivered e Approved são enviados corretamente, usando id de cada pet, gerado anteriormente ao criar o pet: </p>

<p> Salvei estes ids na minha primeira consulta aos pets criados:  </p>

<p> Perry => 154350062005095 </p>
<p> Bichento => 154350062005114 </p>
<p> Snoopy => 154350062005116 </p>

<p> - O Response body resulta em: </p> 

```
Perry:
{
    "id": 6512767,
    "petId": 154350062005095,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}

Bichento:
{
    "id": 6512768,
    "petId": 154350062005114,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}

Snoopy:
{
    "id": 6512769,
    "petId": 154350062005116,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}
```

<p> 4) Consulte as 3 ordens geradas </p> 
Resp: 

<p> - Para consulturar o status Delivered / Approved utilizei o método POST {{baseUrl}}/store/orderId </p> 

- Esta é uma ordem que manipulei com status Approved, portanto: 

```
{
    "petId": "154350062005095",
    "quantity": "1",
    "shipDate": "2020-07-04T23:13:55.005Z",
    "status": "Approved",
    "complete": "true"
}
```
E este é o response body: 

```
{
    "id": 8919,
    "petId": 154350062005095,
    "quantity": 1,
    "shipDate": "2020-07-04T23:13:55.005+0000",
    "status": "Approved",
    "complete": true
}
```
<p> - Com Id 8919 gerado, realizado a consulta através do método GET {{baseUrl}}/store/order/:orderId e verifico a minha ordem gerada que por ser uma consulta deve resultar no mesmo valor acima do response body: 
         
```         
{
    "id": 8919,
    "petId": 154350062005095,
    "quantity": 1,
    "shipDate": "2020-07-04T23:13:55.005+0000",
    "status": "Approved",
    "complete": true
}

```

<p> - Para gerar cada consulta de cada pet, devo manipular os dados através do meu petId. </p> 

<p> - Para consultar petId 154350062005095, que corresponde ao Perry, clico em send e retorna: </p>

```
{
    "id": 6512767,
    "petId": 154350062005095,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}
```

<p> - Para consultar o petId 154350062005114, que corresponde ao Bichento, clico em send e retorna: </p>

```
{
    "id": 6512777,
    "petId": 154350062005114,
    "quantity": 1,
    "shipDate": "2020-07-03T20:04:55.005+0000",
    "status": "Delivered",
    "complete": true
}
```
<p> - Para consultar o petId 154350062005116, que corresponde ao Snoopy, clico em send e retorna: </p>

```
{
    "id": 6512769,
    "petId": 154350062005116,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}
```
