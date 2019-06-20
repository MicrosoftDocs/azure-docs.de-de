---
title: Voraussetzungen für virtuelle Computer für Microsoft Azure | Azure Marketplace
description: Liste mit den Voraussetzungen, die zum Veröffentlichen eines VM-Angebots im Azure Marketplace erfüllt sein müssen.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 258d21eae5af50b5dc0bed6887618e2999cae45a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257384"
---
# <a name="virtual-machine-prerequisites"></a>Voraussetzungen für virtuelle Computer

Dieser Artikel enthält Informationen zu den technischen und geschäftlichen Anforderungen, die erfüllt sein müssen, um ein VM-Angebot im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) veröffentlichen zu können.  Lesen Sie den [Leitfaden für die Veröffentlichung von VM-Angeboten](../../marketplace-virtual-machines.md), falls Sie dies nicht bereits getan haben.


## <a name="technical-requirements"></a>Technische Anforderungen

Die technischen Voraussetzungen für das Veröffentlichen einer VM-Lösung sind überschaubar:

- Sie benötigen ein aktives Azure-Konto. Sollten Sie über keins verfügen, können Sie sich auf der [Microsoft Azure-Website](https://azure.microsoft.com) registrieren.  
- Sie benötigen eine Umgebung, die so konfiguriert ist, dass sie die Entwicklung virtueller Windows- oder Linux-Computer unterstützt.  Weitere Informationen finden Sie auf der entsprechenden VM-Dokumentationsseite:
    - [Dokumentation zu virtuellen Linux-Computern](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentation zu virtuellen Windows-Computern](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Geschäftliche Anforderungen

Die geschäftlichen Anforderungen umfassen verfahrenstechnische, vertragliche und rechtliche Verpflichtungen: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Sie müssen ein registrierter Cloud Marketplace-Herausgeber sein.  Sollten Sie sich noch nicht registriert haben, führen Sie die Schritte des Artikels [Weg zum Cloud Marketplace-Herausgeber](https://docs.microsoft.com/azure/marketplace/become-publisher) aus.

    > [!NOTE]
    > Sie sollten das gleiche Microsoft Developer Center-Registrierungskonto für die Anmeldung beim [Cloud-Partnerportal](https://cloudpartner.azure.com) verwenden.
    > Sie sollten für Ihre Azure Marketplace-Angebote nur ein Microsoft-Konto verwenden. Es sollte nicht für einzelne Dienste oder Angebote spezifisch sein.
    
- Der Standort Ihres Unternehmens (oder der Niederlassung) muss sich in einem vom Azure Marketplace unterstützten Verkäuferland bzw. einer unterstützten Verkaufsregion befinden.  Eine aktuelle Länder-/Regionenliste finden Sie in den [Teilnahmerichtlinien für Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Ihr Produkt muss auf eine Weise lizenziert sein, die mit den vom Azure Marketplace unterstützten Abrechnungsmodellen kompatibel ist.  Weitere Informationen finden Sie unter [Abrechnungsoptionen im Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Sie sind verantwortlich für das Zur-Verfügung-Stellen von technischem Support für Kunden in einer wirtschaftlich vertretbaren Weise. Der Support kann kostenlos, kostenpflichtig oder communitybasiert sein.
- Sie sind verantwortlich für die Lizenzierung Ihrer Software und sämtlicher Abhängigkeiten von Drittanbietersoftware.
- Sie müssen Inhalte bereitstellen, die die notwendigen Kriterien erfüllen, damit Ihr Angebot im Azure Marketplace und im Azure-Portal aufgelistet wird. <!-- TD: Meaning/links? -->
- Sie müssen den Bedingungen der [Teilnahmerichtlinien für Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) und der Herausgebervereinbarung zustimmen.
- Sie müssen sich die [Nutzungsbedingungen für die Microsoft Azure-Website](https://azure.microsoft.com/support/legal/website-terms-of-use/), die [Datenschutzerklärung von Microsoft](https://privacy.microsoft.com/privacystatement) und die [Microsoft Azure Certified-Programmvereinbarung](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/) einhalten.


## <a name="next-steps"></a>Nächste Schritte

Wenn diese Voraussetzungen erfüllt sind, können Sie Ihr [VM-Angebot erstellen](./cpp-create-offer.md).
