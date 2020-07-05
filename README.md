# Teste de API
Testando API com Postman e documentação swagger.  

Exercício 1 - Teste de API
Utilize a Swagger Petstore (https://petstore.swagger.io/):
1) Crie os usuarios Ana Maia, Rodrigo Mendes, Tatiana Vasconcelos
2) Crie os pets Snoopy (dog), Bichento (cat) e Perry (platypus)
3) Mude o status da ordem de venda do Perry e do Snoopy para "approved" e do Bichento para "delivered"
4) Consulte as 3 ordens geradas

OBS: Durante os testes identifiquei que a persistência de dados na criação de um novo Pet, falha após alguns segundos. Não persiste os dados por muito tempo. Portanto salvei algumas destas consultas para exemplificar a resolução do exercício. 

--------------------------------------------------------------------------------------------------------------------


# Solucionando exercício:
Para facilitar a edição dos métodos GET, POST, UPDATE e DELETE defini a variável baseUrl com o end-point "https://petstore.swagger.io/v2" com valor inicial {{baseUrl}}. A partir disso, os métodos criados não são necessários de serem atualizados sempre que um novo método for criado, somente a variável {{baseUrl}}/diretório. <br>

<br> 1) Crie os usuarios Ana Maia, Rodrigo Mendes, Tatiana Vasconcelos: </br>
<br> Resp: </br>
<p> - Criei com o método POST, com endereço: {{baseUrl}}/user/createWithList </p>
<p> - Preenchi no Body o array com todos os dados de cadastro para cada usuário, exemplo: </p>

<p> {                            
        "username": "amaia", 
         "firstName": "Ana",  
         "lastName": "Maia",  
         "email": "amaia@gmail.com", 
         "password": "teste@123", 
         "phone": "11988888888",
         "userStatus": "0"
 },</p>

- Para listar usuário cadastrado, usei o método GET no username de cada usuário, dessa forma: {{baseUrl}}/user/amaia, resultando na resposta: 

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

2) Crie os pets Snoopy (dog), Bichento (cat) e Perry (platypus)
Resp:

- Para criar os pets, usei o método POST em {{baseUrl}}/pet

- Para criar a categoria (dog,cat e platypus) e os nomes de cada animal (Snoopy, Bichento e Perry), adicionei duas variáveis em Pre-request Script. Isto facilita para que eu não preencha manualmente o nome dos animais e suas categorias um a um. Com isso, resultou em:

let name = ["Snoopy", "Bichento","Perry"];
let categoria = ["Dog","Cat","Platypus"];

pm.variables.set("name",name[0]);
pm.variables.set("categoria", categoria[0]);

- No Body chamei as duas variáveis criadas, que resultou em: 
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

- Em Tests criei a variável petId que captura o ID do pet que será usado para atualizar o status Approved e Delivered no próximo passo:
Exemplo: 
let petId = pm.response.json().id;
pm.environment.set("petId",petId);

- Para criar cada pet, alterei o valor do array de 0 a 2 [0].

3) Mude o status da ordem de venda do Perry e do Snoopy para "approved" e do Bichento para "delivered"
Resp:

- Criei o método POST em {{baseUrl}}/store/order
- Com ID gerado ao adicionar um novo pet a store, uso este ID para consulta através do método GET em {{baseUrl}}/store/order/:orderId o status Delivered e Approved são enviados:

Estes Ids foram gerados no momento em que consultei os pets através dos Ids gerados inicialmente, na criação do pet. Salvei estes para consultar futuramente as ordens de vendas. 

Perry => 154350062005095
Bichento => 154350062005114
Snoopy => 154350062005116

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


4) Consulte as 3 ordens geradas
<p> Resp: </p> 

- Para consulturar o status Delivered / Approved utilizei ids dos pets da seguinte forma: 

<p> Através do petId 154350062005095, consultei: </p>

Perry
{
    "id": 6512767,
    "petId": 154350062005095,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}

Através do petId 154350062005114, consultei: 
Bichento 
{
    "id": 6512777,
    "petId": 154350062005114,
    "quantity": 1,
    "shipDate": "2020-07-03T20:04:55.005+0000",
    "status": "Delivered",
    "complete": true
}

Através do petId 154350062005116, consultei:
Snoopy
{
    "id": 6512769,
    "petId": 154350062005116,
    "quantity": 1,
    "shipDate": "2020-07-04T20:04:50.005+0000",
    "status": "approved",
    "complete": true
}
