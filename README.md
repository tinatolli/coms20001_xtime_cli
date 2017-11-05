# coms20001_xtime_cli

Instructions for how to setup xTIMEcomposer's toolchain without using the built-in IDE

Tested on:

 * Fedora 26 x64

Should work just fine on any *nix systems.

1. Download xTIMEcomposer, extract to a suitable place, you should have something like this

    ```bash
    tom@kurobako  ~/xTIMEcomposer/Community_14.3.2  ls -lah
    total 96K
    drwxr-xr-x.  18 tom tom 4.0K Sep 30 17:23 .
    drwxr-xr-x.   3 tom tom 4.0K Sep 30 17:23 ..
    drwxrwxrwx.   6 tom tom 4.0K Sep 30 17:23 arm_toolchain
    drwxr-xr-x.   2 tom tom 4.0K Sep 30 17:23 bin
    drwxr-xr-x.   3 tom tom 4.0K Sep 30 17:23 build
    drwxr-xr-x.   3 tom tom 4.0K Sep 30 17:23 configs
    drwxr-xr-x.   3 tom tom 4.0K Sep 30 17:23 doc
    drwxr-xr-x.   4 tom tom 4.0K Sep 30 17:23 examples
    drwxr-xr-x.   2 tom tom 4.0K Sep 30 17:23 icons
    drwxr-xr-x.   2 tom tom 4.0K Sep 30 17:23 include
    drwxr-xr-x.   4 tom tom 4.0K Sep 30 17:23 lib
    drwxr-xr-x.   2 tom tom 4.0K Sep 30 17:23 libexec
    drwxr-xr-x.   2 tom tom 4.0K Sep 30 17:23 license
    drwxr-xr-x.   2 tom tom 4.0K Sep 30 17:23 scripts
    -rwxr-xr-x.   1 tom tom 2.2K Sep 30 17:23 SetEnv
    drwxr-xr-x.   4 tom tom 4.0K Sep 30 17:23 src
    drwxr-xr-x.   4 tom tom 4.0K Sep 30 17:23 target
    drwxr-xr-x. 301 tom tom  20K Sep 30 17:23 targets
    -rwxr-xr-x.   1 tom tom  147 Sep 30 17:23 xtimecomposer
    drwxr-xr-x.   8 tom tom 4.0K Nov  5 17:45 xtimecomposer_bin
    ```

2. **In the same directory where `SetEnv` is located**, execute it: 

    ```bash
    tom@kurobako  ~/xTIMEcomposer/Community_14.3.2  source ./SetEnv 
    ```
4. Verify that your path has xTIME related variables:

    Before:
    ```bash
    tom@kurobako  ~/xTIMEcomposer/Community_14.3.2  echo $PATH 
    /usr/local/bin:
    /usr/local/sbin:
    /usr/bin:
    /usr/sbin:
    /home/tom/bin:
    /home/tom/.local/bin
    ```
    After:

    ```bash
     tom@kurobako  ~/xTIMEcomposer/Community_14.3.2  echo $PATH
    /home/tom/xTIMEcomposer/Community_14.3.2/bin:
    /home/tom/xTIMEcomposer/Community_14.3.2/xtimecomposer_bin:
    /home/tom/xTIMEcomposer/Community_14.3.2/arm_toolchain/bin:
    /usr/local/bin:
    /usr/local/sbin:
    /usr/bin:
    /usr/sbin:
    /home/tom/bin:
    /home/tom/.local/bin
    ```


3. You can now build your project:

    ```bash
    tom@kurobako  ~/xTIMEcomposer/Community_14.3.2  cd ~/coms20001_cw1/game_of_life
    tom@kurobako  ~/coms20001_cw1/game_of_life   master ●  xmake
    Checking build modules
    Using build modules: lib_i2c(3.0.0) lib_logging(2.0.0) lib_xassert(2.0.0)
    Creating game_of_life.xe
    Build Complete
    ```
4. Now, to make life easier, you can create a script like so:

    ```bash
    #!/bin/sh
    pushd ~/xTIMEcomposer/Community_14.3.2/; source ./SetEnv;  popd
    ```
    Place the script in your project root and run `source ./the_script.sh` before using `xmake`; add this to your `.bashrc` or `.zshrc` if you feel like XC is the next big thing /s.

# Running your project

After xmake successfully builds your project, you want to run it with `xsim`(`xsim`, along with `xmake` should both be in your PATH if you have your environment setup correctly). The compiled binary will be located in `<project_root>/bin/<project_name>.xe`. 

To run the binary:

```bash
tom@kurobako  ~/coms20001_cw1/game_of_life   master xsim bin/game_of_life.xe 
ProcessImage: Start, size = 16x16
DataInStream: Start...
DataOutStream: Start...
Waiting for Board Tilt...
# .....
```

You may want to add some flags:

    --warn-resources --warn-links --warn-stack --warn-registers --stats

The `--stats` flags is especially useful at gaging link(channel) usages.


# Tips

You may want to add the `-report` flag to your `Makefile` so that `xcc` can tell you more. In your `Makefile`, find the definition of `XCC_FLAGS` and append the flag, for example: `XCC_FLAGS = -report`

The output(near the end) should now look something like this:

    Creating game_of_life.xe
    Constraint check for tile[0]:
      Cores available:            8,   used:          4 .  OKAY
      Timers available:          10,   used:          4 .  OKAY
      Chanends available:        32,   used:          6 .  OKAY
      Memory available:       262144,   used:      56436 .  OKAY
        (Stack: 5996, Code: 47452, Data: 2988)
    Constraints checks PASSED.
    Build Complete

This information is very useful for debugging.