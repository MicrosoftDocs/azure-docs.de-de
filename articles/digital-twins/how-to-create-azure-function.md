---
title: Einrichten einer Azure-Funktion zum Verarbeiten von Daten
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie eine Azure-Funktion erstellen, die auf digitale Zwillinge zugreifen und von diesen ausgelöst werden kann.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5352a95b865851be937af7b9f19268afd23148db
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280023"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Verbinden von Azure Functions-Apps für die Verarbeitung von Daten

Die Aktualisierung von digitalen Zwillingen wird auf der Grundlage von Daten unter Verwendung von [**Ereignisrouten**](concepts-route-events.md) über Computeressourcen, z. B. [Azure Functions](../azure-functions/functions-overview.md), behandelt. Eine Azure-Funktion könnte verwendet werden, um als Reaktion darauf einen digitalen Zwilling zu aktualisieren:
* Von IoT Hub stammende Gerätetelemetriedaten
* Änderung von Eigenschaften oder anderen Daten, die von einem anderen digitalen Zwilling innerhalb des Zwillingsgraphen stammen

Dieser Artikel führt Sie durch die Erstellung einer Azure-Funktion zur Verwendung mit Azure Digital Twins. 

Hier finden Sie eine Übersicht über die darin enthaltenen Schritte:

1. Erstellen einer Azure Functions-App in Visual Studio
2. Schreiben einer Azure-Funktion mit einem [Event Grid](../event-grid/overview.md)-Trigger
3. Hinzufügen von Authentifizierungscode zur Funktion (für den Zugriff auf Azure Digital Twins)
4. Veröffentlichen der Funktions-App in Azure
5. Einrichten des [Sicherheitszugriffs](concepts-security.md) für die Azure-Funktions-App

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Voraussetzung: Einrichten einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Erstellen einer Azure Functions-App in Visual Studio

Wählen Sie in Visual Studio 2019 _Datei > Neu > Projekt_ aus, und suchen Sie nach der Vorlage _Azure Functions_. Anschließend wählen Sie _Weiter_ aus.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: Dialogfeld „Neues Projekt“":::

Geben Sie einen Namen für die Funktions-App an, und wählen Sie _Erstellen_ aus.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: Neues Projekt konfigurieren":::

Wählen Sie den Typ der Funktions-App *Event Grid-Trigger* und dann _Erstellen_ aus.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: Dialogfeld für den Projekttrigger der Azure-Funktion":::

Nachdem Ihre Funktions-App erstellt wurde, verfügt Ihr Visual Studio über ein automatisch aufgefülltes Codebeispiel in der Datei **function.cs** in Ihrem Projektordner. Diese kurze Azure-Funktion wird zur Protokollierung von Ereignissen verwendet.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: Projektfenster mit Beispielcode":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Schreiben einer Azure-Funktion mit einem Event Grid-Trigger

