## 艦これの仕様に関する雑多なメモ
---

#### 特注家具職人が必要な条件  
2000≦price<20000  
とくに特別なフラグはない。  

#### ケッコンカッコカリ後の耐久値  
ケッコン前の最大耐久値及び「最大耐久値の最大値」にのみ依存する。艦種や艦型、ケッコンのタイミングなどの影響は受けない。
 
- HP < 30 : +4
- HP < 40 : +5
- HP < 50 : +6
- HP < 70 : +7
- HP < 90 : +8
- HP >= 90 : +9  

「最大耐久値の最大値」は艦ごとに個別に設定されており、この値を超えることはない。  
殆どの艦では上記増分値を足しても問題ないほど大きく設定されているが、Bismarck drei など一部の艦娘はこれによって上昇量が制限されている。

#### パラメータの算出式  
対潜・回避・索敵の3種パラメータは、以下の式で求められる。  

```
value = min + int( ( max - min ) * Lv / 99 )  
```

なお、max = Lv99時点での値。  
「改装して最大値は変わらないが最小値が上昇する艦娘」(島風の回避など)の場合、Lv100を超えると改装前のほうが強くなることがある。  

火力・雷装・対空・装甲はレベルアップ時にランダムに上昇する(らしい)。  
なお、改装時の4種パラメータは、  

```
value = min + ceil( ( max - min ) * ( 0.4 or 0.8 ) * Lv / 99 )  
```

で求められる(0.4 or 0.8 はランダム)。  

#### 季節の衣替え艦娘  
クリスマス・年末・正月に衣装替えした艦娘のデータは ID 901 以降に保存されており、図鑑からのみ参照できる。  
~~なお、システム内では 501 以降の艦は深海棲艦として扱われるため、一応深海棲艦扱い。~~ 現在は修正済。  

2015/07/17アップデートによって、艦船マスターデータ(`api_mst_ship`)からデータが削除された。  
`api_mst_shipgraph` からのみアクセス可能となった。  

#### SuperAAItem  
以下のアイテムが該当する。

* 3: 10cm連装高角砲
* 9: 46cm三連装砲
* 10: 12.7cm連装高角砲
* 35: 三式弾
* 48: 12.7cm単装高角砲

現在は未使用?  

#### 敵艦載機グラフィック
* **1 (敵演習艦載機)**
    * IDにかかわらず、演習の艦載機は全てこれになる
* **2 (緑)**
    * 下記装備に該当しないもの
* **3 (オレンジ)**
    * 517: 深海棲艦攻 Mark.II
    * 520: 深海棲艦戦 Mark.II
    * 524: 深海棲艦爆 Mark.II
* **4 (青)**
    * 518: 深海棲艦攻 Mark.III
    * 521: 深海棲艦戦 Mark.III
    * 525: 深海棲艦偵察機
    * 526: 飛び魚偵察機
    * 544: 深海爆雷投射機 Mk.II *※522: 飛び魚艦戦の設定ミス？*
    * 546: 飛び魚艦爆
* **5 (たこ焼き艦戦)**
    * 547: 深海猫艦戦
* **6 (たこ焼き艦爆)**
    * 548: 深海地獄艦爆
* **7 (たこ焼き艦攻)**
    * 549: 深海復讐艦攻
* **8(たこ焼き水爆)**
    * 544: 深海水上攻撃機
* **9(たこ焼き水爆elite)**
    * 545: 深海水上攻撃機改
* **10(たこ焼き艦戦elite)**
    * 546: 深海猫艦戦改
* **11(たこ焼き艦爆elite)**
    * 547: 深海地獄艦爆改
* **12(たこ焼き艦攻elite)**
    * 548: 深海復讐艦攻改

#### ダメージ処理  
実際の状態にかかわらず、15未満だと常に非クリティカル, 40以上だと常にクリティカルとして表示される。  
ちなみに、「命中したが0ダメージ」という状況も存在する。  
実際に出せるかはさておき、表示上の最大値は9999。  
また、「かばう」が発動した場合は 0.1 がフラグとして加算される(もちろん実ダメージには影響しない)。  

#### ダメコン処理の優先度  

* 補強スロット ＞ メインスロット[0] ＞ … ＞ メインスロット[4]

の順で発動・消費される。戦闘糧食も同様。  
なお、発動した後は削除して詰めた後 -1 が末尾に補充される(補強スロットの場合は 0 に置換される)。  
削除処理は発動直後に行われ、同戦闘中の表示にも反映される(はず)。

#### 探照灯の優先度
発動条件(HP>1)を満たす艦が複数いた場合は、

* (大型探照灯＞探照灯)＞旗艦に近いほう

