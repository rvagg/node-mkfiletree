# mkfiletree

Serialize an object to a file/directory tree. Available in npm as *mkfiletree*

**Serialize an object to a file/directory tree**

[![NPM](https://nodei.co/npm/mkfiletree.svg)](https://nodei.co/npm/mkfiletree/)

Particularly useful for making test fixtures where you need to create a non-trivial tree of files and don't want to have to mock out `fs`. **See [readfiletree](https://github.com/rvagg/node-readfiletree) for file tree deserialization.**

## API

### makeTemp(prefix, tree[, callback])

Make a directory & file tree in the system's temporary directory, under a uniquely named subdirectory prefixed with the `prefix` argument.

* If a `callback` is supplied, it will receive an `error` argument and a `dir` telling you the full path to the root directory created for you.
* If no `callback` is supplied, a `Promise` will be returned which you can `await` on for a `dir` telling you the full path to the root directory created for you.

Using both *mkfiletree* and *readfiletree* we can do the following:

```js
const mkfiletree = require('mkfiletree')
const readfiletree = require('readfiletree')

const directoryContents = {
  'adir': {
    'one.txt': '1\n2\n3\n',
    'two.txt': 'a\nb\nc\n',
    'deeper': {
      'depths.txt': 'whoa...'
    }
  },
  'afile.txt': 'file contents'
}

await mkfiletree.makeTemp('testfiles', directoryContents)
let obj = await readfiletree(dir)
console.log(obj)
```

The directory structre created above looks like the following:

```
$ find /tmp/testfiles11240-23530-r7rs3 -type f -exec sh -c "echo '\n{}: ' && cat '{}'" \;
  →  /tmp/testfiles11240-23530-r7rs3/afile.txt: 
      file contents
      /tmp/testfiles11240-23530-r7rs3/adir/deeper/depths.txt: 
      whoa...
      /tmp/testfiles11240-23530-r7rs3/adir/two.txt: 
      a
      b
      c

      /tmp/testfiles11240-23530-r7rs3/adir/one.txt: 
      1
      2
      3

```

And the output of the program should be the same as the input to *mkfiletree*:

```js
{
  'adir': {
    'one.txt': '1\n2\n3\n',
    'two.txt': 'a\nb\nc\n',
    'deeper': {
      'depths.txt': 'whoa...'
    }
  },
  'afile.txt': 'file contents'
}
```

### cleanUp([callback])

Clean up any temporary directories created with `makeTemp()` since the last `cleanUp()` call or the begining of the current process.

* If a `callback` is supplied it will be supplied with an `error` argument if there was a problem deleting the directory & file tree.
* If no `callback` is supplied, a `Promise` will be returned that you can `await` on.

### make(root, tree[, callback])

Same as `makeTemp()` but you specify the exact root path *to be created* which will contain your directory tree.

* If a `callback` is supplied, it receives the `error` and `dir` arguments.
* If no `callback` is supplied, a `Promise` will be returned which you can `await` on for a `dir`.

Directories created with `make()` won't be removed with a `cleanUp()` call.

## Contributing

Tests can be run with `npm test`. I'm more than happy to receive contributions so fork away!

## Synchronous version

No, there is no sync version, do it async, it's good for your health and contains additional vitamin C, B1, B2 and folate.

## License

**mkfiletree** is Copyright (c) 2014 Rod Vagg [@rvagg](https://twitter.com/rvagg) and licenced under the MIT licence. All rights not explicitly granted in the MIT license are reserved. See the included LICENSE.md file for more details.
