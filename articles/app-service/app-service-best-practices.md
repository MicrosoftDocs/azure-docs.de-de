---
title: Bewährte Methoden – Azure App Service
description: Informationen zu empfohlenen Methoden und zur Problembehandlung für Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fc3749a9ebfbf0319a57b471b6fce9f62042ba27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60852681"
---
# <a name="best-practices-for-azure-app-service"></a>Empfohlene Methoden für Azure App Service
In diesem Artikel werden die empfohlenen Methoden für die Verwendung von [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)zusammengefasst. 

## <a name="colocation"></a>Zusammenstellen
Befinden sich Azure-Ressourcen, aus denen sich eine Lösung zusammensetzt (beispielsweise eine Web-App und eine Datenbank), in unterschiedlichen Regionen, kann sich dies folgendermaßen auswirken:

* erhöhte Latenz bei der Kommunikation zwischen den Ressourcen
* Kosten für ausgehende, regionsübergreifende Datenübertragung, Details finden Sie in der [Azure-Preisübersicht](https://azure.microsoft.com/pricing/details/data-transfers)

Das Zusammenstellen in derselben Region eignet sich am besten für Azure-Ressourcen, die zusammen eine Lösung bilden, wie z.B. eine Web-App und eine Datenbank oder ein Speicherkonto für die Inhalte oder Daten. Stellen Sie beim Erstellen von Ressourcen sicher, dass sich diese in derselben Azure-Region befinden, es sei denn, es sprechen geschäftliche oder entwurfstechnische Gründe dagegen. Mithilfe des [App Service-Klonfeatures](app-service-web-app-cloning.md), das derzeit für Apps im App Service-Plan Premium verfügbar ist, können Sie eine App Service-App in dieselbe Region wie Ihre Datenbank verschieben.   

## <a name="memoryresources"></a>Wenn Apps mehr Speicherplatz als erwartet belegen
Wenn Sie durch Überwachung oder Dienstempfehlungen feststellen, dass eine App mehr Speicherplatz belegt als erwartet, sollten Sie das [Selbstreparaturfeature von App Service](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites) in Betracht ziehen. Eine der Optionen der Selbstreparaturfunktion besteht darin, benutzerdefinierte Aktionen basierend auf einem Speicherschwellenwert zu ergreifen. Die Aktionen reichen von E-Mail-Benachrichtigungen über eine Untersuchung mittels Speicherabbild bis hin zur Behebung vor Ort durch Recycling des Arbeitsprozesses. Die automatische Reparatur kann, wie in diesem Blogbeitrag für die [App Service Support-Websiteerweiterung](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)beschrieben, über „web.config“ und eine benutzerfreundliche Benutzeroberfläche konfiguriert werden.   

## <a name="CPUresources"></a>Wenn Apps mehr CPU-Leistung als erwartet beanspruchen
Wenn Sie feststellen, dass eine App mehr CPU-Leistung als erwartet beansprucht, oder wenn laut Überwachung oder Dienstempfehlungen wiederholt Rechenlastspitzen zu verzeichnen sind, sollten Sie in Betracht ziehen, den App Service-Plan zentral oder horizontal hochzuskalieren. Bei einer zustandsbehafteten Anwendung ist eine zentrale Hochskalierung die einzige Option. Wenn Ihre Anwendung jedoch zustandslos ist, erreichen Sie mit einer horizontalen Hochskalierung mehr Flexibilität und ein höheres Skalierungspotenzial. 

Weitere Informationen zu „zustandsbehafteten“ und „zustandslosen“ Anwendungen bietet das Video über das [Planen einer skalierbaren End-to-End-Anwendung mit mehreren Ebenen in Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Weitere Informationen zur Skalierung von App Service und Optionen zur automatischen Skalierung finden Sie unter [Skalieren einer Web-App in Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Wenn Socketressourcen erschöpft sind
Eine häufige Ursache für das Erschöpfen ausgehender TCP-Verbindungen ist die Verwendung von Clientbibliotheken, die nicht zur Wiederverwendung von TCP-Verbindungen implementiert wurden, oder im Fall eines übergeordneten Protokolls wie HTTP die fehlende Nutzung von Keep-Alive. Informieren Sie sich in den Dokumentationen der einzelnen Bibliotheken, auf die von den Apps in Ihrem App Service-Plan verwiesen wird, ob sie konfiguriert sind oder in Ihrem Code darauf zugegriffen wird, um so eine effiziente Wiederverwendung ausgehender Verbindungen zu gewährleisten. Befolgen Sie auch den Leitfaden zur Bibliotheksdokumentation für eine ordnungsgemäße Erstellung und Freigabe oder Bereinigung, um Verbindungsverluste zu vermeiden. Während diese Clientbibliotheksuntersuchungen ausgeführt werden, können die Auswirkungen durch ein horizontales Hochskalieren auf mehrere Instanzen verringert werden.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js und ausgehende HTTP-Anforderungen
Bei Verwendung von Node.js und zahlreichen ausgehenden HTTP-Anforderungen ist eine Behandlung von HTTP-Keep-Alive wichtig. Sie können zur Vereinfachung in Ihrem Code das [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)-`npm`-Paket verwenden.

Die `http`-Antwort sollte immer behandelt werden, auch wenn im Handler ggf. nichts unternommen wird. Wird die Antwort nicht ordnungsgemäß verarbeitet, bleibt die Anwendung letztendlich hängen, da keine Sockets mehr verfügbar sind.

Beispiel für die Verwendung des `http`- oder `https`-Pakets:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Wenn Sie App Service unter Linux auf einem Computer mit mehreren Kernen ausführen, hat es sich bewährt, für die Ausführung Ihrer Anwendung mit PM2 mehrere Node.js-Prozesse zu starten. Dazu können Sie einen Startbefehl für Ihren Container angeben.

So starten Sie beispielsweise vier Instanzen:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Wenn die Sicherung Ihrer App fehlerhaft zu werden beginnt
Die zwei häufigsten Gründe, warum eine App-Sicherung misslingt, sind ungültige Speichereinstellungen und eine ungültige Datenbankkonfiguration. Diese Fehler treten in der Regel auf, wenn Änderungen an Speicher- oder Datenbankressourcen oder am Zugriff auf diese Ressourcen erfolgt sind (z.B. eine Aktualisierung der Anmeldeinformationen für die in den Sicherungseinstellungen ausgewählte Datenbank). Sicherungen erfolgen meist gemäß einem Zeitplan und erfordern Zugriff auf Speicher (für die Ausgabe der gesicherten Dateien) und Datenbanken (zum Kopieren und Lesen von Inhalten, die in die Sicherung einbezogen werden sollen). Das Ergebnis des Fehlens eines Zugriff auf diese Ressourcen wäre ein durchgängiger Ausfall von Sicherungen. 

Wenn Sicherungsfehler auftreten, überprüfen Sie die letzten Ergebnisse, um herauszufinden, welche Art von Fehler auftritt. Überprüfen und ändern Sie bei Speicherzugriffsfehlern die in der Sicherungskonfiguration verwendeten Speichereinstellungen. Überprüfen und ändern Sie bei Fehlern beim Datenbankzugriff Ihre Verbindungszeichenfolgen in den App-Einstellungen. Fahren Sie dann mit dem Ändern Ihrer Sicherungskonfiguration fort, sodass die erforderlichen Datenbanken einbezogen werden. Weitere Informationen zu App-Sicherungen finden Sie unter [Sichern von Web-Apps in Azure App Service](manage-backup.md).

## <a name="nodejs"></a>Wenn neue Node.js-Apps in Azure App Service bereitgestellt werden
Die Azure App Service-Standardkonfiguration für Node.js-Apps soll den Bedürfnissen der am häufigsten verwendeten Apps am besten entsprechen. Wenn die Konfiguration für Ihre Node.js-App von der personalisierten Abstimmung zur Leistungsverbesserung oder Optimierung des Ressourceneinsatzes für CPU-/Speicher-/Netzwerkressourcen profitieren würden, finden Sie weitere Informationen unter [Bewährte Methoden und Problembehandlungsschritte für Node-Anwendungen bei Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Dieser Artikel beschreibt die iisnode-Einstellungen, die Sie möglicherweise für Ihre Node.js-App konfigurieren müssen, sowie die verschiedenen Szenarien oder Probleme, mit denen Ihre App möglicherweise konfrontiert wird, und zeigt, wie Sie diese Probleme beheben.

