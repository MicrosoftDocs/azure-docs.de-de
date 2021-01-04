---
title: Identifikationsentitäten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779235"
---
Diese Entitätskategorie umfasst Finanzinformationen und offizielle Formen der Identifikation. Erst ab der Modellversion `2019-10-01` verfügbar. Die Untertypen sind im Folgenden aufgeführt. 

### <a name="financial-account-identification"></a>Identifikation bei Finanzkonten

| Name des Untertyps               | BESCHREIBUNG                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-Nummern        | ABA-Nummern (American Bankers Association) für den Zahlungsverkehr mit US-amerikanischen Banken.                  |
| SWIFT-Code                 | SWIFT-Codes in Zahlungsanweisungen.                           |
| Kreditkarte                | Kreditkartennummern                                                       |
| IBAN (International Banking Account Number)                  | IBAN-Codes in Zahlungsanweisungen.                            |


### <a name="government-and-countryregion-specific-identification"></a>Behördliche und landes-/regionsspezifische Informationen zur Identifikation

> [!NOTE]
> Die folgenden finanziellen und länderspezifischen Entitäten werden mit dem Parameter `domain=phi` nicht zurückgegeben:
> * Reisepassnummern
> * Steuernummern

Die folgenden Entitäten sind nach Land/Region gruppiert und aufgelistet:

Argentinien
* Nationale argentinische ID-Nummer (DNI)

Österreich
* Österreichischer Personalausweis
* Österreichische Abgabenkontonummer
* Österreichische Umsatzsteuer-Identifikationsnummer

Australien
* Australische Bankkontonummer
* Australische Geschäftsnummer
* Australische Unternehmensnummer
* Australische Führerscheinnummer
* Australische Krankenversicherungsnummer
* Australische Reisepassnummer
* Australische Steuernummer

Belgien
* Belgische Nationalregisternummer
* Belgische Umsatzsteuer-Identifikationsnummer

Brasilien 
* Brasilianische Nummer juristischer Personen (CNPJ)
* Brasilianische CPF-Nummer
* Nationale brasilianische ID-Karte (RG)

Bulgarien
* Bulgarische Personenkennziffer

Canada
* Kanadische Bankkontonummer
* Kanadische Führerscheinnummer
* Nummer des kanadischen Gesundheitsdiensts
* Kanadische Reisepassnummer
* Persönliche kanadische Krankenversicherungsnummer (PHIN, Personal Health Identification Number)
* Kanadische Sozialversicherungsnummer

Chile
* ID-Kartennummer 

China
* Chinesische Anwohner-ID-Kartennummer (VRC)

Kroatien
* Kroatische ID-Kartennummer
* Kroatische Personalausweisnummer
* Kroatische persönliche ID-Nummer (OIB)

Zypern
* Zypriotische Personalausweisnummer
* Zypriotische Steueridentifikationsnummer

Tschechische Republik
* Tschechische persönliche Identifikationsnummer

Dänemark
* Dänische persönliche ID-Nummer

Estland
* Estnischer persönlicher ID-Code

Europäische Union (EU)
* EU-Debitkartennummer
* EU-Führerscheinnummer
* EU-Personalausweisnummer
* EU-Reisepassnummer
* EU-Sozialversicherungsnummer (SSN) oder entsprechende ID
* EU-Steueridentifikationsnummer (TIN)

Finnland
* Europäische Krankenversicherungsnummer Finnland
* Nationale finnische ID-Nummer
* Finnische Reisepassnummer

