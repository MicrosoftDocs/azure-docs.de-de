---
title: Sperren von ausgehendem Datenverkehr
description: Erfahren Sie, wie Sie mithilfe der Azure Firewall-Integration innerhalb einer App Service-Umgebung ausgehenden Datenverkehr schützen.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: e5f9cd361d4f130d725f608614159d67fb7b56d1
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026359"
---
# <a name="locking-down-an-app-service-environment"></a>Sperren einer App Service-Umgebung

Die App Service-Umgebung verfügt über externe Abhängigkeiten, auf die sie Zugriff benötigt, um ordnungsgemäß zu funktionieren. Die App Service-Umgebung befindet sich in der Azure Virtual Network-Instanz (VNET) des Benutzers. Benutzer müssen den Datenverkehr für Abhängigkeiten der App Service-Umgebung zulassen. Dies ist jedoch problematisch, wenn sie den gesamten ausgehenden Datenverkehr ihres VNET sperren wollen.

Es gibt eine Reihe von Endpunkten für eingehenden Datenverkehr, die zum Verwalten einer App Service-Umgebung verwendet werden. Der eingehende Verwaltungsdatenverkehr kann über eine Firewallgerät nicht gesendet werden. Die Quelladressen für diesen Datenverkehr sind bekannt und werden im Dokument [Verwaltungsadressen der App Service-Umgebung](./management-addresses.md) veröffentlicht. Es gibt auch ein Diensttag mit dem Namen AppServiceManagement, das mit Netzwerksicherheitsgruppen (NSGs) verwendet werden kann, um den eingehenden Datenverkehr zu sichern.

Die Abhängigkeiten der App Service-Umgebung für den ausgehenden Datenverkehr werden fast ausschließlich mit FQDNs definiert, hinter denen sich keine statischen Adressen befinden. Das Fehlen statischer Adressen bedeutet, dass Netzwerksicherheitsgruppen nicht verwendet werden können, um den ausgehenden Datenverkehr einer App Service-Umgebung zu sperren. Die Adressen ändern sich häufig, sodass keine Regeln auf Grundlage der aktuellen Auflösung aufgestellt und keine NSGs damit erstellt werden können. 

Die Lösung zum Sichern ausgehender Adressen besteht in der Verwendung eines Firewallgeräts, das den ausgehenden Datenverkehr basierend auf Domänennamen kontrolliert. Azure Firewall kann ausgehenden HTTP- und HTTPS-Datenverkehr basierend auf den FQDN des Ziels beschränken.  

## <a name="system-architecture"></a>Systemarchitektur

Das Bereitstellen einer App Service-Umgebung (App Service Environment, ASE) mit ausgehendem Datenverkehr über ein Firewallgerät erfordert das Ändern von Routen im ASE-Subnetz. Routen funktionieren auf einer IP-Ebene. Wenn Sie die Routen nicht sorgfältig definieren, können Sie den TCP-Antwortdatenverkehr an die Quelle von einer anderen Adresse erzwingen. Wenn sich Ihre Antwortadresse von der Adresse unterscheidet, an die der Datenverkehr gesendet wurde, wird TCP durch dieses als asymmetrisches Routing bezeichnete Problem unterbrochen.

Es müssen Routen definiert sein, damit der eingehende Datenverkehr an die ASE die Antwort auf die gleiche Weise zurücksenden kann, wie der Datenverkehr eintraf. Routen müssen für eingehende Verwaltungsanforderungen und für eingehende Anwendungsanforderungen definiert sein.

Beim Datenverkehr an und von einer ASE müssen die folgenden Konventionen eingehalten werden:

* Der Datenverkehr an Azure SQL, Storage und Event Hub wird bei Verwendung eines Firewallgeräts nicht unterstützt. Dieser Datenverkehr muss direkt an diese Dienste gesendet werden. Um dies zu ermöglichen, müssen Dienstendpunkte für diese drei Dienste konfiguriert werden. 
* Es müssen Routingtabellenregeln definiert werden, die eingehenden Verwaltungsdatenverkehr an seinen Ursprungsort zurücksenden.
* Es müssen Routingtabellenregeln definiert werden, die eingehenden Verwaltungsdatenverkehr an seinen Ursprungsort zurücksenden. 
* Jeglicher sonstiger Datenverkehr von der ASE kann mit einer Routingtabellenregel an Ihr Firewallgerät gesendet werden.

![ASE mit Azure Firewall: Verbindungsfluss][5]

## <a name="locking-down-inbound-management-traffic"></a>Sperren der Verwaltung des eingehenden Datenverkehrs

