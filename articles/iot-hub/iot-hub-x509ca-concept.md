---
title: Konzepte der Azure IoT Hub-X.509-Sicherheit | Microsoft-Dokumentation
description: Konzept – Verstehen des Nutzens von X.509-Zertifizierungsstellenzertifikaten bei der Fertigung und Authentifizierung von IoT-Geräten
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490719"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Konzeptgrundlagen der X.509-Zertifizierungsstellenzertifikate in der IoT-Branche

In diesem Artikel wird beschrieben, wie nützlich es ist, X.509-Zertifizierungsstellenzertifikaten bei der Fertigung und IoT Hub-Authentifizierung von IoT-Geräten zu verwenden. Er enthält Informationen zur Einrichtung der Lieferkette und zu den Vorteilen.

Dieser Artikel beschreibt Folgendes:

* Grundlagen und Abruf von X.509-Zertifizierungsstellenzertifikaten

* Registrieren Ihres X.509-Zertifizierungsstellenzertifikats für IoT Hub

* Einrichten einer Fertigungslieferkette für die Authentifizierung basierend auf X.509-Zertifizierungsstellenzertifikaten

* Verbinden von Geräten, die mit X.509-Zertifizierungsstellenzertifikaten signiert sind, mit IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Übersicht

Die Authentifizierung per X.509-Zertifizierungsstelle (Certificate Authority, CA) ist ein Ansatz zum Authentifizieren von Geräten für IoT Hub. Hierbei wird eine Methode verwendet, mit der die Erstellung von Geräteidentitäten und die Lebenszyklusverwaltung in der Lieferkette erheblich vereinfacht werden.

Ein Unterscheidungsmerkmal der Authentifizierung per X.509-Zertifizierungsstelle ist eine 1:n-Beziehung eines Zertifizierungsstellenzertifikats mit nachgelagerten Geräten. Diese Beziehung ermöglicht die Registrierung einer beliebigen Anzahl von Geräten in IoT Hub, indem ein X.509-Zertifizierungsstellenzertifikat einmalig registriert wird. Andernfalls müssen eindeutige Gerätezertifikate für jedes Gerät vorab registriert werden, bevor ein Gerät eine Verbindung herstellen kann. Diese 1: n-Beziehung vereinfacht auch die Durchführung von Vorgängen zur Lebenszyklusverwaltung für Gerätezertifikate.

Ein weiteres wichtiges Attribut der Authentifizierung per X.509-Zertifizierungsstelle ist die Vereinfachung der Lieferkettenlogistik. Für die sichere Authentifizierung von Geräten ist es erforderlich, dass jedes Gerät über ein eindeutiges Geheimnis, z.B. einen Schlüssel, als Grundlage für die Vertrauensstellung verfügt. Bei der zertifikatbasierten Authentifizierung ist dieses Geheimnis ein privater Schlüssel. Ein typischer Ablauf zur Fertigung eines Geräts umfasst mehrere Schritte und Verwalter. Das sichere Verwalten von privaten Schlüsseln für Geräte über mehrere Verwalter hinweg und das Aufrechterhalten der Vertrauensstellung ist schwierig und kostenintensiv. Die Nutzung von Zertifizierungsstellen löst dieses Problem, indem jeder Verwalter für eine kryptografische Vertrauenskette signiert wird, anstatt private Geräteschlüssel zu verwenden. Jeder Verwalter signiert wiederum Geräte für den jeweiligen Prozessschritt des Fertigungsablaufs. Das Gesamtergebnis ist eine optimale Lieferkette mit integrierter Zurechenbarkeit dank des Einsatzes einer kryptografischen Vertrauenskette. Hierbei ist der Hinweis wichtig, dass bei diesem Prozess die höchste Sicherheit erzielt wird, wenn Geräte ihre eigenen privaten Schlüssel schützen. Zu diesem Zweck empfehlen wir die Verwendung von Hardwaresicherheitsmodulen (HSM), mit denen intern private Schlüssel generiert werden können, die immer im Hintergrund bleiben.

