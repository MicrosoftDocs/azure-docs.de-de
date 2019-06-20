---
title: Erstellen eines VM-Angebots im Azure Marketplace
description: Hier erfahren Sie Schritt für Schritt, wie Sie ein neues VM-Angebot (virtual machine, virtueller Computer) für den Azure Marketplace erstellen.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 4cd635c6f664a5260b79e62ea72bbb86fc4e1e4f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938362"
---
# <a name="create-virtual-machine-offer"></a>Erstellen eines VM-Angebots

In diesem Abschnitt erfahren Sie Schritt für Schritt, wie Sie eine neue VM-Angebotsanforderung (virtual machine, virtueller Computer) für den Azure Marketplace erstellen.  Jedes Angebot wird als eigene Entität im Azure Marketplace angezeigt und mit mindestens einer SKU verknüpft.  Ein VM-Angebot setzt sich aus folgenden Gruppen von Ressourcen und unterstützenden Diensten zusammen: 

![Ressourcen für ein VM-Angebot](./media/publishvm_002.png)

Hierbei gilt:

|  **Ressourcengruppe**   |  **Beschreibung**  |
|  ---------------   |  ---------------  |
|    SKUs            |  Die kleinste kostenpflichtige Einheit eines Angebots. Einem einzelnen Angebot (Produktklasse) können mehrere SKUs zugeordnet werden, um zwischen unterstützten Features, VM-Imagetypen und Abrechnungsmodellen zu unterscheiden. |
|  Marketplace       | Enthält Ressourcen für Marketing, rechtliche Aspekte und Leadverwaltung sowie entsprechende Spezifikationen.  <ul><li> Zu Marketingressourcen zählen unter anderem der Name des Angebots, eine Beschreibung und Logos.</li> <li> Zu den rechtlichen Ressourcen zählen eine Datenschutzrichtlinie, Nutzungsbedingungen und andere rechtliche Dokumentationen.</li>  <li> Mit der Leadverwaltungsrichtlinie können Sie angeben, wie Leads aus dem Azure Marketplace-Endbenutzerportal verwendet werden sollen.</li> </ul> |
| Support            | Enthält Kontakt- und Richtlinieninformationen. |
| Testversion         | Definiert Ressourcen, die es Endbenutzern ermöglichen, Ihr Angebot vor dem Kauf zu testen. |
|  |  |


## <a name="new-offer-form"></a>Formular „Neues Angebot“

Klicken Sie nach der Anmeldung beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) auf der linken Menüleiste auf **+ Neues Angebot**. Klicken Sie im daraufhin angezeigten Menü auf **Virtual Machines**, um das Formular **Neues Angebot** anzuzeigen und mit dem Definieren von Ressourcen für ein neues VM-Angebot zu beginnen. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Neues VM-Angebot: Auswahl auf der Benutzeroberfläche](./media/publishvm_003.png)

> [!WARNING]
> Sollte die Option **Virtual Machines** nicht angezeigt werden oder nicht aktiviert sein, verfügt Ihr Konto nicht über die erforderlichen Berechtigungen, um diesen Angebotstyp zu erstellen.  Überprüfen Sie, ob Sie alle [Voraussetzungen](./cpp-prerequisites.md) für diesen Angebotstyp erfüllen (einschließlich der Registrierung für ein Entwicklerkonto).


## <a name="next-steps"></a>Nächste Schritte

In den folgenden Themen werden die Registerkarten der Seite **Neues Angebot** (für VM-Angebote) erläutert.  In den Artikeln erfahren Sie, wie Sie die jeweilige Registerkarte verwenden, um die Ressourcengruppen und unterstützenden Dienste für Ihr neues VM-Angebot zu definieren.

- [Registerkarte „Angebotseinstellungen“](./cpp-offer-settings-tab.md)
- [Registerkarte „SKUs“](./cpp-skus-tab.md)
- [Registerkarte „Testversion“](./cpp-test-drive-tab.md)
- [Registerkarte „Marketplace“](./cpp-marketplace-tab.md)
- [Registerkarte „Support“](./cpp-support-tab.md)
