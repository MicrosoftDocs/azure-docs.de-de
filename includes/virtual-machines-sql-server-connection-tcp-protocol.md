---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554989"
---
1. Suchen Sie nach **Konfigurations-Manager**, während eine Verbindung mit dem virtuellen Computer über Remotedesktop besteht:

    ![SSCM öffnen](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Erweitern Sie im SQL Server-Konfigurations-Manager im Konsolenbereich den Knoten **SQL Server-Netzwerkkonfiguration**.

1. Klicken Sie im Konsolenbereich auf **Protokolle für MSSQLSERVER** (der Standardinstanzenname). Klicken Sie im Detailbereich mit der rechten Maustaste auf **TCP**, und klicken Sie auf **Aktivieren**, wenn die Option noch nicht aktiviert ist.

    ![TCP aktivieren](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klicken Sie im linken Bereich auf **SQL Server-Dienste**. Klicken Sie im Detailbereich mit der rechten Maustaste auf **SQL Server (*Instanzname*)** (die Standardinstanz ist **SQL Server (MSSQLSERVER)** ), und klicken Sie dann auf **Neu starten**, um die Instanz von SQL Server zu beenden und neu zu starten.

    ![Datenbank-Engine neu starten](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Schließen Sie den SQL Server-Konfigurations-Manager.

Weitere Informationen zur Aktivierung von Protokollen für SQL Server-Datenbank-Engines finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).