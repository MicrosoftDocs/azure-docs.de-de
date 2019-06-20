---
title: Verwenden des Azure Active Directory-Power BI-Inhaltspakets | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie das Azure Active Directory-Power BI-Inhaltspaket verwenden.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934562147fedcc81b16fd1ad2534af5662ef4b78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437677"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Verwenden des Power BI-Inhaltspakets für Azure Active Directory

|  |
|--|
|Das Azure AD-Power-BI-Inhaltspaket verwendet derzeit die Azure AD Graph-APIs zum Abrufen von Daten des Azure AD-Mandanten. Daher ergibt sich möglicherweise ein Unterschied zwischen den Daten im Inhaltspaket und den Daten, die mit den [Microsoft Graph-APIs für die Berichterstellung](concept-reporting-api.md) abgerufen wurden. |
|  |

Das Power BI-Inhaltspaket für Azure Active Directory (Azure AD) enthält vorkonfigurierte Berichte, mit denen Sie verstehen können, wie Ihre Benutzer Azure AD-Features einführen und verwenden. Dadurch erhalten Sie über die umfassende Visualisierungsumgebung in Power BI Einblicke in alle Aktivitäten in Ihrem Verzeichnis. Sie können auch Ihr eigenes Dashboard erstellen und es für alle anderen Benutzer Ihrer Organisation freigeben. 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Azure AD Premium-Lizenz (P1/P2), um das Inhaltspaket verwenden zu können. Unter [Erste Schritte mit Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) erfahren Sie, wie Sie ein Upgrade für Ihre Azure Active Directory-Edition durchführen.

## <a name="install-the-content-pack"></a>Installieren des Inhaltspakets

Im [Schnellstart](quickstart-install-power-bi-content-pack.md) finden Sie Informationen zum Installieren des Azure AD-Power BI-Inhaltspakets.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Berichte, die in dieser Version des Inhaltspakets für Azure AD-Protokolle enthalten sind

Die folgenden Berichte sind im Power-BI-Inhaltspaket für Azure AD enthalten. Die Berichte enthalten Daten aus den **letzten 30 Tagen**.

**Bericht zur App-Nutzung und zu Trends**:  Dieser Bericht bietet Ihnen einen Erkenntnisse zu den Anwendungen, die in Ihrer Organisation verwendet werden. Sie können eine Liste der am häufigsten verwendeten Anwendungen abrufen, oder Sie können herausfinden, wie eine Anwendung verwendet wird, die vor kurzem in Ihrer Organisation eingeführt wurde. Dadurch können Sie die Nutzung im Laufe der Zeit nachverfolgen und verbessern.

**Anmeldungen nach Ort und Benutzer**: Dieser Bericht enthält Daten zu allen Anmeldungen, die mit einer Azure-Identität ausgeführt wurden. Mit diesem Bericht können Sie einzelne Anmeldungen eingehender untersuchen und beispielsweise folgende Fragen beantworten:

- An welchem Ort hat sich der Benutzer angemeldet?
- Welcher Benutzer hat sich am häufigsten angemeldet, und von wo aus erfolgen die Anmeldungen? 
- War die Anmeldung erfolgreich?  
 
Sie können die Ergebnisse auch filtern, indem Sie ein bestimmtes Datum oder einen Ort auswählen.

**Eindeutige Benutzer pro App**:  Dieser Bericht bietet einen Überblick über alle eindeutigen Benutzer, die eine bestimmte App verwenden. Er enthält nur Benutzer, die sich *erfolgreich* an einer Anwendung angemeldet haben.

**Geräteanmeldungen**: Dieser Bericht unterstützt Sie dabei, die verschiedenen in Ihrer Organisation verwendeten Geräteprofile zu verstehen und basierend auf der Nutzung Geräterichtlinien festzulegen. Er enthält Daten zum Typ des Betriebssystems und der Browser, die für die Anmeldung bei Anwendungen verwendet wurden, sowie ausführliche Informationen zu den Benutzern, einschließlich der folgenden:

- Benutzername
- IP-Adresse
- Location 
- Anmeldestatus 

**SSPR-Trichter**: Mit diesem Bericht können Sie nachvollziehen, wie das SSPR-Tool in Ihrer Organisation verwendet wird. Sie können anzeigen, wie viele Kennwortzurücksetzungen mit dem SSPR-Tool durchgeführt wurden und wie viele davon erfolgreich waren. Zudem können Sie Fehler bei der Kennwortzurücksetzung genauer analysieren, damit Sie verstehen, warum bestimmte Fehler aufgetreten sind. 

## <a name="customize-azure-ad-activity-content-pack"></a>Anpassen des Inhaltspakets für Azure AD-Aktivitäten

**Visualisierung ändern**:  Sie können die Visualisierung eines Berichts ändern, indem Sie auf **Bericht bearbeiten** klicken und die gewünschte Visualisierung auswählen.
 
![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/10.png) 

**Zusätzliche Felder einfügen**:  Sie können dem Bericht ein Feld hinzufügen oder ein Feld daraus entfernen, indem Sie die Visualisierung auswählen, für die Sie das Feld hinzufügen oder entfernen möchten. Beispielsweise können Sie das Feld „Anmeldestatus“ zur Tabellenansicht hinzufügen, wie unten dargestellt. 
 
