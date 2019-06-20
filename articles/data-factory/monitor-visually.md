---
title: Visuelles Überwachen von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Data Factory visuell überwachen.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60716901"
---
# <a name="visually-monitor-azure-data-factories"></a>Visuelles Überwachen von Azure Data Factory
Azure Data Factory ist ein cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows in der Cloud erstellen können, um Datenverschiebungen und Datentransformationen zu orchestrieren und zu automatisieren. Mit Azure Data Factory können Sie datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen, diese Daten mithilfe von Compute Services wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics und Azure Machine Learning verarbeiten/transformieren und die Ausgabedaten für Datenspeicher wie Azure SQL Data Warehouse veröffentlichen, damit diese von Business Intelligence (BI)-Anwendungen genutzt werden können.

In dieser Schnellstartanleitung erfahren Sie, wie Sie Data Factory-Pipelines visuell überwachen, ohne eine einzige zu Codezeile schreiben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="monitor-data-factory-pipelines"></a>Überwachen von Data Factory-Pipelines

Überwachen Sie die Pipeline und Aktivitätsausführungen in einer einfachen Benutzeroberfläche mit Listenansicht. Alle Ausführungen werden in der Zeitzone des lokalen Browsers angezeigt. Sie können die Zeitzone ändern. Dadurch werden alle Datums- und Zeitfelder auf die ausgewählte Zeitzone ausgerichtet.  

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
3. Navigieren Sie zum erstellten Data Factory-Blatt im Azure-Portal, und klicken Sie auf die Kachel „Überwachung und Verwaltung“, um die visuelle Überwachung von Data Factory zu starten.

## <a name="monitor-pipeline-runs"></a>Überwachen der Pipelineausführungen
Die Listenansicht zeigt jede Pipelineausführung für Ihre Data Factory V2-Pipelines an. Enthaltene Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Pipeline Name | Name der Pipeline. |
| Aktionen | Einzelne Aktionen sind verfügbar, um Aktivitätsausführungen anzuzeigen. |
| Run Start | Startdatum und -uhrzeit der Pipelineausführung (MM-TT-JJJJ, hh:mm:ss) |
| Duration | Dauer der Ausführung (hh:mm:ss) |
| Triggered By | Manueller Trigger, Zeitplantrigger |
| Status | Fehlerhaft, Erfolgreich, In Bearbeitung |
| Parameter | Parameter der Pipelineausführung (Name-Wert-Paare) |
| Error | Fehler bei der Pipelineausführung (falls vorhanden) |
| Run ID | ID der Pipelineausführung |

