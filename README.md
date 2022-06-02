# Illustrating an issue with vscode-linter-xo and `.eslintignore`

This repo illustrates an issue where adding an `.eslintignore` file results in the `vscode-linter-xo` extension not correctly using the babel eslint parser and throwing parsing errors such as:
> Parsing error: the keyword 'import' is reserved

This is true despite including `engines` in `package.json` (see values below), whether using es6 imports or commonjs requires (with `unicorn/prefer-modules` turned off in `.xo-config.js`) or even without any modules at all. 

## Tested on

**xojs/xo** v0.49.0 and v0.42.0
	- (latest at time of testing and version in the project I originally noticed this in, respectively)

**engines** (in package.json)
```
"node": ">=14.0.0 <15.0.0",
"npm": ">=8.3.0 <9.0.0"
```

**VSCode:** v1.67.2
```
Version: 1.67.2
Commit: c3511e6c69bb39013c4a4b7b9566ec1ca73fc4d5
Date: 2022-05-17T18:20:57.384Z
Electron: 17.4.1
Chromium: 98.0.4758.141
Node.js: 16.13.0
V8: 9.8.177.13-electron.0
OS: Darwin x64 21.5.0
```

**samverschueren.linter-xo:** v3.9.0

**Node & NPM:**
```
➜ node -v
v14.19.1

➜ npm -v
8.7.0
```

**macOS:** Monterey 12.4

**MacBook Pro:** (16-inch 2019)

## Setup

1. Clone this repo and `npm i`.

2. Observe:
	- VSCode: parsing errors in `ignoreme/ignored.js` (as expected)
	- VSCode: no errors in `lintme.js` or `src/lintme-too.js` (as expected)
	- `npm test`: Parsing error in `ignoreme/ignored.js` as expected
```
➜ npm test

> xo-eslintignore-test@1.0.0 test
> xo


  ignoreme/ignored.js:1:14
  ✖  1:14  Parsing error: Unexpected token thiswillfail

  1 error
```

3. Switch to the `xoconfig` branch which has an `.xo-config.js` file [configured to ignore](https://github.com/xojs/xo/tree/v0.49.0#ignores) the `ignoreme` directory, close the VSCode window for this project, and then reopen this project in VSCode. Observe:
	- VSCode: parsing errors in `ignoreme/ignored.js` (as expected)
	- VSCode: no errors in `lintme.js` or `src/lintme-too.js` (as expected)
	- `npm test`: `ignoreme` is ignored, no errors (as expected)

4. Check out the `eslintignore` branch which contains a `.eslintignore` file, close and reopen VSCode window for this project. Observe:
	- VSCode: parsing errors in `ignoreme/ignored.js` (as expected)
	- VSCode: **unexpected parsing errors** on the first token of both `lintme.js` and `src/lintme-too.js`
	- `npm test`: `ignoreme` is ignored, no errors (as expected)

5. Check out the `eslintconfig` branch which contains a `.eslintrc.js` file with in addition to the `.eslintignore` file, and close and reopen VSCode. Observe:
	- VSCode: parsing errors in `ignoreme/ignored.js` (as expected)
	- VSCode: no errors in `lintme.js` or `src/lintme-too.js` (as expected)
	- `npm test`: `ignoreme` is ignored, no errors (as expected)

