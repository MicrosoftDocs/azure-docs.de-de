---
title: 'Azure-VPN Gateway: Konfigurieren der Paketerfassung'
description: Erfahren Sie mehr über die Paketerfassungsfunktionen, die Sie für VPN Gateways verwenden können, um die Ursache eines Problems einzugrenzen.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: c6aa2b63344ef629d55f5a4efb66e4ee0e55975e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661034"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Konfigurieren der Paketerfassung für VPN Gateways

Konnektivitäts- und leistungsbezogene Probleme sind häufig komplex. Es kann viel Zeit und Mühe kosten, die Ursache des Problems einzugrenzen. Mithilfe der Paketerfassung können Sie den Umfang eines Problems auf bestimmte Teile des Netzwerks eingrenzen. Sie kann Ihnen dabei helfen, festzustellen, ob das Problem auf der Kundenseite des Netzwerks, auf der Azure-Seite des Netzwerks oder irgendwo dazwischen vorliegt. Nachdem Sie das Problem eingegrenzt haben, ist es effizienter, das Problem zu debuggen und Abhilfemaßnahmen zu ergreifen.

Für die Paketerfassung stehen verschiedene gängige Tools zur Verfügung. Eine relevante Paketerfassung kann mit diesen Tools umständlich sein, insbesondere in Szenarien mit hohem Datenaufkommen. Die von einer Paketerfassung für VPN Gateways bereitgestellten Filterfunktionen sind ein wichtiges Unterscheidungsmerkmal. Sie können die Paketerfassung für VPN Gateways zusammen mit allgemein verfügbaren Paketerfassungstools verwenden.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Filterfunktionen für die VPN Gateway-Paketerfassung

Die können die VPN Gateway-Paketerfassung je nach Anforderung für das Gateway oder eine bestimmte Verbindung ausführen. Es ist außerdem möglich, Paketerfassung für mehrere Tunnel gleichzeitig auszuführen. Sie können uni- oder bidirektionalen Datenverkehr, IKE- und ESP-Datenverkehr sowie interne Pakete gemeinsam mit einer Filterung für ein VPN Gateway erfassen.

Die Verwendung von 5-Tupel-Filtern (Quellsubnetz, Zielsubnetz, Quellport, Zielport, Protokoll) und TCP-Flags (SYN, ACK, FIN, URG, PSH, RST) ist hilfreich, um eine Problemisolierung bei einem hohen Datenverkehrsaufkommen durchzuführen.

Die folgenden JSON-Beispiele und ein JSON-Schema bieten Erläuterungen zu den einzelnen Eigenschaften. Beachten Sie die folgenden Einschränkungen, wenn Sie Paketerfassungen ausführen:
- Im hier gezeigten Schema ist der Filter ein Array, aber derzeit kann jeweils nur ein Filter gleichzeitig verwendet werden.
- Mehrere gatewayweite Paketerfassungen können nicht zur gleichen Zeit ausgeführt werden.
- Sie können nicht mehrere Paketerfassungen gleichzeitig für eine einzige Verbindung ausführen. Sie können Paketerfassungen für verschiedene Verbindungen gleichzeitig ausführen.
- Pro Gateway können maximal fünf Paketerfassungen parallel ausgeführt werden. Diese Paketerfassungen können eine Kombination aus der gatewayweiten Paketerfassungen und Paketerfassungen pro Verbindung sein.

### <a name="example-json"></a>JSON-Beispiel
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON-Schema
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="set-up-packet-capture-by-using-powershell"></a>Einrichten der Paketerfassung mithilfe von PowerShell

Die folgenden Beispiele zeigen PowerShell-Befehle zum Starten und Beenden von Paketerfassungen. Weitere Informationen zu Parameteroptionen finden Sie unter [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Starten der Paketerfassung für ein VPN-Gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Sie können den optionalen Parameter `-FilterData` verwenden, um einen Filter anzuwenden.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Beenden der Paketerfassung für ein VPN-Gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Starten der Paketerfassung für eine VPN-Gatewayverbindung

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Sie können den optionalen Parameter `-FilterData` verwenden, um einen Filter anzuwenden.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Beenden der Paketerfassung für eine VPN-Gatewayverbindung

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Wichtige Aspekte

- Das Ausführen von Paketerfassungen kann sich auf die Leistung auswirken. Denken Sie daran, die Paketerfassung zu beenden, wenn sie nicht benötigt wird.
- Die empfohlene Mindestdauer für die Paketerfassung beträgt 600 Sekunden. Aufgrund von Synchronisierungsproblemen zwischen mehreren Komponenten im Pfad stellen kürzere Paketerfassungen möglicherweise keine vollständigen Daten bereit.
- Bei der Paketerfassung werden Datendateien im PCAP-Format generiert. Verwenden Sie Wireshark oder andere allgemein verfügbare Anwendungen, um PCAP-Dateien zu öffnen.
- Paketerfassungen werden bei richtlinienbasierten Gateways nicht unterstützt.
- Wenn der `SASurl`-Parameter nicht ordnungsgemäß konfiguriert ist, treten bei der Ablaufverfolgung möglicherweise Speicherfehler auf. Beispiele zum ordnungsgemäßen Generieren von `SASurl`-Parametern finden Sie unter [Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VPN Gateways finden Sie unter [Was ist ein VPN Gateway?](vpn-gateway-about-vpngateways.md)