---
title: Ungefähre Standortbestimmung in C++/WinRT
description: Hier erfahren Sie ausführlich, wie Sie mithilfe der ungefähren Standortbestimmung in C++/WinRT Anker erstellen und nach ihnen suchen.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9eace1ee1f97acaf04608d37c9edba482622bd59
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097370"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-cwinrt"></a>Erstellen von und Suchen nach Ankern mithilfe der ungefähren Standortbestimmung in C++/WinRT

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure Spatial Anchors kann Daten eines geräteeigenen Positionierungssensors mit den von Ihnen erstellten Ankern verknüpfen. Diese Daten können auch verwendet werden, um schnell festzustellen, ob in der Nähe Ihres Geräts irgendwelche Anker vorhanden sind. Weitere Informationen finden Sie unter [Ungefähre Standortbestimmung](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Anleitungen ausführen können, stellen Sie sicher, dass die folgenden Bedingungen erfüllt sind:

- Grundlegende Kenntnisse zu C++ und den <a href="/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Windows-Runtime-APIs</a>.
- Sie haben die Informationen unter [Azure Spatial Anchors-Übersicht](../overview.md) gelesen.
- Sie haben eine der [fünfminütigen Schnellstartanleitungen](../index.yml) absolviert.
- Sie haben die Informationen unter [Erstellen von und Suchen nach Ankern mit Azure Spatial Anchors](../create-locate-anchors-overview.md) gelesen.

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]