# zmk-roBa-config

roBa キーボード用 ZMK ファームウェア設定リポジトリ

## ハードウェア

- キーボード: [roBa](https://github.com/kumamuk-git/roBa)（42キー分割型）
- MCU: Seeeduino XIAO BLE (nRF52840)
- 右手: トラックボール（PMW3610）搭載、BLEセントラル
- 左手: ロータリーエンコーダ搭載、BLEペリフェラル

## キーマップ変更手順

### 1. Keymap Editor でキーマップを編集

1. [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/) にアクセス
2. GitHub アカウントでログイン
3. `abe-yusuke-6706/zmk-roBa-config` リポジトリを選択
4. GUI でキーマップを編集
5. **Save** をクリック → 自動で GitHub にコミットされる

### 2. GitHub Actions でファームウェアをビルド

- Save すると自動で [GitHub Actions](https://github.com/abe-yusuke-6706/zmk-roBa-config/actions) がビルドを開始する
- ビルドには数分かかる
- 緑チェックマーク = 成功、赤バツ = 失敗

### 3. uf2 ファイルをダウンロード

1. [Actions ページ](https://github.com/abe-yusuke-6706/zmk-roBa-config/actions) で最新のビルド（緑チェック）をクリック
2. **ページを一番下までスクロール**する
3. **Artifacts** セクションの **firmware** (zip) をクリックしてダウンロード
4. ダウンロードした `firmware.zip` を **右クリック → すべて展開** で解凍する
5. 解凍されたフォルダ内に以下のファイルがある:
   - `roBa_R-seeeduino_xiao_ble-zmk.uf2` （右手側）
   - `roBa_L-seeeduino_xiao_ble-zmk.uf2` （左手側）
   - `settings_reset-seeeduino_xiao_ble-zmk.uf2` （設定リセット用）

**注意:** zip を展開せずに中のファイルを直接コピーするとエラーになる。必ず先に展開すること。

### 4. roBa にファームウェアを書き込み

**キーマップだけ変更した場合は右手側（roBa_R）のみでOK。**

1. USB ケーブルで roBa（右手側）を PC に接続
2. XIAO BLE のリセットボタンを **素早く2回押し**（ダブルタップ）
3. `XIAO-SENSE` という USB ドライブが PC に表示される（ドライブレターは環境による）

#### 書き込み方法: コマンドライン（推奨）

GUI のドラッグ＆ドロップでは「予期しないエラー」が出ることがある。ターミナル（Git Bash 等）から以下のコマンドで書き込む:

```bash
# 右手側
cp /c/Users/user/Downloads/firmware/roBa_R-seeeduino_xiao_ble-zmk.uf2 /e/

# 左手側（必要な場合のみ）
cp /c/Users/user/Downloads/firmware/roBa_L-seeeduino_xiao_ble-zmk.uf2 /e/
```

※ Win環境：PowerShellでは以下コマンドで実施

```bash
Copy-Item "C:\Users\user\Downloads\firmware\roBa_L-seeeduino_xiao_ble-zmk.uf2" E:\
Copy-Item "C:\Users\user\Downloads\firmware\roBa_R-seeeduino_xiao_ble-zmk.uf2" E:\
```

書き込み後、自動で再起動 → 設定反映完了。

左手側も更新する場合は、左手側を USB 接続してリセットダブルタップ → 同じ手順で `roBa_L` の uf2 を書き込む。

## どの変更でどちらを書き込むか

| 変更内容 | 右手側 (roBa_R) | 左手側 (roBa_L) |
|----------|:---:|:---:|
| キーマップ変更 | 必要 | 不要 |
| エンコーダ設定変更 | 必要 | 必要 |
| トラックボール設定変更 | 必要 | 不要 |
| Bluetooth 設定変更 | 必要 | 必要 |

## レイヤー構成

| レイヤー | 名前 | 用途 |
|---------|------|------|
| 0 | default_layer | 通常入力（QWERTY） |
| 1 | FUNCTION | ファンクションキー (F1-F12) |
| 2 | NUM | 数字・記号 |
| 3 | ARROW | 矢印キー・ナビゲーション |
| 4 | MOUSE | マウスクリック（トラックボール操作時に自動切替） |
| 5 | SCROLL | スクロールモード |
| 6 | layer_6 | Bluetooth 切替・設定リセット |

## IME 設定（Windows 側）

roBa で日本語/英語を切り替えるには Windows 側の IME 設定が必要:

1. `Win + I` → 時刻と言語 → 言語と地域
2. 日本語 → `...` → 言語のオプション
3. Microsoft IME → キーボードオプション → キーとタッチのカスタマイズ
4. 「各キーに好みの機能を割り当てる」を ON
5. **無変換キー** → `IME-オフ`、**変換キー** → `IME-オン`

## トラブルシューティング

### ペアリングがうまくいかない
`settings_reset` の uf2 を両方に書き込んでから、通常の uf2 を再度書き込む。

### Keymap Editor でキーボードが表示されない
`config/roBa.json` が存在するか確認。物理レイアウト定義ファイル。

## 参考リンク

- [roBa ハードウェアリポ](https://github.com/kumamuk-git/roBa)
- [公式 ZMK config（kumamuk-git）](https://github.com/kumamuk-git/zmk-config-roBa)
- [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/)
- [ZMK ドキュメント](https://zmk.dev/docs)
- [GitHub Actions（このリポ）](https://github.com/abe-yusuke-6706/zmk-roBa-config/actions)
