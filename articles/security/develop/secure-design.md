---
title: Entwerfen von sicheren Anwendungen in Microsoft Azure
description: In diesem Artikel sind die bewährten Methoden beschrieben, die in der Anforderungs- und in der Entwurfsphase eines Webanwendungsprojekts beachtet werden sollten.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487676"
---
# <a name="design-secure-applications-on-azure"></a>Entwerfen von sicheren Anwendungen in Azure
In diesem Artikel werden Sicherheitsaktivitäten und -kontrollen vorgestellt, die Sie berücksichtigen sollten, wenn Sie Anwendungen für die Cloud entwerfen. Es werden Trainingsressourcen zusammen mit Sicherheitsfragen und -konzepten behandelt, die Sie in der Anforderungen- und in der Entwurfsphase von Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) berücksichtigen müssen. Das Ziel ist, Ihnen das Festlegen von Aktivitäten und Azure-Diensten zu ermöglichen, mit denen Sie eine sicherere Anwendung entwickeln können.

In diesem Artikel werden die folgenden SDL-Phasen behandelt:

- Training
- Requirements (Anforderungen)
- Entwurf

## <a name="training"></a>Training
Bevor Sie mit dem Entwickeln Ihrer Cloudanwendung beginnen, sollten Sie sich ausführlich mit den Themen Sicherheit und Datenschutz in Azure beschäftigen. Mit diesem Schritt können Sie Anzahl und Schweregrad der ausnutzbaren Sicherheitsrisiken in Ihrer Anwendung verringern. Sie werden besser darauf vorbereitet sein, angemessen auf die sich ständig ändernde Bedrohungslage zu reagieren.

Verwenden Sie die folgenden Ressourcen während der Trainingsphase, um sich mit den Azure-Diensten, die Entwicklern zur Verfügung stehen, und mit den bewährten Sicherheitsmethoden in Azure vertraut zu machen:

  - Im [Entwicklerleitfaden für Azure](https://azure.microsoft.com/campaigns/developer-guide/) wird gezeigt, wie die ersten Schritte mit Azure aussehen. Der Leitfaden veranschaulicht Ihnen, welche Dienste Sie verwenden können, um Ihre Anwendungen auszuführen, Ihre Daten zu speichern, Business Intelligence einzubinden, IoT-Anwendungen zu erstellen und Ihre Lösungen effizienter und sicherer bereitzustellen.

  - Die [Anleitung mit den ersten Schritten für Azure-Entwickler](../../guides/developer/azure-developer-guide.md) enthält wichtige Informationen für Entwickler, die damit beginnen möchten für ihre Entwicklungsanforderungen die Azure-Plattform zu nutzen.

  - In [SDKs und Tools](../../index.yml?pivot=sdkstools) sind die Tools beschrieben, die in Azure verfügbar sind.

  - In [Azure DevOps Services](/azure/devops/) werden Tools zur Entwicklungszusammenarbeit bereitgestellt. Zu den Tools gehören leistungsstarke Pipelines, kostenlose Git-Repositorys, konfigurierbare Kanban-Boards und umfangreiche automatisierte und cloudbasierte Auslastungstests.
    Das [DevOps Resource Center](/azure/devops/learn/) kombiniert unsere Ressourcen zum Erlernen von DevOps-Praktiken, Git-Versionskontrolle, Agile-Methoden, wie wir bei Microsoft mit DevOps arbeiten und wie Sie Ihren eigenen DevOps-Fortschritt bewerten können.

  - In [Top 5 der Sicherheitselemente, die vor dem Übergang in die Produktion zu beachten sind](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) erfahren Sie, wie Sie Ihre Webanwendungen in Azure schützen und Ihre Apps vor den häufigsten und gefährlichsten Angriffen auf Webanwendungen schützen können.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) ist eine Sammlung von Skripts, Tools, Erweiterungen und Automatisierungen, die den umfassenden Azure-Abonnement- und -Ressourcensicherheitsanforderungen von DevOps-Teams gerecht wird, die umfangreiche Automatisierung nutzen. Im „Secure DevOps Kit for Azure“ ist gezeigt, wie Sie Sicherheit nahtlos in Ihre nativen DevOps-Workflows integrieren können. Im Kit werden Tools wie Sicherheitsüberprüfungstests (Security Verification Tests, SVTs) bereitgestellt, die Entwickler dazu verwenden können, sicheren Code zu schreiben und die sichere Konfiguration ihrer Cloudanwendungen in der frühen Entwicklungsphase und in der Codierungsphase zu testen.

  - [Security best practices for Azure solutions](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) enthält eine Sammlung von bewährten Sicherheitsmethoden, die Sie verwenden können, wenn Sie Ihre Cloudlösungen in Azure entwerfen, bereitstellen und verwalten.

## <a name="requirements"></a>Requirements (Anforderungen)
Die Phase der Definition der Anforderungen ist ein entscheidender Schritt beim Definieren, was Ihre Anwendung ist und was mit ihr erledigt wird, wenn sie freigegeben wurde. Die Anforderungenphase ist außerdem eine Zeit, in der Sie sich Gedanken zu den Sicherheitskontrollen machen sollten, die Sie in Ihre Anwendung integrieren möchten. In dieser Phase beginnen Sie auch mit den Schritten, die Sie im gesamten SDL unternehmen, um sicherzustellen, dass Sie eine sichere Anwendung freigeben und bereitstellen.

### <a name="consider-security-and-privacy-issues"></a>Berücksichtigen von Sicherheits- und Datenschutzfragen
Diese Phase ist die beste Zeit, um grundlegende Sicherheits- und Datenschutzfragen zu berücksichtigen. Das Festlegen von angemessenen Sicherheits- und Datenschutzniveaus zu Beginn eines Projekts hilft einem Team bei folgenden Aspekten:

