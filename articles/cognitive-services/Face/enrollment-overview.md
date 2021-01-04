---
title: Übersicht über die Registrierung für die Gesichtserkennungs-API
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zum Prozess der Gesichtserkennungsregistrierung, um Benutzer bei einem Gesichtserkennungsdienst zu registrieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ac5106aa661cb2baea31ee15d57e9c6fac8c7192
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350329"
---
# <a name="face-api-enrollment"></a>Registrierung für die Gesichtserkennungs-API

Um die Gesichtserkennungs-API von Cognitive Services für die Gesichtsüberprüfung oder -identifizierung verwenden zu können, müssen Gesichter in einer **LargePersonGroup** registriert werden. In dieser ausführlichen Betrachtung erfahren Sie, wie Sie am besten eine sinnvolle Einwilligung von Benutzern einholen. Außerdem finden Sie hier Beispiellogik für die Erstellung hochwertiger Registrierungen, die zur Optimierung der Erkennungsgenauigkeit beitragen.  

## <a name="meaningful-consent"></a>Sinnvolle Einwilligung 

Eine der Hauptaufgaben einer Registrierungsanwendung für die Gesichtserkennung besteht darin, Benutzern die Möglichkeit zu geben, für bestimmte Zwecke in die Nutzung von Bildern ihres Gesichts einzuwilligen (etwa für den Zugang zu einer Arbeitsstätte). Gesichtserkennungstechnologien werden unter Umständen als Technologien zur Sammlung sensibler personenbezogener Daten betrachtet. Daher ist es besonders wichtig, auf transparente und respektvolle Weise eine Einwilligung einzuholen. Die Einwilligung ist für Benutzer sinnvoll, wenn sie die Möglichkeit erhalten, die Entscheidung zu treffen, die ihrer Meinung nach für sie am besten geeignet ist.   

Auf der Grundlage der Benutzeruntersuchungen und der Prinzipien für verantwortungsbewusste KI von Microsoft sowie anhand von [externen Untersuchungen](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf) sind wir zu dem Ergebnis gelangt, dass die Zustimmung sinnvoll ist, wenn sie Benutzern, die sich bei der Technologie registrieren, Folgendes bietet:

* Bewusstsein: Benutzer sollten keinerlei Zweifel haben, wenn sie aufgefordert werden, Ihre Gesichtsvorlage oder Registrierungsfotos bereitzustellen. 
* Verständnis: Benutzer sollten in der Lage sein, mit eigenen Worten genau zu beschreiben, wozu sie von wem zu welchem Zweck aufgefordert wurden und welche Zusicherungen dabei gegeben wurden. 
* Wahlfreiheit: Benutzer sollten bei der Entscheidung, ob sie in die Gesichtserkennung einwilligen und sich dafür registrieren möchten, nicht das Gefühl haben, unter Druck gesetzt oder manipuliert zu werden. 
* Kontrolle: Benutzer sollten jederzeit die Möglichkeit haben, ihre Einwilligung zu widerrufen und ihre Daten zu löschen. 

Dieser Abschnitt enthält hilfreiche Informationen zur Entwicklung einer Registrierungsanwendung für die Gesichtserkennung. Dieser Leitfaden wurde auf der Grundlage der Benutzeruntersuchungen von Microsoft im Zusammenhang mit der Gesichtserkennungsregistrierung von Einzelpersonen für den Gebäudezugang entwickelt. Daher sind diese Empfehlungen unter Umständen nicht für alle Gesichtserkennungslösungen geeignet. Der verantwortungsvolle Umgang mit der Gesichtserkennungs-API hängt stark vom jeweiligen Integrationskontext ab. Die Priorisierung und Anwendung dieser Empfehlungen muss daher an Ihr Szenario angepasst werden. 

> [!NOTE]
> Es liegt in Ihrer Verantwortung, dafür Sorge zu tragen, dass Ihre Registrierungsanwendung die für Sie geltenden rechtlichen Anforderungen erfüllt, und alle Ihre Datensammlungs- und Verarbeitungsverfahren korrekt darzustellen.

## <a name="application-development"></a>Anwendungsentwicklung 

Überlegen Sie sich vor dem Entwerfen eines Registrierungsflows, wie die von Ihnen erstellte Anwendung die Zusagen einhalten kann, die den Benutzern im Zusammenhang mit dem Schutz ihrer Daten gegeben werden. Die folgenden Empfehlungen können Ihnen dabei helfen, eine Registrierungsumgebung mit verantwortungsvollen Konzepten hinsichtlich des Schutzes personenbezogener Daten, der Verwaltung des Datenschutzes von Benutzern und des Anwendungszugriffs für alle Benutzer zu erstellen.  

