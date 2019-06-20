---
title: Bedrohungserkennung in Azure Security Center | Microsoft-Dokumentation
description: " Integrieren Sie Microsoft Cloud App Security in Azure Security Center, um Bedrohungen und böswillige Absichten zu erkennen. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 7ffb9684045031c5bca7a79a15db7cb16fc99e9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332376"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA für Azure-Ressourcen und -Benutzer 

Azure Security Center arbeitet mit Microsoft Cloud App Security zusammen, um auf der Grundlage von UEBA (User and Entity Behavioral Analytics, Verhaltensanalyse für Benutzer und Entitäten) Warnungen für Ihre Azure-Ressourcen und -Benutzer (Azure-Aktivitäten) bereitzustellen. Diese Warnungen erkennen Anomalien beim Verhalten von Benutzern. Sie basieren auf der Verhaltensanalyse für Benutzer und Entitäten sowie auf Machine Learning (ML) und ermöglichen es Ihnen, umgehend eine erweiterte Bedrohungserkennung für die Aktivitäten Ihrer Abonnements durchzuführen. Dank automatischer Aktivierung liefern die neuen Anomalieerkennungen sofort Ergebnisse, da zahlreiche Verhaltensanomalien bei Benutzern und Ressourcen, die Ihrem Abonnement zugeordnet sind, direkt erkannt werden. Darüber hinaus nutzen diese Warnungen zusätzliche Daten, die bereits in der Erkennungsengine von Microsoft Cloud App Security vorhanden sind, um Ihnen dabei zu helfen, den Untersuchungsprozess zu beschleunigen und aktuelle Bedrohungen abzuwehren. 

> [!NOTE]
> Für Azure Security Center gilt Folgendes: (a) Azure Security Center kann eine Kopie sicherheitsbezogener Kundendaten, die in einer Kundenressource (etwa einem virtuellen Computer oder einem Azure Active Directory-Mandanten) erfasst wurden oder einer Kundenressource zugeordnet sind, im gleichen geografischen Raum speichern, in dem sich auch die Ressource befindet. Hiervon ausgenommen sind geografische Räume, in denen Azure Security Center noch nicht von Microsoft bereitgestellt wurde. In diesem Fall werden solche Daten in den USA gespeichert. (b) Wenn Azure Security Center solche Daten mithilfe eines anderen Microsoft-Onlinediensts verarbeitet, können die Daten im Einklang mit den Georeplikationsregeln dieses anderen Onlinediensts gespeichert werden.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Gültige, aktivierte [Microsoft Cloud App Security-Lizenz](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Security Center im Standard-Tarif](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Warnungen der Bedrohungserkennung

Security Center unterstützt unter anderem folgende Anomalieerkennungswarnungen von Cloud App Security:

**Unmöglicher Ortswechsel**
-  Diese Erkennung identifiziert zwei Benutzeraktivitäten (in einer einzelnen Sitzung oder in mehreren Sitzungen), die von unterschiedlichen geografischen Standorten stammen und in einem Zeitraum liegen, der kürzer ist als die Zeit, die der Benutzer benötigt, um von Standort A zu Standort B zu gelangen. Dies deutet darauf hin, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet. Bei dieser Erkennung wird ein Machine Learning-Algorithmus verwendet, der offensichtlich falsch positive Ergebnisse ignoriert, die den Eindruck eines unmöglichen Ortswechsels erwecken. Hierzu zählen beispielsweise VPNs sowie Standorte, die regelmäßig von anderen Benutzern der Organisation verwendet werden. Die Erkennung hat eine anfängliche Lernphase von sieben Tagen, in der sie sich mit dem Aktivitätsmuster eines neuen Benutzers vertraut macht.

**Aktivität aus selten verwendetem Land**
- Bei dieser Erkennungsmethode werden anhand von in der Vergangenheit verwendeten Aktivitätsstandorten neue und selten verwendete Standorte ermittelt. Die Anomalieerkennungsengine speichert Informationen zu Standorten, die Benutzer der Organisation in der Vergangenheit verwendet haben. Eine Warnung wird ausgelöst, wenn eine Aktivität von einem Standort stammt, der schon länger nicht mehr oder noch nie von einem Benutzer in der Organisation verwendet wurde. 

**Aktivitäten von anonymen IP-Adressen**
- Diese Erkennung stellt fest, ob Benutzer eine IP-Adresse verwendet haben, die als anonyme Proxy-IP-Adresse identifiziert wurde. Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden. Die Erkennung nutzt einen Machine Learning-Algorithmus, um falsch positive Ergebnisse wie etwa falsch gekennzeichnete IP-Adressen, die regelmäßig von anderen Benutzern in der Organisation verwendet werden, zu reduzieren.
 
  ![Warnung der Bedrohungserkennung](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Deaktivieren von Warnungen der Bedrohungserkennung

Diese Warnungen sind standardmäßig aktiviert, können aber deaktiviert werden:

1. Klicken Sie auf dem Security Center-Blatt auf **Sicherheitsrichtlinie**. Klicken Sie für das Abonnement, das Sie ändern möchten, auf **Einstellungen bearbeiten**.
2.  Klicken Sie auf **Bedrohungserkennung**.
3. Deaktivieren Sie unter **Enable integrations** (Integrationen aktivieren) das Kontrollkästchen **Allow Microsoft Cloud App Security to access my data** (Microsoft Cloud App Security Zugriff auf meine Daten gewähren), und klicken Sie anschließend auf **Speichern**.

   ![Warnung der Bedrohungserkennung](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Während der anfänglichen Lernphase von sieben Tagen werden nicht alle Anomalieerkennungswarnungen ausgelöst. Danach wird jede Sitzung unter anderem mit den Aktivitäten, den aktiven Zeiten von Benutzern, den IP-Adressen und den Geräten, die im letzten Monat erkannt wurden, sowie mit der Risikobewertung dieser Aktivitäten abgeglichen. Diese Erkennungen sind Teil der Machine Learning-basierten Anomalieerkennungsengine, die Ihre Umgebung analysiert und Warnungen auf der Grundlage einer Baseline auslöst, die anhand der Aktivitäten Ihrer Organisation ermittelt wurde. Darüber hinaus werden bei diesen Erkennungen Machine Learning-Algorithmen zur Analyse des Benutzer- und Anmeldemusters genutzt, um weniger falsch positive Ergebnisse zu generieren.
>
  
## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie die Bedrohungserkennung in Azure Security Center verwenden. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
