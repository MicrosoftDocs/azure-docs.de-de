---
title: Übersicht über das Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie durch das Azure-Portal navigieren und es zur Verwaltung von Diensten einsetzen können.
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: de04b461d8e2d2dce9a88052a86fbe5d163fafec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244198"
---
# <a name="azure-portal-overview"></a>Azure-Portal – Übersicht

In diesem Artikel zur Vorstellung des Azure-Portals werden Sie mit den Elementen auf der Portalseite und seiner Verwaltungsumgebung vertraut gemacht.

## <a name="what-is-the-azure-portal"></a>Was ist das Azure-Portal?

Das Azure-Portal ist eine webbasierte, zentrale Konsole, die eine Alternative zu Befehlszeilentools darstellt. Im Azure-Portal können Sie Ihr Azure-Abonnement auf einer grafischen Benutzeroberfläche verwalten. Sie können alles von einfachen Webanwendungen bis hin zu komplexen Cloudbereitstellungen erstellen, verwalten und überwachen, benutzerdefinierte Dashboards für eine organisierte Ressourcenübersicht erstellen und Barrierefreiheitsoptionen für ein optimales Erlebnis konfigurieren.

Das Azure-Portal ist auf Resilienz und fortlaufende Verfügbarkeit ausgelegt. Es ist in jedem Azure-Rechenzentrum vorhanden, wodurch es hinsichtlich der Ausfälle einzelner Rechenzentren resilient ist und zudem Netzwerkverzögerungen vermeidet, da es sich in der Nähe der Benutzer befindet. Das Azure-Portal wird ständig aktualisiert und erfordert bei Wartungsarbeiten keine Downtime.

## <a name="azure-home"></a>Azure-Startseite

