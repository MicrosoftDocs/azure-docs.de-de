---
title: Problembehandlung in Azure FarmBeats
description: In diesem Artikel wird beschrieben, wie Sie Probleme in Azure FarmBeats behandeln.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: dd0ed78c56e4d656a2ecee6395d831ed093e85b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001608"
---
# <a name="troubleshoot-azure-farmbeats"></a>Problembehandlung in Azure FarmBeats

In diesem Artikel finden Sie Lösungen für häufige Probleme mit Azure FarmBeats. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an unser [Supportforum für Fragen und Antworten](/answers/topics/azure-farmbeats.html), oder senden Sie eine E-Mail an farmbeatssupport@microsoft.com.

> [!NOTE]
  > Wenn Sie FarmBeats im April installiert haben und Ihre Aufträge mit einer leeren Fehlermeldung fehlschlagen, wurde Ihrer Installation möglicherweise kein Batchkontingent zugeteilt, um die Unterstützung für kritische medizinische und Sicherheitsorganisationen zu priorisieren. Weitere Informationen finden Sie [hier](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) . Sie müssen anfordern, dass VMs dem Batch-Konto zugeteilt werden, um Aufträge erfolgreich ausführen zu können.

## <a name="install-issues"></a>Installationsprobleme

  > [!NOTE]
  > Wenn Sie die Installation aufgrund eines Fehlers neu starten, stellen Sie sicher, dass Sie die **Ressourcengruppe** löschen oder alle Ressourcen aus der Ressourcengruppe löschen, bevor Sie die Installation erneut auslösen.

### <a name="invalid-sentinel-credentials"></a>Ungültige Sentinel-Anmeldeinformationen

Die bei der Installation angegebenen Sentinel-Anmeldeinformationen sind falsch. Starten Sie die Installation mit den richtigen Anmeldeinformationen neu.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Das regionale Kontokontingent für Batch-Konten wurde für das angegebene Abonnement erreicht

Erhöhen Sie das Kontingent, oder löschen Sie die nicht verwendeten Batch-Konten, und starten Sie die Installation neu.

### <a name="invalid-resource-group-location"></a>Ungültiger Speicherort für Ressourcengruppe

Stellen Sie sicher, dass der Standort der **Ressourcengruppe** mit der bei der Installation angegebenen **Region** übereinstimmt.

### <a name="other-install-issues"></a>Weitere Installationsprobleme

Kontaktieren Sie uns mit den folgenden Details:

- Ihre Abonnement-ID
- Ressourcengruppenname
- Befolgen Sie die folgenden Anweisungen, um die Protokolldatei zum Bereitstellungsfehler anzufügen:

    1. Navigieren Sie im Azure-Portal zur **Ressourcengruppe**.

    2. Wählen Sie auf der linken Seite im Abschnitt **Einstellungen** die Option **Bereitstellungen** aus.

    3. Navigieren für alle Bereitstellungen, für die **Fehlgeschlagen** angezeigt wird, bis zu den Details, und laden Sie die Bereitstellungsdetails herunter. Fügen Sie diese Datei an die E-Mail an.

## <a name="sensor-telemetry"></a>Sensortelemetrie

### <a name="cant-view-telemetry-data"></a>Telemetriedaten können nicht angezeigt werden

**Symptom**: Geräte oder Sensoren sind bereitgestellt, und Sie haben FarmBeats mit Ihrem Gerätepartner verknüpft, können aber keine Telemetriedaten aus FarmBeats abrufen oder anzeigen.

**Korrekturmaßnahme**:

1. Wechseln Sie zu Ihrer FarmBeats-Ressourcengruppe.
2. Wählen Sie den **Event Hub**-Namespace aus („sensor-partner-eh-namespace-xxxx“), klicken Sie auf „Event Hubs“, und überprüfen Sie die Anzahl von eingehenden Nachrichten in dem Event Hub, der dem Partner zugewiesen ist.
3. Führen Sie einen der folgenden Schritte aus:

   - Wenn *keine eingehenden Nachrichten* vorhanden sind, wenden Sie sich an Ihren Gerätepartner.  
   - Wenn es *eingehende Nachrichten* gibt, kontaktieren Sie uns mit Ihren Datenhub- und Accelerator-Protokollen und der erfassten Telemetrie.

