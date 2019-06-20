---
title: Governance der Azure DevTest Labs-Infrastruktur
description: Dieser Artikel enthält Anweisungen zur Governance der Azure DevTest Labs-Infrastruktur.
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
ms.openlocfilehash: 75ce5d6a88b5398bd010cc363b4241bc90068f55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60192998"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governance der Azure DevTest Labs-Infrastruktur: Migration und Integration von Anwendungen
Nach der Einrichtung Ihrer Entwicklungs-/Test Lab-Umgebung müssen Sie sich die folgenden Fragen stellen:

- Wie verwenden Sie die Umgebung innerhalb Ihres Projektteams?
- Wie stellen Sie sicher, dass alle erforderlichen Organisationsrichtlinien befolgt werden und zugleich die Agilität erhalten bleibt, um Mehrwert für Ihre Anwendung zu generieren?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-Images im Vergleich mit benutzerdefinierten Images

### <a name="question"></a>Frage
Wann sollte einem Azure Marketplace-Image der Vorzug gegenüber einem eigenen, benutzerdefinierten Image der Organisation gegeben werden?

### <a name="answer"></a>Antwort
Azure Marketplace sollte standardmäßig verwendet werden, sofern keine bestimmten Anliegen oder die Organisation betreffenden Anforderungen bestehen. Dies sind einige häufige Beispiele:

- Eine komplexe Softwareeinrichtung, bei der eine Anwendung als Teil des Basisimages enthalten sein muss.
- Installation und Einrichtung einer Anwendung können viele Stunden dauern, und dieses Hinzufügen zu einem Azure Marketplace-Image stellt keine effiziente Nutzung der Computezeit dar.
- Entwickler und Tester benötigen schnell Zugriff auf einen virtuellen Computer und möchten die erforderliche Zeit für die Einrichtung eines neuen virtuellen Computers minimieren.
- Compliance- oder behördliche Bestimmungen (z.B. Sicherheitsrichtlinien), die für alle Computer umgesetzt werden müssen.

Die Verwendung benutzerdefinierter Images sollte nicht auf die leichte Schulter genommen werden. Sie bringen zusätzliche Komplexität ins Spiel, da Sie sich nun um die Verwaltung der VHD-Dateien für die zugrundeliegenden Basisimages kümmern müssen. Außerdem müssen Sie die Basisimages regelmäßig mit Softwareupdates patchen. Zu diesen Updates gehören neue Betriebssystemupdates und alle Updates oder Konfigurationsänderungen, die am Softwarepaket selbst erforderlich werden.

## <a name="formula-vs-custom-image"></a>Formel- im Vergleich mit benutzerdefinierten Images

### <a name="question"></a>Frage
Wann sollte ein Formel- im Vergleich zu einem benutzerdefinierten Image bevorzugt werden?

### <a name="answer"></a>Antwort
In der Regel sind die entscheidenden Faktoren Kosten und Wiederverwendbarkeit.

Wenn in Ihrem Szenario viele Benutzer/Labs ein Image mit viel Software zusätzlich zum Basisimage erfordern, können Sie durch Erstellen eines benutzerdefinierten Images möglicherweise die Kosten reduzieren. Dies bedeutet, dass das Image einmal erstellt wird. Dadurch verringern sich der Zeitbedarf für die Einrichtung des virtuellen Computers und die Kosten, die bei der Ausführung des virtuellen Computers während des Setups anfallen würden.

Ein weiterer Faktor, der Beachtung verdient, ist jedoch die Häufigkeit von Änderungen an Ihrem Softwarepaket. Wenn Sie täglich Builds ausführen und es erforderlich ist, dass diese Software auf den virtuellen Computern Ihrer Benutzer vorhanden ist, ziehen Sie die Verwendung eines Formel- anstelle eines benutzerdefinierten Images in Betracht.

## <a name="use-custom-organizational-images"></a>Verwenden von benutzerdefinierten Images der Organisation

### <a name="question"></a>Frage
Wie kann ich einen leicht wiederholbaren Prozess einrichten, um meine benutzerdefinierten organisationseigenen Images in einer DevTest Labs-Umgebung einzusetzen?

### <a name="answer"></a>Antwort
Sehen Sie sich dazu [dieses Video zum Image Factory-Muster](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) an. Dabei handelt es sich um ein erweitertes Szenario, und die bereitgestellten Skripts stellen lediglich Beispielskripts dar. Wenn irgendwelche Änderungen erforderlich sind, müssen Sie die in Ihrer Umgebung verwendeten Skripts verwalten und warten.

