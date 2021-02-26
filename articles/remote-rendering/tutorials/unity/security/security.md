---
title: Schützen von Azure-Remote Rendering und Modellspeicher
description: Härten einer Remote Rendering-Anwendung zum Sichern von Inhalten
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b1bcba264589d6cbe9b4f671e1e4f2c9b1dbf2c5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594247"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Tutorial: Schützen von Azure-Remote Rendering und Modellspeicher

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Schützen von Azure Blob Storage mit Azure Remote Rendering-Modellen
> * Authentifizieren mit Azure AD für das Zugreifen auf Ihre Azure Remote Rendering-Instanz
> * Verwenden von Azure-Anmeldeinformationen zur Azure Remote Rendering-Authentifizierung

## <a name="prerequisites"></a>Voraussetzungen

* Dieses Tutorial baut auf dem Tutorial [ auf: Optimieren Sie Materialien, Beleuchtung und Effekte](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Vorteile zusätzlicher Sicherheit

Der aktuelle Zustand der Anwendung und der Zugang zu Ihren Azure-Ressourcen sehen wie folgt aus:

![Anfängliche Sicherheit](./media/security-one.png)

Sowohl „AccountID + AccountKey“ als auch „URL + SAS Token“ speichern im Grunde einen Benutzernamen und ein Kennwort. Wenn z. B. „AccountID + AccountKey“ offengelegt wurde, wäre es für einen Angreifer einfach, Ihre ARR-Ressourcen ohne Ihre Erlaubnis zu verwenden.

## <a name="securing-your-content-in-azure-blob-storage"></a>Sichern Ihrer Inhalte in Azure Blob Storage

Azure Remote Rendering kann mit der richtigen Konfiguration auf sichere Weise auf die Inhalte Ihrer Azure Blob Storage-Instanz zugreifen. Weitere Informationen finden Sie unter [Vorgehensweise: Verknüpfen Sie Speicherkonten](../../../how-tos/create-an-account.md#link-storage-accounts), um Ihre Azure Remote Rendering-Instanz mit Ihren Azure Blob Storage-Konten zu konfigurieren.

Wenn Sie einen verknüpften Blob-Speicher verwenden, nutzen Sie zum Laden von Modellen andere Methoden:

```cs
var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams);
```

In den obigen Zeilen wird die `FromSas`-Version der Parameter- und Sitzungsaktion verwendet. Sie müssen in Nicht-SAS-Versionen konvertiert werden:

```cs
var loadModelParams = new LoadModelOptions(storageAccountPath, blobContainerName, modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams);
```

Wir ändern **RemoteRenderingCoordinator**, um ein benutzerdefiniertes Modell aus einem verknüpften Blob-Speicherkonto zu laden.

1. Falls dies noch nicht geschehen ist, schließen Sie die [Anleitung: Verknüpfen von Speicherkonten](../../../how-tos/create-an-account.md#link-storage-accounts) ab, um Ihrer ARR-Instanz die Berechtigung für den Zugriff auf Ihre Blob Storage-Instanz zu erteilen.
1. Fügen Sie **RemoteRenderingCoordinator** direkt unterhalb der aktuellen **LoadModel**-Methode die folgende geänderte **LoadModel**-Methode hinzu:

    ```cs
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelOptions($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

    Der Code ist größtenteils mit der ursprünglichen `LoadModel`-Methode identisch, jedoch wurde die SAS-Version der Methodenaufrufe durch Nicht-SAS-Versionen ersetzt.

    Die zusätzlichen Eingaben `storageAccountName` und `blobContainerName` wurden ebenfalls den Argumenten hinzugefügt. Wir rufen diese neue **LoadModel**-Methode aus einer anderen Methode auf, die der ersten **LoadTestModel**-Methode ähnelt, die wir im ersten Tutorial erstellt haben.

1. Fügen Sie die folgende Methode zu **RemoteRenderingCoordinator** direkt hinter **LoadTestModel** hinzu.

    ```cs
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Dieser Code fügt Ihrer **RemoteRenderingCoordinator**-Komponente drei zusätzliche Zeichenfolgenvariablen hinzu.
    ![Screenshot: Hervorhebung der Optionen für den Speicherkontonamen, Blobcontainernamen und den Modellpfad der RemoteRenderingCoordinator-Komponente](./media/storage-account-linked-model.png)

1. Fügen Sie der **RemoteRenderingCoordinator**-Komponente Werte hinzu. Wenn Sie den [Schnellstart für Modellkonvertierung](../../../quickstarts/convert-model.md) befolgt haben, lauten Ihre Werte wie folgt:

    * **Speicherkontoname:** Ihr Speicherkontoname ist der global eindeutige Name, den Sie für Ihr Speicherkonto auswählen. Im Schnellstart lautete er *arrtutorialstorage*, Ihr Wert wird jedoch anders sein.
    * **Blob-Containername**: ARR-Ausgabe, der Blob Storage-Container
    * **Modellpfad**: Die in der *arrconfig.json*-Datei definierte Kombination aus „outputFolderPath“ und „outputAssetFileName“. In der Schnellstartanleitung war dies „outputFolderPath“:„converted/robot“, „outputAssetFileName“:„robot.arrAsset“. Wenn der Wert des Modellpfads „converted/robot/robot.arrAsset“ ergibt, wird der Wert anders angezeigt.

    >[!TIP]
    > Wenn Sie das Skript [**Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion) ohne das Argument „-UseContainerSas“ ausführen, gibt das Skript alle oben aufgeführten Werte anstelle des SAS-Tokens aus. ![Verknüpftes Modell](./media/converted-output.png)
1. Entfernen oder deaktivieren Sie für den Moment das GameObject-**TestModel**, um Platz für das Laden des benutzerdefinierten Modells zu schaffen.
1. Spielen Sie die Szene ab, und stellen Sie eine Verbindung mit einer Remotesitzung her.
1. Klicken Sie mit der rechten Maustaste auf **RemoteRenderingCoordinator**, und wählen Sie die Option **Load Linked Custom Model** (Verknüpftes benutzerdefiniertes Modell laden) aus.
    ![Laden des verknüpften Modells](./media/load-linked-model.png)

Diese Schritte erhöhen die Sicherheit der Anwendung, indem Sie das SAS-Token aus der lokalen Anwendung entfernen.

Der aktuelle Zustand der Anwendung und der Zugang zu Ihren Azure-Ressourcen sehen wie folgt aus:

![Höhere Sicherheit](./media/security-two.png)

Ein weiteres „Kennwort“ (AccountKey) muss aus der lokalen Anwendung entfernt werden. Dies kann mithilfe der Azure Active Directory-Authentifizierung (ADD) erfolgen.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory-Authentifizierung (Azure AD)

Mit der AAD-Authentifizierung können Sie feststellen, welche Personen oder Gruppen ARR auf kontrolliertere Weise verwenden. ARR verfügt über integrierte Unterstützung für die Annahme von [Zugriffstoken](../../../../active-directory/develop/access-tokens.md) anstelle eines Kontoschlüssels. Sie können sich Zugriffstoken als einen zeitlich begrenzten, benutzerspezifischen Schlüssel vorstellen, der nur bestimmte Teile der spezifischen Ressource freischaltet, für die er angefordert wurde.

Das Skript **RemoteRenderingCoordinator** verfügt über einen Delegaten namens **ARRCredentialGetter**, der eine Methode beinhaltet, die das Objekt **SessionConfiguration** zurückgibt. Dieses wird zur Konfiguration der Remotesitzungsverwaltung verwendet. Sie können **ARRCredentialGetter** eine andere Methode zuweisen, sodass Sie einen Azure-Anmeldeflow verwenden können. Dadurch wird ein **SessionConfiguration**-Objekt generiert, das ein Azure-Zugriffstoken enthält. Dieses Zugriffstoken wird speziell für den Benutzer verwendet, der sich anmeldet.

1. Befolgen Sie die [Anleitung: Konfigurieren der Authentifizierung: Authentifizierung für bereitgestellte Anwendungen](../../../how-tos/authentication.md#authentication-for-deployed-applications). Insbesondere befolgen Sie die Anweisungen in der Dokumentation für Azure Spatial Anchors [Azure AD-Benutzerauthentifizierung](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication). Dies umfasst das Registrieren einer neuen Azure Active Directory-Anwendung und das Konfigurieren des Zugriffs auf Ihre ARR-Instanz.
1. Überprüfen Sie nach dem Konfigurieren der neuen AAD-Anwendung, ob Ihre AAD-Anwendung den folgenden Abbildungen gleicht:

    **AAD-Anwendung > Authentifizierung** ![App-Authentifizierung](./media/app-authentication-public.png)

    **AAD-Anwendung > API-Berechtigungen** ![App-APIs](./media/request-api-permissions-step-five.png)

1. Überprüfen Sie nach dem Konfigurieren des Remote Rendering-Kontos, ob die Konfiguration der folgenden Abbildung gleicht:

    **AAR -> AccessControl (IAM)** ![ARR-Rolle](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Eine *Besitzer*-Rolle reicht nicht aus, um Sitzungen über die Clientanwendung zu verwalten. Für jeden Benutzer, dem Sie die Möglichkeit zum Verwalten von Sitzungen gewähren möchten, müssen Sie die Rolle **Remote Rendering-Client** bereitstellen. Für jeden Benutzer, dem Sie die Möglichkeit zum Verwalten von Sitzungen und zum Konvertieren von Modellen geben möchten, müssen Sie die Rolle **Remote Rendering-Administrator** bereitstellen.

Da nun alle Azure-Einstellungen getätigt sind, müssen wir nun ändern, wie Ihr Code eine Verbindung mit dem AAR-Dienst herstellt. Hierzu wird eine Instanz von **BaseARRAuthentication** implementiert, die ein neues **SessionConfiguration**-Objekt zurückgibt. In diesem Fall werden die Kontoinformationen mit dem Azure-Zugriffstoken konfiguriert.

1. Erstellen Sie ein neues Skript mit dem Namen **AADAuthentication**, und ersetzen Sie den Code durch Folgendes:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountAuthenticationDomain;
        public string AzureRemoteRenderingAccountAuthenticationDomain
        {
            get => azureRemoteRenderingAccountAuthenticationDomain.Trim();
            set => azureRemoteRenderingAccountAuthenticationDomain = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountAuthenticationDomain + "/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<SessionConfiguration> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountAuthenticationDomain, AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> Dieser Code ist keineswegs vollständig und nicht für eine kommerzielle Anwendung bereit. Sie sollten z. B. auch die Möglichkeit zum Abmelden hinzufügen. Dies kann mithilfe der `Task RemoveAsync(IAccount account)`-Methode erfolgen, die von der Clientanwendung bereitgestellt wird. Dieser Code ist nur für die Verwendung im Tutorial vorgesehen, Ihre Implementierung ist für Ihre Anwendung spezifisch.

Der Code versucht zunächst, das Token mithilfe von **AquireTokenSilent** im Hintergrund zu erhalten. Dies ist erfolgreich, wenn der Benutzer diese Anwendung zuvor authentifiziert hat. Wenn dies nicht erfolgreich ist, müssen Sie eine Methode anwenden, die den Benutzer stärker involviert.

Für diesen Code verwenden wir den [Gerätecodeflow](../../../../active-directory/develop/v2-oauth2-device-code.md), um ein Zugriffstoken abzurufen. Mithilfe dieses Flows kann der Benutzer sich bei seinem Azure-Konto auf einem Computer oder mobilen Gerät anmelden, und das resultierende Token wird an die HoloLens-Anwendung zurückgesendet.

Der wichtigste Teil dieser Klasse im Hinblick auf ARR ist diese Zeile:

```cs
return await Task.FromResult(new SessionConfiguration(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Hier erstellen Sie ein neues **SessionConfiguration**-Objekt, indem Sie die Kontodomäne, Konto-ID, Kontoauthentifizierungsdomäne und das Zugriffstoken verwenden. Dieses Token wird dann vom ARR-Dienst zum Abfragen, Erstellen und Verknüpfen von Remote Rendering-Sitzungen verwendet, solange der Benutzer auf der Grundlage der zuvor konfigurierten rollenbasierten Berechtigungen autorisiert ist.

Mit dieser Änderung sehen der aktuelle Zustand der Anwendung und der Zugang zu Ihren Azure-Ressourcen wie folgt aus:

![Noch höhere Sicherheit](./media/security-three.png)

Da die Anmeldeinformationen des Benutzers nicht auf dem Gerät gespeichert sind (und in diesem Fall auch nicht ins Gerät eingegeben werden), ist das Risiko der Gefährdung sehr gering. Nun verwendet das Gerät ein benutzerspezifisches, zeitlich eingeschränktes Zugriffstoken für den Zugriff auf ARR, das eine Zugriffssteuerung (IAM) für den Zugriff auf Blob Storage verwendet. Diese beiden Schritte haben die „Kennwörter“ aus dem Quellcode vollständig entfernt und die Sicherheit erheblich verbessert. Dies ist jedoch nicht die höchste Sicherheitsstufe, da durch das Verschieben des Modells und der Sitzungsverwaltung auf einen Webdienst die Sicherheit weiter verbessert wird. Weitere Sicherheitsüberlegungen werden im Kapitel [Kommerzielle Bereitschaft](../commercial-ready/commercial-ready.md) erläutert.

### <a name="testing-aad-auth"></a>Testen der AAD-Authentifizierung

Wenn die AAD-Authentifizierung aktiv ist, müssen Sie sich im Unity Editor jedes Mal authentifizieren, wenn Sie die Anwendung starten. Auf dem Gerät wird der Authentifizierungsschritt beim ersten Mal ausgeführt und ist nur dann erforderlich, wenn das Token abläuft oder ungültig wird.

1. Fügen Sie die **AADAuthentication**-Komponente dem **RemoteRenderingCoordinator**-GameObject hinzu.

    ![AAD-Authentifizierungskomponente](./media/azure-active-directory-auth-component.png)

1. Geben Sie die Werte für die Client-ID und die Mandanten-ID ein. Diese Werte finden Sie auf der Übersichtsseite Ihrer APP-Registrierung:

    * Die **Kontodomäne** ist dieselbe Domäne, die Sie in der Kontodomäne von **RemoteRenderingCoordinator** verwendet haben.
    * Die **Active Directory-Anwendungsclient-ID** ist die *Anwendungs-ID (Client)* in Ihrer AAD-Appregistrierung (siehe Abbildung unten).
    * Die **Azure-Mandanten-ID** ist die *Verzeichnis-ID (Mandant)* in Ihrer AAD-Appregistrierung (siehe Abbildung unten).
    * Die **Azure Remote Rendering-Konto-ID** ist dieselbe **Konto-ID**, die Sie für **RemoteRenderingCoordinator** verwendet haben.
    * Bei der **Kontoauthentifizierungsdomäne** handelt es sich um dieselbe **Kontoauthentifizierungsdomäne**, die Sie unter **RemoteRenderingCoordinator** verwendet haben.

    ![Screenshot: Hervorhebung der Anwendungs-ID (Client) und der Verzeichnis-ID (Mandant)](./media/app-overview-data.png)

1. Klicken Sie im Unity-Editor auf „Abspielen“, und stimmen Sie dem Ausführen einer Sitzung zu.
    Da die **AADAuthentication**-Komponente über einen Ansichtscontroller verfügt, wird sie automatisch verknüpft, um nach dem modalen Sitzungsautorisierungsbereich eine Eingabeaufforderung anzuzeigen.
1. Befolgen Sie die Anweisungen im Bereich rechts neben **AppMenu**.
    Die Anzeige sollte in etwa der folgenden Ausgabe entsprechen: ![Abbildung des Anweisungsbereichs rechts neben „AppMenu“](./media/device-flow-instructions.png)
    Nach der Eingabe der bereitgestellten Codierung auf Ihrem sekundären Gerät (oder Browser auf demselben Gerät) und dem Einloggen mit Ihren Anmeldeinformationen wird ein Zugriffstoken an die anfordernde Anwendung zurückgegeben. Dies ist in diesem Fall der Unity Editor.
1. Jetzt sollten alle Prozesse in der Anwendung normal fortgesetzt werden. Überprüfen Sie die Unity-Konsole auf Fehler, wenn Sie die einzelnen Schritte nicht erwartungsgemäß durchführen können.

## <a name="build-to-device"></a>Entwickeln auf dem Gerät

Wenn Sie eine Anwendung mit MSAL auf dem Gerät entwickeln, muss eine Datei im Ordner **Objekte** Ihres Projekts enthalten sein. Dadurch kann der Compiler die Anwendung mithilfe der *Microsoft.Identity.Client.dll*, die in den **Tutorialressourcen** enthalten ist, ordnungsgemäß erstellen.

1. Fügen Sie in **Objekte** eine neue Datei namens **link.xml** hinzu.
1. Fügen Sie der Datei Folgendes hinzu:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Speichern der Änderungen

Führen Sie die im [Schnellstart: Bereitstellen eines Unity-Beispiels in HoloLens: Erstellen des Beispielprojekts](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project) beschriebenen Schritte aus, um die Entwicklung in HoloLens durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

Der Rest dieses Tutorialsatzes enthält konzeptionelle Themen zum Erstellen einer produktionsbereiten Anwendung, die Azure Remote Rendering verwendet.

> [!div class="nextstepaction"]
> [Nächster Schritt: Kommerzielle Bereitschaft](../commercial-ready/commercial-ready.md)