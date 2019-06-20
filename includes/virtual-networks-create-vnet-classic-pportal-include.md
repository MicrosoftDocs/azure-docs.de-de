---
title: include file
description: include file
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa88bf3bd6c5037b41c09e9ffe47921f1b9dc9be
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178005"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Erstellen eines klassischen VNET im Azure-Portal.
Führen Sie zum Erstellen eines klassischen VNET basierend auf dem zuvor beschriebenen Szenario die folgenden Schritte aus.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**. Beachten Sie, dass in der Liste **Bereitstellungsmodell auswählen** bereits **Klassisch** aufgeführt wird. 3. Klicken Sie auf **Erstellen**, wie in der folgenden Abbildung gezeigt.
   
    ![Erstellen eines VNET im Azure-Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Geben Sie im Bereich **Virtuelles Netzwerk** den **Namen** des VNET ein, und klicken Sie dann auf **Adressraum**. Konfigurieren Sie Ihre Adressraumeinstellungen für das VNet und sein erstes Subnetz, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Einstellungen des CIDR-Blocks für das aktuelle Szenario.
   
    ![Bereich des Adressraums](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klicken Sie auf **Ressourcengruppe**, und wählen Sie eine Ressourcengruppe aus, zu der Sie das VNet hinzufügen möchten, oder klicken Sie auf **Neue Ressourcengruppe erstellen**, um das VNet einer neuen Ressourcengruppe hinzuzufügen. Die folgende Abbildung zeigt die Ressourcengruppeneinstellungen für eine neue Ressourcengruppe namens **TestRG**. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Bereich zum Erstellen einer Ressourcengruppe](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Ändern Sie ggf. die **Abonnement**- und **Speicherort**-Einstellungen für das VNet. 
7. Wenn das VNet nicht als Kachel im **Startmenü** angezeigt werden soll, deaktivieren Sie **An Startmenü anheften**. 
8. Klicken Sie auf **Erstellen**, und achten Sie auf die Kachel **Virtuelles Netzwerk erstellen**, wie in der folgenden Abbildung dargestellt.
   
    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Warten Sie, bis das VNET erstellt wurde. Wenn die Kachel angezeigt wird, klicken Sie darauf, um weitere Subnetze hinzuzufügen.
   
    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Daraufhin sollte die **Konfiguration** für das VNET wie dargestellt angezeigt werden. 
   
    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klicken Sie auf **Subnetze** > **Hinzufügen**, geben Sie einen **Namen**ein, legen Sie einen **Adressbereich (CIDR-Block)** für Ihr Subnetz fest, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Einstellungen für das aktuelle Szenario.
    
    ![Erstellen eines VNET im Azure-Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

