---
title: Verwaltung der Benutzerbereitstellung für Unternehmens-Apps in Azure AD
description: Erfahren Sie, wie Sie mithilfe von Azure Active Directory die Benutzerkontobereitstellung für Unternehmens-Apps verwalten.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 02/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 02d415bd957b0490857081b996c592f90365f031
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555635"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal

Dieser Artikel enthält eine Beschreibung der allgemeinen Schritte zum Verwalten der automatischen Bereitstellung und zum Aufheben der Bereitstellung von Benutzerkonten für Anwendungen, die dies unterstützen. Die *Bereitstellung von Benutzerkonten* umfasst das Erstellen, Aktualisieren und/oder Deaktivieren der Benutzerkontodatensätze im lokalen Benutzerprofilspeicher einer Anwendung. Die meisten Cloud- und SaaS-Anwendungen speichern Benutzerrolle und -berechtigungen im eigenen lokalen Profilspeicher des jeweiligen Benutzers. Das Vorkommen eines solchen Benutzerdatensatzes im lokalen Speicher ist *erforderlich*, damit das einmalige Anmelden und der Zugriff funktionieren. Weitere Informationen zur automatischen Bereitstellung von Benutzerkonten finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen, die für die automatische Bereitstellung mit Azure AD aktiviert sind. Suchen Sie zunächst in der [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](../saas-apps/tutorial-list.md) nach dem für Ihre Anwendung spezifischen Tutorial zur Bereitstellungseinrichtung. Wahrscheinlich finden Sie eine Schritt-für-Schritt-Anleitung zum Konfigurieren der App und von Azure AD für das Erstellen der Bereitstellungsverbindung.

## <a name="finding-your-apps-in-the-portal"></a>Suchen Ihrer Apps im Portal

Im Azure Active Directory-Portal können Sie alle Anwendungen in einem Verzeichnis anzeigen und verwalten, die für einmaliges Anmelden konfiguriert sind. Unternehmens-Apps sind Apps, die innerhalb Ihrer Organisation bereitgestellt und verwendet werden. Führen Sie zum Anzeigen und Verwalten Ihrer Unternehmens-Apps diese Schritte aus:

