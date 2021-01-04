---
title: Behandeln von Problemen bei der Bewertungs- und Abhängigkeitsvisualisierung in Azure Migrate
description: Erhalten Sie Hilfe bei der Bewertungs- und Abhängigkeitsvisualisierung in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: cefcd4ce287eecfe2c764d88d5d2233cc8ac0a5c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753444"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Behandeln von Problemen bei der Bewertung- und Abhängigkeitsvisualisierung

Dieser Artikel hilft Ihnen bei der Problembehandlung bei der Bewertungs- und Abhängigkeitsvisualisierung mit [Azure Migrate: Serverbewertung](migrate-services-overview.md#azure-migrate-server-assessment-tool) aus.


## <a name="assessment-readiness-issues"></a>Probleme mit der Bewertungsbereitschaft

Beheben Sie Probleme mit der Bewertungsbereitschaft wie folgt:

**Problem** | **Behebung**
--- | ---
Nicht unterstützter Starttyp | Azure unterstützt keine virtuellen Computer mit dem Starttyp „EFI“. Wir empfehlen, dass Sie vor einer Migration den Starttyp in „BIOS“ ändern. <br/><br/>Sie können die Azure Migrate-Servermigration verwenden, um die Migration solcher virtuellen Computer zu verarbeiten. Sie ändert den Starttyp des virtuellen Computers im Rahmen der Migration in „BIOS“.
Bedingt unterstütztes Windows-Betriebssystem | Der Unterstützungszeitraum für das Betriebssystem ist abgelaufen. Für die [Unterstützung in Azure](/troubleshoot/azure/virtual-machines/server-software-support) wird eine benutzerdefinierte Supportvereinbarung (Custom Support Agreement, CSA) benötigt. Ziehen Sie vor der Migration zu Azure ein Upgrade in Erwägung. [Lesen Sie]() die Informationen zum [Vorbereiten von Computern mit Windows Server 2003](prepare-windows-server-2003-migration.md) auf die Migration zu Azure.
Nicht unterstütztes Windows-Betriebssystem | Azure unterstützt nur [ausgewählte Windows-Betriebssystemversionen](/troubleshoot/azure/virtual-machines/server-software-support). Ziehen Sie vor der Migration zu Azure ein Upgrade des Computers in Erwägung.
Bedingt unterstütztes Linux-Betriebssystem | Azure unterstützt nur [ausgewählte Linux-Betriebssystemversionen](../virtual-machines/linux/endorsed-distros.md). Ziehen Sie vor der Migration zu Azure ein Upgrade des Computers in Erwägung. Weitere Informationen finden Sie auch [hier](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment).
Nicht unterstütztes Linux-Betriebssystem | Der Computer startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. Ziehen Sie vor der Migration zu Azure ein Upgrade auf eine [unterstützte Linux-Version](../virtual-machines/linux/endorsed-distros.md) in Erwägung.
Unbekanntes Betriebssystem | Das Betriebssystem der VM wurde in vCenter Server als „Sonstige“ angegeben. Dieses Verhalten hindert Azure Migrate daran, die Azure-Bereitschaft der VM zu überprüfen. Stellen Sie sicher, dass das Betriebssystem von Azure [unterstützt](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) wird, bevor Sie den Computer migrieren.
Nicht unterstützte Bitversion | Virtuelle Computer mit einem 32-Bit-Betriebssystem starten zwar unter Umständen in Azure, es empfiehlt sich jedoch, vor der Migration zu Azure ein Upgrade auf 64-Bit durchzuführen.
Erfordert ein Microsoft Visual Studio-Abonnement. | Auf dem Computer wird ein Windows-Clientbetriebssystem ausgeführt, das nur mit einem Visual Studio-Abonnement unterstützt wird.
Kein virtueller Computer für erforderliche Speicherleistung gefunden. | Die erforderliche Speicherleistung (Eingabe/Ausgabe-Vorgänge pro Sekunde (IOPS) und Durchsatz) für den Computer überschreitet die von virtuellen Azure-Computern unterstützte Leistung. Reduzieren Sie vor der Migration die Speicheranforderungen für den Computer.
Kein virtueller Computer für erforderliche Netzwerkleistung gefunden. | Die erforderliche Netzwerkleistung (ein-/ausgehend) für den Computer überschreitet die von virtuellen Azure-Computern unterstützte Leistung. Reduzieren Sie die Netzwerkanforderungen für den Computer.
Kein virtueller Computer am angegebenen Speicherort gefunden. | Geben Sie vor der Migration einen anderen Zielort an.
Mindestens ein Datenträger ist ungeeignet. | Mindestens ein an den virtuellen Computer angeschlossener Datenträger erfüllt die Azure-Anforderungen nicht.<br/><br/> Von der Azure Die Serverbewertung unterstützt derzeit keine SSD Ultra-Datenträger und bewertet die Datenträger anhand der Datenträgergrenzwerte für verwaltete Premium-Datenträger (32 TB).<br/><br/> Stellen Sie für jeden an den virtuellen Computer angeschlossenen Datenträger sicher, dass die Größe des Datenträgers kleiner als 64 TB (von SSD Ultra-Datenträgern unterstützt) ist.<br/><br/> Wenn dies nicht der Fall ist, reduzieren Sie die Datenträgergröße vor der Migration zu Azure, oder verwenden Sie in Azure mehrere Datenträger, und [kombinieren Sie sie in Stripesets](../virtual-machines/premium-storage-performance.md#disk-striping), um höhere Speichergrenzwerte zu erzielen. Stellen Sie sicher, dass die von jedem Datenträger benötigte Leistung (IOPS und Durchsatz) von [verwalteten Azure-Datenträgern virtueller Computer](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) unterstützt wird.
Mindestens ein Netzwerkadapter ist ungeeignet. | Entfernen Sie nicht verwendete Netzwerkadapter vor der Migration vom Computer.
Anzahl der Datenträger überschreitet den Grenzwert | Entfernen Sie nicht verwendete Datenträger vor der Migration vom Computer.
Datenträgergröße überschreitet den Grenzwert | Von der Azure Die Serverbewertung unterstützt derzeit keine SSD Ultra-Datenträger und bewertet die Datenträger anhand der Grenzwerte für Premium-Datenträger (32 TB).<br/><br/> Azure unterstützt jedoch Datenträger mit einer Größe von bis zu 64 TB (von SSD Ultra-Datenträgern unterstützt). Reduzieren Sie die Datenträgergröße vor der Migration auf weniger als 64 TB, oder verwenden Sie mehrere Datenträger in Azure, und [kombinieren Sie sie in Stripesets](../virtual-machines/premium-storage-performance.md#disk-striping), um höhere Speichergrenzwerte zu erzielen.
Datenträger am angegebenen Speicherort nicht verfügbar | Stellen Sie vor der Migration sicher, dass sich der Datenträger am Zielspeicherort befindet.
Datenträger für die angegebene Redundanz nicht verfügbar | Der Datenträger muss den in den Bewertungseinstellungen definierten Redundanzspeichertyp verwenden (standardmäßig LRS).
Datenträgereignung konnte aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Kein virtueller Computer gefunden, der die Kern- und Arbeitsspeicheranforderungen erfüllt | Azure konnte keinen geeigneten VM-Typ finden. Reduzieren Sie vor der Migration den Arbeitsspeicher und die Anzahl der Kerne auf dem lokalen Computer.
Eignung des virtuellen Computers konnte aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Eignung konnte für mindestens einen Datenträger aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Eignung konnte für mindestens einen Netzwerkadapter aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Es wurde keine VM-Größe für die Angebot/Währung/reservierte Instanz gefunden. | Der Computer ist als „Nicht geeignet“ gekennzeichnet, weil die VM-Größe für die ausgewählte Kombination aus RI, Angebot und Währung nicht gefunden wurde. Bearbeiten Sie die Bewertungseigenschaften, um die gültigen Kombinationen auszuwählen und die Bewertung neu zu berechnen. 
Internetprotokoll mit bedingter Bereitschaft | Nur verfügbar für Bewertungen vom Typ „VMware-Lösung in Azure (AVS)“. Der Faktor der IPv6-Internetadressierung wird von AVS nicht unterstützt.  Wenden Sie sich an das AVS-Team, um eine Anleitung zur Abhilfe zu erhalten, falls Ihr Computer mit IPv6 erkannt wird.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Vorgeschlagenes Migrationstool in der importbasierten AVS-Bewertung als unbekannt markiert

Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool in einer AVS-Bewertung unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension). [Weitere Informationen](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux-VMs sind in einer Azure-VM-Bewertung „bedingt bereit“

Bei VMware- und Hyper-V-VMs werden virtuelle Linux-Computer aufgrund einer bekannten Lücke in der Serverbewertung als „bedingt bereit“ gekennzeichnet. 

- Die Lücke verhindert die Erkennung der Nebenversion des Linux-Betriebssystems, das auf den lokalen virtuellen Computern installiert ist.
- Beispielsweise erkennt die Serverbewertung bei RHEL 6.10 derzeit nur RHEL 6 als Betriebssystemversion. Dies liegt daran, dass die vCenter Server-Instanz und der Hyper-V-Host die Kernel-Version für Linux-VM-Betriebssysteme nicht bereitstellen.
-  Da Azure nur bestimmte Versionen von Linux unterstützt, werden die virtuellen Linux-Computer in der Serverbewertung derzeit als „bedingt bereit“ markiert.
- Sie können ermitteln, ob das Linux-Betriebssystem des lokalen virtuellen Computers für Azure geeignet ist. Informationen hierzu finden Sie unter [Linux-Unterstützung von Azure](../virtual-machines/linux/endorsed-distros.md).
-  Nachdem Sie die unterstützte Distribution überprüft haben, können Sie diese Warnung ignorieren.

Diese Lücke kann behoben werden, indem Sie [Anwendungsermittlung-](./how-to-discover-applications.md) auf den VMware-VMs aktivieren. Bei der Serverbewertung wird das von der VM erkannte Betriebssystem mit den bereitgestellten Gastanmeldeinformationen verwendet. Mit diesen Betriebssystemdaten werden die richtigen Betriebssysteminformationen im Fall von virtuellen Windows- und Linux-Computern identifiziert.

## <a name="operating-system-version-not-available"></a>Betriebssystemversion nicht verfügbar

Bei physischen Servern sollten Informationen zur Nebenversion des Betriebssystems verfügbar sein. Wenn dies nicht der Fall ist, wenden Sie sich an den Microsoft-Support. Bei VMware-VMs verwendet die Serverbewertung die in vCenter Server für die VM angegebenen Betriebssysteminformationen. Allerdings gibt vCenter Server nicht die Nebenversion für Betriebssysteme an. Zum Ermitteln der Nebenversion müssen Sie die [Anwendungsermittlung](./how-to-discover-applications.md) einrichten. Bei Hyper-V-VMs wird die Ermittlung der Betriebssystem-Nebenversion nicht unterstützt. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure-SKUs größer als lokal in einer Azure-VM-Bewertung

Je nach Art der Bewertung empfiehlt die Azure Migrate-Serverbewertung möglicherweise Azure-VM-SKUs mit mehr Kernen und Arbeitsspeicher als bei der aktuellen lokalen Zuordnung:

- Die Empfehlung für VM-SKUs richtet sich nach den Bewertungseigenschaften.
- Dies hängt von der Art der Bewertung ab, die Sie in der Serverbewertung ausführen: *Leistungsbasiert* oder *Wie lokal*.
- Bei leistungsbasierten Bewertungen berücksichtigt die Serverbewertung die Nutzungsdaten der lokalen virtuellen Computer (CPU-, Speicher-, Datenträger- und Netzwerkauslastung), um die richtige Ziel-VM-SKU für Ihre lokalen virtuellen Computer zu bestimmen. Außerdem wird bei der Bestimmung der effektiven Auslastung ein Komfortfaktor hinzugefügt.
- Bei der lokalen Dimensionierung werden Leistungsdaten nicht berücksichtigt, und die Ziel-SKU wird auf der Grundlage der lokalen Zuordnung empfohlen.

Um zu veranschaulichen, wie sich dies auf Empfehlungen auswirken kann, sehen wir uns ein Beispiel an:

Wir haben einen lokalen virtuellen Computer mit vier Kernen und 8 GB Arbeitsspeicher, mit einer CPU- und Speicherauslastung von jeweils 50 % und einem angegebenen Komfortfaktor von 1,3.

-  Beim Bewertungstyp **Wie lokal** wird eine Azure-VM-SKU mit vier Kernen und 8 GB Arbeitsspeicher empfohlen.
- Bei der leistungsbasierten Bewertung würde auf Basis der effektiven CPU- und Speicherauslastung (50 % von 4 Kernen * 1,3 = 2,6 Kerne und 50 % von 8 GB Arbeitsspeicher * 1,3 = 5,3 GB Arbeitsspeicher) die günstigste VM-SKU mit vier Kernen (nächste unterstützte Kernanzahl) und 8 GB Arbeitsspeichergröße (nächste unterstützte Arbeitsspeichergröße) empfohlen.
- Weitere Informationen zur Dimensionierung bei Bewertungen finden Sie [hier](concepts-assessment-calculation.md#types-of-assessments).

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Warum sind die empfohlenen Azure-Datenträger-SKUs größer als lokal in einer Azure-VM-Bewertung?

Die Azure Migrate-Serverbewertung empfiehlt möglicherweise je nach Art der Bewertung einen größeren Datenträger.
- Die Datenträgerdimensionierung in der Serverbewertung hängt von zwei Bewertungseigenschaften ab: dem Dimensionierungskriterium und dem Speichertyp.
- Wenn das Dimensionierungskriterium **Leistungsbasiert** ist und der Speichertyp **Automatisch** lautet, werden bei der Ermittlung des Zieldatenträgertyps (HDD Standard, SSD Standard oder Premium) die IOPS- und Durchsatzwerte des Datenträgers berücksichtigt. Eine Datenträger-SKU dieses Datenträgertyps wird dann unter Berücksichtigung der Größenanforderungen des lokalen Datenträgers empfohlen.
- Wenn das Dimensionierungskriterium **Leistungsbasiert** ist und der Speichertyp **Premium** lautet, wird eine Premium-Datenträger-SKU in Azure basierend auf den IOPS, dem Durchsatz und den Größenanforderungen des lokalen Datenträgers empfohlen. Die gleiche Logik wird verwendet, um die Datenträgerdimensionierung durchzuführen, wenn das Dimensionierungskriterium **Wie lokal** verwendet wird und der Speichertyp **HDD Standard**, **SSD Standard** oder **Premium** lautet.

Wenn Sie beispielsweise einen lokalen Datenträger mit 32 GB Speicher haben, die aggregierten Lese- und Schreib-IOPS für den Datenträger aber 800 IOPS sind, empfiehlt die Serverbewertung einen Premium-Datenträger (aufgrund der höheren IOPS-Anforderungen) und dann eine Datenträger-SKU, welche die erforderlichen IOPS und die Größe unterstützen kann. Die nächstliegende Übereinstimmung in diesem Beispiel wäre P15 (256 GB, 1100 IOPS). Für den lokalen Datenträger ist also nur eine Größe von 32 GB erforderlich, von der Serverbewertung wurde jedoch aufgrund der hohen IOPS-Anforderungen des lokalen Datenträgers ein größerer Datenträger empfohlen.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Warum fehlen in meinem Bewertungsbericht die Leistungsdaten für einige oder alle VMs?

Für eine Bewertung vom Typ „Leistungsbasiert“ enthält der Bewertungsbericht „PercentageOfCoresUtilizedMissing“ oder „PercentageOfMemoryUtilizedMissing“, wenn die Azure Migrate-Appliance keine Leistungsdaten für die lokalen virtuellen Computer sammeln kann. Überprüfen Sie Folgendes:

- Ob die VMs im Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet sind
- Falls nur Arbeitsspeicher-Leistungsindikatoren fehlen und Sie versuchen, virtuelle Hyper-V-Computer zu bewerten, überprüfen Sie, ob auf diesen virtuellen Computern dynamischer Arbeitsspeicher aktiviert ist. Die Azure Migrate-Appliance kann derzeit aufgrund eines bekannten Problems keine Arbeitsspeicherauslastung für virtuelle Computer dieser Art erfassen.
- Wenn alle Leistungsindikatoren fehlen, stellen Sie sicher, dass die Portzugriffsanforderungen für die Bewertung erfüllt sind. Erfahren Sie mehr über die Portzugriffsanforderungen für [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)-, [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access)- und [physische](./migrate-support-matrix-physical.md#port-access) Serverbewertung.
Hinweis: Falls einer der Leistungsindikatoren fehlt, greift die Azure Migrate-Serverbewertung auf die lokalen zugeordneten Kerne bzw. den zugeordneten Arbeitsspeicher zurück und empfiehlt eine entsprechende VM-Größe.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Weshalb weist meine Bewertung eine niedrige Zuverlässigkeitsstufe auf?

Die Zuverlässigkeitsstufe wird für Bewertungen vom Typ „Leistungsbasiert“ anhand des Prozentsatzes von [verfügbaren Datenpunkten](./concepts-assessment-calculation.md#ratings) berechnet, die zum Berechnen der Bewertung benötigt werden. Im Folgenden werden mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung aufgeführt:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil Ihrer Umgebung erstellt. Wenn Sie beispielsweise eine Bewertung mit einer auf eine Woche festgelegten Leistungsdauer erstellen, müssen Sie nach dem Start der Ermittlung mindestens eine Woche warten, damit alle Datenpunkte erfasst werden können. Falls Sie nicht so lange warten können, sollten Sie die Leistungsdauer in einen kürzeren Zeitraum ändern und die Bewertung neu berechnen (Option „Neu berechnen“).
 
- Die Serverbewertung kann die Leistungsdaten für einige oder alle VMs im Bewertungszeitraum nicht erfassen. Überprüfen Sie, ob die VMs während der Dauer der Bewertung eingeschaltet und ausgehende Verbindungen über Port 443 zulässig waren. Wenn bei Verwendung von Hyper-V-VMs der dynamische Arbeitsspeicher aktiviert ist, fehlen Arbeitsspeicher-Leistungsindikatoren. Dies führt zu einer niedrigen Zuverlässigkeitsstufe. Führen Sie die erneute Berechnung der Bewertung mit der Option „Neu berechnen“ durch, um die neuesten Änderungen an der Zuverlässigkeitsstufe widerzuspiegeln. 

- Nach dem Start der Ermittlung in der Serverbewertung wurden wenige VMs erstellt. Ein Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, und in der Umgebung wurden letzte Woche einige virtuelle Computer erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen VMs zur Verfügung und die Zuverlässigkeitsstufe wäre gering.

[Weitere Informationen](./concepts-assessment-calculation.md#confidence-ratings-performance-based) zur Zuverlässigkeitsstufe.

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Ist die Betriebssystemlizenz in einer Azure-VM-Bewertung inbegriffen?

Die Azure Migrate-Serverbewertung berücksichtigt zurzeit die Kosten einer Betriebssystemlizenz nur für Windows-Computer. Die Lizenzkosten für Linux-Computer werden derzeit nicht berücksichtigt.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Wie funktioniert die leistungsbasierte Größenanpassung in einer Azure-VM-Bewertung?

Die Serverbewertung sammelt fortlaufend Leistungsdaten von lokalen Computern und nutzt diese Daten, um die VM-SKU und die Datenträger-SKU in Azure zu empfehlen. [Erfahren Sie](concepts-assessment-calculation.md#calculate-sizing-performance-based), wie leistungsbasierten Daten gesammelt werden.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Warum zeigt meine Bewertung eine Warnung an, dass sie mit einer ungültigen Kombination von reservierten Instanzen, VM-Betriebszeit und Rabatt (%) erstellt wurde?
Wenn Sie „Reservierte Instanzen“ auswählen, sind die „Rabatt (%)“- und „VM-Betriebszeit“-Eigenschaften nicht anwendbar. Da Ihre Bewertung mit einer ungültigen Kombination dieser Eigenschaften erstellt wurde, sind die Schaltflächen „Bearbeiten“ und „Neu berechnen“ deaktiviert. Erstellen Sie eine neue Bewertung. [Weitere Informationen](./concepts-assessment-calculation.md#whats-an-assessment)

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Ich sehe keine Leistungsdaten für einige Netzwerkadapter auf meinen physischen Servern.

Dies kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Auf diesen Servern werden aufgrund einer Produktlücke von Azure Migrate derzeit sowohl der physische als auch der virtuelle Netzwerkadapter ermittelt. Der Netzwerkdurchsatz wird nur auf den erkannten virtuellen Netzwerkadaptern aufgezeichnet.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Die empfohlene Azure-VM-SKU für meinen physischen Server ist überdimensioniert

Dies kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Auf diesen Servern werden von Azure Migrate derzeit sowohl der physische als auch der virtuelle Netzwerkadapter ermittelt. Daher ist die Anzahl der ermittelten Netzwerkadapter höher als die tatsächliche Anzahl. Wählt die Serverbewertung eine Azure-VM aus, die die erforderliche Anzahl von Netzwerkadaptern unterstützen kann, führt dies möglicherweise zu einer überdimensionierten VM. [Weitere Informationen](./concepts-assessment-calculation.md#calculating-sizing) über den Einfluss der Anzahl der Netzwerkadapter auf die Dimensionierung. Dies ist eine Produktlücke, die in Zukunft behoben wird.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Bereitschaftskategorie „Nicht bereit“ für meinen physischen Server

Die Bereitschaftskategorie wird im Falle eines physischen Servers mit aktivierter Hyper-V-Virtualisierung möglicherweise fälschlicherweise als „Nicht bereit“ gekennzeichnet. Auf diesen Servern werden aufgrund einer Produktlücke von Azure Migrate derzeit sowohl der physische als auch der virtuelle Adapter ermittelt. Daher ist die Anzahl der ermittelten Netzwerkadapter höher als die tatsächliche Anzahl. Sowohl bei lokalen als auch leistungsbezogenen Bewertungen wählt die Serverbewertung eine Azure-VM aus, die die erforderliche Anzahl von Netzwerkadaptern unterstützen kann. Wenn festgestellt wird, dass die Anzahl der Netzwerkadapter höher als die maximale Anzahl von NICs ist, die von Azure VMs unterstützt werden (32), wird der Rechner als „Nicht bereit“ gekennzeichnet.  [Weitere Informationen](./concepts-assessment-calculation.md#calculating-sizing) über den Einfluss der Anzahl der NICs auf die Dimensionierung.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Anzahl der ermittelten NICs höher als die tatsächliche Anzahl der physischen Server

Dies kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Auf diesen Servern werden von Azure Migrate derzeit sowohl der physische als auch der virtuelle Adapter ermittelt. Daher ist die Anzahl der ermittelten NICs höher als die tatsächliche Anzahl.

## <a name="dependency-visualization-in-azure-government"></a>Abhängigkeitsvisualisierung in Azure Government

Die Agent-basierte Abhängigkeitsanalyse wird in Azure Government nicht unterstützt. Sie können die Abhängigkeitsanalyse ohne Agent verwenden.


## <a name="dependencies-dont-show-after-agent-install"></a>Abhängigkeiten werden nicht nach der Installation von Agents angezeigt

Nachdem Sie die Agents für die Abhängigkeitsvisualisierung auf lokalen virtuellen Computern installiert haben, benötigt Azure Migrate üblicherweise 15 bis 30 Minuten, um die Abhängigkeiten im Portal anzuzeigen. Stellen Sie nach einer Wartezeit von mehr als 30 Minuten sicher, dass Microsoft Monitoring Agent (MMA) eine Verbindung zum Log Analytics-Arbeitsbereich herstellen kann.

Für virtuelle Windows-Computer:
1. Starten Sie MMA in der Systemsteuerung.
2. Stellen Sie unter **Microsoft Monitoring Agent-Eigenschaften** > **Azure Log Analytics (OMS)** sicher, dass der **Status** für den Arbeitsbereich grün angezeigt wird.
3. Wenn der Status nicht grün angezeigt wird, entfernen Sie den Arbeitsbereich, und fügen Sie ihn erneut zu MMA hinzu.

    ![MMA-Status](./media/troubleshoot-assessment/mma-properties.png)

Stellen Sie bei virtuellen Linux-Computern sicher, dass die Installationsbefehle für MMA und den Abhängigkeits-Agent erfolgreich ausgeführt wurden. Weitere Informationen zur Problembehandlung finden Sie [hier](../azure-monitor/insights/service-map.md#post-installation-issues).

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

- **MMS-Agent**: Überprüfen Sie die unterstützten [Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems)- und [Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems)-Betriebssysteme.
- **Dependency-Agent**: die unterstützten [Windows- und Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)-Betriebssysteme.

## <a name="visualize-dependencies-for--hour"></a>Visualisieren von Abhängigkeiten für > Stunde

Mit der Abhängigkeitsanalyse ohne Agent können Sie Abhängigkeiten für eine Dauer von bis zu 30 Tagen visualisieren oder in eine Karte exportieren.

Obwohl Sie bei der Abhängigkeitsanalyse ohne Agent mit Azure Migrate um bis zu einen Monat zu einem bestimmten Datum zurückgehen können, können Sie die Abhängigkeiten jedoch nur für einen Zeitraum von maximal einer Stunde visualisieren. So können Sie beispielsweise mithilfe der Zeitraumfunktionalität im Abhängigkeitsdiagramm Abhängigkeiten für den gestrigen Tag, jedoch nur für einen Zeitraum von einer Stunde, anzeigen. Sie können jedoch zum [Abfragen der Abhängigkeitsdaten](./how-to-create-group-machine-dependencies.md) über einen längeren Zeitraum Azure Monitor-Protokolle verwenden.

## <a name="visualized-dependencies-for--10-machines"></a>Visualisieren von Abhängigkeiten für > 10 Computer

Bei der Azure Migrate-Serverbewertung mit der Abhängigkeitsanalyse ohne Agent können Sie [Abhängigkeiten für Gruppen](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) mit bis zu 10 virtuellen Computern visualisieren. Bei größeren Gruppen empfiehlt es sich für die visuelle Darstellung von Abhängigkeiten, die virtuellen Computer in kleinere Gruppen aufzuteilen.


## <a name="machines-show-install-agent"></a>Computer zeigen „Agent installieren“ an

Nach dem Migrieren von Computern mit aktivierter Abhängigkeitsvisualisierung zu Azure zeigen Computer möglicherweise aufgrund des folgenden Verhaltens die Aktion „Agent installieren“ anstelle von „Abhängigkeiten anzeigen“ an:

- Nach der Migration zu Azure werden die lokalen Computer ausgeschaltet, und die entsprechenden virtuellen Computer werden in Azure hochgefahren. Diese Computer beziehen eine andere MAC-Adresse.
- Je nachdem, ob Sie die lokale IP-Adresse beibehalten haben oder nicht, haben Computer möglicherweise auch eine andere IP-Adresse.
- Wenn sich sowohl Mac- als auch IP-Adressen von den lokalen Adressen unterscheiden, verknüpft Azure Migrate die lokalen Computer nicht mit Service Map-Abhängigkeitsdaten. In diesem Fall wird die Option zum Installieren des Agents anstatt die Option zum Anzeigen von Abhängigkeiten angezeigt.
- Nach einer Testmigration zu Azure bleiben die lokalen Computer erwartungsgemäß eingeschaltet. Entsprechende Computer, die in Azure hochgefahren wurden, erhalten andere MAC-Adressen und u. U. auch andere IP-Adressen. Sofern Sie ausgehenden Datenverkehr von Azure Monitor-Protokollen von diesen Computern nicht sperren, ordnet Azure Migrate den lokalen Computern keine Service Map-Abhängigkeitsdaten zu und zeigt daher die Option zum Installieren von Agents statt der Option zum Anzeigen von Abhängigkeiten an.

## <a name="dependencies-export-csv-shows-unknown-process"></a>Abhängigkeitenexport-CSV zeigt „Unbekannter Prozess“ an
Bei der Abhängigkeitsanalyse ohne Agent werden die Prozessnamen auf Grundlage der besten Leistung aufgezeichnet. In bestimmten Szenarien ist es nicht möglich, die Prozessnamen an beiden Enden der Abhängigkeit zu ermitteln, obwohl der Quell- und Zielservername sowie der Zielport aufgezeichnet werden. In solchen Fällen wird der Prozess als „Unbekannter Prozess“ gekennzeichnet.

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>Beim Versuch, den Log Analytics-Arbeitsbereich in der Serverbewertung zu konfigurieren, wird der Arbeitsbereich nicht aufgeführt
Azure Migrate unterstützt derzeit die Erstellung von OMS-Arbeitsbereichen in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“. Wenn der Arbeitsbereich außerhalb von Azure Migrate in einer beliebigen anderen Region erstellt wird, kann er derzeit keinem Azure Migrate-Projekt zugeordnet werden.


## <a name="capture-network-traffic"></a>Erfassen des Netzwerkdatenverkehrs

Sammeln Sie Protokolle zum Netzwerkdatenverkehr wie folgt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Drücken Sie F12, um die Entwicklertools zu starten. Deaktivieren Sie ggf. die Einstellung **Einträge beim Navigieren löschen**.
3. Wählen Sie die Registerkarte **Netzwerk** aus, und starten Sie die Erfassung von Netzwerkdatenverkehr:
   - Wählen Sie in Chrome die Option **Protokoll speichern**. Die Aufzeichnung sollte automatisch gestartet werden. Ein roter Kreis gibt an, dass der Datenverkehr erfasst wird. Wenn der rote Kreis nicht angezeigt wird, wählen Sie zum Starten den schwarzen Kreis aus.
   - In Microsoft Edge und Internet Explorer sollte die Aufzeichnung automatisch gestartet werden. Ist dies nicht der Fall, wählen Sie die grüne Wiedergabeschaltfläche aus.
4. Versuchen Sie, den Fehler zu reproduzieren.
5. Nachdem der Fehler während der Aufzeichnung aufgetreten ist, beenden Sie die Aufzeichnung, und speichern Sie eine Kopie der aufgezeichneten Aktivität:
   - Klicken Sie in Chrome mit der rechten Maustaste, und wählen Sie **Als HAR mit Inhalt speichern** aus. Durch diese Aktion werden die Protokolle komprimiert und als HAR-Datei exportiert.
   - Wählen Sie in Microsoft Edge oder Internet Explorer das Symbol **Aufgezeichneten Datenverkehr exportieren** aus. Durch diese Aktion werden die Protokolle komprimiert und exportiert.
6. Wählen Sie die Registerkarte **Konsole** aus, um sie auf Warnungen oder Fehler zu überprüfen. So speichern Sie das Konsolenprotokoll:
   - Klicken Sie in Chrome mit der rechten Maustaste auf eine beliebige Stelle im Konsolenprotokoll. Wählen Sie **Speichern unter** aus, um das Protokoll zu exportieren und zu komprimieren.
   - Klicken Sie in Microsoft Edge oder Internet Explorer mit der rechten Maustaste auf die Fehler, und wählen Sie **Alle kopieren** aus.
7. Schließen Sie die Entwicklertools.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Woher werden die Betriebssystemdaten in meiner Bewertung ermittelt?

- Bei VMware-VMs handelt es sich standardmäßig um die von vCenter bereitgestellten Betriebssystemdaten. 
   - Bei VMware-Linux-VMs werden die Betriebssystemdetails von der Gast-VM abgerufen, wenn die Anwendungsermittlung aktiviert ist. Um zu überprüfen, welche Betriebssystemdetails in der Bewertung enthalten sind, navigieren Sie zur Ansicht „Ermittelte Server“, und fahren Sie mit der Maus über den Wert in der Spalte „Betriebssystem“. In dem Text, der angezeigt wird, können Sie sehen, ob die Betriebssystemdaten von der vCenter Server-Instanz oder der Gast-VM unter Verwendung der VM-Anmeldeinformationen gesammelt werden. 
   - Bei virtuellen Windows-Computern werden die Betriebssystemdetails immer von der vCenter Server-Instanz abgerufen.
- Bei Hyper-V-VMS werden die Betriebssystemdaten vom Hyper-V-Host erfasst.
- Für physische Server werden sie vom Server abgerufen.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen](how-to-create-assessment.md) Sie eine Bewertung oder [passen](how-to-modify-assessment.md) Sie sie an.