---
title: Hinzufügen von Azure Automation-Runbooks zu Site Recovery-Wiederherstellungsplänen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Wiederherstellungspläne mit Azure Automation für die Notfallwiederherstellung mit Azure Site Recovery erweitern.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 26c3466080cb356ca3610d42eaaf5ee4975d3731
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471877"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Hinzufügen von Azure Automation-Runbooks zu Wiederherstellungsplänen
In diesem Artikel wird beschrieben, wie Azure Site Recovery in Azure Automation integriert wird, um Sie bei der Erweiterung Ihrer Wiederherstellungspläne zu unterstützen. Mit Wiederherstellungsplänen kann die Wiederherstellung von VMs orchestriert werden, die mit Site Recovery geschützt werden. Wiederherstellungspläne können sowohl für die Replikation in eine sekundäre Cloud als auch für die Replikation in Azure genutzt werden. Darüber hinaus tragen Wiederherstellungspläne zu einer **durchgängig exakten**, **wiederholbaren** und **automatisierten** Wiederherstellung bei. Wenn Sie für Ihre VMs ein Failover zu Azure durchführen, wird die Integration mit Azure Automation auf Ihre Wiederherstellungspläne erweitert. Sie können sie zum Ausführen von Runbooks verwenden, die leistungsfähige Automatisierungsaufgaben ermöglichen.