Informationen zum Herunterladen von Protokollen finden Sie im Abschnitt [Manuelles Erfassen von Protokollen](#collect-logs-manually).  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Sie können keine Telemetriedaten anzeigen, nachdem Sie historische/Streamingdaten von Ihren Sensoren erfasst haben.

**Symptom**: Geräte oder Sensoren werden bereitgestellt, und Sie haben die Geräte/Sensoren in FarmBeats erstellt und Telemetrie erfasst und an EventHub gesendet, aber Sie können keine Telemetriedaten in FarmBeats abrufen oder anzeigen.

**Korrekturmaßnahme**:

1. Stellen Sie sicher, dass Sie die Partnerregistrierung korrekt durchgeführt haben. Um dies zu überprüfen, wechseln Sie zu Ihrem Datenhub-Swagger, navigieren zur Partner-API, führen ein „Get“ aus und sehen nach, ob der Partner registriert ist. Andernfalls führen Sie die folgenden [Schritte](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) aus, um Partner hinzuzufügen.

2. Stellen Sie sicher, dass Sie das richtige Telemetriemeldungsformat verwendet haben:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Die Azure Event Hubs-Verbindungszeichenfolge liegt nicht vor

**Korrekturmaßnahme**:

1. Wechseln Sie in Datahub Swagger zur Partner-API.
2. Wählen Sie **Abrufen** > **Testen** > **Ausführen** aus.

> [!NOTE]
> Die Partner-ID des Sensorpartners, an dem Sie interessiert sind.

3. Wechseln Sie zurück zur Partner-API, und wählen Sie **Get/\<ID>** aus.
4. Geben Sie die Partner-ID aus Schritt 3 an, und wählen Sie dann **Ausführen** aus.

   Die API-Antwort sollte die Event Hubs-Verbindungszeichenfolge enthalten.

### <a name="device-appears-offline"></a>Das Gerät wird als offline angezeigt

**Symptome:** Geräte sind installiert, und Sie haben FarmBeats mit Ihrem Gerätepartner verknüpft. Die Geräte sind online und senden Telemetriedaten, werden aber als offline angezeigt.

**Korrekturmaßnahme** Für dieses Gerät ist kein Berichtsintervall konfiguriert. Um das Berichtsintervall festzulegen, wenden Sie sich an den Hersteller Ihres Geräts. 

### <a name="error-deleting-a-device"></a>Fehler beim Löschen eines Geräts

Während Sie ein Gerät löschen, tritt möglicherweise eines der folgenden häufigen Fehlerszenarios auf:  

**Meldung**: „Auf das Gerät wird in Sensoren verwiesen: Dem Gerät ist mindestens ein Sensor zugeordnet. Löschen Sie den Sensor bzw. die Sensoren und dann das Gerät.“  

**Bedeutung**: Dem Gerät sind mehrere in dem landwirtschaftlichen Betrieb bereitgestellte Sensoren zugeordnet.

**Korrekturmaßnahme**:  

1. Löschen Sie die Sensoren, die dem Gerät über den Accelerator zugeordnet sind.  
2. Falls Sie die Sensoren einem anderen Gerät zuordnen möchten, bitten Sie Ihren Gerätepartner, dasselbe zu tun.  
3. Löschen Sie das Gerät mithilfe eines `DELETE API`-Aufrufs, und legen Sie den force-Parameter auf *true* fest.  

**Meldung**: „Auf das Gerät wird in Geräten als ParentDeviceId verwiesen: Diesem Gerät sind ein oder mehrere Geräte als untergeordnete Geräte zugeordnet. Löschen Sie diese Geräte, und löschen Sie dann das Gerät.“  

**Bedeutung**: Ihrem Gerät sind andere Geräte zugeordnet.  

**Korrekturmaßnahme**:

1. Löschen Sie die Geräte, die diesem spezifischen Gerät zugeordnet sind.  
2. Löschen Sie das spezifische Gerät.  

    > [!NOTE]
    > Sie können ein Gerät nicht löschen, solange diesem Sensoren zugeordnet sind. Weitere Informationen zum Löschen von zugeordneten Sensoren finden Sie im Abschnitt **Löschen von Sensoren** von [Abrufen von Sensordaten von Sensorpartnern](get-sensor-data-from-sensor-partner.md).
    > Partner haben keine Berechtigung zum Löschen eines Geräts oder Sensors. Nur Administratoren haben die Löschberechtigung.

## <a name="issues-with-jobs"></a>Probleme mit Aufträgen

### <a name="farmbeats-internal-error"></a>Interner FarmBeats-Fehler

**Meldung**: „Interner FarmBeats-Fehler. Weitere Informationen finden Sie im Leitfaden zur Problembehandlung.“

**Korrekturmaßnahme** Dieses Problem könnte durch einen temporären Ausfall in der Datenpipeline verursacht werden. Erstellen Sie den Auftrag erneut. Wenn der Fehler weiterhin auftritt, wenden Sie sich mit der Fehlermeldung/den Protokollen an uns.

## <a name="accelerator-troubleshooting"></a>Problembehandlung bei Accelerators

### <a name="access-control"></a>Zugriffssteuerung

**Problem:** Beim Hinzufügen einer Rollenzuweisung erhalten Sie eine Fehlermeldung.

**Meldung**: „Es wurden keine übereinstimmenden Benutzer gefunden.“

**Korrekturmaßnahme** Überprüfen Sie die E-Mail-ID, für die Sie eine Rollenzuweisung hinzufügen möchten. Die E-Mail-ID muss exakt mit der ID übereinstimmen, die für diesen Benutzer in Active Directory registriert ist. Wenn der Fehler weiterhin auftritt, wenden Sie sich mit der Fehlermeldung/den Protokollen an uns.

### <a name="unable-to-log-in-to-accelerator"></a>Anmeldung beim Accelerator nicht möglich

**Meldung**: „Fehler: Sie sind nicht zum Aufrufen des Diensts berechtigt. Wenden Sie sich an den Administrator, um die entsprechende Autorisierung zu erhalten.“

**Korrekturmaßnahme** Bitten Sie den Administrator, Sie für den Zugriff auf die FarmBeats-Bereitstellung zu autorisieren. Dies kann mit einer POST-Anweisung der RoleAssignment-APIs oder über die Zugriffssteuerung im Bereich **Einstellungen** im Accelerator erfolgen.  

Wenn Ihnen bereits der Zugriff gewährt wurde und dieser Fehler auftritt, versuchen Sie es erneut, indem Sie die Seite aktualisieren. Wenn der Fehler weiterhin auftritt, wenden Sie sich mit der Fehlermeldung/den Protokollen an uns.

![Screenshot: Autorisierungsfehler](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator-Probleme  

**Problem:** Sie haben eine Accelerator-Fehlermeldung unbestimmter Ursache empfangen.

**Meldung**: „Fehler: Unbekannter Fehler.“

**Korrekturmaßnahme** Dieser Fehler tritt auf, wenn die Seite zu lange im Leerlauf belassen wurde. Aktualisieren Sie die Seite. Wenn der Fehler weiterhin auftritt, wenden Sie sich mit der Fehlermeldung/den Protokollen an uns.

**Problem:** Der FarmBeats-Accelerator zeigt auch nach einem Upgrade von FarmBeatsDeployment nicht die neueste Version an.

**Korrekturmaßnahme** Dieser Fehler tritt auf, weil ein Dienstworker im Browser persistent gespeichert wurde. Gehen Sie folgendermaßen vor:

1. Schließen Sie alle Registerkarten des Browsers, in denen der Accelerator geöffnet ist, und schließen Sie das Browserfenster.
2. Starten Sie eine neue Instanz des Browsers, und laden Sie den Accelerator-URI neu. Mit dieser Aktion wird die neue Accelerator-Version geladen.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Probleme im Zusammenhang mit Bildern

### <a name="wrong-username-or-password"></a>Falscher Benutzername oder falsches Kennwort

**Auftragsfehlermeldung**: „Für den Zugriff auf diese Ressource ist eine vollständige Authentifizierung erforderlich.“

**Korrekturmaßnahme**: Führen Sie eines der folgenden Verfahren aus:

- Aktualisieren Sie FarmBeats mit dem richtigen Benutzernamen/Kennwort unter Verwendung der folgenden Schritte, und wiederholen Sie den Auftrag.

  **Aktualisieren des Sentinel-Benutzernamens**

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
    2. Suchen Sie im Feld **Suche** nach der Ressourcengruppe für den FarmBeats-Datenhub.
    3. Wählen Sie Storage-Kontospeicher***** > **Container** > **batch-prep-files** > **to_vm** > **config.ini**
    4. Wählen Sie **Bearbeiten** aus.
    5. Aktualisieren Sie den Benutzernamen im Abschnitt sentinel_account.

  **Aktualisieren des Sentinel-Kennworts**

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
    2. Suchen Sie im Feld **Suche** nach der Ressourcengruppe für den FarmBeats-Datenhub.
    3. Wählen Sie keyvault-*****
    4. Wählen Sie unter „Einstellungen“ die Option „Zugriffsrichtlinien“.
    5. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.
    6. Wählen Sie **Verwaltung von Geheimnissen** für „Anhand einer Vorlage konfigurieren“, und fügen Sie sich zu „Prinzipal“ hinzu.
    7. Wählen Sie auf der Seite **Zugriffsrichtlinien** zunächst **Hinzufügen** und dann **Speichern** aus.
    8. Wählen Sie unter **Einstellungen** die Option **Geheimnisse** aus.
    9. Wählen Sie **Sentinel-Kennwort** aus.
    10. Erstellen Sie eine neue Version des Werts, und aktivieren Sie sie.

- Führen Sie den fehlerhaften Auftrag erneut aus, oder führen Sie einen Satellitenauftrag zur Indizierung für einen Datumsbereich von 5–7 Tagen aus, und überprüfen Sie dann, ob der Auftrag erfolgreich ausgeführt wird.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel-Hub: Falsche URL oder kein Zugriff auf die Site möglich

**Auftragsfehlermeldung**: „Leider ist ein Problem aufgetreten. Die Seite, auf die Sie versucht haben zuzugreifen, ist (vorübergehend) nicht verfügbar.“

**Korrekturmaßnahme**:

1. Öffnen Sie [Sentinel](https://scihub.copernicus.eu/dhus/) in Ihrem Browser, um zu sehen, ob auf die Website zugegriffen werden kann.
2. Wenn nicht auf die Website zugegriffen werden kann, prüfen Sie, ob eine Firewall, ein Unternehmensnetzwerk oder eine andere blockierende Software den Zugriff auf die Website verhindert, und führen Sie dann die erforderlichen Schritte aus, um die Sentinel-URL zuzulassen. 
3. Führen Sie den fehlgeschlagenen Auftrag erneut aus, oder führen Sie einen Satellitenauftrag zur Indizierung für einen Datumsbereich von 5–7 Tagen aus, und prüfen Sie dann, ob der Auftrag erfolgreich ausgeführt wird.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel-Server: Zu Wartungszwecken heruntergefahren

**Auftragsfehlermeldung**: „Der Copernicus Open Access-Hub ist bald wieder verfügbar! Wir führen derzeit Wartungsarbeiten durch. Der Server ist in Kürze wieder online.“ 

**Korrekturmaßnahme**:

Dieses Problem kann auftreten, wenn auf dem Sentinel-Server Wartungsaktivitäten ausgeführt werden.

1. Wenn ein Auftrag oder eine Pipeline wegen Wartungsarbeiten fehlschlägt, übermitteln Sie den Auftrag nach einiger Zeit erneut. 

   Informationen zu geplanten oder ungeplanten Wartungsaktivitäten für Sentinel finden Sie auf der Site [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  

2. Führen Sie den fehlgeschlagenen Auftrag erneut aus, oder führen Sie einen Satellitenauftrag zur Indizierung für einen Datumsbereich von 5–7 Tagen aus, und prüfen Sie dann, ob der Auftrag erfolgreich ausgeführt wird.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Maximale Anzahl von Verbindungen erreicht

**Auftragsfehlermeldung**: „Die maximale Anzahl von zwei gleichzeitigen Flows wurde durch den Benutzer ‚\<username>‘ erreicht.“

**Bedeutung**: Wenn ein Auftrag fehlschlägt, weil die maximale Anzahl von Verbindungen erreicht wurde, wird dasselbe Sentinel-Konto für mehrere Aufträge verwendet.

**Korrekturmaßnahme**: Probieren Sie einen der folgenden Schritte aus:

* Warten Sie, bis die anderen Aufträge beendet sind, ehe Sie den fehlgeschlagenen Auftrag wiederholen.
* Erstellen Sie ein neues Sentinel-Konto, und aktualisieren Sie dann den Sentinel-Benutzernamen und das Kennwort in FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Sentinel-Server: Verbindung verweigert

**Auftragsfehlermeldung**: „Der Server hat die Verbindung unter http://172.30.175.69:8983/solr/dhus verweigert.“

**Korrekturmaßnahme**: Dieses Problem kann auftreten, wenn auf dem Sentinel-Server Wartungsaktivitäten ausgeführt werden.

1. Wenn ein Auftrag oder eine Pipeline wegen Wartungsarbeiten fehlschlägt, übermitteln Sie den Auftrag nach einiger Zeit erneut.

   Informationen zu geplanten oder ungeplanten Wartungsaktivitäten für Sentinel finden Sie auf der Site [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  

2. Führen Sie den fehlgeschlagenen Auftrag erneut aus, oder führen Sie einen Satellitenauftrag zur Indizierung für einen Datumsbereich von 5–7 Tagen aus, und prüfen Sie dann, ob der Auftrag erfolgreich ausgeführt wird.

### <a name="soil-moisture-map-has-white-areas"></a>Die Karte „Soil Moisture“ weist weiße Bereiche auf.

**Problem:** Die Karte **Soil Moisture** wurde generiert, aber sie weist größtenteils weiße Bereiche auf.

**Korrekturmaßnahme**: Dieses Problem kann auftreten, wenn die Satellitenindizes, die für den Zeitraum generiert wurden, für den die Karte angefordert wurde, kleinere NDVI-Werte als 0,3 haben. Weitere Informationen finden Sie im [technischen Handbuch von Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Wiederholen Sie den Auftrag für einen anderen Datumsbereich, und überprüfen Sie, ob die NDVI-Werte in den Satellitenindizes größer als 0,3 sind.

## <a name="collect-logs-manually"></a>Manuelles Erfassen von Protokollen

[Installieren Sie Azure Storage-Explorer, und stellen Sie ihn bereit]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Erfassen von Azure Data Factory-Auftragsprotokollen oder App Service-Protokollen im Datenhub

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie im Feld **Suche** nach der Ressourcengruppe für den FarmBeats-Datenhub.
3. Suchen Sie im Dashboard **Ressourcengruppe** nach dem Speicherkonto *datahublogs\** . Beispiel: *datahublogsmvxmq*.  
4. Wählen Sie in der Spalte **Name** das Speicherkonto aus, um das Dashboard **Speicherkonto** anzuzeigen.
5. Wählen Sie im Bereich **datahublogs\* *_ die Option _* In Explorer öffnen** aus, um die Anwendung **Azure Storage-Explorer öffnen** anzuzeigen.
6. Wählen Sie im linken Bereich **Blobcontainer** und dann **job-logs** für Azure Data Factory-Protokolle oder **appinsights-logs** für App Service-Protokolle aus.
7. Wählen Sie **Herunterladen** aus, und laden Sie die Protokolle in einen lokalen Ordner auf Ihrem Computer herunter.

    ![Screenshot: Heruntergeladene Protokolldateien](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Erfassen von Azure Data Factory-Auftragsprotokollen oder App Service-Protokollen für Accelerator

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie im Feld **Suche** nach der Ressourcengruppe für den FarmBeats-Accelerator.
3. Suchen Sie im Dashboard **Ressourcengruppe** nach dem Speicherkonto *storage\** . Beispiel: *storagedop4k\** .
4. Wählen Sie das Speicherkonto in der Spalte **Name** aus, um das Dashboard **Speicherkonto** anzuzeigen.
5. Wählen Sie im Bereich **storage\* *_ die Option _* In Explorer öffnen** aus, um die Anwendung Azure Storage-Explorer zu öffnen.
6. Wählen Sie im linken Bereich **Blobcontainer** und dann **job-logs** für Azure Data Factory-Protokolle oder **appinsights-logs** für App Service-Protokolle aus.
7. Wählen Sie **Herunterladen** aus, und laden Sie die Protokolle in einen lokalen Ordner auf Ihrem Computer herunter.

## <a name="high-cpu-usage"></a>Hohe CPU-Auslastung

**Fehler:** Sie erhalten eine E-Mail-Benachrichtigung mit einer **Warnung zu einer hohen CPU-Auslastung**.

**Korrekturmaßnahme**:

1. Wechseln Sie zur Ressourcengruppe für Ihren FarmBeats-Datenhub.
2. Wählen Sie den **App-Dienst** aus.  
3. Wechseln Sie zur Seite [App Service-Preise](https://azure.microsoft.com/pricing/details/app-service/windows/) (Hochskalieren), und wählen Sie einen geeigneten Tarif aus.

## <a name="weather-data-job-failures"></a>Fehler bei Wetterdatenauftrag

**Fehler:** Sie führen Aufträge aus, um Wetterdaten zu erhalten, aber bei einem Auftrag tritt ein Fehler auf.

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Sammeln von Protokollen zur Behandlung von Fehlern bei Wetterdatenaufträgen

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe FarmBeats.
2. Klicken Sie auf den Data Factory-Dienst, der Teil der Ressourcengruppe ist. Der Dienst enthält ein Tag „sku: Datahub“.

> [!NOTE]
> Um die Tags der Dienste in der Ressourcengruppe anzuzeigen, klicken Sie auf „Spalten bearbeiten“, und fügen Sie der Ressourcengruppenansicht „Tags“ hinzu.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Screenshot: Hervorgehobenes Tag „sku:Datahub“":::

3. Klicken Sie auf der Seite „Übersicht“ der Data Factory auf **Erstellen und überwachen**. Im Browser wird eine neue Registerkarte geöffnet. Klicken Sie auf **Überwachen**.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Screenshot: Hervorgehobene Menüoption „Überwachen“":::

4. Es wird eine Liste der Pipelineausführungen angezeigt, die Teil der Ausführung des Wetterauftrags sind. Klicken Sie auf den Auftrag, für den Sie Protokolle sammeln möchten.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Screenshot: Hervorgehobene Menüoption „Pipelineausführungen“ und ausgewählter Auftrag":::

5. Auf der Übersichtsseite der Pipeline wird die Liste der Aktivitätsausführungen angezeigt. Notieren Sie sich die Ausführungs-IDs der Aktivitäten, für die Sie Protokolle sammeln möchten.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Screenshot: Liste der Aktivitätsausführungen":::

6. Wechseln Sie zurück zu ihrer FarmBeats-Ressourcengruppe im Azure-Portal, und klicken Sie auf das Speicherkonto mit dem Namen **datahublogs-XXXX**.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Screenshot: Hervorgehobenes Speicherkonto mit dem Namen „datahublogs-XXXX“":::

7. Klicken Sie auf **Container** -> **adfjobs**. Geben Sie im Suchfeld die Ausführungs-ID des Auftrags ein, die Sie oben in Schritt 5 notiert haben.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="FarmBeats-Projekt":::

8. Das Suchergebnis enthält den Ordner mit den Protokollen, die sich auf den Auftrag beziehen. Laden Sie die Protokolle herunter, und senden Sie sie zur Unterstützung beim Debuggen des Problems an farmbeatssupport@microsoft.com.