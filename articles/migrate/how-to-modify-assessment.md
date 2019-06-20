---
title: Anpassen der Einstellungen einer Azure Migrate-Bewertung | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie eine Bewertung für die Migration von virtuellen VMware-Computern mithilfe von Azure Migration Planner einrichten und ausführen.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129822"
---
# <a name="customize-an-assessment"></a>Anpassen einer Bewertung

[Azure Migrate](migrate-overview.md) erstellt Bewertungen mit Standardeigenschaften. Nach dem Erstellen einer Bewertung können Sie die Standardeigenschaften entsprechend den Anweisungen in diesem Artikel ändern.


## <a name="edit-assessment-properties"></a>Bearbeiten von Bewertungseigenschaften

1. Wählen Sie auf der Seite **Bewertungen** des Migrationsprojekts die Bewertung aus, und klicken Sie auf **Eigenschaften bearbeiten**.
2. Passen Sie die Bewertungseigenschaften anhand der folgenden Details an:

    **Einstellung** | **Details** | **Standard**
    --- | --- | ---
    **Zielstandort** | Der Azure-Standort, zu dem die Migration durchgeführt werden soll.<br/><br/> Azure Migrate unterstützt derzeit 30 Regionen, einschließlich „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Kanada, Mitte“, „Kanada, Osten“, „Indien, Mitte“, „USA, Mitte“, „China, Osten“, „China, Norden“, „Asien, Osten“, „USA, Osten“, „Deutschland, Mitte“, „Deutschland, Nordosten“, „USA, Osten 2“, „Japan, Osten“, „Japan, Westen“, „Südkorea, Mitte“, „Südkorea, Süden“, „USA, Norden-Mitte“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Indien, Süden“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „US Gov Arizona“, „US Gov Texas“, „US Gov Virginia“, „USA, Westen-Mitte“, „Europa, Westen“, „Indien, Westen“, „USA, Westen“ und „USA, Westen 2“. |  „USA, Westen 2“ ist der Standardstandort.
    **Speichertyp** | Sie können diese Eigenschaft nutzen, um den Typ der Datenträger anzugeben, die Sie nach Azure verschieben möchten. Für die Größenanpassung für die lokale Umgebung können Sie den Zieldatenträgertyp als „Managed Disks Premium“ oder „Managed Disks Standard“ angeben. Für die leistungsbasierte Größenanpassung können Sie den Zieldatenträgertyp als „Automatisch“, „Managed Disks Premium“ oder „Managed Disks Standard“ angeben. Beim Angeben des Speichertyps als „Automatisch“ erfolgt die Datenträgerempfehlung basierend auf den Leistungsdaten der Datenträger (IOPS und Durchsatz). Wenn Sie beispielsweise eine [Einzelinstanz-VM-SLA von 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) erreichen möchten, könnten Sie als Speichertyp „Managed Disks Premium“ angeben. Dadurch wird sichergestellt, dass für alle Datenträger in der Bewertung „Managed Disks Premium“ empfohlen wird. Hinweis: Azure Migrate unterstützt nur verwaltete Datenträger für die Migrationsbewertung. | Standardmäßig wird „Managed Disks Premium“ verwendet (mit dem Größenkriterium *Wie lokal*).
    **Reservierte Instanzen** |  Sie können auch angeben, ob Sie über [reservierte Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure verfügen. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt. Reservierte Instanzen werden derzeit nur für das Angebot mit nutzungsbasierter Bezahlung in Azure Migrate unterstützt. | Der Standardwert für diese Eigenschaft sind für 3 Jahre reservierte Instanzen.
    **Größenkriterium** | Das Kriterium, das in Azure Migrate zur Größenanpassung virtueller Computer für Azure verwendet werden soll. Sie können eine *leistungsbasierte* Größenanpassung vornehmen oder die Größe der virtuellen Computer *lokal* ändern, ohne den Leistungsverlauf zu berücksichtigen. | Die leistungsbasierte Größenanpassung ist die Standardoption.
    **Leistungsverlauf** | Die zur Bewertung der Leistung der virtuellen Computer zu berücksichtigende Dauer. Diese Eigenschaft ist nur anwendbar, wenn als Größenkriterium die *leistungsbasierte Größenanpassung* festgelegt ist. | Standardmäßig ist ein Tag festgelegt.
    **Perzentilwert der Nutzung** | Der für die Größenanpassung zu berücksichtigende Perzentilwert des festgelegten Leistungsbeispiels. Diese Eigenschaft ist nur anwendbar, wenn als Größenkriterium die *leistungsbasierte Größenanpassung* festgelegt ist.  | Der Standardwert ist das 95. Perzentil.
    **VM-Serie** | Sie können die VM-Serie angeben, die Sie für die Größenanpassung berücksichtigen möchten. Wenn Sie beispielsweise eine Produktionsumgebung haben, die Sie nicht zu VMs der A-Serie in Azure migrieren möchten, können Sie die A-Serie von der Liste oder aus der Serie ausschließen, sodass die Größenanpassung nur in der ausgewählten Serie erfolgt. | Standardmäßig sind alle VM-Serien ausgewählt.
    **Komfortfaktor** | Bei Azure Migrate wird während der Bewertung ein Puffer (Komfortfaktor) berücksichtigt. Dieser Puffer wird zusätzlich zu den Daten zur Computernutzung für VMs (CPU, Arbeitsspeicher, Datenträger und Netzwerk) angewendet. Beim Komfortfaktor geht es um Bereiche wie saisonale Nutzung, kurzer Leistungsverlauf und voraussichtliche zukünftige Zunahme der Nutzung.<br/><br/> Beispiel: Für eine VM mit zehn Kernen und 20% Auslastung ergibt sich normalerweise eine VM mit zwei Kernen. Bei einem Komfortfaktor von 2.0x ist das Ergebnis dagegen eine VM mit vier Kernen. | Die Standardeinstellung ist 1.3x.
    **Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), für das Sie registriert sind. | [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) ist die Standardeinstellung.
    **Währung** | Rechnungswährung. | Der Standardwert ist US-Dollar.
    **Rabatt (%)** | Alle abonnementspezifischen Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. | Die Standardeinstellung ist 0 %.
    **VM-Betriebszeit** | Wenn Ihre virtuellen Computer nicht rund um die Uhr in Azure aktiv sind, können Sie den Zeitraum angeben (Anzahl der Tage pro Monat und Anzahl der Stunden pro Tag), für den sie voraussichtlich ausgeführt werden, um eine entsprechende Kostenschätzung zu erhalten. | Der Standardwert sind 31 Tage pro Monat und 24 Stunden pro Tag.
    **Azure-Hybridvorteil** | Geben Sie an, ob Sie über die Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Wenn diese Einstellung auf „Ja“ festgelegt ist, werden für virtuelle Windows-Computer Nicht-Windows-Azure-Preise veranschlagt. | Die Standardeinstellung ist „Ja“.

