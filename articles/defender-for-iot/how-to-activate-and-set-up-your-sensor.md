---
title: Aktivieren und Einrichten des Sensors
description: In diesem Artikel wird beschrieben, wie Sie sich anmelden und eine Sensorkonsole aktivieren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 56f6c9d3ff3093c3acd28b670e37f823ad5e9f9a
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522511"
---
# <a name="activate-and-set-up-your-sensor"></a>Aktivieren und Einrichten des Sensors

In diesem Artikel wird beschrieben, wie Sie einen Sensor aktivieren und das anfängliche Setup vornehmen.

Administratoren führen die Aktivierung durch, wenn sie sich erstmals anmelden und die Verwaltung der Aktivierung erforderlich ist. Das Setup stellt sicher, dass der Sensor für eine optimale Erkennung und Warnung konfiguriert ist.

Sicherheitsanalysten und Benutzer mit Leseberechtigung können einen Sensor nicht aktivieren und kein neues Kennwort generieren.

## <a name="sign-in-and-activation-for-administrator-users"></a>Anmeldung und Aktivierung für Administratoren

Administratoren, die sich erstmals anmelden, müssen sich vergewissern, dass sie Zugriff auf die Aktivierungs- und Kennwortwiederherstellungsdateien haben, die beim Onboarding des Sensors heruntergeladen wurden. Falls nicht, benötigen sie die Berechtigung „Azure-Sicherheitsadministrator“, „Mitwirkender im Abonnement“ oder „Abonnementbesitzer“, um diese Dateien im Azure Defender für IoT-Portal zu generieren.

### <a name="first-time-sign-in-and-activation-checklist"></a>Prüfliste für die erstmalige Anmeldung und Aktivierung

Ehe Sie sich an der Sensorkonsole anmelden, müssen Administratoren Zugriff auf Folgendes haben:

- Die IP-Adresse des Sensors, die bei der Installation festgelegt wurde.

- Anmeldeinformationen für die Anmeldung des Benutzers beim Sensor. Wenn Sie eine ISO-Datei für den Sensor heruntergeladen haben, verwenden Sie die Standardanmeldeinformationen, die Sie bei der Installation erhalten haben. Wir empfehlen, nach der Aktivierung einen neuen Benutzer des Typs *Administrator* anzulegen.

- Ein anfängliches Kennwort. Wenn Sie einen vorkonfigurierten Sensor von Arrow erworben haben, müssen Sie bei der Erstanmeldung ein Kennwort generieren.

- Die diesem Sensor zugeordnete Aktivierungsdatei. Die Datei wurde beim Onboarding des Sensors im Defender für IoT-Portal generiert und heruntergeladen.

- Ein von einer Zertifizierungsstelle signiertes SSL/TLS-Zertifikat, das Ihr Unternehmen benötigt.

### <a name="about-activation-files"></a>Informationen zu Aktivierungsdateien

Führen Ihren Sensor wurde in einem bestimmten Verwaltungsmodus ein Onboarding in Azure Defender für IoT durchgeführt:

| Modustyp | BESCHREIBUNG |
|--|--|
| **Modus „Mit Cloud verbunden“** | Die vom Sensor erkannten Informationen werden in der Sensorkonsole angezeigt. Warnungsinformationen werden außerdem über den IoT-Hub übermittelt und können für andere Azure-Dienste wie Azure Sentinel freigegeben werden. |
| **Modus „Lokal verbunden“** | Die vom Sensor erkannten Informationen werden in der Sensorkonsole angezeigt. Erkennungsinformationen werden auch für die lokale Verwaltungskonsole freigegeben, wenn der Sensor damit verbunden ist. |

Während des Onboardings wurde für diesen Sensor eine lokal oder mit der Cloud verbundene Aktivierungsdatei generiert und heruntergeladen. Die Aktivierungsdatei enthält Anweisungen für den Verwaltungsmodus des Sensors. *In jeden von Ihnen bereitgestellten Sensor muss eine eindeutige Aktivierungsdatei hochgeladen werden.*  Bei der ersten Anmeldung müssen Sie die entsprechende Aktivierungsdatei für diesen Sensor hochladen.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Onboarding von Sensoren im Azure Defender für IoT-Portal.":::

### <a name="about-certificates"></a>Informationen zu Zertifikaten

Nach der Sensorinstallation wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf die Sensorkonsole verwendet. Nachdem sich ein Administrator zum ersten Mal bei der Konsole angemeldet hat, wird dieser Benutzer zum Onboarding eines SSL/TLS-Zertifikats aufgefordert.

Zwei Sicherheitsstufen stehen zur Verfügung:

- Erfüllen spezifischer Zertifikat- und Verschlüsselungsanforderungen, die von Ihrer Organisation angefordert wurden, durch Hochladen des von der Zertifizierungsstelle signierten Zertifikats.
- Zulassen der Überprüfung zwischen Verwaltungskonsole und verbundenen Sensoren. Die Überprüfung erfolgt anhand einer Zertifikatssperrliste und des Ablaufdatums des Zertifikats. *Wenn bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen Verwaltungskonsole und Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt.* Diese Option wird nach der Installation standardmäßig aktiviert.  

Die Konsole unterstützt die folgenden Zertifikattypen:

- Private und Enterprise Key-Infrastruktur (Private PKI)

- Public Key-Infrastruktur (Public PKI)

- Lokal auf dem Gerät generiert (lokal selbstsigniert) 

  > [!IMPORTANT]
  > Es empfiehlt sich, nicht das standardmäßige selbstsignierte Zertifikat zu verwenden. Das Zertifikat ist nicht sicher und darf nur für Testumgebungen verwendet werden. Der Besitzer des Zertifikats kann nicht überprüft und die Sicherheit Ihres Systems nicht aufrechterhalten werden. Verwenden Sie diese Option auf keinen Fall in Produktionsnetzwerken.

### <a name="sign-in-and-activate-the-sensor"></a>Anmelden und Aktivieren des Sensors

So erfolgen Anmeldung und Aktivierung

1. Rufen Sie die Sensorkonsole in Ihrem Browser auf, indem Sie die bei der Installation festgelegte IP-Adresse verwenden. Das Dialogfeld zur Anmeldung wird geöffnet.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender für IoT-Sensor.":::

