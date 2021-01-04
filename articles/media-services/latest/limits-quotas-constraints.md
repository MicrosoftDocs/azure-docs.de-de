---
title: Kontingente und Grenzwerte für Azure Media Services
description: In diesem Thema werden die Kontingente und Grenzwerte für Microsoft Azure Media Services beschrieben.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: d6ca7a444f2a3d4babe220548edb10bd37784be7
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678108"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Azure Media Services-Kontingente und -Grenzwerte

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel sind einige der gängigsten Limits in Microsoft Azure Media Services aufgeführt, die bisweilen auch als „Kontingente“ bezeichnet werden.

> [!NOTE]
> Erstellen Sie für Ressourcen ohne festes Limit ein Supportticket, um eine Erhöhung der Kontingente anzufordern. Erstellen Sie keine zusätzlichen Azure Media Services-Konten, um höhere Limits zu erhalten.

## <a name="account-limits"></a>Kontolimits

| Resource | Standardlimit |
| --- | --- |
| [Media Services-Konten](media-services-account-concept.md) in einem einzelnen Abonnement | 100 (feststehend) |

## <a name="asset-limits"></a>Medienobjektlimits

| Resource | Standardlimit |
| --- | --- |
| [Medienobjekte](assets-concept.md) pro Media Services-Konto | 1\.000.000|

## <a name="storage-limits"></a>Speichergrenzwerte

| Resource | Standardlimit | 
| --- | --- | 
| Dateigröße| In einigen Szenarien werden für die Verarbeitung in Media Services nur Dateien bis zu einer bestimmten Größe unterstützt. <sup>(1)</sup> |
| [Speicherkonten](storage-account-concept.md) | 100<sup>(2)</sup> (feststehend) |

<sup>1</sup> In Azure Blob Storage werden derzeit als Größe für ein einzelnes Blob bis zu 5 TB unterstützt. In Media Services gelten abhängig von den vom Dienst verwendeten VM-Größen weitere Grenzwerte. Die Größenbeschränkung gilt sowohl für die von Ihnen hochgeladenen Dateien als auch für Dateien, die in Folge der Verarbeitung durch Media Services (Codierung oder Analyse) generiert werden. Wenn Ihre Quelldatei größer als 260 GB ist, wird Ihr Auftrag wahrscheinlich nicht erfolgreich sein. 

Die folgende Tabelle enthält die Grenzwerte für die reservierten Einheiten für Medien (S1, S2 und S3). Übersteigt die Größe Ihrer Quelldatei die in der Tabelle definierten Grenzwerte, ist Ihr Codierungsauftrag nicht erfolgreich. Beim Codieren von Quellen mit 4K-Auflösung und langer Dauer müssen Sie reservierte Einheiten für Medien vom Typ „S3“ verwenden, um die erforderliche Leistung zu erhalten. Sollten Sie über 4K-Inhalte verfügen, die den Grenzwert von 260 GB für reservierte Einheiten für Medien vom Typ „S3“ übersteigen, öffnen Sie ein Supportticket.

|Reservierte Einheiten für Medien (Typ)|Maximale Eingabegröße (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Die Speicherkonten müssen aus demselben Azure-Abonnement stammen.

## <a name="jobs-encoding--analyzing-limits"></a>Auftragslimits (Codierung und Analyse)

| Resource | Standardlimit | 
| --- | --- | 
| [Aufträge](transforms-jobs-concept.md) pro Media Services-Konto | 500.000 <sup>(3)</sup> (feststehend)|
| Auftragseingaben pro Auftrag | 50  (feststehend)|
| Auftragsausgaben pro Auftrag | 20 (feststehend) |
| [Transformationen](transforms-jobs-concept.md) pro Media Services-Konto | 100  (feststehend)|
| Transformationsausgaben in einer Transformation | 20 (feststehend) |
| Dateien pro Auftragseingabe|10 (feststehend)|

<sup>3</sup> Diese Zahl umfasst fertiggestellte, aktive und abgebrochene Aufträge sowie Aufträge in der Warteschlange. Gelöschte Aufträge sind nicht enthalten. 

Alle Auftragsdatensätze in Ihrem Konto, die älter als 90 Tage sind, werden automatisch gelöscht, selbst wenn die Gesamtanzahl von Datensätzen unterhalb des maximalen Kontingents liegt. 

## <a name="live-streaming-limits"></a>Livestreaminglimits

| Resource | Standardlimit | 
| --- | --- | 
| [Liveereignisse](live-events-outputs-concept.md) <sup>(4)</sup> pro Media Services-Konto |5|
| Live-Ausgaben pro Liveereignis |3 <sup>(5)</sup> |
| Max. Dauer für Liveausgabe | [Größe des DVR-Fensters](live-event-cloud-dvr.md) |

<sup>4</sup> Ausführliche Informationen zu den Limits bei Liveereignissen finden Sie unter [Liveereignistypen – Vergleich und Limits](live-event-types-comparison.md).

<sup>5</sup> Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet.

## <a name="packaging--delivery-limits"></a>Limits für Verpackung und Bereitstellung

| Resource | Standardlimit |
| --- | --- |
| [Streamingendpunkte](streaming-endpoint-concept.md) (angehalten oder ausgeführt) pro Media Services-Konto | 2 |
| Premium-Streamingeinheiten | 10 |
| [Dynamische Manifestfilter](filters-dynamic-manifest-overview.md)|100|
| [Streamingrichtlinien](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Eindeutige [Streaminglocator](streaming-locators-concept.md), die einem Medienobjekt gleichzeitig zugeordnet sind | 100<sup>(7)</sup> (feststehend) |

<sup>6</sup> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. Sie sollten nicht für jeden Streaminglocator eine neue Streamingrichtlinie erstellen.

<sup>7</sup> Streaminglocators sind nicht für die Verwaltung der benutzerbezogenen Zugriffssteuerung konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM).

## <a name="protection-limits"></a>Schutzlimits

| Resource | Standardlimit |
| --- | --- |
| Optionen pro [Richtlinie für den Inhaltsschlüssel](content-key-policy-concept.md) |30 |
| Lizenzen pro Monat für jeden der DRM-Typen für den Media Services-Schlüsselbereitstellungsdienst pro Konto|1\.000.000|

## <a name="support-ticket"></a>Supportticket

Für Ressourcen ohne feste Vorgabe können Sie die Erhöhung von Kontingenten anfordern, indem Sie ein [Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) öffnen. Fügen Sie in die Anforderung ausführliche Informationen zu den gewünschten Kontingentänderungen, den Verwendungsfallszenarien und den benötigten Regionen ein. <br/>Erstellen Sie **keine** zusätzlichen Azure Media Services-Konten, um zu versuchen, höhere Grenzwerte zu erzielen.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](media-services-overview.md)
