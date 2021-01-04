---
title: Von Firmware gemessener Start- und Hostnachweis – Azure Security
description: Technische Übersicht über den von Azure-Firmware gemessenen Start- und Hostnachweis.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498862"
---
# <a name="measured-boot-and-host-attestation"></a>Gemessener Start- und Hostnachweis
In diesem Artikel wird beschrieben, wie Microsoft die Integrität und Sicherheit von Hosts durch gemessenen Start- und Hostnachweis gewährleistet.

## <a name="measured-boot"></a>Gemessener Start

Das [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) ist eine manipulationsgeschützte, kryptografisch sichere Überwachungskomponente mit Firmware, die von einem vertrauenswürdigen Drittanbieter bereitgestellt wird. Das Startkonfigurationsprotokoll enthält hashverkettete Messungen, die in den Platfformkonfigurationsregistern (PCR) beim letzten Durchlauf der Bootstrappingsequenz seitens des Hosts aufgezeichnet wurden. In der folgenden Abbildung wird dieser Aufzeichnungsprozess veranschaulicht. Durch inkrementelles Hinzufügen einer früheren Hashmessung zum Hash der nächsten Messung und Ausführen des Hashalgorithmus für die Vereinigungsmenge wird die Hashverkettung erreicht.

![Diagramm der Hashverkettung des Hostnachweisdiensts.](./media/measured-boot-host-attestation/hash-chaining.png)

Der Nachweis erfolgt, wenn ein Host einen Nachweis seines Konfigurationszustands mithilfe seines Startkonfigurationsprotokolls (TCGLog) bereitstellt. Die Fälschung eines Startprotokolls ist schwierig, da das TPM seine PCR-Werte mit Ausnahme von Lese- und Erweiterungsvorgängen nicht verfügbar macht. Außerdem sind die vom Hostnachweisdienst bereitgestellten Anmeldeinformationen für bestimmte PCR-Werte versiegelt. Die Verwendung von Hashverkettung macht es rechnerisch unmöglich, die Anmeldeinformationen „Out-of-Band“ zu fälschen oder zu entsiegeln.

## <a name="host-attestation-service"></a>Hostnachweisdienst

Der Hostnachweisdienst ist eine Präventionsmaßnahme, mit der überprüft wird, ob Hostcomputer vertrauenswürdig sind, bevor ihnen gestattet wird, mit Kundendaten oder Workloads zu interagieren. Der Hostnachweisdienst führt die Prüfung durch Validieren einer Konformitätserklärung (verifizierbarer Nachweis der Compliance des Hosts) durch, die von jedem Host anhand einer Nachweisrichtlinie (Definition des sicheren Zustands) übermittelt wird. Die Integrität dieses Systems wird durch einen [Vertrauensanker](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) gewährleistet, der von einem TPM bereitgestellt wird.

Der Hostnachweisdienst ist in jedem Azure-Cluster innerhalb einer spezialisierten gesperrten Umgebung vorhanden. Die gesperrte Umgebung umfasst andere Gatekeeperdienste, die am Bootstrappingprotokoll des Hostcomputers beteiligt sind. Eine Public Key-Infrastruktur (PKI) fungiert als Vermittler für die Validierung der Herkunft von Nachweisanforderungen und als Identitätsaussteller (abhängig von einem erfolgreichen Hostnachweis). Die nach dem Hostnachweis für den Host ausgestellten Anmeldeinformationen werden für seine Identität versiegelt. Nur der anfordernde Host kann die Anmeldeinformationen entsiegeln und zur Erlangung inkrementeller Berechtigungen nutzen. Dies schützt vor Man-in-the-Middle- und Spoofingangriffen.

Wenn ein Azure-Host eine werkseitig fehlerhafte Sicherheitskonfiguration aufweist oder im Rechenzentrum manipuliert wird, enthält sein TCGLog Anzeichen für eine Kompromittierung, die vom Hostnachweisdienst beim nächsten Nachweis markiert werden, wodurch der Nachweis fehlschlägt. Nachweisfehlern verhindern, dass die Azure-Flotte dem angreifenden Host vertraut. Diese Verhinderung blockiert wirksam die gesamte Kommunikation zum und vom Host und löst einen Incidentworkflow aus. Die Untersuchung und ausführliche Post-Mortem-Analyse werden durchgeführt, um die Ursachen und mögliche Anzeichen für eine Gefährdung zu ermitteln. Erst nach Abschluss dieser Analyse wird ein Host bereinigt und kann sich der Azure-Flotte anschließen und Kundenworkloads übernehmen.

Im folgenden finden Sie eine allgemeine Architektur des Hostnachweisdiensts:

![Diagramm der Architektur des Hostnachweisdiensts.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Nachweismessungen

Nachfolgend finden Sie Beispiele für die vielen heute erfassten Messungen.

### <a name="secure-boot-and-secure-boot-keys"></a>Secure Boot (sicherer Start) und Secure Boot-Schlüssel
Durch Validierung der Richtigkeit der Digests der Signaturdatenbank und der Datenbank für widerrufene Signaturen stellt der Hostnachweisdienst sicher, dass der Client-Agent die richtige Software für vertrauenswürdig hält. Durch die Validierung der Signaturen der Registrierungsschlüsseldatenbank für öffentliche Schlüssel und des öffentlichen Plattformschlüssels stellt der Hostnachweisdienst sicher, dass nur vertrauenswürdige Parteien die Berechtigung erhalten, die Definitionen der als vertrauenswürdig geltenden Software zu ändern. Schließlich stellt der Hostnachweisdienst sicher, dass Secure Boot aktiv ist und diese Definitionen durchgesetzt werden.

### <a name="debug-controls"></a>Debugsteuerelemente
Debugger sind leistungsstarke Tools für Entwickler. Der ungehinderte Zugriff auf Arbeitsspeicher- und andere Debugbefehle könnte jedoch den Datenschutz und die Systemintegrität schwächen, wenn er einer nicht vertrauenswürdigen Partei gewährt würde. Der Hostnachweisdienst stellt sicher, dass beim Start auf Produktionscomputern alle Arten von Debuggen deaktiviert werden.

### <a name="code-integrity"></a>Codeintegrität
UEFI [Secure Boot](secure-boot.md) gewährleistet, dass während der Startsequenz nur vertrauenswürdige Low-Level-Software ausgeführt werden kann. Dieselben Prüfungen müssen jedoch auch in der Umgebung nach dem Start auf Treiber und andere ausführbare Dateien mit Zugriff im Kernel-Modus angewendet werden. Zu diesem Zweck wird eine Codeintegritätsrichtlinie (CI) verwendet, um durch die Angabe gültiger und ungültiger Signaturen zu definieren, welche Treiber, Binärdateien und anderen ausführbaren Dateien als vertrauenswürdig gelten. Diese Richtlinien werden erzwungen. Verletzungen der Richtlinie generieren Warnungen an das Team zur Reaktion auf Sicherheitsincidents, damit diese untersucht werden können.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen darüber, was wir tun, um die Integrität und Sicherheit von Plattformen voranzutreiben, finden Sie unter:

- [Firmwaresicherheit](firmware.md)
- [Sicherer Start](secure-boot.md)
- [Project Cerberus](project-cerberus.md)
- [Verschlüsselung ruhender Daten](encryption-atrest.md)
- [Hypervisor-Sicherheit](hypervisor.md)