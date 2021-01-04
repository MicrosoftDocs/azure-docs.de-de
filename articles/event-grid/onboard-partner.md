---
title: Onboarding als Azure Event Grid-Partner im Azure-Portal
description: Verwenden Sie das Azure-Portal, um das Onboarding eines Azure Event Grid-Partners durchzuführen.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 8344fcd822bfcdc67e2c27b200d97fa70dfefdf2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349385"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Onboarding als Azure Event Grid-Partner im Azure-Portal
In diesem Artikel wird beschrieben, wie das Onboarding für SaaS-Drittanbieter (auch als Ereignisherausgeber oder Partner bezeichnet) in Event Grid durchgeführt wird, damit Ereignisse aus den zugehörigen Diensten veröffentlicht werden können, und wie diese Ereignisse von Endbenutzern genutzt werden.

> [!IMPORTANT]
> Wenn Sie noch nicht mit Partnerereignissen vertraut sind, finden Sie unter [Übersicht über Partnerereignisse](partner-events-overview.md) eine detaillierte Einführung in grundlegende Konzepte, die für das Verständnis und die Ausführung der Schritte in diesem Artikel wesentlich sind.

## <a name="onboarding-process-for-event-publishers-partners"></a>Onboardingprozess für Ereignisherausgeber (Partner)
Damit Benutzer die Ereignisse Ihres Diensts nutzen können, ist zusammengefasst in der Regel folgende Vorgehensweise erforderlich:

1. **Kommunizieren Sie zunächst Ihr Interesse**, Partner zu werden, an das Event Grid-Team, bevor Sie mit den nächsten Schritten fortfahren.
1. Erstellen Sie einen Partnerthementyp durch Erstellen einer **Registrierung**. 
1. Erstellen Sie einen **Namespace**.
1. Erstellen Sie einen **Ereigniskanal** und ein **Partnerthema** (ein Schritt).
1. Testen Sie sämtliche Funktionen der Partnerereignisse umfassend.

Für Schritt 4 sollten Sie festlegen, welche Art von Benutzererfahrung Sie bereitstellen möchten. Hierzu stehen Ihnen folgende Optionen zur Verfügung:
- Stellen Sie eine eigene Lösung bereit, normalerweise eine grafische Webbenutzeroberfläche (GUI), die unter Ihrer Domäne gehostet wird, indem Sie unser SDK und/oder die REST-API verwenden, um einen Ereigniskanal und das zugehörige Partnerthema zu erstellen. Bei dieser Option können Sie den Benutzer nach dem Abonnement und der Ressourcengruppe fragen, unter denen Sie ein Partnerthema erstellen.
- Sie erstellen den Ereigniskanal und das zugehörige Partnerthema über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle. Bei dieser Option muss eine Möglichkeit gefunden werden, das Azure-Abonnement und die Ressourcengruppe des Benutzers abzurufen, unter denen Sie ein Partnerthema erstellen. 

In diesem Artikel wird das Durchführen des Onboardings als Azure Event Grid-Partner über das Azure-Portal erläutert. 

