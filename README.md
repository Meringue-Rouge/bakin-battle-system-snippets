# bakin-battle-system-snippets
"RPG Developer Bakin" バトルシステムのコードスニペットで、デフォルトのYukarバトルシステムに追加のカスタマイズ機能を追加します。

"RPG Developer Bakin" Battle System code snippets that adds additional customization features to the default Yukar battle system.

> [!TIP]
> **NEW！ 手動でコードを編集することなく、簡単に有効にしたオプションに基づいて特定のファイルを自動的に生成します！**
> 
> **NEW! Automatically generate specific files based on the options you've enabled easily, without having to edit the code manually!**
> 
> https://meringue-rouge.github.io/Bakin-Battle-Tweaker/
> 
> ![image](https://github.com/user-attachments/assets/4f855b54-5237-4da2-ae12-0d9f6e7a0f9c)





## 選手の決断：3台のカメラ / Player Decision: 3 Cameras
プレイヤー決定フェイズ中に、状況に応じて3つの異なるカメラを切り替える：味方をターゲット、敵をターゲット、アイドル。 現在バグがあり、攻撃コマンドでソフトロックが発生する。

Swaps between three different cameras during the Player Decision phase according to what's going on: targeting allies, targeting foes and idle. Currently buggy and causes soft-locks on the attack command.

[コード・スニペットを見る / View Code Snippet](https://github.com/Meringue-Rouge/bakin-battle-system-snippets/blob/main/Player%20Decision%3A%20Additional%20Cameras.md)

## 選手コマンド選択カメラ： カメラの "プレイヤー "座標を、コマンドを決定している現在のパーティメンバーに設定します。 / Player Command Selection Camera: Set the Camera's "Player" coordinate to the current party member having their commands decided on.
プレイヤー・コマンド選択時の "プレイヤー "カメラのターゲット・ロックオンを、現在アクションを決めているパーティ・メンバーをターゲットにする。

Make the "Player" camera target lock-on during the Player Command Selection target the party member that's currently deciding their action. 

[コード・スニペットを見る / View Code Snippet](https://github.com/Meringue-Rouge/bakin-battle-system-snippets/blob/main/Player%20Decision%20Player%20Target%20Lock.md)

## 優先攻撃 / Priority Attacks
常に先攻になる攻撃と常に後攻になる攻撃を追加し、2つ以上の優先順位が同じ場合はスピードでソートする。

Adds attacks that always go first and attacks that always go last, and sorts by speed if two or more have the same priority.

[コード・スニペットを見る / View Code Snippet](https://github.com/Meringue-Rouge/bakin-battle-system-snippets/blob/main/Attack%20Priority.md)

## バトル実行前にコモンイベントを呼び出す / Call Common Event Before Turn Execution
ターンが実行される前、すべてのプレーヤーが決定した直後だが、ターンの順番が決定する前に、共通のイベントを呼び出す。

Calls a common event before the turn is executed, right after every player has decided but before the turn order gets decided.

[コード・スニペットを見る / View Code Snippet](https://github.com/Meringue-Rouge/bakin-battle-system-snippets/blob/main/Common%20Event%20Before%20Turn%20Execution.md)

