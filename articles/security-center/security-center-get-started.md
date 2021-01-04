---
title: 'Upgraden auf Azure Defender: Azure Security Center'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Upgrade auf Azure Defender von Security Center durchführen, um die Sicherheit zu verbessern.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: bd8c5c31579ac22cd6c77dffd31103680e393f69
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187169"
---
# <a name="quickstart-setting-up-azure-security-center"></a>Schnellstart: Einrichten von Azure Security Center

Azure Security Center bietet einheitliche Funktionen für die Sicherheitsverwaltung und den Schutz vor Bedrohungen für Ihre Hybrid Cloud-Workloads. Die kostenlosen Features bieten lediglich eingeschränkte Sicherheit für Ihre Azure-Ressourcen. Durch Aktivieren von Azure Defender können Sie diese Funktionen auf lokale Umgebungen und andere Clouds ausweiten. Azure Defender hilft Ihnen dabei, Sicherheitsrisiken zu finden und zu beseitigen, Zugriffs- und Anwendungssteuerungen anzuwenden, um böswillige Aktivitäten zu blockieren, Bedrohungen mithilfe von Analysen und intelligenten Funktionen zu erkennen und bei Angriffen schnell zu reagieren. Sie können Azure Defender kostenlos testen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).

In diesem Artikel führen Sie zur Verbesserung der Sicherheit ein Upgrade auf Azure Defender durch und installieren den Log Analytics-Agent auf Ihren Computern, um Sicherheitslücken und Bedrohungen ausfindig zu machen.

## <a name="prerequisites"></a>Voraussetzungen
Für den Einstieg in Security Center benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/pricing/free-trial/) registrieren.

Wenn Sie Azure Defender für ein Abonnement aktivieren möchten, muss Ihnen die Rolle „Abonnementbesitzer“, „Mitwirkender des Abonnements“ oder „Sicherheitsadministrator“ zugewiesen sein.


## <a name="enable-security-center-on-your-azure-subscription"></a>Aktivieren von Security Center für Ihr Azure-Abonnement

1. Melden Sie sich beim [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) an.

1. Wählen Sie im Menü des Portals die Option **Security Center** aus. 

    Daraufhin wird die Übersichtsseite von Security Center geöffnet.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Übersichtsdashboard von Security Center" lightbox="./media/security-center-get-started/overview.png":::

In der **Security Center – Übersicht** erhalten Sie einen vereinheitlichten Überblick über den Sicherheitsstatus Ihrer Hybrid Cloud-Workloads und können so die Sicherheit Ihrer Workloads ermitteln und bewerten sowie Risiken identifizieren und mindern. Security Center aktiviert automatisch und kostenlos alle Ihre Azure-Abonnements, für die zuvor kein Onboarding von Ihnen oder von einem anderen Abonnementbenutzer durchgeführt wurde.

Sie können die Liste der Abonnements anzeigen und filtern, indem Sie das Menüelement **Abonnements** auswählen. Die Anzeige wird angepasst, um den Sicherheitsstatus der ausgewählten Abonnements widerzuspiegeln. 

Innerhalb weniger Minuten nach dem ersten Start von Security Center wird ggf. Folgendes angezeigt:

- **Empfehlungen** zur Verbesserung der Sicherheit Ihrer verbundenen Ressourcen.
- Die Ressourcen, die nun von Security Center bewertet werden, sowie der jeweilige Sicherheitsstatus.

Aktivieren Sie Azure Defender, und installieren Sie den Log Analytics-Agent, um optimal von Security Center zu profitieren:

> [!TIP]
> Informationen zur Aktivierung von Security Center in allen Abonnements einer Verwaltungsgruppe finden Sie unter [Aktivieren von Security Center in allen Abonnements einer Verwaltungsgruppe](onboard-management-group.md).

## <a name="enable-azure-defender"></a>Aktivieren von Azure Defender

