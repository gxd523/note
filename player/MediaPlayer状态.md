```
enum media_player_states {
		MEDIA_PLAYER_STATE_ERROR        = 0,
		MEDIA_PLAYER_IDLE                = 1 << 0, // 1
		MEDIA_PLAYER_INITIALIZED        = 1 << 1, // 2
		MEDIA_PLAYER_PREPARING            = 1 << 2, // 4
		MEDIA_PLAYER_PREPARED            = 1 << 3, // 8
		MEDIA_PLAYER_STARTED            = 1 << 4, // 16
		MEDIA_PLAYER_PAUSED                = 1 << 5, // 32
		MEDIA_PLAYER_STOPPED            = 1 << 6, // 64
		MEDIA_PLAYER_PLAYBACK_COMPLETE  = 1 << 7, // 128
};
```

