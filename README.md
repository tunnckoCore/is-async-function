# is-async-function [![npm version][npmv-img]][npmv-url] [![mit license][license-img]][license-url] [![NPM monthly downloads](https://img.shields.io/npm/dm/is-async-function.svg?style=flat)](https://npmjs.org/package/is-async-function) [![npm total downloads][downloads-img]][downloads-url]

> Is function really asynchronous function? Trying to guess that based on check if [common-callback-names][] exists as function arguments names or you can pass your custom.

[![code style][standard-img]][standard-url]
[![linux build][travis-img]][travis-url]
[![code coverage][coverage-img]][coverage-url]
[![dependency status][david-img]][david-url]
[![paypal donate][paypalme-img]][paypalme-url]
[![Buy me a Kofi][kofi-img]][kofi-url]

You might also be interested in [always-done](https://github.com/hybridables/always-done#readme).

## Table of Contents
- [Install](#install)
- [Usage](#usage)
- [API](#api)
  * [isAsyncFunction](#isasyncfunction)
- [Related](#related)
- [Contributing](#contributing)
- [Building docs](#building-docs)
- [Running tests](#running-tests)
- [Author](#author)
- [License](#license)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_

## Install
Install with [npm](https://www.npmjs.com/)

```
$ npm install is-async-function --save
```

or install using [yarn](https://yarnpkg.com)

```
$ yarn add is-async-function
```

## Usage
> For more use-cases see the [tests](test.js)

```js
const isAsyncFunction = require('is-async-function')
```

_**Important Note:** It may fail, if the given function is using "default params" like `options = { foo: 1 }`.
That limitation comes currently from the [function-arguments][] package. It may or may not be fixed there in future._

For more advanced stuff, try [parse-function][] which has couple of different versions and support for
the mentioned problem and dozen of others. It uses real parser to parse given function and returns
very useful information. PRs replacing the current logic with `parse-function` are not acceptable -
this is that way intentionally. And was for old times of Node.js!

## API

### [isAsyncFunction](index.js#L53)
> Trying to guess is `fn` asynchronous function or not. But not [is-callback-function][] be aware of that diff.

**Params**

* `fn` **{Function}**: is this `fn` a callback function    
* `names` **{Array}**: arguments names, default are [common-callback-names][]    
* `strict` **{Boolean}**: defaults to `true` to always return a boolean, pass `false` to get index (position) - this is useful when you wanna understand which "callback name" exists as argument in that `fn`    
* `returns` **{Boolean|Number}**: always boolean `true` or `false` when on strict mode, othewise it can be Number index representing the position and if index is 0 it is transformed to boolean `true` - so always positive value if function is async.  

**Example**

```js
var fs = require('fs')
var isAsyncFn = require('is-async-function')

console.log(isAsyncFunction(fs.readFile)) // => true
console.log(isAsyncFunction(fs.stat)) // => true

console.log(isAsyncFunction(fs.readFileSync)) // => false
console.log(isAsyncFunction(fs.statSync)) // => false

// or pass custom names to recognize as `async`
console.log(isAsyncFunction(fs.stat, ['cb'])) // => false
console.log(isAsyncFunction(fs.readFile, ['foo', 'bar']))
// => false, because fs.readFile uses `cb`
```

**non-strict mode**

> passing `false` as second or third argument

```js
var isAsyncFunction = require('is-async-function')

console.log(isAsyncFunction(fs.readFile, false)) // => 2
// => 2, because it callback argument is called `cb`
// and that's the third element in `common-callback-names` array

console.log(isAsyncFunction(fs.stat, false)) // => 1
// => 1, because it callback argument is called `callback_`
// and that's the second element in `common-callback-names` array
```

**Side note:** In previous nodejs versions it was called in a few different ways - `cb_`, `callback_` and etc. That's why [common-callback-names][] exists. As in v7 it seems everything now is called `callback`. So in most of the cases you will get boolean `true` always - both in strict and non-strict mode. In non-strict mode that will mean your function has argument called `callback`.

If you pass array of names as second argument, in non-strict mode you will get index of that array.

**Example**

```js
var isAsyncFn = require('is-async-function')

// you considered you callback fucntion
// to be called `qux` for some reason
function myAsyncFn (foo, bar, qux) {
  qux(null, 123)
}

console.log(isAsyncFn(myAsyncFn)) // => false
console.log(isAsyncFn(myAsyncFn, false)) // => false

console.log(isAsyncFn(myAsyncFn, ['callback', 'qux'], false)) // => 1
// you are getting "1", because `qux` is second item
// in provided `names` array.
```

## Related
- [always-done](https://www.npmjs.com/package/always-done): Handle completion and errors with elegance! Support for streams, callbacks, promises, child processes, async/await and sync functions. A drop-in replacement… [more](https://github.com/hybridables/always-done#readme) | [homepage](https://github.com/hybridables/always-done#readme "Handle completion and errors with elegance! Support for streams, callbacks, promises, child processes, async/await and sync functions. A drop-in replacement for [async-done][] - pass 100% of its tests plus more")
- [common-callback-names](https://www.npmjs.com/package/common-callback-names): List of common callback names - callback, cb, callback_, next, done. | [homepage](https://github.com/tunnckocore/common-callback-names#readme "List of common callback names - callback, cb, callback_, next, done.")
- [fn-args](https://www.npmjs.com/package/fn-args): Get the arguments of a function, arrow function, generator function, async function | [homepage](https://github.com/sindresorhus/fn-args#readme "Get the arguments of a function, arrow function, generator function, async function")
- [fn-name](https://www.npmjs.com/package/fn-name): Get the name of a named function | [homepage](https://github.com/sindresorhus/fn-name#readme "Get the name of a named function")
- [function-arguments](https://www.npmjs.com/package/function-arguments): Get arguments of a function, useful for and used in dependency injectors. Works for regular functions, generator functions and arrow… [more](https://github.com/tunnckocore/function-arguments#readme) | [homepage](https://github.com/tunnckocore/function-arguments#readme "Get arguments of a function, useful for and used in dependency injectors. Works for regular functions, generator functions and arrow functions.")
- [get-fn-name](https://www.npmjs.com/package/get-fn-name): Get function name with strictness and correctness in mind. Also works for arrow functions and getting correct name of bounded… [more](https://github.com/tunnckocore/get-fn-name#readme) | [homepage](https://github.com/tunnckocore/get-fn-name#readme "Get function name with strictness and correctness in mind. Also works for arrow functions and getting correct name of bounded functions. Powered by [fn-name][].")
- [is-callback-function](https://www.npmjs.com/package/is-callback-function): Returns true if function is a callback. Checks its name is one of [common-callback-names][] - callback, cb, cb_, callback_, next… [more](https://github.com/tunnckocore/is-callback-function#readme) | [homepage](https://github.com/tunnckocore/is-callback-function#readme "Returns true if function is a callback. Checks its name is one of [common-callback-names][] - callback, cb, cb_, callback_, next, done, they can be customized, these are default.")
- [minibase](https://www.npmjs.com/package/minibase): Minimalist alternative for Base. Build complex APIs with small units called plugins. Works well with most of the already existing… [more](https://github.com/node-minibase/minibase#readme) | [homepage](https://github.com/node-minibase/minibase#readme "Minimalist alternative for Base. Build complex APIs with small units called plugins. Works well with most of the already existing [base][] plugins.")
- [parse-function](https://www.npmjs.com/package/parse-function): Parse a function into an object using espree, acorn or babylon parsers. Extensible through Smart Plugins | [homepage](https://tunnckocore.com/opensource "Parse a function into an object using espree, acorn or babylon parsers. Extensible through Smart Plugins")
- [try-catch-core](https://www.npmjs.com/package/try-catch-core): Low-level package to handle completion and errors of sync or asynchronous functions, using [once][] and [dezalgo][] libs. Useful for and… [more](https://github.com/hybridables/try-catch-core#readme) | [homepage](https://github.com/hybridables/try-catch-core#readme "Low-level package to handle completion and errors of sync or asynchronous functions, using [once][] and [dezalgo][] libs. Useful for and used in higher-level libs such as [always-done][] to handle completion of anything.")

## Contributing
Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](https://github.com/tunnckoCore/is-async-function/issues/new).
Please read the [contributing guidelines](CONTRIBUTING.md) for advice on opening issues, pull requests, and coding standards.
If you need some help and can spent some cash, feel free to [contact me at CodeMentor.io](https://www.codementor.io/tunnckocore?utm_source=github&utm_medium=button&utm_term=tunnckocore&utm_campaign=github) too.

**In short:** If you want to contribute to that project, please follow these things

1. Please DO NOT edit [README.md](README.md), [CHANGELOG.md](CHANGELOG.md) and [.verb.md](.verb.md) files. See ["Building docs"](#building-docs) section.
2. Ensure anything is okey by installing the dependencies and run the tests. See ["Running tests"](#running-tests) section.
3. Always use `npm run commit` to commit changes instead of `git commit`, because it is interactive and user-friendly. It uses [commitizen][] behind the scenes, which follows Conventional Changelog idealogy.
4. Do NOT bump the version in package.json. For that we use `npm run release`, which is [standard-version][] and follows Conventional Changelog idealogy.

Thanks a lot! :)

## Building docs
Documentation and that readme is generated using [verb-generate-readme][], which is a [verb][] generator, so you need to install both of them and then run `verb` command like that

```
$ npm install verbose/verb#dev verb-generate-readme --global && verb
```

_Please don't edit the README directly. Any changes to the readme must be made in [.verb.md](.verb.md)._

## Running tests
Clone repository and run the following in that cloned directory

```
$ npm install && npm test
```

## Author
**Charlike Mike Reagent**
+ [GitHub Profile](https://github.com/Charlike Mike Reagent)
+ [Twitter Profile](https://twitter.com/Charlike Mike Reagent)
+ [LinkedIn Profile](https://linkedin.com/in/jonschlinkert)

+ [codementor/tunnckoCore](https://codementor.io/tunnckoCore)

## License
Copyright © 2015, 2020, [Charlike Mike Reagent](https://i.am.charlike.online). Released under the [MIT License](LICENSE).

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.8.0, on January 15, 2020._
_Project scaffolded using [charlike][] cli._

[always-done]: https://github.com/hybridables/always-done
[async-done]: https://github.com/gulpjs/async-done
[base]: https://github.com/node-base/base
[charlike]: https://github.com/tunnckoCoreLabs/charlike
[commitizen]: https://github.com/commitizen/cz-cli
[common-callback-names]: https://github.com/tunnckocore/common-callback-names
[dezalgo]: https://github.com/npm/dezalgo
[fn-name]: https://github.com/sindresorhus/fn-name
[function-arguments]: https://github.com/tunnckocore/function-arguments
[is-callback-function]: https://github.com/tunnckocore/is-callback-function
[once]: https://github.com/isaacs/once
[parse-function]: https://tunnckocore.com/opensource
[standard-version]: https://github.com/conventional-changelog/standard-version
[verb-generate-readme]: https://github.com/verbose/verb-generate-readme
[verb]: https://github.com/verbose/verb

[license-url]: https://www.npmjs.com/package/is-async-function
[license-img]: https://badgen.net/npm/license/is-async-function

[downloads-url]: https://www.npmjs.com/package/is-async-function
[downloads-img]: https://badgen.net/npm/dt/is-async-function

[travis-url]: https://travis-ci.org/tunnckoCore/is-async-function
[travis-img]: https://badgen.net/travis/tunnckoCore/is-async-function/master?label=build&icon=travis

[coverage-url]: https://codecov.io/gh/tunnckoCore/is-async-function
[coverage-img]: https://badgen.net/codecov/c/github/tunnckoCore/is-async-function/master

[david-url]: https://david-dm.org/tunnckoCore/is-async-function
[david-img]: https://badgen.net/david/dep/tunnckoCore/is-async-function

[standard-url]: https://github.com/feross/standard
[standard-img]: https://badgen.net/badge/code%20style/standard/green

[paypalme-url]: https://www.paypal.me/tunnckoCore
[paypalme-img]: https://badgen.net/badge/paypal/donate/green

[kofi-url]: https://ko-fi.com/tunnckoCore
[kofi-img]: https://badgen.net/badge/Buy%20me/a%20coffee/29abe0c2?icon=https://rawcdn.githack.com/tunnckoCore/badgen-icons/f8264c6414e0bec449dd86f2241d50a9b89a1203/icons/kofi.svg

[npmv-url]: https://www.npmjs.com/package/is-async-function
[npmv-img]: https://badgen.net/npm/v/is-async-function?icon=npm

