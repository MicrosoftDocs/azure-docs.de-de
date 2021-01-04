---
title: Aktivieren des gestaffelten Rollouts von Features für Zielgruppen
titleSuffix: Azure App Configuration
description: Erfahren Sie, wie Sie das gestaffelte Rollout von Features für Zielgruppen aktivieren.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929683"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Aktivieren des gestaffelten Rollouts von Features für Zielgruppen

Featureflags ermöglichen das dynamische Aktivieren oder Deaktivieren von Funktionen in Ihrer Anwendung. Featurefilter ermitteln bei jeder Auswertung des Featureflags seinen Zustand. Die `Microsoft.FeatureManagement`-Bibliothek enthält den `TargetingFilter`, mit dem ein Featureflag für eine angegebene Liste von Benutzern und Gruppen oder für einen angegebenen Prozentsatz von Benutzern aktiviert wird. `TargetingFilter` ist „fixiert“. Dies bedeutet, dass einem einzelnen Benutzer, sobald er ein Feature erhalten hat, dieses Feature weiterhin bei allen zukünftigen Anforderungen angezeigt wird. Sie können `TargetingFilter` verwenden, um ein Feature für ein bestimmtes Konto während einer Demoversion zu aktivieren, um neue Features in unterschiedlichen Gruppen oder „Ringen“ fortschreitend einzuführen und vieles mehr.

In diesem Artikel erfahren Sie, wie Sie ein neues Feature in einer ASP.NET Core-Webanwendung für bestimmte Benutzer und Gruppen mithilfe von `TargetingFilter` mit Azure App Configuration einführen.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Erstellen einer Webanwendung mit Featureflags und Authentifizierung

Für das Rollout von Features auf Grundlage von Benutzern und Gruppen benötigen Sie eine Webanwendung, die es Benutzern ermöglicht, sich anzumelden.

1. Erstellen Sie mithilfe des folgenden Befehls eine Webanwendung, die die Authentifizierung gegen eine lokale Datenbank durchführt:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Erstellen Sie die App, führen Sie sie aus, und wählen Sie dann den Link **Registrieren** in der oberen rechten Ecke aus, um ein neues Benutzerkonto zu erstellen. Verwenden Sie eine E-Mail-Adresse wie `test@contoso.com`. Wählen Sie im Bildschirm **Registrierungsbestätigung** die Option **Hier klicken, um Ihr Konto zu bestätigen**.

1. Führen Sie die Schritte in [Schnellstart: Hinzufügen von Featureflags zu einer ASP.NET Core-App](./quickstart-feature-flag-aspnet-core.md) aus, um Ihrer neuen Webanwendung ein Featureflag hinzuzufügen.

1. Schalten Sie das Featureflag in App Configuration um. Überprüfen Sie, ob durch diese Aktion die Sichtbarkeit des **Beta**-Elements in der Navigationsleiste gesteuert wird.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Aktualisieren des Webanwendungscodes für die Verwendung von TargetingFilter

An diesem Punkt können Sie das Featureflag verwenden, um das `Beta`-Feature für alle Benutzer zu aktivieren oder zu deaktivieren. Um das Featureflag für einige Benutzer zu aktivieren, während es für andere deaktiviert wird, aktualisieren Sie Ihren Code so, dass er `TargetingFilter` verwendet. In diesem Beispiel verwenden Sie die E-Mail-Adresse des angemeldeten Benutzers als Benutzer-ID und den Domänennamensteil der E-Mail-Adresse als Gruppe. Den Benutzer und die Gruppe fügen Sie dem `TargetingContext` hinzu. Der `TargetingFilter` verwendet diesen Kontext für jede Anforderung, um den Zustand des Featureflags zu bestimmen.

1. Aktualisieren Sie auf die neueste Version des `Microsoft.FeatureManagement.AspNetCore`-Pakets.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Fügen Sie eine Datei *TestTargetingContextAccessor.cs* hinzu:

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. Fügen Sie in *Startup.cs* einen Verweis auf den Namespace *Microsoft.FeatureManagement.FeatureFilters* hinzu:

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Aktualisieren Sie die *ConfigureServices*-Methode, um im Anschluss an den Aufruf von `AddFeatureManagement()` `TargetingFilter` zu registrieren:

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Aktualisieren Sie die *ConfigureServices*-Methode, um den im vorherigen Schritt erstellten `TestTargetingContextAccessor` der Dienstsammlung hinzuzufügen. Der *TargetingFilter* verwendet diesen, um jedes Mal, wenn das Featureflag ausgewertet wird, den Zielgruppenadressierungs-Kontext zu bestimmen.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

