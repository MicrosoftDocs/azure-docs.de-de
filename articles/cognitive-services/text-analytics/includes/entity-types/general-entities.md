---
title: Allgemeine benannte Entitäten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500127"
---
Das NER-Feature (Erkennung benannter Entitäten) für die Textanalyse gibt die folgenden allgemeinen (nicht identifizierenden) Entitätskategorien zurück. Dies ist beispielsweise beim Senden von Anforderungen an den `/entities/recognition/general`-Endpunkt der Fall.


| Category | BESCHREIBUNG                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | Namen von Personen  |
| [PersonType](#category-persontype) | Von einer Person eingenommene Position oder Rolle |
| [Location](#category-location)    | Natürliche und von Menschen hergestellte Wahrzeichen, Gebäude, geografische Merkmale und geopolitische Entitäten |
| [Organisation](#category-organization)  | Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen  |
| [Event](#category-event)  | Historische Ereignisse, gesellschaftliche Ereignisse und Naturereignisse |
| [Produkt](#category-product) | Physische Objekte verschiedener Kategorien. |
| [Skill](#category-skill) | Eine Funktion, Kenntnis oder ein Fachwissen  |
| [Adresse](#category-address) | Vollständige Postanschrift  |
| [Telefonnummer](#category-phonenumber) | Telefonnummern |
| [E-Mail](#category-email) | E-Mail-Adressen |
| [URL](#category-url) | URLs zu Websites |
| [IP](#category-ip) | Netzwerk-IP-Adressen |
| [DateTime](#category-datetime) | Datums- und Uhrzeitangaben |
| [Menge](#category-quantity) | numerische Messungen und Einheiten |


### <a name="category-person"></a>Kategorie: Person

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Person

    :::column-end:::
    :::column span="2":::
        **Details**

        Namen von Personen
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategorie: PersonType

Diese Kategorie enthält die folgende Entität:


:::row:::
    :::column span="":::
        **Entität**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Details**

        Von einer Person eingenommene Position oder Rolle
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Kategorie: Standort

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Standort

    :::column-end:::
    :::column span="2":::
        **Details**

        natürliche und von Menschen hergestellte Wahrzeichen, Gebäude, geografische Merkmale und geopolitische Entitäten
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Geopolitische Entität (GPE)

    :::column-end:::
    :::column span="2":::
        **Details**

        Städte, Länder/Regionen, Staaten
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Strukturell

    :::column-end:::
    :::column span="2":::

        Von Menschen geschaffene Strukturen 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geografisch

    :::column-end:::
    :::column span="2":::

        Geografische und natürliche Merkmale wie Flüsse, Ozeane und Wüsten
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Kategorie: Organization

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Organization

    :::column-end:::
    :::column span="2":::
        **Details**

        Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen Nationalitäten und Religionen werden in diesem Entitätstyp nicht berücksichtigt.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Medizin

    :::column-end:::
    :::column span="2":::
        **Details**

        Medizinische Unternehmen und Gruppen
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Börse

    :::column-end:::
    :::column span="2":::

        Börsengruppen 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportbezogene Organisationen
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Kategorie: Ereignis

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Ereignis

    :::column-end:::
    :::column span="2":::
        **Details**

        Historische Ereignisse, gesellschaftliche Ereignisse und Naturereignisse
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` und `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Kultur

    :::column-end:::
    :::column span="2":::
        **Details**

        Kulturveranstaltungen und Feiertage
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        Naturereignisse
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportveranstaltungen
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Kategorie: Produkt

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Produkt

    :::column-end:::
    :::column span="2":::
        **Details**

        Physische Objekte verschiedener Kategorien.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Rechenergebnisse
    :::column-end:::
    :::column span="2":::
        **Details**

        Rechenergebnisse
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Kategorie: Skill

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Skill

    :::column-end:::
    :::column span="2":::
        **Details**

        Eine Funktion, Kenntnis oder ein Fachwissen
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Kategorie: Adresse

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Adresse

    :::column-end:::
    :::column span="2":::
        **Details**

        vollständige Postanschrift
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategorie: PhoneNumber

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Details**

        Telefonnummern (nur US- und EU-Telefonnummern)
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategorie: Email

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Email

    :::column-end:::
    :::column span="2":::
        **Details**

        E-Mail-Adressen
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategorie: URL

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        URL

    :::column-end:::
    :::column span="2":::
        **Details**

        URLs zu Websites 
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategorie: IP

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        IP

    :::column-end:::
    :::column span="2":::
        **Details**

        Netzwerk-IP-Adressen 
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategorie: Datetime

Diese Kategorie enthält die folgenden Entitäten:

:::row:::
    :::column span="":::
        **Entität**

        Datetime

    :::column-end:::
    :::column span="2":::
        **Details**

        Datums- und Uhrzeitangaben 
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Entitäten in dieser Kategorie können die folgenden Unterkategorien aufweisen:

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Date

    :::column-end:::
    :::column span="2":::
        **Details**

        Kalenderdatumsangaben
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Time

    :::column-end:::
    :::column span="2":::

        Tageszeiten
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Datumsbereiche
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Uhrzeitbereiche
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        Zeitspannen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Set

    :::column-end:::
    :::column span="2":::

        Festgelegte Wiederholungszeiten
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategorie: Menge

Diese Kategorie enthält die folgenden Entitäten:

:::row:::
    :::column span="":::
        **Entität**

        Menge

    :::column-end:::
    :::column span="2":::
        **Details**

        Zahlen und numerische Mengen.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Number

    :::column-end:::
    :::column span="2":::
        **Details**

        Zahlen.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Prozentwert

    :::column-end:::
    :::column span="2":::

        Prozentangaben
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Ordinalzahlen

    :::column-end:::
    :::column span="2":::

        Ordinalzahlen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Age

    :::column-end:::
    :::column span="2":::

        Altersangaben
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Währung

    :::column-end:::
    :::column span="2":::

        Währungen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensionen

    :::column-end:::
    :::column span="2":::

        Abmessungen und Maße
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatur

    :::column-end:::
    :::column span="2":::

        Temperaturen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
