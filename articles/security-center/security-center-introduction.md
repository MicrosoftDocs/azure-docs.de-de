---
title: Was ist Azure Security Center?| Microsoft-Dokumentation
description: 'Auf dieser Seite werden die Hauptvorteile von Security Center beschrieben: die Ermittlung Ihres Sicherheitsstatus sowie die Verbesserung dieses Status durch die Abdeckung cloudbasierter und lokaler Ressourcen.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0a1cac0fe61ea13a18224ea5992b5c7c1ba69d8a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510334"
---
# <a name="what-is-azure-security-center"></a>Was ist Azure Security Center?

Azure Security Center ist ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt wird und ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud (in Azure oder anderswo) und in der lokalen Umgebung bereitgestellt wird.

Der Schutz Ihrer Ressourcen ist eine gemeinsame Aufgabe, die von Ihrem Cloudanbieter (Azure) und Ihnen selbst (dem Kunden) wahrgenommen wird. Sie müssen sicherstellen, dass Ihre Workloads bei der Umstellung auf die Cloud geschützt sind. Bei der Umstellung auf IaaS (Infrastructure-as-a-Service) erhöht sich gegenüber PaaS (Platform-as-a-Service) und SaaS (Software-as-a-Service) außerdem die Verantwortung des Kunden. Mit Azure Security Center erhalten Sie die Tools, die Sie zum Absichern Ihres Netzwerks, Schützen Ihrer Dienste und Sicherstellen des Sicherheitsstatus benötigen.

Azure Security Center ist die Lösung für die drei größten Herausforderungen in Bezug auf die Sicherheit:

-   **Sich schnell ändernde Workloads**: Dies ist gleichzeitig eine Stärke und eine Schwäche der Cloud. Einerseits werden Benutzer in die Lage versetzt, mehr zu erreichen. Andererseits stellt sich die folgende Frage: Wie können Sie sicherstellen, dass die sich ständig ändernden Dienste, die von Benutzern verwendet und erstellt werden, Ihren Sicherheitsstandards entsprechen und auf den bewährten Sicherheitsmethoden basieren?

-   **Immer ausgefeiltere Angriffe**: Unabhängig davon, wo Sie Ihre Workloads ausführen – die Angriffe werden immer komplexer und ausgefeilter. Sie müssen Ihre öffentlichen Cloudworkloads schützen. Bei dieser Aufgabe handelt es sich praktisch um eine internetfähige Workload, die dazu führen kann, dass Sie noch angreifbarer sind, wenn Sie sich nicht an die bewährten Sicherheitsmethoden halten.

-   **Mangel an Personen mit Sicherheitskenntnissen**: Die Anzahl von Sicherheitswarnungen und Warnsystemen übersteigt bei Weitem die Anzahl von verfügbaren Administratoren mit dem erforderlichen Hintergrund und der Erfahrung, die zum Schützen Ihrer Umgebungen benötigt wird. Es ist eine ständige Herausforderung, hinsichtlich der neuesten Angriffstechniken immer auf dem Laufenden zu bleiben. Da sich der Sicherheitsbereich ständig ändert, ist es unmöglich, auf demselben Stand zu verharren.

Damit Sie diese Herausforderungen in Bezug auf den Schutz bewältigen können, werden mit Security Center Tools für folgende Zwecke bereitgestellt:

-   **Erhöhung des Sicherheitsstatus**: Mit Security Center wird Ihre Umgebung bewertet, damit Sie den Status Ihrer Ressourcen verstehen können und ob sie sicher sind oder nicht.

-   **Schutz vor Bedrohungen**: Security Center bewertet Ihre Workloads und löst Empfehlungen zur Vermeidung von Bedrohungen sowie Sicherheitswarnungen aus.

-   **Schnellerer Aufbau des Schutzes**: In Security Center werden alle Vorgänge mit Cloudgeschwindigkeit durchgeführt. Aufgrund der nativen Integration ist die Bereitstellung von Security Center einfach, und Sie erhalten eine automatische Bereitstellung und den Schutz durch Azure-Dienste.

