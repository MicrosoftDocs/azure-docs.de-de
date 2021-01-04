---
title: Verhindern von anonymem öffentlichem Lesezugriff auf Container und Blobs
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie anonyme Anforderungen für ein Speicherkonto analysieren und anonymen Zugriff für das gesamte Speicherkonto oder für einen einzelnen Container verhindern können.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: f12a899d3b6daa3b233e6a799871afca1e24d046
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533745"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Verhindern von anonymem öffentlichem Lesezugriff auf Container und Blobs

Anonymer öffentlicher Lesezugriff auf Container und Blobs in Azure Storage ist eine praktische Methode für die Weitergabe von Daten, stellt aber unter Umständen auch ein Sicherheitsrisiko dar. Anonymer Zugriff muss mit Bedacht verwaltet werden, und Sie müssen verstehen, wie Sie den anonymen Zugriff auf Ihre Daten auswerten können. Betriebliche Komplexität, menschliche Fehler oder böswillige Angriffe auf öffentlich zugängliche Daten können kostspielige Datenschutzverletzungen zur Folge haben. Microsoft empfiehlt, den anonymen Zugriff nur zu aktivieren, wenn dies für Ihr Anwendungsszenario erforderlich ist.

Standardmäßig ist der öffentliche Zugriff auf Ihre Blobdaten immer verboten. Die Standardkonfiguration für ein Speicherkonto gestattet es einem Benutzer mit entsprechenden Berechtigungen jedoch, den öffentlichen Zugriff auf Container und Blobs in einem Speicherkonto zu konfigurieren. Zur Erhöhung der Sicherheit können Sie den gesamten öffentlichen Zugriff auf das Speicherkonto sperren, unabhängig von der Einstellung für den öffentlichen Zugriff für einen einzelnen Container. Wenn Sie den öffentlichen Zugriff auf das Speicherkonto nicht zulassen, wird verhindert, dass ein Benutzer den öffentlichen Zugriff für einen Container im Konto aktiviert. Microsoft empfiehlt, den öffentlichen Zugriff auf ein Speicherkonto zu verbieten, es sei denn, es ist für Ihr Szenario erforderlich. Wenn Sie den öffentlichen Zugriff nicht zulassen, können Sie Sicherheitsverletzungen bei den Daten verhindern, die durch den unerwünschten anonymen Zugriff verursacht werden.

Wenn Sie den öffentlichen Blobzugriff für das Speicherkonto verbieten, lehnt Azure Storage alle anonymen Anforderungen an dieses Konto ab. Nachdem der öffentliche Zugriff für ein Konto gesperrt wurde, können Container in diesem Konto nachträglich nicht mehr für den öffentlichen Zugriff konfiguriert werden. Container, die bereits für öffentlichen Zugriff konfiguriert wurden, akzeptieren keine anonymen Anforderungen mehr. Weitere Informationen finden Sie unter [Konfigurieren des anonymen öffentlichen Lesezugriffs für Container und Blobs](anonymous-read-access-configure.md).

In diesem Artikel wird beschrieben, wie Sie ein DRAG-Framework (Detection-Remediation-Audit-Governance) zur kontinuierlichen Verwaltung des öffentlichen Zugriffs für Ihre Speicherkonten verwenden können.

## <a name="detect-anonymous-requests-from-client-applications"></a>Erkennen anonymer Anforderungen von Clientanwendungen

Wenn Sie öffentlichen Lesezugriff für ein Speicherkonto verweigern, besteht die Gefahr, dass Anforderungen für Container und Blobs abgelehnt werden, die zurzeit für öffentlichen Zugriff konfiguriert sind. Durch Verweigerung des öffentlichen Zugriffs für ein Speicherkonto werden die Einstellungen für den öffentlichen Zugriff für einzelne Container in diesem Konto außer Kraft gesetzt. Wenn der öffentliche Zugriff für das Speicherkonto verweigert wird, tritt bei künftigen anonymen Anforderungen für dieses Konto ein Fehler auf.

