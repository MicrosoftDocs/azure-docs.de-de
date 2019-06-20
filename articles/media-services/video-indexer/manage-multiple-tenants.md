---
title: Verwalten mehrerer Mandanten mit Video Indexer – Azure
description: In diesem Artikel werden verschiedene Integrationsoptionen für die Verwaltung mehrerer Mandanten mit Video Indexer vorgeschlagen.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: a9b75c3454c67112b0e00c7ea4b4e8c676ebcc97
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949468"
---
# <a name="manage-multiple-tenants"></a>Verwalten mehrerer Mandanten

In diesem Artikel werden verschiedene Optionen für die Verwaltung mehrerer Mandanten mit Video Indexer erörtert. Wählen Sie eine Methode, die sich für Ihr Szenario am besten eignet:

* Video Indexer-Konto pro Mandant
* Einzelnes Video Indexer-Konto für alle Mandanten
* Azure-Abonnement pro Mandant

## <a name="video-indexer-account-per-tenant"></a>Video Indexer-Konto pro Mandant

Wenn Sie diese Architektur verwenden, wird für jeden Mandanten ein Video Indexer-Konto erstellt. Die Mandanten zeichnen sich durch vollständige Isolation auf der persistenten und Compute-Ebene aus.  

![Video Indexer-Konto pro Mandant](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Überlegungen

* Kunden nutzen Speicherkonten nicht gemeinsam (es sei denn, der Kunde hat dies manuell konfiguriert).
* Kunden nutzen die Compute-Leistung (reservierten Einheiten) nicht gemeinsam und beeinflussen die Verarbeitungszeiten der Aufträge der jeweils anderen nicht.
* Sie können einen Mandanten ganz einfach aus dem System entfernen, indem Sie das Video Indexer-Konto löschen.
* Es gibt keine Möglichkeit, dass Mandanten benutzerdefinierte Modelle gemeinsam nutzen.

    Stellen Sie sicher, dass keine geschäftliche Notwendigkeit zur gemeinsamen Nutzung benutzerdefinierter Modelle besteht.
* Schwierigere Verwaltung aufgrund mehrerer Video Indexer- (und zugehöriger Media Services-) Konten pro Mandant.

> [!TIP]
> Erstellen Sie im [Video Indexer-Entwicklerportal](https://api-portal.videoindexer.ai/) einen Administratorbenutzer für Ihr System, und stellen Sie Ihren Mandanten mithilfe der Autorisierungs-API das entsprechende [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token) zur Verfügung.

## <a name="single-video-indexer-account-for-all-users"></a>Einzelnes Video Indexer-Konto für alle Benutzer

Bei Verwenden dieser Architektur ist der Kunde für die Isolation von Mandanten zuständig. Alle Mandanten müssen ein einzelnes Video Indexer-Konto mit einem einzelnen Azure Media Service-Konto verwenden. Beim Hochladen, Durchsuchen oder Löschen von Inhalten muss der Kunde die richtigen Ergebnisse für diesen Mandanten filtern.

![Einzelnes Video Indexer-Konto für alle Benutzer](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Mit dieser Option können Anpassungsmodelle (Person, Sprache und Marken) gemeinsam genutzt oder zwischen Mandanten durch Filtern der Modelle nach Mandant isoliert werden.

Wenn Sie [Videos hochladen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), können Sie pro Mandant ein anderes Partitionsattribut angeben. Dies ermöglicht die Isolation in der [Such-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Durch Angabe des Partitionsattributs in der Such-API erhalten Sie nur die Ergebnisse der angegebenen Partition. 

### <a name="considerations"></a>Überlegungen

* Möglichkeit, dass Mandanten Inhalte und benutzerdefinierte Modelle gemeinsam nutzen.
* Ein Mandant wirkt sich auf die Leistung anderer Mandanten aus.
* Der Kunde muss basierend auf Video Indexer eine komplexe Verwaltungsebene aufbauen.

> [!TIP]
> Sie können mit dem Attribut [Priorität](upload-index-videos.md) Aufträge von Mandanten priorisieren.

## <a name="azure-subscription-per-tenant"></a>Azure-Abonnement pro Mandant 

Wenn Sie diese Architektur verwenden, verfügt jeder Mandant über ein eigenes Azure-Abonnement. Für jeden Benutzer erstellen Sie im Abonnement des Mandanten ein neues Video Indexer-Konto.

![Azure-Abonnement pro Mandant](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Überlegungen

* Dies ist die einzige Möglichkeit, eine Trennung für die Abrechnung zu ermöglichen.
* Diese Integration weist mehr Verwaltungsaufwand als ein Video Indexer-Konto pro Mandant auf. Wenn die Abrechnung nicht erforderlich ist, wird empfohlen, eine der anderen in diesem Artikel beschriebenen Optionen zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](video-indexer-overview.md)
