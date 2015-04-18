Bash-sugar is a framework for easier customization of bash
than provided by the standard bash_{rc,profile} mechanism.

It is intended for those who spend their day in the shell.

There are two modes of operation: "alias" and "cond".  Set
MODE in the .sugar file.

"alias" mode is a simple alias loader. First it attempts
to source the $ACONF file, which should be used to
source individual aliases.  If $ACONF is not present,
all $AEXT files in $ACONF are sourced.  Worst case scenario is 
that the shell will have aliases for commands which may
not exist on the system.

When using "alias" mode, create <some cmd>.$AEXT files
under $ADIR, with the desired 'alias foo="bar -v"' lines
present.  If no $ACONF is present, the alias file will
be sourced by default.  If $ACONF is in use, specify to
source the alias file with a requisite ". <some cmd>.$AEXT"
statement in the conf file.

Now, not all aliases are valid on all systems.  This is
simple enough to control via the conf file, but how to 
accommodate a variety of systems?

To rectify this, use "cond" mode.  This mode enables 
advanced functionality, such as conditionally sourcing
aliases or functions.  The intent is that simple bash
snippets are composed in $CEXT files under $CDIR.  The
snippets should specify a simple conditional which 
leads to further sourcing.

For instance, to differentiate between SysVInit and
Systemd systems, drop this in a .cond file::

  which systemctl
  if [[ !? == 0 ]]
  then
    . $ADIR/systemd.alias
  fi

Alternately, consider deploying some sugar under root:

  if [[ `id -u` == 0 ]]
  then
    . $ADIR/root.alias
  fi

Or source a set of functions based on the current SSID:

  if [[ `iwconfig wlan0 | grep -iPo "(?<=essid).+$"` == "worknet" ]]
  then
    . $FDIR/work.func
  fi

Now, admittedly, this simple framework has no protection against
sourcing a function from a condition file, or even writing a
conditional directly in an alias file.  The point is this is one
thought-out approach to bash sugar management.  Feel free to 
hack it up to your needs.


