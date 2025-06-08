# 選手コマンド選択カメラ： カメラの "プレイヤー "座標を、コマンドを決定している現在のパーティメンバーに設定します。 / Player Command Selection Camera: Set the Camera's "Player" coordinate to the current party member having their commands decided on.

この小さなコード・スニペットは、戦闘のコマンド選択部分で機能します。これは、カメラ・システムに、現在決定しているパーティ・メンバーに合わせて「プレイヤー」の位置を更新させるものです。 
手動で各ポジション用のカメラを作成することなく、1つのカメラを使って決定中のプレイヤーにフォーカスしたカメラショットを作るのに最適です。

This small code snippet works during the Command Selection part of battle: it forces the camera system to update the "Player" position to be according to the current party member being decided on. 
Great for making camera shots that focus on the player you're deiciding on using a single camera, without having to manually create seperate cameras for each position.

> [!TIP]
> 待機用バトルカメラは攻撃の合間に使用するため、プレイヤーにフォーカスするよう設定することは推奨しません。
> 待機」バトルカメラをバトルの中心にフォーカスさせ、プレイヤーにフォーカスする通常のカメラ（バトルカメラではない）を新たに作成してください。
> そして、プレイヤー判定開始時に実行されるバトル共通イベントを作成し、カメラを新しい通常カメラに入れ替えます。
>
> It is not recommended to set the "Standby" Battle Camera to focus on the Player, as it will be used in between attacks.
> Make the "Standby" battle camera focus the center of battle, and create a new normal Camera that focuses on the Player (not a Battle Camera).
> Then, create a Battle Common Event that runs at the start of Player Decision, and swap the camera to your new normal camera.

## 使用方法

* これはBakinのデフォルトのバトルシステム用に設計されています。
* もし ``battlescript`` フォルダにバトルシステムがインストールされていない場合は、 Functions -> Expanded Features で Copy Battle System (Default) を選択してください。
* ゲームプロジェクトファイル内の``battlescript``フォルダを開き、コードエディタで``BattleViewer3D.cs``を開きます。
* CTRL+F（検索）を押し、``internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)``を検索します。
* このコードを見つけて、その位置に💢の絵文字があるコメント付きのコードを1行挿入します。

## Instructions

* This was designed for Bakin's default battle system.
* If you don't have the battle system installed in the ``battlescript`` folder, go to Functions -> Expanded Features, and select Copy Battle System (Default)
* Open the ``battlescript`` folder inside the game project files, and open ``BattleViewer3D.cs`` in a code editor.
* Press CTRL+F (find and search) and search for ``internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)``.
* Find this piece of code and insert the single line of code with a comment that has the 💢 emojis at that position.

## コード・スニペット / Code Snippet

```
// 自分のターンだったらその情報を表示
                // If it's your turn, display that information
                if (owner.battleState >= BattleState.WaitEventsBeforeCommandSelect &&
                    owner.battleState <= BattleState.SortBattleActions)
                {
                    if (playerData[i] == owner.commandSelectPlayer)
                    {
                        turnChr[i]?.setVisibility(true);
                        skillUser = friend; // 💢💢💢💢💢💢💢💢💢💢💢💢💢💢💢
                        if (!friend.isActorStateQueued(BattleActor.ActorStateType.START_COMMAND_SELECT) &&
                            friend.getActorState() != BattleActor.ActorStateType.START_COMMAND_SELECT &&
                            friend.getActorState() != BattleActor.ActorStateType.COMMAND_SELECT)
                        {
                            friend.queueActorState(BattleActor.ActorStateType.START_COMMAND_SELECT);
                            friend.queueActorState(BattleActor.ActorStateType.COMMAND_SELECT);
                            continue;
                        }
                    }
                    else
                    {
                        if (!friend.isActorStateQueued(BattleActor.ActorStateType.BACK_TO_WAIT) &&
                            (friend.getActorState() == BattleActor.ActorStateType.START_COMMAND_SELECT ||
                            friend.getActorState() == BattleActor.ActorStateType.COMMAND_SELECT))
                        {
                            friend.queueActorState(BattleActor.ActorStateType.BACK_TO_WAIT);
                            friend.queueActorState(BattleActor.ActorStateType.COMMAND_STANDBY);
                            continue;
                        }
                    }
                }
```
