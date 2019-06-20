---
title: Häufige Szenarien mit Azure Data Catalog
description: Eine Übersicht über allgemeine Szenarien für Azure Data Catalog, einschließlich der Registrierung und Ermittlung von Datenquellen mit hoher Bedeutung, der Aktivierung von Self-Service-Business Intelligence und der Erfassung vorhandenen Wissens über Datenquellen und Prozesse.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e95cc64b9086a6fb4c5e2d42521a5fd3f44244ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61003955"
---
# <a name="azure-data-catalog-common-scenarios"></a>Häufige Szenarien mit Azure Data Catalog
In diesem Artikel werden übliche Szenarien vorgestellt, bei denen Azure Data Catalog Organisationen unterstützt, mehr Nutzen aus ihren vorhandenen Datenquellen zu ziehen.

## <a name="scenario-1-registration-of-central-data-sources"></a>Szenario 1: Registrierung zentraler Datenquellen
Organisationen verfügen häufig über Datenquellen mit hoher Bedeutung. Diese Datenquellen umfassen branchenspezifische OLTP-Systeme (Online Transaction Processing), Data Warehouses und Business Intelligence-/Analysedatenbanken. Die Anzahl der Systeme und die Überlappung zwischen Systemen nehmen im Laufe der Zeit zu, wenn sich die Anforderungen des Unternehmens ändern und sich das Unternehmen selbst aufgrund von beispielsweise Fusionen und Übernahmen weiterentwickelt.

Es kann für Mitglieder der Organisation schwierig zu erkennen sein, wo die Daten in diesen Datenquellen zu finden sind. Fragen wie die folgenden sind keine Seltenheit:

* Welches der drei Personalverwaltungssysteme, die im Unternehmen verwendet werden, soll ich zum Erstellen dieser Art von Bericht einsetzen?
* Wo finde ich die zertifizierten Verkaufszahlen für das gerade abgelaufene Geschäftsjahr?
* Wen soll ich fragen oder welchen Prozess soll ich verwenden, um Zugriff auf das Data Warehouse zu erhalten?
* Ich weiß nicht, ob diese Zahlen korrekt sind. Von wem erhalte ich Informationen dazu, wie diese Daten verwendet werden sollen, bevor ich dieses Dashboard für mein Team freigebe?

Auf diese und weitere Fragen bietet Azure Data Catalog Antworten. Die zentralen, besonders nützlichen und von IT-Experten verwalteten Datenquellen, die in Organisationen verwendet werden, sind häufig der logische Ausgangspunkt zum Auffüllen des Katalogs. Jeder Benutzer kann eine Datenquelle registrieren. Aber wenn der Katalog in Verbindung mit den Datenquellen eingeführt wird, die für die überwiegende Anzahl von Benutzern einen hohen Nutzen darstellen, fördert dies die Akzeptanz und Verwendung des Systems. 

Wenn Sie Azure Data Catalog neu einführen, kann das Identifizieren und Registrieren der wichtigsten Datenquellen, die von vielen unterschiedlichen Teams mit Datennutzern verwendet werden, der erste Schritt zum Erfolg sein.

Dieses Szenario bietet auch die Möglichkeit, die hochwertigen Datenquellen mit Anmerkungen zu versehen, damit diese einfacher zu verstehen sind und der Zugriff leichter ist. Ein wichtiger Aspekt dieses Aufwand ist es, Informationen darüber aufzunehmen, wie Benutzer Zugriff auf die Datenquelle anfordern können. Mit Azure Data Catalog können Sie die E-Mail-Adresse des Benutzers oder Teams, das für die Steuerung des Datenquellenzugriffs zuständig ist, Links zu vorhandenen Tools oder Dokumentation oder Freitext, der den Zugriffgewährungsprozess beschreibt, zur Verfügung zu stellen. Mit diesen Informationen können Benutzer, die registrierte Daten ermitteln, aber noch keine Berechtigungen zum Zugriff auf die Daten haben, den Zugriff einfach anfordern, indem Sie die von den Datenquellenbesitzern definierten und kontrollierten Prozesse befolgen.

## <a name="scenario-2-self-service-business-intelligence"></a>Szenario 2: Self-Service-Business Intelligence
Auch wenn herkömmliche Business Intelligence-Lösungen weiterhin wertvoller Bestandteil vieler Datenlandschaften von Unternehmen sind, hat die immer stärkere Beschleunigung der Geschäftstätigkeit dazu geführt, dass Self-Service-Business Intelligence immer wichtiger wird. Mithilfe von Self-Service-BI können Information-Worker und Analysten eigene Berichte, Arbeitsmappen und Dashboards erstellen, ohne dafür ein zentrales IT-Team in Anspruch nehmen zu müssen – bzw. durch die Zeitplanung und Verfügbarkeit des IT-Teams ausgebremst zu werden.