> [!NOTE]
> Die Registrierung eines Partnerthementyps ist ein optionaler Schritt. Informationen dazu, ob Sie einen Partnerthementyp erstellen sollten, finden Sie unter [Von Ereignisherausgebern verwaltete Ressourcen](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Kommunizieren Ihres Interesses, Partner zu werden
Füllen Sie [dieses Formular](https://aka.ms/gridpartnerform) aus, und wenden Sie sich unter [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) an das Event Grid-Team. Wir beraten Sie und stellen Ihnen detaillierte Informationen zu Anwendungsfällen für Partnerereignisse, Personas, dem Onboardingprozess, Funktionen, Preisen und vielem mehr zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen
Vergewissern Sie sich für die weiteren Schritte, dass Sie über Folgendes verfügen:

- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
- Einen Azure-[Mandanten](../active-directory/develop/quickstart-create-new-tenant.md)

## <a name="register-a-partner-topic-type-optional"></a>Registrieren eines Partnerthementyps (optional)
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im linken Navigationsbereich **Alle Dienste** aus, und geben Sie dann in der Suchleiste **Event Grid-Partnerregistrierungen** ein, und wählen Sie sie aus. 
1. Wählen Sie auf der Seite **Event Grid-Partnerregistrierung** auf der Symbolleiste die Option **+ Hinzufügen** aus. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Partnerregistrierungen: Link „Hinzufügen“":::
1. Geben Sie auf der Seite **Partnerregistrierung erstellen** auf der Registerkarte „Basisinformationen“ die folgenden Informationen ein: 
    1. Gehen Sie im Abschnitt **Projektdetails** wie folgt vor:
        1. Wählen Sie Ihr Azure- **Abonnement** aus. 
        1. Wählen Sie eine vorhandene **Azure-Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe. 
    1. Führen Sie im Abschnitt **Registrierungsdetails** die folgenden Schritte aus:
        1. Geben Sie unter **Registrierungsname** einen Namen für die Registrierung ein. 
        1. Geben Sie unter **Organisationsname** den Namen Ihrer Organisation ein. 
    1. Geben Sie im Abschnitt **Partnerressourcentyp** Details zu dem Ressourcentyp ein, der auf der Seite **partner topic create** (Partnerthema erstellen) angezeigt wird: 
        1. Geben Sie unter **Name des Partnerressourcentyps** den Namen für den Ressourcentyp ein. Dabei handelt es sich um den Typ des Partnerthemas, das in Ihrem Azure-Abonnement erstellt wird. 
        2. Geben Sie unter **Anzeigename** einen benutzerfreundlichen Anzeigenamen für den Partnerthementyp (Ressourcentyp) ein. 
        3. Geben Sie eine **Beschreibung** für den Ressourcentyp ein. 
        4. Geben Sie eine **Szenariobeschreibung** ein. Sie sollte erklären, auf welche Weise oder in welchen Szenarien die Partnerthemen für Ihre Ressourcen verwendet werden können.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Partnerregistrierung erstellen":::            
1. Klicken Sie auf **Weiter: Kundendienst** unten auf der Seite. Geben Sie auf der Registerkarte **Kundendienst** der Seite **Partnerregistrierung erstellen** Informationen ein, die Abonnentenbenutzer verwenden können, um Sie bei einem Problem mit der Ereignisquelle zu kontaktieren:
    1. Geben Sie die **Telefonnummer** ein.
    1. Geben Sie die **Durchwahl** ein.
    1. Geben Sie die **URL** der Support-Website ein. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Partnerregistrierung erstellen: Kundendienst":::        
1. Klicken Sie auf **Weiter: Tags** unten auf der Seite. 
1. Konfigurieren Sie auf der Seite **Tags** die folgenden Werte. 
    1. Geben Sie einen **Namen** und einen **Wert** für das Tag ein, das Sie hinzufügen möchten. Dieser Schritt ist **optional**. 
    1. Wählen Sie unten auf der Seite **Überprüfen + erstellen** aus, um die Registrierung (Partnerthementyp) zu erstellen.

## <a name="create-a-partner-namespace"></a>Erstellen von Partnernamespaces

1. Wählen Sie im Azure-Portal im linken Navigationsmenü **Alle Dienste** aus, geben Sie dann **Event Grid-Partnernamespaces** in der Suchleiste ein, und wählen Sie einen in der Liste aus. 
1. Wählen Sie auf der Seite **Event Grid-Partnernamespaces** auf der Symbolleiste die Option **+ Hinzufügen** aus. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Partnernamespaces: Link „Hinzufügen“":::
1. Geben Sie auf der Seite **Partnernamespace erstellen** auf der Registerkarte „Basisinformationen“ die folgenden Informationen an.
    1. Gehen Sie im Abschnitt **Projektdetails** wie folgt vor: 
        1. Wählen Sie ein Azure-**Abonnement** aus.
        1. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine Ressourcengruppe. 
    1. Führen Sie im Abschnitt **Details zum Namespace** die folgenden Schritte aus:
        1. Geben Sie einen **Namen** für den Namespace ein. 
        1. Wählen Sie einen **Standort** für den Namespace aus. 
    1. Gehen Sie im Abschnitt **Registrierungsdetails** wie folgt vor, um den Namespace einer Partnerregistrierung zuzuordnen. 
        1. Wählen Sie das **Abonnement** aus, in dem die Partnerregistrierung enthalten ist. 
        1. Wählen Sie die **Ressourcengruppe** mit der Partnerregistrierung aus. 
        1. Wählen Sie in der Dropdownliste die **Partnerregistrierung** aus.
    1. Klicken Sie auf **Weiter: Tags** unten auf der Seite.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Partnernamespace erstellen: Seite „Basisinformationen“":::
1. Fügen Sie auf der Seite **Tags** Tags hinzu (optional).
    1. Geben Sie einen **Namen** und einen **Wert** für das Tag ein, das Sie hinzufügen möchten. Dieser Schritt ist **optional**.
    1. Wählen Sie am unteren Rand der Seite die Option **Bewerten + erstellen** aus.         
1. Überprüfen Sie die Details auf der Seite **Überprüfen + erstellen**, und wählen Sie **Erstellen** aus. 

## <a name="create-an-event-channel"></a>Erstellen von Ereigniskanälen
> [!IMPORTANT]
> Sie müssen vom Benutzer ein Azure-Abonnement, eine Ressourcengruppe, einen Standort und einen Partnerthemennamen anfordern, um ein Partnerthema zu erstellen, das der Benutzer besitzen und verwalten wird.

1. Wechseln Sie zur **Übersichtsseite** des Namespace, den Sie erstellt haben. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Partnernamespace: Übersichtsseite":::
    partner-namespace-overview.png
1. Wählen Sie auf der Symbolleiste die Option **+ Event Channel** (Ereigniskanal) aus. 
1. Geben Sie auf der Seite **Ereigniskanal erstellen** auf der Registerkarte „Basisinformationen“ die folgenden Informationen an. 
    1. Führen Sie im Abschnitt **Kanaldetails** die folgenden Schritte aus:
        1. Geben Sie unter **Name des Ereigniskanals** einen Namen für den Ereigniskanal ein. 
        1. Geben Sie die **Quelle** ein. Informationen zu einem geeigneten Wert für die Quelle finden Sie in den [Spezifikationen für CloudEvents 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1). Weitere Informationen finden Sie unter [diesem Beispiel für ein CloudEvents-Schema](cloud-event-schema.md#sample-event-using-cloudevents-schema).
        1. Geben Sie die Quelle ein (Worum handelt es sich?).
    1. Geben Sie im Abschnitt **Zieldetails** Details zum Zielpartnerthema ein, das für diesen Ereigniskanal erstellt wird. 
        1. Geben Sie die **ID des Abonnements** ein, in dem das Partnerthema erstellt wird. 
        1. Geben Sie den **Namen der Ressourcengruppe** ein, in der das Partnerthema erstellt wird. 
        1. Geben Sie einen **Namen für das Partnerthema** ein. 
    1. Klicken Sie auf **Weiter: Filter** unten auf der Seite. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Ereigniskanal erstellen: Seite „Basisinformationen“":::
1. Fügen Sie auf der Seite **Filter** Filter hinzu. führen Sie folgende Schritte aus:
    1. Filtern Sie nach den Attributen der einzelnen Ereignisse. Es werden nur die Ereignisse übermittelt, die allen Filtern entsprechen. Sie können bis zu 25 Filter angeben. Bei den Vergleichen wird die Groß-/Kleinschreibung nicht beachtet. Die für die Filter verwendeten gültigen Schlüssel variieren basierend auf dem Ereignisschema. Im folgenden Beispiel können `eventid`, `source`, `eventtype` und `eventtypeversioin` als Schlüssel verwendet werden. Sie können auch benutzerdefinierte Eigenschaften innerhalb der Nutzdaten verwenden, indem Sie `.` als Schachtelungsoperator verwenden. Beispiel: `data`, `data.key`, `data.key1.key2`.
    1. Klicken Sie auf **Weiter: Zusätzliche Features** unten auf der Seite. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Ereigniskanal erstellen: Seite „Filter“":::
        create-event-channel-filters-page.png
1. Auf der Seite **Zusätzliche Features** können Sie eine **Ablaufzeit** und eine **Beschreibung für das Partnerthema** angeben. 
    1. Die **Ablaufzeit** ist der Zeitpunkt, zu dem das Thema und der zugehörige Ereigniskanal automatisch gelöscht werden, wenn sie vom Kunden nicht aktiviert werden. Wenn keine Ablaufzeit angegeben wird, gilt der Standardwert von sieben Tagen. Aktivieren Sie das Kontrollkästchen, um eine eigene Ablaufzeit anzugeben. 
    1. Da es sich bei diesem Thema um eine Ressource handelt, die nicht von den Benutzern erstellt wird, können Sie ihnen über eine **Beschreibung** den Inhalt des Themas vermitteln. Wenn Sie keine Beschreibung angeben, wird eine generische Beschreibung bereitgestellt. Aktivieren Sie das Kontrollkästchen, um eine eigene Beschreibung des Partnerthemas festzulegen. 
    1. Klicken Sie auf **Weiter: Überprüfen + erstellen**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Ereigniskanal erstellen: Seite „Zusätzliche Features“":::
1. Überprüfen Sie auf der Seite **Überprüfen + erstellen** die Einstellungen, und wählen Sie dann **Erstellen** aus, um den Ereigniskanal zu erstellen. 

## <a name="next-steps"></a>Nächste Schritte
- [Partnerthemen in Azure Event Grid (Vorschau)](./partner-events-overview.md)
- [Onboarding-Formular für Partnerthemen](https://aka.ms/gridpartnerform)
- [Auth0-Partnerthema](auth0-overview.md)
- [Verwenden des Auth0-Partnerthemas](auth0-how-to.md)