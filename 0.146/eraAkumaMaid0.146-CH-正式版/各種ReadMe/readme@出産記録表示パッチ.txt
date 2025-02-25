﻿------------------------------------------------------
このパッチについて
------------------------------------------------------
出産した兒童の父親と性別を記録し、ステータス画面で出力できるようにします

CSV、ERBフォルダをeraAkumaMaid 2019_11_11に上書きしてください
試用する際はデータのバックアップを取って、破棄しても問題ないセーブで遊んでください


------------------------------------------------------
仕様
------------------------------------------------------
@BIRTH_RECORD、@BABY、@GENDER_SYMBOLの3つの関数からなります
@BIRTH_RECORDには出産記録：保存、出産記録：書き出し、出産記録：書き出し２の3つのモードがあります


@BIRTH_RECORD, ARGS, ARG, ARG:1

出産記録：保存
　兒童が生まれ、性別を選択した直後にCALL BIRTH_RECORD, "出産記録：保存", TARGET, RESULTの形で呼ばれます
　CSTR:出産記録に 兒童の父親(父親のBASE:識別番号)_兒童の性別(女なら0、男なら1) が記録されます
　BASE:識別番号は17桁の半角数字、性別の0or1とSPLITのための/_を合わせると、1人の記録に20文字使用します
　CSTRの保存できる文字数が半角2000字らしいので、角色ごとに100人まで保存できると思います

　CSTRに文字列で記録する仕様のため、角色のCSVにあらかじめ書き込んでおくことで登場時点で兒童を産んでいることにできます
　識別番号はセーブごとに変わるので部活メンバーや你の兒童を書き込むことはできませんが、兒童のいる人妻角色などで使えると思います(この設定だと你が外道になりますが)
　旦那で旦那との兒童、彼氏で彼氏との兒童、元彼で元カレとの兒童、不で父親不明の兒童になります
　例えばCSVにCSTR,出産記録,元彼_0/不_1/彼氏_0と記述しておくと加入時点で元カレとの間に女の子1人、父親不明の男の子を1人、今の彼氏との間に女の子を1人産んでることことになります


出産記録：書き出し
　CSTR:出産記録に保存されている父親ごとの兒童数をカウントして以下のように出力します
　産んだ順番は考慮されません

□ 出産の記録 □-------------------------------------------------------------------------------
[ 1]你との兒童             3人(♂ 2:♀ 1) [ 6]乃々との兒童               2人(♂ 1:♀ 1) 
[ 8]真との兒童                 1人(♂ 0:♀ 1) [XX]父親不明の兒童             2人(♂ 1:♀ 1) 

出産記録：書き出し２
　CSTR:出産記録に保存されている順番に羅列します

□ 出産の記録 □-------------------------------------------------------------------------------
[ 1]你との兒童(女の子)            　[ 2]你との兒童(男の子)            　
[ 3]乃々との兒童(女の子)              　[ 4]真との兒童(女の子)                　
[ 5]父親不明の兒童(男の子)            　[ 6]父親不明の兒童(女の子)            　
[ 7]乃々との兒童(男の子)              　[ 8]你との兒童(男の子)            　

　@NEW_PRINT_STATE, ARG, ARGSのCASE "通常：２"でCALL BIRTH_RECORD, "出産記録：書き出し", ARGの形で呼ばれます
　CONFIGURE.ERBの項目ONOFFボタンに"項目：出産の記録"を追加して項目ONOFFできるようにしています
　出産記録：書き出し２は兒童が増えるほど行数がどんどん増えるので、作りはしましたが実際には使用していません
　父親不明の兒童は你や部員以外が父親の場合に表示されます　現状では無いですし今後も可能性は低いと思いますが、EVENT_NINSHIN.ERB内に処理が存在しているので念のためカウントするようにしています
　デバックモードなどで角色を削除した場合、記録された識別番号と一致する識別番号が存在しなくなり、記録数と表示数が移開ます　通常PLAYでは起こらないと思うので特に対策はとっていないです
　保存できる文字数に制限があるため一定数以上になると保存されなくなります
　SPLITやFORループを多用して兒童数をカウントしているので兒童の数や部員数が増えるほど処理が重くなっていきます
　セーブをいじって兒童70人、全角色を勧誘した状態で試したところ問題はありませんでしたが、古いノートPCなどの低スペックなPCではどうなるかわかりません
　パッチ追加以前に出産経験がある場合は、表示と齟齬が出ますがどうしようもないです



