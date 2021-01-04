---
title: Automatisieren von Azure Analysis Services-Aufgaben mit Dienstprinzipalen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Dienstprinzipal zum Automatisieren von Azure Analysis Services-Verwaltungsaufgaben erstellen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b04b9ababfe0e4c2a60d14044b9d3ee120837dc5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491042"
---
# <a name="automation-with-service-principals"></a>Automatisierung mit Dienstprinzipalen

Dienstprinzipale sind eine Azure Active Directory-Anwendungsressource, die Sie in Ihrem Mandanten erstellen, um unbeaufsichtigte Ressourcen- und Servicelevelvorgänge auszuführen. Es handelt sich dabei um einen eindeutigen Typ von *Benutzeridentität* mit einer Anwendungs-ID und einem Kennwort oder Zertifikat. Ein Dienstprinzipal verfügt nur über die Berechtigungen, die zum Ausführen der Aufgaben erforderlich sind, die durch die ihm zugewiesenen Rollen und Berechtigungen definiert sind. 

In Analysis Services werden Dienstprinzipale mit Azure Automation, PowerShell im unbeaufsichtigten Modus, benutzerdefinierten Clientanwendungen und Web-Apps zum Automatisieren von allgemeinen Aufgaben verwendet. So können beispielsweise Aufgaben wie das Bereitstellen von Servern, das Bereitstellen von Modellen, das Aktualisieren von Daten, das Hoch-/Herunterskalieren sowie Anhalten/Fortsetzen mithilfe von Dienstprinzipalen automatisiert werden. Berechtigungen werden Dienstprinzipalen über Rollenmitgliedschaften zugewiesen, ähnlich wie bei Azure AD-UPN-Standardkonten.

Analysis Services unterstützt außerdem Vorgänge, die von verwalteten Identitäten unter Verwendung von Dienstprinzipalen ausgeführt werden. Weitere Informationen finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) und [Azure-Dienste, die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).    

## <a name="create-service-principals"></a>Erstellen von Dienstprinzipalen
 
Dienstprinzipale können im Azure-Portal oder mithilfe von PowerShell erstellt werden. Weitere Informationen finden Sie unter:

[Erstellen eines Dienstprinzipals – Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Erstellen eines Dienstprinzipals – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Speichern von Anmeldeinformations- und Zertifikatassets in Azure Automation

Anmeldeinformationen und Zertifikate von Dienstprinzipalen können in Azure Automation für Runbook-Vorgänge sicher gespeichert werden. Weitere Informationen finden Sie unter:

[Anmeldeinformationsobjekte in Azure Automation](../automation/shared-resources/credentials.md)   
[Zertifikatobjekte in Azure Automation](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Hinzufügen von Dienstprinzipalen zur Serveradministratorrolle

Bevor Sie einen Dienstprinzipal für Analysis Services-Serververwaltungsvorgänge verwenden können, müssen Sie ihn zur Serveradministratorrolle hinzufügen. Dienstprinzipale müssen der Serveradministratorrolle direkt hinzugefügt werden. Das Hinzufügen eines Dienstprinzipals zu einer Sicherheitsgruppe und das anschließende Hinzufügen dieser Sicherheitsgruppe zur Serveradministratorrolle wird nicht unterstützt. Weitere Informationen finden Sie unter [Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Dienstprinzipale in Verbindungszeichenfolgen

Dienstprinzipal-AppID und ein Kennwort oder Zertifikat können in Verbindungszeichenfolgen ähnlich wie ein UPN verwendet werden.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Verwenden des „Az.AnalysisServices“-Moduls

Wenn Sie einen Dienstprinzipal für Ressourcenverwaltungsvorgänge mit dem [Az.AnalysisServices](/powershell/module/az.analysisservices)-Modul nutzen, verwenden Sie das Cmdlet `Connect-AzAccount`. 

Im folgenden Beispiel werden appID und ein Kennwort verwendet, um Vorgänge auf Steuerungsebene für die Synchronisierung in schreibgeschützte Replikate und für das Hoch-/Aufskalieren durchzuführen:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Verwenden des SqlServer-Moduls

Im folgenden Beispiel werden AppID und ein Kennwort verwendet, um einen Aktualisierungsvorgang für eine Modelldatenbank auszuführen:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO und ADOMD 

Beim Herstellen von Verbindungen mit Clientanwendungen und Web-Apps, unterstützen [AMO- und ADOMD-Clientbibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) und installierbare NuGet-Pakete (Version 15.0.2 und höher) Dienstprinzipale in Verbindungszeichenfolgen mit der folgenden Syntax: `app:AppID` und Kennwort oder `cert:thumbprint`. 

Im folgenden Beispiel werden `appID` und ein `password` verwendet, um einen Aktualisierungsvorgang für eine Modelldatenbank auszuführen:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Nächste Schritte
[Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Aktualisieren mit Logic Apps](analysis-services-refresh-logic-app.md)  
[Aktualisieren mit Azure Automation](analysis-services-refresh-azure-automation.md)  
[Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle](analysis-services-addservprinc-admins.md)  
[Automatisieren von Arbeitsbereichs- und Datasetaufgaben in Power BI Premium mithilfe von Dienstprinzipalen](/power-bi/admin/service-premium-service-principal)