# Base 32 encoding/decoding for JavaScript ![example workflow](https://github.com/agnoster/base32-js/actions/workflows/main.yml/badge.svg)

Base 32 is between hexadecimal notation and Base 64 encoding. It's intended to be a **human-friendly** -- you don't have to worry about punctuation, capitalization, or letters/numbers that are easy to confuse, making it easier to transmit in handwriting or over the phone.

One of the primary purposes is to have aesthetically pleasing SHA1 hashes. Compare:

 - Hex: `17057684bea1f9331418b633a8f373119d765fd4`
 - B64: `xE_ptB5SeclHm8JEsD0-ST1mTBM`
 - B32: `2w2qd15ym7wk650rprtuh4vk26eqcqym`

Try giving out the Base 64 hash over the phone! "lowercase 'x', capital 'E', underscore, lowercase 'p', ..." Base 32 will work the same with upper- or lowercase, you can mistake a number for a similar-looking letter, and it will *still* decode to the same data.

## Getting started

In your shell, install with npm:

```sh
npm install base32
```

In your code:

```javascript
var base32 = require('base32')

// simple api

var encoded = base32.encode('some data to encode')
var decoded = base32.decode(encoded)

// streaming api
this.encoder = new Base32.encoder()
this.dataCallback = function(chunk) {
    this.emit(this.encoder(chunk))
}
this.closeCallback = function(chunk) {
    this.emit(this.finish()) // flush any remaining bits
}

// easy sha1 hash
var hash = base32.sha1(some_data_to_hash) // DONE.
```

On the command-line (to install system-wide, use `npm install -g base32`):

```sh
base32 -h
#> Usage: base32 [input_file] [-o output_file] [-d|--decode] [-s|--sha]
echo "Hello World" | base32
#> 91jprv3f41bpywkccg50
echo 'axqqeb10d5u20wk5c5p6ry90exqq4uvk44' | base32 -d
#> Wow, it really works!
base32 -s test/*
#> ky2t1raumjn9cghne773petngx3zz3q7  test/base32-test.coffee
#> 6b4bkjaveddmg5jh7hnyw132yht20g6e  test/compare.coffee
```

## Warning: this is *a* Base 32 implementation, not *the* Base 32 implementation

There are about (128 choose 32) different specifications of something called "Base 32" - see [Wikipedia](http://en.wikipedia.org/wiki/Base_32) for some of them.

This is a _modified_ [Crockford encoding](https://www.crockford.com/base32.html) that should be simple, less error-prone, and streamable (for [Node](http://nodejs.org)).

## Minispec

- The *encoding* alphabet consists of the numerals 0-9 and the letters a-z, excluding a few letters that might look like numbers, which we simply interpret as follows:

  - I -> 1
  - L -> 1
  - O -> 0
  - S -> 5

- When *decoding*, capital letters are converted to lowercase and the "ambiguous" letters mentioned above converted to their numeric counterparts.
- Each character corresponds to 5 bits of input, tail-padded with 0s where necessary.
- Lexicographic order of strings is preserved through Base 32 encoding.

### `decimal -> base32` Table

| 00xxx | 01xxx | 10xxx | 11xxx |
| --- | --- | --- | --- |
| 0 -> 0 | 8 -> 8 | 16 -> g | 24 -> r |
| 1 -> 1 | 9 -> 9 | 17 -> h | 25 -> t |
| 2 -> 2 | 10 -> a | 18 -> j | 26 -> u  |
| 3 -> 3 | 11 -> b | 19 -> k | 27 -> v |
| 4 -> 4 | 12 -> c | 20 -> m | 28 -> w |
| 5 -> 5 | 13 -> d | 21 -> n | 29 -> x |
| 6 -> 6 | 14 -> e | 22 -> p | 30 -> y |
| 7 -> 7 | 15 -> f | 23 -> q | 31 -> z |

## Formalia

Under MIT License.

Fork as much as you like, I'm more than amenable to pull requests. I'm trying to keep it reasonably node-ish, so bear that in mind.
