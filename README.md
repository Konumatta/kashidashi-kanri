# 貸出管理アプリ

このアプリケーションは、機器の貸出状況を管理するためのシンプルなWebアプリケーションです。Firebase (Firestore) をバックエンドとして使用し、機器とユーザーの登録、貸出、返却、および論理削除機能を提供します。

## 主な機能

* **貸出リスト**: 現在の機器の貸出状況を一覧で表示します。

* **機器管理**: 新しい機器の追加、既存機器の編集、論理削除（削除済み機器の復元も可能）を行います。

* **ユーザー管理**: 新しいユーザーの追加、既存ユーザーの編集、論理削除（削除済みユーザーの復元も可能）を行います。

* **リアルタイム同期**: Firestoreにより、データの変更がリアルタイムに反映されます。

* **レスポンシブデザイン**: Tailwind CSSを使用しており、様々なデバイスで利用可能です。

* **ダミーデータ生成**: 初回アクセス時に、テスト用のダミー機器とユーザーデータが自動的に生成されます。

## 技術スタック

* **フロントエンド**: HTML, CSS (Tailwind CSS), JavaScript

* **バックエンド/データベース**: Google Firebase (Cloud Firestore, Authentication)

## セットアップとデプロイ方法

このアプリケーションをGitHub Pagesなどの静的ホスティングサービスで動かすには、以下の手順が必要です。

### 1. Firebaseプロジェクトのセットアップ

1. **Firebaseプロジェクトの作成**:

   * [Firebase コンソール](https://console.firebase.google.com/) にアクセスし、新しいFirebaseプロジェクトを作成します。プロジェクト名は任意です（例: `LendingManager`）。

   * Googleアナリティクスの設定は任意です。

2. **Webアプリの登録**:

   * 作成したFirebaseプロジェクトのダッシュボードで、Webアプリのアイコン (`</>`) をクリックします。

   * アプリのニックネームを入力します（例: `LendingAppWeb`）。

   * 「このアプリの Firebase Hosting も設定します。」のチェックボックスに**チェックを入れて**、「アプリを登録」をクリックします。

   * Hostingサイトの選択では、デフォルトのプロジェクトID（例: `lendingmanager-e01f6`）を選択します。

3. **`firebaseConfig`の取得**:

   * アプリの登録後、「Firebase SDK の追加」画面が表示されます。

   * 「`<script> タグを使用する`」を選択します。

   * 表示されるJavaScriptコードブロックの中から、`const firebaseConfig = { ... };` の部分を**すべてコピー**します。

4. **Firestoreデータベースの作成**:

   * Firebaseコンソールの左側メニューから「**Build**」>「**Firestore Database**」に移動します。

   * 「**データベースの作成**」ボタンをクリックします。

   * セキュリティルールは「**テストモードで開始**」を選択します（開発・テスト用）。

   * ロケーションは、ユーザーに近い地域（例: `asia-northeast1 (Tokyo)`）を選択します。

   * 「有効にする」をクリックし、データベースがプロビジョニングされるのを待ちます。

5. **Firestoreセキュリティルールの設定**:

   * データベースのプロビジョニングが完了したら、Firestore Databaseの画面で「**ルール**」タブをクリックします。

   * 既存のルールをすべて削除し、以下のルールを貼り付けます。

     ```
     rules_version = '2';
     service cloud.firestore {
       match /databases/{database}/documents {
         // 認証済みユーザーのみが公開データに読み書き可能
         match /artifacts/{appId}/public/data/{collection=**} {
           allow read, write: if request.auth != null;
         }
         // ユーザー固有のプライベートデータ（現在のアプリでは未使用）
         match /artifacts/{appId}/users/{userId}/{documents=**} {
           allow read, write: if request.auth != null && request.auth.uid == userId;
         }
       }
     }
     ```

   * 右上の「**公開**」ボタンをクリックしてルールを適用します。

### 2. アプリケーションコードの更新

1. **`index.html`ファイルの準備**:

   * 提供された貸出管理アプリのHTMLコードを`index.html`という名前でファイルに保存します。

   * この`index.html`ファイルを開き、JavaScriptセクションにある以下の行を探します。

     ```
     const firebaseConfig = { /* ...既存のFirebase設定... */ };
     ```

   * この行を、上記「1. Firebaseプロジェクトのセットアップ」のステップ3でコピーした**ご自身の`firebaseConfig`オブジェクト**に置き換えます。

   * また、`appId`の定義が`firebaseConfig.projectId`から取得されることを確認してください（通常は自動でそうなっています）。

   * ファイルを保存します。

### 3. GitHub Pagesへのデプロイ

1. **GitHubリポジトリの作成**:

   * GitHubにログインし、新しいリポジトリを作成します（例: `lending-management-app`）。

   * **Visibility (公開設定)** は必ず「**Public**」（公開）を選択してください。

2. **`index.html`のアップロード**:

   * 作成したGitHubリポジトリのルートディレクトリに、更新した`index.html`ファイルをプッシュします。

3. **GitHub Pagesの有効化**:

   * GitHubリポジトリのページで「**Settings**」タブをクリックします。

   * 左側のサイドバーから「**Pages**」を選択します。

   * 「**Source**」セクションで、コードをプッシュしたブランチ（通常は`main`または`master`）と「`/(root)`」フォルダを選択し、「**Save**」をクリックします。

4. **サイトの確認**:

   * 数分待つと、GitHub Pagesがデプロイを完了し、公開されたURL（例: `https://<your-github-username>.github.io/<repository-name>/`）でアプリにアクセスできるようになります。

## 補足

* 初回アクセス時に、Firestoreにダミーデータ（機器100件、ユーザー10件）が自動的に生成されます。

* このアプリは匿名認証を使用しています。ユーザーIDはアプリ上部に表示されます。

* 「削除」機能は論理削除であり、データはFirestoreに残ります。管理画面から復元可能です。
