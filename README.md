###### JavaScript Browser

# Saving data as a file in browsers

Saving data as a file by a JavaScript command is dead simple in runtimes like Node and Bun, but simply not possible in browsers since there, for good reasons, things like writing to disk should only happen by controlled user dialogs.

What's possible is at least to trigger a "Save as file" dialog with the data to save.

The following is a traditional piece of code to construct a data link and simulate a click on it. Without explicitly passed data, the current HTML document is saved.


## Installation

Note that this code targets also at situations where it is not possible to import it as a module, e.g. remote debugging in closed contexts. Of course you can use it in Node / Bun for code that will be run in a browser.

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
   * @type { ( data?: string, mime_type?: string, file_name?:string ) => boolean | undefined }
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
    // ! combination of unescape + encodeURIComponent indeed essential here
    hidden_a.href = `data:${ mime_type };base64,${ btoa( unescape( encodeURIComponent( data ) ) ) }`
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
