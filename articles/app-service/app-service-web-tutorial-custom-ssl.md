---
title: 'Hochladen und Binden eines SSL-Zertifikats: Azure App Service | Microsoft-Dokumentation'
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
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 32e6311a8796e708119f3e1df813b6ebb2ed0673
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743007"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Tutorial: Hochladen und Binden eines SSL-Zertifikats an Azure App Service

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In diesem Tutorial erfahren Sie, wie Sie eine benutzerdefinierte Domäne in App Service mit einem Zertifikat schützen, das Sie von einer vertrauenswürdigen Zertifizierungsstelle erworben haben. Darüber hinaus wird gezeigt, wie Sie private und öffentliche Zertifikate hochladen, die Ihre App benötigt. Wenn Sie fertig sind, können Sie am HTTPS-Endpunkt Ihrer benutzerdefinierten DNS-Domäne auf Ihre App zugreifen.

![Web-App mit benutzerdefiniertem SSL-Zertifikat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren des Tarifs für Ihre App
> * Schützen einer benutzerdefinierten Domäne mit einem Zertifikat
> * Hochladen eines privaten Zertifikats
> * Hochladen eines öffentlichen Zertifikats
> * Erneuern von Zertifikaten
> * Erzwingen von HTTPS
> * Erzwingen von TLS 1.1/1.2
> * Automatisieren der TLS-Verwaltung mit Skripts

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Erstellen einer App Service-App](/azure/app-service/)
- [Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App Service-App](app-service-web-tutorial-custom-domain.md) (zum Schutz einer benutzerdefinierten Domäne)
- Erwerben eines Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle
- Den privaten Schlüssel, den Sie zum Signieren der Zertifikatanforderung verwendet haben (bei privaten Zertifikaten)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Vorbereiten eines privaten Zertifikats

Ein Zertifikat zum Schutz einer Domäne muss alle der folgenden Anforderungen erfüllen:

* Für die Serverauthentifizierung konfiguriert
* Von einer vertrauenswürdigen Zertifizierungsstelle signiert
* Als kennwortgeschützte PFX-Datei exportiert
* Enthält einen privaten Schlüssel mit mindestens 2048 Bit
* Enthält alle Zwischenzertifikate in der Zertifikatkette