の優先度で発動させる(1隻のみ)。  
条件を満たしていれば確実に発動するため、戦闘APIに対応する値は存在しない。

#### 挟叉  
命中フラグは以下のようになっている。  

* `HITFLG_NOATTACKED` : 攻撃せず(-1)
* `HITFLG_MISS` : ミス(0)
* `HITFLG_NORMAL` : 命中(1)
* `HITFLG_CRITICAL` : クリティカル(2)
* `HITFLG_KYOUSA` : 狭叉(3) (原文ママ, 未実装?)  

#### 航空支援での敵機撃墜  
航空支援でも敵機を撃墜することは可能。  
ただし既に航空戦フェーズは終わっているので、狙ってまでする事ではなさそう。  

####レアリティの設定ミス  
羽黒改二はデータ上は金色扱い(グラフィック上は虹色)。  
武蔵(改)はデータ上は輝虹扱い(グラフィック上は桜虹、こちらは建造画面の背景が分かりやすい)  
他にもいた気がする…  
このレアリティデータは撃沈時のエフェクト表示に使用されるはずなので、撃沈させれば分かるが怖すぎてできない  

#### 撃沈・退避時のグラフィック  
R' = G' = B' = ( R + G + B ) / 3

#### ダメコン使用時の回復量
- 応急修理要員 : HP = int( HPMax * 0.2 )
    - 旗艦大破進撃時は HP = int( HPMax * 0.5 )
- 応急修理女神 : HP = HPMax ( +燃料・弾薬全回復 )  

#### 保有数による出撃時の制限  
なお、「装備空き」は補正後の値(艦これクライアントの表示値)に従う。

* 艦船がドロップしない条件  
艦船空き == 0 or 装備空き <= 3  
* イベント海域出撃不可条件  
艦船空き < 5 or 装備空き < 20

#### 攻撃種別  
* 昼戦
    * 0: 砲撃  
    * 1: レーザー攻撃
    * 2: 連続射撃
    * 3: カットイン(主砲/副砲)  
    * 4: カットイン(主砲/電探)
    * 5: カットイン(主砲/徹甲弾)
    * 6: カットイン(主砲/主砲)
    * 7: 空撃
    * 8: 爆雷攻撃
    * 9: 雷撃
    * 10: ロケット砲撃

* 夜戦
    * 0: 砲撃  
    * 1: 連続射撃
    * 2: カットイン(主砲/魚雷)
    * 3: カットイン(魚雷/魚雷)
    * 4: カットイン(主砲/主砲/副砲)
    * 5: カットイン(主砲/主砲/主砲)
    * 6: ???
    * 7: 空撃
    * 8: 爆雷攻撃
    * 9: 雷撃  
    * 10: ロケット砲撃

なお、通常 7 以降のデータは送られてこないが、以下の式によって蔵側で判定される。  

```
//昼戦の攻撃種別判定
if ( 防御側 == 陸上基地 && 対地装備 を装備している ) 攻撃種別 = 10(ロケット砲撃);
// else if ではないので後続条件が優先される
if ( 攻撃側 == 速吸改 ) {
    if ( 防御側 == ( 潜水艦 | 潜水空母 ) ) {
        if ( ( ( 対潜 > 0 の艦上攻撃機 ) | 水上爆撃機 | オートジャイロ ) を装備している )
            攻撃種別 = 7(空撃);
        else
            攻撃種別 = 8(爆雷攻撃);
    } else if ( 艦上攻撃機 を装備している )
        攻撃種別 = 7(空撃);
    else 攻撃種別 = 0(砲撃);
}
else if ( 攻撃側 == ( 軽空母 | 正規空母 | 装甲空母 ) ) 攻撃種別 = 7(空撃);
else if ( 防御側 == ( 潜水艦 | 潜水空母 ) ) {
    if ( 攻撃側 == ( 航空巡洋艦 | 航空戦艦 | 水上機母艦 | 揚陸艦 ) ) 攻撃種別 = 7(空撃);
    else 攻撃種別 = 8(爆雷攻撃);
}
else if ( 最初の表示装備(api_si_list)のカテゴリ == 魚雷 ) 攻撃種別 = 9(雷撃);
else 攻撃種別 = 0(砲撃);
```

