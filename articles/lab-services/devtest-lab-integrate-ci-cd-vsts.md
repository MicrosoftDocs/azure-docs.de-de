---
title: Integrieren von Azure DevTest Labs in Ihre Azure Pipelines für Continuous Integration und Continuous Delivery | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure DevTest Labs in Ihre Azure Pipelines für Continuous Integration und Continuous Delivery integrieren
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 7806599c1a2f1396ff4b07d6f0538057654029d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66157126"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-devops-continuous-integration-and-delivery-pipeline"></a>Integrieren von Azure DevTest Labs in Ihre Azure DevOps-Pipeline für Continuous Integration und Continuous Delivery
Sie können die in Azure DevOps installierte Erweiterung *Azure DevTest Labs Tasks* für eine einfache Integration von Azure DevTest Labs in Ihre CI/CD-Build- und -Releasepipeline nutzen. Die Erweiterung installiert drei Aufgaben: 
* Erstellen einer VM
* Erstellen eines benutzerdefinierten Images von einem virtuellen Computer
* Löschen eines virtuellen Computers 

Der Prozess ermöglicht es beispielsweise, schnell ein „Golden Image“ für eine bestimmte Testaufgabe bereitzustellen und diesen nach Abschluss des Tests wieder zu löschen.

In diesem Artikel wird gezeigt, wie Sie mithilfe einer abgeschlossenen Pipeline eine VM erstellen und bereitstellen, ein benutzerdefiniertes Image erstellen und anschließend die VM löschen. Normalerweise würden Sie jede Aufgabe einzeln in Ihrer eigenen benutzerdefinierten Pipeline für Builderstellung, Test und Bereitstellung ausführen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Bevor Sie Ihre CI/CD-Pipeline in Azure DevTest Labs integrieren, müssen Sie die Erweiterung über den Visual Studio Marketplace installieren.
1. Wechseln Sie zu [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Wählen Sie **Installieren** aus.
1. Schließen Sie den Assistenten ab.

## <a name="create-a-resource-manager-template"></a>Erstellen einer Resource Manager-Vorlage
In diesem Abschnitt wird beschrieben, wie Sie die Azure Resource Manager-Vorlage erstellen, mit deren Hilfe Sie bei Bedarf eine Azure-VM erstellen.

1. Um eine Resource Manager-Vorlage in Ihrem Abonnement zu erstellen, führen Sie das Verfahren unter [Verwenden einer Resource Manager-Vorlage](devtest-lab-use-resource-manager-template.md) aus.
1. Bevor Sie die Resource Manager-Vorlage generieren, fügen Sie beim Erstellen der VM das [WinRM-Artefakt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) hinzu.

   Der WinRM-Zugriff ist für Bereitstellungsaufgaben wie z.B. *Azure-Dateikopiervorgang* und *PowerShell auf Zielcomputern* erforderlich.

   > [!NOTE]
   > Wenn Sie WinRM mit einer freigegebenen IP-Adresse verwenden, müssen Sie eine NAT-Regel hinzufügen, mit der dem WinRM-Port ein externer Port zugeordnet wird. Dieser Schritt ist nicht erforderlich, wenn Sie die VM mit einer öffentlichen IP-Adresse erstellen.
   >
   >

1. Speichern Sie die Vorlage als Datei auf Ihrem Computer. Geben Sie der Datei den Namen **CreateVMTemplate.json**.
1. Checken Sie die Vorlage in Ihr Quellcodeverwaltungssystem ein.
1. Öffnen Sie einen Text-Editor, und fügen Sie das folgende Skript ein.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Checken Sie das Skript in Ihr Quellcodeverwaltungssystem ein. Geben Sie dem Skript einen Namen wie z.B. **GetLabVMParams.ps1**.

   Wenn Sie dieses Skript im Rahmen der Releasepipeline auf dem Agent ausführen und Aufgabenschritte wie z.B. *Azure-Dateikopiervorgang* und *PowerShell auf Zielcomputern* verwenden, erfasst das Skript die Werte, die Sie zum Bereitstellen Ihrer App auf dem virtuellen Computer benötigen. Normalerweise würden Sie diese Aufgaben zum Bereitstellen von Apps in einer Azure-VM verwenden. Die Aufgaben benötigen Werte wie z.B. den Namen der VM-Ressourcengruppe, die IP-Adresse und den vollqualifizierten Domänennamen (FQDN).

## <a name="create-a-release-pipeline-in-release-management"></a>Erstellen einer Releasepipeline in der Releaseverwaltung
So erstellen Sie die Releasepipeline

1. Wählen Sie im Hub **Build und Release** auf der Registerkarte **Releases** die Schaltfläche mit dem Pluszeichen (+) aus.
1. Wählen Sie im Fenster **Releasedefinition erstellen** die Vorlage **Leer** aus, und klicken Sie dann auf **Weiter**.
1. Wählen Sie **Später auswählen** und dann **Erstellen** aus, um eine neue Releasepipeline mit einer Standardumgebung und ohne verknüpfte Artefakte zu erstellen.
1. Um das Kontextmenü zu öffnen, wählen Sie in der neuen Releasepipeline die Auslassungspunkte (...) neben dem Umgebungsnamen und dann **Variablen konfigurieren** aus. 
1. Geben Sie im Fenster **Konfigurieren – Umgebung** die folgenden Werte für die Variablen ein, die Sie in den Releasepipelineaufgaben verwenden:

   a. Geben Sie für **vmName** den Namen ein, den Sie der VM zugewiesen haben, als Sie die Resource Manager-Vorlage im Azure-Portal erstellt haben.

   b. Geben Sie für **userName** den Benutzernamen ein, den Sie der VM zugewiesen haben, als Sie die Resource Manager-Vorlage im Azure-Portal erstellt haben.

   c. Geben Sie für **password** das Kennwort ein, das Sie der VM zugewiesen haben, als Sie die Resource Manager-Vorlage im Azure-Portal erstellt haben. Verwenden Sie das Schlosssymbol, um das Kennwort auszublenden und zu schützen.

### <a name="create-a-vm"></a>Erstellen einer VM

Der nächste Schritt bei der Bereitstellung besteht darin, die VM zu erstellen, die als „Golden Image“ für nachfolgende Bereitstellungen verwendet werden soll. Sie erstellen die VM in Ihrer Azure DevTest Labs-Instanz, indem Sie die speziell für diesen Zweck entwickelte Aufgabe verwenden. 

1. Wählen Sie in der Releasepipeline die Option **Aufgaben hinzufügen** aus.
1. Fügen Sie auf der Registerkarte **Bereitstellen** die Aufgabe *Azure DevTest Labs – VM erstellen* hinzu. Konfigurieren Sie die Aufgabe wie folgt:

   > [!NOTE]
   > Um die VM für nachfolgende Bereitstellungen zu erstellen, wechseln Sie zu [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Wählen Sie als **Lab-Name** den Namen der Instanz aus, die Sie zuvor erstellt haben.

   c. Geben Sie als **Vorlagenname** den vollständigen Pfad und Namen der Vorlagendatei ein, die Sie in Ihrem Quellcoderepository gespeichert haben. Sie können die integrierten Eigenschaften der Releaseverwaltung verwenden, um den Pfad zu vereinfachen. Beispiel:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Geben Sie als **Vorlagenparameter** die Parameter für die Variablen ein, die in der Vorlage definiert sind. Verwenden Sie die Namen der Variablen, die Sie in der Umgebung definiert haben, beispielsweise:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Geben Sie unter **Ausgabevariablen – Lab-VM-ID** die ID der neu erstellten VM für nachfolgende Schritte an. Sie legen den Standardnamen der Umgebungsvariable fest, die im Abschnitt **Ausgabevariablen** automatisch mit dieser ID aufgefüllt wird. Sie können die Variable bei Bedarf bearbeiten, aber denken Sie daran, in nachfolgenden Aufgaben den richtigen Namen zu verwenden. Die Lab-VM-ID weist das folgende Format auf:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

1. Führen Sie das zuvor erstellte Skript aus, um die Details der DevTest Labs-VM zu erfassen. 
1. Wählen Sie in der Releasepipeline die Option **Aufgaben hinzufügen** aus, und fügen Sie dann auf der Registerkarte **Bereitstellen** eine *Azure PowerShell*-Aufgabe hinzu. Konfigurieren Sie die Aufgabe wie folgt:

   > [!NOTE]
   > Um die Details der DevTest Labs-VM zu erfassen, lesen Sie [Deploy: Azure PowerShell (Bereitstellen: Azure PowerShell)](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzurePowerShellV3), und führen Sie das Skript aus.

   a. Wählen Sie für **Azure-Verbindungstyp** die Option **Azure Resource Manager** aus.

   b. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   c. Wählen Sie als **Skripttyp** die Option **Skriptdatei** aus.
 
   d. Geben Sie als **Skriptpfad** den vollständigen Pfad und Namen des Skripts ein, das Sie in Ihrem Quellcoderepository gespeichert haben. Sie können die integrierten Eigenschaften der Releaseverwaltung verwenden, um den Pfad zu vereinfachen. Beispiel:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Geben Sie in **Skriptargumente** den Namen der Umgebungsvariable ein, die durch die vorherige Aufgabe automatisch mit der ID der Lab-VM aufgefüllt wurde. Beispiel: 
      ```
      -labVmId '$(labVMId)'
      ```
    Das Skript erfasst die erforderlichen Werte und speichert sie in Umgebungsvariablen innerhalb der Releasepipeline, sodass Sie in nachfolgenden Schritten einfach auf die Werte verweisen können.

1. Stellen Sie Ihre App in der neuen DevTest Labs-VM bereit. Die Aufgaben, die Sie normalerweise zum Bereitstellen der App verwenden, sind *Azure-Dateikopiervorgang* und *PowerShell auf Zielcomputern*.
   Die Informationen zum virtuellen Computer, die Sie für die Parameter dieser Aufgaben benötigen, werden in drei Konfigurationsvariablen innerhalb der Releasepipeline gespeichert: **labVmRgName**, **labVMIpAddress** und **labVMFqdn**. Wenn Sie nur mit der Erstellung einer DevTest Labs-VM und einem benutzerdefinierten Image experimentieren möchten, ohne eine App darin bereitzustellen, können Sie diesen Schritt überspringen.

### <a name="create-an-image"></a>Erstellen eines Images

Der nächste Schritt besteht darin, ein Image der neu bereitgestellten VM in Ihrer Azure DevTest Labs-Instanz zu erstellen. Sie können das Image anschließend dazu verwenden, um bei Bedarf Kopien der VM zu erstellen, wenn Sie eine Entwicklungsaufgabe ausführen oder einige Tests durchführen möchten. 

1. Wählen Sie in der Releasepipeline die Option **Aufgaben hinzufügen** aus.
1. Fügen Sie auf der Registerkarte **Bereitstellen** die Aufgabe **Azure DevTest Labs – Benutzerdefiniertes Image erstellen** hinzu. Konfigurieren Sie ihn wie folgt:

   > [!NOTE]
   > Um das Image zu erstellen, wechseln Sie zu [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Wählen Sie als **Lab-Name** den Namen der Instanz aus, die Sie zuvor erstellt haben.

   c. Geben Sie in **Name des benutzerdefinierten Images** einen Namen für das benutzerdefinierte Image ein.

   d. (Optional) Geben Sie unter **Beschreibung** eine Beschreibung ein, um die spätere Auswahl des richtigen Images zu erleichtern.

   e. Wenn Sie den Standardnamen der Umgebungsvariable geändert haben, die durch eine vorherige Aufgabe automatisch mit der ID der Lab-VM aufgefüllt wurde, bearbeiten Sie den Wert für **Quell-Lab-VM – ID der Quell-Lab-VM**. Der Standardwert lautet **$(labVMId)**.

   f. Für **Ausgabevariablen – ID des benutzerdefinierten Images** benötigen Sie die ID des neu erstellten Images, wenn sie dieses verwalten oder löschen möchten. Der Standardname der Umgebungsvariable, die automatisch mit dieser ID aufgefüllt wird, wird im Abschnitt **Ausgabevariablen** festgelegt. Sie können die Variable bei Bedarf bearbeiten.

### <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Der letzte Schritt besteht darin, die VM zu löschen, die Sie in Ihrer Azure DevTest Labs-Instanz bereitgestellt haben. Normalerweise würden Sie die VM löschen, nachdem Sie die erforderlichen Entwicklungsaufgaben oder Tests auf der bereitgestellten VM ausgeführt haben. 

1. Wählen Sie in der Releasepipeline die Option **Aufgaben hinzufügen** aus, und fügen Sie dann auf der Registerkarte **Bereitstellen** die Aufgabe *Azure DevTest Labs – VM löschen* hinzu. Konfigurieren Sie ihn wie folgt:

      > [!NOTE]
      > Um die VM zu löschen, wechseln Sie zu [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).
 
   b. Wenn Sie den Standardnamen der Umgebungsvariable geändert haben, die durch eine vorherige Aufgabe automatisch mit der ID der Lab-VM aufgefüllt wurde, bearbeiten Sie den Wert für **Lab-VM-ID**. Der Standardwert lautet **$(labVMId)**.

1. Geben Sie einen Namen für die Releasepipeline ein, und speichern Sie sie.
1. Erstellen Sie ein neues Release, wählen Sie den aktuellen Build aus, und stellen Sie ihn in der Einzelumgebung in der Pipeline bereit.

Aktualisieren Sie in jeder Phase die Anzeige Ihrer DevTest Labs-Instanz im Azure-Portal, um die erstellte VM und das erstellte Image anzuzeigen und sich zu vergewissern, dass die VM wieder gelöscht wird.

Sie können jetzt das benutzerdefinierte Image verwenden, um bei Bedarf VMs zu erstellen.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über das [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md).
* Sehen Sie sich im [öffentlichen DevTest Labs-GitHub-Repository](https://github.com/Azure/azure-quickstart-templates) weitere Resource Manager-Schnellstartvorlagen für die DevTest Labs-Automatisierung an.
* Sehen Sie sich bei Bedarf die Seite [Azure DevOps-Problembehandlung](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) an.
 
