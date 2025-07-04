```markdown
# VNCサーバー設定手順

## 1. ROOTでログイン
```bash
vas.aiのコンソール上から、SSHの接続コマンドをコピペ
```
## 2. userのパスワードを設定
```bash
passwd user
```

## 3. 以降はuserアカウントで実施
```bash
su user
```

## 4. 既存プロセスを終了
```bash
killall x11vnc
```

## 5. パスワードファイルを再作成
```bash
mkdir -p ~/.vnc && x11vnc -storepasswd ~/.vnc/passwd
```
※新しいパスワードを2回入力

## 6. 新設定でx11vncを再起動
```bash
x11vnc -display :0 -rfbauth ~/.vnc/passwd -forever -bg
```

## 7. 【再接続】SSH接続でポートフォワーディング
5900のポートを開いたままにするため、下記の内容で再度SSH接続
```bash
ssh -L 5900:localhost:5900 user@０００.０００.80.81 -p 51492
                                    グローバルIP     SSHポート
```

※再起動後に、ポートが表示されるため、5900以外になっていたら下記に変更
```bash
ssh -L 5900:localhost:5901 user@０００.０００.80.81 -p 51492
                      ここ
```

**注意**: パスワード入力後、ターミナルは開いたままにしてください  
閉じるとトンネルが切断されます

## 8. ステップ2: RealVNC Viewerで接続
アドレス欄に入力: `localhost:5900`
```
