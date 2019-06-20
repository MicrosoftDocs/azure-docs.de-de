---
title: Überwachungsoptionen – Azure Dedicated HSM | Microsoft-Dokumentation
description: Übersicht über Azure Dedicated HSM-Überwachungsoptionen und die Überwachungszuständigkeiten
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2bcf1d1e007398cf80839f5e1d0bac78fd80db07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912228"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure-Dienst für dedizierte HSMs – Überwachung

Der Azure-Dienst für dedizierte HSMs stellt ein physisches Gerät für die Verwendung durch einen Kunden mit umfassender administrativer Kontrolle und Verwaltungsverantwortung bereit. Ein [Gemalto SafeNet Luna 7 HSM Modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) wird zur Verfügung gestellt.  Microsoft hat nach Bereitstellung durch den Kunden keinen administrativen Zugriff, abgesehen von der Zuordnung eines physischen seriellen Anschlusses für die Überwachungsrolle. Daher sind die Kunden verantwortlich für typische Betriebsaktivitäten einschließlich umfassender Überwachung und Protokollanalyse.
Die Kunden sind vollständig verantwortlich für Anwendungen, die die HSMs nutzen, und sollten hinsichtlich Support bzw. Assistenz auf Beraterbasis mit Gemalto kooperieren. Aufgrund des Ausmaßes, in dem der Kunde für den ordnungsgemäßen Betrieb verantwortlich ist, kann Microsoft keinerlei Garantie für die Hochverfügbarkeit dieses Diensts bieten. Es liegt in der Verantwortung des Kunden, sicherzustellen, dass seine Anwendungen ordnungsgemäß konfiguriert sind, um Hochverfügbarkeit zu erzielen. Microsoft überwacht und wartet Geräteintegrität und Netzwerkkonnektivität.

## <a name="microsoft-monitoring"></a>Überwachung durch Microsoft

Das verwendete Gemalto SafeNet-Gerät weist standardmäßig SNMP und seriellen Anschluss als Optionen für die Überwachung des Geräts auf. Microsoft hat die Verbindung über den seriellen Anschluss als physisches Mittel verwendet, um die Verbindung mit dem Gerät herzustellen, um grundlegende Telemetriedaten hinsichtlich der Integrität des Geräts abzurufen. Dazu gehören auch Temperatur- und Komponentenstatus, z.B. von Stromversorgung und Lüfter.
Um dies zu erreichen, verwendet Microsoft eine nicht administrative, auf dem Gemalto-Gerät eingerichtete „Überwachungsrolle“. Diese Rolle bietet die Möglichkeit, die Telemetriedaten abzurufen, aber keinen Zugriff auf das Gerät im Hinblick auf administrative Aufgaben oder Anzeige kryptografischer Informationen. Unsere Kunden können sicher sein, dass sie ihre Geräte wirklich selbst verwalten und für sensible kryptografische Schlüsselspeicherung verwenden. Für den Fall, dass ein Kunde nicht mit diesem minimalen Zugriff zur Überwachung der Basisintegrität zufrieden ist, hat er die Option, das Überwachungskonto zu deaktivieren. Die offensichtliche Folge davon ist, dass Microsoft keine Informationen erhält und daher keine proaktiven Benachrichtigungen zu Geräteintegritätsproblemen senden kann. In diesem Fall ist der Kunde für die Integrität des Geräts verantwortlich.
Die Überwachungsfunktion selbst wird so eingerichtet, dass alle zehn Minuten Integritätsdaten vom Gerät abgerufen werden. Aufgrund der Fehleranfälligkeit der seriellen Kommunikation wird erst dann eine Warnung ausgelöst, wenn für einen Zeitraum von einer Stunde mehrere negative Integritätsindikatoren auftreten. Diese Warnung löst eine proaktive Kundenbenachrichtigung über das Problem aus.
Je nach Art des Problems würden die entsprechenden Aktionen ausgeführt, um die Auswirkungen zu verringern und die Bereinigung mit geringem Risiko sicherzustellen. So ist beispielsweise ein Stromversorgungsausfall ein Hot-Swap-Verfahren, aus dem kein Manipulationsereignis resultiert, sodass nur geringe Auswirkungen und ein minimales Risiko für den Betrieb vorliegen. Andere Verfahren erfordern eventuell, dass ein Gerät auf null gesetzt und seine Bereitstellung aufgehoben wird, um jedes Sicherheitsrisiko für den Kunden zu minimieren. In diesem Fall würde ein Kunde ein anderes Gerät bereitstellen, erneut eine Hochverfügbarkeitskopplung durchführen und so die Gerätesynchronisierung auslösen. Der Normalbetrieb würde in kürzester Zeit wieder aufgenommen, mit minimalen Unterbrechungen und niedrigstem Sicherheitsrisiko.  

## <a name="customer-monitoring"></a>Kundenüberwachung

Ein Wertvorschlag des dedizierten HSM-Diensts ist die Kontrolle, die der Kunde über das Gerät erhält, insbesondere angesichts der Tatsache, dass es sich um ein in der Cloud bereitgestelltes Gerät handelt. Eine Folge dieser Kontrolle ist die Verantwortung zur Überwachung und Verwaltung der Integrität des Geräts. Zum Gemalto SafeNet-Gerät werden Anleitungen für die SNMP- und Syslog-Implementierung bereitgestellt. Kunden des dedizierten HSM-Diensts sollten diese auch dann verwenden, wenn das Microsoft-Überwachungskonto aktiv bleibt, und als obligatorisch betrachten, wenn sie das Microsoft-Überwachungskonto deaktivieren.
Beide verfügbaren Verfahren ermöglichen einem Kunden, Probleme zu identifizieren und sich an den Microsoft-Support zu wenden, um die entsprechenden Bereinigungsmaßnahmen zu initiieren.

## <a name="next-steps"></a>Nächste Schritte

Es wird empfohlen, sich mit allen Schlüsselkonzepten des Diensts (z.B. Hochverfügbarkeit und Sicherheit) vor der Gerätebereitstellung und dem Entwurf oder der Bereitstellung von Anwendungen vertraut zu machen. Weitere Themen auf Konzeptebene:

* [Hochverfügbarkeit](high-availability.md)
* [Physische Sicherheit](physical-security.md)
* [Netzwerk](networking.md)
* [Unterstützungsmöglichkeiten](supportability.md)
