---
title: "Microsoft Azure StorSimple Manager Virtual Array の管理 | Microsoft Docs"
description: "Azure Portal で StorSimple デバイス マネージャー サービスを使用して、オンプレミスの StorSimple Virtual Array を管理する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: fd73672f97b4c16e49b2fad5e53042764f5793ca
ms.openlocfilehash: b1369c742c0ee1eac7638ca20598060c8542c75f

---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>StorSimple デバイス マネージャー サービスを使用して StorSimple Virtual Array を管理する
![セットアップ プロセス フロー](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Overview
この記事では、StorSimple デバイス マネージャー サービス インターフェイスについて説明します。たとえば、インターフェイスへの接続方法や使用可能な各種オプションを取り上げるほか、この UI を使用して実行できる特定のワークフローへのリンクを示します。

この記事を読むと、次の方法がわかります。

* StorSimple デバイス マネージャー サービスへの接続
* StorSimple デバイス マネージャー UI の操作
* StorSimple デバイス マネージャー サービスでの StorSimple Virtual Array の管理

> [!NOTE]
> StorSimple 8000 シリーズ デバイスで使用可能な管理オプションを確認するには、「 [StorSimple Manager サービスを使用した StorSimple デバイスの管理](storsimple-manager-service-administration.md)」を参照してください。
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスに接続する
StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple Virtual Array に接続します。 こうしたデバイスを管理するには、中央の Microsoft Azure Portal をブラウザーで実行して使用します。 StorSimple デバイス マネージャー サービスに接続するには、次の操作を行います。

#### <a name="to-connect-to-the-service"></a>サービスに接続するには
1. [https://ms.portal.azure.com](https://ms.portal.azure.com) に移動します。
2. Microsoft アカウントの資格情報を使用して、ウィンドウの右上にある Microsoft Azure クラシック ポータルにログオンします。
3. 左側のナビゲーション ウィンドウで下へスクロールして、StorSimple デバイス マネージャー サービスにアクセスします。

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>StorSimple デバイス マネージャー サービスを使用して管理タスクを実行する
次の表に、StorSimple デバイス マネージャー サービス UI 内で実行できる一般的な管理タスクと複雑なワークフローすべての概要を示します。 これらのタスクは、タスクが開始される UI ページに基づいてまとめられています。

各ワークフローの詳細については、表内の適切な手順をクリックしてください。

#### <a name="storsimple-device-manager-workflows"></a>StorSimple デバイス マネージャーのワークフロー
| 目的の操作 | 実行する手順 |
| --- | --- | --- |
| サービスを作成する</br>サービスの削除</br>サービス登録キーを取得する</br>サービス登録キーを再生成する |[StorSimple デバイス マネージャー サービスをデプロイする](storsimple-virtual-array-manage-service.md) |
| サービス データ暗号化キーを変更する</br>アクティビティ ログを表示する |[StorSimple サービスの概要を使用する](storsimple-virtual-array-service-summary.md) |
| 仮想アレイを非アクティブ化する</br>仮想アレイを削除する |[仮想アレイを非アクティブ化または削除する](storsimple-virtual-array-deactivate-and-delete-device.md) |
| 障害復旧とデバイスのフェールオーバー</br>フェールオーバーの前提条件</br>仮想デバイスへのフェールオーバー</br>ビジネス継続性障害復旧 (BCDR)</br>障害復旧時のエラー |[StorSimple Virtual Array の障害復旧とデバイスのフェールオーバー](storsimple-virtual-array-failover-dr.md) |
| 共有やボリュームをバックアップする</br>手動バックアップの取得</br>バックアップのスケジュールを変更する</br>既存のバックアップを表示する |[StorSimple Virtual Array をバックアップする](storsimple-virtual-array-backup.md) |
| バックアップ セットからの共有の復元</br>バックアップ セットからのボリュームの復元</br>項目レベルの回復 (ファイル サーバーのみ) |[StorSimple Virtual Array のバックアップから復元する](storsimple-virtual-array-clone.md) |
| ストレージ アカウントについて</br>ストレージ アカウントの追加</br>ストレージ アカウントの編集</br>ストレージ アカウントの削除 |[StorSimple Virtual Array のストレージ アカウントを管理する](storsimple-virtual-array-manage-storage-accounts.md) |
| アクセス制御レコードについて</br>アクセス制御レコードの追加または変更 </br>アクセス制御レコードの削除 |[StorSimple Virtual Array のアクセス制御レコードを管理する](storsimple-virtual-array-manage-acrs.md) |
| ジョブの詳細を表示する |[StorSimple Virtual Array ジョブを管理する](storsimple-virtual-array-manage-jobs.md) |
| アラート設定を構成する</br>アラート通知を受け取る</br>Manage alerts</br>アラートを確認する |[StorSimple Virtual Array のアラートを表示し、管理する](storsimple-virtual-array-manage-alerts.md) |
| デバイス管理者のパスワードを変更する |[StorSimple Virtual Array デバイス管理者パスワードを変更する](storsimple-virtual-array-change-device-admin-password.md) |
| ソフトウェアの更新プログラムをインストールする |[Virtual Array を更新する](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> 次のタスクには、 [ローカル Web UI](storsimple-ova-web-ui-admin.md) を使用する必要があります。
> 
> * [サービス データ暗号化キーを取得する](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [サポート パッケージを作成する](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [仮想アレイを停止し、再起動する](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>次のステップ
Web UI とその使用方法については、「 [StorSimple Web UI を使用した StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)」をご覧ください。




<!--HONumber=Nov16_HO4-->


