---
title: Grenzwerte und Konfiguration
description: Grenzwerte für Dienste wie Dauer, Durchsatz und Kapazität, sowie Konfigurationswerte wie zulässige IP-Adressen für Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: dc09edee08e97e354ef006416e2d5c0a333a3980
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917816"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Grenzwert- und Konfigurationsinformationen für Azure Logic Apps

In diesem Artikel sind die Einschränkungen und Konfigurationsdetails beschrieben, die beim Erstellen und Ausführen von automatisierten Workflows mit Azure Logic Apps zu beachten sind. Informationen zu Power Automate finden Sie unter [Grenzwerte und Konfiguration in Microsoft Flow](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definitionslimits

Dies sind die Grenzwerte für eine einzelne Logik-App-Definition:

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Aktionen pro Workflow | 500 | Zur Erhöhung dieses Grenzwerts können Sie bei Bedarf geschachtelte Workflows hinzufügen. |
| Zulässige Schachtelungstiefe für Aktionen | 8 | Zur Erhöhung dieses Grenzwerts können Sie bei Bedarf geschachtelte Workflows hinzufügen. |
| Workflows pro Region und Abonnement | 1\.000 | |
| Trigger pro Workflow | 10 | Wenn in der Codeansicht gearbeitet wird, nicht im Designer |
| Bereichsumschaltbegrenzung | 25 | |
| Variablen pro Workflow | 250 | |
| Name für `action` oder `trigger` | 80 Zeichen | |
| Zeichen pro Ausdruck | 8\.192 | |
| Länge von `description` | 256 Zeichen | |
| Maximale Anzahl von `parameters` | 50 | |
| Maximale Anzahl von `outputs` | 10 | |
| Maximale Größe für `trackedProperties` | 16.000 Zeichen |
| Inlinecodeaktion: maximale Anzahl von Codezeichen | 1\.024 Zeichen <p>Für eine Begrenzung auf maximal 100.000 Zeichen erstellen Sie Ihre Logik-Apps mit Visual Studio Code und der [Vorschauversion der **Azure Logic Apps**-Erweiterung](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md). |

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Grenzwerte für Ausführungsdauer und Aufbewahrungsverlauf

Dies sind die Grenzwerte für eine einzelne Ausführung der Logik-App:

| Name | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Ausführungsdauer | 90 Tage | 366 Tage | Die Ausführungsdauer wird mithilfe der Startzeit einer Ausführung und des durch die Workfloweinstellung [**Aufbewahrung des Ausführungsverlaufs in Tagen**](#change-duration) zum Startzeitpunkt festgelegten Grenzwerts berechnet. <p><p>Informationen zum Ändern des Standardgrenzwerts finden Sie unter [Ändern der Ausführungsdauer und Verlaufsaufbewahrung im Speicher](#change-duration). |
| Aufbewahrung des Ausführungsverlaufs im Speicher | 90 Tage | 366 Tage | Wenn die Dauer einer Ausführung den aktuellen Aufbewahrungsgrenzwert für Ausführungsverläufe überschreitet, wird die Ausführung aus dem Ausführungsverlauf im Speicher entfernt. Egal, ob die Ausführung abgeschlossen wird oder einen Timeout hat, wird die Aufbewahrung des Ausführungsverlaufs immer mithilfe der Startzeit der Ausführung und des durch die Workfloweinstellung [**Aufbewahrung des Ausführungsverlaufs in Tagen**](#change-retention) festgelegten aktuellen Grenzwerts berechnet. Unabhängig vom vorherigen Grenzwert wird immer der aktuelle Grenzwert zum Berechnen der Aufbewahrung verwendet. <p><p>Informationen zum Ändern des Standardgrenzwerts sowie weitere Informationen finden Sie unter [Ändern der Dauer und Aufbewahrung des Ausführungsverlaufs im Speicher](#change-retention). Zum Erhöhen des maximalen Grenzwerts [wenden Sie sich an das Logic Apps-Team ](mailto://logicappsemail@microsoft.com), um Hilfestellung zu Ihren Anforderungen zu erhalten. |
| Kürzestes Wiederholungsintervall | 1 Sekunde | 1 Sekunde ||
| Längstes Wiederholungsintervall | 500 Tage | 500 Tage ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Ändern von Ausführungsdauer und Verlaufsaufbewahrung im Speicher

Dieselbe Einstellung steuert die maximale Anzahl von Tagen, die ein Workflow ausgeführt werden kann, als auch für die Aufbewahrung des Ausführungsverlaufs im Speicher. Gehen Sie folgendermaßen vor, um den Standard- oder aktuellen Grenzwert für diese Eigenschaften zu ändern.

* Für Logik-Apps in Azure mit mehreren Mandanten ist der Standardgrenzwert von 90 Tagen gleich dem maximalen Grenzwert. Sie können diesen Wert nur verringern.

* Für Logik-Apps in einer Integrationsdienstumgebung können Sie den Standardgrenzwert von 90 Tagen verringern oder erhöhen.

Nehmen wir beispielsweise an, dass Sie den Aufbewahrungsgrenzwert von 90 Tagen auf 30 Tage verringern möchten. Eine 60 Tage alte Ausführung wird dann aus dem Ausführungsverlauf entfernt. Wenn Sie die Aufbewahrungsdauer von 30 Tagen auf 60 Tage erhöhen, verbleibt eine 20 Tage alte Ausführung für weitere 40 Tage im Ausführungsverlauf.

> [!IMPORTANT]
> Wenn die Dauer einer Ausführung den aktuellen Aufbewahrungsgrenzwert für Ausführungsverläufe überschreitet, wird die Ausführung aus dem Ausführungsverlauf im Speicher entfernt. Um den Verlust des Ausführungsverlaufs zu vermeiden, stellen Sie sicher, dass die Aufbewahrungsdauer *immer* länger als die längste mögliche Dauer einer Ausführung ist.

1. Suchen Sie Suchfeld des [Azure-Portals](https://portal.azure.com) nach **Logic Apps**, und wählen Sie es aus.

1. Suchen Sie Ihre Logik-App, und wählen Sie sie aus. Öffnen Sie Ihre Logik-App im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App **Workfloweinstellungen** aus.

1. Wählen Sie unter **Laufzeitoptionen** in der Liste **Aufbewahrung des Ausführungsverlaufs in Tagen** die Option **Benutzerdefiniert** aus.

1. Ziehen Sie den Schieberegler bis zur gewünschten Anzahl von Tagen.

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste **Workfloweinstellungen** die Option **Speichern** aus.

Wenn Sie für Ihre Logik-App eine Azure Resource Manager-Vorlage generieren, wird diese Einstellung als Eigenschaft in der Ressourcendefinition Ihres Workflows angezeigt, die in der [Microsoft.Logic-Workflows-Vorlagenreferenz](/azure/templates/microsoft.logic/workflows) beschrieben ist:

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Grenzwerte für Parallelität, Schleifen und Auflösen von Batches

Dies sind die Grenzwerte für eine einzelne Ausführung der Logik-App:

### <a name="loops"></a>Schleifen

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Foreach-Arrayelemente | 100.000 | Dieser Grenzwert beschreibt die maximale Anzahl von Arrayelementen, die eine Foreach-Schleife verarbeiten kann. <p><p>Sie können die [Abfrageaktion](logic-apps-perform-data-operations.md#filter-array-action) verwenden, um größere Arrays zu filtern. |
| Foreach-Parallelität | Bei deaktivierter Parallelität: 20 <p><p>Bei aktivierter Parallelität: <p><p>- Standardwert: 20 <br>- Min: 1 <br>- Max: 50 | Dieser Grenzwert entspricht der maximalen Anzahl von Foreach-Schleifeniterationen, die gleichzeitig bzw. parallel ausgeführt werden können. <p><p>Informationen zum Ändern dieses Grenzwerts finden Sie unter [Ändern der „for each“-Parallelität](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) und [Sequenzielles Ausführen von „for each“-Schleifen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Until-Iterationen | - Standardwert: 60 <br>- Min: 1 <br>- Max: 5.000 | Die maximale Anzahl von Zyklen, die eine Until-Schleife während einer Logik-App-Ausführung aufweisen kann. <p><p>Um diesen Grenzwert zu ändern, wählen Sie in der Until-Schleife die Option **Grenzwerte ändern** aus und geben einen Wert für die **Count**-Eigenschaft an. |
| Until-Timeout | - Standardwert: PT1H (1 Stunde) | Die maximale Zeitspanne, die die Until-Schleife vor dem Beenden ausgeführt wird. Die Angabe erfolgt im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601). Der Timeoutwert wird für jeden Schleifendurchlauf ausgewertet. Wenn eine Aktion in der Schleife länger als die Zeitüberschreitung dauert, wird der aktuelle Zyklus nicht beendet. Der nächste Zyklus beginnt jedoch nicht, weil die Grenzwertbedingung nicht erfüllt ist. <p><p>Um diesen Grenzwert zu ändern, wählen Sie in der Until-Schleife die Option **Grenzwerte ändern** aus und geben einen Wert für die **Timeout**-Eigenschaft an. |
||||

### <a name="concurrency-and-debatching"></a>Parallelität und Auflösen von Batches

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Triggerparallelität | Bei deaktivierter Parallelität: Unbegrenzt <p><p>Bei aktivierter Parallelität, die nach dem Aktivieren nicht rückgängig gemacht werden kann: <p><p>- Standardwert: 25 <br>- Min: 1 <br>- Max: 50 | Dieser Grenzwert gibt die maximale Anzahl von Logik-App-Instanzen an, die gleichzeitig bzw. parallel ausgeführt werden können. <p><p>**Hinweis**: Wenn Parallelität aktiviert ist, wird das SplitOn-Limit auf 100 Elemente für das [Auflösen von Arraybatches](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) reduziert. <p><p>Informationen zum Ändern dieses Grenzwerts finden Sie unter [Ändern der Triggerparallelität](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) und [Sequenzielles Auslösen von Instanzen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximale Anzahl von wartenden Ausführungen | Bei deaktivierter Parallelität: <p><p>- Min: 1 <br>- Max: 50 <p><p>Bei aktivierter Parallelität: <p><p>- Min: 10 plus die Anzahl gleichzeitiger Ausführungen (Triggerparallelität) <br>- Max: 100 | Dieser Grenzwert gibt die maximale Anzahl von Logik-App-Instanzen an, die auf die Ausführung warten können, wenn für Ihre Logik-App bereits die maximale Anzahl gleichzeitiger Instanzen ausgeführt wird. <p><p>Informationen zum Ändern dieses Grenzwerts finden Sie unter [Ändern des Limits für wartende Ausführungen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| SplitOn-Elemente | Bei deaktivierter Parallelität: 100.000 <p><p>Bei aktivierter Parallelität: 100 | Für Trigger, die ein Array zurückgeben, können Sie einen Ausdruck angeben, der eine SplitOn-Eigenschaft verwendet, um [Arrayelemente für die Verarbeitung in mehrere Workflowinstanzen aufzuteilen bzw. aufzulösen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch), anstatt eine Foreach-Schleife zu verwenden. Dieser Ausdruck verweist auf das Array, das zum Erstellen und Ausführen einer Workflowinstanz für jedes Arrayelement verwendet werden soll. <p><p>**Hinweis**: Wenn Parallelität aktiviert ist, wird das SplitOn-Limit auf 100 Elemente reduziert. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Durchsatzlimits

Dies sind die Grenzwerte für eine einzelne Logik-App-Definition:

### <a name="multi-tenant-logic-apps-service"></a>Logic Apps-Dienst mit mehreren Mandanten

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Aktion: Ausführungen pro 5 Minuten | 100.000 ist der Standardgrenzwert, aber 300.000 ist der maximale Grenzwert. | Weitere Informationen zum Erhöhen des Grenzwerts auf den Höchstwert für Ihre Logik-App finden Sie unter [Ausführen im Modus mit hohem Durchsatz](#run-high-throughput-mode) (Vorschauversion). Sie können bei Bedarf auch die [Workload auf mehrere Logik-Apps verteilen](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling). |
| Aktion: Gleichzeitig ausgehende Aufrufe | ca. 2.500 | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
| Endpunkt zur Laufzeit: Gleichzeitig eingehende Aufrufe | ca. 1.000 | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
| Endpunkt zur Laufzeit: Read-Aufrufe pro 5 Minuten  | 60.000 | Dieser Grenzwert gilt für Aufrufe, die die unformatierten Eingaben und Ausgaben aus dem Ausführungsverlauf einer Logik-App erhalten. Bei Bedarf können Sie die Workload auf mehrere Apps verteilen. |
| Endpunkt zur Laufzeit: Invoke-Aufrufe pro 5 Minuten | 45.000 | Bei Bedarf können Sie eine Workload auch auf mehrere Apps verteilen. |
| Inhaltsdurchsatz pro 5 Minuten | 600 MB | Bei Bedarf können Sie eine Workload auch auf mehrere Apps verteilen. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Ausführen im Modus mit hohem Durchsatz

Für eine einzelne Logik-App-Definition gilt für die Anzahl von Aktionen, die alle fünf Minuten ausgeführt werden, ein [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Zum Erhöhen des Grenzwerts auf den Höchstwert für Ihre Logik-App können Sie den Modus „Hoher Durchsatz“ aktivieren, der sich noch in der Vorschau befindet. Sie können bei Bedarf auch die [Workload auf mehrere Logik-Apps verteilen](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling).

1. Wählen Sie im Azure-Portal im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus.

1. Ändern Sie die Einstellung unter **Laufzeitoptionen** > **Hoher Durchsatz** in **Ein**.

   ![Screenshot des Logik-App-Menüs im Azure-Portal mit den „Workfloweinstellungen“ und dem Wert „Ein“ für die Einstellung „Hoher Durchsatz“](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Um diese Einstellung in einer ARM-Vorlage für die Bereitstellung Ihrer Logik-App zu aktivieren, fügen Sie im `properties`-Objekt für die Ressourcendefinition Ihrer Logik-App das `runtimeConfiguration`-Objekt hinzu, dessen `operationOptions`-Eigenschaft auf `OptimizedForHighThroughput` festgelegt ist:

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Weitere Informationen zur Ressourcendefinition Ihrer Logik-App finden Sie unter [Übersicht: Automatisieren der Bereitstellung für Azure Logic Apps durch Verwenden von Azure Resource Manager-Vorlagen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

### <a name="integration-service-environment-ise"></a>Integrationsdienstumgebung (Integration Service Environment, ISE)

Dies sind die Durchsatzgrenzwerte für die [ISE Premium-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

| Name | Begrenzung | Notizen |
|------|-------|-------|
| Ausführungsgrenzwert für eine Basiseinheit | System gedrosselt, wenn die Infrastrukturkapazität 80 % erreicht | Bietet ca. 4.000 Aktionsausführungen pro Minute, was rund 160 Mio. Aktionsausführungen pro Monat entspricht. | |
| Ausführungsgrenzwert für eine Skalierungseinheit | System gedrosselt, wenn die Infrastrukturkapazität 80 % erreicht | Jede Skalierungseinheit kann ca. 2.000 zusätzliche Aktionsausführungen pro Minute bereitstellen, was etwa 80 Mio. zusätzlichen Aktionsausführungen pro Monat entspricht. | |
| Maximale Skalierungseinheiten, die Sie hinzufügen können | 10 | |
||||

Wenn diese Grenzwerte bei der normalen Verarbeitung überschritten oder Auslastungstests ausgeführt werden sollen, bei denen diese Grenzwerte möglicherweise überschritten werden, [bitten Sie das Logic Apps-Team](mailto://logicappsemail@microsoft.com) um Unterstützung im Hinblick auf Ihre Anforderungen.

> [!NOTE]
> Für die [Developer ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) gibt es keine veröffentlichten Grenzwerte, keine Funktionen zum Hochskalieren und keine Vereinbarung zum Service Level (Service-Level Agreement, SLA). Verwenden Sie diese SKU nur für Experimente, Entwicklung und Tests, nicht jedoch für die Produktion oder Leistungstests.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Einschränkungen für Gateway

Azure Logic Apps unterstützt Schreibvorgänge, einschließlich Einfügungen und Aktualisierungen, über das Gateway. Allerdings besitzen diese Vorgänge [Limits hinsichtlich Ihrer Nutzlastgröße](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-Grenzwerte

Hier finden Sie die Grenzwerte für einen ausgehenden oder eingehenden HTTP-Aufruf:

#### <a name="timeout"></a>Timeout

Einige Connectorvorgänge führen asynchrone Aufrufe aus oder lauschen auf Webhookanforderungen, sodass das Timeout für diese Vorgänge länger sein kann, als diese Grenzwerte angeben. Weitere Informationen hierzu finden Sie in den technischen Details für den bestimmten Connector sowie unter [Workflowtrigger und -aktionen](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Ausgehende Anforderung | 120 Sekunden <br>(2 Minuten) | 240 Sekunden <br>(4 Minuten) | Beispiele ausgehender Anforderungen umfassen Aufrufe durch HTTP-Trigger. <p><p>**Tipp**: Verwenden Sie für Vorgänge, die länger ausgeführt werden, ein [asynchrones Abrufmuster](../logic-apps/logic-apps-create-api-app.md#async-pattern) oder eine [Until-Schleife](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Um Timeoutlimits zu umgehen, wenn Sie eine andere Logik-App aufrufen, die einen [aufrufbaren Endpunkt](logic-apps-http-endpoint.md) besitzt, können Sie stattdessen die integrierte Azure Logic Apps-Aktion verwenden, die Sie in der Connectorauswahl unter **Integriert** finden. |
| Eingehende Anforderungen | 120 Sekunden <br>(2 Minuten) | 240 Sekunden <br>(4 Minuten) | Beispiele eingehender Anforderungen umfassen Aufrufe, die durch Anforderungstrigger und Webhooktrigger empfangen wurden. <p><p>**Hinweis**: Damit der ursprüngliche Aufrufer die Antwort erhält, müssen alle Schritte in der Antwort innerhalb des Grenzwerts abgeschlossen werden, es sei denn, Sie rufen eine andere Logik-App als geschachtelten Workflow auf. Weitere Informationen hierzu finden Sie unter [Aufrufen, Auslösen oder Schachteln von Logik-Apps](../logic-apps/logic-apps-http-endpoint.md). |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Nachrichtengröße

| Name | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Nachrichtengröße | 100 MB | 200 MB | Informationen, wie Sie diese Beschränkung umgehen können, finden Sie unter [Verarbeiten von großen Nachrichten durch Blockerstellung in Logic Apps](../logic-apps/logic-apps-handle-large-messages.md). Es kann aber sein, dass einige Connectors und APIs Blockerstellung (Segmentierung) oder sogar den Standardgrenzwert nicht unterstützen. <p><p>- Connectors wie AS2, X12 und EDIFACT verfügen über eigene [B2B-Nachrichtenlimits](#b2b-protocol-limits). <br>- ISE-Connectors verwenden den ISE-Grenzwert, nicht ihre Grenzwerte für Nicht-ISE-Connectors. |
| Nachrichtengröße mit Blockerstellung (Segmentierung) | 1 GB | 5 GB | Dieser Grenzwert gilt für Aktionen, die die Blockerstellung automatisch unterstützen, oder für die Sie die Blockerstellung in der Laufzeitkonfiguration aktivieren können. <p><p>Wenn Sie eine ISE verwenden, unterstützt die Logic Apps-Engine diesen Grenzwert. Connectors verfügen jedoch über eigene Blockerstellungsgrenzwerte bis zum Grenzwert der Engine. Beachten Sie hierzu z. B. die Informationen in der [Referenz zur API des Azure Blob Storage-Connectors](/connectors/azureblob/). Weitere Informationen zur Blockerstellung finden Sie unter [Verarbeiten von großen Nachrichten durch Blockerstellung](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Zeichengrenzwerte

| Name | Notizen |
|------|-------|
| Grenzwert für die Auswertung von Ausdrücken | 131.072 Zeichen | Keiner der Ausdrücke `@concat()`, `@base64()` und `@string()` darf länger sein, als dieser Grenzwert angibt. |
| Zeichengrenzwert für Anforderungs-URL | 16.384 Zeichen |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Wiederholungsrichtlinie

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Wiederholungsversuche | 90 | Der Standardwert ist 4. Um den Standardwert zu ändern, verwenden Sie den [Parameter für Richtlinienwiederholung](../logic-apps/logic-apps-workflow-actions-triggers.md) (retryPolicy). |
| Maximale Wiederholungsverzögerung | 1 Tag | Um den Standardwert zu ändern, verwenden Sie den [Parameter für Richtlinienwiederholung](../logic-apps/logic-apps-workflow-actions-triggers.md) (retryPolicy). |
| Minimale Wiederholungsverzögerung | 5 Sekunden | Um den Standardwert zu ändern, verwenden Sie den [Parameter für Richtlinienwiederholung](../logic-apps/logic-apps-workflow-actions-triggers.md) (retryPolicy). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Authentifizierungslimits

Hier finden Sie die Limits für eine Logik-App, die mit einem Anforderungstrigger startet und [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) für die Autorisierung eingehender Aufrufe an den Anforderungstrigger aktiviert:

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Azure AD-Autorisierungsrichtlinien | 5 | |
| Ansprüche pro Autorisierungsrichtlinie | 10 | |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Grenzwerte für einen benutzerdefinierten Connector

Die folgenden Grenzwerte gelten für benutzerdefinierte Connectors, die Sie über Web-APIs erstellen können.

| Name | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Anzahl von benutzerdefinierten Connectors | 1\.000 pro Azure-Abonnement | 1\.000 pro Azure-Abonnement ||
| Anzahl von Anforderungen pro Minute für einen benutzerdefinierten Connector | 500 Anforderungen pro Minute und Verbindung | 2\.000 Anforderungen pro Minute und *benutzerdefiniertem Connector* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Verwaltete Identitäten

| Name | Begrenzung |
|------|-------|
| Verwaltete Identitäten pro Logik-App | Entweder die vom System zugewiesene Identität oder eine benutzerseitig zugewiesene Identität |
| Anzahl von Logik-Apps mit einer verwalteten Identität in einem Azure-Abonnement pro Region | 1\.000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Grenzwerte für Integrationskonten

Für jedes Azure-Abonnement gelten folgende Grenzwerte für das Integrationskonto:

* Ein Integrationskonto im [Tarif „Free“](../logic-apps/logic-apps-pricing.md#integration-accounts) pro Azure-Region. Diese Dienstebene ist nur für öffentliche Regionen in Azure verfügbar, z. B. „USA, Westen“ oder „Asien, Südosten“, aber nicht für [Azure China 21ViaNet](/azure/china/overview-operations) oder [Azure Government](../azure-government/documentation-government-welcome.md).

* 1\.000 Integrationskonten insgesamt, einschließlich Integrationskonten in beliebigen [Integrationsdienstumgebungen (Integration Service Environments, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) in [Developer- und Premium-SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Jede ISE, ob [Developer oder Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), ist auf insgesamt 20 Integrationskonten beschränkt:

  | ISE SKU | Grenzwerte für Integrationskonten |
  |---------|----------------------------|
  | **Premium** | Insgesamt 20: nur [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts)-Konten, einschließlich eines kostenlosen Standard-Kontos. Es sind keine Free- oder Basic-Konten zulässig. |
  | **Developer** | Insgesamt 20: Kombination aus [Free](../logic-apps/logic-apps-pricing.md#integration-accounts) (auf 1 Konto beschränkt) und [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) oder nur Standard-Konten. Es sind keine Basic-Konten zulässig. Verwenden Sie die [Developer-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) für Experimente, Entwicklung und Tests, jedoch nicht für die Produktion oder Leistungstests. |
  |||

Zusätzliche Kosten fallen für Integrationskonten an, die Sie über die Integrationskonten hinaus hinzufügen, die in einer ISE enthalten sind. Weitere Informationen zur Preisgestaltung und Abrechnung für ISEs finden Sie unter [Feststehendes Preismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefaktgrenzwerte pro Integrationskonto

Die folgenden Grenzwerte gelten für die Anzahl von Artefakten für jeden Integrationskontotarif.
Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/). Informationen zur Preisgestaltung und Abrechnung für Integrationskonten finden Sie unter [Integrationskonten](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Verwenden Sie den Free-Tarif nur für Versuchsszenarios und nicht für Produktionsszenarios. Dieser Tarif beschränkt Durchsatz und Nutzung und ist nicht mit einer Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) verbunden.

| Artefakt | Kostenlos | Basic | Standard |
|----------|------|-------|----------|
| EDI-Handelsverträge | 10 | 1 | 1\.000 |
| EDI-Handelspartner | 25 | 2 | 1\.000 |
| Karten | 25 | 500 | 1\.000 |
| Schemas | 25 | 500 | 1\.000 |
| Assemblys | 10 | 25 | 1\.000 |
| Zertifikate | 25 | 2 | 1\.000 |
| Batchkonfigurationen | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefaktkapazitätsgrenzen

| Artefakt | Begrenzung | Notizen |
| -------- | ----- | ----- |
| Assembly | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB ein [Azure Storage-Konto und einen Blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Zuordnung (XSLT-Datei) | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB die [Zuordnungen der REST-API für Azure Logic Apps](/rest/api/logic/maps/createorupdate). <p><p>**Hinweis**: Die Menge der Daten oder Datensätze, die eine Zuordnung erfolgreich verarbeiten kann, basiert auf den Grenzwerten für Nachrichtengröße und Aktionstimeout in Azure Logic Apps. Wenn Sie z. B. eine HTTP-Aktion verwenden, die auf [HTTP-Nachrichtengröße und -Timeoutlimits](#request-limits) basiert, kann eine Zuordnung Daten bis zum HTTP-Nachrichtengrößenlimit verarbeiten, wenn der Vorgang innerhalb des HTTP-Timeoutlimits abgeschlossen wird. |
| Schema | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB ein [Azure Storage-Konto und einen Blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Durchsatzlimits

| Endpunkt zur Laufzeit | Kostenlos | Basic | Standard | Notizen |
|------------------|------|-------|----------|-------|
| Leseaufrufe pro 5 Minuten | 3,000 | 30.000 | 60.000 | Dieser Grenzwert gilt für Aufrufe, die die unformatierten Eingaben und Ausgaben aus dem Ausführungsverlauf einer Logik-App erhalten. Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Aufrufe pro 5 Minuten | 3,000 | 30.000 | 45.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Nachverfolgungsaufrufe pro 5 Minuten | 3,000 | 30.000 | 45.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Gleichzeitige Blockierungsaufrufe | ca. 1.000 | ca. 1.000 | ca. 1.000 | Identisch für alle SKUs. Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Nachrichtengröße für B2B-Protokoll (AS2, X12, EDIFACT)

Folgende Grenzwerte für die Nachrichtengröße gelten für B2B-Protokolle:

| Name | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 25 MB | v2 – 200 MB <br>v1 – 25 MB | Gilt für das Decodieren und das Codieren |
| X12 | 50 MB | 50 MB | Gilt für das Decodieren und das Codieren |
| EDIFACT | 50 MB | 50 MB | Gilt für das Decodieren und das Codieren |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Deaktivieren oder Löschen von Logik-Apps

Wenn Sie eine Logik-App deaktivieren, werden keine neuen Ausführungen instanziiert. Alle in Bearbeitung befindlichen und ausstehenden Ausführungen werden bis zum Ende fortgesetzt, was einige Zeit in Anspruch nehmen kann.

Wenn Sie eine Logik-App löschen, werden keine neuen Ausführungen instanziiert. Alle in Bearbeitung befindlichen und ausstehenden Ausführungen werden abgebrochen. Bei Tausenden von Ausführungen kann der Abbruch möglicherweise erhebliche Zeit in Anspruch nehmen.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Firewallkonfiguration: IP-Adressen und Diensttags

Die IP-Adressen, die Azure Logic Apps für eingehende und ausgehende Aufrufe verwendet, hängen von der Region ab, in der sich Ihre Logik-App befindet. Für *alle* Logik-Apps in derselben Region werden dieselben IP-Adressbereiche verwendet. Einige [Power Automate](/power-automate/getting-started)-Aufrufe, z. B. **HTTP**- und **HTTP + OpenAPI**-Anforderungen, werden direkt über den Azure Logic Apps-Dienst geleitet und stammen von den hier aufgeführten IP-Adressen. Weitere Informationen zu von Power Automate verwendeten IP-Adressen finden Sie unter [Grenzwerte und Konfiguration in Microsoft Flow](/flow/limits-and-config#ip-address-configuration).

> [!TIP]
> Zur Reduzierung der Komplexität beim Erstellen von Sicherheitsregeln können Sie optional [Diensttags](../virtual-network/service-tags-overview.md) verwenden, anstatt die Logic Apps-IP-Adressen für jede Region anzugeben (weiter unten in diesem Abschnitt beschrieben).
> Diese Tags funktionieren in den Regionen, in denen der Logic Apps-Dienst verfügbar ist:
>
> * **LogicAppsManagement**: Steht für die IP-Adresspräfixe des Logic Apps-Diensts in eingehender Richtung.
> * **LogicApps**: Steht für die IP-Adresspräfixe des Logic Apps-Diensts in ausgehender Richtung.

* Für [Azure China 21Vianet](/azure/china/) sind keine festen oder reservierten IP-Adressen für [benutzerdefinierte Connectors](../logic-apps/custom-connector-overview.md) und [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors) (z. B. Azure Storage, SQL Server, Office 365 Outlook usw.) verfügbar.

* Um die Aufrufe zu unterstützen, die Ihre Logik-Apps direkt mit [HTTP](../connectors/connectors-native-http.md)., [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)- und anderen HTTP-Anforderungen durchführen, richten Sie Ihre Firewalls mit allen [eingehenden](#inbound) *und* [ausgehenden](#outbound) IP-Adressen ein, die vom Logic Apps-Dienst verwendet werden. Führen Sie dies basierend auf den Regionen durch, in denen sich Ihre Logik-Apps befinden. Diese Adressen werden unter den Überschriften **Eingehend** und **Ausgehend** in diesem Abschnitt angezeigt, wobei sie nach Region sortiert sind.

* Um die Aufrufe zu unterstützen, die von [verwalteten Connectors](../connectors/apis-list.md#managed-api-connectors) ausgeführt werden, richten Sie Ihre Firewall mit *allen* [ausgehenden](#outbound) IP-Adressen ein, die von diesen Connectors verwendet werden, basierend auf den Regionen, in denen sich Ihre Logik-Apps befinden. Diese Adressen werden unter der Überschrift **Ausgehend** in diesem Abschnitt angezeigt, wobei sie nach Region sortiert sind.

* Zum Ermöglichen der Kommunikation für Logik-Apps, die in einer Integrationsdienstumgebung (ISE) ausgeführt werden, müssen Sie [diese Ports öffnen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Wenn Ihre Logik-Apps Probleme beim Zugriff auf Azure-Speicherkonten mit aktiven [Firewalls und Firewallregeln](../storage/common/storage-network-security.md) haben, stehen Ihnen [verschiedene Optionen zum Ermöglichen des Zugriffs](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls) zur Verfügung.

  Beispielsweise können Logik-Apps nicht direkt auf Speicherkonten zugreifen, für die Firewallregeln gelten und die sich in derselben Region befinden. Wenn Sie jedoch die [ausgehenden IP-Adressen für verwaltete Connectors in Ihrer Region](../logic-apps/logic-apps-limits-and-config.md#outbound) zulassen, können Ihre Logik-Apps auf Speicherkonten in einer anderen Region zugreifen, außer wenn Sie den Azure Table Storage- oder Azure Queue Storage-Connector verwenden. Um auf Ihren Table Storage oder Queue Storage zuzugreifen, können Sie stattdessen HTTP-Trigger und -Aktionen verwenden. Weitere Optionen finden Sie unter [Zugreifen auf Speicherkonten hinter Firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>IP-Adressen für eingehende Richtung

In diesem Abschnitt sind nur die IP-Adressen des Azure Logic Apps-Diensts für die eingehende Richtung aufgeführt. Informationen zur Vorgehensweise bei Verwendung von Azure Government finden Sie unter [Azure Government: IP-Adressen für die eingehende Richtung](#azure-government-inbound).

> [!TIP]
> Zur Reduzierung der Komplexität beim Erstellen von Sicherheitsregeln können Sie optional das [Diensttag](../virtual-network/service-tags-overview.md) **LogicAppsManagement** verwenden, anstatt für jede Region Logic Apps-IP-Adresspräfixe für die eingehende Richtung anzugeben.
> Dieses Tag funktioniert in allen Regionen, in denen der Logic Apps-Dienst verfügbar ist.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Mehrinstanzenfähige Azure-Umgebung: IP-Adressen für die eingehende Richtung

| Mehrinstanzenfähige Region | IP |
|---------------------|----|
| Australien (Osten) | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australien, Südosten | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasilien Süd | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Kanada, Mitte | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada, Osten | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indien, Mitte | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA (Mitte) | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Asien, Osten | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA (Ost) 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Frankreich, Mitte | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Frankreich, Süden | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Deutschland, Norden | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Deutschland, Westen-Mitte | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Japan, Osten | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan, Westen | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea, Mitte | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea, Süden | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA Nord Mitte | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Nordeuropa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Südafrika, Norden | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Südafrika, Westen | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA Süd Mitte | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indien (Süden) | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asien, Südosten | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Schweiz, Norden | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| VAE, Mitte | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| UK, Süden | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| UK, Westen | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA, Westen-Mitte | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa, Westen | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Indien, Westen | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA (Westen) | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA, Westen 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government: IP-Adressen für die eingehende Richtung

| Azure Government-Region | IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Government, Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD, Mitte | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>IP-Adressen für die ausgehende Richtung

In diesem Abschnitt sind die IP-Adressen für die ausgehende Richtung aufgeführt, die für den Azure Logic Apps-Dienst und die verwalteten Connectors gelten. Informationen zur Vorgehensweise bei Verwendung von Azure Government finden Sie unter [Azure Government: IP-Adressen für die ausgehende Richtung](#azure-government-outbound).

> [!TIP]
> Zur Reduzierung der Komplexität beim Erstellen von Sicherheitsregeln können Sie optional das [Diensttag](../virtual-network/service-tags-overview.md) **LogicApps** verwenden, anstatt für jede Region Logic Apps-IP-Adresspräfixe für die ausgehende Richtung anzugeben.
> Bei verwalteten Connectors können Sie optional das **AzureConnectors**-Diensttag verwenden, anstatt für jede Region IP-Adresspräfixe des verwalteten Connectors für die ausgehende Richtung anzugeben. Diese Tags funktionieren in den Regionen, in denen der Logic Apps-Dienst verfügbar ist. 

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Mehrinstanzenfähige Azure-Umgebung: IP-Adressen für die ausgehende Richtung

| Mehrinstanzenfähige Region | IP der Logik-Apps | IP der verwalteten Connectors |
|---------------------|---------------|-----------------------|
| Australien (Osten) | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Australien, Südosten | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Brasilien Süd | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Kanada, Mitte | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Kanada, Osten | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Indien, Mitte | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| USA (Mitte) | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Asien, Osten | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| USA (Ost) 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Frankreich, Mitte | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Frankreich, Süden | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Deutschland, Norden | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Deutschland, Westen-Mitte | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Japan, Osten | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Japan, Westen | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Korea, Mitte | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Korea, Süden | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| USA Nord Mitte | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Nordeuropa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Südafrika, Norden | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 – 102.133.155.15, 102.133.253.0 – 102.133.253.31 |
| Südafrika, Westen | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| USA Süd Mitte | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Indien (Süden) | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Asien, Südosten | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Schweiz, Norden | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| VAE, Mitte | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| UK, Süden | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| UK, Westen | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| USA, Westen-Mitte | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa, Westen | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Indien, Westen | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| USA (Westen) | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| USA, Westen 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government: IP-Adressen für die ausgehende Richtung

| Region | IP der Logik-Apps | IP der verwalteten Connectors |
|--------|---------------|-----------------------|
| US DoD, Mitte | 52.182.48.215, 52.182.92.143 | 52.127.58.160 – 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 – 52.127.61.223 |
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 – 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 – 52.127.5.255 |
| US Gov Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 – 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 – 20.140.137.159 |
| US Government, Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Ihre erste Logik-App erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).
* Lernen Sie [allgemeine Beispiele und Szenarien](../logic-apps/logic-apps-examples-and-scenarios.md) kennen.
