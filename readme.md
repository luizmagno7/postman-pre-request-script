

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

    ![image](https://user-images.githubusercontent.com/7591530/176596930-7d89ebe3-a667-48e2-91d7-2fd6fb64e5b9.png)
    
3. Preencha os campos e clique em “Create workspace”
    
    ![image](https://user-images.githubusercontent.com/7591530/176596992-a35886f4-485a-4c50-9172-547e3d12ae80.png)
    

### Configurando variáveis de ambiente

Vamos configurar as variáveis que serão utilizadas para facilitar nosso trabalho na utilização das requisições da API.

1. Acesse o menu Environments (Ambientes)
2. Clique em “Create new Environment”
    
    ![image](https://user-images.githubusercontent.com/7591530/176597024-8f0a5efd-042d-47c4-8860-261c5c843790.png)
    
3. Dê um nome para seu ambiente
4. Crie as variáveis
    
    ![image](https://user-images.githubusercontent.com/7591530/176597051-d4d4c493-0a69-4bc5-b511-7c9bc986c76b.png)
    
5. Clique em “Save” (Salvar)

### Criando servidor de simulação (Mock Server)

Vamos criar um servidor de exemplo para mostrar a utilização do pre-request script.

Ao criar o servidor de simulação, uma coleção será criada juntamente com suas requisições.

1. Vá no menu “Mock Servers”
2. Clique em “Create Mock Server”
    
    ![image](https://user-images.githubusercontent.com/7591530/176597077-a9ac6770-6dd2-434b-bee9-f244a57220e0.png)
    
3. Crie seus endpoints ou selecione uma collection
    
    ![image](https://user-images.githubusercontent.com/7591530/176597099-ef22db3d-c35d-4ea7-b152-948a0aa7a2b6.png)
    
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
    
    ![image](https://user-images.githubusercontent.com/7591530/176597148-5fd07d99-7435-4e33-803b-153979818f82.png)
    
5. Copie o link do servidor e substitua a variável “baseUrl”
    
    ![image](https://user-images.githubusercontent.com/7591530/176597174-cd9bb99b-90b9-44c9-96af-03450a834ea4.png)
    
6. Vá no menu “Environments”
7. Selecione o ambiente que criamos anteriormente
8. Substitua o valor atual da variável “baseUrl”
    
    ![image](https://user-images.githubusercontent.com/7591530/176597206-aea608eb-71ac-4684-b64c-63699de68373.png)
    
9. Teste suas requisições
    
    ![image](https://user-images.githubusercontent.com/7591530/176597226-78bc4083-0368-46a7-bec6-c96ca2505778.png)
    
    ### Pre-request script
    
    Criando o script que faz a mágica acontecer
    
    1. Selecione sua coleção
    2. Clique em “pre-request Script”
        
        ![image](https://user-images.githubusercontent.com/7591530/176597242-632ba99f-bcdf-4ccd-83a7-da2f21958232.png)
        
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
        
        ![image](https://user-images.githubusercontent.com/7591530/176597276-282e4305-2cec-42c6-86c1-ae7cd695610f.png)
        
    5. Faça o teste com a requisição de produtos e veja se a requisição de login será chamada antes da execução.
        
        ![image](https://user-images.githubusercontent.com/7591530/176597297-3904a332-a78a-4b6b-96ea-5e5d77bebbf4.png)
        
    
    ### Sucesso!!

    
