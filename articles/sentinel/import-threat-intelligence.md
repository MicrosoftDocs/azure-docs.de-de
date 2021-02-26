---
title: Importieren von Threat Intelligence in Azure Sentinel | Microsoft-Dokumentation
description: Verwenden Sie die Threat Intelligence-Feeds in Azure Sentinel, um Daten zu analysieren, Bedrohungen zu erkennen und Warnungen und Incidents zu generieren. Visualisieren Sie Informationen zur Threat Intelligence mithilfe von Arbeitsmappen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 5bf94d7c5889bb5a743655d65e48f021e91e01b7
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807138"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importieren von Threat Intelligence in Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Einführung in Threat Intelligence

Cyber Threat Intelligence (CTI) umfasst Informationen, die bekannte vorhandene oder potenzielle Bedrohungen für Systeme und Benutzer beschreiben. Diese Art von Informationen hat viele Formen, von geschriebenen Berichten zur Motivation des Akteurs einer Bedrohung, zur Infrastruktur und den Techniken bis hin zu genauen Beobachtungen von IP-Adressen, Domänen und Dateihashes, die mit Cyberbedrohungen in Verbindung stehen. CTI wird von Organisationen verwendet, um wichtige Kontextinformationen für ungewöhnliche Aktivitäten bereitzustellen, damit Sicherheitsmitarbeiter schnell Maßnahmen zum Schutz ihrer Kollegen und Ressourcen ergreifen können. CTI kann aus vielen Quellen stammen, z. B. aus Open-Source-Datenfeeds, Communitys zum Teilen von Threat Intelligence, kostenpflichtigen Intelligence-Feeds und Sicherheitsuntersuchungen mit lokalen Informationen in Unternehmen.

Innerhalb einer SIEM-Lösung (Security Information & Event Management) wie Azure Sentinel wird CTI am häufigsten in Form von Bedrohungsindikatoren angewandt, die oft als Anzeichen einer Kompromittierung (Indicator of Compromise, IoC) bezeichnet werden. Bedrohungsindikatoren sind Daten, die Beobachtungen wie URLs, Dateihashes oder IP-Adressen bekannten Bedrohungsaktivitäten wie Phishing, Botnets oder Schadsoftware zuordnen. Diese Form der Threat Intelligence wird oft als taktische Bedrohungsanalyse bezeichnet, da Sicherheitsprodukte und -automatisierungen damit in großem Umfang Schutzmaßnahmen ergreifen und potenzielle Bedrohungen für eine Organisation erkennen können. In Azure Sentinel können Sie Bedrohungsindikatoren verwenden, um schädliche Aktivitäten in Ihrer Umgebung zu erkennen und den Sicherheitsanalysten Kontextinformationen bereitzustellen, damit sie fundierte Entscheidungen für eine Reaktion treffen können.

Sie können Threat Intelligence (TI) mithilfe der folgenden Aktivitäten in Azure Sentinel integrieren:

- Verwenden Sie **Datenconnectors** auf verschiedene TI-Plattformen, um in Azure Sentinel [Threat Intelligence zu importieren](./connect-threat-intelligence.md).
- Sie können die importierten Threat Intelligence-Daten in **Protokollen** und im neuen Bereich **Threat Intelligence** von Azure Sentinel anzeigen und verwalten.
- Verwenden Sie die integrierten Regelvorlagen für die **Analyse**, um Sicherheitswarnungen und -incidents mithilfe Ihrer importierten Bedrohungsinformationen zu generieren.
- Visualisieren Sie wichtige Informationen zu Ihren Threat Intelligence-Daten in Azure Sentinel mit der **Threat Intelligence-Arbeitsmappe**.

Threat Intelligence bietet auch einen nützlichen Kontext für andere Azure Sentinel-Umgebungen wie **Hunting** und **Notebooks**. Diese werden in diesem Artikel nicht behandelt, aber [dieser lesenswerte Blogbeitrag von Ian Hellen zu Jupyter Notebooks in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) beschreibt die Verwendung von CTI in Notebooks.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Azure Sentinel-Datenconnectors für Threat Intelligence

Azure Sentinel importiert Bedrohungsindikatoren wie auch alle anderen Ereignisdaten mithilfe von Datenconnectors. Es stehen zwei Azure Sentinel-Datenconnectors speziell für Bedrohungsindikatoren zur Verfügung: **Threat Intelligence – TAXII** und **Threat Intelligence-Plattformen**. Je nachdem, woher Ihre Organisation Daten zu Bedrohungsindikatoren bezieht, können Sie einen oder beide Datenconnectors verwenden. Im Folgenden erfahren Sie mehr über die einzelnen Datenconnectors.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Hinzufügen von Bedrohungsindikatoren in Azure Sentinel mit dem Datenconnector „Threat Intelligence-Plattformen“

