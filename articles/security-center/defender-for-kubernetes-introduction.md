---
title: Azure Defender für Kubernetes – Vorteile und Features
description: Erfahren Sie mehr über die Vorteile und Features von Azure Defender für Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a815295c4f2ab78cbd3aff82949d7c28197afd82
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791918"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Einführung in Azure Defender für Kubernetes

Azure Kubernetes Service (AKS) ist der verwaltete Dienst von Microsoft für die Entwicklung, Bereitstellung und Verwaltung von Anwendungen in Containern.

Azure Security Center und AKS bilden das beste cloudnative Kubernetes-Sicherheitsangebot und ermöglichen Umgebungshärtung, Workloadschutz und Laufzeitschutz, wie im Folgenden beschrieben.

Aktivieren Sie zur Bedrohungserkennung für Ihre Kubernetes-Cluster **Azure Defender für Kubernetes** .

Die Bedrohungserkennung auf Hostebene für Ihre Linux-AKS-Knoten ist verfügbar, wenn Sie [Azure Defender für Server](defender-for-servers-introduction.md) aktivieren.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemein verfügbar (Generally Available, GA)|
|Preise:|**Azure Defender für Kubernetes** wird gemäß den Angaben in der [Preisübersicht](security-center-pricing.md) abgerechnet.|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator** kann Warnungen verwerfen.<br>Der **Sicherheitsleseberechtigte** kann Ergebnisse anzeigen.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Welche Vorteile bietet Azure Defender für Kubernetes?

### <a name="run-time-protection"></a>Laufzeitschutz

Durch fortlaufende Analyse der folgenden AKS-Quellen bietet Security Center Echtzeitschutz für Ihre Containerumgebungen und generiert Warnungen über auf Host- *und* AKS-Clusterebene erkannte Bedrohungen und schädliche Aktivitäten. Mit diesen Informationen können Sie schnell Sicherheitsprobleme lösen und die Sicherheit Ihrer Container verbessern.

Security Center bietet Bedrohungsschutz auf unterschiedlichen Ebenen: 

- **Hostebene (bereitgestellt von Azure Defender für Server):** Mit dem gleichen Log Analytics Agent, den Security Center auch auf anderen VMs verwendet, überwacht Azure Defender Ihre Linux-AKS-Knoten auf verdächtige Aktivitäten wie Webshellerkennung und Verbindungen mit bekannten verdächtigen IP-Adressen. Der Agent führt bei der Überwachung auch containerspezifische Analysen durch, z. B. für die Erstellung privilegierter Container, den verdächtigen Zugriff auf API-Server und SSH-Server (Secure Shell), die in einem Docker-Container ausgeführt werden.

    Eine Liste der Warnungen auf AKS-Hostebene finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-containerhost).

    >[!IMPORTANT]
    > Wenn Sie die Agents nicht auf Ihren Hosts installieren, profitieren Sie nicht von allen Vorteilen und Sicherheitswarnungen des Bedrohungsschutzes. Sie erhalten weiterhin Warnungen im Zusammenhang mit der Netzwerkanalyse und der Kommunikation mit schädlichen Servern.

- **AKS-Clusterebene (bereitgestellt von Azure Defender für Kubernetes):** Auf Clusterebene basiert der Bedrohungsschutz auf der Analyse der Überwachungsprotokolle von Kubernetes. Aktivieren Sie Azure Defender, um diese Überwachung **ohne Agents** zu ermöglichen. Zum Generieren von Warnungen auf dieser Ebene überwacht Security Center Ihre von AKS verwalteten Dienste mithilfe der von AKS abgerufenen Protokolle. Beispiele für Ereignisse auf dieser Ebene sind verfügbar gemachte Kubernetes-Dashboards und die Erstellung von Rollen mit hohen Berechtigungen und von sensiblen Einbindungen.

    Eine Liste der Warnungen auf AKS-Clusterebene finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-akscluster).

    >[!NOTE]
    > Security Center generiert Sicherheitswarnungen für Azure Kubernetes Service-Aktionen und -Bereitstellungen, die nach der Aktivierung der Kubernetes-Option in den Abonnementeinstellungen erfolgen. 

Außerdem wird die Bedrohungslandschaft von unserem globalen Team von Sicherheitsforschern ständig überwacht. Sie fügen containerspezifische Warnungen und Sicherheitsrisiken hinzu, sobald sie erkannt werden.

> [!TIP]
> Sie können Containerwarnungen simulieren, indem Sie die Anweisungen in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) befolgen.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Wie funktioniert der Kubernetes-Schutz von Security Center?

Im Folgenden finden Sie ein allgemeines Diagramm der Interaktion zwischen Azure Security Center, dem Azure Kubernetes-Dienst und Azure Policy.

Sie können sehen, dass Security Center die folgenden Elemente empfängt und analysiert:

- Überwachungsprotokolle vom API-Server
- Unformatierte Sicherheitsereignisse vom Log Analytics-Agent
- Clusterkonfigurationsinformationen vom AKS-Cluster
- Workloadkonfiguration von Azure Policy (über das **Azure Policy-Add-On für Kubernetes** ) [Weitere Informationen zu den bewährten Methoden zum Workloadschutz mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Allgemeine Architektur der Interaktion zwischen Azure Security Center, dem Azure Kubernetes-Dienst und Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender für Kubernetes – häufig gestellte Fragen (FAQ)

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kann ich AKS-Schutzmaßnahmen ohne den Log Analytics-Agent erhalten?

Wie bereits erwähnt, bietet der optionale Plan mit **Azure Defender für Kubernetes** Schutzmaßnahmen auf Clusterebene, und der Log Analytics-Agent von **Azure Defender für Server** schützt Ihre Knoten. 

Es wird empfohlen, beide Komponenten bereitzustellen, um einen möglichst umfassenden Schutz zu gewährleisten.

Wenn Sie den Agent nicht auf Ihren Hosts installieren, profitieren Sie nicht von allen Vorteilen und Sicherheitswarnungen des Bedrohungsschutzes. Sie erhalten weiterhin Warnungen im Zusammenhang mit der Netzwerkanalyse und der Kommunikation mit schädlichen Servern.


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Erlaubt AKS mir, benutzerdefinierte VM-Erweiterungen auf meinen AKS-Knoten zu installieren?

Damit Azure Defender Ihre AKS-Knoten überwachen kann, muss dort der Log Analytics-Agent ausgeführt werden. 

AKS ist ein verwalteter Dienst, und da der Log Analytics-Agent eine von Microsoft verwaltete Erweiterung ist, wird er auch auf AKS-Clustern unterstützt.



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Wenn auf meinem Cluster bereits ein Agent für Azure Monitor für Containers ausgeführt wird, brauche ich dann auch den Log Analytics-Agent?

Damit Azure Defender Ihre AKS-Knoten überwachen kann, muss dort der Log Analytics-Agent ausgeführt werden.

Wenn auf Ihren Clustern bereits der Azure Monitor für Container-Agent ausgeführt wird, können Sie auch den Log Analytics-Agent installieren, und die beiden Agents können problemlos nebeneinander verwendet werden.

[Erfahren Sie mehr über den Azure Monitor für Container-Agent](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über den Kubernetes-Schutz von Security Center erfahren, einschließlich Azure Defender für Kubernetes. 

Weitere Informationen finden Sie in den folgenden Artikeln: 

- [Aktivieren von Azure Defender](security-center-pricing.md)
- [Streamen von Warnungen in eine SIEM-, SOAR- oder IT Service Management-Lösung](export-to-siem.md)
- [Verweistabelle zu Warnungen](alerts-reference.md)