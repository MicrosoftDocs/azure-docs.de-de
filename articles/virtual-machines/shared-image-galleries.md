---
title: Freigeben von VM-Images für freigegebene Kataloge
description: Erfahren Sie, wie Shared Image Gallerys verwendet werden, um Linux-VM-Images in Ihrer Organisation freizugeben.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 4156308fa58ea1f02e6e3f2c5174839dc1cd381c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96839685"
---
# <a name="shared-image-galleries-overview"></a>Übersicht über Kataloge mit freigegebenen Images

Katalog mit freigegebenen Images ist ein Dienst, der Ihnen hilft, Ihre Images zu strukturieren und organisieren. Kataloge mit freigegebenen Images stellen Folgendes bereit:

- Globale Replikation von Images
- Versionsverwaltung und Gruppierung von Images zur einfacheren Verwaltung.
- Hochverfügbare Images mit ZRS-Konten (zonenredundanter Speicher) in Regionen, die Verfügbarkeitszonen unterstützen. ZRS bietet bessere Ausfallsicherheit bei zonenbezogenen Fehlern.
- Storage Premium-Support (Premium_LRS).
- Freigeben über Abonnements hinweg und sogar zwischen Active Directory-Mandanten (AD) über RBAC.
- Skalieren Ihrer Bereitstellungen mit Imagereplikaten in jeder Region.

Mit einem Katalog mit freigegebenen Images können Sie Ihre Images für unterschiedliche Benutzer, Dienstprinzipale oder AD-Gruppen in Ihrer Organisation freigeben. Freigegebene Images können zur schnelleren Skalierung Ihrer Bereitstellungen in mehreren Regionen repliziert werden.

Ein Image ist eine Kopie entweder einer vollständigen VM (einschließlich sämtlicher angefügter Datenträger) oder lediglich des Betriebssystemdatenträgers, je nachdem, wie es erstellt wird. Wenn Sie aus dem Image einen virtuellen Computer erstellen, werden Kopien der virtuellen Festplatten in dem Image verwendet, um die Datenträger für die neue VM zu erstellen. Das Image verbleibt im Speicher und kann immer wieder zum Erstellen neuer VMs verwendet werden.

Wenn Sie eine große Anzahl Images haben, die Sie verwalten müssen und im gesamten Unternehmen zur Verfügung stellen möchten, können Sie eine Shared Image Gallery als Repository verwenden. 

Die Funktion „Katalog mit geteilten Images“ verfügt über mehrere Ressourcentypen:

| Resource | BESCHREIBUNG|
|----------|------------|
| **Imagequelle** | Dies ist eine Ressource, die zum Erstellen einer **Imageversion** in einem Imagekatalog verwendet werden kann. Eine Imagequelle kann eine vorhandene Azure-VM, die entweder [generalisiert oder spezialisiert](#generalized-and-specialized-images) ist, ein verwaltetes Image, eine Momentaufnahme, eine VHD oder eine Imageversion in einem anderen Imagekatalog sein. |
| **Imagekatalog** | Wie der Azure Marketplace ist ein **Imagekatalog** ein Repository zum Verwalten und Teilen von Images, aber Sie kontrollieren, wer Zugriff hat. |
| **Imagedefinition** | Imagedefinitionen werden in einem Katalog erstellt und enthalten Informationen zum Image und zu den Anforderungen für seine interne Verwendung. Dies schließt ein, ob das Image Windows oder Linux ist, Anmerkungen zu dieser Version und Anforderungen an den minimalen und maximalen Arbeitsspeicher. Es ist eine Definition eines Imagetyps. |
| **Imageversion** | Eine **Imageversion** ist, was Sie verwenden, um einen virtuellen Computer zu erstellen, wenn Sie einen Katalog verwenden. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wie bei einem verwalteten Image wird, wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen. Imageversionen können mehrmals verwendet werden. |

<br>

![Eine Abbildung, die zeigt, wie Sie mehrere Versionen eines Images in Ihrem Katalog haben können.](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Imagedefinitionen

Eine Imagedefinition ist eine logische Gruppierung für Versionen eines Images. Die Imagedefinition enthält Informationen dazu, warum das Image erstellt wurde, für welches Betriebssystem es vorgesehen ist und wie es verwendet wird. Eine Imagedefinition ist wie ein Plan für alle Details rund um das Erstellen eines bestimmten Image. Sie stellen eine VM nicht aus einer Imagedefinition, sondern aus den Imageversionen bereit, die aus der Definition erstellt wurden.

Es gibt drei Parameter für jede Imagedefinition, die in Kombination verwendet werden **Herausgeber**, **Angebot** und **SKU**. Diese Parameter werden verwendet, um eine spezielle Imagedefinition zu finden. Bei einzelnen Imageversionen können ein oder zwei Werte identisch sein, aber nicht alle drei.  Hier werden z.B. drei Imagedefinitionen und deren Werte gezeigt:

|Imagedefinition|Herausgeber|Angebot|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanzen|Back-End|
|myImage2|Contoso|Finanzen|Front-End|
|myImage3|Testen|Finanzen|Front-End|

Alle drei verfügen über eindeutige Sätze von Werten. Das Format ist ähnlich der Weise, in der Sie aktuell Herausgeber, Angebot und SKU für [Azure Marketplace-Images](./windows/cli-ps-findimage.md) in Azure PowerShell angeben können, um die neueste Version eines Marketplace-Images abzurufen. Für jede Imagedefinition ist ein eindeutiger Satz dieser Werte erforderlich.

Die folgenden Parameter bestimmen, welche Arten von Imageversionen sie enthalten können:

- Betriebssystemstatus: Sie können den Betriebssystemstatus auf [„Generalisiert“ oder „Spezialisiert“](#generalized-and-specialized-images) festlegen. Dieses Feld ist erforderlich.
- Betriebssystem: Kann entweder Windows oder Linux sein. Dieses Feld ist erforderlich.
-   Hyper-V-Generation: Sie können angeben, ob das Image aus einer Hyper-V-VHD der Generation 1 oder der [Generation 2](generation-2.md) erstellt wurde. Der Standardwert ist Generation 1.


Die folgenden Parameter sind weitere Parameter, die für Ihre Imagedefinition festgelegt werden können, damit Sie Ihre Ressourcen einfacher verfolgen können:

- Beschreibung: Verwenden Sie eine Beschreibung, um ausführlichere Informationen darüber anzugeben, warum die Imagedefinition vorhanden ist. Sie könnten z. B. eine Imagedefinition für Ihren Front-End-Server haben, in dem die Anwendung vorinstalliert ist.
- EULA (Lizenzbedingungen): Kann verwendet werden, um auf einen Endbenutzer-Lizenzvertrag zu verweisen, der speziell für die Imagedefinition gilt.
- Datenschutzbestimmungen und Versionshinweise: Speichern Sie Versionshinweise und Datenschutzbestimmungen, und stellen Sie einen URI für den Zugriff auf sie als Teil der Imagedefinition bereit.
- Ablaufdatum (Datum für Lebensende): Weisen Sie Ihrer Imagedefinition ein Ablaufdatum zu, damit Automatisierung für das Löschen alter Imagedefinitionen verwendet werden kann.
- Tag: Sie können Tags hinzufügen, wenn Sie Ihre Imagedefinition erstellen. Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Mindest- und Maximalempfehlungen zu vCPU und Arbeitsspeicher: Wenn es für Ihr Image vCPU- und Arbeitsspeicherempfehlungen gibt, können Sie diese Informationen zu Ihrer Imagedefinition hinzufügen.
- Unzulässige Datenträgertypen: Sie können Informationen über die Speicheranforderungen für Ihren virtuellen Computer bereitstellen. Wenn Ihr Image z. B. nicht für normale Festplattenlaufwerke geeignet ist, fügen Sie diese zur Liste „Nicht zulassen“ hinzu.
- Erwerbsplaninformationen für Marketplace-Images: `-PurchasePlanPublisher`, `-PurchasePlanName` und `-PurchasePlanProduct`. Weitere Informationen zu den Erwerbsplaninformationen finden Sie unter [Suchen nach Images in Azure Marketplace](./windows/cli-ps-findimage.md) und [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).


## <a name="image-versions"></a>Imageversionen

Eine **Imageversion** ist, was Sie verwenden, um eine VM zu erstellen. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für die VM zu erstellen. Imageversionen können mehrmals verwendet werden.

Die Eigenschaften einer Imageversion sind die folgenden:

- Versionsnummer: Dies wird als Name der Imageversion verwendet. Es wird immer das folgende Format verwendet: Hauptversion.Nebenversion.Patch. Wenn Sie beim Erstellen einer VM angeben, dass **Neueste** verwendet werden soll, wird basierend auf den höchsten Werten für Hauptversion, dann für Nebenversion und dann für Patch das neueste Image ausgewählt. 
- Source. Die Quelle kann eine VM, ein verwalteter Datenträger, eine Momentaufnahme, ein verwaltetes Image oder eine andere Imageversion sein. 
- Aus aktueller Version ausschließen. Sie können eine Version ausschließen, die nicht als neuste Imageversion verwendet werden soll. 
- Ende der Lebensdauer. Hierbei handelt es sich um ein Datum, nach dem VMs nicht mehr auf Grundlage dieses Images erstellt werden können.


## <a name="generalized-and-specialized-images"></a>Generalisierte und spezialisierte Images

Die Shared Image Gallery unterstützt zwei Betriebssystemzustände. In der Regel ist es erforderlich, dass der zum Erstellen des Images verwendete virtuelle Computer generalisiert wurde, bevor das Image verwendet wird. Durch das Generalisieren werden computer- und benutzerspezifische Informationen aus dem virtuellen Computer entfernt. Für Windows wird das Sysprep-Tool verwendet. Für Linux können Sie den [waagent](https://github.com/Azure/WALinuxAgent)-Parameter `-deprovision` oder `-deprovision+user` verwenden.

Für spezialisierte virtuelle Computer wurden keine computerspezifischen Informationen und Konten entfernt. Virtuellen Computern, die aus spezialisierten Images erstellt wurden, ist zudem kein `osProfile` zugeordnet. Dies bedeutet, dass spezialisierte Images neben Vorteilen auch einige Einschränkungen aufweisen.

- VMs und Skalierungsgruppen, die aus spezialisierten Images erstellt wurden, können schneller aktiv sein und ausgeführt werden. Da sie aus einer Quelle erstellt werden, für die bereits der erste Start erfolgt ist, werden VMs, die aus diesen Images erstellt wurden, schneller gestartet.
- Konten, die für die Anmeldung beim virtuellen Computer verwendet werden können, können auch auf jedem virtuellen Computer verwendet werden, der mit dem aus ihm erstellten spezialisierten Image erstellt wurde.
- Virtuelle Computer haben den **Computernamen** des virtuellen Computers, aus dem das Image erstellt wurde. Sie sollten den Computernamen ändern, um Konflikte zu vermeiden.
- Das `osProfile` gibt an, wie sensible Informationen mithilfe von `secrets` an den virtuellen Computer übermittelt werden. Dies kann Probleme bei KeyVault, WinRM und anderen Funktionen verursachen, die `secrets` im `osProfile` verwenden. In einigen Fällen können Sie verwaltete Dienstidentitäten (Managed Service Identities, MSIs) verwenden, um diese Einschränkungen zu umgehen.

## <a name="regional-support"></a>Regionsunterstützung

Alle öffentlichen Regionen können Zielregionen sein, aber für die Replikation in „Australien, Mitte“ und „Australien, Mitte 2“ muss Ihr Abonnement in die Zulassungsliste aufgenommen werden. Navigieren Sie zu https://azure.microsoft.com/global-infrastructure/australia/contact/, um die Aufnahme eines Abonnements in die Zulassungsliste anzufordern.

## <a name="limits"></a>Einschränkungen 

Pro Abonnement gibt es Einschränkungen hinsichtlich der Bereitstellung von Ressourcen mithilfe des Katalogs mit geteilten Images:
- 100 Kataloge mit freigegebenen Images pro Abonnement und Region
- 1\.000 Imagedefinitionen pro Abonnement und Region
- 10.000 Imageversionen pro Abonnement und Region
- 10 Replikate der Imageversionen pro Abonnement und Region
- Alle an das Image angefügten Datenträger dürfen höchstens 1 TB groß sein.

Weitere Informationen finden Sie unter [Vergleichen der Ressourcennutzung mit Grenzwerten](../networking/check-usage-against-limits.md) in Beispielen dazu, wie Sie Ihre aktuelle Nutzung überprüfen.
 
## <a name="scaling"></a>Skalierung
Im Katalog mit freigegebenen Images können Sie die Anzahl der Replikate angeben, die Azure für die Images verwalten soll. Dies ist in Szenarien mit mehreren VM-Bereitstellungen hilfreich, da die VM-Bereitstellungen auf verschiedene Replikate verteilt werden können. Dadurch wird die Wahrscheinlichkeit verringert, dass der Instanzerstellungsprozess durch die Überlastung eines einzelnen Replikats gedrosselt wird.

Mit dem Katalog mit freigegebenen Images können Sie jetzt bis zu 1.000 VM-Instanzen in einer VM-Skalierungsgruppe bereitstellen (erhöht von 600 mit verwalteten Images). Imagereplikate bieten eine bessere Leistung, Zuverlässigkeit und Konsistenz bei der Bereitstellung.   Sie können in jeder Zielregion ein andere Replikatanzahl festlegen, basierend auf der für die Region erforderlichen Skalierung. Da jedes Replikat eine tiefe Kopie Ihres Images ist, hilft dies dabei, Ihre Bereitstellungen mit jedem zusätzlichen Replikat linear zu skalieren. Natürlich sind wir uns bewusst, dass keine zwei Images oder Regionen identisch sind, doch dies ist unsere allgemeine Richtlinie für die Verwendung von Replikaten in einer Region:

- Bei Bereitstellungen ohne VM-Skalierungsgruppen: Es empfiehlt sich, für je 20 VMs, die Sie gleichzeitig erstellen, ein Replikat beizubehalten. Wenn Sie beispielsweise 120 VMs gleichzeitig mit demselben Image in einer Region erstellen, empfehlen wir, dass Sie mindestens 6 Replikate Ihres Images behalten. 
- Bei Bereitstellungen mit Skalierungsgruppen: Für jede Skalierungsgruppenbereitstellung mit bis zu 600 Instanzen empfiehlt es sich, mindestens ein Replikat beizubehalten. Wenn Sie beispielsweise 5 Skalierungsgruppen gleichzeitig erstellen, wobei jede davon 600 VM-Instanzen in einer Region enthält, die dasselbe Image verwenden, empfehlen wir, dass Sie mindestens 5 Replikate Ihres Images behalten. 

Wir empfehlen Ihnen stets, die Anzahl der Replikate überdimensioniert bereitzustellen, wegen Faktoren wie Größe, Inhalt und Betriebssystemtyp des Images.

![Eine Abbildung, die zeigt, wie Sie Images skalieren können](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Hochverfügbarkeit für Ihre Images

[Zonenredundanter Azure-Speicher (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) bietet Resilienz vor einem Ausfall der Verfügbarkeitszone in der Region. Mit der allgemeinen Verfügbarkeit des Katalogs mit freigegebenen Images können Sie sich entschließen, Ihre Images in ZRS-Konten in Regionen mit Verfügbarkeitszonen zu speichern. 

Sie können außerdem den Kontotyp für jede der Zielregionen auswählen. Der Standardtyp des Speicherkontos ist „Standard_LRS“, aber Sie können „Standard_ZRS“ für Regionen mit Verfügbarkeitszonen auswählen. Überprüfen Sie die regionale Verfügbarkeit von ZRS [hier](../storage/common/storage-redundancy.md).

![Grafik mit ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikation
Der Katalog mit geteilten Images ermöglicht Ihnen außerdem die automatische Replikation Ihrer Images in andere Azure-Regionen. Jede Version eines freigegebenen Images kann entsprechend den Anforderungen Ihrer Organisation in unterschiedliche Regionen repliziert werden. Ein Beispiel ist, immer das neueste Image in mehrere Regionen zu replizieren, während alle ältere Versionen nur in einer Region zur Verfügung stehen. Dadurch können die Speicherkosten für Versionen freigegebener Images gesenkt werden. 

Die Regionen, in die eine Versionen eines freigegebenen Images repliziert wird, können nach der Erstellung aktualisiert werden. Der Zeitaufwand für die Replikation in verschiedene Regionen hängt von der kopierten Datenmenge ab sowie von der Anzahl der Regionen, in die die Version repliziert wird. Dies kann in einigen Fällen mehrere Stunden dauern. Während die Replikation durchgeführt wird, können Sie den Status der Replikation pro Region anzeigen. Sobald die Imagereplikation in einer Region abgeschlossen ist, können Sie einen virtuellen Computer oder eine Skalierungsgruppe über diese Imageversion in der Region bereitstellen.

![Eine Abbildung, die zeigt, wie Sie Images replizieren können](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Zugriff

Da es sich bei dem Katalog mit freigegebenen Images, der Imagedefinition und der Imageversion alles um Ressourcen handelt, lassen sich diese mithilfe der integrierten nativen Azure-RBAC-Steuerelemente freigeben. Mithilfe der rollenbasierten Zugriffssteuerung können Sie diese Ressourcen für andere Benutzer, Dienstprinzipalen und Gruppen freigeben. Sie können sogar Zugriff für Personen freigeben, die sich außerhalb des Mandanten befinden, in dem sie erstellt wurden. Sobald ein Benutzer Zugriff auf die Version eines freigegebenen Images hat, kann er einen virtuellen Computer oder eine VM-Skalierungsgruppe bereitstellen.  Im Folgenden finden Sie die Matrix für das Teilen, die dabei hilft, zu verstehen, worauf der Benutzer Zugriff erhält:

| Geteilt mit Benutzer     | Gemeinsamer Image-Katalog | Imagedefinition | Imageversion |
|----------------------|----------------------|--------------|----------------------|
| Gemeinsamer Image-Katalog | Ja                  | Ja          | Ja                  |
| Imagedefinition     | Nein                   | Ja          | Ja                  |

Zur Erzielung der besten Leistung empfiehlt sich ein Freigeben auf Katalogebene. Wir empfehlen nicht, einzelnen Imageversionen freizugeben. Weitere Informationen zu RBAC finden Sie unter [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC](../role-based-access-control/role-assignments-portal.md).

Images können auch, maßstäblich, freigegeben werden, sogar über eine mehrinstanzenfähige App-Registrierung zwischen Mandanten hinweg. Weitere Informationen zum Freigeben von Images zwischen Mandanten finden Sie unter „Freigeben von Katalog-VM-Images über Azure-Mandanten hinweg“ mithilfe der [Azure-Befehlszeilenschnittstelle](./linux/share-images-across-tenants.md) oder von [PowerShell](./windows/share-images-across-tenants.md).

## <a name="billing"></a>Abrechnung
Für die Verwendung des Katalogs mit geteilten Images fällt keine zusätzliche Gebühren an. Für folgende Ressourcen werden Gebühren berechnet:
-   Speicherkosten für die Speicherung der einzelnen Replikate. Die Speicherkosten werden als Momentaufnahme berechnet und richten sich nach der belegten Größe der Imageversion, der Anzahl der Replikate der Imageversion und der Anzahl der Regionen, in die die Version repliziert wird. 
-   Ausgehender Netzwerkdatenverkehr wird für die Replikation der ersten Imageversion aus der Quellregion in die replizierten Regionen berechnet. Weitere Replikate werden in der Region verarbeitet, sodass keine zusätzlichen Gebühren anfallen. 

Nehmen wir z. B. an, Sie verfügen über ein Image eines 127 GB-Betriebssystemdatenträgers, der nur 10 GB Speicherplatz belegt, und über einen leeren 32 GB-Datenträger. Die belegte Größe jedes Images beträgt nur 10 GB. Das Image wird in drei Regionen repliziert, und jede Region verfügt über zwei Replikate. Insgesamt wird es sechs Momentaufnahmen geben, die jeweils 10 GB verwenden. Ihnen werden die Speicherkosten für jede Momentaufnahme auf der Grundlage der belegten Größe von 10 GB in Rechnung gestellt. Sie zahlen Gebühren für ausgehenden Netzwerkdatenverkehr für das erste Replikat, das in die beiden anderen Regionen kopiert wird. Weitere Informationen zu den Preisen von Momentaufnahmen in den einzelnen Regionen finden Sie unter [Preise für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/). Weitere Informationen zum ausgehenden Netzwerkdatenverkehr finden Sie unter [Bandbreite: Preisübersicht](https://azure.microsoft.com/pricing/details/bandwidth/).


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

- [.NET](/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](/java/azure/?view=azure-java-stable)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](/azure/go/)

## <a name="templates"></a>Vorlagen

Sie können einen Katalog mit freigegebenen Images mithilfe von Vorlagen erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Erstellen eines virtuellen Computers aus einer Imageversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen 

* [Wie kann ich alle Ressourcen eines Katalogs mit geteilten Images über Abonnements hinweg auflisten?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Kann ich mein vorhandenes Image in den Katalog mit geteilten Images verschieben?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Kann ich eine Imageversion von einem speziellen Datenträger erstellen?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Kann ich die Katalogressource mit geteilten Images nach der Erstellung in ein anderes Abonnement verschieben?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Kann ich meine Imageversionen zwischen Clouds replizieren, beispielsweise Azure China 21Vianet, Azure Deutschland und Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Kann ich meine Imageversionen zwischen Abonnements replizieren?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Kann ich Imageversionen zwischen Azure AD-Mandanten freigeben?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Wie lange dauert die Replikation von Imageversionen zwischen Zielregionen?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Was ist der Unterschied zwischen Quellregion und Zielregion?](#what-is-the-difference-between-source-region-and-target-region)
* [Wie gebe ich die Quellregion beim Erstellen der Imageversion an?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Wie gebe ich die Anzahl der Imageversionsreplikate an, die in jeder Region erstellt werden sollen?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Kann ich den Katalog mit geteilten Images an einem anderen als dem Ort für die Imagedefinition und Imageversion erstellen?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Welche Gebühren fallen für die Verwendung des Katalogs mit geteilten Images an?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Welche API-Version sollte ich verwenden, um einen Katalog mit geteilten Images, eine Imagedefinition und eine Imageversion zu erstellen?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Welche API-Version sollte ich verwenden, um einen Katalog mit geteilten Images oder eine Skalierungsgruppe für virtuelle Computer aus der Imageversion zu erstellen?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Kann ich meine VM-Skalierungsgruppe aktualisieren, die mithilfe eines verwalteten Images erstellt wurde, um Shared Image Gallery-Images zu verwenden?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Wie kann ich alle Ressourcen eines Katalogs mit geteilten Images über Abonnements hinweg auflisten?

Um alle Ressourcen eines Katalogs mit geteilten Images über Abonnements hinweg aufzulisten, auf die Sie im Azure-Portal Zugriff haben, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Scrollen Sie auf der Seite nach unten, und wählen Sie **Alle Ressourcen** aus.
1. Wählen Sie alle Abonnements aus, von denen Sie alle Ressourcen auflisten möchten.
1. Suchen Sie nach Ressourcen vom Typ **Shared Image Gallery**.
  
Um alle Ressourcen eines Katalogs mit freigegebenen Images für die Abonnements, auf die Sie Zugriff haben, aufzulisten, verwenden Sie den folgenden Befehl in der Azure-Befehlszeilenschnittstelle:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Weitere Informationen finden Sie unter **Verwalten von Katalogressourcen** mithilfe der [Azure CLI](update-image-resources-cli.md) oder mit [PowerShell](update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Kann ich mein vorhandenes Image in den Katalog mit geteilten Images verschieben?
 
Ja. Es gibt 3 Szenarien, die auf den Typen von Images basieren, die Sie haben können.

 Szenario 1: Wenn Sie ein verwaltetes Image haben, können Sie daraus eine Imagedefinition und eine Imageversion erstellen. Weitere Informationen finden Sie unter **Migrieren von einem verwalteten Image zu einer Imageversion** mithilfe der [Azure CLI](image-version-managed-image-cli.md) oder mit [PowerShell](image-version-managed-image-powershell.md).

 Szenario 2: Wenn Sie ein nicht verwaltetes Image haben, können Sie daraus ein verwaltetes Image erstellen und dann daraus eine Imagedefinition und eine Imageversion erstellen. 

 Szenario 3: Wenn Sie eine VHD in Ihrem lokalen Dateisystem haben, müssen Sie die VHD in ein verwaltetes Image hochladen, und dann können Sie daraus eine Imagedefinition und eine Imageversion erstellen.

- Wenn die VHD von einer Windows-VM stammt, lesen Sie [Hochladen einer generalisierten VHD](./windows/upload-generalized-managed.md).
- Wenn die VHD für eine Linux-VM ist, lesen Sie [Hochladen einer VHD](./linux/upload-vhd.md#option-1-upload-a-vhd).

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Kann ich eine Imageversion von einem speziellen Datenträger erstellen?

Ja, Sie können eine VM aus einem spezialisierten Image erstellen, indem Sie die [CLI](vm-specialized-image-version-cli.md), [PowerShell](vm-specialized-image-version-powershell.md) oder die API verwenden. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Kann ich die Katalogressource mit geteilten Images nach der Erstellung in ein anderes Abonnement verschieben?

Nein, Sie können die Shared Image Gallery-Ressource nicht in ein anderes Abonnement verschieben. Sie können die Imageversionen im Katalog in andere Regionen replizieren oder ein Image aus einem anderen Katalog mithilfe der [Azure CLI](image-version-another-gallery-cli.md) oder mit [PowerShell](image-version-another-gallery-powershell.md) kopieren.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Kann ich meine Imageversionen zwischen Clouds replizieren, beispielsweise Azure China 21Vianet, Azure Deutschland und Azure Government Cloud?

Nein, Sie können Imageversionen nicht zwischen Clouds replizieren.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Kann ich meine Imageversionen zwischen Abonnements replizieren?

Nein, Sie können die Imageversionen zwischen Regionen in einem Abonnement replizieren und mittels rollenbasierter Zugriffssteuerung in anderen Abonnements verwenden.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Kann ich Imageversionen zwischen Azure AD-Mandanten freigeben? 

Ja, Sie können RBAC verwenden, um Imageversionen mandantenübergreifend für Einzelpersonen freizugeben. Wenn Sie jedoch über [PowerShell](./windows/share-images-across-tenants.md) oder [CLI](./linux/share-images-across-tenants.md) maßstäblich freigeben möchten, lesen Sie „Freigeben von Katalog-VM-Images über Azure-Mandanten hinweg“.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Wie lange dauert die Replikation von Imageversionen zwischen Zielregionen?

Die Replikationszeit für Imageversionen ist vollständig abhängig von der Größe des Images und der Anzahl der Regionen, in die es repliziert wird. Allerdings wird als bewährte Methode empfohlen, dass Sie das Image klein halten und die Quell- und Zielregionen möglichst benachbart, um optimale Ergebnisse zu erzielen. Sie können den Status der Replikation mit dem Flag „-ReplicationStatus“ überprüfen.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Was ist der Unterschied zwischen Quellregion und Zielregion?

Quellregion ist die Region, in der Ihre Imageversion erstellt wird, und Zielregionen sind die Regionen, in denen eine Kopie Ihrer Imageversion gespeichert werden soll. Für jede Imageversion können Sie nur eine Quellregion haben. Stellen Sie außerdem sicher, dass Sie beim Erstellen einer Imageversion den Ort der Quellregion als eine der Zielregionen übergeben.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Wie gebe ich die Quellregion beim Erstellen der Imageversion an?

Beim Erstellen einer Imageversion können Sie den Parameter **--location** in der CLI und den Parameter **-Location** in der PowerShell verwenden, um die Quellregion anzugeben. Stellen Sie sicher, dass sich das verwaltete Image, das Sie als Basisimage für die Erstellung der Imageversion verwenden, am selben Ort befindet, an dem Sie die Imageversion erstellen möchten. Stellen Sie außerdem sicher, dass Sie beim Erstellen einer Imageversion den Ort der Quellregion als eine der Zielregionen übergeben.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Wie gebe ich die Anzahl der Imageversionsreplikate an, die in jeder Region erstellt werden sollen?

Es gibt zwei Möglichkeiten, wie Sie die Anzahl der Imageversionsreplikate angeben können, die in jeder Region erstellt werden sollen:
 
1. Die regionale Replikatanzahl, die die Anzahl der Replikate angibt, die Sie pro Region erstellen möchten. 
2. Die allgemeine Replikatanzahl, bei der es sich um die Standardanzahl pro Region handelt für den Fall, dass die regionale Replikatanzahl nicht angegeben ist. 

Um die regionale Replikatanzahl anzugeben, übergeben Sie den Ort zusammen mit der Anzahl der Replikate, die Sie in dieser Region erstellen möchten: „USA, Süden-Mitte=2“. 

Wenn die regionale Replikatanzahl nicht für jeden Ort angegeben ist, entspricht die Standardanzahl der Replikate der allgemeinen Replikatanzahl, die Sie angegeben haben. 

Um die allgemeine Replikatanzahl in der CLI anzugeben, verwenden Sie das Argument **--replica-count** in dem Befehl `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Kann ich den Katalog mit geteilten Images an einem anderen als dem Ort für die Imagedefinition und Imageversion erstellen?

Ja, das ist möglich. Als bewährte Methode empfehlen wir Ihnen aber, dass Sie die Ressourcengruppe, den Katalog mit geteilten Images, die Imagedefinition und die Imageversion am selben Ort halten.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Welche Gebühren fallen für die Verwendung des Katalogs mit geteilten Images an?

Es fallen keine Gebühren für die Verwendung des Diensts für Kataloge mit geteilten Images an, außer den Speichergebühren für das Speichern der Imageversionen sowie den Gebühren für ausgehenden Netzwerkdatenverkehr für die Replikation der Imageversionen aus der Quellregion in Zielregionen.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Welche API-Version sollte ich verwenden, um einen Katalog mit geteilten Images, eine Imagedefinition und eine Imageversion zu erstellen?

Um mit Katalogen mit geteilten Images, Imagedefinitionen und Imageversionen zu arbeiten, empfehlen wir Ihnen die Verwendung der API-Version 2018-06-01. Zonenredundanter Speicher (ZRS) erfordert Version 2019-03-01 oder höher.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Welche API-Version sollte ich verwenden, um einen Katalog mit geteilten Images oder eine Skalierungsgruppe für virtuelle Computer aus der Imageversion zu erstellen?

Für Bereitstellungen von VMs und VM-Skalierungsgruppen mithilfe einer Imageversion wird die Verwendung der API-Version 2018-04-01 oder höher empfohlen.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Kann ich meine VM-Skalierungsgruppe aktualisieren, die mithilfe eines verwalteten Images erstellt wurde, um Shared Image Gallery-Images zu verwenden?

Ja, Sie können die Imagereferenz der Skalierungsgruppe von einem verwalteten Image in ein Shared Image Gallery-Image aktualisieren, sofern der Betriebssystemtyp, die Hyper-V-Generation und das Datenträgerlayout zwischen den Images übereinstimmen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie freigegebene Images mit der [Azure-Befehlszeilenschnittstelle](shared-images-cli.md) oder [PowerShell](shared-images-powershell.md) bereitstellen.

