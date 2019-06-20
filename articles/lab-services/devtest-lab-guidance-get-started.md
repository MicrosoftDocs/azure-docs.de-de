---
title: Gängige Szenarien für die Verwendung von Azure DevTest Labs
description: Dieser Artikel enthält die Hauptszenarien zur Verwendung von Azure DevTest Labs und zwei allgemeine Pfade zur Verwendung des Diensts in Ihrer Organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773807"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Gängige Szenarien für die Verwendung von Azure DevTest Labs
Basierend auf den jeweiligen Anforderungen eines Unternehmens kann DevTest Labs so konfiguriert werden, dass die unterschiedlichen Bedürfnisse erfüllt werden.  In diesem Artikel werden die gängigen Szenarien beschrieben. In jedem Szenario geht es um die Vorteile, die sich durch die Verwendung von DevTest Labs und der entsprechenden Ressourcen zur Implementierung der Szenarien ergeben.  

- Entwicklerdesktops
- Testumgebungen
- Schulungssitzungen, praktische Übungen und Hackathons
- Sandkastenuntersuchungen
- Classroom-Labs

## <a name="developer-desktops"></a>Entwicklerdesktops
Entwickler haben oft andere Anforderungen an Entwicklungscomputer für unterschiedliche Projekte. Mit DevTest Labs können Entwickler auf bedarfsgesteuerte virtuelle Computer zugreifen, die für ihre gängigsten Szenarien konfiguriert sind. DevTest Labs bietet folgende Vorteile:

