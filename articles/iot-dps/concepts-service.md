---
title: Dienstkonzepte beim Azure IoT Hub Device Provisioning-Dienst | Microsoft-Dokumentation
description: Beschreibt Konzepte der Dienstbereitstellung, die speziell für Geräte mit dem Device Provisioning Service und IoT Hub gelten
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 4a4f53f991355e634e8139f9e90bec6c508a527d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60745807"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Konzepte beim IoT Hub Device Provisioning-Dienst

Der IoT Hub Device Provisioning-Dienst ist ein Hilfsdienst für IoT Hub, mit dem Sie Geräte ohne manuelles Eingreifen auf einem angegebenen IoT Hub konfigurieren können. Mit dem Device Provisioning-Dienst können Sie Millionen von Geräten auf sichere und skalierbare Weise [automatisch bereitstellen](concepts-auto-provisioning.md).

Die Gerätebereitstellung ist ein zweiteiliger Prozess. Im ersten Schritt wird durch *Registrieren* des Geräts die Erstverbindung zwischen dem Gerät und der IoT-Lösung hergestellt. Im zweiten Schritt wird basierend auf den spezifischen Anforderungen der Lösung die geeignete *Konfiguration* auf das Gerät angewendet. Nach Ausführung beider Schritte ist das Gerät vollständig *bereitgestellt*. Mit dem Device Provisioning-Dienst werden beide Schritte automatisiert, sodass das Gerät nahtlos bereitgestellt wird.

