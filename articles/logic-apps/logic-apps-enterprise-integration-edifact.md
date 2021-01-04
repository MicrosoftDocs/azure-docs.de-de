---
title: EDIFACT-Nachrichten für die B2B-Integration
description: Austauschen von EDIFACT-Nachrichten im EDI-Format für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006522"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Austauschen von EDIFACT-Nachrichten für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack

Bevor Sie EDIFACT-Nachrichten für Azure Logic Apps austauschen können, müssen Sie eine EDIFACT-Vereinbarung erstellen und in Ihrem Integrationskonto speichern. Hier erfahren Sie, wie Sie eine EDIFACT-Vereinbarung erstellen.

> [!NOTE]
> Auf dieser Seite werden die EDIFACT-Features für Azure Logic Apps beschrieben. Weitere Informationen finden Sie unter [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md)  
* Mindestens zwei [Partner](logic-apps-enterprise-integration-partners.md), die bereits in Ihrem Integrationskonto definiert sind.

> [!NOTE]
> Beim Erstellen einer Vereinbarung muss der Inhalt in den Nachrichten, die Sie an den Partner senden oder vom Partner empfangen, zum Vereinbarungstyp passen.

Nachdem Sie [ein Integrationskonto erstellt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) und [Partner hinzugefügt](logic-apps-enterprise-integration-partners.md) haben, können Sie eine EDIFACT-Vereinbarung erstellen. Gehen Sie dazu wie folgt vor:

## <a name="create-an-edifact-agreement"></a>Erstellen einer EDIFACT-Vereinbarung 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com "Azure-Portal") an. 

2. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Suchfeld das Wort „Integration“ ein, und wählen Sie dann **Integrationskonten** aus.

   ![Suchen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Falls **Alle Dienste** nicht angezeigt wird, müssen Sie das Menü möglicherweise zunächst erweitern. Wählen Sie im oberen Bereich des reduzierten Menüs die Option **Beschriftungen anzeigen** aus.

3. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, in dem Sie die Vereinbarung erstellen möchten.

   ![Integrationskonto für die Erstellung der Vereinbarung auswählen](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Klicken Sie auf **Vereinbarungen**. Sollte die Kachel „Vereinbarungen“ nicht angezeigt werden, fügen Sie sie hinzu.   

   ![Kachel „Vereinbarungen“ auswählen](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Klicken Sie auf der Seite „Vereinbarungen“ auf **Hinzufügen**.

   ![„Hinzufügen“ auswählen](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Geben Sie unter **Hinzufügen** im Feld **Name** einen Namen für Ihre Vereinbarung ein. Wählen Sie unter **Vertragstyp** die Option **EDIFACT** aus. Wählen Sie den **Hostpartner**, die **Hostidentität**, den **Gastpartner** und die **Gastidentität** für Ihre Vereinbarung aus.

   ![Details zur Vereinbarung angeben](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Eigenschaft | BESCHREIBUNG |
   | --- | --- |
   | Name |Name der Vereinbarung |
   | Vereinbarungstyp | Muss EDIFACT lauten. |
   | Hostpartner |Eine Vereinbarung benötigt einen Host- und einen Gastpartner. Der Hostpartner stellt die Organisation dar, die die Vereinbarung konfiguriert. |
   | Hostidentität |Ein Bezeichner für den Hostpartner. |
   | Gastpartner |Eine Vereinbarung benötigt einen Host- und einen Gastpartner. Der Gastpartner stellt die Organisation dar, die Geschäfte mit dem Hostpartner tätigt. |
   | Gastidentität |Ein Bezeichner für den Gastpartner. |
   | Empfangseinstellungen |Diese Eigenschaften gelten für alle Nachrichten, die von einer Vereinbarung empfangen werden. |
   | Sendeeinstellungen |Diese Eigenschaften gelten für alle Nachrichten, die von einer Vereinbarung gesendet werden. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurieren der Behandlung empfangener Nachrichten durch die Vereinbarung

Nachdem Sie die Vereinbarungseigenschaften festgelegt haben, können Sie konfigurieren, wie die Vereinbarung eingehende Nachrichten identifizieren und behandeln soll, die im Rahmen dieser Vereinbarung von Ihrem Partner gesendet werden.

> [!IMPORTANT]
> Der EDIFACT-Connector unterstützt nur UTF-8-Zeichen.
> Wenn Ihre Ausgabe unerwartete Zeichen enthält, überprüfen Sie, ob Ihre EDIFACT-Nachrichten den UTF-8-Zeichensatz verwenden.

1. Wählen Sie unter **Hinzufügen** die Option **Empfangseinstellungen** aus.
Konfigurieren Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner, der Nachrichten mit Ihnen austauscht. Die Eigenschaften werden in den Tabellen in diesem Abschnitt beschrieben.

   **Empfangseinstellungen** ist in die folgenden Abschnitte unterteilt: „Bezeichner“, „Bestätigung“, „Schemas“, „Kontrollnummern“, „Überprüfung“ und „Interne Einstellungen“.

   ![Empfangseinstellungen konfigurieren](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Klicken Sie abschließend auf **OK**, um die Einstellungen zu speichern.

Ihre Vereinbarung kann nun eingehende Nachrichten verarbeiten, die den ausgewählten Einstellungen entsprechen.

### <a name="identifiers"></a>Bezeichner

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNB6.1 (Empfängerverweiskennwort) |Geben Sie einen alphanumerischen Wert mit 1 bis 14 Zeichen ein. |
| UNB6.2 (Empfängerverweisqualifizierer) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal zwei Zeichen ein. |

### <a name="acknowledgments"></a>Danksagungen

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Nachrichtenquittung (CONTRL) |Wählen Sie dieses Kontrollkästchen, um eine technische Bestätigung (CONTRL) an den Austauschabsender zurückzugeben. Die Bestätigung wird anhand der Sendeeinstellungen für die Vereinbarung an den Austauschabsender gesendet. |
| Bestätigung (CONTRL) |Aktivieren Sie dieses Kontrollkästchen, um eine funktionale Bestätigung (CONTRL) an den Austauschabsender zurückzugeben. Die Bestätigung wird anhand der Sendeeinstellungen für die Vereinbarung an den Austauschabsender gesendet. |

### <a name="schemas"></a>Schemas

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNH2.1 (TYP) |Wählen Sie einen Transaktionssatztyp aus. |
| UNH2.2 (VERSION) |Geben Sie die Nachrichtenversionsnummer ein. (Mindestens ein Zeichen, maximal drei Zeichen.) |
| UNH2.3 (FREIGABE) |Geben Sie die Nachrichtenfreigabenummer ein. (Mindestens ein Zeichen, maximal drei Zeichen.) |
| UNH2.5 (ZUGEWIESENER CODE FÜR ZUORDNUNG) |Geben Sie den zugewiesenen Code ein. (Maximal sechs Zeichen. Muss alphanumerisch sein.) |
| UNG2.1 (ID FÜR APP-SENDER) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 35 Zeichen ein. |
| UNG2.2 (CODEQUALIFIZIERER FÜR APP-SENDER) |Geben Sie einen alphanumerischen Wert mit maximal vier Zeichen ein. |
| SCHEMA |Wählen Sie im zugeordneten Integrationskonto das gewünschte Schema aus, das Sie zuvor hochgeladen haben. |

### <a name="control-numbers"></a>Kontrollnummern

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Doppelte Austauschkontrollnummern nicht zulassen |Wählen Sie diese Eigenschaft aus, um doppelte Austauschvorgänge zu blockieren. Bei Aktivierung wird mit der EDIFACT-Decodierungsaktion sichergestellt, dass die Austauschkontrollnummer (UNB5) für den empfangenen Austausch nicht mit einer zuvor verarbeiteten Austauschkontrollnummer übereinstimmt. Wenn eine Übereinstimmung erkannt wird, wird der Austausch nicht verarbeitet. |
| Innerhalb der folgenden Anzahl von Tagen auf UNB5-Duplikate prüfen |Wenn Sie sich gegen das Zulassen von doppelten Austauschkontrollnummern entschieden haben, können Sie die Anzahl von Tagen angeben, nach denen die Überprüfung durchgeführt werden soll. Geben Sie hierzu einen geeigneten Wert für diese Einstellung an. |
| Doppelte Gruppenkontrollnummern nicht zulassen |Wählen Sie diese Eigenschaft aus, um Austauschvorgänge mit doppelten Gruppenkontrollnummern (UNG5) zu blockieren. |
| Doppelte Transaktionssatz-Kontrollnummern nicht zulassen |Wählen Sie diese Eigenschaft aus, um Austauschvorgänge mit doppelten Transaktionssatz-Kontrollnummern (UNH1) zu blockieren. |
| EDIFACT-Bestätigungskontrollnummer |Geben Sie einen Wert für das Präfix, einen Bereich von Verweisnummern und ein Suffix ein, um die Transaktionssatz-Verweisnummern für die Verwendung in einer Bestätigung anzugeben. |

### <a name="validation"></a>Überprüfen

Nach Abschluss der einzelnen Überprüfungszeilen wird jeweils automatisch eine weitere hinzugefügt. Wenn Sie keine Regeln angeben, verwendet die Überprüfung die Standardzeile.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Nachrichtentyp |Wählen Sie den EDI-Nachrichtentyp aus. |
| EDI-Überprüfung |Die EDI-Überprüfung erfolgt für Datentypen gemäß den EDI-Eigenschaften des Schemas, Längenbeschränkungen, leeren Datenelementen und nachfolgenden Trennzeichen. |
| Erweiterte Überprüfung |Wenn der Datentyp nicht EDI ist, werden die Datenelementanforderung sowie zulässige Wiederholung, Enumerationen und Datenelementlänge (Min./Max.) überprüft. |
| Führende/nachfolgende Nullen zulassen |Behält alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| Führende/nachgestellte Nullen abschneiden |Entfernt führende oder nachfolgende Nullen und Leerzeichen. |
| Richtlinie für nachgestellte Trennzeichen |Generiert nachfolgende Trennzeichen. <p>Wählen Sie **Nicht zulässig** aus, wenn der empfangene Austauschvorgang keine nachfolgenden Trennzeichen enthalten darf. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>Wählen Sie **Optional** , um Austauschvorgänge mit oder ohne nachgestellte Trennzeichen zu akzeptieren. <p>Wählen Sie **Erforderlich** aus, wenn der empfangene Austauschvorgang nachfolgende Trennzeichen enthalten muss. |

### <a name="internal-settings"></a>Interne Einstellungen

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Leere XML-Tags erstellen, wenn nachfolgende Trennzeichen zulässig sind |Aktivieren Sie dieses Kontrollkästchen, damit der Austauschabsender leere XML-Tags für nachfolgende Trennzeichen einbezieht. |
| Austausch in Transaktionssätze trennen – Transaktionssätze bei Fehler anhalten|Analysiert jeden Transaktionssatz in einem Austausch in ein separates XML-Dokument, indem der geeignete Umschlag auf den Transaktionssatz angewendet wird. Hält nur die Transaktionssätze an, die die Überprüfung nicht bestehen. |
| Austausch in Transaktionssätze trennen – Austausch bei Fehler anhalten|Analysiert jeden Transaktionssatz in einem Austausch in ein separates XML-Dokument, indem der geeignete Umschlag angewendet wird. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. | 
| Austausch beibehalten – Transaktionssätze bei Fehler anhalten |Behält den Austausch bei und erstellt ein XML-Dokument für den gesamten Batchaustausch. Hält nur die Transaktionssätze an, die die Überprüfung nicht bestehen. Alle anderen Transaktionssätze werden verarbeitet. |
| Austausch beibehalten – Austausch bei Fehler anhalten |Behält den Austausch bei und erstellt ein XML-Dokument für den gesamten Batchaustausch. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurieren des Nachrichtenversands Ihrer Vereinbarung

Sie können konfigurieren, wie Ihre Vereinbarung ausgehende Nachrichten identifizieren und behandeln soll, die Sie im Rahmen dieser Vereinbarung an Ihre Partner senden.

1.  Wählen Sie unter **Hinzufügen** die Option **Sendeeinstellungen** aus.
Konfigurieren Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner, der Nachrichten mit Ihnen austauscht. Die Eigenschaften werden in den Tabellen in diesem Abschnitt beschrieben.

    **Sendeeinstellungen** sind in die folgenden Abschnitte unterteilt: „Bezeichner“, „Bestätigung“, „Schemas“, „Umschläge“, „Zeichensätze und Trennzeichen“, „Kontrollnummern“ und „Überprüfungen“.

    ![Sendeeinstellungen konfigurieren](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Klicken Sie abschließend auf **OK**, um die Einstellungen zu speichern.

Ihre Vereinbarung kann nun ausgehende Nachrichten verarbeiten, die den ausgewählten Einstellungen entsprechen.

### <a name="identifiers"></a>Bezeichner

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNB1.2 (Syntaxversion) |Wählen Sie einen Wert zwischen **1** und **4** aus. |
| UNB2.3 (Adresse für Rückmeldung des Absenders) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 14 Zeichen ein. |
| UNB3.3 (Adresse für Rückmeldung des Empfängers) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 14 Zeichen ein. |
| UNB6.1 (Empfängerverweiskennwort) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 14 Zeichen ein. |
| UNB6.2 (Empfängerverweisqualifizierer) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal zwei Zeichen ein. |
| UNB7 (Anwendungsverweis-ID) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 14 Zeichen ein. |

### <a name="acknowledgment"></a>Bestätigung

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Nachrichtenquittung (CONTRL) |Aktivieren Sie dieses Kontrollkästchen, wenn der gehostete Partner den Empfang einer technischen Bestätigung (CONTRL) erwartet. Diese Einstellung gibt an, dass der gehostete Partner, der die Nachricht sendet, eine Bestätigung vom Gastpartner anfordert. |
| Bestätigung (CONTRL) |Aktivieren Sie dieses Kontrollkästchen, wenn der gehostete Partner den Empfang einer funktionalen Bestätigung (CONTRL) erwartet. Diese Einstellung gibt an, dass der gehostete Partner, der die Nachricht sendet, eine Bestätigung vom Gastpartner anfordert. |
| SG1/SG4-Schleife für akzeptierte Transaktionssätze generieren |Wenn Sie sich für die Anforderung einer funktionalen Bestätigung entschieden haben, können Sie dieses Kontrollkästchen aktivieren, um die Generierung von SG1/SG4-Schleifen in funktionalen CONTRL-Bestätigungen für akzeptierte Transaktionssätze zu erzwingen. |

### <a name="schemas"></a>Schemas

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNH2.1 (TYP) |Wählen Sie einen Transaktionssatztyp aus. |
| UNH2.2 (VERSION) |Geben Sie die Nachrichtenversionsnummer ein. |
| UNH2.3 (FREIGABE) |Geben Sie die Nachrichtenfreigabenummer ein. |
| SCHEMA |Wählen Sie das zu verwendende Schema aus. Schemas befinden sich in Ihrem Integrationskonto. Um auf Ihre Schemas zuzugreifen, verknüpfen Sie zuerst Ihr Integrationskonto mit Ihrer Logik-App. |

### <a name="envelopes"></a>Umschläge

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNB8 (Verarbeitungsprioritätscode) |Geben Sie einen alphabetischen Wert mit einer Länge von maximal einem Zeichen ein. |
| UNB10 (Kommunikationsvereinbarung ) |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 40 Zeichen ein. |
| UNB11 (Testindikator) |Aktivieren Sie dieses Kontrollkästchen, um anzugeben, dass es sich beim generierten Austausch um Testdaten handelt. |
| UNA-Segment übernehmen (Zeichenfolgedienstanweisung) |Aktivieren Sie dieses Kontrollkästchen, um ein UNA-Segment für den zu sendenden Austausch zu generieren. |
| UNG-Segmente übernehmen (Funktionsgruppenheader) |Aktivieren Sie dieses Kontrollkästchen, um Gruppierungssegmente im Funktionsgruppenheader in den Nachrichten zu erstellen, die an den Gastpartner gesendet werden. Die folgenden Werte werden verwendet, um die UNG-Segmente zu erstellen: <p>Geben Sie für **UNG1** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal sechs Zeichen ein. <p>Geben Sie für **UNG2.1** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 35 Zeichen ein. <p>Geben Sie für **UNG2.2** einen alphanumerischen Wert mit maximal vier Zeichen ein. <p>Geben Sie für **UNG3.1** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 35 Zeichen ein. <p>Geben Sie für **UNG3.2** einen alphanumerischen Wert mit maximal vier Zeichen ein. <p>Geben Sie für **UNG6** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal drei Zeichen ein. <p>Geben Sie für **UNG7.1** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal drei Zeichen ein. <p>Geben Sie für **UNG7.2** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal drei Zeichen ein. <p>Geben Sie für **UNG7.3** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal sechs Zeichen ein. <p>Geben Sie für **UNG8** einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 14 Zeichen ein. |

### <a name="character-sets-and-separators"></a>Zeichensätze und Trennzeichen

Anders als beim Zeichensatz können Sie für jeden Nachrichtentyp einen anderen Satz zu verwendender Trennzeichen eingeben. Wenn ein Zeichensatz nicht für ein bestimmtes Nachrichtenschema angegeben ist, wird der Standardzeichensatz verwendet.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNB1.1 (Systembezeichner) |Wählen Sie den EDIFACT-Zeichensatz aus, der auf den ausgehenden Austauschvorgang angewendet werden soll. |
| Schema |Wählen Sie in der Dropdownliste ein Schema aus. Nach Abschluss der einzelnen Zeilen wird jeweils automatisch eine neue Zeile hinzugefügt. Wählen Sie für das ausgewählte Schema den gewünschten Trennzeichensatz aus. Orientieren Sie sich dabei an den folgenden Trennzeichenbeschreibungen. |
| Eingabetyp |Wählen Sie in der Dropdownliste einen Eingabetyp aus. |
| Komponententrennzeichen |Geben Sie ein einzelnes Zeichen zum Trennen zusammengesetzter Datenelemente ein. |
| Datenelementtrennzeichen |Geben Sie ein einzelnes Zeichen zum Trennen einfacher Datenelemente in zusammengesetzten Datenelementen ein. |
| Segmentabschlusszeichen |Geben Sie ein einzelnes Zeichen ein, um das Ende eines EDI-Segments anzugeben. |
| Suffix |Wählen Sie das Zeichen, das mit dem Segmentbezeichner verwendet wird. Wenn Sie ein Suffix angeben, kann das Segmentabschlusszeichen-Datenelement leer sein. Wenn das Segmentabschlusszeichen leer gelassen wird, müssen Sie ein Suffix angeben. |

### <a name="control-numbers"></a>Kontrollnummern

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| UNB5 (Austauschkontrollnummer) |Geben Sie ein Präfix, einen Bereich mit Werten für die Austauschkontrollnummer und ein Suffix ein. Diese Werte werden verwendet, um einen ausgehenden Austausch zu generieren. Präfix und Suffix sind optional. Die Kontrollnummer ist erforderlich. Die Kontrollnummer wird für jede neue Nachricht um den Wert 1 erhöht. Das Präfix und das Suffix bleibt jeweils gleich. |
| UNG5 (Gruppenkontrollnummer) |Geben Sie ein Präfix, einen Bereich mit Werten für die Austauschkontrollnummer und ein Suffix ein. Diese Werte werden verwendet, um die Gruppenkontrollnummer zu generieren. Präfix und Suffix sind optional. Die Kontrollnummer ist erforderlich. Die Kontrollnummer wird für jede neue Nachricht um den Wert 1 erhöht, bis der Maximalwert erreicht ist. Das Präfix und das Suffix bleiben gleich. |
| UNH1 (Verweisnummer des Nachrichtenheaders) |Geben Sie ein Präfix, einen Bereich mit Werten für die Austauschkontrollnummer und ein Suffix ein. Diese Werte werden verwendet, um die Verweisnummer des Nachrichtenheaders zu generieren. Präfix und Suffix sind optional. Die Verweisnummer ist erforderlich. Die Verweisnummer wird für jede neue Nachricht um den Wert 1 erhöht. Das Präfix und das Suffix bleibt jeweils gleich. |

### <a name="validation"></a>Überprüfen

Nach Abschluss der einzelnen Überprüfungszeilen wird jeweils automatisch eine weitere hinzugefügt. Wenn Sie keine Regeln angeben, verwendet die Überprüfung die Standardzeile.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Nachrichtentyp |Wählen Sie den EDI-Nachrichtentyp aus. |
| EDI-Überprüfung |Die EDI-Überprüfung erfolgt für Datentypen gemäß den EDI-Eigenschaften des Schemas, Längenbeschränkungen, leeren Datenelementen und nachfolgenden Trennzeichen. |
| Erweiterte Überprüfung |Wenn der Datentyp nicht EDI ist, werden die Datenelementanforderung sowie zulässige Wiederholung, Enumerationen und Datenelementlänge (Min./Max.) überprüft. |
| Führende/nachfolgende Nullen zulassen |Behält alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| Führende/nachgestellte Nullen abschneiden |Entfernt führende oder nachfolgende Nullen. |
| Richtlinie für nachgestellte Trennzeichen |Generiert nachfolgende Trennzeichen. <p>Wählen Sie **Nicht zulässig** aus, wenn der gesendete Austauschvorgang keine nachfolgenden Trennzeichen enthalten darf. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>Wählen Sie **Optional** aus, um Austauschvorgänge mit und ohne nachgestellte Trennzeichen zu senden. <p>Wählen Sie **Erforderlich** aus, wenn der gesendete Austauschvorgang nachfolgende Trennzeichen enthalten muss. |

## <a name="find-your-created-agreement"></a>Suchen der erstellten Vereinbarung

1.  Wählen Sie nach dem Festlegen der Vereinbarungseigenschaften auf der Seite **Hinzufügen** die Option **OK** aus, um die Erstellung Ihrer Vereinbarung abzuschließen und zu Ihrem Integrationskonto zurückzukehren.

    Die neu hinzugefügte Vereinbarung ist nun in der Liste **Vereinbarungen** enthalten.

2.  Sie können Ihre Vereinbarungen auch in der Integrationskontoübersicht anzeigen. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus, und wählen Sie dann die Kachel **Vereinbarungen** aus. 

    ![Screenshot: Kachel „Vereinbarungen“](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/edifact/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors die [B2B-Nachrichtengrenzwerte für ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
