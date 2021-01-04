---
title: Optimieren von Azure CDN für den Typ der Inhaltsbereitstellung
description: Hier erfahren Sie, wie Azure Content Delivery Network basierend auf dem Inhaltstyp die Übermittlung optimieren kann. Bewährte Methoden für die Optimierung verbessern die Leistung und die Entlastung des Ursprungsservers.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 7c7e4ff05307452da67c37b23bf492db8855e2ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004499"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimieren von Azure CDN für den Typ der Inhaltsbereitstellung

Wenn Sie Inhalte an eine große globale Zielgruppe übermitteln, muss die Übermittlung optimiert werden. Das [Azure Content Delivery Network (CDN)](cdn-overview.md) kann die Übermittlung basierend auf der Art der zu übermittelnden Inhalte optimieren. Bei dem Inhalt kann es sich um eine Website, einen Livestream, ein Video oder eine große Downloaddatei handeln. Beim Erstellen eines CDN-Endpunkts geben Sie in der Option **Optimiert für** ein Szenario an. Ihre Auswahl bestimmt, welche Optimierung auf die Inhalte angewendet wird, die von dem CDN-Endpunkt übermittelt werden.

Die Optimierungsoptionen basieren auf bewährten Maßnahmen zur Verbesserung der Übermittlungsleistung und zur Entlastung des Ursprungsservers. Die gewählten Szenariooptionen passen die Konfigurationen für teilweises Zwischenspeichern, Objektblockerstellung und die Wiederholungsrichtlinie für Ursprungsfehler an und beeinflussen dadurch die Leistung. 

Dieser Artikel enthält eine Übersicht über verschiedene Optimierungsfeatures sowie Informationen zu deren Verwendung. Weitere Informationen zu Features und Einschränkungen finden Sie in den entsprechenden Artikeln zum jeweiligen Optimierungstyp.

> [!NOTE]
> Bei der Erstellung eines CDN-Endpunkts können die Optionen von **Optimiert für** basierend auf dem Typ des Profils, in dem der Endpunkt erstellt wird, variieren. Azure CDN-Anbieter wenden Verbesserungen je nach Szenario auf unterschiedliche Weise an. 

## <a name="provider-options"></a>Anbieteroptionen

Profile für **Azure CDN Standard von Microsoft** unterstützen die folgenden Optimierungen:

