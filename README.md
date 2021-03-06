OVERVIEW
========

Log::Progress implements a text protocol for progress reporting through
a standard text logfile.  Progress bars are a nice feature to have, but
often take more time to implment than the project is worth.  This approach
allows you to add simple text output to a script or module and then view
it from the command line or write web views to display it.

PROTOCOL
========

The syntax for progress-update messages is:

  "progress:" WS [STEP_ID WS] FRACTION [WS "-"] [WS MESSAGE]

the syntax for declaring a new sub-step is:

  "progress:" WS STEP_ID WS ["(" CONTRIBUTION ")" | "-"] WS TITLE

and the syntax for arbitrary progress-related data is:

  "progress:" WS [STEP_ID WS] "{" JSON "}"

where WS is one or more whitespace characters, the optional STEP_ID designates
a sub-step, FRACTION is either a floating point number between 0 and 1 or a
fraction (whose numerator and denominator could be floating point numbers),
CONTRIBUTION is a floating point number between 0 and 1 indicating how step
progress affects the parent progress, MESSAGE and TITLE are plain text, and
JSON is arbitrary JSON content to be used for special cases or extensions. 

This sequence gives you a basic progress bar:

    progress: 0
    progress: 0.3
    progress: 0.7
    progress: 0.9
    progress: 1

This gives you status labels for the progress bar:

    progress: 0.00 - Preparing to do the thing
    progress: 0.75 - The thing is mostly done
    progress: 0.90 - Cleaning up loose ends
    progress: 1.00 - Done

If you want to divide the progress into multiple sub-steps:

    progress: collect (.2) Building catalog of things to sync
    progress: check   (.2) Checking catalog vs. remote catalog
    progress: xfer    (.5) Transferring data
    progress: commit  (.1) Committing transaction
    progress: collect 400/4000
    progress: collect 800/4000
    progress: collect 1600/8000
    progress: collect 9000/10000
    progress: collect 10000/10000
    progress: check   0.1 - 1000 of 10000
    progress: check   0.7 - 7000 of 10000
    progress: check   1
    progress: xfer    0.5 - 228 of 456
    progress: xfer    1
    progress: commit  1 - All changes saved

In that example, the sub-steps were performed in sequence, but you can see how
it is possible to have steps overlap, which could be rendered as multiple
progress bars moving in parallel.  You can also see an example of the sub-step
percentages, where the full progress of a sub-step represents some portion of
the parent progress bar.  If those are omitted, you can continue to report
overall progress independent of the sub-steps.

Sub-steps can have their own sub-steps:

    progress: copy - Copy files
    progress: copy.f1 (.001) File One
    progress: copy.f1.data (.9) File One data xfer
    progress: copy.f1.cksm (.1) File One verify checksum

but that level of detail could also just be shown in the progres status
message.  But options are nice.

