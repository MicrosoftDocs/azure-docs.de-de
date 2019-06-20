---
title: Includedatei
description: Includedatei
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0e361edfea6365c3d3fe072e12bb303e71bb8d63
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248803"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Häufig gestellte Fragen zu Azure-IaaS-VM-Datenträgern sowie zu verwalteten und nicht verwalteten Premium-Datenträgern

In diesem Artikel gehen wir auf einige häufig gestellte Fragen zu Azure Managed Disks und Azure Premium-SSD-Datenträgern ein.

## <a name="managed-disks"></a>Managed Disks

**Was ist Azure Managed Disks?**

Das Managed Disks-Feature nimmt Ihnen die Speicherkontoverwaltung ab und vereinfacht so die Datenträgerverwaltung für virtuelle Azure-IaaS-Computer. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

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

**Kann ich eine inkrementelle Momentaufnahme eines verwalteten Datenträgers erstellen?**

Nein. Die aktuelle Momentaufnahmefunktion erstellt eine vollständige Kopie eines verwalteten Datenträgers.

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

**Welcher Support für rollenbasierte Zugriffssteuerung steht für verwaltete Datenträger zur Verfügung?**

Managed Disks unterstützt drei zentrale Standardrollen:

* Besitzer: Kann alles verwalten, einschließlich des Zugriffs
* Mitwirkender: Kann alles außer den Zugriff verwalten
* Leser: Kann alles anzeigen, jedoch keine Änderungen vornehmen

**Kann ich einen verwalteten Datenträger in ein privates Speicherkonto kopieren oder exportieren?**

Sie können eine schreibgeschützte SAS-URI (Shared Access Signature) für den verwalteten Datenträger generieren und mit ihr den Inhalt in ein privates Speicherkonto oder einen lokalen Speicher kopieren. Sie können die SAS-URI über das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder [AzCopy](../articles/storage/common/storage-use-azcopy.md) verwenden.

**Kann ich eine Kopie meines verwalteten Datenträgers erstellen?**

Kunden können eine Momentaufnahme ihrer verwalteten Datenträger erstellen und anschließend auf der Grundlage der Momentaufnahme einen weiteren verwalteten Datenträger erstellen.

**Werden nicht verwaltete Datenträger weiterhin unterstützt?**

Ja, es werden verwaltete und nicht verwaltete Datenträger unterstützt. Allerdings wird empfohlen, für neue Workloads verwaltete Datenträger zu verwenden und aktuelle Workloads zu verwalteten Datenträgern zu migrieren.

