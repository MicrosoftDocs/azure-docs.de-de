---
title: Konfigurieren von SSL für einen Clouddienst | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können. In diesen Beispielen wird das Azure-Portal verwendet.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeconnoc
ms.openlocfilehash: 2a9879ebc55a5f25c1a358e386697dce1c55ec90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61434066"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurieren von SSL für eine Anwendung in Azure

Secure Socket Layer (SSL)-Verschlüsselung ist die am häufigsten verwendete Methode zur Sicherung von Daten im Internet. Im Folgenden erfahren Sie, wie Sie einen HTTPS-Endpunkt für eine Webrolle angeben und ein SSL-Zertifikat zur Sicherung Ihrer Anwendung hochladen können.

> [!NOTE]
> Die Vorgehensweisen in dieser Aufgabe gelten für Azure Cloud Services. Entsprechende Informationen zu App Services finden Sie [hier](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Diese Aufgabe erfordert die Verwendung einer Produktionsbereitstellung. Informationen zur Verwendung einer Stagingbereitstellung erhalten Sie am Ende dieses Themas.

Lesen Sie [dies](cloud-services-how-to-create-deploy-portal.md) zuerst, wenn Sie noch keinen Clouddienst erstellt haben.

## <a name="step-1-get-an-ssl-certificate"></a>Schritt 1: Beziehen eines SSL-Zertifikats
Sie müssen zuerst ein SSL-Zertifikat beziehen, um SSL für eine Anwendung zu konfigurieren. Dieses muss von einer Zertifizierungsstelle, einem vertrauenswürdigen Dritten, der Zertifikate für diesen Zweck ausgibt, ausgegeben werden. Wenn Sie noch kein Zertifikat haben, müssen Sie eines von einem Unternehmen erwerben, das SSL-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-Zertifikate in Azure erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten.
* Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
* Der Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird. Für die Domäne cloudapp.net können Sie kein SSL-Zertifikat von einer Zertifizierungsstelle beziehen. Sie müssen einen benutzerdefinierten Domänennamen erwerben, den Sie für den Zugriff auf Ihren Dienst verwenden können. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat der Domäne entsprechen, über die auf Ihre Anwendung zugegriffen wird. Wenn beispielsweise der benutzerdefinierte Domänenname **contoso.com** lautet, fordern Sie von Ihrer Zertifizierungsstelle ein Zertifikat für * **.contoso.com** oder **www\..contoso.com** an.
* Das Zertifikat muss mindestens eine 2048-Bit-Verschlüsselung haben.

Zu Testzwecken können Sie ein selbst signiertes Zertifikat [erstellen](cloud-services-certs-create.md) und verwenden. Ein selbstsigniertes Zertifikat wird nicht über eine Zertifizierungsstelle authentifiziert. Daher kann in diesem Fall die Domäne cloudapp.net als Website-URL verwendet werden. Zum Beispiel wird in der folgenden ein selbstsigniertes Zertifikat verwendet, in dem der allgemeine Name, der im Zertifikat verwendet wird, **sslexample.cloudapp.net** lautet.

Daraufhin müssen Sie Informationen zum Zertifikat in Ihre Definitions- und Konfigurationsdateien für den Dienst einfügen.

<a name="modify"></a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Schritt 2: Ändern der Definitions- und Konfigurationsdateien für den Dienst
Ihre Anwendung muss so konfiguriert sein, dass das Zertifikat verwendet wird. Außerdem muss ein HTTPS-Endpunkt hinzugefügt werden. Daher müssen die Definitions- und Konfigurationsdateien für den Dienst aktualisiert werden.

1. Öffnen Sie in Ihrer Entwicklungsumgebung die Dienstdefinitionsdatei (CSDEF), fügen Sie innerhalb des Bereichs **WebRole** einen Bereich **Certificates** hinzu, und geben Sie die folgenden Informationen über das Zertifikat (und Zwischenzertifikate) an:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   Der Bereich **Certificates** definiert den Namen des Zertifikats, dessen Speicherort sowie den Namen des Speichers.

   Berechtigungen (`permissionLevel`-Attribut) können auf einen der folgenden Werte festgelegt werden:

   | Berechtigungswert | BESCHREIBUNG |
   | --- | --- |
   | limitedOrElevated |**(Standard)** Alle Rollenprozesse können auf den privaten Schlüssel zugreifen. |
   | elevated |Nur Prozesse mit erhöhten Rechten können auf den privaten Schlüssel zugreifen. |

2. Fügen Sie in der Dienstdefinitionsdatei im Bereich **Endpoints** ein **InputEndpoint**-Element hinzu, um HTTPS zu aktivieren:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Fügen Sie in der Dienstdefinitionsdatei im Bereich **Sites** ein **Binding**-Element hinzu. Mit diesem Element wird eine HTTPS-Bindung hinzugefügt, die Ihrer Website den Endpunkt zuordnet:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Alle erforderlichen Änderungen an der Dienstdefinitionsdatei sind jetzt abgeschlossen. Sie müssen jedoch noch die Zertifikatinformationen der Dienstkonfigurationsdatei hinzufügen.
4. Fügen Sie in der Dienstkonfigurationsdatei (CSCFG) „ServiceConfiguration.Cloud.cscfg“ einen **Certificates**-Wert mit dem Wert Ihres Zertifikats hinzu. Das folgende Codebeispiel enthält Details zum Abschnitt **Certificates** mit Ausnahme des Fingerabdruckwerts.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(In diesem Beispiel wird **sha1** für den Fingerabdruckalgorithmus verwendet. Geben Sie den entsprechenden Wert für den Fingerabdruckalgorithmus Ihres Zertifikats an.)

Die Definitions- und Konfigurationsdateien für den Dienst wurden aktualisiert. Erstellen Sie jetzt Ihr Bereitstellungspaket und laden Sie es in Azure hoch. Wenn Sie **cspack** verwenden, vernden Sie nicht die Kennzeichnung **/generateConfigurationFile**, da dies die Zertifikatinformationen überschreibt, die Sie zuvor eingefügt haben.

## <a name="step-3-upload-a-certificate"></a>Schritt 3: Hochladen eines Zertifikats
Stellen Sie eine Verbindung mit dem Azure-Portal her, und ...

1. Wählen Sie im Bereich **Alle Ressourcen** des Portals Ihren Clouddienst aus.

    ![Clouddienst veröffentlichen](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klicken Sie auf **Zertifikate**.

    ![Klicken Sie auf das Symbol "Zertifikate".](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klicken Sie über dem Bereich der Zertifikate auf **Hochladen**.

    ![Auf Menüelement „Hochladen“ klicken](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Geben Sie die **Datei** und das **Kennwort** an, und klicken Sie dann unten im Dateneingabebereich auf **Hochladen**.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Schritt 4: Herstellen einer Verbindung mit der Rolleninstanz über HTTPS
Jetzt wird die Bereitstellung in Azure ausgeführt, und Sie können eine HTTPS-Verbindung herstellen.

1. Klicken Sie auf die **Website-URL** , um den Webbrowser zu öffnen.

   ![Auf die Website-URL klicken](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Passen Sie in Ihrem Webbrowser den Link so an, dass **https** statt **http** verwendet wird, und rufen Sie dann die Seite auf.

   > [!NOTE]
   > Wenn Sie ein selbstsigniertes Zertifikat verwenden und zu einem HTTPS-Endpunkt wechseln, der mit dem selbstsignierten Zertifikat verknüpft ist, wird im Browser ein u. U. Zertifikatfehler angezeigt. Verwenden Sie zur Lösung dieses Problems ein Zertifikat, das von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Bis zu diesem Zeitpunkt können Sie den Fehler ignorieren. (Sie können auch das selbstsignierte Zertifikat zum Zertifikatspeicher der vertrauenswürdigen Zertifizierungsstelle des Benutzers hinzufügen.)
   >
   >

   ![Websitevorschau](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Wenn Sie SSL für eine Staging- statt für eine Produktionsbereitstellung verwenden möchten, müssen Sie zuerst die URL festlegen, die für die Stagingbereitstellung verwendet werden soll. Wenn der Clouddienst bereitgestellt wurde, wird die URL zur Stagingumgebung anhand der GUID für die **Bereitstellungs-ID** in folgendem Format festgelegt: `https://deployment-id.cloudapp.net/`  
   >
   > Erstellen Sie ein Zertifikat mit dem allgemeinen Namen (CN), welcher der GUID-basierten URL entspricht (z. B. **328187776e774ceda8fc57609d404462.cloudapp.net**). Fügen Sie das Zertifikat über das Portal Ihrem bereitgestellten Clouddienst hinzu. Fügen Sie dann Ihren CSDRF- und CSCfg-Dateien die Zertifikatinformationen hinzu, packen Sie Ihre Anwendung neu, und aktualisieren Sie Ihre gestaffelte Bereitstellung, sodass das neue Paket verwendet wird.
   >

## <a name="next-steps"></a>Nächste Schritte
* [Allgemeine Konfiguration Ihres Clouddiensts](cloud-services-how-to-configure-portal.md)
* Weitere Informationen zum [Bereitstellen eines Clouddiensts](cloud-services-how-to-create-deploy-portal.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens](cloud-services-custom-domain-name-portal.md)
* [Verwalten Ihres Clouddiensts](cloud-services-how-to-manage-portal.md)