1. Geben Sie die Anmeldeinformationen ein, die Sie bei der Sensorinstallation definiert haben, oder klicken Sie auf **Password recovery** (Kennwortwiederherstellung). Wenn Sie einen vorkonfigurierten Sensor von Arrow erworben haben, generieren Sie zunächst ein Kennwort. Weitere Informationen zur Kennwortwiederherstellung finden Sie unter [Untersuchen von Kennwortfehlern bei der ersten Anmeldung](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Nach der Anmeldung wird das Dialogfeld **Aktivierung** geöffnet. Wählen Sie **Hochladen** aus, und wechseln Sie zur Aktivierungsdatei, die Sie beim Onboarding des Sensors heruntergeladen haben.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Wählen Sie „Hochladen“ aus, und wechseln Sie zur Aktivierungsdatei.":::

1. Wählen Sie den Link **Netzwerkkonfiguration des Sensors** aus, wenn Sie die Netzwerkkonfiguration des Sensors vor der Aktivierung ändern möchten. Weitere Informationen finden Sie unter [Aktualisieren der Netzwerkkonfiguration des Sensors vor der Aktivierung](#update-sensor-network-configuration-before-activation).

1. Akzeptieren Sie die Nutzungsbedingungen.

1. Wählen Sie **Aktivieren** aus. Das Dialogfeld „SSL/TLS-Zertifikat“ wird geöffnet.

1. Legen Sie einen Zertifikatnamen fest.
1. Laden Sie die CRT- und KEY-Datei hoch.
1. Geben Sie eine Passphrase ein, und laden Sie bei Bedarf eine PEM-Datei hoch.
1. Wählen Sie **Weiter** aus. Der Bildschirm zur Überprüfung wird geöffnet. Die Überprüfung zwischen Verwaltungskonsole und verbundenen Sensoren ist standardmäßig aktiviert.
1. Deaktivieren Sie den Umschalter **Enable system-wide validation** (Systemweite Überprüfung aktivieren), um die Überprüfung zu deaktivieren. Es wird empfohlen, die Überprüfung zu aktivieren.
1. Wählen Sie **Speichern** aus.  

Möglicherweise müssen Sie den Bildschirm aktualisieren, nachdem Sie das von einer Zertifizierungsstelle signierte Zertifikat hochgeladen haben.

Informationen zum Hochladen eines neuen Zertifikats, zu unterstützten Zertifikatsparametern und zum Arbeiten mit CLI-Zertifikatsbefehlen finden Sie unter [Verwalten einzelner Sensoren](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Aktualisieren der Netzwerkkonfiguration des Sensors vor der Aktivierung  

Die Netzwerkkonfigurationsparameter des Sensors wurden bei der Softwareinstallation oder beim Kauf eines vorkonfigurierten Sensors definiert. Die folgenden Parameter wurden definiert:

- IP-Adresse
- DNS
- Standardgateway
- Subnetzmaske
- Hostname

Sie möchten diese Informationen möglicherweise aktualisieren, ehe Sie den Sensor aktivieren. Sie müssen z. B. die von Arrow festgelegten vorkonfigurierten Parameter ändern. Sie können vor dem Aktivieren des Sensors auch Proxyeinstellungen festlegen.

So aktualisieren Sie die Netzwerkkonfiguration des Sensors

1. Wählen Sie im Dialogfeld **Aktivierung** den Link **Sensor Network Configuration** (Netzwerkkonfiguration des Sensors) aus.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Netzwerkkonfiguration des Sensors.":::

2. Die während der Installation definierten Parameter werden angezeigt. Es gibt auch eine Option zum Festlegen des Proxys. Ändern Sie Einstellungen nach Bedarf, und wählen Sie **Speichern** aus.

### <a name="subsequent-sign-ins"></a>Nachfolgende Anmeldungen

Nach der erstmaligen Aktivierung wird die Sensorkonsole von Azure Defender für IoT nach der Anmeldung geöffnet, ohne dass eine Aktivierungsdatei erforderlich ist. Sie benötigen nur Ihre Anmeldeinformationen.

Nach der Anmeldung wird die Azure Defender für IoT-Konsole geöffnet.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender für IoT-Konsole.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Anfängliches Setup und Lernmodus (für Administratoren)

Nach Ihrer ersten Anmeldung beginnt der Azure Defender für IoT-Sensor automatisch mit der Überwachung Ihres Netzwerks. Netzwerkgeräte werden in den Abschnitten mit der Gerätezuordnung und dem Gerätebestand angezeigt. Azure Defender für IoT beginnt mit der Erkennung und Warnung bei allen Sicherheits- und Betriebsvorfällen, die in Ihrem Netzwerk auftreten. Sie können anschließend Berichte und Abfragen auf Grundlage der erkannten Informationen erstellen.

Zunächst erfolgt diese Aktivität im Lernmodus, wodurch Ihr Sensor angewiesen wird, die übliche Aktivität Ihres Netzwerks zu erfassen. Der Sensor erfasst z. B. in Ihrem Netzwerk entdeckte Geräte, im Netzwerk erkannte Protokolle und zwischen bestimmten Geräten stattfindende Dateiübertragungen. Diese Aktivität wird zur Baselineaktivität Ihres Netzwerks.

### <a name="review-and-update-basic-system-settings"></a>Überprüfen und Aktualisieren grundlegender Systemeinstellungen

Überprüfen Sie die Systemeinstellungen des Sensors, um sicherzustellen, dass der Sensor für eine optimale Erkennung und Warnung konfiguriert ist.

Legen Sie die Systemeinstellungen des Sensors fest. Beispiel:

- Legen Sie ICS- (oder IoT-) und getrennte Subnetze fest.

- Legen Sie Portaliase für standortspezifische Protokolle fest.

- Bestimmen Sie die verwendeten VLANs und Namen.

- Wenn DHCP verwendet wird, bestimmen Sie zulässige DHCP-Bereiche.

- Bestimmen Sie entsprechend die Integration mit Active Directory und dem E-Mail-Server.

### <a name="disable-learning-mode"></a>Deaktivieren des Lernmodus

Nach Anpassen der Systemeinstellungen können Sie den Azure Defender für IoT-Sensor im Lernmodus betreiben, bis Sie das Gefühl haben, dass das System Ihre Netzwerkaktivität präzise widerspiegelt.

Der Lernmodus sollte je nach Größe und Komplexität Ihres Netzwerks etwa 2 bis 6 Wochen aktiv bleiben. Nach Deaktivieren des Lernmodus löst jede Aktivität, die von Ihrer Baselineaktivität abweicht, eine Warnung aus.

So deaktivieren Sie den Lernmodus

- Wählen Sie **Systemeinstellungen** aus, und deaktivieren Sie die Option **Learning** (Lernmodus).

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Erstmalige Anmeldung für Sicherheitsanalysten und Benutzer mit Leseberechtigung

Vergewissern Sie sich vor der Anmeldung, dass Sie über Folgendes verfügen:

- Die IP-Adresse des Sensors
- Von Ihrem Administrator bereitgestellte Anmeldeinformationen

## <a name="console-tools-overview"></a>Konsolentools: Übersicht

Sie greifen über das seitliche Menü auf Konsolentools zu.

**Navigation** 

| Fenster | Symbol | BESCHREIBUNG |
| -----------|--|--|
| Dashboard | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Zeigt eine intuitive Momentaufnahme des Sicherheitsstatus des Netzwerks. |
| Geräteübersicht | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Zeigt Netzwerkgeräte, Geräteverbindungen und -eigenschaften in einer Übersicht. Zum Anzeigen Ihres Netzwerks stehen verschiedene Optionen zum Zoomen, Hervorheben und Filtern zur Verfügung. |
| Gerätebestand | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | Im Gerätebestand wird eine umfangreiche Liste der von diesem Sensor erkannten Geräteattribute angezeigt. Folgende Optionen stehen zur Verfügung: <br /> Sortieren oder Filtern der Informationen gemäß den Tabellenfeldern und Prüfen der angezeigten gefilterten Informationen <br /> Exportieren von Informationen in eine CSV-Datei <br /> Importieren von Windows-Registrierungsdetails|
| Warnungen | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Zeigt Warnungen an, wenn gegen Richtlinien verstoßen wird, Abweichungen vom Baselineverhalten auftreten oder verdächtige Aktivitäten im Netzwerk erkannt werden. |
| Berichte | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Zeigt Berichte, die auf Data Mining-Abfragen basieren. |

**Analyse**

| Fenster| Symbol | BESCHREIBUNG |
|---|---|---|
| Zeitskala der Ereignisse | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Zeigt eine Zeitskala mit Informationen zu Warnungen, Netzwerkereignissen (zur Information) und Benutzervorgängen an, z. B. Anmelden und Löschen von Benutzern.|

**Navigation**

| Fenster | Symbol | BESCHREIBUNG |
|---|---|---|
| Data Mining | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Generiert umfassende und detaillierte Informationen zu den Geräten in Ihrem Netzwerk auf verschiedenen Ebenen. |
| Untersuchung | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Zeigt Trends und Statistiken in einer Vielzahl von Widgets. |
| Risikobewertung | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Zeigt das Fenster **Sicherheitsrisiken** an. |

**Administrator**

| Fenster | Symbol | BESCHREIBUNG |
|---|---|---|
| Benutzer | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Dient zum Festlegen von Benutzern und Rollen mit verschiedenen Zugriffsebenen. |
| Weiterleitung | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Leiten Sie Warnungsinformationen beispielsweise an Partner und interne Quellen (z. B. Azure Sentinel), die mit Defender for IoT integriert sind, an E-Mail-Adressen und Webhookserver weiter. <br /> Einzelheiten finden Sie unter [Weiterleiten von Warnungsinformationen](how-to-forward-alert-information-to-partners.md). |
| Systemeinstellungen | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Dient zum Konfigurieren der Systemeinstellungen. Sie können beispielsweise DHCP-Einstellungen festlegen, Details zum E-Mail-Server angeben oder Portaliase erstellen. |
| Importieren von Einstellungen | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Zeigt das Fenster **Importeinstellungen** an. Sie können an den Informationen eines Geräts manuell Änderungen vornehmen.<br /> Einzelheiten finden Sie unter [Importieren von Geräteinformationen](how-to-import-device-information.md). |

**Unterstützung**

| Fenster| Symbol | BESCHREIBUNG |
|----|---|---|
| Support | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/), um Hilfe zu erhalten. |

## <a name="next-steps"></a>Nächste Schritte

[Integrieren eines Sensors](getting-started.md#4-onboard-a-sensor)

[Verwalten von Sensoraktivierungsdateien](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Steuern des zu überwachenden Datenverkehrs](how-to-control-what-traffic-is-monitored.md)
