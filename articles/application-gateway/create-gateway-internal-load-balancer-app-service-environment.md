---
title: 'Behandeln von Problemen mit einem Anwendungsgateway in Azure: ILB mit ASE | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie unter Verwendung eines internen Lastenausgleichs mit einer App Service-Umgebung in Azure Probleme mit einem Anwendungsgateway behandeln.
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: baed2b23a321c53a614303d3085fbb3a4bf6ad0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831094"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Back-End-Serverzertifikat nicht in der Whitelist für ein Anwendungsgateway enthalten (bei Verwendung eines internen Lastenausgleichs mit einer App Service-Umgebung)

In diesem Artikel wird die Behebung des folgenden Problems behandelt: Ein Zertifikat ist nicht in der Whitelist enthalten, wenn Sie ein Anwendungsgateway unter Verwendung eines internen Lastenausgleichs (Internal Load Balancer, ILB) und einer App Service-Umgebung (App Service Environment, ASE) am Back-End erstellen und dabei End-to-End-SSL in Azure verwenden.

## <a name="symptoms"></a>Symptome

Wenn Sie ein Anwendungsgateway unter Verwendung eines ILB mit einer ASE am Back-End erstellen, kann es vorkommen, dass der Back-End-Server fehlerhaft wird. Dieses Problem tritt auf, wenn das Authentifizierungszertifikat des Anwendungsgateways nicht dem Zertifikat entspricht, das auf dem Back-End-Server konfiguriert ist. Beispielszenario:

**Konfiguration des Anwendungsgateways:**

- **Listener:** Multi-Site
- **Port**: 443
- **Hostname:** test.appgwtestase.com
- **SSL-Zertifikat**: CN=test.appgwtestase.com
- **Back-End-Pool:** IP-Adresse oder FQDN
- **IP-Adresse:** 10.1.5.11
- **HTTP-Einstellungen:** HTTPS
- **Port:** : 443
- **Benutzerdefinierter Test:** Hostname: test.appgwtestase.com
- **Authentifizierungszertifikat:** CER-Datei von „test.appgwtestase.com“
- **Back-End-Integrität:** Fehlerhaft (Back-End-Serverzertifikat nicht in Whitelist für Application Gateway enthalten)

**ASE-Konfiguration:**

- **ILB-IP-Adresse:** 10.1.5.11
- **Domänenname:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL-Bindung:** SNI SSL – CN=test.appgwtestase.com

Wenn Sie auf das Anwendungsgateway zugreifen, wird die folgende Fehlermeldung angezeigt, da der Back-End-Server fehlerhaft ist:

**502 - Webserver hat als Gateway oder Proxyserver eine ungültige Antwort erhalten.**

## <a name="solution"></a>Lösung

Wenn Sie für den Zugriff auf eine HTTPS-Website keinen Hostnamen verwenden, gibt der Back-End-Server das konfigurierte Zertifikat für die Standardwebsite zurück, falls SNI deaktiviert ist. Bei einem ILB mit ASE basiert das Standardzertifikat auf dem ILB-Zertifikat. Sind keine konfigurierten Zertifikate für den ILB vorhanden, basiert das Zertifikat auf dem ASE-App-Zertifikat.

Wenn Sie für den ILB-Zugriff einen vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verwenden, gibt der Back-End-Server das richtige Zertifikat zurück, das in den HTTP-Einstellungen hochgeladen wird. Wenn dies nicht der Fall ist, haben Sie folgende Möglichkeiten:

- Verwenden Sie den FQDN im Back-End-Pool des Anwendungsgateways, um auf die IP-Adresse des ILB zu verweisen. Diese Option funktioniert nur, wenn Sie eine private DNS-Zone oder ein benutzerdefiniertes DNS konfiguriert haben. Andernfalls müssen Sie einen A-Eintrag für ein öffentliches DNS erstellen.

- Verwenden Sie das hochgeladene Zertifikat für den ILB oder das Standardzertifikat (ILB-Zertifikat) in den HTTP-Einstellungen. Das Anwendungsgateway ruft das Zertifikat ab, wenn es auf die ILB-IP-Adresse für den Test zugreift.

- Verwenden Sie ein Platzhalterzertifikat für den ILB und den Back-End-Server, sodass das Zertifikat für alle Websites einheitlich ist. Diese Lösung ist jedoch nur bei Unterdomänen möglich und nicht, wenn jede der Websites unterschiedliche Hostnamen erfordert.

- Deaktivieren Sie die Option **Use for App service** (Für App-Dienst verwenden) für das Anwendungsgateway, falls Sie die IP-Adresse des ILB verwenden.

Zur Vereinfachung können Sie das ILB-Zertifikat in den HTTP-Einstellungen hochladen, damit der Testpfad funktioniert. (Dieser Schritt betrifft lediglich die Aufnahme in die Whitelist. Es wird nicht für die SSL-Kommunikation verwendet.) Sie können das ILB-Zertifikat abrufen, indem Sie in Ihrem Browser über HTTPS mit der IP-Adresse des ILB auf den ILB zugreifen, das SSL-Zertifikat in ein Base64-codiertes CER-Format exportieren und das Zertifikat anschließend in den entsprechenden HTTP-Einstellungen hochladen.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.