Wenn Subnetz Ihrer App Service-Umgebung nicht bereits eine NSG zugewiesen ist, erstellen Sie eine Netzwerksicherheitsgruppe. Legen Sie in der NSG als erste Regel fest, dass Datenverkehr des Diensttags „AppServiceManagement“ an den Ports 454 und 455 zugelassen werden soll. Die Regel zum Zulassen des Zugriffs für das Tag „AppServiceManagement“ ist das Einzige, was von öffentlichen IP-Adressen erforderlich ist, um Ihre ASE zu verwalten. Die Adressen, die sich hinter diesem Diensttag befinden, werden nur zum Verwalten von Azure App Service verwendet. Der Verwaltungsdatenverkehr, der durch diese Verbindungen fließt, wird verschlüsselt und durch Authentifizierungszertifikate gesichert. Der typische Datenverkehr in diesem Kanal umfasst Dinge wie vom Kunden initiierte Befehle und Integritätstests. 

App Service-Umgebungen, die über das Portal mit einem neuen Subnetz erstellt werden, werden mit einer NSG eingerichtet, die die Zulassungsregel für das AppServiceManagement-Tag enthält.  

Ihre ASE muss auch eingehende Anforderungen des Tags „Load Balancer“ am Port 16001 zulassen. Die Load Balancer-Anforderungen am Port 16001 sind Keep-Alive-Prüfungen zwischen Load Balancer und ASE-Front-Ends. Ist der Port 16001 blockiert, wird Ihre ASE fehlerhaft.

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurieren von Azure Firewall mit Ihrer App Service-Umgebung 

Gehen Sie wie folgt vor, um ausgehenden Datenverkehr Ihrer bestehenden App Service-Umgebung (App Service Environment, ASE) mit Azure Firewall zu sperren:

1. Aktivieren Sie Dienstendpunkte für SQL, Storage und Event Hub in Ihrem ASE-Subnetz. Um Dienstendpunkte zu aktivieren, navigieren Sie im Netzwerkportal zu „Subnetze“, und wählen Sie in der Dropdownliste „Dienstendpunkte“ die Optionen „Microsoft.EventHub“, „Microsoft.SQL“ und „Microsoft.Storage“ aus. Wenn Sie Dienstendpunkte für Azure SQL aktiviert haben, müssen alle Azure SQL-Abhängigkeiten Ihrer Apps ebenfalls mit Dienstendpunkten konfiguriert werden. 

   ![Auswählen von Dienstendpunkten][2]
  
1. Erstellen Sie ein Subnetz namens „AzureFirewallSubnet“ in dem VNET, in dem sich die ASE befindet. Folgen Sie den Anweisungen in der [Azure Firewall-Dokumentation](../../firewall/index.yml), um Ihre Azure Firewall-Instanz zu erstellen.

1. Navigieren Sie in der Azure Firewall-Benutzeroberfläche zu „Regeln“ > „Anwendungsregelsammlung“, und wählen Sie „Anwendungsregelsammlung hinzufügen“ aus. Geben Sie einen Namen und eine Priorität an, und legen Sie „Zulassen“ fest. Geben Sie im Abschnitt „FQDN-Tags“ einen Namen an, legen Sie die Quelladressen auf „*“ fest, und wählen Sie die FQDN-Tags „AppServiceEnvironment“ und „WindowsUpdate“ aus. 
   
   ![Hinzufügen einer Anwendungsregel][1]
   
1. Navigieren Sie in der Azure Firewall-Benutzeroberfläche zu „Regeln“ > „Netzwerkregelsammlung“, und wählen Sie „Netzwerkregelsammlung hinzufügen“ aus. Geben Sie einen Namen und eine Priorität an, und legen Sie „Zulassen“ fest. Geben Sie im Abschnitt „Regeln“ unter „IP-Adressen“ einen Namen an, wählen Sie als Protokoll **Beliebig** aus, legen Sie „*“ auf die Quell- und Zieladressen und die Ports auf 123 fest. Diese Regel ermöglicht es dem System, die Uhrsynchronisierung mit NTP durchzuführen. Erstellen Sie wie oben beschrieben eine weitere Regel für Port 12000, um die Selektierung von Systemproblemen zu erleichtern. 

   ![Hinzufügen einer NTP-Netzwerkregel][3]
   
1. Navigieren Sie in der Azure Firewall-Benutzeroberfläche zu „Regeln“ > „Netzwerkregelsammlung“, und wählen Sie „Netzwerkregelsammlung hinzufügen“ aus. Geben Sie einen Namen und eine Priorität an, und legen Sie „Zulassen“ fest. Geben Sie im Abschnitt „Regeln“ unter „Diensttags“ einen Namen ein, wählen Sie als Protokoll **Beliebig** aus, legen Sie „*“ auf Quelladressen fest, wählen Sie ein Diensttag AzureMonitor aus, und legen Sie die Ports auf 80 und 443 fest. Diese Regel ermöglicht es dem System, für Azure Monitor Integritäts- und Metrikinformationen bereitzustellen.

   ![Hinzufügen einer NTP-Diensttag-Netzwerkregel][6]
   
