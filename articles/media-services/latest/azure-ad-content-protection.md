---
title: End-to-End-Inhaltsschutz mithilfe von Azure AD
description: In diesem Artikel erfahren Sie, wie Sie Ihre Inhalte mit Azure Media Services und Azure Active Directory schützen.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9415d66c49992bc31f773dec908a861f1126e714
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427212"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Tutorial: End-to-End-Inhaltsschutz mithilfe von Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit diesem Tutorial und dem bereitgestellten Playerbeispiel können Sie ein auf Azure Media Services (AMS) und Azure Active Directory (AAD) basierendes End-to-End-Subsystem für den Schutz von Medieninhalten einrichten, um Medieninhalte mit allen von AMS unterstützten DRM-/AES-128-Optionen, Streamingprotokollen, Codecs und Containerformaten zu streamen. Das Beispiel ist generisch genug für sicheren Zugriff auf eine beliebige REST-API, die per OAuth 2.0-Autorisierungscodeflow mit Proof Key for Code Exchange (PKCE) geschützt ist. (Der Azure Media Services-Lizenzübermittlungsdienst ist nur eines von vielen Beispielen.) Das Beispiel kann auch mit der Microsoft Graph-API sowie mit einer beliebigen benutzerdefinierten REST-API verwendet werden, die per OAuth 2.0-Autorisierungscodeflow geschützt ist. Hierbei handelt es sich um das Begleitdokument für den [Beispielcode](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

In diesem Tutorial wird Folgendes vermittelt:

> [!div class="checklist"]
>
> * Authentifizierungsanforderungen
> * Funktionsweise der App
> * Registrieren einer Back-End-Ressourcen-App
> * Registrieren einer Client-App
> * Einrichten der Richtlinie für Inhaltsschlüssel und der Streamingrichtlinien für das Media Services-Konto
> * Einrichten der Player-App

Sollten Sie über kein Azure Media Services-Abonnement verfügen, erstellen Sie ein [kostenloses Azure-Testkonto](https://azure.microsoft.com/free/) und anschließend ein Media Services-Konto.

### <a name="duration"></a>Dauer
Nach der Vorbereitung der erforderlichen Technologie dauert das Tutorial etwa zwei Stunden.

## <a name="prerequisites"></a>Voraussetzungen

Hier werden folgende aktuelle Technologieversionen und Konzepte verwendet. Es wird empfohlen, sich vor Beginn des Tutorials mit ihnen vertraut machen.

### <a name="prerequisite-knowledge"></a>Vorkenntnisse

Im Idealfall sollten Sie vor Beginn dieses Tutorials mit folgenden Konzepten vertraut sein:

* Digital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* [Richtlinien für Inhaltsschlüssel](content-key-policy-concept.md) von AMS mit der AMS-API v3, dem Azure-Portal oder dem Tool [Azure Media Services Explorer (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Azure AD-Endpunkte der Version 2 in [Microsoft Identity Platform](../../active-directory/develop/index.yml)
* Moderne Cloudauthentifizierung (beispielsweise [OAuth 2.0 und OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md))
  * [OAuth 2.0-Autorisierungscodeflow](../../active-directory/develop/v2-oauth2-auth-code-flow.md) und Gründe für PKCE
  * [Gegenüberstellung von delegierter Berechtigung und Anwendungsberechtigung](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT-Token](../../active-directory/develop/access-tokens.md), zugehörige Ansprüche und Rollover von Signaturschlüsseln (im Beispiel enthalten)

### <a name="prerequisite-code-and-installations"></a>Erforderlicher Code und erforderliche Installationen

* Der Beispielcode. Den Beispielcode können Sie [hier](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) herunterladen.
* Eine Installation von Visual Studio Code. Visual Studio Code können Sie hier herunterladen: [https://code.visualstudio.com/download](https://code.visualstudio.com/download).
* Eine Installation von Node.js. Node.js können Sie hier herunterladen: [https://nodejs.org](https://nodejs.org). npm ist in der Installation enthalten.
* Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
* Ein AMS-Konto (Azure Media Services).
* @azure/msal-browser v2.0, eines der Mitglieder der SDK-Familie von [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) für verschiedene Clientplattformen.
* Die neueste Version von [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples) (im Beispiel enthalten).
* FPS-Anmeldeinformationen von Apple, wenn Sie FairPlay-DRM und das mit CORS gehostete Anwendungszertifikat einschließen möchten, auf das über clientseitigen JavaScript-Code zugegriffen werden kann.

> [!IMPORTANT]
> In diesem Tutorial wird .NET verwendet, um die Einschränkung der Richtlinie für Inhaltsschlüssel zu erstellen.  Wenn Sie kein .NET-Entwickler sind, aber Node.js für die Verbindungsherstellung mit Azure Media Services ausprobieren möchten, finden Sie weitere Informationen unter [Verbinden mit der Media Services v3-API – Node.js](configure-connect-nodejs-howto.md). Es ist auch ein Node.js-Modul zur Automatisierung des Schlüsselrollovers verfügbar. Weitere Informationen finden Sie [hier](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Anforderungen für die Authentifizierung und Autorisierung

Die Gestaltung des Subsystems ist mit ein paar Herausforderungen verbunden. Er gibt verschiedene variable Komponenten, Einschränkungen für die Client-App sowie das Azure AD-Schlüsselrollover, das alle sechs Wochen erfolgt.

Bei der in diesem Tutorial verwendeten Single-Page-Webanwendung (Single-Page App, SPA) werden Herausforderungen im Zusammenhang mit Authentifizierungsanforderungen und den damit einhergehenden Einschränkungen berücksichtigt. Folgendes wird verwendet:

* Azure AD-Endpunkte der Version 2 aufgrund des Wechsels von der Azure AD-Entwicklerplattform (Endpunkte der Version 1) zu Microsoft Identity Platform (Endpunkte der Version 2).
* Autorisierungscodeflow, da der OAuth 2.0-Flow zur impliziten Genehmigung veraltet ist.
* Eine App, die folgenden Einschränkungen unterliegt:
    * Ein öffentlicher Client kann den geheimen Clientschlüssel nicht verbergen.  Da der Clientschlüssel bei alleiniger Verwendung des Autorisierungscodeflows verborgen werden muss, wird der Autorisierungscodeflow mit PKCE verwendet.
    * Eine browserbasierte App, die einer Browsersicherheitssandbox (CORS-/Preflight-Einschränkung) unterliegt.
    * Eine browserbasierte App, die modernen JavaScript-Code verwendet, der JavaScript-Sicherheitseinschränkungen (Zugriff auf benutzerdefinierte Header, Korrelations-ID) unterliegt.

## <a name="understand-the-subsystem-design"></a>Grundlegendes zum Entwurf des Subsystems

Der Entwurf des Subsystems ist in der Abbildung weiter unten dargestellt.  Er umfasst drei Ebenen:

* Backoffice-Ebene (schwarz) zum Konfigurieren der Richtlinie für Inhaltsschlüssel sowie zum Veröffentlichen von Streaminginhalten
* Öffentliche Endpunkte (blau), bei denen es sich um Player-/Kundenendpunkte mit Authentifizierung, Autorisierung, DRM-Lizenz und verschlüsseltem Inhalt handelt
* Player-App (hellblau), die alle Komponenten integriert und folgende Aktionen ausführt:
    * Benutzerauthentifizierung über Azure AD
    * Bezug von „access_token“ von Azure AD
    * Empfang des Manifests und der verschlüsselten Inhalte von AMS/CDN
    * Bezug der DRM-Lizenz von Azure Media Services
    * Inhaltsentschlüsselung, Decodierung und Anzeige

![Bildschirm für die Analyse von JWT-Token](media/aad-ams-content-protection/subsystem.svg)

Ausführlichere Informationen zum Subsystem finden Sie unter [Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](./design-multi-drm-system-with-access-control.md).

## <a name="understand-the-single-page-app"></a>Grundlegendes zur Single-Page-Webanwendung

Bei der Player-App handelt es sich um eine in Visual Studio Code entwickelte Single-Page-Webanwendung (Single-Page Application, SPA) mit Folgendem:

* Node.js mit ES 6 JavaScript
* @azure/msal-browser 2.0 (Beta)
* Azure Media Player SDK
* OAuth 2.0-Flow für Azure AD-Endpunkte der Version 2 (Microsoft Identity Platform)

Von der SPA-Player-App werden folgende Aktionen ausgeführt:

* Benutzerauthentifizierung für mandanteninterne Benutzer sowie für Gastbenutzer aus anderen AAD-Mandanten oder MSA-Konten. Benutzer können sich entweder über ein Browserpopup oder über eine Umleitung anmelden (für Browser wie Safari, in denen keine Popups zugelassen werden).
* Bezug von `access_token` per Autorisierungscodeflow mit PKCE.
* Verlängerung von `access_token` (von AAD ausgestellte Token sind eine Stunde lang gültig). Hierfür wird auch `refresh_token` bezogen.
* Analyse von JWT-Token (sowohl `access_token` als auch `id_token`) zu Test- und Überprüfungszwecken.
* Bezug von DRM-Lizenzen für alle drei DRMs oder den AES-128-Inhaltsschlüssel.
* Streaming von Inhalten mit verschiedenen Kombinationen aus DRM/Streamingprotokoll/Containerformat. Für die einzelnen Kombinationen wird jeweils die korrekte Formatzeichenfolge generiert.
* Entschlüsselung, Decodierung und Anzeige.
* Aufruf der Microsoft Graph-API zu Problembehandlungszwecken. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Der Bildschirm für Anmeldung, Tokenbezug, Tokenverlängerung und Tokenanzeige sieht wie folgt aus:

 ![Bildschirm für Anmeldung, Tokenbezug, Tokenverlängerung und Tokenanzeige](media/aad-ams-content-protection/token-acquisition.png)

Der Bildschirm für die Analyse von JWT-Token („access_token“ oder „id_token“) sieht wie folgt aus:

![Screenshot: Analysieren von JWT-Token](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Der Bildschirm zum Testen geschützter Inhalte mit unterschiedlichen Kombinationen aus DRM/AES, Streamingprotokollen und Containerformat sieht wie folgt aus:

![Screenshot: Testen geschützter Inhalte mit unterschiedlichen Kombinationen aus DRM oder AES, Streamingprotokollen und Containerformat](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Auswählen eines Azure AD-Mandanten

> [!NOTE]
> Im weiteren Verlauf wird davon ausgegangen, dass Sie sich beim Azure-Portal angemeldet haben und über mindestens einen Azure AD-Mandanten verfügen.

Wählen Sie einen Azure AD-Mandanten für das End-to-End-Beispiel aus. Sie haben zwei Möglichkeiten:

* Sie können einen bereits vorhandenen Azure AD-Mandanten verwenden. Jedes Azure-Abonnement muss über einen Azure AD-Mandanten verfügen, ein Azure AD-Mandant kann jedoch von mehreren Azure-Abonnements verwendet werden.
* Sie können einen neuen Azure AD-Mandanten verwenden, der *nicht* von einem Azure-Abonnement verwendet wird. Wenn Sie sich für die Verwendung eines neuen Mandanten entscheiden, müssen sich das Media Services-Konto und die exemplarische Player-App in einem Azure-Abonnement befinden, das einen separaten Azure AD-Mandanten verwendet. Dies bietet eine gewisse Flexibilität. So können Sie beispielsweise Ihren eigenen Azure AD-Mandanten, aber auch das Media Services-Konto des Kunden in dessen Azure-Abonnement verwenden.

## <a name="register-the-backend-resource-app"></a>Registrieren der Back-End-Ressourcen-App

1. Navigieren Sie zu dem von Ihnen gewählten oder erstellten Azure AD-Mandanten.
1. Wählen Sie die Menüoption **Azure Active Directory** aus.
1. Wählen Sie die Menüoption **App-Registrierungen** aus.
1. Klicken Sie auf **+ Neue Registrierung**.
1. Nennen Sie die App *LicenseDeliveryResource2*. („2“ steht hier für AAD-Endpunkte der Version 2.)
1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis (nur "[ *Name Ihres Mandanten* ]" – einzelner Mandant)** aus. Falls Sie Zugriff auf mehrere Mandanten ermöglichen möchten, wählen Sie eine der anderen Optionen für mehrere Mandanten aus.
1. Der **Umleitungs-URI** ist optional und kann später geändert werden.
1. Klicken Sie auf **Registrieren**. Die Ansicht für App-Registrierungen wird angezeigt.
1. Wählen Sie die Menüoption **Manifest** aus. Die Manifestansicht wird angezeigt.
1. Ändern Sie den Wert von `accessTokenAcceptedVersion` in *2* (ohne Anführungszeichen).
1. Ändern Sie den Wert von `groupMembershipClaims` in *"SecurityGroup"* (mit Anführungszeichen).
1. Klicken Sie auf **Speichern**.
1. Wählen Sie die Menüoption **Eine API verfügbar machen** aus. Die Ansicht „Bereich hinzufügen“ wird angezeigt. (Azure stellt einen Anwendungs-ID-URI bereit. Dieser kann bei Bedarf durch Bearbeiten des entsprechenden Felds geändert werden.)
1. Klicken Sie auf **Speichern und fortfahren**. Die Ansicht ändert sich. Geben Sie auf der Grundlage der folgenden Tabelle für jede Einstellung aus der Spalte „Einstellung“ den Wert aus der Spalte „Wert“ ein, und klicken Sie anschließend auf **Bereich hinzufügen** :

| Einstellung | Wert | Beschreibung |
| ------- | ----- | ----------- |
| Bereichsname | *DRM.License.Delivery* | Gibt an, wie der Bereich beim Anfordern des Zugriffs auf diese API sowie in Zugriffstoken dargestellt wird, wenn der Bereich für eine Clientanwendung gewährt wurde. Dieser Wert muss in der gesamten Anwendung eindeutig sein. Es empfiehlt sich, den Namen auf der Grundlage des Musters „Ressource.Vorgang.Einschränkung“ zu konstruieren. |
| Wer darf einwilligen? | *Administratoren und Benutzer* | Steuert, ob Benutzer ihre Einwilligung für diesen Bereich in Verzeichnissen erteilen dürfen, in denen die Benutzereinwilligung aktiviert ist. |
| Anzeigename der Administratoreinwilligung | *DRM-Lizenzbereitstellung* | Gibt den Namen des Bereichs im Einwilligungsbildschirm an, wenn Administratoren ihre Einwilligung für diesen Bereich erteilen. |
| Beschreibung der Administratoreinwilligung** | *DRM license delivery backend resource scope* | Eine ausführliche Beschreibung des Bereichs, die angezeigt wird, wenn Mandantenadministratoren einen Bereich im Einwilligungsbildschirm erweitern. |
| Anzeigename der Benutzereinwilligung | *DRM.License.Delivery* | Gibt den Namen des Bereichs im Einwilligungsbildschirm an, wenn Benutzer ihre Einwilligung für diesen Bereich erteilen. |
| Beschreibung der Benutzereinwilligung | *DRM license delivery backend resource scope* | Dies ist eine ausführliche Beschreibung des Bereichs, die angezeigt wird, wenn Benutzer einen Bereich im Einwilligungsbildschirm erweitern. |
| Zustand | *Aktiviert* | Gibt an, ob dieser Bereich für Clientanforderungen verfügbar ist. Legen Sie diese Option für Bereiche, die für Clients nicht sichtbar sein sollen, auf „Deaktiviert“ fest. Nur deaktivierte Bereiche können gelöscht werden, und es empfiehlt sich, mit der Löschung eines deaktivierten Bereichs mindestens eine Woche zu warten, um sicherzustellen, dass er von keinen Clients mehr verwendet wird. |

## <a name="register-the-client-app"></a>Registrieren der Client-App

1. Navigieren Sie zu dem von Ihnen gewählten oder erstellten Azure AD-Mandanten.
1. Wählen Sie die Menüoption **Azure Active Directory** aus.
1. Wählen Sie die Menüoption **App-Registrierungen** aus.
1. Klicken Sie auf **+ Neue Registrierung**.
1. Geben Sie der Client-App einen Namen (beispielsweise *AMS AAD Content Protection* ).
1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis (nur "[ *Name Ihres Mandanten* ]" – einzelner Mandant)** aus. Falls Sie Zugriff auf mehrere Mandanten ermöglichen möchten, wählen Sie eine der anderen Optionen für mehrere Mandanten aus.
1. Der **Umleitungs-URI** ist optional und kann später geändert werden.
1. Klicken Sie auf **Registrieren**.
1. Wählen Sie die Menüoption **API-Berechtigungen** aus.
1. Klicken Sie auf **+ Add a permission** (+ Berechtigung hinzufügen). Die Ansicht „API-Berechtigungen anfordern“ wird geöffnet.
1. Klicken Sie auf die Registerkarte **My API** (Meine API), und wählen Sie die App *LicenseDeliveryResource2* aus, die Sie im letzten Abschnitt erstellt haben.
1. Klicken Sie auf den DRM-Pfeil, und aktivieren Sie die Berechtigung *DRM.License.Delivery*.
1. Klicken Sie a **Berechtigungen hinzufügen**. Die Ansicht „Berechtigungen hinzufügen“ wird geschlossen.
1. Wählen Sie die Menüoption **Manifest** aus. Die Manifestansicht wird angezeigt.
1. Suchen Sie die folgenden Wertpaare, und fügen Sie sie dem Attribut `replyUrlsWithType` hinzu:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > An diesem Punkt verfügen Sie noch nicht über die URL für Ihre Player-App.  Falls Sie die App über Ihren Localhost-Webserver ausführen, können Sie nur das localhost-Wertpaar verwenden. Nach der Bereitstellung der Player-App können Sie den Eintrag hier mit der bereitgestellten URL hinzufügen.  Sollten Sie diesen Schritt vergessen, wird bei der Azure AD-Anmeldung eine Fehlermeldung angezeigt.

1. Klicken Sie auf **Speichern**.
1. Wählen Sie abschließend **Authentifizierung** aus, um zu überprüfen, ob die Konfiguration korrekt ist.  Die Authentifizierungsansicht wird angezeigt. Ihre Clientanwendung wird als Single-Page-Webanwendung (Single-Page App, SPA) aufgeführt, der Umleitungs-URI wird angezeigt, und der Gewährungstyp ist der Autorisierungscodeflow mit PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Einrichten der Richtlinie für Inhaltsschlüssel und der Streamingrichtlinien für das Media Services-Konto

**In diesem Abschnitt wird davon ausgegangen, dass Sie .NET-Entwickler und mit der Verwendung der AMS v3-API vertraut sind.**

> [!NOTE]
> Zum Zeitpunkt der Erstellung dieses Artikels kann die Richtlinie für Inhaltsschlüssel des Media Services-Kontos nicht über das Azure-Portal eingerichtet werden, da die Verwendung eines asymmetrischen Tokensignaturschlüssels mit OpenID-Config im Portal nicht unterstützt wird.  Das Setup muss das Azure AD-Schlüsselrollover unterstützen, da das von Azure AD ausgegebene Token mit einem asymmetrischen Schlüssel signiert und für den Schlüssel alle sechs Wochen ein Rollover durchgeführt wird. Daher werden in diesem .NET und die AMS v3-API verwendet.

Es gelten die Konfiguration der [Richtlinie für Inhaltsschlüssel](content-key-policy-concept.md) sowie die [Streamingrichtlinien](streaming-policy-concept.md) für DRM und AES-128.  Ändern Sie `ContentKeyPolicyRestriction` in der Richtlinie für Inhaltsschlüssel.

Im Anschluss finden Sie den .NET-Code zum Erstellen der Einschränkung der Richtlinie für Inhaltsschlüssel.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Ändern Sie im obigen Code die Werte für `ida_AADOpenIdDiscoveryDocument`, `ida_audience` und `ida_issuer`. Die Werte für diese Elemente finden Sie im Azure-Portal wie folgt:

1. Wählen Sie den zuvor verwendeten AAD-Mandanten aus, klicken Sie im Menü auf **App-Registrierungen** , und klicken Sie anschließend auf den Link **Endpunkte**.
1. Kopieren Sie den Wert des Felds **OpenIdConnect-Metadatendokument** , und fügen Sie ihn als Wert von `ida_AADOpenIdDiscoveryDocument` in den Code ein.
1. Bei dem Wert `ida_audience` handelt es sich um die Anwendungs-ID (Client-ID) der registrierten App *LicenseDeliveryResource2*.
1. Der Wert `ida_issuer` ist die URL `https://login.microsoftonline.com/[tenant_id]/v2.0`. Ersetzen Sie „[ *tenant_id* ]“ durch die Mandanten-ID.

## <a name="set-up-the-sample-player-app"></a>Einrichten der exemplarischen Player-App

Falls noch nicht geschehen, klonen Sie die App, oder laden Sie sie aus dem Quellrepository herunter: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

Bei der Einrichtung der Player-App haben Sie zwei Optionen:

* Sie können die App mit minimalen Anpassungen einrichten und nur einige konstante Zeichenfolgenwerte wie Client-ID, Mandanten-ID und Streaming-URL ersetzen. In diesem Fall müssen Sie allerdings Visual Studio Code und Node.js verwenden.
* Wenn Sie lieber eine andere IDE und Webplattform verwenden möchten (beispielsweise ASP.NET Core), können Sie die Webseiten- und JavaScript-Dateien sowie die CSS-Datei in Ihrem Projekt platzieren, da die Player-App an sich keinerlei serverseitigen Code verwendet.

### <a name="option-1"></a>Option 1:

1. Starten Sie Visual Studio Code.
1. Klicken Sie zum Öffnen des Projekts auf „Datei“ > „Ordner öffnen“, navigieren Sie zum übergeordneten Ordner der Datei *package.json* , und wählen Sie ihn aus.
1. Öffnen Sie die JavaScript-Datei *public/javascript/constants.js*.
1. Ersetzen Sie `OAUTH2_CONST.CLIENT_ID` durch die Client-ID (`client_id`) Ihrer registrierten Client-App im AAD-Mandanten.  Die Client-ID (`client_id`) finden Sie im Azure-Portal im Abschnitt „Übersicht“ der registrierten App. Hinweis: Hierbei handelt es sich um die Client-ID, nicht um die Objekt-ID.
1. Ersetzen Sie `OAUTH2_CONST.TENANT_ID` durch die Mandanten-ID (`tenant_id`) Ihres Azure AD-Mandanten. Klicken Sie zum Ermitteln Ihrer Mandanten-ID (`tenant_id`) auf das Azure Active Directory-Menü. Die Mandanten-ID wird im Abschnitt „Übersicht“ angezeigt.
1. Ersetzen Sie `OAUTH2_CONST.SCOPE` durch den Bereich, den Sie in Ihrer registrierten Client-App hinzugefügt haben. Navigieren Sie zum Ermitteln des Bereichs im Menü **App-Registrierungen** zu der registrierten Client-App, und wählen Sie sie aus:
    1. Wählen Sie Ihre Client-App aus, klicken Sie auf das Menü **API-Berechtigungen** , und wählen Sie anschließend unter der API-Berechtigung *LicenseDeliveryResource2* den Bereich *DRM.License.Delivery* aus. Das Format der Berechtigung sollte in etwa wie folgt aussehen: *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery*. **Wichtig** : Behalten Sie in `OAUTH2_CONST.SCOPE` das Leerzeichen vor `offline_access` bei.
1. Ersetzen Sie die beiden konstanten Zeichenfolgen für `AMS_CONST` wie im Anschluss gezeigt. Eine ist die geschützte Streaming-URL Ihres Testmedienobjekts, die andere ist die FPS-Anwendungszertifikat-URL für den Fall, dass Sie den FairPlay-Testfall einschließen möchten. Andernfalls können Sie `AMS_CONST.APP_CERT_URL` unverändert lassen. Klicken Sie dann auf **Speichern**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Gehen Sie zum lokalen Testen wie folgt vor:

1. Wählen Sie in Visual Studio Code (VSC) im Hauptmenü die Option **Ansicht** und anschließend **Terminal** aus.
1. Sollte npm noch nicht installiert sein, geben Sie an der Eingabeaufforderung `npm install` ein.
1. Geben Sie an der Eingabeaufforderung `npm start` ein. (Falls npm nicht gestartet wird, geben Sie an der Eingabeaufforderung `cd npmweb` ein, um zum Verzeichnis `npmweb` zu wechseln.)
1. Navigieren Sie in einem Browser zu `http://localhost:3000`.

Wählen Sie je nach verwendetem Browser die richtige Kombination aus DRM/AES, Streamingprotokoll und Containerformat aus, um den Test nach der Anmeldung (Bezug von `access_token`) durchzuführen. Falls Sie in Safari unter macOS testen, aktivieren Sie die Option für die Umleitungs-API, da von Safari keine Popups zugelassen werden. In den meisten anderen Browsern stehen sowohl Popups als auch Umleitungsoptionen zur Verfügung.

### <a name="option-2"></a>Option 2:

Wenn Sie eine andere IDE/Webplattform und/oder einen auf Ihrem Entwicklungscomputer ausgeführten Webserver wie etwa IIS verwenden möchten, kopieren Sie die folgenden Dateien auf Ihrem lokalen Webserver in ein neues Verzeichnis. Sie finden die Dateien im von Ihnen heruntergeladenen Code unter den folgenden Pfaden:

* *views/index.ejs* (Ändern Sie das Suffix in „.html“.)
* *views/jwt.ejs* (Ändern Sie das Suffix in „.html“.)
* *views/info.ejs* (Ändern Sie das Suffix in „.html“.)
* *public/* * (JavaScript-Dateien, CSS und Bilder, wie unten gezeigt)

1. Kopieren Sie die Dateien aus dem Ordner *view* an den Stamm des neuen Verzeichnisses.
1. Kopieren Sie die *Ordner* aus dem Ordner *public* an den Stamm des neuen Verzeichnisses.
1. Ändern Sie die Erweiterung der Dateien vom Typ `.ejs` in `.html`. (Da keine serverseitige Variable verwendet wird, kann diese Änderung problemlos vorgenommen werden.)
1. Öffnen Sie *index.html* in VSC (oder in einem anderen Code-Editor), und ändern Sie die Pfade für `<script>` und `<link>` so, dass sie dem Speicherort der Dateien entsprechen.  Wenn Sie die vorherigen Schritte befolgt haben, müssen Sie lediglich den Schrägstrich (`\`) im Pfad löschen.  `<script type="text/javascript" src="/javascript/constants.js"></script>` wird beispielsweise zu `<script type="text/javascript" src="javascript/constants.js"></script>`.
1. Passen Sie die Konstanten in der Datei *javascript/constants.js* wie bei der ersten Option an.

## <a name="common-customer-scenarios"></a>Allgemeine Kundenszenarien

Nachdem Sie das Tutorial nun abgeschlossen haben und über ein funktionierendes Subsystem verfügen, können Sie es für die folgenden Kundenszenarien ändern:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für die Lizenzbereitstellung per Azure AD-Gruppenmitgliedschaft

Bislang kann jeder Benutzer, der sich anmelden kann, eine gültige Lizenz erhalten und die geschützten Inhalte wiedergeben.

Eine gängige Kundenanforderung ist, dass Inhalte nur von einem Teil der authentifizierten Benutzer angesehen werden dürfen. Ein Beispiel wäre etwa ein Kunde, der Basis- und Premium-Abonnements für seine Videoinhalte anbietet. Kunden, die für ein Basisabonnement bezahlt haben, sollen sich keine Inhalte ansehen können, für die ein Premium-Abonnement erforderlich ist. Zur Erfüllung dieser Anforderung sind folgende zusätzliche Schritte erforderlich:

#### <a name="set-up-the-azure-ad-tenant"></a>Einrichten des Azure AD-Mandanten

1. Richten Sie in Ihrem Mandanten zwei Konten ein. Diese können beispielsweise *premium_user* und *basic_user* heißen.
1. Erstellen Sie eine Benutzergruppe, und nennen Sie sie *PremiumGroup*.
1. Fügen Sie der Gruppe *PremiumGroup* das Konto *premium_user* (aber nicht das Konto *basic_user* ) als Mitglied hinzu.
1. Notieren Sie sich die **Objekt-ID** von *PremiumGroup*.

#### <a name="set-up-the-media-services-account"></a>Einrichten des Media Services-Kontos

Ändern Sie `ContentKeyPolicyRestriction`, wie im obigen Einrichtungsabschnitt für das Media Services-Konto gezeigt, indem Sie einen Anspruch namens *groups* hinzufügen und dabei `ida_EntitledGroupObjectId` auf die Objekt-ID von *PremiumGroup* festlegen:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

Der Anspruch *groups* ist Mitglied eines [eingeschränkten Anspruchssatzes](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) in Azure AD.

#### <a name="test"></a>Testen

1. Melden Sie sich mit dem Konto *premium_user* an. Sie sollten den geschützten Inhalt wiedergeben können.
1. Melden Sie sich mit dem Konto *basic_user* an. Sie sollten eine Fehlermeldung mit dem Hinweis erhalten, dass das Video verschlüsselt und kein Schlüssel zum Entschlüsseln vorhanden ist. Wenn Sie die Ereignisse, Fehler und Downloads mithilfe der Dropdownliste am unteren Rand der Diagnoseüberlagerung des Players anzeigen, sollte in der Fehlermeldung auf einen Fehler beim Lizenzbezug hingewiesen werden, der auf den fehlenden Anspruchswert für den Anspruch „groups“ im vom Azure AD-Tokenendpunkt ausgestellten JWT zurückzuführen ist.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Unterstützen mehrerer Media Services-Konten (abonnementübergreifend)

Ein Kunde kann über mehrere Media Services-Konten verfügen, die sich entweder in einem einzelnen Azure-Abonnement oder in mehreren Azure-Abonnements befinden können. So kann ein Kunde beispielsweise ein Media Services-Konto als primäres Produktionskonto, ein anderes Konto als sekundäres Konto/Backupkonto und ein weiteres Konto für die Entwicklung bzw. zu Testzwecken verwenden.

Sie müssen lediglich darauf achten, dass Sie bei der Erstellung von `ContentKeyPolicyRestriction` in allen Media Services-Konten die gleichen Parameter verwenden wie im Einrichtungsabschnitt für das Media Services-Konto.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Unterstützen eines Kunden sowie seiner Lieferanten und/oder Niederlassungen in mehreren AAD-Mandanten

Als Benutzer der Lösung können sich die Niederlassungen, Lieferanten und Partner eines Kunden in verschiedenen AAD-Mandanten (beispielsweise `mycustomer.com`, `mysubsidiary.com` und `myparther.com`) befinden. Diese Lösung basiert zwar auf einem einzelnen spezifischen AAD-Mandanten (beispielsweise `mycustomer.com`), kann aber für die Verwendung durch Benutzer aus anderen Mandanten angepasst werden.

Fügen Sie dem Mandanten `mycustomer.com` einen Benutzer aus `mypartner.com` als Gastbenutzer hinzu (bei Verwendung von `mycustomer.com` für diese Lösung). Stellen Sie sicher, dass der Benutzer `mypartner.com` das Gastkonto aktiviert. Das Gastkonto kann entweder aus einem anderen AAD-Mandanten stammen oder ein Konto vom Typ `outlook.com` sein.

Beachten Sie, dass die Gastbenutzer aus `mypartner.com`nach der Aktivierung in `mycustomer.com` weiterhin über ihren eigenen/ursprünglichen AAD-Mandanten (`mypartner.com`) authentifiziert werden, das Zugriffstoken (`access_token`) aber von `mycustomer.com` ausgestellt wird.

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Unterstützen eines Kundenmandanten/-abonnements mit einer Einrichtung in Ihrem Abonnement/Mandanten

Sie können Ihr Setup verwenden, um geschützte Inhalte im Media Services-Konto/-Abonnement Ihres Kunden zu testen. Dazu müssen Sie es mit einem Azure AD-Mandanten und einem Media Services-Konto im gleichen Abonnement einrichten. Das Media Services-Konto des Kunden befindet sich in dessen Azure-Abonnement mit eigenem Azure AD-Mandanten.

1. Fügen Sie Ihrem Mandanten das Konto Ihres Kunden als Gastkonto hinzu.
1. Bereiten Sie in Zusammenarbeit mit Ihrem Kunden geschützte Inhalte im Media Services-Konto Ihres Kunden vor, indem Sie die drei Parameter bereitstellen, die im Einrichtungsabschnitt für das Media Services-Konto aufgeführt sind.

Ihr Kunde kann dann zu Ihrem Setup navigieren, sich mit dem Gastkonto anmelden und seine eigenen geschützten Inhalte testen. Sie können sich auch mit Ihrem eigenen Konto anmelden und den Inhalt Ihres Kunden testen.

Ihre Beispiellösung kann in einem Microsoft-Mandanten mit Microsoft-Abonnement oder in einem benutzerdefinierten Mandanten mit Microsoft-Abonnement eingerichtet werden. Die Azure Media Services-Instanz kann aus einem anderen Abonnement mit eigenem Mandanten stammen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

> [!WARNING]
> Falls Sie diese Anwendung nicht weiter verwenden möchten, löschen Sie die Ressourcen, die Sie im Rahmen dieses Tutorials erstellt haben. Andernfalls fallen Gebühren an.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Verschlüsseln von Inhalten mithilfe des Portals](encrypt-content-quickstart.md)
