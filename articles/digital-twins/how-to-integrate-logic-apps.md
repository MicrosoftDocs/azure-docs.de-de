---
title: Integration in Logic Apps
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Logic Apps mit Azure Digital Twins verbinden können, indem Sie einen benutzerdefinierten Connector verwenden.
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 54a96d1f3227cd4a66e344b63b2ecb337df31aba
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461072"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integration in Logic Apps mit einem benutzerdefinierten Connector

[Azure Logic Aapps](../logic-apps/logic-apps-overview.md) ist ein Clouddienst, der Sie bei der Automatisierung von Workflows in Apps und Diensten unterstützt. Indem Sie Logic Apps mit den Azure Digital Twins-APIs verbinden, können Sie solche automatisierten Flows um Azure Digital Twins und die zugehörigen Daten erstellen.

Azure Digital Twins verfügt zurzeit über keinen zertifizierten (vordefinierten) Connector für Logic Apps. Stattdessen besteht der aktuelle Prozess für die Verwendung von Logic Apps mit Azure Digital Twins darin, einen [**benutzerdefinierten Logic Apps-Connector**](../logic-apps/custom-connector-overview.md) zu erstellen, wobei ein [benutzerdefinierter Azure Digital Twins-Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) verwendet wird, der für die Zusammenarbeit mit Logic Apps geändert wurde.

> [!NOTE]
> Es gibt mehrere Versionen des Swagger für das im oben verknüpften benutzerdefinierten Swagger-Beispiel. Die neueste Version befindet sich im Unterordner mit dem aktuellsten Datum. Aber auch die im Beispiel enthaltenen früheren Versionen werden noch unterstützt.

