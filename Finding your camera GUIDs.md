## カメラのGUIDの見つけ方
- マップ上に3つの異なるカメラを作成する。 このためにバトルカメラを作成する必要はありませんが、より良いカメラ位置を得るためにバトルカメラセットを作成し、そのカメラをマップにコピー＆ペーストすると良いでしょう。
- 共通のイベントを作成する。
- 3つの異なる "Camera Playback "イベントを作成し、それぞれGUIDを取得する予定のカメラの1つを使用します。
- 右上のボタンから共通イベントをTXTファイルとしてエクスポートします。 共通イベントは安全に削除できます。
- エクスポートしたファイルをテキストエディタで開き、タグ "CAM_ANIMATION "のイベントを以下のように探します：

```
		コマンド	CAM_ANIMATION
			Guid	5f73a3bc-830a-404b-afa1-87a2f4eaf2f0
			整数	0
		コマンド終了
		コマンド	CAM_ANIMATION
			Guid	6ff4c3a2-a5f1-4d46-86d9-5078d0d2cff6
			整数	0
		コマンド終了
		コマンド	CAM_ANIMATION
			Guid	01ab9724-6b93-4440-b55a-2f91def556b8
			整数	0
		コマンド終了
```

- GUID値をコピーする。


## How to find your camera's GUID
- Create three different cameras on any map. You do not need to create Battle Cameras for this, but it's a good idea to create a Battle Camera set to obtain better camera positions, and then copy and paste the camera into a map.
- Create a common event.
- Create three different "Camera Playback" events, each one using one of your cameras you're intending to get the GUID.
- Export the common event from the upper-right button as a TXT file. You can safely delete the common event.
- Open the export in any text editor, and find the events with the tag "CAM_ANIMATION" as followed:

```
		コマンド	CAM_ANIMATION
			Guid	5f73a3bc-830a-404b-afa1-87a2f4eaf2f0
			整数	0
		コマンド終了
		コマンド	CAM_ANIMATION
			Guid	6ff4c3a2-a5f1-4d46-86d9-5078d0d2cff6
			整数	0
		コマンド終了
		コマンド	CAM_ANIMATION
			Guid	01ab9724-6b93-4440-b55a-2f91def556b8
			整数	0
		コマンド終了
```

- Copy the GUID values.