Wenn Sie die möglichen Auswirkungen einer Verweigerung des öffentlichen Zugriffs auf Clientanwendungen verstehen möchten, empfiehlt Microsoft, dass Sie die Protokollierung und Metriken für dieses Konto aktivieren sowie Muster anonymer Anforderungen über einen Zeitraum analysieren. Verwenden Sie Metriken, um die Anzahl anonymer Anforderungen für das Speicherkonto zu ermitteln, und Protokolle, um zu ermitteln, auf welche Container anonym zugegriffen wird.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Überwachen anonymer Anforderungen mit dem Metrik-Explorer

Verwenden Sie den Azure-Metrik-Explorer im Azure-Portal, um anonyme Anforderungen für ein Speicherkonto nachzuverfolgen. Weitere Informationen zum Metrik-Explorer finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Gehen Sie wie folgt vor, um eine Metrik zur Nachverfolgung anonymer Anforderungen zu erstellen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal. Wählen Sie im Abschnitt **Überwachung** die Option **Metriken** aus.
1. Wählen Sie **Metrik hinzufügen** aus. Geben Sie im Dialogfeld **Metrik** die folgenden Werte an:
    1. Behalten Sie im Bereichsfeld den Namen des Speicherkontos bei.
    1. Legen Sie **Metriknamespace** auf *Blob* fest. Von dieser Metrik werden nur Anforderungen für Blobspeicher gemeldet.
    1. Legen Sie das Feld **Metrik** auf *Transaktionen* fest.
    1. Legen Sie das Feld **Aggregation** auf *Summe* fest.

    Die neue Metrik liefert die Summe der Transaktionen für Blobspeicher in einem bestimmten Zeitraum. Die resultierende Metrik sieht wie folgt aus:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Screenshot: Konfigurieren einer Metrik zum Summieren von Blobtransaktionen":::

1. Wählen Sie als Nächstes die Schaltfläche **Filter hinzufügen** aus, um einen Filter für die Metrik für anonyme Anforderungen zu erstellen.
1. Geben Sie im Dialogfeld **Filter** die folgenden Werte an:
    1. Legen Sie **Eigenschaft** auf *Authentifizierung* fest.
    1. Legen Sie das Feld **Operator** auf das Gleichheitszeichen (=) fest.
    1. Legen Sie das Feld **Werte** auf *Anonym* fest.
1. Wählen Sie rechts oben das gewünschte Zeitintervall für die Metrik aus. Sie können auch angeben, wie präzise die Aggregation von Anforderungen sein soll. Hierzu können Intervalle zwischen einer Minute und einem Monat angegeben werden.

Nachdem Sie die Metrik konfiguriert haben, werden nach und nach anonyme Anforderungen im Diagramm angezeigt. Die folgende Abbildung zeigt aggregierte anonyme Anforderungen der letzten 30 Minuten:

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Screenshot: Aggregierte anonyme Anforderungen für Blobspeicher":::

Sie können auch eine Warnungsregel konfigurieren, um benachrichtigt zu werden, wenn eine bestimmte Anzahl anonymer Anforderungen an Ihr Speicherkonto gerichtet wird. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analysieren von Protokollen zur Identifizierung von Containern, die anonyme Anforderungen erhalten

In Azure Storage-Protokollen werden Details zu Anforderungen für das Speicherkonto erfasst. Hierzu zählt auch die Information, wie eine Anforderung autorisiert wurde. Sie können die Protokolle analysieren, um zu ermitteln, von welchen Containern anonyme Anforderungen empfangen werden.

Wenn Sie Anforderungen für Ihr Azure Storage-Konto protokollieren möchten, um anonyme Anforderungen zu untersuchen, können Sie die Azure Storage-Protokollierung in Azure Monitor (Vorschauversion) verwenden. Weitere Informationen finden Sie unter [Überwachen von Azure Storage](./monitor-blob-storage.md).

Die Azure Storage-Protokollierung in Azure Monitor unterstützt die Verwendung von Protokollabfragen für die Analyse von Protokolldaten. Für die Abfrage von Protokollen können Sie einen Azure Log Analytics-Arbeitsbereich verwenden. Weitere Informationen zu Protokollabfragen finden Sie unter [Tutorial: Erste Schritte mit Log Analytics-Abfragen](../../azure-monitor/log-query/log-analytics-tutorial.md).

