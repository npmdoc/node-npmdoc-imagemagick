# api documentation for  [imagemagick (v0.1.3)](https://github.com/rsms/node-imagemagick#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-imagemagick.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-imagemagick) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-imagemagick.svg)](https://travis-ci.org/npmdoc/node-npmdoc-imagemagick)
#### A wrapper around the imagemagick cli

[![NPM](https://nodei.co/npm/imagemagick.png?downloads=true)](https://www.npmjs.com/package/imagemagick)

[![apidoc](https://npmdoc.github.io/node-npmdoc-imagemagick/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-imagemagick_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-imagemagick/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-imagemagick/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-imagemagick/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Rasmus Andersson",
        "email": "http://rsms.me/"
    },
    "bugs": {
        "url": "https://github.com/rsms/node-imagemagick/issues"
    },
    "dependencies": {},
    "description": "A wrapper around the imagemagick cli",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "7483cea093b4d9f2e2f396857adc8821b537c56a",
        "tarball": "https://registry.npmjs.org/imagemagick/-/imagemagick-0.1.3.tgz"
    },
    "engine": [
        "node >=0.6"
    ],
    "homepage": "https://github.com/rsms/node-imagemagick#readme",
    "licenses": [
        "MIT"
    ],
    "main": "imagemagick",
    "maintainers": [
        {
            "name": "rsms",
            "email": "rasmus@notion.se"
        }
    ],
    "name": "imagemagick",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/rsms/node-imagemagick.git"
    },
    "version": "0.1.3"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module imagemagick](#apidoc.module.imagemagick)
1.  [function <span class="apidocSignatureSpan">imagemagick.</span>convert (args, timeout, callback)](#apidoc.element.imagemagick.convert)
1.  [function <span class="apidocSignatureSpan">imagemagick.</span>crop (options, callback)](#apidoc.element.imagemagick.crop)
1.  [function <span class="apidocSignatureSpan">imagemagick.</span>identify (pathOrArgs, callback)](#apidoc.element.imagemagick.identify)
1.  [function <span class="apidocSignatureSpan">imagemagick.</span>readMetadata (path, callback)](#apidoc.element.imagemagick.readMetadata)
1.  [function <span class="apidocSignatureSpan">imagemagick.</span>resize (options, callback)](#apidoc.element.imagemagick.resize)
1.  [function <span class="apidocSignatureSpan">imagemagick.</span>resizeArgs (options)](#apidoc.element.imagemagick.resizeArgs)



# <a name="apidoc.module.imagemagick"></a>[module imagemagick](#apidoc.module.imagemagick)

#### <a name="apidoc.element.imagemagick.convert"></a>[function <span class="apidocSignatureSpan">imagemagick.</span>convert (args, timeout, callback)](#apidoc.element.imagemagick.convert)
- description and source-code
```javascript
convert = function (args, timeout, callback) {
  var procopt = {encoding: 'binary'};
  if (typeof timeout === 'function') {
    callback = timeout;
    timeout = 0;
  } else if (typeof timeout !== 'number') {
    timeout = 0;
  }
  if (timeout && (timeout = parseInt(timeout)) > 0 && !isNaN(timeout))
    procopt.timeout = timeout;
  return exec2(exports.convert.path, args, procopt, callback);
}
```
- example usage
```shell
...
### convert(args, callback(err, stdout, stderr))

Raw interface to 'convert' passing arguments in the array 'args'.

Example:

'''javascript
im.convert(['kittens.jpg', '-resize', '25x120', 'kittens-small.jpg'],
function(err, stdout){
  if (err) throw err;
  console.log('stdout:', stdout);
});
'''

### resize(options, callback(err, stdout, stderr))
...
```

#### <a name="apidoc.element.imagemagick.crop"></a>[function <span class="apidocSignatureSpan">imagemagick.</span>crop (options, callback)](#apidoc.element.imagemagick.crop)
- description and source-code
```javascript
crop = function (options, callback) {
  if (typeof options !== 'object')
    throw new TypeError('First argument must be an object');
  if (!options.srcPath && !options.srcData)
    throw new TypeError("No srcPath or data defined");
  if (!options.height && !options.width)
    throw new TypeError("No width or height defined");

  if (options.srcPath){
    var args = options.srcPath;
  } else {
    var args = {
      data: options.srcData
    };
  }

  exports.identify(args, function(err, meta) {
    if (err) return callback && callback(err);
    var t         = exports.resizeArgs(options),
        ignoreArg = false,
        printNext  = false,
        args      = [];
    t.args.forEach(function (arg) {
      if (printNext === true){
        console.log("arg", arg);
        printNext = false;
      }
      // ignoreArg is set when resize flag was found
      if (!ignoreArg && (arg != '-resize'))
        args.push(arg);
      // found resize flag! ignore the next argument
      if (arg == '-resize'){
        console.log("resize arg");
        ignoreArg = true;
        printNext = true;
      }
      if (arg === "-crop"){
        console.log("crop arg");
        printNext = true;
      }
      // found the argument after the resize flag; ignore it and set crop options
      if ((arg != "-resize") && ignoreArg) {
        var dSrc      = meta.width / meta.height,
            dDst      = t.opt.width / t.opt.height,
            resizeTo  = (dSrc < dDst) ? ''+t.opt.width+'x' : 'x'+t.opt.height,
            dGravity  = options.gravity ? options.gravity : "Center";
        args = args.concat([
          '-resize', resizeTo,
          '-gravity', dGravity,
          '-crop', ''+t.opt.width + 'x' + t.opt.height + '+0+0',
          '+repage'
        ]);
        ignoreArg = false;
      }
    })

    t.args = args;
    resizeCall(t, callback);
  })
}
```
- example usage
```shell
...

### crop(options, callback) ###
Convenience function for resizing and cropping an image. _crop_ uses the resize method, so _options_ and _callback_ are the same
. _crop_ uses _options.srcPath_, so make sure you set it :) Using only _options.width_ or _options.height_ will create a square
dimensioned image.  Gravity can also be specified, it defaults to Center.   Available gravity options are [NorthWest, North, NorthEast
, West, Center, East, SouthWest, South, SouthEast]

Example:

'''javascript
im.crop({
  srcPath: path,
  dstPath: 'cropped.jpg',
  width: 800,
  height: 600,
  quality: 1,
  gravity: "North"
}, function(err, stdout, stderr){
...
```

#### <a name="apidoc.element.imagemagick.identify"></a>[function <span class="apidocSignatureSpan">imagemagick.</span>identify (pathOrArgs, callback)](#apidoc.element.imagemagick.identify)
- description and source-code
```javascript
identify = function (pathOrArgs, callback) {
  var isCustom = Array.isArray(pathOrArgs),
      isData,
      args = isCustom ? ([]).concat(pathOrArgs) : ['-verbose', pathOrArgs];

  if (typeof args[args.length-1] === 'object') {
    isData = true;
    pathOrArgs = args[args.length-1];
    args[args.length-1] = '-';
    if (!pathOrArgs.data)
      throw new Error('first argument is missing the "data" member');
  } else if (typeof pathOrArgs === 'function') {
    args[args.length-1] = '-';
    callback = pathOrArgs;
  }
  var proc = exec2(exports.identify.path, args, {timeout:120000}, function(err, stdout, stderr) {
    var result, geometry;
    if (!err) {
      if (isCustom) {
        result = stdout;
      } else {
        result = parseIdentify(stdout);
        geometry = result['geometry'].split(/x/);

        result.format = result.format.match(/\S*/)[0]
        result.width = parseInt(geometry[0]);
        result.height = parseInt(geometry[1]);
        result.depth = parseInt(result.depth);
        if (result.quality !== undefined) result.quality = parseInt(result.quality) / 100;
      }
    }
    callback(err, result);
  });
  if (isData) {
    if ('string' === typeof pathOrArgs.data) {
      proc.stdin.setEncoding('binary');
      proc.stdin.write(pathOrArgs.data, 'binary');
      proc.stdin.end();
    } else {
      proc.stdin.end(pathOrArgs.data);
    }
  }
  return proc;
}
```
- example usage
```shell
...
### identify(path, callback(err, features))

Identify file at 'path' and return an object 'features'.

Example:

'''javascript
im.identify('kittens.jpg', function(err, features){
  if (err) throw err;
  console.log(features);
  // { format: 'JPEG', width: 3904, height: 2622, depth: 8 }
});
'''

### identify(args, callback(err, output))
...
```

#### <a name="apidoc.element.imagemagick.readMetadata"></a>[function <span class="apidocSignatureSpan">imagemagick.</span>readMetadata (path, callback)](#apidoc.element.imagemagick.readMetadata)
- description and source-code
```javascript
readMetadata = function (path, callback) {
  return exports.identify(['-format', '%[EXIF:*]', path], function(err, stdout) {
    var meta = {};
    if (!err) {
      stdout.split(/\n/).forEach(function(line){
        var eq_p = line.indexOf('=');
        if (eq_p === -1) return;
        var key = line.substr(0, eq_p).replace('/','-'),
            value = line.substr(eq_p+1).trim(),
            typekey = 'default';
        var p = key.indexOf(':');
        if (p !== -1) {
          typekey = key.substr(0, p);
          key = key.substr(p+1);
          if (typekey === 'exif') {
            key = exifKeyName(key);
            var converter = exifFieldConverters[key];
            if (converter) value = converter(value);
          }
        }
        if (!(typekey in meta)) meta[typekey] = {key:value};
        else meta[typekey][key] = value;
      })
    }
    callback(err, meta);
  });
}
```
- example usage
```shell
...

Requires imagemagick CLI tools to be installed. There are numerous ways to install them. For instance, if you're on OS X you can
 use [Homebrew](http://mxcl.github.com/homebrew/): 'brew install imagemagick'.

## Example

'''javascript
var im = require('imagemagick');
im.readMetadata('kittens.jpg', function(err, metadata){
  if (err) throw err;
  console.log('Shot at '+metadata.exif.dateTimeOriginal);
})
// -> Shot at Tue, 06 Feb 2007 21:13:54 GMT
'''

## API
...
```

#### <a name="apidoc.element.imagemagick.resize"></a>[function <span class="apidocSignatureSpan">imagemagick.</span>resize (options, callback)](#apidoc.element.imagemagick.resize)
- description and source-code
```javascript
resize = function (options, callback) {
  var t = exports.resizeArgs(options);
  return resizeCall(t, callback)
}
```
- example usage
```shell
...
'''

srcPath, dstPath and (at least one of) width and height are required. The rest is optional.

Example:

'''javascript
im.resize({
  srcPath: 'kittens.jpg',
  dstPath: 'kittens-small.jpg',
  width:   256
}, function(err, stdout, stderr){
  if (err) throw err;
  console.log('resized kittens.jpg to fit within 256x256px');
});
...
```

#### <a name="apidoc.element.imagemagick.resizeArgs"></a>[function <span class="apidocSignatureSpan">imagemagick.</span>resizeArgs (options)](#apidoc.element.imagemagick.resizeArgs)
- description and source-code
```javascript
resizeArgs = function (options) {
  var opt = {
    srcPath: null,
    srcData: null,
    srcFormat: null,
    dstPath: null,
    quality: 0.8,
    format: 'jpg',
    progressive: false,
    colorspace: null,
    width: 0,
    height: 0,
    strip: true,
    filter: 'Lagrange',
    sharpening: 0.2,
    customArgs: [],
    timeout: 0
  }

  // check options
  if (typeof options !== 'object')
    throw new Error('first argument must be an object');
  for (var k in opt) if (k in options) opt[k] = options[k];
  if (!opt.srcPath && !opt.srcData)
    throw new Error('both srcPath and srcData are empty');

  // normalize options
  if (!opt.format) opt.format = 'jpg';
  if (!opt.srcPath) {
    opt.srcPath = (opt.srcFormat ? opt.srcFormat +':-' : '-'); // stdin
  }
  if (!opt.dstPath)
    opt.dstPath = (opt.format ? opt.format+':-' : '-'); // stdout
  if (opt.width === 0 && opt.height === 0)
    throw new Error('both width and height can not be 0 (zero)');

  // build args
  var args = [opt.srcPath];
  if (opt.sharpening > 0) {
    args = args.concat([
      '-set', 'option:filter:blur', String(1.0-opt.sharpening)]);
  }
  if (opt.filter) {
    args.push('-filter');
    args.push(opt.filter);
  }
  if (opt.strip) {
    args.push('-strip');
  }
  if (opt.width || opt.height) {
    args.push('-resize');
    if (opt.height === 0) args.push(String(opt.width));
    else if (opt.width === 0) args.push('x'+String(opt.height));
    else args.push(String(opt.width)+'x'+String(opt.height));
  }
  opt.format = opt.format.toLowerCase();
  var isJPEG = (opt.format === 'jpg' || opt.format === 'jpeg');
  if (isJPEG && opt.progressive) {
    args.push('-interlace');
    args.push('plane');
  }
  if (isJPEG || opt.format === 'png') {
    args.push('-quality');
    args.push(Math.round(opt.quality * 100.0).toString());
  }
  else if (opt.format === 'miff' || opt.format === 'mif') {
    args.push('-quality');
    args.push(Math.round(opt.quality * 9.0).toString());
  }
  if (opt.colorspace) {
    args.push('-colorspace');
    args.push(opt.colorspace);
  }
  if (Array.isArray(opt.customArgs) && opt.customArgs.length)
    args = args.concat(opt.customArgs);
  args.push(opt.dstPath);

  return {opt:opt, args:args};
}
```
- example usage
```shell
...
    proc.stdin.end(t.opt.srcData);
  }
}
return proc;
}

exports.resize = function(options, callback) {
var t = exports.resizeArgs(options);
return resizeCall(t, callback)
}

exports.crop = function (options, callback) {
if (typeof options !== 'object')
  throw new TypeError('First argument must be an object');
if (!options.srcPath && !options.srcData)
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
