---
title: Importieren von Log Analytics-Daten in Power BI | Microsoft Dokumentation
description: Power BI ist ein cloudbasierter Business Analytics-Dienst von Microsoft, der umfassende Visualisierungen und Berichte zur Analyse verschiedener Datensätze bietet.  In diesem Artikel wird beschrieben, wie Log Analytics-Daten konfiguriert und in Power BI importiert werden und wie die automatische Aktualisierung konfiguriert wird.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 53277f64c3d1b03572732157756da1fececbcd43
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184568"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importieren von Azure Monitor-Protokolldaten in Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) ist ein cloudbasierter Business Analytics-Dienst von Microsoft, der umfassende Visualisierungen und Berichte zur Analyse verschiedener Datensätze bietet.  Sie können die Ergebnisse einer Azure Monitor-Protokollabfrage in ein Power BI-Dataset importieren, damit Sie dessen Funktionen wie das Kombinieren von Daten aus verschiedenen Quellen und das Freigeben von Berichten im Web und auf mobilen Geräten nutzen können.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Übersicht
Zum Importieren von Daten aus einem [Log Analytics-Arbeitsbereich](manage-access.md) in Azure Monitor in Power BI erstellen Sie in Power BI basierend auf einer [Protokollabfrage](../log-query/log-query-overview.md) in Azure Monitor ein Dataset.  Die Abfrage wird bei jeder Aktualisierung des Datasets ausgeführt.  Sie können dann Power BI-Berichte erstellen, die Daten aus dem Dataset verwenden.  Zum Erstellen des Datasets in Power BI exportieren Sie Ihre Abfrage aus Log Analytics in die [Power Query-Sprache (M) ](/powerquery-m/power-query-m-language-specification).  Diese verwenden Sie dann zum Erstellen einer Abfrage in Power BI Desktop, die Sie anschließend als Dataset in Power BI veröffentlichen.  Die Details für diesen Prozess werden nachfolgend beschrieben.

