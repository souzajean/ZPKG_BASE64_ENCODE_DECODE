# ZPKG_BASE64_ENCODE_DECODE
SAP BTP CPI - BASE64_ENCODE_DECODE


![Fluxo](imagens/capa-linkedin.png)

---

<br>

# :building_construction: Arquitetura do iFlow

## 🔄 1. Fluxo da Integração

<br>

### Criando o Package
![Fluxo](imagens/Screenshot_1.png)

<br>

### Nome do Package
![Fluxo](imagens/Screenshot_2.png)
```
ZPKG_BASE64_ENCODE_DECODE
```
<br>

## 🧩 2. Criação do Integration Flow

<br>

### Adicionando o Artefato
![Fluxo](imagens/Screenshot_3.png)

<br>

### Nome do iFlow
![Fluxo](imagens/Screenshot_4.png)
```
IF_CPI_Base64_EncodeDecode_Service
```
<br>

## ⚙️ 3. Configuração do Adapter HTTPS

### Adicionando o Adapter

![Fluxo](imagens/Screenshot_5.png)

<br>

## 🌐 3. Configuração do Adapter HTTPS

### Adicionando o Adapter
![Fluxo](imagens/Screenshot_6.png)

<br>

### Endpoint configurado
![Fluxo](imagens/Screenshot_7.png)
```
/carros
```
<br>

































### Entrada no Postman

## 🌐 🔹 1.POSTMAN
```
{
  "id": 1,
  "title": "TESTE",
  "body": "TESTANDO"
}
```

## 🧩 🔧 iFlow COMPLETO (Baseado nessa JSON)




### ⚙️ 1. Trigger (HTTPS)

```
Adapter: HTTPS
Method: GET ou POST
Endpoint:
/download-anexo
```



⚙️ 2.Content Modifier
```
Name: CM_Save_Base64_Original
Exchange Property
Name: base64_original
Source Type: Expression
Source Value: ${body}
Data Type: java.lang.String
```

⚙️ 3. Base64 Decoder
```
Name: CV_Base64_Decode
```

⚙️ 4. Content Modifier
```
Name: CM_Save_Decoded_JSON
Exchange Property
Name: decoded_body
Source Type: Expression
Source Value: ${body}
Data Type: java.lang.String
```


⚙️ 3. Groovy Script
```
Name: GS_Encode_Base64
```

```
import com.sap.gateway.ip.core.customdev.util.Message
import java.util.Base64

def Message processData(Message message) {
    
    def body = message.getBody(String)
    
    def encoded = Base64.getEncoder().encodeToString(body.getBytes("UTF-8"))
    
    message.setBody(encoded)
    
    return message
}
```



⚙️ 5. Content Modifier
```
Name: CM_Save_Encoded_Base64
Exchange Property
Name: encoded_body
Source Type: Expression
Source Value: ${body}
Data Type: java.lang.String
```

⚙️ 6. Content Modifier
```
Name: CM_Build_Final_Response
```

```
Type: Expression
Body: 
{
  "base64_original": "${property.base64_original}",
  "decoded": ${property.decoded_body},
  "encoded_again": "${property.encoded_body}"
}
```

🎯 Resultado

```
{
  "base64_original": "eyJpZCI6MSwidGl0bGUiOiJURVNURSIsImJvZHkiOiJURVNUQU5ETyJ9",
  "decoded": {
    "id": 1,
    "title": "TESTE",
    "body": "TESTANDO"
  },
  "encoded_again": "eyJpZCI6MSwidGl0bGUiOiJURVNURSIsImJvZHkiOiJURVNUQU5ETyJ9"
}
```



