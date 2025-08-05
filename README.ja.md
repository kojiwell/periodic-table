<!--
# Elements of System Design
-->

# システム設計の構成要素

<!--
#### [Joy Arulraj (Georgia Tech)](https://faculty.cc.gatech.edu/~jarulraj/)
-->

#### [ジョイ・アルルラジ（ジョージア工科大学）](https://faculty.cc.gatech.edu/~jarulraj/) 著

<!--
System design is often taught through  solutions specific to particular domains, such as databases, operating systems, or computer architecture, each with its own methods and vocabulary. While this diversity is a strength, it can obscure cross-cutting principles that recur across domains. This paper proposes a preliminary taxonomy of system design principles distilled from several domains in computer systems. The goal is a shared, concise vocabulary that helps students, researchers, and practitioners reason about structure and trade-offs, compare designs across domains, and communicate choices more clearly.
-->

システム設計は、多くの場合、データベース、オペレーティングシステム、コンピュータアーキテクチャといった特定の分野に特化した解決策を通じて教えられる。それぞれの分野には独自の手法や用語が存在する。この多様性は強みである一方で、分野をまたいで繰り返し現れる共通の原則を見えにくくすることがある。本稿では、コンピュータシステムに関する複数の分野から抽出したシステム設計原則の予備的な分類を提案する。目的は、学生、研究者、実務者が構造やトレードオフについて考え、異なる分野の設計を比較し、選択の意図をより明確に伝えられるような、共通で簡潔な語彙を提供することにある。

<!--
## 1. INTRODUCTION
-->

## 1. はじめに

<!--
One of the rewards of working in computer systems is the field’s sheer diversity, spanning operating systems, databases, computer architecture, distributed systems, programming languages, networking, and more, each with a rich history. For newcomers, it can be challenging to spot connections across different domains due to the diversity of traditions and vocabularies: the same design principle may appear in different guises across domains.
-->

コンピュータシステムの分野で働くことの魅力の一つは、その圧倒的な多様性にある。オペレーティングシステム、データベース、コンピュータアーキテクチャ、分散システム、プログラミング言語、ネットワーキングなど、いずれの分野にも豊かな歴史がある。初心者にとっては、それぞれの分野で使われる伝統や用語が異なるため、共通点を見つけるのが難しいことがある。同じ設計原則であっても、分野によって異なる形で現れることがあるからだ。

<!--
For example, consider the classic paper on database isolation levels by Jim Gray et al. [17]. It offers a careful account of concurrency-control mechanisms and the trade-offs between correctness and performance. Yet without prior exposure to similar issues in operating systems or computer architecture, the ideas can appear narrowly “about databases.” In reality, the same design principle, "relaxation of consistency," reappears across systems in different guises, from weakly ordered memory hierarchies to eventual-consistency protocols in distributed systems. When each community uses its own terms and exemplars, newcomers may find it difficult to recognize the underlying design principles. This fragmentation increases cognitive overhead, as the same trade-off must be relearned in each context.
-->

たとえば、Jim Grayらによるデータベースの分離レベルに関する古典的な論文[17]を考えてみるとよい。そこでは、並行制御の仕組みや、正しさと性能のトレードオフについて丁寧に説明されている。しかし、オペレーティングシステムやコンピュータアーキテクチャにおける類似の問題に触れた経験がなければ、これらの考え方は「データベースだけの話」と捉えられがちだ。実際には、「一貫性の緩和」という同じ設計原則が、弱い順序づけのメモリ階層から分散システムにおける最終的整合性プロトコルまで、さまざまな形でシステムに現れる。各コミュニティが独自の用語や例を使うため、初心者は基盤となる設計原則を認識しにくい。この断片化は認知的な負担を増やし、同じトレードオフを状況ごとに学び直さなければならなくなる。

<!--
This is a broader pattern: systems research is rich in practical insight but lighter on shared conceptual scaffolding. Across domains, similar challenges recur, managing concurrency, ensuring consistency, and adapting to change, while the framing and vocabulary often differ. As a result, deep connections between seemingly disparate domains can remain relatively obscure.
-->

これはより広い傾向を示している。システム研究は実践的な知見に富んでいる一方で、共通の概念的な枠組みはあまり整っていない。分野をまたいで、並行性の管理、一貫性の確保、変化への対応といった類似の課題が繰り返し現れるが、その捉え方や用語はしばしば異なる。その結果、一見無関係に見える分野同士の深いつながりが見えにくくなっている。

<!--
This article is a small step toward bridging those gaps. Borrowing Mendeleev’s metaphor, we present a "periodic table" of recurring system design principles. The goal is not a rigid taxonomy but a working vocabulary: a concise way to annotate papers, lectures, and design documents with the fundamental principles they employ. The aim is to surface structure that already exists in computer systems, so that students can form a more coherent mental map, researchers can situate contributions with precision, and practitioners can discuss design choices across domains with greater clarity.
-->

本稿は、そうしたギャップを埋めるための小さな一歩である。メンデレーエフの比喩を借りて、繰り返し現れるシステム設計原則の「周期表」を提示する。目指すのは厳密な分類ではなく、実際に使える語彙である。つまり、論文、講義、設計資料などにおいて、それぞれが用いる基本的な原則を簡潔に注釈できるようにすることだ。目的は、コンピュータシステムにすでに存在する構造を明らかにすることである。そうすることで、学生はより統一的な思考の地図を描けるようになり、研究者は自身の貢献を正確に位置づけられ、実務者は分野を超えて設計の選択について明確に議論できるようになる。

<!--
## 2. METHODOLOGY
-->

## 2. 調査の進め方

<!--
We identified principles by going over 100+ influential papers across operating systems, computer architecture, databases, networking, programming languages, security, and other domains in computer systems. These papers were chosen for historical significance and ongoing relevance, such as classic papers on concurrency control [17] and consensus [25], and more recent work on using machine learning inside systems [22] and designing systems for the cloud [6]. 
-->

私たちは、オペレーティングシステム、コンピュータアーキテクチャ、データベース、ネットワーキング、プログラミング言語、セキュリティなど、コンピュータシステムのさまざまな分野にまたがる影響力の大きい論文を100本以上読み、その中から原則を抽出した。選んだ論文は、並行制御に関する古典的な研究[17]や合意形成に関する論文[25]、システム内部での機械学習の活用に関する最近の研究[22]、クラウド向けの設計に関する研究[6]など、歴史的に重要であり今も関連性の高いものを含んでいる。

<!--
For each paper we asked: what is the underlying high-level design principle? Across domains, independent systems often converged not on mechanisms but on shared design principles: for example, relaxing consistency to improve performance or lifting abstractions to enhance usability. 
-->

各論文について、根底にある高レベルな設計原則は何かを検討した。異なる分野にまたがっていても、個別のシステムはしばしば、仕組みではなく共通の設計原則に収束していた。たとえば、性能を高めるために一貫性を緩めることや、使いやすさを向上させるために抽象度を上げることなどである。

<!--
To qualify as a system design principle, it must satisfy two conditions:
-->

システム設計原則として認められるには、次の二つの条件を満たす必要がある：

<!--
1. **Abstract** – The principle must be independent of specific technologies or implementations.  
2. **Generality** – The principle must show up across different domains (e.g., database systems, operating systems, programming languages).
-->

