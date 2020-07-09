.. _lab_setup:

----------------------
Postgresのラボセットアップ
----------------------

Databaseブートキャンプへようこそ。
このブートキャンプではNutanixがなぜデータベースワークロードのための理想的なプラットフォームなのかを体験してもらうことを目的としています。

今までのSANベースのアーキテクチャがもたらすパフォーマンスへの影響や従来の仮想化スタックの高コストによりSQL Serverを仮想化することは課題になっていました。
企業やIT部門は、コスト、運用のシンプルさ、一貫した予測可能なパフォーマンスのバランスをとるために議論してきました。

Nutanix Enterprise Cloudはこれらの課題の多くを解決し、ビジネスクリティカルなSQL Serverのようなアプリケーションの仮想化をより簡単に実現させます。
Acropolis Distributed Storage Fabric(DSF)はSANエンタープライズに期待されるような機能を提供するソフトウェアで定義されたソリューションです。
特に、SQL ServerはDSFの機能から恩恵を受けています。

- ローカライズされたI/Oとインデックスとキーデータベースファイル用にフラッシュを使うことでオペレーションのレイテンシを低くします
- ランダムとシーケンシャルの両方のワークロードに対応できる高度に細分化されたアプローチ
- システムにダウンタイムやパフォーマンスへの悪影響を与えることなく新しいノードを追加したり基盤のスケーリングを行う機能
- Nutanixのデータ保護と災害復旧ワークフローはシンプルな操作で業務を止めないプロセス

ビジネスの肝になるアプリケーションのホスティングのための一般的なインフラの課題を解決するために、Nutanixはデータベース管理の多くの致命的な要件に対処しようとしています。

.. figure:: images/4.png

従業員数1,000人以上の北米企業500社の2018年IDC調査をもとに試算したところ…:

- 77%の組織が200以上のデータベースを実用している
- 82％の組織が全てのデータベースを10個以上にコピーしている
- 総ストレージ容量の45-60%がそのコピーデータのために使用されている
- 32％のデータベースクローンは開発と試験の解析のために毎日更新する必要があります
- コピーデータのコストは2020年には556億3000万ドルに達すると予測されます

現状を鑑みると、このままではストレージとその管理者の両方にとって悪影響であると言えます。つまり、Nutanix Eraが必要なわけです。

.. figure:: images/5.png

Nutanix Era はエンタープライズ向けのクラウドのDBaaSを提供します。
Nutanix Enterprise Cloud OSによって、データや処理能力に対して最大限の優位性を得ることができます。
Nutanix Eraはマルチプルデータベースエンジンによって、一般的なAPIのもたらすデータベース操作の複雑さをカバーし、CLIやコンシューマーグレードのGUIの体験を提供します。
また、複製などのデータベース操作をより効率的にし、お客様のTCOを軽減させます。


..  Configuring a Project
  +++++++++++++++++++++

  このラボでは前に構築したCalm Blueprintsを利用してアプリケーションの提供を試みます。

  #. **Prism Central** 内で、:fa:`bars` **> Services > Calm** を選択

  #. 左側のメニューから **Projects** を選択し、**+ Create Project** をクリック

     .. figure:: images/2.png

  #. Fill out the following fields:

  - **Project Name** - *Initials*\ -Project
  - **Users**、 **Groups**、**Roles** それぞれで **+ User** を選択
     - **Name** - Administrators
     - **Role** - Project Admin
     - **Action** - Save
  - **Infrastructure** 内で **Select Provider > Nutanix** を選択
  - **Select Clusters & Subnets** を選択
  - *Your Assigned Cluster* を選択
  - **Subnets** 内で **Primary** 、**Secondary** を選択し、**Confirm** をクリック
  - :fa:`star`をクリックして **Primary** をデフォルトのネットワークとして記録します

     .. figure:: images/3.png

  #. **Save & Configure Environment** をクリックします。

Deploying a Windows Tools VM
++++++++++++++++++++++++++++

このラボでのエクササイズのいくつかはWindows Tools VMに依存します。以下の手順に従ってディスクイメージから個人のVMを展開してください。

#. **Prism Central** 内で:fa:`bars` **> Virtual Infrastructure > VMs** を選択します。

#. **+ Create VM** をクリックします。

#. 以下に従って項目を埋める

- **Name** - *Initials*\ -WinToolsVM
- **Description** - Manually deployed Tools VM
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 4 GiB

- **+ Add New Disk** をクリック
   - **Type** - DISK
   - **Operation** - Clone from Image Service
   - **Image** - WinToolsVM.qcow2
   - **Add** を選択

- Select **Add New NIC**
   - **VLAN Name** - Secondary
   - **Add** を選択

#. **Save** をクリックしてVMを作成します。

#. *Initials*\ **-WinToolsVM** を起動します。
