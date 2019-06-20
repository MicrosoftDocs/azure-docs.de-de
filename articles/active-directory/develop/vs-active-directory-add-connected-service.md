---
title: Hinzufügen von Azure Active Directory mithilfe von verbundenen Diensten in Visual Studio
description: Fügen Sie Azure Active Directory mithilfe des Dialogfelds "Verbundene Dienste hinzufügen" in Visual Studio hinzu
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bea0a870a6ef0685f4f4bce5ad3b0d1ff1f616a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414008"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Hinzufügen von Azure Active Directory mithilfe von verbundenen Diensten in Visual Studio

Mithilfe von Azure Active Directory (Azure AD) können Sie das einmalige Anmelden (Single Sign-On, SSO) für ASP.NET MVC-Webanwendungen oder Active Directory-Authentifizierung in Web-API-Diensten unterstützen. Mit der Azure AD-Authentifizierung können Ihre Benutzer ihre Konten in Azure Active Directory verwenden, um eine Verbindung mit Ihren Webanwendungen herzustellen. Die Vorteile der Azure AD-Authentifizierung mit Web-API sind u. a. eine verbesserte Sicherheit, wenn eine API über eine Webanwendung verfügbar gemacht wird. Mit Azure AD benötigen Sie kein separates Authentifizierungssystem mit eigenem Konto und Benutzerverwaltung.

Dieser Artikel und die Begleitartikel enthalten Details zur Verwendung des Features „Verbundener Visual Studio-Dienst“ für Active Directory. Die Funktion ist in Visual Studio 2015 und höher verfügbar.

Derzeit unterstützt der verbundene Dienst für Active Directory keine ASP.NET Core-Anwendungen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Konto: Wenn Sie noch nicht über ein Azure-Konto verfügen, können Sie sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oder Ihre [Visual Studio-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** oder eine höhere Version. [Laden Sie Visual Studio jetzt herunter](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Herstellen einer Verbindung mit Azure Active Directory über das Dialogfeld „Verbundene Dienste“

1. Erstellen oder öffnen Sie in Visual Studio ein ASP.NET MVC-Projekt oder ein ASP.NET-Web-API-Projekt. Sie können die Vorlagen „MVC“, „Web-API“, „Single-Page-Webanwendung“, „Azure-API-App“, „Azure Mobile App“ und „Azure Mobile Service“ verwenden.

1. Wählen Sie den Menübefehl **Projekt > Verbundenen Dienst hinzufügen...** , oder doppelklicken Sie unter dem Projekt im Projektmappen-Explorer auf den Knoten **Verbundene Dienste**.

1. Wählen Sie auf der Seite **Verbundene Dienste** die Option **Authentifizierung mit Azure Active Directory**.

    ![Seite „Verbundene Dienste“](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Wählen Sie auf der Seite **Einführung** die Option **Weiter**. Falls auf dieser Seite Fehler angezeigt werden, helfen Ihnen die Informationen unter [Diagnostizieren von Fehlern mit dem verbundenen Dienst für Azure Active Directory](vs-active-directory-error.md) weiter.

    ![Seite „Einführung“](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Wählen Sie auf der Seite **Einmaliges Anmelden** in der Dropdownliste **Domäne** eine Domäne aus. Die Liste enthält alle Domänen, auf die mit den Konten zugegriffen werden kann, die im Visual Studio-Dialogfeld „Kontoeinstellungen“ (**Datei > Kontoeinstellungen...** ) aufgeführt sind. Alternativ können Sie einen Domänennamen eingeben, wenn Sie den gewünschten nicht finden, beispielsweise `mydomain.onmicrosoft.com`. Sie können die Option zum Erstellen einer neuen Azure Active Directory-App auswählen oder die Einstellungen einer vorhandenen Azure Active Directory-App verwenden. Wählen Sie abschließend die Option **Weiter** aus.

    ![Seite „Einmaliges Anmelden“](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Treffen Sie auf der Seite **Verzeichniszugriff** für die Option **Verzeichnisdaten lesen** die gewünschte Wahl. Entwickler binden diese Option in der Regel ein.

    ![Seite „Verzeichniszugriff“](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Wählen Sie **Fertig stellen**, um mit den Änderungen an Ihrem Projekt zu beginnen und die Azure AD-Authentifizierung zu aktivieren. In Visual Studio wird hierfür jeweils der Status angezeigt:

    ![Verbundener Dienst für Active Directory – Status](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Nachdem der Prozess abgeschlossen wurde, wird Ihr Browser von Visual Studio mit einem der folgenden Artikel geöffnet (je nach Projekttyp):

    - [Erste Schritte mit .NET MVC-Projekten](vs-active-directory-dotnet-getting-started.md)
    - [Erste Schritte mit WebAPI-Projekten](vs-active-directory-webapi-getting-started.md)

1. Die Active Directory-Domäne wird auch im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) angezeigt.

## <a name="how-your-project-is-modified"></a>Änderungen am Projekt

Beim Hinzufügen des verbundenen Diensts zum Assistenten fügt Visual Studio Ihrem Projekt Azure Active Directory und entsprechende Verweise hinzu. Zudem werden Konfigurationsdateien und Codedateien im Projekt geändert, um Unterstützung für Azure AD hinzuzufügen. Die genauen Änderungen, die von Visual Studio vorgenommen werden, hängen vom Projekttyp ab. Die folgenden Artikel enthalten hierzu ausführliche Informationen:

- [Was ist mit meinem .NET MVC-Projekt passiert?](vs-active-directory-dotnet-what-happened.md)
- [Was ist mit meinem Web-API-Projekt passiert?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierungsszenarien für Azure Active Directory](authentication-scenarios.md)
- [Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App](quickstart-v1-aspnet-webapp.md)
