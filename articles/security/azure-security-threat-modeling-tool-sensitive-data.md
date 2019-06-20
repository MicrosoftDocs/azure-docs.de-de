---
title: Sensible Daten – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Gegenmaßnahmen für durch das Threat Modeling Tool offengelegte Gefahren
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 27028903daeaf62a25584300944538341a861c80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610566"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Sicherheitsrahmen: Sensible Daten | Gegenmaßnahmen 
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Computer-Vertrauensstellungsgrenze** | <ul><li>[Sicherstellen, dass Binärdateien verschleiert werden, wenn sie sensible Informationen enthalten](#binaries-info)</li><li>[Erwägen der Verwendung von Encrypted File System (EFS) zum Schützen von vertraulichen benutzerspezifischen Daten](#efs-user)</li><li>[Sicherstellen, dass von der Anwendung im Dateisystem gespeicherte sensible Daten verschlüsselt sind](#filesystem)</li></ul> | 
| **Web Application** | <ul><li>[Sicherstellen, dass sensible Inhalte nicht im Browser zwischengespeichert werden](#cache-browser)</li><li>[Verschlüsseln von Abschnitten der Web-App-Konfigurationsdateien, die sensible Daten enthalten](#encrypt-data)</li><li>[Explizites Deaktivieren des autocomplete-HTML-Attributs in sensiblen Formularen und Eingabeumgebungen](#autocomplete-input)</li><li>[Sicherstellen, dass auf dem Benutzerbildschirm angezeigte sensible Daten maskiert sind](#data-mask)</li></ul> | 
| **Datenbank** | <ul><li>[Implementieren der dynamischen Datenmaskierung zum Beschränken der Offenlegung von sensiblen Daten für Benutzer ohne Berechtigungen](#dynamic-users)</li><li>[Sicherstellen, dass Kennwörter im Salt-Hashformat gespeichert werden](#salted-hash)</li><li>[Sicherstellen, dass sensible Daten in Datenbankspalten verschlüsselt sind](#db-encrypted)</li><li>[Sicherstellen, dass die Verschlüsselung auf Datenbankebene (TDE) aktiviert ist](#tde-enabled)</li><li>[Sicherstellen, dass Datenbanksicherungen verschlüsselt sind](#backup)</li></ul> | 
| **Web-API** | <ul><li>[Sicherstellen, dass für die Web-API relevante sensible Daten nicht im Speicher des Browsers gespeichert werden](#api-browser)</li></ul> | 
| Azure DocumentDB | <ul><li>[Verschlüsseln sensibler Daten, die in Azure Cosmos DB gespeichert werden](#encrypt-docdb)</li></ul> | 
| **Azure IaaS-VM-Vertrauensstellungsgrenze** | <ul><li>[Verwenden von Azure Disk Encryption zum Verschlüsseln von Datenträgern, die von virtuellen Computern verwendet werden](#disk-vm)</li></ul> | 
| **Service Fabric-Vertrauensstellungsgrenze** | <ul><li>[Verschlüsseln von Geheimnissen in Service Fabric-Anwendungen](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Durchführen der Sicherheitsmodellierung und Verwenden von Geschäftseinheiten/Teams nach Bedarf](#modeling-teams)</li><li>[Einschränken des Zugriffs auf die Funktion „Freigeben“ für kritische Entitäten](#entities)</li><li>[Schulen von Benutzern in Bezug auf die Risiken der Funktion „Freigeben“ von Dynamics CRM und bewährte Sicherheitsmethoden](#good-practices)</li><li>[Einbinden einer Entwicklungsstandardregel, die das Anzeigen von Konfigurationsdetails bei der Ausnahmeverwaltung vorschreibt](#exception-mgmt)</li></ul> | 
| **Azure Storage (in englischer Sprache)** | <ul><li>[Verwenden von Azure Storage Service Encryption (SSE) für ruhende Daten (Vorschau)](#sse-preview)</li><li>[Verwenden der clientseitigen Verschlüsselung zum Speichern von sensiblen Daten in Azure Storage](#client-storage)</li></ul> | 
| **Mobiler Client** | <ul><li>[Verschlüsseln von sensiblen Daten oder personenbezogenen Daten, die in den lokalen Speicher von Smartphones geschrieben werden](#pii-phones)</li><li>[Verschleiern von generierten Binärdateien vor der Verteilung an Endbenutzer](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Festlegen von „clientCredentialType“ auf „Certificate“ oder „Windows“](#cert)</li><li>[WCF-Sicherheitsmodus ist nicht aktiviert](#security)</li></ul> | 

## <a id="binaries-info"></a>Sicherstellen, dass Binärdateien verschleiert werden, wenn sie sensible Informationen enthalten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Stellen Sie sicher, dass Binärdateien verschleiert werden, wenn sie sensible Informationen wie Branchengeheimnisse oder sensible Geschäftslogik enthalten, für die das Reverse Engineering verhindert werden soll. Das Ziel besteht darin, das Reverse Engineering von Assemblys unmöglich zu machen. Hierfür können Tools wie `CryptoObfuscator` verwendet werden. |

## <a id="efs-user"></a>Erwägen der Verwendung von Encrypted File System (EFS) zum Schützen von vertraulichen benutzerspezifischen Daten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Erwägen Sie die Verwendung von Encrypted File System (EFS) zum Schützen von vertraulichen benutzerspezifischen Daten vor Angreifern mit physischem Zugriff auf den Computer. |

## <a id="filesystem"></a>Sicherstellen, dass von der Anwendung im Dateisystem gespeicherte sensible Daten verschlüsselt sind

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Stellen Sie sicher, dass die von der Anwendung im Dateisystem gespeicherten sensiblen Daten verschlüsselt sind (z.B. per DPAPI), wenn EFS nicht erzwungen werden kann. |

## <a id="cache-browser"></a>Sicherstellen, dass sensible Inhalte nicht im Browser zwischengespeichert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, Web Forms, MVC5, MVC6 |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Browser können Informationen für die Zwischenspeicherung und die Verfolgung des Verlaufs speichern. Diese zwischengespeicherten Dateien werden in einem Ordner abgelegt, z.B. dem Ordner „Temporäre Internetdateien“ von Internet Explorer. Wenn auf diese Seite erneut zugegriffen wird, zeigt der Browser sie aus dem Cache an. Falls für den Benutzer sensible Informationen angezeigt werden (z.B. Adresse, Kreditkartendaten, Sozialversicherungsnummer oder Benutzername), können diese Informationen im Cache des Browsers gespeichert werden. Sie können dann abgerufen werden, indem der Cache des Browsers durchsucht wird oder indem im Browser einfach auf die Schaltfläche „Zurück“ geklickt wird. Legen Sie den Wert des cache-control-Antwortheaders für alle Seiten auf „no-store“ fest. |

### <a name="example"></a>Beispiel
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Beispiel
Dies kann über einen Filter implementiert werden. Sie können das folgende Beispiel verwenden: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>Verschlüsseln von Abschnitten der Web-App-Konfigurationsdateien, die sensible Daten enthalten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [How To: Encrypt Configuration Sections in ASP.NET 2.0 Using DPAPI](https://msdn.microsoft.com/library/ff647398.aspx) (Gewusst wie: Verschlüsseln von Konfigurationsabschnitten in ASP.NET 2.0 mithilfe von DPAPI), [Angeben eines geschützten Konfigurationsanbieters](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Verwenden von Azure Key Vault zum Schützen von Anwendungsgeheimnissen](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Schritte** | In Konfigurationsdateien wie „web.config“ und „appsettings.json“ werden häufig sensible Informationen wie Benutzernamen, Kennwörter, Datenbankverbindungszeichenfolgen und Verschlüsselungsschlüssel gespeichert. Wenn Sie diese Informationen nicht schützen, können Angreifer oder böswillige Benutzer an sensible Informationen wie Kontobenutzernamen und Kennwörter, Datenbanknamen und Servernamen gelangen. Verschlüsseln Sie daher die sensiblen Abschnitte von Konfigurationsdateien basierend auf dem Bereitstellungstyp (Azure/lokal) mithilfe von DPAPI oder mithilfe von Diensten wie Azure Key Vault. |

## <a id="autocomplete-input"></a>Explizites Deaktivieren des autocomplete-HTML-Attributs in sensiblen Formularen und Eingabeumgebungen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [MSDN: autocomplete-Attribut](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Verwenden von AutoComplete in HTML-Formularen](https://msdn.microsoft.com/library/ms533032.aspx), [Sicherheitsanfälligkeit bezüglich HTML-Bereinigung](https://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete – Schon wieder?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Schritte** | Mit dem Attribut „autocomplete“ wird angegeben, ob die automatische Vervollständigung für ein Formular aktiviert oder deaktiviert sein soll. Bei aktivierter automatischer Vervollständigung werden die Werte im Browser automatisch basierend auf den Werten vervollständigt, die vom Benutzer vorher eingegeben wurden. Wenn in einem Formular beispielsweise ein neuer Name und ein neues Kennwort eingegeben werden und das Formular übermittelt wird, wird im Browser die Frage angezeigt, ob das Kennwort gespeichert werden soll. Danach werden der Name und das Kennwort beim Anzeigen des Formulars automatisch eingefügt oder beim Eingeben des Namens vervollständigt. Ein Angreifer mit lokalem Zugriff hätte die Möglichkeit, das Klartext-Kennwort aus dem Browsercache zu entwenden. Die automatische Vervollständigung ist standardmäßig aktiviert und muss explizit deaktiviert werden. |

### <a name="example"></a>Beispiel
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Sicherstellen, dass auf dem Benutzerbildschirm angezeigte sensible Daten maskiert sind

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Sensible Daten, z.B. Kennwörter, Kreditkartennummern, Sozialversicherungsnummer usw., sollten bei der Anzeige auf dem Bildschirm maskiert werden. Mit dieser Maßnahme soll verhindert werden, dass unbefugte Personen auf die Daten zugreifen (z.B. ausgespähte Kennwörter, Anzeige von Sozialversicherungsnummern von Benutzern durch Supportpersonal). Stellen Sie sicher, dass diese Datenelemente nicht als Klartext sichtbar sind und auf angemessene Weise maskiert werden. Dies gilt für das Akzeptieren der Daten als Eingabe (z.B. input type="password") und für die erneute Anzeige auf dem Bildschirm (z.B. die Anzeige der letzten vier Ziffern der Kreditkartennummer). |

## <a id="dynamic-users"></a>Implementieren der dynamischen Datenmaskierung zum Beschränken der Offenlegung von sensiblen Daten für Benutzer ohne Berechtigungen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | SQL Azure, lokal |
| **Attribute**              | SQL-Version: V12, SQL-Version: MsSQL2016 |
| **Referenzen**              | [Dynamische Datenmaskierung](https://msdn.microsoft.com/library/mt130841) |
| **Schritte** | Der Zweck besteht bei der dynamischen Datenmaskierung darin, die Offenlegung von sensiblen Daten zu beschränken, um zu verhindern, dass unbefugte Benutzer die Daten anzeigen. Das Ziel der dynamischen Datenmaskierung ist nicht, Datenbankbenutzer am Herstellen einer direkten Verbindung mit der Datenbank und am Durchführen von umfassenden Abfragen zur Verfügbarmachung von einzelnen sensiblen Daten zu hindern. Die dynamische Datenmaskierung wird zusätzlich zu anderen SQL Server-Sicherheitsfunktionen (Überwachung, Verschlüsselung, Sicherheit auf Zeilenebene usw.) eingesetzt. Es wird dringend empfohlen, diese Maskierung zusammen mit den anderen Funktionen zu nutzen, um die in der Datenbank enthaltenen sensiblen Daten besser zu schützen. Beachten Sie, dass diese Funktion nur von SQL Server ab Version 2016 und der Azure SQL-Datenbank unterstützt wird. |

## <a id="salted-hash"></a>Sicherstellen, dass Kennwörter im Salt-Hashformat gespeichert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Password Hashing using .NET Crypto APIs](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) (Kennworthashing mit .NET-Kryptografie-APIs) |
| **Schritte** | Kennwörter sollten nicht in benutzerdefinierten Benutzerspeicherdatenbanken abgelegt werden. Kennworthashes sollten stattdessen mit Salt-Werten gespeichert werden. Achten Sie darauf, dass der Salt-Wert für den Benutzer immer eindeutig ist und dass Sie vor dem Speichern des Kennworts bcrypt, scrypt oder PBKDF2 mit einer Mindestanzahl von 150.000 Schleifen für die Arbeitsfaktoriteration anwenden, um die Möglichkeit eines Brute-Force-Angriffs auszuschließen.| 

## <a id="db-encrypted"></a>Sicherstellen, dass sensible Daten in Datenbankspalten verschlüsselt sind

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | SQL-Version: Alle |
| **Referenzen**              | [Checkliste: Verschlüsseln sensibler Daten](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Vorgehensweise: Verschlüsseln einer Datenspalte](https://msdn.microsoft.com/library/ms179331), [ENCRYPTBYCERT (Transact-SQL)](https://msdn.microsoft.com/library/ms188061) |
| **Schritte** | Sensible Daten, z.B. Kreditkartennummern, müssen in der Datenbank verschlüsselt sein. Daten können verschlüsselt werden, indem die Verschlüsselung auf Spaltenebene oder eine Anwendungsfunktion für die Verschlüsselung verwendet wird. |

## <a id="tde-enabled"></a>Sicherstellen, dass die Verschlüsselung auf Datenbankebene (TDE) aktiviert ist

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Grundlegendes zur transparenten Datenverschlüsselung (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Schritte** | Die transparente Datenverschlüsselung (Transparent Data Encryption, TDE) in SQL Server ermöglicht das Verschlüsseln von sensiblen Daten in einer Datenbank und das Schützen der Schlüssel, die zum Verschlüsseln der Daten mit einem Zertifikat verwendet werden. So können die Daten nicht von Personen verwendet werden, die nicht im Besitz der Schlüssel sind. TDE schützt „ruhende Daten“, also die Daten und die Protokolldateien. Sie ermöglicht die Einhaltung von Gesetzen, Bestimmungen und Richtlinien, die in vielen Branchen etabliert sind. |

## <a id="backup"></a>Sicherstellen, dass Datenbanksicherungen verschlüsselt sind

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | SQL Azure, lokal |
| **Attribute**              | SQL-Version: V12, SQL-Version: MsSQL2014 |
| **Referenzen**              | [Verschlüsseln der Sicherung](https://msdn.microsoft.com/library/dn449489) |
| **Schritte** | In SQL Server können die Daten während der Erstellung einer Sicherung verschlüsselt werden. Indem der Verschlüsselungsalgorithmus und die Verschlüsselungsmethode (Zertifikat oder asymmetrischer Schlüssel) beim Erstellen einer Sicherung angegeben werden, kann eine verschlüsselte Sicherungsdatei erstellt werden. |

## <a id="api-browser"></a>Sicherstellen, dass für die Web-API relevante sensible Daten nicht im Speicher des Browsers gespeichert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC 5, MVC 6 |
| **Attribute**              | Identitätsanbieter: ADFS, Identitätsanbieter: Azure AD |
| **Referenzen**              | N/V  |
| **Schritte** | <p>In bestimmten Implementierungen werden sensible Artefakte, die für die Authentifizierung der Web-API relevant sind, im lokalen Speicher des Browsers abgelegt. Beispiele: Azure AD-Authentifizierungsartefakte wie adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key usw.</p><p>Alle diese Artefakte sind auch nach dem Abmelden oder dem Schließen des Browsers verfügbar. Wenn ein Angreifer Zugriff auf diese Artefakte erlangt, kann er sie zum Zugreifen auf die geschützten Ressourcen (APIs) wiederverwenden. Stellen Sie sicher, dass alle sensiblen Artefakte für die Web-API nicht im Speicher des Browsers abgelegt werden. Falls die Speicherung auf Clientseite nicht zu vermeiden ist (bei Single-Page-Anwendungen (SPA) mit Nutzung von impliziten OpenIdConnect/OAuth-Flows müssen Zugriffstoken beispielsweise lokal gespeichert werden), sollten Sie keine Lösungen mit dauerhafter Speicherung verwenden. Verwenden Sie beispielsweise nicht „LocalStorage“, sondern „SessionStorage“.</p>| 

### <a name="example"></a>Beispiel
Der unten angegebene JavaScript-Codeausschnitt stammt beispielsweise aus einer benutzerdefinierten Authentifizierungsbibliothek, in der Authentifizierungsartefakte im lokalen Speicher abgelegt werden. Implementierungen dieser Art sollten vermieden werden. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>Verschlüsseln sensibler Daten, die in Cosmos DB gespeichert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure DocumentDB | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Verschlüsseln Sie sensible Daten auf der Anwendungsebene, bevor Sie sie in DocumentDB speichern, oder speichern Sie sensible Daten in anderen Speicherlösungen wie Azure Storage oder Azure SQL.| 

## <a id="disk-vm"></a>Verwenden von Azure Disk Encryption zum Verschlüsseln von Datenträgern, die von virtuellen Computern verwendet werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure IaaS-VM-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Verwenden von Azure Disk Encryption zum Verschlüsseln von Datenträgern, die Ihre virtuellen Computer verwenden](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Schritte** | <p>Azure Disk Encryption ist ein neues Feature, das sich derzeit in der Vorschau befindet. Mit diesem Feature können Sie die Betriebssystemdatenträger und andere Datenträger verschlüsseln, die von einem virtuellen IaaS-Computer verwendet werden. Unter Windows werden Laufwerke mit branchenüblicher BitLocker-Verschlüsselung verschlüsselt. Unter Linux werden Datenträger mit der DM-Crypt-Technologie verschlüsselt. Diese ist in Azure Key Vault integriert, damit Sie die Datenträger-Verschlüsselungsschlüssel steuern und verwalten können. Die Lösung Azure Disk Encryption unterstützt die folgenden drei Kundenverschlüsselungsszenarien:</p><ul><li>Aktivieren der Verschlüsselung auf neuen IaaS-VMs, die auf der Basis vom Kunden verschlüsselter VHD-Dateien und vom Kunden bereitgestellter Verschlüsselungsschlüssel, die in Azure Key Vault gespeichert sind, erstellt wurden.</li><li>Aktivieren der Verschlüsselung auf neuen IaaS-VMs, die über Azure Marketplace erstellt wurden.</li><li>Aktivieren der Verschlüsselung auf vorhandenen IaaS-VMs, die bereits unter Azure ausgeführt werden.</li></ul>| 

## <a id="fabric-apps"></a>Verschlüsseln von Geheimnissen in Service Fabric-Anwendungen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Service Fabric-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Umgebung: Azure |
| **Referenzen**              | [Verwalten von Geheimnissen in Service Fabric-Anwendungen](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Schritte** | Geheimnisse beinhalten jegliche Art von vertraulichen Informationen (z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die nicht als Nur-Text verarbeitet werden sollen). Verwenden Sie Azure Key Vault zum Verwalten von Schlüsseln und Geheimnissen in Service Fabric-Anwendungen. |

## <a id="modeling-teams"></a>Durchführen der Sicherheitsmodellierung und Verwenden von Geschäftseinheiten/Teams nach Bedarf

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Durchführen der Sicherheitsmodellierung und Verwenden von Geschäftseinheiten/Teams nach Bedarf |

## <a id="entities"></a>Einschränken des Zugriffs auf die Funktion „Freigeben“ für kritische Entitäten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Einschränken des Zugriffs auf die Funktion „Freigeben“ für kritische Entitäten |

## <a id="good-practices"></a>Schulen von Benutzern in Bezug auf die Risiken der Funktion „Freigeben“ von Dynamics CRM und bewährte Sicherheitsmethoden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Schulen von Benutzern in Bezug auf die Risiken der Funktion „Freigeben“ von Dynamics CRM und bewährte Sicherheitsmethoden |

## <a id="exception-mgmt"></a>Einbinden einer Entwicklungsstandardregel, die das Anzeigen von Konfigurationsdetails bei der Ausnahmeverwaltung vorschreibt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Binden Sie eine Entwicklungsstandardregel ein, die das Anzeigen von Konfigurationsdetails bei der Ausnahmeverwaltung außerhalb der Entwicklung vorschreibt. Führen Sie im Rahmen von Codeprüfungen oder regelmäßigen Inspektionen entsprechende Tests durch.|

## <a id="sse-preview"></a>Verwenden von Azure Storage Service Encryption (SSE) für ruhende Daten (Vorschau)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | StorageType: Blob |
| **Referenzen**              | [Azure Storage Service Encryption für ruhende Daten (Vorschau)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Schritte** | <p>Azure Storage Service Encryption (SSE) für ruhende Daten unterstützt Sie dabei, Ihre Daten zu schützen, um die Sicherheits- und Complianceverpflichtungen Ihrer Organisation zu erfüllen. Mit diesem Feature verschlüsselt Azure Storage Ihre Daten automatisch, bevor sie im Speicher abgelegt werden, und entschlüsselt sie vor dem Abrufen. Verschlüsselung, Entschlüsselung und Schlüsselverwaltung sind für Benutzer vollständig transparent. SSE gilt nur für Blockblobs, Seitenblobs und Anfügeblobs. Die anderen Typen von Daten, einschließlich Tabellen, Warteschlangen und Dateien, werden nicht verschlüsselt.</p><p>Verschlüsselungs- und Entschlüsselungsworkflow:</p><ul><li>Der Kunde aktiviert die Verschlüsselung für das Speicherkonto.</li><li>Wenn der Kunde neue Daten (PUT Blob, PUT Block, PUT Page usw.) in den Blobspeicher schreibt, wird jeder Schreibvorgang per 256-Bit-AES-Verschlüsselung verschlüsselt. Dies ist eines der sichersten verfügbaren Blockchiffreverfahren.</li><li>Wenn der Kunde auf Daten zugreifen muss (GET Blob usw.), werden die Daten vor der Rückgabe an den Benutzer automatisch entschlüsselt.</li><li>Wenn die Verschlüsselung deaktiviert ist, werden neue Schreibvorgänge nicht mehr verschlüsselt, und vorhandene verschlüsselte Daten bleiben verschlüsselt, bis sie vom Benutzer neu geschrieben werden. Während die Verschlüsselung aktiviert ist, werden Schreibvorgänge in den Blobspeicher verschlüsselt. Der Zustand der Daten ändert sich nicht, wenn der Benutzer zwischen der Aktivierung und Deaktivierung der Verschlüsselung für das Speicherkonto wechselt.</li><li>Alle Verschlüsselungsschlüssel werden von Microsoft gespeichert, verschlüsselt und verwaltet</li></ul><p>Beachten Sie, dass die Schlüssel für die Verschlüsselung derzeit von Microsoft verwaltet werden. Microsoft führt die ursprüngliche Generierung der Schlüssel durch und verwaltet die sichere Speicherung der Schlüssel sowie die reguläre Rotation gemäß der internen Microsoft-Richtlinie. In Zukunft erhalten Kunden die Möglichkeit, ihre eigenen Verschlüsselungsschlüssel zu verwalten, und es wird ein Migrationspfad von den von Microsoft verwalteten Schlüsseln zu von Kunden verwalteten Schlüsseln bereitgestellt.</p>| 

## <a id="client-storage"></a>Verwenden der clientseitigen Verschlüsselung zum Speichern von sensiblen Daten in Azure Storage

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Tutorial: Verschlüsseln und Entschlüsseln von Blobs in Microsoft Azure Storage per Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [Storing Data Securely in Azure Blob Storage with Azure Encryption Extensions](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) (Sicheres Speichern von Daten in Azure Blob Storage mit Azure-Verschlüsselungserweiterungen) |
| **Schritte** | <p>Das NuGet-Paket für die Azure Storage-Clientbibliothek für .NET unterstützt die Verschlüsselung von Daten innerhalb von Clientanwendungen vor dem Hochladen der Daten nach Azure Storage sowie die Entschlüsselung von Daten während des Herunterladens auf den Client. Um eine Schlüsselverwaltung für Speicherkonten zu ermöglichen, unterstützt die Bibliothek zudem die Integration in Azure-Schlüsseltresor. Hier eine kurze Beschreibung zur Funktionsweise der clientseitigen Verschlüsselung:</p><ul><li>Das Azure Storage-Client-SDK generiert einen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK), bei dem es sich um einen einmalig verwendeten symmetrischen Schlüssel handelt.</li><li>Die Kundendaten werden mit diesem CEK verschlüsselt.</li><li>Der CEK wird dann mit dem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) umschlossen (verschlüsselt). Der KEK wird anhand eines Schlüsselbezeichners identifiziert und kann ein asymmetrisches Schlüsselpaar oder ein symmetrischer Schlüssel sein. Er kann lokal verwaltet oder in Azure Key Vault gespeichert werden. Der Storage-Client hat selbst niemals Zugriff auf den KEK. Er ruft lediglich den Algorithmus für das Umschließen des Schlüssels aus, der vom Schlüsseltresor bereitgestellt wird. Kunden können bei Bedarf benutzerdefinierte Anbieter für das Umschließen von Schlüsseln bzw. das Aufheben dieses Zustands verwenden.</li><li>Die verschlüsselten Daten werden dann in den Azure Storage-Dienst hochgeladen. Detaillierte Informationen zur Implementierung finden Sie unter den Links im Abschnitt „Referenzen“.</li></ul>|

## <a id="pii-phones"></a>Verschlüsseln von sensiblen Daten oder personenbezogenen Daten, die in den lokalen Speicher von Smartphones geschrieben werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Mobiler Client | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Generisch, Xamarin  |
| **Attribute**              | N/V  |
| **Referenzen**              | [Verwalten von Einstellungen und Features auf Ihren Geräten mit Microsoft Intune-Richtlinien](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), [Keychain Valet](https://components.xamarin.com/view/square.valet) (Keychain-Valet) |
| **Schritte** | <p>Wenn die Anwendung sensible Informationen, z.B. die personenbezogenen Informationen des Benutzers (E-Mail-Adresse, Telefonnummer, Vorname, Nachname, Präferenzen usw.), in das Dateisystem des Mobilgeräts schreibt, sollten sie vor der Durchführung dieses Schreibvorgangs in das lokale Dateisystem verschlüsselt werden. Wenn es sich um eine Unternehmensanwendung handelt, können Sie prüfen, ob die Veröffentlichung der Anwendung mit Windows Intune möglich ist.</p>|

### <a name="example"></a>Beispiel
Für Intune können die folgenden Sicherheitsrichtlinien zum Schutz von sensiblen Daten konfiguriert werden: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Beispiel
Falls die Anwendung keine Unternehmensanwendung ist, sollten Sie den von der Plattform bereitgestellten Keystore verwenden und Keychains zum Speichern von Verschlüsselungsschlüsseln verwenden (je nach Verschlüsselungsvorgang auf dem Dateisystem). Der folgende Codeausschnitt veranschaulicht, wie Sie mit Xamarin über die Keychain auf den Schlüssel zugreifen: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>Verschleiern von generierten Binärdateien vor der Verteilung an Endbenutzer

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Mobiler Client | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Crypto Obfuscator For .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Schritte** | Generierte Binärdateien (Assemblys in apk) sollten verschleiert werden, um ein Reverse Engineering von Assemblys zu verhindern. Hierfür können Tools wie `CryptoObfuscator` verwendet werden. |

## <a id="cert"></a>Festlegen von „clientCredentialType“ auf „Certificate“ oder „Windows“

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | N/V  |
| **Referenzen**              | [Fortify](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Schritte** | Bei der Verwendung eines UsernameToken mit einem Kennwort als Klartext über einen nicht verschlüsselten Kanal ist das Kennwort für Angreifer zugänglich, die die SOAP-Nachrichten ausspionieren können. Unter Umständen akzeptieren Service Providers, die UsernameToken verwenden, in Klartext gesendete Kennwörter. Beim Senden von Kennwörtern als Klartext über einen nicht verschlüsselten Kanal können die Anmeldeinformationen für Angreifer zugänglich sein, die die SOAP-Nachricht ausspionieren können. | 

### <a name="example"></a>Beispiel
Für die folgende WCF-Service Provider-Konfiguration wird das UsernameToken verwendet: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Legen Sie „clientCredentialType“ auf „Certificate“ oder „Windows“ fest. 

## <a id="security"></a>WCF-Sicherheitsmodus ist nicht aktiviert

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Generisch, .NET Framework 3 |
| **Attribute**              | Sicherheitsmodus: Transport, Sicherheitsmodus: Nachricht |
| **Referenzen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Fundamentals of WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) (Grundlagen der WCF-Sicherheit – CoDe Magazine) |
| **Schritte** | Es wurde keine Transport- oder Nachrichtensicherheit definiert. Für Anwendungen, die Nachrichten ohne Transport- oder Nachrichtensicherheit übertragen, kann die Integrität oder Vertraulichkeit der Nachrichten nicht garantiert werden. Wenn eine WCF-Sicherheitsbindung auf „None“ festgelegt ist, wird sowohl die Transport- als auch die Nachrichtensicherheit deaktiviert. |

### <a name="example"></a>Beispiel
In der folgenden Konfiguration ist der Sicherheitsmodus auf „None“ festgelegt. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Beispiel
Sicherheitsmodus: Über alle Dienstbindungen hinweg gibt es fünf mögliche Sicherheitsmodi: 
* None (Keine): Deaktiviert die Sicherheit. 
* Transport: Die Transportsicherheit wird für die gegenseitige Authentifizierung und den Nachrichtenschutz verwendet. 
* Message (Nachricht): Die Nachrichtensicherheit wird für die gegenseitige Authentifizierung und den Nachrichtenschutz verwendet. 
* Both (Beides): Ermöglicht Ihnen das Bereitstellen von Einstellungen für die Sicherheit auf Transport- und Nachrichtenebene (wird nur von MSMQ unterstützt). 
* TransportWithMessageCredential: Die Anmeldeinformationen werden zusammen mit der Nachricht übergeben, und der Nachrichtenschutz und die Serverauthentifizierung werden von der Transportschicht bereitgestellt. 
* TransportCredentialOnly: Die Clientanmeldeinformationen werden mit der Transportschicht übergeben, und es wird kein Nachrichtenschutz angewendet. Verwenden Sie die Transport- und Nachrichtensicherheit, um die Integrität und Vertraulichkeit von Nachrichten zu schützen. In der unten angegebenen Konfiguration wird der Dienst angewiesen, die Transportsicherheit mit Nachrichtenanmeldeinformationen zu verwenden.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```
