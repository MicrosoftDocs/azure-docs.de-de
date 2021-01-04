---
title: 'Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“: Übersicht'
description: 'Übersicht und Architektur des Beispiels zur Blaupause „ISO 27001: Gemeinsame Dienste“ Dieses Blaupausenbeispiel unterstützt Kunden bei der Bewertung spezifischer ISO 27001-Kontrollen.'
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: b14e929a2041daa1e6b35653f3ebd9178efefbfe
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420358"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Übersicht über das Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“

Mit dem Beispiel zur Blaupause „ISO 27001: Gemeinsame Dienste“ wird eine Reihe von konformen Infrastrukturmustern und Schutzmaßnahmen für Richtlinien bereitgestellt, die für den ISO 27001-Nachweis hilfreich sind. Mit dieser Blaupause können Kunden cloudbasierte Architekturen bereitstellen, die Lösungen für Szenarien liefern, in denen Anforderungen in Bezug auf die Akkreditierung oder Konformität erfüllt werden müssen.

Das Blaupausenbeispiel [ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload](../iso27001-ase-sql-workload/index.md) stellt eine Erweiterung dieses Beispiels dar.

## <a name="architecture"></a>Aufbau

Mit dem Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“ wird eine grundlegende Infrastruktur in Azure bereitgestellt, die von Organisationen zum Hosten mehrerer Workloads basierend auf dem Ansatz mit einem virtuellen Rechenzentrum (Virtual Datacenter, VDC) verwendet werden kann.
VDC umfasst einen bewährten Satz mit Referenzarchitekturen, Automatisierungstools und Kundenbindungsmodell und wird von Microsoft für seine größten Unternehmenskunden genutzt. Das Blaupausenbeispiel für gemeinsame Dienste basiert auf einer vollständig nativen Azure-VDC-Umgebung, die unten dargestellt ist.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“ – Entwurf" border="false":::

Diese Umgebung besteht aus mehreren Azure-Diensten, die für die Bereitstellung einer sicheren, vollständig überwachten und unternehmensgerechten Infrastruktur mit gemeinsam genutzten Diensten auf Basis von ISO 27001-Standards genutzt werden. Diese Umgebung besteht aus den folgenden Komponenten:

- [Azure-Rollen](../../../../role-based-access-control/overview.md) für die Aufgabentrennung aus Sicht der Steuerungsebene. Vor der Bereitstellung jeder Infrastruktur werden drei Rollen definiert:
  - Die Rolle „NetOps“ verfügt über die Rechte zum Verwalten der Netzwerkumgebung, z. B. Firewalleinstellungen, NSG-Einstellungen, Routing und andere Netzwerkfunktionen.
  - Die Rolle „SecOps“ verfügt über die erforderlichen Rechte zum Bereitstellen und Verwalten von [Azure Security Center](../../../../security-center/security-center-introduction.md) und zum Definieren von [Azure Policy](../../../policy/overview.md)-Definitionen sowie über andere sicherheitsbezogene Rechte.
  - Die Rolle „SysOps“ verfügt über die erforderlichen Rechte zum Definieren von [Azure Policy](../../../policy/overview.md)-Definitionen im Abonnement und zum Verwalten von [Log Analytics](../../../../azure-monitor/overview.md) für die gesamte Umgebung sowie über andere betriebsbezogene Rechte.
- [Log Analytics](../../../../azure-monitor/overview.md) wird als erster Azure-Dienst bereitgestellt, um sicherzustellen, dass alle Aktionen und Dienste an einem zentralen Ort protokolliert werden, sobald Sie Ihre sichere Bereitstellung starten.
- Ein virtuelles Netzwerk, für das Subnetze zur Sicherstellung der Konnektivität mit einem lokalen Rechenzentrum, ein Eingangs- und Ausgangsstapel für Internetkonnektivität und ein Subnetz für gemeinsam genutzte Dienste mit NSGs und ASGs für die vollständige Mikrosegmentierung unterstützt werden und das Folgendes enthält:
  - Eine Jumpbox oder einen Bastionhost für Verwaltungszwecke, wobei der Zugriff nur über eine [Azure Firewall](../../../../firewall/overview.md) möglich ist, die im Eingangsstapelsubnetz bereitgestellt wird
  - Zwei virtuelle Computer mit Ausführung von Azure Active Directory Domain Services (Azure AD DS) und DNS, auf die nur über die Jumpbox zugegriffen werden kann und die so konfiguriert werden können, dass die Replikation nur per AD über eine VPN- oder [ExpressRoute](../../../../expressroute/expressroute-introduction.md)-Verbindung (keine Bereitstellung durch die Blaupause) durchgeführt wird
  - Verwendung von [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) und DDoS-Standardschutz
- Eine [Azure Key Vault](../../../../key-vault/general/overview.md)-Instanz zum Hosten von Geheimnissen für die VMs, die in der Umgebung für gemeinsam genutzte Dienste bereitgestellt werden

Für alle diese Elemente werden die bewährten Methoden befolgt, die unter [Azure Architecture Center: Referenzarchitekturen](/azure/architecture/reference-architectures/) veröffentlicht wurden.

> [!NOTE]
> Mit der Infrastruktur „ISO 27001: Gemeinsame Dienste“ wird eine grundlegende Architektur für Workloads geschaffen.
> Im Hintergrund dieser grundlegenden Architektur müssen Sie weiterhin Workloads bereitstellen.

Weitere Informationen finden Sie in der [Dokumentation zu virtuellen Rechenzentren](/azure/architecture/vdc/).

## <a name="next-steps"></a>Nächste Schritte

Sie haben sich die Übersicht und Architektur des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“ angesehen.
Lesen Sie als Nächstes die folgenden Artikel, um sich über die Steuerungszuordnung und die Bereitstellung dieses Beispiels zu informieren:

> [!div class="nextstepaction"]
> [Blaupause „ISO 27001: Gemeinsame Dienste“ – Steuerungszuordnung](./control-mapping.md)
> [Blaupause „ISO 27001: Gemeinsame Dienste“ – Schritte zum Bereitstellen](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).