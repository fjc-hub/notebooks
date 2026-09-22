
start a session in a tmux, then run the following command to launch a yolo mode claude session
```
Start a named session:
tmux new -s my-session

Attach to specific session:
tmux a -t my-session

Launch a yolo mode claude session:
claude --dangerously-skip-permissions remote-control
```