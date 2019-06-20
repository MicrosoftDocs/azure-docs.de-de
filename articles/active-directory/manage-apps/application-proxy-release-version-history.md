---
title: 'Azure AD-Anwendungsproxy: Versionsverlauf | Microsoft-Dokumentation'
description: In diesem Artikel werden alle Versionen des Azure AD-Anwendungsproxys aufgeführt sowie neue Features und behobene Probleme beschrieben.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af05769861f98ec192789d90f8a61f5b6638d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783283"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD-Anwendungsproxy: Verlauf der Versionsveröffentlichungen
In diesem Artikel werden die veröffentlichten Versionen und Features des Anwendungsproxys von Azure Active Directory (Azure AD) aufgeführt. Das Azure AD-Team aktualisiert den Anwendungsproxy regelmäßig mit neuen Features und Funktionen. Anwendungsproxyconnectors werden automatisch aktualisiert, wenn eine neue Version veröffentlicht wird.

Hier finden Sie eine Liste der zugehörigen Ressourcen:

Resource |  Details
--------- | --------- |
Aktivieren des Anwendungsproxys | In diesem [Tutorial](application-proxy-add-on-premises-application.md) werden die Voraussetzungen zum Aktivieren des Anwendungsproxys und zum Installieren und Registrieren eines Connectors beschrieben.
Grundlegendes zu Azure AD-Anwendungsproxyconnectors | Erfahren Sie mehr über die [Connectorverwaltung](application-proxy-connectors.md) und die [automatische Aktualisierung](application-proxy-connectors.md#automatic-updates) von Connectors.
Herunterladen des Azure AD-Anwendungsproxyconnectors |  [Laden Sie den aktuellen Connector herunter](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Releasestatus

20\. September 2018: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Es wurde WebSocket-Unterstützung für die Anwendung QlikSense hinzugefügt. Weitere Informationen zum Integrieren von QlikSense mit dem Anwendungsproxy finden Sie in dieser [exemplarischen Vorgehensweise](application-proxy-qlik.md). 
- Der Installations-Assistent wurde verbessert, um das Konfigurieren eines ausgehenden Proxys zu vereinfachen. 
- TLS 1.2 wurde als Standardprotokoll für Connectors festgelegt. 
- Es wurden neue Microsoft-Software-Lizenzbedingungen hinzugefügt.  

### <a name="fixed-issues"></a>Behobene Probleme

- Es wurde ein Fehler behoben, der zu Arbeitsspeicherverlusten im Connector führte.
- Die Azure Service Bus-Version wurde aktualisiert, wobei auch Connectortimeoutfehler behoben wurden.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Releasestatus

19\. Januar 2018: Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme

- Es wurde Unterstützung für benutzerdefinierte Domänen hinzugefügt, die eine Domänenübersetzung im Cookie erfordern.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Releasestatus 

25\. Mai 2017: Für den Download veröffentlicht 

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 

Verbesserte Kontrolle über die Beschränkungen für ausgehende Verbindungen von Connectors. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Releasestatus

15\. April 2017: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Das Onboarding und die Verwaltung wurden vereinfacht, wobei weniger Ports erforderlich sind. Für den Anwendungsproxy müssen jetzt nur zwei ausgehende Standardports geöffnet werden: 443 und 80. Der Anwendungsproxy verwendet weiterhin nur ausgehende Verbindungen, sodass Sie in einer DMZ keine Komponenten benötigen. Weitere Informationen finden Sie in unserer  [Konfigurationsdokumentation](application-proxy-add-on-premises-application.md).  
- Sie können Ihr Netzwerk jetzt über DNS statt über den IP-Adressbereich öffnen, wenn dies von Ihrem externen Proxy oder Ihrer Firewall unterstützt wird. Anwendungsproxydienste erfordern nur Verbindungen mit „*.msappproxy.net“ und „*.servicebus.windows.net“.


## <a name="earlier-versions"></a>Frühere Versionen

Wenn Sie eine Anwendungsproxyconnector-Version vor 1.5.36.0 verwenden, aktualisieren Sie auf die neueste Version, um sicherzustellen, dass Sie über die neuesten vollständig unterstützten Features verfügen.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über den [Remotezugriff auf lokale Anwendungen über den Azure Active Directory-Anwendungsproxy](application-proxy.md).
- Erste Schritte mit dem Anwendungsproxy finden Sie in [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy](application-proxy-add-on-premises-application.md).
