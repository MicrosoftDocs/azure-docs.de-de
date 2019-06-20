---
title: Verwenden der REST-API für Log Analytics-Warnungen
description: Mit der REST-API für Log Analytics-Warnungen können Sie Warnungen in Log Analytics, einer Komponente von Log Analytics, erstellen und verwalten.  Dieser Artikel enthält die Details der API und mehrere Beispiele für verschiedene Vorgänge.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: bee64909c7f3b295691ef1cb1840424aa7e3fe49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345905"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Erstellen und Verwalten von Warnungsregeln in Log Analytics mithilfe der REST-API
Mit der REST-API für Log Analytics-Warnungen können Sie Warnungen in Log Analytics erstellen und verwalten.  Dieser Artikel enthält die Details der API und mehrere Beispiele für verschiedene Vorgänge.

Die REST-API für die Log Analytics-Suche ist RESTful. Der Zugriff darauf erfolgt über die Azure Resource Manager-REST-API. In diesem Dokument finden Sie Beispiele, in denen über eine PowerShell-Befehlszeile per [ARMClient](https://github.com/projectkudu/ARMClient) auf die API zugegriffen wird. Dies ist ein Open-Source-Befehlszeilentool, mit dem das Aufrufen der Azure Resource Manager-API vereinfacht wird. Die Verwendung von ARMClient und PowerShell ist eine von vielen Möglichkeiten, auf die Protokollsuch-API von Log Analytics zuzugreifen. Mit diesen Tools können Sie die RESTful-API von Azure Resource Manager für Aufrufe an Log Analytics-Arbeitsbereiche nutzen und darin Suchbefehle ausführen. Die API wird Ihnen Suchergebnisse im JSON-Format ausgeben, und Sie können die Suchergebnisse programmgesteuert auf viele verschiedene Arten verwenden.

## <a name="prerequisites"></a>Voraussetzungen
Derzeit können Warnungen nur mit einer gespeicherten Suche in Log Analytics erstellt werden.  Weitere Informationen finden Sie unter [REST-API für die Log Analytics-Suche](../../azure-monitor/log-query/log-query-overview.md) .

## <a name="schedules"></a>Zeitpläne
Eine gespeicherte Suche kann über einen oder mehrere Zeitpläne verfügen. Der Zeitplan definiert, wie oft die Suche durchgeführt wird und welches Zeitintervall für die Identifizierung der Kriterien verwendet wird.
Die Zeitplaneigenschaften sind in der folgenden Tabelle aufgeführt.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Intervall |Wie oft die Suche durchgeführt wird. Dieser Wert wird in Minuten gemessen. |
| QueryTimeSpan |Das Zeitintervall, in dem die Kriterien ausgewertet werden. Der Wert muss größer oder gleich dem Intervall sein. Dieser Wert wird in Minuten gemessen. |
| Version |Die verwendete API-Version.  Dieser Wert sollte derzeit immer 1 lauten. |

Stellen Sie sich beispielsweise eine Ereignisabfrage mit einem Intervall von 15 Minuten und einer Zeitspanne von 30 Minuten vor. In diesem Fall würde die Abfrage alle 15 Minuten ausgeführt werden, und es würde eine Warnung ausgelöst werden, wenn sich für die Kriterien weiterhin 30 Minuten lang die Auflösung „true“ ergibt.

### <a name="retrieving-schedules"></a>Abrufen von Zeitplänen
Verwenden Sie die Get-Methode, um alle Zeitpläne für eine gespeicherte Suche abzurufen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Verwenden Sie die Get-Methode mit einer Zeitplan-ID, um einen bestimmten Zeitplan für eine gespeicherte Suche abzurufen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

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
Verwenden Sie die Put-Methode mit der eindeutigen Zeitplan-ID, um einen neuen Zeitplan zu erstellen.  Beachten Sie, dass zwei Zeitpläne dieselbe ID haben können, auch wenn sie unterschiedlichen gespeicherten Suchvorgängen zugeordnet sind.  Wenn Sie einen Zeitplan in der Log Analytics-Konsole erstellen, wird für die Zeitplan-ID eine GUID erstellt.

> [!NOTE]
> Die Namen aller gespeicherten Suchvorgänge, Zeitpläne und Aktionen, die mit der Log Analytics-API erstellt werden, müssen in Kleinbuchstaben geschrieben werden.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Bearbeiten eines Zeitplans
Verwenden Sie die Put-Methode mit einer vorhandenen Zeitplan-ID für die gleiche gespeicherte Suche, um diesen Zeitplan zu bearbeiten. Im Beispiel unten ist der Zeitplan deaktiviert. Der Hauptteil der Anforderung muss das *etag* des Zeitplans enthalten.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Löschen von Zeitplänen
Verwenden Sie die Delete-Methode mit einer Zeitplan-ID, um einen Zeitplan zu löschen.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Aktionen
Ein Zeitplan kann mehrere Aktionen umfassen. Mit einer Aktion können ein oder mehrere durchzuführende Prozesse definiert werden, z.B. das Senden einer E-Mail oder das Starten eines Runbooks. Es kann auch ein Schwellenwert definiert werden, der bestimmt, wann die Ergebnisse einer Suche eine Übereinstimmung mit Kriterien ergeben.  Mit einigen Aktionen wird beides definiert, sodass die Prozesse durchgeführt werden, wenn der Schwellenwert erreicht ist.

Die Eigenschaften aller Aktionen sind in der folgenden Tabelle aufgeführt.  Unterschiedliche Arten von Warnungen verfügen über unterschiedliche Eigenschaften, die weiter unten beschrieben sind.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| `Type` |Der Typ der Aktion.  Derzeit sind die möglichen Werte „Warnung“ und „Webhook“. |
| `Name` |Der Anzeigename für die Warnung. |
| `Version` |Die verwendete API-Version.  Dieser Wert sollte derzeit immer 1 lauten. |

### <a name="retrieving-actions"></a>Abrufen von Aktionen

> [!NOTE]
> Ab 14. Mai 2018 werden alle Warnungen in einer öffentlichen Instanz der Azure Cloud in einem Log Analytics-Arbeitsbereich automatisch auf Azure ausgedehnt. Ein Benutzer kann die Ausdehnung von Warnungen auf Azure bereits vor dem 14. Mai 2018 freiwillig initiieren. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure aus Log Analytics](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure erweitert werden, können Sie über die [Aktionsgruppen-API](https://docs.microsoft.com/rest/api/monitor/actiongroups) Aktionen abrufen oder hinzufügen.

Verwenden Sie die Get-Methode, um alle Aktionen für einen Zeitplan abzurufen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Verwenden Sie die Get-Methode mit der Aktions-ID, um eine bestimmte Aktion für einen Zeitplan abzurufen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Erstellen oder Bearbeiten von Aktionen
Verwenden Sie die Put-Methode mit einer Aktions-ID, die für den Zeitplan eindeutig ist, um eine neue Aktion zu erstellen.  Wenn Sie eine Aktivität in der Log Analytics-Konsole erstellen, wird eine GUID für die Aktions-ID erstellt.

> [!NOTE]
> Die Namen aller gespeicherten Suchvorgänge, Zeitpläne und Aktionen, die mit der Log Analytics-API erstellt werden, müssen in Kleinbuchstaben geschrieben werden.

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID für die gleiche gespeicherte Suche, um diesen Zeitplan zu bearbeiten.  Der Hauptteil der Anforderung muss das ETag des Zeitplans enthalten.

Das Anforderungsformat zum Erstellen einer neuen Aktion variiert je nach Aktivitätstyp. Beispiele hierzu finden Sie in den Abschnitten unten.

### <a name="deleting-actions"></a>Löschen von Aktionen

> [!NOTE]
> Ab 14. Mai 2018 werden alle Warnungen in einer öffentlichen Instanz der Azure Cloud in einem Log Analytics-Arbeitsbereich automatisch auf Azure ausgedehnt. Ein Benutzer kann die Ausdehnung von Warnungen auf Azure bereits vor dem 14. Mai 2018 freiwillig initiieren. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure aus Log Analytics](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure erweitert werden, können Sie über die [Aktionsgruppen-API](https://docs.microsoft.com/rest/api/monitor/actiongroups) Aktionen abrufen oder hinzufügen.

Verwenden Sie die Delete-Methode mit der Aktions-ID, um eine Aktion zu löschen.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Warnungsaktionen
Ein Zeitplan sollte nur über genau eine Warnungsaktion verfügen.  Warnungsaktionen weisen einen oder mehrere Abschnitte auf, die in der folgenden Tabelle aufgeführt sind.  Jeder Abschnitt wird unten ausführlicher beschrieben.

| Abschnitt | BESCHREIBUNG | Verwendung |
|:--- |:--- |:--- |
| Schwellenwert |Kriterien für den Zeitpunkt der Ausführung einer Aktion.| Für jede Warnung vor oder nach der Erweiterung auf Azure erforderlich. |
| Severity |Bezeichnung zum Klassifizieren einer Warnung bei Auslösung.| Für jede Warnung vor oder nach der Erweiterung auf Azure erforderlich. |
| Suppress |Option zum Beenden von Benachrichtigungen bei Warnungen. | Für jede Warnung optional, vor oder nach der Ausweitung auf Azure. |
| Aktionsgruppen |IDs von Azure-Aktionsgruppen, in denen erforderliche Aktionen angegeben sind, z.B. E-Mails, SMS, Sprachanrufe, Webhooks, Automation-Runbooks, ITSM-Connectors usw.| Erforderlich, nachdem Warnungen auf Azure erweitert wurden|
| Anpassen von Aktionen|Ändern der Standardausgabe für ausgewählte Aktionen der Aktionsgruppe| Optional für jede Warnung, kann verwendet werden, nachdem Warnungen auf Azure erweitert wurden. |
| EmailNotification |Senden von E-Mails an mehrere Empfänger. | Nicht erforderlich, wenn Warnungen auf Azure erweitert werden|
| Wiederherstellung |Starten Sie ein Runbook in Azure Automation, um zu versuchen, das identifizierte Problem zu beheben. |Nicht erforderlich, wenn Warnungen auf Azure erweitert werden|
| Webhookaktionen | Pushen von Daten von Warnungen zum gewünschten Dienst als JSON |Nicht erforderlich, wenn Warnungen auf Azure erweitert werden|

> [!NOTE]
> Ab 14. Mai 2018 werden alle Warnungen in einer öffentlichen Instanz der Azure Cloud in einem Log Analytics-Arbeitsbereich automatisch auf Azure ausgedehnt. Ein Benutzer kann die Ausdehnung von Warnungen auf Azure bereits vor dem 14. Mai 2018 freiwillig initiieren. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure aus Log Analytics](../../azure-monitor/platform/alerts-extend.md).

#### <a name="thresholds"></a>Schwellenwerte
Eine Warnungsaktion sollte nur über genau einen Schwellenwert verfügen.  Wenn die Ergebnisse einer gespeicherten Suche mit dem Schwellenwert in einer Aktion übereinstimmen, die der Suche zugeordnet ist, werden alle anderen Prozesse in dieser Aktion ausgeführt.  Eine Aktion kann einen Schwellenwert auch nur zu dem Zweck enthalten, dass sie mit Aktionen eines anderen Typs verwendet werden kann, die keine Schwellenwerte aufweisen.

Die Eigenschaften von Schwellenwerten sind in der folgenden Tabelle aufgeführt.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| `Operator` |Operator für den Schwellenwertvergleich. <br> gt = Greater Than (Größer als) <br> lt = Less Than (Kleiner als) |
| `Value` |Der Wert für den Schwellenwert. |

Stellen Sie sich beispielsweise eine Ereignisabfrage mit einem Intervall von 15 Minuten, einer Zeitspanne von 30 Minuten und einem Schwellenwert größer als 10 vor. In diesem Fall wird die Abfrage alle 15 Minuten ausgeführt. Eine Warnung wird ausgelöst, wenn 10 Ereignisse zurückgegeben werden, die über einen Zeitraum von 30 Minuten erstellt wurden.

Unten ist eine Beispielantwort für eine Aktion angegeben, die nur über einen Schwellenwert verfügt.  

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

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Schwellenwertaktion für einen Zeitplan zu erstellen.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Schwellenwertaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Mit Log Analytics können Sie Ihre Warnungen zur einfacheren Verwaltung und Selektierung in Kategorien klassifizieren. Der definierte Schweregrad der Warnung lautet: „Information“, „Warnung“ oder „Kritisch“. Diese sind der normalisierten Schweregradskala von Azure-Warnungen folgendermaßen zugeordnet:

|Log Analytics-Schweregrad  |Azure-Warnungsschweregrad  |
|---------|---------|
|`critical` |Schweregrad 0|
|`warning` |Schweregrad 1|
|`informational` | Schweregrad 2|

Unten ist eine Beispielantwort für eine Aktion angegeben, die nur einen Schwellenwert und einen Schweregrad aufweist. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Aktion für einen Zeitplan mit Schweregrad zu erstellen.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Schweregradaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Suppress
Log Analytics-basierte Abfragewarnungen werden bei jedem Erreichen oder Überschreiten des Schwellenwerts ausgelöst. Aufbauend auf der in der Abfrage implizierten Logik, kann dies zur Auslösung einer Warnung für eine Abfolge von Intervallen führen und entsprechend auch zum fortlaufenden Senden von Benachrichtigungen. Um dieses Szenario zu verhindern, kann ein Benutzer die Option „Suppress“ festlegen und Log Analytics anweisen, eine festgelegte Zeitspanne abzuwarten, ehe für die Warnungsregel zum zweiten Mal eine Benachrichtigung ausgelöst wird. Wenn also Suppress auf 30 Minuten festgelegt ist, wird die Warnung beim ersten Mal ausgelöst und führt zum Senden der konfigurierten Benachrichtigungen. Anschließend wird jedoch 30 Minuten gewartet, bevor erneut eine Benachrichtigung für die Warnungsregel verwendet wird. In der Zwischenzeit wird die Warnungsregel weiterhin ausgeführt – lediglich die Benachrichtigung wird von Log Analytics für den angegebenen Zeitraum unterdrückt, unabhängig von der Anzahl der Auslösungen der Warnungsregel in diesem Zeitraum.

Die Eigenschaft „Suppress“ der Log Analytics-Warnungsregel wird mithilfe des Werts für *Throttling*, der Unterdrückungszeitraum mithilfe des Werts für *DurationInMinutes* festgelegt.

Unten sehen eine Beispielantwort für eine Aktion, die nur einen Schwellenwert, einen Schweregrad und eine Suppress-Eigenschaft aufweist.

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
        "Version": 1    }

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Aktion für einen Zeitplan mit Schweregrad zu erstellen.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Schweregradaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Aktionsgruppen
Alle Warnungen in Azure verwenden Aktionsgruppen als Standardmechanismus für die Behandlung von Aktionen. Mit Aktionsgruppen können Sie Ihre Aktionen einmal angeben und die Aktionsgruppe dann mehreren Warnungen zuordnen. Dies gilt für sämtliche Bereiche in Azure, sodass Sie dieselbe Aktion nicht mehrmals deklarieren müssen. Aktionsgruppen unterstützen mehrere Aktionen – einschließlich E-Mails, SMS, Sprachanrufen, ITSM-Verbindungen, Automation-Runbooks, Webhook-URIs und anderen. 

Für Benutzer, die ihre Warnungen auf Azure erweitert haben, sollten bei Zeitplänen jetzt zusammen mit dem Schwellenwert auch Aktionsgruppendetails übergeben werden, um eine Warnung erstellen zu können. E-Mail-Details, Webhook-URLs, Runbook-Automatisierungsdetails und weitere Aktionen müssen zuerst in einer Aktivitätsgruppe definiert werden, bevor eine Warnung erstellt werden kann. Es ist möglich, im Portal [Aktionsgruppen aus Azure Monitor zu erstellen](../../azure-monitor/platform/action-groups.md) oder die [Aktionsgruppen-API](https://docs.microsoft.com/rest/api/monitor/actiongroups) zu verwenden.

Um eine Zuordnung einer Aktionsgruppe zu einer Warnung hinzuzufügen, geben Sie die eindeutige Azure Resource Manager-ID der Aktionsgruppe in der Warnungsdefinition an. Im Folgenden finden Sie eine Beispieldarstellung:

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
      },

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine bereits vorhandene Aktionsgruppe für einen Zeitplan zuzuordnen.  Im Folgenden finden eine Beispieldarstellung der Nutzung.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine zugeordnete Aktionsgruppe für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Anpassen von Aktionen
Standardmäßig gilt für Aktionen eine Standardvorlage und ein Standardformat für Benachrichtigungen. Benutzer können jedoch einige Aktionen anpassen, auch wenn sie durch Aktionsgruppen gesteuert werden. Derzeit ist die Anpassung für E-Mail-Betreff und Webhook-Nutzlast möglich.

##### <a name="customize-e-mail-subject-for-action-group"></a>Anpassen des E-Mail-Betreffs für Aktionsgruppen
Der Standard-E-Mail-Betreff für Warnungen lautet: „Alert Notification `<AlertName>` for `<WorkspaceName>`“. Dies kann jedoch angepasst werden, sodass Sie bestimmte Wörter oder Tags angeben können, mit denen Sie ohne Aufwand Filterregeln in Ihrem Posteingang einsetzen können. Die Details des angepassten E-Mail-Headers müssen mit den Details zur Aktionsgruppe gesendet werden, wie im Beispiel unten dargestellt.

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
      },

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine bereits vorhandene angepasste Aktionsgruppe für einen Zeitplan zuzuordnen.  Im Folgenden finden eine Beispieldarstellung der Nutzung.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine zugeordnete Aktionsgruppe für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Anpassen einer Webhook-Nutzlast für eine Aktionsgruppe
Der über Aktionsgruppen gesendete Webhook für Log Analytics weist standardmäßig eine feste Struktur auf. Die JSON-Nutzlast kann jedoch durch Verwendung bestimmter unterstützter Variablen angepasst werden, um die Anforderungen des Webhook-Endpunkts zu erfüllen. Weitere Informationen finden Sie unter [Webhookaktionen für Protokollwarnungsregeln](../../azure-monitor/platform/alerts-log-webhook.md). 

