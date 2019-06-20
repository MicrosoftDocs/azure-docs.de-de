---
title: Datenmodellreferenz zu Azure API Management-Vorlagen | Microsoft-Dokumentation
description: Lernen Sie die Entitäts- und Typendarstellungen für gängige Elemente kennen, die in den Datenmodellen für die Entwicklerportalvorlagen in Azure API Management verwendet werden.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094521"
---
# <a name="azure-api-management-template-data-model-reference"></a>Datenmodellreferenz zu Azure API Management-Vorlagen
In diesem Thema werden die Entitäts- und Typendarstellungen für gängige Elemente beschrieben, die in den Datenmodellen für die Entwicklerportalvorlagen in Azure API Management verwendet werden.  
  
 Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

Das Entwicklerportal ist nicht im Tarif „Verbrauch“ verfügbar.

## <a name="reference"></a>Verweis

-   [API](#API)  
-   [API-Zusammenfassung](#APISummary)  
-   [Anwendung](#Application)  
-   [Anlage](#Attachment)  
-   [Codebeispiel](#Sample)  
-   [Kommentar](#Comment)  
-   [Filterung](#Filtering)  
-   [Kopfzeile](#Header)  
-   [HTTP-Anforderung](#HTTPRequest)  
-   [HTTP-Antwort](#HTTPResponse)  
-   [Problem](#Issue)  
-   [Vorgang](#Operation)  
-   [Vorgangsmenü](#Menu)  
-   [Vorgangsmenüelement](#MenuItem)  
-   [Seitenverwaltung](#Paging)  
-   [Parameter](#Parameter)  
-   [Produkt](#Product)  
-   [Anbieter](#Provider)  
-   [Darstellung](#Representation)  
-   [Abonnement](#Subscription)  
-   [Abonnementzusammenfassung](#SubscriptionSummary)  
-   [Benutzerkontoinformationen](#UserAccountInfo)  
-   [Benutzeranmeldung](#UseSignIn)  
-   [Benutzerregistrierung](#UserSignUp)  
  
##  <a name="API"></a> API  
 Die `API`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`id`|Zeichenfolge|Ressourcenbezeichner. Zur eindeutigen Identifizierung der API in der aktuellen Instanz des API Management-Diensts. Der Wert ist eine gültige relative URL im Format `apis/{id}`, wobei `{id}` einen API-Bezeichner darstellt. Diese Eigenschaft ist schreibgeschützt.|  
|`name`|Zeichenfolge|Der Name der API. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`description`|Zeichenfolge|Beschreibung der API. Darf nicht leer sein. Kann HTML-Formatierungstags umfassen. Die maximale Länge beträgt 1000 Zeichen.|  
|`serviceUrl`|Zeichenfolge|Absolute URL des Back-End-Diensts, der diese API implementiert.|  
|`path`|Zeichenfolge|Relative URL, die diese API und all ihre Ressourcenpfade in der Instanz des API Management-Diensts eindeutig identifiziert. Sie wird an die Basis-URL des API-Endpunkts angehängt, die während der Dienstinstanzerstellung angegeben wurde, um eine öffentliche URL für diese API zu erstellen.|  
|`protocols`|Zahlenarray|Beschreibt, über welche Protokolle die Vorgänge in dieser API aufgerufen werden können. Zulässige Werte sind `1 - http` und `2 - https` oder beide.|  
|`authenticationSettings`|[Authentifizierungseinstellungen für den Autorisierungsserver](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Die Sammlung von Authentifizierungseinstellungen, die in dieser API enthalten sind.|  
|`subscriptionKeyParameterNames`|object|Eine optionale Eigenschaft, mit der benutzerdefinierte Namen für Abfrage- und/oder Headerparameter angegeben werden können, in denen der Abonnementschlüssel enthalten ist. Wenn diese Eigenschaft vorhanden ist, muss sie mindestens eine der beiden folgenden Eigenschaften enthalten.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API-Zusammenfassung  
 Die `API summary`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`id`|Zeichenfolge|Ressourcenbezeichner. Zur eindeutigen Identifizierung der API in der aktuellen Instanz des API Management-Diensts. Der Wert ist eine gültige relative URL im Format `apis/{id}`, wobei `{id}` einen API-Bezeichner darstellt. Diese Eigenschaft ist schreibgeschützt.|  
|`name`|Zeichenfolge|Der Name der API. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`description`|Zeichenfolge|Beschreibung der API. Darf nicht leer sein. Kann HTML-Formatierungstags umfassen. Die maximale Länge beträgt 1000 Zeichen.|  
  
##  <a name="Application"></a> Anwendung  
 Die `application`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|Zeichenfolge|Der eindeutige Bezeichner der Anwendung.|  
|`Title`|Zeichenfolge|Der Titel der Anwendung.|  
|`Description`|Zeichenfolge|Die Beschreibung der Anwendung.|  
|`Url`|URI|Der URI für die Anwendung.|  
|`Version`|Zeichenfolge|Versionsinformationen für die Anwendung.|  
|`Requirements`|Zeichenfolge|Eine Beschreibung der Anforderungen für die Anwendung.|  
|`State`|number|Der aktuelle Status der Anwendung.<br /><br /> – 0 – registriert<br /><br /> – 1 – übermittelt<br /><br /> – 2 – veröffentlicht<br /><br /> – 3 – abgelehnt<br /><br /> – 4 – unveröffentlicht|  
|`RegistrationDate`|DateTime|Das Datum und die Uhrzeit der Anwendungsregistrierung.|  
|`CategoryId`|number|Die Kategorie der Anwendung (Finanzen, Unterhaltung usw.).|  
|`DeveloperId`|Zeichenfolge|Der eindeutige Bezeichner des Entwicklers, der die Anwendung übermittelt hat.|  
|`Attachments`|Sammlung von [Attachment](#Attachment)-Entitäten.|Anlagen für die Anwendung, z. B. Screenshots oder Symbole.|  
|`Icon`|[Anlage](#Attachment)|Das Symbol für die Anwendung.|  
  
##  <a name="Attachment"></a> Anlage  
 Die `attachment`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`UniqueId`|Zeichenfolge|Der eindeutige Bezeichner für die Anlage.|  
|`Url`|Zeichenfolge|Die URL der Ressource.|  
|`Type`|Zeichenfolge|Der Typ der Anlage.|  
|`ContentType`|Zeichenfolge|Der Medientyp der Anlage.|  
  
##  <a name="Sample"></a> Codebeispiel  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`title`|Zeichenfolge|Der Name des Vorgangs.|  
|`snippet`|Zeichenfolge|Diese Eigenschaft ist veraltet und sollte nicht verwendet werden.|  
|`brush`|Zeichenfolge|Die bei der Anzeige des Codebeispiels zu verwendende Vorlage für Syntaxfarben. Zulässige Werte sind `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` und `csharp`.|  
|`template`|Zeichenfolge|Der Name dieser Codebeispielvorlage.|  
|`body`|Zeichenfolge|Ein Platzhalter für den Codebeispielteil des Codeausschnitts.|  
|`method`|Zeichenfolge|Die HTTP-Methode des Vorgangs.|  
|`scheme`|Zeichenfolge|Das Protokoll, das für die Vorgangsanforderung verwendet werden soll.|  
|`path`|Zeichenfolge|Der Pfad des Vorgangs.|  
|`query`|Zeichenfolge|Beispiel für die Abfragezeichenfolge mit definierten Parametern.|  
|`host`|Zeichenfolge|Die URL des API Management-Dienstgateways für die API, die diesen Vorgang enthält.|  
|`headers`|Sammlung von                                  [Header](#Header)-Entitäten.|Die Header für diesen Vorgang.|  
|`parameters`|Sammlung von [Parameter](#Parameter)-Entitäten.|Parameter, die für diesen Vorgang definiert sind.|  
  
##  <a name="Comment"></a> Kommentar  
 Die `API`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|number|Die ID des Kommentars.|  
|`CommentText`|Zeichenfolge|Der Text des Kommentars. Kann HTML enthalten.|  
|`DeveloperCompany`|Zeichenfolge|Der Unternehmensname des Entwicklers.|  
|`PostedOn`|DateTime|Das Datum und Uhrzeit der Bereitstellung des Kommentars.|  
  
##  <a name="Issue"></a> Problem  
 Die `issue`-Entität weist die folgenden Eigenschaften auf.  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|Zeichenfolge|Der eindeutige Bezeichner für das Problem.|  
|`ApiID`|Zeichenfolge|Die ID für die API, für die dieses Problem gemeldet wurde.|  
|`Title`|Zeichenfolge|Der Titel des Problems.|  
|`Description`|Zeichenfolge|Die Beschreibung des Problems.|  
|`SubscriptionDeveloperName`|Zeichenfolge|Der Vorname des Entwicklers, der das Problem gemeldet hat.|  
|`IssueState`|Zeichenfolge|Der aktuelle Status des Problems. Mögliche Werte sind „Vorgeschlagen“, „Geöffnet“, „Geschlossen“.|  
|`ReportedOn`|DateTime|Das Datum und die Uhrzeit, zu der das Problem gemeldet wurde.|  
|`Comments`|Sammlung von [Comment](#Comment)-Entitäten.|Kommentare zu diesem Problem.|  
|`Attachments`|Sammlung von [Attachment](api-management-template-data-model-reference.md#Attachment)-Entitäten.|Eventuelle Anlagen zum Problem.|  
|`Services`|Sammlung von [API](#API)-Entitäten.|Die von dem Benutzer, der das Problem gemeldet hat, abonnierten APIs.|  
  
##  <a name="Filtering"></a> Filterung  
 Die `filtering`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Pattern`|Zeichenfolge|Der aktuelle Suchbegriff oder `null`, wenn kein Suchbegriff vorliegt.|  
|`Placeholder`|Zeichenfolge|Der Text, der im Suchfeld angezeigt werden soll, wenn kein Suchbegriff angegeben ist.|  
  
##  <a name="Header"></a> Header  
 In diesem Abschnitt wird die `parameter`-Darstellung beschrieben.  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|-----------------|----------|  
|`name`|Zeichenfolge|Parametername.|  
|`description`|Zeichenfolge|Beschreibung des Parameters.|  
|`value`|Zeichenfolge|Headerwert.|  
|`typeName`|Zeichenfolge|Der Datentyp des Headerwerts.|  
|`options`|Zeichenfolge|Optionen.|  
|`required`|boolean|Gibt an, ob der Header erforderlich ist.|  
|`readOnly`|boolean|Gibt an, ob der Header schreibgeschützt ist.|  
  
##  <a name="HTTPRequest"></a> HTTP-Anforderung  
 In diesem Abschnitt wird die `request`-Darstellung beschrieben.  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`description`|Zeichenfolge|Vorgangsanforderungsbeschreibung.|  
|`headers`|Array von [Header](#Header)-Entitäten.|Anforderungsheader.|  
|`parameters`|Array von [Parametern](#Parameter)|Die Sammlung von Vorgangsanforderungsparametern.|  
|`representations`|Array von [Darstellungen](#Representation)|Die Sammlung von Vorgangsanforderungsdarstellungen.|  
  
##  <a name="HTTPResponse"></a> HTTP-Antwort  
 In diesem Abschnitt wird die `response`-Darstellung beschrieben.  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`statusCode`|Positive ganze Zahl|Statuscode der Vorgangsantwort.|  
|`description`|Zeichenfolge|Vorgangsantwortbeschreibung.|  
|`representations`|Array von [Darstellungen](#Representation)|Die Sammlung von Vorgangsantwortdarstellungen.|  
  
##  <a name="Operation"></a> Vorgang  
 Die `operation`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`id`|Zeichenfolge|Ressourcenbezeichner. Zur eindeutigen Identifizierung des Vorgangs in der aktuellen Instanz des API Management-Diensts. Der Wert ist eine gültige relative URL im Format `apis/{aid}/operations/{id}`, wobei `{aid}` einen API-Bezeichner und `{id}` einen Vorgangsbezeichner darstellt. Diese Eigenschaft ist schreibgeschützt.|  
|`name`|Zeichenfolge|Name des Vorgangs. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`description`|Zeichenfolge|Beschreibung des Vorgangs. Darf nicht leer sein. Kann HTML-Formatierungstags umfassen. Die maximale Länge beträgt 1000 Zeichen.|  
|`scheme`|Zeichenfolge|Beschreibt, über welche Protokolle die Vorgänge in dieser API aufgerufen werden können. Zulässige Werte sind `http`, `https` oder sowohl `http` als auch `https`.|  
|`uriTemplate`|Zeichenfolge|Relative URL-Vorlage, die die Zielressource für diesen Vorgang identifiziert. Kann Parameter enthalten. Beispiel: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|Zeichenfolge|Die URL des API Management-Gateways, der die API hostet.|  
|`httpMethod`|Zeichenfolge|Vorgangs-HTTP-Methode.|  
|`request`|[HTTP-Anforderung](#HTTPRequest)|Eine Entität, die Anforderungsdetails enthält.|  
|`responses`|Array von [HTTP-Antworten](#HTTPResponse)|Array von [HTTP Response](#HTTPResponse)-Entitäten des Vorgangs.|  
  
##  <a name="Menu"></a> Vorgangsmenü  
 Die `operation menu`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`ApiId`|Zeichenfolge|Die ID der aktuellen API.|  
|`CurrentOperationId`|Zeichenfolge|Die ID des aktuellen Vorgangs.|  
|`Action`|Zeichenfolge|Der Menütyp.|  
|`MenuItems`|Sammlung von [Operation menu item](#MenuItem)-Entitäten.|Die Vorgänge für die aktuelle API.|  
  
##  <a name="MenuItem"></a> Vorgangsmenüelement  
 Die `operation menu item`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|Zeichenfolge|Die ID des Vorgangs.|  
|`Title`|Zeichenfolge|Die Beschreibung des Vorgangs.|  
|`HttpMethod`|Zeichenfolge|Die HTTP-Methode des Vorgangs.|  
  
##  <a name="Paging"></a> Seitenverwaltung  
 Die `paging`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Page`|number|Die aktuelle Seitenzahl.|  
|`PageSize`|number|Die maximale Anzahl von Ergebnissen, die auf einer einzelnen Seite angezeigt werden.|  
|`TotalItemCount`|number|Die Anzahl der anzuzeigenden Elemente.|  
|`ShowAll`|boolean|Gibt an, ob alle Ergebnisse auf einer einzigen Seite angezeigt werden sollen.|  
|`PageCount`|number|Die Anzahl von Ergebnisseiten.|  
  
##  <a name="Parameter"></a> Parameter  
 In diesem Abschnitt wird die `parameter`-Darstellung beschrieben.  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|-----------------|----------|  
|`name`|Zeichenfolge|Parametername.|  
|`description`|Zeichenfolge|Beschreibung des Parameters.|  
|`value`|Zeichenfolge|Parameterwert.|  
|`options`|Array von Zeichenfolgen|Für Abfrageparameterwerte definierte Werte.|  
|`required`|boolean|Gibt an, ob der Parameter erforderlich ist oder nicht.|  
|`kind`|number|Gibt an, ob dieser Parameter ein path-Parameter (1) oder ein querystring-Parameter (2) ist.|  
|`typeName`|Zeichenfolge|Der Parametertyp.|  
  
##  <a name="Product"></a> Produkt  
 Die `product`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|Zeichenfolge|Ressourcenbezeichner. Zur eindeutigen Identifizierung des Produkts in der aktuellen Instanz des API Management-Diensts. Der Wert ist eine gültige relative URL im Format `products/{pid}`, wobei `{pid}` einen Produktbezeichner darstellt. Diese Eigenschaft ist schreibgeschützt.|  
|`Title`|Zeichenfolge|Der Name des Produkts. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`Description`|Zeichenfolge|Beschreibung des Produkts. Darf nicht leer sein. Kann HTML-Formatierungstags umfassen. Die maximale Länge beträgt 1000 Zeichen.|  
|`Terms`|Zeichenfolge|Nutzungsbedingungen für das Produkt. Diese Bedingungen werden Entwicklern angezeigt, die das Produkt abonnieren möchten. Sie müssen die Bedingungen akzeptieren, um den Abonnementvorgang abschließen zu können.|  
|`ProductState`|number|Gibt an, ob das Produkt veröffentlicht wird. Veröffentlichte Produkte sind für Entwickler im Entwicklerportal sichtbar. Nicht veröffentlichte Produkte sind nur für Administratoren sichtbar.<br /><br /> Folgende Werte sind für den Produktstatus zulässig:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Gibt an, ob ein Benutzer zur gleichen Zeit mehrere Abonnements für dieses Produkt besitzen kann.|  
|`MultipleSubscriptionsCount`|number|Die maximale Anzahl von Abonnements für dieses Produkt, die ein Benutzer gleichzeitig haben darf.|  
  
##  <a name="Provider"></a> Anbieter  
 Die `provider`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Properties`|Zeichenfolgenwörterbuch|Eigenschaften für diesen Authentifizierungsanbieter.|  
|`AuthenticationType`|Zeichenfolge|Der Anbietertyp. (Azure Active Directory, Facebook-Anmeldung, Google-Konto, Microsoft-Konto, Twitter).|  
|`Caption`|Zeichenfolge|Der Anzeigename des Anbieters.|  
  
##  <a name="Representation"></a> Darstellung  
 In diesem Abschnitt wird eine `representation` beschrieben.  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`contentType`|Zeichenfolge|Gibt einen registrierten oder benutzerdefinierten Inhaltstyp für diese Darstellung an, z.B. `application/xml`.|  
|`sample`|Zeichenfolge|Ein Beispiel für die Darstellung.|  
  
##  <a name="Subscription"></a> Abonnement  
 Die `subscription`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|Zeichenfolge|Ressourcenbezeichner. Zur eindeutigen Identifizierung des Abonnements in der aktuellen Instanz des API Management-Diensts. Der Wert ist eine gültige relative URL im Format `subscriptions/{sid}`, wobei `{sid}` einen Abonnementbezeichner darstellt. Diese Eigenschaft ist schreibgeschützt.|  
|`ProductId`|Zeichenfolge|Der Produktressourcenbezeichner des abonnierten Produkts. Der Wert ist eine gültige relative URL im Format `products/{pid}`, wobei `{pid}` einen Produktbezeichner darstellt.|  
|`ProductTitle`|Zeichenfolge|Der Name des Produkts. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`ProductDescription`|Zeichenfolge|Beschreibung des Produkts. Darf nicht leer sein. Kann HTML-Formatierungstags umfassen. Die maximale Länge beträgt 1000 Zeichen.|  
|`ProductDetailsUrl`|Zeichenfolge|Relative URL zu den Produktdetails.|  
|`state`|Zeichenfolge|Der Status des Abonnements. Mögliche Statusangaben:<br /><br /> - `0 - suspended`: Das Abonnement ist blockiert, und der Abonnent kann keine APIs des Produkts aufrufen.<br /><br /> - `1 - active`: Das Abonnement ist aktiv.<br /><br /> - `2 - expired`: Das Abonnement hat das Ablaufdatum erreicht und wurde deaktiviert.<br /><br /> - `3 - submitted`: Die Abonnementanforderung wurde vom Entwickler gesendet, aber noch nicht genehmigt oder abgelehnt.<br /><br /> - `4 - rejected`: Die Abonnementanforderung wurde von einem Administrator abgelehnt.<br /><br /> - `5 - cancelled`: Das Abonnement wurde vom Entwickler oder Administrator abgebrochen.|  
|`DisplayName`|Zeichenfolge|Der Anzeigename des Abonnements.|  
|`CreatedDate`|dateTime|Das Datum, an dem das Abonnement erstellt wurde, im ISO 8601-Format: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Gibt an, ob das Abonnement vom aktuellen Benutzer gekündigt werden kann.|  
|`IsAwaitingApproval`|boolean|Gibt an, ob das Abonnement zur Genehmigung vorliegt.|  
|`StartDate`|dateTime|Das Startdatum für das Abonnement im ISO 8601-Format: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|dateTime|Das Ablaufdatum für das Abonnement im ISO 8601-Format: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|dateTime|Das Benachrichtigungsdatum für das Abonnement im ISO 8601-Format: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|Zeichenfolge|Der primäre Abonnementschlüssel. Die maximale Länge beträgt 256 Zeichen.|  
|`secondaryKey`|Zeichenfolge|Der sekundäre Abonnementschlüssel. Die maximale Länge beträgt 256 Zeichen.|  
|`CanBeRenewed`|boolean|Gibt an, ob das Abonnement vom aktuellen Benutzer verlängert werden kann.|  
|`HasExpired`|boolean|Gibt an, ob das Abonnement abgelaufen ist.|  
|`IsRejected`|boolean|Gibt an, ob die Abonnementanforderung abgelehnt wurde.|  
|`CancelUrl`|Zeichenfolge|Die relative URL zum Kündigen des Abonnements.|  
|`RenewUrl`|Zeichenfolge|Die relative URL zum Verlängern des Abonnements.|  
  
##  <a name="SubscriptionSummary"></a> Abonnementzusammenfassung  
 Die `subscription summary`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Id`|Zeichenfolge|Ressourcenbezeichner. Zur eindeutigen Identifizierung des Abonnements in der aktuellen Instanz des API Management-Diensts. Der Wert ist eine gültige relative URL im Format `subscriptions/{sid}`, wobei `{sid}` einen Abonnementbezeichner darstellt. Diese Eigenschaft ist schreibgeschützt.|  
|`DisplayName`|Zeichenfolge|Der Anzeigename des Abonnements.|  
  
##  <a name="UserAccountInfo"></a> Benutzerkontoinformationen  
 Die `user account info`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`FirstName`|Zeichenfolge|Vorname. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`LastName`|Zeichenfolge|Nachname. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`Email`|Zeichenfolge|E-Mail-Adresse. Darf nicht leer sein und muss innerhalb der Dienstinstanz eindeutig sein. Die maximale Länge beträgt 254 Zeichen.|  
|`Password`|Zeichenfolge|Kennwort des Benutzerkontos.|  
|`NameIdentifier`|Zeichenfolge|Kontobezeichner, identisch mit der E-Mail-Adresse des Benutzers.|  
|`ProviderName`|Zeichenfolge|Name des Authentifizierungsanbieters.|  
|`IsBasicAccount`|boolean|TRUE, wenn dieses Konto mithilfe von E-Mail-Adresse und Kennwort registriert wurde. FALSE, wenn das Konto mithilfe eines Anbieters registriert wurde.|  
  
##  <a name="UseSignIn"></a> Benutzeranmeldung  
 Die `user sign in`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`Email`|Zeichenfolge|E-Mail-Adresse. Darf nicht leer sein und muss innerhalb der Dienstinstanz eindeutig sein. Die maximale Länge beträgt 254 Zeichen.|  
|`Password`|Zeichenfolge|Kennwort des Benutzerkontos.|  
|`ReturnUrl`|Zeichenfolge|Die URL der Seite, auf der der Benutzer auf „Anmelden“ geklickt hat.|  
|`RememberMe`|boolean|Gibt an, ob die Informationen des aktuellen Benutzers gespeichert werden sollen.|  
|`RegistrationEnabled`|boolean|Gibt an, ob die Registrierung aktiviert ist.|  
|`DelegationEnabled`|boolean|Gibt an, ob die delegierte Anmeldung aktiviert ist.|  
|`DelegationUrl`|Zeichenfolge|Die URL zur delegierten Anmeldung, falls diese aktiviert ist.|  
|`SsoSignUpUrl`|Zeichenfolge|Die URL zur einmaligen Anmeldung für den Benutzer, sofern vorhanden.|  
|`AuxServiceUrl`|Zeichenfolge|Wenn der aktuelle Benutzer ein Administrator ist, ist dies ein Link zur Dienstinstanz im Azure-Portal.|  
|`Providers`|Sammlung von [Provider](#Provider)-Entitäten.|Die Authentifizierungsanbieter für diesen Benutzer.|  
|`UserRegistrationTerms`|Zeichenfolge|Bedingungen, denen ein Benutzer zustimmen muss, bevor er sich anmeldet.|  
|`UserRegistrationTermsEnabled`|boolean|Gibt an, ob Bedingungen aktiviert sind.|  
  
##  <a name="UserSignUp"></a> Benutzerregistrierung  
 Die `user sign up`-Entität weist die folgenden Eigenschaften auf:  
  
|Eigenschaft|Typ|BESCHREIBUNG|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Der vom [Anmeldesteuerelement](api-management-page-controls.md#sign-up) verwendete Wert.|  
|`Password`|Zeichenfolge|Kennwort des Benutzerkontos.|  
|`PasswordVerdictLevel`|number|Der vom [Anmeldesteuerelement](api-management-page-controls.md#sign-up) verwendete Wert.|  
|`UserRegistrationTerms`|Zeichenfolge|Bedingungen, denen ein Benutzer zustimmen muss, bevor er sich anmeldet.|  
|`UserRegistrationTermsOptions`|number|Der vom [Anmeldesteuerelement](api-management-page-controls.md#sign-up) verwendete Wert.|  
|`ConsentAccepted`|boolean|Der vom [Anmeldesteuerelement](api-management-page-controls.md#sign-up) verwendete Wert.|  
|`Email`|Zeichenfolge|E-Mail-Adresse. Darf nicht leer sein und muss innerhalb der Dienstinstanz eindeutig sein. Die maximale Länge beträgt 254 Zeichen.|  
|`FirstName`|Zeichenfolge|Vorname. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`LastName`|Zeichenfolge|Nachname. Darf nicht leer sein. Die maximale Länge beträgt 100 Zeichen.|  
|`UserData`|Zeichenfolge|Der vom [Anmeldesteuerelement](api-management-page-controls.md#sign-up) verwendete Wert.|  
|`NameIdentifier`|Zeichenfolge|Der vom [Anmeldesteuerelement](api-management-page-controls.md#sign-up) verwendete Wert.|  
|`ProviderName`|Zeichenfolge|Name des Authentifizierungsanbieters.|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).
