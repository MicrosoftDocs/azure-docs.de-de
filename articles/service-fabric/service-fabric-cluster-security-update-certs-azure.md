---
title: Verwalten von Zertifikaten in einem Azure Service Fabric-Cluster
description: Es wird beschrieben, wie Sie neue Zertifikate hinzufügen, ein Rollover für Zertifikate durchführen und ein Zertifikat für einen Service Fabric-Cluster entfernen.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 6dd4440d76bed9d110c13baab9f4e67b3a5c64c0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660898"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Hinzufügen oder Entfernen von Zertifikaten für einen Service Fabric-Cluster in Azure
Es wird empfohlen, dass Sie sich damit vertraut machen, wie Service Fabric X.509-Zertifikate verwendet werden, und dass Sie sich mit den [Szenarien für die Clustersicherheit](service-fabric-cluster-security.md) auskennen. Sie müssen verstehen, was ein Clusterzertifikat ist, und wofür es verwendet wird, bevor Sie den Vorgang fortsetzen.

Das standardmäßige Zertifikatladeverhalten des Azure Service Fabric SDK besteht in der Bereitstellung und Verwendung des definierten Zertifikats mit dem Ablaufdatum, das am weitesten in der Zukunft liegt, und zwar unabhängig von der primären oder sekundären Konfigurationsdefinition. Das Zurückfallen auf das klassische Verhalten ist eine nicht empfohlene erweiterte Aktion und erfordert das Festlegen des Parameters „UseSecondaryIfNewer“ in Ihrer `Fabric.Code`-Konfiguration auf FALSE.

