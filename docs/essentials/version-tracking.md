---
title: 'Xamarin.Essentials: バージョン追跡'
description: Xamarin.Essentials の VersionTracking クラスを使用すると、アプリケーションのバージョンとビルド番号を確認できるだけでなく、アプリケーションの初めての起動か現在のバージョンかや、前回のビルドの情報などの追加情報を見ることができます。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1eae0bf7c21dd7efa7655633896bdb2897f9d782
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674861"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: バージョン追跡

![プレリリースの NuGet](~/media/shared/pre-release.png)

**VersionTracking** クラスを使用すると、アプリケーションのバージョンとビルド番号を確認できるだけでなく、今まで一度も起動されたことのないアプリケーションの初めての起動か、現在のバージョンの起動か、前回のビルドの情報などの追加情報を見ることができます。

## <a name="get-started"></a>作業開始

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>VersionTracking の使用

自分のクラスの Xamarin.Essentials に参照を追加します。

```csharp
using Xamarin.Essentials;
```

**VersionTracking** クラスを初めて使用すると、最初に現在のバージョンが追跡されます。 アプリケーションが読み込まれるたびにのみ早く `Track` を呼び出して現在のバージョン情報が追跡されるようにする必要があります。

```csharp
VersionTracking.Track();
```

最初に `Track` を呼びした後は、次のバージョン情報を読み取ることができます。

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>プラットフォームの実装の詳細

すべてのバージョン情報は Xamarin.Essentials の [Preferences](preferences.md) API を使用して格納され、**<アプリ パッケージ ID>.xamarinessentials.versiontracking** というファイル名で保存され、「[ユーザー設定](preferences.md#persistence)」で説明されているのと同じデータ永続化に従います。

## <a name="api"></a>API

- [VersionTracking のソース コード](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [VersionTracking API のドキュメント](xref:Xamarin.Essentials.VersionTracking)
