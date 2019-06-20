---
title: Erste Schritte mit Microsoft Power BI-Arbeitsbereichssammlungen
description: Bei Power BI-Arbeitsbereichssammlungen handelt es sich um einen Azure-Dienst, mit dem Anwendungsentwickler eigenen Anwendungen interaktive Power BI-Berichte hinzufügen können.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: d1011a8fd8f181233be8e1fa27c3bfaea3d86141
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685170"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Erste Schritte mit Microsoft Power BI-Arbeitsbereichssammlungen

Bei **Power BI-Arbeitsbereichssammlungen** handelt es sich um einen Azure-Dienst, mit dem Anwendungsentwickler eigenen Anwendungen interaktive Power BI-Berichte hinzufügen können. **Power BI-Arbeitsbereichssammlungen** können mit vorhandenen Anwendungen ohne Umgestaltung oder Änderung der Benutzeranmeldung verwendet werden.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Wie Sie Ihre Daten zu Power BI Embedded migrieren, erfahren Sie unter [Migrieren von Inhalten aus Power BI Embedded-Arbeitsbereichssammlungen zu Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Ressourcen für **Microsoft Power BI-Arbeitsbereichssammlungen** werden über die [Azure Resource Manager-APIs](https://msdn.microsoft.com/library/mt712306.aspx) bereitgestellt. In diesem Fall handelt es sich bei der bereitgestellten Ressource um eine **Power BI-Arbeitsbereichssammlung**.

![Allgemeiner Ablauf von Power BI-Arbeitsbereichssammlungen](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Erstellen einer Arbeitsbereichssammlung

Eine **Arbeitsbereichssammlung** ist die Azure-Ressource der obersten Ebene und ein Container für den Inhalt, der in Ihre Anwendung eingebettet wird. Eine **Arbeitsbereichssammlung** kann auf zwei Arten erstellt werden:

* Manuell über das Azure-Portal
* Programmgesteuert über die Azure Resource Manager-APIs

Im Anschluss werden die Schritte zum Erstellen einer **Arbeitsbereichssammlung** über das Azure-Portal erläutert.

1. Öffnen Sie das **Azure-Portal**, und melden Sie sich an: [https://portal.azure.com](https://portal.azure.com).
2. Klicken Sie im oberen Bereich auf **+ Neu**.
   
   ![„+ Neu“ im Azure-Portal](media/get-started/create-workspace-1.png)
3. Wählen Sie unter **Daten + Analysen** die Option **Power BI-Arbeitsbereichssammlung** aus.
4. Wenn Sie bereits über ein Abonnement für Power BI-Arbeitsbereichssammlungen verfügen, klicken Sie am unteren Rand der Einstiegsmeldung auf **Arbeitsbereichssammlung erstellen**.

5. Geben Sie unter **Arbeitsbereichssammlung** die erforderlichen Informationen ein.
   
   ![Erstellung einer Arbeitsbereichssammlung](media/get-started/create-workspace-2.png)
1. Klicken Sie auf **Erstellen**.

Die Bereitstellung der **Arbeitsbereichssammlung** dauert einen Moment. Nach Abschluss des Vorgangs wird **Arbeitsbereichssammlung** angezeigt.

   ![Arbeitsbereichssammlung im Azure-Portal](media/get-started/create-workspace-3.png)

Die Ergebnisse der **Erstellung** enthalten die erforderlichen Informationen zum Aufrufen der APIs, mit denen Arbeitsbereiche erstellt und Inhalte dafür bereitgestellt werden.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Anzeigen von Power BI-API-Zugriffsschlüsseln

Zu den wichtigsten Informationen zum Aufrufen der Power BI-REST-APIs gehören die **Zugriffsschlüssel**. Sie dienen zum Generieren der **App-Token** , die zum Authentifizieren Ihrer API-Anforderungen verwendet werden. Klicken Sie zum Anzeigen der **Zugriffsschlüssel** unter **Einstellungen** auf **Zugriffsschlüssel**. Weitere Informationen zu **App-Token**finden Sie unter [Authenticating and authorizing with Power BI Workspace Collections](app-token-flow.md) (Authentifizieren und Autorisieren mit Power BI-Arbeitsbereichssammlungen).

   ![Zugriffsschlüssel in den Einstellungen für Arbeitsbereichssammlungen im Azure-Portal](media/get-started/access-keys.png)

Sie werden feststellen, dass Sie über zwei Schlüssel verfügen.

   ![Zwei Schlüssel innerhalb von Zugriffsschlüsseln](media/get-started/access-keys-2.png)

Kopieren Sie diese Schlüssel, und speichern Sie sie sicher in Ihrer Anwendung. Behandeln Sie diese Schlüssel wie ein Kennwort, da sie den Zugriff auf den gesamten Inhalt Ihrer **Arbeitsbereichssammlung** ermöglichen.

Es sind zwar zwei Schlüssel aufgeführt, aber es wird nur jeweils ein Schlüssel benötigt. Der zweite Schlüssel wird bereitgestellt, damit Sie Schlüssel regelmäßig neu generieren können, ohne den Zugriff auf den Dienst zu unterbrechen.

Nachdem Sie nun über eine Instanz von Power BI für Ihre Anwendung und **Zugriffsschlüssel**verfügen, können Sie einen Bericht in Ihre eigene App importieren. Bevor das Importieren eines Berichts erklärt wird, wird im nächsten Abschnitt beschrieben, wie Sie Power BI-Datasets und -Berichte zum Einbetten in eine App erstellen.

## <a name="working-with-workspaces"></a>Verwenden von Arbeitsbereichen

Nach der Erstellung Ihrer Arbeitsbereichssammlung müssen Sie einen Arbeitsbereich für Ihre Berichte und Datasets erstellen. Verwenden Sie zum Erstellen eines Arbeitsbereichs die [REST-API „Post Workspace“](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Erstellen von Power BI-Datasets und -Berichten zum Einbetten in eine App mithilfe von Power BI Desktop

Nachdem Sie nun eine Instanz von Power BI für Ihre Anwendung erstellt haben und über **Zugriffsschlüssel** verfügen, müssen Sie die einzubettenden Power BI-Datasets und -Berichte erstellen. Sie können Datasets und Berichte mit **Power BI Desktop**erstellen. Sie können [Power BI Desktop kostenlos herunterladen](https://go.microsoft.com/fwlink/?LinkId=521662). Alternativ können Sie als schnellen Einstieg das [PBIX-Beispiel „Retail Analysis“](https://go.microsoft.com/fwlink/?LinkID=780547)herunterladen.

> [!NOTE]
> Weitere Informationen zur Verwendung von **Power BI Desktop** finden Sie unter [Getting Started with Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop) (Erste Schritte mit Power BI Desktop).

Mit **Power BI Desktop** stellen Sie eine Verbindung mit der Datenquelle her, indem Sie eine Kopie der Daten in **Power BI Desktop** importieren oder per **DirectQuery** eine direkte Verbindung mit der Datenquelle herstellen.

Im Folgenden werden die Unterschiede zwischen der Verwendung des **Importvorgangs** und der Verwendung von **DirectQuery** aufgeführt.

| Importieren | DirectQuery |
| --- | --- |
| Tabellen, Spalten *und Daten* werden in **Power BI Desktop** importiert oder kopiert. Bei der Arbeit mit Visualisierungen fragt **Power BI Desktop** eine Kopie der Daten ab. Um Änderungen anzuzeigen, die ggf. an den zugrunde liegenden Daten vorgenommen wurden, müssen Sie ein Dataset aktualisieren bzw. ein vollständiges aktuelles Dataset importieren. |Nur *Tabellen und Spalten* werden in **Power BI Desktop** importiert oder kopiert. Bei der Arbeit mit Visualisierungen fragt **Power BI Desktop** die zugrunde liegende Datenquelle ab. Dies bedeutet, dass die angezeigten Daten immer aktuell sind. |

Weitere Informationen zum Herstellen einer Verbindung mit einer Datenquelle finden Sie unter [Herstellen einer Verbindung mit einer Datenquelle](connect-datasource.md).

Wenn Sie Ihre Arbeit in **Power BI Desktop** speichern, wird eine PBIX-Datei erstellt. Diese Datei enthält den Bericht. Wenn Sie Daten importieren, enthält die PBIX-Datei das vollständige Dataset. Bei Verwendung von **DirectQuery** enthält die PBIX-Datei nur ein Datasetschema. Sie stellen die PBIX-Datei programmgesteuert in Ihrem Arbeitsbereich bereit, indem Sie die [Power BI-API für den Import](https://msdn.microsoft.com/library/mt711504.aspx) verwenden.

> [!NOTE]
> **Power BI-Arbeitsbereichssammlungen** verfügen über weitere APIs zum Ändern des Servers und der Datenbank, auf den bzw. auf die Ihr Dataset verweist, sowie zum Festlegen von Dienstkonto-Anmeldeinformationen, die vom Dataset zum Herstellen der Verbindung mit Ihrer Datenbank verwendet werden. Weitere Informationen finden Sie unter [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) und [Patch GatewayDatasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Erstellen von Power BI-Datasets und -Berichten mithilfe von APIs

### <a name="datasets"></a>Datasets

Sie können Datasets in Power BI-Arbeitsbereichssammlungen mithilfe der REST-API erstellen. Anschließend können Sie Daten per Push in das Dataset übertragen. So können Sie ohne Power BI Desktop mit Daten arbeiten. Weitere Informationen finden Sie unter [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx).

### <a name="reports"></a>Berichte

Mithilfe der JavaScript-API können Sie einen Bericht aus einem Dataset direkt in der Anwendung erstellen. Weitere Informationen finden Sie unter [Create a new report from a dataset in Power BI Workspace Collections](create-report-from-dataset.md) (Erstellen eines neuen Berichts auf der Grundlage eines Datasets in Power BI-Arbeitsbereichssammlungen).

## <a name="see-also"></a>Siehe auch

[Erste Schritte mit dem Beispiel](get-started-sample.md)  
[Authenticating and authorizing in Power BI Workspace Collections (Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen)](app-token-flow.md)  
[Einbetten eines Berichts](embed-report.md)  
[Create a new report from a dataset in Power BI Workspace Collections](create-report-from-dataset.md) (Erstellen eines neuen Berichts auf der Grundlage eines Datasets in Power BI-Arbeitsbereichssammlungen)
[Save reports in Power BI Workspace Collections](save-reports.md) (Speichern von Berichten in Power BI-Arbeitsbereichssammlungen).  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)

