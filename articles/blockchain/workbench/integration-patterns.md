---
title: Smart Contract-Integrationsmuster – Azure Blockchain Workbench
description: Übersicht über Smart Contract-Integrationsmuster in Azure Blockchain Workbench – Vorschau.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015501"
---
# <a name="smart-contract-integration-patterns"></a>Smart Contract-Integrationsmuster

Smart Contracts stellen häufig einen Geschäftsworkflow dar, der in externe Systeme und Geräte integriert werden muss.

Zu den Anforderungen dieser Workflows gehört die Notwendigkeit, Transaktionen für einen verteilten Ledger zu initiieren, die Daten aus einem externen System, Dienst oder Gerät enthalten. Außerdem müssen externe Systeme auf Ereignisse reagieren können, die aus Smart Contracts für einen verteilten Ledger stammen.

Die Integration von REST-API und Messaging sendet Transaktionen von externen Systemen an Smart Contracts, die in einer Azure Blockchain Workbench-Anwendung enthalten sind. Sie sendet auch Ereignisbenachrichtigungen an externe Systeme, wenn in einer Anwendung Änderungen vorgenommen werden.

Für Datenintegrationsszenarien enthält Azure Blockchain Workbench eine Reihe von Datenbanksichten, die eine Kombination von Transaktionsdaten aus der Blockchain und Metadaten über Anwendungen und intelligente Verträge mergen.

Darüber hinaus können einige Szenarien (z.B. im Zusammenhang mit der Lieferkette oder Medien) auch die Integration von Dokumenten erfordern. Auch wenn Azure Blockchain Workbench keine API-Aufrufe zur direkten Bearbeitung von Dokumenten bereitstellt, können Dokumente in eine Blockchain-Anwendung eingebunden werden. Dieser Abschnitt beschreibt auch dieses Muster.

Dieser Abschnitt enthält die Muster für die Implementierung jeder dieser Integrationsarten in Ihre End-to-End-Lösungen.

## <a name="rest-api-based-integration"></a>REST-API-basierte Integration

Funktionen innerhalb der von Azure Blockchain Workbench generierten Webanwendung werden über die REST-API bereitgestellt. Zu den Funktionen gehören das Hochladen, Konfigurieren und Verwalten von Anwendungen, das Senden von Transaktionen an einen verteilten Ledger und das Abfragen von Anwendungsmetadaten und Ledgerdaten durch Azure Blockchain Workbench.

Die REST-API wird in erster Linie für interaktive Clients wie Web-, Mobile- und Bot Anwendungen verwendet.

Dieser Abschnitt befasst sich mit Mustern, die sich auf die Aspekte der REST-API konzentrieren und die Transaktionen an einen Distributed Ledger senden, und mit Mustern, die Daten zu Transaktionen aus der *nicht verketteten* Datenbank von Azure Blockchain Workbench abfragen.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Senden von Transaktionen an einen verteilten Ledger aus einem externen System

Die REST-API von Azure Blockchain Workbench sendet authentifizierte Anforderungen zum Ausführen von Transaktionen für einen Distributed Ledger.

![Senden von Transaktionen an einen verteilten Ledger](./media/integration-patterns/send-transactions-ledger.png)

Die Ausführung von Transaktionen erfolgt anhand des zuvor dargestellten Prozesses. Dabei gilt Folgendes:

-   Die externe Anwendung authentifiziert sich bei Azure Active Directory, das als Teil von Azure Blockchain Workbench bereitgestellt wird.
-   Autorisierte Benutzer erhalten ein Bearertoken, das mit Anforderungen an die API gesendet werden kann.
-   Externe Anwendungen nehmen Aufrufe der REST-API mit dem Bearertoken vor.
-   Die REST-API verpackt die Anforderung als eine Nachricht und sendet sie an Service Bus. Von hier aus wird sie abgerufen, signiert und an den entsprechenden Distributed Ledger gesendet.
-   Die REST-API stellt eine Anforderung an die SQL-Datenbank von Azure Blockchain Workbench zum Aufzeichnen der Anforderung und Einrichten des aktuellen Bereitstellungstatus.
-   Die SQL-Datenbank gibt den Bereitstellungsstatus zurück, und der API-Aufruf gibt die ID an die externe Anwendung zurück, die sie aufgerufen hat.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Abfragen von Blockchain Workbench-Metadaten und Transaktionen des verteilten Ledgers

