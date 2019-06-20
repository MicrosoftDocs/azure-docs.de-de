---
title: X12-Nachrichten für die B2B-Unternehmensintegration – Azure Logic Apps | Microsoft-Dokumentation
description: Austauschen von X12-Nachrichten im EDI-Format für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: f06e213dbae31c9d7c4e212d605cc962aba71d2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728756"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack

Bevor Sie X12-Nachrichten für Azure Logic Apps austauschen können, müssen Sie eine X12-Vereinbarung erstellen und in Ihrem Integrationskonto speichern. Hier erfahren Sie, wie Sie eine X12-Vereinbarung erstellen.

> [!NOTE]
> Auf dieser Seite werden die X12-Features für Azure Logic Apps beschrieben. Weitere Informationen finden Sie unter [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md)
* Mindestens zwei [Partner](../logic-apps/logic-apps-enterprise-integration-partners.md), die in Ihrem Integrationskonto definiert und mit der X12-Kennung unter **Geschäftsidentitäten** konfiguriert sind    
* Ein erforderliches [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md), das Sie in Ihr Integrationskonto hochladen können

Wenn Sie [ein Integrationskonto erstellt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) und [Partner hinzugefügt](logic-apps-enterprise-integration-partners.md) haben sowie über ein geeignetes [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) verfügen, können Sie eine X12-Vereinbarung erstellen. Gehen Sie dazu wie folgt vor:

## <a name="create-an-x12-agreement"></a>Erstellen einer X12-Vereinbarung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com "Azure-Portal") an. 

2. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. 
   Geben Sie im Suchfeld das Wort „Integration“ ein, und wählen Sie dann **Integrationskonten** aus.  

   ![Suchen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Falls **Alle Dienste** nicht angezeigt wird, müssen Sie das Menü möglicherweise zunächst erweitern. Wählen Sie im oberen Bereich des reduzierten Menüs die Option **Menü anzeigen** aus.

3. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie die Vereinbarung hinzufügen möchten.

   ![Integrationskonto für die Erstellung der Vereinbarung auswählen](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Wählen Sie **Übersicht** und anschließend die Kachel **Vereinbarungen** aus. 
   Sollte die Kachel „Vereinbarungen“ nicht angezeigt werden, fügen Sie sie hinzu. 

   ![Kachel „Vereinbarungen“ auswählen](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Wählen Sie unter **Vereinbarungen** die Option **Hinzufügen** aus.

   ![„Hinzufügen“ auswählen](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Geben Sie unter **Hinzufügen** im Feld **Name** einen Namen für Ihre Vereinbarung ein. 
   Wählen Sie als Vertragstyp die Option **X12** aus. 
   Wählen Sie den **Hostpartner**, die **Hostidentität**, den **Gastpartner** und die **Gastidentität** für Ihre Vereinbarung aus. 
   Weitere Details zu den Eigenschaften finden Sie in der Tabelle in diesem Schritt.

    ![Details zur Vereinbarung angeben](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Eigenschaft | BESCHREIBUNG |
    | --- | --- |
    | NAME |Name der Vereinbarung |
    | Vereinbarungstyp | Muss X12 sein |
    | Hostpartner |Eine Vereinbarung benötigt einen Host- und einen Gastpartner. Der Hostpartner stellt die Organisation dar, die die Vereinbarung konfiguriert. |
    | Hostidentität |Ein Bezeichner für den Hostpartner. |
    | Gastpartner |Eine Vereinbarung benötigt einen Host- und einen Gastpartner. Der Gastpartner stellt die Organisation dar, die Geschäfte mit dem Hostpartner tätigt. |
    | Gastidentität |Ein Bezeichner für den Gastpartner. |
    | Empfangseinstellungen |Diese Eigenschaften gelten für alle Nachrichten, die von einer Vereinbarung empfangen werden. |
    | Sendeeinstellungen |Diese Eigenschaften gelten für alle Nachrichten, die von einer Vereinbarung gesendet werden. |  

   > [!NOTE]
   > Die Auflösung der X12-Vereinbarung hängt von der Übereinstimmung des Absenderqualifizierers und Bezeichners sowie des Empfängerqualifizierers und Bezeichners ab, die beim Partner und in der eingehenden Nachricht definiert sind. Falls sich diese Werte für Ihren Partner ändern, aktualisieren Sie die Vereinbarung entsprechend.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurieren der Behandlung empfangener Nachrichten durch die Vereinbarung

Nachdem Sie die Vereinbarungseigenschaften festgelegt haben, können Sie konfigurieren, wie die Vereinbarung eingehende Nachrichten identifizieren und behandeln soll, die im Rahmen dieser Vereinbarung von Ihrem Partner gesendet werden.

1.  Wählen Sie unter **Hinzufügen** die Option **Empfangseinstellungen** aus.
Konfigurieren Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner, der Nachrichten mit Ihnen austauscht. Die Eigenschaften werden in den Tabellen in diesem Abschnitt beschrieben.

    **Empfangseinstellungen** ist in die folgenden Abschnitte unterteilt: „Bezeichner“, „Bestätigung“, „Schemas“, „Umschläge“, „Kontrollnummern“, „Überprüfungen“ und „Interne Einstellungen“.

2. Klicken Sie abschließend auf **OK**, um die Einstellungen zu speichern.

Ihre Vereinbarung kann nun eingehende Nachrichten verarbeiten, die den ausgewählten Einstellungen entsprechen.

### <a name="identifiers"></a>Bezeichner

![Bezeichnereigenschaften festlegen](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| ISA1 (Autorisierungsqualifizierer) |Wählen Sie den Wert für den Autorisierungsqualifizierer aus der Dropdownliste. |
| ISA2 |Optional. Geben Sie den Wert für den Autorisierungsqualifizierer ein. Wenn Sie für ISA1 einen anderen Wert als 00 eingegeben haben, geben Sie mindestens ein alphanumerisches Zeichen und maximal 10 ein. |
| ISA3 (Sicherheitsqualifizierer) |Wählen Sie den Wert für den Sicherheitsqualifizierer aus der Dropdownliste. |
| ISA4 |Optional. Geben Sie den Wert für Sicherheitsinformationen ein. Wenn Sie für ISA3 einen anderen Wert als 00 eingegeben haben, geben Sie mindestens ein alphanumerisches Zeichen und maximal 10 ein. |

### <a name="acknowledgment"></a>Bestätigung

![Bestätigungseigenschaften festlegen](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TA1 erwartet |Gibt eine technische Bestätigung an den Absender des Austauschs zurück. |
| FA erwartet |Gibt eine Funktionsbestätigung an den Absender des Austauschs zurück. Wählen Sie dann basierend auf den Schemaversionen, mit denen Sie arbeiten, ob Sie die Bestätigungen des Typs 997 oder 999 wünschen. |
| AK2/IK2-Schleife einschließen |Aktiviert die Generierung von AK2-Schleifen in Funktionsbestätigungen für akzeptierte Transaktionssätze |

### <a name="schemas"></a>Schemas

Wählen Sie ein Schema für jeden Transaktionstyp (ST1) und jede Absenderanwendung (GS2) aus. Die Empfangspipeline disassembliert die eingehende Nachricht durch Abstimmen der Werte für ST1 und GS2 in der eingehenden Nachricht mit den hier festgelegten Werten und des Schemas der eingehenden Nachricht mit dem Schema, das Sie hier festlegen.

![Schema auswählen](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Version |Wählen Sie die X12-Version. |
| Transaktionstyp (ST01) |Wählen Sie den Transaktionstyp. |
| Absenderanwendung (GS02) |Wählen Sie die Absenderanwendung. |
| Schema |Wählen Sie die gewünschte Schemadatei aus. Schemas werden Ihrem Integrationskonto hinzugefügt. |

> [!NOTE]
> Konfigurieren Sie das erforderliche [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md), das in Ihr [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md) hochgeladen wird.

### <a name="envelopes"></a>Umschläge

![Trennzeichen in einem Transaktionssatz angeben: „Standardbezeichner“ oder „Wiederholungstrennzeichen“](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| ISA11-Verwendung |Gibt das Trennzeichen an, das in einem Transaktionssatz verwendet werden soll: <p>Wählen Sie **Standardbezeichner** aus, um anstelle der Dezimalschreibweise des eingehenden Dokuments in der EDI-Empfangspipeline einen Punkt (.) für die Dezimalschreibweise zu verwenden. <p>Wählen Sie **Wiederholungstrennzeichen** aus, um das Trennzeichen für wiederholte Vorkommen eines einfachen Datenelements oder einer wiederholten Datenstruktur anzugeben. Als Wiederholungstrennzeichen wird beispielsweise in der Regel das Caretzeichen (^) verwendet. Für HIPAA-Schemas können Sie ausschließlich das Caretzeichen verwenden. |

### <a name="control-numbers"></a>Kontrollnummern

![Behandlung von Kontrollnummernduplikaten auswählen](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Doppelte Austauschkontrollnummern nicht zulassen |Blockiert doppelte Austauschvorgänge. Überprüft die Austauschkontrollnummer (ISA13) auf die empfangene Austauschkontrollnummer. Wird eine Übereinstimmung gefunden, verarbeitet die Empfangspipeline den Austausch nicht. Sie können den Zeitraum für die Überprüfung angeben, indem Sie einen Wert für *Auf ISA13-Duplikate überprüfen alle (Tage)* festlegen. |
| Doppelte Gruppenkontrollnummern nicht zulassen |Blockiert Austauschvorgänge mit doppelten Gruppenkontrollnummern. |
| Doppelte Transaktionssatz-Kontrollnummern nicht zulassen |Blockiert Austauschvorgänge mit doppelten Transaktionssatz-Kontrollnummern. |

### <a name="validations"></a>Überprüfungen

![Überprüfungseigenschaften für empfangene Nachrichten festlegen](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Nach Abschluss der einzelnen Überprüfungszeilen wird jeweils automatisch eine weitere hinzugefügt. Wenn Sie keine Regeln angeben, verwendet die Überprüfung die Standardzeile.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Nachrichtentyp |Wählen Sie den EDI-Nachrichtentyp aus. |
| EDI-Überprüfung |Die EDI-Überprüfung erfolgt für Datentypen gemäß den EDI-Eigenschaften des Schemas, Längenbeschränkungen, leeren Datenelementen und nachfolgenden Trennzeichen. |
| Erweiterte Überprüfung |Wenn der Datentyp nicht EDI ist, werden die Datenelementanforderung sowie zulässige Wiederholung, Enumerationen und Datenelementlänge (Min./Max.) überprüft. |
| Führende/nachfolgende Nullen zulassen |Behält alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| Führende/nachgestellte Nullen abschneiden |Entfernt führende oder nachfolgende Nullen und Leerzeichen. |
| Richtlinie für nachgestellte Trennzeichen |Generiert nachfolgende Trennzeichen. <p>Wählen Sie **Nicht zulässig** aus, wenn der empfangene Austauschvorgang keine nachfolgenden Trennzeichen enthalten darf. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>Wählen Sie **Optional** , um Austauschvorgänge mit oder ohne nachgestellte Trennzeichen zu akzeptieren. <p>Wählen Sie **Erforderlich** aus, wenn der Austauschvorgang nachfolgende Trennzeichen enthalten muss. |

### <a name="internal-settings"></a>Interne Einstellungen

![Interne Einstellungen auswählen](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Implizite Dezimalzahlen im Nn-Format in Zehnerlogarithmuswerte einer Zahl konvertieren |Konvertiert einen im Nn-Format angegebenen EDI-Wert in einen Zehnerlogarithmuswert. |
| Leere XML-Tags erstellen, wenn nachfolgende Trennzeichen zulässig sind |Aktivieren Sie dieses Kontrollkästchen, damit der Austauschabsender leere XML-Tags für nachfolgende Trennzeichen einbezieht. |
| Austausch in Transaktionssätze trennen – Transaktionssätze bei Fehler anhalten|Analysiert jeden Transaktionssatz in einem Austausch in ein separates XML-Dokument, indem der geeignete Umschlag auf den Transaktionssatz angewendet wird. Hält nur die Transaktionen an, bei denen die Überprüfung nicht erfolgreich war. |
| Austausch in Transaktionssätze trennen – Austausch bei Fehler anhalten|Analysiert jeden Transaktionssatz in einem Austausch in ein separates XML-Dokument, indem der geeignete Umschlag angewendet wird. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. | 
| Austausch beibehalten – Transaktionssätze bei Fehler anhalten |Behält den Austausch bei und erstellt ein XML-Dokument für den gesamten Batchaustausch. Hält nur die Transaktionssätze an, die die Überprüfung nicht bestehen. Alle anderen Transaktionssätze werden verarbeitet. |
| Austausch beibehalten – Austausch bei Fehler anhalten |Behält den Austausch bei und erstellt ein XML-Dokument für den gesamten Batchaustausch. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurieren des Nachrichtenversands Ihrer Vereinbarung

Sie können konfigurieren, wie Ihre Vereinbarung ausgehende Nachrichten identifizieren und behandeln soll, die Sie im Rahmen dieser Vereinbarung an Ihren Partner senden.

1.  Wählen Sie unter **Hinzufügen** die Option **Sendeeinstellungen** aus.
Konfigurieren Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner, der Nachrichten mit Ihnen austauscht. Die Eigenschaften werden in den Tabellen in diesem Abschnitt beschrieben.

    **Sendeeinstellungen** sind in die folgenden Abschnitte unterteilt: „Bezeichner“, „Bestätigung“, „Schemas“, „Umschläge“, „Zeichensätze und Trennzeichen“, „Kontrollnummern“ und „Überprüfung“.

2. Klicken Sie abschließend auf **OK**, um die Einstellungen zu speichern.

Ihre Vereinbarung kann nun ausgehende Nachrichten verarbeiten, die den ausgewählten Einstellungen entsprechen.

### <a name="identifiers"></a>Bezeichner

![Bezeichnereigenschaften festlegen](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Autorisierungsqualifizierer (ISA1) |Wählen Sie den Wert für den Autorisierungsqualifizierer aus der Dropdownliste. |
| ISA2 |Geben Sie den Wert für den Autorisierungsqualifizierer ein. Wenn dieser Wert nicht 00 ist, geben Sie mindestens ein alphanumerisches Zeichen und maximal 10 ein. |
| Sicherheitsqualifizierer (ISA3) |Wählen Sie den Wert für den Sicherheitsqualifizierer aus der Dropdownliste. |
| ISA4 |Geben Sie den Wert für Sicherheitsinformationen ein. Wenn dieser Wert nicht 00 ist, geben Sie für den Wert (ISA4) im Textfeld mindestens ein alphanumerisches Zeichen und maximal 10 ein. |

### <a name="acknowledgment"></a>Bestätigung

![Bestätigungseigenschaften festlegen](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TA1 erwartet |Gibt eine technische Bestätigung (TA1) an den Absender des Austauschs zurück. Diese Einstellung gibt an, dass der Hostpartner, der die Nachricht sendet, eine Bestätigung vom Gastpartner in der Vereinbarung anfordert. Diese Bestätigungen werden vom Hostpartner basierend auf den Empfangseinstellungen der Vereinbarung erwartet. |
| FA erwartet |Gibt eine funktionale Bestätigung (FA) an den Absender des Austauschs zurück. Wählen Sie auf der Grundlage der verwendeten Schemaversionen aus, ob Sie 997- oder 999-Bestätigungen verwenden möchten. Diese Bestätigungen werden vom Hostpartner basierend auf den Empfangseinstellungen der Vereinbarung erwartet. |
| FA-Version |Wählen Sie die FA-Version. |

### <a name="schemas"></a>Schemas

![Zu verwendendes Schema auswählen](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Version |Wählen Sie die X12-Version. |
| Transaktionstyp (ST01) |Wählen Sie den Transaktionstyp. |
| Schema |Wählen Sie das zu verwendende Schema aus. Schemas befinden sich in Ihrem Integrationskonto. Wenn Sie das Schema zuerst ausgewählt haben, konfiguriert es automatisch die Version und den Transaktionstyp.  |

> [!NOTE]
> Konfigurieren Sie das erforderliche [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md), das in Ihr [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md) hochgeladen wird.

### <a name="envelopes"></a>Umschläge

![Trennzeichen in einem Transaktionssatz angeben: „Standardbezeichner“ oder „Wiederholungstrennzeichen“](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| ISA11-Verwendung |Gibt das Trennzeichen an, das in einem Transaktionssatz verwendet werden soll: <p>Wählen Sie **Standardbezeichner** aus, um anstelle der Dezimalschreibweise des eingehenden Dokuments in der EDI-Empfangspipeline einen Punkt (.) für die Dezimalschreibweise zu verwenden. <p>Wählen Sie **Wiederholungstrennzeichen** aus, um das Trennzeichen für wiederholte Vorkommen eines einfachen Datenelements oder einer wiederholten Datenstruktur anzugeben. Als Wiederholungstrennzeichen wird beispielsweise in der Regel das Caretzeichen (^) verwendet. Für HIPAA-Schemas können Sie ausschließlich das Caretzeichen verwenden. |

### <a name="control-numbers"></a>Kontrollnummern

![Kontrollnummerneigenschaften angeben](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Kontrollversionsnummer (ISA12) |Wählen der Version des X12-Standards |
| Verwendungsindikator (ISA15) |Wählen des Kontexts eines Austauschs.  Die Werte sind Informationen, Produktionsdaten oder Testdaten. |
| SCHEMA |Generiert die GS- und ST-Segmente für einen X12-codierten Austausch, der an die Sendepipeline gesendet wird. |
| GS1 |Optional, einen Wert für den Funktionscode in der Dropdownliste wählen |
| GS2 |Optional, Anwendungsabsender |
| GS3 |Optional, Anwendungsempfänger |
| GS4 |CCYYMMDD oder YYMMDD wählen |
| GS5 |Optional, HHMM, HHMMSS oder HHMMSSdd wählen |
| GS7 |Optional, einen Wert für die zuständige Agentur in der Dropdownliste wählen |
| GS8 |Optional, Version des Dokuments |
| Austauschkontrollnummer (ISA13) |Erforderlich, einen Wertebereich für die Austauschkontrollnummer eingeben. Geben Sie einen numerischen Wert von mindestens 1 und maximal 999999999 ein. |
| Gruppenkontrollnummer (GS06) |Erforderlich, einen Wertebereich für die Gruppenkontrollnummer eingeben. Geben Sie einen numerischen Wert von mindestens 1 und maximal 999999999 ein. |
| Transaktionssatz-Kontrollnummer (ST02) |Erforderlich, einen Wertebereich für die Transaktionssatz-Kontrollnummer eingeben. Geben Sie einen numerischen Wertebereich von mindestens 1 und maximal 999999999 ein. |
| Präfix |Optional, vorgesehen für den Bereich von Transaktionssatz-Kontrollnummern, die in der Bestätigung verwendet werden. Geben Sie einen numerischen Wert für die beiden mittleren Felder und einen alphanumerischen Wert (falls gewünscht) für die Felder Präfix und Suffix ein. Die mittleren Felder sind erforderlich und enthalten die minimalen und maximalen Werte für die Kontrollnummer. |
| Suffix |Optional, vorgesehen für den Bereich von Transaktionssatz-Kontrollnummern, die in einer Bestätigung verwendet werden. Geben Sie einen numerischen Wert für die beiden mittleren Felder und einen alphanumerischen Wert (falls gewünscht) für die Felder mit dem Präfix und Suffix ein. Die mittleren Felder sind erforderlich und enthalten die minimalen und maximalen Werte für die Kontrollnummer. |

### <a name="character-sets-and-separators"></a>Zeichensätze und Trennzeichen

Anders als beim Zeichensatz können Sie für jeden Nachrichtentyp einen anderen Trennzeichensatz eingeben. Ist für ein Nachrichtenschema kein Zeichensatz angegeben, wird der Standardzeichensatz verwendet.

![Trennzeichen für Nachrichtentypen angeben](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Zu verwendender Zeichensatz |Wählen Sie zum Überprüfen der Eigenschaften den X12-Zeichensatz aus. Verfügbare Optionen: „Basic“, „Extended“ und „UTF8“. |
| Schema |Wählen Sie in der Dropdownliste ein Schema aus. Nach Abschluss der einzelnen Zeilen wird jeweils automatisch eine neue Zeile hinzugefügt. Wählen Sie für das ausgewählte Schema den gewünschten Trennzeichensatz aus. Orientieren Sie sich dabei an den folgenden Trennzeichenbeschreibungen. |
| Eingabetyp |Wählen Sie in der Dropdownliste einen Eingabetyp aus. |
| Komponententrennzeichen |Geben Sie ein einzelnes Zeichen zum Trennen zusammengesetzter Datenelemente ein. |
| Datenelementtrennzeichen |Geben Sie ein einzelnes Zeichen zum Trennen einfacher Datenelemente in zusammengesetzten Datenelementen ein. |
| Ersetzungszeichen |Geben Sie ein Ersetzungszeichen ein, mit dem beim Generieren der ausgehenden X12-Nachricht alle Trennzeichen in den Nutzlastdaten ersetzt werden. |
| Segmentabschlusszeichen |Geben Sie ein einzelnes Zeichen ein, um das Ende eines EDI-Segments anzugeben. |
| Suffix |Wählen Sie das Zeichen, das mit dem Segmentbezeichner verwendet wird. Wenn Sie ein Suffix angeben, kann das Segmentabschlusszeichen-Datenelement leer sein. Wenn das Segmentabschlusszeichen leer gelassen wird, müssen Sie ein Suffix angeben. |

> [!TIP]
> Um Werte für Sonderzeichen bereitzustellen, bearbeiten Sie die Vereinbarung als JSON, und geben Sie den ASCII-Wert für das Sonderzeichen an.

### <a name="validation"></a>Überprüfen

![Überprüfungseigenschaften für den Nachrichtenversand festlegen](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

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

    ![Kachel „Vereinbarungen“ auswählen](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Anzeigen von Swagger
Weitere Informationen finden Sie unter [Details zu Swagger](/connectors/x12/). 

## <a name="learn-more"></a>Weitere Informationen
* [Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack")  

