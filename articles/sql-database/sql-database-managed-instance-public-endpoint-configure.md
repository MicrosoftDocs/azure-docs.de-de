---
title: Konfigurieren des öffentlichen Endpunkts – Verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen öffentlichen Endpunkt für die verwaltete Instanz konfigurieren.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65465171"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurieren des öffentlichen Endpunkts in der verwalteten Azure SQL-Datenbank-Instanz

Der öffentliche Endpunkt für eine [verwaltete Instanz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) ermöglicht den Datenzugriff auf Ihre verwaltete Instanz von außerhalb des [virtuellen Netzwerks](../virtual-network/virtual-networks-overview.md). Sie können von mehrinstanzenfähigen Azure-Diensten wie Power BI, Azure App Service oder einem lokalen Netzwerk aus auf Ihre verwaltete Instanz zugreifen. Bei Verwendung des öffentlichen Endpunkts auf einer verwalteten Instanz müssen Sie kein VPN verwenden, was VPN-Durchsatzprobleme vermeiden kann.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> - Aktivieren des öffentlichen Endpunkts für Ihre verwaltete Instanz im Azure-Portal
> - Aktivieren des öffentlichen Endpunkts für Ihre verwaltete Instanz mit PowerShell
> - Konfigurieren der Netzwerksicherheitsgruppe Ihrer verwalteten Instanz, um Datenverkehr zum öffentlichen Endpunkt der verwalteten Instanz zuzulassen
> - Abrufen der Verbindungszeichenfolge des öffentlichen Endpunkts der verwalteten Instanz

## <a name="permissions"></a>Berechtigungen

Aufgrund der Vertraulichkeit der Daten in einer verwalteten Instanz erfordert die Konfiguration zum Aktivieren des öffentlichen Endpunkts einer verwalteten Instanz einen zweistufigen Prozess. Diese Sicherheitsmaßnahme entspricht der Aufgabentrennung (Separation of Duties, SoD):

