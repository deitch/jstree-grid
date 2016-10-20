# jstreegrid tests

This directory contains the tests for jstreegrid. In order to avoid a lot of duplication,
the tests have been created in a modular fashion. There is a single master file,
`test-master.html`, which provides an interface to loading and running any tests.

All tests should exist as html partial files in `components/` named for the test, e.g. `my-unique-test.html`. Any `script` tags in the file will be executed. The html partial will be loaded into a `<div id="tests">`

To add a new test:

1. Create the test partial `.html` in `components/`, e.g. `my-unique-test.html`
2. Add the correct partial html and JS scripts to the `my-unique-test.html`
3. Add the test file name to the `test-list.json` array

**About ES6/CJS:** Yes, we definitely could use ES6 modules or CommonJS modules (like node),
but for the purpose of clean tests, we stick with native JS understood by the browser,
i.e. no Babel/Traceur transpiling.
