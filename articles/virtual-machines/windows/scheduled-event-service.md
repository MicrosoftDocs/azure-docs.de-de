---
title: Überwachen von geplanten Ereignissen für Windows-VMs in Azure
description: Erfahren Sie, wie Sie Ihre virtuellen Azure-Computer für geplante Ereignisse überwachen.
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 0d1edde5ac1b83feab458eb5d12d524163d3ffb1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483299"
---
# <a name="monitoring-scheduled-events"></a>Überwachen von geplanten Ereignissen

Updates werden täglich auf verschiedene Teile von Azure angewendet, damit die Dienste, die auf ihnen ausgeführt werden, sicher und auf dem neuesten Stand bleiben. Zusätzlich zu geplanten Updates können auch ungeplante Ereignisse auftreten. Wenn beispielsweise eine Hardwarebeeinträchtigung oder ein Hardwarefehler erkannt wird, müssen die Azure-Dienste möglicherweise nicht geplante Wartung ausführen. Durch Verwenden von Livemigration und speichererhaltende Updates sowie grundsätzliches Beachten einer strengen Begrenzung der Auswirkungen von Updates sind diese Ereignisse in den meisten Fällen für Kunden weitestgehend transparent, und die Ereignisse haben entweder keine Auswirkung oder verursachen höchstens für einige Sekunden ein Einfrieren des virtuellen Computers. Es gibt jedoch Anwendungen, bei denen sich bereits wenige Sekunden eines Einfrierens des virtuellen Computers negativ auswirken. Es ist wichtig, im Voraus über eine bevorstehende Azure-Wartung informiert zu sein, um das beste Betriebsverhalten für solche Anwendungen sicherzustellen. Der [Scheduled Events-Dienst](scheduled-events.md) stellt Ihnen eine befehlsorientierte Benutzerschnittstelle zur Verfügung, um über bevorstehende Wartung benachrichtigt zu werden, und ermöglicht es Ihnen, die Wartung reibungslos abzuwickeln. 

In diesem Artikel erfahren Sie, wie Sie geplante Ereignisse verwenden können, um über Wartungsereignisse benachrichtigt zu werden, die sich auf ihre virtuellen Computer auswirken könnten, und wie Sie eine grundlegende Automatisierung erstellen können, die bei der Überwachung und Analyse unterstützen kann.


## <a name="routing-scheduled-events-to-log-analytics"></a>Weiterleiten von geplanten Ereignissen an Log Analytics

Scheduled Events ist als Teil des [Azure Instance Metadata Service](instance-metadata-service.md) verfügbar, der auf jedem virtuellen Azure-Computer verfügbar ist. Kunden können eine Automatisierung schreiben, um den Endpunkt ihrer virtuellen Computer abzufragen, nach Benachrichtigungen über geplante Wartung zu suchen sowie Risikominderungen durchzuführen, etwa Speichern des Zustands und Außerbetriebnahme des virtuellen Computers. Es empfiehlt sich, Automatisierung zu erstellen, um geplante Ereignisse aufzuzeichnen, damit Sie ein Überwachungsprotokoll zu Azure-Wartungsereignissen erhalten. 

In diesem Artikel sind die Schritte erläutert, mit denen Sie das Erfassen von geplanten Wartungsereignissen in Log Analytics einrichten. Anschließend werden einige einfache Benachrichtigungsaktionen ausgelöst, z. B. Senden einer E-Mail an Ihr Team und Abrufen einer Verlaufsansicht aller Ereignisse, die sich auf Ihre virtuellen Computer ausgewirkt haben. Für die Ereignisaggregation und -automatisierung wird [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) verwendet, Sie können jedoch jede beliebige Überwachungslösung verwenden, um diese Protokolle zu erfassen und Automatisierung auszulösen.

