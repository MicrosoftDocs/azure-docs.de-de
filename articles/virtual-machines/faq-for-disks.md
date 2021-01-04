---
title: Häufig gestellte Fragen zu Datenträgern
description: Häufig gestellte Fragen zu Azure-IaaS-VM-Datenträgern unter Linux und Premium-Datenträgern (verwaltet und nicht verwaltet)
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: dcfef81f2d7f3413489490d97c143fdec7e11bed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499321"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Häufig gestellte Fragen zu Azure-IaaS-VM-Datenträgern sowie zu verwalteten und nicht verwalteten Premium-Datenträgern

In diesem Artikel gehen wir auf einige häufig gestellte Fragen zu Azure Managed Disks und Azure Premium-SSD-Datenträgern ein.

## <a name="managed-disks"></a>Managed Disks

**Was ist Azure Managed Disks?**

Das Managed Disks-Feature nimmt Ihnen die Speicherkontoverwaltung ab und vereinfacht so die Datenträgerverwaltung für virtuelle Azure-IaaS-Computer. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks](managed-disks-overview.md).

**Welche Kosten fallen für mich an, wenn ich auf der Grundlage einer vorhandenen virtuellen Festplatte mit einer Größe von 80 GB einen verwalteten Standarddatenträger erstelle?**

Ein verwalteter Standarddatenträger, der auf Grundlage einer virtuellen Festplatte mit einer Größe von 80 GB erstellt wird, wird nach der nächsten verfügbaren Größe für Standarddatenträger abgerechnet (in diesem Fall also als S10-Datenträger). Ihnen wird der Preis für einen S10-Datenträger in Rechnung gestellt. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Fallen bei verwalteten Standarddatenträgern Transaktionskosten an?**

Ja. Jede Transaktion wird Ihnen in Rechnung gestellt. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Wird bei einem verwalteten Standarddatenträger die tatsächliche Größe der Daten auf dem Datenträger oder die bereitgestellte Kapazität des Datenträgers abgerechnet?**

Die Abrechnung erfolgt auf der Grundlage der bereitgestellten Kapazität des Datenträgers. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Inwiefern unterscheiden sich die Preise für verwaltete Premium-Datenträger von den Preisen für nicht verwaltete Datenträger?**

Die Preise für verwaltete Premium-Datenträger unterscheiden sich nicht von den Preisen für nicht verwaltete Premium-Datenträger.

**Kann ich den Speicherkontotyp (Standard oder Premium) meiner verwalteten Datenträger ändern?**

Ja. Der Speicherkontotyp Ihrer verwalteten Datenträger kann über das Azure-Portal mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle geändert werden.

**Kann ich mithilfe einer VHD-Datei in einem Azure-Speicherkonto einen verwalteten Datenträger mit einem anderen Abonnement erstellen?**

Ja.

**Kann ich mithilfe einer VHD-Datei in einem Azure-Speicherkonto einen verwalteten Datenträger in einer anderen Region erstellen?**

Nein.

**Gelten für Kunden, die verwaltete Datenträger verwenden, Einschränkungen bei der Skalierung?**

Managed Disks beseitigt die Grenzwerte im Zusammenhang mit Speicherkonten. Der Grenzwert liegt jedoch bei 50.000 verwalteten Datenträgern pro Region und Datenträgertyp für ein Abonnement.

**Können virtuelle Computer in einer Verfügbarkeitsgruppe eine Kombination aus verwalteten und nicht verwalteten Datenträgern besitzen?**

Nein. Die virtuellen Computer in einer Verfügbarkeitsgruppe müssen entweder alle über verwaltete oder alle über nicht verwaltete Datenträger verfügen. Den gewünschten Datenträgertyp können Sie bei der Erstellung einer Verfügbarkeitsgruppe auswählen.

**Ist Managed Disks die Standardoption im Azure-Portal?**

Ja.

**Kann ich einen leeren verwalteten Datenträger erstellen?**

Ja. Sie können einen leeren Datenträger erstellen. Ein verwalteter Datenträger kann unabhängig von einem virtuellen Computer erstellt werden, also ohne ihn z.B. an einen virtuellen Computer anzufügen.

**Wie viele Fehlerdomänen werden bei Verwendung von verwalteten Datenträgern standardmäßig für Verfügbarkeitsgruppen unterstützt?**

Bei Verwendung von verwalteten Datenträgern werden für Verfügbarkeitsgruppen abhängig von der Region entweder zwei oder drei Fehlerdomänen unterstützt.

**Wie wird das Standardspeicherkonto für die Diagnose eingerichtet?**

Sie richten ein privates Speicherkonto für die VM-Diagnose ein.

**Welcher Support für rollenbasierte Zugriffssteuerung in Azure steht für verwaltete Datenträger zur Verfügung?**

Managed Disks unterstützt drei zentrale Standardrollen:

* Besitzer: Kann alles verwalten, einschließlich des Zugriffs
* Mitwirkender: Kann alles außer den Zugriff verwalten
* Leser: Kann alles anzeigen, jedoch keine Änderungen vornehmen

**Kann ich einen verwalteten Datenträger in ein privates Speicherkonto kopieren oder exportieren?**

