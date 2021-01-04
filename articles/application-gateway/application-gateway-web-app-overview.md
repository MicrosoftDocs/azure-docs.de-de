---
title: Mehrinstanzenfähige Back-Ends
titleSuffix: Azure Application Gateway
description: Diese Seite bietet eine Übersicht über die Application Gateway-Unterstützung für mehrinstanzenfähige Back-Ends.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397687"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway-Unterstützung für mehrinstanzenfähige Back-Ends wie App Service

In mehrinstanzenfähigen Webserverarchitekturen werden mehrere Websites auf derselben Webserverinstanz ausgeführt. Hostnamen werden verwendet, um zwischen den gehosteten Anwendungen zu unterscheiden. Der vom Client eingehende HTTP-Hostheader wird von Application Gateway standardmäßig nicht geändert und in der vorliegenden Form an das Back-End gesendet. Dieses Verfahren ist für Elemente von Back-End-Pools wie Netzwerkschnittstellen, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen und vollqualifizierte Domänennamen gut geeignet, da diese keinen bestimmten Hostheader bzw. keine bestimmte SNI-Erweiterung benötigen, um zum richtigen Endpunkt aufzulösen. Viele Dienste wie Azure App Service-Web-Apps und die Azure API Management sind jedoch mehrinstanzenfähig und benötigen für die Auflösung zum richtigen Endpunkt einen bestimmten Hostheader oder eine SNI-Erweiterung. In der Regel unterscheidet sich der DNS-Name der Anwendung, der wiederum dem DNS-Namen entspricht, der dem Anwendungsgateway zugeordnet ist, vom Domänennamen des Back-End-Diensts. Deshalb ist der Hostheader, der in der vom Anwendungsgateway empfangenen ursprünglichen Anforderung enthalten ist, ein anderer als der Hostname des Back-End-Diensts. Wenn der Hostheader in der Anforderung vom Anwendungsgateway also nicht in den Hostnamen des Back-End-Diensts geändert wird, können mehrinstanzenfähige Back-Ends die Anforderung nicht zum richtigen Endpunkt auflösen. 

Application Gateway bietet eine Funktion, mit der Benutzer den HTTP-Hostheader in der Anforderung je nach Hostnamen des Back-Ends überschreiben können. So werden mehrinstanzenfähige Back-Ends wie Azure App Service-Web-Apps und API Management unterstützt. Die Funktion ist sowohl für die Standard- als auch für die WAF-SKU (v1 und v2) verfügbar. 

