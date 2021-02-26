---
title: Sichern von APIs über eine Clientzertifikatauthentifizierung in API Management
titleSuffix: Azure API Management
description: In diesem Artikel erfahren Sie, wie Sie den Zugriff auf APIs mithilfe von Clientzertifikaten sichern. Eingehende Zertifikate können mit Richtlinienausdrücken überprüft werden.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988886"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Sichern von APIs über eine Clientzertifikatauthentifizierung in API Management

API Management bietet die Möglichkeit, den Zugriff auf APIs (d.h. vom Client auf API Management) mithilfe von Clientzertifikaten zu sichern. Sie können eingehende Zertifikate überprüfen und die Zertifikateigenschaften mit den gewünschten Werten vergleichen, indem Sie Richtlinienausdrücke verwenden.

Informationen zum Schützen des Zugriffs auf den Back-End-Dienst einer API mithilfe von Clientzertifikaten (von API Management auf das Back-End) finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](./api-management-howto-mutual-certificates.md).

> [!IMPORTANT]
> Um Clientzertifikate im Tarif „Entwickler“, „Basic“, „Standard“ oder „Premium“ über HTTP/2 empfangen und überprüfen zu können, müssen Sie wie unten gezeigt auf dem Blatt „Benutzerdefinierte Domänen“ die Einstellung „Clientzertifikat aushandeln“ aktivieren.

![Clientzertifikat aushandeln](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Um Clientzertifikate im Tarif „Verbrauch“ empfangen und überprüfen zu können, müssen Sie wie unten gezeigt auf dem Blatt „Benutzerdefinierte Domänen“ die Einstellung „Clientzertifikat anfordern“ aktivieren.

![Anfordern des Clientzertifikats](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Prüfen des Ausstellers und des Antragstellers

Die unten stehenden Richtlinien konfiguriert werden, um den Aussteller und den Antragsteller eines Clientzertifikats zu prüfen:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Verwenden Sie `context.Request.Certificate.VerifyNoRevocation()` anstelle von `context.Request.Certificate.Verify()`, um die Überprüfung der Zertifikatssperrliste zu deaktivieren.
> Wenn das Clientzertifikat selbstsigniert ist, müssen Stamm- oder Zwischen-Zertifizierungsstellenzertifikate in API Management [hochgeladen](api-management-howto-ca-certificates.md) werden, damit `context.Request.Certificate.Verify()` und `context.Request.Certificate.VerifyNoRevocation()` funktionieren.

## <a name="checking-the-thumbprint"></a>Prüfen des Fingerabdrucks

Die folgenden Richtlinien können zum Prüfen des Fingerabdrucks eines Clientzertifikats konfiguriert werden:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Verwenden Sie `context.Request.Certificate.VerifyNoRevocation()` anstelle von `context.Request.Certificate.Verify()`, um die Überprüfung der Zertifikatssperrliste zu deaktivieren.
> Wenn das Clientzertifikat selbstsigniert ist, müssen Stamm- oder Zwischen-Zertifizierungsstellenzertifikate in API Management [hochgeladen](api-management-howto-ca-certificates.md) werden, damit `context.Request.Certificate.Verify()` und `context.Request.Certificate.VerifyNoRevocation()` funktionieren.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Prüfen eines Fingerabdrucks anhand von auf API Management hochgeladenen Zertifikaten

Das folgende Beispiel zeigt, wie Sie den Fingerabdruck eines Clientzertifikats anhand von auf API Management hochgeladenen Zertifikaten prüfen können:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Verwenden Sie `context.Request.Certificate.VerifyNoRevocation()` anstelle von `context.Request.Certificate.Verify()`, um die Überprüfung der Zertifikatssperrliste zu deaktivieren.
> Wenn das Clientzertifikat selbstsigniert ist, müssen Stamm- oder Zwischen-Zertifizierungsstellenzertifikate in API Management [hochgeladen](api-management-howto-ca-certificates.md) werden, damit `context.Request.Certificate.Verify()` und `context.Request.Certificate.VerifyNoRevocation()` funktionieren.

> [!TIP]
> Das Problem aufgrund eines Clientzertifikat-Deadlocks, das in [diesem Artikel](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) beschrieben ist, kann auf unterschiedliche Art und Weise auftreten, z. B. Einfrieren von Anforderungen, Anforderungen führen nach dem Timeout zum Status `403 Forbidden` oder `context.Request.Certificate` ist `null`. Dieses Problem wirkt sich normalerweise auf Anforderungen vom Typ `POST` und `PUT` mit einer Inhaltslänge von ca. 60 KB oder mehr aus.
> Um dieses Problem zu verhindern, aktivieren Sie auf dem Blatt „Benutzerdefinierte Domänen“ die Einstellung „Clientzertifikat aushandeln“ für gewünschte Hostnamen, wie auf der ersten Abbildung dieses Dokuments gezeigt. Dieses Feature ist im Tarif „Verbrauch“ nicht verfügbar.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Zertifikatsüberprüfung in selbs gehostetem Gateway

Das Standardimage des [selbstgehosteten Gateways](self-hosted-gateway-overview.md) von API Management unterstützt nicht die Validierung von Server- und Clientzertifikaten mithilfe von [Stammzertifikaten von Zertifizierungsstellen](api-management-howto-ca-certificates.md), die in eine API Management-Instanz hochgeladen wurden. Bei Clients, die dem selbstgehosteten Gateway ein benutzerdefiniertes Zertifikat vorlegen, kann es zu langsamen Antworten kommen, da es bei der Überprüfung der Zertifikatssperrliste auf dem Gateway sehr lange bis zu einem Timeout dauern kann. 

Als Problemumgehung beim Ausführen des Gateways können Sie die IP-Adresse der PKI so konfigurieren, dass sie auf die Adresse von localhost (127.0.0.1) statt auf die API Management-Instanz zeigt. Dies führt dazu, dass die Überprüfung der Zertifikatsperrliste schnell fehlschlägt, wenn das Gateway versucht, das Zertifikat des Clients zu überprüfen. Fügen Sie zum Konfigurieren des Gateways einen DNS-Eintrag für die API Management-Instanz hinzu, der in der Datei `/etc/hosts` im Container in localhost aufgelöst wird. Sie können diesen Eintrag während der Gatewaybereitstellung hinzufügen:
 
* Fügen Sie für die Docker-Bereitstellung dem Befehl `docker run` den Parameter `--add-host <hostname>:127.0.0.1` hinzu. Weitere Informationen finden Sie unter [Hinzufügen von Einträgen zur Containerdatei „hosts“](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host).
 
* Fügen Sie für die Kubernetes-Bereitstellung zur Konfigurationsdatei `myGateway.yaml` die Spezifikation `hostAliases` hinzu. Weitere Informationen finden Sie unter [Adding entries to Pod /etc/hosts with Host Aliases](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/) (Hinzufügen von Einträgen zu Pod (/etc/hosts) mit Hostaliasen).




## <a name="next-steps"></a>Nächste Schritte

-   [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung](./api-management-howto-mutual-certificates.md)
-   [Hochladen von Zertifikaten](./api-management-howto-mutual-certificates.md)
