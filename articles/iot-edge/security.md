---
title: Sicherheitsframework – Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Sicherheits-, Authentifizierungs- und Autorisierungsstandards, die bei der Entwicklung von Azure IoT Edge verwendet wurden und die Sie bei der Entwicklung Ihrer Lösung berücksichtigen sollten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8aadddbc9ae13a87f89db4d7e7189ea7aa8aeef5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60612026"
---
# <a name="security-standards-for-azure-iot-edge"></a>Sicherheitsstandards für Azure IoT Edge

Azure IoT Edge wurde entwickelt, um die Risikoszenarien zu behandeln, die mit dem Verschieben Ihrer Daten und Analysen zur Intelligent Edge verbunden sind. Die IoT Edge-Sicherheitsstandards bieten Flexibilität für verschiedene Risikoprofile und Bereitstellungsszenarien, aber dabei den Schutz bieten, den Sie von allen Azure-Diensten erwarten. 

Azure IoT Edge kann auf verschiedener Hardware unterschiedlicher Hersteller und Modelle ausgeführt wurden, unterstützt verschiedene Betriebssysteme und eignet sich für verschiedene Bereitstellungsszenarien. Bei der Risikobewertung eines Bereitstellungsszenarios spielen verschiedene Faktoren eine Rolle, z.B. der Besitz der Lösung, die geografische Region für die Bereitstellung, die Vertraulichkeit der Daten, die Branche, in der die Anwendung eingesetzt wird, sowie gesetzliche Vorgaben. Anstatt konkrete Lösungen für bestimmte Szenarien anzubieten, stellt IoT Edge ein erweiterbares Sicherheitsframework dar, das auf fundierten Prinzipien fußt und Skalierbarkeit bietet. 
 
Dieser Artikel bietet eine Übersicht über das IoT Edge-Sicherheitsframework. Weitere Informationen finden Sie unter [Sicherheit für Intelligent Edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standards

Standards fördern eine einfache Überprüfbarkeit und Implementierung – zwei Eckpfeiler der Sicherheit. Eine Sicherheitslösung sollte zur Überprüfbarkeit beitragen, um Vertrauen aufzubauen, und sie darf kein Hindernis bei der Bereitstellung sein. Das Design des Frameworks für den Schutz von Azure IoT Edge basiert auf Sicherheitsprotokollen, die über lange Zeit getestet wurden und sich in der Branche bewährt haben, und bietet somit Vertrautheit und Wiederverwendbarkeit. 

## <a name="authentication"></a>Authentication

Wenn Sie eine IoT-Lösung bereitstellen, muss gewährleistet sein, dass nur vertrauenswürdige Akteure, Geräte und Module Zugriff auf Ihre Lösung haben. Dieses Wissen sorgt für Verantwortlichkeit bei den Beteiligten. Azure IoT Edge erzielt dieses Wissen durch Authentifizierung. Die zertifikatsbasierte Authentifizierung ist der primäre Authentifizierungsmechanismus für die Azure IoT Edge-Plattform. Dieser Mechanismus wurde aus einer Reihe von Standards abgeleitet, die die Public Key Infrastructure (PKiX) der Internet Engineering Task Force (IETF) regeln.     

Alle Geräte, Module und Akteure, die mit dem Azure IoT Edge-Gerät interagieren – physisch oder über eine Netzwerkverbindung –, müssen eindeutige Zertifikatidentitäten aufweisen. Nicht jedes Szenario und jede Komponente ist jedoch für die zertifikatbasierte Authentifizierung geeignet. In diesen Szenarien bietet die Erweiterbarkeit des Sicherheitsframeworks sichere Alternativen. 

## <a name="authorization"></a>Autorisierung

Das Prinzip der geringsten Rechte besagt, dass Benutzer und Komponenten eines Systems nur Zugriff auf den minimalen Satz von Ressourcen und Daten haben sollten, die zum Ausüben ihrer Rollen erforderlich sind. Geräte, Module und Akteure sollten nur auf Ressourcen und Daten in ihrem Berechtigungsbereich zugreifen – und auch nur dann, wenn die Architektur es zulässt. Einige Berechtigungen sind mit ausreichenden Rechten konfigurierbar, andere dagegen werden architekturbedingt erzwungen.  Beispielsweise kann ein Modul per Berechtigungskonfiguration autorisiert werden, eine Verbindung mit Azure IoT Hub zu initiieren. Es besteht jedoch kein Grund für ein Modul in einem Azure IoT Edge-Gerät, auf den Zwilling eines Moduls in einem anderen Azure IoT Edge-Gerät zuzugreifen.

Weitere Autorisierungskonzepte umfassen Rechte zum Signieren von Zertifikaten und die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). 

