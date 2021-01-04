---
title: Analysieren von Aktivitätsprotokollen mithilfe von Azure Monitor-Protokollen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Active Directory-Aktivitätsprotokolle mithilfe von Azure Monitor-Protokollen analysieren.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4fde22b1b8d72ae8ae775c090e0da25ce0665f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181168"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analysieren von Azure AD-Aktivitätsprotokollen mithilfe von Azure Monitor-Protokollen

Nachdem Sie [Azure AD-Aktivitätsprotokolle mit Azure Monitor-Protokollen integriert haben](howto-integrate-activity-logs-with-log-analytics.md), können Sie die Leistungsfähigkeit von Azure Monitor-Protokollen nutzen, um Einblicke in Ihre Umgebung zu erhalten. Sie können auch die [Log Analytics-Ansichten für Azure AD-Aktivitätsprotokolle](howto-install-use-log-analytics-views.md) installieren, um Zugriff auf vorgefertigte Berichte zu Überwachungs- und Anmeldeereignissen in Ihrer Umgebung zu erhalten.

In diesem Artikel erfahren Sie, wie Sie die Azure AD-Aktivitätsprotokolle in Ihrem Log Analytics-Arbeitsbereich analysieren. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen 

Um dies nachvollziehen zu können, benötigen Sie Folgendes:

* Einen Log Analytics-Arbeitsbereich in Ihrem Azure-Abonnement. Informationen zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie [hier](../../azure-monitor/learn/quick-create-workspace.md).
* Führen Sie zuerst die Schritte aus, um [die Azure AD-Aktivitätsprotokolle in Ihren Log Analytics-Arbeitsbereich umzuleiten](howto-integrate-activity-logs-with-log-analytics.md).
*  [Zugriff](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) auf den Log Analytics-Arbeitsbereich
* Folgende Rollen in Azure Active Directory (beim Zugriff auf Log Analytics über das Azure Active Directory-Portal)
    - Sicherheitsadministrator
    - Sicherheitsleseberechtigter
    - Report Reader (Leseberechtigter für Berichte)
    - Globaler Administrator
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navigieren zum Log Analytics-Arbeitsbereich

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

2. Wählen Sie **Azure Active Directory** aus, und wählen Sie dann **Protokolle** im Abschnitt **Überwachung** aus, um Ihren Log Analytics-Arbeitsbereich zu öffnen. Der Arbeitsbereich wird mit einer Standardabfrage geöffnet.

    ![Standardabfrage](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Anzeigen des Schemas für Azure AD-Aktivitätsprotokollen

Die Protokolle werden per Push an die Tabellen **AuditLogs** und **SigninLogs** im Arbeitsbereich übertragen. So zeigen Sie das Schema für diese Tabellen an

1. Wählen Sie in der Standardabfrageansicht des vorherigen Abschnitts **Schema** aus, und erweitern Sie den Arbeitsbereich. 

2. Erweitern Sie den Abschnitt **Protokollverwaltung**, und erweitern Sie dann entweder **AuditLogs** oder **SigninLogs**, um das Protokollschema anzuzeigen.
    ![Überwachungsprotokolle](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Anmeldeprotokolle](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Abfragen der Azure AD-Aktivitätsprotokolle

Nachdem Sie nun die Protokolle in Ihrem Arbeitsbereich haben, können Sie Abfragen für diese ausführen. Um beispielsweise die in der letzten Woche am häufigsten verwendeten Anwendungen abzurufen, ersetzten Sie die Standardabfrage durch Folgendes, und wählen Sie **Ausführen** aus.

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Um die häufigsten Überwachungsereignisse in der letzten Woche abzurufen, verwenden Sie die folgende Abfrage:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Benachrichtigung bei Azure AD-Aktivitätsprotokolldaten

Sie können auch Benachrichtigungen für Ihre Abfrage einrichten. So konfigurieren Sie beispielsweise eine Benachrichtigung, wenn mehr als 10 Anwendungen in der letzten Woche verwendet wurden:

1. Wählen Sie im Arbeitsbereich **Benachrichtigung festlegen** aus, um die Seite **Regel erstellen** zu öffnen.

    ![Warnung festlegen](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Wählen Sie die standardmäßig in der Benachrichtigung erstellten **Benachrichtigungskriterien** aus, und aktualisieren Sie den **Schwellenwert** in der Standardmetrik auf 10.

    ![Benachrichtigungskriterien](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Geben Sie einen Namen und eine Beschreibung für die Benachrichtigung ein, und wählen Sie den Schweregrad aus. In unserem Beispiel könnten wir ihn auf **Information** festlegen.

4. Wählen Sie die **Aktionsgruppe** aus, die benachrichtigt wird, wenn das Signal auftritt. Sie können auch auswählen, Ihr Team per E-Mail oder SMS zu benachrichtigen, oder Sie könnten die Aktion mithilfe von Webhooks, Azure-Funktionen oder Logik-Apps automatisieren. Erfahren Sie mehr über das [Erstellen und Verwalten von Benachrichtigungen im Azure-Portal](../../azure-monitor/platform/action-groups.md).

5. Nachdem Sie die Benachrichtigung konfiguriert haben, wählen Sie **Benachrichtigung erstellen** aus, um sie zu aktivieren. 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>Verwenden von vorgefertigten Arbeitsmappen für Azure AD-Aktivitätsprotokolle

Die Arbeitsmappen bieten mehrere Berichte, die im Zusammenhang mit allgemeinen Szenarien mit Überwachungs-, Anmelde- und Bereitstellungsereignissen stehen. Sie können auch aufgrund jeglicher Daten benachrichtigen, die in den Berichten bereitgestellt werden, indem Sie die im vorherigen Abschnitt beschriebenen Schritte verwenden.

* **Bereitstellungsanalyse:** In dieser [Arbeitsmappe](../app-provisioning/application-provisioning-log-analytics.md) werden Berichte im Zusammenhang mit der Überwachung von Bereitstellungsaktivitäten angezeigt, also z. B. die Anzahl neuer bereitgestellter Benutzer und die Bereitstellungsfehler, die Anzahl aktualisierter Benutzer und die Aktualisierungsfehler sowie die Anzahl aufgehobener Benutzerbereitstellungen und der entsprechenden Fehler.    
* **Anmeldeereignisse**: In dieser Arbeitsmappe werden die relevantesten Berichte zur Überwachung von Anmeldeaktivitäten angezeigt, z. B. Anmeldungen nach Anwendungen, Benutzern, Geräten sowie eine Zusammenfassungsansicht, in der die Anzahl der Anmeldungen im Lauf der Zeit nachverfolgt wird.
* **Erkenntnisse zum bedingten Zugriff:** Mithilfe der [Arbeitsmappe](../conditional-access/howto-conditional-access-insights-reporting.md) für Erkenntnisse und Berichterstellung für den bedingten Zugriff können Sie die Auswirkungen von Richtlinien für den bedingten Zugriff in Ihrer Organisation im zeitlichen Verlauf nachvollziehen. 

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Abfragen in Azure Monitor-Protokollen](../../azure-monitor/log-query/get-started-queries.md)
* [Erstellen und Verwalten von Benachrichtigungsgruppen im Azure-Portal](../../azure-monitor/platform/action-groups.md)
* [Installieren und Verwenden der Log Analytics-Ansichten für Azure Active Directory](howto-install-use-log-analytics-views.md)