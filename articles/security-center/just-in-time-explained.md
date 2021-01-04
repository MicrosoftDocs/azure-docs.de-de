---
title: Grundlegendes zum Just-In-Time-VM-Zugriff in Azure Security Center
description: In diesem Dokument wird erläutert, wie Sie mit Just-In-Time-VM-Zugriff in Azure Security Center den Zugriff auf die virtuellen Azure-Computer steuern können.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285734"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Grundlegendes zum Just-In-Time(JIT)-VM-Zugriff

Auf dieser Seite werden die Prinzipien der JIT-VM-Zugriffsfunktion von Azure Security Center sowie die Logik hinter der Empfehlung erläutert.

Informationen dazu, wie Sie JIT mithilfe des Azure-Portals (entweder Security Center oder Azure Virtual Machines) oder programmgesteuert auf ihre VMs anwenden, finden Sie unter [Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Das Risiko offener Verwaltungsports auf einem virtuellen Computer

Bedrohungsakteure suchen aktiv nach zugänglichen Computer mit offenen Verwaltungsports wie RDP oder SSH. Alle Ihre virtuellen Computer sind potenzielle Ziele für Angriffe. Wenn eine VM erfolgreich kompromittiert wurde, wird sie als Einstiegspunkt verwendet, um weitere Ressourcen in Ihrer Umgebung anzugreifen.



## <a name="why-jit-vm-access-is-the-solution"></a>Darum ist JIT-VM-Zugriff die Lösung 

Wie bei allen Techniken zur Verbesserung der Cybersicherheit sollte das Ziel darin bestehen, die Angriffsfläche zu reduzieren. In diesem Fall bedeutet das, dass weniger offene Ports vorhanden sind, insbesondere Verwaltungsports.

Ihre legitimen Benutzer verwenden diese Ports ebenfalls, daher ist es nicht sinnvoll, sie geschlossen zu halten.

Um dieses Dilemma zu lösen, bietet Azure Security Center JIT an. Mit JIT kann der eingehende Datenverkehr auf Ihren VMs gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einen einfachen Zugriff auf Verbindungen mit VMs bereitzustellen.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Funktionsweise von JIT mit Netzwerksicherheitsgruppen und Azure Firewall

Wenn Sie den Just-in-Time-VM-Zugriff aktivieren, können Sie die Ports auf der VM auswählen, für die eingehender Datenverkehr blockiert wird. Mit Security Center wird sichergestellt, dass für die von Ihnen ausgewählten Ports in der [Netzwerksicherheitsgruppe ](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) sowie in den [Regeln von Azure Firewall](../firewall/rule-processing.md) Regeln für die „Verweigerung sämtlichen eingehenden Datenverkehrs“ vorhanden sind. Diese Regeln schränken den Zugriff auf die Verwaltungsports ihrer Azure-VMs ein und schützen sie vor Angriffen. 

Sind für die ausgewählten Ports bereits andere Regeln vorhanden, dann haben diese Vorrang vor den neuen Regeln zum Ablehnen von sämtlichem eingehenden Datenverkehr. Wenn es für die ausgewählten Ports keine Regeln gibt, dann haben die neuen Regeln in der NSG und Azure Firewall höchste Priorität.

Wenn ein Benutzer den Zugriff auf einen virtuellen Computer anfordert, überprüft das Security Center, ob der Benutzer über Berechtigungen der [rollenbasierten Zugriffssteuerung von Azure (Azure-RBAC)](../role-based-access-control/role-assignments-portal.md) für diesen virtuellen Computer verfügt. Wenn die Anforderung genehmigt wird, konfiguriert Security Center die NSGs und Azure Firewall so, dass eingehender Datenverkehr zu den ausgewählten Ports von der entsprechenden IP-Adresse (oder dem entsprechenden Bereich) für die angegebene Zeitspanne zugelassen wird. Nach Ablauf dieser Zeitspanne stellt das Security Center die vorherigen Status der NSGs wieder her. Bereits eingerichtete Verbindungen werden nicht unterbrochen.

> [!NOTE]
> JIT unterstützt keine VMs, die durch Azure Firewall-Instanzen geschützt werden, die von [Azure Firewall Manager](../firewall-manager/overview.md) gesteuert werden.




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>So identifiziert Security Center, auf welche VMs JIT angewendet werden soll

Das folgende Diagramm zeigt die Logik, die Security Center bei der Entscheidung über die Kategorisierung Ihrer unterstützten VMs anwendet: 

[![Logikfluss von Just-In-Time (JIT) für virtuelle Computer (VM)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Wenn Security Center einen Computer findet, der von JIT profitieren kann, wird dieser Computer der Registerkarte **Fehlerhafte Ressourcen** der Empfehlung hinzugefügt. 

![Empfehlung für Just-In-Time-Zugriff (JIT) auf virtuelle Computer (VM)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>Häufig gestellte Fragen: Fragen zum Just-in-Time-Zugriff auf virtuelle Computer

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Welche Berechtigungen sind erforderlich, um JIT zu konfigurieren und zu verwenden?

JIT erfordert, dass für das Abonnement [Azure Defender für Server](defender-for-servers-introduction.md) aktiviert ist. 

Die Rollen **Reader** und **SecurityReader** können sowohl den JIT-Status als auch die Parameter anzeigen.

Wenn Sie benutzerdefinierte Rollen erstellen möchten, die mit JIT arbeiten können, benötigen Sie die Details aus der folgenden Tabelle.

> [!TIP]
> Verwenden Sie das Skript [Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) von den GitHub-Communityseiten des Security Center, um für Benutzer, die JIT-Zugriff auf eine VM anfordern und keine anderen JIT-Operationen durchführen müssen, eine Rolle mit den geringsten Berechtigungen zu erstellen.

| Optionen, die Benutzern ermöglicht werden können: | Festzulegende Berechtigungen|
| --- | --- |
|Konfigurieren oder Bearbeiten einer JIT-Richtlinie für einen virtuellen Computer | *Weisen Sie der Rolle diese Aktionen zu:*  <ul><li>Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Im Bereich eines Abonnements oder einer Ressourcengruppe eines virtuellen Computers: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Anfordern von JIT-Zugriff auf einen virtuellen Computer | *Weisen Sie dem Benutzer diese Aktionen zu:*  <ul><li>Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Im Bereich eines Abonnements, einer Ressourcengruppe oder eines virtuellen Computers:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Im Bereich eines Abonnements, einer Ressourcengruppe oder eines virtuellen Computers:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Lesen von JIT-Richtlinien| *Weisen Sie dem Benutzer diese Aktionen zu:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde erläutert, _warum_ Just-in-time(JIT)-VM-Zugriff verwendet werden sollte. 

Fahren Sie mit dem Anleitungsartikel fort, um mehr über die Aktivierung von JIT und die Anforderung des Zugriffs auf Ihre JIT-fähigen VMs zu erfahren:

> [!div class="nextstepaction"]
> [Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)](security-center-just-in-time.md)