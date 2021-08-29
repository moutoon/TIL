# トレイトについてソースを追いかけてみた

`トレイト`を、ログイン認証のソースコードで追いかけてみました。  
<br>
## web.php
```php
Route::get('login', 'Auth\LoginController@showLoginForm')->name('login');
```
(訳)ドメイン以下の`login`にGETリクエスト(アクセス)があったら`Auth\LoginController`内の`showLoginForm`にルーティングせよ。  
<br>
## Auth\LoginController
「先生！`Auth\LoginController`の中に`showLoginForm`がありまへん！」  
不思議ですね。アクションがないのにも関わらず、たしかにログイン機能は使えています。どうしてでしょうか。   

```php
(前略)

use Illuminate\Foundation\Auth\AuthenticatesUsers;

class LoginController extends Controller
{
  use AuthenticatesUsers; // ←が良い仕事をしている

(攻略)
```  

「use トレイト名(ここでは`AuthenticatesUsers`)」を記述するだけで、指定したトレイト名に定義されているメソッドをそのクラスが定義しているのと同じように利用できるんですね。  
ちなみにこれはPHPの機能です(5.4.0から実装)  
では`AuthenticatesUsers`はどこに定義されているのでしょうか。  
追いかけてみましょう。  
<br>
## src/vendor/laracel/framework/src/illuminate/Foundation/Auth/AuthenticatesUsers.php

「深い！めっちゃ階層が深い！まるでマリアナ海溝みたいや…！」  
闇の領域である`vendor`以下に来てしまったようです。闇のゲーム始まってしまいそうですね。  

```php
<?php

namespace Illuminate\Foundation\Auth;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Validation\ValidationException;

trait AuthenticatesUsers
{
    use RedirectsUsers, ThrottlesLogins;

    /**
     * Show the application's login form.
     *
     * @return \Illuminate\Http\Response
     */
    public function showLoginForm()
    {
        return view('auth.login');
    }
    
(後略)
```  

やっと`showLoginForm`を見つけました。  
この記述されている内容`return view('auth.login');`のおかげでログイン画面に遷移ができるわけです。  
さて、プレイヤーにダイレクトアタックをされてしまう前にファイルは閉じてしまいましょう。  
（vendorはいじってはいけません。ダメ、ゼッタイ！）  
<br>
## さいごに
さて、いかがでしたでしょうか。  
いろいろなことを良しなにやってくれるLaravelの深淵を覗けたような気がしますね。  
まだまだLaravelを触り始めて間もないですが、とてもおもしろかったです。  
それでは、明日も良いプログラミングライフを！
