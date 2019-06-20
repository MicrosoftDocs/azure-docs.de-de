---
title: Fortlaufender Export von Telemetriedaten aus Application Insights | Microsoft Docs
description: Exportieren Sie Diagnose- und Nutzungsdaten in Microsoft Azure-Speicher, die Sie anschließendes daraus herunterladen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 71e70962a8c55d397b6261571cfef4a126d3e8b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60899369"
---
# <a name="export-telemetry-from-application-insights"></a>Exportieren von Telemetriedaten aus Application Insights
Möchten Sie Ihre Telemetriedaten länger aufbewahren als von der standardmäßigen Beibehaltungsdauer vorgesehen? Oder möchten Sie sie in einer speziellen Art und Weise verarbeiten? Der fortlaufende Export eignet sich hierfür ideal. Die Ereignisse, die Sie im Application Insights-Portal sehen, können im JSON-Format in Microsoft Azure-Speicher exportiert werden. Sie können Ihre Daten anschließend herunterladen und den Code schreiben, den Sie zu ihrer Verarbeitung benötigen.  

Bevor Sie den fortlaufenden Export einrichten, sollten Sie folgende Alternativen zu dieser Vorgehensweise prüfen:

* Über die Schaltfläche „Exportieren“ ganz oben auf dem Blatt „Metriken“ oder „Suche“ können Sie Tabellen und Diagramme in ein Excel-Arbeitsblatt übertragen.

