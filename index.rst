.. title:: Databases: Era with Postgres Bootcamp

.. toctree::
  :maxdepth: 2
  :caption: Era with Postgres
  :name: _era_postgres
  :hidden:

  labsetup/labsetup
  era_provision_postgresdb/era_provision_postgresdb
  era_clone_postgresdb/era_clone_postgresdb

.. toctree::
  :maxdepth: 2
  :caption: Optional Labs
  :name: _optional_labs
  :hidden:

  era_rest_api/era_rest_api

.. toctree::
  :maxdepth: 2
  :caption: Appendix
  :name: _appendix
  :hidden:

  tools_vms/windows_tools_vm
  tools_vms/linux_tools_vm
  appendix/glossary

.. _getting_started:

---------------
Getting Started
---------------

データベース: Era with Postgresブートキャンプにようこそ！
このワークブックはインストラクター主導のセッションがあり、Nutanixのテクノロジーとたくさんのよくある管理タスクをご紹介します。


What's New
++++++++++

- ワークショップは以下のソフトウェアバージョンで動作します:
    - AOS 5.11.x / 5.15.x / 5.16.x
    - PC 5.16.x

- オプションラボ アップデート:

アジェンダ
++++++

- イントロダクション
- ラボセットアップ
- Postgres DBのプロビジョニング
- Postgres DBのクローン

オプションラボ:

- Era API Explorer

イントロダクション
+++++++++++++

- 名前
- Nutanixの習熟度

初期セットアップ
+++++++++++++

- パスワードがあること注意してください。
- 仮想デスクトップにログインします。（ログイン情報は下記にあります）

環境の詳細
+++++++++++++++++++

Nutanixワークショップは、Nutanix Hosted POC環境で実行することを目的としています。 演習を完了するために必要なすべてのイメージ、ネットワーク、VMがクラスターにプロビジョニングされます。

ネットワーク
..........

HPOCクラスターは標準の命名規則に従います:

- **クラスター名** - POC\ *XYZ*
- **サブネット** - 10.**21**.\ *XYZ*\ .0
- **クラスタIP** - 10.**21**.\ *XYZ*\ .37

マーケティングプールからプロビジョンした場合は下記になります:

- **クラスター名** - MKT\ *XYZ*
- **サブネットSubnet** - 10.**20**.\ *XYZ*\ .0
- **クラスタIP** - 10.**20**.\ *XYZ*\ .37

例:

- **クラスター名** - POC055
- **サブネット** - 10.21.55.0
- **クラスターIP** - 10.21.55.37

ワークショップ全体を通じて、たとえば、次のように* XYZ *を正しいオクテットに置き換える必要がある場合がいくつかあります:

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - IP Address
     - Description
   * - 10.21.\ *XYZ*\ .37
     - Nutanix Cluster Virtual IP
   * - 10.21.\ *XYZ*\ .39
     - **PC** VM IP, Prism Central
   * - 10.21.\ *XYZ*\ .40
     - **DC** VM IP, NTNXLAB.local Domain Controller

各クラスターはVMに使用できる2つのVLANが構成されています:

.. list-table::
  :widths: 25 25 10 40
  :header-rows: 1

  * - Network Name
    - Address
    - VLAN
    - DHCP Scope
  * - Primary
    - 10.21.\ *XYZ*\ .1/25
    - 0
    - 10.21.\ *XYZ*\ .50-10.21.\ *XYZ*\ .124
  * - Secondary
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .132-10.21.\ *XYZ*\ .253

認証情報
...........

.. note::

  <クラスタパスワード> *は各クラスタに固有であり、インストラクターによって提供されます。

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - Credential
     - Username
     - Password
   * - Prism Element
     - admin
     - *<Cluster Password>*
   * - Prism Central
     - admin
     - *<Cluster Password>*
   * - Controller VM
     - nutanix
     - *<Cluster Password>*
   * - Prism Central VM
     - nutanix
     - *<Cluster Password>*

各クラスターには、**NTNXLAB.local**ドメインにADサービスを提供する専用のドメインコントローラーVM **AutoAD**があります。
ドメインには次のユーザーとグループが入力されています:

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - グループ
     - ユーザ名
     - パスワード
   * - Administrators
     - Administrator
     - nutanix/4u
   * - SSP Admins
     - adminuser01-adminuser25
     - nutanix/4u
   * - SSP Developers
     - devuser01-devuser25
     - nutanix/4u
   * - SSP Consumers
     - consumer01-consumer25
     - nutanix/4u
   * - SSP Operators
     - operator01-operator25
     - nutanix/4u
   * - SSP Custom
     - custom01-custom25
     - nutanix/4u
   * - Bootcamp Users
     - user01-user25
     - nutanix/4u

アクセス手順
+++++++++++++++++++

NutanixのHPOC環境には次の方法でアクセスできます:

ラボアクセスユーザー情報
...........................

PHX クラスタ:
**Username:** PHX-POCxxx-User01 〜 PHX-POCxxx-User20, **Password:** *<インストラクターから提供>*

RTP クラスタ:
**Username:** RTP-POCxxx-User01 〜 RTP-POCxxx-User20, **Password:** *<インストラクターから提供>*

Frame VDI
.........

Login to: https://frame.nutanix.com/x/labs

**Nutanix社員** - Use your **NUTANIXDC** credentials
**その他のユーザー** - Use **Lab Access User** Credentials

Parallels VDI
.................

PHX Based Clusters Login to: https://xld-uswest1.nutanix.com

RTP Based Clusters Login to: https://xld-useast1.nutanix.com

**Nutanix社員** - Use your **NUTANIXDC** credentials
**その他のユーザー** - Use **Lab Access User** Credentials

Employee Pulse Secure VPN
..........................

クライアントソフトウェアのダウンロードが必要:

PHX Based Clusters Login to: https://xld-uswest1.nutanix.com

RTP Based Clusters Login to: https://xld-useast1.nutanix.com

**Nutanix社員** - Use your **NUTANIXDC** credentials
**その他のユーザー** - Use **Lab Access User** Credentials

クライアントソフトウェアのインストール.

Pulse Secure Clientの中で接続先を **追加** してください:

For PHX:

- **Type** - Policy Secure (UAC) or Connection Server
- **Name** - X-Labs - PHX
- **Server URL** - xlv-uswest1.nutanix.com

For RTP:

- **Type** - Policy Secure (UAC) or Connection Server
- **Name** - X-Labs - RTP
- **Server URL** - xlv-useast1.nutanix.com


Nutanixソフトウェアバージョン情報
++++++++++++++++++++

- **AHV Version** - AHV 20170830.337
- **AOS Version** - 5.11.x
- **PC Version** - 5.16.1.2