Wenn Sie mit Azure Automation noch nicht vertraut sind, können Sie sich [registrieren](https://azure.microsoft.com/services/automation/) und [Beispielskripts herunterladen](https://azure.microsoft.com/documentation/scripts/). Weitere Informationen hierzu und zur Orchestrierung der Wiederherstellung in Azure mit [Wiederherstellungsplänen](./site-recovery-create-recovery-plans.md) finden Sie unter [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

In diesem Artikel wird beschrieben, wie Sie Azure Automation-Runbooks in Ihre Wiederherstellungspläne integrieren können. Wir verwenden Beispiele zum Automatisieren von einfachen Aufgaben, für die bisher ein manueller Eingriff erforderlich war. Außerdem wird beschrieben, wie Sie eine Wiederherstellung mit mehreren Schritten in eine Single-Click-Wiederherstellungsaktion konvertieren.

## <a name="customize-the-recovery-plan"></a>Anpassen des Wiederherstellungsplans
1. Navigieren Sie zum **Site Recovery**-Blatt für die Wiederherstellungsplanressource. In diesem Beispiel wurden dem Wiederherstellungsplan zwei VMs für die Wiederherstellung hinzugefügt. Klicken Sie auf die Registerkarte **Anpassen**, um mit dem Hinzufügen eines Runbooks zu beginnen.

    ![Klicken auf die Schaltfläche „Anpassen“](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Klicken Sie mit der rechten Maustaste auf **Gruppe 1: Start**, und wählen Sie **Nachfolgende Aktion hinzufügen** aus.

    ![Klicken mit der rechten Maustaste auf „Gruppe 1: Start“ und „Nachfolgende Aktion hinzufügen“](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Klicken Sie auf **Choose a script** (Skript auswählen).

4. Geben Sie dem Skript auf dem Blatt **Updateaktion** den Namen **Hello World**.

    ![Blatt „Updateaktion“](media/site-recovery-runbook-automation-new/update-rp.png)

5. Geben Sie einen Automation-Kontonamen ein.
    >[!NOTE]
    > Das Automation-Konto kann sich in einer beliebigen Azure-Region befinden. Das Automation-Konto muss sich in demselben Abonnement wie der Azure Site Recovery-Tresor befinden.

6. Wählen Sie in Ihrem Automation-Konto ein Runbook aus. Dieses Runbook ist das Skript, das während der Ausführung des Wiederherstellungsplans nach der Wiederherstellung der ersten Gruppe ausgeführt wird.

7. Klicken Sie auf **OK**, um das Skript zu speichern. Das Skript wird **Gruppe 1: Schritte danach** hinzugefügt.

    ![Nachfolgende Aktion, Gruppe 1: Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Hinweise zum Hinzufügen eines Skripts

* Klicken Sie mit der rechten Maustaste auf das Skript, um Optionen zum **Löschen eines Schritts** oder **Aktualisieren des Skripts** anzuzeigen.
* Ein Skript kann in Azure während eines Failovers von einem lokalen Computer zu Azure ausgeführt werden. Es kann in Azure während des Failbacks von Azure zu einem lokalen Computer auch als Skript des primären Standorts vor dem Herunterfahren ausgeführt werden.
* Wenn ein Skript ausgeführt wird, wird ein Wiederherstellungsplan-Kontext eingefügt. Im folgenden Beispiel wird eine Kontextvariable angezeigt:

    ```
            {"RecoveryPlanName":"hrweb-recovery",

            "FailoverType":"Test",

            "FailoverDirection":"PrimaryToSecondary",

            "GroupId":"1",

            "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                    "ResourceGroupName":"ContosoRG",

                    "CloudServiceName":"pod02hrweb-Chicago-test",

                    "RoleName":"Fabrikam-Hrweb-frontend-test",

                    "RecoveryPointId":"TimeStamp"}

                    }

            }
    ```

    In der folgenden Tabelle sind der Name und die Beschreibung der einzelnen Variablen im Kontext aufgeführt:

    | **Variablenname** | **Beschreibung** |
    | --- | --- |
    | RecoveryPlanName |Der Name des Plans, der ausgeführt wird. Mit dieser Variablen können Sie basierend auf dem Namen des Wiederherstellungsplans unterschiedliche Aktionen durchführen. Sie können das Skript auch wiederverwenden. |
    | FailoverType |Gibt an, ob es sich um ein Testfailover, ein geplantes Failover oder ein nicht geplantes Failover handelt. |
    | FailoverDirection |Gibt an, ob die Wiederherstellung auf einem primären oder sekundären Standort durchgeführt wird. |
    | GroupID |Identifiziert die Gruppennummer innerhalb des Wiederherstellungsplans, wenn der Plan ausgeführt wird. |
    | VmMap |Ein Array mit allen VMs der Gruppe. |
    | VMMap-Schlüssel |Ein eindeutiger Schlüssel (GUID) für jeden virtuellen Computer. Er entspricht der Azure-VMM-ID (Virtual Machine Manager) der VM, falls zutreffend. |
    | SubscriptionId |Die ID des Azure-Abonnements, unter dem die VM erstellt wurde. |
    | RoleName |Der Name der Azure-VM, die wiederhergestellt wird. |
    | CloudServiceName |Der Name des Azure-Clouddiensts, unter dem die VM erstellt wurde. |
    | ResourceGroupName|Der Name der Azure-Ressourcengruppe, unter der die VM erstellt wurde. |
    | RecoveryPointId|Der Zeitstempel für den Wiederherstellungszeitpunkt der VM. |

* Stellen Sie sicher, dass das Automation-Konto über die folgenden Module verfügt:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Alle Module sollten auf kompatiblen Versionen basieren. Sie können auf einfache Weise sicherstellen, dass alle Module kompatibel sind, indem Sie die aktuellen Versionen aller Module verwenden.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Zugreifen auf alle VMs der VMMap in einer Schleife
Verwenden Sie den folgenden Code, um eine Schleife durch alle VMs von Microsoft VMMap durchzuführen:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
 foreach($VMID in $VMinfo)
 {
     $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
         #this check is to ensure that we skip when some data is not available else it will fail
 Write-output "Resource group name ", $VM.ResourceGroupName
 Write-output "Rolename " = $VM.RoleName
     }
 }

```

> [!NOTE]
> Die Werte für den Ressourcengruppennamen und den Rollennamen sind leer, wenn es sich bei dem Skript um eine vorausgehende Aktion einer Startgruppe handelt. Die Werte werden nur aufgefüllt, wenn das Failover für die VM dieser Gruppe erfolgreich ist. Das Skript ist eine nachfolgende Aktion der Startgruppe.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Verwenden desselben Automation-Runbooks in mehreren Wiederherstellungsplänen

Sie können ein einzelnes Skript in mehreren Wiederherstellungsplänen nutzen, indem externe Variablen verwendet werden. Sie können [Azure Automation-Variablen](../automation/automation-variables.md) zum Speichern von Parametern verwenden, die Sie für die Ausführung eines Wiederherstellungsplans übergeben können. Indem Sie der Variablen den Namen des Wiederherstellungsplans als Präfix hinzufügen, können Sie für jeden Wiederherstellungsplan einzelne Variablen erstellen. Verwenden Sie die Variablen anschließend als Parameter. Sie können einen Parameter ändern, ohne das Skript zu ändern, und für die Funktionsweise des Skripts trotzdem eine Änderung erzielen.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Verwenden einer einfachen Zeichenfolgenvariablen in einem Runbookskript

In diesem Beispiel wendet ein Skript die Eingabe einer Netzwerksicherheitsgruppe (NSG) auf die VMs eines Wiederherstellungsplans an.

Verwenden Sie den Wiederherstellungsplan-Kontext, damit das Skript erkennt, welcher Wiederherstellungsplan ausgeführt wird:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Zum Anwenden einer vorhandenen NSG müssen Sie den NSG-Namen und den Namen der NSG-Ressourcengruppe kennen. Verwenden Sie diese Variablen als Eingaben für Wiederholungsplanskripts. Erstellen Sie hierzu in den Automation-Kontoassets zwei Variablen. Fügen Sie den Namen des Wiederherstellungsplans, für den Sie die Parameter erstellen, als Präfix dem Variablennamen hinzu.

1. Erstellen Sie eine Variable zum Speichern des NSG-Namens. Fügen Sie dem Variablennamen ein Präfix hinzu, indem Sie den Namen des Wiederherstellungsplans verwenden.

    ![Erstellen einer Variablen für den NSG-Namen](media/site-recovery-runbook-automation-new/var1.png)

2. Erstellen Sie eine Variable, um den Ressourcengruppennamen der NSG zu speichern. Fügen Sie dem Variablennamen ein Präfix hinzu, indem Sie den Namen des Wiederherstellungsplans verwenden.

    ![Erstellen des Namens einer NSG-Ressourcengruppe](media/site-recovery-runbook-automation-new/var2.png)


3.  Verwenden Sie im Skript den folgenden Referenzcode, um die Variablenwerte abzurufen:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Verwenden Sie die Variablen im Runbook, um die NSG auf die Netzwerkschnittstelle der VM anzuwenden, für den das Failover durchgeführt wurde:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```

Erstellen Sie für jeden Wiederherstellungsplan unabhängige Variablen, damit Sie das Skript wiederverwenden können. Fügen Sie ein Präfix hinzu, indem Sie den Namen des Wiederherstellungsplans verwenden. Ein vollständiges End-to-End-Skript für dieses Szenario finden Sie unter [Add a public IP and NSG to VMs during test failover of a Site Recovery recovery plan](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee) (Hinzufügen einer öffentlichen IP und NSG zu VMs während des Testfailovers eines Site Recovery-Wiederherstellungsplans).


### <a name="use-a-complex-variable-to-store-more-information"></a>Verwenden einer komplexen Variablen zum Speichern von weiteren Informationen

Stellen Sie sich ein Szenario vor, bei dem mit nur einem Skript für bestimmte VMs eine öffentliche IP aktiviert werden soll. In einem anderen Szenario kann es sein, dass Sie verschiedene NSGs unterschiedlichen VMs (nicht auf allen VMs) zuordnen möchten. Sie können ein Skript erstellen, das für alle Wiederherstellungspläne wiederverwendet werden kann. Jeder Wiederherstellungsplan kann über eine variable Anzahl von VMs verfügen. Eine SharePoint-Wiederherstellung verfügt über zwei Front-Ends. Eine einfache Branchenanwendung (LOB-Anwendung) hat nur ein Front-End. Sie können nicht für jeden Wiederherstellungsplan separate Variablen erstellen.

Im folgenden Beispiel verwenden wir ein neues Verfahren und erstellen eine [komplexe Variable](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) in den Azure Automation-Kontoassets. Hierzu geben Sie mehrere Werte an. Verwenden Sie Azure PowerShell, um die folgenden Schritte auszuführen:

1. Melden Sie sich in PowerShell bei Ihrem Azure-Abonnement an:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Erstellen Sie zum Speichern der Parameter die komplexe Variable, indem Sie den Namen des Wiederherstellungsplans verwenden:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. In dieser komplexen Variablen ist **VMDetails** die VM-ID für die geschützte VM. Zeigen Sie im Azure-Portal die VM-Eigenschaften an, um die VM-ID zu erhalten. Im folgenden Screenshot ist eine Variable dargestellt, in der die Details von zwei VMs gespeichert werden:

    ![Verwenden der VM-ID als GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Verwenden Sie diese Variable in Ihrem Runbook. Wenn sich die angegebene VM-GUID im Kontext des Wiederherstellungsplans befindet, können Sie die NSG auf die VM anwenden:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Führen Sie in Ihrem Runbook eine Schleife durch die VMs des Wiederherstellungsplan-Kontexts durch. Überprüfen Sie, ob die VM in **$VMDetailsObj** vorhanden ist. Wenn ja, greifen Sie auf die Eigenschaften der Variablen zu, um die NSG anzuwenden:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Sie können dasselbe Skript für unterschiedliche Wiederherstellungspläne verwenden. Geben Sie unterschiedliche Parameter ein, indem Sie den Wert, der einem Wiederherstellungsplan entspricht, in unterschiedlichen Variablen speichern.

## <a name="sample-scripts"></a>Beispielskripts

Klicken Sie auf die Schaltfläche **Deploy to Azure**, um Beispielskripts für Ihr Automation-Konto bereitzustellen.

[![In Azure bereitstellen](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ein weiteres Beispiel ist im folgenden Video enthalten. Es wird veranschaulicht, wie Sie eine WordPress-Anwendung mit zwei Ebenen in Azure wiederherstellen:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](../automation/automation-create-runas-account.md)
* [Übersicht über Azure Automation](https://msdn.microsoft.com/library/azure/dn643629.aspx "Übersicht über Azure Automation")
* [Azure Automation-Beispielskripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure Automation-Beispielskripts")

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen](site-recovery-failover.md) zum Ausführen von Failovern
