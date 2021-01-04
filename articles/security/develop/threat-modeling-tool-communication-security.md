---
title: Kommunikationssicherheit für das Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Erfahren Sie mehr über Gegenmaßnahmen für durch das Threat Modeling Tool offengelegte Kommunikationssicherheitsbedrohungen. Lesen Sie die Informationen zur Risikominderung, und sehen Sie sich die Codebeispiele an.
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
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517528"
---
# <a name="security-frame-communication-security--mitigations"></a>Sicherheitsrahmen: Kommunikationssicherheit | Gegenmaßnahmen 
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Schützen Sie die Kommunikation mit dem Event Hub mithilfe von SSL/TLS.](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Überprüfen Sie die Dienstkontoberechtigungen, und vergewissern Sie sich, dass die benutzerdefinierten Dienste oder ASP.NET-Seiten die CRM-Sicherheit respektieren.](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Verwenden Sie das Datenverwaltungsgateway, um eine Verbindung zwischen einer lokalen SQL Server-Instanz und Azure Data Factory herzustellen.](#sqlserver-factory)</li></ul> |
| **Identity Server** | <ul><li>[Stellen Sie sicher, dass der gesamte an Identity Server gerichtete Datenverkehr über eine HTTPS-Verbindung abgewickelt wird.](#identity-https)</li></ul> |
| **Web Application** | <ul><li>[Überprüfen Sie die X.509 Zertifikate, die zum Authentifizieren von SSL-, TLS- und DTLS-Verbindungen verwendet werden.](#x509-ssltls)</li><li>[Konfigurieren Sie ein TLS/SSL-Zertifikat für eine benutzerdefinierte Domäne in Azure App Service.](#ssl-appservice)</li><li>[Erzwingen Sie, dass der gesamte an Azure App Service gerichtete Datenverkehr über eine HTTPS-Verbindung abgewickelt wird.](#appservice-https)</li><li>[Aktivieren Sie HSTS (HTTP Strict Transport Security).](#http-hsts)</li></ul> |
| **Datenbank** | <ul><li>[Verwenden Sie SQL Server-Verbindungsverschlüsselung und Zertifikatüberprüfung.](#sqlserver-validation)</li><li>[Erzwingen Sie die Verschlüsselung der Kommunikation mit SQL Server.](#encrypted-sqlserver)</li></ul> |
| **Azure Storage (in englischer Sprache)** | <ul><li>[Stellen Sie sicher, dass die Kommunikation mit Azure Storage über HTTPS abgewickelt wird.](#comm-storage)</li><li>[Überprüfen Sie nach dem Herunterladen eines Blobs den MD5-Hash, falls HTTPS nicht aktiviert werden kann.](#md5-https)</li><li>[Verwenden Sie einen SMB 3.0-kompatiblen Client, um die Verschlüsselung von Daten während der Übertragung an Azure-Dateifreigaben zu gewährleisten.](#smb-shares)</li></ul> |
| **Mobiler Client** | <ul><li>[Implementieren Sie das Anheften von Zertifikaten.](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Aktivieren Sie den sicheren HTTPS-Transportkanal.](#https-transport)</li><li>[WCF: Legen Sie die Schutzebene für die Nachrichtensicherheit auf „EncryptAndSign“ fest.](#message-protection)</li><li>[WCF: Führen Sie den WCF-Dienst mit einem Konto mit möglichst wenigen Berechtigungen aus.](#least-account-wcf)</li></ul> |
| **Web-API** | <ul><li>[Erzwingen Sie, dass der gesamte an Web-APIs gerichtete Datenverkehr über eine HTTPS-Verbindung abgewickelt wird.](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Stellen Sie sicher, dass die Kommunikation mit Azure Cache for Redis über TLS abgewickelt wird.](#redis-ssl)</li></ul> |
| **Zwischengeschaltetes IoT-Gateway** | <ul><li>[Schützen Sie die Kommunikation zwischen Gerät und zwischengeschaltetem Gateway.](#device-field)</li></ul> |
| **IoT-Cloudgateway** | <ul><li>[Schützen Sie die Kommunikation zwischen Gerät und Cloudgateway mithilfe von SSL/TLS.](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Schützen Sie die Kommunikation mit dem Event Hub mithilfe von SSL/TLS.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Event Hub | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Event Hubs-Authentifizierung und -Sicherheitsmodell (Übersicht)](../../event-hubs/authenticate-shared-access-signature.md) |
| **Schritte** | Verwenden Sie SSL/TLS, um AMQP- oder HTTP-Verbindungen mit Event Hub zu schützen. |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Überprüfen Sie die Dienstkontoberechtigungen, und vergewissern Sie sich, dass die benutzerdefinierten Dienste oder ASP.NET-Seiten die CRM-Sicherheit respektieren.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Überprüfen Sie die Dienstkontoberechtigungen, und vergewissern Sie sich, dass die benutzerdefinierten Dienste oder ASP.NET-Seiten die CRM-Sicherheit respektieren. |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Verwenden Sie das Datenverwaltungsgateway, um eine Verbindung zwischen einer lokalen SQL Server-Instanz und Azure Data Factory herzustellen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Data Factory | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Verknüpfte Diensttypen: Azure und lokal |
| **Referenzen**              |[Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgatewayy](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway), [Datenverwaltungsgateway](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Schritte** | <p>Das DMG-Tool (Data Management Gateway; Datenverwaltungsgateway) wird benötigt, um eine Verbindung mit Datenquellen herzustellen, die durch ein Unternehmensnetzwerk oder eine Firewall geschützt sind.</p><ol><li>Bei einer Sperrung des Computers wird das DMG-Tool isoliert, und es wird verhindert, dass fehlerhafte Programme den Datenquellcomputer beschädigen oder ausspionieren. (Beispiele: Die neuesten Updates müssen installiert werden, die mindestens erforderlichen Ports müssen aktiviert werden, Konten müssen kontrolliert bereitgestellt werden, Überwachung und Datenträgerverschlüsselung müssen aktiviert werden usw.)</li><li>Der Datengatewayschlüssel muss in regelmäßigen Abständen bzw. bei jeder Erneuerung des DMG-Dienstkontokennworts ausgetauscht werden.</li><li>Datenübertragungen über den Verknüpfungsdienst müssen verschlüsselt werden.</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Stellen Sie sicher, dass der gesamte an Identity Server gerichtete Datenverkehr über eine HTTPS-Verbindung abgewickelt wird.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Identity Server | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [IdentityServer3 - Keys, Signatures and Cryptography](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) (IdentityServer3: Schlüssel, Signaturen und Kryptografie), [IdentityServer3 - Deployment](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) (IdentityServer3: Bereitstellung) |
| **Schritte** | Bei Identity Server muss für alle eingehenden Verbindungen standardmäßig HTTPS verwendet werden. Wichtig: Die Kommunikation mit Identity Server darf ausschließlich über geschützte Transportkanäle erfolgen. In bestimmten Bereitstellungsszenarien (etwa bei der TLS-Abladung) kann diese Anforderung gelockert werden. Weitere Informationen finden Sie auf der unter „Referenzen“ angegebenen Seite zur Identity Server-Bereitstellung. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Überprüfen Sie die X.509 Zertifikate, die zum Authentifizieren von SSL-, TLS- und DTLS-Verbindungen verwendet werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Anwendungen, die SSL, TLS oder DTLS verwenden, müssen die X.509-Zertifikate der Entitäten, mit denen sie eine Verbindung herstellen, umfassend überprüfen. Hierzu muss für die Zertifikate Folgendes überprüft werden:</p><ul><li>Domänenname</li><li>Gültigkeitsdaten (Anfangsdatum und Ablaufdatum)</li><li>Sperrstatus</li><li>Verwendung (beispielsweise Serverauthentifizierung bei Servern und Clientauthentifizierung bei Clients)</li><li>Vertrauenskette Zertifikate müssen mit einer Stammzertifizierungsstelle (Certification Authority, CA) verkettet sein, die von der Plattform als vertrauenswürdig eingestuft wird oder vom Administrator explizit konfiguriert wurde.</li><li>Der öffentliche Schlüssel des Zertifikats muss über 2048 Bit lang sein.</li><li>Als Hashalgorithmus muss mindestens SHA256 verwendet werden. |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurieren eines TLS/SSL-Zertifikats für eine benutzerdefinierte Domäne in Azure App Service

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | EnvironmentType: Azure |
| **Referenzen**              | [Aktivieren von HTTPS für eine App in Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Schritte** | Standardmäßig aktiviert Azure HTTPS bereits für jede App mit einem Platzhalterzertifikat für die Domäne „*.azurewebsites.net“. Platzhalterdomänen sind jedoch generell nicht so sicher wie die Verwendung einer benutzerdefinierten Domäne mit eigenem Zertifikat. (Weitere Informationen finden Sie [hier](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/).) Es empfiehlt sich, TLS für die benutzerdefinierte Domäne zu aktivieren, über die auf die bereitgestellte App zugegriffen wird.|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Erzwingen Sie, dass der gesamte an Azure App Service gerichtete Datenverkehr über eine HTTPS-Verbindung abgewickelt wird.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | EnvironmentType: Azure |
| **Referenzen**              | [Erzwingen von HTTPS in Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Schritte** | <p>Azure aktiviert zwar bereits standardmäßig HTTPS für Azure App Services mit einem Platzhalterzertifikat für die Domäne „*.azurewebsites.net“, erzwingt es aber nicht. Besucher können weiterhin über HTTP auf die App zugreifen, dies kann allerdings die Sicherheit der App gefährden. Daher muss explizit HTTPS erzwungen werden. ASP.NET-MVC-Anwendungen müssen den [RequireHttps-Filter](/dotnet/api/system.web.mvc.requirehttpsattribute) verwenden. Dieser sorgt dafür, dass ungeschützte HTTP-Anforderungen erneut über HTTPS gesendet werden müssen.</p><p>Alternativ kann HTTPS mithilfe des in Azure App Service enthaltenen URL-Rewrite-Moduls erzwungen werden. Mit dem URL-Rewrite-Modul können Entwickler Regeln definieren, die auf eingehende Anforderungen angewendet werden, bevor diese an Ihre Anwendung übergeben werden. URL-Rewrite-Regeln werden im Stammverzeichnis der Anwendung in einer Datei vom Typ „web.config“ gespeichert.</p>|

### <a name="example"></a>Beispiel
Das folgende Beispiel enthält eine einfache URL-Rewrite-Regel, die für sämtlichen eingehenden Datenverkehr die Verwendung von HTTPS erzwingt.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Diese Regel funktioniert durch die Rückgabe eines HTTP-Statuscode von 301 (Permanent Redirect), wenn der Benutzer eine Seite mit HTTP anfragt. Der Code 301 leitet die Anfrage an die gleiche URL weiter, die der Besucher angefragt hat, aber ersetzt den HTTP-Teil der Anfrage mit HTTPS. `HTTP://contoso.com` wird beispielsweise an `HTTPS://contoso.com` umgeleitet. 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Aktivieren Sie HSTS (HTTP Strict Transport Security).

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [OWASP HTTP Strict Transport Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) (OWASP-Cheat Sheet zu HTTP Strict Transport Security) |
| **Schritte** | <p>Bei HSTS (HTTP Strict Transport Security) handelt es sich um eine optionale Sicherheitserweiterung, die von einer Webanwendung mithilfe eines speziellen Antwortheaders angegeben wird. Wenn ein unterstützter Browser diesen Header empfängt, verhindert er, dass die Kommunikation über HTTP an die angegebene Domäne gesendet wird, und sendet die gesamte Kommunikation stattdessen über HTTPS. Darüber hinaus werden in Browsern auch HTTPS-Clickthrough-Aufforderungen verhindert.</p><p>Zur Implementierung von HSTS muss der folgende Antwortheader global für eine Website konfiguriert werden (entweder im Code oder in der Konfiguration): Strict-Transport-Security: max-age=300; includeSubDomains. HSTS dient zur Abwehr folgender Bedrohungen:</p><ul><li>Ein Benutzer erstellt ein Lesezeichen für `https://example.com` oder gibt die Adresse manuell ein und wird Opfer eines Man-in-the-Middle-Angriffs: HSTS leitet HTTP-Anforderungen für die Zieldomäne automatisch an HTTPS um.</li><li>Eine Webanwendung, die eigentlich als reine HTTPS-Anwendung konzipiert ist, enthält unbeabsichtigt HTTP-Links oder stellt Inhalt über HTTP bereit: HSTS leitet HTTP-Anforderungen für die Zieldomäne automatisch an HTTPS um.</li><li>Bei einem Man-in-the-Middle-Angriff wird versucht, Datenverkehr des betroffenen Benutzers mithilfe eines ungültigen Zertifikats abzufangen (in der Hoffnung, dass der Benutzer das falsche Zertifikat akzeptiert): HSTS lässt nicht zu, dass ein Benutzer die Meldung mit dem Hinweis auf ein ungültiges Zertifikat außer Kraft setzt.</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Verwenden Sie SQL Server-Verbindungsverschlüsselung und Zertifikatüberprüfung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | SQL Azure  |
| **Attribute**              | SQL-Version: V12 |
| **Referenzen**              | [Best Practices on Writing Secure Connection Strings for SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) (Bewährte Methoden zum Schreiben sicherer Verbindungszeichenfolgen für SQL-Datenbank) |
| **Schritte** | <p>Die gesamte Kommunikation zwischen SQL-Datenbank und einer Clientanwendung wird mit Transport Layer Security (TLS) verschlüsselt, früher als Secure Sockets Layer (SSL) bezeichnet. SQL-Datenbank unterstützt keine unverschlüsselten Verbindungen. Um Zertifikate mit Anwendungscode oder Tools zu überprüfen, sollten Sie explizit eine verschlüsselte Verbindung anfordern und den Serverzertifikaten nicht vertrauen. Verschlüsselte Verbindungen werden auch dann verwendet, wenn von Ihrem Anwendungscode oder von Ihren Tools keine verschlüsselte Verbindung angefordert wird.</p><p>Es kann aber sein, dass hierbei die Serverzertifikate nicht überprüft werden, sodass die Gefahr von Man-in-the-Middle-Angriffen besteht. Legen Sie `Encrypt=True` und `TrustServerCertificate=False` in der Datenbank-Verbindungszeichenfolge fest, um Zertifikate mit ADO.NET-Anwendungscode zu überprüfen. Um Zertifikate über SQL Server Management Studio zu überprüfen, öffnen Sie das Dialogfeld „Verbindung mit Server herstellen“. Klicken Sie auf der Registerkarte „Verbindungseigenschaften“ auf „Verbindung verschlüsseln“.</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Erzwingen Sie die Verschlüsselung der Kommunikation mit SQL Server.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Lokal |
| **Attribute**              | SQL-Version: MsSQL2016, SQL-Version: MsSQL2012, SQL-Version: MsSQL2014 |
| **Referenzen**              | [Aktivieren von verschlüsselten Verbindungen mit der Datenbank-Engine](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Schritte** | Die Aktivierung der TLS-Verschlüsselung erhöht die Sicherheit von Daten, die über Netzwerke zwischen Instanzen von SQL Server und Anwendungen übertragen werden. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Stellen Sie sicher, dass die Kommunikation mit Azure Storage über HTTPS abgewickelt wird.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Azure Storage-Sicherheitsleitfaden: Verschlüsselung auf Transportebene – mithilfe von HTTPS](../../storage/blobs/security-recommendations.md#networking) |
| **Schritte** | Um die Sicherheit von Azure Storage-Daten während der Übertragung zu gewährleisten, verwenden Sie immer das HTTPS-Protokoll, wenn Sie die REST-APIs aufrufen oder auf gespeicherte Objekte zugreifen. Mit Shared Access Signatures, die zum Delegieren des Zugriffs auf Azure Storage-Objekte verwendet werden können, können Sie außerdem festlegen, dass bei Verwendung von Shared Access Signatures nur das HTTPS-Protokoll verwendet werden darf. So können Sie sicherstellen, dass jeder, der Links mit SAS-Token sendet, das richtige Protokoll verwendet.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Überprüfen Sie nach dem Herunterladen eines Blobs den MD5-Hash, falls HTTPS nicht aktiviert werden kann.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | StorageType: Blob |
| **Referenzen**              | [Windows Azure Blob MD5 Overview](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) (MD5-Übersicht für Windows Azure-Blobs) |
| **Schritte** | <p>Der Windows Azure-Blob-Dienst stellt Mechanismen bereit, um die Datenintegrität sowohl auf der Anwendungs- als auch auf der Transportebene zu gewährleisten. Falls Sie aus irgendeinem Grund HTTP statt HTTPS verwenden müssen und mit Blockblobs arbeiten, können Sie die Integrität der übertragenen Blobs mithilfe einer MD5-Prüfung überprüfen.</p><p>Dies bietet Schutz vor Fehlern der Vermittlungs-/Transportschicht, aber nicht notwendigerweise vor zwischengeschalteten Angriffen. Wenn Sie HTTPS verwenden können, welches Schutz auf der Transportschicht bietet, ist eine MD5-Prüfung redundant und unnötig.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Verwenden Sie einen SMB 3.0-kompatiblen Client, um die Verschlüsselung von Daten während der Übertragung an Azure-Dateifreigaben zu gewährleisten.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Mobiler Client | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | StorageType: Datei |
| **Referenzen**              | [Azure File Storage, now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931) (Azure File Storage, jetzt allgemein verfügbar), [Erste Schritte mit Azure File Storage unter Windows](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Schritte** | Azure File Storage unterstützt HTTPS bei Verwendung der REST-API, wird jedoch häufiger als SMB-Dateifreigabe verwendet, die einer VM angefügt ist. SMB 2.1 unterstützt keine Verschlüsselung, sodass Verbindungen nur innerhalb der gleichen Region in Azure zulässig sind. Allerdings unterstützt SMB 3.0 die Verschlüsselung und kann mit Windows Server 2012 R2, Windows 8, Windows 8.1 und Windows 10 verwendet werden, sodass regionsübergreifender Zugriff und sogar Zugriff auf dem Desktop möglich ist. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementieren Sie das Anheften von Zertifikaten.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, Windows Phone |
| **Attribute**              | –  |
| **Referenzen**              | [Certificate and Public Key Pinning](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) (Anheften von Zertifikaten und öffentlichen Schlüsseln) |
| **Schritte** | <p>Das Anheften von Zertifikaten dient zur Abwehr von MITM-Angriffen (Man-in-the-Middle). Beim Anheften wird ein Host mit dem erwarteten X.509-Zertifikat oder öffentlichen Schlüssel verknüpft. Sobald ein Zertifikat oder öffentlicher Schlüssel für einen Host bekannt ist oder angezeigt wird, wird das Zertifikat oder der öffentliche Schlüssel mit dem Host verknüpft (angeheftet). </p><p>Startet ein Angreifer nun einen TLS-MITM-Angriff, unterscheidet sich der Schlüssel des für den Angriff verwendeten Servers beim TLS-Handshake vom Schlüssel des angehefteten Zertifikats, woraufhin die Anforderung verworfen und der MITM-Angriff abgewehrt wird. Das Anheften von Zertifikaten kann durch Implementieren des ServicePointManager-Delegaten `ServerCertificateValidationCallback` erreicht werden.</p>|

### <a name="example"></a>Beispiel
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Aktivieren Sie den sicheren HTTPS-Transportkanal.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Schritte** | Die Anwendungskonfiguration muss sicherstellen, dass bei jedem Zugriff auf sensible Daten HTTPS verwendet wird.<ul><li>**ERLÄUTERUNG:** Wenn eine Anwendung sensible Informationen enthält und keine Verschlüsselung auf Nachrichtenebene verwendet, darf sie ausschließlich über einen verschlüsselten Transportkanal kommunizieren.</li><li>**EMPFEHLUNGEN:** Stellen Sie sicher, dass der HTTP-Transport deaktiviert ist, und aktivieren Sie stattdessen den HTTPS-Transport. Ersetzen Sie beispielsweise `<httpTransport/>` durch das Tag `<httpsTransport/>`. Verlassen Sie sich nicht auf eine Netzwerkkonfiguration (Firewall), um zu gewährleisten, dass auf die Anwendung nur über einen sicheren Kanal zugegriffen werden kann. In der Theorie darf die Sicherheit der Anwendung nicht vom Netzwerk abhängig sein.</li></ul><p>In der Praxis sind die für den Schutz des Netzwerks zuständigen Personen bei den Sicherheitsanforderungen der Anwendung nicht immer auf dem neuesten Stand.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Legen Sie die Schutzebene für die Nachrichtensicherheit auf „EncryptAndSign“ fest.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Schritte** | <ul><li>**ERLÄUTERUNG:** Wenn die Schutzebene auf „none“ festgelegt wird, wird der Nachrichtenschutz deaktiviert. Vertraulichkeit und Integrität werden durch eine geeignete Ebeneneinstellung erreicht.</li><li>**EMPFEHLUNGEN:**<ul><li>`Mode=None`: Deaktiviert den Nachrichtenschutz.</li><li>`Mode=Sign`: Signiert die Nachricht, verschlüsselt sie aber nicht. Empfiehlt sich, wenn Datenintegrität wichtig ist.</li><li>`Mode=EncryptAndSign`: Signiert und verschlüsselt die Nachricht.</li></ul></li></ul><p>Wenn Sie lediglich die Integrität der Informationen gewährleisten müssen und die Vertraulichkeit keine Rolle spielt, können Sie die Verschlüsselung ggf. deaktivieren und Ihre Nachricht nur signieren. Dies ist unter Umständen bei Vorgängen oder Dienstverträgen hilfreich, bei denen Sie den ursprünglichen Absender überprüfen müssen, aber keine sensiblen Daten übertragen werden. Achten Sie beim Verringern der Schutzebene darauf, dass die Nachricht keine personenbezogenen Daten enthält.</p>|

### <a name="example"></a>Beispiel
In den folgenden Beispielen wird gezeigt, wie Sie Dienst und Vorgang so konfiguriert, dass die Nachricht nur signiert wird. Dienstvertragsbeispiel mit `ProtectionLevel.Sign`: Das folgende Beispiel veranschaulicht die Verwendung von „ProtectionLevel.Sign“ auf Dienstvertragsebene: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Beispiel
Vorgangsvertragsbeispiel mit `ProtectionLevel.Sign` (für präzise Steuerung): Das folgende Beispiel veranschaulicht die Verwendung von `ProtectionLevel.Sign` auf Vorgangsvertragsebene:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Führen Sie den WCF-Dienst mit einem Konto mit möglichst wenigen Berechtigungen aus.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Schritte** | <ul><li>**ERLÄUTERUNG:** Führen Sie WCF-Dienste nicht unter einem Administratorkonto oder unter einem Konto mit hohen Berechtigungen aus. Andernfalls hat die Kompromittierung eines Diensts weitreichende Auswirkungen.</li><li>**EMPFEHLUNGEN:** Hosten Sie Ihren WCF-Dienst unter Verwendung eines Kontos mit möglichst wenigen Berechtigungen, um die Angriffsfläche Ihrer Anwendung und den potenziellen Schaden eines Angriffs zu minimieren. Sollte das Dienstkonto zusätzliche Zugriffsrechte für Infrastrukturressourcen wie MSMQ, das Ereignisprotokoll, Leistungsindikatoren und das Dateisystem benötigen, müssen für diese Ressourcen entsprechende Berechtigungen gewährt werden, damit der WCF-Dienst erfolgreich ausgeführt werden kann.</li></ul><p>Falls Ihr Dienst im Auftrag des ursprünglichen Aufrufers auf bestimmte Ressourcen zugreifen muss, verwenden Sie Identitätswechsel und Delegierung, um die Identität des Aufrufers für eine nachgelagerte Autorisierungsprüfung weiterzugeben. Verwenden Sie in einem Entwicklungsszenario das lokale Netzwerkdienstkonto. Hierbei handelt es sich um ein spezielles integriertes Konto mit eingeschränkten Berechtigungen. Erstellen Sie in einem Produktionsszenario ein benutzerdefiniertes Domänendienstkonto mit möglichst wenigen Berechtigungen.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Erzwingen Sie, dass der gesamte an Web-APIs gerichtete Datenverkehr über eine HTTPS-Verbindung abgewickelt wird.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Enforcing SSL in a Web API Controller](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) (Erzwingen von SSL in einem Web-API-Controller) |
| **Schritte** | Wenn eine Anwendung sowohl über eine HTTPS-Bindung als auch über eine HTTP-Bindung verfügt, können Clients weiterhin über HTTP auf die Website zugreifen. Stellen Sie daher mithilfe eines Aktionsfilters sicher, dass Anforderungen an geschützte APIs immer über HTTPS abgewickelt werden.|

### <a name="example"></a>Beispiel 
Der folgende Code zeigt einen Web-API-Authentifizierungsfilter mit Überprüfung auf TLS: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Fügen Sie diesen Filter allen Web-API-Aktionen hinzu, die TLS erfordern: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Sicherstellen, dass die Kommunikation mit Azure Cache for Redis über TLS abgewickelt wird

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Cache for Redis | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Azure Redis: TLS-Unterstützung](../../azure-cache-for-redis/cache-faq.md) |
| **Schritte** | Der Redis-Server bietet keine integrierte TLS-Unterstützung, Azure Cache for Redis dagegen schon. Wenn Sie eine Verbindung mit Azure Cache for Redis herstellen und Ihr Client TLS unterstützt (z. B. StackExchange.Redis), sollten Sie TLS verwenden. Bei neuen Azure Cache for Redis-Instanzen ist der TLS-fremde Port standardmäßig deaktiviert. Die sicheren Standardeinstellungen sollten nur geändert werden, wenn Redis-Clients auf TLS-Unterstützung angewiesen sind. |

Redis ist für den Zugriff durch vertrauenswürdige Clients in vertrauenswürdigen Umgebungen konzipiert. Es empfiehlt sich daher in der Regel nicht, die Redis-Instanz direkt dem Internet auszusetzen (oder ganz allgemein einer Umgebung, in der nicht vertrauenswürdige Clients direkt auf den Redis-TCP-Port oder UNIX-Socket zugreifen können). 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Schützen Sie die Kommunikation zwischen Gerät und zwischengeschaltetem Gateway.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Zwischengeschaltetes IoT-Gateway | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Bei IP-basierten Geräten kann das Kommunikationsprotokoll in der Regel in einem SSL-/TLS-Kanal gekapselt werden, um die Daten während der Übertragung zu schützen. Ermitteln Sie bei anderen Protokollen ohne SSL-/TLS-Unterstützung, ob sichere Versionen des Protokolls zur Verfügung stehen, die Sicherheit auf Transport- oder Nachrichtenebene bieten. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Schützen Sie die Kommunikation zwischen Gerät und Cloudgateway mithilfe von SSL/TLS.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Cloudgateway | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Choose your Communication Protocol](../../iot-hub/iot-hub-devguide.md) (Auswählen Ihres Kommunikationsprotokolls) |
| **Schritte** | Schützen Sie HTTP-/AMQP- oder MQTT-Protokolle mithilfe von SSL/TLS. |