Viele Organisationen nutzen TIP-Lösungen (Threat Intelligence Platform), um Feeds mit Bedrohungsindikatoren aus mehreren Quellen zu aggregieren, die Daten innerhalb der Plattform zusammenzustellen und dann auszuwählen, welche Bedrohungsindikatoren auf die verschiedene Sicherheitslösungen wie Netzwerkgeräte, Advanced Threat Protection-Lösungen oder SIEM-Lösungen wie Azure Sentinel angewandt werden sollen. Wenn Ihre Organisation eine integrierte TIP-Lösung wie MISP, Anomali ThreatStream, ThreatConnect, EclecticIQ Platform, ThreatQ Threat Intelligence Platform oder MineMeld von Palo Alto Networks verwendet, können Sie mit dem **Datenconnector für Threat Intelligence-Plattformen** Ihre TIP-Lösung zum Importieren von Bedrohungsindikatoren in Azure Sentinel verwenden. Da der Connector dafür die [Sicherheits-API tiIndicators von Microsoft Graph](/graph/api/resources/tiindicator) nutzt, kann der Connector auch von jeder benutzerdefinierten Threat Intelligence-Plattform verwendet werden, um die tiIndicators-API zum Senden von Indikatoren an Azure Sentinel (und andere Microsoft-Sicherheitslösungen wie Defender ATP) zu nutzen.

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Importpfad für Threat Intelligence":::

Führen Sie die folgenden Schritte aus, um Bedrohungsindikatoren aus Ihrer integrierten TIP- oder einer benutzerdefinierten Threat Intelligence-Lösung in Azure Sentinel zu importieren:

1. Abrufen einer Anwendungs-ID und eines geheimen Clientschlüssels aus Azure Active Directory

1. Einfügen dieser Informationen in Ihre TIP-Lösung oder benutzerdefinierte Anwendung

1. Aktivieren des Datenconnectors „Threat Intelligence-Plattformen“ in Azure Sentinel

Im Folgenden finden Sie eine ausführliche Betrachtung der einzelnen Schritte.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Abrufen einer Anwendungs-ID und eines Clientgeheimnisses aus Azure Active Directory

Unabhängig davon, ob Sie eine TIP- oder eine benutzerdefinierte Lösung verwenden, benötigt die tiIndicators-API einige grundlegende Informationen, um eine Verbindung herzustellen und Bedrohungsindikatoren zu senden. Sie müssen die folgenden drei Informationen bereithalten:
- Anwendungs-ID (Client)
- Verzeichnis-ID (Mandant)
- Geheimer Clientschlüssel

Diese Informationen stammen immer aus Azure Active Directory von einem Prozess namens **App-Registrierung**, der die folgenden drei Schritte umfasst:
- Registrieren einer App bei Azure Active Directory
- Gewähren der von der App benötigten Berechtigungen zum Verbinden mit der Microsoft Graph-API tiIndicators und zum Senden von Bedrohungsindikatoren
- Erhalten der Einwilligung von Ihrer Organisation für das Gewähren dieser Berechtigungen für diese Anwendung  

**Registrieren einer Anwendung mit Azure Active Directory**

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Active Directory**.

1. Wählen Sie die Menüoption **App-Registrierungen** und dann **Neue Registrierung** aus.

1. Wählen Sie einen Namen für die Anwendungsregistrierung aus, aktivieren Sie das Optionsfeld **Einzelner Mandant**, und wählen Sie dann **Registrieren** aus.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registrieren einer Anwendung":::

1. Kopieren Sie auf dem angezeigten Bildschirm die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** . Dies sind die ersten beiden Informationen, die Sie später benötigen, um Ihre TIP- oder benutzerdefinierte Lösung zum Senden von Bedrohungsindikatoren an Azure Sentinel zu konfigurieren.

**Gewähren der von der Anwendung benötigten Berechtigungen**

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Active Directory**.

1. Wählen Sie die Menüoption **App-Registrierungen** und dann Ihre neu registrierte App aus.

1. Wählen Sie die Menüoption **API-Berechtigungen** aus, und klicken Sie auf die Schaltfläche **Berechtigung hinzufügen**.