Die REST-API von Azure Blockchain Workbench sendet authentifizierte Anforderungen, um Details abzufragen, die sich auf die Smart Contract-Ausführung für einen Distributed Ledger beziehen.

![Abfragen von Metadaten](./media/integration-patterns/querying-metadata.png)

Die Abfrage erfolgt anhand des zuvor dargestellten Prozesses. Dabei gilt Folgendes:

1. Die externe Anwendung authentifiziert sich bei Azure Active Directory, das als Teil von Azure Blockchain Workbench bereitgestellt wird.
2. Autorisierte Benutzer erhalten ein Bearertoken, das mit Anforderungen an die API gesendet werden kann.
3. Externe Anwendungen nehmen Aufrufe der REST-API mit dem Bearertoken vor.
4. Die REST-API fragt die Daten für die Anforderung aus der SQL-Datenbank ab und gibt sie an den Client zurück.

## <a name="messaging-integration"></a>Messagingintegration

Die Messagingintegration ermöglicht die Interaktion mit Systemen, Diensten und Geräten, für die eine interaktive Anmeldung nicht möglich oder wünschenswert ist. Die Messagingintegration konzentriert sich auf zwei Arten von Nachrichten: auf Nachrichten, die die Ausführung von Transaktionen für einen Distributed Ledger anfordern, und auf Ereignisse, die von diesem Ledger bereitgestellt werden, wenn Transaktionen stattgefunden haben.

Die Messagingintegration konzentriert sich auf die Ausführung und Überwachung von Transaktionen im Zusammenhang mit der Benutzererstellung, der Vertragserstellung und der Ausführung von Transaktionen für Verträge und wird hauptsächlich von *monitorlosen* Back-End-Systemen verwendet.

Dieser Abschnitt befasst sich mit Mustern, die sich auf die Aspekte der nachrichtenbasierten API konzentrieren und die Transaktionen an einen Distributed Ledger senden, und mit Mustern, die Ereignisnachrichten darstellen, die vom zugrunde liegenden Distributed Ledger bereitgestellt werden.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Unidirektionale Ereignisübermittlung aus einem Smart Contract an einen Ereignisconsumer 

In diesem Szenario tritt ein Ereignis innerhalb eines Smart Contract auf, z.B. ein Zustandswechsel oder die Ausführung einer bestimmten Art von Transaktion. Dieses Ereignis wird über ein Event Grid an nachgeschaltete Consumer übertragen, und diese Consumer ergreifen dann geeignete Maßnahmen.

Ein Beispiel für dieses Szenario ist, dass beim Auftreten einer Transaktion ein Consumer benachrichtigt wird und Maßnahmen ergreifen kann, z.B. die Aufzeichnung der Informationen in einer SQL-Datenbank oder dem Common Data Service. Dieses Szenario stellt dasselbe Muster dar, dem Workbench folgt, um die zugehörige *nicht verkettete* SQL-Datenbank mit Daten aufzufüllen.

Ein anderes Beispiel wäre, wenn ein Smart Contract in einen bestimmten Zustand übergeht, z.B. wenn ein Vertrag in den Zustand *OutOfCompliance* wechselt. Wenn diese Zustandsänderung eintritt, kann sie eine Warnung auslösen, die an das Mobiltelefon eines Administrators gesendet wird.

![Unidirektionale Ereignisübermittlung](./media/integration-patterns/one-way-event-delivery.png)

Dieses Szenario erfolgt anhand des zuvor dargestellten Prozesses. Dabei gilt Folgendes:

-   Der Smart Contract geht in einen neuen Zustand über und sendet ein Ereignis an den Ledger.
-   Der Ledger empfängt das Ereignis und übermittelt es an Azure Blockchain Workbench.
-   Azure Blockchain Workbench hat Ereignisse vom Ledger abonniert und empfängt das Ereignis.
-   Azure Blockchain Workbench veröffentlicht das Ereignis für die Abonnenten im Event Grid.
-   Externe Systemen haben Event Grid abonniert, verarbeiten die Nachricht und ergreifen die geeigneten Aktionen.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Unidirektionale Ereignisübermittlung einer Nachricht aus einem externen System an einen Smart Contract

