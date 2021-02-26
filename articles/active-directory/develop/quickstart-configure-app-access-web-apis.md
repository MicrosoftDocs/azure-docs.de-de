---
title: 'Schnellstart: Konfigurieren einer App für den Zugriff auf eine Web-API | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung konfigurieren Sie eine App-Registrierung, die eine Web-API auf der Microsoft Identity Platform darstellt, um den bereichsbezogenen Ressourcenzugriff (Berechtigungen) für Clientanwendungen zu ermöglichen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: bd6acc5d3b7cd6e88b56e4a922e777adca031538
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102562"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Schnellstart: Konfigurieren einer Clientanwendung für den Zugriff auf eine Web-API

In dieser Schnellstartanleitung stellen Sie eine bei der Microsoft Identity Platform registrierte Client-App mit bereichsbezogenem, berechtigungsbasiertem Zugriff auf Ihre eigene Web-API bereit. Außerdem ermöglichen Sie der Client-App den Zugriff auf Microsoft Graph.

Wenn Sie die Bereiche einer Web-API in der Registrierung Ihrer Client-App angeben, kann die Client-App ein Zugriffstoken mit diesen Bereichen von der Microsoft Identity Platform abrufen. Innerhalb des Codes kann die Web-API dann berechtigungsbasierten Zugriff auf die zugehörigen Ressourcen basierend auf den im Zugriffstoken enthaltenen Bereichen bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abschluss von [Schnellstart: Registrieren einer Anwendung](quickstart-register-app.md)
* Abschluss von [Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen einer Web-API](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Hinzufügen von Berechtigungen für den Zugriff auf Ihre Web-API

Im ersten Szenario gewähren Sie einer Client-App Zugriff auf Ihre eigene Web-API. Sie müssen sowohl die Client-App als auch die Web-API als Teil der Voraussetzungen registriert haben. Wenn Sie noch nicht sowohl eine Client-App als auch eine Web-API registriert haben, führen Sie die unter [Voraussetzungen](#prerequisites) beschriebenen Schritte aus.

Im folgenden Diagramm ist dargestellt, wie die beiden App-Registrierungen zueinander in Beziehung stehen. In diesem Abschnitt fügen Sie der Registrierung der Client-App Berechtigungen hinzu.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Liniendiagramm mit einer Web-API mit verfügbar gemachten Bereichen auf der rechten Seite und einer Client-App auf der linken Seite mit diesen als Berechtigungen ausgewählten Bereichen" border="false":::

Nachdem Sie sowohl die Client-App als auch die Web-API registriert und die API durch das Erstellen von Bereichen verfügbar gemacht haben, können Sie die Berechtigungen der Client-App für die API konfigurieren, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im oberen Menü den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, der die Registrierung der Client-App enthält.
1. Wählen Sie **Azure Active Directory** > **App-Registrierungen** aus, und wählen Sie dann Ihre Client Anwendung aus (*nicht* Ihre Web-API).
1. Wählen Sie über die Option **API-Berechtigungen** > **Berechtigung hinzufügen** > **Meine APIs** aus.
1. Wählen Sie die Web-API aus, die Sie als Teil der Voraussetzungen registriert haben.

    Die Option **Delegierte Berechtigungen** ist standardmäßig ausgewählt. Delegierte Berechtigungen sind für Client-Apps geeignet, die als angemeldeter Benutzer auf eine Web-API zugreifen und deren Zugriff auf die Berechtigungen beschränkt werden sollte, die Sie im nächsten Schritt auswählen. Übernehmen Sie für dieses Beispiel die ausgewählte Option **Delegierte Berechtigungen**.

    **Anwendungsberechtigungen** sind für Anwendungen vom Typ „Dienst“ oder „Daemon“ vorgesehen, die als solche auf eine Web-API zugreifen müssen, ohne dass eine Benutzerinteraktion für die Anmeldung oder Zustimmung erforderlich ist. Sofern Sie keine Anwendungsrollen für Ihre Web-API definiert haben, ist diese Option deaktiviert.
1. Erweitern Sie unter **Berechtigungen auswählen** die Ressource, deren Bereiche Sie für Ihre Web-API definiert haben, und wählen Sie die Berechtigungen aus, über die die Client-App im Namen des angemeldeten Benutzers verfügen soll.

    Wenn Sie die im vorherigen Schnellstart angegebenen Beispielbereichsnamen verwendet haben, sollten **Employees.Read.All** und **Employees.Write.All** angezeigt werden.
    Wählen Sie **Employees.Read.All** oder eine andere Berechtigung aus, die Sie ggf. beim Ausführen der Schritte für die Voraussetzungen erstellt haben.
1. Wählen Sie **Berechtigungen hinzufügen** aus, um den Vorgang abzuschließen.

Nachdem Sie Ihrer API Berechtigungen hinzugefügt haben, sollten die ausgewählten Berechtigungen unter **Konfigurierte Berechtigungen** angezeigt werden. In der folgenden Abbildung sehen Sie beispielhaft, dass die delegierte Berechtigung *Employees.Read.All* zur Registrierung der Client-App hinzugefügt wurde.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Der Bereich „Konfigurierte Berechtigungen“ im Azure-Portal mit der neu hinzugefügten Berechtigung":::

Möglicherweise sehen Sie auch die Berechtigung *User.Read* für die Microsoft Graph-API. Diese Berechtigung wird automatisch hinzugefügt, wenn Sie eine App im Azure-Portal registrieren.

## <a name="add-permissions-to-access-microsoft-graph"></a>Hinzufügen von Berechtigungen für den Zugriff auf Microsoft Graph

Zusätzlich zum Zugriff auf Ihre eigene Web-API im Namen des angemeldeten Benutzers muss Ihre Anwendung möglicherweise auch auf die in Microsoft Graph gespeicherten Daten des Benutzers (oder andere Daten) zugreifen oder diese ändern. Möglicherweise verfügen Sie auch über eine Dienst- oder Daemon-App, die als solche auf Microsoft Graph zugreifen muss und Vorgänge ohne Benutzerinteraktion ausführt.

### <a name="delegated-permission-to-microsoft-graph"></a>Delegierte Berechtigung für Microsoft Graph

Konfigurieren Sie die delegierte Berechtigung für Microsoft Graph, damit Ihre Clientanwendung Vorgänge im Namen des angemeldeten Benutzers ausführen kann, z. B. das Lesen seiner E-Mail oder das Ändern seines Profils. Standardmäßig werden die Benutzer Ihrer Client-App bei der Anmeldung aufgefordert, den delegierten Berechtigungen zuzustimmen, die Sie für die App konfiguriert haben.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im oberen Menü den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, der die Registrierung der Client-App enthält.
1. Wählen Sie **Azure Active Directory** > **App-Registrierungen** aus, und wählen Sie dann Ihre Client Anwendung aus.
1. Wählen Sie **API-Berechtigungen** > **Berechtigung hinzufügen** > **Microsoft Graph** aus.
1. Wählen Sie **Delegierte Berechtigungen** aus. Microsoft Graph macht viele Berechtigungen verfügbar, wobei die am häufigsten verwendeten Berechtigungen am Anfang der Liste angezeigt werden.
1. Wählen Sie unter **Berechtigungen auswählen** die folgenden Berechtigungen aus:

    | Berechtigung       | BESCHREIBUNG                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Anzeigen der E-Mail-Adresse des Benutzers                           |
    | `offline_access` | Zugriff auf Daten beibehalten, für die Sie der App Zugriff erteilt haben |
    | `openid`         | Anmelden von Benutzern                                       |
    | `profile`        | Anzeigen des grundlegenden Benutzerprofils                           |
1. Wählen Sie **Berechtigungen hinzufügen** aus, um den Vorgang abzuschließen.

Wenn Sie Berechtigungen konfigurieren, werden die Benutzer Ihrer App bei der Anmeldung aufgefordert, ihre Zustimmung zu erteilen, damit Ihre App in ihrem Namen auf die Ressourcen-API zugreifen kann.

Als Administrator können Sie auch die Zustimmung im Namen *aller* Benutzer erteilen, damit diese nicht dazu aufgefordert werden. Die Administratoreinwilligung wird weiter unten im Abschnitt [Weitere Informationen zu API-Berechtigungen und zur Administratoreinwilligung](#more-on-api-permissions-and-admin-consent) dieses Artikels erläutert.

### <a name="application-permission-to-microsoft-graph"></a>Anwendungsberechtigung für Microsoft Graph

Konfigurieren Sie Anwendungsberechtigungen für eine Anwendung, die sich ohne Benutzerinteraktion oder Zustimmung als sie selbst authentifizieren muss. Anwendungsberechtigungen werden in der Regel von Hintergrunddiensten oder Daemon-Apps verwendet, die „monitorlos“ auf eine API zugreifen, und von Web-APIs, die auf eine andere (nachgeschaltete) API zugreifen.

In den folgenden Schritten erteilen Sie die Berechtigung für die Microsoft Graph-Berechtigung *Files.Read.All* als Beispiel.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im oberen Menü den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, der die Registrierung der Client-App enthält.
1. Wählen Sie **Azure Active Directory** > **App-Registrierungen** aus, und wählen Sie dann Ihre Client Anwendung aus.
1. Wählen Sie **API-Berechtigungen** > **Berechtigung hinzufügen** > **Microsoft Graph** > **Anwendungsberechtigungen** aus.
1. Alle von Microsoft Graph verfügbar gemachten Berechtigungen werden unter **Berechtigungen auswählen** angezeigt.
1. Wählen Sie die Berechtigung(en) aus, die Sie Ihrer Anwendung erteilen möchten. Beispielsweise verfügen Sie möglicherweise über eine Daemon-App, die Dateien in Ihrer Organisation überprüft und bei bestimmten Dateitypen oder -namen eine Warnung ausgibt.

    Erweitern Sie unter **Berechtigungen auswählen** die Option **Dateien**, und wählen Sie dann die Berechtigung *Files.Read.All* aus.
1. Wählen Sie **Berechtigungen hinzufügen** aus.

Einige Berechtigungen (wie z. B. die Microsoft Graph-Berechtigung *Files.Read.All*) erfordern die Zustimmung des Administrators. Sie erteilen die Administratoreinwilligung, indem Sie die Schaltfläche **Administratoreinwilligung erteilen** auswählen, die weiter unten im Abschnitt [Schaltfläche „Administratorzustimmung“](#admin-consent-button) erläutert wird.

### <a name="configure-client-credentials"></a>Konfigurieren von Clientanmeldeinformationen

Apps, die Anwendungsberechtigungen verwenden, authentifizieren sich mit ihren eigenen Anmeldeinformationen als sie selbst, ohne dass eine Benutzerinteraktion erforderlich ist. Bevor Ihre Anwendung (oder API) auf Microsoft Graph, Ihre eigene Web-API oder eine beliebige andere API mithilfe von Anwendungsberechtigungen zugreifen kann, müssen Sie die Anmeldeinformationen dieser Client-App konfigurieren.

Weitere Informationen zum Konfigurieren der Anmeldeinformationen einer App finden Sie im Abschnitt [Hinzufügen von Anmeldeinformationen](quickstart-register-app.md#add-credentials) des [Schnellstarts: Registrieren einer Anwendung bei der Microsoft Identity Platform](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Weitere Informationen zu API-Berechtigungen und zur Administratoreinwilligung

Der Bereich **API-Berechtigungen** einer App-Registrierung enthält eine Tabelle [Konfigurierte Berechtigungen](#configured-permissions) und möglicherweise auch eine Tabelle [Weitere gewährte Berechtigungen](#other-permissions-granted). Beide Tabellen sowie die [Schaltfläche „Administratorzustimmung“](#admin-consent-button) werden in den folgenden Abschnitten beschrieben.

### <a name="configured-permissions"></a>Konfigurierte Berechtigungen

Die Tabelle **Konfigurierte Berechtigungen** im Bereich **API-Berechtigungen** enthält die Liste der Berechtigungen, die Ihre Anwendung zum Ausführen grundlegender Vorgänge benötigt: die Liste *erforderlicher Ressourcenzugriff* (Required Resource Access, RRA). Benutzer oder ihre Administratoren müssen diesen Berechtigungen zustimmen, bevor sie Ihre App verwenden können. Andere optionale Berechtigungen können später zur Laufzeit (mit dynamischer Zustimmung) angefordert werden.

Dies ist die Liste der Mindestberechtigungen, denen die Benutzer für Ihre App zustimmen müssen. Es gibt möglicherweise weitere Berechtigungen, aber diese hier werden immer benötigt. Aus Sicherheitsgründen und um die Verwendung Ihrer App für Benutzer und Administratoren angenehmer zu gestalten, sollten Sie nie etwas anfordern, das Sie nicht benötigen.

Sie können die in dieser Tabelle angezeigten Berechtigungen hinzufügen oder entfernen, indem Sie die oben beschriebenen Schritte ausführen oder aus [Weitere gewährte Berechtigungen](#other-permissions-granted) (im nächsten Abschnitt beschrieben). Als Administrator können Sie die Administratorzustimmung für den vollständigen Satz der in der Tabelle aufgeführten Berechtigungen einer API erteilen und die Zustimmung für einzelne Berechtigungen widerrufen.

### <a name="other-permissions-granted"></a>Weitere gewährte Berechtigungen

Möglicherweise wird im Bereich **API-Berechtigungen** auch eine Tabelle mit dem Titel **Weitere gewährte Berechtigungen für {Mandant}** angezeigt. In der Tabelle **Weitere gewährte Berechtigungen für {Mandant}** werden Berechtigungen angezeigt, die dem Mandanten erteilt, aber nicht explizit für das Anwendungsobjekt konfiguriert wurden. Diese Berechtigungen wurden dynamisch angefordert und gewährt. Dieser Abschnitt wird nur angezeigt, wenn mindestens eine entsprechende Berechtigung vorhanden ist.

Sie können den vollständigen Satz der Berechtigungen einer API oder einzelne Berechtigungen, die in dieser Tabelle enthalten sind, zur Tabelle **Konfigurierte Berechtigungen** hinzufügen. Als Administrator können Sie in diesem Abschnitt die Administratoreinwilligung für APIs oder einzelne Berechtigungen widerrufen.

### <a name="admin-consent-button"></a>Schaltfläche „Administratorzustimmung“

Mithilfe der Schaltfläche **Administratoreinwilligung erteilen für {Mandant}** kann ein Administrator eine Administratoreinwilligung für die Berechtigungen erteilen, die für die Anwendung konfiguriert sind. Wenn Sie die Schaltfläche auswählen, wird ein Dialogfeld angezeigt, in dem Sie die Einwilligungsaktion bestätigen müssen.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Hervorgehobene Schaltfläche „Administratoreinwilligung erteilen“ im Bereich „Konfigurierte Berechtigungen“ im Azure-Portal":::

Nachdem Sie die Einwilligung erteilt haben, werden die Berechtigungen, für die eine Administratoreinwilligung erforderlich ist, als erteilt angezeigt:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Tabelle „Konfigurierte Berechtigungen“ im Azure-Portal mit erteilter Administratoreinwilligung für die Berechtigung „Files.Read.All“":::

Die Schaltfläche **Administratoreinwilligung erteilen** ist *deaktiviert*, wenn Sie kein Administrator sind oder für die Anwendung keine Berechtigungen konfiguriert wurden. Wenn Sie über Berechtigungen verfügen, die erteilt, aber noch nicht konfiguriert wurden, werden Sie nach dem Auswählen der Schaltfläche für die Administratoreinwilligung aufgefordert, zu entscheiden, wie mit diesen Berechtigungen verfahren werden soll. Sie können diese Berechtigungen den konfigurierten Berechtigungen hinzufügen oder entfernen.

## <a name="next-steps"></a>Nächste Schritte

In der nächsten Schnellstartanleitung der Reihe erfahren Sie, wie Sie konfigurieren, welche Kontotypen auf Ihre Anwendung zugreifen können. Beispielsweise möchten Sie den Zugriff auf die Benutzer in Ihrer Organisation (ein Mandant) beschränken oder Benutzer in anderen Azure AD-Mandanten (mehrere Mandanten) und diejenigen mit persönlichen Microsoft-Konten (MSA) zulassen.

> [!div class="nextstepaction"]
> [Ändern der von einer Anwendung unterstützten Konten](quickstart-modify-supported-accounts.md)