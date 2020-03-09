# 12　IPv6 マルチキャスト

　宛先アドレスとしては、グループ
を指す 1 つの IP アドレスを使います。このグループのことをマルチキャストグループといい、
マルチキャストグループを指す IP アドレスをマルチキャストアドレスといいます。

　マルチキャストの受信側は、マルチキャストグループに「参加（ join ） 」することで、そのグ
ループに割り当てられたマルチキャストアドレス宛のデータを受け取れるようになります。そ
のマルチキャストアドレス宛のデータを受け取る必要がなくなったら、該当するマルチキャス
トグループから「離脱（ leave ） 」します。ノードによるマルチキャストグループへの参加や離
脱は、任意のタイミングで動的に可能です。

　受信側とは違い、送信側は自分が参加していないマルチキャストグループに対しても IP
マルチキャストでデータを送信することが可能です。

　IPv6 には、 IPv4 に存在したブロードキャストという仕組みがなく、 IPv4 でブロードキャス
トが担っていた役目をすべてマルチキャストが担うようになりました。

# 12.1　IPv6 のマルチキャストアドレス

　最初の 8 ビットがすべて 1 になる IP アドレス（ff00::/8）がマルチキャストアドレスです。
マルチキャストであることを示す先頭の 8 ビットに続く 4 ビット
（ 8 ビットめ～ 11 ビットめの）はフラグとして使われます。さらに後ろに続く 4 ビット（先頭
から 12 ビットめ～ 15 ビットめ）は、マルチキャストのスコープを表します。

> 図12.1 を参照

* フラグの意味（図12.2 を参照）

| ビット位置 | 記号 | 意味 |
| --- | --- | --- |
| 8 | - | 常に0 |
| 9 | R | ルータをまたぐマルチキャストを実現する場合に、その中継機器（ランデブーポイント、 Rendezvous Point ）のアドレスがマルチキャストアドレスに埋め込まれていることを表しています。 R ビットが 1 の場合のマルチキャストアドレスの構造については RFC 3956 † 4 を参照してください。|
| 10 | P | ユニキャストのネットワークプレフィックスを含むマルチキャストアドレスであることを示しています。 P が 1 の場合は T の値も 1 になります。 P が 1 の場合、 IPv6マルチキャストアドレスは図 12.3 のようなフォーマットになります。|
| 11 | T | 既知の固定アドレスであるかどうかを示しています。 IANA などによって規定されている既知のマルチキャストアドレスであれば、この部分は 0 となります。一方で、固定的ではなく一時的に利用するためのマルチキャストアドレスでは、この部分が 1 となります。 |

　複数の異なるアプリケーション
で同じマルチキャストアドレスを同時に利用すると、不必要なトラフィックがネットワーク
上を行き来してしまいます。そうしたマルチキャストアドレスの競合を回避するため、マル
チキャストアドレスについては RFC 6308 でまとめられています。マル
チキャストアドレスの自動割り当てを行う方法もあり、 RFC 3306 では、ユニキャストアド
レスのネットワークプレフィックスを用いて他のネットワークで利用されているマルチキャ
ストアドレスとの競合を防ぎつつマルチキャストアドレスを自動割り当てする方法が定義さ
れています。

　後述するように、マルチキャストには送信元を指定しない ASM （ Any-Source Multi-
cast ）と、送信元を指定する SSM （ Source Specific Multicast ）があり、マルチキャ
ストアドレスの割り振りや割り当てが大きな意味を持つのは ASM のほうです。 SSM
では、マルチキャストグループと送信元が一体として考慮されるので、マルチキャス
トグループが同じであっても送信元が別であれば異なるトラフィックとして扱われま
す。そのため SSM でマルチキャストアドレスの競合が発生する可能性があるのは、送
信元が同一になるノード内においてのみです。

　図 12.3 の plen は、ユニキャストアドレスのプレフィックス長を表します。それに続く
network prefix フィールドには、 P フラグが 1 の場合にマルチキャストアドレス中で利用さ
れるユニキャストのネットワークプレフィックスを指定します。

