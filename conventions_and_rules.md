Conventions & Rules
===================

First off, conventions and rules are essentially the same thing: the rule "no
trival assertions" could also be written as the convention "all assertions must
be non-trivial". Just two different ways of wording the same thing that we seem
to use interchangeably depending on what sounds nicer.

What they ultimately provide is our primary means of knowledge transfer. They
help us avoid classes of problems that we know we're likely to encounter, and
they help us ensure that code will be resuable in future abstractions by
following common patterns. They are invaluable because it is rarely immediately
apparent from the code we're working on what might go wrong, or how it might be
used in the future. Conventions and rules effeciently dissemenate knowlege both
across a team, and most importantly to future teams.

[Wikipedia](https://en.wikipedia.org/wiki/Coding_conventions) has a lot more
good, in-depth, rationale. As does probably every book on programming.

Breaking the rules
------------------

Sometimes it's tempting to break the rules. Typically because doing so seems
much easier than following the rule. There's nothing really wrong with that,
being lazy is often beneficial (no premature abstractions). The hard part though
is that it's usually not obvious what could possibly go wrong by breaking the
rule. Maybe a bug pops up next month and someone else has to fix it. Maybe
someone is working on an abstraction next year and now there is one exception to
it that they have to refactor. These cases can become particulary annoying when
the new implementor spends time and effort and finds it was because someone else
(often themselves-from-the-past) didn't follow a known convention. Time saved
now ends up costing more time later.

This doesn't mean we should just always follow the rules though.
There are _rules_ for breaking rules:

Breaking a rule might be a pretty good idea in a given scenario. Making that
decision thoughtfully typically requires _more_ work than following the rule
though. These are some of the conventions of convention-breaking, and the
conventions by which they might be applied.

1. There is a valid exception:

There might be a valid case for an exception to the rule. Proving this often
takes a _lot_ of work. It might involve an exhaustive survey of things that can
go wrong and why they don't apply to this scenario. Or it might involve a review
of a given convention and making a solid case of why not following it in the
given scenario will (or likely _might_) lead to a more desirable outcome. All of
this work then must be documented. If it turns out to be correct it will become
part of the new rationale for the rule. We usually add these comments (sometimes
in the form of lengthy novels) in the exceptional code first, eventually
formalizing them into documentation as the exception becomes part of the
convention. The key takeaway though is that a valid case for an exception should
come with a lot of documentation.

2. The rule sucks:

Rules can sometimes be consistently difficult or annoying to follow. Maybe they
save us from a future headache, but if they're causing us lots of headaches now
then they may not be worth the hassle. In these cases what we want is a better
rule, one which still gets us where we need to go without impeding our lives and
making us sad. Typically we first need to identify _why_ the rule is difficult
or annoying and then figure out ways we can improve it.

3. The rule is wrong:

Rules can be wrong or outdated. Sometimes they were based on wrong assumptions.
Mabye the problem they were meant to avoid doesn't apply (or no longer
applies). A better solution might have solved it more generally to where it can
no longer happen (imagine web security before SSL). These changes seem to come
with a lot of FUD that can make it confusing about whether they still apply or
not, and we can waste a lot of time and/or money following rules that provide no
real benefit (see the entire SSL industry). Because of this it's always
important to question the rules and conventions. Discussion and resistance to
rules should be embraced and encouraged. If our rules don't stand up to critical
analysis then it will benefit everyone to get rid of them.

Sidenote:

  FUD and outdated assumptions should be met with _understanding_. We're
  following these rules and conventions because they are immensely helpful, it's
  only natural that we're going to repeat some of them without always applying
  the full gammut of our critical thinking abilities (that's really the whole
  point after all). It seems we have a natural tendency that once we've learned
  something new we start to demonize all spreaders of (what is now)
  misinformation as some kind of evil idiots. But really that's why we make
  these rules: so we can be all be idiots about the common stuff and reserve our
  big brains for solving the novel problems.

4. It's way too hard:

Sometimes the juice just isn't worth the squeeze. We accept that breaking the
rule might lead to future problems. We still all agree the rule is helpful and
should be followed. But the cost (usually measured in some combination of time
and mental anguish) of following the rule in the given scenario is so high that
it's worth exposing the risk of future problems. Clearly this should be rare, or
the rule itself might need to be reconsidered, but it does happen and we should
accept it so that we can mitigate the risks. In these cases I think very long
explanatory comments explaining exactly why the decision was made, acknowledging
the rule was broken (so that the new pattern will not be followed), and
addressing what terrible things may now happen are then needed to help (and
assuage) whoever has to fix the future problems. Lengthy comments also serve a
nice double-duty of forcing ourselves to logically organize and all of our
thoughts on exactly _why_ the rule must be broken -- the process of which can
often lead to a better solution than breaking the rule in the first place.

5. It's just more fun:

Finally, probably the best reason, breaking rules lets us really learn about why
the rule was there in the first place. The documentation might be really
helpful, but it's never as helpful as figuring it yourself (and having to fix
the resulting issues yourself). Breaking the rule might be the only way to
really understand the core of where the rule or convention came from, and the
more people that really understand it the more people there are to come up with
new, better solutions. In practice this is usually best done in your own side
projects. It can be a hard sell to your team that you want to risk lighting the
servers on fire so you can learn how to put them out.

Breaking the rules and getting away with it
-------------------------------------------

There is a convention in here about convention-breaking: Anything unconventional
requires lots of comments. It is conventionally argued that comments themselves
are a code smell and that code should be self-documenting. This seems like a
reasonable rule, and breaking conventions is its exception. A single, simple,
fully understandable line of code doesn't tell you anything about _why_ it's
there. The thing you rely on to understand _why_ it's there is that it follows a
convention and so it clearly _should_ be there. So it stands to reason then that
the purpose of any lines of code that defy a convention will _not_ be readily
understable. Comments being a code smell is probably still true: we shouldn't be
breaking conventions and thus needing comments explaining them. But if we are
going to accept that sometimes we have to break conventions then it follows that
that in those cases comments can be required and expected. In practice this
becomes the main reason we write comments at all: to convey understanding _where
it breaks from the norm_.
