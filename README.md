node-wkhtmltopdf fork with multi-URL/GNU timeout support
================

A Node.js wrapper for the [wkhtmltopdf](http://wkhtmltopdf.org/) command line tool.  As the name implies, 
it converts HTML documents to PDFs using WebKit.

**This fork adds support for multi-URL input as well as the ability to specify a timeout on the wkhtmltopdf process.**

In order to utilize the timeout option the GNU timeout binary must be present on your system and in your PATH.

## Installation

First, you need to install the `wkhtmltopdf` command line tool on your system.

The **easiest way** to do this is to
[download](http://wkhtmltopdf.org/downloads.html#stable) a prebuilt version for your system.  **DO NOT** try to use
the packages provided by your distribution as they may not be using a patched Qt and have missing features.

This package is not yet available as an npm module. You must reference it directly in your package.json file.

`  "dependencies": {
    "wkhtmltopdf": "git+https://github.com/zsiglin/node-wkhtmltopdf.git"
  }
`
    
Be sure the `wkhtmltopdf` command line tool is in your PATH when you're done installing.  If you don't want to do this for some reason, you can change
the `require('wkhtmltopdf').command` property to the path to the `wkhtmltopdf` command line tool.

## Usage

### wkhtmltopdf(source, [options], [callback]);

```javascript
var wkhtmltopdf = require('wkhtmltopdf');

// URL
wkhtmltopdf('http://google.com/', { pageSize: 'letter' })
  .pipe(fs.createWriteStream('out.pdf'));
  
// Multiple URLs
wkhtmltopdf(['http://google.com/', 'http://github.com/'], { pageSize: 'letter' })
  .pipe(fs.createWriteStream('out.pdf'));
  
// Kill the wkhtmltopdf process after 20 seconds to prevent hanging
wkhtmltopdf('http://www.google.com', { timeout: 20 })
  .pipe(fs.createWriteStream('out.pdf'));
  
// HTML
wkhtmltopdf('<h1>Test</h1><p>Hello world</p>')
  .pipe(res);

// Stream input and output
var stream = wkhtmltopdf(fs.createReadStream('file.html'));

// output to a file directly
wkhtmltopdf('http://apple.com/', { output: 'out.pdf' });

// Optional callback
wkhtmltopdf('http://google.com/', { pageSize: 'letter' }, function (err, stream) {
  // do whatever with the stream
});

// Repeatable options
wkhtmltopdf('http://google.com/', {
  allow : ['path1', 'path2'],
  customHeader : [
    ['name1', 'value1'],
    ['name2', 'value2']
  ]
});

// Ignore warning strings
wkhtmltopdf('http://apple.com/', { 
  output: 'out.pdf',
  ignore: ['QFont::setPixelSize: Pixel size <= 0 (0)']
});
// RegExp also acceptable
wkhtmltopdf('http://apple.com/', { 
  output: 'out.pdf',
  ignore: [/QFont::setPixelSize/]
});
```

`wkhtmltopdf` is just a function, which you call with either a URL or an inline HTML string, and it returns
a stream that you can read from or pipe to wherever you like (e.g. a file, or an HTTP response).

## Options

There are [many options](http://wkhtmltopdf.org/docs.html) available to
wkhtmltopdf.  All of the command line options are supported as documented on the page linked to above.  The
options are camelCased instead-of-dashed as in the command line tool. Note that options that do not have values, must be specified as a boolean, e.g. **debugJavascript: true**

There is also an `output` option that can be used to write the output directly to a filename, instead of returning
a stream.

### Debug Options

Apart from the **debugJavascript** option from wkhtmltopdf, there is an additional options **debug** and **debugStdOut** which will help you debug rendering issues, by outputting data to the console. **debug** prints and **stderr** messages while **debugStdOut** prints any **stdout** warning messages.

## Tests

Run `npm test` and manually check that generated files are like the expected files. The test suit prints the paths of the files that needs to be compared.

**TODO** - Find a way to automatically compare the PDF files.

## License

MIT