## <a name="attestation"></a>Nachweis

Ein Nachweis stellt die Integrität von Softwarebestandteilen sicher.  Er ist wichtig für die Erkennung und Verhinderung von Schadsoftware.  Das Azure IoT Edge-Sicherheitsframework klassifiziert Nachweise in drei Hauptkategorien:

* Statischer Nachweis
* Nachweis zur Laufzeit
* Softwarenachweis

### <a name="static-attestation"></a>Statischer Nachweis

Beim statischen Nachweis wird die Integrität aller Softwarebestandteile bestätigt, darunter die des Betriebssystems, aller Runtimes und der Konfigurationsinformationen beim Gerätestart. Er wird oft als sicherer Start bezeichnet. Das Sicherheitsframework für Azure IoT Edge-Geräte wird auch von Herstellern verwendet. Es schließt sichere Hardwarefeatures ein, die statische Nachweisprozesse sicherstellen. Zu diesen Prozessen gehören sicherer Gerätestart und sichere Firmwareupgrades.  Eine enge Zusammenarbeit mit Hardwareherstellern verhindert überflüssige Firmwareschichten und minimiert damit die Angriffsfläche. 

### <a name="runtime-attestation"></a>Nachweis zur Laufzeit

Nachdem ein System einen validierten Startprozess abgeschlossen hat und betriebsbereit ist, erkennen sorgfältig entwickelte Sicherheitssysteme Versuche, Schadsoftware einzuschleusen, und leiten entsprechende Gegenmaßnahmen ein. Angriffe durch Schadsoftware können etwa auf die Ports und Schnittstellen des Systems abzielen, um Zugriff auf das System zu erhalten. Wenn böswillige Akteure physischen Zugriff auf ein Gerät haben, können sie auf ähnliche Weise das Gerät direkt manipulieren oder über Seitenkanalangriffe Zugriff erhalten. Solche schädlichen Inhalte können die Form von Schadsoftware oder nicht autorisierten Konfigurationsänderungen annehmen. Sie werden nach dem Startvorgang eingeschleust, sodass ein statischer Nachweis sie nicht erkennen kann. Gegenmaßnahmen, die von der Gerätehardware angeboten oder erzwungen werden, tragen zur Abwehr solcher Bedrohungen bei.  Das Sicherheitsframework für Azure IoT Edge fordert ausdrücklich Erweiterungen zur Bekämpfung von Laufzeitbedrohungen.  

### <a name="software-attestation"></a>Softwarenachweis

Alle fehlerfreien Systeme einschließlich intelligenter Edgesysteme müssen Patches und Upgrades akzeptieren.  Sicherheit ist wichtig für Updateprozesse, da diese sonst potenzielle Bedrohungsvektoren sein können.  Das Sicherheitsframework für Azure IoT Edge fordert, dass Updates über bewertete und signierte Pakete erfolgen, um die Integrität sicherzustellen und die Quelle der Pakete zu authentifizieren.  Dieser Standard gilt für alle Betriebssysteme und Bestandteile von Softwareanwendungen. 

## <a name="hardware-root-of-trust"></a>Vertrauensanker in Hardware

