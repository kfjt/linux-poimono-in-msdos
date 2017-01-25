1. `ls=dir /d /on $* $b findstr . $b find /v "ボリューム" $b find /v "のファイル" $b find /v "のディレクトリ"` ![](/assets/ls.png)  
   ディレクトリ中のファイルとサブディレクトリを一覧表示します。  
   \[コード解説\]  
   `ls=dir /d /on $*` …エイリアスを作ります、lsの引数をdirにすべて渡すキーワードが$\*です。  
   `$b findstr .` …dirの結果をパイプでfindstrに渡して「空行」の除きます。パイプを表すキーワードが$bです。  
   `$b find /v "ボリューム"` …findstrの結果をパイプでfindに渡して「"ボリューム”を含む行」を除きます。  
   `$b find /v "のファイル" $b find /v "のディレクトリ"` …同様に「"のファイル”を含む行」「"のディレクトリ”を含む行」を除きます。

2. `pwd=echo %CD%` ![pwd](/assets/pwd.png)  
   現在のディレクトリを表示する。

   > $pwd  
   > D:\home\sample

   図3:pwdの実行結果

3. `mv=move $*` ![mv](/assets/mv.png)  
   ファイルを移動およびファイルとディレクトリ名を変更します。

   > $ls  
   > \[.\]      \[..\]     aa.txt   bb.txt
   >
   > $mv aa.txt cc.txt  
   >         1 個のファイルを移動しました。
   >
   > $ls  
   > \[.\]      \[..\]     bb.txt   cc.txt

   図4:mvの実行結果

4. `cp=copy $*` ![cp](/assets/cp.png)  
   1つまたは複数のファイルを別の場所にコピーします。

   > $ls  
   > \[.\]      \[..\]     bb.txt   cc.txt
   >
   > $mkdir aa
   >
   > $ls aa  
   > \[.\]  \[..\]
   >
   > $cp bb.txt aa\aa.txt  
   >         1 個のファイルをコピーしました。
   >
   > $ls aa  
   > \[.\]      \[..\]     aa.txt

   図5:cpの実行結果

5. `rm=del $*` ![rm](/assets/rm.png)  
   ファイル \(複数可\) を削除します。

   > $ls  
   > \[.\]      \[..\]     bb.txt   cc.txt
   >
   > $rm cc.txt
   >
   > $ls  
   > \[.\]      \[..\]     bb.txt

   図6:cpの実行結果

6. `touch=break $g $*` ![touch](/assets/touch.png)  
   ファイルのアクセス時刻と修正時刻を変更する 。空ファイルを作るときに便利。  
   \[コード解説\]  
   `break` …Windows 上では何も効果はありません。DOS システムとの互換性を維持するために用意されています。  
   `$g $*` …doskeyにおけるリダイレクト記号\(&gt;\)およびパラメータ

   > $ls  
   > \[.\]      \[..\]     bb.txt  
   >    
   > $touch dd.txt  
   >    
   > $ls  
   > \[.\]      \[..\]     bb.txt   dd.txt

   図7:touchの実行結果

7. `vim=copy /y con $1 $b find /v "のファイルをコピーしました"` ![vim](/assets/vim.png)  
   ファイルを編集する、もどき。コンソールの入力をパラメータに指定したファイルへ保存する。Ctrl+zで入力を終わる。  
   \[コード解説\]  
   `copy /y con $1` …copyのyオプションは受け側の既存のファイルを上書きする前に確認のメッセージを表示しません。conはキーボードからの入力。  
   `$b find /v "のファイルをコピーしました"` …doskeyにおけるパイプとfindのvオプションでcopyの出力を抑制します。

   > $vim ee.txt  
   > ほげほげ^Z
   >
   > $cat ee.txt  
   > ほげほげ

   図8:vimの実行結果

8. `find=where /r $1 $2` ![find](/assets/find.png)  
   指定したディレクトリ配下から検索パターンに一致するファイルの場所を表示します。

   > $find . \*.txt  
   > D:\home\sample\bb.txt  
   > D:\home\sample\dd.txt  
   > D:\home\sample\ee.txt

   図9:findの実行結果

9. cdをバッチファイルとして作成したうえで`cd=cd.cmd` ![cd](/assets/cd.png)  
   バッチで作る目的はつぎの三つ。

   * `pushd` との置き換え
   * `cd -` のサポート\( `popd` との置き換え\)
   * 移動時にウィンドウタイトルとプロンプトを変更
     \(ついでにプロンプトをLinux風にします\)

   ```
   @echo off
   if "%1x"=="x" pushd %home% && goto :VIEW
   if "%1x"=="-x" popd && goto :VIEW
   pushd %1

   :VIEW
   title %CD%

   call :GETPWD "%CD%"
   prompt [%USERNAME%@%COMPUTERNAME%$S%PWD%]$$
   if %home%==%CD% prompt  [%USERNAME%@%COMPUTERNAME%$S~]$$
   goto :EOF

   :GETPWD
   set PWD=%~nx1
   exit /b
   ```

   リスト1:cd.cmd

   > \[kazuhiro@MyMachine ~\]$cd log  
   > \[kazuhiro@MyMachine log\]$cd -  
   > \[kazuhiro@MyMachine ~\]$

   図10:cdの実行結果

10. sudoをバッチファイルとして作成したうえで`sudo=sudo.cmd` ![sudo](/assets/sudo.png)  
    バッチで作る目的はただ一つ。

    * `runas`の使い勝手が悪いから。

    ```
    @powershell -command "Start-Process -Verb runas %1"
    ```

    リスト2:sudo.cmd

    > $sudo notepad

    図11:sudoの実行結果

11. tailをバッチファイルとして作成したうえで`tail=tail.cmd` ![tail](/assets/tail.png)  
    バッチで作る目的はただ一つ。

    * `tail`に該当するコマンドが標準ではないから（除くPowerShell）。

    ```
    @echo off
    set file=%1
    set opf=%2
    :LOOP
    for /f "delims=:" %%a in ('findstr /n "^" %file%') do set line=%%a
    for /f "tokens=1,* delims=: eol=" %%a in ('findstr /n "^" %file%') do (
    if "x%line%"=="x%%a" if "x%%b"=="x" echo.
    if "x%line%"=="x%%a" if not "x%%b"=="x" echo %%b
    )
    if "x%opf%"=="xf" timeout 1 >nul&cls&goto :LOOP
    ```

    リスト3:tail.cmd

    > $tail log\123.log  
    > e5  
    > $tail log\123.log t  
    > f6

    図12:tailの実行結果

---

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

