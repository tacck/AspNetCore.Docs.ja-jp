## <a name="troubleshoot"></a>トラブルシューティング

### <a name="cookies-and-site-data"></a>Cookie とサイトデータ

Cookie とサイトデータは、アプリの更新間で保持され、テストやトラブルシューティングに干渉する可能性があります。 アプリコードの変更、プロバイダーによるユーザーアカウントの変更、またはプロバイダーアプリの構成の変更を行うときは、次のことをオフにします。

* ユーザーサインイン cookie
* アプリ cookie
* キャッシュおよび保存されたサイトデータ

残留 cookie とサイトデータがテストとトラブルシューティングに干渉しないようにする方法の1つは、次のとおりです。

* ブラウザーを構成する
  * ブラウザーが閉じられるたびに cookie とサイトデータをすべて削除するように構成できるテストには、ブラウザーを使用します。
  * ブラウザーが手動で閉じられていること、またはアプリ、テストユーザー、またはプロバイダーの構成を変更したときに IDE によって閉じられていることを確認します。
* カスタムコマンドを使用して、Visual Studio でブラウザーを incognito またはプライベートモードで開きます。
  * Visual Studio の [**実行**] ボタンから [**ブラウザーを使用して参照**] ダイアログボックスを開きます。
  * **[追加]** ボタンを選びます。
  * **Program**フィールドにブラウザーへのパスを指定します。 次の実行可能パスは、Windows 10 の一般的なインストール場所です。 ブラウザーが別の場所にインストールされている場合、または Windows 10 を使用していない場合は、ブラウザーの実行可能ファイルへのパスを指定します。
    * Microsoft Edge:`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome:`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox:`C:\Program Files\Mozilla Firefox\firefox.exe`
  * [**引数**] フィールドに、ブラウザーが incognito モードまたはプライベートモードで開くために使用するコマンドラインオプションを指定します。 ブラウザーによっては、アプリの URL が必要になる場合があります。
    * Microsoft Edge:`-inprivate`
    * Google Chrome:`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox:`-private -url https://localhost:5001`
  * [**フレンドリ名**] フィールドに名前を入力します。 たとえば、「 `Firefox Auth Testing` 」のように入力します。
  * **[OK]** ボタンを選択します。
  * アプリを使用したテストの各イテレーションでブラウザープロファイルを選択する必要がないようにするには、[**既定値として設定**] ボタンを使用してプロファイルを既定値として設定します。
  * アプリ、テストユーザー、またはプロバイダーの構成を変更したときに、ブラウザーが IDE によって閉じられていることを確認します。

### <a name="run-the-server-app"></a>サーバーアプリを実行する

ホステッド Blazor アプリのテストとトラブルシューティングを行うときは、**サーバー**プロジェクトからアプリを実行していることを確認してください。 たとえば、Visual Studio で、次のいずれかの方法でアプリを起動する前に、**ソリューションエクスプローラー**でサーバープロジェクトが強調表示されていることを確認します。

* **[実行]** ボタンを選択します。
* **Debug**  >  メニューから [デバッグ] [**デバッグの開始**] を使用します。
* <kbd>F5</kbd>キーを押します。

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>JSON Web トークン (JWT) の内容を検査する

JSON Web トークン (JWT) をデコードするには、Microsoft の[jwt.ms](https://jwt.ms/)ツールを使用します。 UI の値がブラウザーから離れていることはありません。
