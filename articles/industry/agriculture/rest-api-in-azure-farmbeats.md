---
title: Azure FarmBeats-APIs
description: Lernen Sie die Azure FarmBeats-APIs kennen, die Unternehmen im Agrarsektor eine standardisierte RESTful-Schnittstelle mit JSON-basierten Antworten bieten.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674508"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats-APIs

In diesem Artikel werden die Azure FarmBeats-APIs beschrieben. Die Azure FarmBeats-APIs bieten Unternehmen im Agrarsektor eine standardisierte RESTful-Schnittstelle mit JSON-basierten Antworten, damit die Funktionen von Azure FarmBeats genutzt werden können. Beispiele hierfür sind:

- APIs zum Abrufen von Daten aus Sensoren, Kameras, Drohnen oder Satelliten zu Wettersituationen und Bodenbeschaffenheit.
- Normalisierung und Kontextualisierung von Daten von allen gängigen Datenanbietern.
- Schemabasierte Zugriffs- und Abfragefunktionen für alle erfassten Daten.
- Automatische Generierung von Metadaten, die auf ackerbauspezifischen Merkmalen basieren und abgefragt werden können.
- Automatisch generierte Zeitreihenaggregate für eine schnelle Modellerstellung.
- Integrierte Azure Data Factory-Engine zum einfachen Erstellen von benutzerdefinierten Pipelines für die Datenverarbeitung.

## <a name="application-development"></a>Anwendungsentwicklung

