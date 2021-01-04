---
title: Schutz vor Datenexfiltration für Azure Synapse Analytics-Arbeitsbereiche
description: Dieser Artikel enthält eine Beschreibung des Schutzes vor Datenexfiltration in Azure Synapse Analytics.
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 71210cdcc2b3758a59a1b41816e6468556e94808
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518259"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Schutz vor Datenexfiltration für Azure Synapse Analytics-Arbeitsbereiche
Dieser Artikel enthält eine Beschreibung des Schutzes vor Datenexfiltration in Azure Synapse Analytics.

## <a name="securing-data-egress-from-synapse-workspaces"></a>Schützen des ausgehenden Datenverkehrs von Synapse-Arbeitsbereichen
Azure Synapse Analytics-Arbeitsbereiche unterstützen die Aktivierung des Schutzes vor Datenexfiltration für Arbeitsbereiche. Mit dem Feature für den Schutz vor Exfiltration können Sie sich vor böswilligen Insidern schützen, die auf Ihre Azure-Ressourcen zugreifen und vertrauliche Daten an Orte exfiltrieren, die sich außerhalb Ihres Organisationsbereichs befinden. Bei der Erstellung des Arbeitsbereichs können Sie auswählen, dass für den Arbeitsbereich ein verwaltetes virtuelles Netzwerk und zusätzlicher Schutz vor Datenexfiltration konfiguriert werden soll. Wenn ein Arbeitsbereich mit einem [verwalteten virtuellen Netzwerk](./synapse-workspace-managed-vnet.md) erstellt wird, werden die Ressourcen für die Datenintegration und für Spark in dem verwalteten virtuellen Netzwerk bereitgestellt. Die dedizierten SQL-Pools des Arbeitsbereichs und die serverlosen SQL-Pools verfügen über Funktionen für mehrere Mandanten und müssen sich daher außerhalb des verwalteten virtuellen Netzwerks befinden. Für Arbeitsbereiche mit Schutz vor Datenexfiltration kommunizieren Ressourcen innerhalb des verwalteten virtuellen Netzwerks immer über [verwaltete private Endpunkte](./synapse-workspace-managed-private-endpoints.md), und die Synapse SQL-Ressourcen können nur eine Verbindung mit autorisierten Azure-Ressourcen herstellen (Ziele von genehmigten Verbindungen des verwalteten privaten Endpunkts für den Arbeitsbereich). 

>[!Note]
>Nachdem der Arbeitsbereich erstellt wurde, können Sie die Arbeitsbereichskonfiguration für das verwaltete virtuelle Netzwerk und den Schutz vor Datenexfiltration nicht mehr ändern.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Verwalten des ausgehenden Datenverkehrs des Synapse-Arbeitsbereichs für genehmigte Ziele
Nachdem der Arbeitsbereich mit aktivierter Datenexfiltration erstellt wurde, können die Besitzer der Arbeitsbereichsressource die Liste mit den genehmigten Azure AD-Mandanten für den Arbeitsbereich verwalten. Benutzer mit den [richtigen Berechtigungen](./synapse-workspace-access-control-overview.md) für den Arbeitsbereich können Synapse Studio verwenden, um Anforderungen für Verbindungen von verwalteten privaten Endpunkten für Ressourcen zu erstellen, die sich auf den genehmigten Azure AD-Mandanten des Arbeitsbereichs befinden. Die Erstellung von verwalteten privaten Endpunkten wird blockiert, wenn der Benutzer versucht, eine Verbindung von einem privaten Endpunkt mit einer Ressource auf einem nicht genehmigten Mandanten herzustellen.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Beispielarbeitsbereich mit aktiviertem Schutz vor Datenexfiltration
Wir verwenden ein Beispiel, um den Schutz vor Datenexfiltration für Synapse-Arbeitsbereiche zu veranschaulichen. Contoso verfügt über Azure-Ressourcen auf Mandant A und Mandant B, und für diese Ressourcen müssen sichere Verbindungen hergestellt werden können. Ein Synapse-Arbeitsbereich wurde auf Mandant A erstellt, und Mandant B wurde als genehmigter Azure AD-Mandant hinzugefügt. Im Diagramm sind Verbindungen für private Endpunkte mit Azure Storage-Konten auf den Mandanten A und B dargestellt, die von den Speicherkontobesitzern genehmigt wurden. Darüber hinaus ist im Diagramm auch die Blockierung der Erstellung eines privaten Endpunkts dargestellt. Die Erstellung dieses privaten Endpunkts wurde blockiert, weil es um ein Azure Storage-Konto auf dem Fabrikam-Azure AD-Mandanten geht, bei dem es sich nicht um einen genehmigten Azure AD-Mandanten für den Arbeitsbereich von Contoso handelt. 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Diagramm: Implementierung des Schutzes vor Datenexfiltration für Synapse-Arbeitsbereiche":::

>[!IMPORTANT]
>Für Ressourcen auf anderen Mandanten als dem Mandanten des Arbeitsbereichs dürfen keine blockierenden Firewallregeln vorhanden sein, weil die SQL-Pools ansonsten keine Verbindung damit herstellen können. Ressourcen im verwalteten virtuellen Netzwerk des Arbeitsbereichs, z. B. Spark-Cluster, können über verwaltete private Links eine Verbindung mit per Firewall geschützten Ressourcen herstellen.
## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Erstellen eines Arbeitsbereichs mit aktiviertem Schutz vor Datenexfiltration](./how-to-create-a-workspace-with-data-exfiltration-protection.md).

Weitere Informationen zu [verwalteten virtuellen Arbeitsbereichsnetzwerken](./synapse-workspace-managed-vnet.md)

Weitere Informationen zu [Verwalteten privaten Endpunkten](./synapse-workspace-managed-private-endpoints.md).

[Erstellen Sie verwaltete private Endpunkte für Ihre Datenquellen.](./how-to-create-managed-private-endpoints.md)