- Verstehen der Risiken im Zusammenhang mit Sicherheitsfragen
- Erkennen und Beheben von sicherheitsrelevanten Fehlern während der Entwicklung
- Anwenden festgelegter Sicherheits- und Datenschutzniveaus im gesamten Projekt

Wenn Sie die Anforderungen für Ihre Anwendung schreiben, sollten Sie Sicherheitskontrollen berücksichtigen, die dazu beitragen können, dass Ihre Anwendung und Daten geschützt bleiben.

### <a name="ask-security-questions"></a>Stellen von Sicherheitsfragen
Stellen Sie Sicherheitsfragen wie:

  - Enthält meine Anwendung sensible Daten?

  - Sammelt oder speichert meine Anwendung Daten, die von mir die Einhaltung von Branchenstandards und Complianceprogrammen wie dem [Federal Financial Institutions Examination Council (FFIEC)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) oder dem [Payment Card Industry Data Security Standard (PCI-DSS)](/previous-versions/azure/security/blueprints/pcidss-analytics-overview) verlangen?

  - Sammelt oder enthält meine Anwendung sensible persönliche oder Kundendaten, die entweder allein oder mit anderen Informationen verwendet werden können, um eine einzelne Person zu identifizieren, zu kontaktieren oder zu finden?

  - Sammelt oder enthält meine Anwendung Daten, die verwendet werden können, um auf die medizinischen, ausbildungsbezogenen, finanziellen oder beruflichen Informationen einer Person zuzugreifen? Dadurch, dass Sie während der Anforderungenphase die Sensibilität Ihrer Daten bestimmen, können Sie diese Daten klassifizieren und die Datenschutzmethode bestimmen, die Sie für Ihre Anwendung verwenden müssen.

  - Wo und wie werden meine Daten gespeichert? Überlegen Sie, wie Sie die von Ihrer Anwendung verwendeten Speicherdienste auf unerwartete Änderungen (z.B. langsamere Antwortzeiten) überwachen können. Haben Sie die Möglichkeit, die Protokollierung zu beeinflussen, um detailliertere Daten zu sammeln und ein Problem gründlich zu analysieren?

  - Ist meine Anwendung öffentlich (über das Internet) oder nur intern verfügbar? Wenn Ihre Anwendung öffentlich verfügbar ist, wie schützen Sie die Daten, die möglicherweise erfasst werden, davor, in falscher Weise verwendet zu werden? Wenn Ihre Anwendung nur intern verfügbar ist, sollten Sie überlegen, welche Personen in Ihrer Organisation Zugriff auf die Anwendung und wie lange sie Zugriff haben sollen.

  - Kennen Sie Ihr Identitätsmodell, bevor Sie mit dem Entwerfen Ihrer Anwendung beginnen? Wie werden Sie feststellen, ob Benutzer tatsächlich die erwarteten Benutzer sind und wozu sie berechtigt sind?

  - Führt meine Anwendung sensible oder wichtige Aufgaben aus (etwa Geldüberweisungen, Türentriegelungen oder Medikamentenlieferungen)?
    Bestimmen Sie, wie Sie überprüfen können, ob der Benutzer, der eine sensible Aufgabe ausführen möchte, dazu berechtigt ist, und wie Sie bestätigen können, ob eine Person diejenige ist, für die sie sich ausgibt. Autorisierung ist der Vorgang, bei dem einem authentifizierten Sicherheitsprinzipal die Berechtigung erteilt wird, eine bestimmte Aktion auszuführen. Authentifizierung ist der Vorgang, bei dem eine Partei zur Angabe von ordnungsgemäßen Anmeldeinformationen aufgefordert wird.

  - Führt meine Anwendung irgendwelche riskanten Softwareaktivitäten aus, etwa Hoch- oder Herunterladen von Dateien oder anderen Daten? Wenn Ihre Anwendung riskante Aktivitäten ausführt, sollten Sie sich überlegen, wie Ihre Anwendung Benutzer vor der Verarbeitung von bösartigen Dateien oder Daten schützt.

### <a name="review-owasp-top-10"></a>Prüfen von OWASP Top Ten
Es empfiehlt sich, die Dokumente zu [<span class="underline">OWASP Top 10 Application Security Risks</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) zu lesen.
Die OWASP Top 10 befassen sich mit den wesentlichen Sicherheitsrisiken für Webanwendungen.
Fundiertes Wissen zu diesen Sicherheitsrisiken ermöglicht es Ihnen, Anforderungs- und Entwurfsscheidungen zu treffen, mit denen diese Risiken in Ihrer Anwendung minimiert werden.

Es ist wichtig, dass Sie sich Gedanken über Sicherheitskontrollen machen, um Sicherheitsverletzungen zu verhindern.
Sie müssen aber auch annehmen, dass [Sicherheitsverletzungen](/azure/devops/learn/devops-at-microsoft/security-in-devops) auftreten. Das Annehmen einer Sicherheitsverletzung führt dazu, dass einige wichtige Fragen zur Sicherheit im Voraus beantwortet werden, sodass sie nicht in einem Notfall beantwortet werden müssen:

  - Wie erkenne ich einen Angriff?

  - Was tue ich, wenn ein Angriff oder eine Sicherheitsverletzung vorliegt?

  - Wie kann ich nach einem Angriff, etwa Datenabschöpfung oder -manipulation, den ordnungsgemäßen Zustand wiederherstellen?

## <a name="design"></a>Entwurf

