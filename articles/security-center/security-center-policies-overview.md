---
title: Azure Security Center-Einstellungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Azure Security Center-Einstellungen konfigurieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703522"
---
# <a name="security-center-settings"></a>Security Center-Einstellungen
Dieser Artikel enthält eine Übersicht über die Einstellungen in Security Center.

Unter „Sicherheitsrichtlinie“ stehen folgende Einstellungen zur Verfügung:

- **Datensammlung**: Bestimmt die Einstellungen für die Agent-Bereitstellung und die [Datensammlung](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- **Sicherheitsrichtlinie**: Bestimmt, welche Maßnahmen Security Center überwacht und empfiehlt. Die [Sicherheitsrichtlinie](tutorial-security-policy.md) kann in Security Center bearbeitet werden. Außerdem können Sie [Azure Policy](tutorial-security-policy.md) verwenden, um neue Definitionen zu erstellen, zusätzliche Richtlinien zu definieren und verwaltungsgruppenübergreifend Richtlinien zuzuweisen. 
- **E-Mail-Benachrichtigungen**: Bestimmt Sicherheitskontakte und Einstellungen für [E-Mail-Benachrichtigungen](security-center-provide-security-contact-details.md).
- **Tarif**: Definiert die [Tarifauswahl](security-center-pricing.md) (Free oder Standard). Der gewählte Tarif beeinflusst, welche Security Center-Features für betroffene Ressourcen verfügbar sind. Sie können einen Tarif für Abonnements und Arbeitsbereiche angeben.

> [!NOTE]
> Sie können alle diese Einstellungen pro Abonnement vornehmen. Für Arbeitsbereiche können Sie nur „Datensammlung“ und „Tarif“ festlegen.
>


## <a name="who-can-edit-security-policies"></a>Wer kann Sicherheitsrichtlinien bearbeiten?
Security Center verwendet die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Dabei werden integrierte Rollen bereitgestellt, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können. In Security Center werden den Benutzern nur Informationen zu den Ressourcen angezeigt, auf die sie Zugriff haben. Das bedeutet, dass Benutzern die Rolle *Besitzer*, *Mitwirkender* oder *Leser* für das Abonnement zugewiesen wird, zu dem die Ressource gehört. Neben diesen Rollen gibt es zwei spezifische Security Center-Rollen:

- **Sicherheitsleseberechtigter**: Kann Informationen in Security Center (wie etwa Empfehlungen, Warnungen, Richtlinien und die Integrität) anzeigen, aber keine Änderungen vornehmen.
- **Sicherheitsadministrator**: Verfügt über dieselben Anzeigeberechtigungen wie ein *Sicherheitsleseberechtigter*, ist zusätzlich aber auch zum Aktualisieren der Sicherheitsrichtlinie und zum Verwerfen von Empfehlungen und Warnungen berechtigt.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie sich über Sicherheitsrichtlinien in Azure Security Center informiert. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Artikeln:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center](tutorial-security-policy.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements konfigurieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md): Erfahren Sie, wie Security Center-Empfehlungen Ihnen beim Schutz Ihrer Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Azure Security Center-Datensicherheit](security-center-data-security.md): Erfahren Sie, wie Security Center Daten verwaltet und schützt.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Neuigkeiten und Informationen zur Azure-Sicherheit.
