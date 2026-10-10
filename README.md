# YOLOloader

`YOLOloader.exe`をダウンロードして起動し、「認識のみ」「ホストシールド追従」「Discord配信用」を選んで「起動」を押してください。Pythonの事前インストールは不要です。Windows 10/11の64bit版、NVIDIAのCUDA対応GPUとドライバーが必要です。確認済みGPUはRTX 3080です。

ダウンロード: https://github.com/EMT0129/YOLO-valo/releases/download/loader-v1.2.0/YOLOloader.exe

ローダーとImGui設定画面は黒と紫のネオン風デザインです。ローダー画面ではモード選択と起動・停止、現在の状態を表示します。

## データの扱い

- 学習済みモデル・TensorRTエンジンはGitHub Releasesから取得し、SHA-256照合後にメモリで保持します。`.pt`や`.engine`をファイルとして保存しません。
- モデルは通常のプロセス間パイプで認識処理へ渡します。モデルが未検出の頭を追従することはできません。
- Python・CUDA・各種DLLはWindowsの通常の仕組みで読み込むため、ユーザーの一時フォルダー（`%TEMP%/YOLO26_...`）へ展開します。通常終了時に削除します。
- EXE自身もPyInstallerの仕組みで一時フォルダーへ実行部品を展開します。
- デスクトップへデータフォルダーを作りません。モデルのキャッシュも作りません。
- 毎回実行部品もダウンロードするため、起動時に数GBの通信と数GB以上の一時ディスク領域が必要です。GPU負荷によってFPSは変わります。
- 完全にディスクを使わない実行ではありません。OSのページファイルやクラッシュダンプなどへの記録はこのアプリでは制御しません。強制終了や停電時には一時フォルダーが残る場合があります。

## 操作

ホストシールド追従はLeonardoとMAX3421E USBホストシールドを使用します。マウスをシールドへ接続し、このプロジェクトのArduinoファームウェアを導入済みの基板を使ってください。

ゲーム画面でF6を押して有効にすると中央へ半径150pxの円を表示します。サイドボタン1（戻る）を押している間、円内で中央に最も近い頭へ追従します。終了はF8またはローダーの「停止」です。

追従モードではImGuiの日本語設定画面も開きます。「吸い付き強度」「吸い付く範囲（半径px）」「縦方向の吸い付きを有効にする」を実行中に変更できます。縦方向OFFでは自動のY補正がゼロになり、横方向だけ追従します。手動の上下操作はそのままです。範囲0は画面全体、強度0は補正停止です。サークルも即時更新します。

設定画面を閉じた場合はF7で再表示します。設定画面の操作中はゲームの追従を停止し、ゲームへ戻ると再開します。ローダーの設定は現在の起動中だけ保持し、終了時に一時部品とともに削除します。次回起動時は初期値に戻ります。

Discord配信用は認識ウィンドウとサークルをキャプチャ可能にします。認識ウィンドウを共有するか、ゲームとサークルを映す場合は画面全体を共有してください。配信用プレビュー自身が認識画面に見える間は認識を一時停止します。ゲームはボーダーレス表示にしてプレビューを覆うか、プレビューを別モニターへ移してください。

## 配布・ソース

`loader_source.zip`にローダー、認識処理、Arduinoスケッチ、ビルド用コードを同梱しています。実行ライブラリの各ライセンスは実行部品のアーカイブに含まれます。Ultralyticsのライセンスはソースアーカイブにも含まれます。

モデル内容を変更する場合は配布manifestとEXEを再ビルドします。ローダーはEXEに同梱したハッシュで照合するため、GitHub上のデータを差し替えるだけでは起動できません。

v1.2.0は変更していない実行部品・モデルをv1.0.0の公開アーカイブから取得し、追加の`runtime-imgui.zip`を最後に展開します。どちらもEXE内の固定SHA-256で照合します。旧Releaseのアーカイブも必要です。

ソースからのビルドはPython 3.12 x64で`setup_runtime.cmd`を実行し、開発環境に`pyinstaller==6.22.3`を追加してください。`build_loader_update.py`で更新アーカイブとmanifestを作り、PyInstallerで`YOLOloader.py`をonefile/windowedビルドします。`loader_release/loader_manifest.json`を`--add-data`でEXEに同梱してください（Windowsでは絶対パスと`;.`を指定）。`build_loader_assets.py`は全実行部品を作り直す場合に使用します。GitHub公開用コードは`publish_loader.py`です。

UI: [Dear ImGui Bundle](https://github.com/pthom/imgui_bundle)（MITライセンス）。

仕様の参考: [PyInstallerの一時展開](https://www.pyinstaller.org/en/stable/operating-mode.html)、[GitHub ReleasesのダウンロードURL](https://docs.github.com/en/repositories/releasing-projects-on-github/linking-to-releases)。
