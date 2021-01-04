---
title: Eingabeüberprüfung – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Eingabevalidierung im Threat Modeling Tool. Lesen Sie die Informationen zur Risikominderung, und sehen Sie sich die Codebeispiele an.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b0be672c0768b4facb6518c777d4fe56eb28aa9
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515675"
---
# <a name="security-frame-input-validation--mitigations"></a>Sicherheitsrahmen: Eingabeüberprüfung | Risikominderung 
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Web Application** | <ul><li>[Deaktivieren der XSLT-Skripterstellung für alle Transformationen, die nicht vertrauenswürdige Stylesheets verwenden](#disable-xslt)</li><li>[Sicherstellen, dass jede Seite, die von Benutzern steuerbare Inhalte enthalten könnte, von der automatischen MIME-Ermittlung abgemeldet wird](#out-sniffing)</li><li>[Absichern oder Deaktivieren der XML-Entitätsauflösung](#xml-resolution)</li><li>[Anwendungen, die „http.sys“ verwenden, überprüfen die Kanonisierungstransformation](#app-verification)</li><li>[Sicherstellen, dass entsprechende Kontrolle gelten, wenn Dateien vom Benutzer akzeptiert werden](#controls-users)</li><li>[Sicherstellen, dass typsichere Parameter für den Datenzugriff in Webanwendungen verwendet werden](#typesafe)</li><li>[Verwenden separater Modellbindungsklassen oder Bindungsfilterlisten, um Sicherheitslücken bei der MVC-Massenzuweisung vorzubeugen](#binding-mvc)</li><li>[Codieren nicht vertrauenswürdiger Webausgabe vor dem Rendering](#rendering)</li><li>[Ausführen der Eingabeüberprüfung und Filtern von Modelleigenschaften aller Zeichenfolgentypen](#typemodel)</li><li>[Anwenden der Bereinigung auf Formularfelder, die alle Zeichen akzeptieren, z.B. Rich-Text-Editor](#richtext)</li><li>[Kein Zuweisen von DOM-Elementen, die keine integrierte Codierung aufweisen](#inbuilt-encode)</li><li>[Überprüfen, ob alle Umleitungen innerhalb der Anwendung geschlossen sind oder sicher ausgeführt werden](#redirect-safe)</li><li>[Implementieren der Eingabeüberprüfung für alle Zeichenfolgentypparameter, die von Controllermethoden akzeptiert werden](#string-method)</li><li>[Festlegen einer Obergrenze für Timeouts bei der Verarbeitung regulärer Ausdrücke, um DoS aufgrund ungültiger Ausdrücke zu verhindern ](#dos-expression)</li><li>[Vermeiden der Verwendung von Html.Raw in Razor-Ansichten](#html-razor)</li></ul> | 
| **Datenbank** | <ul><li>[Unterlassen dynamischer Abfragen in gespeicherten Prozeduren](#stored-proc)</li></ul> |
| **Web-API** | <ul><li>[Sicherstellen, dass die Modellvalidierung auf Web-API-Methoden erfolgt ist](#validation-api)</li><li>[Implementieren der Eingabeüberprüfung für alle Zeichenfolgentypparameter, die von Web-API-Methoden akzeptiert werden](#string-api)</li><li>[Sicherstellen, dass typsichere Parameter in Web-API für den Datenzugriff verwendet werden](#typesafe-api)</li></ul> | 
| **Azure DocumentDB** | <ul><li>[Verwenden parametrisierter SQL-Abfragen für Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-Eingabeüberprüfung durch Schemabindung](#schema-binding)</li><li>[WCF – Eingabeüberprüfung über Parameterinspektoren](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Deaktivieren der XSLT-Skripterstellung für alle Transformationen, die nicht vertrauenswürdige Stylesheets verwenden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [XSLT-Sicherheit](/previous-versions/windows/desktop/ms763800(v=vs.85)), [XsltSettings.EnableScript-Eigenschaft](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **Schritte** | XSLT unterstützt die Skripterstellung in Stylesheets mit dem `<msxml:script>`-Element. Dadurch können benutzerdefinierte Funktionen in XSLT-Transformationen verwendet werden. Das Skript wird im Kontext des Prozesses, der die Transformation durchführt, ausgeführt. Das XSLT-Skript muss in einer nicht vertrauenswürdigen Umgebung deaktiviert werden, um die Ausführung von nicht vertrauenswürdigem Code zu verhindern. *Bei Verwendung von .NET:* Die XSLT-Skriptverarbeitung ist standardmäßig deaktiviert; Sie müssen jedoch sicherstellen, dass sie nicht über die `XsltSettings.EnableScript`-Eigenschaft explizit aktiviert wurde.|

### <a name="example"></a>Beispiel 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Beispiel
Wenn Sie MSXML 6.0 verwenden, ist die XSLT-Skriptverarbeitung standardmäßig deaktiviert; Sie müssen jedoch sicherstellen, dass sie nicht über die XML DOM-Objekteigenschaft „AllowXsltScript“ explizit aktiviert wurde. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Beispiel
Wenn Sie MSXML 5 oder niedriger verwenden, ist die XSLT-Skriptverarbeitung standardmäßig aktiviert, und Sie müssen Sie explizit deaktivieren. Legen Sie für die XML-DOM-Objekteigenschaft AllowXsltScript „false“ fest. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Sicherstellen, dass jede Seite, die von Benutzern steuerbare Inhalte enthalten könnte, von der automatischen MIME-Ermittlung abgemeldet wird

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [IE8-Sicherheit Teil V – Umfassender Schutz](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Schritte** | <p>Für jede Seite, die von Benutzern steuerbare Inhalte enthalten könnte, müssen Sie den HTTP-Header `X-Content-Type-Options:nosniff` verwenden. Um diese Anforderung zu erfüllen, können Sie die erforderlichen Header seitenweise nur für die Seiten festlegen, die von Benutzern steuerbare Inhalte enthalten können, oder Sie können sie global für alle Seiten in der Anwendung festlegen.</p><p>Jeder von einem Webserver übermittelten Datei ist ein [MIME-Typ](https://en.wikipedia.org/wiki/Mime_type) (auch *Inhaltstyp* genannt) zugeordnet, der die Art des Inhalts beschreibt (d.h. Bild, Text, Anwendung usw.)</p><p>Der Header „X-Content-Type-Options“ ist ein HTTP-Header, mit dem Entwickler festlegen, dass für ihre Inhalte keine MIME-Ermittlung erfolgen soll. Dieser Header dient zur Minderung von MIME-Ermittlungsangriffen. Dieser Header wurde in Internet Explorer 8 (IE8) hinzugefügt.</p><p>Nur Benutzer von Internet Explorer 8 (IE8) können von „X-Content-Type-Options“ profitieren. Vorherige Versionen von Internet Explorer unterstützen den X-Content-Type-Options-Header derzeit nicht.</p><p>Internet Explorer 8 (und höher) ist der einzige Browser, in dem ein Feature zum Deaktivieren der MIME-Ermittlung implementiert werden kann. Wenn andere gängige Browser (Firefox, Safari, Chrome) ähnliche Funktionen implementieren, wird diese Empfehlung um eine MDX-Syntax für diese Browser ergänzt.</p>|

### <a name="example"></a>Beispiel
Um den erforderlichen Header global für alle Seiten in der Anwendung zu aktivieren, können Sie einen der folgenden Schritte ausführen: 

* Den Header der Datei „web.config“ hinzufügen, wenn die Anwendung von Internet Information Services (IIS) 7 gehostet wird 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Den Header über die globale Anforderung Application\_BeginRequest hinzufügen 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementieren des benutzerdefinierten HTTP-Moduls 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Sie können den erforderlichen Header nur für bestimmte Seiten aktivieren, indem sie ihn einzelnen Antworten hinzufügen: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Absichern oder Deaktivieren der XML-Entitätsauflösung

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [XML-Entitätserweiterung](https://capec.mitre.org/data/definitions/197.html), [XML-Dienstverweigerungsangriffe und Verteidigungsmaßnahmen](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses), [MSXML Sicherheitsübersicht](/previous-versions/windows/desktop/ms754611(v=vs.85)), [Bewährte Methoden für das Sichern von MSXML-Code](/previous-versions/windows/desktop/ms759188(v=vs.85)), [NSXMLParserDelegate-Protokollreferenz](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [Auflösen von externen Referenzen](/dotnet/standard/data/xml/resolving-external-resources) |
| **Schritte**| <p>Auch wenn sie nicht häufig verwendet wird, ist eine XML-Funktion vorhanden, die dem XML-Parser das Erweitern von Makroentitäten um im Dokument selbst oder aus externen Quellen definierte Werte ermöglicht. Beispielsweise definiert das Dokument möglicherweise eine Entität „companyname“ mit dem Wert „Microsoft“, sodass der Text „&companyname;“ im gesamten Text automatisch durch den Text „Microsoft“ ersetzt wird. Das Dokument könnte auch eine Entität „MSFTStock“ definieren, das zum Abrufen des aktuellen Werts der Microsoft-Aktie auf einen externen Webdienst verweist.</p><p>Dann wird „&MSFTStock;“ im gesamten Dokument automatisch durch den aktuellen Aktienkurs ersetzt. Diese Funktion kann jedoch durch Denial of Service (DoS)-Bedingungen missbraucht werden. Ein Angreifer kann mehrere Entitäten schachteln, um eine exponentielle XML-Expansionsbombe zu erstellen, die den gesamten verfügbaren Arbeitsspeicher im System beansprucht. </p><p>Alternativ kann er einen externen Verweis erstellen, der eine unendliche Datenmenge streamt oder den Thread einfach aufhängt. Daher müssen alle Teams die interne und/oder externe XML-Entitätsauflösung deaktivieren, wenn ihre Anwendung sie nicht verwendet, oder manuell die Menge an Arbeitsspeicher und Zeit begrenzen, die die Anwendung für die Auflösung von Entitäten nutzen darf, wenn dies absolut notwendig ist. Wenn Ihre Anwendung die Entitätsauflösung nicht benötigt, deaktivieren Sie sie. </p>|

### <a name="example"></a>Beispiel
Für .NET Framework-Code können Sie die folgenden Ansätze verwenden:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Beachten Sie, dass der Wert von `ProhibitDtd` in `XmlReaderSettings` „true“, aber in `XmlTextReader` „false“ lautet. Wenn Sie „XmlReaderSettings“ verwenden, müssen Sie „ProhibitDtd“ nicht explizit auf „true“ festlegen, aber aus Sicherheitsgründen raten wir dazu. Beachten Sie zudem, dass die XmlDocument-Klasse die Entitätsauflösung standardmäßig zulässt. 

### <a name="example"></a>Beispiel
Verwenden Sie zum Deaktivieren der Entitätsauflösung für XmlDocuments die `XmlDocument.Load(XmlReader)`-Überladung der Load-Methode und legen Sie die entsprechenden Eigenschaften im XmlReader-Argument fest, um die Auflösung wie im folgenden Code zu deaktivieren: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Beispiel
Wenn Sie die Entitätsauflösung für Ihre Anwendung nicht deaktivieren können, legen Sie für die XmlReaderSettings.MaxCharactersFromEntities-Eigenschaft einen geeigneten Wert gemäß den Anforderungen Ihrer Anwendung fest. Dadurch wird die Auswirkung von potenziellen exponentiellen DoS-Angriffen eingeschränkt. Der folgende Code enthält ein Beispiel für diesen Ansatz: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Beispiel
Wenn Sie Inline-Entitäten, aber keine externen Entitäten auflösen müssen, legen Sie für die XmlReaderSettings.XmlResolver-Eigenschaft den Wert „null“ fest. Beispiel: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Beachten Sie, dass in MSXML6 für „ProhibitDTD“ standardmäßig „true“ (deaktiviert die DTD-Verarbeitung) festgelegt ist. Für Apple OSX/iOS-Code können Sie zwei XML-Parser verwenden: NSXMLParser und libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Anwendungen, die „http.sys“ verwenden, überprüfen die Kanonisierungstransformation

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Jede Anwendung, die „http.sys“ verwendet, sollten diese Richtlinien befolgen:</p><ul><li>Begrenzen Sie die URL-Länge, damit nicht mehr als 16.384 Zeichen (ASCII oder Unicode) möglich sind. Dies ist die maximale URL-Länge, die auf den Standardeinstellungen von Internet Information Services (IIS) 6 basiert. Websites sollten möglichst eine kürzere Länge anstreben.</li><li>Verwenden Sie das standardmäßige .NET-Framework für Datei-E/A-Klassen (z. B. FileStream), da diese von den Kanonisierungsregeln in der .NET FX profitieren.</li><li>Erstellen Sie eine explizite Zulassungsliste bekannter Dateinamen.</li><li>Lehnen Sie bekannte Dateitypen ab, die keine UrlScan Reklamationen zulassen: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, printer, ini, pol, dat files-Dateien</li><li>Fangen Sie die folgenden Ausnahmen ab:<ul><li>System.ArgumentException (für Gerätenamen)</li><li>System.NotSupportedException (für Datenströme)</li><li>System.IO.FileNotFoundException (für ungültige geschützte Dateinamen)</li><li>System.IO.FileNotFoundException (für ungültige geschützte Verzeichnisse)</li></ul></li><li>*Nicht* Win32-Datei-E/A-APIs kontaktieren. Geben Sie dem Benutzer bei einer ungültigen URL einen 400-Fehler zurück und melden Sie sich den tatsächlichen Fehler.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Sicherstellen, dass entsprechende Kontrolle gelten, wenn Dateien vom Benutzer akzeptiert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Unbeschränkter Dateiupload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), [Dateisignaturtabelle](https://www.garykessler.net/library/file_sigs.html) |
| **Schritte** | <p>Hochgeladene Dateien stellen ein erhebliches Risiko für Anwendungen dar.</p><p>Bei vielen Angriffen besteht der erste Schritt daran, Code des Systems abzugreifen, das angegriffen werden soll. Anschließend muss der Angreifer nur eine Gelegenheit finden, den Code auszuführen. Ein Dateiupload hilft dem Angreifer beim ersten Schritt. Die Folgen des uneingeschränkten Dateiuploads reichen von der Übernahme des vollständigen Systems, Überlastung des Dateisystems oder der Datenbank über die Weiterleitung der Angriffe auf Back-End-Systeme bis zur einfachen Verunstaltung.</p><p>Das Ausmaß hängt davon ab, was die Anwendung mit der hochgeladenen Datei macht und insbesondere, wo sie gespeichert ist. Die Validierung des Dateiuploads auf dem Server fehlt. Folgende Sicherheitsmaßnahmen sollte für Dateiuploads implementiert werden:</p><ul><li>Überprüfung der Dateierweiterung (nur zulässigen Dateitypen sollten akzeptiert werden)</li><li>Maximale Dateigröße</li><li>Datei sollte nicht in Webroot hochgeladen werden; der Speicherort sollte ein Verzeichnis sein, das sich nicht in einem-Systemlaufwerks befindet.</li><li>Benennungskonvention muss befolgt werden, der Name der hochgeladenen Datei sollte einen Zufallsfaktor enthalten, damit keine Dateien überschrieben werden</li><li>Dateien sollten vor dem Schreiben auf den Datenträger durch Antiviruslösungen geprüft werden</li><li>Sicherstellen, dass der Dateiname und beliebige Metadaten (z. B. Dateipfad) auf schädliche Zeichen überprüft werden</li><li>Überprüfen des Format der Dateisignatur, und um zu verhindern, dass ein Benutzer eine maskierte Datei hochlädt (z. B. Hochladen einer exe-Datei durch Ändern der Erweiterung in txt)</li></ul>| 

### <a name="example"></a>Beispiel
Informationen zur Überprüfung der Dateiformatsignatur finden Sie in der folgenden Klasse: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Sicherstellen, dass typsichere Parameter für den Datenzugriff in Webanwendungen verwendet werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Wenn Sie die Parametersammlung verwenden, behandelt SQL die Eingabe als literalen statt ausführbaren Code. Die Parametersammlung kann verwendet werden, um Typ- und Längenbeschränkungen bei Eingabedaten zu erzwingen. Bei Werten außerhalb des Bereichs wird eine Ausnahme ausgelöst. Wenn keine typsicheren SQL-Parameter verwendet werden, können Angreifer möglicherweise Einschleusungsangriffe ausführen, die in die ungefilterte Eingabe eingebettet sind.</p><p>Verwenden Sie beim Erstellen der SQL-Abfragen typsichere Parameter, um mögliche SQL-Einschleusungsangriffe zu verhindern, die bei ungefilterten Eingaben auftreten können. Sie können typsichere Parameter mit gespeicherten Prozeduren und dynamischen SQL-Anweisungen verwenden. Parameter werden von der Datenbank als literale Werte und nicht als ausführbarer Code behandelt. Parameter werden auch auf Typ und Länge überprüft.</p>|

### <a name="example"></a>Beispiel 
Der folgende Code zeigt die Verwendung typsicherer Parameter in der SqlParameterCollection beim Aufrufen einer gespeicherten Prozedur. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Im vorigen Codebeispiel darf der Eingabewert darf nicht mehr als 11 Zeichen umfassen. Wenn der Typ oder die Länger der Daten nicht dem vom Parameter definierten Wert entspricht, wird von die Klasse „SqlParameter“ eine Ausnahme ausgelöst. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Verwenden separater Modellbindungsklassen oder Bindungsfilterlisten, um Sicherheitslücken bei der MVC-Massenzuweisung vorzubeugen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Metadatenattribute](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute), [Sicherheitslücken bei öffentlichen Schlüssel und Gegenmaßnahmen](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [Vollständige Anleitung zur Massenzuweisung in ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Erste Schritte in EF mit MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Schritte** | <ul><li>**Wann sollte ich auf Overposting-Sicherheitsrisiken achten? –** Overposting-Sicherheitsrisiken können überall auftreten, wo Sie Modellklassen aus Benutzereingaben binden. Frameworks wie MVC können Benutzerdaten in benutzerdefinierten .NET-Klassen darstellen, einschließlich Plain Old CLR Objects (POCOs) MVC befüllt diese Modellklassen automatisch mit Daten aus der Anforderung, um eine zweckmäßige Darstellung für den Umgang mit Benutzereingaben bereitzustellen. Wenn diese Klassen Eigenschaften enthalten, die nicht vom Benutzer festgelegt werden soll, kann die Anwendung für Overposting-Angriffe anfällig sein, bei der Benutzer Zugriff auf Daten erhalten, die von der Anwendung nie vorgesehen waren. Wie die MVC-Modellbindung unterstützen Technologien für den Datenbankzugriff wie objekt-relationale Zuordnungen wie Entity Framework häufig auch POCO-Objekte zum Darstellen von Datenbankdaten. Diese Datenmodellklassen bieten die gleichen Vorteile im Umgang mit Datenbankdaten wie MVC beim Umgang mit Benutzereingaben. Da MVC und die Datenbank ähnliche Modelle unterstützen, z. B. POCO-Objekte, scheint es einfach zu sein, die gleichen Klassen für beide Zwecke wiederzuverwenden. Bei dieser Vorgehensweise wird die Trennung von Zuständigkeiten nicht beibehalten, und sie stellt einen allgemeinen Bereich dar, in dem unbeabsichtigte Eigenschaften für die Modellbindung verfügbar gemacht werden, was Overposting-Angriffe ermöglicht.</li><li>**Warum sollte ich meine ungefilterten Datenbankmodellklassen nicht als Parameter für meine MVC-Aktionen verwenden? –** Weil die MVC-Modellbindung alle Elemente dieser Klasse bindet. Selbst wenn die Daten nicht in Ihrer Ansicht angezeigt werden, kann ein böswilliger Benutzer eine HTTP-Anforderung mit diesen Daten senden. MVC bindet diese bereitwillig, da Ihre Aktion besagt, dass die Datenbankklasse die gewünschte Datenform für Benutzereingaben ist.</li><li>**Warum muss ich mir Gedanken über die Form für die Modellbindung machen? –** Wenn Sie die ASP.NET-MVC-Modellbindung mit zu weit gefassten Modellen verwenden, wird Ihre Anwendung anfällig für Overposting-Angriffe. Overposting könnte Angreifern ermöglichen, Anwendungsdaten anders als vom Entwickler vorgesehen zu ändern, z. B. den Preis für ein Element oder die Sicherheitsprivilegien für ein Konto zu überschreiben. Anwendungen sollten aktionsspezifische Bindungsmodelle (oder bestimmte zulässige Eigenschaftenfilter) verwenden, um einen expliziten Vertrag darüber bereitzustellen, welche nicht vertrauenswürdigen Eingaben über die Modellbindung zugelassen werden sollen.</li><li>**Muss ich für separate Bindungsmodelle einfach Code duplizieren? –** Nein, Sie müssen einzelne Zuständigkeiten trennen. Wenn Sie in Aktionsmethoden Datenbankmodelle wiederverwenden, lassen Sie zu, dass jede Eigenschaft (oder Untereigenschaft) in dieser Klasse vom Benutzer in einer HTTP-Anforderung festgelegt werden kann. Soll MVC das nicht tun, müssen Sie MVC über eine Filterliste oder eine separate Klassenform anzeigen, welche Daten stattdessen aus Benutzereingaben stammen können.</li><li>**Muss ich alle meine Attribute für die Datenanmerkung duplizieren, wenn ich separate Bindungsmodelle für Benutzereingaben verwende? –** Nicht unbedingt. Sie können MetadataTypeAttribute in der Datenbankmodellklasse verwenden, um eine Verknüpfung zu Metadaten in einer Modellbindungsklasse zu erstellen. Beachten Sie, dass der durch MetadataTypeAttribute verwiesene Typ eine Teilmenge des verweisenden Typs sein muss (er kann weniger Eigenschaften haben, aber nicht mehr).</li><li>**Das Verschieben von Daten zwischen Benutzereingabemodellen und Datenbankmodellen ist mühsam. Kann ich nicht einfach alle Eigenschaften mittels Reflexion kopieren? –** Ja. In den Bindungsmodellen werden lediglich die Eigenschaften angezeigt, die Sie als sichere Benutzereingaben festgelegt haben. Nichts spricht dagegen, mittels Reflexion alle Eigenschaften zu kopieren, die in beiden Modellen vorhanden sind.</li><li>**Was ist mit „[Bind(Exclude ="â&euro;¦")]“? Kann ich statt separaten Bindungsmodellen diesen Ansatz verwenden?–-** Hiervon raten wir ab. Die Verwendung von „[Bind(Exclude ="â&euro;¦")]“ bedeutet, dass jede neue Eigenschaft standardmäßig gebunden werden kann. Wenn eine neue Eigenschaft hinzugefügt wird, erinnert Sie ein zusätzlicher Schritt daran, auf Sicherheit zu setzen, anstatt den Entwurf standardmäßig zu schützen. Je nach Entwickler ist das Überprüfen dieser Liste beim Hinzufügen einer Eigenschaft riskant.</li><li>**Ist „[Bind(Include ="â&euro;¦")]“ für Bearbeitungsvorgänge nützlich? –**  Nein. „[Bind(Include ="â&euro;¦")]“ ist nur für Vorgänge im INSERT-Stil (Hinzufügen neuer Daten) geeignet. Verwenden Sie für UPDATE-style-Vorgänge (Überarbeiten vorhandener Daten) einen anderen Ansatz, z. B. das Verwenden separater Bindungsmodelle oder Übergeben einer expliziten Liste der zulässigen Eigenschaften an UpdateModel oder TryUpdateModel. Das Hinzufügen eines „[Bind(Include ="â&euro;¦")]“-Attributs zu einem Bearbeitungsvorgang bedeutet, dass MVC eine Objektinstanz erstellt und nur die aufgelisteten Eigenschaften festlegt, sodass alle anderen Eigenschaften ihre Standardwerte behalten. Sind die Daten permanent, ersetzen sie die vorhandene Entität vollständig, und die Werte für ausgelassene Eigenschaften werden auf den Standard zurückgesetzt. Wenn z. B. bei einem „[Bind(Include ="â&euro;¦")]“-Attribut „IsAdmin“ für einen Bearbeitungsvorgang weggelassen wurde, würde jeder Benutzer, dessen Name über diese Aktion bearbeitet wurde, auf „IsAdmin = false“ zurückgesetzt (d. h. jeder bearbeitete Benutzer würde den Administratorstatus verlieren). Wenn Sie Updates auf bestimmte Eigenschaften verhindern möchten, verwenden Sie einen der anderen oben aufgeführten Ansätze. Beachten Sie, dass einige Versionen von MVC-Tools Controllerklassen mit „[Bind(Include ="â&euro;¦")]“ für Bearbeitungsaktionen generieren und deshalb durch das Entfernen einer Eigenschaft aus dieser Liste Overposting-Angriffe verhindert werden. Wie oben erwähnt funktioniert dieser Ansatz jedoch nicht wie gewünscht und setzt stattdessen sämtliche Daten in den ausgelassenen Eigenschaften auf ihre Standardwerte zurück.</li><li>**Gibt es bei Erstellungsvorgängen Einschränkungen im Hinblick auf die Verwendung von „[Bind(Include ="â&euro;¦")]“ statt separater Bindungsmodelle? –**  Ja. Erstens ist dieser Ansatz nicht für Bearbeitungsszenarien geeignet und erfordert zwei separate Ansätze, um Overposting-Sicherheitsrisiken zu vermindern. Zweitens erzwingen separate Bindungsmodelle die Interessentrennung zwischen der für die Benutzereingabe und der für die Persistenz verwendeten Form, was „[Bind(Include ="â&euro;¦")]“ nicht macht. Beachten Sie drittens, dass „[Bind(Include ="â&euro;¦")]“ nur Eigenschaften der obersten Ebene bearbeiten kann; Sie können keine Teile untergeordneter Eigenschaften (z. B. „Details.Name“) im Attribut zulassen. Schließlich – und vielleicht das Wichtigste: Die Verwendung von „[Bind(Include ="â&euro;¦")]“ fügt einen weiteren Schritt hinzu, der jedes Mal berücksichtigt werden muss, wenn die Klasse zur Modellbindung verwendet wird. Wenn eine neue Aktionsmethode direkt an die Datenklasse gebunden und dabei vergessen wird, ein „[Bind(Include ="â&euro;¦")]“-Attribut einzubeziehen, kann sie für Overposting-Angriffe anfällig sein. Deshalb ist der „[Bind(Include ="â&euro;¦")]“-Ansatz standardmäßig etwas unsicherer. Wenn Sie „[Bind(Include ="â&euro;¦")]“ verwenden, achten Sie darauf, dies immer dann anzugeben, wenn Ihre Datenklassen als Aktionsmethodenparameter angezeigt werden.</li><li>**Kann ich bei Erstellungsvorgängen das Attribut „[Bind(Include ="â&euro;¦")]“ für die Modellklasse selbst hinzufügen? Spare ich mir so nicht den Aufwand, das Attribut auf jede Aktionsmethode anzuwenden? –** Dieser Ansatz ist nur in manchen Fällen geeignet. Wenn Sie „[Bind(Include ="â&euro;¦")]“ für den Modelltyp selbst verwenden (statt für Aktionsparameter, die diese Klasse verwenden), müssen Sie nicht daran denken, das Attribut „[Bind(Include ="â&euro;¦")]“für jede Aktionsmethode einzubeziehen. Wenn Sie das Attribut direkt auf die Klasse anwenden, wird eine separate Oberfläche dieser Klasse für Modellbindungszwecke erstellt. Bei diesem Ansatz ist jedoch nur eine Modellbindungsform pro Modellklasse möglich. Wenn eine Aktionsmethode die Modellbindung eines Felds zulassen muss (z. B. eine reine Administratoraktion zum Aktualisieren von Benutzerrollen) und andere Aktionen die Modellbindung dieses Felds verhindern müssen, funktioniert dieser Ansatz nicht. Jede Klasse kann nur eine Modellbindungsform haben. Wenn unterschiedliche Aktionen verschiedene Modellbindungsformen benötigen, müssen sie diese separaten Formen entweder mit separaten Modellbindungsklassen oder separaten „[Bind(Include ="â&euro;¦")]“-Attributen für die Aktionsmethoden darstellen.</li><li>**Was sind Bindungsmodelle? Sind sie mit Anzeigemodellen identisch? –** Hierbei handelt es sich um zwei verwandte Konzepte. Der Begriff „Bindungsmodell“ bezieht sich auf eine Modellklasse, die in einer Parameterliste einer Aktion verwendet wird (die Form, die von der MVC-Modellbindung an die Aktionsmethode übergeben wird). Der Begriff „Ansichtsmodell“ bezieht sich auf eine Modellklasse, die von einer Aktionsmethode an eine Ansicht übergeben wird. Das Verwenden eines ansichtsspezifischen Modells ist eine gängige Methode, um Daten aus einer Aktionsmethode an eine Ansicht zu übergeben. Diese Form ist häufig auch für die Modellbindung geeignet, und der Begriff „Ansichtsmodell“ kann verwendet werden, wenn das gleiche Modell an beiden Orten verwendet wird. Dieses Verfahren bezieht sich speziell auf Bindungsmodelle, und der Fokus liegt auf dem Weitergeben der Form an die Aktion, dem wichtigsten Punkt bei der Massenzuweisung.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Codieren nicht vertrauenswürdiger Webausgabe vor dem Rendering

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, Web Forms, MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Verhindern von Cross-Site-Scripting in ASP.NET](/previous-versions/msp-n-p/ff649310(v=pandp.10)), [Cross-Site-Scripting](https://cwe.mitre.org/data/definitions/79.html), [Spickzettel zum Verhindern von XSS (Cross-Site-Scripting)](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Schritte** | Cross-Site-Scripting (Abkürzung: XSS) ist ein Angriffsvektor für Onlinedienste oder beliebige Anwendungen/Komponenten, die Eingabe aus dem Web verwenden. XSS-Sicherheitsrisiken können einem Angreifer das Ausführen von Skripts auf dem Computer eines anderen Benutzers über eine gefährdete Webanwendung ermöglichen. Schädliche Skripts können verwendet werden, um Cookies zu stehlen oder den Computer des Opfers über JavaScript zu manipulieren. XSS wird durch die Überprüfung von Benutzereingaben, deren Form und Codierung vor dem Rendern auf einer Webseite verhindert. Die Überprüfung von Benutzereingaben und Ausgabecodierung kann durch Web Protection Library erfolgen. Für verwalteten Code (C\#, VB.NET usw.) verwenden Sie eine oder mehrere Codierungsmethoden aus der Web Protection (Anti-XSS) Library, die vom Kontext abhängen, in dem die Benutzereingabe manifestiert wird:| 

### <a name="example"></a>Beispiel

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Ausführen der Eingabeüberprüfung und Filtern von Modelleigenschaften aller Zeichenfolgentypen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Hinzufügen einer Überprüfung](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [Überprüfen von Modelldaten in einer MVC-Anwendung](/previous-versions/dd410404(v=vs.90)), [Richtlinien für Ihre ASP.NET MVC-Anwendungen](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Schritte** | <p>Alle Eingabeparameter müssen überprüft werden, bevor sie in der Anwendung verwendet werden, um sicherzustellen, dass die Anwendung vor schädlichen Benutzereingaben geschützt ist. Überprüfen Sie die Eingabewerte mit Überprüfungen regulärer Ausdrücke auf Serverseite mit einer Überprüfungsstrategie, die auf einer Zulassungsliste basiert. Durch unbereinigte Benutzereingaben bzw. an die Methode übergebene Parameter kann das Risiko der Codeeinschleusung steigen.</p><p>Bei Webanwendungen können auch Formularfelder, Abfragezeichenfolgen, Cookies, HTTP-Header und Webdienstparameter mögliche Einstiegspunkte darstellen.</p><p>Die folgenden Eingabeüberprüfungen müssen bei der Modellbindung ausgeführt werden:</p><ul><li>Die Modelleigenschaften sollten mit der RegularExpression-Anmerkung versehen werden, um zulässige Zeichen und die maximal zulässige Länge zu akzeptieren.</li><li>Die Controllermethoden sollten die ModelState-Gültigkeit prüfen.</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Anwenden der Bereinigung auf Felder, die alle Zeichen akzeptieren, z.B. Rich-Text-Editor

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Codieren unsicherer Eingabe](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3), [HTML-Bereinigung](https://github.com/mganss/HtmlSanitizer) |
| **Schritte** | <p>Identifizieren Sie alle statischen Markuptags, die Sie verwenden möchten. Üblicherweise wird die Formatierung dazu auf sichere HTML-Elemente wie Formatierung `<b>` (fett) und `<i>` (kursiv) beschränkt.</p><p>Führen Sie vor dem Schreiben der Daten eine HTML-Codierung durch. Damit werden schädliche Skripts sicher, da sie als Text und nicht als ausführbarer Code behandelt werden.</p><ol><li>Deaktivieren Sie die ASP.NET-Anforderungsvalidierung, indem Sie der \@ Page-Direktive das ValidateRequest = "false"-Attribut hinzufügen.</li><li>Codieren Sie die Zeichenfolgeneingabe mit der HtmlEncode-Methode.</li><li>Verwenden Sie einen StringBuilder und rufen Sie die Replace-Methode auf, um die Codierung der HTML-Elemente, die Sie zulassen möchten, selektiv zu entfernen.</li></ol><p>Die Seite in den Verweisen deaktiviert die ASP.NET-Anforderungsvalidierung durch Festlegen von `ValidateRequest="false"`. Sie führt eine HTML-Codierung der Eingabe durch und lässt `<b>` und `<i>` selektiv zu. Alternativ kann eine .NET-Bibliothek für HTML-Bereinigung verwendet werden.</p><p>HtmlSanitizer ist eine .NET-Bibliothek zum Bereinigen von HTML-Fragmenten und Dokumenten aus Konstrukten, die zu XSS-Angriffen führen können. Sie verwendet AngleSharp zum Analysieren, Bearbeiten und Rendern von HTML und CSS. HtmlSanitizer kann als ein NuGet-Paket installiert werden, und die Benutzereingabe kann über relevante HTML- oder CSS-Bereinigungsmethoden auf dem Server weitergegeben werden. Die Bereinigung als Sicherheitskontrolle sollte jedoch nur als letzte Option betrachtet werden.</p><p>Die Überprüfung von Benutzereingaben und Ausgabecodierung gelten als bessere Sicherheitskontrollen.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Kein Zuweisen von DOM-Elementen, die keine integrierte Codierung aufweisen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Viele Javascript-Funktionen verschlüsseln nicht standardmäßig. Wenn Sie DOM-Elementen über derartige Funktionen Eingaben zuweisen, die nicht vertrauenswürdig sind, kann das zum Ausführen von Cross-Site-Scripting führen.| 

### <a name="example"></a>Beispiel
Hier einige Beispiele für unsichere Elemente: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Verwenden Sie nicht `innerHtml`, sondern `innerText`. Verwenden Sie zudem nicht `$("#elm").html()`, sondern `$("#elm").text()`. 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Überprüfen, ob alle Umleitungen innerhalb der Anwendung geschlossen sind oder sicher ausgeführt werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Das OAuth 2.0-Autorisierungsframework – Offene Redirectors](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Schritte** | <p>Anwendungsentwürfe, die eine Umleitung zu einem vom Benutzer bereitgestellten Speicherort erfordern, müssen die möglichen Umleitungsziele auf eine vordefinierte Liste „sicherer“ Standorte oder Domänen einschränken. Alle Umleitungen in der Anwendung müssen geschlossen/sicher sein.</p><p>Gehen Sie dazu folgendermaßen vor:</p><ul><li>Identifizieren Sie alle Umleitungen.</li><li>Implementieren Sie für jede Umleitung eine entsprechende Gegenmaßnahme. Entsprechende Gegenmaßnahmen wären z. B. eine Umleitungszulassungsliste oder Benutzerbestätigung. Verwendet eine Website oder ein Dienst mit offener Umleitung Facebook/OAuth/OpenID-Identitätsanbieter, so können Angreifer die Anmeldetoken von Benutzern stehlen und deren Identität annehmen. Dies zählt zu den inhärenten Risiken bei Verwendung von OAuth, die in RFC 6749 „Das OAuth 2.0-Autorisierungsframework“ im Abschnitt 10.15 zu offenen Redirectors dokumentiert sind. Zudem können Anmeldeinformationen von Benutzern durch Spear-Phishing-Angriffe mit offenen Umleitungen gestohlen werden.</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementieren der Eingabeüberprüfung für alle Zeichenfolgentypparameter, die von Controllermethoden akzeptiert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Überprüfen von Modelldaten in einer MVC-Anwendung](/previous-versions/dd410404(v=vs.90)), [Richtlinien für Ihre ASP.NET MVC-Anwendungen](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Schritte** | Für Methoden, die nur einen primitiven Datentyp und keine Modelle als Argument akzeptieren, sollte die Überprüfung der Eingabe mit dem regulären Ausdruck ausgeführt werden. Hier sollte Regex.IsMatch mit einem gültigen Regex-Muster verwendet werden. Wenn die Eingabe nicht mit dem angegebenen regulären Ausdruck übereinstimmt, sollte das Steuerelement nicht fortfahren, und eine entsprechende Warnung hinsichtlich Überprüfungsfehler sollte angezeigt werden.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Festlegen einer Obergrenze für Timeouts bei der Verarbeitung regulärer Ausdrücke, um DoS aufgrund ungültiger Ausdrücke zu verhindern

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, Web Forms, MVC5, MVC6  |
| **Attribute**              | –  |
| **Referenzen**              | [DefaultRegexMatchTimeout-Eigenschaft](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **Schritte** | Legen Sie das globale Standardtimeout fest, um DOS-Angriffe auf schlecht erstellte reguläre Ausdrücke sicherzustellen, die zur Rückverfolgung führen. Dauert die Verarbeitungszeit länger als die definierte Obergrenze, wird eine Timeoutausnahme ausgelöst. Wenn nichts konfiguriert ist, wäre das Timeout unendlich.| 

### <a name="example"></a>Beispiel
Die folgende Konfiguration würde z. B. eine RegexMatchTimeoutException auslösen, wenn die Verarbeitung mehr als 5 Sekunden dauert: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Vermeiden der Verwendung von Html.Raw in Razor-Ansichten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Schritt | ASP.NET WebPages (Razor) führen eine automatische HTML-Codierung durch. Alle von eingebetteten Codenuggets (@-Blöcke) ausgegebenen Zeichenfolgen sind automatisch HTML-codiert. Wird jedoch die `HtmlHelper.Raw`-Methode aufgerufen, gibt sie Markup ohne HTML-Codierung zurück. Wenn die `Html.Raw()`-Helfermethode verwendet wird, umgeht sie den automatischen Codierungsschutz von Razor.|

### <a name="example"></a>Beispiel
Hier einige Beispiele für unsichere Elemente: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Verwenden Sie `Html.Raw()` nur, wenn Markup angezeigt werden muss. Diese Methode führt keine implizite Ausgabecodierung durch. Verwenden Sie andere ASP.NET-Helferprogramme wie `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Unterlassen dynamischer Abfragen in gespeicherten Prozeduren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>SQL-Einschleusungsangriffe nutzen Sicherheitslücken bei der Eingabeüberprüfung aus, um beliebige Befehle in der Datenbank auszuführen. Das kann passieren, wenn Ihre Anwendung Eingaben zum Erstellen dynamischer SQL-Anweisungen verwendet, um auf die Datenbank zuzugreifen, oder wenn Ihr Code gespeicherte Prozeduren verwendet, die Zeichenfolgen mit rohen Benutzereingaben übergeben. Mit einem SQL-Einschleusungsangriff kann der Angreifer beliebige Befehle in der Datenbank ausführen. Alle SQL-Anweisungen (einschließlich der SQL-Anweisungen in gespeicherten Prozeduren) müssen parametrisiert werden. Parametrisierte SQL-Anweisungen akzeptieren Zeichen, die für SQL eine besondere Bedeutung haben (z. B. einfache Anführungszeichen), da sie stark typisiert sind. |

### <a name="example"></a>Beispiel
Hier ein Beispiel für eine unsichere dynamische gespeicherte Prozedur: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Beispiel
Hier ein Beispiel für die sichere Implementierung der gleichen gespeicherten Prozedur: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Sicherstellen, dass die Modellvalidierung auf Web-API-Methoden erfolgt ist

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Modellvalidierung in der ASP.NET-Web-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Schritte** | Wenn ein Client Daten an eine Web-API sendet, müssen die Daten vor der Verarbeitung überprüft werden. Nehmen Sie bei ASP.NET-Web-APIs, die Modelle als Eingabe akzeptieren, Datenanmerkungen an Modellen vor, um Validierungsregeln für die Eigenschaften des Modells festzulegen.|

### <a name="example"></a>Beispiel
Der folgende Code zeigt das Vorgehen: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Beispiel
In der action-Methode der API-Controller muss die Gültigkeit des Modells wie unten dargestellt explizit überprüft werden: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementieren der Eingabeüberprüfung für alle Zeichenfolgentypparameter, die von Web-API-Methoden akzeptiert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, MVC 5, MVC 6 |
| **Attribute**              | –  |
| **Referenzen**              | [Überprüfen von Modelldaten in einer MVC-Anwendung](/previous-versions/dd410404(v=vs.90)), [Richtlinien für Ihre ASP.NET MVC-Anwendungen](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Schritte** | Für Methoden, die nur einen primitiven Datentyp und keine Modelle als Argument akzeptieren, sollte die Überprüfung der Eingabe mit dem regulären Ausdruck ausgeführt werden. Hier sollte Regex.IsMatch mit einem gültigen Regex-Muster verwendet werden. Wenn die Eingabe nicht mit dem angegebenen regulären Ausdruck übereinstimmt, sollte das Steuerelement nicht fortfahren, und eine entsprechende Warnung hinsichtlich Überprüfungsfehler sollte angezeigt werden.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Sicherstellen, dass typsichere Parameter in Web-API für den Datenzugriff verwendet werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Wenn Sie die Parametersammlung verwenden, behandelt SQL die Eingabe als literalen statt ausführbaren Code. Die Parametersammlung kann verwendet werden, um Typ- und Längenbeschränkungen bei Eingabedaten zu erzwingen. Bei Werten außerhalb des Bereichs wird eine Ausnahme ausgelöst. Wenn keine typsicheren SQL-Parameter verwendet werden, können Angreifer möglicherweise Einschleusungsangriffe ausführen, die in die ungefilterte Eingabe eingebettet sind.</p><p>Verwenden Sie beim Erstellen der SQL-Abfragen typsichere Parameter, um mögliche SQL-Einschleusungsangriffe zu verhindern, die bei ungefilterten Eingaben auftreten können. Sie können typsichere Parameter mit gespeicherten Prozeduren und dynamischen SQL-Anweisungen verwenden. Parameter werden von der Datenbank als literale Werte und nicht als ausführbarer Code behandelt. Parameter werden auch auf Typ und Länge überprüft.</p>|

### <a name="example"></a>Beispiel
Der folgende Code zeigt die Verwendung typsicherer Parameter in der SqlParameterCollection beim Aufrufen einer gespeicherten Prozedur. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Im vorigen Codebeispiel darf der Eingabewert darf nicht mehr als 11 Zeichen umfassen. Wenn der Typ oder die Länger der Daten nicht dem vom Parameter definierten Wert entspricht, wird von die Klasse „SqlParameter“ eine Ausnahme ausgelöst. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Verwenden parametrisierter SQL-Abfragen für Cosmos DB

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure DocumentDB | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Ankündigung: SQL-Parametrisierung in Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Schritte** | Azure Cosmos DB unterstützt zwar nur schreibgeschützte Abfragen, jedoch ist SQL-Einschleusung weiterhin möglich, wenn Abfragen durch die Verkettung von Benutzereingaben erstellt werden. Möglicherweise erhält ein Benutzer durch das Erstellen böswilliger Abfragen Zugriff auf Daten, auf die er innerhalb derselben Auflistung nicht zugreifen dürfte. Verwenden Sie parametrisierte SQL-Abfragen, wenn Abfragen basierend auf Benutzereingaben erstellt werden. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF-Eingabeüberprüfung durch Schemabindung

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **Schritte** | <p>Bei fehlender Überprüfung sind verschiedenste Einschleusungsangriffe möglich.</p><p>Die Nachrichtenüberprüfung stellt eine Verteidigungslinie zum Schutz Ihrer WCF-Anwendung dar. Bei diesem Ansatz überprüfen Sie Nachrichten mithilfe von Schemas, um WCF-Dienstvorgänge vor Angriffen durch schädliche Clients zu schützen. Überprüfen Sie alle vom Client empfangenen Nachrichten, um den Client vor Angriffen von einem schädlichen Dienst zu schützen. Die Nachrichtenüberprüfung ermöglicht die Überprüfung von Nachrichten, wenn Vorgänge Nachrichten- oder Datenverträge beanspruchen, die nur mit Parametervalidierung erfolgen können. Mit der Nachrichtenüberprüfung können Sie eine Validierungslogik innerhalb von Schemas erstellen, um mehr Flexibilität zu erhalten und Entwicklungszeit zu verkürzen. Schemas können in verschiedenen Anwendungen innerhalb der Organisation wiederverwendet werden und Standards für die Darstellung der Daten erstellen. Darüber hinaus können Sie mit der Nachrichtenüberprüfung Vorgänge schützen, wenn diese komplexere Datentypen mit Verträgen verwenden, die Geschäftslogik darstellen.</p><p>Zum Ausführen der Nachrichtenüberprüfung erstellen Sie zuerst ein Schema, das die Vorgänge Ihres Diensts und die Datentypen darstellt, die von diesen Vorgängen beansprucht werden. Anschließend erstellen Sie eine .NET-Klasse, die die an den bzw. von dem Dienst gesendeten/empfangenen Nachrichten mit einem benutzerdefinierten Inspektor für Client- und Verteilernachrichten überprüft. Dann implementieren Sie ein benutzerdefiniertes Endpunktverhalten, um die Nachrichtenüberprüfung auf dem Client und dem Dienst zu aktivieren. Abschließend implementieren Sie ein benutzerdefiniertes Konfigurationselement für die Klasse, das Ihnen ermöglicht, das erweiterte benutzerdefinierte Endpunktverhalten in der Konfigurationsdatei des Dienstes oder des Clients verfügbar zu machen.</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF – Eingabeüberprüfung über Parameterinspektoren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **Schritte** | <p>Die Eingabe- und Datenüberprüfung stellt eine wichtige Verteidigungslinie zum Schutz Ihrer WCF-Anwendung dar. Überprüfen Sie alle in WCF-Dienstvorgängen verfügbar gemachten Parameter, um den Dienst vor Angriffen durch schädliche Clients zu schützen. Zudem sollten Sie alle vom Client empfangenen Rückgabewerte überprüfen, um den Client vor Angriffen durch einen schädlichen Dienst zu schützen.</p><p>WCF bietet verschiedene Erweiterungspunkte, mit denen Sie das WCF-Laufzeitverhalten durch das Erstellen benutzerdefinierter Erweiterungen anpassen können. Nachrichten- und Parameterinspektoren sind zwei Erweiterungsmechanismen, mit denen Sie größere Kontrolle über die Daten erhalten, die zwischen einem Client und einem Dienst weitergegeben werden. Verwenden Sie Parameterinspektoren für die Überprüfung von Benutzereingaben und setzen Sie Nachrichten-Inspektoren nur bei Bedarf ein, um den gesamte Nachrichtenfluss in und aus einem Dienst zu überprüfen.</p><p>Zum Ausführen der Eingabeüberprüfung erstellen Sie eine .NET-Klasse und implementieren einen benutzerdefinierten Parameterinspektor, um Parameter für Vorgänge in ihrem Dienst zu überprüfen. Dann implementieren Sie ein benutzerdefiniertes Endpunktverhalten, um die Überprüfung auf dem Client und dem Dienst zu aktivieren. Abschließend implementieren Sie ein benutzerdefiniertes Konfigurationselement für die Klasse, das Ihnen ermöglicht, das erweiterte benutzerdefinierte Endpunktverhalten in der Konfigurationsdatei des Dienstes oder des Clients verfügbar zu machen.</p>|