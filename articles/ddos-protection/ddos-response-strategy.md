---
title: Komponenten einer DDoS-Reaktionsstrategie
description: Erfahren Sie, wie Sie Azure DDoS Protection Standard verwenden, um auf DDoS-Angriffe zu reagieren.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 0a80f03ee82e8d1216353482dc867402adcf7d09
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992232"
---
# <a name="components-of-a-ddos-response-strategy"></a>Komponenten einer DDoS-Reaktionsstrategie

DDoS-Angriffe, die Azure-Ressourcen als Ziel haben, erfordern aus Sicht des Benutzers normalerweise nur minimales Eingreifen. Dennoch können Sie durch Integrieren der DDoS-Entschärfung im Rahmen der Reaktionsstrategie auf Incidents minimale Auswirkungen auf die Geschäftskontinuität sicherstellen.

## <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft bietet ein umfassendes Threat Intelligence-Netzwerk (Informationen zu Bedrohungen). Dieses Netzwerk verwendet die gesammelten Kenntnisse einer großen Sicherheitscommunity, die Onlinedienste und Microsoft-Partner sowie Beziehungen in der Internet-Sicherheitscommunity unterstützt. 

Als wichtiger Anbieter von Infrastruktur empfängt Microsoft frühzeitig Warnungen zu Bedrohungen. Microsoft sammelt Threat Intelligence (Informationen zu Bedrohungen) aus Onlinediensten und dem weltweiten Kundenstamm. Die gesamte Threat Intelligence von Microsoft wird wieder in die Azure DDoS Protection-Produkte integriert.

Außerdem führt die Microsoft Digital Crimes Unit (DCU) offensive Strategien gegen Botnets durch. Botnets sind eine häufige Quelle für Befehle und Steuerung von DDoS-Angriffen.

## <a name="risk-evaluation-of-your-azure-resources"></a>Risikobewertung für Ihre Azure-Ressourcen

Es ist zwingend notwendig, dass Sie durchgehend Ihr Risiko für DDoS-Angriffe einschätzen können. Stellen Sie sich in regelmäßigen Abständen die folgenden Fragen:

- Welche neuen, öffentlich verfügbaren Azure-Ressourcen benötigen Schutz?

- Gibt es Single Points of Failure im Dienst? 

- Wie können Dienste isoliert werden, um die Auswirkungen eines Angriffs einzuschränken, aber die Dienste legitimen Kunden weiterhin verfügbar zu machen?

- Gibt es virtuelle Netzwerke, in denen DDoS Protection Standard aktiviert werden sollte, aber noch nicht aktiviert ist? 

- Sind meine Dienste mehrere Regionen übergreifend aktiv / aktiv mit Failover?

Es ist von entscheidender Bedeutung, das normale Verhalten einer Anwendung zu kennen und sich auf den Fall vorzubereiten, dass sich die Anwendung während eines DDoS-Angriffs nicht erwartungsgemäß verhält. Sie sollten Monitore für Ihre unternehmenskritischen Anwendungen konfigurieren, die das Clientverhalten imitieren und Sie benachrichtigen, wenn relevante Anomalien erkannt werden. Unter [Bewährte Methoden für Überwachung und Diagnose](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) erfahren Sie, wie Sie Erkenntnisse über die Integrität Ihrer Anwendung erhalten.

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) auf mehreren Plattformen. Sie können mit Application Insights Ihre Live-Web-App überwachen. Der Dienst erkennt automatisch Leistungsanomalien. Er verfügt über Analysetools, mit denen Sie Probleme diagnostizieren und nachvollziehen können, wie Ihre App von Benutzern verwendet wird. Der Dienst unterstützt Sie bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit Ihrer App.

## <a name="customer-ddos-response-team"></a>Kunden-DDoS-Reaktionsteam

Das Erstellen eines DDoS-Reaktionsteams ist ein wichtiger Schritt, um eine effiziente und schnelle Reaktion auf einen Angriff zu gewährleisten. Geben Sie verschiedene Kontakte in Ihrer Organisation an, die Planung und Ausführung leiten. Dieses DDoS-Reaktionsteam sollte sehr gut mit dem Azure DDoS Protection Standard-Dienst vertraut sein. Stellen Sie sicher, dass Ihr Team einen Angriff identifizieren und entschärfen kann, indem es in Koordination mit internen und externen Kunden und dem Microsoft-Supportteam arbeitet. 

Es wird empfohlen, Simulationsübungen als normalen Bestandteil Ihrer Planung zur Dienstverfügbarkeit und Kontinuität durchzuführen und in diese Tests auch die Skalierung mit einzubeziehen. Weitere Informationen zum Simulieren von Tests des DDoS-Datenverkehrs Ihrer öffentlichen Azure-Endpunkte finden Sie unter [Durchführen von Simulationstests](test-through-simulations.md).

## <a name="alerts-during-an-attack"></a>Warnungen während eines Angriffs

Azure DDoS Protection Standard identifiziert und entschärft DDoS-Angriffe ohne jegliches Eingreifen des Benutzers. Um benachrichtigt zu werden, wenn eine Entschärfung für eine geschützte öffentliche IP-Adresse aktiv ist, können Sie in der Metrik **einem DDoS Angriff ausgesetzt oder nicht**  eine Warnung konfigurieren. Weitere Informationen finden Sie unter [Manage Azure DDoS Protection Standard using the Azure portal (Verwalten von Azure DDoS Protection Standard über das Azure-Portal)](telemetry-monitoring-alerting.md). Sie können außerdem Warnungen für andere DDoS-Metriken erstellen, um das Ausmaß des Angriffs, den gelöschten Datenverkehr usw. zu verstehen.

### <a name="when-to-contact-microsoft-support"></a>Wann Sie den Microsoft-Support kontaktieren sollten

Kunden mit Azure DDoS Protection Standard können sich an das DRR-Team (DDoS Rapid Response) wenden, das sie bei der Untersuchung während eines Angriffs sowie bei der Analyse nach Angriffen unterstützt. Weitere Informationen finden Sie unter [DDoS Rapid Response](ddos-rapid-response.md). Dort erfahren Sie auch, wann Sie das DRR-Team einbeziehen sollten.

## <a name="post-attack-steps"></a>Schritte nach dem Angriff

Nach einem Angriff sollten Sie immer Bilanz ziehen und ggf. die DDoS-Reaktionsstrategie anpassen. Ziehen Sie Folgendes in Betracht:

- Traten aufgrund mangelnder skalierbarer Architektur Unterbrechungen des Dienstes bzw. Unterbrechungen für den Benutzer auf?

- Welche Anwendungen oder Dienste waren am meisten betroffen?

- Wie effektiv war die DDoS-Reaktionsstrategie, und wie kann sie verbessert werden?

Wenn Sie vermuten, dass Sie einem DDoS-Angriff ausgesetzt sind, eskalieren Sie dies über die normalen Supportkanäle von Azure.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen DDoS-Schutzplan erstellen](manage-ddos-protection.md).