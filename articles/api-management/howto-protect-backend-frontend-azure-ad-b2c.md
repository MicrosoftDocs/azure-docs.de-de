---
title: Schützen des SPA-Back-Ends mit OAuth 2.0 mithilfe von Azure Active Directory B2C und Azure API Management
description: Schützen Sie eine API mit OAuth 2.0 mithilfe von Azure Active Directory B2C, Azure API Management und EasyAuth, um Aufrufe von einer JavaScript-SPA zu ermöglichen.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new, devx-track-js
ms.openlocfilehash: aa3bce73d2a91538dff0fdeb9e0eb814d878459a
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676024"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Schützen des SPA-Back-Ends mit OAuth 2.0, Azure Active Directory B2C und Azure API Management

In diesem Szenario wird gezeigt, wie Sie Ihre Azure API Management-Instanz zum Schutz einer API konfigurieren.
Sie verwenden dabei das OAuth 2.0-Protokoll zusammen mit Azure AD B2C und API Management, um ein Azure Functions-Back-End mithilfe von EasyAuth zu schützen.

## <a name="aims"></a>Ziele
Erfahren Sie, wie Sie API Management in einem vereinfachten Szenario mit Azure Functions und Azure AD B2C verwenden können. Sie erstellen eine JavaScript-App (JS), die eine API aufruft, die Benutzer mit Azure AD B2C anmeldet. Anschließend verwenden Sie die Funktionen der API Management-Richtlinie validate-jwt, um die Back-End-API zu schützen.

Zur umfassenden Verteidigung verwenden Sie EasyAuth, um das Token in der Back-End-API erneut zu validieren.

## <a name="prerequisites"></a>Voraussetzungen
Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:
* Ein Azure Storage-Konto (StorageV2) vom Typ „Universell v2“ zum Hosten der Front-End-JS-SPA (Single-Page-Webanwendung)
* Eine Azure API Management-Instanz 
* Eine leere Azure-Funktions-App (mit der .NET Core-Runtime V2 in einem Windows-Verbrauchstarif) zum Hosten der aufgerufenen API
* Einen Azure AD B2C-Mandanten, der mit einem Abonnement verknüpft ist 

Obwohl Sie in der Praxis Ressourcen in derselben Region in Produktionsworkloads verwenden, ist die Bereitstellungsregion in diesem Artikel nicht wichtig.

