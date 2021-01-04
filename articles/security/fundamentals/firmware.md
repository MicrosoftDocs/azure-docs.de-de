---
title: Firmwaresicherheit – Azure Security
description: Erfahren Sie, wie Microsoft Azure-Hardware und -Firmware schützt.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557446"
---
# <a name="firmware-security"></a>Firmwaresicherheit
In diesem Artikel wird beschrieben, wie Microsoft das Cloudhardware-Ökosystem und Lieferketten schützt.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Schützen des Cloudhardware-Ökosystems
Microsoft geht innerhalb des Cloudhardware-Ökosystems aktiv Partnerschaften ein, um kontinuierliche Sicherheitsverbesserungen voranzutreiben durch:

- Zusammenarbeit mit Azure-Hardware- und -Firmware-Partnern (z. B. Komponentenherstellern und Systemintegratoren), um die Sicherheitsanforderungen an Azure-Hardware- und -Firmware zu erfüllen.

- Unterstützung der Partner bei der Durchführung kontinuierlicher Bewertungen und Verbesserungen der Sicherheit ihrer Produkte anhand von durch Microsoft definierten Anforderungen in Bereichen wie:

  - Sicherer Start der Firmware
  - Sichere Wiederherstellung der Firmware
  - Sicheres Update für Firmware
  - Kryptografie der Firmware
  - Gesperrte Hardware
  - Differenzierte Debugtelemetrie
  - Systemunterstützung für TPM 2.0-Hardware, um gemessene Starts zu ermöglichen

- Beteiligung am Sicherheitsprojekt [Open Compute Project (OCP)](https://www.opencompute.org/wiki/Security) durch die Entwicklung von Spezifikationen. Spezifikationen fördern Konsistenz und Klarheit für ein sicheres Design und eine sichere Architektur im Ökosystem.

   > [!NOTE]
   > Ein Beispiel für unseren Beitrag zum OCP-Sicherheitsprojekt ist die [Hardware Secure Boot](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0)-Spezifikation.

## <a name="securing-hardware-and-firmware-supply-chains"></a>Schützen von Hardware- und Firmwarelieferketten
Cloudhardwarelieferanten und -anbieter für Azure müssen außerdem die von Microsoft entwickelten Lieferketten-Sicherheitsprozesse und -anforderungen einhalten. Die Entwicklungs- und Bereitstellungsprozesse für Hardware und Firmware müssen den Microsoft [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) (SDL)-Prozessen folgen, wie z. B.:

- Bedrohungsmodellierung
- Überprüfungen sicherer Entwürfe
- Firmwareüberprüfungen und Penetrationstests
- Sichere Entwicklungs- und Testumgebungen
- Verwaltung von Sicherheitsrisiken und Reaktion auf Vorfälle

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen darüber, was wir tun, um die Integrität und Sicherheit von Plattformen voranzutreiben, finden Sie unter:

- [Sicherer Start](secure-boot.md)
- [Gemessener Start- und Hostnachweis](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Verschlüsselung ruhender Daten](encryption-atrest.md)
- [Hypervisor-Sicherheit](hypervisor.md)