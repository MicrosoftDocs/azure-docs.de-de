---
title: Ressourcen von Azure API Management-Vorlagen | Microsoft-Dokumentation
description: Enthält Informationen zu den Arten von Ressourcen, die für die Verwendung in Entwicklerportalvorlagen in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 673dcbeb630899eebc328cd4fae16f7fe8f47a55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60557884"
---
# <a name="azure-api-management-template-resources"></a>Ressourcen von Azure API Management-Vorlagen
Azure API Management enthält die folgenden Arten von Ressourcen zur Verwendung in Entwicklerportalvorlagen.  
  
-   [Zeichenfolgenressourcen](#strings)  
  
-   [Glyphenressourcen](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Zeichenfolgenressourcen  
 API Management enthält einen umfassenden Satz von Zeichenfolgenressourcen zur Verwendung im Entwicklerportal. Diese Ressourcen wurden für alle Sprachen lokalisiert, die von API Management unterstützt werden. Im Standardsatz der Vorlagen werden diese Ressourcen für Seitenkopfzeilen, Bezeichnungen und alle konstanten Zeichenfolgen verwendet, die im Entwicklerportal angezeigt werden. Geben Sie zum Verwenden einer Zeichenfolgenressource wie im folgenden Beispiel in Ihren Vorlagen das Ressourcenzeichenfolgen-Präfix gefolgt vom Zeichenfolgennamen an.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Das folgende Beispiel stammt aus der Vorlage „Product list“ (Produktliste), und oben auf der Seite wird **Products** (Produkte) angezeigt.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Die folgenden lokalen Lokalisierungsoptionen werden unterstützt:

| Gebietsschema    | Sprache               |
|-----------|------------------------|
| „de“      | „English“              |
| „cs“      | „Čeština“              |
| „de“      | „Deutsch“              |
| „es“      | „Español“              |
| „fr“      | „Français“             |
| „hu“      | „Hungarian“               |
| „it“      | „Italiano“             |
| „ja-JP“   | „日本語“                |
| „ko“      | „한국어“                |
| „nl“      | „Nederlands“           |
| „pl“      | „Polski“               |
| „pt-BR“   | „Português (Brasil)“   |
| „pt-pt“   | „Português (Portugal)“ |
| „ru“      | „Русский“              |
| „sv“      | „Svenska“              |
| „tr“      | „Türkçe“               |
| „zh-hans“ | „中文(简体)“           |
| „zh-hant“ | „中文(繁體)“           |

 In der folgenden Tabelle sind die Zeichenfolgenressourcen aufgeführt, die zur Verwendung in Ihren Entwicklerportalvorlagen verfügbar sind. Verwenden Sie den Tabellennamen als Präfix für die Zeichenfolgenressourcen in dieser Tabelle.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentation](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Application preview (Anwendungsvorschau)|  
|WebApplicationsRequirementsHeader|Requirements (Anforderungen)|  
|WebApplicationsScreenshotAlt|Screenshot|  
|WebApplicationsScreenshotsHeader|Screenshots|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Are you sure that you want to remove application? (Möchten Sie die Anwendung entfernen?)|  
|WebDevelopersAppNotPublished|Not published (Nicht veröffentlicht)|  
|WebDevelopersAppNotSubmitted|Not submitted (Nicht gesendet)|  
|WebDevelopersAppTableCategoryHeader|Category (Kategorie)|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|State (Zustand)|  
|WebDevelopersEditLink|Edit (Bearbeiten)|  
|WebDevelopersRegisterAppLink|Register application (Anwendung registrieren)|  
|WebDevelopersRemoveLink|Remove (Entfernen)|  
|WebDevelopersSubmitLink|Submit (Senden)|  
|WebDevelopersYourApplicationsHeader|Your applications (Ihre Anwendungen)|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsHeader|Applications (Anwendungen)|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Name|Text|  
|----------|----------|  
|NoItemsToDisplay|No results found. (Keine Ergebnisse gefunden.)|  
|GeneralExceptionMessage|Something is not right. (Ein Problem ist aufgetreten.) It could be a temporary glitch or a bug. (Es kann ein vorübergehender oder dauerhafter Fehler sein.) Please, try again. (Wiederholen Sie den Vorgang.)|  
|GeneralJsonExceptionMessage|Something is not right. (Ein Problem ist aufgetreten.) It could be a temporary glitch or a bug. (Es kann ein vorübergehender oder dauerhafter Fehler sein.) Please, reload the page and try again. (Laden Sie die Seite neu, und wiederholen Sie den Vorgang.)|  
|ConfirmationMessageUnsavedChanges|There are some unsaved changes. (Es sind nicht gespeicherte Änderungen vorhanden.) Are you sure you want to cancel and discard the changes? (Möchten Sie die Änderungen abbrechen und verwerfen?)|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Request Body too large. (HTTP-Anforderungstext ist zu groß.)|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelCancel|Abbrechen|  
|ButtonLabelSave|Speichern|  
|GeneralExceptionMessage|Something is not right. (Ein Problem ist aufgetreten.) It could be a temporary glitch or a bug. (Es kann ein vorübergehender oder dauerhafter Fehler sein.) Please, try again. (Wiederholen Sie den Vorgang.)|  
|NoItemsToDisplay|There are no items to display. (Es sind keine Elemente vorhanden, die angezeigt werden können.)|  
|PagerButtonLabelFirst|First (Erster)|  
|PagerButtonLabelLast|Last (Letzter)|  
|PagerButtonLabelNext|Next (Weiter)|  
|PagerButtonLabelPrevious|Prev (Vorheriger)|  
|PagerLabelPageNOfM|Seite {0} von {1}|  
|PasswordTooShort|The Password is too short (Das Kennwort ist zu kurz.)|  
|EmailAsPassword|Do not use your email as your password (E-Mail-Adresse nicht als Kennwort verwenden)|  
|PasswordSameAsUserName|Your password cannot contain your username (Das Kennwort darf nicht Ihren Benutzernamen enthalten.)|  
|PasswordTwoCharacterClasses|Use different character classes (Verwenden Sie unterschiedliche Zeichenklassen.)|  
|PasswordTooManyRepetitions|Too many repetitions (Zu viele Wiederholungen)|  
|PasswordSequenceFound|Your password contains sequences (Ihr Kennwort enthält Sequenzen.)|  
|PagerLabelPageSize|Page size (Seitengröße)|  
|CurtainLabelLoading|Loading... (Wird geladen...)|  
|TablePlaceholderNothingToDisplay|There is no data for the selected period and scope (Es sind keine Daten für den ausgewählten Zeitraum und Bereich vorhanden.)|  
|ButtonLabelClose|Close (Schließen)|  
  
###  <a name="Documentation"></a> Documentation  
  
|Name|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Ungültiger Header '{0}'|  
|WebDocumentationInvalidRequestErrorMessage|Invalid Request URL (Ungültige Anforderungs-URL)|  
|TextboxLabelAccessToken|Access token * (Zugriffstoken)|  
|DropdownOptionPrimaryKeyFormat|Primär-{0}|  
|DropdownOptionSecondaryKeyFormat|Sekundär-{0}|  
|WebDocumentationSubscriptionKeyText|Your subscription key (Ihr Abonnementschlüssel)|  
|WebDocumentationTemplatesAddHeaders|Add required HTTP headers (Erforderliche HTTP-Header hinzufügen)|  
|WebDocumentationTemplatesBasicAuthSample|Basic Authorization Sample (Einfaches Beispiel für Autorisierung)|  
|WebDocumentationTemplatesCurlForBasicAuth|for Basic Authorization use: --user {username}:{password} (Für einfache Autorisierung: --user {benutzername}:{kennwort})|  
|WebDocumentationTemplatesCurlValuesForPath|Specify values for path parameters (shown as {...}), your subscription key and values for query parameters (Geben Sie Werte für Pfadparameter (angezeigt als {...}), Ihren Abonnementschlüssel und Werte für Abfrageparameter an.)|  
|WebDocumentationTemplatesDeveloperKey|Specify your subscription key (Geben Sie Ihren Abonnementschlüssel an)|  
|WebDocumentationTemplatesJavaApache|In diesem Beispiel wird der Apache-HTTP-Client von HTTP Components verwendet (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Specify values for optional parameters, as needed (Geben Sie je nach Bedarf Werte für optionale Parameter an.)|  
|WebDocumentationTemplatesPhpPackage|This sample uses the HTTP_Request2 package. (In diesem Beispiel wird das HTTP_Request2-Paket verwendet.) (Weitere Informationen: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Specify values for path parameters (shown as {...}) and request body if needed (Geben Sie Werte für Pfadparameter (angezeigt als {...}) und den Anforderungstext an, falls erforderlich.)|  
|WebDocumentationTemplatesRequestBody|Specify request body (Anforderungstext angeben)|  
|WebDocumentationTemplatesRequiredParams|Specify values for the following required parameters (Geben Sie Werte für die folgenden erforderlichen Parameter an.)|  
|WebDocumentationTemplatesValuesForPath|Specify values for path parameters (shown as {...}) (Geben Sie Wert für Pfadparameter an (angezeigt als {...}).)|  
|OAuth2AuthorizationEndpointDescription|The authorization endpoint is used to interact with the resource owner and obtain an authorization grant. (Der Autorisierungsendpunkt wird zum Interagieren mit dem Ressourcenbesitzer und Beschaffen einer Autorisierungsgenehmigung verwendet.)|  
|OAuth2AuthorizationEndpointName|Authorization endpoint (Autorisierungsendpunkt)|  
|OAuth2TokenEndpointDescription|The token endpoint is used by the client to obtain an access token by presenting its authorization grant or refresh token. (Der Tokenendpunkt wird vom Client verwendet, um ein Zugriffstoken zu erhalten, indem die Autorisierungsgenehmigung oder das Aktualisierungstoken vorgewiesen werden.)|  
|OAuth2TokenEndpointName|Token endpoint (Tokenendpunkt)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\>         The client initiates the flow by directing the resource owner's         user-agent to the authorization endpoint. (Der Client initiiert den Flow, indem der Benutzer-Agent des Ressourcenbesitzers an den Autorisierungsendpunkt geleitet wird.)  The client includes         its client identifier, requested scope, local state, and a         redirection URI to which the authorization server will send the         user-agent back once access is granted (or denied). (Der Client enthält den Clientbezeichner, angeforderten Bereich, lokalen Status und einen Umleitungs-URI, an den der Autorisierungsserver den Benutzer-Agent zurücksendet, nachdem der Zugriff gewährt (oder verweigert) wurde.)     </p\>     <p\>         The authorization server authenticates the resource owner (via         the user-agent) and establishes whether the resource owner         grants or denies the client's access request. (Der Autorisierungsserver authentifiziert den Ressourcenbesitzer (über den Benutzer-Agent) und ermittelt, ob der Ressourcenbesitzer die Zugriffsanforderung des Clients genehmigt oder verweigert.)     </p\>     <p\>         Assuming the resource owner grants access, the authorization         server redirects the user-agent back to the client using the         redirection URI provided earlier (in the request or during         client registration). (Wenn der Ressourcenbesitzer den Zugriff gewährt, leitet der Autorisierungsserver den Benutzer-Agent über den zuvor bereitgestellten Umleitungs-URI zurück an den Client (in der Anforderung oder während der Clientregistrierung).)  The redirection URI includes an         authorization code and any local state provided by the client         earlier. (Der Umleitungs-URI enthält einen Autorisierungscode sowie alle lokalen Status, die vom Client zuvor bereitgestellt wurden.)     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\>     If the user denies the access request of if the request is invalid, the client will be informed using the following parameters added on to the redirect: </p\> (Wenn der Benutzer die Zugriffsanforderung verweigert, weil sie ungültig ist, wird der Client mit den folgenden Parametern, die der Umleitung hinzugefügt werden, darüber informiert:)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Authorization request (Autorisierungsanforderung)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\>         The client app must send the user to the authorization endpoint in order to initiate the OAuth process. (Die Client-App muss den Benutzer an den Autorisierungsendpunkt senden, um den OAuth-Prozess zu initiieren.)          At the authorization endpoint, the user authenticates and then grants or denies access to the app. (Am Autorisierungsendpunkt wird der Benutzer authentifiziert, und der Zugriff auf die App wird gewährt oder verweigert.)     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\>     Assuming the resource owner grants access, authorization server     redirects the user-agent back to the client using the     redirection URI provided earlier (in the request or during     client registration). (Wenn der Ressourcenbesitzer den Zugriff gewährt, leitet der Autorisierungsserver den Benutzer-Agent über den zuvor bereitgestellten Umleitungs-URI zurück an den Client (in der Anforderung oder während der Clientregistrierung).)  The redirection URI includes an     authorization code and any local state provided by the client     earlier. (Der Umleitungs-URI enthält einen Autorisierungscode sowie alle lokalen Status, die vom Client zuvor bereitgestellt wurden.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\>  The client requests an access token from the authorization     server''s token endpoint by including the authorization code     received in the previous step. (Der Client fordert ein Zugriffstoken vom Tokenendpunkt des Autorisierungsservers an, indem der im vorherigen Schritt empfangene Autorisierungscode eingefügt wird.)  When making the request, the     client authenticates with the authorization server. (Für die Anforderung authentifiziert sich der Client beim Autorisierungsserver.)  The client     includes the redirection URI used to obtain the authorization     code for verification. (Der Client enthält den Umleitungs-URI, der zum Beschaffen des Autorisierungscodes für die Überprüfung verwendet wird.) </p\> <p\>     The authorization server authenticates the client, validates the     authorization code, and ensures that the redirection URI     received matches the URI used to redirect the client in     step (C). (Der Autorisierungsserver authentifiziert den Client, überprüft den Autorisierungscode und stellt sicher, dass der empfangene Umleitungs-URI mit dem URI übereinstimmt, der zum Umleiten des Clients in Schritt (C) verwendet wird.)  If valid, the authorization server responds back with     an access token and, optionally, a refresh token. (Wenn er gültig ist, antwortet der Autorisierungsserver mit einem Zugriffstoken und, optional, mit einem Aktualisierungstoken.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\>     If the request client     authentication failed or is invalid, the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following     parameters with the response. (Wenn die Authentifizierung des Anforderungsclients nicht erfolgreich oder ungültig ist, antwortet der Autorisierungsserver mit dem Statuscode „HTTP 400 (Ungültige Anforderung)“ (sofern nichts anderes angegeben ist) und den folgenden Parametern.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\>   The client makes a request to the token endpoint by sending the     following parameters using the "application/x-www-form-urlencoded"  format with a character encoding of UTF-8 in the HTTP  request entity-body. (Der Client stellt eine Anforderung an den Tokenendpunkt, indem die folgenden Parameter im Format „application/x-www-form-urlencoded“ mit der UTF-8-Zeichencodierung im Text der HTTP-Anforderungsentität gesendet werden.) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\>  The authorization server issues an access token and optional refresh    token, and constructs the response by adding the following parameters   to the entity-body of the HTTP response with a 200 (OK) status code. (Der Autorisierungsserver stellt ein Zugriffstoken und ein optionales Aktualisierungstoken aus und erstellt die Antwort, indem dem Entitätstext der HTTP-Antwort mit dem Statuscode „200 (OK)“ die folgenden Parameter hinzugefügt werden.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\>  The client authenticates with the authorization server and     requests an access token from the token endpoint. (Der Client authentifiziert sich beim Autorisierungsserver und fordert ein Zugriffstoken vom Tokenendpunkt an.) </p\> <p\>  The authorization server authenticates the client, and if valid,     issues an access token. (Der Autorisierungsserver authentifiziert den Client und stellt ein Zugriffstoken aus, wenn die Authentifizierung erfolgreich ist.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>     If the request failed client authentication or is invalid   the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following     parameters with the response. (Wenn die Authentifizierung des Anforderungsclients nicht erfolgreich oder ungültig ist, antwortet der Autorisierungsserver mit dem Statuscode „HTTP 400 (Ungültige Anforderung)“ (sofern nichts anderes angegeben ist) und den folgenden Parametern.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>   The client makes a request to the token endpoint by adding the     following parameters using the "application/x-www-form-urlencoded"     format with a character encoding of UTF-8 in the HTTP     request entity-body. (Der Client stellt eine Anforderung an den Tokenendpunkt, indem die folgenden Parameter im Format „application/x-www-form-urlencoded“ mit der UTF-8-Zeichencodierung im Text der HTTP-Anforderungsentität hinzugefügt werden.) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>  If the access token request is valid and authorized, the     authorization server issues an access token and optional refresh     token, and constructs the response by adding the following parameters     to the entity-body of the HTTP response with a 200 (OK) status code. (Wenn die Zugriffstokenanforderung gültig ist und die Autorisierung erfolgt, stellt der Autorisierungsserver ein Zugriffstoken und optional ein Aktualisierungstoken aus und erstellt die Antwort, indem dem Entitätstext der HTTP-Antwort mit dem Statuscode „200 (OK)“ die folgenden Parameter hinzugefügt werden.) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\>   The client initiates the flow by directing the resource owner''s     user-agent to the authorization endpoint. (Der Client initiiert den Flow, indem der Benutzer-Agent des Ressourcenbesitzers an den Autorisierungsendpunkt geleitet wird.)  The client includes     its client identifier, requested scope, local state, and a     redirection URI to which the authorization server will send the     user-agent back once access is granted (or denied). (Der Client enthält den Clientbezeichner, angeforderten Bereich, lokalen Status und einen Umleitungs-URI, an den der Autorisierungsserver den Benutzer-Agent zurücksendet, nachdem der Zugriff gewährt (oder verweigert) wurde.) </p\> <p\>        The authorization server authenticates the resource owner (via     the user-agent) and establishes whether the resource owner     grants or denies the client''s access request. (Der Autorisierungsserver authentifiziert den Ressourcenbesitzer (über den Benutzer-Agent) und ermittelt, ob der Ressourcenbesitzer die Zugriffsanforderung des Clients genehmigt oder verweigert.) </p\> <p\>        Assuming the resource owner grants access, the authorization   server redirects the user-agent back to the client using the   redirection URI provided earlier. (Wenn der Ressourcenbesitzer den Zugriff gewährt, leitet der Autorisierungsserver den Benutzer-Agent über den zuvor bereitgestellten Umleitungs-URI zurück an den Client.)  The redirection URI includes     the access token in the URI fragment. (Der Umleitungs-URI enthält das Zugriffstoken im URI-Fragment.) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\>     If the resource owner denies the access request or if the request     fails for reasons other than a missing or invalid redirection URI,     the authorization server informs the client by adding the following     parameters to the fragment component of the redirection URI using the     "application/x-www-form-urlencoded" format. (Wenn der Ressourcenbesitzer die Zugriffsanforderung verweigert oder wenn die Anforderung aus anderen Gründen als einem fehlenden oder ungültigen Umleitungs-URI nicht erfolgreich ist, wird der Client vom Autorisierungsserver informiert, indem der Fragmentkomponente des Umleitungs-URI im Format „application/x-www-form-urlencoded“ die folgenden Parameter hinzugefügt werden.) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\>     (Die Client-App muss den Benutzer an den Autorisierungsendpunkt senden, um den OAuth-Prozess zu initiieren.)      At the authorization endpoint, the user authenticates and then grants or denies access to the app. (Am Autorisierungsendpunkt wird der Benutzer authentifiziert, und der Zugriff auf die App wird gewährt oder verweigert.) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\>     If the resource owner grants the access request, the authorization     server issues an access token and delivers it to the client by adding     the following parameters to the fragment component of the redirection     URI using the "application/x-www-form-urlencoded" format. (Wenn der Ressourcenbesitzer die Zugriffsanforderung genehmigt, stellt der Autorisierungsserver ein Zugriffstoken aus und sendet es an den Client, indem die folgenden Parameter an die Fragmentkomponente dem Umleitungs-URI im Format „application/x-www-form-urlencoded“ hinzugefügt werden.) </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Authorization code flow is optimized for clients capable of maintaining the confidentiality of their credentials (e.g., web server applications implemented using  PHP, Java, Python, Ruby, ASP.NET, etc.). (Der Autorisierungscodeflow wurde für Clients optimiert, die die Vertraulichkeit ihrer Anmeldeinformationen verwalten können (z.B. Webserveranwendungen mit einer Implementierung von PHP, Java, Python, Ruby, ASP.NET usw.).)|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Authorization Code grant (Autorisierungscodegenehmigung)|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Client credentials flow is suitable in cases where the client (your application) is requesting access to the protected resources under its control. (Der Clientanmeldeinformations-Flow ist in Fällen geeignet, in denen der Client (Ihre Anwendung) Zugriff auf die geschützten Ressourcen unter seiner Kontrolle anfordert.) The client is considered as a resource owner, so no end-user interaction is required. (Der Client wird als Ressourcenbesitzer angesehen, sodass keine Interaktion mit dem Endbenutzer erforderlich ist.)|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Client Credentials grant (Genehmigung der Clientanmeldeinformationen)|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit flow is optimized for clients incapable of maintaining the confidentiality of their credentials known to operate a particular redirection URI. (Der implizite Flow wurde für Clients optimiert, bei denen es nicht möglich ist, die Vertraulichkeit ihrer Anmeldeinformationen aufrechtzuerhalten, und für die ein bestimmter Umleitungs-URI verwendet wird.) These clients are typically implemented in a browser using a scripting language such as JavaScript. (Diese Clients werden normalerweise in einem Browser implementiert, indem eine Skriptsprache verwendet wird, z.B. JavaScript.)|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit grant (Implizite Gewährung)|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Resource owner password credentials flow is suitable in cases where the resource owner has a trust relationship with the client (your application), such as the device operating system or a highly privileged application. (Der Kennwortanmeldeinformationen-Flow des Ressourcenbesitzers ist in Fällen geeignet, in denen der Ressourcenbesitzer über eine Vertrauensstellung mit dem Client (Ihre Anwendung) verfügt, z.B. dem Betriebssystem des Geräts oder einer Anwendung mit erhöhten Rechten.) This flow is suitable for clients capable of obtaining the resource owner's credentials (username and password, typically using an interactive form). (Dieser Flow ist für Clients geeignet, die die Anmeldeinformationen des Ressourcenbesitzers abrufen können (Benutzername und Kennwort, meist per interaktiven Formular).)|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Resource Owner Password Credentials grant (Gewährung der Kennwortanmeldeinformationen des Ressourcenbesitzers)|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\>   The resource owner provides the client with its username and     password. (Der Ressourcenbesitzer stellt für den Client den Benutzernamen und das Kennwort bereit.) </p\> <p\> The client requests an access token from the authorization     server''s token endpoint by including the credentials received     from the resource owner. (Der Client fordert ein Zugriffstoken vom Tokenendpunkt des Autorisierungsservers an, indem die vom Ressourcenbesitzer empfangenen Anmeldeinformationen eingefügt wurden.)  When making the request, the client     authenticates with the authorization server. (Für die Anforderung authentifiziert sich der Client beim Autorisierungsserver.) </p\> <p\>     The authorization server authenticates the client and validates     the resource owner credentials, and if valid, issues an access     token. (Der Autorisierungsserver authentifiziert den Client und überprüft die Anmeldeinformationen des Ressourcenbesitzers. Er stellt ein Zugriffstoken aus, wenn die Anmeldeinformationen gültig sind.) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>  If the request failed client authentication or is invalid   the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following     parameters with the response. (Wenn die Authentifizierung des Anforderungsclients nicht erfolgreich oder ungültig ist, antwortet der Autorisierungsserver mit dem Statuscode „HTTP 400 (Ungültige Anforderung)“ (sofern nichts anderes angegeben ist) und den folgenden Parametern.) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>    The client makes a request to the token endpoint by adding the     following parameters using the "application/x-www-form-urlencoded"     format with a character encoding of UTF-8 in the HTTP     request entity-body. (Der Client stellt eine Anforderung an den Tokenendpunkt, indem die folgenden Parameter im Format „application/x-www-form-urlencoded“ mit der UTF-8-Zeichencodierung im Text der HTTP-Anforderungsentität hinzugefügt werden.) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>   If the access token request is valid and authorized, the     authorization server issues an access token and optional refresh     token, and constructs the response by adding the following parameters     to the entity-body of the HTTP response with a 200 (OK) status code. (Wenn die Zugriffstokenanforderung gültig ist und die Autorisierung erfolgt, stellt der Autorisierungsserver ein Zugriffstoken und optional ein Aktualisierungstoken aus und erstellt die Antwort, indem dem Entitätstext der HTTP-Antwort mit dem Statuscode „200 (OK)“ die folgenden Parameter hinzugefügt werden.) </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Access token request (Zugriffstokenanforderung)|  
|OAuth2Step_AuthorizationRequest_Name|Authorization request (Autorisierungsanforderung)|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|REQUIRED. (ERFORDERLICH.) The access token issued by the authorization server. (Das vom Autorisierungsserver ausgestellte Zugriffstoken.)|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|REQUIRED. (ERFORDERLICH.) The access token issued by the authorization server. (Das vom Autorisierungsserver ausgestellte Zugriffstoken.)|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|REQUIRED. (ERFORDERLICH.) The access token issued by the authorization server. (Das vom Autorisierungsserver ausgestellte Zugriffstoken.)|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REQUIRED. (ERFORDERLICH.) The access token issued by the authorization server. (Das vom Autorisierungsserver ausgestellte Zugriffstoken.)|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|REQUIRED. (ERFORDERLICH.) Client identifier. (Clientbezeichner.)|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|REQUIRED if the client is not authenticating with the authorization server. (ERFORDERLICH, wenn sich der Client nicht beim Autorisierungsserver authentifiziert.)|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|REQUIRED. (ERFORDERLICH.) The client identifier. (Der Clientbezeichner.)|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|REQUIRED. (ERFORDERLICH.) The authorization code generated by the authorization server. (Der vom Autorisierungsserver generierte Autorisierungscode.)|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|REQUIRED. (ERFORDERLICH.) The authorization code received from the authorization server. (Der vom Autorisierungsserver empfangene Autorisierungscode.)|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPTIONAL. Human-readable ASCII text providing additional information. (Visuell lesbarer ASCII-Text mit weiteren Informationen.)|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPTIONAL. Human-readable ASCII text providing additional information. (Visuell lesbarer ASCII-Text mit weiteren Informationen.)|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPTIONAL. Human-readable ASCII text providing additional information. (Visuell lesbarer ASCII-Text mit weiteren Informationen.)|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPTIONAL. Human-readable ASCII text providing additional information. (Visuell lesbarer ASCII-Text mit weiteren Informationen.)|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPTIONAL. Human-readable ASCII text providing additional information. (Visuell lesbarer ASCII-Text mit weiteren Informationen.)|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPTIONAL. A URI identifying a human-readable web page with information about the error. (Ein URI zum Identifizieren einer visuell lesbaren Webseite mit Informationen zum Fehler.)|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPTIONAL. A URI identifying a human-readable web page with information about the error. (Ein URI zum Identifizieren einer visuell lesbaren Webseite mit Informationen zum Fehler.)|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPTIONAL. A URI identifying a human-readable web page with information about the error. (Ein URI zum Identifizieren einer visuell lesbaren Webseite mit Informationen zum Fehler.)|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPTIONAL. A URI identifying a human-readable web page with information about the error. (Ein URI zum Identifizieren einer visuell lesbaren Webseite mit Informationen zum Fehler.)|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPTIONAL. A URI identifying a human-readable web page with information about the error. (Ein URI zum Identifizieren einer visuell lesbaren Webseite mit Informationen zum Fehler.)|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUIRED. (ERFORDERLICH.) A single ASCII error code from the following: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable. (Ein ASCII-Fehlercode: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.)|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|REQUIRED. (ERFORDERLICH.) A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (Ein ASCII-Fehlercode: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.)|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|REQUIRED. (ERFORDERLICH.) A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (Ein ASCII-Fehlercode: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.)|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|REQUIRED. (ERFORDERLICH.) A single ASCII error code from the following: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable. (Ein ASCII-Fehlercode: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.)|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|REQUIRED. (ERFORDERLICH.) A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (Ein ASCII-Fehlercode: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.)|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMMENDED. (EMPFOHLEN.) The lifetime in seconds of the access token. (Die Lebensdauer des Zugriffstokens in Sekunden.)|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMMENDED. (EMPFOHLEN.) The lifetime in seconds of the access token. (Die Lebensdauer des Zugriffstokens in Sekunden.)|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMMENDED. (EMPFOHLEN.) The lifetime in seconds of the access token. (Die Lebensdauer des Zugriffstokens in Sekunden.)|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMMENDED. (EMPFOHLEN.) The lifetime in seconds of the access token. (Die Lebensdauer des Zugriffstokens in Sekunden.)|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|REQUIRED. (ERFORDERLICH.) Value MUST be set to "authorization_code". (Wert MUSS auf „authorization_code“ festgelegt werden.)|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|REQUIRED. (ERFORDERLICH.) Value MUST be set to "client_credentials". (Wert MUSS auf „client_credentials“ festgelegt werden.)|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUIRED. (ERFORDERLICH.) Value MUST be set to "password". (Wert MUSS auf „password“ festgelegt werden.)|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUIRED. (ERFORDERLICH.) The resource owner password. (Das Kennwort des Ressourcenbesitzers.)|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPTIONAL. The redirection endpoint URI must be an absolute URI. (Der Umleitungsendpunkt-URI muss ein absoluter URI sein.)|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|REQUIRED if the "redirect_uri" parameter was included in the authorization request, and their values MUST be identical. (ERFORDERLICH, wenn der Parameter „redirect_uri“ in der Autorisierungsanforderung enthalten ist, und die Werte MÜSSEN identisch sein.)|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPTIONAL. The redirection endpoint URI must be an absolute URI. (Der Umleitungsendpunkt-URI muss ein absoluter URI sein.)|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPTIONAL. The refresh token, which can be used to obtain new access tokens. (Das Aktualisierungstoken, das zum Beschaffen neuer Zugriffstoken verwendet werden kann.)|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPTIONAL. The refresh token, which can be used to obtain new access tokens. (Das Aktualisierungstoken, das zum Beschaffen neuer Zugriffstoken verwendet werden kann.)|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPTIONAL. The refresh token, which can be used to obtain new access tokens. (Das Aktualisierungstoken, das zum Beschaffen neuer Zugriffstoken verwendet werden kann.)|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|REQUIRED. (ERFORDERLICH.) Value MUST be set to "code". (Wert MUSS auf „code“ festgelegt werden.)|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|REQUIRED. (ERFORDERLICH.) Value MUST be set to "token". (Wert MUSS auf „token“ festgelegt werden.)|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPTIONAL. The scope of the access request. (Der Bereich der Zugriffsanforderung.)|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (OPTIONAL, falls identisch mit dem vom Client angeforderten Bereich, andernfalls ERFORDERLICH.)|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPTIONAL. The scope of the access request. (Der Bereich der Zugriffsanforderung.)|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED. (OPTIONAL, falls identisch mit dem vom Client angeforderten Bereich, andernfalls ERFORDERLICH.)|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPTIONAL. The scope of the access request. (Der Bereich der Zugriffsanforderung.)|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (OPTIONAL, falls identisch mit dem vom Client angeforderten Bereich, andernfalls ERFORDERLICH.)|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPTIONAL. The scope of the access request. (Der Bereich der Zugriffsanforderung.)|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED. (OPTIONAL, falls identisch mit dem vom Client angeforderten Bereich, andernfalls ERFORDERLICH.)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUIRED if the "state" parameter was present in the client authorization request. (ERFORDERLICH, wenn der Parameter „state“ in der Clientautorisierungsanforderung enthalten war.)  The exact value received from the client. (Der genaue Wert, der vom Client empfangen wurde.)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMMENDED. (EMPFOHLEN.) An opaque value used by the client to maintain state between the request and callback. (Ein nicht transparenter Wert, der vom Client verwendet wird, um den Status zwischen der Anforderung und dem Rückruf beizubehalten.)  The authorization server includes this value when redirecting the user-agent back to the client. (Der Autorisierungsserver enthält diesen Wert, wenn der Benutzer-Agent zurück an den Client geleitet wird.)  The parameter SHOULD be used for preventing cross-site request forgery. (Der Parameter SOLLTE verwendet werden, um die websiteübergreifende Anforderungsfälschung zu verhindern.)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|REQUIRED if the "state" parameter was present in the client authorization request. (ERFORDERLICH, wenn der Parameter „state“ in der Clientautorisierungsanforderung enthalten war.)  The exact value received from the client. (Der genaue Wert, der vom Client empfangen wurde.)|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|REQUIRED if the "state" parameter was present in the client authorization request. (ERFORDERLICH, wenn der Parameter „state“ in der Clientautorisierungsanforderung enthalten war.)  The exact value received from the client. (Der genaue Wert, der vom Client empfangen wurde.)|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMMENDED. (EMPFOHLEN.) An opaque value used by the client to maintain state between the request and callback. (Ein nicht transparenter Wert, der vom Client verwendet wird, um den Status zwischen der Anforderung und dem Rückruf beizubehalten.)  The authorization server includes this value when redirecting the user-agent back to the client. (Der Autorisierungsserver enthält diesen Wert, wenn der Benutzer-Agent zurück an den Client geleitet wird.)  The parameter SHOULD be used for preventing cross-site request forgery. (Der Parameter SOLLTE verwendet werden, um die websiteübergreifende Anforderungsfälschung zu verhindern.)|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|REQUIRED if the "state" parameter was present in the client authorization request. (ERFORDERLICH, wenn der Parameter „state“ in der Clientautorisierungsanforderung enthalten war.)  The exact value received from the client. (Der genaue Wert, der vom Client empfangen wurde.)|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|REQUIRED. (ERFORDERLICH.) The type of the token issued. (Der Typ des ausgestellten Tokens.)|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|REQUIRED. (ERFORDERLICH.) The type of the token issued. (Der Typ des ausgestellten Tokens.)|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|REQUIRED. (ERFORDERLICH.) The type of the token issued. (Der Typ des ausgestellten Tokens.)|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REQUIRED. (ERFORDERLICH.) The type of the token issued. (Der Typ des ausgestellten Tokens.)|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUIRED. (ERFORDERLICH.) The resource owner username. (Der Benutzername des Ressourcenbesitzers.)|  
|OAuth2UnsupportedTokenType|Tokentyp '{0}' wird nicht unterstützt.|  
|OAuth2InvalidState|Invalid response from authorization server (Ungültige Antwort vom Autorisierungsserver)|  
|OAuth2GrantType_AuthorizationCode|Authorization code (Autorisierungscode)|  
|OAuth2GrantType_Implicit|Implicit (Implizit)|  
|OAuth2GrantType_ClientCredentials|Client credentials (Clientanmeldeinformationen)|  
|OAuth2GrantType_ResourceOwnerPassword|Resource owner password (Kennwort des Ressourcenbesitzers)|  
|WebDocumentation302Code|302 Found (302 Gefunden)|  
|WebDocumentation400Code|400 (Bad request) (400 (Ungültige Anforderung))|  
|OAuth2SendingMethod_AuthHeader|Authorization header (Autorisierungsheader)|  
|OAuth2SendingMethod_QueryParam|Query parameter (Abfrageparameter)|  
|OAuth2AuthorizationServerGeneralException|Beim Autorisieren des Zugriffs über {0} ist ein Fehler aufgetreten|  
|OAuth2AuthorizationServerCommunicationException|An HTTP connection to authorization server could not be established or it has been unexpectedly closed. (Eine HTTP-Verbindung mit dem Autorisierungsserver konnte nicht hergestellt werden oder wurde unerwartet geschlossen.)|  
|WebDocumentationOAuth2GeneralErrorMessage|Unerwarteter Fehler.|  
|AuthorizationServerCommunicationException|Authorization server communication exception has happened. (Für den Autorisierungsserver ist eine Ausnahme bei der Kommunikation aufgetreten.) Please contact administrator. (Wenden Sie sich an den Administrator.)|  
|TextblockSubscriptionKeyHeaderDescription|Subscription key which provides access to this API. (Abonnementschlüssel, der den Zugriff auf diese API ermöglicht.) Found in your <a href='/developer'\>Profile</a\> (Gefunden in Ihrem Profil).|  
|TextblockOAuthHeaderDescription|OAuth 2.0-Zugriffstoken erhalten von <i\>{0}</i\>. Unterstützte Gewährungstypen: <i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Media type of the body sent to the API. (Medientyp des an die API gesendeten Texts.)|  
|ErrorMessageApiNotAccessible|The API you are trying to call is not accessible at this time. (Die API, die Sie aufrufen möchten, ist derzeit nicht verfügbar.) Please contact the API publisher <a href="/issues"\>here</a\> (Wenden Sie sich an den API-Herausgeber).|  
|ErrorMessageApiTimedout|The API you are trying to call is taking longer than normal to get response back. (Für die API, die Sie aufrufen möchten, dauert das Erhalten einer Antwort länger als normal.) Please contact the API publisher <a href="/issues"\>here</a\> (Wenden Sie sich an den API-Herausgeber).|  
|BadRequestParameterExpected|„Parameter '{0}' wird erwartet“|  
|TooltipTextDoubleClickToSelectAll|Double click to select all. (Doppelklicken, um alles auszuwählen.)|  
|TooltipTextHideRevealSecret|Show/Hide (Ein-/Ausblenden)|  
|ButtonLinkOpenConsole|Ausprobieren|  
|SectionHeadingRequestBody|Anforderungstext|  
|SectionHeadingRequestParameters|Anforderungsparameter|  
|SectionHeadingRequestUrl|Anfrage-URL|  
|SectionHeadingResponse|Antwort|  
|SectionHeadingRequestHeaders|Anforderungsheader|  
|FormLabelSubtextOptional|optional|  
|SectionHeadingCodeSamples|Codebeispiele|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect-ID-Token erhalten von <i\>{0}</i\>. Unterstützte Gewährungstypen: <i\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelBack|back (Zurück)|  
|LinkLabelHomePage|Startseite|  
|LinkLabelSendUsEmail|Send us an e-mail (Senden Sie uns eine E-Mail)|  
|PageTitleError|Sorry, there was a problem serving the requested page (Beim Bereitstellen der angeforderten Seite ist leider ein Fehler aufgetreten.)|  
|TextblockPotentialCauseIntermittentIssue|This may be an intermittent data access issue that is already gone. (Es kann sich um ein vorübergehendes Problem mit dem Datenzugriff handeln, das bereits beseitigt wurde.)|  
|TextblockPotentialCauseOldLink|The link you have clicked on may be old and not point to the correct location anymore. (Der Link, auf den Sie geklickt haben, ist ggf. alt und verweist nicht mehr auf den richtigen Speicherort.)|  
|TextblockPotentialCauseTechnicalProblem|There may be a technical problem on our end. (Unter Umständen liegt auf unserer Seite ein technisches Problem vor.)|  
|TextblockPotentialSolutionRefresh|Try refreshing the page. (Versuchen Sie, die Seite zu aktualisieren.)|  
|TextblockPotentialSolutionStartOver|Beginnen Sie erneut, indem Sie auf {0} zugreifen.|  
|TextblockPotentialSolutionTryAgain|Navigieren Sie zu {0}, und versuchen Sie, den Vorgang zu wiederholen.|  
|TextReportProblem|Beschreiben Sie den Fehler unter {0}. Wir sehen uns dies so schnell wie möglich an.|  
|TitlePotentialCause|Potential cause (Mögliche Ursache)|  
|TitlePotentialSolution|It's possibly just a temporary issue, a few things to try (Unter Umständen ist es nur ein vorübergehendes Problem. Sie können es mit folgenden Schritten probieren:)|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Name|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Probleme|  
|WebIssuesNoActiveSubscriptions|You have no active subscriptions. (Sie haben keine aktiven Abonnements.) You need to subscribe for a product to report an issue. (Sie müssen ein Produkt abonnieren, um ein Problem zu melden.)|  
|WebIssuesNotSignin|You're not signed in. (Sie sind nicht angemeldet.) Nutzen Sie {0}, um ein Problem zu melden oder einen Kommentar zu hinterlassen.|  
|WebIssuesReportIssueButton|Report Issue (Problem melden)|  
|WebIssuesSignIn|Anmelden|  
|WebIssuesStatusReportedBy|Status: {0} &#124; Gemeldet von {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelHomePage|Startseite|  
|LinkLabelSendUsEmail|Send us an e-mail (Senden Sie uns eine E-Mail)|  
|PageTitleNotFound|Sorry, we can’t find the page you are looking for (Die gesuchte Seite wurde leider nicht gefunden.)|  
|TextblockPotentialCauseMisspelledUrl|You may have misspelled the URL if you typed it in. (Unter Umständen haben Sie die URL falsch eingegeben.)|  
|TextblockPotentialCauseOldLink|The link you have clicked on may be old and not point to the correct location anymore. (Der Link, auf den Sie geklickt haben, ist ggf. alt und verweist nicht mehr auf den richtigen Speicherort.)|  
|TextblockPotentialSolutionRetype|Try retyping the URL. (Versuchen Sie, die URL neu einzugeben.)|  
|TextblockPotentialSolutionStartOver|Beginnen Sie erneut, indem Sie auf {0} zugreifen.|  
|TextReportProblem|Beschreiben Sie den Fehler unter {0}. Wir sehen uns dies so schnell wie möglich an.|  
|TitlePotentialCause|Potential cause (Mögliche Ursache)|  
|TitlePotentialSolution|Potential solution (Mögliche Lösung)|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebProductsAgreement|Indem ich das Produkt {0} abonniere, stimme ich den `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>` zu.|  
|WebProductsLegalTermsLink|Terms of Use (Nutzungsbedingungen)|  
|WebProductsSubscribeButton|Abonnieren|  
|WebProductsUsageLimitsHeader|Usage limits (Nutzungseinschränkungen)|  
|WebProductsYouAreNotSubscribed|You are subscribed to this product. (Sie haben dieses Produkt abonniert.)|  
|WebProductsYouRequestedSubscription|You requested subscription to this product. (Sie haben ein Abonnement dieses Produkts angefordert.)|  
|ErrorYouNeedtoAgreeWithLegalTerms|You must agree to the Terms of Use before you can proceed. (Sie müssen den Nutzungsbedingungen zustimmen, bevor Sie fortfahren können.)|  
|ButtonLabelAddSubscription|Abonnement hinzufügen|  
|LinkLabelChangeSubscriptionName|change (Ändern)|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|Sie verfügen über {0} Abonnements für dieses Produkt:|  
|TextblockSingleSubscriptionsCount|Sie verfügen über {0} Abonnement für dieses Produkt:|  
|TextblockSingleApisCount|Dieses Produkt enthält {0} API:|  
|TextblockMultipleApisCount|Dieses Produkt enthält {0} APIs:|  
|TextblockHeaderSubscribe|Subscribe to product (Produkt abonnieren)|  
|TextblockSubscriptionDescription|A new subscription will be created as follows: (Ein neues Abonnement wird wie folgt erstellt:)|  
|TextblockSubscriptionLimitReached|Subscriptions limit reached. (Grenzwert für Abonnements wurde erreicht.)|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleProducts|Produkte|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Sign in is disabled by the administrators at the moment. (Die Anmeldung wurde von den Administratoren deaktiviert.)|  
|TextboxExternalIdentitiesSigninInvitation|Alternatively, sign in with (Als Alternative ist die Anmeldung möglich mit)|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Sign in with: (Anmelden mit:)|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Name|Text|  
|----------|----------|  
|PrincipalNotFound|Principal is not found or signature is invalid (Prinzipal nicht gefunden, oder Signatur ist ungültig)|  
|ErrorSsoAuthenticationFailed|SSO authentication failed (Fehler bei SSO-Authentifizierung)|  
|ErrorSsoAuthenticationFailedDetailed|Invalid token provided or signature cannot be verified. (Ungültiges Token angegeben, oder Signatur kann nicht überprüft werden.)|  
|ErrorSsoTokenInvalid|SSO token is invalid (SSO-Token ist ungültig)|  
|ValidationErrorSpecificEmailAlreadyExists|E-Mail-Adresse {0} ist bereits registriert.|  
|ValidationErrorSpecificEmailInvalid|E-Mail-Adresse {0} ist ungültig.|  
|ValidationErrorPasswordInvalid|Password is invalid. (Kennwort ist ungültig.) Please correct the errors and try again. (Beheben Sie die Fehler, und wiederholen Sie den Vorgang.)|  
|PropertyTooShort|{0} ist zu kurz|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Invalid email address. (Ungültige E-Mail-Adresse.)|  
|ValidationMessageNewPasswordConfirmationRequired|Confirm new password (Neues Kennwort bestätigen)|  
|ValidationErrorPasswordConfirmationRequired|Confirm password is empty (Bestätigungskennwort ist leer)|  
|WebAuthenticationEmailChangeNotice|Die E-Mail mit der Änderungsbestätigung wurde an {0} gesendet. Please follow instructions within it to confirm your new email address. (Befolgen Sie die darin enthaltenen Anweisungen, um Ihre neue E-Mail-Adresse zu bestätigen.) If the email does not arrive to your inbox in the next few minutes, please check your junk email folder. (Überprüfen Sie Ihren Junk-E-Mail-Ordner, falls die E-Mail nicht in den nächsten Minuten in Ihrem Posteingang eintrifft.)|  
|WebAuthenticationEmailChangeNoticeHeader|Your email change request was successfully processed (Die angeforderte Änderung der E-Mail-Adresse wurde verarbeitet.)|  
|WebAuthenticationEmailChangeNoticeTitle|Email change requested (Änderung der E-Mail-Adresse angefordert)|  
|WebAuthenticationEmailHasBeenRevertedNotice|You email already exist. (Ihre E-Mail-Adresse ist bereits vorhanden.) Request has been reverted (Anforderung wurde zurückgesetzt)|  
|ValidationErrorEmailAlreadyExists|Email already exist (E-Mail-Adresse ist bereits vorhanden.)|  
|ValidationErrorEmailInvalid|Invalid e-mail address (Ungültige E-Mail-Adresse)|  
|TextboxLabelEmail|E-Mail|  
|ValidationErrorEmailRequired|Email is required. (E-Mail-Adresse ist erforderlich.)|  
|WebAuthenticationErrorNoticeHeader|Error|  
|WebAuthenticationFieldLengthErrorMessage|{0} darf maximal eine Länge von {1} haben|  
|TextboxLabelEmailFirstName|Vorname|  
|ValidationErrorFirstNameRequired|First name is required. (Vorname ist erforderlich.)|  
|ValidationErrorFirstNameInvalid|Invalid first name (Ungültiger Vorname)|  
|NoticeInvalidInvitationToken|Please note that confirmation links are valid for only 48 hours. (Hinweis: Die Bestätigungslinks sind nur 48 Stunden lang gültig.) If you are still within this timeframe, please make sure your link is correct. (Überprüfen Sie, ob Ihr Link korrekt ist, falls dieser Zeitraum noch nicht abgelaufen ist.) If your link has expired, then please repeat the action you're trying to confirm. (Falls der Link nicht mehr gültig ist, müssen Sie die Aktion wiederholen, die bestätigt werden soll.)|  
|NoticeHeaderInvalidInvitationToken|Invalid invitation token (Ungültiges Einladungstoken)|  
|NoticeTitleInvalidInvitationToken|Confirmation error (Bestätigungsfehler)|  
|WebAuthenticationLastNameInvalidErrorMessage|Invalid last name (Ungültiger Nachname)|  
|TextboxLabelEmailLastName|Nachname|  
|ValidationErrorLastNameRequired|Last name is required. (Nachname ist erforderlich.)|  
|WebAuthenticationLinkExpiredNotice|Confirmation link sent to you has expired. (Der Bestätigungslink, den Sie erhalten haben, ist abgelaufen.) `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Your password reset link is invalid or expired. (Ihr Link zum Zurücksetzen des Kennworts ist ungültig oder abgelaufen.)|  
|WebAuthenticationLinkExpiredNoticeTitle|Link sent (Gesendeter Link)|  
|WebAuthenticationNewPasswordLabel|Neues Kennwort|  
|ValidationMessageNewPasswordRequired|New password is required. (Neues Kennwort erforderlich.)|  
|TextboxLabelNotificationsSenderEmail|Notifications sender email (E-Mail-Adresse Benachrichtigungsabsender)|  
|TextboxLabelOrganizationName|Organization name (Name der Organisation)|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organization name is empty (Name der Organisation ist leer)|  
|WebAuthenticationPasswordChangedNotice|Your password was successfully updated (Ihr Kennwort wurde aktualisiert)|  
|WebAuthenticationPasswordChangedNoticeTitle|Password updated (Kennwort aktualisiert)|  
|WebAuthenticationPasswordCompareErrorMessage|Passwords don't match (Kennwörter stimmen nicht überein)|  
|WebAuthenticationPasswordConfirmLabel|Kennwort bestätigen|  
|ValidationErrorPasswordInvalidDetailed|Password is too weak. (Kennwort ist nicht sicher genug.)|  
|WebAuthenticationPasswordLabel|Kennwort|  
|ValidationErrorPasswordRequired|Password is required. (Kennwort erforderlich.)|  
|WebAuthenticationPasswordResetSendNotice|Die E-Mail mit der Bestätigung der Kennwortänderung wurde an {0} gesendet. Please follow the instructions within the email to continue your password change process. (Befolgen Sie die Anweisungen in der E-Mail, um den Prozess zum Ändern des Kennworts fortzusetzen.)|  
|WebAuthenticationPasswordResetSendNoticeHeader|Your password reset request was successfully processed (Ihre Anforderung zur Zurücksetzung des Kennworts wurde verarbeitet)|  
|WebAuthenticationPasswordResetSendNoticeTitle|Password reset requested (Kennwortzurücksetzung angefordert)|  
|WebAuthenticationRequestNotFoundNotice|Request not found (Anforderung nicht gefunden)|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Notifications sender email is empty (E-Mail-Adresse des Benachrichtigungsabsenders ist leer)|  
|WebAuthenticationSigninPasswordLabel|Please confirm the change by entering a password (Bestätigen Sie die Änderung durch Eingabe eines Kennworts)|  
|WebAuthenticationSignupConfirmNotice|Die Bestätigungs-E-Mail für die Registrierung wurde an {0} gesendet.<br /\>Befolgen Sie die Anweisungen in der E-Mail, um Ihr Konto zu aktivieren.<br /\> Überprüfen Sie Ihren Junk-E-Mail-Ordner, falls die E-Mail nicht in den nächsten Minuten in Ihrem Posteingang eintrifft.|  
|WebAuthenticationSignupConfirmNoticeHeader|Your account was successfully created (Die Erstellung Ihres Kontos war erfolgreich.)|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Registration confirmation email was sent again (Bestätigungs-E-Mail für die Registrierung wurde erneut gesendet)|  
|WebAuthenticationSignupConfirmNoticeTitle|Erstelltes Konto|  
|WebAuthenticationTokenRequiredErrorMessage|Token is empty (Token ist leer)|  
|WebAuthenticationUserAlreadyRegisteredNotice|It seems a user with this email is already registered in the system. (Ein Benutzer mit dieser E-Mail-Adresse ist scheinbar bereits im System registriert.) If you forgot your password, please try to restore it or contact our support team. (Falls Sie Ihr Kennwort vergessen haben, können Sie versuchen, es wiederherzustellen, oder sich an unser Supportteam wenden.)|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|User already registered (Benutzer ist bereits registriert)|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Already registered (Bereits registriert)|  
|ButtonLabelChangePassword|Kennwort ändern|  
|ButtonLabelChangeAccountInfo|Change account information (Kontoinformationen ändern)|  
|ButtonLabelCloseAccount|Close account (Konto schließen)|  
|WebAuthenticationInvalidCaptchaErrorMessage|Text entered doesn't match text on the picture. (Der eingegebene Text stimmt nicht mit dem Text im Bild überein.) Wiederholen Sie den Vorgang.|  
|ValidationErrorCredentialsInvalid|Email or password is invalid. (E-Mail-Adresse oder Kennwort ist ungültig.) Please correct the errors and try again. (Beheben Sie die Fehler, und wiederholen Sie den Vorgang.)|  
|WebAuthenticationRequestIsNotValid|Request is not valid (Anforderung ist ungültig)|  
|WebAuthenticationUserIsNotConfirm|Please confirm your registration before attempting to sign in. (Bestätigen Sie Ihre Registrierung, bevor Sie versuchen, sich anzumelden.)|  
|WebAuthenticationInvalidEmailFormated|E-Mail-Adresse ist ungültig: {0}|  
|WebAuthenticationUserNotFound|User not found (Benutzer wurde nicht gefunden)|  
|WebAuthenticationTenantNotRegistered|Ihr Konto gehört zu einem Azure Active Directory-Mandanten, der für den Zugriff auf dieses Portal nicht autorisiert ist.|  
|WebAuthenticationAuthenticationFailed|Authentication has failed. (Fehler bei der Authentifizierung.)|  
|WebAuthenticationGooglePlusNotEnabled|Authentication has failed. (Fehler bei der Authentifizierung.) If you authorized the application then please contact the admin to make sure that Google authentication is configured correctly. (Falls Sie die Anwendung autorisiert haben, sollten Sie sich an den Administrator wenden, um sicherzustellen, dass die Google-Authentifizierung richtig konfiguriert ist.)|  
|ValidationErrorAllowedTenantIsRequired|Allowed Tenant is required („Zulässiger Mandant“ ist erforderlich)|  
|ValidationErrorTenantIsNotValid|Der Azure Active Directory-Mandant „{0}“ ist ungültig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Anmelden mithilfe Ihres {0}-Kontos|  
|WebAuthenticationUserLimitNotice|This service has reached the maximum number of allowed users. (Für diesen Dienst wurde die maximale Anzahl von zulässigen Benutzern erreicht.) Please `<a href="mailto:{0}"\>contact the administrator</a\>` to upgrade their service and re-enable user registration. (Senden Sie eine E-Mail an `<a href="mailto:{0}"\>contact the administrator</a\>`, um den Dienst zu aktualisieren und die Benutzerregistrierung wieder zu aktivieren.)|  
|WebAuthenticationUserLimitNoticeHeader|User registration disabled (Benutzerregistrierung deaktiviert)|  
|WebAuthenticationUserLimitNoticeTitle|User registration disabled (Benutzerregistrierung deaktiviert)|  
|WebAuthenticationUserRegistrationDisabledNotice|Registration of users has been disabled by the administrator. (Die Registrierung der Benutzer wurde vom Administrator deaktiviert.) Please login with external identity provider. (Melden Sie sich mit dem externen Identitätsanbieter an.)|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|User registration disabled (Benutzerregistrierung deaktiviert)|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|User registration disabled (Benutzerregistrierung deaktiviert)|  
|WebAuthenticationSignupPendingConfirmationNotice|Before we can complete the creation of your account we need to verify your e-mail address. (Bevor wir die Erstellung Ihres Kontos abschließen können, müssen wir Ihre E-Mail-Adresse überprüfen.) Wir haben eine E-Mail an {0} gesendet. Please follow the instructions inside the e-mail to activate your account. (Befolgen Sie die Anweisungen in der E-Mail, um Ihr Konto zu aktivieren.) If the e-mail doesn’t arrive within the next few minutes, please check your junk email folder. (Überprüfen Sie Ihren Junk-E-Mail-Ordner, falls Sie die E-Mail nicht in den nächsten Minuten erhalten.)|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Wir haben ein unbestätigtes Konto für die E-Mail-Adresse {0} gefunden. To complete the creation of your account we need to verify your e-mail address. (Um die Erstellung Ihres Kontos abschließen zu können, müssen wir Ihre E-Mail-Adresse überprüfen.) Wir haben eine E-Mail an {0} gesendet. Please follow the instructions inside the e-mail to activate your account. (Befolgen Sie die Anweisungen in der E-Mail, um Ihr Konto zu aktivieren.) If the e-mail doesn’t arrive within the next few minutes, please check your junk email folder (Überprüfen Sie Ihren Junk-E-Mail-Ordner, falls Sie die E-Mail nicht in den nächsten Minuten erhalten.)|  
|WebAuthenticationSignupConfirmationAlmostDone|Almost Done (Fast fertig)|  
|WebAuthenticationSignupConfirmationEmailSent|Wir haben eine E-Mail an {0} gesendet. Please follow the instructions inside the e-mail to activate your account. (Befolgen Sie die Anweisungen in der E-Mail, um Ihr Konto zu aktivieren.) If the e-mail doesn’t arrive within the next few minutes, please check your junk email folder. (Überprüfen Sie Ihren Junk-E-Mail-Ordner, falls Sie die E-Mail nicht in den nächsten Minuten erhalten.)|  
|WebAuthenticationEmailSentNotificationMessage|E-Mail wurde an {0} gesendet.|  
|WebAuthenticationNoAadTenantConfigured|No Azure Active Directory tenant configured for the service. (Für den Dienst wurde kein Azure Active Directory-Mandant konfiguriert.)|  
|CheckboxLabelUserRegistrationTermsConsentRequired|I agree to the `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`. (Ich stimme den `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>` zu.)|  
|TextblockUserRegistrationTermsProvided|Please review `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>` (Lesen Sie die `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`.)|  
|DialogHeadingTermsOfUse|Terms of Use (Nutzungsbedingungen)|  
|ValidationMessageConsentNotAccepted|You must agree to the Terms of Use before you can proceed. (Sie müssen den Nutzungsbedingungen zustimmen, bevor Sie fortfahren können.)|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Name|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password? (Kennwort vergessen?)|  
|WebAuthenticationIfAdministrator|If you are an Administrator you must sign in `<a href="{0}"\>here</a\>`. (Wenn Sie Administrator sind, müssen Sie sich `<a href="{0}"\>here</a\>` anmelden.)|  
|WebAuthenticationNotAMember|Not a member yet? (Noch kein Mitglied?) `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Remember me on this computer (Kennwort auf diesem Computer speichern)|  
|WebAuthenticationSigininWithPassword|Sign in with your username and password (Melden Sie sich mit Ihrem Benutzernamen und Kennwort an)|  
|WebAuthenticationSigninTitle|Anmelden|  
|WebAuthenticationSignUpNow|Jetzt anmelden|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleSignup|Registrieren|  
|WebAuthenticationAlreadyAMember|Already a member? (Sind Sie bereits Mitglied?)|  
|WebAuthenticationCreateNewAccount|Create a new API Management account (Erstellen Sie ein neues API Management-Konto)|  
|WebAuthenticationSigninNow|Sign in now (Melden Sie sich jetzt an)|  
|ButtonLabelSignup|Registrieren|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Are you sure that you want to cancel this subscription? (Sind Sie sicher, dass Sie dieses Abonnement kündigen möchten?)|  
|SubscriptionRenewConfirmation|Are you sure that you want to renew this subscription? (Sind Sie sicher, dass Sie dieses Abonnement erneuern möchten?)|  
|WebDevelopersManageSubscriptions|Verwalten von Abonnements|  
|WebDevelopersPrimaryKey|Primary key (Primärschlüssel)|  
|WebDevelopersRegenerateLink|Regenerate (Neu generieren)|  
|WebDevelopersSecondaryKey|Secondary key (Sekundärschlüssel)|  
|ButtonLabelShowKey|Show (Anzeigen)|  
|ButtonLabelRenewSubscription|Erneuern|  
|WebDevelopersSubscriptionRequested|Angefordert am {0}|  
|WebDevelopersSubscriptionRequestedState|Requested (Angefordert)|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Zustand|  
|WebDevelopersUsageStatisticsLink|Analytics reports (Analytics-Berichte)|  
|WebDevelopersYourSubscriptions|Your subscriptions (Ihre Abonnements)|  
|SubscriptionPropertyLabelRequestedDate|Requested on (Angefordert am)|  
|SubscriptionPropertyLabelStartedDate|Started on (Gestartet am)|  
|PageTitleRenameSubscription|Rename subscription (Abonnement umbenennen)|  
|SubscriptionPropertyLabelName|Abonnementname|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Looking to close your account? (Möchten Sie Ihr Konto schließen?)|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Hide (Ausblenden)|  
|ButtonLabelRegenerateKey|Regenerate (Neu generieren)|  
|InformationMessageKeyWasRegenerated|Are you sure that you want to regenerate this key? (Sind Sie sicher, dass Sie diesen Schlüssel neu generieren möchten?)|  
|ButtonLabelShowKey|Sendung|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Update profile (Profil aktualisieren)|  
|PageTitleUpdateProfile|Update account information (Kontoinformationen aktualisieren)|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Change account information (Kontoinformationen ändern)|  
|ButtonLabelChangePassword|Kennwort ändern|  
|ButtonLabelCloseAccount|Close account (Konto schließen)|  
|TextboxLabelEmail|E-Mail|  
|TextboxLabelEmailFirstName|Vorname|  
|TextboxLabelEmailLastName|Nachname|  
|TextboxLabelNotificationsSenderEmail|Notifications sender email (E-Mail-Adresse Benachrichtigungsabsender)|  
|TextboxLabelOrganizationName|Organization name (Name der Organisation)|  
|SubscriptionStateActive|Aktiv|  
|SubscriptionStateCancelled|Abgebrochen|  
|SubscriptionStateExpired|Abgelaufen|  
|SubscriptionStateRejected|Rejected (Abgelehnt)|  
|SubscriptionStateRequested|Requested (Angefordert)|  
|SubscriptionStateSuspended|Ausgesetzt|  
|DefaultSubscriptionNameTemplate|{0}  (Standard)|  
|SubscriptionNameTemplate|Entwicklerzugriff #{0}|  
|TextboxLabelSubscriptionName|Abonnementname|  
|ValidationMessageSubscriptionNameRequired|Der Abonnementname darf nicht leer sein.|  
|ApiManagementUserLimitReached|This service has reached the maximum number of allowed users. (Für diesen Dienst wurde die maximale Anzahl von zulässigen Benutzern erreicht.) Please upgrade to a higher pricing tier. (Führen Sie ein Upgrade auf einen höheren Tarif durch.)|  
  
##  <a name="glyphs"></a> Glyphenressourcen  
 Für Vorlagen des API Management-Entwicklerportals können die Glyphen unter [Glyphicons from Bootstrap](https://getbootstrap.com/components/#glyphicons) (Glyphensymbole von Bootstrap) verwendet werden. Dieser Satz mit Glyphen enthält mehr als 250 Glyphen im Schriftartformat aus dem [Glyphicon](https://glyphicons.com/)-Halflings-Satz. Verwenden Sie folgende Syntax, um einen Glyphen aus diesem Satz zu verwenden.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Die vollständige Liste mit Glyphen finden Sie unter [Glyphicons from Bootstrap](https://getbootstrap.com/components/#glyphicons) (Glyphensymbole von Bootstrap).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).
