---
title: Einrichten einer Funktion in Azure zum Verarbeiten von Daten
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie eine Funktion in Azure erstellen, die auf digitale Zwillinge zugreifen und von diesen ausgelöst werden kann.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ecbf07c1a85c708d4117c83bca546cd8bd6ccd2b
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546073"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Verbinden von Funktions-Apps in Azure für die Verarbeitung von Daten

Die Aktualisierung von digitalen Zwillingen auf der Grundlage von Daten wird unter Verwendung von [**Ereignisrouten**](concepts-route-events.md) über Computeressourcen, z. B. eine mithilfe von [Azure Functions](../azure-functions/functions-overview.md) erstellte Funktion, durchgeführt. Azure Functions kann verwendet werden, um als Reaktion auf Folgendes einen digitalen Zwilling zu aktualisieren:
* Von IoT Hub stammende Gerätetelemetriedaten
* Änderung von Eigenschaften oder anderen Daten, die von einem anderen digitalen Zwilling innerhalb des Zwillingsgraphen stammen

Dieser Artikel führt Sie durch die Erstellung einer Funktion in Azure zur Verwendung mit Azure Digital Twins. 

Hier finden Sie eine Übersicht über die darin enthaltenen Schritte:

1. Erstellen eines Azure Functions-Projekts in Visual Studio
2. Schreiben Sie eine Funktion mit einem [Event Grid](../event-grid/overview.md)-Trigger.
3. Hinzufügen von Authentifizierungscode zur Funktion (für den Zugriff auf Azure Digital Twins)
4. Veröffentlichen der Funktions-App in Azure
5. Richten Sie [Sicherheitszugriff](concepts-security.md) für die Funktions-App ein.

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Voraussetzung: Einrichten einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Erstellen einer Funktions-App in Visual Studio

Wählen Sie in Visual Studio 2019 _Datei > Neu > Projekt_ aus, und suchen Sie nach der Vorlage _Azure Functions_. Wählen Sie _Weiter_ aus.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Screenshot von Visual Studio mit dem Dialogfeld „Neues Projekt“ mit hervorgehobener Azure Functions-Projektvorlage":::

Geben Sie einen Namen für die Funktions-App an, und wählen Sie _Erstellen_ aus.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Screenshot von Visual Studio mit dem Dialogfeld zum Konfigurieren eines neues Projekts, einschließlich des Projektnamens, des Speicherorts, der Auswahl zum Erstellen einer neuen Projektmappe und des Projektmappennamens":::

Wählen Sie den Typ der Funktions-App *Event Grid-Trigger* und dann _Erstellen_ aus.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Screenshot von Visual Studio mit dem Dialogfeld zum Erstellen einer neuen Azure Functions-Anwendung mit hervorgehobener Event Grid-Triggeroption":::

Nachdem Ihre Funktions-App erstellt wurde, generiert Visual Studio ein Codebeispiel in der Datei **Function1.cs** in Ihrem Projektordner. Diese kurze Funktion wird zur Protokollierung von Ereignissen verwendet.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Screenshot von Visual Studio im Projektfenster für das neue Projekt, das erstellt wurde, mit einem Code für eine Beispielfunktion namens „Function1“" lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Schreiben einer Funktion mit einem Event Grid-Trigger

