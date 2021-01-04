---
title: Automatische Skalierung v1
description: Automatische Skalierung und App Service-Umgebung v1. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021655"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatische Skalierung und App Service-Umgebung v1

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v1 behandelt.  Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).
> 

Azure App Service-Umgebungen unterstützen die *automatische Skalierung*. Sie können einzelne Workerpools basierend auf Metriken oder nach einem Zeitplan automatisch skalieren.

![Optionen für automatische Skalierung für einen Workerpool][intro]

Durch die automatische Skalierung wird die Ressourcenverwendung optimiert, indem Sie eine App Service-Umgebung automatisch vergrößern und verkleinern, sodass sie zu Ihrem Budget und Lastprofil passt.

## <a name="configure-worker-pool-autoscale"></a>Konfigurieren der automatischen Skalierung für Workerpools
Sie können auf die Funktionalität für die automatische Skalierung über die Registerkarte **Einstellungen** des Workerpools zugreifen.

![Registerkarte „Einstellungen“ des Workerpools][settings-scale]

Die Oberfläche sollte Ihnen vertraut vorkommen, da sie der Oberfläche beim Skalieren eines App Service-Plans stark ähnelt. 

![Einstellungen für manuelle Skalierung][scale-manual]

Sie können auch ein Profil für die automatische Skalierung konfigurieren.

![Einstellungen für automatische Skalierung][scale-profile]

Profile für die automatische Skalierung sind nützlich, um Grenzwerte für die Skalierung festzulegen. Auf diese Weise erreichen Sie eine einheitliche Leistung, indem Sie einen unteren Grenzwert der Skalierung (1) festlegen, sowie vorhersagbare Kosten, indem Sie eine Obergrenze (2) festlegen.

![Skalierungseinstellungen im Profil][scale-profile2]

Nach dem Definieren eines Profils können Sie Regeln für die automatische Skalierung hinzufügen, um die Anzahl von Instanzen im Workerpool innerhalb der Grenzen des Profils hoch- oder herunterzuskalieren. Die automatische Skalierung basiert auf Metriken.

![Skalierungsregel][scale-rule]

 Alle Workerpool- oder Front-End-Metriken können zum Definieren von Regeln für die automatische Skalierung verwendet werden. Hierbei handelt es sich um die gleichen Metriken, die Sie in den Diagrammen des Ressourcenblatts überwachen oder für die Sie Warnungen festlegen können.

## <a name="autoscale-example"></a>Beispiel für die automatische Skalierung
Die automatische Skalierung einer App Service-Umgebung kann am besten anhand eines Szenarios dargestellt werden.

In diesem Artikel werden alle erforderlichen Aspekte für die Einrichtung der automatischen Skalierung beschrieben. Es werden die einzelnen Interaktionen beschrieben, die relevant sind, wenn Sie die automatische Skalierung von App Service-Umgebungen beim Hosten in einer App Service-Umgebung verwenden.

### <a name="scenario-introduction"></a>Einführung in das Szenario
Frank ist Systemadministrator in einem Unternehmen. Er hat einen Teil der Workloads, die er verwaltet, in eine App Service-Umgebung migriert.

Die App Service-Umgebung ist wie folgt für die manuelle Skalierung konfiguriert:

* **Front-Ends**: 3
* **Workerpool 1**: 10
* **Workerpool 2**: 5
* **Workerpool 3**: 5

Workerpool 1 wird für Produktionsworkloads verwendet, und Workerpool 2 und Workerpool 3 werden für Workloads der Qualitätssicherung (QA) und der Entwicklung verwendet.

Die App Service-Pläne für die Qualitätskontrolle und die Entwicklung sind für manuelles Skalieren konfiguriert. Der App Service-Plan für die Produktion ist dagegen auf automatisches Skalieren festgelegt, um Schwankungen bei Last und Datenverkehr zu bewältigen.

