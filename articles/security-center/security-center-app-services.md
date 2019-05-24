---
title: Schützen von App Services in Azure Security Center | Microsoft-Dokumentation
description: Diese Artikel bietet eine Einführung in den Schutz von App Services in von Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: v-mohabe
ms.openlocfilehash: b18267b1443b257a575bf2e1199b9f8fa8c419fc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966818"
---
# <a name="protect-app-service-with-azure-security-center"></a>Schützen von App Services mit Azure Security Center
Dieser Artikel unterstützt Sie bei der Verwendung des Azure Security Center zum Überwachen und Schützen Ihrer Anwendungen, die zusätzlich zu App Services laufen.

App Service ermöglicht das Erstellen und Hosten von Webanwendungen in der Programmiersprache Ihrer Wahl, ohne eine Infrastruktur verwalten zu müssen. App Service bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps oder ein beliebiges Git-Repository. 

Schwachstellen in Webanwendungen werden häufig von Angreifern ausgenutzt, da sie für fast alle Unternehmen im Internet eine gemeinsame und dynamische Oberfläche haben. Anforderungen an Anwendungen, die zusätzlich zu App Service laufen, durchlaufen mehrere Gateways, die in Azure-Rechenzentren auf der ganzen Welt bereitgestellt werden und für die Weiterleitung jeder Anforderung an die entsprechende Anwendung verantwortlich sind. 

Das Azure Security Center kann Bewertungen und Empfehlungen zu Ihren Anwendungen, die im App Service ausgeführt werden, in den Sandboxen Ihrer VM oder On-Demand-Instanzen durchführen. Durch die Nutzung der Transparenz von Azure als Cloudanbieter, analysiert das Security Center Ihre internen App Service-Protokolle um sie auf häufige Web-App-Angriffe zu überwachen, die oft auf mehrere Ziele verteilt sind.

Das Security Center nutzt die Größe der Cloud, um Angriffe auf Ihre App Service-Anwendungen zu identifizieren und sich auf neu auftretende Angriffe zu konzentrieren, während sich die Angreifer in der Aufklärungsphase befinden und Schwachstellen auf mehreren Websites identifizieren, die auf Azure gehostet werden. Das Security Center verwendet Analyse- und Machine Learning-Modelle, um alle Schnittstellen abzudecken, die es Kunden ermöglichen, mit ihren Anwendungen zu interagieren, sei es über HTTP oder über Managementmethoden. Darüber hinaus ist das Security Center als First-Party-Dienst in Azure auch in der einzigartigen Lage, hostbasierte Sicherheitsanalysen anzubieten, die die zugrunde liegenden Serverknoten für diese PaaS abdecken und es dem Security Center ermöglichen, Angriffe auf bereits genutzte Webanwendungen zu erkennen.

## <a name="prerequisites"></a>Voraussetzungen

Um Ihren App Service zu überwachen und zu sichern, benötigen Sie einen App Service-Plan, der mit dedizierten Geräten verbunden ist. Diese Pläne sind: Basic, Standard, Premium, Isolated oder Linux. Azure Security Center unterstützt keine Free-, Shared- oder Consumption-Pläne. Weitere Informationen finden Sie unter [App Service-Pläne](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Security Center-Schutz

Das Azure Security Center schützt die VM-Instanz, in der Ihr App Service ausgeführt wird, und die Verwaltungsoberfläche. Es überwacht auch Anforderungen und Antworten, die an und von Ihren Apps gesendet werden, die im App Service ausgeführt werden.

Das Security Center ist nativ in den App Service integriert, wodurch die Notwendigkeit von Bereitstellung und Onboarding entfällt – die Integration ist völlig transparent.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Aktivieren der Überwachung und des Schutz von App Service

1. Wählen Sie in Azure „Security Center“.
2. Wechseln Sie zu **Sicherheitsrichtlinie**, und wählen Sie ein Abonnement.
3. Klicken Sie am Ende der Zeile des Abonnements auf **Einstellungen bearbeiten**.
4. Schalten Sie Ihren Tarif unter **Tarif** in der Zeile **App Service** auf **Aktiviert** um.

![App Service-Umschaltung](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Die Anzahl der Instanzen, die für Ihre Ressourcenmenge aufgelistet sind, stellt die Anzahl der relevanten Instanzen des App Service dar, die zu dem Zeitpunkt aktiv waren, als Sie das Tarifblatt geöffnet haben. Da sich diese Zahl aufgrund der von Ihnen gewählten Skalierungsoptionen ändern kann, wird die Anzahl der Instanzen, für die Ihnen eine Gebühr berechnet wird, entsprechend angepasst.

Um die Überwachung und Empfehlungen für Ihren App Service zu deaktivieren, wiederholen Sie diesen Vorgang und schalten Sie Ihren **App Service**-Plan auf **Deaktiviert** um.



## <a name="see-also"></a>Weitere Informationen
In diesem Artikel haben Sie erfahren, wie Sie die Überwachungsfunktionen in Azure Security Center verwenden können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center](tutorial-security-policy.md): Erfahren Sie, wie Sie Sicherheitseinstellungen in Azure Security Center konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [App Services](security-center-virtual-machine-protection.md#app-services):  Hier können Sie eine Liste Ihrer App Service-Umgebungen mit Integritätszusammenfassungen anzeigen.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
