---
title: Installieren von Azure Data Lake Tools für Visual Studio
description: In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Tools für Visual Studio installieren.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: cb0183ae229c328588a31c2c0549e7e93fd19b78
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019003"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installieren von Data Lake-Tools für Visual Studio

Erfahren Sie, wie Sie Azure Data Lake Analytics-Konten mithilfe von Visual Studio erstellen. Sie können Aufträge in [U-SQL](data-lake-analytics-u-sql-get-started.md) definieren und Aufträge an den Data Lake Analytics-Dienst übermitteln. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Visual Studio:** alle Editionen außer Express werden unterstützt.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK für .NET**, Version 2.7.1 oder höher. Führen Sie die Installation mit dem [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx)durch.
* Ein **Data Lake Analytics**-Konto. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Installieren von Azure Data Lake Tools für Visual Studio 2017 oder Visual Studio 2019

Azure Data Lake Tools für Visual Studio wird in Visual Studio 2017 ab Version 15.3 unterstützt. Das Tool ist Teil der Workloads **Datenspeicherung und -verarbeitung** und **Azure-Entwicklung**. Aktivieren Sie eine dieser beiden Workloads im Rahmen Ihrer Visual Studio-Installation.

Aktivieren Sie die Workload **Datenspeicherung und -verarbeitung** wie gezeigt:

![Aktivieren der Workload „Datenspeicherung und -verarbeitung“](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Aktivieren Sie die Workload **Azure-Entwicklung** wie gezeigt:

![Auswählen der Workload „Azure-Entwicklung“](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installieren von Azure Data Lake Tools für Visual Studio 2013 und 2015

Laden Sie [Microsoft Azure Data Lake- und Stream Analytics-Tools für Visual Studio](https://aka.ms/adltoolsvs) herunter, und installieren Sie sie. Nach der Installation weist Visual Studio die folgenden Änderungen auf:

* Der Knoten **Server-Explorer** > **Azure** enthält den Knoten **Data Lake Analytics**.
* Das Menü **Extras** enthält die Option **Data Lake**.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Protokollieren von Diagnoseinformationen finden Sie unter [Zugreifen auf Diagnoseprotokolle für Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Weitere Informationen zur Verwendung der Scheitelpunktausführungsansicht finden Sie unter [Verwenden der Scheitelpunktausführungsansicht in Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
