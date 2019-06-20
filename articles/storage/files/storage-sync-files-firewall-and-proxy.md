---
title: Lokale Firewall- und -Proxyeinstellungen der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Lokale Netzwerkkonfiguration der Azure-Dateisynchronisierung
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d9b7296a116ebd06542a53087afbd083dbd3a7eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696747"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- und Firewalleinstellungen der Azure-Dateisynchronisierung
Die Azure-Dateisynchronisierung verbindet Ihre lokalen Server mit Azure Files, wodurch Synchronisierung für mehrere Standorte und Cloudtiering-Funktionalität ermöglicht werden. Daher muss ein lokaler Server eine Verbindung mit dem Internet haben. Ein IT-Administrator muss den besten Weg festlegen, auf dem der Server zu den Azure-Clouddiensten gelangt.

Dieser Artikel gibt einen Einblick in die speziellen Anforderungen und Optionen, die verfügbar sind, um Ihren Server erfolgreich und sicher mit der Azure-Dateisynchronisierung zu verbinden.

> [!Important]
> Firewalls und virtuelle Netzwerke werden von der Azure-Dateisynchronisierung noch nicht für ein Speicherkonto unterstützt.

## <a name="overview"></a>Übersicht
Die Azure-Dateisynchronisierung fungiert als Orchestrierungsdienst zwischen Ihrem Windows-Server, Ihrer Azure-Dateifreigabe und mehreren anderen Azure-Diensten, um Daten so zu synchronisieren, wie dies in Ihrer Synchronisierungsgruppe beschrieben ist. Damit die Azure-Dateisynchronisierung korrekt funktioniert, müssen Sie Ihre Server so konfigurieren, dass sie mit den folgenden Azure-Diensten kommunizieren:

- Azure Storage
- Azure-Dateisynchronisierung
- Azure Resource Manager
- Authentifizierungsdienste

> [!Note]  
> Der Azure-Dateisynchronisierungs-Agent unter Windows Server initiiert alle Anforderungen an die Clouddienste, was dazu führt, dass aus Sicht einer Firewall nur ausgehender Datenverkehr berücksichtigt werden muss. <br /> Keiner der Azure-Dienste initiiert eine Verbindung mit dem Azure-Dateisynchronisierungs-Agent.

## <a name="ports"></a>Ports
Die Azure-Dateisynchronisierung bewegt Dateidaten und Metadaten ausschließlich über HTTPS und erfordert, dass Port 443 für ausgehenden Datenverkehr geöffnet ist.
Daher wird der gesamte Datenverkehr verschlüsselt.

## <a name="networks-and-special-connections-to-azure"></a>Netzwerke und spezielle Verbindungen mit Azure
Der Azure-Dateisynchronisierungs-Agent hat keine Anforderungen hinsichtlich spezieller Kanäle wie [ExpressRoute](../../expressroute/expressroute-introduction.md) usw. zu Azure.

Die Azure-Dateisynchronisierung nutzt alle verfügbaren Mittel, die Zugriff in Azure ermöglichen, passt sich automatisch an verschiedene Netzwerkeigenschaften wie Bandbreite, Wartezeit an und bietet Administratorsteuerung zur Feinabstimmung. Derzeit sind nicht alle Funktionen verfügbar. Wenn Sie ein bestimmtes Verhalten konfigurieren möchten, können Sie uns dies über [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) mitteilen.

## <a name="proxy"></a>Proxy
Die Azure-Dateisynchronisierung unterstützt App-spezifische und computerweite Proxyeinstellungen.

**App-spezifische Proxyeinstellungen** ermöglichen es, einen Proxy speziell für den Datenverkehr der Azure-Dateisynchronisierung zu konfigurieren. App-spezifische Proxyeinstellungen werden ab Version 4.0.1.0 des Agents unterstützt und können während der Installation des Agents oder mit dem PowerShell-Cmdlet Set-StorageSyncProxyConfiguration konfiguriert werden.

PowerShell-Befehle zum Konfigurieren von App-spezifischen Proxyeinstellungen:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Die **computerweiten Proxyeinstellungen** sind für den Azure-Dateisynchronisierungs-Agent transparent, da der gesamte Datenverkehr des Servers über den Proxy geleitet wird.

Um computerweite Proxyeinstellungen zu konfigurieren, führen Sie in die folgenden Schritte aus: 