> [!TIP]
> Falls Sie ein benutzerdefiniertes SSL-Zertifikat benötigen, können Sie dieses direkt im Azure-Portal beziehen und in Ihre App importieren. Absolvieren Sie das [Tutorial zu App Service-Zertifikaten](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **ECC-Zertifikate (Elliptic Curve Cryptography, Kryptografie für elliptische Kurven)** können mit App Service funktionieren, werden in diesem Artikel aber nicht behandelt. Erarbeiten Sie mit Ihrer Zertifizierungsstelle die einzelnen Schritte zum Erstellen von ECC-Zertifikaten.

Wenn Sie über ein Zertifikat von Ihrem Zertifikatanbieter verfügen, gehen Sie wie in diesem Abschnitt beschrieben vor, um es für App Service vorzubereiten.

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

Nun können Sie das Zertifikat in App Service hochladen.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Schützen einer benutzerdefinierten Domäne

> [!TIP]
> Wenn Sie ein benutzerdefiniertes SSL-Zertifikat benötigen, können Sie dieses direkt im Azure-Portal abrufen und an Ihre App binden. Absolvieren Sie das [Tutorial zu App Service-Zertifikaten](web-sites-purchase-ssl-web-site.md).

Wenn Sie eine [benutzerdefinierte Domäne](app-service-web-tutorial-custom-domain.md) mit einem Drittanbieterzertifikat schützen möchten, müssen Sie das [vorbereitete private Zertifikat](#prepare-a-private-certificate) hochladen und anschließend an die benutzerdefinierte Domäne binden. Dieser Prozess wird jedoch von App Service für Sie vereinfacht. Führen Sie folgende Schritte aus:

Klicken Sie im linken Navigationsbereich Ihrer App auf **Benutzerdefinierte Domänen** und anschließend für die zu schützende Domäne auf **Bindung hinzufügen**. Sollte **Bindung hinzufügen** für eine Domäne nicht angezeigt werden, ist sie bereits geschützt, und ihr SSL-Zustand sollte **Sicher** lauten.

![Hinzufügen der Bindung zur Domäne](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Klicken Sie auf **Zertifikat hochladen**.

Wählen Sie unter **PFX-Zertifikatdatei** Ihre PFX-Datei aus. Geben Sie unter **Zertifikatkennwort** das Kennwort ein, das Sie beim Exportieren der PFX-Datei erstellt haben.

Klicken Sie auf **Hochladen**.

![Hochladen des Zertifikats für die Domäne](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Warten Sie, bis Azure Ihr Zertifikat hochgeladen hat und das Dialogfeld für SSL-Bindungen geöffnet wurde.

Wählen im Dialogfeld das hochgeladene Zertifikat und den SSL-Typ aus, und klicken Sie anschließend auf **Bindung hinzufügen**.

> [!NOTE]
> Folgende SSL-Typen werden unterstützt:
>
> - **[SNI-basiertes SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** : Es können mehrere SNI-basierte SSL-Bindungen hinzugefügt werden. Bei dieser Option können mehrere zur selben IP-Adresse zugehörige Domänen durch mehrere SSL-Zertifikate geschützt werden. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI (ausführlichere Informationen zur Browserunterstützung finden Sie unter [Servernamensanzeige](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **IP-basiertes SSL** : Möglicherweise kann nur eine IP-basierte SSL-Bindung hinzugefügt werden. Bei dieser Option kann eine dedizierte öffentliche IP-Adresse nur durch ein SSL-Zertifikat geschützt werden. Um mehrere Domänen zu schützen, müssen Sie sie alle mit demselben SSL-Zertifikat schützen. Dies ist die herkömmliche Option für SSL-Bindungen.

![Binden von SSL an die Domäne](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Der SSL-Zustand der Domäne sollte nun **Sicher** lauten.

![Geschützte Domäne](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Der Zustand **Sicher** unter **Benutzerdefinierte Domänen** bedeutet, dass die Domäne durch ein Zertifikat geschützt ist. App Service überprüft jedoch nicht, ob das Zertifikat beispielsweise selbstsigniert oder abgelaufen ist, was ebenfalls dazu führen kann, dass ein Browser einen Fehler oder eine Warnung anzeigt.

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

## <a name="renew-certificates"></a>Erneuern von Zertifikaten

Ihre IP-Adresse für eingehenden Datenverkehr kann sich ändern, wenn Sie eine Bindung löschen, auch wenn diese Bindung IP-basiert ist. Dies ist besonders wichtig, wenn Sie ein Zertifikat erneuern, das sich bereits in einer IP-basierten Bindung befindet. Um eine Änderung der IP-Adresse in Ihrer App zu vermeiden, führen Sie diese Schritte in angegebener Reihenfolge aus:

1. Laden Sie das neue Zertifikat hoch.
2. Binden Sie das neue Zertifikat an die gewünschte benutzerdefinierte Domäne, ohne die alte zu löschen. Dadurch wird die Bindung ersetzt, anstatt die alte Bindung zu entfernen.
3. Löschen Sie das alte Zertifikat. 

<a name="bkmk_enforce"></a>

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

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

Der folgende Befehl fügt mithilfe des Fingerabdrucks aus dem vorherigen Befehl eine SNI-basierte SSL-Bindung hinzu.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Der folgende Befehl erzwingt mindestens Version 1.2 von TLS.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
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

## <a name="use-certificates-in-your-code"></a>Verwenden von Zertifikaten in Ihrem Code

Falls Ihre App eine Verbindung mit Remoteressourcen herstellen muss und die Remoteressource eine Zertifikatauthentifizierung erfordert, können Sie öffentliche oder private Zertifikate in Ihre App hochladen. Diese Zertifikate müssen in Ihrer App an keine benutzerdefinierte Domäne gebunden werden. Weitere Informationen finden Sie unter [Verwenden eines SSL-Zertifikats in Ihrem Anwendungscode in Azure App Service](app-service-web-ssl-cert-load.md).

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
