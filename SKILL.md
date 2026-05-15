---
name: bikeshed-insert-wpt
description: Discover what WPT tests are missing from a Bikeshed file, look up those tests and analyze them to discover what behavior they're testing, then insert <wpt> blocks into the original Bikeshed file listing the tests next to the specification text most relevant to what the test is testing.
---

# Bikeshed WPT Insertion

This skill helps you categorize WPT tests that are associated with a Bikeshed spec, and automatically find where in that spec the tests should be listed (adding them to the spec in `<wpt>` blocks) to make sure the tests are close to the spec text that they're testing.

## Rules

* While executing these tasks, you will edit **only** the **spec document** file. No other files can be edited.
* While executing these tasks, you must only run the `bikeshed` commands specified in the steps, and whatever command you use to fetch files from the GitHub website. No other commands can be run in the shell.

## Workflows

### Insert Missing Tests

When a user asks you to insert missing tests, or insert missing WPTs:
1. Check that there is a file with a `.bs` extension in the current folder (the **spec document**). If there isn't, quit with a message asking to be run in a folder with a Bikeshed file.
2. Run the command `bikeshed --dry-run spec`. In the generated output, look for a message starting with "Warning: There are (N) WPT tests underneath your path prefix **(path prefix)** that aren't in your document". If there is no such message, quit with a message saying that there are no missing WPT tests in the spec document.
3. Otherwise, the next several indented lines of the output (everything up until the next not-indented line) are the paths to tests that need to be analyzed (the **test path**), one path per line. Remember these paths.
4. For each **test path**, analyze and insert the test into the **spec document**, per the skills below.
5. When you've finished inserting all the tests, verify that you've done everything by running `bikeshed --dry-run spec` again. If the "Warning: There are (N) WPT tests..." message shows up again, you missed some; try again. Otherwise, you're done.

### Analyze and Insert a Test

When you are asked to analyze and insert a test at a **test path** into a **spec document**:

1. Read the **spec document**, which will be a local Bikeshed file (with extension `.bs`), a type of Markdown. This document is a web standard, defining a suite of related features for the web. Understand this document fully.
2. Fetch the **test file**, by concatenating the prefix "https://raw.githubusercontent.com/web-platform-tests/wpt/refs/heads/master/" to the **test path** and fetching it from the web. This **test file** will be a WPT (Web Platform Test), which tests some aspect of a feature int he **spec document**. Read and fully understand the **test file**, so that you comprehend what feature from the **spec document** it is testing.
3. Compare the **test file** to the **spec document**, and find the location in the **spec document** that is defining the feature being tested by the **test file**. If there are multiple viable locations, find the one that is most relevant to the **test file** behavior.
4. It's possible that the **test file** is not relevant to this level of the **spec document**, and is instead for a later level (WPT mixes all levels together in a single folder). If this is the case, insert a hidden wpt block containing the **test path** into the **spec document**.
5. Otherwise, at the relevant location in the **spec document**, insert a wpt block containing the **test path**.

### Insert a WPT Block

When you are asked to insert a WPT block containing a **test path** into a **spec document** at a particular location:

1. Look at that location in the **spec document**. If there is already a `<wpt>` element at that location, insert into that element. (If the `<wpt>` element has the `hidden` attribute, do not use it; insert a new `<wpt>` element instead.) Otherwise, add a new `<wpt>` element, which is a block-level element containing newline-separated test paths.
2. Inside the `<wpt>` element that you either found or created, insert a new line containing the **test path**. If the **test path** starts with the **path prefix** found earlier, remove the prefix first, before inserting the shortened path into the document.

### Insert a Hidden WPT Block

When you are asked to insert a hidden WPT block containing a **test path** into a **spec document**:

1. Look at the end of the **spec document** for a `<wpt>` element with the `hidden` attribute; aka `<wpt hidden>`.  If you can't find one, create a new `<wpt hidden>` element, which is a block-level element containing newline-separated test paths.
2. Inside the `<wpt>` element that you either found or created, append the **test path** to the content of the element on a new line. If the **test path** starts with the **path prefix** found earlier, remove the prefix first, before inserting the shortened path into the document.



## Examples

## No Existing `<wpt>` Block

Assume we start with a **path prefix** of "css/cssom-view", and a **test path** of "css/cssom-view/element-scroll-arguments.html". After analyzing the **test file** and the **spec document**, the following snippet of the **spec document** is determined to be the best place to insert the test:

```

When the <dfn method for=Window lt="scroll(options)|scroll(x, y)">scroll()</dfn> method is invoked these
steps must be run:

1. If invoked with one argument, follow these substeps:
    1. Let <var>options</var> be the argument.
    1. Let <var>x</var> be the value of the {{ScrollToOptions/left}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the x axis otherwise.
    1. Let <var>y</var> be the value of the {{ScrollToOptions/top}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the y axis otherwise.
1. If invoked with two arguments, follow these substeps:
    1. Let <var>options</var> be null <a lt="converted to an IDL value">converted</a> to a {{ScrollToOptions}} dictionary. [[!WEBIDL]]
    1. Let <var>x</var> and <var>y</var> be the arguments, respectively.
1. <a>Normalize non-finite values</a> for <var>x</var> and <var>y</var>.
1. If there is no <a>viewport</a>, return a resolved {{Promise}} and abort the remaining steps.
1. Let <var>viewport width</var> be the width of the <a>viewport</a> excluding the width of the scroll bar, if any.
1. Let <var>viewport height</var> be the height of the <a>viewport</a> excluding the height of the scroll bar, if any.
```

