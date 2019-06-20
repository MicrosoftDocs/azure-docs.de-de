---
title: Erstellen von und Suchen nach Ankern mit Azure Spatial Anchors in Unity | Microsoft-Dokumentation
description: Hier erhalten Sie detaillierte Informationen dazu, wie Sie mit Azure Spatial Anchors in Unity Anker erstellen und nach ihnen suchen.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5dc86aff7324b79f2c1b7a14b349337fc26a1901
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244312"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-unity"></a>Erstellen von und Suchen nach Ankern mit Azure Spatial Anchors in Unity

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Spatial Anchors ermöglicht die weltweite gemeinsame Verwendung von Ankern durch verschiedene Geräte. Der Dienst unterstützt verschiedene Entwicklungsumgebungen. In diesem Artikel wird erläutert, wie das Azure Spatial Anchors SDK in Unity für die folgenden Aufgaben verwendet werden kann:

- Ordnungsgemäßes Einrichten und Verwalten einer Azure Spatial Anchors-Sitzung.
- Erstellen und Festlegen von Eigenschaften für lokale Anker.
- Hochladen von Ankern in die Cloud.
- Suchen nach und Löschen von räumlichen Cloudankern.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Anleitungen ausführen können, stellen Sie sicher, dass die folgenden Bedingungen erfüllt sind:

- Sie haben die Informationen unter [Azure Spatial Anchors-Übersicht](../overview.md) gelesen.
- Sie haben eine der [fünfminütigen Schnellstartanleitungen](../index.yml) absolviert.
- Grundlegende Kenntnisse zu C# und Unity.
- Grundlegende Kenntnisse zu <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> (bei Verwendung von Android) oder <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> (bei Verwendung von iOS).

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Erfahren Sie mehr über die [CloudSpatialAnchorSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession)-Klasse.

```csharp
    CloudSpatialAnchorSession cloudSession;
    // In your view handler
    this.cloudSession = new CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Erfahren Sie mehr über die [SessionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionconfiguration)-Klasse.

```csharp
    this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```csharp
    this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Erfahren Sie mehr über den [TokenRequiredDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.tokenrequireddelegate)-Delegaten.

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AccessToken = @"MyAccessToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AccessToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```csharp
    this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AuthenticationToken = @"MyAuthenticationToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AuthenticationToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

Erfahren Sie mehr über die [Start](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.start)-Methode.

```csharp
#if UNITY_IOS
    this.arkitSession = UnityARSessionNativeInterface.GetARSessionNativeInterface();
    this.cloudSession.Session = this.arkitSession.GetNativeSessionPtr();
#elif UNITY_ANDROID
    this.nativeSession = GoogleARCoreInternal.ARCoreAndroidLifecycleManager.Instance.NativeSession;
    this.cloudSession.Session = this.nativeSession.SessionHandle;
#elif UNITY_WSA || WINDOWS_UWP
    // No need to set a native session pointer for HoloLens.
#else
    throw new NotSupportedException("The platform is not supported.");
#endif

    this.cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Erfahren Sie mehr über die [ProcessFrame](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.processframe)-Methode.

```csharp
#if UNITY_ANDROID
    long latestFrameTimeStamp = this.nativeSession.FrameApi.GetTimestamp();
    bool newFrameToProcess = latestFrameTimeStamp > this.lastFrameProcessedTimeStamp;
    if (newFrameToProcess)
    {
        this.cloudSession.ProcessFrame(this.nativeSession.FrameHandle);
        this.lastFrameProcessedTimeStamp = latestFrameTimeStamp;
    }
#endif
#if UNITY_IOS
    UnityARSessionNativeInterface.ARFrameUpdatedEvent += UnityARSessionNativeInterface_ARFrameUpdatedEvent;

    void UnityARSessionNativeInterface_ARFrameUpdatedEvent(UnityARCamera camera)
    {
        this.cloudSession.ProcessFrame(this.arkitSession.GetNativeFramePtr());
    }
#endif
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Erfahren Sie mehr über den [SessionUpdatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionupdateddelegate)-Delegaten.

```csharp
    this.cloudSession.SessionUpdated += (object sender, SessionUpdatedEventArgs args)
    {
        var status = args.Status;
        if (status.UserFeedback == SessionUserFeedback.None) return;
        this.feedback = $"Feedback: {Enum.GetName(typeof(SessionUserFeedback), status.UserFeedback)} -" +
            $" Recommend Create={status.RecommendedForCreateProgress: 0.#%}";
    };
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Erfahren Sie mehr über die [CloudSpatialAnchor](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor)-Klasse.

```csharp
    // Create a local anchor, perhaps by hit-testing and spawning an object within the scene
    Vector3 hitPosition = new Vector3();
