---
title: 'Azure-Anwendungssicherheitsgruppen: Übersicht'
titlesuffix: Azure Virtual Network
description: Erfahren Sie mehr über die Verwendung von Anwendungssicherheitsgruppen.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4d8ffe8451b2b2a08ab30761eaf3a928b5e117b3
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537627"
---
# <a name="application-security-groups"></a>Anwendungssicherheitsgruppen

Mit Anwendungssicherheitsgruppen können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf der Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren. Sie können Ihre Sicherheitsrichtlinie nach Bedarf wiederverwenden, ohne dass Sie explizite IP-Adressen manuell warten müssen. Die Plattform übernimmt die komplexe Verarbeitung von expliziten IP-Adressen und mehreren Regelsätzen, damit Sie sich auf Ihre Geschäftslogik konzentrieren können. Das folgende Beispiel bietet Ihnen ein besseres Verständnis von Anwendungssicherheitsgruppen:

![Anwendungssicherheitsgruppen](./media/security-groups/application-security-groups.png)

In der Abbildung oben sind *NIC1* und *NIC2* Mitglieder der Anwendungssicherheitsgruppe *AsgWeb*. *NIC3* ist ein Mitglied der Anwendungssicherheitsgruppe *AsgLogic*. *NIC4* ist ein Mitglied der Anwendungssicherheitsgruppe *AsgDb*. Obwohl jede Netzwerkschnittstelle in diesem Beispiel Mitglied von nur einer Netzwerksicherheitsgruppe ist, kann eine Netzwerkschnittstelle Mitglied mehrerer Anwendungssicherheitsgruppen sein. Dabei gelten die [Einschränkungen für Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Keiner der Netzwerkschnittstellen ist eine Netzwerksicherheitsgruppe zugeordnet. *NSG1* ist beiden Subnetzen zugeordnet und enthält die folgenden Regeln:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Diese Regel ist erforderlich, um Datenverkehr aus dem Internet an die Webserver zuzulassen. Da eingehender Datenverkehr aus dem Internet durch die Standardsicherheitsregel **DenyAllInbound** verweigert wird, ist keine zusätzliche Regel für die Anwendungssicherheitsgruppen *AsgLogic* oder *AsgDb* erforderlich.

|Priority|`Source`|Quellports| Destination | Zielports | Protocol | Zugriff |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

Da die Standardsicherheitsregel **AllowVNetInBound** die gesamte Kommunikation zwischen Ressourcen im gleichen virtuellen Netzwerk erlaubt, ist diese Regel erforderlich, um den Datenverkehr von allen Ressourcen zu verweigern.

|Priority|`Source`|Quellports| Destination | Zielports | Protocol | Zugriff |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Any | Verweigern |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Diese Regel lässt Datenverkehr von der Anwendungssicherheitsgruppe *AsgLogic* zur Anwendungssicherheitsgruppe *AsgDb* zu. Die Priorität für diese Regel ist höher als die Priorität für die Regel *Deny-Database-All*. Daher wird diese Regel vor der Regel *Deny-Database-All* verarbeitet, sodass Datenverkehr von den Anwendungssicherheitsgruppen *AsgLogic* zulässig ist, während der andere Datenverkehr blockiert wird.

|Priority|`Source`|Quellports| Destination | Zielports | Protocol | Zugriff |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

Regeln, in denen eine Anwendungssicherheitsgruppe als Quelle oder Ziel angegeben ist, werden nur auf Netzwerkschnittstellen angewendet, bei denen es sich um Mitglieder der Anwendungssicherheitsgruppe handelt. Wenn die Netzwerkschnittstelle nicht Mitglied einer Anwendungssicherheitsgruppe ist, wird die Regel nicht auf die Netzwerkschnittstelle angewendet, auch wenn die Netzwerksicherheitsgruppe dem Subnetz zugeordnet ist.

Für Anwendungssicherheitsgruppen gelten folgende Einschränkungen:

-    Es gibt Beschränkungen für die Anzahl von Anwendungssicherheitsgruppen in einem Abonnement sowie in Bezug auf Anwendungssicherheitsgruppen. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Sie können eine einzelne Anwendungssicherheitsgruppe als Quelle und Ziel in einer Sicherheitsregel angeben. Sie können in der Quelle und im Ziel nicht mehrere Anwendungssicherheitsgruppen angeben.
- Alle Netzwerkschnittstellen, die einer Anwendungssicherheitsgruppe zugewiesen sind, müssen in selben virtuellen Netzwerk vorhanden sein, in dem sich die erste Netzwerkschnittstelle befindet, die der Anwendungssicherheitsgruppe zugewiesen wurde. Wenn sich die erste Netzwerkschnittstelle, die einer Anwendungssicherheitsgruppe mit dem Namen *AsgWeb* zugewiesen ist, im virtuellen Netzwerk *VNet1* befindet, müssen alle nachfolgenden Netzwerkschnittstellen, die *ASGWeb* zugewiesen werden, in *VNet1* enthalten sein. Einer Anwendungssicherheitsgruppe können keine Netzwerkschnittstellen aus verschiedenen virtuellen Netzwerken hinzugefügt werden.
- Wenn Sie eine Anwendungssicherheitsgruppe als Quelle und Ziel in einer Sicherheitsregel angeben, müssen sich die Netzwerkschnittstellen in beiden Anwendungssicherheitsgruppen im gleichen virtuellen Netzwerk befinden. Wenn also beispielsweise *AsgLogic* Netzwerkschnittstellen aus *VNet1* und *AsgDb* Netzwerkschnittstellen aus *VNet2* enthält, kann *AsgLogic* nicht als Quelle und *AsgDb* nicht als Ziel in einer Regel zugewiesen werden. Alle Netzwerkschnittstellen für die Quell- und Ziel-Anwendungssicherheitsgruppen müssen im selben virtuellen Netzwerk vorhanden sein.

> [!TIP]
> Planen Sie die erforderlichen Anwendungssicherheitsgruppen, und erstellen Sie Regeln nach Möglichkeit mithilfe von Diensttags oder Anwendungssicherheitsgruppen anstelle von individuellen IP-Adresse oder IP-Adressbereichen, um die Anzahl der erforderlichen Sicherheitsregeln und die Notwendigkeit von Regeländerungen zu minimieren.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Netzwerksicherheitsgruppe](tutorial-filter-network-traffic.md)
