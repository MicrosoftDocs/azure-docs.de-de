---
title: include file
description: Includedatei
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521531"
---
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zur VM, mit der Sie eine Verbindung herstellen möchten, und klicken Sie dann auf **Verbinden**. Wählen Sie in der Dropdownliste die Option **Bastion** aus.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Auswählen von „Bastion“":::

1. Nachdem Sie „Bastion“ aus dem Dropdown ausgewählt haben, wird eine Seitenleiste mit drei Registerkarten angezeigt: RDP, SSH und Bastion. Wenn Bastion für das virtuelle Netzwerk bereitgestellt wurde, ist die Registerkarte „Bastion“ standardmäßig aktiv. Klicken Sie auf **Bastion verwenden**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Klicken auf „Bastion verwenden“":::

1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Benutzernamen und das Kennwort für Ihre VM ein, und klicken Sie dann auf **Verbinden**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Herstellen einer Verbindung":::

1. Die RDP-Verbindung zu diesem virtuellen Computer über Bastion wird direkt im Azure-Portal (über HTML5) über Port 443 und den Bastion-Dienst geöffnet.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Verbinden mithilfe von Port 443":::
