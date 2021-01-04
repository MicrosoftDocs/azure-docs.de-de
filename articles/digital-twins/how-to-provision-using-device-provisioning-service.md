---
title: Automatisches Verwalten von Geräten mit DPS
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie einen automatisierten Prozess für die Bereitstellung und Außerbetriebnahme von IoT-Geräten in Azure Digital Twins mit dem Device Provisioning Service (DPS) einrichten.
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0a18e6cef568afa8a0092fc06d8f6bb526739b2a
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145802"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Automatisches Verwalten von Geräten in Azure Digital Twins mithilfe des Device Provisioning Service (DPS)

In diesem Artikel erfahren Sie, wie Sie Azure Digital Twins in den [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) integrieren.

Mithilfe der in diesem Artikel beschriebenen Lösung können Sie den Prozess zum **_Bereitstellen_** und **_Außerbetriebnehmen_** von IoT Hub-Geräten in Azure Digital Twins mithilfe des Device Provisioning Service automatisieren. 

Weitere Informationen zu den Phasen der _Bereitstellung_ und _Außerbetriebnahme_ und zu den Phasen der allgemeinen Geräteverwaltung, die in allen Unternehmens-IoT-Projekten gelten, finden Sie im [Abschnitt *Lebenszyklus von Geräten*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) der Dokumentation zur IoT Hub-Geräteverwaltung.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Bereitstellung einrichten können, benötigen Sie eine **Instanz von Azure Digital Twins** , die Modelle und Zwillinge enthält. Diese Instanz sollte auch mit der Möglichkeit eingerichtet werden, Informationen zu digitalen Zwillingen auf der Grundlage von Daten zu aktualisieren. 

Wenn Sie dieses Setup nicht bereits eingerichtet haben, nutzen Sie zum Erstellen die Informationen im Azure Digital Twins-Tutorial [ *Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md). Das Tutorial führt Sie durch die Einrichtung einer Azure Digital Twins-Instanz mit Modellen und Zwillingen, einem verbundenen Azure [IoT Hub](../iot-hub/about-iot-hub.md) und mehreren [Azure-Funktionen](../azure-functions/functions-overview.md), um den Datenfluss weiterzuleiten.

