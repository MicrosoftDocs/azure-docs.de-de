---
title: Verbinden von ExpressRoute mit dem Gateway für virtuelle Netzwerke
description: Schritte zum Verbinden von ExpressRoute mit dem Gateway für virtuelle Netzwerke
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 5f9a565a7662041dbd85e61388129496fa376962
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861515"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navigieren Sie zu der privaten Cloud, die Sie unter [Tutorial: Bereitstellen einer privaten Azure VMware Solution-Cloud in Azure](../tutorial-create-private-cloud.md) erstellt haben. Wählen Sie unter **Verwalten** die Option **Konnektivität** und anschließend die Registerkarte **ExpressRoute** aus.

1. Kopieren Sie den Autorisierungsschlüssel. Falls kein Autorisierungsschlüssel angezeigt wird, müssen Sie einen erstellen. Wählen Sie dazu **+ Autorisierungsschlüssel anfordern** aus.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Kopieren Sie den Autorisierungsschlüssel. Falls kein Autorisierungsschlüssel angezeigt wird, müssen Sie einen erstellen. Wählen Sie dazu „+ Autorisierungsschlüssel anfordern“ aus." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. Navigieren Sie zu dem Gateway des virtuellen Netzwerks, das Sie im vorherigen Schritt erstellt haben, und wählen Sie unter **Einstellungen** die Option **Verbindungen** aus. Wählen Sie auf der Seite **Verbindungen** die Option **+ Hinzufügen** aus.

1. Geben Sie auf der Seite **Verbindung hinzufügen** Werte für die Felder an, und wählen Sie **OK** aus. 

   | Feld | Wert |
   | --- | --- |
   | **Name**  | Geben Sie einen Namen für die Verbindung ein.  |
   | **Verbindungstyp**  | Wählen Sie **ExpressRoute** aus.  |
   | **Autorisierung einlösen**  | Stellen Sie sicher, dass dieses Kontrollkästchen aktiviert ist.  |
   | **Gateway für virtuelle Netzwerke** | Das Gateway für virtuelle Netzwerke, das Sie zuvor erstellt haben  |
   | **Autorisierungsschlüssel**  | Kopieren Sie den Autorisierungsschlüssel von der Registerkarte „ExpressRoute“ für Ihre Ressourcengruppe, und fügen Sie ihn ein. |
   | **Peerleitungs-URI**  | Kopieren Sie die ExpressRoute-ID von der Registerkarte „ExpressRoute“ für Ihre Ressourcengruppe, und fügen Sie sie ein.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Geben Sie auf der Seite „Verbindung hinzufügen“ Werte für die Felder an, und wählen Sie „OK“ aus." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

Die Verbindung zwischen Ihrer ExpressRoute-Leitung und Ihrem virtuellen Netzwerk wird erstellt.