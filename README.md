The Web Platform Tests Project [![IRC chat](https://goo.gl/6nCIks)](http://irc.w3.org/?channels=testing)
==============================

The Web Platform Tests Project is a W3C-coordinated attempt to build a
cross-browser testsuite for the Web-platform stack. Writing tests in a
way that allows them to be run in all browsers gives browser projects
confidence that they are shipping software that is compatible with other
implementations, and that later implementations will be compatible with
their implementations. This in turn gives Web authors/developers
confidence that they can actually rely on the Web platform to deliver on
the promise of working across browsers and devices without needing extra
layers of abstraction to paper over the gaps left by specification
editors and implementors.

Running the Tests
=================

The tests are designed to be run from your local computer. The test
environment requires Python 2.7+ (but not Python 3.x). You will also
need a copy of OpenSSL. Users on Windows should read the
[Windows Notes](#windows-notes) section below.

To get the tests running, you need to set up the test domains in your
[`hosts` file](http://en.wikipedia.org/wiki/Hosts_%28file%29%23Location_in_the_file_system). The
following entries are required:
e.g. When running Web Platform Tests pc's ip address is 192.168.1.100

```
192.168.1.100	web-platform.test
192.168.1.100	www.web-platform.test
192.168.1.100	www1.web-platform.test
192.168.1.100	www2.web-platform.test
192.168.1.100	xn--n8j6ds53lwwkrqhv28a.web-platform.test
192.168.1.100	xn--lve-6lad.web-platform.test
```

Because web-platform-tests uses git submodules, you must ensure that
these are up to date. In the root of your checkout, run:

```
git submodule update --init --recursive
```

For Hybridcast TV, The test environment need to run dns server.
Because Hybridcast TV doesn't have [`hosts` file](http://en.wikipedia.org/wiki/Hosts_%28file%29%23Location_in_the_file_system).
run with root privileges:

```
python ./tools/dns/dns.py 192.168.1.100
```

This dns server resolve test domains used by Web Platform Tests.
Change DNS servers settings to running Web Platform Tests pc's ip address on Hybridcast TV.


The test environment can then be started using

```
./serve
```

This will start HTTP servers on two ports and a websockets server on
one port. By default one web server starts on port 8000 and the other
ports are randomly-chosen free ports. Tests must be loaded from the
*first* HTTP server in the output. To change the ports, edit the
`config.json` file, for example, replacing the part that reads:

```
"http": [8000, "auto"]
```

to some port of your choice e.g.

```
"http":[1234, "auto"]
```

If you installed OpenSSL in such a way that running `openssl` at a
command line doesn't work, you also need to adjust the path to the
OpenSSL binary. This can be done by adding a section to `config.json`
like:

```
"ssl": {"openssl": {"binary": "/path/to/openssl"}}
```

<span id="windows-notes">Windows Notes</span>
=============================================

Running wptserve with SSL enabled on Windows typically requires
installing an OpenSSL distribution.
[Shining Light](http://slproweb.com/products/Win32OpenSSL.html)
provide a convenient installer that is known to work, but requires a
little extra setup.

After installation ensure that the path to OpenSSL is on your `%Path%`
environment variable.

Then set the path to the default OpenSSL configuration file (usually
something like `C:\OpenSSL-Win32\bin\openssl.cfg` in the server
configuration. To do this copy `config.default.json` in the
web-platform-tests root to `config.json`. Then edit the JSON so that
the key `ssl/openssl/base_conf_path` has a value that is the path to
the OpenSSL config file.


Test Runner
===========

There is a test runner that is designed to provide a
convenient way to run the web-platform tests in-browser. It will run
testharness.js tests automatically but requires manual work for
reftests and manual tests.

The runner can be found at `/tools/runner/index.html` on the local
server i.e.

```
http://web-platform.test:8000/tools/runner/index.html
```

in the default configuration. The first time you use this it has to
generate a manifest of all tests. This may take some time, so please
be patient.

Publication
===========

The master branch is automatically synced to http://w3c-test.org/.

Pull requests that have been checked are automatically mirrored to
http://w3c-test.org/submissions/.

Finding Things
==============

Each top-level directory represents a W3C specification: the name
matches the shortname used after the canonical address of the said
specification under http://www.w3.org/TR/ .

For some of the specifications, the tree under the top-level directory
represents the sections of the respective documents, using the section
IDs for directory names, with a maximum of three levels deep.

So if you're looking for tests in HTML for "The History interface",
they will be under `html/browsers/history/the-history-interface/`.

Various resources that tests depend on are in `common`, `images`, and
`fonts`.


If you're looking at a section of the specification and can't figure
out where the directory is for it in the tree, just run:

```
node tools/scripts/id2path.js your-id
```

Branches
========

In the vast majority of cases the **only** upstream branch that you
should need to care about is `master`. If you see other branches in
the repository, you can generally safely ignore them.

Contributing
============

Save the Web, Write Some Tests!

Absolutely everyone is welcome (and even encouraged) to contribute to
test development, so long as you fulfill the contribution requirements
detailed in the [Contributing Guidelines][contributing]. No test is
too small or too simple, especially if it corresponds to something for
which you've noted an interoperability bug in a browser.

The way to contribute is just as usual:

* Fork this repository (and make sure you're still relatively in sync
  with it if you forked a while ago).
* Create a branch for your changes:
  `git checkout -b topic`.
* Make your changes.
* Run the lint script described below.
* Commit locally and push that to your repo.
* Send in a pull request based on the above.

Lint tool
---------

We have a lint tool for catching common mistakes in test files. You
can run it manually by starting the `lint` executable from the root of
your local web-platform-tests working directory like this:

```
./lint
```

The lint tool is also run automatically for every submitted pull
request, and reviewers will not merge branches with tests that have
lint errors, so you must fix any errors the lint tool reports. For
details on doing that, see the [lint-tool documentation][lint-tool].

But in the unusual case of error reports for things essential to a
certain test or that for other exceptional reasons shouldn't prevent
a merge of a test, update and commit the `lint.whitelist` file in the
web-platform-tests root directory to suppress the error reports. For
details on doing that, see the [lint-tool documentation][lint-tool].

[lint-tool]: https://github.com/w3c/web-platform-tests/blob/master/docs/lint-tool.md

Adding command-line scripts ("tools" subdirs)
---------------------------------------------

Sometimes you may want to add a script to the repository that's meant
to be used from the command line, not from a browser (e.g., a script
for generating test files). If you want to ensure (e.g., for security
reasons) that such scripts won't be handled by the HTTP server, but
will instead only be usable from the command line, then place them in
either:

* the `tools` subdir at the root of the repository, or

* the `tools` subdir at the root of any top-level directory in the
  repository which contains the tests the script is meant to be used
  with

Any files in those `tools` directories won't be handled by the HTTP
server; instead the server will return a 404 if a user navigates to
the URL for a file within them.

If you want to add a script for use with a particular set of tests but
there isn't yet any `tools` subdir at the root of a top-level
directory in the repository containing those tests, you can create a
`tools` subdir at the root of that top-level directory and place your
scripts there.

For example, if you wanted to add a script for use with tests in the
`notifications` directory, create the `notifications/tools` subdir and
put your script there.

Test Review
===========

We can sometimes take a little while to go through pull requests
because we have to go through all the tests and ensure that they match
the specification correctly. But we look at all of them, and take
everything that we can.

Getting Involved
================

If you wish to contribute actively, you're very welcome to join the
public-test-infra@w3.org mailing list (low traffic) by
[signing up to our mailing list](mailto:public-test-infra-request@w3.org?subject=subscribe).
The mailing list is [archived][mailarchive].

Join us on irc #testing ([irc.w3.org][ircw3org], port 6665). The channel
is [archived][ircarchive].

[contributing]: https://github.com/w3c/web-platform-tests/blob/master/CONTRIBUTING.md
[ircw3org]: https://www.w3.org/wiki/IRC
[ircarchive]: http://krijnhoetmer.nl/irc-logs/testing/
[mailarchive]: http://lists.w3.org/Archives/Public/public-test-infra/

Documentation
=============

* [How to write and review tests](http://testthewebforward.org/docs/)
* [Documentation for the wptserve server](http://wptserve.readthedocs.org/en/latest/)