![Diagramm, in dem der Ereignislebenszyklus dargestellt ist](./media/notifications/events.png)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel müssen Sie einen [virtuellen Windows-Computer in einer Verfügbarkeitsgruppe](tutorial-availability-sets.md) erstellen. Scheduled Events stellt Benachrichtigungen über Änderungen bereit, die sich auf jeden der virtuellen Computer in ihrer Verfügbarkeitsgruppe, in Ihrem Clouddienst oder in Ihrer VM-Skalierungsgruppe oder auf eigenständige virtuelle Computer auswirken können. Es wird ein [Dienst](https://github.com/microsoft/AzureScheduledEventsService) ausgeführt, der ständig auf geplante Ereignisse auf einem der virtuellen Computer abfragt, der als Collector fungiert, um Ereignisse für alle anderen virtuellen Computer in der Verfügbarkeitsgruppe abzurufen.    

Löschen Sie die Ressourcengruppe nicht am Ende des Tutorials.

Außerdem müssen Sie einen [Log Analytics-Arbeitsbereich](../../azure-monitor/learn/quick-create-workspace.md) erstellen, der dazu verwendet wird, die Informationen von den virtuellen Computern der Verfügbarkeitsgruppe zu aggregieren.

## <a name="set-up-the-environment"></a>Einrichten der Umgebung

Sie sollten jetzt zwei erste virtuelle Computer in einer Verfügbarkeitsgruppe haben. Nun muss in derselben Verfügbarkeitsgruppe ein dritter virtueller Computer erstellt werden, der den Namen „myCollectorVM“ hat. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Laden Sie die Installations-ZIP-Datei des Projekts von [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip) herunter.

Stellen Sie eine Verbindung mit **myCollectorVM** her, kopieren Sie die ZIP-Datei auf den virtuellen Computer, und extrahieren Sie alle Dateien. Öffnen Sie auf dem virtuellen Computer eine PowerShell-Eingabeaufforderung. Wechseln Sie mit der Eingabeaufforderung in den Ordner, der `SchService.ps1` enthält (beispielsweise `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`), und richten Sie den Dienst ein.

```powershell
.\SchService.ps1 -Setup
```

Starten Sie den Dienst.

```powershell
.\SchService.ps1 -Start
```

Der Dienst fragt nun alle 10 Sekunden auf alle geplanten Ereignisse ab und genehmigt die Ereignisse, um die Wartung zu beschleunigen.  Einfrieren (Freeze), Neustarten (Reboot), erneutes Bereitstellen (Redeploy) und Preempt sind die Ereignisse, die von Scheduled Events erfasst werden.   Sie können das Skript so erweitern, dass vor der Genehmigung des jeweiligen Ereignisses einige Risikominderungen auslöst werden.

Überprüfen Sie den Dienststatus, und vergewissern Sie sich, dass er „Running“ lautet.

```powershell
.\SchService.ps1 -status  
```

Dieser Befehl sollte `Running` zurückgegeben.

Der Dienst fragt nun alle 10 Sekunden auf alle geplanten Ereignisse ab und genehmigt die Ereignisse, um die Wartung zu beschleunigen.  Einfrieren (Freeze), Neustarten (Reboot), erneutes Bereitstellen (Redeploy) und Preempt sind die Ereignisse, die von Scheduled Events erfasst werden. Sie können das Skript so erweitern, dass vor der Genehmigung des jeweiligen Ereignisses einige Risikominderungen auslöst werden.

Wird eines der oben aufgeführte Ereignisse vom Scheduled Events-Dienst erfasst, wird es im Anwendungsereignisprotokoll in den Feldern für Ereignisstatus (EventStatus), Ereignistyp (EventType), Ressourcen (Resources, Name des virtuellen Computers) und Ausführungsdatum (NotBefore, Mindestbenachrichtigungsperiode) protokolliert. Sie können nach den Ereignissen mit der ID 1234 im Anwendungsereignisprotokoll suchen.

Nachdem der Dienst eingerichtet und gestartet wurde, protokolliert er Ereignisse in den Windows-Anwendungsprotokollen.   Um dies zu überprüfen, starten Sie einen der virtuellen Computer in der Verfügbarkeitsgruppe neu. Danach sollten Sie in der Ereignisanzeige in „Windows-Protokolle“ > Protokoll „Anwendung“ ein protokolliertes Ereignis sehen, dem zu entnehmen ist, dass der virtuelle Computer neu gestartet wurde. 

![Screenshot der Ereignisanzeige](./media/notifications/event-viewer.png)

Werden Ereignisse vom Scheduled Events-Dienst erfasst, werden sie im Anwendungsereignisprotokoll in den Feldern für Ereignisstatus (EventStatus), Ereignistyp (EventType), Ressourcen (Resources, Name des virtuellen Computers) und Ausführungsdatum (NotBefore, Mindestbenachrichtigungsperiode) protokolliert. Sie können nach den Ereignissen mit der ID 1234 im Anwendungsereignisprotokoll suchen.

> [!NOTE] 
> In diesem Beispiel befinden sich die virtuellen Computer in einer Verfügbarkeitsgruppe, wodurch es möglich ist, einen einzelnen virtuellen Computer als Collector festzulegen, der auf geplante Ereignisse lauscht und diese an den Log Analytics-Arbeitsbereich weiterleitet. Wenn Sie eigenständige virtuelle Computer haben, können Sie den Dienst auf jedem virtuellen Computer ausführen und diese dann einzeln mit Ihrem Log Analytics-Arbeitsbereich verbinden.
>
> Für die vorliegende Einrichtung wurde Windows ausgewählt, Sie können aber eine ähnliche Lösung unter Linux entwickeln.

Zu jedem Zeitpunkt können Sie den Scheduled Events-Dienst anhalten oder entfernen, indem Sie den Parameter `–stop` oder `–remove` verwenden.

## <a name="connect-to-the-workspace"></a>Herstellen einer Verbindung mit dem Arbeitsbereich


Es soll nun ein Log Analytics Arbeitsbereich mit dem virtuellen Collector-Computer verbunden werden. Der Log Analytics-Arbeitsbereich fungiert als Repository, und die Ereignisprotokollsammlung wird so konfiguriert, dass sie die Anwendungsprotokolle virtuellen Collector-Computer erfasst. 

 Um die geplanten Ereignisse an das Ereignisprotokoll weiterzuleiten, das vom Dienst als Anwendungsprotokoll gespeichert wird, müssen Sie Ihren virtuellen Computer mit Ihrem Log Analytics Arbeitsbereich verbinden.  
 
1. Öffnen Sie die Seite für den Arbeitsbereich, den Sie erstellt haben.
1. Wählen Sie unter **Mit einer Datenquelle verbinden** die Option **Virtuelle Azure-Computer** aus.

    ![Herstellen einer Verbindung mit einem virtuellen Computer als Datenquelle](./media/notifications/connect-to-data-source.png)

1. Suchen Sie nach **myCollectorVM**, und wählen Sie diesen Eintrag aus. 
1. Wählen Sie auf der Seite für **myCollectorVM** die Option **Verbinden** aus.

Hiermit wird der [Microsoft Monitoring Agent](../extensions/oms-windows.md) auf Ihrem virtuellen Computer installiert. Es dauert einige Minuten, bis Ihr virtueller Computer mit dem Arbeitsbereich verbunden und die Erweiterung installiert ist. 

## <a name="configure-the-workspace"></a>Konfigurieren des Arbeitsbereichs

1. Öffnen Sie die Seite für Ihren Arbeitsbereich, und wählen Sie **Erweiterte Einstellungen** aus.
1. Wählen Sie **Daten** im linken Menü aus, und wählen Sie dann **Windows-Ereignisprotokolle** aus.
1. Beginnen Sie in **Erfassen Sie Ereignisse aus den folgenden Ereignisprotokollen** mit dem Eingeben von *application*, und wählen Sie dann **Application** in der Liste aus.

    ![Erweiterte Einstellungen auswählen](./media/notifications/advanced.png)

1. Belassen Sie **FEHLER**, **WARNUNG** und **INFORMATION** ausgewählt, und wählen Sie dann **Speichern** aus, um die Einstellungen zu speichern.


> [!NOTE]
> Es tritt eine Verzögerung auf, und es kann bis zu 10 Minuten dauern, bis das Protokoll verfügbar ist. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Erstellen einer Warnungsregel mit Azure Monitor 


Sobald die Ereignisse an Log Analytics übermittelt wurden, können Sie die folgende [Abfrage](../../azure-monitor/log-query/log-analytics-tutorial.md) ausführen, um nach den geplanten Ereignissen zu suchen.

1. Wählen Sie oben auf der Seite die Option **Protokolle** aus, und fügen Sie Folgendes in das Textfeld ein:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Wählen Sie **Speichern** aus, geben Sie dann *logQuery* für den Namen ein, belassen Sie **Abfrage** als Typ, geben Sie *VMLogs* als **Kategorie** ein, und wählen Sie dann **Speichern** aus. 

    ![Speichern der Abfrage](./media/notifications/save-query.png)

1. Wählen Sie **Neue Warnungsregel** aus. 
1. Belassen Sie auf der Seite **Regel erstellen** den Eintrag `collectorworkspace` als Wert für **Ressource**.
1. Wählen Sie unter **Bedingung** den Eintrag *Whenever the customer log search is <login undefined>* aus. Die Seite **Signallogik konfigurieren** wird geöffnet.
1. Geben Sie unter **Schwellenwert** den Wert *0* ein, und wählen Sie dann **Fertig** aus.
1. Wählen Sie unter **Aktionen** die Option **Aktionsgruppe erstellen** aus. Die Seite **Aktionsgruppe hinzufügen** wird geöffnet.
1. Geben Sie in **Name der Aktionsgruppe** den Namen *myActionGroup* ein.
1. Geben Sie in **Kurzname** den Namen **myActionGroup** ein.
1. Wählen Sie unter **Ressourcengruppe** den Eintrag **myResourceGroupAvailability** aus.
1. Geben Sie in **AKTIONSNAME** den Namen **E-Mail** ein, und wählen Sie dann **E-Mail/SMS/Push/Sprache** aus. Die Seite **E-Mail/SMS/Push/Sprache** wird geöffnet.
1. Wählen Sie **E-Mail** aus, geben Sie Ihre E-Mail-Adresse ein, und wählen Sie dann **OK** aus.
1. Wählen Sie auf der Seite **Aktionsgruppe hinzufügen** die Option **OK** aus. 
1. Geben Sie auf der Seite **Regel erstellen** unter **WARNUNGSDETAILS** den Namen *myAlert* für **Name der Warnungsregel** ein, und geben Sie dann *Email alert rule* für **Beschreibung** ein.
1. Wählen Sie abschließend **Warnungsregel erstellen** aus.
1. Starten Sie einen der virtuellen Computer in der Verfügbarkeitsgruppe neu. Innerhalb weniger Minuten sollten Sie eine E-Mail erhalten, in der mitgeteilt wird, dass die Warnung ausgelöst wurde.

Um Ihre Warnungsregeln zu verwalten, wechseln Sie zur Ressourcengruppe, wählen Sie **Warnungen** im linken Menü aus, und wählen Sie dann **Warnungsregeln verwalten** oben auf der Seite aus.

     
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie auf der Seite [Azure Scheduled Events Service](https://github.com/microsoft/AzureScheduledEventsService) auf GitHub.