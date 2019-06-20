---
title: Bewährte Methoden für die Azure Service Fabric-Sicherheit | Microsoft-Dokumentation
description: Enthält eine Beschreibung der bewährten Methoden für die Azure Service Fabric-Sicherheit.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 036a06111c3ec0ad54ac4bad4f75a9be10dbdd2b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258717"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric-Sicherheit 

Weitere Informationen zu den [bewährten Methoden in Bezug auf die Azure-Sicherheit](https://docs.microsoft.com/azure/security/) finden Sie unter [Bewährte Methoden für die Azure Service Fabric-Sicherheit](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices).

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) ist der empfohlene Dienst für die Verwaltung von Geheimnissen für Azure Service Fabric-Anwendungen und -Cluster.
> [!NOTE]
> Wenn Zertifikate/Geheimnisse aus einem Schlüsseltresor (Key Vault) in einer VM-Skalierungsgruppe als zugehöriges Geheimnis bereitgestellt werden, müssen sich der Key Vault und die VM-Skalierungsgruppe in derselben Region befinden.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Erstellen eines von einer Zertifizierungsstelle ausgestellten Service Fabric-Zertifikats

Ein Azure Key Vault-Zertifikat kann entweder erstellt oder in einen Key Vault importiert werden. Wenn ein Key Vault-Zertifikat erstellt wird, wird der private Schlüssel innerhalb des Schlüsseltresors erstellt und niemals für den Zertifikatsinhaber verfügbar gemacht. Hier sind die Möglichkeiten zum Erstellen eines Zertifikats in Key Vault angegeben:

- Erstellen Sie ein selbstsigniertes Zertifikat, um ein öffentlich-privates Schlüsselpaar zu erstellen und einem Zertifikat zuzuordnen. Das Zertifikat wird mit einem eigenen Schlüssel signiert. 
- Erstellen Sie manuell ein neues Zertifikat, um ein öffentlich-privates Schlüsselpaar zu erstellen und die Anforderung einer X.509-Zertifikatsignatur zu generieren. Die Signaturanforderung kann von Ihrer Registrierungs- oder Zertifizierungsstelle signiert werden. Das signierte x509-Zertifikat lässt sich dann mit dem ausstehenden Schlüsselpaar zusammenführen, um das KV-Zertifikat in Key Vault zu vervollständigen. Obwohl diese Methode mehr Schritte erfordert, bietet sie Ihnen mehr Sicherheit, da der private Schlüssel in Key Vault erstellt und darauf beschränkt wird. Dies wird im folgenden Diagramm erläutert. 