> [!NOTE]
> Dieser Dienst unterstützt [Azure Lighthouse](../lighthouse/overview.md). Dies ermöglicht es Dienstanbietern, sich bei ihrem eigenen Mandanten anzumelden, um von Kunden delegierte Abonnements und Ressourcengruppen zu verwalten. In Azure Security Center-Szenarien muss ein Abonnement anstelle einzelner Ressourcengruppen delegiert werden.

## <a name="architecture"></a>Aufbau

Da Security Center ein nativer Teil von Azure ist, werden PaaS-Dienste in Azure – z.B. Service Fabric, SQL-Datenbank, SQL Managed Instance und Speicherkonten – mit Security Center überwacht und geschützt, ohne dass eine Bereitstellung erforderlich ist.

Darüber hinaus werden mit Security Center auch Server und virtuelle Computer, die nicht zu Azure gehören, in der Cloud und lokal geschützt. Dies ist sowohl für Windows- als auch für Linux-Server möglich, indem der Log Analytics-Agent darauf installiert wird. Virtuelle Azure-Computer werden in Security Center automatisch bereitgestellt.

Die über die Agents und über Azure erfassten Ereignisse werden im Sicherheitsanalysemodul korreliert, um maßgeschneiderte Empfehlungen zur Verbesserung der Workloadsicherheit (Härtung) sowie Sicherheitswarnungen bereitzustellen. Es ist ratsam, die Warnungen schnellstmöglich zu untersuchen, um sicherzustellen, dass es für Ihre Workloads nicht zu schädlichen Angriffen kommt.

Wenn Sie Security Center aktivieren, wird die in Security Center integrierte Sicherheitsrichtlinie in Azure Policy als integrierte Initiative unter der Kategorie „Security Center“ dargestellt. Die integrierte Initiative wird automatisch allen in Security Center registrierten Abonnements zugewiesen (unabhängig davon, ob Azure Defender aktiviert ist oder nicht). Die integrierte Initiative enthält nur Überwachungsrichtlinien. Weitere Informationen zu Security Center-Richtlinien in Azure Policy finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Stärken des Sicherheitsstatus

Mit Azure Security Center können Sie Ihren Sicherheitsstatus erhöhen. Sie erhalten mit dem Dienst Unterstützung beim Identifizieren und Durchführen von Aufgaben, die als bewährte Sicherheitsmethoden empfohlen werden, und implementieren sie dann für Ihre Computer, Datendienste und Apps. Dies umfasst auch die Verwaltung und Durchsetzung Ihrer Sicherheitsrichtlinien sowie die Sicherstellung, dass Ihre virtuellen Azure-Computer, Azure-externen Server und Azure PaaS-Dienste konform sind. Mit Security Center erhalten Sie die Tools, die Sie benötigen, um den Gesamtüberblick über Ihre Workloads und besonders über die Sicherheitseinrichtungen Ihres Netzwerks zu behalten. 

### <a name="manage-organization-security-policy-and-compliance"></a>Verwalten der Sicherheitsrichtlinie und der Konformität der Organisation

Es ist eine grundlegende Sicherheitsanforderung, dass Sie Ihre Workloads kennen und für deren Schutz sorgen. Der erste Schritt hierbei ist, maßgeschneiderte Sicherheitsrichtlinien aufzustellen. Da alle Richtlinien in Security Center auf Azure Policy-Kontrollen basieren, können Sie vom vollen Funktionsumfang und der Flexibilität einer **erstklassigen Richtlinienlösung** profitieren. In Security Center können Sie Ihre Richtlinien so festlegen, dass sie für Verwaltungsgruppen, übergreifend für Abonnements und sogar für einen gesamten Mandanten ausgeführt werden.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Seite für die Richtlinienverwaltung":::

Mit Security Center können Sie **Schatten-IT-Abonnements identifizieren**. Indem Sie sich in Ihrem Dashboard Abonnements mit dem Hinweis **Nicht abgedeckt** ansehen, können Sie sofort erkennen, wann neu erstellte Abonnements vorhanden sind. Sie können dann sicherstellen, dass diese durch Ihre Richtlinien abgedeckt und mit Azure Security Center geschützt sind.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Security Center-Dashboard für Richtlinien":::

### <a name="continuous-assessments"></a>Fortlaufende Bewertungen

