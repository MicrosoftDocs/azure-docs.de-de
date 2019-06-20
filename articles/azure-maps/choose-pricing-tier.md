---
title: Auswählen des richtigen Tarifs für Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie mehr über die von Azure Maps angebotenen Tarife
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1bf60014b20f4cec29d661db4deb029fea4ab1d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60796324"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Auswählen des richtigen Tarifs in Azure Maps

Azure Maps bietet zwei Tarife. Dieser Artikel soll Ihnen bei der Auswahl des richtigen Tarifs für Ihre Ansprüche helfen. Um den richtigen Tarif auszuwählen, sollten Sie sich die folgenden zwei Fragen stellen.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Welche Geofunktionen planen Sie, zu verwenden?
Der Tarif S0 ist der richtige für Sie, wenn die Kerngeofunktions-APIs Ihre Dienstanforderungen erfüllen. Wenn Sie erweiterte Funktionen für Ihre Anwendung benötigen, sollten Sie den Tarif S1 in Erwägung ziehen. Beispielfunktionen sind die Raum- und Hybridbildgebung, das Abrufen von Routenbereichen und die Batchgeocodierung. Die folgende Tabelle **Tarife und Funktionen** vermittelt Ihnen einen besseren Eindruck von den Anforderungen Ihrer Anwendung. Sie hilft auch bei der Auswahl eines Tarifs, der für Ihre Anwendung am besten geeignet ist.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Wie viele gleichzeitige Benutzer planen Sie, zu unterstützen? 
Mit den Tarifen S0 und S1 werden unterschiedliche Volumen an Datendurchsatz verarbeitet. Bevor Sie einen Azure Maps-Tarif auswählen, sollten Sie sich einige Fragen stellen, z.B. „Wie viele gleichzeitige Benutzer möchten Sie unterstützen?“ Mit dem Tarif S0 werden bis zu **50 Abfragen pro Sekunde** verarbeitet. Mit dem Tarif S1 werden **mehr als 50 Abfragen pro Sekunde** verarbeitet.

### <a name="pricing-tier-capabilities"></a>Tarife und Funktionen

| Funktion                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Suche (fwd/Rev geocodierung, POIs)  |        ✓           |     ✓    |
| Batchgeocodierung (Preview)              |                   |     ✓    |
| Polygone aus Suche          |                   |     ✓    |
| Routing                                 |        ✓           |     ✓    |
| Routenbereich                    |                   |     ✓    |
| Batchrouting (Preview)                |                   |     ✓    |
| Matrixrouting (Preview)               |                   |     ✓    |
| Rendern                                  |        ✓           |     ✓    |
| Bildgebung und Hybridbildgebung    |            |     ✓    |
| Verkehr                                 |        ✓           |     ✓    |
| Zeitzonen                              |        ✓           |     ✓    |
| Geolocation (Preview)                |        ✓           |     ✓    |
| Daten (Preview)               |                   |     ✓    |
| Räumlich (Preview)               |                   |     ✓    |
| Geofencing (Preview)               |                   |     ✓    |



Diese zusätzliche Datenpunkte sollten in Betracht gezogen werden:
* Welche Art von Unternehmen haben Sie?
* Wie kritisch ist die zu erstellende Anwendung?

Einen besseren Eindruck von den Tarifen S0 und S1 verschafft Ihnen die Tabelle **Tarife und Kundenzielgruppen**. Weitere Informationen finden Sie unter [Azure Maps – Preise](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Tarife und Kundenzielgruppen

| Tarif  |     Tarife und Kundenzielgruppen                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Der Tarif S0 richtet sich an Kunden mit kleinen oder mittelgroßen Unternehmen. Dies ist der richtige Tarif für Sie, wenn Sie keine große Anzahl gleichzeitiger Benutzer erwarten. Sie ist auch richtig, wenn die in der Tabelle oben aufgeführten Haupt-Geo-APIs Ihre Dienstanforderungen erfüllen. Dieser Tarif ist allgemein verfügbar. Er kann für Anwendungen in allen Phasen der Produktion verwendet werden: von der Entwicklung einer Machbarkeitsstudie und frühen Testphasen bis hin zur Produktion und Bereitstellung.<p>|
| S1            |    <p>Der Tarif S1 richtet sich an Kunden, die Unterstützung für große Unternehmen, unternehmenskritische Anwendungen oder eine große Anzahl gleichzeitiger Benutzer benötigen. Er ist auch für Kunden geeignet, die komplexe Geodienste benötigen.</p>|

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Anzeigen und Ändern von Tarifen:

> [!div class="nextstepaction"] 
> [Verwalten eines Tarifs](how-to-manage-pricing-tier.md)
