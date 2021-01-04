---
title: Azure Monitor-Arbeitsmappen für Berichte | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte verwenden.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 76dc7693b1773038d610f9ed42eddad2c81e3eaf
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126633"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte

> [!IMPORTANT]
> Um die zugrunde liegenden Abfragen in dieser Arbeitsmappe zu optimieren, klicken Sie auf „Bearbeiten“, klicken Sie auf das Symbol „Einstellungen“, und wählen Sie den Arbeitsbereich aus, in dem diese Abfragen ausgeführt werden sollen. Für Arbeitsmappen werden standardmäßig alle Arbeitsbereiche ausgewählt, an die Sie die Azure AD-Protokolle weiterleiten. 

Möchten Sie:

- Die Auswirkung Ihrer [Richtlinien für bedingten Zugriff](../conditional-access/overview.md) auf die Anmeldung Ihrer Benutzer verstehen?

- Anmeldefehler beheben, um eine bessere Übersicht über die Anmeldeintegrität in Ihrer Organisation zu erhalten, sowie Probleme schnell beheben?

- Wissen, welche Benutzer sich über Legacyauthentifizierungen bei Ihrer Umgebung anmelden? (Durch [Blockieren der Legacyauthentifizierung](../conditional-access/block-legacy-authentication.md) können Sie den Schutz Ihres Mandanten verbessern.)

- Müssen Sie die Auswirkungen der Richtlinien für bedingten Zugriff im Mandanten verstehen?

- Möchten Sie Anmeldeprotokollabfragen, die Arbeitsmappenberichte zur Anzahl der Benutzer, denen der Zugriff gewährt oder verweigert wurde, sowie zur Anzahl der Benutzer, die beim Zugriff auf Ressourcen die Richtlinien für bedingten Zugriff umgangen haben, überprüfen können?

- Möchten Sie mehr über die Arbeitsmappendetails pro Bedingung wissen, sodass die Auswirkung einer Richtlinie pro Bedingung kontextualisiert werden kann, einschließlich Geräteplattform, Gerätestatus, Client-App, Anmelderisiko, Standort und Anwendung?

- Gewinnen Sie tiefere Einblicke in Anmeldeprotokollabfragen, die Arbeitsmappenberichte zur Anzahl der Benutzer, denen der Zugriff gewährt oder verweigert wurde, sowie zur Anzahl der Benutzer, die beim Zugriff auf Ressourcen die Richtlinien für bedingten Zugriff umgangen haben.

- Damit Sie diese Fragen beantworten können, stellt Azure Active Directory Arbeitsmappen für die Überwachung bereit. [Azure Monitor-Arbeitsmappen](../../azure-monitor/platform/workbooks-overview.md) kombinieren Text, Analyseabfragen, Metriken und Parameter zu umfassenden interaktiven Berichten.



Dieser Artikel:

- Setzt voraus, dass Sie mit dem [Erstellen interaktiver Berichte mit Monitor-Arbeitsmappen](../../azure-monitor/platform/workbooks-overview.md) vertraut sind.

- Erläutert die Verwendung von Monitor-Arbeitsmappen, um die Auswirkungen Ihrer Richtlinien für bedingten Zugriff verstehen, Anmeldefehler beheben und Legacyauthentifizierungen identifizieren zu können.
 


## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden von Monitor-Arbeitsmappen benötigen Sie:

- Einen Azure Active Directory-Mandanten mit einer Premium-Lizenz (P1 oder P2). Wie Sie eine Premium-Lizenz erhalten, erfahren Sie [hier](../fundamentals/active-directory-get-started-premium.md).

- Einen [Log Analytics-Arbeitsbereich](../../azure-monitor/learn/quick-create-workspace.md).

- [Zugriff](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) auf den Log Analytics-Arbeitsbereich
- Folgende Rollen in Azure Active Directory (beim Zugriff auf Log Analytics über das Azure Active Directory-Portal)
    - Sicherheitsadministrator
    - Sicherheitsleseberechtigter
    - Berichtsleseberechtigter
    - Globaler Administrator

