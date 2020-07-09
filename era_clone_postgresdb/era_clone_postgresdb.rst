.. _clone_postgresdb:

----------------------
Postgres DBのクローン
----------------------

Overview
++++++++

.. note::

  所要時間は30分の予定

このラボではデータベースのクローンとリフレッシュについてお見せします。

Cloning Your PostgreSQL Source
++++++++++++++++++++++++++++++

ソースデータベースを作成したので、Era Time Machineを使用して簡単にデータベースをクローンすることできます。
データベースのクローンは、開発とテストのために役立ち、検証環境などで商用環境に影響を与えることなく商用環境と同じデータを利用できます。
EraのクローンはNutanixネイティブのリダイレクトオンライトテクノロジーを利用しており、ゼロバイトのデータベースクローンが可能です。
このスペース効率により、多数のデータベースクローンをサポートする環境のストレージコストを大幅に削減できます。

#. 自分のデータベースを **Era** の **Time Machines** から選択してみましましょう(*Initials*\_LabDB_tm)

   .. figure:: images/16a2.png

#. **Actions** > **Snapshot** と選択し、**First** で **Snapshot Name** を選択します

   .. figure:: images/17a.png

#. **Create** をクリック

#. 進行状況をモニターするためにドロップダウンメニューの **Operations** を選択してください。 この処理には5分ほどかかります

#. スナップショット作業が完了したら、 **Era > Time Machines** からソースデータベース(*Initials*\_LabDB_tm)を選択し、**Actions** > **Clone Database** をクリックします

#. **Time** タブから **Snapshot > First** を選択

   .. note::

   Eraは、手動でスナップショットを作成せずに、継続的（毎秒）、毎日、毎週、毎月、または四半期を含むポイントインタイムの増分に基づいてデータベースをクローンする機能も提供します。
   可用性はソースのSLAによって制御されます。

   .. figure:: images/19a2.png

#. **Next** をクリック

#. **Database Server** タブで以下の項目を埋める

   - **Database Server** - Create New Server
   - **Database Server Name** - *Initials*-PostgresSQL_Clone
   - **Compute Profile** - *Initials*\ -Lab
   - **Network Profile** - Primary-PGSQL-NETWORK
   - **SSH Public Key** -

   .. code-block:: text

     ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCoQRdEfm8ZJNGlYLQ2iw08eVk/Wyj0zl3M5KyqKmBTpUaS1uxj0K05HMHaUNP+AeJ63Qa2hI1RJHBJOnV7Dx28/yN7ymQpvO1jWejv/AT/yasC9ayiIT1rCrpHvEDXH9ee0NZ3Dtv91R+8kDEQaUfJLYa5X97+jPMVFC7fWK5PqZRzx+N0bh1izSf8PW0snk3t13DYovHFtlTpzVaYRec/XfgHF9j0032vQDK3svfQqCVzT02NXeEyksLbRfGJwl3UsA1ujQdPgalil0RyyWzCMIabVofz+Czq4zFDFjX+ZPQKZr94/h/6RMBRyWFY5CsUVvw8f+Rq6kW+VTYMvvkv

   .. figure:: images/20a2.png

#. **Next** をクリック

#. **Database Server** タブで以下の項目を埋める

   - **Name** - *Initials*\_LabDB_Clone
   - **Description** - (Optional) Description
   - **Password** - nutanix/4u
   - **Database Parameter Profile** - DEFAULT_POSTGRES_PARAMS

   .. figure:: images/21a2.png

#. **Clone** をクリック

#. ドロップダウンメニューから **Operations** を選択し進行状況をモニターしてください。 この処理には5-10分分かかります

#. クローンしている間に、**Era > SLAs** を見てEraが提供する標準的なSLAと自分でカスタマイズしたSLAの違いを理解してください

   .. figure:: images/21b.png

#. クローンが完了したら、その作成したクローンに接続できます

   .. figure:: images/23a2.png

   新しいクローンが利用可能です

