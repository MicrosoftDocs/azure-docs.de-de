---
title: Migration der StorSimple 8000-Serie zur Azure-Dateisynchronisierung
description: Erfahren Sie, wie Sie eine StorSimple 8100- oder 8600-Appliance zur Azure-Dateisynchronisierung migrieren.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: daa7c657a47414b01197bed3644caefeda98af1c
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512170"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100- und 8600-Migration zur Azure-Dateisynchronisierung

Die StorSimple 8000-Serie wird durch physische, lokale Appliances der Typen 8100 oder 8600 und die zugehörigen Clouddienstkomponenten dargestellt. Die Daten können von jeder dieser Appliances zu einer Umgebung der Azure-Dateisynchronisierung migriert werden. Die Azure-Dateisynchronisierung ist der standardmäßige und strategisch langfristige Azure-Dienst, zu dem StorSimple-Geräte migriert werden können.

Die StorSimple 8000-Serie erreicht im Dezember 2022 das [Ende des Lebenszyklus](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series). Es ist wichtig, möglichst bald mit der Planung der Migration zu beginnen. Dieser Artikel enthält die erforderlichen Hintergrundinformationen und Migrationsschritte für eine erfolgreiche Migration zur Azure-Dateisynchronisierung.

## <a name="phase-1-prepare-for-migration"></a>Phase 1: Vorbereiten der Migration

Dieser Abschnitt enthält die Schritte, die Sie zu Beginn der Migration von StorSimple-Volumes zu Azure-Dateifreigaben ausführen sollten.

### <a name="inventory"></a>Inventarisierung

Wenn Sie mit der Planung Ihrer Migration beginnen, ermitteln Sie zunächst alle StorSimple-Appliances und -Volumes, die Sie migrieren müssen. Anschließend können Sie sich für den Migrationspfad entscheiden, der für Sie am besten geeignet ist.