- Organisationen können gemeinsame Entwicklungscomputer bereitstellen, die teamübergreifende Konsistenz sicherstellen.
- Entwickler können ihre Entwicklungscomputer bei Bedarf schnell bereitstellen oder [einen vorhandenen vorkonfigurierten Computer anfordern](devtest-lab-add-claimable-vm.md).
- Entwickler können Ressourcen per Self-Service-Verfahren bereitstellen, ohne Berechtigungen auf Abonnementebene zu benötigen.
- Die IT-Abteilung oder Administratoren können die [Netzwerktopologie vorab definieren](devtest-lab-configure-vnet.md), und Entwickler können sie direkt auf einfache und intuitive Weise ohne spezielle Zugriffsberechtigungen nutzen.
- Entwickler können ihre Entwicklungscomputer einfach je nach Bedarf [anpassen](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm).
- Administratoren können die Kosten steuern, indem sie Folgendes sicherstellen:
    - Entwickler können [nicht mehr virtuelle Computer erhalten](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user), als sie für die Entwicklung benötigen.
    - [Virtuelle Computer werden heruntergefahren](devtest-lab-set-lab-policy.md#set-auto-shutdown), wenn sie nicht in Gebrauch sind.
    - Für bestimmte Labs wird nur [eine Teilmenge der VM-Instanzgrößen zugelassen](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes).
    - [Verwalten Sie Kostenziele und Benachrichtigungen](devtest-lab-configure-cost-management.md) für jedes Lab.

Weitere Informationen finden Sie unter [Verwenden von Azure DevTest Labs durch Entwickler](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Testumgebungen
Das Erstellen und Verwalten von Testumgebungen in einem Unternehmen kann mit einem erheblichen Aufwand verbunden sein. Mit DevTest Labs können Testumgebungen leicht erstellt, aktualisiert oder dupliziert werden. Die Teams können auf eine vollständig konfigurierte Umgebung zugreifen, falls sie benötigt wird. In diesem Szenario bietet DevTest Labs die folgenden Vorteile:

- Organisationen können gemeinsame Testumgebungen bereitstellen, die teamübergreifende Konsistenz sicherstellen.
- Tester können schnell Windows- und Linux-Umgebungen mit wiederverwendbaren Vorlagen bereitstellen und so die neueste Version ihrer Anwendung testen.
- Administratoren können das Lab mit Azure DevOps verbinden und dadurch DevOps-Szenarien ermöglichen.
- Labbesitzer können die Kosten steuern, indem sie Folgendes sicherstellen:
    - [VMs in Umgebungen werden heruntergefahren](devtest-lab-set-lab-policy.md#set-auto-shutdown), wenn sie nicht in Gebrauch sind.
    - Für bestimmte Labs wird nur [eine Teilmenge der VM-Instanzgrößen zugelassen](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes).
    - [Verwalten Sie Kostenziele und Benachrichtigungen](devtest-lab-configure-cost-management.md) für jedes Lab.

Weitere Informationen finden Sie unter [Verwenden von Azure DevTest Labs für virtuelle Computer und PaaS-Testumgebungen](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Sandkastenuntersuchungen
Entwickler untersuchen häufig unterschiedliche Technologien oder Infrastrukturentwürfe. Standardmäßig werden alle Umgebungen, die mit DevTest Labs erstellt werden, in ihrer eigenen Ressourcengruppe erstellt. Der DevTest Labs-Benutzer erhält nur Lesezugriff auf diese Ressourcen. Für Entwickler, die mehr Kontrolle benötigen, kann eine für das gesamte Lab geltende Einstellung aktualisiert werden. Hiermit werden dem ursprünglichen DevTest Labs-Benutzer für jede Umgebung, die er erstellt, [Rechte vom Typ „Mitwirkender“](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) erteilt.  Mit DevTest Labs kann Entwicklern die Berechtigung „Mitwirkender“ für Umgebungen, die sie im Lab erstellen, automatisch gewährt werden.  In diesem Szenario können Entwickler Azure-Ressourcen, die sie für ihre Entwicklungs- oder Testumgebungen benötigen, je nach Bedarf hinzufügen bzw. ändern. Auf der Seite [Kosten nach Ressource](devtest-lab-configure-cost-management.md#view-cost-by-resource) können Labbesitzer die Kosten für jede Umgebung nachverfolgen, die für Untersuchungen verwendet wird.

Weitere Informationen finden Sie unter [Set access rights to an environment resource group](https://aka.ms/dtl-sandbox) (Festlegen von Zugriffsrechten für eine Umgebungsressourcengruppe).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Schulungen, praktische Übungen und Hackathons 
Ein Lab in Azure DevTest Labs fungiert als idealer Container für zeitlich begrenzte Aktivitäten wie Workshops, praktische Übungen, Schulungen oder Hackathons.  Mit dem Dienst können Sie in einem erstellten Lab benutzerdefinierte Vorlagen bereitstellen, mit denen jeder Teilnehmer identische und isolierte Umgebungen zu Schulungszwecken erstellen kann. In diesem Szenario bietet DevTest Labs die folgenden Vorteile:

- Mit [Richtlinien](devtest-lab-set-lab-policy.md) wird sichergestellt, dass Benutzer nur die Anzahl von Ressourcen erhalten, die sie auch wirklich benötigen, z. B. virtuelle Computer.
- Für Benutzer ist nur eine Aktion erforderlich, um vorkonfigurierte und erstellte Computer [anzufordern](devtest-lab-add-claimable-vm.md).
- Labs werden für Benutzer freigegeben, indem auf die [URL für das Lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) zugegriffen wird.
- Mit [Ablaufdaten](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) auf virtuellen Computern wird sichergestellt, dass Computer gelöscht werden, wenn sie nicht mehr benötigt werden.
- Das [Löschen eines Labs](devtest-lab-delete-lab-vm.md#delete-a-lab) und aller [zugehörigen Ressourcen](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) nach Abschluss der Schulung ist einfach.

Weitere Informationen finden Sie unter [Verwenden von Azure DevTest Labs zu Schulungszwecken](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Vergleich: Proof of Concept und Skalierte Bereitstellung
Wenn Sie sich für die Erkundung von DevTest Labs entschieden haben, haben Sie zwei Möglichkeiten: „Proof of Concept“ und „Skalierte Bereitstellung“.  

Eine **skalierte Bereitstellung** umfasst Wochen/Monate des Überprüfens und Planens mit dem Zweck, DevTest Labs für das gesamte Unternehmen mit Hunderten oder Tausenden von Entwicklern bereitzustellen.

Bei einer **Proof of Concept**-Bereitstellung geht es um einen konzentrierten Einsatz eines einzelnen Teams zum Erreichen eines Mehrwerts für die Organisation. Obwohl eine skalierte Bereitstellung verlocken kann, versagt dieser Ansatz häufiger als die Proof of Concept-Option. Aus diesem Grund empfehlen wir, klein anzufangen, aus dem ersten Team zu lernen, den gleichen Ansatz mit zwei oder drei zusätzlichen Teams zu wiederholen und dann anhand der erworbenen Kenntnisse eine skalierte Bereitstellung zu planen. Für einen erfolgreichen Proof of Concept empfehlen wir, ein oder zwei Teams auszuwählen, deren Szenarien (Entwicklungsumgebung oder Testumgebung) zu identifizieren, die aktuellen Anwendungsfälle zu dokumentieren und DevTest Labs bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [DevTest Labs-Konzepte](devtest-lab-concepts.md)
- [Häufig gestellte Fragen zu DevTest Labs](devtest-lab-faq.md)

