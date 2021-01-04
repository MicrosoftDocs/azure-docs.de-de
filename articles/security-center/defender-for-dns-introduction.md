---
title: 'Azure Defender für DNS: Vorteile und Features'
description: Enthält eine Beschreibung der Vorteile und Features von Azure Defender für DNS.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 18d9cb3c64eb41b33a58b248f3826bd9a80c6d90
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754819"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Einführung in Azure Defender für DNS

[Azure DNS](../dns/dns-overview.md) ist ein Hostingdienst für DNS-Domänen, der eine Namensauflösung mittels Microsoft Azure-Infrastruktur bietet. Indem Sie Ihre Domänen in Azure hosten, können Sie Ihre DNS-Einträge unter Verwendung der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsabläufe wie bei Ihren anderen Azure-Diensten verwalten.

Azure Defender für DNS bietet eine zusätzliche Schutzebene für Ihre Cloudressourcen durch Folgendes:

- Kontinuierliche Überwachung aller DNS-Abfragen Ihrer Azure-Ressourcen
- Durchführung erweiterter Sicherheitsanalysen, um Sie auf verdächtige Aktivitäten aufmerksam zu machen

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preise:|**Azure Defender für DNS** wird gemäß den Angaben in der [Preisübersicht](security-center-pricing.md) abgerechnet.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Welche Vorteile bietet Azure Defender für DNS?

Azure Defender für DNS schützt unter anderem vor folgenden Problemen:

- Datenexfiltration aus Ihren Azure-Ressourcen mittels DNS-Tunneling
- Schadsoftware, die mit dem C&C-Server kommuniziert
- Kommunikation mit schädlichen Domänen (beispielsweise Phishing und Kryptografiemining)
- DNS-Angriffe (Kommunikation mit schädlichen DNS-Resolvern) 

Eine vollständige Liste der Warnungen, die von Azure Defender für DNS bereitgestellt werden, finden Sie auf der [Referenzseite zu Warnungen](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Abhängigkeiten

Von Azure Defender für DNS werden keine Agents verwendet. 

Aktivieren Sie Azure Defender für DNS zum Schutz Ihrer DNS-Schicht für jedes Ihrer Abonnements, wie unter [Aktivieren von Azure Defender](security-center-pricing.md) beschrieben.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie sich über Azure Defender für DNS informiert. Verwandte Informationen finden Sie im folgenden Artikel: 

- Sicherheitswarnungen können von Security Center generiert oder von Security Center aus anderen Sicherheitsprodukten empfangen werden. Gehen Sie wie unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md) beschrieben vor, um alle diese Warnungen in Azure Sentinel, in ein beliebiges Drittanbieter-SIEM oder in ein beliebiges anderes externes Tool zu exportieren.

- > [!div class="nextstepaction"]
    > [Aktivieren von Azure Defender](security-center-pricing.md)