1. Wählen Sie auf der Seite **API auswählen** die Option **Microsoft Graph** aus, um in einer Liste mit Microsoft Graph-Berechtigungen auszuwählen.

1. Wenn Sie gefragt werden, **Welche Art von Berechtigungen sind für Ihre Anwendung erforderlich?** , wählen Sie **Anwendungsberechtigungen** aus. Dies ist der Berechtigungstyp, der von Anwendungen verwendet wird, die sich mit der App-ID und App-Geheimnissen (API-Schlüssel) authentifizieren.

1. Wählen Sie **ThreatIndicators.ReadWrite.OwnedBy** und dann **Berechtigung hinzufügen** aus, um diese Berechtigung der Liste der Berechtigungen Ihrer App hinzuzufügen.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Angeben von Berechtigungen":::

**Erhalten der Einwilligung von Ihrer Organisation für das Gewähren dieser Berechtigungen**

1. Um eine Einwilligung zu erhalten, muss ein globaler Administrator in Azure Active Directory die Schaltfläche **Grant admin consent for your tenant** (Administratoreinwilligung für Ihren Mandanten) auf der Berechtigungsseite der API Ihrer App auswählen. Wenn Sie nicht über die Rolle „Globaler Administrator“ für Ihr Konto verfügen, ist diese Schaltfläche nicht verfügbar, und Sie müssen einen globalen Administrator Ihrer Organisation bitten, diesen Schritt auszuführen.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Zustimmung erteilen":::

1. Nachdem die Einwilligung für Ihre App erteilt wurde, wird unter **Status** ein grünes Häkchen angezeigt.
 
Nachdem Ihre App registriert wurde und die Berechtigungen erteilt wurden, können Sie die letzte Anforderung auf Ihrer Liste erfüllen: einen geheimen Clientschlüssel für Ihre App abzurufen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Active Directory**.

1. Wählen Sie die Menüoption **App-Registrierungen** und dann Ihre neu registrierte App aus.

1. Wählen Sie im Menü **Zertifikate & Geheimnisse** aus, und klicken Sie auf die Schaltfläche **Neuer geheimer Clientschlüssel**, um ein Geheimnis (API-Schlüssel) für Ihre App zu erhalten.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Abrufen des geheimen Clientschlüssels":::

1. Klicken Sie auf die Schaltfläche **Hinzufügen**, und **kopieren Sie den geheimen Clientschlüssel**, da Sie dieses Geheimnis nicht erneut abrufen können, wenn Sie von dieser Seite weg navigieren. Sie benötigen diesen Wert, wenn Sie Ihre TIP- oder benutzerdefinierte Lösung konfigurieren.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Einfügen dieser Informationen in Ihre TIP-Lösung oder benutzerdefinierte Anwendung

Sie verfügen nun über die drei erforderlichen Informationen, die Sie benötigen, um Ihre TIP- oder Ihre benutzerdefinierte Lösung zum Senden von Bedrohungsindikatoren an Azure Sentinel zu konfigurieren. 
- Anwendungs-ID (Client)
- Verzeichnis-ID (Mandant)
- Geheimer Clientschlüssel

Geben Sie diese Werte in der Konfiguration Ihrer integrierten TIP- oder benutzerdefinierten Lösung an den entsprechenden Stellen ein, und die Bedrohungsindikatoren werden über die Microsoft Graph-API tiIndicators an Azure Sentinel gesendet.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Aktivieren des Datenconnectors „Threat Intelligence-Plattformen“ in Azure Sentinel

Der letzte Schritt im Integrationsprozess besteht darin, den Datenconnector **Threat Intelligence-Plattformen** in Azure Sentinel zu aktivieren. In diesem Schritt werden die Bedrohungsindikatoren, die von Ihrer TIP- oder benutzerdefinierten Lösung gesendet werden, über die Microsoft Graph-API tiIndicators in Azure Sentinel importiert. Diese Indikatoren stehen dann in allen Azure Sentinel-Arbeitsbereichen für Ihre Organisation zur Verfügung. Führen Sie die folgenden Schritte aus, um den Datenconnector „Threat Intelligence-Plattformen“ für alle Arbeitsbereiche zu aktivieren:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren importieren möchten, die von Ihrer TIP- oder benutzerdefinierten Lösung gesendet werden.

1. Wählen Sie im Menü **Datenconnectors** im Connectorkatalog **Threat Intelligence-Plattformen** aus, und klicken Sie auf die Schaltfläche **Connectorseite öffnen**.

