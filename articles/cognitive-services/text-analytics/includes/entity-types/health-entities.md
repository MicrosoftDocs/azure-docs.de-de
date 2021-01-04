---
title: Benannte Entitäten für das Gesundheitswesen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 308b2a9cb00f44f0e330d4fef5592d8855ee3394
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553156"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>Textanalyse für Kategorien, Entitäten und Attribute im Gesundheitswesen

[Gewusst wie: Verwenden von Text Analytics for Health (Vorschauversion)](../../how-tos/text-analytics-for-health.md) erkennt medizinische Begriffe in den folgenden Kategorien.  (Beachten Sie, dass in dieser Containervorschau nur englischer Text unterstützt wird und nur eine einzige Modellversion in jedem Containerimage bereitgestellt wird.)


| Kategorie  | Beschreibung  |
|---------|---------|
| ANATOMY | Begriffe zur Erfassung von Informationen über den Körper und anatomische Systeme und Bereiche. |
 | DEMOGRAPHICS | Begriffe, die Informationen über Geschlecht und Alter erfassen. |
 | EXAMINATION | Begriffe, die Informationen zu Diagnoseverfahren und Tests erfassen. |
 | GENOMICS | Begriffe, die Informationen zu Genen und Varianten erfassen. |
 | HEALTHCARE | Begriffe, die Informationen zu Verwaltungsvorgängen, Pflegeumgebungen und Berufen im Gesundheitswesen erfassen. |
 | MEDICAL CONDITION | Begriffe, die Informationen zu Diagnoseverfahren, Symptomen oder Anzeichen erfassen. |
 | MEDICATION | Begriffe, die Informationen zu Medikamenten einschließlich Name, Klasse, Dosierung und Verwaltungsabläufen erfassen. |
 | SOCIAL | Begriffe, die Informationen über medizinisch relevante soziale Aspekte wie z. B. Verwandschaftsverhältnisse erfassen. |
 | TREATMENT | Begriffe, die Informationen zu Therapieverfahren erfassen. |
  
Jede Kategorie kann zwei Begriffsgruppen beinhalten:

* **Entitäten**: Benennungen, die medizinische Begriffe wie Diagnose, Medikamentenname oder den Namen der Behandlung erfassen.  Beispielsweise ist *Bronchitis* eine Diagnose und *Aspirin* ein Medikamentenname.  Erwähnungen in dieser Gruppe sind möglicherweise mit einer UMLS-Begriffs-ID verknüpft.
* **Attribute**: Ausdrücke, die weitere Informationen zur erkannten Entität bieten; *schwerwiegende* ist z. B. ein Bedingungsqualifizierer für *Bronchitis*, und *81 mg* ist eine Dosierung für *Aspirin*.  Erwähnungen in dieser Kategorie werden NICHT mit einer UMLS-Begriffs-ID verknüpft.

Außerdem erkennt der Dienst Beziehungen zwischen den verschiedenen Begriffen, einschließlich der Beziehungen zwischen Attributen und Entitäten, z. B. *Richtung* und *Körperstruktur* oder *Dosierung* und *Medikamentenname* und Beziehungen zwischen Entitäten, z. B. beim Erkennen von Abkürzungen.

## <a name="anatomy"></a>Aufbau

### <a name="entities"></a>Entitäten

**BODY_STRUCTURE**: Körpersysteme, anatomische Bereiche und Körperregionen. Beispielsweise „Arm“, „Knie“, „Bauch“, „Nase“, „Leber“, „Kopf“, „Atemsystem“, „Lymphozyten“.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Ein Beispiel für die Körperstrukturentität.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Ein erweitertes Beispiel für die Körperstrukturentität.":::

### <a name="attributes"></a>Attribute

**DIRECTION**: Richtungsbenennungen wie „links“, „seitlich“, „oben“, „hinten“, die eine Körperstruktur kennzeichnen.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Ein Beispiel für ein Richtungsattribut.":::

### <a name="supported-relations"></a>Unterstützte Beziehungen

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Demografische Informationen

### <a name="entities"></a>Entitäten

**AGE**: Alle Benennungen und Ausdrücke in Bezug auf das Alter von Patienten, Familienmitgliedern und anderen. Beispielsweise „40-jährig“, „51 Jahre“, „3 Monate alt“, „erwachsen“, „älterer“, „jung“, „minderjährig“, „mittleren Alters“.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Ein Beispiel für eine Altersentität.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Ein weiteres Beispiel für eine Altersentität.":::


