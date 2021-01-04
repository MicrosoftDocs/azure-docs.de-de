---
title: Was ist Azure API for FHIR? - Azure API for FHIR
description: Azure API for FHIR ermöglicht einen Datenaustausch über FHIR-APIs. Führen Sie mit einem verwalteten Clouddienst die Erfassung, Verwaltung und dauerhafte Speicherung von geschützten Gesundheitsdaten (Protected Health Information, PHI) durch.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/13/2020
ms.author: matjazl
ms.openlocfilehash: 386003d7dae80e086f523b2cebe61aa0eee7b89e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95527818"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Was ist Azure API for FHIR&reg;?

Azure API for FHIR ermöglicht einen schnellen Datenaustausch über FHIR®-APIs (Fast Healthcare Interoperability Resources), die auf einem Angebot mit verwaltetem PaaS (Platform-as-a Service) in der Cloud basieren. Er erleichtert allen Personen, die mit Gesundheitsdaten arbeiten, das Erfassen, Verwalten und dauerhafte Speichern von geschützten Gesundheitsdaten ([Protected Health Information, PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html)) in der Cloud: 

- Verwalteter FHIR-Dienst, Bereitstellung in der Cloud innerhalb weniger Minuten 
- Für Unternehmen geeigneter FHIR®-basierter Endpunkt in Azure für den Datenzugriff und die Speicherung im FHIR®-Format
- Hohe Leistung und geringe Latenz
- Sichere Verwaltung von geschützten Gesundheitsdaten in einer konformen Cloudumgebung
- SMART-Ansatz für FHIR für mobile und Webimplementierungen
- Bedarfsabhängiges Steuern Ihrer eigenen Daten per rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC)
- Nachverfolgen von Überwachungsprotokollen für Zugriff, Erstellung, Änderung und Lesevorgänge in jedem Datenspeicher

Azure API for FHIR ermöglicht Ihnen das Erstellen und Bereitstellen eines FHIR-Diensts in wenigen Minuten, um die elastische Skalierung der Cloud zu nutzen.  Sie zahlen nur für den benötigten Durchsatz bzw. Speicherplatz. Die Azure-Dienste, auf denen Azure API for FHIR basiert, sind unabhängig von der Größe der verwalteten Datasets auf schnelle Leistung ausgelegt.

Mit der FHIR-API und dem konformen Datenspeicher können Sie eine sichere Verbindung mit einem beliebigen System herstellen, für das FHIR-APIs genutzt werden, und damit interagieren.  Microsoft übernimmt die Anforderungen in Bezug auf Betrieb, Wartung, Aktualisierung und Compliance im Rahmen des PaaS-Angebots, um Ihre eigenen Ressourcen für Betrieb und Entwicklung zu entlasten. 

Das folgende Video enthält einen Überblick über Azure API for FHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Nutzen der Leistungsfähigkeit Ihrer Daten mit FHIR

