# codeclimate-test-reporter

[![Code Climate][cc-badge]][cc-repo]
[![Test Coverage][cc-coverage-badge]][cc-coverage]
[![PyPI version][pypy-badge]][pypy]

[cc-badge]: https://codeclimate.com/github/codeclimate/python-test-reporter/badges/gpa.svg
[cc-coverage-badge]: https://codeclimate.com/github/codeclimate/python-test-reporter/badges/coverage.svg
[cc-repo]: https://codeclimate.com/github/codeclimate/python-test-reporter
[cc-coverage]: https://codeclimate.com/github/codeclimate/python-test-reporter/coverage
[pypy-badge]: https://badge.fury.io/py/codeclimate-test-reporter.svg
[pypy]: https://pypi.python.org/pypi/codeclimate-test-reporter

Collects test coverage data from your Python test suite and sends it to Code
Climate's hosted, automated code review service.

Code Climate - [https://codeclimate.com][codeclimate.com]

## Uploading Your Test Coverage Report

The `codeclimate-test-reporter` is compatible with [coverage.py][] coverage
reports. By default, coverage.py will generate a `.coverage` file in the current
directory. `codeclimate-test-reporter`, run without arguments, will
look for a coverage report at this default location.

[coverage.py]: https://coverage.readthedocs.org/

Note: The `codeclimate-test-reporter` requires a repo token from
[codeclimate.com][], so if you don't have one, the first step is to [signup][]
and configure your repo.  Then:

[codeclimate.com]: https://codeclimate.com
[signup]: https://codeclimate.com/signup

You can place the repo token in the environment under the key
`CODECLIMATE_REPO_TOKEN` or pass the token as a CLI argument:

```console
$ CODECLIMATE_REPO_TOKEN=[token] codeclimate-test-reporter
Submitting payload to https://codeclimate.com... done!
```

```console
$ codeclimate-test-reporter --token [token]
Submitting payload to https://codeclimate.com... done!
```

We recommend configuring the repo token in the environment through your CI
settings which will hide the value during runs. The token should be considered a
scoped password. Anyone with the token can submit test coverage data to your
Code Climate repo.

```console
# CODECLIMATE_REPO_TOKEN already set in env
$ codeclimate-test-reporter
Submitting payload to https://codeclimate.com... done!
```

### Generating Coverage Reports

To generate a coverage report with [pytest][], you can use the [pytest-cov][]
plugin:

```console
$ py.test --cov=your_package tests/
TOTAL                                                     284     27    90%

======================== 14 passed in 0.75 seconds ========================
```

To generate a coverage report with [nose][], you can use the [nose cover plugin][]:

[pytest]: http://pytest.org
[nose]: https://nose.readthedocs.org
[pytest-cov]: https://pypi.python.org/pypi/pytest-cov
[nose cover plugin]: https://nose.readthedocs.org/en/latest/plugins/cover.html

```console
$ nosetests --with-coverage --cover-erase --cover-package=your_package
TOTAL                                                284     27    90%
----------------------------------------------------------------------
Ran 14 tests in 0.743s

OK
```

By default, coverage.py will create the test coverage report at `./.coverage`.
If you configure coverage.py to generate a coverage report at an alternate
location, pass that to the `codeclimate-test-reporter`:

```console
$ codeclimate-test-reporter --file ./alternate/location/.coverage
Submitting payload to https://codeclimate.com... done!
```

### Tox

If you happen to be using TOX for your tests, you will need to pass
your CI's Environemntal Variables to the environment you wish to report from,
otherwise Codeclimate will not be able to tell which branch your commit belongs to.

Your `tox.ini` should then look a little bit like this:

```ini
[testenv:py35]
commands =
    py.test --cov=your_package tests/
    codeclimate-test-reporter --file .coverage
passenv =
    CODECLIMATE_REPO_TOKEN
    TRAVIS_BRANCH
    TRAVIS_JOB_ID
    TRAVIS_PULL_REQUEST
    CI_NAME
```

The CI specific variables are:

* Travis-CI: CI_NAME, TRAVIS_BRANCH, TRAVIS_JOB_ID, TRAVIS_PULL_REQUEST
* CircleCI: CI_NAME, CIRCLE_BRANCH, CIRCLE_BUILD_NUM, CIRCLE_SHA1
* Semaphore: CI_NAME, BRANCH_NAME, SEMAPHORE_BUILD_NUMBER
* Jenkins: CI_NAME, BUILD_NUMBER, BUILD_URL, GIT_BRANCH, GIT_COMMIT
* Tddium: CI_NAME, TDDIUM_SESSION_ID, TDDIUM_TID
* Wercker: CI_NAME, WERCKER_BUILD_ID, WERCKER_BUILD_URL, WERCKER_GIT_BRANCH, WERCKER_GIT_COMMIT
* Appveyor:
    * CI_NAME, APPVEYOR_BUILD_ID, APPVEYOR_API_URL, APPVEYOR_REPO_BRANCH
    * APPVEYOR_REPO_COMMIT, APPVEYOR_PULL_REQUEST_NUMBER
* Drone: CI_NAME, CI_BUILD_NUMBER, CI_BUILD_URL, CI_BRANCH, CI_COMMIT, CI_PULL_REQUEST
* Codeship: CI_NAME, CI_BUILD_NUMBER, CI_BUILD_URL, CI_BRANCH, CI_COMMIT_ID
* Vexor: CI_NAME, CI_BUILD_NUMBER, CI_BUILD_URL, CI_BRANCH, CI_BUILD_SHA, CI_PULL_REQUEST_ID
* Buildkite: CI_NAME, BUILDKITE_JOB_ID, BUILDKITE_BUILD_URL, BUILDKITE_BRANCH, BUILDKITE_COMMIT
* Gitlab-CI: CI_NAME, CI_BUILD_ID, CI_BUILD_REF_NAME, CI_BUILD_REF

## Installation

You can install the codeclimate-test-reporter using pip:

```console
$ pip install codeclimate-test-reporter
Successfully installed codeclimate-test-reporter-[version]
```

Or you can add the reporter to your `requirements.txt` file and install it along
with other project dependencies:

```txt
# requirements.txt
codeclimate-test-reporter
```

```console
$ pip install -r requirements.txt
Successfully installed codeclimate-test-reporter-[version]
```

## Important FYIs

Across the many different testing frameworks, setups, and environments, there
are lots of variables at play. Before setting up test coverage, it's important
to understand what we do and do not currently support:

* **Default branch only:** We only support test coverage for your default
  branch. Be sure to check out this branch before running your tests.

* **Single payload:** We currently only support a single test coverage payload
  per commit. If you run your tests in multiple steps, or via parallel tests,
  Code Climate will only process the first payload that we receive. If you are
  using a CI, be sure to check if you are running your tests in a parallel mode.

  **Note:** If you've configured Code Climate to analyze multiple languages in
  the same repository (e.g., Python and JavaScript), we can nonetheless only
  process test coverage information for one of these languages. We'll process
  the first payload that we receive.

* **Invalid File Paths:** By default, our test reporters expect your application
  to exist at the root of your repository. If this is not the case, the file
  paths in your test coverage payload will not match the file paths that Code
  Climate expects.

## Troubleshooting

If you're having trouble setting up or working with our test coverage feature,
see our detailed [help doc][], which covers the most common issues encountered.

[help doc]: http://docs.codeclimate.com/article/220-help-im-having-trouble-with-test-coverage

If the issue persists, feel free to [open up an issue][issue] here or contact
help with debug information from the reporter:

[issue]: https://github.com/codeclimate/python-test-reporter/issues/new

```console
$ codeclimate-test-reporter --debug
codeclimate-test-reporter [version]
Requests 2.9.1
Python 3.5.1 (default, Jan 22 2016, 08:54:32)
[GCC 4.2.1 Compatible Apple LLVM 7.0.2 (clang-700.1.81)]
/usr/local/opt/python3/bin/python3.5
Darwin 15.4.0
```

## Contributions

Patches, bug fixes, feature requests, and pull requests are welcome.

## Development

A `Dockerfile` is included for developer convenience. Simply run `make test` to
run the test suite and coverage report.

To release a new version, first run `./bin/prep-release [version]` to bump the
version and open a PR on GitHub.

Once the PR is merged, run `make release` to publish the new version to pypy and
create a tag on GitHub. This step requires pypy credentials at `~/.pypirc`. You
can copy this repo's `.pypirc.sample` file as a starting point.

## Copyright

See [LICENSE.txt][license]

[license]: https://github.com/codeclimate/python-test-reporter/blob/master/LICENSE.txt
