---
title: Migrieren von Azure-Computeressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Computeressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 3662039dad5d85c87c2598fb59b7719ab9251090
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018586"
---
# <a name="migrate-compute-resources-to-global-azure"></a>Migrieren von Computeressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Computeressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="compute-iaas"></a>Compute IaaS

Es ist nicht möglich, Azure Compute-IaaS-Ressourcen (Infrastructure-as-a-Service) direkt von Azure Deutschland zu Azure weltweit zu migrieren. Es gibt jedoch mehrere Möglichkeiten, wie Sie Ihre virtuellen Computer „duplizieren“ können.

### <a name="duplicate-by-using-site-recovery"></a>Duplizieren über Site Recovery

Azure Site Recovery hilft Ihnen bei der Migration Ihrer virtuellen Computer von Azure Deutschland zu Azure weltweit. Da sich die Quelle und das Ziel in verschiedenen Mandanten in einer Migration von Azure Deutschland zu Azure weltweit befinden, können Sie nicht die normale Azure-Notfallwiederherstellungsoption verwenden, die für virtuelle Computer verfügbar ist. Der Trick besteht darin, einen Site Recovery-Tresor in der Zielumgebung (Azure weltweit) einzurichten und dann wie beim Verschieben eines physischen Servers zu Azure vorzugehen. Wählen Sie im Azure-Portal einen Replikationspfad aus, der mit **Nicht virtualisiert** bezeichnet ist. Wenn die Replikation abgeschlossen ist, lösen Sie ein Failover aus.

> [!NOTE]
> Die folgenden Schritte sind dieselben Schritte, die Sie ausführen würden, um einen physischen Server, der lokal ausgeführt wird, zu Azure zu migrieren.

Weitere Informationen hierzu finden Sie in diesem [nützlichen Tutorial zu Site Recovery](../site-recovery/physical-azure-disaster-recovery.md). Für einen schnellen Überblick gibt es hier eine kürzere und leicht angepasste Version des Prozesses:

Installieren Sie einen Konfigurations-/Prozessserver in Ihrer Quellumgebung, um die Server-Images zu erstellen. Replizieren Sie die Images dann in den Azure Recovery Services-Tresor in Ihrer Zielumgebung. Die gesamte Arbeit wird vom Konfigurationsserver erledigt. Sie müssen sich nicht um die einzelnen Servern kümmern.

1. Melden Sie sich am Azure Deutschland-Portal an.
1. Vergleichen Sie die Betriebssystemversionen der zu migrierenden virtuellen Computer mit der [Supportmatrix](../site-recovery/vmware-physical-secondary-support-matrix.md).
1. Richten Sie einen neuen virtuellen Computer in Ihrer Azure Virtual Network-Quellinstanz ein, der Konfigurationsserver als fungieren soll:
   1. Wählen Sie **DS4v3** oder höher (4 bis 8 Kerne, 16 GB Arbeitsspeicher) aus.
   1. Fügen Sie einen zusätzlichen Datenträger an, der mindestens 1 TB verfügbaren Speicherplatz (für die VM-Images) hat.
   1. Verwenden Sie Windows Server 2012 R2 oder höher.
