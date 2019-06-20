---
title: include file
description: include file
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 3ea41a16f79f903b5ff7d2b93f74f7d3d2a0dd83
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133064"
---
Katalog mit freigegebenen Images ist ein Dienst, der Ihnen hilft, Ihre verwalteten Images zu strukturieren und organisieren. Kataloge mit freigegebenen Images stellen Folgendes bereit:

- Verwaltete globale Replikation von Images.
- Versionsverwaltung und Gruppierung von Images zur einfacheren Verwaltung.
- Hochverfügbare Images mit ZRS-Konten (zonenredundanter Speicher) in Regionen, die Verfügbarkeitszonen unterstützen. ZRS bietet bessere Ausfallsicherheit bei zonenbezogenen Fehlern.
- Freigeben über Abonnements hinweg und sogar zwischen Active Directory-Mandanten (AD) über RBAC.
- Skalieren Ihrer Bereitstellungen mit Imagereplikaten in jeder Region.

Mit einem Katalog mit freigegebenen Images können Sie Ihre Images für unterschiedliche Benutzer, Dienstprinzipale oder AD-Gruppen in Ihrer Organisation freigeben. Freigegebene Images können zur schnelleren Skalierung Ihrer Bereitstellungen in mehreren Regionen repliziert werden.

Ein verwaltetes Image ist eine Kopie entweder einer vollständigen VM (einschließlich sämtlicher angefügter Datenträger) oder lediglich des Betriebssystemdatenträgers, je nachdem, wie Sie das Image erstellen. Wenn Sie aus dem Image einen virtuellen Computer erstellen, werden Kopien der virtuellen Festplatten in dem Image verwendet, um die Datenträger für die neue VM zu erstellen. Das verwaltete Image verbleibt im Speicher und kann immer wieder zum Erstellen neuer VMs verwendet werden.

Wenn Sie eine große Anzahl verwalteter Images haben, die Sie verwalten müssen und im gesamten Unternehmen zur Verfügung stellen möchten, können Sie einen Katalog mit geteilten Images als Repository verwenden, mit dem Sie Ihre Images ganz einfach freigeben können. 

Die Funktion „Katalog mit geteilten Images“ verfügt über mehrere Ressourcentypen:

| Resource | BESCHREIBUNG|
|----------|------------|
| **Verwaltetes Image** | Ein Basisimage, das eigenständig oder zum Erstellen einer **Imageversion** in einem Imagekatalog verwendet werden kann. Verwaltete Images werden aus generalisierten virtuellen Computern erstellt. Ein verwaltetes Image ist ein spezieller VHD-Typ, mit dem mehrere virtuelle Computer und jetzt auch Versionen von freigegebenen Images erstellt werden können. |
| **Imagekatalog** | Wie der Azure Marketplace ist ein **Imagekatalog** ein Repository zum Verwalten und Teilen von Images, aber Sie kontrollieren, wer Zugriff hat. |
| **Imagedefinition** | Images sind innerhalb eines Katalogs definiert und enthalten Informationen über das jeweilige Image und die Anforderungen für dessen Verwendung in Ihrer Organisation. Sie können Informationen einbinden, etwa, ob das Image ein Windows- oder Linux-Image ist, Anforderungen hinsichtlich minimalem und maximalem Arbeitsspeicher und Versionshinweise. Es ist eine Definition eines Imagetyps. |
| **Imageversion** | Eine **Imageversion** ist, was Sie verwenden, um einen virtuellen Computer zu erstellen, wenn Sie einen Katalog verwenden. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wie bei einem verwalteten Image wird, wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen. Imageversionen können mehrmals verwendet werden. |

<br>


