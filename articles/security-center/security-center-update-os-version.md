---
title: Aktualisieren der Betriebssystemversion in Azure Security Center | Microsoft Docs
description: In diesem Artikel wird gezeigt, wie Sie die Azure Security Center-Empfehlung zum **Aktualisieren der Betriebssystemversion** implementieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60905836"
---
# <a name="update-os-version-in-azure-security-center"></a>Aktualisieren der Betriebssystemversion in Azure Security Center
Für virtuelle Computer in Clouddiensten empfiehlt Azure Security Center die Aktualisierung des Betriebssystems, falls eine neuere Version zur Verfügung steht.  Es werden nur Clouddienst-Webrollen und -Workerrollen überwacht, die in Produktionsslots ausgeführt werden.

> [!NOTE]
> Der Dienst wird anhand einer Beispielbereitstellung vorgestellt.  Es ist keine schrittweise Anleitung.
> 
> 

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Betriebssystemversion aktualisieren** aus.
   ![Betriebssystemversion aktualisieren][1]
2. Daraufhin wird das Blatt **Betriebssystemversion aktualisieren**geöffnet. Führen Sie die Schritte auf diesem Blatt aus, um die Betriebssystemversion zu aktualisieren.

## <a name="see-also"></a>Weitere Informationen
In diesem Artikel wurde gezeigt, wie Sie die Security Center-Empfehlung „Betriebssystemversion aktualisieren“ implementieren. Weitere Informationen zu Clouddiensten und zum Aktualisieren der Betriebssystemversion für einen Clouddienst finden Sie in folgenden Themen:

* [Übersicht über Cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Aktualisieren eines Clouddiensts](../cloud-services/cloud-services-update-azure-service.md)
* [Konfigurieren von Clouddiensten](../cloud-services/cloud-services-how-to-configure-portal.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center:](security-center-partner-solutions.md) Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