- Das Aktivieren des öffentlichen Endpunkts in einer verwalteten Instanz muss vom Administrator der verwalteten Instanz ausgeführt werden. Den Administrator der verwalteten Instanz finden Sie auf der Seite **Übersicht** Ihrer verwalteten SQL-Instanzenressource.
- Zulassen von Datenverkehr über eine Netzwerksicherheitsgruppe, das von einem Netzwerkadministrator durchgeführt werden muss. Weitere Informationen finden Sie unter [Berechtigungen](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Aktivieren des öffentlichen Endpunkts für eine verwaltete Instanz im Azure-Portal

1. Starten Sie das Azure-Portal unter <https://portal.azure.com/.>.
1. Öffnen Sie die Ressourcengruppe mit der verwalteten Instanz, und wählen Sie die **verwaltete SQL-Instanz** aus, für die Sie den öffentlichen Endpunkt konfigurieren möchten.
1. Wählen Sie in den Einstellungen für die **Sicherheit** die Registerkarte **Virtuelles Netzwerk** aus.
1. Wählen Sie auf der Seite „Konfiguration von virtuellen Netzwerken“ die Option **Aktivieren** und dann das Symbol **Speichern** aus, um die Konfiguration zu aktualisieren.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Aktivieren des öffentlichen Endpunkts für eine verwaltete Instanz mit PowerShell

### <a name="enable-public-endpoint"></a>Aktivieren des öffentlichen Endpunkts

Führen Sie die folgenden PowerShell-Befehle aus: Ersetzen Sie **subscription-id** durch Ihre Abonnement-ID. Ersetzen Sie außerdem **rg-name** durch die Ressourcengruppe für Ihre verwaltete Instanz und **mi-name** durch den Namen Ihrer verwalteten Instanz.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Deaktivieren des öffentlichen Endpunkts

Um den öffentlichen Endpunkt mithilfe von PowerShell zu deaktivieren, führen Sie den folgenden Befehl aus (und vergessen Sie auch nicht, die NSG für den eingehenden Port 3342 zu schließen, wenn Sie ihn konfiguriert haben):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Zulassen von Datenverkehr auf dem öffentlichen Endpunkt in der Netzwerksicherheitsgruppe

1. Wenn die Konfigurationsseite der verwalteten Instanz noch geöffnet ist, navigieren Sie zur Registerkarte **Übersicht**. Andernfalls wechseln Sie zurück zu Ihrer **Verwaltete SQL-Instanz**-Ressource. Wählen Sie den Link **Virtuelles Netzwerk/Subnetz** aus, sodass Sie zu der Konfigurationsseite „Virtuelles Netzwerk“ gelangen.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Wählen Sie im linken Konfigurationsbereich Ihres virtuellen Netzwerks die Registerkarte **Subnetze** aus, und notieren Sie sich die **SICHERHEITSGRUPPE** für Ihre verwaltete Instanz.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Wechseln Sie zurück zur Ressourcengruppe, die Ihre verwaltete Instanz enthält. Daraufhin sollte der oben notierte Name der **Netzwerksicherheitsgruppe** angezeigt werden. Wählen Sie den Namen aus, um die Konfigurationsseite der Netzwerksicherheitsgruppe zu öffnen.

1. Wählen Sie die Registerkarte **Eingangssicherheitsregeln** zum **Hinzufügen** einer Regel mit den folgenden Einstellungen aus, die höhere Priorität aufweist als die Regel **deny_all_inbound**: </br> </br>

    |Einstellung  |Empfohlener Wert  |BESCHREIBUNG  |
    |---------|---------|---------|
    |**Quelle**     |Beliebige IP-Adresse oder beliebiges Diensttag         |<ul><li>Wählen Sie für Azure-Dienste wie Power BI das Azure Cloud-Diensttag aus</li> <li>Verwenden Sie für Ihren Computer oder Ihre Azure-VM die NAT IP-Adresse</li></ul> |
    |**Quellportbereiche**     |*         |Behalten Sie für diese Option „*“ (beliebig) bei, da Quellports in der Regel dynamisch zugeordnet werden und als solche unvorhersehbar sind |
    |**Ziel**     |Beliebig         |Behalten Sie für das Ziel „Beliebig“ bei, um in das Subnetz der verwalteten Instanz eingehenden Datenverkehr zuzulassen |
    |**Zielportbereiche**     |3342         |Legen Sie den Zielport auf 3342 fest, den öffentlichen TDS-Endpunkt der verwalteten Instanz |
    |**Protokoll**     |TCP         |Die verwaltete Instanz verwendet das TCP-Protokoll für TDS |
    |**Aktion**     |ZULASSEN         |Lassen Sie eingehenden Datenverkehr zur verwalteten Instanz über den öffentlichen Endpunkt zu |
    |**Priority**     |1300         |Stellen Sie sicher, dass diese Regel höhere Priorität hat als die Regel **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 wird für Verbindungen des öffentlichen Endpunkts mit der verwalteten Instanz verwendet und kann an dieser Stelle nicht geändert werden.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Abrufen der Verbindungszeichenfolge des öffentlichen Endpunkts der verwalteten Instanz

1. Navigieren Sie zur Konfigurationsseite der verwalteten SQL-Instanz, die für den öffentlichen Endpunkt aktiviert wurde. Wählen Sie die Registerkarte **Verbindungszeichenfolgen** im Konfigurationsabschnitt **Einstellungen** aus.
1. Beachten Sie, dass der Hostname des öffentlichen Endpunkts das Format <mi_name>.**public**.<dns_zone>.database.windows.net hat und der Port 3342 für die Verbindung verwendet wird.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Sicheres Verwenden einer verwalteten Azure SQL-Datenbank-Instanz mit öffentlichem Endpunkt](sql-database-managed-instance-public-endpoint-securely.md).