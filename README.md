# WinBirdアーカイブ
WinBirdのファイルのアーカイブです。脆弱性の多い過去のファイルを見ることができます。

# 現時点で公開可能な脆弱性・バグ

## Client: options.htmlのパスワードがハードコードされている
- 対象： 3-2まで
- ファイル： options.js 32行目
- 再現方法： `chrome-extension://aenkapbklopnjfadjnjghcbnnloadlpl/options.html` を開く

「WinBird$JS6」というパスワードが、以下のように、直接ハードコードされている。

```javascript
if(updateKey.startsWith('6SJ$driBniW'.split('').reverse().join(''))){const tempKeyWords=updateKey.split(/\s+/,5);for(let i=1;i<tempKeyWords.length;i++){tempKeyWords[i]=tempKeyWords[i].toLowerCase();}
```

上記の処理の内容は、「6SJ$driBniW」を逆順にして、「WinBird$JS6」にしている

- ### 使用できたパスワード：
・`WinBird$JS6`：情報・ログを表示

・`WinBird$JS6 + 半角スペース + 以下のもの`

　`on`：クライアントの無効化フラグ（ProhibitClient）を解除し、拡張機能をリロード
 
　`forceoff`：ProhibitClient を有効化し、クライアントを無効にしてリロード
 
　`filecheck reset`：プログラムファイルバージョンチェック済みフラグをリセット
 
　`yakanseigen reset`：夜間制限ポリシー（YakanSeigenPolicy）をリセットしリロード
 
　`cbtsettings reset`：CBTモード設定（CbtModeSettings）をリセットしリロード
 
　`devmode on / off`：開発者モードをオン/オフ（日付付きでローカルストレージに保存）しリロード
 
　`show self on / off`：自身の拡張機能ページ（chrome://extensions/）の表示許可をトグル（セッションストレージ）
 
　`debug core on / off`：デバッグコアモードをトグル（クライアントコアに通知）
 
　`debug trace 0 / 1 / 2`：デバッグトレースレベルを設定（0=UNSET,1=INFO,2=DEBUG）
 
　`testmode A/B/C <数値>`：デバッグテストモードの値を設定（ローカルストレージに保存）
 
　`logsep` または `logseparator`：ログに区切り線（'='×48）を出力

### ・現在でも使用できるもの

(これらはハッシュ化・修正しない仕様だと判断して公開しています)

`WinBird$JS6`は付けず、そのまま使用できます。

　`yakanseigen`：夜間制限情報を表示

　`cbtmode`：CBTモード情報を表示

　`cbtblocked`：CBTでブロックされたURLリストを表示

　`storage`：ストレージ情報を表示 (現在はハッシュ化され使用不可)

　`counter`：カウンター情報を表示 (現在はハッシュ化され使用不可)

　`close`：ウィンドウを閉じる

　`upload`：ログをアップロード

　`logfile` または `savelog`：ログをファイルに保存

　`screeninfo`：スクリーンのサイズなどを表示


- 修正：

2026年 4/16 前後に修正済み

一部のパスワードをハッシュ化。HashCatでは復元できない程度の強さである。また、 `WinBird$JS6` ではない。

```javascript
const tempKeyWords=updateKey.split(/\s+/,10);let keyHash=['','',''];Utils.getHashAsync(tempKeyWords.join(' ')).then((hash)=>{keyHash[0]=hash;for(let i=1;i<tempKeyWords.length;i++)tempKeyWords[i]=tempKeyWords[i].toLowerCase();return Utils.getHashAsync(tempKeyWords.join(' '));}).then((hash)=>{keyHash[1]=hash;return Utils.getHashAsync(tempKeyWords.slice(0,tempKeyWords.length-1).join(' '));}).then((hash)=>{keyHash[2]=hash;if(keyHash[1]==='5c3f01edca632acb5fc03084f8803a258d3faa99f4297c8c5448e744720026bb'){getStorageInfoLogAsync().then((logs)=>{fnAppendLogContainer(logs);}).finally(()=>{isCanSaveLog=true;refreshUpdateKeyTextAsync(true).then(()=>{});});return;}
if(keyHash[1]==='a746454fab76374b96464eb9e1655ebf9176362f9903c58fab0e3cfb12234c0f'){getCountInfoLogAsync().then((logs)=>{if(!logs.length){logs=['ありません'];}
```