1. Da Sie die App-Registrierung bereits abgeschlossen und Ihre TIP- oder benutzerdefinierte Lösung bereits für das Senden von Bedrohungsindikatoren konfiguriert haben, müssen Sie nur noch auf die Schaltfläche **Verbinden** klicken.

Nach einigen Minuten sollten die Bedrohungsindikatoren in den Azure Sentinel-Arbeitsbereich fließen.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Hinzufügen von Bedrohungsindikatoren in Azure Sentinel mit dem Datenconnector „Threat Intelligence – TAXII“

Der am weitesten verbreitete Industriestandard für die Übertragung von Threat Intelligence ist eine [Kombination aus dem STIX-Datenformat und dem TAXII-Protokoll](https://oasis-open.github.io/cti-documentation/). Wenn Ihre Organisation die Bedrohungsindikatoren aus Lösungen erhält, die die aktuelle STIX/TAXII-Version (2.0 oder 2.1) unterstützen, können Sie Ihre Bedrohungsindikatoren mit dem Datenconnector **Threat Intelligence – TAXII** in Azure Sentinel importieren. Der Datenconnector „Threat Intelligence – TAXII“ ermöglicht einem integrierten TAXII-Client in Azure Sentinel das Importieren von Threat Intelligence von TAXII 2.x-Servern.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII-Importpfad":::
 
Gehen Sie folgendermaßen vor, um von einem TAXII-Server Bedrohungsindikatoren im STIX-Format in Azure Sentinel zu importieren:

1. Abrufen der Stamm- und Sammlungs-ID der TAXII-Server-API

1. Aktivieren des Datenconnectors „Threat Intelligence – TAXII“ in Azure Sentinel

Im Folgenden finden Sie eine ausführliche Betrachtung der einzelnen Schritte.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Abrufen der Stamm- und Sammlungs-ID der TAXII-Server-API

TAXII 2.x-Server kündigen API-Stammadressen an, bei denen es sich um URLs handelt, unter denen Threat Intelligence-Sammlungen gehostet werden. In den meisten Fällen kann der API-Stamm über die Dokumentationsseite des Threat Intelligence-Anbieters abgerufen werden, der den TAXII-Server hostet. Manchmal wird jedoch lediglich die URL veröffentlicht, die als Ermittlungsendpunkt bezeichnet wird. In diesem Fall ist es sehr einfach, den API-Stamm mithilfe des Ermittlungsendpunkts zu finden. Wenn Sie den API-Stamm und die Sammlungs-IDs des TAXII-Servers, mit dem Sie arbeiten möchten, bereits kennen, können Sie mit dem nächsten Abschnitt zum **Aktivieren des Datenconnectors „Threat Intelligence – TAXII“ in Azure Sentinel** fortfahren.

Betrachten Sie ein tatsächliches Beispiel für die Verwendung eines einfachen Befehlszeilen-Hilfsprogramms namens [cURL](https://en.wikipedia.org/wiki/CURL), das in Windows und den meisten Linux-Distributionen zur Verfügung steht, zum Ermitteln des API-Stamms und zum Durchsuchen der Sammlungen eines TAXII-Servers ab dem Ermittlungsendpunkt. Für dieses Beispiel wird der Ermittlungsendpunkt des ThreatStream TAXII 2.0-Servers [Anomali Limo](https://www.anomali.com/community/limo) verwendet.

1.  Navigieren Sie in einem Browser zum [Ermittlungsendpunkt des ThreatStream TAXII 2.0-Servers](https://limo.anomali.com/taxii), um den API-Stamm abzurufen. Authentifizieren Sie sich mit dem Benutzer und dem Kennwort `guest`.

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Verwenden Sie das Hilfsprogramm cURL und den API-Stamm (https://limo.anomali.com/api/v1/taxii2/feeds/) aus dem vorherigen Schritt, um die Liste der Sammlungs-IDs zu durchsuchen, die im API-Stamm gehostet werden.

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Sie verfügen nun über alle Informationen, die Sie zum Herstellen einer Verbindung von Azure Sentinel mit einer oder mehreren von Anomali Limo bereitgestellten TAXII-Serversammlungen benötigen.

| **API-Stamm** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Sammlungs-ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch – Ransomware-IP-Adressen**                                  | 135           |
| **Abuse.ch – Ransomware-Domänen**                              | 136           |
| **DShield – Scan-IP-Adressen**                                     | 150           |
| **Malware Domain List – Hotlist**                            | 200           |
| **Blutmagie – TOR-Knoten**                                      | 209           |
| **Emerging Threats – C&C-Server**                              |  31           |
| **Lehigh – Schadsoftwaredomänen**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats – kompromittiert**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Aktivieren des Datenconnectors „Threat Intelligence – TAXII“ in Azure Sentinel

Gehen Sie folgendermaßen vor, um von einem TAXII-Server Bedrohungsindikatoren in Azure Sentinel zu importieren:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren vom TAXII-Server importieren möchten.

1. Wählen Sie im Menü **Datenconnectors** im Connectorkatalog **Threat Intelligence – TAXII** aus, und klicken Sie auf die Schaltfläche **Connectorseite öffnen**.

1. Geben Sie einen **Namen** für diese TAXII-Serversammlung, die **API-Stamm-URL**, die **Sammlungs-ID**, den **Benutzernamen** (falls erforderlich) und das **Kennwort** (falls erforderlich) ein, und klicken Sie auf die Schaltfläche **Hinzufügen**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Konfigurieren von TAXII-Servern":::
 
Sie sollten eine Bestätigung erhalten, dass eine Verbindung mit dem TAXII-Server hergestellt wurde. Sie können Schritt 4 so oft wie gewünscht wiederholen, um eine Verbindung mit mehreren Sammlungen auf demselben oder unterschiedlichen TAXII-Servern herzustellen.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Anzeigen von Bedrohungsindikatoren in Azure Sentinel

Nachdem Sie Bedrohungsindikatoren erfolgreich mit den Datenconnectors **Threat Intelligence-Plattformen** und **Threat Intelligence – TAXII** in Azure Sentinel importiert haben, können Sie diese in der Tabelle **ThreatIntelligenceIndicator** unter **Protokolle** anzeigen, wo alle Azure Sentinel-Ereignisdaten gespeichert sind. Diese Tabelle bildet die Grundlage für Abfragen, die von anderen Azure Sentinel-Features wie Analysen und Arbeitsmappen ausgeführt werden. Im Folgenden finden Sie Informationen zum Suchen und Anzeigen von Bedrohungsindikatoren in der Tabelle **ThreatIntelligenceIndicator**.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mit einem der Threat Intelligence-Datenconnectors importiert haben.

1. Wählen Sie im Abschnitt **Allgemein** des Azure-Sentinel-Menüs **Protokolle** aus.

1. Sie finden die Tabelle **ThreatIntelligenceIndicator** unter der Gruppe **Azure Sentinel**.

1. Wählen Sie neben dem Tabellennamen das Symbol **Datenvorschau** (das Auge) und dann die Schaltfläche **Im Abfrage-Editor anzeigen** aus, um eine Abfrage auszuführen, mit der die Datensätze aus dieser Tabelle angezeigt werden.

Die Ergebnisse sollten den Beispielbedrohungsindikatoren unten ähneln:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Beispielabfragedaten":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Verwalten von Bedrohungsindikatoren im neuen Bereich „Threat Intelligence“ von Azure Sentinel

Mit dem neuen Bereich **Threat Intelligence**, auf den Sie über das Azure Sentinel-Menü zugreifen, können Sie auch die importierten Bedrohungsindikatoren anzeigen, sortieren, filtern und durchsuchen, ohne eine **Protokollabfrage** schreiben zu müssen. Mit diesem neuen Bereich können Sie auch direkt auf der Azure Sentinel-Benutzeroberfläche Bedrohungsindikatoren erstellen und allgemeine Verwaltungsaufgaben für Threat Intelligence ausführen, z. B. das Markieren von Indikatoren mit Tags und das Erstellen neuer Indikatoren im Zusammenhang mit Sicherheitsuntersuchungen.
Betrachten Sie nun zwei der gängigsten Aufgaben: das Erstellen neuer Bedrohungsindikatoren und das Markieren von Indikatoren mit Tags für eine einfache Gruppierung und Referenzierung.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mit einem der Threat Intelligence-Datenconnectors importiert haben.

1. Wählen Sie im Azure Sentinel-Menü im Abschnitt „Bedrohungsmanagement“ die Option **Threat Intelligence** aus.

1. Wählen Sie im Menü oben auf der Seite die Schaltfläche **Neue hinzufügen** aus.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Hinzufügen eines neuen Bedrohungsindikators" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Wählen Sie den Indikatortyp aus, und füllen Sie dann im Panel **Neuer Indikator** die Pflichtfelder aus, die mit einem roten Sternchen (*) gekennzeichnet sind.

1. Klicken Sie auf **Übernehmen**. Der Indikator wird dem Indikatorraster hinzugefügt und auch an die Tabelle „ThreatIntelligenceIndicator“ in **Protokolle** gesendet.

Das Markieren von Bedrohungsindikatoren mit Tags stellt eine einfache Möglichkeit dar, sie zu gruppieren und einfacher auffindbar zu machen. In der Regel können Sie ein Tag auf Indikatoren im Zusammenhang mit einem bestimmten Incident anwenden oder auf Indikatoren, die Bedrohungen von einem bestimmten bekannten Akteur oder einer bekannten Angriffskampagne darstellen. Sie können die Bedrohungsindikatoren einzeln markieren, oder Sie können mehrere Indikatoren auswählen und diese gleichzeitig markieren. Unten finden Sie ein Beispiel für das Markieren mehrerer Indikatoren mit einer Incident-ID. Da das Tagging eine Freiformangabe ist, empfiehlt es sich, Standardnamenskonventionen für Tags für Bedrohungsindikatoren zu erstellen. Sie können mehrere Tags auf jeden Indikator anwenden.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Anwenden von Tags auf Bedrohungsindikatoren" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analytics nutzt Ihre Bedrohungsindikatoren für die Erkennung potenzieller Bedrohungen

Sie haben Ihre Bedrohungsindikatoren in Azure Sentinel übertragen, und Sie haben gesehen, wie Sie sie anzeigen und verwalten können. Nun erfahren Sie, welchen Nutzen sie für Sie haben. Der wichtigste Anwendungsfall für Bedrohungsindikatoren in SIEM-Lösungen wie Azure Sentinel ist die Unterstützung von Analyseregeln.  Diese indikatorbasierten Regeln vergleichen unformatierte Ereignisse aus Ihren Datenquellen mit Ihren Bedrohungsindikatoren, um Sicherheitsbedrohungen in Ihrer Organisation zu erkennen. Sie erstellen in Azure Sentinel **Analytics** Analyseregeln, die nach einem Zeitplan ausgeführt werden und Sicherheitswarnungen generieren. Die Regeln werden über Abfragen und Konfigurationen gesteuert, die festlegen, wie oft die Regel ausgeführt werden soll, welche Art von Abfrageergebnissen Sicherheitswarnungen generieren sollen und welche automatisierten Reaktionen beim Generieren von Warnungen ausgelöst werden.

Sie können stets neue Analyseregeln erstellen. Azure Sentinel bietet aber auch eine Reihe von integrierten Regelvorlagen, die von Microsoft-Sicherheitstechnikern erstellt wurden und die Sie unverändert verwenden oder entsprechend den jeweiligen Anforderungen anpassen können. Sie können Regelvorlagen, die Bedrohungsindikatoren verwenden, ganz einfach identifizieren, da ihr Name immer mit „**TI map**…“ beginnt. Alle diese Regelvorlagen funktionieren ähnlich, wobei der einzige Unterschied darin besteht, welche Art von Bedrohungsindikatoren verwendet wird (Domäne, E-Mail, Dateihash, IP-Adresse oder URL) und mit welchem Ereignistyp ein Abgleich erfolgen soll. Jede Vorlage listet die Datenquellen auf, die für das Funktionieren der Regel erforderlich sind, sodass Sie auf einen Blick erkennen können, ob Sie die nötigen Ereignisse bereits in Azure Sentinel importiert haben.

Sehen Sie sich eine dieser Regelvorlagen und die Vorgehensweise zum Aktivieren und Konfigurieren der Regel zum Generieren von Sicherheitswarnungen mithilfe der Bedrohungsindikatoren an, die Sie in Azure-Sentinel importiert haben. In diesem Beispiel verwenden Sie die Regelvorlage **TI map IP entity to AzureActivity** (TI: IP-Entität zu Azure-Aktivität zuordnen). Mit dieser Regel werden alle Bedrohungsindikatoren vom Typ „IP-Adresse“ mit allen Ihren Azure-Aktivitätsereignissen abgeglichen. Bei einer Übereinstimmung werden eine **Warnung** und ein entsprechender **Incident** generiert, der dann von Ihrem Team für Sicherheitsvorgänge untersucht werden kann. Diese Analyseregel funktioniert nur dann ordnungsgemäß, wenn Sie einen der Datenconnectors **Threat Intelligence** (zum Importieren von Bedrohungsindikatoren) und den Datenconnector **Azure-Aktivität** (zum Importieren von Ereignissen auf Azure-Abonnementebene) aktiviert haben.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mithilfe der Datenconnectors **Threat Intelligence** und Azure-Aktivitätsdaten mithilfe des Datenconnectors **Azure-Aktivität** importiert haben.

1. Wählen Sie im Azure Sentinel-Menü im Abschnitt **Konfiguration** die Option **Analytics** aus.

1. Wählen Sie die Registerkarte **Regelvorlagen** aus, um die Liste der verfügbaren Analyseregelvorlagen anzuzeigen.

1. Navigieren Sie zu der Regel **TI map IP entity to AzureActivity**, und vergewissern Sie sich, dass Sie alle erforderlichen Datenquellen wie unten gezeigt verbunden haben.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Erforderliche Datenquellen":::

1. Wählen Sie diese Regel aus, und wählen Sie dann die Schaltfläche **Regel erstellen** aus. Dadurch wird ein Assistent zum Konfigurieren der Regel geöffnet. Füllen Sie die Einstellungen hier aus, und wählen Sie die Schaltfläche **Weiter: Regellogik festlegen** aus.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Erstellen der Analyseregel":::

1. Der Abschnitt für die Regellogik des Assistenten enthält Folgendes:
    - Die Abfrage, die in der Regel verwendet wird

    - Entitätszuordnungen, die Azure Sentinel mitteilen, wie Entitäten wie Konten, IP-Adressen und URLs identifiziert werden, damit **Incidents** und **Untersuchungen** verstehen, wie mit den Daten in den von dieser Regel generierten Sicherheitswarnungen verfahren werden soll

    - Der Zeitplan für die Ausführung dieser Regel

    - Die Anzahl der erforderlichen Abfrageergebnisse, bevor eine Sicherheitswarnung generiert wird

    Die Standardeinstellungen in der Vorlage lauten:
    - Ausführen einmal pro Stunde

    - Vergleichen aller Bedrohungsindikatoren vom Typ „IP-Adresse“ aus der Tabelle **ThreatIntelligenceIndicator** mit sämtlichen IP-Adressen, die in der letzten Stunde mit Ereignissen in der Tabelle **AzureActivity** gefunden wurden

    - Generieren einer Sicherheitswarnung, wenn die Abfrageergebnisse größer als 0 (null) sind (also Übereinstimmungen gefunden wurden)

Sie können die Standardeinstellungen beibehalten oder sie entsprechend Ihren Anforderungen anpassen. Wenn Sie fertig sind, wählen Sie die Schaltfläche **Weiter: Automatisierte Antwort** aus.

1. In diesem Schritt des Assistenten können Sie alle Automatisierungen konfigurieren, die Sie auslösen möchten, wenn eine Sicherheitswarnung durch diese Analyseregel generiert wird. Für die Automatisierung in Azure Sentinel werden **Playbooks** aus Azure Logic Apps verwendet. Weitere Informationen finden Sie in folgendem [Tutorial: Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](./tutorial-respond-threats-playbook.md). In diesem Beispiel wählen Sie einfach die Schaltfläche **Weiter: Überprüfen** aus, um fortzufahren.

1. In diesem letzten Schritt werden die Einstellungen in Ihrer Regel überprüft. Wenn Sie bereit sind, die Regel zu aktivieren, wählen Sie die Schaltfläche **Erstellen** aus.

Nachdem Sie Ihre Analyseregel aktiviert haben, finden Sie die aktivierte Regel auf der Registerkarte **Aktive Regeln** im Abschnitt **Analytics** von Azure Sentinel. Sie können die aktive Regel an dieser Stelle bearbeiten, aktivieren, deaktivieren, duplizieren oder löschen. Die neue Regel wird sofort nach der Aktivierung ausgeführt, und ab diesem Zeitpunkt nach dem festgelegten Zeitplan ausgeführt.

Gemäß den Standardeinstellungen wird jedes Mal, wenn die Regel nach dem Zeitplan ausgeführt wird und Ergebnisse gefunden werden, eine Sicherheitswarnung generiert. Sicherheitswarnungen in Azure Sentinel können im Abschnitt **Protokolle** von Azure Sentinel in der Tabelle **SecurityAlert** unter der Gruppe **Azure Sentinel** angezeigt werden.

In Azure Sentinel generieren Warnungen, die nach Analyseregeln generiert wurden, auch Sicherheitsincidents, die Sie in **Incidents** unter **Bedrohungsmanagement** im Azure Sentinel-Menü finden. Teams für Sicherheitsvorgänge können die Incidents selektieren und untersuchen, um angemessene Reaktionen zu ermitteln. Ausführliche Informationen hierzu finden Sie in diesem [Tutorial: Untersuchen von Incidents mit Azure Sentinel](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Arbeitsmappen für Erkenntnisse aus Threat Intelligence

Schließlich können Sie eine Azure Sentinel-Arbeitsmappe verwenden, um wichtige Informationen zu Ihrer Threat Intelligence in Azure Sentinel zu visualisieren, und Sie können Ihre Arbeitsmappen ganz einfach an Ihre geschäftlichen Anforderungen anpassen.
Im Folgenden erfahren Sie, wie Sie die in Azure Sentinel bereitgestellte Threat Intelligence-Arbeitsmappe finden. Außerdem wird erläutert, wie Sie Arbeitsmappen bearbeiten, um sie anzupassen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mit einem der Threat Intelligence-Datenconnectors importiert haben.

1. Wählen Sie im Azure Sentinel-Menü im Abschnitt **Bedrohungsmanagement** die Option **Arbeitsmappen** aus.

1. Navigieren Sie zur Arbeitsmappe **Threat Intelligence**, und vergewissern Sie sich, dass die Tabelle **ThreatIntelligenceIndicator** wie unten gezeigt Daten enthält.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Überprüfen der Daten":::
 
1. Wählen Sie die Schaltfläche **Speichern** aus, und wählen Sie dann einen Azure-Speicherort für die Arbeitsmappe aus. Dieser Schritt ist erforderlich, wenn Sie die Arbeitsmappe anpassen und Ihre Änderungen speichern möchten.

1. Wählen Sie nun die Schaltfläche **Gespeicherte Arbeitsmappe anzeigen** aus, um die Arbeitsmappe für die Anzeige und Bearbeitung zu öffnen.

1. Es sollten die von der Vorlage bereitgestellten Standarddiagramme angezeigt werden. Nehmen Sie nun einige Änderungen an einem der Diagramme vor. Wählen Sie oben auf der Seite die Schaltfläche **Bearbeiten** aus, um den Bearbeitungsmodus für die Arbeitsmappe zu aktivieren.

1. Fügen Sie ein neues Diagramm mit Bedrohungsindikatoren nach Bedrohungstyp hinzu. Scrollen Sie auf der Seite nach unten, und wählen Sie „Abfrage hinzufügen“ aus.

1. Fügen Sie den folgenden Text in das Textfeld **Protokollabfrage** des Log Analytics-Arbeitsbereichs ein:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Wählen Sie in der Dropdownliste **Visualisierung** den Eintrag **Balkendiagramm** aus.

1. Wählen Sie die Schaltfläche **Bearbeitung abgeschlossen** aus. Sie haben ein neues Diagramm für Ihre Arbeitsmappe erstellt.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Balkendiagramm":::

Mit Arbeitsmappen verfügen Sie über leistungsstarke interaktive Dashboards, über die Sie Einblicke in alle Aspekte von Azure Sentinel erhalten. Es gibt zahlreiche Anwendungsmöglichkeiten für Arbeitsmappen, und die bereitgestellten Vorlagen sind ein guter Ausgangspunkt. Sie können diese Vorlagen aber natürlich genauer untersuchen und anpassen oder neue Dashboards erstellen, auf denen viele verschiedene Datenquellen kombiniert werden, um so Ihre Daten auf spezifische Weise zu visualisieren. Da Azure Sentinel-Arbeitsmappen auf Azure Monitor-Arbeitsmappen basieren, sind eine umfassende Dokumentation und viele weitere Vorlagen verfügbar. Einen hervorragenden Einstieg bietet der Artikel zum [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../azure-monitor/platform/workbooks-overview.md). 

Es gibt auch eine riesige Community für [Azure Monitor-Arbeitsmappen auf GitHub](https://github.com/microsoft/Application-Insights-Workbooks), in der Sie weitere Vorlagen herunterladen und eigene Vorlagen veröffentlichen können.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie etwas über die Threat Intelligence-Funktionen von Azure Sentinel und das neue Blatt „Threat Intelligence“ erfahren. Eine praktische Anleitung zur Verwendung der Threat Intelligence-Funktionen von Azure Sentinel finden Sie in den folgenden Artikeln:

- [Verknüpfen von Threat Intelligence-Daten mit Azure Sentinel](./connect-threat-intelligence.md)
- Erstellen [integrierter](./tutorial-detect-threats-built-in.md) oder [benutzerdefinierter](./tutorial-detect-threats-custom.md) Warnungen und [Untersuchen](./tutorial-investigate-cases.md) von Incidents in Azure Sentinel