1. Stellen Sie sicher, dass die Ports 443 und 9443 für das Subnetz in beide Richtungen geöffnet sind.
1. Melden Sie sich bei dem neuen virtuellen Computer (ConfigurationServer) an.
1. Melden Sie sich in Ihrer Remotedesktopsitzung am Portal für Azure weltweit an, indem Sie Ihre Anmeldeinformationen für Azure weltweit verwenden.
1. Richten Sie ein virtuelles Netzwerk ein, in dem die replizierten virtuellen Computer ausgeführt werden.
1. Erstellen eines Azure-Speicherkontos.
1. Richten Sie den Recovery Services-Tresor ein.
1. Definieren Sie das **Schutzziel** (**In Azure** > **Nicht virtualisiert/Andere**).
1. Laden Sie die Installationsdatei für das einheitliche Site Recovery-Setup (**Infrastruktur vorbereiten** > **Quelle**) herunter. Wenn Sie die Portal-URL über ConfigurationServer öffnen, wird die Datei auf den richtigen Server heruntergeladen. Laden Sie die Installationsdatei von außerhalb von ConfigurationServer auf ConfigurationServer hoch.
1. Laden Sie den Tresorregistrierungsschlüssel herunter (laden Sie ihn ggf. wie im vorherigen Schritt auf ConfigurationServer hoch).
1. Führen Sie die Installation für das einheitliche Setup von Site Recovery auf ConfigurationServer aus.
1. Richten Sie die Zielumgebung ein (prüfen Sie, ob Sie weiterhin am Zielportal angemeldet sind).
1. Definieren Sie die Replikationsrichtlinie.
1. Starten Sie die Replikation.

Nachdem die Replikation zunächst erfolgreich abgeschlossen wurde, testen Sie das Szenario, indem Sie ein Testfailover durchführen. Überprüfen Sie den Test, und löschen Sie ihn. Im letzten Schritt führen Sie das tatsächliche Failover aus.

> [!CAUTION]
> Es erfolgt keine Rücksynchronisierung mit dem virtuellen Quellcomputer. Wenn Sie erneut migrieren möchten, müssen Sie alles bereinigen und ganz von vorn beginnen!

### <a name="duplicate-by-using-resource-manager-template-exportimport"></a>Duplizieren über Resource Manager-Vorlagenexport/-import

Sie können die Azure Resource Manager-Vorlage exportieren, die Sie zum Bereitstellen auf Ihrem lokalen Computer verwenden. Bearbeiten Sie die Vorlage, um den Standort und andere Parameter oder Variablen zu ändern. Stellen Sie dann erneut in Azure weltweit bereit. 

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Exportieren Sie die Resource Manager-Vorlage im Portal, indem Sie die Ressourcengruppe auswählen. Wählen Sie **Bereitstellungen** aus, und wählen Sie dann die neueste Bereitstellung aus. Wählen Sie im linken Menü die Option **Vorlage** aus, und laden Sie die Vorlage herunter.

