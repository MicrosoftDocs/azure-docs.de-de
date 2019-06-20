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
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708793"
---
# <a name="api-management-authentication-policies"></a>API Management-Authentifizierungsrichtlinien
Dieses Thema enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Authentifizierungsrichtlinien  
  
-   [Standardauthentifizierung](api-management-authentication-policies.md#Basic) – Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.  
  
-   [Authentifizierung mit Clientzertifikat](api-management-authentication-policies.md#ClientCertificate) – Authentifizierung mit einem Back-End-Dienst unter Verwendung von Clientzertifikaten.  

-   [Authentifizierung mit verwalteter Identität](api-management-authentication-policies.md#ManagedIdentity) – Authentifizierung für den API Management-Dienst mit der [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md).  
  
##  <a name="Basic"></a> Standardauthentifizierung  
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
  
|NAME|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-basic|Stammelement|Ja|  
  
### <a name="attributes"></a>Attribute  
  
|NAME|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|userName|Gibt den Benutzernamen für die Standardanmeldeinformationen an.|Ja|–|  
|password|Gibt das Kennwort für die Standardanmeldeinformationen an.|Ja|–|  
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte**: inbound  
  
-   **Richtlinienbereiche:** API  
  
##  <a name="ClientCertificate"></a> Authentifizieren mit Clientzertifikat  
 Verwenden Sie die Richtlinie `authentication-certificate` für die Authentifizierung mit einem Back-End-Dienst unter Verwendung eines Clientzertifikats. Das Zertifikat muss zuerst [in API Management installiert](https://go.microsoft.com/fwlink/?LinkID=511599) werden, es wird durch seinen Fingerabdruck identifiziert.  
  
### <a name="policy-statement"></a>Richtlinienanweisung  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Beispiele  
  
In diesem Beispiel wird das Clientzertifikat durch seinen Fingerabdruck identifiziert.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
In diesem Beispiel wird das Clientzertifikat durch seinen Ressourcennamen identifiziert.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elemente  
  
|NAME|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-certificate|Stammelement|Ja|  
  
### <a name="attributes"></a>Attribute  
  
|NAME|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Der Fingerabdruck für das Clientzertifikat.|Entweder `thumbprint` oder `certificate-id` muss vorhanden sein.|–|  
|certificate-id|Der Zertifikatressourcenname.|Entweder `thumbprint` oder `certificate-id` muss vorhanden sein.|–|  
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte**: inbound  
  
-   **Richtlinienbereiche:** API  

##  <a name="ManagedIdentity"></a> Authentifizierung mit verwalteter Identität  
 Verwenden Sie die `authentication-managed-identity`-Richtlinie, um sich mit der verwalteten Identität des API Management-Diensts bei einem Back-End-Dienst zu authentifizieren. Diese Richtlinie verwendet die verwaltete Identität, um aus Azure Active Directory ein Zugriffstoken für den Zugriff auf die angegebene Ressource abzurufen. 
  
### <a name="policy-statement"></a>Richtlinienanweisung  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Beispiel  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elemente  
  
|NAME|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Stammelement|Ja|  
  
### <a name="attributes"></a>Attribute  
  
|NAME|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|resource|Eine Zeichenfolge. Der App-ID-URI der Ziel-Web-API (geschützte Ressource) in Azure Active Directory.|Ja|–|  
|output-token-variable-name|Eine Zeichenfolge. Name der Kontextvariablen, die den Tokenwert als Objekttyp erhält `string`.|Nein|–|  
|ignore-error|Boolesch. Bei Festlegung auf `true` wird die Richtlinienpipeline auch dann weiter ausgeführt, wenn kein Zugriffstoken abgerufen wird.|Nein|false|  
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte**: inbound  
  
-   **Richtlinienbereiche**: global, Produkt, API, Vorgang  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)   