```
//夜戦の攻撃種別判定
if ( 防御側 == 陸上基地 && 対地装備 を装備している ) 攻撃種別 = 10(ロケット砲撃);
攻撃種別 = 0(砲撃);       //このコードがあるのでロケット砲撃は発生しない? デコンパイルミス?
if ( 攻撃側 == ( 軽空母 | 正規空母 | 装甲空母 ) ) 攻撃種別 = 7(空撃);
else if ( 防御側 == ( 潜水艦 | 潜水空母 ) ) {
    if ( 攻撃側 == ( 航空巡洋艦 | 航空戦艦 | 水上機母艦 | 揚陸艦 ) ) 攻撃種別 = 7(空撃);
    else 攻撃種別 = 8(爆雷攻撃);
} else if ( 攻撃側 == ( 潜水艦 | 潜水空母 ) ) 攻撃種別 = 9(雷撃);
else if ( 最初の表示装備(api_si_list)のカテゴリ == 魚雷 ) 攻撃種別 = 9(雷撃);
else 攻撃種別 = 0(砲撃);
```

なお、「最初の表示装備」は戦闘時にカットインとして表示される装備であり、第一スロットに装備されているアイテムとは異なる場合がある。

#### 対空カットイン  
* 1：高角砲x2/電探
* 2：高角砲/電探
* 3：高角砲x2
* 4：大口径主砲/三式弾/高射装置/電探
* 5：高角砲+高射装置x2/電探
* 6：大口径主砲/三式弾/高射装置
* 7：高角砲/高射装置/電探
* 8：高角砲+高射装置/電探
* 9：高角砲/高射装置
* 10：高角砲/集中機銃/電探
* 11：高角砲/集中機銃
* 12：集中機銃/機銃/電探
* 13：???
* 14：高角砲/対空機銃/電探

(個艦については)固有優先、他はID昇順優先で判定される？  
なお、1-3は秋月型専用、10-11は摩耶改二専用。14は五十鈴改二専用。  
秋月型のみ対空電探(対空値が1以上の電探)でなくても可。  

以下の装備は「高角砲+高射装置」として扱われる：

* 122 : 10cm連装高角砲+高射装置
* 130 : 12.7cm高角砲+高射装置
* 135 : 90mm単装高角砲

#### 敵ボイス  
`areaID` `mapNo` `index` `shipID`  

#### 装備種別  
* [0]: 大分類？
* [1]: 図鑑カテゴリ/戦闘分類(`getSlotItemCardType`)
* [2]: 装備種別(`getSlotItemEquipType`)
* [3]: アイコン(`getSlotItemIconType`)

詳細については apilist.txt を参照すること。

#### 夜戦魚雷連撃  
一応実装されているらしい。  
連撃フラグが立っており、かつ攻撃表示装備[0]が( 小口径主砲/中口径主砲/大口径主砲/大口径主砲(II)/副砲 )でないときに発動する。  
そういったデータが送られてくることはないので通常の手段では見ることはできない。  

#### 対空能力の優先度？  
ある部分で使われている処理。何かの助けになるかもしれない

```
int flag = 0;
foreach ( 装備スロット ) {
	int priority = 0;
	if ( カテゴリ == 対空強化弾 ) priority = 1;
	else if ( アイコン == 高角砲 ) priority = 3;
	else if ( 装備 == 12cm30連装噴進砲 ) priority = 2;
	else if ( カテゴリ == 対空機銃 ) priority = 4;
	else if ( アイコン == 電探 && 対空 > 0 ) priority = 4;

	if ( priority > 0 && ( priority < flag || flag == 0 ) )
		flag = priority;	//優先度の高いものを選択
}
if ( flag == 2 ) flag = 3;
else if ( flag == 3 ) flag = 2;		//謎のスワップ 
return flag;
```

#### 泊地修理の仕様
(英wikiその他より。間違いがあるかも)

* 判定はタイマーが20分以上になった状態で母港に移動すると行われる。詳細は後述。
* 工作艦を旗艦に置く必要がある。第1艦隊である必要はない。
* 以下の条件を満たすとき修理は行われない。
    * 旗艦が中破以上のダメージを受けている。(?)
    * 旗艦が入渠中である。
    * 艦隊が遠征中である。
* 以下の条件を満たす艦娘は修理対象とならない。
    * 中破以上のダメージを受けている。
    * 修理の範囲外にいる。
    * 既に入渠している。
    * 修復に必要な資源が足りない。
* 編成を変更した瞬間にタイマーはリセットされる。  
言い換えれば、編成を変更した時点でタイマーのカウントは開始される。
    * 修理と無関係の艦隊の編成変更は問題ない。
