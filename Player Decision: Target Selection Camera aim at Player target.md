# 選手決定フェイズ： 味方/敵の攻撃/スキル/アイテムのターゲット選択時に、カメラ「プレイヤー」をターゲットに設定する。 / Player Decision Phase: Set Camera "Player" target to target during ally/foe attack/skill/item target selection.

選手決定時のターゲット選択に応じて、カメラの「選手」位置を更新する。

Updates the "Player" position of the camera according to the target selection during the player decision.

## 使用方法

* これはBakinのデフォルトのバトルシステム用に設計されています。
* もし ``battlescript`` フォルダにバトルシステムがインストールされていない場合は、 Functions -> Expanded Features で Copy Battle System (Default) を選択してください。
* ゲームプロジェクトファイル内の``battlescript``フォルダを開き、コードエディタで``BattleViewer3D.cs``を開きます。
* CTRL+F（検索）を押し、``internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)``を検索します。
* このコードで関数全体をオーバーライドします。

## Instructions

* This was designed for Bakin's default battle system.
* If you don't have the battle system installed in the ``battlescript`` folder, go to Functions -> Expanded Features, and select Copy Battle System (Default)
* Open the ``battlescript`` folder inside the game project files, and open ``BattleViewer3D.cs`` in a code editor.
* Press CTRL+F (find and search) and search for ``internal override void Update(List<BattlePlayerData> playerData, List<BattleEnemyData> enemyMonsterData)``.
* Override the entire function with this code.

## コード・スニペット / Code Snippet

```
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
                mapChr.Update(mapDrawer, /*camera.Now.angle.Y*/angleY, false);
            }

            UpdateBattleActors(enemies, angleY, false);

            // バトルステートに応じてカメラを動かす
            // Move camera according to battle state
            if (game.data.system.BattleCameraEnabled[Common.GameData.SystemData.BATTLE_CAMERA_SITUATION_RESULT] &&
                owner.battleState == BattleState.Result &&
                oldState != BattleState.Result)
            {
                PlayCameraAnimation(Rom.Camera.NAME_BATTLE_RESULT);
            }

            // 状態に応じて各キャラに演技させる
            // Let each character act according to the state
            for (int i = 0; i < playerData.Count; i++)
            {
                // ステータスを書く位置を更新する
                // Update position to write status
                var friend = searchFromActors(playerData[i]);

                if (friend == null)
                    continue;

                playerData[i].statusWindowDrawPosition = friend.getScreenPos(p, v, MapScene.EffectPosType.Head);

                Vector3 neutralPos;

                neutralPos = friend.mapChr.pos;
                //var neutralPos = BattleCharacterPosition.getPosition(CenterOfField, BattleCharacterPosition.PosType.FRIEND, i, playerData.Count);
                //if (playerData[i].isMovableToForward() &&
                //    (friend.getActorState() < BattleActor.ActorStateType.START_COMMAND_SELECT ||
                //    friend.getActorState() > BattleActor.ActorStateType.BACK_TO_WAIT))
                //    neutralPos.Z -= friends[i].frontDir;

                //neutralPos.X = playerData[i].pos.X;
                //neutralPos.Z = playerData[i].pos.Z;
                //neutralPos.Y = drawer?.getAdjustedHeight(neutralPos.X, neutralPos.Z) ?? 0;
                //friend.mapChr.setPosition(neutralPos);
                //friend.mapChr.setDirectionFromRadian(playerData[i].directionRad);
                if (i < turnChr.Length)
                    turnChr[i]?.setPosture(SharpKmyMath.Matrix4.translate(neutralPos.X, neutralPos.Y, neutralPos.Z));

                // 逃げる
                // run away
                if (owner.battleState == BattleState.PlayerEscapeSuccess)
                {
                    friend.queueActorState(BattleActor.ActorStateType.ESCAPE);
                    continue;
                }

                // 勝利
                // victory
                if (owner.battleState == BattleState.Result)
                {
                    friend.queueActorState(BattleActor.ActorStateType.WIN);
                    continue;
                }

                updateConditionEffectAndMotion(friend);

                // 自分のターンだったらその情報を表示
                // If it's your turn, display that information
                if (owner.battleState >= BattleState.WaitEventsBeforeCommandSelect &&
                    owner.battleState <= BattleState.SortBattleActions)
                {
                    if (playerData[i] == owner.commandSelectPlayer)
                    {
                        turnChr[i]?.setVisibility(true);
                        skillUser = friend; // [NEW] Set camera target to the current command-selecting player
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

            // [NEW] Update camera target during target selection phases
            if (displayWindow == WindowType.CommandTargetMonsterListWindow)
            {
                var selectedMonster = enemyMonsterData.FirstOrDefault(m => m.IsSelect);
                if (selectedMonster != null)
                {
                    skillUser = searchFromActors(selectedMonster);
                }
                else
                {
                    skillUser = friends[0]; // Default to first party member if no monster is selected
                }
            }
            else if (displayWindow == WindowType.CommandTargetPlayerListWindow)
            {
                var selectedPlayer = playerData.FirstOrDefault(p => p.IsSelect);
                if (selectedPlayer != null)
                {
                    skillUser = searchFromActors(selectedPlayer);
                }
                else
                {
                    skillUser = friends[0]; // Default to first party member if no player is selected
                }
            }

            // 状態に応じてモンスターにも演技させる
            // Let monsters act according to the state
            for (int i = 0; i < enemyMonsterData.Count; i++)
            {
                var actor = searchFromActors(enemyMonsterData[i]);
                if (actor == null)
                    continue;

                updateConditionEffectAndMotion(actor);

                //actor.mapChr.setDirectionFromRadian(enemyMonsterData[i].directionRad);
            }

            oldState = owner.battleState;
        }
```
