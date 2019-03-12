# End User Defined Characters (EUDC): Explainer
## Introduction
[End User Device Characters (EUDC)](https://docs.microsoft.com/en-us/windows/desktop/Intl/end-user-defined-characters
) utilize the [private use area (PUA)](https://unicode-table.com/en/blocks/private-use-area/) within unicode to provide extensibility to support custom
characters. They can be defined by the end user or another party such as an equipment manufacturer, a user group, a government body, or a font design company. Their use enables users to form names and other words using characters that are not available in standard screen and printer fonts.

**insert graphic of EUDCEdit with Chinese character**

## Evidence
Microsoft has received feedback to retain support for EUDC for line-of-business and government applications and for their end users to utilize pre-defined characters.

## Goals
  - Enable access to the EUDC unicode codepoints on Windows

## Non-goals
	
## Use-cases
  - When a user utilizes this character it is rendered correctly rather than U+FFFD which is usually utilized when 
     rendering an unknown or unpresentable character.
  - A common scenario is a user in Chinese or Japanese whose name is not representable utilizing the base font character set,      EUDC allows the user to represent their name in text format
	
## Proposed Solution


