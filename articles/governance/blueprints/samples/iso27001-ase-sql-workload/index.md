---
title: 'Blaupausenbeispiel „ISO 27001: ASE-/SQL-Workload“: Übersicht'
description: 'Übersicht und Architektur des Beispiels für die Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“.'
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: 4972aa09e993f8de445cf4bf581f5ad76dbca520
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420375"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Übersicht über das Blaupausenbeispiel „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“

Das Blaupausenbeispiel „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ enthält zusätzliche Infrastruktur zum Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md).
Mit dieser Blaupause können Kunden cloudbasierte Architekturen bereitstellen, die Lösungen für Szenarien liefern, in denen Anforderungen in Bezug auf die Akkreditierung oder Konformität erfüllt werden müssen.

Es gibt zwei ISO 27001-Blaupausenbeispiele: dieses Beispiel und das Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md).

> [!IMPORTANT]
> Dieses Beispiel basiert auf Infrastruktur, die im Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md) bereitgestellt wurde. Dieses Beispiel muss zuerst bereitgestellt werden.

## <a name="architecture"></a>Aufbau

Im Blaupausenbeispiel „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ wird eine Webumgebung auf PaaS-Basis (Platform-as-a-Service) bereitgestellt. Die Umgebung kann zum Hosten von mehreren Webanwendungen, Web-APIs und SQL-Datenbankinstanzen verwendet werden, für die die ISO 27001-Standards eingehalten werden. Dieses Blaupausenbeispiel basiert auf dem Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md).

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="Blaupausenbeispiel „ISO 27001: ASE-/SQL-Workload“ – Entwurf" border="false":::

Diese Umgebung besteht aus mehreren Azure-Diensten, die für die Bereitstellung einer sicheren, vollständig überwachten und unternehmensgerechten Workloadinfrastruktur auf Grundlage von ISO 27001-Standards genutzt werden. Diese Umgebung besteht aus den folgenden Komponenten:

- [Azure-Rolle](../../../../role-based-access-control/overview.md) mit dem Namen „DevOps“, die über Rechte zum Bereitstellen und Verwalten von Ressourcen in [Azure App Service-Umgebungen](../../../../app-service/environment/intro.md) aus dem Blaupausenbeispiel verfügt
- [Azure Policy](../../../policy/overview.md)-Definitionen zum Beschränken der Dienste, die für die Umgebung bereitgestellt werden können, und mit Ablehnung der Erstellung von öffentlichen IP-Adressressourcen (PIP)
- Ein virtuelles Netzwerk, in dem nur ein Subnetz enthalten ist und das über ein Peering mit einer bereits vorhandenen Umgebung für [gemeinsam genutzte Dienste](../iso27001-shared/index.md) verfügt, sodass für den gesamten Datenverkehr der Weg durch die Firewall der [gemeinsam genutzten Dienste](../iso27001-shared/index.md) erzwungen wird Im virtuellen Netzwerk werden die folgenden Ressourcen gehostet:
  - Eine [Azure App Service-Umgebung](../../../../app-service/environment/intro.md), die zum Hosten von einer oder mehreren Webanwendungen, Web-APIs oder Funktionen genutzt wird
  - Eine [Azure Key Vault](../../../../key-vault/general/overview.md)-Instanz mit Nutzung eines VNET-Dienstendpunkts zum Speichern von Geheimnissen, die von in der Workloadumgebung ausgeführten Anwendungen verwendet werden
  - Eine [Azure SQL-Datenbank](../../../../azure-sql/database/sql-database-paas-overview.md)-Serverinstanz mit einem VNET-Dienstendpunkt zum Hosten von Datenbanken, die für Anwendungen in der Workloadumgebung verwendet werden

## <a name="next-steps"></a>Nächste Schritte

Sie haben sich die Übersicht und Architektur des Beispiels für die Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ angesehen. Lesen Sie als Nächstes die folgenden Artikel, um sich über die Steuerungszuordnung und die Bereitstellung dieses Beispiels zu informieren:

> [!div class="nextstepaction"]
> [Beispiel: Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ – Steuerungszuordnung](./control-mapping.md)
> [Beispiel: Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ – Schritte zum Bereitstellen](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
