---
title: Einführung in Azure Purview (Vorschau)
description: Dieser Artikel enthält eine Übersicht über Azure Purview, einschließlich der Features des Diensts und der Probleme, die er beheben soll. Azure Purview ermöglicht Benutzern das Registrieren, Ermitteln, Verstehen und Nutzen von Datenquellen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 77da3406897c31e48d808541f1f9d579eb0a9cb7
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401541"
---
# <a name="what-is-azure-purview"></a>Was ist Azure Purview?

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Azure Purview ist ein einheitlicher Datengovernancedienst, der Sie bei der Verwaltung und Steuerung von lokalen Daten, von Daten in mehreren Clouds sowie von SaaS-Daten (Software-as-a-Service) unterstützt. Erstellen Sie mühelos eine ganzheitliche, aktuelle Übersicht über Ihre Datenlandschaft – mit automatisierter Datenermittlung, Klassifizierung vertraulicher Daten und End-to-End-Datenherkunft. Unterstützen Sie Datenconsumer bei der Suche nach nützlichen, verlässlichen Daten.

Azure Purview Data Map bildet die Grundlage für die Datenermittlung und eine effektive Datengovernance. Purview Data Map ist ein cloudnativer PaaS-Dienst, der Metadaten zu Unternehmensdaten in Analyse- und Betriebssystemen erfasst – sowohl lokal als auch in der Cloud. Durch das integrierte automatisierte Überprüfungs- und Klassifizierungssystem wird Purview Data Map automatisch auf dem neuesten Stand gehalten. Geschäftsbenutzer können Purview Data Map über eine intuitive Benutzeroberfläche konfigurieren und verwenden, und Entwickler können mithilfe Open-Source-basierter Apache Atlas 2.0-APIs programmgesteuert mit Data Map interagieren.

Azure Purview Data Map bildet die Grundlage für Purview Data Catalog und Purview-Datenerkenntnisse als einheitliche Umgebungen innerhalb von Purview Studio.
 
Mit Purview Data Catalog können sowohl Geschäftsbenutzer als auch technische Benutzer schnell und einfach relevante Daten finden. Hierzu steht ihnen eine Suchfunktion mit Filtern zur Verfügung, die auf verschiedenen Eingrenzungsoptionen wie Glossarbegriffen, Klassifizierungen, Vertraulichkeitsbezeichnungen und Ähnlichem basieren. Für fachliche Ansprechpartner, Data Stewards und Datenverantwortliche bietet Purview Data Catalog Datenkuratierungsfeatures wie etwa die Verwaltung von Unternehmensglossaren und die Möglichkeit, die Markierung von Datenassets mit Glossarbegriffen zu automatisieren. Datenconsumer und Datenproduzenten können zudem die Herkunft von Datenassets visuell nachverfolgen – von den lokalen Betriebssystemen über Bewegungen, Transformationen und Anreicherungen mit verschiedenen cloudbasierten Systemen zur Datenspeicherung und -verarbeitung bis hin zur Nutzung in einem Analysesystem wie Power BI.

Anhand der Purview-Datenerkenntnisse können sich Datenverantwortliche und Sicherheitsbeauftragte einen Überblick verschaffen und auf einen Blick erkennen, welche Daten überprüft werden, wo sich vertrauliche Daten befinden und wie sie bewegt werden.

## <a name="discovery-challenges-for-data-consumers"></a>Aufgaben der Ermittlung für Datennutzer

Das Auffinden von Unternehmensdatenquellen war bisher ein organischer Prozess, der auf gemeinschaftlichem Wissen basierte. Unternehmen, die aus ihren Informationsressourcen den größtmöglichen Nutzen ziehen möchten, stellt dieses Konzept vor eine Vielzahl von Herausforderungen:

* Da es keinen zentralen Ort für die Registrierung von Datenquellen gibt, wissen Benutzer unter Umständen erst, dass eine Datenquelle existiert, wenn sie im Rahmen eines anderen Prozesses darauf stoßen.
* Wenn Benutzer nicht wissen, wo sich eine Datenquelle befindet, können sie damit keine Verbindung über eine Clientanwendung herstellen. Um die Daten nutzen zu können, müssen Benutzer die Verbindungszeichenfolge oder den Pfad kennen.
* Wenn Benutzer nicht wissen, wo sich die Dokumentation einer Datenquelle befindet, wissen sie nicht, wie die Daten verwendet werden sollen. Datenquellen und Dokumentationen befinden sich möglicherweise an mehreren Orten und können auf unterschiedliche Weise genutzt werden.
* Wenn Benutzer Fragen zu einem Informationsobjekt haben, müssen sie den Experten oder das Team suchen, der bzw. das für die Daten verantwortlich ist, und sich offline an diese Personen wenden. Es gibt keine explizite Verbindung zwischen Daten und den Experten für deren Verwendung.
* Ein Benutzer muss wissen, wie er Zugriff auf die Datenquelle anfordern kann, da er mit der ermittelten Datenquelle und der zugehörigen Dokumentation allein noch nicht auf die Daten zugreifen kann.

## <a name="discovery-challenges-for-data-producers"></a>Aufgaben der Ermittlung für Datenproduzenten

Trotz der genannten Hürden für die Nutzer der Daten gelten für Benutzer, die für die Erstellung und Verwaltung von Informationsobjekten verantwortlich sind, besondere Anforderungen:

* Das Erstellen von Anmerkungen zu Datenquellen mit beschreibenden Metadaten ist häufig nutzlos. Clientanwendungen ignorieren in der Regel Beschreibungen, die in der Datenquelle gespeichert sind.
* Die Erstellung einer Dokumentation für Datenquellen ist nicht immer einfach, und die Dokumentation muss kontinuierlich auf die Datenquellen abgestimmt werden. Wirkt eine Dokumentation veraltet, verlieren die Benutzer möglicherweise das Vertrauen.
* Das Erstellen und Verwalten der Dokumentation für eine Datenquelle ist komplex und zeitaufwendig. Dies gilt mitunter noch stärker für die Aufgabe, die Dokumentation für alle Personen, die die Datenquelle verwenden, leicht zugänglich zu machen.
* Eine beständige Herausforderung besteht darin, den Zugriff auf die Datenquelle zu beschränken und sicherzustellen, dass die Datennutzer wissen, wie sie den Zugriff anfordern.

Zusammen stellen diese Probleme eine erhebliche Hürde für Unternehmen dar, die die Nutzung und das Verständnis der Unternehmensdaten fördern möchten.

## <a name="discovery-challenges-for-security-administrators"></a>Herausforderungen für Sicherheitsadministratoren im Zusammenhang mit der Ermittlung

Benutzer, die für die Sicherheit der Daten ihrer Organisation verantwortlich sind, sind neben den oben aufgeführten Herausforderungen für Datenconsumer und Datenproduzenten unter Umständen auch mit folgenden Herausforderungen konfrontiert:

* Der Datenbestand einer Organisation wächst kontinuierlich, und die Daten werden gespeichert und in neue Richtungen weitergegeben. Die Ermittlung, der Schutz und die Steuerung Ihrer vertraulichen Daten sind eine Daueraufgabe. Sie müssen sicherstellen, dass die Inhalte Ihrer Organisation mit den korrekten Berechtigungen an die richtigen Personen und Anwendungen weitergegeben werden.
* Um die Risikostufen in den Daten Ihrer Organisation besser zu verstehen, müssen Sie sich intensiv mit Ihren Inhalten beschäftigen und nach Schlüsselwörtern, RegEx-Mustern und/oder vertraulichen Datentypen Ausschau halten. Vertrauliche Daten können beispielsweise Kreditkartennummern, Sozialversicherungsnummern oder Bankkontonummern sein. Sie müssen ständig sämtliche Datenquellen auf vertrauliche Inhalte überwachen, da selbst der kleinste Datenverlust für Ihre Organisation kritisch sein kann.
* Die kontinuierliche Einhaltung der Unternehmenssicherheitsrichtlinien Ihrer Organisation ist eine anspruchsvolle Aufgabe, da nicht nur Ihre Inhalte ständig zunehmen und sich verändern, sondern auch die entsprechenden Anforderungen und Richtlinien an die sich verändernden digitalen Gegebenheiten angepasst werden. Sicherheitsadministratoren sind häufig dafür zuständig, schnellstmöglich für Datensicherheit zu sorgen.

## <a name="azure-purview-advantages"></a>Vorteile von Azure Purview

Azure Purview wurde für die Lösung der Probleme aus den vorherigen Abschnitten konzipiert und unterstützt Unternehmen bei der optimalen Nutzung ihrer bereits vorhandenen Informationsressourcen. Der Katalog macht Datenquellen für Benutzer, die die Daten verwalten, einfach auffindbar und leichter verständlich.

Azure Purview bietet einen cloudbasierten Dienst für die Registrierung von Datenquellen. Während der Registrierung verbleiben die Daten an ihrem Speicherort. In Azure Purview werden jedoch eine Kopie der Metadaten sowie ein Verweis auf den Ort der Datenquelle hinzugefügt. Die Metadaten werden außerdem indiziert, damit jede Datenquelle per Suchfunktion einfach ermittelt werden kann und für die Benutzer, die sie ermitteln, verständlich ist.

Nachdem Sie eine Datenquelle registriert haben, können Sie deren Metadaten anreichern. Die Metadaten werden entweder von dem Benutzer, der die Datenquelle registriert hat, ober von einem anderen Benutzer im Unternehmen hinzugefügt. Alle Benutzer können eine Datenquelle mit Anmerkungen versehen, indem sie Beschreibungen, Tags oder andere Metadaten zum Anfordern des Datenquellenzugriffs bereitstellen. Diese beschreibenden Metadaten erweitern die strukturellen Metadaten wie Spaltennamen und Datentypen, die aus der Datenquelle registriert wurden.

Das Ermitteln und Verstehen von Datenquellen und ihrer Nutzung sind der Hauptzweck bei der Registrierung von Quellen. Enterprisebenutzer benötigen möglicherweise Daten für Business Intelligence, Anwendungsentwicklung, Data Science oder andere Aufgaben, für die spezifische Daten erforderlich sind. Über die Datenkatalogermittlung können sie schnell die ihren Anforderungen entsprechenden Daten suchen, die Daten verstehen, damit sie ihre Eignung für den jeweiligen Zweck einordnen können, und die Daten nutzen, indem sie die Datenquelle im Tool ihrer Wahl öffnen.

Gleichzeitig können Benutzer Beiträge zum Katalog leisten, indem sie bereits registrierte Datenquellen mit Tags versehen, dokumentieren und kommentieren. Außerdem können sie neue Datenquellen registrieren, die von der Community der Katalogbenutzer gefunden, verstanden und genutzt werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Purview finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