1. Konfigurieren von Proxyeinstellungen für .NET-Anwendungen 

   - Bearbeiten Sie diese beiden Dateien:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Fügen Sie den Abschnitt <system.net> in den machine.config-Dateien (unter dem Abschnitt <system.serviceModel>) hinzu.  Ändern Sie 127.0.01:8888 in die IP-Adresse und den Port des Proxyservers. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Festlegen der WinHTTP-Proxyeinstellungen 

   - Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten oder in PowerShell aus, um die vorhandene Proxyeinstellung anzuzeigen:   

     netsh winhttp show proxy

   - Führen den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten oder in PowerShell aus, um die Proxyeinstellung festzulegen (ändern Sie 127.0.01:8888 in die IP-Adresse und den Port für den Proxyserver):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Starten Sie den Storage-Synchronisierungs-Agent-Dienst neu, indem Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten oder in PowerShell ausführen: 

      net stop filesyncsvc

      Hinweis: Der Storage-Synchronisierungs-Agent-Dienst (filesyncsvc) wird automatisch gestartet, nachdem er beendet wurde.

## <a name="firewall"></a>Firewall
Wie in einem vorhergehenden Abschnitt erwähnt, muss Port 443 für ausgehenden Datenverkehr geöffnet sein. Entsprechend den Richtlinien in Ihrem Rechenzentrum, Ihrer Niederlassung oder Ihrer Region kann eine weitere Beschränkung des Datenverkehrs über diesen Port auf bestimmte Domänen erwünscht oder erforderlich sein.

In der folgenden Tabelle sind die für eine Kommunikation erforderlichen Domänen beschrieben:

| Dienst | Öffentlicher Cloudendpunkt | Azure Government-Endpunkt | Verwendung |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Jeder Benutzeraufruf (etwa PowerShell) geht an/über diese URL, dazu gehört auch der erstmalige Serverregistrierungsaufruf. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Azure Resource Manager-Aufrufe müssen von einem authentifizierten Benutzer vorgenommen werden. Um erfolgreich zu sein, wird diese URL für die Benutzerauthentifizierung verwendet. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Im Rahmen der Bereitstellung der Azure-Dateisynchronisierung wird ein Dienstprinzipal in Azure Active Directory des Abonnements erstellt. Diese URL wird dazu verwendet. Dieser Prinzipal wird dazu verwendet, einen minimalen Satz von Berechtigungen an den Azure-Dateisynchronisierungsdienst zu delegieren. Der Benutzer, der die erste Einrichtung der Azure-Dateisynchronisierung ausführt, muss ein authentifizierter Benutzer mit Abonnementbesitzerberechtigungen sein. |
| **Azure Storage (in englischer Sprache)** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Beim Herunterladen einer Datei auf dem Server wird diese Datenverschiebung effizienter ausgeführt, wenn eine direkte Verbindung zwischen dem Server und der Azure-Dateifreigabe im Speicherkonto besteht. Der Server hat einen SAS-Schlüssel, der nur gezielten Dateifreigabezugriff zulässt. |
| **Azure-Dateisynchronisierung** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Nach der erstmaligen Serverregistrierung erhält der Server eine regionale URL für die Azure-Dateisynchronisierungs-Dienstinstanz in dieser Region. Der Server kann über die URL direkt und effizient mit der Instanz kommunizieren, die seine Synchronisierung verwaltet. |
| **Microsoft PKI** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Sobald der Agent für die Azure-Dateisynchronisierung installiert ist, werden über die PKI-URL Zwischenzertifikate heruntergeladen, die für die Kommunikation mit dem Azure-Dateisynchronisierungsdienst und der Azure-Dateifreigabe erforderlich sind. Mithilfe der OCSP-URL wird der Status eines Zertifikats überprüft. |

> [!Important]
> Wenn Datenverkehr über „&ast;.one.microsoft.com“ zugelassen wird, kann Datenverkehr vom Server nicht nur an den Synchronisierungsdienst, sondern auch an weitere Stellen gesendet werden. Es gibt viele weitere Microsoft-Dienste unter Unterdomänen.

Wenn „&ast;.one.microsoft.com“ zu ausgedehnt ist, können Sie die Kommunikation des Servers begrenzen, indem Sie eine Kommunikation nur explizite regionale Instanzen des Azure File Sync-Diensts zulassen. Welche Instanz(en) ausgewählt werden muss/müssen, hängt von der Region des Speichersynchronisierungsdienst ab, für den Sie den Server bereitgestellt und registriert haben. Diese Region heißt in der folgenden Tabelle „Primäre Endpunkt-URL“.

Für Business Continuity und Disaster Recovery (BCDR) haben Sie Ihre Azure-Dateifreigaben möglicherweise in einem georedundanten (GRS) Speicherkonto angegeben. Wenn das der Fall ist, werden Ihre Azure-Dateifreigaben im Falle eines dauerhaften regionalen Ausfalls auf die gekoppelte Region übertragen. Die Azure-Dateisynchronisierung verwendet die gleichen regionalen Kombinationen als Speicher. Wenn Sie also GRS-Speicherkonten verwenden, müssen Sie zusätzliche URLs aktivieren, damit Ihr Server mit der gekoppelten Region für die Azure-Dateisynchronisierung kommunizieren kann. In der folgenden Tabelle wird dies als „gekoppelte Region“ bezeichnet. Darüber hinaus muss eine Traffic Manager-Profil-URL aktiviert werden. Dadurch wird sichergestellt, dass der Netzwerkverkehr im Falle eines Failovers nahtlos in die gekoppelte Region umgeleitet werden kann. Die URL ist in der folgenden Tabelle als „Ermittlungs-URL“ bezeichnet.

