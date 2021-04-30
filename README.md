Lessons learned doing full-stack-integration-testing-driven development for 10+
years

Test-driven: all functionality is covered by a test. For the most part tests are
written first (though sometimes filled in later), but either way the
functionality ends up covered.

* CI takes ~20 minutes (~1700 full stack tests running in parallel on 32
  virtual nodes, 3 processes per node = 96 test processes).
* Average single test time is ~30s
* Fastest single test time is ~5s
* Slowest single test time is ~160s (2:40)
* Trying to get all tests running in < 60s for better random parallel
  distribution

The testing decision really drive our workflow:

* All code layers (we use Rails-style MVC + OO) are tested via the UI: do the
  given inputs result in the expected outputs?
* All full-stack integration testing + avoiding mocking/stubbing means the only
  code changes that effect tests are UI changes.
  * Nothing is tied to a language choice, mocks, stubs, etc.
    * We use Ruby, but we could change to any other language without affecting
      the tests as long as the UI stayed the same
  * UI changes cause test changes (typically handled w/ find/replace)
  * Moved from jQuery widgets to Backbone to React to Stimulus without changing
    the test code. In many cases we refactor old widgets without affecting the
    test.

* Focus on refactoring. This means code design matters less up front.
  "Anticipating change" becomes less of an issue because refactoring is easy so
  the focus becomes avoiding assumptions about the future and writing less,
  simpler code, expecting it to change in unknown ways at any time.
* Incremental refactoring becomes easy. We can change one 10+ year old jQuery
  widget to use Stimulus while leaving the other 10+ year old jQuery widgets
  largely untouched.
  * We've been productive (deploy multiple features daily) for 10+ years.
  * "Re-write" is never really a question. Incremental refactoring always
    provides a path forward when we want to change patterns/process/technology.

* Tradeoff is slower tests.

* 30s is "fast enough" to have a roughly "get the feature green" type
  development cycle. 20m to wait for CI is too slow for an "always green"
  development cycle.
  * Often work on 2 things at once. Get one of them green, send it to CI, jump
    switch to another feature, get that green, push to CI and switch back.
  * Refactoring can get out of control, a change to get the feature green can
    break the build in a big way when you don't know for 20m. Sometimes have to
    roll back an idea and try something else.
    * Incremental refactoring often requires changing part of a feature without
      changing the rest to avoiding the refactor cascading into many more parts.
    * We add `FIXME` comments for these cases where we have to old "old code"
      that doesn't fit current patterns.
  * 20m build time is also "fast enough" -- we could get it faster with
    additional effort, but 20m has been fast enough to get feedback.

* Flapping tests
  * Often are caused by real race conditions users might run into
  * Fix them immediately or they will add up and be terrible
    * Our rule is that if one pops up on your test run you should fix it (and
      announce it)
  * Tend to fall into a few categories so they become easier to fix over time
    * Race conditions
    * Leftover state
  * We don't automatically deploy so we can re-run the failures and deploy if
    they pass
    * In a CD-only environment might want to a manual option for this case

One issue I always encountered writing smaller unit tests was knowing exactly
what to test. We were typically working in an MVC pattern. The models would end
up very well covered, the controllers much less so, and the views almost not at
all. This coincided with usually starting our problem solving at the model
layer, a practice I think is fairly common and I still see even when using an
integration-only approach. Most programming books I've read seem to start at the
model layer too, and most discussion online seems to be focused on the model
layer. I guess this probably comes from object-oriented programming and it's
focus on domain objects driving code organization. You need a file to start
writing code, and the domain objects typically determine the file organization.

Approaching the models first then often leads to some issues because you have to
make a lot of guesses about how the code is going to be called. The tests for
those guesses then reinforce the assumptions. One because it's now harder to
change them when you get to the view layer and realize the data needs to be
structured a little differently, but also two because the tests themselves
become a caller, giving a false sense of utility. If the only thing calling a
method (or using an option) is a test, it would be better to get rid of that
method: it has no utility to the app, it's just extra code to maintain. I think
all of this is then compounded by our tendency to want to keep these decisions
that we spent time on, so there is always this desire to keep the model the way
it is because it's so clean and well tested, which then means the wrong design
bleeds into all the callers (typically the controller and views).

Integration-testing I think is very conducive to outside-in, or top-down,
problem solving. The tests are always testing the views, and the views are
ultimately the arbiters of the use cases. If some code is inaccessible to any
view then it very well might not need to exist. The test is always testing
whether the inputs to the browser result in the proper outputs. So it makes it
easy for the first thing you write to be parts of the the views themselves (the
input view, like a form, and the output view if they're separate). Now you don't
have to make guesses about the model, you know what the requirements are because
you just wrote the callers in the view. It's definitely weird to get used to
writing callers before you write the methods that they will call, but I think
the end result is that it maximizes efficiency. You don't spend any time writing
(and testing) anything you don't actually need to use.

Another reason I commonly had for different coverage across different parts of
the app was separation of technologies. Model testing seemed to be the most
straightforward and so was usually done the most. Since models have the most
reuse it was also the biggest bang for the buck, so if the technology was hard
it still seemed worth it. Testing controllers became difficult because it
involved setting up lots of state about handling lots of moving pieces, like
the request environment, request parameters, cookies, etc... -- compared to the
models which worked well in isolation. Isolated view testing became even more
complex. It requires a bunch more complex state setup, and then when you get to
JavaScript behavior might even require writing tests in a completely different
programming languge. Depending on the level of isolation destired each of these
might even require libraries specific to the implementation, for instance
isolated React tests might be fundamentally entirely different than isolated
jQuery tests. Switching technologies means learning how to write new tests.

Integration test avoids this entirely. There is a single language and technology
for testing everything in the entire stack. It is simply automating the browser,
so really the browser is abstracting away underlying technology differences for
us. We can test all layers with the same language, and changing underlying
technologies doesn't affect our test code (as long as we're still using a
browser*).

* There's nothing really tying us to a browser either. We can just as easily
integration-test a JSON API. In that case the test interface is just JSON, so
anything that can read and write JSON and send it to an API will work. We can
change everything about the underlying JSON API from Ruby to Rust, and the code
that we're using to test the API itself should stay the same.