**GENDER**: Benennungen, die das Geschlecht der Person angeben. Beispielsweise „männlich“, „weiblich“, „Frau“, „Herr“, „Dame“.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Ein Beispiel für eine Geschlechtsentität.":::

### <a name="attributes"></a>Attribute

**RELATIONAL_OPERATOR**: Ausdrücke, die die Beziehung zwischen einer demographischen Entität und zusätzlichen Informationen ausdrücken.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Ein Beispiel für einen relationalen Operator.":::

## <a name="examinations"></a>Untersuchungen

### <a name="entities"></a>Entitäten

**EXAMINATION_NAME**: Diagnoseverfahren und Tests. Beispielsweise „MRT“, „EKG“, „HIV-Test“, „Hämoglobin“, „Blutplättchenanzahl“, „Skalensysteme“ wie *Bristol-Stuhlformen-Skala*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Ein Beispiel für eine Untersuchungsentität.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Ein weiteres Beispiel für eine Untersuchungsnamenentität.":::

### <a name="attributes"></a>Attribute

**DIRECTION**: Richtungsbenennungen, die eine Untersuchung charakterisieren.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Ein Beispiel für ein Richtungsattribut mit einer Untersuchungsnamenentität.":::

**MEASUREMENT_UNIT**: Die Maßeinheit für eine Untersuchung. Beispielsweise ist in *Hämoglobin > 9,5 g/dl* die Benennung *g/dl* die Einheit für den *Hämoglobintest*.

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Ein Beispiel für ein Maßeinheitsattribut mit einer Untersuchungsnamenentität.":::

**MEASUREMENT_VALUE**: Der Wert für eine Untersuchung. Beispielsweise ist in *Hämoglobin > 9,5 g/dl* die Benennung *9,5* der Wert für den *Hämoglobintest*.

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Ein Beispiel für ein Messwertsattribut mit einer Untersuchungsnamenentität.":::

**RELATIONAL_OPERATOR**: Ausdrücke, die die Beziehung zwischen einer Untersuchung und zusätzlichen Informationen ausdrücken. Beispielsweise der erforderliche Messwert für eine Untersuchung.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Ein Beispiel für einen relationalen Operator mit einer Untersuchungsnamenentität.":::

**TIME**: Zeitliche Benennungen, die sich auf den Anfang und/oder die Dauer einer Untersuchung beziehen. Beispielsweise, wann der Test stattgefunden hat.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Ein Beispiel für ein Zeitattribut mit einer Untersuchungsnamenentität.":::

### <a name="supported-relations"></a>Unterstützte Beziehungen

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entitäten

**GENE**: Alle Erwähnungen von Genen. Beispiel: „MTRR“, „F2“.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Ein Beispiel für eine Genentität.":::

**VARIANT**: Alle Erwähnungen von Genvarianten. Beispielsweise „c.524C>T“, „(MTRR):r.1462_1557del96“.
  
## <a name="healthcare"></a>Integritätsressourcen

### <a name="entities"></a>Entitäten
  
**ADMINISTRATIVE_EVENT**: Ereignisse in Bezug auf das Gesundheitswesen, aber (teilweise) administrativ. Beispielsweise „Registrierung“, „Zulassung“, „Testversion“, „Studieneintrag“, „Überweisung“, „Entlassung“, „Krankenhauseinweisung“, „Krankenhausaufenthalt“. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Ein Beispiel für eine Ereignisentität im Gesundheitswesen.":::

**CARE_ENVIRONMENT**: Eine Umgebung oder ein Ort, wo Patienten versorgt werden. Beispielsweise „Notaufnahme“, „Arztpraxis“, „kardiologische Station“, „Hospiz“, „Krankenhaus“.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Screenshot: Beispiel für eine Umgebungsentität im Gesundheitswesen":::

**HEALTHCARE_PROFESSION**: Praktizierender im Gesundheitswesen. Beispielsweise „Zahnarzt“, „Pathologe“, „Neurologe“, „Radiologe“, „Apotheker“, „Ernährungsberater“, „Physiotherapeut“, „Chiropraktiker“.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Screenshot: Weiteres Beispiel für eine Umgebungsentität im Gesundheitswesen":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Ein weiteres Beispiel für eine Umgebungsentität im Gesundheitswesen.":::

## <a name="medical-condition"></a>Medizinischer Zustand

### <a name="entities"></a>Entitäten

**DIAGNOSIS**: Krankheit, Syndrom, Vergiftung. Beispielsweise „Brustkrebs“, „Alzheimer“, „Bluthochdruck“, „Herzinsuffizienz“, „Wirbelsäulenverletzung“.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Ein Beispiel für eine Entität für den medizinischen Zustand.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Ein weiteres Beispiel für eine Entität für den medizinischen Zustand.":::

