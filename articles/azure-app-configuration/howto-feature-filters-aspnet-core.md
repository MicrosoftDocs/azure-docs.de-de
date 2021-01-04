---
title: Verwenden von Featurefiltern, um bedingte Featureflags zu aktivieren
titleSuffix: Azure App Configuration
description: Erfahren Sie, wie Sie Featurefilter verwenden, um bedingte Featureflags zu aktivieren.
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 9a6ae1581b3958495010b032980b0de2e267088b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931876"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Verwenden von Featurefiltern, um bedingte Featureflags zu aktivieren

Featureflags ermöglichen das Aktivieren oder Deaktivieren von Funktionen in Ihrer Anwendung. Ein einfaches Featureflag ist entweder aktiviert oder deaktiviert. Die Anwendung verhält sich immer gleich. Ein Beispiel: Sie können ein neues Feature hinter einem Featureflag einführen. Ist das Featureflag aktiviert, steht das neue Feature allen Benutzern zur Verfügung. Wenn Sie das Featureflag deaktivieren, wird das neue Feature ausgeblendet.

Im Gegensatz dazu kann ein _bedingtes Featureflag_ dynamisch aktiviert oder deaktiviert werden. Die Kriterien des Featureflags wirken sich unter Umständen auf das Verhalten der Anwendung aus. Angenommen, Sie möchten Ihr neues Feature zunächst nur einer kleinen Teilmenge von Benutzern präsentieren. Mit einem bedingten Featureflag können Sie das Featureflag für einige Benutzer aktivieren und für andere deaktivieren. _Featurefilter_ ermitteln bei jeder Auswertung den Zustand des Featureflags.

Die Bibliothek `Microsoft.FeatureManagement` enthält drei Featurefilter:

- `PercentageFilter` aktiviert das Featureflag auf der Grundlage eines Prozentwerts.
- `TimeWindowFilter` aktiviert das Featureflag während eines bestimmten Zeitfensters.
- `TargetingFilter` aktiviert das Featureflag für bestimmte Benutzer und Gruppen.

Sie können auch einen eigenen Featurefilter erstellen, der die [Microsoft.FeatureManagement.IFeatureFilter-Schnittstelle](/dotnet/api/microsoft.featuremanagement.ifeaturefilter) implementiert.

## <a name="registering-a-feature-filter"></a>Registrieren eines Featurefilters

Rufen Sie zum Registrieren eines Featurefilters die Methode `AddFeatureFilter` auf, und geben Sie dabei den Namen des Featurefilters an. Mit dem folgenden Code wird beispielsweise `PercentageFilter` registriert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurieren eines Featurefilters in Azure App Configuration

Einige Featurefilter verfügen über zusätzliche Einstellungen. `PercentageFilter` aktiviert beispielsweise ein Feature auf der Grundlage eines Prozentwerts. Zu diesem Zweck steht eine Einstellung zum Definieren des gewünschten Prozentwerts zur Verfügung.

Diese Einstellungen können für definierte Featureflags in Azure App Configuration konfiguriert werden. Führen Sie beispielsweise die folgenden Schritte aus, um das Featureflag mithilfe von `PercentageFilter` für 50 Prozent der Anforderungen zu aktivieren, die an eine Web-App gesendet werden:

1. Führen Sie die Schritte in [Schnellstart: Hinzufügen von Featureflags zu einer ASP.NET Core-App](./quickstart-feature-flag-aspnet-core.md) aus, um eine Web-App mit einem Featureflag zu erstellen.

1. Wechseln Sie im Azure-Portal zu Ihrem Konfigurationsspeicher, und klicken Sie auf **Feature-Manager**.

1. Klicken Sie auf das Kontextmenü für das Featureflag *Beta*, das Sie im Rahmen der Schnellstartanleitung erstellt haben. Klicken Sie auf **Bearbeiten**.

    > [!div class="mx-imgBorder"]
    > ![Bearbeiten des Featureflags „Beta“](./media/edit-beta-feature-flag.png)

1. Aktivieren Sie im Bildschirm **Bearbeiten** das Optionsfeld **Ein**, sofern es nicht bereits aktiviert ist. Klicken Sie anschließend auf die Schaltfläche **Filter hinzufügen**. (Die Beschriftung des Optionsfeld **Ein** ändert sich in **Bedingt**.)

1. Geben Sie im Feld **Schlüssel** die Zeichenfolge *Microsoft.Percentage* ein.

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen eines Featurefilters](./media/feature-flag-add-filter.png)

1. Klicken Sie neben dem Featurefilterschlüssel auf das Kontextmenü. Klicken Sie auf **Parameter bearbeiten**.

    > [!div class="mx-imgBorder"]
    > ![Bearbeiten von Featurefilterparametern](./media/feature-flag-edit-filter-parameters.png)

1. Zeigen Sie auf den Bereich unter der Kopfzeile **Name**, sodass Textfelder im Raster angezeigt werden. Geben Sie unter **Name** den Namen *Value* und unter **Wert** den Wert „50“ ein. Das Feld **Wert** gibt den Prozentsatz von Anforderungen an, für die der Featurefilter aktiviert werden soll.

    > [!div class="mx-imgBorder"]
    > ![Festlegen von Featurefilterparametern](./media/feature-flag-set-filter-parameters.png)

1. Klicken Sie auf **Anwenden**, um zum Bildschirm **Featureflag bearbeiten** zurückzukehren. Klicken Sie anschließend erneut auf **Anwenden**, um die Featureflageinstellungen zu speichern.

1. Unter **Zustand** wird für das Featureflag nun *Bedingt* angezeigt. Dieser Zustand gibt an, dass das Featureflag anforderungsspezifisch auf der Grundlage der Kriterien des Featurefilters aktiviert oder deaktiviert wird.

    > [!div class="mx-imgBorder"]
    > ![Bedingtes Featureflag](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Featurefilter in Aktion

Starten Sie zum Überprüfen der Auswirkungen dieses Featureflags die Anwendung, und klicken Sie in Ihrem Browser mehrmals auf die Schaltfläche **Aktualisieren**. Das Element *Beta* wird in etwa 50 Prozent der Fälle auf der Symbolleiste angezeigt. In den übrigen Fällen wird es nicht angezeigt, da das Feature *Beta* durch `PercentageFilter` für eine Teilmenge von Anforderungen deaktiviert wird. Das folgende Video zeigt dieses Verhalten:

> [!div class="mx-imgBorder"]
> ![„TargetingFilter“ in Aktion](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktivieren des gestaffelten Rollouts von Features für Zielgruppen](./howto-targetingfilter-aspnet-core.md)
