---
title: Grenzwerte von Azure Analysis Services-Ressourcen und -Objekten | Microsoft-Dokumentation
description: Beschreibt die Grenzwerte von Azure Analysis Services-Ressourcen und -Objekten.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0ffbffc788baaffd4a0532c3918ed82cc3eaf5c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023549"
---
# <a name="analysis-services-resource-and-object-limits"></a>Grenzwerte von Azure Analysis Services-Ressourcen und -Objekten

In diesem Artikel beschreibt Ressourcen- und Modellobjektgrenzwerte.

## <a name="tier-limits"></a>Tarifgrenzwerte

### <a name="developer-tier"></a>Developer-Tarif

Dieser Tarif wird für Auswertungs-, Entwicklungs- und Testszenarien empfohlen. Ein einzelner Plan enthält die gleichen Funktionen wie der Standard-Tarif, ist aber in Bezug auf die Verarbeitungsleistung, die QPUs und die Speichergröße begrenzt. Das horizontale Hochskalieren von Abfragereplikaten ist für diesen Tarif nicht verfügbar. Für diesen Tarif wird keine Vereinbarung zum Servicelevel angeboten.

|Plan  |QPUs  |Arbeitsspeicher (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Basic-Tarif

Der Tarif wird für Produktionslösungen mit kleineren Tabellenmodellen, einer begrenzten Anzahl von gleichzeitig aktiven Benutzern und einfachen Anforderungen an die Datenaktualisierung empfohlen. Das horizontale Hochskalieren von Abfragereplikaten ist für diesen Tarif *nicht verfügbar*. Features wie Perspektiven, mehrere Partitionen und das DirectQuery-Tabellenmodell werden für diesen Tarif nicht unterstützt.  

|Plan  |QPUs  |Arbeitsspeicher (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Standard-Tarif

Dieser Tarif ist am besten für unternehmenskritische Produktionsanwendungen geeignet, bei denen elastische Anforderungen für gleichzeitig aktive Benutzer erfüllt werden müssen und die Datenmodelle schnell wachsen. Er unterstützt die erweiterte Datenaktualisierung für Datenmodellupdates nahezu in Echtzeit sowie alle tabellarischen Modellierungsfeatures.

|Plan  |QPUs  |Arbeitsspeicher (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Nicht in allen Regionen verfügbar.  

## <a name="object-limits"></a>Objektgrenzwerte

Hierbei handelt um theoretische Grenzwerte. Die Leistung wird bei geringeren Werten beeinträchtigt.

|Object|Maximale Größe/Anzahl|  
|------------|----------------------------|  
|Datenbanken in einer Instanz|16\.000|  
|Kombinierte Anzahl an Tabellen und Spalten in einer Datenbank|16\.000|  
|Zeilen in einer Tabelle|Unbegrenzt<br /><br /> **Warnung:** Mit der Einschränkung, dass keine einzelne Spalte in der Tabelle mehr als 1.999.999.999.999.997 verschiedene Werte haben kann.|  
|Hierarchien in einer Tabelle|15\.999|  
|Ebenen in einer Hierarchie|15\.999|  
|Beziehungen|8\.000|  
|Schlüsselspalten in allen Tabellen|15\.999|  
|Messungen in Tabellen|2^31-1 = 2.147483.647|  
|Von einer Abfrage zurückgegebene Zellen|2^31-1 = 2.147483.647|  
|Datensatzgröße der Quellabfrage|64 K|  
|Länge des Objektnamens|512 Zeichen|  