Service Fabric ermöglicht es Ihnen, zusätzlich zu den Clientzertifikaten zwei Clusterzertifikate anzugeben – ein primäres und ein sekundäres –, wenn Sie die Zertifikatsicherheit während der Erstellung des Clusters konfigurieren. Ausführliche Informationen zur Einrichtung während der Erstellung finden Sie unter [Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal](service-fabric-cluster-creation-via-portal.md) oder [Erstellen eines Service Fabric-Clusters in Azure mithilfe von Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Wenn Sie während der Erstellung nur ein Clusterzertifikat angeben, wird es als primäres Zertifikat verwendet. Nach der Erstellung des Clusters können Sie ein neues Zertifikat als sekundäres hinzufügen.

> [!NOTE]
> Für einen sicheren Cluster muss stets mindestens ein gültiges (nicht widerrufenes oder abgelaufenes) Clusterzertifikat (primär oder sekundär) bereitgestellt sein. Andernfalls funktioniert der Cluster nicht mehr. 90 Tage, bevor alle gültigen Zertifikate ablaufen, generiert das System eine Warnungsablaufverfolgung sowie ein Warnungsintegritätsereignis auf dem Knoten. Dabei handelt es sich zurzeit um die einzigen Benachrichtigungen, die Service Fabric zum Ablauf des Zertifikats sendet.
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Hinzufügen eines sekundären Clusterzertifikats mithilfe des Portals
Das sekundäre Clusterzertifikat kann nicht über das Azure-Portal hinzugefügt werden; verwenden Sie stattdessen Azure PowerShell. Der Prozess wird weiter unten in diesem Dokument beschrieben.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Entfernen eines Clusterzertifikats mit dem Portal
Für einen sicheren Cluster benötigen Sie immer mindestens ein gültiges (nicht widerrufenes oder abgelaufenes) Zertifikat. Das bereitgestellte Zertifikat, das das am weitesten in der Zukunft liegende Ablaufdatum aufweist, wird verwendet, und wenn Sie es entfernen, funktioniert Ihr Cluster nicht mehr. Stellen Sie sicher, dass Sie nur das Zertifikat entfernen, das abgelaufen ist, oder ein nicht verwendetes Zertifikat, das am schnellsten abläuft.

Um ein nicht verwendetes Clustersicherheitszertifikat zu entfernen, navigieren Sie zum Abschnitt „Sicherheit“ und wählen die Option „Löschen“ aus dem Kontextmenü für das nicht verwendete Zertifikat aus.

Wenn Sie beabsichtigen, das als primär markierte Zertifikat zu entfernen, müssen Sie ein sekundäres Zertifikat mit einem Ablaufdatum weiter in der Zukunft als das primäre Zertifikat bereitstellen, um das automatische Rolloververhalten zu aktivieren. Löschen Sie das primäre Zertifikat, nachdem der automatische Rollover abgeschlossen wurde.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Hinzufügen eines sekundären Zertifikats mit Azure Resource Manager

Diese Schritte setzen voraus, dass Sie mit der Funktionsweise von Resource Manager vertraut sind und mindestens einen Service Fabric-Cluster mithilfe einer Resource Manager-Vorlage bereitgestellt haben. Außerdem müssen Sie die Vorlage, die Sie zum Einrichten des Clusters verwendet haben, zur Hand haben. Außerdem wird vorausgesetzt, dass Sie mit der Anwendung von JSON vertraut sind.

> [!NOTE]
> Wenn Sie eine Beispielvorlage und Parameter suchen, die Sie als Leitlinie oder Ausgangspunkt verwenden können, laden Sie sie aus diesem [Git-Repository](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) herunter. 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Bearbeiten Ihrer Resource Manager-Vorlage

Damit Sie alles besser verfolgen können, enthält die Beispieldatei „5-VM-1-NodeTypes-Secure_Step2.JSON“ alle Bearbeitungsschritte, die wir ausführen. Die Beispieldatei finden Sie im [Git-Repository](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Achten Sie darauf, dass Sie alle Schritte ausführen**.

1. Öffnen Sie die Resource Manager-Vorlage, die Sie verwendet haben, um den Cluster bereitzustellen. (Gehen Sie wie folgt vor, wenn Sie das Beispiel aus dem vorherigen Repository heruntergeladen haben: Verwenden Sie die Datei „5-VM-1-NodeTypes-Secure_Step1.JSON“, um einen sicheren Cluster bereitzustellen, und öffnen Sie anschließend diese Vorlage.)

2. Fügen Sie die **beiden neuen Parameter** „secCertificateThumbprint“ und „secCertificateUrlValue“ vom Typ „string“ dem Parameterabschnitt Ihrer Vorlage hinzu. Sie können den folgenden Codeausschnitt kopieren und der Vorlage hinzufügen. Je nach Quelle Ihrer Vorlage kann es sein, dass dies bereits definiert wurde. Wenn dies der Fall ist, können Sie mit dem nächsten Schritt fortfahren. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Nehmen Sie Änderungen an der Ressource **Microsoft.ServiceFabric/clusters** vor. Suchen Sie in Ihrer Vorlage nach der Ressourcendefinition „Microsoft.ServiceFabric/clusters“. Unter den Eigenschaften dieser Definition finden Sie das JSON-Tag „Certificate“, das dem folgenden JSON-Codeausschnitt ähneln sollte:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Fügen Sie ein neues Tag thumbprintSecondary hinzu, und weisen Sie ihm den Wert [parameters('secCertificateThumbprint')] zu.  

    Nun sollte die Ressourcendefinition ungefähr wie folgt aussehen (je nach Ihrer Quelle der Vorlage unter Umständen nicht genauso wie der Codeausschnitt unten). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Wenn Sie ein **Rollover für das Zertifikat** durchführen möchten, geben Sie das neue Zertifikat als primäres Zertifikat an und machen das primäre Zertifikat zum sekundären Zertifikat. Dies führt zum Rollover vom aktuellen primären Zertifikat zum neuen Zertifikat in einem einzigen Bereitstellungsschritt.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Nehmen Sie Änderungen an **allen** **Microsoft.Compute/virtualMachineScaleSets**-Ressourcendefinitionen vor. Suchen Sie nach der Ressourcendefinition „Microsoft.Compute/virtualMachineScaleSets“. Scrollen Sie zu „publisher“: „Microsoft.Azure.ServiceFabric“ unter „virtualMachineProfile“.

    In den Service Fabric-Herausgebereinstellungen sollte in etwa Folgendes angezeigt werden.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Fügen Sie die neuen Zertifikateinträge hinzu.
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Die Eigenschaften sollten nun wie folgt aussehen:
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Wenn Sie ein **Rollover für das Zertifikat** durchführen möchten, geben Sie das neue Zertifikat als primäres Zertifikat an und machen das primäre Zertifikat zum sekundären Zertifikat. Dies führt zum Rollover des aktuellen Zertifikats zum neuen Zertifikat in einem einzigen Bereitstellungsschritt.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Die Eigenschaften sollten nun wie folgt aussehen:    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Nehmen Sie Änderungen an **allen** **Microsoft.Compute/virtualMachineScaleSets**-Ressourcendefinitionen vor. Suchen Sie nach der Ressourcendefinition „Microsoft.Compute/virtualMachineScaleSets“. Scrollen Sie unter „OSProfile“ zu „vaultCertificates“. Dies sollte in etwa wie folgt aussehen:

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Fügen Sie „secCertificateUrlValue“ hinzu. Verwenden Sie den folgenden Codeausschnitt:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Der sich ergebende JSON-Code sollte nun in etwa wie folgt aussehen:
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Stellen Sie sicher, dass Sie die Schritte 4 und 5 für alle Nodetypes/Microsoft.Compute/virtualMachineScaleSets-Ressourcendefinitionen in Ihrer Vorlage wiederholt haben. Falls Sie eine Definition auslassen, wird das Zertifikat nicht auf der entsprechenden VM-Skalierungsgruppe installiert, und Sie erhalten im Cluster unvorhersagbare Ergebnisse, z.B. einen Ausfall des Clusters (falls keine gültigen Zertifikate mehr vorhanden sind, die vom Cluster für Sicherheitszwecke verwendet werden können). Überprüfen Sie dies also noch einmal, bevor Sie fortfahren.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Bearbeiten Ihrer Vorlagendatei gemäß der neuen Parameter, die Sie oben hinzugefügt haben
Wenn Sie das Beispiel aus dem [Git-Repository](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) als Leitlinie verwendet haben, können Sie beginnen, Änderungen im Beispiel „5-VM-1-NodeTypes-Secure.parameters_Step2.JSON“ vorzunehmen. 

Bearbeiten Sie die Parameterdatei der Resource Manager-Vorlage, indem Sie die beiden neuen Parameter für „secCertificateThumbprint“ und „secCertificateUrlValue“ hinzufügen. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Bereitstellen der Vorlage in Azure

- Sie können nun Ihre Vorlage in Azure bereitstellen. Öffnen Sie eine „Azure PowerShell Version 1+“-Eingabeaufforderung.
- Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie das spezifische Azure-Abonnement aus. Dies ist ein wichtiger Schritt für Sie, falls Sie auf mehrere Azure-Abonnements zugreifen können.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Testen Sie die Vorlage vor der Bereitstellung. Verwenden Sie die gleiche Ressourcengruppe, für die Ihr Cluster derzeit bereitgestellt wird.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Stellen Sie die Vorlage für Ihre Ressourcengruppe bereit. Verwenden Sie die gleiche Ressourcengruppe, für die Ihr Cluster derzeit bereitgestellt wird. Führen Sie den Befehl „New-AzResourceGroupDeployment“ aus. Sie müssen nicht den Modus angeben, da der Standardwert **incremental** ist.

> [!NOTE]
> Wenn Sie für „Mode“ den Wert „Complete“ festlegen, können Sie versehentlich Ressourcen löschen, die nicht in Ihrer Vorlage enthalten sind. Darum tun Sie es in diesem Szenario nicht.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Hier finden Sie ein ausgefülltes Beispiel für den gleichen PowerShell-Befehl.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Nachdem die Bereitstellung abgeschlossen ist, stellen Sie unter Verwendung des neuen Zertifikats eine Verbindung mit Ihrem Cluster her, und führen Sie einige Abfragen aus. Sofern es Ihnen möglich ist. Anschließend können Sie das alte Zertifikat löschen. 

Wenn Sie ein selbstsigniertes Zertifikat verwenden, vergessen Sie nicht, es in Ihren lokalen TrustedPeople-Zertifikatspeicher zu importieren.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
In einer Kurzübersicht finden Sie hier den Befehl zum Herstellen der Verbindung mit einem sicheren Cluster: 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
In einer Kurzübersicht finden Sie hier den Befehl zum Abrufen der Clusterintegrität:

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Bereitstellen von Clientzertifikaten für den Cluster

Sie können die gleichen Schritte wie im vorangegangenen Schritt 5 ausführen, um die Zertifikate aus einem Schlüsseltresor auf den Knoten bereitzustellen. Sie müssen nur die verschiedenen Parameter definieren und verwenden.


## <a name="adding-or-removing-client-certificates"></a>Hinzufügen oder Entfernen von Clientzertifikaten

Zusätzlich zu den Clusterzertifikaten können Sie Clientzertifikate hinzufügen, um Verwaltungsvorgänge für einen Service Fabric-Cluster durchzuführen.

Sie können zwei Arten von Clientzertifikaten hinzufügen: „Administrator“ oder „Schreibgeschützt“. Diese können dann zum Steuern des Zugriffs auf die Administratorvorgänge und Abfragevorgänge im Cluster verwendet werden. Standardmäßig werden die Clusterzertifikate der Liste mit den zulässigen Administratorzertifikaten hinzugefügt.

Sie können eine beliebige Anzahl von Clientzertifikaten angeben. Jedes Hinzufügen/Löschen führt zu einem Konfigurationsupdate des Service Fabric-Clusters.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Hinzufügen von Clientzertifikaten – „Administrator“ oder „Schreibgeschützt“ über das Portal

1. Navigieren Sie zum Abschnitt „Sicherheit“, und wählen Sie oben im Abschnitt „Sicherheit“ die Schaltfläche „+ Authentifizierung“.
2. Wählen Sie im Abschnitt „Authentifizierung hinzufügen“ die Option „Authentifizierungstyp“ > „Read-only client“ (Schreibgeschützter Client) oder „Admin client“ (Admin-Client).
3. Wählen Sie nun die Autorisierungsmethode. Gibt für Service Fabric an, ob dieses Zertifikat anhand des Antragstellernamens oder anhand des Fingerabdrucks gesucht werden soll. Es ist im Allgemeinen keine gute Vorgehensweise in Bezug auf die Sicherheit, die Autorisierungsmethode mit dem Antragstellernamen zu verwenden. 

![Hinzufügen des Clientzertifikats][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Löschen von Clientzertifikaten – „Administrator“ oder „Schreibgeschützt“ über das Portal

Zum Entfernen eines sekundären Zertifikats aus der Verwendung für die Clustersicherheit navigieren Sie zum Abschnitt „Sicherheit“ und wählen im Kontextmenü für das spezifische Zertifikat die Option „Löschen“.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Hinzufügen von Anwendungszertifikaten zu einer VM-Skalierungsgruppe

Informationen zum Bereitstellen eines Zertifikats, das Sie für Ihre Anwendungen verwenden, auf Ihrem Cluster, finden Sie in [diesem PowerShell-Beispielskript](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel, um weitere Informationen zur Clusterverwaltung zu enthalten:

* [Service Fabric-Cluster-Upgradeprozess und Erwartungen](service-fabric-cluster-upgrade.md)
* [Rollenbasierte Zugriffssteuerung für Service Fabric-Clients](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