　その後に続くグループ識別子は、 RFC 3307 † 3 で規定された方法を用いて割り当てられます。

# 12.2　マルチキャストのスコープ

　マルチキャストのスコープは、 4.4 節で説明した IPv6 アドレスの有効範囲を示すスコー
プとは異なるので注意してください。

　IPv6 マルチキャストのスコープとしては、以下のような種類が定義されています。

| 番号 | 名前 | 意味 |
| --- | --- | --- |
| 1 | Interface-Local  | 単一のインターフェース内でのみ利用可能なマルチキャストです。インターフェース内でのループバックでのみ受け取ることが可能です。|
| 2 | Link-Local | 単一リンク内（単一サブネット内）に対して配送されるマルチキャストです。|
| 3 | Realm-Local |  RFC 7346 で追加されたもので、リンクローカルスコープより範囲が広いものの、ネットワーク体系に依存するようなマルチキャストスコープです。 RFC7346 では、ノード同士がメッシュでつながっている状況での利用例が記述されています。 |
| 4 | Admin-Local | 管理される最小単位でのネットワークを表します。|
| 5 | Site-Local | 単一サイト内でのマルチキャストです。|
| 8 | Organization-Local  | 同一組織が保持する複数サイトをまたがったマルチキャストのスコープを表します。|
| E | Global | グローバルなマルチキャストのスコープを表します。|

* T = 0 の例  
　IANA では、 NTP で利用するマルチキャストアドレスは、ff0X::101 と記述されています。従って、以下のような記述となる。

ff01::101 ： 同一インターフェース内に存在する NTP サーバ  
ff02::101 ： 同一サブネット内の NTP サーバ  
ff04::101 ： アドミンローカルスコープでの NTP サーバ  
以下略。

# 12.3　Solicited-Node マルチキャストアドレス

　Solicited-Node マルチキャストアドレスは RFC 4291 で定義されており、ユニキャストも
しくはエニーキャストに使う IPv6 アドレスの下位 24 ビットを利用したマルチキャストアド
レスです。

　上位 104 ビットを ff02::1:ff00:0 とし、下位 24 ビットをユニキャストアドレスもしくはエニーキャストアドレスの下位 24
ビットとします。たとえば、 2001:db8::01:800:200e:8c6c という IPv6 アドレスに対応す
る Solicited-Node マルチキャストアドレスは ff02::1:ff0e:8c6c です。

　IPv6 を利用するすべてのノードは、ネットワークインターフェースに対して IPv6 アドレス
を設定するとき、そのネットワークインターフェースにおいて Solicited-Node マルチキャスト
アドレスに参加することが求められます。

　Solicited-Node マルチキャストアドレスは、第 7 章で解説する近隣探索プロトコルで使われ
ます。 Solicited-Node マルチキャストアドレスを利用することによって、近隣探索プロトコル
で行われるアドレス解決を行うときに、必要なノードに対してのみマルチキャストで関連する
メッセージを届けることができます。 IPv4 では、アドレス解決には同一リンク内のすべての
ノードに対するブロードキャストが利用されています。これが IPv6 ではマルチキャストにな
るので、ネットワークやノードに対する負荷が軽減され、効率が良くなることが期待されてい
ます。

# 12.4　マルチキャストにおけるゾーン

　RFC 4007 では、 IPv6 アドレスのスコープに対応する範囲のことをゾーンと呼んでいます
（ 4.7 節参照） 。ユニキャストおよびエニーキャストではリンクローカルスコープとグローバル
スコープに対応する 2 種類のゾーンだけを使いますが、マルチキャストでは多様なゾーンが利
用されます。

# 12.5 MLD （ Multicast Listener Discovery ）

　 IPv4 では、マルチキャストグ
ループへの参加や離脱を制御するために、 IGMP と
いう IP プロトコルが利用されていました。

