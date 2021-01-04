---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465732"
---
Für Daten während einer Übertragung:

- Standardmäßiges TLS 1.2 (Transport Layer Security) wird für Daten verwendet, die zwischen dem Gerät und Azure übertragen werden. Es erfolgt kein Fallback auf TLS 1.1 oder auf ältere Versionen. Agent-Kommunikation wird blockiert, wenn TLS 1.2 nicht unterstützt wird. TLS 1.2 wird auch für Portal- und SDK-Verwaltung benötigt.
- Wenn Clients über die lokale Webbenutzeroberfläche eines Browsers auf Ihr Gerät zugreifen, wird standardmäßiges TLS 1.2 als sicheres Standardprotokoll verwendet.

  - Es empfiehlt sich, Ihren Browser für die Verwendung von TLS 1.2 zu konfigurieren.
  - Ihr Gerät unterstützt nur TLS 1.2 und nicht die älteren Versionen TLS 1.1 oder TLS 1.0.
- Sie sollten SMB 3.0 mit Verschlüsselung verwenden, um Daten zu schützen, wenn Sie diese von Ihren Datenservern kopieren.