Die Entwurfsphase ist entscheidend für das Umsetzen von bewährten Methoden hinsichtlich Entwurfs- und funktionalen Spezifikationen. Sie ist ebenfalls entscheidend für das Vornehmen von Risikoanalysen, die dazu beitragen, Sicherheits- und Datenschutzprobleme innerhalb eines Projekts zu minimieren.

Wenn Sie Sicherheitsanforderungen formuliert haben und sichere Entwurfskonzepte verwenden, können Sie Möglichkeiten für ein Sicherheitsrisiko vermeiden oder minimieren. Ein Sicherheitsrisiko ist ein Flüchtigkeitsfehler im Entwurf der Anwendung, der einem Benutzer die Möglichkeit eröffnet, nach der Veröffentlichung Ihrer Anwendung bösartige oder unerwartete Aktionen auszuführen.

Denken Sie während der Entwurfsphase auch darüber nach, wie Sie Sicherheit in Schichten anwenden können, denn eine Verteidigungsebene ist nicht notwendigerweise ausreichend. Was passiert, wenn ein Angreifer Ihre Web Application Firewall (WAF) überwindet? Sie möchten dafür eine weitere Sicherheitskontrolle einrichten, mit der ein solcher Angriff abgewehrt werden kann.

Vor diesem Hintergrund werden die folgenden Konzepte für sichere Entwürfe und die Sicherheitskontrollen erläutert, die Sie beim Entwerfen sicherer Anwendungen berücksichtigen sollten:

- Verwenden Sie eine sichere Codierungsbibliothek und ein Software-Framework.
- Suchen Sie nach anfälligen Komponenten.
- Verwenden Sie Bedrohungsmodellierung während des Entwerfens der Anwendung.
- Verringern Sie die Angriffsfläche.
- Wenden Sie eine Identitätsrichtlinie als primären Sicherheitsbereich an.
- Verlangen Sie für wichtige Transaktionen eine erneute Authentifizierung.
- Verwenden Sie eine Schlüsselverwaltungslösung, um Schlüssel, Anmeldeinformationen und sonstige Geheimnisse zu schützen.
- Schützen Sie vertrauliche Daten.
- Implementieren Sie ausfallsichere Maßnahmen.
- Nutzen Sie Fehler- und Ausnahmebehandlung.
- Verwenden Sie Protokollierung und Benachrichtigungen.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Verwenden einer sicheren Codierungsbibliothek und eines Software-Frameworks

Verwenden Sie für die Entwicklung eine sichere Codierungsbibliothek und ein Software-Framework, das eingebettete Sicherheit hat. Entwickler können vorhandene bewährte Funktionen (Verschlüsselung, Eingabebereinigung, Ausgabecodierung, Schlüssel oder Verbindungszeichenfolgen sowie jegliche sonstige Maßnahmen, die als Sicherheitskontrolle angesehen werden könnte) verwenden, anstatt Sicherheitskontrollen von Grund auf neu zu entwickeln. Dies hilft, sicherheitsrelevante Entwurfs- und Implementierungsfehler zu vermeiden.

