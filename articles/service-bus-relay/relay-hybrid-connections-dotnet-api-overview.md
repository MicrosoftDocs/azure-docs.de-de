---
title: Übersicht über die .NET Standard-APIs in Azure Relay | Microsoft-Dokumentation
description: Übersicht über die .NET Standard-APIs in Azure Relay
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 78ad3ab49db162af060b4273deea717cd3472668
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749018"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Übersicht über die .NET Standard-APIs für Hybrid Connections in Azure Relay

In diesem Artikel werden einige der wichtigsten .NET Standard-[Client-APIs](/dotnet/api/microsoft.azure.relay) für Hybrid Connections von Azure Relay zusammengefasst.
  
## <a name="relay-connection-string-builder-class"></a>Klasse „Generator für Relay-Verbindungszeichenfolgen“

Die Klasse [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formatiert Verbindungszeichenfolgen, die speziell für Hybrid Connections von Relay gelten. Anhand dieser Klasse können Sie das Format einer Verbindungszeichenfolge überprüfen oder eine Verbindungszeichenfolge von Grund auf erstellen. Der folgende Code enthält ein Beispiel dafür:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Sie können eine Verbindungszeichenfolge auch direkt an die `RelayConnectionStringBuilder`-Methode übergeben. Mit diesem Vorgang können Sie sicherstellen, dass das Format der Verbindungszeichenfolge gültig ist. Wenn einer der Parameter ungültig ist, generiert der Konstruktor eine Ausnahme vom Typ `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hybrid Connection Stream

Die [HybridConnectionStream][HCStream]-Klasse ist das primäre Objekt, das zum Senden und Empfangen von Daten von einem Azure Relay-Endpunkt verwendet wird, unabhängig davon, ob Sie mit einer [HybridConnectionClient][HCClient]- oder einer [HybridConnectionListener][HCListener]-Klasse arbeiten.

### <a name="getting-a-hybrid-connection-stream"></a>Abrufen eines Hybrid Connection Streams

#### <a name="listener"></a>Listener

Mithilfe eines [HybridConnectionListener][HCListener]-Objekts können Sie wie folgt ein `HybridConnectionStream`-Objekt abrufen:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

Mithilfe eines [HybridConnectionClient][HCClient]-Objekts können Sie wie folgt ein `HybridConnectionStream`-Objekt abrufen:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Empfangen von Daten

Die [HybridConnectionStream][HCStream]-Klasse ermöglicht eine bidirektionale Kommunikation. In den meisten Fällen werden von Stream kontinuierlich Daten empfangen. Wenn Sie Text aus dem Stream lesen, sollten Sie auch ein [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx)-Objekt verwenden, das eine einfachere Analyse der Daten ermöglicht. Beispielsweise können Sie Daten als Text lesen statt als `byte[]`.

Der folgende Code liest einzelne Textzeilen aus dem Stream, bis ein Abbruch angefordert wird:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Senden von Daten

Sobald Sie eine Verbindung hergestellt haben, können Sie eine Nachricht an den Relay-Endpunkt senden. Da das Verbindungsobjekt den [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) vererbt, senden Sie Ihre Daten als `byte[]`. Dies wird anhand des folgenden Beispiels veranschaulicht:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Wenn Sie Text jedoch direkt senden möchten, ohne jedes Mal die Zeichenfolge codieren zu müssen, können Sie das `hybridConnectionStream`-Objekt mit einem [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)-Objekt umschließen.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Relay finden Sie unter diesen Links:

* [Microsoft.Azure.Relay-Referenz](/dotnet/api/microsoft.azure.relay)
* [Was ist Azure Relay?](relay-what-is-it.md)
* [Verfügbare Relay-APIs](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener