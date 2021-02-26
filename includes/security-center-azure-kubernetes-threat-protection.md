---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007810"
---
Security Center bietet einen Echtzeit-Bedrohungsschutz für Ihre Containerumgebungen und generiert Warnungen für verdächtige Aktivitäten. Mit diesen Informationen können Sie schnell Sicherheitsprobleme lösen und die Sicherheit Ihrer Container verbessern.

Security Center bietet Bedrohungsschutz auf unterschiedlichen Ebenen: 

* **Hostebene (bereitgestellt von Azure Defender für Server):** Mit dem gleichen Log Analytics Agent, den Security Center auch auf anderen VMs verwendet, überwacht Azure Defender Ihre Linux-AKS-Knoten auf verdächtige Aktivitäten wie Webshellerkennung und Verbindungen mit bekannten verdächtigen IP-Adressen. Der Agent führt bei der Überwachung auch containerspezifische Analysen durch, z. B. für die Erstellung privilegierter Container, den verdächtigen Zugriff auf API-Server und SSH-Server (Secure Shell), die in einem Docker-Container ausgeführt werden.

    Wenn Sie die Agents nicht auf Ihren Hosts installieren, profitieren Sie nicht von allen Vorteilen und Sicherheitswarnungen des Bedrohungsschutzes. Sie erhalten weiterhin Warnungen im Zusammenhang mit der Netzwerkanalyse und der Kommunikation mit schädlichen Servern.

    >[!IMPORTANT]
    > Derzeit wird die Installation des Log Analytics-Agents in Azure Kubernetes Service-Clustern, die in VM-Skalierungsgruppen ausgeführt werden, nicht unterstützt.

    Eine Liste der Warnungen auf AKS-Hostebene finden Sie in der [Referenztabelle der Warnungen](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **AKS-Clusterebene (bereitgestellt von Azure Defender für Kubernetes):** Auf Clusterebene basiert der Bedrohungsschutz auf der Analyse der Überwachungsprotokolle von Kubernetes. Aktivieren Sie Azure Defender, um diese Überwachung **ohne Agents** zu ermöglichen. Zum Generieren von Warnungen auf dieser Ebene überwacht Security Center Ihre von AKS verwalteten Dienste mithilfe der von AKS abgerufenen Protokolle. Beispiele für Ereignisse auf dieser Ebene sind verfügbar gemachte Kubernetes-Dashboards und die Erstellung von Rollen mit hohen Berechtigungen und von sensiblen Einbindungen.

    >[!NOTE]
    > Security Center generiert Sicherheitswarnungen für Azure Kubernetes Service-Aktionen und -Bereitstellungen, die nach der Aktivierung der Kubernetes-Option in den Abonnementeinstellungen erfolgen. 

    Eine Liste der Warnungen auf AKS-Clusterebene finden Sie in der [Referenztabelle der Warnungen](../articles/security-center/alerts-reference.md#alerts-akscluster).

Außerdem wird die Bedrohungslandschaft von unserem globalen Team von Sicherheitsforschern ständig überwacht. Sie fügen containerspezifische Warnungen und Sicherheitsrisiken hinzu, sobald sie erkannt werden.

> [!TIP]
> Sie können Containerwarnungen simulieren, indem Sie die Anweisungen in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) befolgen.