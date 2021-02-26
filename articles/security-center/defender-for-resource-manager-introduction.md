---
title: 'Azure Defender für Resource Manager: Vorteile und Features'
description: Hier finden Sie Informationen zu den Vorteilen und Features von Azure Defender für Resource Manager.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 922189909da21aac65d1e2e898df58598868b929
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590461"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Einführung in Azure Defender für Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) ist der Bereitstellungs- und Verwaltungsdienst für Azure. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Konto ermöglicht. Mithilfe von Verwaltungsfeatures wie Zugriffssteuerung, Sperren und Tags können Sie Ihre Ressourcen nach der Bereitstellung schützen und organisieren.

Die Cloudverwaltungsebene ist ein wichtiger Dienst, der mit allen Ihren Cloudressourcen verbunden ist. Dies macht ihn allerdings auch zu einem potenziellen Ziel für Angreifer. Daher sollte die Ressourcenverwaltungsebene sehr genau von Sicherheitsteams überwacht werden. 

Azure Defender für Resource Manager überwacht automatisch die Ressourcenverwaltungsvorgänge in Ihrer Organisation. Dabei spielt es keine Rolle, ob diese über das Azure-Portal, über Azure-REST-APIs, über die Azure CLI oder über andere programmgesteuerte Azure-Clients ausgeführt werden. Von Azure Defender werden erweiterte Sicherheitsanalysen durchgeführt, um Bedrohungen zu erkennen und Sie auf verdächtige Aktivitäten aufmerksam zu machen.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preise:|**Azure Defender für Resource Manager** wird gemäß den Angaben in der [Preisübersicht](security-center-pricing.md) abgerechnet.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Welche Vorteile bietet Azure Defender für Resource Manager?

Azure Defender für Resource Manager schützt unter anderem vor folgenden Problemen:

- **Verdächtige Ressourcenverwaltungsvorgänge**, beispielsweise Vorgänge von verdächtigen IP-Adressen, Deaktivierung von Antischadsoftware oder die Ausführung verdächtiger Skripts in VM-Erweiterungen
- **Verwendung von Exploit-Toolkits** wie Microburst oder PowerZure
- **Lateral Movement** von der Azure-Verwaltungsebene zur Azure-Ressourcendatenebene

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager: Übersichtsdiagramm":::

Eine vollständige Liste der Warnungen, die von Azure Defender für Resource Manager bereitgestellt werden, finden Sie auf der [Referenzseite zu Warnungen](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Untersuchen von Warnungen von Azure Defender für Resource Manager

Sicherheitswarnungen von Azure Defender für Resource Manager basieren auf Bedrohungen, die durch die Überwachung von Azure Resource Manager Vorgängen erkannt werden. Azure Defender nutzt interne Protokollquellen von Azure Resource Manager sowie das Azure-Aktivitätsprotokoll. Hierbei handelt es sich um ein Plattformprotokoll in Azure, das Einblicke in Ereignisse auf Abonnementebene bietet.

Weitere Informationen zum Azure-Aktivitätsprotokoll finden Sie [hier](../azure-monitor/essentials/activity-log.md).

So untersuchen Sie Sicherheitswarnungen von Azure Defender für Resource Manager:

1. Öffnen Sie das Azure-Aktivitätsprotokoll.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Öffnen des Azure-Aktivitätsprotokolls":::

1. Filtern Sie die Ereignisse nach Folgendem:
    - In der Warnung erwähntes Abonnement
    - Zeitrahmen der erkannten Aktivität
    - Zugehöriges Benutzerkonto (sofern relevant)

1. Suchen Sie nach verdächtigen Aktivitäten.

> [!TIP]
> Streamen Sie Ihre Azure-Aktivitätsprotokolle wie unter [Verknüpfen von Daten aus dem Azure-Aktivitätsprotokoll](../sentinel/connect-azure-activity.md) beschrieben an Azure Sentinel, um sie noch besser und umfassender analysieren zu können.



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie sich über Azure Defender für Resource Manager informiert. Verwandte Informationen finden Sie im folgenden Artikel: 

- Sicherheitswarnungen können von Security Center generiert oder von Security Center aus anderen Sicherheitsprodukten empfangen werden. Gehen Sie wie unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md) beschrieben vor, um alle diese Warnungen in Azure Sentinel, in ein beliebiges Drittanbieter-SIEM oder in ein beliebiges anderes externes Tool zu exportieren.

- > [!div class="nextstepaction"]
    > [Aktivieren von Azure Defender](security-center-pricing.md#enable-azure-defender)