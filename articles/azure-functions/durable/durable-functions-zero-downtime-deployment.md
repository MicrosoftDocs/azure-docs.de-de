---
title: Bereitstellung ohne Ausfallzeit für Durable Functions
description: Erfahren Sie, wie Sie für Ihre Durable Functions-Orchestrierung Bereitstellungen ohne Ausfallzeit aktivieren.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 2c96f2cc37c47c77b82ca86d5fd0295f0c66a896
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009482"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Bereitstellung ohne Ausfallzeit für Durable Functions

Für das [Modell „Zuverlässige Ausführung“](./durable-functions-orchestrations.md) von Durable Functions müssen Orchestrierungen deterministisch sein. Dies ist eine zusätzliche Anforderung, die beim Bereitstellen von Updates erfüllt werden muss. Wenn eine Bereitstellung Änderungen der Signaturen von Aktivitätsfunktionen oder der Orchestratorlogik umfasst, tritt für ausgeführte Orchestrierungsinstanzen ein Fehler auf. Diese Situation ist besonders für Instanzen von zeitintensiven Orchestrierungen ein Problem, für die mehrere Stunden oder Tage an Arbeit anfallen können.

Um zu verhindern, dass diese Fehler auftreten, haben Sie zwei Möglichkeiten: 
- Verzögern Sie die Bereitstellung, bis alle aktuell ausgeführten Orchestrierungsinstanzen abgeschlossen wurden.
- Stellen Sie sicher, dass alle aktuell ausgeführten Orchestrierungsinstanzen die vorhandenen Versionen Ihrer Funktionen verwenden. 

Das folgende Diagramm enthält einen Vergleich der drei Hauptstrategien, um eine Bereitstellung ohne Ausfallzeit für Durable Functions zu erzielen: 

