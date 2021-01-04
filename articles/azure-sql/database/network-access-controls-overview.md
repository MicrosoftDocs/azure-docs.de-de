---
title: Kontrolle des Netzwerkzugriffs
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Übersicht über die Verwaltung und Steuerung des Netzwerkzugriffs für Azure SQL-Datenbank und Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460008"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Netzwerk-Zugriffssteuerung für Azure SQL-Datenbank und Azure Synapse Analytics

Wenn Sie im [Azure-Portal](single-database-create-quickstart.md) eine logische SQL Server-Instanz für Azure SQL-Datenbank und Azure Synapse Analytics erstellen, ist das Ergebnis ein öffentlicher Endpunkt im Format *IhrServername.database.windows.net*.

Mit den folgenden Netzwerkzugriffssteuerungen können Sie den Zugriff auf eine Datenbank über den öffentlichen Endpunkt selektiv zulassen:

- Azure-Dienste zulassen: Bei einer Festlegung auf „Ein“ können andere Ressourcen innerhalb der Azure-Grenze (z. B. ein virtueller Azure-Computer) auf die SQL-Datenbank zugreifen.
- IP-Firewallregeln: Verwenden Sie dieses Feature, um Verbindungen von einer bestimmten IP-Adresse explizit zuzulassen (z. B. von lokalen Computern).

Sie können den privaten Zugriff auf die Datenbank über [virtuelle Netzwerke](../../virtual-network/virtual-networks-overview.md) auch wie folgt zulassen:

- Firewallregeln für virtuelle Netzwerke: Verwenden Sie dieses Feature, um Datenverkehr von einem bestimmten virtuellen Netzwerk innerhalb der Azure-Grenze zuzulassen.
- Private Link: Verwenden Sie dieses Feature zum Erstellen eines privaten Endpunkts für eine [logische SQL Server-Instanz](logical-servers.md) innerhalb eines bestimmten virtuellen Netzwerks.

> [!IMPORTANT]
> Dieser Artikel bezieht sich *nicht* auf **SQL Managed Instance**. Weitere Informationen zur Netzwerkkonfiguration finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und Azure SQL Managed Instance](../managed-instance/connect-application-instance.md).

Im folgenden Video finden Sie eine Übersicht über diese Zugriffssteuerungen und ihre Aufgaben:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure-Dienste zulassen

Bei der Erstellung einer neuen logischen SQL Server-Instanz im [Azure-Portal](single-database-create-quickstart.md) ist diese Einstellung standardmäßig auf **AUS** festgelegt. Diese Einstellung wird angezeigt, wenn die Konnektivität mithilfe des öffentlichen Dienstendpunkts zugelassen ist.

Sie können diese Einstellung nach dem Erstellen der Instanz auch wie folgt über den Bereich „Firewall“ ändern.
  
![Screenshot: Verwalten der Serverfirewall][2]

Wenn die Einstellung auf **EIN** festgelegt ist, erlaubt Ihr Server die Kommunikation von allen Ressourcen innerhalb der Grenzen von Azure, unabhängig davon, ob diese Teil Ihres Abonnements sind.

In vielen Fällen ist die Einstellung **EIN** weniger einschränkend, als es die meisten Kunden wünschen. Sie sollten diese Einstellung auf **AUS** festlegen und sie durch strengere IP-Firewallregeln oder Firewallregeln für virtuelle Netzwerke ersetzen. 

Dies wirkt sich jedoch auf die folgenden Features auf virtuellen Computern in Azure aus, die nicht Teil Ihres virtuellen Netzwerks sind und somit über eine Azure-IP-Adresse eine Verbindung mit der Datenbank herstellen:

### <a name="import-export-service"></a>Import/Export-Dienst

Der Import/Export-Dienst funktioniert nicht, wenn **Azure-Diensten Zugriff auf den Server erlauben** auf **AUS** festgelegt ist. Sie können dieses Problem jedoch umgehen, indem Sie [„sqlpackage.exe“ manuell auf einem virtuellen Azure-Computer oder den Export mithilfe der DACFx-API direkt in Ihrem Code ausführen](./database-import-export-azure-services-off.md).

