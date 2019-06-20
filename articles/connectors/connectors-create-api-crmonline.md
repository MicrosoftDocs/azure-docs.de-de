---
title: Herstellen einer Verbindung mit Dynamics 365 – Azure Logic Apps
description: Erstellen und Verwalten von Datensätzen mit REST-APIs für Dynamics 365 (online) und mit Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: b81efba0ce860bea5fd68dd99ce52980e6816b7e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60313748"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Verwalten von Dynamics 365-Datensätzen in Azure Logic Apps

Mit Azure Logic Apps und dem Dynamics 365-Connector können Sie automatisierte Aufgaben und Workflows basierend auf Ihren Datensätzen in Dynamics 365 erstellen. Ihre Workflows können in Ihrem Dynamics 365-Konto Datensätze erstellen, Elemente aktualisieren, Datensätze zurückgeben und mehr. Sie können Aktionen in Ihre Logik-Apps integrieren, die Antworten von Dynamics 365 erhalten und die Ausgabe für andere Aktionen verfügbar machen. Wenn also beispielsweise ein Element in Dynamics 365 aktualisiert wird, können Sie über Office 365 eine E-Mail senden.

In diesem Artikel erfahren Sie, wie Sie eine Logik-App erstellen, die eine Aufgabe in Dynamics 365 erstellt, sobald in Dynamics 365 ein neuer Leaddatensatz erstellt wird.
Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Ein [Dynamics 365-Konto](https://dynamics.microsoft.com)

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr Dynamics 365-Konto zugreifen möchten. Um Ihre Logik-App mit einem Dynamics 365-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Dynamics 365-Trigger hinzufügen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Fügen Sie zunächst einen Dynamics 365-Trigger, der ausgelöst wird, wenn ein neuer Leaddatensatz in Dynamics 365 angezeigt wird.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre leere Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Suchfeld den Begriff „Dynamics 365“ als Filter ein. Wählen Sie für dieses Beispiel in der Triggerliste den folgenden Trigger aus: **When a record is created** (Wenn ein Datensatz erstellt wird)

   ![Trigger auswählen](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Melden Sie sich bei Dynamics 365 an, falls Sie dazu aufgefordert werden.

1. Geben Sie diese Triggerdetails an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name der Organisation** | Ja | Der Name der zu überwachenden Dynamics 365-Instanz Ihrer Organisation, z.B. „Contoso“ |
   | **Name der Entität** | Ja | Der Name der zu überwachenden Entität, z.B „Leads“ | 
   | **Frequency** | Ja | Die Zeiteinheit, die bei der Suche nach Aktualisierungen im Zusammenhang mit dem Trigger mit Intervallen verwendet werden soll |
   | **Intervall** | Ja | Die Anzahl der Sekunden, Minuten, Stunden, Tage, Wochen oder Monate, die bis zur nächsten Überprüfung vergehen sollen |
   ||| 

   ![Triggerdetails](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365-Aktion hinzufügen

Füge nun die Dynamics 365-Aktion hinzu, die einen Aufgabendatensatz für den neuen Leaddatensatz erstellt.

1. Wählen Sie unter Ihrem Trigger die Option **Neuer Schritt** aus.

1. Geben Sie im Suchfeld den Begriff „Dynamics 365“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Create a new record** (Neuen Datensatz erstellen)

   ![Aktion select](./media/connectors-create-api-crmonline/select-action.png)

1. Geben Sie die folgenden Aktionsdetails an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name der Organisation** | Ja | Die Dynamics 365-Instanz, in der Sie den Datensatz erstellen möchten, die nicht die gleiche Instanz in Ihrem Trigger sein muss, sondern in diesem Beispiel „Contoso“ ist. |
   | **Name der Entität** | Ja | Die Entität, in der Sie den Datensatz erstellen möchten, z.B. „Aufgaben“ |
   | | |

   ![Aktionsdetails](./media/connectors-create-api-crmonline/action-details.png)

1. Wenn das Feld **Betreff** in Ihrer Aktion angezeigt wird, klicken Sie in das Feld **Betreff**, damit die Liste der dynamischen Inhalte angezeigt wird. Wählen Sie in dieser Liste die Feldwerte, die in den Aufgabendatensatz bezüglich des neuen Leaddatensatzes integriert werden sollen:

   | Feld | BESCHREIBUNG |
   |-------|-------------|
   | **Letzter Name** | Der letzte Name aus dem Lead als der primäre Ansprechpartner im Datensatz |
   | **Thema** | Der beschreibende Name für den Lead im Datensatz |
   | | |

   ![Aufgabedatensatz-Details](./media/connectors-create-api-crmonline/create-record-details.png)

1. Wählen Sie in der Symbolleiste des Designers die Option **Speicher** für Ihre Logik-App. 

1. Wählen Sie in der Symbolleiste des Designers die Option **Ausführen**, um Ihre Logik-App manuell zu starten.

   ![Logik-App ausführen](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Erstellen Sie jetzt einen Leaddatensatz in Dynamics 365, damit Sie Ihren Logik-App-Workflow auslösen können.

## <a name="add-filter-or-query"></a>Filter oder Abfrage hinzufügen

Geben Sie an, wie Daten in einer Dynamics 365-Aktion gefiltert werden sollen, indem Sie in dieser Aktion **Erweiterte Optionen anzeigen** auswählen. Sie können dann einen Filter oder eine Reihenfolge nach Abfrage hinzufügen.
So können Sie beispielsweise eine Filterabfrage verwenden, um nur die aktive Konten abzurufen, und die Datensätze nach Kontoname sortieren. Führen Sie diese Aufgabe in folgenden Schritten aus:

1. Geben Sie unter **Filterabfrage** diese OData-Abfrage ein: `statuscode eq 1`

2. Wenn die Liste mit den dynamischen Inhalten angezeigt wird, wählen Sie unter **Ordnen nach** die Option **Kontoname**. 

   ![Filter und Reihenfolge angeben](./media/connectors-create-api-crmonline/advanced-options.png)

Weitere Informationen finden Sie in diesen Dynamics 365 Customer Engagement Web-API-System-Abfrageoptionen:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Bewährte Methoden für erweiterte Optionen

Wenn Sie in einer Aktion oder einem Trigger einen Wert für ein Feld angeben, muss der Datentyp des Wertes mit dem Feldtyp übereinstimmen, egal ob Sie den Wert manuell eingeben oder den Wert aus der Liste mit dem dynamischen Inhalt auswählen.

Diese Tabelle beschreibt einige der Feldtypen und die erforderlichen Datentypen für ihre Werte.

| Feldtyp | Erforderlicher Datentyp | BESCHREIBUNG | 
|------------|--------------------|-------------|
| Textfelder | Einzelne Textzeile | Diese Felder erfordern eine einzelne Textzeile oder dynamischen Inhalt, bei dem es sich um den Texttypen handelt. <p><p>*Beispielfelder*: **Beschreibung** und **Kategorie** | 
| Ganzzahlige Felder | Ganze Zahl | Einige Felder erfordern eine ganze Zahl oder dynamischen Inhalt, bei dem es sich um einen Ganzzahlentyp handelt. <p><p>*Beispielfelder*: **Prozent abgeschlossen** und **Dauer** | 
| Datumsfelder | Datum und Uhrzeit | Einige Felder erfordern ein Datum im Format MM/TT/JJJJ oder dynamischen Inhalt, bei dem es sich um einen Datumstyp handelt. <p><p>*Beispielfelder*: **Erstellt am**, **Startdatum**, **Tatsächlicher Start**, **Tatsächliches Ende** und **Fälligkeitsdatum** | 
| Felder, die sowohl eine Datensatz-ID als auch einen Suchtyp erfordern | Primary key (Primärschlüssel) | Einige Felder, die auf einen anderen Entitätsdatensatz verweisen, erfordern sowohl die Datensatz-ID als auch den Suchtyp. | 
||||

Hier sehen Sie Beispielfelder in Dynamics 365-Trigger und Aktionen, die um diese Feldtypen erweitert wurden, und die sowohl eine Datensatz-ID und den Suchtyp erfordern. Diese Anforderung bedeutet, dass die Werte, die Sie aus der Liste der dynamischen Inhalte auswählen, nicht funktionieren.

| Feld | BESCHREIBUNG |
|-------|-------------|
| **Besitzer** | Muss entweder eine gültige Benutzer-ID oder Teamdatensatz-ID sein |
| **Besitzertyp** | Muss entweder **systemusers** oder **teams** sein. |
| **Bezüglich** | Muss eine gültige Datensatz-ID sein, beispielsweise eine Konto- oder Kontaktdatensatz-ID |
| **Typ für „Bezüglich“** | Muss ein Suchtyp sein, wie z.B **accounts** oder **contacts** |
| **Kunde** | Muss eine gültige Datensatz-ID sein, beispielsweise eine Konto- oder Kontaktdatensatz-ID |
| **Kundentyp** | Muss ein Suchtyp sein, wie z.B **accounts** oder **contacts** |
|||

In diesem Beispiel wird mit der Aktion **Neuen Datensatz erstellen** ein neuer Aufgabendatensatz erstellt:

![Erstellen eines Aufgabendatensatzes mit Datensatz-IDs und Suchtypen](./media/connectors-create-api-crmonline/create-record-advanced.png)

Diese Aktion ordnet den Aufgabendatensatz einer bestimmten Benutzer-ID oder Teamdatensatz-ID basierend auf der Datensatz-ID im Feld **Besitzer** und dem Suchtyp im Feld **Besitzertyp** zu:

![Besitzerdatensatz-ID und Suchtyp](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Diese Aktion fügt auch einen Kontendatensatz hinzu, der der im Feld **Bezüglich** hinzugefügten Datensatz-ID und dem Suchtyp im Feld **Typ für „Bezüglich“** zugeordnet ist:

![Datensatz-ID für Feld „Bezüglich“ und Suchtyp](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Suchen der Datensatz-ID

Um eine Datensatz-ID zu suchen, gehen Sie folgendermaßen vor:

1. Öffnen Sie in Dynamics 365 einen Datensatz (beispielsweise einen Kontodatensatz).

2. Wählen Sie in der Aktionssymbolleiste einen der folgenden Schritte aus:

   * Wählen Sie **Ausklappen**. ![popout record](./media/connectors-create-api-crmonline/popout-record.png) 
   * Wählen Sie **LINK PER E-MAIL SENDEN**, sodass Sie die vollständige URL in Ihr Standard-E-Mail-Programm kopieren können.

   Die Datensatz-ID wird in der URL zwischen den Codierungszeichen `%7b` und `%7d` angezeigt:

   ![Suchen der Datensatz-ID](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Problembehandlung bei fehlerhaften Ausführungen

Um fehlerhafte Schritte in Ihrer Logik-App zu finden und zu überprüfen, können Sie den Ausführungsverlauf, den Status, die Eingaben, die Ausgaben und so weiter Ihrer Logik-App einsehen.

1. Wählen Sie im Azure-Portal im Hauptmenü Ihrer Logik-App **Übersicht** aus. Wählen Sie für weitere Informationen im Abschnitt **Ausführungsverlauf**, der alle Ausführungsstatus für Ihre Logik-App angezeigt, eine fehlerhafte Ausführung.

   ![Ausführungsstatus der Logik-App](./media/connectors-create-api-crmonline/run-history.png)

1. Erweitern Sie einen fehlerhaften Schritt, um weitere Details anzuzeigen.

   ![Erweitern eines fehlerhaften Schritts](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Überprüfen Sie die Schrittdetails, wie z.B. die Eingaben und Ausgaben. So können Sie die Ursache für den Fehler bestimmen.

   ![Fehlerhafter Schritt 2 Eingaben und Ausgaben](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Weitere Informationen zum Behandeln von Problemen mit Logik-Apps finden Sie unter [Diagnostizieren von Fehlern bei Logik-Apps](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Connector-Referenz

Technische Details wie Trigger, Aktionen und Limits, wie sie in der OpenAPI-Datei (ehemals Swagger) des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/dynamicscrmonline/).

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