![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/11.png) 

**Visualisierungen an das Dashboard anheften**:  Sie können Ihr Dashboard anpassen, indem Sie Ihre eigenen Visualisierungen in den Bericht einfügen und an das Dashboard anheften. 

![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/13.png) 
 
**Freigeben des Dashboards**: Sie können das Dashboard auch für die Benutzer in Ihrer Organisation freigeben. Nachdem Sie den Bericht freigegeben haben, können Benutzer die von Ihnen ausgewählten Felder im Bericht sehen.
 
![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Planen einer täglichen Aktualisierung Ihres Power BI-Berichts

Navigieren Sie zum Planen einer täglichen Aktualisierung Ihres Power BI-Berichts zu **Datasets** > **Einstellungen** > **Aktualisierung planen**, und legen Sie wie unten gezeigt Folgendes fest.
 
![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Aktualisieren auf eine neuere Version des Inhaltspakets

Gehen Sie wie folgt vor, wenn Sie Ihr Inhaltspaket auf eine neuere Version aktualisieren möchten:

- Laden Sie das neue Inhaltspaket herunter, und richten Sie es mithilfe der Anleitung in diesem Artikel ein.

- Navigieren Sie nach dem Einrichten zu **Datenquelle** > **Einstellungen** > **Anmeldeinformationen für die Datenquelle**, und geben Sie Ihre Anmeldeinformationen erneut ein.

    ![Azure Active Directory-Power BI-Inhaltspaket](./media/howto-power-bi-content-pack/16.png) 

Nachdem Sie überprüft haben, ob die neue Version des Inhaltspakets wie erwartet funktioniert, können Sie die alte Version bei Bedarf entfernen, indem Sie die zugrunde liegenden Berichte und Datasets löschen, die diesem Inhaltspaket zugeordnet sind.

## <a name="troubleshoot-content-pack-errors"></a>Problembehandlung bei Fehlern mit dem Inhaltspaket

Bei der Arbeit mit dem Inhaltspaket können eventuell folgende Fehler auftreten: 

- [Fehler beim Aktualisieren](#refresh-failed) 
- [Fehler beim Aktualisieren der Anmeldeinformationen für die Datenquelle](#failed-to-update-data-source-credentials) 
- [Der Import von Daten dauert zu lange.](#data-import-is-too-slow) 

Allgemeine Hilfe zu Power BI finden Sie in diesen [Hilfeartikeln](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Fehler beim Aktualisieren 
 
**So werden Sie auf diesen Fehler aufmerksam**: E-Mail von Power BI oder Fehlerstatus im Aktualisierungsverlauf. 


| Ursache | So behebt man den Fehler |
| ---   | ---        |
| Aktualisierungsfehler können auftreten, wenn die Anmeldeinformationen der Benutzer, die eine Verbindung mit dem Inhaltspaket herstellen, zurückgesetzt, aber nicht in den Verbindungseinstellungen des Inhaltspakets aktualisiert wurden. | Suchen Sie in Power BI das Dataset, das dem Dashboard für Azure AD-Aktivitätsprotokolle (**Azure Active Directory-Aktivitätsprotokolle**) entspricht. Wählen Sie die geplante Aktualisierung aus, und geben Sie dann Ihre Azure AD-Anmeldeinformationen ein. |
| Bei einer Aktualisierung können aufgrund von Datenproblemen im zugrunde liegenden Inhaltspaket Fehler auftreten. | [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Fehler beim Aktualisieren der Anmeldeinformationen für die Datenquelle 
 
**So werden Sie auf diesen Fehler aufmerksam**: In Power BI beim Herstellen einer Verbindung mit dem Inhaltspaket des Azure AD-Aktivitätsprotokolls. 

| Ursache | So behebt man den Fehler |
| ---   | ---        |
| Der Benutzer, der eine Verbindung herstellt, ist kein globaler Administrator, kein Sicherheitsleseberechtigter bzw. kein Sicherheitsadministrator. | Verwenden Sie für den Zugriff auf die Inhaltspakete ein Konto, dem entweder die Rolle eines globalen Administrators, eines Sicherheitsleseberechtigten oder eines Sicherheitsadministrators zugewiesen ist. |
| Ihr Mandant ist kein Premium-Mandant oder weist keinen Benutzer mit einer Premium-Lizenzdatei auf. | [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Der Datenimport ist zu langsam 
 
**So werden Sie auf diesen Fehler aufmerksam**: Nachdem Sie in Power BI eine Verbindung mit Ihrem Inhaltspaket hergestellt haben, wird der Datenimportvorgang gestartet, und Ihr Dashboard wird für Azure AD-Aktivitätsprotokolle vorbereitet. Es wird folgende Nachricht angezeigt: **Daten werden importiert** wird ohne weitere Fortschritte angezeigt.  

| Ursache | So behebt man den Fehler |
| ---   | ---        |
| Je nach Größe Ihres Mandanten kann dieser Schritt bis zu 30 Minuten dauern. | Wenn die Meldung innerhalb einer Stunde nicht ausgeblendet und Ihr Dashboard nicht angezeigt wird, [öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Nächste Schritte

* [Installieren des Power BI-Inhaltspakets](quickstart-install-power-bi-content-pack.md)
* [Was sind Azure AD-Berichte?](overview-reports.md)
