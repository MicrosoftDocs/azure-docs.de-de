---
title: Übersicht über Azure Disk Storage
description: Hier finden Sie eine Übersicht über verwaltete Azure-Datenträger. Damit werden bei Verwendung von virtuellen Computern die Speicherkonten für Sie verwaltet.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: 31fe62aabfff77342726158c3b72b61afb518682
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031520"
---
# <a name="introduction-to-azure-managed-disks"></a>Einführung in verwaltete Azure-Datenträger

Verwaltete Azure-Datenträger sind Speichervolumes auf Blockebene, die von Azure verwaltet und mit Azure-Virtual Machines verwendet werden. Verwaltete Datenträger sind wie physische Datenträger in einem lokalen Server, aber der Unterschied besteht darin, dass sie virtualisiert sind. Bei verwalteten Datenträgern müssen Sie lediglich die Datenträgergröße sowie den Datenträgertyp angeben und den Datenträger bereitstellen. Nachdem Sie den Datenträger bereitgestellt haben, übernimmt Azure den Rest.

Die verfügbaren Datenträgertypen sind Ultra-Datenträger, SSD Premium, SSD Standard und Standard Hard Disk Drives (HDD). Informationen zu den einzelnen Datenträgertypen finden Sie unter [Auswählen eines Datenträgertyps für virtuelle IaaS-Computer](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Vorteile von verwalteten Datenträgern

Hier werden einige Vorteile beschrieben, in deren Genuss Sie bei der Verwendung von verwalteten Datenträgern kommen.

### <a name="highly-durable-and-available"></a>Hoch verfügbar und stabil

Verwaltete Datenträger sind auf eine Verfügbarkeit von 99,999% ausgelegt. Hierzu stellen verwaltete Datenträger drei Replikate Ihrer Daten für Sie bereit, um eine hohe Dauerhaftigkeit zu garantieren. Wenn bei einem oder sogar bei zwei Ihrer Replikate Probleme auftreten, stellen Sie mit den übrigen Replikaten die Persistenz Ihrer Daten und eine hohe Fehlertoleranz sicher. Mit dieser Architektur konnte Azure für Infrastructure-as-a-Service-Datenträger (IaaS) durchgängig eine Dauerhaftigkeit auf Unternehmensniveau mit einer branchenweit führenden auf das Jahr umgerechneten Fehlerrate von 0% bereitstellen.

### <a name="simple-and-scalable-vm-deployment"></a>Einfache und skalierbare VM-Bereitstellung

Mit verwalteten Datenträgern können Sie für ein Abonnement pro Region bis zu 50.000VM-**Datenträger** eines Typs erstellen – Sie können also Tausende von **VMs** in einem einzigen Abonnement erstellen. Außerdem wird mit diesem Feature die Skalierbarkeit von [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md) weiter erhöht, sodass Sie mit einem Marketplace-Image bis zu 1.000VMs in einer VM-Skalierungsgruppe erstellen können.

### <a name="integration-with-availability-sets"></a>Integration in Verfügbarkeitsgruppen

Verwaltete Datenträger werden in Verfügbarkeitsgruppen integriert, um sicherzustellen, dass die Datenträger von [VMs in einer Verfügbarkeitsgruppe](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) ausreichend voneinander isoliert sind, um einzelne Fehlerquellen zu vermeiden. Datenträger werden automatisch in unterschiedliche Speicherskalierungseinheiten („Stamps“) platziert. Wenn ein Stamp aufgrund eines Hardware- oder Softwarefehlers ausfällt, treten nur für die VM-Instanzen mit Datenträgern auf diesen Stamps Fehler auf. Angenommen, Sie führen eine Anwendung auf fünf VMs aus, und die VMs sind in einer Verfügbarkeitsgruppe enthalten. Die Datenträger für diese VMs werden nicht alle auf demselben Stamp gespeichert, damit die anderen Instanzen der Anwendung weiter ausgeführt werden, wenn ein Stamp ausfällt.

### <a name="integration-with-availability-zones"></a>Integration in Verfügbarkeitszonen

Verwaltete Datenträger unterstützen [Verfügbarkeitszonen](../availability-zones/az-overview.md). Diese sind ein Hochverfügbarkeitsangebot, das Anwendungen vor Ausfällen von Rechenzentren schützt. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Mit Verfügbarkeitszonen bietet Azure die branchenweit beste Betriebszeit-SLA von 99,99 % für VMs.

### <a name="azure-backup-support"></a>Azure Backup-Unterstützung

Zum Schutz vor regionalen Katastrophen kann mit [Azure Backup](../backup/backup-overview.md) ein Sicherungsauftrag mit zeitbasierten Sicherungen und Richtlinien zur Sicherungsaufbewahrung erstellt werden. So können Sie nach Belieben Wiederherstellungen von virtuellen Computern oder verwalteten Datenträgern ausführen. Azure Backup unterstützt derzeit Datenträgergrößen von bis zu 32 Tebibyte (TiB). [Weitere Informationen](../backup/backup-support-matrix-iaas.md) zur Unterstützung der Azure-VM-Sicherung.

### <a name="granular-access-control"></a>Genau abgestimmte Zugriffssteuerung

Sie können die [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/overview.md) verwenden, um die spezifischen Berechtigungen für einen verwalteten Datenträger einem oder mehreren Benutzern zuzuweisen. Verwaltete Datenträger bieten viele verschiedene Vorgänge, z.B. Lesen, Schreiben (Erstellen/Aktualisieren), Löschen und Abrufen eines [SAS-URI (Shared Access Signature)](../storage/common/storage-sas-overview.md) für den Datenträger. Sie haben die Möglichkeit, Personen nur Zugriff auf die Vorgänge zu gewähren, die sie jeweils benötigen, um ihre Aufgaben zu erledigen. Wenn Sie es für eine Person beispielsweise nicht zulassen möchten, dass sie einen verwalteten Datenträger auf ein Speicherkonto kopiert, können Sie festlegen, dass der Zugriff auf die Exportaktion für diesen verwalteten Datenträger nicht gewährt wird. Wenn Sie nicht möchten, dass eine Person einen SAS-URI zum Kopieren eines verwalteten Datenträgers verwendet, können Sie auch festlegen, dass diese Berechtigung für den verwalteten Datenträger nicht gewährt wird.

### <a name="upload-your-vhd"></a>Hochladen der VHD

Mit dem direkten Upload ist es einfach, Ihre VHD auf einen verwalteten Azure-Datenträger zu übertragen. Bisher war die Übertragung Ihrer Daten eher umständlich und umfasste das Staging der Daten in einem Speicherkonto. Jetzt sind weniger Schritte erforderlich. Es ist einfacher, lokale VMs in Azure hochzuladen (Upload auf große verwaltete Datenträger), und der Sicherungs- und Wiederherstellungsvorgang wurde vereinfacht. Außerdem werden hierdurch die Kosten gesenkt, weil Sie Daten direkt auf verwaltete Datenträger hochladen können, ohne diese an VMs anfügen zu müssen. Sie können direkte Uploads nutzen, um VHDs mit einer Größe von bis zu 32 TiB hochzuladen.

Informationen zur Übertragung Ihrer VHD in Azure finden Sie im Artikel zur [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) bzw. zu [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="security"></a>Sicherheit

### <a name="private-links"></a>Private Links

Die Private Link-Unterstützung für verwaltete Datenträger kann zum Importieren und Exportieren eines verwalteten Datenträgers innerhalb Ihres Netzwerks verwendet werden. Mit privaten Links können Sie einen zeitgebundenen SAS-URI (Shared Access Signature) für nicht angefügte verwaltete Datenträger und Momentaufnahmen generieren, um die Daten zur regionalen Erweiterung, zur Notfallwiederherstellung und für die forensische Analyse in eine andere Region zu exportieren. Sie können den SAS-URI auch verwenden, um eine VHD aus der lokalen Umgebung direkt auf einen leeren Datenträger hochzuladen. Jetzt können Sie [Private Links](../private-link/private-link-overview.md) verwenden, um den Export und Import von verwalteten Datenträgern einzuschränken, sodass er nur innerhalb Ihres virtuellen Azure-Netzwerks erfolgen kann. Mit privaten Links können Sie sicherstellen, dass Ihre Daten nur innerhalb des sicheren Microsoft-Backbone-Netzwerks übertragen werden.

Wie Sie private Links zum Importieren oder Exportieren eines verwalteten Datenträgers aktivieren können, erfahren Sie in den Artikeln zur [CLI](linux/disks-export-import-private-links-cli.md) oder zum [Portal](disks-enable-private-links-for-import-export-portal.md).

### <a name="encryption"></a>Verschlüsselung

Verwaltete Datenträger bieten zwei verschiedene Arten der Verschlüsselung. Die erste ist die serverseitige Verschlüsselung (Server Side Encryption, SSE), die vom Speicherdienst durchgeführt wird. Die zweite ist Azure Disk Encryption (ADE), die Sie für Datenträger für das Betriebssystem und die Daten Ihrer virtuellen Computer aktivieren können.

#### <a name="server-side-encryption"></a>Serverseitige Verschlüsselung

Die serverseitige Verschlüsselung bietet eine Verschlüsselung ruhender Daten und schützt Ihre Daten, um die Sicherheits- und Compliancevorgaben Ihrer Organisation zu erfüllen. Die serverseitige Verschlüsselung ist standardmäßig für alle verwalteten Datenträger, Momentaufnahmen und Images in allen Regionen aktiviert, in denen Managed Disks verfügbar ist. (Temporäre Datenträger werden dagegen nicht durch serverseitige Verschlüsselung verschlüsselt, es sei denn, Sie aktivieren die Verschlüsselung beim Host. Siehe [Datenträgerrollen: temporäre Datenträger](#temporary-disk)).

Sie können zulassen, dass Azure die Schlüssel für Sie verwaltet. Dies sind von der Plattform verwaltete Schlüssel. Alternativ können Sie die Schlüssel selbst verwalten. Dabei handelt es sich um vom Kunden verwaltete Schlüssel. Weitere Informationen finden Sie im Artikel [Serverseitige Verschlüsselung von Azure Disk Storage](./disk-encryption.md).


#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Mit Azure Disk Encryption können Sie die Betriebssystemdatenträger und andere Datenträger verschlüsseln, die von einem virtuellen IaaS-Computer verwendet werden. Diese Verschlüsselung umfasst verwaltete Datenträger. Unter Windows werden Laufwerke mit branchenüblicher BitLocker-Verschlüsselung verschlüsselt. Unter Linux werden Datenträger mit der DM-Crypt-Technologie verschlüsselt. Dieser Verschlüsselungsvorgang ist in Azure Key Vault integriert, damit Sie die Datenträger-Verschlüsselungsschlüssel steuern und verwalten können. Weitere Informationen finden Sie unter [Azure Disk Encryption für Linux-VMs](linux/disk-encryption-overview.md) und [Azure Disk Encryption für virtuelle Windows-Computer](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Datenträgerrollen

Es gibt drei Hauptdatenträgerrollen in Azure: der Datenträger, der BS-Datenträger und der temporäre Datenträger. Diese Rollen werden den Datenträgern zugeordnet, die an Ihren virtuelle Computer angefügt sind.

![Datenträgerrollen in Aktion](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Datenträger

Ein Datenträger für Daten ist ein verwalteter Datenträger, der zum Speichern von Anwendungsdaten oder anderen Daten, die Sie aufbewahren müssen, an einen virtuellen Computer angebunden ist. Datenträger werden als SCSI-Laufwerke registriert und mit einem von Ihnen ausgewählten Buchstaben gekennzeichnet. Jeder Datenträger weist eine maximale Kapazität von 32.767 GiB (Gibibyte) auf. Die Größe des virtuellen Computers bestimmt die Anzahl der Datenträger, die Sie anfügen können, und den Typ des Speichers, den Sie zum Hosten der Datenträger verwenden können.

### <a name="os-disk"></a>Betriebssystem-Datenträger

Jedem virtuellen Computer ist ein Betriebssystem-Datenträger zugeordnet. Dieser Betriebssystem-Datenträger verfügt über ein vorinstalliertes Betriebssystem, das beim Erstellen des virtuellen Computers ausgewählt wurde. Dieser Datenträger enthält das Startvolume.

Dieser Datenträger weist eine maximale Kapazität von 4.095 GiB auf.

### <a name="temporary-disk"></a>Temporärer Datenträger

Die meisten VMs enthalten einen temporären Datenträger, der kein verwalteter Datenträger ist. Der temporäre Datenträger bietet kurzfristigen Speicher für Anwendungen und Prozesse und ist ausschließlich dafür ausgelegt, Daten wie z. B. Seiten- oder Auslagerungsdateien zu speichern. Daten auf dem temporären Datenträger können während eines [Wartungsereignisses](./manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) verloren gehen, oder wenn Sie [eine VM erneut bereitstellen](troubleshooting/redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json). Während eines erfolgreichen standardmäßigen Neustarts der VM bleiben die Daten auf dem temporären Datenträger erhalten. Weitere Informationen zu VMs ohne temporäre Datenträger finden Sie unter [Azure-VM-Größen ohne lokalen temporären Datenträger](azure-vms-no-temp-disk.md).

Bei Azure Linux-VMs ist der temporäre Datenträger standardmäßig „/dev/sdb“, und bei Windows-VMs wird standardmäßig „D:“ verwendet. Der temporäre Datenträger wird nicht durch serverseitige Verschlüsselung verschlüsselt, es sei denn, Sie aktivieren die Verschlüsselung auf dem Host.

## <a name="managed-disk-snapshots"></a>Momentaufnahmen eines verwalteten Datenträgers

Bei einer Momentaufnahme eines verwalteten Datenträgers handelt es sich um eine absturzkonsistente, schreibgeschützte vollständige Kopie eines verwalteten Datenträgers, die standardmäßig als verwalteter Standarddatenträger gespeichert wird. Mit Momentaufnahmen können Sie Ihre verwalteten Datenträger jederzeit sichern. Diese Momentaufnahmen existieren unabhängig vom Quelldatenträger und können zum Erstellen neuer verwalteter Datenträger verwendet werden. 

Momentaufnahmen werden auf Basis der verwendeten Größe in Rechnung gestellt. Wenn Sie beispielsweise eine Momentaufnahme eines verwalteten Datenträgers mit einer bereitgestellten Kapazität von 64GiB und einer tatsächlichen Datengröße von 10GiB erstellen, wird die Momentaufnahme nur für die in Anspruch genommene Datengröße von 10GiB in Rechnung gestellt. Sie können die verwendete Größe Ihrer Momentaufnahmen im [Azure-Nutzungsbericht](../cost-management-billing/understand/review-individual-bill.md) ansehen. Beispiel: Beträgt die verwendete Datengröße einer Momentaufnahme 10 GiB, wird im **täglichen** Nutzungsbericht als verbrauchte Menge Folgendes angezeigt: 10 GiB/(31 Tage) = 0,3226.

Weitere Informationen zur Erstellung von Momentaufnahmen für verwaltete Datenträger finden Sie in den folgenden Ressourcen:

- [Erstellen einer Momentaufnahme](windows/snapshot-copy-managed-disk.md) (Windows)
- [Erstellen einer Momentaufnahme](linux/snapshot-copy-managed-disk.md) (Linux)

### <a name="images"></a>Bilder

Für verwaltete Datenträger wird auch die Erstellung eines verwalteten benutzerdefinierten Image unterstützt. Sie können ein Image aus Ihrer benutzerdefinierten VHD in einem Speicherkonto oder direkt von einer generalisierten VM (mit Systemvorbereitung) erstellen. Dieser Prozess erfasst ein einzelnes Image. Dieses Image enthält alle verwalteten Datenträger, die einer VM zugeordnet sind, einschließlich der Datenträger für das Betriebssystem und für die Daten. Dieses verwaltete benutzerdefinierte Image ermöglicht die Erstellung von Hunderten von VMs mit Ihrem benutzerdefinierten Image, ohne dass Sie Speicherkonten kopieren oder verwalten müssen.

Informationen zum Erstellen von Images finden Sie in den folgenden Artikeln:

- [How to capture a managed image of a generalized VM in Azure](windows/capture-image-resource.md) (Erstellen eines verwalteten Image eines generalisierten virtuellen Computers in Azure)
- [Generalisieren und Erfassen eines virtuellen Linux-Computers mithilfe der Azure CLI](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Vergleich von Images und Momentaufnahmen

Es ist wichtig, den Unterschied zwischen Images und Momentaufnahmen zu verstehen. Bei verwalteten Datenträgern können Sie ein Image einer generalisierten VM erstellen, deren Zuordnung aufgehoben wurde. Dieses Image umfasst alle Datenträger, die der VM angefügt sind. Sie können dieses Image verwenden, um eine VM mit allen Datenträgern zu erstellen.

Eine Momentaufnahme ist eine Kopie eines Datenträgers zum Zeitpunkt der Erstellung der Momentaufnahme. Sie gilt nur für einen einzigen Datenträger. Wenn Sie eine VM mit einem einzigen Datenträger (Betriebssystem-Datenträger) verwenden, können Sie eine Momentaufnahme oder ein Image davon erstellen und dann entweder aus der Momentaufnahme oder dem Image eine VM erstellen.

Eine Momentaufnahme bezieht sich nur auf den Datenträger, von dem sie erstellt wurde. Darum ist ihre Verwendung problematisch in Szenarien, die die Koordination mehrerer Datenträger erfordern, z.B. Striping. Momentaufnahmen müssten sich miteinander abstimmen, und dies wird derzeit nicht unterstützt.

## <a name="disk-allocation-and-performance"></a>Datenträgerzuordnung und Leistung

Das folgende Diagramm zeigt die Echtzeitzuweisung von Bandbreite und IOPS für Datenträger unter Verwendung eines dreistufigen Bereitstellungssystems:

![Dreistufiges Bereitstellungssystem, das die Zuweisung von Bandbreite und IOPS zeigt](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Die Bereitstellung auf der ersten Stufe legt die Zuweisung von IOPS und Bandbreite pro Datenträger fest.  Auf der zweiten Stufe implementiert der Computeserverhost die SSD-Bereitstellung und wendet sie nur auf Daten an, die auf der SSD des Servers gespeichert sind. Dies umfasst Datenträger mit Caching (ReadWrite und ReadOnly) sowie lokale und temporäre Datenträger. Schließlich erfolgt die VM-Netzwerkbereitstellung auf der dritten Stufe für alle E/A-Vorgänge, die der Computehost an das Back-End von Azure Storage sendet. Bei diesem Schema hängt die Leistung einer VM von einer Vielzahl von Faktoren ab, z. B. wie die VM die lokale SSD verwendet, von der Anzahl der angeschlossenen Datenträger sowie von der Leistung und dem Cachetyp der angeschlossenen Datenträger.

Als Beispiel für diese Einschränkungen wird eine Standard_DS1v1-VM daran gehindert, das Potenzial von 5.000 IOPS eines P30-Datenträgers aufgrund von Einschränkungen auf SSD- und Netzwerkebene zu erreichen, unabhängig davon, ob ein Cache verwendet wird:

![Standard_DS1v1-Beispielzuordnung](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure verwendet einen priorisierten Netzwerkkanal für den Datenverkehr des Datenträgers, der eine höhere Priorität gegenüber anderen niedrigen Prioritäten des Netzwerkdatenverkehrs erhält. Dies hilft den Datenträgern, ihre erwartete Leistung im Falle von Netzwerkkonflikten aufrechtzuerhalten. Ebenso behandelt Azure Storage Ressourcenkonflikte und andere Probleme im Hintergrund durch den automatischen Lastenausgleich. Azure Storage weist die erforderlichen Ressourcen zu, wenn Sie einen Datenträger erstellen, und wendet einen proaktiven und reaktiven Ausgleich der Ressourcen an, um den Datenverkehr zu bewältigen. Dies stellt weiterhin sicher, dass die Datenträger ihre erwarteten IOPS- und Durchsatzziele einhalten können. Sie können die Metriken auf VM- und Datenträgerebene verwenden, um die Leistungs- und Setupwarnungen bei Bedarf zu verfolgen.

Lesen Sie unseren Artikel [Entwerfen für hohe Leistung](premium-storage-performance.md), um die bewährten Methoden zur Optimierung von VM- und Datenträgerkonfigurationen kennenzulernen, damit Sie die gewünschte Leistung erreichen können.

## <a name="next-steps"></a>Nächste Schritte

Ein Video mit ausführlichen Informationen zu verwalteten Datenträgern finden Sie hier: [Better Azure VM Resiliency with Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency) (Bessere Resilienz von Azure-VMs mit verwalteten Datenträgern).

Erfahren Sie in unserem Artikel über Datenträgertypen mehr über die einzelnen Datenträgertypen, die Azure bietet, welcher Typ sich gut für Ihre Anforderungen eignet, und erhalten Sie Informationen über deren Leistungsziele.

> [!div class="nextstepaction"]
> [Auswählen eines Datenträgertyps für virtuelle IaaS-Computer](disks-types.md)