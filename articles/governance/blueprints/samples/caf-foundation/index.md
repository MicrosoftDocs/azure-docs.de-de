---
title: 'CAF-Basisblaupausenbeispiel: Übersicht'
description: Übersicht und Architektur des Framework für die Cloudeinführung (Cloud Adoption Framework, CAF) für das Basisblaupausenbeispiel für Azure
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: b605c1af5fcb2a37663d71fecceea169692a5ab9
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2021
ms.locfileid: "108757833"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Übersicht über das Microsoft Cloud Adoption Framework-Basisblaupausenbeispiel für Azure

Die Microsoft CAF-Basisblaupause (Cloud Adoption Framework) für Azure stellt eine Reihe zentraler Infrastrukturressourcen und Richtlinienkontrollen bereit, die für Ihre erste produktionsbereite Azure-Anwendung erforderlich sind. Diese Basisblaupause basiert auf dem empfohlenen CAF-Muster.

## <a name="architecture"></a>Aufbau

Das CAF-Basisblaupausenbeispiel stellt empfohlene Infrastrukturressourcen in Azure bereit, mit denen Organisationen die grundlegenden Kontrollen einrichten können, die sie zur Verwaltung ihrer Cloudressourcen benötigen. In diesem Beispiel werden Ressourcen, Richtlinien und Vorlagen bereitgestellt und erzwungen, die Organisationen einen problemlosen Einstieg in Azure ermöglichen.

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="CAF-Basis: Die Abbildung veranschaulicht, was im Rahmen des CAF-Leitfadens für die Erstellung einer Basis für den Einstieg in Azure installiert wird." border="false":::
   Beschreibt eine Azure-Architektur, die durch Bereitstellen der CAF-Basisblaupause erreicht wird. Dies gilt für ein Abonnement mit Ressourcengruppen, das aus einem Speicherkonto zum Speichern von Protokollen und einer für die Speicherung im Speicherkonto konfigurierten Log Analytics-Instanz besteht. Außerdem wird eine Azure Key Vault-Instanz dargestellt, die mit dem Azure Security Center-Standardsetup konfiguriert ist. Der Zugriff auf alle diese Kerninfrastrukturen erfolgt mithilfe von Azure Active Directory und wird mithilfe von Azure Policy erzwungen.
:::image-end:::

Diese Implementierung umfasst mehrere Azure-Dienste, die für die Bereitstellung einer sicheren, vollständig überwachten und unternehmensgerechten Basis genutzt werden. Diese Umgebung besteht aus den folgenden Komponenten:

- Eine [Azure Key Vault](../../../../key-vault/general/overview.md)-Instanz zum Hosten von Geheimnissen für die VMs, die in der Umgebung für gemeinsam genutzte Dienste bereitgestellt werden
- [Log Analytics](../../../../azure-monitor/overview.md) wird bereitgestellt, um sicherzustellen, dass alle Aktionen und Dienste an einem zentralen Ort protokolliert werden, sobald Sie Ihre sichere Bereitstellung in [Speicherkonten](../../../../storage/common/storage-introduction.md) für die Diagnoseprotokollierung starten.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (Standardversion) schützt Ihre migrierten Workloads vor Bedrohungen.
- Von der Blaupause werden außerdem [Azure Policy](../../../policy/overview.md)-Definitionen definiert und bereitgestellt:
  - Richtliniendefinitionen:
    - Auf Ressourcengruppen angewendetes Tagging (CostCenter)
    - Anfügung des CostCenter-Tags an Ressourcen in der Ressourcengruppe
    - Zulässige Azure-Region für Ressourcen und Ressourcengruppen
    - Zulässige SKUs für Speicherkonten (werden bei der Bereitstellung ausgewählt)
    - Zulässige SKUs für virtuelle Azure-Computer (werden bei der Bereitstellung ausgewählt)
    - Erzwingung der Bereitstellung von Network Watcher
    - Erzwingung der sicheren Übertragungsverschlüsselung für das Azure Storage-Konto
    - Ablehnung von Ressourcentypen (werden bei der Bereitstellung ausgewählt)
  - Richtlinieninitiativen:
    - Aktivieren der Überwachung in Azure Security Center (über 100 Richtliniendefinitionen)

Für alle diese Elemente werden die bewährten Methoden befolgt, die unter [Azure Architecture Center: Referenzarchitekturen](/azure/architecture/reference-architectures/) veröffentlicht wurden.

> [!NOTE]
> Die CAF-Basis richtet eine grundlegende Architektur für Workloads ein.
> Im Hintergrund dieser grundlegenden Architektur müssen Sie weiterhin Workloads bereitstellen.

Weitere Informationen finden Sie unter [Governance, Sicherheit und Konformität in Azure](/azure/cloud-adoption-framework/ready/).

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun mit der Übersicht und Architektur des CAF-Basisblaupausenbeispiels vertraut.

> [!div class="nextstepaction"]
> [Deploy the Microsoft Cloud Adoption Framework for Azure Foundation blueprint sample](./deploy.md) (Bereitstellen des Microsoft Cloud Adoption Framework-Basisblaupausenbeispiels für Azure)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