In der Gesundheitsbranche werden Gesundheitsdaten derzeit mit Hochdruck auf den neuen Standard [FHIR&reg;](https://hl7.org/fhir) (Fast Healthcare Interoperability Resources) umgestellt. FHIR ermöglicht die Nutzung eines stabilen, erweiterbaren Datenmodells mit einer Standardisierung in Bezug auf die Semantik und den Datenaustausch, damit alle Systeme, für die FHIR verwendet wird, zusammenarbeiten können.  Indem Sie Ihre Daten in das FHIR-Format transformieren, können Sie vorhandene Datenquellen schnell verbinden, z. B. Systeme mit elektronischen Gesundheitsakten oder Forschungsdatenbanken. Darüber hinaus ermöglicht FHIR auch den schnellen Austausch von Daten in modernen Implementierungen der mobilen Entwicklung und Webentwicklung. Vor allem kann per FHIR die Datenerfassung vereinfacht und die Entwicklung mit Analyse- und Machine Learning-Tools beschleunigt werden.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Sicheres Verwalten von Gesundheitsdaten in der Cloud

Mit Azure API for FHIR ist der Datenaustausch über einheitliche RESTful-FHIR-APIs gemäß der HL7 FHIR-Spezifikation möglich. Der Dienst basiert auf einem Angebot mit verwaltetem PaaS-Ansatz in Azure und verfügt auch über eine skalierbare und sichere Umgebung für die Verwaltung und Speicherung von geschützten Gesundheitsdaten (Protected Health Information, PHI) im nativen FHIR-Format.  

### <a name="free-up-your-resources-to-innovate"></a>Mehr Zeit für Innovationen für Ihre Ressourcen

Sie können in Ressourcen investieren, um Ihren eigenen FHIR-Dienst zu erstellen und auszuführen. Aber bei Verwendung von Azure API for FHIR übernimmt Microsoft der Anforderungen in Bezug auf Betrieb, Wartung, Aktualisierung und Compliance, damit Sie Ihre eigenen Betriebs- und Entwicklungsressourcen für andere Aufgaben einsetzen können.

### <a name="enable-interoperability-with-fhir"></a>Interoperabilität mit FHIR

Mit Azure API for FHIR können Sie eine Verbindung mit beliebigen Systemen herstellen, die FHIR-APIs für das Lesen, Schreiben, Suchen und andere Funktionen nutzen.  Der Dienst kann als leistungsfähiges Tool eingesetzt werden, um Konsolidierungen und Normalisierungen durchzuführen. Er ermöglicht die Nutzung von maschinellem Lernen mit klinischen Daten aus elektronischen Gesundheitsakten, Dashboards für Ärzte und Patienten, Programmen zur Remoteüberwachung oder mit Datenbanken außerhalb Ihres Systems, die über FHIR-APIs verfügen.

### <a name="control-data-access-at-scale"></a>Bedarfsabhängiges Steuern des Datenzugriffs

Sie haben die Kontrolle über Ihre Daten. Mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) können Sie die Speicherung und den Zugriff für Ihre Daten verwalten.  Indem Sie die Sicherheit erhöhen und den Verwaltungsaufwand reduzieren, bestimmen Sie, wer Zugriff auf die von Ihnen erstellten Datasets hat. Dies basiert auf den Rollendefinitionen, die Sie für Ihre Umgebung erstellen.  

### <a name="audit-logs-and-tracking"></a>Überwachungsprotokolle und Nachverfolgung 

Anhand von integrierten Überwachungsprotokollen können Sie schnell nachverfolgen, wo sich Ihre Daten befinden. Sie können den Zugriff, die Erstellung, Änderungen und Lesevorgänge in jedem Datenspeicher nachverfolgen.

### <a name="secure-your-data"></a>Sichern der Daten

Schützen Sie Ihre geschützten Gesundheitsdaten mit einzigartigen Sicherheitsfunktionen (Security Intelligence).  Ihre Daten sind für jede API-Instanz in einer eindeutigen Datenbank isoliert und durch die Möglichkeit eines Failovers über mehrere Regionen hinweg geschützt. Mit Azure API for FHIR werden mehrstufige umfassende Verteidigungsmaßnahmen und ein erweiterter Bedrohungsschutz für Ihre Daten implementiert.  

## <a name="applications-for-a-fhir-service"></a>Anwendungen für einen FHIR-Dienst

FHIR-Server sind wichtige Tools für die Interoperabilität von Gesundheitsdaten.  Azure API for FHIR ist als API und Dienst so konzipiert, dass die Erstellung, Bereitstellung und die erste Nutzung nach kurzer Zeit möglich sind.  Wenn der FHIR-Standard im Gesundheitswesen immer mehr eingeführt wird, wird auch die Zahl der Anwendungsfälle weiter steigen. Einige erste Kundenanwendungen, für die Azure API for FHIR hilfreich ist, sind hier aber bereits angegeben: 

