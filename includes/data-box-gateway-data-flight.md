---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581096"
---
Für Daten während einer Übertragung:

- Standardmäßiges TLS 1.2 wird für Daten verwendet, die zwischen dem Gerät und Azure übertragen werden. Es erfolgt kein Fallback auf TLS 1.1 oder auf ältere Versionen. Agent-Kommunikation wird blockiert, wenn TLS 1.2 nicht unterstützt wird. TLS 1.2 wird auch für Portal- und SDK-Verwaltung benötigt.
- Wenn Clients über die lokale Webbenutzeroberfläche eines Browsers auf Ihr Gerät zugreifen, wird standardmäßiges TLS 1.2 als sicheres Standardprotokoll verwendet.

    - Es empfiehlt sich, Ihren Browser für die Verwendung von TLS 1.2 zu konfigurieren.
    - Sollte der Browser TLS 1.2 nicht unterstützen, können Sie TLS 1.1 oder TLS 1.0 verwenden.
- Sie sollten SMB 3.0 mit Verschlüsselung verwenden, um Daten zu schützen, wenn Sie diese von Ihren Datenservern kopieren.
