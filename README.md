# 営業管理アプリ v0.3（Public GitHub 対応）

## 構造
- `index.html`: 静的プログラムのみ。施設名簿、訪問履歴、顧客情報、Google OAuthクライアントID、Google Sheets IDを **埋め込まない**。
- 施設マスター（1031件）: 非公開Google Sheets「営業管理_施設マスター_非公開」（タブ `施設マスター`）。
- AI選定/訪問履歴: 非公開Google Sheets「営業管理_AI連携ハブ_八戸」（タブ `AI選定`, `訪問履歴`）。
- 訪問記録: ブラウザの localStorage に下書きを保存し、Google OAuth後に専用JSONバックアップ＋Sheetsの訪問履歴へ同期。
- 毎朝のChatGPT AI選定はWebアプリ外の自動タスク。HTML自身がChatGPTを自動実行するわけではありません。

## 初回接続 (必須)
1. HTMLをHTTPSでホスティングし、Google Cloudで Drive API と Sheets API を有効化する。
2. Google Cloud OAuth「Webアプリケーション」を作成し、承認済みJavaScript生成元へ `https://<公開URLのホスト>` を登録する。
3. OAuth同意画面のテストユーザーに利用者Googleアカウントを追加する。公開範囲・検証要件はGoogle Cloud画面で確認。
4. アプリ「設定・保存」で `OAuthウェブクライアントID`、`AI選定連携ハブID`、`非公開施設マスターID` を入力して保存、Googleで接続を押す。
5. 成功後、非公開マスター1031件を読み込み、当日AI選定10件の読込、訪問履歴の保存・Drive同期を試験する。

## Publicリポジトリ運用の安全確認
- Public GitHubに `*.xlsx`, `*.csv`, `*.json`, 認証キー, トークン, ユーザーの営業記録を絶対にコミットしない。
- `.gitignore` を保持。HTMLに埋込データがないことをCIに頼らず公開前に監査する。
- Cloudflare Pagesの「Git統合」からPrivateではなくPublic GitHubリポジトリを接続（GitHub Actionsは不要）。
- **Cloudflare Accessで本番URLを本人限定に保護**。Previewだけの保護では足りない。
- マスター/履歴/AIデータはGoogle Sheetsで非公開に保持し、閲覧権限はGoogle側も本人に限定する。
- `spreadsheets` OAuth scope は強い権限なので、権限同意画面をよく確認する。Google API OAuthの接続承認が必要。
- パソコン/スマホ共用端末のlocalStorageに営業履歴が残ることがあるため、共用端末にはログインしない。

## 制限事項
- 現時点で実際のGoogle OAuth認証と端末間同期、Cloudflare Access、本番URL公開は未検証。
- Cloudflare/GitHubの操作はアカウントで必要な権限があるユーザーが実行する。
- 元のv0.2で入力済みの情報を引き継ぐ場合は、旧版からJSONをエクスポートし、v0.3の設定から復元する（端末が違う場合も同様）。
- 公開コードは誰でも読める想定。APIシークレットの利用が必要になればバックエンドへ移す。