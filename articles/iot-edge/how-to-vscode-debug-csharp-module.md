---
title: "Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする | Microsoft Docs"
description: "VS Code で Azure IoT Edge を使用して C# モジュールをデバッグします。"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 01d321dce439e153b494dfd0de52c100dab78f39
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする
この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、IoT Edge モジュールをデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

このガイダンスを開始する前に、次のチュートリアルを完了していることを確認してください。
- [Visual Studio Code を使用して Azure IoT Edge で C# モジュールを開発する](how-to-vscode-develop-csharp-module.md)

上記のチュートリアルを終了したら、次のものを準備してください。
- 開発用マシンで実行されているローカル Docker レジストリ。 プロトタイプ作成とテストのために、ローカル Docker レジストリを使用することをお勧めします。
- 最新のフィルター モジュール コードが含まれた `Program.cs` ファイル。
- センサー モジュールとフィルター モジュールの最新の `deployment.json` ファイル。
- 開発用マシンで実行されている Edge ランタイム。

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>デバッグのために IoT Edge モジュールをビルドする
1. デバッグを開始するには、**dockerfile.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度配置する必要があります。 VS Code エクスプローラーで、Docker フォルダーをクリックして開きます。 次に、`linux-x64` フォルダーをクリックし、**Dockerfile.debug** を右クリックして、**[Build IoT Edge module Docker image]\(IoT Edge モジュール Docker イメージのビルド\)** をクリックします。
3. **[フォルダーの選択]** ウィンドウで、`./bin/Debug/netcoreapp2.0/publish` を参照するか入力します。 **[Select Folder as EXE_DIR] (EXE_DIR としてフォルダーを選択)** をクリックします。
4. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、「 `<your container registry address>/filtermodule:latest`」のように入力します。 ローカル レジストリにデプロイする場合は、`localhost:5000/filtermodule:latest` のようになります。
5. イメージを Docker リポジトリにプッシュします。 **[Edge: Push IoT Edge module Docker image]\(Edge: IoT Edge モジュール Docker イメージをプッシュ\)** コマンドを使用し、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージの URL を入力します。 前の手順で使用したイメージの URL を使用してください。
6. `deployment.json` を再利用して再デプロイできます。 コマンド パレットで、「 **Edge: Restart Edge\(Edge: Edge の再起動\)**」と入力して選択し、フィルター モジュールをデバッグ バージョンで実行します。

## <a name="start-debugging-in-vs-code"></a>VS Code でデバッグを開始する
1. VS Code デバッグ ウィンドウに移動します。 **F5** キーを押し、**[IoT Edge(.Net Core)]** を選択します。
2. `launch.json` で、**Debug IoT Edge Custom Module (.NET Core)** セクションに移動し、`pipeArgs` の下に `<container_name>` を入力します。このチュートリアルでは、`filtermodule` になります。
3. Program.cs に移動します。 `method static async Task<MessageResponse> FilterModule(Message message, object userContext)` にブレークポイントを追加します。
4. もう一度 **F5** キーを押します。 アタッチするプロセスを選択します。 このチュートリアルでは、プロセス名は `FilterModule.dll` になります。
5. VS Code デバッグ ウィンドウの左側のパネルに変数が表示されます。 

> [!NOTE]
> 上記の例は、コンテナー上の .Net Core IoT Edge モジュールをデバッグする方法を示しています。 これは、コンテナー イメージに VSDBG (.NET Core コマンド ライン デバッガー) が含まれ、イメージをビルドする、デバッグ バージョンの `Dockerfile.debug` に基づいています。 C# モジュールのデバッグを終了したら、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge モジュールを作成してデバッグ用にデプロイし、VS Code でデバッグを開始しました。 次のチュートリアルに進み、VS Code で Azure IoT Edge を開発するときの他のシナリオを確認できます。 

> [!div class="nextstepaction"]
> [VS Code での C# モジュールの開発とデプロイ](how-to-vscode-develop-csharp-module.md)