　IPv6 では、マルチキャストグループへの参加や離脱といったマルチキャストの制御に、
ICMPv6 の MLD という機能を利用します。

　本書執筆現在、 MLD にはバージョン 1 とバージョン 2 があります。 MLDv2 は、 MLDv1 と
の互換性を維持しつつも、特定の送信元アドレスからのマルチキャストのみを対象としてグ
ループに参加する SSM （ Source Specific Multicast ）という機能が追加されたバージョンです。

# 12.5.1　MLD で利用する ICMPv6 メッセージ

　MLD メッセージには、 Multicast Listener Query （タイプ 130 ） 、 Multicast Listener Report
（タイプ 131 ） 、 Multicast Listener Done （タイプ 132 ） の 3 種類があります。 Multicast Listener
Query メッセージは、マルチキャスト受信者が存在しているかどうかを確認するために、ルー
タがリンクに対して定期的に送信するメッセージです。 Multicast Listener Report メッセージ
は、マルチキャストの受信者となることを表明するためにノードが利用するメッセージです。
Multicast Listener Done メッセージは、マルチキャストグループからの脱退を表明するため
に使われます。

* Multicast Listener Query メッセージ

　リンク上にマルチキャスト受信者が存在しているかどうかを確認するために、ルータはタ
イプ 130 の ICMPv6 メッセージを送信します。同一リンク上で Multicast Listener Query を
送信するルータは、基本的には 1 つだけです。このルータを Querier と呼びます。ルータ
は、もし自分よりも IPv6 アドレスの数値が小さいルータから送信された Multicast Listener
Query メッセージを確認した場合、自分では Multicast Listener Query メッセージを送信し
ないルータ（ Non-Querier ）となります。

　Multicast Listener Query メッセージには、 General Query と、 Address-Specific Query の
2 種類があります。 General Query は、 Querier であるルータから定期的にリンクスコープ
全ノードマルチキャストアドレス（ ff02::1 ）宛に送信されるメッセージです。 General
Query を受信したノードは、自分が参加しているマルチキャストグループを、後述する
Multicast Listener Report メッセージでルータに伝えます。ルータが最初に起動するときに
は、リンク上に存在するすべてのマルチキャスト受信者を把握するために、 General Query
を送信することが推奨されています。

　一方、 Address-Specific Query は、特定のマルチキャストグループに存在しているノードを
知るためのメッセージです。そのため、 Address-Specific Query は、調べたいマルチキャス
トグループを指すマルチキャストアドレス宛に送信されます。

　なお、ルータで考慮するのは各リンクにマルチキャストの受信者が存在しているのかどうか
だけであり、マルチキャスト受信者の数は把握しません

* Multicast Listener Report メッセージ

　マルチキャストグループに参加するノードは、タイプ 131 の ICMPv6 メッセージである
Multicast Listener Report を送信します。ノードでは、ルータからの Multicast
Listener Query を受信すると、 Query に対応するマルチキャストアドレス宛に
Multicast Listener Report を返信します。ルータが Multicast Listener Report
を受け取り、そのリンクに該当するマルチキャストグループに参加しているノー
ドがいることを確認したら、ルータからそのリンクに対してマルチキャストを送信するよう
になるわけです。

　ただし、 Multicast Listener Query を受け取ったノードはすぐに Multicast
Listener Report を返信するわけではなく、ランダムな時間だけ待って返信し
ます。その待ち時間に他のノードから該当するマルチキャストアドレス宛に送信された
Multicast Listener Report を受け取った場合には、自分では Multicast Listener Report を送信しません。

　Multicast Listener Report は、Multicast Listener Query に対する応
答だけでなく、ノードが自発的に送信する場合もあります。 RFC 2710 では、そのリンク内
で最初にマルチキャストを受信するノードになる可能性に備えて、マルチキャストグループ
に参加したときにノードが Multicast Listener Report を送信すべきとされてい
ます。さらに、最初の Multicast Listener Report に対して障害が発生する可能
性を考慮し、間隔を空けて 1 つまたは 2 つを追加で送信することが推奨されています。

