---
title: Azure-Anwendungsangebot | Azure Marketplace
description: Übersicht über den Prozess zum Veröffentlichen eines Azure-Anwendungsangebots im Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 3691664ee6212f838e7a9b95089893e4b52c689f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943011"
---
# <a name="azure-application-offer"></a>Azure-Anwendungsangebot

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> In diesem Abschnitt wird erläutert, wie Sie ein neues Azure-Anwendungsangebot im [Azure Marketplace](https://azuremarketplace.microsoft.com) veröffentlichen.  Jede Azure-Anwendung enthält eine Azure Resource Manager-Vorlage, die alle technischen Ressourcen definiert, die von der Anwendung verwendet werden. Dazu gehören in der Regel mindestens ein virtueller Computer sowie andere unterstützende Azure- oder webbasierte Dienste. Alle Azure-App-Angebote müssen Zugriffssicherheit über [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) ermöglichen.  </div> | ![Symbol für Azure-Apps](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Übersicht über die Veröffentlichung

Das folgende Video, [Building Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) (Erstellen von Lösungsvorlagen und verwalteten Anwendungen für den Azure Marketplace), ist eine Einführung darin, welche Angebotsarten verfügbar sind, welche technischen Ressourcen erforderlich sind, wie eine Azure Resource Manager-Vorlage erstellt werden kann, wie die App-Benutzeroberfläche entwickelt und getestet wird, wie das App-Angebot veröffentlicht wird und wie der App-Überprüfungsprozess abläuft.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typen von Azure-Anwendungen

Es gibt zwei Typen von Azure-Anwendungen: verwaltete Anwendung und Lösungsvorlagen. 

- Lösungsvorlagen zählen zu den wichtigsten Optionen für die Veröffentlichung von Lösungen im Marketplace. Dieser Angebotstyp wird verwendet, wenn für eine Lösung neben einem einzelnen virtuellen Computer noch eine zusätzliche Bereitstellungs- und Konfigurationsautomatisierung erforderlich ist. Sie können das Bereitstellen mehrere virtueller Computer mithilfe einer Lösungsvorlage automatisieren. Diese Automatisierung schließt die Bereitstellung von Netzwerk- und Speicherressourcen ein, um komplexe IaaS-Lösungen bereitzustellen. Eine Übersicht über Lösungsvorlagenanforderungen und das Abrechnungsmodell finden Sie unter [Azure-Anwendungen: Lösungsvorlagen](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Verwaltete Anwendungen und Lösungsvorlagen sind bis auf einen wichtigen Unterschied ähnlich. Bei einer verwalteten Anwendung werden die Ressourcen in einer Ressourcengruppe bereitgestellt, die vom Herausgeber der App verwaltet wird. Die Ressourcengruppe befindet sich zwar im Abonnement des Verbrauchers, eine Identität im Mandanten des Herausgebers hat jedoch Zugriff auf die Ressourcengruppe. Als Herausgeber geben Sie die Kosten für den kontinuierlichen Support der Lösung an. Verwenden Sie verwaltete Azure-Anwendungen, um mühelos vollständig verwaltete, schlüsselfertige Anwendungen für Ihre Kunden zu erstellen und bereitzustellen.

Sie können verwaltete Anwendungen nicht nur im Azure Marketplace, sondern auch in einem Dienstkatalog anbieten. Der Dienstkatalog ist ein interner Katalog mit freigegebenen Lösungen für Benutzer in einer Organisation. Sie verwenden den Katalog, um Organisationsstandards zu erfüllen, während Lösungen für Gruppen in einer Organisation angeboten werden. Mitarbeiter können über den Katalog komfortabel Anwendungen finden, die die IT-Abteilung empfohlen und freigegeben hat.

>[!Note]
>Die Nutzung des CSP-Partnerkanals (Cloud Solution Provider) ist jetzt verfügbar.  Unter [Cloud Solution Providers](../../cloud-solution-providers.md) finden Sie weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle.

Weitere Informationen über die Vorteile und Typen von verwalteten Anwendungen finden Sie unter [Übersicht über verwaltete Azure-Anwendungen](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Workflow zum Veröffentlichungsvorgang

In der folgenden Darstellung ist der grundsätzliche Prozess zum Veröffentlichen eines Azure-Anwendungsangebots aufgeführt.

![Workflow für das Veröffentlichen eines Angebots](./media/new-offer-process.png)

Die Veröffentlichung eines Azure-Anwendungsangebots umfasst ganz allgemein folgende Schritte:

1. Erfüllen der [Voraussetzungen](./cpp-prerequisites.md): (nicht gezeigt) Stellen Sie sicher, dass die geschäftlichen und technischen Anforderungen für die Veröffentlichung einer Azure-App im Azure Marketplace erfüllt sind. 

1. [Erstellen des Angebots](./cpp-create-offer.md): Geben Sie detaillierte Angebotsinformationen an. Hierzu zählen unter anderem die Angebotsbeschreibung, Marketingmaterial, Supportinformationen und Ressourcenspezifikationen.

1. [Erstellen oder Sammeln der geschäftlichen und technischen Ressourcen](./cpp-create-technical-assets.md): In diesem Schritt werden die geschäftlichen Ressourcen (rechtlich relevante Dokumente und Marketingmaterial) und die technischen Ressourcen für die zugeordnete Lösung erstellt.

1. [Erstellen der SKU](./cpp-skus-tab.md): Erstellen Sie die dem Angebot zugeordneten SKUs. Für jedes Image, das Sie veröffentlichen möchten, ist eine eindeutige SKU erforderlich.

1. [Zertifizieren und Veröffentlichen des Angebots](./cpp-publish-offer.md): Nach Fertigstellung des Angebots und der technischen Ressourcen kann das Angebot übermittelt werden. Durch diese Übermittlung wird der Veröffentlichungsprozess gestartet. Im Rahmen dieses Prozesses wird die Lösung getestet, überprüft, zertifiziert und anschließend im Azure Marketplace live geschaltet.

## <a name="next-steps"></a>Nächste Schritte

Bevor Sie diese Schritte ausführen, müssen die [technischen und geschäftlichen Anforderungen](./cpp-prerequisites.md) für die Veröffentlichung einer verwalteten Anwendung im Microsoft Azure Marketplace erfüllt sein.
