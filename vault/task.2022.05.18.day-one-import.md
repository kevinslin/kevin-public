---
id: k0w6tha1znhk0x6cm023n7p
title: Day One Import
desc: ''
updated: 1653786972613
created: 1652904234841
status: 'x'
---


## Tasks
- [[Lookup|dendron://kevin-private/task.2022.05.18.day-one-import.task.lookup]]
- [x] basic trial run
- [x] format daily journal
- [x] import images

## Reference
- example JSON of an entry: [[Entry|dendron://kevin-private/task.2022.05.18.day-one-import.ref.entry]]

## Scripts

```sh
node src/index.js | tee /tmp/out.txt
```

## Issues

### image regex not working

```js
text = "![](dayone-moment:\/\/8F7754BB7F924A6A8E00EF28DC3B6A2F)"
```
### type error - Cannot read property 'slice'
- cause: entries with empty titles/undefined

```
npx: installed 139 in 13.978s
(node:93250) UnhandledPromiseRejectionWarning: Error: TypeError: Cannot read property 'slice' of undefined
    at /Users/kevinlin/.npm/_npx/93250/lib/node_modules/dayone-to-md/src/index.js:13:15
(Use `node --trace-warnings ...` to show where the warning was created)
(node:93250) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 1)
(node:93250) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
```