## <a name="roles"></a>Rollen
Sie müssen Mitglied einer der folgenden Rollen sein und über [Zugriff auf den zugrunde liegenden Log Analytics-Arbeitsbereich](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) verfügen, um die Arbeitsmappen verwalten zu können:
-   Globaler Administrator
-   Sicherheitsadministrator
-   Sicherheitsleseberechtigter
-   Berichtsleseberechtigter
-   Anwendungsadministrator

## <a name="workbook-access"></a>Arbeitsmappenzugriff 

So greifen Sie auf Arbeitsmappen zu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu **Azure Active Directory** > **Überwachung** > **Arbeitsmappen**. 

1. Wählen Sie einen Bericht oder eine Vorlage aus, oder wählen Sie auf der Symbolleiste **Öffnen** aus. 

![Suchen der Azure Monitor-Arbeitsmappen in Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analyse der Anmeldungen

Wählen Sie für den Zugriff auf die Arbeitsmappe zur Analyse der Anmeldungen im Abschnitt **Nutzung** die Option **Anmeldungen** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für Anmeldungen angezeigt:

- Alle Anmeldungen

- Erfolg

- Benutzeraktion ausstehend

- Fehler

Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Analyse der Anmeldungen](./media/howto-use-azure-monitor-workbooks/43.png)


Für jeden Trend erhalten Sie eine Aufschlüsselung nach folgenden Kategorien:

- Standort

    ![Anmeldungen nach Ort](./media/howto-use-azure-monitor-workbooks/45.png)

