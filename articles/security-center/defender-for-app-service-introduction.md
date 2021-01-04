---
title: Azure Defender für App Service – Vorteile und Features
description: Erfahren Sie etwas über die Vorteile und Features von Azure Defender für App Service.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb0e073d5ccf73434d05c801b9a8727c1d19fa47
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122237"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Einführung in Azure Defender für App Service

Azure App Service ist eine vollständig verwaltete Plattform zum Entwickeln und Hosting Ihrer Web-Apps und -APIs, wobei Sie sich keine Gedanken über die Verwaltung der Infrastruktur machen müssen. Sie bietet Verwaltung, Überwachung sowie betriebliche Erkenntnisse und erfüllt die Anforderungen auf Unternehmensniveau hinsichtlich Leistung, Sicherheit und Konformität. Weitere Informationen finden Sie unter [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender für App Service** nutzt die Kapazitäten der Cloud, um Angriffe auf Anwendungen unter App Service zu identifizieren. Angreifer testen Webanwendungen, um Schwachstellen zu suchen und auszunutzen. Bevor Anforderungen für in Azure ausgeführte Anwendungen an bestimmte Umgebungen weitergeleitet werden, durchlaufen sie mehrere Gateways, wo sie jeweils überprüft und protokolliert werden. Diese Daten werden dann verwendet, um Exploits und Angreifer zu identifizieren sowie um neue Muster zu erkennen, die später zur Anwendung kommen.

Dank der Möglichkeiten, über die Azure als Cloudanbieter verfügt, kann Security Center interne App Service-Protokolle analysieren und Angriffsmethoden für mehrere Ziele identifizieren. Die Methodik umfasst beispielsweise großflächige Scans und verteilte Angriffe. Bei dieser Art von Angriff, der in der Regel von einer kleinen Untergruppe von IP-Adressen ausgeht, werden ähnliche Endpunkte auf mehreren Hosts durchforstet. Die Angriffe dienen dazu, anfällige Seiten oder Plug-Ins ausfindig zu machen, und sind aus der Perspektive eines einzelnen Hosts nicht erkennbar.


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemein verfügbar (Generally Available, GA)|
|Preise:|[Azure Defender für App Service](azure-defender.md) wird gemäß den Angaben in der [Preisübersicht](security-center-pricing.md) abgerechnet.|
|Unterstützte App Service-Pläne:|![Ja:](./media/icons/yes-icon.png) Basic, Standard, Premium, Isolated oder Linux<br>![Nein:](./media/icons/no-icon.png) Free, Shared oder Verbrauch<br>[Weitere Informationen zu App Service-Plänen](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Was wird mit Azure Defender für App Service geschützt?

Mit dem aktivierten App Service-Plan bewertet Security Center die von Ihrem App Service-Plan abgedeckten Ressourcen und generiert auf der Grundlage der Erkenntnisse Sicherheitsempfehlungen. Security Center schützt die VM-Instanz, in der Ihr App Service ausgeführt wird, und die Verwaltungsoberfläche. Es überwacht auch Anforderungen und Antworten, die an und von Ihren Apps gesendet werden, die im App Service ausgeführt werden.

Wenn Sie einen Windows-basierten App Service-Plan ausführen, kann Security Center auch auf die zugrunde liegenden Sandboxes und VMs zugreifen. In Kombination mit den oben aufgeführten Protokolldaten kann die Infrastruktur das gesamte Spektrum erfassen – von neu in Umlauf gebrachten Angriffen bis hin zu kompromittierten Kundencomputern. Auch wenn Security Center bereitgestellt wird, nachdem eine Web-App ausgenutzt wurde, können fortlaufende Angriffe möglicherweise erkannt werden.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Schützen Ihrer Azure App Service-Web-Apps und -APIs
So schützen Sie Ihren Azure App Service-Plan mit Azure Defender für App Service

- Stellen Sie sicher, dass Sie über einen App Service-Plan verfügen, der dedizierten Computern zugeordnet ist. Unterstützte Pläne sind oben unter [Verfügbarkeit](#availability) aufgeführt.

- Aktivieren Sie **Azure Defender** in Ihrem Abonnement (oder optional nur den **Azure Defender für App Service**-Plan), wie unter [Preise für Azure Security Center](security-center-pricing.md) beschrieben.

Das Security Center ist nativ in den App Service integriert, wodurch die Notwendigkeit von Bereitstellung und Onboarding entfällt – die Integration ist transparent.

>[!NOTE]
> Auf der Seite „Preise und Einstellungen“ werden verschiedene Instanzen für Ihre **Ressourcenmenge** aufgeführt. Dies stellt die Gesamtanzahl von Compute-Instanzen in allen App Service-Plänen für dieses Abonnement dar, die zu dem Zeitpunkt ausgeführt wurden, an dem Sie die Seite mit den Tarifen geöffnet haben.
>
> Azure App Service bietet eine Vielzahl von Plänen. Ihr App Service-Plan definiert einen Satz von Computeressourcen für eine auszuführende Web-App. Diese entsprechen Serverfarmen im herkömmlichen Webhosting. Es können eine oder mehrere Apps für die Ausführung auf denselben Computeressourcen (oder in demselben App Service-Plan) konfiguriert werden.
>
>Navigieren Sie zum Bestätigen der Anzahl im Azure-Portal zu „App Service-Pläne“. Dort wird die Anzahl der Compute-Instanzen angegeben, die von den einzelnen Plänen verwendet werden. 



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Azure Defender für App Service erfahren. 

Weitere Informationen finden Sie in den folgenden Artikeln: 

- Sie können Warnungen exportieren, und zwar unabhängig davon, ob sie von Security Center generiert oder von Security Center über ein anderes Sicherheitsprodukt empfangen wurden. Befolgen Sie die Anweisungen unter [Streamen von Warnungen an eine SIEM-, SOAR- oder IT-Dienstverwaltungslösung](export-to-siem.md), um die Warnungen zu Azure Sentinel (oder ein Drittanbieter-SIEM) bzw. ein beliebiges anderes externes Tool zu exportieren.
- Eine Liste der Azure App Service-Warnungen finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azureappserv).
- Weitere Informationen zu App Service-Plänen finden Sie unter [App Service-Pläne](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Aktivieren von Azure Defender](security-center-pricing.md)