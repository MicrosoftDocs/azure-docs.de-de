---
title: Automatisieren der Bereitstellung einer VM in Amazon Web Services
description: In diesem Artikel wird gezeigt, wie Sie Azure Automation nutzen, um das Erstellen einer Amazon Web Service-VM zu automatisieren
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 348c28f6a2d72048e34f117e802abf243597b458
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737567"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-Szenario – Bereitstellen eines virtuellen AWS-Computers
In diesem Artikel erfahren Sie, wie Sie Azure Automation zur Bereitstellung eines virtuellen Computers in Ihrem AWS-Abonnement (Amazon Web Services) nutzen und für diesen virtuellen Computer einen bestimmten Namen festlegen können – ein Vorgang, der in AWS als „Tagging“ des virtuellen Computers bezeichnet wird.

## <a name="prerequisites"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben ausführen zu können, benötigen Sie ein Azure Automation-Konto und ein AWS-Abonnement. Weitere Informationen zum Einrichten eines Azure Automation-Kontos sowie zum Konfigurieren dieses Kontos mit den Anmeldeinformationen Ihres AWS-Abonnements finden Sie unter [Authentifizieren mit Amazon Web Services konfigurieren](automation-config-aws-account.md). Da in den nachfolgenden Schritten auf dieses Konto verwiesen wird, sollte dieses Konto mit den Anmeldeinformationen Ihres AWS-Abonnements erstellt oder aktualisiert werden, bevor Sie fortfahren.

## <a name="deploy-amazon-web-services-powershell-module"></a>Bereitstellen des Amazon Web Services-PowerShell-Moduls
Ihr Runbook für die VM-Bereitstellung nutzt das AWS-PowerShell-Modul zur Durchführung seiner Aufgaben. Führen Sie die folgenden Schritte aus, um das Modul zu Ihrem Automation-Konto hinzuzufügen, das mit den Anmeldeinformationen Ihres AWS-Abonnements konfiguriert ist.  