Mit Security Center werden ständig neue Ressourcen erkannt, die für Ihre Workloads bereitgestellt werden, und es wird bewertet, ob diese gemäß den bewährten Sicherheitsmethoden konfiguriert sind. Wenn nicht, werden sie gekennzeichnet, und Sie erhalten eine nach Priorität sortierte Liste mit Empfehlungen zu den Schritten, die zum Schützen Ihrer Computer ausgeführt werden müssen.

Damit Sie besser nachvollziehen können, wie wichtig die einzelnen Empfehlungen für den gesamten Sicherheitsstatus sind, werden die Empfehlungen von Security Center in Sicherheitskontrollelemente gruppiert, und jedem Sicherheitskontrollelement wird eine **Sicherheitsbewertung** hinzugefügt. Dies ist ein entscheidender Faktor beim **Priorisieren Ihrer Sicherheitsmaßnahmen**.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Sicherheitsbewertung von Security Center":::

### <a name="network-map"></a>Netzwerkübersicht

Eines der leistungsfähigsten Tools, das von Security Center für die ständige Überwachung des Sicherheitsstatus Ihres Netzwerks bereitgestellt wird, ist die **Netzwerkübersicht**. In der Übersicht können Sie die Topologie Ihrer Workloads anzeigen, um zu ermitteln, ob jeder Knoten richtig konfiguriert ist. Sie sehen, wie Ihre Knoten miteinander verbunden sind, und können unerwünschte Verbindungen blockieren, die einem Angreifer unter Umständen die Bewegung durch Ihr Netzwerk erleichtern könnten.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Security Center – Netzwerkübersicht":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimieren und Verbessern der Sicherheit durch die Konfiguration von empfohlenen Kontrollen

Der Hauptnutzen von Azure Security Center basiert auf den Empfehlungen. Die Empfehlungen sind genau an die jeweiligen Sicherheitsrisiken Ihrer Workloads angepasst. Security Center übernimmt die Sicherheitsverwaltung für Sie, indem nicht nur Ihre Sicherheitsrisiken ermittelt werden, sondern auch spezifische Anleitungen zu ihrer Beseitigung bereitgestellt werden.

Auf diese Weise können Sie mit Security Center nicht nur die Sicherheitsrichtlinien festlegen, sondern auch Standards für die sichere Konfiguration Ihrer gesamten Ressourcen anwenden.

Mit den Empfehlungen können Sie die Angriffsfläche jeder einzelnen Ressource verkleinern. Dies umfasst virtuelle Azure-Computer, Azure-externe Server und Azure PaaS-Dienste, z.B. SQL und Speicherkonten, und mehr. Hierbei wird jeder Ressourcentyp anders bewertet und verfügt über eigene Standards.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Beispiel für Security Center-Empfehlung":::

## <a name="protect-against-threats"></a>Schutz vor Bedrohungen

Mit dem Bedrohungsschutz von Security Center können Sie Bedrohungen auf IaaS-Ebene (Infrastructure-as-a-Service), für Azure-externe Server und auf PaaS-Ebene (Platform-as-a-Service) in Azure erkennen und verhindern.

Der Bedrohungsschutz von Security Center enthält eine Fusion-Schrittfolgenanalyse, bei der Warnungen in Ihrer Umgebung basierend auf einer Cyber-Schrittfolgenanalyse automatisch korreliert werden. Hiermit können Sie den gesamten Verlauf eines Angriffs, seinen Ausgangspunkt und die Auswirkungen auf Ihre Ressourcen besser verstehen.

:::image type="content" source="./media/security-center-intro/sc-alerts.png" alt-text="Sicherheitswarnungen":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Integration in Microsoft Defender für Endpunkt

Security Center beinhaltet die automatische native Integration in Microsoft Defender für Endpunkt. Dies bedeutet, dass Ihre virtuellen Windows- und Linux-Computer ohne Konfigurationsaufwand vollständig in die Empfehlungen und Bewertungen von Security Center integriert sind.