In Self-Service-BI-Szenarien kommt es häufig vor, dass Benutzer Daten aus mehreren Quellen kombinieren, von denen viele vorher noch nicht für BI und zur Analyse verwendet wurden. Obwohl einige dieser Datenquellen unter Umständen bereits bekannt sind, kann es schwierig sein zu ermitteln, welche Schritte ausgeführt werden müssen, um potenzielle Datenquellen für eine bestimmte Aufgabe zu finden und auszuwerten.

Dieser Ermittlungsprozess wird häufig noch manuell durchgeführt: Analysten nutzen Peer-zu-Peer-Netzwerkverbindungen, um andere Personen zu identifizieren, die mit den gewünschten Daten arbeiten. Nachdem eine Datenquelle gefunden und verwendet wurde, wiederholt sich der Prozess dann für jeden weiteren Self-Service-BI-Vorgang, wobei mehrere Benutzer einen redundanten manuellen Ermittlungsprozess durchführen.

Mit Azure Data Catalog kann Ihre Organisation diesen Kreislauf durchbrechen. Nachdem eine Datenquelle auf herkömmliche Weise ermittelt wurde, kann ein Analyst die Datenquelle registrieren, damit sie in Zukunft von anderen Benutzern leichter gefunden werden kann. Der Analyst kann den Nutzen weiter erhöhen, indem er die registrierten Datenobjekte mit Anmerkungen versieht. Dies muss aber nicht zum Zeitpunkt der Registrierung erfolgen. Benutzer können je nach Arbeitsaufwand im Laufe der Zeit ihren Beitrag leisten und den Nutzen der im Katalog registrierten Datenquellen nach und nach weiter steigern.

Dieses Anwachsen des Kataloginhalts stellt die natürliche Erweiterung nach der anfänglichen Registrierung der zentralen Datenquellen dar. Das Vorabauffüllen des Katalogs mit Daten, die von vielen Benutzern benötigt werden, kann im Hinblick auf die erste Nutzung und Ermittlungstätigkeit motivierend wirken. Wenn Benutzer weitere Quellen registrieren und mit Anmerkungen versehen können, werden sie und andere Mitarbeiter in der Organisation bei der Stange gehalten.

Beachten Sie, dass für umfangreiche BI-Projekte von Unternehmen die gleichen Muster und Anforderungen gelten, obwohl es bei diesem Szenario speziell um Self-Service-BI geht. Mithilfe von Data Catalog kann Ihre Organisation alle Schritte verbessern, bei denen eine manuelle Ermittlung von Datenquellen erfolgt.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Szenario 3: Erfassen von vorhandenem Wissen
Woher wissen Sie, welche Daten Sie für Ihre Arbeit benötigen und wo Sie diese Daten finden?

Wenn Sie Ihren Job schon etwas länger machen, ist Ihnen dies wahrscheinlich bekannt. Sie haben einen allmählichen Lernprozess durchlaufen und gelernt, welche Datenquellen für Ihre tägliche Arbeit wichtig sind.

Wenn Ihr Team einen neuen Mitarbeiter erweitert wird, stellt sich die Frage, woher diese Person weiß, welche Daten sie benötigt und wo sie zu finden sind.

Wahrscheinlich kommt der neue Mitarbeiter mit diesen Fragen zu Ihnen.

Diese ständige Weitergabe vorhandenen Wissens ist in großen und kleinen Unternehmen Teil des Ermittlungsprozesses von Datenquellen. Länger angestellte und erfahrenere Teammitglieder haben sich ihr Wissen im Laufe der Jahre aufgebaut, und neuere Teammitglieder haben gelernt, dass sie sich mit Fragen an diese Kollegen wenden können. Die wichtigsten Informationen sind häufig nur in den Köpfen weniger zentraler Personen vorhanden, und wenn diese Personen im Urlaub sind oder das Team verlassen, wirkt sich dies für das Unternehmen negativ aus.

Datenexperten dokumentieren ihr Wissen üblicherweise und teilen es per E-Mail oder in Word-Dokumenten auf einer SharePoint-Teamwebsite mit anderen. Diese Vorgehensweise ist zwar hilfreich, aber damit werden gleichzeitig neue Ermittlungsprobleme geschaffen: Woher wissen Mitarbeiter, welche Dokumentation vorhanden ist und wo sie sich befindet?

Mit Azure Data Catalog verfügt Ihre Organisation über einen zentralen Speicherort zum Speichern und Freigeben dieses vorhandenen Wissens, in dem es einfach gefunden werden kann. In Data Catalog können Datenexperten Datenobjekte mit Anmerkungen versehen und Links zur vorhandenen Dokumentation einfügen. Wenn Mitglieder der Organisation den Katalog nutzen, um eine Datenquelle zu ermitteln, finden sie nicht nur die Quelle selbst, sondern auch das Wissen, dass zuvor nur in den Köpfen der Experten in Ihrer Organisation vorhanden war.