Frank ist mit der Anwendung gut vertraut. Er weiß, dass die Spitzenzeiten mit der höchsten Auslastung zwischen 9:00 und 18:00 Uhr liegen, da es sich um eine Branchenanwendung handelt, die von den Mitarbeitern während der Geschäftszeiten genutzt wird. Die Nutzung fällt ab, sobald die Benutzer Feierabend machen. Außerhalb der Spitzenzeiten ist immer noch eine geringe Auslastung vorhanden, da die Benutzer die App per Remotezugriff mit mobilen Geräten oder PCs zu Hause nutzen können. Der App Service-Plan für die Produktion ist bereits konfiguriert, um eine automatische Skalierung basierend auf der CPU-Auslastung mit den folgenden Regeln vorzunehmen:

![Spezifische Einstellungen für Branchen-App][asp-scale]

| **Profil für die automatische Skalierung – Werktage – App Service-Plan** | **Profil für die automatische Skalierung – Wochenenden – App Service-Plan** |
| --- | --- |
| **Name:** Profil für Werktage |**Name:** Profil für Wochenenden |
| **Skalieren nach:** Zeitplan und Leistungsregeln |**Skalieren nach:** Zeitplan und Leistungsregeln |
| **Profil:** Wochentage |**Profil:** Wochenende |
| **Typ:** Serie |**Typ:** Serie |
| **Zielbereich:** 5 bis 20 Instanzen |**Zielbereich:** 3 bis 10 Instanzen |
| **Tage:** Montag, Dienstag, Mittwoch, Donnerstag, Freitag |**Tage:** Samstag, Sonntag |
| **Startzeit**: 9:00 |**Startzeit**: 9:00 |
| **Zeitzone:** UTC-08 |**Zeitzone:** UTC-08 |
|  | |
| **Regel für die automatische Skalierung (Hochskalieren)** |**Regel für die automatische Skalierung (Hochskalieren)** |
| **Ressource:** Produktion (App Service-Umgebung) |**Ressource:** Produktion (App Service-Umgebung) |
| **Metrik:** CPU % |**Metrik:** CPU % |
| **Betrieb:** Mehr als 60 % |**Betrieb:** Mehr als 80 % |
| **Dauer:** 5 Minuten |**Dauer:** 10 Minuten |
| **Zeitaggregation**: Average |**Zeitaggregation**: Average |
| **Aktion:** Anzahl erhöhen um 2 |**Aktion:** Anzahl erhöhen um 1 |
| **Abkühlen (Minuten):** 15 |**Abkühlen (Minuten):** 20 |
|  | |
| **Regel für die automatische Skalierung (Zentral herunterskalieren)** |**Regel für die automatische Skalierung (Zentral herunterskalieren)** |
| **Ressource:** Produktion (App Service-Umgebung) |**Ressource:** Produktion (App Service-Umgebung) |
| **Metrik:** CPU % |**Metrik:** CPU % |
| **Betrieb:** Weniger als 30 % |**Betrieb:** Weniger als 20 % |
| **Dauer:** 10 Minuten |**Dauer:** 15 Minuten |
| **Zeitaggregation**: Average |**Zeitaggregation**: Average |
| **Aktion:** Anzahl verringern um 1 |**Aktion:** Anzahl verringern um 1 |
| **Abkühlen (Minuten):** 20 |**Abkühlen (Minuten):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Inflationsrate für den App Service-Plan
App Service-Pläne, die für die automatische Skalierung konfiguriert sind, nutzen dafür eine maximale Rate pro Stunde. Diese Rate kann basierend auf den Werten berechnet werden, die in der Regel für die automatische Skalierung bereitgestellt werden.

Das Verstehen und Berechnen der *Inflationsrate für den App Service-Plan* ist wichtig für die automatische Skalierung in einer App-Service-Umgebung, da Größenänderungen bei einem Workerpool erst nach einiger Zeit wirksam werden.

Die Inflationsrate für den App Service-Plan wird wie folgt berechnet:

![Berechnung der Inflationsrate für den App Service-Plan][ASP-Inflation]

Basierend auf der „Regel für die automatische Skalierung (Hochskalieren)“ für das Profil „Werktage“ des App Service-Plans für die Produktion:

![Inflationsrate für den App Service-Plan für Werktage basierend auf der „Regel für die automatische Skalierung (Hochskalieren)“][Equation1]

