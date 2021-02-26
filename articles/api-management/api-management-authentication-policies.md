---
title: Azure API Management-Authentifizierungsrichtlinien | Microsoft Docs
description: Erfahren Sie mehr über die Authentifizierungsrichtlinien, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492602"
---
# <a name="api-management-authentication-policies"></a>API Management-Authentifizierungsrichtlinien
Dieses Thema enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Authentifizierungsrichtlinien

-   [Standardauthentifizierung](api-management-authentication-policies.md#Basic) – Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.

-   [Authentifizierung mit Clientzertifikat](api-management-authentication-policies.md#ClientCertificate) – Authentifizierung mit einem Back-End-Dienst unter Verwendung von Clientzertifikaten.

-   [Authentifizierung mit verwalteter Identität](api-management-authentication-policies.md#ManagedIdentity) – Authentifizierung für den API Management-Dienst mit der [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md).

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Standardauthentifizierung
 Verwenden Sie die Richtlinie `authentication-basic` für die Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung. Diese Richtlinie legt letztlich den HTTP-Autorisierungsheader auf den Wert fest, der den Anmeldeinformationen in der Richtlinie entspricht.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Beispiel

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elemente

|Name|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|authentication-basic|Stammelement|Ja|

### <a name="attributes"></a>Attributes

|Name|BESCHREIBUNG|Erforderlich|Standard|
|----------|-----------------|--------------|-------------|
|username|Gibt den Benutzernamen für die Standardanmeldeinformationen an.|Ja|–|
|password|Gibt das Kennwort für die Standardanmeldeinformationen an.|Ja|–|

### <a name="usage"></a>Verwendung
 Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound

-   **Richtlinienbereiche:** alle Bereiche

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Authentifizieren mit Clientzertifikat
 Verwenden Sie die Richtlinie `authentication-certificate` für die Authentifizierung mit einem Back-End-Dienst unter Verwendung eines Clientzertifikats. Das Zertifikat muss zuerst [in API Management installiert](./api-management-howto-mutual-certificates.md) werden. Es wird durch seinen Fingerabdruck oder seine Zertifikat-ID (Ressourcenname) identifiziert. 

> [!CAUTION]
> Wenn das Zertifikat auf ein in Azure Key Vault gespeichertes Zertifikat verweist, identifizieren Sie es anhand der Zertifikat-ID. Wenn ein Key Vault-Zertifikat rotiert wird, ändert sich dessen Fingerabdruck in API Management, und die Richtlinie löst das neue Zertifikat nicht auf, wenn es anhand des Fingerabdrucks identifiziert wird.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Beispiele

In diesem Beispiel wird das Clientzertifikat durch die Zertifikat-ID identifiziert:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

In diesem Beispiel wird das Clientzertifikat durch seinen Fingerabdruck identifiziert:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
In diesem Beispiel wird das Clientzertifikat in der Richtlinie festgelegt und nicht aus dem integrierten Zertifikatspeicher abgerufen:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elemente  
  
|Name|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-certificate|Stammelement|Ja|  
  
### <a name="attributes"></a>Attributes  
  
|Name|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Der Fingerabdruck für das Clientzertifikat.|Entweder `thumbprint` oder `certificate-id` muss vorhanden sein.|–|
|certificate-id|Der Zertifikatressourcenname.|Entweder `thumbprint` oder `certificate-id` muss vorhanden sein.|–|
|body|Clientzertifikat als Bytearray|Nein|–|
|password|Das Kennwort für das Clientzertifikat|Dieses Attribut wird verwendet, wenn das in `body` angegebene Zertifikat kennwortgeschützt ist.|–|
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte**: inbound  
  
-   **Richtlinienbereiche:** alle Bereiche  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Authentifizierung mit verwalteter Identität  
 Verwenden Sie die Richtlinie `authentication-managed-identity` für die Authentifizierung mit einem Back-End-Dienst unter Verwendung einer verwalteten Identität. Diese Richtlinie verwendet im Grunde die verwaltete Identität, um aus Azure Active Directory ein Zugriffstoken für den Zugriff auf die angegebene Ressource abzurufen. Wenn das Token erfolgreich abgerufen wurde, legt die Richtlinie den Wert des Tokens unter Verwendung des Schemas `Bearer` im Header `Authorization` fest.

Sowohl eine systemseitig zugewiesene Identität als auch eine der benutzerseitig zugewiesenen Identitäten können verwendet werden, um Token anzufordern. Wenn `client-id` nicht bereitgestellt wird, wird die systemseitig zugewiesene Identität angenommen. Wenn die `client-id`-Variable bereitgestellt wird, wird das Token für die entsprechende benutzerseitig zugewiesene Identität aus Azure Active Directory angefordert.
  
### <a name="policy-statement"></a>Richtlinienanweisung  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Beispiel  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Verwenden der verwalteten Identität für die Authentifizierung bei einem Back-End-Dienst
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Verwenden der verwalteten Identität und manuelles Festlegen des Headers

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Verwenden der verwalteten Identität in einer Richtlinie vom Typ „send-request“
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elemente  
  
|Name|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Stammelement|Ja|  
  
### <a name="attributes"></a>Attributes  
  
|Name|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|resource|Eine Zeichenfolge. Die App-ID der Ziel-Web-API (geschützte Ressource) in Azure Active Directory.|Ja|–|
|client-id|Eine Zeichenfolge. Die App-ID der benutzerseitig zugewiesenen Identität in Azure Active Directory.|Nein|Systemseitig zugewiesene Identität|
|output-token-variable-name|Eine Zeichenfolge. Name der Kontextvariablen, die den Tokenwert als Objekttyp erhält `string`. |Nein|–|  
|ignore-error|Boolesch. Bei Festlegung auf `true` wird die Richtlinienpipeline auch dann weiter ausgeführt, wenn kein Zugriffstoken abgerufen wird.|Nein|false|  
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte**: inbound  
  
-   **Richtlinienbereiche:** alle Bereiche

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management-Richtlinienbeispiele](./policy-reference.md)