@BABY(ARG, ARG:1)
　ARG:1の角色が産んだARGの角色の兒童の数を出力する関数です　ARG:1を省略した場合はTARGETになります
　IF BABY(MASTER)　このような記述でTARGETがMASTERの兒童を産んでるかの判定に使えます
　你の兒童しかいないのであればTALENT:出産経験でOKですが、其他の角色が父親の兒童がいる場合はこちらを使ったほうが正確になると思います
　例えば、女你に色々な角色の兒童を産ませている場合などで口上の分岐条件に使えます

@GENDER_SYMBOL, ARGS, ARG
　@HEARTMARK等のように性別記号を出力する関数です
　SJISで普通に入力できますが、FONTや色の指定をしたかったので関数にしています
　ARG == 1でTimes New Roman、それ以外はFONTを変更しません
　ARGSに"男"、"女"といれることで男女それぞれの性別記号を出力できます　男は青色、女は赤色になります
　このパッチ以外で使用する機会は多分無いと思いますが、色付きの性別記号が必要な時があれば使ってください


------------------------------------------------------
其他
------------------------------------------------------
パッチ作者の知識不足などが原因でどう解決すればいいか悩んでいる問題点など


・BASE:ARG:精液の優勢の取りうる値
　BASE:ARG:精液の優勢の取りうる値は、存在する部員のBASE:識別番号、モブなどの一般人の-4以外にあるのか

・emueraの仕様で出産の記録の項目がボタンになってしまう
　PRINTPLAINFORMを使えばボタン化しなくなるが、処理がかなり重いらしく使っていいのか

・パッチ追加以前に出産経験がある場合の処理
　TALENT:出産経験とCFLAG:息子の数、娘の数から你の兒童としてCSTR:出産記録に追加すれば一応人数の辻褄は合わせられる
　全部你にするのも強引ではあるので、齟齬があっても見なかったことにしたほうがよさそうではある

・記録できる限界のカウント方法
　現在的仕様ではCSTR:出産記録に記録できるかをTALENT:出産経験を使って判定しているが、パッチ追加以前に出産経験がある場合に記録できるのに記録しない状態になってしまう
　解決法に以下の2つを考えたが2のほうがよさそう
　1.カウント用に変数をもう1つ用意する
　2.STRLENS(CSTR:出産記録)で文字数をカウントして判定する
　2なら以下のようにすればいいのか

	IF STRLENS(CSTR:出産記録) > 1980
		CALL PRINT_STR, "イエロー_出産记录的记录总数达到了限界。不能再记录了_W"
		RETURN ARG:1
	ENDIF


・記録数を増やせないか
　このパッチでは父親の記録にBASE:識別番号を使用している(父親の判定に使われるBASE:精液の優勢の中身がBASE:識別番号のため)
　BASE:識別番号は17桁もあるためここを減らすことができれば記録数の上限をかなり増やすことができる
　父親をBASE:識別番号のかわりに角色の登録番号などで保存した場合に何か不具合があるのか
　登録番号を使えるなら以下のような処理で変換できると思う

FOR LCOUNT, 1, CHARANUM
	IF BASE:LCOUNT:識別番号 == BASE:ARG:精液の優勢
		CSTR:ARG:出産記録 += @"%TOSTR(LCOUNT)%"
		BREAK
	ENDIF
NEXT


