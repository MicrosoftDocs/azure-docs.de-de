---
title: Überwachen der Integrität von virtuellen Computern mit Azure Monitor für VMs (Vorschauversion) | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Integrität der VM und des zugrundeliegenden Betriebssystems mithilfe von Azure Monitor for VMs beurteilen können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 9fa76c9637a6dcdca48bf45e8ee2aa9305a4f64f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130451"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Grundlegendes zur Integrität Ihrer Azure-VMs

Azure enthält mehrere Dienste, die einzeln eine bestimmte Rolle oder Aufgabe im Überwachungsbereich ausführen, aber bisher keinen tiefgreifenden Blick auf die Integrität des auf Azure-VMs gehosteten Betriebssystems geboten haben. Zwar konnten Sie mithilfe von Azure Monitor verschiedene Bedingungen überwachen, die Azure Monitor-Plattform war jedoch nicht darauf ausgelegt, die Integrität von Kernkomponenten oder die Gesamtintegrität der VM zu modellieren und darzustellen. Das Integritätsfeature von Azure Monitor for VMs überwacht proaktiv die Verfügbarkeit und Leistung des Windows- oder Linux-Gastbetriebssystems mit einem Modell, das Schlüsselkomponenten und ihre Beziehungen modelliert, und mit Kriterien, die angeben, wie die Integrität dieser Komponenten gemessen wird, und eine Warnung an Sie auslösen, wenn ein Zustand eingeschränkter Integrität erkannt wird.  

Die Anzeige des Integritätsgesamtstatus von Azure-VMs und des zugrundeliegenden Betriebssystems mit dem Azure Monitor for VMs kann aus zwei verschiedenen Perspektiven erfolgen, einmal direkt in einer VM oder übergreifend über alle in einer Ressourcengruppe enthaltenen VMs mithilfe von Azure Monitor.

Dieser Artikel soll Ihr Verständnis dafür schärfen, wie Sie erkannte Integritätsprobleme schnell bewerten, untersuchen und beheben.

Informationen zum Konfigurieren von Azure Monitor for VMs finden Sie unter [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Aktivieren von Azure Monitor for VMs).

## <a name="monitoring-configuration-details"></a>Details der Überwachungskonfiguration

Dieser Abschnitt beschreibt im Umriss die definierten standardmäßigen Integritätskriterien für die Überwachung von Azure Windows- und Linux-VMs. Alle Integritätskriterien sind so vorkonfiguriert, dass sie beim Eintreten der Fehlerbedingung eine Warnung ausgeben. 

### <a name="windows-vms"></a>Virtuelle Windows-Computer

- Verfügbare Megabyte Arbeitsspeicher 
- Datenträger – mittlere Dauer pro Schreibvorgang (logischer Datenträger)
- Datenträger – mittlere Dauer pro Schreibvorgang (Datenträger)
- Logischer Datenträger – mittlere Dauer pro Lesevorgang
- Logischer Datenträger – mittlere Dauer pro Übertragung
- Datenträger – mittlere Dauer pro Lesevorgang
- Datenträger – mittlere Dauer pro Übertragung
- Aktuelle Länge der Datenträgerwarteschlange (Logischer Datenträger)
- Aktuelle Länge der Datenträgerwarteschlange (Datenträger)
- Datenträger – Leerlaufzeit (%)
- Fehler oder Beschädigung im Dateisystem
- Logischer Datenträger – wenig Speicherplatz (%)
- Logischer Datenträger – wenig Speicherplatz (MB)
- Logischer Datenträger – Leerlaufzeit (%)
- Speicherseiten pro Sekunde
- Genutzte Bandbreite (%) beim Lesen
- Genutzte Bandbreite (%) gesamt
- Genutzte Bandbreite (%) beim Schreiben
- Verwendeter Prozentsatz des zugesicherten Speichers
- Datenträger – Leerlaufzeit (%)
- Integrität des DHCP-Clientdiensts
- Integrität des DNS-Clientdiensts
- Integrität des RPC-Diensts
- Integrität des Serverdiensts
- CPU-Auslastung (%) gesamt
- Integrität des Windows-Ereignisprotokolldiensts
- Integrität des Windows-Firewalldiensts
- Integrität des Windows-Remoteverwaltungsdiensts

### <a name="linux-vms"></a>Virtuelle Linux-Computer
- Datenträger Durchschn. Datenträger s/übertragen 
- Datenträger Durchschn. Datenträger s/gelesen 
- Datenträger Durchschn. Datenträger s/geschrieben 
- Datenträgerintegrität
- Logischer Datenträger – verfügbarer Speicherplatz
- Logischer Datenträger – verfügbarer Speicherplatz (%)
- Logischer Datenträger – freie I-Knoten (%)
- Netzwerkadapterintegrität
- Prozessor – Prozessorzeit gesamt
- Betriebssystem – verfügbare Megabyte Arbeitsspeicher

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

## <a name="introduction-to-health-experience"></a>Einführung in die Integritätsoberfläche

Bevor wir in die Verwendung des Integritätsfeatures für eine einzelne VM oder eine Gruppe von VMs einsteigen, müssen wir Ihnen eine kurze Einführung geben, damit Sie verstehen, wie die Informationen dargestellt werden und wofür die Visualisierungen stehen.  

