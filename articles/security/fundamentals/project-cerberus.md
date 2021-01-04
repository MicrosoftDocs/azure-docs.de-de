---
title: 'Firmwareintegrität: Azure Security'
description: Hier finden Sie Informationen zu kryptografischen Messungen, um die Firmwareintegrität sicherzustellen.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557380"
---
# <a name="project-cerberus"></a>Project Cerberus

Bei Cerberus handelt es sich um einen NIST 800-193-konformen Hardware-Vertrauensanker mit einer nicht klonbaren Identität. Cerberus wurde entwickelt, um den Sicherheitsstatus von Azure-Infrastruktur durch die Bereitstellung eines starken Vertrauensankers für die Firmwareintegrität noch weiter zu verbessern.

## <a name="enabling-an-anchor-of-trust"></a>Aktivieren eines Vertrauensankers
Jeder Cerberus-Chip verfügt über eine eindeutige kryptografische Identität. Diese wird mit einer signierten Zertifikatkette etabliert, die in einer Microsoft-Zertifizierungsstelle (certificate authority, CA) verankert ist. Die von Cerberus erhaltenen Messungen ermöglichen die Überprüfung der Integrität von Komponenten wie:

- Host
- Baseboard-Verwaltungscontroller
- Alle Peripheriegeräte, einschließlich Netzwerkschnittstellenkarte und [System-on-a-Chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC)

Dieser Vertrauensanker trägt dazu bei, die Plattformfirmware vor Folgendem zu schützen:

- Kompromittierte Firmwarebinärdateien, die auf der Plattform ausgeführt werden
- Schadsoftware und Hacker, die Fehler im Betriebssystem, in der Anwendung oder in Hypervisor ausnutzen
- Bestimmte Arten von Lieferkettenangriffen (Fertigung, Montage, Transport)
- Böswillige Insider mit Administratorrechten oder Hardwarezugriff

## <a name="cerberus-attestation"></a>Cerberus-Nachweis
Cerberus authentifiziert die Firmwareintegrität für Serverkomponenten mithilfe eines Plattformfirmwaremanifests (PFM). Das PFM definiert eine Liste autorisierter Firmwareversionen und bietet eine Plattformmessung für den [Hostnachweisdienst](measured-boot-host-attestation.md) von Azure. Der Hostnachweisdienst überprüft die Messungen und sorgt dafür, dass nur vertrauenswürdige Hosts der Azure-Flotte beitreten und Kundenworkloads hosten können.

In Verbindung mit dem Hostnachweisdienst erweitern und fördern die Cerberus-Funktionen eine äußerst sichere Azure-Produktionsinfrastruktur.

> [!NOTE]
> Weitere Informationen finden Sie auf GitHub unter [Project Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu unseren Anstrengungen, die Integrität und Sicherheit von Plattformen zu erhöhen, finden Sie unter:

- [Firmwaresicherheit](firmware.md)
- [Sicherer Start](secure-boot.md)
- [Gemessener Start- und Hostnachweis](measured-boot-host-attestation.md)
- [Verschlüsselung ruhender Daten](encryption-atrest.md)
- [Hypervisor-Sicherheit](hypervisor.md)