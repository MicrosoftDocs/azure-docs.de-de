---
title: Verwalten von Warnbenachrichtigungen zu Geräteereignissen für Azure Stack Edge Pro-Ressourcen | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie im Azure-Portal Warnungen für Geräteereignisse in Ihren Azure Stack Edge Pro-Ressourcen verwalten.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 0ab7cdfb3d699a8415739565aae5d4326002bc43
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389143"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Verwalten von Warnbenachrichtigungen zu Geräteereignissen für Azure Stack Edge Pro-Ressourcen

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal Aktionsregeln erstellen, um Warnbenachrichtigungen für Geräteereignisse, die in einer Ressourcengruppe, einem Azure-Abonnement oder einer einzelnen Azure Stack Edge-Ressource auftreten, auszulösen oder zu unterdrücken. Dieser Artikel bezieht sich auf alle Azure Stack Edge-Modelle.  

## <a name="about-action-rules"></a>Informationen zu Aktionsregeln

Mit einer Aktionsregel können Warnbenachrichtigungen ausgelöst oder unterdrückt werden. Die Aktionsregel wird einer *Aktionsgruppe* hinzugefügt. Dabei handelt es sich um eine Gruppe von Benachrichtigungseinstellungen, mit der Benutzer benachrichtigt werden, die auf Warnungen reagieren müssen, die in verschiedenen Kontexten für eine Ressource oder eine Gruppe von Ressourcen ausgelöst werden.

Weitere Informationen zu Aktionsregeln finden Sie unter [Konfigurieren einer Aktionsregel](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule). Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](/azure/azure-monitor/platform/action-groups).

> [!NOTE]
> Die Funktion für Aktionsregeln befindet sich in der Vorschauphase. Einige Bildschirme und Schritte können sich bei der Optimierung der Prozesse ändern.


## <a name="create-an-action-rule"></a>Erstellen einer Aktionsregel

Führen Sie die folgenden Schritte im Azure-Portal aus, um eine Aktionsregel für Ihr Azure Stack Edge-Gerät zu erstellen.

> [!NOTE]
> Mit diesen Schritten wird eine Aktionsregel erstellt, mit der Benachrichtigungen an eine Aktionsgruppe gesendet werden. Ausführliche Informationen zum Erstellen einer Aktionsregel zum Unterdrücken von Benachrichtigungen finden Sie unter [Konfigurieren einer Aktionsregel](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule).