Es wird eine ZIP-Datei heruntergeladen, die mehrere Dateien enthält. Die PowerShell-, die Azure CLI-, Ruby- oder .NET-Skripts unterstützen Sie beim Bereitstellen Ihrer Vorlage. Die Datei *parameters.json* enthält alle Eingaben von der letzten Bereitstellung. Es ist wahrscheinlich, dass Sie einige Einstellungen in dieser Datei ändern müssen. Bearbeiten Sie die Datei *template.json*, wenn Sie nur eine Teilmenge der Ressourcen erneut bereitstellen möchten.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Site Recovery](../site-recovery/index.yml) durcharbeiten.
- Lesen Sie Informationen, wie Sie [eine Resource Manager-Vorlage exportieren](../azure-resource-manager/templates/export-template-portal.md), oder die Übersicht zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Erfahren Sie mehr über [Physisch-zu-Azure-Notfallwiederherstellung über Site Recovery](../site-recovery/physical-azure-disaster-recovery.md).
- Lesen Sie die [Übersicht über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie mehr darüber, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="cloud-services"></a>Cloud Services

Sie können Azure Cloud Services erneut bereitstellen, indem Sie die `.cspkg`- und die `.cscfg`-Definition erneut angeben.

### <a name="azure-portal"></a>Azure-Portal

So stellen Sie Clouddienste im Azure-Portal erneut bereit:

1. [Erstellen Sie einen neuen Clouddienst](../cloud-services/cloud-services-how-to-create-deploy-portal.md), indem Sie Ihre `.cspkg`- und Ihre `.cscfg`-Definition verwenden.
1. Aktualisieren Sie den [CNAME- oder A-Eintrag](../cloud-services/cloud-services-custom-domain-name-portal.md) zur Umleitung des Datenverkehrs an den neuen Clouddienst.
1. Wenn Datenverkehr auf den neuen Clouddienst verweist, löschen Sie den alten Clouddienst in Azure Deutschland.

### <a name="powershell"></a>PowerShell

So stellen Sie Clouddienste über PowerShell erneut bereit:

1. [Erstellen Sie einen neuen Clouddienst](/powershell/module/servicemanagement/azure.service/new-azureservice), indem Sie Ihre `.cspkg`- und Ihre `.cscfg`-Definition verwenden.

    ```powershell
    New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <westeurope>
    ```

1. [Erstellen Sie eine neuen Bereitstellung](/powershell/module/servicemanagement/azure.service/new-azuredeployment), indem Sie Ihre `.cspkg`- und Ihre `.cscfg`-Definition verwenden.

    ```powershell
    New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>
    ```

1. Aktualisieren Sie den [CNAME- oder A-Eintrag](../cloud-services/cloud-services-custom-domain-name-portal.md) zur Umleitung des Datenverkehrs an den neuen Clouddienst.
1. Wenn Datenverkehr auf den neuen Clouddienst verweist, [löschen Sie den alten Clouddienst](/powershell/module/servicemanagement/azure.service/remove-azureservice) in Azure Deutschland.

    ```powershell
    Remove-AzureService -ServiceName <yourOldServiceName>
    ```

### <a name="rest-api"></a>REST-API

So stellen Sie Clouddienste über die REST-API erneut bereit:

1. [Erstellen Sie einen neuen Clouddienst](/rest/api/compute/cloudservices/rest-create-cloud-service) in der Zielumgebung.

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices
    ```

1. Erstellen Sie eine neue Bereitstellung mithilfe der [API zum Erstellen einer Bereitstellung](/previous-versions/azure/reference/ee460813(v=azure.100)). Um Ihre `.cspkg`- und Ihre `.cscfg`-Definition zu finden, können Sie die [Get-Package-API](/previous-versions/azure/reference/jj154121(v=azure.100)) aufrufen.

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production
    ```

1. Wenn Datenverkehr auf den neuen Clouddienst verweist, [löschen Sie den alten Clouddienst](/rest/api/compute/cloudservices/rest-delete-cloud-service) in Azure Deutschland.

    ```http
    https://management.core.cloudapi.de/<subscription-id>/services/hostedservices/<old-cloudservice-name>
    ```

Weitere Informationen finden Sie unter:

- Lesen Sie die [Übersicht über Cloud Services](../cloud-services/cloud-services-choose-me.md).

## <a name="service-fabric"></a>Service Fabric

Es ist nicht möglich, Service Fabric-Ressourcen von Azure Deutschland zu Azure weltweit zu migrieren. Sie müssen Service Fabric-Ressourcen in der neuen Umgebung erneut bereitstellen.

Sie können Informationen zu Ihrer aktuellen Service Fabric-Umgebung abrufen, indem Sie PowerShell-Cmdlets verwenden. Greifen Sie auf alle Cmdlets zu, die sich auf Service Fabric beziehen, indem Sie `Get-Help *ServiceFabric*` in PowerShell eingeben.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Service Fabric](../service-fabric/service-fabric-tutorial-create-dotnet-app.md) durcharbeiten.
- Erfahren Sie, wie Sie [einen neuen Cluster erstellen](../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Lesen Sie die [Übersicht über Service Fabric](../service-fabric/service-fabric-overview.md).

## <a name="batch"></a>Batch

Es ist nicht möglich, Azure Batch-Kontodaten aus einer Region in eine andere zu migrieren. Dem Konto können aktuell aktive virtuelle Computer zugeordnet sein, und das Konto kann aktiv mit Daten in Speicherkonten, Datenbanken oder anderen Speichersystemen interagieren.

Stellen Sie Ihre Bereitstellungsskripts, Ihre Vorlagen oder Ihren Code in der neuen Region erneut bereit. Eine erneute Bereitstellung umfasst die folgenden Aufgaben:

1. [Erstellen eines Batch-Kontos](../batch/batch-account-create-portal.md)
1. [Erhöhen Ihres Batch-Kontokontingents](../batch/batch-quota-limit.md)
1. Erstellen von Batch-Pools
1. Erstellen von neuen Speicherkonten, Datenbanken oder anderen Diensten, die zum persistenten Speichern von Eingabe- und Ausgabedaten verwendet werden
1. Aktualisieren Ihrer Konfiguration und Ihres Codes, um auf das neue Batch-Konto zu verweisen und neue Anmeldeinformationen zu verwenden

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Batch](../batch/tutorial-parallel-dotnet.md) durcharbeiten.
- Lesen Sie die [Übersicht über Azure Batch](../batch/batch-technical-overview.md).

