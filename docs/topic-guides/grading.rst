.. _grading:

Local grading
=============

**tcframe** allows you to grade solutions locally, on your machine.

Before grading a solution, you must have already generated the test cases:

::

    ./runner

Then, you can "grade" a solution, by executing:

::

    ./runner grade [--solution=<solution-command>]

where ``<solution-command>`` is the command for executing the solution. If it is omitted, the default is ``./solution``.

For example, suppose you have written a problem package for a problem. Your friend also has written an alternate solution to the problem, and he wants to check whether his solution agrees with yours. Let's assume that his solution file is ``solution_alt.cpp``. Compile it into ``solution_alt``, place it in the problem package, and then run

::

    ./runner grade --solution=./solution_alt

The verdicts of each test case, each subtask (if any), as well as the overall verdict will be shown, as described below.

.. _grading_verdicts:

Verdicts
--------

A verdict consists of the verdict status and optionally verdict points.

The recognized statuses, from the best to the worst, are as follows:

Accepted
    The output produced by the solution is correct.

OK
    The output produced by the solution is partially correct.

Wrong Answer
    The output produced by the solution is incorrect. By default, the diff will be shown, truncated to the first 10 lines.

Runtime Error
    The solution crashed or used memory above the limit, if specified.

Time Limit Exceeded
    The solution did not stop within the time limit, if specified.

Internal Error
    Custom :ref:`scorer <styles_scorer>` / :ref:`communicator <styles_communicator>` (if any) crashed or did not give a valid verdict.

Test case verdicts
******************

The verdict of each test case will be shown. For OK statuses, the points (given by the scorer) will be also shown.

Subtask verdicts
****************

If the problem has subtasks, the verdict of each subtask will be shown as well. The verdict of a subtask is the combination of:

- status: the worst status of test case verdicts in the subtask
- points:

  - the subtask points (assigned via ``Points()``), if all test case verdicts in the subtask are Accepted,
  - the minimum points of OK verdicts in the subtask, if at least one test case verdict is OK and the rest are Accepted, or
  - 0, otherwise.

Overall verdict
***************

Finally, the overall verdict is as follows.

For problem without subtasks:

- status: the worst test case verdict status
- points: the sum of test case verdict points, where:

  - an Accepted status will be given 100 / (number of test cases) points
  - an OK status will be given its own points
  - any other status will be given 0 points

For problem with subtasks:

- status: the worst subtask verdict status
- points: the sum of subtask verdict points

Sample local grading output
---------------------------

Here is a sample output of a local grading for problems without subtasks.

.. sourcecode:: bash

    Local grading with solution command: './solution_alt'...

    [ SAMPLE TEST CASES ]
      k-product_sample_1: Accepted

    [ OFFICIAL TEST CASES ]
      k-product_1: Accepted
      k-product_2: Accepted
      k-product_3: OK [21]
      k-product_4: Wrong Answer
        * scorer Diff:
    (expected) [line 01]    11
    (received) [line 01]    12

    [ VERDICT ]
      Wrong Answer [71]

and here is for problems with subtasks.

.. sourcecode:: bash

    Local grading with solution command: './solution_alt'...

    [ SAMPLE TEST CASES ]
      k-product_sample_1: Accepted

    [ TEST GROUP 1 ]
      k-product_1_1: Accepted

    [ TEST GROUP 2 ]
      k-product_2_1: Accepted
      k-product_2_2: Accepted
      k-product_2_3: Accepted

    [ TEST GROUP 3 ]
      k-product_3_1: Accepted
      k-product_3_2: Wrong Answer
        * scorer: Diff:
    (expected) [line 01]    11
    (received) [line 01]    12

      k-product_3_3: Accepted

    [ TEST GROUP 4 ]
      k-product_4_1: Accepted
      k-product_4_2: Accepted
      k-product_4_3: Accepted
      k-product_4_4: Accepted
      k-product_4_5: Accepted
      k-product_4_6: Runtime Error
        * Execution of solution failed:
          - Exit code: 1
          - Standard error:

    [ SUBTASK VERDICTS ]
      Subtask 1: Accepted [40]
      Subtask 2: Wrong Answer [0]
      Subtask 3: Runtime Error [0]

    [ VERDICT ]
      Runtime Error [40]

This local grading feature is useful for creating "unit tests" for your test cases. For each problem, you can write many solutions with different intended results. For example, ``solution_123.cpp`` should pass subtasks 1 - 3; ``solution_12.cpp`` should pass subtasks 1 and 2 but not subtask 3, etc.

Brief mode
----------

You can pass an additional ``--brief`` argument to make the output concise. This is primarily intended to be consumed by scripts instead of human eyes.

The first line of the output contains the overall the verdict in the following format:

::

    <status code> <points>

where the status code mapping is:

- ``AC``: Accepted
- ``OK``: OK
- ``WA``: Wrong Answer
- ``RTE``: Runtime Error
- ``TLE``: Time Limit Exceeded
- ``ERR``: Internal Error


If the problem has subtasks, the subtask verdicts will be output in the following lines, one line per subtask verdict ordered by subtask number, in the same format as above.

The sample outputs from the previous sections would become the following using ``--brief`` argument:

::

    WA 71

and

::

    RTE 40
    AC 40
    WA 0
    RTE 0

Notes
-----

Internally, **tcframe** uses ``ulimit`` to limit the time and memory used when running the solution. Unfortunately, there is no easy way to restrict memory limit on OS X, so the memory limit will be always ignored when using this feature on OS X.
