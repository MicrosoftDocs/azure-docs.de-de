---
title: Verwenden von Wiederherstellungsplänen bei der Notfallwiederherstellung mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Wiederherstellungspläne für die Notfallwiederherstellung mit dem Azure Site Recovery-Dienst verwenden.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: ea2399572177cc10006a5d9ee715190fff4a347b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471448"
---
# <a name="about-recovery-plans"></a>Informationen zu Wiederherstellungsplänen

Dieser Artikel beschreibt Wiederherstellungspläne in [Azure Site Recovery](site-recovery-overview.md).

Ein Wiederherstellungsplan fasst Computer in Wiederherstellungsgruppen zusammen. Sie können einen Plan anpassen, indem Sie ihn eine Reihenfolge, Anweisungen und Aufgaben hinzufügen. Nach dem Definieren eines Plans können Sie ein Failover für den Plan ausführen.



## <a name="why-use-a-recovery-plan"></a>Gründe für die Verwendung eines Wiederherstellungsplans

Mit einem Wiederherstellungsplan können Sie einen systematischen Wiederherstellungsprozess definieren, indem Sie kleine unabhängige Einheiten erstellen, für die Sie ein Failover ausführen können. Eine Einheit stellt normalerweise eine App in Ihrer Umgebung dar. Ein Wiederherstellungsplan definiert, wie ein Failover für Computer ausgeführt wird, und gibt die Reihenfolge an, in der sie nach dem Failover gestartet werden. Verwenden Sie Wiederherstellungspläne zu folgenden Zwecken:

* Modellieren einer App entsprechend ihrer Abhängigkeiten
* Automatisieren von Wiederherstellungsaufgaben zur Verringerung des RTO-Werts
* Überprüfen, dass Sie für die Migration oder Notfallwiederherstellung vorbereitet sind, indem Sie sicherstellen, dass Ihre Apps Teil eines Wiederherstellungsplans sind
* Ausführen eines Testfailovers für Wiederherstellungspläne, um sicherzustellen, dass die Notfallwiederherstellung oder Migration wie erwartet funktioniert


## <a name="model-apps"></a>Modellieren von Apps

Sie können eine Wiederherstellungsgruppe zum Erfassen App-spezifischer Eigenschaften planen und erstellen. Als Beispiel dient hier eine typische dreischichtige Anwendung mit einem SQL Server-Back-End, Middleware und einem Web-Front-End. Normalerweise passen Sie den Wiederherstellungsplan so an, dass Computer in jeder Schicht nach dem Failover in der richtigen Reihenfolge gestartet werden.

- Das SQL-Back-End sollte zuerst gestartet werden, dann die Middleware und zum Schluss das Web-Front-End.
- Mit dieser Startreihenfolge wird sichergestellt, dass die App funktioniert, nachdem der letzte Computer gestartet wurde.
- Mit dieser Reihenfolge wird sichergestellt, dass zu dem Zeitpunkt, zu dem die Middleware gestartet wird und versucht, eine Verbindung mit der SQL Server-Schicht herzustellen, die SQL Server-Schicht bereits ausgeführt wird. 
- Mit dieser Reihenfolge kann außerdem sichergestellt werden, dass der Front-End-Server zuletzt gestartet wird, sodass Endbenutzer keine Verbindung mit der App-URL herstellen, bevor alle Komponenten ausgeführt werden und die App für die Annahme von Anforderungen bereit ist.

Um diese Reihenfolge zu erstellen, fügen Sie Gruppen zur Wiederherstellungsgruppe und Computer in den Gruppen hinzu.
- Wo die Reihenfolge angegeben ist, wird eine Sequenzierung verwendet. Aktionen werden ggf. parallel ausgeführt, um den RTO-Wert der Anwendungswiederherstellung zu verbessern.
- Für Computer in einer einzelnen Gruppe wird das Failover parallel ausgeführt.
- Für Computer in unterschiedlichen Gruppen wird das Failover in Gruppenreihenfolge ausgeführt, damit das Failover für Computer der Gruppe 2 nur gestartet wird, nachdem das Failover für alle Computer in Gruppe 1 ausgeführt wurde und diese gestartet sind.

    ![Beispiel eines Wiederherstellungsplans](./media/recovery-plan-overview/rp.png)

Mit dieser Anpassung geschieht Folgendes beim Ausführen eines Failovers für den Wiederherstellungsplan: 

1. Ein Schritt zum Herunterfahren versucht, die lokalen Computer zu deaktivieren. Eine Ausnahme ist das Ausführen eines Testfailovers. In diesem Fall wird der primäre Standort weiterhin ausgeführt. 
2. Das Herunterfahren löst ein paralleles Failover aller Computer im Wiederherstellungsplan aus.
3. Beim Failover werden die Datenträger der virtuellen Computer mit replizierten Daten vorbereitet.
4. Die Startgruppen werden in Reihenfolge ausgeführt und starten die Computer in jeder Gruppe. Zuerst wird Gruppe 1 ausgeführt, dann Gruppe 2 und zum Schluss Gruppe 3. Wenn mehrere Computer in einer Gruppe enthalten sind, werden alle Computer parallel gestartet.


## <a name="automate-tasks"></a>Automatisieren von Aufgaben

Das Wiederherstellen von großen Anwendungen kann eine komplexe Aufgabe sein. Manuelle Schritte machen den Vorgang fehleranfällig, und die Person, die das Failover ausführt, ist sich möglicherweise der Komplexität der App nicht bewusst. Sie können mit einem Wiederherstellungsplan die Reihenfolge vorgeben und mithilfe von Azure Automation-Runbooks für Failover in Azure oder Skripts die in jedem Schritt erforderlichen Aktionen automatisieren. Für Aufgaben, die nicht automatisiert werden können, können Sie Pausen für manuelle Aktionen in die Wiederherstellungspläne einfügen. Sie können verschiedene Aufgabentypen konfigurieren:

* **Aufgaben für die Azure-VM nach dem Failover**: Wenn Sie ein Failover zu Azure ausführen, müssen Sie normalerweise Aktionen ausführen, damit Sie nach dem Failover eine Verbindung mit der VM herstellen können. Beispiel: 
    * Erstellen einer öffentlichen IP-Adresse auf dem virtuellen Azure-Computer
    * Zuweisen einer Netzwerksicherheitsgruppe zum Netzwerkadapter des virtuellen Azure-Computers
    * Hinzufügen eines Lastenausgleichs zu einer Verfügbarkeitsgruppe.
* **Aufgaben innerhalb der VM nach dem Failover**: Mit diesen Aufgaben wird in der Regel die auf dem Computer ausgeführte App neu konfiguriert, damit sie in der neuen Umgebung weiterhin ordnungsgemäß funktioniert. Beispiel:
    * Ändern der Datenbank-Verbindungszeichenfolge innerhalb des Computers
    * Ändern der Webserverkonfiguration oder -regeln.


## <a name="test-failover"></a>Testfailover

Sie können einen Wiederherstellungsplan verwenden, um ein Testfailover auszulösen. Verwenden Sie die folgenden bewährten Methoden:

- Führen Sie für eine App immer ein Testfailover aus, bevor Sie ein vollständiges Failover ausführen. Mithilfe des Testfailovers können Sie überprüfen, ob die App am Wiederherstellungsstandort gestartet wird.
- Wenn Sie feststellen, dass Sie etwas übersehen haben, lösen Sie eine Bereinigung aus, und führen Sie das Testfailover dann erneut aus. 
- Führen Sie ein Testfailover mehrere Male aus, bis Sie sicher sind, dass die App reibungslos wiederhergestellt wird.
- Da jede App anders ist, sollten Sie Wiederherstellungspläne erstellen, die für jede Anwendung entsprechend angepasst sind, und jeweils ein Testfailover ausführen.
- Apps und ihre Abhängigkeiten ändern sich häufig. Um sicherzustellen, dass die Wiederherstellungspläne auf dem neuesten Stand sind, führen Sie vierteljährlich ein Testfailover für jede App aus.

    ![Screenshot eines Beispiel-Testwiederherstellungsplans in Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Video ansehen

Sehen Sie sich ein kurzes Beispielvideo eines mit einem Klick ausgelösten Failovers für eine zweischichtige WordPress-App an.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Nächste Schritte

- [Erstellen](site-recovery-create-recovery-plans.md) Sie einen Wiederherstellungsplan.
- Erfahren Sie mehr über das [Ausführen von Failovern](site-recovery-failover.md).  