**SYMPTOM_OR_SIGN**: Subjektiver oder objektiver Nachweis von Krankheiten oder sonstige Diagnosen. Beispielsweise „Brustschmerzen“, „Kopfschmerzen“, „Schwindel“, „Effloreszenz“, „Dyspnoe“, „gute Darmgeräusche“, „gut genährt“.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Ein Beispiel für eine Anzeichen- oder Symptomentität für den medizinischen Zustand.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Ein weiteres Beispiel für eine Anzeichen- oder Symptomentität für den medizinischen Zustand.":::

### <a name="attributes"></a>Attribute

**CONDITION_QUALIFIER**: Qualitätsbenennungen, die zur Beschreibung eines medizinischen Zustands verwendet werden. Alle folgenden Unterkategorien gelten als berücksichtigte Qualifizierer:

1.  Zeitbezogene Ausdrücke: Dies sind Benennungen, die die Zeitdimension qualitativ beschreiben, z. B. „plötzlich“, „akut“, „chronisch“, „langjährig“. 
2.  Qualitätsausdrücke:  Dabei handelt es sich um Benennungen, die die „Art“ des medizinischen Zustands beschreiben, z. B. „brennen“, „scharf“.
3.  Schweregradausdrücke: „schwerwiegend“, „mild“, „etwas“, „unkontrolliert“.
4.  Ausdehnungsausdrücke: „lokal“, „zentriert“, „diffus“.
5.  Ausstrahlungsausdrücke: „strahlt aus“, „Ausstrahlung“.
6.  Zustandsskala: In einigen Fällen ist ein Zustand durch eine Skala gekennzeichnet, bei der es sich um eine endliche geordnete Liste von Werten handelt. Beispielsweise „Patienten mit Bauchspeicheldrüsenkrebs im Stadium III“.
7.  Zustandsverlauf: Eine Benennung, die sich auf den Verlauf oder Fortschritt eines Zustands bezieht, z. B. „Verbesserung“, „Verschlechterung“, „Überwinden“, „Nachlassen“. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Ein Beispiel für ein Zustandsqualifiziererattribut und eine Diagnoseentität.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Ein weiteres Beispiel für ein Zustandsqualifiziererattribut und eine Diagnoseentität.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Ein Beispiel für ein Zustandsqualifiziererattribut mit Symptom- und Medikamententitäten.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Screenshot: Weiteres Beispiel für ein Zustandsqualifiziererattribut mit einer Diagnoseentität":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Screenshot: Weiteres Beispiel für ein Zustandsqualifiziererattribut mit einer Diagnoseentität":::

**DIRECTION**: Auf den Körper bezogene Richtungsbenennungen, die einen medizinischen Zustand charakterisieren.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Ein Beispiel für ein Richtungsattribut mit einer Entität für den medizinischen Zustand.":::

**FREQUENCY**: Häufigkeit, in der ein medizinischer Zustand aufgetreten ist, auftritt oder auftreten sollte.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Ein Beispiel für ein Häufigkeitsattribut mit einer Entität für den medizinischen Zustand.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Ein Beispiel für ein Richtungsattribut mit einer Symptom- oder Anzeichenentität.":::

**MEASUREMENT_UNIT**: Die Einheit, die einen medizinischen Zustand kennzeichnet. Beispielsweise ist in *1,5x2x1 cm-Tumor* die Benennung *cm* die Maßeinheit für den *Tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Ein Beispiel für ein Maßeinheitsattribut mit einer Entität für den medizinischen Zustand.":::

**MEASUREMENT_VALUE**: Der Wert, der einen medizinischen Zustand kennzeichnet. Beispielsweise ist in *1,5x2x1 cm-Tumor* die Benennung *1,5x2x1* der Messwert für den *Tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Screenshot: Beispiel für ein Richtungsattribut mit einer Symptom- oder Anzeichenentität":::

**RELATIONAL_OPERATOR**: Ausdrücke, die die Beziehung zwischen einem medizinischen Zustand und zusätzlichen Informationen ausdrücken. Beispiel: Zeit oder Messwert. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Screenshot: Weiteres Beispiel für ein Richtungsattribut mit einer Symptom- oder Anzeichenentität":::

**TIME**: Zeitliche Benennungen, die sich auf den Anfang und/oder die Dauer eines medizinischen Zustands beziehen. Beispielsweise der Zeitpunkt, zu dem ein Symptom oder eine Krankheit aufgetreten ist.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Screenshot: Weiteres Beispiel für ein Richtungsattribut mit einer Symptom- oder Anzeichenentität":::