Es gibt auch ein Szenario mit einem Ereignisfluss aus der entgegengesetzten Richtung. In diesem Fall wird ein Ereignis von einem Sensor oder einem externen System generiert, und die Daten aus diesem Ereignis sollten an einen Smart Contract gesendet werden.

Ein gängiges Beispiel ist die Übermittlung von Daten aus den Finanzmärkten, z.B. von Rohstoffpreisen, Aktien oder Anleihen, an einen Smart Contract.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkte Übermittlung einer Azure Blockchain Workbench im erwarteten Format

Einige Anwendungen sind für die Integration in Azure Blockchain Workbench entwickelt worden und generieren und senden direkt Nachrichten in den erwarteten Formaten.

![Direkte Übermittlung](./media/integration-patterns/direct-delivery.png)

Diese Übermittlung erfolgt anhand des zuvor dargestellten Prozesses. Dabei gilt Folgendes:

-   In einem externen System tritt ein Ereignis auf, das die Erstellung einer Nachricht für Azure Blockchain Workbench auslöst.
-   Das externe System verfügt über Code, um diese Nachricht in einem bekannten Format zu erstellen, und sendet diese direkt an Service Bus.
-   Azure Blockchain Workbench hat Ereignisse aus Service-Bus abonniert und ruft die Nachricht ab.
-   Azure Blockchain Workbench initiiert einen Aufruf des Ledgers und sendet Daten aus dem externen System an einen bestimmten Vertrag.
-   Beim Empfang der Nachricht geht der Vertrag in einen neuen Zustand über.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Übermittlung einer Nachricht in einem für Azure Blockchain Workbench unbekannten Format

Einige Systeme können nicht so geändert werden, dass sie Nachrichten in den von Azure Blockchain Workbench verwendeten Standardformaten übermitteln. In diesen Fällen können häufig vorhandene Mechanismen und Nachrichtenformate aus diesen Systemen genutzt werden. Insbesondere können die nativen Nachrichtentypen dieser Systeme mithilfe von Logik-Apps, Azure Functions oder anderem benutzerdefinierten Code transformiert werden, um sie einem der erwarteten Messagingstandardformate zuzuordnen.

![Unbekanntes Nachrichtenformat](./media/integration-patterns/unknown-message-format.png)

Dies erfolgt anhand des zuvor dargestellten Prozesses. Dabei gilt Folgendes:

-   In einem externen System tritt ein Ereignis auf, das die Erstellung einer Nachricht auslöst.
-   Eine Logik-App oder benutzerdefinierter Code wird verwendet, um diese Nachricht zu empfangen und in eine Nachricht im Azure Blockchain Workbench-Standardformat zu transformieren.
-   Die Logik-App sendet die transformierte Nachricht direkt an Service Bus.
-   Azure Blockchain Workbench hat Ereignisse aus Service-Bus abonniert und ruft die Nachricht ab.
-   Azure Blockchain Workbench initiiert einen Aufruf des Ledgers und sendet Daten aus dem externen System an eine bestimmte Funktion des Vertrags.
-   Die Funktion wird ausgeführt und ändert in der Regel den Zustand. Durch die Zustandsänderung wird der im Smart Contract widergespiegelte Geschäftsworkflow fortgesetzt, sodass nun weitere Funktionen entsprechend ausgeführt werden können.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Übertragen der Steuerung an einen externen Prozess und Warten auf den Abschluss

Es gibt Szenarien, in denen ein Smart Contract die interne Ausführung beenden und an einen externen Prozess übergeben muss. Dieser externe Prozess wird dann abgeschlossen, sendet eine Nachricht an den Smart Contract, und die Ausführung wird innerhalb des Smart Contract fortgesetzt.

#### <a name="transition-to-the-external-process"></a>Übergabe an den externen Prozess

Dieses Muster wird normalerweise mit dem folgenden Ansatz implementiert:

-   Das Smart Contract geht in einen bestimmten Zustand über. In diesem Zustand können entweder keine oder nur eine begrenzte Anzahl von Funktionen ausgeführt werden, bis ein externes System eine gewünschte Aktion ausführt.
-   Die Zustandsänderung wird als Ereignis für einen nachgeschalteten Consumer angezeigt.
-   Der nachgeschaltete Consumer empfängt das Ereignis und löst die externe Codeausführung aus.