Als neuer Abonnent von Azure-Diensten sehen Sie nach der [Anmeldung beim Portal](https://portal.azure.com) als Erstes die **Azure-Startseite**. Auf dieser Seite finden Sie Ressourcen, die Ihnen helfen, das Beste aus Ihrem Azure-Abonnement herauszuholen. Sie finden hier Links zu kostenlosen Onlinekursen, Dokumentation, den wichtigsten Diensten und nützlichen Websites, um auf dem Laufenden zu bleiben und Veränderungen für Ihr Unternehmen erfolgreich umzusetzen. Für einen schnellen und einfachen Zugriff auf die aktuellen Aufgaben zeigen wir Ihnen auch eine Liste Ihrer zuletzt verwendeten Ressourcen. Sie können diese Seite nicht anpassen, aber Sie können wählen, ob Sie die **Azure-Startseite** oder das **Azure-Dashboard** als Standardansicht sehen möchten. Bei der ersten Anmeldung wird oben auf der Seite eine Eingabeaufforderung zum Speichern Ihrer Einstellung angezeigt.

![Screenshot mit Selektor für die Standardansicht](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Azure-Dashboard

Dashboards bieten einen fokussierten Überblick über die Ressourcen in Ihrem Abonnement, die für Sie am wichtigsten sind. Wir haben Ihnen ein Standarddashboard zur Verfügung gestellt, um Ihnen den Einstieg zu erleichtern. Sie können dieses Dashboard so anpassen, dass die von Ihnen am meisten genutzten Ressourcen in einer zentralen Ansicht dargestellt werden. Alle Änderungen, die Sie an der Standardansicht vornehmen, wirken sich nur auf Ihre Arbeitsumgebung aus. Sie können jedoch zusätzliche Dashboards für den eigenen Gebrauch erstellen oder Ihre benutzerdefinierten Dashboards veröffentlichen und diese für andere Benutzer in Ihrer Organisation freigeben. Weitere Informationen finden Sie unter [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Navigation im Portal

Es ist hilfreich, das grundlegende Layout des Portals zu kennen und die Interaktion mit ihm zu verstehen. An dieser Stelle stellen wir Ihnen die Komponenten der Benutzeroberfläche und einige der Begriffe in unseren Anweisungen vor. Eine ausführlichere Tour durch das Portal finden Sie in der Lerneinheit [Navigation im Portal](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal) des Kurses.

Randleiste und Kopfzeile sind stets vorhandene globale Elemente des Azure-Portals. Diese unveränderlichen Elemente sind die „Shell“ für die Benutzeroberfläche, die mit allen Diensten und Funktionen verknüpft ist, und die Kopfzeile bietet Zugriff auf globale Steuerelemente. Die Konfigurationsseite (auch „Blatt“ genannt) für eine Ressource kann auch einen linken Bereich aufweisen, der Ihnen hilft, zwischen Funktionen zu wechseln.

In der nachstehenden Abbildung sind die Grundelemente des Azure-Portals gekennzeichnet, die in der folgenden Tabelle beschrieben werden.

![Screenshot der Vollbildansicht des Portals und wichtiger Elemente der Benutzeroberfläche](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Schlüssel|BESCHREIBUNG
|:---:|---|
|1|Kopfzeile. Wird oben auf jeder Portalseite angezeigt und enthält globale Elemente.|
|2| Globale Suche. Über die Suchleiste können Sie schnell eine bestimmte Ressource, einen Dienst oder Dokumentation finden.|
|3|Globale Steuerelemente. Werden ebenso wie alle globalen Elemente stets im Portal angezeigt und umfassen: Cloud Shell, Abonnementfilter, Benachrichtigungen, Portaleinstellungen, Hilfe und Support und Feedbackoption.|
|4|Ihr Konto. Hier können Sie Informationen zu Ihrem Konto einsehen, Verzeichnisse wechseln, sich abmelden oder mit einem anderen Konto anmelden.|
|5|Randleiste. Die Randleiste ist ein globales Element zur Navigation zwischen Diensten. Sie kann ausgeblendet werden, um für den Arbeitsbereich mehr Platz zu schaffen.|
|6|Hauptsteuerelement zum Erstellen einer neuen Ressource im aktuellen Abonnement. Sie können in Azure Marketplace den zu erstellenden Ressourcentyp suchen und auswählen.|
|7|Die Liste Ihrer Favoriten. Auf der Seite **Alle Dienste** können Sie Favoriten hinzufügen oder entfernen.|
|8|Linker Bereich. Viele Dienste weisen im linken Bereich ein Menü auf, das Ihnen bei der Verwaltung des Diensts hilft.|
|9|Befehlsleiste. Der Kontext der Steuerelemente auf der Befehlsleiste ist abhängig von Ihrem aktuellen Fokus.|
|10|Breadcrumb. Über die Breadcrumb-Links kehren Sie in Ihrem Workflow eine Ebene zurück.|
|11|Arbeitsbereich.  Zeigt die Details der Ressource, die gegenwärtig den Fokus hat.|

## <a name="get-started-with-services"></a>Erste Schritte mit Diensten

Wenn Sie ein neuer Abonnent sind, müssen Sie erst eine Ressource erstellen, ehe es etwas zu verwalten gibt. Klicken Sie auf **+Ressource erstellen**, um die in Azure Marketplace verfügbaren Dienste anzuzeigen. Hier finden Sie Anwendungen und Dienste von Hunderten von Anbietern, die alle für die Ausführung in Azure zertifiziert sind.

Wir haben Ihre Favoriten auf der Randleiste vorab mit Links zu häufig genutzten Diensten versehen.  Klicken Sie zum Anzeigen aller verfügbaren Dienste auf der Randleiste auf **Alle Dienste**.

> [!TIP]
> Der schnellste Weg, eine Ressource, einen Dienst oder Dokumentation zu finden, ist die *Suche* in der globalen Kopfzeile. Verwenden Sie die Breadcrumb-Links, um zu vorherigen Seiten zurückzukehren.
>

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie unter [Unterstützte Browser und Geräte](../azure-portal/azure-portal-supported-browsers-devices.md), wo Sie das Azure-Portal aufrufen können.

* Bleiben Sie mit der [mobilen Azure-App](https://azure.microsoft.com/features/azure-portal/mobile-app/) unterwegs auf dem Laufenden.