Außerdem können Sie mit Security Center die Anwendungssteuerungsrichtlinien in Serverumgebungen automatisieren. Mit den adaptiven Anwendungssteuerungen in Security Center können Sie das End-to-End-App-Genehmigungslisting für Ihre Windows-Server nutzen. Sie müssen keine Regeln erstellen und keine Prüfung auf Verstöße durchführen. Dies wird alles automatisch für Sie erledigt.

### <a name="protect-paas"></a>Schützen von PaaS

Mit Security Center können Sie Bedrohungen für Azure PaaS-Dienste erkennen. Sie können Bedrohungen erkennen, die Azure-Dienste wie Azure App Service, Azure SQL, Azure Storage-Konto und andere betreffen. Außerdem können Sie die native Integration mit dem Feature „User and Entity Behavioral Analytics“ (UEBA) von Microsoft Cloud App Security nutzen, um eine Anomalieerkennung für Ihre Azure-Aktivitätsprotokolle durchzuführen.

### <a name="block-brute-force-attacks"></a>Blockieren von Brute-Force-Angriffen

Mit Security Center können Sie die Anfälligkeit für Brute-Force-Angriffe verringern. Wenn Sie den Zugriff auf VM-Ports einschränken, indem Sie den Just-In-Time-Zugriff auf VMs nutzen, können Sie die Sicherheit für Ihr Netzwerk erhöhen, weil unnötige Zugriffsvorgänge verhindert werden. Sie können für ausgewählte Ports Richtlinien für den sicheren Zugriff festlegen, damit der Zugriff nur für autorisierte Benutzer, zulässige IP-Quelladressen oder IP-Adressbereiche und einen begrenzten Zeitraum möglich ist.

### <a name="protect-data-services"></a>Schützen von Datendiensten

Security Center enthält Funktionen, mit denen Sie eine automatische Klassifizierung Ihrer Daten in Azure SQL durchführen können. Sie können auch Bewertungen für potenzielle Sicherheitsrisiken für Azure SQL- und Storage-Dienste sowie Empfehlungen zu ihrer Entschärfung erhalten.

## <a name="get-secure-faster"></a>Schnellerer Aufbau des Schutzes

Dank der Kombination aus nativer Azure-Integration (einschließlich Azure Policy und Azure Monitor-Protokollen) und nahtloser Integration in andere Microsoft-Sicherheitslösungen wie etwa Microsoft Cloud App Security und Microsoft Defender für Endpunkt können Sie sicherstellen, dass Ihre Sicherheitslösung umfassend ist und einfach eingeführt werden kann (Onboarding und Rollout).

Darüber hinaus können Sie die vollständige Lösung über Azure hinaus auf weitere Workloads ausdehnen, die in anderen Clouds und in lokalen Rechenzentren ausgeführt werden.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatisches Erkennen und Einbinden von Azure-Ressourcen

Security Center ermöglicht eine nahtlose, native Integration in Azure und Azure-Ressourcen. Dies bedeutet, dass Sie eine vollständige Sicherheitslösung mit Azure Policy und integrierten Security Center-Richtlinien für Ihre gesamten Azure-Ressourcen zusammenstellen können. Außerdem können Sie sicherstellen, dass dies automatisch auf neu erkannte Ressourcen angewendet wird, wenn Sie diese in Azure erstellen.

Umfassende Protokollsammlung: Protokolle von Windows und Linux werden im Sicherheitsanalysemodul genutzt und für die Erstellung von Empfehlungen und Warnungen verwendet.

## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg in Security Center benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free/)registrieren.

- Der Free-Tarif von Security Center ist für alle aktuellen Azure-Abonnements aktiviert, sobald Sie das Azure Security Center-Dashboard im Azure-Portal zum ersten Mal aufrufen oder wenn es programmgesteuert über die API aktiviert wird. Sie müssen Azure Defender aktivieren, um erweiterte Funktionen für die Sicherheitsverwaltung und Bedrohungserkennung nutzen zu können. Azure Defender kann 30 Tage lang kostenlos getestet werden. Weitere Informationen finden Sie auf der Seite mit den [Preisen für Security Center](https://azure.microsoft.com/pricing/details/security-center/).

- Wenn Sie Azure Defender jetzt aktivieren möchten, finden Sie unter [Schnellstart: Einrichten von Azure Security Center](security-center-get-started.md) die entsprechenden Schritte.