There is not currently a `<wpt>` block here, so a new one must be inserted. This should result in the following changed snippet:

```
When the <dfn method for=Window lt="scroll(options)|scroll(x, y)">scroll()</dfn> method is invoked these
steps must be run:

1. If invoked with one argument, follow these substeps:
    1. Let <var>options</var> be the argument.
    1. Let <var>x</var> be the value of the {{ScrollToOptions/left}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the x axis otherwise.
    1. Let <var>y</var> be the value of the {{ScrollToOptions/top}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the y axis otherwise.
1. If invoked with two arguments, follow these substeps:
    1. Let <var>options</var> be null <a lt="converted to an IDL value">converted</a> to a {{ScrollToOptions}} dictionary. [[!WEBIDL]]
    1. Let <var>x</var> and <var>y</var> be the arguments, respectively.
1. <a>Normalize non-finite values</a> for <var>x</var> and <var>y</var>.
1. If there is no <a>viewport</a>, return a resolved {{Promise}} and abort the remaining steps.
1. Let <var>viewport width</var> be the width of the <a>viewport</a> excluding the width of the scroll bar, if any.
1. Let <var>viewport height</var> be the height of the <a>viewport</a> excluding the height of the scroll bar, if any.

<wpt>
element-scroll-arguments.html
</wpt>
```

Note that the **test path** has been trimmed of the **path prefix**, and the test was inserted on its own line.

### Merging with an Existing `<wpt>` Block

Assuming that the next test also has the **path prefix** "css/cssom-view/" and the **test path** "css/cssom-view/element-scroll-promise-interruption.html", and after analyzing the **test file** and the **spec document**, the same snippet of the **spec document** was found to be the best place to insert the test. There's already a `<wpt>` block there, as shown in this snippet:

```
When the <dfn method for=Window lt="scroll(options)|scroll(x, y)">scroll()</dfn> method is invoked these
steps must be run:

1. If invoked with one argument, follow these substeps:
    1. Let <var>options</var> be the argument.
    1. Let <var>x</var> be the value of the {{ScrollToOptions/left}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the x axis otherwise.
    1. Let <var>y</var> be the value of the {{ScrollToOptions/top}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the y axis otherwise.
1. If invoked with two arguments, follow these substeps:
    1. Let <var>options</var> be null <a lt="converted to an IDL value">converted</a> to a {{ScrollToOptions}} dictionary. [[!WEBIDL]]
    1. Let <var>x</var> and <var>y</var> be the arguments, respectively.
1. <a>Normalize non-finite values</a> for <var>x</var> and <var>y</var>.
1. If there is no <a>viewport</a>, return a resolved {{Promise}} and abort the remaining steps.
1. Let <var>viewport width</var> be the width of the <a>viewport</a> excluding the width of the scroll bar, if any.
1. Let <var>viewport height</var> be the height of the <a>viewport</a> excluding the height of the scroll bar, if any.

<wpt>
element-scroll-arguments.html
</wpt>
```

Because there is already a `<wpt>` block, we must add our **test path** to the existing block, and not create our own. The result should look like this:

```
When the <dfn method for=Window lt="scroll(options)|scroll(x, y)">scroll()</dfn> method is invoked these
steps must be run:

1. If invoked with one argument, follow these substeps:
    1. Let <var>options</var> be the argument.
    1. Let <var>x</var> be the value of the {{ScrollToOptions/left}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the x axis otherwise.
    1. Let <var>y</var> be the value of the {{ScrollToOptions/top}} dictionary member of <var>options</var>, if present,
        or the <a>viewport’s</a> current scroll position on the y axis otherwise.
1. If invoked with two arguments, follow these substeps:
    1. Let <var>options</var> be null <a lt="converted to an IDL value">converted</a> to a {{ScrollToOptions}} dictionary. [[!WEBIDL]]
    1. Let <var>x</var> and <var>y</var> be the arguments, respectively.
1. <a>Normalize non-finite values</a> for <var>x</var> and <var>y</var>.
1. If there is no <a>viewport</a>, return a resolved {{Promise}} and abort the remaining steps.
1. Let <var>viewport width</var> be the width of the <a>viewport</a> excluding the width of the scroll bar, if any.
1. Let <var>viewport height</var> be the height of the <a>viewport</a> excluding the height of the scroll bar, if any.

<wpt>
element-scroll-arguments.html
element-scroll-promise-interruption.html
</wpt>
```

This change preserves the existing tests, and appends our new **test path** line to the end of the existing `<wpt>` block.
