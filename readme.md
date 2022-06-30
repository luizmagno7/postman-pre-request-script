# Postman - Pre-request script

## Utilizando pre-request script

Você pode utilizar o recurso pre-request script para rodar código Javascript antes da sua requisição ser chamada. O pre-request script pode ser adicionado na requisição, pasta, ou coleção. Sua utilização pode variar entre atualizar valores de variáveis, debuggar código utilizando o console como saída, e até mesmo realizar a autenticação da sua requisição (o foco aqui será esse).

Utilizaremos o pre-request script para fazer login na aplicação e obter o access_token para se conectar com nossa API.

[Writing pre-request scripts | Postman Learning Center](https://learning.postman.com/docs/writing-scripts/pre-request-scripts/)

## Configurando Postman

Faça a instalação do Postman para iniciar as configurações.

[Download Postman | Get Started for Free](https://www.postman.com/downloads/)

### Criando workspace

1. Menu workspaces
2. Create workspace (Criar local de trabalho)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1ff6f8f7-5a57-4da6-bd9e-435af2a4a089/Untitled.png)
    
3. Preencha os campos e clique em “Create workspace”
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3b7b7663-903e-441b-9d82-94ab032ee25c/Untitled.png)
    

### Configurando variáveis de ambiente

Vamos configurar as variáveis que serão utilizadas para facilitar nosso trabalho na utilização das requisições da API.

1. Acesse o menu Environments (Ambientes)
2. Clique em “Create new Environment”
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/672cde90-bfe4-46a5-8e16-98088169c124/Untitled.png)
    
3. Dê um nome para seu ambiente
4. Crie as variáveis
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2d1ce76d-b399-4fac-ad5d-cff0f38fc413/Untitled.png)
    
5. Clique em “Save” (Salvar)

### Criando servidor de simulação (Mock Server)

Vamos criar um servidor de exemplo para mostrar a utilização do pre-request script.

Ao criar o servidor de simulação, uma coleção será criada juntamente com suas requisições.

1. Vá no menu “Mock Servers”
2. Clique em “Create Mock Server”
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c42dd0c6-fcc5-42d8-aee9-6516b1b8567e/Untitled.png)
    
3. Crie seus endpoints ou selecione uma collection
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/146ca96c-abf7-45a1-ab50-bfb5f912f4a5/Untitled.png)
    
    - Login
        
        **Response body**
        
        ```json
        {
                "access_token": "MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",
                "token_type": "Bearer",
                "expires_in": 3600,
                "refresh_token": "IwOGYzYTlmM2YxOTQ5MGE3YmNmMDFkNTVk",
                "scope": "create"
            }
        ```
        
    - Products
        
        **Response body**
        
        ```json
        [{
              "title": "Brown eggs",
              "type": "dairy",
              "description": "Raw organic brown eggs in a basket",
              "filename": "0.jpg",
              "height": 600,
              "width": 400,
              "price": 28.1,
              "rating": 4
          }, {
              "title": "Sweet fresh stawberry",
              "type": "fruit",
              "description": "Sweet fresh stawberry on the wooden table",
              "filename": "1.jpg",
              "height": 450,
              "width": 299,
              "price": 29.45,
              "rating": 4
          }, {
              "title": "Asparagus",
              "type": "vegetable",
              "description": "Asparagus with ham on the wooden table",
              "filename": "2.jpg",
              "height": 450,
              "width": 299,
              "price": 18.95,
              "rating": 3
          }]
        ```
        
4. Dê um nome ao servidor e não esqueça de selecionar nosso ambiente
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/56a09e78-a1b1-4c9e-af47-6cb9e4a1c726/Untitled.png)
    
5. Copie o link do servidor e substitua a variável “baseUrl”
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/62b60aae-4884-4fe9-81d6-95efd3226fe7/Untitled.png)
    
6. Vá no menu “Environments”
7. Selecione o ambiente que criamos anteriormente
8. Substitua o valor atual da variável “baseUrl”
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/10b7ece4-d35a-4949-a752-aa702a52c8ab/Untitled.png)
    
9. Teste suas requisições
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6bbc3c3b-1bc3-41af-bab8-f89a02e84f4a/Untitled.png)
    
    ### Pre-request script
    
    Criando o script que faz a mágica acontecer
    
    1. Selecione sua coleção
    2. Clique em “pre-request Script”
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d09e4455-b2e0-403d-83dc-9745463511e4/Untitled.png)
        
    3. Adicione o seguinte código
        
        ```jsx
        // Pega a url de base do Mock Server
        const baseUrl = pm.environment.get('baseUrl');
        
        //Opções para a requisição de login
        const options = {
            url: baseUrl + "/login",
            method: "POST",
            header: {
                'Accept': '*/*',
                'Content-Type': 'application/x-www-form-urlencoded',
            },
            body: {
                mode: 'urlencoded',
                urlencoded: [
                    { key: 'secret_key', value: pm.environment.get('secret_key') },
                    { key: 'client_secret', value: pm.environment.get('client_secret') },
                ]
            }
        }
        
        //Chamada para a requisição de autenticação
        pm.sendRequest(options, (err, res) => {
            //converte o retorno em JSON
            const data = res.json();
            console.log(data); 
        
            // Resultado do console
            // access_token: "MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3"
            // token_type: "Bearer"
            // expires_in: 3600
            // refresh_token: "IwOGYzYTlmM2YxOTQ5MGE3YmNmMDFkNTVk"
            // scope: "create"
        
            //atualiza o valor da variável "accessToken" com o token que retornou na requisição
            pm.environment.set('accessToken', data.access_token);
        })
        ```
        
    4. Configure a autenticação de todas as requisições
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0349af51-f5d0-4863-8beb-7c186d41a335/Untitled.png)
        
    5. Faça o teste com a requisição de produtos e veja se a requisição de login será chamada antes da execução.
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/73c593cf-2680-4d9e-9475-ae1e83655162/Untitled.png)
        
    
    ### Sucesso!!