1. Erstellen Sie eine Routingtabelle mit den Verwaltungsadressen aus [Verwaltungsadressen der App Service-Umgebung]( ./management-addresses.md) mit dem nächsten Hop zum Internet. Die Routingtabelleneinträge werden benötigt, um asymmetrische Routingprobleme zu vermeiden. Fügen Sie mit dem nächsten Hop zum Internet den IP-Adressabhängigkeiten (s. weiter unten „IP-Adressabhängigkeiten“) Routen hinzu. Fügen Sie Ihrer Routingtabelle eine Route für ein virtuelles Gerät für 0.0.0.0/0 hinzu, und legen Sie dabei Ihre private Azure Firewall-IP-Adresse als nächsten Hop fest. 

   ![Erstellen einer Routingtabelle][4]
   
1. Weisen Sie die erstellte Routingtabelle dem Subnetz Ihrer App Service-Umgebung zu.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Bereitstellen Ihrer ASE hinter einer Firewall

Die Schritte zum Bereitstellen Ihrer ASE hinter einer Firewall sind identisch mit der Konfiguration der bestehenden ASE mit einer Azure Firewall-Instanz. Der einzige Unterschied besteht darin, dass Sie Ihr ASE-Subnetz erstellen und anschließend die obigen Schritte ausführen müssen. Wenn Sie Ihre ASE in einem bereits vorhandenen Subnetz erstellen möchten, müssen Sie wie im Dokument [Erstellen einer ASE mit einer Azure Resource Manager-Vorlage](./create-from-template.md) beschrieben eine Resource Manager-Vorlage verwenden.

## <a name="application-traffic"></a>Anwendungsdatenverkehr 

Mithilfe der oben genannten Schritte kann Ihre App Service-Umgebung problemlos ausgeführt werden. Sie müssen die Umgebung noch so konfigurieren, dass sie Ihren Anwendungsanforderungen entspricht. Es gibt zwei Probleme bei Anwendungen in einer App Service-Umgebung, die mit Azure Firewall konfiguriert wurde.  

- Anwendungsabhängigkeiten müssen der Azure Firewall-Instanz oder der Routingtabelle hinzugefügt werden. 
- Für den Anwendungsdatenverkehr müssen Routen erstellt werden, um Probleme durch asymmetrisches Routing zu vermeiden.

Wenn Ihre Anwendungen Abhängigkeiten aufweisen, müssen diese Ihrer Azure Firewall-Instanz hinzugefügt werden. Erstellen Sie Anwendungsregeln, um HTTP/HTTPS-Datenverkehr und Netzwerkregeln zuzulassen. 

Wenn Sie den Adressbereich kennen, aus dem der von Ihrer Anwendung angeforderte Datenverkehr kommt, können Sie ihn der Routingtabelle hinzufügen, die dem Subnetz Ihrer App Service-Umgebung zugeordnet ist. Wenn der Adressbereich groß oder nicht angegeben ist, können Sie ein Netzwerkgerät wie Application Gateway verwenden, um eine Adresse Ihrer Routingtabelle hinzufügen zu können. Weitere Informationen zum Konfigurieren einer Application Gateway-Instanz mit dem internen Lastenausgleichsmodul der App Service-Umgebung finden Sie unter [Integrieren eines internen Lastenausgleichs einer App Service-Umgebung in eine Application Gateway-Instanz](./integrate-with-application-gateway.md).

Diese Verwendung von Application Gateway ist nur ein Beispiel dafür, wie Sie Ihr System konfigurieren können. Bei der obigen Konfiguration müssten Sie der Routingtabelle für das ASE-Subnetz eine Route hinzufügen, damit der an die Application Gateway-Instanz gesendete Antwortdatenverkehr direkt an diese weitergeleitet wird. 

## <a name="logging"></a>Protokollierung 

Azure Firewall kann Protokolle an Azure Storage, Event Hub oder Azure Monitor-Protokolle senden. Sie können ein beliebiges unterstütztes Ziel in Ihre App integrieren, indem Sie im Azure Firewall-Portal zu „Diagnoseprotokolle“ navigieren und die Protokolle für das gewünschte Ziel aktivieren. Wenn Sie sich für die Integration in Azure Monitor-Protokolle entscheiden, können Sie Protokolle für sämtlichen Datenverkehr einsehen, der an Azure Firewall gesendet wird. Öffnen Sie zum Anzeigen des abgelehnten Datenverkehrs das Portal mit dem Log Analytics-Arbeitsbereich, wählen Sie „Protokolle“ aus, und geben Sie eine Abfrage wie die folgende ein: 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Die Integration Ihrer Azure Firewall-Instanz in Azure Monitor-Protokolle ist nützlich, wenn Sie eine Anwendung erstmals einrichten und nicht alle Anwendungsabhängigkeiten kennen. Weitere Informationen zu Azure Monitor-Protokollen finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).
 
