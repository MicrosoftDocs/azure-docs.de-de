---
title: X12-Nachverfolgungsschemas für B2B-Nachrichten – Azure Logic Apps | Microsoft-Dokumentation
description: Erstellen von X12-Nachverfolgungsschemas zur Überwachung von B2B-Nachrichten in Integrationskonten für Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845765"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Erstellen von Schemas für die Nachverfolgung von X12-Nachrichten in Integrationskonten für Azure Logic Apps

Zur Unterstützung bei der Überwachung des Erfolgs, der Fehler und der Nachrichteneigenschaften für B2B-Transaktionen (Business-to-Buiness) können Sie in Ihrem Integrationskonto folgende X12-Nachverfolgungsschemas verwenden:

* X12-Transaktionssatz-Nachverfolgungsschema
* X12-Transaktionssatzbestätigung-Nachverfolgungsschema
* X12-Austausch-Nachverfolgungsschema
* X12-Austauschbestätigung-Nachverfolgungsschema
* X12-Funktionsgruppen-Nachverfolgungsschema
* X12-Funktionsgruppenbestätigung-Nachverfolgungsschema

## <a name="x12-transaction-set-tracking-schema"></a>X12-Transaktionssatz-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der X12-Nachricht. (Optional) |
| receiverPartnerName | String | Name des Empfangspartners der X12-Nachricht. (Optional) |
| senderQualifier | String | Qualifizierer des Sendepartners. (Erforderlich) |
| senderIdentifier | String | Bezeichner des Sendepartners. (Erforderlich) |
| receiverQualifier | String | Qualifizierer des Empfangspartners. (Obligatorisch) |
| receiverIdentifier | String | Bezeichner des Empfangspartners. (Erforderlich) |
| agreementName | String | Name der X12-Vereinbarung, nach der Nachrichten aufgelöst werden. (Optional) |
| direction | Enum | Richtung des Nachrichtenflusses (Empfangen oder Senden). (Erforderlich) |
| interchangeControlNumber | String | Austauschkontrollnummer. (Optional) |
| functionalGroupControlNumber | String | Funktionale Kontrollnummer. (Optional) |
| transactionSetControlNumber | String | Transaktionssatz-Kontrollnummer. (Optional) |
| CorrelationMessageId | String | Korrelationsnachrichten-ID. Kombination aus {AgreementName} *{GroupControlNumber}* {TransactionSetControlNumber}. (Optional) |
| messageType | String | Transaktionssatz oder Dokumenttyp. (Optional) |
| isMessageFailed | Boolean | Gibt an, ob die X12-Nachricht fehlgeschlagen ist. (Erforderlich) |
| isTechnicalAcknowledgmentExpected | Boolean | Gibt an, ob die technische Bestätigung in der X12-Vereinbarung konfiguriert ist. (Erforderlich) |
| isFunctionalAcknowledgmentExpected | Boolean | Gibt an, ob die Funktionsbestätigung in der X12-Vereinbarung konfiguriert ist. (Erforderlich) |
| needAk2LoopForValidMessages | Boolean | Gibt an, ob die AK2-Schleife für eine gültige Nachricht erforderlich ist. (Erforderlich) |
| segmentsCount | Integer | Anzahl von Segmenten im X12-Transaktionssatz. (Optional) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12-Transaktionssatzbestätigung-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der X12-Nachricht. (Optional) |
| receiverPartnerName | String | Name des Empfangspartners der X12-Nachricht. (Optional) |
| senderQualifier | String | Qualifizierer des Sendepartners. (Erforderlich) |
| senderIdentifier | String | Bezeichner des Sendepartners. (Erforderlich) |
| receiverQualifier | String | Qualifizierer des Empfangspartners. (Obligatorisch) |
| receiverIdentifier | String | Bezeichner des Empfangspartners. (Erforderlich) |
| agreementName | String | Name der X12-Vereinbarung, nach der Nachrichten aufgelöst werden. (Optional) |
| direction | Enum | Richtung des Nachrichtenflusses (Empfangen oder Senden). (Erforderlich) |
| interchangeControlNumber | String | Austauschkontrollnummer der Funktionsbestätigung. Der Wert wird nur für die Absenderseite eingefügt, wenn für die an den Partner gesendeten Nachrichten eine Funktionsbestätigung empfangen wurde. (Optional) |
| functionalGroupControlNumber | String | Funktionsgruppen-Kontrollnummer der Funktionsbestätigung. Der Wert wird nur für die Absenderseite eingefügt, wenn für die an den Partner gesendeten Nachrichten eine Funktionsbestätigung empfangen wurde. (Optional) |
| isaSegment | String | ISA-Segment der Nachricht. Der Wert wird nur für die Absenderseite eingefügt, wenn für die an den Partner gesendeten Nachrichten eine Funktionsbestätigung empfangen wurde. (Optional) |
| gsSegment | String | GS-Segment der Nachricht. Der Wert wird nur für die Absenderseite eingefügt, wenn für die an den Partner gesendeten Nachrichten eine Funktionsbestätigung empfangen wurde. (Optional) |
| respondingfunctionalGroupControlNumber | String | Austauschkontrollnummer der Antwort. (Optional) |
| respondingFunctionalGroupId | String | Funktionsgruppen-ID der Antwort, die in der Bestätigung AK101 zugeordnet ist. (Optional) |
| respondingtransactionSetControlNumber | String | Transaktionssatz-Kontrollnummer der Antwort. (Optional) |
| respondingTransactionSetId | String | Transaktionssatz-ID der Antwort an, die in der Bestätigung AK201 zugeordnet ist. (Optional) |
| statusCode | Boolean | Statuscode für die Transaktionssatzbestätigung. (Erforderlich) |
| segmentsCount | Enum | Statuscode für die Bestätigung. Zulässige Werte sind **Accepted**, **Rejected** und **AcceptedWithErrors**. (Erforderlich) |
| processingStatus | Enum | Verarbeitungsstatus der Bestätigung. Zulässige Werte sind **Received**, **Generated** und **Sent**. (Erforderlich) |
| CorrelationMessageId | String | Korrelationsnachrichten-ID. Kombination aus {AgreementName} *{GroupControlNumber}* {TransactionSetControlNumber}. (Optional) |
| isMessageFailed | Boolean | Gibt an, ob die X12-Nachricht fehlgeschlagen ist. (Erforderlich) |
| ak2Segment | String | Bestätigung für einen Transaktionssatz in der empfangenen Funktionsgruppe. (Optional) |
| ak3Segment | String | Meldet Fehler in einem Datensegment. (Optional) |
| ak5Segment | String | Meldet, ob der im AK2-Segment angegebene Transaktionssatz akzeptiert oder abgelehnt wurde (einschließlich des Grunds). (Optional) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12-Austausch-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der X12-Nachricht. (Optional) |
| receiverPartnerName | String | Name des Empfangspartners der X12-Nachricht. (Optional) |
| senderQualifier | String | Qualifizierer des Sendepartners. (Erforderlich) |
| senderIdentifier | String | Bezeichner des Sendepartners. (Erforderlich) |
| receiverQualifier | String | Qualifizierer des Empfangspartners. (Obligatorisch) |
| receiverIdentifier | String | Bezeichner des Empfangspartners. (Erforderlich) |
| agreementName | String | Name der X12-Vereinbarung, nach der Nachrichten aufgelöst werden. (Optional) |
| direction | Enum | Richtung des Nachrichtenflusses (Empfangen oder Senden). (Erforderlich) |
| interchangeControlNumber | String | Austauschkontrollnummer. (Optional) |
| isaSegment | String | ISA-Segment der Nachricht. (Optional) |
| isTechnicalAcknowledgmentExpected | Boolean | Gibt an, ob die technische Bestätigung in der X12-Vereinbarung konfiguriert ist. (Erforderlich) |
| isMessageFailed | Boolean | Gibt an, ob die X12-Nachricht fehlgeschlagen ist. (Erforderlich) |
| isa09 | String | Austauschdatum des X12-Dokuments. (Optional) |
| isa10 | String | Austauschuhrzeit des X12-Dokuments. (Optional) |
| isa11 | String | Kontrollstandardsbezeichner für den X12-Austausch. (Optional) |
| isa12 | String | Kontrollversionsnummer für den X12-Austausch an. (Optional) |
| isa14 | String | X12-Bestätigung wurde angefordert. (Optional) |
| isa15 | String | Indikator für Test oder Produktion. (Optional) |
| isa16 | String | Elementtrennzeichen. (Optional) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12-Austauschbestätigung-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der X12-Nachricht. (Optional) |
| receiverPartnerName | String | Name des Empfangspartners der X12-Nachricht. (Optional) |
| senderQualifier | String | Qualifizierer des Sendepartners. (Erforderlich) |
| senderIdentifier | String | Bezeichner des Sendepartners. (Erforderlich) |
| receiverQualifier | String | Qualifizierer des Empfangspartners. (Obligatorisch) |
| receiverIdentifier | String | Bezeichner des Empfangspartners. (Erforderlich) |
| agreementName | String | Name der X12-Vereinbarung, nach der Nachrichten aufgelöst werden. (Optional) |
| direction | Enum | Richtung des Nachrichtenflusses (Empfangen oder Senden). (Erforderlich) |
| interchangeControlNumber | String | Austauschkontrollnummer der technischen Bestätigung, die von Partnern empfangen wurde. (Optional) |
| isaSegment | String | ISA-Segment der technischen Bestätigung, die von Partnern empfangen wurde. (Optional) |
| respondingInterchangeControlNumber |String | Austauschkontrollnummer der technischen Bestätigung, die von Partnern empfangen wurde. (Optional) |
| isMessageFailed | Boolean | Gibt an, ob die X12-Nachricht fehlgeschlagen ist. (Erforderlich) |
| statusCode | Enum | Statuscode der Austauschbestätigung. Zulässige Werte sind **Accepted**, **Rejected** und **AcceptedWithErrors**. (Erforderlich) |
| processingStatus | Enum | Statuscode der Bestätigung. Zulässige Werte sind **Received**, **Generated** und **Sent**. (Erforderlich) |
| ta102 | String | Austauschdatum. (Optional) |
| ta103 | String | Austauschuhrzeit. (Optional) |
| ta105 | String | Austauschhinweiscode. (Optional) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12-Funktionsgruppen-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der X12-Nachricht. (Optional) |
| receiverPartnerName | String | Name des Empfangspartners der X12-Nachricht. (Optional) |
| senderQualifier | String | Qualifizierer des Sendepartners. (Erforderlich) |
| senderIdentifier | String | Bezeichner des Sendepartners. (Erforderlich) |
| receiverQualifier | String | Qualifizierer des Empfangspartners. (Obligatorisch) |
| receiverIdentifier | String | Bezeichner des Empfangspartners. (Erforderlich) |
| agreementName | String | Name der X12-Vereinbarung, nach der Nachrichten aufgelöst werden. (Optional) |
| direction | Enum | Richtung des Nachrichtenflusses (Empfangen oder Senden). (Erforderlich) |
| interchangeControlNumber | String | Austauschkontrollnummer. (Optional) |
| functionalGroupControlNumber | String | Funktionale Kontrollnummer. (Optional) |
| gsSegment | String | GS-Segment der Nachricht. (Optional) |
| isTechnicalAcknowledgmentExpected | Boolean | Gibt an, ob die technische Bestätigung in der X12-Vereinbarung konfiguriert ist. (Erforderlich) |
| isFunctionalAcknowledgmentExpected | Boolean | Gibt an, ob die Funktionsbestätigung in der X12-Vereinbarung konfiguriert ist. (Erforderlich) |
| isMessageFailed | Boolean | Gibt an, ob die X12-Nachricht fehlgeschlagen ist. (Erforderlich)|
| gs01 | String | Funktionsbezeichnercode. (Optional) |
| gs02 | String | Code des Anwendungsabsenders. (Optional) |
| gs03 | String | Code des Anwendungsempfängers. (Optional) |
| gs04 | String | Datum der Funktionsgruppe. (Optional) |
| gs05 | String | Uhrzeit der Funktionsgruppe. (Optional) |
| gs07 | String | Code der zuständigen Behörde. (Optional) |
| gs08 | String | Versions/Freigabe-/Branchen-ID-Code. (Optional) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12-Funktionsgruppenbestätigung-Nachverfolgungsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| senderPartnerName | String | Name des Absenderpartners der X12-Nachricht. (Optional) |
| receiverPartnerName | String | Name des Empfangspartners der X12-Nachricht. (Optional) |
| senderQualifier | String | Qualifizierer des Sendepartners. (Erforderlich) |
| senderIdentifier | String | Bezeichner des Sendepartners. (Erforderlich) |
| receiverQualifier | String | Qualifizierer des Empfangspartners. (Obligatorisch) |
| receiverIdentifier | String | Bezeichner des Empfangspartners. (Erforderlich) |
| agreementName | String | Name der X12-Vereinbarung, nach der Nachrichten aufgelöst werden. (Optional) |
| direction | Enum | Richtung des Nachrichtenflusses (Empfangen oder Senden). (Erforderlich) |
| interchangeControlNumber | String | Austauschkontrollnummer, die für die Sendeseite aufgefüllt wird, wenn eine technische Bestätigung von Partnern empfangen wird. (Optional) |
| functionalGroupControlNumber | String | Funktionsgruppen-Kontrollnummer der technischen Bestätigung, die für die Sendeseite aufgefüllt wird, wenn eine technische Bestätigung von Partnern empfangen wird. (Optional) |
| isaSegment | String | Entspricht der Austauschkontrollnummer, wird aber nur in bestimmten Fällen aufgefüllt. (Optional) |
| gsSegment | String | Entspricht der Funktionsgruppen-Kontrollnummer, wird aber nur in bestimmten Fällen aufgefüllt. (Optional) |
| respondingfunctionalGroupControlNumber | String | Kontrollnummer der ursprünglichen Funktionsgruppe. (Optional) |
| respondingFunctionalGroupId | String | Wird in der Funktionsgruppen-ID der Bestätigung AK101 zugeordnet. (Optional) |
| isMessageFailed | Boolean | Gibt an, ob die X12-Nachricht fehlgeschlagen ist. (Erforderlich) |
| statusCode | Enum | Statuscode für die Bestätigung. Zulässige Werte sind **Accepted**, **Rejected** und **AcceptedWithErrors**. (Erforderlich) |
| processingStatus | Enum | Verarbeitungsstatus der Bestätigung. Zulässige Werte sind **Received**, **Generated** und **Sent**. (Erforderlich) |
| ak903 | String | Anzahl der empfangenen Transaktionssätze. (Optional) |
| ak904 | String | Anzahl von Transaktionssätzen an, die in der identifizierten Funktionsgruppe akzeptiert wurden. (Optional) |
| ak9Segment | String | Gibt an, ob die im AK1-Segment angegebene Funktionsgruppe akzeptiert oder abgelehnt wurde (einschließlich des Grunds). (Optional) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B-Protokollnachverfolgungsschemas

Informationen zu B2B-Protokollnachverfolgungsschemas finden Sie unter:

* [AS2-Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Benutzerdefinierte B2B-Nachverfolgungsschemas](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Überwachen von B2B-Nachrichten](logic-apps-monitor-b2b-message.md).
* Informieren Sie sich über das [Nachverfolgen von B2B-Nachrichten in Azure Monitor-Protokollen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
