---
title: Continuous Integration in Azure DevOps Services mit Azure-Ressourcengruppenprojekten | Microsoft-Dokumentation
description: Beschreibt die Einrichtung von Continuous Integration in Azure DevOps Services mithilfe von Bereitstellungsprojekten für Azure-Ressourcengruppen in Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: 692c075b55efd138f6d731ffae43608f141abfdc
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66019771"
---
# <a name="continuous-integration-in-azure-devops-services-using-azure-resource-group-deployment-projects"></a>Continuous Integration in Azure DevOps Services mit Bereitstellungsprojekten für Azure-Ressourcengruppen
Zum Bereitstellen einer Azure-Vorlage führen Sie Aufgaben in verschiedenen Phasen aus: Erstellen, Testen, in Azure kopieren (auch als „Staging“ bezeichnet) und Bereitstellen der Vorlage. Es gibt zwei Möglichkeiten zum Bereitstellen von Vorlagen für Azure DevOps Services. Da beide Verfahren zum gleichen Ergebnis führen, können Sie das Verfahren wählen, das am besten in Ihren Workflow passt.

1. Fügen Sie Ihrer Buildpipeline einen Schritt hinzu, mit dem das PowerShell-Skript ausgeführt wird, das im Bereitstellungsprojekt für die Azure-Ressourcengruppe („Deploy-AzureResourceGroup.ps1“) enthalten ist. Mit dem Skript werden Artefakte kopiert, und anschließend wird die Vorlage bereitgestellt.
2. Fügen Sie mehrere Azure DevOps Services-Buildschritte hinzu, mit denen jeweils eine Stagingaufgabe ausgeführt wird.

In diesem Artikel werden beide Optionen veranschaulicht. Der Vorteil der ersten Option liegt in der Verwendung des Skripts, das von Entwicklern in Visual Studio verwendet wird. Auf diese Weise wird die Konsistenz im gesamten Lebenszyklus sichergestellt. Die zweite Option bietet eine praktische Alternative zum integrierten Skript. Bei beiden Verfahren wird vorausgesetzt, dass Sie bereits ein Visual Studio-Bereitstellungsprojekt in Azure DevOps Services eingecheckt haben.

[!INCLUDE [updated-for-az](../includes/updated-for-az.md)]

## <a name="copy-artifacts-to-azure"></a>Kopieren von Artefakten nach Azure
Unabhängig vom Szenario gilt Folgendes: Falls Sie über Artefakte verfügen, die für die Vorlagenbereitstellung benötigt werden, müssen Sie Azure Resource Manager Zugriff darauf gewähren. Diese Artefakte können folgende Dateien umfassen:

* Geschachtelte Vorlagen
* Konfigurationsskripts und DSC-Skripts
* Anwendungsbinärdateien

### <a name="nested-templates-and-configuration-scripts"></a>Geschachtelte Vorlagen und Konfigurationsskripts
Wenn Sie von Visual Studio bereitgestellte (oder mit Visual Studio-Codeausschnitten erstellte) Vorlagen verwenden, werden die Artefakte vom PowerShell-Skript nicht nur bereitgestellt. Es wird außerdem der URI für die Ressourcen für unterschiedliche Bereitstellungen parametrisiert. Anschließend kopiert das Skript die Artefakte in einen sicheren Container in Azure, erstellt ein SAS-Token für diesen Container und übergibt diese Informationen dann an die Vorlagenbereitstellung. Weitere Informationen zu geschachtelten Vorlagen finden Sie unter [Erstellen einer Vorlagenbereitstellung](/previous-versions/azure/reference/dn790564(v=azure.100)).  Bei Verwendung von Aufgaben in Azure DevOps Services müssen Sie die entsprechenden Aufgaben für die Vorlagenbereitstellung auswählen und gegebenenfalls Parameterwerte aus dem Stagingschritt an die Vorlagenbereitstellung übergeben.

