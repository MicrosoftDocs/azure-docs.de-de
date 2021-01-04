---
title: Erstellen eines Arbeitsbereichs mit aktiviertem Schutz vor Datenexfiltration
description: In diesem Artikel wird beschrieben, wie Sie einen Arbeitsbereich mit aktiviertem Schutz vor Datenexfiltration in Azure Synapse Analytics erstellen.
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 8f11f104e8d3d0a4f3a004ebe9f149cb4132d910
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501284"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Erstellen eines Arbeitsbereichs mit aktiviertem Schutz vor Datenexfiltration
In diesem Artikel wird beschrieben, wie Sie einen Arbeitsbereich mit aktiviertem Schutz vor Datenexfiltration und die genehmigten Azure AD-Mandanten für diesen Arbeitsbereich verwalten.

>[!Note]
>Nachdem der Arbeitsbereich erstellt wurde, können Sie die Arbeitsbereichskonfiguration für das verwaltete virtuelle Netzwerk und den Schutz vor Datenexfiltration nicht mehr ändern.

## <a name="prerequisites"></a>Voraussetzungen
- Berechtigungen zum Erstellen einer Arbeitsbereichsressource in Azure.
- Synapse-Arbeitsbereichsberechtigungen zum Erstellen von verwalteten privaten Endpunkten.
- Abonnements, die für den Netzwerkressourcenanbieter registriert sind. [Weitere Informationen.](../../azure-resource-manager/management/resource-providers-and-types.md)

Führen Sie die Schritte unter [Schnellstart: Erstellen eines Synapse-Arbeitsbereichs](../quickstart-create-workspace.md) aus, um mit der Erstellung Ihres Arbeitsbereichs zu beginnen. Verwenden Sie vor dem Erstellen des Arbeitsbereichs die unten angegebenen Informationen, um für Ihren Arbeitsbereich den Schutz vor Datenexfiltration hinzuzufügen.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Hinzufügen des Schutzes vor Datenexfiltration beim Erstellen Ihres Arbeitsbereichs
1. Aktivieren Sie auf der Registerkarte „Netzwerk“ das Kontrollkästchen „Verwaltetes VNET aktivieren“.
1. Legen Sie die Option „Ausgehenden Datenverkehr nur zu genehmigten Zielen zulassen“ auf „Ja“ fest.
1. Wählen Sie die genehmigten Azure AD-Mandanten für diesen Arbeitsbereich aus.
1. Überprüfen Sie die Konfiguration, und erstellen Sie den Arbeitsbereich.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Erstellen eines Arbeitsbereichs mit Schutz vor Datenexfiltration":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Verwalten von genehmigten Azure Active Directory-Mandanten für den Arbeitsbereich
1. Navigieren Sie im Azure-Portal des Arbeitsbereichs zu „Genehmigte Azure AD-Mandanten“. Hier ist die Liste mit den genehmigten Azure AD-Mandanten für den Arbeitsbereich angegeben. Der Mandant des Arbeitsbereichs ist standardmäßig vorhanden und wird nicht angezeigt.
1. Verwenden Sie die Option „+ Hinzufügen“, um der Liste mit den genehmigten Mandanten einen neuen Eintrag hinzuzufügen.
1. Wenn Sie einen Azure AD-Mandanten aus der Liste mit den genehmigten Mandanten entfernen möchten, wählen Sie den Mandanten und dann „Löschen“ und anschließend „Speichern“ aus.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Erstellen eines Arbeitsbereichs mit Schutz vor Datenexfiltration":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Herstellen einer Verbindung mit Azure-Ressourcen auf genehmigten Azure AD-Mandanten

Sie können verwaltete private Endpunkte erstellen, um eine Verbindung mit Azure-Ressourcen herzustellen, die sich auf Azure AD-Mandanten befinden und für einen Arbeitsbereich genehmigt wurden. Führen Sie die Schritte im Leitfaden [Erstellen eines verwalteten privaten Endpunkts zu Ihrer Datenquelle (Vorschau)](./how-to-create-managed-private-endpoints.md) aus.

>[!IMPORTANT]
>Für Ressourcen auf anderen Mandanten als dem Mandanten des Arbeitsbereichs dürfen keine blockierenden Firewallregeln vorhanden sein, weil die SQL-Pools ansonsten keine Verbindung damit herstellen können. Ressourcen im verwalteten virtuellen Netzwerk des Arbeitsbereichs, z. B. Spark-Cluster, können über verwaltete private Links eine Verbindung mit per Firewall geschützten Ressourcen herstellen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über den [Schutz vor Datenexfiltration in Synapse-Arbeitsbereichen](./workspace-data-exfiltration-protection.md).

Weitere Informationen zu [verwalteten virtuellen Arbeitsbereichsnetzwerken](./synapse-workspace-managed-vnet.md)

Weitere Informationen zu [Verwalteten privaten Endpunkten](./synapse-workspace-managed-private-endpoints.md).

[Erstellen Sie verwaltete private Endpunkte für Ihre Datenquellen.](./how-to-create-managed-private-endpoints.md)
