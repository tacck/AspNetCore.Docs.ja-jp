## <a name="troubleshoot"></a>トラブルシューティング

### <a name="cookies-and-site-data"></a>Cookie とサイト データ

Cookie とサイト データは、アプリが更新されても保持され、テストやトラブルシューティングに影響する可能性があります。 アプリ コードの変更、プロバイダーによるユーザー アカウントの変更、プロバイダー アプリの構成変更を行うときは、次のものをクリアしてください。

* ユーザーのサインインの Cookie
* アプリの Cookie
* キャッシュおよび保存されたサイト データ

残った Cookie とサイト データがテストとトラブルシューティングに影響しないようにする方法を、次に示します。

* ブラウザーを構成する
  * ブラウザーが閉じるたびに Cookie とサイト データをすべて削除するように構成できることをテストするために、ブラウザーを使用します。
  * アプリ、テスト ユーザー、プロバイダー構成が変更されるたびにブラウザーが手動で、または IDE によって閉じられていることを確認します。
* カスタム コマンドを使用して、Visual Studio でブラウザーをシークレットまたはプライベート モードで開く。
  * Visual Studio の **[実行]** ボタンをクリックして **[ブラウザーの選択]** ダイアログボックスを開きます。
  * **[追加]** ボタンを選びます。
  * **[プログラム]** フィールドでブラウザーのパスを指定します。 次の実行可能パスが、Windows 10 の一般的なインストール場所です。 ブラウザーが別の場所にインストールされている場合、または Windows 10 を使用していない場合は、ブラウザーの実行可能ファイルのパスを指定してください。
    * Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`
  * **[引数]** フィールドに、ブラウザーをシークレットまたはプライベート モードで開くために使用するコマンドライン オプションを指定します。 ブラウザーによっては、アプリの URL が必要になる場合があります。
    * Microsoft Edge: `-inprivate`
    * Google Chrome: `--incognito --new-window https://localhost:5001`
    * Mozilla Firefox: `-private -url https://localhost:5001`
  * **[フレンドリ名]** フィールドに名前を指定します。 たとえば、`Firefox Auth Testing` のようにします。
  * **[OK]** ボタンを選択します。
  * アプリでテストを繰り返すたびにブラウザー プロファイルを選択する必要がないようにするには、 **[既定値として設定]** ボタンでプロファイルを既定値として設定します。
  * アプリ、テスト ユーザー、プロバイダー構成が変更されるたびにブラウザーが IDE によって閉じられていることを確認します。

### <a name="run-the-server-app"></a>Server アプリを実行する

ホストされている Blazor アプリのテストとトラブルシューティングを行うときは、 **`Server`** プロジェクトからアプリを実行していることを確認してください。 たとえば、Visual Studio で、次のいずれかの方法を使用してアプリを起動する前に、Server プロジェクトが**ソリューション エクスプローラー**で強調表示されていることを確認します。

* **[実行]** ボタンを選択します。
* メニューの、 **[デバッグ]**  >  **[デバッグ開始]** を使用します。
* <kbd>F5</kbd>キーを押します。

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>JSON Web トークン (JWT) の内容を検査する

JSON Web トークン (JWT) をデコードするには、Microsoft の [jwt.ms](https://jwt.ms/) ツールを使用します。 UI の値がブラウザーに残ることはありません。
