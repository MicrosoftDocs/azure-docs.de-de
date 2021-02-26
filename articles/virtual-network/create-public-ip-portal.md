---
title: 'Erstellen einer öffentlichen IP-Adresse: Azure-Portal'
description: Erfahren Sie, wie Sie eine öffentliche IP-Adresse im Azure-Portal erstellen.
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550234"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse im Azure-Portal

In diesem Artikel wird gezeigt, wie Sie mithilfe des Azure-Portals eine öffentliche IP-Adressressource erstellen. Weitere Informationen zu den Ressourcen, denen diese zugeordnet werden kann, zum Unterschied zwischen der SKU „Basic“ und „Standard“ und andere zugehörige Informationen finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md).  In diesem Beispiel konzentrieren wir uns ausschließlich auf IPv4-Adressen. Weitere Informationen zu IPv6-Adressen finden Sie unter [IPv6 für Azure VNET](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-create-public-ip-standard-zones)

Verwenden Sie die folgenden Schritte, um eine zonenredundante öffentliche IP-Standardadresse namens **myStandardZRPublicIP** zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Ressource erstellen**. 
3. Geben Sie in das Suchfeld *Öffentliche IP-Adresse* ein.
4. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adresse** aus. Wählen Sie anschließend auf der Seite **Öffentliche IP-Adressen** die Option **Erstellen** aus.
5. Geben Sie auf der Seite **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus: 

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.                 |    
    | SKU                     | Wählen Sie **Standard** aus.         |
    | Ebene (falls angezeigt*)                  | Wählen Sie **Regional** aus.         |
    | Name                    | Geben Sie *myStandardZRPublicIP* ein.          |
    | IP-Adresszuweisung   | Beachten Sie, dass diese als „statisch“ gesperrt wird.                                        |
    | Leerlaufzeitüberschreitung (Minuten)  | Belassen Sie den Wert bei 4.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, geben Sie „myResourceGroup“ ein, und wählen Sie dann **OK** aus. |
    | Standort                | Wählen Sie **USA, Osten 2** aus.      |
    | Verfügbarkeitszone       | Wählen Sie **Zonenredundant** aus, wählen Sie keine Zone aus, oder wählen Sie eine bestimmte Zone aus (siehe Hinweis unten). |

Beachten Sie, dass dies nur gültige Auswahlmöglichkeiten in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) sind.  (Sie können auch eine bestimmte Zone in diesen Regionen auswählen, die jedoch nicht gegen zonale Ausfälle resilient ist.)  Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = Ebene bezieht sich auf die Funktionalität [Regionsübergreifender Load Balancer](../load-balancer/cross-region-overview.md), die sich derzeit in der Vorschauphase befindet.

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-create-public-ip-basic)

Führen Sie die folgenden Schritte aus, um eine statische öffentliche IP-Adresse des Typs „Basic“ mit dem Namen **myBasicPublicIP** zu erstellen.  Öffentliche IP-Adressen des Typs „Basic“ verwenden nicht das Konzept von Verfügbarkeitszonen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Ressource erstellen**. 
3. Geben Sie in das Suchfeld *Öffentliche IP-Adresse* ein.
4. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adresse** aus. Wählen Sie anschließend auf der Seite **Öffentliche IP-Adressen** die Option **Erstellen** aus.
5. Geben Sie auf der Seite **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus: 

    | Einstellung                 | Wert                       |
    | ---                     | ---                         |
    | IP-Version              | Wählen Sie „IPv4“ aus.                 |    
    | SKU                     | Wählen Sie **Standard** aus.         |
    | Name                    | Geben Sie *myBasicPublicIP* ein.          |
    | IP-Adresszuweisung   | Wählen Sie **Statisch** aus (siehe Hinweis unten).                                     |
    | Leerlaufzeitüberschreitung (Minuten)  | Belassen Sie den Wert bei 4.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, geben Sie „myResourceGroup“ ein, und wählen Sie dann **OK** aus. |
    | Standort                | Wählen Sie **USA, Osten 2** aus.      |

Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann **dynamische** IP-Zuweisung ausgewählt werden.

---

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den oben aufgeführten einzelnen Feldern finden Sie unter [Verwalten öffentlicher IP-Adressen](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](./associate-public-ip-address-vm.md#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