Für die „Regel für die automatische Skalierung (Hochskalieren)“ für das Profil „Wochenenden“ des App Service-Plans für die Produktion würde die Formel wie folgt aufgelöst werden:

![Inflationsrate für den App Service-Plan für Wochenenden basierend auf der „Regel für die automatische Skalierung (Hochskalieren)“][Equation2]

Dieser Wert kann auch für Vorgänge zum zentralen Herunterskalieren berechnet werden:

Basierend auf der „Regel für die automatische Skalierung (Zentral herunterskalieren)“ für das Profil „Werktage“ des App Service-Plans für die Produktion würde dies wie folgt aussehen:

![Inflationsrate für den App Service-Plan für Werktage basierend auf der „Regel für die automatische Skalierung (Zentral herunterskalieren)“][Equation3]

Für die „Regel für die automatische Skalierung (Zentral herunterskalieren)“ für das Profil „Wochenenden“ des App Service-Plans für die Produktion würde die Formel wie folgt aufgelöst werden:  

![Inflationsrate für den App Service-Plan für Wochenenden basierend auf der „Regel für die automatische Skalierung (Zentral herunterskalieren)“][Equation4]

Der App Service-Plan kann für die Produktion mit einer maximalen Rate von acht Instanzen pro Stunde während der Woche und vier Instanzen pro Stunde an Wochenenden vergrößert werden. Instanzen können mit einer maximalen Rate von vier Instanzen pro Stunde während der Woche und sechs Instanzen pro Stunde an Wochenenden freigegeben werden.

Wenn mehrere App Service-Pläne in einem Workerpool gehostet werden, müssen Sie die *Gesamtinflationsrate* als Summe der Inflationsraten für alle App Service-Pläne berechnen, die in diesem Workerpool gehostet werden.

