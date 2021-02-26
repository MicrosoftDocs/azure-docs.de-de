---
title: Absichern von Webdiensten mit TLS
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie HTTPS mit TLS 1.2 aktivieren, um einen über Azure Machine Learning bereitgestellten Webdienst zu schützen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 01/04/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 68163f7ca8cc1b37bda4e1224330f966265554c2
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980471"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning


In diesem Artikel erfahren Sie, wie Sie einen über Azure Machine Learning bereitgestellten Webdienst schützen.

Verwenden Sie [HTTPS](https://en.wikipedia.org/wiki/HTTPS), um den Zugriff auf Webdienste zu beschränken und Daten zu sichern, die Clients übermitteln. Mit HTTPS wird die Kommunikation zwischen einem Client und einem Webdienst geschützt, indem sie verschlüsselt wird. Die Verschlüsselung verwendet [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). In einigen Fällen wird TLS immer noch als *Secure Sockets Layer* (SSL) bezeichnet, der Vorgänger von TLS.

> [!TIP]
> Im Azure Machine Learning SDK wird der Begriff „SSL“ für Eigenschaften im Zusammenhang mit sicherer Kommunikation verwendet. Dies bedeutet nicht, dass Ihr Webdienst *TLS* nicht verwendet. SSL ist nur ein gebräuchlicherer Begriff.
>
> Insbesondere unterstützen über Azure Machine Learning bereitgestellte Webdienste die TLS-Version 1.2 für AKS und ACI. Wenn Sie mit ACI-Bereitstellungen arbeiten oder eine ältere TLS-Version verwenden, empfiehlt sich eine erneute Bereitstellung, um die neueste TLS-Version zu erhalten.
>
> Die TLS-Version 1.3 für AKS-Rückschlüsse über Azure Machine Learning werden nicht unterstützt.

Sowohl TLS als auch SSL beruhen auf *digitalen Zertifikaten*, die zur Verschlüsselung und Identitätsüberprüfung verwendet werden. Weitere Informationen zur Funktionsweise digitaler Zertifikate finden Sie im Wikipedia-Thema [Public key infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure) (Public-Key-Infrastruktur).

> [!WARNING]
> Wenn Sie HTTPS nicht für Ihren Webdienst verwenden, sind vom Dienst an andere Benutzer und zurück gesendete Daten möglicherweise im Internet sichtbar.
>
> HTTPS ermöglicht dem Client auch, die Authentizität des Servers, mit dem eine Verbindung hergestellt wird, zu überprüfen. Dieses Feature schützt Clients vor [Man-in-the-Middle-Angriffen](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

Dies ist der allgemeine Prozess zum Sichern eines Webdiensts:

1. Rufen Sie einen Domänennamen ab.

2. Rufen Sie ein digitales Zertifikat ab.

3. Stellen Sie den Webdienst mit aktiviertem TLS bereit oder aktualisieren Sie ihn.

4. Aktualisieren Sie Ihren DNS, so dass er auf den Dienst verweist.

> [!IMPORTANT]
> Wenn Sie für Azure Kubernetes Service (AKS) bereitstellen, können Sie Ihr eigenes Zertifikat erwerben oder ein von Microsoft bereitgestelltes Zertifikat verwenden. Wenn Sie ein Zertifikat von Microsoft verwenden, müssen Sie weder einen Domänennamen noch ein TLS/SSL-Zertifikat abrufen. Weitere Informationen finden Sie im Abschnitt [Aktivieren und Bereitstellen von TLS](#enable) dieses Artikels.

Es bestehen bei den verschiedenen [Bereitstellungszielen](how-to-deploy-and-where.md) geringfügige Unterschiede beim Schützen von Webdiensten.

## <a name="get-a-domain-name"></a>Abrufen eines Domänennamens

Wenn Sie nicht bereits über einen Domänennamen verfügen, können Sie einen solchen von einer *Domänennamen-Registrierungsstelle* erwerben. Die Prozesse und Preise der Registrierungsstellen sind unterschiedlich. Die Registrierungsstelle bietet Tools zum Verwalten des Domänennamens. Sie verwenden diese Tools zum Zuordnen eines vollqualifizierten Domänennamens (Fully Qualified Domain Name, FQDN) – z.B. www\.contoso.com – zu der IP-Adresse, die Ihren Webdienst hostet.

## <a name="get-a-tlsssl-certificate"></a>Abrufen eines TLS/SSL-Zertifikats

Es gibt viele Möglichkeiten, ein TLS/SSL-Zertifikat (digitales Zertifikat) zu erhalten. Die verbreitetste Methode ist der Erwerb eines Zertifikats von einer *Zertifizierungsstelle* (Certificate Authority, CA). Unabhängig davon, wo Sie das Zertifikat abrufen, benötigen Sie die folgenden Dateien:

* Ein **Zertifikat**. Das Zertifikat muss die vollständige Zertifikatkette enthalten und „PEM-codiert“ sein.
* Einen **Schlüssel**. Der Schlüssel muss auch PEM-codiert sein.

Wenn Sie ein Zertifikat anfordern, müssen Sie den FQDN der Adresse angeben, die Sie für den Webdienst verwenden möchten (z.B. www\.contoso.com). Die in das Zertifikat gestempelte Adresse und die von den Clients verwendete Adresse werden verglichen, um die Identität des Webdiensts zu überprüfen. Wenn diese Adressen nicht übereinstimmen, erhält der Client eine Fehlermeldung.

> [!TIP]
> Wenn die Zertifizierungsstelle das Zertifikat und den Schlüssel nicht als PEM-codierte Dateien bereitstellen kann, können Sie ein Hilfsprogramm wie [OpenSSL](https://www.openssl.org/) zum Ändern des Formats verwenden.

> [!WARNING]
> Verwenden Sie *selbstsignierte* Zertifikate nur für die Entwicklung. Verwenden Sie sie nicht in Produktionsumgebungen. Selbstsignierte Zertifikate können Probleme in Ihren Clientanwendungen verursachen. Weitere Informationen finden Sie in der Dokumentation für die Netzwerkbibliotheken, die Ihre Clientanwendung verwendet.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Aktivieren und Bereitstellen von TLS

Sie können bei einer **AKS-Bereitstellung** den TLS-Abschluss aktivieren, wenn Sie im AML-Arbeitsbereich einen [AKS-Cluster erstellen oder anfügen](how-to-create-attach-kubernetes.md). Zum Zeitpunkt der AKS-Modellimplementierung können Sie den TLS-Abschluss mit dem Konfigurationsobjekt der Bereitstellung deaktivieren. Andernfalls wird der TLS-Abschluss bei allen AKS-Modellimplementierungen standardmäßig beim Erstellen oder Anfügen des AKS-Clusters aktiviert.

Für die ACI-Bereitstellung können Sie den TLS-Abschluss bei der Modellimplementierung mit dem Konfigurationsobjekt der Bereitstellung aktivieren.


### <a name="deploy-on-azure-kubernetes-service"></a>Bereitstellen auf Azure Kubernetes Service

  > [!NOTE]
  > Die Informationen in diesem Abschnitt gelten auch, wenn ein sicherer Webdienst für den Designer bereitgestellt wird. Wenn Sie das Python SDK bisher noch nicht verwendet haben, sollten Sie [Was ist das Azure Machine Learning-SDK für Python?](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) lesen.

Wenn Sie in einem AML-Arbeitsbereich [einen AKS-Cluster erstellen oder anfügen](how-to-create-attach-kubernetes.md), können Sie den TLS-Abschluss mit den Konfigurationsobjekten **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** und **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** aktivieren. Bei beiden Methoden wird ein Konfigurationsobjekt zurückgegeben, das über eine **enable_ssl**-Methode verfügt. Mit dieser **enable_ssl**-Methode können Sie TLS aktivieren.

Sie können TLS mit einem Microsoft-Zertifikat oder einem von der Zertifizierungsstelle erworbenen benutzerdefinierten Zertifikat aktivieren. 

* Wenn Sie ein **Zertifikat von Microsoft** verwenden, müssen Sie den *leaf_domain_label*-Parameter verwenden. Dieser Parameter generiert den DNS-Namen für den Dienst. Mit dem Wert „contoso“ wird z. B. der Domänenname „contoso\<six-random-characters>.\<azureregion>.cloudapp.azure.com“ erstellt, wobei \<azureregion> die Region ist, in der sich der Dienst befindet. Optional können Sie den *overwrite_existing_domain*-Parameter zum Überschreiben des vorhandenen *leaf_domain_label* verwenden. Im folgenden Beispiel wird das Erstellen einer Konfiguration veranschaulicht, die TLS mit einem Zertifikat von Microsoft aktiviert:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Wenn Sie ein Zertifikat von Microsoft verwenden, müssen Sie nicht Ihr eigenes Zertifikat oder Ihren eigenen Domänennamen erwerben.

    > [!WARNING]
    > Wenn Ihr AKS-Cluster mit einem internen Lastenausgleich konfiguriert ist, wird die Verwendung eines von Microsoft bereitgestellten Zertifikats __nicht unterstützt__. Sie müssen dann für die Aktivierung von TLS ein benutzerdefiniertes Zertifikat verwenden.

* Wenn Sie **ein benutzerdefiniertes Zertifikat verwenden, das Sie erworben haben**, verwenden Sie die Parameter *ssl_cert_pem_file*, *ssl_key_pem_file* und *ssl_cname*. Im folgenden Beispiel wird veranschaulicht, wie PEM-Dateien zum Erstellen einer Konfiguration verwendet werden, die ein von Ihnen erworbenes TLS/SSL-Zertifikat verwendet:
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Weitere Informationen zu *enable_ssl* finden Sie unter [AksProvisioningConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) und [AksAttachConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Bereitstellen in Azure Container Instances

Wenn Sie in Azure Container Instances bereitstellen möchten, geben Sie Werte für die TLS-Parameter an, wie im folgenden Codeausschnitt gezeigt:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Weitere Informationen finden Sie unter [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aktualisieren Ihres DNS

Bei einer AKS-Bereitstellung mit einem benutzerdefinierten Zertifikat oder bei einer ACI-Bereitstellung müssen Sie Ihren DNS-Eintrag so aktualisieren, dass er auf die IP-Adresse des Bewertungsendpunkts verweist.

  > [!IMPORTANT]
  > Wenn Sie ein Zertifikat von Microsoft für die AKS-Bereitstellung verwenden, müssen Sie den DNS-Wert für den Cluster nicht manuell aktualisieren. Der Wert sollte automatisch festgelegt werden.

Sie können die folgenden Schritte ausführen, um den DNS-Eintrag für Ihren benutzerdefinierten Domänennamen zu aktualisieren:
* Sie finden die IP-Adresse des Bewertungsendpunkts im Bewertungsendpunkt-URI, der normalerweise das Format *http://104.214.29.152:80/api/v1/service/<service-name>/score* aufweist. 
* Verwenden Sie die Tools von Ihrer Domänennamen-Registrierungsstelle, um den DNS-Eintrag für Ihren Domänennamen zu aktualisieren. Der Eintrag muss auf die IP-Adresse des Bewertungsendpunkts verweisen.
* Nach dem Aktualisieren des DNS-Eintrags können Sie die DNS-Auflösung mit dem Befehl *nslookup custom-domain-name* überprüfen. Wenn der DNS-Eintrag ordnungsgemäß aktualisiert wurde, verweist der benutzerdefinierte Domänenname auf die IP-Adresse des Bewertungsendpunkts.
* Abhängig von der Registrierungsstelle und dem TTL-Wert (Time-to-Live), der für den Domänennamen konfiguriert ist, kann es einige Minuten bis mehrere Stunden dauern, bevor Clients den Domänennamen auflösen können.


## <a name="update-the-tlsssl-certificate"></a>Aktualisieren des TSL/SSL-Zertifikats

TLS/SSL-Zertifikate laufen ab und müssen erneuert werden. In der Regel erfolgt dies jedes Jahr. Verwenden Sie die Informationen in den folgenden Abschnitten, um das Zertifikat für in Azure Kubernetes Service bereitgestellte Modelle zu aktualisieren und zu erneuern:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualisieren eines von Microsoft generierten Zertifikats

Wenn das Zertifikat ursprünglich von Microsoft generiert wurde (bei Verwendung von *leaf_domain_label* zum Erstellen des Diensts), wird es bei Bedarf **automatisch verlängert**. Wenn Sie die Datei manuell verlängern möchten, verwenden Sie eines der folgenden Beispiele, um das Zertifikat zu aktualisieren:

> [!IMPORTANT]
> * Wenn das vorhandene Zertifikat noch gültig ist, verwenden Sie `renew=True` (SDK) oder `--ssl-renew` (CLI), um die Konfiguration für die Erneuerung zu erzwingen. Wenn das vorhandene Zertifikat beispielsweise noch 10 Tage lang gültig ist und Sie nicht `renew=True` verwenden, wird das Zertifikat möglicherweise nicht erneuert.
> * Bei der ursprünglichen Bereitstellung des Diensts wurde `leaf_domain_label` zum Erstellen eines DNS-Namens nach dem Muster `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` verwendet. Verwenden Sie den ursprünglichen Wert für `leaf_domain_label`, um den vorhandenen Namen (einschließlich der ursprünglich generierten 6 Ziffern) beizubehalten. Geben Sie die generierten 6 Ziffern nicht an.

**Verwenden des SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Verwenden der Befehlszeilenschnittstelle**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Weitere Informationen finden Sie in den folgenden Referenzen:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aktualisieren eines benutzerdefinierten Zertifikats

Wenn das Zertifikat ursprünglich von einer Zertifizierungsstelle generiert wurde, gehen Sie wie folgt vor:

1. Verwenden Sie die von der Zertifizierungsstelle bereitgestellte Dokumentation, um das Zertifikat zu erneuern. Bei diesem Vorgang werden neue Zertifikatsdateien erstellt.

1. Aktualisieren Sie den Dienst mithilfe des SDK oder über die Befehlszeilenschnittstelle mit dem neuen Zertifikat:

    **Verwenden des SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Verwenden der Befehlszeilenschnittstelle**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Weitere Informationen finden Sie in den folgenden Referenzen:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

## <a name="disable-tls"></a>Deaktivieren von TLS

Erstellen Sie zum Deaktivieren von TLS für ein in Azure Kubernetes Service bereitgestelltes Modell ein `SslConfiguration`-Element mit `status="Disabled"`, und führen Sie dann die Aktualisierung aus:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel werden folgende Themen erläutert:
+ [Nutzen eines als Webdienst bereitgestellten Machine Learning-Modells](how-to-consume-web-service.md)
+ [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)