### <a name="view-health-directly-from-a-virtual-machine"></a>Direktes Anzeigen der Integrität in einer VM 

Um die Integrität einer Azure-VM anzuzeigen, wählen Sie im linken Bereich der VM **Insights (Vorschau)** aus. Auf der Insights-Seite der VM ist standardmäßig **Integrität** geöffnet und zeigt die Integritätsansicht der VM an.  

![Azure Monitor for VMs-Integritätsübersicht einer ausgewählten Azure-VM](./media/vminsights-health/vminsights-directvm-health.png)

Auf der Registerkarte **Integrität** zeigt die Tabelle unter dem Abschnitt **Integrität der Gast-VM** den aktuellen Integritätsstatus Ihrer VM und die Gesamtzahl der VM-Integritätswarnungen an, die von einer Komponente mit eingeschränkter Integrität ausgelöst wurden. Wenn Sie zusätzliche Einzelheiten zur Warnungsoberfläche erfahren möchten, lesen Sie [Warnungen](#alerts).  

Die für eine VM definierten Integritätszustände sind in der folgenden Tabelle beschrieben: 

|Symbol |Integritätsstatus |Bedeutung |
|-----|-------------|---------------|
| |Healthy |Der Integritätszustand lautet „Fehlerfrei“, wenn er innerhalb des Bereichs der definierten Integritätsbedingungen liegt, für den virtuellen Computer keine Probleme erkannt werden und dieser wie erwartet ausgeführt wird. Bei einem übergeordneten Rollupmonitor wird ein Rollup der Integrität durchgeführt, das den Status des untergeordneten Elements im günstigsten und ungünstigsten Fall wiedergibt.|
| |Kritisch |Der Integritätszustand lautet „Kritisch“, wenn er nicht innerhalb des Bereichs der definierten Integritätsbedingung liegt und ein oder mehrere kritische Probleme erkannt wurden, die behoben werden müssen, um die normale Funktionsweise wiederherzustellen. Bei einem übergeordneten Rollupmonitor wird ein Rollup der Integrität durchgeführt, das den Status des untergeordneten Elements im günstigsten und ungünstigsten Fall wiedergibt.|
| |Warnung |Der Integritätszustand lautet „Warnung“, wenn er zwischen zwei Schwellenwerten für die definierte Integritätsbedingung liegt, wobei ein Wert den Zustand *Warnung* und der andere Wert den Zustand *kritisch* angibt (drei Schwellenwerte für den Integritätszustand können konfiguriert werden), oder wenn ein nicht kritisches Problem erkannt wird, das zu kritischen Problemen führen kann, wenn es nicht behoben wird. Bei einem übergeordneten Rollupmonitor gibt das übergeordnete Element den Status *Warnung* wieder, wenn mindestens eines der untergeordneten Elemente den Status „Warnung“ aufweist. Wenn ein untergeordnetes Element vorhanden ist, das den Status *Kritisch* und zugleich ein weiteres untergeordnetes Element den Status *Warnung* aufweist, weist das übergeordnete Rollup den Integritätsstatus *Kritisch* aus.|
| |Unknown |Der Integritätszustand ist *Unbekannt*, falls er aus verschiedenen Gründen nicht berechnet werden kann. Fußnote <sup>1</sup> enthält weitere Details und Lösungsmöglichkeiten. |

<sup>1</sup> Der Integritätszustand „Unbekannt“ wird durch die folgenden Probleme verursacht:

- Der Agent wurde neu konfiguriert und sendet keine Berichtsdaten mehr an den Arbeitsbereich, der beim Aktivieren von Azure Monitor für VMs angegeben wurde. Informationen zur Konfiguration des Agents zum Senden von Berichten an den Arbeitsbereich finden Sie unter [Hinzufügen oder Entfernen von Arbeitsbereichen](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Die VM wurde gelöscht.
- Der Arbeitsbereich, der Azure Monitor für VMs zugeordnet ist, wurde gelöscht. Wenn Sie über Premier Support-Vorteile verfügen, können Sie zum Wiederherstellen des Arbeitsbereichs unter [Premier](https://premier.microsoft.com/) eine Supportanfrage erstellen.
- Die Abhängigkeiten der Lösung wurden gelöscht. Um die Lösungen ServiceMap und InfrastructureInsights in Ihrem Log Analytics-Arbeitsbereich wieder zu aktivieren, können Sie für die erneute Installation eine bereitgestellte [Azure Resource Manager-Vorlage](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions) oder auf der Registerkarte „Erste Schritte“ die Option „Arbeitsbereich konfigurieren“ verwenden.
- Die VM wurde beendet.
- Der Azure-VM-Dienst ist nicht verfügbar, oder es wird gerade eine Wartung durchgeführt.
- Für den Arbeitsbereich wurde das [Tages- bzw. Speicherlimit für Daten](../platform/manage-cost-storage.md) erreicht.

Durch Auswählen von **Integritätsdiagnose anzeigen** wird eine Seite geöffnet, auf der alle Komponenten der VM mit den ihnen zugeordneten Integritätskriterien, Statusänderungen und sonstigen wichtigen Problemen dargestellt sind, die durch Überwachung der mit der VM zusammenhängenden Komponenten gefunden wurden. Weitere Informationen finden Sie unter [Integritätsdiagnose](#health-diagnostics). 

Die Tabelle unter dem Abschnitt **Integrität der Komponente** zeigt einen Integritätsrollupstatus der wichtigsten Leistungskategorien, die anhand von Integritätskriterien für die betreffenden Bereiche überwacht werden, im Einzelnen **CPU**, **Arbeitsspeicher**, **Datenträger** und **Netzwerk**.  Durch Auswählen einer der Komponenten wird eine Seite geöffnet, auf der alle Integritätsaspekte für die einzelnen Integritätskriterien der betreffenden Komponente mit dem jeweiligen Integritätsstatus aufgelistet sind.  

Beim Zugriff auf „Integrität“ on einer Azure-VM, die das Betriebssystem Windows ausführt, wird der Integritätsstatus der fünf wichtigsten Windows-Kerndienste unter **Integrität der Kerndienste** angezeigt.  Durch Auswählen eines der Dienste wird eine Seite geöffnet, auf der die Integritätskriterien zur Überwachung der betreffenden Komponente und ihr Integritätsstatus aufgelistet werden.  Ein Klick auf den Namen eines Integritätskriteriums öffnet dessen Eigenschaftenseite, auf der Sie die Konfigurationsdetails überprüfen können, darunter auch, ob für das Integritätskriterium eine entsprechende Azure Monitor-Warnung definiert ist. Weitere Informationen finden Sie unter [Integritätsdiagnose](#health-diagnostics) im Abschnitt zur Verwendung von Integritätskriterien.  

### <a name="aggregate-virtual-machine-perspective"></a>Aggregieren der VM-Perspektive

Um die Integritätssammlung aller Ihrer virtuellen Computer in einer Ressourcengruppe anzuzeigen, wählen Sie in der Navigationsliste im Portal **Azure Monitor** und dann **Virtual Machines (Vorschau)** aus.  

![VM Insights-Überwachungsansicht in Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Wählen Sie in den Dropdownlisten **Abonnement** und **Ressourcengruppe** die passende Ressourcengruppe aus, die die zur Gruppe gehörenden VMs enthält, um ihren gemeldeten Integritätszustand anzuzeigen.  Ihre Auswahl gilt nur für das Integritätsfeature und nicht für die Leistung oder die Karte.

Die Registerkarte **Integrität** stellt die folgenden Informationen dar:

* Wie viele VMs sich in einem kritischen oder fehlerhaften Zustand im Vergleich zur Anzahl der VMs in fehlerfreiem Zustand oder zu denjenigen befinden, die keine Daten übermitteln (was als unbekannter Zustand bezeichnet wird)
* Welche und wie viele VMs – nach Betriebssystem dargestellt – einen fehlerhaften Zustand melden
* Wie viele VMs aufgrund eines bei einem Prozessor, einem Datenträger, beim Arbeitsspeicher oder bei einem Netzwerkadapter erkannten Problems fehlerhaft sind, nach Integritätsstatus kategorisiert  
* Wie viele VMs aufgrund eines bei einem Kerndienst des Betriebssystems erkannten Problems fehlerhaft sind, nach Integritätsstatus kategorisiert

Hier können Sie die kritischsten der erkannten Probleme anhand der Integritätskriterien, nach denen die VMs proaktiv überwacht werden, schnell identifizieren und die Details der VM-Integritätswarnungen zusammen mit zugeordneten Wissensartikeln durcharbeiten, die Sie bei der Diagnose und Korrektur der Probleme unterstützen.  Klicken Sie auf einen der Schweregrade, um die Seite [Alle Warnungen](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) gefiltert nach diesem Schweregrad zu öffnen.

Die Liste **VM-Verteilung nach Betriebssystem** zeigt die VMs nach Windows-Edition oder Linux-Distribution zusammen mit den jeweiligen Versionen an. In jeder Betriebssystemkategorie sind die VMs basierend auf der Integrität weiter aufgeschlüsselt. 

![Distributionsperspektive von virtuellen Maschinen in VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Sie können auf ein beliebiges Spaltenelement klicken – **VM-Anzahl**, **Kritisch**, **Warnung**, **Fehlerfrei** oder **Unbekannt** –, um auf der Seite **Virtual Machines** eine Liste der gefilterten Ergebnisse passend zur ausgewählten Spalte anzuzeigen. Wenn Sie beispielsweise alle VMs überprüfen möchten, die **Red Hat Enterprise Linux, Version 7.5** ausführen, klicken Sie auf den Wert von **VM-Anzahl** für dieses Betriebssystem. Dann wird die folgende Seite geöffnet, auf der die VMs, die diesem Filterkriterium entsprechen, mit ihrem aktuell bekannten Integritätszustand aufgelistet sind.  

![Beispielrollup von Red Hat Linux-VMs](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Wenn Sie auf der Seite **Virtual Machines** in der Spalte **VM-Name** den Namen einer VM auswählen, werden Sie zur Seite der VM-Instanz geführt, die weitere Details zu den Warnungen und erkannten Problemen bei Integritätskriterien enthält, die die ausgewählte VM betreffen.  Hier können Sie die Details zum Integritätsstatus filtern, indem Sie auf das Symbol **Integritätsstatus** in der oberen linken Ecke der Seite klicken, um zu sehen, welche Komponenten fehlerhaft sind. Alternativ können Sie die Warnungen zur VM-Integrität anzeigen, die von einer fehlerhaften Komponente ausgelöst wurden, kategorisiert nach Schweregrad der Warnung.    

In der VM-Listenansicht wird durch Klicken auf den Namen einer VM die Seite **Integrität** der ausgewählten VM geöffnet, auf ähnliche Weise wie durch direktes Auswählen von **Insights (Vorschau)** aus der VM.

![VM Insights einer ausgewählten Azure Virtual Machine](./media/vminsights-health/vminsights-directvm-health.png)

Hier ist ein Rollup des **Integritätsstatus** für die VM mit **Warnungen** nach Schweregrad kategorisiert dargestellt, die die Warnungen zur VM-Integrität darstellen, die beim Wechsel des Integritätsstatus von fehlerfrei zu fehlerhaft für ein bestimmtes Integritätskriterium ausgelöst werden.  Durch Auswählen von **VMs in kritischem Zustand** wird eine Seite mit einer Liste einer oder mehrerer VMs geöffnet, die sich in einem kritischen Integritätsstatus befinden.  Durch Klicken auf den Integritätsstatus für eine der VMs in der Liste wird die Ansicht **Integritätsdiagnose** der betreffenden VM angezeigt.  Hier können Sie herausfinden, welche Integritätskriterien ein Problem mit dem Integritätsstatus widerspiegeln. Wenn die Seite **Integritätsdiagnose** geöffnet wird, zeigt sie alle Komponenten der VM und die ihnen zugeordneten Integritätskriterien mit dem aktuellen Integritätsstatus an. Weitere Informationen finden Sie unter [Integritätsdiagnose](#health-diagnostics).  

Wenn Sie **Alle Integritätskriterien anzeigen** auswählen, wird eine Seite geöffnet, die eine Liste aller für dieses Feature verfügbaren Integritätskriterien anzeigt.  Die Informationen können auf der Grundlage der folgenden Optionen weiter gefiltert werden:

* **Typ** – Es gibt drei Typen von Integritätskriterien für die Bewertung von Zuständen und für den Rollup des Gesamtintegritätsstatus der überwachten VM.  
    a. **Einheit** – misst einen Aspekt des virtuellen Computers. Dieser Typ von Integritätskriterium kann beispielsweise einen Leistungsindikator überprüfen, um die Leistung der Komponente zu bestimmen, ein Skript auszuführen, um eine synthetische Transaktion durchzuführen oder nach einem Ereignis Ausschau zu halten, das auf einen Fehler hinweist.  Standardmäßig ist der Filter auf „Einheit“ festgelegt.  
    b. **Abhängigkeit** – Stellt einen Integritätsrollup zwischen verschiedenen Entitäten bereit. Durch diese Integritätskriterien ist es möglich, dass die Integrität einer Entität von der Integrität einer anderen Art von Entität abhängt, auf die sie für erfolgreichen Betrieb angewiesen ist.  
    c. **Aggregat** – stellt einen kombinierten Integritätsstatus ähnlicher Integritätskriterien bereit. Die Integritätskriterien Einheit und Abhängigkeit sind normalerweise unter einem zusammengefassten Integritätskriterium konfiguriert. Nicht nur wird eine bessere allgemeine Organisation der vielen verschiedenen für eine Entität angezielten Integritätskriterien ermöglicht, ein zusammengefasstes Integritätskriterium stellt auch einen eindeutigen Integritätsstatus für verschiedene Kategorien der Entitäten zur Verfügung.

* **Kategorie** – Typ von Integritätskriterien, der zum Gruppieren von Kriterien ähnlichen Typs für die Berichterstellung verwendet wird.  Dabei handelt es sich entweder um **Verfügbarkeit** oder um **Leistung**.

Sie können weiter ins Detail einsteigen, um zu sehen, welche Instanzen fehlerhaft sind, indem Sie auf einen Wert in der Spalte **Fehlerhafte Komponente** klicken.  Auf der Seite sind die Komponenten, die sich in einem kritischen Integritätsstatus befinden, in einer Tabelle aufgelistet.    

## <a name="health-diagnostics"></a>Integritätsdiagnose

Auf der Seite **Integritätsdiagnose** können Sie das Integritätsmodell eines virtuellen Computers anzeigen, in dem alle Komponenten des virtuellen Computers, die zugeordneten Integritätskriterien, Zustandsänderungen und weitere wichtige Probleme aufgeführt sind, die bei der Überwachung der mit dem virtuellen Computer verbundenen Komponenten ermittelt wurden.

![Beispiel der Seite „Integritätsdiagnose“ für eine VM](./media/vminsights-health/health-diagnostics-page-01.png)

Sie können die Integritätsdiagnose auf eine der folgenden Weisen starten.

* Nach dem Rollupintegritätsstatus für alle VMs aus der VM-Aggregatperspektive in Azure Monitor.  Klicken Sie auf der Seite **Integrität** im Abschnitt **Integrität der Gast-VM** auf das Symbol für den Integritätsstatus **Kritisch**, **Warnung**, **Fehlerfrei** oder **Unbekannt**, und führen Sie einen Drilldown zu der Seite aus, auf der alle VMs aufgelistet sind, die der Filterkategorie entsprechen.  Durch Klicken auf den Wert in der Spalte **Integritätsstatus** wird die Integritätsdiagnose mit dem Bereich der betreffenden VM geöffnet.      

* Nach dem Betriebssystem aus der VM-Aggregatperspektive in Azure Monitor. Unter **VM-Distribution** bewirkt das Auswählen eines der Spaltenwerte das Öffnen der Seite **Virtual Machines** und die Rückgabe einer Liste in der Tabelle, die der Filterkategorie entspricht.  Durch Klicken auf den Wert in der Spalte **Integritätszustand** wird die Integritätsdiagnose für den ausgewählten virtuellen Computer geöffnet.    
 
* Aus der Gast-VM auf der Registerkarte **Integrität** im Azure Monitor for VMs durch Auswählen von **Integritätsdiagnose anzeigen** 

In Integritätsdiagnosen sind die Integritätsinformationen nach den folgenden Kategorien geordnet: 

* Verfügbarkeit
* Leistung
 
Alle für eine bestimmte Komponente definierten Integritätskriterien wie logischer Datenträger, CPU usw. können ohne Filterung anhand der beiden Kategorien angezeigt werden (in einer Gesamtansicht aller Kriterien). Sie können die Ergebnisse auch nach beiden Kategorien filtern, wenn Sie die Optionen **Verfügbarkeit** oder **Leistung** auf der Seite auswählen. Zudem ist die Kategorie der Kriterien daneben in der Spalte **Integritätskriterien** zu sehen. Wenn die Kriterien nicht mit der ausgewählten Kategorie übereinstimmen, wird in der Spalte **Integritätskriterien** die Meldung **Für die ausgewählte Kategorie ist kein Integritätskriterium verfügbar** angezeigt.  

Integritätskriterien werden durch einen dieser vier Zustände definiert: *Kritisch*, *Warnung*, *Fehlerfrei* und *Unbekannt*. Die ersten drei Zustände sind konfigurierbar. Dies bedeutet: Sie können die Schwellenwerte der Monitore direkt aus dem Konfigurationsbereich „Integritätskriterien“ oder mithilfe des [Vorgangs zur Monitoraktualisierung](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update) der Azure Monitor-REST-API ändern. *Unbekannt* kann nicht konfiguriert werden und ist für bestimmte Szenarien reserviert.  

Die Seite „Integritätsdiagnose“ weist drei Hauptabschnitte auf:

* Komponentenmodell 
* Integritätskriterien
* Zustandsänderungen 

![Abschnitte auf der Seite „Integritätsdiagnose“](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Komponentenmodell

Die Spalte ganz links auf der Seite „Integritätsdiagnose“ ist das Komponentenmodell. In dieser Spalte werden alle dem virtuellen Computer zugeordneten Komponenten zusammen mit dem jeweiligen aktuellen Integritätszustand angezeigt. 

Im folgenden Beispiel sind die erkannten Instanzen Datenträger, logischer Datenträger, Prozessor, Arbeitsspeicher und Betriebssystem. Mehrere Instanzen dieser Komponenten werden ermittelt und in dieser Spalte angezeigt. In der folgenden Abbildung werden beispielsweise die zwei Instanzen von logischen Datenträgern – C: und D: – des virtuellen Computers angezeigt, die beide den Zustand „Fehlerfrei“ aufweisen.  

![Beispiel für die Darstellung eines Komponentenmodells in der Integritätsdiagnose](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Integritätskriterien

Die mittlere Spalte auf der Seite „Integritätsdiagnose“ ist die Spalte **Integritätskriterien**. Das für die VM definierte Integritätsmodell wird als Hierarchiebaum angezeigt. Das Integritätsmodell für einen virtuellen Computer besteht aus Einheits- und Aggregatintegritätskriterien.  

![Beispiel für die Darstellung von Integritätskriterien in der Integritätsdiagnose](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Ein Integritätskriterium misst die Integrität der überwachten Instanz anhand bestimmter Kriterien, bei denen es sich z.B. um einen Schwellenwert oder den Zustand einer Entität handeln kann. Ein Integritätskriterium weist wie weiter oben beschrieben entweder zwei oder drei Schwellenwerte für den Integritätszustand auf. Das Integritätskriterium kann sich jederzeit nur in einem seiner möglichen Zustände befinden. 

Die Gesamtintegrität eines Ziels wird durch die Integrität der einzelnen im Integritätsmodell definierten Integritätskriterien bestimmt. Dies ist eine Kombination aus Integritätskriterien, die direkt auf das Ziel gerichtet sind, und Integritätskriterien, die auf Komponenten abzielen, für die mittels eines Aggregatintegritätskriteriums ein Rollup zum Ziel ausgeführt wird. Diese Hierarchie ist im Abschnitt **Integritätskriterien** der Seite Integritätsdiagnose veranschaulicht. Die Richtlinie für den Integritätsrollup ist Bestandteil der Konfiguration der Aggregatintegritätskriterien (Standardwert ist auf *Worst-of* festgelegt). Eine Liste des Standardsatzes von Integritätskriterien, die im Rahmen dieses Features ausgeführt werden, finden Sie unter dem Abschnitt [Details der Überwachungskonfiguration](#monitoring-configuration-details), und Sie können über [Monitorinstanzen – Liste nach Ressourcenvorgang](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) der Azure Monitor-REST-API eine Liste aller Integritätskriterien mit deren detaillierter Konfiguration abrufen, die für die Azure VM-Ressource ausgeführt wird.  

Für Integritätskriterien vom Typ **Einheit** kann die Konfiguration durch Klicken auf den Link mit den Auslassungspunkten ganz rechts geändert und durch Auswählen von **Details anzeigen** der Konfigurationsbereich geöffnet werden. 

![Beispiel für das Konfigurieren eines Integritätskriteriums](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Durch Verwendung des Beispiels **Mittlere Dauer pro Schreibvorgang** im Konfigurationsbereich für das ausgewählte Integritätskriterium kann der zugehörige Schwellenwert mit einem anderen numerischen Wert konfiguriert werden. Es handelt sich um eine Überwachung mit zwei Zuständen, d.h., es ist nur eine Änderung von „Fehlerfrei“ in „Warnung“ möglich. Andere Integritätskriterien können drei Zustände aufweisen; bei diesen können Sie den Wert für den Warnungsschwellenwert und den Schwellenwert für den kritischen Zustand konfigurieren. Sie können auch den Schwellenwert mithilfe des [Vorgangs zur Monitoraktualisierung](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update) der Azure Monitor-REST-API ändern.

>[!NOTE]
>Das Anwenden einer Konfigurationsänderung auf Integritätskriterien wirkt sich auf alle überwachten Instanzen aus.  Wenn Sie beispielsweise **Datenträger –1 D:** auswählen und den Schwellenwert **Mittlere Dauer pro Schreibvorgang** ändern, gilt die Änderung nicht nur für diese Instanz, sondern für alle auf dem virtuellen Computer erkannten und überwachten Instanzen von Datenträgern.
>

![Beispiel für das Konfigurieren eines Integritätskriteriums für einen Einheitenmonitor](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Wenn Sie mehr zum Integritätsindikator erfahren möchten, können Sie in den enthaltenen Artikeln Probleme, Ursachen und Lösungen ermitteln. Klicken Sie auf der Seite auf den Link **Informationen anzeigen**. In Ihrem Browser wird eine neue Registerkarte mit dem entsprechenden Wissensartikel geöffnet. Sie können sich [hier](https://docs.microsoft.com/azure/monitoring/infrastructure-health/) jederzeit alle Wissensartikel zu Integritätskriterien ansehen, die im Integritätsfeature von Azure Monitor for VMs enthalten sind.
  
### <a name="state-changes"></a>Zustandsänderungen

Ganz rechts auf der Seite „Integritätsdiagnose“ wird die Spalte **Zustandsänderungen** angezeigt. Sie listet alle Zustandsänderungen auf, die den im Abschnitt **Integritätskriterien** ausgewählten Integritätskriterien zugeordnet sind, oder die Zustandsänderungen einer VM, wenn in der Spalte **Komponentenmodell** oder **Integritätskriterien** der Tabelle eine VM ausgewählt ist. 

![Beispiel für die Darstellung von Zustandsänderungen in der Integritätsdiagnose](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Dieser Abschnitt besteht aus dem Status der Integritätskriterien und den entsprechenden Zeitpunkten, wobei der letzte Zustand oben angezeigt wird.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Zuordnung der Spalten „Komponentenmodell“, „Integritätskriterien“ und „Zustandsänderung“ 

Die drei Spalten sind miteinander verknüpft. Wenn Sie eine erkannte Instanz im Abschnitt **Komponentenmodell** auswählen, wird der Abschnitt **Integritätskriterien** nach dieser Komponentenansicht gefiltert. Entsprechend wird auch der Abschnitt **Zustandsänderung** basierend auf dem ausgewählten Integritätskriterium aktualisiert. 

![Beispiel zum Auswählen einer überwachten Instanz mit Ergebnissen](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Wenn Sie im Beispiel oben **Datenträger – 1 D:** auswählen, wird die Struktur der Integritätskriterien nach **Datenträger – 1 D:** gefiltert. In der Spalte **Zustandsänderung** wird die Zustandsänderung basierend auf der Verfügbarkeit von **Datenträger – 1 D:** angezeigt. 

Um den aktualisierten Integritätszustand anzuzeigen, können Sie die Seite „Integritätsdiagnose“ aktualisieren, indem Sie auf den Link **Aktualisieren** klicken.  Wenn basierend auf dem vordefinierten Abrufintervall eine Aktualisierung des Integritätsstatus des Integritätskriteriums erfolgt, können Sie mit dieser Aufgabe Wartezeiten vermeiden und den aktuellen Integritätsstatus anzeigen.  Der **Zustand der Integritätskriterien** ist ein Filter, über den Sie den Bereich der Ergebnisse basierend auf dem ausgewählten Integritätszustand – *Fehlerfrei*, *Warnung*, *Kritisch*, *Unbekannt* und *Alle* – festlegen können.  Die Zeitangabe **Letzte Aktualisierung** oben rechts stellt den Zeitpunkt der letzten Aktualisierung der Seite „Integritätsdiagnose“ dar.  

## <a name="alerts"></a>Alerts

Das Integritätsfeature von Azure Monitor for VMs ist in [Azure-Warnungen](../../azure-monitor/platform/alerts-overview.md) integriert und löst eine Warnung aus, wenn die vordefinierten Integritätskriterien von „Fehlerfrei“ in einen fehlerhaften Zustand wechseln und der Umstand erkannt wird. Warnungen werden nach dem Schweregrad kategorisiert – Schweregrad 0 bis 4, wobei 0 den höchsten Schweregrad darstellt. 

Warnungen sind keiner Aktionsgruppe zugeordnet, damit Sie benachrichtigt werden, wenn die Warnung ausgelöst wurde. Der Abonnementbesitzer muss Benachrichtigungen konfigurieren und dazu die Schritte [weiter unten in diesem Abschnitt](#configure-alerts) ausführen.   

Die Gesamtzahl der VM-Integritätswarnungen, nach Schweregrad kategorisiert, steht auf dem Dashboard **Integrität** im Bereich **Warnungen** zur Verfügung. Wenn Sie entweder die Gesamtzahl der Warnungen oder die einem Schweregrad entsprechende Anzahl auswählen, wird die Seite **Warnungen** geöffnet und listet alle Warnungen auf, die Ihrer Auswahl entsprechen.  Wenn Sie beispielsweise die Zeile auswählen, die **Schweregrad 1** entspricht, sehen Sie eine Ansicht ähnlich der folgenden:

![Beispiel für alle Warnungen mit Schweregrad 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Die Warnungen auf der Seite **Warnungen** weisen nicht nur einen auf Ihre Auswahl eingeschränkten Bereich auf, sondern sind darüber hinaus nach **Ressourcentyp** gefiltert und zeigen nur Integritätswarnungen an, die von der VM-Ressource ausgelöst wurden.  Dies spiegelt sich in der Liste der Warnungen unter der Spalte **Zielressource** wider, wo die Azure-VM angezeigt wird, für die die Warnung beim Erreichen der Fehlerbedingung für das einzelne Integritätskriterium ausgegeben wurde.  

Warnungen von anderen Ressourcentypen oder Diensten sollen nicht in dieser Ansicht enthalten sein, also z.B. keine Protokollwarnungen, die auf Protokollabfragen basieren, oder Metrikwarnungen, die Sie normalerweise auf der Standardseite [Alle Warnungen](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) in Azure Monitor anzeigen. 

Zum Filtern dieser Ansicht können Sie Werte in den Dropdownmenüs am oberen Rand der Seite auswählen.

|Column |BESCHREIBUNG | 
|-------|------------| 
|Abonnement |Wählen Sie ein Azure-Abonnement aus. Nur Warnungen im ausgewählten Abonnement sind in der Ansicht enthalten. | 
|Ressourcengruppe |Wählen Sie eine einzelne Ressourcengruppe aus. Nur Warnungen mit Zielen in der ausgewählten Ressourcengruppe sind in der Ansicht enthalten. | 
|Ressourcentyp |Wählen Sie mindestens einen Ressourcentyp aus. Standardmäßig sind in dieser Ansicht nur Warnungen mit dem Ziel **Virtuelle Computer** ausgewählt und enthalten. Diese Spalte ist nur verfügbar, nachdem eine Ressourcengruppe angegeben wurde. | 
|Resource |Wählen Sie eine Ressource aus. Nur Warnungen mit dieser Ressource als Ziel sind in der Ansicht enthalten. Diese Spalte ist nur verfügbar, nachdem ein Ressourcentyp angegeben wurde. | 
|Severity |Wählen Sie einen Warnungsschweregrad oder *Alle* aus, um Warnungen aller Schweregrade einzuschließen. | 
|Überwachungsbedingung |Wählen Sie eine Überwachungsbedingung aus, um Warnungen danach zu filtern, ob sie vom System *Ausgelöst* oder vom System *Gelöst* wurden, falls die Bedingung nicht mehr aktiv ist. Oder wählen Sie *Alle* aus, um Warnungen in allen Zuständen einzuschließen. | 
|Warnungsstatus |Wählen Sie einen Warnungsstatus aus, *Neu*, *Bestätigt*, *Geschlossen*, oder wählen Sie *Alle* aus, um Warnungen mit jedem Status einzuschließen. | 
|Überwachungsdienst |Wählen Sie einen Dienst oder *Alle* aus, um alle Dienste einzuschließen. Für dieses Feature werden nur Warnungen von *VM Insights* unterstützt.| 
|Zeitbereich| Nur Warnungen, die innerhalb des ausgewählten Zeitfensters ausgelöst wurden, sind in der Ansicht enthalten. Unterstützte Werte sind die letzte Stunde, die letzten 24 Stunden, die letzten 7 Tage und die letzten 30 Tage. | 

Die Seite **Warnungsdetail** wird angezeigt, wenn Sie eine Warnung auswählen, stellt Details zur Warnung zur Verfügung und erlaubt Ihnen, ihren Status zu ändern. Weitere Informationen zum Verwalten von Warnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Warnungen mithilfe von Azure Monitor](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>Derzeit ist es nicht möglich, über das Portal neue Warnungen basierend auf Integritätskriterien zu erstellen oder vorhandene Integritätswarnungsregeln in Azure Monitor zu ändern.  
>

![Bereich „Warnungsdetails“ für eine ausgewählte Warnung](./media/vminsights-health/alert-details-pane-01.png)

Der Warnungszustand kann außerdem für eine oder mehrere Warnungen geändert werden, indem Sie in der oberen linken Ecke der Seite **Alle Warnungen** den Befehl **Status ändern** auswählen. Im Bereich **Warnungsstatus ändern** wählen Sie einen der Status aus, fügen eine Beschreibung der Änderung im Feld **Kommentar** hinzu und klicken dann auf **OK**, um Ihre Änderungen zu committen. Während die Informationen überprüft und die Änderungen übernommen werden, können Sie den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.  

### <a name="configure-alerts"></a>Konfigurieren von Warnungen
Bestimmte Warnungsverwaltungsaufgaben können nicht über das Azure-Portal ausgeführt werden und müssen mit der [Azure Monitor-REST-API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) erfolgen. Dies gilt insbesondere in folgenden Fällen:

- Aktivieren oder Deaktivieren einer Warnung für Integritätskriterien 
- Einrichten von Benachrichtigungen für Warnungen zu Integritätskriterien 

Der in den einzelnen Beispielen verwendete Ansatz sieht die Verwendung von [ARMClient](https://github.com/projectkudu/armclient) auf Ihrem Windows-Computer vor. Wenn Sie nicht mit dieser Vorgehensweise vertraut sind, siehe [Verwenden von ARMClient](../platform/rest-api-walkthrough.md#use-armclient).  

#### <a name="enable-or-disable-alert-rule"></a>Aktivieren oder Deaktivieren der Warnungsregel

Um eine Warnung für ein bestimmtes Integritätskriterium zu aktivieren oder zu deaktivieren, muss die Eigenschaft *alertGeneration* der Integritätskriterien in den Wert **Disabled** oder **Enabled** geändert werden. Um die *monitorId* eines bestimmten Integritätskriteriums zu ermitteln, wird im folgenden Beispiel gezeigt, wie der Wert für das Kriterium **Logischer Datenträger\Mittlere Dauer pro Übertragung** abgefragt werden kann.

1. Geben Sie in einem Terminalfenster **armclient.exe login**ein. Dabei werden Sie dazu aufgefordert, sich bei Azure anzumelden.

2. Geben Sie den folgenden Befehl ein, um alle auf einem bestimmten virtuellen Computer aktiven Integritätskriterien abzurufen und den Wert für die Eigenschaft *monitorId* zu bestimmen. 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Das folgende Beispiel zeigt die Ausgabe dieses Befehls. Notieren Sie sich den Wert von *MonitorId*. Dieser Wert ist im nächsten Schritt erforderlich, bei dem wir die ID der Integritätskriterien angeben und ihre Eigenschaft ändern müssen, um eine Warnung zu erstellen.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Geben Sie den folgenden Befehl zum Ändern der *alertGeneration*-Eigenschaft ein.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Geben Sie den in Schritt 2 verwendeten GET-Befehl ein, um zu überprüfen, ob der Wert der Eigenschaft auf **Disabled** festgelegt ist.  

#### <a name="associate-action-group-with-health-criteria"></a>Zuordnen einer Aktionsgruppe zu Integritätskriterien

Die Funktion „Integrität“ von Azure Monitor für VMs unterstützt SMS-und E-Mail-Benachrichtigungen, wenn Warnungen generiert werden, sobald Integritätskriterien fehlerhaft werden. Um Benachrichtigungen zu konfigurieren, müssen Sie den Namen der Aktionsgruppe notieren, die für das Senden von SMS- oder E-Mail-Benachrichtigungen konfiguriert ist. 

>[!NOTE]
>Diese Aktion muss für jede überwachte VM durchgeführt werden, für die Sie eine Benachrichtigung erhalten möchten. Sie gilt nicht automatisch für alle VMs der Ressourcengruppe.  

1. Geben Sie in einem Terminalfenster **armclient.exe login**ein. Dabei werden Sie dazu aufgefordert, sich bei Azure anzumelden.

2. Geben Sie den folgenden Befehl ein, um einer Aktionsgruppe Warnungsregeln zuzuordnen.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Um zu überprüfen, ob der Wert der Eigenschaft **actionGroupResourceIds** erfolgreich aktualisiert wurde, geben Sie den folgenden Befehl ein.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Die Ausgabe sollte wie folgt aussehen:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erkennen von Engpässen und Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [View Azure VM Performance](vminsights-performance.md) (Anzeigen der Leistung von Azure-VMs). Informationen zu erkannten Anwendungsabhängigkeiten finden Sie unter [View Azure Monitor for VMs Map](vminsights-maps.md) (Anzeigen der Zuordnung von Azure Monitor for VMs). 
