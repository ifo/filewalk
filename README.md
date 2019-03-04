## filewalk

A simple file system walker with the goal of discovering duplicated files.

## Design

### Goals

- Safe
- Fast
- Incremental
- Simple

#### Safe

filewalk shouldn't modify any files it is walking.
It will only potentially read them, but probably only stat them.
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
It should do well only the one thing it is trying to do.

### Parts

#### Output

filewalk will output to a file or standard out.
The format will be [jsonlines](jsonlines.org).
Protobuf was considered, but I am less familiar with it, and ideally the output will be human readable.
It will be possible to move to protobuf for output later.

The output file will be unsorted, as the order of walking can't be guaranteed.
It may be valuable to sort the output, but not necessary to do so for the sake of the cache.

The output file will only be appended to.
This also means a partially filled output file may be used for the resumption of previous work.

#### Cache

In order to prevent rework, filewalk will have a cache of the files it has walked.
Because the generation of output is unordered, this will have to be a map.
The keys will be the file path, and the values will be the last modified date.

The cache may be pre-populated by a given file.

#### Walker

To be fast, instead of using filepath.Walk, filewalk will use [fastwalk](https://github.com/golang/tools/tree/master/internal/fastwalk).
This has no order guarantees, which is why the output and cache above are unordered.

### Some Random Thoughts

These were written just to get ideas down.
I'm leaving them here as an artifact of the design.
You probably don't need to read them.

In order to be incremental, filewalk should be continually generating output as it goes along.
This means that sorting the output needs to be done after it is generated, not while being made.
It also means filewalk needs to handle unsorted output as its cache for resuming.

To be fast, filewalk can't use the standard filepath.Walk, which is single threaded.
It will need to use [fastwalk](https://github.com/golang/tools/tree/master/internal/fastwalk) instead.
fastwalk doesn't guarantee order of execution, which means the output will be random and need to be sorted later.

The output being random means it will need to be made easily sortable.