> [!NOTE]
> Die Vorschauversion der Azure Storage-Protokollierung in Azure Monitor wird nur in der öffentlichen Azure-Cloud unterstützt. Government-Clouds unterstützten die Protokollierung für Azure Storage mit Azure Monitor nicht.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Erstellen einer Diagnoseeinstellung im Azure-Portal

Wenn Sie Azure Storage-Daten mit Azure Monitor protokollieren und mit Azure Log Analytics analysieren möchten, müssen Sie zunächst eine Diagnoseeinstellung erstellen, die angibt, welche Anforderungstypen und für welche Speicherdienste Daten protokolliert werden sollen. Führen Sie zum Erstellen einer Diagnoseeinstellung im Azure-Portal die folgenden Schritte aus:

1. Registrieren Sie sich für die [Azure Storage-Protokollierung in Azure Monitor (Vorschauversion)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Erstellen Sie in dem Abonnement, das Ihr Azure Storage-Konto enthält, einen neuen Log Analytics-Arbeitsbereich. Nachdem Sie die Protokollierung für Ihr Speicherkonto konfiguriert haben, sind die Protokolle im Log Analytics-Arbeitsbereich verfügbar. Weitere Informationen finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Klicken Sie im Abschnitt „Überwachung“ auf **Diagnoseeinstellungen (Vorschau)** .
1. Wählen Sie **Blob** aus, um Anforderungen für Blobspeicher zu protokollieren.
1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
1. Geben Sie einen Namen für die Diagnoseeinstellung an.
1. Wählen Sie unter **Kategoriedetails** im Abschnitt **Protokoll** aus, welche Arten von Anforderungen protokolliert werden sollen. Da es sich bei anonymen Anforderungen um Leseanforderungen handelt, wählen Sie **StorageRead** aus, um anonyme Anforderungen zu erfassen.
1. Wählen Sie unter **Zieldetails** die Option **An Log Analytics senden** aus. Wählen Sie Ihr Abonnement und den zuvor erstellten Log Analytics-Arbeitsbereich aus, wie in der folgenden Abbildung zu sehen:

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Screenshot: Erstellen einer Diagnoseeinstellung für die Protokollierung von Anforderungen":::

Nachdem Sie die Diagnoseeinstellung erstellt haben, werden Anforderungen für das Speicherkonto gemäß dieser Einstellung protokolliert. Weitere Informationen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../../azure-monitor/platform/diagnostic-settings.md).

