---
title: Hyperledger Fabric-Konsortiumsnetzwerk in Azure
description: Projektmappenvorlage zum Bereitstellen und Konfigurieren eines Hyperledger Fabric-Konsortiumsnetzwerks
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: 80de4e1479fac7296889e45289a5f20e586e3f57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510748"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric-Konsortiumsnetzwerk

Sie können die Hyperledger Fabric-Konsortiums-Projektmappenvorlage zum Bereitstellen und Konfigurieren eines Hyperledger Fabric-Konsortiumsnetzwerks in Azure verwenden.

In diesem Artikel lernen Sie Folgendes:

- Kenntnisse im Umgang mit Blockchain, Hyperledger Fabric und komplizierteren Konsortiumsnetzwerk-Architekturen
- Erfahren Sie, wie Sie ein Hyperledger Fabric-Konsortiumsnetzwerk vom Azure-Portal aus bereitstellen und konfigurieren.

## <a name="about-blockchain"></a>Über Blockchain

Neuen Mitgliedern der Blockchain-Community bietet diese Lösungsvorlage eine ausgezeichnete Möglichkeit, die Technologie auf einfache und konfigurierbare Weise in Azure kennenzulernen. Blockchain ist die Bitcoin zugrunde liegende Technologie. Sie ist allerdings viel mehr als nur ein Faktor für virtuelle Währungen. Es handelt sich um eine Kombination aus vorhandenen Technologien für Datenbanken, verteilte Systeme und Kryptografie, die eine sichere Berechnung durch mehrere Parteien und Garantien im Hinblick auf Unveränderlichkeit, Überprüfbarkeit, Nachverfolgbarkeit und Resilienz gegenüber Angriffen ermöglicht. Unterschiedliche Protokolle verwenden unterschiedliche Mechanismen, um diese Attribute bereitzustellen. [Hyperledger Fabric](https://github.com/hyperledger/fabric) ist ein solches Protokoll.

## <a name="consortium-architecture-on-azure"></a>Konsortiumsarchitektur bei Azure

In Azure werden zwei primäre Bereitstellungstypen zum Aktivieren von Hyperledger Fabric unterstützt. Diese Bereitstellungen sollen basierend auf dem gewünschten Ziel verschiedene Topologien berücksichtigen.

- **Einzelner virtueller Computer, Entwicklerserver**: Diese Art der Bereitstellung ist als eine Entwicklungsumgebung zum Erstellen und Testen von Lösungen konzipiert, die mit Hyperledger Fabric entwickelt wurden.
- **Mehrere virtuelle Computer, Erweiterungsbereitstellung**: Diese Art der Bereitstellung ist für Umgebungen konzipiert, die ein Konsortium verschiedener Teilnehmer modellieren, die eine gemeinsame Umgebung nutzen.

In beiden Bereitstellungen sind die Bausteine identisch, die den Kern von Hyperledger Fabric bilden.  Die Unterschiede in den Bereitstellungen hängen davon ab, wie diese Komponenten erweitert werden.

- **ZS-Knoten**: Ein Knoten, auf dem die Zertifizierungsstelle ausgeführt wird, die verwendet wird, um Zertifikate zu generieren, die für die Identitäten im Netzwerk verwendet werden.
- **Auftraggeberknoten**: Knoten, auf dem der Kommunikationsdienst zum Implementieren einer Zustellungsgarantie, z.B. als Gesamtauftragsübertragung oder als Einzeltransaktionen, ausgeführt wird.
- **Peerknoten**: Knoten, der Transaktionen committet und den Status sowie eine Kopie des Distributed Ledger verwaltet.
- **CouchDB-Knoten**: Ein Knoten, der den CouchDB-Dienst ausführen kann, der die Statusdatenbank speichern und umfangreiche Abfragen von Chaincodedaten bieten kann, von einfachem Schlüssel/Wert bis zu Speicher des JSON-Typs.

### <a name="single-virtual-machine-architecture"></a>Architektur des einzelnen virtuellen Computers

Wie bereits erwähnt, ist die Architektur des einzelnen virtuellen Computers für Entwickler bestimmt, damit ihnen ein Server mit geringem Ressourcenbedarf zum Entwickeln von Anwendungen zur Verfügung steht. Alle gezeigten Container werden in einem einzelnen virtuellen Computer ausgeführt. Der Sortierdienst verwendet [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) für diese Konfiguration. Diese Konfiguration ist *kein* fehlertoleranter Sortierdienst, sondern für Entwicklungszwecke einfach konzipiert.

![Architektur des einzelnen virtuellen Computers](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Architektur mehrerer virtueller Computer

Die erweiterte Architektur mehrerer virtueller Computer ist mit hoher Verfügbarkeit und Skalierung jeder Komponente im Kern konzipiert. Diese Architektur eignet sich viel besser für Bereitstellungen auf Produktionsniveau.

![Architektur mehrerer virtueller Computer](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Erste Schritte

Damit Sie beginnen können, benötigen Sie ein Azure-Abonnement, das die Bereitstellung mehrerer virtueller Computer und Standardspeicherkonten unterstützen kann. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

Wenn Sie über ein Abonnement verfügen, wechseln Sie zum [Azure-Portal](https://portal.azure.com). Wählen Sie **Ressource erstellen > Blockchain > Hyperledger Fabric Consortium** aus.

![Vorlage für Hyperledger Fabric-Blockchain für Einzelmember](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Bereitstellung

Wählen Sie in der Vorlage **Hyperledger Fabric Consortium** **Erstellen** aus.

Während der Vorlagenbereitstellung werden Sie durch die Konfiguration des [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/)-Netzwerks mit mehreren Knoten geführt. Der Ablauf der Bereitstellung ist in vier Schritte unterteilt: Grundlagen, Konsortiumnetzwerkeinstellungen, Fabrickonfiguration und optionale Komponenten.

### <a name="basics"></a>Grundlagen

Geben Sie in **Grundlagen** Werte für die Standardparameter für jede Bereitstellung an. Beispiele: Abonnement, Ressourcengruppe, grundlegende VM-Eigenschaften.

![Grundlagen](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Parametername | BESCHREIBUNG | Zulässige Werte |
|---|---|---|
**Ressourcenpräfix** | Namenspräfix für Ressourcen, die als Teil der Bereitstellung verfügbar sind |Max. 6 Zeichen |
**Benutzername** | Der Benutzername des Administrators für jeden bereitgestellten virtuellen Computer dieses Members. |1–64 Zeichen |
**Authentifizierungstyp** | Die Methode zur Authentifizierung des virtuellen Computers. |Kennwort oder öffentlicher SSH-Schlüssel|
**Kennwort (Authentifizierungstyp = Kennwort)** |Das Kennwort für das Administratorkonto jedes bereitgestellten, virtuellen Computers. Das Kennwort muss Zeichen aus drei der folgenden Zeichentypen enthalten: 1 Großbuchstaben, 1 Kleinbuchstaben, 1 Ziffer und 1 Sonderzeichen.<br /><br />Alle VMs haben zunächst dasselbe Kennwort, das nach der Bereitstellung jedoch geändert werden kann.|12–72 Zeichen|
**SSH-Schlüssel (Authentifizierungstyp = öffentlicher SSH-Schlüssel)** |Der Secure Shell-Schlüssel für die Remoteanmeldung. ||
**Abonnement** |Das Abonnement für die Bereitstellung ||
**Ressourcengruppe** |Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird ||
**Location** |Die Azure-Region, in der der erste Member bereitgestellt wird ||

Klicken Sie auf **OK**.

### <a name="consortium-network-settings"></a>Konsortium-Netzwerkeinstellungen

Nehmen Sie in **Netzwerkeinstellungen** die Eingaben für das Erstellen eines Konsortiumsnetzwerks oder Beitreten zu einem vorhandenen vor, und konfigurieren Sie die Einstellungen Ihrer Organisation.

![Konsortium-Netzwerkeinstellungen](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Parametername | BESCHREIBUNG | Zulässige Werte |
|---|---|---|
**Network Configuration** |Sie können ein neues Netzwerk erstellen oder einem vorhandenen beitreten. Wenn Sie *Vorhandenem beitreten* auswählen, müssen Sie zusätzliche Werte angeben. |Neues Netzwerk <br/> Vorhandenem beitreten |
**HLF CA-Kennwort** |Ein Kennwort, das für die Zertifikate verwendet wird, die von den Zertifizierungsstellen generiert werden, die als Teil der Bereitstellung erstellt werden. Das Kennwort muss Zeichen aus drei der folgenden Zeichentypen enthalten: 1 Großbuchstaben, 1 Kleinbuchstaben, 1 Ziffer und 1 Sonderzeichen.<br /><br />Alle VMs haben zunächst dasselbe Kennwort, das nach der Bereitstellung jedoch geändert werden kann.|1–25 Zeichen |
**Organisationssetup** |Sie können Namen und Zertifikat Ihres Unternehmens anpassen oder Standardwerte verwenden.|Standard <br/> Erweitert |
**VPN-Netzwerkeinstellungen** | Bereitstellen eines VPN-Tunnelgateways für den Zugriff auf die virtuellen Computer | Ja <br/> Nein |

Klicken Sie auf **OK**.

### <a name="fabric-specific-settings"></a>Fabricspezifische Einstellungen

In **Fabrickonfiguration** können Sie Netzwerkgröße und Leistung konfigurieren sowie Eingaben für die Verfügbarkeit des Netzwerks vornehmen. Etwa Anzahl der Auftraggeber- und Peerknoten, von jedem Knoten verwendete Persistenzengine und die Größe des virtuellen Computers.

![Fabriceinstellungen](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Parametername | BESCHREIBUNG | Zulässige Werte |
|---|---|---|
**Skalierungstyp** |Der Bereitstellungstyp entweder eines einzelnen virtuellen Computers mit mehreren Containern oder mehrerer virtueller Computer in einem Erweiterungsmodell.|Einzelner virtueller Computer oder mehrere VMs |
**VM-Datenträgertyp** |Der Typ des Speichers für die einzelnen bereitgestellten Knoten. <br/> Weitere Informationen zu den verfügbaren Datenträgertypen finden Sie unter [Auswählen eines Datenträgertyps](../../virtual-machines/windows/disks-types.md).|SSD Standard <br/> SSD Premium |

### <a name="multiple-vm-deployment-additional-settings"></a>Bereitstellung mehrerer VMs (zusätzliche Einstellungen)

![Fabriceinstellungen für Bereitstellungen mehrerer VMs](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Parametername | BESCHREIBUNG | Zulässige Werte |
|---|---|---|
**Anzahl der Auftraggeberknoten** |Die Anzahl der Knoten, die Transaktionen in einen Block sortieren (organisieren). <br />Weitere Informationen zum Sortierdienst finden Sie in der [Dokumentation](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) zu Hyperledger. |1–4 |
**VM-Größe von Auftraggeberknoten** |Die Größe des virtuellen Computers, der für Auftraggeberknoten im Netzwerk verwendet wird|Standard Bs,<br />Standard DS,<br />Standard FS |
**Anzahl der Peerknoten** | Knoten, deren Besitzer das Konsortium ist und die Transaktionen ausführen und den Status einer Ledgerkopie verwalten.<br />Weitere Informationen zum Sortierdienst finden Sie in der [Dokumentation](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html) zu Hyperledger.|1–4 |
**Knotenstatuspersistenz** |Die Persistenzengine, die von den Peerknoten verwendet wird. Sie können diese Engine für jeden Peerknoten konfigurieren. Nachstehend finden Sie Details für mehrere Peerknoten.|CouchDB <br />LevelDB |
**VM-Größe von Peerknoten** |Die Größe des virtuellen Computers, die für alle Knoten im Netzwerk verwendet wird|Standard Bs,<br />Standard DS,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Konfiguration mit mehreren Peerknoten

Mit dieser Vorlage können Sie Ihre Persistenzengine pro Peerknoten auswählen. Wenn Sie z.B. über drei Peerknoten verfügen, können Sie CouchDB bei einem und LevelDB bei den anderen beiden verwenden.

![Konfiguration mit mehreren Peerknoten](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Klicken Sie auf **OK**.

### <a name="deploy"></a>Bereitstellen

Überprüfen Sie unter **Zusammenfassung** die Eingaben, und führen Sie eine Standardüberprüfung vor der Bereitstellung durch.

![Zusammenfassung](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Lesen Sie die rechtlichen Hinweise und Datenschutzrichtlinien, und wählen Sie **Kauf** aus, um die Bereitstellung abzuschließen. Abhängig von der Anzahl an bereitgestellten virtuellen Computern dauert die Bereitstellung zwischen wenigen Minuten und längerer Zeit.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie sich auf die Entwicklung von Anwendungen und Chain Codes für Ihr Blockchain-Konsortiumsnetzwerk mit Hyperledger konzentrieren.
