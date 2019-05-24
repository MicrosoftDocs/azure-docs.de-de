---
title: 'Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats: Azure App Service | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie ein benutzerdefiniertes SSL-Zertifikat an Ihre Web-App, ein Back-End Ihrer mobilen App oder Ihre API-App in Azure App Service binden.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0a5b8bdbcd5a05574d824e3f57cfc23967278e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138663"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service

Von Azure App Service wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In diesem Tutorial erfahren Sie, wie Sie ein benutzerdefiniertes SSL-Zertifikat, das Sie von einer vertrauenswürdigen Zertifizierungsstelle erworben haben, an [Azure App Service](overview.md) binden. Wenn Sie fertig sind, können Sie am HTTPS-Endpunkt Ihrer benutzerdefinierten DNS-Domäne auf Ihre App zugreifen.

![Web-App mit benutzerdefiniertem SSL-Zertifikat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren des Tarifs für Ihre App
> * Binden Ihres benutzerdefinierten Zertifikats an App Service
> * Erneuern von Zertifikaten
> * Erzwingen von HTTPS
> * Erzwingen von TLS 1.1/1.2
> * Automatisieren der TLS-Verwaltung mit Skripts

> [!NOTE]
> Wenn Sie ein benutzerdefiniertes SSL-Zertifikat benötigen, können Sie dieses direkt im Azure-Portal abrufen und an Ihre App binden. Absolvieren Sie das [Tutorial zu App Service-Zertifikaten](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Erstellen einer App Service-App](/azure/app-service/)
- [Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App Service-App](app-service-web-tutorial-custom-domain.md)
- Erwerben eines SSL-Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle
- Den privaten Schlüssel, den Sie zum Signieren der SSL-Zertifikatanforderung verwendet haben.

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Anforderungen an Ihr SSL-Zertifikat

Das Zertifikat muss sämtliche der folgenden Anforderungen erfüllen, damit Ihr Zertifikat in App Service verwendet werden kann:

* Von einer vertrauenswürdigen Zertifizierungsstelle signiert
* Als kennwortgeschützte PFX-Datei exportiert
* Enthält einen privaten Schlüssel mit mindestens 2048 Bit
* Enthält alle Zwischenzertifikate in der Zertifikatkette

> [!NOTE]
> **ECC-Zertifikate (Elliptic Curve Cryptography, Kryptografie für elliptische Kurven)** können mit App Service funktionieren, werden in diesem Artikel aber nicht behandelt. Erarbeiten Sie mit Ihrer Zertifizierungsstelle die einzelnen Schritte zum Erstellen von ECC-Zertifikaten.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Binden Ihres SSL-Zertifikats

Sie können das SSL-Zertifikat in Ihre App hochladen.

### <a name="merge-intermediate-certificates"></a>Zusammenführen von Zwischenzertifikaten

Wenn Ihre Zertifizierungsstelle Ihnen mehrere Zertifikate in der Zertifikatskette bereitstellt, müssen Sie die Zertifikate nacheinander zusammenführen. 

Öffnen Sie hierzu alle Zertifikate, die Sie erhalten haben, in einem Text-Editor. 

Erstellen Sie eine Datei für das zusammengeführte Zertifikat mit dem Namen _mergedcertificate.crt_. Kopieren Sie den Inhalt der einzelnen Zertifikate in einem Text-Editor in diese Datei. Die Reihenfolge Ihrer Zertifikate sollte der Reihenfolge in der Zertifikatkette folgen, beginnend mit Ihrem Zertifikat und endend mit dem Stammzertifikat. Dies sieht in etwa wie im folgenden Beispiel aus:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportieren des Zertifikats als PFX-Datei

Exportieren Sie Ihr benutzerdefiniertes SSL-Zertifikat mit dem privaten Schlüssel, mit dem Ihre Zertifikatanforderung generiert wurde.

Wenn Sie die Zertifikatanforderung mittels OpenSSL generiert haben, haben Sie eine Datei des privaten Schlüssels erstellt. Führen Sie folgenden Befehl aus, um Ihr Zertifikat nach PFX zu exportieren. Ersetzen Sie die Platzhalter _&lt;private-key-file>_ und _&lt;merged-certificate-file>_ mit den Pfaden zu Ihrem privaten Schlüssel und Ihrer zusammengeführten Zertifikatdatei.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Definieren Sie ein Kennwort für den Export, wenn Sie dazu aufgefordert werden. Dieses Kennwort verwenden Sie, wenn Sie Ihr SSL-Zertifikat zu einem späteren Zeitpunkt in App Service hochladen.

Wenn Sie IIS oder _Certreq.exe_ zum Generieren Ihrer Zertifikatanforderung verwendet haben, installieren Sie das Zertifikat auf dem lokalen Computer, und klicken Sie anschließend auf [Zertifikat nach PFX exportieren](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Hochladen Ihres SSL-Zertifikats

Klicken Sie im linken Navigationsbereich Ihrer App auf **SSL-Einstellungen**, um Ihr SSL-Zertifikat hochzuladen.

Klicken Sie auf **Zertifikat hochladen**. 

Wählen Sie unter **PFX-Zertifikatdatei** Ihre PFX-Datei aus. Geben Sie unter **Zertifikatkennwort** das Kennwort ein, das Sie beim Exportieren der PFX-Datei erstellt haben.

Klicken Sie auf **Hochladen**.

![Hochladen des Zertifikats](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Wenn der Upload Ihres Zertifikats in App Service abgeschlossen ist, wird es auf der Seite **SSL-Einstellungen** angezeigt.

![Zertifikat hochgeladen](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Binden Ihres SSL-Zertifikats

Klicken Sie im Abschnitt **SSL-Bindungen** auf **Bindung hinzufügen**.

Wählen Sie auf der Seite **SSL-Bindung hinzufügen** aus den Dropdownlisten den Domänennamen, der geschützt werden soll, sowie das zu verwendende Zertifikat aus.

> [!NOTE]
> Wenn Sie Ihr Zertifikat hochgeladen haben, der/die Domänenname(n) im Dropdownmenü **Hostname** jedoch nicht angezeigt wird/werden, versuchen Sie, die Browserseite zu aktualisieren.
>
>

Wählen Sie unter **SSL-Typ** aus, ob SSL auf der **[Servernamensanzeige (Server Name Indication, SNI)](https://en.wikipedia.org/wiki/Server_Name_Indication)** oder der IP basieren soll.

- **SNI-basiertes SSL**: Möglicherweise können mehrere SNI-basierte SSL-Bindungen hinzugefügt werden. Bei dieser Option können mehrere zur selben IP-Adresse zugehörige Domänen durch mehrere SSL-Zertifikate geschützt werden. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI (ausführlichere Informationen zur Browserunterstützung finden Sie unter [Servernamensanzeige](https://wikipedia.org/wiki/Server_Name_Indication)).
- **IP-basiertes SSL** : Möglicherweise kann nur eine IP-basierte SSL-Bindung hinzugefügt werden. Bei dieser Option kann eine dedizierte öffentliche IP-Adresse nur durch ein SSL-Zertifikat geschützt werden. Um mehrere Domänen zu schützen, müssen Sie sie alle mit demselben SSL-Zertifikat schützen. Dies ist die herkömmliche Option für SSL-Bindungen.

Klicken Sie auf **Bindung hinzufügen**.

![Binden eines SSL-Zertifikats](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Wenn der Upload Ihres Zertifikats in App Service abgeschlossen ist, wird es in den Abschnitten **SSL-Bindungen** angezeigt.

![An die Web-App gebundenes Zertifikat](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Neuzuordnen eines A-Eintrags für IP-SSL

Wenn Sie kein IP-basiertes SSL in Ihrer App verwenden, fahren Sie mit [Testen von HTTPS](#test) fort.

Standardmäßig verwendet Ihre App eine freigegebene öffentliche IP-Adresse. Wenn Sie ein Zertifikat mit IP-basiertem SSL binden, erstellt App Service eine neue, dedizierte IP-Adresse für Ihre App.

Wenn Sie Ihrer App einen A-Eintrag zugeordnet haben, aktualisieren Sie Ihre Domänenregistrierung mit dieser neuen, dedizierten IP-Adresse.

Die Seite **Benutzerdefinierte Domäne** Ihrer App wird mit der neuen, dedizierten IP-Adresse aktualisiert. [Kopieren Sie diese IP-Adresse](app-service-web-tutorial-custom-domain.md#info) und [ordnen Sie dieser neuen IP-Adresse dann den A-Eintrag neu zu](app-service-web-tutorial-custom-domain.md#map-an-a-record).

<a name="test"></a>

## <a name="test-https"></a>Testen von HTTPS

Sie müssen jetzt nur noch sicherstellen, dass HTTPS für Ihre benutzerdefinierte Domäne funktioniert. Rufen Sie in verschiedenen Browsern `https://<your.custom.domain>` auf, um zu sehen, ob Ihre App angeboten wird.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Wenn Ihre App Zertifikatüberprüfungsfehler meldet, verwenden Sie wahrscheinlich ein selbstsigniertes Zertifikat.
>
> Wenn dies nicht der Fall ist, haben Sie beim Exportieren des Zertifikats als PFX-Datei eventuell Zwischenzertifikate ausgelassen.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Erneuern von Zertifikaten

Ihre IP-Adresse für eingehenden Datenverkehr kann sich ändern, wenn Sie eine Bindung löschen, auch wenn diese Bindung IP-basiert ist. Dies ist besonders wichtig, wenn Sie ein Zertifikat erneuern, das sich bereits in einer IP-basierten Bindung befindet. Um eine Änderung der IP-Adresse in Ihrer App zu vermeiden, führen Sie diese Schritte in angegebener Reihenfolge aus:

1. Laden Sie das neue Zertifikat hoch.
2. Binden Sie das neue Zertifikat an die gewünschte benutzerdefinierte Domäne, ohne die alte zu löschen. Dadurch wird die Bindung ersetzt, anstatt die alte Bindung zu entfernen.
3. Löschen Sie das alte Zertifikat. 

## <a name="enforce-https"></a>Erzwingen von HTTPS

Standardmäßig können Benutzer weiterhin mit HTTP auf Ihre App zugreifen. Sie können alle HTTP-Anforderungen an den HTTPS-Port umleiten.

Wählen Sie im linken Navigationsbereich der App-Seite **SSL-Einstellungen** aus. Wählen Sie anschließend für **Nur HTTPS** die Option **Ein**.

![Erzwingen von HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Wenn der Vorgang abgeschlossen ist, navigieren Sie zu einer beliebigen HTTP-URL, die auf Ihre App verweist. Beispiel: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Erzwingen von TLS-Versionen

Ihre App lässt standardmäßig [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 zu. Diese TLS-Version wird von Branchenstandards wie [PCI-DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) empfohlen. Andere TLS-Versionen können wie folgt erzwungen werden:

Wählen Sie im linken Navigationsbereich der App-Seite **SSL-Einstellungen** aus. Wählen Sie dann unter **TLS-Version** die gewünschte TLS-Mindestversion aus. Diese Einstellung steuert nur eingehende Aufrufe. 

![Erzwingen von TLS 1.1 oder 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Nach Abschluss des Vorgangs lehnt Ihre App alle Verbindungen mit niedrigerer TLS-Version ab.

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Mit der [Azure CLI](/cli/azure/install-azure-cli) oder mit [Azure PowerShell](/powershell/azure/overview) können Sie mithilfe von Skripts SSL-Bindungen für Ihre App automatisieren.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Mit folgendem Befehl wird eine exportierte PFX-Datei hochgeladen und der Fingerabdruck abgerufen.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Der folgende Befehl fügt mithilfe des Fingerabdrucks aus dem vorherigen Befehl eine SNI-basierte SSL-Bindung hinzu.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Der folgende Befehl erzwingt mindestens Version 1.2 von TLS.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mit folgendem Befehl wird eine exportierte PFX-Datei hochgeladen und eine SNI-basierte SSL-Bindung hinzugefügt.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Öffentliche Zertifikate (optional)
Falls Ihre App als Client auf Remoteressourcen zugreifen muss und die Remoteressource eine Zertifikatauthentifizierung erfordert, können Sie [öffentliche Zertifikate](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) in Ihre App hochladen. Öffentliche Zertifikate sind für SSL-Bindungen Ihrer App nicht erforderlich.

Weitere Informationen zum Laden und Verwenden eines öffentlichen Zertifikats in Ihrer App finden Sie unter [Verwenden eines SSL-Zertifikats in Ihrem Anwendungscode in Azure App Service](app-service-web-ssl-cert-load.md). Sie können öffentliche Zertifikate auch mit Apps in App Service-Umgebungen verwenden. Wenn Sie das Zertifikat im LocalMachine-Zertifikatspeicher speichern müssen, müssen Sie eine App in der App Service-Umgebung verwenden. Weitere Informationen finden Sie unter [App Service Certificates now supports public certificates (.cer)](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer) (App Service-Zertifikate unterstützen jetzt öffentliche Zertifikate [CER].).

![Hochladen eines öffentlichen Zertifikats](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Aktualisieren des Tarifs für Ihre App
> * Binden Ihres benutzerdefinierten Zertifikats an App Service
> * Erneuern von Zertifikaten
> * Erzwingen von HTTPS
> * Erzwingen von TLS 1.1/1.2
> * Automatisieren der TLS-Verwaltung mit Skripts

Im nächsten Tutorial erfahren Sie, wie Sie Azure Content Delivery Network verwenden.

> [!div class="nextstepaction"]
> [Hinzufügen eines CDN (Content Delivery Network) zu einer Azure App Service-Instanz](../cdn/cdn-add-to-web-app.md)

Weitere Informationen finden Sie unter [Verwenden eines SSL-Zertifikats in Ihrem Anwendungscode in Azure App Service](app-service-web-ssl-cert-load.md).