Bei vielen intelligenten Edgegeräten, insbesondere Geräten an Orten, an denen potenziell böswillige Akteure physischen Zugriff auf das Gerät haben, ist die Sicherheit auf Gerätehardwareebene die letzte Verteidigungslinie. Manipulationssichere Hardware ist für solche Bereitstellungen ausschlaggebend. Azure IoT Edge ermuntert Anbieter von sicherer Hardware, verschiedene Arten von Hardware-Vertrauensankern für unterschiedliche Risikoprofile und Bereitstellungsszenarien anzubieten. Das Vertrauen in Hardware kann von allgemeinen Sicherheitsprotokollstandards wie dem Trusted Platform Module (ISO/IEC 11889) oder der Device Identifier Composition Engine (DICE) der Trusted Computing Group stammen. Auch sichere Enklaventechnologien wie TrustZones und Software Guard Extensions (SGX) stellen Vertrauen in Hardware her. 

## <a name="certification"></a>Zertifizierung

Damit Kunden beim Erwerb von Azure IoT Edge-Geräten fundierte Entscheidungen für ihre Bereitstellung treffen können, enthält das Azure IoT Edge-Framework Zertifizierungsanforderungen.  Die Grundlage dieser Anforderungen sind Zertifizierungen in Bezug auf Sicherheitserfordernisse und Zertifizierungen in Bezug auf die Validierung der Sicherheitsimplementierung.  Eine Zertifizierung in Bezug auf Sicherheitserfordernisse informiert beispielsweise darüber, dass für das IoT Edge-Gerät sichere Hardware eingesetzt wird, die Startangriffe bekanntermaßen abwehrt. Eine Validierungszertifizierung informiert darüber, dass die sichere Hardware ordnungsgemäß implementiert wurde, sodass dieses Feature im Gerät wirklich genutzt werden kann.  Im Einklang mit dem Prinzip der Einfachheit ist das Framework darauf ausgelegt, den Aufwand für Zertifizierungen so gering wie möglich zu halten.   

## <a name="extensibility"></a>Erweiterbarkeit

Da die IoT-Technologie verschiedene Formen von geschäftlichen Transformationen fördert, leuchtet es ein, dass Sicherheitsmaßnahmen parallel weiterentwickelt werden sollten, um neue Szenarien abzudecken.  Das Azure IoT Edge-Sicherheitsframework startet mit einem soliden Fundament, auf dem die Erweiterbarkeit in verschiedene Dimensionen aufgebaut wird, damit folgende Aspekte berücksichtigt werden: 

* Sicherheitsdienste des Erstanbieters, wie etwa den Device Provisioning-Dienst für Azure IoT Hub.
* Drittanbieterdienste wie z.B. verwaltete Sicherheitsdienste für verschiedene Branchen (z.B. Industrie oder Gesundheitswesen) oder technologische Fokusbereiche (z.B. Sicherheitsüberwachung in Meshnetzwerken oder Hardwarenachweisdienste) über ein umfassendes Partnernetzwerk.
* Ältere Systeme, die mit alternativen Sicherheitsstrategien umgerüstet werden müssen, indem z.B. eine andere Sicherheitstechnologie als Zertifikate für die Authentifizierung und Identitätsverwaltung verwendet wird.
* Sichere Hardware für die Einführung neuer sicherer Hardwaretechnologien und Beiträge von Hardwarepartnern.

Schlussendlich resultiert der größte Erfolg beim Sichern des intelligenten Edge aus gemeinsamen Beiträgen einer offenen Community, die vom gemeinsamen Interesse an der Sicherheit des Internets der Dinge angetrieben wird.  Bei diesen Beiträgen kann es sich um sichere Technologien oder sichere Dienste handeln.  Das Azure IoT Edge-Sicherheitsframework bietet ein solides Fundament für die Sicherheit, das erweiterbar ist, um im intelligenten Edge das gleiche Maß an Vertrauen und Integrität bereitzustellen wie in der Azure-Cloud.  

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure IoT Edge unter [Sicherheit für Intelligent Edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
