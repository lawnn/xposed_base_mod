xposed_base_mod
===============

1. パッケージ名の変更

AndroidManifest.xmlを開き、

package=”com.wedy.twitrans”
のcom.wedy.twitransをパッケージ名にしたい好きな英数字に変更します。(jp.android.mymoduleなど)

android:versionCode=”5″

android:versionName=”2.9.7″
の二つはバージョン情報で、アプリを更新するときは必ずandroid:versionCodeを以前の数字より大きくしないといけません。

android:versionNameのほうはユーザーがわかりやすいようにつけるバージョン名なので何でもいいです。

 

次に、assetsフォルダ内のxposed_initをテキストファイルとして開きます。

com.wedy.twitrans.NotificationiconPatcher
となっているので、「com.wedy.twitrans」の部分をAndroidManifest.xmlで入力したパッケージ名にしてください。

例: jp.android.mymodule.NotificationiconPatcher

srcフォルダの中はcom→wedy→twitrans、とパッケージ名をピリオドで分けたフォルダ名になっているので、これも同じく

AndroidManifest.xmlで入力したパッケージ名をピリオドで分けた形にしてそれぞれ変更します。

最後に、srcフォルダの一番奥にあるNotificationiconPatcher.javaをテキストファイルとして開き、

package com.wedy.twitrans;
をAndroidManifest.xmlで入力したパッケージ名に変更します。

例: package jp.android.mymodule;

 

これでパッケージ名の変更ができました。

2. 変更するリソースの指定

続いてXposedモジュールの核となるNotificationiconPatcher.javaの中身を書き換えます。

private static final String PACKAGE_ESF = “org.mariotaku.twidere”;
の行の”org.mariotaku.twidere”で改変するリソースのあるパッケージを指定しています。

これを書き換えたいアプリのパッケージ名に変更してください。

 

下の方にある

resparam.res.setReplacement(PACKAGE_ESF, “string”, “multi_select”, modRes.fwd(R.string.multi_select));
は

Twidere(org.mariotaku.twidere)の”strings.xml”にある”<string name=”multi_select”>”を、

Xposedモジュール内の”res/values/strings.xml”にある”<string name=”multi_select”>”で置き換える、という意味です。

 

array.xmlの中身を書き換えたければ

resparam.res.setReplacement(PACKAGE_ESF, “array”, “ex_array”, modRes.fwd(R.array.ex_array));
bools.xmlなら

resparam.res.setReplacement(PACKAGE_ESF, “bool”, “config_ex”, modRes.fwd(R.bool.config_ex));
となります。

書き換える内容はNotificationiconPatcher.java内に直接書くこともでき、

例えばintegers.xmlなら

resparam.res.setReplacement(PACKAGE_ESF, “integer”, “config_in”, 0);
drawable.xml(色を変える場合)なら

resparam.res.setReplacement(PACKAGE_ESF, “drawable”, “icon_bg”, 0×00000000);
となります。

 

置き換えに使うリソースはresフォルダ以下におきます。

少し紛らわしいですが、日本語翻訳に置き換えたい、という場合でも置き換える文字列は/res/values/strings.xmlに書いてください。

日本語訳だからといって/res/values-ja/strings.xmlに書いても

resparam.res.setReplacement(PACKAGE_ESF, “string”, “***”, modRes.fwd(R.string.***));

という風に読み込む箇所を指定しているので反映されません。

 

元々あるファイル(arrays.xmlやplurals.xml)や文字列はTwidere翻訳用なので適宜削除してください。

このとき、strings.xmlの<string name=”app_name”>と<string name=”app_description”>はモジュールの名前と説明の表示に必要なので削除しないようにしてください。

 

3. モジュールの名前と説明を入力

/res/values/strings.xml

にある

<string name=”app_name”>Twidere Translator for JPN</string>

<string name=”app_description”>Fix Japanese translation of Twidere</string>
を 自分のモジュールの名前に書き換えます。

<string name=”app_name”>はモジュール名、<string name=”app_description”>はXposedインストーラのモジュールリストで表示する説明です。

/res/values-ja/string.xmlは日本語環境での表示に使うものなので、同様に編集しUTF-8で保存してください。

 

/res/drawable/ic_launcher.pngはアプリアイコンです。

 

後はEclipseなどでビルドすればXposedモジュール作成は完了です。

※ビルドする際はXposedBridgeApi-42.jarをダウンロードして、

ビルド・パス→外部ライブラリなどで追加するのを忘れないでください。
