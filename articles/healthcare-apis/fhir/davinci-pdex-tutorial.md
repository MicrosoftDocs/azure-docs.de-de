---
title: 'Tutorial : DaDex PDex – Azure API for FHIR'
description: Dieses Tutorial führt Sie durch die Einrichtung der Azure API for FHIR zum Bestehen von Tests für den Da Payer Data Exchange-Implementierungsleitfaden.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/07/2021
ms.openlocfilehash: 454b049a90bcf6e1d1793606a8759222698e5697
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751107"
---
# <a name="da-vinci-pdex"></a>DaDex PDex

In diesem Tutorial wird das Einrichten der Azure API for FHIR zum Bestehen der [Touchstone-Tests](https://touchstone.aegis.net/touchstone/) für das [Da Payer Data Exchange Implementation Guide](http://hl7.org/fhir/us/davinci-pdex/toc.html) (PDex IG) durchlaufen.

> [!NOTE]
> Für alle diese Tests führen wir sie für die JSON-Tests aus. Die Azure API for FHIR unterstützt sowohl JSON als auch XML, verfügt jedoch nicht über separate Endpunkte für den Zugriff auf JSON oder XML. Aus diesem Grund schlagen alle XML-Tests fehl. Wenn Sie die Funktionsanweisung in XML anzeigen möchten, übergeben Sie einfach den \_ Formatparameter \` GET {fhirurl}/metadata? \_ format=xml\`

## <a name="touchstone-capability-statement"></a>Touchstone-Funktions-Anweisung

Der erste Satz von Tests, auf den wir uns konzentrieren werden, besteht darin, die Azure API for FHIR mit der PDex IG-Funktions-Anweisung zu testen. Dies umfasst drei Tests:

* Der erste Test überprüft die grundlegende Funktions-Anweisung anhand der IG-Anforderungen und wird ohne Updates bestanden.

* Der zweite Test überprüft, ob alle Profile für US Core hinzugefügt wurden. Dieser Test wird ohne Updates bestanden, enthält jedoch eine Reihe von Warnungen. Damit diese Warnungen entfernt werden, müssen Sie [die US Core-Profile laden.](validation-against-profiles.md) Wir haben eine [HTTP-Beispieldatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) erstellt, die die Erstellung aller Profile durchläuft. Sie können die [Profile](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) auch direkt von der HL7-Website abrufen, die über die aktuellsten Versionen verfügt.

* Der dritte Test überprüft, ob der [vorgang $patient-all](patient-everything.md) unterstützt wird. Im Moment schlägt dieser Test fehl. Der Vorgang ist Mitte Juni 2021 im Azure API for FHIR verfügbar und jetzt auf dem Open-Source-FHIR-Server auf Cosmos DB verfügbar. Es fehlt jedoch in der Capability-Anweisung, sodass dieser Test fehlschlägt, bis wir eine Korrektur für Fehler [1989](https://github.com/microsoft/fhir-server/issues/1989)veröffentlichen. 

 
:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-failed.png" alt-text="Fehler bei der PDex-Ausführung.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-Match-Test

Der [zweite Test](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) im Abschnitt "Payer Data Exchange" testet das Vorhandensein des [$member-Match-Vorgangs](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html). Weitere Informationen zum $member-Match-Vorgang finden Sie in unserer [Übersicht über $member-Match-Vorgänge.](tutorial-member-match.md)

In diesem Test müssen Sie einige Beispieldaten laden, damit der Test erfolgreich ist. Hier ist eine [Restdatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) mit dem Patienten und der Abdeckung verknüpft, die Sie für den Test benötigen. Nachdem diese Daten geladen wurden, können Sie diesen Test erfolgreich bestehen. Wenn die Daten nicht geladen sind, erhalten Sie eine 422-Antwort, da keine genaue Übereinstimmung gefunden wurde.

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-passed.png" alt-text="DaDex PDex-Testskript erfolgreich.":::

## <a name="touchstone-patient-by-reference"></a>Touchstone-Patient als Referenz

Die nächsten Tests, die wir überprüfen, sind die [Patienten anhand von Referenztests.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) Mit diesem Satz von Tests wird überprüft, ob Sie einen Patienten anhand verschiedener Suchkriterien finden können. Die beste Möglichkeit, den Patienten anhand einer Referenz zu testen, ist das Testen mit Ihren eigenen Daten, aber wir haben eine [Beispielressourcendatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) hochgeladen, die Sie auch laden können.

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da Die PDex-Ausführung wurde erfolgreich ausgeführt.":::

## <a name="touchstone-patienteverything-test"></a>Touchstone–Patient/$everything-Test

Der letzte Test, den wir durchlaufen, ist das Testen von Patient-Everything. Für diesen Test müssen Sie einen Patienten laden, und dann verwenden Sie die ID dieses Patienten, um zu testen, ob Sie den $everything Vorgang verwenden können, um alle Daten zu pullen, die sich auf den Patienten beziehen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir schritt für Schritt erfahren, wie Sie die Exchange-Tests für zahlende Unternehmen in Touchstone bestehen. Als Nächstes erfahren Sie mehr über alle Azure API for FHIR Features.

>[!div class="nextstepaction"]
>[Unterstützte Features](fhir-features-supported.md)  