- Sicherungsmedium

    ![Anmeldungen nach Gerät](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Anmeldungen mit Legacyauthentifizierung 


Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen mit [Legacyauthentifizierung](../conditional-access/block-legacy-authentication.md) im Abschnitt **Nutzung** die Option **Anmeldungen mit Legacyauthentifizierung** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für Anmeldungen angezeigt:

- Alle Anmeldungen

- Erfolg


Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

- Protokolle

![Anmeldungen mit Legacyauthentifizierung](./media/howto-use-azure-monitor-workbooks/47.png)


Für jeden Trend erhalten Sie eine Aufschlüsselung nach App und Protokoll.

![Anmeldungen mit Legacyauthentifizierung nach App und Protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Anmeldungen mithilfe des bedingten Zugriffs 


Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen nach [Richtlinien für bedingten Zugriff](../conditional-access/overview.md) im Abschnitt **Bedingter Zugriff** die Option **Anmeldungen mithilfe des bedingten Zugriffs** aus. 

Mit dieser Arbeitsmappe werden die Trends für deaktivierte Anmeldungen angezeigt. Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Anmeldungen mithilfe des bedingten Zugriffs](./media/howto-use-azure-monitor-workbooks/49.png)


Für deaktivierte Anmeldungen erhalten Sie eine Aufschlüsselung nach dem Status des bedingten Zugriffs.

![Screenshot: Status des bedingten Zugriffs und kürzlich erfolgte Anmeldungen](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Erkenntnisse zum bedingten Zugriff

### <a name="overview"></a>Übersicht

Arbeitsmappen enthalten Anmeldeprotokollabfragen, mit denen IT-Administratoren die Auswirkung von Richtlinien für bedingten Zugriff im Mandanten überwachen können. Sie können berichten, wie vielen Benutzern der Zugriff gewährt oder verweigert wurde. Die Arbeitsmappe enthält Informationen darüber, wie viele Benutzer die Richtlinien für bedingten Zugriff umgangen haben, auf Grundlage der Attribute der Benutzer zum Zeitpunkt der Anmeldung. Sie enthält Informationen pro Bedingung, sodass die Auswirkung einer Richtlinie pro Bedingung, einschließlich Geräteplattform, Gerätestatus, Client-App, Anmelderisiko, Standort und Anwendung, kontextualisiert werden kann.

### <a name="instructions"></a>Anleitungen 
Um auf die Arbeitsmappe für Erkenntnisse zum bedingten Zugriff zuzugreifen, wählen Sie im Abschnitt „Bedingter Zugriff“ die Arbeitsmappe **Erkenntnisse zum bedingten Zugriff** aus. In dieser Arbeitsmappe werden die erwarteten Auswirkungen der einzelnen Richtlinien für bedingten Zugriff im Mandanten angezeigt. Wählen Sie in der Dropdownliste eine oder mehrere Richtlinien für bedingten Zugriff aus, und schränken Sie den Bereich der Arbeitsmappe ein, indem Sie die folgenden Filter anwenden: 

- **Zeitbereich**

- **Benutzer**

- **Apps**

- **Datenansicht**

![Screenshot: Bereich „Bedingter Zugriff“, in dem Sie eine Richtlinie für bedingten Zugriff auswählen können](./media/howto-use-azure-monitor-workbooks/access-insights.png)


In „Zusammenfassung der Auswirkungen“ wird die Anzahl der Benutzer oder Anmeldungen angezeigt, für die die ausgewählten Richtlinien ein bestimmtes Ergebnis hatten. „Gesamt“ gibt die Anzahl der Benutzer oder Anmeldungen an, für die die ausgewählten Richtlinien im ausgewählten Zeitbereich ausgewertet wurden. Klicken Sie auf eine Kachel, um die Daten in der Arbeitsmappe nach dem entsprechenden Ergebnistyp zu filtern. 

![Screenshot: Kacheln zum Filtern von Ergebnissen z. B. nach Gesamtzahl oder nach erfolgreichen und fehlerhaften Anmeldungen](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

In dieser Arbeitsmappe werden auch die Auswirkungen der ausgewählten Richtlinien nach sechs einzelnen Bedingungen untergliedert angezeigt: 
- **Gerätestatus**
- **Geräteplattform**
- **Client-Apps**
- **Anmelderisiko**
- **Location**
- **Anwendungen**

![Screenshot: Details nach Verwendung des Filters für die Gesamtzahl der Anmeldungen](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Sie können auch einzelne Anmeldungen untersuchen, die nach den in der Arbeitsmappe ausgewählten Parametern gefiltert sind. Suchen Sie nach einzelnen Benutzern, sortiert nach Anmeldehäufigkeit, und zeigen Sie die entsprechenden Anmeldeereignisse an. 

![Screenshot: Individuelle Anmeldungen, die Sie überprüfen können](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Anmeldungen nach Steuerelementen zur Rechteerteilung

Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen nach [Steuerelementen zur Rechteerteilung](../conditional-access/controls.md) im Abschnitt **Bedingter Zugriff** die Option **Anmeldungen nach Steuerelementen zur Rechteerteilung** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für deaktivierte Anmeldungen angezeigt:

- Anfordern von MFA
 
- Vorschreiben der Verwendung von Nutzungsbedingungen

- Vorschreiben von Datenschutzbestimmungen

- Andere


Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Anmeldungen nach Steuerelementen zur Rechteerteilung](./media/howto-use-azure-monitor-workbooks/50.png)


Für jeden Trend erhalten Sie eine Aufschlüsselung nach App und Protokoll.

![Aufschlüsselung der aktuellen Anmeldungen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse der Anmeldungsfehler

Verwenden Sie die Arbeitsmappe zur **Analyse der Anmeldungsfehler** , um Fehler in folgenden Bereichen zu beheben:

- Anmeldungen
- Richtlinien für bedingten Zugriff
- Legacyauthentifizierung 


Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen nach Daten zum bedingten Zugriff im Abschnitt **Problembehandlung** die Option **Anmeldungen mit Legacyauthentifizierung** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für Anmeldungen angezeigt:

- Alle Anmeldungen

- Erfolg

- Ausstehende Aktion

- Fehler


Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Problembehandlung bei Anmeldungen](./media/howto-use-azure-monitor-workbooks/52.png)


Damit Sie Probleme mit Anmeldungen beheben können, bietet Azure Monitor Ihnen eine Aufschlüsselung nach den folgenden Kategorien:

- Häufigste Fehler

    ![Zusammenfassung der häufigsten Fehler](./media/howto-use-azure-monitor-workbooks/53.png)

- Anmeldungen mit Benutzeraktionen

    ![Zusammenfassung der Anmeldungen mit Benutzeraktionen](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Nächste Schritte

[Erstellen interaktiver Berichte mit Monitor-Arbeitsmappen](../../azure-monitor/platform/workbooks-overview.md).