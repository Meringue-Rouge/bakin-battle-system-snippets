# 選手指揮の決定：追加カメラ3台 / Player Command Decision: 3 Additional Cameras

バトルシステムは、コマンド決定フェイズに3つの追加カメラ（バトルカメラではない）を使用する。
正しく動作させるには、3つの異なるカメラを作成する必要があります。

Makes the battle system use three additional normal cameras (not battle cameras) during the command decision phase: one for the player decision menus, one for targeting enemy targets, and one for targeting allies.
Requires the creation of 3 different cameras for it to work properly.

> [!IMPORTANT]
> カメラはバトルカメラではなく、ノーマルカメラでなければならない。
> ノーマルカメラをバトルカメラに変換するには、バトルカメラを右クリックしてコピーし、マップフォルダに貼り付けます。
> 
> The cameras must be Normal Cameras, not Battle Cameras.
> To convert a Normal Camera into a Battle Camera, right-click the Battle Camera, copy it, and then in any map folder, paste it.

> [!IMPORTANT]
> コードスニペットを動作させるには、カメラGUIDが必要です。
> 共通イベントを作成し、各カメラで3つの異なるカメラ再生イベントを追加し、イベントをTXTファイルとしてエクスポートすることで取得できます。
> TXTファイルを開き、作成した3つのイベントを見つけ、CAM_ANIMATIONイベントのguidフィールドにある数字と文字の長い文字列をコピーします。
>
> You'll need the Camera GUIDs for the code snippet to work.
> You can obtain them by creating a common event, adding 3 different Camera Playback events with each camera, and then exporting the event as a TXT file.
> Open the TXT file and find the 3 events you've created, and copy the long string of numbers and letters in the guid field of the CAM_ANIMATION events.

> [!CAUTION]
> 逃げるなど、いくつかのプリセットアクションのアニメーションが壊れるかもしれません。
> このコードを使用する際は注意してください！ バックアップを取り、すべてをテストしてください。
> 
> The animation of some preset actions, such as running away, might break.
> There might be severe game breaking bugs that I did not find yet, so use this code with caution! Make a backup and test everything.

## 使用方法

* これはBakinのデフォルトのバトルシステム用に設計されています。
* もし ``battlescript`` フォルダにバトルシステムがインストールされていない場合は、 Functions -> Expanded Features で Copy Battle System (Default) を選択してください。
* ゲームプロジェクトファイル内の``battlescript``フォルダを開き、コードエディタで``BattleViewer3D.cs``を開きます。
* CTRL+F（検索）を押し、``internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)``を検索します。
* このコードで関数全体をオーバーライドします。
* 希望する各CameraAnimation割り当てのGUID値を、独自のカメラGUIDに置き換えます。 コメントは使用するカメラのタイプを示します：
  * PLAYER DEC: プレイヤー決定時のアイドリング時に使用するカメラです。
  * ALLY TARGET： 味方のターゲット（ヒーリング、アイテムなど）を選択するときに使用するカメラ。
  * ENEMY TARGET： 敵ターゲット(攻撃、スキルなど)を選択する際に使用するカメラ。

## Instructions

* This was designed for Bakin's default battle system.
* If you don't have the battle system installed in the ``battlescript`` folder, go to Functions -> Expanded Features, and select Copy Battle System (Default)
* Open the ``battlescript`` folder inside the game project files, and open ``BattleViewer3D.cs`` in a code editor.
* Press CTRL+F (find and search) and search for ``internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)``.
* Override the entire function with this code.
* Replace the GUID values for each desiredCameraAnimation assignment with your own camera GUIDs. The comments indicate the type of camera to use:
  * PLAYER DEC: The camera used when idling on player decision.
  * ALLY TARGET: The camera used when choosing an ally target (healing, item, etc).
  * ENEMY TARGET: The camera used when choosing an enemy target (attack, skill, etc).

## コード・スニペット / Code Snippet