![Berechnung der Gesamtinflationsrate für mehrere App Service-Pläne, die in einem Workerpool gehostet werden][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Verwenden der Inflationsrate für den App Service-Plan zum Definieren von Regeln für die automatische Skalierung für Workerpools
Workerpools, von denen App Service-Pläne gehostet werden, die für die automatische Skalierung konfiguriert sind, muss ein Kapazitätspuffer zugeordnet werden. Der Puffer ermöglicht, dass die Vorgänge der automatischen Skalierung den App Service-Plan je nach Bedarf erhöhen oder verringern. Die Mindestgröße des Puffers wäre die berechnete Gesamtinflationsrate für den App Service-Plan.

Da Skalierungsvorgänge in der App Service-Umgebung einige Zeit dauern, sollten bei jeder Änderung weitere Bedarfsänderungen berücksichtigt werden, die stattfinden können, während eine Skalierung ausgeführt wird. Zur Berücksichtigung dieser Latenz empfehlen wir Ihnen, die berechnete Gesamtinflationsrate für den App Service-Plan als Mindestanzahl von Instanzen zu verwenden, die der automatischen Skalierung für jeden Vorgang hinzugefügt werden.

Mit diesen Informationen kann Frank die folgenden Profile und Regeln für die automatische Skalierung definieren:

![Regeln für die automatische Skalierung für Branchenbeispiel][Worker-Pool-Scale]

| **Profil für die automatische Skalierung – Werktage** | **Profil für die automatische Skalierung – Wochenenden** |
| --- | --- |
| **Name:** Profil für Werktage |**Name:** Profil für Wochenenden |
| **Skalieren nach:** Zeitplan und Leistungsregeln |**Skalieren nach:** Zeitplan und Leistungsregeln |
| **Profil:** Wochentage |**Profil:** Wochenende |
| **Typ:** Serie |**Typ:** Serie |
| **Zielbereich:** 13 bis 25 Instanzen |**Zielbereich:** 6 bis 15 Instanzen |
| **Tage:** Montag, Dienstag, Mittwoch, Donnerstag, Freitag |**Tage:** Samstag, Sonntag |
| **Startzeit**: 7:00 |**Startzeit**: 9:00 |
| **Zeitzone:** UTC-08 |**Zeitzone:** UTC-08 |
|  | |
| **Regel für die automatische Skalierung (Hochskalieren)** |**Regel für die automatische Skalierung (Hochskalieren)** |
| **Ressource:** Workerpool 1 |**Ressource:** Workerpool 1 |
| **Metrik:** WorkersAvailable |**Metrik:** WorkersAvailable |
| **Betrieb:** Weniger als 8 |**Betrieb:** Weniger als 3 |
| **Dauer:** 20 Minuten |**Dauer:** 30 Minuten |
| **Zeitaggregation**: Average |**Zeitaggregation**: Average |
| **Aktion:** Anzahl erhöhen um 8 |**Aktion:** Anzahl erhöhen um 3 |
| **Abkühlen (Minuten):** 180 |**Abkühlen (Minuten):** 180 |
|  | |
| **Regel für die automatische Skalierung (Zentral herunterskalieren)** |**Regel für die automatische Skalierung (Zentral herunterskalieren)** |
| **Ressource:** Workerpool 1 |**Ressource:** Workerpool 1 |
| **Metrik:** WorkersAvailable |**Metrik:** WorkersAvailable |
| **Betrieb:** Mehr als 8 |**Betrieb:** Mehr als 3 |
| **Dauer:** 20 Minuten |**Dauer:** 15 Minuten |
| **Zeitaggregation**: Average |**Zeitaggregation**: Average |
| **Aktion:** Anzahl verringern um 2 |**Aktion:** Anzahl verringern um 3 |
| **Abkühlen (Minuten):** 120 |**Abkühlen (Minuten):** 120 |

Der im Profil definierte Zielbereich wird anhand der minimalen Instanzen, die im Profil für den App Service-Plan definiert sind, plus dem Puffer berechnet.

Der maximale Bereich wäre die Summe aller maximalen Bereiche für alle App Service-Pläne, die im Workerpool gehostet werden.

Bei den Regeln für das Hochskalieren sollte die Anzahl, um die erhöht wird, mindestens auf den einfachen Wert der Inflationsrate für den App Service-Plan für das Hochskalieren festgelegt werden.

Die Anzahl, um die verringert wird, kann zwischen dem 0,5- und 1-Fachen der Inflationsrate für den App Service-Plan für das zentrale Herunterskalieren liegen.

### <a name="autoscale-for-front-end-pool"></a>Automatische Skalierung für Front-End-Pool
Regeln für die automatische Front-End-Skalierung sind einfacher als für Workerpools. In erster Linie  
sollten Sie sicherstellen, dass für die Dauer der Messung und die Abkühltimer berücksichtigt wird, dass Skalierungsvorgänge eines App Service-Plans nicht sofort wirksam werden.

In diesem Szenario weiß Frank, dass sich die Fehlerrate erhöht, sobald Front-Ends eine CPU-Auslastung von 80% erreichen. Er legt die Regel für die automatische Skalierung zum Erhöhen von Instanzen wie folgt fest:

![Einstellungen für automatische Skalierung für Front-End-Pool][Front-End-Scale]

| **Profil für die automatische Skalierung – Front-Ends** |
| --- |
| **Name:** Automatische Skalierung – Front-Ends |
| **Skalieren nach:** Zeitplan und Leistungsregeln |
| **Profil:** Täglich |
| **Typ:** Serie |
| **Zielbereich:** 3 bis 10 Instanzen |
| **Tage:** Täglich |
| **Startzeit**: 9:00 |
| **Zeitzone:** UTC-08 |
|  |
| **Regel für die automatische Skalierung (Hochskalieren)** |
| **Ressource:** Front-End-Pool |
| **Metrik:** CPU % |
| **Betrieb:** Mehr als 60 % |
| **Dauer:** 20 Minuten |
| **Zeitaggregation**: Average |
| **Aktion:** Anzahl erhöhen um 3 |
| **Abkühlen (Minuten):** 120 |
|  |
| **Regel für die automatische Skalierung (Zentral herunterskalieren)** |
| **Ressource:** Workerpool 1 |
| **Metrik:** CPU % |
| **Betrieb:** Weniger als 30 % |
| **Dauer:** 20 Minuten |
| **Zeitaggregation**: Average |
| **Aktion:** Anzahl verringern um 3 |
| **Abkühlen (Minuten):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