1. **抽象的であること** ― 原則は特定の技術や実装に依存していないこと。
2. **一般性があること** ― 原則が異なる分野（例：データベースシステム、オペレーティングシステム、プログラミング言語）にまたがって現れること。

<!--
This analysis does not aim to catalogue every principle, but to surface many of those with lasting, general-purpose value.
-->

この分析の目的は、すべての原則を網羅することではなく、長く使える汎用的な価値を持つ多くの原則を明らかにすることにある。

<!--
## 3. THE DESIGN PRINCIPLE TABLE
-->

## 3. 設計原則の表

<!--
We have curated a structured set of 40+ general-purpose design principles distilled from the systems literature. As shown in Table 1, they are organised into thematic groups that mirror familiar axes of system design.
-->

私たちは、システムに関する文献から抽出した40以上の汎用的な設計原則を整理し、構造的な形でまとめた。表1に示すように、これらの原則は、システム設計においてよく知られた観点に沿ったテーマ別のグループに分類されている。

<!--
Each principle is tagged with a short symbol (e.g., `Co` for composability, `Op` for optimistic design) for quick reference. We emphasise **design intent** rather than prescribing mechanisms: instead of “use this locking protocol” or “optimise this query plan,” the principles state aims such as “preserve correctness under concurrency” or “prioritise the common case,” leaving concrete realisations to specific domains.
-->

各原則には、素早く参照できるように短い記号（例：「Co」は構成可能性、「Op」は楽観的設計）を付けている。ここで重視しているのは、仕組みではなく**設計の意図**である。「このロックプロトコルを使うべき」や「このクエリ計画を最適化すべき」といった指示ではなく、「並行性の中でも正しさを保つ」や「よくあるケースを優先する」といった目標を示し、具体的な実現方法は各分野に委ねている。

<!--
## Table of Contents
-->

## 目次