### <a name="data-sync"></a>Datensynchronisierung

Wenn **Azure-Diensten Zugriff auf den Server erlauben** mit **AUS** deaktiviert ist und Sie das Datensynchronisierungsfeature verwenden möchten, müssen Sie zum [Hinzufügen von IP-Adressen](firewall-create-server-level-portal-quickstart.md) einzelne Firewallregeleinträge auf der Grundlage des **SQL-Diensttags** für die Region erstellen, in der die **Hub**-Datenbank gehostet wird.
Fügen Sie diese Firewallregeln auf Serverebene den Servern hinzu, die sowohl die **Hub-Datenbank** als auch die **Mitgliedsdatenbanken** hosten. (Diese können sich in verschiedenen Regionen befinden.)

Verwenden Sie das folgende PowerShell-Skript, um die entsprechenden IP-Adressen für das SQL-Diensttag für die Region „USA, Westen“ zu generieren:

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> „Get-AzNetworkServiceTag“ gibt trotz Angabe des Parameters „Location“ den globalen Bereich für das SQL-Diensttag zurück. Filtern Sie daher nach der Region, in der die von Ihrer Synchronisierungsgruppe verwendete Hub-Datenbank gehostet wird.

Beachten Sie, dass die Ausgabe des PowerShell-Skripts in CIDR-Notation (Classless Inter-Domain Routing) erfolgt. Diese muss wie folgt unter Verwendung von [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) in ein Format mit Start- und End-IP-Adresse konvertiert werden:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Sie können dieses zusätzliche PowerShell-Skript verwenden, um alle IP-Adressen von CIDR in das Start- und End-IP-Adressformat zu konvertieren.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Diese können nun als individuelle Firewallregeln hinzugefügt werden. Danach können Sie **Azure-Diensten Zugriff auf den Server erlauben** deaktivieren.

## <a name="ip-firewall-rules"></a>IP-Firewallregeln

Die IP-basierte Firewall ist ein Feature der logischen SQL Server-Instanz in Azure, das den gesamten Zugriff auf den Server verhindert, bis Sie explizit die [IP-Adressen](firewall-create-server-level-portal-quickstart.md) der Clientcomputer hinzufügen.

## <a name="virtual-network-firewall-rules"></a>Firewallregeln für virtuelle Netzwerke

Zusätzlich zu den IP-Regeln können Sie mit der Serverfirewall *Regeln für virtuelle Netzwerke* definieren.  
Weitere Informationen finden Sie unter [Verwenden von Virtual Network-Dienstendpunkten und -Regeln für Server in Azure SQL-Datenbank](vnet-service-endpoint-rule-overview.md) oder in diesem Video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure-Netzwerkterminologie

Beachten Sie die folgenden Azure-Netzwerkbegriffe beim Erkunden von Firewallregeln für virtuelle Netzwerke.

**Virtuelles Netzwerk:** Sie können Ihrem Azure-Abonnement virtuelle Netzwerke zuordnen.

**Subnetz:** Ein virtuelles Netzwerk enthält **Subnetze**. Ihre virtuellen Azure-Computer (VMs) sind Subnetzen zugewiesen. Ein Subnetz kann mehrere VMs oder andere Computeknoten enthalten. Computeknoten, die sich außerhalb Ihres virtuellen Netzwerks befinden, können nicht auf Ihr virtuelles Netzwerk zugreifen, es sei denn, Sie konfigurieren für sie den sicheren Zugriff.

**Dienstendpunkt eines virtuellen Netzwerks:** Ein [Dienstendpunkt eines virtuellen Netzwerks](../../virtual-network/virtual-network-service-endpoints-overview.md) ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. In diesem Artikel beschäftigen wir uns mit dem Typnamen **Microsoft.Sql**, der auf einen Azure-Dienst mit dem Namen „SQL-Datenbank“ verweist.

