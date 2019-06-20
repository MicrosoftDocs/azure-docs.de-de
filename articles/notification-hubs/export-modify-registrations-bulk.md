---
title: Massenexportieren und -importieren von Azure Notification Hubs-Registrierungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Notification Hubs-Unterstützung für Massenvorgänge zum Durchführen einer großen Anzahl von Vorgängen für einen Notification Hub oder zum Exportieren aller Registrierungen verwenden.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: d7e38e8eca58c06fc6896887522b320a797fc42e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575302"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Massenexportieren und -importieren von Azure Notification Hubs-Registrierungen
In einigen Szenarios ist das Erstellen oder Anpassen großer Mengen von Registrierungen in einem Notification Hub erforderlich. Bei einigen dieser Szenarios handelt es sich um Tagaktualisierungen, die auf Batchberechnungen folgen, oder Migrationen vorhandener Pushimplementierungen zur Verwendung von Notification Hubs.

In diesem Artikel wird erläutert, wie Sie große Mengen von Vorgängen in einem Notification Hub durchführen oder alle Registrierungen in einem Massenvorgang exportieren.

## <a name="high-level-flow"></a>Allgemeiner Ablauf
Die Batchunterstützung wurde dazu entworfen, Aufträge mit langer Ausführungszeit mit Millionen von Registrierungen zu unterstützen. Die Batchunterstützung nutzt Azure Storage zum Speichern von Auftragsdetails und Ausgaben, um diese Skalierung zu ermöglichen. Für Massenaktualisierungsvorgänge muss der Benutzer eine Datei in einem Blobcontainer erstellen, die eine Liste der Registrierungaktualisierungsvorgänge enthält. Wenn der Auftrag gestartet wird, stellt der Benutzer die jeweiligen URLs zu den Blobcontainern für das Eingabe- und das Ausgabeverzeichnis bereit. Nachdem der Auftrag gestartet wurde, kann der Benutzer den Status überprüfen, indem er einen URL-Speicherort abfragt, der beim Starten des Auftrags bereitgestellt wird. Ein spezifischer Auftrag kann nur Vorgänge einer spezifischen Art (Erstellen, Aktualisieren oder Löschen) durchführen. Exportvorgänge werden analog durchgeführt.

## <a name="import"></a>Importieren

### <a name="set-up"></a>Einrichtung
In diesem Abschnitt wird vorausgesetzt, dass Sie über folgende Entitäten verfügen:

- Ein bereitgestellter Notification Hub
- Ein Azure Storage-Blobcontainer
- Verweise auf das [Azure Storage NuGet-Paket](https://www.nuget.org/packages/windowsazure.storage/) und [Notification Hubs NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Erstellen einer Eingabedatei und Speichern in einem Blob
Eine Eingabedatei enthält eine Liste von Registrierungen, die pro Zeile in XML serialisiert sind. Im folgenden Beispiel wird mithilfe des Azure SDK veranschaulicht, wie die Registrierungen serialisiert und in den Blobcontainer hochgeladen werden.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Im obigen Code werden die Registrierungen im Arbeitsspeicher serialisiert, und der gesamte Datenstrom wird dann in einen Blob hochgeladen. Wenn Sie eine Datei mit mehreren Megabytes hochgeladen haben, finden Sie Informationen zum Ausführen dieser Schritte in den Anleitungen zu Azure-Blobs, z. B. unter [Understanding Block Blobs, Append Blobs, and Page Blobs (Grundlegendes zu Blockblobs, Anfügeblobs und Seitenblobs)](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Erstellen von URL-Token
Sobald Sie Ihre Eingabedatei hochgeladen haben, generieren Sie die URLs für die Eingabedatei und das Ausgabeverzeichnis, die Ihrem Notification Hub bereitgestellt werden sollen. Sie können zwei verschiedene Blobcontainer für die Eingabe und die Ausgabe verwenden.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Übermitteln des Auftrags
Mit den zwei Eingabe- und Ausgabe-URLs können Sie nun den Batchauftrag starten.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Neben den Eingabe- und Ausgabe-URLs wird in diesem Beispiel ein `NotificationHubJob`-Objekt erstellt, das ein `JobType`-Objekt enthält, das einen der folgenden Typen aufweisen kann:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Sobald der Aufruf abgeschlossen ist, wird der Auftrag von Notification Hub fortgesetzt, und Sie können seinen Status überprüfen, indem Sie [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet) aufrufen.

Bei Abschluss des Auftrags können Sie die Ergebnisse untersuchen, indem Sie sich die folgenden Dateien in Ihrem Ausgabeverzeichnis ansehen:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Diese Dateien enthalten die Liste der erfolgreichen und fehlgeschlagenen Vorgänge im Batch. Die Dateien weisen das Format `.cvs` auf, in den Dateien weist jede Zeile die Zeilennummer der ursprünglichen Eingabedatei und die Ausgabe des Vorgangs auf (normalerweise die erstellte oder aktualisierte Beschreibung der Registrierung).

### <a name="full-sample-code"></a>Vollständiger Beispielcode
Mit dem folgenden Beispielcode werden Registrierungen in einen Notification Hub importiert.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Export
Das Exportieren von Registrierungen ähnelt dem Importieren mit den folgenden Unterschieden:

- Sie benötigen lediglich die Ausgabe-URL
- Sie erstellen eine NotificationHubJob-Klasse vom Typ „ExportRegistrations“

### <a name="sample-code-snippet"></a>Beispielcodeausschnitt
Hier ist ein Beispielcodeausschnitt zum Exportieren von Registrierungen in Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Registrierungen finden Sie in den folgenden Artikeln:

- [Registrierungsverwaltung](notification-hubs-push-notification-registration-management.md)
- [Tags für Registrierungen](notification-hubs-tags-segment-push-message.md)
- [Vorlagenregistrierungen](notification-hubs-templates-cross-platform-push-messages.md)
