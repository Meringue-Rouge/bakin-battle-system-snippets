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