* 艦隊を出撃させてもタイマーはリセットされない。
* 随伴艦を入渠させること・装備変更することはタイマーに影響しない。
* 修理中はオフラインでも構わない。
* 修理はコンディション(cond)に依存しない。
* 修理可能艦数は ( 2 + 艦艇修理施設の装備数 )。旗艦も含む。
* 20分以上経過した状態で中断すると少なくとも1回復する。そのため、高レベル・長時間入渠の艦は入渠での修理よりも早く終わることがある。
* 修理コストは*中断しない限り*入渠での修理と同じになる。  
中断した場合は入渠より多くかかることがある。
* 修理時間は分単位切り捨てで処理される。
* HP1当たりの回復時間は入渠と同じ。  
`回復量 = max( ceiling( 修理時間 / 各艦の回復単位時間 ) - 1, 1 )` となる。
* 消費資材は回復値に応じる(ceil)。消費資材が足りない場合、修理は行われない。  
但し、タイマー開始時に足りなくても修理処理時に自然回復によって資源が賄えた場合は修理が行われる(自然回復判定のほうが先)。
* 修理時間のタイマーは共通になっているらしく、複数艦隊で修理するときは注意が必要?

以下のサイトに詳しい:

* [複数艦隊の泊地修理の仕様](http://fujieda.blog.jp/archives/20645383.html)
* [明石の泊地修理の仕様 - 計時について](http://fujieda.blog.jp/archives/12988568.html)
* [明石の泊地修理の仕様 - 耐久の回復量について](http://fujieda.blog.jp/archives/13023308.html)

#### 時報の読み込みタイミング  
サーバの混雑を回避するためか、時間の5分ほど前から読み込みを開始することがある。  
詳細は未調査。  

#### 戦闘勝利判定  
確証はないが(特に撃沈艦ありのとき)、観測儀では以下のように実装*していた*。

```
味方損害率 = int( ( 味方開始時HP - 味方終了時HP ) / 味方開始時HP * 100 );
敵損害率 = int( ( 敵開始時HP - 敵終了時HP ) / 敵開始時HP * 100 );

if ( 敵損害率 >= 100 ) {
    if ( 味方損害率 <= 0 ) { //ダメコン発動時に負になる可能性がある
        rank = SS;
    } else {
        rank = S;
    }
} else if ( 敵撃沈数 >= round( 敵艦総数 * 0.6 ) ) {
    rank = A;
} else if ( 敵旗艦撃沈 || 敵損害率 > 味方損害率 * 2.5 ) {
    rank = B;
} else if ( 敵損害率 > 味方損害率 || 敵損害率 >= 50 ) {
    rank = C;
} else {
    rank = D;
}

if ( 味方に撃沈艦がある ) {
    rank = min( rank, A ) - 1;
}
```

なお、連合艦隊での判定は少なくとも「本隊だけ」ではない模様。調査中  
ver. 0.2.1 現在、観測儀では本体・随伴隊全員の損害率を計算している。  

損害率が自軍71.5%/敵軍67.6%(旗艦生存/撃沈3隻)、自軍62.2%/敵軍57.8%(旗艦生存/撃沈3隻)でC敗北となったことがある。  
敵側の損害率が一定以上だとC敗北になる？  
C敗北判定の 敵損害率 >= 50 は試験実装。  
これまでの例外判定メモ：  

* 自軍71.5%/敵軍67.6%(旗艦生存/撃沈3隻); 判定D→実際C
* 自軍62.2%/敵軍57.8%(旗艦生存/撃沈3隻); 判定D→実際C
* 自軍32.5%/敵軍32.7%(旗艦生存/撃沈2隻); 判定D→実際C

現在は航海日誌拡張版と同じ判定式。

#### 一度に大破できる艦数  
表示上は3隻?  

#### 連合艦隊における電探の渦潮被害軽減効果  
艦隊ごとに判定されるため、どちらの艦隊にも載せる必要がある。

#### '15 冬イベントの海域ゲージHP  
全て甲作戦のもの。

* E-1 : 192 (旗艦HP45; 最低5回撃破)
* E-2 : 720 (旗艦HP通常96/最終160 160以下で最終形態 最低7回撃破)
* E-3 : 1050 (旗艦HP270; 最低4回撃破)
* E-4 : 1925 (旗艦HP350; 最低6回撃破)
* E-5 : 3000 (旗艦HP500; 最低6回撃破)

#### '15 春イベントの海域ゲージHP  
全て甲作戦のもの。

* E-1 : 440 (旗艦HP88; 最低5回撃破)
* E-2 : 1620 (旗艦HP270; 最低6回撃破)
* E-3 : 910 (旗艦HP130; 最低7回撃破)
* E-4 : 3200 (旗艦HP450(最終形態500); 最低7回撃破)
* E-5 : 2410 (旗艦HP330(最終形態430); 最低7回撃破)
* E-6 : 3405 (旗艦HP500; 最低7回撃破)

#### 海域HPゲージ  
* わずかでもダメージを与えないと正常な値にならない。  
    (ex. 初期は300/300でも、30ダメージ与えると2370/2400になる、など)  
    最大HPが1000以上だと正しく取得できる？  
* '15冬イベ以前は初期値300、'15冬イベ以降は9999  
* '15春イベでは難易度を選択した時点で決定される？  
* '15春イベにて、司令部Lvによって海域HPが変化する仕様が確認されている。

#### 猫った時のAPIレスポンス  
練習巡洋艦旗艦で開発すると落ちる不具合を利用したもの。(2015/02/08)  

* `api_result` : 100  
* `api_result_msg` : "申し訳ありませんがブラウザを再起動し再ログインしてください。"

#### 特殊な装備判定(改装UI)  
Bismarck dreiに対する魚雷装備・試製51cm連装砲といったものの装備判定は、改装UIシステムの中で*例外的に決め打ち*されている。  
具体的には、

|対象艦|装備可能|装備不可能|
|:--|:--|:--|
|Bismarck drei|魚雷||
|大和(改), 武蔵(改), 長門改, 陸奥改|大口径主砲(II)||
|秋津洲|大型飛行艇|特殊潜航艇・上陸用舟艇・対地装備|
|秋津洲改|大型飛行艇・ソナー・航空要員|特殊潜航艇・上陸用舟艇|
|阿武隈改二|特殊潜航艇・上陸用舟艇||
|速吸改|艦上攻撃機・大型電探・ソナー||

となっている。  
(艦船IDが特殊装備リストに含まれていた場合は*マスターデータの内容にかかわらず*装備可能・不可能にする、というコードになっている)  
つまりある意味では不正な装備ともいえる(?)  
~~そのために新艦種を追加するのも面倒なのは分かるが、ちょっとこれはひどい気がする~~  

判定自体については RemodelMain.swf/scene/remodel/view/changeEquip/EquipList.as を参照すること。

#### 特殊な装備判定(`SLOTITEM_SPECIAL_FLAGS`)  

`api_start2`のデータでは、装備種別が[1,1,3,3]=[砲,主砲,大口径主砲,大口径主砲]となっており、なぜか大口径主砲(II)ではない。  
装備画面ではきちんと扱われているところを見ると、蔵のどこかで書き換えているらしいが…？  
2015/02/16現在、図鑑において「装備種別」「装備可能艦種」が表示されていないことから、マスターを書き換えるコードがどこかに存在するはず、である。  
2015/02/25現在、commonAssets内に`SLOTITEM_SPECIAL_FLAGS`なるオブジェクトが存在し、ID128(=試製51cm連装砲)のeqTypeを38(=大口径主砲(II))にするらしき指定がなされているのを確認。  
また、2015/07/21 オンメンテで実装された「15m二重測距儀＋21号電探改二」も同様の指定あり。  

|装備|APIのカテゴリ|実際のカテゴリ|
|:--|:--|:--|
|128(試製51cm連装砲)|3(大口径主砲)|38(大口径主砲(II))|
|142(15m二重測距儀＋21号電探改二)|13(大型電探)|43(大型電探(II))|

なお、2015/08/10('15 夏イベアップデート)で、大型電探(II)はIDが43→93に変更された。43は戦闘糧食に置換された。  
しかしcommonAssets内の指定はそのままである。  
上に「書き換えている」と予想しているが、実際は改装UI中で決め打ちで装備制限を行っているため、死にフラグの可能性もある。  
(試製51cm連装砲については改装UI中で、15m二重測距儀＋21号電探改二についてはapi_start2中で装備指定がある)  
(大型電探(II)については調査中)

#### 主要な任務一覧  

|任務ID|任務名|達成条件|追加条件|
|--:|:--|:--|:--|
|201|敵艦隊を撃破せよ！|勝利1
|216|敵艦隊主力を撃滅せよ！|戦闘1
|210|敵艦隊を10回邀撃せよ！|戦闘10
|211|敵空母を3隻撃沈せよ！|空母3
|212|敵輸送船団を叩け！|輸送5
|218|敵補給艦を3隻撃沈せよ！|輸送3
|226|南西諸島海域の制海権を握れ！|2-(1~5)ボス勝利5
|230|敵潜水艦を制圧せよ！|潜水6
|213|海上通商破壊作戦|輸送20
|214|あ号作戦|出撃36/S勝利6/ボス24/ボス勝利12
|220|い号作戦|空母20
|221|ろ号作戦|輸送50
|228|海上護衛戦|潜水15
|229|敵東方艦隊を撃滅せよ！|4-(1~5)ボス勝利12
|242|敵東方中枢艦隊を撃破せよ！|4-4ボス勝利1
|243|南方海域珊瑚諸島沖の制空権を握れ！|5-2ボスS勝利2
|261|海上輸送路の安全確保に努めよ！|1-5ボスA勝利3
|241|敵北方艦隊主力を撃滅せよ！|3-(3~5)ボス勝利5
|256|「潜水艦隊」出撃せよ！|6-1ボスS勝利3
|257|「水雷戦隊」南西へ！|1-4ボスS勝利1|要軽巡旗艦、軽巡3隻まで、他駆逐艦　他艦種禁止
|249|「第五戦隊」出撃せよ！|2-5ボスS勝利1|要「那智」「妙高」「羽黒」
|259|「水上打撃部隊」南方へ！|5-1ボスS勝利1|要(戦艦or航戦)3/軽巡1　巡戦禁止、戦艦追加禁止
|265|海上護衛強化月間|1-5ボスA勝利10
|264|「空母機動部隊」西へ！|4-2ボスS勝利1|要(空母or軽母)2/駆逐2
|266|「水上反撃部隊」突入せよ！|2-5ボスS勝利1|要駆逐旗艦、重巡1軽巡1駆逐4
|303|「演習」で練度向上！|演習3
|304|「演習」で他提督を圧倒せよ！|演習勝利5
|302|大規模演習|演習勝利20
|402|「遠征」を3回成功させよう！|遠征成功3
|403|「遠征」を10回成功させよう！|遠征成功10
|404|大規模遠征作戦、発令！|遠征成功30
|410|南方への輸送作戦を成功させよ！|「東京急行」「東京急行(弐)」成功1
|411|南方への鼠輸送を継続実施せよ！|「東京急行」「東京急行(弐)」成功6
|503|艦隊大整備！|入渠5
|504|艦隊酒保祭り！|補給15回
|605|新装備「開発」指令|開発1
|606|新造艦「建造」指令|建造1
|607|装備「開発」集中強化！|開発3
|608|艦娘「建造」艦隊強化！|建造3
|609|軍縮条約対応！|解体2
|619|装備の改修強化|装備改修1(失敗可)
|613|資源の再利用|廃棄24回
|702|艦の「近代化改修」を実施せよ！|改修成功2
|703|「近代化改修」を進め、戦備を整えよ！|改修成功15

#### 任務の特殊仕様について

* デイリー開発・建造任務はカウンタが共用。2回目の操作(3回任務の1回目)を行った時点で50%になる
* 東京急行遠征任務も共用。7回とあるが6回でok
* 輸送船5隻の変則デイリーと輸送船3隻のデイリーはカウンタが干渉。1隻撃沈で2増える
* あ号作戦の「出撃」は文字通りであり、戦闘を行う必要はない(ex. 6-1で戦艦2を含めて即帰投しても増える)

#### EnemySlotItemImage

CommonAssetsに存在する、敵艦装備グラフィックの置換処理らしきもの。  

敵艦装備のグラフィックはカットイン発動時に確認することができるが、特に指定のない場合 `EquipmentID-500` 、すなわちIDインデックスの同じ味方側装備が表示される。  
(ex. 507: 14inch連装砲 = 7 : 35.6cm連装砲)  
しかし、以下の装備は特定の装備グラフィックを持つよう指定されている（ように見える）。

|装備ID|装備名|置換装備ID|置換装備名|
|--:|:--|--:|:--|
|550|5inch連装両用莢砲|3|10cm連装高角砲|
|551|20inch連装砲|128|試製51cm連装砲|
|552|15inch要塞砲|76|38cm連装砲|
|553|4inch連装両用砲+CIC|3|10cm連装高角砲|

なお、2015/05/30現在これらを装備している艦はカットイン攻撃を行わないため、実際に確認することはできない。

#### オンメンテで実装された艦娘の扱い

オンメンテ前のテーブルで確保してある「なし」領域を埋めるように実装される。  
そのため、メンテ前の情報を保持した蔵が新規実装艦を持つ艦隊と演習しても落ちることはない。  

但しその際、当然ながら(表示上の)パラメータ及び処理は「なし」のデータに基づくため、以下のような現象が発生する。  

* 艦種は駆逐艦、艦名は「なし」
* グラフィック自体は新規艦のものが表示される
* 演習開始時のカットインでグラフィックが左上に大きくずれる
* 速力が0のため(もちろん表示上のみだが)陸上基地扱い。当然バナーも「混乱」「損壊」といった表示になる

当然ではあるが、戦闘自体はサーバ側の演算によるので全く問題はない(潜水艦が駆逐艦扱いになって砲撃する、などといったことはない)。

具体的な「なし」のパラメータについては以下のとおりである。ほぼ0で埋められている。

|パラメータ|値|
|:--|:--|
|図鑑番号|0|
|艦名|"なし"|
|読み|"なし"|
|艦種|駆逐艦|
|改装|不可|
|耐久|0(戦闘時に与えられる)|
|各種パラメータ|0|
|速力|0(陸上基地)|
|射程|無|
|レア|0|
|スロット数|0|
|搭載機数|全て0|
|建造時間|0|
|解体資源|0/0/0/0|
|近代化改修増加値|0/0/0/0|
|ドロップ文章|*(なし)*|
|搭載燃料/弾薬|0/0|
|ボイスフラグ|0|
|リソース名|(一意の文字列)|

また、新規実装ボイスを持つ艦は、母港については再読み込みしなくても新しいボイスが再生される。  
但し時報・放置ボイスまで適用されるかは未確認。データ上は未実装扱いのはずなので再生されない？  

#### 連合艦隊経験値バグ

'15 春イベで確認。  
戦闘結果で表示された入手経験値と実際に入手した経験値が食い違うバグ。  
連合艦隊戦で退避艦が存在するときに発生する。  

具体的には、第一艦隊・第二艦隊ともに退避艦があるときに  

* 第一艦隊六番艦に旗艦MVP相当の経験値  
* 第二艦隊MVP艦の経験値が非MVP艦と変わらない
* 第二艦隊五番艦・六番艦は経験値0(もちろん退避はしていない)

といった現象が発生したりする。(あくまで一例であり、状況によって異なる。)  
なお、戦闘結果画面は通常どおり(本来の仕様通り)表示される。この表示と実際の取得経験値が異なるのが問題である。  

これが発生すると、入手経験値が「増える」「減る」「0になる」の3通りの現象が発生する。  
このバグで余分に入った経験値でnext. が0以下になってもレベルアップしない。(処理が行われないらしく、next. が負値になったままの状態になる。次の戦闘でレベルアップ処理が行われる。)  
こちらは未確認だが、同様に「必要経験値未満でもレベルアップする」現象が発生するかもしれない。

以下は推測であるが、「退避艦を除外した上で計算している」のが原因であると思われる。  
例えば、以下のような取得経験値となったとする。@ はMVPである。

|第一艦隊|第二艦隊|
|--:|--:|
|1200 @|600|
|400|400|
|400|0(退避)|
|0(退避)|400|
|400|800 @|
|400|400|

本来ならばこのように処理されるべきであるが、どうやら退避艦をこのリストからスキップして詰めるらしく、以下のように処理される：

|第一艦隊|第二艦隊|
|--:|--:|
|1200 @|400|
|400|400|
|400|800|
|400|400|
|400|0 @|
|600|0|

退避艦をスキップしたため空いた2-5, 2-6は0で埋められる。  
そのうえで退避艦処理をさらに行うため、

|第一艦隊|第二艦隊|
|--:|--:|
|1200 @|*400*|
|400|400|
|400|0(退避)|
|0(退避)|400|
|400|*0 @*|
|*600*|*0*|

となる。結果として、*斜体で示した艦*の経験値がずれる。  

第二艦隊のみに退避艦がいた場合、第一艦隊は通常通りで第二艦隊の退避艦以降の艦の経験値のみがずれ込むので、多分このような処理になっていると思う。  

この不具合があるため、第二艦隊五番艦と六番艦は退避発動中は経験値を得ることができない。4隻以上退避した場合は未確認であるが、同様に経験値の不審な増減が発生するものと思われる。    
育成においては注意が必要である。  

'15 夏イベでは修正された？確認中。

なお、連合艦隊でなくても轟沈艦が存在した場合に同様の現象が起こる、との噂がある。  

#### 演習経験値バグ

演習で撃沈判定を受けた艦娘の経験値が減少するバグ。

上記経験値バグの検証の一環で、演習で撃沈判定を発生させて実験してみたところ、上に挙げたような経験値のずれは確認されなかった。  
しかし、撃沈判定を受けた艦の累積取得経験値が -1 されるという現象が発生した(戦闘結果での表示上は `+ 0 exp`)。  
確かに戦闘結果のAPIの取得経験値の項目は -1 になっていたが、直接適用されたのは予想外。  

Lv. 99の艦を以上の状態におき経験値を -1 させたところ、 Lv. 99 / 999999 exp. / next. 0 になった。  
この状態で別の戦闘を行ったところ、戦闘結果では「次のレベルまで」に何も表示されなかった(通常のLv99と同じ扱い)が、実経験値は 1000000 exp.に戻っていた。  
exp を -1 した状態でもケッコンカッコカリを行うことができる。この場合は、 Lv. 100 / 999999 exp. / next. 10001 となった。  
また、Lv. 1 / 0 exp. / next. 100 の状態でこの現象を発生させたところ、 Lv. 1 / -1 exp. / next. 101 となった。

> 注: 経験値0の大破艦は、単艦で5-5に出撃した後戦闘結果を見る前に再読み込みするとほぼ確実に入手することができる。

以上をまとめるとこのようになる。

* 演習で撃沈判定を受けると累積経験値が -1 される
* これによってレベルが下がる・レベルが上げられなくなる・ケッコンできなくなるといったことはない
* 減少した経験値はLv99でも(おそらくLv150でも)上限まで再入手できる
* Lv99で減少した経験値はケッコン後も引き継がれる(100万に補正されない) 
* 累積経験値は負値にすることができる

これによって何らかの不具合が出るかは未確認。  
経験値計算に艦の累積経験値を利用しているらしい演習において、経験値が負値の艦隊と演習したらどうなるのだろうか…

#### MVPの優先度

```
与ダメージ > 後に攻撃したほう > 6番艦に近いほう
```

と推測している。確認中である。  
与ダメージが同じ場合の判定では、少なくとも、

* 先に攻撃したほう
* 旗艦に近いほう
* 6番艦に近いほう

については反例が存在する。  

与ダメージが同じ場合の判定をこのように推測した理由を以下に示す。  
オリョクル時には6番艦側の艦がMVPを取っている。  
オリョクル(潜水艦のみの編成/昼戦のみ)の場合はダメージソースが雷撃戦のみであり、雷撃戦には攻撃順が存在しない。  
そのため、そのような場合には6番艦側が優先されると推測する。  
ただこれには反例が存在し、水上艦隊では旗艦に近い側の艦もMVPを取ることがあるという情報がある。  
よって、条件はこれだけでは不足であることが分かる。  
このことから、「後から攻撃したほう」という条件が存在し(「先に攻撃したほう」については反例があるため)、このほうが優先度が高いのではないか、と推測した。  

> 雷撃戦においても「旗艦から順に雷撃している」と考えれば、6番艦に近いほう=後から攻撃したほうが優先されるのと辻褄が合う。
> もっとも検証不足で推測の域を出ないが…

また、MVPになる条件として「与ダメージが1以上」が存在する。D敗北以上の判定で条件を満たす艦が1隻も存在しない場合(全員の与ダメージが0の場合)、後続条件にかかわらず旗艦がMVPとなる。

#### 突然の入渠API

2015/05/28(暁改二アップデート前日)、シーンにかかわらず(図鑑や出撃・戦闘中ですら)突然入渠APIが呼び出される現象が発生した。  
タイミングとしては「入渠中の艦の待ち時間が00:00:00になった瞬間」である。[画面下部の通信プログレスバー(?)が表示されたため](https://twitter.com/andanteyk/status/603934695819083777/photo/1)、正規クライアントによる通信であることは明らかである。  
起動直後は発生せず、しばらく経過すると発生していた。特定の操作をトリガーにしていた可能性がある。  
なお、その際「2回連続でndockリクエストを送る」現象も確認されている。例えば、

```
battle.request
battle.response
ndock.request
ndock.request
ndock.response
ndock.response
battleresult.request
battleresult.response
```

といった通信が行われていたことを確認している。  
時間経過とともに通信回数が増える(最初は0回(発生しない)→1回→2回(上記))。  
このような冗長な通信を正規に行うとは考えにくいため、クライアントに何らかの不具合が発生しているものと推察される。  
通信中はUI操作がブロックされるため、地味に厄介。  

[twitter](https://twitter.com/RizaSTAR/status/601763671119831041)にて、5/22時点ですでに発生していたらしき報告有。

#### next 非表示バグ
Lv148→149にレベルアップした時に、戦闘結果画面の next. がLv99・Lv150の時のように空欄になるバグ。  
サーバーからnext. のデータが送られてこないために発生する。  
この現象が発生するのはレベルアップ時のみで、以降は通常通り表示される。  

#### 応急修理要員の画像が読み込めない問題
2015/07/29現在、応急修理要員の`item_up`及び`item_character`の画像にアクセスできなくなっている。(`404 Not found` とのこと)  
画像をうっかり消してしまったのだろうか？  
wiki曰く、同年5月ごろには既にこの問題が確認されていた模様。  

 * 2014/02/03 の書き込みにもそれらしき報告がある。

#### 補強スロットについて
(応急修理要員/戦闘糧食/補給物資)を、艦娘自体の装備可能リストに積演算(and)したものが装備可能。(2015/08/18現在意味を持たないが)積演算は特殊装備判定の後に行われる。  
よって補給物資は補給艦のみ装備可能。  
この装備リストは `api_start2/api_mst_equip_exslot` を参照。  
なお、一応見張員判定は補強スロットも参照するようになっている(2015/08/18現在装備することはできない)。

#### 艦載機熟練度のデフォルト値
(熟練)及び(○○隊)はLv.2、(○○空)はLv.1になる？
確認中。


