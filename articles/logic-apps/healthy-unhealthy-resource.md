---
title: Einrichten der Protokollierung zum Überwachen von Logik-Apps in Azure Security Center
description: Überwachen Sie die Integrität Ihrer Logic Apps-Ressourcen in Azure Security Center, indem Sie die Diagnoseprotokollierung einrichten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 9c22e7cbc71c2b29c4b3e50319c6b6d256856bb3
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855559"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Einrichten der Protokollierung zum Überwachen von Logik-Apps in Azure Security Center

Bei der Überwachung Ihrer Logic Apps-Ressourcen in [Microsoft Azure Security Center](../security-center/security-center-introduction.md) können Sie [überprüfen, ob die Logik-Apps den Standardrichtlinien entsprechen](#view-logic-apps-health-status). In Azure wird der Integritätsstatus für eine Logic Apps-Ressource angezeigt, nachdem Sie die Protokollierung aktiviert und das Ziel der Protokolle ordnungsgemäß eingerichtet haben. In diesem Artikel wird erläutert, wie Sie die Diagnoseprotokollierung konfigurieren und sicherstellen, dass alle Logik-Apps fehlerfreie Ressourcen sind.

> [!TIP]
> Informationen zum aktuellen Status des Logic Apps-Diensts finden Sie auf der Seite [Azure-Status](https://status.azure.com/), auf der die Status für verschiedene Produkte und Dienste in jeder verfügbaren Region aufgeführt sind.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie noch nicht über ein Abonnement verfügen, müssen Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
* Vorhandene Logik-Apps mit [aktivierter Diagnoseprotokollierung](#enable-diagnostic-logging).
* Ein Log Analytics-Arbeitsbereich, der zum Aktivieren der Protokollierung für die Logik-App erforderlich ist. [Erstellen Sie zunächst Ihren Arbeitsbereich](/learn/quick-create-workspace.md), wenn noch kein Arbeitsbereich vorhanden ist.

## <a name="enable-diagnostic-logging"></a>Aktivieren der Diagnoseprotokollierung

Damit Sie den Integritätsstatus von Ressourcen anzeigen können, müssen Sie zunächst [die Diagnoseprotokollierung einrichten](monitor-logic-apps-log-analytics.md). Wenn Sie bereits über einen Log Analytics-Arbeitsbereich verfügen, können Sie die Protokollierung entweder beim Erstellen Ihrer Logik-App oder für vorhandene Logik-Apps aktivieren.

> [!TIP]
> Standardmäßig wird empfohlen, Diagnoseprotokolle für Logic Apps zu aktivieren. Diese Einstellung können Sie jedoch für Ihre Logik-Apps steuern. Wenn Sie Diagnoseprotokolle für Ihre Logik-Apps aktivieren, können Sie anhand der Informationen Sicherheitsvorfälle analysieren.

### <a name="check-diagnostic-logging-setting"></a>Überprüfen der Einstellung für die Diagnoseprotokollierung

Wenn Sie nicht sicher sind, ob die Diagnoseprotokollierung für Ihre Logik-Apps aktiviert ist, können Sie dies in Security Center überprüfen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie auf der Suchleiste **Security Center** ein, und wählen Sie den entsprechenden Eintrag aus.
1. Wählen Sie im Menü auf dem Security Center-Dashboard unter **Allgemein** die Option **Empfehlungen** aus.
1. Suchen Sie in der Tabelle mit den Sicherheitsvorschlägen nach **Überwachung und Protokollierung aktivieren** &gt; **In Logic Apps müssen Diagnoseprotokolle aktiviert sein**, und wählen Sie die Empfehlung aus.
1. Erweitern Sie auf der Empfehlungsseite den Abschnitt **Schritte zur Korrektur**, und überprüfen Sie die Optionen. Sie können die Logic Apps-Diagnose aktivieren, indem Sie die Schaltfläche **Schnelle Problembehebung** auswählen oder die Anweisungen für die manuelle Korrektur befolgen.

## <a name="view-logic-apps-health-status"></a>Anzeigen des Integritätsstatus von Logik-Apps

Nachdem Sie [die Diagnoseprotokollierung aktiviert](#enable-diagnostic-logging) haben, können Sie den Integritätsstatus Ihrer Logik-Apps in Security Center anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie auf der Suchleiste **Security Center** ein, und wählen Sie den entsprechenden Eintrag aus.
1. Wählen Sie im Menü auf dem Security Center-Dashboard unter **Allgemein** die Option **Bestand** aus.
1. Filtern Sie auf der Seite „Bestand“ die Liste Ihrer Ressourcen, um nur Logic Apps-Ressourcen anzuzeigen. Wählen Sie im Seitenmenü die Optionen **Ressourcentypen** &gt; **Logik-Apps** aus.

   Der Zähler **Fehlerhafte Ressourcen** zeigt die Anzahl der Logik-Apps an, die in Security Center als fehlerhaft eingestuft werden.
1.  Überprüfen Sie in der Liste der Logic Apps-Ressourcen die Spalte **Empfehlungen**. Wählen Sie zum Überprüfen der Integritätsdetails für eine bestimmte Logik-App einen Ressourcennamen oder die Schaltfläche mit den Auslassungspunkten ( **...** ) &gt; **Ressource anzeigen** aus.
1.  Um potenzielle Probleme mit der Ressourcenintegrität zu beheben, führen Sie die aufgeführten Schritte für Ihre Logik-Apps aus.

Wenn die Diagnoseprotokollierung bereits aktiviert ist, liegt möglicherweise ein Problem mit dem Ziel für die Protokolle vor. Weitere Informationen dazu finden Sie unter [Beheben von Problemen bei unterschiedlichen Zielen der Diagnoseprotokollierung](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Korrigieren der Diagnoseprotokollierung für Logik-Apps

Wenn Ihre [Logik-Apps in Security Center als fehlerhaft aufgeführt sind](#view-logic-apps-health-status), öffnen Sie die jeweilige Logik-App in der Codeansicht im Azure-Portal oder über die Azure-Befehlszeilenschnittstelle. Überprüfen Sie dann die Zielkonfiguration für die Diagnoseprotokolle: [Azure Log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations) oder [ein Azure Storage-Konto](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics- und Event Hubs-Ziele

Wenn Sie Log Analytics oder Event Hubs als Ziel für die Logic Apps-Diagnoseprotokolle verwenden, überprüfen Sie die folgenden Einstellungen. 

1. Um zu überprüfen, ob Diagnoseprotokolle aktiviert sind, vergewissern Sie sich, ob das Feld `logs.enabled` der Diagnoseeinstellungen auf `true` festgelegt ist. 
1. Um zu überprüfen, ob Sie stattdessen nicht ein Speicherkonto als Ziel angegeben haben, vergewissern Sie sich, ob das Feld `storageAccountId` auf `false` festgelegt ist.

Beispiel:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Speicherkontoziel

Wenn Sie ein Speicherkonto als Ziel für die Logic Apps-Diagnoseprotokolle verwenden, überprüfen Sie die folgenden Einstellungen.

1. Um zu überprüfen, ob Diagnoseprotokolle aktiviert sind, vergewissern Sie sich, dass das Feld `logs.enabled` der Diagnoseeinstellungen auf `true` festgelegt ist.
1. Um zu überprüfen, ob Sie eine Aufbewahrungsrichtlinie für die Diagnoseprotokolle aktiviert haben, vergewissern Sie sich, dass das Feld `retentionPolicy.enabled` auf `true` festgelegt ist.
1. Um zu überprüfen, ob Sie eine Aufbewahrungsdauer von 0–365 Tagen angegeben haben, vergewissern Sie sich, dass das Feld `retentionPolicy.days` auf eine Zahl zwischen 0 und 365 festgelegt ist.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```