1. Navigieren Sie im Azure-Portal zu dem Azure Stack Edge-Gerät und dann zu **Überwachung > Warnungen**. Wählen Sie **Aktionen verwalten** aus.

   ![Überwachen von Warnungen, Ansicht „Aktionen verwalten“](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Wählen Sie **Aktionsregeln (Vorschau)** und dann **+ Neue Aktionsregel** aus.

   ![Aktionen verwalten, Option „Aktionsregeln“](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. Wählen Sie auf dem Bildschirm **Aktionsregel erstellen** über die Option **Bereich** ein Azure-Abonnement, eine Ressourcengruppe oder eine Zielressource aus. Die Aktionsregel wirkt sich auf alle Warnungen aus, die in diesem Bereich generiert werden.

   1. Wählen Sie **Auswählen** nach **Bereich** aus.

      ![Auswählen eines Bereichs für eine neue Aktionsregel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Wählen Sie das **Abonnement** für die Aktionsregel aus. Optional können Sie nach einem **Ressourcentyp** filtern. Um Azure Stack Edge-Ressourcen zu filtern, wählen Sie **Data Box Edge-Geräte (dataBoxEdge)** aus.

      Im Bereich **Ressource** werden die verfügbaren Ressourcen basierend auf Ihrer Auswahl aufgelistet.
  
      ![Verfügbare Ressourcen auf dem Bildschirm „Bereich auswählen“](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Aktivieren Sie das Kontrollkästchen für jede Ressource, auf die die Regel angewandt werden soll. Sie können das Abonnement, Ressourcengruppen oder einzelne Ressourcen auswählen. Wählen Sie dann **Fertig** aus.

      ![Beispieleinstellungen für den Bereich einer Aktionsregel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      Auf dem Bildschirm **Aktionsregel erstellen** wird der ausgewählte Bereich angezeigt.

      ![Festgelegter Bereich für eine Azure Stack Edge-Aktionsregel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Verwenden Sie die Optionen unter **Filter**, um die Anwendung der Regel auf eine Teilmenge der Warnungen innerhalb des ausgewählten Bereichs einzugrenzen.

   1. Wählen Sie **Hinzufügen** aus, um den Bereich **Filter hinzufügen** zu öffnen.

      ![Option zum Hinzufügen von Filtern zu einer Aktionsregel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. Fügen Sie unter **Filter** alle anzuwendenden Filter hinzu. Wählen Sie für jeden Filter den Filtertyp, den **Operator** und den **Wert** aus.
   
      Eine Liste der Filteroptionen finden Sie unter [Filterkriterien](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#filter-criteria).

      Die folgenden Beispielfilter gelten für alle Warnungen mit dem Schweregrad 2, 3 und 4, die mit dem Monitor-Dienst für Azure Stack Edge-Ressourcen ausgelöst werden.

      Wenn Sie die gewünschten Filter hinzugefügt haben, wählen Sie **Fertig** aus.
   
      ![Beispielfilter für eine Aktionsregel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Wählen Sie auf dem Bildschirm **Aktionsregel erstellen** die Option **Aktionsgruppe** aus, um eine Regel zu erstellen, mit der Benachrichtigungen gesendet werden. Wählen Sie dann bei **Aktionen** die Schaltfläche **Auswählen** aus.

   ![Option „Aktionsgruppe“ zum Erstellen einer Aktionsregel zum Senden von Benachrichtigungen](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Zum Erstellen einer Regel zum Unterdrücken von Benachrichtigungen wählen Sie **Unterdrückung** aus. Weitere Informationen finden Sie unter [Konfigurieren einer Aktionsregel](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule).

6. Wählen Sie die Aktionsgruppe aus, die mit der Aktionsregel verwendet werden soll. Wählen Sie anschließend **Auswählen** aus. Die neue Aktionsregel wird den Benachrichtigungseinstellungen der ausgewählten Aktionsgruppe hinzugefügt.

   Wenn Sie eine neue Aktionsgruppe erstellen möchten, wählen Sie **+ Aktionsgruppe erstellen** aus, und führen Sie dann die unter [Erstellen einer Aktionsgruppe mit dem Azure-Portal](/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal) beschriebenen Schritte aus.

   ![Wählen Sie eine Aktionsgruppe, die mit der Regel verwendet werden soll, und dann „Auswählen“ aus.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Geben Sie für die neue Aktionsregel einen **Namen** und eine **Beschreibung** ein, und weisen Sie die Regel einer Ressourcengruppe zu.

9. Die neue Regel ist standardmäßig aktiviert. Wenn Sie die Regel nicht sofort verwenden möchten, wählen Sie für **Regel beim Erstellen aktivieren** die Option **Nein** aus.

10. Wählen Sie abschließend **Erstellen** aus.

    ![Ausgewählte Einstellungen für eine Aktionsregel, mit der Warnbenachrichtigungen gesendet werden](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Der Bildschirm **Aktionsregeln (Vorschau)** wird geöffnet, möglicherweise wird die neue Aktionsregel jedoch nicht sofort angezeigt. Der Fokus liegt auf **allen** Ressourcengruppen.

11. Wählen Sie zum Anzeigen der neuen Aktionsregel die Ressourcengruppe für die Regel aus.

    ![Bildschirm „Aktionsregeln“ mit angezeigter neuer Regel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Anzeigen von Benachrichtigungen

Benachrichtigungen werden ausgegeben, wenn ein neues Ereignis eine Warnung für eine Ressource auslöst, die sich innerhalb des Bereichs einer Aktionsregel befindet.

In der Aktionsgruppe für eine Regel ist der Empfänger einer Benachrichtigung und der Benachrichtigungstyp festgelegt: E-Mail und/oder Short Message Service (SMS).

Es kann einige Minuten dauern, bis nach dem Auslösen einer Warnung Benachrichtigungen empfangen werden.

Die E-Mail-Benachrichtigung sieht in etwa wie folgt aus.

![Beispiel-E-Mail-Benachrichtigung für eine Aktionsregel](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Nächste Schritte

<!-- - See [Create and manage action groups in the Azure portal](/azure/azure-monitor/platform/action-groups) for guidance on creating a new action group.
- See [Configure an action rule](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Informationen zum Überprüfen von Geräteereignissen, Hardwarestatus und Metrikdiagrammen finden Sie unter [Überwachen Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-monitor.md). 
- Informationen zum Optimieren von Azure Monitor für Azure Stack Edge Pro-GPU-Geräte finden Sie unter [Verwenden von Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md).
- Informationen zum Verwalten von einzelnen Warnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](/azure/azure-monitor/platform/alerts-metric).