Dieser Artikel enthält einen umfassenden Überblick über die Verwendung der Authentifizierung per X.509-Zertifizierungsstelle von der Einrichtung der Lieferkette bis zur Verbindungsherstellung für das Gerät. Zur Erleichterung des Verständnisses ist ein Beispiel aus der Praxis angegeben.

Sie können auch Registrierungsgruppen mit dem Azure IoT Hub Device Provisioning-Dienst (DPS) verwenden, um die Bereitstellung von Geräten für Hubs durchzuführen. Weitere Informationen zum Bereitstellen von X.509-Zertifikatgeräten mithilfe von DPS finden Sie unter [Tutorial: Bereitstellen mehrerer X.509-Geräte mit Registrierungsgruppen](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="introduction"></a>Einführung

Das X.509-Zertifizierungsstellenzertifikat ist ein digitales Zertifikat, dessen Zertifikatinhaber andere Zertifikate signieren kann. Es ist ein digitales X.509-Zertifikat, da es die Vorgaben eines Zertifikatformatierungsstandards gemäß RFC 5280-Standard der IETF erfüllt, und es handelt sich um eine Zertifizierungsstelle (Certificate Authority, CA), da der Inhaber andere Zertifikate signieren kann.

Die Verwendung der X.509-Zertifizierungsstelle lässt sich am besten anhand eines konkreten Beispiels erläutern. Beispiel: Company-X, ein Hersteller von Smart-X-Widgets für die professionelle Installation. Company-X outsourct sowohl die Fertigung als auch die Installation. Das Unternehmen nutzt den Hersteller Factory-Y für die Fertigung der Smart-X-Widgets und den Dienstanbieter Technician-Z für die Installation. Company-X möchte, dass Smart-X-Widgets für die Installation direkt von Factory-Y an Technician-Z geliefert werden und dass nach der Installation dafür eine direkte Verbindung mit der IoT Hub-Instanz von Company-X hergestellt wird, ohne dass Company-X weiter eingreifen muss. Hierzu muss Company-X einige einmalige Einrichtungsvorgänge durchführen, um Smart-X-Widgets für die automatische Verbindungsherstellung vorzubereiten. Basierend auf dem gesamten Szenario ist der restliche Teil dieses Artikels wie folgt strukturiert:

* Beschaffen des X.509-Zertifizierungsstellenzertifikats

* Registrieren des X.509-Zertifizierungsstellenzertifikats bei IoT Hub

* Anmelden von Geräten an einer Zertifikatvertrauenskette

* Geräteverbindung

## <a name="acquire-the-x509-ca-certificate"></a>Beschaffen des X.509-Zertifizierungsstellenzertifikats

Company-X hat die Möglichkeit, ein X.509-Zertifizierungsstellenzertifikat von einer öffentlichen Stammzertifizierungsstelle zu erwerben oder es anhand eines selbstsignierten Prozesses zu erstellen. Je nach Anwendungsszenario kann die eine oder die andere Vorgehensweise besser geeignet sein. Unabhängig von der gewählten Option umfasst der Prozess zwei grundlegende Schritte: das Generieren eines Schlüsselpaars (öffentlich/privat) und das Signieren des öffentlichen Schlüssels für ein Zertifikat.

![Ablauf zum Generieren eines X.509-Zertifizierungsstellenzertifikats](./media/iot-hub-x509ca-concept/csr-flow.png)

Die Details zur Ausführung dieser Schritte unterscheidet sich je nach Dienstanbieter.

### <a name="purchasing-an-x509-ca-certificate"></a>Erwerben eines X.509-Zertifizierungsstellenzertifikats

Der Erwerb eines Zertifizierungsstellenzertifikats hat den Vorteil, dass eine bekannte Stammzertifizierungsstelle als vertrauenswürdiger Drittanbieter fungiert, um bei der Verbindungsherstellung der Geräte die Legitimität von IoT-Geräten zu bestätigen. Company-X würde diese Option wählen, wenn Smart-X-Widgets nach der ersten Verbindungsherstellung mit IoT Hub mit Produkten oder Diensten von Drittanbietern interagieren sollen.

Zum Erwerben eines X.509-Zertifizierungsstellenzertifikats würde Company-X einen Dienstanbieter von Stammzertifikaten wählen. Sie erhalten gute Ergebnisse, wenn Sie im Internet nach „Root CA“ (Stammzertifizierungsstelle) suchen. Die Stammzertifizierungsstelle weist Company-X an, wie das Schlüsselpaar (öffentlich/privat) erstellt und eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) für die Dienste generiert wird. Eine Zertifikatsignieranforderung wird für die formale Bewerbung für ein Zertifikat bei einer Zertifizierungsstelle benötigt. Das Ergebnis dieses Erwerbsvorgangs ist ein Zertifikat zur Verwendung als Zertifizierungsstellenzertifikat. Aufgrund der häufigen Verwendung von X.509-Zertifikaten ist die Wahrscheinlichkeit hoch, dass das Zertifikat richtig gemäß RFC 5280-Standard der IETF formatiert ist.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Erstellen eines selbstsignierten X.509-Zertifizierungsstellenzertifikats

