---
title: Integrieren von Protokollen aus Azure Key Vault mithilfe von Event Hubs | Microsoft-Dokumentation
description: Dieses Tutorial zeigt die nötigen Schritte, um Key Vault-Protokolle mithilfe der Azure-Protokollintegration für SIEM verfügbar zu machen
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 5614cc6fa01ddd10d670fdf429051a8e024550fc
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298199"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial zu Azure Log Integration: Verarbeiten von Azure Key Vault-Ereignissen mithilfe von Event Hubs

>[!IMPORTANT]
> Das Feature Azure Log Integration gilt ab dem 15.06.2019 als veraltet. AzLog-Downloads wurden am 27. Juni 2018 deaktiviert. Um Unterstützung bei der künftigen Vorgehensweise zu erhalten, lesen Sie den Beitrag [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Verwenden von Azure Monitor für die Integration mit SIEM-Tools). 

Sie können die Azure-Protokollintegration verwenden, um protokollierte Ereignisse abzurufen und sie Ihrem Sicherheits- und Ereignisverwaltungssystem (SIEM) zur Verfügung zu stellen. Dieses Tutorial veranschaulicht anhand eines Beispiels, wie Sie über Azure Event Hubs bezogene Protokolle mithilfe der Azure-Protokollintegration verarbeiten können.

