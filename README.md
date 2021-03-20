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
