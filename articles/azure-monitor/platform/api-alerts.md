---
title: Verwenden der REST-API für Log Analytics-Warnungen
description: Mit der REST-API für Log Analytics-Warnungen können Sie Warnungen in Log Analytics, einer Komponente von Log Analytics, erstellen und verwalten.  Dieser Artikel enthält die Details der API und mehrere Beispiele für verschiedene Vorgänge.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: dce340db90c1528c46c1be0bc172751a04feaf31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006403"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Erstellen und Verwalten von Warnungsregeln in Log Analytics mithilfe der REST-API 

> [!IMPORTANT]
> Wie [angekündigt](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) werden bei Log Analytics-Arbeitsbereichen, die nach dem *1. Juni 2019* erstellt wurden, Warnungsregeln mithilfe der aktuellen [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules/) verwaltet. Kunden wird empfohlen, bei älteren Arbeitsbereichen [zur aktuellen API zu wechseln](./alerts-log-api-switch.md), um die [Vorteile](./alerts-log-api-switch.md#benefits) von scheduledQueryRules in Azure Monitor zu nutzen. In diesem Artikel wird die Verwaltung von Warnungsregeln mithilfe der Legacy-API beschrieben.

Mit der REST-API für Log Analytics-Warnungen können Sie Warnungen in Log Analytics erstellen und verwalten.  Dieser Artikel enthält die Details der API und mehrere Beispiele für verschiedene Vorgänge.

Die REST-API für die Log Analytics-Suche ist RESTful. Der Zugriff darauf erfolgt über die Azure Resource Manager-REST-API. In diesem Dokument finden Sie Beispiele, in denen über eine PowerShell-Befehlszeile per [ARMClient](https://github.com/projectkudu/ARMClient) auf die API zugegriffen wird. Dies ist ein Open-Source-Befehlszeilentool, mit dem das Aufrufen der Azure Resource Manager-API vereinfacht wird. Die Verwendung von ARMClient und PowerShell ist eine von vielen Möglichkeiten, auf die Protokollsuch-API von Log Analytics zuzugreifen. Mit diesen Tools können Sie die RESTful-API von Azure Resource Manager für Aufrufe an Log Analytics-Arbeitsbereiche nutzen und darin Suchbefehle ausführen. Die API wird Ihnen Suchergebnisse im JSON-Format ausgeben, und Sie können die Suchergebnisse programmgesteuert auf viele verschiedene Arten verwenden.

## <a name="prerequisites"></a>Voraussetzungen
Derzeit können Warnungen nur mit einer gespeicherten Suche in Log Analytics erstellt werden.  Weitere Informationen finden Sie unter [REST-API für die Log Analytics-Suche](../log-query/log-query-overview.md) .

## <a name="schedules"></a>Zeitpläne
Eine gespeicherte Suche kann über einen oder mehrere Zeitpläne verfügen. Der Zeitplan definiert, wie oft die Suche durchgeführt wird und welches Zeitintervall für die Identifizierung der Kriterien verwendet wird.
Die Zeitplaneigenschaften sind in der folgenden Tabelle aufgeführt.

| Eigenschaft | Beschreibung |
|:--- |:--- |
| Intervall |Wie oft die Suche durchgeführt wird. Dieser Wert wird in Minuten gemessen. |
| QueryTimeSpan |Das Zeitintervall, in dem die Kriterien ausgewertet werden. Der Wert muss größer oder gleich dem Intervall sein. Dieser Wert wird in Minuten gemessen. |
| Version |Die verwendete API-Version.  Dieser Wert sollte derzeit immer 1 lauten. |

Stellen Sie sich beispielsweise eine Ereignisabfrage mit einem Intervall von 15 Minuten und einer Zeitspanne von 30 Minuten vor. In diesem Fall würde die Abfrage alle 15 Minuten ausgeführt werden, und es würde eine Warnung ausgelöst werden, wenn sich für die Kriterien weiterhin 30 Minuten lang die Auflösung „true“ ergibt.

### <a name="retrieving-schedules"></a>Abrufen von Zeitplänen
Verwenden Sie die Get-Methode, um alle Zeitpläne für eine gespeicherte Suche abzurufen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Verwenden Sie die Get-Methode mit einer Zeitplan-ID, um einen bestimmten Zeitplan für eine gespeicherte Suche abzurufen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

Im Folgenden finden Sie eine Beispielantwort für einen Zeitplan.

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>Erstellen eines Zeitplans
Verwenden Sie die Put-Methode mit der eindeutigen Zeitplan-ID, um einen neuen Zeitplan zu erstellen.  Zwei Zeitpläne können nicht dieselbe ID haben, auch wenn sie unterschiedlichen gespeicherten Suchvorgängen zugeordnet sind.  Wenn Sie einen Zeitplan in der Log Analytics-Konsole erstellen, wird für die Zeitplan-ID eine GUID erstellt.

> [!NOTE]
> Die Namen aller gespeicherten Suchvorgänge, Zeitpläne und Aktionen, die mit der Log Analytics-API erstellt werden, müssen in Kleinbuchstaben geschrieben werden.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Bearbeiten eines Zeitplans
Verwenden Sie die Put-Methode mit einer vorhandenen Zeitplan-ID für die gleiche gespeicherte Suche, um diesen Zeitplan zu bearbeiten. Im Beispiel unten ist der Zeitplan deaktiviert. Der Hauptteil der Anforderung muss das *etag* des Zeitplans enthalten.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Löschen von Zeitplänen
Verwenden Sie die Delete-Methode mit einer Zeitplan-ID, um einen Zeitplan zu löschen.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Aktionen
Ein Zeitplan kann mehrere Aktionen umfassen. Mit einer Aktion können ein oder mehrere durchzuführende Prozesse definiert werden, z.B. das Senden einer E-Mail oder das Starten eines Runbooks. Es kann auch ein Schwellenwert definiert werden, der bestimmt, wann die Ergebnisse einer Suche eine Übereinstimmung mit Kriterien ergeben.  Mit einigen Aktionen wird beides definiert, sodass die Prozesse durchgeführt werden, wenn der Schwellenwert erreicht ist.

Die Eigenschaften aller Aktionen sind in der folgenden Tabelle aufgeführt.  Unterschiedliche Arten von Warnungen verfügen über unterschiedliche Eigenschaften, die weiter unten beschrieben sind.

| Eigenschaft | Beschreibung |
|:--- |:--- |
| `Type` |Der Typ der Aktion.  Derzeit sind die möglichen Werte „Warnung“ und „Webhook“. |
| `Name` |Der Anzeigename für die Warnung. |
| `Version` |Die verwendete API-Version.  Dieser Wert sollte derzeit immer 1 lauten. |

### <a name="retrieving-actions"></a>Abrufen von Aktionen

Verwenden Sie die Get-Methode, um alle Aktionen für einen Zeitplan abzurufen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Verwenden Sie die Get-Methode mit der Aktions-ID, um eine bestimmte Aktion für einen Zeitplan abzurufen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Erstellen oder Bearbeiten von Aktionen
Verwenden Sie die Put-Methode mit einer Aktions-ID, die für den Zeitplan eindeutig ist, um eine neue Aktion zu erstellen.  Wenn Sie eine Aktivität in der Log Analytics-Konsole erstellen, wird eine GUID für die Aktions-ID erstellt.

> [!NOTE]
> Die Namen aller gespeicherten Suchvorgänge, Zeitpläne und Aktionen, die mit der Log Analytics-API erstellt werden, müssen in Kleinbuchstaben geschrieben werden.

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID für die gleiche gespeicherte Suche, um diesen Zeitplan zu bearbeiten.  Der Hauptteil der Anforderung muss das ETag des Zeitplans enthalten.

Das Anforderungsformat zum Erstellen einer neuen Aktion variiert je nach Aktivitätstyp. Beispiele hierzu finden Sie in den Abschnitten unten.

### <a name="deleting-actions"></a>Löschen von Aktionen

Verwenden Sie die Delete-Methode mit der Aktions-ID, um eine Aktion zu löschen.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Warnungsaktionen
Ein Zeitplan sollte nur über genau eine Warnungsaktion verfügen.  Warnungsaktionen weisen einen oder mehrere Abschnitte auf, die in der folgenden Tabelle aufgeführt sind.  Jeder Abschnitt wird unten ausführlicher beschrieben.

| `Section` | BESCHREIBUNG | Verwendung |
|:--- |:--- |:--- |
| Schwellenwert |Kriterien für den Zeitpunkt der Ausführung einer Aktion.| Für jede Warnung vor oder nach der Erweiterung auf Azure erforderlich. |
| Schweregrad |Bezeichnung zum Klassifizieren einer Warnung bei Auslösung.| Für jede Warnung vor oder nach der Erweiterung auf Azure erforderlich. |
| Suppress |Option zum Beenden von Benachrichtigungen bei Warnungen. | Für jede Warnung optional, vor oder nach der Ausweitung auf Azure. |
| Aktionsgruppen |IDs von Azure-Aktionsgruppen, in denen erforderliche Aktionen angegeben sind, z.B. E-Mails, SMS, Sprachanrufe, Webhooks, Automation-Runbooks, ITSM-Connectors usw.| Erforderlich, nachdem Warnungen auf Azure erweitert wurden|
| Anpassen von Aktionen|Ändern der Standardausgabe für ausgewählte Aktionen der Aktionsgruppe| Optional für jede Warnung, kann verwendet werden, nachdem Warnungen auf Azure erweitert wurden. |

### <a name="thresholds"></a>Schwellenwerte
Eine Warnungsaktion sollte nur über genau einen Schwellenwert verfügen.  Wenn die Ergebnisse einer gespeicherten Suche mit dem Schwellenwert in einer Aktion übereinstimmen, die der Suche zugeordnet ist, werden alle anderen Prozesse in dieser Aktion ausgeführt.  Eine Aktion kann einen Schwellenwert auch nur zu dem Zweck enthalten, dass sie mit Aktionen eines anderen Typs verwendet werden kann, die keine Schwellenwerte aufweisen.

Die Eigenschaften von Schwellenwerten sind in der folgenden Tabelle aufgeführt.

| Eigenschaft | Beschreibung |
|:--- |:--- |
| `Operator` |Operator für den Schwellenwertvergleich. <br> gt = Greater Than (Größer als) <br> lt = Less Than (Kleiner als) |
| `Value` |Der Wert für den Schwellenwert. |

Stellen Sie sich beispielsweise eine Ereignisabfrage mit einem Intervall von 15 Minuten, einer Zeitspanne von 30 Minuten und einem Schwellenwert größer als 10 vor. In diesem Fall wird die Abfrage alle 15 Minuten ausgeführt. Eine Warnung wird ausgelöst, wenn 10 Ereignisse zurückgegeben werden, die über einen Zeitraum von 30 Minuten erstellt wurden.

Unten ist eine Beispielantwort für eine Aktion angegeben, die nur über einen Schwellenwert verfügt.  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Schwellenwertaktion für einen Zeitplan zu erstellen.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Schwellenwertaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Schweregrad
Mit Log Analytics können Sie Ihre Warnungen zur einfacheren Verwaltung und Selektierung in Kategorien klassifizieren. Der definierte Schweregrad der Warnung lautet: „Information“, „Warnung“ oder „Kritisch“. Diese sind der normalisierten Schweregradskala von Azure-Warnungen folgendermaßen zugeordnet:

|Log Analytics-Schweregrad  |Azure-Warnungsschweregrad  |
|---------|---------|
|`critical` |Schweregrad 0|
|`warning` |Schweregrad 1|
|`informational` | Schweregrad 2|

Unten ist eine Beispielantwort für eine Aktion angegeben, die nur einen Schwellenwert und einen Schweregrad aufweist. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Aktion für einen Zeitplan mit Schweregrad zu erstellen.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Schweregradaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Suppress
Log Analytics-basierte Abfragewarnungen werden bei jedem Erreichen oder Überschreiten des Schwellenwerts ausgelöst. Aufbauend auf der in der Abfrage implizierten Logik, kann dies zur Auslösung einer Warnung für eine Abfolge von Intervallen führen und entsprechend auch zum fortlaufenden Senden von Benachrichtigungen. Um dieses Szenario zu verhindern, kann ein Benutzer die Option „Suppress“ festlegen und Log Analytics anweisen, eine festgelegte Zeitspanne abzuwarten, ehe für die Warnungsregel zum zweiten Mal eine Benachrichtigung ausgelöst wird. Wenn also Suppress auf 30 Minuten festgelegt ist, wird die Warnung beim ersten Mal ausgelöst und führt zum Senden der konfigurierten Benachrichtigungen. Anschließend wird jedoch 30 Minuten gewartet, bevor erneut eine Benachrichtigung für die Warnungsregel verwendet wird. In der Zwischenzeit wird die Warnungsregel weiterhin ausgeführt – lediglich die Benachrichtigung wird von Log Analytics für den angegebenen Zeitraum unterdrückt, unabhängig von der Anzahl der Auslösungen der Warnungsregel in diesem Zeitraum.

Die Eigenschaft „Suppress“ der Log Analytics-Warnungsregel wird mithilfe des Werts für *Throttling*, der Unterdrückungszeitraum mithilfe des Werts für *DurationInMinutes* festgelegt.

Unten sehen eine Beispielantwort für eine Aktion, die nur einen Schwellenwert, einen Schweregrad und eine Suppress-Eigenschaft aufweist.

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Aktion für einen Zeitplan mit Schweregrad zu erstellen.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Schweregradaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Aktionsgruppen
Alle Warnungen in Azure verwenden Aktionsgruppen als Standardmechanismus für die Behandlung von Aktionen. Mit Aktionsgruppen können Sie Ihre Aktionen einmal angeben und die Aktionsgruppe dann mehreren Warnungen zuordnen. Dies gilt für sämtliche Bereiche in Azure, sodass Sie dieselbe Aktion nicht mehrmals deklarieren müssen. Aktionsgruppen unterstützen mehrere Aktionen – einschließlich E-Mails, SMS, Sprachanrufen, ITSM-Verbindungen, Automation-Runbooks, Webhook-URIs und anderen. 

Für Benutzer, die ihre Warnungen auf Azure erweitert haben, sollten bei Zeitplänen jetzt zusammen mit dem Schwellenwert auch Aktionsgruppendetails übergeben werden, um eine Warnung erstellen zu können. E-Mail-Details, Webhook-URLs, Runbook-Automatisierungsdetails und weitere Aktionen müssen zuerst in einer Aktivitätsgruppe definiert werden, bevor eine Warnung erstellt werden kann. Es ist möglich, im Portal [Aktionsgruppen aus Azure Monitor zu erstellen](./action-groups.md) oder die [Aktionsgruppen-API](/rest/api/monitor/actiongroups) zu verwenden.

Um eine Zuordnung einer Aktionsgruppe zu einer Warnung hinzuzufügen, geben Sie die eindeutige Azure Resource Manager-ID der Aktionsgruppe in der Warnungsdefinition an. Im Folgenden finden Sie eine Beispieldarstellung:

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine bereits vorhandene Aktionsgruppe für einen Zeitplan zuzuordnen.  Im Folgenden finden eine Beispieldarstellung der Nutzung.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine zugeordnete Aktionsgruppe für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Anpassen von Aktionen
Standardmäßig gilt für Aktionen eine Standardvorlage und ein Standardformat für Benachrichtigungen. Benutzer können jedoch einige Aktionen anpassen, auch wenn sie durch Aktionsgruppen gesteuert werden. Derzeit ist die Anpassung für E-Mail-Betreff und Webhook-Nutzlast möglich.

##### <a name="customize-e-mail-subject-for-action-group"></a>Anpassen des E-Mail-Betreffs für Aktionsgruppen
Der Standard-E-Mail-Betreff für Warnungen lautet: „Alert Notification `<AlertName>` for `<WorkspaceName>`“. Dies kann jedoch angepasst werden, sodass Sie bestimmte Wörter oder Tags angeben können, mit denen Sie ohne Aufwand Filterregeln in Ihrem Posteingang einsetzen können. Die Details des angepassten E-Mail-Headers müssen mit den Details zur Aktionsgruppe gesendet werden, wie im Beispiel unten dargestellt.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine bereits vorhandene angepasste Aktionsgruppe für einen Zeitplan zuzuordnen.  Im Folgenden finden eine Beispieldarstellung der Nutzung.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine zugeordnete Aktionsgruppe für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Anpassen einer Webhook-Nutzlast für eine Aktionsgruppe
Der über Aktionsgruppen gesendete Webhook für Log Analytics weist standardmäßig eine feste Struktur auf. Die JSON-Nutzlast kann jedoch durch Verwendung bestimmter unterstützter Variablen angepasst werden, um die Anforderungen des Webhook-Endpunkts zu erfüllen. Weitere Informationen finden Sie unter [Webhookaktionen für Protokollwarnungsregeln](./alerts-log-webhook.md). 

Die Details des angepassten Webhooks müssen mit den Details zur Aktionsgruppe gesendet werden. Sie werden auf alle in der Aktionsgruppe angegebenen Webhook-URIs angewendet, wie im Beispiel unten dargestellt.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine bereits vorhandene angepasste Aktionsgruppe für einen Zeitplan zuzuordnen.  Im Folgenden finden eine Beispieldarstellung der Nutzung.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine zugeordnete Aktionsgruppe für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie die [REST-API zum Durchführen von Protokollsuchen](../log-query/log-query-overview.md) in Log Analytics.
* Erfahren Sie mehr über [Protokollwarnungen in Azure Monitor](./alerts-unified-log.md).
* [Erstellen, Bearbeiten und Verwalten von Protokollwarnungsregeln in Azure Monitor](./alerts-log.md)