Frankreich
* Französische Führerscheinnummer
* Französische Krankenversicherungsnummer
* Französischer Personalausweis (Carte Nationale d'Identité, CNI)
* Französische Reisepassnummer
* Französische Sozialversicherungsnummer (NIRPP)
* Französische Steueridentifikationsnummer (SPI)
* Französische Umsatzsteuer-Identifikationsnummer

Deutschland
* Deutsche Führerscheinnummer
* Deutsche Personalausweisnummer
* Deutsche Reisepassnummer
* Deutsche Steueridentifikationsnummer
* Deutsche Umsatzsteuer-Identifikationsnummer

Griechenland 
* Griechische Personalausweisnummer
* Griechische Steueridentifikationsnummer

Hongkong
* ID-Kartennummer aus Hongkong (HKID)

Ungarn
* Ungarische nationale Identifikationsnummer
* Ungarische Steueridentifikationsnummer
* Ungarische Umsatzsteuer-Identifikationsnummer

Indien
* Indische PAN (Permanent Account Number)
* Indische eindeutige ID-Nummer (Aadhaar)

Indonesien
* Indonesische ID-Kartennummer (KTP)

Irland
* Irische Sozialversicherungsnummer (PPS, Personal Public Service)

Israel
* Nationale israelische ID-Nummer
* Israelische Bankkontonummer

Italien
* Italienische Führerscheinnummer
* Italienische Steuernummer
* Italienische Umsatzsteuer-Identifikationsnummer

Japan
* Japanische Bankkontonummer
* Japanische Führerscheinnummer
* Japanische „Meine Nummer“, persönlich
* Japanische „Meine Nummer“, geschäftlich
* Japanische Melderegisternummer
* Japanische Aufenthaltskartennummer
* Japanische Sozialversicherungsnummer (SIN)
* Japanische Reisepassnummer

Lettland
* Lettischer persönlicher Code

Litauen
* Litauischer persönlicher Code

Luxemburg
* Luxemburgische nationale Identifikationsnummer (natürliche Personen)
* Luxemburgische nationale Identifikationsnummer (juristische Personen)

Malaysia
* Malaysische ID-Kartennummer

Malta
* Maltesische ID-Kartennummer
* Maltesische Steueridentifikationsnummer

Niederlande
* Niederländische persönliche Identifikationsnummer (BSN)
* Niederländische Steueridentifikationsnummer
* Niederländische Umsatzsteuer-Identifikationsnummer

Neuseeland
* Neuseeländische Bankkontonummer
* Neuseeländische Führerscheinnummer
* Neuseeländische Steuernummer
* Neuseeländische Nummer des Gesundheitsministeriums
* Neuseeländische Sozialversicherungsnummer

Norwegen
* Norwegische ID-Nummer

Philippinen
* Philippinische einheitliche Mehrzweck-ID-Nummer (Unified Multi-Purpose ID)

Polen
* Polnische Identitätskarte
* Nationale polnische ID-Nummer (PESEL)
* Polnische Reisepassnummer
* Polnische REGON-Nummer
* Polnische Steueridentifikationsnummer

Portugal 
* Portugiesische ID-Kartennummer (Citizen Card)
* Portugiesische Steueridentifikationsnummer

Rumänien
* Rumänische Personenidentifikationsnummer (CNP)

Russland
* Rumänische Reisepassnummer (Inland)
* Rumänische Reisepassnummer (International)

Saudi-Arabien
* Nationale saudi-arabische ID-Nummer

Singapur
* Singapur: Nationale Registrierungs-ID-Kartennummer (NRIC)

Slowakei 
* Slowakische Personennummer

Slowenien
* Slowenische Steueridentifikationsnummer
* Slowenische eindeutige Personenidentifkationsnummer

Südafrika
* Südafrikanische ID-Nummer

Südkorea
* Südkoreanische Einwohnerregistrierungsnummer (Resident Registration Number)

Spanien 
* Spanischer Personalausweis (DNI)
* Spanische Sozialversicherungsnummer (SSN)
* Spanische Steueridentifikationsnummer

Schweden
* Nationale schwedische ID-Nummer
* Schwedische Reisepassnummer
* Schwedische Steueridentifikationsnummer

Schweiz
* Schweizerische Sozialversicherungsnummer (AHV)

Taiwan 
* Nationale taiwanesische ID-Nummer
* Taiwanesisches Einwohnerzertifikat (ARC/TARC, Resident Certificate)
* Taiwanesische Reisepassnummer

Thailand
* Thailändische Personenidentifizierungscode

Türkei
* Türkische nationale Identifikationsnummer

Ukraine
* Ukrainische Reisepassnummer (Inland)
* Ukrainische Reisepassnummer (International)

United Kingdom
* UK- Führerscheinnummer
* UK- Wählerverzeichnisnummer
* UK- National Health Service-Nummer (NHS)
* UK- Sozialversicherungsnummer (NINO)
* UK- Reisepassnummer
* UK- Eindeutige Steuerzahlerreferenznummer

USA
* USA US-Sozialversicherungsnummer (Social Security Number, SSN)
* USA Führerscheinnummer
* USA Reisepassnummer
* USA Steuernummer (Individual Taxpayer Identification Number, ITIN)
* USA DEA-Nummer
* USA Bankkontonummer
