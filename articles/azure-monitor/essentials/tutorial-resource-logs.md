---
title: Sammeln von Ressourcenprotokollen von einer Azure-Ressource und Analysieren der Protokolle mit Azure Monitor
description: 'Tutorial: Konfigurieren von Diagnoseeinstellungen zum Sammeln von Ressourcenprotokollen von einer Azure-Ressource in einem Log Analytics-Arbeitsbereich, wo diese per Protokollabfrage analysiert werden können.'
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 88138cc1366dcef68aeccf652af05704e72c8e15
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597545"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource

Ressourcenprotokolle bieten detaillierte Einblicke in die Vorgänge einer Azure-Ressource und sind hilfreich bei der Überwachung der Integrität und Verfügbarkeit der Ressource. Azure-Ressourcen generieren automatisch Ressourcenprotokolle, Sie müssen aber konfigurieren, wo diese gesammelt werden sollen. In diesem Tutorial lernen Sie, wie Sie eine Diagnoseeinstellung erstellen, um Ressourcenprotokolle für eine Ressource in Ihrem Azure-Abonnement zu sammeln und diese per Protokollabfrage zu analysieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Log Analytics-Arbeitsbereich in Azure Monitor
> * Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen 
> * Erstellen einer einfachen Protokollabfrage zum Analysieren von Protokollen


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie eine Azure-Ressource, die überwacht werden kann. Sie können jede Ressource in Ihrem Azure-Abonnement verwenden, die Diagnoseeinstellungen unterstützt. Um zu überprüfen, ob eine Ressource Diagnoseeinstellungen unterstützt, navigieren Sie im Azure-Portal zum Menü der Ressource, und vergewissern Sie sich, dass im Abschnitt **Überwachung** des Menüs eine Option **Diagnoseeinstellungen** vorhanden ist.


## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.


## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs
Ein Log Analytics-Arbeitsbereich in Azure Monitor sammelt und indiziert Protokolldaten aus einer Vielzahl von Quellen und ermöglicht eine erweiterte Analyse mithilfe einer leistungsfähigen Abfragesprache. Der Log Analytics-Arbeitsbereich muss vorhanden sein, bevor Sie eine Diagnoseeinstellung erstellen, um Daten an den Bereich zu senden. Sie können einen vorhandenen Arbeitsbereich in Ihrem Azure-Abonnement verwenden oder mithilfe der folgenden Schritte einen neuen erstellen. 

> [!NOTE]
> Sie können in Log Analytics-Arbeitsbereichen im Menü **Azure Monitor** mit Daten arbeiten, aber die Erstellung und Verwaltung der Arbeitsbereiche erfolgt im Menü **Log Analytics-Arbeitsbereiche**.

1. Wählen Sie unter **Alle Dienste** die Option **Log Analytics-Arbeitsbereich** aus.
2. Klicken Sie oben auf dem Bildschirm auf **Hinzufügen**, und geben Sie die folgenden Informationen zum Arbeitsbereich an:
   - **Log Analytics-Arbeitsbereich**: Der Name des neuen Arbeitsbereichs. Dieser Name muss in allen Azure Monitor-Abonnements global eindeutig sein.
   - **Abonnement**: Wählen Sie das Abonnement aus, in dem der Arbeitsbereich gespeichert werden soll. Hierbei muss es sich nicht um dasselbe Abonnement handeln, in dem sich die überwachte Ressource befindet.
   - **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder klicken Sie auf **Neu erstellen**, um eine zu erstellen. Hierbei muss es sich nicht um dieselbe Ressourcengruppe handeln, in der sich die überwachte Ressource befindet.
   - **Standort**: Wählen Sie eine Azure-Region aus, oder erstellen Sie einen neuen Standort. Hierbei muss es sich nicht um denselben Standort handeln, in dem sich die überwachte Ressource befindet.
   - **Tarif:** Wählen Sie *Nutzungsbasierte Zahlung* aus. Sie können den Tarif später ändern. Klicken Sie auf den Link **Log Analytics-Preise**, um mehr über die verschiedenen Tarife zu erfahren.

    ![Neuer Arbeitsbereich](media/tutorial-resource-logs/new-workspace.png)

3. Klicken Sie auf **OK**, um den Arbeitsbereich zu erstellen.