## <a name="dependencies"></a>Abhängigkeiten

Die folgenden Informationen sind nur erforderlich, wenn Sie ein anderes Firewallgerät als Azure Firewall konfigurieren möchten. 

- Dienste, die Dienstendpunkte unterstützen, sollten mit Dienstendpunkten konfiguriert werden.
- IP-Adressabhängigkeiten gelten für Nicht-HTTP/S-Datenverkehr (TCP- und UDP-Datenverkehr).
- FQDN-HTTP/HTTPS-Endpunkte können in Ihrem Firewallgerät bereitgestellt werden.
- Platzhalter-HTTP/HTTPS-Endpunkte sind Abhängigkeiten, die von Ihrer App Service-Umgebung abhängig sein können, basierend auf einer Reihe von Qualifizierern. 
- Linux-Abhängigkeiten sind nur relevant, wenn Sie in Ihrer App Service-Umgebung Linux-Apps bereitstellen. Wenn Sie in Ihrer App Service-Umgebung keine Linux-Apps bereitstellen, müssen diese Adressen Ihrer Firewall nicht hinzugefügt werden. 

#### <a name="service-endpoint-capable-dependencies"></a>Dienstendpunktfähige Abhängigkeiten 

| Endpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-Adressabhängigkeiten

| Endpunkt | Details |
|----------| ----- |
| \*:123 | NTP-Uhrzeitüberprüfung. Datenverkehr wird an mehreren Endpunkten am Port 123 überprüft. |
| \*:12000 | Dieser Port wird für einige Systemüberwachungsfunktionen verwendet. Wenn er blockiert wird, ist die Selektierung mancher Probleme schwieriger, die ASE wird jedoch weiterhin ausgeführt. |
| 40.77.24.27:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 40.77.24.27:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.90.249.229:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.90.249.229:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 104.45.230.69:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 104.45.230.69:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.82.184.151:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.82.184.151:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |

Mit einer Azure Firewall-Instanz erhalten Sie automatisch alle der unten aufgeführten Abhängigkeiten (mit den FQDN-Tags konfiguriert). 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN-HTTP/HTTPS-Abhängigkeiten 

| Endpunkt |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Platzhalter-HTTP/HTTPS-Abhängigkeiten 

| Endpunkt |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux-Abhängigkeiten 

| Endpunkt |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*.cdn.mscr.io:443 |
| \*.data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*.data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov-Abhängigkeiten

Befolgen Sie für App Service-Umgebungen in Regionen des Typs US Gov die Anweisungen im Abschnitt [Konfigurieren von Azure Firewall mit ihrer App Service-Umgebung](#configuring-azure-firewall-with-your-ase) in diesem Dokument, um Azure Firewall mit ihrer App Service-Umgebung zu konfigurieren.

Wenn Sie ein anderes Gerät als Azure Firewall in Regionen des Typs US Gov verwenden möchten 

* Dienste, die Dienstendpunkte unterstützen, sollten mit Dienstendpunkten konfiguriert werden.
* FQDN-HTTP/HTTPS-Endpunkte können in Ihrem Firewallgerät bereitgestellt werden.
* Platzhalter-HTTP/HTTPS-Endpunkte sind Abhängigkeiten, die von Ihrer App Service-Umgebung abhängig sein können, basierend auf einer Reihe von Qualifizierern.

Linux ist in Regionen des Typs US Gov nicht verfügbar und wird daher nicht als optionale Konfiguration aufgeführt.

#### <a name="service-endpoint-capable-dependencies"></a>Dienstendpunktfähige Abhängigkeiten ####

| Endpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-Adressabhängigkeiten

| Endpunkt | Details |
|----------| ----- |
| \*:123 | NTP-Uhrzeitüberprüfung. Datenverkehr wird an mehreren Endpunkten am Port 123 überprüft. |
| \*:12000 | Dieser Port wird für einige Systemüberwachungsfunktionen verwendet. Wenn er blockiert wird, ist die Selektierung mancher Probleme schwieriger, die ASE wird jedoch weiterhin ausgeführt. |
| 40.77.24.27:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 40.77.24.27:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.90.249.229:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.90.249.229:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 104.45.230.69:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 104.45.230.69:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.82.184.151:80 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |
| 13.82.184.151:443 | Erforderlich für die Überwachung von ASE-Problemen und Warnungen zu diesen |

#### <a name="dependencies"></a>Abhängigkeiten ####

| Endpunkt |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