クローンされたデータベースのリフレッシュ
++++++++++++++++++++++++++++

開発の中で新しいデータを使ってデータベースのクローンを簡単に更新するためには、検証や他の関連する新しいデータへのアクセスを確実にすることで改善することができます。この章ではソースデータベースの新しいテーブルの格納とそのクローンの更新をします。

#. **pgAdmin** でソースデータベース(クローンでない)を選択し、メニューバーから **Tools > Query Tool** とクリックします(PGAdminを最大化するとToolMenuをが見やすいかもしれません)

   .. figure:: images/25a2.png

#. **Query Tool** から以下のSQLコマンドをエディタに入力します

   .. code-block:: postgresql
     :name: products-table-sql

     CREATE TABLE products (
     product_no integer,
     name text,
     price numeric
     );

#. :fa:`bolt` **Execute/Refresh** をクリック

   .. figure:: images/26a.png

#. **Schemas > Public > Tables > products** からテーブルが作成されたことを確認します

   .. note::

     新しく作成したテーブルを表示するには **Table** の更新が必要です

   .. figure:: images/27a2.png

   前回は手動でデータベースのスナップショットを作成しましたが、今回はEraの **Point in Time** 機能を使って更新します

   デフォルトの **Log Catch Up** 用のスケジュールでは、ソースデータベースが用意されたときから30分毎と設定されています。スケジュールに従って過去30分毎にアップデートされたデータベースを基にクローンを更新します、これ以上のアクションは不要です。

   この場合、ソースデータベースの**products**テーブルが作成されただけなので、ソースデータベースからEraにトランザクションログをコピーするには**Log Catch Up**を実行する必要があります。

#. **Era** で **Time Machines** を選び、ソースデータベースのTime Machineインスタンスを選択して **Actions** > **Log Catch Up > Yes** をクリックします

   .. figure:: images/27c.png

#. ドロップダウンメニューから **Operations** を選択して処理状況をモニターします。 この処理には5-10分かかります

#. **Log Catchuo**処理が完了したら、**Databases > Clones** を選択し、ソースデータベースを選んで **Refresh** を選択する

   .. figure:: images/27b2.png

#. デフォルトでは使用可能な最新の **Point in Time** に更新されます。**Refresh** をクリック

   .. figure:: images/27d.png

#. ドロップダウンメニューから **Operations** を選択して処理状況をモニターします。 この処理には5-10分かかります

#. **Refresh Clone** 処理が完了したら、 **pgAdmin** で自分のクローンデータベースの **Tables** の表示を更新して、**products** テーブルが存在することを確認します

   .. figure:: images/28a2.png

   数回のクリックと数分の所要時間で最新の実データを使ったクローンデータベースの更新が出来ました。これはスナップショットや復元ポイントから失ったデータを復元させるためにも使えるアプローチです。

#. **Dashboard** に戻って管理者向けの重要情報、ストレージの節約情報、クローンの世代管理、タスク、アラートなどを確認してください。

   .. figure:: images/28b2.png

まとめ
+++++++++

- Eraはデータベースの登録、プロビジョニング、クローン作成、およびリフレッシュのための1-Click操作をサポートしています。
- Eraはデータベース管理者がコントロール性を維持しながら、パブリッククラウドに期待するのと同様のシンプルさと運用の効率性を実現します。
- Eraは複雑なデータベース操作を自動化します。従来のテクノロジーでは課題になっていたデータベースを管理するためのデータベース管理者の時間とコストの両方を削減し、エンタープライズOpExを大幅に節約します。
- Eraを使用するとデータベース管理者はデータベースエンジン全体でデータベースのデプロイメントを標準化し、データベースのベストプラクティスを自動的に組み込むことができます。
- Eraは、データベース管理者がアプリケーション整合性のあるトランザクション環境をクローンできるようにします。