| Cloud  | Region | URL des primären Endpunkts | Regionspaar | Ermittlungs-URL |
|--------|--------|----------------------|---------------|---------------|
| Öffentlich |Australien (Osten) | https://kailani-aue.one.microsoft.com | Australien, Südosten | https://kailani-aue.one.microsoft.com |
| Öffentlich |Australien, Südosten | https://kailani-aus.one.microsoft.com | Australien (Osten) | https://tm-kailani-aus.one.microsoft.com |
| Öffentlich | Kanada, Mitte | https://kailani-cac.one.microsoft.com | Kanada, Osten | https://tm-kailani-cac.one.microsoft.com |
| Öffentlich | Kanada, Osten | https://kailani-cae.one.microsoft.com | Kanada, Mitte | https://tm-kailani.cae.one.microsoft.com |
| Öffentlich | USA (Mitte) | https://kailani-cus.one.microsoft.com | USA (Ost) 2 | https://tm-kailani-cus.one.microsoft.com |
| Öffentlich | Asien, Osten | https://kailani11.one.microsoft.com | Asien, Südosten | https://tm-kailani11.one.microsoft.com |
| Öffentlich | USA (Ost) | https://kailani1.one.microsoft.com | USA (Westen) | https://tm-kailani1.one.microsoft.com |
| Öffentlich | USA (Ost) 2 | https://kailani-ess.one.microsoft.com | USA (Mitte) | https://tm-kailani-ess.one.microsoft.com |
| Öffentlich | Nordeuropa | https://kailani7.one.microsoft.com | Europa, Westen | https://tm-kailani7.one.microsoft.com |
| Öffentlich | Asien, Südosten | https://kailani10.one.microsoft.com | Asien, Osten | https://tm-kailani10.one.microsoft.com |
| Öffentlich | UK, Süden | https://kailani-uks.one.microsoft.com | UK, Westen | https://tm-kailani-uks.one.microsoft.com |
| Öffentlich | UK, Westen | https://kailani-ukw.one.microsoft.com | UK, Süden | https://tm-kailani-ukw.one.microsoft.com |
| Öffentlich | Europa, Westen | https://kailani6.one.microsoft.com | Nordeuropa | https://tm-kailani6.one.microsoft.com |
| Öffentlich | USA (Westen) | https://kailani.one.microsoft.com | USA (Ost) | https://tm-kailani.one.microsoft.com |
| Behörden | US Gov Arizona | https://usgovarizona01.afs.azure.us | US Gov Texas | https://tm-usgovarizona01.afs.azure.us |
| Behörden | US Gov Texas | https://usgovtexas01.afs.azure.us | US Gov Arizona | https://tm-usgovtexas01.afs.azure.us |

- Wenn Sie lokal redundanten (LRS) oder zonenredundante (ZRS) Speicherkonten verwenden, müssen Sie nur die URL aktivieren, die unter "Primäre Endpunkt-URL" aufgeführt ist.

- Wenn Sie georedundante (GRS) Speicherkonten verwenden, aktivieren Sie die drei URLs.

**Beispiel:** Sie stellen einen Speichersynchronisierungsdienst in `"West US"` bereit und registrieren damit Ihren Server. Die URLs, mit denen der Server in diesem Fall kommunizieren kann, sind:

> - https://kailani.one.microsoft.com (primärer Endpunkt: USA, Westen)
> - https://kailani1.one.microsoft.com (gekoppelte Failoverregion: USA, Osten)
> - https://tm-kailani.one.microsoft.com (Ermittlungs-URL der primären Region)

## <a name="summary-and-risk-limitation"></a>Zusammenfassung und Risikobegrenzung
Die Listen weiter oben in diesem Dokument enthalten die URLs, mit denen die Azure-Dateisynchronisierung derzeit kommuniziert. Firewalls müssen in der Lage sein, ausgehenden Datenverkehr zu diesen Domänen zuzulassen. Microsoft ist bestrebt, diese Liste auf dem neuesten Stand zu halten.

Ein Einrichten von domäneneinschränkenden Firewallregeln kann eine Maßnahme sein, die Sicherheit zu verbessern. Wenn Sie diese Firewallkonfigurationen verwenden, müssen Sie bedenken, dass URLs hinzugefügt und im Verlauf der Zeit vielleicht geändert werden. Lesen Sie diesen Artikel in regelmäßigen Abständen.

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
- [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
