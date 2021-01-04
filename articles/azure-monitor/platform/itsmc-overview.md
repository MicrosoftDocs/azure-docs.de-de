---
title: ITSM-Connector in Log Analytics
description: Dieser Artikel bietet eine Übersicht über den ITSM-Connector (IT-Service-Management-Connector) sowie Informationen zur Verwendung dieser Lösung, um die ITSM-Arbeitselemente in Log Analytics zu überwachen und zu verwalten und um etwaige Probleme schnell zu lösen.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b4e35296a999070a6f536f4a52cfb7c3a1d4f42a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186489"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Verbinden von Azure mit ITSM-Tools mithilfe des ITSM-Connectors

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Der ITSM-Connector (IT-Service-Management) ermöglicht Ihnen, eine Verbindung zwischen Azure und einem unterstützten ITSM-Produkt oder -Dienst herzustellen.

Azure-Dienste wie Azure Log Analytics und Azure Monitor stellen Tools zur Verfügung, mit denen Sie Probleme mit Ihren Azure- und Nicht-Azure-Ressourcen erkennen, analysieren und beheben können. Die Arbeitselemente, die sich auf ein Problem beziehen, befinden sich jedoch typischerweise in einem ITSM-Produkt oder -Dienst. Der ITSM-Connector bietet eine bidirektionale Verbindung zwischen Azure und ITSM-Tools, sodass Sie Probleme schneller lösen können.

Der ITSM-Connector unterstützt Verbindungen mit den folgenden ITSM-Tools:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Ab dem 1. Oktober 2020 werden ITSM-Integrationen von Cherwell und Provance in Azure-Warnungen für neue Kunden nicht mehr aktiviert. Neue ITSM-Verbindungen werden nicht unterstützt. Bestehende ITSM-Verbindungen werden unterstützt.

Mit dem ITSMC können Sie folgende Aktionen ausführen:

-  Sie können Arbeitselemente im ITSM-Tool basierend auf Ihren Azure-Warnungen (Metrikwarnungen, Aktivitätsprotokollwarnungen und Log Analytics-Warnungen) erstellen.
-  Optional können Sie Incident- und Änderungsanforderungsdaten aus Ihrem ITSM-Tool mit einem Azure Log Analytics-Arbeitsbereich synchronisieren.