* [Allgemeine Webbereitstellung](#general-web-delivery). Diese Optimierung wird auch für Medienstreaming und den Download großer Datei verwendet.

> [!NOTE]
> Beschleunigung dynamischer Websites von Microsoft wird über [Azure Front Door Service](../frontdoor/front-door-overview.md) angeboten.

Profile für **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** unterstützen die folgenden Optimierungen:

* [Allgemeine Webbereitstellung](#general-web-delivery). Diese Optimierung wird auch für Medienstreaming und den Download großer Datei verwendet.

* [Beschleunigung dynamischer Websites](#dynamic-site-acceleration) 


Profile für **Azure CDN Standard von Akamai** unterstützen die folgenden Optimierungen:

* [Allgemeine Webbereitstellung](#general-web-delivery) 

* [Allgemeines Medienstreaming](#general-media-streaming)

* [Video on Demand-Medienstreaming](#video-on-demand-media-streaming)

* [Download großer Dateien](#large-file-download)

* [Beschleunigung dynamischer Websites](#dynamic-site-acceleration) 

Microsoft empfiehlt, die Leistungsunterschiede zwischen verschiedenen Anbietern zu testen, um den optimalen Anbieter für die Übermittlung zu finden.

## <a name="select-and-configure-optimization-types"></a>Auswählen und Konfigurieren von Optimierungstypen

Wählen Sie beim Erstellen eines CDN-Endpunkts einen Optimierungstyp aus, der am besten zu Ihrem Szenario und zur Art der Inhalte passt, die Sie über den Endpunkt übermittelt möchten. Standardmäßig ist **Allgemeine Webbereitstellung** ausgewählt. Nur für vorhandene Endpunkte vom Typ **Azure CDN Standard von Akamai** können Sie die Optimierungsoption jederzeit aktualisieren. Die Übermittlung über Azure CDN wird durch diese Änderung nicht beeinträchtigt. 

1. Wählen Sie in einem Profil für **Azure CDN Standard von Akamai** einen Endpunkt aus.

    ![Endpunktauswahl](./media/cdn-optimization-overview/01_Akamai.png)

2. Wählen Sie unter „EINSTELLUNGEN“ die Option **Optimierung** aus. Wählen Sie anschließend in der Dropdownliste **Optimiert für** einen Typ aus.

    ![Auswählen von Optimierung und Typ](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimierung für bestimmte Szenarien

Der CDN-Endpunkt kann für eines der folgenden Szenarien optimiert werden: 

### <a name="general-web-delivery"></a>Allgemeine Webbereitstellung

Allgemeine Webbereitstellung ist die am häufigsten verwendete Optimierungsoption. Sie wird für die Optimierung allgemeiner Webinhalte wie Webseiten und Webanwendungen verwendet. Die Optimierung eignet sich aber auch für Datei- und Videodownloads.

Eine typische Website enthält sowohl statische als auch dynamische Inhalte. Statische Inhalte sind unter anderem Bilder, JavaScript-Bibliotheken und Stylesheets, die zwischengespeichert und an verschiedene Benutzer übermittelt werden können. Dynamische Inhalte werden für den jeweiligen Benutzer personalisiert. Hierzu zählen beispielsweise speziell auf ein Benutzerprofil zugeschnittene Nachrichten. Dynamische Inhalte, z. B. Warenkorbinhalte, werden nicht zwischengespeichert, da es sich hierbei um benutzerspezifische Inhalte handelt. Die allgemeine Webbereitstellung kann Ihre gesamte Website optimieren. 

> [!NOTE]
> Wählen Sie bei Verwendung eines Profils für **Azure CDN Standard von Akamai** diesen Optimierungstyp aus, wenn ihre Dateien im Schnitt kleiner als 10 MB sind. Wenn sie im Schnitt größer als 10 MB sind, wählen Sie stattdessen in der Dropdownliste **Optimiert für** die Option **Download großer Dateien** aus.

### <a name="general-media-streaming"></a>Allgemeines Medienstreaming

Wenn Sie den Endpunkt für Livestreaming und Video on Demand-Streaming verwenden müssen, wählen Sie den Optimierungstyp „Allgemeines Medienstreaming“ aus.

Medienstreaming ist ein zeitkritischer Prozess, da das verspätete Eintreffen von Paketen beim Client, z. B. häufiges Puffern von Videoinhalten, eine Beeinträchtigung der Wiedergabe zur Folge haben kann. Die Medienstreaming-Optimierung verringert die Wartezeit bei der Übermittlung von Medieninhalten und bietet dem Benutzer reibungsloses Streaming. 

Hierbei handelt es sich um ein gängiges Szenario für Azure Media Services-Kunden. Wenn Sie Azure Media Services verwenden, erhalten Sie einen einzigen Streamingendpunkt, der für Live- und On-Demand-Streaming verwendet werden kann. Dadurch müssen Kunden keinen anderen Endpunkt verwenden, wenn sie von Livestreaming zu On-Demand-Streaming wechseln. Dieses Szenario wird von der Optimierungsoption „Allgemeines Medienstreaming“ unterstützt.

Verwenden Sie für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** den Optimierungstyp „Allgemeine Webbereitstellung“, um allgemeine Streamingmedieninhalte zu übermitteln.

Weitere Informationen zur Medienstreaming-Optimierung finden Sie unter [Medienstreaming-Optimierung über das Azure Content Delivery Network](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Video on Demand-Medienstreaming

Die Optimierung „Video on Demand-Medienstreaming“ verbessert das Streamen von Video on Demand-Inhalten. Wählen Sie diese Option, wenn Sie einen Endpunkt für Video on Demand-Streaming verwenden.

Verwenden Sie bei Profilen für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** den Optimierungstyp „Allgemeine Webbereitstellung“, um Inhalte von Video on Demand-Streamingmedien zu übermitteln.

Weitere Informationen zur Medienstreaming-Optimierung finden Sie unter [Medienstreaming-Optimierung über das Azure Content Delivery Network](cdn-media-streaming-optimization.md).

> [!NOTE]
> Verwenden Sie diesen Optimierungstyp, wenn der CDN-Endpunkt in erster Linie Video on Demand-Inhalte bereitstellt. Der Hauptunterschied zwischen diesem Optimierungstyp und dem Optimierungstyp „Allgemeines Medienstreaming“ ist das Timeout für wiederholte Verbindungsversuche. Für Livestreamingszenarien wird ein erheblich kürzeres Timeout verwendet.
>

### <a name="large-file-download"></a>Download großer Dateien

Bei Verwendung von Profilen für **Azure CDN Standard von Akamai** ist der Download großer Dateien für Inhalte mit einer Größe von mehr als 10 MB optimiert. Wenn Ihre Dateien im Schnitt kleiner als 10 MB sind, verwenden Sie die Optimierung „Allgemeine Webbereitstellung“. Wenn Ihre Dateien durchweg größer als 10 MB sind, ist es unter Umständen effizienter, einen separaten Endpunkt für große Dateien zu erstellen. Firmware- oder Softwareupdates sind beispielsweise in der Regel große Dateien. Für die Bereitstellung von Dateien, die größer als 1,8 GB sind, ist die Optimierung für den Download großer Dateien erforderlich.

Verwenden Sie bei Profilen für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** den Optimierungstyp „Allgemeine Webbereitstellung“, um Inhalte umfangreicher Dateidownloads zu übermitteln. Es gibt keine Einschränkung hinsichtlich der Dateidownloadgröße.

Weitere Informationen zur Optimierung für große Dateien finden Sie unter [Optimierung großer Dateidownloads über das Azure Content Delivery Network](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Beschleunigung dynamischer Websites

 Die Beschleunigung dynamischer Websites (Dynamic Site Acceleration, DSA) steht bei Profilen für **Azure CDN Standard von Akamai**, **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** zur Verfügung. Die Verwendung dieser Optimierung ist mit zusätzlichen Kosten verbunden. Weitere Informationen finden Sie unter [Azure Content Delivery Network – Preise ](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Die Beschleunigung dynamischer Websites wird über [Azure Front Door Service](../frontdoor/front-door-overview.md) angeboten. Dabei handelt es sich um einen globalen [Anycast](https://en.wikipedia.org/wiki/Anycast)-Dienst, der das private globale Netzwerk von Microsoft zum Übermitteln Ihrer Workloads nutzt.

DSA umfasst verschiedene Maßnahmen zur Verbesserung der Wartezeit und der Leistung dynamischer Inhalte. Hierzu zählen unter anderem Routen- und Netzwerkoptimierungen sowie TCP-Optimierungen. 

Mit dieser Optimierung können Sie eine Web-App mit zahlreichen nicht zwischenspeicherbaren Antworten beschleunigen. Beispiele hierfür wären etwa Suchergebnisse, Auftragsabschlusstransaktionen oder Echtzeitdaten. Für statische Daten können Sie weiterhin die grundlegenden Azure CDN-Cachefunktionen verwenden. 

Weitere Informationen zur Beschleunigung dynamischer Websites finden Sie unter [Beschleunigung dynamischer Websites durch Azure CDN](cdn-dynamic-site-acceleration.md).