## <a name="set-up-continuous-deployment-in-azure-pipelines"></a>Einrichten von Continuous Deployment in Azure Pipelines
Zum Aufrufen des PowerShell-Skripts in Azure Pipelines müssen Sie Ihre Buildpipeline aktualisieren. Die Schritte sind hier in Kurzform aufgeführt: 

1. Bearbeiten Sie die Buildpipeline.
2. Richten Sie die Azure-Autorisierung in Azure Pipelines ein.
3. Fügen Sie einen Azure PowerShell-Buildschritt hinzu, bei dem auf das PowerShell-Skript im Azure-Ressourcengruppen-Bereitstellungsprojekt verwiesen wird.
4. Legen Sie den Wert des Parameters *-ArtifactsStagingDirectory* fest, um mit einem Projekt, das in Azure Pipelines erstellt wurde, zu arbeiten.

### <a name="detailed-walkthrough-for-option-1"></a>Ausführliche exemplarische Vorgehensweise für Option 1
Hier werden Sie durch die erforderlichen Schritte der Continuous Deployment-Konfiguration in Azure DevOps Services geführt. Hierzu wird eine einzelne Aufgabe verwendet, über die das PowerShell-Skript in Ihrem Projekt ausgeführt wird. 

1. Bearbeiten Sie die Azure DevOps Services-Buildpipeline, und fügen Sie einen Azure PowerShell-Buildschritt hinzu. Wählen Sie die Buildpipeline in der Kategorie **Buildpipelines** und dann den Link **Bearbeiten** aus.
   
   ![Bearbeiten der Buildpipeline][0]
2. Fügen Sie der Buildpipeline einen neuen **Azure PowerShell**-Buildschritt hinzu, und wählen Sie dann die Schaltfläche **Buildschritt hinzufügen** aus .
   
   ![Hinzufügen eines Buildschritts][1]
3. Wählen Sie die Kategorie **Bereitstellungsaufgabe** und dann die Aufgabe **Azure PowerShell**, und klicken Sie auf die Schaltfläche **Hinzufügen**.
   
   ![Hinzufügen von Aufgaben][2]
4. Wählen Sie den Buildschritt **Azure PowerShell** , und geben Sie die Werte ein.
   
   1. Falls Sie Azure DevOps Services bereits einen Azure-Dienstendpunkt hinzugefügt haben, wählen Sie das Abonnement im Dropdown-Listenfeld **Azure-Abonnement** aus, und fahren Sie mit dem nächsten Abschnitt fort. 
      
      Wenn Sie über keinen Azure-Dienstendpunkt in Azure DevOps Services verfügen, müssen Sie einen hinzufügen. In diesem Unterabschnitt werden Sie durch das erforderliche Verfahren geführt. Wenn für Ihr Azure-Konto ein Microsoft-Konto (z.B. Hotmail) verwendet wird, müssen Sie die folgenden Schritte ausführen, um eine Authentifizierung des Dienstprinzipals zu erhalten.

   2. Klicken Sie im Dropdown-Listenfeld **Azure-Abonnement** auf den Link **Verwalten**.
      
      ![Verwalten von Azure-Abonnements][3]
   3. Wählen Sie im Dropdown-Listenfeld **Neuer Dienstendpunkt** die Option **Azure**.
      
      ![Neuer Dienstendpunkt][4]
   4. Wählen Sie im Dialogfeld **Azure-Abonnement hinzufügen** die Option **Dienstprinzipal**.
      
      ![Option „Dienstprinzipal“][5]
   5. Geben Sie Ihre Informationen zum Azure-Abonnement im Dialogfeld **Azure-Abonnement hinzufügen** an. Sie müssen Folgendes angeben:
      
      * Subscription Id (Abonnement-ID)
      * Abonnementname
      * Dienstprinzipal-ID
      * Dienstprinzipalschlüssel
      * Mandanten-ID
   6. Fügen Sie im Namensfeld **Abonnement** einen Namen Ihrer Wahl hinzu. Dieser Wert wird später in der Dropdownliste **Azure-Abonnement** in Azure DevOps Services angezeigt. 

   7. Wenn Sie die ID Ihres Azure-Abonnements nicht kennen, können Sie sie mit einem der folgenden Befehle abrufen.
      
      Verwenden Sie für PowerShell-Skripts den folgenden Befehl:
      
      `Get-AzSubscription`
      
      Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:
      
      `az account show`
   8. Verwenden Sie zum Ermitteln von Dienstprinzipal-ID, Dienstprinzipalschlüssel und Mandanten-ID die Vorgehensweise unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mithilfe des Portals](active-directory/develop/howto-create-service-principal-portal.md) oder [Authentifizieren eines Dienstprinzipals mit dem Azure Resource Manager](active-directory/develop/howto-authenticate-service-principal-powershell.md).
   9. Fügen Sie im Dialogfeld **Azure-Abonnement hinzufügen** die Werte für Dienstprinzipal-ID, Dienstprinzipalschlüssel und Mandanten-ID hinzu, und klicken Sie auf die Schaltfläche **OK**.
      
      Sie verfügen nun über einen gültigen Dienstprinzipal, den Sie zum Ausführen des Azure PowerShell-Skripts verwenden können.