![Eine Abbildung, die zeigt, wie Sie mehrere Versionen eines Images in Ihrem Katalog haben können.](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Imagedefinitionen

Eine Imagedefinition ist eine logische Gruppierung für Versionen eines Images. Die Imagedefinition enthält Informationen darüber, warum das Image erstellt wurde, für welches Betriebssystem es vorgesehen ist und wie es verwendet wird. Eine Imagedefinition ist wie ein Plan für alle Details rund um das Erstellen eines bestimmten Image. Sie stellen einen virtuellen Computer nicht aus einer Imagedefinition, sondern aus der Imageversion bereit, die aus der Definition erstellt wurde.


Es gibt drei Parameter für jede Imagedefinition, die in Kombination verwendet werden **Herausgeber**, **Angebot** und **SKU**. Diese Parameter werden verwendet, um eine spezielle Imagedefinition zu finden. Bei einzelnen Imageversionen können ein oder zwei Werte identisch sein, aber nicht alle drei.  Hier werden z.B. drei Imagedefinitionen und deren Werte gezeigt:

|Imagedefinition|Herausgeber|Angebot|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanzen|Back-End|
|myImage2|Contoso|Finanzen|Front-End|
|myImage3|Testen|Finanzen|Front-End|

Alle drei verfügen über eindeutige Sätze von Werten. Das Format ist ähnlich der Weise, in der Sie aktuell Herausgeber, Angebot und SKU für [Azure Marketplace-Images](../articles/virtual-machines/windows/cli-ps-findimage.md) in Azure PowerShell angeben können, um die neueste Version eines Marketplace-Images abzurufen. Für jede Imagedefinition ist ein eindeutiger Satz dieser Werte erforderlich.

Die folgenden Parameter sind weitere Parameter, die für Ihre Imagedefinition festgelegt werden können, damit Sie Ihre Ressourcen einfacher verfolgen können:

* Betriebssystemstatus: Sie können den Betriebssystemstatus auf „Generalisiert“ oder „Spezialisiert“ festlegen, derzeit wird jedoch nur „Generalisiert“ unterstützt. Images müssen von virtuellen Computern erstellt werden, die mithilfe von Sysprep für Windows oder `waagent -deprovision` für Linux generalisiert wurden.
* Betriebssystem: Kann entweder Windows oder Linux sein.
* Beschreibung: Verwenden Sie eine Beschreibung, um ausführlichere Informationen darüber anzugeben, warum die Imagedefinition vorhanden ist. Sie könnten z. B. eine Imagedefinition für Ihren Front-End-Server haben, in dem die Anwendung vorinstalliert ist.
* EULA (Lizenzbedingungen): Kann verwendet werden, um auf einen Endbenutzer-Lizenzvertrag zu verweisen, der speziell für die Imagedefinition gilt.
* Datenschutzbestimmungen und Versionshinweise: Speichern Sie Versionshinweise und Datenschutzbestimmungen, und stellen Sie einen URI für den Zugriff auf sie als Teil der Imagedefinition bereit.
* Ablaufdatum (Datum für Lebensende): Weisen Sie Ihrer Imagedefinition ein Ablaufdatum zu, damit das Löschen alter Imagedefinitionen automatisiert werden kann.
* Tag: Sie können Tags hinzufügen, wenn Sie Ihre Imagedefinition erstellen. Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../articles/azure-resource-manager/resource-group-using-tags.md).
* Mindest- und Maximalempfehlungen zu vCPU und Arbeitsspeicher: Wenn es für Ihr Image vCPU- und Arbeitsspeicherempfehlungen gibt, können Sie diese Informationen zu Ihrer Imagedefinition hinzufügen.
* Unzulässige Datenträgertypen: Sie können Informationen über die Speicheranforderungen für Ihren virtuellen Computer bereitstellen. Wenn Ihr Image z. B. nicht für normale Festplattenlaufwerke geeignet ist, fügen Sie diese zur Liste „Nicht zulassen“ hinzu.


## <a name="regional-support"></a>Regionsunterstützung

Quellregionen sind in der folgenden Tabelle aufgeführt. Alle öffentlichen Regionen können Zielregionen sein, aber um in „Australien, Mitte“ und „Australien, Mitte 2“ zu replizieren, muss Ihr Abonnement in eine Whitelist aufgenommen werden. Hier können Sie die Aufnahme in die Whitelist anfordern: https://www.microsoft.com/en-au/central-regions-eligibility/


| Quellregionen |
|---------------------|-----------------|------------------|-----------------|
| Australien, Mitte   | USA, Mitte (EUAP) | Korea, Mitte    | USA, Westen-Mitte |
| Australien, Mitte 2 | Asien, Osten       | Korea, Süden      | Europa, Westen     |
| Australien (Osten)      | USA (Ost)         | USA Nord Mitte | Indien, Westen      |
| Australien, Südosten | USA (Ost) 2       | Nordeuropa     | USA (Westen)         |
| Brasilien Süd        | USA, Osten 2 (EUAP)  | USA Süd Mitte | USA, Westen 2       |
| Kanada, Mitte      | Frankreich, Mitte  | Indien (Süden)      |                 |
| Kanada, Osten         | Frankreich, Süden    | Asien, Südosten   |                 |
| Indien, Mitte       | Japan, Osten      | UK, Süden         |                 |
| USA (Mitte)          | Japan, Westen      | UK, Westen          |                 |