Sie können eine schreibgeschützte SAS-URI (Shared Access Signature) für den verwalteten Datenträger generieren und mit ihr den Inhalt in ein privates Speicherkonto oder einen lokalen Speicher kopieren. Sie können die SAS-URI über das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder [AzCopy](../storage/common/storage-use-azcopy-v10.md) verwenden.

**Kann ich eine Kopie meines verwalteten Datenträgers erstellen?**

Kunden können eine Momentaufnahme ihrer verwalteten Datenträger erstellen und anschließend auf der Grundlage der Momentaufnahme einen weiteren verwalteten Datenträger erstellen.

**Werden nicht verwaltete Datenträger weiterhin unterstützt?**

Ja, es werden verwaltete und nicht verwaltete Datenträger unterstützt. Allerdings wird empfohlen, für neue Workloads verwaltete Datenträger zu verwenden und aktuelle Workloads zu verwalteten Datenträgern zu migrieren.

**Kann ich verwaltete und nicht verwaltete Datenträger auf ein und derselben VM zusammenstellen?**

Nein.

**Wenn ich einen 128-GB-Datenträger erstelle und dann die Größe auf 130GiB (Gibibytes) erhöhe, wird mir dann die nächsthöhere Datenträgergröße (256GiB) in Rechnung gestellt?**

Ja.

**Kann ich verwaltete Datenträger für lokal redundanten Speicher, georedundanten Speicher und zonenredundanten Speicher erstellen?**

Azure Managed Disks unterstützt momentan nur lokal redundanten Speicher (LRS).

**Kann ich meine verwalteten Datenträger verkleinern?**

Nein. Dies wird derzeit nicht unterstützt.

**Kann ich auf meinen Datenträgern eine Lease unterbrechen?**

Nein. Dies wird derzeit nicht unterstützt, da eine Lease dafür da ist, versehentliches Löschen zu verhindern, wenn der Datenträger verwendet wird.

**Kann ich die Eigenschaft „Computername“ ändern, wenn ein spezialisierter Betriebssystemdatenträger (der nicht mit dem Systemvorbereitungstool erstellt wurde und nicht generalisiert ist) zur Bereitstellung einer VM verwendet wird?**

Nein. Sie können die Eigenschaft „Computername“ nicht aktualisieren. Die neue VM erbt diese von der übergeordneten VM, mit der der Betriebssystem-Datenträger erstellt wurde. 