|Kategorie | Empfehlungen |
|---|---|
|Hardware | Berücksichtigen Sie die Kameraqualität des Registrierungsgeräts. |
|Empfohlene Registrierungsfeatures | Schließen Sie einen Anmeldeschritt mit mehrstufiger Authentifizierung ein.</br></br>Verknüpfen Sie Benutzerinformationen wie einen Alias oder eine Identifikationsnummer mit der Gesichtsvorlagen-ID aus der Gesichtserkennungs-API (Personen-ID). Diese Zuordnung wird zum Abrufen und Verwalten der Registrierung eines Benutzers benötigt. Hinweis: Die Personen-ID muss in der Anwendung als Geheimnis behandelt werden.</br></br>Richten Sie einen automatisierten Prozess für die Löschung aller Registrierungsdaten ein – einschließlich der Gesichtsvorlagen und Registrierungsfotos von Personen, die die Gesichtserkennungstechnologie nicht mehr verwenden (beispielsweise ehemalige Mitarbeiter).</br></br>Verwenden Sie möglichst keine automatische Registrierung, da der Benutzer in diesem Fall nicht über das Bewusstsein, das Verständnis, die Wahlfreiheit und die Kontrolle verfügt, die für die Einholung der Einwilligung empfohlen werden. </br></br>Bitten Sie Benutzer, die Speicherung der für die Registrierung verwendeten Bilder zu erlauben. Dies ist im Falle einer Modellaktualisierung hilfreich, da für die erneute Registrierung bei dem neuen Modell neue Registrierungsfotos erforderlich sind (etwa alle zehn Monate). Werden die ursprünglichen Bilder nicht gespeichert, müssen die Benutzer den gesamten Registrierungsprozess erneut durchlaufen.</br></br>Geben Sie den Benutzern die Möglichkeit, sich gegen die Speicherung von Fotos im System zu entscheiden. Sie können einen zweiten Einwilligungsbildschirm für die Speicherung der Registrierungsfotos hinzufügen, um die Option deutlicher zu machen. </br></br>Falls Fotos gespeichert werden, erstellen Sie einen automatisierten Prozess, um alle Benutzer im Falle einer Modellaktualisierung erneut zu registrieren. Benutzer, die ihre Registrierungsfotos gespeichert haben, müssen sich nicht erneut registrieren. </br></br>Erstellen Sie ein App-Feature, das es designierten Administratoren ermöglicht, bestimmte Qualitätsfilter außer Kraft zu setzen, falls ein Benutzer Probleme bei der Registrierung hat. |
|Sicherheit | Von Cognitive Services werden Benutzerdaten im Ruhezustand und während der Übertragung mithilfe [bewährter Methoden](../cognitive-services-virtual-networks.md?tabs=portal) verschlüsselt. Im Anschluss finden Sie weitere Verfahren, die dazu beitragen können, die Sicherheitszusagen einzuhalten, die Benutzern im Rahmen der Registrierung gegeben werden. </br></br>Ergreifen Sie Sicherheitsmaßnahmen, um sicherzustellen, dass während der gesamten Registrierung niemand auf die Personen-ID zugreifen kann. Hinweis: Die Personen-ID (PersonID) muss im Registrierungssystem als Geheimnis behandelt werden. </br></br>Verwenden Sie für Cognitive Services die [rollenbasierte Zugriffssteuerung](../../role-based-access-control/overview.md). </br></br>Verwenden Sie für den Zugriff auf Ressourcen wie Datenbanken eine tokenbasierte Authentifizierung und/oder Shared Access Signatures (SAS) anstelle von Schlüsseln und Geheimnissen. Durch die Verwendung von Anforderungs- oder SAS-Token können Sie eingeschränkten Zugriff auf Daten gewähren, ohne Ihre Kontoschlüssel zu gefährden. Außerdem können Sie eine Ablaufzeit für das Token angeben. </br></br>Speichern Sie niemals Geheimnisse, Schlüssel oder Kennwörter in Ihrer App. |
|Datenschutz |Bieten Sie mehrere Registrierungsoptionen an, um unterschiedlich stark ausgeprägte Bedenken in puncto Datenschutz abzudecken. Schreiben Sie nicht vor, dass Benutzer für die Registrierung bei einem Gesichtserkennungssystem ihre privaten Geräte verwenden müssen. </br></br>Geben Sie Benutzern die Möglichkeit, sich jederzeit und aus einem beliebigen Grund erneut zu registrieren, ihre Einwilligung zu widerrufen und Daten aus der Registrierungsanwendung zu löschen. |
|Barrierefreiheit |Berücksichtigen Sie Barrierefreiheitsstandards (beispielsweise [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) oder [W3C](https://www.w3.org/TR/WCAG21/)), um sicherzustellen, dass die Anwendung von Personen mit Mobilitätseinschränkungen oder Sehschwäche verwendet werden kann. |

## <a name="next-steps"></a>Nächste Schritte  

Verwenden Sie den [Leitfaden zur Erstellung einer Registrierungs-App](build-enrollment-app.md), um sich anhand einer exemplarischen Registrierungs-App mit den ersten Schritten vertraut zu machen. Anschließend können Sie sie anpassen oder eine eigene App schreiben, die auf die Anforderungen Ihres Produkts zugeschnitten ist.