---
title: Angebote für Azure Marketplace und Microsoft AppSource
description: Erstellen und Verwalten von Angeboten für Azure Marketplace und Microsoft AppSource
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: 2d440adc5111db11486d1fc606abbda0ffae9595
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942529"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Angebote für Azure Marketplace und Microsoft AppSource

Der erste Teil dieses Abschnitts stellt die allgemeinen Abläufe zum Erstellen und Verwalten von Angeboten für Azure Marketplace und Microsoft AppSource vor.  Dieser Teil bietet den Hintergrund, den Sie verstehen müssen, um bestimmte Angebotstypen zu verwalten, sowie technische Informationen, die allen Angebotstypen gemeinsam sind.  Der Großteil dieses Abschnitts enthält ausführliche Anweisungen zum Erstellen und Verwalten bestimmter Angebotstypen.  

Das folgende Video stellt die verschiedenen Funktionen und Angebotstypen in Azure Marketplace oder AppSource vor.  Darüber hinaus werden wichtige technische und wirtschaftliche Aspekte der Veröffentlichung einer Anwendung oder eines Diensts in diesen Marketplaces behandelt.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Erstellen von Apps und Beratungsdiensten für Azure Marketplace und AppSource – Build 2018**

Weitere Informationen zu diesen Marketplaces finden Sie unter [Veröffentlichungsleitfaden für Azure Marketplace und AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Gängige Vorgänge für Angebote

Das Verfahren zum Erstellen eines neuen Angebots unterscheidet sich je nach Angebotstyp erheblich, z.B. zwischen einem [Azure-Anwendungsangebot](./azure-applications/cpp-azure-app-offer.md) und [Beratungsdienstangebot](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Im Gegensatz dazu sind viele der anderen Vorgänge, die Sie für ein Angebot im [Cloud-Partnerportal](https://cloudpartner.azure.com) durchführen, bei allen Angebotsarten weitgehend standardisiert.  Diese allgemeinen Vorgänge, einschließlich Veröffentlichung, Statusanzeige, Aktualisierung und Löschen, werden im Abschnitt [Verwalten von Angeboten](./manage-offers/cpp-manage-offers.md) behandelt.


## <a name="test-drive"></a>Testversion

*Testversion* ist ein Marketplace-Feature, das Kunden für jedes derart aktivierte Angebot eine Demo-Option zum Testen vor dem Kauf bietet.  Die Funktion „Testversion“ ist auf die folgenden Angebotstypen beschränkt: [Azure-Anwendungen](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [ SaaS-Anwendungen](./saas-app/cpp-saas-offer.md) und [virtuelle Computer](./virtual-machine/cpp-virtual-machine-offer.md).  Diese Funktion erfordert, dass der Herausgeber eine Testversionsvorlage erstellt, die an sein Angebot angepasst ist.  Weitere Informationen finden Sie im Abschnitt [Testversion](./test-drive/what-is-test-drive.md).

Sie können die vorhandenen Marketplace-Angebote mit Testversionen durchsuchen, indem Sie den Filter [Testversion](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive) verwenden. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace- und AppSource-Angebotstypen

Die folgende Tabelle enthält die aktuellen Angebotstypen, die vom [Cloud-Partnerportal](https://cloudpartner.azure.com) unterstützt werden.  Für jeden Angebotstyp sind die Marketplaces, in denen das Angebot aufgeführt werden kann, sowie eine allgemeine Beschreibung der Angebotslösungstechnologie aufgeführt.

|                Angebotstyp                |  Marketplace  |   BESCHREIBUNG                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-Anwendung](./azure-applications/cpp-azure-app-offer.md) | Azure | Die Lösung besteht aus einer oder mehreren virtuellen Computern (VMs), optionalem benutzerdefiniertem Azure-Code, der über eine Azure Resource Manager-Vorlage bereitgestellt wird.  Die Bereitstellung kann entweder durch den Kunden über eine Lösungsvorlage oder durch den Herausgeber erfolgen. Dieser Typ wird verwendet, um mehr Flexibilität zu bieten als der VM-Angebotstyp.  |
| [Beratungsdienst](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | beide | Microsoft-qualifizierte Berater können ihre domänenspezifischen Dienste entweder in Azure Marketplace oder AppSource aufführen.  Ihre Expertise unterstützt Kunden dabei, ihre Probleme zu beurteilen sowie die richtigen Lösungen zu entwickeln und bereitzustellen, um ihre Geschäftsziele zu erreichen.  |
| [Container](./containers/cpp-containers-offer.md)  | Azure | Die Lösung ist ein Docker-Containerimage, das entweder als Kubernetes-basierter Dienst oder in Form von Azure Containerinstanzen bereitgestellt wird. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Ein Paket, das dieses Enterprise Resource Planning- (ERP) und Business Management-System erweitert. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Ein Paket, das dieses Customer Resource Management-System (CRM) um die Module „Vertrieb“, „Service“, „Project Service“ und „Außendienst“ erweitert.  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Ein Paket, das diesen Enterprise Resource Planning-Dienst (ERP) erweitert, der erweiterte Features für Finanzierung, operative Vorgänge, Fertigung und Lieferkettenmanagement unterstützt. |
| [IoT Edge-Modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Ein Docker-kompatibler Container, der auf einem IoT Edge-Gerät ausgeführt wird.  Er enthält kleine Berechnungsmodule, die eine Kombination aus benutzerdefiniertem Code, anderen Azure-Diensten und Diensten von Drittanbietern verwenden. |
| [Power BI-App](./power-bi/cpp-power-bi-offer.md) | AppSource | In einer Power BI-App werden anpassbare Power BI-Inhalte, z. B. Datasets, Berichte und Dashboards, gebündelt. |
| [SaaS-App](./saas-app/cpp-saas-offer.md) | Azure | Die Lösung ist ein vom Herausgeber verwaltetes Software-as-a-Service-Abonnement, an dem sich die Benutzer über eine benutzerdefinierte Schnittstelle anmelden, die Azure Active Directory nutzt. |
| [Virtueller Computer](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Die Lösung ist auf einem einzigen virtuellen Computer enthalten, der im Abonnement des Kunden bereitgestellt wird.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Weitere Informationen finden Sie im [Leitfaden zur Veröffentlichung nach Angebotstyp](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Nächste Schritte

Die allgemeinen Vorgänge, die Sie mit Marketplace-Angeboten durchführen können, und deren gemeinsame technische Attribute und Ressourcen lernen Sie im Artikel [Verwalten von Angeboten](./manage-offers/cpp-manage-offers.md) kennen.