Sie können eine Azure-Funktion schreiben, indem Sie das SDK zu Ihrer Funktions-App hinzufügen. Die Funktions-App interagiert mit Azure Digital Twins unter Verwendung des [Azure Digital Twins-SDKs für .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Wenn Sie das SDK verwenden möchten, müssen Sie die folgenden Pakete in Ihr Projekt einbinden. Sie können die Pakete entweder mit dem NuGet-Paket-Manager von Visual Studio installieren oder die Pakete mit dem Befehlszeilentool `dotnet` hinzufügen. Wählen Sie eine der folgenden Methoden aus: 

**Option 1. Pakete mithilfe von Visual Studio-Paket-Manager hinzufügen:**
    
Dazu können Sie mit der rechten Maustaste auf das Projekt klicken und in der Liste _NuGet-Pakete verwalten_ auswählen. Wählen Sie dann in dem sich öffnenden Fenster die Registerkarte _Durchsuchen_ aus und suchen Sie nach den folgenden Paketen. Wählen Sie _Installieren_ aus, und _akzeptieren_ Sie die Lizenzvereinbarung zur Installation der Pakete.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Damit die Konfiguration der Azure SDK-Pipeline für Azure Functions ordnungsgemäß eingerichtet werden kann, benötigen Sie auch die folgenden Pakete. Wiederholen Sie denselben Vorgang wie oben, um alle Pakete zu installieren.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Option 2. Hinzufügen von Paketen mit dem `dotnet`Befehlszeilentool:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Öffnen Sie als nächstes in Ihrem Visual Studio-Projektmappen-Explorer die Datei _function.cs_ , die Beispielcode enthält, und fügen Sie die folgenden  _using_ -Anweisungen zu Ihrer Azure-Funktion hinzu. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Hinzufügen von Authentifizierungscode zur Azure-Funktion

Sie werden jetzt Variablen auf Klassenebene deklarieren und einen Authentifizierungscode hinzufügen, der der Funktion den Zugriff auf Azure Digital Twins ermöglicht. Sie fügen Ihrer Azure-Funktion in der Datei „{Ihr Funktionsname}.cs“ Folgendes hinzu.

* Lesen Sie die ADT-Dienst-URL als eine Umgebungsvariable. Es ist eine bewährte Methode, die Dienst-URL aus einer Umgebungsvariablen zu lesen, anstatt für diese die Hartcodierung in der Funktion zu verwenden.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Eine statische Variable zum Aufnehmen einer HttpClient-Instanz Die Erstellung von HttpClient ist relativ aufwendig, und wir möchten vermeiden, dies für jeden Funktionsaufruf durchführen zu müssen.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Sie können die verwalteten Anmeldeinformationen für die Identität in der Azure-Funktion verwenden.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Fügen Sie die lokale Variable _DigitalTwinsClient_ innerhalb Ihrer Funktion hinzu, um Ihre Azure Digital Twins-Clientinstanz für das Funktionsprojekt aufzunehmen. Verwenden Sie hierfür *keine* statische Variable innerhalb Ihrer Klasse.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Fügen Sie eine NULL-Überprüfung für _adtInstanceUrl_ hinzu, und umschließen Sie Ihre Funktionslogik mit einem Try-Catch-Block, um alle Ausnahmen abzufangen.

Nach diesen Änderungen ähnelt Ihr Funktionscode dem folgenden:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Veröffentlichen der Funktions-App in Azure

Um die Funktions-App in Azure zu veröffentlichen, wählen Sie das Funktionsprojekt (nicht die Projektmappe) im Projektmappen-Explorer mit der rechten Maustaste aus, und wählen Sie dann **Publish** aus.

> [!IMPORTANT] 
> Für die Veröffentlichung einer Azure-Funktion fallen zusätzliche Gebühren für Ihr Abonnement an, unabhängig von Azure Digital Twins.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: Azure-Funktion „Publish“":::

Wählen Sie **Azure** als Veröffentlichungsziel und dann **Weiter** aus.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: Dialogfeld „Azure-Funktion veröffentlichen“, Azure auswählen":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: Dialogfeld „Azure-Funktion veröffentlichen“, Azure-Funktions-App auswählen (Windows) oder (Linux), je nach Ihrem Computer":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: Dialogfeld „Azure-Funktion veröffentlichen“, neue Azure-Funktion erstellen":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: Dialogfeld „Azure-Funktion veröffentlichen“, Felder ausfüllen und dann auf „Erstellen“ klicken":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: Dialogfeld „Azure-Funktion veröffentlichen“, Funktions-App aus der Liste auswählen und den Vorgang beenden":::

Geben Sie auf der folgenden Seite den gewünschten Namen für die neue App der Funktion, eine Ressourcengruppe und andere Details ein.
Damit Ihre Funktions-App auf Azure Digital Twins zugreifen kann, muss sie über eine vom System verwaltete Identität und Zugriffsrechte auf Ihre Azure Digital Twins-Instanz verfügen.

Als nächstes können Sie den Sicherheitszugriff für die Funktion mithilfe der Befehlszeilenschnittstelle oder über das Azure-Portal einrichten. Wählen Sie eine der folgenden Methoden aus:

## <a name="set-up-security-access-for-the-azure-function-app"></a>Einrichten des Sicherheitszugriffs für die Azure-Funktions-App
Mit einer dieser Optionen können Sie den Sicherheitszugriff für die Azure-Funktions-App einrichten:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>Option 1: Einrichten des Sicherheitszugriffs für die Azure-Funktions-App mithilfe der CLI

Das Azure-Funktionsgerüst aus früheren Beispielen erfordert, dass ihm ein Bearertoken übergeben wird, um sich bei Azure Digital Twins authentifizieren zu können. Um sicherzustellen, dass dieses Bearertoken übergeben wird, müssen Sie die [verwaltete Dienstidentität (MSI)](../active-directory/managed-identities-azure-resources/overview.md) für die Funktions-App einrichten. Dies muss nur einmal für jede Funktions-App durchgeführt werden.

Sie können eine systemseitig verwaltete Identität erstellen und die Identität der Funktions-App der Rolle _**Azure Digital Twins Data Owner**_ (Azure Digital Twins-Datenbesitzer) für Ihre Azure Digital Twins-Instanz zuweisen. Dadurch erhält die Funktions-App die Berechtigung in der Instanz zum Ausführen von Aktivitäten auf Datenebene. Dann gestalten Sie die URL Ihrer Azure Digital Twins-Instanz für Ihre Funktion zugänglich, indem Sie eine Umgebungsvariable festlegen.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

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
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>Option 2: Einrichten des Sicherheitszugriffs für die Azure-Funktions-App mithilfe des Azure-Portals

Azure-Ressourcen können über eine systemseitig zugewiesene verwaltete Identität bei Clouddiensten (z. B. Azure Key Vault) authentifiziert werden, ohne dass Anmeldeinformationen im Code gespeichert werden. Einmal aktiviert, können alle erforderlichen Berechtigungen über die rollenbasierte Zugriffskontrolle von Azure erteilt werden. Der Lebenszyklus dieser Art verwalteter Identitäten ist an den Lebenszyklus dieser Ressource gebunden. Zusätzlich kann jede Ressource (z. B. ein virtueller Computer) nur eine systemseitig zugewiesene verwaltete Identität aufweisen.

Suchen Sie im [Azure-Portal](https://portal.azure.com/) in der Suchleiste nach _Funktions-App_ mit dem Namen der Funktions-App, die Sie zuvor erstellt haben. Wählen Sie die *Funktions-App* aus der Liste aus. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure-Portal: Suchen der Funktions-App":::

Wählen Sie im Fenster der Funktions-App _Identität_ in der linken Navigationsleiste aus, um die verwaltete Identität zu aktivieren.
Wechseln Sie unter der Registerkarte _Vom System zugewiesen_ den _Status_ zu „Ein“ und _speichern_ Sie ihn dann. Es wird ein Popupfenster angezeigt, in dem Sie die _systemseitig zugewiesene verwaltete Identität aktivieren_.
Wählen Sie die Schaltfläche _Ja_ aus. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure-Portal: Systemseitig verwaltete Identität aktivieren":::

Sie können in den Benachrichtigungen überprüfen, ob Ihre Funktion erfolgreich in Azure Active Directory registriert wurde.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure-Portal: Benachrichtigungen":::

Beachten Sie auch die auf der Seite _Identität_ gezeigte **Objekt-ID** , da sie im nächsten Abschnitt verwendet wird.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Kopieren der zukünftig zu verwendenden Objekt-ID":::

### <a name="assign-access-roles-using-azure-portal"></a>Zuweisen von Zugriffsrollen mithilfe des Azure-Portals

Wählen Sie die Schaltfläche _Azure-Rollenzuweisungen_ aus, die die Seite mit den *Azure-Rollenzuweisungen* öffnet. Wählen Sie dann _+ Rollenzuweisung hinzufügen (Vorschau)_ aus.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure-Portal: Rollenzuweisung hinzufügen":::

Wählen Sie auf der sich öffnenden Seite _Rollenzuweisung hinzufügen (Vorschau)_ Folgendes aus:

* _Bereich:_ Ressourcengruppe
* _Abonnement_ : Wählen Sie Ihr Azure-Abonnement aus.
* _Ressourcengruppe_ : Wählen Sie Ihre Ressourcengruppe aus der Dropdownliste aus.
* _Rolle_ : Wählen Sie im Dropdownmenü _Azure Digital Twins Data Owner_ (Azure Digital Twins-Datenbesitzer) aus.

Speichern Sie dann Ihre Details, indem Sie auf die Schaltfläche _Speichern_ klicken.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure-Portal: Rollenzuweisung hinzufügen (Vorschau)":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurieren von Anwendungseinstellungen mithilfe des Azure-Portals

Sie können die URL Ihrer Azure Digital Twins-Instanz für Ihre Funktion zugänglich gestalten, indem Sie eine Umgebungsvariable festlegen. Weitere Informationen hierzu finden Sie unter [*Umgebungsvariablen*](/sandbox/functions-recipes/environment-variables). Anwendungseinstellungen werden als Umgebungsvariablen für den Zugriff auf die Instanz der digitalen Zwillinge verfügbar gemacht. 

Sie benötigen ADT_INSTANCE_URL, um eine Anwendungseinstellung zu erstellen.

Sie können ADT_INSTANCE_URL erhalten, indem Sie **_https://_** an den Hostnamen Ihrer Instanz anfügen. Im Azure-Portal können Sie den Hostnamen Ihrer digitalen Zwillingsinstanz finden, indem Sie in der Suchleiste nach Ihrer Instanz suchen. Wählen Sie dann _Übersicht_ in der linken Navigationsleiste aus, um den _Hostnamen_ anzuzeigen. Kopieren Sie diesen Wert, um eine Anwendung zu erstellen.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure-Portal: Übersicht -> Kopieren Sie den Hostnamen, um ihn im Feld „_Value_“ zu verwenden.":::

Sie können jetzt eine Anwendung gemäß den folgenden Schritten erstellen:

* Suchen Sie nach Ihrer Azure-Funktion mithilfe des Funktionsnamens in der Suchleiste, und wählen Sie die Funktion aus der Liste aus.
* Wählen Sie _Konfiguration_ in der Navigationsleiste links aus, um eine neue Anwendungseinstellung zu erstellen.
* Wählen Sie auf der Registerkarte _Anwendungseinstellungen_ die Option _+ Neue Anwendungseinstellung_ aus.

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure-Portal: Suchen nach vorhandener Azure-Funktion":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure-Portal: Konfigurieren von Anwendungseinstellungen":::

Verwenden Sie in dem sich öffnenden Fenster den oben kopierten Wert, um eine Anwendung zu erstellen. \
_Name_ : ADT_SERVICE_URL \
_Wert_ : https://{Ihr-Azure-Digital-Twins-Hostname}

Wählen Sie _OK_ aus, um eine Anwendungseinstellung zu erstellen.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure-Portal: Hinzufügen von Anwendungseinstellungen.":::

Sie können Ihre Anwendungseinstellungen mit Anwendungsnamen unter dem Feld _Name_ anzeigen. Speichern Sie dann Ihre Anwendungseinstellungen, indem Sie die Schaltfläche _Speichern_ auswählen.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure-Portal: Anzeigen der erstellten Anwendung und Neustarten der Anwendung":::

Alle Änderungen an den Anwendungseinstellungen erfordern einen Neustart der Anwendung. Wählen Sie _Fortsetzen_ aus, um Ihre Anwendung neu zu starten.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure-Portal: Speichern von Anwendungseinstellungen":::

Sie können sehen, dass die Anwendungseinstellungen aktualisiert werden, indem Sie das Symbol _Benachrichtigungen_ auswählen. Wenn Ihre Anwendungseinstellung nicht erstellt wurde, können Sie das Hinzufügen einer Anwendungseinstellung erneut versuchen, indem Sie dem obigen Prozess folgen.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure-Portal: Benachrichtigungen zum Aktualisieren von Anwendungseinstellungen":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die Schritte zum Einrichten einer Azure-Funktion für die Verwendung mit Azure Digital Twins befolgt. Als nächstes können Sie Ihre Azure-Funktion bei Event Grid abonnieren, um an einem Endpunkt zu lauschen. Für diesen Endpunkt könnte Folgendes gelten:
* Ein Event Grid-Endpunkt, der an Azure Digital Twins angefügt ist, um Nachrichten zu verarbeiten, die von Azure Digital Twins selbst stammen (z. B. Nachrichten zur Änderung von Eigenschaften, Telemetrienachrichten, die von [digitalen Zwillingen](concepts-twins-graph.md) im Zwillingsgraphen erzeugt werden, oder Lebenszyklusnachrichten).
* Die von IoT Hub zum Senden von Telemetrie- und anderen Geräteereignissen verwendeten IoT-Systemthemen.
* Ein Event Grid-Endpunkt, der Nachrichten von anderen Diensten empfängt.

Als nächstes erfahren Sie, wie Sie auf Ihrer grundlegenden Azure-Funktion aufbauen können, um IoT Hub-Daten in Azure Digital Twins zu erfassen:
* [*Verwenden Erfassen von Telemetriedaten aus IoT Hub*](how-to-ingest-iot-hub-data.md)