In dieser Schnellstartanleitung benötigen Sie die folgenden Werte, die Sie bei der Instanzeinrichtung verwendet haben. Wenn Sie diese Werte erneut erfassen müssen, verwenden Sie die folgenden Links, um Anweisungen zu erhalten.
* Azure Digital Twins-Instanz: **_Hostname_** ( [im Portal suchen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Verbindungszeichenfolge für Azure Event Hubs **_Verbindungszeichenfolge_** ( [im Portal suchen](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

In diesem Beispiel wird auch ein **Gerätesimulator** verwendet, der die Bereitstellung mithilfe des Device Provisioning Service umfasst. Der Gerätesimulator befindet sich hier: [Beispiel für die Integration von Azure Digital Twins und IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Laden Sie das Beispielprojekt auf Ihren Computer herunter, indem Sie zum Beispiellink navigieren und die Schaltfläche *ZIP-Datei herunterladen* unter dem Titel auswählen. Extrahieren Sie den heruntergeladenen Ordner.

Der Gerätesimulator basiert auf **Node.js** -Version 10.0. x oder höher. Unter [*Prepare your development environment*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) wird beschrieben, wie Sie Node.js für dieses Tutorial unter Windows oder Linux installieren.

## <a name="solution-architecture"></a>Lösungsarchitektur

Die folgende Abbildung veranschaulicht die Architektur dieser Lösung mit Azure Digital Twins und dem Device Provisioning Service. Sie zeigt den Ablauf für die Bereitstellung und Außerbetriebnahme von Geräten.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Darstellung eines Geräts und mehrerer Azure-Dienste in einem End-to-End-Szenario. Daten werden zwischen einem Thermostat und DPS übermittelt. Außerdem werden Daten über eine Azure-Funktion mit der Bezeichnung „Zuweisung“ aus DPS zu IoT Hub und zu Azure Digital Twins übertragen. Daten aus einer manuellen Aktion zum Löschen des Geräts durchlaufen IoT Hub > Event Hubs > Azure Functions > Azure-Digital Twins.":::

Dieser Artikel ist in zwei Abschnitte unterteilt:
* [*Bereitstellen eines Geräts mithilfe des Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Automatisches Außerbetriebnehmen von Geräten mithilfe von IoT Hub-Lebenszyklusereignissen*](#auto-retire-device-using-iot-hub-lifecycle-events)

Ausführliche Erläuterungen zu den einzelnen Schritten in der Architektur finden Sie in den entsprechenden Abschnitten weiter unten in diesem Artikel.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Bereitstellen eines Geräts mithilfe des Device Provisioning Service

In diesem Abschnitt fügen Sie den Device Provisioning Service an Azure Digital Twins an, um Geräte automatisch über den unten beschriebenen Pfad bereitzustellen. Dies ist ein Auszug aus der [oben dargestellten](#solution-architecture) vollständigen Architektur.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Bereitstellungsfluss: Auszug aus dem Diagramm der Lösungsarchitektur mit nummerierten Abschnitten. Daten werden zwischen einem Thermostat und DPS übermittelt (1 für Gerät > DPS und 5 für DPS > Gerät). Außerdem werden Daten über eine Azure-Funktion mit der Bezeichnung „Zuweisung“ (2) aus DPS zu IoT Hub (4) und zu Azure Digital Twins (3) übertragen.":::

Beschreibung des Prozesses:
1. Das Gerät kontaktiert den DPS-Endpunkt und übergibt Informationen, um seine Identität zu bestätigen.
2. DPS überprüft die Geräteidentität, indem die Registrierungs-ID und der Schlüssel anhand der Registrierungsliste überprüft werden, und ruft eine [Azure-Funktion](../azure-functions/functions-overview.md) auf, um die Zuordnung durchzuführen.
3. Die Azure-Funktion erstellt in Azure Digital Twins einen neuen [Zwilling](concepts-twins-graph.md) für das Gerät.
4. DPS registriert das Gerät bei einem IoT-Hub und gibt den gewünschten Zwillingsstatus des Geräts an.
5. Der IoT-Hub gibt Informationen zur Geräte-ID und zur IoT-Hub-Verbindung an das Gerät zurück. Das Gerät kann nun eine Verbindung mit dem IoT-Hub herstellen.

In den folgenden Abschnitten werden die Schritte zum Einrichten dieses Ablaufs für die automatische Bereitstellung von Geräten erläutert.

### <a name="create-a-device-provisioning-service"></a>Erstellen einer Device Provisioning Service-Instanz

Wenn ein neues Gerät mit dem Device Provisioning Service bereitgestellt wird, kann ein neuer Zwilling für dieses Gerät in Azure Digital Twins erstellt werden.

Erstellen Sie eine Instanz des Device Provisioning Service, die zum Bereitstellen von IoT-Geräten verwendet wird. Sie können entweder die folgenden Azure CLI Anweisungen oder das Azure-Portal verwenden: [*Schnellstart: Einrichten des IoT Hub Device Provisioning Service über das Azure-Portal*](../iot-dps/quick-setup-auto-provision.md).

Mit dem folgenden Azure CLI Befehl wird ein Device Provisioning Service erstellt. Sie müssen einen Namen, eine Ressourcengruppe und eine Region angeben. Der Befehl kann in [Cloud Shell](https://shell.azure.com) oder lokal ausgeführt werden, wenn die Azure CLI [auf dem Computer installiert](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ist.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Erstellen einer Azure-Funktion

Als Nächstes erstellen Sie eine durch eine HTTP-Anforderung ausgelöste Funktion innerhalb einer Funktions-App. Sie können die im End-to-End-Tutorial ( [ *Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md)) erstellte Funktions-App oder Ihre eigene verwenden.

Diese Funktion wird vom Device Provisioning Service in einer [benutzerdefinierten Zuweisungsrichtlinie](../iot-dps/how-to-use-custom-allocation-policies.md) zur Bereitstellung eines neuen Geräts verwendet. Weitere Informationen zur Verwendung von HTTP-Anforderungen mit Azure-Funktionen finden Sie unter [*HTTP-Trigger in Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

Fügen Sie im Projekt der Funktions-App eine neue Funktion hinzu. Fügen Sie dem Projekt außerdem ein NuGet-Paket hinzu: `Microsoft.Azure.Devices.Provisioning.Service`.

Fügen Sie in der neu erstellten Funktionscodedatei den folgenden Code ein.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);

            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);

            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(dtId, twinProps);
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Speichern Sie die Datei, und veröffentlichen Sie die Funktions-App erneut. Anweisungen zum Veröffentlichen der Funktions-App finden Sie im Abschnitt [*Veröffentlichen der App*](tutorial-end-to-end.md#publish-the-app) im End-to-End-Tutorial.

### <a name="configure-your-function"></a>Konfigurieren Ihrer Funktion

Als Nächstes müssen Sie in ihrer Funktions-App von oben Umgebungsvariablen festlegen, die den Verweis auf die von Ihnen erstellte Azure Digital Twins-Instanz enthalten. Wenn Sie das End-to-End-Tutorial verwendet haben ( [*Tutorial: Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md)), ist die Einstellung bereits konfiguriert.

Fügen Sie die Einstellung mit folgendem Azure CLI-Befehl hinzu:

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Stellen Sie sicher, dass die Berechtigungen und die Rollenzuweisung für verwaltete Identitäten für die Funktions-App ordnungsgemäß konfiguriert wurden. Dies wird im End-to-End-Tutorial im Abschnitt [*Zuweisen von Berechtigungen zur Funktions-App*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) beschrieben.

### <a name="create-device-provisioning-enrollment"></a>Erstellen der Registrierung für den Device Provisioning Service

Als Nächstes müssen Sie eine Registrierung im Device Provisioning Service mithilfe einer **benutzerdefinierten Zuweisungsfunktion** erstellen. Befolgen Sie hierzu die Anweisungen in den Abschnitten [*Erstellen der Registrierung*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) und [*Ableiten eindeutiger Geräteschlüssel*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) im Artikel zu benutzerdefinierten Zuweisungsrichtlinien im Device Provisioning Service.

Beim Durchlaufen dieses Flows verknüpfen Sie die Registrierung mit der Funktion, die Sie gerade erstellt haben, indem Sie die Funktion im Schritt **Wählen Sie, wie Geräte den Hubs zugewiesen werden sollen** auswählen. Nachdem Sie die Registrierung erstellt haben, werden der Registrierungsname und der primäre oder sekundäre SAS-Schlüssel später in diesem Artikel zum Konfigurieren des Gerätesimulators verwendet.

### <a name="set-up-the-device-simulator"></a>Einrichten des Gerätesimulators

In diesem Beispiel wird ein Gerätesimulator verwendet, der die Bereitstellung mithilfe des Device Provisioning Service umfasst. Der Gerätesimulator befindet sich hier: [Beispiel für die Integration von Azure Digital Twins und IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Wenn Sie dies noch nicht getan haben, laden Sie das Beispielprojekt auf Ihren Computer herunter, indem Sie zum Beispiellink navigieren und die Schaltfläche *ZIP-Datei herunterladen* unter dem Titel auswählen. Extrahieren Sie den heruntergeladenen Ordner.

Öffnen Sie ein Befehlsfenster, und navigieren Sie zum heruntergeladenen Ordner und dann zum Verzeichnis *device-simulator*. Installieren Sie die Abhängigkeiten für das Projekt, indem Sie den folgenden Befehl ausführen:

```cmd
npm install
```

Kopieren Sie dann die Datei *.env.template* in eine neue Datei mit dem Namen *.env* , und geben Sie die folgenden Einstellungen ein:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Speichern und schließen Sie die Datei.

### <a name="start-running-the-device-simulator"></a>Ausführen des Gerätesimulators

Starten Sie im Verzeichnis *device-simulator* den Gerätesimulator mit dem folgenden Befehl:

```cmd
node .\adt_custom_register.js
```

Sie sollten sehen, dass das Gerät registriert und mit IoT Hub verbunden ist und Nachrichten sendet.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Befehlsfenster mit Geräteregistrierung und gesendeten Nachrichten":::

### <a name="validate"></a>Überprüfen

Durch den in diesem Artikel eingerichteten Ablauf wird das Gerät automatisch in Azure Digital Twins registriert. Mit dem folgenden [Azure Digital Twins-CLI-Befehl](how-to-use-cli.md) können Sie den Zwilling des Geräts in der von Ihnen erstellten Azure Digital Twins-Instanz suchen.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Sie sollten den Zwilling des Geräts in der Azure Digital Twins-Instanz sehen.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Befehlsfenster mit neu erstelltem Zwilling":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatisches Außerbetriebnehmen von Geräten mithilfe von IoT Hub-Lebenszyklusereignissen

In diesem Abschnitt fügen Sie IoT Hub-Lebenszyklusereignisse an Azure Digital Twins an, um Geräte automatisch über den folgenden Ablauf außer Betrieb zu nehmen. Dies ist ein Auszug aus der [oben dargestellten](#solution-architecture) vollständigen Architektur.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Ablauf für die Außerbetriebnahme: Auszug aus dem Diagramm der Lösungsarchitektur mit nummerierten Abschnitten. Das Thermostat wird ohne Verbindungen mit Azure-Diensten im Diagramm angezeigt. Daten aus einer manuellen Aktion zum Löschen des Geräts durchlaufen IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure-Digital Twins (3).":::

Beschreibung des Prozesses:
1. Ein externer oder manueller Prozess löst das Löschen eines Geräts in IoT Hub aus.
2. IoT Hub löscht das Gerät und generiert ein [Gerätelebenszyklusereignis](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle), das an einen [Event Hub](../event-hubs/event-hubs-about.md) weitergeleitet wird.
3. Eine Azure-Funktion löscht den Zwilling des Geräts in Azure Digital Twins.

In den folgenden Abschnitten werden die Schritte zum Einrichten dieses Ablaufs für die automatische Außerbetriebnahme von Geräten erläutert.

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Sie müssen nun einen Azure-[Event Hub](../event-hubs/event-hubs-about.md) erstellen, über den die IoT Hub-Lebenszyklusereignisse empfangen werden. 

Führen Sie die Schritte aus, die im Schnellstart [*Erstellen eines Event Hubs*](../event-hubs/event-hubs-create.md) beschrieben werden. Verwenden Sie dabei die folgenden Informationen:
* Wenn Sie das End-to-End-Tutorial verwenden ( [*Tutorial: Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md)), können Sie die Ressourcengruppe wiederverwenden, die Sie für das End-to-End-Tutorial erstellt haben.
* Geben Sie als Namen für den Event Hub *lifecycleevents* oder eine eigene Bezeichnung ein, und merken Sie sich den von Ihnen erstellten Namespace. Diese Informationen werden bei der Einrichtung der Lebenszyklusfunktion und der IoT Hub-Route in den nächsten Abschnitten verwendet.

### <a name="create-an-azure-function"></a>Erstellen einer Azure-Funktion

Als Nächstes erstellen Sie eine durch Event Hubs ausgelöste Funktion innerhalb einer Funktions-App. Sie können die im End-to-End-Tutorial ( [ *Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md)) erstellte Funktions-App oder Ihre eigene verwenden. 

Geben Sie als Namen für den Event Hub *lifecycleevents* ein, und verbinden Sie den Event Hub-Trigger mit dem Event Hub, den Sie im vorherigen Schritt erstellt haben. Wenn Sie einen anderen Namen für den Event Hub verwendet haben, ändern Sie ihn so, dass er mit dem unten angegebenen Namen des Auslösers übereinstimmt.

Diese Funktion verwendet das IoT Hub-Gerätelebenszyklus-Ereignis, um ein vorhandenes Gerät außer Betrieb zu nehmen. Weitere Informationen zu Lebenszyklusereignissen finden Sie unter [*Nicht telemetriebezogene Ereignisse in IoT Hub*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Weitere Informationen zur Verwendung von Event Hubs mit Azure-Funktionen finden Sie unter [*Azure Event Hubs-Trigger für Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Fügen Sie in der veröffentlichten Funktions-App eine neue Funktionsklasse des Typs *Event Hub-Trigger* hinzu, und fügen Sie den folgenden Code ein.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Speichern Sie das Projekt, und veröffentlichen Sie die Funktions-App erneut. Anweisungen zum Veröffentlichen der Funktions-App finden Sie im Abschnitt [*Veröffentlichen der App*](tutorial-end-to-end.md#publish-the-app) im End-to-End-Tutorial.

### <a name="configure-your-function"></a>Konfigurieren Ihrer Funktion

Als Nächstes müssen Sie in ihrer Funktions-App von oben Umgebungsvariablen festlegen, die den Verweis auf die von Ihnen erstellte Azure Digital Twins-Instanz und den Event Hub enthalten. Wenn Sie das End-to-End-Tutorial verwendet haben ( [*Tutorial: Erstellen einer End-to-End-Lösung*](./tutorial-end-to-end.md)), ist die erste Einstellung bereits konfiguriert.

Fügen Sie die Einstellung mit folgendem Azure CLI-Befehl hinzu. Der Befehl kann in [Cloud Shell](https://shell.azure.com) oder lokal ausgeführt werden, wenn die Azure CLI [auf dem Computer installiert](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ist.

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Als Nächstes müssen Sie die Umgebungsvariable für die Funktion für die Verbindung mit dem neu erstellten Event Hub konfigurieren.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Stellen Sie sicher, dass die Berechtigungen und die Rollenzuweisung für verwaltete Identitäten für die Funktions-App ordnungsgemäß konfiguriert wurden. Dies wird im End-to-End-Tutorial im Abschnitt [*Zuweisen von Berechtigungen zur Funktions-App*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) beschrieben.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Erstellen einer IoT Hub-Route für Lebenszyklusereignisse

Nun müssen Sie eine IoT Hub-Route einrichten, um Lebenszyklusereignisse für das Gerät weiterzuleiten. In diesem Fall lauschen Sie speziell auf Ereignisse, bei denen ein Gerät gelöscht wird. Diese sind durch `if (opType == "deleteDeviceIdentity")` gekennzeichnet. Dadurch wird das Löschen des Elements für den digitalen Zwilling und die Außerbetriebnahme eines Geräts und des zugehörigen digitalen Zwillings auslöst.

Anweisungen zum Erstellen einer IoT Hub-Route finden Sie in folgendem Artikel: [*Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte*](../iot-hub/iot-hub-devguide-messages-d2c.md). Im Abschnitt *Nicht telemetriebezogene Ereignisse* wird erläutert, dass Sie **Ereignisse im Gerätelebenszyklus** als Datenquelle für die Route verwenden können.

Für dieses Setup sind folgende Schritte erforderlich:
1. Erstellen Sie einen benutzerdefinierten Endpunkt für den IoT Hub-Event Hub. Dieser Endpunkt sollte den Event Hub als Ziel verwenden, den Sie im Abschnitt [*Erstellen eines Ereignis-Hubs*](#create-an-event-hub) erstellt haben.
2. Fügen Sie eine Route für *Ereignisse des Gerätelebenszyklus* hinzu. Wählen Sie den im vorherigen Schritt erstellten Endpunkt aus. Sie können mit der Routing-Abfrage `opType='deleteDeviceIdentity'` die Ereignisse des Gerätelebenszyklus einschränken, sodass nur die Löschereignisse gesendet werden.
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Hinzufügen einer Route":::

Wenn Sie diese Schritte durchlaufen haben, ist alles für die End-to-End-Außerbetriebnahme der Geräte vorbereitet.

### <a name="validate"></a>Überprüfen

Um die Außerbetriebnahme auszulösen, müssen Sie das Gerät manuell aus IoT Hub löschen.

In der [ersten Hälfte dieses Artikels](#auto-provision-device-using-device-provisioning-service) haben Sie ein Gerät in IoT Hub und einen entsprechenden digitalen Zwilling erstellt. 

Wechseln Sie nun zu IoT Hub, und löschen Sie dieses Gerät (Sie können dies mit einem [Azure CLI-Befehl](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest&preserve-view=true#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) oder über das [Azure-Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs) tun). 

Das Gerät wird automatisch aus Azure Digital Twins entfernt. 

Mit dem folgenden [Azure Digital Twins CLI-Befehl](how-to-use-cli.md) können Sie überprüfen, ob der Zwilling des Geräts in der Azure Digital Twins-Instanz gelöscht wurde.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Sie sollten sehen, dass der Zwilling des Geräts in der Azure Digital Twins-Instanz nicht mehr gefunden werden kann.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Befehlsfenster mit Hinweis, dass der Zwilling nicht gefunden werden kann":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Artikel erstellten Ressourcen nicht mehr benötigen, folgen Sie den Schritten unten, um sie zu löschen.

Bei Verwendung von Azure Cloud Shell oder der lokalen Azure CLI können Sie alle Azure-Ressourcen in einer Ressourcengruppe mit dem Befehl [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) löschen. Hierdurch werden die Ressourcengruppe, die Azure Digital Twins-Instanz, der IoT-Hub und die Registrierung des Hubgeräts, das Event Grid-Thema und die zugehörigen Abonnements, der Event Hub-Namespace sowie beide Azure Functions-Apps (einschließlich zugeordneter Ressourcen, z. B. Speicher) entfernt.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Löschen Sie dann den Beispielordner des Projekts, den Sie heruntergeladen haben, von Ihrem lokalen Computer.

## <a name="next-steps"></a>Nächste Schritte

Die für die Geräte erstellten digitalen Zwillinge werden als flache Hierarchie in Azure Digital Twins gespeichert, können jedoch für die Organisation mit Modellinformationen und einer Hierarchie mit mehreren Ebenen erweitert werden. Weitere Informationen zu diesem Vorgang finden Sie hier:

* [*Konzepte: Digital Twins und der Digital Twins-Graph*](concepts-twins-graph.md)

Sie können eine benutzerdefinierte Logik schreiben, um diese Informationen mithilfe der bereits in Azure Digital Twins gespeicherten Modell- und Diagrammdaten automatisch bereitzustellen. Weitere Informationen zum Verwalten, Aktualisieren und Abrufen von Informationen aus dem Zwillingsgraphen finden Sie in den folgenden Referenzen:

* [*Verwenden Verwalten digitaler Zwillinge*](how-to-manage-twin.md)
* [*Verwenden Abfragen des Zwillingsgraphen*](how-to-query-graph.md)