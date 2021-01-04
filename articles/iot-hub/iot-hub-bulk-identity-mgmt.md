---
title: Importieren/Exportieren von Azure IoT Hub-Geräteidentitäten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Azure IoT-Dienst-SDK Massenvorgänge zum Importieren und Exportieren von Geräteidentitäten auf die Identitätsregistrierung anwenden. Mit Importvorgängen können Sie Geräteidentitäten per Massenvorgang erstellen, aktualisieren und löschen.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e7a725b78fa828ce1286e212ee7de0205968156
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536078"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Importieren und Exportieren von IoT Hub-Geräteidentitäten per Massenvorgang

Jeder IoT Hub verfügt über eine Identitätsregistrierung, die Sie zum Erstellen von gerätebasierten Ressourcen im Dienst verwenden können. Außerdem wird mit der Identitätsregistrierung der Zugriff auf geräteseitige Endpunkte ermöglicht. In diesem Artikel wird das massenhafte Importieren und Exportieren von Geräteidentitäten in und aus einer Identitätsregistrierung beschrieben. Wenn Sie in C# ein funktionierendes Beispiel sehen und erfahren möchten, wie Sie diese Funktion beim Klonen eines Hubs in eine andere Region verwenden können, lesen Sie [Klonen einer IoT Hub-Instanz](iot-hub-how-to-clone.md).

> [!NOTE]
> IoT Hub hat vor kurzem Unterstützung für virtuelle Netzwerke in einer begrenzten Anzahl von Regionen hinzugefügt. Diese Funktion sichert Import- und Exportvorgänge und macht Hauptschlüssel zur Authentifizierung überflüssig.  Anfänglich ist die Unterstützung virtueller Netzwerke nur in den folgenden Regionen verfügbar: *WestUS2* , *EastUS* und *SouthCentralUS* . Weitere Informationen zur Unterstützung virtueller Netzwerke und den API-Aufrufen zu deren Implementierung finden Sie unter [IoT Hub-Unterstützung für virtuelle Netzwerke](virtual-network-support.md).

Import- und Exportvorgänge erfolgen im Kontext von *Aufträgen* , die Ihnen das Anwenden von Massendienstvorgängen auf einen IoT Hub ermöglichen.

Die **RegistryManager** -Klasse enthält die Methoden **ExportDevicesAsync** und **ImportDevicesAsync** , die das **Job** -Framework verwenden. Diese Methoden ermöglichen das Exportieren, Importieren und Synchronisieren der gesamten IoT Hub-Identitätsregistrierung.

In diesem Thema wird die Verwendung der Klasse **RegistryManager** und des **Job** -Systems zum Ausführen von Massenimport- und -exportvorgängen von Geräten in die bzw. aus der Identitätsregistrierung eines IoT-Hubs erörtert. Ferner können Sie den Azure IoT Hub Device Provisioning-Dienst auch dazu verwenden, eine Just-in-Time-Bereitstellung auf einem oder mehreren IoT-Hubs zu ermöglichen, die keinen menschlichen Eingriff erfordert. Weitere Informationen finden Sie in der [Dokumentation zum Bereitstellungsdienst](../iot-dps/index.yml).

## <a name="what-are-jobs"></a>Was sind Aufträge?

Identitätsregistrierungsvorgänge verwenden das **Job** -System, wenn der Vorgang:

* eine potenziell lange Ausführungsdauer im Vergleich mit standardmäßigen Laufzeitvorgängen hat.

* eine große Menge von Daten an den Benutzer zurückgibt.

Stattdessen erstellt der Vorgang asynchron einen **Auftrag** für diesen IoT Hub, anstatt eines einzelnen API-Aufrufs, mit dem auf das Ergebnis des Vorgangs gewartet bzw. mit dem der Vorgang blockiert wird. Für den Vorgang wird dann sofort ein **JobProperties** -Objekt zurückgegeben.

Der folgende C#-Codeausschnitt zeigt, wie ein Exportauftrag erstellt wird:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Um die **RegistryManager** -Klasse in Ihrem C#-Code zu verwenden, fügen Sie das **Microsoft.Azure.Devices** -NuGet-Paket Ihrem Projekt hinzu. Die **RegistryManager** -Klasse befindet sich im **Microsoft.Azure.Devices** -Namespace.

Sie können die **RegistryManager** -Klasse verwenden, um den Status des **Auftrags** unter Verwendung der zurückgegebenen **JobProperties** -Metadaten abzufragen. Verwenden Sie zum Erstellen einer Instanz der **RegistryManager** -Klasse die **CreateFromConnectionString** -Methode.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

