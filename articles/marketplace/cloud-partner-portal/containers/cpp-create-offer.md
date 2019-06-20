---
title: Erstellen eines Azure-Containerangebots | Azure Marketplace
description: Es wird beschrieben, wie Sie ein neues Containerangebot für den Marketplace veröffentlichen.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 1a0a2bd9132ba5d018bc5d45699c052d10c30162
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942675"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Erstellen eines neuen Containerangebots mit dem Cloud-Partnerportal

In diesem Artikel wird beschrieben, wie Sie einen Azure Marketplace-Eintrag für ein Containerangebot erstellen und veröffentlichen. Jedes Angebot wird als eigene Entität im Azure Marketplace angezeigt und mit mindestens einer SKU verknüpft.  Ein Containerangebot setzt sich aus den folgenden Gruppen mit Ressourcen und unterstützenden Diensten zusammen:

|  **Ressourcengruppe**   |  **Beschreibung**  |
|  ---------------   |  ---------------  |
|    SKUs            |  Die kleinste bereitstellbare Einheit eines Angebots. Einem einzelnen Angebot (Produktklasse) können mehrere SKUs zugeordnet werden. Mithilfe von SKUs können Sie zwischen unterstützten Funktionen und Abrechnungsmodellen unterscheiden. |
|  Marketplace       | Enthält Ressourcen für Marketing, rechtliche Aspekte und Leadverwaltung sowie entsprechende Spezifikationen.  <ul><li> Zu Marketingressourcen zählen unter anderem der Name des Angebots, eine Beschreibung und Logos.</li> <li> Zu den rechtlichen Ressourcen zählen eine Datenschutzrichtlinie, Nutzungsbedingungen und andere rechtliche Dokumentationen.</li>  <li> Mit der Leadverwaltungsrichtlinie können Sie angeben, wie Leads aus dem Azure Marketplace-Endbenutzerportal verwendet werden sollen.</li> </ul> |
| Support            | Enthält Kontakt- und Richtlinieninformationen. |


## <a name="new-offer-form"></a>Formular „Neues Angebot“ 

Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an, und wählen Sie dann in der linken Menüleiste die Option **+ Neues Angebot**. Wählen Sie im Menü „Neues Angebot“ die Option **Container**, um das Formular **Neues Angebot** anzuzeigen und dann mit dem Definieren von Ressourcen für ein neues Containerangebot zu beginnen.

![Option „Container“ für neues Angebot auswählen](./media/azure-container-offer.png)

## <a name="next-steps"></a>Nächste Schritte

Die Seite **Neues Angebot** für den Containerangebotstyp enthält eine Reihe von Registerkarten und Formularfeldern zum Erstellen eines neuen Angebots. In den folgenden Artikeln wird erläutert, wie Sie auf der jeweiligen Registerkarte die Ressourcengruppen und unterstützenden Dienste für Ihr neues Containerangebot definieren.

- [Registerkarte „Angebotseinstellungen“](./cpp-offer-settings-tab.md)
- [Registerkarte „SKUs“](./cpp-skus-tab.md)
- [Registerkarte „Marketplace“](./cpp-marketplace-tab.md)
- [Registerkarte „Support“](./cpp-support-tab.md)