Sie können eine Funktion schreiben, indem Sie das SDK zu Ihrer Funktions-App hinzufügen. Die Funktions-App interagiert mit Azure Digital Twins unter Verwendung des [Azure Digital Twins-SDKs für .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Wenn Sie das SDK verwenden möchten, müssen Sie die folgenden Pakete in Ihr Projekt einbinden. Sie können die Pakete entweder mit dem NuGet-Paket-Manager von Visual Studio installieren oder die Pakete mithilfe von `dotnet` in einem Befehlszeilentool hinzufügen. Führen Sie die folgenden Schritte für Ihre bevorzugte Methode aus.

**Option 1. Pakete mithilfe von Visual Studio-Paket-Manager hinzufügen:**
    
Wählen Sie mit der rechten Maustaste das Projekt und in der Liste _NuGet-Pakete verwalten_ aus. Wählen Sie dann in dem sich öffnenden Fenster die Registerkarte _Durchsuchen_ aus, und suchen Sie nach den folgenden Paketen. Wählen Sie _Installieren_ aus, und _akzeptieren_ Sie die Lizenzvereinbarung zur Installation der Pakete.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Option 2. Hinzufügen von Paketen mit dem `dotnet`Befehlszeilentool:**

Alternativ können Sie die folgenden `dotnet add`-Befehle in einem Befehlszeilentool verwenden:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Öffnen Sie als Nächstes in Ihrem Visual Studio-Projektmappen-Explorer die Datei _Function1.cs_, die Beispielcode enthält, und fügen Sie die folgenden `using`-Anweisungen zu Ihrer Funktion hinzu. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Hinzufügen von Authentifizierungscode zur Funktion

Sie werden jetzt Variablen auf Klassenebene deklarieren und einen Authentifizierungscode hinzufügen, der der Funktion den Zugriff auf Azure Digital Twins ermöglicht. Sie fügen Ihrer Funktion in der Datei _Function1.cs_ Folgendes hinzu.

* Code zum Lesen der Dienst-URL für Azure Digital Twins als Umgebungsvariable. Es ist eine bewährte Methode, die Dienst-URL aus einer Umgebungsvariablen zu lesen, anstatt für diese die Hartcodierung in der Funktion zu verwenden.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Eine statische Variable zum Aufnehmen einer HttpClient-Instanz Die Erstellung von HttpClient ist relativ aufwendig, und wir möchten vermeiden, dies für jeden Funktionsaufruf durchführen zu müssen.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Sie können die Anmeldeinformationen für die verwaltete Identität in Azure Functions verwenden.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Fügen Sie die lokale Variable _DigitalTwinsClient_ innerhalb Ihrer Funktion hinzu, um Ihre Azure Digital Twins-Clientinstanz aufzunehmen. Verwenden Sie hierfür *keine* statische Variable innerhalb Ihrer Klasse.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Fügen Sie eine NULL-Überprüfung für _adtInstanceUrl_ hinzu, und umschließen Sie Ihre Funktionslogik mit einem Try-Catch-Block, um alle Ausnahmen abzufangen.

Nach diesen Änderungen ähnelt Ihr Funktionscode dem folgenden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Nachdem Sie Ihre Anwendung geschrieben haben, können Sie sie mithilfe der Schritte im nächsten Abschnitt in Azure veröffentlichen.

## <a name="publish-the-function-app-to-azure"></a>Veröffentlichen der Funktions-App in Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>Einrichten des Sicherheitszugriffs für die Funktions-App

Sie können den Sicherheitszugriff für die Funktions-App mithilfe der Azure CLI oder des Azure-Portals einrichten. Führen Sie die folgenden Schritte für Ihre bevorzugte Option aus.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Option 1: Einrichten des Sicherheitszugriffs für die Funktions-App mithilfe der CLI

Das Funktionsgerüst aus früheren Beispielen erfordert, dass ihm ein Bearertoken übergeben wird, um sich bei Azure Digital Twins authentifizieren zu können. Um sicherzustellen, dass dieses Bearertoken übergeben wird, müssen Sie die [verwaltete Dienstidentität (MSI)](../active-directory/managed-identities-azure-resources/overview.md) für die Funktions-App einrichten. Dies muss nur einmal für jede Funktions-App durchgeführt werden.

Sie können eine systemseitig verwaltete Identität erstellen und die Identität der Funktions-App der Rolle _**Azure Digital Twins Data Owner**_ (Azure Digital Twins-Datenbesitzer) für Ihre Azure Digital Twins-Instanz zuweisen. Dadurch erhält die Funktions-App die Berechtigung in der Instanz zum Ausführen von Aktivitäten auf Datenebene. Dann gestalten Sie die URL Ihrer Azure Digital Twins-Instanz für Ihre Funktion zugänglich, indem Sie eine Umgebungsvariable festlegen.

Verwenden Sie [Azure Cloud Shell](https://shell.azure.com), um die Befehle auszuführen.

Verwenden Sie den folgenden Befehl, um die vom System verwaltete Identität zu erstellen. Beachten Sie in der Ausgabe das Feld _principalId_.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Verwenden Sie den Wert _principalId_ im folgenden Befehl, um die Identität der Funktions-App der Rolle _Azure Digital Twins Data Owner_ (Azure Digital Twins-Datenbesitzer) für Ihre Azure Digital Twins-Instanz zuzuweisen.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Zuletzt können Sie die URL Ihrer Azure Digital Twins-Instanz für Ihre Funktion zugänglich gestalten, indem Sie eine Umgebungsvariable festlegen. Weitere Informationen zum Festlegen von Umgebungsvariablen finden Sie unter [*Umgebungsvariablen*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Die URL der Azure Digital Twins-Instanz wird durch Hinzufügen von *https://* an den Anfang des *Hostnamens* der Azure Digital Twins-Instanz erstellt. Um den Hostnamen mit allen Eigenschaften der Instanz anzuzeigen, können Sie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` ausführen.

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Option 2: Einrichten des Sicherheitszugriffs für die Funktions-App mithilfe des Azure-Portals

Azure-Ressourcen können über eine systemseitig zugewiesene verwaltete Identität bei Clouddiensten (z. B. Azure Key Vault) authentifiziert werden, ohne dass Anmeldeinformationen im Code gespeichert werden. Einmal aktiviert, können alle erforderlichen Berechtigungen über die rollenbasierte Zugriffskontrolle von Azure erteilt werden. Der Lebenszyklus dieser Art verwalteter Identitäten ist an den Lebenszyklus dieser Ressource gebunden. Zusätzlich kann jede Ressource (z. B. ein virtueller Computer) nur eine systemseitig zugewiesene verwaltete Identität aufweisen.

Suchen Sie im [Azure-Portal](https://portal.azure.com/) in der Suchleiste nach _Funktions-App_ mit dem Namen der Funktions-App, die Sie zuvor erstellt haben. Wählen Sie die *Funktions-App* aus der Liste aus. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot des Azure-Portals: Der Name der Funktions-App wird in der Suchleiste des Portals gesucht, und das Suchergebnis wird hervorgehoben.":::

Wählen Sie im Fenster der Funktions-App _Identität_ in der linken Navigationsleiste aus, um die verwaltete Identität zu aktivieren.
Wechseln Sie unter der Registerkarte _Vom System zugewiesen_ den _Status_ zu „Ein“ und _speichern_ Sie ihn dann. Es wird ein Popupfenster angezeigt, in dem Sie die _systemseitig zugewiesene verwaltete Identität aktivieren_.
Wählen Sie die Schaltfläche _Ja_ aus. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Screenshot des Azure-Portals: Auf der Identitätsseite der Funktions-App ist die Option zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf „Ja“ festgelegt. Die Statusoption ist auf „Ein“ festgelegt.":::

Sie können in den Benachrichtigungen überprüfen, ob Ihre Funktion erfolgreich in Azure Active Directory registriert wurde.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Screenshot des Azure-Portals: Die Benachrichtigungsliste, die über das glockenförmige Symbol in der oberen Leiste des Portals ausgewählt wurde. Eine Benachrichtigung wird angezeigt, dass der Benutzer die systemseitig zugewiesene verwaltete Identität aktiviert hat.":::

Beachten Sie auch die auf der Seite _Identität_ gezeigte **Objekt-ID**, da sie im nächsten Abschnitt verwendet wird.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Screenshot des Azure-Portals: Eine Hervorhebung um das Feld „Objekt-ID“ auf der Identitätsseite der Azure-Funktion":::

### <a name="assign-access-roles-using-azure-portal"></a>Zuweisen von Zugriffsrollen mithilfe des Azure-Portals

Wählen Sie die Schaltfläche _Azure-Rollenzuweisungen_ aus, die die Seite mit den *Azure-Rollenzuweisungen* öffnet. Wählen Sie dann _+ Rollenzuweisung hinzufügen (Vorschau)_ aus.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Screenshot des Azure-Portals: Eine Hervorhebung um die Schaltfläche „Azure-Rollenzuweisungen“ unter „Berechtigungen“ auf der Identitätsseite der Azure-Funktion":::

Wählen Sie auf der sich öffnenden Seite _Rollenzuweisung hinzufügen (Vorschau)_ Folgendes aus:

* _Bereich:_ Ressourcengruppe
* _Abonnement_: Wählen Sie Ihr Azure-Abonnement aus.
* _Ressourcengruppe_: Wählen Sie Ihre Ressourcengruppe aus der Dropdownliste aus.
* _Rolle_: Wählen Sie im Dropdownmenü _Azure Digital Twins Data Owner_ (Azure Digital Twins-Datenbesitzer) aus.

Speichern Sie dann Ihre Details, indem Sie auf die Schaltfläche _Speichern_ klicken.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Screenshot des Azure-Portals: Dialog zum Hinzufügen einer neuen Rollenzuweisung (Vorschau), einschließlich Felder für den Bereich, das Abonnement, die Ressourcengruppe und die Rolle":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurieren von Anwendungseinstellungen mithilfe des Azure-Portals

Sie können die URL Ihrer Azure Digital Twins-Instanz für Ihre Funktion zugänglich gestalten, indem Sie eine Umgebungsvariable festlegen. Weitere Informationen hierzu finden Sie unter [*Umgebungsvariablen*](/sandbox/functions-recipes/environment-variables). Anwendungseinstellungen werden als Umgebungsvariablen für den Zugriff auf die Instanz der digitalen Zwillinge verfügbar gemacht. 

Rufen Sie zunächst die URL ab, indem Sie den Hostnamen Ihrer Azure Digital Twins-Instanz suchen, um dann eine Umgebungsvariable mit der URL dieser Instanz festzulegen. Suchen Sie nach Ihrer Instanz in der Suchleiste des [Azure-Portals](https://portal.azure.com). Wählen Sie dann _Übersicht_ in der linken Navigationsleiste aus, um den _Hostnamen_ anzuzeigen. Kopieren Sie diesen Wert.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Screenshot des Azure-Portals: Hervorgehobener Wert für den Hostnamen auf der Übersichtsseite für die Instanz von Azure Digital Twins":::

Sie können jetzt eine Anwendung gemäß den folgenden Schritten erstellen:

1. Suchen Sie in der Suchleiste des Portals nach Ihrer Funktions-App, und wählen Sie sie aus den Ergebnissen aus.
1. Wählen Sie _Konfiguration_ in der Navigationsleiste links aus, um eine neue Anwendungseinstellung zu erstellen.
1. Wählen Sie auf der Registerkarte _Anwendungseinstellungen_ die Option _+ Neue Anwendungseinstellung_ aus.

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Screenshot des Azure-Portals: Der Name der Funktions-App wird in der Suchleiste des Portals gesucht, und das Suchergebnis wird hervorgehoben.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Screenshot des Azure-Portals: Hervorgehobene Schaltfläche zum Erstellen einer neuen Anwendungseinstellung auf der Konfigurationsseite für die Funktions-App":::

Verwenden Sie in dem sich öffnenden Fenster den oben kopierten Hostnamenwert, um eine Anwendung zu erstellen.
* **Name**: ADT_SERVICE_URL
* **Value**: https://{your-azure-digital-twins-host-name}

Wählen Sie _OK_ aus, um eine Anwendungseinstellung zu erstellen.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Screenshot des Azure-Portals: Hervorgehobene „OK“-Schaltfläche nach Ausfüllen der Felder „Name“ und „Value“ auf der Seite „Hinzufügen/Bearbeiten“ der Anwendungseinstellung":::

Sie können Ihre Anwendungseinstellungen mit Anwendungsnamen unter dem Feld _Name_ anzeigen. Speichern Sie dann Ihre Anwendungseinstellungen, indem Sie die Schaltfläche _Speichern_ auswählen.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Screenshot des Azure-Portals: Hervorgehobene Anwendungseinstellungsseite mit neuer ADT_SERVICE_URL-Einstellung. Die Schaltfläche „Speichern“ ist ebenfalls hervorgehoben.":::

Alle Änderungen an den Anwendungseinstellungen erfordern einen Neustart der Anwendung, um wirksam zu werden. Wählen Sie _Fortsetzen_ aus, um Ihre Anwendung neu zu starten.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Screenshot des Azure-Portals: Benachrichtigung, dass für Änderungen an Anwendungseinstellungen ein Neustart Ihrer Anwendung vorgenommen wird. Die Schaltfläche „Weiter“ ist hervorgehoben.":::

Sie können sehen, dass die Anwendungseinstellungen aktualisiert werden, indem Sie das Symbol _Benachrichtigungen_ auswählen. Wenn Ihre Anwendungseinstellung nicht erstellt wurde, können Sie das Hinzufügen einer Anwendungseinstellung erneut versuchen, indem Sie dem obigen Prozess folgen.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Screenshot des Azure-Portals: Die Benachrichtigungsliste, die über das glockenförmige Symbol in der oberen Leiste des Portals ausgewählt wurde. Es wird eine Benachrichtigung angezeigt, dass die Web-App-Einstellungen erfolgreich aktualisiert wurden.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die Schritte zum Einrichten einer Funktions-App in Azure für die Verwendung mit Azure Digital Twins befolgt.

Als Nächstes erfahren Sie, wie Sie auf Ihrer grundlegenden Funktion aufbauen können, um IoT Hub-Daten in Azure Digital Twins zu erfassen:
* [*Verwenden Erfassen von Telemetriedaten aus IoT Hub*](how-to-ingest-iot-hub-data.md)