Hier finden Sie weitere Informationen, um mit DevTest Labs eine benutzerdefinierte Imagepipeline in Azure Pipelines zu erstellen:

- [Einführung: Betriebsfertig machen von VMs innerhalb von Minuten durch Einrichten einer Image Factory in Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Image Factory – Teil 2: Einrichten von Azure Pipelines und Factory Lab zum Erstellen von VMs](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Image Factory – Teil 3: Speichern von benutzerdefinierten Images und Verteilen an mehrere Labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Video: Factory für benutzerdefinierte Images mit Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Muster zum Einrichten der Netzwerkkonfiguration

### <a name="question"></a>Frage
Wie kann ich sicherstellen, dass VMs für Entwicklung und Test das öffentliche Internet nicht erreichen können? Gibt es empfohlene Muster zum Einrichten der Netzwerkkonfiguration?

### <a name="answer"></a>Antwort
Ja. Zwei Aspekte müssen berücksichtigt werden: eingehender und ausgehender Datenverkehr.

**Eingehender Datenverkehr**: Wenn der virtuelle Computer nicht über eine öffentliche IP-Adresse verfügt, kann er vom Internet aus nicht erreicht werden. Ein gängiger Ansatz besteht darin, eine Richtlinie auf Abonnementebene festzulegen, die sicherstellt, dass kein Benutzer eine öffentliche IP-Adresse erstellen kann.

**Ausgehender Datenverkehr**: Wenn Sie verhindern möchten, dass virtuelle Computer direkte Verbindungen mit dem öffentlichen Internet herstellen und Datenverkehr durch eine Unternehmensfirewall senden können, können Sie den Datenverkehr lokal mithilfe von ExpressRoute oder VPN routen, indem Sie erzwungenes Routing einsetzen.

> [!NOTE]
> Wenn Sie über einen Proxyserver verfügen, der ohne Proxyeinstellungen Datenverkehr blockiert, vergessen Sie nicht, Ausnahmen für das Speicherkonto des Labs für Artefakte hinzuzufügen.

Sie können außerdem Netzwerksicherheitsgruppen für virtuelle Computer oder Subnetze verwenden. Dieser Schritt fügt eine zusätzliche Schutzschicht zum Zulassen/Blockieren von Datenverkehr hinzu.

## <a name="new-vs-existing-virtual-network"></a>Neues im Vergleich mit vorhandenem virtuellem Netzwerk

### <a name="question"></a>Frage
Wann sollte ich ein neues virtuelles Netzwerk für meine DevTest Labs-Umgebung erstellen und wann ein vorhandenes virtuelles Netzwerk nutzen?

### <a name="answer"></a>Antwort
Wenn Ihre VMs mit vorhandener Infrastruktur interagieren müssen, sollten Sie die Verwendung eines vorhandenen virtuellen Netzwerks innerhalb Ihrer DevTest Labs-Umgebung in Erwägung ziehen. Wenn Sie ExpressRoute verwenden, kann es darüber hinaus sinnvoll sein, die Menge der VNets/Subnetze zu minimieren, um Fragmentierung Ihres IP-Adressraums, der für die Verwendung in den Abonnements zugewiesen wird, zu vermeiden. Sie sollten hier ferner die Verwendung des VNet-Peeringmusters (Nabe-Speiche-Modell) in Erwägung ziehen. Dieser Ansatz ermöglicht die abonnementübergreifende Kommunikation in VNets/Subnetzen innerhalb einer bestimmten Region. Das regionsübergreifende Peering ist als zukünftiges Feature in Azure-Netzwerken vorgesehen.

Andernfalls kann jede DevTest Labs-Umgebung über ein eigenen virtuelles Netzwerk verfügen. Beachten Sie aber, dass die Anzahl der virtuellen Netzwerke pro Abonnement [begrenzt](../azure-subscription-service-limits.md) ist. Standardmäßig sind 50 möglich, dieser Grenzwert kann jedoch auf 100 heraufgesetzt werden.

## <a name="shared-public-or-private-ip"></a>Freigegebene, öffentliche oder private IP

### <a name="question"></a>Frage
Wann sollte eine gemeinsame IP-Adresse, wann eine öffentliche, wann eine private verwendet werden?

### <a name="answer"></a>Antwort
Wenn Sie ein Standort-zu-Standort-VPN oder Express Route verwenden, ziehen Sie die Verwendung privater IPs in Erwägung, so dass auf Ihre Computer über Ihr internes Netzwerk zugegriffen werden kann, nicht aber aus dem öffentlichen Internet.

> [!NOTE]
> Labbesitzer können diese Subnetzrichtlinie ändern, um sicherzustellen, dass niemand versehentlich öffentliche IP-Adressen für seine VMs erstellt. Der Abonnementbesitzer sollte eine Abonnementrichtlinie einrichten, die das Erstellen öffentlicher IPs verhindert.

Bei der Verwendung gemeinsamer öffentlicher IPs teilen die virtuellen Computer in einem Lab eine öffentliche IP-Adresse. Dieser Ansatz kann nützlich sein, wenn Sie das Überschreiten des Grenzwerts für öffentliche IP-Adressen für ein bestimmtes Abonnement vermeiden müssen.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Grenzen der Anzahl virtueller Computer pro Benutzer oder Lab

### <a name="question"></a>Frage
Gibt es eine Regel hinsichtlich der Anzahl virtueller Computer, die pro Benutzer oder Lab eingerichtet werden sollten?

### <a name="answer"></a>Antwort
Beim Betrachten der Anzahl virtueller Computer pro Benutzer oder Lab gibt es drei Hauptanliegen:

- Die **Gesamtkosten**, die das Team für Ressourcen im Lab aufwenden kann. Es ist natürlich kein Problem, viele Computer laufen zu lassen. Ein Mechanismus zur Kostenkontrolle besteht darin, die Anzahl der VMs pro Benutzer und/oder Lab zu beschränken.
- Die Gesamtzahl der virtuellen Computer in einem Lab wird durch die verfügbaren [Kontingente auf Abonnementebene](../azure-subscription-service-limits.md) beeinflusst. Eine der Obergrenzen liegt bei 800 Ressourcengruppen pro Abonnement. DevTest Labs erstellt derzeit eine neue Ressourcengruppe für jeden virtuellen Computer (sofern keine gemeinsamen öffentlichen IP-Adressen verwendet werden). Wenn Sie 10 Labs in einem Abonnement betreiben, könnten pro Lab ungefähr 79 virtuelle Computer eingesetzt werden (Obergrenze von 800 – 10 Ressourcengruppen für die 10 Labs selbst) = 79 virtuelle Computer pro Lab.
- Wenn das Lab mithilfe von ExpressRoute (beispielsweise) mit dem lokalen Netzwerk verbunden ist, sind für das VNet/Subnetz **definierte IP-Adressräume verfügbar**. Um sicherzustellen, dass keine Fehler beim Erstellen von VMs im Lab auftreten (Fehler: IP-Adresse kann nicht abgerufen werden), können sich Lab-Besitzer beim Festlegen der maximalen Anzahl von VMs pro Lab am zur Verfügung stehenden IP-Adressraum orientieren.

## <a name="use-resource-manager-templates"></a>Verwenden von Resource Manager-Vorlagen

### <a name="question"></a>Frage
Wie können Resource Manager-Vorlagen in der DevTest Labs-Umgebung verwendet werden?

### <a name="answer"></a>Antwort
Sie stellen Ihre Resource Manager-Vorlagen mithilfe der im Artikel [Environments feature in DevTest labs](devtest-lab-test-env.md) (Umgebungsfeature in DevTest Labs) genannten Schritte in einer DevTest Labs-Umgebung bereit. Im Wesentlichen checken Sie Ihre Resource Manager-Vorlagen in ein Git-Repository ein (Azure Repos oder GitHub) und fügen dem Lab ein [privates Repository für Ihre Vorlagen](devtest-lab-test-env.md) hinzu.

Dieses Szenario ist möglicherweise nicht nützlich, wenn Sie DevTest Labs zum Hosten von Entwicklungscomputern verwenden, es kann aber beim Erstellen einer Stagingumgebung nützlich sein, die eine Produktionsumgebung darstellen soll.

Außerdem ist erwähnenswert, dass die Option für die Anzahl der virtuellen Computer pro Lab oder pro Benutzer nur die Anzahl der nativ im Lab selbst erstellten Computer einschränkt, Umgebungsparameter (Resource Manager-Vorlagen) aber nicht betrifft.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Use environments in DevTest Labs](devtest-lab-test-env.md) (Verwenden von Umgebungen in DevTest Labs).