Dieser Artikel bietet eine Übersicht über die Bereitstellungskonzepte, die sich am besten zur Verwaltung des *Diensts* eignen. Dieser Artikel ist besonders für Personen relevant, die am [Schritt zum Einrichten der Cloud](about-iot-dps.md#cloud-setup-step) bei der Vorbereitung von Geräten für die Bereitstellung beteiligt sind.

## <a name="service-operations-endpoint"></a>Endpunkt für Dienstvorgänge

Der Endpunkt für Dienstvorgänge ist der Endpunkt zum Verwalten der Diensteinstellungen und der Registrierungsliste. Dieser Endpunkt wird nur vom Dienstadministrator verwendet. Er wird nicht von Geräten verwendet.

## <a name="device-provisioning-endpoint"></a>Endpunkt zur Gerätebereitstellung

Der Endpunkt zur Gerätebereitstellung ist der einzelne Endpunkt, den alle Geräte für die automatische Bereitstellung verwenden. Die URL ist für alle Bereitstellungsdienstinstanzen identisch, sodass bei neuen Verbindungsinformationen in Supply Chain-Szenarien nicht die Firmware von Geräten aktualisiert werden muss. Durch den ID-Bereich wird die Mandantenisolation sichergestellt.

## <a name="linked-iot-hubs"></a>Verknüpfte IoT Hubs

Der Device Provisioning-Dienst kann nur Geräte in IoT Hubs bereitstellen, die mit dem Dienst verknüpft wurden. Durch Verknüpfen eines IoT Hub mit einer Instanz des Device Provisioning-Diensts erhält der Dienst Lese-/Schreibberechtigungen für die Geräteregistrierung des IoT Hubs. Durch die Verknüpfung kann der Device Provisioning-Dienst eine Geräte-ID registrieren und die Erstkonfiguration im Gerätezwilling festlegen. Verknüpfte IoT Hubs können sich in allen Azure-Regionen befinden. Sie können Hubs in anderen Abonnements mit Ihrem Bereitstellungsdienst verknüpfen.

## <a name="allocation-policy"></a>Zuordnungsrichtlinie

Die Einstellung auf Dienstebene, mit der festgelegt wird, wie der Device Provisioning-Dienst einem IoT Hub Geräte zuweist. Es gibt drei unterstützte Zuordnungsrichtlinien:

* **Gleichmäßig gewichtete Verteilung:** Bei verknüpften IoT Hubs ist die Wahrscheinlichkeit gleich hoch, dass Geräte für sie bereitgestellt werden. Dies ist die Standardeinstellung. Wenn Sie nur für eine IoT Hub-Instanz Geräte bereitstellen, können Sie diese Einstellung beibehalten.

* **Niedrigste Latenz:** Geräte werden für einen IoT Hub mit der geringsten Latenz für das Gerät bereitgestellt. Wenn mehrere verknüpfte IoT Hubs die gleiche geringste Latenz aufweisen, verteilt der Bereitstellungsdienst die Geräte auf diese Hubs.

* **Statische Konfiguration über die Registrierungsliste:** Die Angabe des gewünschten IoT Hub in der Registrierungsliste hat gegenüber der Zuordnungsrichtlinie auf Dienstebene Vorrang.

## <a name="enrollment"></a>Registrierung

Bei einer Registrierung handelt es sich um den Datensatz der Geräte oder Gerätegruppen, die mittels automatischer Bereitstellung registriert werden können. Der Registrierungsdatensatz enthält unter anderem folgende Informationen zum Gerät bzw. zur Gerätegruppe:
- Den verwendeten [Nachweismechanismus](concepts-security.md#attestation-mechanism) des Geräts
- Die optionale anfängliche gewünschte Konfiguration
- Die gewünschte IoT Hub-Instanz
- Die gewünschte Geräte-ID

Im Device Provisioning-Dienst werden zwei Registrierungsarten unterstützt:

### <a name="enrollment-group"></a>Registrierungsgruppe

Eine Registrierungsgruppe stellt eine Gruppe von Geräten dar, die einen bestimmten Nachweismechanismus gemeinsam nutzen. Alle Geräte in der Registrierungsgruppe stellen X.509-Zertifikate bereit, die von der gleichen Stamm- oder Zwischenzertifizierungsstelle signiert wurden. Registrierungsgruppen können nur den X.509-Nachweismechanismus verwenden. Der Name und das Zertifikat der Registrierungsgruppe müssen alphanumerisch sein und dürfen nur Kleinbuchstaben enthalten. Bindestriche sind zulässig.

> [!TIP]
> Es empfiehlt sich, eine Registrierungsgruppe für eine große Anzahl von Geräten, die eine gewünschte Erstkonfiguration gemeinsam nutzen, oder für Geräte zu verwenden, die alle demselben Mandanten zugeordnet sind.

### <a name="individual-enrollment"></a>Individuelle Registrierung

Eine individuelle Registrierung ist ein Eintrag für ein einzelnes Gerät, das registriert werden kann. Individuelle Registrierungen verwenden entweder untergeordnete X.509-Zertifikate oder SAS-Token (in einem physischen oder virtuellen TPM) als Nachweismechanismen. Die Registrierungs-ID in einer einzelnen Registrierung ist alphanumerisch und besteht aus Kleinbuchstaben und ggf. Bindestrichen. Bei individuellen Registrierungen ist möglicherweise die gewünschte IoT Hub-Geräte-ID angegeben.

> [!TIP]
> Individuelle Registrierungen sollten für Geräte verwendet werden, die besondere Erstkonfigurationen erfordern, oder für Geräte, die sich nur mittels SAS-Token per TPM-Nachweis authentifizieren können.

## <a name="registration"></a>Registrierung

Eine Registrierung ist der Datensatz eines Geräts, das über den Device Provisioning-Dienst erfolgreich in einem IoT Hub registriert/bereitgestellt wurde. Registrierungsdatensätze werden automatisch erstellt. Sie können gelöscht, jedoch nicht aktualisiert werden.

## <a name="operations"></a>Vorgänge

Vorgänge stellen die Abrechnungseinheit des Device Provisioning-Diensts dar. Ein Vorgang entspricht der erfolgreichen Ausführung einer Anweisung für den Dienst. Vorgänge umfassen Registrierungen und erneute Registrierungen von Geräten sowie dienstseitige Änderungen wie das Hinzufügen und Aktualisieren von Registrierungslisteneinträgen.