So finden Sie die Verbindungszeichenfolge Ihres IoT-Hubs im Azure-Portal

- Navigieren Sie zu Ihrem IoT Hub.

- Klicken Sie auf **Freigegebene Zugriffsrichtlinien** .

- Wählen Sie eine Richtlinie aus. Berücksichtigen Sie dabei die benötigten Berechtigungen.

- Kopieren Sie die Verbindungszeichenfolge aus dem Bereich am rechten Bildschirmrand.

Der folgende C#-Codeausschnitt zeigt, wie alle fünf Sekunden eine Abfrage erfolgt, um festzustellen, ob die Auftragsausführung beendet wurde:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Wenn für Ihr Speicherkonto Firewallkonfigurationen vorhanden sind, die die Konnektivität von IoT Hub einschränken, sollten Sie die Verwendung der [Ausnahme vertrauenswürdiger Microsoft-Erstanbieter](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) in Erwägung ziehen (in ausgewählten Regionen für IoT-Hubs mit verwalteter Dienstidentität verfügbar).


## <a name="device-importexport-job-limits"></a>Grenzwerte bei Geräteimport-/exportaufträgen

Bei allen IoT Hub-Tarifen ist jeweils nur 1 aktiver Geräteimport- oder -exportauftrag zulässig. Außerdem gibt es in IoT Hub Grenzwerte für die Rate der Auftragsvorgänge. Weitere Informationen finden Sie unter [Referenz: IoT Hub-Kontingente und Drosselung](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Exportieren von Geräten

Mithilfe der Methode **ExportDevicesAsync** können Sie die gesamte IoT Hub-Identitätsregistrierung in einen Azure Storage-Blobcontainer exportieren und dazu eine Shared Access Signature (SAS) verwenden. Weitere Informationen zu SAS (Shared Access Signatures) finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md).

Mit dieser Methode können Sie zuverlässige Sicherungen Ihrer Geräte-Informationen in einem Blobcontainer erstellen, den Sie steuern.

Die **ExportDevicesAsync** -Methode erfordert zwei Parameter:

* Eine *Zeichenfolge* , die einen URI eines Blobcontainers enthält. Dieser URI muss ein SAS-Token enthalten, das Schreibzugriff auf den Container gewährt. Der Auftrag erstellt zum Speichern der serialisierten Exportgerätedaten ein Blockblob in diesem Container. Das SAS-Token muss diese Berechtigungen enthalten:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Einen *booleschen* Wert, der angibt, ob Sie Authentifizierungsschlüssel aus Ihren Exportdaten ausschließen möchten. Bei **FALSE** werden Authentifizierungsschlüssel in die Exportausgabe eingeschlossen. Andernfalls werden Schlüssel als **NULL** exportiert.

Der folgende C#-Codeausschnitt zeigt, wie Sie einen Exportauftrag initiieren, der Geräteauthentifizierungsschlüssel in die exportierten Daten einbezieht und für den Abschluss eine Umfrage durchführt:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Der Auftrag speichert seine Ausgabe im angegebenen Blobcontainer als Blockblob mit dem Namen **devices.txt** . Die Ausgabedaten bestehen aus serialisierten JSON-Gerätedaten mit einem Gerät pro Zeile.

Das folgende Beispiel zeigt die Ausgabedaten:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Wenn ein Gerät Zwillingsdaten aufweist, werden die Zwillingsdaten ebenfalls zusammen mit den Gerätedaten exportiert. Das folgende Beispiel zeigt dieses Format. Alle Daten ab der Zeile „twinETag“ bis zum Ende sind Zwillingsdaten.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Wenn Sie Zugriff auf diese Daten im Code benötigen, können Sie diese Daten mithilfe der **ExportImportDevice** -Klasse problemlos deserialisieren. Der folgende C#-Codeausschnitt zeigt, wie Geräte-Informationen gelesen werden, die zuvor in einen Blockblob exportiert wurden:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importieren von Geräten

Die **ImportDevicesAsync** -Methode in der **RegistryManager** -Klasse ermöglicht Ihnen das Anwenden von Massenvorgängen (Import/Synchronisierung) auf eine IoT Hub-Identitätsregistrierung. Wie die **ExportDevicesAsync** -Methode auch, wird für die **ImportDevicesAsync** -Methode das **Job** -Framework verwendet.

Nutzen Sie die **ImportDevicesAsync** -Methode umsichtig, damit mit ihr nicht nur neue Geräte in der Identitätsregistrierung bereitgestellt, sondern auch vorhandene Geräte aktualisiert und gelöscht werden können.

