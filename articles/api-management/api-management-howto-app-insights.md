---
title: Integrieren von Azure API Management mit Azure Application Insights
titleSuffix: Azure API Management
description: Informationen zum Protokollieren und Anzeigen von Ereignissen aus Azure API Management in Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: adb221c12af436135b1e740fdef7c5c0a0a7f0cb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096035"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Vorgehensweise beim Integrieren von Azure API Management in Azure Application Insights

Azure API Management ermöglicht die mühelose Integration in Azure Application Insights, einen erweiterbaren Dienst für Webentwickler, die Apps auf mehreren Plattformen erstellen und verwalten. Diese Anleitung führt Sie durch jeden Schritt einer solchen Integration und beschreibt Strategien zur Reduzierung der Auswirkungen auf die Leistung Ihrer API Management-Dienstinstanz.

## <a name="prerequisites"></a>Voraussetzungen

Um dieser Anleitung folgen zu können, benötigen Sie eine Azure API Management-Instanz. Wenn Sie keine besitzen, führen Sie zuerst das [Tutorial](get-started-create-service-instance.md) durch.

## <a name="create-an-azure-application-insights-instance"></a>Erstellen einer Azure Application Insights-Instanz

Bevor Sie Azure Application Insights verwenden können, müssen Sie zunächst eine Instanz des Diensts erstellen.

1. Öffnen Sie das **Azure-Portal** , und navigieren Sie zu **Application Insights**.  
    ![Screenshot der Navigation zu Application Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klicken Sie auf **+ Hinzufügen**.  
    ![Application Insights erstellen](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Füllen Sie das Formular aus. Wählen Sie **Allgemein** als **Anwendungstyp** aus.
4. Klicken Sie auf **Erstellen**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Erstellen einer Verbindung zwischen Azure Application Insights und der Azure API Management-Dienstinstanz

1. Navigieren Sie im **Azure-Portal** zu Ihrer **Azure API Management-Dienstinstanz**.
2. Wählen Sie **Application Insights** im Menü auf der linken Seite aus.
3. Klicken Sie auf **+ Hinzufügen**.  
    ![Screenshot der Stelle zum Hinzufügen einer neuen Verbindung](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Wählen Sie die zuvor erstellte **Application Insights** -Instanz aus, und geben Sie eine kurze Beschreibung.
5. Klicken Sie auf **Erstellen**.
6. Sie haben gerade eine Azure Application Insights-Protokollierung mit einem Instrumentierungsschlüssel erstellt. Sie sollte jetzt in der Liste angezeigt werden.  
    ![Screenshot der Stelle zum Anzeigen der neu erstellten Azure Application Insights-Protokollierung mit dem Instrumentierungsschlüssel](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Im Hintergrund wird eine [Protokollierungsentität](/rest/api/apimanagement/2019-12-01/logger/createorupdate) in der API Management-Instanz erstellt, die den Instrumentierungsschlüssel der Application Insights-Instanz enthält.

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivieren der Application Insights-Protokollierung für Ihre API

1. Navigieren Sie im **Azure-Portal** zu Ihrer **Azure API Management-Dienstinstanz**.
2. Wählen Sie im Menü links **APIs** aus.
3. Klicken Sie auf Ihre API, in diesem Fall **Demo Conference API**.
4. Wechseln Sie zur Registerkarte **Einstellungen** in der oberen Leiste.
5. Scrollen Sie nach unten zum Abschnitt **Diagnoseprotokolle**.  
    ![Application Insights-Protokollierung](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Aktivieren Sie das Kontrollkästchen **Aktivieren**.
7. Wählen Sie eine angefügte Protokollierung in der Dropdownliste **Ziel** aus.
8. Geben Sie **100** als **Sampling (%)** ein, und aktivieren Sie das Kontrollkästchen **Fehler immer protokollieren**.
9. Klicken Sie auf **Speichern**.

> [!WARNING]
> Überschreiben des Standardwerts **0** im Feld **Erste Bytes des Texts** kann die Leistung Ihrer APIs erheblich beeinträchtigen.

> [!NOTE]
> Im Hintergrund wird eine [Diagnoseentität](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) namens „applicationinsights“ auf API-Ebene erstellt.

| Einstellungsname                        | Werttyp                        | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivieren                              | boolean                           | Gibt an, ob die Protokollierung dieser API aktiviert ist.                                                                                                                                                                                                                                                                                                |
| Destination                         | Azure Application Insights-Protokollierung | Gibt zu verwendende Azure Application Insights-Protokollierung an                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | Decimal                           | Werte von 0 bis 100 (Prozent). <br/> Gibt an, welcher Prozentsatz der Anforderungen in Azure Application Insights protokolliert wird. 0% Sampling bedeutet, dass 0 (null) Anforderungen protokolliert werden, während ein Sampling von 100% bedeutet, dass alle Anforderungen protokolliert werden. <br/> Diese Einstellung wird verwendet, um Leistungseinbußen durch Protokollierungsanforderungen an Azure Application Insights (siehe Abschnitt weiter unten) zu verringern. |
| Fehler immer protokollieren                   | boolean                           | Wenn diese Einstellung ausgewählt ist, werden alle Fehler unabhängig von der Einstellung **Sampling** in Azure Application Insights protokolliert.                                                                                                                                                                                                                  |
| Grundlegende Optionen: Header              | list                              | Gibt die Header an, die in Azure Application Insights für Anforderungen und Antworten protokolliert werden.  Standard: Es werden keine Header protokolliert.                                                                                                                                                                                                             |
| Grundlegende Optionen: Erste Bytes des Texts  | integer                           | Gibt an, wie viele erste Bytes des Texts in Azure Application Insights für Anforderungen und Antworten protokolliert werden.  Standard: Text wird nicht protokolliert.                                                                                                                                                                                                    |
| Erweiterte Optionen: Ausführlichkeit         |                                   | Gibt den Ausführlichkeitsgrad an. Nur benutzerdefinierte Ablaufverfolgungen mit höherem Schweregrad werden protokolliert. Standardwert: Information.                                                                                                                                                                                                                               |
| Erweiterte Optionen: Front-End-Anforderung  |                                   | Gibt an, ob und wie *Front-End-Anforderungen* in Azure Application Insights protokolliert werden. Eine *Front-End-Anforderung* ist eine an den Azure API Management-Dienst gerichtete Anforderung.                                                                                                                                                                        |
| Erweiterte Optionen: Front-End-Antwort |                                   | Gibt an, ob und wie *Front-End-Antworten* in Azure Application Insights protokolliert werden. *Front-End-Antwort* ist eine vom Azure API Management-Dienst ausgehende Antwort.                                                                                                                                                                   |
| Erweiterte Optionen: Back-End-Anforderung   |                                   | Gibt an, ob und wie *Back-End-Anforderungen* in Azure Application Insights protokolliert werden. Eine *Back-End-Anforderung* ist eine vom Azure API Management-Dienst ausgehende Anforderung.                                                                                                                                                                        |
| Erweiterte Optionen: Back-End-Antwort  |                                   | Gibt an, ob und wie *Back-End-Antworten* in Azure Application Insights protokolliert werden. *Back-End-Antwort* ist eine an den Azure API Management-Dienst gerichtete Antwort.                                                                                                                                                                       |

> [!NOTE]
> Sie können auf verschiedenen Ebenen Protokollierungen angeben – eine Protokollierung für eine einzelne API oder eine Protokollierung für alle APIs.
>  
> Wenn Sie beide angeben:
> + Wenn es sich um unterschiedliche Protokollierungen handelt, werden beide verwendet (Multiplexprotokolle),
> + wenn die Protokollierungen identisch sind, jedoch unterschiedliche Einstellungen haben, überschreibt die für die einzelne API (mit der feineren Granularität) die für alle APIs.

## <a name="what-data-is-added-to-azure-application-insights"></a>Welche Daten Azure Application Insights hinzugefügt werden

Azure Application Insights empfängt:

+ Ein *Anforderungstelemetrieereignis* für jede eingehende Anforderung ( *Front-End-Anforderung* , *Front-End-Antwort* ),
+ ein *Abhängigkeitstelemetrieereignis* für jede an einen Back-End-Dienst weitergeleitete Anforderung ( *Back-End-Anforderung* , *Back-End-Antwort* ),
+ ein *Ausnahmetelemetrieereignis* für jede fehlerhafte Anforderung.

Für eine fehlerhafte Anforderung gilt Folgendes:

+ Sie ist aufgrund einer geschlossenen Clientverbindung fehlerhaft, oder
+ sie wurde im *bei Fehler* -Abschnitt der API-Richtlinien ausgelöst, oder
+ sie verfügt über einen mit 4xx oder 5xx übereinstimmenden Antwort-HTTP-Statuscode.

## <a name="performance-implications-and-log-sampling"></a>Auswirkungen auf die Leistung und Protokollsampling

> [!WARNING]
> Die Protokollierung aller Ereignisse kann abhängig von der Rate der eingehenden Anforderungen schwerwiegende Auswirkungen auf die Leistung haben.

Bei internen Auslastungstests reduzierte die Aktivierung dieses Features den Durchsatz um 40-50%, wenn die Anforderungsrate 1.000 Anforderungen pro Sekunde überschritt. Mit Azure Application Insights wird die statistische Analyse zur Bewertung der Anwendungsleistung verwendet. Jedoch ist dieses Programm kein Überwachungssystem und nicht zur Protokollierung jeder einzelnen an APIs mit hohem Volumen gerichteten Anforderung geeignet.

Sie können die Anzahl der protokollierten Anforderungen durch Anpassen der Einstellung **Sampling** beeinflussen (siehe die oben beschriebenen Schritte). Der Wert 100% bedeutet, dass alle Anforderungen protokolliert werden, während 0% überhaupt keine Protokollierung widerspiegelt. Mit **Sampling** können Sie das Volumen von Telemetriedaten reduzieren, einen erheblichen Leistungsabfall effektiv verhindern und dabei dennoch die Vorteile der Protokollierung nutzen.

Das Überspringen der Protokollierung von Headern und Textkörpern der Anforderungen und Antworten hat auch positive Auswirkungen auf die Abschwächung von Leistungsproblemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nächste Schritte

+ Erfahren Sie mehr über [Azure Application Insights](/azure/application-insights/).
+ Betrachten Sie die [Protokollierung mit Azure Event Hubs](api-management-howto-log-event-hubs.md).
