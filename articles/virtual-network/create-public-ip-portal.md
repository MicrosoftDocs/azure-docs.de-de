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
ms.openlocfilehash: add763b713b93604e089d7aec586876fecd2887c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895637"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse im Azure-Portal

In diesem Artikel wird gezeigt, wie Sie mithilfe des Azure-Portals eine öffentliche IP-Adressressource erstellen. Weitere Informationen zu den Ressourcen, denen diese zugeordnet werden kann, zum Unterschied zwischen der SKU „Basic“ und „Standard“ und andere zugehörige Informationen finden Sie unter [Öffentliche IP-Adressen](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  In diesem Beispiel konzentrieren wir uns ausschließlich auf IPv4-Adressen. Weitere Informationen zu IPv6-Adressen finden Sie unter [IPv6 für Azure VNET](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**Standard-SKU: Verwenden von Zonen**](#tab/option-create-public-ip-standard-zones)

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
    | Name                    | Geben Sie *myStandardZRPublicIP* ein.          |
    | IP-Adresszuweisung   | Beachten Sie, dass diese als „statisch“ gesperrt wird.                                        |
    | Leerlaufzeitüberschreitung (Minuten)  | Belassen Sie den Wert bei 4.        |
    | DNS-Namensbezeichnung          | Lassen Sie den Wert leer.    |
    | Subscription            | Wählen Sie Ihr Abonnement aus.   |
    | Resource group          | Wählen Sie **Neu erstellen** aus, geben Sie „myResourceGroup“ ein, und wählen Sie dann **OK** aus. |
    | Standort                | Wählen Sie **USA, Osten 2** aus.      |
    | Verfügbarkeitszone       | Wählen Sie **Zonenredundant** aus, oder wählen Sie eine bestimmte Zone aus (siehe Hinweis unten). |

Beachten Sie, dass dies nur gültige Auswahlmöglichkeiten in Regionen mit [Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) sind.  (Sie können auch eine bestimmte Zone in diesen Regionen auswählen, die jedoch nicht gegen zonale Ausfälle resilient ist.)

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

Weitere Informationen zu den oben aufgeführten einzelnen Feldern finden Sie unter [Verwalten öffentlicher IP-Adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).