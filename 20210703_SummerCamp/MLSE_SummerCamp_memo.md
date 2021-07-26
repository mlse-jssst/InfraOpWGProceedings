# MLSE夏合宿2021 運用とインフラWG 討論・相談会 議事メモ

## 概要

- 日時
    - 2021/07/03 09:00-11:30
- 場所
    - Zoom
- 参加者
    - 20名弱
- アジェンダ・トピック
    - [討論会] アノテーション手法とアーキテクチャ
    - [討論会] 試行錯誤からシームレスに本番適用にもっていくためのアーキテクチャ
    - [相談会] 数分ごとなどの時間制限がある中で、速度を担保したりロバスト性について運用・インフラ面でどのように工夫しているか
    - [相談会] モデル精度の監視をどうやっているか
    - [相談会] MLチームメンバのリソース、チーム・社外パートナのリソースをどのように活用しているか

## 始めに

- WG幹事、ファシリテータの自己紹介
    - NTTデータ 土橋
    - Treasure Data 有賀
- WGについて
    - Googleによる、機械学習は利息の高いシステムである、という課題認識から、機械学習の基盤特有の要件や機能を明らかにしていきたい
        - 継続的で安定した機械学習のパイプラインを実現する、というのをより多くの企業や組織に適用できるように
    - 2020年は主に討論会とカンファレンス（事例を発表するプレゼン形式）を実施した
        - 去年の夏合宿の話題
            - 機械学習のモデルに介入することで結果が分からなくなるケースがある、監視とかどうしてる？
            - 人の意思決定の支援・効率化の工夫はあるか？
            - 開発（研究開発でいろいろモデル作っていく）と運用（プロダクションに乗せていく）でどういう切り分け方しているか？
                - アルゴリズムやアイディアだけ渡していくようなタイトな例も
        - カンファレンス
            - 事業系、様々なドメインや領域の取り組みが紹介された。パイプラインなど。
    - 有賀氏の所感、意見
        - 機械学習基盤に銀の弾丸はない
            - 数日前のAndrew Ngのパネルディスカッションにも似た話題があった
            - 組織の規模や活用フェーズによって課題がまちまち
            - ML Opsとしていろんな製品やOSSあるが、組織に合わせていかないといけない
            - Human In The Loop、人の関わり・人の依存を含めた設計が重要
                - ドメインのエキスパートじゃないがソフトウェアにかかわるケースも多い。どうやって質のいいアノテーションをお願いするのか、ユーザフィードバックの扱い方、fallback、・・・
                - 研究的に精度のいいモデルを作れる・システム開発できる、両面が無いといい設計が難しい
            - 普段の業務で解決できてないけど、、、というものを議論できるだけでも進む
    - 土橋氏より補足
        - 機械学習の歴史自体は長いが、急にシステムに組み込まれようとしているところにギャップが大きい
            - そこでいろいろ課題が出ている
            - 長い時間をかけることでうまくできるだろうが、今はその途中にある
    - [https://sites.google.com/view/sig-mlse/wg](https://sites.google.com/view/sig-mlse/wg)
        - 学習、推論など機械学習パイプラインを支えるシステムや基盤（機械学習基盤）は、アプリケーション等機能要件やレイテンシ等非機能要件に応じて多様である。一方、産業界では多くが各社のエンジニアリングによるものであり、機械学習基盤特有の課題やシステムアーキテクチャのパターンが明らかになっていない。
        - 本WGでは、1)機械学習基盤特有の機能や業務要件を考慮したシステムデザインのパターンを明らかにし、
        2)継続的で安定した機械学習パイプラインを実現できる企業や組織の数を増やすことを目指して活動を行っていく。
    - 過去の議事録
        - [https://github.com/mlse-jssst/InfraOpWGProceedings](https://github.com/mlse-jssst/InfraOpWGProceedings)
- 各自の自己紹介
    - 内容は省略。本議事メモでは、参加者の呼称はイニシャルとする。例：D氏
    - 参加者の傾向は機械学習を次サービスに組み込んでいる立場から、ベンダー・受託側まで多様。
    - 各自の取り組みフェーズ、成熟度も多様。すでに商用サービスを取り廻せているケースもあれば、研究開発が主だったり、これから商用化を目指すプロジェクトもあった。
- 討論会

## 討論会

- メイントピック
    - 「アノテーション手法とアーキテクチャ」
    - (導入説明 by ファシリテータ)
    - 企業独自の工夫が必要になってくる部分。
    - 想定される議論
        - タスク固有のアノテ
            - テキストのアノテはスプレッドシートとかでもできるが、複数で管理する場合や、データがマルチメディアの場合は工夫がいる。
        - バージョン管理や修正をどうするか。
        - アノテデータをどうやって取得するか。
        - エクスパートが手で作る場合、システムから取得するとか色々ある
    - 成功事例、失敗事例ともに議論したい
    - (ここから議論)
    - NW氏のところでは、エッジ組み込みなどで多様なデータを扱っているのかと想像するが、何か苦労などあるか？（D氏
        - MATLABExpoで自分たちが発表した事例ある。遠赤外線でセグメンテーションしてロボットを動かす
        [参考資料：遠赤外線画像へのGANの適用と自律移動ロボットの制御](https://www.matlabexpo.com/content/dam/mathworks/mathworks-dot-com/images/events/matlabexpo/jp/2020/applying-gan-to-far-infrared-image-and-controlling-autonomous-mobile-robot.pdf)
            - **可視光の映像でアノテーションしておき、それを遠赤外線の映像に生かすことで遠赤外線でのセグメンテーションを省く** ★
            - **なるべくアノテーション自体が必要ないようにしないと実際には使ってもらえないのではないか。** ★
        - 可視光を使ったモデルを転移学習している？（A氏
            - そうではない、どちらかというと知識蒸留。精度は落ちる（NW氏
            - ロボットで使うときセグメンテーションは中間データに過ぎない、セグメンテーションからロボット制御する部分はヒューリスティクスによるのでそれに差し支えない程度の精度があればいい（NW氏
            - 機械学習がコモディティになるにつれて他の部分で精度担保するという議論がある。その例に見える。他にも生かせそう（D氏
    - NR氏のところでは　運用に苦心しているとおっしゃっていた。研究だと膨大に人手があるわけではないと思うので苦労されているのではないか？データを取り扱いなおす、再学習するという点で運用にかかわるのでは（D氏
        - [参考資料：世界初！AIを高い精度のまま維持し安定運用可能な技術を開発](https://pr.fujitsu.com/jp/news/2019/10/25.html)
        - **HDL（High Durability Learning）という技術を作っている。運用時はデータがドリフトするが、いきなり大きく変わることはない、直近のデータであれば推論の精度はあまり誤差がないという前提で、その推論結果をアノテーションに使う。これによって手動でのアノテーションをなくそうとしている。（NR氏** ★
        - 正面からアノテーションするのではなく、それを回避するということ？（D氏
        - Yes（NR氏
        - 人間の手間を極力減らさないとスケールしないという点で重要（D氏
        - **ドメイン依存が大きい。どこまでのドリフトが激しい環境で使えるか、という尺度が難しい。すぐには大きく劣化しない前提だが、どれくらいの劣化なら大丈夫なのかがやってみないとわからない。PoCやってみないといけないがその工数も大きい。運用環境下での劣化の数値化ができる方法があればいいのだが。どんなデータの変化があるかも含めてデータ活用できるといいのでは。（発話者不明** ★
        - **バージョン管理とか、データの統計を取るだとか、データ基盤の裏でやっていることでアプローチできるかもしれない。世界的に見ると、データをどう取りまわるかという議論は盛り上がっており、メタデータ管理でどう情報を記述して見れるようにするかという議論は盛り上がっている。(D氏** ★
    - NS氏のところでは、コンサルをやってる認識。いろんなドメインに入って、いろんなプロジェクトでどうやっていたかみたいな知見が聞きたい。(D氏
        - コンサルというより裏方側。苦労話は聞くが、出せない情報がおおい。うまくいった情報のほうが少ない（NS氏
        - 苦労話のほうがマジョリティ？（D氏
        - そういう風に見える。運用に行く前にPoCにをやるが、そこで失敗するケースが多い。（NS氏
        - **お客様のビジネス的に、ラベルは得られるが、それが結構後になるというのが多く、速報値から順番に得られ、何段階かで確度が高くなるというケースがある。その場合、バージョン管理はどうするか、どう取り廻すかみたいな話は聞いたことがある（NS氏** ★
        - 通信周りを扱っていると、即時性が求められているものが先に飛んでくる。後から差し込まれたものをどう処理するか、というのでいつも悩んでいる。翌日朝まで作りなおすのか、12時までのデータが12時に届かない、とかあるが、本当にエレガントなのか、と疑問に思っている（D氏
    - I氏のところではPoCだと手数勝負なような気がするが、知見あるか（D氏
        - **いろんなアルゴリズム試す、いろんなデータパターン試すというところで、モデルの条件やパターンの管理がバラバラになることが多い。もともとExcelで管理していたが、デグレが多かったり、MLopsとかで実験管理を試したという経緯はあるが、それを使いまわせる人が少なかった。誰もが使えるように普及していくことの重要性を感じている。（I氏** ★
        - ML Opsっぽいツールは、みんなが使っているときに効果を発揮する。逸脱があるとその時点で壊れる（D氏
        - **よく話題になるのは、中央化した仕組みを作って広げるのか、ボトムアップで作って広げるのか、どちらがよいのかという議論はある。全社部門が作ってみんなに展開しても技術者が使ってくれず箱だけできた、みたいな。現場の課題感とのすり合わせは重要。苦労する人が誰なのか、実はデータサイエンティスト苦労してない、マネージャーが一番苦労しているみたいなケースもある。メンバーのスキルレベルがそろっていて苦労が少ないケースもある。（A氏** ★
        - こういう風にやろうとべスプラ的に決めている、背景も含めて知見が共有されている、というケースだと感じたが、そこまでもっていくのが難しいケースもある。（D氏
        - 基盤側の人間としては、基盤ってこうなんですよ、って説明するのにすごく時間がかかる。説明会とか、チュートリアルを行うことがよくある。プロダクトのGetting Startedを作るのは大変だが、それだけ渡されても現場の人は進められない。実際の使われ方を想定してこういう風に使うんですよ、という形で見せてちゃんと知識を合わせないと変な使われ方するなという印象（D氏
        - cookpad時代に、ruby技術者ばっかりだったに、Jupyterのハンズオンをして啓蒙活動すると、データ分析が進んで感謝された経験がある。基盤やツールも使ってもらって、あなたにこういう嬉しさがあるという売り込みが必要（A氏
    - ラベルを修正する場合のバージョン管理ってどうやっているのか気になっているが、知見はあるか。そんなに真面目にやってないんじゃないのか？してなくて困ったケースでも（A氏
        - **お客様からよく話はいただく。ファイルをいかに管理するかとイコールなように思える。フォルダ管理をどうするかという印象。Rawデータはいじらず、メタデータなどを追加して、ファイルで管理。バージョン更新の場合は、新しくファイルを作る。　(IM氏** ★
        - 3年前でのApple論文でも、莫大なRawデータと、小さいけど追加されるメタデータなどは、それぞれの管理の方法を導入しましょうという話があった。DatabricksのDelta Lakeでも、データそのもののバージョン管理とアノテデータのバージョン管理はどうするのかというのも重要。同じ方法で管理するのか、片方がRDBMSにするのか、ということも考えなければと思った（D氏
        - Githubにもそういう話がある。LFS（Large File Storage）（IM氏
- サブトピック
    - 「試行錯誤からシームレスに本番適用に持っていくためのアーキテクチャ」
    - 関係者間でモデルを受け渡すようなケースでどうやっているかも関心がある（D氏
    - YZ氏のところでは、まさにシステム化・本番適用する渦中で、課題があるのでは？（D氏
        - **数分おきに予測。システム化にあたり、例えばネットワーク上の問題でデータが予定通りこない、データが一件のはずが二件来るなどPoCでは考えられなかったことが起きている。きちんとやるにはどういう風にすればいいのかというのが悩み（YZ氏** ★
        - アジャイル開発部隊とコラボレーションして既存システム開発べスプラを組み込もうしている？（D氏
            - 既存の知見を取り込めていない、孤独にやっている（YZ氏
            - 開発方法論で対応していくアプローチもあるが、システム・インフラ・アーキテクチャで仕組みとして担保することもあるのでは。そういった議論もできると（D氏
    - いくつか本番適用している+本番適用を試みている。素晴らしい方法がなく苦労している。（IS氏
        - 品質担保や安定性担保が重要と考えている。研究開発していたものを本番に持ち込もうとするとエラーハンドリングや想定外のデータに耐えられないなど。こういうのに、こういうツールを使って担保するなどの工夫はあるか（D氏
            - **いろいろ工夫はしているがいいツールがあるというレベルには達していない。我々では、想定外のデータに対してあまりいい解決方法がない。直接人命にかかわるようなことはやっておらず、防災にかかわるところはシビアにやっている。自分のプロジェクト以外でも同じデータを使っているので、基本的なデータに対しては何段階も品質検査されている。プロジェクト固有のデータで問題になる。（IS氏** ★
            - 機械学習の結果の性能についてもあまりいい方法がない。学習データのチェック等はしているが、試行錯誤の面はある。想定レンジにハマるかのチェックくらいはするが、それも誤りなのかどうかははっきりわからない。大雪の予測でそこまで降ることはないだろと思っていたら本当に降った、などがある。 **業務特性上、異常に見えるものも異常だとして切り捨ててはいけない。** ★（IS氏
            - 自然現象の予測なので未来の保証はできない。合理的な知見と物理的な関係で想定しているにすぎない。（IS氏
            - **前提としているサービスや事業領域によって、品質担保の考え方が変わってくる。例えば車だと人命にかかわるので品質管理を厳格にやらないといけない、一方でレコメンデーションなど人命にかかわらないのであれば投機的にやってみるというほうがビジネス価値がある、といった形で力点が違うのでは（D氏** ★
    - K氏のところでは企画から組み込みまでを経験されていると思うが、どういうことが求められてどうやっているかというのをお話しいただけるか（D氏
        - 製造業で外観検査をされたいお客様。AIでキズ検知したり。製造業だと製品がどんどん変わるのでいちいち学習させていられないのでオンプレ（工場内のGPUサーバなど）で再学習したい（K氏
        - **PoCでは再学習までやっていない、どう対応するか。内製化するか、ネットワークの仕組みが変わったときに対応するか、など。（K氏** ★
        - オンラインにつながるのなら指標を見て通知するなどできるが、完全スタンドアロンだとどうやるか。ユーザとしてはうまく使える状態でないといけないが、うまく使えないというのをどのように検知して、そのときにどのように入れ替えるか課題（D氏
        - **製造業は短期的ドリフトと長期的ドリフトがあるように思う。短期的は機械の調子など、長期的は機械の劣化など。長期的は30年とかのスパンなので、導入3年程度だとそこまで問題になっていないが今後問題になるかもしれない（YZ氏** ★
        - 短期的と長期的のそれぞれの周期の変化が重なるイメージをもった。以前製造業のプロジェクトの経験でも同様の事を感じた。
        例えば、パッキンの劣化のような短期のものや金属の劣化のような長期のものがあるだろう。（D氏
        - 今はPoCとかするときはデータ活用のプラットフォームを入れている → DataIku。
        便利だが費用はかかる。他ではどういう風にやっているかを聞きたい（YZ氏
        - 昔は作りこむしかなかったが、ダッシュボードのような製品はOSSでも存在する。（D氏

## 相談会

- トピック：モデル精度の監視をみなどのようにしているか
    - 背景：受託開発でやっていてバラバラにやりたくない、監視可視化をツールで共通化したいが、データを入れるところはバラバラ。共通化できるところとできないところがあるのが苦労。みなさんどうされているのかを聞いてみたい。モデル的な監視とシステム的な監視とで見るものやロールも違うが合わせてみないといけない部分もあってどうしているのか聞いてみたい（NS氏
    - I氏のところではPoCでどういうの確認しているか？（D氏
        - **モデルの精度としてF値やPresicionなどをみている。アメリカのベンチャーと、PoCをうまく進める方法がないかを検討している。モデルがバグりやすいデータなのかを検知するなど（I氏** ★
    - あまりそのフェーズに行っておらず整理できていない（NK氏
    - 自分のプロジェクトも監視までいっていない（T氏
    - **まずは精度、次に公平性、説明性をどう見せるか。またそれを別の方式でカテゴライズしてAIの結果と照合する（NR氏** ★
        - **どう納得感を出すかという点で重要。エンドユーザがこれでいいと思えるか。ダッシュボードでパネルで見せるなどは効果ありそう（D氏** ★
    - コンサル的にどういうことを可視化するかを議論していたり、GCEで提供している（I氏
        - [参考資料：The ML Test Score A Rubric for ML Production Readiness and Technical Debt Reduction](https://research.google/pubs/pub46555/)
        - 相対するお客様によって指標等は変わるか？（D氏
            - 基本変わらないが、スピード・アジリティに注目する人もいるし精度も注目する人もいる（I氏
    - **システム組む人にアドバイスするにあたり、ログをとること、入力データを追えるようにすることはやっておいたほうがいい。どこでどういう判断の狂いがあったかをわかるようにする。そのなかで危なそうなデータをとり、出た場合には開発サイドが分かるように残すことが必要（NW氏** ★
        - 速報としての監視でなく、追跡するための履歴やログをとっておくことと理解。ログ自体の分析にログ分析基盤を使うと言ったメタな構造もある（D氏
    - **いろんなものをスクラッチで作るより共通化できるツールがあるといいな、と思っている。いろいろ考えた結果、ずっと監視することにはなっていない。モデルを取り込むには検査、承認がいるため、監視していてもすぐ変更できないため。定期的な運用見直しでどう変えるかを検討している。（YI氏** ★
        - 今今は、自分が考えられるものをとっている（YI氏
    - システムの監視はしていたが、モデルの監視までは行っていなかった。ただ、データサイエンティストから、SHAPで説明性を見たい、という話はあった（H氏
        - サイエンティスト側のお手製でやってるという現場もあるという印象（D氏
    - これからしようかなというところ。予測のシステムでは、精度が落ちたらメールする。監視用のダッシュボードの載せる。今はそれくらいの印象（YZ氏
- トピック:
MLチームのメンバリソース、チーム・社外パートナーリソースをどのように活用しているか
(活用し分けたり、コラボレートしているか?)
    - 自社サービスなのか、受託なのか、で話が変わってくるので、どこかで取り扱いたい（D氏
- （本相談会中では時間切れにつき、取り扱えず）

## クロージング

まとめに代えて、ファシリテータより印象に残った議論を紹介

- 土橋
    - 印象に残った議論
        - アノテも含めたパイプライン管理。アノテだけの話ではなく、ドリフトが起きた時にどうするかなど、ライフサイクル全体の話なのではと思っている。
        バックグラウンドや前提によってどう取り扱うか変わる。今回の議事録を見て、ちょっと違う観点からの意見や手法も確認できるのではと思う。
    - 得られた示唆
        - アジリティ重視なのか安全性重視なのか、短周期なのか長周期なのか、という話は印象に残っている。
        要素が違うところを、基盤側で拾って可視化できればなと思う。
        異なるような要素をうまく分割してダッシュボードに表示できれば、複合的な要因を分割して原因を拾う、みたいなことができればな思う。
- 有賀
    - 印象に残った議論
        - 製造業系の話が多かったが、他の業界と比べて、きっちりとしたプロセスがある中で、一度プロセスを更新するとうまくいかない、既存のシステムが塩漬けになるような印象がある。
        ソフトウェア的な短いサイクルで更新できればよいのではと思った。
        - 説明性や可視化のところで、PoCから脱却するフェーズでどうやったら事業部門が納得するのかという疑問を皆が持っているという印象を受けた。
        どういうものを見せたらよかったのか、(Discord上にリンクあり）
        昔の東芝の半導体の事例もあるが、どう見えるようにすればよいのか、というところを頑張らなければいけないのではと感じた。

<!-- vim: set ft=markdown et ts=4 sw=4: -->