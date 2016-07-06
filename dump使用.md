##adb shell dumpsys activity -h
Activity manager dump options:

  [-a] [-c] [-p package] [-h] [cmd] ...
  
  cmd may be one of:
  
    a[ctivities]: activity stack state
    r[recents]: recent activities state
    b[roadcasts] [PACKAGE_NAME] [history [-s]]: broadcast state
    i[ntents] [PACKAGE_NAME]: pending intent state
    p[rocesses] [PACKAGE_NAME]: process state
    o[om]: out of memory management
    perm[issions]: URI permission grant state
    prov[iders] [COMP_SPEC ...]: content provider state
    provider [COMP_SPEC]: provider client-side state
    s[ervices] [COMP_SPEC ...]: service state
    as[sociations]: tracked app associations
    service [COMP_SPEC]: service client-side state
    package [PACKAGE_NAME]: all state related to given package
    all: dump all activities
    top: dump the top activity
    write: write all pending state to storage
    track-associations: enable association tracking
    untrack-associations: disable and clear association tracking

  cmd may also be a COMP_SPEC to dump activities.
  
  COMP_SPEC may be a component name (com.foo/.myApp),
  
  a partial substring in a component name, a
    hex object identifier.
  -a: include all available server state.
  -c: include client state.
  -p: limit output to given package.
  
##使用


adb shell dumpsys activity DemoActivity

adb shell dumpsys activity Demo

后面的参数可以是组件的名字或者组件名字的一部分