## <a name="functions"></a>Functions

Ein Migrieren von Azure Functions-Ressourcen von Azure Deutschland zu Azure weltweit wird zurzeit nicht unterstützt. Die empfohlene Vorgehensweise ist, dass Sie die Resource Manager-Vorlage exportieren, den Standort ändern und dann in der Zielregion erneut bereitstellen.

> [!IMPORTANT]
> Ändern Sie den Standort, Key Vault-Geheimnisse, Zertifikate, App-Einstellungen und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Functions-Tutorials](../azure-functions/index.yml) durcharbeiten.
- Erfahren Sie, wie Sie [eine Resource Manager-Vorlage exportieren](../azure-resource-manager/templates/export-template-portal.md), oder lesen Sie die Übersicht zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Lesen Sie die [Übersicht zu Azure Functions](../azure-functions/functions-overview.md).
- Verschaffen Sie sich einen [Überblick über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="virtual-machine-scale-sets"></a>VM-Skalierungsgruppen

Um VM-Skalierungsgruppen zu Azure weltweit zu migrieren, exportieren Sie die Resource Manager-Vorlage, passen Sie die Vorlage an die neue Umgebung an, und stellen Sie die Gruppen dann in der Zielregion erneut bereit. Exportieren Sie nur die Basisvorlage, und stellen Sie die Vorlage in der neuen Umgebung erneut bereit. Einzelne VM-Skalierungsgruppeninstanzen müssen alle identisch sein.

> [!IMPORTANT]
> Ändern Sie den Standort, Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu VM-Skalierungsgruppeen](../virtual-machine-scale-sets/tutorial-create-and-manage-cli.md) durcharbeiten.
- Erfahren Sie, wie Sie eine [Azure Resource Manager-Vorlage exportieren](../azure-resource-manager/templates/export-template-portal.md).
- Lesen Sie die [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Lesen Sie eine Übersicht zu [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md).
- Lesen Sie die [Übersicht über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="web-apps"></a>Web-Apps

Derzeit können Apps, die Sie mit dem Web Apps-Feature von Azure App Service erstellt haben, nicht von Azure Deutschland zu Azure weltweit migriert werden. Die empfohlene Vorgehensweise ist, dass Sie eine Web-App als Resource Manager-Vorlage exportieren und diese dann erneut bereitstellen, nachdem Sie die Standorteigenschaft in die neue Region geändert haben.

> [!IMPORTANT]
> Ändern Sie den Standort, Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu App Service](../app-service/tutorial-dotnetcore-sqldb-app.md) durcharbeiten.
- Erfahren Sie, wie Sie [eine Resource Manager-Vorlage exportieren](../azure-resource-manager/templates/export-template-portal.md), oder lesen Sie die Übersicht zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Lesen Sie die [Übersicht über Azure App Service](../app-service/overview.md).
- Lesen Sie die [Übersicht über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)