In diesem Artikel verwenden Sie das [Azure-Portal](https://portal.azure.com), um einen **benutzerdefinierten Connector** zu erstellen, der zum Herstellen einer Verbindung zwischen Logic Apps und einer Azure Digital Twins-Instanz verwendet werden kann. Sie erstellen dann  **eine Logik-App** , die diese Verbindung für ein Beispielszenario verwendet, in dem durch einen Timer ausgelöste Ereignisse automatisch einen Zwilling in Ihrer Azure Digital Twins Instanz aktualisieren. 

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement haben, **erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** , bevor Sie beginnen.
Melden Sie sich mit diesem Konto am [Azure-Portal](https://portal.azure.com) an. 

Zu den Voraussetzungen zählen auch die folgenden Punkte. Im weiteren Verlauf dieses Abschnitts werden die folgenden Schritte erläutert:
- Einrichten einer Azure Digital Twins-Instanz
- Abrufen des geheimen Clientschlüssels für die Azure AD-App-Registrierung
- Hinzufügen eines digitalen Zwillings

### <a name="set-up-azure-digital-twins-instance"></a>Einrichten einer Azure Digital Twins-Instanz

Um eine Instanz von Azure Digital Twins mit Logic Apps in diesem Artikel zu verbinden, müssen Sie die **Azure Digital Twins-Instanz** bereits eingerichtet haben. 

Richten Sie zunächst eine **Azure Digital Twins-Instanz** und die erforderliche Authentifizierung ein, um damit arbeiten zu können. Anweisungen hierzu finden Sie unter [*Vorgehensweise: Einrichten einer Instanz und der Authentifizierung*](how-to-set-up-instance-portal.md). Abhängig von Ihrer bevorzugten Umgebung wird der Setupartikel für das [Azure-Portal](how-to-set-up-instance-portal.md), die [CLI](how-to-set-up-instance-cli.md) oder das [automatisierte Cloud Shell-Bereitstellungsskriptbeispiel](how-to-set-up-instance-scripted.md) bereitgestellt. Alle Versionen der Anleitung enthalten auch Schritte zur Überprüfung, ob Sie jeden Schritt erfolgreich abgeschlossen haben und für die Nutzung Ihrer neuen Instanz bereit sind.
* Nach dem Einrichten der Azure Digital Twins-Instanz benötigen Sie den **_Hostnamen_** der Instanz ([im Azure-Portal suchen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Um den Connector zu authentifizieren, müssen Sie außerdem eine **App-Registrierung** einrichten. Führen Sie die Anweisungen unter [ *Erstellen einer App-Registrierung*](how-to-create-app-registration.md) aus, um diese einzurichten. 
* Wenn Sie über eine App-Registrierung verfügen, benötigen Sie die **_Anwendungs-ID (Client)_** und die **_Verzeichnis-ID (Mandant)_** ([im Azure-Portal suchen](how-to-create-app-registration.md#collect-client-id-and-tenant-id)) der Registrierung.

### <a name="get-app-registration-client-secret"></a>Abrufen des geheimen Clientschlüssels für die Azure AD-App-Registrierung

Außerdem müssen Sie einen **_geheimen Clientschlüssel_** für die Azure AD-App-Registrierung erstellen. Navigieren Sie zu diesem Zweck zur Seite [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) im Azure-Portal (Sie können diesen Link verwenden oder nach ihm in der Suchleiste des Portals suchen). Wählen Sie die Registrierung aus der Liste aus, die Sie im vorherigen Abschnitt erstellt haben, um die zugehörigen Details zu öffnen. 

Klicken Sie im Menü der Registrierung auf *Zertifikate und geheime Schlüssel* , und wählen Sie *+ Neuer geheimer Clientschlüssel* aus.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::.

Geben Sie die Werte ein, die Sie für die Beschreibung und den Ablauf wünschen, und wählen Sie *Hinzufügen* aus.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Vergewissern Sie sich nun, dass der geheime Clientschlüssel auf der Seite _Zertifikate & Geheimnisse_ mit den Feldern _Ablauf_ und _Wert_ angezeigt wird. Notieren Sie sich den _Wert_ für später. (Sie können ihn auch mithilfe des Kopiersymbols in die Zwischenablage kopieren.)

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

### <a name="add-a-digital-twin"></a>Hinzufügen eines digitalen Zwillings

In diesem Artikel wird Logic Apps verwendet, um einen Zwilling in Ihrer Azure Digital Twins-Instanz zu aktualisieren. Um fortzufahren, sollten Sie mindestens einen Zwilling in der Instanz hinzufügen. 

Sie können Zwillinge mit den [DigitalTwins-APIs](/rest/api/digital-twins/dataplane/twins), dem [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true) oder der [Azure Digital Twins-CLI](how-to-use-cli.md) hinzufügen. Ausführliche Schritte zum Erstellen von Zwillingen mithilfe dieser Methoden finden Sie unter [*Vorgehensweise: Verwalten digitaler Zwillinge*](how-to-manage-twin.md).

Sie benötigen die **_Zwillings-ID_** eines Zwillings in Ihrer Instanz, den Sie erstellt haben.

## <a name="create-custom-logic-apps-connector"></a>Erstellen eines benutzerdefinierten Logic Apps-Connectors

In diesem Schritt erstellen Sie einen [benutzerdefinierten Logic Apps-Connector](../logic-apps/custom-connector-overview.md) für die Azure Digital Twins-APIs. Anschließend können Sie bei der Erstellung einer Logik-App im nächsten Abschnitt Azure Digital Twins einbinden.

Navigieren Sie zu diesem Zweck zur Seite [Benutzerdefinierter Logic Apps-Connector](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) im Azure-Portal (Sie können diesen Link verwenden oder nach ihm in der Suchleiste des Portals suchen). Klicken Sie auf *+ Hinzufügen* .

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Wählen Sie auf der Seite *Benutzerdefinierten Logic Apps-Connector erstellen* , die anschließend angezeigt wird, Ihr Abonnement und Ihre Ressourcengruppe sowie einen Namen und einen Bereitstellungsort für den neuen Connector aus. Klicke Sie auf *Überprüfen und erstellen* . 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Auf diese Weise gelangen Sie zur Registerkarte *Überprüfen und erstellen* , auf der Sie unten auf *Erstellen* klicken können, um die Ressource zu erstellen.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Sie gelangen zur Bereitstellungsseite für den Connector. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf die Schaltfläche *Zu Ressource wechseln* , um die Details des Connectors im Portal anzuzeigen.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurieren des Connectors für Azure Digital Twins

Als Nächstes konfigurieren Sie den von Ihnen erstellten Connector für die Verbindung mit Azure Digital Twins.

Laden Sie zunächst eine benutzerdefinierte Azure Digital Twins Swagger-Datei herunter, die geändert wurde, um mit Logic Apps zusammenzuarbeiten. Laden Sie das Beispiel **Benutzerdefinierte Azure Digital Twins-Swagger (Logic Apps-Connector)** über [**diesen Link**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) herunter, indem Sie auf die Schaltfläche *ZIP herunterladen* klicken. Navigieren Sie zum heruntergeladenen Ordner *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* , und entzippen Sie ihn. 

Der benutzerdefinierte Swagger für dieses Tutorial befindet sich im Ordner _**Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps **_. Dieser Ordner enthält die Unterordner *stable* und *preview* , die jeweils verschiedene Versionen des Swagger nach Datum geordnet enthalten. Der Ordner mit dem aktuellen Datum enthält die neueste Kopie des Swagger. Unabhängig von der ausgewählten Version hat die Swagger-Datei die Bezeichnung „_** digitaltwins.json**_“.

> [!NOTE]
> Sofern Sie keine Previewfunktion verwenden, empfiehlt sich im Allgemeinen die Verwendung der neuesten Swagger-Version unter *stable* . Frühere Versionen und Vorschauversionen des Swagger werden jedoch ebenfalls unterstützt. 

Wechseln Sie dann zur Übersichtsseite Ihres Connector im [Azure-Portal](https://portal.azure.com), und wählen Sie *Bearbeiten* aus.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Konfigurieren Sie auf der Seite *Benutzerdefinierten Logic Apps-Connector bearbeiten* , die anschließend angezeigt wird, die folgenden Informationen:
* **Benutzerdefinierte Connectors**
    - API-Endpunkt: REST (Standardwert beibehalten)
    - Importmodus: OpenAPI-Datei (Standardwert beibehalten)
    - Datei: Dies ist die benutzerdefinierte Swagger-Datei, die Sie zuvor heruntergeladen haben. Klicken Sie auf *Importieren* , suchen Sie die Datei auf Ihrem Computer ( *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\...\digitaltwins.json* ), und klicken Sie auf *Öffnen* .
* **Allgemeine Informationen**
    - Symbol: Laden Sie ein Symbol hoch, das Ihnen gefällt.
    - Symbolhintergrundfarbe: Geben Sie für Ihre Farbe einen Hexadezimalcode im Format „#xxxxxx“ ein.
    - Beschreibung: Geben Sie beliebige gewünschte Werte ein.
    - Schema: HTTPS (Standardwert beibehalten)
    - Host: Der *Hostname* Ihrer Azure Digital Twins-Instanz.
    - Basis-URL: / (Standardwert beibehalten)

Klicken Sie dann unten im Fenster auf die Schaltfläche *Sicherheit* , um mit dem nächsten Konfigurationsschritt fortzufahren.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Klicken Sie im Schritt „Sicherheit“ auf *Bearbeiten* , und konfigurieren Sie die folgenden Informationen:
* **Authentifizierungstyp:** OAuth 2.0
* **OAuth 2.0** :
    - Identitätsanbieter: Azure Active Directory
    - Client-ID: Die *Anwendungs-ID (Client)* für Ihre Azure AD-App-Registrierung.
    - Geheimer Clientschlüssel: Der *geheime Clientschlüssel* , den Sie in [*Voraussetzungen*](#prerequisites) für Ihre Azure AD-App-Registrierung erstellt haben.
    - Anmelde-URL: https://login.windows.net (Standardwert beibehalten)
    - Mandanten-ID: Die *Verzeichnis-ID (Mandant)* für Ihre Azure-AD-App-Registrierung.
    - Ressourcen-URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Bereich: Directory.AccessAsUser.All
    - Umleitungs-URL: (Standardeinstellung vorerst beibehalten)

Beachten Sie, dass das Feld „Umleitungs-URL“ den Text *Benutzerdefinierten Connector speichern, um die Umleitungs-URL zu generieren* enthält. Starten Sie diesen Vorgang jetzt, indem Sie auf *Connector aktualisieren* oben im Bereich klicken, um Ihre Connectoreinstellungen zu bestätigen.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

<!-- Success message? didn't see one -->

Kehren Sie zum Feld „Umleitungs-URL“ zurück, und kopieren Sie den generierten Wert. Sie werden ihn im nächsten Schritt benötigen.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Dies sind alle erforderlichen Informationen zum Erstellen Ihres Connectors (es ist nicht erforderlich, nach „Sicherheit“ mit dem Definitionsschritt fortzufahren). Sie können den Bereich *Benutzerdefinierten Logic Apps -Connector bearbeiten* schließen.

>[!NOTE]
>Beachten Sie zurück auf der Übersichtsseite Ihres Connectors, auf der Sie ursprünglich auf *Bearbeiten* geklickt haben, dass durch erneutes Klicken auf *Bearbeiten* der gesamte Prozess der Eingabe Ihrer Konfigurationsoptionen neu gestartet wird. Wenn Sie also eine aktualisierte Konfiguration mit geänderten Werten speichern möchten, müssen Sie auch alle anderen Werte erneut eingeben, um zu verhindern, dass sie durch die Standardwerte überschrieben werden.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Erteilen von Berechtigungen in der Azure AD-App für den Connector

Verwenden Sie nun die *Umleitungs-URL* des benutzerdefinierten Connector Wert, die Sie im letzten Schritt kopiert haben, um den Connector Berechtigungen in der Registrierung der Azure AD-App zu erteilen.

Navigieren Sie im Azure-Portal zur Seite [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), und wählen Sie Ihre Registrierung aus der Liste aus. 

Fügen Sie im Menü der Registrierung unter *Authentifizierungs* einen URI hinzu.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“"::: 

Geben Sie die *Umleitungs-URL* des benutzerdefinierten Connectors in das neue Feld ein, und klicken Sie auf das Symbol *Speichern* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Sie sind jetzt fertig mit der Einrichtung eines benutzerdefinierten Connectors, der auf die Azure Digital Twins-APIs zugreifen kann. 

## <a name="create-logic-app"></a>Erstellen einer Logik-App

Nun erstellen Sie eine Logik-App, die Ihren neuen Connector zum Automatisieren von Azure Digital Twins-Updates verwendet.

Suchen Sie im [Azure-Portal](https://portal.azure.com) über die Suchleiste nach *Logik-Apps* . Durch Auswählen dieser Option gelangen Sie zur Seite *Logic-Apps* . Klicken Sie auf die Schaltfläche *Logik-App erstellen* , um eine neue Logik-App zu erstellen.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Geben Sie auf der anschließend angezeigten Seite *Logik-App* Ihr Abonnement und Ihre Ressourcengruppe ein. Wählen Sie außerdem einen Namen für Ihre Logik-App sowie den Bereitstellungsstandort aus.

Klicken Sie auf die Schaltfläche _Überprüfen und erstellen_ .

Daraufhin gelangen Sie zur Registerkarte *Überprüfen und erstellen* , auf der Sie Ihre Angaben überprüfen und unten auf *Erstellen* klicken können, um die Ressource zu erstellen.

Sie gelangen zur Bereitstellungsseite für die Logik-App. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf die Schaltfläche *Zu Ressource wechseln* , um mit dem *Designer für Logik-Apps* fortzufahren, in dem Sie die Logik des Workflows angeben.

### <a name="design-workflow"></a>Entwerfen des Workflows

Wählen Sie im *Designer für Logik-Apps* unter *Starten Sie mit einem gängigen Trigger* die Option _**Wiederholung**_ aus.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Ändern Sie auf der Seite *Designer für Logik-Apps* , die anschließend angezeigt wird, die Häufigkeit der **Wiederholung** in *Sekunde* , sodass das Ereignis alle 3 Sekunden ausgelöst wird. Auf diese Weise können die Ergebnisse später leicht eingesehen werden, ohne sehr lange warten zu müssen.

Klicken Sie auf *+ Neuen Schritt hinzufügen* .

Daraufhin wird ein Feld zum *Auswählen einer Aktion* geöffnet. Wechseln Sie zur Registerkarte *Benutzerdefiniert* . Der zuvor erstellte benutzerdefinierte Connector sollte im oberen Feld angezeigt werden.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

Wählen Sie diese Option aus, um die Liste der in diesem Connector enthaltenen APIs anzuzeigen. Verwenden Sie die Suchleiste, oder scrollen Sie durch die Liste, um **DigitalTwins_Add** auszuwählen. (Dies ist die API, die in diesem Artikel verwendet wird, aber Sie können auch eine beliebige andere API als gültige Option für eine Logic Apps-Verbindung auswählen).

Sie werden möglicherweise aufgefordert, sich mit ihren Azure-Anmeldeinformationen anzumelden, um eine Verbindung mit dem Connector herzustellen. Wenn ein Dialogfeld *Angeforderte Berechtigungen* angezeigt wird, folgen Sie den Eingabeaufforderungen, um die Einwilligung für Ihre App zu erteilen und zu akzeptieren.

Füllen Sie die Felder im neuen Feld *DigitalTwinsAdd* wie folgt aus:
* _id:_ Geben Sie die *Zwillings-ID* des digitalen Zwillings in Ihrer Instanz an, die die Logik-App aktualisieren soll.
* _twin:_ In dieses Feld geben Sie den Text ein, der für die ausgewählte API-Anforderung erforderlich ist. Für *DigitalTwinsUpdate* ist dieser Text JSON-Patchcode. Weitere Informationen zum Strukturieren eines JSON-Patches zum Aktualisieren Ihres Zwillings finden Sie im Abschnitt [Aktualisieren eines digitalen Zwillings](how-to-manage-twin.md#update-a-digital-twin) von *Vorgehensweise: Verwalten digitaler Zwillinge* .
* _api-version:_ Die neueste API-Version. Derzeit ist dieser Wert *2020-10-31* .

Klicken Sie im Designer für Logik-Apps auf *Speichern* .

Sie können weitere Vorgänge auswählen, indem Sie im gleichen Fenster _+ Neuer Schritt_ auswählen.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Portalansicht einer Azure AD-App-Registrierung. Im Ressourcenmenü gibt es eine Hervorhebung von „Zertifikate und geheime Schlüssel“ und eine Hervorhebung auf der Seite von „Neuer geheimer Clientschlüssel“":::

## <a name="query-twin-to-see-the-update"></a>Abfragen des Zwillings, um das Update anzuzeigen

Nachdem Ihre Logik-App erstellt wurde, sollte das im Designer für Logik-Apps definierte Zwillingsaktualisierungsereignis alle drei Sekunden auftreten. Dies bedeutet, dass Sie nach drei Sekunden in der Lage sein sollten, Ihren Zwilling abzufragen und die neuen gepatchten Werte anzuzeigen.

Sie können Ihren Zwilling mit der Methode Ihrer Wahl abfragen (z. B. mit einer [benutzerdefinierten Client-App](tutorial-command-line-app.md), der [Azure Digital Twins Explorer-Beispielanwendung](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), den [SDKs und APIs](how-to-use-apis-sdks.md) oder der [CLI](how-to-use-cli.md)). 

Weitere Informationen zum Abfragen ihrer Azure Digital Twins-Instanz finden Sie unter [*Vorgehensweise: Abfragen des Zwillingsdiagramms*](how-to-query-graph.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Logik-App erstellt, die regelmäßig einen Zwilling in Ihrer Azure Digital Twins-Instanz mit einem von Ihnen bereitgestellten Patch aktualisiert. Sie können versuchen, andere APIs im benutzerdefinierten Connector auszuwählen, um Logik-Apps für eine Vielzahl von Aktionen für Ihre Instanz zu erstellen.

Weitere Informationen zu den verfügbaren API-Vorgängen und den dazu erforderlichen Details finden Sie unter [*Vorgehensweise: Verwenden der Azure Digital Twins-APIs und SDKs*](how-to-use-apis-sdks.md).