## <a name="overview"></a>Übersicht
Die folgende Abbildung zeigt die verwendeten Komponenten und den Flow zwischen den Komponenten, nachdem dieser Prozess abgeschlossen ist.
![Verwendete Komponenten und Flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Verwendete Komponenten und Flow")

Es folgt eine kurze Übersicht über die Schritte:

1. Erstellen des Azure AD B2C-Aufrufs (Front-End, API Management) und der API-Anwendungen mit Bereichen sowie Gewähren des API-Zugriffs
1. Erstellen der Registrierungs- oder Anmelderichtlinien, damit sich Benutzer mit Azure AD B2C anmelden können 
1. Konfigurieren von API Management mit den neuen Azure AD B2C-Client-IDs und -schlüsseln zum Aktivieren der OAuth2-Benutzerautorisierung in der Entwicklerkonsole
1. Erstellen der Funktions-API
1. Konfigurieren der Funktions-API zum Aktivieren von EasyAuth mit den neuen Azure AD B2C-Client-IDs und -schlüsseln und Sperren der APIM-VIP 
1. Erstellen der API-Definition in API Management
1. Einrichten von Oauth2 für die API Management-API-Konfiguration
1. Einrichten der **CORS** -Richtlinie und Hinzufügen der **validate-jwt** -Richtlinie zum Validieren des OAuth-Tokens für jede eingehende Anforderung
1. Erstellen der aufrufenden Anwendung für die Nutzung der API
1. Hochladen der JS-Beispiel-SPA
1. Konfigurieren der JS-Client-Beispiel-App mit den neuen Azure AD B2C-Client-IDs und -schlüsseln 
1. Testen der Clientanwendung

## <a name="configure-azure-ad-b2c"></a>Configure Azure AD B2C 
Öffnen Sie das Blatt „Azure AD B2C“ im Portal, und führen Sie die folgenden Schritte aus.
1. Wählen Sie die Registerkarte **Anwendungen** aus. 
1. Klicken auf die Schaltfläche „Hinzufügen“ und Erstellen von drei Anwendungen für folgende Zwecke
   * Front-End-Client
   * Back-End-Funktions-API
   * (Optional:) Entwicklerportal für API Management (sofern Sie Azure API Management im Verbrauchstarif ausführen – weitere Informationen zu diesem Szenario finden Sie weiter unten)
1. Legen Sie die Web-App-/Web-API für alle drei Anwendungen fest, und legen Sie „Impliziten Ablauf zulassen“ nur für den Front-End-Client auf „Ja“ fest.
1. Legen Sie nun den App-ID-URI fest, und wählen Sie eine eindeutige und relevante Funktion für den erstellten Dienst aus.
1. Verwenden Sie zunächst Platzhalter für die Antwort-URLs (z. B. https://localhost ). Diese URLs werden später aktualisiert.
1. Klicken Sie auf „Erstellen“, und wiederholen Sie die Schritte 2–5 für jede der drei oben genannten Apps. Notieren Sie App-ID-URI, Namen und Anwendungs-ID von allen drei Apps für die spätere Verwendung.
1. Öffnen Sie über die Anwendungsliste das Entwicklerportal für API Management, und wählen Sie unter „Allgemein“ die Registerkarte *Schlüssel* aus. Klicken Sie anschließend auf „Schlüssel generieren“, um einen Authentifizierungsschlüssel zu generieren.
1. Bevor Sie auf „Speichern“ klicken, notieren Sie den Schlüssel für die spätere Verwendung. Beachten Sie, dass dies die einzige Stelle ist, an der dieser Schlüssel angezeigt wird und kopiert werden kann.
1. Wählen Sie nun die Registerkarte *Veröffentlichte Bereiche* (unter „API-Zugriff“) aus.
1. Erstellen und benennen Sie einen Bereich für Ihre Funktions-API, notieren Sie diesen sowie den ausgefüllten Wert „Vollständiger Bereich“, und klicken Sie auf „Speichern“.
   > [!NOTE]
   > Azure AD B2C-Bereiche sind letztlich Berechtigungen innerhalb Ihrer API, auf die andere Anwendungen Zugriff über das Blatt „API-Zugriff“ der Anwendungen anfordern können. Sie haben damit praktisch Anwendungsberechtigungen für Ihre aufgerufene API erstellt.
1. Öffnen Sie die anderen beiden Anwendungen, und zeigen Sie dann die Registerkarte *API-Zugriff* an.
1. Gewähren Sie ihnen Zugriff auf den Back-End-API-Bereich und den Standardbereich, der bereits vorhanden ist („Zugriff auf das Profil des Benutzers“).
1. Generieren Sie einen Schlüssel für diese, indem Sie die Registerkarte *Schlüssel* unter „Allgemein“ auswählen, um einen Authentifizierungsschlüssel zu generieren. Notieren Sie diese Schlüssel für die spätere Verwendung.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Erstellen eines Benutzerflows „Registrieren oder anmelden“
1. Wechseln Sie zum Stammverzeichnis (oder „Übersicht“) auf dem Blatt „Azure AD B2C“ zurück. 
1. Wählen Sie dann „Benutzerflows (Richtlinien)“ aus, und klicken Sie auf „Neuer Benutzerflow“.
1. Auswählen des Benutzerflowtyps „Registrieren und Anmelden“
1. Benennen Sie die Richtlinie, und notieren Sie den Namen für die spätere Verwendung.
1. Aktivieren Sie dann unter „Identitätsanbieter“ die Option „Benutzer-ID-Registrierung“ (kann ggf. auch „E-Mail-Registrierung“ heißen), und klicken Sie auf „OK“. 
1. Klicken Sie unter „Benutzerattribute und -ansprüche“ auf „Mehr anzeigen“, und wählen Sie die Anspruchsoptionen aus, die von den Benutzern eingegeben und im Token zurückgegeben werden sollen. Aktivieren Sie für die Erfassung und Rückgabe mindestens „Anzeigename“ und „E-Mail-Adresse“, und klicken Sie auf „OK“ und dann auf „Erstellen“.
1. Wählen Sie in der Liste die erstellte Richtlinie aus, und klicken Sie auf die Schaltfläche „Benutzerflow ausführen“.
1. Mit dieser Aktion wird das Blatt „Benutzerflow ausführen“ geöffnet. Wählen Sie die Front-End-Anwendung aus, und notieren Sie die Adresse der Domäne b2clogin.com, die in der Dropdownliste für „Domäne auswählen“ angezeigt wird.
1. Klicken Sie oben auf den Link, um den bekannten OpenID-Konfigurationsendpunkt zu öffnen, und notieren Sie die Werte für authorization_endpoint und token_endpoint sowie den Wert des Links selbst als bekannten OpenID-Konfigurationsendpunkt.

   > [!NOTE]
   > B2C-Richtlinien ermöglichen Ihnen, die Azure AD B2C-Anmeldeendpunkte verfügbar zu machen, um unterschiedliche Datenkomponenten zu erfassen und Benutzer auf unterschiedliche Weise anzumelden. In diesem Fall haben Sie einen Registrierungs- oder Anmeldeendpunkt konfiguriert, der einen bekannten Konfigurationsendpunkt verfügbar macht. Dabei wurde insbesondere Ihre erstellte Richtlinie durch den Parameter „p=“ in der URL angegeben.
   > 
   > Nachdem dies erledigt ist, verfügen Sie nun über eine funktionsfähige B2C-Identitätsplattform, mit der Benutzer in mehreren Anwendungen angemeldet werden können. 
   > Sie können hier auf die Schaltfläche „Benutzerflow ausführen“ klicken (für den Registrierungs- oder Anmeldungsprozess), um ein Gefühl dafür zu erhalten, was in der Praxis passiert. Der Umleitungsschritt am Ende führt jedoch zu einem Fehler, da die App noch nicht bereitgestellt wurde.

## <a name="build-the-function-api"></a>Erstellen der Funktions-API
1. Kehren Sie im Azure-Portal zu Ihrem Azure AD-Standardmandanten zurück, um die Elemente in Ihrem Abonnement erneut konfigurieren zu können. 
1. Wechseln Sie im Azure-Portal zum Blatt „Funktions-Apps“, öffnen Sie die leere Funktions-App, und erstellen Sie dann anhand des Schnellstarts im Portal eine neue Funktion „Webhook und API“.
1. Fügen Sie den Beispielcode von unten in der Datei „Run.csx“ über dem angezeigten vorhandenen Code ein.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > Der Code der C#-Skriptfunktion, den Sie soeben eingefügt haben, protokolliert lediglich eine Zeile in den Funktionsprotokollen und gibt den Text „Hello World“ mit einigen dynamischen Daten (Datum und Uhrzeit) zurück.

3. Wählen Sie auf dem linken Blatt „Integrieren“ und dann rechts oben im Bereich „Erweiterter Editor“ aus.
4. Fügen Sie den Beispielcode unter dem vorhandenen JSON-Code ein.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Wechseln Sie zurück zur Registerkarte „HttpTrigger1“, klicken Sie auf „Funktions-URL abrufen“, und kopieren Sie die angezeigte URL.

   > [!NOTE]
   > Die Bindungen, die Sie soeben erstellt haben, weisen Functions nur an, auf anonyme HTTP GET-Anforderungen an die kopierte URL zu antworten. (`https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey`) Sie verfügen damit über eine skalierbare, serverlose HTTPS-API, die eine sehr einfache Nutzlast zurückgeben kann.
   > Sie können den Aufruf dieser API nun über einen Webbrowser mithilfe der obigen URL testen. Sie können auch den Teil „?code=secret“ aus der URL entfernen, um zu bestätigen, dass Azure Functions einen 401-Fehler zurückgibt.

## <a name="configure-and-secure-the-function-api"></a>Konfigurieren und Schützen der Funktions-API
1. Es müssen zwei weitere Bereiche in der Funktions-App konfiguriert werden (Authentifizierung und Netzwerkeinschränkungen).
1. Konfigurieren Sie zunächst die Authentifizierung/Autorisierung. Klicken Sie dazu auf den Namen der Funktions-App (neben dem Funktionssymbol &lt;Z&gt;), um die Übersicht anzuzeigen.
1. Wählen Sie die Registerkarte „Plattformfeatures“ und dann „Authentifizierung/Autorisierung“ aus.
1. Aktivieren Sie die Funktion „App Service-Authentifizierung“.
1. Wählen Sie unter „Authentifizierungsanbieter“ die Option „Azure Active Directory“ und dann für den Schalter „Verwaltungsmodus“ die Option „Erweitert“ aus.
1. Fügen Sie die Anwendungs-ID der Back-End-Funktions-API (aus Azure AD B2C) in das Feld „Client-ID“ ein.
1. Fügen Sie den bekannten OpenID-Konfigurationsendpunkt aus der Registrierungs- oder Anmelderichtlinie in das Feld „Aussteller-URL“ ein (Sie haben diese Konfiguration zuvor notiert).
1. Wählen Sie „OK“ aus.
1. Legen Sie das Dropdownfeld „Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist.“ auf „Mit Azure Active Directory anmelden“ fest, und klicken Sie anschließend auf „Speichern“.

   > [!NOTE]
   > Ihre Funktions-API ist nun bereitgestellt. Bei Anforderungen ohne korrekten Schlüssel sollten Antworten vom Typ 401 ausgelöst und bei gültigen Anforderungen Daten zurückgegeben werden.
   > Sie haben die Sicherheit in EasyAuth durch zusätzliche Defense-in-Depth-Maßnahmen verbessert, indem Sie für nicht authentifizierte Anforderungen die Option „Mit Azure AD anmelden“ konfiguriert haben. Beachten Sie, dass sich dadurch das Verhalten bei nicht autorisierten Anforderungen zwischen der Back-End-Funktions-App und der Front-End-SPA ändert, da EasyAuth anstelle einer Antwort vom Typ „401 – Nicht autorisiert“ eine 302-Umleitung zu AAD veranlasst, was später noch mithilfe von API Management korrigiert wird.
   > Sie verfügen damit allerdings immer noch nicht über IP-Sicherheit. Mit einem gültigen Schlüssel und OAuth2-Token sind Aufrufe von überall aus möglich. Im Idealfall sollten jedoch alle Anforderungen über API Management erzwungen werden.
   > Bei Verwendung des Verbrauchstarifs für API Management kann diese Sperrung nicht auf der Grundlage der VIP realisiert werden, da es für diesen Tarif keine dedizierte statische IP-Adresse gibt. In diesem Fall müssen Sie zum Sperren Ihrer API-Aufrufe den gemeinsamen geheimen Funktionsschlüssel verwenden, sodass die Schritte 11–13 nicht möglich sind.

1. Schließen Sie das Blatt „Authentifizierung/Autorisierung“. 
1. Wählen Sie „Netzwerk“ und dann „Zugriffseinschränkungen“ aus.
1. Sperren Sie als Nächstes die zugelassenen IP-Adressen der Funktions-App für die VIP der API Management-Instanz. Diese virtuelle IP-Adresse wird im Portal im Abschnitt „Übersicht“ von API Management angezeigt.
1. Wenn Sie weiterhin mit dem Functions-Portal interagieren und die unten aufgeführten optionalen Schritte ausführen möchten, sollten Sie auch hier eine eigene öffentliche IP-Adresse oder einen CIDR-Bereich hinzufügen.
1. Nachdem ein Zulassungseintrag in der Liste vorhanden ist, fügt Azure eine implizite Ablehnungsregel hinzu, um alle anderen Adressen zu blockieren. 

Sie müssen dem Bereich mit IP-Einschränkungen CIDR-formatierte Adressblöcke hinzufügen. Wenn Sie eine einzelne Adresse (beispielsweise die VIP von API Management) hinzufügen möchten, verwenden Sie das Format xx.xx.xx.xx.

   > [!NOTE]
   > Ihre Funktions-API sollte nun nur noch von API Management oder über Ihre Adresse aufgerufen werden können.
   
## <a name="import-the-function-app-definition"></a>Importieren der Funktions-App-Definition
1. Öffnen Sie das Blatt *API Management* und anschließend *Ihre Instanz* .
1. Wählen Sie im Bereich „API Management“ Ihrer Instanz das Blatt „APIs“ aus.
1. Wählen Sie im Bereich „Neue API hinzufügen“ die Option „Funktions-App“ und dann oben im Popupfenster „Vollständig“ aus.
1. Klicken Sie auf „Durchsuchen“, wählen Sie die Funktions-App aus, in der Sie die API hosten, und klicken Sie auf „Auswählen“.
1. Weisen Sie der API einen Namen und eine Beschreibung für die interne Verwendung durch API Management zu, und fügen Sie sie dem Produkt „Unbegrenzt“ hinzu.
1. Notieren Sie sich die Basis-URL zur späteren Verwendung, und klicken Sie dann auf „Erstellen“.

## <a name="configure-oauth2-for-api-management"></a>Konfigurieren von Oauth2 für API Management

1. Wählen Sie anschließend auf der Registerkarte „Sicherheit“ das Blatt „OAuth 2.0“ aus, und klicken Sie auf „Hinzufügen“.
1. Geben Sie Werte für *Anzeigename* und *Beschreibung* für den hinzugefügten OAuth-Endpunkt an. (Diese Werte werden im nächsten Schritt als Oauth2-Endpunkt angezeigt.)
1. Sie können einen beliebigen Wert für die URL der Clientregistrierungsseite eingeben, da dieser Wert nicht verwendet wird.
1. Aktivieren Sie den Gewährungstyp *Implizite Authentifizierung* , und lassen Sie den Gewährungstyp „Autorisierungscode“ aktiviert.
1. Wechseln Sie zu den Feldern für *Autorisierungs-* und *Tokenendpunkt* , und geben Sie die Werte ein, die Sie zuvor im XML-Dokument der bekannten Konfiguration notiert haben.
1. Scrollen Sie nach unten, und geben Sie einen *zusätzlichen Textparameter* mit der Bezeichnung „resource“ und der Client-ID der Back-End-Funktions-API aus der Azure AD B2C-App-Registrierung an.
1. Wählen Sie „Clientanmeldeinformationen“ aus, und legen Sie die Client-ID auf die App-ID der Entwicklerkonsolen-App fest. Überspringen Sie diesen Schritt, wenn Sie das Verbrauchsmodell von API Management verwenden.
1. Legen Sie den geheimen Clientschlüssel auf den zuvor notierten Schlüssel fest. Überspringen Sie diesen Schritt, wenn Sie das Verbrauchsmodell von API Management verwenden.
1. Notieren Sie sich abschließend den redirect_uri der Autorisierungscodegewährung von API Management für die spätere Verwendung.

## <a name="set-up-oauth2-for-your-api"></a>Einrichten von Oauth2 für Ihre API
1. Ihre API wird auf der linken Seite des Portals im Abschnitt „Alle APIs“ angezeigt. Öffnen Sie die API, indem Sie darauf klicken.
1. Wählen Sie die Registerkarte „Einstellungen“ aus.
1. Aktualisieren Sie Ihre Einstellungen, indem Sie im Optionsfeld zur Benutzerautorisierung die Option „OAuth 2.0“ auswählen.
1. Wählen Sie den OAuth-Server aus, den Sie zuvor definiert haben.
1. Aktivieren Sie das Kontrollkästchen „Bereich überschreiben“, und geben Sie den Bereich ein, den Sie für den Back-End-API-Aufruf zuvor notiert haben.

   > [!NOTE]
   > Sie verfügen nun über eine API Management-Instanz, die weiß, wie Zugriffstoken von Azure AD B2C zum Autorisieren von Anforderungen abgerufen werden, und die Ihre Oauth2-Konfiguration in Azure Active Directory B2C versteht.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Einrichten der Richtlinien **CORS** und **validate-jwt**

> Die folgenden Abschnitte sollten unabhängig von der APIM-Dienstebene befolgt werden. 

1. Wechseln Sie zurück zur Registerkarte „Entwurf“, und wählen Sie „Alle APIs“ aus. Klicken Sie dann auf die Schaltfläche „Codeansicht“, um den Richtlinien-Editor anzuzeigen.
1. Bearbeiten Sie den eingehenden Abschnitt, und fügen Sie den folgenden XML-Code ein, sodass er wie folgt aussieht.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Bearbeiten Sie die openid-config-URL, damit diese mit dem bekannten Azure AD B2C-Endpunkt für die Registrierungs- oder Anmelderichtlinie übereinstimmt.
1. Bearbeiten Sie den Anspruchswert so, dass er mit der gültigen Anwendungs-ID (auch als Client-ID bezeichnet) für die Back-End-API-Anwendung übereinstimmt, und speichern Sie ihn.

   > [!NOTE]
   > API Management ist nun in der Lage, auf quellenübergreifende Anforderungen an JS-SPA-Apps zu reagieren. Der Dienst führt Drosselung, Ratenbegrenzung und Vorabvalidierung des JWT-Authentifizierungstokens VOR dem Weiterleiten der Anforderung an die Funktions-API durch.

   > [!NOTE]
   > Der folgende Abschnitt ist optional und gilt nicht für den **Verbrauchstarif** , der keine Unterstützung für das Entwicklerportal bietet.
   > Wenn Sie nicht beabsichtigen, das Entwicklerportal zu verwenden, oder dieses nicht verwenden können, da Sie den Verbrauchstarif verwenden, überspringen Sie diesen Schritt, und fahren Sie direkt mit [Erstellen der JavaScript-SPA für die Verwendung der API](#build-the-javascript-spa-to-consume-the-api) fort.

## <a name="optional-configure-the-developer-portal"></a>(Optional:) Konfigurieren des Entwicklerportals

1. Öffnen Sie das Blatt „Azure AD B2C“, und navigieren Sie zur Anwendungsregistrierung für das Entwicklerportal.
1. Legen Sie den Eintrag „Antwort-URL“ auf den Wert fest, den Sie zuvor beim Konfigurieren des redirect_uri für die Autorisierungscodegewährung in API Management notiert haben.

   Nachdem nun die OAuth 2.0-Benutzerautorisierung für die `Echo API` aktiviert ist, ruft die Entwicklerkonsole im Namen des Benutzers ein Zugriffstoken ab, bevor die API aufgerufen wird.

1. Navigieren Sie im Entwicklerportal zu irgendeinem Vorgang unter der `Echo API`, und wählen Sie **Ausprobieren** aus, damit Sie zur Entwicklerkonsole gelangen.
1. Beachten Sie ein neues Element im Abschnitt **Autorisierung** , das dem soeben hinzugefügten Autorisierungsserver entspricht.
1. Wählen Sie in der Dropdownliste „Autorisierung“ die Option **Autorisierungscode** aus. Sie werden dann aufgefordert, sich beim Azure AD-Mandanten anzumelden. Wenn Sie bereits mit dem Konto angemeldet sind, werden Sie möglicherweise nicht dazu aufgefordert.
1. Nach der erfolgreichen Anmeldung wird der Anforderung ein `Authorization: Bearer`-Header mit einem Zugriffstoken von Azure AD B2C im Base64-Format hinzugefügt. 
1. Wählen Sie **Senden** aus, dann können Sie die API aufrufen.

   > [!NOTE]
   > Jetzt kann API Management Token für das Entwicklerportal abrufen, um Ihre API zu testen, es versteht ihre Definition und kann die entsprechende Testseite im Entwicklerportal rendern.

1. Klicken Sie im API Management-Portal auf dem Blatt „Übersicht“ auf „Entwicklerportal“, um sich als Administrator der API anzumelden.
1. Hier können Sie (und andere ausgewählte Consumer Ihrer API) Tests und Aufrufe über eine Konsole durchführen.
1. Wählen Sie „Produkte“, „Unbegrenzt“ und dann die zuvor erstellte API aus, und klicken Sie auf „Ausprobieren“.
1. Blenden Sie den API-Abonnementschlüssel ein, und notieren Sie ihn zusammen mit der Anforderungs-URL an einem sicheren Ort, da Sie beide später benötigen.
1. Wählen Sie in der Dropdownliste der OAuth-Authentifizierung auch „Implizit“ aus. Sie müssen sich hier möglicherweise in einem Popupfenster authentifizieren.
1. Klicken Sie auf „Senden“. Wenn alles funktioniert, sollte Ihre Funktions-App mit einer „Hello“-Nachricht über API Management und einer Nachricht „200 OK“ sowie einigem JSON-Code antworten.

   > [!NOTE]
   > Herzlichen Glückwunsch! Azure AD B2C, API Management und Azure Functions arbeiten nun zusammen, um eine API zu veröffentlichen, zu sichern UND zu verwenden. Sie haben vielleicht bemerkt, dass die API mit dieser Methode in Wirklichkeit zweimal geschützt ist: einmal mit dem Ocp-Subscription-Key-Header von API Management und einmal über die Autorisierung: JWT-Bearertoken.
   > Da es sich bei diesem Beispiel um eine Single-Page-Webanwendung in JavaScript handelt, wird der API Management-Schlüssel nur für die Ratenbegrenzung und Abrechnungsanrufe verwendet.
   > Die tatsächliche Autorisierung und Authentifizierung wird von Azure AD B2C verarbeitet und im JWT gekapselt. Dieses JSON Web Token wird zweimal überprüft: einmal durch API Management und dann durch Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Erstellen der JavaScript-SPA zur Nutzung der API
1. Öffnen Sie das Blatt „Speicherkonten“ im Azure-Portal. 
1. Wählen Sie das Konto aus, das Sie erstellt haben, und wählen Sie im Abschnitt „Einstellungen“ das Blatt „Statische Website“ aus. (Wenn die Option „Statische Website“ nicht angezeigt wird, überprüfen Sie, ob Sie ein V2-Konto erstellt haben.)
1. Legen Sie die Funktion für das statische Webhosting auf „aktiviert“ und den Namen des Indexdokuments auf „index.html“ fest, und klicken Sie dann auf „Speichern“.
1. Notieren Sie den Inhalt des primären Endpunkts, da an diesem Speicherort die Front-End-Website gehostet wird. 

   > [!NOTE]
   > Sie können entweder Azure Blob Storage und die CDN-Neuschreibung oder Azure App Service verwenden. Das Feature zum Hosten der statischen Website von Blob Storage stellt jedoch einen Standardcontainer für statische Webinhalte/HTML/JS/CSS aus Azure Storage bereit und leitet ohne weiteres Zutun eine Standardseite ab.

## <a name="upload-the-js-spa-sample"></a>Hochladen der JS-Beispiel-SPA
1. Wählen Sie auf dem Blatt „Speicherkonto“ im Abschnitt „Blob-Dienst“ das Blatt „Blobs“ aus, und klicken Sie auf den Container „$web“, der im rechten Bereich angezeigt wird.
1. Speichern Sie den unten stehenden Code in einer Datei lokal auf Ihrem Computer als „index.html“, und laden Sie dann die Datei „index.html“ in den Container „$web“ hoch.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Navigieren Sie zum primären Endpunkt der statischen Website, den Sie im letzten Abschnitt gespeichert haben.

   > [!NOTE]
   > Herzlichen Glückwunsch! Sie haben soeben eine Single-Page-Webanwendung in JavaScript in Azure Storage bereitgestellt. Da Sie die JS-App nicht mit Ihren Schlüsseln für die API konfiguriert haben und die JS-App noch nicht mit Ihren Azure AD B2C-Informationen konfiguriert wurde, funktioniert die Seite beim Öffnen noch nicht.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Konfigurieren der JS-SPA für Azure AD B2C
1. Da Sie nun wissen, wo sich alles befindet, können Sie die SPA mit der entsprechenden Adresse der API Management-API und den richtigen Anwendungs-/Client-IDs aus Azure AD B2C konfigurieren.
1. Wechseln Sie im Azure-Portal zurück zum Blatt „Speicher“, klicken Sie auf „index.html“, und wählen Sie „Blob bearbeiten“ aus. 
1. Aktualisieren Sie die Authentifizierungsdetails entsprechend Ihrer Front-End-Anwendung, die Sie zuvor in B2C registriert haben. Beachten Sie dabei, dass die Werte „b2cScopes“ für das API-Back-End gelten.
1. Den Web-API-Schlüssel und die API-URL finden Sie im API Management-Testbereich für den API-Vorgang.
1. Erstellen Sie einen APIM-Abonnementschlüssel, indem Sie zum Blatt „API Management“ zurücknavigieren, „Abonnements“ auswählen und auf „Abonnement hinzufügen“ klicken. Speichern Sie den Datensatz anschließend. Durch Klicken auf die Auslassungspunkte (...) neben der erstellten Zeile können Sie die Schlüssel anzeigen und den Primärschlüssel kopieren.
1. Das Ergebnis sollte wie der folgende Code aussehen:  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Klicken Sie auf Speichern.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Festlegen der Umleitungs-URIs für die Azure AD B2C-Front-End-App
1. Öffnen Sie das Blatt „Azure AD B2C“, und navigieren Sie zur Anwendungsregistrierung für die JavaScript-Front-End-Anwendung.
1. Legen Sie die Umleitungs-URL auf die URL fest, die Sie zuvor beim Einrichten des primären Endpunkts für die statische Website notiert haben.

   > [!NOTE] 
   > Diese Konfiguration führt dazu, dass ein Client der Front-End-Anwendung ein Zugriffstoken mit entsprechenden Ansprüchen von Azure AD B2C erhält.
   > Die SPA kann dieses als Bearertoken im HTTPS-Header im Aufruf der Back-End-API hinzufügen.
   > API Management überprüft das Token vorab, um Ratenbegrenzungen für Aufrufe an den Endpunkt anhand des Abonnentenschlüssels festzulegen, bevor die Anforderung an die empfangende Azure Functions-API weitergeleitet wird.
   > Die SPA rendert die Antwort im Browser.

   > *Herzlichen Glückwunsch! Sie haben Azure AD B2C, Azure API Management, Azure Functions und die Azure App Service-Autorisierung so konfiguriert, dass diese Funktionen perfekt zusammenarbeiten.*

   > [!NOTE]
   > Sie verfügen nun über eine einfache App mit einer einfachen geschützten API. Testen Sie sie nun.

## <a name="test-the-client-application"></a>Testen der Clientanwendung
1. Öffnen Sie die URL der Beispiel-App, die Sie im zuvor erstellten Speicherkonto notiert haben.
1. Klicken Sie in der rechten oberen Ecke auf „Anmelden“. Dadurch wird das Azure AD B2C-Registrierungs- oder -Anmeldeprofil angezeigt.
1. Nach dem Anmelden wird der Abschnitt „Angemeldet als“ auf dem Bildschirm aus Ihrem JSON Web Token (JWT) aufgefüllt.
1. Klicken Sie nun auf „Web-API aufrufen“. Die Seite sollte mit den Werten aktualisiert werden, die von der geschützten API zurückgesendet werden.

## <a name="and-were-done"></a>Fertig.
Die obigen Schritte können angepasst und bearbeitet werden, um viele verschiedene Anwendungsfälle von Azure AD B2C mit API Management zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu [Azure Active Directory und OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md)
* Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
* Weitere Methoden zum Sichern Ihres Back-End-Diensts finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](api-management-howto-mutual-certificates.md).
* [Erstellen einer API Management-Dienstinstanz](get-started-create-service-instance.md)
* [Verwalten Ihrer ersten API](import-and-publish.md)
