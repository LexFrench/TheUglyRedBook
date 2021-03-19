# General debugging tips or commands for Linux

## Get a list of linked libraries for a specific process ID

```bash
grep fbx /proc/{PID}/maps
```

## GDB Commands

### Command

The `command` keyword will allow you to run a command each time a break is hit.

```bash
(gdb) command 1
>print addr
>print len
>end
```

### Setting source code

You can set the location of the source you are debugging with the `directory` keyword.

```bash
(gdb) directory /location/of/source/code/cpp
```

### Printing a boost::shared_ptr

```bash
(gdb) set print pretty
(gdb) print {variable}.px
(gdb) print $1->{method}
```