- **Startup/IoT und App-Entwicklung:**  Kunden, die eine App für Patienten oder Anbieter entwickeln (mobil oder Web), können Azure API for FHIR als vollständig verwalteten Back-End-Dienst nutzen. Azure API for FHIR ist eine wertvolle Ressource, weil Kunden Daten in einer sicheren Cloudumgebung verwalten und austauschen können, die speziell auf Gesundheitsdaten ausgelegt ist. Darüber hinaus können sie im Rahmen der Richtlinien für die FHIR-Implementierung den SMART-Ansatz verwenden und ihre Technologie so gestalten, dass sie von allen Anbietersystemen genutzt werden kann (z. B. sind bei den meisten elektronischen Gesundheitsakten FHIR-Lese-APIs aktiviert).   
- **Umgebungen im Gesundheitswesen:**  Elektronische Gesundheitsakten sind in vielen Klinikumgebungen meist die Hauptinformationsquelle. Es ist aber nicht ungewöhnlich, dass Anbieter über mehrere Datenbanken verfügen, die nicht miteinander verbunden sind, oder dass Daten in unterschiedlichen Formaten gespeichert werden.  Indem Sie Azure API for FHIR als übergeordneten Dienst für diese Systeme nutzen, können Sie Daten im FHIR-Format standardisieren.  Dies ermöglicht den Datenaustausch in einem einheitlichen Format über mehrere Systeme hinweg. 

- **Forschung:** Für Forschungspersonal im Gesundheitswesen stellen der allgemeine FHIR-Standard und Azure API for FHIR eine wertvolle Hilfe dar. Mit dem Dienst werden Daten basierend auf einem gemeinsamen FHIR-Datenmodell normalisiert, und die Arbeitsauslastung in Bezug auf maschinelles Lernen und den Datenaustausch wird verringert.
Beim Datenaustausch per Azure API for FHIR werden Überwachungsprotokolle und Zugriffssteuerungen bereitgestellt, um steuern zu können, wohin die Daten fließen und wer Zugriff auf welche Datentypen hat. 

## <a name="fhir-from-microsoft"></a>FHIR von Microsoft

FHIR-Funktionen von Microsoft sind in zwei Konfigurationen verfügbar:

* Azure API for FHIR: Ein PaaS-Angebot in Azure, das einfach im Azure-Portal bereitgestellt werden kann und von Microsoft verwaltet wird.
* FHIR-Server für Azure: Ein Open-Source-Projekt, das unter Ihrem Azure-Abonnement bereitgestellt werden kann. Sie finden es auf GitHub unter https://github.com/Microsoft/fhir-server.

Für Anwendungsfälle, bei denen der FHIR-Server erweitert oder angepasst werden muss oder der Zugriff auf zugrunde liegende Dienste, z. B. die Datenbank, erforderlich ist, ohne dass der Weg über die FHIR-APIs gewählt wird, sollten Entwickler die Open-Source-Version des FHIR-Servers für Azure wählen.   Für die Implementierung einer fertigen und für die Produktion geeigneten FHIR-API und eines Back-End-Diensts, bei der auf gespeicherte Daten nur über die FHIR-API zugegriffen werden sollte, sollten Entwickler Azure API for FHIR wählen.

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT-Konnektor für FHIR (Vorschauversion)

Azure IoT-Konnektor für FHIR&#174; (Fast Healthcare Interoperability Resources)* ist ein optionales Feature von Azure API for FHIR, das die Erfassung der Daten von IoMT-Geräten (Internet of Medical Things) ermöglicht. Internet of Medical Things (Internet der medizinischen Dinge) ist eine Kategorie von IoT-Geräten, die Gesundheits- und Befindlichkeitsdaten erfassen und über ein Netzwerk mit anderen IT-Systemen im Gesundheitswesen austauschen. Einige Beispiele für IoMT-Geräte sind Fitness- und klinische Wearables, Überwachungssensoren, Aktivitätstracker, Point-of-Care-Kioske oder sogar eine digitale Pille. Mit dem Feature für Azure IoT-Konnektor für FHIR können Sie schnell einen Dienst einrichten, um IoMT-Daten auf skalierbare, sichere und konforme Weise in Azure API for FHIR zu erfassen.

