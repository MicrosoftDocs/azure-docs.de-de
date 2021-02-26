---
title: Abrufen der Kostenanalyse und Festlegen von Budgets für Azure Batch
description: Erfahren Sie, wie Sie eine Kostenanalyse erhalten, ein Budget festlegen und die Kosten für die zugrunde liegenden Computeressourcen und Softwarelizenzen reduzieren, die für die Ausführung Ihrer Batch-Workloads verwendet werden.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091326"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Abrufen der Kostenanalyse und Festlegen von Budgets für Azure Batch

In diesem Thema lernen Sie die Kosten kennen, die im Zusammenhang mit Azure Batch anfallen können. Außerdem erfahren Sie, wie Sie ein Budget für einen Batch-Pool oder ein Batch-Konto festlegen und wie Sie die Kosten für Batch-Workloads senken.

## <a name="understand-costs-associated-with-batch-resources"></a>Grundlegendes zu den Batch-Ressourcen verbundenen Kosten

Für Azure Batch selbst fallen keine Kosten an, für die zugrunde liegenden Computeressourcen und Softwarelizenzen, die zum Ausführen von Batch-Workloads verwendet werden, können jedoch Gebühren berechnet werden. Kosten können für VMs (virtuelle Computer) in einem Pool, für die Datenübertragung von der VM oder für in der Cloud gespeicherte Eingabe- oder Ausgabedaten anfallen.

### <a name="virtual-machines"></a>Virtuelle Computer

Virtuelle Computer sind die wichtigste Ressource, die für die Batch-Verarbeitung verwendet wird. Die Kosten für die Verwendung von VMs für Batch wird basierend auf dem Typ, der Menge und der Nutzungsdauer berechnet. Zu den VM-Abrechnungsoptionen gehören die [nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) und die [Reservierung](../cost-management-billing/reservations/save-compute-costs-reservations.md) (Zahlung im Voraus). Beide Optionen für die Bezahlung haben bestimmte Vorteile, die von Ihrer Computeworkload abhängen, und beide wirken sich anders auf Ihre Rechnung aus.