Eine Referenz der Felder, die in Azure Storage-Protokollen in Azure Monitor verfügbar sind, finden Sie unter [Ressourcenprotokolle (Vorschau)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Abfragen anonymer Anforderungen in Protokollen

Azure Storage-Protokolle in Azure Monitor enthalten die Art der Autorisierung, die bei einer Anforderung für ein Speicherkonto verwendet wurde. Filtern Sie in Ihrer Protokollabfrage nach der Eigenschaft **AuthenticationType**, um anonyme Anforderungen anzuzeigen.

Wenn Sie Protokolle für anonyme Blobspeicheranforderungen der letzten sieben Tage abrufen möchten, öffnen Sie Ihren Log Analytics-Arbeitsbereich. Fügen Sie dann die folgende Abfrage in eine neue Protokollabfrage ein, und führen Sie sie aus:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Sie können auch eine auf dieser Abfrage basierende Warnungsregel konfigurieren, um über anonyme Anforderungen informiert zu werden. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Behandeln von anonymem öffentlichem Zugriff

Nachdem Sie anonyme Anforderungen für Container und Blobs in Ihrem Speicherkonto untersucht haben, können Sie Maßnahmen ergreifen, um den öffentlichen Zugriff einzuschränken oder zu verhindern. Müssen einige Container in Ihrem Speicherkonto für öffentliche Zugriffe verfügbar sein, können Sie die Einstellung für den öffentlichen Zugriff für jeden Container in Ihrem Speicherkonto konfigurieren. Dank dieser Option kann der öffentliche Zugriff äußerst präzise gesteuert werden. Weitere Informationen finden Sie unter [Festlegen der öffentlichen Zugriffsebene auf einen Container](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Zur Erhöhung der Sicherheit können Sie den öffentlichen Zugriff für das gesamte Speicherkonto verweigern. Durch die Einstellung für den öffentlichen Zugriff für ein Speicherkonto werden die individuellen Einstellungen für Container in diesem Konto überschrieben. Wenn Sie den öffentlichen Zugriff für ein Speicherkonto verweigern, kann auf Container, die für öffentlichen Zugriff konfiguriert wurden, nicht mehr anonym zugegriffen werden. Weitere Informationen finden Sie unter [Zulassen oder Verweigern des öffentlichen Lesezugriffs für ein Speicherkonto](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Falls in Ihrem Szenario bestimmte Container für den öffentlichen Zugriff verfügbar sein müssen, empfiehlt es sich gegebenenfalls, diese Container und die zugehörigen Blobs in Speicherkonten zu verschieben, die für öffentlichen Zugriff reserviert sind. Anschließend können Sie den öffentlichen Zugriff für alle anderen Speicherkonten verweigern.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Vergewissern, dass der öffentliche Zugriff auf ein Blob nicht gestattet ist

Wenn Sie sich vergewissern möchten, dass der öffentliche Zugriff auf ein bestimmtes Blob verweigert wird, können Sie versuchen, das Blob über seine URL herunterzuladen. Ist der Download erfolgreich, ist das Blob weiterhin öffentlich verfügbar. Wenn das Blob nicht öffentlich zugänglich ist, weil der öffentliche Zugriff für das Speicherkonto verweigert wurde, wird eine Fehlermeldung mit dem Hinweis angezeigt, dass öffentliche Zugriffe auf dieses Speicherkonto nicht zulässig sind.

Im folgenden Beispiel wird gezeigt, wie Sie mithilfe von PowerShell versuchen können, ein Blob über seine URL herunterzuladen. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Vergewissern, dass die Einstellung für den öffentlichen Zugriff des Containers nicht geändert werden kann

Wenn Sie sich vergewissern möchten, dass die Einstellung für den öffentlichen Zugriff eines Containers nicht geändert werden kann, nachdem der öffentliche Zugriff für das Speicherkonto verweigert wurde, können Sie versuchen, die Einstellung zu ändern. Wird der öffentliche Zugriff für das Speicherkonto verweigert, kann die Einstellung für den öffentlichen Zugriff des Containers nicht geändert werden.

Im folgenden Beispiel wird gezeigt, wie Sie mithilfe von PowerShell versuchen können, die Einstellung für den öffentlichen Zugriff eines Containers zu ändern. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Vergewissern, dass kein Container mit aktiviertem öffentlichem Zugriff erstellt werden kann

Wenn der öffentliche Zugriff für das Speicherkonto verweigert wird, können Sie keinen neuen Container mit aktiviertem öffentlichem Zugriff erstellen. Zur Überprüfung können Sie versuchen, einen Container mit aktiviertem öffentlichem Zugriff zu erstellen.

Im folgenden Beispiel wird gezeigt, wie Sie mithilfe von PowerShell versuchen können, einen Container mit aktiviertem öffentlichem Zugriff zu erstellen. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Überprüfen der Einstellung für den öffentlichen Zugriff für mehrere Konten

Wenn Sie die Einstellung für den öffentlichen Zugriff für eine Gruppe von Speicherkonten mit optimaler Leistung überprüfen möchten, können Sie den Azure Resource Graph-Explorer im Azure-Portal verwenden. Weitere Informationen zur Verwendung des Resource Graph-Explorers finden Sie unter [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md).

Die Eigenschaft **AllowBlobPublicAccess** wird nicht standardmäßig für ein Speicherkonto festgelegt und gibt erst dann einen Wert zurück, wenn Sie sie explizit festgelegt haben. Das Speicherkonto gestattet den öffentlichen Zugriff, wenn der Eigenschaftswert entweder **NULL** oder **true** ist.

Wenn Sie die folgende Abfrage im Resource Graph-Explorer ausführen, wird eine Liste der Speicherkonten zurückgegeben und für jedes Konto die Einstellung für den öffentlichen Zugriff angezeigt:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

Die folgende Abbildung zeigt die Ergebnisse einer abonnementweiten Abfrage. Beachten Sie, dass bei Speicherkonten, für die die Eigenschaft **AllowBlobPublicAccess** explizit festgelegt wurde, in den Ergebnissen **true** oder **false** angegeben wird. Wurde die Eigenschaft **AllowBlobPublicAccess** für ein Speicherkonto nicht festgelegt, wird in den Abfrageergebnissen ein Leer- oder Nullwert angegeben.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Screenshot: Abfrageergebnisse für die Einstellung für öffentlichen Zugriff für Speicherkonten":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Verwenden von Azure Policy zur Überwachung der Konformität

Wenn Sie über eine große Anzahl von Speicherkonten verfügen, möchten Sie vielleicht eine Überwachung durchführen, um sicherzustellen, dass diese Konten so konfiguriert sind, dass der öffentliche Zugriff verhindert wird. Verwenden Sie Azure Policy, um eine Reihe von Speicherkonten auf ihre Konformität zu überwachen. Azure Policy ist ein Dienst, mit dem Sie Richtlinien zum Anwenden von Regeln auf Azure-Ressourcen erstellen, zuweisen und verwalten können. Azure Policy hilft Ihnen, die Konformität dieser Ressourcen mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel sicherzustellen. Weitere Informationen finden Sie in der [Übersicht über Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Erstellen einer Richtlinie mit der Auswirkung „Audit“

Azure Policy unterstützt Auswirkungen, die bestimmen, was passiert, wenn eine Richtlinie anhand einer Ressource ausgewertet wird. Die Auswirkung „Audit“ erzeugt eine Warnung, wenn eine Ressource nicht konform ist, beendet aber die Anforderung nicht. Weitere Informationen zu Auswirkungen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md).

Führen Sie die folgenden Schritte aus, um eine Richtlinie mit der Auswirkung „Audit“ für die Einstellung für den öffentlichen Zugriff für ein Speicherkonto mit dem Azure-Portal zu erstellen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie unter dem Abschnitt **Autorisierung** die Option **Definitionen** aus.
1. Wählen Sie **Richtliniendefinition hinzufügen** aus, um eine neue Richtliniendefinition zu erstellen.
1. Wählen Sie für das Feld **Definitionsspeicherort** die Schaltfläche **Mehr** aus, um anzugeben, wo sich die Ressource für die Überwachungsrichtlinie befindet.
1. Geben Sie einen Namen für die Richtlinie an. Sie können optional eine Beschreibung und eine Kategorie angeben.
1. Fügen Sie unter **Richtlinienregel** die folgende Richtliniendefinition zum Abschnitt **policyRule** hinzu.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Speichern Sie die Richtlinie.

### <a name="assign-the-policy"></a>Zuweisen der Richtlinie

Anschließend weisen Sie die Richtlinie einer Ressource zu. Der Umfang der Richtlinie entspricht der Ressource und den darunter liegenden Ressourcen. Weitere Informationen zur Zuweisung von Richtlinien finden Sie unter [Azure Policy-Zuweisungsstruktur](../../governance/policy/concepts/assignment-structure.md).

Führen Sie die folgenden Schritte aus, um die Richtlinie dem Azure-Portal zuzuweisen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie unter dem Abschnitt **Autorisierung** die Option **Zuweisungen** aus.
1. Wählen Sie **Richtlinie zuweisen** aus, um eine neue Richtlinienzuweisung zu erstellen.
1. Wählen Sie für das Feld **Umfang** den Umfang der Richtlinienzuweisung aus.
1. Wählen Sie für das Feld **Richtliniendefinition** die Schaltfläche **Mehr** und dann die im vorherigen Abschnitt definierte Richtlinie aus der Liste aus.
1. Geben Sie einen Namen für die Richtlinienzuweisung an. Die Angabe einer Beschreibung ist optional.
1. Behalten Sie für **Richtlinienerzwingung** die Einstellung *Aktiviert* bei. Diese Einstellung hat keine Auswirkung auf die Überwachungsrichtlinie.
1. Klicken Sie zum Erstellen der Zuweisung auf **Überprüfen + erstellen**.

### <a name="view-compliance-report"></a>Anzeigen des Konformitätsberichts

Nachdem Sie die Richtlinie zugewiesen haben, können Sie den Konformitätsbericht anzeigen. Der Konformitätsbericht für eine Überwachungsrichtlinie gibt Auskunft darüber, welche Speicherkonten nicht mit der Richtlinie konform sind. Weitere Informationen finden Sie unter [Abrufen von Daten zur Richtlinienkonformität](../../governance/policy/how-to/get-compliance-data.md).

Es kann einige Minuten dauern, bis der Konformitätsbericht nach Erstellung der Richtlinienzuweisung verfügbar ist.

Führen Sie die folgenden Schritte aus, um den Konformitätsbericht im Azure-Portal anzuzeigen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie **Compliance** aus.
1. Filtern Sie die Ergebnisse nach dem Namen der Richtlinienzuweisung, die Sie im vorherigen Schritt erstellt haben. Der Bericht zeigt, wie viele Ressourcen nicht mit der Richtlinie konform sind.
1. Sie können einen Drilldown in den Bericht ausführen, um weitere Details anzuzeigen, einschließlich einer Liste von Speicherkonten, die nicht konform sind.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Screenshot mit Konformitätsbericht für Überwachungsrichtlinien für den öffentlichen Blobzugriff":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Erzwingen des autorisierten Zugriffs mit Azure Policy

Azure Policy unterstützt die Cloudgovernance, indem es sicherstellt, dass Azure-Ressourcen den Anforderungen und Standards entsprechen. Um sicherzustellen, dass Speicherkonten in Ihrer Organisation nur autorisierte Anforderungen zulassen, können Sie eine Richtlinie erstellen, die die Erstellung eines neuen Speicherkontos mit einer Einstellung für den öffentlichen Zugriff verhindert, die anonyme Anforderungen zulässt. Diese Richtlinie verhindert auch alle Konfigurationsänderungen an einem bestehenden Konto, wenn die Einstellung für den öffentlichen Zugriff für dieses Konto nicht mit der Richtlinie übereinstimmt.

Die Erzwingungsrichtlinie nutzt die Auswirkung „Deny“, um eine Anforderung zu verhindern, die ein Speicherkonto erstellen oder ändern würde, um den öffentlichen Zugriff zu ermöglichen. Weitere Informationen zu Auswirkungen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md).

Um eine Richtlinie mit der Auswirkung „Deny“ für eine Einstellung für den öffentlichen Zugriff zu erstellen, die anonyme Anforderungen zulässt, befolgen Sie dieselben Schritte, die unter [Verwenden von Azure Policy zur Überwachung der Konformität](#use-azure-policy-to-audit-for-compliance) beschrieben sind, aber stellen Sie den folgenden JSON-Code im Abschnitt **policyRule** der Richtliniendefinition zur Verfügung:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Nachdem Sie die Richtlinie mit der Auswirkung „Deny“ erstellt und einem Bereich zugewiesen haben, kann ein Benutzer kein Speicherkonto erstellen, das den öffentlichen Zugriff gestattet. Außerdem kann ein Benutzer keine Konfigurationsänderungen an einem vorhandenen Speicherkonto vornehmen, das zurzeit den öffentlichen Zugriff zulässt. Ein entsprechender Versuch führt zu einem Fehler. Die Einstellung für den öffentlichen Zugriff für das Speicherkonto muss auf **false** (falsch) festgelegt werden, um mit der Kontoerstellung oder -konfiguration fortzufahren.

Die folgende Abbildung zeigt den Fehler, der beim Erstellen eines Speicherkontos auftritt, das den öffentlichen Zugriff erlaubt (die Standardeinstellung für ein neues Konto), wenn eine Richtlinie mit der Auswirkung „Deny“ erfordert, dass der öffentliche Zugriff nicht gestattet wird.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Screenshot mit dem Fehler, der beim Erstellen eines Speicherkontos bei einem Verstoß gegen die Richtlinie auftritt":::

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des anonymen, öffentlichen Lesezugriffs auf Container und Blobs](anonymous-read-access-configure.md)
- [Anonymer Zugriff auf öffentliche Container und Blobs mit .NET](anonymous-read-access-client.md)