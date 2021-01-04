---
title: Erstellen und Suchen von Ankern in Java
description: Hier erhalten Sie detaillierte Informationen dazu, wie Sie mit Azure Spatial Anchors in Java Anker erstellen und nach ihnen suchen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.custom: devx-track-java
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7c50c1b1de2dbad4ec26b259791fed202b4990d9
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96000622"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-java"></a>Erstellen von und Suchen nach Ankern mit Azure Spatial Anchors in Java

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Spatial Anchors ermöglicht die weltweite gemeinsame Verwendung von Ankern durch verschiedene Geräte. Der Dienst unterstützt verschiedene Entwicklungsumgebungen. In diesem Artikel wird erläutert, wie das Azure Spatial Anchors SDK in Java für die folgenden Aufgaben verwendet werden kann:

- Ordnungsgemäßes Einrichten und Verwalten einer Azure Spatial Anchors-Sitzung.
- Erstellen und Festlegen von Eigenschaften für lokale Anker.
- Hochladen von Ankern in die Cloud.
- Suchen nach und Löschen von räumlichen Cloudankern.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Anleitungen ausführen können, stellen Sie sicher, dass die folgenden Bedingungen erfüllt sind:

- Sie haben die Informationen unter [Azure Spatial Anchors-Übersicht](../overview.md) gelesen.
- Sie haben eine der [fünfminütigen Schnellstartanleitungen](../index.yml) absolviert.
- Sie verfügen über Grundkenntnisse zu Java.
- Sie verfügen über Grundkenntnisse in <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a>.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Erfahren Sie mehr über die [CloudSpatialAnchorSession](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession)-Klasse.

