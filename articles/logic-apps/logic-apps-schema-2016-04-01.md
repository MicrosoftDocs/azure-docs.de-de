---
title: Schemaupdates 1. Juni 2016
description: Aktualisierte Schemaversion 2016-06-01 für Logik-App-Definitionen in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000316"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schemaaktualisierungen für Azure Logic Apps – 1. Juni 2016

Die [aktualisierte Schemaversion](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) und API-Version für Azure Logic Apps enthält wichtige Verbesserungen, mit denen Logik-Apps zuverlässiger und einfacher zu verwenden sind:

* Mit [Bereichen](#scopes) (Scopes) können Sie Aktionen als Sammlungen mit Aktionen gruppieren oder schachteln.
* [Bedingungen und Schleifen](#conditions-loops) sind jetzt erstklassige Aktionen.
* Genauere Sortierung der Ausführung von Aktionen mit der `runAfter`-Eigenschaft (ersetzt `dependsOn`)

Informationen zum Durchführen des Upgrades für Ihre Logik-App vom Schema vom 1. August 2015 (Vorschau) auf das Schema vom 1. Juni 2016 finden Sie im [entsprechenden Abschnitt](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Bereiche

Dieses Schema enthält Bereiche, mit denen Sie Aktionen gruppieren oder schachteln können. Beispielsweise kann eine Bedingung eine andere Bedingung enthalten. Informieren Sie sich unter [Schleifen, Bereiche und Auflösen von Batches in Logik-Apps](./logic-apps-control-flow-loops.md) über die Syntax von Bereichen, oder sehen Sie sich dieses einfache Beispiel zu Bereichen an:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Änderungen bei Bedingungen und Schleifen

In den bisherigen Schemaversionen handelte es sich bei Bedingungen und Schleifen um Parameter, die einer einzelnen Aktion zugeordnet waren. Mit diesem Schema wird diese Einschränkung aufgehoben, sodass Bedingungen und Schleifen jetzt als Aktionstypen verfügbar sind. Erfahren Sie mehr über [Schleifen und Bereiche](./logic-apps-control-flow-loops.md) und [Bedingungen](../logic-apps/logic-apps-control-flow-conditional-statement.md), oder arbeiten Sie dieses einfache Beispiel durch, das eine Bedingungsaktion zeigt:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>runAfter-Eigenschaft

Die `runAfter`-Eigenschaft ersetzt die `dependsOn`-Eigenschaft und ermöglicht eine höhere Genauigkeit beim Angeben der Ausführungsreihenfolge für Aktionen basierend auf dem Status vorheriger Aktionen. Die Eigenschaft `dependsOn` gab an, ob „die Aktion ausgeführt wurde und erfolgreich war“, ausgehend vom Erfolg, Fehlschlag oder Überspringen der vorhergehenden Aktion, nicht aber von der gewünschten Anzahl der Ausführungen der Aktion. Die Eigenschaft `runAfter` bietet Flexibilität als ein Objekt, das alle Namen von Aktionen angibt, nach denen das Objekt ausgeführt wird. Außerdem wird mit dieser Eigenschaft ein Array mit Statusangaben definiert, die als Trigger zulässig sind. Wenn Sie beispielsweise eine Aktion nach erfolgreicher Ausführung von Aktion A und ebenfalls nach dem Erfolg oder Fehlschlag von Aktion B ausführen möchten, richten Sie die `runAfter`-Eigenschaft wie folgt ein:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Durchführen des Upgrades für Ihr Schema

Um ein Upgrade auf die [neueste Schemaversion](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) durchzuführen, benötigen Sie nur wenige Schritte. Der Upgradeprozess umfasst das Ausführen des Upgradeskripts, das Speichern als neue Logik-App und, falls gewünscht, das Überschreiben der vorherigen Logik-App.

1. Öffnen Sie Ihre Logik-App im Azure-Portal.

2. Navigieren Sie zu **Übersicht**. Wählen Sie in der Logik-App-Symbolleiste die Option **Schema aktualisieren**.
   
   ![Auswählen von „Schema aktualisieren“][1]
   
   Die aktualisierte Definition wird zurückgegeben, und Sie können sie bei Bedarf kopieren und in eine Ressourcendefinition einfügen. 

   > [!IMPORTANT]
   > *Achten Sie darauf,* **Speichern unter** auszuwählen, damit alle Verbindungsverweise in der aktualisierten Logik-App gültig bleiben.

3. Wählen Sie in der Symbolleiste des Blatts für das Upgrade die Option **Speichern unter**.

4. Geben Sie den Logiknamen und -status ein. Wählen Sie die Option **Erstellen**, um die aktualisierte Logik-App bereitzustellen.

5. Vergewissern Sie sich, dass die aktualisierte Logik-App wie gewünscht funktioniert.
   
   > [!NOTE]
   > Bei Verwendung eines manuellen oder anforderungsbasierten Triggers ändert sich die Rückruf-URL in der neuen Logik-App. Testen Sie die neue URL, um sicherzustellen, dass alles richtig funktioniert. Zum Beibehalten der vorherigen URLs können Sie Ihre vorhandene Logik-App klonen.

6. *Optional*: Wählen Sie neben **Schema aktualisieren** die Option **Klonen**, um die vorherige Logik-App mit der neuen Schemaversion zu überschreiben. Dieser Schritt ist nur erforderlich, wenn Sie die Ressourcen-ID oder Anforderungstrigger-URL Ihrer Logik-App beibehalten möchten.

## <a name="upgrade-tool-notes"></a>Hinweise zum Upgradetool

### <a name="mapping-conditions"></a>Zuordnungsbedingungen

In der aktualisierten Definition gruppiert das Tool die „wahren“ und „falschen“ Verzweigungsaktionen so gut wie möglich als Bereiche. Insbesondere wird das Designermuster von `@equals(actions('a').status, 'Skipped')` als `else`-Aktion angezeigt. Wenn vom Tool aber nicht erkennbare Muster ermittelt werden, werden ggf. für die „wahre“ und die „falsche“ Verzweigung separate Bedingungen erstellt. Sie können die Aktionen nach dem Upgrade bei Bedarf neu zuordnen.

#### <a name="foreach-loop-with-condition"></a>foreach-Schleife mit Bedingung

Im neuen Schema können Sie die Filteraktion verwenden, um das Muster zu replizieren, das eine **For each**-Schleife mit einer Bedingung pro Element verwendet. Allerdings erfolgt die Änderung automatisch, wenn Sie ein Upgrade ausführen. Die Bedingung wird zu einer Filteraktion, die vor der **For each**-Schleife erscheint und nur ein Array mit Elementen zurückgibt, die die Bedingung erfüllen. Dieses Array wird dann an die **For each**-Aktion übergeben. Ein Beispiel hierzu finden Sie unter [Schleifen, Bereiche und Auflösen von Batches in Logik-Apps](./logic-apps-control-flow-loops.md).

### <a name="resource-tags"></a>Ressourcentags

Nach dem Upgrade sind die Ressourcentags nicht mehr vorhanden, sodass Sie sie für den aktualisierten Workflow zurücksetzen müssen.

## <a name="other-changes"></a>Weitere Änderungen

### <a name="renamed-manual-trigger-to-request-trigger"></a>Umbenennung des Triggers „manual“ in „request“

Der Triggertyp `manual` wurde ausgesondert und in `request` mit dem Typ `http` umbenannt. Mit dieser Änderung wird für mehr Konsistenz für die Art von Muster gesorgt, für dessen Erstellung der Trigger verwendet wird.

### <a name="new-filter-action"></a>Neue Filteraktion

Zum Filtern eines großen Arrays in eine kleinere Gruppe von Elementen akzeptiert der neue Typ `filter` ein Array und eine Bedingung, wertet die Bedingung für die einzelnen Elemente aus und gibt ein Array mit Elementen zurück, die diese Bedingung erfüllen.

### <a name="restrictions-for-foreach-and-until-actions"></a>Einschränkungen für die Aktionen „foreach“ und „until“

Die `foreach`- und `until`-Schleifen sind auf eine einzelne Aktion beschränkt.

### <a name="new-trackedproperties-for-actions"></a>Neue trackedProperties-Eigenschaft für Aktionen

Aktionen können jetzt über eine zusätzliche Eigenschaft mit dem Namen `trackedProperties` verfügen. Sie ist den Eigenschaften `runAfter` und `type` gleichgeordnet. Mit diesem Objekt werden bestimmte Aktionseingaben oder -ausgaben angegeben, die Sie im Rahmen der Ausgabe eines Workflows in die Azure-Diagnosetelemetrie einbeziehen möchten. Beispiel:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Logik-App-Definitionen](../logic-apps/logic-apps-author-definitions.md)
* [Automatisieren der Logik-App-Bereitstellung](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
