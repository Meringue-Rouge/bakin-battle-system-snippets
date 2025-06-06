# 優先攻撃 / Priority Attacks

このコードスニペットは ``#highpriority`` タグの付いた攻撃をターン順で最初に行うようにします。 複数のキャラクターが ``#highpriority`` というタグを持つ攻撃を使用した場合、スピード順にソートされます。

また、``#lowpriority``のタグを持つ攻撃はターン順で最後になります。 複数のキャラクターが``#lowpriority``のタグを持つ攻撃を使用した場合、スピード順にソートされます。

2つのタグのいずれかを使用しない攻撃は、スピードのステータス順に並べられ、その間に配置される。

This code snippet makes attacks with the tag ```#highpriority``` to go first in the turn order. If multiple characters use attacks with the tag ```#highpriority```, they are sorted by speed.

It also makes attacks with the tag ```#lowpriority``` to go last in the turn order. If multiple characters use attacks with the tag ```#lowpriority```, they are sorted by speed.

Attacks that don't use any of the two tags are placed in between, sorted by their speed stat.

## 使用方法 / Instructions 

* This was designed for Bakin's default battle system.
* If you don't have the battle system installed in the ``battlescript`` folder, go to Functions -> Expanded Features, and select Copy Battle System (Default)
* Open the ``battlescript`` folder inside the game project files, and open ``BattleSequenceManager.cs`` in a code editor.
* Press CTRL+F (find and search) and search for ``UpdateBattleState_SortBattleActions``.
* Replace the entire function with the following:

```
private void UpdateBattleState_SortBattleActions()
        {

            if (battleEvents.isBusy())
                return;
            battleEvents.clearCurrentProcessingTrigger();

            commandExecuteMemberCount = 0;

            // Create the initial character list, excluding those with Nothing_Down command
            // Nothing_Downコマンドを持つ文字を除いた初期文字リストを作成する。
            var characters = createBattleCharacterList()
                .Where(character => character.selectedBattleCommandType != BattleCommandType.Nothing_Down)
                .ToList();

            // Add actions for escape and guard commands (existing priorities)
            // エスケープコマンドとガードコマンドのアクションを追加（既存の優先順位）
            AddBattleActionEntry(characters.Where(character => character.selectedBattleCommandType == BattleCommandType.PlayerEscape));
            AddBattleActionEntry(characters.Where(character => character.selectedBattleCommandType == BattleCommandType.MonsterEscape));
            AddBattleActionEntry(characters.Where(character => character.selectedBattleCommandType == BattleCommandType.Guard)
                .OrderBy(character => character.UniqueID));

            // Filter out characters already added (those with escape or guard commands)
            // すでに追加された文字（エスケープやガードコマンドを含む文字）を除外する。
            var remainingCharacters = characters
                .Where(character => !battleEntryCharacters.Exists(x => x.character == character))
                .ToList();

            // Tier 1: #highpriority (highest priority, sorted by Speed)
            // ティア1：#highpriority（最優先、スピード順）
            var pastCharacters = remainingCharacters
                .Where(character => character.selectedSkill?.tags?.Contains("#highpriority") ?? false)
                .OrderByDescending(character => character.Speed);
            AddBattleActionEntry(pastCharacters);

            // Tier 2: Normal skills (no #highpriority or #lowpriority, sorted by Speed)
            // ティア2：ノーマルスキル（#highpriority、#lowpriorityなし、スピード順）
            var normalCharacters = remainingCharacters
                .Where(character => 
                    !(character.selectedSkill?.tags?.Contains("#highpriority") ?? false) &&
                    !(character.selectedSkill?.tags?.Contains("#lowpriority") ?? false))
                .OrderByDescending(character => character.Speed);
            AddBattleActionEntry(normalCharacters);

            // Tier 3: #future (lowest priority, sorted by Speed)
            // ティア3：#future（優先順位が最も低い、スピード順）
            var futureCharacters = remainingCharacters
                .Where(character => character.selectedSkill?.tags?.Contains("#lowpriority") ?? false)
                .OrderByDescending(character => character.Speed);
            AddBattleActionEntry(futureCharacters);

            ChangeBattleState(BattleState.ReadyExecuteCommand);
        }
```
