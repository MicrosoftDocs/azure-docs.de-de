---
title: Azure Service Fabric – Setup des Reverseproxys | Microsoft-Dokumentation
description: Grundlegendes zum Einrichten und Konfigurieren des Reverseproxys in Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: v-jamebr
ms.openlocfilehash: 7f1b6f955dd3f59f6c17403b536cf99d666aab08
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60392993"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Einrichten und Konfigurieren des Reverseproxys in Service Fabric
Der Reverseproxy ist ein optionaler Azure Service Fabric-Dienst, mit dessen Hilfe in einem Service Fabric-Cluster ausgeführte Microservices andere Dienste mit HTTP-Endpunkten ermitteln und mit ihnen kommunizieren können. Weitere Informationen finden Sie unter [Reverseproxy in Azure Service Fabric](service-fabric-reverseproxy.md). In diesem Artikel wird das Einrichten und Konfigurieren eines Reverseproxys in Ihrem Cluster veranschaulicht. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Aktivieren des Reverseproxys über das Azure-Portal

Das Azure-Portal bietet eine Option, mit der Sie beim Erstellen eines neuen Service Fabric-Clusters den Reverseproxy aktivieren können. Sie können über das Portal keinen vorhandenen Cluster zur Verwendung des Reverseproxys aktualisieren. 

Um den Reverseproxy beim [Erstellen eines Clusters mithilfe des Azure-Portals](./service-fabric-cluster-creation-via-portal.md) zu konfigurieren, gehen Sie folgendermaßen vor:

