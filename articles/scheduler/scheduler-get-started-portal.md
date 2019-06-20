---
title: Erstellen von geplanten Aufträgen mit Azure Scheduler – Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihren ersten automatisierten Auftrag mit Azure Scheduler im Azure-Portal erstellen, planen und ausführen können.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532015"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Erstellen und Planen des ersten Auftrags mit Azure Scheduler – Azure-Portal

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt Azure Scheduler, der eingestellt wird. Zum Planen von Aufträgen, sollten Sie [stattdessen Azure Logic Apps testen](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

In diesem Tutorial wird gezeigt, wie Sie einen Auftrag erstellen und planen und diesen Auftrag dann überwachen und verwalten können. 

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

## <a name="create-job"></a>Erstellen eines Auftrags

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.  

1. Wählen Sie im Azure-Menü **Ressource erstellen** aus. Geben Sie „scheduler“ in das Suchfeld ein. Wählen Sie in der Ergebnisliste **Scheduler** und dann **Erstellen** aus.

   ![Erstellen einer Ressource in Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Erstellen Sie nun einen Auftrag, der eine GET-Anforderung an die folgende URL sendet: `https://www.microsoft.com/` 

1. Geben Sie unter **Scheduler-Auftrag** folgende Informationen ein:

   | Eigenschaft | Beispielwert | BESCHREIBUNG |
   |----------|---------------|-------------| 
   | **Name** | getMicrosoft | Der Name für Ihren Auftrag. | 
   | **Auftragssammlung** | <*job-collection-name*> | Erstellen Sie eine Auftragssammlung, oder wählen Sie eine vorhandene Sammlung aus. | 
   | **Abonnement** | <*Name des Azure-Abonnements*> | Der Name Ihres Azure-Abonnements | 
   |||| 

1. Wählen Sie **Aktionseinstellungen – Konfigurieren** aus, geben Sie die jeweiligen Informationen ein, und klicken Sie dann auf **OK**:

   | Eigenschaft | Beispielwert | BESCHREIBUNG |
   |----------|---------------|-------------| 
   | **Aktion** | **Http** | Der Typ der auszuführenden Aktion. | 
   | **Methode** | **Get** | Die aufzurufende Methode. | 
   | **URL** | **https://www.microsoft.com** | Die Ziel-URL. | 
   |||| 
   
   ![Definieren eines Auftrags](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Wählen Sie **Zeitplan – Konfigurieren** aus, definieren Sie den Zeitplan, und klicken Sie dann auf **OK**:

   Sie können zwar einen einmaligen Auftrag erstellen, in diesem Beispiel wird jedoch ein Wiederholungszeitplan eingerichtet.

   | Eigenschaft | Beispielwert | BESCHREIBUNG |
   |----------|---------------|-------------| 
   | **Serie** | **Serie** | Ein einmaliger Auftrag oder ein wiederkehrender Auftrag | 
   | **Start am** | <*today's-date*> | Startdatum des Auftrags | 
   | **Wiederholen alle** | **Stündlich** | Das Wiederholungsintervall und die Häufigkeit. | 
   | **Ende** | **Endet am**: Zwei Tage nach dem heutigen Datum | Enddatum des Auftrags | 
   | **UTC-Abweichung** | **UTC +08:00** | Der Zeitunterschied zwischen Coordinated Universal Time (UTC) und dem beobachteten Zeitpunkt Ihres Standorts | 
   |||| 

   ![Definieren eines Zeitplans](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Wählen Sie abschließend **Erstellen** aus.

   Nach der Erstellung Ihres Auftrags stellt Azure Ihren Auftrag bereit, der auf dem Azure-Dashboard angezeigt wird. 

1. Wenn in Azure eine Benachrichtigung angezeigt wird, dass die Bereitstellung erfolgreich war, klicken Sie auf **An Dashboard anheften**. Klicken Sie andernfalls auf der Azure-Symbolleiste auf das Symbol **Benachrichtigungen** (Glocke) und dann auf **An Dashboard anheften**.

## <a name="monitor-and-manage-jobs"></a>Überwachen und Verwalten von Aufträgen

Um Ihren Auftrag zu überprüfen, zu überwachen und zu verwalten, wählen Sie ihn auf dem Azure-Dashboard aus. Unter **Einstellungen** werden die Bereiche angezeigt, die Sie für Ihren Auftrag überprüfen und verwalten können:

![Auftragseinstellungen](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Um weitere Informationen zu diesen Bereichen anzuzeigen, wählen Sie einen Bereich aus:

* [**Eigenschaften**](#properties)
* [**Aktionseinstellungen**](#action-settings)
* [**Zeitplan**](#schedule)
* [**Verlauf**](#history)
* [**Benutzer**](#users)

<a name="properties"></a>

### <a name="properties"></a>Eigenschaften

Um schreibgeschützte Eigenschaften anzuzeigen, die die Verwaltungsmetadaten für Ihren Auftrag beschreiben, klicken Sie auf **Eigenschaften**.

![Anzeigen von Auftragseigenschaften](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Aktionseinstellungen

Um die erweiterten Einstellungen Ihres Auftrags zu ändern, klicken Sie auf **Aktionseinstellungen**. 

![Überprüfen von Aktionseinstellungen](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Aktionstyp | BESCHREIBUNG | 
|-------------|-------------| 
| Alle Typen | Sie können die Einstellung **Wiederholungsrichtlinie** und die **Fehleraktion** ändern. | 
| HTTP und HTTPS | Sie können die **Methode** in eine zulässige Methode ändern. Darüber hinaus können Sie die Header sowie Standardauthentifizierungsinformationen hinzufügen, löschen oder ändern. | 
| Speicherwarteschlange| Sie können das Speicherkonto, den Warteschlangennamen, den SAS-Token und den Text ändern. | 
| Service Bus | Sie können den Namespace, den Thema- oder Warteschlangenpfad, die Authentifizierungseinstellungen, den Transporttyp, die Nachrichteneigenschaften und den Nachrichtentext ändern. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Schedule

Wenn Sie einen Zeitplan über den Auftragsassistenten einrichten, können Sie diesen Zeitplan ändern, z.B. Startdatum und -zeit, Wiederholungszeitplan und Enddatum und -zeit für wiederkehrende Aufträge.
Zudem können Sie [komplexere Zeitpläne und fortgeschrittener Serien erstellen](scheduler-advanced-complexity.md).

Um die Ansicht anzuzeigen oder Ihren Auftragszeitplan zu ändern, klicken Sie auf **Zeitplan**:

![Anzeigen des Auftragszeitplans](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Verlauf

Um Metriken zu jeder Ausführung für einen ausgewählten Auftrag anzuzeigen, klicken Sie auf **Verlauf**. Diese Metriken liefern Echtzeitwerte zur Integrität Ihres Auftrags, wie Status, Anzahl von Wiederholungen, Anzahl der Vorkommen, Startzeit und Endzeit.

![Anzeigen des Auftragsverlaufs und der Metriken](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Um die Details zum Verlauf der einzelnen Ausführungen anzuzeigen (z.B. vollständige Antwort für jede Ausführung), wählen Sie unter **Verlauf** die jeweilige Ausführung aus. 

![Anzeigen von Auftragsverlaufsdetails](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Benutzer

Mit der rollenbasierten Zugriffssteuerung (RBAC) in Azure können Sie den Zugriff auf Azure Scheduler für jeden Benutzer auf granularer Ebene verwalten. Informationen zum Einrichten des Zugriffs basierend auf Rollen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Konzepte, Terminologie und Entitäten in Azure Scheduler](scheduler-concepts-terms.md).
* [Erstellen von erweiterten Zeitplänen und Wiederholungen für Aufträge im Azure Scheduler](scheduler-advanced-complexity.md)
* Erfahren Sie mehr über die [Hochverfügbarkeit und Zuverlässigkeit bei Azure Scheduler](scheduler-high-availability-reliability.md).
* Erfahren Sie mehr über [Grenzwerte, Kontingente und Drosselungsschwellenwerte im Azure Scheduler](scheduler-limits-defaults-errors.md).
