---
title: Berechtigungen für den Azure DDoS Protection-Plan
description: Erfahren Sie, wie Sie die Berechtigung in einem Schutzplan verwalten können.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806255"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Verwalten von DDoS Protection-Plänen: Berechtigungen und Einschränkungen

EIn DDoS Protection-Plan funktioniert regions- und abonnementübergreifend. Derselbe Plan kann mit virtuellen Netzwerken aus anderen Abonnements in unterschiedlichen Regionen in Ihrem gesamten Mandanten verknüpft werden. Für das Abonnement, dem der Plan zugeordnet ist, fallen die monatlichen wiederkehrenden Kosten für den Plan sowie Überschreitungsgebühren an für den Fall, dass die Anzahl der geschützten öffentlichen IP-Adressen 100 überschreitet. Weitere Informationen zu den Preisen für DDoS finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Voraussetzungen

- Um die Schritte in diesem Tutorial auszuführen, müssen Sie zunächst einen [Azure DDoS Standard-Schutzplan](manage-ddos-protection.md) erstellen.

## <a name="permissions"></a>Berechtigungen

Zum Arbeiten mit DDoS-Schutzplänen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden Aktionen in der folgenden Tabelle zugewiesen wurden:

| Aktion                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Lesen eines DDoS-Schutzplans              |
| Microsoft.Network/ddosProtectionPlans/write       | Erstellen oder Aktualisieren eines DDoS-Schutzplans  |
| Microsoft.Network/ddosProtectionPlans/delete      | Löschen eines DDoS-Schutzplans            |
| Microsoft.Network/ddosProtectionPlans/join/action | Verknüpfen eines DDoS-Schutzplans              |

Für eine Aktivierung des DDoS-Schutzes für ein virtuelles Netz müssen Ihrem Konto darüber hinaus die entsprechenden [Aktionen für virtuelle Netzwerke](../virtual-network/manage-virtual-network.md#permissions) zugewiesen werden.

## <a name="azure-policy"></a>Azure Policy

Die Erstellung von mehr als einem Plan ist für die meisten Organisationen nicht erforderlich. Ein Plan kann nicht zwischen den Abonnements verschoben werden. Wenn das Abonnement, das einen Plan enthält, geändert werden soll, müssen Sie den vorhandenen Plan löschen und einen neuen erstellen.

Für Kunden, die verschiedene Abonnements haben und sicherstellen möchten, dass ein einziger Plan zur Kostenkontrolle für ihren Mandanten bereitgestellt wird, können Sie mit Azure Policy die [Erstellung von Azure DDoS Protection Standard-Plänen einschränken](https://aka.ms/ddosrestrictplan). Diese Richtlinie blockiert die Erstellung von DDoS-Plänen, es sei denn, das Abonnement wurde zuvor als Ausnahme gekennzeichnet. Diese Richtlinie zeigt außerdem eine Liste aller Abonnements an, für die fälschlicherweise ein DDoS-Plan bereitgestellt wurde, und markiert diese als nicht kompatibel.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anzeigen und Konfigurieren von Telemetriedaten für Ihren DDoS-Schutzplan, finden Sie in den Tutorials.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren der DDoS Protection-Telemetrie](telemetry.md)
