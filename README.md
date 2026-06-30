###### JavaScript Browser

# Saving data as a file in browsers

Saving data as a file by a JavaScript command is dead simple in runtimes like Node and Bun, but simply not possible in browsers since there, for good reasons, things like writing to disk should only happen by controlled user dialogs.

What's possible is at least to trigger a "Save as file" dialog with the data to save.

The following is a traditional and easy to copy-paste to console piece of code to construct a data link and simulate a click on it. Without explicitly passed data, the current HTML document is saved.

**Note that the keyword "data" here is to be understood as textual content of reasonable size** - for broader and unavoidably complexer goals you should skip to a [more elaborate alternative](#more-elaborated-alternatives).

> Note: The same day that this short note was initially pushed to GitHub an article ["How to save a file"](#google-webdev-save-file) appeared on Google's web.dev that uses the well-known approach given here as a fallback for the currently only in Chrome / Edge / Opera (without iOS versions) available [showSaveFilePicker](#mdn-showsavefilepicker). The following is not stealing from Google.


## Installation

Note that this code is aimed to be small and simple to fit situations where it is not possible to import it as a module, e.g. remote debugging in closed contexts. Of course you can use it in Node / Bun for code that will be run in a browser.

Copy the following function `save_as_file` to your code or into a DevTools console:

```js
  /** Trigger "Save as file" for given data or current HTML document
   *  - Throws an error if used outside browser (= no document object)
   * @example
   *  - save_as_file()
   *  - save_as_file( 'demo.json', '{ "key": "val" }' )
   *  - save_as_file( 'demo.txt', 'First line\nSecond line' )
   * @param [ file_name ] - file name to suggest for file to save, defaults to title of current HTML document + '.html'
   * @param [ data ] - data to save, defaults to current HTML document
   * @param [ mime_type ] - MIME type for data to save as, defaults to trying to guess from file_name's extension, falling back to "text/html"
   * @returns true on success, undefined false
   * @see {@link https://hh-lohmann.github.io/browser-save-data-as-file/}
   * @version 1.1.1
   * @type { ( file_name?:string, data?: string, mime_type?: string ) => boolean | undefined }
   */
  const save_as_file = function( file_name, data, mime_type ) {
    if( typeof document === 'undefined' ) throw Error( 'save_as_file: Can only be run in browsers (with document object)' )
    if( ! file_name ) file_name = `${ document.title }.html`
    if( ! data ) data = '<!DOCTYPE html>\\n' + document.documentElement.innerHTML
    if( ! mime_type ) {
      let extension = file_name.split( '.' ).slice( -1 )[ 0 ]
      if( extension === 'html' ) mime_type = 'text/html'
      if( extension === 'json' ) mime_type = 'application/json'
      if( extension === 'txt' ) mime_type = 'text/plain'
    }
    let hidden_a = document.createElement( 'a' )
    hidden_a.download = file_name
    hidden_a.href = URL.createObjectURL( new Blob( [ data ], { type: mime_type } ) )
    hidden_a.click()
    return true
  }
```

## Usage

Add calls to `save_as_file` where needed into your code or into a DevTools console, e.g.

```js
  save_as_file( 'demo.json', '{ "key": "val" }' )
```

or

```js
  save_as_file(
    'buggy_header.html',
    document.querySelector( 'header' ).outerHTML
  )
```


## Demo

<!-- ! HTML demo: dev vs. release switch
  * GitHub repo view does not render HTML, so a GitHub Pages view is linked
    * NB: GitHub Pages allows to maintain a single instance of the HTML demo file in the repo
  * In dev a GitHub Pages view would require a Pages build for any change to check instead of live reloading, so JavaScript is utilized here to detect a dev environment and reroute the link to the local repo instance
    * NB: JavaScript is stripped off in GitHub repo view
    * "dev environment" is defined by using "localhost" or a numerical ID as hostname
      * NB RegEx: `.replace( /\d/g, '' ).replaceAll( '.', '' )` instead of `location.hostname.replace( /[\d\.]/g, '' )` to avoid `[]` which may mislead Markdown parsers to read it as link syntax
  * Unfortunately GitHub repo view displays "<script>" tags and their contents as literal content (for security), so the JavaScript here has to be pressed into an "onclick"
-->
See <a arial-description="Release vs. Dev switch = GitHub Pages vs. local file" href="https://hh-lohmann.github.io/browser-save-data-as-file/demo.html" onclick="if( location.hostname.replace( /\d/g, '' ).replaceAll( '.', '' ) === '' || location.hostname === 'localhost' ){ this.href='./demo.html'; alert( 'Dev environment detected - switching to local version' ); }">demo.html</a>



## More elaborated alternatives

If your goal goes beyond saving a certain kind of data in certain amounts you should check the well established [file-server / FileSaver.js](#file-saver-eligrey) or [js-file-manager](#js-file-manager-jjv360) or [Google' reference implementation "Browser-FS-Access"](#google-browser-fs-access).


## References

###### file-saver-eligrey
  * [file-server / FileSaver.js](https://www.npmjs.com/package/file-saver)

###### google-browser-fs-access
  * [Google Chrome Labs: Browser-FS-Access](https://www.npmjs.com/package/browser-fs-access)

###### google-webdev-save-file
  * Note: the exposition above was written before getting aware of this source
  * [Google web.dev: How to save a file](https://web.dev/patterns/files/save-a-file)

###### mdn-showSaveFilePicker
  * [MDN: Window: showSaveFilePicker()](https://developer.mozilla.org/en-US/docs/Web/API/Window/showSaveFilePicker)

###### js-file-manager-jjv360
  * [save-file](https://www.npmjs.com/package/js-file-manager)