Informationen zu rechtlichen und Datenschutzbestimmungen finden Sie in der [Datenschutzerklärung von Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Damit Sie den ITSM-Connector verwenden können, führen Sie vorab die folgenden Schritte aus:

1.  [Fügen Sie den ITSM-Connector hinzu.](#add-it-service-management-connector)
2.  [Erstellen Sie eine ITSM-Verbindung.](#create-an-itsm-connection)
3.  [Verwenden Sie die Verbindung.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>Hinzufügen des ITSM-Connectors

Bevor Sie eine Verbindung herstellen können, müssen Sie den ITSM-Connector hinzufügen.

1. Wählen Sie im Azure-Portal **Ressource erstellen** aus:

   ![Screenshot des Menüelemente „Ressource erstellen“](media/itsmc-overview/azure-add-new-resource.png)

2. Suchen Sie im Azure Marketplace nach **ITSM-Connector**. Wählen Sie **Erstellen** aus:

   ![Screenshot der Schaltfläche „Erstellen“ im Azure Marketplace](media/itsmc-overview/add-itsmc-solution.png)

3. Wählen Sie im Abschnitt **OMS-Arbeitsbereich** den Azure Log Analytics-Arbeitsbereich aus, in dem Sie den ITSM-Connector installieren möchten.
   >[!NOTE]
   > * Im Rahmen der laufenden Umstellung von der Microsoft Operations Management Suite (OMS) auf Azure Monitor werden OMS-Arbeitsbereiche nun als *Log Analytics-Arbeitsbereiche* bezeichnet.
   > * Der ITSM-Connector kann nur in Log Analytics-Arbeitsbereichen in den folgenden Regionen installiert werden: „USA, Osten“, „USA, Westen 2“, „USA, Süden-Mitte“, „USA, Westen-Mitte“, „US Gov Arizona“, „US Gov Virginia“, „Kanada, Mitte“, „Europa, Westen“, „Vereinigtes Königreich, Süden“, „Asien, Südosten“, „Japan, Osten“, „Indien, Mitte“ und „Australien, Südosten“.


4. Wählen Sie im Abschnitt **Log Analytics-Arbeitsbereich** die Ressourcengruppe aus, in der Sie die Ressource für den ITSM-Connector erstellen möchten:

   ![Screenshot des Abschnitts „Log Analytics-Arbeitsbereich“](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Im Rahmen der laufenden Umstellung von der Microsoft Operations Management Suite (OMS) auf Azure Monitor werden OMS-Arbeitsbereiche nun als *Log Analytics-Arbeitsbereiche* bezeichnet.

5. Klicken Sie auf **OK**.

Nachdem die Ressource für den ITSM-Connector bereitgestellt wurde, wird rechts oben im Fenster eine Benachrichtigung angezeigt.


## <a name="create-an-itsm-connection"></a>Erstellen einer ITSM-Verbindung

Nachdem Sie den ITSM-Connector installiert haben, können Sie eine Verbindung herstellen.

Um eine Verbindung herzustellen, müssen Sie Ihr ITSM-Tool vorbereiten, damit die Verbindung vom ITSM-Connector aus möglich ist.  

Wählen Sie basierend auf dem ITSM-Produkt, mit dem Sie eine Verbindung herstellen, einen der folgenden Links aus, um Anweisungen zu erhalten:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Nachdem Sie Ihre ITSM-Tools vorbereitet haben, führen Sie diese Schritte aus, um eine Verbindung herzustellen:

1. Suchen Sie in **Alle Ressourcen** nach **ServiceDesk(*IhrArbeitsbereichsname*)** :

   ![Screenshot der zuletzt verwendeten Ressourcen im Azure-Portal](media/itsmc-overview/itsm-connections.png)

1. Wählen Sie im linken Bereich unter **Arbeitsbereichsdatenquellen** die Option **ITSM-Verbindungen** aus:

   ![Screenshot des Menüelements „ITSM-Verbindungen“](media/itsmc-overview/add-new-itsm-connection.png)
   Diese Seite zeigt die Liste der Verbindungen.
1. Wählen Sie **Verbindung hinzufügen** aus.

4. Legen Sie die Verbindungseinstellungen wie in [Verbinden von ITSM-Produkten/-Diensten mit dem ITSM-Connector](./itsmc-connections.md) beschrieben fest.

   > [!NOTE]
   >
   > Standardmäßig aktualisiert der ITSM-Connector die Konfigurationsdaten der Verbindung einmal alle 24 Stunden. Um die Daten Ihrer Verbindung bei von Ihnen vorgenommenen Änderungen oder Vorlagenupdates sofort zu aktualisieren, wählen Sie auf dem Blatt Ihrer Verbindung die Schaltfläche **Synchronisieren** aus:
   >
   > ![Screenshot der Schaltfläche „Synchronisieren“ auf dem Verbindungsblatt](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>Verwenden des ITSM-Connectors
   Sie können mit dem ITSM-Connector Arbeitselemente aus Azure-Warnungen, Log Analytics-Warnungen und Log Analytics-Protokolldatensätzen erstellen.

## <a name="template-definitions"></a>Vorlagendefinitionen
   Hierbei handelt es sich um Typen von Arbeitselementen, die vom ITSM-Tool definierte Vorlagen verwenden können.
Mithilfe von Vorlagen können Sie Felder definieren, die anhand von im Rahmen der Aktionsgruppe definierten festgelegten Werten automatisch aufgefüllt werden. Sie definieren Vorlagen im ITSM-Tool. Sie können definieren, welche Vorlage Sie als Teil der Aktionsgruppendefinition verwenden möchten.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Erstellen von ITSM-Arbeitselementen aus Azure-Warnungen

Nachdem Sie die ITSM-Verbindung hergestellt haben, können Sie in Ihrem ITSM-Tool Arbeitselemente basierend auf Azure-Warnungen erstellen. Sie verwenden für das Erstellen der Arbeitselemente die ITSM-Aktion in Aktionsgruppen.

Die wiederverwendbaren Aktionsgruppen bieten Ihnen die Möglichkeit, modular Aktionen für Ihre Azure-Warnungen auszulösen. Sie können Aktionsgruppen im Azure-Portal mit Metrikwarnungen, Aktivitätsprotokollwarnungen und Azure Log Analytics-Warnungen verwenden.

> [!NOTE]
> Nachdem Sie die ITSM-Verbindung hergestellt haben, müssen Sie 30 Minuten warten, bis der Synchronisierungsvorgang abgeschlossen ist.
> 

Verwenden Sie das folgende Verfahren, um ein Arbeitselement zu erstellen:

1. Wählen Sie im Azure-Portal **Warnungen** aus.
2. Wählen Sie im Menü oben auf dem Bildschirm **Aktionen verwalten** aus:

    ![Screenshot des Menüelements „Aktionen verwalten“](media/itsmc-overview/action-groups-selection-big.png)

   Das Fenster **Aktionsgruppe erstellen** wird angezeigt.

3. Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, in dem bzw. der Sie die Aktionsgruppe erstellen möchten. Geben Sie einen **Aktionsgruppennamen** und einen **Anzeigenamen** für Ihre Aktionsgruppe an. Klicken Sie auf **Weiter: Benachrichtigungen**.

    ![Screenshot des Fensters „Aktionsgruppe erstellen“](media/itsmc-overview/action-groups-details.png)

4. Wählen Sie in der Benachrichtigungsliste Folgendes aus: **Weiter: Aktionen**.
5. Wählen Sie in der Aktionsliste in der Liste **Aktionstyp** den Eintrag **ITSM** aus. Geben Sie einen **Namen** für die Aktion an. Wählen Sie die Stiftschaltfläche aus, über die Sie die **Details bearbeiten** können.
6. Wählen Sie in der Liste **Abonnement** das Abonnement aus, in dem sich Ihr Log Analytics-Arbeitsbereich befindet. Wählen Sie in der Liste **Verbindung** den Namen Ihres ITSM-Connectors aus. Auf diesen folgt der Name Ihres Arbeitsbereichs. Beispiel: „MeinITSM-Connector(MeinArbeitsbereich)“.

7. Wählen Sie einen Typ für das **Arbeitselement** aus.

8. Wenn Sie vordefinierte Felder mit festen Werten ausfüllen möchten, wählen Sie **Benutzerdefinierte Vorlage verwenden** aus. Wählen Sie andernfalls in der Liste **Vorlagen** eine vorhandene [Vorlage](#template-definitions) aus, und geben Sie die festen Werte in den Vorlagenfeldern ein.

9. Durch Aktivieren von **Einzelne Arbeitselemente für jedes Konfigurationselement erstellen** erhält jedes Konfigurationselement ein eigenes Arbeitselement. Pro Konfigurationselement gibt es ein Arbeitselement. Die Aktualisierung erfolgt entsprechend den erstellten Warnungen.

   * Bei Auswahl von „Incident“ oder „Warnung“ in der Dropdownliste für das Arbeitselement: Wenn Sie das Kontrollkästchen **Einzelne Arbeitselemente für jedes Konfigurationselement erstellen** deaktivieren, wird bei jeder Warnung ein neues Arbeitselement erstellt. Für jedes Konfigurationselement können mehrere Warnungen verwendet werden.

   ![Screenshot des Fensters „ITSM-Ticket“](media/itsmc-overview/itsm-action-configuration.png)
   
   * Bei Auswahl von „Ereignis“ in der Dropdownliste für das Arbeitselement: Wenn Sie das Optionsfeld **Einzelne Arbeitselemente für jeden Protokolleintrag erstellen** aktivieren, wird bei jeder Warnung ein neues Arbeitselement erstellt. Durch Aktivieren des Optionsfelds **Einzelne Arbeitselemente für jedes Konfigurationselement erstellen** erhält jedes Konfigurationselement ein eigenes Arbeitselement.
   ![Screenshot des Fensters „ITSM-Ticket“](media/itsmc-overview/itsm-action-configuration-event.png)

10. Klicken Sie auf **OK**.

Verwenden Sie beim Erstellen oder Bearbeiten einer Azure-Warnungsregel eine Aktionsgruppe mit einer ITSM-Aktion. Beim Auslösen der Warnung wird das Arbeitselement im ITSM-Tool erstellt bzw. aktualisiert.

> [!NOTE]
>
>- Informationen zu den Preisen für ITSM-Aktionen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/) für Aktionsgruppen.
>
>
>- Das Feld für Kurzbeschreibungen in der Warnungsregeldefinition ist auf 40 Zeichen begrenzt, wenn Sie es über eine ITSM-Aktion senden.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisieren und Analysieren der Incident- und Änderungsanforderungsdaten

Basierend auf Ihrer Konfiguration, die Sie beim Einrichten einer Verbindung vorgenommenen haben, kann der ITSM-Connector bis zu 120 Tage an Incident- und Änderungsanforderungsdaten synchronisieren. Das Schema für den Protokolldatensatz dieser Daten finden Sie im [nächsten Abschnitt](#additional-information) dieses Artikels.

Sie können die Incident- und die Änderungsanforderungsdaten über das Dashboard des ITSM-Connectors visualisieren:

![Screenshot des Dashboards des ITSM-Connectors](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Das Dashboard bietet darüber hinaus Informationen zum Connectorstatus, die als Ausgangspunkt bei der Analyse von Verbindungsproblemen verwendet werden können.

Zudem lassen sich die mit den betroffenen Computern synchronisierten Incidents innerhalb der Dienstzuordnungslösung visuell darstellen.

Service Map ermittelt automatisch die Anwendungskomponenten auf Windows- und Linux-Systemen und stellt die Kommunikation zwischen Diensten dar. In dieser Lösung können Sie die Server nach ihrer Funktion anzeigen: als verbundene Systeme, die wichtige Dienste bereitstellen. Die Dienstzuordnungslösung zeigt Verbindungen zwischen Servern, Prozessen und Ports über die gesamte TCP-Verbindungsarchitektur an. Außer der Installation eines Agents ist keine Konfiguration erforderlich. Weitere Informationen finden Sie unter [Verwenden der Dienstzuordnung](../insights/service-map.md).

Wenn Sie die Dienstzuordnungslösung verwenden, können Sie die in ITSM-Lösungen erstellten Service Desk-Elemente wie im Folgenden gezeigt anzeigen:

![Screenshot des Log Analytics-Bildschirms](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


## <a name="additional-information"></a>Zusätzliche Informationen

### <a name="data-synced-from-your-itsm-product"></a>Aus dem ITSM-Produkt synchronisierte Daten
Incidents und Änderungsanforderungen aus dem ITSM-Produkt werden mit Ihrem Log Analytics-Arbeitsbereich basierend auf der Verbindungskonfiguration synchronisiert.

Der folgende Abschnitt enthält Beispiele für Daten, die vom ITSM-Connector gesammelt werden.

Die Felder in **ServiceDesk_CL** variieren abhängig vom Arbeitselementtyp, den Sie in Log Analytics importieren. Im Folgenden finden Sie eine Liste der Felder für zwei Arbeitselementtypen:

**Arbeitselement**: **Incidents**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- Service Desk-ID
- State
- Dringlichkeit
- Auswirkung
- Priority
- Eskalation
- Erstellt von
- Gelöst von
- Geschlossen von
- `Source`
- Zugewiesen zu
- Category
- Titel
- BESCHREIBUNG
- Erstellt am
- Geschlossen am
- Gelöst am
- Datum der letzten Änderung
- Computer


**Arbeitselement**: **Änderungsanforderungen**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk-ID
- Erstellt von
- Geschlossen von
- `Source`
- Zugewiesen zu
- Titel
- type
- Category
- State
- Eskalation
- Konfliktstatus
- Dringlichkeit
- Priority
- Risiko
- Auswirkung
- Zugewiesen zu
- Erstellt am
- Geschlossen am
- Datum der letzten Änderung
- Anforderungsdatum
- Geplantes Startdatum
- Geplantes Enddatum
- Startdatum der Arbeit
- Enddatum der Arbeit
- BESCHREIBUNG
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Ausgabedaten für einen ServiceNow-Incident

| Log Analytics-Feld | ServiceNow-Feld |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Dringlichkeit |
| Impact_s |Auswirkung|
| Priority_s | Priority |
| CreatedBy_s | Geöffnet von |
| ResolvedBy_s | Gelöst von|
| ClosedBy_s  | Geschlossen von |
| Source_s| Kontakttyp |
| AssignedTo_s | Zugewiesen zu  |
| Category_s | Category |
| Title_s|  Kurze Beschreibung |
| Description_s|  Notizen |
| CreatedDate_t|  Geöffnet |
| ClosedDate_t| closed|
| ResolvedDate_t|Gelöst|
| Computer  | Konfigurationselement |

## <a name="output-data-for-a-servicenow-change-request"></a>Ausgabedaten für eine ServiceNow-Änderungsanforderung

| Log Analytics | ServiceNow-Feld |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Angefordert von |
| ClosedBy_s | Geschlossen von |
| AssignedTo_s | Zugewiesen zu  |
| Title_s|  Kurze Beschreibung |
| Type_s|  type |
| Category_s|  Category |
| CRState_s|  State|
| Urgency_s|  Dringlichkeit |
| Priority_s| Priority|
| Risk_s| Risiko|
| Impact_s| Auswirkung|
| RequestedDate_t  | Datum der Anforderung |
| ClosedDate_t | Geschlossen am |
| PlannedStartDate_t  |     Geplantes Startdatum |
| PlannedEndDate_t  |   Geplantes Enddatum |
| WorkStartDate_t  | Tatsächliches Startdatum |
| WorkEndDate_t | Tatsächliches Enddatum|
| Description_s | BESCHREIBUNG |
| Computer  | Konfigurationselement |


## <a name="troubleshoot-itsm-connections"></a>Problembehandlung bei ITSM-Verbindungen
- Wenn bei der Verbindung auf der Benutzeroberfläche der verbundenen Quelle ein Fehler auftritt und die Fehlermeldung **Fehler beim Speichern der Verbindung** angezeigt wird, gehen Sie folgendermaßen vor:
   - Bei ServiceNow-, Cherwell- und Provance-Verbindungen:  
     - Stellen Sie sicher, dass Sie Benutzername, Kennwort, Client-ID und geheimen Clientschlüssel für jede der Verbindungen richtig eingegeben haben.  
     - Stellen Sie sicher, dass Sie über ausreichende Berechtigungen für das entsprechende ITSM-Produkt verfügen, um die Verbindung herzustellen.  
   - Bei Service Manager-Verbindungen:  
     - Stellen Sie sicher, dass die Web-App erfolgreich bereitgestellt und die Hybridverbindung erstellt wird. Um zu überprüfen, ob die Verbindung mit dem lokalen Service Manager-Computer erfolgreich hergestellt wird, navigieren Sie zur Web-App-URL, wie in der Dokumentation zum Herstellen der [Hybridverbindung](./itsmc-connections.md#configure-the-hybrid-connection) erläutert.  

- Wenn Daten von ServiceNow nicht in Log Analytics synchronisiert werden, stellen Sie sicher, dass sich die ServiceNow-Instanz nicht im Energiesparmodus befindet. ServiceNow-Entwicklungsinstanzen wechseln manchmal nach längerem Leerlauf in den Energiesparmodus. Wenn dies nicht der Fall ist, melden Sie das Problem.
- Wenn Log Analytics-Warnungen ausgelöst werden, aber keine Arbeitselemente im ITSM-Produkt oder keine Konfigurationselemente erstellt bzw. diese nicht mit Arbeitselementen verknüpft werden, nutzen Sie folgende Quellen. Dort finden Sie darüber hinaus allgemeine Informationen:
   -  ITSMC: Die Lösung zeigt eine Zusammenfassung der Verbindungen, Arbeitselemente, Computer usw. an. Wählen Sie die Kachel mit der Bezeichnung **Connectorstatus** aus. Dadurch wechseln Sie zur **Protokollsuche** mit der zugehörigen Abfrage. Weitere Informationen finden Sie in den Protokolldatensätzen mit dem `LogType_S` `ERROR`.
   - Seite **Protokollsuche**: Sie können die Fehler und zugehörigen Informationen direkt mithilfe der Abfrage `*ServiceDeskLog_CL*` anzeigen.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Beheben von Problemen bei der Service Manager-Web-App-Bereitstellung
-   Stellen Sie bei Problemen mit der Web-App-Bereitstellung sicher, dass Sie im angegebenen Abonnement über ausreichende Berechtigungen zum Erstellen/Bereitstellen von Ressourcen verfügen.
-   Wenn die Fehlermeldung **Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt** angezeigt wird, während Sie das [Skript](itsmc-service-manager-script.md) ausführen, vergewissern Sie sich, dass Sie im Abschnitt **Benutzerkonfiguration** gültige Werte eingegeben haben.
-   Wenn Sie den Service Bus-Relaynamespace nicht erstellen können, stellen Sie sicher, dass der erforderliche Ressourcenanbieter im Abonnement registriert ist. Wenn er nicht registriert ist, erstellen Sie den Service Bus-Relaynamespace manuell über das Azure-Portal. Sie können ihn auch beim [Erstellen der Hybridverbindung](./itsmc-connections.md#configure-the-hybrid-connection) über das Azure-Portal erstellen.


## <a name="contact-us"></a>Kontakt

Kontaktieren Sie uns bei Fragen oder Feedback zum ITSM-Connector über [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte
[Hinzufügen von ITSM-Produkten/-Diensten zum ITSM-Connector](./itsmc-connections.md)