1. Öffnen Sie Ihren Webbrowser, navigieren Sie zum [PowerShell-Katalog](https://www.powershellgallery.com/packages/AWSPowerShell/), und klicken Sie auf **In Azure Automation bereitstellen**.<br><br> ![Importieren des AWS-PS-Moduls](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Sie werden auf die Azure-Anmeldeseite geleitet und nach der Authentifizierung zum Azure-Portal weitergeleitet, in dem die folgende Seite angezeigt wird:<br><br> ![Seite zum Importieren des Moduls](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Wählen Sie das zu verwendende Automation-Konto aus, und klicken Sie auf **OK**, um die Bereitstellung zu starten.

   > [!NOTE]
   > Beim Importieren eines PowerShell-Moduls in Azure Automation werden auch die Cmdlets extrahiert, und diese Aktivitäten erscheinen erst, wenn der Import des Moduls und das Extrahieren der Cmdlets vollständig abgeschlossen sind. Dieser Vorgang kann einige Minuten dauern.  
   > <br>

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto, auf das in Schritt 3 verwiesen wurde.
2. Klicken Sie auf die Kachel **Assets**, und wählen Sie im Bereich **Assets** die Kachel **Module**.
3. Auf der Seite **Module** wird das Modul **AWSPowerShell** in der Liste angezeigt.

## <a name="create-aws-deploy-vm-runbook"></a>Erstellen eines Runbooks zur Bereitstellung einer VM in AWS
Nach der Bereitstellung des AWS-PowerShell-Moduls können Sie nun ein Runbook erstellen, mit dem sich die Bereitstellung eines virtuellen Computers in AWS unter Verwendung eines PowerShell-Skripts automatisieren lässt. Die Schritte unten zeigen, wie Sie ein natives PowerShell-Skript in Azure Automation nutzen.  

> [!NOTE]
> Weitere Optionen und Informationen zu diesem Skript finden Sie im [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Laden Sie das PowerShell-Skript „New-AwsVM“ aus dem PowerShell-Katalog herunter. Öffnen Sie dazu eine PowerShell-Sitzung, und geben Sie Folgendes ein:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Öffnen Sie im Azure-Portal Ihr Automation-Konto, und klicken Sie im Abschnitt **Prozessautomatisierung** links auf **Runbooks**.  
3. Wählen Sie auf der Seite **Runbooks** die Option **Runbook hinzufügen**.
4. Wählen Sie im Bereich **Runbook hinzufügen** die Option **Schnellerfassung** (Neues Runbook erstellen).
5. Geben Sie im Eigenschaftenbereich für das **Runbook** im Feld „Name“ einen Namen für Ihr Runbook ein, und wählen Sie in der Dropdownliste **Runbooktyp** die Option **PowerShell**. Klicken Sie dann auf **Erstellen**.<br><br> ![Bereich zum Erstellen eines Runbooks](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Wenn die Seite „PowerShell-Runbook bearbeiten“ angezeigt wird, kopieren Sie das PowerShell-Skript, und fügen Sie es in der Canvas zur Runbookerstellung ein.<br><br> ![Runbook-PowerShell-Skript](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Beachten Sie Folgendes, wenn Sie mit dem PowerShell-Beispielskript arbeiten:
    > 
    > * Das Runbook enthält eine Reihe von Standardparameterwerten. Evaluieren Sie alle Standardwerte, und aktualisieren Sie diese bei Bedarf.
    > * Wenn Sie Ihre AWS-Anmeldeinformationen als Anmeldeinformationsasset gespeichert haben, dessen Name nicht **AWScred** lautet, müssen Sie das Skript in Zeile 57 entsprechend aktualisieren.  
    > * Bei der Arbeit mit den AWS-CLI-Befehlen in PowerShell müssen Sie – insbesondere bei diesem Beispielrunbook – die AWS-Region angeben. Andernfalls tritt bei den Cmdlets ein Fehler auf. Weitere Einzelheiten finden Sie im Dokument zu den AWS-Tools für PowerShell im AWS-Thema [Specify AWS Region (Angeben der AWS-Region)](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) .  
    >

7. Um eine Liste mit Imagenamen aus Ihrem AWS-Abonnement abzurufen, starten Sie PowerShell ISE, und importieren Sie das AWS-PowerShell-Modul. Führen Sie die Authentifizierung gegenüber AWS durch, indem Sie **Get-AutomationPSCredential** in Ihrer ISE-Umgebung durch **AWScred = Get-Credential** ersetzen. Sie werden zur Eingabe Ihrer Anmeldeinformationen aufgefordert und können Ihre **Zugriffsschlüssel-ID** als Benutzername und Ihren **geheimen Zugriffsschlüssel** als Kennwort eingeben. Betrachten Sie das folgende Beispiel:  

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    Die folgende Ausgabe wird zurückgegeben:<br><br>
   ![AWS-Images abrufen](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopieren Sie einen der Imagenamen, und fügen Sie ihn in eine Automation-Variable ein, auf die im Runbook per **$InstanceType**verwiesen wird. Da in diesem Beispiel das kostenlose mehrstufige AWS-Abonnement verwendet wird, verwenden Sie für Ihr Beispielrunbook **t2.micro**.  
9. Speichern Sie das Runbook, und klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen. Klicken Sie dann bei Aufforderung auf **Ja**.

### <a name="testing-the-aws-vm-runbook"></a>Testen des AWS-VM-Runbooks
Bevor Sie mit dem Testen des Runbooks fortfahren, müssen einige Dinge überprüft werden. Dies gilt insbesondere in folgenden Fällen:  

* Ein Asset namens **AWScred** wurde für die Authentifizierung gegenüber AWS erstellt, oder das Skript wurde aktualisiert, um auf den Namen Ihres Anmeldeinformationsassets zu verweisen.    
* Das AWS-PowerShell-Modul wurde in Azure Automation importiert.  
* Es wurde ein neues Runbook erstellt, und Parameterwerte wurden bei Bedarf überprüft und aktualisiert.  
* Für **Ausführliche Datensätze protokollieren** und optional **Statusdatensätze protokollieren** unter der Runbookeinstellung **Protokollierung und Nachverfolgung** wurde **Ein** festgelegt.<br><br> ![Runbookprotokollierung und -nachverfolgung](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Das Runbook soll jetzt gestartet werden. Klicken Sie deshalb auf **Start** und dann im Bereich „Runbook starten“ auf **OK**.
2. Geben Sie im Bereich „Runbook starten“ einen Wert für **VMname** an. Akzeptieren Sie die Standardwerte für die anderen Parameter, die Sie zuvor im Skript vorkonfiguriert haben. Klicken Sie auf **OK**, um den Runbookauftrag zu starten.<br><br> ![New-AwsVM-Runbook starten](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Ein Auftragsbereich für den soeben erstellten Runbookauftrag wird angezeigt. Schließen Sie diesen Bereich.
4. Sie können den Fortschritt des Auftrags und die **Ausgabedatenströme** anzeigen, indem Sie auf der Seite des Runbookauftrags auf die Kachel **Alle Protokolle** klicken.<br><br> ![Datenstromausgabe](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Um die Bereitstellung der VM zu bestätigen, melden Sie sich bei der AWS-Verwaltungskonsole an (sofern Sie noch nicht angemeldet sind).<br><br> ![Per AWS-Konsole bereitgestellte VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Nächste Schritte
* Informationen über die ersten Schritte mit grafischen Runbooks finden Sie unter [Mein erstes grafisches Runbook](automation-first-runbook-graphical.md)
* Die ersten Schritte mit PowerShell-Workflow-Runbooks sind unter [Mein erstes PowerShell-Workflow-Runbook](automation-first-runbook-textual.md)
* Informationen über die verschiedenen Runbooktypen, ihre Vorteile und Einschränkungen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md)
* Weitere Informationen zur PowerShell-Skriptunterstützung finden Sie unter [Native PowerShell Script Support in Azure Automation (Native PowerShell-Skriptunterstützung in Azure Automation)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


