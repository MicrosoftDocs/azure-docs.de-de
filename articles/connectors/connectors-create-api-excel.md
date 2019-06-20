---
title: Herstellen einer Verbindung mit Excel Online – Azure Logic Apps
description: Verwalten von Daten mit Excel Online-REST-APIs und Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 28739ad65462acc9f2d2ed7db1e9ed14d19f032c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311963"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Verwalten von Excel Online-Daten mit Azure Logic Apps

Mit Azure Logic Apps und dem Excel Online-Connector können Sie automatisierte Aufgaben und Workflows basierend auf Ihren Daten in Excel Online for Business oder OneDrive erstellen. Dieser Connector bietet Aktionen, die Sie beim Verwalten Ihrer Daten und Tabellen unterstützen. Beispiele:

* Sie können neue Arbeitsblätter und Tabellen erstellen.
* Sie können Arbeitsblätter, Tabellen und Zeilen abrufen und verwalten.
* Sie können einzelne Zeilen und Schlüsselspalten hinzufügen.

Sie können dann die Ausgaben dieser Aktionen mit Aktionen für andere Dienste verwenden. Wenn Sie beispielsweise eine Aktion verwenden, die jede Woche Arbeitsblätter erstellt, können Sie eine weitere Aktion verwenden, die über den Office 365 Outlook-Connector eine Bestätigungs-E-Mail sendet.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

> [!NOTE]
> Die Connectors für [Excel Online for Business](/connectors/excelonlinebusiness/) und [Excel Online for OneDrive](/connectors/excelonline/) funktionieren mit Azure Logic Apps und unterscheiden sich vom [Excel-Connector für PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Ein [Office 365-Konto](https://www.office.com/) für Ihr Geschäftskonto oder Ihr persönliches Microsoft-Konto.

  Ihre Excel-Daten können sich in einer durch Trennzeichen getrennten Datei (CSV-Datei) in einem Speicherordner befinden, beispielsweise in OneDrive. 
  Sie können diese CSV-Datei auch mit dem [Flatfileconnector](../logic-apps/logic-apps-enterprise-integration-flatfile.md)verwenden.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihre Excel Online-Daten zugreifen möchten. Dieser Connector stellt nur Aktionen bereit, daher müssen Sie zum Starten Ihrer Logik-App einen separaten Trigger auswählen, z.B. einen **Wiederholungstrigger**.

## <a name="add-excel-action"></a>Hinzufügen eine Excel-Aktion

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer, wenn sie nicht bereits geöffnet ist.

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus.

1. Geben Sie im Suchfeld den Begriff „excel“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Wenn Sie aufgefordert werden, sich bei Ihrem Office 365-Konto anzumelden, wählen Sie **Anmelden** aus.

   Ihre Anmeldeinformationen autorisieren Ihre Logik-App zur Erstellung einer Verbindung mit Excel Online sowie zum Zugriff auf Ihre Daten.

1. Fahren Sie damit fort, die erforderlichen Informationen für die ausgewählte Aktion einzugeben und Ihren Logik-App-Workflow zu erstellen.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details wie Trigger, Aktionen und Limits, wie sie in der OpenAPI-Datei (ehemals Swagger) des Connectors beschrieben werden, finden Sie auf diesen Referenzseite des Connectors:

* [Excel Online for Business](/connectors/excelonlinebusiness/)
* [Excel Online for OneDrive](/connectors/excelonline/)

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