* [Analytics](../../azure-monitor/app/analytics.md) bietet eine leistungsstarke Abfragesprache für Telemetriedaten. Das Exportieren von Ergebnissen ist auch möglich.
* Wenn Sie [Ihre Daten in Power BI untersuchen](../../azure-monitor/app/export-power-bi.md )möchten, benötigen Sie dazu keinen fortlaufenden Export.
* Mit dem [REST-API für Datenzugriff](https://dev.applicationinsights.io/) können Sie programmgesteuert auf Ihre Telemetriedaten zugreifen.
* Sie können auch auf das Setup für den [fortlaufenden Export über PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport) zugreifen.

Nachdem Ihre Daten vom Fortlaufende Export in den Speicher kopiert wurden (wo sie beliebig lange bleiben können), sind sie für die übliche [Aufbewahrungsdauer](../../azure-monitor/app/data-retention-privacy.md)auch noch in Application Insights verfügbar.

## <a name="continuous-export-advanced-storage-configuration"></a>Fortlaufender Export – erweiterte Speicherkonfiguration

Der fortlaufende Export **unterstützt nicht** die folgenden Azure-Speicherfeatures/-Konfigurationen:

* Verwenden von [VNET-/Azure Storage-Firewalls](https://docs.microsoft.com/azure/storage/common/storage-network-security) in Verbindung mit Azure Blob Storage.

* [Unveränderlicher Speicher](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) für Azure Blob Storage.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a> Erstellen eines fortlaufenden Exports
1. Öffnen Sie in der Application Insights-Ressource für Ihre App die Option „Fortlaufender Export“, und klicken Sie auf **Hinzufügen**:

2. Wählen Sie die Telemetriedatentypen aus, die Sie exportieren möchten.

3. Erstellen oder wählen Sie ein [Azure-Speicherkonto](../../storage/common/storage-introduction.md), in dem Sie die Daten speichern möchten. Weitere Informationen zu Preisoptionen für Speicher finden Sie auf der [Seite mit offiziellen Preisen](https://azure.microsoft.com/pricing/details/storage/).

    > [!Warning]
    > Der Speicherort wird standardmäßig auf die gleiche geografische Region festgelegt, in der sich auch Ihre Application Insights-Ressource befindet. Wenn Sie die Daten in einer anderen Region speichern, fallen möglicherweise Übertragungsgebühren an.

    ![Klicken Sie auf "Hinzufügen", "Exportziel", "Speicherkonto", und erstellen Sie dann einen neuen Speicher, oder wählen Sie einen vorhandenen Speicher aus.](./media/export-telemetry/02-add.png)

4. Erstellen oder wählen Sie einen Container im Speicher:

    ![Klicken Sie auf "Ereignistypen auswählen".](./media/export-telemetry/create-container.png)

Nachdem Sie Ihren Exportvorgang erstellt haben, geht es gleich los. Sie erhalten nur Daten, die nach dem Erstellen des Exportvorgangs eingehen.

Es kann etwa eine Stunde dauern, bis Daten im Speicher angezeigt werden.

### <a name="to-edit-continuous-export"></a>Bearbeiten des fortlaufenden Exports

Wenn Sie die Ereignistypen später ändern möchten, bearbeiten Sie einfach den Export:

![Klicken Sie auf "Ereignistypen auswählen".](./media/export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Beenden des fortlaufenden Exports

Um den Export zu beenden, klicken Sie auf „Deaktivieren“. Wenn Sie erneut auf „Aktivieren“ klicken, wird der Export mit neuen Daten neu gestartet. Sie erhalten nicht die Daten, die im Portal eingegangen sind, während der Export deaktiviert war.

Um den Export dauerhaft zu beenden, löschen Sie ihn. Dabei werden Ihre Daten nicht aus dem Speicher gelöscht.

### <a name="cant-add-or-change-an-export"></a>Wer darf einen Export ergänzen oder ändern?
* Zum Ergänzen oder Ändern von Exporten benötigen Sie das Zugriffsrecht "Besitzer", "Mitwirkender" oder "Application Insights-Mitwirkender". [Weitere Informationen zu Rollen][roles].

## <a name="analyze"></a> Welche Ereignisse werden abgerufen?
Bei den exportierten Daten handelt es sich um die Telemetrierohdaten, die wir von Ihrer Anwendung empfangen. Wir fügen allerdings Standortdaten hinzu, die wir anhand der Client-IP-Adresse berechnen.

Daten, die während der [Stichprobenerstellung](../../azure-monitor/app/sampling.md) verworfen wurden, werden nicht in die exportierten Daten aufgenommen.

Andere berechnete Metriken sind nicht enthalten. Wir exportieren z. B. nicht die durchschnittliche CPU-Auslastung, doch wir exportieren die rohen Telemetriedaten, anhand derer der Durchschnitt berechnet wird.

Die Daten umfassen außerdem die Ergebnisse von [Verfügbarkeitswebtests](../../azure-monitor/app/monitor-web-app-availability.md), die Sie eingerichtet haben.

> [!NOTE]
> **Stichproben** Wenn Ihre Anwendung große Datenmengen sendet, wird möglicherweise das Stichprobenfeature verwendet und sendet nur einen Teil der Telemetriedaten. [Erfahren Sie mehr über das Erstellen von Stichproben.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Untersuchen der Daten
Sie können den Speicher direkt im Portal überprüfen. Klicken Sie auf **Durchsuchen**, wählen Sie das Speicherkonto aus, und öffnen Sie dann **Container**.

Um Azure Storage in Visual Studio zu überprüfen, öffnen Sie **Anzeigen** und dann **Cloud-Explorer**. (Wenn dieser Menübefehl nicht verfügbar ist, müssen Sie das Azure SDK installieren: Öffnen Sie das Dialogfeld **Neues Projekt**, erweitern Sie „Visual C#/Cloud“, und wählen Sie dann **Microsoft Azure SDK für .NET abrufen** aus.)

Beim Öffnen Ihres Blobspeichers sehen Sie einen Container mit einer Gruppe von Blobdateien. Der URI der einzelnen Dateien wird vom Application Insights-Ressourcennamen, dem zugehörigen Instrumentierungsschlüssel sowie von Typ, Datum und Uhrzeit der Telemetriedaten abgeleitet. (Der Ressourcenname enthält nur Kleinbuchstaben, und beim Instrumentierungsschlüssel werden Bindestriche entfernt.)

![Überprüfen Sie den Blobspeicher mit einem geeigneten Tool](./media/export-telemetry/04-data.png)

Datum und Uhrzeit werden in UTC angegeben und entsprechen dem Zeitpunkt, an dem die Telemetriedaten im Speicher abgelegt wurden, nicht dem Zeitpunkt ihrer Erzeugung. Wenn Sie also Code zum Herunterladen der Daten schreiben, kann sich dieser linear durch die Daten bewegen.

Hier ist die Form des Pfads:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Hierbei gilt:

* `blobCreationTimeUtc` ist die Uhrzeit, zu der das Blob im internen Stagingspeicher erstellt wurde.
* `blobDeliveryTimeUtc` ist die Uhrzeit, zu der das Blob in den Zielspeicher für den Export kopiert wird.

## <a name="format"></a> Datenformat
* Jedes Blob ist eine Textdatei, die mehrere durch '\n' getrennte Zeilen enthält. Es enthält die Telemetriedaten, die über einen Zeitraum von etwa einer halben Minute verarbeitet wurden.
* Jede Zeile stellt einen Telemetriedatenpunkt dar, z.B. eine Anforderung oder einen Seitenaufruf.
* Jede Zeile ist ein unformatiertes JSON-Dokument. Wenn Sie den Vorgang verfolgen möchten, öffnen Sie es in Visual Studio, und wählen Sie "Bearbeiten", "Erweitert", "Formatdatei":

![Zeigen Sie die Telemetriedaten mit einem geeigneten Tool an](./media/export-telemetry/06-json.png)

Zeiten werden mithilfe von Teilstrichen dargestellt: 10.000 Teilstriche = 1 ms. Diese Werte zeigen beispielsweise eine Dauer von 1 ms für das Senden einer Anforderung aus dem Browser, von 3 ms für den Empfang und von 1,8 s für die Verarbeitung der Seite im Browser an:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Detaillierte Datenmodellreferenz für die Eigenschaftstypen und -werte.](export-data-model.md)

## <a name="processing-the-data"></a>Verarbeiten der Daten
Im kleinen Rahmen können Sie Code zum Aufteilen Ihrer Daten schreiben, sie in eine Kalkulationstabelle einlesen usw. Beispiel:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Ein umfangreicheres Codebeispiel finden Sie unter [Verwenden einer Workerrolle][exportasa].

## <a name="delete"></a>Löschen der alten Daten
Sie sind für die Verwaltung Ihrer Speicherkapazität und das Löschen der alten Daten nach Bedarf verantwortlich.

## <a name="if-you-regenerate-your-storage-key"></a>Wenn Sie Ihren Speicherschlüssel erneut generieren...
Wenn Sie den Schlüssel zu Ihrem Speicher ändern, funktioniert der fortlaufende Export nicht mehr. In Ihrem Azure-Konto wird eine Benachrichtigung angezeigt.

Öffnen Sie das Blatt "Fortlaufender Export", und bearbeiten Sie den Export. Bearbeiten Sie das Exportziel, aber lassen Sie den gleichen Speicher ausgewählt. Klicken Sie auf "OK", um zu bestätigen.

![Bearbeiten Sie den fortlaufenden Exports, und öffnen und schließen Sie das Exportziel.](./media/export-telemetry/07-resetstore.png)

Der fortlaufende Export wird neu gestartet.

## <a name="export-samples"></a>Export-Beispiele

* [Exportieren in SQL mit Stream Analytics][exportasa]
* [Stream Analytics-Beispiel 2](export-stream-analytics.md)

Bei größeren Dimensionen sollten Sie [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-Cluster in der Cloud erwägen. HDInsight bietet eine Vielzahl von Technologien zum Verwalten und Analysieren von Big Data, die zum Verarbeiten von Daten genutzt werden können, die von Application Insights exportiert wurden.

## <a name="q--a"></a>Fragen und Antworten
* *Ich möchte aber nur ein Diagramm einmalig herunterladen.*  

    Das ist kein Problem. Klicken Sie oben im Blatt auf **Daten exportieren**.
* *Ich habe einen Export eingerichtet, aber es sind keine Daten im meinem Speicher.*

    Hat Application Insights Telemetriedaten aus Ihrer App empfangen, seit Sie den Export eingerichtet haben? Sie erhalten nur neue Daten.
* *Ich habe versucht, einen Export einrichten, aber der Zugriff wurde verweigert.*

    Wenn das Konto im Besitz Ihrer Organisation ist, müssen Sie der Gruppe "Besitzer" oder "Mitwirkende" angehören.
* *Kann ich Daten direkt in meinen eigenen lokalen Datenspeicher exportieren?*

    Nein, leider nicht. Unsere Export-Engine funktioniert zurzeit nur mit Azure-Speicher.  
* *Gibt es Begrenzung der Menge von Daten, die im Speicher abgelegt werden können?*

    Nein. Wir übertragen Daten immer weiter, bis Sie den Export löschen. Wir hören auf, wenn wir uns den Grenzwerten Ihres Blobspeichers nähern, der aber ziemlich groß ist. Es liegt an Ihnen, wie viel Speicher Sie belegen.  
* *Wie viele Blobs sollten im Speicher angezeigt werden?*

  * Für jeden Datentyp, den Sie zum Exportieren ausgewählt haben, wird jede Minute ein neues Blob erstellt (falls Daten verfügbar sind).
  * Darüber hinaus werden weitere Partitionseinheiten für Anwendungen mit hohem Datenverkehr zugeordnet. In diesem Fall erstellt jede Einheit jede Minute ein Blob.
* *Ich habe den Schlüssel zu meinem Speicher neu generiert oder den Namen des Containers geändert, und jetzt funktioniert der Export nicht mehr.*

    Bearbeiten Sie den Export, und öffnen Sie das Blatt "Exportziel". Lassen Sie denselben Speicher wie zuvor ausgewählt, und klicken Sie auf "OK", um zu bestätigen. Der Export wird neu gestartet. Wenn die Änderung in den vergangenen Tagen erfolgt ist, gehen keine Daten verloren.
* *Kann ich den Export anhalten?*

    Ja. Klicken Sie auf „Deaktivieren“.

## <a name="code-samples"></a>Codebeispiele

* [Stream Analytics-Beispiel](export-stream-analytics.md)
* [Exportieren in SQL mit Stream Analytics][exportasa]
* [Detaillierte Datenmodellreferenz für die Eigenschaftstypen und -werte.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
