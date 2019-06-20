---
title: Azure Front Door Service – Unterstützung für HTTP2 | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur HTTP/2-Unterstützung in Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 33e738f31be493d5890fc972ec71e7c6cd733bf4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736447"
---
# <a name="http2-support-in-azure-front-door-service"></a>HTTP/2-Unterstützung in Azure Front Door Service
HTTP/2 ist eine größere Überarbeitung von HTTP/1.1. Sie bietet eine höhere Webleistung, kürzere Antwortzeiten und eine verbesserte Benutzeroberfläche, während gleichzeitig die gewohnten HTTP-Methoden, Statuscodes und Semantik beibehalten werden. Obwohl HTTP/2 darauf ausgelegt ist, unter HTTP und HTTPS zu funktionieren, unterstützen viele Clientwebbrowser HTTP/2 nur über TLS (Transport Layer Security).

### <a name="http2-benefits"></a>Vorteile von HTTP/2

HTTP/2 bietet unter anderem folgende Vorteile:

*   **Multiplexing und Parallelität**

    Bei der Verwendung von HTTP 1.1 benötigt man für die Anforderung mehrerer Ressourcen mehrere TCP-Verbindungen, und mit jeder Verbindung ist Leistungsaufwand verbunden. HTTP/2 ermöglicht die Anforderung mehrerer Ressourcen über eine einzelne TCP-Verbindung.

*   **Header-Komprimierung**

    Durch das Komprimieren von HTTP-Headern für bereitgestellte Ressourcen wird die Zeit bei der Übertragung erheblich reduziert.

*   **Datenstrom-Abhängigkeiten**

    Datenstromabhängigkeiten ermöglichen dem Client, dem Server anzugeben, welche Ressourcen Vorrang haben.


## <a name="http2-browser-support"></a>HTTP/2-Browserunterstützung

Alle wichtigen Browser haben HTTP/2-Unterstützung in ihren aktuellen Versionen implementiert. Nicht unterstützte Browser führen ein automatisches Fallback auf HTTP/1.1 durch.

|"Browser"|Mindestversion|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Aktivieren der HTTP/2-Unterstützung in Azure Front Door Service

Derzeit ist die HTTP/2-Unterstützung für alle Front Door-Konfigurationen aktiviert. Es ist keine weitere Aktion vom Kunden erforderlich.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu HTTP/2 finden Sie in den folgenden Ressourcen:

- [HTTP/2-Homepage](https://http2.github.io/) (in englischer Sprache)
- [Häufig gestellte Fragen zu HTTP/2 (offiziell)](https://http2.github.io/faq/) (in englischer Sprache)
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