Azure IoT-Konnektor für FHIR kann alle JSON-basierten Nachrichten akzeptieren, die von einem IoMT-Gerät gesendet werden. Diese Daten werden zunächst in geeignete FHIR-basierte [Überwachungsressourcen](https://www.hl7.org/fhir/observation.html) transformiert und dann dauerhaft in Azure API for FHIR gespeichert. Die Datentransformationslogik wird durch ein Paar von Zuordnungsvorlagen definiert, die Sie basierend auf Ihrem Nachrichtenschema und Ihren FHIR-Anforderungen konfigurieren. Gerätedaten können direkt an Azure IoT-Konnektor für FHIR übermittelt oder nahtlos mit anderen Azure IoT-Lösungen ([Azure IoT Hub](../iot-hub/index.yml) und [Azure IoT Central](../iot-central/index.yml)) verwendet werden. Azure IoT-Konnektor für FHIR bietet eine sichere Datenpipeline und ermöglicht den Azure IoT-Lösungen, Bereitstellung und Wartung der physischen Geräte zu verwalten.

### <a name="applications-of-azure-iot-connector-for-fhir-preview"></a>Anwendungen von Azure IoT-Konnektor für FHIR (Vorschauversion)

Der Einsatz von IoMT-Geräten nimmt im Gesundheitswesen rapide zu, und Azure IoT-Konnektor für FHIR wurde entwickelt, um die Lücke zu schließen, damit Daten von mehreren Geräten auf sichere und konforme Weise in Azure API for FHIR eingebunden werden können. Das Einbinden von IoMT-Daten in einen FHIR-Server ermöglicht ganzheitliche Einblicke in Daten und innovative Arbeitsabläufe in Kliniken. Einige gängige Szenarien für Azure IoT-Konnektor für FHIR:
- **Remote-Patientenüberwachung/Telegesundheitssystem:** Die Remote-Patientenüberwachung bietet die Möglichkeit, Gesundheitsdaten von Patienten außerhalb der herkömmlichen Gegebenheiten des Gesundheitssystems zu erfassen. Institutionen des Gesundheitswesens können mithilfe von Azure IoT-Konnektor für FHIR Gesundheitsdaten, die von Remotegeräten generiert wurden, in Azure API for FHIR einbinden. Diese Daten können verwendet werden, um den Gesundheitszustand von Patienten genau zu verfolgen, die Einhaltung des Behandlungsplans durch die Patienten zu überwachen und eine personalisierte Versorgung zu bieten.
- **Forschung und Biowissenschaften:** In klinischen Studien werden IoMT-Geräte wie Biosensoren, Wearables und mobile Apps immer häufiger zur Erfassung von Versuchsdaten eingesetzt. Hierbei können mithilfe von Azure IoT-Konnektor für FHIR Gerätedaten auf sichere, effiziente und effektive Weise an Azure API for FHIR übertragen werden. Sobald die Versuchsdaten in Azure API for FHIR vorliegen, können sie für eine Echtzeitanalyse genutzt werden.
- **Erweiterte Analyse:** IoMT-Geräte können Daten in großen Mengen und aus einer Vielzahl von Quellen mit hoher Geschwindigkeit bereitstellen, wodurch sie sich hervorragend für die Bereitstellung von Trainings- und Testdaten für Machine Learning-Modelle eignen. Azure IoT-Konnektor für FHIR ist grundsätzlich auf eine große Bandbreite von Datenfrequenzen, flexible Datenschemas und Cloudskalierung mit geringer Latenz ausgelegt. Diese Eigenschaften machen Azure IoT-Konnektor für FHIR zu einer hervorragenden Wahl für die Erfassung von Gerätedaten für Ihre erweiterten Analyseanforderungen.
- **Intelligente Krankenhäuser/Kliniken:** Heute sind intelligente Krankenhäuser und Kliniken mit der Einrichtung einer Infrastruktur verbundener digitaler Ressourcen befasst. Mit Azure IoT-Konnektor für FHIR können Daten von diesen verbundenen Komponenten erfasst und integriert werden. Verwertbare Erkenntnisse aus einem solchen Dataset ermöglichen eine bessere Patientenversorgung und betriebliche Effizienz.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die fünfminütige Schnellstartanleitung für die Bereitstellung von Azure API for FHIR, um mit der Nutzung zu beginnen.

>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)

Wenn Sie Azure IoT-Konnektor für FHIR einmal ausprobieren möchten, sehen Sie sich den Schnellstart zum Bereitstellen von Azure IoT-Konnektor für FHIR über das Azure-Portal an.

>[!div class="nextstepaction"]
>[Schnellstart: Bereitstellen von IoT-Konnektor (Vorschauversion) mithilfe des Azure-Portals](iot-fhir-portal-quickstart.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet. 