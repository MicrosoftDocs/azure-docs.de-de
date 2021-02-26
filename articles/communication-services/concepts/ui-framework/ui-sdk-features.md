---
title: 'Azure Communication Services: Funktionen des Benutzeroberflächen-Frameworks'
titleSuffix: An Azure Communication Services conceptual document
description: Enthält eine Beschreibung der Funktionen des Benutzeroberflächen-Frameworks.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 51caf1e73cdeda5862e033a05878f35ddb6ff8dc
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539707"
---
# <a name="ui-framework-capabilities"></a>Funktionen des Benutzeroberflächen-Frameworks

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Mit dem Benutzeroberflächen-Framework von Azure Communication Services können Sie Kommunikationsumgebungen entwickeln, indem Sie verschiedene wiederverwendbare Komponenten nutzen. Diese Komponenten sind in zwei Bereiche unterteilt: Bei den Komponenten vom Typ **Basis** handelt es sich um die grundlegenden Bausteine Ihrer Benutzeroberfläche, während Kombinationen dieser Basiskomponenten als **zusammengesetzte** Komponenten bezeichnet werden.

## <a name="ui-framework-composite-components"></a>Zusammengesetzte Komponenten des Benutzeroberflächen-Frameworks

| Composite               | BESCHREIBUNG                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Zusammengesetzte Komponente für Gruppenanrufe | Einfache Umgebung für ausgehende Sprach- und Videoanrufe für Azure Communication Services-Anrufe mit Entwurfsressourcen der Fluent-Benutzeroberfläche. Unterstützung von Gruppenanrufen per Azure Communication Services-Gruppen-ID. Die zusammengesetzte Komponente ermöglicht 1:1-Anrufe, indem eine Azure Communication Services-Identität oder eine Festnetz-Telefonnummer, die über Azure beschafft wurde, angegeben wird.                                    | React |  |  |
| Zusammengesetzte Komponente für Gruppenchat    | Einfache Chatumgebung für Azure Communication Services mit Entwurfsressourcen der Fluent-Benutzeroberfläche. Bei dieser Benutzeroberfläche geht es um die Bereitstellung eines einfachen Chatclients, mit dem eine Verbindung mit Azure Communication Services-Threads hergestellt werden kann. Für Benutzer wird so das Senden von Nachrichten und das Anzeigen empfangener Nachrichten mit Eingabeindikatoren und Lesebestätigungen ermöglicht. Es sind Chats zwischen zwei Personen und in der Gruppe möglich. Hierbei wird nur ein Chatthread unterstützt.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Benutzeroberflächen-Framework: Basiskomponenten

| Komponente             | BESCHREIBUNG                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Anrufanbieter    | Zentrale Initialisierungskomponente für Anrufe. Erforderliche Komponente für die weitere Initialisierung anderer Komponenten. Verarbeitet die Kernlogik für die Initialisierung des aufrufenden Clients mit Azure Communication Services-Zugriffstoken. Unterstützt den Gruppenbeitritt. | React | Nicht zutreffend     | Nicht zutreffend |
| Mediensteuerungen   | Ermöglicht Benutzern die Verwaltung des aktuellen Anrufs mit Optionen zum Stummschalten, Aktivieren/Deaktivieren der Videofunktion und Beenden des Anrufs.                                                                                                                                                              | React | Nicht zutreffend     | Nicht zutreffend |
| Mediengalerie   | Alle Teilnehmer des Anrufs werden in einer zentralen Galerie angezeigt. Für die Galerie wird die Anzeige von Teilnehmern mit und ohne Videoaktivierung unterstützt. Bei Teilnehmern mit Videoaktivierung werden die Videodaten angezeigt.                                                                                                                | React | Nicht zutreffend     | Nicht zutreffend |
| Mikrofoneinstellungen | Hier wählen Sie das Mikrofon aus, das für Anrufe verwendet werden soll. Dieses Steuerelement kann vor und während des Anrufs genutzt werden, um das Mikrofongerät auszuwählen.                                                                                                                                               | React | Nicht zutreffend     | Nicht zutreffend |
| Kameraeinstellungen     | Hier wählen Sie die Kamera aus, die für Videoanrufe verwendet werden soll. Dieses Steuerelement kann vor und während des Anrufs genutzt werden, um das Kameragerät auszuwählen.                                                                                                                                             | React | Nicht zutreffend     | Nicht zutreffend |
| Geräteeinstellungen     | Kombination der Einstellungen für Mikrofon und Kamera in einer gemeinsamen Komponente.                                                                                                 | React | Nicht zutreffend     | Nicht zutreffend |
| Chatanbieter       | Zentrale Initialisierungskomponente für Chats. Erforderliche Komponente für die weitere Initialisierung anderer Komponenten. Verarbeitung der Kernlogik zum Initialisieren des Chatclients mit einem Azure Communication Services-Zugriffstoken und dem Thread für den Beitritt.                                     | React | Nicht zutreffend     | Nicht zutreffend |
| Sendefeld          | Eingabekomponente, die Benutzern das Senden von Nachrichten an den Chatthread ermöglicht. In Bezug auf die Eingabe werden Text, Hyperlinks, Emojis und andere Unicode-Zeichen (auch andere Alphabete) unterstützt.                                                                                                                         | React | Nicht zutreffend     | Nicht zutreffend |
| Chatthread           | Threadkomponente, in der dem Benutzer die empfangenen und gesendeten Nachrichten mit den Absenderinformationen angezeigt werden. Für den Thread werden Eingabeindikatoren und Lesebestätigungen unterstützt. Sie können durch diese Threads scrollen, um den Chatverlauf zu überprüfen.
| Teilnehmerliste      | Alle Teilnehmer des Anrufs bzw. Chatthreads werden als Liste angezeigt.  | React | Nicht zutreffend     | Nicht zutreffend |

