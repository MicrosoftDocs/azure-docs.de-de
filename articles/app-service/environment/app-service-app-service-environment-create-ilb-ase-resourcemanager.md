---
title: Erstellen einer ILB-ASE mit Azure Resource Manager-Vorlagen – App Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit Azure Resource Manager-Vorlagen einen internen Load Balancer erstellen.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 35e0dc5dabaf1602b87ec6a8be86ed609f3ea12f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130754"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Gewusst wie: Erstellen einer ILB-ASE mit Azure Resource Manager-Vorlagen

> [!NOTE] 
> In diesem Artikel wird die App Service-Umgebung v1 behandelt. Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
App Service-Umgebungen (ASEs) können mit einer internen Adresse eines virtuellen Netzwerks erstellt werden, anstatt mit einer öffentlichen VIP.  Diese interne Adresse wird von einer Azure-Komponente bereitgestellt, die als interner Load Balancer (ILB) bezeichnet wird.  Eine ILB-ASE kann mit dem Azure-Portal erstellt werden.  Für die Erstellung kann auch eine Automation genutzt werden, indem Azure Resource Manager-Vorlagen eingesetzt werden.  In diesem Artikel werden die Schritte und Syntaxelemente beschrieben, die zum Erstellen einer ILB-ASE mit Azure Resource Manager-Vorlagen benötigt werden.

Die Automatisierung einer ILB-ASE-Erstellung umfasst drei Schritte:

