---
title: Aktualisieren von Threat Intelligence-Daten
description: Das Threat Intelligence-Datenpaket wird mit jeder neuen Defender für IoT-Version oder bei Bedarf zwischen den Releases bereitgestellt.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: a210771d99d28a0e9c15d7952d491a5e5f94e704
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521406"
---
# <a name="threat-intelligence-research-and-packages"></a>Forschung und Pakete zur Bedrohungsanalyse

Sicherheitsteams in Microsoft führen proprietäre Forschungen von ICS-Bedrohungsanalyse und Sicherheitsrisiken aus. Zu diesen Teams gehören MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (Digital Crimes Unit) und Section 52 (IoT/OT/ICS-Domänenexperten, die ICS-spezifische Zero-Days, Reverse-Engineering-Schadsoftware, Kampagnen und Angreifer verfolgen).

Die Teams bieten Sicherheitsermittlung, -analyse und -reaktion für folgende Komponenten von Microsoft:

- Cloudinfrastruktur und Dienste.
- Herkömmliche Produkte und Geräte.
- Interne Unternehmensressourcen.

Sicherheitsteams profitieren von folgenden Vorteilen:

- Schutz vor bekannten und relevanten Bedrohungen.
- Einblicke, die Ihnen bei der Triage und Prioritätensetzung helfen.
- Ein Verständnis für den vollen Kontext von Bedrohungen, bevor sie betroffen sind.
- Relevantere, präzisere und handlungsrelevante Daten.

Diese Intelligenz fügt kontextbezogene Informationen hinzu, um die Analysen der Microsoft-Plattform zu bereichern, und unterstützt die verwalteten Dienste des Unternehmens bei der Reaktion auf Vorfälle und der Untersuchung von Sicherheitsverletzungen. Threat Intelligence-Pakete enthalten Signaturen (einschließlich Schadsoftwaresignaturen), CVEs und andere Sicherheitsinhalte.

Sie können Pakete von der Seite **Updates** im Azure Defender für IoT-Portal herunterladen.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Herunterladen von Updates über das Azure Defender für IoT-Portal.":::

## <a name="update-threat-intelligence-data"></a>Aktualisieren von Threat Intelligence-Daten

Threat Intelligence-Pakete werden mit jedem neuen Defender für IoT-Versionsupdate oder bei Bedarf zwischen den Releases bereitgestellt.

Pakete, die Sie aus dem Defender für IoT-Portal herunterladen, können manuell auf einzelne Sensoren hochgeladen werden. Wenn Ihre Sensoren über die lokale Verwaltungskonsole verwaltet werden, können Sie Threat Intelligence-Pakete auf die Verwaltungskonsole herunterladen und sie an mehrere Sensoren gleichzeitig übertragen.

So aktualisieren Sie ein Paket auf einem einzelnen Sensor

1. Navigieren Sie zur Azure Defender für IoT-Seite **Updates**.

2. Laden Sie das **Threat Intelligence**-Paket herunter, und speichern Sie es.

3. Melden Sie sich bei der Sensorkonsole an.

4. Wählen Sie im Seitenmenü **Systemeinstellungen** aus.

5. Wählen Sie **Threat Intelligence-Daten** und dann **Update** aus.

6. Laden Sie das neue Paket hoch.

So aktualisieren Sie ein Paket auf mehreren Sensoren gleichzeitig

1. Navigieren Sie zur Azure Defender für IoT-Seite **Updates**.

2. Laden Sie das **Threat Intelligence**-Paket herunter, und speichern Sie es.

3. Melden Sie sich bei der Verwaltungskonsole an.

4. Wählen Sie im Seitenmenü **Systemeinstellungen** aus.

5. Wählen Sie im Abschnitt **Sensor-Engine-Konfiguration** die Sensoren aus, die die aktualisierten Pakete erhalten sollen.  

6. Wählen Sie im Abschnitt **Threat Intelligence-Daten auswählen** das Pluszeichen ( **+** ) aus.

7. Hochladen des Pakets

## <a name="next-steps"></a>Nächste Schritte

[Updateversionen](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