Die bevorzugte Methode für die Integration von Azure-Protokollen ist der Azure Monitor-Connector Ihres SIEM-Anbieters unter Verwendung dieser [Anweisungen](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Wenn jedoch der SIEM-Anbieter keinen Connector für Azure Monitor anbietet, können Sie Azure Log Integration möglicherweise als eine temporäre Lösung verwenden (sofern Ihr SIEM von Azure Log Integration unterstützt wird), bis ein solcher Connector verfügbar ist.

 
Führen Sie die einzelnen Schritte des Beispiels in diesem Tutorial aus, um zu erfahren, wie die Azure-Protokollintegration und Event Hubs zusammenarbeiten und wie der jeweilige Schritt die Lösung unterstützt. Auf der Grundlage dieser Erkenntnisse können Sie dann eigene Schritte entwickeln, die die speziellen Anforderungen Ihres Unternehmens unterstützen.

> [!WARNING]
> Die Schritte und Befehle in diesem Tutorial sind zum Kopieren und Einfügen vorgesehen. Sie sind lediglich Beispiele. Übernehmen Sie die PowerShell-Befehle nicht unverändert in Ihre Liveumgebung. Sie müssen sie an Ihre spezielle Umgebung anpassen.


Dieses Tutorial führt Sie durch den Prozess, die Azure Key Vault-Aktivitäten, die in einem Event Hub protokolliert sind, für Ihr SIEM-System als JSON-Dateien verfügbar zu machen. Sie können dann Ihr SIEM-System so konfigurieren, dass es die JSON-Dateien verarbeiten kann.

>[!NOTE]
>Die meisten Schritte in diesem Tutorial beinhalten die Konfiguration von Schlüsseltresoren, Speicherkonten und Event Hubs. Die entsprechenden Schritte für die Azure-Protokollintegration befinden sich am Ende dieses Tutorials. Führen Sie diese Schritte nicht in einer Produktionsumgebung aus. Sie sind ausschließlich für eine Laborumgebung bestimmt. Vor der Verwendung in einer Produktionsumgebung müssen Sie die Schritte entsprechend anpassen.

Die Informationen, die Ihnen währenddessen bereitgestellt werden, helfen Ihnen dabei, die Gründe für jeden Schritt nachzuvollziehen. Links zu anderen Artikeln liefern Ihnen weitere Details zu bestimmten Themen.

Weitere Informationen zu den Diensten, die in diesem Tutorial verwendet werden, finden Sie hier: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure-Protokollintegration](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Anfangssetup

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Ein Azure-Abonnement und ein zum Abonnement gehöriges Konto mit Administratorrechten. Falls Sie über kein Abonnement verfügen, können Sie [ein kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen.
 
* Ein System mit Zugriff auf das Internet, das die Voraussetzungen für die Installation der Azure-Protokollintegration erfüllt. Das System kann sich auf einem Clouddienst befinden oder lokal gehostet sein.

* Azure-Protokollintegration installiert. So installieren Sie sie:

   a. Stellen Sie über Remotedesktop eine Verbindung zu dem in Schritt 2 erwähnten System her.   
   b. Kopieren Sie das Installationsprogramm für die Azure-Protokollintegration auf das System. c. Starten Sie das Installationsprogramm, und akzeptieren Sie die Microsoft-Software-Lizenzbedingungen.

* Wenn Sie Telemetriedaten bereitstellen möchten, lassen Sie das Kontrollkästchen aktiviert. Wenn Sie keine Nutzungsinformationen an Microsoft senden möchten, deaktivieren Sie das Kontrollkästchen.

   Weitere Informationen zur Azure-Protokollintegration und zur Installation finden Sie unter [Azure-Protokollintegration mit Azure-Diagnoseprotokollierung und Windows-Ereignisweiterleitung](security-azure-log-integration-get-started.md).

* Die neueste Version von PowerShell

   Wenn Sie Windows Server 2016 installiert haben, haben Sie mindestens PowerShell 5.0. Wenn Sie eine andere Version von Windows Server verwenden, haben Sie möglicherweise eine frühere Version von PowerShell installiert. Sie können die Version überprüfen, indem Sie in einem PowerShell-Fenster ```get-host``` eingeben. Wenn Sie nicht PowerShell 5.0 installiert haben, können Sie [es herunterladen](https://www.microsoft.com/download/details.aspx?id=50395).

   Sobald Sie mindestens über PowerShell 5.0 verfügen, können Sie die neueste Version installieren, indem Sie die Anweisungen unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps) befolgen.


## <a name="create-supporting-infrastructure-elements"></a>Erstellen der unterstützenden Infrastrukturelemente

1. Öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und navigieren Sie zu **C:\Programme\Microsoft Azure Log Integration**.
1. Importieren Sie die AzLog-Cmdlets, indem Sie das Script „LoadAzLogModule.ps1“ ausführen. Geben Sie den Befehl `.\LoadAzLogModule.ps1` ein. (Beachten Sie das „.\"“ in diesem Befehl.) Die Ausgabe sollte folgendermaßen aussehen:</br>

   ![Liste der geladenen Module](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Geben Sie den Befehl `Connect-AzAccount` ein. Geben Sie im Anmeldefenster die Anmeldeinformationen für das Abonnement ein, das Sie für dieses Tutorial verwenden.

   >[!NOTE]
   >Wenn Sie sich von diesem Computer aus zum ersten Mal bei Azure anmelden, wird Ihnen eine Meldung angezeigt, um Microsoft zu gestatten, Ihre PowerShell-Nutzungsdaten zu sammeln. Es wird empfohlen, die Sammlung der Daten zu aktivieren, da diese zur Verbesserung von Azure PowerShell verwendet werden.

1. Nach erfolgreicher Authentifizierung werden Sie angemeldet. Notieren Sie sich die Abonnement-ID und den Abonnementnamen, da Sie diese benötigen, um spätere Schritte abzuschließen.

1. Erstellen Sie Variablen zum Speichern von Werten, die später verwendet werden. Geben Sie jede der folgenden PowerShell-Zeilen ein. Möglicherweise müssen Sie die Werte entsprechend Ihrer Umgebung anpassen.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (Ihr Abonnementname lautet möglicherweise anders. Er wird in der Ausgabe des vorherigen Befehls angezeigt.)
    - ```$location = 'West US'``` (Diese Variable dient zum Übergeben des gewünschten Erstellungsorts für Ihre Ressourcen. Sie können sie auf einen beliebigen anderen Standort festlegen.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (Der Name ist frei wählbar, sollte jedoch ausschließlich aus Kleinbuchstaben und Zahlen bestehen.)
    - ```$storageName = $name``` (Diese Variable wird für den Speicherkontonamen verwendet.)
    - ```$rgname = $name``` (Diese Variable wird für den Ressourcengruppennamen verwendet.)
    - ```$eventHubNameSpaceName = $name``` (Dies ist der Name des Event Hub-Namespace.)
1. Geben Sie das Abonnement an, das Sie verwenden möchten:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Erstellen Sie eine Ressourcengruppe:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Wenn Sie an diesem Punkt `$rg` eingeben, sollte die Ausgabe in etwa wie im folgenden Screenshot aussehen:

   ![Ausgabe nach der Erstellung einer Ressourcengruppe](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Erstellen Sie ein Speicherkonto, das verwendet wird, um Statusinformationen zu verfolgen:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Erstellen Sie den Event Hub-Namespace. Dieser ist erforderlich, um einen Event Hub zu erstellen.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Rufen Sie die Regel-ID ab, die für den Insights-Anbieter verwendet wird:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Rufen Sie alle möglichen Azure-Standorte ab und fügen Sie deren Namen zu einer Variable hinzu, die in einem späteren Schritt verwendet werden kann:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Wenn Sie an diesem Punkt `$locations` eingeben, werden die Standortnamen ohne die zusätzlichen Informationen angezeigt, die von „Get-AzLocation“ zurückgegeben werden.
1. Erstellen eines Azure Resource Manager-Protokollprofils: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Weitere Informationen zu Azure-Protokollprofilen finden Sie unter [Overview of the Azure Activity Log (Übersicht über das Azure-Aktivitätsprotokoll)](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Möglicherweise erhalten Sie eine Fehlermeldung, wenn Sie versuchen, ein Protokollprofil zu erstellen. Konsultieren Sie in diesem Fall die Dokumentation zu „Get-AzLogProfile“ und „Remove-AzLogProfile“. Wenn Sie „Get-AzLogProfile“ ausführen, sehen Sie die Informationen zum Protokollprofil. Sie können das vorhandene Protokollprofil löschen, indem Sie den Befehl ```Remove-AzLogProfile -name 'Log Profile Name'``` eingeben.
>
>![Fehler beim Profil des Ressourcen-Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

1. Erstellen des Schlüsseltresors:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Konfigurieren der Protokollierung für den Schlüsseltresor:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Generieren der Protokollaktivität

Anforderungen müssen an Key Vault gesendet werden, um Protokollaktivität zu generieren. Aktionen wie z.B. das Generieren eines Schlüssels und das Speichern oder Lesen von Geheimnissen aus Key Vault erstellen Protokolleinträge.

1. Anzeigen der aktuellen Speicherschlüssel:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generieren Sie einen neuen **key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Lassen Sie den Schlüssel erneut anzeigen und Sie sehen, dass **key2** einen anderen Wert enthält:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Legen Sie ein Geheimnis fest und lassen Sie es lesen, um weitere Protokolleinträge zu erzeugen:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Zurückgegebenes Geheimnis](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurieren der Azure-Protokollintegration

Nachdem Sie nun alle erforderlichen Elemente konfiguriert haben, damit Key Vault in einen Event Hub protokolliert, müssen Sie die Azure-Protokollintegration konfigurieren:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Führen Sie den AzLog-Befehl für jeden Event Hub aus:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Etwa eine Minute, nachdem Sie die letzten beiden Befehle ausgeführt haben, sollten Sie sehen, wie die JSON-Dateien erzeugt werden. Sie können das bestätigen, indem Sie das Verzeichnis **C:\users\AzLog\EventHubJson** überwachen.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Log Integration FAQ (Azure-Protokollintegration (FAQ))](security-azure-log-integration-faq.md)
- [Get started with Azure Log Integration (Erste Schritte mit der Azure-Protokollintegration)](security-azure-log-integration-get-started.md)
- [Integrate logs from Azure resources into your SIEM systems (Integrieren von Protokollen aus Azure-Ressourcen in Ihre SIEM-Systeme)](security-azure-log-integration-overview.md)
