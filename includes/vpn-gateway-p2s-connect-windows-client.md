---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041603"
---
>[!NOTE]
>Auf dem Windows-Clientcomputer, von dem aus Sie die Verbindung herstellen, müssen Sie über Administratorrechte verfügen.
>

1. Um eine Verbindung mit Ihrem VNET herzustellen, navigieren Sie auf dem Clientcomputer zu den VPN-Einstellungen und suchen nach der VPN-Verbindung, die Sie erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Wählen Sie **Verbinden**. Möglicherweise wird eine Popupmeldung angezeigt, die sich auf die Verwendung des Zertifikats bezieht. Wählen Sie **Weiter** aus, um erhöhte Rechte zu verwenden.

1. Wählen Sie auf der Statusseite **Verbindung** **Verbinden** aus, um die Verbindung herzustellen. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, vergewissern Sie sich, dass das angezeigte Clientzertifikat dem Zertifikat entspricht, die Sie zum Herstellen der Verbindung verwenden möchten. Wenn dies nicht der Fall ist, verwenden Sie den Dropdownpfeil, um das richtige Zertifikat auszuwählen, und wählen dann **OK** aus.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Herstellen einer Verbindung von einem Windows-Computer":::

1. Die Verbindung wurde hergestellt.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Herstellen einer Verbindung zwischen einem Computer und einem Azure VNET – Diagramm der Point-to-Site-Verbindung":::