Der Prozess zur Erstellung eines selbstsignierten X.509-Zertifizierungsstellenzertifikats ähnelt dessen Erwerb, aber dabei ist ein Drittanbieter, z. B. die Stammzertifizierungsstelle, als Signaturgeber beteiligt. In unserem Beispiel signiert Company-X sein Zertifizierungsstellenzertifikat selbst ohne Beteiligung einer Stammzertifizierungsstelle. Company-X kann diese Option zu Testzwecken wählen, bis alles für den Erwerb eines Zertifizierungsstellenzertifikats bereit ist. Company-X kann auch ein selbstsigniertes X.509-Zertifizierungsstellenzertifikat in der Produktion verwenden, wenn es bei Smart-X-Widgets nicht vorgesehen ist, eine Verbindung mit Drittanbieterdiensten außerhalb von IoT Hub herzustellen.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrieren des X.509-Zertifikats für IoT Hub

Company-X muss die X.509-Zertifizierungsstelle für IoT Hub registrieren, damit sie dort bei der Verbindungsherstellung zum Authentifizieren von Smart-X-Widgets dienen kann. Dies ein einmaliger Prozess, mit dem eine beliebige Anzahl von Smart-X-Widget-Geräten authentifiziert und verwaltet werden kann. Dies ist aufgrund einer 1:n-Beziehung zwischen ZS-Zertifikat und Gerätezertifikaten, die durch das ZS-Zertifikat oder ein Zwischenzertifikat signiert sind, ein einmaliger Prozess. Diese Beziehung ist einer der Hauptvorteile der Verwendung der Authentifizierungsmethode mit X.509-Zertifizierungsstellen. Die Alternative ist das Hochladen von einzelnen Zertifikatfingerabdrücken für jedes einzelne Smart-X-Widget-Gerät, was aber zu einer Erhöhung der Betriebskosten führt.

Das Registrieren des X.509-Zertifizierungsstellenzertifikats ist ein Prozess aus zwei Schritten: Upload des Zertifikats und Eigentumsnachweis für das Zertifikat.

