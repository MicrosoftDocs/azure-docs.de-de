---
title: Installieren und Verwenden der Log Analytics-Ansichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Log Analytics-Ansichten für Azure Active Directory installieren und verwenden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: d9fc1592681429998f5082fbadcd45fa068f130e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180471"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installieren und Verwenden der Log Analytics-Ansichten für Azure Active Directory

Mithilfe der Log Analytics-Ansichten für Azure Active Directory können Sie die Azure AD-Aktivitätsprotokolle in Ihrem Azure AD-Mandanten analysieren und durchsuchen. Azure AD-Aktivitätsprotokolle umfassen:

* Überwachungsprotokolle: Mit dem [Aktivitätsbericht zu Überwachungsprotokollen](concept-audit-logs.md) erhalten Sie Zugriff auf den Verlauf aller Aufgaben, die in Ihrem Mandanten durchgeführt werden.
* Anmeldeprotokolle: Mit dem [Aktivitätsbericht zu Anmeldungen](concept-sign-ins.md) können Sie ermitteln, von wem die Aufgaben durchgeführt wurden, die in den Überwachungsprotokollen aufgeführt sind.

## <a name="prerequisites"></a>Voraussetzungen

Um die Log Analytics-Ansichten verwenden zu können, benötigen Sie Folgendes:

* Einen Log Analytics-Arbeitsbereich in Ihrem Azure-Abonnement. Informationen zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie [hier](../../azure-monitor/learn/quick-create-workspace.md).
* Führen Sie zuerst die Schritte aus, um [die Azure AD-Aktivitätsprotokolle in Ihren Log Analytics-Arbeitsbereich umzuleiten](howto-integrate-activity-logs-with-log-analytics.md).
* Laden Sie die Ansichten aus dem [GitHub-Repository](https://aka.ms/AADLogAnalyticsviews) auf Ihren lokalen Computer herunter.

## <a name="install-the-log-analytics-views"></a>Installieren der Log Analytics-Ansichten

1. Navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich. Navigieren Sie hierzu zuerst zum [Azure-Portal](https://portal.azure.com), und wählen Sie **Alle Dienste** aus. Geben Sie **Log Analytics** in das Textfeld ein, und wählen Sie **Log Analytics-Arbeitsbereiche** aus. Wählen Sie den Arbeitsbereich aus, zu dem Sie die Aktivitätsprotokolle im Rahmen der Voraussetzungen umgeleitet haben.
2. Wählen Sie **Ansicht-Designer** aus, dann **Importieren**, und wählen Sie dann **Datei auswählen** aus, um die Ansichten von Ihrem lokalen Computer zu importieren.
3. Wählen Sie die Ansichten aus, die Sie aus den Voraussetzungen heruntergeladen haben, und wählen Sie **Speichern** aus, um den Import zu speichern. Führen Sie diesen Vorgang für die Ansichten **Azure AD-Kontobereitstellungsereignisse** und **Anmeldeereignisse** aus.

## <a name="use-the-views"></a>Verwenden der Ansichten

1. Navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich. Navigieren Sie hierzu zuerst zum [Azure-Portal](https://portal.azure.com), und wählen Sie **Alle Dienste** aus. Geben Sie **Log Analytics** in das Textfeld ein, und wählen Sie **Log Analytics-Arbeitsbereiche** aus. Wählen Sie den Arbeitsbereich aus, zu dem Sie die Aktivitätsprotokolle im Rahmen der Voraussetzungen umgeleitet haben.

2. Sobald Sie sich in Ihrem Arbeitsbereich befinden, wählen Sie **Zusammenfassung des Arbeitsbereichs** aus. Die folgenden drei Ansichten sollten angezeigt werden:

    * **Azure AD-Kontobereitstellungsereignisse**: In dieser Ansicht werden Berichte im Zusammenhang mit der Überwachung von Bereitstellungsaktivitäten angezeigt, also z. B. die Anzahl neuer bereitgestellter Benutzer und die Bereitstellungsfehler, die Anzahl aktualisierter Benutzer und die Aktualisierungsfehler sowie die Anzahl aufgehobener Benutzerbereitstellungen und der entsprechenden Fehler.    
    * **Anmeldeereignisse**: In dieser Ansicht werden die relevantesten Berichte zur Überwachung von Anmeldeaktivitäten angezeigt, z. B. Anmeldungen nach Anwendungen, Benutzern, Geräten sowie eine Zusammenfassungsansicht, in der die Anzahl der Anmeldungen im Laufe der Zeit nachverfolgt wird.

3. Wählen Sie eine der beiden Ansichten aus, um zu den einzelnen Berichten zu wechseln. Sie können auch Benachrichtigungen für jeden der Berichtsparameter festlegen. Lassen Sie uns zum Beispiel eine Benachrichtigung bei jedem Mal festlegen, wenn es zu einem Anmeldefehler kommt. Zu diesem Zweck wählen Sie zuerst die Ansicht **Anmeldeereignisse** aus, dann den Bericht **Anmeldefehler im Laufe der Zeit**, und wählen Sie dann **Analytics** aus, um die Seite mit den Details zu öffnen, in denen sich die eigentliche Abfrage befindet, die dem Bericht zugrunde liegt. 

    ![Screenshot der Analytics-Detailseite mit der Abfrage für den Bericht.](./media/howto-install-use-log-analytics-views/details.png)


4. Wählen Sie **Benachrichtigung festlegen** aus, und wählen Sie dann **Immer wenn die benutzerdefinierte Protokollsuche &lt;Logik nicht definiert ist&gt;** im Abschnitt **Benachrichtigungskriterien** aus. Da wir immer dann benachrichtigen möchten, wenn ein Anmeldefehler auftritt, legen Sie den **Schwellenwert** der Standardbenachrichtigungslogik auf **1** fest, und wählen Sie dann **Fertig** aus. 

    ![Konfigurieren der Signallogik](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Geben Sie einen Namen und eine Beschreibung für die Benachrichtigung ein, und legen Sie den Schweregrad auf **Benachrichtigung** fest.

    ![Erstellen einer Regel](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Wählen Sie die zu benachrichtigende Aktionsgruppe aus. Dies kann im Allgemeinen entweder ein Team sein, das Sie per E-Mail oder SMS benachrichtigen möchten, oder es kann eine automatisierte Aufgabe sein, die Webhooks, Runbooks, Funktionen, Logik-Apps oder externe ITSM-Lösungen verwendet. Erfahren Sie, wie Sie [Aktionsgruppen im Azure-Portal erstellen und verwalten](../../azure-monitor/platform/action-groups.md).

7. Wählen Sie **Benachrichtigungsregel erstellen** aus, um die Benachrichtigung zu erstellen. Jetzt werden Sie jedes Mal benachrichtigt, wenn ein Anmeldefehler auftritt.

## <a name="next-steps"></a>Nächste Schritte

* [Analysieren von Aktivitätsprotokollen mit Azure Monitor-Protokollen](howto-analyze-activity-logs-log-analytics.md)
* [Erste Schritte mit Azure Monitor-Protokollen im Azure-Portal](../../azure-monitor/log-query/log-analytics-tutorial.md)