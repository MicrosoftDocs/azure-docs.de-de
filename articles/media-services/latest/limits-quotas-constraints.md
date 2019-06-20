---
title: Kontingente und Einschränkungen für Azure Media Services (v3) | Microsoft-Dokumentation
description: In diesem Thema werden die Kontingente und Einschränkungen für Azure Media Services v3 beschrieben.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/16/2019
ms.author: juliako
ms.openlocfilehash: 1aa15a42893d867ae18c267e163e8df94af50723
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824452"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kontingente und Einschränkungen für Azure Media Services (v3)

In diesem Artikel werden die Kontingente und Einschränkungen für Azure Media Services v3 beschrieben.

| Resource | Standardlimit | 
| --- | --- | 
| Medienobjekte pro Azure Media Services-Konto | 1.000.000|
| Dynamische Manifestfilter|100|
| „JobInputs“ pro Auftrag | 50  (feststehend)|
| „JobOutputs“ pro Auftrag | 20 (feststehend) |
| „TransformOutputs“ in einer Transformation | 20 (feststehend) |
| Dateien pro „JobInput“|10 (feststehend)|
| Dateigröße| In einigen Szenarien werden für die Verarbeitung in Media Services nur Dateien bis zu einer bestimmten Größe unterstützt. <sup>(1)</sup> |
| Aufträge pro Media Services-Konto | 500.000 <sup>(2)</sup> (feststehend)|
| Auflisten von Transformationen|Paginieren Sie die Antwort mit 1.000 Transformationen pro Seite|
| Auflisten von Aufträgen|Paginieren Sie die Antwort mit 500 Transformationen pro Seite|
| Liveereignisse pro Media Services-Konto |5|
| Media Services-Konten in einem einzelnen Abonnement | 25 (feststehend) |
| Live-Ausgaben pro Liveereignis |3 <sup>(3)</sup> |
| Max. Dauer für Liveausgabe | 25 Stunden |
| Speicherkonten | 100<sup>(4)</sup> (feststehend) |
| Streamingendpunkte (angehalten oder ausgeführt) pro Media Services-Konto|2 (feststehend)|
| Streamingrichtlinien | 100 <sup>(5)</sup> |
| Transformationen pro Media Services-Konto | 100  (feststehend)|
| Eindeutige Streaminglocator, die einem Medienobjekt gleichzeitig zugeordnet sind | 100<sup>(6)</sup> (feststehend) |
| Optionen pro Richtlinie für den Inhaltsschlüssel |30 | 

<sup>1</sup> In Azure Blob Storage werden derzeit als Größe für ein einzelnes Blob bis zu 5 TB unterstützt. In Media Services gelten abhängig von den vom Dienst verwendeten VM-Größen weitere Grenzwerte. Die Größenbeschränkung gilt sowohl für die von Ihnen hochgeladenen Dateien als auch für Dateien, die in Folge der Verarbeitung durch Media Services (Codierung oder Analyse) generiert werden. Wenn Ihre Quelldatei größer als 260 GB ist, wird Ihr Auftrag wahrscheinlich nicht erfolgreich sein. 

Die folgende Tabelle enthält die Grenzwerte für die reservierten Einheiten für Medien (S1, S2 und S3). Übersteigt die Größe Ihrer Quelldatei die in der Tabelle definierten Grenzwerte, ist Ihr Codierungsauftrag nicht erfolgreich. Beim Codieren von Quellen mit 4K-Auflösung und langer Dauer müssen Sie reservierte Einheiten für Medien vom Typ „S3“ verwenden, um die erforderliche Leistung zu erhalten. Sollten Sie über 4K-Inhalte verfügen, die den Grenzwert von 260 GB für reservierte Einheiten für Medien vom Typ „S3“ übersteigen, setzen Sie sich amshelp@microsoft.com mit uns in Verbindung, um Informationen zu möglichen Lösungen für Ihr Szenario zu erhalten.

|Reservierte Einheiten für Medien (Typ)   |Maximale Eingabegröße (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Diese Zahl umfasst fertig gestellte, aktive und abgebrochene Aufträge sowie Aufträge in der Warteschlange. Gelöschte Aufträge sind nicht enthalten. 

Alle Auftragsdatensätze in Ihrem Konto, die älter als 90 Tage sind, werden automatisch gelöscht, selbst wenn die Gesamtanzahl von Datensätzen unterhalb des maximalen Kontingents liegt. 

<sup>3</sup> Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet.

<sup>4</sup> Die Speicherkonten müssen aus demselben Azure-Abonnement stammen.

<sup>5</sup> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](https://docs.microsoft.com/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. Sie sollten nicht für jeden Streaminglocator eine neue Streamingrichtlinie erstellen.

<sup>6</sup> Streaminglocators sind nicht für die Verwaltung der benutzerbezogenen Zugriffssteuerung konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM).

## <a name="support-ticket"></a>Supportticket

Für Ressourcen ohne feste Vorgabe können Sie die Erhöhung von Kontingenten anfordern, indem Sie ein [Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) öffnen. Fügen Sie in die Anforderung ausführliche Informationen zu den gewünschten Kontingentänderungen, den Verwendungsfallszenarien und den benötigten Regionen ein. <br/>Erstellen Sie **keine** zusätzlichen Azure Media Services-Konten, um zu versuchen, höhere Grenzwerte zu erzielen.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](media-services-overview.md)
