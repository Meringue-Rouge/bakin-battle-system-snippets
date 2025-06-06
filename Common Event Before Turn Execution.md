# バトル実行前にコモンイベントを呼び出す / Call Common Event Before Turn Execution

このコード・スニペットは、戦闘のターン順が決まる前に「Before_Turn_Execution」という名前の共通イベントを実行する。

誰が最初に行動するかに関係なく、ターン実行の一番最初に共通イベントを実行する必要がある場合に便利です。

私の場合、バトルシステムでキャラクターのアクションをキャンセルできなくすることなく、特定のアクションタイプを選択した場合にキャラクターのアクションを置き換えるために使いました。

> [!IMPORTANT]
> このコモンイベントで特定のパーティメンバーの行動選択やコマンドタイプを取得することは不可能です。
> これを回避するには、コマンド選択が確認されたときに呼び出されるバトル共通イベントを作成し、そこで情報を取得します。
> そして、「Before_Turn_Execution」コモンイベントを使って、取得した情報でアクションを変更します。

This code snippet runs a common event with the name "Before_Turn_Execution" before the turn order of the battle is decided. 

Useful if you need to run a common event at the very start of the turn execution, regardless of who's acting first.

In my case, I used it to replace a character's action if they chose a specific action type, without the battle system making it impossible to cancel the character's action.

> [!IMPORTANT]
> Retrieving a specific party member's action choice or command type in this common event will be impossible.
> To get around this, create a battle common event that gets called when any Command Selection is confirmed and retrieve the information there.
> Then use the "Before_Turn_Execution" common event to change the action with the information retrieved.

## 使用方法

* これはBakinのデフォルトのバトルシステム用に設計されています。
* もし ``battlescript`` フォルダにバトルシステムがインストールされていない場合は、 Functions -> Expanded Features で Copy Battle System (Default) を選択してください。
* ゲームプロジェクトファイル内の``battlescript``フォルダを開き、コードエディターで``BattleSequenceManager.cs``を開きます。
* CTRL+F（検索）を押し、``UpdateBattleState_SortBattleActions``を検索します。
* UpdateBattleState_SortBattleActions``のコードのすぐ下、"Code to insert" 行の下に以下のスニペットを追加します。

## Instructions

* This was designed for Bakin's default battle system.
* If you don't have the battle system installed in the ``battlescript`` folder, go to Functions -> Expanded Features, and select Copy Battle System (Default)
* Open the ``battlescript`` folder inside the game project files, and open ``BattleSequenceManager.cs`` in a code editor.
* Press CTRL+F (find and search) and search for ``UpdateBattleState_SortBattleActions``.
* Add the following snippet under the "Code to insert" line, just below the code from ``UpdateBattleState_SortBattleActions``.

## コード・スニペット / Code Snippet

```

if (battleEvents.isBusy())
    return;
battleEvents.clearCurrentProcessingTrigger();

// 挿入するコード
// Code to insert

// イベント名から共通イベントを探す（GUIDを知っている場合は、直接GUIDを設定できる）
// Find the common event by name (if you know the GUID, you can set the GUID directly)
var guid = catalog.getGameSettings().commonEvents.FirstOrDefault(x => catalog.getItemFromGuid(x)?.name == "Before_Turn_Execution");
if(guid != Guid.Empty)
{
    battleEvents.start(guid);
}

// UpdateBattleState_SortBattleActionsコードの残りの部分
// Rest of the UpdateBattleState_SortBattleActions code
```