5. Bearbeiten Sie die Buildpipeline, und wählen Sie den Buildschritt **Azure PowerShell** aus. Wählen Sie das Abonnement im Dropdown-Listenfeld **Azure-Abonnement** aus. (Wenn das Abonnement nicht angezeigt wird, wählen Sie neben dem Link **Verwalten** die Schaltfläche **Aktualisieren**.) 
   
   ![Konfigurieren der Azure PowerShell-Buildaufgabe][8]
6. Geben Sie einen Pfad zum PowerShell-Skript „Deploy-AzureResourceGroup.ps1“ an. Wählen Sie hierfür die Schaltfläche mit den Auslassungspunkten (...) neben dem Feld **Skriptpfad**, und navigieren Sie zum PowerShell-Skript „Deploy-AzureResourceGroup.ps1“ im Ordner **Skripts** Ihres Projekts. Wählen Sie ihn aus, und klicken Sie dann auf die Schaltfläche **OK**.    
   
   ![Auswählen des Skriptpfads][9]
7. Aktualisieren Sie nach dem Auswählen des Skripts den Pfad zum Skript so, dass es über „Build.StagingDirectory“ ausgeführt wird (dasselbe Verzeichnis, auf das *ArtifactsLocation* festgelegt ist). Dies erreichen Sie, indem Sie am Anfang des Skriptpfads „$(Build.StagingDirectory)/“ hinzufügen.
   
    ![Bearbeiten des Skriptpfads][10]
