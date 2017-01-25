ここまで説明したdoskeyマクロは一度コマンドプロンプトを終了すると消えてしまいます。  
マクロファイルにまとめてmacrofileオプションで読み込むと簡単に使えます。  
`doskey /macrofile=".cmdrc" >nul 2>&1`

最後にこのコマンドをバッチファイルに保存したうえで、  
コマンドプロンプト起動時に実行する設定を二通り紹介します。

1. レジストリにて指定

   > \[HKEY\_CURRENT\_USER\Software\Microsoft\Command Processor\]  
   > "AutoRun"="Path\to\batfile\cmd.bat"

2. cmdのkオプションにて指定

   > %windir%\system32\cmd.exe /k Path\to\batfile\cmd.bat

またさらに使用頻度によって、catやifconfigなども簡単に代替コマンドが考えられます。

様々な作業環境にさわる場合に、外部ソフトウェアを導入せず標準環境をなるべく標準環境のまま活用することは、セキュリティポリシーなどの事情に抵触せず効率化を図る良い方法です。  
皆さま素敵なDOSライフお過ごしください。

