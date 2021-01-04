---
title: Herstellen einer Verbindung von Office 365-Protokollen mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Office 365-Protokollconnector Informationen zu laufenden Benutzer- und Administratoraktivitäten in Exchange, Teams und SharePoint (einschließlich OneDrive) importieren.
services: sentinel
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 4d3e3615ede7406a3b581171ae759ec0ec53f13e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655369"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Herstellen einer Verbindung von Office 365-Protokollen mit Azure Sentinel

Der [Office 365](/office/)-Protokollconnector liefert Azure Sentinel Informationen zu laufenden Benutzer- und Administratoraktivitäten in **Exchange** und **SharePoint** (einschließlich **OneDrive**) und jetzt auch **Teams**. Diese Informationen umfassen Details zu Aktionen wie z. B. Dateidownloads, gesendeten Zugriffsanforderungen, Änderungen an Gruppenereignissen und Postfachvorgängen, Teams-Ereignissen (etwa Chat-, Team-, Mitglieds- oder Kanalereignissen) sowie ausführliche Informationen zu dem Benutzer, der die Aktionen durchgeführt hat. Das Verbinden von Office 365-Protokollen mit Azure Sentinel ermöglicht Ihnen, diese Daten in Ihren Arbeitsmappen anzuzeigen und zu analysieren, sie zum Erstellen benutzerdefinierter Warnungen abzufragen und zu integrieren, um den Untersuchungsprozess zu verbessern und Ihnen einen besseren Einblick in Ihre Office 365-Sicherheit zu geben.

> [!IMPORTANT]
> Die **Erweiterung für Microsoft Teams-Protokolle** des Office 365-Protokollconnectors befindet sich derzeit in der Public Preview.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen globaler Administrator oder Sicherheitsadministrator für Ihren Mandanten sein.

- Ihre Office 365-Bereitstellung muss sich auf demselben Mandanten wie Ihr Azure Sentinel-Arbeitsbereich befinden.

> [!IMPORTANT]
> - Damit der Connector über die Verwaltungsaktivitäts-API von Office 365 auf Daten zugreifen kann, muss für Ihre Office 365-Bereitstellung die **einheitliche Überwachungsprotokollierung** aktiviert sein. Abhängig vom Typ Ihrer Lizenz für Office 365/Microsoft 365 ist sie möglicherweise standardmäßig aktiviert. Informieren Sie sich im [Security & Compliance Center](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) über den Status der einheitlichen Überwachungsprotokollierung gemäß Ihrem Lizenztyp.
> - Sie können den aktuellen Status der einheitlichen Überwachungsprotokollierung von Office 365 auch manuell aktivieren, deaktivieren und überprüfen. Anweisungen finden Sie unter [Aktivieren oder Deaktivieren der Office 365-Überwachungsprotokollsuche](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Weitere Informationen finden Sie unter [Office 365-Verwaltungsaktivitäts-API – Referenz](/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Wie bereits oben und auf der Connectorseite unter **Datentypen** erwähnt, unterstützt der Azure Sentinel-Office 365-Connector die Erfassung von Überwachungsprotokollen derzeit nur von Microsoft Exchange und SharePoint (einschließlich OneDrive) **und jetzt auch von Teams**. Es gibt jedoch einige externe Lösungen, mit denen Sie [andere Office-Daten](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) in Azure Sentinel einbinden können. 

## <a name="enable-the-office-365-log-connector"></a>Aktivieren des Office 365-Protokollconnectors

### <a name="instructions-tab"></a>Registerkarte „Anweisungen“

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie im **Katalog für Datenconnectors** **Office 365** und dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Markieren Sie im Abschnitt mit der Bezeichnung **Konfiguration** die Kontrollkästchen der Office 365-Aktivitätsprotokolle, die Sie mit Azure Sentinel verbinden möchten, und klicken Sie auf **Änderungen** anwenden. 

   > [!NOTE]
   > Wenn Sie bereits mehrere Mandanten mit einer älteren Version des Office 365-Connectors mit Azure Sentinel, die dies unterstützt, verbunden haben, können Sie anzeigen und ändern, welche Protokolle Sie von den einzelnen Mandanten erfassen. Sie können keine weiteren Mandanten hinzufügen, aber Sie können zuvor hinzugefügte Mandanten entfernen.

### <a name="next-steps-tab"></a>Registerkarte „Nächste Schritte“

- Sehen Sie sich die empfohlenen Arbeitsmappen, Abfragebeispiele und Analyseregelvorlagen an, die mit dem **Office 365**-Datenconnector gebündelt sind, um Erkenntnisse zu Ihren SharePoint-, OneDrive-, Exchange- und Teams-Protokolldaten zu erhalten.

- Um Protokolldaten von Office 365 in **Protokollen** abzufragen, geben Sie in der ersten Zeile des Abfragefensters `OfficeActivity` ein.
   - Wenn Sie die Abfrage nach einem bestimmten Protokolltyp filtern möchten, geben Sie in der zweiten Zeile der Abfrage `| where OfficeWorkload == "<logtype>"` ein, wobei *\<logtype\>* eine der Angaben `SharePoint`, `OneDrive`, `Exchange` oder `MicrosoftTeams` ist.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Office 365 mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit dem Erkennen von Bedrohungen mit Azure Sentinel mithilfe von [integrierten](tutorial-detect-threats-built-in.md) oder [benutzerdefinierten](tutorial-detect-threats-custom.md) Regeln.