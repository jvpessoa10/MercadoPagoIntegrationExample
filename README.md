---
Integraçao do mercado pago com o ionic
---
Primeiro é necessario importar o SDK do mercadopago, que pode ser feito diretamente no index.html do projeto.

```html
  <script src="https://secure.mlstatic.com/sdk/javascript/v1/mercadopago.js"></script>
```

Após a importação, será necessário configurar a chave publica para poder consumir os serviços da API. A chave publica esta disponivel para todos os vendedores no dashboard do mercadopago

```html
  <script>
    Mercadopago.setPublishableKey("TEST-a24b2fe0-df67-4088-a484-73eac5a55e58");
  ...
```


Para a coleta dos dados sensíveis do usuário utiliza-se a diretiva `data-checkout` nas tags de input:

```html
  <ion-input type="text" id="cardNumber" data-checkout="cardNumber" placeholder="0000 0000 0000 0000"  value="4509 9535 6623 3704"></ion-input>
```

**Exemplo de formulário de recolhimento de dados de cartão:**
```html
       <form id="pay">
          <ion-list>
          <ion-item>
            <ion-label color="primary">Email:</ion-label>
            <ion-input id="email" name="email"  type="email" value="jvpessoa10@hotmail.com" placeholder="your email"></ion-input>
          </ion-item>
          <ion-item>
              <ion-label color="primary">Nome:</ion-label>
              <ion-input type="text" id="cardholderName" value="João Victor Pessoa" data-checkout="cardholderName" ></ion-input>
          </ion-item>
          <ion-item>
              <ion-label color="primary">Numero do cartão de credito:</ion-label>
              <ion-input type="text" id="cardNumber" data-checkout="cardNumber" placeholder="0000 0000 0000 0000"  value="4509 9535 6623 3704"></ion-input>
            </ion-item>
            <ion-item>
                <ion-label color="primary">Codigo secreto:</ion-label>
                <ion-input type="text" id="securityCode" data-checkout="securityCode" value="142" placeholder="123" ></ion-input>
              </ion-item>
              <ion-item>
                  <ion-label color="primary">Mes de expiração:</ion-label>
                  <ion-input type="text" id="cardExpirationMonth" data-checkout="cardExpirationMonth" value="07" placeholder="12" ></ion-input>
                </ion-item>
                <ion-item>
                    <ion-label color="primary">Ano de expiração:</ion-label>
                    <ion-input type="text" id="cardExpirationYear" data-checkout="cardExpirationYear" value="2020" placeholder="2015" ></ion-input>
                </ion-item>


                <ion-item>
                    <ion-label color="primary">Document Type:</ion-label>
                    <ion-input  id="docType" data-checkout="docType" value="CPF" ></ion-input>
                </ion-item>
                <ion-item>
                    <ion-label color="primary">Document number</ion-label>
                    <ion-input type="text" id="docNumber" data-checkout="docNumber" value="11749620464" placeholder="12345678"></ion-input>
                </ion-item>
          <ion-item>
            <button onclick="procede(this.form)" ion-button>Continuar</button> 
          </ion-item>
        </ion-list>

        </form>
        
 ```       
        
        
        
        
        
Cada dado expecifico recebe um nome diferente em sua diretiva `data-checkout`.

**Ex:**

       Dado: Ano de expiração.
       Diretiva: `data-checkout="cardExpirationYear"`

#### Observação:
  A tag que possui a diretiva `data-checkout="docType"`(Document Type), se refere a algum documento exigido em alguns paises para a compra 
  com cartão de credito. No brasil, é exigido o CPF.
  
  É necessário fazer uma consulta a um servico do mercadopago que retornara um array de Document types:
  
```javascript
  Mercadopago.getIdentificationTypes(function ress(res,message){
        console.log(message)
      });
```

## Gerar token com os dados obtidos do form

Com o formulario preenchido,deve-se passar todo o form como um parametro de uma função, junto com um callback que recebera o
token produzido pela função. E esse token que deve ser enviado para o servidor.

```javascript
function procede(){
         var form = document.getElementById('pay')
         Mercadopago.createToken(form,function sdkResponseHandler(status, res){
          console.log()
          console.log(status);
          console.log(res)

          if (status != 200 && status != 201) {
           
            alert("Verificar os dados");
          }else{
            

            var card = document.createElement('input');
            card.setAttribute('name',"token");
            card.setAttribute('type',"hidden");
            card.setAttribute('value',res.id);
            form.appendChild(card);
            doSubmit=true;
            form.submit();
          }
         }); 
      }

```
#### Observação
  É necessário fazer verificações de digitação nos inputs do usuário(Sequencias Ex:"1,2,3" , lenght, tipo do input: int float etc.)) para evitar ter que implementar handlers 
  de erros no callback