Achten Sie darauf, dass Sie die neueste Version Ihres Frameworks und alle Sicherheitsfunktionen verwenden, die im Framework verfügbar sind. Microsoft bietet eine umfassende [Auswahl an Entwicklungstools](https://azure.microsoft.com/product-categories/developer-tools/), mit denen alle Entwickler, die mit einer beliebige Plattform oder Sprache arbeiten, Cloudanwendungen bereitstellen können. Sie können mit der von Ihnen bevorzugten Sprache codieren, indem Sie aus verschiedenen [SDKs](https://azure.microsoft.com/downloads/) wählen.
Sie können von integrierten Entwicklungsumgebungen (IDEs) mit umfassendem Funktionsumfang sowie von Editoren profitieren, die erweiterte Debuggingfunktionen und integrierten Azure-Support haben.

Microsoft stellt eine Vielzahl von [Sprachen, Frameworks und Tools](../../index.yml?panel=sdkstools-all&pivot=sdkstools) bereit, mit denen Sie Anwendungen in Azure entwickeln können. Ein Beispiel ist [Azure für .NET- und .NET Core-Entwickler](/dotnet/azure/). Für jede Sprache und jedes Framework, das wir anbieten, finden Sie Schnellstarts, Tutorials und API-Referenzen, die Ihnen einen schnellen Einstieg ermöglichen.

Azure bietet eine Vielzahl von Diensten, die Sie zum Hosten von Websites und Webanwendungen verwenden können. Mit diesen Diensten können Sie in Ihrer bevorzugten Sprache entwickeln, egal, ob dies .NET, .NET Core, Java, Ruby, Node.js, PHP oder Python ist.
[Azure App Service-Web-Apps](../../app-service/overview.md) (Web-Apps) ist einer dieser Dienste.

Web-Apps erweitert Ihre Anwendung um die Leistungsfähigkeit von Microsoft Azure. Der Dienst umfasst Sicherheit, Lastenausgleich, automatische Skalierung und automatisierte Verwaltung. Sie können auch die Vorteile der DevOps-Funktionen in Web-Apps nutzen, wozu Paketverwaltung, Stagingumgebungen, benutzerdefinierte Domänen, SSL/TLS-Zertifikate und kontinuierliche Bereitstellung über Azure DevOps, GitHub, Docker Hub und andere Quellen gehören.

Azure bietet weitere Dienste, die Sie zum Hosten von Websites und Webanwendungen verwenden können. In den meisten Fällen sind Web-Apps die beste Wahl. Für eine Microservicearchitektur sollten Sie [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) in Betracht ziehen.
Wenn Sie eine bessere Kontrolle über die virtuellen Computer benötigen, auf denen Ihr Code ausgeführt wird, können Sie [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) einsetzen.
Weitere Informationen zur Wahl zwischen diesen Azure-Diensten finden Sie in einem [Vergleich von Azure App Service, Azure Virtual Machines, Azure Service Fabric und Azure Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Anwenden von Updates auf Komponenten

Um Sicherheitsrisiken zu vermeiden, sollten Sie sowohl Ihre client- als auch Ihre serverseitigen Komponenten (z.B. Frameworks und Bibliotheken) und deren Abhängigkeiten hinsichtlich Updates kontinuierlich erfassen. Neue Sicherheitsrisiken und aktualisierte Softwareversionen werden fortlaufend veröffentlicht. Achten Sie darauf, dass Sie einen permanenten Plan haben, gemäß dem Updates oder Konfigurationsänderungen an den von Ihnen verwendeten Bibliotheken und Komponenten überwacht, selektiert und angewendet werden.

Werkzeugvorschläge finden Sie auf der [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page)-Seite [Using Components with Known Vulnerabilities](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) (Verwenden von Komponenten mit bekannten Sicherheitsrisiken). Sie können außerdem E-Mail-Benachrichtigungen zu Sicherheitsrisiken abonnieren, die mit Komponenten verknüpft sind, die Sie verwenden.

### <a name="use-threat-modeling-during-application-design"></a>Verwenden von Bedrohungsmodellen beim Anwendungsentwurf

Bedrohungsmodellierung ist der Prozess, bei dem potenzielle Sicherheitsbedrohungen für Ihr Unternehmen und Ihre Anwendung identifiziert werden und dann sichergestellt wird, dass geeignete Risikominderungen wirksam sind. Der SDL gibt an, dass sich Teams während der Entwurfsphase mit der Bedrohungsmodellierung beschäftigen sollten, wenn die Lösung möglicher Probleme relativ einfach und kostengünstig ist. Durch Verwenden von Bedrohungsmodellierung in der Entwurfsphase können Sie Ihre Gesamtkosten für die Entwicklung erheblich verringern.

Um den Prozess einer Bedrohungsmodellierung zu vereinfachen, haben wir das [SDL Threat Modeling Tool](threat-modeling-tool.md) so gestaltet, dass es auch für Leute geeignet ist, die keine Sicherheitsexperten sind. Dieses Tool erleichtert die Bedrohungsmodellierung für alle Entwickler, indem es eine klare Anleitung dazu bereitstellt, wie Bedrohungsmodelle zu erstellen und zu analysieren sind.

Das Modellieren des Anwendungsentwurfs und das Aufzählen von [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)-Bedrohungen – Spoofing, Tampering (Manipulation), Repudiation (Nichtanerkennung), Information Disclosure (Veröffentlichung von Informationen), Denial of Service und Elevation of Privilege (Rechteerweiterungen) – über alle Vertrauensgrenzen hinweg hat sich als effektiver Weg erwiesen, Entwurfsfehler frühzeitig zu erkennen. In der folgende Tabelle sind die STRIDE-Bedrohungen sowie einige Beispiele für vorbeugende Maßnahmen aufgeführt, in denen Funktionen verwendet werden, die von Azure bereitgestellt werden. Diese vorbeugenden Maßnahmen funktionieren nicht in allen Situationen.

| Bedrohung | Sicherheitseigenschaft | Mindern potenzieller Risiken für die Azure-Plattform |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Authentifizierung        | [Legen Sie fest, dass HTTPS-Verbindungen erforderlich sind](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1). |
| Manipulation              | Integrität             | Überprüfen Sie SSL/TLS-Zertifikate. Anwendungen, die SSL/TLS verwenden, müssen die X.509-Zertifikate der Entitäten, mit denen sie eine Verbindung herstellen, umfassend überprüfen. Verwenden Sie Azure Key Vault-Zertifikate, um [Ihre X509-Zertifikate zu verwalten](../../key-vault/general/about-keys-secrets-certificates.md). |
| Nichtanerkennung            | Unleugbarkeit       | Aktivieren Sie die [Überwachung und Diagnose](/azure/architecture/best-practices/monitoring) von Azure.|
| Veröffentlichung von Informationen | Vertraulichkeit       | Verschlüsseln Sie sensible [ruhende](../fundamentals/encryption-atrest.md) Daten und Daten [während einer Übertragung](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Denial of Service      | Verfügbarkeit          | Überwachen Sie Leistungsmetriken auf potenzielle Denial of Service-Angriffe. Implementieren Sie Verbindungsfilter. [Azure DDoS-Schutz](../../ddos-protection/ddos-protection-overview.md#next-steps) in Kombination mit bewährten Anwendungsentwurfsmethoden stellt den bestmöglichen Schutz vor DDoS-Angriffen dar.|
| Rechteerweiterungen | Authorization         | Verwenden Sie Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Verringern der Angriffsfläche

Eine Angriffsfläche ist die Gesamtheit der Stellen, an denen potenzielle SicherheitsrisikenSchwachstellen auftreten können. In diesem Artikel konzentrieren wir uns auf die Angriffsfläche einer Anwendung.
Der Schwerpunkt liegt auf dem Schutz einer Anwendung vor Angriffen. Ein einfacher und schneller Weg zum Minimieren der Angriffsfläche besteht darin, ungenutzte Ressourcen und ungenutzten Code aus Ihrer Anwendung zu entfernen. Je kleiner Ihre Anwendung ist, desto kleiner ist die entsprechende Angriffsfläche. Entfernen Sie beispielsweise Folgendes:

- Code für Funktionen, die Sie noch nicht veröffentlicht haben
- Code zur Debugging-Unterstützung
- Netzwerkschnittstellen und -protokolle, die nicht verwendet werden oder veraltet sind
- Virtuelle Computer und andere Ressourcen, die nicht verwendet werden

Regelmäßiges Bereinigen Ihrer Ressourcen und Sicherstellen, dass Sie ungenutzten Code entfernen, sind großartige Möglichkeiten, dafür zu sorgen, dass böswillige Akteure weniger Angriffsmöglichkeiten haben.

Eine detailliertere und gründlichere Möglichkeit zur Verringerung der Angriffsfläche besteht darin, eine Angriffsflächenanalyse durchzuführen. Eine Angriffsflächenanalyse hilft Ihnen, die Teile eines Systems abzubilden, die auf Sicherheitsschwachstellen überprüft und getestet werden müssen.

Der Zweck einer Angriffsflächenanalyse ist es, die Risikobereiche in einer Anwendung zu erkennen, sodass Entwickler und Sicherheitsspezialisten wissen, welche Teile der Anwendung für Angriffe anfällig sind. Dann können Sie nach Wegen suchen, dieses Risikopotenzial zu minimieren, und verfolgen, wann und wie sich die Angriffsfläche ändert und was dies aus Risikoperspektive bedeutet.

Mit einer Angriffsflächenanalyse können Sie Folgendes erkennen:

- Funktionen und Teile des Systems, die Sie auf Sicherheitsrisiken überprüfen und testen müssen
- Besonders risikobehaftete Codebereiche, die einen gründlichen Schutz erfordern (Teile des Systems, die Sie schützen müssen)
- Wann sich die Angriffsfläche geändert hat und die Bedrohungsbewertung aktualisiert werden muss

Um für Angreifer die Möglichkeiten zu verringern, eine potenzielle Schwachstelle oder ein potenzielles Sicherheitsrisiko ausnutzen zu können, müssen Sie die gesamte Angriffsfläche Ihrer Anwendung gründlich analysieren. Dazu gehört auch das Deaktivieren oder Einschränken des Zugriffs auf Systemdienste, das Anwenden des Prinzips der geringsten Rechte und das Nutzen von Verteidigungsebenen, wo immer dies möglich ist.

Hier wird das [Durchführen einer Überprüfung der Angriffsfläche](secure-develop.md#conduct-attack-surface-review) während der Überprüfungsphase des SDL beschrieben.

> [!NOTE]
> **Worin besteht der Unterschied zwischen Bedrohungsmodellierung und Angriffsflächenanalyse?**
Bedrohungsmodellierung ist der Prozess, bei dem potenzielle Sicherheitsbedrohungen für Ihre Anwendung identifiziert werden und sichergestellt wird, dass geeignete Risikominderungen für die Bedrohungen wirksam sind. In einer Angriffsflächenanalyse werden die besonders risikobehafteten Codebereiche ermittelt, die für Angriffe offen sind. Zu einer solchen Analyse gehört es, Wege zu finden, risikobehaftete Bereiche Ihrer Anwendung zu schützen und diese Codebereiche zu überprüfen und zu testen, bevor Sie die Anwendung bereitstellen.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Übernehmen einer Identitätsrichtlinie als primärer Sicherheitsbereich

Wenn Sie Cloudanwendungen entwerfen, ist es wichtig, Ihren Sicherheitsbereichsfokus von einem netzwerkzentrierten Ansatz auf einen identitätszentrierten Ansatz zu erweitern. In der Vergangenheit war der primäre lokale Sicherheitsbereich das Netzwerk einer Organisation. In den meisten lokalen Sicherheitskonzepten wird das Netzwerk als Dreh- und Angelpunkt für die Sicherheit verwendet. Bei Cloudanwendungen empfiehlt es sich dagegen, die Identität als primären Sicherheitsbereich zu betrachten.

Das können Sie tun, um einen identitätszentrierten Ansatz bei der Entwicklung von Webanwendungen zu implementieren:

- Erzwingen Sie mehrstufige Authentifizierung für Benutzer.
- Verwenden Sie sichere Authentifizierungs- und Autorisierungsplattformen.
- Wenden Sie das Prinzip der geringsten Rechte an.
- Implementieren Sie Just-In-Time-Zugriff.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Erzwingen von mehrstufiger Authentifizierung für Benutzer

Verwenden Sie eine zweistufige Authentifizierung. Die zweistufige Authentifizierung ist der aktuelle Standard für die Authentifizierung und Autorisierung, da sie die Sicherheitsprobleme eliminiert, die für Authentifizierungsmethoden mit Benutzername und Kennwort typisch sind. Der Zugriff auf die Azure-Verwaltungsschnittstellen (Azure-Portal/Remote-PowerShell) und auf kundenorientierte Dienste sollte für die Verwendung von [Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) (mehrstufige Authentifizierung) konzipiert und konfiguriert sein.

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Verwenden von sicheren Authentifizierungs- und Autorisierungsplattformen

Verwenden Sie von der Plattform bereitgestellte Authentifizierungs- und Autorisierungsmechanismen anstelle von benutzerdefiniertem Code. Dies empfiehlt sich, weil das Entwickeln von benutzerdefiniertem Authentifizierungscode Fehlerrisiko darstellen kann. Kommerzieller Code (etwa von Microsoft) wird häufig umfangreich auf Sicherheit geprüft. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) ist die Lösung für Identitäts- und Zugriffsverwaltung von Azure. Diese Azure AD-Tools und -Dienste unterstützen bei der sicheren Entwicklung:

- Bei [Microsoft Identity Platform](../../active-directory/develop/index.yml) handelt es sich um eine Reihe von Komponenten, die Entwickler zum Erstellen von Apps verwenden, die Benutzer sicher anmelden. Die Plattform unterstützt sowohl Entwickler, die branchenspezifische Apps für Einzelmandanten erstellen, als auch Entwickler, die mehrinstanzenfähige Apps (Apps für mehrere Mandanten) entwickeln. Neben der einfachen Anmeldung können Apps, die mit Microsoft Identity Platform erstellt wurden, Microsoft-APIs und benutzerdefinierte APIs aufrufen. Microsoft Identity Platform unterstützt die branchenüblichen Protokolle wie OAuth 2.0 und OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) ist ein Identitätsverwaltungsdienst, mit dem Sie anpassen und steuern können, wie sich Kunden registrieren und anmelden und wie die Kunden ihre Profile verwalten, wenn sie Ihre Anwendungen verwenden. Dazu gehören Anwendungen, die für iOS, Android und .NET sowie andere Systeme entwickelt wurden. Azure AD B2C ermöglicht diese Aktionen und schützt gleichzeitig Kundenidentitäten.

#### <a name="apply-the-principle-of-least-privilege"></a>Anwenden des Prinzips der geringsten Rechte

Das Konzept der [geringsten Rechte](https://en.wikipedia.org/wiki/Principle_of_least_privilege) bedeutet, dass Benutzern Zugriff und Kontrolle genau in dem Umfang gewährt wird, den sie zum Erledigen ihrer Aufgaben benötigen, und mehr nicht.

Benötigt ein Softwareentwickler die Rechte eines Domänenadministrators? Benötigt ein Verwaltungsassistent Zugriff auf administrative Komponenten auf seinem Personalcomputer? Bewerten von Zugriff auf Software unterscheidet sich davon nicht. Wenn Sie [rollenbasierte Azure-Zugriffssteuerung (Azure RBAC)](../../role-based-access-control/overview.md) verwenden, um Benutzern unterschiedliche Fähigkeiten und Befugnisse in Ihrer Anwendung zu gewähren, werden Sie nicht jedem Zugriff auf alles ermöglichen. Durch Beschränken des Zugriffs auf die Elemente, die für jede Rolle erforderlich sind, beschränken Sie das Risiko, dass ein Sicherheitsproblem auftritt.

Achten Sie darauf, dass in Ihrer Anwendung in allen Zugriffsmustern das [Prinzip der geringsten Rechte](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) erzwungen wird.

> [!NOTE]
> Die Regeln der geringsten Rechte müssen für die Software und die Personen gelten, die die Software erstellen. Softwareentwickler können ein großes Risiko für die IT-Sicherheit darstellen, wenn ihnen zu viel Zugriff gewährt wird. Die Folgen können schwerwiegend sein, wenn ein Entwickler böswillige Absichten hat oder mehr Zugriff erhält, als er benötigt. Es empfiehlt sich, dass für Entwickler die Regeln der geringsten Rechte während des gesamten Entwicklungslebenszyklus angewendet werden.

#### <a name="implement-just-in-time-access"></a>Implementieren von Just-In-Time-Zugriff

Implementieren Sie *Just-In-Time-Zugriff* (JIT-Zugriff), um die Zeit, in der Rechte verfügbar gemacht werden, weiter zu verkürzen. Verwenden Sie [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity), um wie folgt vorzugehen:

- Erteilen der Berechtigungen, die Benutzer benötigen, nur Just-In-Time.
- Zuweisen von Rollen für eine verkürzte Dauer mit der Gewissheit, dass die Berechtigungen automatisch widerrufen werden.

### <a name="require-re-authentication-for-important-transactions"></a>Verlangen einer erneuten Authentifizierung für wichtige Transaktionen

Bei einer [websiteübergreifenden Anforderungsfälschung](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (auch bekannt als *XSRF* oder *CSRF*) handelt es sich um einen Angriff auf im Web gehostete Apps, in denen eine schädliche Web-App die Interaktion zwischen einem Clientbrowser und einer Web-App beeinflusst, die diesem Browser vertraut. Angriffe in Form von websiteübergreifenden Anforderungsfälschungen sind möglich, weil Webbrowser einige Typen von Authentifizierungstoken automatisch bei jeder Anforderung an eine Website senden.
Diese Form missbräuchlicher Verwendung wird auch als *Ein-Klick-Angriff* oder *Session-Riding* bezeichnet, weil für den Angriff die zuvor authentifizierte Sitzung des Benutzers verwendet wird.

Die beste Möglichkeit, diese Art von Angriff abzuwehren, besteht darin, den Benutzer vor jeder wichtigen Transaktion, z. B. eine Bestellung, Kontodeaktivierung oder Kennwortänderung, nach etwas zu fragen, das nur er bereitstellen kann. Sie können den Benutzer bitten, sein Passwort erneut einzugeben, ein Captcha zu vervollständigen oder ein geheimes Token zu senden, das nur der Benutzer hat. Der gängigste Ansatz ist das geheime Token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Verwenden einer Schlüsselverwaltungslösung, um Schlüssel, Anmeldeinformationen und sonstige Geheimnisse zu schützen

Der Verlust von Schlüsseln und Anmeldeinformationen ist ein verbreitetes Problem. Noch schlimmer als der Verlust von Schlüsseln und Anmeldeinformationen ist es, wenn nicht autorisierte Personen an Ihre Schlüssel und Anmeldeinformationen gelangen. Angreifer können automatisierte und manuelle Techniken nutzen, um nach Schlüsseln und Geheimnissen zu suchen, die in Coderepositorys wie GitHub gespeichert sind. Legen Sie keine Schlüssel und Geheimnisse in diesen öffentlichen Coderepositorys oder auf irgendeinem anderen Server ab.

Legen Sie Ihre Schlüssel, Zertifikate, Geheimnisse und Verbindungszeichenfolgen immer in einer Schlüsselverwaltungslösung ab. Sie können eine zentrale Lösung verwenden, in der Schlüssel und Geheimnisse in Hardwaresicherheitsmodulen (HSMs) gespeichert werden. Azure stellt Ihnen mit [Azure Key Vault](../../key-vault/general/overview.md) ein HSM in der Cloud zur Verfügung.

Key Vault ist ein *Geheimnisspeicher*: Es ist eine zentraler Clouddienst zum Speichern von Anwendungsgeheimnissen. Key Vault schützt Ihre vertraulichen Daten, indem es Anwendungsgeheimnisse an einem einzigen zentralen Ort aufbewahrt und sicheren Zugriff, Berechtigungssteuerung und Zugriffsprotokollierung bietet.

Geheimnisse werden in einzelnen *Tresoren* (Vaults) gespeichert. Jeder Tresor hat seine eigene Konfiguration und eigenen Sicherheitsrichtlinien, um Zugriffe zu steuern. Sie rufen Ihren Daten über eine REST-API oder über ein Client-SDK ab, das für die meisten Programmiersprachen verfügbar ist.

> [!IMPORTANT]
> Azure Key Vault ist dafür konzipiert, Konfigurationsgeheimnisse für Serveranwendungen zu speichern. Es ist nicht dazu vorgesehen, Daten zu speichern, die zu App-Benutzern gehören. Dies findet sich in den Leistungsmerkmalen, der API und dem Kostenmodell von Azure Key Vault wieder.
>
> Benutzerdaten sollten an anderer Stelle gespeichert werden, z.B. in einer Azure SQL-Datenbankinstanz mit Transparent Data Encryption (TDE) oder in einem Speicherkonto, für das Azure-Speicherdienstverschlüsselung verwendet wird. Geheimnisse, die von Ihrer Anwendung für den Zugriff auf diese Datenspeicher verwendet werden, können in Azure Key Vault aufbewahrt werden.

### <a name="protect-sensitive-data"></a>Schützen von vertraulichen Daten

Schützen von Daten ist ein wesentlicher Bestandteil Ihrer Sicherheitsstrategie.
Das Klassifizieren Ihrer Daten und das Bestimmen Ihrer Datenschutzanforderungen ermöglicht es Ihnen, Ihre App mit Beachtung der Datensicherheit zu entwerfen. Durch Klassifizieren (Kategorisieren) von gespeicherten Daten nach Vertraulichkeit und geschäftlichen Auswirkungen können Entwickler die Risiken bestimmen, die mit Daten verbunden sind.

Kennzeichnen Sie alle maßgeblichen Daten als sensibel, wenn Sie Ihre Datenformate konzipieren. Stellen Sie sicher, dass die Anwendung die maßgeblichen Daten als sensible Daten behandelt. Diese Maßnahmen können Ihnen helfen, Ihre sensiblen Daten zu schützen:

- Verwenden Sie Verschlüsselung.
- Vermeiden Sie die Hartcodierung von Geheimnissen wie Schlüssel und Kennwörter.
- Stellen Sie sicher, dass Zugriffssteuerungen und Überwachung vorhanden sind.

#### <a name="use-encryption"></a>Verwenden von Verschlüsselung

Schützen von Daten sollte ein wesentlicher Bestandteil Ihrer Sicherheitsstrategie sein.
Wenn Ihre Daten in einer Datenbank gespeichert sind oder wenn die Daten zwischen Standorten hin und her gesendet werden, verwenden Sie Verschlüsselung von [ruhenden Daten](../fundamentals/encryption-atrest.md) (während diese sich in der Datenbank befinden) und Verschlüsselung von [Daten während der Übertragung](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (auf deren Weg zu und von einem Benutzer, zu und von einer Datenbank oder API oder zu und von einem Dienstendpunkt). Es empfiehlt sich, für den Austausch von Daten immer SSL/TLS-Protokolle zu verwenden. Achten Sie darauf, dass Sie die neueste Version von TLS für Verschlüsselung verwenden (aktuell ist dies Version 1.2).

#### <a name="avoid-hard-coding"></a>Vermeiden von Hartcodierung

Einige Elemente sollten in Ihre Software nie hartcodiert sein. Einige Beispiele sind Hostnamen oder IP-Adressen, URLs, E-Mail-Adressen, Benutzernamen, Kennwörter, Speicherkontoschlüssel und andere kryptografische Schlüssel. Erwägen Sie das Implementieren von Anforderungen zu den Elementen, die in Ihrem Code hartcodiert werden dürfen oder nicht, wobei auch die Kommentarabschnitte in Ihrem Code berücksichtigt werden sollten.

Wenn Sie Kommentare in Ihren Code einfügen, müssen Sie darauf achten, dass Sie keine vertraulichen Informationen speichern. Dazu gehören Ihre E-Mail-Adresse, Kennwörter, Verbindungszeichenfolgen, Informationen über Ihre Anwendung, die nur jemand in Ihrer Organisation bekannt sein sollten, und alles Sonstige, was einem Angreifer einen Vorteil bei einem Angriff auf Ihre Anwendung oder Organisation verschaffen könnte.

Grundsätzlich sollten Sie davon ausgehen, dass alles in Ihrem Entwicklungsprojekt öffentlich bekannt ist, sobald das Projekt bereitgestellt wurde. Vermeiden Sie es, sensible Daten jeglicher Art in das Projekt aufzunehmen.

An früherer Stelle ist [Azure Key Vault](../../key-vault/general/overview.md) erläutert. Sie können Key Vault verwenden, um Geheimnisse, etwa Schlüssel und Kennwörter, zu speichern, anstatt sie hartzucodieren. Wenn Sie Key Vault zusammen mit verwalteten Identitäten für Azure-Ressourcen verwenden, kann Ihre Azure-Webanwendung einfach und sicher auf geheime Konfigurationswerte zugreifen, ohne irgendwelche Geheimnisse in Ihrer Quellcodeverwaltung oder Konfiguration zu speichern. Weitere Informationen hierzu finden Sie unter [Verwalten von Geheimnissen in Ihren Server-Apps mit Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementieren von ausfallsicheren Maßnahmen

Ihre Anwendung muss in der Lage, [Fehler](/dotnet/standard/exceptions/), die während der Ausführung auftreten, konsistent zu verarbeiten. Die Anwendung sollte alle Fehler abfangen und entweder ausfallsicher sein oder geschlossen werden.

Sie sollten auch sicherstellen, dass Fehler mit ausreichendem Benutzerkontext protokolliert werden, um verdächtige oder bösartige Aktivitäten zu erkennen. Protokolle sollten ausreichend lange aufbewahrt werden, um eine verzögerte forensische Analyse zu ermöglichen. Protokolle sollten in einem Format vorliegen, das von einer Protokollverwaltungslösung problemlos verwendet werden kann. Stellen Sie sicher, dass Warnungen für Fehler ausgelöst werden, die die Sicherheit betreffen. Eine unzureichende Protokollierung und Überwachung versetzt Angreifer in die Lage, Systeme weiter anzugreifen und Persistenz zu erhalten.

### <a name="take-advantage-of-error-and-exception-handling"></a>Nutzen von Fehler- und Ausnahmebehandlung

Das Implementieren einer korrekten Fehler- und [Ausnahmebehandlung](/dotnet/standard/exceptions/best-practices-for-exceptions) ist ein wichtiger Bestandteil von defensiver Programmierung. Fehler- und Ausnahmebehandlung sind entscheidend, um ein System zuverlässig und sicher zu machen. Fehler in der Fehlerbehandlung können zu verschiedenen Arten von Sicherheitsrisiken führen, etwa ein Durchsickern von Informationen an Angreifer sowie ermöglichen, dass Angreifer Ihre Plattform und deren Entwurf besser verstehen.

Stellen Sie Folgendes sicher:

- Sie behandeln Ausnahmen an zentraler Stelle, um doppelte [try/catch](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)-Blöcke im Code zu vermeiden.

- Jedes unerwartete Verhalten wird in der Anwendung behandelt.

- Meldungen, die den Benutzern angezeigt werden, enthalten keine kritischen Daten, sondern stellen genügend Informationen bereit, um das Problem zu erläutern.

- Ausnahmen werden protokolliert und stellen genügend Informationen für die Forensik oder die Gefahrenabwehrteams zur Verfügung.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) bietet eine erstklassige Umgebung für das [Behandeln von Fehlern und Ausnahmen](../../logic-apps/logic-apps-exception-handling.md), die durch abhängige Systeme verursacht werden. Mit Logic Apps können Sie Workflows erstellen, um Aufgaben und Prozesse zu automatisieren, in denen Apps, Daten, Systeme und Dienste in Unternehmen und Organisationen integriert sind.

### <a name="use-logging-and-alerting"></a>Verwenden von Protokollierung und Benachrichtigungen

[Protokollieren](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) Sie sicherheitsbezogene Probleme für Sicherheitsuntersuchungen, und lösen Sie Benachrichtigungen zu Problemen aus, damit sichergestellt ist, dass betroffene Personen rechtzeitig über Probleme informiert werden. Aktivieren Sie die Überwachung und Protokollierung für alle Komponenten. Überwachungsprotokolle sollten den Benutzerkontext erfassen und alle wichtigen Ereignisse identifizieren.

Stellen Sie sicher, dass Sie keine sensiblen Daten protokollieren, die ein Benutzer an Ihre Website übermittelt. Beispiele für sensible Daten:

- Benutzeranmeldeinformationen
- Sozialversicherungsnummern oder andere identifizierende Informationen
- Kreditkartennummern oder andere finanzielle Informationen
- Informationen zur Gesundheit
- Private Schlüssel oder anderen Daten, die verwendet werden können, um verschlüsselte Informationen zu entschlüsseln
- System- oder Anwendungsinformationen, die für effektivere Angriffe auf die Anwendung verwendet werden können

Stellen Sie sicher, dass die Anwendung Ereignisse der Benutzerverwaltung überwacht, z.B. erfolgreiche und fehlgeschlagene Benutzeranmeldungen, Kennwortzurücksetzungen, Kennwortänderungen, Kontosperrungen und Benutzerregistrierungen. Das Protokollieren dieser Ereignisse ermöglicht es Ihnen, potenziell verdächtiges Verhalten zu erkennen und darauf zu reagieren. Es ermöglicht Ihnen auch, Betriebsdaten zu sammeln, so z.B., wer auf die Anwendung zugreift.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln empfehlen wir die Sicherheitskontrollen und -aktivitäten, die Ihnen beim Entwickeln und Bereitstellen von sicheren Anwendungen helfen können.

- [Entwickeln sicherer Anwendungen](secure-develop.md)
- [Bereitstellen sicherer Anwendungen](secure-deploy.md)