* Für physische StorSimple-Appliances (Serie 8000) verwenden Sie dieses Migrationshandbuch.
* Für virtuelle Appliances, [StorSimple 1200-Serie, verwenden Sie eine anderes Migrationsanleitung](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Zusammenfassung der Migrationskosten

Migrationen, die von StorSimple-Volumes über Aufträge des Datentransformationsdiensts in einer StorSimple Data Manager-Ressource zu Azure-Dateifreigaben durchgeführt werden, sind kostenlos. Während und nach einer Migration können ggf. andere Kosten anfallen:

* **Netzwerkausgang:** Ihre StorSimple-Dateien befinden sich in einem Speicherkonto in einer bestimmten Azure-Region. Wenn Sie die Azure-Dateifreigaben, zu denen Sie migrieren, in einem Speicherkonto in derselben Azure-Region bereitstellen, entstehen keine Ausgangskosten. Sie können Ihre Dateien im Rahmen dieser Migration in ein Speicherkonto in einer anderen Region verschieben. In diesem Fall werden Ihnen Ausgangskosten in Rechnung gestellt.
* **Azure-Dateifreigabetransaktionen:** Werden Dateien auf eine Azure-Dateifreigabe kopiert (im Rahmen einer Migration oder auch nicht), fallen Transaktionskosten an, weil Dateien und Metadaten geschrieben werden. Es empfiehlt sich, dass Sie Ihre Azure-Dateifreigabe während der Migration auf der für Transaktionen optimierten Ebene starten. Wechseln Sie nach Abschluss der Migration zu der von Ihnen gewünschten Ebene. In den folgenden Phasen ist dies an der entsprechenden Stelle jeweils angegeben.
* **Wechseln einer Ebene einer Azure-Dateifreigabe:** Ein Wechseln der Ebene einer Azure-Dateifreigabe führt zu Kosten für Transaktionen. In den meisten Fällen ist es kostengünstiger, die Ratschläge aus dem vorherigen Punkt zu befolgen.
* **Speicherkosten:** Wenn in dieser Migration damit begonnen wird, Dateien in eine Azure-Dateifreigabe zu kopieren, wird Azure Files-Speicher genutzt und in Rechnung gestellt.
* **StorSimple:** Solange Sie keine Möglichkeit haben, die Bereitstellung der StorSimple-Geräte und -Speicherkonten aufzuheben, fallen weiter StorSimple-Kosten für Speicher, Sicherungen und Geräte an.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direkter Zugriff auf Dateifreigaben im Vergleich mit Azure-Dateisynchronisierung

Azure-Dateifreigaben eröffnen eine ganz neue Welt der Möglichkeiten zum Strukturieren der Bereitstellung Ihrer Dateidienste. Eine Azure-Dateifreigabe ist lediglich eine SMB-Freigabe in der Cloud, die Sie so einrichten können, dass Benutzer direkt über das SMB-Protokoll mit der vertrauten Kerberos-Authentifizierung und vorhandenen NTFS-Berechtigungen (Datei- und Ordnerzugriffssteuerungslisten) darauf zugreifen können. Erfahren Sie mehr über [identitätsbasierten Zugriff auf Azure-Dateifreigaben](storage-files-active-directory-overview.md).

Eine Alternative zum direkten Zugriff ist die [Azure-Dateisynchronisierung](./storage-sync-files-planning.md). Die Azure-Dateisynchronisierung ist das direkte Gegenstück zur StorSimple-Funktionalität, bei der häufig verwendete Dateien lokal zwischengespeichert werden.

Die Azure-Dateisynchronisierung ist ein Microsoft-Clouddienst, der auf zwei Hauptkomponenten basiert:

* Dateisynchronisierung und Cloudtiering.
* Dateifreigaben als nativer Speicher in Azure, auf den über verschiedene Protokolle wie SMB und Datei-REST zugegriffen werden kann.

In Azure-Dateifreigaben werden wichtige Dateigenauigkeitsaspekte, etwa Attribute, Berechtigungen und Zeitstempel, beibehalten. Mit Azure-Dateifreigaben ist es nicht mehr erforderlich, dass eine Anwendung oder ein Dienst die in der Cloud gespeicherten Dateien und Ordner interpretiert. Sie können darauf nativ über vertraute Protokolle und Clients wie den Windows-Datei-Explorer zugreifen. Azure-Dateifreigaben ermöglichen es Ihnen, allgemeine Dateiserverdaten und Anwendungsdaten in der Cloud speichern. Das Sichern einer Azure-Dateifreigabe ist eine integrierte Funktion und kann mit Azure Backup weiter verbessert werden.

In diesem Artikel werden hauptsächlich die Migrationsschritte behandelt. Weitere Informationen zur Azure-Dateisynchronisierung vor der Migration finden Sie in den folgenden Artikeln:

* [Azure-Dateisynchronisierung – Übersicht](./storage-sync-files-planning.md "Übersicht")
* [Azure-Dateisynchronisierung – Bereitstellungsleitfaden](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple-Dienstdatenverschlüsselungs-Schlüssel

Als Sie Ihr StorSimple-Gerät erstmalig eingerichtet haben, wurde ein Dienstdatenverschlüsselungs-Schlüssel generiert, und Sie wurden gebeten, den Schlüssel sicher zu speichern. Dieser Schlüssel wird verwendet, um alle Daten im zugeordneten Azure-Speicherkonto zu verschlüsseln, in dem Ihre Dateien vom StorSimple-Gerät gespeichert werden.

Der Dienstdatenverschlüsselungs-Schlüssel ist für eine erfolgreiche Migration erforderlich. Daher ist es nun erforderlich, dass Sie diesen Schlüssel für jedes der Geräte in Ihrem Bestand aus Ihren Aufzeichnungen abrufen.

Sollten Sie den jeweiligen Schlüssel nicht in Ihren Aufzeichnungen finden, können Sie ihn vom Gerät abrufen. Jedes Gerät hat einen eindeutigen Verschlüsselungsschlüssel. Rufen Sie den Schlüssel wie folgt ab:

* Senden Sie eine Supportanfrage mit Microsoft Azure über die Azure-Portal. Die Anfrage sollte die Seriennummer des StorSimple-Geräts mit dem Hinweis enthalten, dass der „Dienstdatenverschlüsselungs-Schlüssel“ (Service Data Encryption Key) abgerufen werden soll.
* Ein StorSimple-Supporttechniker wird Kontakt mit Ihnen aufnehmen und Sie bitten, eine Bildschirmübertragungssitzung (Screen-Sharing) einzurichten.
* Stellen Sie vor Beginn der Sitzung [über eine serielle Konsole](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) oder über eine [Remote-PowerShell-Sitzung](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple) eine Verbindung mit Ihrer StorSimple-Appliance her.

> [!CAUTION]
> Bei der Entscheidung, wie Sie die Verbindung mit Ihrer StorSimple-Appliance herstellen, sollten Sie Folgendes beachten:
>
> * Eine Verbindung über eine HTTPS-Sitzung ist die sicherste und empfohlene Option.
> * Das direkte Verbinden mit der seriellen Konsole des Geräts ist sicher, was für das Verbinden mit der seriellen Konsole über Netzwerkswitches nicht gilt.
> * HTTP-Sitzungsverbindungen sind eine Option, aber sie sind *nicht verschlüsselt*. Sie sind nur zu empfehlen, wenn sie in einem geschlossenen vertrauenswürdigen Netzwerk verwendet werden.

### <a name="storsimple-volume-backups"></a>StorSimple-Volumesicherungen

StorSimple bietet differenzielle Sicherungen auf Volumeebene. Azure-Dateifreigaben bieten diese Funktionalität ebenfalls in Form der sogenannten Freigabemomentaufnahmen.

Entscheiden Sie, ob Sie als Bestandteil der Migration auch alle Sicherungen verschieben möchten.

> [!CAUTION]
> Hören Sie hier auf, wenn Sie Sicherungen von StorSimple-Volumes migrieren müssen.
>
> Sie können derzeit nur die neueste Volumesicherung migrieren. Unterstützung für die Migration von Sicherungen wird ab Ende 2020 verfügbar sein. Wenn Sie jetzt starten, können Sie Ihre Sicherungen später nicht mehr anfügen. In der nächsten Version müssen Sicherungen von der ältesten bis zur neuesten in die Azure-Dateifreigaben „zurückgespielt“ werden, wobei dazwischen Azure-Dateifreigabemomentaufnahmen erstellt werden.

Wenn Sie nur die Livedaten migrieren möchten und keine Sicherungen benötigen, können Sie weiter diese Anleitung befolgen. Falls Sie Sicherungen nur für kurze Zeit benötigen, z. B. ein oder zwei Monate, können Sie die Migration jetzt auch fortsetzen und die Bereitstellung Ihrer StorSimple-Ressourcen dann nach Ablauf dieses Zeitraums aufheben. Dieser Ansatz ermöglicht es Ihnen, auf der Azure-Dateifreigabeseite so viele Sicherungsverläufe zu erstellen, wie Sie benötigen. Für den Zeitraum, in dem Sie beide Systeme betreiben, fallen zusätzliche Kosten an. Daher sollten Sie diesen Ansatz nicht in Betracht ziehen, wenn Sie eine längerfristige Sicherungsaufbewahrung benötigen.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Zuordnen Ihrer vorhandenen StorSimple-Volumes zu Azure-Dateifreigaben

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Anzahl von Speicherkonten

Ihre Migration profitiert wahrscheinlich von einer Bereitstellung mehrerer Speicherkonten, von denen jedes eine kleinere Anzahl von Azure-Dateifreigaben enthält.

Wenn Ihre Dateifreigaben intensiv genutzt werden (Nutzung durch viele Benutzer oder Anwendungen), wird unter Umständen für zwei Azure-Dateifreigaben die Leistungsgrenze Ihres Speicherkontos erreicht. Aus diesem Grund empfiehlt es sich, zu mehreren Speicherkonten zu migrieren, wobei jedes seine eigenen individuellen Dateifreigaben hat, und normalerweise nicht mehr als zwei oder drei Freigaben pro Speicherkonto zu nutzen.

Als bewährte Methode empfiehlt es sich, Speicherkonten mit je einer Dateifreigabe bereitzustellen. Sie können mehrere Azure-Dateifreigaben in einem Speicherkonto zusammenfassen, falls Sie darin über Archivierungsfreigaben verfügen.

Diese Überlegungen gelten eher für [direkten Cloudzugriff](#direct-share-access-vs-azure-file-sync) (über eine Azure-VM oder einen Azure-Dienst) als für die Azure-Dateisynchronisierung. Wenn Sie Freigaben lediglich für die Azure-Dateisynchronisierung verwenden möchten, können Sie mehrere Freigaben in einem einzelnen Azure-Speicherkonto gruppieren. Unter Umständen kann auch das Verschieben einer App in die Cloud per Lift & Shift-Vorgang ratsam sein, die dann direkt auf eine Dateifreigabe zugreifen würde. Oder Sie könnten mit der Nutzung eines Diensts in Azure beginnen, der ebenfalls von höheren IOPS- und Durchsatzzahlen profitieren würde.

Nachdem Sie eine Liste mit Ihren Freigaben erstellt haben, ordnen Sie jede Freigabe dem Speicherkonto zu, unter dem diese sich befinden soll.

> [!IMPORTANT]
> Entscheiden Sie sich für eine Azure-Region, und stellen Sie sicher, dass jedes Speicherkonto und jede Ressource der Azure-Dateisynchronisierung mit der von Ihnen ausgewählten Region übereinstimmt.

### <a name="phase-1-summary"></a>Zusammenfassung von Phase 1

Am Ende der Phase 1:

* Sie haben einen guten Überblick über Ihre StorSimple-Geräte und -Volumes.
* Der Datentransformationsdienst hat Zugriff auf Ihre StorSimple-Volumes in der Cloud, weil Sie Ihren Dienstdatenverschlüsselungs-Schlüssel für jedes StorSimple-Gerät abgerufen haben.
* Sie haben einen Plan, in dem festgelegt ist, welche Volumes migriert werden müssen und auch wie Ihre Volumes der entsprechenden Anzahl von Azure-Dateifreigaben und Speicherkonten zugeordnet werden sollen.

> [!CAUTION]
> Wenn Sie Sicherungen von StorSimple-Volumes migrieren müssen, **HÖREN SIE HIER AUF**.
>
> Dieser Migrationsansatz basiert auf neuen Funktionen des Datentransformationsdiensts, mit denen derzeit keine Sicherungen migriert werden können. Unterstützung für die Migration von Sicherungen wird ab Ende 2020 verfügbar sein. Sie können derzeit nur Ihre Livedaten migrieren. Wenn Sie jetzt starten, können Sie Ihre Sicherungen später nicht mehr anfügen. Sicherungen müssen von der ältesten bis zur neuesten bis hin zu den Livedaten in die Azure-Dateifreigaben „zurückgespielt“ werden, wobei dazwischen Azure-Dateifreigabemomentaufnahmen erstellt werden.

Wenn Sie nur die Livedaten migrieren möchten und keine Sicherungen benötigen, können Sie weiter diese Anleitung befolgen.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Phase 2: Bereitstellen von Azure-Speicher- und -Migrationsressourcen

In diesem Abschnitt werden Aspekte zum Bereitstellen der verschiedenen Ressourcentypen erläutert, die in Azure benötigt werden. In einigen Ressourcen befinden sich Ihre Daten nach der Migration, und einige werden ausschließlich für die Migration benötigt. Starten Sie die Bereitstellung von Ressourcen erst, nachdem Sie Ihren Bereitstellungsplan fertiggestellt haben. Es ist schwierig bzw. in einigen Fällen auch unmöglich, bestimmte Aspekte Ihrer Azure-Ressourcen zu ändern, nachdem sie bereitgestellt wurden.

### <a name="deploy-storage-accounts"></a>Bereitstellen von Speicherkonten

Sie müssen wahrscheinlich mehrere Azure-Speicherkonten bereitstellen. Jedes dieser Konten enthält eine kleinere Anzahl von Azure-Dateifreigaben, wie dies in Ihrem Bereitstellungsplan vorgesehen ist, den Sie im vorherigen Abschnitt dieses Artikels fertiggestellt haben. Wechseln Sie zum Azure-Portal, um [Ihre geplanten Speicherkonten bereitzustellen](../common/storage-account-create.md#create-a-storage-account). Es bietet sich an, die unten angegebenen grundlegenden Einstellungen für jedes neue Speicherkonto zu berücksichtigen.

#### <a name="subscription"></a>Abonnement

Sie können das Abonnement verwenden, das Sie für Ihre StorSimple-Bereitstellung verwendet haben. Sie können aber auch ein anderes Abonnement verwenden. Die einzige Einschränkung besteht darin, dass sich Ihr Abonnement auf demselben Azure Active Directory-Mandanten wie das StorSimple-Abonnement befinden muss. Verschieben Sie das StorSimple-Abonnement ggf. auf den richtigen Mandanten, bevor Sie eine Migration starten. Sie können nur das gesamte Abonnement verschieben. Einzelne StorSimple-Ressourcen können nicht auf einen anderen Mandanten oder in ein anderes Abonnement verschoben werden.

#### <a name="resource-group"></a>Ressourcengruppe

Ressourcengruppen unterstützen beim Strukturieren von Ressourcen und administrativen Verwaltungsberechtigungen. Erfahren Sie mehr über [Ressourcengruppen in Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Speicherkontoname

Der Name Ihres Speicherkontos wird Bestandteil einer URL, und für ihn gelten bestimmte Zeichenbeschränkungen. In Ihrer Namenskonvention sollten Sie berücksichtigen, dass Speicherkontonamen weltweit eindeutig sein müssen, nur Kleinbuchstaben und Ziffern enthalten können, zwischen 3 und 24 Zeichen lang sein müssen und keine Sonderzeichen wie Bindestriche oder Unterstriche aufweisen dürfen. Weitere Informationen finden Sie unter [Benennungsregeln für Azure-Speicherressourcen](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Standort

Der Standort bzw. die Azure-Region eines Speicherkontos ist äußerst wichtig. Wenn Sie Azure-Dateisynchronisierung verwenden, müssen sich Ihre Speicherkonten in derselben Region befinden wie die Speichersynchronisierungsdienst-Ressource. Die Azure-Region, die Sie auswählen, sollte sich in der Nähe bzw. am Mittelpunkt Ihrer lokalen Server und Benutzer befinden. Nachdem Ihre Ressource bereitgestellt wurde, können Sie die zugehörige Region nicht mehr ändern.

Sie können eine Region auswählen, die nicht mit der identisch ist, in der sich Ihre StorSimple-Daten (Speicherkonto) aktuell befinden.

> [!IMPORTANT]
> Wenn Sie eine andere Region als den aktuellen Standort Ihres StorSimple-Speicherkontos auswählen, werden [Gebühren für ausgehenden Datenverkehr](https://azure.microsoft.com/pricing/details/bandwidth) fällig. Die Daten werden aus der StorSimple-Region in die Region Ihres neuen Speicherkontos gesendet. Wenn Sie innerhalb derselben Azure-Region bleiben, fallen keine Bandbreitengebühren an.

#### <a name="performance"></a>Leistung

Sie haben die Möglichkeit, Storage Premium (SSD) für Azure-Dateifreigaben oder Standardspeicher auszuwählen. Standardspeicher umfasst [verschiedenen Ebenen für eine Dateifreigabe](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Standardspeicher ist die richtige Option für die meisten Kunden, die von StorSimple migrieren.

Sie sind immer noch nicht sicher?

* Wählen Sie Storage Premium aus, wenn Sie die [Leistung einer Premium-Azure-Dateifreigabe](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) benötigen.
* Wählen Sie Standardspeicher für allgemeine Dateiserverworkloads aus, einschließlich heißer Daten und Archivdaten. Wählen Sie Standardspeicher auch aus, wenn Azure-Dateisynchronisierung die einzige Workload auf der Freigabe in der Cloud ist.

#### <a name="account-kind"></a>Kontoart

* Wählen Sie für Standardspeicher die Option *StorageV2 (Universell V2)* aus.
* Wählen Sie für Premium-Dateifreigaben die Option *FileStorage* aus.

#### <a name="replication"></a>Replikation

Es sind mehrere Replikationseinstellungen verfügbar. Erfahren Sie mehr über die verschiedenen Replikationstypen.

Wählen Sie eine dieser beiden Optionen aus:

* *Lokal redundanter Speicher (LRS)* :
* *Zonenredundanter Speicher (ZRS)* : Ist nicht in allen Azure-Regionen verfügbar.

> [!NOTE]
> Nur die Redundanztypen LRS und ZRS sind mit den großen Azure-Dateifreigaben mit einer Kapazität von 100 TiB kompatibel.

Alle Varianten des georedundanten Speichers (GRS) werden derzeit nicht unterstützt. Sie können den Redundanztyp zu einem späteren Zeitpunkt ändern und zu GRS wechseln, sobald dieser Typ in Azure unterstützt wird.

#### <a name="enable-100-tib-capacity-file-shares"></a>Aktivieren von Dateifreigaben mit 100 TiB Kapazität

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Registerkarte „Erweitert“ im Azure-Portal, auf der ein Speicherkonto erstellt werden kann":::
    :::column-end:::
    :::column:::
        Im Abschnitt **Erweitert** des Assistenten für neue Speicherkonten im Azure-Portal können Sie Unterstützung für **Große Dateifreigaben** in diesem Speicherkonto aktivieren. Falls Ihnen diese Option nicht zur Verfügung steht, haben Sie wahrscheinlich den falschen Redundanztyp ausgewählt. Sie dürfen nur LRS oder ZRS auswählen, damit diese Option verfügbar wird.
    :::column-end:::
:::row-end:::

Die Entscheidung für die großen Dateifreigaben mit 100 TiB Kapazität hat mehrere Vorteile:

* Die Leistung wird im Vergleich zu den kleineren Dateifreigaben mit 5 TiB Kapazität deutlich erhöht (z. B. IOPS-Verzehnfachung).
* Ihre Migration wird erheblich schneller abgeschlossen.
* Sie stellen sicher, dass eine Dateifreigabe genügend Kapazität für alle Daten hat, die Sie zur Freigabe migrieren.
* Zukünftige Vergrößerungen sind abgedeckt.

### <a name="azure-file-shares"></a>Azure-Dateifreigaben

Nachdem Sie Ihre Speicherkonten erstellt haben, navigieren Sie zum Abschnitt **Dateifreigabe** des Speicherkontos und stellen die entsprechende Anzahl von Azure-Dateifreigaben gemäß Ihrem Migrationsplan aus Phase 1 bereit. Es bietet sich an, die unten angegebenen grundlegenden Einstellungen für Ihre neuen Dateifreigaben in Azure zu berücksichtigen.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Azure-Portal-Screenshot des neuen Dialogfelds „Neue Dateifreigabe“":::
    :::column-end:::
    :::column:::
        </br>**Name**</br>Kleinbuchstaben, Zahlen und Bindestriche werden unterstützt.</br></br>**Kontingent**</br>„Kontingent“ ist hier vergleichbar mit einer harten SMB-Kontingentgrenze auf einer Windows Server-Instanz. Sie sollten in diesem Fall nach Möglichkeit kein Kontingent festlegen, da die Migration und andere Dienste fehlschlagen, wenn das Kontingent erreicht wird.</br></br>**Tarife**</br>Wählen Sie für Ihre neue Dateifreigabe die Option **Transaktionsoptimiert** aus. Während der Migration werden viele Transaktionen durchgeführt. Es ist kostengünstiger, Ihren Tarif später so zu ändern, wie dies für Ihre Workload am besten geeignet ist.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Die Azure-Ressource, die der Ihre Migrationsaufträge dann enthalten sind, wird als **StorSimple Data Manager** bezeichnet. Wählen Sie die Option **Neue Ressource** aus, und suchen Sie danach. Klicken Sie anschließend auf **Erstellen**.

Diese temporäre Ressource wird für die Orchestrierung verwendet. Sie heben die Bereitstellung dafür auf, nachdem Ihre Migration abgeschlossen ist. Sie sollte in derselben Umgebung (Abonnement, Ressourcengruppe und Region) wie Ihr StorSimple-Speicherkonto bereitgestellt werden.

### <a name="azure-file-sync"></a>Azure-Dateisynchronisierung

Bei der Azure-Dateisynchronisierung können Sie die lokale Zwischenspeicherung für die Dateien hinzufügen, auf die am häufigsten zugegriffen wird. Ähnlich wie die Zwischenspeicherungsfunktionen von StorSimple bietet die Cloudtieringfunktionalität der Azure-Dateisynchronisierung lokale Zugriffslatenz in Kombination mit verbesserter Kontrolle der verfügbaren Cachekapazität bei der Synchronisierung der Windows Server-Instanz und von mehreren Standorten. Wenn Sie einen lokalen Cache benötigen, bereiten Sie in Ihrem lokalen Netzwerk eine Windows Server-VM (physische Server und Failovercluster werden ebenfalls unterstützt) mit genügend Kapazität für direkt angeschlossenen Speicher vor.

> [!IMPORTANT]
> Richten Sie die Azure-Dateisynchronisierung noch nicht ein. Es ist ratsam, die Azure-Dateisynchronisierung einzurichten, nachdem die Migration Ihrer Freigabe beendet wurde. Die Bereitstellung der Azure-Dateisynchronisierung sollte nicht vor Beginn von Phase 4 einer Migration starten.

### <a name="phase-2-summary"></a>Zusammenfassung von Phase 2

Am Ende von Phase 2 haben Sie Ihre Speicherkonten und alle zugehörigen Azure-Dateifreigaben bereitgestellt. Darüber hinaus verfügen Sie über eine StorSimple Data Manager-Ressource. Sie verwenden diese Ressource in Phase 3, wenn Sie Ihre Migrationsaufträge konfigurieren.

## <a name="phase-3-create-and-run-a-migration-job"></a>Phase 3: Erstellen und Ausführen eines Migrationsauftrags

In diesem Abschnitt ist beschrieben, wie Sie einen Migrationsauftrag einrichten und sorgfältig die Verzeichnisse auf einem StorSimple-Volume zuordnen, die in die von Ihnen ausgewählte Azure-Zieldateifreigabe kopiert werden sollen. Navigieren Sie zunächst zu Ihrem StorSimple Data Manager, suchen Sie im Menü nach der Option **Auftragsdefinitionen**, und wählen Sie **+ Auftragsdefinition** aus. Der Zielspeichertyp ist die standardmäßige **Azure-Dateifreigabe**.

![Migrationsauftragstypen für die StorSimple 8000-Serie](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Screenshot des Azure-Portal-Abschnitts „Auftragsdefinitionen“ mit dem neuen Dialogfeld „Auftragsdefinitionen“, in dem nach dem Typ des Auftrags gefragt wird: Kopieren in eine Dateifreigabe oder einen Blobcontainer.")

> [!IMPORTANT]
> Bevor Sie einen Migrationsauftrag ausführen, sollten Sie alle automatisch geplanten Sicherungen Ihrer StorSimple-Volumes beenden.

:::row:::
    :::column:::
        ![Migrationsauftrag für die StorSimple 8000-Serie](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Screenshot: Auftragserstellungsformular für einen Datentransformationsdienst-Auftrag")
    :::column-end:::
    :::column:::
        **Auftragsdefinitionsname**</br>Mit diesem Namen sollten die zu verschiebenden Dateien angegeben werden. Es hat sich bewährt, einen Namen zu vergeben, der dem Ihrer Azure-Dateifreigabe ähnelt. </br></br>**Speicherort, in dem der Auftrag ausgeführt wird**</br>Sie müssen hierbei die Region auswählen, in der sich auch Ihr StorSimple-Speicherkonto befindet. Falls diese Region nicht verfügbar ist, wählen Sie eine Region in deren Nähe aus. </br></br><h3>`Source`</h3>**Quellabonnement**</br>Wählen Sie das Abonnement aus, unter dem Sie Ihre StorSimple-Geräte-Manager-Ressource speichern. </br></br>**StorSimple-Ressource**</br>Wählen Sie Ihren StorSimple-Geräte-Manager aus, bei dem Ihre Appliance registriert ist. </br></br>**Verschlüsselungsschlüssel für Dienstdaten**</br>Lesen Sie den [vorherigen Abschnitt dieses Artikels](#storsimple-service-data-encryption-key), falls Sie den Schlüssel nicht in Ihren Aufzeichnungen finden können. </br></br>**Device**</br>Wählen Sie das StorSimple-Gerät aus, auf dem sich das Volume befindet, zu dem Sie migrieren möchten. </br></br>**Umfang**</br>Wählen Sie das Quellvolume aus. Später können Sie entscheiden, ob das gesamte Volume oder Unterverzeichnisse in die Azure-Zieldateifreigabe migriert werden sollen. </br></br><h3>Ziel</h3>Wählen Sie das Abonnement, das Speicherkonto und die Azure-Dateifreigabe als Ziel dieses Migrationsauftrags aus.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Die letzte Volumesicherung wird dazu verwendet, die Migration auszuführen. Es muss mindestens eine Volumesicherung vorhanden sein, denn andernfalls schlägt der Auftrag fehl. Stellen Sie außerdem sicher, dass die neueste Sicherung relativ aktuell ist, um die Zeitspanne zur aktiven Freigabe so kurz wie möglich zu halten. Es kann sinnvoll sein, eine weitere Volumesicherung manuell auszulösen und abzuschließen, *bevor* der soeben erstellte Auftrag ausgeführt wird.

### <a name="directory-mapping"></a>Verzeichniszuordnung

Die Verzeichniszuordnung ist für Ihren Migrationsauftrag optional. Wenn Sie diesen Abschnitt leer lassen, werden *alle* Dateien und Ordner im Stammverzeichnis Ihres StorSimple-Volumes in das Stammverzeichnis Ihrer Azure-Zieldateifreigabe verschoben. In den meisten Fällen ist das Speichern der Inhalte eines gesamten Volumes auf einer Azure-Dateifreigabe nicht der beste Ansatz. Häufig ist es besser, den Inhalt eines Volumes auf mehrere Dateifreigaben in Azure aufzuteilen. Falls Sie noch keinen Plan erstellt haben, helfen Ihnen die Informationen unter [Zuordnen Ihrer vorhandenen StorSimple-Volumes zu Azure-Dateifreigaben](#map-your-existing-storsimple-volumes-to-azure-file-shares) weiter.

Im Rahmen Ihres Migrationsplans haben Sie ggf. die Entscheidung getroffen, dass die Ordner auf einem StorSimple-Volume auf mehrere Azure-Dateifreigaben aufgeteilt werden müssen. Ist dies der Fall, können Sie die Aufteilung wie folgt vornehmen:

1. Definieren mehrerer Aufträge zum Migrieren der Ordner auf einem Volume. Jeder verfügt über dieselbe StorSimple-Volumequelle, aber eine andere Azure-Dateifreigabe als Ziel.
1. Geben Sie genau an, welche Ordner aus dem StorSimple-Volume in die angegebene Dateifreigabe migriert werden müssen. Verwenden Sie hierfür den Abschnitt **Verzeichniszuordnung** des Auftragserstellungsformulars, und halten Sie sich an die spezifische [Zuordnungssemantik](#semantic-elements).

> [!IMPORTANT]
> Die Pfade und Zuordnungsausdrücke in diesem Formular können nicht überprüft werden, wenn das Formular übermittelt wird. Sind Zuordnungen falsch angegeben, kann ein Auftrag entweder vollständig fehlschlagen oder zu einem unerwünschten Ergebnis führen. In diesem Fall empfiehlt es sich in der Regel, die Azure-Dateifreigabe zu löschen, sie erneut zu erstellen und dann die Zuordnungsanweisungen in einem neuen Migrationsauftrag für die Freigabe zu korrigieren. Durch Ausführen eines neuen Auftrags mit korrigierten Zuordnungsanweisungen können nicht berücksichtigte Ordner bestimmt und auf die vorhandene Freigabe übertragen werden. Allerdings können so nur Ordner erfasst werden, die aufgrund von falsch geschriebenen Pfaden nicht berücksichtigt wurden.

#### <a name="semantic-elements"></a>Semantikelemente

Eine Zuordnung wird von links nach rechts formuliert: [\Quellpfad] \> [\Zielpfad].

|Semantisches Zeichen          | Bedeutung  |
|:---------------------------|:---------|
| **\\**                     | Kennzeichen für Stammebene.       |
| **\>**                     | Operator für [Quelle] und [Ziel] der Zuordnung.     |
|**\|** oder EINGABETASTE (neue Zeile) | Trennzeichen von zwei Anweisungen für die Ordnerzuordnung. </br>Alternativ können Sie auf dieses Zeichen verzichten und die **EINGABETASTE** drücken, um den nächsten Zuordnungsausdruck in seiner eigenen Zeile zu erhalten.        |

### <a name="examples"></a>Beispiele
Verschiebt den Inhalt des Ordners *User Data* in das Stammverzeichnis der Zieldateifreigabe:
``` console
\User data > \
```
Verschiebt den gesamten Volumeinhalt in einen neuen Pfad in der Zieldateifreigabe:
``` console
\ > \Apps\HR tracker
```
Verschiebt den Inhalt des Quellordners unter einen neuen Pfad auf der Zieldateifreigabe:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Sortiert mehrere Quellspeicherorte in einer neuen Verzeichnisstruktur:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantikregeln

* Geben Sie Ordnerpfade immer relativ zur Stammebene an.
* Beginnen Sie jeden Ordnerpfad mit dem folgenden Kennzeichen für die Stammebene: „\\“.
* Fügen Sie keine Laufwerkbuchstaben ein.
* Wenn Sie mehrere Pfade angeben, dürfen sich die Quell- und Zielpfade nicht überlappen:</br>
   Beispiel für eine ungültige Quellpfadüberlappung:</br>
    *\\Ordner\1 > \\Ordner*</br>
    *\\Ordner\\1\\2 > \\Ordner2*</br>
   Beispiel für eine ungültige Zielpfadüberlappung:</br>
   *\\Ordner > \\*</br>
   *\\2 > \\*</br>
* Nicht vorhandene Quellordner werden ignoriert.
* Ordnerstrukturen, die auf dem Ziel nicht vorhanden sind, werden erstellt.
* Wie bei Windows wird die Groß-/Kleinschreibung für Ordnernamen nicht beachtet und beibehalten.

> [!NOTE]
> Der Inhalt des Ordners *\System Volume Information* und der Inhalt von *$Recycle.Bin* auf Ihrem StorSimple-Volume werden vom Transformationsauftrag nicht kopiert.

### <a name="phase-3-summary"></a>Zusammenfassung von Phase 3

Am Ende von Phase 3 haben Sie Ihre Aufträge des Datentransformationsdiensts für die Umstellung von StorSimple-Volumes auf Azure-Dateifreigaben ausgeführt. Sie können nun entweder die Azure-Dateisynchronisierung für die Freigabe einrichten (nachdem die Migrationsaufträge für eine Freigabe abgeschlossen wurden) oder für Ihre Information-Worker und Apps den Zugriff auf die Azure-Dateifreigabe festlegen.

## <a name="phase-4-access-your-azure-file-shares"></a>Phase 4: Zugreifen auf Ihre Azure-Dateifreigaben

Es gibt zwei Hauptstrategien für das Zugreifen auf Ihre Azure-Dateifreigaben:

* **Azure-Dateisynchronisierung:** Führen Sie die [Bereitstellung der Azure-Dateisynchronisierung](#deploy-azure-file-sync) auf einer lokalen Windows Server-Instanz durch. Die Azure-Dateisynchronisierung verfügt genauso wie StorSimple über alle Vorteile eines lokalen Caches.
* **Direkter Zugriff auf Dateifreigaben:** [Stellen Sie den direkten Zugriff auf Dateifreigaben bereit](#deploy-direct-share-access). Verwenden Sie diese Strategie, wenn Ihr Zugriffsszenario für eine bestimmte Azure-Dateifreigabe nicht vom lokalen Zwischenspeichern profitiert oder Sie keine Möglichkeit mehr haben, eine lokale Windows Server-Instanz zu hosten. Hier greifen Ihre Benutzer und Apps weiterhin über das SMB-Protokoll auf SMB-Freigaben zu. Diese Freigaben befinden sich nicht mehr auf einem lokalen Server, sondern direkt in der Cloud.

Sie sollten bereits in [Phase 1](#phase-1-prepare-for-migration) dieser Anleitung die Entscheidung getroffen haben, welche Option für Sie am besten geeignet ist.

Im Rest dieses Abschnitts liegt der Schwerpunkt auf Bereitstellungsanweisungen.

### <a name="deploy-azure-file-sync"></a>Bereitstellen der Azure-Dateisynchronisierung

Es ist an der Zeit, einen Teil der Azure-Dateisynchronisierung bereitzustellen.

1. Erstellen Sie die Cloudressource für die Azure-Dateisynchronisierung.
1. Stellen Sie den Azure-Dateisynchronisierungs-Agent auf Ihrem lokalen Server bereit.
1. Registrieren Sie den Server bei der Cloudressource.

Erstellen Sie noch keine Synchronisierungsgruppen. Das Einrichten der Synchronisierung mit einer Azure-Dateifreigabe sollte erst erfolgen, nachdem Ihre Aufträge für die Migration zu einer Azure-Dateifreigabe abgeschlossen sind. Wenn Sie mit der Nutzung der Azure-Dateisynchronisierung beginnen, bevor Sie die Migration abgeschlossen haben, wird die Migration unnötig erschwert. Sie können dann nicht einfach erkennen, wann es an der Zeit ist, eine Übernahme einzuleiten.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Bereitstellen der Cloudressource für die Azure-Dateisynchronisierung

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Wenn Sie möchten, dass sich Ihre Daten nach Abschluss der Migration in einer anderen Azure-Region als bisher befinden, sollten Sie den Speichersynchronisierungsdienst in der Region bereitstellen, in der sich die Zielspeicherkonten für diese Migration befinden.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Bereitstellen einer lokalen Windows Server-Instanz

* Erstellen Sie eine Windows Server 2019-Instanz (mindestens 2012 R2) als virtuellen Computer oder physischen Server. Ein Windows Server-Failovercluster wird ebenfalls unterstützt. Verwenden Sie nicht wieder den Server, den Sie StorSimple 8100 oder 8600 vorgeschaltet haben.
* Stellen Sie einen direkt angeschlossenen Speicher bereit, oder fügen Sie ihn hinzu. NAS (Network-Attached Storage) wird nicht unterstützt.

Wir empfehlen Ihnen, für Ihre neue Windows Server-Instanz eine gleich große oder größere Menge an Speicher bereitzustellen, als für Ihre StorSimple 8100- oder 8600-Appliance lokal für die Zwischenspeicherung zur Verfügung steht. Sie verwenden die Windows Server-Instanz genauso wie die StorSimple-Appliance. Falls diese über die gleiche Speichermenge wie die Appliance verfügt, sollte das Verhalten bei der Zwischenspeicherung ähnlich oder gleich sein. Sie können für Ihre Windows Server-Instanz wie gewünscht Speicher hinzufügen oder entfernen. Mit dieser Funktion können Sie die Größe des lokalen Volumes und die Menge des für die Zwischenspeicherung verfügbaren lokalen Speichers anpassen.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Vorbereiten der Windows Server-Instanz für die Dateisynchronisierung

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Konfigurieren der Azure-Dateisynchronisierung auf der Windows Server-Instanz

Die registrierte lokale Windows Server-Instanz muss für diesen Prozess vorbereitet und mit dem Internet verbunden sein.

> [!IMPORTANT]
> Bevor Sie die weiteren Schritte ausführen, muss Ihre StorSimple-Migration von Dateien und Ordnern in die Azure-Dateifreigabe abgeschlossen sein. Stellen Sie sicher, dass keine weiteren Änderungen an der Dateifreigabe vorgenommen werden.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Vergewissern Sie sich, dass das Cloudtiering aktiviert ist. Cloudtiering ist ein Feature der Azure-Dateisynchronisierung, das es dem lokalen Server ermöglicht, weniger Speicherkapazität als in der Cloud zu haben, aber trotzdem über den vollständigen Namespace zu verfügen. Lokal interessante Daten werden zudem für eine schnelle, lokale Zugriffsleistung lokal zwischengespeichert. Ein weiterer Grund für die Aktivierung des Cloudtierings in diesem Schritt ist, dass wir in dieser Phase keine Dateiinhalte synchronisieren möchten. Hier soll nur der Namespace verschoben werden.

### <a name="deploy-direct-share-access"></a>Bereitstellen von direktem Zugriff auf Dateifreigaben

:::row:::
    :::column:::
        [![Ausführliche Anleitung und Demo dazu, wie Azure-Dateifreigaben sicher direkt für Information-Worker und Apps verfügbar gemacht werden können: Klicken Sie, um das Video wiederzugeben!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Dieses Video ist eine Anleitung und Demo dazu, wie Azure-Dateifreigaben in fünf einfachen Schritte für Information-Worker und Apps auf sichere Weise direkt verfügbar gemacht werden können.</br>
        In diesem Video wird auf dedizierte Dokumentation für einige Themen verwiesen:

* [Übersicht über Identitäten](storage-files-active-directory-overview.md)
* [Übersicht – lokale Active Directory Domain Services-Authentifizierung über SMB für Azure-Dateifreigaben](storage-files-identity-auth-active-directory-enable.md)
* [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md)
* [Konfigurieren von Azure Files-Netzwerkendpunkten](storage-files-networking-endpoints.md)
* [Konfigurieren eines Site-to-Site-VPN zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md)
* [Konfigurieren eines P2S-VPN (Point-to-Site) unter Windows zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-windows.md)
* [Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-linux.md)
* [Konfigurieren der DNS-Weiterleitung für Azure Files](storage-files-networking-dns.md)
* [Übersicht über DFS-Namespaces](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Zusammenfassung von Phase 4

In dieser Phase haben Sie mehrere Datentransformationsdienst-Aufträge in Ihrem StorSimple Data Manager erstellt und ausgeführt. Mit diesen Aufträgen wurden Ihre Dateien und Ordner in Azure-Dateifreigaben migriert. Darüber hinaus haben Sie die Azure-Dateisynchronisierung bereitgestellt oder Ihr Netzwerk und Ihre Speicherkonten für den direkten Zugriff auf Dateifreigaben vorbereitet.

## <a name="phase-5-user-cut-over"></a>Phase 5: Benutzerübernahme

In dieser Phase geht es darum, Ihre Migration abzuschließen:

* Planen Sie Ihre Downtime.
* Informieren Sie sich über alle Änderungen, die Ihre Benutzer und Apps auf der StorSimple-Seite vorgenommen haben, während die Datentransformationsaufträge in Phase 3 ausgeführt wurden.
* Führen Sie für die Benutzer ein Failover zur neuen Windows Server-Instanz aus, indem Sie die Azure-Dateisynchronisierung oder den direkten Zugriff auf die Azure-Dateifreigaben verwenden.

### <a name="plan-your-downtime"></a>Planen der Downtime

Dieser Migrationsansatz erfordert etwas Downtime für Ihre Benutzer und Apps. Das Ziel besteht darin, die Downtime möglichst gering zu halten. Hierbei ist die Beachtung der folgenden Aspekte hilfreich:

* Halten Sie Ihre StorSimple-Volumes verfügbar, während Ihre Datentransformationsaufträge ausgeführt werden.
* Nachdem die von Ihnen ausgeführten Datenmigrationsaufträge für eine Freigabe abgeschlossen sind, sollten Sie den Benutzerzugriff (mindestens Schreibzugriff) aus den StorSimple-Volumes oder -Freigaben entfernen. Mit einem abschließenden RoboCopy-Vorgang wird Ihre Azure-Dateifreigabe auf den aktuellen Stand gebracht. Anschließend können Sie für Ihre Benutzer die Übernahme durchführen. Wo Sie RoboCopy ausführen, hängt davon ab, ob Sie sich für die Verwendung von Azure-Dateisynchronisierung oder direkten Zugriff auf Dateifreigaben entschieden haben. Informationen zu diesem Thema finden Sie weiter unten im Abschnitt zu RoboCopy.
* Nachdem Sie den RoboCopy-Vorgang abgeschlossen haben, um den aktuellen Stand herzustellen, können Sie den neuen Standort für Ihre Benutzer verfügbar machen. Hierfür verwenden Sie entweder direkt die Azure-Dateifreigabe oder eine SMB-Freigabe auf einer Windows Server-Instanz mit Azure-Dateisynchronisierung. Häufig ermöglicht eine DFS-Namespace-Bereitstellung eine schnelle und effiziente Übernahme. Sie bewirkt, dass Ihre vorhandenen Freigabenadressen konsistent bleiben, und verweist auf einen neuen Speicherort, der Ihre migrierten Dateien und Ordner enthält.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Feststellen, wann Ihr Namespace vollständig mit dem Server synchronisiert ist

Wenn Sie die Azure-Dateisynchronisierung für eine Azure-Dateifreigabe verwenden, müssen Sie *vor* dem Starten eines lokalen RoboCopy-Befehls ermitteln, ob Ihr gesamter Namespace vollständig auf den Server heruntergeladen wurde. Wie viel Zeit für das Herunterladen Ihres Namespace erforderlich ist, hängt von der Anzahl der Elemente in ihrer Azure-Dateifreigabe ab. Sie können anhand von zwei Methoden ermitteln, ob der Download Ihres Namespace auf den Server vollständig abgeschlossen wurde.

#### <a name="azure-portal"></a>Azure-Portal

Sie können das Azure-Portal verwenden, um zu prüfen, ob Ihr Namespace vollständig heruntergeladen ist.

* Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrer Synchronisierungsgruppe. Überprüfen Sie den Synchronisierungsstatus Ihrer Synchronisierungsgruppe und des Serverendpunkts.
* Uns interessiert hier die Downloadrichtung. Wenn der Serverendpunkt neu bereitgestellt wird, wird dafür **Erste Synchronisierung** angezeigt. Dies ist ein Hinweis darauf, dass der Download des Namespace noch nicht abgeschlossen ist.
Wenn etwas anderes als **Erste Synchronisierung** angezeigt wird, befindet sich Ihr Namespace vollständig auf dem Server. Sie können jetzt mit einem lokalen RoboCopy-Vorgang fortfahren.

#### <a name="windows-server-event-viewer"></a>Windows Server-Ereignisanzeige

Sie können auch die Ereignisanzeige auf Ihrer Windows Server-Instanz verwenden, um zu ermitteln, wann der Namespace vollständig heruntergeladen wurde.

1. Öffnen Sie die **Ereignisanzeige**, und navigieren Sie zu **Anwendungen und Dienste**.
1. Navigieren Sie zu **Microsoft\FileSync\Agent\Telemetry**, und öffnen Sie den Ordner.
1. Suchen Sie nach dem neuesten **Ereignis 9102**, das einer abgeschlossenen Synchronisierungssitzung entspricht.
1. Wählen Sie **Details** aus, und vergewissern Sie sich, dass ein Ereignis angezeigt wird, für das **SyncDirection** den Wert **Download** hat.
1. Für den Zeitpunkt, zu dem der Download Ihres Namespace auf den Server vollständig abgeschlossen war, ist ein einzelnes Ereignis mit **Scenario**, **FullGhostedSync**-Wert und **HResult** = **0** vorhanden.
1. Sollte dieses Ereignis nicht vorhanden sein, können Sie nach anderen **9102-Ereignissen** mit **SyncDirection** = **Download** und **Scenario** = **RegularSync** suchen. Wenn Sie eines dieser Ereignisse finden, ist dies auch ein Hinweis darauf, dass das Herunterladen des Namespace abgeschlossen und die Synchronisierung zu regelmäßigen Synchronisierungssitzungen übergegangen ist. Dies gilt unabhängig davon, ob derzeit eine Synchronisierung erforderlich ist oder nicht.

### <a name="a-final-robocopy"></a>Abschließende RoboCopy

An diesem Punkt gibt es Unterschiede zwischen Ihrer lokalen Windows Server-Instanz und der StorSimple 8100- oder 8600-Appliance.

1. Sie müssen die Änderungen erfassen, die Benutzer oder Apps auf der StorSimple-Seite vorgenommen haben, während die Migration durchgeführt wurde.
1. In den Fällen, in denen Sie Azure-Dateisynchronisierung verwenden: Die StorSimple-Appliance weist einen gefüllten Cache auf, wohingegen die Windows Server-Instanz zu diesem Zeitpunkt nur über einen Namespace ohne lokal gespeicherten Dateiinhalt verfügt. Daher kann der abschließende RoboCopy-Befehl als Starthilfe für Ihren lokalen Cache der Azure-Dateisynchronisierung dienen, indem so viele lokal zwischengespeicherte Dateiinhalte abgerufen werden, wie verfügbar sind und auf den Azure-Dateisynchronisierungsserver passen.
1. Einige Dateien wurden vom Datentransformationsauftrag wegen ungültiger Zeichen unter Umständen nicht übertragen. Ist dies der Fall, kopieren Sie diese Dateien auf die Windows Server-Instanz mit aktivierter Azure-Dateisynchronisierung. Später können Sie die Dateien so anpassen, dass sie synchronisiert werden. Wenn Sie die Azure-Dateisynchronisierung für eine bestimmte Freigabe nicht verwenden, ist es besser, die Dateien auf dem StorSimple-Volume mit ungültigen Zeichen umzubenennen. Führen Sie RoboCopy dann direkt für die Azure-Dateifreigabe aus.

> [!WARNING]
> Sie dürfen RoboCopy *nicht* starten, bevor der Server den Namespace für eine Azure-Dateifreigabe vollständig heruntergeladen hat. Weitere Informationen finden Sie unter [Feststellen, wann Ihr Namespace vollständig mit dem Server synchronisiert ist](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Sie möchten nur Dateien kopieren, die nach dem letzten Ausführen des Migrationsauftrags geändert wurden, sowie Dateien, die durch diese Aufträge noch nicht verschoben wurden. Das Problem, das dazu geführt hat, dass diese Dateien nicht verschoben wurden, können Sie später nach Abschluss der Migration auf dem Server beheben. Weitere Informationen finden Sie unter [Problembehandlung für Azure-Dateisynchronisierung](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy hat mehrere Parameter. Im folgenden Beispiel sind ein fertiger Befehl und eine Liste mit den Gründen für die Auswahl dieser Parameter aufgeführt.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Hintergrund:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Ermöglicht RoboCopy die Multithread-Ausführung. Der Standardwert ist 8, und der zulässige Höchstwert ist 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Gibt den Status als UNICODE in die LOG-Datei aus (überschreibt vorhandenes Protokoll).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Ausgabe an das Konsolenfenster. Wird in Verbindung mit der Ausgabe in eine Protokolldatei verwendet.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Lässt die Protokollierung des Fortschritts aus, damit das Protokoll lesbar bleibt.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Führt RoboCopy in dem Modus aus, den auch eine Sicherungsanwendung verwenden würde. Diese Option ermöglicht RoboCopy das Verschieben von Dateien, für die der aktuelle Benutzer keine Berechtigungen hat.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Ermöglicht RoboCopy, nur Abweichungen zwischen der Quelle (StorSimple-Appliance) und dem Ziel (Windows Server-Verzeichnis) zu berücksichtigen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:Kopierflag[s]
   :::column-end:::
   :::column span="1":::
      Genauigkeit der Dateikopie (Standard: „/COPY:DAT“), Kopierflags: D = Daten, A = Attribute, T = Zeitstempel, S = Sicherheit = NTFS-ACLs, O = Eigentümerinformationen, U = Überprüfungsinformationen
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopieren aller Dateiinformationen (äquivalent zu „/COPY:DATSOU“)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:Kopierflag[s]
   :::column-end:::
   :::column span="1":::
      Genauigkeit für Kopien von Verzeichnissen (Standard: „/DCOPY:DA“), Kopierflags: D = Daten, A = Attribute, T = Zeitstempel
   :::column-end:::
:::row-end:::

Wenn Sie den Quell- und Zielspeicherort des RoboCopy-Befehls konfigurieren, sollten Sie die Struktur der Quelle und des Ziels überprüfen, um sicherzustellen, dass sie übereinstimmen. Wenn Sie die Verzeichniszuordnungsfunktion des Migrationsauftrags verwendet haben, kann es sein, dass sich die Struktur Ihres Stammverzeichnisses von der Struktur Ihres StorSimple-Volumes unterscheidet. Ist dies der Fall, benötigen Sie unter Umständen mehrere RoboCopy-Aufträge (einen für jedes Unterverzeichnis). Falls Sie unsicher sind, ob der Befehl zum gewünschten Ergebnis führt, können Sie den Parameter */L* verwenden. Hiermit wird der Befehl simuliert, ohne dass Änderungen vorgenommen werden.

Bei diesem RoboCopy-Befehl wird der Zusatz „/MIR“ verwendet, damit keine gleichen Dateien (z. B. mehrstufige Dateien) verschoben werden. Wenn aber der Quell- und Zielpfad fehlerhaft ist, werden mit „/MIR“ auch Verzeichnisstrukturen auf Ihrer Windows Server-Instanz oder Azure-Dateifreigabe bereinigt, die im StorSimple-Quellpfad nicht vorhanden sind. Die Pfade müssen genau übereinstimmen, damit der RoboCopy-Auftrag das beabsichtigte Ziel erreicht und lediglich Ihre migrierten Inhalte mit den neuesten Änderungen aktualisiert, die während der Ausführung der Migration vorgenommen wurden.

Überprüfen Sie die RoboCopy-Protokolldatei, um festzustellen, ob Dateien ausgelassen wurden. Wenn Probleme vorliegen, beheben Sie diese, und führen Sie den RoboCopy-Befehl erneut aus. Heben Sie keine Bereitstellung einer StorSimple-Ressource auf, bevor Sie nicht alle Probleme für Dateien oder Ordner behoben haben, die für Sie wichtig sind.

Wenn Sie nicht die Azure-Dateisynchronisierung zum Zwischenspeichern der betreffenden Azure-Dateifreigabe verwenden, sondern sich für den direkten Zugriff auf Dateifreigaben entschieden haben:

1. [Binden Sie Ihre Azure-Dateifreigabe](storage-how-to-use-files-windows.md#mount-the-azure-file-share) als Netzwerklaufwerk auf einem lokalen Windows-Computer ein.
1. Führen Sie den RoboCopy-Befehl zwischen Ihrer StorSimple- und der eingebundenen Azure-Dateifreigabe aus. Falls Dateien nicht kopiert werden, sollten Sie deren Namen auf der StorSimple-Seite korrigieren, um ungültige Zeichen zu entfernen. Versuchen Sie dann erneut, RoboCopy auszuführen. Der weiter oben angegebene RoboCopy-Befehl kann mehrmals ausgeführt werden, ohne dass unnötige StorSimple-Abrufe verursacht werden.

### <a name="user-cut-over"></a>Benutzerübernahme

Wenn Sie die Azure-Dateisynchronisierung verwenden, müssen Sie wahrscheinlich die SMB-Freigaben auf der Windows Server-Instanz mit aktivierter Azure-Dateisynchronisierung erstellen, die den Freigaben auf den StorSimple-Volumes entsprechen. Sie können diesen Schritt vorverlegen und zu einem früheren Zeitpunkt ausführen, um hier keine Zeit zu verlieren. Allerdings müssen Sie sicherstellen, dass vor diesem Zeitpunkt niemand Zugriff hat, damit auf der Windows Server-Instanz keine Änderungen vorgenommen werden.

Wenn Sie über eine DFS-N-Bereitstellung verfügen, können Sie die DFN-Namespaces auf die neuen Speicherorte für Serverordner verweisen. Wenn Sie keine DFS-N-Bereitstellung nutzen und Ihrer 8100- oder 8600-Appliance lokal eine Windows Server-Instanz vorgeschaltet haben, können Sie diesen Server aus der Domäne entfernen. Binden Sie anschließend Ihre neue Windows Server-Instanz mit aktivierter Azure-Dateisynchronisierung in die Domäne ein. Geben Sie dem Server im Rahmen dieses Vorgangs denselben Servernamen und dieselben Freigabenamen wie beim alten Server, damit die Übernahme für Ihre Benutzer, Gruppenrichtlinien und Skripts transparent bleibt.

Erfahren Sie mehr zu [DFS-Namespaces](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Aufheben der Bereitstellung

Wenn Sie die Bereitstellung einer Ressource aufheben, haben Sie keinen Zugriff mehr auf die Konfiguration dieser Ressource und die zugehörigen Daten. Die Aufhebung der Bereitstellung kann nicht rückgängig gemacht werden. Fahren Sie erst fort, nachdem Sie Folgendes sichergestellt haben:

* Die Migration ist abgeschlossen.
* Es sind keine Abhängigkeiten von den StorSimple-Dateien, -Ordnern oder -Volumesicherungen vorhanden, für die Sie die Bereitstellung aufheben möchten.

Bevor Sie damit beginnen, ist es ratsam, die neue Bereitstellung der Azure-Dateisynchronisierung in der Produktionsumgebung eine Weile zu beobachten. Dieser Zeitraum gibt Ihnen die Möglichkeit, etwaige auftretende Probleme zu beheben. Nachdem Sie Ihre Bereitstellung der Azure-Dateisynchronisierung einige Tage lang beobachtet haben, können Sie damit beginnen, die Bereitstellung von Ressourcen in der folgenden Reihenfolge aufzuheben:

1. Heben Sie die Bereitstellung Ihrer StorSimple Data Manager-Ressource über das Azure-Portal auf. Dabei werden alle Datentransformationsdienst-Aufträge gelöscht, die Sie erstellt haben. Sie können die Kopierprotokolle nicht auf einfache Weise abrufen. Falls diese für Ihre Aufzeichnungen wichtig sind, müssen Sie sie abrufen, bevor Sie die Bereitstellung aufheben.
1. Vergewissern Sie sich, dass Ihre physischen StorSimple-Appliances migriert wurden, und heben Sie anschließend deren Registrierungen auf. Wenn Sie nicht ganz sicher sind, dass sie migriert wurden, sollten Sie keine weiteren Schritte ausführen. Wenn Sie die Bereitstellungen dieser Ressourcen aufheben, während sie noch erforderlich sind, können Sie weder die Daten noch deren Konfiguration wiederherstellen.<br>Optional können Sie zuerst die Bereitstellung der StorSimple-Volumeressource aufheben. Dies führt dazu, dass die Daten auf der Appliance bereinigt werden. Dieser Vorgang kann mehrere Tage dauern und umfasst **keine** vollständige forensische Ersetzung der Daten durch Nullen auf der Appliance. Falls dies für Sie wichtig ist, sollten Sie die Ersetzung durch Nullen auf dem Datenträger separat von der Aufhebung der Bereitstellung der Ressourcen und gemäß Ihren Richtlinien durchführen.
1. Sind in einem StorSimple-Geräte-Manager keine Geräte mehr registriert, können Sie diese Geräte-Manager-Ressource entfernen.
1. An diesem Punkt kann das StorSimple-Speicherkonto in Azure gelöscht werden. Gehen Sie erneut so vor, dass Sie die nächsten Schritte erst ausführen, nachdem Sie sich vergewissert haben, dass die Migration abgeschlossen ist und keine Abhängigkeiten von diesen Daten bestehen.
1. Trennen Sie die physische StorSimple-Appliance von Ihrem Rechenzentrum.
1. Wenn Sie der Besitzer der StorSimple-Appliance sind, können Sie sie in die Wiederverwertung geben. Ist Ihr Gerät geleast, informieren Sie den Leasinggeber, und geben Sie das Gerät entsprechend zurück.

Die Migration ist abgeschlossen.

> [!NOTE]
> Haben Sie noch Fragen, oder sind Probleme aufgetreten?</br>
> Wenden Sie sich an AzureFilesMigration@microsoft.com, falls Sie Hilfe benötigen.

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit der [Azure-Dateisynchronisierung](./storage-sync-files-planning.md) vertraut.
* Informationen über die Flexibilität von [Cloudtiering](storage-sync-cloud-tiering.md)-Richtlinien.
* [Aktivieren Sie Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) in ihren Azure-Dateifreigaben, um Momentaufnahmen zu planen und Aufbewahrungszeitpläne für Sicherungen zu definieren.
* Wenn Sie im Azure-Portal feststellen, dass einige Dateien dauerhaft nicht synchronisiert werden, helfen Ihnen die Schritte zum Lösen des Problems im [Leitfaden zur Problembehandlung](storage-sync-files-troubleshoot.md) weiter.