Die gesamte *ConfigureServices*-Methode sieht dann folgendermaßen aus:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Aktualisieren des Featureflags für die Verwendung von TargetingFilter

1. Wechseln Sie im Azure-Portal zu Ihrem App Configuration-Speicher, und wählen Sie **Feature-Manager** aus.

1. Wählen Sie das Kontextmenü für das Featureflag *Beta* aus, das Sie im Rahmen der Schnellstartanleitung erstellt haben. Wählen Sie **Bearbeiten** aus.

    > [!div class="mx-imgBorder"]
    > ![Bearbeiten des Featureflags „Beta“](./media/edit-beta-feature-flag.png)

1. Aktivieren Sie im Bildschirm **Bearbeiten** das Kontrollkästchen **Featureflag aktivieren**, sofern es nicht bereits aktiviert ist. Aktivieren Sie dann das Kontrollkästchen **Featurefilter verwenden**.

1. Aktivieren Sie das Optionsfeld **Targeting** (Zielgruppenadressierung).

1. Wählen Sie die folgenden Optionen:

    - **Standardprozentsatz**: 0
    - **Gruppen**: Geben Sie den **Namen** _contoso.com_ und einen **Prozentsatz** von _50_ ein.
    - **Benutzer**: `test@contoso.com`

    Der Funktionsfilter-Bildschirm sieht wie folgt aus:

    > [!div class="mx-imgBorder"]
    > ![Bedingtes Featureflag](./media/feature-flag-filter-enabled.png)

    Diese Einstellungen führen zu folgendem Verhalten:

    - Das Featureflag ist für den Benutzer `test@contoso.com` immer aktiviert, weil `test@contoso.com` im Abschnitt _Benutzer_ aufgeführt ist.
    - Das Featureflag ist für 50 % der anderen Benutzer in der Gruppe _contoso.com_ aktiviert, weil _contoso.com_ im Abschnitt _Gruppen_ mit einem _Prozentsatz_ von _50_ aufgeführt ist.
    - Das Feature ist für alle anderen Benutzer immer deaktiviert, weil der _Standardprozentsatz_ auf _0_ festgelegt ist.

1. Wählen Sie **Anwenden** aus, um diese Einstellungen zu speichern und zum Bildschirm **Feature-Manager** zurückzukehren.

1. Der **Featurefilter** wird für das Featureflag nun als *Targeting* (Zielgruppenadressierung) angezeigt. Dieser Zustand gibt an, dass das Featureflag anforderungsspezifisch auf der Grundlage der Kriterien des Featurefilters *Targeting* (Zielgruppenadressierung) aktiviert oder deaktiviert wird.

## <a name="targetingfilter-in-action"></a>„TargetingFilter“ in Aktion

Um die Auswirkungen dieses Featureflags zu sehen, erstellen Sie die Anwendung, und führen Sie sie aus. Anfänglich wird das *Beta*-Element nicht in der Symbolleiste angezeigt, weil die Option _Standardprozentsatz_ auf 0 festgelegt ist.

Melden Sie sich jetzt mit dem Kennwort, das Sie bei der Registrierung festgelegt haben, als `test@contoso.com` an. Das *Beta*-Element wird jetzt in der Symbolleiste angezeigt, weil `test@contoso.com` als Zielbenutzer angegeben ist.

Das folgende Video zeigt dieses Verhalten:

> [!div class="mx-imgBorder"]
> ![„TargetingFilter“ in Aktion](./media/feature-flags-targetingfilter.gif)

Sie können weitere Benutzer mit `@contoso.com`-E-Mail-Adressen erstellen, um das Verhalten der Gruppeneinstellungen anzuzeigen. 50 % dieser Benutzer wird das *Beta*-Element angezeigt. Den anderen 50 % wird das *Beta*-Element nicht angezeigt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übersicht über die Featureverwaltung](./concept-feature-management.md)