* Multicast Listener Done メッセージ

　マルチキャスト受信者は、マルチキャストグループから脱退するときに Multicast Listener
Done を送信します。

　Multicast Listener Done を受け取った Querier
は、そのマルチキャストグループに対する Multicast Listener Query を送信し
ます。この Multicast Listener Query に対する応答として Multicast Listener
Report を受け取れなかった場合は、そのリンク上に対象となるマルチキャストグ
ループの受信ノードが存在しないと判断します。その判断をするために Multicast Listener
Report の到着を待つ時間は、 Multicast Listener Done の Maximum
Report Delay フィールドで指定します。

　図 12.7 に、上記 3 種類の MLD メッセージの基本フォーマットを示します。

　MLD メッセージを配送する IPv6 パケットでは、 IPv6 ヘッダの Hop Limit が 1 である必要が
あります。これは、 MLD メッセージはリンク内で利用するためのものであり、ルータを越えな
いからです。また、 MLD メッセージを配送する IPv6 パケットの送信元 IPv6 アドレスはリンク
ローカルスコープとします。

　Maximum Response Delay フィールドは、 Multicast Listener Query メッセージに対する
Multicast Listener Report の応答の待ち時間を指定するフィールドで、 Multicast Listener
Query メッセージのみで利用されます。 Multicast Listener Report メッセージおよび Multi-
cast Listener Done メッセージでは、このフィールドはゼロに設定します。

* Router Alert オプション

　MLD パケットは、 IPv6 拡張ヘッダの Hop-by-Hop オプションに Router Alert オプションを
含めて送信されます。 Router Alert オプションは、それを受け取ったルータが IP パケットを精
査することを求めるもので、 RFC 2711 で定義されています。

　Router Alert オプションを含めることで、自分自身を宛先としない IPv6 パケットのうち中
身の確認が必要なものをルータが識別しやすくなります。 MLD では、通常のパケットを転
送するためのルータの負荷を抑制するために、 Router Alert オプションを使います。 RSVP
（ Resource Reservation Protocol ） 、 MRD （ Multicast Router Discovery ） 、 PGM （ Pragmatic
General Multicast ）などのプロトコルでも、同じ目的で Router Alert オプションが利用されます。

　Router Alert オプションのフォーマットを図 12.8 に示します。

　Value フィールドは、どのような値を運んでいるかを 16 ビットの数値で示すもので、 MLD
の場合はゼロに設定します。 Value フィールドで利用可能な値は IANA の Web サイトで公開さ
れています † 7

# 12.5.2　MLDv2

　MLDv2 は、受信者が興味のある送信元を限定することができるようにする機能を MLDv1 に
対して追加したものです。 MLDv2 は、 MLDv1 と互換性がある設計になっています。 MLDv2
は、 RFC 3810 † 11 で定義されています。

　MLDv2 は、 MLDv1 と同様の機能に加えて、マルチキャストのトラフィックの送信ノード
を指定するための INCLUDE フィルタと EXCLUDE フィルタを指定できるのが大きな特徴です。
本書では、 MLDv2 の詳細には立ち入りません。詳細は RFC 3810 を参照してください。

# 12.5.3　SSM

　送信元が特定されないマルチキャストは、ASM（Any-Source
Multicast）と呼ばれています。それに対して、マルチキャストグループと送信者の両方を指
定できるのが SSM（Source Specific Multicast）です。

　SSM は、 RFC 4607 † 12 で定義されています。 IPv6 マルチキャストにおける SSM では、 x をス
コープ識別子として、 ff3x::/32 というマルチキャストアドレスが利用されます † 13 。 IPv6 の
マルチキャストアドレスであることを示す先頭 8 ビットの ff に続く 3 は、 12.1 節で説明した P
フラグと T フラグが両方とも 1 であることを示しています。