Für die Security Center-Schnellstartanleitungen -Tutorials muss Azure Defender aktiviert werden. Sie können eine kostenlose 30-Tage-Testversion nutzen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/). 

1. Wählen Sie auf der Security Center-Randleiste die Option **Erste Schritte** aus.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Registerkarte „Upgrade“ der Seite für die ersten Schritte"::: 

    Auf der Registerkarte **Upgrade** werden für das Onboarding geeignete Abonnements und Arbeitsbereiche aufgeführt.

1. Wählen Sie in der Liste **Arbeitsbereiche für die Aktivierung von Azure Defender auswählen** die Arbeitsbereiche aus, für die das Upgrade durchgeführt werden soll.
   - Wenn Sie Abonnements und Arbeitsbereiche auswählen, die nicht für eine Testversion geeignet sind, wird für diese im nächsten Schritt ein Upgrade durchgeführt, und es fallen ab diesem Zeitpunkt Kosten an.
   - Wenn Sie einen Arbeitsbereich auswählen, der für eine kostenlose Testversion geeignet ist, wird im nächsten Schritt eine Testversion gestartet.
1. Wählen Sie **Upgrade ausführen** aus, um Azure Defender zu aktivieren.

## <a name="enable-automatic-data-collection"></a>Aktivieren der automatischen Datensammlung
Security Center sammelt Daten von Ihren Computern, um sie auf Sicherheitslücken und Bedrohungen zu überwachen. Die Daten werden mit dem Log Analytics-Agent gesammelt. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle auf dem Computer und kopiert die Daten zur Analyse in den Arbeitsbereich. Standardmäßig erstellt Security Center einen neuen Arbeitsbereich für Sie.

Bei aktivierter automatischer Bereitstellung wird der Log Analytics-Agent von Security Center auf allen unterstützten und neu erstellten Computern installiert. Die automatische Bereitstellung wird dringend empfohlen.

So aktivieren Sie die automatische Bereitstellung des Log Analytics-Agents:

1. Wählen Sie im Menü von Security Center **Preise und Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Legen Sie auf der Seite **Datensammlung** die Option **Automatische Bereitstellung** auf **Ein** fest.
1. Klicken Sie auf **Speichern**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Aktivieren der automatischen Bereitstellung des Log Analytics-Agents":::

>[!TIP]
> Wenn ein Arbeitsbereich bereitgestellt werden muss, kann die Installation des Agents bis zu 25 Minuten dauern.

Nachdem der Agent auf Ihren Computern bereitgestellt wurde, kann Security Center zusätzliche Empfehlungen im Zusammenhang mit dem Status von Systemupdates, mit Sicherheitskonfigurationen für Betriebssysteme und mit dem Endgeräteschutz geben sowie zusätzliche Sicherheitswarnungen generieren.

>[!NOTE]
> Wenn Sie die automatische Bereitstellung auf **Aus** festlegen, wird der Log Analytics-Agent nicht von virtuellen Azure-Computern entfernt, auf denen er bereits bereitgestellt wurde. Wenn Sie die automatische Bereitstellung deaktivieren, schränkt dies die Sicherheitsüberwachung für Ihre Ressourcen ein.



## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie Azure Defender aktiviert und den Log Analytics-Agent bereitgestellt, um ein einheitliches Sicherheitsmanagement und Bedrohungsschutz für Ihre Hybrid Cloud-Workloads zu erhalten. Wenn Sie mehr darüber erfahren möchten, wie Sie Security Center verwenden können, fahren Sie mit dem Schnellstart für das Einbinden von Windows-Computern fort, die lokal und in anderen Clouds installiert sind.

> [!div class="nextstepaction"]
> [Schnellstart: Durchführen des Onboardings für Azure-fremde Computer](quickstart-onboard-machines.md)

Möchten Sie Ihre Cloudausgaben optimieren und Geld sparen?

> [!div class="nextstepaction"]
> [Beginnen mit der Kostenanalyse mit Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png