## <a name="ui-framework-capabilities"></a>Funktionen des Benutzeroberflächen-Frameworks

| Funktion                                                             | Zusammengesetzte Komponente für Gruppenanrufe | Zusammengesetzte Komponente für Gruppenchat | Basiskomponenten |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Teams-Besprechung beitreten                                                  |                         |                      |           
| Teams-Liveereignis beitreten                                               |                         |                      | 
| VoIP-Anruf mit Teams-Benutzer starten                                       |                         |                      | 
| Teams-Besprechungschat beitreten                                           |                         |                      |            
| Azure Communication Services-Anruf mit Gruppen-ID beitreten                | ✔                      |                      | ✔
| VoIP-Anruf mit Azure Communication Services-Benutzer(n) starten |                         |                      |           
| Azure Communication Services-Chatthread beitreten                    |                         | ✔                   | ✔
| Stummschalten ein/aus für Anruf                                                    | ✔                       |                      | ✔
| Video ein/aus für Anruf                                                | ✔                       |                      | ✔
| Bildschirmfreigabe                                                      | ✔                       |                      | ✔
| Teilnehmergalerie                                                 | ✔                       |                      | ✔
| Mikrofonverwaltung                                               | ✔                       |                      | ✔
| Kameraverwaltung                                                   | ✔                       |                      | ✔
| Wartebereich für Anrufe                                                          |                         |                      | ✔
| Chatnachricht senden                                                   |                         | ✔                   |            
| Chatnachricht empfangen                                                |                         | ✔                   | ✔
| Eingabeindikatoren                                                   |                         | ✔                   | ✔
| Lesebestätigung                                                        |                         | ✔                   | ✔
| Teilnehmerliste                                                    |                         |                      | ✔


## <a name="customization-support"></a>Anpassungsunterstützung

| Komponententyp            | Designs     | Layout                                                              | Datenmodelle |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Zusammengesetzte Komponente       |     Nicht zutreffend    | Nicht zutreffend                                                                 |     Nicht zutreffend     |
| Basiskomponente            |     Nicht zutreffend    | Layout von Komponenten kann mit externer Formatierung geändert werden         |     Nicht zutreffend     |


## <a name="platform-support"></a>Plattformunterstützung

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| Benutzeroberflächen-SDK | Chrome\*, Edge (neu) | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Hinweis: Neben den beiden vorherigen Releases wird auch die neueste Version von Chrome unterstützt.

\*\*Hinweis: Safari wird ab Version 13.1 unterstützt. Ausgehende Videodaten werden für Safari macOS noch nicht unterstützt, für iOS dagegen schon. Die ausgehende Bildschirmfreigabe wird in der Desktopversion von iOS unterstützt.
