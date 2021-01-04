---
title: 'Azure IoT-Konnektor für FHIR (Vorschauversion): Leitfaden zur Problembehandlung und Anleitungen'
description: Informationen zur Problembehandlung bei häufigen Fehlermeldungen zu Azure IoT-Konnektor für FHIR (Vorschauversion), zu den damit verbundenen Bedingungen und zum Kopieren von Zuordnungsdateien
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 403b6656a47f56508682dcda2438a85d513fbfb1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630497"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT-Konnektor für FHIR (Vorschauversion): Leitfaden zur Problembehandlung

Dieser Artikel enthält Schritte zur Problembehandlung bei häufigen Fehlermeldungen und Bedingungen zum Azure IoT-Konnektor für Fast Healthcare Interoperability Resources (FHIR&#174;)*.  

Außerdem erfahren Sie, wie Sie Kopien der JSON-Datei für Konvertierungszuordnung zu Azure IoT-Konnektor für FHIR erstellen (z. B.: „Gerät“ und „FHIR“).  

Sie können die Kopien der JSON-Datei für Konvertierungszuordnung zur Bearbeitung und Archivierung außerhalb des Azure-Portals verwenden.  

> [!TIP]
> Wenn Sie ein [Azure Technical Support](https://azure.microsoft.com/support/create-ticket/)-Ticket zu Azure IoT-Konnektor für FHIR öffnen, müssen Sie unbedingt Kopien Ihrer JSON-Datei für Konvertierungszuordnung als Hilfe bei der Problembehandlung einbeziehen.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>JSON-Vorlagenüberprüfungen für die Konvertierungszuordnung vom Typ „Gerät“ und „FHIR“ für Azure IoT-Konnektor für FHIR (Vorschau)
In diesem Abschnitt erfahren Sie mehr über den Überprüfungsprozess, den Azure IoT-Konnektor für FHIR durchführt, um die JSON-Vorlagen für die Konvertierungszuordnung vom Typ „Gerät“ und „FHIR“ zu überprüfen, bevor sie zur Verwendung gespeichert werden können.  Diese Elemente werden im JSON-Code für die Konvertierungszuordnung vom Typ „Gerät“ und „FHIR“ benötigt.

**Gerätezuordnung**

|Element|Erforderlich|
|:-------|:------|
|TypName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|True|
|Values[].ValueExpression|True|

> [!NOTE]
> Values[].ValueName und Values[].ValueExpression
>
> Diese Elemente sind nur erforderlich, wenn sich ein Werteintrag im Array befindet – es ist zulässig, dass keine Werte zugeordnet sind. Dies wird verwendet, wenn die gesendete Telemetrie ein Ereignis ist. Beispiel: Wenn ein tragbares IoMT-Gerät angelegt oder entfernt wird. Die Elemente haben keine Werte außer einem Namen, der von Azure IoT-Konnektor für FHIR verglichen und ausgegeben wird. Bei der FHIR-Konvertierung wird es von Azure IoT-Konnektor für FHIR einem codierbaren Konzept zugeordnet, das auf dem semantischen Typ basiert – es werden keine tatsächlichen Werte aufgefüllt.

**FHIR-Zuordnung**

|Element|Erforderlich|
|:------|:-------|
|TypName|True|

> [!NOTE]
> Dies ist das einzige erforderliche FHIR-Zuordnungselement, das zu diesem Zeitpunkt überprüft wurde.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Fehlermeldungen und Korrekturen bei Azure IoT-Konnektor für FHIR (Vorschauversion)

|`Message`|Angezeigt wird|Bedingung|Fix| 
|-------|---------|---------|---|
|Invalid mapping name, mapping name should be device or FHIR. (Ungültiger Zuordnungsname; Zuordnungsname muss „Gerät“ oder „FHIR“ sein).|API|Der angegebene Zuordnungstyp ist kein Gerät oder FHIR.|Verwenden Sie einen der beiden unterstützten Zuordnungstypen (z. B.: „Gerät“ oder „FHIR“).|
|Fehler bei der Überprüfung. Erforderliche Informationen fehlen oder sind ungültig.|API und Azure-Portal|Beim Versuch, eine Konvertierungszuordnung zu speichern, fehlen erforderliche Informationen oder Elemente.|Fügen Sie die fehlenden Informationen oder Elemente hinzu, und versuchen Sie erneut, die Konvertierungszuordnung zu speichern.|
|Regenerate key parameters not defined. (Erneutes Generieren von Schlüsselparametern wurde nicht definiert.)|API|Anforderung zum erneuten Generieren des Schlüssels.|Beziehen Sie die Parameter in die Anforderung zur erneuten Schlüsselgenerierung ein.|
|Reached the maximum number of IoT Connector instances that can be provisioned in this subscription. (Die maximale Anzahl von IoT-Konnektor-Instanzen, die in diesem Abonnement bereitgestellt werden können, wurde erreicht.)|API und Azure-Portal|Das Abonnementkontingent für Azure IoT-Konnektor für FHIR wurde erreicht (der Standardwert ist „2“ pro Abonnement).|Löschen Sie eine der vorhandenen Instanzen von Azure IoT-Konnektor für FHIR.  Verwenden Sie ein anderes Abonnement, bei dem das Abonnementkontingent noch nicht erreicht wurde.  Fordern Sie eine Erhöhung des Abonnementkontingents an.|
|Move resource is not supported for IoT Connector enabled Azure API for FHIR resource. (Ressourcenverschiebung wird bei IoT-Konnektor-fähiger Azure-API für FHIR-Ressource nicht unterstützt).|API und Azure-Portal|Es wurde versucht, einen Verschiebevorgang bei einer Azure-API für FHIR-Ressource durchzuführen, die mindestens eine Instanz von Azure IoT-Konnektor für FHIR enthält.|Löschen Sie die vorhandene(n) Instanz(en) von Azure IoT-Konnektor für FHIR, um den Verschiebevorgang ausführen zu können.|
|IoT Connector not provisioned. (IoT-Konnektor nicht bereitgestellt.)|API|Es wurde versucht, untergeordnete Dienste (Verbindungen und Zuordnungen) zu nutzen, doch das übergeordnete Element (Azure IoT-Konnektor für FHIR) war nicht bereitgestellt.|Stellen Sie einen Azure IoT-Konnektor für FHIR bereit.|
|Die Anforderung wird nicht unterstützt.|API|Eine bestimmte API-Anforderung wird nicht unterstützt.|Verwenden Sie die richtige API-Anforderung.|
|Konto nicht vorhanden.|API|Beim Versuch zum Hinzufügen eines Azure IoT-Konnektors für FHIR war die Azure-API für FHIR-Ressource nicht vorhanden.|Erstellen Sie die Azure-API für FHIR-Ressource, und wiederholen Sie dann den Vorgang.|
|Azure API for FHIR resource FHIR version is not supported for IoT Connector. (Die FHIR-Version der Azure-API für FHIR-Ressource wird bei IoT-Konnektor nicht unterstützt.)|API|Es wurde versucht, einen Azure IoT-Konnektor für FHIR mit einer inkompatiblen Version der Azure-API für FHIR-Ressource zu verwenden.|Erstellen Sie eine neue Azure-API für FHIR-Ressource (Version R4), oder verwenden Sie eine vorhandene Azure-API für FHIR-Ressource (Version R4).

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Warum werden meine Daten zu Azure IoT-Konnektor für FHIR (Vorschauversion) in der Azure-API für FHIR nicht angezeigt?

|Mögliche Probleme|Fehlerbehebungen|
|----------------|-----|
|Die Daten werden noch verarbeitet.|Daten werden an die Azure-API für FHIR in Batches (ca. alle 15 Minuten) ausgegeben.  Es ist möglich, dass die Daten noch verarbeitet werden und zusätzliche Zeit erforderlich ist, damit die Daten in der Azure-API für FHIR persistent gespeichert werden.|
|JSON-Datei für Konvertierungszuordnung des Typs „Gerät“ wurde nicht konfiguriert.|Konfigurieren und speichern Sie die entsprechende JSON-Datei für Konvertierungszuordnung des Typs „Gerät“.|
|JSON-Datei für Konvertierungszuordnung des Typs „FHIR“ wurde nicht konfiguriert.|Konfigurieren und speichern Sie die entsprechende JSON-Datei für Konvertierungszuordnung des Typs „FHIR“.|
|Die Gerätenachricht enthält keinen erwarteten Ausdruck, der in der Gerätezuordnung definiert wurde.|Überprüfen Sie, ob die in der Gerätezuordnung definierten „JsonPath“-Ausdrücke mit den in der Gerätenachricht definierten Token übereinstimmen.|
|In der Azure-API für FHIR wurde keine Patientenressource erstellt (Lösungstyp: Nur Suche)*.|Erstellen Sie in der Azure-API für FHIR eine gültige Geräteressource. Sorgen Sie dafür, dass die Geräteressource einen Bezeichner enthält, der mit der in der eingehenden Nachricht angegebenen Geräte-ID übereinstimmt.|
|In der Azure-API für FHIR wurde keine Patientenressource erstellt (Lösungstyp: Nur Suche)*.|Erstellen Sie in der Azure-API für FHIR eine gültige Patientenressource.|
|Der Verweis „Device.patient“ wurde nicht festgelegt, oder der Verweis ist ungültig (Lösungstyp: Nur Suche)*.|Stellen Sie sicher, dass die Geräteressource einen gültigen [Verweis](https://www.hl7.org/fhir/device-definitions.html#Device.patient) auf eine Patientenressource enthält.| 

*Referenz [Schnellstart: Bereitstellen von Azure IoT-Konnektor für FHIR (Vorschauversion) mithilfe des Azure-Portals](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview). Sie finden darin eine Funktionsbeschreibung der Lösungstypen zu Azure IoT-Konnektor (Beispiel: Suche oder Erstellen).

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>Verwenden von Metriken zum Beheben von Fehlern im Azure IoT-Konnektor für FHIR (Vorschauversion)

Der Azure IoT-Konnektor für FHIR generiert mehrere Metriken, um Erkenntnisse über den Datenflussprozess zu ermöglichen. Eine der unterstützten Metriken, *Fehler gesamt*  genannt, enthält die Anzahl aller Fehler, die in einer Instanz des Azure IoT-Konnektors für FHIR auftreten.

Jeder Fehler wird mit einer Reihe von zugeordneten Eigenschaften protokolliert. Jede Eigenschaft stellt einen anderen Aspekt des Fehlers dar, der Ihnen helfen kann, Probleme zu identifizieren und zu beheben. In diesem Abschnitt werden verschiedene Eigenschaften aufgelistet, die für jeden Fehler in der Metrik *Fehler gesamt* erfasst werden, und mögliche Werte für diese Eigenschaften.

> [!NOTE]
> Wie Sie zur Metrik *Fehler gesamt* für eine Instanz des Azure IoT-Konnektors für FHIR (Vorschau) navigieren können, wird auf der Seite [Anzeigen und Konfigurieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau)](iot-metrics-display.md) beschrieben.

Klicken Sie auf das Diagramm *Fehler gesamt* und dann auf die Schaltfläche *Filter hinzufügen*, um die Fehlermetrik mithilfe einer der unten erwähnten Eigenschaften gezielt auszuwerten.

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>Der vom Azure IoT-Konnektor für FHIR (Vorschau) durchgeführte Vorgang

Diese Eigenschaft stellt den Vorgang dar, der vom IoT-Konnektor ausgeführt wird, wenn der Fehler aufgetreten ist. Ein Vorgang stellt in der Regel die Datenflussphase während der Verarbeitung einer Gerätenachricht dar. Hier ist die Liste der möglichen Werte für diese Eigenschaft.

> [!NOTE]
> Weitere Informationen zu verschiedenen Phasen des Datenflusses im Azure IoT-Konnektor für FHIR (Vorschau) finden Sie [hier](iot-data-flow.md).

|Datenflussphase|BESCHREIBUNG|
|---------------|-----------|
|Setup|Der spezifische Vorgang für das Einrichten Ihrer IoT-Konnektor-Instanz|
|Normalisierung|Datenflussphase, in der die Gerätedaten normalisiert werden|
|Gruppierung|Datenflussphase, in der die normalisierten Gerätedaten gruppiert werden|
|FHIRConversion|Datenflussphase, in der die normalisierten und gruppierten Gerätedaten in eine FHIR-Ressource umgewandelt werden|
|Unbekannt|Der Vorgangstyp ist unbekannt, wenn ein Fehler aufgetreten ist.|

### <a name="the-severity-of-the-error"></a>Der Schweregrad des Fehlers

Diese Eigenschaft stellt den Schweregrad des aufgetretenen Fehlers dar. Hier ist die Liste der möglichen Werte für diese Eigenschaft.

|severity|BESCHREIBUNG|
|---------------|-----------|
|Warnung|Im Datenflussprozess ist ein geringfügiges Problem aufgetreten, aber die Verarbeitung der Gerätenachricht wird nicht beendet.|
|Fehler|Bei der Verarbeitung einer bestimmten Gerätenachricht ist ein Fehler aufgetreten, und andere Meldungen können weiterhin erwartungsgemäß ausgeführt werden.|
|Kritisch|Beim IoT-Konnektor liegen Probleme auf Systemebene vor, und es wird nicht erwartet, dass Nachrichten verarbeitet werden.|

### <a name="the-type-of-the-error"></a>Der Typ des Fehlers

Diese Eigenschaft gibt eine Kategorie für einen bestimmten Fehler an, die im Grunde eine logische Gruppierung für ähnliche Fehlertypen darstellt. Hier ist die Liste der möglichen Werte für diese Eigenschaft.

|Fehlertyp|BESCHREIBUNG|
|----------|-----------|
|DeviceTemplateError|Fehler im Zusammenhang mit Gerätezuordnungsvorlagen|
|DeviceMessageError|Fehler beim Verarbeiten einer bestimmten Gerätenachricht|
|FHIRTemplateError|Fehler im Zusammenhang mit FHIR-Zuordnungsvorlagen|
|FHIRConversionError|Fehler beim Transformieren einer Nachricht in eine FHIR-Ressource|
|FHIRResourceError|Fehler im Zusammenhang mit vorhandenen Ressourcen auf dem FHIR-Server, auf die vom IoT-Konnektor verwiesen wird|
|FHIRServerError|Fehler, die bei der Kommunikation mit dem FHIR-Server auftreten|
|GeneralError|Alle anderen Typen von Fehlern|

### <a name="the-name-of-the-error"></a>Der Name des Fehlers

Diese Eigenschaft stellt den Namen für einen bestimmten Fehler bereit. Hier ist die Liste aller Fehlernamen mit ihrer Beschreibung und den zugehörigen Fehlertypen, dem Schweregrad und den Datenflussphasen.

|Fehlerbezeichnung|BESCHREIBUNG|Fehlertyp(en)|Schweregrad des Fehlers|Datenflussphase(n)|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|Es trat ein Fehler auf, als auf dem FHIR-Server für entsprechende Bezeichner in der Gerätenachricht mehrere Patienten- oder Geräteressourcen gefunden wurden.|FHIRResourceError|Fehler|FHIRConversion|
|TemplateNotFoundException|Eine Geräte- oder FHIR-Zuordnungsvorlage ist nicht mit der Instanz des IoT-Konnektors konfiguriert.|DeviceTemplateError, FHIRTemplateError|Kritisch|Normalisierung, FHIRConversion|
|CorrelationIdNotDefinedException|Die Korrelations-ID ist nicht in der Gerätezuordnungsvorlage angegeben. CorrelationIdNotDefinedException ist ein bedingter Fehler, der nur auftreten würde, wenn die FHIR-Beobachtung Gerätemessungen mithilfe einer Korrelations-ID gruppieren muss, die aber nicht ordnungsgemäß konfiguriert ist.|DeviceMessageError|Fehler|Normalisierung|
|PatientDeviceMismatchException|Dieser Fehler tritt auf, wenn die Geräteressource auf dem FHIR-Server einen Verweis auf eine Patientenressource enthält, die nicht mit dem in der Nachricht vorhandenen Patientenbezeichner identisch ist.|FHIRResourceError|Fehler|FHIRConversionError|
|PatientNotFoundException|Die Geräte-FHIR-Ressource, die dem Gerätebezeichner in der Gerätenachricht zugeordnet ist, verweist auf keine Patienten-FHIR-Ressource. Beachten Sie, dass dieser Fehler nur auftritt, wenn die IoT-Konnektor-Instanz mit *Lookup*-Auflösungstyp konfiguriert ist.|FHIRConversionError|Fehler|FHIRConversion|
|DeviceNotFoundException|Auf dem FHIR-Server, der dem Gerätebezeichner in der Gerätenachricht zugeordnet ist, ist keine Geräteressource vorhanden.|DeviceMessageError|Fehler|Normalisierung|
|PatientIdentityNotDefinedException|Dieser Fehler tritt auf, wenn der Ausdruck zum Analysieren des Patientenbezeichners aus der Gerätenachricht nicht in der Gerätezuordnungsvorlage konfiguriert ist oder der Patientenbezeichner nicht in der Gerätenachricht vorhanden ist. Beachten Sie, dass dieser Fehler nur auftritt, wenn der Auflösungstyp des IoT-Konnektors auf *Erstellen* festgelegt ist.|DeviceTemplateError|Kritisch|Normalisierung|
|DeviceIdentityNotDefinedException|Dieser Fehler tritt auf, wenn der Ausdruck zum Analysieren des Gerätebezeichners aus der Gerätenachricht nicht in der Gerätezuordnungsvorlage konfiguriert ist oder der Gerätebezeichner nicht in der Gerätenachricht vorhanden ist.|DeviceTemplateError|Kritisch|Normalisierung|
|NotSupportedException|Fehler beim Empfang einer Gerätenachricht mit nicht unterstütztem Format|DeviceMessageError|Fehler|Normalisierung|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Erstellen von Kopien der JSON-Datei für Konvertierungszuordnung zu Azure IoT-Konnektor für FHIR (Vorschauversion)

Das Kopieren von Azure IoT-Konnektor für FHIR-Zuordnungsdateien kann zur Bearbeitung und Archivierung außerhalb der Azure-Portalwebsite nützlich sein.

Die Kopien der Zuordnungsdatei sollten beim Öffnen eines Supporttickets für den Azure Technical Support als Hilfe bei der Problembehandlung bereitgestellt werden.

> [!NOTE]
> JSON ist zurzeit das einzige unterstützte Format für Geräte- und FHIR-Zuordnungsdateien.

> [!TIP]
> Informieren Sie sich ausführlicher zum Thema [JSON-Datei für Geräte- und FHIR-Konvertierungszuordnung](./iot-mapping-templates.md) zu Azure IoT-Konnektor für FHIR.

1. Wählen Sie auf der unteren linken Seite des Ressourcendashboards für Azure-API für FHIR im Abschnitt **„Add-Ins“** die Option **„IoT-Konnektor (Vorschauversion)“** aus.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-Konnektor1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Wählen Sie den **„Konnektor“** aus, aus dem Sie die JSON-Datei für Konvertierungszuordnung kopieren werden.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-Konnektor2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Dieser Prozess kann auch zum Kopieren und Speichern der Inhalte der JSON-Datei für **„FHIR-Zuordnung konfigurieren“** verwendet werden.

3. Wählen Sie **„Gerätezuordnung konfigurieren“** aus.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-Konnektor3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Wählen Sie den Inhalt der JSON-Datei aus, und führen Sie einen Kopiervorgang aus (Beispiel: Drücken Sie STRG+C). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-Konnektor4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Führen Sie einen Einfügevorgang (Beispiel: STRG+C drücken) in eine neue Datei in einem Text-Editor (Beispiel: Visual Studio Code, Notepad) aus, und speichern Sie die Datei mit der Erweiterung „*.JSON“.

> [!TIP]
> Wenn Sie ein [Azure Technical Support](https://azure.microsoft.com/support/create-ticket/)-Ticket zu Azure IoT-Konnektor für FHIR öffnen, müssen Sie unbedingt Kopien Ihrer JSON-Datei für Konvertierungszuordnung als Hilfe bei der Problembehandlung einbeziehen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie häufig gestellte Fragen zu Azure IoT-Konnektor für FHIR.

>[!div class="nextstepaction"]
>[Azure IoT-Konnektor für FHIR – Häufig gestellte Fragen](fhir-faq.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet.