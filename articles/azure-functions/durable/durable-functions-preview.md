---
title: Durable Functions-Previewfunktionen – Azure Functions
description: Hier erhalten Sie Informationen Sie zu den Previewfunktionen für Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596077"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 Preview (Azure Functions)

*Durable Functions* ist eine Erweiterung von [Azure Functions](../functions-overview.md) und [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md), mit der Sie zustandsbehaftete Funktionen in einer serverlosen Umgebung schreiben können. Die Erweiterung verwaltet Status, Prüfpunkte und Neustarts für Sie. Wenn Sie nicht bereits mit Durable Functions vertraut sind, lesen Sie die [Übersichtsdokumentation](durable-functions-overview.md).

Durable Functions ist ein GA-Feature (allgemeine Verfügbarkeit) von Azure Functions, enthält aber auch mehrere Unterfeatures, die derzeit als öffentliche Vorschauversion verfügbar sind. In diesem Artikel werden neu veröffentlichte Previewfunktionen beschrieben sowie Details dazu, wie sie funktionieren und wie Sie mit ihrer Verwendung beginnen können.

> [!NOTE]
> Diese Previewfunktionen sind Teil eines Durable Functions 2.0-Releases, bei dem es sich derzeit um ein **Alpha-Qualitätsrelease** mit mehreren Breaking Changes handelt. Die Azure Functions Durable-Erweiterungspaketbuilds finden Sie auf „nuget.org“ mit Versionsangaben in der Form **2.0.0-alpha**. Diese Builds sind für keine Produktionsworkloads geeignet, und Folgereleases könnten zusätzliche Breaking Changes enthalten.

## <a name="breaking-changes"></a>Wichtige Änderungen

In Durable Functions 2.0 werden mehrere Breaking Changes eingeführt. Bei vorhandenen Anwendungen ist davon auszugehen, dass sie nicht ohne Codeänderungen mit Durable Functions 2.0 kompatible sein werden. In diesem Abschnitt werden einige der Änderungen aufgeführt:

### <a name="dropping-net-framework-support"></a>Unterstützung von .NET Framework wird eingestellt

Die Unterstützung für .NET Framework (und deshalb Functions 1.0) wurde für Durable Functions 2.0 eingestellt. Der Hauptgrund hierfür ist, dass Nicht-Windows-Mitwirkenden ermöglicht werden soll, Änderungen einfach erstellen und testen zu können, die sie von macOS- und Linux-Plattformen aus an Durable Functions vornehmen. Der zweite Grund ist es, Entwickler zu ermutigen, auf die neueste Version der Azure Functions Runtime umzusteigen.

### <a name="hostjson-schema"></a>host.json-Schema