![Überwachen der Pipelineausführungen](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Überwachung der Aktivitätsausführungen
Die Listenansicht zeigt die Aktivitätsausführungen an, die der jeweiligen Pipelineausführung entsprechen. Klicken Sie auf das Symbol **Aktivitätsausführungen** unter der Spalte **Aktionen**, um die Aktivitätsausführungen für jede Pipelineausführung anzuzeigen. Enthaltene Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Activity Name | Name der Aktivität innerhalb der Pipeline |
| Activity Type | Art der Aktivität, z.B. Kopieren, HDInsightSpark, HDInsightSpark usw. |
| Run Start | Startdatum und -uhrzeit der Aktivitätsausführung (MM-TT-JJJJ, hh:mm:ss) |
| Duration | Dauer der Ausführung (hh:mm:ss) |
| Status | Fehlerhaft, Erfolgreich, In Bearbeitung |
| Eingabe | Ein JSON-Array, das die Aktivitätseingaben beschreibt. |
| Output | Ein JSON-Array, das die Aktivitätsausgaben beschreibt. |
| Error | Fehler bei der Aktivitätsausführung (falls vorhanden) |

![Überwachung der Aktivitätsausführungen](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Sie müssen im oberen Bereich auf das Symbol **Aktualisieren** klicken, um die Liste der Pipelines und Aktivitätsausführungen zu aktualisieren. Ein automatisches Update wird derzeit nicht unterstützt.

![Aktualisieren](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Auswählen einer zu überwachenden Data Factory
Zeigen Sie auf das Symbol **Data Factory** im oberen linken Bereich. Klicken Sie auf das Pfeilsymbol, um eine Liste der Azure-Abonnements und Data Factorys anzuzeigen, die Sie überwachen können.

![Data Factory auswählen](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Konfigurieren der Listenansicht

### <a name="apply-rich-ordering-and-filtering"></a>Umfangreiches Sortieren und Filtern

Sortieren Sie Pipelineausführungen auf- oder absteigend nach Start der Ausführung, und filtern Sie Pipelineausführungen nach folgenden Spalten:

| **Spaltenname** | **Beschreibung** |
| --- | --- |
| Pipeline Name | Name der Pipeline. Die Optionen enthalten Schnellfilter für „Letzte 24 Stunden“, „Letzte Woche“, „Letzte 30 Tage“ oder einen benutzerdefinierten Zeitraum. |
| Run Start | Startdatum und -uhrzeit der Pipelineausführung |
| Run Status | Filtern von Ausführungen nach Status, z.B. Erfolgreich, Fehlgeschlagen, In Bearbeitung |

![Filter](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Hinzufügen oder Entfernen von Spalten
Klicken Sie mit der rechten Maustaste auf den Header der Listenansicht, und wählen Sie die Spalten aus, die in der Listenansicht angezeigt werden sollen.

![Spalten](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Anpassen der Spaltenbreite
Erhöhen oder verringern Sie die Spaltenbreiten in der Listenansicht, indem Sie mit der Maus auf die Spaltenüberschrift zeigen.

## <a name="promote-user-properties-to-monitor"></a>Heraufstufen von Benutzereigenschaften für die Überwachung

Sie können jede Pipelineaktivitätseigenschaft als Benutzereigenschaft heraufstufen, sodass sie zu einer Entität wird, die Sie überwachen können. Sie können z.B. die Eigenschaften **Quelle** und **Ziel** der Kopieraktivität in der Pipeline als Benutzereigenschaften heraufstufen. Sie können auch **Automatisch generieren** zum Generieren der Benutzereigenschaften **Quelle** und **Ziel** für eine Kopieraktivität auswählen.

![Erstellen von Benutzereigenschaften](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Sie können nur bis zu 5 Pipelineaktivitätseigenschaften zu Benutzereigenschaften heraufstufen.

Nachdem Sie die Benutzereigenschaften erstellt haben, können Sie sie in den Überwachungslistenansichten überwachen. Ist die Quelle für die Kopieraktivität ein Tabellenname, können Sie den Namen der Quelltabelle als Spalte in der Listenansicht der Aktivitätsausführungen überwachen.

![Aktivitätsausführungenliste ohne Benutzereigenschaften](media/monitor-visually/monitor-user-properties-image2.png)

![Hinzufügen von Spalten für Benutzereigenschaften zur Aktivitätsausführungenliste](media/monitor-visually/monitor-user-properties-image3.png)

![Aktivitätsausführungenliste mit Spalten für Benutzereigenschaften](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Erneutes Ausführen von Aktivitäten innerhalb einer Pipeline

Aktivitäten innerhalb einer Pipeline können nun erneut ausgeführt werden. Klicken Sie auf **View Activity Runs** (Aktivitätsausführungen anzeigen), und wählen Sie die Aktivität in Ihrer Pipeline aus, ab der die Pipeline erneut ausgeführt werden soll.

![Anzeigen der Aktivitätsausführungen](media/monitor-visually/rerun-activities-image1.png)

![Auswählen einer Aktivitätsausführung](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Anzeigen des Verlaufs für erneute Ausführungen

Der Verlauf für erneute Ausführungen kann für alle Pipelineausführungen in der Listenansicht angezeigt werden.

![Anzeigen des Verlaufs](media/monitor-visually/rerun-history-image1.png)

Sie können aber auch den Verlauf für erneute Ausführungen für eine bestimmte Pipelineausführung anzeigen.

![Anzeigen des Verlaufs für eine Pipelineausführung](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>Führungen
Klicken Sie auf das Symbol „Informationen“ im unteren linken Bereich und dann auf „Führungen“, um eine ausführliche Anleitung zum Überwachen Ihrer Pipeline und Aktivitätsausführungen zu erhalten.

![Führungen](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Feedback
Klicken Sie auf das Symbol „Feedback“, um uns Ihr Feedback zu den verschiedenen Features oder zu möglichen Problemen zu übermitteln.

![Feedback](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alerts

Sie können Warnungen auf der Grundlage unterstützter Metriken in Data Factory auslösen. Wählen Sie auf der Überwachungsseite von Data Factory **Überwachen > Warnungen und Metriken** aus, um zu beginnen.

![](media/monitor-visually/alerts01.png)

Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Erstellen von Warnungen

1.  Klicken Sie auf **Neue Warnungsregel** , um eine neue Warnung zu erstellen.

    ![](media/monitor-visually/alerts02.png)

1.  Geben Sie den Regelnamen an, und wählen Sie den **Schweregrad** für die Warnung ein.

    ![](media/monitor-visually/alerts03.png)

1.  Wählen Sie die Warnungskriterien aus.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Konfigurieren Sie die Warnungslogik. Sie können eine Warnung für die ausgewählte Metrik für alle Pipelines und entsprechenden Aktivitäten erstellen. Sie können auch einen bestimmten Aktivitätstyp, Aktivitätsnamen, Pipelinenamen oder einen Fehlertyp auswählen.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurieren Sie Benachrichtigungen des Typs **E-Mail/SMS/Push/Sprachanruf** für die Warnung. Erstellen Sie eine **Aktionsgruppe** für die Warnungsbenachrichtigungen, oder wählen Sie eine vorhandene Gruppe aus.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Erstellen Sie die Warnungsregel.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Programmgesteuertes Überwachen und Verwalten von Pipelines](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) erfahren Sie mehr zum Überwachen und Verwalten von Pipelines.
