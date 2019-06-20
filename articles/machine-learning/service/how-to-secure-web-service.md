---
title: Sichern von Webdiensten mit SSL
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie einen mit Azure Machine Learning Service bereitgestellten Webdienst schützen, indem Sie HTTPS aktivieren. HTTPS schützt die von Clients übertragenen Daten mithilfe von TLS (Transport Layer Security), einem Ersatz für SSL (Secure Sockets Layer). Clients verwenden auch HTTPS zum Überprüfen der Identität des Webdiensts verwendet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393823"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Verwenden von SSL zum Schützen eines Webdiensts mit Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie einen mit Azure Machine Learning Service bereitgestellten Webdienst schützen.

Verwenden Sie [HTTPS](https://en.wikipedia.org/wiki/HTTPS), um den Zugriff auf Webdienste zu beschränken und Daten zu sichern, die Clients übermitteln. Mit HTTPS wird die Kommunikation zwischen einem Client und einem Webdienst geschützt, indem sie verschlüsselt wird. Die Verschlüsselung verwendet [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). In einigen Fällen wird TLS immer noch als *Secure Sockets Layer* (SSL) bezeichnet, der Vorgänger von TLS.

> [!TIP]
> Im Azure Machine Learning SDK wird der Begriff „SSL“ für Eigenschaften im Zusammenhang mit sicherer Kommunikation verwendet. Dies bedeutet nicht, dass Ihr Webdienst *TLS* nicht verwendet. SSL ist nur ein gebräuchlicherer Begriff.

Sowohl TLS als auch SSL beruhen auf *digitalen Zertifikaten*, die zur Verschlüsselung und Identitätsüberprüfung verwendet werden. Weitere Informationen zur Funktionsweise digitaler Zertifikate finden Sie im Wikipedia-Thema [Public key infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure) (Public-Key-Infrastruktur).

> [!WARNING]
> Wenn Sie HTTPS nicht für Ihren Webdienst verwenden, sind vom Dienst an andere Benutzer und zurück gesendete Daten möglicherweise im Internet sichtbar.
>
> HTTPS ermöglicht dem Client auch, die Authentizität des Servers, mit dem eine Verbindung hergestellt wird, zu überprüfen. Dieses Feature schützt Clients vor [Man-in-the-Middle-Angriffen](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

Dies ist der allgemeine Prozess zum Sichern eines Webdiensts:

1. Rufen Sie einen Domänennamen ab.

2. Rufen Sie ein digitales Zertifikat ab.

3. Stellen Sie den Webdienst mit aktiviertem SSL bereit, bzw. aktualisieren Sie ihn.

4. Aktualisieren Sie Ihren DNS, so dass er auf den Dienst verweist.

> [!IMPORTANT]
> Wenn Sie für Azure Kubernetes Service (AKS) bereitstellen, können Sie Ihr eigenes Zertifikat erwerben oder ein von Microsoft bereitgestelltes Zertifikat verwenden. Wenn Sie ein Zertifikat von Microsoft verwenden, müssen Sie weder einen Domänennamen noch ein SSL-Zertifikat abrufen. Weitere Informationen finden Sie im Abschnitt [Aktivieren und Bereitstellen von SSL](#enable) dieses Artikels.

Es bestehen bei den verschiedenen [Bereitstellungszielen](how-to-deploy-and-where.md) geringfügige Unterschiede beim Schützen von Webdiensten.

## <a name="get-a-domain-name"></a>Abrufen eines Domänennamens

Wenn Sie nicht bereits über einen Domänennamen verfügen, können Sie einen solchen von einer *Domänennamen-Registrierungsstelle* erwerben. Die Prozesse und Preise der Registrierungsstellen sind unterschiedlich. Die Registrierungsstelle bietet Tools zum Verwalten des Domänennamens. Sie verwenden diese Tools zum Zuordnen eines vollqualifizierten Domänennamens (Fully Qualified Domain Name, FQDN) – z.B. www\.contoso.com – zu der IP-Adresse, die Ihren Webdienst hostet.

## <a name="get-an-ssl-certificate"></a>Beziehen eines SSL-Zertifikats

Es gibt viele Möglichkeiten, ein SSL-Zertifikat (digitales Zertifikat) zu erhalten. Die verbreitetste Methode ist der Erwerb eines Zertifikats von einer *Zertifizierungsstelle* (Certificate Authority, CA). Unabhängig davon, wo Sie das Zertifikat abrufen, benötigen Sie die folgenden Dateien:

* Ein **Zertifikat**. Das Zertifikat muss die vollständige Zertifikatkette enthalten und „PEM-codiert“ sein.
* Einen **Schlüssel**. Der Schlüssel muss auch PEM-codiert sein.

Wenn Sie ein Zertifikat anfordern, müssen Sie den FQDN der Adresse angeben, die Sie für den Webdienst verwenden möchten (z.B. www\.contoso.com). Die in das Zertifikat gestempelte Adresse und die von den Clients verwendete Adresse werden verglichen, um die Identität des Webdiensts zu überprüfen. Wenn diese Adressen nicht übereinstimmen, erhält der Client eine Fehlermeldung.

> [!TIP]
> Wenn die Zertifizierungsstelle das Zertifikat und den Schlüssel nicht als PEM-codierte Dateien bereitstellen kann, können Sie ein Hilfsprogramm wie [OpenSSL](https://www.openssl.org/) zum Ändern des Formats verwenden.

> [!WARNING]
> Verwenden Sie *selbstsignierte* Zertifikate nur für die Entwicklung. Verwenden Sie sie nicht in Produktionsumgebungen. Selbstsignierte Zertifikate können Probleme in Ihren Clientanwendungen verursachen. Weitere Informationen finden Sie in der Dokumentation für die Netzwerkbibliotheken, die Ihre Clientanwendung verwendet.

## <a id="enable"></a> Aktivieren und Bereitstellen von SSL

Legen Sie zum Bereitstellen (oder erneuten Bereitstellen) des Diensts mit aktiviertem SSL den *ssl_enabled*-Parameter auf „True“ fest, wenn zutreffend. Legen Sie den *ssl_certificate*-Parameter auf den Wert der *Zertifikatdatei* fest. Legen Sie den *ssl_key* auf den Wert der *Schlüsseldatei* fest.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Bereitstellen auf AKS und Field-Programmable Gate Array (FPGA)

  > [!NOTE]
  > Die Informationen in diesem Abschnitt gelten auch, wenn ein sicherer Webdienst für die grafische Benutzeroberfläche bereitgestellt wird. Wenn Sie das Python SDK bisher noch nicht verwendet haben, sollten Sie [Was ist das Azure Machine Learning-SDK für Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lesen.

Beim Bereitstellen in AKS können Sie einen neuen AKS-Cluster erstellen oder einen vorhandenen anfügen.
  
-  Wenn Sie einen neuen Cluster erstellen, verwenden Sie **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** .
- Wenn Sie einen vorhandenen Cluster anfügen, verwenden Sie **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** . Beide geben ein Konfigurationsobjekt mit einer **enable_ssl**-Methode zurück.

Die **enable_ssl**-Methode kann ein Zertifikat verwenden, das von Microsoft bereitgestellt wird, oder ein von Ihnen erworbenes Zertifikat.

  * Wenn Sie ein Zertifikat von Microsoft verwenden, müssen Sie den *leaf_domain_label*-Parameter verwenden. Dieser Parameter generiert den DNS-Namen für den Dienst. Der Wert „myservice“ erstellt z.B. den Domänennamen „myservice\<sechs zufällige Zeichen>.\<Azureregion>.cloudapp.azure.com“, wobei \<Azureregion> die Region ist, die den Dienst enthält. Optional können Sie den *overwrite_existing_domain*-Parameter zum Überschreiben des vorhandenen *leaf_domain_label* verwenden.

    Legen Sie zum Bereitstellen (oder erneuten Bereitstellen) des Diensts mit aktiviertem SSL den *ssl_enabled*-Parameter auf „True“ fest, wenn zutreffend. Legen Sie den *ssl_certificate*-Parameter auf den Wert der *Zertifikatdatei* fest. Legen Sie den *ssl_key* auf den Wert der *Schlüsseldatei* fest.

    > [!IMPORTANT]
    > Wenn Sie ein Zertifikat von Microsoft verwenden, müssen Sie nicht Ihr eigenes Zertifikat oder Ihren eigenen Domänennamen erwerben.

    Im folgenden Beispiel wird das Erstellen einer Konfiguration veranschaulicht, die ein SSL-Zertifikat von Microsoft aktiviert:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Wenn Sie *ein Zertifikat verwenden, das Sie erworben haben*, verwenden Sie die Parameter *ssl_cert_pem_file*, *ssl_key_pem_file* und *ssl_cname*. Im folgenden Beispiel wird veranschaulicht, wie *PEM*-Dateien zum Erstellen einer Konfiguration verwendet werden, die ein von Ihnen erworbenes SSL-Zertifikat verwendet:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Weitere Informationen zu *enable_ssl* finden Sie unter [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) und [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Bereitstellen in Azure Container Instances

Wenn Sie in Azure Container Instances bereitstellen möchten, geben Sie Werte für die SSL-Parameter an, wie im folgenden Codeausschnitt gezeigt:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Weitere Informationen finden Sie unter [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Aktualisieren Ihres DNS

Als Nächstes müssen Sie Ihren DNS aktualisieren, so dass er auf den Dienst verweist.

+ **Für Container Instances:**

  Verwenden Sie die Tools von Ihrer Domänennamen-Registrierungsstelle, um den DNS-Eintrag für Ihren Domänennamen zu aktualisieren. Der Eintrag muss auf die IP-Adresse des Diensts verweisen.

  Abhängig von der Registrierungsstelle und dem TTL-Wert (Time-to-Live), der für den Domänennamen konfiguriert ist, kann es einige Minuten bis mehrere Stunden dauern, bevor Clients den Domänennamen auflösen können.

+ **Für AKS**:

  > [!WARNING]
  > Wenn Sie *leaf_domain_label* zum Erstellen des Diensts mit einem Zertifikat von Microsoft verwendet haben, aktualisieren Sie den DNS-Wert für den Cluster nicht manuell. Der Wert sollte automatisch festgelegt werden.

  Aktualisieren Sie das DNS auf der Registerkarte **Konfiguration** der öffentlichen IP-Adresse des AKS-Clusters. (Siehe folgende Abbildung.) Die öffentliche IP-Adresse ist ein Ressourcentyp, der unter der Ressourcengruppe erstellt wird, die die AKS-Agent-Knoten und weitere Netzwerkressourcen enthält.

  ![Azure Machine Learning Service: Sichern von Webdiensten mit SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel werden folgende Themen erläutert:
+ [Nutzen eines als Webdienst bereitgestellten Machine Learning-Modells](how-to-consume-web-service.md)
+ [Sicheres Ausführen von Experimenten und Ziehen von Rückschlüssen innerhalb eines virtuellen Azure-Netzwerks](how-to-enable-virtual-network.md)
