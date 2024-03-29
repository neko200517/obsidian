## 他の原則との関係性

OCPを実現するための継承の使い方を示すのがLSP

⇒　判断に迷う継承関係があった場合、クライアント側から見て基本型と置換型が置換可能かどうかが判断できるかが重要になる

## LSPのまとめ

- リスコフの置換原則（LSP）は、「派生型はその基本型と置換可能でなければならない」という原則 
- LSPを守ることでサブタイプを切り替えられるようになり、OCPを満たす継承ができるようになる
- 派生型が基本型と置換可能かどうかを判断するには契約による設計（DbC）の観点が役立つ
- 事前条件（precondition）：関数（メソッド）の開始時に保証されるべき条件
- 事後条件（postcondition）：関数（メソッド）の正常終了時に満たされるべき条件 
- 不変条件（invariant）：関数（メソッド）の開始時と正常終了時に共通して保証されるべき状態についての条件 
- 例外の使いどころは契約を満たせなくなったとき
- 派生型で基本型の①事前条件を強める、②事後条件を弱める、③不変条件に違反するとLSPに違反してしまう
- LSPはクライアントから見た「is aの関係」を保証するための原則といえる