Jede VM in einem Pool, der mit [Konfiguration des virtuellen Computers](nodes-and-pools.md#virtual-machine-configuration) erstellt wurde, verfügt über einen zugeordneten Betriebssystemdatenträger, für den von Azure verwaltete Datenträger verwendet werden. Von Azure verwaltete Datenträger weisen zusätzliche Kosten auf, und auch für andere Datenträger-Leistungsstufen gelten noch andere Kosten.

### <a name="storage"></a>Storage

Bei der Bereitstellung von Anwendungen auf Batch-Knoten (VMs) mit [Anwendungspaketen](batch-application-packages.md) werden Ihnen die Azure Storage-Ressourcen in Rechnung gestellt, die von Ihren Anwendungspaketen verbraucht wurden. Darüber hinaus wird Ihnen die Speicherung von Eingabe- oder Ausgabedateien berechnet, z. B. Ressourcendateien und andere Protokolldaten.

Im Allgemeinen liegen die Kosten für Speicherdaten in Verbindung mit Batch deutlich unter den Kosten für Computeressourcen.

### <a name="networking-resources"></a>Netzwerkressourcen

In Batch-Pools werden Netzwerkressourcen verwendet, von denen einige Kosten verursachen. Insbesondere werden für Pools vom Typ „VM-Konfiguration“ Standard-Lastenausgleichsmodule verwendet, für die statische IP-Adressen erforderlich sind. Die von Batch verwendeten Lastenausgleichsmodule sind für im Modus „Benutzerabonnement“ konfigurierte [Konten](accounts.md#batch-accounts) sichtbar, jedoch nicht für Konten im Modus „Batch-Dienst“.

Für Standard-Lastenausgleichsmodule fallen Gebühren für alle Daten an, die für Batch-Pool-VMs übergeben werden. Für einige Batch-APIs, mit denen Daten aus Poolknoten (z. B. Task/Knotendatei abrufen), Taskanwendungspaketen, Ressourcen-/Ausgabedateien und Containerimages abgerufen werden, fallen ebenfalls Gebühren an.

### <a name="additional-services"></a>Zusätzliche Dienste

Je nach den Diensten, die Sie mit Ihrer Batch-Lösung nutzen, fallen unter Umständen weitere Gebühren an. Mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator/) können Sie die Kosten für die einzelnen zusätzlichen Dienste ermitteln. Zu den häufig mit Batch verwendeten Diensten, die mit Kosten verbunden sein können, gehören:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- VMs mit Grafikanwendungen

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Anzeigen der Kostenanalyse und Erstellen eines Budgets für einen Pool

Im Azure-Portal können Sie Budgets und Ausgabenwarnungen für Ihre Batch-Pools oder Ihr Batch-Konto erstellen. Budgets und Warnungen sind hilfreich, um Beteiligte über Risiken der Ausgabenüberschreitung zu benachrichtigen. Es ist aber durchaus möglich, eine Verzögerung bei der Ausgabe von Warnungen einzurichten und ein Budget geringfügig zu überschreiten.

> [!NOTE]
> Der Pool in diesem Beispiel verwendet die **VM-Konfiguration**, die für die meisten Pools empfohlen wird und deren Gebühren auf der Preisstruktur für Microsoft Azure Virtual Machines basieren. Für Pools, für die die **Cloud Services-Konfiguration** verwendet wird, erfolgt die Abrechnung auf Grundlage der Preisstruktur, die auf der Seite mit den Cloud Services-Preisen angegeben ist.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Anzeigen der Kostenanalyse für einen Batch-Pool

1. Navigieren Sie im Azure-Portal zu **Cost Management + Billing**.
1. Wählen Sie im Abschnitt **Abrechnungsbereiche** Ihr Abonnement aus.
1. Wählen Sie unter **Cost Management** die Option **Kostenanalyse** aus.
1. Wählen Sie **+ Filter hinzufügen**. Wählen Sie in der ersten Dropdownliste **Ressource** aus.
1. Wählen Sie in der zweiten Dropdownliste den Batch-Pool aus. Wenn der Pool ausgewählt ist, wird die Kostenanalyse für den Pool angezeigt, die dem hier gezeigten Beispiel ähnelt.
    ![Screenshot der Kostenanalyse eines Pools im Azure-Portal](./media/batch-budget/pool-cost-analysis.png)

In der sich ergebenden Kostenanalyse werden die Kosten des Pools und die Ressourcen angezeigt, die zu diesen Kosten beitragen. In diesem Beispiel sind die im Pool verwendeten VMs die teuerste Ressource.

### <a name="create-a-budget-for-a-batch-pool"></a>Erstellen eines Budgets für einen Batch-Pool

1. Wählen Sie auf der Seite **Kostenanalyse** die Option **Budget: Keines** aus.
1. Wählen Sie **Neues Budget erstellen >** aus.
1. Konfigurieren Sie in dem daraufhin angezeigten Fenster ein Budget speziell für Ihren Pool. Weitere Informationen finden Sie im [Tutorial: Erstellen und Verwalten von Azure-Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimieren der mit Azure Batch verbundenen Kosten

Abhängig von Ihrem Szenario möchten Sie Ihre Kosten eventuell so weit wie möglich reduzieren. Nutzen Sie eine oder mehrere dieser Strategien, um die Effizienz Ihrer Workloads zu maximieren und die möglichen Kosten zu senken.

### <a name="use-low-priority-virtual-machines"></a>Verwenden Sie virtuelle Computer mit niedriger Priorität

[VMs mit niedriger Priorität](batch-low-pri-vms.md) führen zu einer Reduzierung der Kosten von Batch-Workloads, indem in Azure überschüssige Rechenkapazität genutzt wird. Wenn Sie in Ihren Pools VMs mit niedriger Priorität angeben, nutzt Batch diese überschüssige Kapazität zum Ausführen Ihrer Workload. Wenn Sie VMs mit niedriger Priorität anstelle von dedizierten VMs verwenden, kann dies zu erheblichen Kosteneinsparungen führen.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Auswählen eines Standarddatenträgertyps für das Betriebssystem des virtuellen Computers

Azure bietet mehrere [Typen von Betriebssystemdatenträgern für VMs](../virtual-machines/disks-types.md). Die meisten VM-Serien verfügen über Größen, die sowohl Premium- als auch Standard-Speicher unterstützen. Wenn für einen Pool die VM-Größe „S“ ausgewählt wird, werden von Batch SSD Premium-Betriebssystemdatenträger konfiguriert. Bei Auswahl einer anderen VM-Größe als „S“ wird der kostengünstigere Datenträgertyp „HDD Standard“ verwendet. Beispielsweise werden SSD Premium-Betriebssystemdatenträger für `Standard_D2s_v3` und HDD Standard-Betriebssystemdatenträger für `Standard_D2_v3` verwendet.

SSD Premium-Betriebssystemdatenträger sind teurer, bieten aber eine höhere Leistung. VMs mit Premium-Datenträgern werden etwas schneller gestartet als VMs mit HDD Standard-Betriebssystemdatenträgern. Bei Batch wird der Betriebssystemdatenträger häufig nicht viel genutzt, da sich die Anwendungen und Taskdateien auf dem temporären SSD-Datenträger der VMs befinden. Aus diesem Grund können Sie häufig eine andere VM-Größe als „S“ auswählen, um die höheren Kosten für SSD Premium zu vermeiden, die bei der Bereitstellung einer „S“-VM-Größe anfallen.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Kaufen von Reservierungen für VM-Instanzen

Falls Sie Batch längere Zeit nutzen möchten, können Sie bei den Kosten für die VMs sparen, indem Sie für Ihre Workloads [Azure-Reservierungen](../cost-management-billing/reservations/save-compute-costs-reservations.md) verwenden. Reservierungsraten sind erheblich niedriger als die Raten bei der nutzungsbasierten Bezahlung. Für VM-Instanzen, die ohne Reservierung verwendet werden, wird die Rate für die nutzungsbasierte Bezahlung berechnet. Wenn Sie eine Reservierung erwerben, wird der Reservierungsrabatt angewandt.

### <a name="use-automatic-scaling"></a>Verwenden der Autoskalierung

Bei der [Autoskalierung](batch-automatic-scaling.md) wird die Anzahl von VMs in Ihrem Batch-Pool je nach dem Bedarf des aktuellen Auftrags dynamisch skaliert. Indem der Pool basierend auf der Lebensdauer eines Auftrags skaliert wird, kann durch die Autoskalierung sichergestellt werden, dass VMs zentral hochskaliert und nur verwendet werden, wenn ein Auftrag durchgeführt werden muss. Wenn der Auftrag abgeschlossen ist oder keine Aufträge vorhanden sind, werden die VMs automatisch herunterskaliert, um Computeressourcen zu sparen. Die Skalierung ermöglicht es Ihnen, die Gesamtkosten Ihrer Batch-Lösung zu reduzieren, indem Sie nur die benötigten Ressourcen verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure Cost Management und Abrechnung](../cost-management-billing/cost-management-billing-overview.md)
- Informieren Sie sich über die [Verwendung von VMs mit niedriger Priorität mit Batch](batch-low-pri-vms.md).
