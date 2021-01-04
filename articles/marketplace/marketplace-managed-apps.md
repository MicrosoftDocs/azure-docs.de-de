---
title: 'Azure-Anwendungen: Leitfaden für die Veröffentlichung von Angeboten verwalteter Anwendungen – Azure Marketplace'
description: In diesem Artikel werden die Anforderungen für das Veröffentlichen einer verwalteten Anwendung im Azure Marketplace beschrieben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: d4fb3354b7035149b80191528b2f5335b593b764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433549"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Leitfaden für die Veröffentlichung von verwalteten Azure-Anwendungen

Ein Angebot einer von Azure *verwalteten Anwendung* ist eine Möglichkeit, eine Azure-Anwendung im Azure Marketplace zu veröffentlichen. Verwaltete Anwendungen sind Transaktionsangebote, die über den Azure Marketplace bereitgestellt und abgerechnet werden. Den Benutzern wird die Listenoption *Jetzt kaufen* angezeigt.

In diesem Artikel werden die Anforderungen für den Angebotstyp „Verwaltete Anwendung“ erläutert.

Verwenden Sie den Angebotstyp „Verwaltete Anwendung“ unter den folgenden Bedingungen:

- Sie stellen eine abonnementbasierte Lösung für Ihren Kunden bereit, indem Sie entweder einen virtuellen Computer (Virtual Machine, VM) oder eine gesamte auf Infrastructure-as-a-Service (IaaS) basierende Lösung verwenden.
- Sie oder Ihr Kunde benötigen/benötigt eine von einem Partner verwaltete Lösung.

>[!NOTE]
>Ein Partner kann z. B. ein Systemintegrator oder ein Anbieter für verwaltete Dienste (Managed Service Provider, MSP) sein.  

## <a name="managed-application-offer-requirements"></a>Anforderungen für Angebote verwalteter Anwendungen

|Requirements (Anforderungen) |Details  |
|---------|---------|
|Ein Azure-Abonnement | Verwaltete Anwendungen müssen im Abonnement eines Kunden bereitgestellt werden, können aber von einem Drittanbieter verwaltet werden. |
|Abrechnung und Messung    |  Die Ressourcen werden im Azure-Abonnement eines Kunden bereitgestellt. Bei VMs, für die das Modell der nutzungsbasierten Bezahlung verwendet wird, erfolgt die Transaktion mit dem Kunden über Microsoft, und die Abrechnung wird über das Azure-Abonnement des Kunden abgewickelt. <br><br> Bei Bring-Your-Own-License-VMs rechnet Microsoft die angefallenen Infrastrukturkosten im Kundenabonnement ab. Die Softwarelizenzgebühren rechnen Sie jedoch direkt mit dem Kunden ab.        |
|Eine Azure-kompatible virtuelle Festplatte (Virtual Hard Disk, VHD)    |   VMs müssen unter Windows oder Linux erstellt werden.<br><br>Weitere Informationen zum Erstellen einer Linux-VHD finden Sie unter [Von Azure unterstützte Distributionen von Linux](../virtual-machines/linux/endorsed-distros.md).<br><br>Weitere Informationen zum Erstellen einer Windows-VHD finden Sie unter [Erstellen eines Azure-Anwendungsangebots](./create-new-azure-apps-offer.md). |

---

> [!NOTE]
> Verwaltete Anwendungen müssen über den Azure Marketplace bereitgestellt werden können. Wenn die Kundenkommunikation von Bedeutung ist, wenden Sie sich an interessierte Kunden, nachdem Sie den gemeinsamen Zugriff auf Leads aktiviert haben.  

> [!Note]
> Eine Option zur Nutzung des CSP-Partnerkanals (Cloud Solution Provider) ist jetzt verfügbar. Weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle finden Sie unter [Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich (falls noch nicht geschehen), wie Sie [Ihr Cloudgeschäft mit dem Azure Marketplace ausweiten](https://azuremarketplace.microsoft.com/sell).

So registrieren Sie sich in Partner Center und beginnen mit der Arbeit

- [Melden Sie sich bei Partner Center an](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership), um Ihr Angebot zu erstellen oder abzuschließen.
- Weitere Informationen finden Sie unter [Erstellen eines Azure-Anwendungsangebots](./create-new-azure-apps-offer.md).