### <a name="supported-relations"></a>Unterstützte Beziehungen

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Medikament

### <a name="entities"></a>Entitäten

**MEDICATION_CLASS**: Eine Gruppe von Medikamenten, die einen ähnlichen Aktionsmechanismus, einen verwandten Aktionsmodus, eine ähnliche chemische Struktur haben und/oder zur Behandlung derselben Krankheit verwenden. Beispielsweise „ACE-Hemmer“, „Opioid“, „Antibiotika“, „Mittel zur Schmerzlinderung“.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Ein Beispiel für eine Medikamentenklassenentität.":::

**MEDICATION_NAME**: Erwähnungen von Medikamenten einschließlich urheberrechtlich geschützter Markennamen und Namen, die keine Markennamen sind. Beispielsweise „Advil“, „Ibuprofen“.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Ein Beispiel für eine Medikamentennamenentität.":::

### <a name="attributes"></a>Attribute

**DOSAGE**: Menge der bestellten Medikamente. Beispielsweise „*1.000 ml* Natriumchlorid-Infusionslösung“.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Ein Beispiel für ein Medikamentdosierungsattribut.":::

**FREQUENCY**: Häufigkeit, mit der ein Medikament eingenommen werden sollte.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Ein Beispiel für ein Attribut zur Häufigkeit der Medikamenteneinnahme.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Ein weiteres Beispiel für ein Attribut zur Häufigkeit der Medikamenteneinnahme.":::

**MEDICATION_FORM**: Die Form des Medikaments. Beispielsweise „Lösung“, „Pille“, „Kapsel“, „Tablette“, „Pflaster“, „Gel“, „Paste“, „Schaum“, „Spray“, „Drops“, „Creme“, „Sirup“.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Ein Beispiel für ein Medikamentenformattribut.":::

**MEDICATION_ROUTE**: Die Methode der Medikamenteneinnahme. Beispielsweise „oral“, „vaginal“, „IV“, „epidural“, „inhalieren“.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Ein Beispiel für ein Attribut für die Methode der Medikamenteneinnahme.":::

**RELATIONAL_OPERATOR**: Ausdrücke, die die Beziehung zwischen Medikamenten und zusätzlichen Informationen ausdrücken. Beispielsweise der erforderliche Messwert.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Screenshot: Beispiel für ein Relationaler-Operator-Attribut mit einer Medikamententität":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Screenshot: Weiteres Beispiel für ein Relationaler-Operator-Attribut mit einer Medikamententität":::

### <a name="supported-relations"></a>Unterstützte Beziehungen

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**
  
## <a name="treatment"></a>Behandlung

### <a name="entities"></a>Entitäten

**TREATMENT_NAME**: Therapeutische Verfahren. Beispielsweise „Knieprothesenchirurgie“, „Knochenmarktransplantation“, „TAVI“, „Diät“.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Ein Beispiel für eine Behandlungsnamenentität.":::

### <a name="attributes"></a>Attribute

**DIRECTION**: Richtungsbenennungen, die eine Behandlung charakterisieren.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Screenshot: Beispiel für ein Behandlungsrichtungsattribut":::

**FREQUENCY**: Häufigkeit, mit der eine Behandlung durchgeführt wird oder durchgeführt werden sollte.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Screenshot: Weiteres Beispiel für ein Behandlungsrichtungsattribut":::
 
**RELATIONAL_OPERATOR**: Ausdrücke, die die Beziehung zwischen der Behandlung und zusätzlichen Informationen ausdrücken.  Beispielsweise, wieviel Zeit seit der vorherigen Behandlung vergangen ist.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Ein Beispiel für ein Relationaler-Operator-Attribut für eine Behandlung.":::

**TIME**: Zeitliche Benennungen, die sich auf den Anfang und/oder die Dauer einer Behandlung beziehen. Beispielsweise das Datum, an dem die Behandlung erfolgte.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Screenshot: Beispiel für ein Behandlungszeitattribut":::


### <a name="supported-relations"></a>Unterstützte Beziehungen

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**

## <a name="social"></a>Soziale Netzwerke

### <a name="entities"></a>Entitäten

**FAMILY_RELATION**: Erwähnungen von Familienangehörigen des Patienten. Beispielsweise „Vater“, „Tochter“, „Geschwister“, „Eltern“.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Screenshot: Weiteres Beispiel für ein Behandlungszeitattribut":::