## <a name="limits"></a>Einschränkungen 

Pro Abonnement gibt es Einschränkungen hinsichtlich der Bereitstellung von Ressourcen mithilfe des Katalogs mit geteilten Images:
- 100 Kataloge mit freigegebenen Images pro Abonnement und Region
- 1\.000 Imagedefinitionen pro Abonnement und Region
- 10\.000 Imageversionen pro Abonnement und Region

Weitere Informationen finden Sie unter [Vergleichen der Ressourcennutzung mit Grenzwerten](https://docs.microsoft.com/azure/networking/check-usage-against-limits) in Beispielen dazu, wie Sie Ihre aktuelle Nutzung überprüfen.
 

## <a name="scaling"></a>Skalieren
Im Katalog mit freigegebenen Images können Sie die Anzahl der Replikate angeben, die Azure für die Images verwalten soll. Dies ist in Szenarien mit mehreren VM-Bereitstellungen hilfreich, da die VM-Bereitstellungen auf verschiedene Replikate verteilt werden können. Dadurch wird die Wahrscheinlichkeit verringert, dass der Instanzerstellungsprozess durch die Überlastung eines einzelnen Replikats gedrosselt wird.


Mit dem Katalog mit freigegebenen Images können Sie jetzt bis zu 1.000 VM-Instanzen in einer VM-Skalierungsgruppe bereitstellen (erhöht von 600 mit verwalteten Images). Imagereplikate bieten eine bessere Leistung, Zuverlässigkeit und Konsistenz bei der Bereitstellung.  Sie können in jeder Zielregion ein andere Replikatanzahl festlegen, basierend auf der für die Region erforderlichen Skalierung. Da jedes Replikat eine tiefe Kopie Ihres Images ist, hilft dies dabei, Ihre Bereitstellungen mit jedem zusätzlichen Replikat linear zu skalieren. Natürlich sind wir uns bewusst, dass keine zwei Images oder Regionen identisch sind, doch dies ist unsere allgemeine Richtlinie für die Verwendung von Replikaten in einer Region:

- Pro 20 virtuelle Computer, die Sie gleichzeitig erstellen, empfehlen wir, dass Sie ein Replikat behalten. Wenn Sie beispielsweise 120 VMs gleichzeitig mit demselben Image in einer Region erstellen, empfehlen wir, dass Sie mindestens 6 Replikate Ihres Images behalten. 
- Für jede Skalierungsgruppenbereitstellung mit bis zu 600 Instanzen empfehlen wir, dass Sie mindestens ein Replikat behalten. Wenn Sie beispielsweise 5 Skalierungsgruppen gleichzeitig erstellen, wobei jede davon 600 VM-Instanzen in einer Region enthält, die dasselbe Image verwenden, empfehlen wir, dass Sie mindestens 5 Replikate Ihres Images behalten. 

Wir empfehlen Ihnen stets, die Anzahl der Replikate überdimensioniert bereitzustellen, wegen Faktoren wie Größe, Inhalt und Betriebssystemtyp des Images.


![Eine Abbildung, die zeigt, wie Sie Images skalieren können](./media/shared-image-galleries/scaling.png)



## <a name="make-your-images-highly-available"></a>Hochverfügbarkeit für Ihre Images

[Zonenredundanter Azure-Speicher (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) bietet Resilienz vor einem Ausfall der Verfügbarkeitszone in der Region. Mit der allgemeinen Verfügbarkeit des Katalogs mit freigegebenen Images können Sie sich entschließen, Ihre Images in ZRS-Konten in Regionen mit Verfügbarkeitszonen zu speichern. 

Sie können außerdem den Kontotyp für jede der Zielregionen auswählen. Der Standardtyp des Speicherkontos ist „Standard_LRS“, aber Sie können „Standard_ZRS“ für Regionen mit Verfügbarkeitszonen auswählen. Überprüfen Sie die regionale Verfügbarkeit von ZRS [hier](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Grafik mit ZRS](./media/shared-image-galleries/zrs.png)


## <a name="replication"></a>Replikation
Der Katalog mit geteilten Images ermöglicht Ihnen außerdem die automatische Replikation Ihrer Images in andere Azure-Regionen. Jede Version eines freigegebenen Images kann entsprechend den Anforderungen Ihrer Organisation in unterschiedliche Regionen repliziert werden. Ein Beispiel ist, immer das neueste Image in mehrere Regionen zu replizieren, während alle ältere Versionen nur in einer Region zur Verfügung stehen. Dadurch können die Speicherkosten für Versionen freigegebener Images gesenkt werden. 

Die Regionen, in die eine Versionen eines freigegebenen Images repliziert wird, können nach der Erstellung aktualisiert werden. Der Zeitaufwand für die Replikation in verschiedene Regionen hängt von der kopierten Datenmenge ab sowie von der Anzahl der Regionen, in die die Version repliziert wird. Dies kann in einigen Fällen mehrere Stunden dauern. Während die Replikation durchgeführt wird, können Sie den Status der Replikation pro Region anzeigen. Sobald die Imagereplikation in einer Region abgeschlossen ist, können Sie einen virtuellen Computer oder eine Skalierungsgruppe über diese Imageversion in der Region bereitstellen.

![Eine Abbildung, die zeigt, wie Sie Images replizieren können](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Da es sich bei dem Katalog mit freigegebenen Images, der Imagedefinition und der Imageversion alles um Ressourcen handelt, lassen sich diese mithilfe der integrierten nativen Azure-RBAC-Steuerelemente freigeben. Mithilfe der rollenbasierten Zugriffssteuerung können Sie diese Ressourcen für andere Benutzer, Dienstprinzipalen und Gruppen freigeben. Sie können sogar Zugriff für Personen freigeben, die sich außerhalb des Mandanten befinden, in dem sie erstellt wurden. Sobald ein Benutzer Zugriff auf die Version eines freigegebenen Images hat, kann er einen virtuellen Computer oder eine VM-Skalierungsgruppe bereitstellen.  Im Folgenden finden Sie die Matrix für das Teilen, die dabei hilft, zu verstehen, worauf der Benutzer Zugriff erhält:

| Geteilt mit Benutzer     | Gemeinsamer Image-Katalog | Imagedefinition | Imageversion |
|----------------------|----------------------|--------------|----------------------|
| Gemeinsamer Image-Katalog | Ja                  | Ja          | Ja                  |
| Imagedefinition     | Nein                   | Ja          | Ja                  |

Zur Erzielung der besten Leistung empfiehlt sich ein Freigeben auf Katalogebene. Wir empfehlen nicht, einzelnen Imageversionen freizugeben. Weitere Informationen zu RBAC finden Sie unter [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Images können auch, maßstäblich, freigegeben werden, sogar über eine mehrinstanzenfähige App-Registrierung zwischen Mandanten hinweg. Weitere Informationen zum Freigeben von Images zwischen Mandanten finden Sie unter [Freigeben von Katalog-VM-Images über Azure-Mandanten hinweg](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Abrechnung
Für die Verwendung des Katalogs mit geteilten Images fällt keine zusätzliche Gebühren an. Für folgende Ressourcen werden Gebühren berechnet:
- Speicherkosten für die Speicherung der Versionen freigegebener Images. Die Kosten hängen von der Anzahl der Replikate der Imageversion sowie von der Anzahl der Regionen ab, in denen die Version repliziert wird. Wenn Sie z. B. 2 Images haben und beide in 3 Regionen repliziert werden, dann werden Ihnen 6 verwaltete Datenträger anhand von deren Größen berechnet. Weitere Informationen finden Sie unter [Verwaltete Datenträger – Preise ](https://azure.microsoft.com/pricing/details/managed-disks/).
- Ausgehender Netzwerkdatenverkehr wird für die Replikation der ersten Imageversion aus der Quellregion in die replizierten Regionen berechnet. Weitere Replikate werden in der Region verarbeitet, sodass keine zusätzlichen Gebühren anfallen. 

## <a name="updating-resources"></a>Aktualisieren von Ressourcen

Sobald die Imagekatalogressourcen erstellt sind, können Sie diese ändern. Diese Änderungen sind auf Folgendes beschränkt:
 
Katalog mit geteilten Images:
- BESCHREIBUNG

Imagedefinition:
- Empfohlene vCPUs
- Empfohlener Arbeitsspeicher
- BESCHREIBUNG
- Datum für Ende des Lebenszyklus

Imageversion:
- Anzahl regionaler Replikate
- Zielregionen
- Aus Neueste ausschließen
- Datum für Ende des Lebenszyklus


## <a name="sdk-support"></a>SDK-Unterstützung

Die folgenden SDKs unterstützen das Erstellen von Katalogen mit freigegebenen Images:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Vorlagen

Sie können einen Katalog mit freigegebenen Images mithilfe von Vorlagen erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Erstellen eines virtuellen Computers aus einer Imageversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen 

**F.** Wie kann ich alle Ressourcen eines Katalogs mit geteilten Images über Abonnements hinweg auflisten? 
 
 A. Um alle Ressourcen eines Katalogs mit geteilten Images über Abonnements hinweg aufzulisten, auf die Sie im Azure-Portal Zugriff haben, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Wechseln Sie zu **Alle Ressourcen**.
1. Wählen Sie alle Abonnements aus, von denen Sie alle Ressourcen auflisten möchten.
1. Suchen Sie nach Ressourcen vom Typ **Privater Katalog**.
 
   Um die Imagedefinitionen und Imageversionen anzuzeigen, sollten Sie ebenfalls **Ausgeblendete Typen anzeigen** auswählen.
 
   Um alle Ressourcen eines Katalogs mit freigegebenen Images für die Abonnements, auf die Sie Zugriff haben, aufzulisten, verwenden Sie den folgenden Befehl in der Azure-Befehlszeilenschnittstelle:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**F.** Kann ich mein vorhandenes Image in den Katalog mit geteilten Images verschieben?
 
 A. Ja. Es gibt 3 Szenarien, die auf den Typen von Images basieren, die Sie haben können.

 Szenario 1: Wenn Sie ein verwaltetes Image haben, können Sie daraus eine Imagedefinition und eine Imageversion erstellen.

 Szenario 2: Wenn Sie ein nicht verwaltetes, generalisiertes Image haben, können Sie daraus ein verwaltetes Image erstellen und dann daraus eine Imagedefinition und eine Imageversion erstellen. 

 Szenario 3: Wenn Sie eine VHD in Ihrem lokalen Dateisystem haben, müssen Sie die VHD hochladen, ein verwaltetes Image erstellen, und dann können Sie daraus eine Imagedefinition und eine Imageversion erstellen.
- Wenn die VHD von einer Windows-VM stammt, lesen Sie [Hochladen einer generalisierten VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Wenn die VHD für eine Linux-VM ist, lesen Sie [Hochladen einer VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd).


**F.** Kann ich eine Imageversion von einem speziellen Datenträger erstellen?

 A. Nein, zurzeit unterstützen wir keine speziellen Datenträger als Images. Wenn Sie einen speziellen Datenträger haben, müssen Sie [einen virtuellen Computer aus der VHD erstellen](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk), indem Sie den speziellen Datenträger an einen neuen virtuellen Computer anfügen. Wenn Sie einen virtuellen Computer haben, der ausgeführt wird, befolgen Sie die Anweisungen, um ein verwaltetes Image auf der Grundlage des [virtuellen Windows-Computers](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) oder des [virtuellen Linux-Computers](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) zu erstellen. Nachdem Sie ein generalisiertes, verwaltetes Image haben, können Sie den Prozess zum Erstellen einer geteilten Imagebeschreibung und Imageversion starten.

 
**F.** Kann ich nach der Erstellung die Katalogressource mit geteilten Images in ein anderes Abonnement verschieben?

 A. Nein, Sie können Katalogressource mit geteilten Images nicht in ein anderes Abonnement verschieben. Sie können allerdings die Imageversionen im Katalog nach Bedarf in andere Regionen replizieren.

**F.** Kann ich meine Imageversionen zwischen Clouds replizieren – Azure China 21Vianet, Azure Deutschland und Azure Government Cloud? 

 A. Nein, Sie können Imageversionen nicht zwischen Clouds replizieren.

**F.** Kann ich meine Imageversionen zwischen Abonnements replizieren? 

 A. Nein, Sie können die Imageversionen zwischen Regionen in einem Abonnement replizieren und mittels rollenbasierter Zugriffssteuerung in anderen Abonnements verwenden.

**F.** Kann ich Imageversionen zwischen Azure AD-Mandanten freigeben? 

 A. Ja, Sie können RBAC verwenden, um Imageversionen mandantenübergreifend für Einzelpersonen freizugeben. Wenn Sie jedoch über [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) oder [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md) maßstäblich freigeben möchten, lesen Sie „Freigeben von Katalog-VM-Images über Azure-Mandanten hinweg“.


**F.** Wie lange dauert die Replikation von Imageversionen zwischen Zielregionen?

 A. Die Replikationszeit für Imageversionen ist vollständig abhängig von der Größe des Images und der Anzahl der Regionen, in die es repliziert wird. Allerdings wird als bewährte Methode empfohlen, dass Sie das Image klein halten und die Quell- und Zielregionen möglichst benachbart, um optimale Ergebnisse zu erzielen. Sie können den Status der Replikation mit dem Flag „-ReplicationStatus“ überprüfen.


**F.** Was ist der Unterschied zwischen Quellregion und Zielregion?

 A. Quellregion ist die Region, in der Ihre Imageversion erstellt wird, und Zielregionen sind die Regionen, in denen eine Kopie Ihrer Imageversion gespeichert werden soll. Für jede Imageversion können Sie nur eine Quellregion haben. Stellen Sie außerdem sicher, dass Sie beim Erstellen einer Imageversion den Ort der Quellregion als eine der Zielregionen übergeben.  


**F.** Wie gebe ich die Quellregion beim Erstellen der Imageversion an?

 A. Beim Erstellen einer Imageversion können Sie den Parameter **--location** in der CLI und den Parameter **-Location** in der PowerShell verwenden, um die Quellregion anzugeben. Stellen Sie sicher, dass sich das verwaltete Image, das Sie als Basisimage für die Erstellung der Imageversion verwenden, am selben Ort befindet, an dem Sie die Imageversion erstellen möchten. Stellen Sie außerdem sicher, dass Sie beim Erstellen einer Imageversion den Ort der Quellregion als eine der Zielregionen übergeben.  


**F.** Wie gebe ich die Anzahl der Imageversionsreplikate an, die in jeder Region erstellt werden sollen?

 A. Es gibt zwei Möglichkeiten, wie Sie die Anzahl der Imageversionsreplikate angeben können, die in jeder Region erstellt werden sollen:
 
1. Die regionale Replikatanzahl, die die Anzahl der Replikate angibt, die Sie pro Region erstellen möchten. 
2. Die allgemeine Replikatanzahl, bei der es sich um die Standardanzahl pro Region handelt für den Fall, dass die regionale Replikatanzahl nicht angegeben ist. 

Um die regionale Replikatanzahl anzugeben, übergeben Sie den Ort zusammen mit der Anzahl der Replikate, die Sie in dieser Region erstellen möchten: „USA, Süden-Mitte=2“. 

Wenn die regionale Replikatanzahl nicht für jeden Ort angegeben ist, entspricht die Standardanzahl der Replikate der allgemeinen Replikatanzahl, die Sie angegeben haben. 

Um die allgemeine Replikatanzahl in der CLI anzugeben, verwenden Sie das Argument **--replica-count** in dem Befehl `az sig image-version create`.


**F.** Kann ich den Katalog mit geteilten Images an einem anderen Ort als dem erstellen, an dem ich die Imagedefinition und Imageversion erstellen möchte?

 A. Ja, das ist möglich. Als bewährte Methode empfehlen wir Ihnen aber, dass Sie die Ressourcengruppe, den Katalog mit geteilten Images, die Imagedefinition und die Imageversion am selben Ort halten.


**F.** Welche Gebühren fallen für die Verwendung des Katalogs mit geteilten Images an?

 A. Es fallen keine Gebühren für die Verwendung des Diensts für Kataloge mit geteilten Images an, außer den Speichergebühren für das Speichern der Imageversionen sowie den Gebühren für ausgehenden Netzwerkdatenverkehr für die Replikation der Imageversionen aus der Quellregion in Zielregionen.

**F.** Welche API-Version sollte ich verwenden, um einen Katalog mit geteilten Images, eine Imagedefinition, eine Imageversion und aus der Imageversion eine VM/VMSS zu erstellen?

 A. Für Bereitstellungen von VMs und VM-Skalierungsgruppen mithilfe einer Imageversion wird die Verwendung der API-Version 2018-04-01 oder höher empfohlen. Um mit Katalogen mit geteilten Images, Imagedefinitionen und Imageversionen zu arbeiten, empfehlen wir Ihnen die Verwendung der API-Version 2018-06-01. Zonenredundanter Speicher (ZRS) erfordert Version 2019-03-01 oder höher.
