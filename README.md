# postcss-px-to-viewport

> This a fork of [evrone/postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport)

A plugin for [PostCSS](https://github.com/ai/postcss) that generates viewport units (vw, vh, vmin, vmax) from pixel units.

## Usage

If your project involves a fixed width, this script will help to convert pixels into viewport units. Support third library UI.

### Input/Output

```css
// input

.class {
  margin: -10px .5vh;
  padding: 5vmin 9.5px 1px;
  border: 3px solid black;
  border-bottom-width: 1px;
  font-size: 14px;
  line-height: 20px;
}

.class2 {
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 20PX;
  line-height: 30px;
}

@media (min-width: 750px) {
  .class3 {
    font-size: 16px;
    line-height: 22px;
  }
}

// output

.class {
  margin: -3.125vw .5vh;
  padding: 5vmin 2.96875vw 1px;
  border: 0.9375vw solid black;
  border-bottom-width: 1px;
  font-size: 14px;
  line-height: 6.25vw;
}

.class2 {
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 20PX;
  line-height: 9.375vw;
}

@media (min-width: 234.375vw) {
  .class3 {
    font-size: 16px;
    line-height: 6.875vw;
  }
}
```

### Example

```js
'use strict';

var fs = require('fs');
var postcss = require('postcss');
var pxToViewport = require('..');
var css = fs.readFileSync('main.css', 'utf8');
var options = {
    replace: false
};
var processedCss = postcss(pxToViewport(options)).process(css).css;

fs.writeFile('main-viewport.css', processedCss, function (err) {
  if (err) {
    throw err;
  }
  console.log('File with viewport units written.');
});
```

### Options

Default:
```js
{
  unitToConvert: 'px',
  viewportWidth: 750,
  viewportHeight: 1366, // not now used; TODO: need for different units and math for different properties
  unitPrecision: 3,
  viewportUnit: 'vw',
  propertyBlacklist: ['font'],
  selectorBlackList: [],
  minPixelValue: 1,
  exclude: [],
  LibraryUI: [],
  LibraryRate: 2,
  libraryRoot: 'node_modules',
  mediaQuery: false
}
```
- `unitToConvert` (String) unit to convert, by default, it is px.
- `viewportWidth` (Number) The width of the viewport.
- `viewportHeight` (Number) The height of the viewport.
- `unitPrecision` (Number) The decimal numbers to allow the REM units to grow to.
- `viewportUnit` (String) Expected units.
- `propertyBlacklist` (Array) The selectors to ignore and leave as px.
- `selectorBlackList` (Array) The selectors to ignore and leave as px.
    - If value is string, it checks to see if selector contains the string.
        - `['body']` will match `.body-class`
    - If value is regexp, it checks to see if the selector matches the regexp.
        - `[/^body$/]` will match `body` but not `.body`
- `minPixelValue` (Number) Set the minimum pixel value to replace.
- `exclude` (Array) just like selectorBlackList.
- `LibraryUI` (Array) example vux,miui. if style in LibraryUI, result = (2 * (x)px/viewportWidth) viewportUnit
- `LibraryRate` (Number) vendor UI scale rate, example vux,miui LibraryRate is 2.
- `libraryRoot` (String) example node_modules.
- `mediaQuery` (Boolean) Allow px to be converted in media queries.

### Use with gulp-postcss

```js
var gulp = require('gulp');
var postcss = require('gulp-postcss');
var pxtoviewport = require('postcss-px-to-viewport');

gulp.task('css', function () {

    var processors = [
        pxtoviewport({
            viewportWidth: 320,
            viewportUnit: 'vmin'
        })
    ];

    return gulp.src(['build/css/**/*.css'])
        .pipe(postcss(processors))
        .pipe(gulp.dest('build/css'));
});
```

### Use with webapck

```js
  // webpack loader
  loader: 'postcss-loader'
  // .postcssrc.js
  "postcss-px-to-viewport-lagou": {
      viewportWidth: 750,   // 视窗的宽度，对应的是我们设计稿的宽度，一般是750
      viewportHeight: 1334, // 视窗的高度，根据750设备的宽度来指定，一般指定1334，也可以不配置
      unitPrecision: 3,     // 指定`px`转换为视窗单位值的小数位数
      viewportUnit: "rem",   //指定需要转换成的视窗单位，建议使用vw
      propertyBlacklist: ['border'],// 指定不转换为视窗单位的属性，可以自定义，可以无限添加,建议定义一至两个通用的类名
      // selectorBlackList: ['.ignore', 'weui'],// 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名
      selectorBlackList: [],// 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名
      LibraryUI: ['vux'],// 使用的第三方ui库
      libraryRoot: 'node_modules',// 使用的第三方ui库，目录
      minPixelValue: 1,     // 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值
      mediaQuery: false     // 允许在媒体查询中转换`px`
    },
```
> if you write px(unitToConvert default) to PX, this plugin will not convert pixels into viewport units.