> [!WARNING]
> Ein Importvorgang kann nicht rückgängig gemacht werden. Sichern Sie vor dem Verwenden der **ExportDevicesAsync** -Methode stets Ihre vorhandenen Daten in einem anderen Blobcontainer, bevor Sie Massenänderungen an Ihrer Identitätsregistrierung vornehmen.

Die **ImportDevicesAsync** -Methode erfordert zwei Parameter:

* Eine *Zeichenfolge* , die einen URI eines [Azure Storage](../storage/index.yml)-Blobcontainers als *Eingabe* in den Auftrag enthält. Dieser URI muss ein SAS-Token enthalten, das Lesezugriff auf den Container gewährt. Dieser Container muss ein Blob mit dem Namen **devices.txt** enthalten, das die serialisierten Gerätedaten enthält, die in Ihre Identitätsregistrierung importiert werden sollen. Die importierten Daten müssen Geräteinformationen im gleichen JSON-Format enthalten, das vom **ExportImportDevice** -Auftrag verwendet wird, wenn er das Blob **devices.txt** erstellt. Das SAS-Token muss diese Berechtigungen enthalten:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Eine *Zeichenfolge* , die einen URI eines [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)-Blobcontainers als *Ausgabe* aus dem Auftrag enthält. Der Auftrag erstellt in diesem Container ein Blockblob zum Speichern von Fehlerinformationen aus dem abgeschlossenen **Importauftrag** . Das SAS-Token muss diese Berechtigungen enthalten:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Die beiden Parameter können auf den gleichen Blobcontainer verweisen. Die separaten Parameter ermöglichen einfach mehr Kontrolle über Ihre Daten, da der Ausgabecontainer zusätzliche Berechtigungen erfordert.

Der folgende C#-Codeausschnitt zeigt, wie ein Importauftrag ausgelöst wird:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Diese Methode kann auch verwendet werden, um die Daten für den Gerätezwilling zu importieren. Das Format für die Dateneingabe entspricht dem Format wie im Abschnitt für **ExportDevicesAsync** . Auf diese Weise können Sie die exportierten Daten erneut importieren. **$metadata** ist optional.

## <a name="import-behavior"></a>Importverhalten

Sie können die **ImportDevicesAsync** -Methode zum Anwenden der folgenden Massenvorgänge auf Ihre Identitätsregistrierung verwenden:

* Massenregistrierung neuer Geräte
* Massenlöschung vorhandener Geräte
* Massenstatusänderungen (Aktivieren oder Deaktivieren von Geräten)
* Massenzuweisung neuer Geräteauthentifizierungsschlüssel
* Automatische Massenneugenerierung von Geräteauthentifizierungsschlüsseln
* Massenaktualisierung von Zwillingsdaten

Sie können eine beliebige Kombination der obigen Vorgänge innerhalb eines einzelnen Aufrufs von **ImportDevicesAsync** ausführen. Sie können beispielsweise gleichzeitig neue Geräte registrieren oder vorhandene Geräte aktualisieren und löschen. Bei Verwendung zusammen mit der **ExportDevicesAsync** -Methode können Sie alle Ihre Geräte vollständig von einem IoT Hub zu einem anderen migrieren.

Wenn die Importdatei Zwillingsmetadaten enthält, dann überschreiben diese Metadaten die vorhandenen Zwillingsmetadaten. Wenn die Importdatei keine Zwillingsmetadaten enthält, werden nur die `lastUpdateTime`-Metadaten mit der aktuellen Uhrzeit aktualisiert.

Verwenden Sie die optionale **importMode** -Eigenschaft in den Importserialisierungsdaten für jedes Gerät, um den Importprozess gerätebezogen zu steuern. Die **importMode** -Eigenschaft hat die folgenden Optionen:

| importMode | BESCHREIBUNG |
| --- | --- |
| **createOrUpdate** |Wenn ein Gerät mit der angegebenen **ID** nicht vorhanden ist, wird es neu registriert. <br/>Wenn das Gerät bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten ohne Berücksichtigung des **ETag** -Werts überschrieben. <br> Der Benutzer kann optional Zwillingsdaten mit den Gerätedaten angeben. Das ETag des Zwillings wird, sofern angegeben, unabhängig vom Geräte-ETag verarbeitet. Wenn ein Konflikt mit dem ETag des vorhandenen Zwillings vorliegt, wird ein Fehler in die Protokolldatei geschrieben. |
| **erstellen** |Wenn ein Gerät mit der angegebenen **ID** nicht vorhanden ist, wird es neu registriert. <br/>Wenn das Gerät bereits vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. <br> Der Benutzer kann optional Zwillingsdaten mit den Gerätedaten angeben. Das ETag des Zwillings wird, sofern angegeben, unabhängig vom Geräte-ETag verarbeitet. Wenn ein Konflikt mit dem ETag des vorhandenen Zwillings vorliegt, wird ein Fehler in die Protokolldatei geschrieben. |
| **update** |Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten ohne Berücksichtigung des **ETag** -Werts überschrieben. <br/>Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. |
| **updateIfMatchETag** |Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten nur überschrieben, wenn es eine Übereinstimmung mit einem **ETag** gibt. <br/>Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. <br/>Wenn es keine Übereinstimmung mit einem **ETag** gibt, wird ein Fehler in die Protokolldatei geschrieben. |
| **createOrUpdateIfMatchETag** |Wenn ein Gerät mit der angegebenen **ID** nicht vorhanden ist, wird es neu registriert. <br/>Wenn das Gerät bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten nur überschrieben, wenn es eine Übereinstimmung mit einem **ETag** gibt. <br/>Wenn es keine Übereinstimmung mit einem **ETag** gibt, wird ein Fehler in die Protokolldatei geschrieben. <br> Der Benutzer kann optional Zwillingsdaten mit den Gerätedaten angeben. Das ETag des Zwillings wird, sofern angegeben, unabhängig vom Geräte-ETag verarbeitet. Wenn ein Konflikt mit dem ETag des vorhandenen Zwillings vorliegt, wird ein Fehler in die Protokolldatei geschrieben. |
| **delete** |Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, wird es ohne Berücksichtigung des **ETag** -Werts gelöscht. <br/>Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. |
| **deleteIfMatchETag** |Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, wird es nur gelöscht, wenn es eine Übereinstimmung mit einem **ETag** gibt. Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. <br/>Wenn es keine Übereinstimmung mit einem ETag gibt, wird ein Fehler in die Protokolldatei geschrieben. |

> [!NOTE]
> Wenn die Serialisierungsdaten nicht explizit ein **importMode** -Flag für ein Gerät definieren, wird standardmäßig **createOrUpdate** während des Importvorgangs gewählt.

## <a name="import-devices-example--bulk-device-provisioning"></a>Beispiel für das Importieren von Geräten – Massengerätebereitstellung

Im folgenden C#-Codebeispiel wird veranschaulicht, wie Sie mehrere Geräteidentitäten generieren, mit denen Folgendes durchgeführt wird:

* Einfügen von Authentifizierungsschlüsseln
* Schreiben von Geräteinformationen in ein Blockblob
* Importieren der Geräte in die Identitätsregistrierung

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Beispiel für das Importieren von Geräten – Massenlöschung

Im folgenden Codebeispiel wird das Löschen der Geräte gezeigt, die Sie mithilfe des vorherigen Codebeispiels hinzugefügt haben:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Abrufen des SAS-URI des Containers

Das folgende Codebeispiel veranschaulicht das Erstellen eines [SAS-URI](../storage/common/storage-sas-overview.md) mit Lese-, Schreib- und Löschberechtigungen für einen Blobcontainer:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Massenvorgänge auf die Identitätsregistrierung in einem IoT Hub angewendet werden. Viele dieser Vorgänge, darunter das Verschieben von Geräten von einem Hub zu einem anderen, werden verwendet im Abschnitt [„Verwalten von Geräten, die im IoT Hub registriert wurden“ von „Klonen einer IoT Hub-Instanz“](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Dem Artikel zum Thema „Klonen“ ist ein funktionierendes Beispiel zugeordnet, das in den IoT C# -Beispielen auf dieser Seite zu finden ist: [Azure IoT-Beispiele zu C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/); der Name des Projekts lautet „ImportExportDevicesSample“. Sie können das Beispiel herunterladen und ausprobieren; Anleitungen dazu finden Sie im Artikel [Klonen einer IoT Hub-Instanz](iot-hub-how-to-clone.md).

Weitere Informationen zum Verwalten von Azure IoT Hub finden Sie in den folgenden Artikeln:

* [Überwachen von IoT Hub](monitor-iot-hub.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)

Informationen, die Sie beim Erforschen der Verwendung des IoT Hub Device Provisioning-Diensts für die Just-in-Time-Bereitstellung ohne Benutzereingriff unterstützen, finden Sie in: 

* [Azure IoT Hub Device Provisioning-Dienst](../iot-dps/index.yml)