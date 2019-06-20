---
title: Erstellen einer App Service-Umgebung mit einer Resource Manager-Vorlage – Azure
description: Dieser Artikel beschreibt das Erstellen einer externen oder ILB-basierten Azure App Service-Umgebung mithilfe einer Resource Manager-Vorlage.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bdf722ffa7a7c499ff256392886e0f229f27c7a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137080"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Erstellen einer ASE mit einer Azure Resource Manager-Vorlage

## <a name="overview"></a>Übersicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service-Umgebungen (App Service Environments, ASEs) können mit einem über das Internet zugänglichen Endpunkt oder einem Endpunkt an einer internen Adresse in einem virtuellen Azure-Netzwerk (Virtual Network, VNET) erstellt werden. Beim Erstellen mit einem internen Endpunkt wird dieser Endpunkt über eine Azure-Komponente, den sogenannten internen Lastenausgleich (Internal Load Balancer, ILB), bereitgestellt. Eine ASE an einer internen IP-Adresse wird als ILB-ASE bezeichnet. Eine ASE mit einem öffentlichen Endpunkt wird als externe ASE bezeichnet. 

Eine ASE kann über das Azure-Portal oder mit einer Azure Resource Manager-Vorlage erstellt werden. In diesem Artikel werden die Schritte und Syntaxelemente beschrieben, die Sie zum Erstellen einer externen ASE oder einer ILB-ASE mit Resource Manager-Vorlagen benötigen. Informationen zum Erstellen einer ASE im Azure-Portal finden Sie unter [Erstellen einer externen ASE][MakeExternalASE] oder [Erstellen einer ILB-ASE][MakeILBASE].

Beim Erstellen einer ASE im Azure-Portal können Sie entweder gleichzeitig ein VNET erstellen oder ein vorhandenes VNET auswählen, in dem die Bereitstellung erfolgt. Wenn Sie eine ASE aus einer Vorlage erstellen, benötigen Sie zunächst Folgendes: 

* Ein Resource Manager-VNET.
* Ein Subnetz in diesem VNET. Wir empfehlen eine ASE-Subnetzgröße von `/24` mit 256 Adressen, um genügend Platz für zukünftiges Wachstum und Skalierungsanforderungen zu haben. Nachdem die ASE erstellt wurde, können Sie die Größe nicht mehr ändern.
* Die Ressourcen-ID aus Ihrem VNET. Sie finden diese Informationen im Azure-Portal in den Eigenschaften Ihres virtuellen Netzwerks.
* Das Abonnement, in dem die Bereitstellung erfolgen soll.
* Der Speicherort, in dem die Bereitstellung erfolgen soll.

Gehen Sie zum Automatisieren der ASE-Erstellung wie folgt vor:

1. Erstellen Sie die ASE aus einer Vorlage. Wenn Sie eine externe ASE erstellen, sind Sie nach diesem Schritt fertig. Wenn Sie eine ILB-ASE erstellen, sind einige weitere Schritte erforderlich.

2. Nach dem Erstellen Ihrer ILB-ASE wird ein SSL-Zertifikat hochgeladen, das Ihrer ILB-ASE-Domäne entspricht.

