---
title: Senden und Empfangen von X12-Nachrichten für B2B
description: Austauschen von X12-Nachrichten für Szenarien der B2B-Unternehmensintegration mithilfe von Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 61f3f2af61bc24f76d061de672a3eaacd54f7f0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015195"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack

Um mit X12-Nachrichten in Azure Logic Apps zu arbeiten, können Sie den X12-Connector verwenden, der Auslöser und Aktionen für die Verwaltung der X12-Kommunikation bereitstellt. Weitere Informationen zu EDIFACT-Nachrichten finden Sie unter [Austauschen von EDIFACT-Nachrichten](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement haben, [melden Sie sich für ein kostenloses Azure-Konto an](https://azure.microsoft.com/free/).

* Die Logik-App, von der aus Sie auf den X12-Connector zugreifen möchten, und einen Trigger, der den Workflow Ihre Logik-App startet. Der X12-Connector stellt nur Aktionen und keine Trigger bereit. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), das Ihrem Azure-Abonnement zugeordnet und mit der Logik-App verknüpft ist, in der Sie den X12-Connector verwenden möchten. Sowohl Ihre Logik-App als auch Ihr Integrationskonto müssen an demselben Standort oder in derselben Azure-Region vorhanden sein.

* Mindestens zwei [Parteien](../logic-apps/logic-apps-enterprise-integration-partners.md), die Sie bereits in Ihrem Integrationskonto unter Verwendung des X12-Identitätsqualifizierers definiert haben.

* Die [Schemas](../logic-apps/logic-apps-enterprise-integration-schemas.md), die für die XML-Überprüfung verwendet werden sollen, die Sie Ihrem Integrationskonto bereits hinzugefügt haben. Wenn Sie mit HIPAA-Schemas (Health Insurance Portability and Accountability Act) arbeiten, finden Sie Informationen unter [HIPAA-Schemas](#hipaa-schemas).

* Bevor Sie den X12-Connector verwenden können, müssen Sie eine X12-[Vereinbarung](../logic-apps/logic-apps-enterprise-integration-agreements.md) zwischen den Parteien erstellen und diese Vereinbarung in Ihrem Integrationskonto speichern. Wenn Sie mit HIPAA-Schemas (Health Insurance Portability and Accountability Act) arbeiten, müssen Sie Ihrer Vereinbarung einen `schemaReferences`-Abschnitt hinzufügen. Weitere Informationen finden Sie unter [HIPAA-Schemas](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Empfangseinstellungen

Nachdem Sie die Vereinbarungseigenschaften festgelegt haben, können Sie konfigurieren, wie die Vereinbarung eingehende Nachrichten identifizieren und behandeln soll, die Sie im Rahmen dieser Vereinbarung von Ihrem Partner empfangen.

1. Wählen Sie unter **Hinzufügen** die Option **Empfangseinstellungen** aus.

1. Konfigurieren Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner, der Nachrichten mit Ihnen austauscht. Die **Empfangseinstellungen** sind in diese Abschnitte unterteilt:

   * [Identifiers (Bezeichner)](#inbound-identifiers)
   * [Bestätigung ](#inbound-acknowledgement)
   * [Schemas](#inbound-schemas)
   * [Umschläge](#inbound-envelopes)
   * [Kontrollnummern](#inbound-control-numbers)
   * [validations](#inbound-validations)
   * [Interne Einstellungen](#inbound-internal-settings)

   Die Eigenschaften werden in den Tabellen in diesem Abschnitt beschrieben.

1. Wählen Sie abschließend **OK** aus, um die Einstellungen zu speichern.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Empfangseinstellungen – Bezeichner

![Bezeichnereigenschaften für eingehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **ISA1 (Autorisierungsqualifizierer)** | Der Wert des Autorisierungsqualifizierers, den Sie verwenden möchten. Der Standardwert ist **00 – Keine Autorisierungsinformationen vorhanden**. <p>**Hinweis**: Wenn Sie andere Werte auswählen, geben Sie einen Wert für die Eigenschaft **ISA2** an. |
| **ISA2** | Der Wert der Autorisierungsinformationen, der verwendet werden soll, wenn die Eigenschaft **ISA1** nicht **00 – Keine Autorisierungsinformationen vorhanden** lautet. Dieser Eigenschaftswert muss aus mindestens einem alphanumerischen Zeichen und maximal 10 Zeichen bestehen. |
| **ISA3 (Sicherheitsqualifizierer)** | Der Wert des Sicherheitsqualifizierers, den Sie verwenden möchten. Der Standardwert ist **00 – Keine Sicherheitsinformationen vorhanden**. <p>**Hinweis**: Wenn Sie andere Werte auswählen, geben Sie einen Wert für die Eigenschaft **ISA4** an. |
| **ISA4** | Der Wert der Sicherheitsinformationen, der verwendet werden soll, wenn die Eigenschaft **ISA3** nicht **00 – Keine Sicherheitsinformationen vorhanden** lautet. Dieser Eigenschaftswert muss aus mindestens einem alphanumerischen Zeichen und maximal 10 Zeichen bestehen. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Empfangseinstellungen – Bestätigung

![Bestätigung für eingehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **TA1 erwartet** | Gibt eine technische Bestätigung (TA1) an den Absender des Austauschs zurück. |
| **FA erwartet** | Gibt eine funktionale Bestätigung (FA) an den Absender des Austauschs zurück. <p>Wählen Sie basierend auf der Schemaversion für die **FA-Version** Die Bestätigungen des Typs 997 oder 999 aus. <p>Um die Generierung von AK2-Schleifen in funktionalen Bestätigungen für akzeptierte Transaktionssätze zu aktivieren, wählen Sie **AK2-/IK2-Schleife einschließen** aus. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Empfangseinstellungen – Schemas

![Schemas für eingehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Wählen Sie für diesen Abschnitt ein [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) in Ihrem [Integrationskonto](./logic-apps-enterprise-integration-create-integration-account.md) für jeden Transaktionstyp (ST01) und jede Absenderanwendung (GS02) aus. Die EDI-Empfangspipeline disassembliert die eingehende Nachricht durch Abgleich der Werte und des Schemas, die Sie in diesem Abschnitt festgelegt haben, mit den Werten für ST01 und GS02 in der eingehenden Nachricht sowie mit dem Schema der eingehenden Nachricht. Nach Abschluss aller einzelnen Zeilen wird automatisch eine neue leere Zeile angezeigt.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Version** | Die X12-Version für das Schema. |
| **Transaktionstyp (ST01)** | Der Transaktionstyp. |
| **Absenderanwendung (GS02)** | Die Absenderanwendung. |
| **Schema** | Die Schemadatei, die verwendet werden soll. |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Empfangseinstellungen – Umschläge

![Trennzeichen, die in Transaktionssätzen für eingehende Nachrichten verwendet werden sollen.](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **ISA11-Verwendung** | Das Trennzeichen, das in einem Transaktionssatz verwendet werden soll: <p>- **Standardbezeichner**: Verwenden Sie anstelle der Dezimalschreibweise des eingehenden Dokuments in der EDI-Empfangspipeline einen Punkt (.) als Dezimalschreibweise. <p>- **Wiederholungstrennzeichen**: Geben Sie das Trennzeichen für wiederholte Vorkommen eines einfachen Datenelements oder einer wiederholten Datenstruktur an. Als Wiederholungstrennzeichen wird beispielsweise in der Regel das Caretzeichen (^) verwendet. Für HIPAA-Schemas können Sie ausschließlich das Caretzeichen verwenden. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Empfangseinstellungen – Kontrollnummern

![Behandeln von Kontrollnummernduplikaten für eingehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Doppelte Austauschkontrollnummern nicht zulassen** | Blockiert doppelte Austauschvorgänge. Überprüft die Austauschkontrollnummer (ISA13) auf die empfangene Austauschkontrollnummer. Wird eine Übereinstimmung gefunden, verarbeitet die EDI-Empfangspipeline den Austausch nicht. <p><p>Um die Anzahl der Tage anzugeben, an denen die Überprüfung durchgeführt werden soll, geben Sie einen Wert für die Eigenschaft **Auf ISA13-Duplikate überprüfen alle (Tage)** ein. |
| **Doppelte Gruppenkontrollnummern nicht zulassen** | Blockiert Austauschvorgänge, die doppelte Gruppenkontrollnummern enthalten. |
| **Doppelte Transaktionssatz-Kontrollnummern nicht zulassen** | Blockiert Austauschvorgänge, die doppelte Transaktionssatz-Kontrollnummern enthalten. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Empfangseinstellungen – Überprüfungen

![Überprüfungen für eingehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

In der Zeile **Standard** werden die Überprüfungsregeln angezeigt, die für einen EDI-Nachrichtentyp verwendet werden. Wenn Sie unterschiedliche Regeln definieren möchten, wählen Sie jedes Feld aus, für das die Regel auf **true** festgelegt werden soll. Nach Abschluss aller einzelnen Zeilen wird automatisch eine neue leere Zeile angezeigt.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Nachrichtentyp** | Der EDI-Nachrichtentyp. |
| **EDI-Überprüfung** | Die EDI-Überprüfung erfolgt für Datentypen gemäß den EDI-Eigenschaften des Schemas, Längenbeschränkungen, leeren Datenelementen und nachfolgenden Trennzeichen. |
| **Erweiterte Überprüfung** | Wenn der Datentyp nicht EDI ist, werden die Datenelementanforderung sowie zulässige Wiederholung, Enumerationen und Datenelementlänge (Min. oder Max.) überprüft. |
| **Führende/nachfolgende Nullen zulassen** | Behält alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| **Führende/nachfolgende Nullen abschneiden** | Entfernt jegliche führende oder nachfolgende Nullen und Leerzeichen. |
| **Richtlinie für nachfolgende Trennzeichen** | Generiert nachfolgende Trennzeichen. <p>- **Nicht zulässig**: Verhindert nachfolgende Trennzeichen im eingehenden Austauschvorgang. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>- **Optional**: Akzeptiert Austauschvorgänge mit oder ohne nachfolgende Trennzeichen. <p>- **Obligatorisch**: Der eingehende Austauschvorgang muss nachfolgende Trennzeichen enthalten. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Empfangseinstellungen – Interne Einstellungen

![Interne Einstellungen für eingehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Implizite Dezimalzahlen im Nn-Format in numerischen Wert zur Basis 10 konvertieren** | Konvertiert einen im Nn-Format angegebenen EDI-Wert in einen numerischen Wert zur Basis 10. |
| **Leere XML-Tags erstellen, wenn nachfolgende Trennzeichen zulässig sind** | Den Austauschabsender veranlassen, leere XML-Tags für nachfolgende Trennzeichen einzubeziehen. |
| **Trennen des Austauschs in Transaktionssätze – Transaktionssätze bei Fehler anhalten** | Analysiert jeden Transaktionssatz, der sich in einem Austausch befindet, in ein separates XML-Dokument, indem der geeignete Umschlag auf den Transaktionssatz angewendet wird. Hält nur die Transaktionen an, bei denen die Überprüfung nicht erfolgreich war. |
| **Trennen des Austauschs in Transaktionssätze – Austausch bei Fehler anhalten** | Analysiert jeden Transaktionssatz, der sich in einem Austausch befindet, in ein separates XML-Dokument, indem der geeignete Umschlag angewendet wird. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. |
| **Austausch beibehalten – Transaktionssätze bei Fehler anhalten** | Behält den Austausch bei und erstellt ein XML-Dokument für den gesamten Batchaustausch. Hält nur die Transaktionssätze an, die die Überprüfung nicht bestehen, setzt aber die Verarbeitung aller anderen Transaktionssätze fort. |
| **Austausch beibehalten – Austausch bei Fehler anhalten** |Behält den Austausch bei und erstellt ein XML-Dokument für den gesamten Batchaustausch. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Sendeeinstellungen

Nachdem Sie die Vereinbarungseigenschaften festgelegt haben, können Sie konfigurieren, wie die Vereinbarung ausgehende Nachrichten identifizieren und behandeln soll, die Sie im Rahmen dieser Vereinbarung an Ihren Partner senden.

1. Wählen Sie unter **Hinzufügen** die Option **Sendeeinstellungen** aus.

1. Konfigurieren Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner, der Nachrichten mit Ihnen austauscht. Die Eigenschaften werden in den Tabellen in diesem Abschnitt beschrieben.

   Die **Sendeeinstellungen** sind in diese Abschnitte unterteilt:

   * [Identifiers (Bezeichner)](#outbound-identifiers)
   * [Bestätigung ](#outbound-acknowledgement)
   * [Schemas](#outbound-schemas)
   * [Umschläge](#outbound-envelopes)
   * [Kontrollversionsnummer](#outbound-control-version-number)
   * [Kontrollnummern](#outbound-control-numbers)
   * [Zeichensätze und Trennzeichen](#outbound-character-sets-separators)
   * [Überprüfung](#outbound-validation)

1. Wählen Sie abschließend **OK** aus, um die Einstellungen zu speichern.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Sendeeinstellungen – Bezeichner

![Bezeichnereigenschaften für ausgehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **ISA1 (Autorisierungsqualifizierer)** | Der Wert des Autorisierungsqualifizierers, den Sie verwenden möchten. Der Standardwert ist **00 – Keine Autorisierungsinformationen vorhanden**. <p>**Hinweis**: Wenn Sie andere Werte auswählen, geben Sie einen Wert für die Eigenschaft **ISA2** an. |
| **ISA2** | Der Wert der Autorisierungsinformationen, der verwendet werden soll, wenn die Eigenschaft **ISA1** nicht **00 – Keine Autorisierungsinformationen vorhanden** lautet. Dieser Eigenschaftswert muss aus mindestens einem alphanumerischen Zeichen und maximal 10 Zeichen bestehen. |
| **ISA3 (Sicherheitsqualifizierer)** | Der Wert des Sicherheitsqualifizierers, den Sie verwenden möchten. Der Standardwert ist **00 – Keine Sicherheitsinformationen vorhanden**. <p>**Hinweis**: Wenn Sie andere Werte auswählen, geben Sie einen Wert für die Eigenschaft **ISA4** an. |
| **ISA4** | Der Wert der Sicherheitsinformationen, der verwendet werden soll, wenn die Eigenschaft **ISA3** nicht **00 – Keine Sicherheitsinformationen vorhanden** lautet. Dieser Eigenschaftswert muss aus mindestens einem alphanumerischen Zeichen und maximal 10 Zeichen bestehen. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Sendeeinstellungen – Bestätigung

![Bestätigungseigenschaften für ausgehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **TA1 erwartet** | Gibt eine technische Bestätigung (TA1) an den Absender des Austauschs zurück. <p>Diese Einstellung gibt an, dass der Hostpartner, der die Nachricht sendet, eine Bestätigung vom Gastpartner in der Vereinbarung anfordert. Diese Bestätigungen werden vom Hostpartner basierend auf den Empfangseinstellungen der Vereinbarung erwartet. |
| **FA erwartet** | Gibt eine funktionale Bestätigung (FA) an den Absender des Austauschs zurück. Wählen Sie basierend auf der Schemaversion für die **FA-Version** Die Bestätigungen des Typs 997 oder 999 aus. <p>Diese Einstellung gibt an, dass der Hostpartner, der die Nachricht sendet, eine Bestätigung vom Gastpartner in der Vereinbarung anfordert. Diese Bestätigungen werden vom Hostpartner basierend auf den Empfangseinstellungen der Vereinbarung erwartet. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Sendeeinstellungen – Schemas

![Schemas für ausgehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Wählen Sie für diesen Abschnitt ein [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) in Ihrem [Integrationskonto](./logic-apps-enterprise-integration-create-integration-account.md) für jeden Transaktionstyp (ST01) aus. Nach Abschluss aller einzelnen Zeilen wird automatisch eine neue leere Zeile angezeigt.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Version** | Die X12-Version für das Schema. |
| **Transaktionstyp (ST01)** | Der Transaktionstyp für das Schema. |
| **Schema** | Die Schemadatei, die verwendet werden soll. Wenn Sie das Schema zuerst auswählen, werden die Version und der Transaktionstyp automatisch festgelegt. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Sendeeinstellungen – Umschläge

![Trennzeichen in einem Transaktionssatz, der für eingehende Nachrichten verwendet werden soll.](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **ISA11-Verwendung** | Das Trennzeichen, das in einem Transaktionssatz verwendet werden soll: <p>- **Standardbezeichner**: Verwenden Sie anstelle der Dezimalschreibweise des ausgehenden Dokuments in der EDI-Sendepipeline einen Punkt (.) als Dezimalschreibweise. <p>- **Wiederholungstrennzeichen**: Geben Sie das Trennzeichen für wiederholte Vorkommen eines einfachen Datenelements oder einer wiederholten Datenstruktur an. Als Wiederholungstrennzeichen wird beispielsweise in der Regel das Caretzeichen (^) verwendet. Für HIPAA-Schemas können Sie ausschließlich das Caretzeichen verwenden. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Sendeeinstellungen – Kontrollversionsnummer

![Kontrollversionsnummer für ausgehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Wählen Sie für diesen Abschnitt ein [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) in Ihrem [Integrationskonto](./logic-apps-enterprise-integration-create-integration-account.md) für jeden Austauschvorgang aus. Nach Abschluss aller einzelnen Zeilen wird automatisch eine neue leere Zeile angezeigt.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Kontrollversionsnummer (ISA12)** | Die Version des X12-Standards. |
| **Verwendungsindikator (ISA15)** | Der Kontext eines Austauschs, bei dem es sich um **Test** daten, **Information** sdaten oder **Produktion** sdaten handelt. |
| **Schema** | Das zu verwendende Schema für die Generierung der GS- und ST-Segmente für einen X12-codierten Austausch, der an die EDI-Sendepipeline gesendet wird. |
| **GS1** | Optional. Wählen Sie den Funktionscode aus. |
| **GS2** | Optional. Geben Sie den Anwendungsabsender an. |
| **GS3** | Optional. Geben Sie den Anwendungsempfänger an. |
| **GS4** | Optional. Wählen Sie **CCYYMMDD** oder **YYMMDD** aus. |
| **GS5** | Optional. Wählen Sie **HHMM**, **HHMMSS** oder **HHMMSSdd** aus. |
| **GS7** | Optional. Wählen Sie einen Wert für die zuständige Agentur aus. |
| **GS8** | Optional. Geben Sie die Schemadokumentversion an. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Sendeeinstellungen – Kontrollnummern

![Kontrollnummern für ausgehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Austauschkontrollnummer (ISA13)** | Der Wertebereich für die Austauschkontrollnummer, der mindestens den Wert 1 und höchsten einen Wert von 999999999 haben kann. |
| **Gruppenkontrollnummer (GS06)** | Der Wertebereich für die Gruppenkontrollnummer, der mindestens den Wert 1 und höchstens einen Wert von 999999999 haben kann. |
| **Transaktionssatz-Kontrollnummer (ST02)** | Der Wertebereich für die Transaktionssatz-Kontrollnummer, der mindestens den Wert 1 und höchstens einen Wert von 999999999 haben kann. <p>- **Präfix**: Optional. Ein alphanumerischer Wert. <br>- **Suffix**: Optional. Ein alphanumerischer Wert. |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Sendeeinstellungen – Zeichensätze und Trennzeichen

![Trennzeichen für Nachrichtentypen in ausgehenden Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

In der Zeile **Standard** wird der Zeichensatz angezeigt, der als Trennzeichen für ein Nachrichtenschema verwendet wird. Wenn Sie den **Standard**-Zeichensatz nicht verwenden möchten, können Sie einen anderen Satz von Trennzeichen für jeden Nachrichtentyp eingeben. Nach Abschluss aller einzelnen Zeilen wird automatisch eine neue leere Zeile angezeigt.

> [!TIP]
> Um Werte für Sonderzeichen bereitzustellen, bearbeiten Sie die Vereinbarung als JSON, und geben Sie den ASCII-Wert für das Sonderzeichen an.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Zu verwendender Zeichensatz** | Der X12-Zeichensatz, der entweder **Basic**, **Erweitert** oder **UTF8** ist. |
| **Schema** | Das Schema, das verwendet werden soll. Nachdem Sie das Schema ausgewählt haben, wählen Sie den gewünschten Zeichensatz aus. Orientieren Sie sich dabei an den folgenden Trennzeichenbeschreibungen. |
| **Eingabetyp** | Der Eingabetyp für den Zeichensatz. |
| **Komponententrennzeichen** | Ein einzelnes Zeichen, das zusammengesetzte Datenelemente trennt. |
| **Datenelementtrennzeichen** | Ein einzelnes Zeichen, das einfache Datenelemente in zusammengesetzten Datenelementen trennt. |
| **Ersetzungstrennzeichen** | Ein Ersetzungszeichen, mit dem beim Generieren der ausgehenden X12-Nachricht alle Trennzeichen in den Nutzlastdaten ersetzt werden. |
| **Segmentabschlusszeichen** | Ein einzelnes Zeichen, das das Ende eines EDI-Segments anzeigt. |
| **Suffix** | Das Zeichen, das mit dem Segmentbezeichner verwendet werden soll. Wenn Sie ein Suffix angeben, kann das Segmentabschlusszeichen-Datenelement leer sein. Wenn das Segmentabschlusszeichen leer gelassen wird, müssen Sie ein Suffix angeben. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Sendeeinstellungen – Überprüfung

![Überprüfungseigenschaften für ausgehende Nachrichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

In der Zeile **Standard** werden die Überprüfungsregeln angezeigt, die für einen EDI-Nachrichtentyp verwendet werden. Wenn Sie unterschiedliche Regeln definieren möchten, wählen Sie jedes Feld aus, für das die Regel auf **true** festgelegt werden soll. Nach Abschluss aller einzelnen Zeilen wird automatisch eine neue leere Zeile angezeigt.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Nachrichtentyp** | Der EDI-Nachrichtentyp. |
| **EDI-Überprüfung** | Die EDI-Überprüfung erfolgt für Datentypen gemäß den EDI-Eigenschaften des Schemas, Längenbeschränkungen, leeren Datenelementen und nachfolgenden Trennzeichen. |
| **Erweiterte Überprüfung** | Wenn der Datentyp nicht EDI ist, werden die Datenelementanforderung sowie zulässige Wiederholung, Enumerationen und Datenelementlänge (Min. oder Max.) überprüft. |
| **Führende/nachfolgende Nullen zulassen** | Behält alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| **Führende/nachfolgende Nullen abschneiden** | Entfernt jegliche führende oder nachfolgende Nullen und Leerzeichen. |
| **Richtlinie für nachfolgende Trennzeichen** | Generiert nachfolgende Trennzeichen. <p>- **Nicht zulässig**: Verhindert nachfolgende Trennzeichen im ausgehenden Austauschvorgang. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>- **Optional**: Sendet Austauschvorgänge mit oder ohne nachfolgende Trennzeichen. <p>- **Obligatorisch**: Der ausgehende Austauschvorgang muss nachfolgende Trennzeichen enthalten. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA-Schemas und Nachrichtentypen

Wenn Sie mit HIPAA-Schemas und den Nachrichtentypen 277 oder 837 arbeiten, müssen Sie einige zusätzliche Schritte ausführen. Die [Dokumentversionsnummern (GS8)](#outbound-control-version-number) für diese Nachrichtentypen haben mehr als 9 Zeichen, z. B. „005010x222a1“. Außerdem sind einige Dokumentversionsnummern verschiedenen Nachrichtentypen zugeordnet. Wenn Sie in Ihrem Schema und in Ihrer Vereinbarung nicht auf den richtigen Nachrichtentyp verweisen, erhalten Sie die folgende Fehlermeldung:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

In dieser Tabelle sind die betroffenen Nachrichtentypen, alle Varianten und die Dokumentversionsnummern aufgelistet, die diesen Nachrichtentypen zugeordnet sind:

| Nachrichtentyp oder -variante |  BESCHREIBUNG | Dokumentversionsnummer (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Informationen im Gesundheitswesen: Statusbenachrichtigung (USA) | 005010X212 |
| 837_I | Geltendmachung von Ansprüchen bei der Krankenkasse – Institution (USA) | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Geltendmachung von Ansprüchen bei der Krankenkasse – Zahnmedizin (USA) | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Geltendmachung von Ansprüchen bei der Krankenkasse – Professionell (USA) | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Sie müssen außerdem die EDI-Validierung deaktivieren, wenn Sie diese Dokumentversionsnummern verwenden, weil sie zu einem Fehler führen, der besagt, dass die Zeichenlänge ungültig ist.

Um diese Dokumentversionsnummern und Nachrichtentypen anzugeben, führen Sie die folgenden Schritte aus:

1. Ersetzen Sie in Ihrem HIPAA-Schema den aktuellen Nachrichtentyp durch die Nachrichtentypvariante für die Dokumentversionsnummer, die Sie verwenden möchten.

   Angenommen, Sie möchten die Dokumentversionsnummer `005010X222A1` mit dem Nachrichtentyp `837` verwenden. Ersetzen Sie in Ihrem Schema jeden `"X12_00501_837"`-Wert stattdessen durch den Wert `"X12_00501_837_P"`.

   Um Ihr Schema zu aktualisieren, führen Sie die folgenden Schritte aus:

   1. Wechseln Sie im Azure-Portal zu Ihrem Integrationskonto. Suchen Sie Ihr Schema, und laden Sie es herunter. Ersetzen Sie den Nachrichtentyp, benennen Sie die Schemadatei um, und laden Sie Ihr überarbeitetes Schema in Ihr Integrationskonto hoch. Weitere Informationen finden Sie unter [Bearbeiten von Schemas](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Wählen Sie in den Nachrichteneinstellungen Ihrer Vereinbarung das überarbeitete Schema aus.

1. Fügen Sie im `schemaReferences`-Objekt Ihrer Vereinbarung einen weiteren Eintrag hinzu, der die Nachrichtentypvariante angibt, die Ihrer Dokumentversionsnummer entspricht.

   Angenommen, Sie möchten die Dokumentversionsnummer `005010X222A1` für den Nachrichtentyp `837` verwenden. Ihre Vereinbarung verfügt über einen `schemaReferences`-Abschnitt mit diesen Eigenschaften und Werten:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   Fügen Sie in diesem `schemaReferences`-Abschnitt einen weiteren Eintrag mit den folgenden Werten hinzu:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Wenn Sie fertig sind, sieht Ihr `schemaReferences`-Abschnitt wie folgt aus:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Deaktivieren Sie in den Nachrichteneinstellungen Ihrer Vereinbarung die EDI-Überprüfung, indem Sie das Kontrollkästchen **EDI-Überprüfung** entweder für jeden Nachrichtentyp oder für alle Nachrichtentypen deaktivieren, wenn Sie die **Standard** werte verwenden.

   ![Überprüfung für alle Nachrichtentypen oder jeden Nachrichtentyp deaktivieren](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/x12/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors die [B2B-Nachrichtengrenzwerte für ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über andere [Connectors für Logic Apps](../connectors/apis-list.md).
