---
title: APK 拡張ファイル
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 38568fa9258c7e3de2c3333cdca5dc7d5867319c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117103"
---
# <a name="apk-expansion-files"></a>APK 拡張ファイル

一部のアプリケーション (一部のゲームなど) は、Google Play で課される Android アプリのサイズ上限を超えるリソースやアセットを必要とします。 この上限は、ご利用の APK が対象とする Android のバージョンによって異なります。

-  Android 4.0 以降 (API レベル 14 以上) を対象とする APK の場合は 100MB です。
-  Android 3.2 以前 (API レベル 13 以上) を対象とする APK の場合は 50MB です。

この制限を取り払う目的で、Google Play は 2 つの*拡張ファイル*を提供、配信します。このファイルは APK と連動し、アプリケーションがこの制限を間接的に超過することを可能にします。 

ほとんどのデバイスで、アプリケーションがインストールされるとき、APK と共に拡張ファイルがダウンロードされ、デバイスの共有ストレージ (SD カードや USB マウント可能パーティション) に保存されます。 一部の古いデバイスでは、APK と共に拡張ファイルが自動的にインストールされないことがあります。 そのような場合、アプリケーションを初めて実行したときに拡張ファイルをダウンロードするコードをアプリケーションに含める必要があります。

拡張ファイルは *OBB (Opaque Binary Blob)* として処理され、そのサイズは最大 2GB です。 Android はダウンロード後のこれらのファイルに特別な処理を行いません。アプリケーションに適した任意の形式のファイルを利用できます。 概念的には、拡張ファイルに推奨される手法は次のようになります。

-   **メイン拡張** &ndash; このファイルは、APK サイズ制限に収まらないリソースやアセットのための主要な拡張ファイルです。 メイン拡張ファイルには、アプリケーションにとって必須であり、めったに更新しない主要なアセットを含めます。
-   **パッチ拡張** &ndash; これは、メイン拡張ファイルの小規模な更新を意図するものです。 このファイルは更新できます。 このファイルから必要なパッチや更新を行うのはアプリケーションの担当です。


拡張ファイルは、APK のアップロードと同時にアップロードする必要があります。
Google Play では、拡張ファイルを既存の APK にアップロードしたり、既存の APK を更新したりできません。 既存のファイルを更新する必要がある場合、`versionCode` を更新し、新しい APK をアップロードする必要があります。


## <a name="expansion-file-storage"></a>拡張ファイル ストレージ

ファイルをデバイスにダウンロードすると、**_shared-store_/Android/obb/_package-name_** に保存されます。

-   **_shared-store_** &ndash; `Android.OS.Environment.ExternalStorageDirectory` によって指定されるディレクトリです。
-   **_package-name_** &ndash; アプリケーションの Java スタイルのパッケージ名です。


ダウンロード後、拡張ファイルはデバイス上の保存先から移動したり、作り替えたり、名前を変更したり、削除しないでください。 そのような行為を行った場合、拡張ファイルが再びダウンロードされ、古いファイルが削除されます。 また、拡張ファイル ディレクトリには、拡張パック ファイルのみを含めます。

拡張ファイルでは、そのコンテンツに何のセキュリティも保護も適用されません。共有ストレージに保存されているファイルには他のアプリケーションやユーザーがアクセスできます。

拡張ファイルを解凍する必要がある場合、解凍したパックは別のディレクトリに保存してください。たとえば、 `Android.OS.Environment.ExternalStorageDirectory` の別ディレクトリに保存します。

拡張ファイルのファイル抽出に代わる方法は、拡張ファイルから直接、アセットやリソースを読み取ることです。 拡張ファイルは、適切な `ContentProvider` と併用できる zip ファイルです。 [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) にはアセンブリの [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip) が含まれています。このアセンブリには、一部のメディア ファイルの直接ファイル アクセスを可能にする `ContentProvider` が含まれています。 メディア ファイルが zip ファイルにパッケージ化されている場合、メディア再生呼び出しでは、その zip ファイルを解凍することなく、zip のファイルを直接利用できることがあります。 zip ファイルに追加するとき、メディア ファイルを圧縮しないでください。 


### <a name="filename-format"></a>FileName 形式

拡張ファイルをダウンロードするとき、Google Play は次の方式で拡張に名前を付けます。

    [main|patch].<expansion-version>.<package-name>.obb

この方式の 3 つの構成要素は次のとおりです。

-   `main` または `patch` &ndash; メイン拡張ファイルなのか、パッチ拡張ファイルなのかを指定します。 いずれかになります。
-   `<expansion-version>` &ndash; これは、ファイルが最初に関連付けられた APK の `versionCode` に一致する整数です。
-   `<package-name>` &ndash; これはアプリケーションの Java スタイルのパッケージ名です。


たとえば、APK バージョンが 21 で、パッケージ名が `mono.samples.helloworld` であれば、メイン拡張ファイルには **main.21.mono.samples.helloworld** と名前が付けられます。


## <a name="download-process"></a>ダウンロード プロセス

アプリケーションを Google Play からインストールすると、拡張ファイルがダウンロードされ、APK と共に保存されます。 状況によっては、拡張ファイルがダウンロードされなかったり、拡張ファイルが削除されたりします。 その場合、アプリは拡張ファイルの存在を確認し、必要に応じてダウンロードする必要があります。 次のフローチャートは、このプロセスの推奨ワークフローを示すものです。

[![APK 拡張フローチャート](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

起動時に、アプリケーションは現在のデバイスにしかるべき拡張ファイルが存在することを確認します。 存在しない場合、アプリケーションは Google Play の[アプリケーション ライセンス](http://developer.android.com/google/play/licensing/index.html)に要求する必要があります。 この確認は *LVL (License Verification Library)* によって行われます。無料のアプリケーションにも、ライセンス購入アプリケーションにも行います。 LVL は主に、ライセンス制限を適用する目的で有料アプリケーションによって使用されます。 ただし、Google では LVL が拡張されており、拡張ライブラリでも使用できます。 無料のアプリケーションは LVL 確認を実行する必要がありますが、ライセンス制限は無視できます。 LVL 要求は、アプリケーションが必要とする拡張ファイルに関する次の情報を提供する役割を担っています。 

-   **ファイル サイズ** &ndash; 拡張ファイルのファイル サイズは、正しい拡張ファイルが既にダウンロードされているかどうかを判断するための確認で利用されます。
-   **ファイル名** &ndash; これは拡張パックを保存する現在のデバイスでのファイル名です。
-   **ダウンロード用の URL** &ndash; 拡張パックのダウンロードに利用する URL です。 ダウンロードごとに一意となっており、提供後、短期間で有効期限が切れます。


LVL 確認後、アプリケーションは拡張ファイルをダウンロードします。その際、ダウンロードの一環として、次の点が考慮されます。

-  拡張ファイルを保存するための十分な領域がデバイスにない可能性があります。
-  Wi-Fi が利用できない場合、望まないデータ料金の発生を防ぐためにダウンロードの一時停止またはキャンセルをユーザーに許可します。
-  ユーザー操作の邪魔にならないように、拡張ファイルはバックグラウンドでダウンロードされます。
-  ダウンロードがバックグラウンドで進行している間、進捗状況インジケーターを表示します。
-  ダウンロード中に発生したエラーは回復可能であり、正常に処理されます。



## <a name="architectural-overview"></a>アーキテクチャの概要

メイン アクティビティが開始すると、拡張ファイルのダウンロードが確認されます。 ファイルがダウンロードされている場合、有効性を確認する必要があります。

拡張ファイルがダウンロードされていない場合、あるいは現在のファイルが無効になっている場合、新しい拡張ファイルをダウンロードする必要があります。 制限付きサービスがアプリケーションの一部として作成されます。 アプリケーションのメイン アクティビティが開始すると、制限付きサービスを利用して Google Licensing サービスに対して照合を行い、ダウンロードするファイルの拡張ファイル名と URL を見つけます。 制限付きサービスはバックグラウンド スレッドでファイルをダウンロードします。

拡張ファイルをアプリケーションに統合するために必要な労力を減らす目的で、Google はいくつかのライブラリを Java で作成しました。 そのライブラリは次のとおりです。

-   **ダウンローダー ライブラリ** &ndash; これは、アプリケーションで拡張ファイルを統合するために必要な労力を減らすライブラリです。 このライブラリはバックグラウンド サービスで拡張ファイルをダウンロードしたり、ユーザー通知を処理したり、ネットワーク接続問題を処理したり、ダウンロードを再開したりします。
-   **LVL (License Verification Library)** &ndash; アプリケーション ライセンス サービスを呼び出し、処理するためのライブラリです。 ライセンス確認にも利用できます。アプリケーションをデバイスで使用する許可を得ているか確認できます。
-   **APK 拡張 Zip ライブラリ (オプション)** &ndash; 拡張ファイルが zip ファイルの場合、このライブラリはコンテンツ プロバイダーとして機能し、zip ファイルを展開しなくても、zip ファイルから直接、リソースやアセットを読み取ることをアプリケーションに許可します。


これらのライブラリは C# に移植されており、Apache 2.0 ライセンスの下で利用できます。 拡張ファイルを既存のアプリケーションが手早く統合する目的で、ライブラリを既存の Xamarin.Android アプリケーションに追加できます。 このコードは GitHub の [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) にあります。