1. Öffnen Sie das [Azure Active Directory-Portal](https://aad.portal.azure.com).
1. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Eine Liste mit allen konfigurierten Apps wird angezeigt, einschließlich Apps, die aus dem Katalog hinzugefügt wurden.
1. Wählen Sie eine beliebige App aus, um ihren Ressourcenbereich zu laden, in dem Sie Berichte anzeigen und App-Einstellungen verwalten können.
1. Wählen Sie **Bereitstellung** aus, um die Einstellungen für die Bereitstellung von Benutzerkonten für die ausgewählte App zu verwalten.

   ![Bildschirm „Bereitstellung“ zum Verwalten der Einstellungen für die Bereitstellung von Benutzerkonten](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Bereitstellungsmodi

Der Bereich **Bereitstellung** beginnt mit dem Menü **Modus**, das die unterstützten Bereitstellungsmodi eine Unternehmensanwendung zeigt und deren Konfiguration ermöglicht. Die verfügbaren Optionen umfassen:

* **Automatisch**: Diese Option wird angezeigt, wenn Azure AD die automatische API-basierte Bereitstellung bzw. das Aufheben der Bereitstellung von Benutzerkonten für diese Anwendung unterstützt. Wählen Sie den Modus zum Anzeigen einer Oberfläche, auf der Administratoren folgende Aufgaben ausführen können:

  * Konfigurieren von Azure AD zum Herstellen einer Verbindung mit der Benutzerverwaltungs-API der Anwendung
  * Erstellen von Kontozuordnungen und Workflows, die definieren, wie die Benutzerkontendaten zwischen Azure AD und der App übertragen werden sollen
  * Verwalten des Azure AD-Bereitstellungsdiensts

* **Manuell**: Diese Option wird angezeigt, wenn Azure AD die automatische Bereitstellung von Benutzerkonten für diese Anwendung nicht unterstützt. Dies bedeutet, dass in der Anwendung gespeicherte Benutzerkontodatensätze basierend auf den Benutzerverwaltungs- und Bereitstellungsfunktionen dieser Anwendung (einschließlich SAML Just-in-Time-Bereitstellung) mit einem externen Prozess verwaltet werden müssen.

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten

Wählen Sie die Option **Automatisch** aus, um Einstellungen für Administratoranmeldeinformationen, Zuordnungen, das Starten und Beenden sowie für die Synchronisierung anzugeben.

### <a name="admin-credentials"></a>Administratoranmeldeinformationen

Erweitern Sie **Administratoranmeldeinformationen**, um die Anmeldeinformationen einzugeben, die Azure AD zum Herstellen einer Verbindung mit der Benutzerverwaltungs-API der Anwendung benötigt. Die erforderliche Eingabe variiert je nach Anwendung. Informationen zu den Anmeldeinformationstypen und den Anforderungen für bestimmte Anwendungen finden Sie im [Konfigurationstutorial der jeweiligen Anwendung](user-provisioning.md).

Durch Klicken auf **Verbindung testen** können Sie die Anmeldeinformationen testen, indem Sie Azure AD versuchen lassen, mit den angegebenen Anmeldeinformationen eine Verbindung mit der Bereitstellungs-App der App herzustellen.

### <a name="mappings"></a>Zuordnungen

Erweitern Sie **Zuordnungen**, um die Benutzerattribute anzuzeigen und zu bearbeiten, die beim Bereitstellen oder Aktualisieren von Benutzerkonten zwischen Azure AD und der Zielanwendung übertragen werden.

Zwischen Azure AD-Benutzerobjekten und Benutzerobjekten der einzelnen SaaS-Apps ist eine vorkonfigurierte Gruppe von Zuordnungen vorhanden. Einige Apps verwalten auch Gruppenobjekte. Wählen Sie in der Tabelle eine Zuordnung aus, um den Zuordnungs-Editor auf der rechten Seite zu öffnen, in dem Sie diese anzeigen und anpassen können.

![Zeigt den Bildschirm „Attributzuordnung“](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Unterstützte Anpassungen umfassen:

* Aktivieren und Deaktivieren von Zuordnungen für bestimmte Objekte, z. B. das Azure AD-Benutzerobjekt an das Benutzerobjekt der SaaS-App.
* Bearbeiten der Attribute, die vom Azure AD-Benutzerobjekt an das Benutzerobjekt der App übermittelt werden Weitere Informationen zur Attributzuordnung finden Sie unter [Grundlegendes zu Attributzuordnungstypen](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtern Sie die Bereitstellungsaktionen, die Azure AD für die Zielanwendung ausführt. Anstatt eine vollständige Synchronisierung von Objekten durch Azure AD zuzulassen, können Sie die ausgeführten Aktionen beschränken.

  Beispiel: Wenn Sie nur **Aktualisieren** auswählen, aktualisiert Azure AD nur vorhandene Benutzerkonten in einer Anwendung, ohne neue zu erstellen. Wird nur **Erstellen** ausgewählt, erstellt Azure nur neue Benutzerkonten, ohne vorhandene Konten zu aktualisieren. Dieses Feature ermöglicht Administratoren das Erstellen verschiedener Zuordnungen für Workflows zur Erstellung und Aktualisierung von Konten.

* Hinzufügen einer neuen Attributzuordnung. Klicken Sie unten im Bereich **Attributzuordnung** auf **Neue Zuordnung hinzufügen**. Füllen Sie das Formular **Attribut bearbeiten** aus, und klicken Sie auf **OK**, um die neue Zuordnung der Liste hinzuzufügen.

### <a name="settings"></a>Einstellungen

Sie können den Azure AD-Bereitstellungsdienst für die ausgewählte Anwendung im Bildschirm **Bereitstellung** im Bereich **Einstellungen** starten und beenden. Sie können auch den Bereitstellungscache leeren und den Dienst neu starten.

Wenn die Bereitstellung für eine Anwendung zum ersten Mal aktiviert wird, legen Sie den **Bereitstellungsstatus** auf **Ein** fest, um den Dienst zu aktivieren. Diese Änderung bewirkt, dass der Azure AD-Bereitstellungsdienst eine anfänglichen Zyklus ausführt. Hierbei liest er die im Abschnitt **Benutzer und Gruppen** zugewiesenen Benutzer, fragt die Zielanwendung auf diese Benutzer ab und führt dann die im Azure AD-Abschnitt **Zuordnungen** definierten Bereitstellungsaktionen aus. Während dieses Vorgangs speichert der Bereitstellungsdienst Daten im Cache dazu, welche Benutzerkonten er verwaltet, damit die Bereitstellung nicht verwalteter Konten innerhalb der Zielanwendungen, die nicht im Zuweisungsumfang enthalten waren, nicht aufgehoben wird. Nach dem ersten Zyklus synchronisiert der Bereitstellungsdienst automatisch Benutzer- und Gruppenobjekte in einem 40-Minuten-Intervall.

Durch Ändern des **Bereitstellungsstatus** in **Aus** wird der Bereitstellungsdienst angehalten. In diesem Status werden von Azure keine Benutzer- oder Gruppenobjekte in der App erstellt, aktualisiert oder entfernt. Wenn Sie den Zustand wieder in **Ein** ändern, fährt der Dienst da fort, wo er aufgehört hat.

Durch **Aktuellen Status löschen und Synchronisierung neu starten** wird ein Startzyklus ausgelöst. Der Dienst wertet dann alle Benutzer im Quellsystem nochmal aus und ermittelt, ob sie für die Bereitstellung zulässig sind. Dies kann sich als nützlich erweisen, wenn Ihre Anwendung sich derzeit in Quarantäne befindet oder wenn Sie eine Änderung an der Zuordnung Ihrer Attribute vornehmen müssen. Beachten Sie, dass die Ausführung des Startzyklus aufgrund der Anzahl der auszuwertenden Objekte länger dauert als herkömmliche inkrementelle Zyklen. Weitere Informationen zur Leistung von Startzyklen und inkrementellen Zyklen finden Sie [hier](application-provisioning-when-will-provisioning-finish-specific-user.md).
