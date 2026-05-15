# Bikeshed WTP Auto-Insertion Skill

The [Web Platform Tests project](https://wpt.fyi)
hosts essentially all the tests for web platform specs
maintained by the W3C, WHATWG, and a few other standards orgs.

Specs written with the [Bikeshed tool](https://speced.github.io/bikeshed/)
can indicate which WPT folders are associated with their specs,
and then insert inline annotations
detailing exactly *which* tests are relevant to the nearby text,
which allows the spec to show those tests inline near the text they're testing,
list test-pass stats,
etc.

However, it's fairly annoying to *do* that inline annotation.
You have to read each test to understand what it's testing,
then you need to find where in the spec it should be inserted.
While not *difficult*, this can take a few minutes per test,
and it *feels like* shitwork.
Until you do so, tho,
Bikeshed continually complains about all the un-annotated tests it knows about.

This repository contains an LLM skill (following the [Agent Skills model](https://agentskills.io/))
which automates the insertion of WPT tests for a given spec.
While in a spec folder, you can ask the agent to "insert missing tests",
and it will use a local Bikeshed install to find the missing tests,
then read both the spec and the tests to determine where best to insert them.
It should only edit the `.bs` spec source file,
and do nothing else permanent.

After running, manually verify that the tests all look like they're in a good spot with `git diff`.

(Still under development, but in local usage with Gemini it does quite well. I've even had it identify tests meant for a different level of the current spec, and correctly insert them into a `<wpt hidden>` element at the end of the doc.)
