---
title: Verbinden mit virtuellen SQL Server-Computern (Resource Manager) | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Herstellen einer Verbindung mit einem virtuellen SQL Server-Computer in Azure. In diesem Thema wird das klassische Bereitstellungsmodell verwendet. Die Szenarien unterscheiden sich abhängig von der Netzwerkkonfiguration und dem Clientstandort.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 28b68178b98e53b7a7d4192ad20c05a667344969
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356718"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Herstellen einer Verbindung mit einem virtuellen SQL Server-Computer in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Übersicht

In diesem Thema wird beschrieben, wie eine Verbindung mit SQL auf einem virtuellen Azure-Computer (VM) hergestellt wird. Es behandelt einige [allgemeine Konnektivitätsszenarien](#connection-scenarios) und stellt dann die [Schritte im Portal zum Ändern der Konnektivitätseinstellungen](#change) vor. Wenn Sie außerhalb des Portals eine Problembehandlung durchführen oder die Konfiguration von Verbindungen ändern müssen, finden Sie unter [Manuelle Konfiguration](#manual) am Ende dieses Themas weitere Informationen. 

Eine vollständige exemplarische Vorgehensweise, die Bereitstellung und Konnektivität behandelt, finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers in Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Konnektivitätsszenarien

Wie ein Client eine Verbindung mit einem virtuellen SQL Server-Computer herstellt, hängt jeweils vom Standort des Clients und von der Konfiguration des Netzwerks ab.

Wenn Sie einen virtuellen SQL Server-Computer über das Azure-Portal bereitstellen, können Sie die Art der **SQL-Konnektivität** angeben.

![SQL-Verbindungsoption „Öffentlich“ während der Bereitstellung](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Verfügbare Konnektivitätsoptionen:

| Option | BESCHREIBUNG |
|---|---|
| **Öffentlich** | Verbindung mit SQL Server über das Internet. |
| **Privat** | Verbindung mit SQL Server innerhalb des gleichen virtuellen Netzwerks. |
| **Lokal** | Lokale Verbindung mit SQL Server auf dem gleichen virtuellen Computer. | 

In den folgenden Abschnitten werden die Optionen **Öffentlich** und **Privat** näher erläutert.

## <a name="connect-to-sql-server-over-the-internet"></a>Verbinden mit SQL Server über das Internet

Wenn Sie mit der SQL Server-Datenbank-Engine eine Verbindung über das Internet herstellen möchten, wählen Sie im Portal beim Bereitstellen für **SQL-Konnektivität** den Typ **Öffentlich** aus. Das Portal führt automatisch folgende Schritte aus:

* Es aktiviert das TCP/IP-Protokoll für SQL Server.
* Es konfiguriert eine Firewallregel, um den SQL Server-TCP-Port (standardmäßig 1433) zu öffnen.
* Es aktiviert die für den öffentlichen Zugriff erforderliche SQL Server-Authentifizierung.
* Es konfiguriert die Netzwerksicherheitsgruppe auf dem virtuellen Computer für sämtlichen TCP-Datenverkehr am SQL Server-Port.

> [!IMPORTANT]
> Durch die VM-Images für die Editionen SQL Server Developer und Express wird das TCP/IP-Protokoll nicht automatisch aktiviert. Für die Editionen Developer und Express müssen Sie den SQL Server-Konfigurations-Manager verwenden, um [das TCP/IP-Protokoll nach dem Erstellen des virtuellen Computers manuell zu aktivieren](#manualtcp).

Jeder Client mit Internetzugriff kann eine Verbindung mit der SQL Server-Instanz herstellen. Dazu muss entweder die öffentliche IP-Adresse des virtuellen Computers oder eine dieser IP-Adresse zugewiesene DNS-Bezeichnung angegeben werden. Wird der SQL Server-Port 1433 verwendet, müssen Sie ihn nicht in der Verbindungszeichenfolge angeben. Die folgende Verbindungszeichenfolge stellt eine Verbindung mit einem virtuellen SQL-Computer mit der DNS-Bezeichnung `sqlvmlabel.eastus.cloudapp.azure.com` unter Verwendung der SQL-Authentifizierung her. (Sie können auch die öffentliche IP-Adresse verwenden.)

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Zwar wird durch diese Zeichenfolge die Konnektivität für Clients über das Internet ermöglicht, dies bedeutet jedoch nicht, dass jeder Verbindungen mit Ihrer SQL Server-Instanz herstellen kann. Clients von außerhalb müssen den richtigen Benutzernamen und das entsprechende Kennwort verwenden. Zur Erhöhung der Sicherheit können Sie allerdings den bekannten Port 1433 vermeiden. Wenn Sie beispielsweise SQL Server zum Abhören von Port 1500 konfiguriert und entsprechende Regeln für die Firewall und die Netzwerksicherheitsgruppe eingerichtet haben, können Sie eine Verbindung herstellen, indem Sie die Portnummer an den Servernamen anfügen. Im folgenden Beispiel (das auf dem vorherigen Beispiel basiert) wird dem Servernamen die benutzerdefinierte Portnummer **1500** hinzugefügt:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Wenn Sie SQL Server auf einem virtuellen Computer über das Internet abfragen, gelten für alle ausgehenden Daten des Azure-Datencenters die normalen [Preise für ausgehende Datenübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Verbinden mit SQL Server innerhalb eines virtuellen Netzwerks

Wenn Sie im Portal für **SQL-Konnektivität** den Typ **Privat** auswählen, werden die meisten Einstellungen genau wie beim Typ **Öffentlich** konfiguriert. Der einzige Unterschied ist, dass es keine Netzwerksicherheitsgruppen-Regel gibt, die externen Datenverkehr am SQL Server-Port (standardmäßig 1433) zulässt.

> [!IMPORTANT]
> Durch die VM-Images für die Editionen SQL Server Developer und Express wird das TCP/IP-Protokoll nicht automatisch aktiviert. Für die Editionen Developer und Express müssen Sie den SQL Server-Konfigurations-Manager verwenden, um [das TCP/IP-Protokoll nach dem Erstellen des virtuellen Computers manuell zu aktivieren](#manualtcp).

Private Konnektivität wird häufig in Verbindung mit einem [virtuellen Netzwerk](../../../virtual-network/virtual-networks-overview.md) verwendet, was verschiedene Szenarien ermöglicht. Sie können Verbindungen mit VMs innerhalb des gleichen virtuellen Netzwerks herstellen, selbst wenn diese VMs in verschiedenen Ressourcengruppen ausgeführt werden. Mithilfe eines [Site-to-Site-VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)können Sie eine hybride Architektur erstellen, die VMs mit lokalen Netzwerken und Computern verbindet.

Virtuelle Netzwerke ermöglichen auch das Einbinden Ihrer Azure-VMs in eine Domäne. Dies ist die einzige Möglichkeit, wie Sie Windows-Authentifizierung mit SQL Server verwenden können. Für alle anderen Verbindungsszenarien muss SQL-Authentifizierung mit Benutzernamen und Kennwörtern verwendet werden.

Vorausgesetzt, Sie haben DNS in Ihrem virtuellen Netzwerk konfiguriert, können Sie eine Verbindung mit Ihrer SQL Server-Instanz herstellen, indem Sie den VM-Computernamen für SQL Server in der Verbindungszeichenfolge angeben. Im folgenden Beispiel wird zudem angenommen, dass die Windows-Authentifizierung konfiguriert und dem Benutzer Zugriff auf die SQL Server-Instanz erteilt wurde.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Ändern der SQL-Konnektivitätseinstellungen

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Die Konnektivitätseinstellungen für den virtuellen SQL Server-Computer können im Azure-Portal geändert werden.

1. Wählen Sie im Azure-Portal die Option für **SQL-VMs** aus.

2. Wählen Sie den virtuellen SQL Server-Computer aus.

3. Wählen Sie unter **Einstellungen** die Option **Sicherheit** aus.

4. Legen Sie **SQL-Konnektivitätsebene** auf die gewünschte Einstellung fest. In diesem Bereich können Sie optional den SQL Server-Port oder die Einstellungen für die SQL-Authentifizierung ändern.

   ![Ändern der SQL-Konnektivität](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Warten Sie einige Minuten, bis das Update abgeschlossen ist.

   ![Aktualisierungsbenachrichtigung für den virtuellen SQL-Computer](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Aktivieren von TCP/IP für die Editionen Developer und Express

Wenn Sie die SQL Server-Konnektivitätseinstellungen ändern, wird das TCP/IP-Protokoll für die SQL Server-Editionen Developer und Express nicht automatisch aktiviert. In den folgenden Schritten wird erläutert, wie TCP/IP manuell aktiviert wird, sodass Sie über die IP-Adresse eine Remoteverbindung herstellen können.

Stellen Sie zunächst mit Remotedesktop eine Verbindung mit dem virtuellen SQL Server-Computer her.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Aktivieren Sie dann mithilfe des **SQL Server-Konfigurations-Managers** das TCP/IP-Protokoll.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Herstellen einer Verbindung mit SSMS

Die folgenden Schritte zeigen, wie Sie eine optionale DNS-Bezeichnung für Ihren virtuellen Azure-Computer erstellen und anschließend eine Verbindung mit SQL Server Management Studio (SSMS) herstellen.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Manuelle Konfiguration und Problembehandlung

Obwohl das Portal Optionen zum automatischen Konfigurieren der Konnektivität enthält, ist es hilfreich, zu wissen, wie Sie Verbindungen manuell konfigurieren. Grundlegende Informationen zu den Anforderungen können auch für die Problembehandlung hilfreich sein.

Die folgende Tabelle enthält eine Liste der Anforderungen für Verbindungen mit SQL Server auf einer Azure-VM.

| Anforderung | BESCHREIBUNG |
|---|---|
| [Aktivieren des SQL Server-Authentifizierungsmodus](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Die SQL Server-Authentifizierung ist für eine Remoteverbindung mit dem virtuellen Computer erforderlich, sofern Sie nicht Active Directory in einem virtuellen Netzwerk konfiguriert haben. |
| [Erstellen einer SQL-Anmeldung](/sql/relational-databases/security/authentication-access/create-a-login) | Wenn Sie die SQL-Authentifizierung verwenden, benötigen Sie eine SQL-Anmeldung mit einem Benutzernamen und einem Kennwort, die auch über die erforderlichen Berechtigungen für die Zieldatenbank verfügt. |
| [Aktivieren des TCP/IP-Protokolls](#manualtcp) | SQL Server muss Verbindungen über TCP zulassen. |
| [Aktivieren von Firewallregeln für den SQL Server-Port](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Die Firewall auf dem virtuellen Computer muss eingehenden Datenverkehr am SQL Server-Port (Standardport: 1433) zulassen. |
| [Erstellen einer Netzwerksicherheitsgruppen-Regel für TCP-Port 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Sie müssen auf dem virtuellen Computer eingehenden Datenverkehr am SQL Server-Port (Standardport: 1433) zulassen, wenn Sie eine Verbindung über das Internet herstellen möchten. Bei lokalen Verbindungen und Verbindungen ausschließlich über virtuelle Netzwerke ist dies nicht erforderlich. Dies ist der einzige im Azure-Portal erforderliche Schritt. |

> [!TIP]
> Die Schritte in der obigen Tabelle werden für Sie automatisch ausgeführt, wenn Sie die Konnektivität im Portal konfigurieren. Befolgen Sie diese Schritte nur, um Ihre Konfiguration zu bestätigen oder die Konnektivität für SQL Server manuell einzurichten.

## <a name="next-steps"></a>Nächste Schritte

Anweisungen zur Bereitstellung für diese Konnektivitätsschritte finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers in Azure](create-sql-vm-portal.md).

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md).