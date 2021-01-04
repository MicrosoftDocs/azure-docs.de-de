---
title: Verwenden von Azure Defender für Containerregistrierungen
description: Hier erfahren Sie etwas über die Verwendung von Azure Defender für Containerregistrierungen zum Überprüfen von Linux-Images in Ihren von Linux gehosteten Registrierungen.
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a5d66e43485ec66b6297ef11ed382e8fb82b7cb3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96014572"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Verwenden von Azure Defender für Containerregistrierungen zum Überprüfen Ihrer Images auf Sicherheitsrisiken

Auf dieser Seite wird erläutert, wie Sie den integrierten Sicherheitsrisikoscanner verwenden, um die in Ihrer auf Azure Resource Manager basierenden Azure Container Registry gespeicherten Containerimages zu überprüfen.

Wenn **Azure Defender für Containerregistrierungen** aktiviert ist, wird jedes in Ihre Registrierung gepushte Image sofort überprüft. Außerdem wird jedes in den letzten 30 Tagen gepullte Image ebenfalls überprüft. 

Wenn der Scanner Sicherheitsrisiken an Security Center meldet, stellt Security Center die Ergebnisse und zugehörigen Informationen als Empfehlungen dar. Darüber hinaus enthalten die Ergebnisse zugehörige Informationen, etwa Problembehandlungsschritte, relevante CVEs, CVSS-Bewertungen und mehr. Sie können die ermittelten Sicherheitsrisiken für ein oder mehrere Abonnements oder für eine bestimmte Registrierung anzeigen.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identifizieren von Sicherheitsrisiken in Images in Azure-Containerregistrierungen 

So aktivieren Sie Sicherheitsrisikoscans von Images in Ihrer auf Azure Resource Manager basierenden Azure Container Registry

1. Aktivieren Sie **Azure Defender für Containerregistrierungen** für Ihr Abonnement. Security Center ist jetzt bereit, Images in Ihren Registrierungen zu überprüfen.

    >[!NOTE]
    > Dieses Feature wird pro Image abgerechnet.

1. Imagescans werden für jeden Push- oder Importvorgang und für den Fall ausgelöst, dass das Image innerhalb der letzten 30 Tage gepullt wurde. 

    Wenn der Scanvorgang abgeschlossen ist (in der Regel nach ungefähr zwei Minuten, er kann aber auch bis zu 15 Minuten dauern), sind die Ergebnisse als Security Center-Empfehlungen verfügbar.

1. [Zeigen Sie die Ergebnisse an, und beheben Sie sie, wie nachstehend erläutert.](#view-and-remediate-findings)

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identifizieren von Sicherheitsrisiken in Images in anderen Containerregistrierungen 

1. Verwenden Sie die ACR-Tools, um Images aus Docker Hub oder Microsoft Container Registry in Ihre Registrierung zu übertragen.  Wenn der Import abgeschlossen ist, werden die importierten Images von Azure Defender gescannt. 

    Weitere Informationen finden Sie unter [Importieren von Containerimages in eine Containerregistrierung](../container-registry/container-registry-import-images.md).

    Wenn der Scanvorgang abgeschlossen ist (in der Regel nach ungefähr zwei Minuten, er kann aber auch bis zu 15 Minuten dauern), sind die Ergebnisse als Security Center-Empfehlungen verfügbar.

1. [Zeigen Sie die Ergebnisse an, und beheben Sie sie, wie nachstehend erläutert.](#view-and-remediate-findings)


## <a name="view-and-remediate-findings"></a>Anzeigen und Beheben von Ergebnissen

1. Auf der Seite **Empfehlungen** können Sie sich die Ergebnisse ansehen. Wenn Probleme gefunden wurden, wird die Empfehlung **Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden** angezeigt.

    ![Empfehlung zum Beheben von Problemen ](media/monitor-container-security/acr-finding.png)

1. Wählen Sie die Empfehlung aus. 

    Die Detailseite der Empfehlung wird geöffnet und zeigt zusätzliche Informationen an. Diese Informationen umfassen die Liste der Registrierungen mit anfälligen Images („Betroffene Ressourcen“) und die Schritte zum Beheben des Problems. 

1. Wählen Sie eine bestimmte Registrierung aus, um die darin enthaltenen Repositorys mit möglicherweise gefährdeten Images anzuzeigen.

    ![Registrierung auswählen](media/monitor-container-security/acr-finding-select-registry.png)

    Die Detailseite der Registrierung wird geöffnet und zeigt eine Liste der betroffenen Repositorys an.

1. Wählen Sie ein bestimmtes Repository aus, um die darin enthaltenen Repositorys mit möglicherweise gefährdeten Images anzuzeigen.

    ![Repository auswählen](media/monitor-container-security/acr-finding-select-repository.png)

    Die Detailseite für das Repository wird geöffnet. Sie zeigt die möglicherweise gefährdeten Images sowie eine Bewertung des Schweregrads der Ergebnisse an.

1. Wählen Sie ein bestimmtes Image aus, um die Sicherheitsrisiken anzuzeigen.

    ![Image auswählen](media/monitor-container-security/acr-finding-select-image.png)

    Die Liste der Ergebnisse für das ausgewählte Image wird geöffnet.

    ![Liste der Ergebnisse](media/monitor-container-security/acr-findings.png)

1. Um mehr über ein Ergebnis zu erfahren, wählen Sie es aus. 

    Der Detailbereich für das Ergebnis wird geöffnet.

    [![Detailbereich für Ergebnis](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Dieser Bereich enthält eine detaillierte Beschreibung des Problems sowie Links zu externen Ressourcen, die bei der Minimierung der Bedrohungen helfen können.

1. Führen Sie die in diesem Bereich angegebenen Schritte zur Problembehebung aus.

1. Wenn Sie die Schritte zum Beheben des Sicherheitsproblems ausgeführt haben, ersetzen Sie das Image in Ihrer Registrierung:

    1. Übertragen Sie das aktualisierte Image per Pushvorgang. Damit wird eine Überprüfung ausgelöst. 
    
    1. Suchen Sie auf der Empfehlungsseite nach der Empfehlung „Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden“. 
    
        Wenn die Empfehlung weiterhin angezeigt wird und das bearbeitete Image sich immer noch in der Liste der anfälligen Images befindet, überprüfen Sie die Schritte zur Problembehebung erneut.

    1. Wenn Sie sicher sind, dass das aktualisierte Image per Push übertragen und überprüft wurde und nicht mehr in der Empfehlung angezeigt wird, löschen Sie das „alte“, anfällige Image aus der Registrierung.


## <a name="disable-specific-findings-preview"></a>Deaktivieren bestimmter Ergebnisse (Vorschau)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

Wenn in Ihrer Organisation eine Suche ignoriert werden muss, anstatt sie zu beheben, können Sie sie optional deaktivieren. Deaktivierte Ergebnisse haben keine Auswirkung auf Ihre Sicherheitsbewertung und erzeugen kein unerwünschtes Rauschen.

Wenn eine Suche mit den in Ihren Deaktivierungsregeln definierten Kriterien übereinstimmt, wird sie nicht in der Liste der Ergebnisse angezeigt. Zu den typischen Szenarien gehören:

- Deaktivieren von Ergebnissen mit einem Schweregrad unterhalb des Mittelwerts
- Deaktivieren von nicht patchbaren Ergebnissen
- Deaktivieren von Ergebnissen mit einer CVSS-Bewertung unter 6,5
- Deaktivieren von Ergebnissen mit spezifischem Text in der Sicherheitsüberprüfung oder -kategorie (z. B. „RedHat“, „CentOS Security Update for sudo“)

> [!IMPORTANT]
> Zum Erstellen einer Regel benötigen Sie Berechtigungen zum Bearbeiten von Richtlinien in Azure Policy.
>
> Weitere Informationen finden Sie unter [Azure RBAC-Berechtigungen in Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Sie können auch eines der folgenden Kriterien verwenden: 

- Ergebnis-ID 
- Category
- Sicherheitsüberprüfung 
- CVSS v3-Bewertungen
- severity 
- Patchbarkeitsstatus 

So erstellen Sie eine Regel:

1. Suchen Sie auf der Seite mit den Empfehlungsdetails nach **Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden**, und wählen Sie **Regel deaktivieren** aus.
1. Wählen Sie den entsprechenden Gültigkeitsbereich aus.
1. Definieren Sie Ihre Kriterien.
1. Wählen Sie **Regel anwenden** aus.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Erstellen einer Deaktivierungsregel für VA-Ergebnisse in der Registrierung":::

1. So können Sie eine Regel anzeigen, überschreiben oder löschen 
    1. Wählen Sie **Regel deaktivieren** aus.
    1. In der Bereichsliste werden Abonnements mit aktiven Regeln als **Rule applied** (Angewandte Regel) angezeigt.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Ändern oder Löschen einer vorhandenen Regel":::
    1. Um die Regel anzuzeigen oder zu löschen, wählen Sie das Menü mit den Auslassungspunkten („...“) aus.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Weitere Informationen zu [Azure Defender](azure-defender.md).