Weitere Informationen finden Sie unter [Methoden für die Zertifikaterstellung](https://docs.microsoft.com/azure/key-vault/create-certificate).

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Bereitstellen von Key Vault-Zertifikaten für VM-Skalierungsgruppen von Service Fabric-Clustern

Verwenden Sie zum Bereitstellen von Zertifikaten aus einem in derselben Region angeordneten Schlüsseltresor in einer VM-Skalierungsgruppe die VM-Skalierungsgruppe [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Hier sind die Eigenschaften von Resource Manager-Vorlagen aufgeführt:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Für den Tresor muss die Bereitstellung von Resource Manager-Vorlagen aktiviert sein.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Anwenden einer Zugriffssteuerungsliste (Access Control List, ACL) auf Ihr Zertifikat für Ihren Service Fabric-Cluster

Der Microsoft.Azure.ServiceFabric-Herausgeber für [VM-Skalierungsgruppenerweiterungen](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) wird verwendet, um Ihre Knotensicherheit zu konfigurieren.
Verwenden Sie die folgenden Resource Manager-Vorlageneigenschaften, um eine ACL auf die Zertifikate für Ihre Service Fabric-Clusterprozesse anzuwenden:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Schützen eines Service Fabric-Clusterzertifikats anhand eines allgemeinen Namens

Verwenden Sie die Resource Manager-Vorlageneigenschaft [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames) wie folgt, um Ihren Service Fabric-Cluster anhand des Zertifikats `Common Name` zu schützen:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Für Service Fabric-Cluster wird das erste gültige Zertifikat verwendet, das im Zertifikatspeicher Ihres Hosts gefunden wird. Unter Windows ist dies das Zertifikat mit dem spätesten Ablaufdatum, das mit Ihrem allgemeinen Namen und dem Fingerabdruck des Ausstellers übereinstimmt.

Azure-Domänen, z.B. „*\<IHRE UNTERDOMÄNE\>.cloudapp.azure.com“ oder „\<IHRE UNTERDOMÄNE\>.trafficmanager.net“, befinden sich im Besitz von Microsoft. Zertifizierungsstellen stellen für nicht berechtigte Benutzer keine Zertifikate für Domänen aus. Die meisten Benutzer müssen eine Domäne von einer Registrierungsstelle erwerben oder ein autorisierter Domänenadministrator für eine Zertifizierungsstelle sein, um für Sie ein Zertifikat mit diesem allgemeinen Namen ausstellen zu können.

Weitere Informationen dazu, wie Sie den DNS-Dienst zum Auflösen Ihrer Domäne in eine Microsoft-IP-Adresse konfigurieren, finden Sie in der Anleitung zur Konfiguration von [Azure DNS zum Hosten Ihrer Domäne](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Nachdem Sie Ihre Domänennamenserver an Ihre Azure DNS-Zonennamenserver delegiert haben, können Sie Ihrer DNS-Zone die beiden folgenden Einträge hinzufügen:
> - Einen A-Eintrag für den Domänen-APEX, bei dem es sich NICHT um ein `Alias record set` für alle IP-Adressen handelt, die von Ihrer benutzerdefinierten Domäne aufgelöst werden.
> - Einen C-Eintrag für von Ihnen bereitgestellte Microsoft-Unterdomänen, bei denen es sich NICHT um ein `Alias record set` handelt. Sie können beispielsweise den DNS-Namen Ihrer Traffic Manager-Instanz oder Ihres Load Balancers verwenden.

Aktualisieren Sie die folgenden Resource Manager-Vorlageneigenschaften für den Service Fabric-Cluster, damit in Ihrem Portal ein benutzerdefinierter DNS-Name für den `"managementEndpoint"` Ihres Service Fabric-Clusters angezeigt wird:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Verschlüsseln der Geheimniswerte von Service Fabric-Paketen

Allgemeine Werte, die in Service Fabric-Paketen verschlüsselt werden, umfassen ACR-Anmeldeinformationen (Azure Container Registry), Umgebungsvariablen, Einstellungen und Speicherkontoschlüssel für Azure-Volume-Plug-Ins.

Gehen Sie wie folgt vor, [um ein Verschlüsselungszertifikat einzurichten und Geheimnisse in Windows-Clustern zu verschlüsseln](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Generieren Sie ein selbstsigniertes Zertifikat zum Verschlüsseln Ihres Geheimnisses:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Befolgen Sie die Anleitung unter [Bereitstellen von Key Vault-Zertifikaten für VM-Skalierungsgruppen von Service Fabric-Clustern](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets), um Key Vault-Zertifikate für die VM-Skalierungsgruppen Ihres Service Fabric-Clusters bereitzustellen.

Verschlüsseln Sie Ihr Geheimnis mit dem folgenden PowerShell-Befehl, und aktualisieren Sie Ihr Service Fabric-Anwendungsmanifest anschließend mit dem verschlüsselten Wert:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Gehen Sie wie folgt vor, [um ein Verschlüsselungszertifikat einzurichten und Geheimnisse in Linux-Clustern zu verschlüsseln](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Generieren Sie ein selbstsigniertes Zertifikat zum Verschlüsseln Ihrer Geheimnisse:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Befolgen Sie die Anleitung unter [Bereitstellen von Key Vault-Zertifikaten für VM-Skalierungsgruppen von Service Fabric-Clustern](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets), um dies für die VM-Skalierungsgruppen Ihres Service Fabric-Clusters durchzuführen.

Verschlüsseln Sie Ihr Geheimnis mit den folgenden Befehlen, und aktualisieren Sie Ihr Service Fabric-Anwendungsmanifest anschließend mit dem verschlüsselten Wert:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Nachdem Sie Ihre geschützten Werte verschlüsselt haben, können Sie [verschlüsselte Geheimnisse in der Service Fabric-Anwendung angeben](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application) und [verschlüsselte Geheimnisse aus dem Dienstcode entschlüsseln](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Authentifizieren von Service Fabric-Anwendungen für Azure-Ressourcen per verwalteter Dienstidentität (Managed Service Identity, MSI)

Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Azure Service Fabric-Cluster werden in VM-Skalierungsgruppen gehostet, die die [verwaltete Dienstidentität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources) unterstützen.
Eine Liste mit Diensten, für die MSI für die Authentifizierung genutzt werden kann, finden Sie unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Deklarieren Sie die folgende `"Microsoft.Compute/virtualMachinesScaleSets"`-Eigenschaft, um die vom System zugewiesene verwaltete Identität bei der Erstellung einer VM-Skalierungsgruppe oder in einer vorhandenen VM-Skalierungsgruppe zu aktivieren:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity).

Gehen Sie wie folgt vor, wenn Sie eine [vom Benutzer zugewiesene verwaltete Identität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity) erstellt haben: Deklarieren Sie die folgende Ressource in Ihrer Vorlage, um sie Ihrer VM-Skalierungsgruppe zuzuweisen. Ersetzen Sie `\<USERASSIGNEDIDENTITYNAME\>` durch den Namen der vom Benutzer zugewiesenen verwalteten Identität, die Sie erstellt haben:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Bevor Ihre Service Fabric-Anwendung eine verwaltete Identität nutzen kann, müssen den Azure-Ressourcen die Berechtigungen gewährt werden, die für die Authentifizierung erforderlich sind.
Mit den folgenden Befehlen wird der Zugriff auf eine Azure-Ressource gewährt:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

Rufen Sie in Ihrem Service Fabric-Anwendungscode [ein Zugriffstoken](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) für Azure Resource Manager ab, indem Sie beispielsweise einen REST-Aufruf wie den folgenden durchführen:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Ihre Service Fabric-App kann das Zugriffstoken dann zum Authentifizieren gegenüber Azure-Ressourcen verwenden, die Active Directory unterstützen.
Im folgenden Beispiel ist dargestellt, wie Sie dies für die Cosmos DB-Ressource durchführen:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows-Sicherheitsbaselines
[Es empfiehlt sich, eine branchenübliche Konfiguration zu implementieren, die allgemein bekannt ist und ausführlich getestet wurde (beispielsweise Microsoft-Sicherheitsbaselines), anstatt eine eigene Baseline zu erstellen.](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) Zur Bereitstellung für Ihre VM-Skalierungsgruppen können Sie beispielsweise den Azure-DSC-Erweiterungshandler (Desired State Configuration) verwenden, um die virtuellen Computer zu konfigurieren, sobald sie online geschaltet werden, damit auf ihnen die Produktionssoftware ausgeführt wird.

## <a name="azure-firewall"></a>Azure Firewall
[Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst zum Schutz Ihrer Azure Virtual Network-Ressourcen. Hierbei handelt es sich um eine vollständig zustandsbehaftete Firewall-as-a-Service mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit.](https://docs.microsoft.com/azure/firewall/overview) Dadurch lässt sich ausgehender HTTP/S-Datenverkehr auf eine Liste vollständig qualifizierter Domänennamen (Fully Qualified Domain Names, FQDNs) einschließlich Platzhaltern beschränken. Diese Funktion erfordert keine SSL-Beendigung. Es wird empfohlen, [FQDN-Tags von Azure Firewall](https://docs.microsoft.com/azure/firewall/fqdn-tags) für Windows-Updates zu nutzen und Netzwerkdatenverkehr für Microsoft Windows Update-Endpunkte zu aktivieren, damit dieser Ihre Firewall passieren kann.

## <a name="tls-12"></a>TLS 1.2
[TSG](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Standardmäßig ist Windows Defender Antivirus unter Windows Server 2016 installiert. Weitere Informationen finden Sie unter [Windows Defender Antivirus auf Windows Server2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Die Benutzeroberfläche wird bei einigen SKUs standardmäßig installiert, aber dies ist keine erforderliche Komponente. Gehen Sie wie folgt vor, um durch Windows Defender verursachte Leistungsbeeinträchtigungen und Erhöhungen des Ressourcenverbrauchs zu verringern (und falls Ihre Sicherheitsrichtlinien es zulassen, Prozesse und Pfade für Open-Source-Software auszuschließen): Deklarieren Sie die folgenden Resource Manager-Vorlageneigenschaften für die VM-Skalierungsgruppenerweiterung, um Ihren Service Fabric-Cluster von Scanvorgängen auszuschließen:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Sehen Sie sich in der Dokumentation zu Ihrer Antischadsoftware-Anwendung die Konfigurationsregeln an, falls Sie Windows Defender nicht nutzen. Windows Defender wird unter Linux nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Windows Server: [Erstellen eines Service Fabric-Clusters für Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Linux: [Erstellen eines Linux-Clusters](service-fabric-cluster-creation-via-portal.md)
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