![Registrieren eines X.509-Zertifizierungsstellenzertifikats](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Hochladen des X.509-Zertifizierungsstellenzertifikats

Mit dem Prozess zum Hochladen des X.509-Zertifizierungsstellenzertifikats wird das Zertifizierungsstellenzertifikat auf IoT Hub hochgeladen. IoT Hub erwartet das Zertifikat als Datei. Company-X lädt die Zertifikatsdatei einfach hoch. Die Zertifikatsdatei darf NIEMALS private Schlüssel enthalten. Die bewährten Methoden der Standards für die Public Key-Infrastruktur (PKI) besagen, dass die Informationen zum privaten Schlüssel von Company-X in diesem Fall ausschließlich innerhalb von Company-X vorliegen sollten.

### <a name="proof-of-possession-of-the-certificate"></a>Eigentumsnachweis für das Zertifikat

Beim X.509-Zertifizierungsstellenzertifikat handelt es sich wie bei allen digitalen Zertifikaten um öffentliche Informationen, für die die Gefahr des Abfangens besteht. In den Prozess eingreifende Dritte können ein Zertifikat ggf. abfangen und versuchen, es als eigenes Zertifikat hochzuladen. In unserem Beispiel soll per IoT Hub sichergestellt werden, dass das von Company-X hochgeladene Zertifizierungsstellenzertifikat wirklich von Company-X stammt. Hierzu wird Company-X aufgefordert, anhand eines [Ablaufs zum Eigentumsnachweis (Proof-of-Possession)](https://tools.ietf.org/html/rfc5280#section-3.1) den Besitz des Zertifikats nachzuweisen. Der Ablauf zum Eigentumsnachweis umfasst das Generieren einer Zufallszahl durch IoT Hub für die Signierung durch Company-X mit dem privaten Schlüssel. Wenn sich Company-X an die bewährten Methoden für PKI hält und den privaten Schlüssel schützt, kann nur dieses Unternehmen die richtige Antwort auf die Aufforderung zum Eigentumsnachweis liefern. Wenn die Antwort zum Nachweisen des Eigentums erfolgreich war, fährt IoT Hub mit dem Registrieren des X.509-Zertifizierungsstellenzertifikats fort.

Die Registrierung des X.509-Zertifizierungsstellenzertifikats wird abgeschlossen, nachdem die Aufforderung zum Eigentumsnachweis erfolgreich beantwortet wurde.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Anmelden von Geräten an einer Zertifikatvertrauenskette

Für IoT ist es erforderlich, dass jedes Gerät eine eindeutige Identität besitzt. Diese Identitäten liegen in Form von Zertifikaten für zertifikatbasierte Authentifizierungsschemas vor. Für unser Beispiel bedeutet dies, dass jedes Smart-X-Widget-Gerät über ein eindeutiges Gerätezertifikat verfügen muss. Wie richtet Company-X dies für seine Lieferkette ein?

Eine Möglichkeit besteht darin, Zertifikate für Smart-X-Widgets vorab zu generieren und die Informationen zu den entsprechenden eindeutigen privaten Schlüsseln für Geräte Lieferkettenpartnern anzuvertrauen. Für Company-X bedeutet dies, dass die Informationen Factory-Y und Technician-Z anvertraut werden. Dies ist zwar eine akzeptable Vorgehensweise, aber es müssen auch bestimmte Anforderungen erfüllt werden, um die Vertrauensstellung zu gewährleisten:

1. Wenn private Schlüssel von Geräten für Lieferkettenpartner bereitgestellt werden müssen (und die bewährten Methoden für PKI somit ignoriert werden), erhöhen sich die Kosten für die Einrichtung von Vertrauensstellungen in der Lieferkette. Es müssen Systeme, z.B. geschützte Räume zur Aufbewahrung der privaten Schlüssel von Geräten, und Prozesse, z.B. regelmäßige Sicherheitsüberwachungen, installiert werden. Beides führt zu einer Erhöhung der Lieferkettenkosten.

2. Das sichere Verwalten von Geräten in der Lieferkette und später während der Bereitstellung wird für jedes Schlüssel-Gerät-Paar von der Generierung des eindeutigen Gerätezertifikats (daher als privater Schlüssel bezeichnet) bis zur Aussonderung des Geräts zu einer individuellen Aufgabe. Die Gruppenverwaltung von Geräten ist somit ausgeschlossen, sofern das Gruppenkonzept nicht explizit in den Prozess integriert ist. Die sichere Zurechenbarkeit und die Lebenszyklusverwaltung für Geräte ist also mit hohem Aufwand verbunden. In unserem Beispiel fällt dieser Aufwand für Company-X an.

Die Authentifizierung des X.509-Zertifizierungsstellenzertifikats ermöglicht elegante Lösungen für die obigen Anforderungen mithilfe von Zertifikatketten. Eine Zertifikatkette ist das Ergebnis, wenn eine Zertifizierungsstelle eine zwischengeschaltete Zertifizierungsstelle signiert, die wiederum eine weitere zwischengeschaltete Zertifizierungsstelle signiert usw., bis eine letzte zwischengeschaltete Zertifizierungsstelle ein Gerät signiert. In unserem Beispiel wird Factory-Y von Company-X signiert, Factory-Y signiert wiederum Technician-Z, und Technician-Z signiert schließlich das Smart-X-Widget-Gerät.

![Hierarchie der Zertifikatskette](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Die obige Abfolge von Zertifikaten der Kette stellt die logische Weitergabe der Autorität als Zertifizierungsstelle dar. Diese logische Weitergabe wird für viele Lieferketten genutzt. Jede zwischengeschaltete Zertifizierungsstelle wird für die Kette signiert, während die Zertifikate der vorgeschalteten Zertifizierungsstellen empfangen werden. Die letzte zwischengeschaltete Zertifizierungsstelle signiert dann jeweils das Gerät und fügt alle Zertifizierungsstellenzertifikate der Kette in das Gerät ein. Dies kommt häufig vor, wenn das Fertigungssubunternehmen mit einer Hierarchie von Werken ein bestimmtes Werk mit der Fertigung beauftragt. Die Hierarchie kann zwar mehrere Ebenen umfassen (z.B. nach Geografie/Produkttyp/Fertigungslinie), aber nur das Werk am Ende der Hierarchie interagiert mit dem Gerät. Die Kette wird vom oberen Ende der Hierarchie aus verwaltet.

Bei anderen Ketten kann es sein, dass unterschiedliche zwischengeschaltete Zertifizierungsstellen mit dem Gerät interagieren. In diesem Fall fügt die Zertifizierungsstelle, die mit dem Gerät interagiert, an diesem Punkt Zertifikatketteninhalt ein. Hybridmodelle sind auch möglich, wenn nur für einige Zertifizierungsstellen eine physische Interaktion mit dem Gerät erfolgt.

In unserem Beispiel interagieren sowohl Factory-Y als auch Technician-Z mit dem Smart-X-Widget. Das Smart-X-Widget befindet sich zwar im Besitz von Company-X, aber es kommt in der gesamten Lieferkette nicht zu einer physischen Interaktion damit. Im Rahmen der Zertifikatvertrauenskette für Smart-X-Widget signiert Company-X also Factory-Y, und Factory-Y signiert wiederum Technician-Z, wo dann das endgültige Signieren des Smart-X-Widget-Geräts erfolgt. Bei der Fertigung und Installation des Smart-X-Widget-Geräts nutzen Factory-Y und Technician-Z ihre jeweiligen zwischengeschalteten Zertifizierungsstellenzertifikate, um die einzelnen Smart-X-Widgets zu signieren. Das Endergebnis dieses gesamten Prozesses umfasst Smart-X-Widgets mit eindeutigen Gerätezertifikaten und eine Zertifikatvertrauenskette für das Zertifizierungsstellenzertifikat von Company-X.

![Vertrauenskette von den Zertifikaten eines Unternehmens zu den Zertifikaten eines anderen Unternehmens](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Dies ist ein guter Zeitpunkt, um den Wert der X.509-Zertifizierungsstellenmethode zu prüfen. Anstatt Zertifikate für jedes Smart-X-Widget in der Lieferkette vorab zu generieren und bereitzustellen, muss Company-X das Signieren für Factory-Y nur einmal durchführen. Company-X muss nicht jedes einzelne Gerät während des Gerätelebenszyklus nachverfolgen, sondern kann Geräte ggf. in Gruppen nachverfolgen und verwalten, die sich auf natürliche Weise durch den Lieferkettenprozess ergeben, z.B. von Technician-Z ab Juli eines bestimmten Jahres installierte Geräte.

Zu guter Letzt fügt die Zertifizierungsstellenmethode der Authentifizierung die sichere Zurechenbarkeit in die Lieferkette für die Gerätefertigung ein. Aufgrund des Zertifikatkettenprozesses werden die Aktionen jedes Mitglieds der Kette kryptografisch aufgezeichnet und sind verifizierbar.

Für diesen Prozess gelten bestimmte Annahmen, die der Vollständigkeit halber erwähnt werden müssen. Eindeutige Schlüsselpaare (öffentlich/privat) für Geräte müssen erstellt werden, und der private Schlüssel muss innerhalb des Geräts geschützt sein. Glücklicherweise gibt es sichere Siliziumchips in Form von Hardwaresicherheitsmodulen (HSM), mit denen Schlüssel intern generiert und geschützt werden können. Company-X muss der Stückliste eines Smart-X-Widget-Geräts lediglich einen Chip dieser Art hinzufügen.

## <a name="device-connection"></a>Geräteverbindung

In den obigen Abschnitten wurde die Geräteverbindung vorbereitet. Wie können im Bedarfsfall Millionen von Geräten zum ersten Mal eine Verbindung herstellen und authentifiziert werden, indem einfach jeweils ein X.509-Zertifizierungsstellenzertifikat für IoT Hub registriert wird?  Kein Problem: Es wird der gleiche Ablauf wie oben mit Zertifikatupload und Eigentumsnachweis verwendet, den wir bereits von der Registrierung des X.509-Zertifizierungsstellenzertifikats kennen.

Für die Authentifizierung per X.509-Zertifizierungsstelle ausgelegte Geräte verfügen über eindeutige Gerätezertifikate und eine Zertifikatkette, die auf der jeweiligen Fertigungslieferkette basiert. Zum Herstellen der Geräteverbindung wird auch beim ersten Mal ein zweistufiger Prozess verwendet: Upload der Zertifikatkette und Durchführung des Eigentumsnachweises.

Beim Hochladen der Zertifikatkette lädt das Gerät sein für das Gerät eindeutige Zertifikat zusammen mit der installierten Zertifikatkette auf IoT Hub hoch. Mit dem vorab registrierten X.509-Zertifizierungsstellenzertifikat kann IoT Hub einige Dinge kryptografisch überprüfen, z.B. ob die hochgeladene Zertifikatkette intern konsistent ist und ob die Kette vom gültigen Eigentümer des X.509-Zertifizierungsstellenzertifikats stammt. Wie beim Registrierungsprozess über die X.509-Zertifizierungsstelle auch, würde IoT Hub einen Abfrage/-Rückmeldung-Prozess zum Nachweisen des Eigentums initiieren, um sicherzustellen, dass die Kette und somit auch das Gerätezertifikat zum hochladenden Gerät gehört. Hierzu wird eine zufällige Herausforderung generiert, die vom Gerät als Bestätigung für IoT Hub mit dem privaten Schlüssel signiert werden muss. Eine erfolgreiche Antwort führt dazu, dass IoT Hub das Gerät als authentisch akzeptiert und die Verbindungsherstellung zulässt.

In unserem Beispiel lädt jedes Smart-X-Widget sein eindeutiges Gerätezertifikat zusammen mit den X.509-Zertifizierungsstellenzertifikaten für Factory-Y und Technician-Z hoch und beantwortet anschließend die Herausforderung für den Eigentumsnachweis von IoT Hub.

![Ablauf von einem Zertifikat zu einem anderen, PoP-Abfrage aus dem Hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Beachten Sie, dass die Grundlage der Vertrauensstellung auf dem Schutz von privaten Schlüsseln basiert – einschließlich der privaten Geräteschlüssel. Daher soll noch einmal betont werden, wie wichtig die Verwendung von sicheren Siliziumchips in Form von Hardwaresicherheitsmodulen (HSM) als Schutz für private Geräteschlüssel ist und dass die bewährte Methode immer darin besteht, private Schlüssel niemals weiterzugeben (z.B. von einem Werk zum anderen).
