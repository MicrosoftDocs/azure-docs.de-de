---
title: Benutzerinitiierte Azure Automation-Runbook-Aktion in Log Analytics | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie ein Automation-Runbook über ein Log Analytics-Suchergebnis bei Bedarf ausgeführt wird.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 9194d5fe6553607ac5a0bb4e133da97f53790984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424733"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Führen Sie die Aktion mit einem Automation-Runbook über ein Log Analytics-Protokollsuchergebnis aus

> [!NOTE]
> Das Starten eines Runbooks in Suchergebnissen ist eine Funktion des klassischen Portals für die Protokollsuche, die am 15. Februar 2019 eingestellt wird. Sie können eine Aktionsgruppe konfigurieren, mit der neben anderen Aktionen ein Runbook über eine [Warnungsregel](../platform/alerts-log.md) in Azure Monitor gestartet werden kann.

Sie können in einem Protokollsuchergebnis in Azure Log Analytics nun **Take action** (Aktion ausführen) auswählen, um ein Automation-Runbook auszuführen.  Das Runbook kann verwendet werden, um das Problem zu beheben oder eine andere Aktion auszuführen, z.B. Informationen zur Problembehandlung sammeln, eine E-Mail senden oder eine Dienstanforderung erstellen. 


## <a name="components-and-features-used"></a>Verwendete Komponenten und Features
* [Azure Automation-Konto](../../automation/automation-quickstart-create-account.md)
* [Log Analytics-Arbeitsbereich](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>So initiieren Sie ein Runbook über die Protokollsuche

Zum Ausführen einer Aktion zu einem Ereignis und Initiieren eines Runbooks über die Protokollsuchergebnisse erstellen Sie zuerst eine Protokollsuche. Über die Ergebnisse können Sie bei Bedarf ein Runbook aufrufen. Dieser Schritt kann über die klassische Protokollsuchfunktion im [Azure-Portal](../../azure-monitor/log-query/log-query-overview.md) ausgeführt werden. In diesem Beispiel führen Sie eine Protokollsuche über das Azure-Portal mit einer einfachen Demonstration dieser Funktion aus.

1. Klicken Sie im Azure-Portal auf **Alle Dienste**, und wählen Sie **Log Analytics** aus.  
2. Wählen Sie Ihren Log Analytics-Arbeitsbereich aus.
3. Wählen Sie im Arbeitsbereich **Protokolle (klassisch)** aus.  
4. Führen Sie auf der Seite „Protokollsuche“ eine Protokollsuche durch.  
5. Klicken Sie in den Protokollsuchergebnissen auf die Auslassungszeichen auf der linken Seite eines der Felder, und wählen Sie **Take action on** (Aktion ausführen für) aus.<br><br> ![Ausführen von Aktionen aus Suchergebnissen](./media/take-action/log-search-takeaction-menuoption.png) 
6. Wählen Sie **Runbook ausführen** und anschließend ein Runbook für die Ausführung aus.  Sie können jedes Runbook im Automation-Konto auswählen, das mit dem Log Analytics-Arbeitsbereich verknüpft ist.  Beachten Sie Folgendes:

    * Runbooks werden nach Tags organisiert
    * Die Werte der Runbook-Eingabeparameter können direkt aus den Feldern des Suchergebnisses ausgewählt werden.  Eine Dropdownliste wird angezeigt, in der alle verfügbaren Felder des Ergebnisses eingeblendet werden, in denen Sie eine Wahl treffen können.  
    * Sie können angeben, dass das Runbook auf einem [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) ausgeführt werden soll, den Sie auf dem Computer installiert haben, auf dem das Problem auftritt. Voraussetzung ist jedoch eine entsprechende Hybrid Runbook Worker-Gruppe, die nur diesen Computer als Mitglied enthält.  Wenn der Name der Hybrid Worker-Gruppe mit dem Namen des Computers aus dem Protokollsuchergebnis übereinstimmt, wird die Gruppe automatisch ausgewählt.    

6. Nachdem Sie auf **Ausführen** geklickt haben, wird die Seite mit Runbookaufträgen geöffnet, auf der Sie den Status des Auftrags überprüfen können.   

Wenn Sie ein Runbook auswählen, das so konfiguriert wurde, dass es [von einer Log Analytics-Warnung aufgerufen wird](../../automation/automation-create-alert-triggered-runbook.md), hat es einen Eingabeparameter mit dem Namen **WebhookData**, der vom Typ **Object** ist.  Wenn der Eingabeparameter obligatorisch ist, müssen Sie die Suchergebnisse an das Runbook übergeben, damit es die JSON-formatierte Zeichenfolge in einen Objekttyp konvertieren kann, sodass Sie nach bestimmten Elementen filtern können, auf die Sie in Runbook-Aktivitäten verweisen.  Wählen Sie dazu **Suchergebnis (Objekt)** in der Dropdownliste aus.<br><br> ![Wählen Sie das Webhook-Datenobjekt für den Runbook-Parameter aus](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie sich die [Referenz zur Log Analytics-Suche](../../azure-monitor/log-query/log-query-overview.md) durch, um sich über alle Suchfelder und Facetten zu informieren, die in Log Analytics verfügbar sind.
* Informationen zum automatischen Aufrufen eines Automation-Runbooks finden Sie unter [Aufrufen eines Azure Automation-Runbooks über eine Log Analytics-Warnung](../../automation/automation-create-alert-triggered-runbook.md).  