　MLDv2 を利用して IPv6 で SSM を実現するための仕様は、 IPv4 における IGMPv3 を利用した
SSM と合わせて、 RFC 4604 † 14 で定義されています。

# 12.5.4　Lightweight MLDv2

　MLDv2 の簡易版として、 Lightweight-MLDv2 （ LW-MLDv2 ） と呼ばれるものが RFC 5790 † 15 で
定義されています。 LW-MLDv2 は、 MLDv2 の EXCLUDE フィルタを実装しないことによって、
MLDv2 の簡易版を実現するという仕様です。 MLDv2 では SSM を実現するときなどに INCUDE
フィルタを利用しますが、 EXCLUDE フィルタについては利用しない場合が多いので、 EXCLUDE
フィルタを必要としない簡易版として標準化されたのが RFC 5790 です。

# 12.5.5　MLD と DAD の問題

　MLDv1 が定義されている RFC 2710 の仕様では、 MLD メッセージの送信元 IPv6 アドレス
としてリンクローカルアドレスを使うことが要求されています。さらに、 Multicast Listener
Report メッセージを送信する際にマルチキャストグループへの参加も要求されています。こ
れらの仕様は、実は近隣探索プロトコルの仕様と競合します。

　まず、NDによる SLAAC では、アドレスの重複がない
かどうかを DAD でチェックするために複数のマルチキャストグループに参加する必要があり
ますが、その時点ではまだ送信元 IPv6 アドレスが決定していません。この仮の IPv6 アドレス
を通信に利用することはできないので、 MLD メッセージを送信する際の送信元 IPv6 アドレス
として利用可能な IPv6 アドレスが存在しないという状況が発生してしまいます。そこで、適
切な送信元 IPv6 アドレスが存在しない場合に Multicast Listener Report メッセージを未定義
IPv6 アドレス（ :: ）で送信できるようにするため、 RFC 2710 を上書きする RFC 3590 が策
定されました。

　さらに、 RFC 4862 では、後述する MLD スヌーピングを行うスイッチによっ
て SLAAC における DAD が正常に動作しない可能性があることが指摘されています。 MLD ス
ヌーピング機能があるスイッチでは、 Multicast Listener Report メッセージが送信されたあ
とに、関連するマルチキャストパケットを該当するポートに対して配送するようになります。
Multicast Listener Report メッセージを送信するまでは、 Solicited-Node マルチキャストアド
レスに送信される DAD のためのパケットが受信できない可能性があるのです。実際、電源障
害などに起因して複数のノードでネットワークインターフェースの同時設定が発生する場合
など、同一リンク上に同時に多数の Multicast Listener Report メッセージが送信されてしま
い、スイッチにおける MLD スヌーピングの処理が追いつかず、DAD が正常に機能しない可能
性があります。そのような状態を避けるため、 RFC 4862 では、ランダムな時間だけ待ってか
ら Solicited-Node マルチキャストグループへ参加することが推奨されています。

# 12.6　ルータを越えるマルチキャスト

　ネットワーク層でのマルチキャストでは、ルータを越えてマルチキャストグループに参加し
ているノードへとパケットを転送できる必要があります。そのための機能を持ったルータはマ
ルチキャストルータと呼ばれています。

　ネットワーク層でのマルチキャストでパケットの経路を決めるには、複雑な処理が必要にな
ります。ユニキャストであれば特定の宛先に対する経路（ Next hop ）が 1 つに定まりますが、
マルチキャストでは複数のサブネットにパケットをコピーして送信する必要があり、一般に複
数の Next Hop が存在するからです。受信するノードが動的にマルチキャストグループに入っ
たり抜けたりできるので、配送先が頻繁に変わる可能性もあります。

　マルチキャストルータでは、マルチキャストの Next Hop を決定するために、マルチキャス
トグループを宛先とするパケットの配送経路を示す木構造（マルチキャスト配信ツリー）を構
築します。そのために利用される仕組みとして、 PIM （ Protocol Independent Multicast ）と
呼ばれるものがあります。

