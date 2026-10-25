# 【無償公開版】Webサイト & PHP脆弱性 防衛ハンドブック

**プロジェクト**: SECURDIAG オープン・セキュリティ・イニシアティブ  
**発起人・代表**: 新川 慎吾  
**ライセンス**: クリエイティブ・コモンズ 表示 - 継承 4.0 国際 (CC BY-SA 4.0)  
※本ドキュメントは非営利のボランティア活動の一環として作成されており、どなたでも無償で転載・社内共有・活用いただけます。

---

## 1. はじめに：なぜいま無償でセキュリティ知識を届けるのか

日本のWebサービス、特に中小企業やECサイトを支えるPHP環境は、現在深刻なサイバー脅威に直面しています：

1. **サポート終了（EOL）の放置**: 国内サイトの多くでPHP 7.x/8.0/8.1が放置され、公式セキュリティパッチが届かない状態です。
2. **深刻な遠隔攻撃（CVE-2024-4577等）**: Windowsサーバー上のPHPを狙った、認証なしでのOSコマンド実行脆弱性。
3. **クレジットカード情報の抜き取り（フォームジャッキング）**: 決済画面の不正改ざん被害。

「予算がないから対策できない」という現場をなくすため、SECURDIAGでは現場のエンジニアやWeb担当者が自力で防衛できる知識と設定コードを無償提供しています。

---

## 2. すぐに適用できる「3大緊急防御コード」

### ① CVE-2024-4577 (PHP CGI引数インジェクション) のApache遮断
```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{QUERY_STRING} ^%ADd.* [NC]
    RewriteRule ^.*$ - [F,L]
</IfModule>
```

### ② Nginx による機密ファイル（.env, .git）のアクセス完全遮断
```nginx
location ~ /\.(env|git|ht) {
    deny all;
    return 404;
}
```

### ③ セキュリティヘッダの返却（情報漏洩・不正スクリプト防止）
```nginx
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header Content-Security-Policy "default-src 'self' 'unsafe-inline' https:;" always;
```

---

## 3. セルフ診断チェックリスト（10項目）

- [ ] 1. PHPのバージョンが 8.2 または 8.3 になっているか？
- [ ] 2. `php.ini` で `expose_php = Off` になっているか？
- [ ] 3. 本番環境で `display_errors = Off`（エラー画面が出ない）になっているか？
- [ ] 4. フレームワークの `APP_DEBUG` が `false` になっているか？
- [ ] 5. URLに `/.env` と入力してファイルの中身がダウンロードされないか？
- [ ] 6. データベースアクセスに「プリペアドステートメント（PDO）」を使っているか？
- [ ] 7. 管理画面にIP制限または二要素認証（2FA）を設けているか？
- [ ] 8. SSL/TLSが有効で、HTTPからHTTPSへ自動リダイレクトされているか？
- [ ] 9. HSTSやX-Content-Type-Optionsなどのセキュリティヘッダが設定されているか？
- [ ] 10. `security.txt` を配置し、脆弱性の通報窓口を明示しているか？

---

## 4. ご相談・ご質問について
本ハンドブックに関するご質問や、設定手順でお困りの点がございましたら、SECURDIAGボランティア窓口（`davidsilver1105@gmail.com`）までお気軽にお寄せください。営利勧誘等は一切行いません。