## <a name="create-a-diagnostic-setting"></a>Erstellen einer Diagnoseeinstellung
[Diagnoseeinstellungen](../essentials/diagnostic-settings.md) definieren, wohin Ressourcenprotokolle für eine bestimmte Ressource gesendet werden sollen. Eine einzelne Diagnoseeinstellung kann mehrere [Ziele](../essentials/diagnostic-settings.md#destinations) enthalten. In diesem Tutorial verwenden wir nur einen Log Analytics-Arbeitsbereich.

1. Wählen Sie im Menü Ihrer Ressource im Abschnitt **Überwachung** die Option **Diagnoseeinstellungen** aus.
2. Die Meldung „Es sind keine Diagnoseeinstellungen definiert.“ wird angezeigt. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

    ![Diagnoseeinstellungen](media/tutorial-resource-logs/diagnostic-settings.png)

3. Jede Diagnoseeinstellung besteht aus drei Teilen:
 
   - **Name**: Dieser Teil hat keine Auswirkungen und dient nur zur Beschreibung.
   - **Ziele:** Ein oder mehrere Ziele zum Senden der Protokolle. Für alle Azure-Dienste gelten dieselben drei möglichen Ziele. Jede Diagnoseeinstellung kann ein oder mehrere Ziele definieren, aber nicht mehr als ein Ziel eines bestimmten Typs. 
   - **Kategorien**: Kategorien der Protokolle, die an die einzelnen Ziele gesendet werden können. Die Kategorien unterscheiden sich je nach Azure-Dienst.

4. Klicken Sie auf **An Log Analytics-Arbeitsbereich senden**, und wählen Sie dann den erstellten Arbeitsbereich aus.
5. Wählen Sie die Kategorien aus, für die Sie Daten erfassen möchten. Eine Definition der verfügbaren Kategorien finden Sie in der Dokumentation jedes Diensts.

    ![Diagnoseeinstellung](media/tutorial-resource-logs/diagnostic-setting.png)

6. Klicken Sie auf **Speichern**, um die Diagnoseeinstellungen zu speichern.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Verwenden einer Protokollabfrage zum Abrufen von Protokollen
Die Daten werden mithilfe einer in Kusto Query Language (KQL) geschriebenen Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Erkenntnisse und Lösungen in Azure Monitor stellen Protokollabfragen bereit, um Daten aus einem bestimmten Dienst abzurufen, aber Sie können auch im Azure-Portal über Log Analytics direkt mit Protokollabfragen und deren Ergebnissen arbeiten. 

1. Wählen Sie im Menü Ihrer Ressource im Abschnitt **Überwachung** die Option **Protokolle** aus.
2. Log Analytics wird mit einem leeren Abfragefenster geöffnet, der Bereich ist auf Ihre Ressource festgelegt. Abfragen geben nur Datensätze aus dieser Ressource zurück.

    > [!NOTE]
    > Wenn Sie Protokolle über das Azure Monitor-Menü öffnen, ist der Bereich nicht auf den Log Analytics-Arbeitsbereich festgelegt. In diesem Fall werden Abfragen für alle Datensätze im Arbeitsbereich ausgeführt.
   
    ![Screenshot: Option „Protokolle“ für eine Logik-App, neue Abfrage mit hervorgehobenem App-Namen](media/tutorial-resource-logs/logs.png)

4. Der im Beispiel gezeigte Dienst schreibt Ressourcenprotokolle in die Tabelle **AzureDiagnostics**, andere Dienste schreiben ihre Ergebnisse möglicherweise in andere Tabellen. Ausführliche Informationen zu den Tabellen, die von den einzelnen Azure-Diensten verwendet werden, finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Ressourcenprotokolle](../essentials/resource-logs-schema.md).

    > [!NOTE]
    > Mehrere Dienste schreiben Ressourcenprotokolle in die Tabelle „AzureDiagnostics“. Wenn Sie Log Analytics über das Azure Monitor-Menü starten, müssen Sie eine `where`-Anweisung mit der Spalte `ResourceProvider` hinzufügen, um den gewünschten Dienst anzugeben. Wenn Sie Log Analytics über das Menü einer Ressource starten, ist der Bereich auf Datensätze aus dieser Ressource festgelegt, und diese Spalte ist nicht erforderlich. Beispielabfragen finden Sie in der Dokumentation des Diensts.


5. Geben Sie eine Abfrage ein, und klicken Sie auf **Ausführen**, um die Ergebnisse anzuzeigen. 
6. Ein Tutorial zum Schreiben von Protokollabfragen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../log-query/get-started-queries.md).

    ![Protokollabfrage](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Nächste Schritte
Hier haben Sie gelernt, wie Sie Ressourcenprotokolle in einem Log Analytics-Arbeitsbereich sammeln. In einem weiteren Tutorial können Sie erfahren, wie Sie Protokollabfragen schreiben, um diese Daten zu analysieren.

> [!div class="nextstepaction"]
> [Erste Schritte mit Protokollabfragen in Azure Monitor](../log-query/get-started-queries.md)