　PIM には PIM-SM （ Sparse Mode ）と PIM-DM （ Dense Mode ）という 2 種類があります。 PIM-
SM では、ランデブーポイント（ RP ）と呼ばれる機能を持ったルータを設定し、マルチキャスト
の各送信元は RP までの配信ツリーを作成します。一方、サブネット内のノードから MLD で特
定のマルチキャストグループへの参加を表明されたルータは、やはり RP に対し、配下のノー
ドが当該のマルチキャストグループに参加していることを PIM で伝えます。こうして、送信側
と受信側の双方から RP に向けてマルチキャスト配信ツリーが構築されます。 RP から受信ノー
ドへの配信ツリーはすべての送信ノードで共有することになるので、このような配信ツリーを
共有ツリーと呼びます。 PIM-SM についての詳細は RFC 7761 † 18 を参照してください。

　RP から受信ノードまでの配信ツリーを共有する PIM-SM に対し、各送信ノードから受信ノー
ドまでの配信ツリーをすべて途中のルータで管理する方式が PIM-DM です。この場合の配信ツ
リーは、宛先だけでなく送信元についても経路表の要素とするので、送信元ツリーと呼ばれま
す。 PIM-DM は、受信側のノードがあまり散らばっておらず、密集（ dense ）している場合に
利用されます。 PIM-DM についての詳細は RFC 3973 † 19 を参照してください。

# 12.7　MRD （ Multicast Router Discovery ）

　リンク層が複数のスイッチで構成されている場合に、 IPv6 マルチキャストを流すと、該当す
るマルチキャストグループに参加しているノードが接続されていないスイッチにもトラフィッ
クが流れてしまう可能性があります。そのような状況で無駄なトラフィックを抑制するため
に、 MLD のメッセージをリンク層レベルで覗き見（ snoop ）し、 IPv6 マルチキャストを考慮し
てフォワーディングテーブルを設定する機能（ MLD スヌーピング）がスイッチに実装されてい
る場合があります。

　リンク層のスイッチで MLD スヌーピングを利用するには、マルチキャストグループに参加
するノードだけでなく、リンクにおけるマルチキャストルータの情報も必要になります。そこ
で、接続している環境でどのノードがマルチキャストパケットを転送するマルチキャストルー
タとして稼働しているのかを知るための仕組みが MRD （ Multicast Router Discovery ）として
標準化されています。 MRD は、 RFC 4286 † 20 で定義されています。

　IPv6 の MRD では、次の 3 種類の ICMPv6 メッセージを使うことで、マルチキャストのデー
タや MLD のメッセージを送信すべきルータをノードが発見できるようにします。

```
Multicast Router Advertisement メッセージ（タイプ 151 ）
Multicast Router Solicitation メッセージ（タイプ 152 ）
Multicast Router Termination メッセージ（タイプ 153 ）
```

　Multicast Router Advertisement メッセージは、マルチキャストルータであることを広告
するために、マルチキャストルータのすべてのインターフェースから定期的に配信されます。
ノードからの Multicast Router Solicitation メッセージへの応答として返送される場合もあり
ます。 Multicast Router Solicitation メッセージは、ノードがマルチキャストルータを探すと
きに利用されます。 Multicast Router Termination メッセージは、あるインターフェースでマ
ルチキャストルータとしての機能を停止するときに、接続しているリンクから送信されます。

　MRD に利用する ICMPv6 メッセージは、 Hop Limit が 1 のパケットによって送信されます。
その際の IPv6 パケットとしての宛先アドレスは、リ
ンクローカルスコープのマルチキャストアドレスのひとつとして IANA で割り当てられている
All-Snoopers と呼ばれるアドレスとします。 All-Snoopers は、 IPv6 では ff02::6a です（ IPv4
では 224.0.0.106 です） 。

