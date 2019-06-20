---
title: SSL-Richtlinienübersicht für Azure Application Gateway | Microsoft-Dokumentation
description: Erfahren Sie, welche Konfigurationsmöglichkeiten Azure Application Gateway für die SSL-Richtlinie bietet.
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: 46a823e4e230656b53a93a97f195d0879fd08bf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122233"
---
# <a name="application-gateway-ssl-policy-overview"></a>SSL-Richtlinienübersicht für Azure Application Gateway

Mit Azure Application Gateway können Sie die SSL-Zertifikatverwaltung zentralisieren sowie den Ver- und Entschlüsselungsaufwand für eine Back-End-Serverfarm verringern. Diese zentralisierte SSL-Behandlung ermöglicht auch die Angabe einer zentralen SSL-Richtlinie, die auf die Sicherheitsanforderungen Ihrer Organisation abgestimmt ist. Dadurch können Sie sowohl Complianceanforderungen erfüllen als auch Sicherheitsrichtlinien und empfohlene Vorgehensweisen umsetzen.

Mit der SSL-Richtlinie können Sie die SSL-Protokollversion sowie Verschlüsselungssammlungen und die Reihenfolge steuern, in der Verschlüsselungen im Rahmen des SSL-Handshakes verwendet werden. Application Gateway bietet zwei Mechanismen für das Steuern der SSL-Richtlinie. Sie können eine vordefinierte Richtlinie oder eine benutzerdefinierte Richtlinie verwenden.

## <a name="predefined-ssl-policy"></a>Vordefinierte SSL-Richtlinie

Application Gateway verfügt über drei vordefinierte Sicherheitsrichtlinien. Sie können Ihr Gateway mit diesen Richtlinien konfigurieren, um das gewünschte Maß an Sicherheit zu erhalten. Die Richtliniennamen werden mit dem Jahr und Monat versehen, in dem sie konfiguriert wurden. Jede Richtlinie bietet verschiedene SSL-Protokollversionen und Verschlüsselungssammlungen. Verwenden Sie möglichst die neuesten SSL-Richtlinien, um ein Höchstmaß an SSL-Sicherheit zu gewährleisten.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Eigenschaft  |Wert  |
|---|---|
|NAME     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Standard| True (wenn keine vordefinierte Richtlinie angegeben ist) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Eigenschaft  |Wert  |
|   ---      |  ---       |
|NAME     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Standard| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Eigenschaft  |Wert  |
|---|---|
|NAME     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Standard| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Benutzerdefinierte SSL-Richtlinie

Wenn eine vordefinierte SSL-Richtlinie für Ihre Anforderungen konfiguriert werden muss, müssen Sie eine eigene benutzerdefinierte SSL-Richtlinie definieren. Mit einer benutzerdefinierten SSL-Richtlinie haben Sie die uneingeschränkte Kontrolle über die zu unterstützende Mindestversion des SSL-Protokolls sowie über unterstützte Verschlüsselungssammlungen und deren jeweilige Priorität.
 
### <a name="ssl-protocol-versions"></a>SSL-Protokollversionen

* SSL 2.0 und 3.0 sind für alle Anwendungsgateways standardmäßig deaktiviert. Diese Protokollversionen sind nicht konfigurierbar.
* Bei einer benutzerdefinierten SSL-Richtlinie können Sie ein beliebiges der drei folgenden Protokolle als SSL-Mindestprotokollversion für Ihr Gateway auswählen: TLSv1_0, TLSv1_1 und TLSv1_2.
* Wenn keine SSL-Richtlinie definiert wird, werden alle drei Protokolle („TLSv1_0“, „TLSv1_1“ und „TLSv1_2“) aktiviert.

### <a name="cipher-suites"></a>Verschlüsselungssammlungen

Application Gateway unterstützt die folgenden Verschlüsselungssammlungen. Aus diesen können Sie Ihre benutzerdefinierte Richtlinie auswählen. Die Reihenfolge der Verschlüsselungssammlungen bestimmt ihre Priorität bei der SSL-Aushandlung.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie erfahren möchten, wie Sie die SSL-Auslagerung konfigurieren, lesen Sie [Konfigurieren einer SSL-Richtlinie auf einem Anwendungsgateway](application-gateway-configure-ssl-policy-powershell.md).