3. Klicken Sie auf **Speichern**, um die Bewertung zu aktualisieren.

## <a name="faqs-on-assessment-properties"></a>Häufig gestellte Fragen zu Bewertungseigenschaften

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Was ist der Unterschied zwischen den Größenanpassungen „Wie lokal“ und „Leistungsbasiert“?

Wenn Sie als Größenkriterium „Wie lokal“ angeben, berücksichtigt Azure Migrate die Leistungsdaten der virtuellen Computer nicht und legt die Größe der virtuellen Computer basierend auf der lokalen Konfiguration fest. Wenn das Größenkriterium „Leistungsbasiert“ lautet, erfolgt die Größenanpassung basierend auf den Nutzungsdaten. Beispiel: Eine lokale VM mit 4 Kernen und 8 GB Arbeitsspeicher mit einer CPU- und Arbeitsspeicherauslastung von jeweils 50 %. Wenn das Größenkriterium „Wie lokal“ gilt, wird eine Azure-VM-SKU mit 4 Kernen und 8 GB Arbeitsspeicher empfohlen. Falls das Größenkriterium „Leistungsbasiert“ lautet, wird eine VM-SKU mit 2 Kernen und 4 GB Arbeitsspeicher empfohlen, da für die Größenempfehlung der Auslastungsprozentsatz berücksichtigt wird.

Entsprechend hängt bei Datenträgern die Größenanpassung von zwei Bewertungseigenschaften ab: dem Größenkriterium und dem Speichertyp. Wenn das Größenkriterium „Leistungsbasiert“ ist und der Speichertyp „Automatisch“ lautet, werden die IOPS- und Durchsatzwerte des Datenträgers berücksichtigt, um den Typ des Zieldatenträgers zu ermitteln (Standard oder Premium). Wenn das Größenkriterium „Leistungsbasiert“ ist und der Speichertyp „Premium“ lautet, wird ein Premium-Datenträger empfohlen. Die Premium-Datenträger-SKU in Azure wird basierend auf der Größe des lokalen Datenträgers ausgewählt. Die gleiche Logik wird verwendet, um die Größenanpassung für Datenträger durchzuführen, wenn das Größenkriterium „Wie lokal“ verwendet wird und der Speichertyp „Standard“ oder „Premium“ lautet.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Welche Auswirkungen haben der Leistungsverlauf und der Quantilwert der Nutzung auf die Größenempfehlungen?

Diese Eigenschaften gelten nur für die leistungsbasierte Größenanpassung. Azure Migrate sammelt den Leistungsverlauf von lokalen Computern und nutzt diese Daten, um die VM-Größe und den Datenträgertyp in Azure zu empfehlen.

- Die Collectorappliance erstellt für die lokale Umgebung kontinuierlich Profildaten, um alle 20 Sekunden Echtzeit-Verwendungsdaten erfassen zu können.
- Die Appliance führt für die 20-Sekunden-Stichproben einen Rollup aus und erstellt für jeweils 15 Minuten einen Datenpunkt. Zum Erstellen jedes Datenpunkts wählt die Appliance den Spitzenwert aus allen 20-Sekunden-Stichproben aus und sendet diesen an Azure.
- Beim Erstellen einer Bewertung in Azure berechnet Azure Migrate basierend auf der Leistungsdauer und dem Quantilwert des Leistungsverlaufs den geltenden Auslastungswert und verwendet ihn für die Größenanpassung.

Wenn Sie die Leistungsdauer beispielsweise auf einen Tag und den Quantilwert auf 95 festgelegt haben, verwendet Azure Migrate die vom Collector gesendeten 15-Minuten-Stichprobenpunkte für den gesamten letzten Tag, sortiert sie in aufsteigender Reihenfolge und wählt den Wert des 95. Quantils als gültige Auslastung aus. Mit dem Wert des 95. Quantils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können auftreten, wenn Sie das 99. Quantil wählen. Falls Sie die Spitzenauslastung für den Zeitraum wählen möchten und keine Ausreißer verpassen möchten, sollten Sie das 99. Quantil wählen.

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen
