---
title: Fehler „Zugriff auf diese Unternehmensanwendung nicht möglich“ bei Verwendung der App-Proxy-App
description: Informationen zum Beheben häufiger Probleme mit Azure AD-Anwendungsproxyanwendungen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0339e9f7688555b4d99c2d3255461b5675f642ff
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649550"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Fehler „Zugriff auf diese Unternehmensanwendung nicht möglich“ bei Verwendung der Anwendungsproxyanwendung

In diesem Artikel erhalten Sie Unterstützung bei der Problembehandlung von häufigen Fehlern, wenn der Fehler „Zugriff auf diese Unternehmens-App nicht möglich“ in einer Azure AD-Anwendungsproxyanwendung auftritt.

## <a name="overview"></a>Übersicht

Wenn dieser Fehler angezeigt wird, suchen Sie den Statuscode auf der Seite mit dem Fehler. Es handelt sich bei dem Code wahrscheinlich um einen der folgenden Statuscodes:

- **Gatewaytimeout:** Der Anwendungsproxydienst kann den Connector nicht erreichen. Dieser Fehler deutet in der Regel auf ein Problem mit der Connectorzuweisung, dem Connector selbst oder den Netzwerkregeln für den Connector hin.
- **Ungültiges Gateway:** Der Connector kann die Back-End-Anwendung nicht erreichen. Dieser Fehler kann auf eine Fehlkonfiguration der Anwendung hinweisen.
- **Verboten:** Der Benutzer ist nicht autorisiert, auf die Anwendung zuzugreifen. Dieser Fehler kann vorkommen, wenn der Benutzer der Anwendung in Azure Active Directory nicht zugewiesen ist oder wenn der Benutzer auf dem Back-End nicht über die Berechtigung zum Zugriff auf die Anwendung verfügt.

Den Code finden Sie im Text der Fehlermeldung im Feld „Statuscode“ unten links. Überprüfen Sie auch, ob auf der Seite im unteren Bereich zusätzliche Tipps vorhanden sind.

![Beispiel: Fehler „Gatewaytimeout“](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Informationen zur Behandlung der grundlegenden Ursache für diesen Fehler und weitere Informationen zu den vorgeschlagenen Fehlerbehebungen finden Sie im entsprechenden Abschnitt weiter unten.

## <a name="gateway-timeout-errors"></a>Fehler „Gatewaytimeout“

Ein Gatewaytimeout tritt auf, wenn der Dienst versucht, den Connector zu erreichen, und dies nicht innerhalb des Timeoutfensters möglich ist. Dieser Fehler wird normalerweise durch eine Anwendung verursacht, die einer Connectorgruppe ohne funktionierende Connectors zugewiesen ist, oder wenn einige vom Connector benötigte Ports geschlossen sind.

## <a name="bad-gateway-errors"></a>Fehler „Ungültiges Gateway“

Der Fehler „Ungültiges Gateway“ gibt an, dass der Connector die Back-End-Anwendung nicht erreichen kann. Stellen Sie sicher, dass Sie die richtige Anwendung veröffentlicht haben. Häufige Ursachen dieses Fehlers sind Folgende:

- Ein Tippfehler oder ein Fehler in der internen URL
- Ein nicht veröffentlichtes Stammverzeichnis der Anwendung. Beispiel: `http://expenses/reimbursement` wird veröffentlicht, jedoch wird versucht, auf `http://expenses` zuzugreifen.
- Probleme mit der Konfiguration der eingeschränkten Kerberos-Delegierung (KCD)
- Probleme mit der Back-End-Anwendung

## <a name="forbidden-errors"></a>Fehler „Verboten“

Wenn der Fehler „Verboten“ angezeigt wird, wurde der Benutzer der Anwendung nicht zugewiesen. Dieser Fehler kann entweder Azure Active Directory oder die Back-End-Anwendung betreffen.

Informationen über das Zuweisen von Benutzern zur Anwendung in Azure finden Sie in der [Konfigurationsdokumentation](application-proxy-add-on-premises-application.md#test-the-application).

Wenn Sie bestätigen, dass der Benutzer der Anwendung in Azure zugewiesen ist, überprüfen Sie die Benutzerkonfiguration in der Back-End-Anwendung. Wenn Sie die eingeschränkte Kerberos-Delegierung bzw. integrierte Windows-Authentifizierung verwenden, finden Sie nützliche Informationen auf der Seite zur KCD-Problembehandlung.

## <a name="check-the-applications-internal-url"></a>Überprüfen der internen URL der Anwendung

Als ersten schnellen Schritt können Sie die interne URL überprüfen und korrigieren, indem Sie die Anwendung über **Unternehmensanwendungen** öffnen und dann das Menü **Anwendungsproxy** auswählen. Stellen Sie sicher, dass die interne URL aus Ihrem lokalen Netzwerk für den Zugriff auf die Anwendung verwendet wird.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Überprüfen, ob die Anwendung einer funktionierenden Connectorgruppe zugewiesen wurde

So überprüfen Sie, ob die Anwendung einer funktionierenden Connectorgruppe zugewiesen wurde

1. Öffnen Sie die Anwendung im Verwaltungsportal, indem Sie zu **Azure Active Directory** navigieren und dann auf **Unternehmensanwendungen** und **Alle Anwendungen** klicken. Öffnen Sie die Anwendung, und wählen Sie dann im linken Menü **Anwendungsproxy** aus.
1. Sehen Sie sich das Feld „Connectorgruppe“ an. Wenn keine aktiven Connectors in der Gruppe vorhanden sind, wird eine Warnung angezeigt. Wenn keine Warnungen angezeigt werden, setzen Sie den Vorgang fort und überprüfen, ob alle [erforderlichen Ports](application-proxy-add-on-premises-application.md) zulässig sind.
1. Wird die falsche Connectorgruppe angezeigt, wählen Sie die richtige Gruppe über die Dropdownliste aus, und bestätigen Sie, dass keine Warnungen mehr angezeigt werden. Wird die gewünschte Connectorgruppe angezeigt, klicken Sie auf die Warnung und öffnen die Seite zur Connectorverwaltung.
1. Hier stehen Ihnen mehrere Möglichkeiten zur weiteren Problembehebung zur Verfügung:

   - Verschieben Sie einen aktiven Connector in die Gruppe: Wenn Sie über einen aktiven Connector verfügen, der dieser Gruppe angehören sollte und uneingeschränkten Zugriff auf die Back-End-Anwendung hat, können Sie den Connector in die zugewiesene Gruppe verschieben. Klicken Sie hierzu auf den Connector. Verwenden Sie im Feld „Connectorgruppe“ die Dropdownliste, um die richtige Gruppe auszuwählen, und klicken Sie dann auf „Speichern“.
   - Herunterladen eines neuen Connectors für diese Gruppe: Auf dieser Seite finden Sie den Link zum [Herunterladen eines neuen Connectors](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installieren Sie den Connector auf einem Computer mit direktem Zugriff auf die Back-End-Anwendung. In der Regel ist der Connector auf dem selben Server wie die Anwendung installiert. Verwenden Sie den Downloadlink, um einen Connector auf den Zielcomputer herunterzuladen. Als Nächstes klicken Sie auf den Connector und verwenden die Dropdownliste „Connectorgruppe“, um sicherzustellen, dass er der richtigen Gruppe angehört.
   - Untersuchen eines inaktiven Connectors: Wenn ein Connector als inaktiv angezeigt wird, kann er den Dienst nicht erreichen. Dieser Fehler liegt in der Regel darin begründet, dass einige erforderliche Ports blockiert werden. Um das Problem zu beheben, setzen Sie den Vorgang fort und überprüfen, ob alle erforderlichen Ports zulässig sind.

Testen Sie nach dem Ausführen der Schritte die Anwendung erneut, um sicherzustellen, dass die Anwendung einer Gruppe funktionierender Connectors zugewiesen ist. Wenn der Fehler immer noch auftritt, fahren Sie mit dem nächsten Abschnitt fort.

## <a name="check-all-required-ports-are-open"></a>Überprüfen, ob alle erforderlichen Ports geöffnet sind

Überprüfen, ob alle erforderlichen Ports geöffnet sind Erforderliche Ports finden Sie im Abschnitt „Öffnen von Ports“ in [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md). Wenn alle erforderlichen Ports geöffnet sind, fahren Sie mit dem nächsten Abschnitt fort.

## <a name="check-for-other-connector-errors"></a>Überprüfen auf andere Connectorfehler

Wenn keine der oben genannten Schritte das Problem beheben, sollten Sie im nächsten Schritt nach Problemen oder Fehlern mit dem Connector selbst suchen. Einige häufig auftretende Fehler sind im [Problembehandlungsdokument](./application-proxy-troubleshoot.md#connector-errors) aufgeführt.

Sie können auch direkt auf die Connectorprotokolle zugreifen, um Fehler zu ermitteln. Eine Vielzahl der Fehlermeldungen enthält genauere Empfehlungen für Fehlerbehebungen. Informationen zum Anzeigen der Protokolle finden Sie in der [Dokumentation zu Connectors](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Weitere Lösungen

Wenn die oben genannten Schritte das Problem nicht beheben konnten, können einige andere Ursachen vorliegen. So identifizieren Sie das Problem

Wenn Ihre Anwendung für die integrierte Windows-Authentifizierung (IWA) konfiguriert ist, testen Sie die Anwendung ohne einmaliges Anmelden. Wenn dies nicht der Fall ist, lesen Sie im nächsten Absatz weiter. Öffnen Sie die Anwendung zum Überprüfen ohne einmaliges Anmelden über **Unternehmensanwendungen**, und wechseln Sie zum Menü **Einmaliges Anmelden**. Ändern Sie die Auswahl in der Dropdownliste von „Integrierte Windows-Authentifizierung“ in „Azure AD-SSO deaktiviert“.

Öffnen Sie einen Browser, und versuchen Sie erneut, auf die Anwendung zuzugreifen. Es sollte eine Authentifizierungsaufforderung angezeigt und anschließend die Anwendung geöffnet werden. Wenn eine Authentifizierung möglich ist, wird das Problem durch die Konfiguration der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) verursacht, die das einmalige Anmelden ermöglicht. Weitere Informationen finden Sie auf der Seite zur Problembehandlung bei der KCD.

Wenn die Fehlermeldung weiterhin angezeigt wird, wechseln Sie zum Computer, auf dem der Connector installiert ist, öffnen einen Browser und versuchen, die interne URL der Anwendung aufzurufen. Der Connector fungiert wie ein weiterer Client vom gleichen Computer. Wenn Sie die Anwendung nicht erreichen können, überprüfen Sie, warum dieser Computer die Anwendung nicht erreichen kann, oder verwenden Sie einen Connector auf einem Server, der auf die Anwendung zugreifen kann.

Wenn die Anwendung von diesem Computer aus erreicht werden kann, sollten Sie nach Problemen und Fehlern des Connectors selbst suchen. Einige häufig auftretende Fehler sind im [Problembehandlungsdokument](application-proxy-troubleshoot.md#connector-errors) aufgeführt. Sie können auch direkt auf die Connectorprotokolle zugreifen, um Fehler zu ermitteln. Anhand vieler Fehlermeldungen können Sie genauere Empfehlungen für Fehlerbehebungen erhalten. Informationen zum Verständnis der Protokolle finden Sie in der [Connectordokumentation](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Nächste Schritte

[Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)