# Enabling Segoe UI Semilight via Font Sets: Explainer
## Introduction
Internally, the version of Skia used by Chromium uses DWriteFontCollections to contain font families.  When accessing Windows system fonts, Chromium font matching can currently only match fonts based on their family name, and not their family name type. This limits the number of accessible Windows fonts to ones specified by a WSS (Width Stretch Style) family name. 

With the introduction of variable fonts, WSS names will not be sufficient for matching. 

Some Windows system fonts cannot be matched using this method, such as Segoe UI Semilight which is of Win32 name type. The switch from GDI to DWrite has also left some fonts inaccessible via their GDI name (such as Arial Bold). 

## Evidence
[This bug](https://bugs.chromium.org/p/chromium/issues/detail?id=710539#) shows the faulty behavior.

[This bug](https://bugs.chromium.org/p/chromium/issues/detail?id=710539#) also outlines issues selecting correct fonts.

## Goals
  - Enable the proper selection of Segoe UI Semilight 
  - Enable the selection of fonts of family type WSS, Win32, Typographic and FullName.

## Non-goals
  - Regress compatibility with older versions of Windows that don't support newer DirectWrite APIs
  - Expose vulnerabilities by not using IPCs and font proxy for new font enumeration
	
## Use-cases
  - Anytime a web developer wants to use fonts such as Segoe UI Semilight, Arial Narrow
  - Anytime a web developer tries to use Windows system fonts
	
## Proposed Solution
The solution will involve a restructure of font enumeration within Skia. It will be versioned to maintain old behavior for machines running older versions of Windows. 
The main architectural change is the switch from DWrite's font collections and families to the exclusive use of font sets. Font sets provide a more malleable way of storing and accessing fonts. Instead of a collection containing a list of families which in turn contain a list of fonts, a font set is simply a large set of fonts. Font sets allow for font filtering by various font properties. For example, a set can be filtered by font family name in conjunction with family name types. This allows for various family name types to be used when narrowing down fonts belonging to a family.

Font sets will also provide ensure Chromium's ability to handle Windows system variable fonts. (How??)

To ensure security and get around the sandboxing, Chromium uses a font proxy. This proxy will be updated to enable the use of DWrite APIs related to font sets. 

Provide example code that ties together problem, proposed solution and how it enables the listed use-cases
The following code snippet shows how a given family is found via its name in the current Chromium implementation.

```
UINT32 index;
BOOL exists;
HRNM(fFontCollection->FindFamilyName(dwFamilyName.get(), &index, &exists),
     "Failed while finding family by name.");
if (!exists) {
  return nullptr;
}
return this->onCreateStyleSet(index);
```

As mentioned previously, this method does not take into account the type of family name, and will not be able to find some families.

The next code snippet shows the use of font sets and name types to allow the filtering of fonts into a desired set. 

```
DWRITE_FONT_PROPERTY win32FamilyName = { DWRITE_FONT_PROPERTY_ID_WIN32_FAMILY_NAME, 
							  dwFamilyName.get() };
fFontSet1->GetPropertyOccurrenceCount(&win32FamilyName, &fontCount);
if (fontCount != 0){
  HRNM(fFontSet1->GetFilteredFonts(&win32FamilyName, 1, false, &filteredFontFamilySet),
                             "Failed while filtering fonts by win32 family name.");
  return new SkFontStyleSet_DirectWrite(this, filteredFontFamilySet.get());
}
```

This snippet shows the filtering of fonts based on their Win32 family name. By using this format, a given family name can be checked against other different family name types.

## Other Implementation Options
This problem was initially discovered due to Chromium's lack of support for the specific font Segoe UI Semilight. Another solution discussed was the addition of support for this font specifically via hardcoding. There is a place in Skia that provides a hardcoded mapping between a fixed set of weight and width keywords (such as semilight) to appropriate Skia values. 

Adding appropriate keywords to this mapping could potentially be a simple solution to the Segoe UI Semilight problem. This solution could also be explored in the future to enable this font's use on systems that are not updated to support the font set change.

The primary cons of this solution are its inflexibility and limited robustness. This does not solve the problem for other fonts plagued with the same issue, such as Arial Narrow. Font sets also allow the incorporation of system variable fonts, whereas this limited solution does not.

