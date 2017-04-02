# NAME

Term::Spinner::Color - A terminal spinner/progress bar with
Unicode, color, and no non-core dependencies.

# SYNOPSIS

```perl
use utf8;
use 5.010;
use Term::Spinner::Color;
my $spin = Term::Spinner::Color->new(
  seq => ['◑', '◒', '◐', '◓'],
  );
$spin->start();
$spin->next() for 1 .. 10;
$spin->done();
```

Or, if you want to not worry about ticking the sequence forward with `next`
you can use the `auto_start` and `auto_done` methods instead.

```perl
use utf8;
use 5.010;
use Term::Spinner::Color;
my $spin = Term::Spinner::Color->new(
  'delay' => 0.3,
  'colorcycle' => 1,
  );
$spin->auto_start();
sleep 5; # do something slow here
$spin->auto_done();
```

# DESCRIPTION

This is a simple spinner, useful when you want to show some kind of activity
during a long-running process of indeterminant length.  It's loosely based
on the API from [Term::Spinner](https://metacpan.org/pod/Term::Spinner) and [Term::Spinner::Lite](https://metacpan.org/pod/Term::Spinner::Lite).  Unlike
[Term::Spinner](https://metacpan.org/pod/Term::Spinner) though, this module doesn't have any dependencies outside
of modules shipped with Perl itself. And, unlike [Term::Spinner::Lite](https://metacpan.org/pod/Term::Spinner::Lite), this
module has color support and support for wide progress bars.

This module also provides an asynchronous mode, which does not require your
program to manually call the `next` method.

Some features and some (Unicode) frame sets do not work in Windows PowerShell
or cmd.exe. If you must work across a wide variety of platforms, choosing
ASCII frame sets is wise. `run_ok` method currently only provides Unicode
output, so it is not suitable for use on Windows (bash, of many types, on
Windows works fine, however).

# ATTRIBUTES

- delay

    If used asynchronously, this is how long each tick will last. It uses the
    [Time::HiRes](https://metacpan.org/pod/Time::HiRes) sleep function, so you can use fractional seconds (0.2 is the
    default, and provides a nice smooth animation, generally).

- seq

    Either an ref to an array containing your prerred spin character frames, or
    a scalar containing the name of your preferred spin character frames, from
    the available defaults. Because it re-draws the whole frame on each tick,
    very long frames may be unwieldy over slow connections. Several nice Unicode
    and ASCII frame sets are provided.

- color

    If provided, this will be the starting color of the spinner. It uses
    [Term::ANSIColor](https://metacpan.org/pod/Term::ANSIColor) color names, and the default is `cyan`.

- colorcycle

    If set to 1, or any truthy value, the colors will cycle through all seven
    of the base ANSI color values changing on each tick of the `seq`.

# METHODS

- start

    Prints the first frame of the `seq`. Your cursor should be placed where
    you want the spinner to appear before starting. This method hides the
    cursor, so if interrupted, it may leave the terminal without a cursor (will
    be fixed sometime...).

- next

    Increments the `seq` and prints the next one. Call this method before or
    after each step in your program to show "progress". If you don't want to
    increment the indicator manually, you can use `auto_start` at the beginning
    of your long-running step or series of steps.

- done

    Resets the cursor to its original position and makes it visible. Call this
    when you are finished running your steps.

- auto\_start

    Forks a new autonomous spinner process. It will print a spinner at the
    current cursor position until `auto_done` is called. If your program does
    not have many short steps, but instead one or more very long-running ones,
    this is likely preferable to the manual ticking process provided by `start`,
    `next`, and `done`.

- auto\_done

    Call this method to end the current spinner process.

- run\_ok

    This is a sort of weird method to eval one expression or a series of
    expressions in a list, with a spinner running throughout. At the end,
    it prints a Unicode checkmark or X to indicate success or failure.

    It currently only works with `seq` frames with length 1, 5, or 7. Any of the
    built-in spinner `seq` options will work with this method.

    It uses eval, so should not be given user-provided data or otherwise
    tricky stuff. It has no protections against shooting of feet.

# BUGS

Somewhat spotty on Windows shells (cmd.exe or PowerShell). PowerShell seems
to have ANSI color support, but Unicode doesn't seem to work.

Does not support multiple simultaneous spinners. It does not know how to
find current spinner position or return to it. The program would likely
need to make use of Curses, which is not in core, and is probably even
less likely to work on Windows shells than the stuff I'm already using.

Requires tput for the run\_ok method to figure out the term column width.

I have no idea how to write tests for this, so there's only a placeholder
test.
