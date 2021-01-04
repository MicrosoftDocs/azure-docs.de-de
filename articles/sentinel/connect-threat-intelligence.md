---
title: Verknüpfen von Threat Intelligence-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Threat Intelligence-Daten mit Azure Sentinel verknüpfen.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121742"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Verknüpfen von Daten von Threat Intelligence-Anbietern

> [!IMPORTANT]
> Die Threat Intelligence-Datenconnectors in Azure Sentinel befinden sich aktuell in der Public Preview-Phase.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Azure Sentinel können Sie die von Ihrer Organisation verwendeten Bedrohungsindikatoren importieren. Dies kann dazu beitragen, dass Ihre Sicherheitsanalysten bekannte Bedrohungen besser erkennen und priorisieren können. Einige Features von Azure Sentinel werden dann verfügbar oder wurden verbessert:

- **Analytics** enthält eine Reihe von Vorlagen für geplante Regeln, die Sie aktivieren können, um Warnungen und Incidents zu generieren, die auf Übereinstimmungen von Protokollereignissen aus Ihren Bedrohungsindikatoren basieren.

- **Arbeitsmappen** bieten zusammengefasste Informationen zu den in Azure Sentinel importierten Bedrohungsindikatoren und allen Warnungen, die von Analyseregeln generiert werden, die Ihren Bedrohungsindikatoren entsprechen.

- **Hunting**-Abfragen ermöglichen es Sicherheitsbeauftragten, Bedrohungsindikatoren im Rahmen allgemeiner Hunting-Szenarien zu verwenden.

- **Notebooks** können Bedrohungsindikatoren verwenden, wenn Sie Anomalien untersuchen und nach schädlichen Verhaltensweisen suchen.

Sie können Bedrohungsindikatoren an Azure Sentinel streamen, indem Sie eines der im Abschnitt „Verbinden von Azure Sentinel mit TAXII-Servern“ aufgeführten Produkte der integrierten Threat Intelligence-Plattform (TIP) oder aber die direkte Integration mit der [Microsoft Graph-Sicherheits-API „tiIndicators“](/graph/api/resources/tiindicator) verwenden.

## <a name="integrated-threat-intelligence-platform-products"></a>Produkte der integrierten Threat Intelligence Platform

- [MISP-Open-Source-Threat Intelligence Platform](https://www.misp-project.org/)
    
    Ein Beispielskript, das Clients mit MISP-Instanzen zur Migration von Bedrohungsindikatoren in die Microsoft Graph-Sicherheits-API bereitstellt, finden Sie unter [MISP zu Microsoft Graph-Sicherheitsskript](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    Auf der Seite für [ThreatStream-Downloads](https://ui.threatstream.com/downloads) können Sie ThreatStream Integrator und Erweiterungen herunterladen. Außerdem finden Sie dort Anweisungen dazu, wie Sie ThreatStream-Daten mit der Microsoft Graph-Sicherheits-API verbinden.

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Anleitungen finden Sie unter [Senden von IOCs an die Microsoft Graph-Sicherheits-API mit MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect-Plattform](https://threatconnect.com/solution/)

    Weitere Informationen finden Sie auf der Seite [ThreatConnect-Integrationen](https://threatconnect.com/integrations/) unter „Microsoft Graph Security API“ (Microsoft Graph-Sicherheits-API).

- [EclecticIQ Platform](https://www.eclecticiq.com/solutions)

- [ThreatQ Threat Intelligence Platform](https://www.threatq.com/)

    Weitere Informationen und Anweisungen finden Sie unter [Microsoft Sentinel Connector for ThreatQ integration](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview) (Microsoft Sentinel-Connector für die ThreatQ-Integration).

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Verbinden von Azure Sentinel mit Ihrer Threat Intelligence-Plattform

### <a name="prerequisites"></a>Voraussetzungen  

- Azure AD-Rolle des Unternehmensadministrators oder Sicherheitsadministrators, um Berechtigungen für Ihr TIP-Produkt oder Ihre benutzerdefinierte Anwendung zu erteilen, die die direkte Integration mit der tiIndicators-API von Microsoft Graph-Sicherheit verwendet.

- Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich, um Ihre Bedrohungsindikatoren zu speichern.

### <a name="instructions"></a>Instructions

1. [Registrieren Sie eine Anwendung](/graph/auth-v2-service#1-register-your-app) in Azure Active Directory, um eine Anwendungs-ID, ein Anwendungsgeheimnis und eine Azure Active Directory-Mandanten-ID zu erhalten. Sie benötigen diese Werte, wenn Sie Ihr integriertes TIP-Produkt oder Ihre integrierte Anwendung konfigurieren, die eine direkte Integration mit der tiIndicators-API der Microsoft Graph-Sicherheit verwendet.

2. [Konfigurieren Sie API-Berechtigungen](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) für die registrierte Anwendung: Fügen Sie Ihrer registrierten Anwendung die Microsoft Graph-Anwendungsberechtigung **ThreatIndicators.ReadWrite.OwnedBy** hinzu.

3. Bitten Sie Ihren Azure Active Directory-Mandantenadministrator, der registrierten Anwendung die Administratoreinwilligung für Ihre Organisation zu erteilen. Gehen Sie im Azure-Portal so vor: **Azure Active Directory** > **App-Registrierungen** >  **\<_app name_>**  > **API-Berechtigungen anzeigen** > **Administratoreinwilligung erteilen für \<_tenant name_>** .

4. Konfigurieren Sie das TIP-Produkt oder die TIP-App, das bzw. die die direkte Integration mit der tiIndicators-API der Microsoft Graph-Sicherheit verwendet, um Indikatoren an Azure Sentinel zu senden, indem Sie Folgendes angeben:
    
    a. Die Werte für die ID, das Geheimnis und die Mandanten-ID der registrierten Anwendung.
    
    b. Geben Sie für das Zielprodukt „Azure Sentinel“ an.
    
    c. Geben Sie für die Aktion „Warnung“ an.

5. Navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Datenconnectors**, und wählen Sie dann den Connector **Threat Intelligence Platforms (Vorschauversion)** aus.

6. Wählen Sie **Connectorseite öffnen** und dann **Verbinden** aus.

7. Um die in Azure Sentinel importierten Bedrohungsindikatoren anzuzeigen, navigieren Sie zu **Azure Sentinel – Protokolle** > **SecurityInsights**, und erweitern Sie **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Verbinden von Azure Sentinel mit TAXII-Servern

### <a name="prerequisites"></a>Voraussetzungen

- Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich, um Ihre Bedrohungsindikatoren zu speichern.

- TAXII 2.0-Server-URI und Sammlungs-ID

### <a name="instructions"></a>Instructions

1. Navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Datenconnectors**, und wählen Sie den Connector **Threat Intelligence – TAXII (Vorschauversion)** aus.

2. Wählen Sie **Connectorseite öffnen** aus.

3. Geben Sie in den Textfeldern die erforderlichen und optionalen Informationen an.

4. Wählen Sie **Hinzufügen** aus, um die Verbindung mit dem TAXII 2.0-Server zu aktivieren.

5. Sollten Sie über zusätzliche TAXII 2.0-Server verfügen: Wiederholen Sie die Schritte 3 und 4.

6. Um die in Azure Sentinel importierten Bedrohungsindikatoren anzuzeigen, navigieren Sie zu **Azure Sentinel – Protokolle** > **SecurityInsights**, und erweitern Sie **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Ihren Threat Intelligence-Anbieter mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln.

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).