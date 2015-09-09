# Local Font Access Explained

# What's This All About?

This document looks into how a web developer would use the local font access API, which gives access to the fonts installed locally on a user's device. In a nutshell, fonts are composed of glyphs and the spacing around them. The web platform currently does not allow a developer to gain access to these lower level descriptions of fonts. This functionality elevates the web platform's OS integration, enabling the development of designer web apps.

# To Get the Local Font List

```js
var fontListPromise = FontAccess.getFonts();
fontListPromise.then(function(fontDescriptionArray) {
    // success
    fontDescriptionArray.forEach(function(fontDescription) {
        console.log("This font is " + fontDescription.family +
                    " with a style of " + fontDescription.style);
    });
}, function() {
    // failure
});
```

In this example, `FontAccess.getFonts()` returns a promise. When the promise is fulfilled, it will
have a list of fonts installed on the system as a FontDescription. A FontDescription
contains both the font face (`fontDescription.family`) and the font style (`fontDescription.style`). For example, a FontDescription may consist of
"Arial" family and "Bold Italic" style and would refer to the Arial font that is both Bold and Italic.
Designers often do not want programmatic bold and italic versions of a font, but instead want the local font's
specific implementation. Thus, we need to give a list of all font faces and their defined styles.

# To Get a Font Blob

```js
var fontBlobPromise = FontAccess.getFontBlob("Arial", "Regular");
fontBlobPromise.then(function(file) {
    var reader = new FileReader();
    reader.onload = function(e) {
        // Do something with the reader.result here!
    };
    reader.readAsArrayBuffer(file);
}, function() {
    // failure
});
```

Once we know which font we want, we can ask for the blob associated with the face and style of the font we want to use. `FontAccess.getFontBlob` also returns a promise. When resolved, this promise will provide a File object, as per the HTML5 specification. We can read the File object using the FileReader. Several solutions for handling the font blob exist today for web. We can use a JS library like  [FontKit](https://github.com/devongovett/fontkit) or [OpenType.js](https://github.com/nodebox/opentype.js) or C libraries (such as FreeType2) through Emscripten to inspect and rasterize the glyphs contained in the font.

# Notes

* A permission must be granted for this feature to work. This is to ensure that the local system fonts are not exposed without the user's consent.
