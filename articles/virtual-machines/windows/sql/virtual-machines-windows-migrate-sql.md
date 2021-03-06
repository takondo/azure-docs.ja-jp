---
title: "VM の SQL Server への SQL Server データベースの移行 | Microsoft Docs"
description: "オンプレミスのユーザー データベースを Azure 仮想マシンの SQL Server に移行する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: sabotta
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: carlasab
translationtype: Human Translation
ms.sourcegitcommit: 0367b10864d5eea06f9dd676e36cce3402840dff
ms.openlocfilehash: bb3f60994df3562e20664c1378f45137936b059c


---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Azure VM の SQL Server への SQL Server データベースの移行
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Resource Manager モデル。

オンプレミスの SQL Server ユーザー データベースを Azure VM の SQL Server に移行する方法は多数あります。 この記事では、さまざまな方法について簡単に説明したうえで、さまざまなシナリオに最適な方法を紹介します。また、**Microsoft Azure VM への SQL Server データベースのデプロイ** ウィザードを使用する方法について順を追って説明する[チュートリアル](#azure-vm-deployment-wizard-tutorial)も含まれています。 

**チュートリアル** で説明する [Microsoft Azure VM への SQL Server Database のデプロイ](#azure-vm-deployment-wizard-tutorial) ウィザードを使用する方法は、クラシック デプロイ モデル専用です。 

## <a name="what-are-the-primary-migration-methods"></a>主な移行方法
主な移行方法は次のとおりです。

* Microsoft Azure VM への SQL Server Database のデプロイ ウィザードを使用する
* 圧縮機能を使用してオンプレミスのバックアップの実行し、そのバックアップ ファイルを Azure 仮想マシンに手動でコピーする
* URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する
* データとログ ファイルをデタッチしてから、Azure BLOB ストレージにコピーし、その後、URL から Azure VM の SQL Server にアタッチする
* オンプレミスの物理マシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい VM としてデプロイする
* Windows の Import/Export サービスを使用して、ハード ドライブを発送する
* オンプレミスの AlwaysOn デプロイがある場合は、 [Azure のレプリカ追加ウィザード](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) を使用して Azure でレプリカを作成した後、フェールオーバーして、ユーザーに Azure データベース インスタンスを参照させる
* SQL Server の [トランザクション レプリケーション](https://msdn.microsoft.com/library/ms151176.aspx) を使用して Azure SQL Server インスタンスをサブスクライバーとして構成した後、レプリケーションを無効にして、ユーザーに Azure データベース インスタンスを参照させる

## <a name="choosing-your-migration-method"></a>移行方法の選択
最適なデータ転送パフォーマンスを実現するために、Azure VM へのデータベース ファイルの移行は、一般的には圧縮されたバックアップ ファイルを使って行うのがベストです。 この方法は、 [Microsoft Azure VM への SQL Server データベースのデプロイ ウィザード](#azure-vm-deployment-wizard-tutorial) で使用されます。 このウィザードは、圧縮されたデータベースのバックアップ ファイルが 1 TB より小さいとき、SQL Server 2005 以降で実行されているオンプレミスのユーザー データベースを、SQL Server 2014 以降に移行する際に使用することをお勧めします。

データベース移行プロセス中のダウンタイムを最小限に抑えるには、AlwaysOn の方法またはトランザクション レプリケーションの方法を使います。

上記の方法を使用できない場合は、手動でデータベースを移行します。 この方法を使うときは、一般に、まずデータベースをバックアップしてから、データベースのバックアップを Azure にコピーした後、データベースの復元を実行します。 データベース ファイル自体を Azure にコピーしてからアタッチしてもかまいません。 この手動による Azure VM へのデータベースの移行プロセスは、複数の方法で実行できます。

> [!NOTE]
> 以前のバージョンの SQL Server から SQL Server 2014 または SQL Server 2016 にアップグレードする場合は、変更が必要かどうかを検討する必要があります。 移行プロジェクトの一環として、新しいバージョンの SQL Server でサポートされない機能への依存関係すべてを解決することをお勧めします。 サポートされるエディションとシナリオの詳細については、 [SQL Server へのアップグレード](https://msdn.microsoft.com/library/bb677622.aspx)に関するページを参照してください。
> 
> 

次の表に、主な移行方法の一覧を示します。ここでは、それぞれの方法を使用するタイミングとして最適な状況についても説明します。

| メソッド | 移行元データベースのバージョン | 移行先データベースのバージョン | 移行元データベースのバックアップ サイズ制限 | メモ |
| --- | --- | --- | --- | --- |
| [Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードを使用する](#azure-vm-deployment-wizard-tutorial) |SQL Server 2005 以降 |SQL Server 2014 以降 |1 TB まで |最も高速で簡単な方法。Azure 仮想マシンの新規または既存の SQL Server インスタンスに移行するときにいつでも使用できます。 |
| [Azure レプリカの追加ウィザードを使用する](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 以降 |SQL Server 2012 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |ダウンタイムが最小限になります。AlwaysOn のオンプレミスのデプロイがあるときに使います。 |
| [SQL Server のトランザクション レプリケーションを使用する](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |ダウンタイムを最小限にする必要があり、AlwaysOn のオンプレミスのデプロイがないときに使います。 |
| [圧縮機能を使用してオンプレミスのバックアップの実行し、そのバックアップ ファイルを Azure 仮想マシンに手動でコピーする](#backup-to-file-and-copy-to-vm-and-restore) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |ウィザードを使用できない場合にのみ使用。たとえば、移行先データベースのバージョンが SQL Server 2012 SP1 CU2 より以前の場合や、データベースのバックアップ サイズが 1 TB (SQL Server 2016 の場合は 12.8 TB) を超えている場合は、ウィザードを使用できません。 |
| [URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 以上 |SQL Server 2012 SP1 CU2 以上 |SQL Server 2016 の場合は 12.8 TBまで、それ以外の場合は 1 TB まで |一般的に、 [URL へのバックアップ](https://msdn.microsoft.com/library/dn435916.aspx) は、パフォーマンスについてはウィザードを使用する場合と同等で、それほど簡単ではありません。 |
| [データとログ ファイルをデタッチしてから、Azure BLOB ストレージにコピーし、その後、URL から Azure 仮想マシンの SQL Server にアタッチする](#detach-and-copy-to-url-and-attach-from-url) |SQL Server 2005 以降 |SQL Server 2014 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |この方法は、特にデータベースのサイズが非常に大きい場合に、 [Azure BLOB ストレージ サービスを使用してこれらのファイルを格納](https://msdn.microsoft.com/library/dn385720.aspx) し、Azure VM で実行されている SQL Server にファイルをアタッチするときに使用します。 |
| [オンプレミスのマシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい仮想マシンをデプロイする](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |[ご自身の SQL Server ライセンスを使用する](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)場合、古いバージョンの SQL Server で実行するデータベースを移行する場合、または他のユーザー データベースやシステム データベースに応じて、データベースの移行の一環として、データベース システムとユーザー データベースを一緒に移行する場合に使用します。 |
| [Windows の Import/Export サービスを使用して、ハード ドライブを発送する](#ship-hard-drive) |SQL Server 2005 以降 |SQL Server 2005 以降 |[Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |[Windows の Import/Export サービス](../../../storage/storage-import-export-service.md) は、データベースのサイズが非常に大きい場合など、手動でのコピーが遅すぎるときに使用します。 |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Azure VM のデプロイ ウィザードのチュートリアル
Microsoft SQL Server Management Studio の " **Microsoft Azure VM への SQL Server データベースのデプロイ** ウィザード" を使用して、SQL Server 2005、SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、または SQL Server 2016 のオンプレミスのユーザー データベース (最大 1 TB) を、Azure 仮想マシンの SQL Server 2014 または SQL Server 2016 に移行します。 このウィザードを使用すると、ユーザー データベースが、既存の Azure 仮想マシン、または移行プロセス中にウィザードによって作成された SQL Server が含まれる Azure VM のいずれかに移行されます。 データベースを新しいバージョンの SQL Server に移行すると、そのデータベースは、プロセス中に自動的にアップグレードされます。

この方法は、クラシック デプロイ モデル専用です。 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>最新の Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードを入手する
最新の **Microsoft Azure VM への SQL Server データベースのデプロイ** ウィザードを確実に入手するには、最新の Microsoft SQL Server Management Studio for SQL Server を使用します。 このウィザードの最新バージョンには、Azure クラシック ポータルの最新の更新プログラムが組み込まれており、ギャラリーの最新の Azure VM イメージがサポートされます (以前のバージョンのウィザードは機能しないことがあります)。 最新の Microsoft SQL Server Management Studio for SQL Server を入手するには、 [これをダウンロード](http://go.microsoft.com/fwlink/?LinkId=616025) して、移行するデータベースとインターネットに接続されているクライアント コンピューターにインストールします。

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>既存の Azure 仮想マシンと SQL Server インスタンスを構成する (該当する場合)
既存の Azure VM に移行するには、次の構成手順を実行します。

* [別のコンピューター上の SSMS から SQL Server VM インスタンスに接続する](virtual-machines-windows-sql-connect.md)手順に従って、Azure VM と SQL Server インスタンスを構成し、別のコンピューターからの接続を有効にします。 ウィザードを使用して移行する場合、サポートされるのは、ギャラリーの SQL Server 2014 および SQL Server 2016 のイメージのみです。
* Microsoft Azure ゲートウェイの SQL Server クラウド アダプター サービスのオープン エンドポイントを、プライベート ポート 11435 で構成します。 このポートは、Microsoft Azure VM における SQL Server 2014 または SQL Server 2016 のプロビジョニングの一環として作成されます。 また、クラウド アダプターでは、既定のポート 11435 での着信 TCP 接続を許可する Windows ファイアウォール ルールも作成されます。 このエンドポイントにより、ウィザードがクラウド アダプター サービスを利用して、バックアップ ファイルをオンプレミスのインスタンスから Azure VMにコピーできます。 詳細については、「 [SQL Server のクラウド アダプター](https://msdn.microsoft.com/library/dn169301.aspx)」をご覧ください。
  
    ![クラウド アダプターのエンドポイントの作成](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードを実行する
1. Microsoft SQL Server Management Studio for Microsoft SQL Server 2016 を開き、Azure VMに移行するユーザー データベースを含む SQL Server のインスタンスに接続します。
2. 移行するデータベースを右クリックし、[タスク] をポイントして、[Microsoft Azure VM にデプロイする] をクリックします。
   
    ![ウィザードの開始](./media/virtual-machines-windows-migrate-sql/start-wizard.png)
3. [説明] ページで [次へ] をクリックします。
4. [ソース設定] ページで、Azure VM に移行するデータベースを含む SQL Server インスタンスに接続します。
5. バックアップ ファイルの一時的な場所を指定します。 リモート サーバーに接続する場合は、ネットワーク ドライブを指定する必要があります。
   
    ![移行元の設定](./media/virtual-machines-windows-migrate-sql/source-settings.png)
6. [次へ] をクリックします。
7. [Microsoft Azure ログイン] ページで、[ログイン] をクリックし、Azure アカウントにログインします。
8. 使用するサブスクリプションを選択し、[次へ] をクリックします。
   
    ![Azure のサインイン](./media/virtual-machines-windows-migrate-sql/azure-signin.png)
9. [デプロイの設定] ページでは、新規または既存のクラウド サービス名と仮想マシン名を指定できます。
   
   * 新しいクラウド サービス名と仮想マシン名を指定し、SQL Server 2014 ギャラリーまたは SQL Server 2016 ギャラリーのイメージを使用して、新しい Azure 仮想マシンで新しいクラウド サービスを作成します。
     
     * 新しいクラウド サービス名を指定する場合は、使用するストレージ アカウントを指定します。
     * 既存のクラウド サービス名を指定する場合は、ストレージ アカウントが取得され、入力されます。
   * 既存のクラウド サービス名と新しい仮想マシン名を指定して、既存のクラウド サービスで新しい Azure 仮想マシンを作成します。 SQL Server 2014 ギャラリーまたは SQL Server 2016 ギャラリーのイメージのみを指定します。
   * 既存のクラウド サービス名と仮想マシン名を指定して、既存の Azure 仮想マシンを使用します。 これには、SQL Server 2014 ギャラリーまたは SQL Server 2016 ギャラリーのイメージを使用して作成されたイメージが必要です。
     
        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)
10. Click Settings
    
    * 既存のクラウド サービス名と仮想マシン名を指定した場合は、ユーザー名とパスワードを入力するように求められます。
      
        ![Azure のマシン設定](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)
    * 新しい仮想マシン名を指定した場合は、ギャラリーのイメージの一覧からイメージを選択し、次の情報を入力するように求められます。
      
      * イメージ - SQL Server 2014 または SQL Server 2016 のみを選択
        * ユーザー名
        * 新しいパスワード
        * パスワードの確認
        * 場所
        * サイズ
      * また、[この新しい Microsoft Azure 仮想マシンに対して自動生成された証明書を使用する] チェック ボックスをオンにして、[OK] をクリックします。
    
    ![Azure の新しいコンピューターの設定](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)
11. 移行先データベース名を指定します (移行元データベース名と異なる場合)。 移行先データベースが既に存在する場合、既存のデータベースは上書きされず、データベース名が自動的にインクリメントされます。
12. [次へ] をクリックし、[完了] をクリックします。
    
    ![結果](./media/virtual-machines-windows-migrate-sql/results.png)
13. ウィザードが完了したら、仮想マシンに接続し、データベースが移行されていることを確認します。
14. 新しい仮想マシンを作成した場合は、 [別のコンピューター上の SSMS から SQL Server VM インスタンスに接続する](virtual-machines-windows-sql-connect.md)手順に従って、Azure 仮想マシンと SQL Server インスタンスを構成します。

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>ファイルへのバックアップと VM へのコピーおよび復元
SQL Server 2014 より前の SQL Server に移行する場合、またはバックアップ ファイルが 1 TB を超える場合、Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードは使用できません。この方法は、このような場合に使用します。 VM ディスクの最大サイズは 1 TB であるため、バックアップ ファイルが 1 TB を超える場合、そのファイルはストライプする必要があります。 この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1. オンプレミスの場所へのデータベースの完全バックアップを実行します。
2. 必要な SQL Server バージョンを使用して、仮想マシンを作成またはアップロードします。
3. 要件に基づいて接続をセットアップします。 「 [Connect to a SQL Server Virtual Machine on Azure (Resource Manager) (Azure での SQL Server 仮想マシンへの接続 (Resource Manager))](virtual-machines-windows-sql-connect.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
4. リモート デスクトップ、Windows エクスプローラー、またはコマンド プロンプトのコピー コマンドを使用して、バックアップ ファイルを VM にコピーします。

## <a name="backup-to-url-and-restore"></a>URL へのバックアップと復元
バックアップ ファイルが 1 TB を超える場合、SQL Server 2016 から移行する場合、および SQL Server 2016 に移行する場合、Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードは使用できません。[URL へのバックアップ](https://msdn.microsoft.com/library/dn435916.aspx)方法は、このような場合に使用します。 データベースが 1 TB 未満の場合、または SQL Server 2016 より前のバージョンの SQL Server を実行している場合は、ウィザードを使用することをお勧めします。 SQL Server 2016 では、ストライプ バックアップ セットがサポートされています。このバックアップ セットは、パフォーマンスを確保するために使用することが推奨されます。また、BLOB ごとのサイズ制限を超える場合は必須です。 データベースのサイズが非常に大きい場合は、[Windows の Import/Export サービス](../../../storage/storage-import-export-service.md)をお勧めします。

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>デタッチして URL をコピーし、URL からアタッチ
この方法は、特にデータベースのサイズが非常に大きい場合に、 [Azure BLOB ストレージ サービスを使用してこれらのファイルを格納](https://msdn.microsoft.com/library/dn385720.aspx) し、Azure VM で実行されている SQL Server にファイルをアタッチするときに使用します。 この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1. オンプレミスのデータベース インスタンスからデータベース ファイルをデタッチします。
2. デタッチされたデータベース ファイルを、 [AZCopy コマンド ライン ユーティリティ](../../../storage/storage-use-azcopy.md)を使用して Azure BLOB ストレージにコピーします。
3. データベース ファイルを、Azure URL から Azure VM の SQL Server のインスタンスにアタッチします。

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>VM に変換して URL にアップロードし、新しい VM としてデプロイ
この方法は、オンプレミスの SQL Server インスタンスのすべてのシステムとユーザー データベースを、Azure 仮想マシンに移行するときに使用します。 この手動による方法を使用して SQL Server インスタンス全体を移行するには、次の一般的な手順を実行します。

1. [Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx)を使用して、物理マシンまたは仮想マシンを HYPER-V VHD に変換します。
2. [Add-AzureVHD コマンドレット](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)を使用して、VHD ファイルを Azure Storage にアップロードします。
3. アップロードした VHD を使用して、新しい仮想マシンをデプロイします。

> [!NOTE]
> アプリケーション全体を移行するには、 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)を使用することを検討します。
> 
> 

## <a name="ship-hard-drive"></a>ハード ドライブの発送
ネットワーク経由のアップロードが実現不可能であるか、非常にコストがかかる場合には、[Windows の Import/Export サービス方法](../../../storage/storage-import-export-service.md)を使用して、ファイルの大量のデータを Azure BLOB ストレージに転送します。 このサービスでは、そのデータを含む 1 台以上のハード ドライブを Azure データ センターに発送します。このデータ センターでデータがストレージ アカウントにアップロードされます。

## <a name="next-steps"></a>次のステップ
Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

Azure SQL Server 仮想マシンをキャプチャ イメージから作成する手順については、CSS SQL Server Engineers のブログ「[Tips & Tricks on ‘cloning’ Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/)」 (Azure SQL 仮想マシンをキャプチャ イメージから複製するためのヒント) を参照してください。




<!--HONumber=Jan17_HO2-->


