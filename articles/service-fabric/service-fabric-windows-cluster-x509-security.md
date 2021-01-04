---
title: Sichern eines Clusters unter Windows mithilfe von Zertifikaten
description: Sichern Sie die Kommunikation in einem eigenständigen oder lokalen Azure Service Fabric-Cluster sowie zwischen Clients und dem Cluster.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: 34ba457ce0f39705393962d5c5ec8fa11668f413
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686122"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Schützen eines eigenständigen Windows-Clusters mithilfe von X.509-Zertifikaten
In diesem Artikel wird beschrieben, wie Sie die Kommunikation zwischen verschiedenen Knoten eines eigenständigen Windows-Clusters sichern. Außerdem wird beschrieben, wie Sie mithilfe von X.509-Zertifikaten Clients authentifizieren, die sich mit diesem Cluster verbinden. Mit der Authentifizierung wird sichergestellt, dass nur autorisierte Benutzer auf den Cluster und bereitgestellte Anwendungen zugreifen und Verwaltungsaufgaben ausführen können. Die Zertifikatsicherheit sollte auf dem Cluster aktiviert werden, wenn der Cluster erstellt wird.  

Weitere Informationen zur Clustersicherheit wie Knoten-zu-Knoten-Sicherheit, Client-zu-Knoten-Sicherheit und rollenbasierte Zugriffssteuerung finden Sie unter [Szenarios für die Clustersicherheit](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Welche Zertifikate werden benötigt?
Laden Sie als Erstes das [Service Fabric-Paket für Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) auf einen der Knoten im Cluster herunter. Das heruntergeladene Paket enthält die Datei „ClusterConfig.X509.MultiMachine.json“. Öffnen Sie die Datei, und sehen Sie sich den Abschnitt „security“ im Abschnitt „properties“ an:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

In diesem Abschnitt werden die Zertifikate beschrieben, die Sie zum Schützen des eigenständigen Windows-Clusters benötigen. Wenn Sie ein Zertifikat für Cluster angeben, legen Sie den Wert von „ClusterCredentialType“ auf _X509_ fest. Wenn Sie ein Serverzertifikat für externe Verbindungen angeben, legen Sie den Wert von „ServerCredentialType“ auf _X509_ fest. Auch wenn es nicht zwingend erforderlich ist, werden beide Zertifikate für einen ordnungsgemäß gesicherten Cluster empfohlen. Wenn Sie diese Werte auf *X509* festgelegt haben, müssen Sie auch die entsprechenden Zertifikate angeben, weil Service Fabric andernfalls eine Ausnahme auslöst. In einigen Szenarios sollten Sie nur _ClientCertificateThumbprints_ oder _ReverseProxyCertificate_ angeben. Es ist in diesen Szenarios nicht erforderlich, _ClusterCredentialType_ oder _ServerCredentialType_ auf _X509_ festzulegen.


> [!NOTE]
> Ein [Fingerabdruck](https://en.wikipedia.org/wiki/Public_key_fingerprint) ist die primäre Identität eines Zertifikats. Wie Sie den Fingerabdruck der von Ihnen erstellten Zertifikate ermitteln, erfahren Sie unter [Abrufen des Fingerabdrucks eines Zertifikats](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
> 
> 

In der folgenden Tabelle sind die Zertifikate aufgeführt, die Sie für die Einrichtung des Clusters benötigen:

| **CertificateInformation-Einstellung** | **Beschreibung** |
| --- | --- |
| ClusterCertificate |Empfohlen für eine Testumgebung. Dieses Zertifikat ist erforderlich, um die Kommunikation zwischen den Knoten in einem Cluster zu schützen. Sie können zwei verschiedene Zertifikate verwenden: ein primäres Zertifikat und ein sekundäres Zertifikat für Upgrades. Legen Sie den Fingerabdruck des primären Zertifikats im Abschnitt „Thumbprint“ und den Fingerabdruck des sekundären Zertifikats in den Variablen unter „ThumbprintSecondary“ fest. |
| ClusterCertificateCommonNames |Empfohlen für eine Produktionsumgebung. Dieses Zertifikat ist erforderlich, um die Kommunikation zwischen den Knoten in einem Cluster zu schützen. Sie können einen oder zwei allgemeine Clusterzertifikatnamen verwenden. „CertificateIssuerThumbprint“ entspricht dem Fingerabdruck des Zertifikatausstellers dieses Zertifikats. Wenn mehrere Zertifikate mit dem gleichen allgemeinen Namen verwendet werden, können Sie mehrere Fingerabdrücke für Zertifikataussteller angeben.|
| ClusterCertificateIssuerStores |Empfohlen für eine Produktionsumgebung. Dieses Zertifikat entspricht dem Zertifikataussteller des Clusterzertifikats. Sie können den allgemeinen Ausstellernamen und den entsprechenden Speichername unter diesem Abschnitt angeben, anstatt den Fingerabdruck des Zertifikatausstellers unter „ClusterCertificateCommonNames“ bereitzustellen.  Dies vereinfacht das Rollover von Clusterausstellerzertifikaten. Wenn mehrere Clusterzertifikate verwendet werden, können mehrere Zertifikataussteller angegeben werden. Ein leeres IssuerCommonName-Objekt lässt alle Zertifikate in den entsprechenden Speichern zu, die unter „X509StoreNames“ angegeben werden.|
| ServerCertificate |Empfohlen für eine Testumgebung. Dieses Zertifikat wird dem Client angezeigt, wenn versucht wird, eine Verbindung mit diesem Cluster herzustellen. Der Einfachheit halber können Sie für „ClusterCertificate“ und „ServerCertificate“ dasselbe Zertifikat auswählen. Sie können zwei verschiedene Serverzertifikate verwenden: ein primäres Zertifikat und ein sekundäres Zertifikat für Upgrades. Legen Sie den Fingerabdruck des primären Zertifikats im Abschnitt „Thumbprint“ und den Fingerabdruck des sekundären Zertifikats in den Variablen unter „ThumbprintSecondary“ fest. |
| ServerCertificateCommonNames |Empfohlen für eine Produktionsumgebung. Dieses Zertifikat wird dem Client angezeigt, wenn versucht wird, eine Verbindung mit diesem Cluster herzustellen. „CertificateIssuerThumbprint“ entspricht dem Fingerabdruck des Zertifikatausstellers dieses Zertifikats. Wenn mehrere Zertifikate mit dem gleichen allgemeinen Namen verwendet werden, können Sie mehrere Fingerabdrücke für Zertifikataussteller angeben. Der Einfachheit halber können Sie für „ClusterCertificateCommonNames“ und „ServerCertificateCommonNames“ dasselbe Zertifikat auswählen. Sie können einen oder zwei allgemeine Serverzertifikatnamen verwenden. |
| ServerCertificateIssuerStores |Empfohlen für eine Produktionsumgebung. Dieses Zertifikat entspricht dem Zertifikataussteller des Serverzertifikats. Sie können den allgemeinen Ausstellernamen und den entsprechenden Speichername unter diesem Abschnitt angeben, anstatt den Fingerabdruck des Zertifikatausstellers unter „ServerCertificateCommonNames“ bereitzustellen.  Dies vereinfacht das Rollover von Serverausstellerzertifikaten. Wenn mehrere Serverzertifikate verwendet werden, können mehrere Zertifikataussteller angegeben werden. Ein leeres IssuerCommonName-Objekt lässt alle Zertifikate in den entsprechenden Speichern zu, die unter „X509StoreNames“ angegeben werden.|
| ClientCertificateThumbprints |Installieren Sie diese Zertifikate auf den authentifizierten Clients. Auf den Computern, denen Sie Zugriff auf den Cluster gewähren möchten, können Sie verschiedene Clientzertifikate installieren. Legen Sie den Fingerabdruck eines Zertifikats jeweils in der Variablen „CertificateThumbprint“ fest. Wenn Sie „IsAdmin“ auf *TRUE* festlegen, kann der Client, auf dem dieses Zertifikat installiert ist, Administratorverwaltungsaktivitäten für den Cluster durchführen. Wenn „IsAdmin“ auf *FALSE* festgelegt ist, kann der Client mit diesem Zertifikat Aktionen durchführen, die nur für Benutzerzugriffsrechte (üblicherweise Lesezugriffe) zulässig sind. Weitere Informationen zu Rollen finden Sie unter [Rollenbasierte Service Fabric-Zugriffssteuerung](service-fabric-cluster-security.md#service-fabric-role-based-access-control). |
| ClientCertificateCommonNames |Legen Sie den allgemeinen Namen des ersten Clientzertifikats für „CertificateCommonName“ fest. „CertificateIssuerThumbprint“ ist der Fingerabdruck für den Aussteller dieses Zertifikats. Weitere Informationen zu allgemeinen Namen und zum Zertifikataussteller finden Sie unter [Verwenden von Zertifikaten](/dotnet/framework/wcf/feature-details/working-with-certificates). |
| ClientCertificateIssuerStores |Empfohlen für eine Produktionsumgebung. Dieses Zertifikat entspricht dem Zertifikataussteller des Clientzertifikats (sowohl Administrator- als auch Nicht-Administratorrollen). Sie können den allgemeinen Ausstellernamen und den entsprechenden Speichername unter diesem Abschnitt angeben, anstatt den Fingerabdruck des Zertifikatausstellers unter „ClientCertificateCommonNames“ bereitzustellen.  Dies vereinfacht das Rollover von Clientausstellerzertifikaten. Wenn mehrere Clientzertifikate verwendet werden, können mehrere Zertifikataussteller angegeben werden. Ein leeres IssuerCommonName-Objekt lässt alle Zertifikate in den entsprechenden Speichern zu, die unter „X509StoreNames“ angegeben werden.|
| ReverseProxyCertificate |Empfohlen für eine Testumgebung. Dieses optionale Zertifikat kann zum Schutz des [Reverseproxys](service-fabric-reverseproxy.md) angegeben werden kann. Stellen Sie bei Verwendung dieses Zertifikats sicher, dass „reverseProxyEndpointPort“ unter „nodeTypes“ festgelegt ist. |
| ReverseProxyCertificateCommonNames |Empfohlen für eine Produktionsumgebung. Dieses optionale Zertifikat kann zum Schutz des [Reverseproxys](service-fabric-reverseproxy.md) angegeben werden kann. Stellen Sie bei Verwendung dieses Zertifikats sicher, dass „reverseProxyEndpointPort“ unter „nodeTypes“ festgelegt ist. |

Im Folgenden sehen Sie ein Beispiel für eine Clusterkonfiguration mit Cluster-, Server- und Clientzertifikaten. Bei Cluster-, Server- und Reverseproxyzertifikaten können der Fingerabdruck und der allgemeine Name nicht gemeinsam für den gleichen Zertifikattyp konfiguriert werden.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Zertifikatrollover
Wenn Sie anstelle eines Fingerabdrucks den allgemeinen Namen des Zertifikats verwenden, ist für den Zertifikatrollover kein Upgrade der Clusterkonfiguration erforderlich. Stellen Sie bei Upgrades der Fingerabdrücke der Zertifikataussteller sicher, dass die neue Fingerabdruckliste und die alte Liste eine Schnittmenge aufweisen. Sie müssen zuerst ein Konfigurationsupgrade mit den neuen Zertifikataussteller-Fingerabdrücken durchführen und dann die neuen Zertifikate (Cluster-/Serverzertifikate und Zertifikate der Zertifikataussteller) im Speicher installieren. Bewahren Sie das alte Zertifikat des Zertifikatausstellers nach der Installation des neuen Zertifikats mindestens zwei Stunden im Zertifikatspeicher auf.
Bei Verwendung von Zertifikatausstellerspeichern muss kein Konfigurationsupgrade für das Rollover des Ausstellerzertifikats ausgeführt werden. Installieren Sie das neue Ausstellerzertifikat mit einem späteren Ablaufdatum im entsprechenden Zertifikatspeicher, und entfernen Sie das alte Ausstellerzertifikat nach ein paar Stunden.

## <a name="acquire-the-x509-certificates"></a>Erwerben der X.509-Zertifikate
Zum Schutz der Kommunikation zwischen Clustern müssen Sie zuerst X.509-Zertifikate für die Clusterknoten abrufen. Um die Verbindungsherstellung für diesen Cluster auf autorisierte Computer und Benutzer zu beschränken, müssen Sie Zertifikate für die Clientcomputer abrufen und installieren.

Verwenden Sie zum Schutz von Clustern, die Produktionsworkloads ausführen, ein von der [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) signiertes X.509-Zertifikat. Weitere Informationen zum Abrufen dieser Zertifikate finden Sie unter [Abrufen eines Zertifikats](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

Es gibt eine Reihe von Eigenschaften, die das Zertifikat besitzen muss, damit es ordnungsgemäß funktioniert:

* Der Anbieter des Zertifikats muss **Microsoft Enhanced RSA and AES Cryptographic Provider** sein.

* Wenn Sie einen RSA-Schlüssel erstellen, stellen Sie sicher, dass der Schlüssel ein **2048-Bit-Schlüssel** ist.

* Die Erweiterung „Key Usage“ hat den Wert **Digital Signature, Key Encipherment (a0)** .

* Die Erweiterung „Enhanced Key Usage“ weist die Werte **Server Authentication** (OID: 1.3.6.1.5.5.7.3.1) und **Client Authentication** (OID: (1.3.6.1.5.5.7.3.2) auf.

Für Testcluster können Sie ein selbstsigniertes Zertifikat verwenden.

Antworten auf weitere Fragen finden Sie unter [häufig gestellte Fragen zu Zertifikaten](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions).

## <a name="optional-create-a-self-signed-certificate"></a>Optional: Erstellen eines selbstsignierten Zertifikats
Ein selbstsigniertes Zertifikat, das ordnungsgemäß geschützt werden kann, können Sie beispielsweise mithilfe des Skripts „CertSetup.ps1“ im Service Fabric SDK-Ordner im Verzeichnis „C:\Programme\Microsoft SDKs\Service Fabric\ClusterSetup\Secure“ erstellen. Bearbeiten Sie diese Datei, um den Standardnamen des Zertifikats zu ändern. Suchen Sie dabei nach dem Wert „CN=ServiceFabricDevClusterCert“. Führen Sie dieses Skript als `.\CertSetup.ps1 -Install` aus.

Exportieren Sie das Zertifikat nun in eine PFX-Datei mit einem geschützten Kennwort. Rufen Sie zuerst den Zertifikatfingerabdruck ab. 
1. Führen Sie im **Startmenü** **Computerzertifikate verwalten** aus. 

2. Navigieren Sie im Ordner **Lokaler Computer\Persönlich** zum erstellten Zertifikat. 

3. Doppelklicken Sie auf das Zertifikat, um es zu öffnen. Klicken Sie anschließend auf die Registerkarte **Details**, und scrollen Sie nach unten zum Feld **Fingerabdruck**. 

4. Kopieren Sie den Fingerabdruckwert ohne Leerzeichen in den folgenden PowerShell-Befehl. 

5. Ändern Sie den `String`-Wert in ein geeignetes sicheres Kennwort, um ihn zu schützen, und führen Sie Folgendes in PowerShell aus:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Die Details eines auf dem Computer installierten Zertifikats zeigen Sie mithilfe des folgenden PowerShell-Befehls an:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Wenn Sie über ein Azure-Abonnement verfügen, befolgen Sie alternativ die Schritte in [Erstellen eines Service Fabric-Clusters in Azure mithilfe von Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Installieren der Zertifikate
Sobald Sie über Zertifikate verfügen, können Sie diese auf den Clusterknoten installieren. Auf den Knoten muss die neueste Windows PowerShell 3.x-Version installiert sein. Wiederholen Sie die Schritte auf jedem Knoten für Cluster- und Serverzertifikate sowie für sekundäre Zertifikate.

1. Kopieren Sie die PFX-Datei oder -Dateien auf den Knoten.

2. Öffnen Sie ein PowerShell-Fenster als Administrator, und geben Sie die folgenden Befehle ein. Ersetzen Sie *$pswd* durch das Kennwort, das Sie bei der Zertifikaterstellung verwendet haben. Ersetzen Sie *$PfxFilePath* durch den vollständigen Pfad der auf diesen Knoten kopierten PFX-Datei.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Als Nächstes muss die Zugriffssteuerung für dieses Zertifikat durch Ausführen des folgenden Skripts so festgelegt werden, dass der unter dem Netzwerkdienstkonto ausgeführte Service Fabric-Prozess sie nutzen kann. Geben Sie den Fingerabdruck des Zertifikats und den **Netzwerkdienst** für das Dienstkonto an. Sie können überprüfen, ob die ACLs für das Zertifikat richtig sind, indem Sie das Zertifikat in **Start** > **Computerzertifikate verwalten** öffnen und **Alle Aufgaben** > **Private Schlüssel verwalten** ansehen.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Wiederholen Sie die vorherigen Schritte für jedes Serverzertifikat. Mit diesen Schritten können Sie auch die Clientzertifikate auf den Computern installieren, denen Sie Zugriff auf den Cluster gewähren möchten.

## <a name="create-the-secure-cluster"></a>Erstellen des geschützten Clusters
Nach dem Konfigurieren des Abschnitts „security“ der Datei „ClusterConfig.X509.MultiMachine.json“ können Sie mit dem Abschnitt [Erstellen des Clusters](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) fortfahren, um die Knoten zu konfigurieren und den eigenständigen Cluster zu erstellen. Denken Sie beim Erstellen des Clusters daran, die Datei „ClusterConfig.X509.MultiMachine.json“ zu verwenden. Der Befehl kann beispielsweise wie folgt aussehen:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Sobald der gesicherte eigenständige Windows-Cluster ausgeführt wird und Sie die authentifizierten Clients für die Verbindungsherstellung eingerichtet haben, führen Sie die Schritte im Abschnitt [Herstellen einer Verbindung mit einem Cluster mit PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) aus, um die Verbindung herzustellen. Beispiel:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Sie können dann andere PowerShell-Befehle zum Arbeiten mit diesem Cluster ausführen. Führen Sie beispielsweise [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) aus, um eine Liste von Knoten in diesem sicheren Cluster anzuzeigen.


Stellen Sie zum Entfernen des Clusters eine Verbindung mit dem Knoten im Cluster her, in den Sie das Service Fabric-Paket heruntergeladen haben, öffnen Sie eine Befehlszeile, und navigieren Sie zum Paketordner. Führen Sie jetzt den folgenden Befehl aus:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Eine fehlerhafte Zertifikatkonfiguration kann dazu führen, dass der Cluster während der Bereitstellung nicht gestartet wird. Um Sicherheitsprobleme selbst zu diagnostizieren, sehen Sie in der Ereignisanzeigegruppe **Anwendungs- und Dienstprotokolle** > **Microsoft-Service Fabric** nach.
> 
> 
