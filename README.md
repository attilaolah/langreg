langreg
=====

langreg is a lightweight Go package to handle ISO language and region (sometimes called country) codes. This only uses ISO 639-1 language codes, and ISO 3166-1 alpha-2 region codes.  These are commonly used for language and region settings.  E.g. `en_US`, `en_GB`, `zu_ZA`.

## Usage

Language codes must be lowercase, while region codes must be uppercase (blame ISO for that).

With a language code, it is possible to:
- validate the code
- look up its English name
- look up its native name(s) in its native script(s)

With a region code, it is possible to:
- validate the code
- look up the English region name

### Examples

The library is quite simple, but here are a few example use cases:

**Validate a composite (e.g. "en_US") code**
```go
code := "en_US"
if langreg.IsValidLangRegCode(code) {
	...
	// do something
}
```

**Get a language in its native script**
```go
lang := "zh"
name := langreg.LangNativeName(lang)
fmt.Println(name)
```

```Result: 中文(Zhōngwén); 汉语; 漢語```

**Get a language in English**
```go
lang := "xh"
name := langreg.LangEnglishName(lang)
fmt.Println(name)
```

```Result: Xhosa```

**Get a region name**
```go
regCode := "DZ"
region := langreg.RegionName(regCode)
fmt.Println(region)
```

```Result: Algeria```


## Data Source

All data sources that have been used are in the public domain.

The data used for the language codes and English names, come from the [official source](http://loc.gov/standards/iso639-2/ISO-639-2_utf-8.txt) at the Library of Congress.  As the official specification doesn't include native names (What were they thinking?!), I've scraped the native names from Wikipedia's [list of ISO 639-1 codes](http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

As this is intended for general use cases, rather than distinguishing between ancient and modern languages, some of the names from the official source have been simplified where I felt they would be clear.  For example "Greek, Modern (1453-)" - the official ISO name - is less clear than "Greek", and so I used "Greek".  There are a few other minor cases like this, but nothing significant.

The data used for the region codes and data has been scraped from Wikipedia's [ISO 3166-1 page](http://en.wikipedia.org/wiki/ISO_3166-1). I couldn't find any single download from ISO without paying for it or scraping a page per letter of the alphabet, so I stuck with Wikipedia - because lazy.

## Design and Benchmarks

This was designed primarily as a lightweight lookup table that would be called infrequently.  I tested various options for this, and narrowed it down to either a map or a switch statement.  The switch statement for a single lookup table was a little slower at 67-78 ns/op vs the maps' 38-50 ns/op, but had no data to load into memory first, while the startup time for loading the map into memory was 48 µs (48k ns).

The worst case benchmarks results on a MacBook Pro are:
```
BenchmarkIsValidLangRegCode		10000000	       158 ns/op

BenchmarkLangCodeInfo			20000000	        75.2 ns/op
BenchmarkIsValidLanguageCode	20000000	        76.6 ns/op
BenchmarkLangEnglishName		20000000	        77.9 ns/op
BenchmarkLangNativeName			20000000	        77.2 ns/op

BenchmarkRegionCodeInfo			20000000	        74.6 ns/op
BenchmarkIsValidRegionCode		20000000	        76.1 ns/op
BenchmarkRegionName				20000000	        77.0 ns/op
```

## Stability

This code is currently being used in a live environment, but should still be considered Alpha, as the code may still change.

## Testing

The code has full test coverage of all logic functions, but due to the use of a long switch statement for the lookup table, it's not practial to test the full switch statement.

## Support

If you find any errors, or simply have constructive feedback, please post an issue directly, and I'll get to it as soon as I can.

## License

The MIT License (MIT)

Copyright (c) 2014 John Beckett

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.