Der folgende Codeausschnitt zeigt das neue Schema für host.json. Die wichtigste Änderung, derer Sie sich bewusst sein sollten, ist der neue Abschnitt `"storageProvider"` sowie der darunter befindliche Abschnitt `"azureStorage"`. Diese Änderung erfolgte zur Unterstützung von [alternativen Speicheranbietern](durable-functions-preview.md#alternate-storage-providers).

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Mit der zunehmenden Stabilität von Durable Functions 2.0 werden weitere Änderungen am Abschnitt `durableTask` von host.json eingeführt. Weitere Informationen zu diesen Änderungen finden Sie in [diesem GitHub-Problem](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Änderungen an der öffentlichen Schnittstelle

Die verschiedenen „context“-Objekte, die von Durable Functions unterstützt werden, besaßen abstrakte Basisklassen, die für die Verwendung bei Komponententests gedacht waren. Als Bestandteil von Durable Functions 2.0 wurden diese abstrakten Basisklassen durch Schnittstellen ersetzt. Funktionscode, der die konkreten Typen direkt verwendet, ist davon nicht betroffen.

In der folgenden Tabelle werden die Hauptänderungen dargestellt:

| Alter Typ | Neuer Typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

In dem Fall, wo eine abstrakte Basisklasse virtuelle Methoden enthielt, wurden diese virtuellen Methoden durch Erweiterungsmethoden ersetzt, die in `DurableContextExtensions` definiert sind.

## <a name="entity-functions"></a>Entitätsfunktionen

Entitätsfunktionen definieren Vorgänge zum Lesen und Aktualisieren kleinerer Zustandsteile, bekannt als *dauerhafte Entitäten*. Wie Orchestratorfunktionen besitzen Entitätsfunktionen einen speziellen Triggertyp, den *Entitätstrigger*. Im Gegensatz zu Orchestratorfunktionen müssen Entitätsfunktionen keine spezifischen Codeeinschränkungen besitzen. Entitätsfunktionen verwalten Zustände auch explizit, statt Zustände implizit durch die Ablaufsteuerung darzustellen.

Der folgende Code ist ein Beispiel für eine einfache Entitätsfunktion, die eine *Counter*-Entität (Zähler) definiert. Die Funktion definiert drei Vorgänge, `add`, `subtract` und `reset`, von denen jeder einen ganzzahligen Wert aktualisiert, `currentValue`.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Der Zugriff auf Entitäts*instanzen* erfolgt über einen eindeutigen Bezeichner, die *Entitäts-ID*. Eine Entitäts-ID ist einfach ein Paar aus Zeichenfolgen, das eine Entitätsinstanz eindeutig identifiziert. Sie besteht aus:

1. einem **Entitätsnamen**: Ein Name, der den Typ der Entität identifiziert (z. B. „Zähler“).
2. einem **Entitätsschlüssel**: Eine Zeichenfolge, die die Entität unter allen anderen Entitäten desselben Namens eindeutig identifiziert (z. B. eine GUID).

Beispielsweise könnte eine *Counter*-Entitätsfunktion verwendet werden, um den Punktestand in einem Onlinespiel nachzuhalten. Jede Instanz des Spiels besitzt dann eine eindeutige Entitäts-ID, z. B. `@Counter@Game1`, `@Counter@Game2` usw.

### <a name="comparison-with-virtual-actors"></a>Vergleich mit virtuellen Akteuren

Das Design der dauerhaften Entitäten ist stark vom [Akteur-Modell](https://en.wikipedia.org/wiki/Actor_model) beeinflusst. Wenn Sie mit Akteuren bereits vertraut sind, sollten Ihnen die Konzepte, die dauerhaften Entitäten zugrunde liegen, vertraut sein. Insbesondere sind permanente Entitäten [virtuellen Akteuren](https://research.microsoft.com/en-us/projects/orleans/) in vielerlei Hinsicht sehr ähnlich:

* Dauerhafte Entitäten sind über eine *Entitäts-ID* adressierbar.
* Vorgänge mit dauerhaften Entitäten werden seriell ausgeführt, immer einzeln, um Racebedingungen zu verhindern.
* Dauerhafte Entitäten werden automatisch erstellt, wenn sie aufgerufen oder signalisiert werden.
* Wenn keine Vorgänge ausgeführt werden, werden dauerhafte Entitäten automatisch aus dem Arbeitsspeicher entladen.

Es gibt jedoch einige wichtige Unterschiede, die beachtenswert sind:

* Dauerhafte Entitäten sind als reine Funktionen modelliert. Dieses Design unterscheidet sich von den meisten objektorientierten Frameworks, in denen Akteure mithilfe von sprachspezifischer Unterstützung für Klassen, Eigenschaften und Methoden dargestellt werden.
* Dauerhafte Entitäten priorisieren *Dauerhaftigkeit* über *Latenz*, weshalb sie dann möglicherweise nicht für Anwendungen mit strengen Latenzanforderungen geeignet sind.
* Zwischen Entitäten gesendete Nachrichten werden zuverlässig und in Reihenfolge übermittelt.
* Dauerhafte Entitäten können in Verbindung mit dauerhaften Orchestrierungen verwendet werden und können als verteilte Sperren dienen, die später in diesem Artikel beschrieben werden.
* Anforderung/Antwort-Muster in Entitäten sind auf Orchestrierungen beschränkt. Bei der Kommunikation von Entität zu Entität sind nur unidirektionale Nachrichten zulässig (auch bekannt als „Signalisierung“), wie im ursprüngliche Akteur-Modell. Dieses Verhalten verhindert verteilte Deadlocks.

### <a name="durable-entity-apis"></a>Dauerhafte Entitäts-APIs

Die Entitätsunterstützung umfasst mehrere APIs. Einmal gibt es eine neue API zum Definieren von Entitätsfunktionen, wie oben zu sehen, die angeben, was geschehen soll, wenn ein Vorgang für eine Entität aufgerufen wird. Ferner wurden vorhandene APIs für Clients und Orchestrierungen mit neuen Funktionen für die Interaktion mit Entitäten aktualisiert.

### <a name="implementing-entity-operations"></a>Implementieren von Entitätsvorgängen

Die Ausführung eines Vorgangs für eine Entität kann diese Member für das Context-Objekt aufrufen (`IDurableEntityContext` in .NET):

* **OperationName**: Ruft den Namen des Vorgangs ab.
* **GetInput\<T>** : Ruft die Eingabe für den Vorgang ab.
* **GetState\<T>** : Ruft den aktuelle Zustand der Entität ab.
* **SetState**: Aktualisiert den Zustand der Entität.
* **SignalEntity**: Sendet eine unidirektionale Nachricht an eine Entität.
* **Self**: Ruft die ID der Entität ab.
* **Return**: Gibt einen Wert an den Client oder die Orchestrierung zurück, der/die den Vorgang aufgerufen hat.
* **IsNewlyConstructed**: Gibt `true` zurück, wenn die Entität vor dem Vorgang noch nicht vorhanden war.
* **DestructOnExit**: Löscht die Entität nach Abschluss des Vorgangs.

Vorgänge sind weniger eingeschränkt als Orchestrierungen:

* Vorgänge können mithilfe von synchronen oder asynchronen APIs externe E/A aufrufen (wir empfehlen die ausschließliche Verwendung asynchroner APIs).
* Vorgänge können nichtdeterministisch sein. Beispielsweise ist es sicher, `DateTime.UtcNow`, `Guid.NewGuid()` oder `new Random()` aufzurufen.

### <a name="accessing-entities-from-clients"></a>Zugriff auf Entitäten aus Clients

Dauerhafte Entitäten können von normalen Funktionen über die `orchestrationClient`-Bindung (`IDurableOrchestrationClient` in .NET) aufgerufen werden. Folgende Methoden werden unterstützt:

* **ReadEntityStateAsync\<T>** : Liest den Zustand einer Entität.
* **SignalEntityAsync**: Sendet eine unidirektional Nachricht an eine Entität und wartet darauf, dass diese in die Warteschlange eingereiht wird.

Diese Methoden priorisieren Leistung über Konsistenz: `ReadEntityStateAsync` kann einen veralteten Wert zurückgeben, und `SignalEntityAsync` kann zurückgegeben werden, bevor der Vorgang abgeschlossen ist. Im Gegensatz dazu ist das Aufrufen von Entitäten aus Orchestrierungen (wie im Folgenden beschrieben) streng konsistent.

### <a name="accessing-entities-from-orchestrations"></a>Zugriff auf Entitäten aus Orchestrierungen

Orchestrierungen können mithilfe des Context-Objekts auf Entitäten zugreifen. Sie können zwischen unidirektionaler Kommunikation (auslösen und vergessen) und bidirektionaler Kommunikation (Anforderung und Antwort) wählen. Die entsprechenden Methoden sind

* **SignalEntity**: Sendet eine unidirektionale Nachricht an eine Entität.
* **CallEntityAsync**: Sendet eine Nachricht an eine Entität und wartet auf eine Antwort, die anzeigt, dass der Vorgang abgeschlossen wurde.
* **CallEntityAsync\<T>** : Sendet eine Nachricht an eine Entität und wartet auf eine Antwort, die ein Ergebnis vom Typ „T“ enthält.

Wenn Sie bidirektionale Kommunikation verwenden, werden auch alle während der Ausführung des Vorgangs ausgelösten Ausnahmen zurück an die aufrufende Orchestrierung übertragen und erneut ausgelöst. Im Gegensatz dazu werden bei der Verwendung von „auslösen und vergessen“ Ausnahmen nicht berücksichtigt.

### <a name="locking-entities-from-orchestrations"></a>Sperren von Entitäten aus Orchestrierungen

Orchestrierungen können Entitäten sperren. Diese Funktion bietet eine einfache Möglichkeit, unerwünschte Racebedingungen durch Verwendung *kritischer Abschnitte* zu verhindern.

Das „Context“-Objekt bietet die folgenden Methoden:

* **LockAsync**: Richtet Sperren für eine oder mehrere Entitäten ein.
* **IsLocked**: Gibt „true“ zurück, wenn aktuell in einem kritischen Abschnitt, andernfalls „false“.

Der kritische Abschnitt endet, und alle Sperren werden freigegeben, wenn die Orchestrierung beendet wird. In .NET gibt `LockAsync` ein `IDisposable` zurück, das den kritischen Abschnitt beim Verwerfen beendet, was zusammen mit einer `using`-Klausel verwendet werden kann, um eine syntaktische Darstellung des kritischen Abschnitts zu erhalten.

Nehmen Sie beispielsweise eine Orchestrierung an, die testen muss, ob zwei Spieler zur Verfügung stehen, und dann beide zu einem Spiel zuweisen muss. Diese Aufgabe kann mit einem kritischen Abschnitt wie folgt implementiert werden:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

Innerhalb des kritischen Abschnitts sind beide Player-Entitäten (Spieler) gesperrt, was bedeutet, dass sie keine anderen Vorgänge ausführen, als die, die aus dem kritischen Abschnitt aufgerufen werden. Dieses Verhalten verhindert Racebedingungen mit in Konflikt stehenden Vorgänge, z. B. Spieler, die einem anderen Spiel zugewiesen werden, oder sich abmeldende Spieler.

Wir setzen mehrere Einschränkungen durch, wie kritische Abschnitte verwendet werden können. Diese Einschränkungen dienen zum Verhindern von Deadlocks und Eintrittsinvarianz.

* Kritische Abschnitte können nicht geschachtelt werden.
* Kritische Abschnitte können keine Unterorchestrierungen erstellen.
* Kritische Abschnitte können nur Entitäten aufrufen, die sie gesperrt haben.
* Kritische Abschnitte können nicht dieselbe Entität mit mehreren parallelen Aufrufen aufrufen.
* Kritische Abschnitte können nur Entitäten signalisieren, die sie nicht gesperrt haben.

## <a name="alternate-storage-providers"></a>Alternativer Speicheranbieter

Das Durable Task Framework unterstützt heutzutage mehrere Speicheranbieter, einschließlich [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [In-Memory-Emulator](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator) sowie einen experimentellen [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis)-Anbieter. Bisher hat die Durable Task-Erweiterung für Azure Functions jedoch nur den Azure Storage-Anbieter unterstützt. Ab Durable Functions 2.0 wird Unterstützung für alternative Speicheranbieter hinzugefügt, beginnend mit dem Redis-Anbieter.

> [!NOTE]
> Durable Functions 2.0 unterstützt nur .NET Standard 2.0-kompatible Anbieter. Zum Zeitpunkt der Erstellung dieses Artikels unterstützt der Azure Service Bus-Anbieter kein .NET Standard 2.0 und ist deshalb nicht als alternativer Speicheranbieter verfügbar.

### <a name="emulator"></a>Emulator

Der [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/)-Anbieter ist ein Anbieter im lokalen Speicher für nicht dauerhaften Speicher, geeignet für lokale Testszenarios. Er kann mithilfe des folgenden, minimalen **host.json**-Schemas konfiguriert werden:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimentell)

Der [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/)-Anbieter legt den gesamten Orchestrierungszustand permanent in einem konfigurierten Redis-Cluster ab.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

Der `connectionStringName` muss auf den Namen einer App-Einstellung oder einer Umgebungsvariablen verweisen. Diese App-Einstellung oder Umgebungsvariable sollte einen Redis-Verbindungszeichenfolgenwert in Form von enthalten *Server:Port* enthalten. Z. B. `localhost:6379` zum Herstellen einer Verbindung mit einem lokalen Redis-Cluster.

> [!NOTE]
> Der Redis-Anbieter ist derzeit experimentell und unterstützt nur Funktions-Apps, die auf einem einzelnen Knoten ausgeführt wird.
