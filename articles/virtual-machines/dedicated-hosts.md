---
title: Übersicht über dedizierte Azure-Hosts für virtuelle Computer
description: Erfahren Sie mehr darüber, wie dedizierte Azure-Hosts für die Bereitstellung virtueller Computer verwendet werden können.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: a42b07254deaf19d253f7523631018bfe7166a57
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339590"
---
# <a name="azure-dedicated-hosts"></a>Dedizierte Azure-Hosts

Azure Dedicated Host ist ein Dienst zum Bereitstellen physischer Server, die virtuelle Computer hosten können und ausschließlich für ein Azure-Abonnement reserviert sind. Dedizierte Hosts sind die in unseren Rechenzentren verwendeten physischen Server, die als Ressource bereitgestellt werden. Sie können dedizierte Hosts in einer Region, einer Verfügbarkeitszone und einer Fehlerdomäne bereitstellen. Anschließend können Sie VMs direkt in Ihren bereitgestellten Hosts platzieren, und zwar in einer beliebigen Konfiguration, die Ihren Anforderungen am besten entspricht.


## <a name="benefits"></a>Vorteile 

Die Reservierung des gesamten Hosts bietet die folgenden Vorteile:

-   Hardwareisolation auf physischer Serverebene. Es werden keine anderen virtuellen Computer auf Ihren Hosts platziert. Dedizierte Hosts werden in denselben Rechenzentren bereitgestellt und nutzen dasselbe Netzwerk und dieselbe zugrunde liegende Speicherinfrastruktur wie andere, nicht isolierte Hosts.
-   Kontrolle über Wartungsereignisse, die von der Azure-Plattform initiiert werden. Der Großteil der Wartungsereignisse hat nur geringfügige oder gar keine Auswirkungen auf Ihre virtuellen Computer, es gibt jedoch einige empfindliche Workloads, bei denen jede Sekunde Pause Auswirkungen haben kann. Mit dedizierten Hosts können Sie sich für ein Wartungsfenster anmelden, um die Auswirkungen auf Ihren Dienst zu verringern.
-   Mit dem Azure-Hybridvorteil können Sie Ihre eigenen Lizenzen für Windows und SQL in Azure nutzen. Die Verwendung des Hybridvorteils bietet noch mehr Vorteile. Weitere Informationen finden Sie unter [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Gruppen, Hosts und VMs  

![Ansicht der neuen Ressourcen für dedizierte Hosts.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Eine **Hostgruppe** ist eine Ressource, die eine Sammlung dedizierter Hosts darstellt. Sie erstellen eine Hostgruppe in einer Region und einer Verfügbarkeitszone und fügen ihr Hosts hinzu.

Ein **Host** ist eine Ressource, die einem physischen Server in einem Azure-Rechenzentrum zugeordnet ist. Der physische Server wird beim Erstellen des Hosts zugewiesen. Ein Host wird in einer Hostgruppe erstellt. Für jeden Host gilt eine SKU, die beschreibt, welche VM-Größen erstellt werden können. Auf jedem Host können mehrere VMs unterschiedlicher Größen gehostet werden, sofern sie aus derselben Größenserie stammen.


## <a name="high-availability-considerations"></a>Überlegungen zur Hochverfügbarkeit 

Für Hochverfügbarkeit sollten Sie mehrere VMs bereitstellen, die über mehrere Hosts verteilt sind (mindestens 2). Mit dedizierten Azure-Hosts können Sie Ihre Infrastruktur auf unterschiedliche Weise bereitstellen, um Ihre Fehlerisolationsgrenzen zu strukturieren.

### <a name="use-availability-zones-for-fault-isolation"></a>Verwenden von Verfügbarkeitszonen für die Fehlerisolation

Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Eine Hostgruppe wird in einer einzelnen Verfügbarkeitszone erstellt. Nach der Erstellung werden alle Hosts in dieser Zone platziert. Um zonenübergreifende Hochverfügbarkeit zu erreichen, müssen Sie mehrere Hostgruppen (eine pro Zone) erstellen und die Hosts entsprechend verteilen.

Wenn Sie einer Verfügbarkeitszone eine Hostgruppe zuweisen, müssen alle auf diesem Host erstellten VMs in derselben Zone erstellt werden.

### <a name="use-fault-domains-for-fault-isolation"></a>Verwenden von Fehlerdomänen für die Fehlerisolation

Ein Host kann in einer bestimmten Fehlerdomäne erstellt werden. Genau wie virtuelle Computer in einer Skalierungsgruppe oder Verfügbarkeitsgruppe werden Hosts in verschiedenen Fehlerdomänen in unterschiedlichen physischen Racks im Rechenzentrum platziert. Wenn Sie eine Hostgruppe erstellen, müssen Sie die Anzahl der Fehlerdomänen angeben. Beim Erstellen von Hosts in der Hostgruppe weisen Sie den einzelnen Hosts Fehlerdomänen zu. Für die VMs ist keine Zuweisung von Fehlerdomänen erforderlich.

Fehlerdomänen sind nicht mit einer Colocation identisch. Wenn dieselbe Fehlerdomäne für zwei Hosts verwendet wird, bedeutet das nicht, dass Sie sich nahe beieinander befinden.

Fehlerdomänen beziehen sich auf die Hostgruppe. Sie sollten nicht von Antiaffinität zwischen zwei Hostgruppen ausgehen (es sei denn, sie befinden sich in verschiedenen Verfügbarkeitszonen).

Bei virtuellen Computern, die auf Hosts mit unterschiedlichen Fehlerdomänen bereitgestellt werden, befinden sich die zugrunde liegenden Dienste für verwaltete Datenträger in mehreren Speicherstempeln, um den Schutz durch Fehlerisolation zu erhöhen.

### <a name="using-availability-zones-and-fault-domains"></a>Verwenden von Verfügbarkeitszonen und Fehlerdomänen

Sie können beide Funktionen kombinieren, um eine noch höhere Fehlerisolation zu erzielen. In diesem Fall geben Sie die Verfügbarkeitszone und die Anzahl der Fehlerdomänen für jede Hostgruppe an und weisen jedem Host in der Gruppe eine Fehlerdomäne und jeder VM eine Verfügbarkeitszone zu.

[Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) finden Sie die Resource Manager-Beispielvorlage, die für maximale Resilienz in einer Region Zonen und Fehlerdomänen zum Verteilen der Hosts verwendet.


## <a name="manual-vs-automatic-placement"></a>Manuelle und automatische Platzierung 

> [!IMPORTANT]
> Automatische Platzierung befindet sich zurzeit in der öffentlichen Vorschau.
> Um an der Vorschau teilzunehmen, füllen Sie die Onboardingumfrage für die Vorschau unter [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) aus.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn Sie eine VM in Azure erstellen, können Sie den dedizierten Host auswählen, der verwendet werden soll. Sie können darüber hinaus die Option zum automatischen Platzieren von VMs auf vorhandenen Hosts innerhalb einer Hostgruppe verwenden. 

Achten Sie beim Erstellen einer neuen Hostgruppe darauf, dass die Einstellung für automatische VM-Platzierung ausgewählt ist. Wählen Sie beim Erstellen Ihrer VM die Hostgruppe aus, und lassen Sie Azure den besten Host für Ihre VM wählen. 

Hostgruppen, die für automatische Platzierung aktiviert sind, erfordern keine automatische Platzierung aller VMs. Sie können trotzdem noch einen Host explizit auswählen, selbst wenn für die Hostgruppe automatische Platzierung aktiviert ist. 

### <a name="limitations"></a>Einschränkungen

Bekannte Probleme und Einschränkungen bei der Verwendung der automatischen VM-Platzierung:

- Sie können für Ihre dedizierten Hosts keine Azure-Hybridvorteile anwenden.
- Sie können Ihre VMs nicht erneut bereitstellen. 
- Sie können in Verbindung mit dedizierten Hosts keine VMs der Serien Lsv2, NVasv4, NVsv3, Msv2 oder M verwenden. 


## <a name="virtual-machine-scale-set-support"></a>Unterstützung für VM-Skalierungsgruppe

Mithilfe von VM-Skalierungsgruppen können Sie eine Gruppe von virtuellen Computern als einzelne Ressource behandeln und Verfügbarkeits-, Verwaltungs-, Skalierungs- und Orchestrierungsrichtlinien als Gruppe anwenden. Ihre vorhandenen dedizierten Hosts können ebenfalls für VM-Skalierungsgruppen verwendet werden. 

> [!IMPORTANT]
> Virtual Machine Scale Sets auf Dedicated Hosts befindet sich derzeit in der öffentlichen Vorschau.
> Um an der Vorschau teilzunehmen, füllen Sie die Onboardingumfrage für die Vorschau unter [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) aus.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Beim Erstellen einer VM-Skalierungsgruppe können Sie eine vorhandene Hostgruppe angeben, die alle VM-Instanzen aufnimmt, die auf dedizierten Hosts erstellt werden.

Die folgenden Anforderungen gelten für das Erstellen einer VM-Skalierungsgruppe in einer dedizierten Hostgruppe:

- Die automatische Platzierung der VM muss aktiviert sein.
- Die Verfügbarkeitseinstellung Ihrer Hostgruppe sollte mit Ihrer Skalierungsgruppe übereinstimmen. 
    - Für regionale Skalierungsgruppen sollte eine regionale Hostgruppe (die ohne Angabe einer Verfügbarkeitszone erstellt wurde) verwendet werden.
    - Die Hostgruppe und die Skalierungsgruppe müssen die gleiche Verfügbarkeitszone verwenden. 
    - Die Anzahl von Fehlerdomänen für die Hostgruppenebene sollte mit der Anzahl der Fehlerdomänen für die Skalierungsgruppe übereinstimmen. Im Azure-Portal können Sie die *Maximale Zuweisung* für Ihre Skalierungsgruppe angeben, die die Anzahl der Fehlerdomänen auf 1 festlegt.
- Dedizierte Hosts mit ausreichender Kapazität und gleichen Einstellungen für Skalierungsgruppenzonen und Fehlerdomänen sollten zuerst erstellt werden.
- Die unterstützten VM-Größen für Ihre dedizierten Hosts sollten mit der für Ihre Skalierungsgruppe verwendeten übereinstimmen.

Nicht alle Orchestrierungs- und Optimierungseinstellungen für Skalierungsgruppen werden von dedizierten Hosts unterstützt. Wenden Sie die folgenden Einstellungen auf Ihre Skalierungsgruppe an: 
- Deaktivieren Sie Überbereitstellung
- Verwenden Sie den Orchestrierungsmodus „ScaleSetVM“ 
- Verwenden Sie keine Näherungsplatzierungsgruppen für die gemeinsame Anordnung



## <a name="maintenance-control"></a>Wartungssteuerung

Die Infrastruktur, die Ihre virtuellen Computer unterstützt, kann gelegentlich aktualisiert werden, um die Zuverlässigkeit, Leistung und Sicherheit zu verbessern und neue Features zu integrieren. Die Azure-Plattform versucht, die Auswirkungen der Plattformwartung möglichst gering zu halten, aber Kunden mit *wartungsempfindlichen* Workloads können nicht einmal wenige Sekunden tolerieren, in denen die VM zur Wartung eingefroren oder getrennt werden muss.

Die **Wartungssteuerung** bietet Kunden eine Option zum Überspringen regulärer Plattformupdates, die für ihre dedizierten Hosts geplant sind, und wendet sie dann zu einem ausgewählten Zeitpunkt innerhalb eines 35-tägigen gleitenden Fensters an. Innerhalb des Wartungsfensters können Sie die Wartung direkt auf Hostebene und in beliebiger Reihenfolge anwenden. Wenn das Wartungsfenster vorbei ist, setzt Microsoft den Vorgang fort und wendet die ausstehende Wartung auf die Hosts an. Dabei richtet sich die Reihenfolge nicht unbedingt nach den benutzerdefinierten Fehlerdomänen.

Weitere Informationen finden Sie unter [Verwalten von Plattformupdates mit der Wartungssteuerung](./maintenance-control.md).

## <a name="capacity-considerations"></a>Überlegungen zur Kapazität

Nach der Bereitstellung eines dedizierten Hosts weist Azure ihn dem physischen Server zu. Dadurch wird sichergestellt, dass die Kapazität verfügbar ist, wenn Sie Ihren virtuellen Computer bereitstellen müssen. Azure nutzt die gesamte Kapazität in der Region (oder Zone), um einen physischen Server für Ihren Host auszuwählen. Das bedeutet auch, dass Kunden davon ausgehen können, ihren Bedarf an dedizierten Hosts nach Wunsch zu vergrößern, ohne auf mangelnden Speicherplatz im Cluster zu stoßen.

## <a name="quotas"></a>Kontingente

Es gibt zwei Arten von Kontingenten, die beim Bereitstellen eines dedizierten Hosts genutzt werden.

1. vCPU-Kontingent des dedizierten Hosts. Die standardmäßige Kontingentgrenze beträgt 3.000 vCPUs pro Region.
1. Kontingent der VM-Größenfamilie. Beispielsweise ist für ein Abonnement mit **nutzungsbasierter Bezahlung** in der Region „USA, Osten“ möglicherweise nur ein Kontingent von 10 vCPUs für die Größenserie Dsv3 verfügbar. Zur Bereitstellung eines dedizierten Dsv3-Hosts müssen Sie eine Kontingenterhöhung auf mindestens 64 vCPUs anfordern, bevor Sie den dedizierten Host bereitstellen können. 

Erstellen Sie eine Supportanfrage im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), um eine Kontingenterhöhung anzufordern.

Beim Bereitstellen eines dedizierten Hosts wird sowohl das vCPU-Kontingent des dedizierten Hosts als auch das vCPU-Kontingent der VM-Familie genutzt. Das regionale vCPU-Kontingent wird jedoch nicht genutzt.


![Screenshot der Seite für Verbrauch und Kontingente im Portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Weitere Informationen finden Sie unter [vCPU-Kontingente für virtuelle Computer](./windows/quotas.md).

Für kostenlose Testversionen und MSDN-Abonnements gilt kein Kontingent für dedizierte Azure-Hosts.

## <a name="pricing"></a>Preise

Benutzern wird die Anzahl dedizierter Hosts in Rechnung gestellt, unabhängig davon, wie viele VMs bereitgestellt sind. In Ihrem monatlichen Kontoauszug wird ein neuer abrechenbarer Ressourcentyp von Hosts aufgeführt. Die VMs auf einem dedizierten Host werden weiterhin in Ihrem Kontoauszug angezeigt, sie weisen jedoch einen Preis von 0 auf.

Der Hostpreis wird anhand der VM-Familie, dem Typ (Hardwaregröße) und der Region festgelegt. Ein Hostpreis ist relativ zur höchsten auf dem Host unterstützten VM-Größe.

Softwarelizenzierung, Speicher und Netzwerknutzung werden separat vom Host und den VMs abgerechnet. An diesen abrechenbaren Elementen finden keine Änderungen statt.

Weitere Informationen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

Sie können auch mit einer [reservierten Instanz von Azure Dedicated Hosts](prepay-dedicated-hosts-reserved-instances.md) Kosten einsparen.
 
## <a name="sizes-and-hardware-generations"></a>Größen und Hardwaregenerationen

Eine SKU ist für einen Host definiert und stellt die Größenserie und den Typ der VM dar. Sie können mehrere VMs unterschiedlicher Größe innerhalb eines einzelnen Hosts kombinieren, solange diese derselben Größenserie angehören. 

Der *Typ* ist die Hardwaregeneration. Die unterschiedlichen Hardwaretypen der einzelnen VM-Serien stammen von unterschiedlichen CPU-Anbietern und weisen andere CPU-Generationen und Anzahlen von Kernen auf. 

Die Größen und Hardwaretypen variieren je nach Region. Weitere Informationen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

> [!NOTE]
> Nach der Bereitstellung eines dedizierten Hosts können Sie Größe und Typ nicht mehr ändern. Wenn Sie eine andere Größe oder einen anderen Typ benötigen, müssen Sie einen neuen Host erstellen.  

## <a name="host-life-cycle"></a>Hostlebenszyklus


Azure überwacht und verwaltet den Integritätsstatus Ihrer Hosts. Die folgenden Zustände werden zurückgegeben, wenn Sie Ihren Host abfragen:

| Integritätsstatus   | BESCHREIBUNG       |
|----------|----------------|
| Host verfügbar     | Es liegen keine bekannten Probleme mit dem Host vor.   |
| Host wird untersucht  | Es liegen einige Probleme mit dem Host vor, die derzeit untersucht werden. Dies ist ein erforderlicher Übergangszustand, in dem Azure versucht, den Umfang und die Ursache des identifizierten Problems zu identifizieren. Auf dem Host ausgeführte virtuelle Computer können beeinträchtigt werden. |
| Ausstehende Aufhebung der Freigabe für den Host   | Azure kann den Host nicht wieder in einen fehlerfreien Status versetzen, und Sie werden aufgefordert, Ihre virtuellen Computer außerhalb dieses Hosts erneut bereitzustellen. Wenn `autoReplaceOnFailure` aktiviert ist, werden Ihre virtuellen Computer für eine fehlerfreie Hardware *wieder einsatzfähig gemacht*. Andernfalls wird der virtuelle Computer möglicherweise auf einem Host ausgeführt, der in Kürze ausfallen könnte.|
| Zuordnung für Host aufgehoben  | Alle virtuellen Computer wurden vom Host entfernt. Dieser Host wird Ihnen nicht mehr in Rechnung gestellt, da die Hardware aus der Rotation entfernt wurde.   |


## <a name="next-steps"></a>Nächste Schritte

- Sie können einen dedizierten Host über [Azure PowerShell](./windows/dedicated-hosts-powershell.md), das [Portal](./dedicated-hosts-portal.md) und die [Azure-Befehlszeilenschnittstelle](./linux/dedicated-hosts-cli.md) bereitstellen.

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) finden Sie eine Beispielvorlage, die sowohl Zonen als auch Fehlerdomänen für maximale Resilienz in einer Region verwendet.

- Sie können auch mit einer [reservierten Instanz von Azure Dedicated Hosts](prepay-dedicated-hosts-reserved-instances.md) Kosten einsparen.