**Kann ich verwaltete und nicht verwaltete Datenträger zusammen auf derselben VM platzieren?**

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
* [Liste der Vorlagen die Managed Disks verwenden](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Gibt es beim Erstellen eines Datenträgers aus einem Blob eine ständig bestehende Beziehung mit diesem Quellblob?**

Nein. Wenn der neue Datenträger erstellt wird, handelt es sich dabei um eine vollständige eigenständige Kopie dieses Blobs zu diesem Zeitpunkt, und es besteht keine Verbindung zwischen den beiden. Nachdem Sie den Datenträger erstellt haben, können Sie das Quellblob ohne Auswirkungen auf den neu erstellten Datenträger löschen.

**Kann ich einen verwalteten oder nicht verwalteten Datenträger nach der Erstellung umbenennen?**

Verwaltete Datenträger können nicht umbenannt werden. Sie können jedoch einen nicht verwalteten Datenträger umbenennen, sofern dieser aktuell nicht an eine virtuelle Festplatte oder einen virtuellen Computer angefügt ist.

**Kann ich die GPT-Partitionierung auf Azure-Datenträgern verwenden?**

Die GPT-Partitionierung kann nur auf Datenträgern für Daten, nicht auf Betriebssystem-Datenträgern verwendet werden. Betriebssystem-Datenträger müssen den MBR-Partitionstyp verwenden.

**Welche Datenträgertypen unterstützen Momentaufnahmen?**

SSD Premium und SSD Standard unterstützen Momentaufnahmen ohne jegliche Beschränkung der Größe. Somit können Sie Momentaufnahmen großer Datenträger erstellen (bis zu 32 TiB). Ultra-SSDs unterstützen keine Momentaufnahmen.

## <a name="standard-ssd-disks"></a>Standard-SSD-Datenträger

**Was sind Azure-Standard-SSD-Datenträger?**
Standard-SSD-Datenträger sind Standarddatenträger mit Unterstützung durch SSD-Medien, die als kostengünstige Speicher für Workloads optimiert sind, bei denen eine konsistente Leistung auf niedrigeren IOPS-Ebenen benötigt wird.

<a id="standard-ssds-azure-regions"></a>**Welche Regionen werden derzeit für Standard-SSD-Datenträger unterstützt?**
Alle Azure-Regionen unterstützen jetzt Standard-SSD-Datenträger.

**Ist Azure Backup verfügbar, wenn Standard-SSDs verwendet werden?**
Ja, Azure Backup ist jetzt verfügbar.

**Wie erstelle ich Standard-SSD-Datenträger?**
Sie können SSD Standard-Datenträger mithilfe von Azure Resource Manager-Vorlagen, SDK, PowerShell oder CLI erstellen. Im Folgenden werden die in der Resource Manager-Vorlage zum Erstellen von Standard-SSD-Datenträgern erforderlichen Parameter aufgeführt:

* *apiVersion* für Microsoft.Compute muss auf `2018-04-01` (oder höher) festgelegt werden.
* Geben Sie unter *managedDisk.storageAccountType* den Typ `StandardSSD_LRS` an.

Das folgende Beispiel zeigt den Abschnitt *properties.storageProfile.osDisk* für eine VM, die Standard-SSD-Datenträger verwendet:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Eine vollständige Beispielvorlage zum Erstellen eines Standard-SSD-Datenträgers mit einer Vorlage finden Sie unter [Create a Virtual Machine from a Windows Image with multiple empty Standard SSD Data Disks](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Erstellen eines virtuellen Computers aus einem Windows-Image mit mehreren leeren Standard-SSD-Datenträgern).

**Kann ich meine vorhandenen Laufwerke in Standard-SSD konvertieren?**
Ja, das ist möglich. Allgemeine Richtlinien zum Konvertieren von verwalteten Datenträgern finden Sie unter [Konvertieren zwischen dem Standardspeicher und Storage Premium für verwaltete Azure-Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Verwenden Sie außerdem den folgenden Wert, um den Datenträgertyp auf Standard-SSD zu aktualisieren.
-AccountType StandardSSD_LRS

**Was ist der Vorteil der Verwendung von Standard-SSD-Datenträgern anstelle von HDD?**
Im Vergleich zu Festplattenlaufwerken bieten SSD Standard-Datenträger eine bessere Latenz, Konsistenz, Verfügbarkeit und Zuverlässigkeit. Aus diesem Grund können Anwendungsworkloads sehr viel reibungsloser auf Standard-SSD-Datenträgern ausgeführt werden. Premium-SSD-Datenträger sind die empfohlene Lösung für die meisten E/A-intensiven Produktionsworkloads.

**Kann ich Standard-SSDs als nicht verwaltete Datenträger verwenden?**
Nein, Standard-SSD-Datenträger sind nur als verwaltete Datenträger verfügbar.

**Unterstützen Standard-SSD-Datenträger SLAs für Einzelinstanz-VMs?**
Nein, Standard-SSDs weisen keine SLA für Einzelinstanz-VMs auf. Verwenden Sie Premium-SSD-Datenträger für eine SLA für Einzelinstanz-VMs.

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

Ja. Derzeit ist Azure-zu-Azure-Schutz mit Azure Site Recovery für virtuelle Computer mit verwalteten Datenträgern nur als allgemein verfügbarer Dienst verfügbar.

**Kann ich VMs mit nicht verwalteten Datenträgern, die sich auf Speicherkonten befinden, die verschlüsselt sind oder dies waren, zu verwalteten Datenträgern migrieren?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks und Storage Service Encryption

**Ist Azure Storage Service Encryption standardmäßig beim Erstellen eines verwalteten Datenträgers aktiviert?**

Ja.

**Wer verwaltet die Verschlüsselungsschlüssel?**

Microsoft verwaltet die Verschlüsselungsschlüssel.

**Kann ich für meine verwalteten Datenträger Storage Service Encryption deaktivieren?**

Nein.

**Ist Storage Service Encryption nur in bestimmten Regionen verfügbar?**

Nein. Sie ist in allen Regionen verfügbar, in denen Managed Disks verfügbar ist. Managed Disks ist in allen öffentlichen Regionen und Deutschland verfügbar. Es ist auch in China verfügbar, jedoch nur für von Microsoft verwaltete Schlüssel, nicht für vom Kunden verwaltete Schlüssel.

**Wie finde ich heraus, ob mein verwalteter Datenträger verschlüsselt ist?**

Sie können im Azure-Portal, der Azure-CLI und in PowerShell herausfinden, wann ein verwaltetet Datenträger erstellt wurde. Wenn der Zeitpunkt nach dem 9. Juni 2017 liegt, ist Ihr Datenträger verschlüsselt.

**Wie kann ich meine vorhandenen Laufwerke verschlüsseln, die vor dem 10. Juni 2017 erstellt wurden?**

Ab dem 10. Juni 2017 werden neue Daten, die in vorhandene Datenträger geschrieben werden, automatisch verschlüsselt. Darüber hinaus ist geplant, dass vorhandene Daten verschlüsselt werden, wobei die Verschlüsselung asynchron im Hintergrund abläuft. Wenn Sie jetzt schon vorhandene Daten verschlüsseln müssen, erstellen Sie eine Kopie Ihres Datenträgers. Neue Datenträger werden verschlüsselt.

* [Kopieren von verwalteten Datenträger mithilfe der Azure-CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopieren von verwalteten Datenträger mithilfe von PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Werden verwaltete Momentaufnahmen und Bilder verschlüsselt?**

Ja. Alle verwalteten Momentaufnahmen und Bilder, die nach dem 9. Juni 2017 erstellt werden, werden automatisch verschlüsselt. 

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

**Welche ist die maximale Größe verwalteter Datenträger, die für Betriebssystemdatenträger und Datenträger für Daten unterstützt wird?**

Der Partitionstyp, den Azure für einen Betriebssystemdatenträger unterstützt, ist der Master Boot Record (MBR). Das MBR-Format unterstützt eine Datenträgergröße von bis zu 2 TiB. Die maximale Größe, die Azure für einen Betriebssystemdatenträger unterstützt, beträgt 2 TiB. Azure unterstützt bis zu 32TiB für verwaltete Datenträger für Daten im globalen Azure, 4TiB in Sovereign Clouds von Azure.

**Welche ist die maximale Größe nicht verwalteter Datenträger, die für Betriebssystemdatenträger und Datenträger für Daten unterstützt wird?**

Der Partitionstyp, den Azure für einen Betriebssystemdatenträger unterstützt, ist der Master Boot Record (MBR). Das MBR-Format unterstützt eine Datenträgergröße von bis zu 2 TiB. Die maximale Größe, die Azure für einen nicht verwalteten Betriebssystemdatenträger unterstützt, beträgt 2 TiB. Für nicht verwaltete Datenträger für Daten unterstützt Azure bis zu 4 TiB.

**Was ist die größte Seitenblobgröße, die unterstützt wird?**

Die maximale Seitenblobgröße, die von Azure unterstützt wird, ist 8 TiB (8.191 GiB). Die maximale Seitenblobgröße beim Anfügen an eine VM als Daten- oder Betriebssystem-Datenträger beträgt 4 TiB (4.095 GiB).

**Benötige ich eine neue Version der Azure-Tools, um Datenträger, die größer als 1TiB sind, erstellen, anfügen, anpassen und hochladen zu können?**

Sie müssen Ihre vorhandenen Azure-Tools nicht aktualisieren, um Datenträger, die größer als 1 TiB sind, erstellen, anfügen oder anpassen zu können. Um Ihre VHD-Datei von einem lokalen Speicherort als Seitenblob oder als nicht verwalteten Datenträger direkt in Azure hochladen zu können, müssen Sie die neuesten Toolsets verwenden, die im Folgenden aufgeführt sind. Wir unterstützen nur VHD-Uploads von bis zu 8 TiB.

|Azure-Tools      | Unterstützte Versionen                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnummer 4.1.0: Release vom Juni 2017 oder später|
|Azure-CLI V1     | Versionsnummer 0.10.13: Release vom Mai 2017 oder später|
|Azure CLI v2     | Versionsnummer 2.0.12: Release vom Juli 2017 oder später|
|AzCopy           | Versionsnummer 6.1.0: Release vom Juni 2017 oder später|

**Werden P4- und P6-Datenträgergrößen für nicht verwaltete Datenträger oder Seitenblobs unterstützt?**

Die Datenträgergrößen P4 (32 GiB) und P6 (64 GiB) werden nicht als Standarddatenträgertarife für nicht verwaltete Datenträger und Seitenblobs unterstützt. Sie müssen den [Blobtarif](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) explizit auf P4 und P6 festlegen, damit Ihr Datenträger diesen Tarifen zugeordnet wird. Wenn Sie einen nicht verwalteten Datenträger oder ein Seitenblob mit einer Datenträgergröße oder Inhaltslänge von weniger als 32 GiB oder zwischen 32 GiB und 64 GiB bereitstellen, ohne den Blobtarif festzulegen, gilt für Sie weiterhin P10 mit 500 IOPS und 100 MiB/s sowie der zugehörige Tarif.

**Wie wird mein vorhandener verwalteter Premium-Datenträger mit weniger als 64 GiB abgerechnet, wenn er vor der Aktivierung des kleinen Datenträgers (um den 15. Juni 2017) erstellt wurde?**

Vorhandene kleine Premium-Datenträger mit weniger als 64 GiB werden weiterhin gemäß dem P10-Tarif in Rechnung gestellt.

**Wie kann ich den Datenträgertarif von kleinen Premium-Datenträgern mit weniger als 64 GiB von P10 in P4 oder P6 ändern?**

Machen Sie eine Momentaufnahme Ihres kleinen Datenträgers, und erstellen Sie dann einen Datenträger, damit der Tarif automatisch in P4 oder P6 basierend auf der bereitgestellten Größe geändert wird.

**Kann man vorhandene verwaltete Datenträger mit einer Größe von weniger als 4TiB (Tebibytes) in die neu eingeführten Datenträgergrößen von bis zu 32TiB ändern?**

Ja.

**Welche Datenträgergröße wird vom Azure Backup- und vom Azure Site Recovery-Dienst maximal unterstützt?**

Die maximale Datenträgergröße, die vom Azure Backup- und vom Azure Site Recovery-Dienst unterstützt wird, ist 4 TiB. Unterstützung für die größeren Datenträger bis zu 32 TiB ist noch nicht verfügbar.

**Welche VM-Größen werden für große Datenträger (> 4TiB) empfohlen, damit SSD Standard- und HDD Standard-Datenträger optimale Werte für IOPS und Bandbreite erzielen?**

Um für SSD Standard und HDD Standard mit großen Datenträgern (> 4TiB) einen Datenträgerdurchsatz über 500 IOPS und 60MiB/s zu erzielen, sollten Sie zum Optimieren Ihrer Leistung eine neue VM einer der folgenden VM-Größen verwenden: VMs der Serien B, DSv2, Dsv3, ESv3, Fs, Fsv2, M, GS, NCv2, NCv3 oder Ls. Beim Anfügen großer Datenträger an vorhandene VMs oder VMs, die nicht die empfohlenen Größen verwenden, tritt möglicherweise geringere Leistung auf.

**Wie kann ich meine Datenträger (> 4TiB) aktualisieren, die in der Vorschauphase größerer Datenträger bereitgestellt wurden, um die höhere IOPS und Bandbreite bei allgemeiner Verfügbarkeit zu erhalten?**

Sie können entweder die VM beenden und starten, der der Datenträger angefügt ist, oder den Datenträger trennen und erneut anfügen. Die Leistungsziele für größere Datenträger wurden sowohl für Premium-SSDs als auch Standard-SSDs bei allgemeiner Verfügbarkeit erhöht.

**In welchen Regionen werden Größen von 8TiB, 16TiB und 32TiB für verwaltete Datenträger unterstützt?**

Die SKUs für Datenträger mit 8TiB, 16TiB und 32TiB werden in allen Regionen unter globalem Azure, Microsoft Azure Government und Azure China 21Vianet unterstützt.

**Wird die Aktivierung der Hostzwischenspeicherung auf allen Datenträgergrößen unterstützt?**

Die Hostzwischenspeicherung mit Schreibschutz und Lese-/Schreibzugriff wird für Datenträgergrößen unter 4TiB unterstützt. Für Datenträgergrößen von mehr als 4 TiB wird nur die Zwischenspeicherungsoption „Keine“ unterstützt. Es wird empfohlen, die Zwischenspeicherung für kleinere Datenträgergrößen zu nutzen, bei denen durch das Zwischenspeichern von Daten auf der VM eine Leistungssteigerung zu erwarten ist.

## <a name="what-if-my-question-isnt-answered-here"></a>Was kann ich tun, wenn meine Frage hier nicht beantwortet wird?

Wenn Ihre Frage hier nicht aufgeführt wird, informieren Sie uns, und wir helfen Ihnen dabei, eine Antwort zu finden. Sie können in den Kommentaren am Ende dieses Artikels Fragen stellen. Im [Azure Storage-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) von MSDN können Sie sich mit dem Azure Storage-Team und anderen Mitgliedern der Community über diesen Artikel austauschen.

Funktionsvorschläge und -ideen können über das [Azure Storage-Feedbackforum](https://feedback.azure.com/forums/217298-storage) eingereicht werden.