**Wo finde ich Beispielvorlagen von Azure Resource Manager, um virtuelle Computer mit verwalteten Datenträgern zu erstellen?**
* [Liste der Vorlagen die Managed Disks verwenden](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Gibt es beim Erstellen eines Datenträgers aus einem Blob eine ständig bestehende Beziehung mit diesem Quellblob?**

Nein. Wenn der neue Datenträger erstellt wird, handelt es sich dabei um eine vollständige eigenständige Kopie dieses Blobs zu diesem Zeitpunkt, und es besteht keine Verbindung zwischen den beiden. Nachdem Sie den Datenträger erstellt haben, können Sie das Quellblob ohne Auswirkungen auf den neu erstellten Datenträger löschen.

**Kann ich einen verwalteten oder nicht verwalteten Datenträger nach der Erstellung umbenennen?**

Verwaltete Datenträger können nicht umbenannt werden. Sie können jedoch einen nicht verwalteten Datenträger umbenennen, sofern dieser aktuell nicht an eine virtuelle Festplatte oder einen virtuellen Computer angefügt ist.

**Kann ich die GPT-Partitionierung auf Azure-Datenträgern verwenden?**

Bei Images der Generation 1 kann die GPT-Partitionierung nur auf Datenträgern und nicht auf Betriebssystem-Datenträgern verwendet werden. Betriebssystem-Datenträger müssen den MBR-Partitionstyp verwenden.

[Bei Images der Generation 2](./generation-2.md) kann die GPT-Partitionierung sowohl auf dem Betriebssystem-Datenträger als auch auf den Datenträgern verwendet werden.

**Welche Datenträgertypen unterstützen Momentaufnahmen?**

Für SSD Premium, SSD Standard und HDD Standard werden Momentaufnahmen unterstützt. Für diese drei Datenträgertypen werden Momentaufnahmen für alle Datenträgergrößen unterstützt (einschließlich Datenträger mit einer Größe von bis zu 32 TiB). Ultra-Datenträger unterstützen keine Momentaufnahmen.

**Was sind Azure-Datenträgerreservierungen?**
Die Datenträgerreservierung bietet die Möglichkeit, Datenträgerspeicher ein Jahr im Voraus zu erwerben und so die Gesamtkosten zu reduzieren. Ausführliche Informationen zu Azure-Datenträgerreservierungen finden Sie in unserem Artikel zum Thema: [Grundlegendes zur Anwendung des Rabatts für Reservierungen auf Azure Storage](../cost-management-billing/reservations/understand-disk-reservations.md).

**Welche Optionen werden für die Azure-Datenträgerreservierung angeboten?**     
Die Azure-Datenträgerreservierung bietet die Option, SSD Premium in den angegebenen SKUs von P30 (1 TiB) bis P80 (32 TiB) für eine einjährige Laufzeit zu erwerben. Es gibt keine Beschränkung für die Mindestmenge an Datenträgern zum Erwerb einer Datenträgerreservierung. Darüber hinaus können Sie entweder eine einmalige Vorauszahlung oder monatliche Zahlungen durchführen. Es fallen keine zusätzlichen Transaktionskosten für die verwalteten SSD Premium-Datenträger an.    

Reservierungen werden in Form von Datenträgern vorgenommen, nicht nach Kapazität. Anders ausgedrückt: Wenn Sie einen P80-Datenträger (32 TiB) reservieren, erhalten Sie einen einzelnen P80-Datenträger. Sie können diese Reservierung nicht in zwei kleinere P70-Datenträger (16 TiB) aufteilen. Sie können natürlich beliebig viele (oder wenige) Datenträger reservieren, auch zwei separate P70-Datenträger (16 TiB).

**Wie wird die Azure-Datenträgerreservierung angewandt?**     
Die Datenträgerreservierung folgt einem Modell, das reservierten VM-Instanzen ähnelt. Der Unterschied besteht darin, dass eine Datenträgerreservierung im Gegensatz zu einer VM-Instanz nicht auf verschiedene SKUs angewandt werden kann. Weitere Informationen zu VM-Instanzen finden Sie unter [Kostensparen mit Azure Reserved VM Instances](./prepay-reserved-vm-instances.md).     

**Kann ich meinen über die Azure-Datenträgerreservierung erworbenen Datenspeicher in mehreren Regionen verwenden?**     
Azure-Datenträgerreservierungen werden für eine bestimmte Region und SKU erworben (z. B. P30 in „USA, Osten 2“) und können daher nicht außerhalb dieser Vorgaben verwendet werden. Sie können jederzeit eine zusätzliche Azure-Datenträgerreservierung für Ihre Datenträger-Speicheranforderungen in anderen Regionen oder SKUs erwerben.    

**Was geschieht beim Ablauf meiner Azure-Datenträgerreservierung?**     
Sie erhalten 30 Tage vor dem Ablauf und am Ablaufdatum eine E-Mail-Benachrichtigung. Nach dem Ablauf der Reservierung werden bereitgestellte Datenträger weiterhin ausgeführt und mit dem aktuellen [Satz für die nutzungsbasierte Bezahlung](https://azure.microsoft.com/pricing/details/managed-disks/) in Rechnung gestellt.

**Unterstützen Standard-SSD-Datenträger SLAs für Einzelinstanz-VMs?**

Ja, alle Datenträgertypen unterstützen SLAs für Einzelinstanz-VMs.

### <a name="azure-shared-disks"></a>Freigegebene Azure-Datenträger

**Wird das Feature Freigegebene Datenträger für nicht verwaltete Datenträger oder Seitenblobs unterstützt?**

Nein, es wird nur für Ultra-Datenträger und verwaltete SSD-Premium-Datenträger unterstützt.

**Welche Regionen unterstützen freigegebene Datenträger?**

Informationen zu Regionen finden Sie in unserem [Konzeptartikel](disks-shared.md).

**Können freigegebene Datenträger als Betriebssystemdatenträger verwendet werden?**

Nein, freigegebene Datenträger werden nur für Datenträger mit Daten unterstützt.

**Welche Datenträgergrößen werden als freigegebene Datenträger unterstützt?**

Informationen zu unterstützten Größen finden Sie in unserem [Konzeptartikel](disks-shared.md).

**Wenn ich bereits über einen Datenträger verfüge, kann ich darauf freigegebene Datenträger aktivieren?**

Alle verwalteten Datenträger, die mit API-Version 2019-07-01 oder höher erstellt wurden, können freigegebene Datenträger aktivieren. Zu diesem Zweck müssen Sie die Einbindung des Datenträgers in allen VMs aufheben, an die er angefügt ist. Bearbeiten Sie anschließend die **maxShares**-Eigenschaft des Datenträgers.

**Wenn ich einen Datenträger nicht mehr im freigegebenen Modus verwenden möchte, wie kann ich die Funktion deaktivieren?**

Heben Sie die Einbindung des Datenträgers in allen VMs auf, an die er angefügt ist. Legen Sie anschließend die maxShare-Eigenschaft des Datenträgers auf 1 fest.

**Lässt sich die Größe eines freigegebenen Datenträgers ändern?**

Ja.

**Kann ich die Schreibbeschleunigung für einen Datenträger aktivieren, auf dem auch freigegebene Datenträger aktiviert sind?**

Nein.

**Kann ich die Hostzwischenspeicherung für einen Datenträger aktivieren, auf dem auch freigegebene Datenträger aktiviert sind?**

Die einzige unterstützte Option für die Hostzwischenspeicherung ist **Ohne**.

## <a name="ultra-disks"></a>Ultra-Datenträger

**Auf welchen Wert sollte ich den Durchsatz meines Ultra-Datenträgers festlegen?**
Wenn Sie nicht sicher sind, auf welchen Wert Sie den Datenträgerdurchsatz festlegen sollen, nehmen Sie zunächst eine E/A-Größe von 16 KiB an, und passen Sie die Leistung davon ausgehend an, während Sie Ihre Anwendung überwachen. Die Formel lautet: Durchsatz in MBit/s = Wert von IOPS * 16/1000.

**Ich habe meinen Datenträger auf 40000 IOPS konfiguriert, sehe aber nur 12800 IOPS. Warum wird die Leistung des Datenträgers nicht angezeigt?**
Zusätzlich zur Datenträgerdrosselung gibt es eine E/A-Drosselung, die auf VM-Ebene durchgesetzt wird. Stellen Sie sicher, dass die von Ihnen verwendete VM-Größe die Ebenen unterstützt, die auf Ihren Datenträgern konfiguriert sind. Weitere Informationen zu den von Ihrer VM vorgegebenen E/A-Beschränkungen finden Sie unter [Größen für virtuelle Computer in Azure](sizes.md).

**Kann ich Zwischenspeicherungsebenen (Cachingebenen) mit einem Ultra-Datenträger verwenden?**
Nein, für Ultra-Datenträger werden die verschiedenen Zwischenspeicherungsmethoden, die für andere Datenträgertypen unterstützt werden, nicht unterstützt. Legen Sie die Datenträgerzwischenspeicherung auf **Ohne** fest.

**Kann ich meinem vorhandenen virtuellen Computer einen Ultra-Datenträger zuordnen?**
Möglicherweise muss sich Ihr virtueller Computer in einer Kombination aus Region und Verfügbarkeitszone befinden, für die Ultra-Datenträger unterstützt werden. Weitere Informationen finden Sie unter [Verwenden von Azure Ultra-Datenträgern](disks-enable-ultra-ssd.md).

**Kann ich einen Ultra-Datenträger als Betriebssystemdatenträger für meinen virtuellen Computer verwenden?**
Nein, Ultra-Datenträger werden nur als Datenträger für Daten und nur als native 4K-Datenträger unterstützt.

**Kann ich einen vorhandenen Datenträger in einen Ultra-Datenträger konvertieren?**
Nein, aber Sie können die Daten von einem vorhandenen Datenträger auf einen Ultra-Datenträger migrieren. Um einen vorhandenen Datenträger auf einen Ultra-Datenträger zu migrieren, ordnen Sie beide Datenträger demselben virtuellen Computer zu, und kopieren Sie die Daten vom vorhandenen Datenträger auf den Ultra-Datenträger, oder nutzen Sie eine Drittanbieterlösung für Datenmigration.

**Kann ich Momentaufnahmen für Ultra-Datenträger erstellen?**
Nein, Momentaufnahmen sind noch nicht verfügbar.

**Ist Azure Backup für Ultra-Datenträger verfügbar?**
Nein, Azure Backup-Unterstützung ist noch nicht verfügbar.

**Kann ich einen Ultra-Datenträger einem virtuellen Computer zuordnen, der in einer Verfügbarkeitsgruppe ausgeführt wird?**
Nein, das wird noch nicht unterstützt.

**Kann ich Azure Site Recovery für virtuelle Computer mit Ultra-Datenträgern aktivieren?**
Nein, Azure Site Recovery wird für Ultra-Datenträger noch nicht unterstützt.

## <a name="uploading-to-a-managed-disk"></a>Hochladen in einen verwalteten Datenträger

**Kann ich Daten in einen vorhandenen verwalteten Datenträger hochladen?**

Nein, Hochladen kann nur während der Erstellung eines neuen leeren Datenträgers verwendet werden, der den Status **ReadyToUpload** hat.

**Wie lade ich Daten auf einen verwalteten Datenträger hoch?**

Erstellen Sie den verwalteten Datenträger so, dass die [createOption](/rest/api/compute/disks/createorupdate#diskcreateoption)-Eigenschaft von [creationData](/rest/api/compute/disks/createorupdate#creationdata) auf „Upload“ festgelegt ist. Danach können Sie Daten auf den Datenträger hochladen.

**Kann ich einen Datenträger einem virtuellen Computer zuordnen, während der Datenträger einen Uploadstatus hat?**

Nein.

**Kann ich eine Momentaufnahme eines verwalteten Datenträgers erstellen, der einen Uploadstatus hat?**

Nein.

## <a name="standard-ssd-disks"></a>Standard-SSD-Datenträger

**Was sind Azure-Standard-SSD-Datenträger?**
Standard-SSD-Datenträger sind Standarddatenträger mit Unterstützung durch SSD-Medien, die als kostengünstige Speicher für Workloads optimiert sind, bei denen eine konsistente Leistung auf niedrigeren IOPS-Ebenen benötigt wird.

<a id="standard-ssds-azure-regions"></a>**Welche Regionen werden derzeit für Standard-SSD-Datenträger unterstützt?**
Alle Azure-Regionen unterstützen jetzt Standard-SSD-Datenträger.

**Ist Azure Backup verfügbar, wenn Standard-SSDs verwendet werden?**
Ja, Azure Backup ist jetzt verfügbar.

**Was ist der Vorteil der Verwendung von Standard-SSD-Datenträgern anstelle von HDD?**
Im Vergleich zu Festplattenlaufwerken bieten SSD Standard-Datenträger eine bessere Latenz, Konsistenz, Verfügbarkeit und Zuverlässigkeit. Aus diesem Grund können Anwendungsworkloads sehr viel reibungsloser auf Standard-SSD-Datenträgern ausgeführt werden. Premium-SSD-Datenträger sind die empfohlene Lösung für die meisten E/A-intensiven Produktionsworkloads.

**Kann ich Standard-SSDs als nicht verwaltete Datenträger verwenden?**
Nein, Standard-SSD-Datenträger sind nur als verwaltete Datenträger verfügbar.

## <a name="migrate-to-managed-disks"></a>Migrieren zu Managed Disks

**Hat die Migration Auswirkungen auf die Leistung von Managed Disks?**

Migration umfasst das Verschieben des Datenträgers von einem Speicherort zu einem anderen. Dies wird über eine Hintergrundkopie der Daten orchestriert, die bis zum Abschluss mehrere Stunden dauern kann, je nach der Menge der Daten auf den Datenträgern meist weniger als 24 Stunden. Während dieser Zeit können bei Ihrer Anwendung höhere Latenzen bei Lesevorgängen als üblich auftreten, da einige Lesevorgänge an den ursprünglichen Speicherort umgeleitet werden können, sodass es länger dauert, sie abzuschließen. Die Latenz beim Schreiben ist während dieses Zeitraums nicht betroffen.  

**Welche Änderungen sind in einer bereits vorhandenen Azure Backup-Dienstkonfiguration vor/nach der Migration zu Managed Disks erforderlich?**

Es sind keine Änderungen erforderlich.

**Werden meine VM-Sicherungen über den Azure Backup-Dienst erstellt, bevor die Migration weiterhin funktioniert?**

Ja, Sicherungen funktionieren reibungslos.

**Welche Änderungen sind in einer bereits vorhandenen Azure Disk Encryption-Konfiguration vor/nach der Migration zu Managed Disks erforderlich?**

Es sind keine Änderungen erforderlich.

**Wird die automatisierte Migration einer vorhandenen VM-Skalierungsgruppe von nicht verwalteten Datenträgern zu Managed Disks unterstützt?**

Nein. Sie können eine neue Skalierungsgruppe mit Managed Disks mithilfe des Images von Ihrer alten Skalierungsgruppe mit nicht verwalteten Datenträgern erstellen.

**Kann ich verwaltete Datenträger über eine Seitenblob-Momentaufnahme erstellen, die vor der Migration zu Managed Disks erstellt wurde?**

Nein. Sie können eine Seitenblob-Momentaufnahme als Seitenblob exportieren und anschließend den verwalteten Datenträger aus dem exportierten Seitenblob erstellen.

**Kann ich für meine lokalen Computer, die mit Azure Site Recovery geschützt sind, ein Failover auf eine VM mit Managed Disks ausführen?**

Ja, Sie können ein Failover auf einem virtuellen Computer mit Managed Disks auswählen.

**Hat die Migration zu Azure-VMs, die mit Azure Site Recovery geschützt sind, über die Azure-zu-Azure-Replikation irgendwelche Auswirkungen?**

Nein. Azure-zu-Azure-Schutz mit Azure Site Recovery für virtuelle Computer mit Managed Disks ist verfügbar.

**Kann ich VMs mit nicht verwalteten Datenträgern, die sich auf Speicherkonten befinden, die verschlüsselt sind oder dies waren, zu verwalteten Datenträgern migrieren?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks und Storage Service Encryption

**Ist serverseitige Verschlüsselung standardmäßig beim Erstellen eines verwalteten Datenträgers aktiviert?**

Ja. Verwaltete Datenträger werden standardmäßig mit serverseitiger Verschlüsselung mit von der Plattform verwalteten Schlüsseln verschlüsselt. 

**Ist das Startvolume auf einem verwalteten Datenträger standardmäßig verschlüsselt?**

Ja. Standardmäßig sind alle verwalteten Datenträger verschlüsselt, einschließlich des Betriebssystem-Datenträgers.

**Wer verwaltet die Verschlüsselungsschlüssel?**

Von der Plattform verwaltete Schlüssel werden von Microsoft verwaltet. Sie können auch Ihre eigenen Schlüssel in Azure Key Vault verwenden und verwalten. 

**Kann ich die serverseitige Verschlüsselung für meine verwalteten Datenträger deaktivieren?**

Nein.

**Ist die serverseitige Verschlüsselung nur in bestimmten Regionen verfügbar?**

Nein. Die serverseitige Verschlüsselung sowohl mit den von der Plattform als auch mit vom Kunden verwalteten Schlüsseln ist in allen Regionen verfügbar, in denen verwaltete Datenträger verfügbar sind. 

**Unterstützt Azure Site Recovery die serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüssel in Szenarien der Notfallwiederherstellung zwischen lokalen Standorten und Azure und zwischen zwei Azure-Standorten?**

Ja. 

**Kann ich verwaltete Datenträger mit serverseitiger Verschlüsselung mit vom Kunden verwalteten Schlüsseln mit dem Azure Backup-Dienst sichern?**

Ja.

**Werden verwaltete Momentaufnahmen und Bilder verschlüsselt?**

Ja. Alle verwalteten Momentaufnahmen und Images werden automatisch verschlüsselt. 

**Kann ich VMs mit nicht verwalteten Datenträgern, die sich auf Speicherkonten befinden, die verschlüsselt sind oder dies waren, in verwaltete Datenträger konvertieren?**

Ja

**Wird eine exportierte VHD-Datei von einem verwalteten Datenträger oder eine Momentaufnahme auch verschlüsselt?**

Nein. Wenn Sie allerdings eine VHD-Datei von einem verschlüsselten, verwalteten Datenträger oder einer Momentaufnahme in ein verschlüsseltes Speicherkonto exportieren, ist sie verschlüsselt. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium-Datenträger: verwaltet und nicht verwaltet

**Kann ich sowohl Premium- als auch Standard-Datenträger anfügen, wenn ein virtueller Computer eine Größenserie mit Unterstützung für Premium-SSD-Datenträger (beispielsweise DSv2) verwendet?** 

Ja.

**Kann ich an eine Größenserie ohne Unterstützung für Premium-SSD-Datenträger (also beispielsweise an die D-, Dv2-, G- oder F-Serie) sowohl Premium- als auch Standard-Datenträger anfügen?**

Nein. An virtuelle Computer, die keine Größenserie mit Unterstützung für Premium-SSD-Datenträger verwenden, können nur Standard-Datenträger angefügt werden.

**Welche Kosten fallen an, wenn ich einen Storage Premium-Datenträger von einer vorhandenen virtuellen Festplatte mit einer Größe von 80 GB erstelle?**

Ein Premiumdatenträger, der auf der Grundlage einer virtuellen Festplatte mit einer Größe von 80 GB erstellt wird, wird nach der nächsten verfügbaren Größe für Premiumdatenträger abgerechnet (in diesem Fall also als P10-Datenträger). Ihnen wird der Preis für einen P10-Datenträger in Rechnung gestellt.

**Fallen bei Verwendung von Premium-SSD-Datenträgern Transaktionskosten an?**

Es fallen feste Kosten für die Datenträgergröße an, und es gelten bestimmte Grenzwerte für IOPS und Durchsatz. Weitere Kosten fallen gegebenenfalls für die ausgehende Bandbreite und die Momentaufnahmekapazität an. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Welche IOPS- und Durchsatzgrenzwerte gelten für den Datenträgercache?**

Die kombinierten Grenzwerte für den Cache und die lokale SSD für virtuelle Computer der DS-Serie liegt bei 4000 IOPS pro Kern und 33 MiB pro Sekunde und Kern. Die GS-Serie bietet 5000 IOPS pro Kern und 50 MiB pro Sekunde und Kern.

**Wird für virtuelle Computer mit Managed Disks die lokale SSD unterstützt?**

Bei der lokalen SSD handelt es sich um einen temporären Speicher, der in einem virtuellen Computer mit Managed Disks enthalten ist. Für diesen temporären Speicher fallen keine zusätzlichen Kosten an. Es empfiehlt sich, auf dieser lokalen SSD keine Anwendungsdaten zu speichern, da die Daten nicht dauerhaft in Azure Blob Storage gespeichert werden.

**Gibt es Konsequenzen für die Verwendung von TRIM auf Premium-Datenträgern?**

Es gibt keinen Nachteil bei der Verwendung von TRIM auf Azure-Datenträger auf Premium- oder Standard-Datenträgern.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Neue Datenträgergrößen: verwaltet und nicht verwaltet

**Welche Regionen unterstützen die Burstfunktion für die gültigen SSD Premium-Datenträgergrößen?**

Die Burstfunktion wird derzeit in allen Regionen der öffentlichen Azure-Cloud unterstützt, und Unterstützung von Sovereign Clouds folgt in Kürze. 

**In welchen Regionen werden die Größen mit 4/8/16 GiB für verwaltete Datenträger (P1/P2/P3, E1/E2/E3) unterstützt?**

Diese neuen Datenträgergrößen werden derzeit in allen Regionen der öffentlichen Azure-Cloud unterstützt, und Unterstützung von Sovereign Clouds folgt in Kürze. 

**Werden die Datenträgergrößen P1/P2/P3 für nicht verwaltete Datenträger oder Seitenblobs unterstützt?**

Nein, sie werden nur auf verwalteten SSD Premium-Datenträgern unterstützt. 

**Werden die Datenträgergrößen E1/E2/E3 für nicht verwaltete Datenträger oder Seitenblobs unterstützt?**

Nein, verwaltete SSD Standard-Datenträger beliebiger Größe können nicht mit nicht verwalteten Datenträgern oder Seitenblobs verwendet werden.

**Welche ist die maximale Größe verwalteter Datenträger, die für Betriebssystemdatenträger und Datenträger auf Gen1-VMs unterstützt wird?**

Der Partitionstyp, den Azure für einen Gen1-Betriebssystemdatenträger unterstützt, ist der Master Boot Record (MBR). Obwohl Gen1-Betriebssystemdatenträger nur MBR unterstützen, unterstützen die Datenträger GPT. Sie können einen Betriebssystemdatenträger von bis zu 4 TiB zuordnen, mit dem MBR-Partitionstyp können jedoch nur bis zu 2 TiB dieses Speicherplatzes für das Betriebssystem verwendet werden. Für verwaltete Datenträger unterstützt Azure bis zu 32 TiB.

**Welche ist die maximale Größe verwalteter Datenträger, die für Betriebssystemdatenträger und Datenträger auf Gen2-VMs unterstützt wird?**

Der Partitionstyp, den Azure für einen Gen2-Betriebssystemdatenträger unterstützt, ist die GUID-Partitionstabelle (GPT). Gen2-VMs unterstützen Betriebssystemdatenträger von bis zu 4 TiB. Für verwaltete Datenträger unterstützt Azure bis zu 32 TiB.


**Welche ist die maximale Größe nicht verwalteter Datenträger, die für Betriebssystemdatenträger und Datenträger für Daten unterstützt wird?**

Der Partitionstyp, den Azure für einen Betriebssystemdatenträger mit nicht verwalteten Datenträgern unterstützt, ist der Master Boot Record (MBR).  Sie können einen Betriebssystemdatenträger von bis zu 4 TiB zuordnen, mit dem MBR-Partitionstyp können jedoch nur bis zu 2 TiB dieses Speicherplatzes für das Betriebssystem verwendet werden. Für nicht verwaltete Datenträger unterstützt Azure bis zu 4 TiB.


**Was ist die größte Seitenblobgröße, die unterstützt wird?**

Die maximale Seitenblobgröße, die von Azure unterstützt wird, ist 8 TiB (8.191 GiB). Die maximale Seitenblobgröße beim Anfügen an eine VM als Daten- oder Betriebssystem-Datenträger beträgt 4 TiB (4.095 GiB).

**Benötige ich eine neue Version der Azure-Tools, um Datenträger, die größer als 1TiB sind, erstellen, anfügen, anpassen und hochladen zu können?**

Sie müssen Ihre vorhandenen Azure-Tools nicht aktualisieren, um Datenträger, die größer als 1 TiB sind, erstellen, anfügen oder anpassen zu können. Um Ihre VHD-Datei von einem lokalen Speicherort als Seitenblob oder als nicht verwalteten Datenträger direkt in Azure hochladen zu können, müssen Sie die neuesten Toolsets verwenden, die im Folgenden aufgeführt sind. Wir unterstützen nur VHD-Uploads von bis zu 8 TiB.

|Azure-Tools      | Unterstützte Versionen                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnummer 4.1.0: Release vom Juni 2017 oder später|
|Azure-CLI V1     | Versionsnummer 0.10.13: Release vom Mai 2017 oder später|
|Azure CLI v2     | Versionsnummer 2.0.12: Release vom Juli 2017 oder später|
|AzCopy              | Versionsnummer 6.1.0: Release vom Juni 2017 oder später|

**Werden P4- und P6-Datenträgergrößen für nicht verwaltete Datenträger oder Seitenblobs unterstützt?**

Die Datenträgergrößen P4 (32 GiB) und P6 (64 GiB) werden nicht als Standarddatenträgertarife für nicht verwaltete Datenträger und Seitenblobs unterstützt. Sie müssen den [Blobtarif](/rest/api/storageservices/set-blob-tier) explizit auf P4 und P6 festlegen, damit Ihr Datenträger diesen Tarifen zugeordnet wird. Wenn Sie einen nicht verwalteten Datenträger oder ein Seitenblob mit einer Datenträgergröße oder Inhaltslänge von weniger als 32 GiB oder zwischen 32 GiB und 64 GiB bereitstellen, ohne den Blobtarif festzulegen, gilt für Sie weiterhin P10 mit 500 IOPS und 100 MiB/s sowie der zugehörige Tarif.

**Wie wird mein vorhandener verwalteter Premium-Datenträger mit weniger als 64 GiB abgerechnet, wenn er vor der Aktivierung des kleinen Datenträgers (um den 15. Juni 2017) erstellt wurde?**

Vorhandene kleine Premium-Datenträger mit weniger als 64 GiB werden weiterhin gemäß dem P10-Tarif in Rechnung gestellt.

**Wie kann ich den Datenträgertarif von kleinen Premium-Datenträgern mit weniger als 64 GiB von P10 in P4 oder P6 ändern?**

Machen Sie eine Momentaufnahme Ihres kleinen Datenträgers, und erstellen Sie dann einen Datenträger, damit der Tarif automatisch in P4 oder P6 basierend auf der bereitgestellten Größe geändert wird.

**Kann man vorhandene verwaltete Datenträger mit einer Größe von weniger als 4TiB (Tebibytes) in die neu eingeführten Datenträgergrößen von bis zu 32TiB ändern?**

Ja.

**Welche Datenträgergröße wird vom Azure Backup- und vom Azure Site Recovery-Dienst maximal unterstützt?**

Die größte von Azure Backup unterstützte Datenträgergröße beträgt 32 TiB (4 TiB für verschlüsselte Datenträger). Die maximale Datenträgergröße, die von Azure Site Recovery unterstützt wird, beträgt 8 TiB. Unterstützung für die größeren Datenträger bis zu 32 TiB ist in Azure Site Recovery noch nicht verfügbar.

**Welche VM-Größen werden für große Datenträger (> 4TiB) empfohlen, damit SSD Standard- und HDD Standard-Datenträger optimale Werte für IOPS und Bandbreite erzielen?**

Um für SSD Standard und HDD Standard mit großen Datenträgern (> 4TiB) einen Datenträgerdurchsatz über 500 IOPS und 60MiB/s zu erzielen, sollten Sie zum Optimieren Ihrer Leistung eine neue VM einer der folgenden VM-Größen verwenden: VMs der Serien B, DSv2, Dsv3, ESv3, Fs, Fsv2, M, GS, NCv2, NCv3 oder Ls. Beim Anfügen großer Datenträger an vorhandene VMs oder VMs, die nicht die empfohlenen Größen verwenden, tritt möglicherweise geringere Leistung auf.

**Wie kann ich meine Datenträger (> 4TiB) aktualisieren, die in der Vorschauphase größerer Datenträger bereitgestellt wurden, um die höhere IOPS und Bandbreite bei allgemeiner Verfügbarkeit zu erhalten?**

Sie können entweder die VM beenden und starten, der der Datenträger angefügt ist, oder den Datenträger trennen und erneut anfügen. Die Leistungsziele für größere Datenträger wurden sowohl für Premium-SSDs als auch Standard-SSDs bei allgemeiner Verfügbarkeit erhöht.

**In welchen Regionen werden Größen von 8TiB, 16TiB und 32TiB für verwaltete Datenträger unterstützt?**

Die SKUs für Datenträger mit 8TiB, 16TiB und 32TiB werden in allen Regionen unter globalem Azure, Microsoft Azure Government und Azure China 21Vianet unterstützt.

**Wird die Aktivierung der Hostzwischenspeicherung auf allen Datenträgergrößen unterstützt?**

Die Hostzwischenspeicherung (**ReadOnly** und **Read/Write**) wird auf Datenträgergrößen unter 4 TiB unterstützt. Das bedeutet, dass alle Datenträger, die mit einer Größe von maximal 4095 GiB bereitgestellt wurden, von der Hostzwischenspeicherung profitieren können. Die Hostzwischenspeicherung wird für Datenträger größer oder gleich 4096 GiB nicht unterstützt. Ein Beispiel: Ein P50-Premium-Datenträger, der mit 4095 GiB bereitgestellt wurde, kann die Hostzwischenspeicherung nutzen, ein mit 4096 GiB bereitgestellter P50-Datenträger dagegen nicht. Es wird empfohlen, die Zwischenspeicherung für kleinere Datenträgergrößen zu nutzen, bei denen durch das Zwischenspeichern von Daten auf der VM eine Leistungssteigerung zu erwarten ist.

## <a name="private-links-for-securely-exporting-and-importing-managed-disks"></a>Private Link für einen sicheren Export und Import von Managed Disks

**Welchen Vorteil bietet Private Link für das Exportieren und Importieren von Managed Disks?**

Sie können Private Link nutzen, um den Export und Import aus bzw. in Managed Disks nur auf Ihr virtuelles Azure-Netzwerk zu beschränken. 

**Wie kann ich sicherstellen, dass ein Datenträger nur über Private Link exportiert oder importiert wird?**

Sie müssen die Eigenschaft `DiskAccessId` auf eine Instanz eines Datenträgerzugriffsobjekts und die NetworkAccessPolicy-Eigenschaft auf `AllowPrivate` festlegen.

**Kann ich mehrere virtuelle Netzwerke mit demselben Datenträgerzugriffsobjekt verknüpfen?**

Nein. Derzeit können Sie ein Datenträgerzugriffsobjekt nur mit einem einzigen virtuellen Netzwerk verknüpfen.

**Kann ich ein virtuelles Netzwerk mit einem Datenträgerzugriffsobjekt in einem anderen Abonnement verknüpfen?**

Nein. Derzeit können Sie ein Datenträgerzugriffsobjekt nur mit einem virtuellen Netzwerk im selben Abonnement verknüpfen.

**Kann ich ein virtuelles Netzwerk mit einem Datenträgerzugriffsobjekt in einem anderen Abonnement verknüpfen?**

Nein. Derzeit können Sie ein Datenträgerzugriffsobjekt nur mit einem virtuellen Netzwerk im selben Abonnement verknüpfen.

**Wie viele Export- oder Importvorgänge mit demselben Datenträgerzugriffsobjekt können gleichzeitig stattfinden?**

5

**Kann ich einen SAS-URI eines Datenträgers oder einer Momentaufnahme verwenden, um die zugrunde liegende VHD einer VM im selben Subnetz herunterzuladen, in dem sich auch der dem Datenträger zugeordnete private Endpunkt befindet?**

Ja.

**Kann ich einen SAS-URI eines Datenträgers oder einer Momentaufnahme verwenden, um die zugrunde liegende VHD einer VM herunterzuladen, die sich nicht im selben Subnetz wie der dem Datenträger nicht zugeordnete private Endpunkt befindet?**

Nein.

## <a name="what-if-my-question-isnt-answered-here"></a>Was kann ich tun, wenn meine Frage hier nicht beantwortet wird?

Wenn Ihre Frage hier nicht aufgeführt wird, informieren Sie uns, und wir helfen Ihnen dabei, eine Antwort zu finden. Sie können in den Kommentaren am Ende dieses Artikels Fragen stellen. Auf der [Microsoft-Seite mit Fragen und Antworten zu Azure Storage](/answers/products/azure?product=storage) können Sie sich mit dem Azure Storage-Team und anderen Communitymitgliedern über diesen Artikel austauschen.

Funktionsvorschläge und -ideen können über das [Azure Storage-Feedbackforum](https://feedback.azure.com/forums/217298-storage) eingereicht werden.