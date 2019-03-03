## filewalk

A simple file system walker with the goal of discovering duplicated files.

## Design

### Goals

- Safe
- Fast
- Incremental
- Simple

#### Safe

filewalk shouldn't affect any files it is walking.
It can write only to a designated output file or stdout.

#### Fast

filewalk shouldn't take an incredibly long time to go through a filesystem.
It should use whatever parallelism is available to walk files quickly.
It shouldn't spend an excessive amount of time handling each file it is walking.

#### Incremental

filewalk should be able to be resumed from where it left off.
It should only re-walk files or directories when they have changed.
It should be able to handle reasonable failures like being stopped by the user, without losing much.

#### Simple

filewalk should have minimal configuration and minimal options.
It should do the one thing it is trying to do well.
