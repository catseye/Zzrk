Zzrk - Adventures in the Great Unsaturated Grammar
==================================================

What is it?
-----------

Zzrk is a toy text adventure (or, if you prefer, a toy work of interactive
fiction) written in "pure" Zz — assuming such a concept makes any sense.
Zz is a dynamic meta-language designed for the APE parallel computing
project to enable variants on languages such as FORTRAN to be quickly
developed.  As such, Zz maintains very little distinction between the
language definition level and the language use level.  For this reason, it
might be more accurate to describe Zz as a self-modifying grammar.

Since Zz is typically used to define programming languages, I thought it
would be a nice abuse to try to use it to write an actual program (albeit
a very language-like one.)  The result is Zzrk.


Playing the Game
----------------

First, you need an implementation of Zz.  The only one I've found, and
subsequently the one I've written Zzrk around, is OpenZz.  It can be
gotten from [http://openzz.sourceforge.net/](http://openzz.sourceforge.net/).

Although OpenZz does have an interactive mode, it unfortunately assumes
you're using Zz to describe a compiler.  Since I assume you'll want to
play Zzrk interactively (as opposed to typing the sequence of actions you
want to take into a text file, piping it through Zzrk, and seeing what
happens), here's a guide to working around some of OpenZz's drawbacks.

First, you can't just specify zzrk.zz as the input to ozz.  Instead,
start OpenZz, then at the `ozz>` prompt, type

    /include "zzrk.zz"

You can then treat the `ozz>` prompt as the prompt for the adventure
game parser.  Try the usual actions, and see what happens.  Be forewarned
that any unrecognizable command on your part -- an unknown verb, noun, or
other part of speech -- will trigger a syntax error.  And, because OpenZz
thinks you are the input to a compiler, accumulating 10 of these errors
will actually cause OpenZz to quit!


The Game Itself
---------------

The game itself is embarrassingly simplistic, but there are a few nominal
puzzles just to demonstrate that the standard adventure game furniture can
be concocted in Zz.  Collecting treasures will get you points, and
obtaining 50 points will win the game (that is, if you have 50 points and
you type `score`, you will get a message telling you that you have won.
You will still need to quit OpenZz through some other manual action, such
as typing Ctrl-D, or causing syntax errors.)


Comments
--------

I originally wrote `zzrk.zz` on November 21st, 2003.  I gave it actual
puzzles, wrote this readme, and released it under a BSD license on
May 6, 2007.

A couple of annoying things about Zz...

The documentation for OpenZz doesn't mention that the `/if { }` construct
has a variant that takes an `else` clause.  And, `/return` does not actually
cause an immediate return, it only assigns a value to the nonterminal
under consideration.  For these reasons, I was originally writing many of
my tests twice (like `/if a == b {} /if a != b {}`).  Then I noticed, in
examining the results of `/krules`, that the kernel does in fact support
`else`.  The fact that curly braces actually denote lists means they're not
optional in tests though, and the nesting is still pretty annoying.

The other annoying thing is that there's no way to prioritize selection
of nonterminals.  For example, I feel very strongly that it ought to be
possible to write the `score_of` production like this:

    /$num_t -> "score_of" coin     { /return  5 }
    /$num_t -> "score_of" jewel    { /return 10 }
    /$num_t -> "score_of" sceptre  { /return 15 }
    /$num_t -> "score_of" crown    { /return 20 }
    /$num_t -> "score_of" object^$ { /return  0 }

On the basis that `coin` et al. are more specific than `object^$`, and should
be selected before it.  But, it's not possible to set this up, at least as
far as I can see.  Which is a pity, because this sort of "catch-all"
mechanism would also enable much better error handling.

A couple of nice things about Zz...

The parser-oriented approach is, of course, very nice for an adventure
game.  It was very simple to create a parser which is in some respects
sophisticated: it can understand "pick up the key" just as easily as
"get key".  Even nicer is that the scope rules (verbs that apply only
to objects that are being held, are in the same room, or either) are
implemented directly as productions in the grammar.

Changing the grammar of the language you are using *on the fly* can be
very scary, but also very powerful.  Note that until you learn what the
magic word is, you can't even use it without causing a syntax error.
But afterward it is a full-fledged part of the parser's vocabulary.
Note also that this is how the properties of an object are updated:
the production which handles `e_exit square_room` is replaced by one
that returns a different value, once the door is open.

Some of the action productions are a bit clumsy because they contain
logic that should probably be associated with the object instead.
Putting lists of statements in a property of the object, and using
Zz's `/execute` facility, would probably allow a "method-like" style
that permits this, but I haven't played with it.

Lastly, it's interesting to note that internal helper functions (not
to mention the full complement of OpenZz builtins) are all available to
the player during the course of the game.  For example, try

    move player to chest

and see what happens.  Even better, why not try

    object snake "snake" "Eek!" wall wall wall wall "no" loc player
    look
    examine snake

Using Zz's notion of scopes, it might be possible to protect the
internals from being invoked like this, but considering Zzrk is just a
toy it hardly seems worth bothering.

Happy adventuring!

-Chris Pressey  
May 6, 2007  
Vancouver, BC
