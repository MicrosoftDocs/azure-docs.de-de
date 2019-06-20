---
title: 'Senden und Empfangen von Ereignissen unter Verwendung von Go: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine exemplarische Vorgehensweise für die Erstellung einer Go-Anwendung, die Ereignisse aus Azure Event Hubs sendet.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821687"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Senden von Ereignissen an oder Empfangen von Ereignissen aus Event Hubs mithilfe von Go
Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In diesem Tutorial wird beschrieben, wie Sie Go-Anwendungen schreiben, die Ereignisse an einen Event Hub senden oder von diesem empfangen. 

> [!NOTE]
> Sie können diese Schnellstartvorlage als Beispiel aus [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) herunterladen, die Zeichenfolgen `EventHubConnectionString` und `EventHubName` durch die Werte für Ihren Event Hub ersetzen und das Beispiel dann ausführen. Alternativ können Sie auch die Schritte in diesem Tutorial ausführen, um eine eigene Vorlage zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- Eine lokale Go-Installation. Befolgen Sie bei Bedarf [diese Anweisungen](https://golang.org/doc/install).
- Ein aktives Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][] erstellen, bevor Sie beginnen.
- **Erstellen eines Event Hubs-Namespace und eines Event Hubs.** Verwenden Sie das [Azure-Portal](https://portal.azure.com), um einen Namespace des Typs „Event Hubs“ zu erstellen, und rufen Sie die Verwaltungsanmeldeinformationen ab, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub.

## <a name="send-events"></a>Senden von Ereignisse
In diesem Abschnitt erfahren Sie, wie Sie eine Go-Anwendung zum Senden von Ereignissen an einen Event Hub erstellen. 

### <a name="install-go-package"></a>Installieren des Go-Pakets

Rufen Sie das Go-Paket für Event Hubs mit `go get` oder `dep` ab. Beispiel:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importieren von Paketen in eine Codedatei

Verwenden Sie das folgende Codebeispiel, um Go-Pakete zu importieren:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

Erstellen Sie anhand der Anweisungen unter [Erstellen eines Azure-Dienstprinzipals mit Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli) einen neuen Dienstprinzipal. Speichern Sie die bereitgestellten Anmeldeinformationen in Ihrer Umgebung mit den folgenden Namen. Sowohl das Azure SDK für Go als auch die Event Hubs-Pakete sind vorkonfiguriert, um nach diesen Variablennamen zu suchen:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Erstellen Sie dann einen Autorisierungsanbieter für Ihren Event Hubs-Client, der diese Anmeldeinformationen verwendet:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Erstellen eines Event Hubs-Clients

Der folgende Code erstellt einen neuen Event Hubs-Client:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Schreiben von Code zum Senden von Nachrichten

Verwenden Sie im folgenden Ausschnitt „1.“, um Nachrichten interaktiv von einem Terminal aus zu senden, oder „2.“, um Nachrichten innerhalb Ihres Programms zu senden:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Extras

Rufen Sie die IDs der Partitionen in Ihrem Event Hub ab:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Führen Sie die Anwendung aus, um Ereignisse an den Event Hub zu senden. 

Glückwunsch! Sie haben jetzt Nachrichten an einen Event Hub gesendet.

## <a name="receive-events"></a>Empfangen von Ereignissen

### <a name="create-a-storage-account-and-container"></a>Erstellen eines Storage-Kontos und -Containers

Angaben zum Status, z.B. Leases auf Partitionen und Prüfpunkte im Ereignisstream, werden über einen Azure Storage-Container zwischen den Empfängern freigegeben. Sie können ein Speicherkonto und einen Container erstellen, indem Sie das Go SDK verwenden oder die Anweisungen unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-create-storage-account.md) befolgen.

Beispiele zum Erstellen von Storage-Artefakte mit dem Go SDK finden Sie im [Go-Beispielrepository](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) und im Beispiel für dieses Tutorial.

### <a name="go-packages"></a>Go-Pakete

Um Nachrichten zu empfangen, rufen Sie die Go-Pakete für Event Hubs mit `go get` oder `dep` ab:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importieren von Paketen in eine Codedatei

Verwenden Sie das folgende Codebeispiel, um Go-Pakete zu importieren:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

Erstellen Sie anhand der Anweisungen unter [Erstellen eines Azure-Dienstprinzipals mit Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli) einen neuen Dienstprinzipal. Speichern Sie die bereitgestellten Anmeldeinformationen in Ihrer Umgebung unter folgenden Namen: Sowohl das Azure SDK für Go als auch das Event Hubs-Paket sind vorkonfiguriert, um nach diesen Variablennamen zu suchen.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Erstellen Sie dann einen Autorisierungsanbieter für Ihren Event Hubs-Client, der diese Anmeldeinformationen verwendet:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Metadatenstruktur abrufen

Mit dem Azure SDK für Go können Sie eine Metadatenstruktur Ihrer Azure-Umgebung abrufen. Mithilfe dieser Struktur lassen sich in späteren Vorgängen die richtigen Endpunkte finden.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Erstellen eines Anmeldeinformationen-Hilfsprogramms 

Erstellen Sie ein Anmeldeinformationen-Hilfsprogramm, das mithilfe der vorherigen Azure Active Directory-Anmeldeinformationen (AAD) Shared Access Signature-Anmeldeinformationen (SAS) für Storage erstellt. Der letzte Parameter weist diesen Konstruktor an, die gleichen Umgebungsvariablen wie zuvor zu verwenden:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Erstellen eines Checkpointers und Leasers 

Erstellen Sie einen **Leaser**, der eine Partition an einen bestimmten Empfänger least, und einen **Checkpointer**, der Prüfpunkte für den Nachrichtenstream schreibt, damit andere Empfänger ab dem richtigen Offset lesen können.

Derzeit ist ein einzelnes **StorageLeaserCheckpointer**-Element verfügbar, das denselben Storage-Container für die Verwaltung von Leases und Checkpoints verwendet. Zusätzlich zu den Speicherkonten- und Containernamen benötigt **StorageLeaserCheckpointer** die im vorherigen Schritt erstellten Anmeldeinformationen und die Azure-Umgebungsstruktur, um korrekt auf den Container zuzugreifen.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Erstellen eines Ereignisprozessorhosts

Nun sind alle Voraussetzungen erfüllt, um EventProcessorHost wie folgt zu erstellen. Dasselbe **StorageLeaserCheckpointer**-Element wird sowohl als Leaser als auch als Checkpointer verwendet, wie zuvor beschrieben:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Erstellen eines Handlers 

Erstellen Sie einen Handler, und registrieren Sie ihn beim Ereignisprozessorhost. Wenn der Host gestartet wird, wendet er diesen und alle anderen angegebenen Handler auf eingehende Nachrichten an:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Schreiben von Code zum Empfangen von Nachrichten

Wenn alles eingerichtet ist, starten Sie den Ereignisprozessorhost entweder mit `Start(context)`, damit er permanent ausgeführt wird, oder mit `StartNonBlocking(context)`, damit er nur ausgeführt wird, wenn Nachrichten verfügbar sind.

In diesem Tutorial erfolgen der Start und die Ausführung wie folgt. Im GitHub-Beispiel finden Sie ein Beispiel für die Verwendung von `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Features und Terminologie in Azure Event Hubs](event-hubs-features.md)
- [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[kostenloses Konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