3. Das hochgeladene SSL-Zertifikat wird der ILB-ASE als SSL-Standardzertifikat zugewiesen.  Dieses Zertifikat wird für SSL-Datenverkehr zu Apps in der ILB-ASE verwendet, wenn die Apps die allgemeine Stammdomäne verwenden, die der ASE zugewiesen ist (z.B. https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Erstellen der ASE
Eine Resource Manager-Vorlage, die eine ASE erstellt, und die zugehörige Parameterdatei finden Sie auf GitHub [in diesem Beispiel][quickstartasev2create].

Wenn Sie eine ILB-ASE erstellen möchten, verwenden Sie diese [Beispiele][quickstartilbasecreate] für Resource Manager-Vorlagen. Diese sind für diesen Anwendungsfall bestimmt. Die meisten Parameter in der Datei *azuredeploy.parameters.json* gelten für die Erstellung sowohl einer ILB-ASE als auch einer externen ASE. In der folgenden Liste sind die Parameter aufgeführt, die für die Erstellung einer ILB-ASE eine besondere Bedeutung haben bzw. nur dafür benötigt werden:

* *internalLoadBalancingMode*: Sollte in den meisten Fällen auf „3“ festgelegt werden. Dies bedeutet, dass sowohl der HTTP/HTTPS-Datenverkehr über die Ports 80/443 als auch die Steuer-/Datenkanalports, auf die der FTP-Dienst in der ASE lauscht, an eine per ILB zugeordnete Adresse des virtuellen Netzwerks gebunden sind. Wenn diese Eigenschaft auf „2“ festgelegt wird, sind nur die Ports, die sich auf den FTP-Dienst beziehen (sowohl Steuer- als auch Datenkanäle) an eine ILB-Adresse gebunden. Der HTTP-/HTTPS-Datenverkehr verbleibt an der öffentlichen VIP.
* *dnsSuffix*: Dieser Parameter definiert die Standardstammdomäne, die der ASE zugewiesen wird. In der öffentlichen Variante von Azure App Service lautet die Standardstammdomäne für alle Web-Apps *azurewebsites.net*. Da eine ILB-ASE sich intern im virtuellen Netzwerk eines Kunden befindet, wäre es nicht sinnvoll, die Standardstammdomäne des öffentlichen Diensts zu verwenden. Stattdessen sollte eine ILB-ASE über eine Standardstammdomäne verfügen, für die die Verwendung im internen virtuellen Netzwerk eines Unternehmens sinnvoll ist. Die Contoso Corporation könnte beispielsweise die Standardstammdomäne *internal-contoso.com* für Apps verwenden, für die beabsichtigt ist, dass sie nur im virtuellen Netzwerk von Contoso auflösbar und zugänglich sind. 
* *ipSslAddressCount*: Für diesen Parameter wird in der Datei *azuredeploy.json* automatisch der Wert 0 verwendet, da ILB-ASEs nur über eine einzige ILB-Adresse verfügen. Es gibt keine expliziten IP-SSL-Adressen für eine ILB-ASE. Daher muss der IP-SSL-Adresspool für eine ILB-ASE auf 0 (null) festgelegt werden. Andernfalls tritt ein Bereitstellungsfehler auf. 

Nachdem alle notwendigen Informationen in der Datei *azuredeploy.parameters.json* eingegeben sind, erstellen Sie die ASE mithilfe des PowerShell-Codeausschnitts. Ändern Sie die Dateipfade in die Speicherorte, die in der Azure Resource Manager-Vorlagendatei auf Ihrem Computer angegeben sind. Denken Sie daran, Ihre eigenen Werte für den Resource Manager-Bereitstellungsnamen und den Ressourcengruppennamen anzugeben:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Die Erstellung einer ASE dauert etwa eine Stunde. Danach wird die ASE im Port in der Liste der ASEs für das Abonnement angezeigt, in dem die Bereitstellung ausgelöst wurde.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Hochladen und Konfigurieren des SSL-Standardzertifikats
Der ASE muss ein SSL-Zertifikat als SSL-Standardzertifikat zugeordnet werden, das zum Herstellen von SSL-Verbindungen mit Apps verwendet wird. Wenn das DNS-Standardsuffix der ASE *internal-contoso.com* lautet, ist für eine Verbindung mit https://some-random-app.internal-contoso.com ein SSL-Zertifikat erforderlich, das für * *.internal-contoso.com* gültig ist. 

Ein gültiges SSL-Zertifikat können Sie erhalten, indem Sie interne Zertifizierungsstellen verwenden, ein Zertifikat von einem externen Aussteller erwerben oder ein selbstsigniertes Zertifikat verwenden. Unabhängig von der Quelle des SSL-Zertifikats müssen die folgenden Zertifikatattribute ordnungsgemäß konfiguriert werden:

* **Betreff**: Dieses Attribut muss auf * *.your-root-domain-here.com* festgelegt werden.
* **Alternativer Antragstellername**: Dieses Attribut muss sowohl * *.your-root-domain-here.com* als auch * *.scm.your-root-domain-here.com* enthalten. SSL-Verbindungen mit der SCM/Kudu-Website, die jeder App zugeordnet ist, verwenden eine Adresse im Format *your-app-name.scm.your-root-domain-here.com*.

Wenn ein gültiges SSL-Zertifikat vorhanden ist, sind zwei weitere Vorbereitungsschritte erforderlich. Konvertieren Sie das SSL-Zertifikat in eine PFX-Datei, und speichern Sie es. Denken Sie daran, dass die PFX-Datei alle Zwischenzertifikate und Stammzertifikate enthalten muss. Sichern Sie es mit einem Kennwort.

Die PFX-Datei muss in eine Base64-Zeichenfolge konvertiert werden, da das SSL-Zertifikat mithilfe einer Resource Manager-Vorlage hochgeladen wird. Da es sich bei Resource Manager-Vorlagen um Textdateien handelt, muss die PFX-Datei in eine Base64-Zeichenfolge konvertiert werden. Auf diese Weise kann sie als Parameter in die Vorlage eingeschlossen werde.

Verwenden Sie nachstehenden PowerShell-Codeausschnitt für folgende Zwecke:

* Generieren eines selbstsignierten Zertifikats
* Exportieren des Zertifikats als PFX-Datei
* Konvertieren der PFX-Datei in eine Base64-codierte Zeichenfolge
* Speichern der Base64-codierten Zeichenfolge als separate Datei 

Dieser PowerShell-Code für die Base64-Codierung wurde aus dem [Blog zu PowerShell-Skripts][examplebase64encoding] übernommen und angepasst:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Nachdem das SSL-Zertifikat erfolgreich generiert und in eine Base64-codierte Zeichenfolge konvertiert wurde, verwenden Sie die Resource Manager-Vorlage [Configure the default SSL certificate][quickstartconfiguressl] (Konfigurieren des SSL-Standardzertifikats) auf GitHub. 

Die Parameter in der Datei *azuredeploy.parameters.json* sind hier aufgeführt:

* *appServiceEnvironmentName*: Der Name der ILB-ASE, die konfiguriert wird.
* *existingAseLocation*: Die Textzeichenfolge mit der Azure-Region, in der die ILB-ASE bereitgestellt wurde.  Beispiel:  „USA, Süden-Mitte“
* *pfxBlobString*: Die Base64-codierte Zeichenfolgendarstellung der PFX-Datei. Verwenden Sie den oben gezeigten Codeausschnitt, und kopieren Sie die Zeichenfolge in „exportedcert.pfx.b64“. Fügen Sie sie als Wert des Attributs *pfxBlobString* ein.
* *password*: Das Kennwort, das zum Schützen der PFX-Datei verwendet wird.
* *certificateThumbprint*: Der Fingerabdruck des Zertifikats. Wenn Sie diesen Wert aus PowerShell abrufen (z.B. als *$certificate.Thumbprint* aus dem Codeausschnitt weiter oben), können Sie den Wert unverändert nutzen. Falls Sie den Wert aus dem Windows-Zertifikatdialogfeld kopieren, müssen Sie die überflüssigen Leerzeichen entfernen. *certificateThumbprint* sollte etwa wie folgt aussehen: AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: Ein benutzerfreundlicher Zeichenfolgenbezeichner zum Identifizieren des Zertifikats, den Sie selbst wählen können. Der Name wird als Teil des eindeutigen Resource Manager-Bezeichners für die Entität *Microsoft.Web/certificates* verwendet, die das SSL-Zertifikat repräsentiert. Der Name *muss* auf folgendes Suffix enden: \_yourASENameHere_InternalLoadBalancingASE. Dieses Suffix wird im Azure-Portal als Indikator dafür verwendet, dass das Zertifikat zum Sichern einer für den ILB geeigneten ASE genutzt wird.

Ein gekürztes Beispiel für *azuredeploy.parameters.json* sehen Sie hier:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Nachdem alle notwendigen Informationen in der Datei *azuredeploy.parameters.json* eingegeben sind, konfigurieren Sie das SSL-Standardzertifikat mithilfe des PowerShell-Codeausschnitts. Ändern Sie die Dateipfade in die Speicherorte, in denen sich die Resource Manager-Vorlagendateien auf Ihrem Computer befinden. Denken Sie daran, Ihre eigenen Werte für den Resource Manager-Bereitstellungsnamen und den Ressourcengruppennamen anzugeben:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Es dauert in jedem ASE-Front-End etwa 40 Minuten, bis die Änderungen angewendet wurden. Bei einer ASE mit Standardgröße, die zwei Front-Ends nutzt, dauert es beispielsweise ungefähr eine Stunde und 20 Minuten, bis der Vorgang für die Vorlage abgeschlossen ist. Während der Ausführung der Vorlage kann die ASE nicht skaliert werden.  

Nachdem die Ausführung der Vorlage abgeschlossen ist, kann über HTTPS auf Apps in der ILB-ASE zugegriffen werden. Die Verbindungen werden mit dem SSL-Standardzertifikat gesichert. Das SSL-Standardzertifikat wird verwendet, wenn der Zugriff auf Apps in der ILB-ASE mit einer Kombination aus dem Anwendungsnamen und dem Standardhostnamen angefordert wird. Für https://mycustomapp.internal-contoso.com wird beispielsweise das SSL-Standardzertifikat für * *.internal-contoso.com* verwendet.

Entwickler können jedoch genau wie bei Apps, die im öffentlichen Mehrinstanzendienst ausgeführt werden, benutzerdefinierte Hostnamen für einzelne Apps konfigurieren. Sie können auch eindeutige SNI-SSL-Zertifikatbindungen für einzelne Apps konfigurieren.

## <a name="app-service-environment-v1"></a>App Service-Umgebung v1 ##
Es gibt zwei Versionen der App Service-Umgebung: ASEv1 und ASEv2. Die oben aufgeführten Informationen basieren auf ASEv2. In diesem Abschnitt werden die Unterschiede zwischen ASEv1 und ASEv2 aufgezeigt.

In ASEv1 verwalten Sie alle Ressourcen manuell. Dies schließt Front-Ends, Worker und IP-Adressen ein, die für IP-basiertes SSL verwendet werden. Bevor Sie Ihren App Service-Plan horizontal hochskalieren können, müssen Sie zunächst den Workerpool skalieren, den Sie zum Hosten verwenden möchten.

ASEv1 verwendet ein anderes Preismodell als ASEv2. In ASEv1 bezahlen Sie jede zugewiesene vCPU. Dazu gehören vCPUs für Front-Ends oder Worker, die keine Workloads hosten. In ASEv1 beträgt die maximale Standardskalierungsgröße einer ASE insgesamt 55 Hosts. Dazu gehören Worker und Front-Ends. ASEv1 hat den Vorteil, dass die Bereitstellung in einem klassischen virtuellen Netzwerk sowie in einem virtuellen Resource Manager-Netzwerk möglich ist. Weitere Informationen zu ASEv1 finden Sie unter [Einführung in die App Service-Umgebung v1][ASEv1Intro].

Informationen zum Erstellen einer ASEv1 mithilfe einer Resource Manager-Vorlage finden Sie unter [Erstellen einer ILB-ASE v1 mit einer Resource Manager-Vorlage][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