8. Geben Sie im Feld **Skriptargumente** die folgenden Parameter ein (in einer Zeile). Wenn Sie das Skript in Visual Studio ausführen, sehen Sie, wie VS die Parameter im **Ausgabefenster** verwendet. Sie können dies als Ausgangspunkt zum Festlegen der Parameterwerte im Buildschritt verwenden.
   
   | Parameter | BESCHREIBUNG |
   | --- | --- |
   | -ResourceGroupLocation |Der Wert für den geografischen Standort, an dem sich die Ressourcengruppe befindet, z.B. **eastus** oder **'USA, Osten'**. (Verwenden Sie einfache Anführungszeichen oben, wenn der Name ein Leerzeichen enthält.) Weitere Informationen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). |
   | -ResourceGroupName |Der Name der Ressourcengruppe, die für diese Bereitstellung verwendet wird. |
   | -UploadArtifacts |Mit diesem Parameter wird (falls vorhanden) angegeben, dass Artefakte aus dem lokalen System in Azure hochgeladen werden müssen. Sie müssen diesen Switch nur festlegen, falls für Ihre Vorlagenbereitstellung zusätzliche Artefakte erforderlich sind, die Sie mit dem PowerShell-Skript bereitstellen möchten (z. B. Konfigurationsskripts oder geschachtelte Vorlagen). |
   | -StorageAccountName |Der Name des Speicherkontos, das zum Bereitstellen von Artefakten für diese Bereitstellung verwendet wird. Dieser Parameter wird nur verwendet, wenn Sie Artefakte zur Bereitstellung nach Azure kopieren. Wenn dieser Parameter angegeben wird, wird ein neues Speicherkonto erstellt, sofern über das Skript nicht bereits eines während einer vorherigen Bereitstellung erstellt wurde. Wenn der Parameter angegeben wird, muss das Speicherkonto bereits vorhanden sein. |
   | -StorageAccountResourceGroupName |Der Name der Ressourcengruppe, die dem Speicherkonto zugeordnet ist. Dieser Parameter ist nur erforderlich, wenn Sie einen Wert für den Parameter „StorageAccountName“ angeben. |
   | -TemplateFile |Der Pfad zur Vorlagendatei im Azure-Ressourcengruppen-Bereitstellungsprojekt. Zur Steigerung der Flexibilität verwenden Sie für diesen Parameter anstelle eines absoluten Pfads einen Pfad, der relativ zum Speicherort des PowerShell-Skripts ist. |
   | -TemplateParametersFile |Gibt den Pfad zur Parameterdatei im Azure-Ressourcengruppen-Bereitstellungsprojekt an. Zur Steigerung der Flexibilität verwenden Sie für diesen Parameter anstelle eines absoluten Pfads einen Pfad, der relativ zum Speicherort des PowerShell-Skripts ist. |
   | -ArtifactStagingDirectory |Mit diesem Parameter können Sie dem PowerShell-Skript den Ordner mitteilen, aus dem die Binärdateien des Projekts kopiert werden sollen. Dieser Wert überschreibt den Standardwert, der vom PowerShell-Skript verwendet wird. Legen Sie den Wert bei Verwendung von Azure DevOps Services wie folgt fest: -ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Hier ist ein Beispiel für Skriptargumente angegeben (Zeile wurde zu besseren Lesbarkeit umgebrochen):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Wenn Sie fertig sind, sollte das Feld **Skriptargumente** etwa wie die folgende Aufstellung aussehen:
   
   ![Skriptargumente][11]
9. Nachdem Sie alle erforderlichen Komponenten für den Azure PowerShell-Buildschritt hinzugefügt haben, wählen Sie die Schaltfläche **Build zur Warteschlange hinzufügen** , um das Projekt zu erstellen. Im **Buildfenster** wird die Ausgabe des PowerShell-Skripts angezeigt.

### <a name="detailed-walkthrough-for-option-2"></a>Ausführliche exemplarische Vorgehensweise für Option 2
Im Folgenden werden Sie durch die erforderlichen Schritte der Continuous Deployment-Konfiguration in Azure DevOps Services geführt. Hierzu werden die integrierten Aufgaben verwendet.

1. Bearbeiten Sie Ihre Azure DevOps Services-Buildpipeline, um zwei neue Buildschritte hinzuzufügen. Wählen Sie die Buildpipeline in der Kategorie **Builddefinitionen** und dann den Link **Bearbeiten** aus.
   
   ![Bearbeiten der Builddefinition][12]
2. Fügen Sie der Buildpipeline die neuen Buildschritte über die Schaltfläche **Buildschritt hinzufügen** hinzu .
   
   ![Hinzufügen eines Buildschritts][13]
3. Wählen Sie die Aufgabenkategorie **Bereitstellen** und dann die Aufgabe **Azure-Dateikopiervorgang** aus, und klicken Sie auf die zugehörige Schaltfläche **Hinzufügen**.
   
   ![Hinzufügen der Aufgabe „Azure-Dateikopiervorgang“][14]