　Multicast Router Advertisement メッセージは、マルチキャストルータであることを広告
するために、マルチキャストルータのすべてのインターフェースから定期的に配信されます。
Multicast Router Termination メッセージは、あるインターフェースでマ
ルチキャストルータとしての機能を停止するときに、接続しているリンクから送信されます。

　MRD に利用する ICMPv6 メッセージは、 Hop Limit が 1 のパケットによって送信されます。
その際の IPv6 パケットとしての宛先アドレスは、リ
ンクローカルスコープのマルチキャストアドレスのひとつとして IANA で割り当てられている
All-Snoopers と呼ばれるアドレスとします。 All-Snoopers は、 IPv6 では ff02::6a です。

# 12.7.1　MRD で利用される ICMPv6 メッセージ

　Type フィールドには、 Multicast Router Advertisement メッセージであることを示す 151
を指定します。 ICMPv6 の基本ヘッダにおける Code フィールドに相当する Ad. Interval フィー
ルドには、 Multicast Router Solicited メッセージへの応答ではない定期的な Advertisement
メッセージの送信間隔を秒単位で示します。 Query Interval フィールドには、 MLD において
General Query の Multicast Listener Query メッセージを送信する間隔を秒単位で指定します。
MLD を利用していないインターフェースではゼロを設定します。 Robustness Variable フィー
ルドには、 MLD においてリンク上でのパケットロス対策に利用される係数を指定します。

　Multicast Router Solicitation メッセージと Multicast Router Termination メッセージのパ
ケットフォーマットは単純で、それぞれ ICMPv6 の基本ヘッダのみの構造です（図 12.10 ） 。
Type フィールドは、 Multicast Router Solicitation メッセージの場合は 152 、 Multicast Router
Termination メッセージの場合は 153 です。 ICMPv6 の基本ヘッダにおける Code フィールド
は 0 に設定します

# 12.8　リンク層でのマルチキャストアドレス

　マルチキャストは、ネットワーク層だけで実現される通信方法ではありません。リンク層の
プロトコルでもマルチキャストの方法が規定されている場合があります。リンク層におけるマ
ルチキャストは、ネットワーク層におけるマルチキャストと異なり、基本的には同一セグメン
ト内のみが送信範囲です。

　ネットワーク層プロトコルの設定によって、送信される対象の機器が変わ
ることもあります。 IPv6 では、 MLD スヌーピング対応スイッチによって必要なポートに対し
てのみマルチキャストパケットを転送するようにできる場合があります。

# 12.8.1　IPv6 アドレスからイーサネットにおけるマルチキャストアドレスを生成する

　リンク層プロトコルであるイーサネットにもマルチキャストがあります。イーサネットで
は、宛先がユニキャストの場合にはネットワークインターフェースに設定されている MAC ア
ドレスを利用しますが、宛先が IPv6 マルチキャストの場合には、その IPv6 マルチキャストア
ドレスに対応するイーサネットアドレスが必要になります。

　IPv6 マルチキャストアドレスに対応するイーサネットアドレスを生成する方法は、 RFC
2464 で定義されています。 RFC 2464 では、 図 12.11 のように、 0x33 0x33 に続けて、 IPv6
マルチキャストパケットの宛先アドレスのうちの 13 オクテットめから 16 オクテットめまで
の値をイーサネットにおけるマルチキャストアドレスとして利用するとあります。たとえば、
ff02::1 という IPv6 マルチキャストアドレスに対応するイーサネットアドレスは、 33:33:
00:00:01 になります。

　ただし、その IPv6 マルチキャストの受信ノードが明確に 1 つであるとわかる場合には、リン
ク層において宛先アドレスをマルチキャストではなくユニキャストとしてもよいという拡張が
RFC 6085 で行われています。それに伴い、 IPv6 マルチキャストパケットのリンク層におけ
る宛先アドレスがユニキャストであるという理由で、 IPv6 マルチキャストの受信ノードがパ
ケットを破棄することが禁止されています。