![Im Diagramm ist eine Zustandsänderung im Vertrag zu sehen, die bewirkt, dass ein Ereignis in den Distributed Ledger wechselt. Blockchain Workbench ruft dann das Ereignis ab und veröffentlicht es.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Rückgabe der Steuerung aus dem Smart Contract

Abhängig von der Möglichkeit, das externe System anzupassen, kann es Nachrichten ggf. in einem der Standardformate übermitteln, die Azure Blockchain Workbench erwartet. Basierend auf der Fähigkeit des externen Systems, eine dieser Nachrichten zu generieren, wird bestimmt, welcher der beiden folgenden Rückgabepfade verwendet wird.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkte Übermittlung einer Azure Blockchain Workbench im erwarteten Format

![Im Diagramm ist eine API-Nachricht aus dem externen System zu sehen, die von Blockchain Workbench über Service Bus abgerufen wird. Blockchain Workbench sendet dann im Auftrag des Agents eine Nachricht als Transaktion an den Distributed Ledger. Sie wird an den Vertrag weitergegeben und bewirkt dort eine Zustandsänderung.](./media/integration-patterns/direct-delivery.png)

In diesem Modell erfolgt die Kommunikation mit dem Vertrag und die anschließende Zustandsänderung nach dem obigen Verfahren, wobei Folgendes gilt:

-   Nach Erreichen des Abschlusses oder eines bestimmten Meilensteins in der externen Codeausführung wird ein Ereignis an den mit Azure Blockchain Workbench verbundenen Service Bus gesendet.

-   Für Systeme, die nicht direkt angepasst werden können, um eine Nachricht zu schreiben, die den Erwartungen der API entspricht, wird diese transformiert.

-   Der Inhalt der Nachricht wird verpackt und an eine bestimmte Funktion für den Smart Contract gesendet. Diese Übermittlung erfolgt im Namen des Benutzers, der dem externen System zugeordnet ist.

-   Die Funktion wird ausgeführt und ändert in der Regel den Zustand. Durch die Zustandsänderung wird der im Smart Contract widergespiegelte Geschäftsworkflow fortgesetzt, sodass nun weitere Funktionen entsprechend ausgeführt werden können.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Übermittlung einer Nachricht in einem für Azure Blockchain Workbench unbekannten Format

![Unbekanntes Nachrichtenformat](./media/integration-patterns/unknown-message-format.png)

In diesem Modell, in dem eine Nachricht in einem Standardformat nicht direkt gesendet werden kann, erfolgt die Kommunikation mit dem Vertrag und die anschließende Zustandsänderung nach dem obigen Verfahren, wobei Folgendes gilt:

1.  Nach Erreichen des Abschlusses oder eines bestimmten Meilensteins in der externen Codeausführung wird ein Ereignis an den mit Azure Blockchain Workbench verbundenen Service Bus gesendet.
2.  Eine Logik-App oder benutzerdefinierter Code wird verwendet, um diese Nachricht zu empfangen und in eine Nachricht im Azure Blockchain Workbench-Standardformat zu transformieren.
3.  Die Logik-App sendet die transformierte Nachricht direkt an Service Bus.
4.  Azure Blockchain Workbench hat Ereignisse aus Service-Bus abonniert und ruft die Nachricht ab.
5.  Azure Blockchain Workbench initiiert einen Aufruf des Ledgers und sendet Daten aus dem externen System an einen bestimmten Vertrag.
6. Der Inhalt der Nachricht wird verpackt und an eine bestimmte Funktion für den Smart Contract gesendet. Diese Übermittlung erfolgt im Namen des Benutzers, der dem externen System zugeordnet ist.
7.  Die Funktion wird ausgeführt und ändert in der Regel den Zustand. Durch die Zustandsänderung wird der im Smart Contract widergespiegelte Geschäftsworkflow fortgesetzt, sodass nun weitere Funktionen entsprechend ausgeführt werden können.

## <a name="iot-integration"></a>IoT-Integration

Ein häufiges Integrationsszenario ist die Einbindung von Telemetriedaten aus Sensoren in einen Smart Contract. Basierend auf den von den Sensoren gelieferten Daten können Smart Contracts geeignete Maßnahmen ergreifen und den Vertragszustand ändern.

Wenn in einem LKW, der Medikamente liefert, beispielsweise eine Temperatur von 44 Grad Celsius herrscht, kann dies die Wirksamkeit der Medikamente beeinträchtigen und ein Problem der öffentlichen Sicherheit verursachen, wenn dieser Zustand nicht erkannt wird und die Medikamente nicht aus der Lieferkette entfernt werden. Wenn ein Fahrer sein Auto trotz Geschwindigkeitsbeschränkung auf 160 km/h beschleunigt, könnte die sich daraus ergebende Sensorinformation eine Kündigung der Versicherung durch seinen Versicherer auslösen. Wenn es sich um einen Mietwagen handelt, könnten GPS-Daten angeben, wann der Fahrer außerhalb einer von seinem Mietvertrag abgedeckten Region unterwegs war und zu einer Strafe führen.

Die Herausforderung besteht darin, dass diese Sensoren ständig Daten liefern können und es nicht angebracht ist, alle diese Daten an einen Smart Contract zu senden. Ein typischer Ansatz besteht darin, die Anzahl der an die Blockchain gesendeten Nachrichten zu begrenzen, während alle Nachrichten an einen sekundären Speicher übermittelt werden. Beispielsweise können Sie empfangene Nachrichten nur in einem festen Intervall (z.B. ein Mal pro Stunde) übermitteln und wenn ein enthaltener Wert außerhalb eines vereinbarten Bereichs für einen Smart Contract liegt. Das Überprüfen von Werten, die außerhalb der Toleranzen liegen, stellt sicher, dass die für die Geschäftslogik des Vertrags relevanten Daten empfangen und ausgeführt werden. Durch die Überprüfung des Werts im Intervall wird bestätigt, dass der Sensor noch sendet. Alle Daten werden an einen sekundären Berichtsspeicher gesendet, um umfassendere Berichte, Analysen und Machine Learning zu ermöglichen. Während für einen Smart Contract nicht jede Minute Sensorwerte für GPS benötigt werden, können diese z.B. interessante Daten liefern, die in Berichten oder Zuordnungsrouten verwendet werden können.

Auf der Azure-Plattform erfolgt die Integration in Geräte in der Regel mit IoT Hub. IoT Hub ermöglicht das Routing von Nachrichten basierend auf dem Inhalt sowie die oben beschriebene Art von Funktionalität.

![IoT-Nachrichten](./media/integration-patterns/iot.png)

Der Prozess stellt ein Muster dar:

-   Ein Gerät kommuniziert direkt oder über ein Bereichsgateway mit IoT Hub.
-   IoT Hub empfängt die Nachrichten und wertet die Nachrichten anhand der hergestellten Routen aus, die z.B. den Inhalt der Nachricht überprüfen. *Meldet der Sensor eine höhere Temperatur als 10 Grad Celsius?*
-   IoT Hub sendet Nachrichten, die die Kriterien für einen definierten Service Bus für die Route erfüllen.
-   Eine Logik-App oder anderer Code lauscht auf den Service Bus, den IoT Hub für die Route eingerichtet hat.
-   Die Logik-App oder anderer Code ruft die Nachricht ab und transformiert sie in ein bekanntes Format.
-   Die transformierte Nachricht (jetzt in einem Standardformat) wird an den Service Bus für Azure Blockchain Workbench gesendet.
-   Azure Blockchain Workbench hat Ereignisse aus Service-Bus abonniert und ruft die Nachricht ab.
-   Azure Blockchain Workbench initiiert einen Aufruf des Ledgers und sendet Daten aus dem externen System an einen bestimmten Vertrag.
-   Nach Empfang der Nachricht wertet der Vertrag die Daten aus und kann den Zustand basierend auf dem Ergebnis dieser Bewertung ändern. Er kann bei einer hohen Temperatur den Zustand z.B. in *OutOfCompliance* ändern.

## <a name="data-integration"></a>Datenintegration

Azure Blockchain Workbench bietet neben REST und der nachrichtenbasierten API auch Zugriff auf eine SQL-Datenbank, die mit Anwendungs- und Vertragsmetadaten sowie Transaktionsdaten aus verteilten Ledgern aufgefüllt wird.

![Datenintegration](./media/integration-patterns/data-integration.png)

Die Datenintegration ist gut bekannt:

-   Azure Blockchain Workbench speichert Metadaten zu Anwendungen, Workflows, Verträgen und Transaktionen als Teil des normalen Betriebsverhaltens.
-   Externe Systeme oder Tools stellen mindestens ein Dialogfeld zur Verfügung, um die Erfassung von Informationen zur Datenbank zu erleichtern, z.B. Name des Datenbankservers, Datenbankname, Art der Authentifizierung, Anmeldeinformationen und welche Datenbanksichten verwendet werden sollen.
-   Abfragen werden für Datenbanksichten zur Erleichterung der nachgeschalteten Nutzung durch externe Systeme, Dienste, Berichterstellung, Entwicklertools und Produktivitätstools des Unternehmens geschrieben.

## <a name="storage-integration"></a>Speicherintegration

Viele Szenarien können die Notwendigkeit aufweisen, belegbare Dateien einzubinden. Es ist aus mehreren Gründen unangebracht, Dateien in eine Blockchain zu integrieren. Stattdessen ist es üblich, einen kryptografischen Hash (z. B. SHA-256) für eine Datei auszuführen und diesen Hash für ein Distributed Ledger freizugeben. Eine erneute Ausführung des Hash zu einem späteren Zeitpunkt sollte das gleiche Ergebnis liefern. Wenn die Datei geändert wird (auch wenn nur ein Pixel in einem Bild geändert wird), gibt der Hash einen anderen Wert zurück.

![Speicherintegration](./media/integration-patterns/storage-integration.png)

Das Muster kann implementiert werden, wobei Folgendes gilt:

-   Ein externes System speichert eine Datei persistent in einem Speichermechanismus, z.B. in Azure Storage.
-   Ein Hash wird mit der Datei oder der Datei und den zugehörigen Metadaten wie z.B. einem Bezeichner für den Besitzer, der URL, dem Speicherort der Datei usw. generiert.
-   Der Hash und alle Metadaten werden an eine Funktion für einen Smart Contract (z.B. *FileAdded*) gesendet.
-   In Zukunft können die Datei und die Metadaten erneut gehasht und mit den auf dem Ledger gespeicherten Werten verglichen werden.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Voraussetzungen für die Implementierung von Integrationsmustern mithilfe der REST- und Nachrichten-APIs

Damit ein externes System oder Gerät mit dem Smart Contract über die REST- oder Nachrichten-API interagieren kann, müssen die folgenden Aktionen auftreten:

1. Im Azure Active Directory für das Konsortium wird ein Konto erstellt, das das externe System oder Gerät darstellt.
2. Für die entsprechenden Smart Contracts für Ihre Azure Blockchain Workbench-Anwendung sind Funktionen definiert, um die Ereignisse von Ihrem externen System oder Gerät zu akzeptieren.
3. Die Anwendungskonfigurationsdatei für Ihren Smart Contract enthält die Rolle, die dem System oder Gerät zugewiesen ist.
4. Die Anwendungskonfigurationsdatei für Ihren Smart Contract gibt an, in welchen Zuständen diese Funktion von der definierten Rolle aufgerufen wird.
5. Die Anwendungskonfigurationsdatei und ihre Smart Contracts werden in Azure Blockchain Workbench hochgeladen.

Nach dem Hochladen der Anwendung wird das Azure Active Directory-Konto für das externe System dem Vertrag und der zugehörigen Rolle zugewiesen.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testen der Datenflüsse der externen Systemintegration vor dem Schreiben von Integrationscode 

Die Integration mit externen Systemen ist eine zentrale Anforderung vieler Szenarien. Es ist wünschenswert, den Smart Contract-Entwurf vor der oder parallel zur Entwicklung von Code zur Integration in externe Systeme überprüfen zu können.

Die Verwendung von Azure Active Directory (Azure AD) kann die Produktivität der Entwickler und die Zeit bis zur Wertschöpfung erheblich beschleunigen. Insbesondere kann die Codeintegration in ein externes System eine nicht triviale Zeitspanne in Anspruch nehmen. Durch die Verwendung von Azure AD und die automatische Generierung der Benutzererfahrung durch Azure Blockchain Workbench können Entwickler sich als dieses externe System bei Blockchain Workbench anmelden und die von dem externen System erwarteten Werte über die Benutzererfahrung mit Daten auffüllen. Sie können so sehr schnell Ideen in einer Proof of Concept-Umgebung entwickeln und überprüfen, bevor Integrationscode für die externen Systeme geschrieben wird.