Die Details des angepassten Webhooks müssen mit den Details zur Aktionsgruppe gesendet werden. Sie werden auf alle in der Aktionsgruppe angegebenen Webhook-URIs angewendet, wie im Beispiel unten dargestellt.

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

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine bereits vorhandene angepasste Aktionsgruppe für einen Zeitplan zuzuordnen.  Im Folgenden finden eine Beispieldarstellung der Nutzung.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine zugeordnete Aktionsgruppe für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>E-Mail-Benachrichtigung
Per E-Mail-Benachrichtigung werden E-Mails an einen oder mehrere Empfänger gesendet.  Sie enthalten die Eigenschaften, die in der folgenden Tabelle angegeben sind.

> [!NOTE]
> Ab 14. Mai 2018 werden alle Warnungen in einer öffentlichen Instanz der Azure Cloud in einem Log Analytics-Arbeitsbereich automatisch auf Azure ausgedehnt. Ein Benutzer kann die Ausdehnung von Warnungen auf Azure bereits vor dem 14. Mai 2018 freiwillig initiieren. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure aus Log Analytics](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen wie E-Mail-Benachrichtigungen nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure erweitert werden, können Sie über die [Aktionsgruppen-API](https://docs.microsoft.com/rest/api/monitor/actiongroups) Aktionen abrufen oder hinzufügen.
   

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Recipients |Liste mit den E-Mail-Adressen |
| Subject |E-Mail-Betreff |
| Attachment |Anlagen werden derzeit nicht unterstützt. Der Wert lautet daher immer „Keine“. |

Unten ist eine Beispielantwort für eine E-Mail-Benachrichtigungsaktion mit einem Schwellenwert angegeben.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue E-Mail-Aktion für einen Zeitplan zu erstellen.  Im folgenden Beispiel wird eine E-Mail-Benachrichtigung mit einem Schwellenwert erstellt, damit die E-Mail gesendet wird, wenn die Ergebnisse der gespeicherten Suche den Schwellenwert überschreiten.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine E-Mail-Aktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Aktionen zur Problembehebung
Bei Behebungen wird ein Runbook in Azure Automation gestartet, mit dem versucht wird, das von der Warnung identifizierte Problem zu korrigieren.  Sie müssen einen Webhook für das Runbook erstellen, das in der Aktion für die Problembehebung verwendet wird, und anschließend den URI in der WebhookUri-Eigenschaft angeben.  Wenn Sie diese Aktion mit dem Azure-Portal erstellen, wird automatisch ein neuer Webhook für das Runbook erstellt.

> [!NOTE]
> Ab 14. Mai 2018 werden alle Warnungen in einer öffentlichen Instanz der Azure Cloud in einem Log Analytics-Arbeitsbereich automatisch auf Azure ausgedehnt. Ein Benutzer kann die Ausdehnung von Warnungen auf Azure bereits vor dem 14. Mai 2018 freiwillig initiieren. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure aus Log Analytics](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen wie die Wartung mithilfe von Runbooks nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure erweitert werden, können Sie über die [Aktionsgruppen-API](https://docs.microsoft.com/rest/api/monitor/actiongroups) Aktionen abrufen oder hinzufügen.

Behebungen enthalten die Eigenschaften, die in der folgenden Tabelle angegeben sind.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| RunbookName |Der Name des Runbooks. Er muss mit einem veröffentlichten Runbook im Automation-Konto übereinstimmen, das in der Automation-Lösung in Ihrem Log Analytics-Arbeitsbereich konfiguriert ist. |
| WebhookUri |Der URI des Webhooks. |
| Expiry |Das Ablaufdatum und die Uhrzeit des Webhooks.  Wenn der Webhook kein Ablaufdatum enthält, kann dies jedes gültige Datum in der Zukunft sein. |

Unten ist eine Beispielantwort für eine Aktion zur Problembehebung mit einem Schwellenwert angegeben.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Aktion zur Problembehandlung für einen Zeitplan zu erstellen.  Im folgenden Beispiel wird eine Problembehebung mit einem Schwellenwert erstellt, damit das Runbook gestartet wird, wenn die Ergebnisse der gespeicherten Suche den Schwellenwert überschreiten.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Aktion zur Problembehandlung für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Beispiel
Unten ist ein vollständiges Beispiel zur Erstellung einer neuen E-Mail-Benachrichtigung angegeben.  Es werden ein neuer Zeitplan und eine Aktion mit einem Schwellenwert und einer E-Mail erstellt.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"
    
    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson
    
    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Webhookaktionen
Bei Webhookaktionen wird ein Prozess gestartet, indem eine URL aufgerufen und optional eine zu sendende Nutzlast bereitgestellt wird.  Diese Aktionen sind mit Aktionen zur Problembehebung vergleichbar. Sie sind aber für Webhooks bestimmt, mit denen andere Prozesse als Azure Automation-Runbooks aufgerufen werden können.  Außerdem verfügen sie über die zusätzliche Option zum Angeben einer Nutzlast, die für den Remoteprozess bereitgestellt wird.

> [!NOTE]
> Ab 14. Mai 2018 werden alle Warnungen in einer öffentlichen Instanz der Azure Cloud in einem Log Analytics-Arbeitsbereich automatisch auf Azure ausgedehnt. Ein Benutzer kann die Ausdehnung von Warnungen auf Azure bereits vor dem 14. Mai 2018 freiwillig initiieren. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure aus Log Analytics](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen wie Webhooks nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure erweitert werden, können Sie über die [Aktionsgruppen-API](https://docs.microsoft.com/rest/api/monitor/actiongroups) Aktionen abrufen oder hinzufügen.


Webhookaktionen verfügen nicht über einen Schwellenwert, sondern sollten stattdessen einem Zeitplan hinzugefügt werden, der über eine Warnungsaktion mit Schwellenwert verfügt.  

Unten sind eine Beispielantwort für eine Webhookaktion und eine zugeordnete Warnungsaktion mit einem Schwellenwert angegeben.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Erstellen oder Bearbeiten einer Webhookaktion
Verwenden Sie die Put-Methode mit einer eindeutigen Aktions-ID, um eine neue Webhookaktion für einen Zeitplan zu erstellen.  Im folgenden Beispiel werden eine Webhookaktion und eine Warnungsaktion mit einem Schwellenwert erstellt, damit der Webhook ausgelöst wird, wenn die Ergebnisse der gespeicherten Suche den Schwellenwert überschreiten.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Verwenden Sie die Put-Methode mit einer vorhandenen Aktions-ID, um eine Webhookaktion für einen Zeitplan zu ändern.  Der Hauptteil der Anforderung muss das ETag der Aktion enthalten.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie die [REST-API zum Durchführen von Protokollsuchen](../../azure-monitor/log-query/log-query-overview.md) in Log Analytics.
* Erfahren Sie mehr über [Protokollwarnungen in Azure-Warnungen](../../azure-monitor/platform/alerts-unified-log.md).