![Von Log Analytics nach Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Exportieren einer Abfrage
Erstellen Sie zunächst eine [Protokollabfrage](../log-query/log-query-overview.md), die die Daten zurückgibt, mit denen das Power BI-Dataset aufgefüllt werden soll.  Anschließend exportieren Sie die Abfrage in der [Power Query-Sprache (M)](/powerquery-m/power-query-m-language-specification), die von Power BI Desktop verwendet werden kann.

1. [Erstellen Sie die Protokollabfrage in Log Analytics](../log-query/log-analytics-tutorial.md) zum Extrahieren der Daten für das Dataset.
2. Wählen Sie **Exportieren** > **Power BI Query (M)** (Power BI-Abfrage (M)) aus.  Damit wird die Abfrage in eine Textdatei mit dem Namen **PowerBIQuery.txt** exportiert. 

    ![Exportieren einer Protokollsuche](media/powerbi/export-analytics.png)

3. Öffnen Sie die Textdatei, und kopieren Sie den Inhalt.

## <a name="import-query-into-power-bi-desktop"></a>Importieren einer Abfrage in Power BI Desktop
Power BI Desktop ist eine Desktopanwendung, die Ihnen das Erstellen von Datasets und Berichten ermöglicht, die in Power BI veröffentlicht werden können.  Sie können damit auch eine Abfrage in der aus Azure Monitor exportierten Power Query-Sprache erstellen. 

1. Installieren Sie [Power BI Desktop](https://powerbi.microsoft.com/desktop/), wenn Sie es noch nicht haben, und öffnen Sie die Anwendung.
2. Wählen Sie **Daten abrufen** > **Leere Abfrage**, um eine neue Abfrage zu öffnen.  Wählen Sie dann **Erweiterter Editor**, und fügen Sie den Inhalt der exportierten Datei in die Abfrage ein. Klicken Sie auf **Fertig**.

    ![Power BI Desktop-Abfrage](media/powerbi/desktop-new-query.png)

5. Die Abfrage wird ausgeführt, und die Ergebnisse werden angezeigt.  Sie werden möglicherweise aufgefordert, Anmeldeinformationen zum Verbinden mit Azure einzugeben.  
6. Geben Sie einen beschreibenden Namen für die Abfrage ein.  Der Standardwert lautet **Abfrage1**. Klicken Sie auf **Schließen und übernehmen**, um das Dataset dem Bericht hinzuzufügen.

    ![Power BI Desktop-Name](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Veröffentlichen in Power BI
Wenn Sie in Power BI veröffentlichen, werden ein Dataset und ein Bericht erstellt.  Wenn Sie einen Bericht in Power BI Desktop erstellen, wird dieser mit Ihren Daten veröffentlicht.  Wenn nicht, wird ein leerer Bericht erstellt.  Sie können den Bericht in Power BI ändern oder einen neuen auf Basis des Datasets erstellen.

1. Erstellen Sie einen Bericht auf Basis Ihrer Daten.  Wenn Sie mit Power BI Desktop nicht vertraut sind, lesen Sie in der [Dokumentation](/power-bi/desktop-report-view) dazu nach.  
1. Wenn bereit sind, ihn an Power BI zu senden, klicken Sie auf **Veröffentlichen**.  
1. Wählen Sie ein Ziel in Ihrem Power BI-Konto aus, wenn Sie dazu aufgefordert werden.  Sofern Sie kein bestimmtes Ziel verwenden möchten, verwenden Sie **Mein Arbeitsbereich**.

    ![Veröffentlichen in Power BI Desktop](media/powerbi/desktop-publish.png)

1. Wenn die Veröffentlichung abgeschlossen ist, klicken Sie auf **In Power BI öffnen**, um Power BI mit Ihrem neuen Dataset zu öffnen.


### <a name="configure-scheduled-refresh"></a>Konfigurieren von geplanten Aktualisierungen
Das in Power BI erstellte Dataset enthält dieselben Daten, die zuvor in Power BI Desktop angezeigt wurden.  Sie müssen das Dataset in regelmäßigen Abständen aktualisieren, um die Abfrage erneut auszuführen und es mit den aktuellen Daten aus Azure Monitor zu füllen.  

1. Klicken Sie auf den Arbeitsbereich, in dem Sie Ihren Bericht hochgeladen haben, und wählen Sie dann das Menü **Datasets**. 
1. Wählen Sie das Kontextmenü neben dem neuen Dataset und dann **Einstellungen** aus. 
1. Unter **Anmeldeinformationen für die Datenquelle** sollte eine Meldung angezeigt werden, dass die Anmeldeinformationen ungültig sind.  Dies liegt daran, dass Sie noch keine Anmeldeinformationen für das Dataset zum Aktualisieren seiner Daten bereitgestellt haben.  
1. Klicken Sie auf **Anmeldeinformationen bearbeiten**, und geben Sie Anmeldeinformationen mit Zugriff auf den Log Analytics-Arbeitsbereich in Azure Monitor an. Wenn Sie die zweistufige Authentifizierung benötigen, wählen Sie **OAuth2** für die **Authentifizierungsmethode** aus, damit Sie zur Anmeldung mit Ihren Anmeldeinformationen aufgefordert werden.

    ![Power BI-Zeitplan](media/powerbi/powerbi-schedule.png)

5. Legen Sie unter **Geplante Aktualisierung** die Option **Halten Sie Ihre Daten aktuell** fest.  Sie haben die Möglichkeit, die **Aktualisierungshäufigkeit** und eine oder mehrere bestimmte Uhrzeiten für die Ausführung der Aktualisierung zu ändern.

    ![Power BI-Aktualisierung](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr zu [Protokollsuchvorgängen](../log-query/log-query-overview.md) zum Erstellen von Abfragen, die nach Power BI exportiert werden können.
* Erfahren Sie mehr über [Power BI](https://powerbi.microsoft.com), um Visualisierungen basierend auf Azure Monitor-Protokolle-Exporten zu erstellen.