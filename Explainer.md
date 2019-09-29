Explainer
--

**Problem**

A problem that cannot current be solved without using a native application, or if preferred, a shell script.

**Solution**

Select a local directory to store and/or write and modify shell scripts to be executed, including using parameters passed from JavaScript contexts, whether `Window`, `Worker`, or `Worklet`, etc. scopes.

Observe the local directory for changes, for example, using `inotifywait` or similar directory and file change observing program to monitor (potentially recursively), which ultimately can be incorporated into source code shipped by the implementer.

Built on top of Native File System the user provides a name to the shell script (which can already exist in the directory where permission to read/write is requested). The user writes or modifies the shell script where an event is dispatched for each read/write. The written file is executed. When the supplied output file name is written the directory a `Promise` is resolved with value set to the output file name. Native File System can then be used to get the output file.

This solution avoids using an extension or app, a local server, or WASM, to execute and get output of native applications.

The basic algorithm

Context: `Window`, `WorkerGlobalScope`, `WorkletGlobalScope`

1. `let outputFileName = "file.ext", scriptName = "doStuff"`
2. `let nativeScripts = await self.requestNativeScripts(<Map> [[<${scriptName}>, "/path/to/local/directory/sciptName"]])`
3. `let writeScript = await nativeScripts.get(<${scriptName}>).write("#!/bin/bash ... doStuff(<${outputFileName}>)")`
4. `await writeScript.execute() // when outputFileName is written resolve Promise`
5. `let dir = await self.chooseFileSystemEntries({type: "openDirectory"})`
6. `let result = await dir.getFile(<${outputFileName}>) // get output of executed native application`

**Related**

- [<script type="shell"> to execute arbitrary shell commands, and import stdout or result written to local file as a JavaScript module](https://github.com/whatwg/html/issues/3443)
- https://github.com/WICG/native-file-system/issues/72
