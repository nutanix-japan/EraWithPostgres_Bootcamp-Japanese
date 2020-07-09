.. _provision_postgresdb:

--------------------------
Postgres DBのプロビジョニング
--------------------------

Overview
++++++++

初期リリースのEraでは次のOSとデータベースサーバをサポートしています。

- CentOS 6.9, 7.2, and 7.3
- Oracle Linux 7.3
- RHEL 6.9, 7.2, and 7.3
- Windows Server 2012, Windows Server 2012 R2, and Windows Server 2016
- Oracle 11.2.0.4.x, 12.1.0.2.x, and 12.2.0.1.x
- PostgreSQL 9.x and 10.x
- SQL Server 2008 R2, SQL Server 2012, SQL Server 2014, SQL Server 2016, and SQL Server 2017

Eraは登録されたNutanixクラスタ上にデータベースサーバやデータベースをプロビジョニングできます。また、クラスタ上で稼働している既存のデータベースを登録することも出来ます。

.. note::

  予想される所要時間は **30分**

このラボではPostgres Databaseのプロビジョニング、接続、表示方法をお見せします。

Exploring Era Resources
+++++++++++++++++++++++

EraはAHVやESXiに導入できる仮想アプライアンスとして提供されており、共有Eraサーバはメモリリソース節約のために既にあなたのクラスタ上にあります。

.. note::

   Eraアプライアンスに興味のある方は `こちら <https://portal.nutanix.com/#/page/docs/details?targetId=Nutanix-Era-User-Guide-v12:era-era-installing-on-ahv-t.html>`_.

#. **Prism Central > VMs > List** 内で、**IP Addresses** 列を使って **EraServer-\*** VMに割り当てられたIPアドレスを確認します。

#. \https://*ERA-VM-IP:8443*/ を新しいブラウザダブで開く

#. 以下の承認情報を使ってログインします

   - **Username** - admin
   - **Password** - nutanix/4u

#. **Dashbord** のドロップダウンメニューから **Administration** を選択します。

#. **Cluster Details** 内で、Eraがあなたに割り当てられたクラスタ用に設定されていることを確認してください。

   .. figure:: images/6.png

左側のメニューから **Era Resources** を選択

#. **Era** のドロップダウンメニューから **Profiles** を選び、左側のメニューの **Software** を選択

   .. figure:: images/3g.png

#. **PostgreSQL 10.4** と **MariaDB 10.3** はEraに同梱されています

  Era.追加のPostgreSQL、 MariaDB、 SQL Server、及びOracleプロファイルはEraでデータベースサーバVMを登録することで作成できます。

#. **Compute > DEFAULT_OOB_COMPUTE** を選択してデフォルトのConpute Profile が4コア、32GiBRAMのデータベースホスト用のVM
  を作成することを確認してください

#. **+ Create** をクリックして以下の項目を埋める:

- **Name** - *Initials*\ -Lab
- **Description** - Lab Compute Profile
- **vCPUs** - 1
- **Cores per CPU** - 2
- **Memory (GiB)** - 16

   .. figure:: images/3f2.png

#. 設定されたNetworksを確認します。もし **VLANs Available for Network Profiles** 内にNeteorksが見当たらない場合は、**Add** をクリック。 **Secondary** VLANを選択して **Add** をクリック

   .. note::

      今回はアドレス管理にクラスタのIPAMを用いるので、**Manage IP Address Pool** のチェックは外したままにします、

   .. figure:: images/era_networks_001.png

#. ドロップダウンメニューから **SLAs** を選択

   .. figure:: images/7a.png

   Eraには5つのSLAが組み込まれています(Gold, Silver, Bronze, Zero, and Brass)。SLAはデータベースサーバのバックアップ方法を制御し、継続的な保護や日毎、週毎、月毎、四半期毎の保護間隔を組み合わせることが出来ます。

#. ドロップダウンメニューから **Profiles** を選択

   プロファイルはリソースや設定を予め定義して環境の供給を一貫してシンプルにし、設定が煩雑になることを抑制します。例えば、Compute ProfilesはデータベースサーバのサイズをvCPUsやそのコア、メモリなどの詳細を考慮して決定できます。

#. **Network** 内に定義されたネットワークが無い場合、**+ Create** をクリック

   .. figure:: images/8.png

#. 以下の項目を埋めて、 **Create** をクリック

   - **Engine** - PostgreSQL
   - **Name** - Primary-PGSQL-NETWORK
   - **Public Service VLAN** - Secondary

   .. figure:: images/3f3.png

PostgreSQL Databaseのプロビジョニング
++++++++++++++++++++++++++++++++++

これでDB Server VMを用意するために必要なワンタイムオペレーションは完了しました。以下の手順に従って新しいデータベースを用意してください。Eraの適用によって必然的に最高の実践経験を得られます。

#. **Era** 内のドロップダウンメニューから **Databases** を選び、左側のメニューから **Sources** を選択

#. **+ Provision > Single Node Database** をクリック

