---
title: Hypervisor-Sicherheit in der Azure-Flotte – Azure Security
description: Technische Übersicht über die Hypervisor-Sicherheit in der Azure-Flotte.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696114"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Hypervisor-Sicherheit in der Azure-Flotte

Das Azure-Hypervisorsystem basiert auf Windows Hyper-V. Das Hypervisorsystem ermöglicht es dem Computer Administrator, Gastpartitionen anzugeben, die separate Adressräume aufweisen. Die separaten Adressräume ermöglichen es Ihnen, parallel zum (Host-)Betriebssystem, das auf der Stammpartition des Computers ausgeführt wird, ein Betriebssystem und Anwendungen zu laden. Das Hostbetriebssystem (auch als privilegierte Stammpartition bezeichnet) verfügt über direkten Zugriff auf alle physischen und Peripheriegeräte auf dem System (Speichercontroller, Netzwerkadapter). Das Hostbetriebssystem ermöglicht es Gastpartitionen, diese physischen Geräte gemeinsam zu nutzen, indem es „virtuelle Geräte“ für jede Gastpartition verfügbar macht. Daher hat ein Betriebssystem, das in einer Gastpartition ausgeführt wird, Zugriff auf virtualisierte Peripheriegeräte, die von Virtualisierungsdiensten bereitgestellt werden, die in der Stammpartition ausgeführt werden.

Der Azure-Hypervisor ist so aufgebaut, dass er die folgenden Sicherheitsziele berücksichtigt:

| Ziel | `Source` |
|--|--|
| Isolation | Eine Sicherheitsrichtlinie verlangt, dass keine Informationen zwischen virtuellen Computern übertragen werden. Diese Einschränkung erfordert Funktionen im Virtual Machine Manager (VMM) und Hardware für die Isolation von Arbeitsspeicher, Geräten, Netzwerk und verwalteten Ressourcen wie persistente Daten. |
| VMM-Integrität | Um die allgemeine Systemintegrität sicherzustellen, wird die Integrität einzelner Hypervisorkomponenten eingerichtet und verwaltet. |
| Plattformintegrität | Die Integrität des Hypervisors hängt von der Integrität der Hardware und Software ab, auf der er aufbaut. Obwohl der Hypervisor keine direkte Kontrolle über die Integrität der Plattform hat, setzt Azure auf Hardware- und Firmwaremechanismen wie den [Cerberus](project-cerberus.md)-Chip, um die Integrität der zugrunde liegenden Plattform zu schützen und zu erkennen. VMM und Gäste können nicht ausgeführt werden, wenn die Plattformintegrität beeinträchtigt ist. |
| Eingeschränkter Zugriff | Verwaltungsfunktionen werden nur von autorisierten Administratoren über sichere Verbindungen ausgeführt. Das Prinzip der geringsten Rechte wird durch Mechanismen der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) erzwungen. |
| Audit | Azure unterstützt Überwachungsfunktionen, um Daten zu den Vorgängen auf einem System zu erfassen und zu schützen, damit diese später untersucht werden können. |

Der Ansatz von Microsoft für die Härtung des Azure-Hypervisors und des Virtualisierungssubsystems lässt sich in die folgenden drei Kategorien unterteilen.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Durch den Hypervisor erzwungene stark definierte Sicherheitsgrenzen

Der Azure-Hypervisor erzwingt mehrere Sicherheitsgrenzen zwischen folgenden Komponenten:

- Virtualisierten Gastpartitionen und privilegierten Partitionen (Host)
- Mehreren Gästen
- Dem Hypervisor selbst und dem Host
- Dem Hypervisor selbst und allen Gästen

Vertraulichkeit, Integrität und Verfügbarkeit sind für die Hypervisorsicherheitsgrenzen sichergestellt. Die Grenzen schützen vor einer Reihe von Angriffen, einschließlich Informationslecks im Seitenkanal, Denial of Service und Rechteerweiterungen.

Die Hypervisorsicherheitsgrenze bietet auch eine Segmentierung zwischen Mandanten bei Netzwerkdatenverkehr, virtuellen Geräten, Speicher, Compute- und alle anderen VM-Ressourcen.

## <a name="defense-in-depth-exploit-mitigations"></a>Tiefgehende Verteidigung zur Angriffsentschärfung

Für den unwahrscheinlichen Fall, dass eine Sicherheitsgrenze ein Sicherheitsrisiko aufweist, verfügt der Azure-Hypervisor über mehrere Entschärfungsebenen, z. B.:

- Isolation des hostbasierten Prozesses, der VM-übergreifende Komponenten hostet
- Virtualisierungsbasierte Sicherheit (VBS) zum Sicherstellen der Integrität von Benutzer- und Kernelmoduskomponenten aus einer Secure World
- Mehrere Ebenen von Exploitentschärfungen. Zu den Entschärfungen zählen die zufällige Anordnung von Adressraumlayouts (Address Space Layout Randomization, ASLR), Datenausführungsverhinderung (Data Execution Prevention, DEP), Schutz vor beliebigem Code, Integrität der Ablaufsteuerung und Verhinderung von Datenbeschädigung.
- Automatische Initialisierung von Stapelvariablen auf Compilerebene
- Kernel-APIs, die von Hyper-V vorgenommene Kernelheapzuordnungen automatisch mit null initialisieren

Diese Entschärfungen sind darauf ausgelegt, die Entwicklung eines Exploits für ein VM-übergreifendes Sicherheitsrisiko unmöglich zu machen.

## <a name="strong-security-assurance-processes"></a>Starke Sicherheitszusicherungsprozesse

Die Angriffsfläche im Zusammenhang mit dem Hypervisor umfasst Softwarenetzwerke, virtuelle Geräte und alle VM-übergreifenden Flächen. Die Angriffsfläche wird über die automatisierte Buildintegration verfolgt, die regelmäßige Sicherheitsüberprüfungen auslöst.

Unser RED-Team hat für alle VM-Angriffsflächen im Hinblick auf Risiken für die Sicherheitsgrenzen Bedrohungsmodelle erstellt, Code Reviews, Fuzzing und Tests durchgeführt. Microsoft verfügt über ein [Bounty-Programm](https://www.microsoft.com/msrc/bounty-hyper-v), das für relevante Sicherheitsrisiken in qualifizierten Produktversionen für Microsoft Hyper-V Prämien vergibt.

> [!NOTE]
> Weitere Informationen zu den [starken Sicherheitszusicherungsprozessen](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) in Hyper-V

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu unseren Anstrengungen, die Integrität und Sicherheit von Plattformen zu erhöhen, finden Sie unter:

- [Firmwaresicherheit](firmware.md)
- [Sicherer Start](secure-boot.md)
- [Gemessener Start- und Hostnachweis](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Verschlüsselung ruhender Daten](encryption-atrest.md)