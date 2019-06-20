---
title: Horizontales Hochskalieren von Azure Analysis Services | Microsoft-Dokumentation
description: Replizieren von Azure Analysis Services-Servern mittels horizontalem Hochskalieren
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5524645153db0468076cc9b567965bff79d915cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192303"
---
# <a name="azure-analysis-services-scale-out"></a>Horizontales Hochskalieren von Azure Analysis Services

Durch die horizontale Skalierung können Clientabfragen auf mehrere *Abfragereplikate* in einem *Abfragepool* verteilt werden, was bei einem hohen Aufkommen von Abfrageworkloads zu kürzeren Antwortzeiten führt. Darüber hinaus kann die Verarbeitung vom Abfragepool getrennt werden, um sicherzustellen, dass Clientabfragen nicht durch Verarbeitungsvorgänge beeinträchtigt werden. Horizontales Hochskalieren kann im Azure-Portal oder mithilfe der Analysis Services-REST-API konfiguriert werden.

Horizontales Hochskalieren ist für Server im Standardtarif verfügbar. Jedes Abfragereplikat wird mit dem gleichen Tarif abgerechnet wie Ihr Server. Alle Abfragereplikate werden in der gleichen Region wie Ihr Server erstellt. Die Anzahl der von Ihnen konfigurierbaren Abfragereplikate ist durch die Region, in der sich Ihr Server befindet, eingeschränkt. Weitere Informationen finden Sie unter [Verfügbarkeit nach Region](analysis-services-overview.md#availability-by-region). Durch horizontales Hochskalieren erhöht sich nicht die Menge an verfügbarem Arbeitsspeicher für Ihren Server. Zur Erhöhung des Arbeitsspeichers müssen Sie Ihren Plan upgraden. 

## <a name="why-scale-out"></a>Horizontale Skalierung

In einer typischen Serverbereitstellung fungiert ein einzelner Server sowohl als Verarbeitungs- als auch als Abfrageserver. Wenn die Anzahl von Clientabfragen für Modelle auf dem Server die QPUs (Query Processing Units) für den Tarif Ihres Servers übersteigt oder die Modellverarbeitung mit einem hohen Aufkommen von Abfrageworkloads zusammenfällt, kann sich dies negativ auf die Leistung auswirken. 

Mit der horizontalen Skalierung können Sie einen Abfragepool mit bis zu sieben zusätzlichen Abfragereplikatressourcen erstellen, sodass Sie zusammen mit Ihrem *primären* Server über insgesamt acht verfügen. Sie können die Anzahl von Replikaten im Abfragepool jederzeit skalieren, um die QPU-Anforderungen in kritischen Zeiten zu erfüllen, und Sie können einen Verarbeitungsserver aus dem Abfragepool herauslösen. 

Verarbeitungsworkloads werden unabhängig von der Anzahl von Abfragereplikaten in einem Abfragepool nicht auf Abfragereplikate verteilt. Der primäre Server fungiert als Verarbeitungsserver. Abfragereplikate bedienen ausschließlich Abfragen für die Modelldatenbanken, die zwischen dem primären Server und den einzelnen Replikaten im Abfragepool synchronisiert werden. 

Beim horizontalen Skalieren kann es bis zu fünf Minuten dauern, bis neue Abfragereplikate in den Abfragepool aufgenommen werden. Wenn alle neuen Abfragereplikate einsatzbereit sind, kommen für neue Clientverbindungen Ressourcen im Abfragepool für den Lastenausgleich zum Einsatz. Bestehende Clientverbindungen werden nicht geändert; die Verbindung mit der bisherigen Ressource bleibt bestehen. Beim horizontalen Herunterskalieren werden alle bestehenden Clientverbindungen mit einer Abfragepoolressource, die aus dem Abfragepool entfernt wird, beendet. Clients können die Verbindung mit verbleibenden Poolressourcen wiederherstellen.

## <a name="how-it-works"></a>So funktioniert's

Beim ersten Konfigurieren der horizontalen Skalierung werden Modelldatenbanken auf Ihrem primären Server *automatisch* mit neuen Replikaten in einem neuen Abfragepool synchronisiert. Die automatische Synchronisierung erfolgt nur einmal. Bei der automatischen Synchronisierung werden die Datendateien des (im Ruhezustand im Blobspeicher verschlüsselten) primären Servers an einen zweiten Speicherort kopiert, der ebenfalls im Ruhezustand im Blobspeicher verschlüsselt ist. Replikate im Abfragepool werden dann mit Daten aus dem zweiten Datensatz *aufgefüllt*. 

Eine automatische Synchronisierung erfolgt nur, wenn Sie erstmals eine horizontale Skalierung ausführen. Sie können jedoch auch eine manuelle Synchronisierung durchführen. Durch die Synchronisierung wird sichergestellt, dass die Daten auf Replikaten im Abfragepool mit den Daten im primären Server übereinstimmen. Beim Verarbeiten von (aktualisierten) Modellen auf dem primären Server ist *nach* dem Abschluss der Verarbeitungsvorgänge eine Synchronisierung erforderlich. Bei dieser Synchronisierung werden aktualisierte Daten aus den Dateien des primären Servers im Blobspeicher in den zweiten Datensatz kopiert. Replikate im Abfragepool werden dann mit aktualisierten Daten aus dem zweiten Datensatz im Blobspeicher aufgefüllt. 

Wenn Sie einen nachfolgenden horizontalen Skalierungsvorgang ausführen und beispielsweise die Anzahl von Replikaten im Abfragepool von zwei auf fünf erhöhen, werden die neuen Replikate mit Daten aus dem Datensatz im Blobspeicher aktualisiert. Hier erfolgt keine Synchronisierung. Wenn Sie nach dem Hochskalieren eine Synchronisierung durchführen, werden die neuen Replikaten im Abfragepool zweimal aufgefüllt – ein redundanter Vorgang. Bedenken Sie Folgendes, wenn Sie eine nachfolgende horizontale Skalierung durchführen:

* Führen Sie *vor der horizontalen Skalierung* eine Synchronisierung durch, um redundante Vorgänge mit zusätzlichen Replikaten zu vermeiden. Ein gleichzeitiges Ausführen von Synchronisierung und horizontaler Skalierung ist nicht zulässig.

* Beim Automatisieren von Verarbeitungs- *und* Skalierungsvorgängen müssen Sie zuerst die Daten auf dem primären Server verarbeiten, dann eine Synchronisierung und dann die horizontale Skalierung durchführen. So stellen Sie möglichst geringe Auswirkungen auf die Ressourcen in QPU und Arbeitsspeicher sicher.

* Die Synchronisierung ist auch dann zulässig, wenn keine Replikate im Abfragepool vorhanden sind. Wenn Sie mit neuen Daten aus einem Verarbeitungsvorgang auf dem primären Server von Null auf ein oder mehrere Replikate hochskalieren, führen Sie zuerst die Synchronisierung ohne Replikate im Abfragepool durch und nehmen dann die horizontale Skalierung vor. Durch das Synchronisieren vor dem horizontalen Skalieren werden redundante Vorgänge mit den neu hinzugefügten Replikaten vermieden.

* Wenn Sie eine Modelldatenbank auf dem primären Server löschen, wird sie nicht automatisch aus Replikaten im Abfragepool gelöscht. Sie müssen einen Synchronisierungsvorgang mit dem PowerShell-Befehl [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) ausführen, der die Dateien für diese Datenbank aus dem Speicherort des freigegebenen Blobs des Replikats entfernt und dann die Modelldatenbank auf den Replikaten im Abfragepool löscht. Um zu bestimmen, ob eine Modelldatenbank auf Replikaten im Abfragepool vorhanden ist, jedoch nicht auf dem primären Server, stellen Sie sicher, dass die Einstellung **Verarbeitungsserver vom Abfragepool trennen** auf **Ja** gesetzt ist. Verwenden Sie dann SSMS, um mithilfe des `:rw`-Qualifizierers eine Verbindung mit dem primären Server herzustellen und zu ermitteln, ob die Datenbank vorhanden ist. Stellen Sie dann eine Verbindung mit Replikaten im Abfragepool her, indem Sie eine Verbindung ohne den `:rw`-Qualifizierer herstellen, um zu ermitteln, ob die gleiche Datenbank ebenfalls vorhanden ist. Ist die Datenbank auf Replikaten im Abfragepool vorhanden, jedoch nicht auf dem primären Server, führen Sie einen Synchronisierungsvorgang aus.   

* Wenn Sie eine Datenbank auf dem primären Server umbenennen, ist ein zusätzlicher Schritt erforderlich, um sicherzustellen, dass die Datenbank ordnungsgemäß mit Replikaten synchronisiert wird. Nach dem Umbenennen führen Sie eine Synchronisierung mit dem Befehl [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) durch. Dabei legen Sie den Parameter `-Database` mit dem alten Datenbanknamen fest. Bei dieser Synchronisierung werden die Datenbank und die Dateien mit dem alten Namen aus Replikate entfernt. Führen Sie eine weitere Synchronisierung durch. Dabei legen Sie den Parameter `-Database` mit dem alten Datenbanknamen fest. Bei der zweiten Synchronisierung wird die neu benannte Datenbank in den zweiten Datensatz kopiert und alle Replikate aufgefüllt. Diese Synchronisierungen können nicht mithilfe des Befehls „Modell synchronisieren“ im Portal ausgeführt werden.

### <a name="separate-processing-from-query-pool"></a>Getrennte Verarbeitung vom Abfragepool

Zur Optimierung der Leistung bei Verarbeitungs- und Abfragevorgängen können Sie optional Ihren Verarbeitungsserver vom Abfragepool trennen. Nach der Trennung werden neue Clientverbindungen nur den Abfragereplikaten im Abfragepool zugewiesen. Wenn Verarbeitungsvorgänge nur eine kurze Zeit dauern, können Sie Ihren Verarbeitungsserver auch nur für den Zeitraum vom Abfragepool trennen, der zur Durchführung der Verarbeitungs- und Synchronisierungsvorgänge erforderlich ist, und ihn dann wieder in den Abfragepool aufnehmen. Der Vorgang zum Trennen des Verarbeitungsservers vom Abfragepool oder zum erneuten Hinzufügen kann bis zu fünf Minuten dauern.

## <a name="monitor-qpu-usage"></a>Überwachen der QPU-Nutzung

Überwachen Sie Ihren Server im Azure-Portal mithilfe von Metriken, um zu ermitteln, ob horizontales Hochskalieren für Ihren Server erforderlich ist. Wenn regelmäßig die QPU-Obergrenze erreicht wird, übersteigt die Anzahl von Abfragen für Ihre Modelle das QPU-Limit für Ihren Tarif. Die Metrik „Warteschlangenlänge für Abfragepoolaufträge“ erhöht sich auch, wenn die Anzahl von Abfragen in der Warteschlange des Abfragethreadpools die verfügbaren QPUs übersteigt. 

Eine weitere gute Metrik zum Überwachen ist die durchschnittliche QPU nach ServerResourceType. Mit dieser Metrik wird die durchschnittliche QPU für den primären Server mit der des Abfragepools verglichen. 

![Abfragen von Metriken für horizontales Hochskalieren](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Konfigurieren von QPU nach ServerResourceType
1. Klicken Sie in einem Metrik-Liniendiagramm auf **Metrik hinzufügen**. 
2. Wählen Sie unter **RESSOURCE** Ihren Server aus. Wählen Sie dann unter **METRIKNAMESPACE** die Option **Standardmetriken für Analysis Services** und unter **METRIK** die Option **QPU** aus, und klicken Sie unter **AGGREGATION** auf **Durchschn**. 
3. Klicken Sie auf **Teilen anwenden**. 
4. Wählen Sie unter **WERTE** die Option **ServerResourceType**.  

Weitere Informationen finden Sie unter [Überwachen von Servermetriken](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurieren des horizontalen Hochskalierens

### <a name="in-azure-portal"></a>Im Azure-Portal

1. Klicken Sie im Portal auf **Horizontale Skalierung**. Wählen Sie mithilfe des Schiebereglers die Anzahl von Abfragereplikatservern aus. Die gewählte Anzahl von Replikaten kommt zu Ihrem bereits vorhandenen Server hinzu.

2. Klicken Sie unter **Verarbeitungsserver vom Abfragepool trennen** auf „Ja“, um Ihren Verarbeitungsserver von Abfrageservern auszuschließen. [Clientverbindungen](#connections), die die Standardverbindungszeichenfolge (ohne `:rw`) verwenden, werden an Replikate im Abfragepool umgeleitet. 

   ![Schieberegler für horizontales Hochskalieren](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicken Sie auf **Speichern**, um Ihre neuen Abfragereplikatserver bereitzustellen. 

Beim ersten Konfigurieren der horizontalen Skalierung für einen Server werden Modelle auf Ihrem primären Server automatisch mit neuen Replikaten im Abfragepool synchronisiert. Die automatische Synchronisierung erfolgt nur einmal, wenn Sie erstmals die horizontale Skalierung auf ein oder mehrere Replikate konfigurieren. Nachfolgende Änderungen an der Anzahl von Replikaten auf dem gleichen Server *lösen keine weitere automatische Synchronisierung aus*. Die automatische Synchronisierung wird nicht erneut ausgeführt, selbst wenn Sie den Server auf null Replikate festlegen und dann eine horizontale Skalierung auf eine bestimmte Anzahl von Replikaten vornehmen. 

## <a name="synchronize"></a>Synchronisieren 

Synchronisierungsvorgänge müssen manuell oder mithilfe der REST-API ausgeführt werden.

### <a name="in-azure-portal"></a>Im Azure-Portal

Klicken Sie in der **Übersicht** für das Modell auf das Symbol **Modell synchronisieren**.

![Schieberegler für horizontales Hochskalieren](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API

Verwenden Sie die **sync**-Operation.

#### <a name="synchronize-a-model"></a>Synchronisieren eines Modells   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Abrufen des Synchronisierungsstatus  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Rückgabestatuscodes:


|Code  |BESCHREIBUNG  |
|---------|---------|
|-1     |  Ungültig       |
|0     | Replikation wird ausgeführt        |
|1     |  Aktivierung wird ausgeführt       |
|2     |   Abgeschlossen       |
|3     |   Fehler      |
|4     |    Ausführung wird abgeschlossen     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vor der Verwendung von PowerShell müssen Sie [das neueste Azure PowerShell-Modul installieren oder aktualisieren](/powershell/azure/install-az-ps). 

Verwenden Sie [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance), um die Synchronisierung auszuführen.

Verwenden Sie [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver), um die Anzahl von Abfragereplikaten festzulegen. Geben Sie den optionalen Parameter `-ReadonlyReplicaCount` an.

Verwenden Sie [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver), um den verarbeitenden Server vom Abfragepool zu trennen. Geben Sie den optionalen Parameter `-DefaultConnectionMode` an, um `Readonly` zu verwenden.

Weitere Informationen finden Sie unter [Verwenden eines Dienstprinzipals mit dem Az.AnalysisServices-Modul](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Verbindungen

Auf der Übersichtsseite des Servers werden zwei Servernamen angezeigt. Wenn Sie noch keine horizontale Hochskalierung für einen Server konfiguriert haben, funktionieren beide Servernamen gleich. Nach dem Konfigurieren der horizontalen Hochskalierung für einen Server müssen Sie den passenden Servernamen für den jeweiligen Verbindungstyp angeben. 

Für Endbenutzer-Clientverbindungen wie Power BI Desktop, Excel und benutzerdefinierte Apps muss der **Servername** verwendet werden. 

Für SSMS, SSDT und Verbindungszeichenfolgen in PowerShell, Azure Functions-Apps und AMO muss der **Name des Verwaltungsservers** verwendet werden. Der Name des Verwaltungsservers enthält einen speziellen `:rw`-Qualifizierer (Lesen/Schreiben). Sämtliche Verarbeitungsvorgänge finden auf dem (primären) Verwaltungsserver statt.

![Servernamen](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Problembehandlung

**Problem:** Benutzer erhalten die Fehlermeldung **Serverinstanz „\<Name des Servers>“ im Verbindungsmodus „ReadOnly“ nicht gefunden.**

**Lösung:** Beim Auswählen der Option **Verarbeitungsserver vom Abfragepool trennen** werden Clientverbindungen, die die Standardverbindungszeichenfolge (ohne `:rw`) verwenden, an Abfragepoolreplikate umgeleitet. Wenn Replikate im Abfragepool noch nicht online sind, weil die Synchronisierung noch nicht abgeschlossen ist, können umgeleitete Clientverbindungen fehlschlagen. Um Verbindungsfehler zu verhindern, müssen beim Durchführen einer Synchronisierung mindestens zwei Server im Abfragepool enthalten sein. Jeder Server wird einzeln synchronisiert, während andere Server online bleiben. Wenn der Verarbeitungsserver während der Verarbeitung nicht im Abfragepool enthalten sein soll, können Sie ihn für die Verarbeitung aus dem Pool entfernen und dann nach Abschluss der Verarbeitung, jedoch vor der Synchronisierung, wieder zum Pool hinzufügen. Verwenden Sie die Metriken „Arbeitsspeicher“ und „QPU“, um den Synchronisierungsstatus zu überwachen.



## <a name="related-information"></a>Verwandte Informationen

[Überwachen von Servermetriken](analysis-services-monitor.md)   
[Verwalten von Azure Analysis Services](analysis-services-manage.md) 
