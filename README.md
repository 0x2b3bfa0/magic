# Web Assembly port of [libmagic](https://darwinsys.com/file/) as used in [file(1)](https://en.wikipedia.org/wiki/File_(command))) command


The file command uses sophisticated methods to identify file types from their name and contents. This library brings that powerful capability to javascript projects in an efficient and integrated way.

* * *

The projects directly uses the native C implementation of libmagic, transpiled to Web Assembly using emscripten. The result is a close to native performance for file type identification, portable across any platform that has node.js.

### List of features

*   Get a descriptive string of the file contents
*   Get the mime type and mime encoding of a file
*   Optimized loading of the magic file for multiple calls to the API or uses from multiple threads (e.g. WebWorker)

NOTE: file system access is required and therefore the module is only useful in node.js environment. There is possibility though to enhance it to provide content identification using buffers and with the magic file preloaded in emscripten virtual filesystem (not implemented for now).

### Using the raw binding

```javascript

const fs = require("fs");
const Module = require("../dist/magic-js");
const instance = Module({
  // Wait for the WASM to be compiled and ready
  onRuntimeInitialized() {
    // Initialize the binding
    if (
      -1 ===
      instance.FileMagic.init(
        "/Users/abdessattar/Projects/maestro-magic/dist/magic.mgc",
        instance.MAGIC_PRESERVE_ATIME
      )
    ) {
      console.error("Initialization failed!");
      return;
    }
    
    // Get an instance of the binding and use it
    const m = new instance.FileMagic();

    const files = fs.readdirSync(".");
    console.log(`${files.length} files to check`);
    files.forEach(file => {
      console.log(
        file,
        " : ",
        m.detect(
          file,
          instance.FileMagic.flags() | instance.MAGIC_MIME
        )
      );
      console.log(file, " : ", m.detect(file, -1));
    });

    // Destroy the instance to release memory and resources when it is no longer needed.
    instance.FileMagic.destroy();
  }
});

```

### Download & Installation

```shell 
$ npm i @npcz/magic
```

```shell 
$ yarn add @npcz/magic
```

### Contributing

To build the module from source:

```shell 
$ git clone https://github.com/npcz/magic.git
$ cd magic
$ yarn build

$ node tests/raw-binding-example.js
```

The build uses docker to reduce the hassle of platform specific things when building libmagic. Setting up docker varies between platforms, refer to the official [docker documentation](https://docs.docker.com/get-started).

Pull requests, bug reports, enhancement suggestions etc... are welcome at the github repository.

### Acknowledgments

* This file command (and magic file) was originally written by Ian Darwin (who still contributes occasionally) and is now maintained by a group of developers lead by Christos Zoulas.
* Piotr Paczkowski for the emscripten [docker images](https://github.com/trzecieu/emscripten-docker).
* The [emscripten](https://emscripten.org) project.

### License

This project is licensed under the BSD-3-Clause [License](./LICENSE)