**VNET-Regel:** Bei einer VNET-Regel für Ihren Server handelt es sich um ein Subnetz, das in der Zugriffssteuerungsliste des Servers aufgeführt ist. Um auf die Zugriffssteuerungsliste für Ihre SQL-Datenbank-Instanz zu gelangen, muss das Subnetz den Typnamen **Microsoft.Sql** enthalten. Eine solche Regel weist Ihren Server an, Nachrichten von allen Knoten im Subnetz zu akzeptieren.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP-Firewallregeln im Vergleich zu Firewallregeln für virtuelle Netzwerke

Die Firewall von Azure SQL-Datenbank ermöglicht Ihnen das Angeben von IP-Adressbereichen, aus denen SQL-Datenbank Nachrichten akzeptiert. Dieser Ansatz eignet sich gut für statische IP-Adressen, die sich außerhalb des privaten Azure-Netzwerks befinden. Doch viele virtuelle Computer (VMs) innerhalb des privaten Azure-Netzwerks sind mit *dynamischen* IP-Adressen konfiguriert. Dynamische IP-Adressen können sich ändern, wenn der virtuelle Computer neu gestartet wird, und damit die IP-basierte Firewallregel ungültig machen. Es wäre töricht, eine dynamische IP-Adresse in einer Firewallregel in einer Produktionsumgebung anzugeben.

Sie können diese Einschränkung umgehen, indem Sie eine *statische* IP-Adresse für Ihre VM abrufen. Einzelheiten finden Sie unter [Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). Der Ansatz mit statischen IP-Adressen kann jedoch schwierig zu handhaben und aufwendig sein, wenn er in großem Maßstab befolgt wird.

Regeln für virtuelle Netzwerke stellen eine einfachere Alternative zum Einrichten und Verwalten des Zugriffs von einem bestimmten Subnetz mit Ihren virtuellen Computern dar.

> [!NOTE]
> Azure SQL-Datenbank wird noch nicht in einem Subnetz unterstützt. Wenn Ihr Server ein Knoten in einem Subnetz in Ihrem virtuellen Netzwerk ist, können alle Knoten innerhalb des virtuellen Netzwerks mit Ihrer SQL-Datenbank-Instanz kommunizieren. In diesem Fall könnten Ihre virtuellen Computer mit der SQL-Datenbank-Instanz kommunizieren, ohne dass VNET-Regeln oder IP-Regeln erforderlich sind.

## <a name="private-link"></a>Private Link

Private Link ermöglicht das Herstellen von Verbindungen mit einem Server über einen **privaten Endpunkt**. Ein privater Endpunkt ist eine private IP-Adresse in einem bestimmten [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) und Subnetz.

## <a name="next-steps"></a>Nächste Schritte

- Einen Schnellstart zum Erstellen einer IP-Firewallregel auf Serverebene finden Sie unter [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](single-database-create-quickstart.md).

- Einen Schnellstart zum Erstellen einer Firewallregel für ein virtuelles Netzwerk auf Serverebene finden Sie unter [Verwenden von Virtual Network-Dienstendpunkten und -Regeln für Server in Azure SQL-Datenbank](vnet-service-endpoint-rule-overview.md).

- Hilfe beim Herstellen einer Verbindung mit einer Datenbank in SQL-Datenbank über Open-Source-Anwendungen oder Drittanbieteranwendungen finden Sie unter [Clientschnellstart: Codebeispiele für SQL-Datenbank](/previous-versions/azure/ee336282(v=azure.100)).

- Informationen zu zusätzlichen Ports, die Sie ggf. öffnen müssen, finden Sie im Abschnitt **SQL-Datenbank: ,Außerhalb‘ im Vergleich zu ,Innerhalb‘** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](adonet-v12-develop-direct-route-ports.md).

- Eine Übersicht über die Azure SQL-Datenbank-Konnektivität finden Sie unter [Verbindungsarchitektur von Azure SQL-Datenbank](connectivity-architecture.md).

- Eine Übersicht über die Sicherheit von Azure SQL-Datenbank finden Sie unter [Sichern der SQL-Datenbank](security-overview.md).

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
