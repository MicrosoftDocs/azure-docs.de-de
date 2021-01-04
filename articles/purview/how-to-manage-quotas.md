---
title: Verwalten von Ressourcen und Kontingenten
titleSuffix: Azure Purview
description: Enthält eine Beschreibung der Kontingente und Grenzwerte für Azure Purview-Ressourcen und der Anforderung von Kontingenterhöhungen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 3b0a413db304b4f9d2c50a3d221c480f1e9dc37a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550372"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Purview
 
Azure Purview ist ein Clouddienst, der von Datenbenutzern verwendet werden kann. Mithilfe von Azure Purview können Sie die Datengovernance in Ihrem gesamten Datenbestand in Cloud- und lokalen Umgebungen zentral verwalten. Der Dienst ermöglicht Business Analysts, mithilfe aussagekräftiger Geschäftsbedingungen nach relevanten Daten zu suchen. Wenden Sie sich an den Support, um die Grenzwerte bis zum Höchstwert für Ihr Abonnement zu erhöhen.
 
## <a name="azure-purview-limits"></a>Azure Purview-Grenzwerte
 
|**Ressource**|  **Standardlimit**  |**Maximales Limit**|
|---|---|---|
|Purview-Konten pro Mandant (alle Abonnements kombiniert)|3|Kontaktieren des Supports|
|Zum Scannen verfügbare virtuelle Kerne pro Konto*|160|160|
|Gleichzeitige Scans pro Konto an einem bestimmten Punkt. Der Grenzwert basiert auf dem Typ der überprüften Datenquellen*|5 | 10 |
|Maximale Zeit, für die eine Überprüfung ausgeführt werden kann|7 Tage|7 Tage|
|API-Aufrufe pro Konto|10 Mio. APIs/Monat für eine Plattformgröße von 4 Kapazitätseinheiten. <br>40 Mio. APIs/Monat für eine Plattformgröße von 16 Kapazitätseinheiten. |10 Mio. APIs/Monat für eine Plattformgröße von 4 Kapazitätseinheiten. <br>40 Mio. APIs/Monat für eine Plattformgröße von 16 Kapazitätseinheiten.|
|Speicher pro Konto|10 GB für eine Plattformgröße von 4 Kapazitätseinheiten. <br>40 GB für eine Plattformgröße von 16 Kapazitätseinheiten. |10 GB für eine Plattformgröße von 4 Kapazitätseinheiten. <br> 40 GB für eine Plattformgröße von 16 Kapazitätseinheiten. |
|Ressourcenumfang pro Konto|100 Mio. physische Ressourcen |Kontaktieren des Supports|
|Maximale Größe einer Ressource in einem Katalog|2 MB|2 MB|
|Maximale Länge eines Ressourcennamens und Klassifizierungsnamens|4 KB|4 KB|
|Maximale Länge des Ressourceneigenschaftsnamens und -werts|32 KB|32 KB|
|Maximale Länge des Klassifizierungsattributnamens und -werts|32 KB|32 KB|
|Maximale Anzahl von Glossarbegriffen pro Konto|100k|100k|
 
*Szenarios mit selbstgehosteter Integrationslaufzeit liegen außerhalb des Bereichs der in der obigen Tabelle definierten Grenzwerte. 
 
## <a name="next-steps"></a>Nächste Schritte
 
> [!div class="nextstepaction"]
>[Tutorial: Überprüfen von Daten mit Azure Purview](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Tutorial: Navigieren zur Startseite und Suchen nach einer Ressource](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Tutorial: Durchsuchen von Ressourcen und Anzeigen ihrer Herkunft](tutorial-browse-and-view-lineage.md)