#. Database Serverの設定のために **Provision a Database** ウィザード内で以下の項目を埋めてください

   - **Engine** - PostgresSQL
   - **Database Server** - Select **Create New Server**
   - **Database Server Name** - *Initials*\ -PostgresSQL
   - **Description** - (Optional)
   - **Software Profile** - POSTGRES_10.4_OOB
   - **Compute Profile** - *Initials*\ -Lab
   - **Network Profile** - Primary-PGSQL-NETWORK
   - **Database Time Zone** - America/Los_Angeles
   - **SSH Public Key for Node Access** - Select **Text**

   .. code-block:: text

     ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCoQRdEfm8ZJNGlYLQ2iw08eVk/Wyj0zl3M5KyqKmBTpUaS1uxj0K05HMHaUNP+AeJ63Qa2hI1RJHBJOnV7Dx28/yN7ymQpvO1jWejv/AT/yasC9ayiIT1rCrpHvEDXH9ee0NZ3Dtv91R+8kDEQaUfJLYa5X97+jPMVFC7fWK5PqZRzx+N0bh1izSf8PW0snk3t13DYovHFtlTpzVaYRec/XfgHF9j0032vQDK3svfQqCVzT02NXeEyksLbRfGJwl3UsA1ujQdPgalil0RyyWzCMIabVofz+Czq4zFDFjX+ZPQKZr94/h/6RMBRyWFY5CsUVvw8f+Rq6kW+VTYMvvkv

   .. note::

     上記のSSHキーは例としてEraから用意されているOS向けの認証キーとして提供されています。実際には自分で秘密キーと公開キーを作成してこのステップのときに提供します。

   .. figure:: images/4d2.png

#. **Next** クリック

#. 以下の **Database** の項目を埋める

   - **Database Name** - *Initials*\_LabDB
   - **Description** - (Optional) Description
   - **POSTGRES Password** - nutanix/4u
   - **Database Parameter Profile** - DEFAULT_POSTGRES_PARAMS
   - **Listener Port** - 5432
   - **Size (GiB)** - 200

   .. note::

     Eraはスクリプトやコマンドをデータベースの作成の前後に実行する機能を提供しています。この機能によって企業のニーズに合わせて環境をカスタマイズすることが出来ます。

   .. figure:: images/4e2.png

#. **Next** をクリック

#. 以下の **Time Machine** の項目を埋めてください

   - **Name** - *Initials*\_LabDB_tm
   - **Description** - (Optional) Description
   - **SLA** - DEFAULT_OOB_GOLD_SLA
   - **Schedule** - Default

   .. figure:: images/4f2.png

#. **Provision** をクリック

#. ドロップダウンメニューから **Operations** を選択して処理状況をモニターしてください。 この処理には5分程かかります

   .. note::

     Eraの全てのオペレーションは完全なログや監視のために固有のIDをあたえられています。

   .. figure:: images/4g2.png

#. 完了後、ドロップダウンメニューから **Dashboard** を選び新しい自分の **Source Database** を確認します

   .. figure:: images/4i2.png

   Prismで*Initials*\ -PostgresSQL VMが動作していることを見ることができます


Databaseへの接続
++++++++++++++++++++++++++

Eraがデータベースの用意を完了したので、実際に接続してデータベースが作成されたか確かめてみましょう。

#. **Era** 内のドロップダウンメニューから **Databases** を選択

#. **Sources** 内で自分のデータベース名を選択

   .. figure:: images/5a2.png

#. 自分の **Database Server** のIPアドレスを確認する


   .. figure:: images/5b.png

#. *Initials*\ **-WinToolsVM** を使って **pgAdmin** を開く

   .. note::

     インストールされているならpgAdminのインスタンスを使えます。ToolVMは安定した一連の操作を保証するために提供されています。

#. **Browser** 内で **Servers** を右クリックし、**Create > Server...** を選択

   .. figure:: images/5c.png

#. **General** タブで自分のデータベースサーバの名前をつけます( *Initials*-**DBServer** など

#. **Connection** タブで以下の項目を埋める

   - **Hostname/IP Address** - *Initials*\ -PostgresSQL
   - **Port** - 5432
   - **Maintenance Database** - postgres
   - **Username** - postgres
   - **Password** - nutanix/4u

   .. figure:: images/5d2.png

#. *Initials*\ **-DBServer > Databases** を展開し、Eraで作成された空のデータベースがあることを確認してください。

   .. figure:: images/5h2.png

..  Now you will create a table to store data regarding Names and Ages.

  *Initials*\_**labdb** **> Schemas > public** と展開し、**Tables** を右クリックし**Create > Table** を選択

  .. figure:: images/5e.png

  **General** タブで **Name** に **table1** と入力

  **Columns** タブで **+** をクリックし以下の項目を埋める

  - **Name** - Id
  - **Data type** - integer
  - **Primary key?** - Yes

  **+** をクリックし以下の様に項目を埋める

  - **Name** - Name
  - **Data type** - text
  - **Primary key?** - No

  **+** をクリックし以下の様に項目を埋める

  - **Name** - Age
  - **Data type** - integer
  - **Primary key?** - No

  .. figure:: images/5f.png

  **Save** をクリック

  **Tools VM** を使い、以下のリンクから、自分のデータベーステーブルに使うデータを含む.CSVファイルをダウンロード: http://ntnx.tips/EraTableData

  **pgAdmin** を使い、**table1** を右クリックして **Import/Export** を選択

  **Import/Export** ボタンを **Import** に切り替えて、以下の項目を埋める

  - **Filename** - C:\\Users\\Nutanix\\Downloads\\table1data.csv
  - **Format** - csv

  .. figure:: images/5g.png

  **OK** をクリック

  **table1** を右クリックし**View/Edit Data > All Rows** と選択するとインポートしたテータを閲覧できます

 まとめ
  +++++++++
  - Era1.0はOracle、SQL Server、PostgreSQLをサポートします。MySQLは近日サポート予定です。
  - Eraはデータベースの登録、プロビジョニング、クローン、リフレッシュの1-Clickオペレーションをサポートします
  - Eraはパブリッククラウドに期待されるのと同様のシンプルさや運用効率を持ち、データベース管理者の持続的なコントロールを可能にします。
  - Eraは複雑なデータベース運用を自動化します - データベース管理者の時間やデータベース管理のコストを削減し、企業の負担を大幅に抑えます。
  - Eraはデータベースエンジンを跨いでデータベースのプロビジョニングを標準化し、データベース運用の自動化と最適化をデータベース管理者に提供します。
