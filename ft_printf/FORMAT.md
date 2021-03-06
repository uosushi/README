```md
# %[フラグ][最小フィールド幅][.精度][長さ修飾子]変換指定子
%[flags][width][.precision][length]type
```

cspdiuxX%

# flags (Linux man)
- [#] 代替形式。基数を表すプレフィックスの出力等を指定
	- [ ] bcdsuでは無効
	- [ ] o 変換のとき、(先頭文字が 0 になっていない場合に先頭に 0 を追加することで) 出力文字列の最初の文字を 0 にする（数値の精度が上がる）
	- [ ] xX変換かつ数値が 0 でないとき、文字列 0x ( X 変換の場合には 0X ) が前に付与される（ 0x は16進数を表すもの）
	- [ ] aAeEfFgG 変換では、 小数点に続く数字がなくても、 出力には常に小数点が含まれる (通常は、小数点の後に数字が続く場合にのみ、小数点が表示される)。 
	- [ ] gG 変換では、末尾のゼロが変換結果から削除されない（通常は削除される）。
- [-] フィールドの左寄せ
- [+] 数字が符号付きであるとき、数字の前に符号をつける
- [ ] 数字が符号付きでかつ 正の数 のとき、数字の前に空白を出力
	- スペースと+がどちらとも指定されているとき、スペースは無効化される）
- [0] 出力文字数が最小フィールド幅未満の場合は 空白 の代わりに 0 で埋める
	- 0 と - がどちらとも指定されているとき、 - が優先され 0 は無効化する
	- diouxXaAeEfFgG 変換 以外では Werror でエラーとして扱われコンパイル失敗。
	- [x] ↑CFLAGSをはずして無理矢理実行したら、warning とゼロ埋めされて出力

# width (int)
正の数のとき有効である（INT_MAXより大きいと無効）
直接入力しても、引数で受け取っても同じ。
- [*] 引数をINTで受け取る。引数が存在しなかったらNULL（つまり0）が入る。
- [ ] INT_MAX+1は-1扱いになる（埋めないので左寄せ）
- [ ] width - format->len > INT_MAX だと失敗扱いになる
- [ ] widthより長さがちいさいときのみ埋める。
- [ ] flagsで 0 埋めの指定がないなら空白で埋める
- [x] （ものすごく面白いことに気がついてしまった）widthが`-n`のとき
	- 左寄せフラグ扱いをされている（-がフラグとして解釈されてるっぽい?/未定義/Guacamole挙動）
	- widthは`-n * -1`として扱われる

# .precision (int)
- [ ] ドットのみの場合、ゼロとみなされる
- [ ] 精度が負の数だった場合、精度は指定されなかったものとみなされる
- [ ] preciに`INT_MAX + 1`を入れたときは int でキャストされ`-1`扱いになるので`%f`ならデフォルトの`6`が指定される
- [ ] ef 変換なら小数点以下の桁数を、文字列なら出力文字数を指定する。

# type (全てGuacamoleのmanチェック済み。localとguacamoleのman printfは同一だった（diff済))

- [ ] 記入例
	- [#] CFLAG: エラー詳細 ／ CFLAGSなしで実行したときの結果 ／ Guacamole man (明記がなければ未定義/言及なしである) ／ その他 man （任意、どのmanがソースか明記）
	- [0] エラーもなく動くときはその仕様と挙動について ／ Guacamole man（明記するような特徴があれば）

- [ ] c
	- [#] Werror: 未定義 ／ 効果なし ／ bcdsuでは効果がないと明記
	- [0] Werror: 未定義 ／ ゼロ埋めされる
	- [-] 左揃え（右埋め）
	- [ ] Werror: 未定義 ／ 効果なし
	- [+] Werror: 未定義 ／ 効果なし
	- [width] 可能
	- [preci] 無効
- [ ] s
	- 0およびNULLが入った時は文字列`(null)`を出力
	- [#] Werror: 未定義 ／ 効果なし ／ bcdsuでは効果がないと明記
	- [0] Werror: 未定義 ／ ゼロ埋めされる
	- [-] 左揃え（右埋め）
	- [ ] Werror: 未定義 ／ 効果なし ／ 符号付きフォーマットかつ正の数のときのみ効果ありと明記（%sに効果はない）
	- [+] Werror: 未定義 ／ 効果なし ／ 符号付きフォーマットにのみ効果ありと明記（%sに効果はない）
	- [width] 可能
	- [preci] 最大出力文字数
- [ ] p
	- 常に0xがつく 
	- Guacamole man では言及されていない
	- [#] Werror: 未定義 ／ 効果なし (もともと 0x がつくため)
	- [0] Werror: 未定義 ／ 0x の後をゼロ埋め
	- [-] 左揃え（右埋め）
	- [ ] Werror: 未定義 ／ 効果なし
	- [+] Werror: 未定義 ／ 効果なし
	- [width] `+`も` `も出力文字数に含まれる
	- [preci] Werror: 未定義 ／ 効果なし、ただし入力が 0 かつ preciが 0 のときは効果があり、0x のみ出力する
- [ ] di
	- int 型の引数を 符号付き10進数に変換する
	- [#] Werror: 未定義 ／ 効果なし ／ bcdsuでは効果がないと明記
	- [0] ゼロ埋め
	- [-] 左揃え（右埋め）かつ 0 フラグを無効に
	- [ ] 正の数のとき + 符号の代わりに空白を出力 ただし + に押し負ける ／ 符号付きフォーマットかつ正の数のときのみ効果あり
	- [+] 常に符号を表示 ／ 符号付きフォーマットにのみ効果ありと明記
	- [width] `+`も` `も出力文字数に含まれる 
	- [preci] 最小桁数までのゼロ埋め。左揃えが指定されても関係ない。widthに基づくゼロ埋めは空白埋めに置き換わる ／ Gucamole man には明記なし
- [ ] u
	- unsigned int 型の引数を符号なし10進数に変換する
	- [#] Werror: 未定義 ／ 効果なし ／ bcdsuでは効果がないと明記
	- [0] ゼロ埋め
	- [-] 左揃え（右埋め）
	- [ ] Werror: 未定義 ／ 効果なし ／ 符号付きフォーマットかつ正の数のときのみ効果ありと明記（%uに効果はない）
	- [+] Werror: 未定義 ／ 効果なし ／ 符号付きフォーマットにのみ効果ありと明記（%uに効果はない）
	- [width] 可能
	- [preci] 入力が 0 かつ preciが 0 なら効果あり
- [ ] xおよびX
	- unsigned int 型の引数を符号なし16進数に変換する
	- [#] x なら 0x 、X なら 0X がつく（ただし、入力が 0 なら何も付加されない）
	- [0] ゼロ埋め
	- [-] 左揃え（右埋め）
	- [ ] Werror: 未定義 ／ 効果なし ／ 符号付きフォーマットかつ正の数のときのみ効果ありと明記（%xXに効果はない）
	- [+] Werror: 未定義 ／ 効果なし ／ 符号付きフォーマットにのみ効果ありと明記（%xXに効果はない）
	- [width] 0x および 0X は出力文字数に含む
	- [preci] 0x を除いた最小桁数までのゼロ埋め。入力が 0 かつ preci が 0 のときのみ数字本体が消失する（ゼロ埋め空白埋めなどは消えない）。
- [ ] f
	- 精度の指定なしなら小数点以下6桁まで
	- 一部テストケースには符号をつけないと整数として処理されてしまうので注意
	- [#] 常に小数点を付加（本来は小数点以下の数字がある場合にのみ小数点が表示されます）
	- [0] ゼロ埋め
	- [-] 左揃え（右埋め）`0`フラグが無効に
	- [ ] 正の数のとき + 符号の代わりに空白を出力 ただし + に押し負ける
	- [+] 常に符号を表示 ／ 符号付きフォーマットにのみ効果ありと明記
	- [round] max+1桁目が5以上かつ0~max桁全てが9であるとき1の位+1される

# formatの優先順位
if (数値変換(d, i, o, u, x, X))
	if (精度(preci)による最小桁数の指定)
		preciの0埋めのみ、0フラグは無効化
if (a, A, e, E, f, F, g, G)
	if (小数点以下が1桁でも表示されている || #フラグが指定されている)
		小数点（ドット）が常につく

# MEMO
%dixuなどにpreciが指定されたとき
最小桁数埋めはpush側でやる

%s(string)にpreciが指定されたとき
print_typeの方で先にpreci分のbody削りを行なってしまう
（push側ではpreciを考慮しなくてよい）

%sはNULLが入りうるため%s側で処理

widthに基づく0埋めは
(width - (prefixの長さ + 本体の長さ + preciによる最小桁数までの0埋めの長さ))の長さだけ行われる。

print_address.c
    // add_prefix prefix_space (address >= 0) always true
# d, i, o, u, x, X
width
- [preci] 余った桁数分、前を0埋め
d, i, o, u, x, X 変換では、表示される最小の桁数を指定する。
((bodyの長さ > preci)のとき body前 で桁埋めあり)

a, A, e, E, f, F 変換では、小数点以下に表示される数字の桁数を指定する。
((DECIMAL_DIGIT < preci)のとき body後ろ で桁埋めあり)

g と G 変換では、有効数字の最大桁数を指定する。 

s と S 変換では、文字列から出力される最大文字数を指定する。
((bodyの長さ > preci)のときbodyの長さがpreciになる)


## comment memo

double_to_idouble

frac_to_integer
// n=1からスタート(最も右を1の位とする)

ft_printf
// 変換指定がいくつあるか（引数の特定）

parse
// parse_done  : 読み込んだ長さ（MAX: inputの長さ）
// format_done : (input文字列に含まれる1個の)formatの長さ
// done        : 出力する長さ（失敗したときのみ-1が入る）

idouble_to_ascii
// preciがdecimalの長さを超えたときの処理はpush側で

print_string
// format->len = stringの(NULL止め)長さ

push
// typeが整数でpreciによる最小桁数0埋めの可能性があるならlength_of.precisionにformatの長さを抜いたprecisionを代入
