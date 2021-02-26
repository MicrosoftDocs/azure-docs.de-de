---
title: Aktivieren und Einrichten der lokalen Verwaltungskonsole
description: Durch die Aktivierung und Einrichtung der Verwaltungskonsole wird sichergestellt, dass Sensoren bei Azure registriert werden, Informationen an die lokale Verwaltungskonsole gesendet werden und die lokale Verwaltungskonsole Verwaltungsaufgaben auf verbundenen Sensoren ausführt.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d326a90ffb957604dba74982d8983acedc6ab85d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522579"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Aktivieren und Einrichten der lokalen Verwaltungskonsole 

Durch die Aktivierung und Einrichtung der lokalen Verwaltungskonsole wird Folgendes sichergestellt:

- Netzwerkgeräte, die Sie über verbundene Sensoren überwachen, werden bei einem Azure-Konto registriert.

- Sensoren senden Informationen an die lokale Verwaltungskonsole.

- Die lokale Verwaltungskonsole führt Verwaltungsaufgaben auf verbundenen Sensoren aus.

- Sie haben ein SSL-Zertifikat installiert.

## <a name="sign-in-for-the-first-time"></a>Erstmaliges Anmelden

So melden Sie sich bei der Verwaltungskonsole an:

- Öffnen Sie einen Webbrowser, und geben Sie die IP-Adresse und das Kennwort ein, die Sie während der Systeminstallation für die lokale Verwaltungskonsole erhalten haben. Wenn Sie Ihr Kennwort vergessen haben, wählen Sie **Kennwort wiederherstellen** aus, und zeigen Sie [Kennwortwiederherstellung](how-to-manage-the-on-premises-management-console.md#password-recovery) an.

## <a name="upload-an-activation-file"></a>Hochladen einer Aktivierungsdatei

Aktivieren Sie nach der erstmaligen Anmeldung die lokale Verwaltungskonsole, indem Sie im Azure Defender für IoT-Portal über die Seite **Preise** eine Aktivierungsdatei herunterladen. Diese Datei enthält die aggregierten zugesicherten Geräte, die während des Onboardingprozesses definiert wurden. **Zugesicherte Geräte** gibt die Anzahl von Geräten an, die Defender für IoT pro Abonnement überwachen wird.

So laden Sie eine Aktivierungsdatei hoch:

1. Wechseln Sie zur Defender für IoT-Seite **Preise**.
1. Wählen Sie die Registerkarte **Aktivierungsdatei für die Verwaltungskonsole herunterladen** aus. Die Aktivierungsdatei wird heruntergeladen.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Laden Sie die Aktivierungsdatei herunter.":::

1. Wählen Sie in der Verwaltungskonsole **Systemeinstellungen** aus.
1. Wählen Sie **Aktivierung** aus.
1. Wählen Sie **Datei auswählen** und dann die gespeicherte Datei aus.

Nach der erstmaligen Aktivierung könnte die Anzahl der überwachten Geräte die Anzahl der zugesicherten Geräte überschreiten, die während des Onboardings definiert wurden. Dies könnte beispielsweise geschehen, wenn Sie weitere Sensoren mit der Verwaltungskonsole verbinden. Bei einer Abweichung zwischen der Anzahl von überwachten Geräten und der Anzahl von zugesicherten Geräten wird in der Verwaltungskonsole eine Warnung angezeigt. In diesem Fall sollten Sie eine neue Aktivierungsdatei hochladen.

## <a name="set-up-a-certificate"></a>Einrichten eines Zertifikats

Nach der Installation der Verwaltungskonsole wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf die Konsole verwendet. Nachdem sich ein Administrator zum ersten Mal bei der Verwaltungskonsole angemeldet hat, wird dieser Benutzer aufgefordert, ein SSL/TLS-Zertifikat zu integrieren. 

Zwei Sicherheitsstufen stehen zur Verfügung:

- Erfüllen spezifischer Zertifikat- und Verschlüsselungsanforderungen, die von Ihrer Organisation angefordert wurden, durch Hochladen des von der Zertifizierungsstelle signierten Zertifikats.
- Zulassen der Überprüfung zwischen der Verwaltungskonsole und verbundenen Sensoren. Die Überprüfung wird anhand einer Zertifikatssperrliste und des Ablaufdatums des Zertifikats ausgewertet. *Wenn bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen der Verwaltungskonsole und dem Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt.* Diese Option wird nach der Installation standardmäßig aktiviert.  

Die Konsole unterstützt die folgenden Arten von Zertifikaten:

- Private und Enterprise Key-Infrastruktur (private PKI)

- Public Key-Infrastruktur (Public PKI)

- Lokal auf dem Gerät generiert (lokal selbstsigniert) 

  > [!IMPORTANT]
  > Wir empfehlen, dass Sie kein selbstsigniertes Zertifikat verwenden. Das Zertifikat ist nicht sicher und sollte nur für Testumgebungen verwendet werden. Der Besitzer des Zertifikats kann nicht überprüft und die Sicherheit Ihres Systems nicht aufrechterhalten werden. Verwenden Sie diese Option niemals bei Produktionsnetzwerken.

So laden Sie ein Zertifikat hoch:

1. Wenn Sie nach der Anmeldung dazu aufgefordert werden, definieren Sie einen Zertifikatnamen.
1. Laden Sie die CRT-Datei und die Schlüsseldatei hoch.
1. Geben Sie eine Passphrase ein, und laden Sie bei Bedarf eine PEM-Datei hoch.

Möglicherweise müssen Sie den Bildschirm aktualisieren, nachdem Sie das von einer Zertifizierungsstelle signierte Zertifikat hochgeladen haben.

So deaktivieren Sie die Überprüfung zwischen der Verwaltungskonsole und verbundenen Sensoren:

1. Wählen Sie **Weiter** aus.
1. Schalten Sie den Umschalter für **Enable system-wide validation** (Systemweite Überprüfung aktivieren) auf „Aus“.

Informationen zum Hochladen eines neuen Zertifikats, unterstützter Zertifikatdateien und zugehöriger Elemente finden Sie unter [Verwalten der lokalen Verwaltungskonsole](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Verbinden von Sensoren mit der lokalen Verwaltungskonsole

Sie müssen sicherstellen, dass Sensoren Informationen an die lokale Verwaltungskonsole senden und dass die lokale Verwaltungskonsole Sicherungen ausführen, Warnungen verwalten sowie andere Aktivitäten auf den Sensoren ausführen kann. Mit den folgenden Verfahren können Sie überprüfen, ob eine anfängliche Verbindung zwischen Sensoren und der lokalen Verwaltungskonsole hergestellt wird.

Zum Verbinden von Azure Defender für IoT-Sensoren mit der lokalen Verwaltungskonsole gibt es zwei Optionen:

- Herstellen einer Verbindung über die Sensorkonsole

- Herstellen einer Verbindung mithilfe von Tunneln

Nachdem Sie eine Verbindung hergestellt haben, müssen Sie mit diesen Sensoren einen Standort einrichten.

### <a name="connect-sensors-from-the-sensor-console"></a>Verbinden von Sensoren über die Sensorkonsole

So verbinden Sie bestimmte Sensoren über die Sensorkonsole mit der lokalen Verwaltungskonsole:

1. Wählen Sie im linken Bereich der Sensorkonsole **Systemeinstellungen** aus.

2. Wählen Sie **Verbindung mit Verwaltung** aus.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Screenshot des Statusfensters einer lokalen Verwaltungskonsole mit dem Status „Nicht verbunden“":::

3. Geben Sie im Textfeld **Adresse** die IP-Adresse der lokalen Verwaltungskonsole ein, mit der Sie eine Verbindung herstellen möchten.

4. Wählen Sie **Verbinden** aus. Der Status wird geändert:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Screenshot des Statusfensters einer lokalen Verwaltungskonsole mit dem Status „Verbunden“":::

### <a name="connect-sensors-by-using-tunneling"></a>Verbinden von Sensoren mithilfe von Tunneln

Ermöglichen Sie eine gesicherte Tunnelverbindung zwischen Organisationssensoren und der lokalen Verwaltungskonsole. Durch dieses Setup wird eine Interaktion mit der Organisationsfirewall umgangen und so die Angriffsfläche verringert.

Mithilfe von Tunneln können Sie die lokale Verwaltungskonsole über deren IP-Adresse und einen einzelnen Port (9000) mit einem beliebigen Sensor verbinden.

So richten Sie Tunneln in der lokalen Verwaltungskonsole ein:

- Melden Sie sich bei der lokalen Verwaltungskonsole an, und führen Sie die folgenden Befehle aus:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

So richten Sie Tunneln auf dem Sensor ein:

1. Öffnen Sie den TCP-Port 9000 manuell auf dem Sensor („network.properties“). Wenn der Port nicht geöffnet ist, lehnt der Sensor die Verbindung über lokale Verwaltungskonsole ab.

2. Melden Sie sich bei jedem Sensor an, und führen Sie die folgenden Befehle aus:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Einrichten eines Standorts

Die Standardzuordnung für Unternehmen bietet eine allgemeine Ansicht Ihrer Geräte auf mehreren Ebenen geografischer Standorte.

Möglicherweise wird eine Ansicht Ihrer Geräte benötigt, in der die Organisationsstruktur und die Benutzerberechtigungen komplex sind. In diesen Fällen könnte die Standorteinrichtung – zusätzlich zur Standardstruktur von Standort oder Zone – durch eine globale Organisationsstruktur bestimmt werden.

Zur Unterstützung dieser Umgebung müssen Sie eine globale Unternehmenstopologie erstellen, die auf den Geschäftseinheiten, Regionen, Standorten und Zonen Ihrer Organisation basiert. Außerdem müssen Sie die Benutzerzugriffsberechtigungen für diese Entitäten mithilfe von Zugriffsgruppen definieren.

Zugriffsgruppen ermöglichen eine bessere Kontrolle darüber, wo Benutzer Geräte auf der Defender für IoT-Plattform verwalten und analysieren.

### <a name="how-it-works"></a>Funktionsweise

Für jeden Standort können Sie eine Geschäftseinheit und eine Region definieren. Anschließend können Sie Zonen hinzufügen, die logische Entitäten in Ihrem Netzwerk sind. 

Für jede Zone sollten Sie mindestens einen Sensor zuweisen. Das fünfstufige Modell bietet die Flexibilität und Granularität, die für die Bereitstellung des Schutzsystems erforderlich sind, das die Struktur Ihrer Organisation widerspiegelt.

Sie können Ihre Standorte direkt aus einer beliebigen Kartenansicht heraus bearbeiten. Wenn Sie einen Standort aus einer Kartenansicht öffnen, wird die Anzahl der geöffneten Warnungen neben der jeweiligen Zone angezeigt.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Screenshot einer Karte der lokalen Verwaltungskonsole mit eingeblendeten Daten zu Berlin.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Das Diagramm zeigt Sensoren und eine regionale Beziehung.":::

So richten Sie einen Standort ein:

1. Fügen Sie neue Geschäftseinheiten hinzu, um die logische Struktur Ihrer Organisation widerzuspiegeln.

2. Fügen Sie neue Regionen hinzu, um die Regionen Ihrer Organisation widerzuspiegeln.

3. Fügen Sie einen Standort hinzu.

4. Fügen Sie einem Standort Zonen hinzu.

5. Verbinden Sie die Sensoren.

6. Weisen Sie einen Sensor Standortzonen zu.

So fügen Sie Geschäftseinheiten hinzu:

1. Wählen Sie in der Unternehmensansicht **Alle Standorte** > **Geschäftseinheiten verwalten** aus.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Screenshot der Ansicht „Geschäftseinheiten verwalten“":::

2. Geben Sie den Namen der neuen Geschäftseinheit ein, und wählen Sie **HINZUFÜGEN** aus.

So fügen Sie eine neue Region hinzu:

1. Wählen Sie in der Unternehmensansicht **Alle Regionen** > **Regionen verwalten** aus.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Screenshot der Ansicht „Regionen verwalten“":::

2. Geben Sie den neuen Regionsnamen ein, und wählen Sie **HINZUFÜGEN** aus.

So fügen Sie einen neuen Standort hinzu:

1. Wählen Sie in der Unternehmensansicht in der oberen Leiste :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: aus. Der Cursor wird als Pluszeichen ( **+** ) angezeigt.

2. Positionieren Sie das **+** auf dem neuen Standort, und wählen Sie ihn aus. Das Dialogfeld **Neuen Standort erstellen** wird geöffnet.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Screenshot der Ansicht „Neuen Standort erstellen“":::

3. Definieren Sie den Namen und die physische Adresse für den neuen Standort, und wählen Sie **SPEICHERN** aus. Der neue Standort wird in der Standortkarte angezeigt.

So löschen Sie einen Standort:

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone löschen** aus. Das Bestätigungsfeld wird angezeigt, und es wird überprüft, ob Sie den Standort löschen möchten.

2. Wählen Sie im Bestätigungsfeld **JA** aus. Das Bestätigungsfeld wird geschlossen, und das Fenster **Standortverwaltung** wird ohne den von Ihnen gelöschten Standort angezeigt.

## <a name="create-enterprise-zones"></a>Erstellen von Unternehmenszonen

Zonen sind logische Entitäten, mit denen Sie Geräte innerhalb eines Standorts entsprechend verschiedenen Merkmalen in Gruppen aufteilen können. So können Sie beispielsweise Gruppen für Produktionslinien, Unterstationen, Standortbereiche oder Gerätetypen erstellen. Sie können Zonen basierend auf allen Merkmalen definieren, die für Ihre Organisation geeignet sind.

Sie konfigurieren Zonen im Rahmen des Standortkonfigurationsprozesses.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Screenshot der Ansicht „Standortverwaltungszonen“":::

In der folgenden Tabelle werden die Parameter im Fenster **Standortverwaltung** beschrieben.

| Parameter | Beschreibung |
|--|--|
| Name | Der Name des Sensors. Sie können diesen Namen nur über den Sensor ändern. Weitere Informationen finden Sie im Benutzerhandbuch zu Defender für IoT. |
| IP | Die IP-Adresse des Sensors. |
| Version | Die Sensorversion. |
| Konnektivität | Der Konnektivitätsstatus des Sensors. Der Status kann **Verbunden** oder **Getrennt** lauten. |
| Letztes Upgrade | Das Datum des letzten Upgrades. |
| Upgradestatus | Die Statusanzeige zeigt den Status des Upgradeprozesses so an:<br />– Paket wird hochgeladen.<br />– Installation wird vorbereitet.<br />– Prozesse werden angehalten.<br />– Daten werden gesichert.<br />– Momentaufnahme wird erstellt.<br />– Konfiguration wird aktualisiert.<br />– Abhängigkeiten werden aktualisiert.<br />– Bibliotheken werden aktualisiert.<br />– Datenbanken werden gepatcht.<br />– Prozesse werden gestartet.<br />– Systemintegrität wird überprüft.<br />– Überprüfung war erfolgreich.<br />– Erfolg<br />– Fehler<br />– Upgrade wurde gestartet.<br />– Installation wird gestartet.ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Falls Sie Informationen zum Upgrade benötigen, bitten Sie den [Microsoft-Support](https://support.microsoft.com/) um Hilfe. |
| Geräte | Die Anzahl von OT-Geräten, die der Sensor überwacht |
| Alerts | Die Anzahl von Warnungen auf dem Sensor. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Ermöglicht das Zuweisen eines Sensors zu Zonen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Ermöglicht das Löschen eines nicht verbundenen Sensors aus dem Standort. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Gibt an, wie viele Sensoren zurzeit mit der Zone verbunden sind. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Gibt an, wie viele OT-Ressourcen zurzeit mit der Zone verbunden sind. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Gibt die Anzahl der Warnungen an, die von der Zone zugewiesenen Sensoren gesendet werden. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Hebt die Zuweisung von Sensoren zu Zonen auf. |

So fügen Sie eine Zone zu einem Standort hinzu:

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone hinzufügen** aus. Das Dialogfeld **Neue Zone erstellen** wird angezeigt.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Screenshot der Ansicht „Neue Zone erstellen“":::

2. Geben Sie den Zonennamen ein.

3. Geben Sie eine Beschreibung für die neue Zone ein, in der die Merkmale, mit denen Sie den Standort in Zonen aufgeteilt haben, eindeutig angegeben werden.

4. Wählen Sie **SAVE** (SPEICHERN) aus. Die neue Zone wird im Fenster **Standortverwaltung** unter dem Standort angezeigt, zu dem diese Zone gehört.

So bearbeiten Sie eine Zone:

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone bearbeiten** aus. Das Dialogfeld **Zone bearbeiten** wird angezeigt.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Screenshot des Dialogfelds „Zone bearbeiten“":::

2. Bearbeiten Sie die Zonenparameter, und wählen Sie **SPEICHERN** aus.

So löschen Sie eine Zone

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone löschen** aus.

2. Wählen Sie im Bestätigungsfeld **JA** aus.

So filtern Sie nach dem Konnektivitätsstatus:

- Wählen Sie in der oberen linken Ecke neben **Konnektivität** :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: und dann eine der folgenden Optionen aus:

  - **All**: Zeigt alle Sensoren an, die an diese lokale Verwaltungskonsole berichten.

  - **Verbunden**: Zeigt nur verbundene Sensoren an.

  - **Getrennt**: Zeigt nur getrennte Sensoren an.

So filtern Sie nach dem Upgradestatus:

- Wählen Sie in der oberen linken Ecke neben **Upgradestatus** :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: und dann eine der folgenden Optionen aus:

  - **All**: Zeigt alle Sensoren an, die an diese lokale Verwaltungskonsole berichten.

  - **Gültig**: Zeigt Sensoren mit einem gültigen Upgradestatus an.

  - **In Bearbeitung**: Zeigt Sensoren an, die gerade aktualisiert werden.

  - **Fehler:** Zeigt Sensoren an, deren Upgradeprozess fehlgeschlagen ist.

## <a name="assign-sensors-to-zones"></a>Zuweisen von Sensoren zu Zonen

Sie müssen für jede Zone Sensoren zuweisen, die eine lokale Datenverkehrsanalyse durchführen und Warnungen ausgeben. Sie können nur die Sensoren zuweisen, die mit der lokalen Verwaltungskonsole verbunden sind.

So weisen Sie einen Sensor zu:

1. Wählen Sie **Standortverwaltung** aus. Die nicht zugewiesenen Sensoren werden in der oberen linken Ecke des Dialogfelds angezeigt.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Screenshot der Ansicht „Nicht zugewiesene Sensoren“":::

2. Überprüfen Sie, ob der Status für **Konnektivität** „Verbunden“ lautet. Falls das nicht zutrifft, finden Sie Informationen zum Herstellen einer Verbindung unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](#connect-sensors-to-the-on-premises-management-console). 

3. Wählen Sie :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: für den Sensor aus, den Sie zuweisen möchten.

4. Wählen Sie im Dialogfeld **Sensor zuweisen** die Geschäftseinheit, die Region, den Standort und die Zone aus, die zugewiesen werden sollen.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Screenshot der Ansicht „Sensor zuweisen“":::

5. Wählen Sie **ZUWEISEN** aus.

So heben Sie die Zuweisung eines Sensors auf und löschen ihn:

1. Trennen Sie die Verbindung des Sensors mit der lokalen Verwaltungskonsole. Informationen dazu finden Sie unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](#connect-sensors-to-the-on-premises-management-console).

2. Wählen Sie im Fenster **Standortverwaltung** den gewünschten Sensor und dann :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: aus. Nach kurzer Zeit wird der Sensor in der Liste der nicht zugewiesenen Sensoren angezeigt.

3. Zum Löschen des nicht zugewiesenen Sensors aus dem Standort wählen Sie ihn aus der Liste der nicht zugewiesenen Sensoren und dann :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: aus.

## <a name="next-steps"></a>Nächste Schritte

[Behandeln von Problemen mit dem Sensor und der lokalen Verwaltungskonsole](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
