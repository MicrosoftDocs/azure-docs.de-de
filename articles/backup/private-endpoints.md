---
title: Private Endpunkte
description: Erfahren Sie mehr über den Prozess zum Erstellen privater Endpunkte für Azure Backup und die Szenarien, in denen private Endpunkte dazu beitragen, die Sicherheit Ihrer Ressourcen zu gewährleisten.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0d9d77c139896f9067f73943dbb213fc655f00f6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054871"
---
# <a name="private-endpoints-for-azure-backup"></a>Private Endpunkte für Azure Backup

Azure Backup ermöglicht Ihnen die sichere Sicherung und Wiederherstellung Ihrer Daten aus Ihren Recovery Services-Tresoren unter Verwendung [privater Endpunkte](../private-link/private-endpoint-overview.md). Private Endpunkte arbeiten mit einer oder mehreren privaten IP-Adressen in Ihrem VNET, wodurch der Dienst faktisch in Ihr VNET eingebunden wird.

In diesem Artikel erfahren Sie, wie private Endpunkte für Azure Backup erstellt werden und in welchen Szenarien sie dazu beitragen, die Sicherheit Ihrer Ressourcen zu gewährleisten.

## <a name="before-you-start"></a>Vorbereitung

- Private Endpunkte können nur für neue Recovery Services-Tresore erstellt werden (bei denen keine Elemente im Tresor registriert sind). Daher müssen private Endpunkte erstellt werden, bevor Sie versuchen, Elemente im Tresor zu schützen.
- Ein virtuelles Netzwerk kann private Endpunkte für mehrere Recovery Services-Tresore enthalten. Außerdem kann ein Recovery Services-Tresor über private Endpunkte in mehreren virtuellen Netzwerken verfügen. Die maximale Anzahl privater Endpunkte, die für einen Tresor erstellt werden können, ist jedoch 12.
- Sobald ein privater Endpunkt für einen Tresor erstellt ist, wird der Tresor gesperrt. Der Zugriff (für Sicherungen und Wiederherstellungen) ist nur aus Netzwerken möglich, die einen privaten Endpunkt für den Tresor enthalten. Wenn alle privaten Endpunkte für den Tresor entfernt werden, können alle Netzwerke auf den Tresor zugreifen.
- Eine Verbindung mit privaten Endpunkten für die Sicherung verwendet insgesamt 11 private IP-Adressen in Ihrem Subnetz einschließlich der von Azure Backup zur Sicherung verwendeten. Diese Anzahl kann für bestimmte Azure-Regionen höher sein (bis zu 25). Wir empfehlen daher, genügend private IP-Adressen verfügbar zu haben, wenn Sie versuchen, private Endpunkte für die Sicherung zu erstellen.
- Wenngleich ein Recovery Services-Tresor von sowohl Azure Backup als auch Azure Site Recovery verwendet wird, beschränkt sich die Erörterung in diesem Artikel auf die Verwendung privater Endpunkte für Azure Backup.
- Azure Active Directory unterstützt derzeit keine privaten Endpunkte. Daher müssen IP-Adressen und FQDNs, die für den Betrieb von Azure Active Directory in einer Region erforderlich sind, ein ausgehender Zugriff aus dem abgesicherten Netzwerk gestattet werden, wenn eine Sicherung von Datenbanken in Azure-VMs und eine Sicherung mit dem MARS-Agent erfolgt. Sie können ggf. auch NSG- und Azure Firewall-Tags verwenden, um Zugriff auf Azure AD zuzulassen.
- Virtuelle Netzwerke mit Netzwerkrichtlinien werden für private Endpunkte nicht unterstützt. Sie müssen Netzwerkrichtlinien deaktivieren, ehe Sie fortfahren können.
- Sie müssen den Recovery Services-Ressourcenanbieter erneut mit dem Abonnement registrieren, wenn Sie ihn vor dem 1. Mai 2020 registriert haben. Um den Anbieter erneut zu registrieren, wechseln Sie im Azure-Portal zu Ihrem Abonnement, navigieren Sie in der linken Navigationsleiste zu **Ressourcenanbieter** und wählen Sie **Microsoft.RecoveryServices** und dann **Erneut registrieren** aus.
- [Regionsübergreifende Wiederherstellungen](backup-create-rs-vault.md#set-cross-region-restore) für SQL- und SAP HANA-Datenbanksicherungen werden nicht unterstützt, wenn für den Tresor private Endpunkte aktiviert sind.

## <a name="recommended-and-supported-scenarios"></a>Empfohlene und unterstützte Szenarien

Solange private Endpunkte für den Tresor aktiviert sind, werden sie nur für die Sicherung und Wiederherstellung von SQL- und SAP HANA-Workloads in einer Sicherung in einer Azure-VM und mit dem MARS-Agent verwendet. Sie können den Tresor auch für die Sicherung anderer Workloads einsetzen (die allerdings keine privaten Endpunkte benötigen). Neben der Sicherung von SQL- und SAP HANA-Workloads und einer Sicherung mit dem MARS-Agent werden private Endpunkte bei einer Azure-VM-Sicherung auch für die Dateiwiederherstellung verwendet. Weitere Informationen finden Sie in der Tabelle unten:

| Sicherung von Workloads in Azure-VM (SQL, SAP HANA), Sicherung mit MARS-Agent | Private Endpunkte werden empfohlen, um eine Sicherung und Wiederherstellung zu ermöglichen, ohne dass Sie IP-Adressen/FQDNs für Azure Backup oder Azure Storage in Ihren virtuellen Netzwerken auf eine Zulassungsliste setzen müssen. Stellen Sie in diesem Szenario sicher, dass VMs, die SQL-Datenbanken hosten, Azure AD-IPs oder FQDNs erreichen können. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure-VM-Sicherung**                                         | Für die VM-Sicherung ist es nicht erforderlich, Zugriff auf IP-Adressen oder FQDNs zu gewähren. Es werden also keine privaten Endpunkte für eine Sicherung und Wiederherstellung von Datenträgern benötigt.  <br><br>   Die Dateiwiederherstellung aus einem Tresor mit privaten Endpunkten wäre jedoch auf virtuelle Netzwerke beschränkt, die einen privaten Endpunkt für den Tresor enthalten. <br><br>    Wenn Sie nicht verwaltete Datenträger mit Zugriffssteuerungslisten (ACLs) verwenden, stellen Sie sicher, dass das Speicherkonto mit den Datenträgern den Zugriff auf **vertrauenswürdige Microsoft-Dienste** erlaubt, wenn eine ACL vorhanden ist. |
| **Azure Files-Sicherung**                                      | Azure Files-Sicherungen werden im lokalen Speicherkonto gespeichert. Es werden daher keine privaten Endpunkte für eine Sicherung und Wiederherstellung benötigt. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Erstellen und Verwenden privater Endpunkte für Azure Backup

Dieser Abschnitt befasst sich mit den Schritten zum Erstellen und Verwenden privater Endpunkte für Azure Backup innerhalb Ihrer virtuellen Netzwerke.

>[!IMPORTANT]
> Sie sollten die in diesem Dokument beschriebenen Schritte unbedingt in der genannten Reihenfolge durchführen. Andernfalls kann dies dazu führen, dass der Tresor nicht mehr mit dem Einsatz privater Endpunkte kompatibel ist und Sie den Vorgang mit einem neuen Tresor neu starten müssen.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

In [diesem Abschnitt](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) erfahren Sie, wie Sie einen Tresor mit dem Azure Resource Manager-Client erstellen. Dadurch wird ein Tresor erstellt, dessen verwaltete Identität bereits aktiviert ist. [Hier](./backup-azure-recovery-services-vault-overview.md) erfahren Sie mehr zu Recovery Services-Tresoren.

## <a name="enable-managed-identity-for-your-vault"></a>Aktivieren der verwalteten Identität für Ihren Tresor

Verwaltete Identitäten ermöglichen dem Tresor, private Endpunkte zu erstellen und zu nutzen. In diesem Abschnitt wird das Aktivieren der verwalteten Identität für Ihren Tresor erläutert.

1. Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Identität**.

    ![Ändern von „Identitätsstatus“ in „Ein“](./media/private-endpoints/identity-status-on.png)

1. Ändern Sie den **Status** in **Ein**, und klicken Sie auf **Speichern**.

1. Es wird eine **Objekt-ID** generiert, die die verwaltete Identität des Tresors darstellt.

    >[!NOTE]
    >Nach der Aktivierung darf die verwaltete Identität **nicht** deaktiviert werden (auch nicht vorübergehend). Die Deaktivierung der verwalteten Identität kann zu inkonsistentem Verhalten führen.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Erteilen von Berechtigungen für den Tresor zum Erstellen erforderlicher privater Endpunkte

Um die erforderlichen privaten Endpunkte für Azure Backup zu erstellen, muss der Tresor (bzw. die verwaltete Identität des Tresors) über Berechtigungen für die folgenden Ressourcengruppen verfügen:

- Die Ressourcengruppe mit dem Ziel-VNET
- Die Ressourcengruppe, in der die privaten Endpunkte erstellt werden sollen
- Die Ressourcengruppe, die die privaten DNS-Zonen enthält, wie detailliert unter [Erstellen privater Endpunkte für Azure Backup](#creating-private-endpoints-for-backup) erläutert

Wir empfehlen, dem Tresor (der verwalteten Identität) die Rolle **Mitwirkender** für diese drei Ressourcengruppen zu gewähren. Die folgenden Schritte beschreiben, wie dies für eine bestimmte Ressourcengruppe erfolgt (was für jede der drei Ressourcengruppen erfolgen muss):

1. Wechseln Sie zur Ressourcengruppe und dann auf der linken Leiste zu **Zugriffssteuerung (IAM)** .
1. Wechseln Sie in **Zugriffssteuerung (IAM)** zu **Rollenzuweisung hinzufügen**.

    ![Hinzufügen einer Rollenzuweisung](./media/private-endpoints/add-role-assignment.png)

1. Wählen Sie im Bereich **Rollenzuweisung hinzufügen** die Option **Mitwirkender** als **Rolle** aus, und verwenden Sie den **Namen** des Tresors als **Prinzipal**. Wählen Sie Ihren Tresor und dann **Speichern** aus, wenn Sie fertig sind.

    ![Wählen von „Rolle“ und „Prinzipal“](./media/private-endpoints/choose-role-and-principal.png)

Informationen zum Verwalten von Berechtigungen auf detaillierterer Ebene finden Sie unter [ Manuelles Erstellen von Rollen und Berechtigungen](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Erstellen und Genehmigen privater Endpunkte für Azure Backup

### <a name="creating-private-endpoints-for-backup"></a>Erstellen privater Endpunkte für Azure Backup

In diesem Abschnitt wird das Erstellen eines privaten Endpunkts für Ihren Tresor beschrieben.

1. Suchen Sie in der Suchleiste nach **Private Link**, und wählen Sie die Option aus. Dadurch gelangen Sie zum Abschnitt **Private Link-Center**.

    ![Suchen nach „Private Link“](./media/private-endpoints/search-for-private-link.png)

1. Klicken Sie auf der linken Navigationsleiste auf **Private Endpunkte**. Sobald Sie sich im Bereich **Private Endpunkte** befinden, wählen Sie **+Hinzufügen** aus, um mit der Erstellung eines privaten Endpunkts für Ihren Tresor zu beginnen.

    ![Hinzufügen eines privaten Endpunkts im Private Link-Center](./media/private-endpoints/add-private-endpoint.png)

1. Sobald Sie sich im Prozess **Privaten Endpunkt erstellen** befinden, müssen Sie Details für die Herstellung der Verbindung mit Ihrem privaten Endpunkt angeben.

    1. **Grundlagen**: Geben Sie die grundlegenden Details für Ihre privaten Endpunkte ein. Die Region muss mit der des Tresors und der Ressource identisch sein.

        ![Grundlegende Informationen eingeben](./media/private-endpoints/basic-details.png)

    1. **Ressource:** Auf dieser Registerkarte müssen Sie die PaaS-Ressource angeben, für die Sie Ihre Verbindung herstellen möchten. Wählen Sie **Microsoft.RecoveryServices/vaults** als Ressourcentyp für Ihr gewünschtes Abonnement. Wählen Sie anschließend den Namen Ihres Recovery Services-Tresors für **Ressource** und **Azure Backup** für **Zielunterressource** aus.

        ![Registerkarte „Ressource“ ausfüllen](./media/private-endpoints/resource-tab.png)

    1. **Konfiguration:** Geben Sie in der Konfiguration das virtuelle Netzwerk und Subnetz an, in dem der private Endpunkt erstellt werden soll. Dies ist das VNET mit der VM. Sie können nach Wunsch den privaten Endpunkt in eine **private DNS-Zone** integrieren. Alternativ können Sie auch Ihren benutzerdefinierten DNS-Server nutzen oder eine private DNS-Zone erstellen.

        ![Ausfüllen der Registerkarte „Konfiguration“](./media/private-endpoints/configuration-tab.png)

        In [diesem Abschnitt](#dns-changes-for-custom-dns-servers) finden Sie Informationen, wenn Sie Ihre benutzerdefinierten DNS-Server verwenden möchten, anstatt eine Integration mit privaten DNS-Zonen in Azure vorzunehmen.  

    1. Optional können Sie für Ihren privaten Endpunkt **Tags** hinzufügen.

    1. Fahren Sie mit **Überprüfen + erstellen** fort, nachdem Sie die Details eingegeben haben. Klicken Sie nach Abschluss der Validierung auf **Erstellen**, um den privaten Endpunkt zu erstellen.

## <a name="approving-private-endpoints"></a>Genehmigen privater Endpunkte

Wenn der Benutzer, der den privaten Endpunkt erstellt, auch der Besitzer des Recovery Services-Tresors ist, wird der zuvor erstellte private Endpunkt automatisch genehmigt. Andernfalls muss der Besitzer des Tresors den privaten Endpunkt genehmigen, bevor er genutzt werden kann. In diesem Abschnitt wird die manuelle Genehmigung privater Endpunkte im Azure-Portal erläutert.

Weitere Informationen zum Genehmigen privater Endpunkte mithilfe des Azure Resource Manager-Clients finden Sie unter [Manuelle Genehmigung privater Endpunkte mithilfe des Azure Resource Manager-Clients](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client).

1. Navigieren Sie in Ihrem Recovery Services-Tresor auf der linken Leiste zu **Verbindungen mit privatem Endpunkt**.
1. Wählen Sie die Verbindung mit dem privaten Endpunkt aus, die Sie genehmigen möchten.
1. Wählen Sie auf der oberen Leiste **Genehmigen** aus. Sie können auch **Ablehnen** oder **Entfernen** auswählen, wenn Sie die Verbindung mit dem Endpunkt ablehnen oder löschen möchten.

    ![Genehmigen privater Endpunkte](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Verwenden privater Endpunkte für Azure Backup

Sobald die privaten Endpunkte, die für den Tresor in Ihrem VNET erstellt wurden, genehmigt sind, können Sie sie für Sicherungen und Wiederherstellungen nutzen.

>[!IMPORTANT]
>Vergewissern Sie sich vor dem Fortfahren, dass Sie alle zuvor im Dokument erwähnten Schritte erfolgreich abgeschlossen haben. Zur Erinnerung: Sie müssen die Schritte in der folgenden Checkliste ausgeführt haben:
>
>1. (Neuen) Recovery Services-Tresor erstellt
>1. Den Tresor für die Nutzung der systemseitig zugewiesenen verwalteten Identität aktiviert
>1. Der verwalteten Identität des Tresors die entsprechenden Berechtigungen zugewiesen
>1. Einen privaten Endpunkt für Ihren Tresor erstellt
>1. Den privaten Endpunkt genehmigt (falls nicht automatisch genehmigt)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Sichern und Wiederherstellen von Workloads in Azure-VM (SQL, SAP HANA)

Nachdem der private Endpunkt erstellt und genehmigt wurde, sind keine weiteren Änderungen auf Clientseite erforderlich, um den privaten Endpunkt zu nutzen. Die gesamte Kommunikation und Datenübertragung aus Ihrem abgesicherten Netzwerk zum Tresor erfolgt über den privaten Endpunkt.
Wenn Sie jedoch private Endpunkte für den Tresor entfernen, nachdem ein Server (SQL/SAP HANA) bei ihm registriert wurde, müssen Sie den Container erneut beim Tresor registrieren. Sie müssen den Schutz für sie nicht aufheben.

### <a name="backup-and-restore-through-mars-agent"></a>Sichern und Wiederherstellen über den Mars-Agent

Wenn Sie den MARS-Agent zur Sicherung Ihrer lokalen Ressourcen verwenden, stellen Sie sicher, dass Ihr lokales Netzwerk (das die zu sichernden Ressourcen enthält) ein Peering mit dem virtuellen Azure-Netzwerk aufweist, das einen privaten Endpunkt für den Tresor enthält, damit Sie ihn nutzen können. Sie können dann mit der Installation des MARS-Agents fortfahren und die Sicherung wie hier beschrieben konfigurieren. Sie müssen jedoch sicherstellen, dass die gesamte Kommunikation für die Sicherung ausschließlich über das Netzwerk mit Peering erfolgt.

Wenn Sie jedoch private Endpunkte für den Tresor entfernen, nachdem ein MARS-Agent bei ihm registriert wurde, müssen Sie den Container erneut beim Tresor registrieren. Sie müssen den Schutz für sie nicht aufheben.

## <a name="additional-topics"></a>Weitere Themen

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Erstellen eines Recovery Services-Tresors mit dem Azure Resource Manager-Client

Sie können den Recovery Services-Tresor erstellen und seine verwaltete Identität mit dem Azure Resource Manager-Client aktivieren (die Aktivierung der verwalteten Identität ist erforderlich, wie wir später sehen werden). Ein Beispiel hierfür finden Sie nachstehend:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Die obige JSON-Datei muss folgenden Inhalt aufweisen:

JSON-Code der Anforderung:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

JSON-Code der Antwort:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Der Tresor, der in diesem Beispiel mit dem Azure Resource Manager-Client erstellt wird, wurde bereits mit einer systemseitig zugewiesenen verwalteten Identität angelegt.

### <a name="managing-permissions-on-resource-groups"></a>Verwalten von Berechtigungen für Ressourcengruppen

Die verwaltete Identität für den Tresor muss in der Ressourcengruppe und im virtuellen Netzwerk, in dem die privaten Endpunkte erstellt werden, über die folgenden Berechtigungen verfügen:

- `Microsoft.Network/privateEndpoints/*` Erforderlich, um CRUD-Vorgänge (Create, Read, Update und Delete [Erstellen, Lesen, Aktualisieren und Löschen]) für private Endpunkte in der Ressourcengruppe auszuführen. Muss der Ressourcengruppe zugewiesen werden.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Erforderlich für das virtuelle Netzwerk, in dem private IP-Adressen mit dem privaten Endpunkt verbunden werden.
- `Microsoft.Network/networkInterfaces/read` Erforderlich für die Ressourcengruppe, um die für den privaten Endpunkt erstellte Netzwerkschnittstelle abzurufen.
- Rolle „Mitwirkender“ für die private DNS-Zone: Ist bereits vorhanden und kann verwendet werden, um die Berechtigungen `Microsoft.Network/privateDnsZones/A/*` und `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` bereitzustellen.

Sie können mithilfe einer der folgenden Methoden Rollen mit den erforderlichen Berechtigungen anlegen:

#### <a name="create-roles-and-permissions-manually"></a>Manuelles Erstellen von Rollen und Berechtigungen

Erstellen Sie die folgenden JSON-Dateien, und legen Sie anschließend mit dem PowerShell-Befehl am Ende des Abschnitts Rollen an:

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Verwenden eines Skripts

1. Starten Sie im Azure-Portal die **Cloud Shell**, und wählen Sie im PowerShell-Fenster **Datei hochladen** aus.

    ![„Datei hochladen“ im PowerShell-Fenster auswählen](./media/private-endpoints/upload-file-in-powershell.png)

1. Laden Sie das folgende Skript hoch: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Wechseln Sie zu Ihrem Stammordner (Beispiel: `cd /home/user`).

1. Führen Sie folgendes Skript aus:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Die Parameter lauten wie folgt:

    - **subscription**: **SubscriptionId mit der Ressourcengruppe, in der der private Endpunkt für den Tresor erstellt werden soll, und dem Subnetz, an das der private Endpunkt des Tresors angefügt werden soll

    - **vaultPEResourceGroup**: Ressourcengruppe, in der die privaten Endpunkte für den Tresor erstellt werden sollen

    - **vaultPESubnetResourceGroup**: Ressourcengruppe des Subnetzes, mit dem der private Endpunkt verknüpft wird

    - **vaultMsiName**: MSI-Name des Tresors, der mit **VaultName** übereinstimmt

1. Vervollständigen Sie die Authentifizierung. Das Skript berücksichtigt den Kontext des oben angegebenen Abonnements. Es erstellt die entsprechenden Rollen, falls sie im Mandanten fehlen, und weist dem MSI des Tresors Rollen zu.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Erstellen privater Endpunkte mit Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automatisch genehmigte private Endpunkte

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Manuelle Genehmigung privater Endpunkte mithilfe des Azure Resource Manager-Clients

1. Rufen Sie mit **GetVault** die Verbindungs-ID für Ihren privaten Endpunkt ab.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Dadurch wird die Verbindungs-ID des privaten Endpunkts zurückgegeben. Der Name der Verbindung kann durch Nutzung des ersten Teils der Verbindungs-ID wie folgt abgerufen werden:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Rufen Sie die **Verbindungs-ID des privaten Endpunkts** (und den **Namen des privaten Endpunkts**, sofern erforderlich) aus der Antwort ab. Ersetzen Sie sie im folgenden JSON-Code und Azure Resource Manager-URI, und versuchen Sie, den Status in „Approved/Rejected/Disconnected“ zu ändern, wie im folgenden Beispiel veranschaulicht:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>DNS-Änderungen für benutzerdefinierte DNS-Server

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Erstellen von DNS-Zonen für benutzerdefinierte DNS-Server

Sie müssen drei private DNS-Zonen erstellen und diese mit Ihrem virtuellen Netzwerk verknüpfen. Beachten Sie, dass die öffentlichen URLs des Sicherungsdiensts im Gegensatz zu Blob und Warteschlange nicht in Azure Public DNS für die Umleitung zu den Private Link-DNS-Zonen registriert werden. 

| **Zone**                                                     | **Service** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Warteschlange       |

>[!NOTE]
>Im obigen Text bezieht sich *geo* auf den Regionscode. Zum Beispiel stehen *wcus* und *ne* für „USA, Westen-Mitte“ und „Europa, Norden“.

Regionscodes finden Sie in [dieser Liste](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx). Unter den folgenden Links finden Sie Informationen zu den URL-Namenskonventionen in nationalen Regionen:

- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Deutschland](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Erstellen von benutzerdefinierten DNS-Einträgen für eine Web-App

Dazu müssen Sie Einträge für jeden FQDN in Ihrem privaten Endpunkt in Ihrer privaten DNS-Zone vornehmen.

Zu beachten ist, dass wir die privaten Endpunkte nutzen werden, die für den Backup-, Blob- und Warteschlangendienst erstellt wurden.

- Der private Endpunkt für den Tresor nutzt den beim Erstellen des privaten Endpunkts angegebenen Namen.
- Den privaten Endpunkten für den Blob- und Warteschlangendienst wird derselbe Name für den Tresor vorangestellt.

Die folgende Abbildung zeigt beispielsweise die drei privaten Endpunkte, die für eine Verbindung mit einem privaten Endpunkt mit dem Namen *pee2epe* erstellt wurden:

![Drei private Endpunkte für eine Verbindung mit einem privaten Endpunkt](./media/private-endpoints/three-private-endpoints.png)

DNS-Zone für den Azure Backup-Dienst (`privatelink.<geo>.backup.windowsazure.com`):

1. Navigieren Sie im **Private Link-Center** zu Ihrem privaten Endpunkt für Azure Backup. Auf der Übersichtsseite sind der FQDN und die privaten IP-Adressen für Ihren privaten Endpunkt aufgelistet.

1. Fügen Sie für jeden FQDN und jede IP-Adresse einen A-Eintrag hinzu.

    ![Eintrag für jeden FQDN und jede private IP-Adresse hinzufügen](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

DNS-Zone für den Blob-Dienst (`privatelink.blob.core.windows.net`):

1. Navigieren Sie im **Private Link-Center** zu Ihrem privaten Endpunkt für Blob. Auf der Übersichtsseite sind der FQDN und die privaten IP-Adressen für Ihren privaten Endpunkt aufgelistet.

1. Fügen Sie für den FQDN und die IP-Adresse einen A-Eintrag hinzu.

    ![Eintrag für den FQDN und die private IP-Adresse als Typ A für den Blob-Dienst hinzufügen](./media/private-endpoints/add-type-a-record-for-blob.png)

DNS-Zone für den Warteschlangendienst (`privatelink.queue.core.windows.net`):

1. Navigieren Sie im **Private Link-Center** zu Ihrem privaten Endpunkt für die Warteschlange. Auf der Übersichtsseite sind der FQDN und die privaten IP-Adressen für Ihren privaten Endpunkt aufgelistet.

1. Fügen Sie für den FQDN und die IP-Adresse einen A-Eintrag hinzu.

    ![Eintrag für den FQDN und die private IP-Adresse als Typ A für den Warteschlangendienst hinzufügen](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Q. Kann ich einen privaten Endpunkt für einen vorhandenen Azure Backup-Tresor erstellen?<br>
A. Nein, private Endpunkte können nur für neue Azure Backup-Tresore erstellt werden. Daher durfte der Tresor zu keiner Zeit Elemente enthalten, die durch ihn geschützt wurden. Vor der Einrichtung privater Endpunkte können keine Versuche erfolgen, Elemente im Tresor zu schützen.

Q. Ich habe versucht, ein Element in meinem Tresor zu schützen, was aber nicht gelang, und der Tresor enthält immer noch keine geschützten Elemente. Kann ich private Endpunkte für diesen Tresor erstellen?<br>
A. Nein, mit dem Tresor darf in der Vergangenheit nicht versucht worden sein, Elemente darin zu schützen.

Q. Ich habe einen Tresor, der private Endpunkte für Sicherung und Wiederherstellung nutzt. Kann ich später private Endpunkte für diesen Tresor hinzufügen oder entfernen, auch wenn ich Sicherungselemente darin geschützt habe?<br>
A. Ja. Wenn Sie bereits private Endpunkte für einen Tresor und geschützte Sicherungselemente darin erstellt haben, können Sie später je nach Bedarf private Endpunkte hinzufügen oder entfernen.

Q. Kann der private Endpunkt für Azure Backup auch für Azure Site Recovery genutzt werden?<br>
A. Nein, der private Endpunkt für Backup kann nur für Azure Backup genutzt werden. Sie müssen einen neuen privaten Endpunkt für Azure Site Recovery erstellen, sofern vom Dienst unterstützt.

Q. Ich habe einen der Schritte in diesem Artikel versäumt und das Schützen meiner Datenquelle fortgesetzt. Kann ich weiterhin private Endpunkte verwenden?<br>
A. Wenn Sie die Schritte im Artikel nicht befolgen und Elemente weiterhin schützen, kann dies dazu führen, dass der Tresor private Endpunkte nicht nutzen kann. Es wird daher empfohlen, sich auf diese Checkliste zu beziehen, bevor Sie mit dem Schützen von Elementen fortfahren.

Q. Kann ich meinen eigenen DNS-Server verwenden, anstatt die private DNS-Zone in Azure oder eine integrierte private DNS-Zone zu nutzen?<br>
A. Ja, Sie können Ihre eigenen DNS-Server verwenden. Stellen Sie jedoch sicher, dass alle erforderlichen DNS-Einträge wie in diesem Abschnitt beschrieben hinzugefügt werden.

Q. Muss ich zusätzliche Schritte auf meinem Server durchführen, nachdem ich den in diesem Artikel beschriebenen Prozess befolgt habe?<br>
A. Nachdem Sie den in diesem Artikel beschriebenen Prozess befolgt haben, müssen Sie keine weiteren Schritte unternehmen, um private Endpunkte für Sicherung und Wiederherstellung zu nutzen.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Sicherheitsfeatures in Azure Backup](security-overview.md) an.
