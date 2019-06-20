---
title: Orchestrieren der Implementierung von Azure DevTest Labs
description: Dieser Artikel bietet einen Leitfaden für das Orchestrieren der Implementierung von Azure DevTest Labs in Ihrer Organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127343"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orchestrieren der Implementierung von Azure DevTest Labs
Dieser Artikel stellt einen empfohlenen Ansatz für die schnelle Bereitstellung und Implementierung von Azure DevTest Labs vor. Die folgende Abbildung stellt den Gesamtprozess in Form eines normativen Leitfadens dar, berücksichtigt aber zugleich die erforderliche Flexibilität, um verschiedene Branchenanforderungen und Szenarien zu unterstützen.

![Schritte für die Implementierung von Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Annahmen
In diesem Artikel wird davon ausgegangen, dass die folgenden Elemente verfügbar sind, bevor mit einer DevTest Labs-Pilotimplementierung begonnen wird:

- **Azure-Abonnement**: Das Pilotteam hat Zugriff auf die Bereitstellung von Ressourcen in einem Azure-Abonnement. Wenn sich die Workloads nur auf Entwicklung und Test beziehen, empfiehlt sich das Enterprise DevTest-Angebot, da es zusätzliche verfügbare Images und niedrigere Sätze für virtuelle Windows-Computer bietet.
- **Lokaler Zugriff**: Falls erforderlich, wurde der lokale Zugriff bereits konfiguriert. Der lokale Zugriff kann mithilfe einer Site-to-Site-VPN-Verbindung oder über ExpressRoute realisiert werden. Die Einrichtung von Konnektivität mithilfe von ExpressRoute nimmt normalerweise viele Wochen in Anspruch – die ExpressRoute sollte bereits etabliert sein, bevor mit dem Projekt begonnen wird.
- **Pilotteams**: Die ersten für die Entwicklung zuständigen Projektteams für DevTest Labs wurden benannt, die entsprechenden Entwicklungs- und Testaktivitäten wurden definiert, und die Anforderungen/Ziele für die Teams wurden festgelegt.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Meilenstein 1: Einrichten der anfänglichen Netzwerktopologie und des ersten Netzwerkentwurfs
Der erste Schwerpunkt bei der Bereitstellung einer Azure DevTest Labs-Lösung liegt in der Herstellung der geplanten Konnektivität für die virtuellen Computer. Die folgenden Schritte beschreiben die erforderlichen Prozeduren:

1. Definieren der **anfänglichen IP-Adressbereiche**, die dem DevTest Labs-Abonnement in Azure zugewiesen werden. Für diesen Schritt muss die erwartete zukünftige Nutzung in Form der Anzahl der VMs eingeschätzt werden, damit ein für zukünftiges Wachstum ausreichend großer Block bereitgestellt werden kann.
2. Identifizieren der **gewünschten Zugriffsmethoden** auf die DevTest Labs (beispielsweise externer/interner Zugriff). Ein wichtiger Punkt bei diesem Schritt besteht in der Festlegung, ob virtuelle Computer über öffentliche IP-Adressen (so dass sie vom Internet aus direkt zugänglich sind) verfügen sollen.
3. Identifizieren und Einrichten von **Konnektivitätsmethoden** mit der übrigen Azure-Cloudumgebung und lokalen Ressourcen. Wenn erzwungenes Routing mit ExpressRoute aktiviert ist, benötigen die virtuellen Computer für den Durchgang durch die Unternehmensfirewall wahrscheinlich geeignete Proxykonfigurationen.
4. Wenn VMs **Domäneneinbindung** erhalten sollen, legen Sie fest, ob sie einer cloudbasierten Domäne (beispielsweise AAD Directory Services) oder einer lokalen Domäne beitreten sollen. Legen Sie im Fall der lokalen Domäne fest, zu welcher Organisationseinheit (OU) in Active Directory die virtuellen Computer gehören sollen. Überprüfen Sie ferner, ob Benutzer Zugriff haben (richten Sie ggf. ein Dienstkonto ein, das Computereinträge in der Domäne erstellen kann).

## <a name="milestone-2-deploy-the-pilot-lab"></a>Meilenstein 2: Bereitstellen des Pilot-Labs
Sobald die Netzwerktopologie eingerichtet ist, kann das Pilot-Lab mithilfe der folgenden Schritte erstellt werden:

1. Erstellen einer anfänglichen DevTest Labs-Umgebung (Schritt-für-Schritt Anweisungen finden Sie [hier](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md)).
2. Bestimmen zulässiger VM-Images und -Größen für die Nutzung im Lab. Entscheiden Sie, ob benutzerdefinierte Images für die Verwendung in DevTest Labs auf Azure hochgeladen werden können.
3. Schützen des Zugriffs auf das Lab durch Erstellen von rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC) für das Lab (Lab-Besitzer und Lab-Benutzer). Wir empfehlen die Verwendung synchronisierter Active Directory-Konten in Azure Active Directory für die Identität in DevTest Labs.
4. Konfigurieren von DevTest Labs für die Verwendung von Richtlinien, wie etwa Zeitplänen, Kostenverwaltung, abrufbaren VMs, benutzerdefinierten Images oder Formeln.
5. Einrichten eines Onlinerepositorys wie Azure Repos/Git.
6. Entscheidung für die Verwendung öffentlicher oder privater Repositorys bzw. einer Kombination aus beiden. Stellen Sie JSON-Vorlagen für Bereitstellungen und langfristig vorbeugende Wartung bereit.
7. Erstellen benutzerdefinierter Artefakte bei Bedarf. Dieser Schritt ist optional. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Meilenstein 3: Dokumentation, Support, Lernen und Verbessern
Die anfänglichen Pilotteams benötigen möglicherweise eingehenden Support für den Einstieg. Nutzen Sie ihre Erfahrungen, um sicherzustellen, dass für das fortgesetzte Rollout von Azure DevTest Labs die passende Dokumentation und geeigneter Support verfügbar sind.

1. Führen Sie die Pilotteams in ihre neuen DevTest Labs-Ressourcen ein (Demos, Dokumentation).
2. Planen Sie ausgehend von den Erfahrungen der Pilotteams Dokumentation nach Bedarf, und stellen Sie sie entsprechend zur Verfügung.
3. Formalisieren Sie den Prozess für das Onboarding neuer Teams (Erstellen und Konfigurieren von Labs, Erteilen des Zugriffs usw.).
4. Überprüfen Sie auf der Grundlage der anfänglichen Akzeptanz, ob die ursprüngliche Einschätzung des IP-Adressbereichs noch realistisch und ausreichend genau ist.
5. Stellen Sie sicher, dass geeignete Überprüfungen von Compliance und Sicherheit durchgeführt werden.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den nächsten Artikel in dieser Reihe: [Governance der Azure DevTest Labs-Infrastruktur](devtest-lab-guidance-governance-resources.md)
