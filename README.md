# bakin-battle-system-snippets
"RPG Developer Bakin" バトルシステムのコードスニペットで、デフォルトのYukarバトルシステムに追加のカスタマイズ機能を追加します。

"RPG Developer Bakin" Battle System code snippets that adds additional customization features to the default Yukar battle system.

## 優先攻撃 / Priority Attacks
常に先攻になる攻撃と常に後攻になる攻撃を追加し、2つ以上の優先順位が同じ場合はスピードでソートする。

Adds attacks that always go first and attacks that always go last, and sorts by speed if two or more have the same priority.

[コード・スニペットを見る / View Code Snippet](https://github.com/Meringue-Rouge/bakin-battle-system-snippets/blob/main/Attack%20Priority.md)

## バトル実行前にコモンイベントを呼び出す / Call Common Event Before Turn Execution
ターンが実行される前、すべてのプレーヤーが決定した直後だが、ターンの順番が決定する前に、共通のイベントを呼び出す。

Calls a common event before the turn is executed, right after every player has decided but before the turn order gets decided.

[コード・スニペットを見る / View Code Snippet](https://github.com/Meringue-Rouge/bakin-battle-system-snippets/blob/main/Common%20Event%20Before%20Turn%20Execution.md)