| Strategie |  Verwendung | Vorteile | Nachteile |
| -------- | ------------ | ---- | ---- |
| [Versionsverwaltung](#versioning) |  Anwendungen, für die es nicht häufig zu [Breaking Changes](durable-functions-versioning.md) kommt | Einfache Implementierung |  Erweiterte Funktions-App-Größe im Arbeitsspeicher und Anzahl von Funktionen<br/>Codeduplizierung |
| [Statusüberprüfung mit Slot](#status-check-with-slot) | Ein System, das keine zeitintensiven Orchestrierungen mit einer Länge von mehr als 24 Stunden oder sich häufig überlappenden Orchestrierungen aufweist. | Einfache Codebasis<br/>Keine zusätzliche Funktions-App-Verwaltung erforderlich | Zusätzliche Verwaltung des Speicherkontos oder des Aufgabenhubs erforderlich<br/>Zeiträume erforderlich, in denen keine Orchestrierungen ausgeführt werden |
| [Anwendungsrouting](#application-routing) | Ein System ohne Zeiträume, in denen keine Orchestrierungen ausgeführt werden, z.B. bei Zeiträumen mit Orchestrierungen mit einer Länge von mehr als 24 Stunden oder sich häufig überlappenden Orchestrierungen. | Verarbeitung neuer Versionen von Systemen mit fortlaufend ausgeführten Orchestrierungen, die Breaking Changes aufweisen | Intelligenter Anwendungsrouter erforderlich<br/>Mögliche Erreichung der maximal zulässigen Anzahl von Funktions-Apps für Ihr Abonnement. Der Standardwert ist 100. |

## <a name="versioning"></a>Versionsverwaltung

Definieren Sie neue Versionen Ihrer Funktionen, und belassen Sie die alten Versionen in Ihrer Funktions-App. Wie im Diagramm dargestellt, wird die Version einer Funktion zu einem Teil ihres Namens. Da frühere Versionen von Funktionen beibehalten werden, kann von ausgeführten Orchestrierungsinstanzen weiterhin darauf verwiesen werden. In der Zwischenzeit wird bei Anforderungen neuer Orchestrierungsinstanzen die aktuelle Version benötigt, auf die die Funktion Ihres Orchestrierungsclients über eine App-Einstellung verweisen kann.

![Strategie für Versionsverwaltung](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Bei dieser Strategie muss jede Funktion kopiert werden, und die zugehörigen Verweise auf andere Funktionen müssen aktualisiert werden. Sie können dies vereinfachen, indem Sie ein Skript schreiben. Hier ist ein [Beispielprojekt](https://github.com/TsuyoshiUshio/DurableVersioning) mit einem Migrationsskript.

>[!NOTE]
>Bei dieser Strategie werden Bereitstellungsslots verwendet, um Ausfälle während der Bereitstellung zu vermeiden. Ausführlichere Informationen zur Erstellung und Verwendung neuer Bereitstellungsslots finden Sie unter [Azure Functions-Bereitstellungsslots](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Statusüberprüfung mit Slot

Während die aktuelle Version Ihrer Funktions-App in Ihrem Produktionsslot ausgeführt wird, stellen Sie die neue Version der Funktions-App in Ihrem Stagingslot bereit. Überprüfen Sie vor dem Austauschen Ihrer Produktions- und Stagingslots, ob aktuell ausgeführte Orchestrierungsinstanzen vorhanden sind. Nachdem die Vorgänge für alle Orchestrierungsinstanzen abgeschlossen wurden, können Sie das Austauschen durchführen. Diese Strategie funktioniert, wenn Sie über vorhersagbare Zeiträume verfügen, in denen keine Orchestrierungsinstanzen ausgeführt werden. Dies ist der beste Ansatz, wenn Ihre Orchestrierungen nicht zeitintensiv sind und sich Ihre Orchestrierungsausführungen nicht häufig überlappen.

### <a name="function-app-configuration"></a>Konfiguration von Funktions-Apps

Verwenden Sie das folgende Verfahren, um dieses Szenario einzurichten.

1. [Fügen Sie Ihrer Funktions-App für Staging und Produktion Bereitstellungsslots hinzu](../functions-deployment-slots.md#add-a-slot).

1. Legen Sie für jeden Slot die [AzureWebJobsStorage-Anwendungseinstellung](../functions-app-settings.md#azurewebjobsstorage) auf die Verbindungszeichenfolge eines freigegebenen Speicherkontos fest. Diese Verbindungszeichenfolge für das Speicherkonto wird von der Azure Functions-Runtime verwendet. Dieses Konto wird von der Azure Functions-Runtime verwendet und verwaltet die Schlüssel der Funktion.

1. Erstellen Sie für jeden Slot eine neue App-Einstellung (z.B. `DurableManagementStorage`). Legen Sie den zugehörigen Wert auf die Verbindungszeichenfolge unterschiedlicher Speicherkonten fest. Diese Speicherkonten werden von der Durable Functions-Erweiterung für [zuverlässige Ausführung](./durable-functions-orchestrations.md) verwendet. Verwenden Sie ein separates Speicherkonto für jeden Slot. Markieren Sie diese Einstellung nicht als Einstellung für den Bereitstellungsslot.

1. Geben Sie im [Abschnitt „durableTask“ der Datei „host.json“](durable-functions-bindings.md#hostjson-settings) Ihrer Funktions-App `connectionStringName` (Durable 2.x) oder `azureStorageConnectionStringName` (Durable 1.x) als Namen der App-Einstellung an, die Sie in Schritt 3 erstellt haben.

Die folgende Abbildung zeigt die beschriebene Konfiguration mit Bereitstellungsslots und Speicherkonten. Bei diesem potenziellen Szenario vor der Bereitstellung wird Version 2 einer Funktions-App im Produktionsslot ausgeführt, während Version 1 im Stagingslot verbleibt.

![Bereitstellungsslots und Speicherkonten](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>Beispiele für „host.json“

Die folgenden JSON-Fragmente sind Beispiele für die Einstellung der Verbindungszeichenfolge in der Datei *host.json*.

#### <a name="functions-20"></a>Functions 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Konfiguration der CI/CD-Pipeline

Konfigurieren Sie Ihre CI/CD-Pipeline so, dass die Bereitstellung nur dann durchgeführt wird, wenn Ihre Funktions-App nicht über ausstehende oder ausgeführte Orchestrierungsinstanzen verfügt. Bei Verwendung von Azure Pipelines können Sie eine Funktion erstellen, mit der eine Überprüfung auf diese Bedingungen durchgeführt wird. Dies ist im folgenden Beispiel dargestellt:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Konfigurieren Sie als Nächstes das Staging-Gate, damit abgewartet wird, bis keine Orchestrierungen mehr ausgeführt werden. Weitere Informationen finden Sie unter [Steuerung von Releasebereitstellungen mit Gates](/azure/devops/pipelines/release/approvals/gates?view=azure-devops).

![Bereitstellungsgate](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines überprüft Ihre Funktions-App auf ausgeführte Orchestrierungsinstanzen, bevor Ihre Bereitstellung gestartet wird.

![Bereitstellungsgate (aktuell ausgeführt)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Die neue Version Ihrer Funktions-App sollte nun im Stagingslot bereitgestellt worden sein.

![Stagingslot](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Tauschen Sie abschließend die Slots aus. 

Anwendungseinstellungen, die nicht als Einstellungen für Bereitstellungsslots gekennzeichnet sind, werden ebenfalls ausgetauscht, sodass Version 2 weiterhin über den Verweis auf Speicherkonto A verfügt. Da der Orchestrierungszustand im Speicherkonto nachverfolgt wird, werden alle Orchestrierungen, die in Version 2 der App ausgeführt werden, im neuen Slot ohne Unterbrechung weiter ausgeführt.

![Bereitstellungsslot](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Wenn Sie für beide Slots dasselbe Speicherkonto nutzen möchten, können Sie die Namen Ihrer Aufgabenhubs ändern. In diesem Fall müssen Sie den Zustand Ihrer Slots und die HubName-Einstellungen Ihrer App verwalten. Weitere Informationen finden Sie unter [Aufgabenhubs in Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Anwendungsrouting

Dies ist die Strategie mit der höchsten Komplexität. Sie kann aber für Funktions-Apps genutzt werden, für die zwischen ausgeführten Orchestrierungen keine Zeit vorhanden ist.

Für diese Strategie müssen Sie vor Ihrer Durable Functions-Instanz einen *Anwendungsrouter* einrichten. Dieser Router kann mit Durable Functions implementiert werden. Der Router ist für folgende Aufgaben verantwortlich:

* Bereitstellen der Funktions-App.
* Verwalten der Durable Functions-Version. 
* Weiterleiten von Orchestrierungsanforderungen an Funktions-Apps.

Beim ersten Empfang einer Orchestrierungsanforderung werden vom Router die folgenden Aufgaben durchgeführt:

1. Erstellen einer neuen Funktions-App in Azure
2. Bereitstellen des Codes Ihrer Funktions-App für die neue Funktions-App in Azure
3. Weiterleiten der Orchestrierungsanforderung an die neue App

Der Router verwaltet den Zustand in Bezug darauf, welche Version Ihres App-Codes für welche Funktions-App in Azure bereitgestellt wird.

![Anwendungsrouting (erstmalig)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Der Router leitet Bereitstellungs- und Orchestrierungsanforderungen basierend auf der Version, die mit der Anforderung gesendet wurde, an die richtige Funktions-App weiter. Die Patchversion wird ignoriert.

Wenn Sie eine neue Version Ihrer App ohne Breaking Change bereitstellen, können Sie die Patchversion inkrementieren. Der Router führt die Bereitstellung für Ihre vorhandene Funktions-App durch und sendet Anforderungen für die alte und neue Version des Codes, die an dieselbe Funktions-App weitergeleitet werden.

![Anwendungsrouting (ohne Breaking Change)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Wenn Sie eine neue Version Ihrer App mit einer Breaking Change bereitstellen, können Sie die Haupt- oder Nebenversion inkrementieren. Anschließend erstellt der Anwendungsrouter eine neue Funktions-App in Azure, führt die Bereitstellung dafür durch und leitet Anforderungen für die neue Version Ihrer App weiter. In der folgenden Abbildung werden ausgeführte Orchestrierungen von Version 1.0.1 der App weiterhin ausgeführt, aber Anforderungen für Version 1.1.0 werden an die neue Funktions-App weitergeleitet.

![Anwendungsrouting (mit Breaking Change)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Der Router überwacht den Status von Orchestrierungen der Version 1.0.1 und entfernt die Apps, nachdem alle Orchestrierungen abgeschlossen wurden. 

### <a name="tracking-store-settings"></a>Nachverfolgen von Speichereinstellungen

Für jede Funktions-App sollten separate Zeitplanungswarteschlangen verwendet werden (ggf. in separaten Speicherkonten). Falls Sie übergreifend alle Orchestrierungsinstanzen für alle Versionen Ihrer Anwendung abfragen möchten, können Sie Instanz- und Verlaufstabellen für Ihre gesamten Funktions-Apps gemeinsam nutzen. Sie können Tabellen freigeben, indem Sie die `trackingStoreConnectionStringName`- und `trackingStoreNamePrefix`-Einstellungen in der Datei [host.json](durable-functions-bindings.md#host-json) so konfigurieren, dass jeweils die gleichen Werte verwendet werden.

Weitere Informationen finden Sie unter [Verwalten von Instanzen in Durable Functions in Azure](durable-functions-instance-management.md).

![Nachverfolgen von Speichereinstellungen](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Versionsverwaltung für Durable Functions](durable-functions-versioning.md)
