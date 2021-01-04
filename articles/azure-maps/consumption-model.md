---
title: Fahrzeugnutzungsmodelle für das Routing | Microsoft Azure Maps
description: 'Erfahren Sie mehr über die von Azure Maps unterstützten Verbrauchsmodelle: Verbrennung und Strom. Sehen Sie, welche Parameter von den einzelnen Modellen verwendet werden, und zeigen Sie Parametereinschränkungen an.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013138"
---
# <a name="consumption-model"></a>Nutzungsmodell

Der Routenplanungsdienst bietet einen Satz von Parametern für eine ausführliche Beschreibung des fahrzeugspezifischen Verbrauchsmodells.
Abhängig vom Wert für **vehicleEngineType** werden zwei grundlegende Verbrauchsmodelle unterstützt: _Combustion_ und _Electric_. Es ist falsch, Parameter in derselben Anforderung anzugeben, die zu unterschiedlichen Modellen gehören. Verbrauchsmodellparameter können auch nicht mit den **travelMode**-Werten _bicycle_ (Fahrrad) und _pedestrian_ (Fußgänger) verwendet werden.

## <a name="parameter-constraints-for-consumption-model"></a>Parametereinschränkungen für das Verbrauchsmodell

Bei beiden Verbrauchsmodellen bestehen beim Angeben von Parametern einige Abhängigkeiten. Dies bedeutet, dass die explizite Angabe von Parametern die Angabe anderer Parameter erfordern kann. Im Folgenden finden Sie die zu berücksichtigenden Abhängigkeiten:

* Alle Parameter erfordern, dass der Benutzer **constantSpeedConsumption** angibt. Es ist ein Fehler, andere Verbrauchsmodellparameter anzugeben, wenn **constantSpeedConsumption** nicht angegeben ist. Der Parameter **vehicleweight-** ist eine Ausnahme für diese Anforderung.
* **accelerationEfficiency** und **decelerationEfficiency** müssen immer als Paar (d. h. beide oder keiner) angegeben werden.
* Wenn **accelerationEfficiency** und **decelerationEfficiency** angegeben sind, darf das Produkt ihrer Werte nicht größer als 1 sein (um eine unaufhörliche Bewegung zu verhindern).
* **uphillEfficiency** und **downhillEfficiency** müssen immer als Paar (d. h. beide oder keiner) angegeben werden.
* Wenn **uphillEfficiency** und **downhillEfficiency** angegeben sind, darf das Produkt ihrer Werte nicht größer als 1 sein (um eine unaufhörliche Bewegung zu verhindern).
* Wenn die \*__Efficiency__-Parameter vom Benutzer angegeben werden, muss auch **vehicleWeight** angegeben werden. Wenn **vehicleEngineType** den Wert _combustion_ hat, muss auch **fuelEnergyDensityInMJoulesPerLiter** angegeben werden.
* **maxChargeInkWh** und **currentChargeInkWh** müssen immer als Paar (d. h. beide oder keiner) angegeben werden.

> [!NOTE]
> Wenn nur **constantSpeedConsumption** angegeben ist, werden keine anderen Verbrauchsaspekte wie Steigungen und Fahrzeugbeschleunigung für Verbrauchsberechnungen berücksichtigt.

## <a name="combustion-consumption-model"></a>Verbrauchsmodell „Combustion“

Das Verbrauchsmodell „Combustion“ wird verwendet, wenn **vehicleEngineType** auf _combustion_ festgelegt ist.
Die Liste der Parameter, die zu diesem Modell gehören, ist unten aufgeführt. Eine detaillierte Beschreibung finden Sie im Abschnitt „Parameter“.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Verbrauchsmodell „Electric“

Das Verbrauchsmodell „Electric“ wird verwendet, wenn **vehicleEngineType** auf _electric_ festgelegt ist.
Die Liste der Parameter, die zu diesem Modell gehören, ist unten aufgeführt. Eine detaillierte Beschreibung finden Sie im Abschnitt „Parameter“.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Sinnvolle Werte für Verbrauchsparameter

Ein bestimmter Satz von Verbrauchsparametern kann abgelehnt werden, auch wenn er alle expliziten Anforderungen erfüllt. Dies geschieht, wenn der Wert eines bestimmten Parameters oder eine Kombination von Werten mehrerer Parameter wahrscheinlich zu unangemessenen Größen von Verbrauchswerten führt. In diesem Fall ist die Ursache wahrscheinlich ein Eingabefehler, da sorgfältig darauf geachtet wird, alle sinnvollen Werte für Verbrauchsparameter zu berücksichtigen. Für den Fall, dass ein bestimmter Satz von Verbrauchsparametern abgelehnt wird, enthält die zugehörige Fehlermeldung einen Erläuterungstext zu den Ursachen.
Die detaillierten Beschreibungen der Parameter enthalten Beispiele für sinnvolle Werte für beide Modelle.
