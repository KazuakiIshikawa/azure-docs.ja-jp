---
title: "ポータルを使用した Azure SSIS 統合ランタイムのプロビジョニング | Microsoft Docs"
description: "この記事では、Azure Portal を使用して Azure-SSIS 統合ランタイムを作成する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: b6a795f8a26340f24f9e09aea371ba90afe50101
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Data Factory UI を使用した Azure SSIS 統合ランタイムのプロビジョニング
このチュートリアルでは、Azure Portal を使用して Azure-SSIS 統合ランタイム (IR) を Azure Data Factory にプロビジョニングする手順について説明します。 その後、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイできます。 Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事をご覧ください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成および起動する

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。


## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 
- **Azure SQL Database サーバー**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 Azure Data Factory は、このデータベース サーバーに SSIS カタログ データベース (SSISDB) を作成します。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)」を参照してください。
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。 
 
## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. [Azure Portal](https://portal.azure.com/) にログインします。    
2. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**MyAzureSsisDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
      リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 データ ファクトリの作成がサポートされている場所のみが一覧に表示されます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** をクリックして、別のタブで Data Factory ユーザー インターフェイス (UI) を起動します。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 統合ランタイムをプロビジョニングする

1. 開始ページで、**[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。 

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. **[Integration Runtime Setup]\(統合ランタイムの設定\)** の **[全般設定]** ページで、次の手順を実行します。 

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. 統合ランタイムの**名前**を指定します。
    2. 統合ランタイムの**場所**を指定します。 サポートされている場所のみが表示されます。
    3. SSIS ランタイムで構成する**ノードのサイズ**を選択します。
    4. クラスター内の**ノードの数**を指定します。
    5. **[次へ]** をクリックします。 
1. **[SQL の設定]** で、次の手順を実行します。 

    ![SQL の設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Azure SQL サーバーを保有する Azure **サブスクリプション**を指定します。 
    2. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で Azure SQL サーバーを選択します。
    3. **管理者**のユーザー名を入力します。
    4. 管理者の**パスワード**を入力します。  
    5. SSISDB データベースの**サービス レベル**を選択します。 既定値は Basic です。
    6. **[次へ]** をクリックします。 
1.  **[詳細設定]** ページで、**[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** の値を選択します。   

    ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. この手順は**省略可能**です。 クラシック仮想ネットワーク (VNet) があり、これを統合ランタイムに参加させる場合は、**[Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings]\(参加させる Azure-SSIS 統合ランタイム用の VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** オプションを選択し、次の手順を実行します。 

    ![VNet の詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. クラシック VNet を保有する**サブスクリプション**を指定します。 
    2. **VNet** を選択します。 <br/>
    4. **サブネット**を選択します。<br/> 
1. **[完了]** をクリックして、Azure-SSIS 統合ランタイムの作成を開始します。 

    > [!IMPORTANT]
    > - このプロセスは、完了するまで約 20 分かかります
    > - Data Factory サービスは、Azure SQL Database に接続して SSIS カタログ データベース (SSISDB) を準備します。 また、VNet (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS 統合ランタイムの新しいインスタンスを VNet に参加させます。
7. **[接続]** ウィンドウで、必要に応じて **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 **[最新の情報に更新]** をクリックして、状態を更新します。 

    ![作成の状態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. **[アクション]** 列のリンクを使用して、統合ランタイムを監視、停止/起動、編集、または削除します。 最後のリンクを使用すると、統合ランタイムの JSON コードを表示できます。 編集ボタンと削除ボタンは、IR が停止している場合にのみ有効になります。 

    ![Azure SSIS IR - アクション](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. **[アクション]** の **[監視]** リンクをクリックします。  

    ![Azure SSIS IR - 詳細](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Azure SSIS IR に関連する**エラー**がある場合は、エラーの数とエラーの詳細を表示するためのリンクがこのページに表示されます。 たとえば、SSIS カタログがデータベース サーバー上に既に存在する場合は、SSISDB データベースの存在を示すエラーが表示されます。  
11. 上部の **[Integration Runtimes]\(統合ランタイム\)** をクリックして前のページに戻り、データ ファクトリに関連付けられているすべての統合ランタイムを表示します。  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>ポータルでの Azure SSIS 統合ランタイム

1. Azure Data Factory UI で、**[編集]** タブに切り替えます。**[接続]** をクリックし、**[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。 
    ![既存の IR を表示する](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. **[新規]** をクリックして新しい Azure-SSIS IR を作成します。 

    ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Azure-SSIS 統合ランタイムを作成するには、図に示すように **[新規]** をクリックします。 
3. [Integration Runtime Setup]\(統合ランタイムの設定\) ウィンドウで、**[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフトアンドシフトする\)** を選択し、**[次へ]** をクリックします。

    ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

    
## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB) をホストする Azure SQL サーバーに接続します。 Azure SQL サーバーの名前は、`<servername>.database.windows.net` の形式になります (Azure SQL Database の場合)。 

SSIS ドキュメントの次の記事をご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSIS カタログへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成および起動する

次のチュートリアルに進んで、オンプレミスからクラウドにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[クラウドのデータをコピーする](tutorial-copy-data-portal.md)