```
        // Determine the desired camera animation
        private string currentCameraAnimationGUID;
        internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)
        {
            base.Update(playerData, enemyMonsterData);

            if (mapDrawer.currentRom == null)
                return;

            Quaternion qt = camManager.camQuat;
            Vector3 rot = new Vector3();
            camManager.convQuaternionToRotation(qt, out rot);
            float angleY = rot.Y;
            mapDrawer.Update(GameMain.getElapsedTime());

            foreach (var entry in turnChr)
            {
                entry?.setVisibility(false);
            }

            UpdateBattleActors(friends, angleY, true);

            foreach (var mapChr in extras)
            {
                mapChr.Update(mapDrawer, angleY, false);
            }

            UpdateBattleActors(enemies, angleY, false);

            // Move camera according to battle state
            if (game.data.system.BattleCameraEnabled[Common.GameData.SystemData.BATTLE_CAMERA_SITUATION_RESULT] &&
                owner.battleState == BattleState.Result &&
                oldState != BattleState.Result)
            {
                PlayCameraAnimation(Rom.Camera.NAME_BATTLE_RESULT);
            }

            if (owner.battleState == BattleState.WaitEventsBeforeCommandSelect && 
                oldState != BattleState.WaitEventsBeforeCommandSelect)
            {
                StopCameraAnimation(); // Ensure any ongoing animation is stopped
                camManager.setWaitFunc(null); // Clear any wait function
                currentCameraAnimationGUID = null; // Clear current animation to force update
            }

            // Let each character act according to the state
            for (int i = 0; i < playerData.Count; i++)
            {
                var friend = searchFromActors(playerData[i]);

                if (friend == null)
                    continue;

                playerData[i].statusWindowDrawPosition = friend.getScreenPos(p, v, MapScene.EffectPosType.Head);

                Vector3 neutralPos = friend.mapChr.pos;

                if (i < turnChr.Length)
                    turnChr[i]?.setPosture(SharpKmyMath.Matrix4.translate(neutralPos.X, neutralPos.Y, neutralPos.Z));

                if (owner.battleState == BattleState.PlayerEscapeSuccess)
                {
                    friend.queueActorState(BattleActor.ActorStateType.ESCAPE);
                    continue;
                }

                if (owner.battleState == BattleState.Result)
                {
                    friend.queueActorState(BattleActor.ActorStateType.WIN);
                    continue;
                }

                updateConditionEffectAndMotion(friend);

                if (owner.battleState >= BattleState.WaitEventsBeforeCommandSelect &&
                    owner.battleState <= BattleState.SortBattleActions)
                {
                    if (playerData[i] == owner.commandSelectPlayer)
                    {
                        turnChr[i]?.setVisibility(true);
                        skillUser = friend;
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
            }

            // Determine the desired camera animation
            string desiredCameraAnimation = null;

            if (displayWindow == WindowType.CommandTargetMonsterListWindow)
            {
                var selectedMonster = enemyMonsterData.FirstOrDefault(m => m.IsSelect);
                if (selectedMonster != null)
                {
                    skillUser = searchFromActors(selectedMonster);
                    desiredCameraAnimation = "01ab9724-6b93-4440-b55a-2f91def556b8"; // ENEMY TARGET
                }
                else
                {
                    skillUser = friends[0];
                    desiredCameraAnimation = "5f73a3bc-830a-404b-afa1-87a2f4eaf2f0"; // PLAYER DEC
                }
            }
            else if (displayWindow == WindowType.CommandTargetPlayerListWindow)
            {
                var selectedPlayer = playerData.FirstOrDefault(p => p.IsSelect);
                if (selectedPlayer != null)
                {
                    skillUser = searchFromActors(selectedPlayer);
                    desiredCameraAnimation = "6ff4c3a2-a5f1-4d46-86d9-5078d0d2cff6"; // ALLY TARGET
                }
                else
                {
                    skillUser = friends[0];
                    desiredCameraAnimation = "5f73a3bc-830a-404b-afa1-87a2f4eaf2f0"; // PLAYER DEC
                }
            }
            else if (owner.battleState >= BattleState.WaitEventsBeforeCommandSelect &&
                    owner.battleState <= BattleState.SortBattleActions)
            {
                desiredCameraAnimation = "5f73a3bc-830a-404b-afa1-87a2f4eaf2f0"; // PLAYER DEC
            }

            // Play the custom camera animation only if it has changed
            if (desiredCameraAnimation != null && desiredCameraAnimation != currentCameraAnimationGUID)
            {
                // Explicitly stop the current animation before playing a new one
                PlayCustomCameraAnimationGUID(desiredCameraAnimation);
                currentCameraAnimationGUID = desiredCameraAnimation;
            }

            // Let monsters act according to the state
            for (int i = 0; i < enemyMonsterData.Count; i++)
            {
                var actor = searchFromActors(enemyMonsterData[i]);
                if (actor == null)
                    continue;

                updateConditionEffectAndMotion(actor);
            }

            oldState = owner.battleState;
        }
```
