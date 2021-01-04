---
title: Analysieren von Azure CDN-Verwendungsmustern
description: In diesem Artikel werden die verschiedenen Arten von Analyseberichten beschrieben, die für Azure CDN-Produkte zur Verfügung stehen.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483987"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysieren von Azure CDN-Verwendungsmustern

Nachdem Sie CDN für Ihre Anwendung aktiviert haben, können Sie die CDN-Nutzung überwachen, die Integrität Ihrer Bereitstellung überprüfen und potenzielle Probleme behandeln. Es gibt die folgenden Möglichkeiten für die Bereitstellung dieser Funktionen über Azure CDN: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Unformatierte Protokolle für Azure CDN von Microsoft
Mit einem Microsoft-Standardprofil können Sie unformatierte Protokolle aktivieren und zum Streamen von Protokollen an folgende Ziele auswählen:

* Azure Storage
* Event Hubs
* Azure Log Analytics

Mit Azure Log Analytics können Sie Überwachungsmetriken anzeigen und Warnungen einrichten. 

Weitere Informationen finden Sie unter [Unformatierte Azure CDN-HTTP-Protokolle](monitoring-and-access-log.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Kernanalyse über Azure-Diagnoseprotokolle

Die Kernanalyse ist für CDN-Endpunkte für alle Tarife verfügbar. Durch die Azure-Diagnoseprotokolle kann die Kernanalyse in Azure Storage, Event Hubs oder Azure Monitor-Protokolle exportiert werden. Azure Monitor-Protokolle bieten eine Lösung mit Diagrammen, die vom Benutzer konfiguriert und angepasst werden können. Weitere Informationen zu Azure-Diagnoseprotokollen finden Sie unter [Azure-Diagnoseprotokolle](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon-Kernberichte

Profile für **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** beinhalten Kernberichte. Sie können Kernberichte im zusätzlichen Verizon-Portal anzeigen. Auf Verizon-Kernberichte kann im Azure-Portal über die Option **Verwalten** zugegriffen werden. Sie bieten verschiedene Arten von Diagrammen und Ansichten. Weitere Informationen finden Sie unter [Kernberichte aus Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Benutzerdefinierte Verizon-Berichte

Profile für **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** beinhalten benutzerdefinierte Berichte. Sie können benutzerdefinierte Berichte im zusätzlichen Verizon-Portal anzeigen. Auf benutzerdefinierte Verizon-Berichte kann im Azure-Portal über die Option **Verwalten** zugegriffen werden. 

Die benutzerdefinierten Berichte enthalten die Anzahl der Treffer oder übertragenen Daten für jeden Edge-CNAME. Daten werden über einen Zeitraum nach HTTP-Antwortcode oder Cachestatus gruppiert. Weitere Informationen finden Sie unter [Benutzerdefinierte Berichte aus Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium von Verizon: Berichte

Mit **Azure CDN Premium von Verizon** können Sie darüber hinaus auf die folgenden Berichte zugreifen:
   * [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)
   * [Echtzeitstatistiken](cdn-real-time-stats.md)
   * [Analysieren der Leistung von Edgeknoten in Microsoft Azure CDN](cdn-edge-performance.md)

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie die verschiedenen Optionen für Analyseberichte für Azure CDN kennengelernt.

Weitere Informationen zu Azure CDN und den anderen in diesem Artikel erwähnten Azure-Diensten finden Sie unter den folgenden Quellen:

* [Was ist Azure CDN?](cdn-overview.md)
* [Unformatierte Azure CDN-HTTP-Protokolle](monitoring-and-access-log.md)
