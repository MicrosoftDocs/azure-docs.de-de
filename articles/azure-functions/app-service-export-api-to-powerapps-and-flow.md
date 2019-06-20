---
title: Exportieren einer in Azure gehosteten API nach PowerApps und Microsoft Flow | Microsoft-Dokumentation
description: Übersicht über das Verfügbarmachen einer in App Service gehosteten API für PowerApps und Microsoft Flow
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: 9f4bbf91b09abeb917fd9f49482881e33bf788ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499568"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportieren einer in Azure gehosteten API nach PowerApps und Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) ist ein Dienst zum Erstellen und Verwenden benutzerdefinierter Branchen-Apps, die eine Verbindung mit Ihren Daten herstellen und plattformübergreifend verwendet werden können. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) ermöglicht die problemlose Automatisierung von Workflows und Geschäftsprozessen zwischen Ihren bevorzugten Apps und Diensten. Sowohl PowerApps als auch Microsoft Flow verfügt über verschiedene integrierte Connectors für Datenquellen wie Office 365, Dynamics 365 und Salesforce. In einigen Fällen sollten App- und Flow-Generatoren auch mit den von ihrer Organisation erstellten Datenquellen und APIs verbunden werden.

Analog dazu können Entwickler, die eine umfassendere Bereitstellung ihrer APIs in einer Organisation anstreben, ihre APIs für App- und Flow-Generatoren verfügbar machen. In diesem Thema wird beschrieben, wie eine in [Azure Functions](../azure-functions/functions-overview.md) oder [Azure App Service](../app-service/overview.md) integrierte API exportiert wird. Die exportierte API dient als *benutzerdefinierter Connector*, der wie ein integrierter Connector in PowerApps und Microsoft Flow verwendet wird.

## <a name="create-and-export-an-api-definition"></a>Erstellen und Exportieren einer API-Definition
Vor dem Exportieren einer API müssen Sie die API mithilfe einer OpenAPI-Definition (ehemals als [Swagger](https://swagger.io/)-Datei bezeichnet) beschreiben. Diese Definition enthält Informationen zu den in einer API verfügbaren Vorgängen sowie zur Strukturierung der Anforderungs- und Antwortdaten für die API. PowerApps und Microsoft Flow können benutzerdefinierte Connectors für jede beliebige OpenAPI 2.0-Definition erstellen. In Azure Functions und Azure App Service ist die Unterstützung für das Erstellen, Hosten und Verwalten von OpenAPI-Definitionen bereits integriert. Weitere Informationen finden Sie unter [Hosten einer RESTful-API mit CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Sie können auch benutzerdefinierte Connectors in der Benutzeroberfläche von PowerApps und Microsoft Flow erstellen, ohne eine OpenAPI-Definition zu verwenden. Weitere Informationen finden Sie unter [Registrieren und Verwenden von benutzerdefinierten Connectors (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) und [Registrieren und Verwenden von benutzerdefinierten Connectors (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Um die API-Definition zu exportieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure Functions- oder einer anderen App Service-Anwendung.

    Wenn Sie Azure Functions verwenden, wählen Sie Ihre Funktionen-App aus, und klicken Sie dann auf **Plattformfeatures** und **API-Definition**.

    ![API-Definition von Azure Functions](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Wählen Sie bei Verwendung von Azure App Service in der Liste mit den Einstellungen die Option **API-Definition** aus.

    ![API-Definition von App Service](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Die Schaltfläche **Nach PowerApps + Microsoft Flow exportieren** sollte verfügbar sein (wenn dies nicht der Fall ist, müssen Sie zunächst eine OpenAPI-Definition erstellen). Klicken Sie auf diese Schaltfläche, um den Exportvorgang zu starten.

    ![Schaltfläche „Nach PowerApps + Microsoft Flow exportieren“](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Wählen Sie den **Exportmodus** aus:

    Mit **Express** können Sie innerhalb des Azure-Portals den benutzerdefinierten Connector erstellen. Hierfür müssen Sie bei PowerApps oder Microsoft Flow angemeldet sein und über die Berechtigung zum Erstellen von Connectors in der Zielumgebung verfügen. Dies ist die empfohlene Vorgehensweise, wenn diese beiden Anforderungen erfüllt werden können. Wenn Sie diesen Modus verwenden, befolgen Sie die unten stehenden Anweisungen unter [Verwenden des Expressexports](#express).

    Über die Option **Manuell** können Sie die API-Definition exportieren, die Sie anschließend über die Portale PowerApps oder Microsoft Flow importieren. Dies ist die empfohlene Vorgehensweise, wenn der Azure-Benutzer und der Benutzer mit der Berechtigung zum Erstellen von Connectors nicht dieselbe Person sind oder der Connector in einem anderen Azure-Mandanten erstellt werden muss. Wenn Sie diesen Modus verwenden, befolgen Sie die unten stehenden Anweisungen unter [Verwenden des manuellen Exports](#manual).

    ![Exportmodus](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Der benutzerdefinierte Connector verwendet eine *Kopie* der API-Definition. Änderungen an der Anwendung und der entsprechenden API-Definition werden daher nicht sofort auf PowerApps und Microsoft Flow übertragen. Wenn Sie Änderungen vornehmen, wiederholen Sie die Exportschritte für die neue Version.

<a name="express"></a>
## <a name="use-express-export"></a>Verwenden des Expressexports

Um den Export im **Express**-Modus durchzuführen, gehen Sie folgendermaßen vor:

1. Stellen Sie sicher, dass Sie beim PowerApps- oder Microsoft Flow-Mandanten angemeldet sind, für den Sie den Export durchführen möchten. 

2. Verwenden Sie die Einstellungen aus der Tabelle.

    |Einstellung|BESCHREIBUNG|
    |--------|------------|
    |**Umgebung**|Wählen Sie die Umgebung aus, in der der benutzerdefinierte Connector gespeichert werden soll. Weitere Informationen finden Sie in der [Umgebungsübersicht](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Name der benutzerdefinierten API**|Geben Sie einen Namen ein, der in der Connectorliste der Generatoren PowerApps und Microsoft Flow angezeigt werden soll.|
    |**Vorbereiten der Sicherheitskonfiguration**|Falls erforderlich, geben Sie die erforderlichen Sicherheitskonfigurationsdetails ein, um Benutzern Zugriff auf Ihre API zu gewähren. Dieses Beispiel zeigt einen API-Schlüssel. Weitere Informationen finden Sie weiter unten unter [Angeben eines Authentifizierungstyps](#auth).|
 
    ![Expressexport nach PowerApps und Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klicken Sie auf **OK**. Der benutzerdefinierte Connector wird nun erstellt und der angegebenen Umgebung hinzugefügt.

Ein Beispiel für die Verwendung des **Express**-Modus mit Azure Functions finden Sie unter [Aufrufen einer Funktion aus PowerApps](functions-powerapps-scenario.md) und [Call a function from Microsoft Flow](functions-flow-scenario.md) (Aufrufen einer Funktion aus Microsoft Flow.

<a name="manual"></a>
## <a name="use-manual-export"></a>Verwenden des manuellen Exports

Um den Export im **manuellen** Modus durchzuführen, gehen Sie folgendermaßen vor:

1. Klicken Sie auf **Herunterladen**, und speichern Sie die Datei. Klicken Sie alternativ auf die Schaltfläche „Kopieren“, und speichern Sie die URL. Der heruntergeladene Datei oder die URL wird beim Import verwendet.
 
    ![Manueller Export nach PowerApps und Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Falls Ihre API-Definition Sicherheitsdefinitionen enthält, werden diese in Schritt 2 angegeben. Während des Imports werden sie von PowerApps und Microsoft Flow erkannt, und Sie werden zur Angabe von Sicherheitsinformationen aufgefordert. Erfassen Sie die Anmeldeinformationen für die einzelnen Definitionen, um sie im nächsten Abschnitt verwenden zu können. Weitere Informationen finden Sie weiter unten unter [Angeben eines Authentifizierungstyps](#auth).

    ![Sicherheit beim manuellen Export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    In diesem Beispiel wird die Sicherheitsdefinition des API-Schlüssels gezeigt, die in der OpenAPI-Definition einbezogen wurde.

Nachdem Sie die API-Definition exportiert haben, importieren Sie sie, um einen benutzerdefinierten Connector in PowerApps und Microsoft Flow zu erstellen. Da benutzerdefinierte Connectors von den beiden Diensten gemeinsam genutzt werden, muss die Definition nur einmal importiert werden.

Um die API-Definition in PowerApps und Microsoft Flow zu importieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu [web.powerapps.com](https://web.powerapps.com) oder [flow.microsoft.com](https://flow.microsoft.com).

2. Klicken Sie in der rechten oberen Ecke auf das Zahnradsymbol und dann auf **Benutzerdefinierte Connectors**.

   ![Zahnradsymbol im Dienst](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klicken Sie auf **Benutzerdefinierten Connector erstellen** und dann auf **Import an OpenAPI definition** (OpenAPI-Definition importieren).

   ![Erstellen eines benutzerdefinierten Connectors](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Geben Sie einen Namen für den benutzerdefinierten Connector ein, navigieren Sie dann zu der exportierten OpenAPI-Definition, und klicken Sie auf **Weiter**.

   ![Hochladen der OpenAPI-Definition](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Überprüfen Sie auf der Registerkarte **Allgemein** die Informationen, die aus der OpenAPI-Definition stammen.

5. Geben Sie auf der Registerkarte **Sicherheit** die für den Authentifizierungstyp entsprechenden Werte ein, wenn Sie zur Angabe von Authentifizierungsinformationen aufgefordert werden. Klicken Sie auf **Weiter**.

    ![Registerkarte „Sicherheit“](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Dieses Beispiel zeigt die erforderlichen Felder für die Authentifizierung der API-Schlüssel. Die Felder variieren je nach Authentifizierungstyp.

6. Auf der Registerkarte **Definitionen** werden alle in der OpenAPI-Datei definierten Vorgänge automatisch ausgefüllt. Wenn alle gewünschten Vorgänge definiert sind, können Sie mit dem nächsten Schritt fortfahren. Andernfalls können Sie hier Vorgänge hinzufügen und ändern.

    ![Registerkarte „Definitionen“](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Dieses Beispiel beinhaltet einen Vorgang mit der Bezeichnung `CalculateCosts`. Alle Metadaten (z.B. **Beschreibung**) stammen von der OpenAPI-Datei.

7. Klicken Sie am oberen Rand der Seite auf **Connector erstellen**.

Nun können Sie eine Verbindung mit dem benutzerdefinierten Connector in PowerApps und Microsoft Flow herstellen. Weitere Informationen zum Erstellen von Connectors in den Portalen PowerApps und Microsoft Flow finden Sie unter [Registrieren des benutzerdefinierten Connectors (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) und [Registrieren des benutzerdefinierten Connectors (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Angeben des Authentifizierungstyps

PowerApps und Microsoft Flow unterstützen Auflistungen von Identitätsanbietern, die die Authentifizierung für benutzerdefinierte Connectors ermöglichen. Falls Ihre API eine Authentifizierung erfordert, stellen Sie sicher, dass sie in Ihrem OpenAPI-Dokument als _Sicherheitsdefinition_ erfasst ist. Dies wird im folgenden Beispiel veranschaulicht:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Während des Exports geben Sie Konfigurationswerte an, mit denen PowerApps und Microsoft Flow Benutzer authentifizieren können.

In diesem Abschnitt werden die im **Express**-Modus unterstützten Authentifizierungstypen erläutert: API-Schlüssel, Azure Active Directory und generisches OAuth 2.0. PowerApps und Microsoft Flow unterstützen für bestimmte Dienste wie Dropbox, Facebook und Salesforce auch die Standardauthentifizierung sowie OAuth 2.0.

### <a name="api-key"></a>API-Schlüssel
Wenn ein API-Schlüssel verwendet wird, werden die Benutzer Ihres Connectors beim Herstellen einer Verbindung aufgefordert, den Schlüssel anzugeben. Geben Sie einen API-Schlüsselnamen an, um kenntlich zu machen, welcher Schlüssel erforderlich ist. Im vorangehenden Beispiel haben wir den Namen `API Key (contact meganb@contoso.com)` verwendet, damit die Benutzer wissen, wo Informationen zum API-Schlüssel abgerufen werden können. Bei Azure Functions handelt es sich beim Schlüssel in der Regel um einen der Hostschlüssel, die in der Funktionen-App verschiedene Funktionen erfüllen.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Für die Verwendung von Azure AD benötigen Sie zwei Azure AD-Anwendungsregistrierungen: eine für die API selbst und eine für den benutzerdefinierten Connector:

- Verwenden Sie zum Konfigurieren der Registrierung für die API die Funktion [App Service-Authentifizierung/-Autorisierung](../app-service/configure-authentication-provider-aad.md).

- Um die Registrierung für den Connector zu konfigurieren, befolgen Sie die Schritte unter [Hinzufügen einer Anwendung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Für die Registrierung muss ein delegierter Zugriff auf Ihre API und die Antwort-URL `https://msmanaged-na.consent.azure-apim.net/redirect` vorhanden sein. 

Weitere Informationen finden Sie in den Azure AD-Registrierungsbeispielen für [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) und [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). In diesen Beispielen wird Azure Resource Manager als API verwendet. Ersetzen Sie sie bei der Durchführung der Schritte durch Ihre API.

Die folgenden Konfigurationswerte sind erforderlich:
- **Client-ID** – Die Client-ID der Azure AD-Registrierung Ihres Connectors
- **Geheimer Clientschlüssel** – Der geheime Clientschlüssel der Azure AD-Registrierung Ihres Connectors
- **Anmelde-URL** – Die Basis-URL für Azure AD. In Azure lautet diese in der Regel `https://login.windows.net`.
- **Mandanten-ID** – Die für die Anmeldung verwendete ID des Mandanten. Diese sollte allgemein oder die ID des Mandanten sein, in dem der Connector erstellt wird.
- **Ressourcen-URL** – Die Ressourcen-URL der Azure AD-Registrierung Ihrer API

> [!IMPORTANT]
> Falls die API-Definition im Rahmen des manuellen Flusses von einer anderen Person in PowerApps und Microsoft Flow importiert wird, stellen Sie ihr die Client-ID und den geheimen Clientschlüssel der *Connectorregistrierung* sowie die Ressourcen-URL Ihrer API bereit. Achten Sie auf eine sichere Verwaltung dieser Geheimnisse. **Geben Sie die Sicherheitsanmeldeinformationen der API nicht weiter.**

### <a name="generic-oauth-20"></a>Generisches OAuth 2.0
Wenn Sie das generische OAuth 2.0-Protokoll verwenden, können Sie Integrationen in einen beliebigen OAuth 2.0-Anbieter durchführen. So können Sie auch mit benutzerdefinierten Anbietern arbeiten, die nicht nativ unterstützt werden.

Die folgenden Konfigurationswerte sind erforderlich:
- **Client-ID** – Client-ID von OAuth 2.0
- **Geheimer Clientschlüssel** – Der geheime OAuth 2.0-Clientschlüssel
- **Autorisierungs-URL** – Die OAuth 2.0-Autorisierungs-URL
- **Token-URL** – Die OAuth 2.0-Token-URL
- **Aktualisierungs-URL** – Die OAuth 2.0-Aktualisierungs-URL


