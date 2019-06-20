---
title: Vorbereiten von AppSource-Paketen | Azure Marketplace
description: Erläuterung zum Vorbereiten und Erstellen von AppSource-Paketen
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ff822e87bfec5daa161172c0d47975eb06cc2808
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935646"
---
# <a name="appsource-package-preparation"></a>Vorbereiten von AppSource-Paketen

Zusätzlich zur Datei „solution.zip“ benötigen Sie ein **AppSource-Paket**. Dabei handelt es sich um eine ZIP-Datei mit allen Ressourcen, die zum Automatisieren des Bereitstellungsvorgangs für Ihre Lösung in der CRM-Umgebung des Kunden erforderlich sind. Das **AppSource-Paket** enthält die folgenden Komponenten:

* Paket für Package Deployer
* Datei **Content_Types.xml** mit den von Ihnen verwendeten Ressourcen
* XML-Datei mit Ihren App-spezifischen Daten
* Logo im Format 32x32, das in Ihrem Eintrag im Admin Center angezeigt wird
* Lizenzbedingungen, Datenschutzrichtlinie

Die unten aufgeführten Schritte unterstützen Sie beim Erstellen des AppSource-Pakets.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Erstellen eines Pakets für Package Deployer

Das Paket für Package Deployer ist ein Teil des AppSource-Pakets.

Befolgen Sie zum Erstellen eines Pakets für Package Deployer die folgenden Anweisungen: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Nach Abschluss des Vorgangs besteht Ihr Paket aus den folgenden Ressourcen:

1. Paketordner: Enthält alle Lösungen, Konfigurationsdaten, Flatfiles und Inhalte für das Paket. _Hinweis: Im folgenden Beispiel wird davon ausgegangen, dass der Paketordner „PkgFolder“ heißt._
2. DLL-Datei: Die Assembly enthält den benutzerdefinierten Code für Ihr Paket. _Hinweis: Im folgenden Beispiel wird davon ausgegangen, dass diese Datei „MicrosoftSample.dll“ heißt._

Sie müssen nun eine Datei namens **Content_Types.xml** erstellen. In dieser Datei sind alle Ressourcenerweiterungen aufgeführt, die Teil Ihres Pakets sind. Hier sehen Sie Beispielcode für die Datei:

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Im letzten Schritt werden die folgenden Komponenten in einer ZIP-Datei zusammengefasst. Geben Sie ihr den Namen **package.zip**. Sie enthält Folgendes:

1. PkgFolder (einschließlich aller im Ordner enthaltenen Elemente)
2. DLL-Datei
3. **Content_Types.xml**

Schritte zum Erstellen der Datei „package.zip“:

1. Legen Sie den Paketordner, die Datei **Content_Types.xml** und „PackageName.dll“ in einem Verzeichnis ab.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Markieren Sie alle Elemente im Ordner, klicken Sie mit der rechten Maustaste, und wählen Sie „Senden an“ und dann „ZIP-komprimierter Ordner“ aus.

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Ändern Sie den Namen in „package.zip“.

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Erstellen eines AppSource-Pakets

Für das AppSource-Paket sind einige zusätzliche Dateien erforderlich.

1. JPG (Auflösung: 32x32)
2. HTML (HTML-formatierte Datei)
3. **Content_Types.xml** (gleiche Datei wie oben)
4. Xml

Hier sehen Sie Beispielcode für „input.xml“. Definitionen finden Sie in der Tabelle unterhalb des Codes.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Erläuterung:**

|Feld|Details|
|---|---|
|ProviderName|Anbieter der Lösung. Stammt sie von einem Microsoft-Team, sollte dafür „Microsoft“ angegeben werden.|
|PackageFile |In der Datei „content\_types.xml“ zusammengefasste Package Deployer-Ressourcen. Diese ZIP-Datei muss die Package Deployer-Assembly und den Ordner mit den Package Deployer-Ressourcen enthalten, d.h. „package.zip“.|
|SolutionAnchorName |Der Name der ZIP-Datei der Lösung in Package Deployer, der als Anzeigename und Beschreibung der Lösungsressourcen verwendet wird|
| StartDate| Das Datum, an dem das Lösungspaket zur Verfügung gestellt wird. Format: MM/TT/JJJJ|
|EndDate|Das Datum, ab dem das Lösungspaket nicht mehr verfügbar ist. Format: MM/TT/JJJJ |
|SupportedCountries |Eine durch Trennzeichen getrennte Liste der Länder/Regionen, für die das Paket zur Verfügung gestellt werden soll. Eine Liste aller aktuellen Ländercodes erhalten Sie von den Onlinediensten. Zum Zeitpunkt der Erstellung dieses Artikels lautete die Liste: AE,AL,AM,AO,AR,AT,AU,AZ,BA,BB,BD,BE,BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC,EE,EG,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW |
|LearnMoreLink | URL zur Seite „Weitere Informationen“ für dieses Paket |
|Locales|Eine Instanz dieses Knotens für jede UX-Sprache, die in der bevorzugten Lösungs-UX unterstützt werden soll. Dieser Knoten enthält untergeordnete Elemente, die das Gebietsschema, das Logo und Bedingungen für jede Sprache beschreiben.|
|Locales: PackageLocale.Code|LCID der Sprache für diesen Knoten. Beispiel: Englisch (USA) ist 1033|
|Locales: PackageLocale.IsDefault|Gibt an, dass dies die Standardsprache ist. Auf diese Sprache wird ausgewichen, wenn die vom Kunden ausgewählte UX-Sprache nicht verfügbar ist.|
|Locales: Logo|Das Logo, das Sie für dieses Paket verwenden möchten. Die Größe für das Symbol ist 32x32. Zulässige Formate: PNG und JPG|
|Locales:Terms: PackageTerm.File|Der Dateiname des HTML-Dokuments, das Ihre Lizenzbedingungen enthält|

Das Logo wird hier angezeigt:

![CRMScreenShot5](media/CRMScreenShot5.png)

Im letzten Schritt werden die folgenden Komponenten in einer ZIP-Datei zusammengefasst.

1. ZIP-Datei (zuvor erstellt)
2. **Content_Types.xml**
3. Xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Geben Sie der Datei einen passenden Namen für Ihre App. Es wird empfohlen, Ihren Firmen- und App-Namen aufzunehmen. Beispiel: **_Microsoft_SamplePackage.zip**
