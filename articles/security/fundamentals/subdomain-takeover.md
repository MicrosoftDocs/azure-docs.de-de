---
title: Verhindern von Unterdomänenübernahmen mit Azure DNS-Aliaseinträgen und der Überprüfung benutzerdefinierter Domänen von Azure App Service
description: Erfahren Sie, wie Sie die häufige Bedrohung mit hohem Schweregrad durch Unterdomänenübernahmen verhindern
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: c3a821156074727d02ab36cf88f3e998756b8cc4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389449"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen

In diesem Artikel werden die allgemeinen Sicherheitsrisiken einer Unterdomänenübernahme und die Schritte beschrieben, die Sie ergreifen können, um dieses Problem abzuschwächen.


## <a name="what-is-a-subdomain-takeover"></a>Was ist eine Unterdomänenübernahme?

Unterdomänenübernahmen sind häufige Bedrohungen mit hohem Schweregrad für Unternehmen, die regelmäßig viele Ressourcen erstellen und löschen. Eine Unterdomänenübernahme kann auftreten, wenn Sie über einen [DNS-Eintrag](../../dns/dns-zones-records.md#dns-records) verfügen, der auf eine Azure-Ressource verweist, deren Bereitstellung aufgehoben wurde. Solche DNS-Einträge werden auch als „verwaiste DNS“-Einträge bezeichnet. CNAME-Einträge sind besonders anfällig für diese Bedrohung. Durch die Übernahme von Unterdomänen können böswillige Akteure Datenverkehr, der für die Domäne eines Unternehmens bestimmt ist, an eine Website für schädliche Aktivitäten umleiten.

Ein häufiges Szenario für eine Unterdomänenübernahme:

1. **ERSTELLUNG:**

    1. Sie stellen eine Azure-Ressource mit dem folgenden vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) bereit: `app-contogreat-dev-001.azurewebsites.net`.

    1. Sie weisen einen CNAME-Eintrag in Ihrer DNS-Zone mit der Unterdomäne `greatapp.contoso.com` zu, durch den Datenverkehr an Ihre Azure-Ressource weitergeleitet wird.

1. **AUFHEBUNG DER BEREITSTELLUNG:**

    1. Wenn die Azure-Ressource nicht mehr benötigt wird, wird sie gelöscht oder ihre Bereitstellung aufgehoben. 
    
        An diesem Punkt *sollte* der CNAME-Eintrag `greatapp.contoso.com` aus der DNS-Zone entfernt werden. Wird der CNAME-Eintrag nicht entfernt, wird er als aktive Domäne angekündigt, es wird jedoch kein Datenverkehr an eine aktive Azure-Ressource weitergeleitet. Dies ist die Definition eines verwaisten DNS-Eintrags.

    1. Die verwaiste Unterdomäne `greatapp.contoso.com` ist jetzt anfällig und kann übernommen werden, indem sie einer Ressource eines anderen Azure-Abonnements zugewiesen wird.

1. **ÜBERNAHME:**

    1. Ein Bedrohungsakteur findet die verwaiste Unterdomäne mithilfe allgemein verfügbarer Methoden und Tools.  

    1. Der Bedrohungsakteur stellt eine Azure-Ressource bereit, die den gleichen vollqualifizierten Domänennamen besitzt wie die Ressource, die sich zuvor unter Ihrer Kontrolle befand. In diesem Beispiel ist dies `app-contogreat-dev-001.azurewebsites.net`.

    1. An die Unterdomäne `greatapp.contoso.com` gesendeter Datenverkehr wird nun an die Ressource des bösartigen Akteurs weitergeleitet, wo er die Kontrolle über die Inhalte hat.



