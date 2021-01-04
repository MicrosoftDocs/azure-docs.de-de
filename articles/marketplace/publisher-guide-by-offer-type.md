---
title: Leitfaden zur Veröffentlichung nach Angebotstyp – Kommerzieller Microsoft-Marketplace
description: In diesem Artikel werden die im kommerziellen Microsoft-Marketplace verfügbaren Angebotstypen beschrieben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 2191a4df5b319ec16a4a6116aa99cfac50c87d9b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462922"
---
# <a name="publishing-guide-by-offer-type"></a>Leitfaden zur Veröffentlichung nach Angebotstyp

In diesem Artikel werden die im kommerziellen Marketplace verfügbaren Angebotstypen beschrieben. Der *Angebotstyp* definiert die Angebotsstruktur einschließlich der Metadaten, Artefakte und sonstigen Inhalte, die im kommerziellen Marketplace präsentiert werden.

Nachdem Sie sich für eine [Veröffentlichungsoption entschieden haben](determine-your-listing-type.md), müssen Sie einen Angebotstyp auswählen, bevor Sie mit der Erstellung Ihres Angebots in Partner Center beginnen. Der Angebotstyp hängt davon ab, welchen Lösungstyp, welche App oder welches Dienstangebot Sie veröffentlichen möchten, sowie von der Ausrichtung des Angebots an Produkten und Diensten von Microsoft.

Sie können einen einzelnen Angebotstyp auf verschiedene Weisen konfigurieren, um unterschiedliche Veröffentlichungsoptionen, Auflistungsoptionen, Bereitstellungen oder Preisgestaltungen zu ermöglichen. Die Veröffentlichungsoption und Konfiguration des Angebotstyps entsprechen auch der Angebotsberechtigung und technischen Anforderungen.

Bevor Sie Ihr Angebot erstellen, informieren Sie sich über die erforderlichen Berechtigungen für Onlineshop und Angebotstyp und darüber hinaus über die technischen Anforderungen der Veröffentlichung.

## <a name="list-of-offer-types"></a>Liste der Angebotsarten

Die folgende Tabelle zeigt die Angebotstypen im kommerziellen Marketplace in Partner Center.

| **Angebotstyp**    | **Beschreibung**  |
| :------------------- | :-------------------|
| [**Azure-Anwendung**](plan-azure-application-offer.md) | Es gibt zwei Typen von Azure-Anwendungsplänen: _Lösungsvorlage_ und _verwaltete Anwendung_. Beide Plantypen unterstützen die Automatisierung der Bereitstellung und Konfiguration einer Lösung auf mehr als einer einzelnen VM. Sie können die Bereitstellung mehrerer Ressourcen automatisieren, beispielsweise VMs, Netzwerke und Speicherressourcen, um komplexe Lösungen (z. B. IaaS-Lösungen) bereitzustellen. Bei beiden Plantypen können viele verschiedene Arten von Azure-Ressourcen genutzt werden, einschließlich u. a. VMs.<ul><li>Pläne für **Lösungsvorlagen** zählen zu den wichtigsten Optionen für die Veröffentlichung von Lösungen im kommerziellen Marketplace. Pläne für Lösungsvorlagen sind keine Transaktionsangebote im kommerziellen Marketplace. Sie können jedoch zum Bereitstellen kostenpflichtiger VM-Angebote verwendet werden, die über den kommerziellen Marketplace abgerechnet werden. Verwenden Sie den Plantyp für Lösungsvorlagen, wenn der Kunde die Lösung verwaltet und die Transaktionen über einen anderen Plan abgerechnet werden.</li><br><li>Verwenden Sie Pläne für **verwaltete Anwendungen**, um mühelos vollständig verwaltete, schlüsselfertige Anwendungen für Ihre Kunden zu erstellen und bereitzustellen. Sie verfügen über die gleichen Funktionen wie Pläne für Lösungsvorlagen mit einigen wichtigen Unterschieden:</li><ul><li> Die Ressourcen werden in einer Ressourcengruppe bereitgestellt und vom Herausgeber der App verwaltet. Die Ressourcengruppe befindet sich zwar im Abonnement des Verbrauchers, eine Identität im Mandanten des Herausgebers hat jedoch Zugriff auf die Ressourcengruppe.</li><li>Als Herausgeber legen Sie die Kosten für den laufenden Support der Lösung fest, und Transaktionen erfolgen über den kommerziellen Marketplace.</li></ul>Verwenden Sie den Plantyp für verwaltete Anwendungen, wenn Sie oder Ihr Kunde möchten, dass die Lösung von einem Partner verwaltet wird, oder wenn Sie eine abonnementbasierte Lösung bereitstellen.</ul> |
| [**Azure-Container**](marketplace-containers.md) | Verwenden Sie den Angebotstyp „Azure Container“, wenn Ihre Lösung ein Docker-Container-Image ist, das als Kubernetes-basierter Azure-Containerdienst bereitgestellt wird. |
| [**Virtueller Azure-Computer**](marketplace-virtual-machines.md) | Verwenden Sie den VM-Angebotstyp beim Bereitstellen einer virtuellen Appliance für das Abonnement Ihres Kunden. |
| [**Beratungsdienst**](./plan-consulting-service-offer.md) | Beratungsdienste unterstützen Kunden dabei, Verbindungen mit Diensten herzustellen, um die Nutzung von Azure-, Dynamics 365- oder Power Suite-Diensten zu unterstützen und zu erweitern.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Sie können AppSource-Angebote veröffentlichen, die Dynamics 365 Business Central, Dynamics 365 Customer Engagement, Power Apps sowie die Apps „Finance“ und „Operations“ ergänzen oder erweitern.|
| [**IoT Edge-Modul**](iot-edge-module.md) | Azure IoT Edge-Module sind die kleinste von IoT Edge verwaltete Recheneinheit. Sie können Microsoft-Dienste (wie Azure Stream Analytics), Dienste von Drittanbietern oder Ihren eigenen lösungsspezifischen Code enthalten. |
| [**Verwalteter Dienst**](partner-center-portal/create-new-managed-service-offer.md) | Über [Azure Lighthouse](../lighthouse/overview.md) können Sie verwaltete Dienstangebote erstellen und vom Kunden delegierte Abonnements oder Ressourcengruppen verwalten.|
| [**Power BI-App**<br/>**Microsoft 365**](appsource-offer-publishing-guide.md) | Sie können AppSource-Angebote veröffentlichen, die Power BI und Microsoft 365 ergänzen oder erweitern.|
| [**Software-as-a-Service**](plan-saas-offer.md) | Verwenden Sie den SaaS-Angebotstyp (Software-as-a-Service), um Ihren Kunden die Möglichkeit zu geben, Ihre SaaS-basierte, technische Lösung als Abonnement zu kaufen. Informationen zu den Anforderungen des einmaligen Anmeldens für SaaS-Angebote finden Sie unter [Azure AD und transaktionsfähige SaaS-Angebote im kommerziellen Marketplace](azure-ad-saas.md). |


## <a name="next-steps"></a>Nächste Schritte

- Berücksichtigen Sie im entsprechenden Artikel für Ihren Angebotstyp die Berechtigungsvoraussetzungen, um die Auswahl und Konfiguration Ihres Angebots abzuschließen.
- Entnehmen Sie den Veröffentlichungsmustern der einzelnen Onlineshops Beispiele für die Zuordnung Ihrer Lösung zu einem Angebotstyp und einer Konfiguration.