4. Wählen Sie die Aufgabe **Bereitstellung einer Azure-Ressourcengruppe** aus, klicken Sie auf die zugehörige Schaltfläche **Hinzufügen**, und **schließen** Sie dann den **Aufgabenkatalog**.
   
   ![Hinzufügen der Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“][15]
5. Wählen Sie die Aufgabe **Azure-Dateikopiervorgang** aus, und geben Sie die zugehörigen Werte ein.
   
   Falls Sie Azure DevOps Services bereits einen Azure-Dienstendpunkt hinzugefügt haben, wählen Sie das Abonnement im Dropdown-Listenfeld **Azure-Abonnement** aus. Wenn Sie noch kein Abonnement haben, finden Sie unter [Option 1](#detailed-walkthrough-for-option-1) Anweisungen zum Einrichten eines Abonnements in Azure DevOps Services.
   
   * Quelle: Geben Sie **$(Build.StagingDirectory)** ein.
   * Azure-Verbindungstyp: Wählen Sie **Azure Resource Manager** aus.
   * Azure RM-Abonnement: Wählen Sie im Dropdown-Listenfeld **Azure-Abonnement** das Abonnement für das Speicherkonto aus, das Sie verwenden möchten. Wenn das Abonnement nicht angezeigt wird, wählen Sie neben dem Link **Verwalten** die Schaltfläche **Aktualisieren** aus.
   * Zieltyp: Wählen Sie **Azure-Blob** aus.
   * RM-Speicherkonto: Wählen Sie das Speicherkonto aus, das zum Kopieren von Artefakten nach Azure verwendet werden soll.
   * Containername: Geben Sie den Namen des Containers ein, der für den Stagingvorgang verwendet werden soll. Sie können jeden gültigen Container eingeben, sollten jedoch einen Container verwenden, der der Buildpipeline zugewiesen ist.
   
   Für die Ausgabewerte:
   
   * Speichercontainer-URI: Geben Sie **artifactsLocation** ein.
   * SAS-Token des Speichercontainers: Geben Sie **artifactsLocationSasToken** ein.
   
   ![Konfigurieren der Aufgabe „Azure-Dateikopiervorgang“][16]
6. Wählen Sie den Buildschritt **Bereitstellung einer Azure-Ressourcengruppe** aus, und geben Sie dann die zugehörigen Werte ein.
   
   * Azure-Verbindungstyp: Wählen Sie **Azure Resource Manager** aus.
   * Azure RM-Abonnement: Wählen Sie im Dropdown-Listenfeld **Azure-Abonnement** das Abonnement für die Bereitstellung aus. Dies ist in der Regel das gleiche Abonnement, das auch im vorherigen Schritt verwendet wurde.
   * Aktion: Wählen Sie **Ressourcengruppe erstellen oder Ressourcengruppe aktualisieren** aus.
   * Ressourcengruppe: Wählen Sie für die Bereitstellung eine Ressourcengruppe aus, oder geben Sie den Namen einer neuen Ressourcengruppe ein.
   * Speicherort: Wählen Sie den Speicherort für die Ressourcengruppe aus.
   * Vorlage: Geben Sie den Pfad und Namen der bereitzustellenden Vorlage mit vorangestellter Zeichenfolge **$(Build.StagingDirectory)** ein, z.B. **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**.
   * Vorlagenparameter: Geben Sie den Pfad und Namen der zu verwendenden Parameter mit vorangestellter Zeichenfolge **$(Build.StagingDirectory)** ein, z.B. **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**.
   * Vorlagenparameter überschreiben: Geben Sie folgenden Code ein, oder kopieren Sie ihn, und fügen Sie ihn dann ein:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Konfigurieren der Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“][17]
7. Nachdem Sie alle erforderlichen Elemente eingegeben haben, speichern Sie die Buildpipeline und wählen oben **Neuen Build in Warteschlange** aus.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Azure-Ressourcen-Manager und Azure-Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](azure-resource-manager/resource-group-overview.md) .

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
