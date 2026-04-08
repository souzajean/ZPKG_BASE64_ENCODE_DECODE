# Integração com SAP BTP CPI Base

## 📘 Descrição

Implementação de um iFlow no SAP BTP Integration Suite responsável por realizar operações de encode e decode em Base64.

O fluxo contempla:
- Recebimento de requisição via HTTPS (REST)
- Persistência de dados em Exchange Properties
- Processamento com Groovy Script
- Construção de resposta customizada

O retorno inclui:
- Payload original em Base64
- Conteúdo decodificado (JSON)
- Payload reprocessado (encoded novamente)

![Fluxo](imagens/capa-linkedin.png)

---

<br>

# 🏗️ Arquitetura do iFlow

<br>

## 🌐 🔹 Entrada no Postman

json
```
{
  "id": 1,
  "title": "TESTE",
  "body": "TESTANDO"
}
```

<br>


## 🔄 1. Fluxo da Integração

<br>

### 🧱 Criando o Package
![Fluxo](imagens/Screenshot_1.png)

<br><br>

### 🏷️ Nome do Package
```
ZPKG_BASE64_ENCODE_DECODE
```
![Fluxo](imagens/Screenshot_2.png)

<br>

## 🧩 2. Criação do Integration Flow

<br>

### ➕ Adicionando o Artefato
![Fluxo](imagens/Screenshot_3.png)

<br>

### 🏷️ Nome do iFlow
![Fluxo](imagens/Screenshot_4.png)
```
IF_CPI_Base64_EncodeDecode_Service
```
<br>

## ⚙️ 3. Configuração do Adapter HTTPS

### ➕ Adicionando o Adapter

![Fluxo](imagens/Screenshot_5.png)

<br>

### 🌐 Trigger (HTTPS)

```
Adapter: HTTPS
Method: GET ou POST
Endpoint:
/download-anexo
```
![Fluxo](imagens/Screenshot_6.png)

<br>

## ⚙️ 4.Content Modifier

### ➕ Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_7.png)

<br>

### 🏷️ Renomeando o Content Modifier
![Fluxo](imagens/Screenshot_8.png)
```
Name: CM_Save_Base64_Original
```

<br>

### ⚙️ Configuração do Content Modifier
```
Name: CM_Save_Base64_Original
Exchange Property
Name: base64_original
Source Type: Expression
Source Value: ${body}
Data Type: java.lang.String
```
![Fluxo](imagens/Screenshot_9.png)

<br>

## ⚙️ 5.Content Modifier

### Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_10.png)

<br>

### 🏷️ Renomeando o Content Modifier
![Fluxo](imagens/Screenshot_11.png)
```
Name: CM_Save_Decoded_JSON
```

<br>

### ⚙️ Configuração o Content Modifier
```
Name: CM_Save_Decoded_JSON
Exchange Property
Name: decoded_body
Source Type: Expression
Source Value: ${body}
Data Type: java.lang.String
```
![Fluxo](imagens/Screenshot_12.png)

<br>

## ⚙️ 6. Groovy Script

### ➕ Adicionando o Groovy Script
![Fluxo](imagens/Screenshot_13.png)

<br>

### 🏷️ Nome do Groovy Script
![Fluxo](imagens/Screenshot_14.png)

```
Name: GS_Encode_Base64
```

<br>

### ➕ Adicionando o Groovy Script
![Fluxo](imagens/Screenshot_15.png)

<br>

### 💻 Código do Groovy Script
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
![Fluxo](imagens/Screenshot_16.png)

<br>

## ⚙️ 7. Content Modifier

### ➕ Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_17.png)

<br>

### ⚙️ Configuração do Content Modifier
```
Name: CM_Save_Encoded_Base64
Exchange Property
Name: encoded_body
Source Type: Expression
Source Value: ${body}
Data Type: java.lang.String
```
![Fluxo](imagens/Screenshot_18.png)

<br>

## ⚙️ 8. Content Modifier

### ➕ Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_19.png)

<br>

### 🏷️ Nome do Content Modifier
```
Name: CM_Build_Final_Response
```
![Fluxo](imagens/Screenshot_20.png)

<br>

### ⚙️ Configuração do Content Modifier
```
Type: Expression
Body: 
{
  "base64_original": "${property.base64_original}",
  "decoded": ${property.decoded_body},
  "encoded_again": "${property.encoded_body}"
}
```
![Fluxo](imagens/Screenshot_21.png)

<br>

## 9. 🎯 Resultado

### ✅ Resposta Decode e Encode
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
![Fluxo](imagens/Screenshot_22.png)


<br>
<br>

---

## 📦 Exemplo prático – iFlow para baixar

📦 [Download do iFlow – PKG_BASE64_ENCODE_DECODE](https://github.com/souzajean/ZPKG_BASE64_ENCODE_DECODE/raw/main/Package/IFL_CPI_Base64_EncodeDecode_Service.zip)
