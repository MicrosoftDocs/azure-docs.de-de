---
title: Arbeiten mit den APIs von Defender für IoT
description: Verwenden Sie eine externe REST-API, um auf die von Sensoren und Verwaltungskonsolen ermittelten Daten zuzugreifen und Aktionen mit diesen Daten auszuführen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: ae7965dd319f2ff885f4329262ae4772452afd62
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523242"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Sensor- und Verwaltungskonsolen-APIs für Defender für IoT

Verwenden Sie eine externe REST-API, um auf die von Sensoren und Verwaltungskonsolen ermittelten Daten zuzugreifen und Aktionen mit diesen Daten auszuführen.

Die Verbindungen sind durch SSL gesichert.

## <a name="getting-started"></a>Erste Schritte

Im Allgemeinen müssen Sie ein Zugriffstoken generieren, wenn Sie eine externe API auf dem Sensor oder in der lokalen Verwaltungskonsole von Azure Defender für IoT verwenden. Für Authentifizierungs-APIs, die Sie auf dem Sensor und in der lokalen Verwaltungskonsole verwenden, sind keine Token erforderlich.

So generieren Sie ein Token

1. Wählen Sie im Fenster **Systemeinstellungen** die Option **Zugriffstoken** aus.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Screenshot des Fensters „Systemeinstellungen“ mit hervorgehobener Schaltfläche „Zugriffstoken“":::

2. Wählen Sie **Neues Token generieren** aus.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Auswählen der Schaltfläche zum Generieren eines neuen Tokens":::

3. Beschreiben Sie den Zweck des neuen Tokens, und wählen Sie **Weiter** aus.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Generieren eines neuen Tokens und Eingeben des Namens der zugehörigen Integration":::

4. Das Zugriffstoken wird angezeigt. Kopieren Sie das Token, da es nicht noch einmal angezeigt wird.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Kopieren des Zugriffstokens für die Integration":::

5. Wählen Sie **Fertig stellen** aus. Die von Ihnen erstellten Token werden im Dialogfeld **Zugriffstoken** angezeigt.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Screenshot des Dialogfelds „Zugriffstoken“ mit ausgefüllten Token":::

   **Verwendet** gibt den letzten Zeitpunkt an, an dem ein externer Aufruf mit diesem Token empfangen wurde.

   Wenn im Feld **Verwendet** für dieses Token der Wert **N/V** angezeigt wird, funktioniert die Verbindung zwischen dem Sensor und dem verbundenen Server nicht.

6. Fügen Sie Ihrer Anforderung einen HTTP-Header mit dem Titel **Autorisierung** hinzu, und legen Sie den Wert auf das von Ihnen generierte Token fest.

## <a name="sensor-api-specifications"></a>Spezifikationen der Sensor-APIs

In diesem Abschnitt werden die folgenden Sensor-APIs beschrieben:

- [Abrufen von Geräteinformationen: /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Abrufen von Geräteverbindungsinformationen: /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Abrufen von Informationen zu CVEs: /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Abrufen von Informationen zu Warnungen: /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Abrufen von Zeitachsenereignissen: /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Abrufen von Informationen zu Sicherheitsrisiken: /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Abrufen von Sicherheitsrisiken: /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Abrufen von betrieblichen Sicherheitsrisiken: /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Überprüfen von Benutzeranmeldeinformationen: /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Ändern des Kennworts: /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Aktualisieren von Benutzerkennwörtern durch den Systemadministrator: /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Abrufen von Geräteinformationen: /api/v1/devices

Mit dieser API können Sie eine Liste aller Geräte anfordern, die von einem Defender für IoT-Sensor erkannt wurden.

#### <a name="method"></a>Methode

**GET**

Fordert eine Liste aller Geräte an, die vom Defender für IoT-Sensor erkannt wurden.

#### <a name="query-parameters"></a>Abfrageparameter

- **authorized**: Filtert nur autorisierte und nicht autorisierte Geräte.

  **Beispiele**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Geräte darstellen.

#### <a name="device-fields"></a>Gerätefelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **id** | Numeric | Nein | - |
| **ipAddresses** | JSON-Array | Ja | IP-Adressen (können bei Internetadressen oder einem Gerät mit dualen Netzwerkkarten mehrere Adressen sein) |
| **name** | String | Nein | - |
| **type** | String | Nein | Unknown, Engineering Station, PLC, HMI, Historian, Domain Controller, DB Server, Wireless Access Point, Router, Switch, Server, Workstation, IP Camera, Printer, Firewall, Terminal station, VPN Gateway, Internet oder Multicast und Broadcast |
| **macAddresses** | JSON-Array | Ja | MAC-Adressen (können bei einem Gerät mit dualen Netzwerkkarten mehrere Adressen sein) |
| **operatingSystem** | String | Ja | - |
| **engineeringStation** | Boolesch | Nein | „true“ oder „false“ |
| **scanner** | Boolesch | Nein | „true“ oder „false“ |
| **authorized** | Boolesch | Nein | „true“ oder „false“ |
| **vendor** | String | Ja | - |
| **protocols** | JSON-Array | Ja | Protokollobjekt |
| **firmware** | JSON-Array | Ja | Firmwareobjekt |

#### <a name="protocol-fields"></a>Protokollfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **Name** | String | Nein |  |
| **Adresses** | JSON-Array | Ja | Master oder numerische Werte |

#### <a name="firmware-fields"></a>Firmwarefelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **serial** | String | Nein | N/V oder der tatsächliche Wert |
| **model** | String | Nein | N/V oder der tatsächliche Wert |
| **firmwareVersion** | Double | Nein | N/V oder der tatsächliche Wert |
| **additionalData** | String | Nein | N/V oder der tatsächliche Wert |
| **moduleAddress** | String | Nein | N/V oder der tatsächliche Wert |
| **rack** | String | Nein | N/V oder der tatsächliche Wert |
| **slot** | String | Nein | N/V oder der tatsächliche Wert |
| **address** | String | Nein | N/V oder der tatsächliche Wert |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:<span>//127<span>.0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Abrufen von Geräteverbindungsinformationen: /api/v1/devices/connections

Verwenden Sie diese API, um eine Liste aller Verbindungen pro Gerät anzufordern.

#### <a name="method"></a>Methode

**GET**

#### <a name="query-parameters"></a>Abfrageparameter

Wenn Sie keine Abfrageparameter festlegen, werden alle Geräteverbindungen zurückgegeben.

**Beispiel**:

`/api/v1/devices/connections`

- **deviceId**: Zum Filtern nach einer bestimmten Geräte-ID, um die zugehörigen Verbindungen anzuzeigen.

  **Beispiel**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: Der Zeitrahmen in Minuten (von jetzt an rückwärts), in dem die Verbindungen aktiv waren.

  **Beispiel**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: Filtert nur Verbindungen, die vor einem bestimmten Zeitpunkt erkannt wurden (in Millisekunden, UTC).

  **Beispiel**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: Filtert nur Verbindungen, die nach einem bestimmten Zeitpunkt erkannt wurden (in Millisekunden, UTC).

  **Beispiel**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Geräteverbindungen darstellen.

#### <a name="fields"></a>Felder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **firstDeviceId** | Numeric | Nein | - |
| **secondDeviceId** | Numeric | Nein | - |
| **lastSeen** | Numeric | Nein | Epoche (UTC) |
| **discovered** | Numeric | Nein | Epoche (UTC) |
| **ports** | Zahlenarray | Nein | - |
| **protocols** | JSON-Array | Nein | Protokollfeld |

#### <a name="protocol-field"></a>Protokollfeld

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **name** | String | Nein | - |
| **commands** | Zeichenfolgenarray | Nein | - |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl-Befehl

> [!div class="mx-tdBreakAll"]
> | type | APIs | Beispiel |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/devices/connections | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" 'https://<IP-ADRESSE>/api/v1/devices/<deviceId>/connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Abrufen von Informationen zu CVEs: /api/v1/devices/cves

Mit dieser API können Sie eine Liste aller bekannten CVEs anfordern, die auf Geräten im Netzwerk erkannt werden.

#### <a name="method"></a>Methode

**GET**

#### <a name="query-parameters"></a>Abfrageparameter

Standardmäßig stellt diese API eine Liste mit allen IP-Adressen von Geräten mit CVEs und für jede IP-Adresse bis zu 100 CVEs mit den höchsten Bewertungskennzahlen bereit.

**Beispiel**:

`/api/v1/devices/cves`

- **deviceId**: Zum Filtern nach einer bestimmten Geräte-IP-Adresse, um bis zu 100 CVEs mit den höchsten Bewertungskennzahlen auf dem jeweiligen Gerät zu ermitteln.

  **Beispiel**:

  `/api/v1/devices/<ipAddress>/cves`

- **top**: Gibt an, wie viele CVEs mit den höchsten Bewertungskennzahlen für jede Geräte-IP-Adresse abgerufen werden.

  **Beispiel**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, welche die unter den IP-Adressen identifizierten CVEs darstellen.

#### <a name="fields"></a>Felder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **cveId** | String | Nein | - |
| **ipAddress** | String | Nein | IP-Adresse |
| **score** | String | Nein | 0,0-10,0 |
| **attackVector** | String | Nein | Network, Adjacent Network, Local oder Physical (Netzwerk, angrenzendes Netzwerk, lokal oder physisch) |
| **description** | String | Nein | - |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/devices/cves | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/devices/<deviceIpAddress>/cves?top= | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Abrufen von Informationen zu Warnungen: /api/v1/alerts

Mit dieser API können Sie eine Liste aller Warnungen anfordern, die vom Defender für IoT-Sensor erkannt wurden.

#### <a name="method"></a>Methode

**GET**

#### <a name="query-parameters"></a>Abfrageparameter

- **state**: Filtert nur nach bearbeiteten und unbearbeiteten Warnungen.

  **Beispiel**:

  `/api/v1/alerts?state=handled`

- **fromTime**: Zum Filtern von Warnungen, die zu einem bestimmten Zeitpunkt erstellt wurden (in Millisekunden, UTC).

  **Beispiel**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: Zum Filtern von Warnungen, die vor einem bestimmten Zeitpunkt erstellt wurden (in Millisekunden, UTC).

  **Beispiel**:

  `/api/v1/alerts?toTime=<epoch>`

- **type**: Zum Filtern von Warnungen nach einem bestimmten Typ. Vorhandene zum Filtern verfügbare Typen: unerwartete neue Geräte, Verbindungstrennungen.

  **Beispiel**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Warnungen darstellen.

#### <a name="alert-fields"></a>Warnungsfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **ID** | Numeric | Nein | - |
| **time** | Numeric | Nein | Epoche (UTC) |
| **title** | String | Nein | - |
| **Nachricht** | String | Nein | - |
| **severity** | String | Nein | Warning, Minor, Major oder Critical (Warnung, gering, wichtig oder kritisch) |
| **engine** | String | Nein | Protocol Violation, Policy Violation, Malware, Anomaly oder Operational (Protokollverletzung, Richtlinienverletzung, Malware, Anomalie oder Betrieb) |
| **sourceDevice** | Numeric | Ja | Geräte-ID |
| **destinationDevice** | Numeric | Ja | Geräte-ID |
| **additionalInformation** | Zusätzliches Informationsobjekt | Ja | - |

#### <a name="additional-information-fields"></a>Zusätzliche Informationsfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **description** | String | Nein | - |
| **information** | JSON-Array | Nein | String |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl-Befehl

> [!div class="mx-tdBreakAll"]
> | type | APIs | Beispiel |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" 'https://<IP-ADRESSE>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>Abrufen von Zeitachsenereignissen: /api/v1/events

Mit dieser API können Sie eine Liste der Ereignisse anfordern, die an die Ereigniszeitachse gemeldet wurden.

#### <a name="method"></a>Methode

**GET**

#### <a name="query-parameters"></a>Abfrageparameter

- **minutesTimeFrame**: Der Zeitrahmen in Minuten (von jetzt an rückwärts), in dem die Ereignisse gemeldet wurden.

  **Beispiel**:

  `/api/v1/events?minutesTimeFrame=20`

- **type**: Zum Filtern der Ereignisliste nach einem bestimmten Typ.

  **Beispiele**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Warnungen darstellen.

#### <a name="event-fields"></a>Ereignisfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|--|
| **timestamp** | Numeric | Nein | Epoche (UTC) |
| **title** | String | Nein | - |
| **severity** | String | Nein | INFO, NOTICE oder ALERT (Information, Hinweis oder Warnung) |
| **owner** | String | Ja | Wenn das Ereignis manuell erstellt wurde, enthält dieses Feld den Namen des Benutzers, der das Ereignis erstellt hat |
| **content** | String | Nein | - |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" 'https://<IP-ADRESSE>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Abrufen von Informationen zu Sicherheitsrisiken: /api/v1/reports/vulnerabilities/devices

Mit dieser API können Sie die Ergebnisse der Sicherheitsrisikobewertung für jedes Gerät anfordern.

#### <a name="method"></a>Methode

**GET**

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, welche die Geräte darstellen, auf die zugegriffen wurde.

Das Geräteobjekt hat folgenden Inhalt:

- Allgemeine Daten

- Bewertungskennzahl

- Sicherheitsrisiken

#### <a name="device-fields"></a>Gerätefelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **name** | String | Nein | - |
| **ipAddresses** | JSON-Array | Nein | - |
| **securityScore** | Numeric | Nein | - |
| **vendor** | String | Ja |  |
| **firmwareVersion** | String | Ja | - |
| **model** | String | Ja | - |
| **isWirelessAccessPoint** | Boolesch | Nein | „true“ oder „false“ |
| **operatingSystem** | Betriebssystemobjekt | Ja | - |
| **vulnerabilities** | Sicherheitsrisikoobjekt | Ja | - |

#### <a name="operating-system-fields"></a>Betriebssystemfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **Name** | String | Ja | - |
| **Type** | String | Ja | - |
| **Version** | String | Ja | - |
| **latestVersion** | String | Ja | - |

#### <a name="vulnerabilities-fields"></a>Sicherheitsrisikofelder
 
| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **antiViruses** | JSON-Array | Ja | Namen der Virenschutzprogramme |
| **plainTextPasswords** | JSON-Array | Ja | Kennwortobjekte |
| **remoteAccess** | JSON-Array | Ja | Remotezugriffobjekte |
| **isBackupServer** | Boolesch | Nein | „true“ oder „false“ |
| **openedPorts** | JSON-Array | Ja | „Geöffneter Port“-Objekte |
| **isEngineeringStation** | Boolesch | Nein | „true“ oder „false“ |
| **isKnownScanner** | Boolesch | Nein | „true“ oder „false“ |
| **cves** | JSON-Array | Ja | CVE-Objekte |
| **isUnauthorized** | Boolesch | Nein | „true“ oder „false“ |
| **malwareIndicationsDetected** | Boolesch | Nein | „true“ oder „false“ |
| **weakAuthentication** | JSON-Array | Ja | Erkannte Anwendungen, die eine schwache Authentifizierung verwenden |

#### <a name="password-fields"></a>Kennwortfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **password** | String | Nein | - |
| **protocol** | String | Nein | - |
| **strength** | String | Nein | Very weak, Weak, Medium oder Strong (sehr schwach, schwach, mittel, sicher) |

#### <a name="remote-access-fields"></a>Felder für den Remotezugriff

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **port** | Numeric | Nein | - |
| **transport** | String | Nein | TCP oder UDP |
| **client** | String | Nein | IP-Adresse |
| **clientSoftware** | String | Nein | SSH, VNC, Remotedesktop oder Teamviewer |

#### <a name="open-port-fields"></a>„Geöffneter Port“-Felder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **port** | Numeric | Nein | - |
| **transport** | String | Nein | TCP oder UDP |
| **protocol** | String | Ja | - |
| **isConflictingWithFirewall** | Boolesch | Nein | „true“ oder „false“ |

#### <a name="cve-fields"></a>CVE-Felder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **ID** | String | Nein | - |
| **score** | Numeric | Nein | Double |
| **description** | String | Nein | - |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/reports/vulnerabilities/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Abrufen von Sicherheitsrisiken: /api/v1/reports/vulnerabilities/security

Mit dieser API können Sie die Ergebnisse einer allgemeinen Sicherheitsrisikobewertung anfordern. Mit dieser Bewertung werden Erkenntnisse zur Sicherheitsstufe Ihres Systems bereitgestellt.

Diese Bewertung basiert auf allgemeinen Netzwerk- und Systeminformationen und nicht auf der Auswertung eines bestimmten Geräts.

#### <a name="method"></a>Methode

**GET**

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

JSON-Objekt, das bewertete Ergebnisse darstellt. Jeder Schlüssel kann Nullwerte zulassen. Andernfalls enthält er ein JSON-Objekt mit Schlüsseln, die keine Nullwerte zulassen.

### <a name="result-fields"></a>Ergebnisfelder

**Schlüssel**

**unauthorizedDevices**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **address** | String | IP-Adresse |
| **name** | String | - |
| **firstDetectionTime** | Numeric | Epoche (UTC) |
| lastSeen | Numeric | Epoche (UTC) |

**illegalTrafficByFirewallRules**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **server** | String | IP-Adresse |
| **client** | String | IP-Adresse |
| **port** | Numeric | - |
| **transport** | String | TCP, UDP oder ICMP |

**weakFirewallRules**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **sources** | JSON-Array der Quellen. Jede Quelle kann in einem von vier Formaten vorliegen: | „Any“ (Beliebig), „IP-Adresse (Host)“, „Von IP-Adresse bis IP-Adresse (BEREICH)“, „IP-Adresse, Subnetzmaske (NETZWERK)“ |
| **destinations** | JSON-Array der Ziele. Jedes Ziel kann in einem von vier Formaten vorliegen: | „Any“ (Beliebig), „IP-Adresse (Host)“, „Von IP-Adresse bis IP-Adresse (BEREICH)“, „IP-Adresse, Subnetzmaske (NETZWERK)“ |
| **ports** | JSON-Array der Ports in einem von drei Formaten: | „Any“ (Beliebig), „Port (Protokoll, falls erkannt)“, „Von Port bis Port (Protokoll, falls erkannt)“ |

**accessPoints**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **macAddress** | String | MAC-Adresse |
| **vendor** | String | Herstellername |
| **ipAddress** | String | IP-Adresse oder N/V |
| **name** | String | Gerätename oder N/V |
| **wireless** | String | No, Suspected oder Yes (Nein, Vermutet oder Ja) |

**connectionsBetweenSubnets**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **server** | String | IP-Adresse |
| **client** | String | IP-Adresse |

**industrialMalwareIndicators**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeric | Epoche (UTC) |
| **alertMessage** | String | - |
| **description** | String | - |
| **devices** | JSON-Array | Gerätenamen | 

**internetConnections**

| Feldname | Typ | Liste der Werte |
| ---------- | ---- | -------------- |
| **internalAddress** | String | IP-Adresse |
| **authorized** | Boolean | Ja oder Nein | 
| **externalAddresses** | JSON-Array | IP-Adresse |

#### <a name="response-example"></a>Antwortbeispiel

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Abrufen von betrieblichen Sicherheitsrisiken: /api/v1/reports/vulnerabilities/operational

Mit dieser API können Sie die Ergebnisse einer allgemeinen Sicherheitsrisikobewertung anfordern. Mit dieser Bewertung werden Erkenntnisse zum Betriebsstatus Ihres Netzwerks bereitgestellt. Diese Bewertung basiert auf allgemeinen Netzwerk- und Systeminformationen und nicht auf der Auswertung eines bestimmten Geräts.

#### <a name="method"></a>Methode

**GET**

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

JSON-Objekt, das bewertete Ergebnisse darstellt. Jeder Schlüssel enthält ein JSON-Array der Ergebnisse.

#### <a name="result-fields"></a>Ergebnisfelder

**Keys**

**backupServer**

| Feldname | Typ | Liste der Werte |
|--|--|--|
| **Quelle** | String | IP-Adresse |
| **destination** | String | IP-Adresse |
| **port** | Numeric | - |
| **transport** | String | TCP oder UDP |
| **backupMaximalInterval** | String | - |
| **lastSeenBackup** | Numeric | Epoche (UTC) |

**ipNetworks**

| Feldname | Typ | Liste der Werte |
|--|--|--|
| **addresses** | Numeric | - |
| **network** | String | IP-Adresse |
| **mask** | String | Subnetzmaske |

**protocolProblems**

| Feldname | Typ | Liste der Werte |
|--|--|--|
| **protocol** | String | - |
| **addresses** | JSON-Array | IP-Adressen |
| **alert** | String | - |
| **reportTime** | Numeric | Epoche (UTC) |

**protocolDataVolumes**

| Feldname | Typ | Liste der Werte |
|--|--|--|
| Protokoll | String | - |
| Volume | String | „Volumenummer, MB“ |

**disconnections**

| Feldname | Typ | Liste der Werte |
|--|--|--|
| **assetAddress** | String | IP-Adresse |
| **assetName** | String | - |
| **lastDetectionTime** | Numeric | Epoche (UTC) |
| **backToNormalTime** | Numeric | Epoche (UTC) |     

#### <a name="response-example"></a>Antwortbeispiel

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Überprüfen von Benutzeranmeldeinformationen: /api/external/authentication/validation

Mit dieser API können Sie einen Namen und das Kennwort eines Benutzers von Defender für IoT überprüfen. Alle Defender für IoT-Benutzerrollen können mit der API arbeiten.

Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="query-parameters"></a>Abfrageparameter

| **Name** | **Typ** | **Nullwerte zulässig** |
|--|--|--|
| **username** | String | Nein |
| **password** | String | Nein |

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Meldungszeichenfolge mit Details zum Vorgangsstatus:

- **Success – Meldung**: Authentifizierung erfolgreich

- **Failure – Fehler**: Fehler beim Überprüfen der Anmeldeinformationen

#### <a name="response-example"></a>Antwortbeispiel

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/api/external/authentication/validation | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>Ändern des Kennworts: /external/authentication/set_password

Mit dieser API können Sie Benutzern ermöglichen, ihre eigenen Kennwörter zu ändern. Alle Defender für IoT-Benutzerrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Meldungszeichenfolge mit Details zum Vorgangsstatus:

- **Success – Meldung**: Das Kennwort wurde ersetzt

- **Failure – Fehler**: Fehler bei der Benutzerauthentifizierung

- **Failure – Fehler**: Das Kennwort entspricht nicht der Sicherheitsrichtlinie

#### <a name="response-example"></a>Antwortbeispiel

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Gerätefelder

| **Name** | **Typ** | **Nullwerte zulässig** |
|--|--|--|
| **username** | String | Nein |
| **password** | String | Nein |
| **new_password** | String | Nein |

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| POST | curl -k -d '{"username": "<BENUTZERNAME>","password": "<AKTUELLES_KENNWORT>","new_password": "<NEUES_KENNWORT>"}' -H 'Content-Type: application/json'  https://<IP-ADRESSE>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualisieren von Benutzerkennwörtern durch den Systemadministrator: /external/authentication/set_password_by_admin

Mit dieser API können Sie Systemadministratoren ermöglichen, die Kennwörter für bestimmte Benutzer zu ändern. Defender für IoT-Administratorrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Meldungszeichenfolge mit Details zum Vorgangsstatus:

- **Success – Meldung**: Das Kennwort wurde ersetzt

- **Failure – Fehler**: Fehler bei der Benutzerauthentifizierung

- **Failure – Fehler**: Benutzer ist nicht vorhanden

- **Failure – Fehler**: Das Kennwort entspricht nicht der Sicherheitsrichtlinie

- **Failure – Fehler**: Der Benutzer hat keine Berechtigung zum Ändern des Kennworts

#### <a name="response-example"></a>Antwortbeispiel

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Gerätefelder

| **Name** | **Typ** | **Nullwerte zulässig** |
|--|--|--|
| **admin_username** | String | Nein |
| **admin_password** | String | Nein |
| **username** | String | Nein |
| **new_password** | String | Nein |

#### <a name="curl-command"></a>Curl-Befehl

> [!div class="mx-tdBreakAll"]
> | type | APIs | Beispiel |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMINISTRATORBENUTZERNAME>","admin_password":"<ADMINISTRATORKENNWORT>","username": "<BENUTZERNAME>","new_password": "<NEUES_KENNWORT>"}' -H 'Content-Type: application/json'  https://<IP-ADRESSE>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>Spezifikationen der APIs der lokalen Verwaltungskonsole

In diesem Abschnitt werden die folgenden APIs der lokalen Verwaltungskonsole beschrieben:

- **/external/v1/alerts/<UUID>**

- **Warnungsausschlüsse (Wartungsfenster)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Das Fenster „Warnungsausschlüsse“ mit aktiven Regeln":::

Legen Sie Bedingungen fest, bei denen keine Warnungen gesendet werden. Definieren und aktualisieren Sie beispielsweise die End- und Startzeiten, Geräte oder Subnetze, die beim Auslösen von Warnungen ausgeschlossen werden sollen, oder Defender für IoT-Engines, die nicht einbezogen werden sollen. Während eines Wartungsfensters können Sie beispielsweise die Übermittlung aller Warnungen mit Ausnahme von Malwarewarnungen auf wichtigen Geräten unterbrechen.

Die hier definierten APIs werden in der lokalen Verwaltungskonsole im Fenster **Warnungsausschlüsse** als schreibgeschützte Ausschlussregel angezeigt.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Antwortbeispiel**

- **Antwort:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Ändern des Kennworts: /external/authentication/set_password

Mit dieser API können Sie Benutzern ermöglichen, ihre eigenen Kennwörter zu ändern. Alle Defender für IoT-Benutzerrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualisieren von Benutzerkennwörtern durch den Systemadministrator: /external/authentication/set_password_by_admin

Mit dieser API können Sie Systemadministratoren ermöglichen, die Kennwörter für bestimmte Benutzer zu ändern. Defender für IoT-Administratorrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

### <a name="retrieve-device-information---externalv1devices"></a>Abrufen von Geräteinformationen: /external/v1/devices

Diese API fordert eine Liste aller Geräte an, die von Defender für IoT-Sensoren, die mit einer lokalen Verwaltungskonsole verbunden sind, erkannt wurden.

#### <a name="method"></a>Methode

**GET**

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Geräte darstellen.

#### <a name="query-parameters"></a>Abfrageparameter

- **authorized**: Filtert nur nach autorisierten und nicht autorisierten Geräten.

- **siteId**: Filtert nur Geräte, die mit bestimmten Standorten verknüpft sind.

- **zoneId**: Filtert nur Geräte, die mit bestimmten Zonen verknüpft sind. [1](#1)

- **sensorId**: Filtert nur Geräte, die von bestimmten Sensoren erkannt wurden. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Die Standort- und Zonen-IDs sind Ihnen möglicherweise nicht bekannt. Fragen Sie in diesem Fall alle Geräte ab, um die Standort- und Zonen-IDs abzurufen.*

#### <a name="query-parameters-example"></a>Beispiel für Abfrageparameter

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Gerätefelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **sensorId** | Numeric | Nein | - |
| **zoneId** | Numeric | Ja | - |
| **siteId** | Numeric | Ja | - |
| **ipAddresses** | JSON-Array | Ja | IP-Adressen (können bei Internetadressen oder einem Gerät mit dualen Netzwerkkarten mehrere Adressen sein) |
| **name** | String | Nein | - |
| **type** | String | Nein | Unknown, Engineering Station, PLC, HMI, Historian, Domain Controller, DB Server, Wireless Access Point, Router, Switch, Server, Workstation, IP Camera, Printer, Firewall, Terminal station, VPN Gateway, Internet oder Multicast und Broadcast |
| **macAddresses** | JSON-Array | Ja | MAC-Adressen (können bei einem Gerät mit dualen Netzwerkkarten mehrere Adressen sein) |
| **operatingSystem** | String | Ja | - |
| **engineeringStation** | Boolesch | Nein | „true“ oder „false“ |
| **scanner** | Boolesch | Nein | „true“ oder „false“ |
| **authorized** | Boolesch | Nein | „true“ oder „false“ |
| **vendor** | String | Ja | - |
| **Protokolle** | JSON-Array | Ja | Protokollobjekt |
| **firmware** | JSON-Array | Ja | Firmwareobjekt |

#### <a name="protocol-fields"></a>Protokollfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| Name | String | Nein | - |
| Adressen | JSON-Array | Ja | Master oder numerische Werte |

#### <a name="firmware-fields"></a>Firmwarefelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **serial** | String | Nein | N/V oder der tatsächliche Wert |
| **model** | String | Nein | N/V oder der tatsächliche Wert |
| **firmwareVersion** | Double | Nein | N/V oder der tatsächliche Wert |
| **additionalData** | String | Nein | N/V oder der tatsächliche Wert |
| **moduleAddress** | String | Nein | N/V oder der tatsächliche Wert |
| **rack** | String | Nein | N/V oder der tatsächliche Wert |
| **slot** | String | Nein | N/V oder der tatsächliche Wert |
| **address** | String | Nein | N/V oder der tatsächliche Wert |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" 'https://<IP-ADRESSE>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Abrufen von Informationen zu Warnungen: /external/v1/alerts

Mit dieser API können Sie alle oder gefilterte Warnungen von einer lokalen Verwaltungskonsole abrufen.

#### <a name="method"></a>Methode

**GET**

#### <a name="query-parameters"></a>Abfrageparameter

- **state**: Filtert nur nach bearbeiteten und unbearbeiteten Warnungen.

  **Beispiel**:

  `/api/v1/alerts?state=handled`

- **fromTime**: Zum Filtern von Warnungen, die zu einem bestimmten Zeitpunkt erstellt wurden (in Millisekunden, UTC).

  **Beispiel**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: Zum Filtern von Warnungen, die vor einem bestimmten Zeitpunkt erstellt wurden (in Millisekunden, UTC).

  **Beispiel**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**: Der Standort, an dem die Warnung erkannt wurde. [2](#2)

- **zoneId**: Die Zone, in der die Warnung erkannt wurde. [2](#2)

- **sensor**: Der Sensor, auf dem die Warnung erkannt wurde.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *Die Standort- und Zonen-IDs sind Ihnen möglicherweise nicht bekannt. Fragen Sie in diesem Fall alle Geräte ab, um die Standort- und Zonen-IDs abzurufen.*

#### <a name="alert-fields"></a>Warnungsfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **ID** | Numeric | Nein | - |
| **time** | Numeric | Nein | Epoche (UTC) |
| **title** | String | Nein | - |
| **Nachricht** | String | Nein | - |
| **severity** | String | Nein | Warning, Minor, Major oder Critical (Warnung, gering, wichtig oder kritisch) |
| **engine** | String | Nein | Protocol Violation, Policy Violation, Malware, Anomaly oder Operational (Protokollverletzung, Richtlinienverletzung, Malware, Anomalie oder Betrieb) |
| **sourceDevice** | Numeric | Ja | Geräte-ID |
| **destinationDevice** | Numeric | Ja | Geräte-ID |
| **additionalInformation** | Zusätzliches Informationsobjekt | Ja | - |

#### <a name="additional-information-fields"></a>Zusätzliche Informationsfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **description** | String | Nein | - |
| **information** | JSON-Array | Nein | String |

#### <a name="response-example"></a>Antwortbeispiel

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Curl-Befehl

> [!div class="mx-tdBreakAll"]
> | type | APIs | Beispiel |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" 'https://<IP-ADRESSE>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>QRadar-Warnungen

Die QRadar-Integration in Defender für IoT unterstützt Sie beim Identifizieren der von Defender für IoT generierten Warnungen und beim Durchführen von Aktionen aufgrund dieser Warnungen. QRadar empfängt die Daten von Defender für IoT und kontaktiert dann die öffentliche API der lokalen Verwaltungskonsolenkomponente.

Um die von Defender für IoT ermittelten Daten an QRadar zu senden, definieren Sie im Defender für IoT-System eine Weiterleitungsregel, und wählen Sie die Option **Remoteunterstützung für Warnungsbearbeitung** aus.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Bearbeiten der Weiterleitungsregeln entsprechend Ihren Anforderungen":::

Wenn Sie diese Option bei der Konfiguration von Weiterleitungsregeln auswählen, werden in QRadar die folgenden zusätzlichen Felder angezeigt:

- **UUID**: Eindeutiger Warnungsbezeichner, z. B. 1-1555245116250.

- **Site**: Der Standort, an dem die Warnung ermittelt wurde.

- **Zone**: Die Zone, in der die Warnung ermittelt wurde.

Beispiel für die an QRadar gesendete Payload:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/&lt;UUID&gt;

#### <a name="method"></a>Methode

**PUT**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="request-content"></a>Inhalt anfordern

JSON-Objekt, das die Aktion darstellt, die für die Warnung mit der UUID ausgeführt werden soll.

#### <a name="action-fields"></a>Aktionsfelder

| Name | Typ | Nullwerte zulässig | Liste der Werte |
|--|--|--|--|
| **action** | String | Nein | „handle“ oder „handleAndLearn“ |

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Geräte darstellen.

#### <a name="response-fields"></a>Antwortfelder


| Name | Typ | Nullwerte zulässig | BESCHREIBUNG |
|--|--|--|--|
| **content/error** | String | Nein | Wenn die Anforderung erfolgreich ist, wird die Eigenschaft „content“ angezeigt. Andernfalls wird die Eigenschaft „error“ angezeigt. |

#### <a name="possible-content-values"></a>Mögliche Werte für die Eigenschaft „content“

| Statuscode | Wert für „content“ | BESCHREIBUNG |
|--|--|--|
| 200 | Die Aktualisierungsanforderung der Warnung wurde erfolgreich abgeschlossen. | Die Aktualisierungsanforderung wurde erfolgreich abgeschlossen. Keine Kommentare. |
| 200 | Die Warnung wurde bereits bearbeitet (**handle**). | Die Warnung wurde bereits bearbeitet, als eine „handle“-Anforderung für die Warnung empfangen wurde.<br />Die Warnung bleibt im Status **handled**. |
| 200 | Für die Warnung wurde bereits ein Bearbeitungs- und Lernvorgang durchgeführt (**handleAndLearn**). | Für die Warnung wurde bereits ein Bearbeitungs- und Lernvorgang durchgeführt, als eine **handleAndLearn**-Anforderung empfangen wurde.<br />Die Warnung bleibt im Status **handledAndLearn**. |
| 200 | Die Warnung wurde bereits bearbeitet (**handled**).<br />Für die Warnung wurde bereits ein Bearbeitungs- und Lernvorgang (**handleAndLearn**) ausgeführt. | Die Warnung wurde bereits bearbeitet, als eine **handleAndLearn**-Anforderung empfangen wurde.<br />Die Warnung erhält den Status **handleAndLearn**. |
| 200 | Für die Warnung wurde bereits ein Bearbeitungs- und Lernvorgang durchgeführt (**handleAndLearn**). Die Bearbeitungsanforderung wurde ignoriert. | Die Warnung befand sich bereits im Status **handleAndLearn**, als eine Anforderung zum Bearbeiten der Warnung empfangen wurde. Die Warnung bleibt im Status **handleAndLearn**. |
| 500 | Ungültige Aktion. | Die gesendete Aktion ist keine gültige Aktion, die für die Warnung ausgeführt werden kann. |
| 500 | Unerwarteter Fehler. | Ein unerwarteter Fehler ist aufgetreten. Wenden Sie sich an den technischen Support, um das Problem zu beheben. |
| 500 | Die Anforderung konnte nicht ausgeführt werden, da für diese UUID keine Warnung gefunden wurde. | Die angegebene Warnungs-UUID wurde im System nicht gefunden. |

#### <a name="response-example"></a>Antwortbeispiel

**Erfolgreich**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Nicht erfolgreich**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| PUT | curl -k -X PUT -d '{"action": "<ACTION>"}' -H "Authorization: <AUTORISIERUNGSTOKEN>" https://<IP-ADRESSE>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Warnungsausschlüsse (Wartungsfenster): /external/v1/maintenanceWindow

Legen Sie Bedingungen fest, bei denen keine Warnungen gesendet werden. Definieren und aktualisieren Sie beispielsweise die End- und Startzeiten, Geräte oder Subnetze, die beim Auslösen von Warnungen ausgeschlossen werden sollen, oder Defender für IoT-Engines, die nicht einbezogen werden sollen. Während eines Wartungsfensters können Sie beispielsweise die Übermittlung aller Warnungen mit Ausnahme von Malwarewarnungen auf wichtigen Geräten unterbrechen.

Die hier definierten APIs werden in der lokalen Verwaltungskonsole im Fenster **Warnungsausschlüsse** als schreibgeschützte Ausschlussregel angezeigt.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Das Fenster „Warnungsausschlüsse“ mit einer Liste aller Ausschlussregeln":::

#### <a name="method---post"></a>Methode: POST

#### <a name="query-parameters"></a>Abfrageparameter

- **ticketId**: Definiert die ID des Wartungstickets in den Systemen des Benutzers.

- **ttl**: Definiert die Gültigkeitsdauer (Time to Live, TTL), welche die Dauer des Wartungsfensters in Minuten angibt. Nach dem Zeitraum, den dieser Parameter definiert, beginnt das System automatisch mit dem Senden von Warnungen.

- **engines**: Definiert die Sicherheitsengine, deren Warnungen während des Wartungsvorgangs unterdrückt werden sollen:

   - ANOMALY

   - MALWARE

   - OPERATIONAL

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: Definiert den Defender für IoT-Sensor, dessen Warnungen während des Wartungsvorgangs unterdrückt werden sollen. Dabei handelt es sich um dieselbe ID, die unter „/api/v1/appliances (GET)“ abgerufen wurde.

- **subnets**: Definiert das Subnetz, aus dem Warnungen während des Wartungsvorgangs unterdrückt werden sollen: Das Subnetz wird in folgendem Format gesendet: 192.168.0.0/16.

#### <a name="error-codes"></a>Fehlercodes

- **201 (Created)** : Die Aktion wurde erfolgreich abgeschlossen.

- **400 (Bad Request)** : Wird in folgenden Fällen angezeigt:

   - Der Parameter **ttl** ist nicht numerisch oder nicht positiv.

   - Der Parameter **subnets** wurde in einem falschen Format definiert.

   - Der Parameter **ticketId** fehlt.

   - Der unter **engine** angegebene Parameter stimmt nicht mit den vorhandenen Sicherheitsengines überein.

- **404 (Not Found)** : Einer der Sensoren ist nicht vorhanden.

- **409 (Conflict)** : Die Ticket-ID ist mit einem anderen geöffneten Wartungsfenster verknüpft.

- **500 (Internal Server Error)** : Sonstiger unerwarteter Fehler.

> [!NOTE]
> Stellen Sie sicher, dass die Ticket-ID nicht mit einem vorhandenen geöffneten Wartungsfenster verknüpft ist. Die folgende Ausschlussregel wird generiert: Maintenance-{Tokenname}-{Ticket-ID}.

#### <a name="method---put"></a>Methode: PUT

Durch Ändern des Parameters **ttl** können Sie die Dauer des Wartungsfensters aktualisieren, nachdem Sie den Wartungsvorgang gestartet haben. Die neue Definition der Dauer überschreibt die vorherige Definition.

Diese Methode ist hilfreich, wenn Sie anstelle der aktuell konfigurierten Dauer eine längere Zeitspanne festlegen möchten.

#### <a name="query-parameters"></a>Abfrageparameter

- **ticketId**: Definiert die ID des Wartungstickets in den Systemen des Benutzers.

- **ttl**: Definiert die Dauer des Fensters in Minuten.

#### <a name="error-code"></a>Fehlercode

- **200 (OK)** : Die Aktion wurde erfolgreich abgeschlossen.

- **400 (Bad Request)** : Wird in folgenden Fällen angezeigt:

   - Der Parameter **ttl** ist nicht numerisch oder nicht positiv.

   - Der Parameter **ticketId** fehlt.

   - Der Parameter **ttl** fehlt.

- **404 (Not Found)** : Die Ticket-ID ist nicht mit einem geöffneten Wartungsfenster verknüpft.

- **500 (Internal Server Error)** : Sonstiger unerwarteter Fehler.

> [!NOTE]
> Stellen Sie sicher, dass die Ticket-ID mit einem vorhandenen geöffneten Wartungsfenster verknüpft ist.

#### <a name="method---delete"></a>Methode: DELETE

Schließt ein vorhandenes Wartungsfenster.

#### <a name="query-parameters"></a>Abfrageparameter

- **ticketId**: Protokolliert die ID des Wartungstickets in den Systemen des Benutzers.

#### <a name="error-code"></a>Fehlercode

- **200 (OK)** : Die Aktion wurde erfolgreich abgeschlossen.

- **400 (Bad Request)** : Der Parameter **ticketId** fehlt.

- **404 (Not Found)** : Die Ticket-ID ist nicht mit einem geöffneten Wartungsfenster verknüpft.

- **500 (Internal Server Error)** : Sonstiger unerwarteter Fehler.

> [!NOTE]
> Stellen Sie sicher, dass die Ticket-ID mit einem vorhandenen geöffneten Wartungsfenster verknüpft ist.

#### <a name="method---get"></a>Methode: GET

Ruft ein Protokoll aller Aktionen zum Öffnen, Schließen und Aktualisieren ab, die im System während der Wartung ausgeführt wurden. Sie können ein Protokoll nur für Wartungsfenster abrufen, die in der Vergangenheit aktiv waren und geschlossen wurden.

#### <a name="query-parameters"></a>Abfrageparameter

- **fromDate**: Filtert die Protokolle aufsteigend ab dem vordefinierten Datum. Das Format lautet 2019-12-30.

- **toDate**: Filtert die Protokolle bis zum vordefinierten Datum. Das Format lautet 2019-12-30.

- **ticketId**: Filtert die Protokolle, die mit einer bestimmten Ticket-ID verknüpft sind.

- **tokenName**: Filtert die Protokolle, die mit einem bestimmten Tokennamen verknüpft sind.

#### <a name="error-code"></a>Fehlercode

- **200 (OK)** : Die Aktion wurde erfolgreich abgeschlossen.

- **400 (Bad Request)** : Das Datumsformat ist falsch.

- **204 (No Content)** : Es sind keine anzuzeigenden Daten vorhanden.

- **500 (Internal Server Error)** : Sonstiger unerwarteter Fehler.

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Array von JSON-Objekten, die Vorgänge im Wartungsfenster darstellen.

#### <a name="response-structure"></a>Antwortstruktur

| Name | Typ | Kommentar | Nullwerte zulässig |
|--|--|--|--|
| **dateTime** | String | Beispiel: „2012-04-23T18:25:43.511Z“ | nein |
| **ticketId** | String | Beispiel: „9a5fe99c-d914-4bda-9332-307384fe40bf“ | nein |
| **tokenName** | Zeichenfolge | - | nein |
| **engines** | Zeichenfolgenarray | - | ja |
| **sensorIds** | Zeichenfolgenarray | - | ja |
| **Subnetze** | Zeichenfolgenarray | - | ja |
| **ttl** | Numeric | - | ja |
| **operationType** | String | Werte sind „OPEN“, „UPDATE“ und „CLOSE“ | nein |

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET-ID>",ttl": <GÜLTIGKEITSDAUER>,"engines": [<ENGINE1, ENGINE2...ENGINEn>],"sensorIds": [<SENSOR-ID1, SENSOR-ID2...SENSOR-IDn>],"subnets": [<SUBNETZ1, SUBNETZ2....SUBNETZn>]}' -H "Authorization: <AUTORISIERUNGSTOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","3"],"subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET-ID>",ttl": "<GÜLTIGKEITSDAUER>"}' -H "Authorization: <AUTORISIERUNGSTOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET-ID>"}' -H "Authorization: <AUTORISIERUNGSTOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Authorization: <AUTORISIERUNGSTOKEN>" 'https://<IP-ADRESSE>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-01-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Authentifizieren von Benutzeranmeldeinformationen: /external/authentication/validation

Mit dieser API können Sie Benutzeranmeldeinformationen überprüfen. Alle Defender für IoT-Benutzerrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Meldungszeichenfolge mit Details zum Vorgangsstatus:

- **Success – Meldung**: Authentifizierung erfolgreich

- **Failure – Fehler**: Fehler beim Überprüfen der Anmeldeinformationen

#### <a name="device-fields"></a>Gerätefelder

| **Name** | **Typ** | **Nullwerte zulässig** |
|--|--|--|
| **username** | String | Nein |
| **password** | String | Nein |

#### <a name="response-example"></a>Antwortbeispiel

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| POST | curl -k -d '{"username":"<BENUTZERNAME>","password":"<KENNWORT>"}' 'https://<IP-ADRESSE>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":"1234@abcd"}' 'https:/<span>/127.0.0.1/external/authentication/validation' |

### <a name="change-password---externalauthenticationset_password"></a>Ändern des Kennworts: /external/authentication/set_password

Mit dieser API können Sie Benutzern ermöglichen, ihre eigenen Kennwörter zu ändern. Alle Defender für IoT-Benutzerrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Meldungszeichenfolge mit Details zum Vorgangsstatus:

- **Success – Meldung**: Das Kennwort wurde ersetzt

- **Failure – Fehler**: Fehler bei der Benutzerauthentifizierung

- **Failure – Fehler**: Das Kennwort entspricht nicht der Sicherheitsrichtlinie

#### <a name="response-example"></a>Antwortbeispiel

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Gerätefelder

| **Name** | **Typ** | **Nullwerte zulässig** |
|--|--|--|
| **username** | String | Nein |
| **password** | String | Nein |
| **new_password** | String | Nein |

#### <a name="curl-command"></a>Curl-Befehl

| type | APIs | Beispiel |
|--|--|--|
| POST | curl -k -d '{"username": "<BENUTZERNAME>","password": "<AKTUELLES_KENNWORT>","new_password": "<NEUES_KENNWORT>"}' -H 'Content-Type: application/json'  https://<IP-ADRESSE>/external/authentication/set_password | curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualisieren von Benutzerkennwörtern durch den Systemadministrator: /external/authentication/set_password_by_admin

Mit dieser API können Sie Systemadministratoren ermöglichen, die Kennwörter für bestimmte Benutzer zu ändern. Defender für IoT-Administratorrollen können mit der API arbeiten. Zum Verwenden dieser API benötigen Sie kein Defender für IoT-Zugriffstoken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Anforderungstyp

**JSON**

#### <a name="request-example"></a>Anforderungsbeispiel

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Antworttyp

**JSON**

#### <a name="response-content"></a>Antwortinhalt

Meldungszeichenfolge mit Details zum Vorgangsstatus:

- **Success – Meldung**: Das Kennwort wurde ersetzt

- **Failure – Fehler**: Fehler bei der Benutzerauthentifizierung

- **Failure – Fehler**: Benutzer ist nicht vorhanden

- **Failure – Fehler**: Das Kennwort entspricht nicht der Sicherheitsrichtlinie

- **Failure – Fehler**: Der Benutzer hat keine Berechtigung zum Ändern des Kennworts

#### <a name="response-example"></a>Antwortbeispiel

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Gerätefelder

| **Name** | **Typ** | **Nullwerte zulässig** |
|--|--|--|
| **admin_username** | String | Nein |
| **admin_password** | String | Nein |
| **username** | String | Nein |
| **new_password** | String | Nein |

#### <a name="curl-command"></a>Curl-Befehl

> [!div class="mx-tdBreakAll"]
> | type | APIs | Beispiel |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMINISTRATORBENUTZERNAME>","admin_password":"<ADMINISTRATORKENNWORT>","username": "<BENUTZERNAME>","new_password": "<NEUES_KENNWORT>"}' -H 'Content-Type: application/json'  https://<IP-ADRESSE>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen von Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)

[Untersuchen aller Unternehmenssensorerkennungen in einem Gerätebestand](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