<!--
- [🟪 Group 1: Structure](#-group-1-structure): *How to carve and connect parts with clear boundaries and extension points.*
- [🟧 Group 2: Efficiency](#-group-2-efficiency): *Do less work, or do it cheaper, by focusing effort where it pays.*
- [🟨 Group 3: Semantics](#-group-3-semantics): *Specify behavior and interfaces precisely.*
- [⬛ Group 4: Distribution](#-group-4-distribution): *Coordinate work and data across distributed architectures.*
- [🟩 Group 5: Planning](#-group-5-planning): *Select plans automatically from goals, costs, and constraints.*
- [🟦 Group 6: Operability](#-group-6-operability): *Observe, adapt, and evolve running systems with minimal disruption.*
- [🟥 Group 7: Reliability](#-group-7-reliability): *Stay correct under faults, concurrency, and partial failure.*
- [🟫 Group 8: Security](#-group-8-security): *Bound authority and enforce isolation to preserve safety and integrity.*
-->

- [🟪 グループ 1](#-group-1-structure-ja)：構造：明確な境界と拡張ポイントを持つように、要素をどう分け、どうつなぐか。
- [🟧 グループ 2](#-group-2-efficiency-ja)：効率：効果の高い部分に力を集中させることで、作業量を減らすか、コストを下げる。
- [🟨 グループ 3](#-group-3-semantics-ja)：セマンティクス/意味：動作やインターフェースを正確に定義する。
- [⬛ グループ 4](#-group-4-distribution-ja)：分散：分散アーキテクチャ全体で作業とデータをうまく連携させる。
- [🟩 グループ 5](#-group-5-planning-ja)：計画：目標、コスト、制約に基づいて自動的に計画を選ぶ。
- [🟦 グループ 6](#-group-6-operability-ja)：運用性：稼働中のシステムを観察し、適応させ、最小限の混乱で進化させる。
- [🟥 グループ 7](#-group-7-reliability-ja)：信頼性：障害や並行処理、部分的な失敗の中でも正しく動作し続ける。
- [🟫 グループ 8](#-group-8-security-ja)：セキュリティ：権限を制限し、分離を徹底することで、安全性と整合性を保つ。

<!--
**Legend:** `Code` = unique short symbol, `Name` = principle, `Intent` = short description.
-->

**凡例：** `Code` = 固有の短い記号, `Name` = 原則の名前, `Intent` = 簡潔な説明。

<img width="408" height="423" alt="table" src="https://github.com/user-attachments/assets/f35f47fd-52b0-4ea9-9c85-5fd43d0030f6" />

<!--
## 🟪 Group 1: Structure
-->

## 🟪 グループ 1: Structure (構造) {#-group-1-structure-ja}

<!--
🟪 **Si – Simplicity**

Choose the simplest system design that meets current needs; resist complexity, such as additional layers, services, or generality added "just in case", until evidence shows benefit. 

**Example:** Avoid premature architectural optimisation of the system [23].
-->

🟪 **Si – Simplicity (単純さ)**

現在のニーズを満たす中で最も単純なシステム設計を選ぶこと。「念のため」に追加される層、サービス、汎用性など、複雑さは明確な利点が見えるまで避けるべきである。

**例：** システムの早すぎるアーキテクチャ最適化は避ける[23]。

<!--
🟪 **Mo – Modularity**

Partition the system into cohesive units with minimal interfaces, so that each unit can be reasoned about, replaced, or evolved independently. This principle focuses on decomposition: choosing boundaries to favor clear separation of concerns so that each responsibility sits in one module.

**Example:** The OSI model decomposes communication into standardised layers with well-defined boundaries that permit independent development and substitution [48].
-->

🟪 **Mo – Modularity (モジュール性)**

システムを、結びつきの強い単位に分け、インターフェースを最小限に抑えることで、それぞれの単位を個別に理解し、交換し、発展させやすくする。この原則は分解に重点を置いており、責任が明確に分かれたモジュールごとに機能を整理するための境界を適切に選ぶことを重視する。

**例：** OSIモデルは通信を標準化された層に分け、明確な境界によってそれぞれを独立に開発・置き換えできるようにしている[48]。

<!--
🟪 **Co – Composability**

Design components that can be safely and flexibly recombined; rely on explicit contracts and type-constrained interfaces so that every legal composition remains correct, letting components be assembled like interchangeable bricks. Unlike modularity, this principle focuses on re-composition: making sure the components can be combined safely and flexibly.

**Example:** Unix programs (e.g., grep, sort, uniq) read from stdin and write to stdout, letting the user compose complex text processing pipelines [41].
-->

🟪 **Co – Composability (構成可能性)**

部品を安全かつ柔軟に組み合わせられるように設計すること。明示的な契約や型制約のあるインターフェースに基づくことで、合法な組み合わせが常に正しく動作し、部品を積み木のように自由に組み立てられるようにする。モジュール性が「分けること」に重点を置くのに対し、この原則は「組み合わせ直すこと」に重点を置く。

**例：** Unixのプログラム（例：grep、sort、uniq）は標準入力から読み、標準出力に書き出すため、複雑なテキスト処理パイプラインを柔軟に組み合わせて構築できる[41]。

<!--
🟪 **Ex – Extensibility**

Design systems to allow safe user-defined extensions, such as plug-ins, without requiring changes to the system core. When extensions come from untrusted parties, isolate them through sandboxing to preserve safety.

**Example:** Unix also illustrates extensibility: new programs can be added by the user without kernel changes [41].
-->

🟪 **Ex – Extensibility (機能の追加容易性)**

システムの設計は、プラグインなどのユーザー定義の拡張をシステムの中核を変更することなく安全に追加できるようにするべきである。信頼できない第三者からの拡張がある場合には、サンドボックス化によって隔離し、安全性を確保することが求められる。

**例：** Unixはこの追加容易性（extensibility）の好例でもある。新しいプログラムは、カーネルを変更せずにユーザーが追加することができる [41]。

<!--
🟪 **Pm – Policy/Mechanism Separation**

Separate what should be done (policy) from how it is carried out (mechanism) by exposing a common interface through which multiple policies can plug into the same mechanism.

**Example:** Hydra has a kernel of generic mechanisms (scheduling, paging, protection) and moved resource-allocation policies to user-level modules [32].
-->

🟪 **Pm – Policy/Mechanism Separation (ポリシーとメカニズムの分離)**

「何をすべきか」（ポリシー）と「どう実行するか」（メカニズム）を分離し、共通のインターフェースを通じて、複数のポリシーが同じメカニズムに接続できるようにする。

例： Hydraは、スケジューリング、ページング、保護といった一般的なメカニズムをカーネルに持ち、リソース割り当てのポリシーはユーザーレベルのモジュールに移している[32]。

<!--
🟪 **Gr – Generalized Design**

Design a single core with explicit variation points like types, knobs, or plug-ins, so that it can serve many use cases without duplication, but specialise when doing so yields meaningful gains in performance, accuracy, or clarity.

**Example:** The C++ Standard Template Library is a collection of containers, iterators, and algorithms parameterized by templates [45]. Postgres allows users to add types and operators to the core database system [46].
-->

🟪 **Gr – Generalized Design (汎用的設計)**

型や設定パラメータ、プラグインなどの明示的な可変点を持つ単一の中核を設計し、重複なく多くの用途に対応できるようにする。ただし、性能や精度、明確さに大きな利点がある場合には、用途ごとの最適化を行う。

**例：** C++の標準テンプレートライブラリは、テンプレートによってパラメータ化されたコンテナ、イテレータ、アルゴリズムの集合である[45]。Postgresは、ユーザーがコアのデータベースに型や演算子を追加できるようにしている[46]。

<!--
## 🟧 Group 2: Efficiency
-->

## グループ 2： Efficiency 効率 {#-group-2-efficiency-ja}

<!--
🟧 **Sc – Scalability**

Design the system to handle growth in data, traffic, or nodes with near-linear cost or latency.

**Example:** MapReduce scales across nodes by dividing work into parallel tasks and aggregating results with minimal coordination [10].
-->

🟧 **Sc – Scalability (拡張性)**

データ量、トラフィック、ノード数の増加に対して、コストや遅延がほぼ直線的に増えるようにシステムを設計する。

**例：** MapReduceは、作業を並列タスクに分割し、最小限の調整で結果を集約することで、ノード全体にわたってスケーラブルに処理を行っている[10]。

<!--
🟧 **Rc – Reuse of Computation**

Avoid redundant work by caching, materializing intermediate results (e.g., indexes), or incrementally updating outputs across repeated or slightly modified inputs, saving computation.

**Example:** A B+tree reuses its sorted key order: lookups follow the existing search path instead of rescanning the entire data set each time, thereby reusing computation [2].
-->

🟧 **Rc – Reuse of Computation (計算の再利用)**

キャッシュや中間結果の保存（例：インデックス）、または入力が繰り返されたり少し変更された場合に出力を増分的に更新することで、無駄な計算を避ける。

**例：** B-treeは、ソートされたキーの順序を再利用しており、検索時には毎回全データを走査するのではなく、既存の探索経路をたどることで計算を再利用している[2]。

<!--
🟧 **Wv – Work Avoidance**

Skip computation that would not alter externally observable results. Examples include lazy evaluation and predicate short-circuiting.

**Example:** Lazy evaluation defers work until a value is demanded, eliminating useless computation [19].
-->

🟧 **Wv – Work Avoidance (作業の回避)**

外から見える結果に影響を与えない計算は行わないようにする。たとえば、遅延評価や条件の短絡評価などが該当する。

**例：** 遅延評価は、値が実際に必要になるまで計算を先送りすることで、無駄な処理を省いている[19]。

<!--
🟧 **Cc – Common-Case Specialization**

Detect the execution paths or data items that dominate run-time ("hot spots") and create a streamlined fast path just for them, while a slower, general path still handles every case correctly.

**Example:** Caching the target method for the receiver class on the first call, so that subsequent calls on that common receiver hit the fast path; uncommon classes fall back to the full method-lookup routine [5].
-->

🟧 **Cc – Common-Case Specialization (典型的な入力に合わせた最適化)**

実行時によく使われる処理やデータ（ホットスポット）を見つけ出し、それ専用の高速な経路を用意する。一方で、すべてのケースを正しく処理できる一般的な経路も残しておく。

**例：** 最初の呼び出し時に受け手クラスに対するメソッドをキャッシュしておき、以降の呼び出しではそのキャッシュにより高速に処理する。まれなクラスの場合は通常のメソッド検索処理に戻る[5]。

<!--
🟧 **Bo – Bottleneck-Oriented Optimisation**

Profile end-to-end performance, locate the tightest resource constraint, and focus improvement effort there until another stage becomes the limiter.

**Example:** Rare 99th-percentile stragglers bottleneck latency, and replicated requests help cut tail response times [9].
-->

🟧 **Bo – Bottleneck-Oriented Optimisation (ボトルネック重視の最適化)**

システム全体の性能を計測し、最も厳しいリソース制約がある部分を特定し、そこに集中的に最適化を行う。別の部分が新たな制約になるまで、改善を繰り返す。

**例：** 全体の遅延を悪化させる99パーセンタイルの遅い処理（ストラグラー）に対しては、リクエストの複製によって応答時間の下限を削減する方法が使われている[9]。

<!--
🟧 **Ha – Hardware-Aware Design**

Shape algorithms and data structures to the latency, bandwidth, parallelism, and persistence properties of underlying hardware (e.g., cache hierarchy, NUMA, SSDs, GPUs).

**Example:** BLAS defines cache- and vector-tuned kernels so linear-algebra code exploits hardware efficiently [31].
-->

🟧 **Ha – Hardware-Aware Design (ハードウェアを意識した設計)**

アルゴリズムやデータ構造を、基盤となるハードウェアの特性（レイテンシ、帯域幅、並列性、永続性など）に合わせて設計する。たとえば、キャッシュ階層、NUMA構成、SSD、GPUといった要素を考慮に入れる。

**例：** BLASは、キャッシュやベクトル処理に最適化されたカーネルを定義し、線形代数のコードがハードウェアを効率よく活用できるようにしている[31]。

<!--
🟧 **Op – Optimistic Design**

Proceed as if the common case will succeed, skipping coordination, and rely on a (possibly expensive) recovery path only when that assumption proves wrong.

**Example:** Optimistic Concurrency Control runs transactions lock-free, then validates at commit and rolls back only when a conflict is detected [24].
-->

🟧 **Op – Optimistic Design (楽観的設計)**

よくあるケースがうまくいくと仮定して先に進み、同期などを省略する。もしその仮定が間違っていた場合に限り、高コストでも復旧処理に頼る。

**例：** 楽観的並行制御では、トランザクションをロックなしで実行し、コミット時に検証を行い、衝突が見つかった場合にだけロールバックする[24]。

<!--
🟧 **La – Learned Approximation**

Replace hand-crafted algorithms with models trained on data, trading bounded inaccuracy for efficiency or flexibility.

**Example:** The perceptron branch predictor learns weights online to forecast branch outcomes, outperforming fixed two-bit counters without enlarging the table [22].
-->

🟧 **La – Learned Approximation (学習による近似)**

手作業で作られたアルゴリズムの代わりに、データから学習したモデルを使い、ある程度の誤差と引き換えに効率や柔軟性を得る。

**例：** パーセプトロン分岐予測器は、分岐の結果を予測するために重みをオンラインで学習し、固定の2ビットカウンタよりも高い精度を保ちつつ、テーブルサイズを増やさずに済ませている[22]。

<!--
## 🟨 Group 3: Semantics
-->

## 🟨 グループ 3: Semantics (セマンティクス／意味) {#-group-3-semantics-ja}

<!--
🟨 **Al – Abstraction Lifting**

Wrap low-level operations behind a higher-level interface or domain-specific language that expresses intent rather than steps. This enables internal optimization and also allows a single definition to target diverse back-ends.

**Example:** SQL queries declare the result to retrieve; the DBMS chooses access paths, join orders, and physical operators automatically \[44].
-->

🟨 **Al – Abstraction Lifting (抽象化の引き上げ)**

低レベルの操作を、高レベルのインターフェースや特定分野向けの言語で包み、処理手順ではなく意図を表現するようにする。これにより、内部の最適化が可能になり、同じ定義を使って複数のバックエンドに対応できるようになる。

**例：** SQLクエリは取得したい結果を宣言的に記述し、DBMSがアクセス経路、結合順序、物理演算子などを自動的に選択する[44]。

<!--
🟨 **Lu – Language Homogeneity**

Adopt a single, well-specified intermediate representation (or language) across core components and extensions so semantics align, tools compose, and cross-layer optimisations and reuse happen with minimal effort.

**Example:** LLVM exposes a typed, SSA-based IR that many front ends target and many back ends share, enabling cross-language optimisation and reuse of the same middle-end passes [30].
-->

🟨 **Lu – Language Homogeneity (言語の統一性)**

中核の構成要素や拡張機能にわたって、意味がそろい、ツールが組み合わせやすく、階層をまたいだ最適化や再利用が簡単にできるように、単一で明確に定義された中間表現（あるいは言語）を採用する。

**例：** LLVMは型付きのSSAベースの中間表現を提供しており、多くのフロントエンドがこれを出力し、多くのバックエンドがこれを利用することで、言語をまたいだ最適化や同じ中間処理の再利用が可能になっている[30]。

<!--
🟨 **Se – Semantically Explicit Interfaces**

Specify an interface precisely (covering effect visibility, ordering, durability, etc.) so that users can reason about a call’s true externally observable state without guessing about hidden buffering or replication.

**Example:** SQL isolation levels specify precise anomaly semantics and make visibility guarantees explicit [3].
-->

🟨 **Se – Semantically Explicit Interfaces (意味が明示されたインターフェース)**

副作用の可視性、順序、永続性などを含めて、インターフェースの意味を正確に定義することで、ユーザーが隠れたバッファリングやレプリケーションの有無を推測せずに、呼び出しによって外から観察できる状態を正しく理解できるようにする。

**例：** SQLの分離レベルは、具体的な異常の意味を定め、可視性に関する保証を明示している[3]。

<!--
🟨 **Fs – Formal Specification**

Describe system behaviour using mathematical models or logic to support rigorous reasoning, verification, or synthesis. Mechanisms for realizing this principle include temporal logic, state machines, and other formalisms that make system properties analyzable.

**Example:** TLA+ shows how to specify and check systems using logic and set theory to catch design errors before coding \[27].
-->

🟨 **Fs – Formal Specification (形式的な仕様)**

システムの動作を数学的モデルや論理で記述し、厳密な検証や設計の正しさの確認、あるいは合成を可能にする。この原則を実現する手段には、時相論理、状態機械、その他の形式手法があり、システムの性質を分析可能にする。

**例：** TLA+は、論理や集合論を使ってシステムを記述・検証する方法を提供し、コーディング前に設計ミスを発見できるようにしている[27]。

<!--
🟨 **Ig – Invariant-Guided Transformation**

Use formally stated invariants to drive safe refactoring, optimisation, or reconfiguration.

**Example:** In compilers, SSA treats "one definition per name" as an IR invariant; passes rewrite code while preserving semantics and then re-establish SSA \[8]. In query optimisers, relational-algebra equivalences (e.g., selection/projection pushdown) preserve result semantics \[44].
-->

🟨 **Ig – Invariant-Guided Transformation (不変条件に基づく変換)**

形式的に定義された不変条件をもとに、安全なリファクタリング、最適化、再構成を行う。

**例：** コンパイラでは、SSAが「各名前に対して定義は1つ」という不変条件を持つ中間表現とし、各パスが意味を保ったままコードを書き換えた後、SSAの状態を再構築する[8]。クエリ最適化では、選択や射影のプッシュダウンのような関係代数の等価変換が、結果の意味を保ったまま処理を最適化する[44]。

<!--
## ⬛ Group 4: Distribution
-->

## ⬛ グループ 4： Distribution (分散) {#-group-4-distribution-ja}

<!--
⬛ **Lt – Location Transparency**

Hide the physical whereabouts of resources so clients interact via uniform names or handles.

**Example:** Programs can call remote procedures as if they were local, masking host location \[4].
-->

⬛ **Lt – Location Transparency (位置の透過性)**

リソースの物理的な場所を隠し、クライアントが一貫した名前やハンドルを通じて操作できるようにする。

例： プログラムがリモート手続きをローカルのように呼び出せることで、ホストの場所を意識せずに済む[4]。

<!--
⬛ **Dc – Decentralised Control**

Distribute decision-making among many nodes to avoid single points of failure or bottlenecks.

**Example:** Dynamo partitions data via consistent hashing and uses gossip-based membership, avoiding any central coordinator \[12].
-->

⬛ **Dc – Decentralised Control (分散制御)**

意思決定を複数のノードに分散させ、単一障害点やボトルネックを避ける。

**例：** Dynamoは、コンシステントハッシュでデータを分散し、ゴシッププロトコルによるメンバー管理を行うことで、中央のコーディネータを必要としない[12]。

<!--
⬛ **Fp – Function Placement**

Place functionality where the necessary context and resources exist to achieve correctness and efficiency, avoiding redundant work elsewhere.

**Example:** The end-to-end argument shows that functions like reliability checks achieve correctness only at the endpoints \[42].
-->

⬛ **Fp – Function Placement (機能の配置)**

正しさと効率を保つために、必要な情報やリソースがある場所に機能を配置し、他の場所での無駄な処理を避ける。

**例：** エンドツーエンド原則では、信頼性チェックのような機能はエンドポイントでのみ正しく実現できることを示している[42]。

<!--
⬛ **Lo – Locality of Reference**

Place related data and operations close together in time and space to preserve access patterns and minimize separation between computation and state.

**Example:** The working-set model formalises temporal locality to keep hot pages in memory \[11].
-->

⬛ **Lo – Locality of Reference (参照の局所性)**

関連するデータや処理を時間的・空間的に近くに配置し、アクセスの流れを保ち、計算と状態の間の距離を最小限にする。

**例：** ワーキングセットモデルは、時間的局所性を形式化し、頻繁に使われるページをメモリ内に保持するようにしている[11]。

<!--
⬛ **Cz – Coordination Avoidance**

Design computations and dataflows to reduce the need for distributed coordination by identifying operations that can proceed independently while preserving application-level correctness.

**Example:** CRDTs allow replicas to update independently and merge states deterministically, guaranteeing convergence without runtime coordination [49].
-->

⬛ **Cz – Coordination Avoidance (協調の回避)**

アプリケーションレベルの正しさを保ちながら、処理やデータの流れを設計して、分散環境での同期や調整を最小限に抑える。独立に進められる操作を見極めることが重要である。

**例：** CRDTは、各レプリカが独立して更新を行い、その状態を決定的にマージすることで、実行時の協調なしに収束を保証している[49]。

<!--
## 🟩 Group 5: Planning
-->

## 🟩 グループ 5：Planning (計画) {#-group-5-planning-ja}

<!--
🟩 **Ep – Equivalence-based Planning**

Apply algebraic/logic rewrite rules over a common IR that preserve semantic equivalence; defer final choice to later cost/constraint stages.

**Example:** Starburst’s rule-based rewrite system applies relational equivalences (e.g., predicate pushdown) to generate logically equivalent queries \[39].
-->

🟩 **Ep – quivalence-based Planning (同値変換による計画)**

意味を保ったまま書き換える代数的・論理的な変換ルールを、共通の中間表現に対して適用し、最終的な選択はコストや制約の評価段階まで先送りにする。

**例：** Starburstのルールベース書き換えシステムは、述語のプッシュダウンなどの関係代数的な等価変換を用いて、意味的に同じクエリを生成する[39]。

<!--
🟩 **Cm – Cost-based Planning**

When a system must choose among alternative designs, configurations, or execution strategies, use a cost model to guide the search toward low-cost solutions (energy, money, etc.) without needing to enumerate the full space.

**Example:** The Selinger query optimizer selects the lowest-cost plan under a cost model \[44].
-->

🟩 **Cm – Cost-based Planning (コストベースの計画)**

システムが複数の設計、構成、実行戦略から選ぶ必要があるとき、すべての選択肢を列挙することなく、コストモデルを使ってエネルギーや金銭などのコストが低い解を見つけるようにする。

**例：** Selingerのクエリオプティマイザは、コストモデルに基づいて最もコストの低い実行計画を選択する[44]。

<!--
🟩 **Cp – Constraint-based Planning**

Encode decisions and hard or soft constraints and rely on a solver (ILP/SMT etc.) to find a feasible or optimal assignment.

**Example:** Quincy formulates cluster scheduling as a min-cost flow with locality and fairness constraints and solves it to obtain an assignment \[21].
-->

🟩 **Cp – Constraint-based Planning (制約ベースの計画)**

意思決定や制約（厳密なものや緩やかなもの）を形式的に記述し、ILPやSMTなどのソルバーにより、実行可能または最適な割り当てを求める。

**例：** Quincyは、クラスタスケジューリングを局所性や公平性の制約を含む最小コストフローとして定式化し、それを解くことで割り当てを決定している[21]。

<!--
🟩 **Gd – Goal-Directed Planning**

Accept a declarative description of the desired end-state and automatically synthesise a concrete sequence of operations to reach it, shielding the user from implementation details.

**Example:** The Cascades query optimizer turns an SQL query (the goal) into an executable plan via rule-based transformation and cost-guided search \[14].
-->

🟩 **Gd – Goal-Directed Planning (目標指向の計画)**

最終的に目指す状態を宣言的に記述し、それを達成するための具体的な操作の流れを自動的に生成することで、ユーザーが実装の詳細を意識せずに済むようにする。

**例：** Cascadesのクエリオプティマイザは、SQLクエリという目標から、ルールに基づく変換とコスト指向の探索を通じて実行可能な計画を自動的に導き出す[14]。

<!--
🟩 **Bb – Black-Box Tuning**

When analytic cost models are not available, search the plan/configuration space by measuring candidates on the target system, iteratively choosing better ones (e.g., heuristic or Bayesian search), and caching the winner.

**Example:** ATLAS empirically times candidate BLAS kernel configurations on the target CPU and fixes the best-performing parameters, without an analytic cost model \[47].
-->

🟩 **Bb – Black-Box Tuning (ブラックボックス型のチューニング)**

分析的なコストモデルが使えない場合、実際に候補をターゲットシステム上で測定しながら、より良いものを順に選んでいく（ヒューリスティック検索やベイズ最適化など）。最も良かった設定は記録して再利用する。

**例：** ATLASは、BLASカーネルの候補設定を実機のCPUで実際に計測し、最も性能が良かったパラメータを採用する。分析的なコストモデルは使っていない[47]。

<!--
🟩 **Ah – Advisory Hinting**

Provide non-binding hints that systems may exploit to improve performance, without changing correctness or requiring enforcement.

**Example:** Lampson advocates optional "hints" that help performance but must not affect correctness if ignored \[29].
-->

🟩 **Ah – Advisory Hinting (助言的ヒント)**

システムが性能向上のために活用できるが、無視しても正しさには影響しないような、拘束力のないヒントを与える。

**例：** Lampsonは、性能向上に役立つが無視しても正しく動作する「ヒント」の活用を提唱している[29]。

<!--
## 🟦 Group 6: Operability
-->

## 🟦 グループ 6：Operability (運用性) {#-group-6-operability-ja}

<!--
🟦 **Ad – Adaptive Processing**

Monitor runtime conditions and automatically adjust parameters or strategy.

**Example:** Eddies continuously reorder query operators at runtime based on feedback, adapting without stopping execution \[1].
-->

🟦 **Ad – Adaptive Processing (適応的な処理)**

実行時の状況を監視し、それに応じてパラメータや戦略を自動で調整する。

**例：** Eddiesは、実行を止めることなく、実行中にフィードバックを元にクエリ演算子の順序を継続的に入れ替え、動的に適応している[1]。

<!--
🟦 **Ec – Elasticity**

Automatically adjust resource allocation in response to shifting demand and cost goals. Examples include predictive autoscaling and load shaping.

**Example:** Chase et al. dynamically provision servers based on load and utility, exemplifying elastic resource management \[6].
-->

🟦 **Ec – Elasticity (弾力性)**

需要の変化やコスト目標に応じて、リソースの割り当てを自動で調整する。予測にもとづくオートスケーリングや負荷の整形などが含まれる。

**例：** Chaseらの研究では、負荷と効用に応じてサーバーを動的に割り当てることで、弾力的なリソース管理を実現している[6]。

<!--
🟦 **Wa – Workload-Aware Optimisation**

Continuously observe workload shape (skew, locality, access frequency, etc.), and adapt data layouts, algorithm choices, or resource allocations to match current patterns.

**Example:** Database "cracking" incrementally reorganises column data based on query predicates, adapting the data layout continuously to the observed workload \[20].
-->

🟦 **Wa – Workload-Aware Optimisation (ワークロードを意識した最適化)**

偏り、局所性、アクセス頻度など、ワークロードの特徴を継続的に観察し、それに応じてデータ配置、アルゴリズムの選択、リソースの割り当てなどを動的に最適化する。

**例：** データベースの「クラッキング」は、クエリの述語にもとづいてカラムデータを少しずつ再編成し、実際のワークロードに合わせてデータ配置を継続的に適応させる[20]。

<!--
🟦 **Au – Automation and Autonomy**

Let the system perform routine or reactive tasks without human intervention, often by learning from traces or user-provided examples.

**Example:** AutoAdmin automatically recommends indexes/materialized views from workload traces \[7]. Programming-by-example systems automate tasks by generalizing from a few user-provided examples \[33].
-->

🟦 **Au – Automation and Autonomy (自動化と自律性)**

システムが定型的な作業や反応的な処理を人手を介さずに実行できるようにする。多くの場合、トレースやユーザーが与えた例から学習して実現される。

**例：** AutoAdminは、ワークロードのトレースからインデックスやマテリアライズドビューの作成を自動で提案する[7]。プログラミング・バイ・エグザンプルのシステムは、少数のユーザー例から一般化し、作業を自動化する[33]。

<!--
🟦 **Ho – Human Observability**

Expose internal state of the system, like metrics, traces, plans, to make the system intentionally transparent; that transparency improves observability, debugging, introspection, and control.

**Example:** Paxson’s end-to-end Internet packet dynamics analysis demonstrates how rich measurement and tracing enable informed debugging and tuning \[37].
-->

🟦 **Ho – Human Observability (人による可観測性)**

メトリクス、トレース、計画など、システム内部の状態を意図的に可視化することで、観察、デバッグ、内省、制御をしやすくする。

**例：** Paxsonのエンドツーエンドによるインターネットパケットの動的解析は、豊富な測定とトレースによって、効果的なデバッグやチューニングが可能になることを示している[37]。

<!--
🟦 **Ev – Evolvability**

Design so the system can change with minimal downtime or rewrites and do so without breaking external contracts or observable behaviour for existing clients. Unlike extensibility that lets outsiders add new behavior via defined hook points without touching the core, evolvability lets the system’s internals change over time without breaking existing external contracts.

**Example:** Parnas presents how a modular design makes system easier to extend without disruptive rewrites \[36].
-->

🟦 **Ev – Evolvability (進化可能性)**

ダウンタイムや大規模な書き換えを最小限に抑えながらシステムを変更できるように設計し、既存のクライアントに対する外部契約や観察可能な動作を壊さないようにする。拡張性が外部から新しい機能を追加できる仕組みであるのに対し、進化可能性はシステム内部を時間とともに変更できることを指す。

**例：** Parnasは、モジュール設計によりシステムを破壊的な書き換えなしで拡張しやすくできることを示している[36]。

<!--
## 🟥 Group 7: Reliability
-->

## 🟥 グループ 7：Reliability (信頼性) {#-group-7-reliability-ja}

<!--
🟥 **Ft – Fault Tolerance**

Design the system to continue operating, perhaps in degraded form, despite component failures.

**Example:** Gray’s analysis of why computers stop shows that replication and automatic restart let services keep running through hardware and software faults \[15].
-->

🟥 **Ft – Fault Tolerance (障害耐性)**

システムの一部に障害が起きても、たとえ性能が低下しても動作を継続できるように設計する。

**例：** Grayによる「なぜコンピュータは止まるのか」の分析では、レプリケーションと自動再起動によって、ハードウェアやソフトウェアの障害があってもサービスの継続が可能になることが示されている[15]。

<!--
🟥 **Is – Isolation for Correctness**

Prevent unintended interference among components so local reasoning remains valid.

**Example:** Two-phase row-level locking stops one transaction from reading or overwriting another’s uncommitted data, preserving isolation guarantees \[16].
-->

🟥 **Is – Isolation for Correctness (正しさのための分離)**

コンポーネント間の意図しない干渉を防ぎ、局所的な考察が正しく通用するようにする。

**例：** 2相ロックによる行レベルの制御は、あるトランザクションが別のトランザクションの未確定データを読み取ったり上書きしたりするのを防ぎ、分離性の保証を守っている[16]。

<!--
🟥 **At – Atomic Execution**

Group multiple operations so they appear indivisible, either all take effect or none do.

**Example:** With Transactional Memory, memory operations inside a transaction speculatively execute, then commit atomically; if any conflict or fault occurs, the entire block aborts and leaves no partial state \[18].
-->

🟥 **At – Atomic Execution (原子的な実行)**

複数の操作をまとめて、すべてが一度に実行されたか、まったく実行されなかったかのように見えるようにする。

**例：** トランザクショナルメモリでは、トランザクション内のメモリ操作が投機的に実行され、最後にまとめて原子的にコミットされる。衝突や障害があった場合は、そのブロック全体が中断され、部分的な状態は残らない[18]。

<!--
🟥 **Cr – Consistency Relaxation**

Deliberately relax strong consistency or ordering constraints, but only within documented bounds, to improve performance, availability, or concurrency.

**Example:** Bayou lets mobile clients update replicas while disconnected, guaranteeing eventual convergence when replicas reconnect, trading strict consistency for offline availability \[38].
-->

🟥 **Cr – Consistency Relaxation (一貫性の緩和)**

性能、可用性、並行性を高めるために、強い一貫性や順序の制約を意図的に緩める。ただし、その緩和は文書化された範囲内にとどめることが重要である。

**例：** Bayouは、モバイルクライアントがオフライン中でもレプリカを更新できるようにし、再接続時に最終的な収束を保証することで、厳密な一貫性の代わりにオフラインでの可用性を実現している[38]。

<!--
## 🟫 Group 8: Security
-->

## 🟫 グループ 8： Security (セキュリティ) {#-group-8-security-ja}

<!--
🟫 **Sy – Security via Isolation**

Enforce strong boundaries so faults or hostile code cannot affect other components.

**Example:** A correct virtual machine monitor presents each guest with a complete, isolated machine and intercepts privileged operations, preventing one guest from compromising others or the host \[40].
-->

🟫 **Sy – Security via Isolation (分離によるセキュリティ)**

障害や悪意のあるコードが他のコンポーネントに影響を与えないように、強力な境界を設けて保護する。

**例：** 正しく実装された仮想マシンモニタは、それぞれのゲストに完全に分離された仮想マシンを提供し、特権操作を監視・制御することで、あるゲストが他のゲストやホストを侵害するのを防いでいる[40]。

<!--
🟫 **Ac – Access Control and Auditing**

Define permissions and log every access for accountability.

**Example:** Lampson’s taxonomy of access-control lists, capabilities, and audit trails underpins modern security mechanisms \[28].
-->

🟫 **Ac – Access Control and Auditing (アクセス制御と監査)**

権限を明確に定義し、すべてのアクセスを記録して追跡可能にする。

**例：** Lampsonのアクセス制御リスト、ケイパビリティ、監査ログに関する分類は、現代のセキュリティ機構の基礎となっている[28]。

<!--
🟫 **Lp – Least Privilege**

Grant only minimal authority needed for a task, shrinking the blast radius.

**Example:** The post-mortem on the 1988 Internet Worm shows how excess privilege let the worm spread and spurred widespread adoption of least-privilege daemons \[35].
-->

🟫 **Lp – Least Privilege (最小権限の原則)**

作業に必要な最小限の権限だけを与え、被害の範囲を最小化する。

**例：** 1988年のインターネットワームの事後分析では、過剰な権限がワームの拡散を許したことが示され、それ以降、最小権限で動作するデーモンの採用が広まった[35]。

<!--
🟫 **Tq – Trust via Quorum**

Rely on agreement from multiple, independent participants rather than a single authority.

**Example:** Paxos algorithm replicates state across a majority quorum so the service stays correct even if minority nodes crash or act maliciously \[26].
-->

🟫 **Tq – Trust via Quorum (クォーラムによる信頼)**

単一の権限に頼るのではなく、複数の独立した参加者の合意に基づいて信頼性を確保する。

**例：** Paxosアルゴリズムは、過半数のクォーラムにまたがって状態を複製することで、少数のノードがクラッシュしたり悪意ある動作をしてもサービスの正しさを保てるようにしている[26]。

🟫 **Cf – Conservative Defaults**

Ship with restrictive, safe settings; let experts opt-in to riskier, faster modes.

**Example:** With a "default no-access" policy, every protection mechanism should allow access only when explicitly granted \[43].

🟫 **Cf – Conservative Defaults (保守的なデフォルト設定)**

初期設定は制限的かつ安全なものにし、リスクや高速化を望む専門家だけが明示的に切り替えられるようにする。

**例：** 「デフォルトはアクセス不可」という方針では、すべての保護機構が、明示的に許可された場合にのみアクセスを許すようになっている[43]。

<!--
🟫 **Sa – Safety by Construction**

Structure code or data so entire classes of errors become impossible rather than merely detected.

**Example:** Rust’s ownership and borrow checker prevent data races and dangling pointers at compile time \[34].
-->

🟫 **Sa – Safety by Construction (構造による安全性の確保)**

コードやデータの構造自体を工夫して、エラーを単に検出するのではなく、そもそも発生しないようにする。

**例：** Rustの所有権と借用チェッカーは、コンパイル時にデータ競合やダングリングポインタの発生を防いでいる[34]。

<!--
## 4. CASE STUDY
-->

## 4. ケーススタディ

<!--
To illustrate how multiple design principles intersect in practice, consider the mapping from logical to physical operator plans in a relational database system.
-->

複数の設計原則が実際にどのように交差するかを示す例として、リレーショナルデータベースシステムにおける論理演算子計画から物理演算子計画への変換を考えてみる。

<!--
- The database system translates declarative intent into executable steps (**Policy/Mechanism Separation**).
- SQL expresses the "what" (**Abstraction Lifting**) with precise semantics (**Semantically Explicit Interfaces**).
- The optimizer first rewrites the query using algebraic equivalences (**Equivalence-Based Planning**).
- It then chooses concrete physical operators using a cost model (**Cost-based Planning**).
- Physical operators are often optimized for underlying hardware features (**Hardware-Aware Design**).
- Predicate-pushdown illustrates **Work Avoidance**, while indexes enable **Reuse of Computation**.
- **Advisory Hints** can guide the optimizer, and newer database systems add runtime re-optimization (**Adaptive Processing**), learned models (**Learned Approximation**), and sampling (**Probabilistic Design**).
-->

- データベースシステムは、宣言的な意図を実行可能な手順に変換する（Pm - ポリシーとメカニズムの分離）。
- SQLは「何をするか」を表現し（Al - 抽象化の引き上げ）、その意味を正確に定義している（意味が明示されたインターフェース）。
- オプティマイザはまず、代数的な等価変換を用いてクエリを書き換える（Ep - 同値変換による計画）。
- 次に、コストモデルを使って具体的な物理演算子を選択する（Pm - コストベースの計画）。
- 物理演算子は、基盤となるハードウェアの特性に合わせて最適化されていることが多い（Ha - ハードウェアを意識した設計）。
- 断定されたプッシュダウンは**作業の回避(Wv)** の例であり、インデックスは**計算の再利用(Rc)** を可能にする。
- **助言的ヒント(Ah)** はオプティマイザの判断を助けることができ、新しいデータベースシステムでは、実行時の再最適化（Ad - 適応的な処理）、学習モデルの活用（La - 学習による近似）、サンプリングの導入（確率的設計）なども加えられている。

<!--
Thus, logical-to-physical operator mapping in database systems exemplifies how several design principles come together to efficiently process declarative SQL queries.
-->

このように、データベースシステムにおける論理演算子から物理演算子への変換は、宣言的なSQLクエリを効率よく処理するために、複数の設計原則が組み合わさって機能していることを示す好例である。

<!--
## 5. LIMITATIONS
-->

## 5. 制限事項

<!--
Any attempt to organise a field as broad as computer systems involves trade-offs. This table is not a checklist or a universal theory; it is a shared vocabulary that highlights recurring principles and encourages structural reflection. That said, there are several limitations:
-->

コンピュータシステムのように広範な分野を整理しようとすれば、必ず何らかのトレードオフが生じる。この表は、チェックリストでも普遍的な理論でもなく、繰り返し現れる原則を強調し、構造的な振り返りを促すための共通語彙である。とはいえ、いくつかの制限がある:

<!--
- **Orthogonality**: Principles can overlap, reinforce, or partially conflict; design is about balancing such tensions.
- **Subjectivity and granularity**: Deriving and mapping principles involves judgement; boundaries are fuzzy and different readers may tag the same system differently or interpret the same principle differently.
- **Not a formal taxonomy**: This is not a complete or minimal set of design principles. There is no attempt to derive the principles from a minimal core.
-->

- **直交性**：原則同士が重なったり、互いに強め合ったり、時には部分的に矛盾することもある。設計とは、そうした緊張関係のバランスを取る作業である。
- **主観性と粒度**：原則の抽出や分類には判断が伴い、境界はあいまいである。同じシステムに対して異なる読者が異なるタグを付けたり、同じ原則を異なる意味で解釈することがありうる。
- **正式な分類体系ではない**：これは設計原則の完全な集合でも、最小限の集合でもない。最小の中核から原則を導こうという意図はない。

<!--
Ultimately, this table is a means to help students see recurring design principles more clearly, to assist system designers in communicating tradeoffs more precisely, and to help researchers recognize where their ideas fit into the broader landscape of system design.
-->

最終的に、この表は、学生が繰り返し現れる設計原則をより明確に理解できるようにし、システム設計者がトレードオフをより正確に伝えられるようにし、研究者が自分のアイデアがシステム設計全体の中でどこに位置づけられるかを認識する助けとなることを目的としている。

<!--
## 6. CONCLUSION
-->

## 6. 結論

<!--
System design spans diverse domains and vocabularies, which can make shared discussion harder. We inherit mechanisms, study tradeoffs, and build intuitions, yet concise terms for the underlying ideas are not always available. The “periodic table” of design principles offered here aims to provide a modest common language, naming recurring ideas so they are easier to teach, compare, and build upon.
-->

システム設計は多様な分野と用語にまたがっており、それが共通の議論を難しくすることがある。私たちは過去の仕組みを受け継ぎ、トレードオフを学び、直感を鍛えるが、根底にある考え方を表す簡潔な言葉は常に用意されているわけではない。本稿で提示した設計原則の「周期表」は、繰り返し現れるアイデアに名前を与えることで、それらを教えやすくし、比較しやすくし、次の設計へとつなげやすくするための、ささやかな共通言語を提供することを目指している。

<!--
## REFERENCES
-->

## 参考文献

[1] Ron Avnur and Joseph M. Hellerstein. *Eddies: Continuously Adaptive Query Processing*. In SIGMOD, 2000.

[2] Rudolf Bayer and Edward McCreight. *Organization and Maintenance of Large Ordered Indexes*. Acta Informatica, 1972.

[3] Hal Berenson, Philip A. Bernstein, Jim Gray, Jim Melton, Elizabeth J. O’Neil, and Patrick E. O’Neil. *A Critique of ANSI SQL Isolation Levels*. In SIGMOD, 1995.

[4] Andrew D. Birrell and Bruce J. Nelson. *Implementing Remote Procedure Calls*. ACM TOCS, 1984.

[5] Craig Chambers and David Ungar. *Customization: Optimizing Compiler Technology for SELF*. In PLDI, 1989.

[6] Jeffrey S. Chase et al. *Managing Energy and Server Resources in Hosting Centers*. In SOSP, 2001.

[7] Surajit Chaudhuri and Vivek R. Narasayya. *An Efficient, Cost-Driven Index Selection Tool for Microsoft SQL Server*. In VLDB, 1997.

[8] Ron Cytron et al. *Efficiently Computing Static Single Assignment Form and the Control Dependence Graph*. ACM TOPLAS, 1991.

[9] Jeff Dean and Luiz André Barroso. *The Tail at Scale*. Communications of the ACM, 2013.

[10] Jeffrey Dean and Sanjay Ghemawat. *MapReduce: Simplified Data Processing on Large Clusters*. In OSDI, 2004.

[11] Peter J. Denning. *The Working Set Model for Program Behavior*. Communications of the ACM, 1968.

[12] Giuseppe DeCandia et al. *Dynamo: Amazon’s Highly Available Key-Value Store*. In SOSP, 2007.

[13] Sally Floyd and Van Jacobson. *Random Early Detection Gateways for Congestion Avoidance*. In SIGCOMM, 1993.

[14] Goetz Graefe. *The Cascades Framework for Query Optimisation*. HPL Technical Report HPL-95-18, 1995.

[15] Jim Gray. *Why Do Computers Stop and What Can Be Done About It?* Tandem Technical Report, 1986.

[16] Jim Gray and Andreas Reuter. *Transaction Processing: Concepts and Techniques*. Morgan Kaufmann, 1993.

[17] J. N. Gray et al. *Granularity of Locks in a Shared Data Base*. In VLDB, 1975.

[18] Maurice Herlihy and J. Eliot B. Moss. *Transactional Memory: Architectural Support for Lock-Free Data Structures*. In ISCA, 1993.

[19] John Hughes. *Why Functional Programming Matters*. In *Research Topics in Functional Programming*, Addison-Wesley, 1990.

[20] Stratos Idreos et al. *Database Cracking*. In CIDR, 2007.

[21] Michael Isard et al. *Quincy: Fair Scheduling for Distributed Computing Clusters*. In SOSP, 2009.

[22] Daniel A. Jiménez and Calvin Lin. *Dynamic Branch Prediction with Perceptrons*. In HPCA, 2001.

[23] Donald E. Knuth. *Structured Programming with go to Statements*. ACM Computing Surveys, 1974.

[24] H. T. Kung and John T. Robinson. *On Optimistic Methods for Concurrency Control*. ACM TODS, 1981.

[25] Leslie Lamport. *The Part-Time Parliament*. ACM TOCS, 1998.

[26] Leslie Lamport. *The Part-Time Parliament*. ACM TOCS, 1998.

[27] Leslie Lamport. *Specifying Systems: The TLA+ Language and Tools for Hardware and Software Engineers*. Addison-Wesley, 2002.

[28] Butler W. Lampson. *Protection*. ACM Operating Systems Review, 1974.

[29] Butler W. Lampson. *Hints for Computer System Design*. ACM Operating Systems Review, 1983.

[30] Chris Lattner and Vikram Adve. *LLVM: A Compilation Framework for Lifelong Program Analysis & Transformation*. In CGO, 2004.

[31] C. L. Lawson et al. *Basic Linear Algebra Subprograms for Fortran Usage*. ACM TOMS, 1979.

[32] R. Levin et al. *Policy/Mechanism Separation in Hydra*. In SOSP, 1975.

[33] Henry Lieberman. *Your Wish is My Command: Programming by Example*. Morgan Kaufmann, 2001.

[34] Nicholas D. Matsakis and Felix Klock. *The Rust Language*. In ACM SIGAda, 2014.

[35] Robert T. Morris. *A Tour of the Worm*. USENIX, 1989.

[36] David L. Parnas. *Designing Software for Ease of Extension and Contraction*. IEEE TSE, 1979.

[37] Vern Paxson. *End-to-End Internet Packet Dynamics*. IEEE/ACM TON, 1999.

[38] K. Petersen et al. *Flexible Update Propagation for Weakly Consistent Replication*. In SOSP, 1997.

[39] Hamid Pirahesh et al. *Extensible/Rule-Based Query Rewrite Optimization in Starburst*. In SIGMOD, 1992.

[40] Gerald J. Popek and Robert P. Goldberg. *Formal Requirements for Virtualizable Third Generation Architectures*. Communications of the ACM, 1974.

[41] Dennis M. Ritchie and Ken Thompson. *The UNIX Time-Sharing System*. Communications of the ACM, 1974.

[42] J. H. Saltzer et al. *End-to-End Arguments in System Design*. ACM TOCS, 1984.

[43] Jerome H. Saltzer and Michael D. Schroeder. *The Protection of Information in Computer Systems*. Proc. IEEE, 1975.

[44] Patricia G. Selinger et al. *Access Path Selection in a Relational Database Management System*. In SIGMOD, 1979.

[45] Alexander A. Stepanov and Meng Lee. *The Standard Template Library*. HP Laboratories Technical Report, 1994. 

[46] Michael Stonebraker and Lawrence A. Rowe. *The Design of POSTGRES*. In SIGMOD, 1986.

[47] R. Clint Whaley and Jack J. Dongarra. *Automatically Tuned Linear Algebra Software*. In SC, 1998.

[48] Hubert Zimmermann. *OSI Reference Model – The ISO Model of Architecture for Open Systems Interconnection*. IEEE Transactions on Communications, 1980.

[49] Marc Shapiro, Nuno Preguiça, Carlos Baquero, and Marek Zawirski. 2011. Conflict-free Replicated Data Types. Technical Report. INRIA.

<!--
## HOW TO CITE
-->

## 引用方法

<!--
If you find this analysis useful, please cite it as:
-->

この分析が参考になった場合は、次のように引用してください:

```
@misc{arulraj2025periodictablecomputerdesign,
      title={Towards a Periodic Table of Computer System Design Principles}, 
      author={Joy Arulraj},
      year={2025},
      eprint={2507.22098},
      archivePrefix={arXiv},
      primaryClass={cs.OH},
      url={https://arxiv.org/abs/2507.22098}, 
}
```
<!--
or
-->

または

> Joy Arulraj. *Towards a Periodic Table of Computer System Design Principles* arXiv preprint arXiv:2507.22098, 2025.

<!--
## HOW TO CONTRIBUTE
-->

## 貢献方法

<!--
We welcome PRs that add, refine, or re-group principles, and PRs that add "classic" papers as examples. Open an issue describing:
-->

原則の追加、改善、再分類、または「古典的な」論文を例として追加するPRを歓迎します。次の内容を含むIssueを立ててください。

  - **What** you are adding or changing.
  - **Why** (include a rationale for the orthogonality and generality of the principle).
  - **Citations** (classic papers preferred).
---
- **何**を追加または変更しようとしているのか。
- **なぜ**その変更や追加が必要なのか（直交性や一般性の観点からの理由を含めてください）。
- **参考文献**（できれば古典的な論文を挙げてください）。