1. Wählen Sie in **Schritt 2: Clusterkonfiguration** unter **Knotentypkonfiguration** die Option **Reverseproxy aktivieren** aus.

   ![Aktivieren des Reverseproxys im Portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Optional) Um einen sicheren Reverseproxy zu konfigurieren, müssen Sie ein SSL-Zertifikat konfigurieren. Wählen Sie in **Schritt 3: Sicherheit** unter **Clustersicherheitseinstellungen konfigurieren** für **Konfigurationstyp** die Option **Benutzerdefiniert** aus. Wählen Sie dann unter **Reverseproxy-SSL-Zertifikat** die Option **SSL-Zertifikat für Reverseproxy einschließen** aus, und geben Sie die Details Ihres Zertifikats ein.

   ![Konfigurieren eines sicheren Reverseproxys im Portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Wenn Sie den Reverseproxy beim Erstellen des Clusters nicht mit einem Zertifikat konfigurieren, können Sie dies später über die Resource Manager-Vorlage für die Ressourcengruppe des Clusters nachholen. Weitere Informationen finden Sie unter [Aktivieren des Reverseproxys über Azure Resource Manager-Vorlagen](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Aktivieren des Reverseproxys über Azure Resource Manager-Vorlagen

Für Cluster unter Azure können Sie den Service Fabric-Reverseproxy über die Azure Resource Manager-Vorlage aktivieren. Sie können den Reverseproxy aktivieren, wenn Sie den Cluster erstellen, oder durch Aktualisieren des Clusters zu einem späteren Zeitpunkt. 

Für einen neuen Cluster können Sie [eine benutzerdefinierte Resource Manager-Vorlage erstellen](service-fabric-cluster-creation-via-arm.md) oder eine Beispielvorlage verwenden. 

Sie finden die Resource Manager-Beispielvorlagen, mit denen Sie einen sicheren Reverseproxy für einen Azure-Cluster konfigurieren können, auf GitHub in den [Beispielvorlagen für einen sicheren Reverseproxy](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample). In der Infodatei finden Sie unter [Konfigurieren des HTTPS-Reverseproxys in einem sicheren Cluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) Anweisungen und Vorlagen zum Konfigurieren eines sicheren Reverseproxys mit einem Zertifikat und zum Umgang mit einem Zertifikatrollover.

Für einen vorhandenen Cluster können Sie die Resource Manager-Vorlage für die Ressourcengruppe des Clusters mit [Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) oder [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) exportieren.

Nachdem Sie die Resource Manager-Vorlage erstellt haben, aktivieren Sie den Reverseproxy mit den folgenden Schritten:

1. Definieren Sie im Abschnitt [parameters](../azure-resource-manager/resource-group-authoring-templates.md) der Vorlage einen Port für den Reverseproxy.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Geben Sie den Port für jedes der nodetype-Objekte in [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) im [Ressourcentypenabschnitt](../azure-resource-manager/resource-group-authoring-templates.md) an.

    Der Port wird anhand des Parameternamens reverseProxyEndpointPort identifiziert.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Um SSL-Zertifikate für den Port des Reverseproxys zu konfigurieren, fügen Sie das Zertifikat der ***reverseProxyCertificate***-Eigenschaft in **Microsoft.ServiceFabric/clusters** im [Ressourcentypenabschnitt](../resource-group-authoring-templates.md) hinzu.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Unterstützung für Reverseproxyzertifikate, die sich vom Clusterzertifkat unterscheiden
 Wenn das Reverseproxyzertifikat sich von dem Zertifikat unterscheidet, das den Cluster schützt, sollte das zuvor angegebene Zertifikat auf dem virtuellen Computer installiert und der Zugriffssteuerungsliste (Access Control List, ACL) hinzugefügt worden sein, damit Service Fabric darauf zugreifen kann. Dies kann in [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) im [Ressourcentypenabschnitt](../resource-group-authoring-templates.md) erfolgen. Fügen Sie das Zertifikat für die Installation dem osProfile hinzu. Der Erweiterungsabschnitt der Vorlage kann das Zertifikat in der ACL aktualisieren.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Wenn Sie zum Aktivieren des Reverseproxys auf einem vorhandenen Cluster Zertifikate verwenden, die sich vom Clusterzertifikat unterscheiden, installieren Sie das Reverseproxyzertifikat, und aktualisieren Sie die ACL auf dem Cluster, bevor Sie den Reverseproxy aktivieren. Schließen Sie die Bereitstellung der [Azure Resource Manager-Vorlage](service-fabric-cluster-creation-via-arm.md) mit den oben angegebenen Einstellungen ab, bevor Sie eine Bereitstellung starten, um den Reverseproxy mit den Schritten 1-3 zu aktivieren.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Aktivieren des Reverseproxys auf eigenständigen Clustern

Bei eigenständigen Clustern können Sie den Reverseproxy in der Datei „ClusterConfig.json“ aktivieren. Sie können den Reverseproxy beim Erstellen des Clusters oder durch ein Upgrade der Konfiguration für einen vorhandenen Cluster aktivieren. Weitere Informationen zu den Einstellungen in den „ClusterConfig.json“-Dateien finden Sie unter [Konfigurationseinstellungen für einen eigenständigen Windows-Cluster](./service-fabric-cluster-manifest.md).

Die folgenden Schritte zeigen Ihnen die Einstellungen zum Aktivieren des Reverseproxys und (optional) zum Schützen des Reverseproxys mit einem X.509-Zertifikat. 

1. Legen Sie zum Aktivieren des Reverseproxys den **reverseProxyEndpointPort**-Wert für den Knotentyp unter **properties** in der Konfiguration des Clusters fest. Der folgende JSON-Code zeigt die Einstellung des Reverseproxy-Endpunktports für Knoten vom Typ „NodeType0“ auf 19081:

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Optional) Konfigurieren Sie für einen sicheren Reverseproxy ein Zertifikat im Abschnitt **security** unter **properties**. 
   - Für eine Entwicklungs- oder Testumgebung können Sie die **ReverseProxyCertificate**-Einstellung verwenden:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Für eine Produktionsumgebung wird die **ReverseProxyCertificateCommonNames**-Einstellung empfohlen:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Weitere Informationen zum Konfigurieren und Verwalten von Zertifikaten für einen eigenständigen Cluster sowie weitere Details zum Konfigurieren von Zertifikaten zum Schützen des Reverseproxys finden Sie unter [Schützen eines eigenständigen Windows-Clusters mithilfe von X.509-Zertifikaten](./service-fabric-windows-cluster-x509-security.md).

Nachdem Sie die Datei „ClusterConfig.json“ so geändert haben, dass der Reverseproxy aktiviert wird, befolgen Sie die Anweisungen unter [Aktualisieren der Clusterkonfiguration](service-fabric-cluster-config-upgrade-windows-server.md), um die Änderungen auf Ihren Cluster anzuwenden.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Bereitstellen des Reverseproxys auf einem öffentlichen Port über Azure Load Balancer

Um den Reverseproxy von außerhalb des Azure-Clusters zu adressieren, richten Sie Azure Load Balancer-Regeln und einen Azure-Integritätstest für den Reverseproxyport ein. Diese Schritte können mithilfe des Azure-Portals oder der Resource Manager-Vorlage zu einem beliebigen Zeitpunkt nach der Erstellung des Clusters ausgeführt werden. 

> [!WARNING]
> Wenn Sie den Port des Reverseproxys in Load Balancer konfigurieren, können alle Microservices im Cluster, die einen HTTP-Endpunkt verfügbar machen, von außerhalb des Clusters adressiert werden. Dies bedeutet, dass Microservices, die intern gedacht sind, von einem bestimmten böswilligen Benutzer entdeckt werden können. Dies legt potenziell schwerwiegende Schwachstellen offen, die ausgenutzt werden können:
>
> * Ein böswilliger Benutzer könnte zum Beispiel einen Denial-of-Service-Angriff starten, indem er wiederholt einen internen Dienst aufruft, der keine ausreichend geschützte Angriffsfläche hat.
> * Ein böswilliger Benutzer könnte fehlerhafte Pakete an einen internen Dienst senden, was zu unbeabsichtigtem Verhalten führt.
> * Ein interner Dienst könnte private oder vertrauliche Informationen zurückgeben, die nicht für Dienste außerhalb des Clusters bestimmt sind, wodurch diese vertraulichen Informationen einem böswilligen Benutzer zugänglich gemacht werden könnten. 
>
> Stellen Sie sicher, dass Sie alle potenziellen Auswirkungen auf die Sicherheit Ihres Clusters und der darauf laufenden Apps kennen und verringern, bevor Sie den Reverseproxyport veröffentlichen. 
>

Wenn Sie den Reverseproxy für einen eigenständigen Cluster öffentlich verfügbar machen möchten, hängt die Art und Weise, in der Sie dies tun, von dem System ab, das den Cluster hostet, und wird im Rahmen dieses Artikels nicht behandelt. Die vorherige Warnung zum öffentlichen Verfügbarmachen von Reverseproxys gilt jedoch immer noch.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Verfügbarmachen des Reverseproxys über das Azure-Portal 

1. Klicken Sie im Azure-Portal auf die Ressourcengruppe für Ihren Cluster und dann auf den Lastenausgleich für Ihren Cluster.
2. Um einen Integritätstest für den Port des Reverseproxys hinzuzufügen, klicken Sie im linken Bereich des Lastenausgleichsfensters unter **EINSTELLUNGEN** auf **Integritätstests**. Klicken Sie dann am oberen Rand des Integritätstestfensters auf **Hinzufügen**, geben Sie Details für den Reverseproxyport ein, und klicken Sie dann auf **OK**. Standardmäßig ist der Reverseproxyport 19081, es sei denn, Sie haben ihn bei der Erstellung des Clusters geändert.

   ![Konfigurieren des Reverseproxy-Integritätstests](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Um eine Load Balancer-Regel zum Verfügbarmachen des Ports des Reverseproxys hinzuzufügen, klicken Sie im linken Bereich des Lastenausgleichsfensters unter **EINSTELLUNGEN** auf **Lastenausgleichsregeln**. Klicken Sie dann am oberen Rand des Lastenausgleichsregeln-Fensters auf **Hinzufügen**, und geben Sie Details für den Reverseproxyport ein. Legen Sie für **Port** den Wert des Ports fest, auf dem Sie den Reverseproxy verfügbar machen möchten, für **Back-End-Port** den Wert des Ports, den Sie beim Aktivieren des Reverseproxys festgelegt haben, und für **Integritätstest** den Wert des Integritätstests, den Sie im vorherigen Schritt konfiguriert haben. Legen Sie andere Felder entsprechend fest, und klicken Sie auf **OK**.

   ![Konfigurieren von Lastenausgleichsregeln für den Reverseproxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Verfügbarmachen des Reverseproxys über Azure Resource Manager-Vorlagen

Die folgende JSON-Code verweist auf die gleiche Vorlage, die in [Aktivieren des Reverseproxys über Azure Resource Manager-Vorlagen](#enable-reverse-proxy-via-azure-resource-manager-templates) verwendet wird. In diesem Abschnitt des Dokuments finden Sie Informationen zum Erstellen einer Resource Manager-Vorlage oder Exportieren einer Vorlage für einen vorhandenen Cluster.  Die Änderungen werden in [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) im [Ressourcentypenabschnitt](../resource-group-authoring-templates.md) vorgenommen.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Anpassen des Reverseproxyverhaltens mit Fabric-Einstellungen

Sie können das Verhalten des Reverseproxys für Cluster, die in Azure gehostet werden, über die Fabric-Einstellungen in der Resource Manager-Vorlage, oder für eigenständige Cluster in der Datei ClusterConfig.json anpassen. Einstellungen zur Steuerung des Reverseproxyverhaltens befinden sich im Abschnitt [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) des Abschnitts **fabricSettings** unter dem Clusterabschnitt **properties**. 

Sie können beispielsweise den Wert von **DefaultHttpRequestTimeout** festlegen, um das Timeout für Anforderungen an den Reverseproxy wie im folgenden JSON-Code auf 180 Sekunden festzulegen:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Weitere Informationen zum Aktualisieren von Fabric-Einstellungen für Azure-Cluster finden Sie unter [Anpassen von Clustereinstellungen mithilfe von Resource Manager-Vorlagen](service-fabric-cluster-config-upgrade-azure.md). Informationen für eigenständige Cluster finden Sie unter [Anpassen von Clustereinstellungen für eigenständige Cluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Verschiedene Fabric-Einstellungen werden verwendet, um sichere Kommunikation zwischen dem Reverseproxy und Diensten einzurichten. Ausführliche Informationen zu diesen Einstellungen finden Sie unter [Herstellen einer Verbindung mit einem sicheren Dienst mit dem Reverseproxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Nächste Schritte
* [Einrichten der Weiterleitung an einen sicheren HTTP-Dienst mit dem Reverseproxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Konfigurationsoptionen für den Reverseproxy finden Sie im [Abschnitt zu ApplicationGateway/Http in „Anpassen von Service Fabric-Clustereinstellungen und der Fabric-Upgraderichtlinie“](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
