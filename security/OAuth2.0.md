# OAuth 2.0

## 一番分かりやすい大まかな流れ解説
https://qiita.com/TakahikoKawasaki/items/e37caf50776e00e733be
- OAuth 2.0はアクセストークンの要求とその応答を標準化したもの(RFC 6749)
- OAuthを用いて **認可** (authorization)を行う．(認証(authentication)とは異なる)

### 補足: 認可(authorization; AuthZ, AuthR)と認証(authentication; AuthN)との違い
- 認証(authentication): 誰であるか(who one is)を扱う
    - 本人確認
    - 誰なのかを特定する処理
    - 認証とは，ユーザーの一意識別子を特定する処理
- 認可(authorization): 誰が誰に何の権限を与えるか(who grants what permissions to whom)を扱う
    - 「誰が」(ユーザーが)，「誰に」(クライアントに)，「何の権限を」
    - 「誰が」は認証処理である

OAuthは認証に流用することができる
- ユーザーのIDとパスワードの管理を外部サービスに任せることができる
    - e.g. ゲームのアカウントをGoogleアカウントで作成する(Sign in with Google)
    - ユーザー登録の手間がなくなる
- **警戒**: クライアントが外部サービスへのアクセス権を入手することになる
    - アクセスしてきたユーザーが本当にアクセス先のユーザーかどうか知りえない(カット&ペーストアタック)
        - 得たアクセストークンを用いて，本来のユーザーが認知できない場所(別のクライアント)からアクセス
    - OpenID Connectに対応することで解決する

## OAuth 2.0 解説
用語
- リソースサーバー: 保護対象のリソースを保持しているサーバー
- リソースオーナー: 保護対象のリソースを所有している人，エンドユーザー
- クライアント: リソースにアクセスするソフトウエア(サービス，アプリ)
- 認可サーバー: リソースサーバーに信頼された，アクセストークンを発行するサーバー

処理
1. 認可リクエスト: クライアントから認可サーバーへリダイレクト to 認可エンドポイント w/ パラメータ
    - `client_id`: どのクライアントが認可を要求しているかの識別に用いる
    - `redirect_url`: ユーザーが連携を許可した後にリダイレクトされるURL．事前に設定されたURLと一致させる必要がある
    - `scope`: どのアクセスの認可を要求するか
2. 認可サーバーでの確認
    - ユーザーはスコープを確認し，問題なければパスワードなどを用いて認証する
    - 認可エンドポイント -> 認可画面 -> 認可決定エンドポイント
3. 認可コードの発行
    - アクセストークンを発行するための認可コードを発行し，クライアントへリダイレクト
    - 認可コードが外部に漏れないように，リダイレクトは事前に設定されたURLのみ可能
4. アクセストークンの発行
    - 認可コードを認可サーバー(トークンエンドポイント)に渡して，アクセストークンを取得
    - POSTコール
5. リソースサーバーへアクセス
    - アクセストークンを用いてリソースにアクセス

RFC 6749(The OAuth 2.0 Authorization Framework)で定義されている4つの認可フロー & アクセストークンの再発行
1. 認可コードフロー (4.1. Authorization Code Grant)
    - 上の処理と同じ．
    - なぜリダイレクトで直接アクセストークンを渡さず認可コードを介すのか?
        - リダイレクトを盗聴された場合，認可コードは短命なため比較的安全(よくわからん)
            - 何のためのリダイレクト先の設定? リダイレクトはそもそもなりすましされないように
        - 認可コードフローでは，アクセストークンはWebAPIのJSONで帰ってくる(?)
        - インプリシットフローでは，アクセストークンはフラグメント部に置かれる
        - この差で盗聴のしやすさが変わるらしい?
2. インプリシットフロー (4.2. Implicit Grant)
    - 認可コードを介さず直接アクセストークンを受け取る
    - URLのフラグメント部にアクセスできるwebアプリ(JS)のためにある．
    - 認可コードフローを使いましょう
3. リソースオーナー・パスワード・クレデンシャルズフロー (4.3. Resource Owner Password Credentials Grant)
    - 認証リクエストにログインIDとパスワードを含めている
    - アクセストークンを直接受け取る
    - 使ってはいけない，移行中など一時的な使用にとどめる
4. クライアント・クレデンシャルズフロー (4.4. Client Credentials Grant)
    - ユーザー認証無し，クライアントアプリケーションの認証のみ
    - アクセストークンを直接受け取る
    - ユーザーに紐づいたアクセス出なければOK
5. リフレッシュトークンフロー (6. Refreshing an Access Token)
    - 事前に発行されているリフレッシュトークンをトークンエンドポイントに提示しアクセストークンを再発行してもらう

## 参考文献
- https://zenn.dev/mryhryki/articles/2020-12-28-oauth2-flow
- https://qiita.com/TakahikoKawasaki/items/200951e5b5929f840a1f
- https://logmi.jp/tech/articles/322829 (認可コードフローとインプリシットフローの差について)