![Hostnamen überschreiben](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Auf die Azure App Service-Umgebung kann dieses Verfahren nicht angewendet werden, da es sich um eine dedizierte Ressource handelt, nicht um eine mehrinstanzfähige Ressource wie Azure App Service.

## <a name="override-host-header-in-the-request"></a>Überschreiben des Hostheaders in der Anforderung

Die Möglichkeit zum Angeben einer Hostüberschreibung wird in den [HTTP-Einstellungen](./configuration-overview.md#http-settings) festgelegt und kann bei der Regelerstellung für einen beliebigen Back-End-Pool angewendet werden. Hostheader und SNI-Erweiterung können bei mehrinstanzenfähigen Back-Ends auf zwei Arten überschrieben werden:

- Der Hostnamen kann auf einen Wert festgelegt werden, der explizit in den HTTP-Einstellungen eingegeben wird. Hierdurch wird sichergestellt, dass der Hostheader für sämtlichen eingehenden Datenverkehr des Back-End-Pools, auf den diese HTTP-Einstellungen angewendet werden, mit diesem Wert überschrieben wird. Bei Verwendung von End-to-End-TLS wird der überschriebene Hostname in der SNI-Erweiterung verwendet. Diese Funktion ermöglicht Szenarien, bei denen eine Back-End-Poolfarm einen Hostheader erwartet, der sich vom eingehenden Hostheader des Kunden unterscheidet.

- Ableiten des Hostnamens von der IP-Adresse oder dem FQDN der Back-End-Poolmitglieder: Die HTTP-Einstellungen enthalten auch eine Option zur dynamischen Auswahl des Hostnamens vom vollqualifizierten Domänennamen eines Back-End-Poolelements, sofern die Option zur Ableitung des Hostnamens von einem einzelnen Back-End-Poolelement konfiguriert ist. Bei Verwendung von End-to-End-TLS wird dieser Hostname vom FQDN abgeleitet und in der SNI-Erweiterung verwendet. Diese Funktion ermöglicht Szenarien, in denen ein Back-End-Pool mehrere mehrinstanzenfähige PaaS-Dienste (z.B. Azure Web Apps) enthalten kann und der Hostheader der Anforderung für die einzelnen Mitglieder den vom entsprechenden FQDN abgeleiteten Hostnamen enthält. Für die Implementierung dieses Szenarios wird die Option [Hostnamen aus Back-End-Adresse auswählen](./configuration-http-settings.md#pick-host-name-from-back-end-address) in den HTTP-Einstellungen eingesetzt, durch die der Hostheader in der ursprünglichen Anforderung dynamisch mit dem erwähnten Hostheader im Back-End-Pool überschrieben wird.  Wenn der vollqualifizierte Domänenname Ihres Back-End-Pools beispielsweise „contoso11.azurewebsites.net“ und „contoso22.azurewebsites.net“ enthält, wird der ursprüngliche Hostheader der Anforderung (contoso.com) mit „contoso11.azurewebsites.net“ oder „contoso22.azurewebsites.net“ überschrieben, wenn die Anforderung an den entsprechenden Back-End-Server gesendet wird. 

  ![Web-App-Szenario](./media/application-gateway-web-app-overview/scenario.png)

Mit dieser Funktion können Kunden die Optionen in den HTTP-Einstellungen und benutzerdefinierten Tests auf die entsprechende Konfiguration festlegen. Diese Einstellung wird dann mithilfe einer Regel an einen Listener und an einen Back-End-Pool gebunden.

## <a name="special-considerations"></a>Besondere Überlegungen

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS-Terminierung und End-to-End-TLS mit mehrinstanzenfähigen Diensten

Sowohl die TLS-Terminierung als auch die End-to-End-TLS-Verschlüsselung werden für mehrinstanzenfähige Dienste unterstützt. Wenn die TLS-Terminierung im Anwendungsgateway durchgeführt werden soll, muss weiterhin ein TLS-Zertifikat zum Listener des Anwendungsgateways hinzugefügt werden. Bei der End-to-End-TLS-Verschlüsselung ist es für vertrauenswürdige Azure-Dienste wie Azure App Service-Web-Apps jedoch nicht erforderlich, dass die Back-Ends im Anwendungsgateway zugelassen werden. Daher müssen auch keine Authentifizierungszertifikate hinzugefügt werden. 

![End-to-End-TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Auf der vorherigen Abbildung sehen Sie, dass keine Authentifizierungszertifikate hinzugefügt werden müssen, wenn App Service als Back-End ausgewählt ist.

### <a name="health-probe"></a>Integritätstest

Das Überschreiben des Hostheaders in den **HTTP-Einstellungen** wirkt sich nur auf die Anforderung und deren Routing aus, nicht auf das Verhalten von Integritätstests. Für die Verwendung von End-to-End-Funktionen müssen sowohl die Test- als auch die HTTP-Einstellungen an die korrekte Konfiguration angepasst werden. Zusätzlich zur Möglichkeit, einen Hostheader in der Testkonfiguration festzulegen, unterstützen benutzerdefinierte Tests auch die Ableitung des Hostheaders aus den derzeit konfigurierten HTTP-Einstellungen. Diese Konfiguration kann mithilfe des Parameters `PickHostNameFromBackendHttpSettings` in der Testkonfiguration angegeben werden.

### <a name="redirection-to-app-services-url-scenario"></a>Anwendungsfälle für die Umleitung zur App Service-URL

In manchen Fällen leitet der Hostname in der Antwort von App Service den Browser des Endbenutzers beispielsweise zum Hostnamen *.azurewebsites.net anstatt zu der Domäne weiter, die dem Anwendungsgateway zugeordnet wurde. Dieses Problem kann in folgenden Fällen auftreten:

- Sie haben für App Service eine Umleitung konfiguriert. Diese kann im einfachsten Fall auf einen nachgestellten Schrägstrich zurückzuführen sein, der der Anforderung hinzugefügt wurde.
- Die Umleitung wird durch die Nutzung der Azure AD-Authentifizierung ausgelöst.

Weitere Informationen zum Beheben solcher Probleme finden Sie im Artikel zur [Behandlung von Problemen mit der Umleitung zur App Service-URL](./troubleshoot-app-service-redirection-app-service-url.md).

## <a name="next-steps"></a>Nächste Schritte

Unter [Konfigurieren von App Service mit Application Gateway](./configure-web-app-portal.md) erfahren Sie, wie Sie ein Anwendungsgateway mit einer mehrinstanzenfähigen App wie einer Azure App Service-Web-App als Back-End-Poolelement einrichten.