```java
    private CloudSpatialAnchorSession mCloudSession;
    // In your view handler
    mCloudSession = new CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Erfahren Sie mehr über die [SessionConfiguration](/java/api/com.microsoft.azure.spatialanchors.sessionconfiguration)-Klasse.

```java
    mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```java
    mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Erfahren Sie mehr über die [TokenRequiredListener](/java/api/com.microsoft.azure.spatialanchors.tokenrequiredlistener)-Schnittstelle.

```java
    mCloudSession.addTokenRequiredListener(args -> {
        args.setAccessToken("MyAccessToken");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```java
    mCloudSession.addTokenRequiredListener(args -> {
        CloudSpatialAnchorSessionDeferral deferral = args.getDeferral();
        MyGetTokenAsync(myToken -> {
            if (myToken != null) args.setAccessToken(myToken);
            deferral.complete();
        });
    });
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```java
    mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```java
    mCloudSession.addTokenRequiredListener(args -> {
        args.setAuthenticationToken("MyAuthenticationToken");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```java
    mCloudSession.addTokenRequiredListener(args -> {
        CloudSpatialAnchorSessionDeferral deferral = args.getDeferral();
        MyGetTokenAsync(myToken -> {
            if (myToken != null) args.setAuthenticationToken(myToken);
            deferral.complete();
        });
    });
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

Erfahren Sie mehr über die [start](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.start)-Methode.

```java
    mCloudSession.setSession(mSession);
    mCloudSession.start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Erfahren Sie mehr über die [processFrame](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.processframe)-Methode.

```java
    mCloudSession.processFrame(mSession.update());
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Erfahren Sie mehr über die [SessionUpdatedListener](/java/api/com.microsoft.azure.spatialanchors.sessionupdatedlistener)-Schnittstelle.

```java
    mCloudSession.addSessionUpdatedListener(args -> {
        auto status = args->Status();
        if (status->UserFeedback() == SessionUserFeedback::None) return;
        NumberFormat percentFormat = NumberFormat.getPercentInstance();
        percentFormat.setMaximumFractionDigits(1);
        mFeedback = String.format("Feedback: %s - Recommend Create=%s",
            FeedbackToString(status.getUserFeedback()),
            percentFormat.format(status.getRecommendedForCreateProgress()));
    });
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Erfahren Sie mehr über die [CloudSpatialAnchor](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchor)-Klasse.

```java
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    Anchor localAnchor = null;
    List<HitResult> hitResults = mSession.update().hitTest(0.5f, 0.5f);
    for (HitResult hit : hitResults) {
        Trackable trackable = hit.getTrackable();
        if (trackable instanceof Plane) {
            if (((Plane) trackable).isPoseInPolygon(hit.getHitPose())) {
                localAnchor = hit.createAnchor();
                break;
            }
        }
    }

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.setLocalAnchor(localAnchor);
    Future createAnchorFuture = mCloudSession.createAnchorAsync(cloudAnchor);
    CheckForCompletion(createAnchorFuture, cloudAnchor);

    // ...

    private void CheckForCompletion(Future createAnchorFuture, CloudSpatialAnchor cloudAnchor) {
        new android.os.Handler().postDelayed(() -> {
            if (createAnchorFuture.isDone()) {
                try {
                    createAnchorFuture.get();
                    mFeedback = String.format("Created a cloud anchor with ID=%s", cloudAnchor.getIdentifier());
                }
                catch(InterruptedException e) {
                    mFeedback = String.format("Save Failed:%s", e.getMessage());
                }
                catch(ExecutionException e) {
                    mFeedback = String.format("Save Failed:%s", e.getMessage());
                }
            }
            else {
                CheckForCompletion(createAnchorFuture, cloudAnchor);
            }
        }, 500);
    }
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Erfahren Sie mehr über [getSessionStatusAsync](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.getsessionstatusasync)-Methode.

```java
    Future<SessionStatus> sessionStatusFuture = mCloudSession.getSessionStatusAsync();
    CheckForCompletion(sessionStatusFuture);

    // ...

    private void CheckForCompletion(Future<SessionStatus> sessionStatusFuture) {
        new android.os.Handler().postDelayed(() -> {
            if (sessionStatusFuture.isDone()) {
                try {
                    SessionStatus value = sessionStatusFuture.get();
                    if (value.getRecommendedForCreateProgress() < 1.0f) return;
                    // Issue the creation request...
                }
                catch(InterruptedException e) {
                    mFeedback = String.format("Session status error:%s", e.getMessage());
                }
                catch(ExecutionException e) {
                    mFeedback = String.format("Session status error:%s", e.getMessage());
                }
            }
            else {
                CheckForCompletion(sessionStatusFuture);
            }
        }, 500);
    }
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Erfahren Sie mehr über [getAppProperties](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchor.getappproperties)-Methode.

```java
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.setLocalAnchor(localAnchor);
    Map<String,String> properties = cloudAnchor.getAppProperties();
    properties.put("model-type", "frame");
    properties.put("label", "my latest picture");
    Future createAnchorFuture = mCloudSession.createAnchorAsync(cloudAnchor);
    // ...
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Erfahren Sie mehr über die [updateAnchorPropertiesAsync](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.updateanchorpropertiesasync)-Methode.

```java
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.getAppProperties().put("last-user-access", "just now");
    Future updateAnchorPropertiesFuture = mCloudSession.updateAnchorPropertiesAsync(anchor);
    CheckForCompletion(updateAnchorPropertiesFuture);

    // ...

    private void CheckForCompletion(Future updateAnchorPropertiesFuture) {
        new android.os.Handler().postDelayed(() -> {
            if (updateAnchorPropertiesFuture.isDone()) {
                try {
                    updateAnchorPropertiesFuture.get();
                }
                catch(InterruptedException e) {
                    mFeedback = String.format("Updating Properties Failed:%s", e.getMessage());
                }
                catch(ExecutionException e) {
                    mFeedback = String.format("Updating Properties Failed:%s", e.getMessage());
                }
            }
            else {
                CheckForCompletion1(updateAnchorPropertiesFuture);
            }
        }, 500);
    }
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Erfahren Sie mehr über die [getAnchorPropertiesAsync](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.getanchorpropertiesasync)-Methode.

```java
    Future<CloudSpatialAnchor> getAnchorPropertiesFuture = mCloudSession.getAnchorPropertiesAsync("anchorId");
    CheckForCompletion(getAnchorPropertiesFuture);

    // ...

    private void CheckForCompletion(Future<CloudSpatialAnchor> getAnchorPropertiesFuture) {
        new android.os.Handler().postDelayed(() -> {
            if (getAnchorPropertiesFuture.isDone()) {
                try {
                    CloudSpatialAnchor anchor = getAnchorPropertiesFuture.get();
                    if (anchor != null) {
                        anchor.getAppProperties().put("last-user-access", "just now");
                        Future updateAnchorPropertiesFuture = mCloudSession.updateAnchorPropertiesAsync(anchor);
                        // ...
                    }
                } catch (InterruptedException e) {
                    mFeedback = String.format("Getting Properties Failed:%s", e.getMessage());
                } catch (ExecutionException e) {
                    mFeedback = String.format("Getting Properties Failed:%s", e.getMessage());
                }
            } else {
                CheckForCompletion(getAnchorPropertiesFuture);
            }
        }, 500);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Erfahren Sie mehr über [setExpiration](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchor.setexpiration)-Methode.

```java
    Date now = new Date();
    Calendar cal = Calendar.getInstance();
    cal.setTime(now);
    cal.add(Calendar.DATE, 7);
    Date oneWeekFromNow = cal.getTime();
    cloudAnchor.setExpiration(oneWeekFromNow);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Erfahren Sie mehr über die [createWatcher](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.createwatcher)-Methode.

```java
    AnchorLocateCriteria criteria = new AnchorLocateCriteria();
    criteria.setIdentifiers(new String[] { "id1", "id2", "id3" });
    mCloudSession.createWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Erfahren Sie mehr über die [AnchorLocatedListener](/java/api/com.microsoft.azure.spatialanchors.anchorlocatedlistener)-Schnittstelle.

```java
    mCloudSession.addAnchorLocatedListener(args -> {
        switch (args.getStatus()) {
            case Located:
                CloudSpatialAnchor foundAnchor = args.getAnchor();
                // Go add your anchor to the scene...
                break;
            case AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    });
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Erfahren Sie mehr über die [deleteAnchorAsync](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.deleteanchorasync)-Methode.

```java
    Future deleteAnchorFuture = mCloudSession.deleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes (deleteAnchorFuture is done)
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Erfahren Sie mehr über die [stop](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.stop)-Methode.

```java
    mCloudSession.stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Erfahren Sie mehr über die [reset](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.reset)-Methode.

```java
    mCloudSession.reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-java.md)]

Erfahren Sie mehr über die [close](/java/api/com.microsoft.azure.spatialanchors.cloudspatialanchorsession.close)-Methode.

```java
    mCloudSession.close();
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]