Die FarmBeats-APIs enthalten eine technische Swagger-Dokumentation. Informationen zu allen APIs und den entsprechenden Anforderungen oder Antworten finden Sie unter [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

In der folgenden Tabelle sind alle Objekte und Ressourcen des FarmBeats-Datenhubs zusammengefasst:

| Objekte und Ressourcen | BESCHREIBUNG
--- | ---|
Bauernhof | „Bauernhof“ entspricht einem physischen Ort, der innerhalb des FarmBeats-Systems von Interesse ist. Jeder landwirtschaftliche Betrieb besitzt einen Namen und eine eindeutige zugehörige ID. |
Sicherungsmedium  | „Gerät“ (Device) entspricht einem physischen Gerät, das in dem landwirtschaftlichen Betrieb vorhanden ist. Jedes Gerät verfügt über eine eindeutige Geräte-ID. Ein Gerät wird für einen landwirtschaftlichen Betrieb in der Regel mit einer entsprechenden ID bereitgestellt.
DeviceModel  | „DeviceModel“ entspricht den Metadaten des Geräts, beispielsweise dem Hersteller und dem Gerätetyp, wobei es sich um ein Gateway oder einen Knoten handeln kann.
Sensor  | „Sensor“ entspricht einem physischen Sensor, der Werte aufzeichnet. Ein Sensor ist in der Regel mit einem Gerät mit einer Geräte-ID verbunden.
SensorModel  | „SensorModel“ entspricht den Metadaten des Sensors. Hierzu zählen beispielsweise der Hersteller, der Sensortyp – analog oder digital – und die Sensormessung, z. B. Umgebungstemperatur und Luftdruck.
Telemetrie  | Telemetrie ermöglicht es, Telemetrienachrichten für einen bestimmten Sensor und Zeitbereich zu lesen.
Auftrag  | „Auftrag“ (Job) entspricht einem beliebigen Workflow mit Aktivitäten, die im FarmBeats-System ausgeführt werden, um eine gewünschte Ausgabe zu erhalten. Jedem Auftrag ist eine Auftrags-ID und ein Auftragstyp zugeordnet.
JobType  | „JobType“ entspricht den verschiedenen Auftragstypen, die vom System unterstützt werden. Hierin sind vom System definierte und benutzerdefinierte Auftragstypen enthalten.
ExtendedType  | „ExtendedType“ entspricht der Liste mit system- und benutzerdefinierten Typen im System. Mit „ExtendedType“ können im FarmBeats-System ein neuer Sensor, eine Szene oder ein Szenendateityp eingerichtet werden.
Partner  | „Partner“ entspricht dem Partner für die Integration von Sensoren und Bildern in FarmBeats.
Szene  | „Szene“ (Scene) entspricht einer beliebigen generierten Ausgabe im Kontext eines landwirtschaftlichen Betriebs. Jeder Szene ist eine Szenen-ID, eine Szenenquelle, ein Szenentyp und eine entsprechende ID des landwirtschaftlichen Betriebs zugeordnet. Jeder Szenen-ID können mehrere Szenendateien zugeordnet sein.
SceneFile |„SceneFile“ entspricht allen Dateien, die für eine einzelne Szene generiert werden. Einer einzelnen Szenen-ID können mehrere SceneFile-IDs zugeordnet sein.
Regel  |„Regel“ entspricht einer Bedingung für Daten in Bezug auf den Bauernhof, die zum Auslösen einer Warnung verwendet wird. Jede Regel befindet sich im Kontext der Daten eines landwirtschaftlichen Betriebs.
Warnung  | „Warnung“ entspricht einer Benachrichtigung, die generiert wird, wenn eine Regelbedingung erfüllt ist. Jede Warnung befindet sich im Kontext einer Regel.
RoleDefinition  | „RoleDefinition“ definiert zulässige und nicht zulässige Aktionen für eine Rolle.
RoleAssignment  |„RoleAssignment“ entspricht der Zuweisung einer Rolle zu einem Benutzer oder Dienstprinzipal.

### <a name="data-format"></a>Datenformat

JSON ist ein gängiges sprachunabhängiges Datenformat, bei dem eine einfache Textdarstellung beliebiger Datenstrukturen bereitgestellt wird. Weitere Informationen finden Sie auf der [JSON-Website](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

HTTP-Anforderungen an die REST-API werden durch Azure Active Directory (Azure AD) geschützt.
Um eine authentifizierte Anforderung an die REST-APIs zu senden, erfordert der Clientcode eine Authentifizierung mit gültigen Anmeldeinformationen, bevor Sie die API aufrufen können. Die Authentifizierung wird von Azure AD für die verschiedenen Akteure koordiniert. Für Ihren Client wird als Nachweis der Authentifizierung ein Zugriffstoken bereitgestellt. Das Token wird dann im HTTP-Autorisierungsheader der REST-API-Anforderungen gesendet. Weitere Informationen zur Azure AD-Authentifizierung finden Sie unter [Azure Active Directory](https://portal.azure.com) für Entwickler.

Das Zugriffstoken muss in nachfolgenden API-Anforderungen wie folgt im Headerabschnitt gesendet werden:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP-Anforderungsheader

Hier sind die gängigsten Anforderungsheader aufgeführt, die Sie bei einem API-Aufruf des Azure FarmBeats-Datenhubs angeben müssen.


**Kopfzeile** | **Beschreibung und Beispiel**
--- | ---
Content-Type  | Das Anforderungsformat (Content-Type: application/<format>). Für Azure FarmBeats-Datenhub-APIs wird das JSON-Format verwendet. Content-Type: application/json
Authorization  | Gibt das Zugriffstoken an, das zum Ausführen eines API-Aufrufs erforderlich ist. Autorisierung: Bearer <Access-Token>
Akzeptieren | Das Antwortformat. Für Azure FarmBeats-Datenhub-APIs wird das JSON-Format verwendet. Accept: application/json

### <a name="api-requests"></a>API-Anforderungen

Zum Senden einer REST-API-Anforderung kombinieren Sie die HTTP-Methode (GET, POST, PUT oder DELETE), die URL zum API-Dienst und den Ressourcen-URI (zum Abfragen, Übermitteln von Daten, Aktualisieren oder Löschen) und fügen anschließend mindestens einen HTTP-Anforderungsheader hinzu.

Die URL zum API-Dienst ist Ihre Datenhub-URL, z. B. „https://\<yourdatahub-website-name>.azurewebsites.net.

Optional können Sie Abfrageparameter in GET-Aufrufe einfügen, um Daten in den Antworten zu filtern, zu sortieren und deren Größe zu beschränken.

Die folgende Beispielanforderung wird zum Abrufen der Liste mit den Geräten verwendet:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Für die meisten GET-, POST- und PUT-Aufrufe ist ein JSON-Anforderungstext erforderlich.

Mit der folgenden Beispielanforderung wird ein Gerät erstellt. Diese Anforderung enthält eine JSON-Eingabe mit dem Anforderungstext.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Abfrageparameter

Bei REST-GET-Aufrufen können Sie die Daten in einer API-Antwort filtern, sortieren und ihre Größe beschränken, indem Sie mindestens einen Abfrageparameter in den Anforderungs-URI einfügen. Informationen zu den Abfrageparametern finden Sie in der Dokumentation zur API und unter den einzelnen GET-Aufrufen.
Beim Abfragen der Geräteliste (GET-Aufruf für „/Device“) können die folgenden Abfrageparameter angegeben werden:

![Liste mit Geräten](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Fehlerbehandlung

Azure FarmBeats-Datenhub-APIs geben die HTTP-Standardfehler zurück. Hier finden Sie die häufigsten Fehlercodes:

 |Fehlercode             | BESCHREIBUNG |
 |---                    | --- |
 |200                    | Erfolg |
 |201                    | Erfolgreich erstellt |
 |400                    | Ungültige Anforderung; Die Anforderung enthält einen Fehler. |
 |401                    | Nicht autorisiert. Der Aufrufer der API ist nicht für den Zugriff auf die Ressource autorisiert. |
 |404                    | Ressource nicht gefunden. |
 |5XX                    | Interner Serverfehler. Die mit „5XX“ beginnenden Fehlercodes weisen auf einen Fehler auf dem Server hin. Weitere Informationen finden Sie im folgenden Abschnitt und in den Serverprotokollen. |


Zusätzlich zu den HTTP-Standardfehlern geben Azure FarmBeats-Datenhub-APIs auch interne Fehler im folgenden Format zurück:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

In diesem Beispiel wurde bei der Erstellung eines landwirtschaftlichen Betriebs das obligatorische Feld „Name“ in der Eingabenutzlast nicht angegeben. Daraus resultiert die folgende Fehlermeldung:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Hinzufügen von Benutzern oder App-Registrierungen zu Azure Active Directory

Auf Azure FarmBeats-APIs kann von einem Benutzer oder einer App-Registrierung in Azure Active Directory zugegriffen werden. Führen Sie die folgenden Schritte aus, um in Azure Active Directory eine App-Registrierung zu erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) nacheinander die Optionen **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus. Alternativ dazu können Sie auch ein vorhandenes Konto verwenden.
2. Führen Sie für ein neues Konto die folgenden Schritte aus:

    - Geben Sie einen Namen ein.
    - Wählen Sie **Nur Konten in diesem Organisationsverzeichnis (einzelner Mandant)** aus.
    - Übernehmen Sie in den restlichen Feldern die Standardwerte.
    - Wählen Sie **Registrieren** .

3. Gehen Sie im Bereich **Übersicht** für neue und vorhandene App-Registrierungen wie folgt vor:

    - Erfassen Sie die **Client-ID** und die **Mandanten-ID** .
    - Wechseln Sie zu **Zertifikate und Geheimnisse** , um einen neuen geheimen Clientschlüssel zu generieren, und erfassen Sie den **geheimen Clientschlüssel** .
    - Wechseln Sie zurück zur **Übersicht** , und wählen Sie den Link neben **Anwendung im lokalen Verzeichnis verwalten** aus.
    - Wechseln Sie zu **Eigenschaften** , um die **Objekt-ID** zu erfassen.

4. Wechseln Sie zum Datahub Swagger (https://<yourdatahub>.azurewebsites.net/swagger/index.html), und gehen Sie wie folgt vor:
    - Navigieren Sie zur **RoleAssignment-API** .
    - Führen Sie einen POST-Vorgang durch, um ein **RoleAssignment** -Objekt für die soeben erstellte **Objekt-ID** zu erstellen.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Weitere Informationen zum Hinzufügen von Benutzern und zur Active Directory-Registrierung finden Sie unter [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

Nachdem Sie die oben beschriebenen Schritte ausgeführt haben, kann Ihre App-Registrierung (der Client) die Azure FarmBeats-APIs mit einem Zugriffstoken per Bearerauthentifizierung aufrufen.

Senden Sie das Zugriffstoken in nachfolgenden API-Anforderungen im Headerabschnitt:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```