1. Zuerst wird die Basis-ASE in einem virtuellen Netzwerk erstellt, indem anstelle einer öffentlichen VIP die Adresse eines internen Load Balancers verwendet wird.  Im Rahmen dieses Schritts wird der ILB-ASE ein Stammdomänenname zugewiesen.
2. Nachdem die ILB-ASE erstellt wurde, wird ein SSL-Zertifikat hochgeladen.  
3. Das hochgeladene SSL-Zertifikat wird der ILB-ASE explizit als SSL-Standardzertifikat zugewiesen.  Dieses SSL-Zertifikat wird für SSL-Datenverkehr zu Apps in der ILB-ASE verwendet, wenn die Apps mit der allgemeinen Stammdomäne adressiert werden, die der ASE zugewiesen ist (z.B. https://someapp.mycustomrootcomain.com) ).

## <a name="creating-the-base-ilb-ase"></a>Erstellen der Basis-ILB-ASE
Eine Azure Resource Manager-Beispielvorlage und die zugeordnete Parameterdatei stehen auf [GitHub][quickstartilbasecreate] zur Verfügung.

Die meisten Parameter in der Datei *azuredeploy.parameters.json* gelten für die Erstellung beider ILB-ASEs sowie für ASEs, die an eine öffentliche VIP gebunden sind.  In der Liste unten sind die Parameter angegeben, die für die Erstellung einer ILB-ASE eine besondere Bedeutung haben bzw. eindeutig sind:

* *internalLoadBalancingMode*:  Sollte in den meisten Fällen auf „3“ festgelegt werden. Dies bedeutet, dass sowohl der HTTP/HTTPS-Datenverkehr über die Ports 80/443 als auch die Steuer-/Datenkanalports, auf die der FTP-Dienst in der ASE lauscht, an eine per ILB zugeordnete Adresse des virtuellen Netzwerks gebunden sind.  Falls diese Eigenschaft stattdessen auf 2 festgelegt ist, sind nur die auf den FTP-Dienst bezogenen Ports (Steuer- und Datenkanäle) an eine ILB-Adresse gebunden. Der HTTP/HTTPS-Datenverkehr verbleibt auf der öffentlichen VIP.
* *dnsSuffix*:  Dieser Parameter definiert die Standardstammdomäne, die der ASE zugewiesen wird.  In der öffentlichen Variante von Azure App Service lautet die Standardstammdomäne für alle Web-Apps *azurewebsites.net*.  Da eine ILB-ASE intern im virtuellen Netzwerk eines Kunden vorliegt, ergibt es keinen Sinn, die Standardstammdomäne des öffentlichen Diensts zu verwenden.  Stattdessen sollte eine ILB-ASE über eine Standardstammdomäne verfügen, für die die Verwendung im internen virtuellen Netzwerk eines Unternehmens sinnvoll ist.  Die fiktive Contoso Corporation kann beispielsweise die Standardstammdomäne *internal-contoso.com* für Apps verwenden, für die beabsichtigt ist, dass sie nur im virtuellen Netzwerk von Contoso auflösbar und zugänglich sind. 
* *ipSslAddressCount*:  Für diesen Parameter wird in der Datei *azuredeploy.json* automatisch der Wert 0 verwendet, da ILB-ASEs nur über eine einzelne ILB-Adresse verfügen.  Für eine ILB-ASE sind keine expliziten IP-SSL-Adressen vorhanden, sodass der IP-SSL-Adresspool für eine ILB-ASE auf 0 festgelegt werden muss. Andernfalls tritt ein Bereitstellungsfehler auf. 

Nachdem die Datei *azuredeploy.parameters.json* für eine ILB-ASE ausgefüllt wurde, kann die ILB-ASE mit dem folgenden PowerShell-Codeausschnitt erstellt werden.  Ändern Sie die Dateipfade (PATH) in den Speicherort, an dem sich die Azure Resource Manager-Vorlagendateien auf Ihrem Computer befinden.  Geben Sie auch Ihre eigenen Werte für den Azure Resource Manager-Bereitstellungsnamen und Ressourcengruppennamen an.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Nachdem die Azure Resource Manager-Vorlage übermittelt wurde, dauert es einige Stunden, bis die ILB-ASE erstellt wird.  Nach Abschluss der Erstellung wird die ILB-ASE auf der Benutzeroberfläche des Portals in der Liste mit den App Service-Umgebungen für das Abonnement angezeigt, über das die Bereitstellung ausgelöst wurde.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Hochladen und Konfigurieren des SSL-Standardzertifikats
Nach der Erstellung der ILB-ASE sollte ein SSL-Zertifikat der ASE als SSL-Standardzertifikat zugeordnet werden, das zum Herstellen von SSL-Verbindungen mit Apps verwendet wird.  Für das Beispiel mit der fiktiven Contoso Corporation gilt: Wenn das DNS-Standardsuffix der ASE *internal-contoso.com* lautet, ist für eine Verbindung mit *https://some-random-app.internal-contoso.com* ein SSL-Zertifikat erforderlich, das für * *.internal-contoso.com* gültig ist. 

Es gibt viele Möglichkeiten, ein gültiges SSL-Zertifikat zu beschaffen, z.B. interne Zertifizierungsstellen, Erwerb eines Zertifikats von einem externen Aussteller und Verwendung eines selbstsignierten Zertifikats.  Unabhängig von der Quelle des SSL-Zertifikats müssen die folgenden Zertifikatattribute richtig konfiguriert werden:

* *Antragsteller*:  Dieses Attribut muss auf * *.your-root-domain-here.com* festgelegt werden.
* *Alternativer Antragstellername*:  Dieses Attribut muss sowohl * *.your-root-domain-here.com* als auch * *.scm.your-root-domain-here.com* enthalten.  Der Grund für den zweiten Eintrag ist, dass für SSL-Verbindungen zur SCM/Kudu-Website, die jeder App zugeordnet ist, eine Adresse im Format *your-app-name.scm.your-root-domain-here.com*verwendet wird.

Wenn ein gültiges SSL-Zertifikat vorhanden ist, sind zwei weitere Vorbereitungsschritte erforderlich.  Das SSL-Zertifikat muss in eine PFX-Datei konvertiert bzw. in diesem Format gespeichert werden.  Beachten Sie, dass die PFX-Datei alle Zwischen- und Stammzertifikate enthalten und mit einem Kennwort geschützt werden muss.

Anschließend muss die sich ergebende PFX-Datei in eine Base64-Zeichenfolge konvertiert werden, da das SSL-Zertifikat mit einer Azure Resource Manager-Vorlage hochgeladen wird.  Da Azure Resource Manager-Vorlagen Textdateien sind, muss die PFX-Datei in eine Base64-Zeichenfolge konvertiert werden, damit sie als Parameter der Vorlage einbezogen werden kann.

Der folgende PowerShell-Codeausschnitt veranschaulicht ein Beispiel für das Generieren eines selbstsignierten Zertifikats, das Exportieren des Zertifikats als PFX-Datei, das Konvertieren der PFX-Datei in eine Base64-codierte Zeichenfolge und das anschließende Speichern der Base64-codierten Zeichenfolge in eine separate Datei.  Der PowerShell-Code für die Base64-Codierung wurde aus dem [Blog zu PowerShell-Skripts][examplebase64encoding] übernommen und angepasst.

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Nachdem das SSL-Zertifikat erfolgreich generiert und in eine Base64-codierte Zeichenfolge konvertiert wurde, kann die Azure Resource Manager-Beispielvorlage auf GitHub zum [Konfigurieren des SSL-Standardzertifikats][configuringDefaultSSLCertificate] verwendet werden.

Die Parameter in der Datei *azuredeploy.parameters.json* sind nachfolgend aufgeführt:

* *appServiceEnvironmentName*:  Der Name der ILB-ASE, die konfiguriert wird.
* *existingAseLocation*:  Die Textzeichenfolge mit der Azure-Region, in der die ILB-ASE bereitgestellt wurde.  Beispiel:   „USA, Süden-Mitte“
* *pfxBlobString*:  Die Base64-codierte Zeichenfolgendarstellung der PFX-Datei.  Bei Verwendung des weiter oben angegebenen Codeausschnitts kopieren Sie die in „exportedcert.pfx.b64“ enthaltene Zeichenfolge und fügen sie als Wert des Attributs *pfxBlobString* ein.
* *password*:  Das Kennwort, das zum Schützen der PFX-Datei verwendet wird.
* *certificateThumbprint*:  Der Fingerabdruck des Zertifikats.  Wenn Sie diesen Wert aus PowerShell abrufen (z.B. *$certificate.Thumbprint* aus dem Codeausschnitt weiter oben), können Sie den Wert unverändert nutzen.  Falls Sie den Wert aus dem Windows-Zertifikatdialogfeld kopieren, müssen Sie die überflüssigen Leerzeichen entfernen.  *certificateThumbprint* sollte etwa wie folgt aussehen:  AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*:  Ein benutzerfreundlicher Zeichenfolgenbezeichner zum Identifizieren des Zertifikats, den Sie selbst wählen können.  Der Name wird als Teil des eindeutigen Azure Resource Manager-Bezeichners für die Entität *Microsoft.Web/certificates* verwendet, die das SSL-Zertifikat darstellt.  Der Name **muss** auf folgendes Suffix enden: \_yourASENameHere_InternalLoadBalancingASE.  Dieses Suffix ist ein Indikator für das Portal, dass das Zertifikat zum Sichern einer für den internen Lastenausgleich geeigneten App Service-Umgebung genutzt wird.

Ein gekürztes Beispiel für *azuredeploy.parameters.json* sehen Sie hier:

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

Nachdem die Daten in die Datei *azuredeploy.parameters.json* eingefügt wurden, kann das SSL-Standardzertifikat mit dem folgenden PowerShell-Codeausschnitt konfiguriert werden.  Ändern Sie die Dateipfade (PATH) in den Speicherort, an dem sich die Azure Resource Manager-Vorlagendateien auf Ihrem Computer befinden.  Geben Sie auch Ihre eigenen Werte für den Azure Resource Manager-Bereitstellungsnamen und Ressourcengruppennamen an.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Nachdem die Azure Resource Manager-Vorlage übermittelt wurde, dauert die Anwendung der Änderung ca. 40 Minuten pro ASE-Front-End.  Bei einer ASE mit Standardgröße, für die zwei Front-Ends genutzt werden, dauert es beispielsweise ungefähr eine Stunde und 20 Minuten, bis der Vorgang für die Vorlage abgeschlossen ist.  Während der Ausführung der Vorlage kann die ASE nicht skaliert werden.  

Nachdem die Vorlage abgeschlossen wurde, kann auf die Apps in der ILB-ASE per HTTPS zugegriffen werden, und die Verbindungen werden mit dem SSL-Standardzertifikat geschützt.  Das SSL-Standardzertifikat wird verwendet, wenn Apps in der ILB-ASE mit einer Kombination aus dem Anwendungsnamen und dem Standardhostnamen adressiert werden.  Für *https://mycustomapp.internal-contoso.com* wird beispielsweise das SSL-Standardzertifikat für * *.internal-contoso.com* verwendet.

Wie bei Apps, die unter dem öffentlichen mehrinstanzenfähigen Dienst ausgeführt werden, können Entwickler für einzelne Apps auch benutzerdefinierte Hostnamen und dann eindeutige SNI SSL-Zertifikatbindungen konfigurieren.  

## <a name="getting-started"></a>Erste Schritte
Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