![Unterdomänenübernahme einer Website, deren Bereitstellung aufgehoben wurde](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Risiken von Unterdomänenübernahmen

Wenn ein DNS-Eintrag auf eine Ressource verweist, die nicht verfügbar ist, hätte der Eintrag selbst aus der DNS-Zone entfernt worden sollen. Wenn er nicht gelöscht wurde, handelt es sich um einen „verwaisten DNS“-Eintrag, was eine Unterdomänenübernahme ermöglicht.

Verwaiste DNS-Einträge ermöglichen es Bedrohungsakteuren, den zugeordneten DNS-Namen zu kontrollieren, um böswillige Websites oder Dienste zu hosten. Böswillige Seiten und Dienste in der Unterdomäne einer Organisation können Folgendes verursachen:

- **Verlust der Kontrolle über den Inhalt der Unterdomäne** – negative Presse dazu, dass Ihre Organisation ihre Inhalte nicht schützen kann, Schädigung der Marke und Vertrauensverlust

- **Cookie-Harvesting von ahnungslosen Besuchern** – es ist üblich, dass Web-Apps Sitzungscookies für Unterdomänen (*.contoso.com) verfügbar machen, sodass jede Unterdomäne darauf zugreifen kann. Bedrohungsakteure können mithilfe einer Unterdomänenübernahme eine authentisch wirkende Seite erstellen, die von ahnungslosen Besuchern aufgerufen wird, und anschließend deren Cookies sammeln (einschließlich sicherer Cookies). Ein gängiges Missverständnis besteht darin, dass SSL-Zertifikate Ihre Website und die Cookies Ihrer Benutzer vor einer Übernahme schützen. Bedrohungsakteure können die gehackte Unterdomäne verwenden, um ein gültiges SSL-Zertifikat zu beantragen und abzurufen. Gültige SSL-Zertifikate gewähren ihnen Zugriff auf sichere Cookies und führen außerdem dazu, dass die böswillige Website als legitim wahrgenommen wird.

- **Phishingkampagnen:** Authentisch wirkende Unterdomänen können in Phishingkampagnen verwendet werden. Das gilt für böswillige Websites sowie für MX-Einträge, die es dem Bedrohungsakteur ermöglichen, E-Mails zu empfangen, die an eine legitime Unterdomäne einer bekanntermaßen sicheren Marke adressiert sind.

- **Weitere Risiken:** Böswillige Websites können auch für andere klassische Angriffe wie XSS, CSRF, CORS-Umgehungen und Ähnliches verwendet werden.



## <a name="identify-dangling-dns-entries"></a>Erkennen verwaister DNS-Einträge

Möglicherweise verwaiste DNS-Einträge in Ihrer Organisation können mithilfe des auf GitHub gehosteten Microsoft-PowerShell-Tools [Get-DanglingDnsRecords](https://aka.ms/DanglingDNSDomains) erkannt werden.

Mit diesem Tool können Azure-Kunden alle Domänen mit einem CNAME-Eintrag auflisten, der einer vorhandenen Azure-Ressource zugeordnet ist, die in ihren Abonnements oder Mandanten erstellt wurde.

Wenn sich Ihre CNAME-Einträge in anderen DNS-Diensten befinden und auf Azure-Ressourcen verweisen, übergeben Sie die CNAME-Einträge in einer Eingabedatei an das Tool.

Von dem Tool werden die in der folgenden Tabelle aufgeführten Azure-Ressourcen unterstützt. Das Tool extrahiert alle CNAME-Einträge des Mandanten oder verwendet sie als Eingaben.


| Dienst                   | Typ                                        | FQDNproperty                               | Beispiel                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | properties.cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | microsoft.storage/storageaccounts           | properties.primaryEndpoints.blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | microsoft.cdn/profiles/endpoints            | properties.hostName                        | `abc.azureedge.net`             |
| Öffentliche IP-Adressen       | microsoft.network/publicipaddresses         | properties.dnsSettings.fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | microsoft.network/trafficmanagerprofiles    | properties.dnsConfig.fqdn                  | `abc.trafficmanager.net`        |
| Azure Container Instances  | microsoft.containerinstance/containergroups | properties.ipAddress.fqdn                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | microsoft.apimanagement/service             | properties.hostnameConfigurations.hostName | `abc.azure-api.net`             |
| Azure App Service         | microsoft.web/sites                         | properties.defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service – Slots | microsoft.web/sites/slots                   | properties.defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Abfrage als Benutzer aus, der über Folgendes verfügt:

- mindestens Zugriff auf Leserebene für die Azure-Abonnements
- Lesezugriff auf Azure Resource Graph

Wenn Sie ein globaler Administrator des Mandanten Ihrer Organisation sind, erhöhen Sie die Rechte Ihres Kontos, um Zugriff auf das gesamte Abonnement Ihrer Organisation zu erhalten. Gehen Sie dazu wie unter [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](../../role-based-access-control/elevate-access-global-admin.md) beschrieben vor.


> [!TIP]
> Bei Azure Resource Graph gelten Drosselungs- und Auslagerungsgrenzwerte, die in umfangreichen Azure-Umgebungen berücksichtigt werden müssen. 
> 
> Weitere Informationen finden Sie unter [Arbeiten mit großen Datasets von Azure-Ressourcen](../../governance/resource-graph/concepts/work-with-data.md).
> 
> Das Tool verwendet eine Batchverarbeitung für Abonnements, um diese Einschränkungen zu vermeiden.

### <a name="run-the-script"></a>Ausführen des Skripts

Unter https://aka.ms/Get-DanglingDnsRecords können Sie das PowerShell-Skript **Get-DanglingDnsRecords.ps1** von GitHub herunterladen, und Sie erhalten weitere Informationen dazu.

## <a name="remediate-dangling-dns-entries"></a>Behandeln verwaister DNS-Einträge 

Überprüfen Sie Ihre DNS-Zonen, und ermitteln Sie verwaiste oder übernommene CNAME-Einträge. Sollten verwaiste oder übernommene Unterdomänen gefunden werden, entfernen Sie die gefährdeten Unterdomänen, und verringern Sie die damit verbundenen Risiken wie folgt:

1. Entfernen Sie in Ihrer DNS-Zone alle CNAME-Einträge, die auf FQDNs nicht mehr bereitgestellter Ressourcen verweisen.

1. Stellen Sie zusätzliche Ressourcen mit den FQDNs bereit, die in den CNAME-Einträgen der verwaisten Unterdomänen angegeben sind, damit Datenverkehr an Ressourcen weitergeleitet werden kann, die Ihrer Kontrolle unterliegen.

1. Überprüfen Sie Ihren Anwendungscode auf Verweise auf bestimmte Unterdomänen, und aktualisieren Sie alle fehlerhaften oder veralteten Unterdomänenverweise.

1. Untersuchen Sie, ob eine Kompromittierung vorliegt, und gehen Sie gemäß den für Ihre Organisation vorgesehenen Verfahren für die Reaktion auf Vorfälle vor, um geeignete Maßnahmen zu ergreifen. Tipps und bewährte Methoden für die Untersuchung dieses Problems finden Sie weiter unten.

    Wenn Ihre Anwendungslogik so konzipiert ist, dass Geheimnisse wie OAuth-Anmeldeinformationen oder datenschutzrelevante Informationen an die verwaisten Unterdomänen gesendet wurden, kann es sein, dass Dritte Zugriff auf diese Daten hatten.

1. Ermitteln Sie, warum der CNAME-Eintrag beim Aufheben der Ressourcenbereitstellung nicht aus Ihrer DNS-Zone entfernt wurde, und stellen Sie sicher, dass DNS-Einträge künftig entsprechend aktualisiert werden, wenn die Bereitstellung von Azure-Ressourcen aufgehoben wird.


## <a name="prevent-dangling-dns-entries"></a>Vermeiden verwaister DNS-Einträge

Ein wichtiger Teil des Sicherheitsprogramms besteht darin, sicherzustellen, dass Ihre Organisation Prozesse implementiert hat, die verwaiste DNS-Einträge und daraus resultierende Unterdomänenübernahmen verhindern.

Einige Azure-Dienste bieten Features, die Sie bei der Erstellung vorbeugender Maßnahmen unterstützen. Diese werden weiter unten ausführlicher beschrieben. Andere Methoden zur Vermeidung dieses Problems müssen über Best Practices oder Standards Ihrer Organisation implementiert werden.

### <a name="enable-azure-defender-for-app-service"></a>Aktivieren von Azure Defender für App Service

Azure Defender ist die Cloud Workload Protection Platform (CWPP) von Azure Security Center und bietet eine Reihe von Plänen zum Schützen Ihrer Azure-Ressourcen sowie Hybrid- und Multicloudressourcen und -workloads.

Der Plan **Azure Defender für App Service** umfasst das Erkennen verwaister DNS-Einträge. Wenn dieser Plan aktiviert ist, erhalten Sie Sicherheitswarnungen, wenn Sie eine App Service-Website außer Betrieb nehmen, aber nicht ihre benutzerdefinierte Domäne aus Ihrer DNS-Registrierungsstelle entfernen.

Der Azure Defender-Schutz vor verwaisten DNS-Einträgen ist unabhängig davon verfügbar, ob Ihre Domänen mit Azure DNS oder mit einer externen Domänenregistrierungsstelle verwaltet werden. Der Schutz gilt sowohl für App Service unter Windows als auch für App Service unter Linux.

Weitere Informationen zu diesem und anderen Vorteilen des Azure Defender-Plans finden Sie unter [Einführung in Azure Defender für App Service](../../security-center/defender-for-app-service-introduction.md).

### <a name="use-azure-dns-alias-records"></a>Verwenden von Azure DNS-Aliaseinträgen

Azure DNS-[Aliaseinträge](../../dns/dns-alias.md#scenarios) verhindern verwaiste Verweise durch eine Kopplung des Lebenszyklus eines DNS-Eintrags mit einer Azure-Ressource. Stellen Sie sich beispielsweise einen DNS-Eintrag vor, der als Aliaseintrag qualifiziert wird, um auf eine öffentliche IP-Adresse oder auf ein Traffic Manager-Profil zu verweisen. Wenn Sie diese zugrunde liegenden Ressourcen löschen, wird der DNS-Aliaseintrag ein leerer Eintragssatz. Er verweist nicht mehr auf die gelöschte Ressource. Beachten Sie jedoch, dass es Grenzen in Bezug darauf gibt, was Sie mit Aliaseinträgen schützen können. Dies ist derzeit auf Folgendes beschränkt:

- Azure Front Door
- Traffic Manager-Profile
- Azure Content Delivery Network-Endpunkte (CDN)
- Öffentliche IP-Adressen

Trotz der aktuell begrenzten Dienstangebote empfiehlt es sich, nach Möglichkeit Aliaseinträge zu verwenden, um sich vor Unterdomänenübernahmen zu schützen.

Unter [Funktionen](../../dns/dns-alias.md#capabilities) erhalten Sie weitere Informationen zu den Funktionen von Aliaseinträgen von Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Verwenden der Überprüfung benutzerdefinierter Domänen von Azure App Service

Wenn Sie DNS-Einträge für Azure App Service erstellen, erstellen Sie auch einen asuid.{subdomain}-TXT-Eintrag mit der Verifizierungs-ID für die Domäne. Wenn ein solcher TXT-Datensatz vorhanden ist, kann kein anderes Azure-Abonnement die benutzerdefinierte Domäne verifizieren und übernehmen. 

Diese Datensätze verhindern nicht, dass jemand den Azure App Service mit dem gleichen Namen wie in Ihrem CNAME-Eintrag erstellt. Wenn Bedrohungsakteure nicht nachweisen können, dass sie Besitzer des Domänennamens sind, können sie keinen Datenverkehr empfangen oder den Inhalt kontrollieren.

Unter [Tutorial: Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md) erhalten Sie weitere Informationen.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Erstellen und Automatisieren von Prozessen zum Abschwächen der Bedrohung

Entwickler und Betriebsteams müssen häufig Bereinigungsprozesse durchführen, damit keine Bedrohungen durch verwaiste DNS entstehen. Mithilfe der folgenden Vorgehensweisen können Sie sicherstellen, dass Ihre Organisation diese Bedrohung vermeidet. 

- **Erstellen von Verfahren zur Prävention:**

    - Informieren Sie Ihre Anwendungsentwickler darüber, dass sie beim Löschen von Ressourcen Adressen immer umleiten.

    - Nehmen Sie „DNS-Eintrag entfernen“ auf die Liste der Punkte auf, die bei der Außerbetriebsetzung eines Diensts beachtet werden müssen.

    - Legen Sie [Löschsperren](../../azure-resource-manager/management/lock-resources.md) für alle Ressourcen mit einem benutzerdefinierten DNS-Eintrag fest. Eine Löschsperre dient als Indikator dafür, dass die Zuordnung entfernt werden muss, bevor die Bereitstellung der Ressource aufgehoben wird. Maßnahmen wie diese funktionieren nur in Kombination mit internen Schulungsprogrammen.

- **Erstellen von Verfahren zur Ermittlung:**

    - Überprüfen Sie Ihre DNS-Einträge regelmäßig, um sicherzustellen, dass alle Ihre Unterdomänen Azure-Ressourcen zugeordnet sind, auf die Folgendes zutrifft:

        - Vorhanden: Fragen Sie die DNS-Zonen in Bezug auf Ressourcen ab, die auf Azure-Unterdomänen wie *.azurewebsites.net oder *.cloudapp.azure.com verweisen. Weitere Informationen dazu finden Sie unter [Referenzliste von Azure-Domänen (nicht vollständig)](azure-domains.md).
        - In Ihrem Besitz – stellen Sie sicher, dass Sie alle Ressourcen besitzen, auf die Ihre DNS-Unterdomänen verweisen.

    - Pflegen Sie einen Dienstkatalog mit Ihren Azure-FQDN-Endpunkten (Fully Qualified Domain Name, vollqualifizierter Domänenname) und den Anwendungsbesitzern. Führen Sie zum Erstellen des Dienstkatalogs das folgende Azure Resource Graph-Abfrageskript aus. Das Skript projiziert die FQDN-Endpunktinformationen der Ressourcen, auf die Sie Zugriff haben, und gibt sie in einer CSV-Datei aus. Wenn Sie Zugriff auf alle Abonnements Ihres Mandanten haben, berücksichtigt das Skript all diese Abonnements, wie im folgenden Beispielskript gezeigt. Um die Ergebnisse auf eine bestimmte Gruppe von Abonnements zu beschränken, bearbeiten Sie das Skript wie gezeigt.


- **Erstellen von Verfahren zur Korrektur:**
    - Wenn Sie verwaiste DNS-Einträge finden, muss das Team überprüfen, ob eine Gefährdung aufgetreten ist.
    - Untersuchen Sie, weshalb die Adresse nicht umgeleitet wurde, als die Ressource außer Betrieb gesetzt wurde.
    - Löschen Sie den DNS-Eintrag, wenn er nicht mehr verwendet wird, oder verweisen Sie ihn auf die korrekte Azure-Ressource (FQDN), die sich im Besitz Ihrer Organisation befindet.
 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu verwandten Diensten und Azure-Features, die Sie verwenden können, um sich vor Unterdomänenübernahmen zu schützen, finden Sie auf den folgenden Seiten.

- [Aktivieren von Azure Defender für App Service](../../security-center/defender-for-app-service-introduction.md), um Warnungen zu erhalten, wenn verwaiste DNS-Einträge erkannt werden

- [Vermeiden verwaister DNS-Einträge](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Abrufen der Verifizierungs-ID für eine Domäne](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mit Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)