#if UNITY_IOS
    var screenPosition = Camera.main.ScreenToViewportPoint(new Vector3(0.5f, 0.5f));
    ARPoint point = new ARPoint
    {
        x = screenPosition.x,
        y = screenPosition.y
    };
    var hitResults = UnityARSessionNativeInterface.GetARSessionNativeInterface().HitTest(point, ARHitTestResultType.ARHitTestResultTypeEstimatedHorizontalPlane | ARHitTestResultType.ARHitTestResultTypeExistingPlaneUsingExtent);
    if (hitResults.Count > 0)
    {
        // The hitTest method sorts the resulting list by increasing distance from the camera
        // The first hit result will usually be the most relevant when responding to user input
        ARHitTestResult hitResult = hitResults[0];
        hitPosition = UnityARMatrixOps.GetPosition(hitResult.worldTransform);
    }
#elif UNITY_ANDROID
    TrackableHit hit;
    TrackableHitFlags raycastFilter = TrackableHitFlags.PlaneWithinPolygon | TrackableHitFlags.FeaturePointWithSurfaceNormal;
    if (Frame.Raycast(0.5f, 0.5f, raycastFilter, out hit))
    {
        hitPosition = hit.Pose.position;
    }
#elif WINDOWS_UWP || UNITY_WSA
    RaycastHit hit;
    if (this.TryGazeHitTest(out hit))
    {
        hitPosition = hit.point;
    }
#endif

    Quaternion rotation = Quaternion.AngleAxis(0, Vector3.up);
    this.localAnchor = GameObject.Instantiate(/* some prefab */, hitPosition, rotation);
    this.localAnchor.AddARAnchor();

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.LocalAnchor = this.localAnchor.GetNativeAnchorPointer();
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
    this.feedback = $"Created a cloud anchor with ID={cloudAnchor.Identifier}");
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Erfahren Sie mehr über [GetSessionStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getsessionstatusasync)-Methode.

```csharp
    SessionStatus value = await this.cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Erfahren Sie mehr über die [AppProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.appproperties)-Eigenschaft.

```csharp
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor() { LocalAnchor = localAnchor };
    cloudAnchor.AppProperties[@"model-type"] = @"frame";
    cloudAnchor.AppProperties[@"label"] = @"my latest picture";
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Erfahren Sie mehr über die [UpdateAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.updateanchorpropertiesasync)-Methode.

```csharp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties[@"last-user-access"] = @"just now";
    await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Erfahren Sie mehr über die [GetAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getanchorpropertiesasync)-Methode.

```csharp
    var anchor = await cloudSession.GetAnchorPropertiesAsync(@"anchorId");
    if (anchor != nullptr)
    {
        anchor.AppProperties[@"last-user-access"] = @"just now";
        await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Erfahren Sie mehr über die [Expiration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.expiration)-Eigenschaft.

```csharp
    cloudAnchor.Expiration = DateTimeOffset.Now.AddDays(7);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Erfahren Sie mehr über die [CreateWatcher](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.createwatcher)-Methode.

```csharp
    AnchorLocateCriteria criteria = new AnchorLocateCriteria();
    criteria.Identifiers = new string[] { @"id1", @"id2", @"id3" };
    this.cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Erfahren Sie mehr über den [AnchorLocatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocateddelegate)-Delegaten.

```csharp
    this.cloudSession.AnchorLocated += (object sender, AnchorLocatedEventArgs args) =>
    {
        switch (args.Status)
        {
            case LocateAnchorStatus.Located:
                CloudSpatialAnchor foundAnchor = args.Anchor;
                // Go add your anchor to the scene...
                break;
            case LocateAnchorStatus.AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus.NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus.NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Erfahren Sie mehr über die [DeleteAnchorAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.deleteanchorasync)-Methode.

```csharp
    await this.cloudSession..DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Erfahren Sie mehr über die [Stop](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.stop)-Methode.

```csharp
    this.cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Erfahren Sie mehr über die [Reset](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.reset)-Methode.

```csharp
    this.cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-unity.md)]

Erfahren Sie mehr über die [Dispose](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.dispose)-Methode.

```csharp
    this.cloudSession.Dispose();
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
